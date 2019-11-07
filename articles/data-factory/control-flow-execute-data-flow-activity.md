---
title: Gegevens stroom activiteit in Azure Data Factory
description: Gegevens stromen uitvoeren vanuit een data factory pijp lijn.
services: data-factory
documentationcenter: ''
author: kromerm
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.author: makromer
ms.date: 10/07/2019
ms.openlocfilehash: 5623907346ee3882ad53a27695336ba4bc449db8
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73679950"
---
# <a name="data-flow-activity-in-azure-data-factory"></a>Gegevens stroom activiteit in Azure Data Factory

Gebruik de activiteit gegevens stroom om gegevens te transformeren en te verplaatsen via toewijzing van gegevens stromen. Zie [overzicht van gegevens stroom toewijzen](concepts-data-flow-overview.md) als u geen ervaring hebt met gegevens stromen

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

## <a name="type-properties"></a>Type-eigenschappen

Eigenschap | Beschrijving | Toegestane waarden | Vereist
-------- | ----------- | -------------- | --------
stroom | De verwijzing naar de gegevens stroom die wordt uitgevoerd | DataFlowReference | Ja
integrationRuntime | De compute-omgeving waarop de gegevens stroom wordt uitgevoerd | IntegrationRuntimeReference | Ja
staging. linkedService | Als u een SQL DW-bron of-sink gebruikt, wordt het opslag account dat wordt gebruikt voor poly base staging | Linkedservicereference is | Alleen als de gegevens stroom leest of schrijft naar een SQL DW
staging. folderPath | Als u een SQL DW-bron of-sink gebruikt, wordt het mappad in het Blob Storage-account dat wordt gebruikt voor poly base staging | Tekenreeks | Alleen als de gegevens stroom leest of schrijft naar een SQL DW

![Gegevens stroom uitvoeren](media/data-flow/activity-data-flow.png "Gegevens stroom uitvoeren")

### <a name="data-flow-integration-runtime"></a>Data flow Integration runtime

Kies welke Integration Runtime moet worden gebruikt voor de uitvoering van de activiteit van de gegevens stroom. Data Factory maakt standaard gebruik van de automatisch oplossen van Azure Integration runtime met vier worker-kernen en geen TTL (time to Live). Deze IR heeft een reken type voor algemeen gebruik en wordt uitgevoerd in dezelfde regio als uw fabriek. U kunt uw eigen Azure Integration Runtimes maken voor het definiëren van specifieke regio's, reken type, kern aantallen en TTL voor de uitvoering van de gegevens stroom activiteit.

Voor de uitvoering van pijp lijnen is het cluster een taak cluster, dat enkele minuten in beslag neemt voordat de uitvoering wordt gestart. Als er geen TTL is opgegeven, is deze opstart tijd vereist op elke pijplijn uitvoering. Als u een TTL opgeeft, blijft een warme cluster groep actief gedurende de tijd die na de laatste uitvoering is opgegeven, wat resulteert in kortere opstart tijden. Als u bijvoorbeeld een TTL van 60 minuten hebt en een gegevens stroom eenmaal per uur uitvoert, blijft de cluster groep actief. Zie [Azure Integration runtime](concepts-integration-runtime.md)voor meer informatie.

![Azure Integration Runtime](media/data-flow/ir-new.png "Azure-integratieruntime")

> [!NOTE]
> De Integration Runtime selectie in de activiteit gegevens stroom is alleen van toepassing op *geactiveerde uitvoeringen* van de pijp lijn. Fout opsporing voor de pijp lijn met gegevens stromen worden uitgevoerd op het cluster dat is opgegeven in de foutopsporingssessie.

### <a name="polybase"></a>PolyBase

Als u een Azure SQL Data Warehouse als sink of bron gebruikt, moet u een faserings locatie voor het laden van poly base-batches kiezen. Met poly Base kan batch in bulk worden geladen in plaats van de gegevensrij per rij te laden. Poly base verlaagt drastisch de laad tijd in de SQL DW.

## <a name="parameterizing-data-flows"></a>Parameterizing-gegevens stromen

### <a name="parameterized-datasets"></a>Gegevens sets met para meters

Als uw gegevens stroom gebruikmaakt van parameter gegevens sets, stelt u de parameter waarden in op het tabblad **instellingen** .

![Data flow-para meters uitvoeren](media/data-flow/params.png "Parameters")

### <a name="parameterized-data-flows"></a>Gegevens stromen met para meters

Als uw gegevens stroom is para meters, stelt u de dynamische waarden van de para meters voor de gegevens stroom in op het tabblad **para meters** . U kunt de taal van de ADF-pijplijn expressie (alleen voor teken reeks typen) of de taal van de gegevens stroom expressie gebruiken om dynamische of letterlijke parameter waarden toe te wijzen. Zie [Data flow-para meters](parameters-data-flow.md)voor meer informatie.

![Voor beeld van para meter voor gegevens stroom uitvoeren](media/data-flow/parameter-example.png "Parameter voorbeeld")

## <a name="pipeline-debug-of-data-flow-activity"></a>Pijp lijn fout opsporing van gegevens stroom activiteit

Als u een pijp lijn voor fout opsporing wilt uitvoeren met een activiteit voor gegevens stromen, moet u overschakelen op de modus voor het opsporen van gegevens stromen via de schuif regelaar voor **fout opsporing van gegevens stromen** op de bovenste balk. Met de foutopsporingsmodus kunt u de gegevens stroom uitvoeren op een actief Spark-cluster. Zie [debug mode (foutopsporingsmodus](concepts-data-flow-debug-mode.md)) voor meer informatie.

![Knop fout opsporing](media/data-flow/debugbutton.png "Knop fout opsporing")

De pijp lijn voor fout opsporing wordt uitgevoerd op het actieve debug-cluster, niet de Integration runtime-omgeving die is opgegeven in de instellingen voor de activiteit van de gegevens stroom. U kunt de compute-omgeving voor fout opsporing kiezen wanneer u de foutopsporingsmodus opstart.

## <a name="monitoring-the-data-flow-activity"></a>De activiteit gegevens stroom bewaken

De activiteit gegevens stroom heeft een speciale bewakings ervaring waarbij u gegevens over partitionering, fase tijd en gegevens afkomst kunt weer geven. Open het deel venster bewaking via het pictogram bril onder **acties**. Zie [gegevens stromen bewaken](concepts-data-flow-monitoring.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Zie controle stroom activiteiten die worden ondersteund door Data Factory: 

- [If Condition Activity](control-flow-if-condition-activity.md)
- [Execute Pipeline Activity](control-flow-execute-pipeline-activity.md)
- [Voor elke activiteit](control-flow-for-each-activity.md)
- [Get Metadata Activity](control-flow-get-metadata-activity.md)
- [Lookup Activity](control-flow-lookup-activity.md)
- [Webactiviteit](control-flow-web-activity.md)
- [Until Activity](control-flow-until-activity.md)
