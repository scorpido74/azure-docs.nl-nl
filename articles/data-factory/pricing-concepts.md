---
title: Azure Data Factory prijzen door middel van voor beelden
description: In dit artikel wordt uitgelegd wat het Azure Data Factory-prijs model met gedetailleerde voor beelden bevat
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/27/2019
ms.openlocfilehash: ee5acc97e4b05a0e93f4ceee8c04b400da211b49
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2020
ms.locfileid: "76769500"
---
# <a name="understanding-data-factory-pricing-through-examples"></a>Data Factory prijzen door middel van voor beelden

In dit artikel wordt uitgelegd wat het Azure Data Factory-prijs model met gedetailleerde voor beelden bevat.

> [!NOTE]
> De prijzen die in deze voor beelden worden gebruikt, zijn hypothetisch en zijn niet bedoeld als werkelijke prijzen.

## <a name="copy-data-from-aws-s3-to-azure-blob-storage-hourly"></a>Gegevens vanaf elk uur kopiëren van AWS S3 naar Azure Blob Storage

In dit scenario wilt u gegevens van AWS S3 kopiëren naar Azure Blob-opslag op basis van een schema.

Als u het scenario wilt volt ooien, moet u een pijp lijn maken met de volgende items:

1. Een Kopieer activiteit met een invoer-gegevensset voor de gegevens die moeten worden gekopieerd uit AWS S3.

2. Een uitvoer gegevensset voor de gegevens op Azure Storage.

3. Een schema trigger om elk uur de pijp lijn uit te voeren.

   ![Scenario1](media/pricing-concepts/scenario1.png)

| **Bewerkingen** | **Typen en eenheden** |
| --- | --- |
| Een gekoppelde service maken | 2 entiteit voor lezen/schrijven  |
| Gegevens sets maken | 4 entiteiten voor lezen/schrijven (2 voor het maken van de gegevensset, 2 voor gekoppelde service verwijzingen) |
| Pijp lijn maken | 3 entiteiten voor lezen/schrijven (1 voor het maken van pijp lijnen, 2 voor verwijzingen naar gegevensset) |
| Pijp lijn ophalen | 1 entiteit lezen/schrijven |
| Pijp lijn uitvoeren | 2 uitvoeringen van activiteit (1 voor uitvoering van trigger, 1 voor uitvoeringen van activiteit) |
| Gegevens kopiëren veronderstelling: uitvoerings tijd = 10 minuten | 10 \* 4 Azure Integration Runtime (standaard instelling voor DIU = 4) Zie [dit artikel](copy-activity-performance.md) voor meer informatie over de gegevens integratie-eenheden en het optimaliseren van de Kopieer prestaties |
| Bewaak pijp lijn controleren: er is slechts 1 uitvoering uitgevoerd | 2 uitgevoerde pogingen voor het controleren van records (1 voor de pijplijn uitvoering, 1 voor de uitvoering van de activiteit) |

**Totaal prijs scenario: $0,16811**

- Data Factory bewerkingen = **$0,0001**
  - Lezen/schrijven = 10\*00001 = $0,0001 [1 R/W = $0,50/50.000 = 0,00001]
  - Bewaking = 2\*000005 = $0,00001 [1 bewaking = $0,25/50.000 = 0,000005]
- Pipeline-indeling &amp; Execution = **$0,168**
  - Uitvoeringen van activiteit = 001\*2 = 0,002 [1 run = $1/1000 = 0,001]
  - Activiteiten voor gegevens verplaatsing = $0,166 (in verhouding tot 10 minuten voor de uitvoerings tijd). $0,25/uur op Azure Integration Runtime)

## <a name="copy-data-and-transform-with-azure-databricks-hourly"></a>Gegevens en trans formatie met Azure Databricks per uur kopiëren

In dit scenario wilt u gegevens van AWS S3 kopiëren naar Azure Blob-opslag en de gegevens transformeren met Azure Databricks volgens een planning per uur.

Als u het scenario wilt volt ooien, moet u een pijp lijn maken met de volgende items:

1. Een Kopieer activiteit met een invoer-gegevensset voor de gegevens die moeten worden gekopieerd uit AWS S3 en een uitvoer gegevensset voor de gegevens in azure Storage.
2. Een Azure Databricks activiteit voor de gegevens transformatie.
3. Eén schema-trigger voor het uitvoeren van de pijp lijn elk uur.

![Scenario2](media/pricing-concepts/scenario2.png)

| **Bewerkingen** | **Typen en eenheden** |
| --- | --- |
| Een gekoppelde service maken | 3 entiteit voor lezen/schrijven  |
| Gegevens sets maken | 4 entiteiten voor lezen/schrijven (2 voor het maken van de gegevensset, 2 voor gekoppelde service verwijzingen) |
| Pijp lijn maken | 3 entiteiten voor lezen/schrijven (1 voor het maken van pijp lijnen, 2 voor verwijzingen naar gegevensset) |
| Pijp lijn ophalen | 1 entiteit lezen/schrijven |
| Pijp lijn uitvoeren | 3 uitvoeringen van activiteit (1 voor uitvoering van trigger, 2 voor uitvoeringen van activiteit) |
| Gegevens kopiëren veronderstelling: uitvoerings tijd = 10 minuten | 10 \* 4 Azure Integration Runtime (standaard instelling voor DIU = 4) Zie [dit artikel](copy-activity-performance.md) voor meer informatie over de gegevens integratie-eenheden en het optimaliseren van de Kopieer prestaties |
| Bewaak pijp lijn controleren: er is slechts 1 uitvoering uitgevoerd | 3 uitgevoerde uitvoerings records voor bewaking (1 voor pijplijn uitvoering, 2 voor uitvoering van activiteit) |
| Uitvoer van Databricks-activiteit uitvoeren: uitvoerings tijd = 10 minuten | 10 min. uitvoering van externe pijplijn activiteit |

**Totaal prijs scenario: $0,16916**

- Data Factory bewerkingen = **$0,00012**
  - Lezen/schrijven = 11\*00001 = $0,00011 [1 R/W = $0,50/50.000 = 0,00001]
  - Bewaking = 3\*000005 = $0,00001 [1 bewaking = $0,25/50.000 = 0,000005]
- Pipeline-indeling &amp; Execution = **$0,16904**
  - Uitvoeringen van activiteit = 001\*3 = 0,003 [1 run = $1/1000 = 0,001]
  - Activiteiten voor gegevens verplaatsing = $0,166 (in verhouding tot 10 minuten voor de uitvoerings tijd). $0,25/uur op Azure Integration Runtime)
  - Externe pijplijn activiteit = $0,000041 (in verhouding tot 10 minuten van uitvoerings tijd. $0.00025/uur op Azure Integration Runtime)

## <a name="copy-data-and-transform-with-dynamic-parameters-hourly"></a>Gegevens en trans formatie met dynamische para meters per uur kopiëren

In dit scenario wilt u gegevens van AWS S3 kopiëren naar Azure Blob-opslag en transformeren met Azure Databricks (met dynamische para meters in het script) volgens een planning per uur.

Als u het scenario wilt volt ooien, moet u een pijp lijn maken met de volgende items:

1. Een Kopieer activiteit met een invoer-gegevensset voor de gegevens die moeten worden gekopieerd uit AWS S3, een uitvoer gegevensset voor de gegevens in azure Storage.
2. Een opzoek activiteit voor het dynamisch door geven van para meters aan het transformatie script.
3. Een Azure Databricks activiteit voor de gegevens transformatie.
4. Eén schema-trigger voor het uitvoeren van de pijp lijn elk uur.

![Scenario3](media/pricing-concepts/scenario3.png)

| **Bewerkingen** | **Typen en eenheden** |
| --- | --- |
| Een gekoppelde service maken | 3 entiteit voor lezen/schrijven  |
| Gegevens sets maken | 4 entiteiten voor lezen/schrijven (2 voor het maken van de gegevensset, 2 voor gekoppelde service verwijzingen) |
| Pijp lijn maken | 3 entiteiten voor lezen/schrijven (1 voor het maken van pijp lijnen, 2 voor verwijzingen naar gegevensset) |
| Pijp lijn ophalen | 1 entiteit lezen/schrijven |
| Pijp lijn uitvoeren | 4 uitvoeringen van activiteit (1 voor uitvoering van trigger, 3 voor uitvoeringen van activiteit) |
| Gegevens kopiëren veronderstelling: uitvoerings tijd = 10 minuten | 10 \* 4 Azure Integration Runtime (standaard instelling voor DIU = 4) Zie [dit artikel](copy-activity-performance.md) voor meer informatie over de gegevens integratie-eenheden en het optimaliseren van de Kopieer prestaties |
| Bewaak pijp lijn controleren: er is slechts 1 uitvoering uitgevoerd | 4 uitgevoerde uitvoerings records voor de controle (1 voor de pijplijn uitvoering, 3 voor de uitvoering van de activiteit) |
| Opzoek activiteit uitvoeren hypo these: uitvoerings tijd = 1 min | 1 min uitvoering pijplijn activiteit |
| Uitvoer van Databricks-activiteit uitvoeren: uitvoerings tijd = 10 minuten | 10 min. uitvoering van externe pijplijn activiteit |

**Totaal prijs scenario: $0,17020**

- Data Factory bewerkingen = **$0,00013**
  - Lezen/schrijven = 11\*00001 = $0,00011 [1 R/W = $0,50/50.000 = 0,00001]
  - Bewaking = 4\*000005 = $0,00002 [1 bewaking = $0,25/50.000 = 0,000005]
- Pipeline-indeling &amp; Execution = **$0,17007**
  - Uitvoeringen van activiteit = 001\*4 = 0,004 [1 run = $1/1000 = 0,001]
  - Activiteiten voor gegevens verplaatsing = $0,166 (in verhouding tot 10 minuten voor de uitvoerings tijd). $0,25/uur op Azure Integration Runtime)
  - Pijplijn activiteit = $0,00003 (voor 1 minuut van uitvoer tijd). $0.002/uur op Azure Integration Runtime)
  - Externe pijplijn activiteit = $0,000041 (in verhouding tot 10 minuten van uitvoerings tijd. $0.00025/uur op Azure Integration Runtime)

## <a name="using-mapping-data-flow-debug-for-a-normal-workday"></a>Fout opsporing voor het toewijzen van gegevens stromen gebruiken voor een normale werkdag

Als data Engineer bent u verantwoordelijk voor het ontwerpen, bouwen en testen van gegevens stromen die dagelijks worden uitgevoerd. U meldt zich in de ochtend aan bij de ADF-gebruikers interface en schakelt de foutopsporingsmodus in voor gegevens stromen. De standaard-TTL voor debug-sessies is 60 minuten. U werkt gedurende 8 uur op de hele dag, waardoor de foutopsporingssessie nooit verloopt. Daarom zijn uw kosten voor de dag:

**8 (uur) x 8 (door Compute geoptimaliseerde kernen) x $0,193 = $12,35**

## <a name="transform-data-in-blob-store-with-mapping-data-flows"></a>Gegevens in BLOB Store transformeren met toewijzings gegevens stromen

In dit scenario wilt u gegevens in BLOB Store visueel transformeren in ADF-toewijzings gegevens stromen op schema.

Als u het scenario wilt volt ooien, moet u een pijp lijn maken met de volgende items:

1. Een gegevens stroom activiteit met de transformatie logica.

2. Een invoer gegevensset voor de gegevens op Azure Storage.

3. Een uitvoer gegevensset voor de gegevens op Azure Storage.

4. Een schema trigger om elk uur de pijp lijn uit te voeren.

| **Bewerkingen** | **Typen en eenheden** |
| --- | --- |
| Een gekoppelde service maken | 2 entiteit voor lezen/schrijven  |
| Gegevens sets maken | 4 entiteiten voor lezen/schrijven (2 voor het maken van de gegevensset, 2 voor gekoppelde service verwijzingen) |
| Pijp lijn maken | 3 entiteiten voor lezen/schrijven (1 voor het maken van pijp lijnen, 2 voor verwijzingen naar gegevensset) |
| Pijp lijn ophalen | 1 entiteit lezen/schrijven |
| Pijp lijn uitvoeren | 2 uitvoeringen van activiteit (1 voor uitvoering van trigger, 1 voor uitvoeringen van activiteit) |
| Veronderstellingen voor gegevens stroom: uitvoerings tijd = 10 min + 10 min TTL | 10 \* 16 kernen van de algemene Compute met TTL van 10 |
| Bewaak pijp lijn controleren: er is slechts 1 uitvoering uitgevoerd | 2 uitgevoerde pogingen voor het controleren van records (1 voor de pijplijn uitvoering, 1 voor de uitvoering van de activiteit) |

**Totaal prijs scenario: $1,4631**

- Data Factory bewerkingen = **$0,0001**
  - Lezen/schrijven = 10\*00001 = $0,0001 [1 R/W = $0,50/50.000 = 0,00001]
  - Bewaking = 2\*000005 = $0,00001 [1 bewaking = $0,25/50.000 = 0,000005]
- Pipeline-indeling &amp; Execution = **$1,463**
  - Uitvoeringen van activiteit = 001\*2 = 0,002 [1 run = $1/1000 = 0,001]
  - Data flow-activiteiten = $1,461 naar verhouding van 20 minuten (10 minuten uitvoerings tijd + 10 minuten TTL). $0.274/uur op Azure Integration Runtime met een algemene reken kracht van 16 kernen

## <a name="next-steps"></a>Volgende stappen

Nu u de prijzen voor Azure Data Factory begrijpt, kunt u aan de slag.

- [Een data factory maken met behulp van de Azure Data Factory gebruikers interface](quickstart-create-data-factory-portal.md)

- [Inleiding tot Azure Data Factory](introduction.md)

- [Visueel ontwerpen in Azure Data Factory](author-visually.md)
