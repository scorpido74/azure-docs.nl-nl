---
title: Sjablonen voor logische app gebruiken
description: Meer informatie over het implementeren van Azure Resource Manager-sjablonen die zijn gemaakt voor Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/01/2019
ms.openlocfilehash: 95bfa7b1918da09e4f5913eeb2b57c290b093efe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270444"
---
# <a name="deploy-azure-resource-manager-templates-for-azure-logic-apps"></a>Azure Resource Manager-sjablonen implementeren voor Azure Logic Apps

Nadat u een Azure Resource Manager-sjabloon voor uw logische app hebt gemaakt, u uw sjabloon op de volgende manieren implementeren:

* [Azure-portal](#portal)
* [Visual Studio](#visual-studio)
* [Azure PowerShell](#powershell)
* [Azure-CLI](#cli)
* [REST-API azure resourcemanager](../azure-resource-manager/templates/deploy-rest.md)
* [Azure DevOps](#azure-pipelines)

<a name="portal"></a>

## <a name="deploy-through-azure-portal"></a>Implementeren via Azure-portal

Als u een sjabloon voor een logische app automatisch wilt implementeren in Azure, u de volgende knop **Implementeren naar Azure** kiezen, die u aanmeldt bij de Azure-portal en u om informatie over uw logische app vraagt. U vervolgens de nodige wijzigingen aanbrengen in de sjabloon of parameters van de logische app.

[![Implementeren naar Azure](./media/logic-apps-deploy-azure-resource-manager-templates/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-logic-app-create%2Fazuredeploy.json)

U wordt bijvoorbeeld gevraagd om de volgende informatie nadat u zich hebt aangemeld bij de Azure-portal:

* Naam azure-abonnement
* Resourcegroep die u wilt gebruiken
* Locatie van logische app
* De naam voor uw logische app
* Een test URI
* Aanvaarding van de gespecificeerde voorwaarden

Zie deze onderwerpen voor meer informatie:

* [Overzicht: Implementatie automatiseren voor logische apps met Azure Resource Manager-sjablonen](logic-apps-azure-resource-manager-templates-overview.md)
* [Resources implementeren met Azure Resource Manager-sjablonen en de Azure-portal](../azure-resource-manager/templates/deploy-portal.md)

<a name="visual-studio"></a>

## <a name="deploy-with-visual-studio"></a>Implementeren met Visual Studio

Voer deze stappen uit om een sjabloon voor logische apps te implementeren uit een Azure Resource Group-project dat u hebt gemaakt met Behulp van Visual Studio, [om uw logische app handmatig te implementeren](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure) in Azure.

<a name="powershell"></a>

## <a name="deploy-with-azure-powershell"></a>Implementeren met Azure PowerShell

Als u wilt implementeren in een specifieke *Azure-brongroep,* gebruikt u de volgende opdracht:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName <Azure-resource-group-name> -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json
```

Zie deze onderwerpen voor meer informatie:

* [Resources implementeren met Resource Manager-sjablonen en Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)
* [`New-AzResourceGroupDeployment`](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment)

<a name="cli"></a>

## <a name="deploy-with-azure-cli"></a>Implementeren met Azure CLI

Als u wilt implementeren in een specifieke *Azure-brongroep,* gebruikt u de volgende opdracht:

```azurecli
az group deployment create -g <Azure-resource-group-name> --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json
```

Zie deze onderwerpen voor meer informatie:

* [Resources implementeren met Resource Manager-sjablonen en Azure CLI](../azure-resource-manager/templates/deploy-cli.md) 
* [`az group deployment create`](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create)

<a name="azure-pipelines"></a>

## <a name="deploy-with-azure-devops"></a>Implementeren met Azure DevOps

Om logische app-sjablonen te implementeren en omgevingen te beheren, gebruiken teams vaak een hulpprogramma zoals [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/what-is-azure-pipelines) in [Azure DevOps.](https://docs.microsoft.com/azure/devops/user-guide/what-is-azure-devops-services) Azure Pipelines biedt een [Azure Resource Group Deployment-taak](https://github.com/Microsoft/azure-pipelines-tasks/tree/master/Tasks/AzureResourceGroupDeploymentV2) die u toevoegen aan een build- of releasepijplijn. Voor autorisatie voor het implementeren en genereren van de releasepijplijn hebt u ook een [AD-serviceprincipal](../active-directory/develop/app-objects-and-service-principals.md)(Azure Active Directory) nodig. Meer informatie over [het gebruik van serviceprincipals met Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure).

Zie de volgende onderwerpen en voorbeelden voor meer informatie over continue integratie en continue implementatie (CI/CD) voor Azure Resource Manager-sjablonen met Azure Pipelines:

* [Resourcebeheersjablonen integreren met Azure Pipelines](../azure-resource-manager/templates/add-template-to-azure-pipelines.md)
* [Zelfstudie: Continue integratie voor Azure Resource Manager-sjablonen met Azure Pipelines](../azure-resource-manager/templates/template-tutorial-use-azure-pipelines.md)
* [Voorbeeld: Verbinding maken met Azure Service Bus-wachtrijen vanuit Azure Logic Apps en implementeren met Azure Pipelines in Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-service-bus-queues-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Voorbeeld: Verbinding maken met Azure Storage-accounts vanuit Azure Logic Apps en implementeren met Azure Pipelines in Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-storage-accounts-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Voorbeeld: Een functie-app-actie instellen voor Azure Logic Apps en implementeren met Azure Pipelines in Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/set-up-an-azure-function-app-action-for-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Voorbeeld: Verbinding maken met een integratieaccount van Azure Logic Apps en implementeren met Azure Pipelines in Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-an-integration-account-from-azure-logic-apps-and-deploy-by-using-azure-devops-pipelines/)
* [Voorbeeld: Azure-pijplijnen orkestreren met Azure Logic Apps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-pipeline-orchestration/azure-devops-orchestration-with-logic-apps/)

Dit zijn de algemene stappen op hoog niveau voor het gebruik van Azure Pipelines:

1. Maak in Azure Pipelines een lege pijplijn.

1. Kies de resources die u nodig hebt voor de pijplijn, zoals uw sjabloon voor logische apps en sjabloonparametersbestanden, die u handmatig of als onderdeel van het buildproces genereert.

1. Zoek en voeg de **taak Azure Resource Group Deployment** toe voor uw agenttaak.

   ![Taak 'Azure Resource Group Deployment' toevoegen](./media/logic-apps-deploy-azure-resource-manager-templates/add-azure-resource-group-deployment-task.png)

1. Configureren met een [serviceprincipal](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure).

1. Voeg verwijzingen toe aan de sjabloon- en sjabloonparametersbestanden van uw logische app.

1. Ga door met het uitbouwen van stappen in het releaseproces voor elke andere omgeving, geautomatiseerde test of fiatteurs als dat nodig is.

<a name="authorize-oauth-connections"></a>

## <a name="authorize-oauth-connections"></a>OAuth-verbindingen autoriseren

Na de implementatie werkt uw logische app end-to-end met geldige parameters. U moet echter nog steeds oauth-verbindingen autoriseren om geldige toegangstokens te genereren voor [het verifiëren van uw referenties.](../active-directory/develop/authentication-scenarios.md) Hier volgen manieren waarop u OAuth-verbindingen autoriseren:

* Voor geautomatiseerde implementaties u een script gebruiken dat toestemming biedt voor elke OAuth-verbinding. Hier is een voorbeeldscript in GitHub in het [LogicAppConnection-project.](https://github.com/logicappsio/LogicAppConnectionAuth)

* Als u OAuth-verbindingen handmatig wilt autoriseren, opent u uw logische app in Logic App Designer, in de Azure-portal of in Visual Studio. Geef in de ontwerper toestemming voor alle vereiste verbindingen.

Als u in plaats daarvan een Azure Active [Directory-serviceprincipal](../active-directory/develop/app-objects-and-service-principals.md) (Azure Active Directory) gebruikt om verbindingen te autoriseren, leest u hoe u [serviceprincipalparameters opgeeft in uw sjabloon voor logische apps](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#authenticate-connections).

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Logische apps bewaken](../logic-apps/monitor-logic-apps.md)
