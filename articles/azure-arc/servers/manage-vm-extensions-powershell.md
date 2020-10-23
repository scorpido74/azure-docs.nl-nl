---
title: VM-extensie inschakelen met behulp van Azure PowerShell
description: In dit artikel wordt beschreven hoe u virtuele-machine uitbreidingen implementeert voor Azure Arc-servers die worden uitgevoerd in hybride Cloud omgevingen met behulp van Azure PowerShell.
ms.date: 10/19/2020
ms.topic: conceptual
ms.openlocfilehash: 631aa323fee8db712acc975336bdbf9436833240
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92462887"
---
# <a name="enable-azure-vm-extensions-using-azure-powershell"></a>Azure VM-extensies inschakelen met behulp van Azure PowerShell

Dit artikel laat u zien hoe u Azure-VM-extensies, ondersteund door Azure Arc-servers, kunt implementeren en verwijderen naar een Linux-of Windows-hybride computer met Azure PowerShell.

## <a name="prerequisites"></a>Vereisten

- Een computer met Azure PowerShell. Zie [Azure PowerShell installeren en configureren](/powershell/azure/)voor instructies.

Voordat u Azure PowerShell gebruikt om VM-extensies te beheren op uw hybride server die wordt beheerd door servers met Arc-functionaliteit, moet u de `Az.ConnectedMachine` module installeren. Voer de volgende opdracht uit op de server met Arc-functionaliteit:

`Install-Module -Name Az.ConnectedMachine`.

Wanneer de installatie is voltooid, wordt het volgende bericht weer gegeven:

`The installed extension `AZ. ConnectedMachine` is experimental and not covered by customer support. Please use with discretion.`

## <a name="enable-extension"></a>Extensie inschakelen

Als u een VM-extensie wilt inschakelen op de server waarop u een Arc hebt ingeschakeld, gebruikt u [New-AzConnectedMachineExtension](/powershell/module/az.connectedmachine/new-azconnectedmachineextension) met de `-Name` `-ResourceGroupName` `-MachineName` `-Location` `-Publisher` `ExtensionType` `-Settings` para meters,,,,, en.

In het volgende voor beeld wordt de extensie Log Analytics VM ingeschakeld op een Linux-server die is ingeschakeld voor Arc:

```powershell
PS C:\> $Setting = @{ "workspaceId" = "workspaceId" }
PS C:\> $protectedSetting = @{ "workspaceKey" = "workspaceKey" }
PS C:\> New-AzConnectedMachine -Name OMSLinuxAgent -ResourceGroupName "myResourceGroup" -MachineName "myMachine" -Location "eastus" -Publisher "Microsoft.EnterpriseCloud.Monitoring" -TypeHandlerVersion "1.10" -Settings $Setting -ProtectedSetting $protectedSetting -ExtensionType OmsAgentforLinux"
```

In het volgende voor beeld wordt de aangepaste script extensie ingeschakeld op een server met Arc-functionaliteit:

```powershell
PS C:\> $Setting = @{ "commandToExecute" = "powershell.exe -c Get-Process" }
PS C:\> New-AzConnectedMachine -Name custom -ResourceGroupName myResourceGroup -MachineName myMachineName -Location eastus -Publisher "Microsoft.Compute" -TypeHandlerVersion 1.10 -Settings $Setting -ExtensionType CustomScriptExtension
```

## <a name="list-extensions-installed"></a>Lijst met geïnstalleerde uitbrei dingen

Gebruik [Get-AzConnectedMachineExtension](/powershell/module/az.connectedmachine/get-azconnectedmachineextension) met de `-MachineName` para meters om een lijst op te halen van de VM-extensies op de door Arc ingeschakelde server `-ResourceGroupName` .

Voorbeeld:

```powershell
Get-AzConnectedMachineExtension -ResourceGroupName myResourceGroup -MachineName myMachineName

Name    Location  PropertiesType        ProvisioningState
----    --------  --------------        -----------------
custom  westus2   CustomScriptExtension Succeeded
```

## <a name="remove-an-installed-extension"></a>Een geïnstalleerde extensie verwijderen

Gebruik [Remove-AzConnectedMachineExtension](/powershell/module/az.connectedmachine/remove-azconnectedmachineextension) met de `-Name` `-MachineName` para meters om een geïnstalleerde VM-extensie op uw server met Arc-functionaliteit te verwijderen `-ResourceGroupName` .

Als u de Log Analytics VM-extensie voor Linux bijvoorbeeld wilt verwijderen, voert u de volgende opdracht uit:

```powershell
Remove-AzConnectedMachineExtension -MachineName myMachineName -ResourceGroupName myResourceGroup -Name OmsAgentforLinux
```

## <a name="next-steps"></a>Volgende stappen

- U kunt VM-extensies implementeren, beheren en verwijderen met behulp van de [Azure cli](manage-vm-extensions-cli.md), via de [Azure Portal](manage-vm-extensions-portal.md)of [Azure Resource Manager sjablonen](manage-vm-extensions-template.md).

- Informatie over probleem oplossing vindt u in de [gids voor het oplossen van problemen met VM-extensies](troubleshoot-vm-extensions.md).
