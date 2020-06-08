---
title: Cloud opslag om zeer veilige, duurzame, schaal bare apps te bouwen met Azure Storage
description: Meer informatie over de services voor het opslaan van grote gestructureerde en niet-gestructureerde mobiele toepassings gegevens in de Cloud.
author: codemillmatt
ms.assetid: 12bbb070-9b3c-4faf-8588-ccff02097224
ms.service: mobile-services
ms.topic: article
ms.date: 06/05/2020
ms.author: masoucou
ms.openlocfilehash: e45726cfc54963c546274e0777dd0a10d89da72d
ms.sourcegitcommit: f57fa5f3ce40647eda93f8be4b0ab0726d479bca
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/07/2020
ms.locfileid: "84483309"
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

## <a name="azure-queue-storage"></a>Azure Queue Storage
[Azure Queue-opslag](https://azure.microsoft.com/services/storage/queues/) is een service voor het opslaan van grote aantallen berichten. U kunt overal ter wereld toegang krijgen tot berichten via geverifieerde oproepen met HTTP of HTTPS. Een wachtrij bericht kan Maxi maal 64 KB groot zijn. Een wachtrij kan miljoenen berichten bevatten, tot de totale capaciteits limiet van een opslag account. Wacht rijen worden vaak gebruikt om een achterstand te maken voor het asynchroon verwerken van werk.

**Verwijzingen**
- [Azure-portal](https://portal.azure.com)
- [Documentatie voor Azure Queue Storage](/azure/storage/queues/)
- [Snelstartgidsen](/azure/storage/queues/storage-quickstart-queues-portal)
- [Voorbeelden](/azure/storage/common/storage-samples-dotnet?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
