---
title: Zelfstudie - parameterbestand gebruiken om sjabloon te implementeren
description: Gebruik parameterbestanden die de waarden bevatten die moeten worden gebruikt voor het implementeren van uw Azure Resource Manager-sjabloon.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: b91041b96a3819dbace3898d92226f0351f0f973
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80411511"
---
# <a name="tutorial-use-parameter-files-to-deploy-your-arm-template"></a>Zelfstudie: Parameterbestanden gebruiken om uw ARM-sjabloon te implementeren

In deze zelfstudie leert u hoe u [parameterbestanden](parameter-files.md) gebruikt om de waarden op te slaan die u tijdens de implementatie doorgeeft. In de vorige zelfstudies hebt u inlineparameters gebruikt met de opdracht implementatie. Deze aanpak werkte voor het testen van uw Azure Resource Manager (ARM)-sjabloon, maar bij het automatiseren van implementaties kan het eenvoudiger zijn om een set waarden voor uw omgeving door te geven. Parameterbestanden maken het eenvoudiger om parameterwaarden voor een specifieke omgeving te verpakken. In deze zelfstudie maakt u parameterbestanden voor ontwikkel- en productieomgevingen. Het duurt ongeveer **12 minuten** om te voltooien.

## <a name="prerequisites"></a>Vereisten

We raden u aan de [zelfstudie over tags in](template-tutorial-add-tags.md)te vullen, maar dit is niet vereist.

U moet beschikken over Visual Studio Code met de extensie Hulpmiddelen voor ResourceBeheer en Azure PowerShell of Azure CLI. Zie [sjabloongereedschappen voor](template-tutorial-create-first-template.md#get-tools)meer informatie .

## <a name="review-template"></a>Sjabloon bekijken

Uw sjabloon heeft veel parameters die u tijdens de implementatie opgeven. Aan het einde van de vorige zelfstudie zag uw sjabloon eruit als volgt:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.json":::

Deze sjabloon werkt goed, maar nu wilt u eenvoudig de parameters beheren die u voor de sjabloon doorgeeft.

## <a name="add-parameter-files"></a>Parameterbestanden toevoegen

Parameterbestanden zijn JSON-bestanden met een structuur die vergelijkbaar is met uw sjabloon. In het bestand geeft u de parameterwaarden op die u tijdens de implementatie wilt doorgeven.

Maak in VS-code een nieuw bestand met de volgende inhoud. Sla het bestand op met de naam **azuredeploy.parameters.dev.json**.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.parameters.dev.json":::

Dit bestand is het parameterbestand voor de ontwikkelomgeving. Merk op dat het Standard_LRS gebruikt voor het opslagaccount, resources met een v-voorvoegsel noemt en de **omgevingstag** instelt op **Dev**. **dev**

Maak opnieuw een nieuw bestand met de volgende inhoud. Sla het bestand op met de naam **azuredeploy.parameters.prod.json**.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.parameters.prod.json":::

Dit bestand is het parameterbestand voor de productieomgeving. Merk op dat het Standard_GRS gebruikt voor het opslagaccount, resources met een **contoso-voorvoegsel** noemt en de **milieutag** instelt op **Productie**. In een echte productieomgeving wilt u ook een app-service met een SKU gebruiken, anders dan gratis, maar we blijven die SKU gebruiken voor deze zelfstudie.

## <a name="deploy-template"></a>Sjabloon implementeren

Gebruik Azure CLI of Azure PowerShell om de sjabloon te implementeren.

Als laatste test van uw sjabloon maken we twee nieuwe resourcegroepen. Een voor de dev milieu en een voor de productie-omgeving.

Eerst zetten we in op de dev-omgeving.

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

Als u deze implementatieopdracht wilt uitvoeren, moet u over de [nieuwste versie](/cli/azure/install-azure-cli) van Azure CLI beschikken.

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

Nu zetten we in op de productieomgeving.

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
> Als de implementatie is mislukt, gebruikt u de **foutopsporingsschakelaar** met de opdracht implementatie om de foutopsporingslogboeken weer te geven.  U ook de **verbose-schakelaar** gebruiken om de volledige foutopsporingslogboeken weer te geven.

## <a name="verify-deployment"></a>Implementatie verifiëren

U de implementatie verifiëren door de brongroepen te verkennen vanuit de Azure-portal.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Selecteer **Resourcegroepen**in het linkermenu .
1. U ziet de twee nieuwe resourcegroepen die u hebt geïmplementeerd in deze zelfstudie.
1. Selecteer een van beide resourcegroepen en bekijk de geïmplementeerde resources. Ze komen overeen met de waarden die u hebt opgegeven in het parameterbestand voor die omgeving.

## <a name="clean-up-resources"></a>Resources opschonen

1. Selecteer **resourcegroep** in de linkermenu in de Azure-portal.
2. Voer de naam van de resourcegroep in het veld **Filter by name** in. Als u deze reeks hebt voltooid, u drie brongroepen verwijderen: myResourceGroup, myResourceGroupDev en myResourceGroupProd.
3. Selecteer de naam van de resourcegroep.
4. Selecteer **Brongroep verwijderen** in het bovenste menu.

## <a name="next-steps"></a>Volgende stappen

Gefeliciteerd, u bent klaar met deze inleiding tot het implementeren van sjablonen in Azure. Laat het ons weten als je opmerkingen en suggesties hebt in de feedbacksectie. Bedankt!

De volgende zelfstudiereeks gaat verder in meer details over het implementeren van sjablonen.

> [!div class="nextstepaction"]
> [Een lokale sjabloon implementeren](./deployment-tutorial-local-template.md)
