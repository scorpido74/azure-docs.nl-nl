---
title: 'Azure Cosmos DB: overzicht van BulkExecutor-bibliotheek'
description: Bulkbewerkingen uitvoeren in Azure Cosmos DB via bulkimport- en bulkupdate-API's die worden aangeboden door de bulkexecutorbibliotheek.
author: tknandu
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: ramkris
ms.reviewer: sngun
ms.openlocfilehash: 9d335bcf6daf0b38e7a68ca2d40894dd64c93e40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75442158"
---
# <a name="azure-cosmos-db-bulk-executor-library-overview"></a>Azure Cosmos DB: overzicht van BulkExecutor-bibliotheek
 
Azure Cosmos DB is een snelle, flexibele en wereldwijd gedistribueerde databaseservice die ontworpen is om elastisch naar ondersteuning uit te schalen: 

* Grote lees- en schrijfdoorvoer (miljoenen bewerkingen per seconde).  
* Opslag van grote volumes (honderden terabytes, of zelfs meer) transactionele en operationele gegevens met een voorspelbare latentie van milliseconden.  

De BulkExecutor-bibliotheek helpt u bij het benutten van deze enorme doorvoer en opslag. De BulkExecutor-bibliotheek stelt u in staat om bulkbewerkingen in Azure Cosmos DB uit te voeren door middel van API’s voor bulksgewijs importeren en bijwerken. U kunt meer lezen over de functies van de BulkExecutor-bibliotheek in de volgende secties. 

> [!NOTE] 
> Momenteel ondersteunt de bulkexecutorbibliotheek import- en updatebewerkingen en deze bibliotheek wordt alleen ondersteund door Azure Cosmos DB SQL API- en Gremlin-API-accounts.
 
## <a name="key-features-of-the-bulk-executor-library"></a>Belangrijkste kenmerken van de bibliotheek voor bulkuitvoerbedrijven  
 
* Het vermindert aanzienlijk de client-side compute resources die nodig zijn om de doorvoer toegewezen aan een container te verzadigen. Een single threaded applicatie die gegevens schrijft met behulp van de bulk import API bereikt 10 keer meer schrijfdoorvoer in vergelijking met een multi-threaded applicatie die gegevens schrijft in parallel, terwijl het verzadigen van de client machine CPU.  

* Het abstraheert de vervelende taken van het schrijven van toepassingslogica om tariefbeperkende van de aanvraag, time-outs van aanvragen en andere tijdelijke uitzonderingen te verwerken door ze efficiënt in de bibliotheek te verwerken.  

* Het biedt een vereenvoudigd mechanisme voor toepassingen die bulkbewerkingen uitvoeren om uit te schalen. Een enkele bulkexecutorinstantie die wordt uitgevoerd op een Azure VM kan meer dan 500K RU/s verbruiken en u een hogere doorvoersnelheid bereiken door extra exemplaren toe te voegen aan afzonderlijke client-VM's.  
 
* Het kan bulk importeren meer dan een terabyte aan gegevens binnen een uur met behulp van een scale-out architectuur.  

* Het kan bestaande gegevens in Azure Cosmos-containers bulkbijwerken als patches. 
 
## <a name="how-does-the-bulk-executor-operate"></a>Hoe werkt de bulkuitvoerder? 

Wanneer een bulkbewerking voor het importeren of bijwerken van documenten wordt geactiveerd met een batch entiteiten, worden ze in eerste instantie geschud in buckets die overeenkomen met hun Azure Cosmos DB-partitiesleutelbereik. Binnen elke bucket die overeenkomt met een partitiesleutelbereik, worden ze opgesplitst in minibatches en elke minibatch fungeert als een payload die is vastgelegd aan de serverkant. De bulk executor bibliotheek heeft ingebouwde optimalisaties voor gelijktijdige uitvoering van deze mini-batches, zowel binnen als tussen partitie sleutel bereiken. Onderstaande afbeelding illustreert hoe bulkuitvoerdergegevens batches in verschillende partitiesleutels:  

![Architectuur voor bulkuitvoerbeleid](./media/bulk-executor-overview/bulk-executor-architecture.png)

De bulk executor bibliotheek zorgt ervoor dat maximaal gebruik maken van de doorvoer toegewezen aan een verzameling. Het maakt gebruik van een [AIMD-stijl congestie controlemechanisme](https://tools.ietf.org/html/rfc5681) voor elke Azure Cosmos DB partitie sleutel bereik om efficiënt omgaan met tarief beperking en time-outs. 

## <a name="next-steps"></a>Volgende stappen 
  
* Meer informatie door de voorbeeldtoepassingen uit te proberen die de bibliotheek voor bulkuitvoerbestanden in [.NET](bulk-executor-dot-net.md) en [Java gebruiken](bulk-executor-java.md).  
* Bekijk de bulk executor SDK informatie en release notes in [.NET](sql-api-sdk-bulk-executor-dot-net.md) en [Java](sql-api-sdk-bulk-executor-java.md).
* De bulkexecutorbibliotheek is geïntegreerd in de Cosmos DB Spark-connector, zie het artikel [azure cosmos DB Spark-connector.](spark-connector.md)  
* De bulkexecutorbibliotheek is ook geïntegreerd in een nieuwe versie van [azure cosmos DB-connector](https://aka.ms/bulkexecutor-adf-v2) voor Azure Data Factory om gegevens te kopiëren.
