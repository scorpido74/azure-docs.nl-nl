---
title: Cloud opslag om zeer veilige, duurzame, schaal bare apps te bouwen met Azure Storage
description: Meer informatie over de services voor het opslaan van grote gestructureerde en niet-gestructureerde mobiele toepassings gegevens in de Cloud.
author: elamalani
ms.assetid: 12bbb070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: 01407f843ef36095fc87feb3722e85dc477ad8bb
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795651"
---
# <a name="cloud-storage-for-highly-secure-durable-scalable-apps-with-azure-storage"></a>Cloud opslag voor zeer veilige, duurzame, schaal bare apps met Azure Storage
[Azure Storage](https://azure.microsoft.com/services/storage/) is de oplossing voor Cloud opslag van micro soft voor moderne toepassingen die een zeer schaal bare object opslag voor gegevens objecten, een File System-Service voor de Cloud, een berichten Archief voor betrouw bare berichten en een NoSQL Store bieden. Azure Storage is:
- **Duurzame en Maxi maal beschik bare** redundantie zorgt ervoor dat uw gegevens veilig zijn in het geval van tijdelijke hardwarefouten. U kunt er ook voor kiezen gegevens te repliceren in datacenters of geografische regio's voor extra beveiliging tegen lokale rampen of natuurrampen. Op deze manier gerepliceerde gegevens blijven maximaal beschikbaar in het geval van een stroomstoring.
- **Beveiligd** : alle gegevens die naar Azure Storage zijn geschreven, worden versleuteld door de service. Azure Storage biedt u gedetailleerde controle over wie toegang tot uw gegevens heeft.
- **Schaal bare** Services zijn zodanig ontworpen dat ze zeer schaalbaar zijn om te voldoen aan de vereisten voor gegevens opslag en prestaties van de huidige toepassingen.
- **Beheerd** -Azure verwerkt het onderhoud, updates en kritieke problemen voor hardware.
- De gegevens zijn overal ter wereld **toegankelijk** via http of https. Micro soft biedt client bibliotheken in verschillende talen, waaronder .NET, Java, node. js, Python, PHP, Ruby, Go en anderen, en een rijp REST API. Scripting wordt ondersteund in Azure PowerShell of Azure CLI en de Azure Portal en Azure Storage Explorer bieden eenvoudige visuele oplossingen voor het werken met uw gegevens.

Gebruik de volgende services om Cloud opslag in te scha kelen in uw mobiele apps.

## <a name="azure-blob-storage"></a>Azure Blob Storage
[Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/) biedt een oplossing voor object opslag voor de Cloud en is geoptimaliseerd voor het opslaan van enorme hoeveel heden ongestructureerde gegevens die niet in overeenstemming zijn met een bepaald gegevens model of specifieke definitie, zoals tekst of binair. Het ondersteunt client bibliotheken met verschillende talen en is ontworpen voor:
- Het rechtstreeks aan een browser leveren van afbeeldingen of documenten.
- De opslag van bestanden voor gedistribueerde toegang.
- Streaming van video en audio.
- Schrijven naar logboekbestanden.
- De opslag van gegevens voor back-up en herstel, herstel na noodgevallen en archivering.
- De opslag van gegevens voor analyse door een on-premises of in Azure gehoste service.

**Referentie**
- [Azure-portal](https://portal.azure.com)
- [Documentatie](/azure/storage/blobs/storage-blobs-introduction)
- [Snelstartgidsen](/azure/storage/blobs/storage-quickstart-blobs-portal)
- [Voorbeelden](/azure/storage/common/storage-samples-dotnet?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

## <a name="azure-table-storage"></a>Azure-tabelopslag
[Azure Table Storage](https://azure.microsoft.com/services/storage/tables/) is een service waarmee gestructureerde NoSQL-gegevens worden opgeslagen in de Cloud, waarbij een sleutel/kenmerk opslag wordt geleverd met een schema-minder ontwerp. Met Azure Table Storage kunnen grote hoeveelheden gestructureerde gegevens worden opgeslagen. De service is een NoSQL-gegevens archief dat geverifieerde aanroepen binnen en buiten de Azure-Cloud accepteert. Azure-tabellen zijn ideaal voor het opslaan van gestructureerde, niet-relationele gegevens. Enkele voorbeelden van veelvoorkomende toepassingen van Tabel Storage:
- Het opslaan van de TBs van gestructureerde gegevens die kunnen worden toegepast op webschaal toepassingen.
- Het opslaan van gegevens sets waarvoor geen complexe samen voegingen, refererende sleutels of opgeslagen procedures zijn vereist en die kunnen worden genormaliseerd voor snelle toegang.
- Snel query's uitvoeren op gegevens met behulp van een geclusterde index.
- Toegang tot gegevens met behulp van het OData-protocol en LINQ-query's met de WCF data service .NET-bibliotheken.

U kunt Table Storage gebruiken om zeer grote sets gestructureerde, niet-relationele gegevens op te slaan en query’s op de gegevens uit te voeren. En wanneer de vraag toeneemt, worden uw tabellen opgeschaald.

**Referentie**
- [Azure-portal](https://portal.azure.com)
- [Documentatie](/azure/storage/tables/table-storage-overview)
- [Voorbeelden](/azure/cosmos-db/tutorial-develop-table-dotnet?toc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fstorage%2Ftables%2FTOC.json&bc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fbread%2Ftoc.json)
- [Snelstartgidsen](/azure/storage/tables/table-storage-quickstart-portal)

## <a name="azure-files"></a>Azure Files
Met [Azure Files](https://azure.microsoft.com/services/storage/files/) kunt u zeer netwerkbestandsshares met een hoge beschikbaarheid instellen die toegankelijk zijn via het standaard SMB-protocol (Server Message Block). Meerdere Vm's kunnen dezelfde bestanden delen met lees-en schrijf toegang. U kunt de bestanden ook lezen met behulp van de REST-interface of de opslagclientbibliotheken. U kunt vanaf elke locatie in de wereld toegang krijgen tot de bestanden met behulp van een URL die naar het bestand verwijst en een SAS-token (Shared Access Signature) bevat. U kunt SAS-tokens genereren. Met deze tokens hebt u gedurende een opgegeven tijdperiode toegang tot een specifiek privéasset.

Azure-bestandsshares kunnen worden gebruikt voor het volgende:
- On-premises bestands servers vervangen of aanvullen: met populaire besturings systemen, zoals Windows, macOS en Linux kunnen Azure-bestands shares rechtstreeks worden gekoppeld, waar ze zich ook in de wereld bevinden. Azure-bestandsshares kunnen ook met Azure File Sync worden gerepliceerd naar Windows-Servers, on-premises of in de cloud, voor een krachtige en gedistribueerde opslag in de cache van gegevens waar deze worden gebruikt.
- **Til en verplaats toepassingen** naar de cloud die verwachten dat een bestands share bestands toepassing of gebruikers gegevens opslaat.
- **Ontwikkeling van Clouds vereenvoudigen**: u kunt Azure files ook op verschillende manieren gebruiken om nieuwe ontwikkelings projecten in de cloud te vereenvoudigen. Bijvoorbeeld:
    - Instellingen voor gedeelde toepassingen: een gemeen schappelijk patroon voor gedistribueerde toepassingen is het configureren van configuratie bestanden op een centrale locatie waar ze toegankelijk zijn vanuit veel toepassings exemplaren. Toepassingsexemplaren kunnen hun configuratie laden via de File REST-API, en mensen kunnen hier indien nodig toegang toe krijgen door de SMB-share lokaal te koppelen.
    - Diagnostische share: een Azure-bestands share is een handige locatie voor Cloud toepassingen voor het schrijven van de logboeken, metrische gegevens en crash dumps. Logboeken kunnen door de toepassingsexemplaren worden geschreven via de File REST-API en ontwikkelaars kunnen er toegang toe krijgen door de bestandsshare te koppelen met hun lokale computer. Dit biedt veel flexibiliteit, aangezien ontwikkelaars kunnen ontwikkelen in de cloud zonder hun bestaande tooling waarmee ze bekend zijn en graag werken te hoeven opgeven.

**Referentie**
- [Azure-portal](https://portal.azure.com)
- [Documentatie](/azure/storage/files/storage-files-introduction)
- [Snelstartgidsen](/azure/storage/files/storage-files-quick-create-use-windows)

## <a name="azure-queues"></a>Azure-wachtrijen
[Azure Queue Storage](https://azure.microsoft.com/services/storage/queues/) is een service voor het opslaan van grote aantallen berichten. U opent berichten van overal ter wereld via geverifieerde oproepen met HTTP of HTTPS. Een wachtrij bericht kan Maxi maal 64 KB groot zijn en een wachtrij kan miljoenen berichten bevatten, tot de totale capaciteits limiet van een opslag account. Wacht rijen worden vaak gebruikt om een achterstand te maken voor het asynchroon verwerken van werk.

**Referentie**
- [Azure-portal](https://portal.azure.com)
- [Documentatie](/azure/storage/queues/)
- [Snelstartgidsen](/azure/storage/queues/storage-quickstart-queues-portal)
- [Voorbeelden](/azure/storage/common/storage-samples-dotnet?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
