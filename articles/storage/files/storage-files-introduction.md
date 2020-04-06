---
title: Inleiding tot Azure Files | Microsoft Docs
description: Een overzicht van Azure Files, een service waarmee u netwerkbestandsshares kunt maken en gebruiken in de cloud volgens het industriestandaard SMB-protocol.
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 03/10/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: aff6f99c119ba2854fd7923d2a15efb2e1a6b601
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/05/2020
ms.locfileid: "80666800"
---
# <a name="what-is-azure-files"></a>Wat is Azure Files?
Met Azure Files beschikt u over volledig beheerde bestandsshares in de cloud die toegankelijk zijn via het industriestandaard SMB-protocol ([Server Message Block](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx)). Azure-bestandsshares kunnen gelijktijdig worden gekoppeld door on-premises of cloudimplementaties van Windows, Linux en macOS. Bovendien kunnen Azure-bestandsshares worden opgeslagen in de cache op Windows-servers met Azure-bestandssynchronisatie voor snelle toegang tot locaties waar de gegevens worden gebruikt.

## <a name="videos"></a>Video's
| Introductie van Azure File Sync | Azure Files with Sync (Ignite 2019)  |
|-|-|
| [![Screencast van de Introducing Azure File Sync video - klik om af te spelen!](./media/storage-files-introduction/azure-file-sync-video-snapshot.png)](https://www.youtube.com/watch?v=Zm2w8-TRn-o) | [![Screencast van de Azure Files with Sync-presentatie - klik om af te spelen!](./media/storage-files-introduction/ignite-2018-video.png)](https://www.youtube.com/embed/6E2p28XwovU) |

Hier volgen enkele video's over de algemene use cases van Azure Files:
* [Uw bestandsserver vervangen door een serverloze Azure-bestandsshare](https://sec.ch9.ms/ch9/3358/0addac01-3606-4e30-ad7b-f195f3ab3358/ITOpsTalkAzureFiles_high.mp4)
* [Aan de slag met FSLogix-profielcontainers op Azure Files in Windows Virtual Desktop die gebruikmaakt van AD-verificatie](https://www.youtube.com/embed/9S5A1IJqfOQ)

## <a name="why-azure-files-is-useful"></a>Waarom Azure Files handig is
Azure-bestandsshares kunnen worden gebruikt voor het volgende:

* **Ter vervanging van of ter aanvulling op on-premises bestandsservers**:  
    Azure Files kan worden gebruikt om bestandsshares op traditionele on-premises bestandsservers of NAS-apparaten volledig te vervangen of aan te vullen. Populaire besturingssystemen, zoals Windows, macOS en Linux kunnen een Azure-bestandsshare rechtstreeks koppelen, waar ter wereld deze zich ook bevinden. Azure-bestandsshares kunnen ook met Azure File Sync worden gerepliceerd naar Windows-Servers, on-premises of in de cloud, voor een krachtige en gedistribueerde opslag in de cache van gegevens waar deze worden gebruikt. Met de recente release van [Azure Files AD Authentication](storage-files-active-directory-overview.md)kunnen Azure-bestandsshares blijven werken met ad die on-premises wordt gehost voor toegangscontrole. 

* **"Lift and shift"-toepassingen**:  
    Met Azure Files kunnen toepassingen die een bestandsshare voor het opslaan van gegevens uit een bestandstoepassing verwachten eenvoudig worden 'gelift en geshift' naar de cloud. Azure Files biedt zowel het 'klassieke' lift- en shift-scenario, waarbij zowel de toepassing als de gegevens worden verplaatst naar Azure, als het 'hybride' lift- en shift-scenario, waarin de toepassingsgegevens worden verplaatst naar Azure Files en de toepassing nog steeds on-premises wordt uitgevoerd. 

* **Vereenvoudig cloudontwikkeling:**  
    Azure Files kan ook op verschillende manieren worden gebruikt voor het vereenvoudigen van nieuwe cloudontwikkelingsprojecten. Bijvoorbeeld:
    * **Instellingen voor gedeelde toepassingen:**  
        Een algemeen patroon voor gedistribueerde toepassingen is de aanwezigheid van configuratiebestanden op een centrale locatie waar ze toegankelijk zijn vanuit veel andere toepassingsexemplaren. Toepassingsexemplaren kunnen hun configuratie laden via de File REST-API, en mensen kunnen hier indien nodig toegang toe krijgen door de SMB-share lokaal te koppelen.

    * **Diagnostisch aandeel**:  
        Een Azure-bestandsshare is een handige locatie voor cloudtoepassingen voor het wegschrijven van hun logboeken, metrische gegevens en crashdumps. Logboeken kunnen door de toepassingsexemplaren worden geschreven via de File REST-API en ontwikkelaars kunnen er toegang toe krijgen door de bestandsshare te koppelen met hun lokale computer. Dit biedt veel flexibiliteit, aangezien ontwikkelaars kunnen ontwikkelen in de cloud zonder hun bestaande tooling waarmee ze bekend zijn en graag werken te hoeven opgeven.

    * **Dev/Test/Debug**:  
        Wanneer ontwikkelaars of beheerders op virtuele machines in de cloud werken, hebben ze vaak een set hulpprogramma's nodig. Het kopiëren van deze hulpprogramma's naar elke VM kan een tijdrovend proces zijn. Door een Azure-bestandsshare lokaal te koppelen aan de virtuele machines, kunnen de ontwikkelaar en beheerder indien nodig snel toegang krijgen tot hun hulpprogramma's.

## <a name="key-benefits"></a>Belangrijkste voordelen
* **Gedeelde toegang**. Azure-bestandsshares bieden ondersteuning voor de industrienorm SMB-protocol, wat betekent dat u uw lokale bestandsshares naadloos kunt vervangen door de Azure-bestandsshares zonder dat u zich zorgen hoeft te maken over de compatibiliteit van toepassingen. Het delen van een bestandssysteem op meerdere machines, toepassingen/exemplaren is een belangrijk voordeel van Azure Files voor toepassingen die gegevens moeten kunnen delen. 
* **Volledig beheerd**. Azure-bestandsshares kunnen worden gemaakt zonder de noodzaak om hardware of een besturingssysteem te beheren. Dit betekent dat u geen essentiële beveiligingsupgrades hoeft te installeren op het serverbesturingssysteem of defecte harde schijven moet vervangen.
* **Scripting en tooling**. PowerShell-cmdlets en Azure CLI kunnen worden gebruikt voor het maken, koppelen en beheren van Azure-bestandsshares als onderdeel van het beheer van Azure-toepassingen. U kunt Azure-bestandsshares maken en beheren met Azure Portal en Azure Storage Explorer. 
* **Veerkracht**. Azure Files is vanaf de grond opgebouwd om altijd beschikbaar te zijn. Het vervangen van on-premises bestandsshares door Azure Files betekent dat u niet langer problemen ondervindt door lokale stroomstoringen of netwerkstoringen. 
* **Vertrouwde programmeerbaarheid**. Toepassingen die worden uitgevoerd in Azure hebben toegang tot de gegevens in de share via [System I/O-API's](https://msdn.microsoft.com/library/system.io.file.aspx). Dat betekent dat ontwikkelaars bestaande code en vaardigheden kunnen inzetten voor het migreren van bestaande toepassingen. Naast System IO-API's kunt u [Azure Storage-clientbibliotheken](https://msdn.microsoft.com/library/azure/dn261237.aspx) of de [REST-API van Azure Storage](/rest/api/storageservices/file-service-rest-api) gebruiken.

## <a name="next-steps"></a>Volgende stappen
* [Azure-bestandsshare maken](storage-how-to-create-file-share.md)
* [Verbinding maken en koppelen op Windows](storage-how-to-use-files-windows.md)
* [Verbinden en monteren op Linux](storage-how-to-use-files-linux.md)
* [Verbinding maken en monteren op macOS](storage-how-to-use-files-mac.md)
