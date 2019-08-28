---
title: Kopieer activiteit in Azure Data Factory | Microsoft Docs
description: Meer informatie over de Kopieer activiteit in Azure Data Factory. U kunt dit gebruiken om gegevens van een ondersteund brongegevens archief te kopiëren naar een ondersteund Sink-gegevens archief.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/12/2019
ms.author: jingwang
ms.openlocfilehash: 8af5673ff0ffef7306a13eceda86f879b5b31413
ms.sourcegitcommit: 388c8f24434cc96c990f3819d2f38f46ee72c4d8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/27/2019
ms.locfileid: "70060694"
---
# <a name="copy-activity-in-azure-data-factory"></a>Kopieer activiteit in Azure Data Factory

> [!div class="op_single_selector" title1="Selecteer de versie van Data Factory die u gebruikt:"]
> * [Versie 1:](v1/data-factory-data-movement-activities.md)
> * [Huidige versie](copy-activity-overview.md)

In Azure Data Factory kunt u de Kopieer activiteit gebruiken om gegevens te kopiëren tussen gegevens archieven die zich lokaal en in de cloud bevinden. Nadat u de gegevens hebt gekopieerd, kunt u andere activiteiten gebruiken om deze verder te transformeren en te analyseren. U kunt ook de Kopieer activiteit gebruiken om trans formatie-en analyse resultaten te publiceren voor business intelligence (BI) en het gebruik van toepassingen.

![De rol van de Kopieer activiteit](media/copy-activity-overview/copy-activity.png)

De Kopieer activiteit wordt uitgevoerd op een [Integration runtime](concepts-integration-runtime.md). U kunt verschillende typen Integration Runtimes voor verschillende scenario's voor het kopiëren van gegevens gebruiken:

* Bij het kopiëren van gegevens tussen twee gegevens archieven die openbaar toegankelijk zijn via internet vanuit elk IP-adres, kunt u de Azure Integration runtime gebruiken voor de Kopieer activiteit. Deze Integration runtime is veilig, betrouwbaar, schaalbaar en [wereld wijd beschikbaar](concepts-integration-runtime.md#integration-runtime-location).
* Wanneer u gegevens kopieert van en naar gegevens archieven die zich on-premises of in een netwerk met toegangs beheer (bijvoorbeeld een virtueel netwerk van Azure) bevinden, moet u een zelf-hostende Integration runtime instellen.

Een Integration runtime moet worden gekoppeld aan elke bron-en Sink-gegevens opslag. Zie [bepalen welke IR u moet gebruiken](concepts-integration-runtime.md#determining-which-ir-to-use)voor meer informatie over hoe de Kopieer activiteit bepaalt welke Integration runtime moet worden gebruikt.

Als u gegevens wilt kopiëren van een bron naar een sink, voert de service die de Kopieer activiteit uitvoert de volgende stappen uit:

1. Leest gegevens uit een bron-gegevensopslag.
2. Voert serialisatie/deserialisatie, compressie/decompressie, kolom toewijzing, enzovoort uit. Deze bewerkingen worden uitgevoerd op basis van de configuratie van de invoer gegevensset, de uitvoer gegevensset en de Kopieer activiteit.
3. Schrijft gegevens naar het sink/doelgegevensarchief.

![Overzicht van de Kopieer activiteit](media/copy-activity-overview/copy-activity-overview.png)

## <a name="supported-data-stores-and-formats"></a>Ondersteunde gegevensarchieven en indelingen

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores.md)]

### <a name="supported-file-formats"></a>Ondersteunde bestandsindelingen

U kunt de Kopieer activiteit gebruiken om bestanden te kopiëren als tussen twee op bestanden gebaseerde gegevens archieven. In dit geval worden de gegevens efficiënt gekopieerd zonder serialisatie of deserialisatie.

De Kopieer activiteit kan ook lezen uit en schrijven naar bestanden in de volgende indelingen:
- Text
- JSON
- Avro
- ORC
- Parquet

De Kopieer activiteit kan bestanden comprimeren en decomprimeren met de volgende codecs: 
- Gzip
- Deflate
- Bzip2
- ZipDeflate

Zie [ondersteunde bestands-en compressie-indelingen](supported-file-formats-and-compression-codecs.md)voor meer informatie.

U kunt bijvoorbeeld de volgende Kopieer activiteiten uitvoeren:

* Gegevens kopiëren van een on-premises SQL Server Data Base en de gegevens schrijven naar Azure Data Lake Storage Gen2 in de Parquet-indeling.
* Kopieer bestanden in de tekst indeling (CSV) van een on-premises bestands systeem en schrijf naar Azure Blob-opslag in de Avro-indeling.
* Kopieer gezipte bestanden van een on-premises bestands systeem, Decomprimeer ze en schrijf ze naar Azure Data Lake Storage Gen2.
* Kopieer gegevens in de indeling van de tekst in het type gzip (gecomprimeerd) in de vorm van Azure Blob-opslag en schrijf deze naar Azure SQL Database.
* Veel meer activiteiten waarvoor serialisatie/deserialisatie of compressie/decompressie nodig is.

## <a name="supported-regions"></a>Ondersteunde regio’s

De service die de Kopieer activiteit mogelijk maakt, is wereld wijd beschikbaar in de regio's en geografies die worden vermeld in [Azure Integration runtime-locaties](concepts-integration-runtime.md#integration-runtime-location). De algemeen beschikbare topologie zorgt u ervoor efficiënt gegevensverplaatsing die meestal regio-overschrijdende hops voorkomt. Zie [producten per regio](https://azure.microsoft.com/regions/#services) om de beschik baarheid van Data Factory en het verplaatsen van gegevens in een specifieke regio te controleren.

## <a name="configuration"></a>Configuratie

Als u de Kopieer activiteit in Azure Data Factory wilt gebruiken, moet u het volgende doen:

1. **Gekoppelde services maken voor de brongegevens opslag en het sink-gegevens archief.** Raadpleeg de sectie ' eigenschappen van gekoppelde service ' van het connector artikel voor informatie over de configuratie en ondersteunde eigenschappen. De lijst met ondersteunde connectors vindt u in de sectie [ondersteunde gegevens archieven en-indelingen](#supported-data-stores-and-formats) van dit artikel.
2. **Gegevens sets maken voor de bron en Sink.** Raadpleeg de sectie eigenschappen van gegevensset van de bron-en Sink-connector artikelen voor informatie over de configuratie en ondersteunde eigenschappen.
3. **Maak een pijp lijn met de Kopieer activiteit.** De volgende sectie bevat een voorbeeld.

### <a name="syntax"></a>Syntaxis

De volgende sjabloon van een Kopieer activiteit bevat een volledige lijst met ondersteunde eigenschappen. Geef de waarden die geschikt zijn voor uw scenario.

```json
"activities":[
    {
        "name": "CopyActivityTemplate",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<source dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<sink dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>",
                <properties>
            },
            "sink": {
                "type": "<sink type>"
                <properties>
            },
            "translator":
            {
                "type": "TabularTranslator",
                "columnMappings": "<column mapping>"
            },
            "dataIntegrationUnits": <number>,
            "parallelCopies": <number>,
            "enableStaging": true/false,
            "stagingSettings": {
                <properties>
            },
            "enableSkipIncompatibleRow": true/false,
            "redirectIncompatibleRowSettings": {
                <properties>
            }
        }
    }
]
```

#### <a name="syntax-details"></a>Syntaxis van de details

| Eigenschap | Description | Vereist? |
|:--- |:--- |:--- |
| Type | Stel voor een Kopieer activiteit in op`Copy` | Ja |
| inputs | Geef de gegevensset op die u hebt gemaakt die verwijst naar de bron gegevens. De Kopieer activiteit ondersteunt slechts één invoer. | Ja |
| outputs | Geef de gegevensset op die u hebt gemaakt die verwijst naar de Sink-gegevens. De Kopieer activiteit ondersteunt slechts één uitvoer. | Ja |
| typeProperties | Geef eigenschappen op voor het configureren van de Kopieer activiteit. | Ja |
| source | Geef het type Kopieer bron en de bijbehorende eigenschappen op om gegevens op te halen.<br/><br/>Zie voor meer informatie de sectie ' eigenschappen van de Kopieer activiteit ' in het connector artikel dat wordt vermeld in [ondersteunde gegevens archieven en-indelingen](#supported-data-stores-and-formats). | Ja |
| sink | Geef het sink-type voor het kopiëren en de bijbehorende eigenschappen op voor het schrijven van gegevens.<br/><br/>Zie voor meer informatie de sectie ' eigenschappen van de Kopieer activiteit ' in het connector artikel dat wordt vermeld in [ondersteunde gegevens archieven en-indelingen](#supported-data-stores-and-formats). | Ja |
| translator | Geef expliciete kolomtoewijzingen van bron naar een sink. Deze eigenschap is van toepassing wanneer het standaard gedrag voor kopiëren niet aan uw behoeften voldoet.<br/><br/>Zie [schema toewijzing in de Kopieer activiteit](copy-activity-schema-and-type-mapping.md)voor meer informatie. | Nee |
| dataIntegrationUnits | Geef een meting op die staat voor de hoeveelheid kracht die de [Azure Integration runtime](concepts-integration-runtime.md) gebruikt voor het kopiëren van gegevens. Deze eenheden waren voorheen bekend als DMU (Cloud data units). <br/><br/>Zie [gegevens integratie-eenheden](copy-activity-performance.md#data-integration-units)voor meer informatie. | Nee |
| parallelCopies | Geef de parallelle factor op die de Kopieer activiteit moet gebruiken bij het lezen van gegevens van de bron en het schrijven van gegevens naar de sink.<br/><br/>Zie [parallelle kopie](copy-activity-performance.md#parallel-copy)voor meer informatie. | Nee |
| enableStaging<br/>stagingSettings | Geef op of de tussenliggende gegevens in Blob Storage moeten worden gestage in plaats van gegevens rechtstreeks van bron naar sink te kopiëren.<br/><br/>Zie voor meer informatie over nuttige scenario's en configuratie Details [gefaseerde kopie](copy-activity-performance.md#staged-copy). | Nee |
| enableSkipIncompatibleRow<br/>redirectIncompatibleRowSettings| Kies hoe u incompatibele rijen wilt verwerken wanneer u gegevens kopieert van bron naar sink.<br/><br/>Zie [fout tolerantie](copy-activity-fault-tolerance.md)voor meer informatie. | Nee |

## <a name="monitoring"></a>Bewaking

U kunt de uitvoering van de Kopieer activiteit bewaken in de gebruikers interface van de Azure Data Factory **Author &-controle** of via een programma.

### <a name="monitor-visually"></a>Visueel bewaken

Als u de uitvoering van de Kopieer activiteit visueel wilt controleren, gaat u naar uw data factory en gaat u naar **& monitor**maken. Op het tabblad **monitor** ziet u een lijst met pijplijn uitvoeringen met een knop voor het uitvoeren van de **activiteit weer geven** in de kolom **acties** :

![Pijplijnuitvoeringen controleren](./media/load-data-into-azure-data-lake-store/monitor-pipeline-runs.png)

Selecteer **uitvoering van activiteit weer geven** om de lijst met activiteiten in de pijplijn uitvoering weer te geven. In de kolom **acties** ziet u koppelingen naar de invoer van de Kopieer activiteit, uitvoer, fouten (als het uitvoeren van de Kopieer activiteit mislukt) en Details:

![Uitvoering van activiteiten controleren](./media/load-data-into-azure-data-lake-store/monitor-activity-runs.png)

Selecteer de knop **Details** in de kolom **acties** om de uitvoerings Details en prestatie kenmerken van de Kopieer activiteit te bekijken. U ziet informatie zoals volume/aantal rijen/aantal gekopieerde gegevens van bron naar sink, door Voer, stappen die door de Kopieer activiteit worden uitgevoerd met de bijbehorende duur en de configuraties die worden gebruikt voor uw Kopieer scenario.

>[!TIP]
>In sommige scenario's ziet u ook tips voor het afstemmen van **prestaties** boven aan de pagina controle van kopiëren. In deze tips wordt uitgelegd hoe u knel punten herkent en informatie geeft over wat u moet wijzigen om de door Voer van kopieën te verhogen. Zie de sectie [prestaties en afstemming](#performance-and-tuning) van dit artikel voor een voor beeld.

**Voorbeeld: Kopiëren van Amazon S3 naar Azure data Lake Store**
![controle activiteit Details](./media/copy-activity-overview/monitor-activity-run-details-adls.png)

**Voorbeeld: Kopiëren van Azure SQL database naar Azure SQL data warehouse met behulp**van de controle activiteit monitor van de gefaseerde kopie
![](./media/copy-activity-overview/monitor-activity-run-details-sql-dw.png)

### <a name="monitor-programmatically"></a>Controleren via een programma

De details van de Kopieer activiteit en prestatie kenmerken worden ook weer gegeven in de sectie > **uitvoer** resultaat van de **Kopieer activiteit**. Hieronder vindt u een volledige lijst met eigenschappen die kunnen worden geretourneerd. U ziet alleen de eigenschappen die van toepassing zijn op uw Kopieer scenario. Zie [een pijplijn uitvoering bewaken](quickstart-create-data-factory-dot-net.md#monitor-a-pipeline-run)voor meer informatie over het controleren van de uitvoering van activiteiten.

| Naam van eigenschap  | Description | Eenheid |
|:--- |:--- |:--- |
| dataRead | Hoeveelheid gegevens die uit de bron is gelezen. | Int64-waarde, in bytes |
| datawritten door | De hoeveelheid gegevens die naar de sink wordt geschreven. | Int64-waarde, in bytes |
| filesRead | Het aantal bestanden dat is gekopieerd tijdens het kopiëren uit de bestands opslag. | Waarde voor Int64 (geen unit) |
| filesWritten | Het aantal bestanden dat is gekopieerd tijdens het kopiëren naar de bestands opslag. | Waarde voor Int64 (geen unit) |
| sourcePeakConnections | Het maximum aantal gelijktijdige verbindingen dat tot het bron gegevens archief is gemaakt tijdens het uitvoeren van de Kopieer activiteit. | Waarde voor Int64 (geen unit) |
| sinkPeakConnections | Het maximum aantal gelijktijdige verbindingen dat tot stand is gebracht met het sink-gegevens archief tijdens het uitvoeren van de Kopieer activiteit. | Waarde voor Int64 (geen unit) |
| rowsRead | Het aantal rijen dat is gelezen van de bron (niet van toepassing op een binaire kopie). | Waarde voor Int64 (geen unit) |
| rowsCopied | Het aantal rijen dat is gekopieerd naar de Sink (niet van toepassing op een binaire kopie). | Waarde voor Int64 (geen unit) |
| rowsSkipped | Aantal niet-compatibele rijen dat is overgeslagen. U kunt incompatibele rijen inschakelen door in te stellen `enableSkipIncompatibleRow` op waar. | Waarde voor Int64 (geen unit) |
| copyDuration | De duur van de Kopieer uitvoering. | Int32-waarde, in seconden |
| throughput | Frequentie van gegevens overdracht. | Drijvende-komma getal, in KBps |
| sourcePeakConnections | Het maximum aantal gelijktijdige verbindingen dat tot het bron gegevens archief is gemaakt tijdens het uitvoeren van de Kopieer activiteit. | Int32-waarde (geen eenheid) |
| sinkPeakConnections| Het maximum aantal gelijktijdige verbindingen dat tot stand is gebracht met het sink-gegevens archief tijdens het uitvoeren van de Kopieer activiteit.| Int32-waarde (geen eenheid) |
| sqlDwPolyBase | Hiermee wordt aangegeven of poly Base wordt gebruikt wanneer gegevens naar SQL Data Warehouse worden gekopieerd. | Boolean-waarde |
| redshiftUnload | Hiermee wordt aangegeven of verwijderen wordt gebruikt wanneer gegevens uit Redshift worden gekopieerd. | Boolean-waarde |
| hdfsDistcp | Hiermee wordt aangegeven of DistCp wordt gebruikt wanneer gegevens worden gekopieerd uit HDFS. | Boolean-waarde |
| effectiveIntegrationRuntime | Het uitvoeren van de uitvoering van de activiteit (IR) of runtime (Integration runtime), in `<IR name> (<region if it's Azure IR>)`de indeling. | Tekst (tekenreeks) |
| usedDataIntegrationUnits | De effectieve gegevens integratie eenheden tijdens het kopiëren. | Int32-waarde |
| usedParallelCopies | De effectieve parallelCopies tijdens het kopiëren. | Int32-waarde |
| redirectRowPath | Pad naar het logboek van overgeslagen rijen die niet compatibel zijn in de Blob-opslag `redirectIncompatibleRowSettings` die u configureert in de eigenschap. Zie [fout tolerantie](#fault-tolerance) verderop in dit artikel. | Tekst (tekenreeks) |
| executionDetails | Meer details over de stadia waarin de Kopieer activiteit wordt uitgevoerd, en de bijbehorende stappen, duur, configuraties, enzovoort. We raden u aan deze sectie niet te parseren omdat deze kan worden gewijzigd.<br/><br/>Data Factory rapporteert ook de gedetailleerde duur (in seconden) die is besteed aan verschillende `detailedDurations`fasen onder. De duur van deze stappen is exclusief. Er worden alleen duur waarden weer gegeven die van toepassing zijn op de opgegeven Kopieer activiteit:<br/>**Wachtrij duur** (`queuingDuration`): De hoeveelheid tijd voordat de Kopieer activiteit daad werkelijk wordt gestart op de Integration runtime. Als u een zelf-hostende IR gebruikt en deze waarde groot is, controleert u de IR-capaciteit en het gebruik en breidt u uit op basis van uw werk belasting. <br/>**Script duur vooraf kopiëren** (`preCopyScriptDuration`): De tijd tussen het moment dat de Kopieer activiteit wordt gestart op de IR en wanneer de Kopieer activiteit is voltooid met het uitvoeren van het script dat voorafgaat aan de Sink-gegevens opslag. Van toepassing wanneer u het script voor het vooraf kopiëren configureert. <br/>**Tijd tot eerste byte** (`timeToFirstByte`): De verstreken tijd tussen het einde van de vorige stap en de tijd waarop de IR de eerste byte van de brongegevens opslag ontvangt. Is van toepassing op bronnen die niet op bestanden zijn gebaseerd. Als deze waarde groot is, controleert u de query of de server en optimaliseert u deze.<br/>**Overdrachts duur** (`transferDuration`): De verstreken tijd tussen het einde van de vorige stap en het tijdstip waarop de IR alle gegevens van de bron naar de Sink overdraagt. | Array |
| perfRecommendation | Tips voor het afstemmen van de prestaties kopiëren. Zie [prestaties en afstemmen](#performance-and-tuning) voor meer informatie. | Array |

```json
"output": {
    "dataRead": 6198358,
    "dataWritten": 19169324,
    "filesRead": 1,
    "sourcePeakConnections": 1,
    "sinkPeakConnections": 2,
    "rowsRead": 39614,
    "rowsCopied": 39614,
    "copyDuration": 1325,
    "throughput": 4.568,
    "errors": [],
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (West US)",
    "usedDataIntegrationUnits": 4,
    "usedParallelCopies": 1,
    "executionDetails": [
        {
            "source": {
                "type": "AzureBlobStorage"
            },
            "sink": {
                "type": "AzureSqlDatabase"
            },
            "status": "Succeeded",
            "start": "2019-08-06T01:01:36.7778286Z",
            "duration": 1325,
            "usedDataIntegrationUnits": 4,
            "usedParallelCopies": 1,
            "detailedDurations": {
                "queuingDuration": 2,
                "preCopyScriptDuration": 12,
                "transferDuration": 1311
            }
        }
    ],
    "perfRecommendation": [
        {
            "Tip": "Sink Azure SQL Database: The DTU utilization was high during the copy activity run. To achieve better performance, you are suggested to scale the database to a higher tier than the current 1600 DTUs.",
            "ReferUrl": "https://go.microsoft.com/fwlink/?linkid=2043368",
            "RuleName": "AzureDBTierUpgradePerfRecommendRule"
        }
    ]
}
```

## <a name="schema-and-data-type-mapping"></a>Schema en de toewijzing van het type gegevens

Zie [schema en gegevens type toewijzing](copy-activity-schema-and-type-mapping.md) voor informatie over hoe de Kopieer activiteit uw bron gegevens aan uw Sink koppelt.

## <a name="fault-tolerance"></a>Fouttolerantie

Standaard stopt de Kopieer activiteit met het kopiëren van gegevens en wordt een fout geretourneerd wanneer brongegevens rijen niet compatibel zijn met Sink-gegevens rijen. Als u de kopie wilt laten slagen, kunt u de Kopieer activiteit configureren om de niet-compatibele rijen over te slaan en te registreren en alleen de compatibele gegevens te kopiëren. Zie [fout tolerantie voor kopieer activiteiten](copy-activity-fault-tolerance.md) voor meer informatie.

## <a name="performance-and-tuning"></a>Prestaties en afstemmen

In de [hand leiding Kopieer activiteit prestaties en schaal baarheid](copy-activity-performance.md) worden de belangrijkste factoren beschreven die van invloed zijn op de prestaties van het verplaatsen van gegevens via de Kopieer activiteit in azure Data Factory. Het bevat ook een lijst met de prestatie waarden die tijdens het testen zijn waargenomen en bespreekt hoe u de prestaties van de Kopieer activiteit kunt optimaliseren.

In sommige gevallen ziet u, wanneer u een Kopieer activiteit uitvoert in Data Factory, de **Tips** voor het afstemmen van prestaties boven aan de [pagina controle activiteit controleren](#monitor-visually), zoals wordt weer gegeven in het volgende voor beeld. De tips geven een uitleg over de knel punt die is geïdentificeerd voor de opgegeven kopie-uitvoering. Ze geven ook informatie over wat ze moeten wijzigen om de door Voer van kopieën te verhogen. De tips voor het afstemmen van prestaties bieden momenteel suggesties als bij het kopiëren van gegevens naar Azure SQL Data Warehouse, het verg Roten van Azure Cosmos DB RUs of Azure SQL Database Dtu's wanneer de resource aan de kant van het gegevens archief het knel punt is en verwijdert onnodige kopieën.

**Voorbeeld: Kopiëren naar Azure SQL Database, met een tip voor het afstemmen van prestaties**

In dit voor beeld houdt Data Factory tijdens het uitvoeren van een kopie een hoog DTU-gebruik in de Sink-Azure SQL Database. Deze voor waarde vertraagt schrijf bewerkingen. Het voor stel is om de Dtu's te verg Roten op de laag Azure SQL Database:

![Bewaking met tips voor het afstemmen van prestaties kopiëren](./media/copy-activity-overview/copy-monitoring-with-performance-tuning-tips.png)

## <a name="incremental-copy"></a>Incrementele kopie
Data Factory kunt u de Delta gegevens van een brongegevens archief stapsgewijs naar een Sink-gegevens archief kopiëren. Zie [voor meer informatie zelf studie: Incrementeel gegevens](tutorial-incremental-copy-overview.md)kopiëren.

## <a name="next-steps"></a>Volgende stappen
Zie de volgende snelstartgidsen, zelfstudies en voorbeelden:

- [Gegevens kopiëren van de ene locatie naar een andere locatie in hetzelfde Azure Blob Storage-account](quickstart-create-data-factory-dot-net.md)
- [Gegevens kopiëren van Azure Blob-opslag naar Azure SQL Database](tutorial-copy-data-dot-net.md)
- [Gegevens van een on-premises SQL Server Data Base naar Azure kopiëren](tutorial-hybrid-copy-powershell.md)
