---
title: Voorbeelden van starter query's
description: Gebruik Azure Resource Graph om een aantal starterquery's uit te voeren, waaronder het tellen van resources, het ordenen van resources, of het opvragen op een specifieke tag.
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/23/2019
ms.topic: quickstart
ms.service: resource-graph
manager: carmonm
ms.openlocfilehash: 688c591cbe94c69c73779843011cb24c3d2fd4cf
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70241085"
---
# <a name="starter-resource-graph-queries"></a>Starter query's van Resource Graph

Om inzicht te krijgen in query's met Azure Resource Graph moet u eerst enige basiskennis hebben van de [querytaal](../concepts/query-language.md). Als u nog geen ervaring hebt met [Azure Data Explorer](../../../data-explorer/data-explorer-overview.md), is het raadzaam om eerst de basisbeginselen door te nemen, zodat u weet hoe u aanvragen opstelt voor de resources die u zoekt.

We nemen de volgende starter query's door:

> [!div class="checklist"]
> - [Azure-resources tellen](#count-resources)
> - [Een lijst van resources weergeven, gesorteerd op naam](#list-resources)
> - [Alle virtuele machines weergeven, aflopend geordend op naam](#show-vms)
> - [De eerste vijf virtuele machines weergeven op naam en met hun type besturingssysteem](#show-sorted)
> - [Virtuele machines tellen op type besturingssysteem](#count-os)
> - [Resources weergeven die opslag bevatten](#show-storage)
> - [Een lijst van alle openbare IP-adressen weergeven](#list-publicip)
> - [Resources tellen met IP-adressen die zijn geconfigureerd op abonnement](#count-resources-by-ip)
> - [Een lijst weergeven van resources met een specifieke tagwaarde](#list-tag)
> - [Een lijst weergeven van alle opslagaccounts met een specifieke tagwaarde](#list-specific-tag)
> - [Aliassen weer geven voor een bron van een virtuele machine](#show-aliases)
> - [DISTINCT-waarden voor een specifieke alias weer geven](#distinct-alias-values)

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free) aan voordat u begint.

## <a name="language-support"></a>Taalondersteuning

Azure CLI (met een extensie) en Azure PowerShell (met een module) ondersteunen Azure Resource Graph. Controleer voordat u een van de volgende query's uitvoert, of uw omgeving gereed is. Zie [Azure CLI](../first-query-azurecli.md#add-the-resource-graph-extension) en [Azure PowerShell](../first-query-powershell.md#add-the-resource-graph-module) voor stappen voor het installeren en valideren van uw gewenste shellomgeving.

## <a name="a-namecount-resourcescount-azure-resources"></a><a name="count-resources"/>Azure-Resources tellen

Deze query retourneert het aantal Azure-resources in de abonnementen waartoe u toegang hebt. Het is ook een goede query om te valideren of in uw gekozen shell de juiste Azure Resource Graph-onderdelen correct zijn geïnstalleerd.

```kusto
summarize count()
```

```azurecli-interactive
az graph query -q "summarize count()"
```

```azurepowershell-interactive
Search-AzGraph -Query "summarize count()"
```

## <a name="a-namelist-resourceslist-resources-sorted-by-name"></a><a name="list-resources"/>Lijst met resources gesorteerd op naam

Deze query retourneert een willekeurig resourcetype, maar alleen de eigenschappen **naam**, **type** en **locatie**. De query maakt gebruik van `order by` om de eigenschappen in oplopende volgorde (`asc`) op de eigenschap **naam** te sorteren.

```kusto
project name, type, location
| order by name asc
```

```azurecli-interactive
az graph query -q "project name, type, location | order by name asc"
```

```azurepowershell-interactive
Search-AzGraph -Query "project name, type, location | order by name asc"
```

## <a name="a-nameshow-vmsshow-all-virtual-machines-ordered-by-name-in-descending-order"></a><a name="show-vms"/>Alle virtuele machines weer geven die op naam zijn gesorteerd in aflopende volg orde

Als u alleen virtuele machines wilt vermelden (die van het type `Microsoft.Compute/virtualMachines` zijn), kan een overeenkomst worden gezocht met de eigenschap **type** in de resultaten. Net als in de vorige query verandert u met `desc` de `order by` in aflopend. De `=~` in de type-overeenkomst betekent in Resource Graph dat de sortering hoofdlettergevoelig is.

```kusto
project name, location, type
| where type =~ 'Microsoft.Compute/virtualMachines'
| order by name desc
```

```azurecli-interactive
az graph query -q "project name, location, type| where type =~ 'Microsoft.Compute/virtualMachines' | order by name desc"
```

```azurepowershell-interactive
Search-AzGraph -Query "project name, location, type| where type =~ 'Microsoft.Compute/virtualMachines' | order by name desc"
```

## <a name="a-nameshow-sortedshow-first-five-virtual-machines-by-name-and-their-os-type"></a><a name="show-sorted"/>De eerste vijf virtuele machines met de naam en het type besturings systeem weer geven

Deze query gebruikt `top` om slechts vijf overeenkomende records op te halen, gesorteerd op naam. Het type van de Azure-resource is `Microsoft.Compute/virtualMachines`. `project` geeft in Azure Resource Graph aan welke eigenschappen u wilt opnemen.

```kusto
where type =~ 'Microsoft.Compute/virtualMachines'
| project name, properties.storageProfile.osDisk.osType
| top 5 by name desc
```

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Compute/virtualMachines' | project name, properties.storageProfile.osDisk.osType | top 5 by name desc"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type =~ 'Microsoft.Compute/virtualMachines' | project name, properties.storageProfile.osDisk.osType | top 5 by name desc"
```

## <a name="a-namecount-oscount-virtual-machines-by-os-type"></a><a name="count-os"/>Aantal virtuele machines per OS-type

Voortbouwend op de vorige query zijn we nog steeds een grens aan het stellen aan het aantal Azure-resources van het type `Microsoft.Compute/virtualMachines`, maar beperken we niet langer het aantal geretourneerde records.
In plaats daarvan hebben we `summarize` en `count()` gebruikt om te definiëren hoe we de waarden willen groeperen en aggregeren op basis van de eigenschap. In dit voorbeeld is dat `properties.storageProfile.osDisk.osType`. Voor een voorbeeld van hoe deze tekenreeks er uitziet in het volledige object, raadpleegt u [Resources verkennen - detectie van virtuele machines](../concepts/explore-resources.md#virtual-machine-discovery).

```kusto
where type =~ 'Microsoft.Compute/virtualMachines'
| summarize count() by tostring(properties.storageProfile.osDisk.osType)
```

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by tostring(properties.storageProfile.osDisk.osType)"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by tostring(properties.storageProfile.osDisk.osType)"
```

Een andere manier om dezelfde query te schrijven, is door het `extend` van een eigenschap en deze een tijdelijke naam te geven voor gebruik in de query, in dit geval **os**. **os** wordt vervolgens door `summarize` en `count()` gebruikt, zoals in het vorige voorbeeld.

```kusto
where type =~ 'Microsoft.Compute/virtualMachines'
| extend os = properties.storageProfile.osDisk.osType
| summarize count() by tostring(os)
```

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Compute/virtualMachines' | extend os = properties.storageProfile.osDisk.osType | summarize count() by tostring(os)"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type =~ 'Microsoft.Compute/virtualMachines' | extend os = properties.storageProfile.osDisk.osType | summarize count() by tostring(os)"
```

> [!NOTE]
> Hoewel u met `=~` naar overeenkomsten kunt zoeken zonder onderscheid te maken tussen hoofd- en kleine letters, moet het hoofdlettergebruik in de query correct zijn als u eigenschappen gebruikt (zoals **properties.storageProfile.osDisk.osType**). Als de hoofd-/kleine letters in de eigenschap niet overeenkomen, kan het zijn dat er alsnog een waarde wordt geretourneerd, maar de groepering of samenvatting zou dan onjuist juist.

## <a name="a-nameshow-storageshow-resources-that-contain-storage"></a><a name="show-storage"/>Resources weer geven die opslag bevatten

In plaats van expliciet het type te definiëren dat u zoekt, zoekt deze voorbeeldquery elke Azure-resource die het woord **opslag** `contains`.

```kusto
where type contains 'storage' | distinct type
```

```azurecli-interactive
az graph query -q "where type contains 'storage' | distinct type"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type contains 'storage' | distinct type"
```

## <a name="a-namelist-publiciplist-all-public-ip-addresses"></a><a name="list-publicip"/>Alle open bare IP-adressen weer geven

Net als bij de vorige query vindt deze query alles dat een type is met het woord **publicIPAddresses**.
Deze query breidt uit op dat patroon om alleen resultaten op te vragen waarbij **Eigenschappen. ipaddress**
`isnotempty`alleen `limit` de **Eigenschappen. ipaddress**en de resultaten van de bovenkant moeten retour neren.
100. Afhankelijk van uw gekozen shell, kan het zijn dat u de aanhalingstekens tussen escape-tekens moet plaatsen.

```kusto
where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress)
| project properties.ipAddress
| limit 100
```

```azurecli-interactive
az graph query -q "where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | project properties.ipAddress | limit 100"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | project properties.ipAddress | limit 100"
```

## <a name="a-namecount-resources-by-ipcount-resources-that-have-ip-addresses-configured-by-subscription"></a><a name="count-resources-by-ip"/>Resources met IP-adressen die zijn geconfigureerd met een abonnement tellen

Door aan de vorige voorbeeldquery `summarize` en `count()` toe te voegen, krijgen we een lijst per abonnement van resources met geconfigureerde IP-adressen.

```kusto
where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress)
| summarize count () by subscriptionId
```

```azurecli-interactive
az graph query -q "where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | summarize count () by subscriptionId"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | summarize count () by subscriptionId"
```

## <a name="a-namelist-taglist-resources-with-a-specific-tag-value"></a><a name="list-tag"/>Resources met een specifieke tag-waarde weer geven

We kunnen de resultaten beperken op basis van andere eigenschappen dan het type Azure-resource, bijvoorbeeld op basis van een tag. In dit voorbeeld filteren we op Azure-resources met de tagnaam **omgeving** en de waarde **Intern**.

```kusto
where tags.environment=~'internal'
| project name
```

```azurecli-interactive
az graph query -q "where tags.environment=~'internal' | project name"
```

```azurepowershell-interactive
Search-AzGraph -Query "where tags.environment=~'internal' | project name"
```

Als u ook wilt opgeven welke tags plus de bijbehorende waarden een resource heeft, voegt u de eigenschap **tags** aan het trefwoord `project` toe.

```kusto
where tags.environment=~'internal'
| project name, tags
```

```azurecli-interactive
az graph query -q "where tags.environment=~'internal' | project name, tags"
```

```azurepowershell-interactive
Search-AzGraph -Query "where tags.environment=~'internal' | project name, tags"
```

## <a name="a-namelist-specific-taglist-all-storage-accounts-with-specific-tag-value"></a><a name="list-specific-tag"/>Alle opslag accounts met een specifieke tag-waarde weer geven

Combineer de filterfunctie uit het vorige voorbeeld en filter het Azure-resourcetype op de eigenschap **type**. Deze query beperkt ook het zoeken naar specifieke typen Azure-resources met een specifieke tagnaam- en waarde.

```kusto
where type =~ 'Microsoft.Storage/storageAccounts'
| where tags['tag with a space']=='Custom value'
```

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Storage/storageAccounts' | where tags['tag with a space']=='Custom value'"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type =~ 'Microsoft.Storage/storageAccounts' | where tags['tag with a space']=='Custom value'"
```

> [!NOTE]
> In dit voorbeeld wordt `==` in plaats van het voorwaardelijke `=~` gebruikt om naar overeenkomsten te zoeken. `==` is een hoofdlettergevoelige overeenkomst.

## <a name="a-nameshow-aliasesshow-aliases-for-a-virtual-machine-resource"></a><a name="show-aliases"/>Aliassen weer geven voor een bron van een virtuele machine

[Azure Policy-aliassen](../../policy/concepts/definition-structure.md#aliases) worden door Azure Policy gebruikt voor het beheren van de naleving van resources. Azure resource Graph kan de _aliassen_ van een resource type retour neren. Deze waarden zijn handig voor het vergelijken van de huidige waarde van aliassen bij het maken van een aangepaste beleids definitie. De _aliassen_ matrix is niet standaard opgenomen in de resultaten van een query. Gebruik `project aliases` dit om het expliciet toe te voegen aan de resultaten.

```kusto
where type =~ 'Microsoft.Compute/virtualMachines'
| limit 1
| project aliases
```

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Compute/virtualMachines' | limit 1 | project aliases"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type =~ 'Microsoft.Compute/virtualMachines' | limit 1 | project aliases" | ConvertTo-Json
```

## <a name="a-namedistinct-alias-valuesshow-distinct-values-for-a-specific-alias"></a><a name="distinct-alias-values"/>DISTINCT-waarden voor een specifieke alias weer geven

Het is handig om de waarde van aliassen voor één bron te bekijken, maar de werkelijke waarde van het gebruik van Azure resource Graph wordt niet weer gegeven voor het uitvoeren van een query tussen abonnementen. In dit voor beeld worden alle waarden van een specifieke alias gecontroleerd en worden de afzonderlijke waarden geretourneerd.

```kusto
where type=~'Microsoft.Compute/virtualMachines'
| extend alias = aliases['Microsoft.Compute/virtualMachines/storageProfile.osDisk.managedDisk.storageAccountType']
| distinct tostring(alias)"
```

```azurecli-interactive
az graph query -q "where type=~'Microsoft.Compute/virtualMachines' | extend alias = aliases['Microsoft.Compute/virtualMachines/storageProfile.osDisk.managedDisk.storageAccountType'] | distinct tostring(alias)"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type=~'Microsoft.Compute/virtualMachines' | extend alias = aliases['Microsoft.Compute/virtualMachines/storageProfile.osDisk.managedDisk.storageAccountType'] | distinct tostring(alias)"
```

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [querytaal](../concepts/query-language.md)
- [Resources verkennen](../concepts/explore-resources.md)
- Voorbeelden uit [Geavanceerde query's](advanced.md) bekijken
