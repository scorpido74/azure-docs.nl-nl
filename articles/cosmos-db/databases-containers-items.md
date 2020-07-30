---
title: Werken met data bases, containers en items in Azure Cosmos DB
description: In dit artikel wordt beschreven hoe u data bases, containers en items in Azure Cosmos DB maakt en gebruikt.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/24/2020
ms.reviewer: sngun
ms.openlocfilehash: e1718ac9a7b7fcaab096595ea7341fcc90c2ddd6
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/29/2020
ms.locfileid: "87422331"
---
# <a name="work-with-databases-containers-and-items-in-azure-cosmos-db"></a>Werken met data bases, containers en items in Azure Cosmos DB

Nadat u een [Azure Cosmos DB account](account-overview.md) hebt gemaakt onder uw Azure-abonnement, kunt u gegevens in uw account beheren door data bases, containers en items te maken. In dit artikel wordt elk van deze entiteiten beschreven. 

In de volgende afbeelding ziet u de hiërarchie van verschillende entiteiten in een Azure Cosmos DB-account:

:::image type="content" source="./media/databases-containers-items/cosmos-entities.png" alt-text="Entiteiten van Azure Cosmos-account" border="false":::

## <a name="azure-cosmos-databases"></a>Azure Cosmos-databases

U kunt een of meer Azure Cosmos-data bases maken onder uw account. Een data base komt overeen met een naam ruimte. Een Data Base is de beheer eenheid voor een set Azure Cosmos-containers. In de volgende tabel ziet u hoe een Azure Cosmos-data base wordt toegewezen aan verschillende API-specifieke entiteiten:

| Azure Cosmos-entiteit | SQL-API | Cassandra-API | Azure Cosmos DB-API voor MongoDB | Gremlin-API | Tabel-API |
| --- | --- | --- | --- | --- | --- |
|Azure Cosmos-data base | Database | Keys Pace | Database | Database | NA |

> [!NOTE]
> Wanneer u uw eerste tabel maakt met Table-API accounts, wordt er automatisch een standaard database gemaakt in uw Azure Cosmos-account.

### <a name="operations-on-an-azure-cosmos-database"></a>Bewerkingen in een Azure Cosmos-data base

U kunt communiceren met een Azure Cosmos-data base met Azure Cosmos-Api's, zoals beschreven in de volgende tabel:

| Bewerking | Azure CLI | SQL-API | Cassandra-API | Azure Cosmos DB-API voor MongoDB | Gremlin-API | Tabel-API |
| --- | --- | --- | --- | --- | --- | --- |
|Alle data bases opsommen| Ja | Ja | Ja (Data Base is toegewezen aan een spatie) | Ja | NA | NA |
|Data Base lezen| Ja | Ja | Ja (Data Base is toegewezen aan een spatie) | Ja | NA | NA |
|Nieuwe data base maken| Ja | Ja | Ja (Data Base is toegewezen aan een spatie) | Ja | NA | NA |
|Data base bijwerken| Ja | Ja | Ja (Data Base is toegewezen aan een spatie) | Ja | NA | NA |


## <a name="azure-cosmos-containers"></a>Azure Cosmos-containers

Een Azure Cosmos-container is de eenheid van schaal baarheid voor ingerichte door Voer en opslag. Een container is horizon taal gepartitioneerd en vervolgens gerepliceerd in meerdere regio's. De items die u toevoegt aan de container en de door Voer die u hebt ingericht, worden automatisch gedistribueerd over een set logische partities op basis van de partitie sleutel. Zie [partitie gegevens](partition-data.md)voor meer informatie over partitioneren en partitie sleutels. 

Wanneer u een Azure Cosmos-container maakt, configureert u door Voer in een van de volgende modi:

* **Toegewezen doorvoer modus voor ingerichte**: de door Voer die is ingericht voor een container is exclusief gereserveerd voor die container en wordt ondersteund door de sla's. Zie voor meer informatie [over het inrichten van de door Voer voor een Azure Cosmos-container](how-to-provision-container-throughput.md).

* **Gedeelde doorvoer modus**voor opslag: deze containers delen de ingerichte door Voer met de andere containers in dezelfde data base (met uitzonde ring van containers die zijn geconfigureerd met een toegewezen ingerichte door Voer). Met andere woorden, de ingerichte door Voer voor de data base wordt gedeeld door alle "gedeelde door Voer"-containers. Zie [het inrichten van de door Voer voor een Azure Cosmos-data base](how-to-provision-database-throughput.md)voor meer informatie.

> [!NOTE]
> U kunt gedeelde en toegewezen door Voer alleen configureren bij het maken van de data base en container. Als u wilt overschakelen van de modus voor toegewezen doorvoer naar de modus voor gedeelde doorvoer (of omgekeerd) nadat de container is gemaakt, moet u een nieuwe container maken en de gegevens naar de nieuwe container migreren. U kunt de gegevens migreren met behulp van de Azure Cosmos DB functie voor het wijzigen van de feed.

Een Azure Cosmos-container kan flexibel worden geschaald, ongeacht of u containers maakt met behulp van toegewezen of gedeelde ingerichte doorvoer modi.

Een Azure Cosmos-container is een schema-neutraal-container van items. Items in een container kunnen wille keurige schema's hebben. Een item dat een persoon vertegenwoordigt en een item dat een auto vertegenwoordigt, kan bijvoorbeeld in *dezelfde container*worden geplaatst. Standaard worden alle items die u toevoegt aan een container automatisch geïndexeerd zonder dat hiervoor expliciete index of schema beheer nodig is. U kunt het indexerings gedrag aanpassen door het [indexerings beleid](index-overview.md) op een container te configureren. 

U kunt [time to Live (TTL)](time-to-live.md) instellen voor geselecteerde items in een Azure Cosmos-container of voor de hele container om deze items op een correcte manier van het systeem te verwijderen. Azure Cosmos DB worden automatisch de items verwijderd wanneer deze verlopen. Het zorgt er ook voor dat een query die in de container wordt uitgevoerd, niet de verlopen items binnen een vaste grens retourneert. Zie [Configure TTL in your container](how-to-time-to-live.md)voor meer informatie.

U kunt [Change feed](change-feed.md) gebruiken om u te abonneren op het operations-logboek dat wordt beheerd voor elke logische partitie van de container. De wijzigings feed biedt het logboek van alle updates die in de container worden uitgevoerd, samen met de voor-en na-installatie kopieën van de items. Zie [reactieve toepassingen bouwen met behulp van Change feed](serverless-computing-database.md)voor meer informatie. U kunt ook de Bewaar periode voor de wijzigings feed configureren met behulp van het beleid voor wijzigings invoer in de container.

U kunt [opgeslagen procedures, triggers, door de gebruiker gedefinieerde functies (udf's)](stored-procedures-triggers-udfs.md)en [samenvoeg procedures](how-to-manage-conflicts.md) voor uw Azure Cosmos-container registreren.

U kunt een [Unique key-beperking](unique-keys.md) opgeven in de Azure Cosmos-container. Door een uniek sleutel beleid te maken, zorgt u ervoor dat een of meer waarden per logische partitie sleutel uniek zijn. Als u een container maakt met behulp van een beleid met unieke sleutels, kunnen er geen nieuwe of bijgewerkte items worden gemaakt met waarden die de waarden dupliceren die zijn opgegeven door de beperking voor unieke sleutels. Zie [beperkingen voor unieke sleutels](unique-keys.md)voor meer informatie.

Een Azure Cosmos-container is gespecialiseerd in API-specifieke entiteiten, zoals wordt weer gegeven in de volgende tabel:

| Azure Cosmos-entiteit | SQL-API | Cassandra-API | Azure Cosmos DB-API voor MongoDB | Gremlin-API | Tabel-API |
| --- | --- | --- | --- | --- | --- |
|Azure Cosmos-container | Container | Tabel | Verzameling | Graph | Tabel |

> [!NOTE]
> Wanneer u containers maakt, moet u ervoor zorgen dat u niet twee containers maakt met dezelfde naam, maar met een ander hoofdletter gebruik. Dat komt omdat sommige onderdelen van het Azure-platform niet hoofdletter gevoelig zijn. Dit kan leiden tot Verwar ring/botsing van telemetriegegevens en acties op containers met dergelijke namen.

### <a name="properties-of-an-azure-cosmos-container"></a>Eigenschappen van een Azure Cosmos-container

Een Azure Cosmos-container heeft een reeks door het systeem gedefinieerde eigenschappen. Afhankelijk van welke API u gebruikt, zijn sommige eigenschappen mogelijk niet rechtstreeks zichtbaar. In de volgende tabel wordt de lijst met door het systeem gedefinieerde eigenschappen beschreven:

| Door het systeem gedefinieerde eigenschap | Door het systeem gegenereerd of door de gebruiker te configureren | Doel | SQL-API | Cassandra-API | Azure Cosmos DB-API voor MongoDB | Gremlin-API | Tabel-API |
| --- | --- | --- | --- | --- | --- | --- | --- |
|\_rid | Door het systeem gegenereerd | Unieke id van de container | Ja | Nee | Nee | Nee | Nee |
|\_ETAG | Door het systeem gegenereerd | Entiteits code die wordt gebruikt voor optimistisch gelijktijdigheids beheer | Ja | Nee | Nee | Nee | Nee |
|\_Terminal | Door het systeem gegenereerd | Laatst bijgewerkte tijds tempel van de container | Ja | Nee | Nee | Nee | Nee |
|\_Online | Door het systeem gegenereerd | Adresseer bare URI van de container | Ja | Nee | Nee | Nee | Nee |
|id | Door de gebruiker te configureren | Door de gebruiker gedefinieerde unieke naam van de container | Ja | Ja | Ja | Ja | Ja |
|indexingPolicy | Door de gebruiker te configureren | Biedt de mogelijkheid om het pad naar de index, het index type en de index modus te wijzigen | Ja | Nee | Nee | Nee | Ja |
|TimeToLive | Door de gebruiker te configureren | Biedt de mogelijkheid om items automatisch uit een container te verwijderen na een bepaalde periode. Zie [time to Live](time-to-live.md)voor meer informatie. | Ja | Nee | Nee | Nee | Ja |
|changeFeedPolicy | Door de gebruiker te configureren | Wordt gebruikt om wijzigingen te lezen die zijn aangebracht in items in een container. Zie [Change feed](change-feed.md)voor meer informatie. | Ja | Nee | Nee | Nee | Ja |
|uniqueKeyPolicy | Door de gebruiker te configureren | Hiermee wordt de uniekheid van een of meer waarden in een logische partitie gegarandeerd. Zie [beperkingen voor unieke sleutels](unique-keys.md)voor meer informatie. | Ja | Nee | Nee | Nee | Ja |

### <a name="operations-on-an-azure-cosmos-container"></a>Bewerkingen in een Azure Cosmos-container

Een Azure Cosmos-container ondersteunt de volgende bewerkingen wanneer u een van de Azure Cosmos-Api's gebruikt:

| Bewerking | Azure CLI | SQL-API | Cassandra-API | Azure Cosmos DB-API voor MongoDB | Gremlin-API | Tabel-API |
| --- | --- | --- | --- | --- | --- | --- |
| Containers in een Data Base opsommen | Ja | Ja | Ja | Ja | NA | NA |
| Een container lezen | Ja | Ja | Ja | Ja | NA | NA |
| Een nieuwe container maken | Ja | Ja | Ja | Ja | NA | NA |
| Een container bijwerken | Ja | Ja | Ja | Ja | NA | NA |
| Een container verwijderen | Ja | Ja | Ja | Ja | NA | NA |

## <a name="azure-cosmos-items"></a>Azure Cosmos-items

Afhankelijk van de API die u gebruikt, kan een Azure Cosmos-item een document in een verzameling, een rij in een tabel of een knoop punt of rand in een grafiek vertegenwoordigen. De volgende tabel toont de toewijzing van API-specifieke entiteiten aan een Azure Cosmos-item:

| Cosmos entiteit | SQL-API | Cassandra-API | Azure Cosmos DB-API voor MongoDB | Gremlin-API | Tabel-API |
| --- | --- | --- | --- | --- | --- |
|Azure Cosmos-item | Item | Rij | Document | Knoop punt of rand | Item |

### <a name="properties-of-an-item"></a>Eigenschappen van een item

Elk Azure Cosmos-item heeft de volgende door het systeem gedefinieerde eigenschappen. Afhankelijk van welke API u gebruikt, zijn sommige daarvan mogelijk niet rechtstreeks zichtbaar.

| Door het systeem gedefinieerde eigenschap | Door het systeem gegenereerd of door de gebruiker te configureren| Doel | SQL-API | Cassandra-API | Azure Cosmos DB-API voor MongoDB | Gremlin-API | Tabel-API |
| --- | --- | --- | --- | --- | --- | --- | --- |
|\_rid | Door het systeem gegenereerd | De unieke id van het item | Ja | Nee | Nee | Nee | Nee |
|\_ETAG | Door het systeem gegenereerd | Entiteits code die wordt gebruikt voor optimistisch gelijktijdigheids beheer | Ja | Nee | Nee | Nee | Nee |
|\_Terminal | Door het systeem gegenereerd | Tijds tempel van de laatste update van het item | Ja | Nee | Nee | Nee | Nee |
|\_Online | Door het systeem gegenereerd | Adresseer bare URI van het item | Ja | Nee | Nee | Nee | Nee |
|id | Merken | Door de gebruiker gedefinieerde unieke naam in een logische partitie. | Ja | Ja | Ja | Ja | Ja |
|Wille keurige door de gebruiker gedefinieerde eigenschappen | Door de gebruiker gedefinieerde routes | Door de gebruiker gedefinieerde eigenschappen die worden weer gegeven in API-native representatie (waaronder JSON, BSON en CQL) | Ja | Ja | Ja | Ja | Ja |

> [!NOTE]
> De uniekheid van de `id` eigenschap wordt alleen afgedwongen binnen elke logische partitie. Meerdere documenten kunnen dezelfde eigenschap hebben `id` met andere partitie sleutel waarden.

### <a name="operations-on-items"></a>Bewerkingen op items

Azure Cosmos-items ondersteunen de volgende bewerkingen. U kunt een van de Azure Cosmos-Api's gebruiken om de bewerkingen uit te voeren.

| Bewerking | Azure CLI | SQL-API | Cassandra-API | Azure Cosmos DB-API voor MongoDB | Gremlin-API | Tabel-API |
| --- | --- | --- | --- | --- | --- | --- |
| Invoegen, vervangen, verwijderen, Upsert, lezen | Nee | Ja | Ja | Ja | Ja | Ja |

## <a name="next-steps"></a>Volgende stappen

Meer informatie over deze taken en concepten:

* [Doorvoer inrichten voor een Azure Cosmos-database](how-to-provision-database-throughput.md)
* [Doorvoer inrichten voor een Azure Cosmos-container](how-to-provision-container-throughput.md)
* [Werken met logische partities](partition-data.md)
* [TTL configureren in een Azure Cosmos-container](how-to-time-to-live.md)
* [Reactieve toepassingen bouwen met behulp van Change feed](change-feed.md)
* [Een Unique key-beperking configureren in uw Azure Cosmos-container](unique-keys.md)
