---
title: Label resources voor logische organisatie
description: Laat zien hoe u Tags toepast om Azure-resources te organiseren voor facturering en beheer.
ms.topic: conceptual
ms.date: 01/03/2020
ms.openlocfilehash: c7f8d8672e205fa677bff33c8ed173c1105b26c6
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77166584"
---
# <a name="use-tags-to-organize-your-azure-resources"></a>Tags gebruiken om uw Azure-resources te organiseren

U past tags toe op uw Azure-resources om ze logisch in een taxonomie te organiseren. Elke tag bestaat uit een naam en een waardepaar. U kunt de naam Omgeving en de waarde Productie bijvoorbeeld toepassen op alle resources in de productie.

Nadat u de tags hebt toegepast, kunt u alle resources in het abonnement ophalen met deze tagnaam en -waarde. Met tags kunt u verwante resources ophalen uit verschillende resource groepen. Deze aanpak is nuttig wanneer u resources moet ordenen voor facturering of beheer.

Uw taxonomie moet rekening houden met een self-service voor het coderen van meta gegevens naast een strategie voor automatisch labelen om de belasting van gebruikers te verminderen en de nauw keurigheid te verg Roten.

[!INCLUDE [Handle personal data](../../../includes/gdpr-intro-sentence.md)]

## <a name="limitations"></a>Beperkingen

Voor tags gelden de volgende beperkingen:

* Niet alle resource typen ondersteunen Tags. Zie [tag-ondersteuning voor Azure-resources](tag-support.md)om te bepalen of u een tag kunt Toep assen op een resource type.
* Elke resource of resource groep mag Maxi maal 50 label naam/waarde-paren hebben. Als u meer tags wilt Toep assen dan het Maxi maal toegestane aantal, gebruikt u een JSON-teken reeks voor de waarde van de tag. De JSON-tekenreeks kan veel waarden bevatten die worden toegepast op een enkele tagnaam. Een resource groep kan veel resources bevatten die elk een 50-tag/waarde-paren hebben.
* De tagnaam is beperkt tot 512 tekens en de tagwaarde is beperkt tot 256 tekens. Voor opslagaccounts is de tagnaam beperkt tot 128 tekens en de tagwaarde beperkt tot 256 tekens.
* Gegeneraliseerde Vm's ondersteunen geen tags.
* Tags die zijn toegepast op de resourcegroep, worden niet overgenomen door de resources in deze resourcegroep.
* Labels kunnen niet worden toegepast op klassieke resources, zoals Cloud Services.
* Label namen mogen niet deze tekens bevatten: `<`, `>`, `%`, `&`, `\`, `?``/`

   > [!NOTE]
   > Op het moment dat u Azure DNS zones en Traffic Manager-services, is het gebruik van spaties in het label ook niet toegestaan. 

## <a name="required-access"></a>Vereiste toegang

Om labels toe te passen op resources, moet de gebruiker schrijf toegang hebben tot het bron type. Gebruik de rol [Inzender](../../role-based-access-control/built-in-roles.md#contributor) om labels toe te passen op alle resource typen. Als u labels wilt Toep assen op slechts één resource type, gebruikt u de rol Inzender voor die resource. Gebruik bijvoorbeeld de Inzender van de [virtuele machine](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)om labels toe te passen op virtuele machines.

## <a name="policies"></a>Beleidsregels

U kunt [Azure Policy](../../governance/policy/overview.md) gebruiken om regels en conventies voor code ring af te dwingen. Door een beleid te maken, vermijdt u het scenario van resources die worden geïmplementeerd in uw abonnement en die niet voldoen aan de verwachte Tags voor uw organisatie. In plaats van labels hand matig toe te passen of te zoeken naar resources die niet compatibel zijn, kunt u een beleid maken waarmee automatisch de benodigde Tags tijdens de implementatie worden toegepast. Labels kunnen nu ook worden toegepast op bestaande resources met het nieuwe [wijzigings](../../governance/policy/concepts/effects.md#modify) effect en een [herstel taak](../../governance/policy/how-to/remediate-resources.md). In de volgende sectie ziet u voor beelden van beleids regels voor Tags.

[!INCLUDE [Tag policies](../../../includes/azure-policy-samples-policies-tags.md)]

## <a name="powershell"></a>PowerShell

Gebruik het volgende om de bestaande tags van een *resourcegroep* te bekijken:

```azurepowershell-interactive
(Get-AzResourceGroup -Name examplegroup).Tags
```

Dat script retourneert de volgende indeling:

```powershell
Name                           Value
----                           -----
Dept                           IT
Environment                    Test
```

Als u de bestaande tags voor een *resource met een opgegeven naam en resource groep*wilt weer geven, gebruikt u:

```azurepowershell-interactive
(Get-AzResource -ResourceName examplevnet -ResourceGroupName examplegroup).Tags
```

Als u de resource-ID voor een resource hebt, kunt u deze resource-ID door geven om de tags op te halen.

```azurepowershell-interactive
(Get-AzResource -ResourceId /subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Storage/storageAccounts/<storage-name>).Tags
```

Als u *resource groepen wilt ophalen met een specifieke label naam en-waarde*, gebruikt u:

```azurepowershell-interactive
(Get-AzResourceGroup -Tag @{ "Dept"="Finance" }).ResourceGroupName
```

Als u *resources wilt ophalen met een specifieke label naam en-waarde*, gebruikt u:

```azurepowershell-interactive
(Get-AzResource -Tag @{ "Dept"="Finance"}).Name
```

Als u *resources met een specifieke label naam*wilt ophalen, gebruikt u:

```azurepowershell-interactive
(Get-AzResource -TagName "Dept").Name
```

Wanneer u tags op een resource of resourcegroep toepast, overschrijft u de bestaande tags van die resource of resourcegroep. Als u een resource of resourcegroep hebt met bestaande tags, gaat u anders te werk dan wanneer dat niet het geval is.

Gebruik het volgende om tags toe te voegen aan een *resourcegroep zonder bestaande tags*:

```azurepowershell-interactive
Set-AzResourceGroup -Name examplegroup -Tag @{ "Dept"="IT"; "Environment"="Test" }
```

Als u tags wilt toevoegen aan een *resourcegroep met bestaande tags*, haalt u de bestaande tags op, voegt u de nieuwe tag toe en past u de tags opnieuw toe:

```azurepowershell-interactive
$tags = (Get-AzResourceGroup -Name examplegroup).Tags
$tags.Add("Status", "Approved")
Set-AzResourceGroup -Tag $tags -Name examplegroup
```

Gebruik het volgende om tags toe te voegen aan een *resource zonder bestaande tags*:

```azurepowershell-interactive
$resource = Get-AzResource -ResourceName examplevnet -ResourceGroupName examplegroup
Set-AzResource -Tag @{ "Dept"="IT"; "Environment"="Test" } -ResourceId $resource.ResourceId -Force
```

Een resource groep kan meer dan één resource met dezelfde naam bevatten. In dat geval kunt u elke resource instellen met de volgende opdrachten:

```azurepowershell-interactive
$resource = Get-AzResource -ResourceName sqlDatabase1 -ResourceGroupName examplegroup
$resource | ForEach-Object { Set-AzResource -Tag @{ "Dept"="IT"; "Environment"="Test" } -ResourceId $_.ResourceId -Force }
```

Gebruik het volgende om tags toe te voegen aan een *resource met bestaande tags*:

```azurepowershell-interactive
$resource = Get-AzResource -ResourceName examplevnet -ResourceGroupName examplegroup
$resource.Tags.Add("Status", "Approved")
Set-AzResource -Tag $resource.Tags -ResourceId $resource.ResourceId -Force
```

Gebruik het volgende script om alle tags van een resource groep toe te passen op de bijbehorende resources en *geen bestaande tags op de resources te blijven*gebruiken:

```azurepowershell-interactive
$group = Get-AzResourceGroup -Name examplegroup
Get-AzResource -ResourceGroupName $group.ResourceGroupName | ForEach-Object {Set-AzResource -ResourceId $_.ResourceId -Tag $group.Tags -Force }
```

Gebruik het volgende script als u alle tags van een resource groep wilt Toep assen op de bijbehorende resources en *bestaande tags wilt blijven gebruiken voor resources die geen duplicaten zijn*:

```azurepowershell-interactive
$group = Get-AzResourceGroup -Name examplegroup
if ($null -ne $group.Tags) {
    $resources = Get-AzResource -ResourceGroupName $group.ResourceGroupName
    foreach ($r in $resources)
    {
        $resourcetags = (Get-AzResource -ResourceId $r.ResourceId).Tags
        if ($resourcetags)
        {
            foreach ($key in $group.Tags.Keys)
            {
                if (-not($resourcetags.ContainsKey($key)))
                {
                    $resourcetags.Add($key, $group.Tags[$key])
                }
            }
            Set-AzResource -Tag $resourcetags -ResourceId $r.ResourceId -Force
        }
        else
        {
            Set-AzResource -Tag $group.Tags -ResourceId $r.ResourceId -Force
        }
    }
}
```

Geef een lege hash-tabel door om alle tags te verwijderen:

```azurepowershell-interactive
Set-AzResourceGroup -Tag @{} -Name examplegroup
```

## <a name="azure-cli"></a>Azure CLI

Gebruik het volgende om de bestaande tags van een *resourcegroep* te bekijken:

```azurecli-interactive
az group show -n examplegroup --query tags
```

Dat script retourneert de volgende indeling:

```json
{
  "Dept"        : "IT",
  "Environment" : "Test"
}
```

Als u de bestaande tags voor een *resource met een opgegeven naam, type en resource groep*wilt weer geven, gebruikt u:

```azurecli-interactive
az resource show -n examplevnet -g examplegroup --resource-type "Microsoft.Network/virtualNetworks" --query tags
```

Wanneer u een verzameling resources wilt door lopen, kunt u de resource weer geven op Resource-ID. Verderop in dit artikel wordt een volledig voor beeld weer gegeven. Gebruik het volgende om de bestaande tags te bekijken van een *resource met een opgegeven bron-id*:

```azurecli-interactive
az resource show --id <resource-id> --query tags
```

Gebruik `az group list`om resource groepen met een specifieke tag op te halen:

```azurecli-interactive
az group list --tag Dept=IT
```

Als u alle resources met een bepaalde tag en waarde wilt ophalen, gebruikt u `az resource list`:

```azurecli-interactive
az resource list --tag Dept=Finance
```

Wanneer labels aan een resource groep of resource worden toegevoegd, kunt u de bestaande Tags overschrijven of nieuwe Tags toevoegen aan bestaande tags.

Als u de bestaande tags voor een resource groep wilt overschrijven, gebruikt u:

```azurecli-interactive
az group update -n examplegroup --tags 'Environment=Test' 'Dept=IT'
```

Als u een tag wilt toevoegen aan de bestaande tags voor een resource groep, gebruikt u:

```azurecli-interactive
az group update -n examplegroup --set tags.'Status'='Approved'
```

Als u de tags voor een resource wilt overschrijven, gebruikt u:

```azurecli-interactive
az resource tag --tags 'Dept=IT' 'Environment=Test' -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks"
```

Gebruik de volgende opdracht om een tag toe te voegen aan de bestaande tags op een resource:

```azurecli-interactive
az resource update --set tags.'Status'='Approved' -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks"
```

Gebruik het volgende script om alle tags van een resource groep toe te passen op de bijbehorende resources en *geen bestaande tags op de resources te blijven*gebruiken:

```azurecli-interactive
jsontags=$(az group show --name examplegroup --query tags -o json)
tags=$(echo $jsontags | tr -d '"{},' | sed 's/: /=/g')
resourceids=$(az resource list -g examplegroup --query [].id --output tsv)
for id in $resourceids
do
  az resource tag --tags $tags --id $id
done
```

Gebruik het volgende script als u alle tags van een resource groep wilt Toep assen op de bijbehorende resources en *bestaande tags op resources wilt blijven*gebruiken:

```azurecli-interactive
jsontags=$(az group show --name examplegroup --query tags -o json)
tags=$(echo $jsontags | tr -d '"{},' | sed 's/: /=/g')

resourceids=$(az resource list -g examplegroup --query [].id --output tsv)
for id in $resourceids
do
  resourcejsontags=$(az resource show --id $id --query tags -o json)
  resourcetags=$(echo $resourcejsontags | tr -d '"{},' | sed 's/: /=/g')
  az resource tag --tags $tags$resourcetags --id $id
done
```

Als de namen van tags of waarden spaties bevatten, moet u een paar extra stappen uitvoeren. In het volgende voor beeld worden alle labels van een resource groep op de bijbehorende resources toegepast wanneer de Tags spaties kunnen bevatten.

```azurecli-interactive
jsontags=$(az group show --name examplegroup --query tags -o json)
tags=$(echo $jsontags | tr -d '{}"' | sed 's/: /=/g' | sed "s/\"/'/g" | sed 's/, /,/g' | sed 's/ *$//g' | sed 's/^ *//g')
origIFS=$IFS
IFS=','
read -a tagarr <<< "$tags"
resourceids=$(az resource list -g examplegroup --query [].id --output tsv)
for id in $resourceids
do
  az resource tag --tags "${tagarr[@]}" --id $id
done
IFS=$origIFS
```

## <a name="templates"></a>Sjablonen

Als u een resource wilt labelen tijdens de implementatie, voegt u het `tags`-element toe aan de resource die u implementeert. Geef de naam en waarde van de tag op.

### <a name="apply-a-literal-value-to-the-tag-name"></a>Een letterlijke waarde toepassen op de tagnaam

In het volgende voorbeeld wordt een opslagaccount weergegeven met twee tags (`Dept` en `Environment`) die zijn ingesteld op letterlijke waarden:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[parameters('location')]",
            "tags": {
                "Dept": "Finance",
                "Environment": "Production"
            },
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        }
    ]
}
```

Als u een tag wilt instellen op een datum/tijd-waarde, gebruikt u de [functie utcNow](../templates/template-functions-string.md#utcnow).

### <a name="apply-an-object-to-the-tag-element"></a>Een object toepassen op het tagelement

U kunt een objectparameter definiëren waarmee verschillende tags worden opgeslagen en dit object vervolgens toepassen op het tagelement. Elke eigenschap in het object wordt een afzonderlijke tag voor de resource. Het volgende voorbeeld heeft een parameter met de naam `tagValues` die wordt toegepast op het tagelement.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        },
        "tagValues": {
            "type": "object",
            "defaultValue": {
                "Dept": "Finance",
                "Environment": "Production"
            }
        }
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[parameters('location')]",
            "tags": "[parameters('tagValues')]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        }
    ]
}
```

### <a name="apply-a-json-string-to-the-tag-name"></a>Een JSON-tekenreeks toepassen op de tagnaam

Als u veel waarden wilt opslaan in een enkele tag, past u een JSON-tekenreeks toe die de waarden vertegenwoordigt. De volledige JSON-teken reeks wordt opgeslagen als één tag die niet langer is dan 256 tekens. Het volgende voorbeeld heeft een enkele tag met de naam `CostCenter` die verschillende waarden uit een JSON-tekenreeks bevat:  

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[parameters('location')]",
            "tags": {
                "CostCenter": "{\"Dept\":\"Finance\",\"Environment\":\"Production\"}"
            },
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        }
    ]
}
```

### <a name="apply-tags-from-resource-group"></a>Tags Toep assen vanuit de resource groep

Als u labels van een resource groep wilt Toep assen op een resource, gebruikt u de functie [resourceGroup](../templates/template-functions-resource.md#resourcegroup) . Wanneer u de tag-waarde ophaalt, gebruikt u de syntaxis `tags[tag-name]` in plaats van de `tags.tag-name` syntaxis, omdat sommige tekens niet goed worden geparseerd in de punt notatie.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[parameters('location')]",
            "tags": {
                "Dept": "[resourceGroup().tags['Dept']]",
                "Environment": "[resourceGroup().tags['Environment']]"
            },
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        }
    ]
}
```

## <a name="portal"></a>Portal

[!INCLUDE [resource-manager-tag-resource](../../../includes/resource-manager-tag-resources.md)]

## <a name="rest-api"></a>REST-API

De Azure Portal en Power shell gebruiken beide de [Resource Manager rest API](/rest/api/resources/) achter de schermen. Als u tags wilt integreren in een andere omgeving, kunt u Tags ophalen met behulp van de resource-ID **ophalen** en de set met Tags bijwerken met behulp van een **patch** -oproep.

## <a name="tags-and-billing"></a>Tags en facturering

U kunt tags gebruiken om uw facturerings gegevens te groeperen. Als u bijvoorbeeld meerdere Vm's voor verschillende organisaties uitvoert, gebruikt u de labels om gebruik te groeperen op kosten plaats. U kunt ook labels gebruiken om de kosten te categoriseren per runtime-omgeving, zoals het facturerings gebruik voor Vm's die in de productie omgeving worden uitgevoerd.

U kunt informatie over Tags ophalen via het [Azure resource usage-en RateCard-api's](../../billing/billing-usage-rate-card-overview.md) of het gebruik van CSV-bestand (Comma Separated Values). U downloadt het gebruiks bestand van de [Azure-Accountcentrum](https://account.azure.com/Subscriptions) of Azure Portal. Zie uw Azure-factuur [en dagelijks gebruiks gegevens downloaden of weer geven](../../billing/billing-download-azure-invoice-daily-usage-date.md)voor meer informatie. Selecteer **versie 2**bij het downloaden van het gebruiks bestand van de Azure-Accountcentrum. Voor services die Tags ondersteunen met facturering, worden de tags weer gegeven in de kolom **Tags** .

Zie voor REST API bewerkingen de [Naslag informatie voor Azure billing rest API](/rest/api/billing/).

## <a name="next-steps"></a>Volgende stappen

* Niet alle resource typen ondersteunen Tags. Zie [tag-ondersteuning voor Azure-resources](tag-support.md)om te bepalen of u een tag kunt Toep assen op een resource type.
* Zie [de Azure Portal gebruiken om uw Azure-resources te beheren](manage-resource-groups-portal.md)voor een inleiding tot het gebruik van de portal.  
