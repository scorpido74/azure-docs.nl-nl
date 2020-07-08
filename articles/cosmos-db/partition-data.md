---
title: Partitionering en horizontaal schalen in Azure Cosmos DB
description: Meer informatie over de werking van partitionering in Azure Cosmos DB, het configureren van partitionering en partitie sleutels en het kiezen van de juiste partitie sleutel voor uw toepassing.
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/28/2020
ms.openlocfilehash: 57417a80ea83005c01b6f2a17206d46e6c049719
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85112775"
---
# <a name="partitioning-and-horizontal-scaling-in-azure-cosmos-db"></a>Partitionering en horizontaal schalen in Azure Cosmos DB

In dit artikel wordt de relatie tussen logische en fysieke partities uitgelegd. Daarnaast worden de aanbevolen procedures voor het partitioneren beschreven en wordt een uitgebreide weer gave geboden op de manier waarop horizontale schaling werkt in Azure Cosmos DB. Het is niet nodig om deze interne gegevens te begrijpen om [uw partitie sleutel te selecteren](partitioning-overview.md#choose-partitionkey) , maar we hebben deze gedekt, zodat u de duidelijkheid hebt over de werking van Azure Cosmos db.

## <a name="logical-partitions"></a>Logische partities

Een logische partitie bestaat uit een set items die dezelfde partitie sleutel hebben. In een container die bijvoorbeeld gegevens bevat over voedsel voeding, bevatten alle items een `foodGroup` eigenschap. U kunt gebruiken `foodGroup` als de partitie sleutel voor de container. Groepen van items waarvoor specifieke waarden zijn opgegeven voor `foodGroup` , zoals `Beef Products` , `Baked Products` en `Sausages and Luncheon Meats` , vormen afzonderlijke logische partities. U hoeft zich geen zorgen te maken over het verwijderen van een logische partitie wanneer de onderliggende gegevens worden verwijderd.

Een logische partitie definieert ook het bereik van database transacties. U kunt items binnen een logische partitie bijwerken met behulp van een [trans actie met snap shot-isolatie](database-transactions-optimistic-concurrency.md). Wanneer nieuwe items worden toegevoegd aan een container, worden nieuwe logische partities transparant gemaakt door het systeem.

Er is geen limiet voor het aantal logische partities in de container. Elke logische partitie kan Maxi maal 20 GB aan gegevens bevatten. Goede opties voor de partitie sleutel hebben een breed scala aan mogelijke waarden. Bijvoorbeeld, in een container waarin alle items een eigenschap bevatten `foodGroup` , kunnen de gegevens in de `Beef Products` logische partitie tot 20 GB groeien. Als [u een partitie sleutel selecteert](partitioning-overview.md#choose-partitionkey) met een breed scala aan mogelijke waarden, zorgt u ervoor dat de container kan worden geschaald.

## <a name="physical-partitions"></a>Fysieke partities

Een Azure Cosmos-container wordt geschaald door gegevens en door voer te distribueren over fysieke partities. Intern worden een of meer logische partities toegewezen aan één fysieke partitie. De meeste kleine Cosmos-containers hebben een groot aantal logische partities, maar vereisen slechts één fysieke partitie. In tegens telling tot logische partities vormen fysieke partities een interne implementatie van het systeem en ze worden volledig beheerd door Azure Cosmos DB.

Het aantal fysieke partities in de Cosmos-container is afhankelijk van het volgende:

- Hoeveelheid ingerichte door Voer (elke afzonderlijke fysieke partitie kan een doorvoer snelheid hebben van Maxi maal 10.000 aanvraag eenheden per seconde)
- Totale gegevens opslag (elke afzonderlijke fysieke partitie kan Maxi maal 50 GB bevatten)

Er is geen limiet voor het totale aantal fysieke partities in de container. Als uw ingerichte door Voer of gegevens grootte groeit, Azure Cosmos DB automatisch nieuwe fysieke partities maken door bestaande te splitsen. Fysieke partitie splitsingen hebben geen invloed op de beschik baarheid van uw toepassing. Nadat de fysieke partitie is gesplitst, worden alle gegevens in één logische partitie nog steeds opgeslagen op dezelfde fysieke partitie. Een fysieke partitie splitsing maakt simpelweg een nieuwe toewijzing van logische partities aan fysieke partities.

De door Voer die is ingericht voor een container, wordt gelijkmatig verdeeld over fysieke partities. Een partitie sleutel ontwerp dat de doorvoer aanvragen niet gelijkmatig distribueert, kan leiden tot het maken van ' hot ' partities. Dynamische partities kunnen leiden tot een frequentie beperking en een inefficiënt gebruik van de ingerichte door Voer en hogere kosten.

U kunt de fysieke partities van uw container bekijken in de sectie **opslag** van de **Blade metrische gegevens** van de Azure portal:

:::image type="content" source="./media/partition-data/view-partitions-zoomed-out.png" alt-text="Aantal fysieke partities weer geven" lightbox="./media/partition-data/view-partitions-zoomed-in.png" ::: 

In dit voor beeld waarin we hebben gekozen `/foodGroup` als onze partitie sleutel, vertegenwoordigen elk van de drie rechthoeken een fysieke partitie. In de installatie kopie is het **partitie sleutel bereik** hetzelfde als een fysieke partitie. De geselecteerde fysieke partitie bevat drie logische partities: `Beef Products` , `Vegetable and Vegetable Products` en `Soups, Sauces, and Gravies` .

Als we een door Voer van 18.000 aanvraag eenheden per seconde (RU/s) inrichten, kan elk van de drie fysieke partities 1/3 van de totale ingerichte door Voer gebruiken. Binnen de geselecteerde fysieke partitie, de logische partitie sleutels `Beef Products` , `Vegetable and Vegetable Products` en `Soups, Sauces, and Gravies` kan gezamenlijk de 6.000 ingerichte ru/s van de fysieke partitie gebruiken. Omdat de ingerichte door Voer gelijkmatig is verdeeld over de fysieke partities van uw container, is het belang rijk om een partitie sleutel te kiezen waarmee het doorvoer verbruik gelijkmatig kan worden verdeeld door [de juiste logische partitie sleutel te kiezen](partitioning-overview.md#choose-partitionkey). Als u een partitie sleutel kiest die het doorvoer verbruik gelijkmatig distribueert over logische partities, zorgt u ervoor dat het doorvoer verbruik over fysieke partities evenwichtig is.

## <a name="replica-sets"></a>Replica sets

Elke fysieke partitie bestaat uit een set replica's, ook wel een [*replicaset*](global-dist-under-the-hood.md)genoemd. Elke replica set host een exemplaar van de Azure Cosmos data base-engine. Een replicaset zorgt ervoor dat de gegevens die zijn opgeslagen in de fysieke partitie, duurzaam, Maxi maal beschikbaar en consistent zijn. Elke replica die de fysieke partitie vormt, neemt de opslag limiet van de partitie over. Alle replica's van een fysieke partitie ondersteunen gezamenlijk de door Voer die is toegewezen aan de fysieke partitie. Azure Cosmos DB worden automatisch replica sets beheerd.

Voor de meeste kleine Cosmos-containers is slechts één fysieke partitie vereist, maar er zijn nog steeds ten minste vier replica's.

In de volgende afbeelding ziet u hoe logische partities worden toegewezen aan fysieke partities die globaal worden gedistribueerd:

:::image type="content" source="./media/partition-data/logical-partitions.png" alt-text="Een afbeelding die Azure Cosmos DB partitionering demonstreert" border="false":::

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [het kiezen van een partitie sleutel](partitioning-overview.md#choose-partitionkey).
* Meer informatie over [ingerichte door Voer in azure Cosmos DB](request-units.md).
* Meer informatie over [globale distributie in azure Cosmos DB](distribute-data-globally.md).
* Meer informatie over het [inrichten van de door Voer voor een Azure Cosmos-container](how-to-provision-container-throughput.md).
* Meer informatie over het [inrichten van de door Voer voor een Azure Cosmos-data base](how-to-provision-database-throughput.md).
