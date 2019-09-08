---
title: Een HPC-cache van Azure koppelen
description: Clients verbinden met een Azure HPC cache-service
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: v-erkell
ms.openlocfilehash: 0ccd256ed399fff7c364883ada45d2d15c75a120
ms.sourcegitcommit: a4b5d31b113f520fcd43624dd57be677d10fc1c0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/06/2019
ms.locfileid: "70775349"
---
# <a name="mount-the-azure-hpc-cache"></a>De Azure HPC-cache koppelen

Nadat de cache is gemaakt, hebben NFS-clients toegang tot de server met een eenvoudige koppelings opdracht.

Gebruik de koppelings adressen die worden vermeld op de pagina cache-overzicht en het pad van de virtuele naam ruimte dat u instelt tijdens het maken van het opslag doel. 

![scherm afbeelding van de overzichts pagina van het Azure HPC-cache-exemplaar, met een markeer stift rond de lijst met koppelings adressen op de rechter benedenhoek](media/mount-addresses.png)

> [!NOTE] 
> De cache koppel adressen komen overeen met netwerk interfaces in het subnet van de cache. Deze nic's worden weer gegeven in de resource groep met namen `-cluster-nic-` die eindigen op en een getal. Wijzig of verwijder deze interfaces niet, of de cache is niet meer beschikbaar.

De virtuele naam ruimte paden worden weer gegeven op de pagina **opslag doelen** . 
![scherm afbeelding van het opslag doel paneel van de cache, met een markeer stift rond een vermelding in de kolom pad van de tabel](media/storage-target-path.png)

## <a name="mount-command-syntax"></a>Syntaxis van koppelings opdracht

Gebruik een koppelings opdracht zoals de volgende:

> sudo mount *cache_mount_address*:/*namespace_path* *local_path* {*Opties*}

Voorbeeld: 

```
root@test-client:/tmp# mkdir hpccache
root@test-client:/tmp# sudo mount 10.0.0.28:/blob-demo-0722 ./hpccache/ -orw,tcp,mountproto=tcp,vers3,hard,intr
root@test-client:/tmp# 
```

Wanneer deze opdracht is geslaagd, moet de inhoud van de opslag export zichtbaar zijn in de ``hpccache`` Directory op de client. 

> [!NOTE] 
> Uw clients moeten toegang hebben tot het virtuele netwerk en het subnet dat uw cache heeft. Maak bijvoorbeeld client-Vm's binnen hetzelfde virtuele netwerk, of gebruik een eind punt, gateway of andere oplossing in het virtuele netwerk voor toegang vanaf buiten. Houd er rekening mee dat niets anders kan worden gehost in het subnet van de cache.

### <a name="mount-command-options"></a>Opdracht Opties koppelen

Voor een robuuste client koppeling geeft u deze instellingen en argumenten door in de opdracht Mount: 

``mount -o hard,nointr,proto=tcp,mountproto=tcp,retry=30 ${CACHE_IP_ADDRESS}:/${NAMESPACE_PATH} ${LOCAL_FILESYSTEM_MOUNT_POINT}``

| Aanbevolen opdracht instellingen voor koppelen | |
--- | --- 
``hard`` | Zachte koppelingen naar het vFXT-cluster zijn gekoppeld aan toepassings fouten en mogelijke gegevens verlies. 
``proto=netid`` | Met deze optie wordt de juiste verwerking van NFS-netwerk fouten ondersteund.
``mountproto=netid`` | Deze optie biedt ondersteuning voor de juiste verwerking van netwerk fouten voor koppelings bewerkingen.
``retry=n`` | Stel ``retry=30`` in om storingen van de tijdelijke koppeling te voor komen. (Een andere waarde wordt aanbevolen in voorgrond koppelingen.)

## <a name="next-steps"></a>Volgende stappen

* Als u gegevens naar de opslag doelen van de cache wilt verplaatsen, leest u [nieuwe Azure Blob-opslag vullen](hpc-cache-ingest.md).
