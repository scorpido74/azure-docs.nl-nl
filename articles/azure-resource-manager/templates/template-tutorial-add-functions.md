---
title: Zelf studie-sjabloon functies toevoegen
description: Voeg sjabloon functies toe aan uw Azure Resource Manager sjabloon om waarden te maken.
author: mumian
ms.date: 10/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: b32b0377502697e32e56e5c8324ca287c9a3cf69
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75473050"
---
# <a name="tutorial-add-template-functions-to-your-resource-manager-template"></a>Zelf studie: sjabloon functies toevoegen aan uw Resource Manager-sjabloon

In deze zelf studie leert u hoe u [sjabloon functies](template-functions.md) kunt toevoegen aan uw sjabloon. U gebruikt functies om waarden dynamisch samen te stellen. Naast deze door het systeem opgegeven sjabloon functies kunt u ook door de [gebruiker gedefinieerde functies](./template-user-defined-functions.md)maken. Het volt ooien van deze zelf studie duurt **zeven minuten** .

## <a name="prerequisites"></a>Vereisten

U wordt aangeraden de [zelf studie over para meters](template-tutorial-add-parameters.md)te volt ooien, maar dit is niet vereist.

U moet Visual Studio code hebben met de uitbrei ding Resource Manager tools en een Azure PowerShell of Azure CLI. Zie voor meer informatie [sjabloon hulpprogramma's](template-tutorial-create-first-template.md#get-tools).

## <a name="review-template"></a>Sjabloon controleren

Aan het einde van de vorige zelf studie had uw sjabloon de volgende JSON:

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-sku/azuredeploy.json)]

De locatie van het opslag account wordt vastgelegd in een VS- **Oost**. Het is echter mogelijk dat u het opslag account moet implementeren in andere regio's. Er wordt een probleem opgetreden bij het oplossen van uw sjabloon. U kunt een para meter voor de locatie toevoegen, maar dit zou geweldig zijn als de standaard waarde meer zinniger is dan alleen een hardcoded waarde.

## <a name="use-function"></a>Functie gebruiken

Als u de vorige zelf studie in deze serie hebt voltooid, hebt u al een functie gebruikt. Wanneer u **[para meters (' opslag naam ')] '** hebt toegevoegd, hebt u de functie [para meters](template-functions-deployment.md#parameters) gebruikt. De vier Kante haken geven aan dat de syntaxis tussen de haken een [sjabloon expressie](template-expressions.md)is. Resource Manager lost de syntaxis op in plaats van deze als een letterlijke waarde te behandelen.

Met functies kunt u flexibiliteit toevoegen aan uw sjabloon door dynamische waarden te verkrijgen tijdens de implementatie. In deze zelf studie gebruikt u een functie om de locatie op te halen van de resource groep die u voor de implementatie gebruikt.

In het volgende voor beeld worden de wijzigingen gemarkeerd voor het toevoegen van een para meter met de naam **Location**.  Met de standaard waarde voor de para meter wordt de functie [resourceGroup](template-functions-resource.md#resourcegroup) aangeroepen. Deze functie retourneert een-object met informatie over de resource groep die wordt gebruikt voor de implementatie. Een van de eigenschappen van het object is een locatie-eigenschap. Wanneer u de standaard waarde gebruikt, heeft de locatie van het opslag account dezelfde locatie als de resource groep. De resources in een resource groep hoeven niet dezelfde locatie te delen. U kunt ook een andere locatie opgeven wanneer dat nodig is.

Kopieer het hele bestand en vervang de sjabloon door de inhoud ervan.

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-location/azuredeploy.json?range=1-44&highlight=24-27,34)]

## <a name="deploy-template"></a>Sjabloon implementeren

In de vorige zelf studies hebt u een opslag account gemaakt in VS-Oost, maar uw resource groep is gemaakt in de centrale vs. Voor deze zelf studie wordt uw opslag account in dezelfde regio gemaakt als de resource groep. Gebruik de standaard waarde voor locatie, zodat u deze parameter waarde niet hoeft op te geven. U moet een nieuwe naam opgeven voor het opslag account, omdat u een opslag account maakt op een andere locatie. Gebruik bijvoorbeeld **store2** als voor voegsel in plaats van **store1**.

Als u de resource groep nog niet hebt gemaakt, raadpleegt u [resource groep maken](template-tutorial-create-first-template.md#create-resource-group). In het voor beeld wordt ervan uitgegaan dat u de **templateFile** -variabele hebt ingesteld op het pad naar het sjabloon bestand, zoals wordt weer gegeven in de [eerste zelf studie](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addlocationparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{new-unique-name}"
```

# <a name="azure-clitabazure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli
az group deployment create \
  --name addlocationparameter \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageName={new-unique-name}
```

---

## <a name="verify-deployment"></a>Implementatie verifiëren

U kunt de implementatie controleren door de resource groep te verkennen van de Azure Portal.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com).
1. Selecteer **resource groepen**in het menu links.
1. Selecteer de resource groep die u hebt geïmplementeerd.
1. U ziet dat een opslag account resource is geïmplementeerd en dezelfde locatie heeft als de resource groep.

## <a name="clean-up-resources"></a>Resources opschonen

Als u verdergaat met de volgende zelf studie, hoeft u de resource groep niet te verwijderen.

Als u nu stopt, wilt u misschien de resources opschonen die u hebt geïmplementeerd door de resource groep te verwijderen.

1. Selecteer **Resourcegroep** in het linkermenu van Azure Portal.
2. Voer de naam van de resourcegroep in het veld **Filter by name** in.
3. Selecteer de naam van de resourcegroep.
4. Selecteer **Resourcegroep verwijderen** in het bovenste menu.

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u een functie gebruikt bij het definiëren van de standaard waarde voor een para meter. In deze zelfstudie reeks gaat u functies blijven gebruiken. Aan het einde van de reeks voegt u functies toe aan elke sectie van de sjabloon.

> [!div class="nextstepaction"]
> [Variabelen toevoegen](template-tutorial-add-variables.md)
