---
title: Meer informatie over de query taal
description: Beschrijft de beschik bare Kusto-Opera tors en-functies die bruikbaar zijn met Azure resource Graph.
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/22/2019
ms.topic: conceptual
ms.service: resource-graph
manager: carmonm
ms.openlocfilehash: c6e35d688581d0839e12806117e63c7d71fbc459
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/03/2019
ms.locfileid: "70231507"
---
# <a name="understanding-the-azure-resource-graph-query-language"></a>Informatie over de query taal van Azure resource Graph

De query taal voor de Azure-resource grafiek ondersteunt een aantal opera tors en functies. Elk werk en werken op basis van [Azure Data Explorer](../../../data-explorer/data-explorer-overview.md).

De beste manier om meer te weten te komen over de query taal die wordt gebruikt door resource grafiek, is om te beginnen met de documentatie voor de Azure Data Explorer- [query taal](/azure/kusto/query/index). Het bevat een uitleg over de manier waarop de taal is gestructureerd en hoe de verschillende ondersteunde Opera tors en functies samen werken.

## <a name="supported-tabular-operators"></a>Ondersteunde tabellaire Opera tors

Hier volgt een lijst met ondersteunde tabellaire Opera tors in de resource grafiek:

- [count](/azure/kusto/query/countoperator)
- [onderscheiden](/azure/kusto/query/distinctoperator)
- [uitbreidbaar](/azure/kusto/query/extendoperator)
- [ondergrens](/azure/kusto/query/limitoperator)
- [sorteren op](/azure/kusto/query/orderoperator)
- [project](/azure/kusto/query/projectoperator)
- [project-away](/azure/kusto/query/projectawayoperator)
- [voorbeeld](/azure/kusto/query/sampleoperator)
- [voor beeld-DISTINCT](/azure/kusto/query/sampledistinctoperator)
- [sorteren op](/azure/kusto/query/sortoperator)
- [samenvatten](/azure/kusto/query/summarizeoperator)
- [Houd](/azure/kusto/query/takeoperator)
- [Boven](/azure/kusto/query/topoperator)
- [top-nested](/azure/kusto/query/topnestedoperator)
- [Top-hitters](/azure/kusto/query/tophittersoperator)
- [positie](/azure/kusto/query/whereoperator)

## <a name="supported-functions"></a>Ondersteunde functies

Hier volgt een lijst met ondersteunde functies in resource grafiek:

- [geleden ()](/azure/kusto/query/agofunction)
- [buildschema()](/azure/kusto/query/buildschema-aggfunction)
- [strcat()](/azure/kusto/query/strcatfunction)
- [isnotempty()](/azure/kusto/query/isnotemptyfunction)
- [tostring()](/azure/kusto/query/tostringfunction)
- [zip()](/azure/kusto/query/zipfunction)

## <a name="escape-characters"></a>Escape tekens

Sommige eigenschapnamen van eigenschappen, zoals die van een `.` or `$`, moeten in de query worden verpakt of worden geescaped of de naam van de eigenschap wordt onjuist geïnterpreteerd en levert niet de verwachte resultaten op.

- `.`-Laat de naam van de eigenschap als volgt teruglopen:`['propertyname.withaperiod']`
  
  Voorbeeld query waarmee de eigenschap _odata. type_wordt geterugloopd:

  ```kusto
  where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.['odata.type']
  ```

- `$`-Escape het teken in de naam van de eigenschap. Welk escape teken wordt gebruikt, is afhankelijk van de resource grafiek van de shell wordt uitgevoerd vanaf.

  - **bash** - `\`

    Voorbeeld query waarmee het eigenschaps  _\$type_ wordt verescapet in bash:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.\$type
    ```

  - **cmd** : laat het `$` teken onescape.

  - **PowerShell** - ``` ` ```

    Voorbeeld query waarmee het eigenschaps  _\$type_ in Power shell wordt geescapet:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.`$type
    ```

## <a name="next-steps"></a>Volgende stappen

- De taal die wordt gebruikt voor [Start query's](../samples/starter.md) weer geven
- Zie Geavanceerd gebruik in [Geavanceerde query's](../samples/advanced.md)
- [Resources verkennen](explore-resources.md)