---
title: Kopieeractiviteit in Azure Data Factory | Microsoft Docs
description: Meer informatie over de kopieeractiviteit in Azure Data Factory die u gebruiken kunt om gegevens te kopiëren van een ondersteund brongegevensarchief naar een ondersteunde sink-gegevensarchief.
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
ms.openlocfilehash: 23ae7b5cfec26fb2483a3e4ac13a1220888d76ee
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69614262"
---
# <a name="copy-activity-in-azure-data-factory"></a>De Kopieeractiviteit in Azure Data Factory

## <a name="overview"></a>Overzicht

> [!div class="op_single_selector" title1="Selecteer de versie van Data Factory service die u gebruikt:"]
> * [Versie 1:](v1/data-factory-data-movement-activities.md)
> * [Huidige versie](copy-activity-overview.md)

In Azure Data Factory, kunt u Kopieeractiviteit om gegevens tussen gegevens winkels zich on-premises en in de cloud te kopiëren. Nadat de gegevens zijn gekopieerd, kunnen ze verder worden getransformeerd en geanalyseerd met andere activiteiten. U kunt ook een Kopieeractiviteit gebruiken om transformatie en analyseresultaten voor business intelligence (BI) en het verbruik van de toepassing te publiceren.

![Rol van Kopieeractiviteit](media/copy-activity-overview/copy-activity.png)

Kopieeractiviteit wordt uitgevoerd op een [Integration Runtime](concepts-integration-runtime.md). Voor een ander scenario voor het kopiëren van gegevens kunnen verschillende soorten Integration Runtime worden gebruikt:

* Bij het kopiëren van gegevens tussen gegevens archieven die via internet openbaar toegankelijk zijn vanuit elke Ip's, kan de Kopieer activiteit worden toegestaan door **Azure Integration runtime**, dat veilig, betrouwbaar, schaalbaar en [wereld wijd beschikbaar](concepts-integration-runtime.md#integration-runtime-location)is.
* Wanneer gegevens kopiëren van/naar de opgeslagen gegevens zich on-premises of in een netwerk met toegangsbeheer (bijvoorbeeld Azure Virtual Network), moet u voor het instellen van een **geïntegreerde Runtime zelf-hostend** om te kopiëren van gegevens.

Integratieruntime moet worden gekoppeld aan elke bron en sink-gegevensopslag. Meer informatie over hoe u kopieeractiviteit [bepaalt welke IR u moet gebruiken](concepts-integration-runtime.md#determining-which-ir-to-use).

Kopieeractiviteit verloopt via de volgende fasen om gegevens te kopiëren van een bron naar een sink. De service die wordt gebruikt door de Kopieeractiviteit:

1. Leest gegevens uit een bron-gegevensopslag.
2. Voert serialisatie/deserialisatie, compressie/decompressie, kolomtoewijzing, enzovoort. Dit gebeurt deze bewerkingen op basis van de configuraties van de invoergegevensset, de uitvoergegevensset en de Kopieeractiviteit.
3. Schrijft gegevens naar het sink/doelgegevensarchief.

![Overzicht kopieeractiviteit](media/copy-activity-overview/copy-activity-overview.png)

## <a name="supported-data-stores-and-formats"></a>Ondersteunde gegevensarchieven en indelingen

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores.md)]

### <a name="supported-file-formats"></a>Ondersteunde bestandsindelingen

U kunt Copy-activiteit naar **bestanden als kopiëren-is** tussen twee bestanden zijn gebaseerd, in dat geval de gegevens worden gekopieerd efficiënt zonder een serialisatie/deserialisatie.

Kopieer activiteit biedt ook ondersteuning voor lezen van en schrijven naar bestanden in opgegeven indelingen: **Text, JSON, AVRO, Orc en Parquet**, en comprimeren en decomprimeren van bestanden met de volgende codecs: **Gzip, Deflate, bzip2 en ZipDeflate**. Zie [ondersteunde indelingen voor bestanden en compressie](supported-file-formats-and-compression-codecs.md) met details.

U kunt bijvoorbeeld de volgende kopieeractiviteiten doen:

* Kopieer gegevens in on-premises SQL Server en schrijf naar Azure Data Lake Storage Gen2 in de Parquet-indeling.
* Bestanden kopiëren in tekstindeling (CSV) van on-premises bestandssysteem en naar Azure-Blob in Avro-indeling wilt schrijven.
* Kopieer gezipte bestanden van het on-premises bestands systeem en decomprimeren vervolgens land tot Azure Data Lake Storage Gen2.
* Kopiëren van gegevens in de indeling van GZip gecomprimeerde tekstbestand (CSV) van Azure Blob en schrijven naar Azure SQL Database.
* En veel meer gevallen waarvoor serialisatie/deserialisatie of compressie/decompressie nodig is.

## <a name="supported-regions"></a>Ondersteunde regio’s

De service die wordt gebruikt door de Kopieeractiviteit is wereldwijd beschikbaar in de regio's en regio's die worden vermeld in [Azure Integration Runtime locaties](concepts-integration-runtime.md#integration-runtime-location). De algemeen beschikbare topologie zorgt u ervoor efficiënt gegevensverplaatsing die meestal regio-overschrijdende hops voorkomt. Zie [-Services per regio](https://azure.microsoft.com/regions/#services) voor beschikbaarheid van Data Factory en de verplaatsing van gegevens in een regio.

## <a name="configuration"></a>Configuratie

Voor het gebruik van kopieeractiviteit in Azure Data Factory, moet u naar:

1. **Maak gekoppelde services voor gegevensopslag van de bron en sink-gegevensopslag.** Raadpleeg het connector-artikel 'Gekoppelde service-eigenschappen' gedeelte over het configureren en de ondersteunde eigenschappen. U vindt de lijst met ondersteunde connector in [ondersteunde gegevensarchieven en indelingen](#supported-data-stores-and-formats) sectie.
2. **Gegevenssets voor de bron en sink maken.** Raadpleeg het hoofd artikel over eigenschappen van de bron-en Sink-connector voor informatie over het configureren en de ondersteunde eigenschappen.
3. **Een pijplijn maken met de kopieeractiviteit.** De volgende sectie bevat een voorbeeld.

### <a name="syntax"></a>Syntaxis

De volgende sjabloon van een kopieeractiviteit bevat een uitgebreide lijst met ondersteunde eigenschappen. Geef de waarden die geschikt zijn voor uw scenario.

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

### <a name="syntax-details"></a>Syntaxis van de details

| Eigenschap | Description | Vereist |
|:--- |:--- |:--- |
| Type | De eigenschap type van een Kopieer activiteit moet worden ingesteld op: **Kopieer** | Ja |
| inputs | Geef op de gegevensset die u hebt gemaakt die verwijst naar de brongegevens. Kopieeractiviteit ondersteunt alleen een één invoer. | Ja |
| outputs | Geef op de gegevensset die u hebt gemaakt die verwijst naar de sink-gegevens. Kopieeractiviteit ondersteunt slechts één uitvoer. | Ja |
| typeProperties | Een groep met eigenschappen te configureren van de kopieeractiviteit. | Ja |
| source | Het brontype voor kopiëren en de bijbehorende eigenschappen opgeven over het ophalen van gegevens.<br/><br/>Meer details in de sectie 'Eigenschappen van de Kopieeractiviteit' connector-artikel die worden vermeld in [ondersteunde gegevensarchieven en indelingen](#supported-data-stores-and-formats). | Ja |
| sink | Geef de sink-type voor kopiëren en de bijbehorende eigenschappen voor het schrijven van gegevens.<br/><br/>Meer details in de sectie 'Eigenschappen van de Kopieeractiviteit' connector-artikel die worden vermeld in [ondersteunde gegevensarchieven en indelingen](#supported-data-stores-and-formats). | Ja |
| translator | Geef expliciete kolomtoewijzingen van bron naar een sink. Is van toepassing wanneer het standaardgedrag van het exemplaar kan niet voldoen aan uw behoeften.<br/><br/>Informatie over de details van [toewijzing van het type Schema en gegevens](copy-activity-schema-and-type-mapping.md). | Nee |
| dataIntegrationUnits | Geef de powerfulness van [Azure Integration Runtime](concepts-integration-runtime.md) om te kopiëren van gegevens. Voorheen bekend als cloud Data Movement eenheden (DMU). <br/><br/>Informatie over de details van [integratie gegevenseenheden](copy-activity-performance.md#data-integration-units). | Nee |
| parallelCopies | Geef de parallelle uitvoering die u wilt dat Copy-activiteit naar het sink-wordt gebruikt bij het lezen van gegevens uit de bron en het schrijven van gegevens.<br/><br/>Informatie over de details van [kopie parallelle](copy-activity-performance.md#parallel-copy). | Nee |
| enableStaging<br/>stagingSettings | Kies ervoor om de tussenliggende gegevens in een Blob-opslag te plaatsen in plaats van gegevens rechtstreeks van bron naar sink te kopiëren.<br/><br/>Meer informatie over de handige scenario's en de configuratiedetails van de van [gefaseerd kopiëren](copy-activity-performance.md#staged-copy). | Nee |
| enableSkipIncompatibleRow<br/>redirectIncompatibleRowSettings| Kiezen hoe u voor het afhandelen van incompatibele rijen tijdens het kopiëren van gegevens van bron naar een sink.<br/><br/>Informatie over de details van [fouttolerantie](copy-activity-fault-tolerance.md). | Nee |

## <a name="monitoring"></a>Bewaking

U kunt controleren met de kopieeractiviteit in Azure Data Factory 'Maken en controleren' gebruikersinterface of via een programma uitvoeren.

### <a name="monitor-visually"></a>Visueel bewaken

Als u wilt de copy-activiteit uitvoeren visueel te controleren, Ga naar uw data factory -> **Author & Monitor** -> **controletabblad**, ziet u een lijst van de pijplijn wordt uitgevoerd met een koppeling 'Uitvoeringen van activiteit weergeven' in de  **Acties** kolom.

![Pijplijnuitvoeringen controleren](./media/load-data-into-azure-data-lake-store/monitor-pipeline-runs.png)

Klik om te zien van de lijst met activiteiten in deze pijplijn-run. In de **acties** kolom, hebt u koppelingen naar de kopie activiteit invoer, uitvoer, fouten (als de uitvoering van activiteit kopiëren mislukt) en details.

![Uitvoering van activiteiten controleren](./media/load-data-into-azure-data-lake-store/monitor-activity-runs.png)

Klik op de '**Details**' koppeling onder **acties** om te zien van de kopieeractiviteit uitvoeringsdetails en prestatiekenmerken. Hier ziet u informatie, inclusief volume/rijen/bestanden van de gegevens opgehaald uit de bron om op te vangen, doorvoer, stappen gaat door met de bijbehorende tijdsduur en gebruikt als deze configuraties voor uw scenario kopiëren.

>[!TIP]
>Voor sommige scenario's ziet u ook ' tips voor het afstemmen van**prestaties**' boven op de pagina controle van het kopiëren van gegevens. hier wordt uitgelegd hoe u de knel punten hebt geïdentificeerd en kunt u zien wat er moet worden gewijzigd, zodat u de Kopieer doorvoer kunt verhogen. [hier volgt](#performance-and-tuning)een voor beeld met informatie.

**Voorbeeld: kopiëren van Amazon S3 naar Azure Data Lake Store**
![details uitvoering van activiteiten controleren](./media/copy-activity-overview/monitor-activity-run-details-adls.png)

**Voorbeeld: kopiëren uit een Azure SQL Database naar Azure SQL Data Warehouse met behulp van gefaseerd kopiëren**
![details uitvoering van activiteiten controleren](./media/copy-activity-overview/monitor-activity-run-details-sql-dw.png)

### <a name="monitor-programmatically"></a>Controleren via een programma

Uitvoerings Details van de Kopieer activiteit en prestatie kenmerken worden ook weer gegeven in de sectie uitvoer resultaten van de Kopieer activiteit >. Hieronder vindt u een volledige lijst. alleen van toepassing die op uw scenario kopiëren worden weergegeven. Informatie over het bewaken van de uitvoering van activiteiten [snelstartgids sectie bewaking](quickstart-create-data-factory-dot-net.md#monitor-a-pipeline-run).

| Naam van eigenschap  | Description | Eenheid |
|:--- |:--- |:--- |
| dataRead | Grootte van de gegevens lezen van bron | Waarde voor Int64 in **bytes** |
| datawritten door | Grootte van gegevens naar een sink geschreven | Waarde voor Int64 in **bytes** |
| filesRead | Het aantal bestanden die bij het kopiëren van gegevens uit de opslag van bestanden worden gekopieerd. | Waarde voor Int64 (geen unit) |
| filesWritten | Het aantal bestanden die bij het kopiëren van gegevens met file storage worden gekopieerd. | Waarde voor Int64 (geen unit) |
| sourcePeakConnections | Het maximum aantal gelijktijdige verbindingen dat tot het bron gegevens archief is gemaakt tijdens het uitvoeren van de Kopieer activiteit. | Waarde voor Int64 (geen unit) |
| sinkPeakConnections | Het maximum aantal gelijktijdige verbindingen dat tot het sink-gegevens archief is gemaakt tijdens het uitvoeren van de Kopieer activiteit. | Waarde voor Int64 (geen unit) |
| rowsRead | Het aantal rijen dat wordt gelezen uit de bron (niet van toepassing op een binaire kopie). | Waarde voor Int64 (geen unit) |
| rowsCopied | Het aantal rijen dat naar de Sink moet worden gekopieerd (niet van toepassing op een binaire kopie). | Waarde voor Int64 (geen unit) |
| rowsSkipped | Het aantal niet-compatibele rijen wordt overgeslagen. U kunt de functie inschakelen door de set 'enableSkipIncompatibleRow' op ' True '. | Waarde voor Int64 (geen unit) |
| copyDuration | De duur van de kopie. | Int32-waarde in seconden |
| throughput | De verhouding waarmee gegevens worden overgebracht. | Drijvendekommagetal in **KB/s** |
| sourcePeakConnections | Het maximum aantal gelijktijdige verbindingen dat tot stand is gebracht tijdens het kopiëren van het bron gegevens archief. | Int32-waarde |
| sinkPeakConnections| Piek aantal gelijktijdige verbindingen dat tot stand is gebracht tijdens het kopiëren van het sink-gegevens archief.| Int32-waarde |
| sqlDwPolyBase | Als u PolyBase is gebruikt bij het kopiëren van gegevens in SQL Data Warehouse. | Booleaans |
| redshiftUnload | Als uit het geheugen laden wordt gebruikt bij het kopiëren van gegevens van Redshift. | Booleaans |
| hdfsDistcp | Als DistCp wordt gebruikt bij het kopiëren van gegevens uit HDFS. | Booleaans |
| effectiveIntegrationRuntime | Laten zien welke integratie Runtime(s) wordt gebruikt voor het maken van de activiteit die wordt uitgevoerd, in de indeling van `<IR name> (<region if it's Azure IR>)`. | Tekst (tekenreeks) |
| usedDataIntegrationUnits | De effectieve gegevens integratie eenheden tijdens het kopiëren. | Int32-waarde |
| usedParallelCopies | De effectieve parallelCopies tijdens het kopiëren. | Int32-waarde |
| redirectRowPath | Pad naar het logboek van overgeslagen incompatibele rijen in de blob-opslag die u onder "redirectIncompatibleRowSettings" configureren. Zie onderstaande voorbeeld. | Tekst (tekenreeks) |
| executionDetails | Meer informatie over de fasen copy-activiteit doorloopt, en de bijbehorende stappen, duur, gebruikte configuraties, enzovoort. Het is niet raadzaam deze sectie parseren als kan worden gewijzigd.<br/><br/>ADF rapporteert ook de gedetailleerde duur (in seconden) die is besteed aan de `detailedDurations`verschillende stappen onder. De duur van deze stappen is exclusief en alleen de waarden die van toepassing zijn op de opgegeven Kopieer activiteit, worden weer gegeven:<br/>- **Wachtrij duur** (`queuingDuration`): De verstreken tijd totdat de Kopieer activiteit daad werkelijk wordt gestart op de Integration runtime. Als u een zelf-hostende IR gebruikt en deze waarde groot is, kunt u het beste de IR-capaciteit en het gebruik controleren en omhoog/omlaag schalen op basis van uw werk belasting. <br/>- **Script duur vooraf kopiëren** (`preCopyScriptDuration`): De verstreken tijd tussen de Kopieer activiteit, te beginnen bij de IR-en kopieer activiteit, waarbij het vooraf kopiëren van het script in de Sink-gegevens opslag wordt voltooid. Toep assen wanneer u het script vóór kopiëren configureert. <br/>- **Time-to-first-byte** (`timeToFirstByte`): De verstreken tijd tussen het einde van de vorige stap en de IR waarbij de eerste byte van de brongegevens opslag wordt ontvangen. Toep assen op bron die niet op een bestand is gebaseerd. Als deze waarde groot is, Voorst Ellen om de query of server te controleren en te optimaliseren.<br/>- **Overdrachts duur** (`transferDuration`): De verstreken tijd tussen het einde van de vorige stap en de IR waarbij alle gegevens van de bron naar de Sink worden overgebracht. | Array |
| perfRecommendation | Tips voor het afstemmen van de prestaties kopiëren. Zie de sectie [prestaties en afstemmen](#performance-and-tuning) voor meer informatie. | Array |

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

Zie de [toewijzing van het type Schema en gegevens](copy-activity-schema-and-type-mapping.md), waarin wordt beschreven hoe copy activity in uw brongegevens naar het sink-kaarten.

## <a name="fault-tolerance"></a>Fouttolerantie

De Kopieer activiteit stopt standaard met het kopiëren van gegevens en retourneert een fout wanneer er incompatibele gegevens tussen de bron-en Sink-service worden aangetroffen. U kunt expliciet configureren als u wilt overslaan en meld u de niet-compatibele rijen en alleen deze compatibel gegevens kopiëren naar de kopie is voltooid. Zie de [Kopieeractiviteit fouttolerantie](copy-activity-fault-tolerance.md) op meer informatie.

## <a name="performance-and-tuning"></a>Prestaties en afstemmen

Zie de [Kopieeractiviteit prestatie- en afstemmingshandleiding](copy-activity-performance.md), waarin belangrijke factoren die invloed hebben op de prestaties van de verplaatsing van gegevens (Kopieeractiviteit) in Azure Data Factory wordt beschreven. Ook geeft een lijst van de waargenomen prestaties tijdens interne tests van en worden verschillende manieren om te optimaliseren de prestaties van Kopieeractiviteit besproken.

In sommige gevallen, wanneer u een Kopieer activiteit uitvoert in ADF, ziet u direct ' tips voor het afstemmen van**prestaties**' boven op de [pagina controle activiteit controleren](#monitor-visually) , zoals in het volgende voor beeld wordt weer gegeven. Dit geeft niet alleen aan dat de knel punt die is geïdentificeerd voor de opgegeven kopie, maar u doorloopt op wat u wilt wijzigen om de door voer te verbeteren. De tips voor het afstemmen van de prestaties bieden momenteel suggesties voor het gebruik van poly Base bij het kopiëren van gegevens naar Azure SQL Data Warehouse, om Azure Cosmos DB RU of Azure SQL DB-DTU te verg Roten als de resource aan gegevens opslag het knel punt is, om de overbodige gefaseerde verwijdering te verwijderen kopiëren, etc. De regels voor het afstemmen van de prestaties worden ook geleidelijk verrijkt.

**Voor beeld: kopiëren naar Azure SQL DB met tips voor het afstemmen van prestaties**

In dit voor beeld maakt ADF tijdens het uitvoeren van een Kopieer bewerking dat de Sink Azure SQL DB een hoog DTU-gebruik bereikt waardoor de schrijf bewerkingen worden vertraagd, zodat de suggestie de Azure SQL DB-laag met meer DTU kan verhogen.

![Bewaking met tips voor het afstemmen van prestaties kopiëren](./media/copy-activity-overview/copy-monitoring-with-performance-tuning-tips.png)

## <a name="incremental-copy"></a>Incrementele kopie
Data Factory ondersteunt scenario's voor het incrementeel kopiëren van Delta gegevens uit een brongegevens archief naar een Sink-gegevens archief. Zie [zelfstudie: gegevens stapsgewijs kopiëren](tutorial-incremental-copy-overview.md).

## <a name="read-and-write-partitioned-data"></a>Gepartitioneerde gegevens lezen en schrijven
Azure Data Factory ondersteund in versie 1, lezen of schrijven van gepartitioneerde gegevens met behulp van de slicestart-waarde/SliceEnd/WindowStart/WindowEnd systeemvariabelen. In de huidige versie, kunt u dit gedrag bewerkstelligen met behulp van een pijplijnparameter en de tijd/geplande begintijd van trigger als een waarde van de parameter. Zie voor meer informatie, [lezen of schrijven gegevens gepartitioneerd](how-to-read-write-partitioned-data.md).

## <a name="next-steps"></a>Volgende stappen
Zie de volgende snelstartgidsen, zelfstudies en voorbeelden:

- [Gegevens van de ene locatie naar een andere locatie in dezelfde Azure Blob Storage kopiëren](quickstart-create-data-factory-dot-net.md)
- [Gegevens kopiëren van Azure Blob Storage naar Azure SQL Database](tutorial-copy-data-dot-net.md)
- [Gegevens kopiëren van on-premises SQL Server naar Azure](tutorial-hybrid-copy-powershell.md)
