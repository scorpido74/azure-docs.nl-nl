---
title: Apache Spark in Azure Synapse Analytics - Kernconcepten
description: In dit artikel vindt u een inleiding tot Apache Spark in Azure Synapse Analytics en de verschillende concepten.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: 3cf654e77bf68c5194a0213d4452242b5c44e234
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81423668"
---
# <a name="apache-spark-in-azure-synapse-analytics-core-concepts"></a>Apache Spark in Azure Synapse Analytics Kernconcepten

Apache Spark is een framework voor parallelle verwerking dat ondersteuning biedt voor in-memory verwerking om de prestaties van toepassingen voor de analyse van big data te verbeteren. Apache Spark in Azure Synapse Analytics is een van de implementaties van Apache Spark door Microsoft in de cloud. 

Azure Synapse maakt het eenvoudig om Spark-mogelijkheden in Azure te maken en te configureren. Azure Synapse biedt een andere implementatie van deze Spark-mogelijkheden die hier zijn gedocumenteerd.

## <a name="spark-pools-preview"></a>Spark-pools (voorbeeld)

Er wordt een Spark-pool (voorbeeld) gemaakt in de Azure-portal. Het is de definitie van een Spark-pool die, wanneer deze wordt geinstantd, wordt gebruikt om een Spark-instantie te maken die gegevens verwerkt. Wanneer een Spark-pool wordt gemaakt, bestaat deze alleen als metagegevens. er worden geen resources verbruikt, uitgevoerd of in rekening gebracht. Een Spark-pool heeft een reeks eigenschappen die de kenmerken van een Spark-instantie bepalen. deze kenmerken omvatten, maar zijn niet beperkt tot naam, grootte, schaalgedrag, tijd om te leven.

Omdat er geen dollar- of resourcekosten zijn gekoppeld aan het maken van Spark-pools, kan elk nummer worden gemaakt met een willekeurig aantal verschillende configuraties. Machtigingen kunnen ook worden toegepast op Spark-pools, zodat gebruikers alleen toegang hebben tot sommige en niet tot anderen.

Een aanbevolen toepassing is het maken van kleinere Spark-pools die kunnen worden gebruikt voor ontwikkeling en foutopsporing en vervolgens grotere voor het uitvoeren van productieworkloads.

U lezen hoe u een Spark-pool maakt en al hun eigenschappen hier bekijken [Aan de slag met Spark-pools in Synapse Analytics](apache-spark-notebook-create-spark-use-sql.md#create-an-apache-spark-pool)

## <a name="spark-instances"></a>Spark-exemplaren

Spark-exemplaren worden gemaakt wanneer u verbinding maakt met een Spark-pool, een sessie maakt en een taak uitvoert. Omdat meerdere gebruikers toegang hebben tot één Spark-groep, wordt er een nieuwe Spark-instantie gemaakt voor elke gebruiker die verbinding maakt. 

Wanneer u een tweede taak indient, dan als er capaciteit in de pool is, heeft de bestaande Spark-instantie ook capaciteit, dan zal de bestaande instantie de taak verwerken; zo niet en is er capaciteit op poolniveau, dan wordt er een nieuwe Spark-instantie gemaakt.

## <a name="examples"></a>Voorbeelden

### <a name="example-1"></a>Voorbeeld 1

- U maakt een Spark-pool met de naam SP1; het heeft een vaste clustergrootte van 20 knooppunten.
- U dient een notitiebloktaak in, J1 die 10 knooppunten gebruikt, een Spark-instantie, SI1 wordt gemaakt om de taak te verwerken.
- U verzendt nu een andere taak, J2, die 10 knooppunten gebruikt omdat er nog capaciteit in de groep is en de instantie, de J2, wordt verwerkt door SI1.
- Als J2 om 11 knooppunten had gevraagd, zou er geen capaciteit in SP1 of SI1 zijn geweest. In dit geval, als J2 uit een notitieboekje komt, dan zal de baan worden verworpen; als J2 afkomstig is van een batchtaak, wordt deze in de wachtrij geplaatst.

### <a name="example-2"></a>Voorbeeld 2

- U maakt een Spark-pooloproep SP2; het heeft een autoscale ingeschakeld 10 - 20 knooppunten
- U dient een notitiebloktaak in, J1 die 10 knooppunten gebruikt, een Spark-instantie, SI1, wordt gemaakt om de taak te verwerken.
- U dient nu een andere taak in, J2, die 10 knooppunten gebruikt, omdat er nog steeds capaciteit in de groep is, het instantieauto-exemplaar groeit naar 20 knooppunten en verwerkt J2.

### <a name="example-3"></a>Voorbeeld 3

- U maakt een Spark-pool met de naam SP1; het heeft een vaste clustergrootte van 20 knooppunten.
- U dient een notitiebloktaak in, J1 die 10 knooppunten gebruikt, een Spark-instantie, SI1 wordt gemaakt om de taak te verwerken.
- Een andere gebruiker, U2, dient een taak in, J3, die 10 knooppunten gebruikt, een nieuwe Spark-instantie, SI2, wordt gemaakt om de taak te verwerken.
- U dient nu een andere taak in, J2, die 10 knooppunten gebruikt omdat er nog capaciteit in de groep is en de instantie, J2, wordt verwerkt door SI1.

## <a name="next-steps"></a>Volgende stappen

- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
- [Documentatie apache vonk](https://spark.apache.org/docs/2.4.4/)
