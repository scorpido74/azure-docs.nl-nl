---
title: Zelf studie-Quick Start-sjablonen gebruiken
description: Meer informatie over het gebruik van Azure Quick Start-sjablonen voor het volt ooien van uw sjabloon ontwikkeling.
author: mumian
ms.date: 10/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 0feb0a1a682328f1e23a7d800eb4f5653a6acdd1
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2020
ms.locfileid: "76765426"
---
# <a name="tutorial-use-azure-quickstart-templates"></a>Zelf studie: Azure Quick Start-sjablonen gebruiken

[Sjablonen voor Azure Quick](https://azure.microsoft.com/resources/templates/) start is een opslag plaats van community-sjablonen. U kunt de voorbeeld sjablonen gebruiken in de ontwikkeling van uw sjabloon. In deze zelf studie vindt u een bron definitie van een website en voegt u deze toe aan uw eigen sjabloon. Het duurt ongeveer **12 minuten** om te volt ooien.

## <a name="prerequisites"></a>Vereisten

U wordt aangeraden de [zelf studie over geëxporteerde sjablonen](template-tutorial-export-template.md)te volt ooien, maar dit is niet vereist.

U moet Visual Studio code hebben met de uitbrei ding Resource Manager tools en een Azure PowerShell of Azure CLI. Zie voor meer informatie [sjabloon hulpprogramma's](template-tutorial-create-first-template.md#get-tools).

## <a name="review-template"></a>Sjabloon controleren

Aan het einde van de vorige zelf studie had uw sjabloon de volgende JSON:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/export-template/azuredeploy.json":::

Deze sjabloon kan worden gebruikt voor het implementeren van opslag accounts en app service-plannen, maar u wilt mogelijk een website toevoegen. U kunt vooraf gemaakte sjablonen gebruiken om snel de JSON te detecteren die vereist is voor het implementeren van een resource.

## <a name="find-template"></a>Sjabloon zoeken

1. Open [Azure Quick](https://azure.microsoft.com/resources/templates/) start-sjablonen
1. Voer in de **Zoek opdracht** **Linux-web-app implementeren**in.
1. Selecteer het abonnement met de titel **een basis-Linux-web-app implementeren**. Als u deze niet kunt vinden, volgt u de [direct-koppeling](https://azure.microsoft.com/resources/templates/101-webapp-basic-linux/).
1. Selecteer **Bladeren op github**.
1. Selecteer **azuredeploy. json**.
1. Controleer de sjabloon. Zoek in het bijzonder naar de `Microsoft.Web/sites`-resource.

    ![Snelstartgids voor de Resource Manager-sjabloon](./media/template-tutorial-quickstart-template/resource-manager-template-quickstart-template-web-site.png)

## <a name="revise-existing-template"></a>Bestaande sjabloon herzien

De Quick Start-sjabloon samen voegen met de bestaande sjabloon:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/quickstart-template/azuredeploy.json" range="1-108" highlight="32-45,49,85-100":::

De naam van de web-app moet uniek zijn binnen Azure. Om te voor komen dat dubbele namen worden opgegeven, is de variabele **webAppPortalName** bijgewerkt van **' webAppPortalName ': ' [concat (para meters (' webapp naam '), '-webapp ')] '** naar **' webAppPortalName ': ' [concat (para meters (' webapp naam '), Unique string (resourceGroup (). id)] '** .

Voeg aan het einde van de `Microsoft.Web/serverfarms` definitie een komma toe om de resource definitie te scheiden van de `Microsoft.Web/sites` definitie.

Er zijn een aantal belang rijke functies om in deze nieuwe resource te noteren.

U ziet dat er een element met de naam **dependsOn** is dat is ingesteld op het app service-plan. Deze instelling is vereist omdat het app service-plan moet bestaan voordat de web-app wordt gemaakt. Het element **dependsOn** vertelt Resource Manager hoe de resources voor implementatie moeten worden geordend.

De eigenschap **serverFarmId** maakt gebruik van de functie [resourceId](template-functions-resource.md#resourceid) . Deze functie haalt de unieke id voor een resource op. In dit geval wordt de unieke id voor het app service-plan opgehaald. De web-app is gekoppeld aan één specifiek app service-plan.

## <a name="deploy-template"></a>Sjabloon implementeren

Gebruik Azure CLI of Azure PowerShell voor het implementeren van een sjabloon.

Als u de resource groep nog niet hebt gemaakt, raadpleegt u [resource groep maken](template-tutorial-create-first-template.md#create-resource-group). In het voor beeld wordt ervan uitgegaan dat u de **templateFile** -variabele hebt ingesteld op het pad naar het sjabloon bestand, zoals wordt weer gegeven in de [eerste zelf studie](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addwebapp `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS `
  -webAppName demoapp
```

# <a name="azure-clitabazure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli
az group deployment create \
  --name addwebapp \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS webAppName=demoapp
```

---

## <a name="clean-up-resources"></a>Resources opschonen

Als u verdergaat met de volgende zelf studie, hoeft u de resource groep niet te verwijderen.

Als u nu stopt, wilt u misschien de resources opschonen die u hebt geïmplementeerd door de resource groep te verwijderen.

1. Selecteer **Resourcegroep** in het linkermenu van Azure Portal.
2. Voer de naam van de resourcegroep in het veld **Filter by name** in.
3. Selecteer de naam van de resourcegroep.
4. Selecteer **Resourcegroep verwijderen** in het bovenste menu.

## <a name="next-steps"></a>Volgende stappen

U hebt geleerd hoe u een Quick Start-sjabloon kunt gebruiken voor de ontwikkeling van uw sjabloon. In de volgende zelf studie voegt u tags toe aan de resources.

> [!div class="nextstepaction"]
> [Tags toevoegen](template-tutorial-add-tags.md)
