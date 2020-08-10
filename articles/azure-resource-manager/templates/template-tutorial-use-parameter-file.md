---
title: 'Zelfstudie: een sjabloon implementeren met behulp van een parameterbestand'
description: Gebruik parameterbestanden die de waarden bevatten voor het implementeren van uw Azure Resource Manager-sjabloon.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: devx-track-azurecli
ms.openlocfilehash: bd7917a96550d45b14eb5a5b5cae1ac957aa78b5
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/31/2020
ms.locfileid: "87502797"
---
# <a name="tutorial-use-parameter-files-to-deploy-your-arm-template"></a>Zelfstudie: Parameterbestanden gebruiken voor het implementeren van uw ARM-sjabloon

In deze zelfstudie leert u hoe u [parameterbestanden](parameter-files.md) kunt gebruiken om de waarden op te slaan die tijdens de implementatie worden doorgegeven. In de vorige zelfstudies hebt u inline parameters gebruikt bij de implementatieopdracht. Deze aanpak werkt voor het testen van uw ARM-sjabloon (Azure Resource Manager), maar voor het automatiseren van implementaties is het makkelijker om een set waarden voor uw omgeving door te geven. Met parameterbestanden kunt u gemakkelijker parameterwaarden voor een specifieke omgeving inpakken. In deze zelfstudie maakt u parameterbestanden voor ontwikkel- en productieomgevingen. Dit neemt ongeveer **12 minuten** in beslag.

## <a name="prerequisites"></a>Vereisten

U wordt aangeraden om eerst de [zelfstudie over tags](template-tutorial-add-tags.md) te voltooien, maar dit is niet verplicht.

U moet beschikken over Visual Studio Code met de extensie Resource Manager Tools (hulpprogramma's voor resourcebeheer), plus Azure PowerShell of Azure CLI. Zie de [hulpprogramma's voor sjablonen](template-tutorial-create-first-template.md#get-tools) voor meer informatie.

## <a name="review-template"></a>Sjabloon controleren

De sjabloon bevat veel parameters die u tijdens de implementatie kunt opgeven. Aan het einde van de vorige zelfstudie zag uw sjabloon er als volgt uit:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.json":::

Deze sjabloon werkt goed, maar nu wilt u de parameters die u aan de sjabloon doorgeeft, eenvoudig kunnen beheren.

## <a name="add-parameter-files"></a>Parameterbestanden toevoegen

Parameterbestanden zijn JSON-bestanden met een structuur die vergelijkbaar is met die van uw sjabloon. In het bestand geeft u de parameterwaarden op die u tijdens de implementatie wilt doorgeven.

Maak in Visual Studio Code een nieuw bestand met de volgende inhoud. Sla het bestand op met de naam **azuredeploy.parameters.dev.json**.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.parameters.dev.json":::

Dit bestand is het parameterbestand voor de ontwikkelomgeving. U ziet dat Standard_LRS wordt gebruikt als opslagaccount, namen van resources worden voorzien van het voorvoegsel **dev** en de tag **Environment** (Omgeving) wordt ingesteld op **Dev** (Ontwikkeling).

Maak nogmaals een nieuw bestand, ditmaal met de volgende inhoud. Sla het bestand op met de naam **azuredeploy.parameters.prod.json**.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.parameters.prod.json":::

Dit bestand is het parameterbestand voor de productieomgeving. U ziet dat Standard_GRS wordt gebruikt als opslagaccount, namen van resources worden voorzien van het voorvoegsel **contoso** en de tag **Environment** (Omgeving) wordt ingesteld op **Production** (Productie). In een echte productieomgeving zou u ook een app-service willen gebruiken met een andere SKU dan een gratis SKU, maar in deze zelfstudie blijven we die SKU gebruiken.

## <a name="deploy-template"></a>Sjabloon implementeren

Gebruik Azure CLI of Azure PowerShell om de sjabloon te implementeren.

Als laatste test van uw sjabloon maken we twee nieuwe resourcegroepen. Eén voor de ontwikkelomgeving en één voor de productieomgeving.

Eerst implementeren de sjabloon in de ontwikkelomgeving.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$templateFile = "{path-to-the-template-file}"
$parameterFile="{path-to-azuredeploy.parameters.dev.json}"
New-AzResourceGroup `
  -Name myResourceGroupDev `
  -Location "East US"
New-AzResourceGroupDeployment `
  -Name devenvironment `
  -ResourceGroupName myResourceGroupDev `
  -TemplateFile $templateFile `
  -TemplateParameterFile $parameterFile
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u deze implementatieopdracht wilt uitvoeren, moet u beschikken over de [nieuwste versie](/cli/azure/install-azure-cli) van Azure CLI.

```azurecli
templateFile="{path-to-the-template-file}"
devParameterFile="{path-to-azuredeploy.parameters.dev.json}"
az group create \
  --name myResourceGroupDev \
  --location "East US"
az deployment group create \
  --name devenvironment \
  --resource-group myResourceGroupDev \
  --template-file $templateFile \
  --parameters $devParameterFile
```

---

Nu implementeren we de sjabloon in de productieomgeving.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$parameterFile="{path-to-azuredeploy.parameters.prod.json}"
New-AzResourceGroup `
  -Name myResourceGroupProd `
  -Location "West US"
New-AzResourceGroupDeployment `
  -Name prodenvironment `
  -ResourceGroupName myResourceGroupProd `
  -TemplateFile $templateFile `
  -TemplateParameterFile $parameterFile
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli
prodParameterFile="{path-to-azuredeploy.parameters.prod.json}"
az group create \
  --name myResourceGroupProd \
  --location "West US"
az deployment group create \
  --name prodenvironment \
  --resource-group myResourceGroupProd \
  --template-file $templateFile \
  --parameters $prodParameterFile
```

---

> [!NOTE]
> Als de implementatie is mislukt, gebruikt u de schakeloptie **debug** met de implementatieopdracht om de logboeken voor foutopsporing weer te geven.  U kunt ook de schakeloptie **verbose** gebruiken om de volledige logboeken voor foutopsporing weer te geven.

## <a name="verify-deployment"></a>Implementatie verifiëren

U kunt de implementatie controleren door de resourcegroepen te bekijken vanuit de Azure-portal.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer **Resourcegroepen** in het linkermenu.
1. U ziet de twee nieuwe resourcegroepen die u in deze zelfstudie hebt geïmplementeerd.
1. Selecteer een van beide resourcegroepen en bekijk de geïmplementeerde resources. U ziet dat ze overeenkomen met de waarden die u hebt opgegeven in het parameterbestand voor die omgeving.

## <a name="clean-up-resources"></a>Resources opschonen

1. Selecteer **Resourcegroep** in het linkermenu van Azure Portal.
2. Voer de naam van de resourcegroep in het veld **Filter by name** in. Als u deze serie hebt voltooid, hebt u drie resourcegroepen om te verwijderen: myResourceGroup, myResourceGroupDev en myResourceGroupProd.
3. Selecteer de naam van de resourcegroep.
4. Selecteer **Resourcegroep verwijderen** in het bovenste menu.

## <a name="next-steps"></a>Volgende stappen

Gefeliciteerd. U hebt deze inleiding tot het implementeren van sjablonen in Azure voltooid. Laat het ons weten als u opmerkingen en suggesties hebt in de feedbacksectie. Bedankt!

De volgende serie zelfstudies gaat dieper in op het implementeren van sjablonen.

> [!div class="nextstepaction"]
> [Een lokale sjabloon implementeren](./deployment-tutorial-local-template.md)
