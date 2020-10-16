---
title: Een aangepaste Azure-rol maken met behulp van een Azure Resource Manager sjabloon-Azure RBAC
description: Meer informatie over het maken van een aangepaste Azure-rol met behulp van Azure Resource Manager sjablonen en Azure op rollen gebaseerd toegangs beheer (Azure RBAC).
services: role-based-access-control,azure-resource-manager
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.custom: subject-armqs
ms.workload: identity
ms.date: 06/25/2020
ms.author: rolyon
ms.openlocfilehash: bcf1966ffc326291448cb611d99390fe0d652151
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85392928"
---
# <a name="create-an-azure-custom-role-using-an-azure-resource-manager-template"></a>Een aangepaste Azure-rol maken met behulp van een Azure Resource Manager sjabloon

Als de [ingebouwde rollen van Azure](built-in-roles.md) niet voldoen aan de specifieke behoeften van uw organisatie, kunt u uw eigen [aangepaste rollen](custom-roles.md)maken. In dit artikel wordt beschreven hoe u een aangepaste rol maakt met behulp van een Azure Resource Manager sjabloon.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Vereisten

Als u een aangepaste rol wilt maken, hebt u het volgende nodig:

- Machtigingen voor het maken van aangepaste rollen, zoals [Eigenaar](built-in-roles.md#owner) of [Administrator voor gebruikerstoegang](built-in-roles.md#user-access-administrator)

## <a name="create-a-custom-role"></a>Een aangepaste rol maken

Als u een aangepaste rol wilt maken, geeft u een rolnaam, machtigingen en waar de rol kan worden gebruikt. In dit artikel maakt u een rol met de naam ' aangepaste rol-RG lezer ' met resource machtigingen die kunnen worden toegewezen bij een abonnements bereik of minder.

### <a name="review-the-template"></a>De sjabloon controleren

De sjabloon die in dit artikel wordt gebruikt, is afkomstig uit [Azure Quick](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-deployments/create-role-def)start-sjablonen. De sjabloon heeft vier para meters en een sectie resources. De vier para meters zijn:

- Matrix van acties met de standaard waarde [' micro soft. resources/abonnementen/resourceGroups/lezen ']
- Matrix van intacte waarden met een lege standaard waarde
- Rolnaam met de standaard waarde ' aangepaste rol-RG-lezer '
- Beschrijving van rol met de standaard waarde ' implementatie op abonnements niveau van een roldefinitie '

De resource die is gedefinieerd in de sjabloon:

- [Micro soft. Authorization/roleDefinitions](/azure/templates/Microsoft.Authorization/roleDefinitions)

Het bereik waar deze aangepaste rol kan worden toegewezen, wordt ingesteld op het huidige abonnement.

:::code language="json" source="~/quickstart-templates/subscription-deployments/create-role-def/azuredeploy.json":::

### <a name="deploy-the-template"></a>De sjabloon implementeren

Volg deze stappen om de vorige sjabloon te implementeren.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

1. Open Azure Cloud Shell voor PowerShell.

1. Kopieer en plak het volgende script in Cloud Shell.

    ```azurepowershell
    $location = Read-Host -Prompt "Enter a location (i.e. centralus)"
    [string[]]$actions = Read-Host -Prompt "Enter actions as a comma-separated list (i.e. action1,action2)"
    $actions = $actions.Split(',')

    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/subscription-deployments/create-role-def/azuredeploy.json"

    New-AzDeployment -Location $location -TemplateUri $templateUri -actions $actions
    ```

1. Voer een locatie in voor de implementatie, zoals *middenus*.

1. Voer een lijst met acties voor de aangepaste rol in als een door komma's gescheiden lijst *, zoals micro soft. resources/resources/lezen, micro soft. resources/abonnementen/resourceGroups/lezen*.

1. Druk, indien nodig, op ENTER om de New-AzDeployment opdracht uit te voeren.

    De opdracht [New-AzDeployment](/powershell/module/az.resources/new-azdeployment) implementeert de sjabloon voor het maken van de aangepaste rol.

    De uitvoer ziet er als volgt uit:

    ```azurepowershell
    PS> New-AzDeployment -Location $location -TemplateUri $templateUri -actions $actions
    
    Id                      : /subscriptions/{subscriptionId}/providers/Microsoft.Resources/deployments/azuredeploy
    DeploymentName          : azuredeploy
    Location                : centralus
    ProvisioningState       : Succeeded
    Timestamp               : 6/25/2020 8:08:32 PM
    Mode                    : Incremental
    TemplateLink            :
                              Uri            : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/subscription-deployments/create-role-def/azuredeploy.json
                              ContentVersion : 1.0.0.0
    
    Parameters              :
                              Name               Type                       Value
                              =================  =========================  ==========
                              actions            Array                      [
                                "Microsoft.Resources/resources/read",
                                "Microsoft.Resources/subscriptions/resourceGroups/read"
                              ]
                              notActions         Array                      []
                              roleName           String                     Custom Role - RG Reader
                              roleDescription    String                     Subscription Level Deployment of a Role Definition
    
    Outputs                 :
    DeploymentDebugLogLevel :
    ```

## <a name="review-deployed-resources"></a>Geïmplementeerde resources bekijken

Volg deze stappen om te controleren of de aangepaste rol is gemaakt.

1. Voer de opdracht [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) uit om de aangepaste rol weer te geven.

    ```azurepowershell
    Get-AzRoleDefinition "Custom Role - RG Reader" | ConvertTo-Json
    ```

    De uitvoer ziet er als volgt uit:

    ```azurepowershell
    {
      "Name": "Custom Role - RG Reader",
      "Id": "11111111-1111-1111-1111-111111111111",
      "IsCustom": true,
      "Description": "Subscription Level Deployment of a Role Definition",
      "Actions": [
        "Microsoft.Resources/resources/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read"
      ],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/{subscriptionId}"
      ]
    }
    ```

1. Open uw abonnement in het Azure Portal.

1. Klik in het linkermenu op **Toegangsbeheer (IAM)** .

1. Klik op het tabblad **rollen** .

1. Stel de **type** lijst in op **CustomRole**.

1. Controleer of de rol **aangepaste rol-RG-lezer** wordt weer gegeven.

   ![Nieuwe aangepaste rol in Azure Portal](./media/custom-roles-template/custom-role-template-portal.png)

## <a name="clean-up-resources"></a>Resources opschonen

Voer de volgende stappen uit om de aangepaste functie te verwijderen.

1. Voer de volgende opdracht uit om de aangepaste functie te verwijderen.

    ```azurepowershell
    Get-AzRoleDefinition -Name "Custom Role - RG Reader" | Remove-AzRoleDefinition
    ```

1. Voer **Y** in om te bevestigen dat u de aangepaste rol wilt verwijderen.

## <a name="next-steps"></a>Volgende stappen

- [Informatie over Azure Role-definities](role-definitions.md)
- [Quickstart: Een Azure-roltoewijzing toevoegen met behulp van een Azure Resource Manager-sjabloon](quickstart-role-assignments-template.md)
- [Documentatie voor ARM-sjablonen](../azure-resource-manager/templates/index.yml)
