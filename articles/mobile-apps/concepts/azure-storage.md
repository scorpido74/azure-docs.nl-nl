---
title: Cloud opslag om zeer veilige, duurzame, schaal bare apps te bouwen met Azure Storage
description: Meer informatie over de services voor het opslaan van grote gestructureerde en niet-gestructureerde mobiele toepassings gegevens in de Cloud.
author: codemillmatt
ms.assetid: 12bbb070-9b3c-4faf-8588-ccff02097224
ms.service: mobile-services
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: 6f8ed02659f1720435df3ff98817233a5a46dbcb
ms.sourcegitcommit: 0a5bb9622ee6a20d96db07cc6dd45d8e23d5554a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/05/2020
ms.locfileid: "84450988"
---
# <a name="cloud-storage-for-highly-secure-durable-scalable-apps-with-azure-storage"></a>Cloud opslag voor zeer veilige, duurzame, schaal bare apps met Azure Storage
[Azure Storage](https://azure.microsoft.com/services/storage/) is de oplossing voor Cloud opslag van micro soft voor moderne toepassingen die een zeer schaal bare object opslag voor gegevens objecten, een File System-Service voor de Cloud, een berichten Archief voor betrouw bare berichten en een NoSQL Store bieden. Azure Storage is:
- **Duurzaam en Maxi maal beschikbaar:** Redundantie zorgt ervoor dat uw gegevens veilig zijn in het geval van tijdelijke hardwarefouten. U kunt er ook voor kiezen gegevens te repliceren in datacenters of geografische regio's voor extra beveiliging tegen lokale rampen of natuurrampen. Op deze manier gerepliceerde gegevens blijven maximaal beschikbaar in het geval van een stroomstoring.
- **Beveiligd:** Alle gegevens die naar Azure Storage worden geschreven, worden versleuteld door de service. Azure Storage biedt u gedetailleerde controle over wie toegang tot uw gegevens heeft.
- **Schaalbaar:** Services zijn ontworpen om zeer schaalbaar te zijn om te voldoen aan de vereisten voor gegevens opslag en prestaties van de huidige toepassingen.
- **Beheerd:** Azure verwerkt onderhoud van hardware, updates en kritieke problemen voor u.
- **Toegankelijk:** De gegevens zijn overal ter wereld toegankelijk via HTTP of HTTPS. Micro soft biedt client bibliotheken in verschillende talen, zoals .NET, Java, node. js, Python, PHP, Ruby en go, en een rijpe REST API. Scripting wordt ondersteund in Azure PowerShell of de Azure CLI. De Azure Portal en Azure Storage Explorer bieden eenvoudige visuele oplossingen voor het werken met uw gegevens.

Gebruik de volgende services om Cloud opslag in te scha kelen in uw mobiele apps.

## <a name="azure-blob-storage"></a>Azure Blob Storage
[Azure Blob-opslag](https://azure.microsoft.com/services/storage/blobs/) biedt een oplossing voor object opslag voor de Cloud. Blob-opslag is geoptimaliseerd voor het opslaan van enorme hoeveel heden ongestructureerde gegevens die niet voldoen aan een bepaald gegevens model of specifieke definitie, zoals tekst of binair. Het ondersteunt diverse talen die door client bibliotheken worden gebruikt. Blob-opslag is ontworpen voor het volgende:
- Afbeeldingen of documenten rechtstreeks naar een browser te verzenden.
- Bestanden opslaan voor gedistribueerde toegang.
- Video en Audio streamen.
- Schrijven naar logboek bestanden.
- Sla gegevens op voor back-up en herstel, herstel na nood gevallen en archivering.
- Bewaar gegevens voor analyse door een on-premises of door Azure gehoste service.

**Verwijzingen**
- [Azure-portal](https://portal.azure.com)
- [Documentatie voor Azure Blob Storage](/azure/storage/blobs/storage-blobs-introduction)
- [Snelstartgidsen](/azure/storage/blobs/storage-quickstart-blobs-portal)
- [Voorbeelden](/azure/storage/common/storage-samples-dotnet?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

## <a name="azure-table-storage"></a>Azure Table Storage
[Azure Table Storage](https://azure.microsoft.com/services/storage/tables/) is een service voor het opslaan van gestructureerde NoSQL-gegevens in de Cloud en biedt een sleutel-of kenmerk opslag met een schemaloos ontwerp. Met Azure Table Storage kunnen grote hoeveelheden gestructureerde gegevens worden opgeslagen. De service is een NoSQL-gegevens archief dat geverifieerde aanroepen binnen en buiten de Azure-Cloud accepteert. Azure-tabellen zijn ideaal voor het opslaan van gestructureerde, niet-relationele gegevens. Tabel opslag wordt doorgaans gebruikt voor het volgende:
- Sla terabytes aan gestructureerde gegevens op die geschikt zijn voor webschaal toepassingen.
- Gegevens sets opslaan waarvoor geen complexe samen voegingen, refererende sleutels of opgeslagen procedures zijn vereist en kunnen worden genormaliseerd voor snelle toegang.
- Snel gegevens opvragen met behulp van een geclusterde index.
- Toegang tot gegevens met behulp van het OData-protocol en LINQ-query's met Windows Communication Foundation (WCF) Data Services .NET-bibliotheken.

U kunt tabel opslag gebruiken om grote sets gestructureerde, niet-relationele gegevens op te slaan en op te vragen. Uw tabellen worden geschaald als de vraag toeneemt.

**Verwijzingen**
- [Azure-portal](https://portal.azure.com)
- [Documentatie voor Azure Table Storage](/azure/storage/tables/table-storage-overview)
- [Voorbeelden](/azure/cosmos-db/tutorial-develop-table-dotnet?toc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fstorage%2Ftables%2FTOC.json&bc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fbread%2Ftoc.json)
- [Snelstartgidsen](/azure/storage/tables/table-storage-quickstart-portal)

## <a name="azure-files"></a>Azure Files
Met [Azure files](https://azure.microsoft.com/services/storage/files/)kunt u Maxi maal beschik bare netwerk bestands shares instellen waartoe toegang kan worden verkregen met behulp van het standaard SMB-protocol (Server Message Block). Meerdere Vm's kunnen dezelfde bestanden delen met lees-en schrijf toegang. U kunt de bestanden ook lezen met behulp van de REST-interface of de opslag-client bibliotheken. U kunt de bestanden overal ter wereld openen met behulp van een URL die verwijst naar het bestand en een Shared Access Signature SAS-token bevat. U kunt SAS-tokens genereren. Ze bieden specifieke tijd toegang tot een privé-Asset.

Azure-bestandsshares kunnen worden gebruikt voor het volgende:
- **On-premises bestands servers vervangen of aanvullen:** Populaire besturings systemen, zoals Windows, macOS en Linux, kunnen rechtstreeks Azure-bestands shares koppelen waar ze zich ook in de wereld bevinden. Azure-bestandsshares kunnen ook met Azure File Sync worden gerepliceerd naar Windows-Servers, on-premises of in de cloud, voor een krachtige en gedistribueerde opslag in de cache van gegevens waar deze worden gebruikt.
- **Lift-en Shift-toepassingen:** Migreer toepassingen naar de cloud die verwachten dat een bestands share bestands toepassing of gebruikers gegevens opslaat.
- **Ontwikkeling van de Cloud vereenvoudigen:** Azure Files kunnen ook worden gebruikt in verschillende manieren om nieuwe projecten voor Cloud ontwikkeling te vereenvoudigen. Bijvoorbeeld:
    - **Instellingen voor gedeelde toepassing:** Een gemeen schappelijk patroon voor gedistribueerde toepassingen is het configureren van configuratie bestanden op een centrale locatie waar ze toegankelijk zijn vanuit veel toepassings exemplaren. Toepassings exemplaren kunnen hun configuratie laden via de bestands REST API. Gebruikers kunnen deze indien nodig openen door de SMB-share lokaal te koppelen.
    - **Diagnostische share:** Een Azure-bestands share is een handige locatie voor Cloud toepassingen voor het schrijven van hun logboeken, metrische gegevens en crash dumps. Logboeken kunnen worden geschreven door de instanties van de toepassing via de bestands REST API. Ontwikkel aars hebben toegang tot de bestanden door de bestands share op hun lokale machine te koppelen. Deze mogelijkheid maakt grote flexibiliteit mogelijk. Ontwikkel aars kunnen gebruikmaken van Cloud ontwikkeling zonder dat ze de bestaande hulp middelen hoeven te verlaten.

**Verwijzingen**
- [Azure-portal](https://portal.azure.com)
- [Documentatie voor Azure Files](/azure/storage/files/storage-files-introduction)
- [Snelstartgidsen](/azure/storage/files/storage-files-quick-create-use-windows)

## <a name="azure-queue-storage"></a>Azure Queue Storage
[Azure Queue-opslag](https://azure.microsoft.com/services/storage/queues/) is een service voor het opslaan van grote aantallen berichten. U kunt overal ter wereld toegang krijgen tot berichten via geverifieerde oproepen met HTTP of HTTPS. Een wachtrij bericht kan Maxi maal 64 KB groot zijn. Een wachtrij kan miljoenen berichten bevatten, tot de totale capaciteits limiet van een opslag account. Wacht rijen worden vaak gebruikt om een achterstand te maken voor het asynchroon verwerken van werk.

**Verwijzingen**
- [Azure-portal](https://portal.azure.com)
- [Documentatie voor Azure Queue Storage](/azure/storage/queues/)
- [Snelstartgidsen](/azure/storage/queues/storage-quickstart-queues-portal)
- [Voorbeelden](/azure/storage/common/storage-samples-dotnet?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
