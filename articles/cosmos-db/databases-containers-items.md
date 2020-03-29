---
title: Werken met databases, containers en items in Azure Cosmos DB
description: In dit artikel wordt beschreven hoe u databases, containers en items maakt en gebruikt in Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/01/2019
ms.reviewer: sngun
ms.openlocfilehash: 43a842c3b6d6d421eca4196c7f3facc7876318cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246784"
---
# <a name="work-with-databases-containers-and-items-in-azure-cosmos-db"></a>Werken met databases, containers en items in Azure Cosmos DB

Nadat u een [Azure Cosmos DB-account](account-overview.md) hebt gemaakt onder uw Azure-abonnement, u gegevens in uw account beheren door databases, containers en items te maken. In dit artikel wordt elk van deze entiteiten beschreven. 

In de volgende afbeelding wordt de hiërarchie van verschillende entiteiten in een Azure Cosmos DB-account weergegeven:

![Azure Cosmos-accountentiteiten](./media/databases-containers-items/cosmos-entities.png)

## <a name="azure-cosmos-databases"></a>Azure Cosmos-databases

U een of meerdere Azure Cosmos-databases maken onder uw account. Een database is analoog aan een naamruimte. Een database is de beheereenheid voor een set Azure Cosmos-containers. In de volgende tabel ziet u hoe een Azure Cosmos-database is toegewezen aan verschillende API-specifieke entiteiten:

| Azure Cosmos- entiteit | SQL-API | Cassandra-API | Azure Cosmos DB-API voor MongoDB | Gremlin-API | Tabel-API |
| --- | --- | --- | --- | --- | --- |
|Azure Cosmos-database | Database | Sleutelruimte | Database | Database | N.v.t. |

> [!NOTE]
> Met Tabel API-accounts wordt bij het maken van uw eerste tabel automatisch een standaarddatabase gemaakt in uw Azure Cosmos-account.

### <a name="operations-on-an-azure-cosmos-database"></a>Bewerkingen op een Azure Cosmos-database

U communiceren met een Azure Cosmos-database met Azure Cosmos API's zoals beschreven in de volgende tabel:

| Bewerking | Azure-CLI | SQL-API | Cassandra-API | Azure Cosmos DB-API voor MongoDB | Gremlin-API | Tabel-API |
| --- | --- | --- | --- | --- | --- | --- |
|Alle databases opsommen| Ja | Ja | Ja (database is toegewezen aan een keyspace) | Ja | N.v.t. | N.v.t. |
|Database lezen| Ja | Ja | Ja (database is toegewezen aan een keyspace) | Ja | N.v.t. | N.v.t. |
|Nieuwe database maken| Ja | Ja | Ja (database is toegewezen aan een keyspace) | Ja | N.v.t. | N.v.t. |
|Database bijwerken| Ja | Ja | Ja (database is toegewezen aan een keyspace) | Ja | N.v.t. | N.v.t. |


## <a name="azure-cosmos-containers"></a>Azure Cosmos-containers

Een Azure Cosmos-container is de eenheid van schaalbaarheid voor zowel ingerichte doorvoer als opslag. Een container wordt horizontaal verdeeld en vervolgens gerepliceerd in meerdere regio's. De items die u aan de container toevoegt en de doorvoer die u op deze container indient, worden automatisch verdeeld over een set logische partities op basis van de partitiesleutel. Zie [Partitiegegevens](partition-data.md)voor meer informatie over partitionering en partitiesleutels. 

Wanneer u een Azure Cosmos-container maakt, configureert u de doorvoer in een van de volgende modi:

* **Speciale ingerichte doorvoermodus**: De doorvoer die op een container is ingericht, is uitsluitend gereserveerd voor die container en wordt ondersteund door de SLA's. Zie [Doorvoer voorzien op een Azure Cosmos-container voor](how-to-provision-container-throughput.md)meer informatie.

* **Gedeelde ingerichte doorvoermodus**: Deze containers delen de ingerichte doorvoer met de andere containers in dezelfde database (met uitzondering van containers die zijn geconfigureerd met speciale ingerichte doorvoer). Met andere woorden, de ingerichte doorvoer op de database wordt gedeeld tussen alle "gedeelde doorvoer" containers. Zie [Doorvoer inrichten op een Azure Cosmos-database](how-to-provision-database-throughput.md)voor meer informatie.

> [!NOTE]
> U gedeelde en speciale doorvoer alleen configureren bij het maken van de database en container. Als u wilt overschakelen van de modus voor toegewezen doorvoer naar de modus voor gedeelde doorvoer (of omgekeerd) nadat de container is gemaakt, moet u een nieuwe container maken en de gegevens naar de nieuwe container migreren. U de gegevens migreren met de feedfunctie Azure Cosmos DB change.

Een Azure Cosmos-container kan elastisch schalen, of u nu containers maakt met behulp van speciale of gedeelde ingerichte doorvoermodi.

Een Azure Cosmos-container is een schema-agnostische container met items. Items in een container kunnen willekeurige schema's hebben. Een item dat een persoon vertegenwoordigt en een item dat een auto vertegenwoordigt, kan bijvoorbeeld in *dezelfde container*worden geplaatst. Standaard worden alle items die u aan een container toevoegt, automatisch geïndexeerd zonder expliciet index- of schemabeheer. U het indexeringsgedrag aanpassen door het [indexeringsbeleid](index-overview.md) voor een container te configureren. 

U [Time to Live (TTL)](time-to-live.md) instellen op geselecteerde items in een Azure Cosmos-container of voor de hele container om deze items op een elegante manier uit het systeem te verwijderen. Azure Cosmos DB verwijdert de items automatisch wanneer ze verlopen. Het garandeert ook dat een query die op de container wordt uitgevoerd, de verlopen artikelen niet binnen een vaste gebonden grens retourneert. Zie [TTL configureren op uw container](how-to-time-to-live.md)voor meer informatie.

U [de wijzigingsfeed](change-feed.md) gebruiken om u te abonneren op het bewerkingslogboek dat wordt beheerd voor elke logische partitie van uw container. Change feed biedt het logboek van alle updates die op de container worden uitgevoerd, samen met de voor- en naafbeeldingen van de items. Zie Reactieve toepassingen opnieuw maken voor meer informatie [met behulp van de wijzigingsfeed](serverless-computing-database.md). U ook de bewaarduur voor de wijzigingsfeed configureren met behulp van het feedbeleid wijzigen in de container. 

U [opgeslagen procedures, triggers, door gebruikers gedefinieerde functies (UDF's)](stored-procedures-triggers-udfs.md)en [samenvoegprocedures](how-to-manage-conflicts.md) voor uw Azure Cosmos-container registreren. 

U een [unieke sleutelbeperking](unique-keys.md) opgeven voor uw Azure Cosmos-container. Door een uniek sleutelbeleid te maken, zorgt u voor de uniciteit van een of meer waarden per logische partitiesleutel. Als u een container maakt met behulp van een uniek sleutelbeleid, kunnen er geen nieuwe of bijgewerkte items worden gemaakt met waarden die de waarden dupliceren die zijn opgegeven door de unieke sleutelbeperking. Zie [Unieke sleutelbeperkingen voor](unique-keys.md)meer informatie.

Een Azure Cosmos-container is gespecialiseerd in API-specifieke entiteiten zoals weergegeven in de volgende tabel:

| Azure Cosmos- entiteit | SQL-API | Cassandra-API | Azure Cosmos DB-API voor MongoDB | Gremlin-API | Tabel-API |
| --- | --- | --- | --- | --- | --- |
|Azure Cosmos-container | Container | Tabel | Verzameling | Graph | Tabel |

### <a name="properties-of-an-azure-cosmos-container"></a>Eigenschappen van een Azure Cosmos-container

Een Azure Cosmos-container heeft een reeks systeemgedefinieerde eigenschappen. Afhankelijk van welke API u gebruikt, worden sommige eigenschappen mogelijk niet direct weergegeven. In de volgende tabel worden de lijst met systeemgedefinieerde eigenschappen beschreven:

| Systeemgedefinieerde eigenschap | Door het systeem gegenereerd of door de gebruiker configureerbaar | Doel | SQL-API | Cassandra-API | Azure Cosmos DB-API voor MongoDB | Gremlin-API | Tabel-API |
| --- | --- | --- | --- | --- | --- | --- | --- |
|\_rid | Door het systeem gegenereerde systemen | Unieke identificatie van container | Ja | Nee | Nee | Nee | Nee |
|\_etag etag | Door het systeem gegenereerde systemen | Entiteitstag gebruikt voor optimistische gelijktijdigheidscontrole | Ja | Nee | Nee | Nee | Nee |
|\_Ts | Door het systeem gegenereerde systemen | Laatst bijgewerkte tijdstempel van de container | Ja | Nee | Nee | Nee | Nee |
|\_Zelf | Door het systeem gegenereerde systemen | Adresseerbare URI van de container | Ja | Nee | Nee | Nee | Nee |
|id | Door de gebruiker configureerbaar | Door de gebruiker gedefinieerde unieke naam van de container | Ja | Ja | Ja | Ja | Ja |
|indexeringBeleid | Door de gebruiker configureerbaar | Biedt de mogelijkheid om het indexpad, het indextype en de indexmodus te wijzigen | Ja | Nee | Nee | Nee | Ja |
|TimeToLive | Door de gebruiker configureerbaar | Biedt de mogelijkheid om items automatisch uit een container te verwijderen na een ingestelde periode. Zie Time [to Live voor](time-to-live.md)meer informatie. | Ja | Nee | Nee | Nee | Ja |
|changeFeedPolicy | Door de gebruiker configureerbaar | Wordt gebruikt om wijzigingen in items in een container te lezen. Zie Feed [wijzigen voor](change-feed.md)meer informatie. | Ja | Nee | Nee | Nee | Ja |
|uniqueKeyPolicy | Door de gebruiker configureerbaar | Wordt gebruikt om de uniciteit van een of meer waarden in een logische partitie te garanderen. Zie [Unieke belangrijke beperkingen voor](unique-keys.md)meer informatie . | Ja | Nee | Nee | Nee | Ja |

### <a name="operations-on-an-azure-cosmos-container"></a>Bewerkingen op een Azure Cosmos-container

Een Azure Cosmos-container ondersteunt de volgende bewerkingen wanneer u een van de Azure Cosmos-API's gebruikt:

| Bewerking | Azure-CLI | SQL-API | Cassandra-API | Azure Cosmos DB-API voor MongoDB | Gremlin-API | Tabel-API |
| --- | --- | --- | --- | --- | --- | --- |
| Containers opsommen in een database | Ja | Ja | Ja | Ja | N.v.t. | N.v.t. |
| Een container lezen | Ja | Ja | Ja | Ja | N.v.t. | N.v.t. |
| Een nieuwe container maken | Ja | Ja | Ja | Ja | N.v.t. | N.v.t. |
| Een container bijwerken | Ja | Ja | Ja | Ja | N.v.t. | N.v.t. |
| Een container verwijderen | Ja | Ja | Ja | Ja | N.v.t. | N.v.t. |

## <a name="azure-cosmos-items"></a>Azure Cosmos-items

Afhankelijk van de API die u gebruikt, kan een Azure Cosmos-item een document in een verzameling, een rij in een tabel of een knooppunt of rand in een grafiek weergeven. In de volgende tabel wordt de toewijzing van API-specifieke entiteiten aan een Azure Cosmos-item weergegeven:

| Cosmos entiteit | SQL-API | Cassandra-API | Azure Cosmos DB-API voor MongoDB | Gremlin-API | Tabel-API |
| --- | --- | --- | --- | --- | --- |
|Azure Cosmos-item | Document | Rij | Document | Knooppunt of rand | Item |

### <a name="properties-of-an-item"></a>Eigenschappen van een item

Elk Azure Cosmos-item heeft de volgende systeemgedefinieerde eigenschappen. Afhankelijk van welke API u gebruikt, worden sommige mogelijk niet direct blootgesteld.

| Systeemgedefinieerde eigenschap | Door het systeem gegenereerd of door de gebruiker configureerbaar| Doel | SQL-API | Cassandra-API | Azure Cosmos DB-API voor MongoDB | Gremlin-API | Tabel-API |
| --- | --- | --- | --- | --- | --- | --- | --- |
|\_rid | Door het systeem gegenereerde systemen | Unieke id van het item | Ja | Nee | Nee | Nee | Nee |
|\_etag etag | Door het systeem gegenereerde systemen | Entiteitstag gebruikt voor optimistische gelijktijdigheidscontrole | Ja | Nee | Nee | Nee | Nee |
|\_Ts | Door het systeem gegenereerde systemen | Tijdstempel van de laatste update van het item | Ja | Nee | Nee | Nee | Nee |
|\_Zelf | Door het systeem gegenereerde systemen | Adresseerbare URI van het item | Ja | Nee | Nee | Nee | Nee |
|id | Ofwel | Door de gebruiker gedefinieerde unieke naam in een logische partitie. | Ja | Ja | Ja | Ja | Ja |
|Willekeurige door de gebruiker gedefinieerde eigenschappen | Door de gebruiker gedefinieerde routes | Door de gebruiker gedefinieerde eigenschappen weergegeven in API-native representatie (inclusief JSON, BSON en CQL) | Ja | Ja | Ja | Ja | Ja |

> [!NOTE]
> Uniciteit van `id` de eigenschap wordt alleen afgedwongen binnen elke logische partitie. Meerdere documenten kunnen `id` dezelfde eigenschap hebben met verschillende partitiesleutelwaarden.

### <a name="operations-on-items"></a>Bewerkingen op artikelen

Azure Cosmos-items ondersteunen de volgende bewerkingen. U een van de Azure Cosmos API's gebruiken om de bewerkingen uit te voeren.

| Bewerking | Azure-CLI | SQL-API | Cassandra-API | Azure Cosmos DB-API voor MongoDB | Gremlin-API | Tabel-API |
| --- | --- | --- | --- | --- | --- | --- |
| Invoegen, vervangen, verwijderen, upsert, lezen | Nee | Ja | Ja | Ja | Ja | Ja |

## <a name="next-steps"></a>Volgende stappen

Meer informatie over deze taken en concepten:

* [Doorvoer inrichten voor een Azure Cosmos-database](how-to-provision-database-throughput.md)
* [Doorvoer inrichten voor een Azure Cosmos-container](how-to-provision-container-throughput.md)
* [Werken met logische partities](partition-data.md)
* [TTL configureren op een Azure Cosmos-container](how-to-time-to-live.md)
* [Reactieve toepassingen opnieuw maken met behulp van wijzigingsfeed](change-feed.md)
* [Een unieke sleutelbeperking configureren voor uw Azure Cosmos-container](unique-keys.md)
