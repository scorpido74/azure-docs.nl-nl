---
title: Activiteit kopiëren in Azure Data Factory
description: Meer informatie over de activiteit Kopiëren in Azure Data Factory. U het gebruiken om gegevens uit een ondersteund brongegevensarchief te kopiëren naar een ondersteund sinkdataarchief.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: jingwang
ms.openlocfilehash: edb80c604951a140d21e3775eec3f1dc6d55af73
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80421415"
---
# <a name="copy-activity-in-azure-data-factory"></a>Activiteit kopiëren in Azure Data Factory

> [!div class="op_single_selector" title1="Selecteer de versie van Data Factory die u gebruikt:"]
> * [Versie 1](v1/data-factory-data-movement-activities.md)
> * [Huidige versie](copy-activity-overview.md)

In Azure Data Factory u de activiteit Kopiëren gebruiken om gegevens te kopiëren tussen gegevensarchieven die zich on-premises en in de cloud bevinden. Nadat u de gegevens hebt gekopieerd, u andere activiteiten gebruiken om deze verder te transformeren en te analyseren. U de activiteit Kopiëren ook gebruiken om transformatie- en analyseresultaten te publiceren voor business intelligence (BI) en toepassingsverbruik.

![De rol van de activiteit Kopiëren](media/copy-activity-overview/copy-activity.png)

De activiteit Kopiëren wordt uitgevoerd op een [internetverbinding](concepts-integration-runtime.md). U verschillende typen integratie-runtimes gebruiken voor verschillende scenario's voor gegevenskopiëren:

* Wanneer u gegevens kopieert tussen twee gegevensopslag die vanaf elk IP-adres openbaar toegankelijk zijn via internet, u de runtime voor Azure-integratie gebruiken voor de kopieeractiviteit. Deze runtime voor integratie is veilig, betrouwbaar, schaalbaar en [wereldwijd beschikbaar.](concepts-integration-runtime.md#integration-runtime-location)
* Wanneer u gegevens kopieert van en naar gegevensopslag die zich on-premises of in een netwerk met toegangscontrole bevinden (bijvoorbeeld een virtueel Azure-netwerk), moet u een runtime voor zelfgehoste integratie instellen.

Een integratieruntijd moet worden gekoppeld aan elke bron- en sinkgegevensopslag. Zie Bepalen welke IR moet worden gebruikt voor informatie over hoe de activiteit Kopiëren bepaalt welke indeintegratieruntime [moet worden gebruikt.](concepts-integration-runtime.md#determining-which-ir-to-use)

Als u gegevens van een bron naar een gootsteen wilt kopiëren, voert de service die de activiteit Kopiëren uitvoert de volgende stappen uit:

1. Hiermee leest u gegevens uit een brongegevensarchief.
2. Hiermee wordt serialisatie/deserialisatie, compressie/decompressie, kolomtoewijzing enzovoort uitgevoerd. Deze bewerkingen worden uitgevoerd op basis van de configuratie van de invoergegevensset, uitvoergegevensset en Kopieeractiviteit.
3. Hiermee schrijft u gegevens naar het opslagarchief sink/bestemming.

![Overzicht van kopieeractiviteiten](media/copy-activity-overview/copy-activity-overview.png)

## <a name="supported-data-stores-and-formats"></a>Ondersteunde gegevensarchieven en -indelingen

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores.md)]

### <a name="supported-file-formats"></a>Ondersteunde bestandsindelingen

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

U de activiteit Kopiëren gebruiken om bestanden te kopiëren tussen twee gegevensarchieven op basis van bestanden, in welk geval de gegevens efficiënt worden gekopieerd zonder serialisatie of deserialisatie. Bovendien u ook ontken of bestanden van een bepaalde indeling genereren, bijvoorbeeld, u het volgende uitvoeren:

* Kopieer gegevens uit een on-premises SQL Server-database en schrijf naar Azure Data Lake Storage Gen2 in parketindeling.
* Kopieer bestanden in de CSV-indeling (text) vanuit een on-premises bestandssysteem en schrijf naar Azure Blob-opslag in Avro-indeling.
* Kopieer bestanden met rits en bestanden uit een on-premises bestandssysteem, decomprimeer ze on-the-fly en schrijf geëxtraheerde bestanden naar Azure Data Lake Storage Gen2.
* Kopieer gegevens in de CSV-indeling (Gzip compressed-text) vanuit Azure Blob-opslag en schrijf deze naar Azure SQL Database.
* Veel meer activiteiten die serialisatie/deserialisatie of compressie/decompressie vereisen.

## <a name="supported-regions"></a>Ondersteunde regio’s

De service waarmee de activiteit Kopiëren wordt ingeschakeld, is wereldwijd beschikbaar in de regio's en regio's die worden vermeld in [runtime-locaties voor Azure-integratie.](concepts-integration-runtime.md#integration-runtime-location) De wereldwijd beschikbare topologie zorgt voor een efficiënte gegevensverplaatsing die hop tussen regio's meestal vermijdt. Zie [Producten per regio](https://azure.microsoft.com/regions/#services) om de beschikbaarheid van Data Factory en gegevensverkeer in een bepaalde regio te controleren.

## <a name="configuration"></a>Configuratie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Als u de activiteit Kopiëren in Azure Data Factory wilt gebruiken, moet u het tabblad:

1. **Maak gekoppelde services voor het brongegevensarchief en het sink datastore.** U vindt de lijst met ondersteunde connectors in het gedeelte [Ondersteunde gegevensopslag en -indelingen](#supported-data-stores-and-formats) van dit artikel. Raadpleeg de sectie 'Gekoppelde service-eigenschappen' van het connectorartikel voor configuratie-informatie en ondersteunde eigenschappen. 
2. **Maak gegevenssets voor de bron en gootsteen.** Raadpleeg de secties 'Gegevensseteigenschappen' van de bron- en sinkconnectorartikelen voor configuratie-informatie en ondersteunde eigenschappen.
3. **Maak een pijplijn met de activiteit Kopiëren.** De volgende sectie geeft een voorbeeld.

### <a name="syntax"></a>Syntaxis

De volgende sjabloon van een kopieeractiviteit bevat een volledige lijst met ondersteunde eigenschappen. Geef de opties op die bij uw scenario passen.

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

#### <a name="syntax-details"></a>Syntaxisdetails

| Eigenschap | Beschrijving | Vereist? |
|:--- |:--- |:--- |
| type | Voor een kopieeractiviteit wordt ingesteld op`Copy` | Ja |
| Ingangen | Geef de gegevensset op die u hebt gemaakt en die naar de brongegevens verwijst. De activiteit Kopiëren ondersteunt slechts één invoer. | Ja |
| Uitgangen | Geef de gegevensset op die u hebt gemaakt en die naar de sinkgegevens verwijst. De activiteit Kopiëren ondersteunt slechts één uitvoer. | Ja |
| typeProperties | Geef eigenschappen op om de activiteit Kopiëren te configureren. | Ja |
| source | Geef het type kopiebron en de bijbehorende eigenschappen op voor het ophalen van gegevens.<br/>Zie de sectie Activiteitseigenschappen kopiëren in het verbindingsartikel in [ondersteunde gegevensarchieven en -indelingen](#supported-data-stores-and-formats)voor meer informatie. | Ja |
| Wastafel | Geef het type kopieergootsteen en de bijbehorende eigenschappen voor het schrijven van gegevens op.<br/>Zie de sectie Activiteitseigenschappen kopiëren in het verbindingsartikel in [ondersteunde gegevensarchieven en -indelingen](#supported-data-stores-and-formats)voor meer informatie. | Ja |
| vertalen | Geef expliciete kolomtoewijzingen op van bron tot gootsteen. Deze eigenschap is van toepassing wanneer het standaard kopieergedrag niet aan uw behoeften voldoet.<br/>Zie [Schematoewijzing in kopieeractiviteit voor](copy-activity-schema-and-type-mapping.md)meer informatie . | Nee |
| dataIntegrationUnits | Geef een maat op die de hoeveelheid stroom vertegenwoordigt die de runtime van [azure-integratie](concepts-integration-runtime.md) gebruikt voor gegevenskopiëren. Deze eenheden stonden voorheen bekend als cloud Data Movement Units (DMU). <br/>Zie [Data Integration Units](copy-activity-performance-features.md#data-integration-units)voor meer informatie. | Nee |
| parallellekopieën | Geef de parallellisme op die u wilt dat de activiteit Kopiëren gebruikt bij het lezen van gegevens uit de bron en het schrijven van gegevens naar de gootsteen.<br/>Zie [Parallelle kopie](copy-activity-performance-features.md#parallel-copy)voor meer informatie . | Nee |
| Behouden | Geef op of metagegevens/ACL's moeten worden bewaard tijdens het kopiëren van gegevens. <br/>Zie [Metagegevens behouden voor](copy-activity-preserve-metadata.md)meer informatie . |Nee |
| inschakelen<br/>stagingInstellingen | Geef op of u de tussentijdse gegevens wilt fasen in blobopslag in plaats van rechtstreeks gegevens van bron naar gootsteen te kopiëren.<br/>Zie [Gefaseerde kopie](copy-activity-performance-features.md#staged-copy)voor informatie over nuttige scenario's en configuratiedetails. | Nee |
| enableSkipIncompatibleRow<br/>redirectIncompatibleRowSettings| Kies hoe u onverenigbare rijen verwerkt wanneer u gegevens van bron naar gootsteen kopieert.<br/>Zie [Fouttolerantie](copy-activity-fault-tolerance.md)voor meer informatie. | Nee |

## <a name="monitoring"></a>Bewaking

U de activiteit Kopiëren in de Azure Data Factory zowel visueel als programmatisch controleren. Zie [Kopieeractiviteit controleren](copy-activity-monitoring.md)voor meer informatie .

## <a name="incremental-copy"></a>Incrementele kopie

Met Data Factory u deltagegevens stapsgewijs kopiëren van een brongegevensarchief naar een sink data store. Zie [Zelfstudie: Gegevens stapsgewijs kopiëren](tutorial-incremental-copy-overview.md)voor meer informatie.

## <a name="performance-and-tuning"></a>Prestaties en afstemmen

De [ervaring voor het controleren van kopieeractiviteiten](copy-activity-monitoring.md) toont u de kopieerprestatiestatistieken voor elk van uw activiteiten. In [de handleiding voor prestaties en schaalbaarheid kopiëren](copy-activity-performance.md) worden belangrijke factoren beschreven die de prestaties van gegevensverkeer beïnvloeden via de kopieeractiviteit in Azure Data Factory. Het bevat ook de prestatiewaarden die tijdens het testen worden waargenomen en bespreekt hoe de prestaties van de kopieeractiviteit kunnen worden geoptimaliseerd.

## <a name="resume-from-last-failed-run"></a>Hervatten van de laatste mislukte run

Kopieeractiviteit ondersteunt hervatten van de laatste mislukte run wanneer u een grote omvang van bestanden kopieert met een binaire indeling tussen bestandenopslag en ervoor kiest om de map/bestandshiërarchie van bron naar gootsteen te behouden, bijvoorbeeld om gegevens van Amazon S3 naar Azure Data Lake Storage Gen2 te migreren. Het is van toepassing op de volgende bestandsgebaseerde connectors: [Amazon S3,](connector-amazon-simple-storage-service.md) [Azure Blob,](connector-azure-blob-storage.md) [Azure Data Lake Storage Gen1,](connector-azure-data-lake-store.md) [Azure Data Lake Storage Gen2,](connector-azure-data-lake-storage.md) [Azure File Storage](connector-azure-file-storage.md), File [System](connector-file-system.md), [FTP,](connector-ftp.md) [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md)en [SFTP](connector-sftp.md).

U het hervatten van de kopieeractiviteit op de volgende twee manieren gebruiken:

- **Opnieuw proberen op activiteitenniveau:** U het aantal gegevens opnieuw instellen op kopieeractiviteit. Als deze kopieeractiviteit mislukt tijdens de uitvoering van de pijplijn, begint de volgende automatische poging vanaf het foutpunt van de laatste proef.
- **Opnieuw worden uitgevoerd vanaf mislukte activiteit:** Nadat de pijplijnuitvoering is voltooid, u ook een rerun activeren vanuit de mislukte activiteit in de ADF-ui-bewakingsweergave of programmatisch. Als de mislukte activiteit een kopieeractiviteit is, wordt de pijplijn niet alleen opnieuw uitgevoerd vanuit deze activiteit, maar ook hervat vanaf het mislukte punt van de vorige run.

    ![Cv kopiëren](media/copy-activity-overview/resume-copy.png)

Enkele punten om op te merken:

- Cv gebeurt op bestandsniveau. Als kopieeractiviteit mislukt bij het kopiëren van een bestand, wordt dit specifieke bestand in de volgende run opnieuw gekopieerd.
- Als cv goed werkt, wijzigt u de instellingen voor kopieeractiviteit tussen de herhalingen niet.
- Wanneer u gegevens van Amazon S3, Azure Blob, Azure Data Lake Storage Gen2 en Google Cloud Storage kopieert, kan kopieeractiviteit worden hervat vanuit willekeurig aantal gekopieerde bestanden. Terwijl voor de rest van bestandsgebaseerde connectors als bron, op dit moment kopieeractiviteit ondersteunt hervatten van een beperkt aantal bestanden, meestal op het bereik van tienduizenden en varieert afhankelijk van de lengte van de bestandspaden; bestanden die verder gaan dan dit nummer, worden opnieuw gekopieerd tijdens herhalingen.

Voor andere scenario's dan binaire bestandskopie begint het opnieuw uitvoeren van kopieeractiviteiten vanaf het begin.

## <a name="preserve-metadata-along-with-data"></a>Metagegevens samen met gegevens bewaren

Terwijl u gegevens kopieert van bron naar gootsteen, u in scenario's zoals migratie van gegevensmeer er ook voor kiezen om de metagegevens en ACL's te behouden, samen met gegevens met kopieeractiviteit. Zie [Metagegevens behouden](copy-activity-preserve-metadata.md) voor meer informatie.

## <a name="schema-and-data-type-mapping"></a>Toewijzing van schema' s en gegevenstypen

Zie [Schema- en gegevenstypetoewijzing](copy-activity-schema-and-type-mapping.md) voor informatie over hoe de activiteit Kopiëren uw brongegevens naar uw gootsteen brengt.

## <a name="add-additional-columns-during-copy"></a>Extra kolommen toevoegen tijdens het kopiëren

Naast het kopiëren van gegevens uit het brongegevensarchief om te zinken, u ook configureren om extra gegevenskolommen toe te voegen om mee te kopiëren om te zinken. Bijvoorbeeld:

- Wanneer u uit een bestandsbron wordt gekopieerd, slaat u het relatieve bestandspad op als een extra kolom om te traceren van welk bestand de gegevens afkomstig zijn.
- Voeg een kolom toe met ADF-expressie om ADF-systeemvariabelen zoals pijplijnnaam/pijplijn-id toe te voegen of andere dynamische waarden op te slaan uit de uitvoer van upstream-activiteit.
- Voeg een kolom met statische waarde toe om aan uw downstream verbruiksbehoefte te voldoen.

U vindt de volgende configuratie op het tabblad bron van kopieeractiviteit: 

![Extra kolommen toevoegen in kopieeractiviteit](./media/copy-activity-overview/copy-activity-add-additional-columns.png)

>[!TIP]
>Deze functie werkt met het nieuwste gegevenssetmodel. Als u deze optie niet ziet vanuit de gebruikersinterface, probeert u een nieuwe gegevensset te maken.

Als u het programmatisch `additionalColumns` wilt configureren, voegt u de eigenschap toe aan de bron van kopieeractiviteit:

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| extraKolommen | Voeg extra gegevenskolommen toe om te kopiëren om te zinken.<br><br>Elk object `additionalColumns` onder de array vertegenwoordigt een extra kolom. Hiermee `name` definieert u de `value` kolomnaam en geeft het de gegevenswaarde van die kolom aan.<br><br>Toegestane gegevenswaarden zijn:<br>- **`$$FILEPATH`**- een gereserveerde variabele geeft aan dat het relatieve pad van de bronbestanden moet worden opgeslagen in het mappad dat is opgegeven in de gegevensset. Solliciteer op een bron op basis van bestanden.<br>- **Expressie**<br>- **Statische waarde** | Nee |

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyWithAdditionalColumns",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "<source type>",
                "additionalColumns": [
                    {
                        "name": "filePath",
                        "value": "$$FILEPATH"
                    },
                    {
                        "name": "pipelineName",
                        "value": {
                            "value": "@pipeline().Pipeline",
                            "type": "Expression"
                        }
                    },
                    {
                        "name": "staticValue",
                        "value": "sampleValue"
                    }
                ],
                ...
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="fault-tolerance"></a>Fouttolerantie

Standaard stopt de activiteit Kopiëren met het kopiëren van gegevens en retourneert een fout wanneer brongegevensrijen niet compatibel zijn met sinkgegevensrijen. Als u de kopie wilt laten slagen, u de activiteit Kopiëren configureren om de incompatibele rijen over te slaan en te registreren en alleen de compatibele gegevens te kopiëren. Zie [Activiteitsfouttolerantie kopiëren](copy-activity-fault-tolerance.md) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen
Bekijk de volgende quickstarts, tutorials en voorbeelden:

- [Gegevens van de ene locatie naar een andere locatie in hetzelfde Azure Blob-opslagaccount kopiëren](quickstart-create-data-factory-dot-net.md)
- [Gegevens uit Azure Blob-opslag kopiëren naar Azure SQL-database](tutorial-copy-data-dot-net.md)
- [Gegevens uit een on-premises SQL Server-database naar Azure kopiëren](tutorial-hybrid-copy-powershell.md)
