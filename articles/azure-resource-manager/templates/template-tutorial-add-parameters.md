---
title: Zelf studie-para meters aan sjabloon toevoegen
description: Voeg para meters toe aan uw Azure Resource Manager sjabloon om deze opnieuw te kunnen bruikbaar maken.
author: mumian
ms.date: 10/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 28c171dfa067ec9b3eff2e0d7e5d5dd0a0c274c0
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75472966"
---
# <a name="tutorial-add-parameters-to-your-resource-manager-template"></a>Zelf studie: para meters toevoegen aan uw Resource Manager-sjabloon

In de [vorige zelf studie](template-tutorial-add-resource.md)hebt u geleerd hoe u een opslag account toevoegt aan de sjabloon en hoe u deze implementeert. In deze zelf studie leert u hoe u de sjabloon kunt verbeteren door para meters toe te voegen. Deze zelf studie duurt ongeveer **14 minuten** .

## <a name="prerequisites"></a>Vereisten

U wordt aangeraden de [zelf studie over resources](template-tutorial-add-resource.md)te volt ooien, maar dit is niet vereist.

U moet Visual Studio code hebben met de uitbrei ding Resource Manager tools en een Azure PowerShell of Azure CLI. Zie voor meer informatie [sjabloon hulpprogramma's](template-tutorial-create-first-template.md#get-tools).

## <a name="review-template"></a>Sjabloon controleren

Aan het einde van de vorige zelf studie had uw sjabloon de volgende JSON:

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-storage/azuredeploy.json)]

Mogelijk hebt u gemerkt dat er een probleem is met deze sjabloon. De naam van het opslag account is vastgelegd in een vaste code. U kunt deze sjabloon alleen gebruiken om elk keer hetzelfde opslag account te implementeren. Als u een opslag account met een andere naam wilt implementeren, moet u een nieuwe sjabloon maken. Dit is uiteraard geen praktische manier om uw implementaties te automatiseren.

## <a name="make-template-reusable"></a>Sjabloon herbruikbaar maken

Als u uw sjabloon opnieuw wilt gebruiken, gaan we een para meter toevoegen waarmee u de naam van een opslag account kunt door geven. De gemarkeerde JSON in het volgende voor beeld laat zien wat er in uw sjabloon is gewijzigd. De para meter voor de **opslag** -id wordt geïdentificeerd als een teken reeks. De maximale lengte is ingesteld op 24 tekens om te voor komen dat namen te lang zijn.

Kopieer het hele bestand en vervang de sjabloon door de inhoud ervan.

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-name/azuredeploy.json?range=1-26&highlight=4-10,15)]

## <a name="deploy-template"></a>Sjabloon implementeren

We gaan de sjabloon implementeren. In het volgende voor beeld wordt de sjabloon geïmplementeerd met Azure CLI of Power shell. U ziet dat u de naam van het opslag account opgeeft als een van de waarden in de implementatie opdracht. Geef voor de naam van het opslag account dezelfde naam op die u in de vorige zelf studie hebt gebruikt.

Als u de resource groep nog niet hebt gemaakt, raadpleegt u [resource groep maken](template-tutorial-create-first-template.md#create-resource-group). In het voor beeld wordt ervan uitgegaan dat u de **templateFile** -variabele hebt ingesteld op het pad naar het sjabloon bestand, zoals wordt weer gegeven in de [eerste zelf studie](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addnameparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{your-unique-name}"
```

# <a name="azure-clitabazure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli
az group deployment create \
  --name addnameparameter \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageName={your-unique-name}
```

---

## <a name="understand-resource-updates"></a>Resource-updates begrijpen

In de vorige sectie hebt u een opslag account geïmplementeerd met dezelfde naam die u eerder hebt gemaakt. U vraagt zich misschien af hoe de resource wordt beïnvloed door de herimplementatie.

Als de resource al bestaat en er geen wijziging wordt gedetecteerd in de eigenschappen, wordt er geen actie ondernomen. Als de resource al bestaat en een eigenschap is gewijzigd, wordt de resource bijgewerkt. Als de resource niet bestaat, wordt deze gemaakt.

Deze manier van het afhandelen van updates betekent dat uw sjabloon alle resources kan bevatten die u nodig hebt voor een Azure-oplossing. U kunt de sjabloon veilig opnieuw implementeren en weet dat de resources zijn gewijzigd of alleen worden gemaakt wanneer dat nodig is. Als u bijvoorbeeld bestanden aan uw opslag account hebt toegevoegd, kunt u het opslag account opnieuw implementeren zonder dat deze bestanden verloren gaan.

## <a name="customize-by-environment"></a>Aanpassen per omgeving

Met parameters kunt u de implementatie aanpassen door waarden op te geven die voor een specifieke omgeving zijn aangepast. U kunt bijvoorbeeld verschillende waarden door geven op basis van het feit of u implementeert in een omgeving voor ontwikkeling, testen en productie.

In de vorige sjabloon is altijd een Standard_LRS Storage-account geïmplementeerd. Mogelijk wilt u de flexibiliteit om verschillende Sku's te implementeren, afhankelijk van de omgeving. In het volgende voor beeld ziet u de wijzigingen voor het toevoegen van een para meter voor SKU. Kopieer het hele bestand en plak over uw sjabloon.

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-sku/azuredeploy.json?range=1-40&highlight=10-23,32)]

De **storageSKU** -para meter heeft een standaard waarde. Deze waarde wordt gebruikt wanneer tijdens de implementatie geen waarde is opgegeven. Het bevat ook een lijst met toegestane waarden. Deze waarden komen overeen met de waarden die nodig zijn voor het maken van een opslag account. U wilt niet dat gebruikers van uw sjabloon door geven in Sku's die niet werken.

## <a name="redeploy-template"></a>Sjabloon opnieuw implementeren

U bent klaar om te implementeren. Omdat de standaard-SKU is ingesteld op **Standard_LRS**, hoeft u geen waarde voor die para meter op te geven.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addskuparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{your-unique-name}"
```

# <a name="azure-clitabazure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli
az group deployment create \
  --name addskuparameter \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageName={your-unique-name}
```

---

Voor een overzicht van de flexibiliteit van uw sjabloon, gaan we opnieuw implementeren. In deze tijd stelt u de SKU-para meter in op **Standard_GRS**. U kunt een nieuwe naam door geven om een ander opslag account te maken of dezelfde naam gebruiken om uw bestaande opslag account bij te werken. Beide opties werken.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name usedefaultsku `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{your-unique-name}" `
  -storageSKU Standard_GRS
```

# <a name="azure-clitabazure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli
az group deployment create \
  --name usedefaultsku \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageSKU=Standard_GRS storageName={your-unique-name}
```

---

Ten slotte gaan we nog een test uitvoeren en zien wat er gebeurt wanneer u een SKU doorgeeft die geen van de toegestane waarden is. In dit geval testen we het scenario waarbij een gebruiker van uw sjabloon een van de Sku's als **basis** beschouwt.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name testskuparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{your-unique-name}" `
  -storageSKU basic
```

# <a name="azure-clitabazure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli
az group deployment create \
  --name testskuparameter \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageSKU=basic storageName={your-unique-name}
```

---

De opdracht mislukt onmiddellijk met een fout bericht dat aangeeft welke waarden zijn toegestaan. Resource Manager identificeert de fout voordat de implementatie wordt gestart.

## <a name="clean-up-resources"></a>Resources opschonen

Als u verdergaat met de volgende zelf studie, hoeft u de resource groep niet te verwijderen.

Als u nu stopt, wilt u misschien de resources opschonen die u hebt geïmplementeerd door de resource groep te verwijderen.

1. Selecteer **Resourcegroep** in het linkermenu van Azure Portal.
2. Voer de naam van de resourcegroep in het veld **Filter by name** in.
3. Selecteer de naam van de resourcegroep.
4. Selecteer **Resourcegroep verwijderen** in het bovenste menu.

## <a name="next-steps"></a>Volgende stappen

U hebt de sjabloon die in de [eerste zelf studie](template-tutorial-create-first-template.md) is gemaakt, verbeterd door para meters toe te voegen. In de volgende zelf studie leert u over sjabloon functies.

> [!div class="nextstepaction"]
> [Sjabloon functies toevoegen](template-tutorial-add-functions.md)
