---
title: Inzicht in de prijzen van Azure Data Factory aan de andere doormiddel van voorbeelden
description: In dit artikel wordt het prijsmodel azure data factory met gedetailleerde voorbeelden uitgelegd en gedemonstreerd
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/27/2019
ms.openlocfilehash: 9d96e3f7d127f4839592e766537cbdb07cc697dc
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414941"
---
# <a name="understanding-data-factory-pricing-through-examples"></a>Inzicht in de prijzen van Data Factory aan de andere doorgeven

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In dit artikel wordt het prijsmodel azure data factory met gedetailleerde voorbeelden uitgelegd en gedemonstreerd.

> [!NOTE]
> De prijzen die in deze onderstaande voorbeelden worden gebruikt, zijn hypothetisch en zijn niet bedoeld om de werkelijke prijzen te impliceren.

## <a name="copy-data-from-aws-s3-to-azure-blob-storage-hourly"></a>Gegevens van AWS S3 naar Azure Blob-opslag per uur kopiëren

In dit scenario wilt u gegevens van AWS S3 naar Azure Blob-opslag kopiëren volgens een uurschema.

Als u het scenario wilt uitvoeren, moet u een pijplijn maken met de volgende items:

1. Een kopieeractiviteit met een invoergegevensset voor de gegevens die moeten worden gekopieerd van AWS S3.

2. Een uitvoergegevensset voor de gegevens op Azure Storage.

3. Een schema trigger om de pijplijn elk uur uit te voeren.

   ![Scenario1](media/pricing-concepts/scenario1.png)

| **Bewerkingen** | **Typen en eenheden** |
| --- | --- |
| Gekoppelde service maken | 2 Entiteit Lezen/schrijven  |
| Gegevenssets maken | 4 Lees-/schrijfentiteiten (2 voor het maken van gegevenssets, 2 voor gekoppelde serviceverwijzingen) |
| Pijplijn maken | 3 Lees-/schrijfentiteiten (1 voor het maken van pijplijnen, 2 voor gegevenssetverwijzingen) |
| Pijplijn krijgen | 1 Entiteit Lezen/schrijven |
| Pijplijn uitvoeren | 2 Activiteitsuitvoeringen (1 voor triggerrun, 1 voor activiteitsuitvoeringen) |
| Copy Data Assumption: uitvoeringstijd = 10 min | 10 \* 4 Azure Integration Runtime (standaard DIU-instelling = 4) Voor meer informatie over gegevensintegratie-eenheden en het optimaliseren van kopieerprestaties, zie [dit artikel](copy-activity-performance.md) |
| Monitor Pipeline Veronderstelling: Slechts 1 run opgetreden | 2 Controle-runrecords opnieuw geprobeerd (1 voor pijplijnuitvoering, 1 voor activiteitsuitvoering) |

**Totale scenarioprijzen: $ 0,16811**

- Gegevensfabriekbewerkingen = **$0,0001**
  - Lezen/schrijven =\*10 00001 = $0,0001 [1 R/W = $0,50/50000 = 0,00001]
  - Monitoring =\*2 000005 = $0,00001 [1 Monitoring = $0,25/50000 = 0,000005]
- Uitvoering van &amp; pijplijnorkesten = **$0,168**
  - Activiteitsuitvoeringen =\*001 2 = 0,002 [1 run = $1/1000 = 0,001]
  - Activiteiten voor gegevensverkeer = $ 0,166 (Naar rato voor 10 minuten uitvoeringstijd. $0,25/uur op Azure Integration Runtime)

## <a name="copy-data-and-transform-with-azure-databricks-hourly"></a>Gegevens kopiëren en transformeren met Azure Databricks per uur

In dit scenario wilt u gegevens van AWS S3 naar Azure Blob-opslag kopiëren en de gegevens met Azure Databricks in een uurschema transformeren.

Als u het scenario wilt uitvoeren, moet u een pijplijn maken met de volgende items:

1. Eén kopieeractiviteit met een invoergegevensset voor de gegevens die moeten worden gekopieerd van AWS S3 en een uitvoergegevensset voor de gegevens over Azure-opslag.
2. Eén Azure Databricks-activiteit voor de gegevenstransformatie.
3. Een schema trigger om de pijplijn uit te voeren elk uur.

![Scenario2](media/pricing-concepts/scenario2.png)

| **Bewerkingen** | **Typen en eenheden** |
| --- | --- |
| Gekoppelde service maken | 3 Entiteit Lezen/schrijven  |
| Gegevenssets maken | 4 Lees-/schrijfentiteiten (2 voor het maken van gegevenssets, 2 voor gekoppelde serviceverwijzingen) |
| Pijplijn maken | 3 Lees-/schrijfentiteiten (1 voor het maken van pijplijnen, 2 voor gegevenssetverwijzingen) |
| Pijplijn krijgen | 1 Entiteit Lezen/schrijven |
| Pijplijn uitvoeren | 3 Activiteitsuitvoeringen (1 voor triggerrun, 2 voor activiteitsuitvoeringen) |
| Copy Data Assumption: uitvoeringstijd = 10 min | 10 \* 4 Azure Integration Runtime (standaard DIU-instelling = 4) Voor meer informatie over gegevensintegratie-eenheden en het optimaliseren van kopieerprestaties, zie [dit artikel](copy-activity-performance.md) |
| Monitor Pipeline Veronderstelling: Slechts 1 run opgetreden | 3 Monitoring run records opnieuw geprobeerd (1 voor pijplijn run, 2 voor activiteit uitvoeren) |
| Databricks activiteit Veronderstelling uitvoeren: uitvoeringstijd = 10 min | 10 min Externe pijplijnactiviteit uitvoering |

**Totale scenarioprijzen: $ 0,16916**

- Gegevensfabriekbewerkingen = **$0,00012**
  - Lezen/schrijven =\*11 00001 = $0,00011 [1 R/W = $0,50/50000 = 0,00001]
  - Monitoring =\*3 000005 = $0,00001 [1 Monitoring = $0,25/50000 = 0,000005]
- Uitvoering van &amp; pijplijnorkesten = **$0,16904**
  - Activiteitsuitvoeringen =\*001 3 = 0,003 [1 run = $1/1000 = 0,001]
  - Activiteiten voor gegevensverkeer = $ 0,166 (Naar rato voor 10 minuten uitvoeringstijd. $0,25/uur op Azure Integration Runtime)
  - Externe pijplijnactiviteit = $ 0,000041 (Naar rato van 10 minuten uitvoeringstijd. $ 0,00025/uur op Azure Integration Runtime)

## <a name="copy-data-and-transform-with-dynamic-parameters-hourly"></a>Gegevens kopiëren en transformeren met dynamische parameters per uur

In dit scenario wilt u gegevens van AWS S3 naar Azure Blob-opslag kopiëren en transformeren met Azure Databricks (met dynamische parameters in het script) volgens een uurschema.

Als u het scenario wilt uitvoeren, moet u een pijplijn maken met de volgende items:

1. Eén kopieeractiviteit met een invoergegevensset voor de gegevens die moeten worden gekopieerd uit AWS S3, een uitvoergegevensset voor de gegevens op Azure-opslag.
2. Eén opzoekactiviteit voor het dynamisch doorgeven van parameters aan het transformatiescript.
3. Eén Azure Databricks-activiteit voor de gegevenstransformatie.
4. Een schema trigger om de pijplijn uit te voeren elk uur.

![Scenario3](media/pricing-concepts/scenario3.png)

| **Bewerkingen** | **Typen en eenheden** |
| --- | --- |
| Gekoppelde service maken | 3 Entiteit Lezen/schrijven  |
| Gegevenssets maken | 4 Lees-/schrijfentiteiten (2 voor het maken van gegevenssets, 2 voor gekoppelde serviceverwijzingen) |
| Pijplijn maken | 3 Lees-/schrijfentiteiten (1 voor het maken van pijplijnen, 2 voor gegevenssetverwijzingen) |
| Pijplijn krijgen | 1 Entiteit Lezen/schrijven |
| Pijplijn uitvoeren | 4 Activiteitsuitvoeringen (1 voor triggerrun, 3 voor activiteitsuitvoeringen) |
| Copy Data Assumption: uitvoeringstijd = 10 min | 10 \* 4 Azure Integration Runtime (standaard DIU-instelling = 4) Voor meer informatie over gegevensintegratie-eenheden en het optimaliseren van kopieerprestaties, zie [dit artikel](copy-activity-performance.md) |
| Monitor Pipeline Veronderstelling: Slechts 1 run opgetreden | 4 Monitoring run records opnieuw geprobeerd (1 voor pijplijn run, 3 voor activiteit uitvoeren) |
| Opzoekactiviteit uitvoeren Veronderstelling: uitvoeringstijd = 1 min | 1 min Uitvoering pijplijnactiviteit |
| Databricks activiteit Veronderstelling uitvoeren: uitvoeringstijd = 10 min | 10 min externe pijplijnactiviteit uitvoering |

**Totale scenarioprijzen: $ 0,17020**

- Gegevensfabriekbewerkingen = **$0,00013**
  - Lezen/schrijven =\*11 00001 = $0,00011 [1 R/W = $0,50/50000 = 0,00001]
  - Monitoring =\*4 000005 = $0,00002 [1 Monitoring = $0,25/50000 = 0,000005]
- Uitvoering van &amp; pijplijnorkesten = **$0,17007**
  - Activiteitsuitvoeringen =\*001 4 = 0,004 [1 run = $1/1000 = 0,001]
  - Activiteiten voor gegevensverkeer = $ 0,166 (Naar rato voor 10 minuten uitvoeringstijd. $0,25/uur op Azure Integration Runtime)
  - Pijplijnactiviteit = $ 0,00003 (Naar rato van 1 minuut uitvoeringstijd. $ 0,002/uur op Azure Integration Runtime)
  - Externe pijplijnactiviteit = $ 0,000041 (Naar rato van 10 minuten uitvoeringstijd. $ 0,00025/uur op Azure Integration Runtime)

## <a name="using-mapping-data-flow-debug-for-a-normal-workday"></a>Foutopsporing van de gegevensstroom in kaart brengen voor een normale werkdag

Als Data Engineer ben je verantwoordelijk voor het ontwerpen, bouwen en testen van datastromen per dag. U meldt zich 's ochtends aan bij de ADF-gebruikersinterface en schakelt de foutopsporingsmodus in voor gegevensstromen. De standaard TTL voor Foutopsporingssessies is 60 minuten. U werkt de hele dag gedurende 8 uur, zodat uw Foutopsporingssessie nooit verloopt. Daarom zijn uw kosten voor de dag:

**8 (uur) x 8 (rekengeoptimaliseerde kernen) x $0.193 = $12.35**

## <a name="transform-data-in-blob-store-with-mapping-data-flows"></a>Gegevens transformeren in blob-archief met toewijzingsgegevensstromen

In dit scenario wilt u gegevens in Blob Store visueel omzetten in ADF-toewijzingsgegevensstromen op een uurschema.

Als u het scenario wilt uitvoeren, moet u een pijplijn maken met de volgende items:

1. Een activiteit Gegevensstroom met de transformatielogica.

2. Een invoergegevensset voor de gegevens op Azure Storage.

3. Een uitvoergegevensset voor de gegevens op Azure Storage.

4. Een schema trigger om de pijplijn elk uur uit te voeren.

| **Bewerkingen** | **Typen en eenheden** |
| --- | --- |
| Gekoppelde service maken | 2 Entiteit Lezen/schrijven  |
| Gegevenssets maken | 4 Lees-/schrijfentiteiten (2 voor het maken van gegevenssets, 2 voor gekoppelde serviceverwijzingen) |
| Pijplijn maken | 3 Lees-/schrijfentiteiten (1 voor het maken van pijplijnen, 2 voor gegevenssetverwijzingen) |
| Pijplijn krijgen | 1 Entiteit Lezen/schrijven |
| Pijplijn uitvoeren | 2 Activiteitsuitvoeringen (1 voor triggerrun, 1 voor activiteitsuitvoeringen) |
| Data Flow Veronderstellingen: uitvoeringstijd = 10 min + 10 min TTL | 10 \* 16 cores van General Compute met TTL van 10 |
| Monitor Pipeline Veronderstelling: Slechts 1 run opgetreden | 2 Controle-runrecords opnieuw geprobeerd (1 voor pijplijnuitvoering, 1 voor activiteitsuitvoering) |

**Totale scenarioprijzen: $ 1.4631**

- Gegevensfabriekbewerkingen = **$0,0001**
  - Lezen/schrijven =\*10 00001 = $0,0001 [1 R/W = $0,50/50000 = 0,00001]
  - Monitoring =\*2 000005 = $0,00001 [1 Monitoring = $0,25/50000 = 0,000005]
- Uitvoering van &amp; pijplijnorkesten = **$1.463**
  - Activiteitsuitvoeringen =\*001 2 = 0,002 [1 run = $1/1000 = 0,001]
  - Data Flow Activities = $1.461 prorated for 20 minutes (10 mins execution time + 10 mins TTL). $ 0,274 per uur op Azure Integration Runtime met 16 cores algemene rekenkracht

## <a name="next-steps"></a>Volgende stappen

Nu u de prijzen voor Azure Data Factory begrijpt, u aan de slag!

- [Een gegevensfabriek maken met de gebruikersinterface van Azure Data Factory](quickstart-create-data-factory-portal.md)

- [Inleiding tot Azure Data Factory](introduction.md)

- [Visueel ontwerpen in Azure Data Factory](author-visually.md)
