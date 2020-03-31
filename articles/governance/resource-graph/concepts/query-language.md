---
title: Inzicht krijgen in de querytaal
description: Beschrijft Resource Graph-tabellen en de beschikbare Kusto-gegevenstypen, -operators en -functies die kunnen worden gebruikt met Azure Resource Graph.
ms.date: 03/07/2020
ms.topic: conceptual
ms.openlocfilehash: 2f4be4d86a340867e1ad3015ff288f98fc54cecf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78927484"
---
# <a name="understanding-the-azure-resource-graph-query-language"></a>De querytaal Azure Resource Graph begrijpen

De querytaal voor de Azure Resource Graph ondersteunt een aantal operators en functies. Elk werk en werk op basis [van Kusto Query Language (KQL)](/azure/kusto/query/index). Als u meer wilt weten over de querytaal die wordt gebruikt door Resource Graph, begint u met de [zelfstudie voor KQL](/azure/kusto/query/tutorial).

In dit artikel worden de taalcomponenten die worden ondersteund door Resource Graph:

- [Resourcegrafiektabellen](#resource-graph-tables)
- [Ondersteunde KQL-taalelementen](#supported-kql-language-elements)
- [Escape-personages](#escape-characters)

## <a name="resource-graph-tables"></a>Resourcegrafiektabellen

Resourcegraph bevat verschillende tabellen voor de gegevens die worden opgeslagen over resourcemanager-brontypen en hun eigenschappen. Deze tabellen kunnen `join` worden `union` gebruikt met of operators om eigenschappen te krijgen van gerelateerde resourcetypen. Hier is de lijst met tabellen die beschikbaar zijn in Resource Graph:

|Resourcegrafiektabellen |Beschrijving |
|---|---|
|Resources |De standaardtabel als deze niet is gedefinieerd in de query. De meeste resourcetypen en eigenschappen van Resource Manager zijn hier. |
|ResourceContainers |Inclusief abonnementstypen (in `Microsoft.Resources/subscriptions`preview --`Microsoft.Resources/subscriptions/resourcegroups`) en resourcegroep () resourcetypen en -gegevens. |
|AdvisorResources |Inclusief resources `Microsoft.Advisor`met _betrekking_ tot . |
|AlertsManagementResources |Inclusief resources `Microsoft.AlertsManagement`met _betrekking_ tot . |
|Onderhoudsresources |Inclusief resources `Microsoft.Maintenance`met _betrekking_ tot . |
|SecurityResources (SecurityResources) |Inclusief resources `Microsoft.Security`met _betrekking_ tot . |

Zie [Referentie: Ondersteunde tabellen en resourcetypen](../reference/supported-tables-resources.md)voor een volledige lijst met resourcetypen.

> [!NOTE]
> _Resources_ is de standaardtabel. Tijdens het opvragen van de tabel _Resources_ is het `join` `union` niet nodig om de tabelnaam op te geven, tenzij of worden gebruikt. De aanbevolen praktijk is echter om altijd de eerste tabel in de query op te nemen.

Gebruik Resource Graph Explorer in de portal om te ontdekken welke resourcetypen beschikbaar zijn in elke tabel. Als alternatief gebruikt u een `<tableName> | distinct type` query zoals om een lijst met resourcetypen te krijgen die de opgegeven resourcegrafiektabel ondersteunt die in uw omgeving bestaan.

De volgende query `join`toont een eenvoudige . Het queryresultaat combineert de kolommen samen en eventuele dubbele kolomnamen uit de samengevoegde tabel, _ResourceContainers_ in dit voorbeeld, worden toegevoegd met **1**. Aangezien de tabel _ResourceContainers_ typen heeft voor zowel abonnementen als resourcegroepen, kan een van beide typen worden gebruikt om lid te worden van de resource uit _de tabel Resources._

```kusto
Resources
| join ResourceContainers on subscriptionId
| limit 1
```

De volgende query toont een `join`complexer gebruik van . De query beperkt de samengevoegde tabel `project` tot abonnementsbronnen en bevat alleen het oorspronkelijke _veldabonnementId_ en het _naamveld_ omgedoopt tot _Subnaam_. Het veld wijzigt `join` de naam van het veld en voegt deze niet toe als _naam1,_ omdat het veld al bestaat in _Resources_. De oorspronkelijke tabel `where` wordt gefilterd `project` met en de volgende bevat kolommen uit beide tabellen. Het queryresultaat is een enkel sleutelkluis met type, de naam van de sleutelkluis en de naam van het abonnement waarin het zich bevindt.

```kusto
Resources
| where type == 'microsoft.keyvault/vaults'
| join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId
| project type, name, SubName
| limit 1
```

> [!NOTE]
> Bij het `join` beperken `project`van de `join` resultaten met , de eigenschap die wordt gebruikt door `project`de twee tabellen te relateren, _moet subscriptionId_ in het bovenstaande voorbeeld worden opgenomen in .

## <a name="supported-kql-language-elements"></a>Ondersteunde KQL-taalelementen

Resource Graph ondersteunt alle [KQL-gegevenstypen](/azure/kusto/query/scalar-data-types/), [scalaire functies](/azure/kusto/query/scalarfunctions), [scalaire operatoren](/azure/kusto/query/binoperators)en [aggregatiefuncties](/azure/kusto/query/any-aggfunction). Specifieke [tabeloperatoren](/azure/kusto/query/queries) worden ondersteund door Resource Graph, waarvan sommige verschillende gedragingen hebben.

### <a name="supported-tabulartop-level-operators"></a>Ondersteunde operatoren in tabel-/topniveau

Hier is de lijst van KQL tabeloperatoren ondersteund door Resource Graph met specifieke voorbeelden:

|KQL KQL |Voorbeeldquery resourcegrafiek |Opmerkingen |
|---|---|---|
|[Tellen](/azure/kusto/query/countoperator) |[Sleutelkluizen tellen](../samples/starter.md#count-keyvaults) | |
|[distinct](/azure/kusto/query/distinctoperator) |[Afzonderlijke waarden voor een specifieke alias weergeven](../samples/starter.md#distinct-alias-values) | |
|[Uitbreiden](/azure/kusto/query/extendoperator) |[Virtuele machines tellen op type besturingssysteem](../samples/starter.md#count-os) | |
|[join](/azure/kusto/query/joinoperator) |[Sleutelkluis met abonnementsnaam](../samples/advanced.md#join) |Join smaken ondersteund: [innerunique](/azure/kusto/query/joinoperator#default-join-flavor), [inner](/azure/kusto/query/joinoperator#inner-join), [leftouter](/azure/kusto/query/joinoperator#left-outer-join). Limiet van `join` 3 in één query. Aangepaste joinstrategieën, zoals broadcast join, zijn niet toegestaan. Kan worden gebruikt binnen één tabel of tussen de tabellen _Resources_ en _ResourceContainers._ |
|[Beperken](/azure/kusto/query/limitoperator) |[Een lijst van alle openbare IP-adressen weergeven](../samples/starter.md#list-publicip) |Synoniem van`take` |
|[mvexpand](/azure/kusto/query/mvexpandoperator) | | Legacy operator, `mv-expand` gebruik in plaats daarvan. _RowLimit_ max van 400. De standaardinstelling is 128. |
|[mv-uit te breiden](/azure/kusto/query/mvexpandoperator) |[Lijst Cosmos DB met specifieke schrijflocaties](../samples/advanced.md#mvexpand-cosmosdb) |_RowLimit_ max van 400. De standaardinstelling is 128. |
|[Volgorde](/azure/kusto/query/orderoperator) |[Bronnen op naam gesorteerd](../samples/starter.md#list-resources) |Synoniem van`sort` |
|[Project](/azure/kusto/query/projectoperator) |[Bronnen op naam gesorteerd](../samples/starter.md#list-resources) | |
|[project-away](/azure/kusto/query/projectawayoperator) |[Kolommen uit resultaten verwijderen](../samples/advanced.md#remove-column) | |
|[Sorteren](/azure/kusto/query/sortoperator) |[Bronnen op naam gesorteerd](../samples/starter.md#list-resources) |Synoniem van`order` |
|[Samenvatten](/azure/kusto/query/summarizeoperator) |[Azure-resources tellen](../samples/starter.md#count-resources) |Alleen vereenvoudigde eerste pagina |
|[Nemen](/azure/kusto/query/takeoperator) |[Een lijst van alle openbare IP-adressen weergeven](../samples/starter.md#list-publicip) |Synoniem van`limit` |
|[top](/azure/kusto/query/topoperator) |[Eerste vijf virtuele machines weergeven op naam en het type besturingssysteem](../samples/starter.md#show-sorted) | |
|[Unie](/azure/kusto/query/unionoperator) |[Resultaten van twee query's combineren tot één resultaat](../samples/advanced.md#unionresults) |`| union` \[ `kind=` `inner` \| `outer` \] \[Enkele tabel toegestaan: `withsource=` _T_ _ColumnName-tabel_ \] _Table_. Limiet van `union` 3 poten in een enkele query. Fuzzy resolutie `union` van been tafels is niet toegestaan. Kan worden gebruikt binnen één tabel of tussen de tabellen _Resources_ en _ResourceContainers._ |
|[Waar](/azure/kusto/query/whereoperator) |[Resources weergeven die opslag bevatten](../samples/starter.md#show-storage) | |

## <a name="escape-characters"></a>Escape-personages

Sommige eigenschapsnamen, zoals namen `.` `$`die een of , moeten worden verpakt of ontsnapt in de query of de eigenschapnaam wordt verkeerd geïnterpreteerd en biedt niet de verwachte resultaten.

- `.`- Wikkel de naam van de eigenschap als zodanig:`['propertyname.withaperiod']`
  
  Voorbeeldquery die de _eigenschap odata.type_omsluit:

  ```kusto
  where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.['odata.type']
  ```

- `$`- Ontsnap aan het personage in de eigendomsnaam. Het gebruikte escape-teken is afhankelijk van de shell Resource Graph wordt uitgevoerd.

  - **Bash** - `\`

    Voorbeeldquery die ontsnapt aan het _ \$eigenschapstype_ in bash:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.\$type
    ```

  - **cmd** - Ontsnap `$` niet aan het personage.

  - **Powershell** - ``` ` ```

    Voorbeeldquery die ontsnapt aan het _ \$eigenschapstype_ in PowerShell:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.`$type
    ```

## <a name="next-steps"></a>Volgende stappen

- Zie de taal die wordt gebruikt in [Starter-query's](../samples/starter.md).
- Zie geavanceerde toepassingen in [Geavanceerde query's](../samples/advanced.md).
- Meer informatie over het [verkennen van bronnen.](explore-resources.md)