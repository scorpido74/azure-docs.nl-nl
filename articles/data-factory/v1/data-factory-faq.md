---
title: Azure Data Factory - veelgestelde vragen
description: Veelgestelde vragen over Azure Data Factory.
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
ms.openlocfilehash: 1b042c0279e458cf83bd5c81147f4b1f25d7687d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645137"
---
# <a name="azure-data-factory---frequently-asked-questions"></a>Azure Data Factory - veelgestelde vragen
> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Als u de huidige versie van de Data [Factory-service](../frequently-asked-questions.md)gebruikt, raadpleegt u de veelgestelde vraag - Data Factory .

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="general-questions"></a>Algemene vragen
### <a name="what-is-azure-data-factory"></a>Wat is Azure Data Factory?
Data Factory is een cloudgebaseerde data-integratieservice die **de beweging en transformatie van gegevens automatiseert.** Net als een fabriek die apparatuur draait om grondstoffen te verwerken en om te zetten in afgewerkte goederen, orkestreert Data Factory bestaande diensten die ruwe gegevens verzamelen en omzetten in kant-en-klare informatie.

Met Data Factory u gegevensgestuurde werkstromen maken om gegevens te verplaatsen tussen zowel on-premises als cloudgegevensopslag, evenals gegevens te verwerken/transformeren met behulp van compute services zoals Azure HDInsight en Azure Data Lake Analytics. Nadat u een pijplijn hebt gemaakt die de actie uitvoert die u nodig hebt, u deze periodiek plannen (per uur, dagelijks, wekelijks enz.).   

Zie [Overzicht & sleutelbegrippen voor](data-factory-introduction.md)meer informatie.

### <a name="where-can-i-find-pricing-details-for-azure-data-factory"></a>Waar vind ik prijsgegevens voor Azure Data Factory?
Zie [de pagina Met prijsdetails voor gegevensfabriek][adf-pricing-details] voor de prijsdetails voor de Azure Data Factory.  

### <a name="how-do-i-get-started-with-azure-data-factory"></a>Hoe ga ik aan de slag met Azure Data Factory?
* Zie [Inleiding tot Azure Data Factory](data-factory-introduction.md)voor een overzicht van Azure Data Factory.
* Zie [Gegevens kopiëren van Azure Blob Storage naar Azure SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)voor een zelfstudie over het **kopiëren/verplaatsen van gegevens** met Kopieeractiviteit.
* Voor een zelfstudie over het **transformeren van gegevens** met HDInsight Hive Activity. Zie [Procesgegevens door Hive-script uit te voeren op het Hadoop-cluster](data-factory-build-your-first-pipeline.md)

### <a name="what-is-the-data-factorys-region-availability"></a>Wat is de beschikbaarheid van de regio van de Data Factory?
Data Factory is beschikbaar in **de VS West-** en **Noord-Europa.** De reken- en opslagservices die door gegevensfabrieken worden gebruikt, kunnen zich in andere regio's begeven. Zie [Ondersteunde regio's](data-factory-introduction.md#supported-regions).

### <a name="what-are-the-limits-on-number-of-data-factoriespipelinesactivitiesdatasets"></a>Wat zijn de beperkingen op het aantal datafabrieken/pipelines/activiteiten/datasets?
Zie **azure data factory limits** sectie van het [azure-abonnement en servicelimieten, quota en beperkingen](../../azure-resource-manager/management/azure-subscription-service-limits.md#data-factory-limits) artikel.

### <a name="what-is-the-authoringdeveloper-experience-with-azure-data-factory-service"></a>Wat is de ervaring van authoring/developer met Azure Data Factory-service?
U gegevensfabrieken maken/maken met een van de volgende tools/SDK's:

* **Visual Studio** U Visual Studio gebruiken om een Azure-gegevensfabriek te maken. Zie [Uw eerste gegevenspijplijn maken met Visual Studio](data-factory-build-your-first-pipeline-using-vs.md) voor meer informatie.
* **Azure PowerShell** Zie [Azure Data Factory maken en bewaken met Azure PowerShell](data-factory-build-your-first-pipeline-using-powershell.md) voor een zelfstudie/walkthrough voor het maken van een gegevensfabriek met PowerShell. Zie [Gegevensfabriek Cmdlet-referentie-inhoud][adf-powershell-reference] op MSDN Library voor een uitgebreide documentatie van gegevensfabriek-cmdlets.
* **.NET-klassebibliotheek** U programmatisch datafabrieken maken met Data Factory .NET SDK. Zie [Gegevensfabrieken maken, bewaken en beheren met behulp van .NET SDK](data-factory-create-data-factories-programmatically.md) voor een walkthrough van het maken van een gegevensfabriek met .NET SDK. Zie [Gegevensfactory Class Library Reference][msdn-class-library-reference] voor een uitgebreide documentatie van Data Factory .NET SDK.
* **REST-API** U de REST-API die door de Azure Data Factory-service wordt weergegeven, ook gebruiken om gegevensfabrieken te maken en te implementeren. Zie [Data Factory REST API Reference][msdn-rest-api-reference] voor een uitgebreide documentatie van Data Factory REST API.
* **Sjabloon Azure Resource Manager** Zie [Zelfstudie: Bouw uw eerste Azure-gegevensfabriek met azure resource beheersjabloon](data-factory-build-your-first-pipeline-using-arm.md) voor details.

### <a name="can-i-rename-a-data-factory"></a>Kan ik de naam van een gegevensfabriek wijzigen?
Nee. Net als andere Azure-bronnen kan de naam van een Azure-gegevensfabriek niet worden gewijzigd.

### <a name="can-i-move-a-data-factory-from-one-azure-subscription-to-another"></a>Kan ik een gegevensfabriek verplaatsen van het ene Azure-abonnement naar het andere?
Ja. Gebruik de knop **Verplaatsen** op het fabrieksblad van uw gegevens, zoals in het volgende diagram wordt weergegeven:

![Gegevensfabriek verplaatsen](media/data-factory-faq/move-data-factory.png)

### <a name="what-are-the-compute-environments-supported-by-data-factory"></a>Wat zijn de compute-omgevingen die worden ondersteund door Data Factory?
In de volgende tabel vindt u een lijst met rekenomgevingen die worden ondersteund door Data Factory en de activiteiten die erop kunnen worden uitgevoerd.

| Compute-omgeving | activities |
| --- | --- |
| [On-demand HDInsight-cluster](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) of [uw eigen HDInsight-cluster](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) |[DotNet](data-factory-use-custom-activities.md), [Hive](data-factory-hive-activity.md), [Pig](data-factory-pig-activity.md), [MapReduce](data-factory-map-reduce.md), [Hadoop Streaming](data-factory-hadoop-streaming-activity.md) |
| [Azure Batch](data-factory-compute-linked-services.md#azure-batch-linked-service) |[DotNet](data-factory-use-custom-activities.md) |
| [Azure Machine Learning](data-factory-compute-linked-services.md#azure-machine-learning-linked-service) |[Machine Learning-activiteiten: batchuitvoering en resources bijwerken](data-factory-azure-ml-batch-execution-activity.md) |
| [Azure Data Lake Analytics](data-factory-compute-linked-services.md#azure-data-lake-analytics-linked-service) |[Data Lake Analytics U-SQL](data-factory-usql-activity.md) |
| [Azure SQL](data-factory-compute-linked-services.md#azure-sql-linked-service), [Azure SQL Data Warehouse](data-factory-compute-linked-services.md#azure-sql-data-warehouse-linked-service), SQL [Server](data-factory-compute-linked-services.md#sql-server-linked-service) |[Opgeslagen procedure](data-factory-stored-proc-activity.md) |

### <a name="how-does-azure-data-factory-compare-with-sql-server-integration-services-ssis"></a>Hoe verhoudt Azure Data Factory zich tot SQL Server Integration Services (SSIS)? 
Bekijk de [Azure Data Factory vs. SSIS-presentatie](https://www.sqlbits.com/Sessions/Event15/Azure_Data_Factory_vs_SSIS) van een van onze MVP's (Most Valued Professionals): Reza Rad. Sommige recente wijzigingen in Data Factory worden mogelijk niet vermeld in het diadek. We voegen voortdurend meer mogelijkheden toe aan Azure Data Factory. We voegen voortdurend meer mogelijkheden toe aan Azure Data Factory. We zullen deze updates in de vergelijking van data-integratie technologieën van Microsoft ergens later dit jaar.   

## <a name="activities---faq"></a>Activiteiten - VEELGESTELDE VRAGEN
### <a name="what-are-the-different-types-of-activities-you-can-use-in-a-data-factory-pipeline"></a>Wat zijn de verschillende soorten activiteiten die u gebruiken in een Data Factory-pijplijn?
* [Activiteiten voor gegevensverplaatsing](data-factory-data-movement-activities.md) om gegevens te verplaatsen.
* [Activiteiten voor gegevenstransformatie](data-factory-data-transformation-activities.md) om gegevens te verwerken/transformeren.

### <a name="when-does-an-activity-run"></a>Wanneer wordt een activiteit uitgevoerd?
De **configuratie-instelling voor beschikbaarheid** in de uitvoergegevenstabel bepaalt wanneer de activiteit wordt uitgevoerd. Als invoergegevenssets zijn opgegeven, controleert de activiteit of aan alle afhankelijkheden van invoergegevens is voldaan (dat wil zeggen de status **Gereed)** voordat deze wordt uitgevoerd.

## <a name="copy-activity---faq"></a>Activiteit kopiëren - Veelgestelde vragen
### <a name="is-it-better-to-have-a-pipeline-with-multiple-activities-or-a-separate-pipeline-for-each-activity"></a>Is het beter om een pijplijn te hebben met meerdere activiteiten of een aparte pijplijn voor elke activiteit?
Pijpleidingen worden verondersteld om gerelateerde activiteiten te bundelen. Als de gegevenssets die deze verbinding maken, niet worden verbruikt door andere activiteiten buiten de pijplijn, u de activiteiten in één pijplijn houden. Op deze manier hoeft u geen actieve perioden van pijplijnen te ketenen, zodat ze op elkaar aansluiten. Ook wordt de gegevensintegriteit in de tabellen intern in de pijplijn beter bewaard bij het bijwerken van de pijplijn. Pijplijnupdate stopt in wezen alle activiteiten in de pijplijn, verwijdert ze en maakt ze opnieuw. Vanuit het oogpunt van auteur, kan het ook gemakkelijker zijn om de stroom van gegevens binnen de gerelateerde activiteiten te zien in een JSON-bestand voor de pijplijn.

### <a name="what-are-the-supported-data-stores"></a>Wat zijn de ondersteunde gegevensarchieven?
De kopieeractiviteit in Data Factory kopieert gegevens van een brongegevensarchief naar een sinkgegevensarchief. Data Factory ondersteunt de volgende gegevensarchieven. Gegevens vanuit elke willekeurige bron kunnen naar een sink worden geschreven. Klik op een gegevensarchief voor informatie over het kopiëren van gegevens naar en van dat archief.

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

> [!NOTE]
> Gegevensarchieven met een * kunnen zich on-premises of op Azure IaaS bevinden. Hiervoor moet u [Data Management Gateway](data-factory-data-management-gateway.md) installeren op een on-premises/Azure IaaS-computer.

### <a name="what-are-the-supported-file-formats"></a>Wat zijn de ondersteunde bestandsindelingen?
[!INCLUDE [data-factory-file-format](../../../includes/data-factory-file-format.md)]

### <a name="where-is-the-copy-operation-performed"></a>Waar wordt de kopieerbewerking uitgevoerd?
Zie sectie [Globaal beschikbare gegevensverplaatsing](data-factory-data-movement-activities.md#global) voor meer informatie. Kortom, wanneer het om een on-premises gegevensarchief gaat, wordt de kopieerbewerking uitgevoerd door de Data Management Gateway in uw on-premises omgeving. En wanneer de gegevensverplaatsing zich tussen twee cloudopslagbevindt, wordt de kopieerbewerking uitgevoerd in het gebied dat het dichtst bij de gootsteenlocatie in dezelfde geografie ligt.

## <a name="hdinsight-activity---faq"></a>HDInsight-activiteit - veelgestelde vragen
### <a name="what-regions-are-supported-by-hdinsight"></a>Welke regio's worden ondersteund door HDInsight?
Zie de sectie Geografische beschikbaarheid in het volgende artikel: of [HDInsight-prijsdetails][hdinsight-supported-regions].

### <a name="what-region-is-used-by-an-on-demand-hdinsight-cluster"></a>Welke regio wordt gebruikt door een on-demand HDInsight-cluster?
Het on-demand HDInsight-cluster wordt gemaakt in dezelfde regio waar de opslag die u hebt opgegeven om te worden gebruikt met het cluster bestaat.    

### <a name="how-to-associate-additional-storage-accounts-to-your-hdinsight-cluster"></a>Extra opslagaccounts koppelen aan uw HDInsight-cluster?
Als u uw eigen HDInsight Cluster (BYOC - Bring Your Own Cluster) gebruikt, raadpleegt u de volgende onderwerpen:

* [Een HDInsight-cluster gebruiken met alternatieve opslagaccounts en metastores][hdinsight-alternate-storage]
* [Extra opslagaccounts gebruiken met HDInsight Hive][hdinsight-alternate-storage-2]

Als u een on-demand cluster gebruikt dat is gemaakt door de Data Factory-service, geeft u extra opslagaccounts op voor de gekoppelde SERVICE HDInsight, zodat de datafabriekservice deze namens u kan registreren. Gebruik in de JSON-definitie voor de on-demand gekoppelde service de eigenschap **AdditionalLinkedServiceNames** om alternatieve opslagaccounts op te geven zoals weergegeven in het volgende JSON-fragment:

```JSON
{
    "name": "MyHDInsightOnDemandLinkedService",
    "properties":
    {
        "type": "HDInsightOnDemandLinkedService",
        "typeProperties": {
            "version": "3.5",
            "clusterSize": 1,
            "timeToLive": "00:05:00",
            "osType": "Linux",
            "linkedServiceName": "LinkedService-SampleData",
            "additionalLinkedServiceNames": [ "otherLinkedServiceName1", "otherLinkedServiceName2" ]
        }
    }
}
```
In het bovenstaande voorbeeld vertegenwoordigen otherLinkedServiceName1 en andere LinkedServiceName2 gekoppelde services waarvan de definities referenties bevatten die het HDInsight-cluster nodig heeft om toegang te krijgen tot alternatieve opslagaccounts.

## <a name="slices---faq"></a>Segmenten - veelgestelde vragen
### <a name="why-are-my-input-slices-not-in-ready-state"></a>Waarom staan mijn invoersegmenten niet in de status Gereed?
Een veelvoorkomende fout is niet **extern** eigendom op **true** instellen op de invoergegevensset wanneer de invoergegevens zich buiten de gegevensfabriek begeven (niet geproduceerd door de gegevensfabriek).

In het volgende voorbeeld hoeft u alleen **extern** in te stellen op de **true-gegevensset1**.  

**DataFactory1** Pijplijn 1: gegevensset1 -> activiteit1 -> dataset2 -> activiteit2 -> dataset3 Pipeline 2: dataset3-> activity3 -> dataset4

Als u een andere gegevensfabriek hebt met een pijplijn die gegevensset 4 (geproduceerd door pijplijn 2 in gegevensfabriek 1) neemt, markeert u gegevensset4 als een externe gegevensset omdat de gegevensset wordt geproduceerd door een andere gegevensfabriek (DataFactory1, niet DataFactory2).  

**DataFactory2**    
Pijplijn 1: gegevensset4->activiteit4->dataset5

Als de externe eigenschap goed is ingesteld, controleert u of de invoergegevens bestaan op de locatie die is opgegeven in de definitie van invoergegevensset.

### <a name="how-to-run-a-slice-at-another-time-than-midnight-when-the-slice-is-being-produced-daily"></a>Hoe voer je een segment op een ander tijdstip uit dan middernacht wanneer het segment dagelijks wordt geproduceerd?
Gebruik de eigenschap **offset** om het tijdstip op te geven waarop u het segment wilt produceren. Zie [sectie Beschikbaarheid van gegevenssets](data-factory-create-datasets.md#dataset-availability) voor meer informatie over deze eigenschap. Hier is een snel voorbeeld:

```json
"availability":
{
    "frequency": "Day",
    "interval": 1,
    "offset": "06:00:00"
}
```
Dagelijkse segmenten beginnen om **6 uur** in plaats van de standaard middernacht.     

### <a name="how-can-i-rerun-a-slice"></a>Hoe kan ik een segment opnieuw uitvoeren?
U een segment op een van de volgende manieren opnieuw uitvoeren:

* Gebruik App controleren en beheren om een activiteitenvenster of -segment opnieuw uit te voeren. Zie [Geselecteerde activiteitsvensters opnieuw uitvoeren](data-factory-monitor-manage-app.md#perform-batch-actions) voor instructies.   
* Klik **op Uitvoeren** op de opdrachtbalk op het DATA **SLICE-blad** voor het segment in de Azure-portal.
* Voer de cmdlet **Set-AzDataFactorySliceStatus** uit met status ingesteld op Wachten **op** het segment.   

    ```powershell
    Set-AzDataFactorySliceStatus -Status Waiting -ResourceGroupName $ResourceGroup -DataFactoryName $df -TableName $table -StartDateTime "02/26/2015 19:00:00" -EndDateTime "02/26/2015 20:00:00"
    ```
  Zie [Set-AzDataFactorySliceStatus][set-azure-datafactory-slice-status] voor meer informatie over de cmdlet.

### <a name="how-long-did-it-take-to-process-a-slice"></a>Hoe lang duurde het om een segment te verwerken?
Gebruik Activity Window Explorer in Monitor & App beheren om te weten hoe lang het duurde om een gegevenssegment te verwerken. Zie [Activiteitsvensterverkenner](data-factory-monitor-manage-app.md#activity-window-explorer) voor meer informatie.

U het volgende ook doen in de Azure-portal:  

1. Klik op de tegel **Gegevenssets** op het **DATA FACTORY-blad** voor uw gegevensfabriek.
2. Klik op de specifieke gegevensset op het **gegevenssetblad.**
3. Selecteer het segment waarin u geïnteresseerd bent in de lijst **Recente segmenten** op het **tafelblad.**
4. Klik op de activiteitsrun in de lijst **Activiteitsuitvoeringen** op het **DATA SLICE-blad.**
5. Klik op de tegel **Eigenschappen** op het blad DETAILS VAN ACTIVITEIT **RUN.**
6. U moet het veld **DUUR** met een waarde zien. Deze waarde is de tijd die nodig is om het segment te verwerken.   

### <a name="how-to-stop-a-running-slice"></a>Hoe een lopend segment te stoppen?
Als u wilt voorkomen dat de pijplijn wordt uitgevoerd, u de cmdlet [Suspend-AzDataFactoryPipeline](/powershell/module/az.datafactory/suspend-azdatafactorypipeline) gebruiken. Als u de pijplijn momenteel opschort, worden de slice-uitvoeringen die aan de gang zijn, niet gestopt. Zodra de lopende uitvoeringen zijn voltooid, wordt er geen extra slice opgehaald.

Als u werkelijk alle uitvoeringen onmiddellijk wilt tegenhouden, zou de enige manier zijn om de pijpleiding te verwijderen en het opnieuw te creëren. Als u ervoor kiest de pijplijn te verwijderen, hoeft u geen tabellen en gekoppelde services te verwijderen die door de pijplijn worden gebruikt.

[create-factory-using-dotnet-sdk]: data-factory-create-data-factories-programmatically.md
[msdn-class-library-reference]: /dotnet/api/microsoft.azure.management.datafactories.models
[msdn-rest-api-reference]: /rest/api/datafactory/

[adf-powershell-reference]: /powershell/module/az.datafactory/
[azure-portal]: https://portal.azure.com
[set-azure-datafactory-slice-status]: /powershell/module/az.datafactory/set-Azdatafactoryslicestatus

[adf-pricing-details]: https://go.microsoft.com/fwlink/?LinkId=517777
[hdinsight-supported-regions]: https://azure.microsoft.com/pricing/details/hdinsight/
[hdinsight-alternate-storage]: https://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx
[hdinsight-alternate-storage-2]: https://blogs.msdn.com/b/cindygross/archive/2014/05/05/use-additional-storage-accounts-with-hdinsight-hive.aspx
