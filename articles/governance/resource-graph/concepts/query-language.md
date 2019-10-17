---
title: Meer informatie over de query taal
description: Hierin worden resource grafiek tabellen en de beschik bare Kusto-gegevens typen,-Opera tors en-functies die bruikbaar zijn met Azure resource Graph beschreven.
author: DCtheGeek
ms.author: dacoulte
ms.date: 10/18/2019
ms.topic: conceptual
ms.service: resource-graph
ms.openlocfilehash: 6189920cb03a6cf388f0b5d232c6ce97ae4f3f82
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/16/2019
ms.locfileid: "72389767"
---
# <a name="understanding-the-azure-resource-graph-query-language"></a>Informatie over de query taal van Azure resource Graph

De query taal voor de Azure-resource grafiek ondersteunt een aantal opera tors en functies. Elk werk en werkt op basis van [Kusto query language (KQL)](/azure/kusto/query/index). Als u meer wilt weten over de query taal die wordt gebruikt door resource grafiek, begint u met de [zelf studie voor KQL](/azure/kusto/query/tutorial).

In dit artikel worden de taal onderdelen beschreven die worden ondersteund door resource grafiek:

- [Resource grafiek tabellen](#resource-graph-tables)
- [Ondersteunde KQL-taal elementen](#supported-kql-language-elements)
- [Escape tekens](#escape-characters)

## <a name="resource-graph-tables"></a>Resource grafiek tabellen

Resource grafiek biedt verschillende tabellen voor de gegevens die worden opgeslagen over Resource Manager-resource typen en hun eigenschappen. Deze tabellen kunnen worden gebruikt met `join`-of `union`-Opera tors om eigenschappen van gerelateerde resource typen op te halen. Hier volgt de lijst met tabellen die beschikbaar zijn in resource grafiek:

|Resource grafiek tabellen |Beschrijving |
|---|---|
|Bronnen |De standaard tabel als niets is gedefinieerd in de query. De resource typen en eigenschappen van Resource Manager zijn hier beschikbaar. |
|ResourceContainers |Bevat de resource typen abonnement (@no__t 0) en resource groep (`Microsoft.Resources/subscriptions/resourcegroups`) en gegevens. |
|AlertsManagementResources |Bevat resources met _betrekking_ tot `Microsoft.AlertsManagement`. |
|SecurityResources |Bevat resources met _betrekking_ tot `Microsoft.Security`. |

> [!NOTE]
> _Resources_ is de standaard tabel. Tijdens het uitvoeren van een query op de tabel _resources_ is het niet nodig om de naam van de tabel op te geven, tenzij `join` of `union` worden gebruikt. De aanbevolen procedure is echter om altijd de eerste tabel in de query op te halen.

Gebruik resource Graph Explorer in de portal om te ontdekken welke resource typen beschikbaar zijn in elke tabel. Als alternatief kunt u een query als `<tableName> | distinct type` gebruiken om een lijst met resource typen op te halen. de gegeven resource grafiek tabel ondersteunt in uw omgeving.

Met de volgende query wordt een eenvoudig `join` weer gegeven. In het query resultaat worden de kolommen samengebracht en dubbele kolom namen uit de gekoppelde tabel, _ResourceContainers_ in dit voor beeld, worden toegevoegd aan **1**. Als _ResourceContainers_ -tabel heeft typen voor beide abonnementen en resource groepen, kan een van beide typen worden gebruikt om lid te worden van de tabel Resource van _resources_ .

```kusto
Resources
| join ResourceContainers on subscriptionId
| limit 1
```

Met de volgende query wordt een complexere gebruik van `join` weer gegeven. Met de query wordt de gekoppelde tabel beperkt tot resources voor inschrijving en met `project` alleen het oorspronkelijke veld _subscriptionId_ en de _naam veld naam wijzigen_ in _subnaam_. De naam van het veld Vermijd `join` het toevoegen als _NAME1_ omdat het veld al in _resources_bestaat. De oorspronkelijke tabel wordt gefilterd met `where` en de volgende `project` bevat kolommen uit beide tabellen. Het query resultaat is een enkele sleutel kluis met het type, de naam van de sleutel kluis en de naam van het abonnement dat in wordt weer gegeven.

```kusto
Resources
| join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId
| where type == 'microsoft.keyvault/vaults'
| project type, name, SubName
| limit 1
```

> [!NOTE]
> Bij het beperken van de `join` resultaten met `project`, moet de eigenschap die door `join` wordt gebruikt om de twee tabellen te koppelen, zoals _subscriptionId_ in het bovenstaande voor beeld, worden opgenomen in `project`.

## <a name="supported-kql-language-elements"></a>Ondersteunde KQL-taal elementen

Resource grafiek ondersteunt alle KQL- [gegevens typen](/azure/kusto/query/scalar-data-types/), [scalaire functies](/azure/kusto/query/scalarfunctions), [scalaire Opera tors](/azure/kusto/query/binoperators)en [aggregatie functies](/azure/kusto/query/any-aggfunction). Specifieke [tabellaire Opera tors](/azure/kusto/query/queries) worden ondersteund door resource grafiek, waarvan sommige verschillende gedragingen hebben.

### <a name="supported-tabulartop-level-operators"></a>Ondersteunde Opera tors voor tabellaire/hoogste niveau

Hier volgt een lijst met KQL-Opera tors die worden ondersteund door resource grafiek met specifieke voor beelden:

|KQL |Voorbeeld query resource grafiek |Opmerkingen |
|---|---|---|
|[count](/azure/kusto/query/countoperator) |[Sleutel kluizen tellen](../samples/starter.md#count-keyvaults) | |
|[onderscheiden](/azure/kusto/query/distinctoperator) |[DISTINCT-waarden voor een specifieke alias weer geven](../samples/starter.md#distinct-alias-values) | |
|[uitbreidbaar](/azure/kusto/query/extendoperator) |[Virtuele machines tellen op type besturingssysteem](../samples/starter.md#count-os) | |
|[join](/azure/kusto/query/joinoperator) |[Sleutel kluis met de naam van het abonnement](../samples/advanced.md#join) |Ondersteunde jointypen: [innerunique](/azure/kusto/query/joinoperator#default-join-flavor), [inner](/azure/kusto/query/joinoperator#inner-join), [leftouter](/azure/kusto/query/joinoperator#left-outer-join). De limiet van 3 `join` in één query. Aangepaste deelname strategieën, zoals broadcast toevoegen, zijn niet toegestaan. Kan worden gebruikt binnen één tabel of tussen de tabellen _resources_ en _ResourceContainers_ . |
|[ondergrens](/azure/kusto/query/limitoperator) |[Een lijst van alle openbare IP-adressen weergeven](../samples/starter.md#list-publicip) |Synoniem van `take` |
|[MV-uitvouwen](/azure/kusto/query/mvexpandoperator) |[Cosmos DB met specifieke schrijf locaties weer geven](../samples/advanced.md#mvexpand-cosmosdb) |_RowLimit_ maximum van 400 |
|[ter](/azure/kusto/query/orderoperator) |[Een lijst van resources weergeven, gesorteerd op naam](../samples/starter.md#list-resources) |Synoniem van `sort` |
|[project](/azure/kusto/query/projectoperator) |[Een lijst van resources weergeven, gesorteerd op naam](../samples/starter.md#list-resources) | |
|[project-weg](/azure/kusto/query/projectawayoperator) |[Kolommen verwijderen uit resultaten](../samples/advanced.md#remove-column) | |
|[acties](/azure/kusto/query/sortoperator) |[Een lijst van resources weergeven, gesorteerd op naam](../samples/starter.md#list-resources) |Synoniem van `order` |
|[samenvatten](/azure/kusto/query/summarizeoperator) |[Azure-resources tellen](../samples/starter.md#count-resources) |Alleen de eerste vereenvoudigde pagina |
|[Houd](/azure/kusto/query/takeoperator) |[Een lijst van alle openbare IP-adressen weergeven](../samples/starter.md#list-publicip) |Synoniem van `limit` |
|[Boven](/azure/kusto/query/topoperator) |[De eerste vijf virtuele machines weergeven op naam en met hun type besturingssysteem](../samples/starter.md#show-sorted) | |
|[Réunion](/azure/kusto/query/unionoperator) |[Resultaten van twee query's combi neren tot één resultaat](../samples/advanced.md#unionresults) |Eén tabel toegestaan: _T_ `| union` \[ @ no__t-3 `inner` @ no__t-5 @ no__t-6 @ no__t-7 \[ @ no__t-9_kolom_naam 1 _tabel_. De limiet van 3 `union` poten in één query. Het oplossen van fuzzy-tabellen `union`-poot is niet toegestaan. Kan worden gebruikt binnen één tabel of tussen de tabellen _resources_ en _ResourceContainers_ . |
|[positie](/azure/kusto/query/whereoperator) |[Resources weergeven die opslag bevatten](../samples/starter.md#show-storage) | |

## <a name="escape-characters"></a>Escape tekens

Sommige eigenschapnamen, zoals de namen die een `.` of `$` bevatten, moeten in de query worden verpakt of worden geescaped of de naam van de eigenschap wordt onjuist geïnterpreteerd en levert niet de verwachte resultaten op.

- `.`: verpakken de naam van de eigenschap als volgt: `['propertyname.withaperiod']`
  
  Voorbeeld query waarmee de eigenschap _odata. type_wordt geterugloopd:

  ```kusto
  where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.['odata.type']
  ```

- `$`: het teken in de naam van de eigenschap Escape. Welk escape teken wordt gebruikt, is afhankelijk van de resource grafiek van de shell wordt uitgevoerd vanaf.

  - **bash** -  @ no__t-2

    Voorbeeld query waarmee de eigenschap _\$Type_ in bash wordt geescapet:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.\$type
    ```

  - **cmd** : laat het `$`-teken onescape.

  - **Power shell**- -  @ no__t-2

    Voorbeeld query waarmee de eigenschap _\$Type_ in Power shell wordt overgeslagen:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.`$type
    ```

## <a name="next-steps"></a>Volgende stappen

- De taal die wordt gebruikt voor [Start query's](../samples/starter.md) weer geven
- Zie Geavanceerd gebruik in [Geavanceerde query's](../samples/advanced.md)
- [Resources verkennen](explore-resources.md)