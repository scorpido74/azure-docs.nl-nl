---
title: Resources, resourcegroepen en abonnementen voor logische organisatie taggen
description: Hier ziet u hoe u tags toepast om Azure-resources te ordenen voor facturering en beheer.
ms.topic: conceptual
ms.date: 03/20/2020
ms.openlocfilehash: ffc97df0923e26c3abf0eed8e7810f3b1dc61ed2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132249"
---
# <a name="use-tags-to-organize-your-azure-resources-and-management-hierarchy"></a>Tags gebruiken om uw Azure-resources en beheerhiërarchie te ordenen

U past tags toe op uw Azure-bronnen, resourcegroepen en abonnementen om ze logisch te ordenen in een taxonomie. Elke tag bestaat uit een naam en een waardepaar. U kunt de naam Omgeving en de waarde Productie bijvoorbeeld toepassen op alle resources in de productie.

Zie Handleiding voor het benoemen en [taggen](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)van resources voor aanbevelingen over het implementeren van een strategie voor het implementeren van tags.

[!INCLUDE [Handle personal data](../../../includes/gdpr-intro-sentence.md)]

## <a name="required-access"></a>Vereiste toegang

Als u tags wilt toepassen op een resource, moet u schrijftoegang hebben tot het brontype **Microsoft.Resources/tags.** Met de rol **Tag Contributor** u tags toepassen op een entiteit zonder toegang te hebben tot de entiteit zelf.

De [rol Inzender](../../role-based-access-control/built-in-roles.md#contributor) verleent ook de vereiste toegang om tags toe te passen op elke entiteit. Als u tags op slechts één resourcetype wilt toepassen, gebruikt u de rol inzender voor die resource. Als u bijvoorbeeld tags wilt toepassen op virtuele machines, gebruikt u de [virtuele machinebijdrager](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor).

## <a name="powershell"></a>PowerShell

### <a name="apply-tags"></a>Tags toepassen

Azure PowerShell biedt twee opdrachten voor het toepassen van tags - [Nieuw-AzTag](/powershell/module/az.resources/new-aztag) en [Update-AzTag](/powershell/module/az.resources/update-aztag). U moet over Azure PowerShell 3.6.1 of hoger beschikken om deze opdrachten te kunnen gebruiken.

De **Nieuw-AzTag** vervangt alle tags op de resource, resourcegroep of abonnement. Wanneer u de opdracht aanroept, geeft u de resource-id in van de entiteit die u wilt taggen.

In het volgende voorbeeld wordt een set tags op een opslagaccount gebruikt:

```azurepowershell-interactive
$tags = @{"Dept"="Finance"; "Status"="Normal"}
$resource = Get-AzResource -Name demoStorage -ResourceGroup demoGroup
New-AzTag -ResourceId $resource.id -Tag $tags
```

Wanneer de opdracht is voltooid, ziet u dat de resource twee tags heeft.

```output
Properties :
        Name    Value
        ======  =======
        Dept    Finance
        Status  Normal
```

Als u de opdracht opnieuw uitvoert, maar deze keer met verschillende tags, ziet u dat de eerdere tags worden verwijderd.

```azurepowershell-interactive
$tags = @{"Team"="Compliance"; "Environment"="Production"}
New-AzTag -ResourceId $resource.id -Tag $tags
```

```output
Properties :
        Name         Value
        ===========  ==========
        Environment  Production
        Team         Compliance
```

Als u tags wilt toevoegen aan een bron die al tags heeft, gebruikt u **Update-AzTag**. Stel de parameter **-Bewerking** in op **Samenvoegen**.

```azurepowershell-interactive
$tags = @{"Dept"="Finance"; "Status"="Normal"}
Update-AzTag -ResourceId $resource.id -Tag $tags -Operation Merge
```

Merk op dat de twee nieuwe tags zijn toegevoegd aan de twee bestaande tags.

```output
Properties :
        Name         Value
        ===========  ==========
        Status       Normal
        Dept         Finance
        Team         Compliance
        Environment  Production
```

Elke tagnaam kan slechts één waarde hebben. Als u een nieuwe waarde voor een tag opgeeft, wordt de oude waarde vervangen, zelfs als u de samenvoegbewerking gebruikt. In het volgende voorbeeld wordt de tag Status gewijzigd van Normaal naar Groen.

```azurepowershell-interactive
$tags = @{"Status"="Green"}
Update-AzTag -ResourceId $resource.id -Tag $tags -Operation Merge
```

```output
Properties :
        Name         Value
        ===========  ==========
        Status       Green
        Dept         Finance
        Team         Compliance
        Environment  Production
```

Wanneer u de parameter **-Bewerking** instelt **op Vervangen,** worden de bestaande tags vervangen door de nieuwe set tags.

```azurepowershell-interactive
$tags = @{"Project"="ECommerce"; "CostCenter"="00123"; "Team"="Web"}
Update-AzTag -ResourceId $resource.id -Tag $tags -Operation Replace
```

Alleen de nieuwe tags blijven op de bron.

```output
Properties :
        Name        Value
        ==========  =========
        CostCenter  00123
        Team        Web
        Project     ECommerce
```

Dezelfde opdrachten werken ook met resourcegroepen of abonnementen. U geeft de id door voor de resourcegroep of het abonnement dat u wilt taggen.

Als u een nieuwe set tags aan een resourcegroep wilt toevoegen, gebruikt u:

```azurepowershell-interactive
$tags = @{"Dept"="Finance"; "Status"="Normal"}
$resourceGroup = Get-AzResourceGroup -Name demoGroup
New-AzTag -ResourceId $resourceGroup.ResourceId -tag $tags
```

Als u de tags voor een resourcegroep wilt bijwerken, gebruikt u het:

```azurepowershell-interactive
$tags = @{"CostCenter"="00123"; "Environment"="Production"}
$resourceGroup = Get-AzResourceGroup -Name demoGroup
Update-AzTag -ResourceId $resourceGroup.ResourceId -Tag $tags -Operation Merge
```

Als u een nieuwe set tags aan een abonnement wilt toevoegen, gebruikt u het volgende:

```azurepowershell-interactive
$tags = @{"CostCenter"="00123"; "Environment"="Dev"}
$subscription = (Get-AzSubscription -SubscriptionName "Example Subscription").Id
New-AzTag -ResourceId "/subscriptions/$subscription" -Tag $tags
```

Gebruik het als u de tags voor een abonnement wilt bijwerken:

```azurepowershell-interactive
$tags = @{"Team"="Web Apps"}
$subscription = (Get-AzSubscription -SubscriptionName "Example Subscription").Id
Update-AzTag -ResourceId "/subscriptions/$subscription" -Tag $tags -Operation Merge
```

U hebt mogelijk meer dan één resource met dezelfde naam in een resourcegroep. In dat geval u elke resource instellen met de volgende opdrachten:

```azurepowershell-interactive
$resource = Get-AzResource -ResourceName sqlDatabase1 -ResourceGroupName examplegroup
$resource | ForEach-Object { Update-AzTag -Tag @{ "Dept"="IT"; "Environment"="Test" } -ResourceId $_.ResourceId -Operation Merge }
```

### <a name="list-tags"></a>Tags weergeven

Als u de tags voor een resource, resourcegroep of abonnement wilt krijgen, gebruikt u de opdracht [Get-AzTag](/powershell/module/az.resources/get-aztag) en geeft u de bron-id voor de entiteit door.

Gebruik het als u de tags voor een resource wilt weergeven:

```azurepowershell-interactive
$resource = Get-AzResource -Name demoStorage -ResourceGroup demoGroup
Get-AzTag -ResourceId $resource.id
```

Gebruik het als u de tags voor een resourcegroep wilt weergeven:

```azurepowershell-interactive
$resourceGroup = Get-AzResourceGroup -Name demoGroup
Get-AzTag -ResourceId $resourceGroup.ResourceId
```

Gebruik het als u de tags voor een abonnement wilt bekijken:

```azurepowershell-interactive
$subscription = (Get-AzSubscription -SubscriptionName "Example Subscription").Id
Get-AzTag -ResourceId "/subscriptions/$subscription"
```

### <a name="list-by-tag"></a>Lijst op tag

Gebruik het als u resources wilt krijgen met een specifieke tagnaam en -waarde:

```azurepowershell-interactive
(Get-AzResource -Tag @{ "CostCenter"="00123"}).Name
```

Gebruik het als u resources wilt krijgen met een specifieke tagnaam met een tagwaarde:

```azurepowershell-interactive
(Get-AzResource -TagName "Dept").Name
```

Gebruik het als u resourcegroepen met een specifieke tagnaam en -waarde wilt gebruiken:

```azurepowershell-interactive
(Get-AzResourceGroup -Tag @{ "CostCenter"="00123" }).ResourceGroupName
```

### <a name="remove-tags"></a>Tags verwijderen

Als u specifieke tags wilt verwijderen, gebruikt u **Update-AzTag** en stelt **u De bewerking** in om te **verwijderen**. Geef de tags door die u wilt verwijderen.

```azurepowershell-interactive
$removeTags = @{"Project"="ECommerce"; "Team"="Web"}
Update-AzTag -ResourceId $resource.id -Tag $removeTags -Operation Delete
```

De opgegeven tags worden verwijderd.

```output
Properties :
        Name        Value
        ==========  =====
        CostCenter  00123
```

Als u alle tags wilt verwijderen, gebruikt u de opdracht [Verwijderen-AzTag.](/powershell/module/az.resources/remove-aztag)

```azurepowershell-interactive
$subscription = (Get-AzSubscription -SubscriptionName "Example Subscription").Id
Remove-AzTag -ResourceId "/subscriptions/$subscription"
```

## <a name="azure-cli"></a>Azure-CLI

### <a name="apply-tags"></a>Tags toepassen

Wanneer u tags toevoegt aan een resourcegroep of resource, u de bestaande tags overschrijven of nieuwe tags toevoegen aan bestaande tags.

Als u de tags op een resource wilt overschrijven, gebruikt u het als:

```azurecli-interactive
az resource tag --tags 'Dept=IT' 'Environment=Test' -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks"
```

Gebruik het als u een tag wilt toevoegen aan de bestaande tags op een resource:

```azurecli-interactive
az resource update --set tags.'Status'='Approved' -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks"
```

Als u de bestaande tags in een resourcegroep wilt overschrijven, gebruikt u het als:

```azurecli-interactive
az group update -n examplegroup --tags 'Environment=Test' 'Dept=IT'
```

Als u een tag wilt toevoegen aan de bestaande tags in een resourcegroep, gebruikt u:

```azurecli-interactive
az group update -n examplegroup --set tags.'Status'='Approved'
```

Momenteel biedt Azure CLI geen ondersteuning voor het toepassen van tags op abonnementen.

### <a name="list-tags"></a>Tags weergeven

Gebruik het als u de bestaande tags voor een resource wilt bekijken:

```azurecli-interactive
az resource show -n examplevnet -g examplegroup --resource-type "Microsoft.Network/virtualNetworks" --query tags
```

Gebruik het volgende om de bestaande tags van een resourcegroep te bekijken:

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

### <a name="list-by-tag"></a>Lijst op tag

`az resource list`Gebruik:

```azurecli-interactive
az resource list --tag Dept=Finance
```

`az group list`Gebruik:

```azurecli-interactive
az group list --tag Dept=IT
```

### <a name="handling-spaces"></a>Ruimten verwerken

Als uw tagnamen of -waarden spaties bevatten, moet u een paar extra stappen nemen. In het volgende voorbeeld worden alle tags uit een resourcegroep op de resources gebruikt wanneer de tags spaties kunnen bevatten.

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

U resources, resourcegroepen en abonnementen taggen tijdens de implementatie met een resourcemanagersjabloon.

### <a name="apply-values"></a>Waarden toepassen

In het volgende voorbeeld wordt een opslagaccount met drie tags geïmplementeerd. Twee van de`Dept` `Environment`tags (en) zijn ingesteld op letterlijke waarden. Eén tag`LastDeployed`( ) is ingesteld op een parameter die standaard wordt ingesteld op de huidige datum.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "utcShort": {
            "type": "string",
            "defaultValue": "[utcNow('d')]"
        },
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
                "Environment": "Production",
                "LastDeployed": "[parameters('utcShort')]"
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

### <a name="apply-an-object"></a>Een object toepassen

U kunt een objectparameter definiëren waarmee verschillende tags worden opgeslagen en dit object vervolgens toepassen op het tagelement. Deze benadering biedt meer flexibiliteit dan het vorige voorbeeld omdat het object verschillende eigenschappen kan hebben. Elke eigenschap in het object wordt een afzonderlijke tag voor de resource. Het volgende voorbeeld heeft een parameter met de naam `tagValues` die wordt toegepast op het tagelement.

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

### <a name="apply-a-json-string"></a>Een JSON-tekenreeks toepassen

Als u veel waarden wilt opslaan in een enkele tag, past u een JSON-tekenreeks toe die de waarden vertegenwoordigt. De hele JSON-tekenreeks wordt opgeslagen als één tag die niet meer dan 256 tekens kan bevatten. Het volgende voorbeeld heeft een enkele tag met de naam `CostCenter` die verschillende waarden uit een JSON-tekenreeks bevat:  

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

### <a name="apply-tags-from-resource-group"></a>Tags toepassen uit resourcegroep

Als u tags uit een resourcegroep wilt toepassen op een resource, gebruikt u de functie [resourceGroep.](../templates/template-functions-resource.md#resourcegroup) Wanneer u de tagwaarde `tags[tag-name]` krijgt, `tags.tag-name` gebruikt u de syntaxis in plaats van de syntaxis, omdat sommige tekens niet correct worden geparseerd in de notatie van de stip.

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

### <a name="apply-tags-to-resource-groups-or-subscriptions"></a>Tags toepassen op resourcegroepen of -abonnementen

U tags toevoegen aan een brongroep of -abonnement door het brontype **Microsoft.Resources/tags** te implementeren. De tags worden toegepast op de doelgroep of het abonnement voor de implementatie. Elke keer dat u de sjabloon implementeert, vervangt u alle tags die er eerder zijn toegepast.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "tagName": {
            "type": "string",
            "defaultValue": "TeamName"
        },
        "tagValue": {
            "type": "string",
            "defaultValue": "AppTeam1"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Resources/tags",
            "name": "default",
            "apiVersion": "2019-10-01",
            "dependsOn": [],
            "properties": {
                "tags": {
                    "[parameters('tagName')]": "[parameters('tagValue')]"
                }
            }
        }
    ]
}
```

Als u de tags wilt toepassen op een resourcegroep, gebruikt u PowerShell of Azure CLI. Implementeren in de resourcegroep die u wilt taggen.

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName exampleGroup -TemplateFile https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/tags.json
```

```azurecli-interactive
az deployment group create --resource-group exampleGroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/tags.json
```

Als u de tags wilt toepassen op een abonnement, gebruikt u PowerShell of Azure CLI. Implementeren in het abonnement dat u wilt taggen.

```azurepowershell-interactive
New-AzSubscriptionDeployment -name tagresourcegroup -Location westus2 -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/tags.json
```

```azurecli-interactive
az deployment sub create --name tagresourcegroup --location westus2 --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/tags.json
```

In de volgende sjabloon worden de tags van een object toegevoegd aan een resourcegroep of -abonnement.

```json
"$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "tags": {
            "type": "object",
            "defaultValue": {
                "TeamName": "AppTeam1",
                "Dept": "Finance",
                "Environment": "Production"
            }
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Resources/tags",
            "name": "default",
            "apiVersion": "2019-10-01",
            "dependsOn": [],
            "properties": {
                "tags": "[parameters('tags')]"
            }
        }
    ]
}
```

## <a name="portal"></a>Portal

[!INCLUDE [resource-manager-tag-resource](../../../includes/resource-manager-tag-resources.md)]

## <a name="rest-api"></a>REST API

Gebruik het als u met tags wilt werken via de Azure REST API:

* [Tags - Maken of bijwerken op scope](/rest/api/resources/tags/createorupdateatscope) (PUT-bewerking)
* [Tags - Update at Scope](/rest/api/resources/tags/updateatscope) (PATCH-bewerking)
* [Tags - Get At Scope](/rest/api/resources/tags/getatscope) (GET-bewerking)
* [Tags - Verwijderen bij bereik](/rest/api/resources/tags/deleteatscope) (bewerking VERWIJDEREN)

## <a name="inherit-tags"></a>Tags overnemen

Tags die worden toegepast op de brongroep of het abonnement, worden niet overgenomen door de resources. Zie [Azure Policies - tags](tag-policies.md)als u tags uit een abonnement of resourcegroep wilt toepassen op de bronnen.

## <a name="tags-and-billing"></a>Tags en facturering

U kunt tags gebruiken om uw factureringsgegevens te groeperen. Als u bijvoorbeeld meerdere VM's voor verschillende organisaties uitvoert, kunt u de tags gebruiken om het gebruiker te groeperen op basis van de kostenplaats. U kunt ook tags gebruiken om de kosten te categoriseren op basis van de runtimeomgeving, zoals de facturering van het gebruik voor VM's die worden uitgevoerd in de productieomgeving.

U informatie over tags ophalen via de [AZURE Resource Usage and RateCard API's](../../billing/billing-usage-rate-card-overview.md) of het CSV-bestand (Usage Comma-separated values). U downloadt het gebruiksbestand van het [Azure-accountcentrum](https://account.azure.com/Subscriptions) of de Azure-portal. Zie [Uw Azure-factuur en daggebruiksgegevens downloaden of weergeven](../../billing/billing-download-azure-invoice-daily-usage-date.md)voor meer informatie. Selecteer **versie 2**wanneer u het gebruiksbestand downloadt uit het Azure-accountcentrum . Voor services die tags ondersteunen bij facturering, worden de tags weergegeven in de kolom **Labels.**

Zie Azure Billing [REST API Reference](/rest/api/billing/)voor REST API-bewerkingen .

## <a name="limitations"></a>Beperkingen

Voor tags gelden de volgende beperkingen:

* Niet alle resourcetypen ondersteunen tags. Zie [Tagondersteuning voor Azure-bronnen](tag-support.md)als u wilt bepalen of u een tag op een resourcetype toepassen.
* Beheergroepen ondersteunen momenteel geen tags.
* Elke resource, resourcegroep en abonnement kan maximaal 50 tagnaam/waardeparen hebben. Als u meer tags moet toepassen dan het maximaal toegestane aantal, gebruikt u een JSON-tekenreeks voor de tagwaarde. De JSON-tekenreeks kan veel waarden bevatten die worden toegepast op een enkele tagnaam. Een resourcegroep of -abonnement kan veel bronnen bevatten die elk 50 tagnaam/waardeparen hebben.
* De tagnaam is beperkt tot 512 tekens en de tagwaarde is beperkt tot 256 tekens. Voor opslagaccounts is de tagnaam beperkt tot 128 tekens en de tagwaarde beperkt tot 256 tekens.
* Gegeneraliseerde VM's ondersteunen geen tags.
* Tags kunnen niet worden toegepast op klassieke bronnen zoals Cloud Services.
* Tagnamen mogen deze tekens `<`niet `>` `%`bevatten: , , `&`, `\` `?``/`

   > [!NOTE]
   > Op dit moment staan Azure DNS-zones en Traffic Manger-services ook het gebruik van spaties in de tag niet toe.

## <a name="next-steps"></a>Volgende stappen

* Niet alle resourcetypen ondersteunen tags. Zie [Tagondersteuning voor Azure-bronnen](tag-support.md)als u wilt bepalen of u een tag op een resourcetype toepassen.
* Zie Handleiding voor het benoemen en [taggen](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)van resources voor aanbevelingen over het implementeren van een strategie voor het implementeren van tags.
