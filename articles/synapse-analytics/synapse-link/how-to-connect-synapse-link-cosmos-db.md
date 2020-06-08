---
title: Verbinding maken met Azure Synapse Link (preview) voor Azure Cosmos DB
description: Een Azure Cosmos-database verbinden met een Synapse-werkruimte met behulp van Azure Synapse Link
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/21/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: 6bdc2eca3bdf02814ee851ff266e04bb57f7978c
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83658820"
---
# <a name="connect-to-azure-synapse-link-preview-for-azure-cosmos-db"></a>Verbinding maken met Azure Synapse Link (preview) voor Azure Cosmos DB

In dit artikel wordt beschreven hoe u toegang krijgt tot een Azure Cosmos DB-database vanuit Azure Synapse Analytics Studio met behulp van Azure Synapse Link.

## <a name="prerequisites"></a>Vereisten

Voordat u een Azure Cosmos DB-database verbindt met uw werkruimte, hebt u het volgende nodig:

* Een bestaande Azure Cosmos DB-database, of u kunt een nieuw account maken door deze [quickstart](https://docs.microsoft.com/azure/cosmos-db/how-to-manage-database-account) te volgen
* Een bestaande Synapse-werkruimte , of u kunt een nieuwe werkruimte maken door deze [quickstart](https://docs.microsoft.com/azure/synapse-analytics/quickstart-create-workspace) te volgen 

## <a name="enable-azure-cosmos-db-analytical-store"></a>Analytische opslag van Azure Cosmos DB inschakelen

Als u grootschalige analyses wilt uitvoeren in Azure Cosmos DB zonder de prestaties van bewerkingen te beïnvloeden, wordt u aangeraden om Synapse Link voor Azure Cosmos DB in te schakelen. Synapse Link brengt HTAP-mogelijkheden naar een container en biedt ingebouwde ondersteuning in Azure Synapse.

## <a name="navigate-to-synapse-studio"></a>Ga naar Synapse Studio

Selecteer **Synapse Studio starten** vanuit uw Synapse-werkruimte. Selecteer op de startpagina van Synapse Studio de optie **Gegevens. U wordt nu naar **Data Object Explorer** geleid.

## <a name="connect-an-azure-cosmos-db-database-to-a-synapse-workspace"></a>Een Azure Cosmos DB-database verbinden met een Synapse-werkruimte

Het verbinden van een Azure Cosmos DB-database wordt uitgevoerd als een gekoppelde service. Met een gekoppelde service van Azure Cosmos DB kunnen gebruikers bladeren in gegevens en deze verkennen, en lees- en schrijfbewerkingen vanuit Apache Spark for Azure Synapse Analytics of SQL uitvoeren in Azure Cosmos DB.

Vanuit Data Object Explorer kunt u rechtstreeks verbinding maken met een Azure Cosmos DB-database door de volgende stappen uit te voeren:

1. Selecteer het pictogram ***+*** bij de optie Gegevens
2. Selecteer **Verbinding maken met externe gegevens**
3. Selecteer de API waarmee u verbinding wilt maken: SQL API of API for MongoDB
4. Selecteer ***Doorgaan***
5. Geef de gekoppelde service een naam. De naam wordt weergegeven in Object Explorer en wordt tijdens Synapse-uitvoeringen gebruikt om verbinding te maken met de database en containers. We raden u aan een beschrijvende naam te gebruiken.
6. Selecteer de **Azure Cosmos DB-accountnaam** en **databasenaam**
7. Als er geen regio is opgegeven, worden Synapse-uitvoeringen gerouteerd naar de dichtstbijzijnde regio waar de analytische opslag is ingeschakeld (optioneel). U kunt echter ook handmatig instellen in welke regio u wilt dat gebruikers toegang krijgen tot de analytische opslag van Azure Cosmos DB. Selecteer **Aanvullende verbindingseigenschappen** en vervolgens **Nieuw**. Schrijf ***PreferredRegions*** onder **Naam van eigenschap** en stel de **waarde** in op de gewenste regio (voorbeeld: WestUS2, zonder spatie tussen woorden en getal)
8. Selecteer ***Maken***

Azure Cosmos DB-databases worden weergegeven op het tabblad **Gekoppeld** in de sectie Azure Cosmos DB. Met Azure Cosmos DB kunt u een container met HTAP onderscheiden van een alleen-OLTP-container, met behulp van de volgende pictogrammen:

**Alleen-OLTP-container**:

![OLTP-container](../media/quickstart-connect-synapse-link-cosmosdb/oltp-container.png)

**Container met HTAP**:

![HTAP-container](../media/quickstart-connect-synapse-link-cosmosdb/htap-container.png)

## <a name="quickly-interact-with-code-generated-actions"></a>Snel communiceren via met code gegenereerde acties

Als u met de rechtermuisknop op een container klikt, ziet u een lijst met gebaren die een Spark- of SQL-uitvoering activeren. Schrijven naar een container gebeurt via de transactionele opslag van Azure Cosmos DB, en hierbij worden aanvraageenheden verbruikt.  

## <a name="next-steps"></a>Volgende stappen

* [Krijg inzicht in wat wordt ondersteund tussen Synapse en Azure Cosmos DB](./concept-synapse-link-cosmos-db-support.md)
* [Leer hoe u de analytische opslag doorzoekt met Spark](./how-to-query-analytical-store-spark.md)