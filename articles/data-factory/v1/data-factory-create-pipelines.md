---
title: Pijplijnen maken/plannen, ketenactiviteiten in gegevensfabriek
description: Leer een gegevenspijplijn maken in Azure Data Factory om gegevens te verplaatsen en te transformeren. Maak een datagestuurde workflow om gebruiksklare informatie te produceren.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281520"
---
# <a name="pipelines-and-activities-in-azure-data-factory"></a>Pijplijnen en activiteiten in Azure Data Factory
> [!div class="op_single_selector" title1="Selecteer de versie van de datafabriekservice die u gebruikt:"]
> * [Versie 1](data-factory-create-pipelines.md)
> * [Versie 2 (huidige versie)](../concepts-pipelines-activities.md)

> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Zie [Pipelines in V2](../concepts-pipelines-activities.md)als u de huidige versie van de service Data Factory gebruikt.

Met behulp van dit artikel krijgt u inzicht in de pijplijnen en activiteiten in Azure Data Factory en in de wijze waarop u deze kunt gebruiken om end-to-end gegevensgestuurde werkstromen te maken voor uw gegevensverplaatsingen en scenario’s voor gegevensverwerking.

> [!NOTE]
> In dit artikel wordt ervan uitgegaan dat u [de inleiding tot Azure Data Factory](data-factory-introduction.md)hebt doorlopen. Als u geen hands-on-ervaring met het creëren van gegevens fabrieken, gaan door middel van [data transformatie tutorial](data-factory-build-your-first-pipeline.md) en / of data beweging [tutorial](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) zou u helpen begrijpen dit artikel beter.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>Overzicht
Een gegevensfactory kan één of meer pijplijnen hebben. Een pijplijn is een logische groep activiteiten die samen een taak uitvoeren. Met activiteiten in een pijplijn definieert u welk acties moeten worden uitgevoerd voor uw gegevens. U kunt bijvoorbeeld een kopieeractiviteit gebruiken om gegevens van een on-premises SQL Server naar Azure Blob Storage te kopiëren. Vervolgens kunt u een Hive-activiteit gebruiken waarmee een Hive-script op een Azure HDInsight-cluster wordt uitgevoerd om gegevens uit Blob Storage te verwerken/transformeren en uitvoergegevens te produceren. Gebruik tot slot een tweede kopieeractiviteit voor het kopiëren van de uitvoergegevens naar een Azure SQL Data Warehouse waarop rapportageoplossingen voor business intelligence (BI) zijn gebouwd.

Een activiteit kan nul of meer [invoergegevenssets](data-factory-create-datasets.md) hebben en een of meer [uitvoergegevenssets](data-factory-create-datasets.md) produceren. Het volgende diagram toont de relatie tussen de pijplijn, activiteit en gegevensset in Data Factory:

![Relatie tussen pijplijn, activiteit en gegevensset](media/data-factory-create-pipelines/relationship-pipeline-activity-dataset.png)

Met een pijplijn u activiteiten beheren als een set in plaats van elke pijplijn afzonderlijk. U bijvoorbeeld een pijplijn implementeren, plannen, onderbreken en hervatten, in plaats van zelfstandig met activiteiten in de pijplijn om te gaan.

Data Factory ondersteunt twee soorten activiteiten: activiteiten voor gegevensverplaatsing en activiteiten voor gegevenstransformatie. Elke activiteit kan nul of meer [invoergegevenssets](data-factory-create-datasets.md) bevatten en een of meer uitvoergegevenssets produceren.

Een invoergegevensset vertegenwoordigt de invoer voor een activiteit in de pijplijn en een uitvoergegevensset vertegenwoordigt de uitvoer voor de activiteit. Met gegevenssets worden gegevens binnen andere gegevensarchieven geïdentificeerd, waaronder tabellen, bestanden, mappen en documenten. Nadat u een gegevensset hebt gemaakt, kunt u deze gebruiken voor activiteiten in een pijplijn. Een gegevensset kan bijvoorbeeld een gegevensset voor invoer/uitvoer van een kopieeractiviteit of een HDInsightHive-activiteit zijn. Zie het artikel [Gegevenssets in Azure Data Factory](data-factory-create-datasets.md) voor meer informatie over gegevenssets.

### <a name="data-movement-activities"></a>Activiteiten voor gegevensverplaatsing
De kopieeractiviteit in Data Factory kopieert gegevens van een brongegevensarchief naar een sinkgegevensarchief. Data Factory ondersteunt de volgende gegevensarchieven. Gegevens vanuit elke willekeurige bron kunnen naar een sink worden geschreven. Klik op een gegevensarchief voor informatie over het kopiëren van gegevens naar en van dat archief.

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

> [!NOTE]
> Gegevensarchieven met een * kunnen zich on-premises of op Azure IaaS bevinden. Hiervoor moet u [Data Management Gateway](data-factory-data-management-gateway.md) installeren op een on-premises/Azure IaaS-computer.

Zie het artikel [Activiteiten voor gegevensverplaatsing](data-factory-data-movement-activities.md) voor meer informatie.

### <a name="data-transformation-activities"></a>Activiteiten voor gegevenstransformatie
[!INCLUDE [data-factory-transformation-activities](../../../includes/data-factory-transformation-activities.md)]

Zie het artikel [Activiteiten voor gegevenstransformatie](data-factory-data-transformation-activities.md) voor meer informatie.

### <a name="custom-net-activities"></a>Aangepaste .NET-activiteiten
Als u gegevens naar/van een gegevensarchief moet verplaatsen die de activiteit kopiëren niet ondersteunt, of gegevens wilt transformeren met uw eigen logica, maakt u een **aangepaste .NET-activiteit**. Zie [Aangepaste activiteiten gebruiken in een Azure Data Factory-pijplijn](data-factory-use-custom-activities.md) voor meer informatie over het maken en gebruiken van een aangepaste activiteit.

## <a name="schedule-pipelines"></a>Pijplijnen plannen
Een pijplijn is alleen actief tussen **de begintijd** en **de eindtijd.** Het wordt niet uitgevoerd vóór de begintijd of na de eindtijd. Als de pijplijn is onderbroken, wordt deze niet uitgevoerd, ongeacht de begin- en eindtijd. Als een pijplijn moet worden uitgevoerd, mag deze niet worden onderbroken. Zie [Planning en uitvoering](data-factory-scheduling-and-execution.md) om te begrijpen hoe planning en uitvoering werken in Azure Data Factory.

## <a name="pipeline-json"></a>Pijplijn in JSON-indeling
We gaan dieper in op hoe een pijplijn wordt gedefinieerd in JSON-indeling. De algemene structuur voor een pijplijn ziet er als volgt uit:

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

| Label | Beschrijving | Vereist |
| --- | --- | --- |
| name |Naam van de pijplijn. Geef een naam op die staat voor de actie die de pijplijn uitvoert. <br/><ul><li>Maximum aantal tekens: 260</li><li>Moet beginnen met een letternummer,\_of een underscore ( )</li><li>Volgende tekens zijn niet toegestaan: "",+,','?",,/',<\*>',>',>',',&':'","","","","\\</li></ul> |Ja |
| description | Voer een beschrijving in van het doel waarvoor de pijplijn wordt gebruikt. |Ja |
| activities | De sectie **activities** kan één of meer activiteiten bevatten die zijn gedefinieerd binnen de activiteit. Zie de volgende sectie voor meer informatie over de activiteiten JSON element. | Ja |
| start | Begindatumtijd voor de pijplijn. Moet in [ISO-formaat](https://en.wikipedia.org/wiki/ISO_8601)zijn . Bijvoorbeeld: `2016-10-14T16:32:41Z`. <br/><br/>Het is mogelijk om een lokale tijd op te geven, bijvoorbeeld een EST-tijd. Hier is een `2016-02-27T06:00:00-05:00`voorbeeld: ", dat is 6 AM EST.<br/><br/>De eigenschappen begin en eind geven samen een actieve periode voor de pijplijn op. Uitvoersegmenten worden alleen geproduceerd met in deze actieve periode. |Nee<br/><br/>Als u een waarde opgeeft voor de eigenschap end, moet u de waarde opgeven voor de eigenschap start.<br/><br/>De begin- en eindtijden kunnen beide leeg zijn om een pijplijn te maken. U moet beide waarden opgeven om een actieve periode in te stellen waarop de pijplijn moet worden uitgevoerd. Als u de begin- en eindtijden bij het maken van een pijplijn niet opgeeft, u deze later instellen met de cmdlet Set-AzDataFactoryPipelineActivePeriod. |
| beëindigen | Einddatum voor de pijplijn. Indien opgegeven moet in ISO-formaat. Bijvoorbeeld: `2016-10-14T17:32:41Z` <br/><br/>Het is mogelijk om een lokale tijd op te geven, bijvoorbeeld een EST-tijd. Hier is een `2016-02-27T06:00:00-05:00`voorbeeld: , dat is 6 AM EST.<br/><br/>Als u de pijplijn voor onbepaalde tijd wilt uitvoeren, geeft u 9999-09-09 op als waarde voor de eigenschap end. <br/><br/> Een pijplijn is alleen actief tussen de begintijd en de eindtijd. Het wordt niet uitgevoerd vóór de begintijd of na de eindtijd. Als de pijplijn is onderbroken, wordt deze niet uitgevoerd, ongeacht de begin- en eindtijd. Als een pijplijn moet worden uitgevoerd, mag deze niet worden onderbroken. Zie [Planning en uitvoering](data-factory-scheduling-and-execution.md) om te begrijpen hoe planning en uitvoering werken in Azure Data Factory. |Nee <br/><br/>Als u een waarde opgeeft voor de eigenschap start, moet u de waarde opgeven voor de eigenschap end.<br/><br/>Zie notities voor de **eigenschap start.** |
| isOnderbrokened | Als deze is ingesteld op true, wordt de pijplijn niet uitgevoerd. Het is in de onderbroken toestand. Standaardwaarde = false. U deze eigenschap gebruiken om een pijplijn in of uit te schakelen. |Nee |
| pipelineMode | De methode voor het plannen wordt uitgevoerd voor de pijplijn. Toegestane waarden zijn: gepland (standaard), eenmalig.<br/><br/>'Gepland' geeft aan dat de pijplijn wordt uitgevoerd met een opgegeven tijdsinterval op basis van de actieve periode (begin- en eindtijd). 'Eenmalig' geeft aan dat de pijplijn slechts één keer wordt uitgevoerd. Eenmalige pijplijnen die eenmaal zijn gemaakt, kunnen momenteel niet worden gewijzigd/bijgewerkt. Zie [Eenmalige pijplijn](#onetime-pipeline) voor meer informatie over eenmalige instelling. |Nee |
| vervaldatumTijd | Duur van de tijd na de creatie waarvoor de [eenmalige pijplijn](#onetime-pipeline) geldig is en moet blijven ingericht. Als er geen actieve, mislukte of lopende uitvoeringen zijn, wordt de pijplijn automatisch verwijderd zodra deze de vervaldatum heeft bereikt. De standaardwaarde:`"expirationTime": "3.00:00:00"`|Nee |
| gegevenssets |Lijst met gegevenssets die moeten worden gebruikt door activiteiten die in de pijplijn zijn gedefinieerd. Deze eigenschap kan worden gebruikt om gegevenssets te definiëren die specifiek zijn voor deze pijplijn en niet zijn gedefinieerd in de gegevensfabriek. Gegevenssets die in deze pijplijn zijn gedefinieerd, kunnen alleen door deze pijplijn worden gebruikt en kunnen niet worden gedeeld. Zie [Scoped-gegevenssets](data-factory-create-datasets.md#scoped-datasets) voor meer informatie. |Nee |

## <a name="activity-json"></a>Activity in JSON
De sectie **activities** kan één of meer activiteiten bevatten die zijn gedefinieerd binnen de activiteit. Elke activiteit heeft de volgende structuur op het hoogste niveau:

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

De volgende tabel beschrijft de eigenschappen in de JSON-definitie activity:

| Label | Beschrijving | Vereist |
| --- | --- | --- |
| name | De naam van de activiteit. Geef een naam op die staat voor de actie die de activiteit uitvoert. <br/><ul><li>Maximum aantal tekens: 260</li><li>Moet beginnen met een letternummer,\_of een underscore ( )</li><li>Volgende tekens zijn niet toegestaan: "",+,','?",,/',<',>',"",","","&,"""""","","","","\\</li></ul> |Ja |
| description | Beschrijving van het doel waarvoor de activiteit of wordt gebruikt |Ja |
| type | Type activiteit. Zie de secties [Activiteiten voor gegevensverplaatsingen](#data-movement-activities) en [gegevenstransformaties](#data-transformation-activities) voor verschillende soorten activiteiten. |Ja |
| Ingangen |Invoertabellen die door de activiteit worden gebruikt<br/><br/>`// one input table`<br/>`"inputs":  [ { "name": "inputtable1"  } ],`<br/><br/>`// two input tables` <br/>`"inputs":  [ { "name": "inputtable1"  }, { "name": "inputtable2"  } ],` |Ja |
| Uitgangen |Uitvoertabellen die door de activiteit worden gebruikt.<br/><br/>`// one output table`<br/>`"outputs":  [ { "name": "outputtable1" } ],`<br/><br/>`//two output tables`<br/>`"outputs":  [ { "name": "outputtable1" }, { "name": "outputtable2" }  ],` |Ja |
| linkedServiceName |De naam van de gekoppelde service die door de activiteit wordt gebruikt. <br/><br/>Een activiteit kan vereisen dat u de gekoppelde service opgeeft die is gekoppeld aan de vereiste rekenomgeving. |Ja voor HDInsight-activiteit en Azure Machine Learning Batchscoring-activiteit <br/><br/>Nee voor alle andere |
| typeProperties |Eigenschappen in de sectie **typeEigenschappen** zijn afhankelijk van het type activiteit. Klik op koppelingen naar de activiteiten in de vorige sectie om typeProperties voor een activiteit te bekijken. | Nee |
| policy |Beleidsregels die van invloed zijn op het runtimegedrag van de activiteit. Als dit niet is opgegeven, worden standaardbeleidsregels gebruikt. |Nee |
| scheduler | De eigenschap 'scheduler' wordt gebruikt om de gewenste planning voor de activiteit te definiëren. De subeigenschappen zijn dezelfde als die in de [eigenschap beschikbaarheid in een gegevensset.](data-factory-create-datasets.md#dataset-availability) |Nee |

### <a name="policies"></a>Beleidsregels
Beleidsregels zijn van invloed op het verloop van een activiteit, met name wanneer het segment van een tabel wordt verwerkt. De volgende tabel bevat de details.

| Eigenschap | Toegestane waarden | Standaardwaarde | Beschrijving |
| --- | --- | --- | --- |
| Concurrency |Geheel getal <br/><br/>Maximale waarde: 10 |1 |Aantal gelijktijdige uitvoeringen van de activiteit.<br/><br/>Het bepaalt het aantal parallelle activiteitsuitvoeringen dat op verschillende segmenten kan plaatsvinden. Als een activiteit bijvoorbeeld een grote set beschikbare gegevens moet doorlopen, versnelt een grotere gelijktijdigheidswaarde de gegevensverwerking. |
| uitvoeringPrioriteitOrder |NieuwsteEerste<br/><br/>OudsteEerste |OudsteEerste |Hiermee bepaalt u de volgorde van gegevenssegmenten die worden verwerkt.<br/><br/>Bijvoorbeeld, als u 2 segmenten (een gebeurt om 16.00 uur, en een andere om 17.00 uur), en beide zijn in afwachting van uitvoering. Als u de executionPriorityOrder als NieuwsteEerste instelt, wordt het segment om 17.00 uur eerst verwerkt. Als u de uitvoeringPrioriteitORder als OldestFIrst instelt, wordt het segment om 16.00 uur verwerkt. |
| retry |Geheel getal<br/><br/>Maximale waarde kan 10 zijn |0 |Het aantal nieuwe pogingen vóór de gegevensverwerking voor het segment wordt gemarkeerd als Mislukt. Activiteitsuitvoering voor een gegevenssegment wordt opnieuw geprobeerd tot het opgegeven aantal opnieuw proberen. De poging wordt zo snel mogelijk na de storing uitgevoerd. |
| timeout |TimeSpan |00:00:00 |Time-out voor de activiteit. Voorbeeld: 00:10:00 (impliceert time-out 10 minuten)<br/><br/>Als een waarde niet is opgegeven of 0 is, is de time-out oneindig.<br/><br/>Als de verwerkingstijd van gegevens op een segment de time-outwaarde overschrijdt, wordt deze geannuleerd en probeert het systeem de verwerking opnieuw te proberen. Het aantal nieuwe pogingen is afhankelijk van de eigenschap opnieuw proberen. Wanneer een time-out optreedt, wordt de status ingesteld op TimedOut. |
| Vertraging |TimeSpan |00:00:00 |Geef de vertraging op voordat de gegevensverwerking van het segment wordt gestart.<br/><br/>De uitvoering van activiteit voor een gegevenssegment wordt gestart nadat de vertraging de verwachte uitvoeringstijd is voorbijgegaan.<br/><br/>Voorbeeld: 00:10:00 (impliceert vertraging van 10 minuten) |
| longRetry |Geheel getal<br/><br/>Maximale waarde: 10 |1 |Het aantal lange pogingen om opnieuw te proberen voordat de slice-uitvoering is mislukt.<br/><br/>longRetry pogingen worden gespreid door longRetryInterval. Dus als u een tijd tussen pogingen opnieuw proberen moet opgeven, gebruikt u longRetry. Als zowel Opnieuw proberen als longRetry zijn opgegeven, omvat elke longRetry-poging opnieuw proberen pogingen en het maximum aantal pogingen is Opnieuw proberen * longRetry.<br/><br/>Als we bijvoorbeeld de volgende instellingen in het activiteitenbeleid hebben:<br/>Opnieuw proberen: 3<br/>longRetry: 2<br/>longRetryInterval: 01:00:00<br/><br/>Stel dat er slechts één segment moet worden uitgevoerd (status is Wachten) en dat de activiteitsuitvoering elke keer mislukt. Aanvankelijk zouden er 3 opeenvolgende uitvoeringspogingen zijn. Na elke poging wordt de segmentstatus opnieuw geprobeerd. Nadat de eerste 3 pogingen voorbij zijn, zou de segmentstatus LongRetry zijn.<br/><br/>Na een uur (dat wil zeggen, longRetryInteval's waarde), zou er een andere set van 3 opeenvolgende uitvoeringspogingen. Daarna is de segmentstatus mislukt en worden er geen pogingen meer geprobeerd. Vandaar dat er in totaal 6 pogingen werden gedaan.<br/><br/>Als een uitvoering slaagt, is de segmentstatus gereed en worden er geen pogingen meer geprobeerd.<br/><br/>longRetry kan worden gebruikt in situaties waarin afhankelijke gegevens op niet-deterministische tijden aankomen of de algehele omgeving schilferig is waaronder gegevensverwerking plaatsvindt. In dergelijke gevallen, het doen van pogingen een na de ander kan niet helpen en doen na een interval van tijd resulteert in de gewenste output.<br/><br/>Woord van voorzichtigheid: stel geen hoge waarden in voor longRetry of longRetryInterval. Hogere waarden impliceren doorgaans andere systemische problemen. |
| longRetryInterval |TimeSpan |00:00:00 |De vertraging tussen lange pogingen om opnieuw te proberen |

## <a name="sample-copy-pipeline"></a>Voorbeeld van kopieerpijplijn
De volgende voorbeeldpijplijn bevat een activiteit van het type **Copy** in de sectie **activities**. In dit voorbeeld kopieert de [kopieeractiviteit](data-factory-data-movement-activities.md) gegevens vanuit een Azure-blobopslag naar een Azure SQL-database.

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

* In het gedeelte Activiteiten is er slechts één activiteit waarvan **type** is ingesteld op **Copy**.
* De invoer voor de activiteit is ingesteld op **InputDataset** en de uitvoer voor de activiteit is ingesteld op **OutputDataset**. Zie het artikel [Gegevenssets](data-factory-create-datasets.md) voor informatie over het definiëren van gegevenssets in JSON.
* In het gedeelte **typeProperties** is **BlobSource** opgegeven als het brontype en **SqlSink** als het sink-type. Klik in de sectie [Activiteiten voor gegevensverplaatsing](#data-movement-activities) op het gegevensarchief dat u als bron of gootsteen wilt gebruiken voor meer informatie over het verplaatsen van gegevens van/naar dat gegevensarchief.

Zie [Zelfstudie: Gegevens kopiëren van Blob-opslag naar SQL-database voor](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)een volledige walkthrough van het maken van deze pijplijn.

## <a name="sample-transformation-pipeline"></a>Voorbeeld van pijplijn voor transformatie
De volgende voorbeeldpijplijn bevat een activiteit van het type **HDInsightHive** in de sectie **activities**. In dit voorbeeld transformeert de [HDInsight Hive-activiteit](data-factory-hive-activity.md) gegevens uit een Azure-blobopslag door een Hive-scriptbestand uit te voeren op een Azure HDInsight Hadoop-cluster.

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

* In het gedeelte activities is er slechts één activiteit waarvan **type** is ingesteld op **HDInsightHive**.
* Het Hive-scriptbestand **partitionweblogs.hql** wordt opgeslagen in het Azure-opslagaccount (opgegeven door de scriptLinkedService met de naam **AzureStorageLinkedService**) en in de map **script** van de container **adfgetstarted**.
* De `defines` sectie wordt gebruikt om de runtime-instellingen op te geven die `${hiveconf:inputtable}`worden `${hiveconf:partitionedtable}`doorgegeven aan het hive-script als Hive-configuratiewaarden (bijv. ).

De sectie **typeProperties** verschilt voor elke transformatieactiviteit. Als u meer wilt weten over teksteigenschappen die worden ondersteund voor een transformatieactiviteit, klikt u op de transformatieactiviteit in de tabel [Activiteiten voor gegevenstransformatie.](#data-transformation-activities)

Zie [Zelfstudie: Uw eerste pijplijn maken om gegevens te verwerken met hadoopcluster](data-factory-build-your-first-pipeline.md)voor een volledige walkthrough van het maken van deze pijplijn.

## <a name="multiple-activities-in-a-pipeline"></a>Meerdere activiteiten in een pijplijn
De vorige twee voorbeeldpijplijnen bevatten slechts één activiteit. Een pijplijn kan echter meer dan één activiteit hebben.

Als u meerdere activiteiten in een pijplijn hebt en de uitvoer van een activiteit geen invoer van een andere activiteit is, kunnen de activiteiten parallel worden uitgevoerd als invoergegevenssegmenten voor de activiteiten gereed zijn.

U twee activiteiten ketenen door de uitvoergegevensset van één activiteit als invoergegevensset van de andere activiteit te hebben. De tweede activiteit wordt alleen uitgevoerd wanneer de eerste activiteit is voltooid.

![Ketenactiviteiten in dezelfde pijplijn](./media/data-factory-create-pipelines/chaining-one-pipeline.png)

In deze steekproef heeft de pijplijn twee activiteiten: Activiteit1 en Activiteit2. De Activiteit1 neemt Dataset1 als invoer en produceert een uitvoergegevensset2. De activiteit neemt Dataset2 als invoer en produceert een uitvoergegevensset3. Aangezien de uitvoer van Activiteit1 (Dataset2) de invoer van Activiteit2 is, wordt activiteit2 pas uitgevoerd nadat de activiteit is voltooid en het segment Gegevensset2 produceert. Als activiteit1 om de een of andere reden mislukt en het segment Gegevensset2 niet produceert, wordt de activiteit 2 niet uitgevoerd voor dat segment (bijvoorbeeld: 9.00 tot 10.00 uur).

U ook activiteiten ketenen die zich in verschillende pijplijnen bevinden.

![Ketenactiviteiten in twee pijpleidingen](./media/data-factory-create-pipelines/chaining-two-pipelines.png)

In dit voorbeeld heeft Pipeline1 slechts één activiteit die Dataset1 als invoer neemt en Dataset2 als uitvoer produceert. De Pipeline2 heeft ook slechts één activiteit die Dataset2 als invoer en Dataset3 als uitvoer neemt.

Zie [planning en uitvoering](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline)voor meer informatie.
## <a name="create-and-monitor-pipelines"></a>Pijplijnen maken en bewaken
U pijplijnen maken met een van deze hulpprogramma's of SDK's.

- De wizard Kopiëren
- Visual Studio
- Azure PowerShell
- Azure Resource Manager-sjabloon
- REST API
- .NET API

Bekijk de volgende zelfstudies voor stapsgewijze instructies voor het maken van pijplijnen met behulp van een van deze hulpprogramma's of SDK's.

- [Een pijplijn met een transformatieactiviteit voor gegevens bouwen](data-factory-build-your-first-pipeline.md)
- [Een pijplijn maken met een activiteit voor gegevensverplaatsing](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)

Zodra een pijplijn is gemaakt/geïmplementeerd, u uw pijplijnen beheren en bewaken met behulp van de Azure-portalblades of Monitor en Manage App. Zie de volgende onderwerpen voor stapsgewijze instructies.

- [Pijplijnen bewaken en beheren met Azure-portalblades.](data-factory-monitor-manage-pipelines.md)
- [Pijplijnen bewaken en beheren met behulp van Monitor en Beheer app](data-factory-monitor-manage-app.md)

## <a name="onetime-pipeline"></a>Eenmalige pijplijn
U een pijplijn maken en plannen die periodiek wordt uitgevoerd (bijvoorbeeld: per uur of per dag) binnen de begin- en eindtijden die u in de pijplijndefinitie opgeeft. Zie Activiteiten plannen voor meer informatie. U ook een pijplijn maken die slechts één keer wordt uitgevoerd. Als u dit wilt doen, stelt u de eigenschap **pipelineMode** in de pijplijndefinitie in **op eenmalige definitie,** zoals wordt weergegeven in het volgende JSON-voorbeeld. De standaardwaarde voor deze eigenschap is **gepland**.

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

Houd rekening met het volgende:

* **Begin-** en **eindtijden** voor de pijplijn zijn niet opgegeven.
* **Beschikbaarheid** van invoer- en uitvoergegevenssets is opgegeven **(frequentie** en **interval),** ook al gebruikt Data Factory de waarden niet.
* In de diagramweergave worden geen eenmalige pijplijnen weergegeven. Dit gedrag is standaard.
* Eenmalige pijplijnen kunnen niet worden bijgewerkt. U een eenmalige pijplijn klonen, de naam wijzigen, eigenschappen bijwerken en implementeren om een andere pijplijn te maken.

## <a name="next-steps"></a>Volgende stappen
- Zie Artikel [Gegevenssets maken voor](data-factory-create-datasets.md) meer informatie over gegevenssets.
- Zie Plannen en uitvoeren in het artikel [Azure Data Factory](data-factory-scheduling-and-execution.md) voor meer informatie over hoe pijplijnen worden gepland en uitgevoerd.
