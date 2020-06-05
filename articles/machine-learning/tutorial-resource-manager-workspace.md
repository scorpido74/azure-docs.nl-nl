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
ms.openlocfilehash: b6eac711ed6c420330b036cb6094c841508cee9f
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/22/2020
ms.locfileid: "83790713"
---
# <a name="tutorial-deploy-an-azure-machine-learning-workspace-using-a-resource-manager-template"></a>Zelfstudie: Een werkruimte voor Azure Machine Learning implementeren met een Resource Manager-sjabloon
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In deze zelfstudie wordt uitgelegd hoe u een werkruimte voor Azure Machine Learning maakt met een Azure Resource Manager-sjabloon. Met werkruimten voor Azure Machine Learning kunt u al uw machine learning assets organiseren, van basisgegevenssets tot geïmplementeerde modellen. Werkruimten zijn een enkele locatie waarin u samen met collega's experimenten maakt, uitvoert en beoordeelt, uw training en deductieberekeningsresources beheert en modellen controleert en implementeert.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://aka.ms/AMLFree) voordat u begint.

* Als u de CLI-opdrachten in dit document wilt gebruiken vanuit uw **lokale omgeving**, hebt u de [Azure CLI nodig](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)

## <a name="create-a-workspace"></a>Een werkruimte maken

### <a name="review-the-template"></a>De sjabloon controleren

De sjabloon die in deze quickstart wordt gebruikt, komt uit [Azure Quick Start-sjablonen](https://azure.microsoft.com/resources/templates/101-machine-learning-create/).

:::code language="json" source="~/quickstart-templates/101-machine-learning-create/azuredeploy.json":::

De volgende resources zijn gedefinieerd in de sjabloon:

* [Microsoft.MachineLearningServices/workspaces](/azure/templates/microsoft.machinelearningservices/workspaces): Een Azure ML-werkruimte maken. In deze sjabloon zijn de locatie en naam parameters die de gebruiker kan doorgeven of interactief kan invoeren.

### <a name="deploy-the-template"></a>De sjabloon implementeren 

Als u de sjabloon uit de Azure CLI wilt gebruiken, meldt u zich aan en kiest u uw abonnement (Zie [Aanmelden met Azure CLI](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest)). Voer vervolgens

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

1. Een projectnaam die de basis vormt van de namen van de gemaakte resourcegroep en Azure ML-werkruimte
1. De Azure-locatie waarin u de implementatie wilt uitvoeren

## <a name="review-deployed-resources"></a>Geïmplementeerde resources bekijken

Uw Azure ML-werkruimte bekijken:

1. Ga naar https://portal.azure.com 
1. Aanmelden 
1. Kies de werkruimte die u zojuist hebt gemaakt

U ziet de startpagina van Azure Machine Learning: 

:::image type="content" source="media/tutorial-resource-manager-workspace/workspace-home.png" alt-text="Schermopname van de Azure ML-werkruimte":::

Als u alle resources wilt zien die zijn gekoppeld aan de implementatie, klikt u op de koppeling in de linkerbovenhoek met de naam van de werkruimte (in de schermopname, `my_templated_ws`). Via deze koppeling gaat u naar de resourcegroep in Azure Portal. De naam van de resourcegroep is `{projectName}rg` en die van de werkruimte is `{projectName}ws`.

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze werkruimte niet wilt gebruiken, verwijdert u deze. Aangezien de werkruimte is gekoppeld aan andere resources, zoals een opslagaccount, wilt u waarschijnlijk de gehele resourcegroep die u hebt gemaakt verwijderen. U kunt de resourcegroep verwijderen via de portal door op de knop Verwijderen te klikken en te bevestigen. U kunt de resourcegroep ook uit de CLI verwijderen met: 

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een werkruimte voor Azure Machine Learning gemaakt met een Azure Resource Manager-sjabloon. Als u Azure Machine Learning wilt verkennen, gaat u door met de zelfstudie. 

> [!div class="nextstepaction"]
> [Zelfstudie: Ga aan de slag met het maken van uw eerste ML-experiment met de Python SDK](tutorial-1st-experiment-sdk-setup.md)
