---
title: Partitionering en horizontaal schalen in Azure Cosmos DB
description: Meer informatie over hoe partitionering werkt in Azure Cosmos DB, hoe u partitie- en partitiesleutels configureert en hoe u de juiste partitiesleutel voor uw toepassing kiezen.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: cbd171e10cc1a8b27de98d9d4d779f345ac5a3ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246615"
---
# <a name="partitioning-and-horizontal-scaling-in-azure-cosmos-db"></a>Partitionering en horizontaal schalen in Azure Cosmos DB

In dit artikel wordt de werking van fysieke en logische partities in Azure Cosmos DB beschreven. Ook worden best practices voor schalen en partitioneren besproken. 

## <a name="logical-partitions"></a>Logische partities

Een logische partitie bestaat uit een set items met dezelfde partitiesleutel. In een container waar alle artikelen `City` een eigenschap `City` bevatten, u bijvoorbeeld de partitiesleutel voor de container gebruiken. Groepen items met specifieke `City`waarden voor `London` `Paris`, `NYC`zoals , en , vormen afzonderlijke logische partities. U hoeft zich geen zorgen te maken over het verwijderen van een partitie wanneer de onderliggende gegevens worden verwijderd.

In Azure Cosmos DB is een container de fundamentele eenheid van schaalbaarheid. Gegevens die aan de container zijn toegevoegd en de doorvoer die u op de container indient, worden automatisch (horizontaal) verdeeld over een set logische partities. Gegevens en doorvoer worden verdeeld op basis van de partitiesleutel die u opgeeft voor de Azure Cosmos-container. Zie [Een Azure Cosmos-container maken](how-to-create-container.md)voor meer informatie.

Een logische partitie definieert ook het bereik van databasetransacties. U items in een logische partitie bijwerken met behulp van een [transactie met momentopnameisolatie](database-transactions-optimistic-concurrency.md). Wanneer nieuwe items aan een container worden toegevoegd, worden nieuwe logische partities transparant gemaakt door het systeem.

## <a name="physical-partitions"></a>Fysieke partities

Een Azure Cosmos-container wordt geschaald door gegevens en doorvoer te distribueren over een groot aantal logische partities. Intern worden een of meer logische partities toegewezen aan een fysieke partitie die bestaat uit een set replica's, ook wel [*replicaset*](global-dist-under-the-hood.md)genoemd. Elke replicaset bevat een exemplaar van de Azure Cosmos-databaseengine. Een replicaset maakt de gegevens die zijn opgeslagen in de fysieke partitie duurzaam, zeer beschikbaar en consistent. Een fysieke partitie ondersteunt de maximale hoeveelheid opslag- en aanvraageenheden (RU's). Elke replica die deel uitmaakt van de fysieke partitie erft het opslagquotum van de partitie. Alle replica's van een fysieke partitie ondersteunen gezamenlijk de doorvoer die is toegewezen aan de fysieke partitie. 

In de volgende afbeelding ziet u hoe logische partities worden toegewezen aan fysieke partities die wereldwijd worden gedistribueerd:

![Een afbeelding die Azure Cosmos DB-partitionering demonstreert](./media/partition-data/logical-partitions.png)

Doorvoer die voor een container is ingericht, wordt gelijkmatig verdeeld over fysieke partities. Een ontwerp van partitiesleutels dat de doorvoeraanvragen niet gelijkmatig verdeelt, kan 'hot'-partities maken. Hot partities kunnen leiden tot tariefbeperkend en inefficiënt gebruik van de ingerichte doorvoer en hogere kosten.

In tegenstelling tot logische partities zijn fysieke partities een interne implementatie van het systeem. U de grootte, plaatsing of het aantal fysieke partities niet beheren en u de toewijzing tussen logische partities en fysieke partities niet beheren. U echter het aantal logische partities en de verdeling van gegevens, werkbelasting en doorvoer beheren door [de juiste logische partitiesleutel te kiezen.](partitioning-overview.md#choose-partitionkey)

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [het kiezen van een partitiesleutel](partitioning-overview.md#choose-partitionkey).
* Meer informatie over [ingerichte doorvoer in Azure Cosmos DB](request-units.md).
* Meer informatie over [wereldwijde distributie in Azure Cosmos DB](distribute-data-globally.md).
* Meer informatie over het [inrichten van doorvoer op een Azure Cosmos-container](how-to-provision-container-throughput.md).
* Meer informatie over het [inrichten van doorvoer op een Azure Cosmos-database](how-to-provision-database-throughput.md).
