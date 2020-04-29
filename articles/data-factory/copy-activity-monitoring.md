---
title: Kopieeractiviteit bewaken
description: Meer informatie over het controleren van de uitvoering van de Kopieer activiteit in Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: jingwang
ms.openlocfilehash: 47824095e892ca3c919d2d871feb612758ab2308
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81417841"
---
# <a name="monitor-copy-activity"></a>Kopieeractiviteit bewaken

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel wordt beschreven hoe u de uitvoering van de Kopieer activiteit kunt controleren in Azure Data Factory. Het is gebaseerd op het artikel overzicht van de [Kopieer activiteit](copy-activity-overview.md) . Dit geeft een algemeen overzicht van de Kopieer activiteit.

## <a name="monitor-visually"></a>Visueel bewaken

Zodra u een pijp lijn in Azure Data Factory hebt gemaakt en gepubliceerd, kunt u deze koppelen aan een trigger of hand matig een ad-hoc-uitvoering starten. U kunt al uw pijplijn uitvoeringen in de Azure Data Factory gebruikers ervaring zelf bewaken. Meer informatie over Azure Data Factory bewaking in het algemeen van het [visueel bewaken van Azure Data Factory](monitor-visually.md).

Als u de uitvoering van de Kopieer activiteit wilt controleren, gaat u naar uw data factory **Author & monitor** -gebruikers interface. Op het tabblad **monitor** ziet u een lijst met pijplijn uitvoeringen, klikt u op de koppeling van de **pijplijn naam** voor toegang tot de lijst met uitvoeringen van activiteiten in de pijplijn uitvoering.

![Uitvoering van Kopieer activiteit controleren](./media/copy-activity-overview/monitor-pipeline-run.png)

Op dit niveau ziet u koppelingen om de invoer van de activiteit, uitvoer en fouten te kopiëren (als de Kopieer activiteit is mislukt), evenals statistieken zoals duur/status. Als u op de knop **Details** (bril) naast de naam van de Kopieer activiteit klikt, krijgt u gedetailleerde informatie over de uitvoering van de Kopieer activiteit. 

![Uitvoering van Kopieer activiteit controleren](./media/copy-activity-overview/monitor-copy-activity-run.png)

In deze weer gave van de grafische bewaking geeft Azure Data Factory u de informatie over de uitvoering van de Kopieer activiteit, inclusief gegevens lezen/schrijven volume, het aantal bestanden/rijen gegevens dat is gekopieerd van bron naar sink, door Voer, de configuraties die worden toegepast voor uw Kopieer scenario, stappen die door de Kopieer activiteit worden uitgevoerd met de bijbehorende duur en Details. Raadpleeg [deze tabel](#monitor-programmatically) voor elke mogelijke metriek en de gedetailleerde beschrijving ervan. 

In sommige gevallen ziet u, wanneer u een Kopieer activiteit uitvoert in Data Factory, de weer gave voor het **afstemmen van prestaties** aan de bovenkant van de bewakings weergave voor het kopiëren van activiteiten, zoals wordt weer gegeven in het voor beeld. De tips geven u de knel punt aan die door ADF is geïdentificeerd voor de specifieke Kopieer uitvoering, samen met suggesties voor wat u moet wijzigen om de door Voer van de Kopieer capaciteit te verhogen. Meer informatie over [Tips voor automatisch afstemmen van prestaties](copy-activity-performance-troubleshooting.md#performance-tuning-tips).

In de **Details van de onderste uitvoering en de duur** worden de belangrijkste stappen beschreven waarmee uw Kopieer activiteit wordt uitgevoerd. Dit is met name handig bij het oplossen van problemen met de Kopieer prestaties. Het knel punt van de Kopieer uitvoering is de versie met de langste duur. Raadpleeg de [prestaties van de Kopieer activiteit oplossen](copy-activity-performance-troubleshooting.md) voor wat elke fase vertegenwoordigt en de gedetailleerde richt lijnen voor probleem oplossing.

**Voor beeld: kopiëren van Amazon S3 naar Azure Data Lake Storage Gen2**

![Details van de uitvoering van de Kopieer activiteit controleren](./media/copy-activity-overview/monitor-copy-activity-run-details.png)

## <a name="monitor-programmatically"></a>Programmatisch controleren

De details van de gegevens van de Kopieer activiteit en prestatie kenmerken worden ook weer gegeven in de sectie > **uitvoer** resultaat van de **Kopieer activiteit**, die wordt gebruikt om de weer gave van de UI-bewaking te genereren. Hieronder vindt u een volledige lijst met eigenschappen die kunnen worden geretourneerd. U ziet alleen de eigenschappen die van toepassing zijn op uw Kopieer scenario. Zie [programmatisch een Azure-Data Factory bewaken](monitor-programmatically.md)voor meer informatie over het uitvoeren van een programma voor het bewaken van activiteiten in het algemeen.

| Naam van eigenschap  | Beschrijving | Eenheid in uitvoer |
|:--- |:--- |:--- |
| dataRead | De werkelijke hoeveelheid gegevens die uit de bron is gelezen. | Int64-waarde, in bytes |
| dataWritten | De werkelijke koppeling van gegevens die zijn geschreven/doorgevoerd in de sink. De grootte kan afwijken van `dataRead` de grootte, omdat in elk gegevens archief de gegevens worden opgeslagen. | Int64-waarde, in bytes |
| filesRead | Het aantal bestanden dat is gelezen van de bron op basis van een bestand. | Int64-waarde (geen eenheid) |
| filesWritten | Het aantal bestanden dat is geschreven/doorgevoerd naar de op bestanden gebaseerde sink. | Int64-waarde (geen eenheid) |
| sourcePeakConnections | Het maximum aantal gelijktijdige verbindingen dat tot het bron gegevens archief is gemaakt tijdens het uitvoeren van de Kopieer activiteit. | Int64-waarde (geen eenheid) |
| sinkPeakConnections | Het maximum aantal gelijktijdige verbindingen dat tot stand is gebracht met het sink-gegevens archief tijdens het uitvoeren van de Kopieer activiteit. | Int64-waarde (geen eenheid) |
| rowsRead | Het aantal rijen dat is gelezen van de bron (niet van toepassing op een binaire kopie). | Int64-waarde (geen eenheid) |
| rowsCopied | Het aantal rijen dat is gekopieerd naar de Sink (niet van toepassing op een binaire kopie). | Int64-waarde (geen eenheid) |
| rowsSkipped | Aantal niet-compatibele rijen dat is overgeslagen. U kunt incompatibele rijen inschakelen door in te stellen `enableSkipIncompatibleRow` op waar. | Int64-waarde (geen eenheid) |
| copyDuration | De duur van de Kopieer uitvoering. | Int32-waarde, in seconden |
| doorvoer | Frequentie van gegevens overdracht. | Drijvende-komma getal, in KBps |
| sourcePeakConnections | Het maximum aantal gelijktijdige verbindingen dat tot het bron gegevens archief is gemaakt tijdens het uitvoeren van de Kopieer activiteit. | Int32-waarde (geen eenheid) |
| sinkPeakConnections| Het maximum aantal gelijktijdige verbindingen dat tot stand is gebracht met het sink-gegevens archief tijdens het uitvoeren van de Kopieer activiteit.| Int32-waarde (geen eenheid) |
| sqlDwPolyBase | Hiermee wordt aangegeven of poly Base wordt gebruikt wanneer gegevens naar SQL Data Warehouse worden gekopieerd. | Booleaans |
| redshiftUnload | Hiermee wordt aangegeven of verwijderen wordt gebruikt wanneer gegevens uit Redshift worden gekopieerd. | Booleaans |
| hdfsDistcp | Hiermee wordt aangegeven of DistCp wordt gebruikt wanneer gegevens worden gekopieerd uit HDFS. | Booleaans |
| effectiveIntegrationRuntime | Het uitvoeren van de uitvoering van de activiteit (IR) of runtime (Integration runtime), in `<IR name> (<region if it's Azure IR>)`de indeling. | Tekst (teken reeks) |
| usedDataIntegrationUnits | De efficiënte gegevens integratie-eenheden tijdens het kopiëren. | Int32-waarde |
| usedParallelCopies | De effectief parallelCopies tijdens de Kopieer activiteit. | Int32-waarde |
| redirectRowPath | Pad naar het logboek van overgeslagen rijen die niet compatibel zijn in de Blob-opslag `redirectIncompatibleRowSettings` die u configureert in de eigenschap. Zie [fout tolerantie](copy-activity-overview.md#fault-tolerance). | Tekst (teken reeks) |
| executionDetails | Meer details over de stadia waarin de Kopieer activiteit wordt uitgevoerd, en de bijbehorende stappen, duur, configuraties, enzovoort. We raden u aan deze sectie niet te parseren omdat deze kan worden gewijzigd. Raadpleeg de sectie [visueel bewaken](#monitor-visually) voor meer informatie over de manier waarop u de prestaties van kopiëren kunt begrijpen en oplossen. | Matrix |
| perfRecommendation | Tips voor het afstemmen van de prestaties kopiëren. Zie [Tips voor het afstemmen van prestaties](copy-activity-performance-troubleshooting.md#performance-tuning-tips) voor meer informatie. | Matrix |

**Hierbij**

```json
"output": {
    "dataRead": 1180089300500,
    "dataWritten": 1180089300500,
    "filesRead": 110,
    "filesWritten": 110,
    "sourcePeakConnections": 640,
    "sinkPeakConnections": 1024,
    "copyDuration": 388,
    "throughput": 2970183,
    "errors": [],
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)",
    "usedDataIntegrationUnits": 128,
    "billingReference": "{\"activityType\":\"DataMovement\",\"billableDuration\":[{\"Managed\":11.733333333333336}]}",
    "usedParallelCopies": 64,
    "executionDetails": [
        {
            "source": {
                "type": "AmazonS3"
            },
            "sink": {
                "type": "AzureBlobFS",
                "region": "East US",
                "throttlingErrors": 6
            },
            "status": "Succeeded",
            "start": "2020-03-04T02:13:25.1454206Z",
            "duration": 388,
            "usedDataIntegrationUnits": 128,
            "usedParallelCopies": 64,
            "profile": {
                "queue": {
                    "status": "Completed",
                    "duration": 2
                },
                "transfer": {
                    "status": "Completed",
                    "duration": 386,
                    "details": {
                        "listingSource": {
                            "type": "AmazonS3",
                            "workingDuration": 0
                        },
                        "readingFromSource": {
                            "type": "AmazonS3",
                            "workingDuration": 301
                        },
                        "writingToSink": {
                            "type": "AzureBlobFS",
                            "workingDuration": 335
                        }
                    }
                }
            },
            "detailedDurations": {
                "queuingDuration": 2,
                "transferDuration": 386
            }
        }
    ],
    "perfRecommendation": [
        {
            "Tip": "6 write operations were throttled by the sink data store. To achieve better performance, you are suggested to check and increase the allowed request rate for Azure Data Lake Storage Gen2, or reduce the number of concurrent copy runs and other data access, or reduce the DIU or parallel copy.",
            "ReferUrl": "https://go.microsoft.com/fwlink/?linkid=2102534 ",
            "RuleName": "ReduceThrottlingErrorPerfRecommendationRule"
        }
    ],
    "durationInQueue": {
        "integrationRuntimeQueue": 0
    }
}
```

## <a name="next-steps"></a>Volgende stappen
Zie de andere artikelen over Kopieer activiteiten:

\-[Overzicht van de Kopieer activiteit](copy-activity-overview.md)

\- [Prestaties van kopieeractiviteit](copy-activity-performance.md)