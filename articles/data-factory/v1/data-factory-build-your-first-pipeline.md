---
title: 'Zelfstudie Data Factory: Eerste gegevenspijplijn '
description: In deze zelfstudie van Azure Data Factory ziet u hoe u een gegevensfabriek maakt en plant die gegevens verwerkt met Hive-script op een Hadoop-cluster.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.openlocfilehash: 80644ed2d655544fa176a7be92aec3c01aa3bf14
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75966077"
---
# <a name="tutorial-build-your-first-pipeline-to-transform-data-using-hadoop-cluster"></a>Zelfstudie: Bouw uw eerste pijplijn om gegevens te transformeren met hadoop-cluster
> [!div class="op_single_selector"]
> * [Overzicht en vereisten](data-factory-build-your-first-pipeline.md)
> * [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
> * [Powershell](data-factory-build-your-first-pipeline-using-powershell.md)
> * [Sjabloon ResourceManager](data-factory-build-your-first-pipeline-using-arm.md)
> * [REST-API](data-factory-build-your-first-pipeline-using-rest-api.md)


> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Als u de huidige versie van de Data Factory-service gebruikt, raadpleegt u [snelstart: een gegevensfactory maken met Azure Data Factory](../quickstart-create-data-factory-dot-net.md).

In deze zelfstudie bouwt u uw eerste Azure-gegevensfabriek met een gegevenspijplijn. De pijplijn transformeert invoergegevens door Hive-script uit te voeren op een Azure HDInsight -cluster (Hadoop) om uitvoergegevens te produceren.  

In dit artikel vindt u overzicht en vereisten voor de zelfstudie. Nadat u de vereisten hebt voltooid, u de zelfstudie uitvoeren met een van de volgende hulpprogramma's/SDK's: Visual Studio, PowerShell, Resource Manager-sjabloon, REST API. Selecteer een van de opties in de vervolgkeuzelijst aan het begin (of) links aan het einde van dit artikel om de zelfstudie te doen met behulp van een van deze opties.    

## <a name="tutorial-overview"></a>Overzicht van de zelfstudie
In deze zelfstudie voert u de volgende stappen uit:

1. Maak een **gegevensfabriek**. Een gegevensfabriek kan een of meer gegevenspijplijnen bevatten die gegevens verplaatsen en transformeren.

    In deze zelfstudie maakt u één pijplijn in de gegevensfabriek.
2. Een **pijplijn maken**. Een pijplijn kan een of meer activiteiten bevatten (bijvoorbeeld: kopieeractiviteit, HDInsight Hive-activiteit). In dit voorbeeld wordt gebruik gemaakt van de HDInsight Hive-activiteit die een Hive-script uitvoert op een HDInsight Hadoop-cluster. Het script maakt eerst een tabel die verwijst naar de ruwe weblogboekgegevens die zijn opgeslagen in Azure blob-opslag en vervolgens de ruwe gegevens per jaar en maand partities.

    In deze zelfstudie gebruikt de pijplijn de Hive-activiteit om gegevens te transformeren door een Hive-query uit te voeren op een Azure HDInsight Hadoop-cluster.
3. Gekoppelde **services maken**. U maakt een gekoppelde service om een gegevensopslag of een rekenservice te koppelen aan de gegevensfactory. In een gegevensopslag zoals Azure Storage worden invoer-/uitvoergegevens bewaard van activiteiten in de pijplijn. Een compute service zoals HDInsight Hadoop cluster processen/transformeert data.

    In deze zelfstudie maakt u twee gekoppelde services: **Azure Storage** en **Azure HDInsight**. De gekoppelde Azure Storage-service koppelt een Azure Storage-account met de invoer-/uitvoergegevens aan de gegevensfabriek. Azure HDInsight gekoppelde service koppelt een Azure HDInsight-cluster dat wordt gebruikt om gegevens te transformeren naar de gegevensfabriek.
3. Maak **gegevenssets** voor invoer en uitvoer. Een invoergegevensset vertegenwoordigt de invoer voor een activiteit in de pijplijn en een uitvoergegevensset vertegenwoordigt de uitvoer voor de activiteit.

    In deze zelfstudie geven de invoer- en uitvoergegevens locaties op van invoer- en uitvoergegevens in de Azure Blob Storage. De gekoppelde Azure Storage-service geeft aan welk Azure Storage-account wordt gebruikt. Een invoergegevensset geeft aan waar de invoerbestanden zich bevinden en een uitvoergegevensset geeft aan waar de uitvoerbestanden worden geplaatst.


Zie Inleiding tot het artikel [Van Azure Data Factory](data-factory-introduction.md) voor een gedetailleerd overzicht van Azure Data Factory.

Hier is de **diagramweergave** van de voorbeeldgegevensfabriek die u in deze zelfstudie bouwt. **MyFirstPipeline** heeft één activiteit van type Hive die **AzureBlobInput-gegevensset** als invoer gebruikt en **AzureBlobOutput-gegevensset** produceert als uitvoer.

![Zelfstudie voor de Data Factory-diagramweergave](media/data-factory-build-your-first-pipeline/data-factory-tutorial-diagram-view.png)


In deze zelfstudie bevat **de map invoergegevens** van de Azure blobcontainer met **adfgetstarted** één bestand met de naam input.log. Dit logboekbestand heeft vermeldingen van drie maanden: januari, februari en maart 2016. Hier ziet u de voorbeeldrijen per maand in het invoerbestand.

```
2016-01-01,02:01:09,SAMPLEWEBSITE,GET,/blogposts/mvc4/step2.png,X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,53175,871
2016-02-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871
2016-03-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871
```

Wanneer het bestand is verwerkt via de pijplijn met HDInsight Hive-activiteit, wordt met de activiteit een Hive-script uitgevoerd op de HDInsight-cluster waardoor de invoer wordt gesplitst in jaren en maanden. Met het script worden drie uitvoermappen gemaakt die een bestand bevatten met de invoer uit elke maand.  

```
adfgetstarted/partitioneddata/year=2016/month=1/000000_0
adfgetstarted/partitioneddata/year=2016/month=2/000000_0
adfgetstarted/partitioneddata/year=2016/month=3/000000_0
```

Uit de bovenstaande voorbeeldregels wordt de eerste (met 2016-01-01) naar het 000000_0-bestand in de map month=1 geschreven. Op dezelfde manier is de tweede regel geschreven naar het bestand in de map maand=2 en de derde naar het bestand in de map maand=3.  

## <a name="prerequisites"></a>Vereisten
Voordat u met deze zelfstudie begint, moet u aan de volgende vereisten voldoen:

1. U moet beschikken over een **Azure-abonnement**. Als u geen Azure-abonnement hebt, kunt u in enkele minuten een gratis proefaccount maken. Zie het artikel [Gratis proefversie](https://azure.microsoft.com/pricing/free-trial/) voor informatie over het verkrijgen van een gratis proefaccount.
2. U moet gebruikmaken van **Azure Storage**. U gebruikt een Azure-opslagaccount om de gegevens in deze zelfstudie op te slaan. Zie het artikel [Een opslagaccount maken](../../storage/common/storage-account-create.md) als u geen account Azure-opslagaccount hebt. Nadat u het opslagaccount hebt gemaakt, noteert u de **accountnaam** en **de toegangssleutel**. Zie [Toegangssleutels voor opslagaccount beheren](../../storage/common/storage-account-keys-manage.md)voor informatie over het ophalen van de toegangssleutels voor opslagaccount.
3. Download en bekijk het Hive querybestand **(HQL)** op: [https://adftutorialfiles.blob.core.windows.net/hivetutorial/partitionweblogs.hql](https://adftutorialfiles.blob.core.windows.net/hivetutorial/partitionweblogs.hql). Met deze query worden invoergegevens getransformeerd om uitvoergegevens te produceren.
4. Download en controleer het voorbeeldinvoerbestand **(input.log)** op:[https://adftutorialfiles.blob.core.windows.net/hivetutorial/input.log](https://adftutorialfiles.blob.core.windows.net/hivetutorial/input.log)
5. Maak een blobcontainer met de naam **adfgetstarted** in uw Azure Blob Storage.
6. Upload **partitionweblogs.hql-bestand** naar de **scriptmap** in de **adfgetstarted-container.** Gebruik hulpprogramma's zoals [Microsoft Azure Storage Explorer](https://storageexplorer.com/).
7. Upload **input.log-bestand** naar de map **invoergegevens** in de **container met adfgetstarted.**

Nadat u de vereisten hebt voltooid, selecteert u een van de volgende hulpprogramma's/SDK's om de zelfstudie uit te voeren:

- [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [Powershell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Sjabloon ResourceManager](data-factory-build-your-first-pipeline-using-arm.md)
- [REST-API](data-factory-build-your-first-pipeline-using-rest-api.md)

Visual Studio biedt een GUI-manier om uw datafabrieken te bouwen. Terwijl, PowerShell, Resource Manager Template, en REST API opties biedt scripting / programmering manier van het bouwen van uw data fabrieken.

> [!NOTE]
> Met de gegevenspijplijn in deze zelfstudie worden invoergegevens getransformeerd in uitvoergegevens. Er worden bijvoorbeeld geen gegevens gekopieerd van een brongegevensarchief naar een doelgegevensarchief. Zie [Zelfstudie: gegevens kopiëren van Blob Storage naar SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) voor informatie over het kopiëren van gegevens met Azure Data Factory.
>
> U kunt twee activiteiten koppelen (de ene activiteit na de andere laten uitvoeren) door de uitvoergegevensset van één activiteit in te stellen als invoergegevensset voor een andere activiteit. Zie [Planning en uitvoering in Data Factory](data-factory-scheduling-and-execution.md) voor gedetailleerde informatie.
