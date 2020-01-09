---
title: Vereisten voor de Azure HPC-cache
description: Vereisten voor het gebruik van Azure HPC cache
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: 90b84d936bda4e3a974e60934e82ac6c3389d85a
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/03/2020
ms.locfileid: "75645766"
---
# <a name="prerequisites-for-azure-hpc-cache"></a>Vereisten voor de Azure HPC-cache

Voordat u de Azure Portal gebruikt om een nieuwe Azure HPC-cache te maken, moet u ervoor zorgen dat uw omgeving aan deze vereisten voldoet.

## <a name="azure-subscription"></a>Azure-abonnement

Een betaald abonnement wordt aanbevolen.

> [!NOTE]
> Tijdens de eerste maanden van de GA-release moet het team van de Azure HPC-cache uw abonnement toevoegen aan de lijst met toegang voordat het kan worden gebruikt om een cache-exemplaar te maken. Met deze procedure kunt u ervoor zorgen dat elke klant de reactie snelheid van hoge kwaliteit vanuit hun caches ontvangt. Vul [dit formulier](https://aka.ms/onboard-hpc-cache) in om toegang aan te vragen.

## <a name="network-infrastructure"></a>Netwerkinfrastructuur

Er moeten twee aan het netwerk gerelateerde vereisten worden ingesteld voordat u uw cache kunt gebruiken:

* Een toegewezen subnet voor het Azure HPC-cache-exemplaar
* DNS-ondersteuning zodat de cache toegang kan krijgen tot opslag en andere bronnen

### <a name="cache-subnet"></a>Cache-subnet

Voor de Azure HPC-cache is een toegewezen subnet met de volgende kwaliteiten vereist:

* Voor het subnet moeten ten minste 64 IP-adressen beschikbaar zijn.
* Het subnet kan geen andere Vm's hosten, zelfs voor gerelateerde services zoals client machines.
* Als u meerdere Azure HPC-cache-exemplaren gebruikt, heeft elk een eigen subnet nodig.

De best practice is het maken van een nieuw subnet voor elke cache. U kunt een nieuw virtueel netwerk en subnet maken als onderdeel van het maken van de cache.

### <a name="dns-access"></a>DNS-toegang

De cache heeft DNS nodig om toegang te krijgen tot bronnen buiten het virtuele netwerk. Afhankelijk van de bronnen die u gebruikt, moet u mogelijk een aangepaste DNS-server instellen en door sturen configureren tussen die server en Azure DNS servers:

* Als u toegang wilt krijgen tot Azure Blob Storage-eind punten en andere interne resources, hebt u de op Azure gebaseerde DNS-server nodig.
* Voor toegang tot on-premises opslag moet u een aangepaste DNS-server configureren die uw opslag hostnamen kan omzetten.

Als u alleen toegang tot de Blob-opslag nodig hebt, kunt u de standaard-DNS-server van Azure gebruiken voor uw cache. Als u echter toegang tot andere bronnen nodig hebt, moet u een aangepaste DNS-server maken en deze configureren voor het door sturen van aanvragen voor specifieke oplossingen van Azure naar de Azure DNS-server.

Een eenvoudige DNS-server kan ook worden gebruikt voor het verdelen van client verbindingen tussen alle beschik bare cache koppel punten.

Meer informatie over Azure Virtual Networks en DNS-server configuraties in [naam omzetting voor bronnen in azure Virtual Networks](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances).

## <a name="permissions"></a>Machtigingen

Controleer deze aan de machtigingen gerelateerde vereisten voordat u begint met het maken van de cache.

* Het cache-exemplaar moet virtuele netwerk interfaces (Nic's) kunnen maken. De gebruiker die de cache maakt, moet voldoende bevoegdheden hebben in het abonnement om Nic's te maken.

* Bij gebruik van Blob-opslag moet de Azure HPC-cache worden gemachtigd om toegang te krijgen tot uw opslag account. Gebruik op rollen gebaseerd toegangs beheer (RBAC) om de cache toegang te geven tot uw Blob-opslag. Er zijn twee rollen vereist: Inzender van het opslag account en de gegevensinzender voor opslag-blobs.

  Volg de instructies in [opslag doelen toevoegen](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account) om de rollen toe te voegen.

## <a name="storage-infrastructure"></a>Opslag infrastructuur

De cache ondersteunt Azure Blob-containers of NFS-hardware Storage-export. Voeg opslag doelen toe nadat u de cache hebt gemaakt.

Elk opslag type heeft specifieke vereisten.

### <a name="nfs-storage-requirements"></a>NFS-opslag vereisten

Als u gebruikmaakt van on-premises hardware-opslag, moet de cache toegang hebben tot het netwerk van hoge band breedte van het subnet. [ExpressRoute](https://docs.microsoft.com/azure/expressroute/) of vergelijk bare toegang wordt aanbevolen.

Opslag van NFS-back-end moet een compatibel hardware/software-platform zijn. Neem contact op met het team van HPC-cache voor meer informatie.

### <a name="blob-storage-requirements"></a>Vereisten voor Blob-opslag

Als u Azure Blob Storage wilt gebruiken met uw cache, hebt u een compatibel opslag account nodig en een lege BLOB-container of een container die is gevuld met gegevens in de Azure HPC-cache, zoals wordt beschreven in [gegevens verplaatsen naar Azure Blob-opslag](hpc-cache-ingest.md).

Maak het account voordat u probeert een opslag doel toe te voegen. U kunt een nieuwe container maken wanneer u het doel toevoegt.

Als u een compatibel opslag account wilt maken, gebruikt u deze instellingen:

* Prestaties: **standaard**
* Soort account: **StorageV2 (algemeen gebruik v2)**
* Replicatie: **lokaal redundante opslag (LRS)**
* Access-laag (standaard): **Hot**

Het is een goed idee om een opslag account te gebruiken dat zich op dezelfde locatie bevinden als de cache.
<!-- clarify location - same region or same resource group or same virtual network? -->

U moet ook de cache toepassing toegang geven tot uw Azure Storage-account, zoals vermeld in de [machtigingen](#permissions)hierboven. Volg de procedure in [opslag doelen toevoegen](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account) om de vereiste toegangs rollen in de cache op te slaan. Als u niet de eigenaar van het opslag account bent, laat u de eigenaar deze stap uitvoeren.

## <a name="next-steps"></a>Volgende stappen

* [Een Azure HPC-cache-exemplaar maken](hpc-cache-create.md) op basis van de Azure Portal
