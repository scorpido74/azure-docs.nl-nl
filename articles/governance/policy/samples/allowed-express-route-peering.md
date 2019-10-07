---
title: 'Voorbeeld: toegestane locaties voor ExpressRoute-peering'
description: Dit voorbeeld van een beleidsdefinitie vereist dat ExpressRoute opgegeven peeringlocaties gebruikt.
author: DCtheGeek
ms.service: azure-policy
ms.topic: sample
ms.date: 01/23/2019
ms.author: dacoulte
ms.openlocfilehash: 5273440fb142376dc4fe2dced7230906e191612b
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/06/2019
ms.locfileid: "71980682"
---
# <a name="sample---allowed-peering-location-for-expressroute"></a>Voorbeeld - Toegestane peeringlocatie voor ExpressRoute

Dit beleid vereist dat ExpressRoute opgegeven peeringlocaties gebruikt. U geeft een matrix op van toegestane peeringlocaties.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-template"></a>Voorbeeldsjabloon

[!code-json[main](../../../../policy-templates/samples/Network/express-route-peeringLocation/azurepolicy.json "Allowed Peering Location for ExpressRoute")]

U kunt deze sjabloon implementeren met behulp van [Azure Portal](#deploy-with-the-portal), met [PowerShell](#deploy-with-powershell) of met de [Azure CLI](#deploy-with-azure-cli).

## <a name="deploy-with-the-portal"></a>Implementeren met portal

[![Deploy het voor beeld van het beleid naar Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/?feature.customportal=false&microsoft_azure_policy=true&microsoft_azure_policy_policyinsights=true&feature.microsoft_azure_security_policy=true&microsoft_azure_marketplace_policy=true#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FNetwork%2Fexpress-route-peeringLocation%2Fazurepolicy.json)

## <a name="deploy-with-powershell"></a>Implementeren met PowerShell

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh-az.md)]

```azurepowershell-interactive
$definition = New-AzPolicyDefinition -Name "express-route-peeringLocation" -DisplayName "Allowed Peering Location for ExpressRoute" -description "This policy enables you to specify a set of allowed peering location for ExpressRoute" -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Network/express-route-peeringLocation/azurepolicy.rules.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Network/express-route-peeringLocation/azurepolicy.parameters.json' -Mode All
$definition
$assignment = New-AzPolicyAssignment -Name <assignmentname> -Scope <scope>  -listOfLocations <Allowed Peering Locations> -PolicyDefinition $definition
$assignment
```

### <a name="clean-up-powershell-deployment"></a>PowerShell-implementatie opschonen

Gebruik de volgende opdracht om de resourcegroep, VM, en alle gerelateerde resources te verwijderen.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="deploy-with-azure-cli"></a>Implementeren met Azure CLI

[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

```azurecli-interactive
az policy definition create --name 'express-route-peeringLocation' --display-name 'Allowed Peering Location for ExpressRoute' --description 'This policy enables you to specify a set of allowed peering location for ExpressRoute' --rules 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Network/express-route-peeringLocation/azurepolicy.rules.json' --params 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Network/express-route-peeringLocation/azurepolicy.parameters.json' --mode All

az policy assignment create --name <assignmentname> --scope <scope> --policy "express-route-peeringLocation"
```

### <a name="clean-up-azure-cli-deployment"></a>Implementatie van Azure CLI opschonen

Gebruik de volgende opdracht om de resourcegroep, VM, en alle gerelateerde resources te verwijderen.

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Volgende stappen

- Bekijk meer voorbeelden op [Voorbeelden van Azure Policy](index.md)