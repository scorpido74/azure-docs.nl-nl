---
title: Spark-programma's aanroepen vanuit Azure Data Factory
description: Meer informatie over het aanroepen van Spark-programma's uit een Azure-gegevensfabriek met behulp van de activiteit MapReduce.
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
ms.openlocfilehash: ce5fb014c7d954b3e8430a86430c6a666adff204
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75969231"
---
# <a name="invoke-spark-programs-from-azure-data-factory-pipelines"></a>Spark-programma's aanroepen vanuit Azure Data Factory-pijplijnen

> [!div class="op_single_selector" title1="Transformatieactiviteiten"]
> * [Hive activiteit](data-factory-hive-activity.md)
> * [Varkensactiviteit](data-factory-pig-activity.md)
> * [Activiteit verkleinen](data-factory-map-reduce.md)
> * [Hadoop Streaming activiteit](data-factory-hadoop-streaming-activity.md)
> * [Vonkactiviteit](data-factory-spark.md)
> * [Machine Learning Batch Execution-activiteit](data-factory-azure-ml-batch-execution-activity.md)
> * [Activiteit machine learning-updatebron](data-factory-azure-ml-update-resource-activity.md)
> * [Opgeslagen procedureactiviteit](data-factory-stored-proc-activity.md)
> * [Data Lake Analytics U-SQL-activiteit](data-factory-usql-activity.md)
> * [.NET aangepaste activiteit](data-factory-use-custom-activities.md)

> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Azure Data Factory, die algemeen beschikbaar is. Zie [Gegevens transformeren met de Apache Spark-activiteit in Data Factory](../transform-data-using-spark.md)als u de huidige versie van de gegevensfabriek gebruikt.

## <a name="introduction"></a>Inleiding
De Spark-activiteit is een van de [activiteiten voor gegevenstransformatie](data-factory-data-transformation-activities.md) die worden ondersteund door Data Factory. Met deze activiteit wordt het opgegeven Spark-programma uitgevoerd op uw Spark-cluster in Azure HDInsight.

> [!IMPORTANT]
> - De Spark-activiteit biedt geen ondersteuning voor HDInsight Spark-clusters die Azure Data Lake Store als primaire opslag gebruiken.
> - De Spark-activiteit ondersteunt alleen bestaande (uw eigen) HDInsight Spark-clusters. Het ondersteunt geen on-demand HDInsight gekoppelde service.

## <a name="walkthrough-create-a-pipeline-with-a-spark-activity"></a>Walkthrough: Een pijplijn maken met een Spark-activiteit
Dit zijn de typische stappen om een pijplijn voor gegevensfabrieken te maken met een Spark-activiteit:

* Een gegevensfactory maken.
* Maak een azure storage-gekoppelde service om uw opslag die is gekoppeld aan uw HDInsight Spark-cluster te koppelen aan de gegevensfabriek.
* Maak een HDInsight-gekoppelde service om uw Spark-cluster in HDInsight te koppelen aan de gegevensfabriek.
* Maak een gegevensset die verwijst naar de gekoppelde opslagservice. Momenteel moet u een uitvoergegevensset opgeven voor een activiteit, zelfs als er geen uitvoer wordt geproduceerd.
* Maak een pijplijn met Spark-activiteit die verwijst naar de hdinsight-gekoppelde service die u hebt gemaakt. De activiteit is geconfigureerd met de gegevensset die u in de vorige stap hebt gemaakt als uitvoergegevensset. De uitvoergegevensset is wat de planning aanstuurt (per uur, per dag). Daarom moet u de uitvoergegevensset opgeven, ook al produceert de activiteit niet echt een uitvoer.

### <a name="prerequisites"></a>Vereisten
1. Maak een opslagaccount voor algemene doeleinden door de instructies te volgen in [Een opslagaccount maken](../../storage/common/storage-account-create.md).

1. Maak een Spark-cluster in HDInsight door de instructies in de zelfstudie [Een Spark-cluster maken in HDInsight](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md)te volgen. Koppel het opslagaccount dat u in stap 1 hebt gemaakt aan dit cluster.

1. Download en bekijk het Python-scriptbestand **test.py** zich op [https://adftutorialfiles.blob.core.windows.net/sparktutorial/test.py](https://adftutorialfiles.blob.core.windows.net/sparktutorial/test.py).

1. Upload **test.py** naar de **pyFiles-map** in de **adfsparkcontainer** in uw blob-opslag. Maak de container en de map als deze niet bestaan.

### <a name="create-a-data-factory"></a>Een gegevensfactory maken
Volg deze stappen om een data factory te maken:

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).

1. Selecteer **Nieuwe** > **data + Analytics** > **Data Factory**.

1. Voer **sparkdf**in op het blad Van de **nieuwe gegevensfabriek** onder **Naam**.

   > [!IMPORTANT]
   > De naam van de Azure-gegevensfactory moet wereldwijd uniek zijn. Als u de fout 'Naam SparkDF voor gegevensfabriek is niet beschikbaar' ziet, wijzigt u de naam van de gegevensfabriek. Gebruik bijvoorbeeld uw naamSparkDFdate en maak de gegevensfabriek opnieuw. Zie voor meer informatie over naamgevingsregels [Data Factory: naamgevingsregels](data-factory-naming-rules.md).

1. Selecteer onder **Abonnement** het Azure-abonnement waarvoor u de data factory wilt maken.

1. Selecteer een bestaande resourcegroep of maak een Azure-brongroep.

1. Schakel het selectievakje bij **Vastmaken aan dashboard** in.

1. Selecteer **Maken**.

   > [!IMPORTANT]
   > Als u instanties voor Gegevensfabriek wilt maken, moet u lid zijn van de rol [van de bijdrager gegevensfabriek](../../role-based-access-control/built-in-roles.md#data-factory-contributor) op het niveau van de abonnementsgroep/resourcegroep.

1. U ziet de gegevensfabriek zoals deze is gemaakt in het dashboard van de Azure-portal.

1. Nadat de data factory is gemaakt, ziet u de pagina **Data factory**. Hier wordt de inhoud van de data factory weergegeven. Als u de **fabriekspagina Gegevens** niet ziet, selecteert u de tegel voor uw gegevensfabriek op het dashboard.

    ![Blade Gegevensfactory](./media/data-factory-spark/data-factory-blade.png)

### <a name="create-linked-services"></a>Gekoppelde services maken
In deze stap maakt u twee gekoppelde services. De ene service koppelt uw Spark-cluster aan uw gegevensfabriek en de andere service koppelt uw opslag aan uw gegevensfabriek.

#### <a name="create-a-storage-linked-service"></a>Een gekoppelde Storage-service maken
In deze stap koppelt u uw opslagaccount aan uw data factory. Een gegevensset die u in een stap later in deze walkthrough maakt, verwijst naar deze gekoppelde service. De HDInsight gekoppelde service die u definieert in de volgende stap verwijst ook naar deze gekoppelde service.

1. Selecteer op het **fabrieksblad Gegevens** de optie **Auteur en implementeren**. De Data Factory Editor wordt weergegeven.

1. Selecteer **Nieuwe gegevensopslag** en kies **Azure Storage**.

   ![Nieuw gegevensarchief](./media/data-factory-spark/new-data-store-azure-storage-menu.png)

1. Het JSON-script dat u gebruikt om een gekoppelde opslagservice te maken, wordt weergegeven in de editor.

   ![AzureStorageLinkedService](./media/data-factory-build-your-first-pipeline-using-editor/azure-storage-linked-service.png)

1. Vervang **de accountnaam** en **de accountsleutel** door de naam en toegangssleutel van uw opslagaccount. Zie [Toegangssleutels voor opslagaccount beheren](../../storage/common/storage-account-keys-manage.md)voor meer informatie over het ophalen van uw opslagtoegangssleutel.

1. Als u de gekoppelde service wilt implementeren, selecteert u **Implementeren** op de opdrachtbalk. Nadat de gekoppelde service is geïmplementeerd, verdwijnt het venster Draft-1. U ziet **AzureStorageLinkedService** in de structuurweergave links.

#### <a name="create-an-hdinsight-linked-service"></a>Een gekoppelde HDInsight-service maken
In deze stap maakt u een HDInsight-gekoppelde service om uw HDInsight Spark-cluster te koppelen aan de gegevensfabriek. Het HDInsight-cluster wordt gebruikt om het Spark-programma uit te voeren dat is opgegeven in de Spark-activiteit van de pijplijn in dit voorbeeld.

1. Selecteer in de datafabriekeditor **meer** > **nieuwe compute** > **HDInsight-cluster**.

    ![Gekoppelde HDInsight-service maken](media/data-factory-spark/new-hdinsight-linked-service.png)

1. Kopieer het onderstaande codefragment en plak het in het venster Draft-1. Neem in de JSON-editor de volgende stappen:

    a. Geef de URI op voor het HDInsight Spark-cluster. Bijvoorbeeld: `https://<sparkclustername>.azurehdinsight.net/`.

    b. Geef de naam op van de gebruiker die toegang heeft tot het Spark-cluster.

    c. Geef het wachtwoord voor de gebruiker op.

    d. Geef de gekoppelde opslagservice op die is gekoppeld aan het HDInsight Spark-cluster. In dit voorbeeld is het AzureStorageLinkedService.

    ```json
    {
        "name": "HDInsightLinkedService",
        "properties": {
            "type": "HDInsight",
            "typeProperties": {
                "clusterUri": "https://<sparkclustername>.azurehdinsight.net/",
                "userName": "admin",
                "password": "**********",
                "linkedServiceName": "AzureStorageLinkedService"
            }
        }
    }
    ```

    > [!IMPORTANT]
    > - De Spark-activiteit biedt geen ondersteuning voor HDInsight Spark-clusters die Azure Data Lake Store als primaire opslag gebruiken.
    > - De Spark-activiteit ondersteunt alleen bestaande (uw eigen) HDInsight Spark-clusters. Het ondersteunt geen on-demand HDInsight gekoppelde service.

    Zie [HDInsight linked service](data-factory-compute-linked-services.md#azure-hdinsight-linked-service)voor meer informatie over de hdInsight-gekoppelde service.

1. Als u de gekoppelde service wilt implementeren, selecteert u **Implementeren** op de opdrachtbalk.

### <a name="create-the-output-dataset"></a>De uitvoergegevensset maken
De uitvoergegevensset is wat de planning aanstuurt (per uur, per dag). Daarom moet u een uitvoergegevensset opgeven voor de Spark-activiteit in de pijplijn, ook al produceert de activiteit geen uitvoer. Het opgeven van een invoergegevensset voor de activiteit is optioneel.

1. Selecteer in de datafabriekeditor **de** > optie Meer**nieuwe gegevensset** > Azure**Blob-opslag**.

1. Kopieer het onderstaande codefragment en plak het in het venster Draft-1. Het JSON-fragment definieert een gegevensset met de naam **OutputDataset**. Bovendien geeft u op dat de resultaten zijn opgeslagen in de blobcontainer genaamd **adfspark** en de map **genaamd pyFiles/output.** Zoals eerder vermeld, is deze gegevensset een dummy-gegevensset. Het Spark-programma in dit voorbeeld produceert geen uitvoer. De **sectie beschikbaarheid** geeft aan dat de uitvoergegevensset dagelijks wordt geproduceerd.

    ```json
    {
        "name": "OutputDataset",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "fileName": "sparkoutput.txt",
                "folderPath": "adfspark/pyFiles/output",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": "\t"
                }
            },
            "availability": {
                "frequency": "Day",
                "interval": 1
            }
        }
    }
    ```
1. Als u de gegevensset wilt implementeren, selecteert u **Implementeren** op de opdrachtbalk.


### <a name="create-a-pipeline"></a>Een pijplijn maken
In deze stap maakt u een pijplijn met een HDInsightSpark-activiteit. Op dit moment wordt de planning gebaseerd op de uitvoergegevensset. Daarom moet u ook een uitvoergegevensset maken als er tijdens de activiteit geen uitvoer wordt geproduceerd. Als er voor de activiteit geen invoer nodig is, kunt u het maken van de invoergegevensset overslaan. Daarom wordt in dit voorbeeld geen invoergegevensset opgegeven.

1. **Selecteer** > meer**nieuwe pijplijn**in de editor voor gegevensfabrieken .

1. Vervang het script in het venster Concept 1 door het volgende script:

    ```json
    {
        "name": "SparkPipeline",
        "properties": {
            "activities": [
                {
                    "type": "HDInsightSpark",
                    "typeProperties": {
                        "rootPath": "adfspark\\pyFiles",
                        "entryFilePath": "test.py",
                        "getDebugInfo": "Always"
                    },
                    "outputs": [
                        {
                            "name": "OutputDataset"
                        }
                    ],
                    "name": "MySparkActivity",
                    "linkedServiceName": "HDInsightLinkedService"
                }
            ],
            "start": "2017-02-05T00:00:00Z",
            "end": "2017-02-06T00:00:00Z"
        }
    }
    ```
    Houd rekening met de volgende punten:

    a. De **eigenschap type** is ingesteld op **HDInsightSpark.**

    b. De eigenschap **rootPath** is ingesteld op **\\adfspark pyFiles** waar adfspark de blobcontainer is en pyFiles de bestandsmap in die container. In dit voorbeeld is de blobopslag de opslag die is gekoppeld aan het Spark-cluster. U het bestand uploaden naar een ander opslagaccount. Als u dit doet, maakt u een door Opslag gekoppelde service om dat opslagaccount te koppelen aan de gegevensfabriek. Geef vervolgens de naam van de gekoppelde service op als waarde voor de eigenschap **sparkJobLinkedService.** Zie [Spark-activiteitseigenschappen](#spark-activity-properties)voor meer informatie over deze eigenschap en andere eigenschappen die worden ondersteund door de Spark-activiteit.

    c. De eigenschap **entryFilePath** is ingesteld op **test.py,** het Python-bestand.

    d. De eigenschap **getDebugInfo** is ingesteld op **Altijd,** wat betekent dat de logbestanden altijd worden gegenereerd (succes of mislukking).

    > [!IMPORTANT]
    > We raden u aan deze `Always` eigenschap niet in te stellen in een productieomgeving, tenzij u een probleem oplost.

    e. De sectie **uitvoer** heeft één uitvoergegevensset. U moet een uitvoergegevensset opgeven, zelfs als het Spark-programma geen uitvoer produceert. De uitvoergegevensset stuurt de planning voor de pijplijn aan (per uur, per dag).

    Zie de sectie [Spark-activiteitseigenschappen](#spark-activity-properties)voor meer informatie over de eigenschappen die worden ondersteund door de Spark-activiteit.

1. Als u de pijplijn wilt implementeren, selecteert u **Implementeren** op de opdrachtbalk.

### <a name="monitor-a-pipeline"></a>Een pijplijn bewaken
1. Selecteer op het **fabrieksblad Gegevens** de optie **Monitor & Beheren** om de controletoepassing op een ander tabblad te starten.

    ![De tegel Bewaking en beheer](media/data-factory-spark/monitor-and-manage-tile.png)

1. Wijzig het filter **Begintijd** bovenaan in **2/1/2017**en selecteer **Toepassen**.

1. Er wordt slechts één activiteitsvenster weergegeven omdat er slechts één dag is tussen de start (2017-02-01) en de eindtijden (2017-02-02) van de pijplijn. Controleer of het gegevenssegment zich in de status **Gereed** bevindt.

    ![De pijplijn bewaken](media/data-factory-spark/monitor-and-manage-app.png)

1. Selecteer in de lijst **Activiteitsvensters** een activiteitsrun om details hierover te bekijken. Als er een fout optreedt, ziet u details hierover in het rechterdeelvenster.

### <a name="verify-the-results"></a>De resultaten verifiëren

1. Start de Jupyter Notebook voor uw HDInsight Spark-cluster door naar [deze website](https://CLUSTERNAME.azurehdinsight.net/jupyter)te gaan. U ook een clusterdashboard voor uw HDInsight Spark-cluster openen en vervolgens het Jupyter-notitieblok starten.

1. Selecteer **Nieuw** > **PySpark** om een nieuw notitieblok te starten.

    ![Jupyter nieuwe notebook](media/data-factory-spark/jupyter-new-book.png)

1. Voer de volgende opdracht uit door de tekst te kopiëren en plakken en op Shift+Enter aan het einde van de tweede instructie te drukken:

    ```sql
    %%sql

    SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"
    ```
1. Controleer of u de gegevens van de hvac-tabel ziet.

    ![Jupyter query resultaten](media/data-factory-spark/jupyter-notebook-results.png)

<!-- Removed bookmark #run-a-hive-query-using-spark-sql since it doesn't exist in the target article -->
Zie de sectie [Voer een Spark SQL-query uit](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md)voor gedetailleerde instructies.

### <a name="troubleshooting"></a>Problemen oplossen
Omdat u getDebugInfo instelt op **Altijd,** ziet u een logsubmap in de map pyFiles in uw blobcontainer. Het logboekbestand in de logboekmap bevat aanvullende informatie. Dit logboekbestand is vooral handig wanneer er een fout optreedt. In een productieomgeving u deze instellen op **Mislukken.**

Ga voor verdere probleemoplossing de volgende stappen uit:


1. Ga naar `https://<CLUSTERNAME>.azurehdinsight.net/yarnui/hn/cluster`.

    ![YARN UI-toepassing](media/data-factory-spark/yarnui-application.png)

1. Selecteer **Logboeken** voor een van de runpogingen.

    ![Toepassingspagina](media/data-factory-spark/yarn-applications.png)

1. U ziet de volgende aanvullende foutgegevens op de logboekpagina:

    ![Logboekfout](media/data-factory-spark/yarnui-application-error.png)

In de volgende secties vindt u informatie over de entiteiten van de gegevensfabriek die spark-cluster- en Spark-activiteit in uw gegevensfabriek kunnen gebruiken.

## <a name="spark-activity-properties"></a>Eigenschappen van spark-activiteit
Hier is de voorbeeld-JSON-definitie van een pijplijn met een Spark-activiteit:

```json
{
    "name": "SparkPipeline",
    "properties": {
        "activities": [
            {
                "type": "HDInsightSpark",
                "typeProperties": {
                    "rootPath": "adfspark\\pyFiles",
                    "entryFilePath": "test.py",
                    "arguments": [ "arg1", "arg2" ],
                    "sparkConfig": {
                        "spark.python.worker.memory": "512m"
                    },
                    "getDebugInfo": "Always"
                },
                "outputs": [
                    {
                        "name": "OutputDataset"
                    }
                ],
                "name": "MySparkActivity",
                "description": "This activity invokes the Spark program",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2017-02-01T00:00:00Z",
        "end": "2017-02-02T00:00:00Z"
    }
}
```

In de volgende tabel worden de JSON-eigenschappen beschreven die worden gebruikt in de JSON-definitie.

| Eigenschap | Beschrijving | Vereist |
| -------- | ----------- | -------- |
| name | Naam van de activiteit in de pijplijn. | Ja |
| description | Tekst die beschrijft wat de activiteit doet. | Nee |
| type | Deze eigenschap moet worden ingesteld op HDInsightSpark. | Ja |
| linkedServiceName | Naam van de HDInsight gekoppelde service waarop het Spark-programma wordt uitgevoerd. | Ja |
| rootPath | De blobcontainer en map die het Spark-bestand bevat. De bestandsnaam is hoofdlettergevoelig. | Ja |
| entryFilePath | Relatief pad naar de hoofdmap van de Spark-code/-pakket. | Ja |
| Classname | De Java/Spark hoofdklasse van de toepassing. | Nee |
| Argumenten | Een lijst met opdrachtregelargumenten voor het Spark-programma. | Nee |
| proxyGebruiker | Het gebruikersaccount dat zich moet voordoen om het Spark-programma uit te voeren. | Nee |
| sparkConfig | Waarden opgeven voor de spark-configuratie-eigenschappen die worden weergegeven in [spark-configuratie: toepassingseigenschappen](https://spark.apache.org/docs/latest/configuration.html#available-properties). | Nee |
| getDebugInfo | Hiermee geeft u op wanneer de Spark-logboekbestanden worden gekopieerd naar de opslag die wordt gebruikt door het HDInsight-cluster (of) dat is opgegeven door sparkJobLinkedService. Toegestane waarden zijn Geen, Altijd of Mislukt. De standaardwaarde is Geen. | Nee |
| sparkJobLinkedService | De gekoppelde opslagservice met het Spark-taakbestand, afhankelijkheden en logboeken. Als u geen waarde voor deze eigenschap opgeeft, wordt de opslag gebruikt die is gekoppeld aan het HDInsight-cluster. | Nee |

## <a name="folder-structure"></a>Mapstructuur
De Spark-activiteit ondersteunt geen inline script zoals Pig en Hive activiteiten doen. Spark banen zijn ook meer uitbreidbaar dan Pig / Hive banen. Voor Spark-taken u meerdere afhankelijkheden bieden, zoals potpakketten (geplaatst in het javaCLASSPATH), Python-bestanden (geplaatst op het PYTHONPATH) en andere bestanden.

Maak de volgende mapstructuur in de blobopslag waarnaar wordt verwezen door de gekoppelde SERVICE HDInsight. Upload vervolgens afhankelijke bestanden naar de juiste submappen in de hoofdmap die wordt weergegeven door **entryFilePath**. Upload bijvoorbeeld Python-bestanden naar de submap van pyFiles en jarbestanden naar de submap van de potvan de hoofdmap. Bij runtime verwacht de service Data Factory de volgende mapstructuur in de blobopslag:

| Pad | Beschrijving | Vereist | Type |
| ---- | ----------- | -------- | ---- |
| . | Het hoofdpad van de Spark-taak in de gekoppelde opslagservice. | Ja | Map |
| &lt;gebruiker gedefinieerd&gt; | Het pad dat verwijst naar het invoerbestand van de spark-taak. | Ja | File |
| ./potten | Alle bestanden onder deze map worden geüpload en geplaatst op het Java-klassepad van het cluster. | Nee | Map |
| ./pyFiles | Alle bestanden onder deze map worden geüpload en op het PYTHONPATH van het cluster geplaatst. | Nee | Map |
| ./bestanden | Alle bestanden onder deze map worden geüpload en geplaatst op de werkmap executor. | Nee | Map |
| ./archieven | Alle bestanden onder deze map zijn niet gecomprimeerd. | Nee | Map |
| ./logboeken | De map waarin logboeken van het Spark-cluster worden opgeslagen.| Nee | Map |

Hier is een voorbeeld voor opslag dat twee Spark-taakbestanden bevat in de blob-opslag waarnaar wordt verwezen door de gekoppelde HDInsight-service:

```
SparkJob1
    main.jar
    files
        input1.txt
        input2.txt
    jars
        package1.jar
        package2.jar
    logs

SparkJob2
    main.py
    pyFiles
        scrip1.py
        script2.py
    logs
```
