---
title: Implementatiegeschiedenis
description: Hierin wordt beschreven hoe u Azure Resource Manager implementatie bewerkingen kunt weer geven met de portal, Power shell, Azure CLI en REST API.
tags: top-support-issue
ms.topic: conceptual
ms.date: 11/26/2019
ms.openlocfilehash: 753071a3edca62690b772f7b8d34fec43641466f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75477861"
---
# <a name="view-deployment-history-with-azure-resource-manager"></a>Implementatie geschiedenis met Azure Resource Manager weer geven

Met Azure Resource Manager kunt u de implementatie geschiedenis bekijken en specifieke bewerkingen in eerdere implementaties onderzoeken. U kunt de resources zien die zijn geïmplementeerd en informatie over eventuele fouten ophalen.

Zie [algemene fouten oplossen bij het implementeren van resources in azure met Azure Resource Manager](common-deployment-errors.md)voor hulp bij het oplossen van bepaalde implementatie fouten.

## <a name="get-deployments-and-correlation-id"></a>Implementaties en correlatie-ID ophalen

U kunt details over een implementatie bekijken via de Azure Portal, Power shell, Azure CLI of REST API. Elke implementatie heeft een correlatie-ID, die wordt gebruikt voor het bijhouden van gerelateerde gebeurtenissen. Het kan handig zijn bij het werken met technische ondersteuning bij het oplossen van problemen met een implementatie.

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

1. Selecteer de resource groep die u wilt bekijken.

1. Selecteer de koppeling onder **implementaties**.

   ![Implementatie geschiedenis selecteren](./media/deployment-history/select-deployment-history.png)

1. Selecteer een van de implementaties in de implementatie geschiedenis.

   ![Implementatie selecteren](./media/deployment-history/select-details.png)

1. Er wordt een samen vatting van de implementatie weer gegeven, met inbegrip van de correlatie-ID. 

    ![Implementatieoverzicht](./media/deployment-history/show-correlation-id.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Als u alle implementaties voor een resource groep wilt weer geven, gebruikt u de opdracht [Get-AzResourceGroupDeployment](/powershell/module/az.resources/Get-AzResourceGroupDeployment) .

```azurepowershell-interactive
Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup
```

Als u een specifieke implementatie van een resource groep wilt ophalen, voegt u de para meter voor de **implementatienaam** toe.

```azurepowershell-interactive
Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -DeploymentName ExampleDeployment
```

Als u de correlatie-ID wilt ophalen, gebruikt u:

```azurepowershell-interactive
(Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -DeploymentName ExampleDeployment).CorrelationId
```

# <a name="azure-clitabazure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u de implementatie van een resource groep wilt weer geven, gebruikt u de [lijst AZ Group Deployment](/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-list).

```azurecli-interactive
az group deployment list --resource-group ExampleGroup
```

Als u een specifieke implementatie wilt ophalen, gebruikt u de [groep AZ Group Deployment show](/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-show).

```azurecli-interactive
az group deployment show --resource-group ExampleGroup --name ExampleDeployment
```
  
Als u de correlatie-ID wilt ophalen, gebruikt u:

```azurecli-interactive
az group deployment show --resource-group ExampleGroup --name ExampleDeployment --query properties.correlationId
```

# <a name="httptabhttp"></a>[HTTP](#tab/http)

Als u de implementaties voor een resource groep wilt weer geven, gebruikt u de volgende bewerking. Zie [implementaties-lijst op resource groep](/rest/api/resources/deployments/listbyresourcegroup)voor het meest recente API-versie nummer voor gebruik in de aanvraag. 

```
GET https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Resources/deployments/?api-version={api-version}
```

Om een specifieke implementatie te verkrijgen. Gebruik de volgende bewerking. Zie [implementaties-ophalen](/rest/api/resources/deployments/get)voor het meest recente API-versie nummer voor gebruik in de aanvraag.

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

## <a name="get-deployment-operations-and-error-message"></a>Implementatie bewerkingen en fout berichten ophalen

Elke implementatie kan meerdere bewerkingen bevatten. Raadpleeg de implementatie bewerkingen voor meer informatie over een implementatie. Wanneer een implementatie mislukt, bevatten de implementatie bewerkingen een fout bericht.

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

1. Selecteer op de samen vatting van een implementatie **bewerkings gegevens**.

    ![Implementatie bewerkingen selecteren](./media/deployment-history/get-operation-details.png)

1. U ziet de Details voor die stap van de implementatie. Als er een fout optreedt, bevatten de details het fout bericht.

    ![Bewerkings details weer geven](./media/deployment-history/see-operation-details.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Als u de implementatie bewerkingen voor implementatie naar een resource groep wilt weer geven, gebruikt u de opdracht [Get-AzResourceGroupDeploymentOperation](/powershell/module/az.resources/get-azdeploymentoperation) .

```azurepowershell-interactive
Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName ExampleDeploy
```

Voor het weer geven van mislukte bewerkingen, filter bewerkingen met de status **mislukt** .

```azurepowershell-interactive
(Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName ExampleDeploy).Properties | Where-Object ProvisioningState -eq Failed
```

Als u het status bericht van mislukte bewerkingen wilt ophalen, gebruikt u de volgende opdracht:

```azurepowershell-interactive
((Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName ExampleDeploy ).Properties | Where-Object ProvisioningState -eq Failed).StatusMessage.error
```

# <a name="azure-clitabazure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u de implementatie bewerkingen voor implementatie naar een resource groep wilt weer geven, gebruikt u de opdracht [AZ Group Deployment Operation List](/cli/azure/group/deployment/operation?view=azure-cli-latest#az-group-deployment-operation-list) .

```azurecli-interactive
az group deployment operation list --resource-group ExampleGroup --name ExampleDeployment
```

Voor het weer geven van mislukte bewerkingen, filter bewerkingen met de status **mislukt** .

```azurecli-interactive
az group deployment operation list --resource-group ExampleGroup --name ExampleDeploy --query "[?properties.provisioningState=='Failed']"
```

Als u het status bericht van mislukte bewerkingen wilt ophalen, gebruikt u de volgende opdracht:

```azurecli-interactive
az group deployment operation list --resource-group ExampleGroup --name ExampleDeploy --query "[?properties.provisioningState=='Failed'].properties.statusMessage.error"
```

# <a name="httptabhttp"></a>[HTTP](#tab/http)

Gebruik de volgende bewerking om implementatie bewerkingen te verkrijgen. Zie [implementatie bewerkingen-lijst](/rest/api/resources/deploymentoperations/list)voor het meest recente API-versie nummer voor gebruik in de aanvraag.

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}/operations?$skiptoken={skiptoken}&api-version={api-version}
```

Het antwoord bevat een fout bericht.

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

* Zie [algemene fouten oplossen bij het implementeren van resources in azure met Azure Resource Manager](common-deployment-errors.md)voor hulp bij het oplossen van bepaalde implementatie fouten.
* Zie [activiteiten logboeken weer geven om Azure-resources te beheren](../management/view-activity-logs.md)voor meer informatie over het gebruik van de activiteiten logboeken voor het controleren van andere typen acties.
* Zie [een resource groep implementeren met Azure Resource Manager sjabloon](deploy-powershell.md)om uw implementatie te valideren voordat u deze uitvoert.

