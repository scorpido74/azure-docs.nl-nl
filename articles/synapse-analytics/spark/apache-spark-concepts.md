---
title: Basisconcepten van Apache Spark
description: Dit artikel bevat een inleiding tot Apache Spark in Azure Synapse Analytics en de verschillende concepten.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: spark
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: 806f4dff49e9650dba073721109e7d54a18ecbbe
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87052335"
---
# <a name="apache-spark-in-azure-synapse-analytics-core-concepts"></a>Basisconcepten van Apache Spark in Azure Synapse Analytics

Apache Spark is een framework voor parallelle verwerking dat ondersteuning biedt voor in-memory verwerking om de prestaties van toepassingen voor de analyse van big data te verbeteren. Apache Spark in Azure Synapse Analytics is een van de implementaties van Apache Spark van Microsoft in de cloud. 

Met Azure Synapse kunt u eenvoudig Spark-functies maken en configureren in Azure. Azure Synapse biedt een andere implementatie van de Spark-functies die hier worden beschreven.

## <a name="spark-pools-preview"></a>Spark-pools (preview)

Er wordt een Spark-pool (preview) gemaakt in de Azure-portal. Een Spark-pool wordt, wanneer deze wordt geïnstantieerd, standaard gebruikt voor het maken van een Spark-exemplaar dat gegevens verwerkt. Wanneer er een Spark-pool wordt gemaakt, bestaat deze alleen als een set metagegevens; er worden geen resources verbruikt, uitgevoerd of in rekening gebracht. Een Spark-pool bevat een reeks eigenschappen die de kenmerken van een Spark-exemplaar bepalen. Deze kenmerken omvatten, maar zijn niet beperkt tot, naam, grootte, schaalgedrag en time to live.

Omdat er geen resourcekosten zijn verbonden aan het maken van Spark-pools, kunt u een onbeperkt aantal met verschillende configuraties maken. Er kunnen ook machtigingen op Spark-pools worden toegepast, zodat gebruikers alleen toegang hebben tot sommige pools.

U kunt het beste kleinere Spark-pools maken die kunnen worden gebruikt voor ontwikkeling en foutopsporing en vervolgens grotere maken voor het uitvoeren van productieworkloads.

In [Get started with Spark pools in Synapse Analytics](../quickstart-create-apache-spark-pool-portal.md) (Aan de slag met Spark-pools in Synapse Analytics) kunt u lezen hoe u een Spark-pool maakt en alle bijbehorende eigenschappen bekijkt

## <a name="spark-instances"></a>Spark-exemplaren

Spark-exemplaren worden gemaakt wanneer u verbinding maakt met een Spark-pool, een sessie maakt of een taak uitvoert. Aangezien meerdere gebruikers toegang tot één Spark-pool kunnen hebben, wordt er een nieuw Spark-exemplaar gemaakt voor elke gebruiker die ermee verbinding maakt. 

Wanneer u een tweede taak indient en er geen capaciteit in de pool is, heeft het bestaande Spark-exemplaar ook capaciteit en wordt de taak door het bestaande exemplaar verwerkt. Als dat niet het geval is en er wel capaciteit op het pool-niveau is, wordt er een nieuw Spark-exemplaar gemaakt.

## <a name="examples"></a>Voorbeelden

### <a name="example-1"></a>Voorbeeld 1

- U maakt een Spark-pool met de naam SP1; deze heeft een vaste clustergrootte van 20 knooppunten.
- U verzendt een notebooktaak, J1, die gebruikmaakt van 10 knooppunten. Een Spark-exemplaar, SI1, wordt gemaakt om de taak te verwerken.
- U verzendt nu een andere taak, J2, die 10 knooppunten gebruikt omdat er nog capaciteit in de pool is. Het exemplaar, J2, wordt verwerkt door SI1.
- Als J2 11 knooppunten nodig had gehad, was er geen capaciteit in SP1 of SI1 geweest. Als J2 in dat geval afkomstig is van een notebook, wordt de taak geweigerd. Als J2 afkomstig is van een batch-taak, wordt deze in de wachtrij geplaatst.

### <a name="example-2"></a>Voorbeeld 2

- U maakt een Spark-poolaanroep SP2. Deze heeft automatische schaalaanpassing voor 10 tot 20 knooppunten
- U verzendt een notebooktaak, J1, die gebruikmaakt van 10 knooppunten. Een Spark-exemplaar, SI1, wordt gemaakt om de taak te verwerken.
- U verzendt nu een andere taak, J2, die 10 knooppunten gebruikt. Omdat er nog capaciteit in de pool is, wordt het exemplaar automatisch uitgebreid naar 20 knooppunten en verwerkt het J2.

### <a name="example-3"></a>Voorbeeld 3

- U maakt een Spark-pool met de naam SP1; deze heeft een vaste clustergrootte van 20 knooppunten.
- U verzendt een notebooktaak, J1, die gebruikmaakt van 10 knooppunten. Een Spark-exemplaar, SI1, wordt gemaakt om de taak te verwerken.
- Een andere gebruiker, U2, verzendt een taak, J3, die gebruikmaakt van 10 knooppunten. Een nieuw Spark-exemplaar, SI2, wordt gemaakt om de taak te verwerken.
- U verzendt nu een andere taak, J2, die 10 knooppunten gebruikt. Omdat er nog capaciteit in de pool en het exemplaar is, wordt J2 verwerkt door SI1.

## <a name="next-steps"></a>Volgende stappen

- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
- [Documentatie voor Apache Spark](https://spark.apache.org/docs/2.4.4/)
