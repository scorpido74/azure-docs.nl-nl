---
title: Consistentieniveaus en Azure Cosmos DB-API's
description: Meer informatie over de consistentie niveau toewijzing tussen verschillende Api's in Azure Cosmos DB en Apache Cassandra, MongoDB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/23/2020
ms.reviewer: sngun
ms.openlocfilehash: 2398e95d9a119fe24c97f3887d16aa5b86c6ac76
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85119304"
---
# <a name="consistency-levels-and-azure-cosmos-db-apis"></a>Consistentieniveaus en Azure Cosmos DB-API's

Azure Cosmos DB biedt systeem eigen ondersteuning voor Api's die compatibel zijn met wire-protocol voor populaire data bases. Dit zijn onder andere MongoDB, Apache Cassandra, Gremlin en Azure Table Storage. Deze data bases bieden niet nauw keurig gedefinieerde consistentie modellen of garanties met SLA-ondersteuning voor de consistentie niveaus. Ze bieden doorgaans slechts een subset van de vijf consistentie modellen die worden aangeboden door Azure Cosmos DB. 

Wanneer u SQL API, Gremlin API en Table-API gebruikt, wordt het standaard consistentie niveau gebruikt dat is geconfigureerd voor het Azure Cosmos-account. 

Bij het gebruik van Cassandra-API-of Azure Cosmos DB-API voor MongoDB, krijgen toepassingen een volledige set consistentie niveaus die worden aangeboden door Apache Cassandra en MongoDB, met nog sterker consistentie-en duurzaamheids garanties. In dit document worden de bijbehorende Azure Cosmos DB consistentie niveaus voor Apache Cassandra-en MongoDB-consistentie niveaus weer gegeven.

## <a name="mapping-between-apache-cassandra-and-azure-cosmos-db-consistency-levels"></a><a id="cassandra-mapping"></a>Toewijzing tussen Apache Cassandra-en Azure Cosmos DB-consistentie niveaus

In tegens telling tot Azure Cosmos DB biedt Apache Cassandra geen systeem eigen nauw keurige consistentie garanties.  In plaats daarvan biedt Apache Cassandra een schrijf consistentie niveau en een consistentie niveau voor lezen om de hoge Beschik baarheid, consistentie en latentie-afwegingen mogelijk te maken. Bij het gebruik van de Cassandra-API van Azure Cosmos DB: 

* Het schrijf consistentie niveau van Apache Cassandra wordt toegewezen aan het standaard consistentie niveau dat is geconfigureerd voor uw Azure Cosmos-account. Consistentie voor een schrijf bewerking (LC) kan niet per aanvraag worden gewijzigd.

* Azure Cosmos DB wijst het Lees consistentie niveau dat is opgegeven door het Cassandra-client stuur programma dynamisch toe aan een van de Azure Cosmos DB consistentie niveaus die dynamisch zijn geconfigureerd op een lees aanvraag. 

In de volgende tabel ziet u hoe de systeem eigen Cassandra consistentie niveaus worden toegewezen aan de consistentie niveaus van de Azure Cosmos DB wanneer u Cassandra-API gebruikt:  

:::image type="content" source="./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png" alt-text="Toewijzing van Cassandra-consistentie model" lightbox="./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png" :::

## <a name="mapping-between-mongodb-and-azure-cosmos-db-consistency-levels"></a><a id="mongo-mapping"></a>Toewijzing tussen MongoDB en Azure Cosmos DB consistentie niveaus

In tegens telling tot Azure Cosmos DB biedt de systeem eigen MongoDB niet precies gedefinieerde consistentie garanties. In plaats daarvan kunnen gebruikers met systeem eigen MongoDB de volgende consistentie garanties configureren: een schrijf probleem, een lees probleem en de isMaster-instructie-om de Lees bewerkingen naar primaire of secundaire replica's te sturen om het gewenste consistentie niveau te krijgen. 

Wanneer u de API van Azure Cosmos DB gebruikt voor MongoDB, behandelt het MongoDB-stuur programma uw schrijf regio als de primaire replica en worden alle andere regio's een replica lezen. U kunt kiezen welke regio is gekoppeld aan uw Azure Cosmos-account als primaire replica. 

Tijdens het gebruik van de API van Azure Cosmos DB voor MongoDB:

* De schrijf bezorgdheid wordt toegewezen aan het standaard consistentie niveau dat is geconfigureerd voor uw Azure Cosmos-account.
 
* Azure Cosmos DB wijst de Lees bezorgdheid die door het MongoDB-client stuur programma is opgegeven, dynamisch toe aan een van de Azure Cosmos DB consistentie niveaus die dynamisch zijn geconfigureerd voor een lees aanvraag.  

* U kunt aantekeningen toevoegen aan een specifieke regio die is gekoppeld aan uw Azure Cosmos-account als ' Master ' door de regio als de eerste Beschrijf bare regio te maken. 

In de volgende tabel ziet u hoe de systeem eigen MongoDB-schrijf-en lees problemen worden toegewezen aan de consistentie niveaus van Azure Cosmos wanneer u de API van Azure Cosmos DB gebruikt voor MongoDB:

:::image type="content" source="./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png" alt-text="Toewijzing van MongoDB-consistentie model" lightbox= "./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png":::

## <a name="next-steps"></a>Volgende stappen

Lees meer over consistentie niveaus en compatibiliteit tussen Azure Cosmos DB Api's met de open source-Api's. Zie de volgende artikelen:

* [Beschik baarheid en prestaties voor diverse consistentie niveaus](consistency-levels-tradeoffs.md)
* [MongoDB-functies die worden ondersteund door de API van de Azure Cosmos DB voor MongoDB](mongodb-feature-support.md)
* [Apache Cassandra-functies die worden ondersteund door de Azure Cosmos DB Cassandra-API](cassandra-support.md)
