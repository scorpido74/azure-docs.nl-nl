---
title: Aangepaste rollen voor Azure-resources maken of bijwerken met Azure PowerShell
description: Meer informatie over het aanbieden, maken, bijwerken of verwijderen van aangepaste rollen met RBAC (Role-based access control) voor Azure-resources met Azure PowerShell.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 9e225dba-9044-4b13-b573-2f30d77925a9
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/18/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 3c72e04ff7a08fecc2ef352a5879898c4c6d41c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062282"
---
# <a name="create-or-update-custom-roles-for-azure-resources-using-azure-powershell"></a>Aangepaste rollen voor Azure-resources maken of bijwerken met Azure PowerShell

> [!IMPORTANT]
> Het toevoegen van `AssignableScopes` een beheergroep aan is momenteel in preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie.

Als de [ingebouwde rollen voor Azure-resources](built-in-roles.md) niet voldoen aan de specifieke behoeften van uw organisatie, u uw eigen aangepaste rollen maken. In dit artikel wordt beschreven hoe aangepaste rollen met Azure PowerShell kunnen worden weergegeven, gemaakt, bijgewerkt of verwijderd.

Zie [Zelfstudie: Een aangepaste rol maken voor Azure-resources met Azure PowerShell voor](tutorial-custom-role-powershell.md)een stapsgewijze zelfstudie over het maken van een aangepaste rol.

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten

Als u aangepaste rollen wilt maken, hebt u het belangrijkste nodig:

- Machtigingen voor het maken van aangepaste rollen, zoals [Eigenaar](built-in-roles.md#owner) of [Administrator voor gebruikerstoegang](built-in-roles.md#user-access-administrator)
- [Azure Cloud Shell](../cloud-shell/overview.md) of [Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="list-custom-roles"></a>Aangepaste rollen opvragen

Als u de rollen wilt weergeven die beschikbaar zijn voor toewijzing op een bereik, gebruikt u de opdracht [Get-AzRoleDefinition.](/powershell/module/az.resources/get-azroledefinition) In het volgende voorbeeld worden alle rollen weergegeven die beschikbaar zijn voor toewijzing in het geselecteerde abonnement.

```azurepowershell
Get-AzRoleDefinition | FT Name, IsCustom
```

```Example
Name                                              IsCustom
----                                              --------
Virtual Machine Operator                              True
AcrImageSigner                                       False
AcrQuarantineReader                                  False
AcrQuarantineWriter                                  False
API Management Service Contributor                   False
...
```

In het volgende voorbeeld worden alleen de aangepaste rollen weergegeven die beschikbaar zijn voor toewijzing in het geselecteerde abonnement.

```azurepowershell
Get-AzRoleDefinition | ? {$_.IsCustom -eq $true} | FT Name, IsCustom
```

```Example
Name                     IsCustom
----                     --------
Virtual Machine Operator     True
```

Als het geselecteerde abonnement zich `AssignableScopes` niet in de rol bevindt, wordt de aangepaste rol niet weergegeven.

## <a name="list-a-custom-role-definition"></a>Een aangepaste roldefinitie weergeven

Als u een aangepaste roldefinitie wilt weergeven, gebruikt u [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition). Dit is dezelfde opdracht als u gebruikt voor een ingebouwde rol.

```azurepowershell
Get-AzRoleDefinition <role_name> | ConvertTo-Json
```

```Example
PS C:\> Get-AzRoleDefinition "Virtual Machine Operator" | ConvertTo-Json

{
  "Name": "Virtual Machine Operator",
  "Id": "00000000-0000-0000-0000-000000000000",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.ResourceHealth/availabilityStatuses/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
    "/subscriptions/11111111-1111-1111-1111-111111111111"
  ]
}
```

In het volgende voorbeeld worden alleen de acties van de rol weergegeven:

```azurepowershell
(Get-AzRoleDefinition <role_name>).Actions
```

```Example
PS C:\> (Get-AzRoleDefinition "Virtual Machine Operator").Actions

"Microsoft.Storage/*/read",
"Microsoft.Network/*/read",
"Microsoft.Compute/*/read",
"Microsoft.Compute/virtualMachines/start/action",
"Microsoft.Compute/virtualMachines/restart/action",
"Microsoft.Authorization/*/read",
"Microsoft.ResourceHealth/availabilityStatuses/read",
"Microsoft.Resources/subscriptions/resourceGroups/read",
"Microsoft.Insights/alertRules/*",
"Microsoft.Insights/diagnosticSettings/*",
"Microsoft.Support/*"
```

## <a name="create-a-custom-role"></a>Een aangepaste rol maken

Als u een aangepaste rol wilt maken, gebruikt u de opdracht [Nieuw-AzRoleDefinition.](/powershell/module/az.resources/new-azroledefinition) Er zijn twee methoden voor het `PSRoleDefinition` structureren van de rol, met behulp van object of een JSON-sjabloon. 

### <a name="get-operations-for-a-resource-provider"></a>Bewerkingen voor een resourceprovider opvragen

Wanneer u aangepaste rollen maakt, is het belangrijk om alle mogelijke bewerkingen van de resourceproviders te kennen.
U de lijst met bewerkingen van [resourceprovideren](resource-provider-operations.md) bekijken of u de opdracht [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) gebruiken om deze informatie te krijgen.
Als u bijvoorbeeld alle beschikbare bewerkingen voor virtuele machines wilt controleren, gebruikt u de opdracht:

```azurepowershell
Get-AzProviderOperation <operation> | FT OperationName, Operation, Description -AutoSize
```

```Example
PS C:\> Get-AzProviderOperation "Microsoft.Compute/virtualMachines/*" | FT OperationName, Operation, Description -AutoSize

OperationName                                  Operation                                                      Description
-------------                                  ---------                                                      -----------
Get Virtual Machine                            Microsoft.Compute/virtualMachines/read                         Get the propertie...
Create or Update Virtual Machine               Microsoft.Compute/virtualMachines/write                        Creates a new vir...
Delete Virtual Machine                         Microsoft.Compute/virtualMachines/delete                       Deletes the virtu...
Start Virtual Machine                          Microsoft.Compute/virtualMachines/start/action                 Starts the virtua...
...
```

### <a name="create-a-custom-role-with-the-psroledefinition-object"></a>Een aangepaste rol maken met het object PSRoleDefinition

Wanneer u PowerShell gebruikt om een aangepaste rol te maken, u een van de [ingebouwde rollen](built-in-roles.md) als uitgangspunt gebruiken of helemaal opnieuw beginnen. Het eerste voorbeeld in deze sectie begint met een ingebouwde rol en past deze vervolgens aan met meer machtigingen. Bewerk de kenmerken om `Actions` `NotActions`de `AssignableScopes` , wilt toevoegen of dat u wilt, en sla de wijzigingen op als een nieuwe rol.

In het volgende voorbeeld begint de ingebouwde rol [Virtuele machineinzender](built-in-roles.md#virtual-machine-contributor) om een aangepaste rol met de naam *Virtual Machine Operator*te maken. De nieuwe rol geeft toegang tot alle leesbewerkingen van *Microsoft.Compute,* *Microsoft.Storage*en *Microsoft.Network resourceproviders* en verleent toegang tot het starten, opnieuw opstarten en bewaken van virtuele machines. De aangepaste rol kan worden gebruikt in twee abonnementen.

```azurepowershell
$role = Get-AzRoleDefinition "Virtual Machine Contributor"
$role.Id = $null
$role.Name = "Virtual Machine Operator"
$role.Description = "Can monitor and restart virtual machines."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Storage/*/read")
$role.Actions.Add("Microsoft.Network/*/read")
$role.Actions.Add("Microsoft.Compute/*/read")
$role.Actions.Add("Microsoft.Compute/virtualMachines/start/action")
$role.Actions.Add("Microsoft.Compute/virtualMachines/restart/action")
$role.Actions.Add("Microsoft.Authorization/*/read")
$role.Actions.Add("Microsoft.ResourceHealth/availabilityStatuses/read")
$role.Actions.Add("Microsoft.Resources/subscriptions/resourceGroups/read")
$role.Actions.Add("Microsoft.Insights/alertRules/*")
$role.Actions.Add("Microsoft.Support/*")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/00000000-0000-0000-0000-000000000000")
$role.AssignableScopes.Add("/subscriptions/11111111-1111-1111-1111-111111111111")
New-AzRoleDefinition -Role $role
```

In het volgende voorbeeld wordt een andere manier weergegeven om de aangepaste rol *van de virtuele machineoperator* te maken. Het begint met `PSRoleDefinition` het maken van een nieuw object. De actiebewerkingen worden `perms` opgegeven in `Actions` de variabele en ingesteld op de eigenschap. De `NotActions` eigenschap wordt ingesteld `NotActions` door het lezen van de van de [Virtuele Machine Contributor](built-in-roles.md#virtual-machine-contributor) ingebouwde rol. Aangezien [Virtual Machine Contributor](built-in-roles.md#virtual-machine-contributor) `NotActions`geen bijdrage heeft, is deze regel niet vereist, maar het laat zien hoe informatie kan worden opgehaald uit een andere rol.

```azurepowershell
$role = [Microsoft.Azure.Commands.Resources.Models.Authorization.PSRoleDefinition]::new()
$role.Name = 'Virtual Machine Operator 2'
$role.Description = 'Can monitor and restart virtual machines.'
$role.IsCustom = $true
$perms = 'Microsoft.Storage/*/read','Microsoft.Network/*/read','Microsoft.Compute/*/read'
$perms += 'Microsoft.Compute/virtualMachines/start/action','Microsoft.Compute/virtualMachines/restart/action'
$perms += 'Microsoft.Authorization/*/read'
$perms += 'Microsoft.ResourceHealth/availabilityStatuses/read'
$perms += 'Microsoft.Resources/subscriptions/resourceGroups/read'
$perms += 'Microsoft.Insights/alertRules/*','Microsoft.Support/*'
$role.Actions = $perms
$role.NotActions = (Get-AzRoleDefinition -Name 'Virtual Machine Contributor').NotActions
$subs = '/subscriptions/00000000-0000-0000-0000-000000000000','/subscriptions/11111111-1111-1111-1111-111111111111'
$role.AssignableScopes = $subs
New-AzRoleDefinition -Role $role
```

### <a name="create-a-custom-role-with-json-template"></a>Een aangepaste rol maken met JSON-sjabloon

Een JSON-sjabloon kan worden gebruikt als brondefinitie voor de aangepaste rol. In het volgende voorbeeld wordt een aangepaste rol gemaakt waarmee leestoegang tot opslag- en rekenbronnen, toegang tot ondersteuning en het toevoegt van die rol aan twee abonnementen. Maak een `C:\CustomRoles\customrole1.json` nieuw bestand met het volgende voorbeeld. De id moet `null` worden ingesteld op bij het maken van een nieuwe rol, omdat een nieuwe id automatisch wordt gegenereerd. 

```json
{
  "Name": "Custom Role 1",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows for read access to Azure storage and compute resources and access to support",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Storage/*/read",
    "Microsoft.Support/*"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000",
    "/subscriptions/11111111-1111-1111-1111-111111111111"
  ]
}
```

Voer de volgende PowerShell-opdracht uit om de rol aan de abonnementen toe te voegen:

```azurepowershell
New-AzRoleDefinition -InputFile "C:\CustomRoles\customrole1.json"
```

## <a name="update-a-custom-role"></a>Een aangepaste rol bijwerken

Net als bij het maken van een aangepaste rol, u een bestaande aangepaste rol wijzigen met behulp van het `PSRoleDefinition` object of een JSON-sjabloon.

### <a name="update-a-custom-role-with-the-psroledefinition-object"></a>Een aangepaste rol bijwerken met het object PSRoleDefinition

Als u een aangepaste rol wilt wijzigen, gebruikt u eerst de opdracht [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) om de roldefinitie op te halen. Ten tweede, breng de gewenste wijzigingen in de roldefinitie. Gebruik ten slotte de opdracht [Set-AzRoleDefinition](/powershell/module/az.resources/set-azroledefinition) om de gewijzigde roldefinitie op te slaan.

In het volgende `Microsoft.Insights/diagnosticSettings/*` voorbeeld wordt de bewerking toegevoegd aan de aangepaste rol *van de virtuele machineoperator.*

```azurepowershell
$role = Get-AzRoleDefinition "Virtual Machine Operator"
$role.Actions.Add("Microsoft.Insights/diagnosticSettings/*")
Set-AzRoleDefinition -Role $role
```

```Example
PS C:\> $role = Get-AzRoleDefinition "Virtual Machine Operator"
PS C:\> $role.Actions.Add("Microsoft.Insights/diagnosticSettings/*")
PS C:\> Set-AzRoleDefinition -Role $role

Name             : Virtual Machine Operator
Id               : 88888888-8888-8888-8888-888888888888
IsCustom         : True
Description      : Can monitor and restart virtual machines.
Actions          : {Microsoft.Storage/*/read, Microsoft.Network/*/read, Microsoft.Compute/*/read,
                   Microsoft.Compute/virtualMachines/start/action...}
NotActions       : {}
AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000,
                   /subscriptions/11111111-1111-1111-1111-111111111111}
```

In het volgende voorbeeld wordt een Azure-abonnement toegevoegd aan de toewijsbare scopes van de aangepaste rol *Van de virtuele machineoperator.*

```azurepowershell
Get-AzSubscription -SubscriptionName Production3

$role = Get-AzRoleDefinition "Virtual Machine Operator"
$role.AssignableScopes.Add("/subscriptions/22222222-2222-2222-2222-222222222222")
Set-AzRoleDefinition -Role $role
```

```Example
PS C:\> Get-AzSubscription -SubscriptionName Production3

Name     : Production3
Id       : 22222222-2222-2222-2222-222222222222
TenantId : 99999999-9999-9999-9999-999999999999
State    : Enabled

PS C:\> $role = Get-AzRoleDefinition "Virtual Machine Operator"
PS C:\> $role.AssignableScopes.Add("/subscriptions/22222222-2222-2222-2222-222222222222")
PS C:\> Set-AzRoleDefinition -Role $role

Name             : Virtual Machine Operator
Id               : 88888888-8888-8888-8888-888888888888
IsCustom         : True
Description      : Can monitor and restart virtual machines.
Actions          : {Microsoft.Storage/*/read, Microsoft.Network/*/read, Microsoft.Compute/*/read,
                   Microsoft.Compute/virtualMachines/start/action...}
NotActions       : {}
AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000,
                   /subscriptions/11111111-1111-1111-1111-111111111111,
                   /subscriptions/22222222-2222-2222-2222-222222222222}
```

In het volgende voorbeeld `AssignableScopes` wordt een beheergroep toegevoegd aan de aangepaste rol *van de virtuele machineoperator.* Het toevoegen van `AssignableScopes` een beheergroep aan is momenteel in preview.

```azurepowershell
Get-AzManagementGroup

$role = Get-AzRoleDefinition "Virtual Machine Operator"
$role.AssignableScopes.Add("/providers/Microsoft.Management/managementGroups/{groupId1}")
Set-AzRoleDefinition -Role $role
```

```Example
PS C:\> Get-AzManagementGroup

Id          : /providers/Microsoft.Management/managementGroups/marketing-group
Type        : /providers/Microsoft.Management/managementGroups
Name        : marketing-group
TenantId    : 99999999-9999-9999-9999-999999999999
DisplayName : Marketing group

PS C:\> $role = Get-AzRoleDefinition "Virtual Machine Operator"
PS C:\> $role.AssignableScopes.Add("/providers/Microsoft.Management/managementGroups/marketing-group")
PS C:\> Set-AzRoleDefinition -Role $role

Name             : Virtual Machine Operator
Id               : 88888888-8888-8888-8888-888888888888
IsCustom         : True
Description      : Can monitor and restart virtual machines.
Actions          : {Microsoft.Storage/*/read, Microsoft.Network/*/read, Microsoft.Compute/*/read,
                   Microsoft.Compute/virtualMachines/start/action...}
NotActions       : {}
AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000,
                   /subscriptions/11111111-1111-1111-1111-111111111111,
                   /subscriptions/22222222-2222-2222-2222-222222222222,
                   /providers/Microsoft.Management/managementGroups/marketing-group}
```

### <a name="update-a-custom-role-with-a-json-template"></a>Een aangepaste rol bijwerken met een JSON-sjabloon

Met de vorige JSON-sjabloon u eenvoudig een bestaande aangepaste rol wijzigen om acties toe te voegen of te verwijderen. Werk de JSON-sjabloon bij en voeg de leesactie voor netwerken toe, zoals in het volgende voorbeeld wordt weergegeven. De definities in de sjabloon worden niet cumulatief toegepast op een bestaande definitie, wat betekent dat de rol precies wordt weergegeven zoals u in de sjabloon opgeeft. U moet ook het veld Id bijwerken met de id van de rol. Als u niet zeker weet wat deze waarde is, u de [cmdlet Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) gebruiken om deze informatie te krijgen.

```json
{
  "Name": "Custom Role 1",
  "Id": "acce7ded-2559-449d-bcd5-e9604e50bad1",
  "IsCustom": true,
  "Description": "Allows for read access to Azure storage and compute resources and access to support",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Support/*"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000",
    "/subscriptions/11111111-1111-1111-1111-111111111111"
  ]
}
```

Voer de volgende PowerShell-opdracht uit om de bestaande rol bij te werken:

```azurepowershell
Set-AzRoleDefinition -InputFile "C:\CustomRoles\customrole1.json"
```

## <a name="delete-a-custom-role"></a>Een aangepaste rol verwijderen

Als u een aangepaste rol wilt verwijderen, gebruikt u de opdracht [Verwijderen-AzRoleDefinition.](/powershell/module/az.resources/remove-azroledefinition)

In het volgende voorbeeld wordt de aangepaste rol *van virtual machine operator* verwijderd.

```azurepowershell
Get-AzRoleDefinition "Virtual Machine Operator"
Get-AzRoleDefinition "Virtual Machine Operator" | Remove-AzRoleDefinition
```

```Example
PS C:\> Get-AzRoleDefinition "Virtual Machine Operator"

Name             : Virtual Machine Operator
Id               : 88888888-8888-8888-8888-888888888888
IsCustom         : True
Description      : Can monitor and restart virtual machines.
Actions          : {Microsoft.Storage/*/read, Microsoft.Network/*/read, Microsoft.Compute/*/read,
                   Microsoft.Compute/virtualMachines/start/action...}
NotActions       : {}
AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000,
                   /subscriptions/11111111-1111-1111-1111-111111111111}

PS C:\> Get-AzRoleDefinition "Virtual Machine Operator" | Remove-AzRoleDefinition

Confirm
Are you sure you want to remove role definition with name 'Virtual Machine Operator'.
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y
```

## <a name="next-steps"></a>Volgende stappen

- [Zelfstudie: Een aangepaste rol maken voor Azure-resources met Azure PowerShell](tutorial-custom-role-powershell.md)
- [Aangepaste rollen voor Azure-resources](custom-roles.md)
- [Azure Resource Manager-resourceproviderbewerkingen](resource-provider-operations.md)
