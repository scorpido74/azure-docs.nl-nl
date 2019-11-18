---
title: Implementatiegeschiedenis
description: Hierin wordt beschreven hoe u Azure Resource Manager implementatie bewerkingen kunt weer geven met de portal, Power shell, Azure CLI en REST API.
tags: top-support-issue
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: d8daf7191bb22f7c7057f6ef6b220a18868872cc
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2019
ms.locfileid: "74149566"
---
# <a name="view-deployment-history-with-azure-resource-manager"></a>Implementatie geschiedenis met Azure Resource Manager weer geven

Met Azure Resource Manager kunt u de implementatie geschiedenis bekijken en specifieke bewerkingen in eerdere implementaties onderzoeken. U kunt de resources zien die zijn geïmplementeerd en informatie over eventuele fouten ophalen.

Zie [algemene fouten oplossen bij het implementeren van resources in azure met Azure Resource Manager](resource-manager-common-deployment-errors.md)voor hulp bij het oplossen van bepaalde implementatie fouten.

## <a name="portal"></a>Portal

Voor meer informatie over een implementatie in de implementatie geschiedenis.

1. Selecteer de resource groep die u wilt bekijken.

1. Selecteer de koppeling onder **implementaties**.

   ![Implementatie geschiedenis selecteren](./media/resource-manager-deployment-operations/select-deployment-history.png)

1. Selecteer een van de implementaties in de implementatie geschiedenis.

   ![Implementatie selecteren](./media/resource-manager-deployment-operations/select-details.png)

1. Er wordt een samen vatting van de implementatie weer gegeven, met inbegrip van een lijst met de resources die zijn geïmplementeerd.

    ![Implementatie samenvatting](./media/resource-manager-deployment-operations/view-deployment-summary.png)

1. Selecteer **sjabloon**om de sjabloon weer te geven die wordt gebruikt voor de implementatie. U kunt de sjabloon downloaden om deze opnieuw te gebruiken.

    ![Sjabloon weer geven](./media/resource-manager-deployment-operations/show-template-from-history.png)

1. Als de implementatie is mislukt, wordt een fout bericht weer gegeven. Selecteer het fout bericht voor meer informatie.

    ![Mislukte implementatie weer geven](./media/resource-manager-deployment-operations/show-error.png)

1. Het gedetailleerde fout bericht wordt weer gegeven.

    ![Fout details weer geven](./media/resource-manager-deployment-operations/show-details.png)

1. De correlatie-ID wordt gebruikt voor het bijhouden van gerelateerde gebeurtenissen en kan nuttig zijn bij het werken met technische ondersteuning bij het oplossen van problemen met een implementatie.

    ![Correlatie-ID ophalen](./media/resource-manager-deployment-operations/get-correlation-id.png)

1. Selecteer **bewerkings Details**voor meer informatie over de stap die is mislukt.

    ![Implementatie bewerkingen selecteren](./media/resource-manager-deployment-operations/select-deployment-operations.png)

1. U ziet de Details voor die stap van de implementatie.

    ![Bewerkings details weer geven](./media/resource-manager-deployment-operations/show-operation-details.png)

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Gebruik de opdracht **Get-AzResourceGroupDeployment** om de algehele status van een implementatie te verkrijgen.

```azurepowershell-interactive
Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup
```

U kunt ook de resultaten filteren voor alleen de implementaties die zijn mislukt.

```azurepowershell-interactive
Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup | Where-Object ProvisioningState -eq Failed
```

De correlatie-ID wordt gebruikt voor het bijhouden van gerelateerde gebeurtenissen en kan nuttig zijn bij het werken met technische ondersteuning bij het oplossen van problemen met een implementatie. Als u de correlatie-ID wilt ophalen, gebruikt u:

```azurepowershell-interactive
(Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -DeploymentName azuredeploy).CorrelationId
```

Elke implementatie bevat meerdere bewerkingen. Elke bewerking vertegenwoordigt een stap in het implementatie proces. Als u wilt weten wat er mis ging met een implementatie, moet u doorgaans details over de implementatie bewerkingen zien. U kunt de status van de bewerkingen zien met **Get-AzResourceGroupDeploymentOperation**.

```azurepowershell-interactive
Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName azuredeploy
```

Hiermee worden meerdere bewerkingen met elke bewerking in de volgende notatie geretourneerd:

```powershell
Id             : /subscriptions/{guid}/resourceGroups/ExampleGroup/providers/Microsoft.Resources/deployments/Microsoft.Template/operations/A3EB2DA598E0A780
OperationId    : A3EB2DA598E0A780
Properties     : @{provisioningOperation=Create; provisioningState=Succeeded; timestamp=2019-05-13T21:42:40.7151512Z;
                duration=PT23.0227078S; trackingId=11d376e8-5d6d-4da8-847e-6f23c6443fbf;
                serviceRequestId=0196828d-8559-4bf6-b6b8-8b9057cb0e23; statusCode=OK; targetResource=}
PropertiesText : {duration:PT23.0227078S, provisioningOperation:Create, provisioningState:Succeeded,
                serviceRequestId:0196828d-8559-4bf6-b6b8-8b9057cb0e23...}
```

Voor meer informatie over mislukte bewerkingen haalt u de eigenschappen op voor bewerkingen met de status **mislukt** .

```azurepowershell-interactive
(Get-AzResourceGroupDeploymentOperation -DeploymentName azuredeploy -ResourceGroupName ExampleGroup).Properties | Where-Object ProvisioningState -eq Failed
```

Hiermee worden alle mislukte bewerkingen met elk een van de volgende indelingen geretourneerd:

```powershell
provisioningOperation : Create
provisioningState     : Failed
timestamp             : 2019-05-13T21:42:40.7151512Z
duration              : PT3.1449887S
trackingId            : f4ed72f8-4203-43dc-958a-15d041e8c233
serviceRequestId      : a426f689-5d5a-448d-a2f0-9784d14c900a
statusCode            : BadRequest
statusMessage         : @{error=}
targetResource        : @{id=/subscriptions/{guid}/resourceGroups/ExampleGroup/providers/
                       Microsoft.Network/publicIPAddresses/myPublicIP;
                       resourceType=Microsoft.Network/publicIPAddresses; resourceName=myPublicIP}
```

Noteer de serviceRequestId en de trackingId voor de bewerking. De serviceRequestId kan handig zijn bij het werken met technische ondersteuning bij het oplossen van problemen met een implementatie. U gebruikt de trackingId in de volgende stap om te focussen op een bepaalde bewerking.

Als u het status bericht van een bepaalde mislukte bewerking wilt ophalen, gebruikt u de volgende opdracht:

```azurepowershell-interactive
((Get-AzResourceGroupDeploymentOperation -DeploymentName azuredeploy -ResourceGroupName ExampleGroup).Properties | Where-Object trackingId -eq f4ed72f8-4203-43dc-958a-15d041e8c233).StatusMessage.error
```

Hiermee wordt het volgende geretourneerd:

```powershell
code           message                                                                        details
----           -------                                                                        -------
DnsRecordInUse DNS record dns.westus.cloudapp.azure.com is already used by another public IP. {}
```

Elke implementatie bewerking in azure bevat informatie over de aanvraag en het antwoord. Tijdens de implementatie kunt u de para meter **DeploymentDebugLogLevel** gebruiken om op te geven dat de aanvraag en/of het antwoord worden vastgelegd.

U ontvangt deze informatie uit het logboek en slaat deze lokaal op met behulp van de volgende Power shell-opdrachten:

```powershell
(Get-AzResourceGroupDeploymentOperation -DeploymentName "TestDeployment" -ResourceGroupName "Test-RG").Properties.request | ConvertTo-Json |  Out-File -FilePath <PathToFile>

(Get-AzResourceGroupDeploymentOperation -DeploymentName "TestDeployment" -ResourceGroupName "Test-RG").Properties.response | ConvertTo-Json |  Out-File -FilePath <PathToFile>
```

## <a name="azure-cli"></a>Azure-CLI

Als u de algehele status van een implementatie wilt weten, gebruikt u de opdracht **show Azure Group Deployment** .

```azurecli-interactive
az group deployment show -g ExampleGroup -n ExampleDeployment
```
  
De correlatie-ID wordt gebruikt voor het bijhouden van gerelateerde gebeurtenissen en kan nuttig zijn bij het werken met technische ondersteuning bij het oplossen van problemen met een implementatie.

```azurecli-interactive
az group deployment show -g ExampleGroup -n ExampleDeployment --query properties.correlationId
```

Als u de bewerkingen voor een implementatie wilt bekijken, gebruikt u:

```azurecli-interactive
az group deployment operation list -g ExampleGroup -n ExampleDeployment
```

## <a name="rest"></a>REST

Als u informatie wilt ophalen over een implementatie, gebruikt u de bewerking [informatie over een sjabloon implementeren](https://docs.microsoft.com/rest/api/resources/deployments) .

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}?api-version={api-version}
```

Bekijk in het antwoord met name de **provisioningState**, **correlationId**en **fout** elementen. De **correlationId** wordt gebruikt voor het bijhouden van gerelateerde gebeurtenissen en kan nuttig zijn bij het werken met technische ondersteuning bij het oplossen van problemen met een implementatie.

```json
{ 
 ...
 "properties": {
   "provisioningState":"Failed",
   "correlationId":"d5062e45-6e9f-4fd3-a0a0-6b2c56b15757",
   ...
   "error":{
     "code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.",
     "details":[{"code":"Conflict","message":"{\r\n  \"error\": {\r\n    \"message\": \"Conflict\",\r\n    \"code\": \"Conflict\"\r\n  }\r\n}"}]
   }  
 }
}
```

Voor informatie over implementaties, gebruikt u [lijst met alle sjabloon implementatie bewerkingen](https://docs.microsoft.com/rest/api/resources/deployments). 

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}/operations?$skiptoken={skiptoken}&api-version={api-version}
```
   
Het antwoord bevat aanvraag-en/of reactie gegevens op basis van wat u tijdens de implementatie hebt opgegeven in de eigenschap **debugSetting** .

```json
{
 ...
 "properties": 
 {
   ...
   "request":{
     "content":{
       "location":"West US",
       "properties":{
         "accountType": "Standard_LRS"
       }
     }
   },
   "response":{
     "content":{
       "error":{
         "message":"Conflict","code":"Conflict"
       }
     }
   }
 }
}
```

## <a name="next-steps"></a>Volgende stappen
* Zie [algemene fouten oplossen bij het implementeren van resources in azure met Azure Resource Manager](resource-manager-common-deployment-errors.md)voor hulp bij het oplossen van bepaalde implementatie fouten.
* Zie [activiteiten logboeken weer geven om Azure-resources te beheren](resource-group-audit.md)voor meer informatie over het gebruik van de activiteiten logboeken voor het controleren van andere typen acties.
* Zie [een resource groep implementeren met Azure Resource Manager sjabloon](resource-group-template-deploy.md)om uw implementatie te valideren voordat u deze uitvoert.

