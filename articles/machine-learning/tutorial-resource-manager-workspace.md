---
title: Zelfstudie - Een Azure ML-werkruimte maken - Resource Manager-sjabloon
description: In deze zelfstudie gebruikt u een Azure Resource Manager-sjabloon om snel een Azure-werkruimte te implementeren voor Machine Learning
services: machine-learning
author: lobrien
ms.author: laobri
ms.custom: subject-armqs
ms.date: 05/26/2020
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.openlocfilehash: 098d82e6521a4a355ac31809937b589f984816f2
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/07/2020
ms.locfileid: "86027151"
---
# <a name="tutorial-deploy-an-azure-machine-learning-workspace-using-an-arm-template"></a>Zelfstudie: Een werkruimte voor Azure Machine Learning implementeren met een ARM-sjabloon

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In deze zelfstudie wordt uitgelegd hoe u een werkruimte voor Azure Machine Learning maakt met een Azure Resource Manager-sjabloon (ARM-sjabloon). Met werkruimten voor Azure Machine Learning kunt u al uw machine learning assets organiseren, van basisgegevenssets tot geïmplementeerde modellen. Werkruimten zijn een enkele locatie waarin u samen met collega's experimenten maakt, uitvoert en beoordeelt, uw training en deductieberekeningsresources beheert en modellen controleert en implementeert.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Als uw omgeving voldoet aan de vereisten en u benkend bent met het gebruik van ARM-sjablonen, selecteert u de knop **Implementeren naar Azure**. De sjabloon wordt in Azure Portal geopend.

[![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-machine-learning-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/services/machine-learning/) aan voordat u begint.

* Als u de CLI-opdrachten in dit document wilt gebruiken vanuit uw **lokale omgeving**, hebt u de [Azure CLI nodig](/cli/azure/install-azure-cli).

## <a name="review-the-template"></a>De sjabloon controleren

De sjabloon die in deze quickstart wordt gebruikt, komt uit [Azure-quickstartsjablonen](https://azure.microsoft.com/resources/templates/101-machine-learning-create/).

:::code language="json" source="~/quickstart-templates/101-machine-learning-create/azuredeploy.json" range="1-258" highlight="224-254":::

De volgende resources zijn gedefinieerd in de sjabloon:

* [Microsoft.MachineLearningServices/workspaces](/azure/templates/microsoft.machinelearningservices/workspaces): Een Azure ML-werkruimte maken. In deze sjabloon zijn de locatie en naam parameters die de gebruiker kan doorgeven of interactief kan invoeren.

## <a name="deploy-the-template"></a>De sjabloon implementeren

Als u de sjabloon uit de Azure CLI wilt gebruiken, meldt u zich aan en kiest u uw abonnement (Zie [Aanmelden met Azure CLI](/cli/azure/authenticate-azure-cli)). Voer vervolgens

```azurecli-interactive
read -p "Enter a project name that is used for generating resource names:" projectName &&
read -p "Enter the location (i.e. centralus):" location &&
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-machine-learning-create/azuredeploy.json" &&
resourceGroupName="${projectName}rg" &&
workspaceName="${projectName}ws" &&
az group create --name $resourceGroupName --location "$location" &&
az deployment group create --resource-group $resourceGroupName --template-uri $templateUri --parameters workspaceName=$workspaceName location=$location &&
echo "Press [ENTER] to continue ..." &&
read
```

Wanneer u de bovenstaande opdracht uitvoert, voert u het volgende in:

1. Een projectnaam die de basis vormt van de namen van de gemaakte resourcegroep en Azure ML-werkruimte.
1. De Azure-locatie waarin u de implementatie wilt uitvoeren.

## <a name="review-deployed-resources"></a>Geïmplementeerde resources bekijken

Uw Azure ML-werkruimte bekijken:

1. Ga naar https://portal.azure.com.
1. Meld u aan.
1. Kies de werkruimte die u zojuist hebt gemaakt.

U ziet de startpagina van Azure Machine Learning:

:::image type="content" source="media/tutorial-resource-manager-workspace/workspace-home.png" alt-text="Schermopname van de Azure ML-werkruimte":::

Als u alle resources wilt zien die zijn gekoppeld aan de implementatie, klikt u op de koppeling in de linkerbovenhoek met de naam van de werkruimte (in de schermopname, `my_templated_ws`). Via deze koppeling gaat u naar de resourcegroep in Azure Portal. De naam van de resourcegroep is `{projectName}rg` en die van de werkruimte is `{projectName}ws`.

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze werkruimte niet wilt gebruiken, verwijdert u deze. Aangezien de werkruimte is gekoppeld aan andere resources, zoals een opslagaccount, wilt u waarschijnlijk de gehele resourcegroep die u hebt gemaakt verwijderen. U kunt de resourcegroep verwijderen via de portal door op de knop **Verwijderen** te klikken en te bevestigen. U kunt de resourcegroep ook uit de CLI verwijderen met:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een werkruimte voor Azure Machine Learning gemaakt met een ARM-sjabloon. Als u Azure Machine Learning wilt verkennen, gaat u door met de zelfstudie.

> [!div class="nextstepaction"]
> [Zelfstudie: Ga aan de slag met het maken van uw eerste ML-experiment met de Python SDK](tutorial-1st-experiment-sdk-setup.md)
