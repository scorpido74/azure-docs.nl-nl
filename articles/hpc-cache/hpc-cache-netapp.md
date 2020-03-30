---
title: Azure HPC-cache en Azure NetApp-bestanden gebruiken
description: Azure HPC-cache gebruiken om de toegang tot gegevens die zijn opgeslagen met Azure NetApp-bestanden te verbeteren
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: 38f9d0338ce4c47024d670e6d3ee89a97faecc91
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80238682"
---
# <a name="use-azure-hpc-cache-with-azure-netapp-files"></a>Azure HPC-cache gebruiken met Azure NetApp-bestanden

U [Azure NetApp-bestanden](https://azure.microsoft.com/services/netapp/) gebruiken als opslagdoel voor uw Azure HPC-cache. In dit artikel wordt uitgelegd hoe de twee services kunnen samenwerken en geeft het tips om ze in te stellen.

Azure NetApp Files combineert hun ONTAP-besturingssysteem met de schaalbaarheid en snelheid van Microsoft Azure. Met deze combinatie kunnen gebruikers gevestigde workflows naar de cloud verplaatsen zonder code te herschrijven.

Het toevoegen van een Azure HPC-cachecomponent kan de bestandstoegang verbeteren door meerdere Azure NetApp-bestandenvolumes in één geaggregeerde naamruimte te presenteren. Het kan edge caching bieden voor volumes in een ander servicegebied. Het kan ook de prestaties op aanvraag verbeteren voor volumes die zijn gemaakt op serviceniveaus op een lager niveau om kosten te besparen.

## <a name="overview"></a>Overzicht

Als u een Azure NetApp Files-systeem wilt gebruiken als back-endopslag met Azure HPC-cache, volgt u dit proces.

1. Maak hieronder het Azure NetApp Files-systeem en de volgende volumes volgens de richtlijnen in [Uw systeem plannen.](#plan-your-azure-netapp-files-system)
1. Maak de Azure HPC-cache in het gebied waar u bestandstoegang nodig hebt. (Gebruik de instructies in [Een Azure HPC-cache maken](hpc-cache-create.md).)
1. [Definieer opslagdoelen](#create-storage-targets-in-the-cache) in de cache die wijzen op de Azure NetApp-bestandenvolumes. Maak één cacheopslagdoel voor elk uniek IP-adres dat wordt gebruikt om toegang te krijgen tot de volumes.
1. Laat clients [de Azure HPC-cache monteren](#mount-storage-targets) in plaats van azure NetApp-bestandenrechtstreeks te monteren.

## <a name="plan-your-azure-netapp-files-system"></a>Uw Azure NetApp-bestandensysteem plannen

Let bij het plannen van uw Azure NetApp Files-systeem op de items in deze sectie om ervoor te zorgen dat u het probleemloos integreren met Azure HPC-cache.

Lees ook de [documentatie van Azure NetApp Files](../azure-netapp-files/index.yml) voordat u volumes maakt voor gebruik met Azure HPC-cache.

### <a name="nfs-client-access-only"></a>Nfs-clienttoegang alleen

Azure HPC-cache ondersteunt momenteel alleen NFS-toegang. Het kan niet worden gebruikt met SMB ACL of POSIX mode bit volumes.

### <a name="exclusive-subnet-for-azure-netapp-files"></a>Exclusief subnet voor Azure NetApp-bestanden

Azure NetApp Files gebruikt één gedelegeerd subnet voor de volumes. Geen andere bronnen kunnen dat subnet gebruiken. Ook kan slechts één subnet in een virtueel netwerk worden gebruikt voor Azure NetApp-bestanden. Meer informatie vindt u in [richtlijnen voor netwerkplanning van Azure NetApp Files](../azure-netapp-files/azure-netapp-files-network-topologies.md).

### <a name="delegated-subnet-size"></a>Gedelegeerde subnetgrootte

Gebruik de minimale grootte voor het gedelegeerde subnet bij het maken van een Azure NetApp Files-systeem voor gebruik met Azure HPC-cache.

De minimale grootte, die is opgegeven met het netmask /28, biedt 16 IP-adressen. In de praktijk gebruikt Azure NetApp Files slechts drie van deze beschikbare IP-adressen voor volumetoegang. Dit betekent dat u slechts drie opslagdoelen hoeft te maken in uw Azure HPC-cache om alle volumes te dekken.

Als het gedelegeerde subnet te groot is, kunnen de Azure NetApp-bestandendelen meer IP-adressen gebruiken dan één Azure HPC-cache-instantie aankan. Een enkele cache kan maximaal tien opslagdoelen hebben.

In het voorbeeld snel aan de start in de documentatie van Azure NetApp Files wordt 10.7.0.0/16 gebruikt voor het gedelegeerde subnet, dat een te groot subnet geeft.

### <a name="capacity-pool-service-level"></a>Serviceniveau capaciteitspool

Houd bij het kiezen van het serviceniveau voor uw capaciteitspool rekening met uw werkstroom. Als u vaak gegevens terugschrijft naar het Azure NetApp Files-volume, kunnen de prestaties van de cache worden beperkt als de terugschrijftijd traag is. Kies een hoog serviceniveau voor volumes met frequente schrijfbewerkingen.

Volumes met lage serviceniveaus kunnen ook enige vertraging vertonen aan het begin van een taak, terwijl de cache inhoud vooraf vult. Nadat de cache is up and running met een goede werkset van bestanden, moet de vertraging onmerkbaar worden.

Het is belangrijk om het serviceniveau van de capaciteitspool van tevoren te plannen, omdat het niet kan worden gewijzigd na het maken. Er moet een nieuw volume worden gemaakt in een andere capaciteitsgroep en de gegevens worden gekopieerd.

Houd er rekening mee dat u het opslagquotum van een volume en de grootte van de capaciteitsgroep wijzigen zonder de toegang te verstoren.

## <a name="create-storage-targets-in-the-cache"></a>Opslagdoelen maken in de cache

Nadat uw Azure NetApp-bestandensysteem is ingesteld en de Azure HPC-cache is gemaakt, definieert u opslagdoelen in de cache die naar de bestandssysteemvolumes wijzen.

Maak één opslagdoel voor elk IP-adres dat wordt gebruikt door uw Azure NetApp-bestandenvolumes. Het IP-adres wordt vermeld op de pagina bevestigingsinstructies van het volume.

Als meerdere volumes hetzelfde IP-adres delen, u voor elk van deze volumes één opslagdoel gebruiken.  

Volg de [bevestigingsinstructies in de documentatie van Azure NetApp-bestanden](../azure-netapp-files/azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md) om de IP-adressen te vinden die u wilt gebruiken.

U ook IP-adressen vinden met de Azure CLI:

```azurecli
az netappfiles volume list -g ${RESOURCE_GROUP} --account-name ${ANF_ACCOUNT} --pool-name ${POOL} --query "[].mountTargets[].ipAddress" | grep -Ee '[0-9]+[.][0-9]+[.][0-9]+[.][0-9]+' | tr -d '"' | tr -d , | sort | uniq
```

Exportnamen op het Azure NetApp Files-systeem hebben één padcomponent. Probeer geen opslagdoel voor de hoofdexport ``/`` in Azure NetApp-bestanden te maken, omdat die export geen bestandstoegang biedt.

Er zijn geen speciale beperkingen voor virtuele naamruimtepaden voor deze opslagdoelen.

## <a name="mount-storage-targets"></a>Opslagdoelen monteren

Clientmachines moeten de cache monteren in plaats van de Azure NetApp Files-volumes rechtstreeks te monteren. Volg de instructies in [De Azure HPC-cache monteren.](hpc-cache-mount.md)

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het instellen en gebruiken van [Azure NetApp-bestanden](../azure-netapp-files/index.yml)
* Neem [contact op met ondersteuning](hpc-cache-support-ticket.md)voor het plannen en instellen van uw Azure HPC-cachesysteem voor het gebruik van Azure NetApp-bestanden.
