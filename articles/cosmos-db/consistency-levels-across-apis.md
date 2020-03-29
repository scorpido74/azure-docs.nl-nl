---
title: Consistentieniveaus en Azure Cosmos DB-API's
description: Inzicht in de consistentie niveau mapping tussen verschillende API's in Azure Cosmos DB en Apache Cassandra, MongoDB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.reviewer: sngun
ms.openlocfilehash: ef7d032d37105549ff7b05f85b953cd420954602
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131457"
---
# <a name="consistency-levels-and-azure-cosmos-db-apis"></a>Consistentieniveaus en Azure Cosmos DB-API's

Azure Cosmos DB biedt native ondersteuning voor API's die compatibel zijn met het protocol voor populaire databases. Deze omvatten MongoDB, Apache Cassandra, Gremlin en Azure Table-opslag. Deze databases bieden geen nauwkeurig gedefinieerde consistentiemodellen of SLA-backed garanties voor de consistentieniveaus. Ze bieden meestal slechts een subset van de vijf consistentiemodellen die worden aangeboden door Azure Cosmos DB. 

Bij het gebruik van SQL API, Gremlin API en Table API wordt het standaardconsistentieniveau gebruikt dat is geconfigureerd voor het Azure Cosmos-account. 

Bij het gebruik van Cassandra API of Azure Cosmos DB's API voor MongoDB, toepassingen krijgen een volledige set van consistentie niveaus aangeboden door Apache Cassandra en MongoDB, respectievelijk, met nog sterkere consistentie en duurzaamheid garanties. Dit document toont de bijbehorende Azure Cosmos DB-consistentieniveaus voor apache Cassandra- en MongoDB-consistentieniveaus.

## <a name="mapping-between-apache-cassandra-and-azure-cosmos-db-consistency-levels"></a><a id="cassandra-mapping"></a>Toewijzing tussen consistentieniveaus apache Cassandra en Azure Cosmos DB

In tegenstelling tot Azure Cosmos DB biedt Apache Cassandra niet in eigen land nauwkeurig gedefinieerde consistentiegaranties.  In plaats daarvan biedt Apache Cassandra een schrijfconsistentieniveau en een leesconsistentieniveau, om de hoge beschikbaarheid, consistentie en latentie-afwegingen mogelijk te maken. Bij het gebruik van de Cassandra API van Azure Cosmos DB: 

* Het schrijfconsistentieniveau van Apache Cassandra is toegewezen aan het standaardconsistentieniveau dat is geconfigureerd op uw Azure Cosmos-account. Consistentie voor een schrijfbewerking (CL) kan niet per aanvraag worden gewijzigd.

* Azure Cosmos DB brengt het leesconsistentieniveau dat door het cassandra-clientstuurprogramma is opgegeven dynamisch in kaart op een van de consistentieniveaus van Azure Cosmos DB die dynamisch zijn geconfigureerd op een leesaanvraag. 

In de volgende tabel ziet u hoe de oorspronkelijke consistentieniveaus van Cassandra worden toegewezen aan de consistentieniveaus van de Azure Cosmos DB wanneer u de Cassandra API gebruikt:  

[![Cassandra consistency model mapping Cassandra](./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png)](./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png#lightbox)

## <a name="mapping-between-mongodb-and-azure-cosmos-db-consistency-levels"></a><a id="mongo-mapping"></a>Toewijzing tussen mongoDB- en Azure Cosmos DB-consistentieniveaus

In tegenstelling tot Azure Cosmos DB biedt de native MongoDB niet precies gedefinieerde consistentiegaranties. In plaats daarvan stelt native MongoDB gebruikers in staat om de volgende consistentiegaranties te configureren: een schrijfprobleem, een leeszorg en de isMaster-richtlijn - om de leesbewerkingen te richten op primaire of secundaire replica's om het gewenste consistentieniveau te bereiken. 

Wanneer u de API van Azure Cosmos DB voor MongoDB gebruikt, behandelt het MongoDB-stuurprogramma uw schrijfregio als de primaire replica en worden alle andere regio's replica gelezen. U kiezen welke regio is gekoppeld aan uw Azure Cosmos-account als primaire replica. 

Tijdens het gebruik van de API van Azure Cosmos DB voor MongoDB:

* Het schrijfprobleem is toegewezen aan het standaardconsistentieniveau dat is geconfigureerd op uw Azure Cosmos-account.
 
* Azure Cosmos DB brengt het leesprobleem dat door het MongoDB-clientstuurprogramma is opgegeven dynamisch in kaart op een van de consistentieniveaus van Azure Cosmos DB die dynamisch is geconfigureerd op een leesaanvraag.  

* U een specifieke regio die is gekoppeld aan uw Azure Cosmos-account annoteren als 'Master' door de regio als de eerste schrijfbare regio te maken. 

In de volgende tabel ziet u hoe de oorspronkelijke MongoDB-schrijf-/leesproblemen worden toegewezen aan de consistentieniveaus van Azure Cosmos wanneer de API van Azure Cosmos DB voor MongoDB wordt gebruikt:

[![MongoDB consistentiemodeltoewijzing](./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png)](./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png#lightbox)

## <a name="next-steps"></a>Volgende stappen

Lees meer over consistentieniveaus en compatibiliteit tussen Azure Cosmos DB API's met de open-source API's. Zie de volgende artikelen:

* [Beschikbaarheid en prestatieafwegingen voor verschillende consistentieniveaus](consistency-levels-tradeoffs.md)
* [MongoDB-functies die worden ondersteund door de API van azure cosmos DB voor MongoDB](mongodb-feature-support.md)
* [Apache Cassandra-functies worden ondersteund door de Azure Cosmos DB Cassandra API](cassandra-support.md)
