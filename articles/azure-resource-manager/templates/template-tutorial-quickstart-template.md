---
title: Zelfstudie - Quickstart-sjablonen gebruiken
description: Meer informatie over het gebruik van Azure Quickstart-sjablonen om de sjabloonontwikkeling te voltooien.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 4b82e02ecc009e587b89d1fd151fd13f75a4bcf8
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80408513"
---
# <a name="tutorial-use-azure-quickstart-templates"></a>Zelfstudie: Azure Quickstart-sjablonen gebruiken

[Azure Quickstart-sjablonen](https://azure.microsoft.com/resources/templates/) zijn een opslagplaats van door de community bijgedragen sjablonen. U de voorbeeldsjablonen gebruiken in de ontwikkeling van uw sjabloon. In deze zelfstudie vindt u een definitie van websitebronnen en voegt u deze toe aan uw eigen sjabloon. Het duurt ongeveer **12 minuten** om te voltooien.

## <a name="prerequisites"></a>Vereisten

We raden u aan de [zelfstudie over geëxporteerde sjablonen in te](template-tutorial-export-template.md)vullen, maar dit is niet vereist.

U moet beschikken over Visual Studio Code met de extensie Hulpmiddelen voor ResourceBeheer en Azure PowerShell of Azure CLI. Zie [sjabloongereedschappen voor](template-tutorial-create-first-template.md#get-tools)meer informatie .

## <a name="review-template"></a>Sjabloon bekijken

Aan het einde van de vorige zelfstudie had uw sjabloon de volgende JSON:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/export-template/azuredeploy.json":::

Deze sjabloon werkt voor het implementeren van opslagaccounts en app-serviceplannen, maar u er een website aan toevoegen. U vooraf gebouwde sjablonen gebruiken om snel de JSON te ontdekken die nodig is voor het implementeren van een resource.

## <a name="find-template"></a>Sjabloon zoeken

1. [Azure Quickstart-sjablonen openen](https://azure.microsoft.com/resources/templates/)
1. Voer in **Zoeken** **de Linux-web-app implementeren**in.
1. Selecteer de ene met de titel **Een eenvoudige Linux-web-app implementeren**. Als je moeite hebt met het vinden van het, hier is de [directe link](https://azure.microsoft.com/resources/templates/101-webapp-basic-linux/).
1. Selecteer **Bladeren op GitHub**.
1. Selecteer **azuredeploy.json**.
1. Bekijk de sjabloon. Zoek in het `Microsoft.Web/sites` bijzonder naar de bron.

    ![Resource Manager-sjabloon snelstart-website](./media/template-tutorial-quickstart-template/resource-manager-template-quickstart-template-web-site.png)

## <a name="revise-existing-template"></a>Bestaande sjabloon herzien

Voeg de snelstartsjabloon samen met de bestaande sjabloon:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/quickstart-template/azuredeploy.json" range="1-108" highlight="32-45,49,85-100":::

De naam van de web-app moet uniek zijn in Azure. Om dubbele namen te voorkomen, is de **webAppPortalName-variabele** bijgewerkt van **"webAppPortalName": "[concat(parameters('webAppName'), '-webapp')]"** naar **"webAppPortalName": "[concat(parameters('webAppName'), uniqueString(resourceGroup().id)]"**.

Voeg aan het einde van `Microsoft.Web/serverfarms` de definitie een komma `Microsoft.Web/sites` toe om de resourcedefinitie van de definitie te scheiden.

Er zijn een paar belangrijke functies op te merken in deze nieuwe bron.

U zult merken dat het een element met de naam **dependsOn** dat is ingesteld op de app-service plan. Deze instelling is vereist omdat het app-serviceplan moet bestaan voordat de web-app wordt gemaakt. Het element **dependsOn** vertelt Resource Manager hoe u de resources voor implementatie moet ordenen.

De eigenschap **serverFarmId** maakt gebruik van de [functie resourceId.](template-functions-resource.md#resourceid) Met deze functie krijgt u de unieke id voor een resource. In dit geval krijgt het de unieke id voor het app-serviceplan. De web-app is gekoppeld aan één specifiek app-serviceplan.

## <a name="deploy-template"></a>Sjabloon implementeren

Gebruik Azure CLI of Azure PowerShell om een sjabloon te implementeren.

Zie [Resourcegroep maken](template-tutorial-create-first-template.md#create-resource-group)als u de resourcegroep niet hebt gemaakt. In het voorbeeld wordt ervan uitgegaan dat u de **variabele templateFile** hebt ingesteld op het pad naar het sjabloonbestand, zoals wordt weergegeven in de [eerste zelfstudie.](template-tutorial-create-first-template.md#deploy-template)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addwebapp `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS `
  -webAppName demoapp
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u deze implementatieopdracht wilt uitvoeren, moet u over de [nieuwste versie](/cli/azure/install-azure-cli) van Azure CLI beschikken.

```azurecli
az deployment group create \
  --name addwebapp \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS webAppName=demoapp
```

---

> [!NOTE]
> Als de implementatie is mislukt, gebruikt u de **foutopsporingsschakelaar** met de opdracht implementatie om de foutopsporingslogboeken weer te geven.  U ook de **verbose-schakelaar** gebruiken om de volledige foutopsporingslogboeken weer te geven.

## <a name="clean-up-resources"></a>Resources opschonen

Als u doorgaat naar de volgende zelfstudie, hoeft u de brongroep niet te verwijderen.

Als u nu stopt, u de resources die u hebt geïmplementeerd, opschonen door de resourcegroep te verwijderen.

1. Selecteer **resourcegroep** in de linkermenu in de Azure-portal.
2. Voer de naam van de resourcegroep in het veld **Filter by name** in.
3. Selecteer de naam van de resourcegroep.
4. Selecteer **Brongroep verwijderen** in het bovenste menu.

## <a name="next-steps"></a>Volgende stappen

U hebt geleerd hoe u een snelstartsjabloon gebruiken voor de ontwikkeling van uw sjabloon. In de volgende zelfstudie voegt u tags toe aan de resources.

> [!div class="nextstepaction"]
> [Tags toevoegen](template-tutorial-add-tags.md)
