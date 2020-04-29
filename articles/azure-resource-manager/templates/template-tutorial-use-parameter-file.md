---
title: Zelf studie-parameter bestand voor de implementatie van de sjabloon gebruiken
description: Gebruik parameter bestanden die de waarden bevatten die moeten worden gebruikt voor het implementeren van uw Azure Resource Manager-sjabloon.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: b91041b96a3819dbace3898d92226f0351f0f973
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80411511"
---
# <a name="tutorial-use-parameter-files-to-deploy-your-arm-template"></a>Zelf studie: parameter bestanden gebruiken voor het implementeren van uw ARM-sjabloon

In deze zelf studie leert u hoe u [parameter bestanden](parameter-files.md) kunt gebruiken voor het opslaan van de waarden die tijdens de implementatie worden door gegeven. In de vorige zelf studies hebt u inline-para meters gebruikt met de implementatie opdracht. Deze aanpak werkt voor het testen van uw Azure Resource Manager-sjabloon (ARM), maar bij het automatiseren van implementaties kan het gemakkelijker worden om een set waarden voor uw omgeving door te geven. Met parameter bestanden kunt u gemakkelijker parameter waarden voor een specifieke omgeving inpakken. In deze zelf studie maakt u parameter bestanden voor ontwikkel-en productie omgevingen. Het duurt ongeveer **12 minuten** om te volt ooien.

## <a name="prerequisites"></a>Vereisten

U wordt aangeraden de [zelf studie over Tags](template-tutorial-add-tags.md)te volt ooien, maar dit is niet vereist.

U moet Visual Studio code hebben met de uitbrei ding Resource Manager tools en een Azure PowerShell of Azure CLI. Zie voor meer informatie [sjabloon hulpprogramma's](template-tutorial-create-first-template.md#get-tools).

## <a name="review-template"></a>Sjabloon controleren

De sjabloon bevat een groot aantal para meters die u tijdens de implementatie kunt opgeven. Aan het einde van de vorige zelf studie is uw sjabloon als volgt bekeken:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.json":::

Deze sjabloon werkt goed, maar nu wilt u de para meters die u doorgeeft, eenvoudig beheren voor de sjabloon.

## <a name="add-parameter-files"></a>Parameter bestanden toevoegen

Parameter bestanden zijn JSON-bestanden met een structuur die vergelijkbaar is met uw sjabloon. In het bestand geeft u de parameter waarden op die u tijdens de implementatie wilt door geven.

Maak in VS code een nieuw bestand met de volgende inhoud. Sla het bestand op met de naam **azuredeploy. para meters. dev. json**.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.parameters.dev.json":::

Dit bestand is het parameter bestand voor de ontwikkel omgeving. U ziet dat deze Standard_LRS gebruikt voor het opslag account, namen van resources met een **dev** -voor voegsel en het **omgevings** label instelt op **dev**.

Maak opnieuw een nieuw bestand met de volgende inhoud. Sla het bestand op met de naam **azuredeploy. para meters. Prod. json**.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.parameters.prod.json":::

Dit bestand is het parameter bestand voor de productie omgeving. U ziet dat deze Standard_GRS gebruikt voor het opslag account, resources benoemt met een voor voegsel van **Contoso** en de **omgevings** label instelt op **productie**. In een echte productie omgeving zou u ook een app service willen gebruiken met een andere SKU dan gratis, maar we blijven die SKU gebruiken voor deze zelf studie.

## <a name="deploy-template"></a>Sjabloon implementeren

Gebruik Azure CLI of Azure PowerShell om de sjabloon te implementeren.

Als laatste test van uw sjabloon gaan we twee nieuwe resource groepen maken. Een voor de ontwikkel omgeving en één voor de productie omgeving.

Eerst gaan we implementeren in de ontwikkel omgeving.

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

Als u deze implementatie opdracht wilt uitvoeren, moet u de [nieuwste versie](/cli/azure/install-azure-cli) van Azure cli hebben.

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

Nu gaan we implementeren naar de productie omgeving.

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
> Als de implementatie is mislukt, gebruikt u de switch **debug** met de implementatie opdracht om de logboeken voor fout opsporing weer te geven.  U kunt ook de **uitgebreide** switch gebruiken om de volledige logboeken voor fout opsporing weer te geven.

## <a name="verify-deployment"></a>Implementatie verifiëren

U kunt de implementatie controleren door de resource groepen van de Azure Portal te verkennen.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer **resource groepen**in het menu links.
1. U ziet de twee nieuwe resource groepen die u in deze zelf studie hebt geïmplementeerd.
1. Selecteer een resource groep en Bekijk de geïmplementeerde resources. U ziet dat ze overeenkomen met de waarden die u hebt opgegeven in het parameter bestand voor die omgeving.

## <a name="clean-up-resources"></a>Resources opschonen

1. Selecteer in de Azure Portal **resource groep** in het menu links.
2. Voer de naam van de resourcegroep in het veld **Filter by name** in. Als u deze serie hebt voltooid, hebt u drie resource groepen om myResourceGroup, myResourceGroupDev en myResourceGroupProd te verwijderen.
3. Selecteer de naam van de resourcegroep.
4. Selecteer **resource groep verwijderen** in het bovenste menu.

## <a name="next-steps"></a>Volgende stappen

Gefeliciteerd, u hebt deze inleiding voltooid voor het implementeren van sjablonen in Azure. Laat het ons weten als u opmerkingen en suggesties hebt in het gedeelte feedback. Bedankt!

De volgende reeks zelf studies gaat meer details over het implementeren van sjablonen.

> [!div class="nextstepaction"]
> [Een lokale sjabloon implementeren](./deployment-tutorial-local-template.md)
