---
title: VM-extensie inschakelen met behulp van Azure CLI
description: In dit artikel wordt beschreven hoe u virtuele-machine uitbreidingen implementeert voor Azure Arc-servers die worden uitgevoerd in hybride Cloud omgevingen met behulp van de Azure CLI.
ms.date: 10/19/2020
ms.topic: conceptual
ms.openlocfilehash: 8f09914f246635f07b3c51c682bd67591c706732
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92462904"
---
# <a name="enable-azure-vm-extensions-using-the-azure-cli"></a>Azure-VM-extensies inschakelen met behulp van Azure CLI

In dit artikel wordt beschreven hoe u Azure-VM-extensies die worden ondersteund door Azure Arc-servers, kunt implementeren en verwijderen naar een virtuele Linux-of Windows-machine met behulp van Azure CLI.

[!INCLUDE [Azure CLI Prepare your environment](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="prerequisites"></a>Vereisten

[Installeer de Azure CLI](/cli/azure/install-azure-cli).

Voordat u de Azure CLI gebruikt om VM-extensies te beheren op uw hybride server die wordt beheerd door servers met Arc-functionaliteit, moet u de `ConnectedMachine` cli-extensie installeren. Voer de volgende opdracht uit op de server met Arc-functionaliteit:

```azurecli
az extension add connectedmachine
```

Wanneer de installatie is voltooid, wordt het volgende bericht weer gegeven:

`The installed extension `connectedmachine` is experimental and not covered by customer support. Please use with discretion.`

## <a name="enable-extension"></a>Extensie inschakelen

Als u een VM-extensie op uw server met Arc wilt inschakelen, gebruikt u [AZ connectedmachine machine-extension Create](/cli/azure/ext/connectedmachine/connectedmachine/machine-extension#ext_connectedmachine_az_connectedmachine_machine_extension_create) met de `--machine-name` `--extension-name` `--location` `--type` `settings` `--publisher` para meters,,,, en.

In het volgende voor beeld wordt de extensie Log Analytics VM ingeschakeld op een Linux-server die is ingeschakeld voor Arc:

```azurecli
az connectedmachine machine-extension create --machine-name "myMachineName" --name "OmsAgentforLinux" --location "eastus" --type "CustomScriptExtension" --publisher "Microsoft.EnterpriseCloud.Monitoring" --settings "{\"workspaceId\":\"workspaceId"}" --protected-settings "{\workspaceKey\":"\workspaceKey"} --type-handler-version "1.10" --resource-group "myResourceGroup"
```

In het volgende voor beeld wordt de aangepaste script extensie ingeschakeld op een server met Arc-functionaliteit:

```azurecli
az connectedmachine machine-extension create --machine-name "myMachineName" --name "CustomScriptExtension" --location "eastus" --type "CustomScriptExtension" --publisher "Microsoft.Compute" --settings "{\"commandToExecute\":\"powershell.exe -c \\\"Get-Process | Where-Object { $_.CPU -gt 10000 }\\\"\"}" --type-handler-version "1.10" --resource-group "myResourceGroup"
```

## <a name="list-extensions-installed"></a>Lijst met geïnstalleerde uitbrei dingen

Als u een lijst wilt weer geven met de VM-extensies op de server waarop u een Arc hebt ingeschakeld, gebruikt u [AZ connectedmachine machine Extension List](/cli/azure/ext/connectedmachine/connectedmachine/machine-extension#ext_connectedmachine_az_connectedmachine_machine_extension_list) met de `--machine-name` `--resource-group` para meters en.

Voorbeeld:

```azurecli
az connectedmachine machine-extension list --machine-name "myMachineName" --resource-group "myResourceGroup"
```

De uitvoer van Azure CLI-opdrachten bevindt zich standaard in JSON (JavaScript Object Notation). Als u de standaard uitvoer wilt wijzigen in een lijst of tabel, gebruikt u bijvoorbeeld [AZ Configure--Output](/cli/azure/reference-index). U kunt ook toevoegen `--output` aan elke opdracht voor eenmalige wijziging in uitvoer indeling.

In het volgende voor beeld wordt de gedeeltelijke JSON-uitvoer van de opdracht weer gegeven `az connectedmachine machine-extension -list` :

```json
[
  {
    "autoUpgradingMinorVersion": "false",
    "forceUpdateTag": null,
    "id": "/subscriptions/subscriptionId/resourceGroups/resourceGroupName/providers/Microsoft.HybridCompute/machines/SVR01/extensions/DependencyAgentWindows",
    "location": "eastus",
    "name": "DependencyAgentWindows",
    "namePropertiesInstanceViewName": "DependencyAgentWindows",
```

## <a name="remove-an-installed-extension"></a>Een geïnstalleerde extensie verwijderen

Als u een geïnstalleerde VM-extensie wilt verwijderen van de server waarop u een Arc hebt ingeschakeld, gebruikt u [AZ connectedmachine machine extension delete](/cli/azure/ext/connectedmachine/connectedmachine/machine-extension#ext_connectedmachine_az_connectedmachine_machine_extension_delete) met de `--extension-name` `--machine-name` `--resource-group` para meters.

Als u de Log Analytics VM-extensie voor Linux bijvoorbeeld wilt verwijderen, voert u de volgende opdracht uit:

```azurecli
az connectedmachine machine-extension delete --machine-name "myMachineName" --name "OmsAgentforLinux" --resource-group "myResourceGroup"
```

## <a name="next-steps"></a>Volgende stappen

- U kunt VM-extensies implementeren, beheren en verwijderen met behulp van [Power shell](manage-vm-extensions-powershell.md), via de [Azure Portal](manage-vm-extensions-portal.md)of [Azure Resource Manager sjablonen](manage-vm-extensions-template.md).

- Informatie over probleem oplossing vindt u in de [gids voor het oplossen van problemen met VM-extensies](troubleshoot-vm-extensions.md).
