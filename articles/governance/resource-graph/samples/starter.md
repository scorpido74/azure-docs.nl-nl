---
title: Voorbeelden van starter query's
description: Gebruik Azure Resource Graph om een aantal starterquery's uit te voeren, waaronder het tellen van resources, het ordenen van resources, of het opvragen op een specifieke tag.
ms.date: 11/21/2019
ms.topic: sample
ms.openlocfilehash: b966d8c239cb6ff706c967174bcea23bf25de374
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79238396"
---
# <a name="starter-resource-graph-query-samples"></a>Queryvoorbeelden van Starter Resource Graph

Om inzicht te krijgen in query's met Azure Resource Graph moet u eerst enige basiskennis hebben van de [querytaal](../concepts/query-language.md). Als u nog niet bekend bent met [Kusto Query Language (KQL),](/azure/kusto/query/index)is het raadzaam om de [zelfstudie voor KQL](/azure/kusto/query/tutorial) te bekijken om te begrijpen hoe u aanvragen opstellen voor de resources die u zoekt.

We nemen de volgende starter query's door:

> [!div class="checklist"]
> - [Azure-resources tellen](#count-resources)
> - [Belangrijke kluisbronnen tellen](#count-keyvaults)
> - [Bronnen op naam gesorteerd](#list-resources)
> - [Alle virtuele machines op naam weergeven in aflopende volgorde](#show-vms)
> - [Eerste vijf virtuele machines weergeven op naam en het type besturingssysteem](#show-sorted)
> - [Virtuele machines tellen op type besturingssysteem](#count-os)
> - [Resources weergeven die opslag bevatten](#show-storage)
> - [Een lijst van alle openbare IP-adressen weergeven](#list-publicip)
> - [Resources tellen waarvan IP-adressen zijn geconfigureerd per abonnement](#count-resources-by-ip)
> - [Bronnen met een specifieke tagwaarde weergeven](#list-tag)
> - [Alle opslagaccounts met een specifieke tagwaarde weergeven](#list-specific-tag)
> - [Aliassen weergeven voor een virtuele machinebron](#show-aliases)
> - [Afzonderlijke waarden voor een specifieke alias weergeven](#distinct-alias-values)
> - [Niet-gekoppelde netwerkbeveiligingsgroepen weergeven](#unassociated-nsgs)

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free) voordat u begint.

## <a name="language-support"></a>Taalondersteuning

Azure CLI (met een extensie) en Azure PowerShell (met een module) ondersteunen Azure Resource Graph. Controleer voordat u een van de volgende query's uitvoert, of uw omgeving gereed is. Zie [Azure CLI](../first-query-azurecli.md#add-the-resource-graph-extension) en [Azure PowerShell](../first-query-powershell.md#add-the-resource-graph-module) voor stappen voor het installeren en valideren van uw gewenste shellomgeving.

## <a name="count-azure-resources"></a><a name="count-resources" />Azure-resources tellen

Deze query retourneert het aantal Azure-resources in de abonnementen waartoe u toegang hebt. Het is ook een goede query om te valideren of in uw gekozen shell de juiste Azure Resource Graph-onderdelen correct zijn geïnstalleerd.

```kusto
Resources
| summarize count()
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | summarize count()"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | summarize count()"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

![Pictogram Resource Graph explorer](../media/resource-graph-small.png) Probeer deze query in Azure Resource Graph Explorer:

- Azure-portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20summarize%20count()" target="_blank">portal.azure.com</a> ![Koppeling openen in nieuw vensterpictogram](../../media/new-window.png)
- Azure Government-portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20summarize%20count()" target="_blank">portal.azure.us</a> ![koppeling openen in nieuw vensterpictogram](../../media/new-window.png)
- Azure China-portal: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20summarize%20count()" target="_blank">portal.azure.cn</a> ![koppeling openen in nieuw vensterpictogram](../../media/new-window.png)

---

## <a name="count-key-vault-resources"></a><a name="count-keyvaults" />Belangrijke kluisbronnen tellen

Deze query `count` gebruikt `summarize` in plaats van het aantal geretourneerde records te tellen. Alleen sleutelkluizen zijn inbegrepen in de telling.

```kusto
Resources
| where type =~ 'microsoft.keyvault/vaults'
| count
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.keyvault/vaults' | count"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.keyvault/vaults' | count"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

![Pictogram Resource Graph explorer](../media/resource-graph-small.png) Probeer deze query in Azure Resource Graph Explorer:

- Azure-portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.keyvault%2Fvaults'%20%7C%20count" target="_blank">portal.azure.com</a> ![Koppeling openen in nieuw vensterpictogram](../../media/new-window.png)
- Azure Government-portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.keyvault%2Fvaults'%20%7C%20count" target="_blank">portal.azure.us</a> ![koppeling openen in nieuw vensterpictogram](../../media/new-window.png)
- Azure China-portal: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.keyvault%2Fvaults'%20%7C%20count" target="_blank">portal.azure.cn</a> ![koppeling openen in nieuw vensterpictogram](../../media/new-window.png)

---

## <a name="list-resources-sorted-by-name"></a><a name="list-resources" />Een lijst van resources weergeven, gesorteerd op naam

Deze query retourneert een willekeurig resourcetype, maar alleen de eigenschappen **naam**, **type** en **locatie**. De query maakt gebruik van `order by` om de eigenschappen in oplopende volgorde (`asc`) op de eigenschap **naam** te sorteren.

```kusto
Resources
| project name, type, location
| order by name asc
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | project name, type, location | order by name asc"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project name, type, location | order by name asc"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

![Pictogram Resource Graph explorer](../media/resource-graph-small.png) Probeer deze query in Azure Resource Graph Explorer:

- Azure-portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20project%20name%2C%20type%2C%20location%20%7C%20order%20by%20name%20asc" target="_blank">portal.azure.com</a> ![Koppeling openen in nieuw vensterpictogram](../../media/new-window.png)
- Azure Government-portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20project%20name%2C%20type%2C%20location%20%7C%20order%20by%20name%20asc" target="_blank">portal.azure.us</a> ![koppeling openen in nieuw vensterpictogram](../../media/new-window.png)
- Azure China-portal: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20project%20name%2C%20type%2C%20location%20%7C%20order%20by%20name%20asc" target="_blank">portal.azure.cn</a> ![koppeling openen in nieuw vensterpictogram](../../media/new-window.png)

---

## <a name="show-all-virtual-machines-ordered-by-name-in-descending-order"></a><a name="show-vms" />Alle virtuele machines weergeven, aflopend geordend op naam

Als u alleen virtuele machines wilt vermelden (die van het type `Microsoft.Compute/virtualMachines` zijn), kan een overeenkomst worden gezocht met de eigenschap **type** in de resultaten. Net als in de vorige query verandert u met `desc` de `order by` in aflopend. De `=~` in de type-overeenkomst betekent in Resource Graph dat de sortering hoofdlettergevoelig is.

```kusto
Resources
| project name, location, type
| where type =~ 'Microsoft.Compute/virtualMachines'
| order by name desc
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | project name, location, type| where type =~ 'Microsoft.Compute/virtualMachines' | order by name desc"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project name, location, type| where type =~ 'Microsoft.Compute/virtualMachines' | order by name desc"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

![Pictogram Resource Graph explorer](../media/resource-graph-small.png) Probeer deze query in Azure Resource Graph Explorer:

- Azure-portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20project%20name%2C%20location%2C%20type%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20order%20by%20name%20desc" target="_blank">portal.azure.com</a> ![Koppeling openen in nieuw vensterpictogram](../../media/new-window.png)
- Azure Government-portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20project%20name%2C%20location%2C%20type%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20order%20by%20name%20desc" target="_blank">portal.azure.us</a> ![koppeling openen in nieuw vensterpictogram](../../media/new-window.png)
- Azure China-portal: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20project%20name%2C%20location%2C%20type%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20order%20by%20name%20desc" target="_blank">portal.azure.cn</a> ![koppeling openen in nieuw vensterpictogram](../../media/new-window.png)

---

## <a name="show-first-five-virtual-machines-by-name-and-their-os-type"></a><a name="show-sorted" />De eerste vijf virtuele machines weergeven op naam en met hun type besturingssysteem

Deze query gebruikt `top` om slechts vijf overeenkomende records op te halen, gesorteerd op naam. Het type van de Azure-resource is `Microsoft.Compute/virtualMachines`. `project` geeft in Azure Resource Graph aan welke eigenschappen u wilt opnemen.

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| project name, properties.storageProfile.osDisk.osType
| top 5 by name desc
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | project name, properties.storageProfile.osDisk.osType | top 5 by name desc"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | project name, properties.storageProfile.osDisk.osType | top 5 by name desc"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

![Pictogram Resource Graph explorer](../media/resource-graph-small.png) Probeer deze query in Azure Resource Graph Explorer:

- Azure-portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20project%20name%2C%20properties.storageProfile.osDisk.osType%20%7C%20top%205%20by%20name%20desc" target="_blank">portal.azure.com</a> ![Koppeling openen in nieuw vensterpictogram](../../media/new-window.png)
- Azure Government-portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20project%20name%2C%20properties.storageProfile.osDisk.osType%20%7C%20top%205%20by%20name%20desc" target="_blank">portal.azure.us</a> ![koppeling openen in nieuw vensterpictogram](../../media/new-window.png)
- Azure China-portal: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20project%20name%2C%20properties.storageProfile.osDisk.osType%20%7C%20top%205%20by%20name%20desc" target="_blank">portal.azure.cn</a> ![koppeling openen in nieuw vensterpictogram](../../media/new-window.png)

---

## <a name="count-virtual-machines-by-os-type"></a><a name="count-os" />Virtuele machines tellen op type besturingssysteem

Voortbouwend op de vorige query zijn we nog steeds een grens aan het stellen aan het aantal Azure-resources van het type `Microsoft.Compute/virtualMachines`, maar beperken we niet langer het aantal geretourneerde records.
In plaats daarvan hebben we `summarize` en `count()` gebruikt om te definiëren hoe we de waarden willen groeperen en aggregeren op basis van de eigenschap. In dit voorbeeld is dat `properties.storageProfile.osDisk.osType`. Voor een voorbeeld van hoe deze tekenreeks er uitziet in het volledige object, raadpleegt u [Resources verkennen - detectie van virtuele machines](../concepts/explore-resources.md#virtual-machine-discovery).

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| summarize count() by tostring(properties.storageProfile.osDisk.osType)
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by tostring(properties.storageProfile.osDisk.osType)"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by tostring(properties.storageProfile.osDisk.osType)"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

![Pictogram Resource Graph explorer](../media/resource-graph-small.png) Probeer deze query in Azure Resource Graph Explorer:

- Azure-portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20summarize%20count()%20by%20tostring(properties.storageProfile.osDisk.osType)" target="_blank">portal.azure.com</a> ![Koppeling openen in nieuw vensterpictogram](../../media/new-window.png)
- Azure Government-portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20summarize%20count()%20by%20tostring(properties.storageProfile.osDisk.osType)" target="_blank">portal.azure.us</a> ![koppeling openen in nieuw vensterpictogram](../../media/new-window.png)
- Azure China-portal: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20summarize%20count()%20by%20tostring(properties.storageProfile.osDisk.osType)" target="_blank">portal.azure.cn</a> ![koppeling openen in nieuw vensterpictogram](../../media/new-window.png)

---

Een andere manier om dezelfde query te schrijven, is door het `extend` van een eigenschap en deze een tijdelijke naam te geven voor gebruik in de query, in dit geval **os**. **os** wordt vervolgens door `summarize` en `count()` gebruikt, zoals in het vorige voorbeeld.

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| extend os = properties.storageProfile.osDisk.osType
| summarize count() by tostring(os)
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | extend os = properties.storageProfile.osDisk.osType | summarize count() by tostring(os)"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | extend os = properties.storageProfile.osDisk.osType | summarize count() by tostring(os)"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

![Pictogram Resource Graph explorer](../media/resource-graph-small.png) Probeer deze query in Azure Resource Graph Explorer:

- Azure-portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20extend%20os%20%3D%20properties.storageProfile.osDisk.osType%20%7C%20summarize%20count()%20by%20tostring(os)" target="_blank">portal.azure.com</a> ![Koppeling openen in nieuw vensterpictogram](../../media/new-window.png)
- Azure Government-portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20extend%20os%20%3D%20properties.storageProfile.osDisk.osType%20%7C%20summarize%20count()%20by%20tostring(os)" target="_blank">portal.azure.us</a> ![koppeling openen in nieuw vensterpictogram](../../media/new-window.png)
- Azure China-portal: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20extend%20os%20%3D%20properties.storageProfile.osDisk.osType%20%7C%20summarize%20count()%20by%20tostring(os)" target="_blank">portal.azure.cn</a> ![koppeling openen in nieuw vensterpictogram](../../media/new-window.png)

---

> [!NOTE]
> Hoewel u met `=~` naar overeenkomsten kunt zoeken zonder onderscheid te maken tussen hoofd- en kleine letters, moet het hoofdlettergebruik in de query correct zijn als u eigenschappen gebruikt (zoals **properties.storageProfile.osDisk.osType**). Als de eigenschap het verkeerde geval is, wordt een niet-of onjuiste waarde geretourneerd en is de groepering of sommatie onjuist.

## <a name="show-resources-that-contain-storage"></a><a name="show-storage" />Resources weergeven die opslag bevatten

In plaats van expliciet het type te definiëren dat u zoekt, zoekt deze voorbeeldquery elke Azure-resource die het woord **opslag**`contains`.

```kusto
Resources
| where type contains 'storage' | distinct type
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type contains 'storage' | distinct type"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type contains 'storage' | distinct type"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

![Pictogram Resource Graph explorer](../media/resource-graph-small.png) Probeer deze query in Azure Resource Graph Explorer:

- Azure-portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20contains%20'storage'%20%7C%20distinct%20type" target="_blank">portal.azure.com</a> ![Koppeling openen in nieuw vensterpictogram](../../media/new-window.png)
- Azure Government-portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20contains%20'storage'%20%7C%20distinct%20type" target="_blank">portal.azure.us</a> ![koppeling openen in nieuw vensterpictogram](../../media/new-window.png)
- Azure China-portal: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20contains%20'storage'%20%7C%20distinct%20type" target="_blank">portal.azure.cn</a> ![koppeling openen in nieuw vensterpictogram](../../media/new-window.png)

---

## <a name="list-all-public-ip-addresses"></a><a name="list-publicip" />Een lijst van alle openbare IP-adressen weergeven

Net als bij de vorige query vindt deze query alles dat een type is met het woord **publicIPAddresses**.
Deze query wordt uitgebreid op dat patroon om alleen resultaten op te nemen waar `limit` **properties.ipAddress**
`isnotempty`, om alleen de **properties.ipAddress**en de resultaten door de top te retourneren
100. Afhankelijk van uw gekozen shell, kan het zijn dat u de aanhalingstekens tussen escape-tekens moet plaatsen.

```kusto
Resources
| where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress)
| project properties.ipAddress
| limit 100
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | project properties.ipAddress | limit 100"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | project properties.ipAddress | limit 100"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

![Pictogram Resource Graph explorer](../media/resource-graph-small.png) Probeer deze query in Azure Resource Graph Explorer:

- Azure-portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20contains%20'publicIPAddresses'%20and%20isnotempty(properties.ipAddress)%20%7C%20project%20properties.ipAddress%20%7C%20limit%20100" target="_blank">portal.azure.com</a> ![Koppeling openen in nieuw vensterpictogram](../../media/new-window.png)
- Azure Government-portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20contains%20'publicIPAddresses'%20and%20isnotempty(properties.ipAddress)%20%7C%20project%20properties.ipAddress%20%7C%20limit%20100" target="_blank">portal.azure.us</a> ![koppeling openen in nieuw vensterpictogram](../../media/new-window.png)
- Azure China-portal: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20contains%20'publicIPAddresses'%20and%20isnotempty(properties.ipAddress)%20%7C%20project%20properties.ipAddress%20%7C%20limit%20100" target="_blank">portal.azure.cn</a> ![koppeling openen in nieuw vensterpictogram](../../media/new-window.png)

---

## <a name="count-resources-that-have-ip-addresses-configured-by-subscription"></a><a name="count-resources-by-ip" />Resources tellen met IP-adressen die zijn geconfigureerd op abonnement

Door aan de vorige voorbeeldquery `summarize` en `count()` toe te voegen, krijgen we een lijst per abonnement van resources met geconfigureerde IP-adressen.

```kusto
Resources
| where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress)
| summarize count () by subscriptionId
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | summarize count () by subscriptionId"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | summarize count () by subscriptionId"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

![Pictogram Resource Graph explorer](../media/resource-graph-small.png) Probeer deze query in Azure Resource Graph Explorer:

- Azure-portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20contains%20'publicIPAddresses'%20and%20isnotempty(properties.ipAddress)%20%7C%20summarize%20count%20()%20by%20subscriptionId" target="_blank">portal.azure.com</a> ![Koppeling openen in nieuw vensterpictogram](../../media/new-window.png)
- Azure Government-portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20contains%20'publicIPAddresses'%20and%20isnotempty(properties.ipAddress)%20%7C%20summarize%20count%20()%20by%20subscriptionId" target="_blank">portal.azure.us</a> ![koppeling openen in nieuw vensterpictogram](../../media/new-window.png)
- Azure China-portal: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20contains%20'publicIPAddresses'%20and%20isnotempty(properties.ipAddress)%20%7C%20summarize%20count%20()%20by%20subscriptionId" target="_blank">portal.azure.cn</a> ![koppeling openen in nieuw vensterpictogram](../../media/new-window.png)

---

## <a name="list-resources-with-a-specific-tag-value"></a><a name="list-tag" />Een lijst weergeven van resources met een specifieke tagwaarde

We kunnen de resultaten beperken op basis van andere eigenschappen dan het type Azure-resource, bijvoorbeeld op basis van een tag. In dit voorbeeld filteren we op Azure-resources met de tagnaam **omgeving** en de waarde **Intern**.

```kusto
Resources
| where tags.environment=~'internal'
| project name
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where tags.environment=~'internal' | project name"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where tags.environment=~'internal' | project name"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

![Pictogram Resource Graph explorer](../media/resource-graph-small.png) Probeer deze query in Azure Resource Graph Explorer:

- Azure-portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20tags.environment%3D~'internal'%20%7C%20project%20name" target="_blank">portal.azure.com</a> ![Koppeling openen in nieuw vensterpictogram](../../media/new-window.png)
- Azure Government-portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20tags.environment%3D~'internal'%20%7C%20project%20name" target="_blank">portal.azure.us</a> ![koppeling openen in nieuw vensterpictogram](../../media/new-window.png)
- Azure China-portal: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20tags.environment%3D~'internal'%20%7C%20project%20name" target="_blank">portal.azure.cn</a> ![koppeling openen in nieuw vensterpictogram](../../media/new-window.png)

---

Als u ook wilt opgeven welke tags plus de bijbehorende waarden een resource heeft, voegt u de eigenschap **tags** aan het trefwoord `project` toe.

```kusto
Resources
| where tags.environment=~'internal'
| project name, tags
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where tags.environment=~'internal' | project name, tags"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where tags.environment=~'internal' | project name, tags"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

![Pictogram Resource Graph explorer](../media/resource-graph-small.png) Probeer deze query in Azure Resource Graph Explorer:

- Azure-portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20tags.environment%3D~'internal'%20%7C%20project%20name%2C%20tags" target="_blank">portal.azure.com</a> ![Koppeling openen in nieuw vensterpictogram](../../media/new-window.png)
- Azure Government-portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20tags.environment%3D~'internal'%20%7C%20project%20name%2C%20tags" target="_blank">portal.azure.us</a> ![koppeling openen in nieuw vensterpictogram](../../media/new-window.png)
- Azure China-portal: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20tags.environment%3D~'internal'%20%7C%20project%20name%2C%20tags" target="_blank">portal.azure.cn</a> ![koppeling openen in nieuw vensterpictogram](../../media/new-window.png)

---

## <a name="list-all-storage-accounts-with-specific-tag-value"></a><a name="list-specific-tag" />Een lijst weergeven van alle opslagaccounts met een specifieke tagwaarde

Combineer de filterfunctie uit het vorige voorbeeld en filter het Azure-resourcetype op de eigenschap **type**. Deze query beperkt ook het zoeken naar specifieke typen Azure-resources met een specifieke tagnaam- en waarde.

```kusto
Resources
| where type =~ 'Microsoft.Storage/storageAccounts'
| where tags['tag with a space']=='Custom value'
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Storage/storageAccounts' | where tags['tag with a space']=='Custom value'"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Storage/storageAccounts' | where tags['tag with a space']=='Custom value'"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

![Pictogram Resource Graph explorer](../media/resource-graph-small.png) Probeer deze query in Azure Resource Graph Explorer:

- Azure-portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Storage%2FstorageAccounts'%20%7C%20where%20tags%5B'tag%20with%20a%20space'%5D%3D%3D'Custom%20value'" target="_blank">portal.azure.com</a> ![Koppeling openen in nieuw vensterpictogram](../../media/new-window.png)
- Azure Government-portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Storage%2FstorageAccounts'%20%7C%20where%20tags%5B'tag%20with%20a%20space'%5D%3D%3D'Custom%20value'" target="_blank">portal.azure.us</a> ![koppeling openen in nieuw vensterpictogram](../../media/new-window.png)
- Azure China-portal: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Storage%2FstorageAccounts'%20%7C%20where%20tags%5B'tag%20with%20a%20space'%5D%3D%3D'Custom%20value'" target="_blank">portal.azure.cn</a> ![koppeling openen in nieuw vensterpictogram](../../media/new-window.png)

---

> [!NOTE]
> In dit voorbeeld wordt `==` in plaats van het voorwaardelijke `=~` gebruikt om naar overeenkomsten te zoeken. `==` is een hoofdlettergevoelige overeenkomst.

## <a name="show-aliases-for-a-virtual-machine-resource"></a><a name="show-aliases" />Aliassen weergeven voor een virtuele machinebron

[Azure Policy-aliassen](../../policy/concepts/definition-structure.md#aliases) worden door Azure Policy gebruikt om de naleving van resources te beheren. Azure Resource Graph kan de aliassen van een resourcetype _retourneren._ Deze waarden zijn handig voor het vergelijken van de huidige waarde van aliassen bij het maken van een aangepaste beleidsdefinitie. De array _aliassen_ wordt standaard niet standaard weergegeven in de resultaten van een query. Gebruik `project aliases` om het expliciet toe te voegen aan de resultaten.

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| limit 1
| project aliases
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | limit 1 | project aliases"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | limit 1 | project aliases" | ConvertTo-Json
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

![Pictogram Resource Graph explorer](../media/resource-graph-small.png) Probeer deze query in Azure Resource Graph Explorer:

- Azure-portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20limit%201%20%7C%20project%20aliases" target="_blank">portal.azure.com</a> ![Koppeling openen in nieuw vensterpictogram](../../media/new-window.png)
- Azure Government-portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20limit%201%20%7C%20project%20aliases" target="_blank">portal.azure.us</a> ![koppeling openen in nieuw vensterpictogram](../../media/new-window.png)
- Azure China-portal: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20limit%201%20%7C%20project%20aliases" target="_blank">portal.azure.cn</a> ![koppeling openen in nieuw vensterpictogram](../../media/new-window.png)

---

## <a name="show-distinct-values-for-a-specific-alias"></a><a name="distinct-alias-values" />Afzonderlijke waarden voor een specifieke alias weergeven

Het is handig om de waarde van aliassen op één resource te zien, maar het geeft niet de werkelijke waarde weer van het gebruik van Azure Resource Graph om verschillende abonnementen op te vragen. In dit voorbeeld wordt gekeken naar alle waarden van een specifieke alias en wordt de afzonderlijke waarden geretourneerd.

```kusto
Resources
| where type=~'Microsoft.Compute/virtualMachines'
| extend alias = aliases['Microsoft.Compute/virtualMachines/storageProfile.osDisk.managedDisk.storageAccountType']
| distinct tostring(alias)
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type=~'Microsoft.Compute/virtualMachines' | extend alias = aliases['Microsoft.Compute/virtualMachines/storageProfile.osDisk.managedDisk.storageAccountType'] | distinct tostring(alias)"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type=~'Microsoft.Compute/virtualMachines' | extend alias = aliases['Microsoft.Compute/virtualMachines/storageProfile.osDisk.managedDisk.storageAccountType'] | distinct tostring(alias)"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

![Pictogram Resource Graph explorer](../media/resource-graph-small.png) Probeer deze query in Azure Resource Graph Explorer:

- Azure-portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%3D~'Microsoft.Compute%2FvirtualMachines'%20%7C%20extend%20alias%20%3D%20aliases%5B'Microsoft.Compute%2FvirtualMachines%2FstorageProfile.osDisk.managedDisk.storageAccountType'%5D%20%7C%20distinct%20tostring(alias)" target="_blank">portal.azure.com</a> ![Koppeling openen in nieuw vensterpictogram](../../media/new-window.png)
- Azure Government-portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%3D~'Microsoft.Compute%2FvirtualMachines'%20%7C%20extend%20alias%20%3D%20aliases%5B'Microsoft.Compute%2FvirtualMachines%2FstorageProfile.osDisk.managedDisk.storageAccountType'%5D%20%7C%20distinct%20tostring(alias)" target="_blank">portal.azure.us</a> ![koppeling openen in nieuw vensterpictogram](../../media/new-window.png)
- Azure China-portal: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%3D~'Microsoft.Compute%2FvirtualMachines'%20%7C%20extend%20alias%20%3D%20aliases%5B'Microsoft.Compute%2FvirtualMachines%2FstorageProfile.osDisk.managedDisk.storageAccountType'%5D%20%7C%20distinct%20tostring(alias)" target="_blank">portal.azure.cn</a> ![koppeling openen in nieuw vensterpictogram](../../media/new-window.png)

---

## <a name="show-unassociated-network-security-groups"></a><a name="unassociated-nsgs" />Niet-gekoppelde netwerkbeveiligingsgroepen weergeven

Met deze query worden Netwerkbeveiligingsgroepen (NSG's) geretourneerd die niet zijn gekoppeld aan een netwerkinterface of subnet.

```kusto
Resources
| where type =~ "microsoft.network/networksecuritygroups" and isnull(properties.networkInterfaces) and isnull(properties.subnets)
| project name, resourceGroup
| sort by name asc
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.network/networksecuritygroups' and isnull(properties.networkInterfaces) and isnull(properties.subnets) | project name, resourceGroup | sort by name asc"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.network/networksecuritygroups' and isnull(properties.networkInterfaces) and isnull(properties.subnets) | project name, resourceGroup | sort by name asc"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

![Pictogram Resource Graph explorer](../media/resource-graph-small.png) Probeer deze query in Azure Resource Graph Explorer:

- Azure-portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.network%2Fnetworksecuritygroups'%20and%20isnull(properties.networkInterfaces)%20and%20isnull(properties.subnets)%20%7C%20project%20name%2C%20resourceGroup%20%7C%20sort%20by%20name%20asc" target="_blank">portal.azure.com</a> ![Koppeling openen in nieuw vensterpictogram](../../media/new-window.png)
- Azure Government-portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.network%2Fnetworksecuritygroups'%20and%20isnull(properties.networkInterfaces)%20and%20isnull(properties.subnets)%20%7C%20project%20name%2C%20resourceGroup%20%7C%20sort%20by%20name%20asc" target="_blank">portal.azure.us</a> ![koppeling openen in nieuw vensterpictogram](../../media/new-window.png)
- Azure China-portal: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.network%2Fnetworksecuritygroups'%20and%20isnull(properties.networkInterfaces)%20and%20isnull(properties.subnets)%20%7C%20project%20name%2C%20resourceGroup%20%7C%20sort%20by%20name%20asc" target="_blank">portal.azure.cn</a> ![koppeling openen in nieuw vensterpictogram](../../media/new-window.png)

---

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [querytaal](../concepts/query-language.md).
- Meer informatie over het [verkennen van bronnen.](../concepts/explore-resources.md)
- Zie voorbeelden van [geavanceerde query's](advanced.md).