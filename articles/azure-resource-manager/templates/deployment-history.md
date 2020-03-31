---
title: Implementatiegeschiedenis
description: Beschrijft hoe u Azure Resource Manager-implementatiebewerkingen weergeven met de portal, PowerShell, Azure CLI en REST API.
tags: top-support-issue
ms.topic: conceptual
ms.date: 11/26/2019
ms.openlocfilehash: b0f196f86bed05094b04bfc20c7cef2248a91c65
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460293"
---
# <a name="view-deployment-history-with-azure-resource-manager"></a>Implementatiegeschiedenis weergeven met Azure Resource Manager

Met Azure Resource Manager u uw implementatiegeschiedenis bekijken en specifieke bewerkingen in eerdere implementaties onderzoeken. U de bronnen zien die zijn geïmplementeerd en informatie krijgen over eventuele fouten.

Zie [Veelvoorkomende fouten oplossen bij het implementeren van resources naar Azure met Azure Resource Manager](common-deployment-errors.md)voor hulp bij het oplossen van bepaalde implementatiefouten.

## <a name="get-deployments-and-correlation-id"></a>Implementaties en correlatie-ID

U details over een implementatie weergeven via de Azure-portal, PowerShell, Azure CLI of REST API. Elke implementatie heeft een correlatie-ID, die wordt gebruikt om gerelateerde gebeurtenissen bij te houden. Het kan handig zijn bij het werken met technische ondersteuning om een implementatie op te lossen.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Selecteer de resourcegroep die u wilt onderzoeken.

1. Selecteer de koppeling onder **Implementaties**.

   ![Implementatiegeschiedenis selecteren](./media/deployment-history/select-deployment-history.png)

1. Selecteer een van de implementaties uit de implementatiegeschiedenis.

   ![Implementatie selecteren](./media/deployment-history/select-details.png)

1. Er wordt een samenvatting van de implementatie weergegeven, inclusief de correlatie-ID.

    ![Implementatieoverzicht](./media/deployment-history/show-correlation-id.png)

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Als u alle implementaties voor een resourcegroep wilt weergeven, gebruikt u de opdracht [Get-AzResourceGroupDeployment.](/powershell/module/az.resources/Get-AzResourceGroupDeployment)

```azurepowershell-interactive
Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup
```

Als u een specifieke implementatie uit een resourcegroep wilt ophalen, voegt u de parameter **DeploymentName** toe.

```azurepowershell-interactive
Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -DeploymentName ExampleDeployment
```

Gebruik het als u de correlatie-id wilt krijgen:

```azurepowershell-interactive
(Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -DeploymentName ExampleDeployment).CorrelationId
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u de implementatie voor een resourcegroep wilt weergeven, gebruikt u [de lijst met az-implementatiegroepen](/cli/azure/group/deployment?view=azure-cli-latest#az-deployment-group-list).

```azurecli-interactive
az deployment group list --resource-group ExampleGroup
```

Gebruik de [groep az-implementatiegroep voor](/cli/azure/group/deployment?view=azure-cli-latest#az-deployment-group-show)een specifieke implementatie.

```azurecli-interactive
az deployment group show --resource-group ExampleGroup --name ExampleDeployment
```

Gebruik het als u de correlatie-id wilt krijgen:

```azurecli-interactive
az deployment group show --resource-group ExampleGroup --name ExampleDeployment --query properties.correlationId
```

# <a name="http"></a>[HTTP (HTTP)](#tab/http)

Als u de implementaties voor een resourcegroep wilt weergeven, gebruikt u de volgende bewerking. Zie [Implementaties - Lijst op resourcegroep](/rest/api/resources/deployments/listbyresourcegroup)voor het laatste API-versienummer dat in de aanvraag moet worden gebruikt.

```
GET https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Resources/deployments/?api-version={api-version}
```

Om een specifieke implementatie te krijgen. gebruik de volgende bewerking. Zie [Implementaties - Opstappen](/rest/api/resources/deployments/get)voor het nieuwste API-versienummer dat in de aanvraag moet worden gebruikt.

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}?api-version={api-version}
```

Het antwoord bevat de correlatie-ID.

```json
{
 ...
 "properties": {
   "mode": "Incremental",
   "provisioningState": "Failed",
   "timestamp": "2019-11-26T14:18:36.4518358Z",
   "duration": "PT26.2091817S",
   "correlationId": "47ff4228-bf2e-4ee5-a008-0b07da681230",
   ...
 }
}
```

---

## <a name="get-deployment-operations-and-error-message"></a>Implementatiebewerkingen en foutbericht uitvoeren

Elke implementatie kan meerdere bewerkingen bevatten. Als u meer informatie wilt over een implementatie, bekijkt u de implementatiebewerkingen. Wanneer een implementatie mislukt, bevatten de implementatiebewerkingen een foutbericht.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Selecteer In het overzicht voor een implementatie de optie **Bewerkingsdetails**.

    ![Implementatiebewerkingen selecteren](./media/deployment-history/get-operation-details.png)

1. U ziet de details voor die stap van de implementatie. Wanneer er een fout optreedt, bevatten de details het foutbericht.

    ![Bewerkingsgegevens weergeven](./media/deployment-history/see-operation-details.png)

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Als u de implementatiebewerkingen voor implementatie naar een resourcegroep wilt weergeven, gebruikt u de opdracht [Get-AzResourceGroupDeploymentOperation.](/powershell/module/az.resources/get-azdeploymentoperation)

```azurepowershell-interactive
Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName ExampleDeploy
```

Als u mislukte bewerkingen wilt weergeven, filtert u bewerkingen met de status **Mislukt.**

```azurepowershell-interactive
(Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName ExampleDeploy).Properties | Where-Object ProvisioningState -eq Failed
```

Als u het statusbericht van mislukte bewerkingen wilt ontvangen, gebruikt u de volgende opdracht:

```azurepowershell-interactive
((Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName ExampleDeploy ).Properties | Where-Object ProvisioningState -eq Failed).StatusMessage.error
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u de implementatiebewerkingen voor implementatie naar een resourcegroep wilt weergeven, gebruikt u de opdracht lijst met [de bewerkingsgroep AZ.To](/cli/azure/group/deployment/operation?view=azure-cli-latest#az-deployment-group-operation-list) view the deployment operations for deployment to a resource group, use the az deployment group list command.

```azurecli-interactive
az deployment group operation list --resource-group ExampleGroup --name ExampleDeployment
```

Als u mislukte bewerkingen wilt weergeven, filtert u bewerkingen met de status **Mislukt.**

```azurecli-interactive
az deployment group operation list --resource-group ExampleGroup --name ExampleDeploy --query "[?properties.provisioningState=='Failed']"
```

Als u het statusbericht van mislukte bewerkingen wilt ontvangen, gebruikt u de volgende opdracht:

```azurecli-interactive
az deployment group operation list --resource-group ExampleGroup --name ExampleDeploy --query "[?properties.provisioningState=='Failed'].properties.statusMessage.error"
```

# <a name="http"></a>[HTTP (HTTP)](#tab/http)

Als u implementatiebewerkingen wilt uitvoeren, gebruikt u de volgende bewerking. Zie [Implementatiebewerkingen - Lijst](/rest/api/resources/deploymentoperations/list)voor het nieuwste API-versienummer dat in de aanvraag moet worden gebruikt.

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}/operations?$skiptoken={skiptoken}&api-version={api-version}
```

Het antwoord bevat een foutmelding.

```json
{
  "value": [
    {
      "id": "/subscriptions/xxxx/resourceGroups/examplegroup/providers/Microsoft.Resources/deployments/exampledeploy/operations/13EFD9907103D640",
      "operationId": "13EFD9907103D640",
      "properties": {
        "provisioningOperation": "Create",
        "provisioningState": "Failed",
        "timestamp": "2019-11-26T14:18:36.3177613Z",
        "duration": "PT21.0580179S",
        "trackingId": "9d3cdac4-54f8-486c-94bd-10c20867b8bc",
        "serviceRequestId": "01a9d0fe-896b-4c94-a30f-60b70a8f1ad9",
        "statusCode": "BadRequest",
        "statusMessage": {
          "error": {
            "code": "InvalidAccountType",
            "message": "The AccountType Standard_LRS1 is invalid. For more information, see - https://aka.ms/storageaccountskus"
          }
        },
        "targetResource": {
          "id": "/subscriptions/xxxx/resourceGroups/examplegroup/providers/Microsoft.Storage/storageAccounts/storageq2czadzfgizc2",
          "resourceType": "Microsoft.Storage/storageAccounts",
          "resourceName": "storageq2czadzfgizc2"
        }
      }
    },
    ...
  ]
}
```

---

## <a name="next-steps"></a>Volgende stappen

* Zie [Veelvoorkomende fouten oplossen bij het implementeren van resources naar Azure met Azure Resource Manager](common-deployment-errors.md)voor hulp bij het oplossen van bepaalde implementatiefouten.
* Zie [Activiteitslogboeken weergeven voor](../management/view-activity-logs.md)meer informatie over het gebruik van de activiteitslogboeken om azure-bronnen te beheren.
* Zie [Een resourcegroep implementeren met Azure Resource Manager-sjabloon](deploy-powershell.md)om uw implementatie te valideren voordat u deze uitvoert.

