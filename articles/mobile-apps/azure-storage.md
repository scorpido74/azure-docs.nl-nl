---
title: Cloudopslag om zeer veilige, duurzame, schaalbare apps te bouwen met Azure Storage
description: Meer informatie over de services voor het opslaan van grote gestructureerde en niet-gestructureerde mobiele toepassingsgegevens in de cloud.
author: codemillmatt
ms.assetid: 12bbb070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: 682f06936480d9018d759b4df3bed80be1206faf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240978"
---
# <a name="cloud-storage-for-highly-secure-durable-scalable-apps-with-azure-storage"></a>Cloudopslag voor zeer veilige, duurzame, schaalbare apps met Azure Storage
[Azure Storage](https://azure.microsoft.com/services/storage/) is de cloudopslagoplossing van Microsoft voor moderne toepassingen die een enorm schaalbare objectstore biedt voor gegevensobjecten, een bestandssysteemservice voor de cloud, een berichtenwinkel voor betrouwbare berichten en een NoSQL-winkel. Azure Storage is:
- **Duurzaam en zeer beschikbaar:** Redundantie zorgt ervoor dat uw gegevens veilig zijn in het geval van tijdelijke hardwarefouten. U kunt er ook voor kiezen gegevens te repliceren in datacenters of geografische regio's voor extra beveiliging tegen lokale rampen of natuurrampen. Op deze manier gerepliceerde gegevens blijven maximaal beschikbaar in het geval van een stroomstoring.
- **Veilig:** Alle gegevens die naar Azure Storage zijn verzonden, worden versleuteld door de service. Azure Storage biedt u gedetailleerde controle over wie toegang tot uw gegevens heeft.
- **Schaalbaar:** Services zijn ontworpen om enorm schaalbaar te zijn om te voldoen aan de behoeften op het aangegevensopslag en -prestaties van de huidige toepassingen.
- **Beheerd:** Azure verwerkt hardwareonderhoud, updates en kritieke problemen voor u.
- **Toegankelijk:** De gegevens zijn overal ter wereld toegankelijk via HTTP of HTTPS. Microsoft biedt clientbibliotheken in verschillende talen, zoals .NET, Java, Node.js, Python, PHP, Ruby en Go en een volwassen REST-API. Scripting wordt ondersteund in Azure PowerShell of de Azure CLI. De Azure-portal en Azure Storage Explorer bieden eenvoudige visuele oplossingen voor het werken met uw gegevens.

Gebruik de volgende services om cloudopslag in uw mobiele apps in te schakelen.

## <a name="azure-blob-storage"></a>Azure Blob Storage
[Azure Blob-opslag](https://azure.microsoft.com/services/storage/blobs/) biedt een oplossing voor objectopslag voor de cloud. Blob-opslag is geoptimaliseerd voor het opslaan van enorme hoeveelheden ongestructureerde gegevens die niet voldoen aan een bepaald gegevensmodel of -definitie, zoals tekst of binaire. Het ondersteunt verschillende talen die clientbibliotheken gebruiken. Blob-opslag is ontworpen om:
- Afbeeldingen of documenten rechtstreeks aan een browser weergeven.
- Bestanden opslaan voor gedistribueerde toegang.
- Stream video en audio.
- Schrijven om bestanden te loggen.
- Sla gegevens op voor back-upen en herstel, herstel na noodgevallen en archivering.
- Sla gegevens op voor analyse door een on-premises of door Azure gehoste service.

**Verwijzingen**
- [Azure-portal](https://portal.azure.com)
- [Documentatie voor Azure Blob Storage](/azure/storage/blobs/storage-blobs-introduction)
- [Snel gestart](/azure/storage/blobs/storage-quickstart-blobs-portal)
- [Monsters](/azure/storage/common/storage-samples-dotnet?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

## <a name="azure-table-storage"></a>Azure Table Storage
[Azure Table-opslag](https://azure.microsoft.com/services/storage/tables/) is een service die gestructureerde NoSQL-gegevens opslaat in de cloud en een sleutel- of kenmerkarchief biedt met een schemaloos ontwerp. Met Azure Table Storage kunnen grote hoeveelheden gestructureerde gegevens worden opgeslagen. De service is een NoSQL-gegevensarchief, dat geverifieerde oproepen van binnen en buiten de Azure-cloud accepteert. Azure-tabellen zijn ideaal voor het opslaan van gestructureerde, niet-relationele gegevens. Tabelopslag wordt meestal gebruikt om:
- Sla terabytes aan gestructureerde gegevens op die toepassingen op webschaal kunnen weergeven.
- Sla gegevenssets op waarvoor geen complexe joins, externe sleutels of opgeslagen procedures nodig zijn en die kunnen worden gedenormaliseerd voor snelle toegang.
- Stel snel gegevens op met behulp van een geclusterde index.
- Toegang tot gegevens met behulp van het OData-protocol en LINQ-query's met Windows Communication Foundation (WCF) Data Services .NET-bibliotheken.

U tabelopslag gebruiken om enorme sets gestructureerde, niet-relationele gegevens op te slaan en op te vragen. Uw tabellen schalen naarmate de vraag toeneemt.

**Verwijzingen**
- [Azure-portal](https://portal.azure.com)
- [Documentatie over Azure Table Storage](/azure/storage/tables/table-storage-overview)
- [Monsters](/azure/cosmos-db/tutorial-develop-table-dotnet?toc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fstorage%2Ftables%2FTOC.json&bc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fbread%2Ftoc.json)
- [Snel gestart](/azure/storage/tables/table-storage-quickstart-portal)

## <a name="azure-files"></a>Azure Files
Met [Azure Files](https://azure.microsoft.com/services/storage/files/)u zeer beschikbare netwerkbestandsshares instellen die toegankelijk zijn met behulp van het standaard SMB-protocol (Server Message Block). Meerdere VM's kunnen dezelfde bestanden delen met zowel lees- als schrijftoegang. U de bestanden ook lezen met behulp van de REST-interface of de opslagclientbibliotheken. U hebt overal ter wereld toegang tot de bestanden met behulp van een URL die naar het bestand verwijst en een SAS-token (Shared Access Signature) bevat. U SAS-tokens genereren. Ze bieden specifieke toegang tot een particulier actief voor een bepaalde tijd.

Azure-bestandsshares kunnen worden gebruikt voor het volgende:
- **On-premises bestandsservers vervangen of aanvullen:** Populaire besturingssystemen zoals Windows, macOS en Linux kunnen azure-bestandsshares direct monteren, waar ze ook ter wereld zijn. Azure-bestandsshares kunnen ook met Azure File Sync worden gerepliceerd naar Windows-Servers, on-premises of in de cloud, voor een krachtige en gedistribueerde opslag in de cache van gegevens waar deze worden gebruikt.
- **Lift- en ploegentoepassingen:** Migreer toepassingen naar de cloud waarvan wordt verwacht dat een bestandsshare bestandstoepassing of gebruikersgegevens opslaat.
- **Vereenvoudig cloudontwikkeling:** Azure Files kunnen ook op tal van manieren worden gebruikt om nieuwe cloudontwikkelingsprojecten te vereenvoudigen. Bijvoorbeeld:
    - **Instellingen voor gedeelde toepassingen:** Een veelvoorkomend patroon voor gedistribueerde toepassingen is om configuratiebestanden op een centrale locatie te hebben waar ze vanuit veel toepassingsinstanties kunnen worden geopend. Toepassingsinstanties kunnen hun configuratie laden via de File REST API. Gebruikers kunnen ze waar nodig openen door het aandeel SMB lokaal te monteren.
    - **Diagnostisch aandeel:** Een Azure-bestandsshare is een handige plek voor cloudtoepassingen om hun logboeken, statistieken en crashdumps te schrijven. Logboeken kunnen door de toepassingsinstanties worden geschreven via de File REST API. Ontwikkelaars hebben er toegang toe door het bestandsaandeel op hun lokale machine te monteren. Deze mogelijkheid maakt een grote flexibiliteit mogelijk. Ontwikkelaars kunnen cloudontwikkeling omarmen zonder dat ze de bestaande tooling die ze kennen, hoeven te verlaten.

**Verwijzingen**
- [Azure-portal](https://portal.azure.com)
- [Documentatie voor Azure Files](/azure/storage/files/storage-files-introduction)
- [Snel gestart](/azure/storage/files/storage-files-quick-create-use-windows)

## <a name="azure-queue-storage"></a>Azure Queue Storage
[Azure Queue-opslag](https://azure.microsoft.com/services/storage/queues/) is een service voor het opslaan van grote aantallen berichten. U krijgt overal ter wereld toegang tot berichten via geverifieerde oproepen via HTTP of HTTPS. Een wachtrijbericht kan maximaal 64 KB groot zijn. Een wachtrij kan miljoenen berichten bevatten, tot de totale capaciteitslimiet van een opslagaccount. Wachtrijen worden vaak gebruikt om een werkachterstand te maken om asynchroon te verwerken.

**Verwijzingen**
- [Azure-portal](https://portal.azure.com)
- [Documentatie voor Azure Queue Storage](/azure/storage/queues/)
- [Snel gestart](/azure/storage/queues/storage-quickstart-queues-portal)
- [Monsters](/azure/storage/common/storage-samples-dotnet?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
