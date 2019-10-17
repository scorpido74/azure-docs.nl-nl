---
title: Voorbeelden van geavanceerde query's
description: Gebruik Azure resource Graph om geavanceerde query's uit te voeren, waaronder capaciteit van de schaalset voor virtuele machines, waarbij alle gebruikte labels worden weer gegeven en overeenkomende virtuele machines met reguliere expressies.
author: DCtheGeek
ms.author: dacoulte
ms.date: 10/18/2019
ms.topic: quickstart
ms.service: resource-graph
ms.openlocfilehash: 8b848eac338260340cb9160a72f33294e51101f2
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387576"
---
# <a name="advanced-resource-graph-queries"></a>Geavanceerde query's van Resource Graph

Om inzicht te krijgen in query's met Azure Resource Graph moet u eerst enige basiskennis hebben van de [querytaal](../concepts/query-language.md). Als u nog geen ervaring hebt met [Azure Data Explorer](../../../data-explorer/data-explorer-overview.md), is het raadzaam om eerst de basisbeginselen door te nemen, zodat u weet hoe u aanvragen opstelt voor de resources die u zoekt.

We nemen de volgende geavanceerde query's door:

> [!div class="checklist"]
> - [De capaciteit en grootte van schaal sets voor virtuele machines ophalen](#vmss-capacity)
> - [Kolommen verwijderen uit resultaten](#remove-column)
> - [Een lijst weergeven van alle tagnamen](#list-all-tags)
> - [Virtuele machines zoeken met reguliere expressies](#vm-regex)
> - [Cosmos DB met specifieke schrijf locaties weer geven](#mvexpand-cosmosdb)
> - [Sleutel kluis met de naam van het abonnement](#join)
> - [SQL-data bases en hun elastische Pools weer geven](#join-sql)
> - [Virtuele machines weer geven met hun netwerk interface en openbaar IP-adres](#join-vmpip)
> - [Opslag accounts met een specifieke tag in de resource groep zoeken](#join-findstoragetag)
> - [Resultaten van twee query's combi neren tot één resultaat](#unionresults)
> - [De namen van de Tenant en het abonnement toevoegen met DisplayName](#displaynames)

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free) aan voordat u begint.

## <a name="language-support"></a>Taalondersteuning

Azure CLI (met een extensie) en Azure PowerShell (met een module) ondersteunen Azure Resource Graph. Controleer voordat u een van de volgende query's uitvoert, of uw omgeving gereed is. Zie [Azure CLI](../first-query-azurecli.md#add-the-resource-graph-extension) en [Azure PowerShell](../first-query-powershell.md#add-the-resource-graph-module) voor stappen voor het installeren en valideren van uw gewenste shellomgeving.

## <a name="vmss-capacity"></a>Capaciteit en grootte van een virtuele-machineschaalset ophalen

Met deze query zoekt u virtuele-machineschaalsetresources en verkrijgt u diverse informatie, waaronder de grootte van de virtuele machines en de capaciteit van de schaalset. Deze query maakt gebruik van de functie `toint()` om de capaciteit te converteren naar een waarde die kan worden gesorteerd. Ten slotte worden de namen van de kolommen gewijzigd in eigenschappen met aangepaste namen.

```kusto
Resources
| where type=~ 'microsoft.compute/virtualmachinescalesets'
| where name contains 'contoso'
| project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name
| order by Capacity desc
```

```azurecli-interactive
az graph query -q "Resources | where type=~ 'microsoft.compute/virtualmachinescalesets' | where name contains 'contoso' | project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name | order by Capacity desc"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type=~ 'microsoft.compute/virtualmachinescalesets' | where name contains 'contoso' | project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name | order by Capacity desc"
```

## <a name="remove-column"></a>Kolommen verwijderen uit resultaten

De volgende query maakt gebruik van `summarize` om resources te tellen per abonnement, `join` om deze te combi neren met abonnements details uit de tabel _ResourceContainers_ , en vervolgens `project-away` om een aantal van de kolommen te verwijderen.

```kusto
Resources
| summarize resourceCount=count() by subscriptionId
| join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId
| project-away subscriptionId, subscriptionId1
```

```azurecli-interactive
az graph query -q "Resources | summarize resourceCount=count() by subscriptionId | join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId| project-away subscriptionId, subscriptionId1"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | summarize resourceCount=count() by subscriptionId | join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId| project-away subscriptionId, subscriptionId1"
```

## <a name="list-all-tags"></a>Een lijst weergeven van alle tagnamen

Deze query start met de tag en bouwt een JSON-object dat alle unieke tagnamen en hun overeenkomende typen vermeldt.

```kusto
Resources
| project tags
| summarize buildschema(tags)
```

```azurecli-interactive
az graph query -q "Resources | project tags | summarize buildschema(tags)"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project tags | summarize buildschema(tags)"
```

## <a name="vm-regex"></a>Virtuele machines zoeken met reguliere expressies

Deze query zoekt virtuele machines die overeenkomen met een [reguliere expressie](/dotnet/standard/base-types/regular-expression-language-quick-reference) (ook wel _regex_ genoemd). **Met de corresponderende regex \@** kan de regex worden gedefinieerd als match, die `^Contoso(.*)[0-9]+$` is.
De definitie van deze reguliere expressie wordt als volgt uitgelegd:

- `^`: Overeenkomst moet beginnen aan het begin van de tekenreeks.
- `Contoso`: De hoofdlettergevoelige reeks.
- `(.*)`: Een subexpressieovereenkomst:
  - `.`: Komt overeen met een willekeurig teken (met uitzondering van een nieuwe regel).
  - `*`: Komt nul keer of vaker overeen met vorig element.
- `[0-9]`: Tekengroepovereenkomst met de cijfers 0 t/m 9.
- `+`: Komt één keer of vaker overeen met vorig element.
- `$`: Overeenkomst met het vorige element moet voorkomen aan het einde van de tekenreeks.

Na de vergelijking op naam worden de namen in oplopende volgorde weergegeven.

```kusto
Resources
| where type =~ 'microsoft.compute/virtualmachines' and name matches regex @'^Contoso(.*)[0-9]+$'
| project name
| order by name asc
```

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.compute/virtualmachines' and name matches regex @'^Contoso(.*)[0-9]+$' | project name | order by name asc"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.compute/virtualmachines' and name matches regex @'^Contoso(.*)[0-9]+$' | project name | order by name asc"
```

## <a name="mvexpand-cosmosdb"></a>Cosmos DB met specifieke schrijf locaties weer geven

De volgende query is beperkt tot Cosmos DB resources, gebruikt `mv-expand` om de eigenschappen verzameling voor **Eigenschappen. writeLocations**uit te breiden, vervolgens project specifieke velden te gebruiken en de resultaten verder te beperken tot de waarden van **Eigenschappen. writeLocations. locatie** naam overeenkomende VS-Oost of VS-West.

```kusto
Resources
| where type =~ 'microsoft.documentdb/databaseaccounts'
| project id, name, writeLocations = (properties.writeLocations)
| mv-expand writeLocations
| project id, name, writeLocation = tostring(writeLocations.locationName)
| where writeLocation in ('East US', 'West US')
| summarize by id, name
```

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.documentdb/databaseaccounts' | project id, name, writeLocations = (properties.writeLocations) | mv-expand writeLocations | project id, name, writeLocation = tostring(writeLocations.locationName) | where writeLocation in ('East US', 'West US') | summarize by id, name"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.documentdb/databaseaccounts' | project id, name, writeLocations = (properties.writeLocations) | mv-expand writeLocations | project id, name, writeLocation = tostring(writeLocations.locationName) | where writeLocation in ('East US', 'West US') | summarize by id, name"
```

## <a name="join"></a>Sleutel kluis met de naam van het abonnement

Met de volgende query wordt een complex gebruik van `join` weer gegeven. Met de query wordt de gekoppelde tabel beperkt tot resources voor inschrijving en met `project` alleen het oorspronkelijke veld _subscriptionId_ en de _naam veld naam wijzigen_ in _subnaam_. De naam van het veld Vermijd `join` het toevoegen als _NAME1_ omdat het veld al in _resources_bestaat. De oorspronkelijke tabel wordt gefilterd met `where` en de volgende `project` bevat kolommen uit beide tabellen. Het query resultaat is een enkele sleutel kluis met het type, de naam van de sleutel kluis en de naam van het abonnement dat in wordt weer gegeven.

```kusto
Resources
| join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId
| where type == 'microsoft.keyvault/vaults'
| project type, name, SubName
| limit 1
```

```azurecli-interactive
az graph query -q "Resources | join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId | where type == 'microsoft.keyvault/vaults' | project type, name, SubName| limit 1"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId | where type == 'microsoft.keyvault/vaults' | project type, name, SubName| limit 1"
```

## <a name="join-sql"></a>SQL-data bases en hun elastische Pools weer geven

De volgende query maakt gebruik van **leftouter** `join` om SQL database resources en de bijbehorende elastische Pools samen te brengen, indien aanwezig.

```kusto
Resources
| where type =~ 'microsoft.sql/servers/databases'
| project databaseId = id, databaseName = name, elasticPoolId = tolower(tostring(properties.elasticPoolId))
| join kind=leftouter (
    Resources
    | where type =~ 'microsoft.sql/servers/elasticpools'
    | project elasticPoolId = tolower(id), elasticPoolName = name, elasticPoolState = properties.state)
on elasticPoolId
| project-away elasticPoolId1
```

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.sql/servers/databases' | project databaseId = id, databaseName = name, elasticPoolId = tolower(tostring(properties.elasticPoolId)) | join kind=leftouter ( Resources | where type =~ 'microsoft.sql/servers/elasticpools' | project elasticPoolId = tolower(id), elasticPoolName = name, elasticPoolState = properties.state) on elasticPoolId | project-away elasticPoolId1"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.sql/servers/databases' | project databaseId = id, databaseName = name, elasticPoolId = tolower(tostring(properties.elasticPoolId)) | join kind=leftouter ( Resources | where type =~ 'microsoft.sql/servers/elasticpools' | project elasticPoolId = tolower(id), elasticPoolName = name, elasticPoolState = properties.state) on elasticPoolId | project-away elasticPoolId1"
```

## <a name="join-vmpip"></a>Virtuele machines weer geven met hun netwerk interface en openbaar IP-adres

Deze query gebruikt twee @no__t **leftouter** -opdrachten voor het samen voegen van virtuele machines, hun gerelateerde netwerk interfaces en een openbaar IP-adres dat betrekking heeft op deze netwerk interfaces.

```kusto
Resources
| where type =~ 'microsoft.compute/virtualmachines'
| extend nics=array_length(properties.networkProfile.networkInterfaces) 
| mvexpand nic=properties.networkProfile.networkInterfaces 
| where nics == 1 or nic.properties.primary =~ 'true' or isempty(nic) 
| project vmId = id, vmName = name, vmSize=tostring(properties.hardwareProfile.vmSize), nicId = tostring(nic.id) 
| join kind=leftouter (
    Resources
    | where type =~ 'microsoft.network/networkinterfaces'
    | extend ipConfigsCount=array_length(properties.ipConfigurations) 
    | mvexpand ipconfig=properties.ipConfigurations 
    | where ipConfigsCount == 1 or ipconfig.properties.primary =~ 'true'
    | project nicId = id, publicIpId = tostring(ipconfig.properties.publicIPAddress.id))
on nicId
| project-away nicId1
| summarize by vmId, vmName, vmSize, nicId, publicIpId
| join kind=leftouter (
    Resources
    | where type =~ 'microsoft.network/publicipaddresses'
    | project publicIpId = id, publicIpAddress = properties.ipAddress)
on publicIpId
| project-away publicIpId1
```

```azurecli-interactive
azure graph query -q "Resources | where type =~ 'microsoft.compute/virtualmachines' | extend nics=array_length(properties.networkProfile.networkInterfaces) | mvexpand nic=properties.networkProfile.networkInterfaces | where nics == 1 or nic.properties.primary =~ 'true' or isempty(nic) | project vmId = id, vmName = name, vmSize=tostring(properties.hardwareProfile.vmSize), nicId = tostring(nic.id) | join kind=leftouter ( Resources | where type =~ 'microsoft.network/networkinterfaces' | extend ipConfigsCount=array_length(properties.ipConfigurations) | mvexpand ipconfig=properties.ipConfigurations | where ipConfigsCount == 1 or ipconfig.properties.primary =~ 'true' | project nicId = id, publicIpId = tostring(ipconfig.properties.publicIPAddress.id)) on nicId | project-away nicId1 | summarize by vmId, vmName, vmSize, nicId, publicIpId | join kind=leftouter ( Resources | where type =~ 'microsoft.network/publicipaddresses' | project publicIpId = id, publicIpAddress = properties.ipAddress) on publicIpId | project-away publicIpId1"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.compute/virtualmachines' | extend nics=array_length(properties.networkProfile.networkInterfaces) | mvexpand nic=properties.networkProfile.networkInterfaces | where nics == 1 or nic.properties.primary =~ 'true' or isempty(nic) | project vmId = id, vmName = name, vmSize=tostring(properties.hardwareProfile.vmSize), nicId = tostring(nic.id) | join kind=leftouter ( Resources | where type =~ 'microsoft.network/networkinterfaces' | extend ipConfigsCount=array_length(properties.ipConfigurations) | mvexpand ipconfig=properties.ipConfigurations | where ipConfigsCount == 1 or ipconfig.properties.primary =~ 'true' | project nicId = id, publicIpId = tostring(ipconfig.properties.publicIPAddress.id)) on nicId | project-away nicId1 | summarize by vmId, vmName, vmSize, nicId, publicIpId | join kind=leftouter ( Resources | where type =~ 'microsoft.network/publicipaddresses' | project publicIpId = id, publicIpAddress = properties.ipAddress) on publicIpId | project-away publicIpId1"
```

## <a name="join-findstoragetag"></a>Opslag accounts met een specifieke tag in de resource groep zoeken

De volgende query maakt gebruik van een **innerunique** `join` om opslag accounts te verbinden met resource groepen met een opgegeven label naam en label waarde.

```kusto
Resources
| where type =~ 'microsoft.storage/storageaccounts'
| join kind=innerunique (
    ResourceContainers
    | where type =~ 'microsoft.resources/subscriptions/resourcegroups'
    | where tags['key1'] == 'value1'
    | project subscriptionId, resourceGroup)
on subscriptionId, resourceGroup
| project-away subscriptionId1, resourceGroup1
```

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.storage/storageaccounts' | join kind=innerunique ( ResourceContainers | where type =~ 'microsoft.resources/subscriptions/resourcegroups' | where tags['key1'] == 'value1' | project subscriptionId, resourceGroup) on subscriptionId, resourceGroup | project-away subscriptionId1, resourceGroup1"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.storage/storageaccounts' | join kind=innerunique ( ResourceContainers | where type =~ 'microsoft.resources/subscriptions/resourcegroups' | where tags['key1'] == 'value1' | project subscriptionId, resourceGroup) on subscriptionId, resourceGroup | project-away subscriptionId1, resourceGroup1"
```

## <a name="unionresults"></a>Resultaten van twee query's combi neren tot één resultaat

De volgende query maakt gebruik van `union` om resultaten uit de tabel _ResourceContainers_ te halen en toe te voegen aan de resultaten van de tabel _resources_ .

```kusto
ResourceContainers
| where type=='microsoft.resources/subscriptions/resourcegroups' | project name, type  | limit 5
| union  (Resources | project name, type | limit 5)
```

```azurecli-interactive
az graph query -q "ResourceContainers | where type=='microsoft.resources/subscriptions/resourcegroups' | project name, type  | limit 5 | union  (Resources | project name, type | limit 5)"
```

```azurepowershell-interactive
Search-AzGraph -Query "ResourceContainers | where type=='microsoft.resources/subscriptions/resourcegroups' | project name, type  | limit 5 | union  (Resources | project name, type | limit 5)"
```

## <a name="displaynames"></a>De namen van de Tenant en het abonnement toevoegen met DisplayName

Deze query gebruikt de nieuwe para meter **include** met de optie _DisplayName_ om **subscriptionDisplayName** en **tenantDisplayName** toe te voegen aan de resultaten. Deze para meter is alleen beschikbaar voor Azure CLI en Azure PowerShell.

```azurecli-interactive
az graph query -q "limit 1" --include displayNames
```

```azurepowershell-interactive
Search-AzGraph -Query "limit 1" -Include DisplayNames
```

> [!NOTE]
> Als de query geen gebruik maakt van **project** om de geretourneerde eigenschappen op te geven, worden **subscriptionDisplayName** en **tenantDisplayName** automatisch opgenomen in de resultaten.
> Als de query gebruikmaakt van **project**, moeten alle _DisplayName_ -velden expliciet worden opgenomen in het **project** of worden ze niet geretourneerd in de resultaten, zelfs niet wanneer de para meter **include** wordt gebruikt.

## <a name="next-steps"></a>Volgende stappen

- Voorbeelden uit [Starter query's](starter.md) bekijken
- Meer informatie over de [querytaal](../concepts/query-language.md)
- [Resources verkennen](../concepts/explore-resources.md)