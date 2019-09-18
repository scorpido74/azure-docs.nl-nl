---
title: Vereisten voor de Azure HPC-cache
description: Vereisten voor het gebruik van Azure HPC cache
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: v-erkell
ms.openlocfilehash: 50c60e38b58815be04cfb892c3622b9579529e67
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71036858"
---
# <a name="prerequisites-for-azure-hpc-cache"></a>Vereisten voor de Azure HPC-cache

Voordat u de Azure Portal gebruikt om een nieuwe Azure HPC-cache te maken, moet u ervoor zorgen dat uw omgeving aan deze vereisten voldoet.

## <a name="azure-subscription"></a>Azure-abonnement

Een betaald abonnement wordt aanbevolen.

> [!NOTE]
> Tijdens de preview-versie moet het Azure HPC-cache team uw abonnement toevoegen aan de lijst met toegang voordat het kan worden gebruikt voor het maken van een cache-exemplaar. Met deze procedure kunt u ervoor zorgen dat elke klant de reactie snelheid van hoge kwaliteit vanuit hun test caches ontvangt. Vul [dit formulier](https://aka.ms/onboard-hpc-cache) in om toegang aan te vragen.

## <a name="network-infrastructure"></a>Netwerk infrastructuur

Er moeten twee opties voor het netwerk worden ingesteld voordat u uw cache kunt gebruiken:

* Een toegewezen subnet voor het Azure HPC-cache-exemplaar
* DNS-ondersteuning zodat de cache toegang kan krijgen tot opslag en andere bronnen

### <a name="cache-subnet"></a>Cache-subnet

Voor de Azure HPC-cache is een toegewezen subnet met de volgende kwaliteiten vereist:

* Voor het subnet moeten ten minste 64 IP-adressen beschikbaar zijn.
* Het subnet kan geen andere Vm's hosten, zelfs voor gerelateerde services zoals client machines.
* Als u meerdere cache-instanties gebruikt, heeft elk een eigen subnet nodig.

De best practice is het maken van een nieuw subnet voor de cache. U kunt een nieuw virtueel netwerk en subnet maken als onderdeel van het maken van de cache.

### <a name="dns-access"></a>DNS-toegang

De Azure HPC-cache moet DNS hebben om toegang te krijgen tot bronnen buiten het virtuele netwerk. Afhankelijk van de bronnen die u gebruikt, moet u mogelijk een aangepaste DNS-server instellen en door sturen configureren tussen die server en Azure DNS servers: 

* Als u toegang wilt krijgen tot Azure Blob Storage-eind punten en andere interne resources, hebt u de op Azure gebaseerde DNS-server nodig.
* Voor toegang tot on-premises opslag moet u een aangepaste DNS-server configureren die uw opslag hostnamen kan omzetten.

Als u alleen toegang tot de Blob-opslag nodig hebt, kunt u de standaard-DNS-server van Azure gebruiken voor uw cache. Als u echter toegang tot andere bronnen nodig hebt, moet u een aangepaste DNS-server maken en deze configureren voor het door sturen van aanvragen voor specifieke oplossingen van Azure naar de Azure DNS-server. (Een eenvoudige DNS-server kan ook worden gebruikt voor het verdelen van client verbindingen tussen alle beschik bare cache koppel punten.)

Meer informatie over Azure Virtual Networks en DNS-server configuraties in [naam omzetting voor bronnen in azure Virtual Networks](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances).

## <a name="permissions"></a>Machtigingen

Controleer deze aan de machtigingen gerelateerde vereisten voordat u begint met het maken van de cache.

* De Azure HPC-cache moet virtuele netwerk interfaces (Nic's) kunnen maken. De gebruiker die de cache maakt, moet voldoende bevoegdheden hebben in het abonnement om Nic's te maken.
<!-- There are several ways to authorize this access; read [Additional prerequisites](media/preview-prereqs.md) to learn more. -->

* Bij gebruik van Blob-opslag moet het Azure HPC-cache-exemplaar toegang hebben tot uw opslag account. U kunt op rollen gebaseerd toegangs beheer (RBAC) gebruiken om de cache toegang te geven tot uw Blob-opslag. Er zijn twee rollen vereist: Inzender voor opslag accounts en Inzender voor opslag-blobs. Volg de instructies in [opslag ruimte toevoegen aan de cache](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account).

## <a name="storage-infrastructure"></a>Opslag infrastructuur

De cache ondersteunt Azure Blob-containers of NFS-hardware Storage-export. U kunt opslag doelen definiëren wanneer u de cache maakt, maar u kunt ze later ook toevoegen. 

### <a name="nfs-storage-requirements"></a>NFS-opslag vereisten

Als u gebruikmaakt van on-premises hardware-opslag, moet de cache toegang hebben tot het netwerk van hoge band breedte van het subnet. [ExpressRoute](https://docs.microsoft.com/azure/expressroute/) of vergelijk bare toegang wordt aanbevolen.

Opslag van NFS-back-end moet een compatibel hardware/software-platform zijn. Neem contact op met het team van HPC-cache voor meer informatie.

### <a name="blob-storage-requirements"></a>Vereisten voor Blob-opslag

Als u Azure Blob Storage wilt gebruiken met de Azure HPC-cache, hebt u een compatibel opslag account nodig en een lege BLOB-container of een container die is gevuld met gegevens in de Azure HPC-cache, zoals wordt beschreven in [gegevens verplaatsen naar Azure Blob-opslag](hpc-cache-ingest.md).

Maak het account en de container voordat u deze probeert toe te voegen als een opslag doel.

Als u een compatibel opslag account wilt maken, gebruikt u deze instellingen:

* Nemen **Standard**
* Soort account: **StorageV2 (algemeen gebruik v2)**
* Replicat **Lokaal redundante opslag (LRS)**
* Toegangs niveau (standaard): **hot**

Het is een goed idee om een opslag account te gebruiken dat zich op dezelfde locatie bevinden als de cache.

U moet ook de cache toepassing toegang geven tot uw Azure-opslag account. Volg de beschrijving in [opslag ruimte toevoegen aan de cache](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account) om de toegangs rollen van het opslag account Inzender en de gegevensinzender voor opslag-blobs in de cache op te geven. Als u niet de eigenaar van het opslag account bent, laat u de eigenaar deze stap uitvoeren.

## <a name="next-steps"></a>Volgende stappen

* [Een Azure HPC-cache-exemplaar maken](hpc-cache-create.md) op basis van de Azure Portal
