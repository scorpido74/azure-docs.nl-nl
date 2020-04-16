---
title: Kopieeractiviteit bewaken
description: Meer informatie over het controleren van de uitvoering van kopieeractiviteiten in Azure Data Factory.
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
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417841"
---
# <a name="monitor-copy-activity"></a>Kopieeractiviteit bewaken

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel wordt beschreven hoe u de uitvoering van kopieeractiviteiten in Azure Data Factory controleren. Het bouwt voort op de [kopie activiteit overzicht](copy-activity-overview.md) artikel dat een algemeen overzicht van kopieeractiviteit presenteert.

## <a name="monitor-visually"></a>Visueel bewaken

Zodra u een pijplijn hebt gemaakt en gepubliceerd in Azure Data Factory, u deze koppelen aan een trigger of handmatig een ad-hocrun starten. U al uw pijplijnuitvoeringen native controleren in de gebruikerservaring van Azure Data Factory. Meer informatie over Azure Data Factory-bewaking in het algemeen vanuit [Azure Data Factory .](monitor-visually.md)

Als u de activiteit kopiëren wilt controleren, gaat u naar de gebruikersinterface van de gegevensfabriek **Auteur & Monitor.** Klik op het tabblad **Monitor** een lijst met pijplijnuitvoeringen, klik op de koppeling **met pijplijnnaam** om toegang te krijgen tot de lijst met activiteitsruns in de pijplijnuitvoering.

![Uitvoeren van kopieeractiviteiten controleren](./media/copy-activity-overview/monitor-pipeline-run.png)

Op dit niveau u koppelingen zien om activiteitsinvoer, uitvoer en fouten te kopiëren (als de uitvoeren van de activiteit kopiëren mislukt), evenals statistieken zoals duur/status. Als u op de knop **Details** (bril) klikt naast de naam van de kopieeractiviteit, krijgt u diepgaande details over de uitvoering van uw kopieeractiviteit. 

![Uitvoeren van kopieeractiviteiten controleren](./media/copy-activity-overview/monitor-copy-activity-run.png)

In deze grafische bewakingsweergave presenteert Azure Data Factory u de uitvoeringsgegevens voor kopieeractiviteiten, inclusief gegevenslees-/geschreven volume, aantal bestanden/rijen gegevens die van bron naar gootsteen worden gekopieerd, doorvoer, de configuraties die zijn toegepast voor uw kopieerscenario, stappen die de kopieeractiviteit doormaakt met bijbehorende duur en details, en meer. Raadpleeg [deze tabel](#monitor-programmatically) op elke mogelijke statistiek en de gedetailleerde beschrijving ervan. 

In sommige scenario's ziet u bij het uitvoeren van een kopieeractiviteit in Gegevensfabriek **bovenaan** de weergave voor het controleren van kopieeractiviteiten, zoals in het voorbeeld wordt weergegeven. De tips vertellen u het knelpunt geïdentificeerd door ADF voor de specifieke kopie uit te voeren, samen met suggestie over wat te wijzigen om kopieerdoorvoer te stimuleren. Meer informatie over [tips voor het afstemmen van automatische prestaties](copy-activity-performance-troubleshooting.md#performance-tuning-tips).

De onderste **uitvoeringsgegevens en -duur** beschrijven de belangrijkste stappen die uw kopieeractiviteit doormaakt, wat vooral handig is voor het oplossen van problemen met de kopieerprestaties. Het knelpunt van uw copy run is degene met de langste duur. Raadpleeg [problemen met de prestaties van kopieeractiviteiten](copy-activity-performance-troubleshooting.md) voor wat elke fase vertegenwoordigt en de gedetailleerde richtlijnen voor het oplossen van problemen.

**Voorbeeld: Kopiëren van Amazon S3 naar Azure Data Lake Storage Gen2**

![Details van het uitvoeren van kopieeractiviteiten controleren](./media/copy-activity-overview/monitor-copy-activity-run-details.png)

## <a name="monitor-programmatically"></a>Programmatisch controleren

Details voor de uitvoering van kopieeractiviteiten en prestatiekenmerken worden ook geretourneerd in de sectie > **Resultaatuitvoer van** **kopieeractiviteit,** die wordt gebruikt om de weergave UI-controle weer te geven. Hieronder volgt een volledige lijst met eigenschappen die mogelijk worden geretourneerd. U ziet alleen de eigenschappen die van toepassing zijn op uw kopieerscenario. Zie Programmatisch controleren van [een Azure-gegevensfabriek](monitor-programmatically.md)voor informatie over het programmatisch controleren van activiteitsuitvoeringen.

| Naam van eigenschap  | Beschrijving | Eenheid in output |
|:--- |:--- |:--- |
| gegevensLezen | De werkelijke hoeveelheid gegevens die uit de bron wordt gelezen. | Int64-waarde, in bytes |
| dataGeschreven | De werkelijke mount van de gegevens geschreven / toegewijd aan de gootsteen. De grootte kan `dataRead` afwijken van de grootte, omdat het betrekking heeft op hoe elke gegevensopslag de gegevens opslaat. | Int64-waarde, in bytes |
| bestandenLezen | Het aantal bestanden dat wordt gelezen uit de bron op basis van bestanden. | Int64-waarde (geen eenheid) |
| bestandenGeschreven | Het aantal bestanden dat is geschreven/vastgelegd op de op bestanden gebaseerde sink. | Int64-waarde (geen eenheid) |
| bronPiekverbindingen | Piek aantal gelijktijdige verbindingen die tijdens de uitvoeren van de activiteit Kopiëren met het brongegevensarchief zijn ingesteld. | Int64-waarde (geen eenheid) |
| sinkPeakConnections | Piek aantal gelijktijdige verbindingen die tijdens de activiteitsrun kopiëren is ingesteld op het sink-gegevensarchief. | Int64-waarde (geen eenheid) |
| rijenLezen | Aantal rijen dat van de bron wordt gelezen (niet van toepassing op binaire kopie). | Int64-waarde (geen eenheid) |
| rijenGekopieerd | Aantal rijen gekopieerd om te zinken (niet van toepassing voor binaire kopie). | Int64-waarde (geen eenheid) |
| rijenOvergeslagen | Aantal incompatibele rijen die zijn overgeslagen. U incompatibele rijen inschakelen om `enableSkipIncompatibleRow` te worden overgeslagen door true in te stellen. | Int64-waarde (geen eenheid) |
| copyDuration | Duur van de kopie run. | Int32-waarde, in enkele seconden |
| doorvoer | Snelheid van gegevensoverdracht. | Zwevend puntnummer in KBps |
| bronPiekverbindingen | Piek aantal gelijktijdige verbindingen die tijdens de uitvoeren van de activiteit Kopiëren met het brongegevensarchief zijn ingesteld. | Int32-waarde (geen eenheid) |
| sinkPeakConnections| Piek aantal gelijktijdige verbindingen die tijdens de activiteitsrun kopiëren is ingesteld op het sink-gegevensarchief.| Int32-waarde (geen eenheid) |
| sqlDwPolyBase | Of PolyBase wordt gebruikt wanneer gegevens worden gekopieerd naar SQL Data Warehouse. | Booleaans |
| roodverschuivingUitladen | Of UNLOAD wordt gebruikt wanneer gegevens worden gekopieerd van Redshift. | Booleaans |
| hdfsDistcp | Of DistCp wordt gebruikt wanneer gegevens worden gekopieerd van HDFS. | Booleaans |
| effectiefIntegrationRuntime | De inburgeringsruntime (IR) of de runtimes `<IR name> (<region if it's Azure IR>)`die worden gebruikt om de activiteit smachten in de indeling . | Tekst (tekenreeks) |
| usedDataIntegrationUnits | De effectieve Data Integration Units tijdens het kopiëren. | Int32-waarde |
| gebruikte ParallelCopies | De effectieve parallelKopieën tijdens de kopie. | Int32-waarde |
| redirectRowPath | Pad naar het logboek van overgeslagen incompatibele rijen `redirectIncompatibleRowSettings` in de blobopslag die u in de eigenschap configureert. Zie [Fouttolerantie](copy-activity-overview.md#fault-tolerance). | Tekst (tekenreeks) |
| uitvoeringDetails | Meer informatie over de fasen waarop de kopieeractiviteit wordt uitgevoerd en de bijbehorende stappen, duur, configuraties, enzovoort. We raden u niet aan deze sectie te ontzien, omdat deze mogelijk verandert. Als u beter wilt begrijpen hoe het u helpt de kopieerprestaties te begrijpen en op te lossen, raadpleegt u de sectie [Visueel controleren.](#monitor-visually) | Matrix |
| perfAanbeveling | Kopieer prestatietuningtips. Zie Tips voor [het afstemmen van prestaties](copy-activity-performance-troubleshooting.md#performance-tuning-tips) voor meer informatie. | Matrix |

**Voorbeeld:**

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
Zie de andere artikelen Copy Activity:

\-[Overzicht van activiteit kopiëren](copy-activity-overview.md)

\- [Prestaties van kopieeractiviteit](copy-activity-performance.md)