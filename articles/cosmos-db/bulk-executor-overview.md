---
title: Overzicht van de bibliotheek bulk-uitvoeringen Azure Cosmos DB
description: Voer bulk bewerkingen uit in Azure Cosmos DB via bulksgewijze import-en bulk update-Api's die worden aangeboden door de bibliotheek voor bulk-uitvoerder.
author: tknandu
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: ramkris
ms.reviewer: sngun
ms.openlocfilehash: 9d335bcf6daf0b38e7a68ca2d40894dd64c93e40
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75442158"
---
# <a name="azure-cosmos-db-bulk-executor-library-overview"></a>Overzicht van de bibliotheek bulk-uitvoeringen Azure Cosmos DB
 
Azure Cosmos DB is een snelle, flexibele en wereldwijd gedistribueerde databaseservice die ontworpen is om elastisch naar ondersteuning uit te schalen: 

* Grote lees- en schrijfdoorvoer (miljoenen bewerkingen per seconde).  
* Opslag van grote volumes (honderden terabytes, of zelfs meer) transactionele en operationele gegevens met een voorspelbare latentie van milliseconden.  

De BulkExecutor-bibliotheek helpt u bij het benutten van deze enorme doorvoer en opslag. De BulkExecutor-bibliotheek stelt u in staat om bulkbewerkingen in Azure Cosmos DB uit te voeren door middel van API’s voor bulksgewijs importeren en bijwerken. U kunt meer lezen over de functies van de BulkExecutor-bibliotheek in de volgende secties. 

> [!NOTE] 
> De bibliotheek voor bulk-uitvoerbaar ondersteunt nu import-en update bewerkingen en deze bibliotheek wordt alleen ondersteund door Azure Cosmos DB SQL API-en Gremlin-API-accounts.
 
## <a name="key-features-of-the-bulk-executor-library"></a>Belangrijkste functies van de bibliotheek voor bulk-uitvoerder  
 
* Het vermindert ook de reken resources aan de client zijde die nodig zijn om de door voer te verzadigen die aan een container is toegewezen. Een single-thread toepassing die gegevens schrijft met behulp van de API voor bulk import, behaalt tien keer meer schrijf doorvoer in vergelijking met een multi thread-toepassing die parallel gegevens schrijft terwijl de CPU van de client computer wordt geverzadigd.  

* Er wordt een overzicht van de tijdrovende taken voor het schrijven van toepassings logica voor het afhandelen van de verwerkings frequentie van aanvragen, time-outs en andere tijdelijke uitzonde ringen door ze in de bibliotheek efficiënt te verwerken.  

* Het biedt een vereenvoudigd mechanisme voor toepassingen die bulk bewerkingen uitvoeren om uit te breiden. Een exemplaar van een bulk-uitvoerder dat wordt uitgevoerd op een virtuele machine van Azure kan meer dan 500.000 RU/s gebruiken en u kunt een hogere doorvoer snelheid krijgen door extra instanties toe te voegen op afzonderlijke client-Vm's.  
 
* Het kan bulksgewijs meer dan een terabyte van gegevens binnen een uur importeren met behulp van een scale-out architectuur.  

* Bestaande gegevens in azure Cosmos-containers kunnen bulksgewijs worden bijgewerkt als patches. 
 
## <a name="how-does-the-bulk-executor-operate"></a>Hoe werkt de bulk-uitvoerder? 

Wanneer een bulk bewerking voor het importeren of bijwerken van documenten wordt geactiveerd met een batch-entiteit, worden deze in eerste instantie in een wille keurige volg orde gerangschikt die overeenkomt met het Azure Cosmos DB partitie sleutel bereik. Binnen elke Bucket die overeenkomt met een partitie sleutel bereik, worden deze onderverdeeld in Mini batches en elke mini-batch fungeert als een Payload die aan de server zijde wordt doorgevoerd. De bibliotheek voor bulk-uitvoerder bevat optimalisaties voor gelijktijdige uitvoering van deze mini-batches binnen en tussen partitie sleutel bereik. In de volgende afbeelding ziet u hoe bulk-uitvoerder batch gegevens omzetten in verschillende partitie sleutels:  

![Architectuur voor bulk-uitvoerder](./media/bulk-executor-overview/bulk-executor-architecture.png)

De bibliotheek bulk-uitvoerder zorgt ervoor dat de door Voer die is toegewezen aan een verzameling maximally wordt gebruikt. Het maakt gebruik van een [AIMD-mechanisme](https://tools.ietf.org/html/rfc5681) voor elk Azure Cosmos DB partitie sleutel bereik om frequentie limieten en time-outs efficiënt te kunnen afhandelen. 

## <a name="next-steps"></a>Volgende stappen 
  
* Meer informatie over het uitproberen van de voorbeeld toepassingen die de bulk-uitvoerder bibliotheek in [.net](bulk-executor-dot-net.md) en [Java](bulk-executor-java.md)gebruiken.  
* Bekijk de informatie over de bulk-uitvoerder-SDK en release opmerkingen in [.net](sql-api-sdk-bulk-executor-dot-net.md) en [Java](sql-api-sdk-bulk-executor-java.md).
* De bibliotheek bulk-uitvoerder is geïntegreerd in de Cosmos DB Spark-connector, Zie [Azure Cosmos DB artikel Spark-connector](spark-connector.md) voor meer informatie.  
* De bibliotheek bulk-uitvoerder is ook geïntegreerd in een nieuwe versie van [Azure Cosmos DB connector](https://aka.ms/bulkexecutor-adf-v2) voor Azure Data Factory om gegevens te kopiëren.
