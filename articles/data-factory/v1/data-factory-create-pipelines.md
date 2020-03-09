---
title: Pijp lijnen maken/plannen, keten activiteiten in Data Factory
description: Meer informatie over het maken van een gegevens pijplijn in Azure Data Factory voor het verplaatsen en transformeren van gegevens. Maak een gegevensgestuurde werk stroom voor het maken van gereed voor gebruik van informatie.
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
ms.openlocfilehash: f93bea240ee3f139c9be84199d116f9f3f231261
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78361430"
---
# <a name="pipelines-and-activities-in-azure-data-factory"></a>Pijp lijnen en activiteiten in Azure Data Factory
> [!div class="op_single_selector" title1="Selecteer de versie van Data Factory service die u gebruikt:"]
> * [Versie 1](data-factory-create-pipelines.md)
> * [Versie 2 (huidige versie)](../concepts-pipelines-activities.md)

> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Als u de huidige versie van de Data Factory-service gebruikt, raadpleegt u [pijp lijnen in v2](../concepts-pipelines-activities.md).

Dit artikel helpt u bij het begrijpen van pijp lijnen en activiteiten in Azure Data Factory en het gebruik ervan om end-to-end gegevensgestuurde werk stromen te maken voor uw gegevens verplaatsings-en gegevens verwerkings scenario's.

> [!NOTE]
> In dit artikel wordt ervan uitgegaan dat u de [Inleiding tot Azure Data Factory](data-factory-introduction.md)hebt door lopen. Als u geen praktijk ervaring hebt met het maken van gegevens fabrieken, kunt u dit artikel beter begrijpen met behulp van [zelf studie voor gegevens transformatie](data-factory-build-your-first-pipeline.md) en/of de [zelf studie](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) voor het verplaatsen van gegevens.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>Krijgt
Een data factory kan een of meer pijp lijnen hebben. Een pijp lijn is een logische groep activiteiten die samen een taak uitvoeren. De activiteiten in een pijp lijn bepalen acties die moeten worden uitgevoerd op uw gegevens. U kunt bijvoorbeeld een Kopieer activiteit gebruiken om gegevens van een on-premises SQL Server naar een Azure Blob Storage te kopiëren. Gebruik vervolgens een Hive-activiteit die een Hive-script uitvoert op een Azure HDInsight-cluster om gegevens uit de Blob-opslag te verwerken/transformeren om uitvoer gegevens te produceren. Gebruik ten slotte een tweede Kopieer activiteit om de uitvoer gegevens te kopiëren naar een Azure SQL Data Warehouse op de eerste waarvan business intelligence (BI) Reporting-oplossingen zijn gebouwd.

Een activiteit kan nul of meer invoer [gegevens sets](data-factory-create-datasets.md) hebben en een of meer uitvoer [gegevens sets](data-factory-create-datasets.md)produceren. In het volgende diagram ziet u de relatie tussen pijp lijn, activiteit en gegevensset in Data Factory:

![Relatie tussen pijp lijn, activiteit en gegevensset](media/data-factory-create-pipelines/relationship-pipeline-activity-dataset.png)

Met een pijp lijn kunt u activiteiten beheren als een set in plaats van elk afzonderlijk. U kunt bijvoorbeeld een pijp lijn implementeren, plannen, onderbreken en hervatten, in plaats van de activiteiten in de pijp lijn onafhankelijk te verwerken.

Data Factory ondersteunt twee soorten activiteiten: activiteiten voor gegevens verplaatsing en activiteiten voor gegevens transformatie. Elke activiteit kan nul of meer invoer [gegevens sets](data-factory-create-datasets.md) bevatten en een of meer uitvoer gegevens sets produceren.

Een invoer-gegevensset vertegenwoordigt de invoer voor een activiteit in de pijp lijn en een uitvoer gegevensset vertegenwoordigt de uitvoer voor de activiteit. Met gegevens sets wordt informatie in verschillende gegevens archieven geïdentificeerd, zoals tabellen, bestanden, mappen en documenten. Nadat u een gegevensset hebt gemaakt, kunt u deze gebruiken met activiteiten in een pijp lijn. Een gegevensset kan bijvoorbeeld een gegevensset voor invoer/uitvoer van een Kopieer activiteit of een HDInsightHive-activiteit zijn. Zie [gegevens sets in azure Data Factory](data-factory-create-datasets.md) artikel voor meer informatie over gegevens sets.

### <a name="data-movement-activities"></a>Activiteiten voor gegevens verplaatsing
Met de Kopieer activiteit in Data Factory worden gegevens gekopieerd van een brongegevens archief naar een Sink-gegevens archief. Data Factory ondersteunt de volgende gegevens archieven. Gegevens uit een wille keurige bron kunnen naar elke Sink worden geschreven. Klik op een gegevens Archief voor informatie over het kopiëren van gegevens naar en van dat archief.

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

> [!NOTE]
> Gegevens archieven met * kunnen on-premises of op Azure IaaS zijn, en u moet [Data Management Gateway](data-factory-data-management-gateway.md) installeren op een on-premises/Azure IaaS-computer.

Zie het artikel activiteiten voor [gegevens verplaatsing](data-factory-data-movement-activities.md) voor meer informatie.

### <a name="data-transformation-activities"></a>Activiteiten voor gegevens transformatie
[!INCLUDE [data-factory-transformation-activities](../../../includes/data-factory-transformation-activities.md)]

Zie het artikel [Data Transformation activities](data-factory-data-transformation-activities.md) (Engelstalig) voor meer informatie.

### <a name="custom-net-activities"></a>Aangepaste .NET-activiteiten
Als u gegevens wilt verplaatsen naar/van een gegevens archief dat niet wordt ondersteund door de Kopieer activiteit, of als u gegevens transformeert met uw eigen logica, maakt u een **aangepaste .net-activiteit**. Zie [aangepaste activiteiten gebruiken in een Azure Data Factory pijp lijn](data-factory-use-custom-activities.md)voor meer informatie over het maken en gebruiken van een aangepaste activiteit.

## <a name="schedule-pipelines"></a>Pijp lijnen plannen
Een pijp lijn is alleen actief tussen de **begin** tijd en **eind** tijd. Deze wordt niet uitgevoerd vóór de begin tijd of na de eind tijd. Als de pijp lijn is onderbroken, wordt deze niet uitgevoerd, ongeacht de begin-en eind tijd. Om een pijp lijn uit te voeren, mag deze niet worden onderbroken. Zie [planning en uitvoering](data-factory-scheduling-and-execution.md) om inzicht te krijgen in de werking van planning en uitvoering in azure Data Factory.

## <a name="pipeline-json"></a>JSON van pijp lijn
Laten we eens kijken hoe een pijp lijn is gedefinieerd in de JSON-indeling. De algemene structuur voor een pijp lijn ziet er als volgt uit:

```json
{
    "name": "PipelineName",
    "properties":
    {
        "description" : "pipeline description",
        "activities":
        [

        ],
        "start": "<start date-time>",
        "end": "<end date-time>",
        "isPaused": true/false,
        "pipelineMode": "scheduled/onetime",
        "expirationTime": "15.00:00:00",
        "datasets":
        [
        ]
    }
}
```

| Tag | Beschrijvingen | Vereist |
| --- | --- | --- |
| Naam |De naam van de pijp lijn. Geef een naam op die de actie vertegenwoordigt die de pijp lijn uitvoert. <br/><ul><li>Maximum aantal tekens: 260</li><li>Moet beginnen met een letter nummer of een onderstrepings teken (\_)</li><li>De volgende tekens zijn niet toegestaan: '. ', ' + ', '? ', '/', ' < ', ' > ', '\*', '% ', ' & ', ': ', '\\'</li></ul> |Klikt |
| Beschrijvingen | Geef de tekst op waarmee wordt beschreven waarvoor de pijp lijn wordt gebruikt. |Klikt |
| activiteiten | In het gedeelte **activiteiten** kunnen een of meer activiteiten worden gedefinieerd. Zie de volgende sectie voor meer informatie over het JSON-element activities. | Klikt |
| starten | De begin datum/-tijd voor de pijp lijn. Moet de [ISO-indeling](https://en.wikipedia.org/wiki/ISO_8601)hebben. Bijvoorbeeld: `2016-10-14T16:32:41Z`. <br/><br/>Het is mogelijk om een lokale tijd op te geven, bijvoorbeeld een EST-tijd. Hier volgt een voor beeld: `2016-02-27T06:00:00-05:00`'. Dit is 6 AM EST.<br/><br/>Met de eigenschappen Start en end geeft u de actieve periode voor de pijp lijn op. Uitvoer segmenten worden alleen geproduceerd in deze actieve periode. |Geen<br/><br/>Als u een waarde voor de eigenschap end opgeeft, moet u een waarde voor de eigenschap Start opgeven.<br/><br/>De begin-en eind tijd kunnen beide leeg zijn om een pijp lijn te maken. U moet beide waarden opgeven om een actieve periode in te stellen voor het uitvoeren van de pijp lijn. Als u geen begin-en eind tijden opgeeft bij het maken van een pijp lijn, kunt u ze later instellen met de cmdlet Set-AzDataFactoryPipelineActivePeriod. |
| endsystemen | Eind datum-tijd voor de pijp lijn. Indien opgegeven moet de ISO-indeling hebben. Bijvoorbeeld: `2016-10-14T17:32:41Z` <br/><br/>Het is mogelijk om een lokale tijd op te geven, bijvoorbeeld een EST-tijd. Hier volgt een voor beeld: `2016-02-27T06:00:00-05:00`, dat is 6 uur EST.<br/><br/>Als u de pijp lijn voor onbepaalde tijd wilt uitvoeren, geeft u 9999-09-09 op als de waarde voor de eigenschap End. <br/><br/> Een pijp lijn is alleen actief tussen de begin tijd en eind tijd. Deze wordt niet uitgevoerd vóór de begin tijd of na de eind tijd. Als de pijp lijn is onderbroken, wordt deze niet uitgevoerd, ongeacht de begin-en eind tijd. Om een pijp lijn uit te voeren, mag deze niet worden onderbroken. Zie [planning en uitvoering](data-factory-scheduling-and-execution.md) om inzicht te krijgen in de werking van planning en uitvoering in azure Data Factory. |Geen <br/><br/>Als u een waarde voor de eigenschap Start opgeeft, moet u een waarde voor de eigenschap End opgeven.<br/><br/>Zie opmerkingen voor de eigenschap **Start** . |
| isPaused | Als deze eigenschap is ingesteld op True, wordt de pijp lijn niet uitgevoerd. De status is onderbroken. Standaard waarde = false. U kunt deze eigenschap gebruiken om een pijp lijn in of uit te scha kelen. |Geen |
| pipelineMode | De methode voor het plannen van uitvoeringen voor de pijp lijn. Toegestane waarden zijn: gepland (standaard), eenmalige.<br/><br/>' Gepland ' geeft aan dat de pijp lijn wordt uitgevoerd op een opgegeven tijds interval op basis van de actieve periode (begin-en eind tijd). ' Eenmalige ' geeft aan dat de pijp lijn slechts één keer wordt uitgevoerd. Eenmalige-pijp lijnen kunnen op dit moment niet worden gewijzigd of bijgewerkt. Zie [eenmalige-pijp lijn](#onetime-pipeline) voor meer informatie over de instelling van eenmalige. |Geen |
| ExpirationTime | De tijds duur na het maken van de [eenmalige pijp lijn](#onetime-pipeline) die geldig is en die moet worden ingericht. Als het geen actieve, mislukte of uitgevoerde uitvoeringen heeft, wordt de pijp lijn automatisch verwijderd zodra de verloop tijd is bereikt. De standaard waarde: `"expirationTime": "3.00:00:00"`|Geen |
| sets |Lijst met gegevens sets die moeten worden gebruikt door activiteiten die in de pijp lijn zijn gedefinieerd. Deze eigenschap kan worden gebruikt om gegevens sets te definiëren die specifiek zijn voor deze pijp lijn en die niet zijn gedefinieerd in de data factory. Gegevens sets die in deze pijp lijn zijn gedefinieerd, kunnen alleen worden gebruikt door deze pijp lijn en kunnen niet worden gedeeld. Zie [bereik gegevens sets](data-factory-create-datasets.md#scoped-datasets) voor meer informatie. |Geen |

## <a name="activity-json"></a>JSON van activiteit
In het gedeelte **activiteiten** kunnen een of meer activiteiten worden gedefinieerd. Elke activiteit heeft de volgende structuur op het hoogste niveau:

```json
{
    "name": "ActivityName",
    "description": "description",
    "type": "<ActivityType>",
    "inputs": "[]",
    "outputs": "[]",
    "linkedServiceName": "MyLinkedService",
    "typeProperties":
    {

    },
    "policy":
    {
    },
    "scheduler":
    {
    }
}
```

De volgende tabel beschrijft de eigenschappen in de JSON-definitie van de activiteit:

| Tag | Beschrijvingen | Vereist |
| --- | --- | --- |
| Naam | De naam van de activiteit. Geef een naam op die de actie vertegenwoordigt die door de activiteit wordt uitgevoerd. <br/><ul><li>Maximum aantal tekens: 260</li><li>Moet beginnen met een letter nummer of een onderstrepings teken (\_)</li><li>De volgende tekens zijn niet toegestaan: '. ', ' + ', '? ', '/', ' < ', ' > ', ' * ', '% ', ' & ', ': ', '\\'</li></ul> |Klikt |
| Beschrijvingen | Tekst die beschrijft wat de activiteit is of wordt gebruikt voor |Klikt |
| voert | Het type van de activiteit. Zie de secties [activiteiten voor gegevens verplaatsing](#data-movement-activities) en [activiteiten voor gegevens transformatie](#data-transformation-activities) voor verschillende typen activiteiten. |Klikt |
| invoer |Invoer tabellen die worden gebruikt door de activiteit<br/><br/>`// one input table`<br/>`"inputs":  [ { "name": "inputtable1"  } ],`<br/><br/>`// two input tables` <br/>`"inputs":  [ { "name": "inputtable1"  }, { "name": "inputtable2"  } ],` |Klikt |
| uitvoer |Uitvoer tabellen die worden gebruikt door de activiteit.<br/><br/>`// one output table`<br/>`"outputs":  [ { "name": "outputtable1" } ],`<br/><br/>`//two output tables`<br/>`"outputs":  [ { "name": "outputtable1" }, { "name": "outputtable2" }  ],` |Klikt |
| linkedServiceName |De naam van de gekoppelde service die door de activiteit wordt gebruikt. <br/><br/>Voor een activiteit kan het nodig zijn dat u de gekoppelde service specificeert die is gekoppeld aan de vereiste Compute-omgeving. |Ja voor HDInsight-activiteit en Azure Machine Learning-batch Score activiteit <br/><br/>Nee voor alle anderen |
| typeProperties |De eigenschappen in de sectie **typeProperties** zijn afhankelijk van het type van de activiteit. Als u type-eigenschappen voor een activiteit wilt weer geven, klikt u op koppelingen naar de activiteit in de vorige sectie. | Geen |
| verslaggev |Beleids regels die van invloed zijn op het runtime gedrag van de activiteit. Als deze niet is opgegeven, wordt standaard beleid gebruikt. |Geen |
| Scheduler | de eigenschap scheduler wordt gebruikt voor het definiëren van de gewenste planning voor de activiteit. De subeigenschappen zijn hetzelfde als die in de [eigenschap Beschik baarheid in een gegevensset](data-factory-create-datasets.md#dataset-availability). |Geen |

### <a name="policies"></a>restrictie
Beleids regels zijn van invloed op het runtime gedrag van een activiteit, in het bijzonder wanneer het segment van een tabel wordt verwerkt. De volgende tabel bevat de details.

| Eigenschap | Toegestane waarden | Standaard waarde | Beschrijvingen |
| --- | --- | --- | --- |
| gelijktijdigheid |Geheeltallige <br/><br/>Maximum waarde: 10 |i |Aantal gelijktijdige uitvoeringen van de activiteit.<br/><br/>Hiermee wordt het aantal uitvoeringen van parallelle activiteit bepaald dat op verschillende segmenten kan plaatsvinden. Als een activiteit bijvoorbeeld een grote set beschik bare gegevens moet door lopen, kan de gegevens verwerking worden versneld met een grotere gelijktijdige waarde. |
| executionPriorityOrder |NewestFirst<br/><br/>OldestFirst |OldestFirst |Bepaalt de volg orde van de gegevens segmenten die worden verwerkt.<br/><br/>Als u bijvoorbeeld 2 segmenten hebt (één op 4 p.m. en een andere op tot 17:00 uur), en beide in behandeling zijn. Als u de executionPriorityOrder instelt op NewestFirst, wordt het segment op 5 PM eerst verwerkt. Op dezelfde manier als u de executionPriorityORder instelt op OldestFIrst, wordt het segment op 4 uur verwerkt. |
| voeren |Geheeltallige<br/><br/>De maximale waarde mag 10 zijn |0,3 |Aantal nieuwe pogingen voordat de gegevens verwerking voor het segment als mislukt wordt gemarkeerd. Voor het uitvoeren van de activiteit voor een gegevens segment wordt opnieuw geprobeerd tot het opgegeven aantal nieuwe pogingen. De nieuwe poging wordt zo snel mogelijk na de fout uitgevoerd. |
| out |Duur |00:00:00 |Time-out voor de activiteit. Voor beeld: 00:10:00 (impliceert een time-out van 10 minuten)<br/><br/>Als er geen waarde is opgegeven of 0, is de time-out oneindig.<br/><br/>Als de verwerkings tijd van de gegevens op een segment de time-outwaarde overschrijdt, wordt deze geannuleerd en probeert het systeem de verwerking opnieuw uit te voeren. Het aantal nieuwe pogingen is afhankelijk van de eigenschap retry. Als er een time-out optreedt, wordt de status ingesteld op out. |
| spoedig |Duur |00:00:00 |Geef de vertraging op voordat de gegevens verwerking van het segment wordt gestart.<br/><br/>De uitvoering van activiteit voor een gegevens segment wordt gestart nadat de vertraging voorbij de verwachte uitvoerings tijd ligt.<br/><br/>Voor beeld: 00:10:00 (impliceert vertraging van 10 minuten) |
| longRetry |Geheeltallige<br/><br/>Maximum waarde: 10 |i |Het aantal lange nieuwe pogingen voordat de segment uitvoering is mislukt.<br/><br/>longRetry-pogingen zijn gespatieerd door longRetryInterval. Als u dus een tijd tussen nieuwe pogingen wilt opgeven, gebruikt u longRetry. Als zowel een nieuwe poging als een longRetry wordt opgegeven, bevat elke longRetry-poging nieuwe pogingen en het maximum aantal pogingen * longRetry.<br/><br/>Als we bijvoorbeeld de volgende instellingen in het activiteiten beleid hebben:<br/>Opnieuw proberen: 3<br/>longRetry: 2<br/>longRetryInterval: 01:00:00<br/><br/>Stel dat er slechts één segment moet worden uitgevoerd (status is in afwachting) en dat de uitvoering van de activiteit elke keer mislukt. In eerste instantie zou er drie opeenvolgende uitvoerings pogingen kunnen worden uitgevoerd. Na elke poging zou de segment status opnieuw proberen. Nadat de eerste 3 pogingen zijn voltooid, wordt de segment status LongRetry.<br/><br/>Na een uur (dat wil zeggen longRetryInteval waarde), is er een andere set van drie opeenvolgende uitvoerings pogingen. Daarna zou de status van het segment mislukken en worden er geen nieuwe pogingen gedaan. Daarom zijn er in totaal 6 pogingen gedaan.<br/><br/>Als de uitvoering is geslaagd, zou de segment status gereed zijn en worden er geen nieuwe pogingen meer geprobeerd.<br/><br/>longRetry kan worden gebruikt in situaties waar afhankelijke gegevens op niet-deterministische tijden arriveren of de algehele omgeving Flaky is waaronder gegevens verwerking plaatsvindt. In dergelijke gevallen is het mogelijk dat er een nieuwe poging wordt gedaan om het opnieuw te proberen na een tijds interval, waardoor de gewenste uitvoer wordt bereikt.<br/><br/>Woord van voorzichtig: Stel geen hoge waarden in voor longRetry of longRetryInterval. Een hogere waarde impliceert meestal andere systeem problemen. |
| longRetryInterval |Duur |00:00:00 |De vertraging tussen nieuwe pogingen |

## <a name="sample-copy-pipeline"></a>Voor beeld van een Kopieer pijplijn
In de volgende voorbeeld pijplijn ziet u één activiteit van het type **copy** in de sectie **activities** . In dit voor beeld kopieert de [Kopieer activiteit](data-factory-data-movement-activities.md) gegevens uit een Azure Blob-opslag naar een Azure SQL database.

```json
{
  "name": "CopyPipeline",
  "properties": {
    "description": "Copy data from a blob to Azure SQL table",
    "activities": [
      {
        "name": "CopyFromBlobToSQL",
        "type": "Copy",
        "inputs": [
          {
            "name": "InputDataset"
          }
        ],
        "outputs": [
          {
            "name": "OutputDataset"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "SqlSink",
            "writeBatchSize": 10000,
            "writeBatchTimeout": "60:00:00"
          }
        },
        "Policy": {
          "concurrency": 1,
          "executionPriorityOrder": "NewestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
    ],
    "start": "2016-07-12T00:00:00Z",
    "end": "2016-07-13T00:00:00Z"
  }
}
```

Houd rekening met de volgende punten:

* In het gedeelte activiteiten is er slechts één activiteit waarvan het **type** is ingesteld op **kopiëren**.
* De invoer voor de activiteit is ingesteld op **input dataset** en de uitvoer voor de activiteit is ingesteld op **output dataset**. Zie het artikel [gegevens sets](data-factory-create-datasets.md) voor het definiëren van gegevens sets in JSON.
* In de sectie **typeProperties** is **BlobSource** opgegeven als bron type en **SqlSink** is opgegeven als het sink-type. Klik in de sectie [activiteiten voor gegevens verplaatsing](#data-movement-activities) op het gegevens archief dat u wilt gebruiken als bron of sink voor meer informatie over het verplaatsen van gegevens naar/van het gegevens archief.

Zie [zelf studie: gegevens kopiëren van Blob Storage naar SQL database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)voor een volledig overzicht van het maken van deze pijp lijn.

## <a name="sample-transformation-pipeline"></a>Voorbeeld transformatie pijplijn
De volgende voorbeeld pijplijn bevat een activiteit van het type **HDInsightHive** in de sectie **activities** . In dit voor beeld transformeert de [HDInsight Hive-activiteit](data-factory-hive-activity.md) gegevens van een Azure Blob-opslag door een Hive-script bestand uit te voeren op een Azure HDInsight Hadoop cluster.

```json
{
    "name": "TransformPipeline",
    "properties": {
        "description": "My first Azure Data Factory pipeline",
        "activities": [
            {
                "type": "HDInsightHive",
                "typeProperties": {
                    "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                    "scriptLinkedService": "AzureStorageLinkedService",
                    "defines": {
                        "inputtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/inputdata",
                        "partitionedtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/partitioneddata"
                    }
                },
                "inputs": [
                    {
                        "name": "AzureBlobInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "policy": {
                    "concurrency": 1,
                    "retry": 3
                },
                "scheduler": {
                    "frequency": "Month",
                    "interval": 1
                },
                "name": "RunSampleHiveActivity",
                "linkedServiceName": "HDInsightOnDemandLinkedService"
            }
        ],
        "start": "2016-04-01T00:00:00Z",
        "end": "2016-04-02T00:00:00Z",
        "isPaused": false
    }
}
```

Houd rekening met de volgende punten:

* In het gedeelte activiteiten is er slechts één activiteit waarvan het **type** is ingesteld op **HDInsightHive**.
* Het Hive-script bestand **partitionweblogs. HQL**wordt opgeslagen in het Azure-opslag account (opgegeven door de scriptLinkedService, de naam **AzureStorageLinkedService**) en in de map **script** van de container **adfgetstarted**.
* De sectie `defines` wordt gebruikt om de runtime-instellingen op te geven die worden door gegeven aan het Hive-script als Hive-configuratie waarden (bijvoorbeeld `${hiveconf:inputtable}`, `${hiveconf:partitionedtable}`).

De sectie **typeProperties** verschilt voor elke transformatie activiteit. Voor meer informatie over type-eigenschappen die worden ondersteund voor een transformatie activiteit, klikt u op de activiteit trans formatie in de tabel [gegevens transformatie activiteiten](#data-transformation-activities) .

Zie [zelf studie: uw eerste pijp lijn maken om gegevens te verwerken met een Hadoop-cluster](data-factory-build-your-first-pipeline.md)voor een volledig overzicht van het maken van deze pijp lijn.

## <a name="multiple-activities-in-a-pipeline"></a>Meerdere activiteiten in een pijp lijn
De voor gaande twee voorbeeld pijplijnen bevatten slechts één activiteit. U kunt meer dan één activiteit in een pijp lijn hebben.

Als u meerdere activiteiten in een pijp lijn hebt en de uitvoer van een activiteit geen invoer is van een andere activiteit, kunnen de activiteiten parallel worden uitgevoerd als de invoer gegevens segmenten voor de activiteiten gereed zijn.

U kunt twee activiteiten koppelen door de uitvoer gegevensset van een activiteit te hebben als de invoer gegevensset van de andere activiteit. De tweede activiteit wordt alleen uitgevoerd als de eerste wordt voltooid.

![De activiteiten in dezelfde pijp lijn koppelen](./media/data-factory-create-pipelines/chaining-one-pipeline.png)

In dit voor beeld heeft de pijp lijn twee activiteiten: Activiteit1 en Activiteit2. De Activiteit1 neemt Dataset1 als invoer en produceert een uitvoer Dataset2. De activiteit neemt Dataset2 als invoer en produceert een uitvoer Dataset3. Omdat de uitvoer van Activiteit1 (Dataset2) de invoer van Activiteit2 is, wordt de Activiteit2 alleen uitgevoerd nadat de activiteit is voltooid en wordt het Dataset2 segment gegenereerd. Als de Activiteit1 om een of andere reden mislukt en het Dataset2 segment niet produceert, wordt de activiteit 2 niet uitgevoerd voor dat segment (bijvoorbeeld: 9 AM tot 10 uur).

U kunt ook activiteiten koppelen die zich in verschillende pijp lijnen bevinden.

![Activiteiten in twee pijp lijnen koppelen](./media/data-factory-create-pipelines/chaining-two-pipelines.png)

In dit voor beeld heeft Pipeline1 slechts één activiteit die Dataset1 als invoer neemt en Dataset2 als uitvoer produceert. De Pipeline2 heeft ook maar één activiteit die Dataset2 als invoer en Dataset3 als uitvoer neemt.

Zie [planning en uitvoering](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline)voor meer informatie.
## <a name="create-and-monitor-pipelines"></a>Pijp lijnen maken en bewaken
U kunt pijp lijnen maken met behulp van een van deze hulpprogram ma's of Sdk's.

- Wizard kopiëren
- Visual Studio
- Azure PowerShell
- Azure Resource Manager sjabloon
- REST API
- .NET-API

Raadpleeg de volgende zelf studies voor stapsgewijze instructies voor het maken van pijp lijnen met behulp van een van deze hulpprogram ma's of Sdk's.

- [Een pijp lijn bouwen met een activiteit voor gegevens transformatie](data-factory-build-your-first-pipeline.md)
- [Een pijp lijn bouwen met een activiteit voor gegevens verplaatsing](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)

Zodra een pijp lijn is gemaakt/geïmplementeerd, kunt u uw pijp lijnen beheren en bewaken met behulp van de Azure Portal-Blades of de app controleren en beheren. Zie de volgende onderwerpen voor stapsgewijze instructies.

- [Bewaak en beheer pijp lijnen met behulp van Azure Portal-Blades](data-factory-monitor-manage-pipelines.md).
- [Pijp lijnen bewaken en beheren met behulp van de app controleren en beheren](data-factory-monitor-manage-app.md)

## <a name="onetime-pipeline"></a>Eenmalige-pijp lijn
U kunt een pijp lijn maken en plannen die regel matig wordt uitgevoerd (bijvoorbeeld elk uur of dagelijks) binnen de begin-en eind tijd die u in de pijplijn definitie opgeeft. Zie plannings activiteiten voor meer informatie. U kunt ook een pijp lijn maken die slechts één keer wordt uitgevoerd. Als u dit wilt doen, stelt u de eigenschap **pipelineMode** in de pijplijn definitie in op **eenmalige** , zoals weer gegeven in de volgende JSON-voor beeld. De standaard waarde voor deze eigenschap is **gepland**.

```json
{
    "name": "CopyPipeline",
    "properties": {
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource",
                        "recursive": false
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "InputDataset"
                    }
                ],
                "outputs": [
                    {
                        "name": "OutputDataset"
                    }
                ],
                "name": "CopyActivity-0"
            }
        ],
        "pipelineMode": "OneTime"
    }
}
```

Let op het volgende:

* De **begin** -en **eind** tijden voor de pijp lijn zijn niet opgegeven.
* De **Beschik baarheid** van invoer-en uitvoer gegevens sets wordt opgegeven (**frequentie** en **interval**), zelfs als Data Factory geen gebruik maakt van de waarden.
* In de diagram weergave worden niet de eenmalige pijp lijnen weer gegeven. Dit gedrag is inherent aan het ontwerp.
* Eenmalige pijp lijnen kunnen niet worden bijgewerkt. U kunt een eenmalige pijp lijn klonen, de naam ervan wijzigen, eigenschappen bijwerken en deze implementeren om er een te maken.

## <a name="next-steps"></a>Volgende stappen
- Zie het artikel [gegevens sets maken](data-factory-create-datasets.md) voor meer informatie over gegevens sets.
- Zie [planning en uitvoering in azure Data Factory](data-factory-scheduling-and-execution.md) artikel voor meer informatie over hoe pijp lijnen worden gepland en uitgevoerd.
