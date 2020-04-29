---
title: Inzicht krijgen in de querytaal
description: Hierin worden resource grafiek tabellen en de beschik bare Kusto-gegevens typen,-Opera tors en-functies die bruikbaar zijn met Azure resource Graph beschreven.
ms.date: 03/07/2020
ms.topic: conceptual
ms.openlocfilehash: 2f4be4d86a340867e1ad3015ff288f98fc54cecf
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "78927484"
---
# <a name="understanding-the-azure-resource-graph-query-language"></a>Informatie over de query taal van Azure resource Graph

De query taal voor de Azure-resource grafiek ondersteunt een aantal opera tors en functies. Elk werk en werkt op basis van [Kusto query language (KQL)](/azure/kusto/query/index). Als u meer wilt weten over de query taal die wordt gebruikt door resource grafiek, begint u met de [zelf studie voor KQL](/azure/kusto/query/tutorial).

In dit artikel worden de taal onderdelen beschreven die worden ondersteund door resource grafiek:

- [Resource grafiek tabellen](#resource-graph-tables)
- [Ondersteunde KQL-taal elementen](#supported-kql-language-elements)
- [Escape tekens](#escape-characters)

## <a name="resource-graph-tables"></a>Resource grafiek tabellen

Resource grafiek biedt verschillende tabellen voor de gegevens die worden opgeslagen over Resource Manager-resource typen en hun eigenschappen. Deze tabellen kunnen met `join` or `union` -Opera tors worden gebruikt voor het ophalen van eigenschappen van gerelateerde resource typen. Hier volgt de lijst met tabellen die beschikbaar zijn in resource grafiek:

|Resource grafiek tabellen |Beschrijving |
|---|---|
|Resources |De standaard tabel als niets is gedefinieerd in de query. De resource typen en eigenschappen van Resource Manager zijn hier beschikbaar. |
|ResourceContainers |Bevat een abonnement (in Preview- `Microsoft.Resources/subscriptions`-) en resource groep`Microsoft.Resources/subscriptions/resourcegroups`()-resource typen en-gegevens. |
|AdvisorResources |Bevat resources _related_ met betrekking `Microsoft.Advisor`tot. |
|AlertsManagementResources |Bevat resources _related_ met betrekking `Microsoft.AlertsManagement`tot. |
|MaintenanceResources |Bevat resources _related_ met betrekking `Microsoft.Maintenance`tot. |
|SecurityResources |Bevat resources _related_ met betrekking `Microsoft.Security`tot. |

Zie [verwijzing: ondersteunde tabellen en resource typen](../reference/supported-tables-resources.md)voor een volledige lijst met resource typen.

> [!NOTE]
> _Resources_ is de standaard tabel. Tijdens het uitvoeren van een query op de tabel _resources_ is het niet nodig om de `join` tabel `union` naam op te geven, tenzij of wordt gebruikt. De aanbevolen procedure is echter om altijd de eerste tabel in de query op te halen.

Gebruik resource Graph Explorer in de portal om te ontdekken welke resource typen beschikbaar zijn in elke tabel. Als alternatief kunt u een query gebruiken `<tableName> | distinct type` om een lijst met resource typen op te halen. de gegeven resource grafiek tabel ondersteunt in uw omgeving.

De volgende query bevat een eenvoudige `join`. In het query resultaat worden de kolommen samengebracht en dubbele kolom namen uit de gekoppelde tabel, _ResourceContainers_ in dit voor beeld, worden toegevoegd aan **1**. Als _ResourceContainers_ -tabel heeft typen voor beide abonnementen en resource groepen, kan een van beide typen worden gebruikt om lid te worden van de tabel Resource van _resources_ .

```kusto
Resources
| join ResourceContainers on subscriptionId
| limit 1
```

Met de volgende query wordt een complexere gebruik `join`van weer gegeven. Met de query wordt de gekoppelde tabel beperkt tot resources voor `project` abonnementen en met om alleen het oorspronkelijke veld _subscriptionId_ op te nemen _en de naam_ van het veld met de naam van de _subnaam_. De naam van het veld `join` wordt voor komen dat het wordt toegevoegd als _NAME1_ omdat het veld al in _resources_bestaat. De oorspronkelijke tabel wordt gefilterd `where` en het `project` volgende bevat kolommen uit beide tabellen. Het query resultaat is een enkele sleutel kluis met het type, de naam van de sleutel kluis en de naam van het abonnement dat in wordt weer gegeven.

```kusto
Resources
| where type == 'microsoft.keyvault/vaults'
| join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId
| project type, name, SubName
| limit 1
```

> [!NOTE]
> Als `join` de resultaten worden beperkt `project`met, moet de eigenschap `join` die wordt gebruikt om de twee tabellen te koppelen, _subscriptionId_ in het bovenstaande voor beeld zijn `project`opgenomen in.

## <a name="supported-kql-language-elements"></a>Ondersteunde KQL-taal elementen

Resource grafiek ondersteunt alle KQL- [gegevens typen](/azure/kusto/query/scalar-data-types/), [scalaire functies](/azure/kusto/query/scalarfunctions), [scalaire Opera tors](/azure/kusto/query/binoperators)en [aggregatie functies](/azure/kusto/query/any-aggfunction). Specifieke [tabellaire Opera tors](/azure/kusto/query/queries) worden ondersteund door resource grafiek, waarvan sommige verschillende gedragingen hebben.

### <a name="supported-tabulartop-level-operators"></a>Ondersteunde Opera tors voor tabellaire/hoogste niveau

Hier volgt een lijst met KQL-Opera tors die worden ondersteund door resource grafiek met specifieke voor beelden:

|KQL |Voorbeeld query resource grafiek |Opmerkingen |
|---|---|---|
|[aantal](/azure/kusto/query/countoperator) |[Sleutel kluizen tellen](../samples/starter.md#count-keyvaults) | |
|[distinct](/azure/kusto/query/distinctoperator) |[DISTINCT-waarden voor een specifieke alias weer geven](../samples/starter.md#distinct-alias-values) | |
|[uitbreidbaar](/azure/kusto/query/extendoperator) |[Virtuele machines tellen op type besturingssysteem](../samples/starter.md#count-os) | |
|[Jointypen](/azure/kusto/query/joinoperator) |[Sleutel kluis met de naam van het abonnement](../samples/advanced.md#join) |Ondersteunde jointypen: [innerunique](/azure/kusto/query/joinoperator#default-join-flavor), [inner](/azure/kusto/query/joinoperator#inner-join), [leftouter](/azure/kusto/query/joinoperator#left-outer-join). De limiet van `join` 3 in één query. Aangepaste deelname strategieën, zoals broadcast toevoegen, zijn niet toegestaan. Kan worden gebruikt binnen één tabel of tussen de tabellen _resources_ en _ResourceContainers_ . |
|[ondergrens](/azure/kusto/query/limitoperator) |[Een lijst van alle openbare IP-adressen weergeven](../samples/starter.md#list-publicip) |Synoniem van`take` |
|[mvexpand](/azure/kusto/query/mvexpandoperator) | | Verouderde operator `mv-expand` . gebruik in plaats daarvan. _RowLimit_ maximum van 400. De standaard waarde is 128. |
|[MV-uitvouwen](/azure/kusto/query/mvexpandoperator) |[Cosmos DB met specifieke schrijf locaties weer geven](../samples/advanced.md#mvexpand-cosmosdb) |_RowLimit_ maximum van 400. De standaard waarde is 128. |
|[ter](/azure/kusto/query/orderoperator) |[Lijst met resources gesorteerd op naam](../samples/starter.md#list-resources) |Synoniem van`sort` |
|[project](/azure/kusto/query/projectoperator) |[Lijst met resources gesorteerd op naam](../samples/starter.md#list-resources) | |
|[project-weg](/azure/kusto/query/projectawayoperator) |[Kolommen verwijderen uit resultaten](../samples/advanced.md#remove-column) | |
|[acties](/azure/kusto/query/sortoperator) |[Lijst met resources gesorteerd op naam](../samples/starter.md#list-resources) |Synoniem van`order` |
|[samenvatten](/azure/kusto/query/summarizeoperator) |[Azure-resources tellen](../samples/starter.md#count-resources) |Alleen de eerste vereenvoudigde pagina |
|[Houd](/azure/kusto/query/takeoperator) |[Een lijst van alle openbare IP-adressen weergeven](../samples/starter.md#list-publicip) |Synoniem van`limit` |
|[top](/azure/kusto/query/topoperator) |[De eerste vijf virtuele machines met de naam en het type besturings systeem weer geven](../samples/starter.md#show-sorted) | |
|[Réunion](/azure/kusto/query/unionoperator) |[Resultaten van twee query's combi neren tot één resultaat](../samples/advanced.md#unionresults) |Eén tabel _toegestaan:_ `| union` \[ `kind=` `inner` \] _Table_tabel kolom naam _ColumnName_ . \| `outer` \] \[ `withsource=` Maxi maal drie `union` zijden in één query. Het is niet toegestaan `union` om de tabel met fuzzy op te lossen. Kan worden gebruikt binnen één tabel of tussen de tabellen _resources_ en _ResourceContainers_ . |
|[positie](/azure/kusto/query/whereoperator) |[Resources weer geven die opslag bevatten](../samples/starter.md#show-storage) | |

## <a name="escape-characters"></a>Escape tekens

Sommige eigenschapnamen van eigenschappen, zoals die van een `.` or `$`, moeten in de query worden verpakt of worden geescaped of de naam van de eigenschap wordt onjuist geïnterpreteerd en levert niet de verwachte resultaten op.

- `.`-Laat de naam van de eigenschap als volgt teruglopen:`['propertyname.withaperiod']`
  
  Voorbeeld query waarmee de eigenschap _odata. type_wordt geterugloopd:

  ```kusto
  where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.['odata.type']
  ```

- `$`-Escape het teken in de naam van de eigenschap. Welk escape teken wordt gebruikt, is afhankelijk van de resource grafiek van de shell wordt uitgevoerd vanaf.

  - **bash** - `\`

    Voorbeeld query waarmee het eigenschaps _ \$type_ wordt verescapet in bash:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.\$type
    ```

  - **cmd** : laat het `$` teken onescape.

  - **Zo** - ``` ` ```

    Voorbeeld query waarmee het eigenschaps _ \$type_ in Power shell wordt geescapet:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.`$type
    ```

## <a name="next-steps"></a>Volgende stappen

- Zie de taal die wordt gebruikt in [Start query's](../samples/starter.md).
- Zie Geavanceerd gebruik in [Geavanceerde query's](../samples/advanced.md).
- Meer informatie over hoe u [resources kunt verkennen](explore-resources.md).