---
title: Azure-gegevensfabriek - Voorbeelden
description: Bevat informatie over voorbeelden die worden verzonden met de Azure Data Factory-service.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 63db1810cbdd2133bc0577530325351997b31f30
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70139263"
---
# <a name="azure-data-factory---samples"></a>Azure-gegevensfabriek - Voorbeelden
> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Zie [PowerShell-voorbeelden in Gegevensfabriek](../samples-powershell.md) en [codevoorbeelden in de azure code samples gallery](https://azure.microsoft.com/resources/samples/?service=data-factory)als u de huidige versie van de datafabriekservice gebruikt.


## <a name="samples-on-github"></a>Voorbeelden op GitHub
De [GitHub Azure-DataFactory-repository](https://github.com/azure/azure-datafactory) bevat verschillende voorbeelden waarmee u snel opvoeren met de Azure Data Factory-service (of) de scripts wijzigen en in een eigen toepassing gebruiken. De map Samples\JSON bevat JSON-fragmenten voor veelvoorkomende scenario's.

| Voorbeeld | Beschrijving |
|:--- |:--- |
| [ADF-walkthrough](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/ADFWalkthrough) |Dit voorbeeld biedt een end-to-end walkthrough voor het verwerken van logbestanden met Azure Data Factory om gegevens van logbestanden om te zetten in inzichten. <br/><br/>In deze walkthrough verzamelt de Pipeline Data Factory voorbeeldlogboeken, verwerkt en verrijkt de gegevens uit logboeken met referentiegegevens en transformeert de gegevens om de effectiviteit van een onlangs gestarte marketingcampagne te evalueren. |
| [JSON-monsters](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/JSON) |Deze steekproef bevat JSON-voorbeelden voor veelvoorkomende scenario's. |
| [Voorbeeld van http-gegevensdownloader](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/HttpDataDownloaderSample) |In dit voorbeeld wordt het downloaden van gegevens van een HTTP-eindpunt naar Azure Blob Storage weergegeven met behulp van aangepaste .NET-activiteit. |
| [Voorbeeld van cross-appdomaindotnetactiviteit](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/CrossAppDomainDotNetActivitySample) |Met dit voorbeeld u een aangepaste .NET-activiteit maken die niet is beperkt tot assemblageversies die worden gebruikt door het ADF-launcher (bijvoorbeeld WindowsAzure.Storage v4.3.0, Newtonsoft.Json v6.0.x, enz.). |
| [R-script uitvoeren](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample) |Dit voorbeeld bevat de aangepaste activiteit Gegevensfabriek die kan worden gebruikt om RScript.exe aan te roepen. Dit voorbeeld werkt alleen met uw eigen (niet on-demand) HDInsight-cluster waarop r al is geïnstalleerd. |
| [Spark-taken aanroepen op hdInsight Hadoop-cluster](https://docs.microsoft.com/azure/data-factory/tutorial-transform-data-spark-portal) |In dit voorbeeld ziet u hoe u MapReduce-activiteit gebruikt om een Spark-programma aan te roepen. Het spark-programma kopieert alleen gegevens van de ene Azure Blob-container naar de andere. |
| [Twitter-analyse met Azure Machine Learning Batch scoring Activity](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/TwitterAnalysisSample-AzureMLBatchScoringActivity) |In dit voorbeeld wordt uitgelegd hoe u AzureMLBatchScoringActivity gebruiken om een Azure Machine Learning-model aan te roepen dat twittersentimentanalyse, -score, voorspelling enz. |
| [Twitter-analyse met aangepaste activiteit](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/TwitterAnalysisSample-CustomC%23Activity) |In dit voorbeeld ziet u hoe u een aangepaste .NET-activiteit gebruiken om een Azure Machine Learning-model aan te roepen dat twittersentimentanalyse, -score, voorspelling enz. |
| [Geparameteriseerde pijplijnen voor Azure Machine Learning](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/ParameterizedPipelinesForAzureML) |Het voorbeeld biedt een end-to-end C#-code voor het implementeren van N-pijplijnen voor het scoren en omscholen van elk met een andere regioparameter waarbij de lijst met regio's afkomstig is van een bestand parameters.txt, dat bij dit voorbeeld is opgenomen. |
| [Vernieuwen van referentiegegevens voor Azure Stream Analytics-taken](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/ReferenceDataRefreshForASAJobs) |In dit voorbeeld ziet u hoe u Azure Data Factory en Azure Stream Analytics samen gebruiken om de query's met referentiegegevens uit te voeren en de vernieuwing in te stellen voor referentiegegevens in een planning. |
| [Hybride pijpleiding met on-premises Hortonworks Hadoop](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/HybridPipelineWithOnPremisesHortonworksHadoop) |Het voorbeeld maakt gebruik van een on-premises Hadoop-cluster als een compute-doel voor het uitvoeren van taken in Data Factory, net zoals u andere compute targets zou toevoegen, zoals een HDInsight-gebaseerd Hadoop-cluster in de cloud. |
| [Json-conversietool](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/JSONConversionTool) |Met deze tool u JSON's converteren van versie vóór 2015-07-01-preview naar de nieuwste of 2015-07-01-preview (standaard). |
| [U-SQL-voorbeeldinvoerbestand](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/U-SQL%20Sample%20Input%20File) |Dit bestand is een voorbeeldbestand dat wordt gebruikt door een U-SQL-activiteit. |
| [Blobbestand verwijderen](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/DeleteBlobFileFolderCustomActivity) | In dit voorbeeld wordt een C#-bestand weergegeven dat kan worden gebruikt als onderdeel van adf-aangepaste .net-activiteit om bestanden van de bron Azure Blob-locatie te verwijderen zodra de bestanden zijn gekopieerd.|

## <a name="azure-resource-manager-templates"></a>Azure Resource Manager-sjablonen
U vindt de volgende Azure Resource Manager-sjablonen voor Gegevensfabriek op GitHub.

| Template | Beschrijving |
| --- | --- |
| [Kopiëren vanuit Azure Blob Storage naar Azure SQL Database](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-blob-to-sql-copy) |Als u deze sjabloon implementeert, wordt een Azure-gegevensfabriek gemaakt met een pijplijn die gegevens van de opgegeven Azure blob-opslag kopieert naar de Azure SQL-database |
| [Kopiëren van Salesforce naar Azure Blob Storage](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-salesforce-to-blob-copy) |Als u deze sjabloon implementeert, wordt een Azure-gegevensfabriek gemaakt met een pijplijn die gegevens van het opgegeven Salesforce-account kopieert naar de Azure-blobopslag. |
| [Gegevens transformeren door Hive-script uit te voeren op een Azure HDInsight-cluster](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-hive-transformation) |Als u deze sjabloon implementeert, wordt een Azure-gegevensfabriek gemaakt met een pijplijn die gegevens transformeert door het voorbeeldhive-script uit te voeren op een Azure HDInsight Hadoop-cluster. |

## <a name="samples-in-azure-portal"></a>Voorbeelden in Azure-portal
U de tegel **Voorbeeldpijplijnen** op de startpagina van uw gegevensfabriek gebruiken om voorbeeldpijplijnen en de bijbehorende entiteiten (gegevenssets en gekoppelde services) in uw gegevensfabriek te implementeren.

1. Maak een gegevensfabriek of open een bestaande gegevensfabriek. Zie [Gegevens kopiëren van Blob-opslag naar SQL-database met Gegevensfabriek](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) voor stappen om een gegevensfabriek te maken.
2. Klik in het **DATA FACTORY-blad** voor de gegevensfabriek op de tegel **Voorbeeldpijplijnen.**

    ![Betegel voorbeeldpijplijnen](./media/data-factory-samples/SamplePipelinesTile.png)
3. Klik in het **blade van voorbeeldpijplijnen** op het **voorbeeld** dat u wilt implementeren.

    ![Voorbeeldvan pijpleidingenblad](./media/data-factory-samples/SampleTile.png)
4. Geef configuratie-instellingen op voor het voorbeeld. Bijvoorbeeld uw Azure-opslagaccountnaam en accountsleutel, Azure SQL-servernaam, database, gebruikersnaam en wachtwoord, enz.

    ![Monsterblad](./media/data-factory-samples/SampleBlade.png)
5. Nadat u klaar bent met het opgeven van de configuratie-instellingen, klikt u op **Maken** om de voorbeeldpijplijnen en gekoppelde services/tabellen die door de pijplijnen worden gebruikt, te maken/implementeren.
6. U ziet de status van implementatie op de voorbeeldtegel waarop u eerder hebt geklikt op het blade van de **voorbeeldpijplijnen.**

    ![Implementatiestatus](./media/data-factory-samples/DeploymentStatus.png)
7. Wanneer u het bericht **Implementatie op de** tegel voor het voorbeeld ziet, sluit u het blade van de **voorbeeldpijplijnen.**  
8. Op **DATA FACTORY blade** ziet u dat gekoppelde services, gegevenssets en pijplijnen worden toegevoegd aan uw gegevensfabriek.  

    ![Blade Gegevensfactory](./media/data-factory-samples/DataFactoryBladeAfter.png)

## <a name="samples-in-visual-studio"></a>Voorbeelden in Visual Studio
### <a name="prerequisites"></a>Vereisten
De volgende zaken moeten op uw computer zijn geïnstalleerd:

* Visual Studio 2013 of Visual Studio 2015
* Download de Azure SDK voor Visual Studio 2013 of Visual Studio 2015. Ga naar de [Azure-downloadpagina](https://azure.microsoft.com/downloads/) en klik in het gedeelte **.NET** op **VS 2013** of **VS 2015**.
* Download de nieuwste Azure Data Factory-invoegtoepassing voor Visual Studio: [VS 2013](https://visualstudiogallery.msdn.microsoft.com/754d998c-8f92-4aa7-835b-e89c8c954aa5) of [VS 2015](https://visualstudiogallery.msdn.microsoft.com/371a4cf9-0093-40fa-b7dd-be3c74f49005). Als u Visual Studio 2013 gebruikt, u de plug-in ook bijwerken door de volgende stappen te doen: klik in het menu op -> **Extra-extensies en Updates** -> **Online** -> **Visual Studio Gallery** -> **Microsoft Azure Data Factory Tools voor Visual Studio** -> **Update**. **Tools**

### <a name="use-data-factory-templates"></a>Gegevensfabriekssjablonen gebruiken
1. Klik op **Bestand** in het menu, wijs **Nieuw**aan en klik op **Project**.
2. Voer in het dialoogvenster **Nieuw project** de volgende stappen uit:

   1. Selecteer **DataFactory** onder **Sjablonen**.
   2. Selecteer **Gegevensfabriekssjablonen** in het rechterdeelvenster.
   3. Voer een **naam** in voor het project.
   4. Selecteer een **locatie** voor het project.
   5. Klik op **OK**.

      ![Het dialoogvenster New Project](./media/data-factory-samples/vs-new-project-adf-templates.png)
3. Selecteer in het dialoogvenster **Sjablonen voor gegevensfabrieken** de voorbeeldsjabloon in de sectie **Use-Case Templates** en klik op **Volgende**. Met de volgende stappen u de sjabloon **Klantprofilering** gebruiken. Stappen zijn vergelijkbaar voor de andere monsters.

    ![Dialoogvenster Gegevensfabriekssjablonen](./media/data-factory-samples/vs-data-factory-templates-dialog.png)
4. Klik in het dialoogvenster **Configuratie van gegevensfabriek** op **Volgende** op de basispagina van **de gegevensfabriek.**
5. Ga op de fabriekspagina **Gegevens configureren** de volgende stappen uit:
   1. Selecteer **Nieuwe gegevensfabriek maken**. U ook **bestaande gegevensfabriek gebruiken**selecteren.
   2. Voer een **naam** in voor de gegevensfabriek.
   3. Selecteer het **Azure-abonnement** waarin u de gegevensfabriek wilt maken.
   4. Selecteer de **resourcegroep** voor de gegevensfabriek.
   5. Selecteer de **West-VS,** **Oost-VS**of **Noord-Europa** voor de **regio**.
   6. Klik op **Volgende**.
6. Geef op de pagina **Gegevensopslag configureren** een bestaande Azure SQL-database en **Azure-opslagaccount** (of) database/opslag maken en klik op Volgende. **Azure SQL database**
7. Selecteer **op** de pagina Compute configureren standaardinstellingen en klik op **Volgende**.
8. Controleer **op** de pagina Overzicht alle instellingen en klik op **Volgende**.
9. Wacht op de pagina **Implementatiestatus** totdat de implementatie is voltooid en klik op **Voltooien**.
10. Klik met de rechtermuisknop op het project in Solution Explorer. Klik vervolgens op **Publish**.
11. Als u het dialoogvenster **Sign in to your Microsoft account** ziet, voert u uw referenties in voor het account met het Azure-abonnement en klikt u op **Sign in**.
12. Het volgende dialoogvenster wordt weergegeven:

    ![Het dialoogvenster Publish](./media/data-factory-build-your-first-pipeline-using-vs/publish.png)
13. Op de pagina **Configure data factory** voert u de volgende stappen uit:

    1. Controleer of De optie **Bestaande gegevensfabriek gebruiken.**
    2. Selecteer de **gegevensfabriek die** u had geselecteerd wanneer u de sjabloon gebruikt.
    3. Klik op **Next** om over te schakelen naar de pagina **Publish Items**. (Druk op **TAB** als u het veld Naam wilt verlaten, maar de knop **Next** is uitgeschakeld.)
14. Op de pagina **Publish Items** controleert u of alle Data Factory-entiteiten zijn geselecteerd en klikt u op **Next** om over te schakelen naar de pagina **Summary**.     
15. Controleer de samenvatting en klik op **Next** om te beginnen met het implementatieproces en om de **implementatiestatus** te bekijken.
16. Op de pagina **Deployment Status** ziet u de status van het implementatieproces. Klik op Finish wanneer de implementatie is uitgevoerd.

Zie [Uw eerste gegevensfabriek (Visual Studio) bouwen](data-factory-build-your-first-pipeline-using-vs.md) voor meer informatie over het gebruik van Visual Studio om entiteiten in Data Factory te schrijven en te publiceren naar Azure.          
