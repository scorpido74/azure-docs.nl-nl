---
title: Zelfstudie - tags toevoegen aan bronnen in sjabloon
description: Voeg tags toe aan resources die u implementeert in uw Azure Resource Manager-sjabloon. Met tags u op logische wijze resources ordenen.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 748a32d0ea8bfb0f23a99ce99d0aaf051118bc19
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80369884"
---
# <a name="tutorial-add-tags-in-your-arm-template"></a>Zelfstudie: tags toevoegen aan de ARM-sjabloon

In deze zelfstudie leert u hoe u tags toevoegt aan resources in uw Azure Resource Manager-sjabloon (ARM). [Tags](../management/tag-resources.md) helpen u uw resources logisch te organiseren. De tagwaarden worden weergegeven in kostenrapporten. Deze tutorial duurt **8 minuten** om te voltooien.

## <a name="prerequisites"></a>Vereisten

We raden u aan de [zelfstudie over Quickstart-sjablonen](template-tutorial-quickstart-template.md)in te vullen, maar dit is niet vereist.

U moet beschikken over Visual Studio Code met de extensie Hulpmiddelen voor ResourceBeheer en Azure PowerShell of Azure CLI. Zie [sjabloongereedschappen voor](template-tutorial-create-first-template.md#get-tools)meer informatie .

## <a name="review-template"></a>Sjabloon bekijken

Uw vorige sjabloon heeft een opslagaccount, een App Service-abonnement en een web-app geïmplementeerd.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/quickstart-template/azuredeploy.json":::

Nadat u deze resources hebt geïmplementeerd, moet u mogelijk kosten bijhouden en resources vinden die tot een categorie behoren. U tags toevoegen om deze problemen op te lossen.

## <a name="add-tags"></a>Tags toevoegen

U tagt resources om waarden toe te voegen die u helpen het gebruik ervan te identificeren. U bijvoorbeeld tags toevoegen die de omgeving en het project weergeven. U tags toevoegen die een kostenplaats of het team identificeren dat eigenaar is van de bron. Voeg waarden toe die zinvol zijn voor uw organisatie.

In het volgende voorbeeld worden de wijzigingen in de sjabloon belicht. Kopieer het hele bestand en vervang de sjabloon door de inhoud ervan.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.json" range="1-118" highlight="46-52,64,78,102":::

## <a name="deploy-template"></a>Sjabloon implementeren

Het is tijd om de sjabloon te implementeren en de resultaten te bekijken.

Zie [Resourcegroep maken](template-tutorial-create-first-template.md#create-resource-group)als u de resourcegroep niet hebt gemaakt. In het voorbeeld wordt ervan uitgegaan dat u de **variabele templateFile** hebt ingesteld op het pad naar het sjabloonbestand, zoals wordt weergegeven in de [eerste zelfstudie.](template-tutorial-create-first-template.md#deploy-template)

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addtags `
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
  --name addtags \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS webAppName=demoapp
```

---

## <a name="verify-deployment"></a>Implementatie verifiëren

U de implementatie verifiëren door de brongroep te verkennen vanuit de Azure-portal.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Selecteer **Resourcegroepen**in het linkermenu .
1. Selecteer de resourcegroep die u hebt geïmplementeerd.
1. Selecteer een van de bronnen, zoals de bron van het opslagaccount. Je ziet dat het nu tags heeft.

   ![Tags weergeven](./media/template-tutorial-add-tags/show-tags.png)

## <a name="clean-up-resources"></a>Resources opschonen

Als u doorgaat naar de volgende zelfstudie, hoeft u de brongroep niet te verwijderen.

Als u nu stopt, u de resources die u hebt geïmplementeerd, opschonen door de resourcegroep te verwijderen.

1. Selecteer **resourcegroep** in de linkermenu in de Azure-portal.
2. Voer de naam van de resourcegroep in het veld **Filter by name** in.
3. Selecteer de naam van de resourcegroep.
4. Selecteer **Brongroep verwijderen** in het bovenste menu.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u tags aan de bronnen toegevoegd. In de volgende zelfstudie leert u hoe u parameterbestanden gebruiken om het doorgeven van waarden aan de sjabloon te vereenvoudigen.

> [!div class="nextstepaction"]
> [Parameterbestand gebruiken](template-tutorial-use-parameter-file.md)
