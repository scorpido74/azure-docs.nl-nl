---
title: Activiteit gegevensstroom
description: Gegevensstromen uitvoeren vanuit een pijplijn in een gegevensfabriek.
services: data-factory
documentationcenter: ''
author: kromerm
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: makromer
ms.date: 03/16/2020
ms.openlocfilehash: 115cb3e499117457629e130b6432a1cbc2224edb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79463047"
---
# <a name="data-flow-activity-in-azure-data-factory"></a>Gegevensstroomactiviteit in Azure Data Factory

Gebruik de activiteit Gegevensstroom om gegevens te transformeren en te verplaatsen via gegevensstromen in kaart brengen. Zie [Overzicht van gegevensstroom toewijzen](concepts-data-flow-overview.md) als u nieuw bent in gegevensstromen

## <a name="syntax"></a>Syntaxis

```json
{
    "name": "MyDataFlowActivity",
    "type": "ExecuteDataFlow",
    "typeProperties": {
      "dataflow": {
         "referenceName": "MyDataFlow",
         "type": "DataFlowReference"
      },
      "compute": {
         "coreCount": 8,
         "computeType": "General"
      },
      "staging": {
          "linkedService": {
              "referenceName": "MyStagingLinkedService",
              "type": "LinkedServiceReference"
          },
          "folderPath": "my-container/my-folder"
      },
      "integrationRuntime": {
          "referenceName": "MyDataFlowIntegrationRuntime",
          "type": "IntegrationRuntimeReference"
      }
}

```

## <a name="type-properties"></a>Eigenschappen typen

Eigenschap | Beschrijving | Toegestane waarden | Vereist
-------- | ----------- | -------------- | --------
gegevensstroom | De verwijzing naar de gegevensstroom die wordt uitgevoerd | GegevensstroomReferentie | Ja
integratieRuntime | De compute-omgeving waarop de gegevensstroom wordt uitgevoerd. Als dit niet is opgegeven, wordt de runtime voor Azure-integratie automatisch oplossen gebruikt | IntegratieRuntimeReference | Nee
compute.coreCount | Het aantal kernen dat in het sparkcluster wordt gebruikt. Kan alleen worden opgegeven als de runtime voor het automatisch oplossen van Azure-integratie wordt gebruikt | 8, 16, 32, 48, 80, 144, 272 | Nee
compute.computeType | Het type rekenkracht dat wordt gebruikt in het sparkcluster. Kan alleen worden opgegeven als de runtime voor het automatisch oplossen van Azure-integratie wordt gebruikt | "Algemeen", "ComputeOptimized", "MemoryOptimized" | Nee
staging.linkedService | Als u een SQL DW-bron of -sink gebruikt, wordt het opslagaccount gebruikt voor PolyBase-fasering | LinkedServiceReference | Alleen als de gegevensstroom wordt gelezen of geschreven naar een SQL DW
staging.folderPath | Als u een SQL DW-bron of -sink gebruikt, wordt het mappad in blob-opslagaccount gebruikt voor PolyBase-fasering | Tekenreeks | Alleen als de gegevensstroom wordt gelezen of geschreven naar een SQL DW

![Gegevensstroom uitvoeren](media/data-flow/activity-data-flow.png "Gegevensstroom uitvoeren")

### <a name="dynamically-size-data-flow-compute-at-runtime"></a>Dynamisch formaat gegevensstroom compute bij runtime

De eigenschappen Core Count en Compute Type kunnen dynamisch worden ingesteld om aan te passen aan de grootte van uw binnenkomende brongegevens tijdens de runtime. Gebruik pijplijnactiviteiten zoals Opzoeking of Metagegevens ophalen om de grootte van de gegevensgegevens van de brongegevensset te vinden. Gebruik vervolgens Dynamische inhoud toevoegen in de activiteitseigenschappen Gegevensstroom.

![Dynamische gegevensstroom](media/data-flow/dyna1.png "Dynamische gegevensstroom")

[Hier is een korte video tutorial uitleg over deze techniek](https://www.youtube.com/watch?v=jWSkJdtiJNM)

### <a name="data-flow-integration-runtime"></a>Runtime voor integratie van gegevensstroom

Kies welke integratieruntime u wilt gebruiken voor de uitvoering van uw datastroomactiviteit. Standaard gebruikt Data Factory de runtime voor het automatisch oplossen van Azure Integration met vier werkkernen en geen time-to-live (TTL). Deze IR heeft een compute-type voor algemene doeleinden en wordt uitgevoerd in dezelfde regio als uw fabriek. U uw eigen Azure Integration Runtimes maken die specifieke regio's, rekentype, kerntellingen en TTL definiëren voor de uitvoering van uw gegevensstroomactiviteit.

Voor pijplijnuitvoeringen is het cluster een taakcluster, dat enkele minuten duurt voordat de uitvoering begint. Als er geen TTL is opgegeven, is deze opstarttijd vereist voor elke pijplijnuitvoering. Als u een TTL opgeeft, blijft een warme clustergroep actief gedurende de periode die is opgegeven na de laatste uitvoering, wat resulteert in kortere opstarttijden. Als u bijvoorbeeld een TTL van 60 minuten hebt en er eenmaal per uur een gegevensstroom op uitvoert, blijft de clustergroep actief. Zie [runtime azure-integratie voor](concepts-integration-runtime.md)meer informatie .

![Runtime azure-integratie](media/data-flow/ir-new.png "Runtime azure-integratie")

> [!NOTE]
> De selectie voor integratieruntijd in de activiteit Gegevensstroom is alleen van toepassing op *geactiveerde uitvoeringen* van uw pijplijn. Het opsporen van uw pijplijn met gegevensstromen wordt uitgevoerd op het cluster dat is opgegeven in de foutopsporingssessie.

### <a name="polybase"></a>PolyBase

Als u een Azure SQL Data Warehouse gebruikt als een sink of bron, moet u een faseringslocatie kiezen voor uw PolyBase-batchbelasting. PolyBase maakt batchladen in bulk mogelijk in plaats van de gegevens rij voor rij te laden. PolyBase vermindert de laadtijd in de SQL DW drastisch.

## <a name="parameterizing-data-flows"></a>Gegevensstromen parameteriseren

### <a name="parameterized-datasets"></a>Geparameteriseerde gegevenssets

Als uw gegevensstroom parametersets gebruikt, stelt u de parameterwaarden in op het tabblad **Instellingen.**

![Gegevensstroomparameters uitvoeren](media/data-flow/params.png "Parameters")

### <a name="parameterized-data-flows"></a>Geparameteriseerde gegevensstromen

Als uw gegevensstroom is geparameteriseerd, stelt u de dynamische waarden van de gegevensstroomparameters in op het tabblad **Parameters.** U de taal van de ADF-pijplijnexpressie (alleen voor tekenreekstypen) of de taal van de gegevensstroomexpressie gebruiken om dynamische of letterlijke parameterwaarden toe te wijzen. Zie [Gegevensstroomparameters](parameters-data-flow.md)voor meer informatie .

![Voorbeeld van gegevensstroomparameter uitvoeren](media/data-flow/parameter-example.png "Parametervoorbeeld")

### <a name="parameterized-compute-properties"></a>Geparameteriseerde rekeneigenschappen.

U het aantal kernen of het rekentype parameteriseren als u de runtime voor azure-integratie automatisch oplossen gebruikt en waarden opgeven voor compute.coreCount en compute.computeType.

![Voorbeeld van gegevensstroomparameter uitvoeren](media/data-flow/parameterize-compute.png "Parametervoorbeeld")

## <a name="pipeline-debug-of-data-flow-activity"></a>Pijplijnfoutfout van activiteit gegevensstroom

Als u een foutopsporingspijplijn wilt uitvoeren met een gegevensstroomactiviteit, moet u de foutopsporingsmodus gegevensstroom inschakelen via de schuifregelaar **Foutopsporingsgegevens gegevensstroom** op de bovenste balk. Met de foutopsporingsmodus u de gegevensstroom uitvoeren op een actief Spark-cluster. Zie [Foutopsporingsmodus](concepts-data-flow-debug-mode.md)voor meer informatie .

![Foutopsporingsknop](media/data-flow/debugbutton.png "Foutopsporingsknop")

De foutopsporingspijplijn wordt uitgevoerd tegen het actieve foutopsporingscluster, niet tegen de runtime-omgeving voor integratie die is opgegeven in de activiteitsinstellingen gegevensstroom. U de foutopsporingsmodus kiezen bij het opstarten van de foutopsporingsmodus.

## <a name="monitoring-the-data-flow-activity"></a>De activiteit Gegevensstroom bewaken

De activiteit Gegevensstroom heeft een speciale monitoringervaring waarbij u partitie-, fasetijd- en gegevensregelgegevens bekijken. Open het controlevenster via het pictogram van de bril onder **Acties**. Zie [Gegevensstromen controleren](concepts-data-flow-monitoring.md)voor meer informatie .

### <a name="use-data-flow-activity-results-in-a-subsequent-activity"></a>Activiteit gegevensstroom gebruiken resulteert in een volgende activiteit

De gegevensstroomactiviteit maakt metriek met betrekking tot het aantal rijen dat naar elke gootsteen is geschreven en rijen die uit elke bron worden gelezen. Deze resultaten worden `output` geretourneerd in het gedeelte van het resultaat van de activiteitsrun. De geretourneerde statistieken zijn in het formaat van de onderstaande json.

``` json
{
    "runStatus": {
        "metrics": {
            "<your sink name1>": {
                "rowsWritten": <number of rows written>,
                "sinkProcessingTime": <sink processing time in ms>,
                "sources": {
                    "<your source name1>": {
                        "rowsRead": <number of rows read>
                    },
                    "<your source name2>": {
                        "rowsRead": <number of rows read>
                    },
                    ...
                }
            },
            "<your sink name2>": {
                ...
            },
            ...
        }
    }
}
```

Als u bijvoorbeeld bij het aantal rijen wilt komen dat naar een gootsteen met de `@activity('dataflowActivity').output.runStatus.metrics.sink1.rowsWritten`naam 'sink1' is geschreven in een activiteit met de naam 'dataflowActivity', gebruikt u .

Gebruik . `@activity('dataflowActivity').output.runStatus.metrics.sink1.sources.source1.rowsRead`

> [!NOTE]
> Als een gootsteen nul rijen heeft geschreven, wordt deze niet weergegeven in metrische gegevens. Het bestaan kan worden `contains` geverifieerd met behulp van de functie. Controleer bijvoorbeeld `contains(activity('dataflowActivity').output.runStatus.metrics, 'sink1')` of er rijen zijn geschreven om te zinken1.

## <a name="next-steps"></a>Volgende stappen

Bekijk de activiteiten van de besturingsstroom die worden ondersteund door Data Factory: 

- [If Condition Activity](control-flow-if-condition-activity.md)
- [Execute Pipeline Activity](control-flow-execute-pipeline-activity.md)
- [Voor elke activiteit](control-flow-for-each-activity.md)
- [Get Metadata Activity](control-flow-get-metadata-activity.md)
- [Lookup Activity](control-flow-lookup-activity.md)
- [Webactiviteit](control-flow-web-activity.md)
- [Until Activity](control-flow-until-activity.md)
