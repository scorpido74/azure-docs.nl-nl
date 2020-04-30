---
title: Apache Spark in azure Synapse Analytics-basis concepten
description: Dit artikel bevat een inleiding tot Apache Spark in azure Synapse Analytics en de verschillende concepten.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: b1db306ffdb1c05c880e5fc639de2cc1db130d8e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82096279"
---
# <a name="apache-spark-in-azure-synapse-analytics-core-concepts"></a>Apache Spark in azure Synapse Analytics core-concepten

Apache Spark is een framework voor parallelle verwerking dat ondersteuning biedt voor in-memory verwerking om de prestaties van toepassingen voor de analyse van big data te verbeteren. Apache Spark in azure Synapse Analytics is een van de implementaties van Apache Spark van micro soft in de Cloud. 

Met Azure Synapse kunt u eenvoudig Spark-mogelijkheden maken en configureren in Azure. Azure Synapse biedt een andere implementatie van deze Spark-mogelijkheden die hier worden beschreven.

## <a name="spark-pools-preview"></a>Spark-Pools (preview-versie)

Er wordt een Spark-groep (preview-versie) gemaakt in de Azure Portal. Het is de definitie van een Spark-groep die, wanneer geïnstantieerd, wordt gebruikt voor het maken van een Spark-instantie waarmee gegevens worden verwerkt. Wanneer een Spark-groep wordt gemaakt, bestaat deze alleen als meta gegevens; Er worden geen resources verbruikt, uitgevoerd of in rekening gebracht voor. Een Spark-groep bevat een reeks eigenschappen die de kenmerken van een Spark-exemplaar bepalen; deze kenmerken omvatten, maar zijn niet beperkt tot naam, grootte, schaal gedrag, time to Live.

Omdat er geen dollar of resource kosten zijn gekoppeld aan het maken van Spark-Pools, kan elk gewenst nummer worden gemaakt met een wille keurig aantal verschillende configuraties. Machtigingen kunnen ook worden toegepast op Spark-Pools zodat gebruikers alleen toegang hebben tot sommige en geen andere.

Een best practice is het maken van kleinere Spark-groepen die kunnen worden gebruikt voor ontwikkeling en fout opsporing en vervolgens groter worden voor het uitvoeren van productie werkbelastingen.

U kunt lezen hoe u een Spark-pool maakt en alle bijbehorende eigenschappen hier [aan de slag gaat met Spark-Pools in Synapse Analytics](../quickstart-create-apache-spark-pool.md)

## <a name="spark-instances"></a>Spark-instanties

Spark-instanties worden gemaakt wanneer u verbinding maakt met een Spark-pool, een sessie maakt en een taak uitvoert. Aangezien meerdere gebruikers toegang tot één Spark-groep hebben, wordt er een nieuw Spark-exemplaar gemaakt voor elke gebruiker die verbinding maakt. 

Wanneer u een tweede taak indient en er capaciteit is in de pool, heeft de bestaande Spark-instantie ook capaciteit. vervolgens wordt de taak door het bestaande exemplaar verwerkt. Als dat niet het geval is en er sprake is van capaciteit op het niveau van de groep, wordt er een nieuw Spark-exemplaar gemaakt.

## <a name="examples"></a>Voorbeelden

### <a name="example-1"></a>Voorbeeld 1

- U maakt een Spark-pool met de naam SP1; het heeft een vaste cluster grootte van 20 knoop punten.
- U verzendt een notitieblok taak, J1 die gebruikmaakt van tien knoop punten, een Spark-instantie, SI1 wordt gemaakt om de taak te verwerken.
- U verzendt nu een andere taak, j2, die 10 knoop punten gebruikt omdat er nog capaciteit in de groep en het exemplaar, de J2, wordt verwerkt door SI1.
- Als j2 voor 11 knoop punten werd gevraagd, is er geen capaciteit in SP1 of SI1. Als j2 afkomstig is uit een notitie blok, wordt de taak geweigerd. Als j2 afkomstig is van een batch-taak, wordt deze in de wachtrij geplaatst.

### <a name="example-2"></a>Voorbeeld 2

- U maakt een Spark-pool aanroep SP2; Er zijn 10 – 20 knoop punten automatisch schalen ingeschakeld
- U verzendt een notitieblok taak, J1 die gebruikmaakt van tien knoop punten, een Spark-instantie, SI1, wordt gemaakt om de taak te verwerken.
- U verzendt nu een andere taak, j2, die tien knoop punten gebruikt, omdat de groep nog steeds de capaciteit heeft die automatisch wordt uitgebreid tot 20 knoop punten en processen j2.

### <a name="example-3"></a>Voorbeeld 3

- U maakt een Spark-pool met de naam SP1; het heeft een vaste cluster grootte van 20 knoop punten.
- U verzendt een notitieblok taak, J1 die gebruikmaakt van tien knoop punten, een Spark-instantie, SI1 wordt gemaakt om de taak te verwerken.
- Een andere gebruiker, U2, verzendt een taak, J3, die gebruikmaakt van tien knoop punten, een nieuwe Spark-instantie, SI2, wordt gemaakt om de taak te verwerken.
- U verzendt nu een andere taak, j2, die 10 knoop punten gebruikt omdat er nog capaciteit in de groep en het exemplaar j2 is verwerkt door SI1.

## <a name="next-steps"></a>Volgende stappen

- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
- [Documentatie over Apache Spark](https://spark.apache.org/docs/2.4.4/)
