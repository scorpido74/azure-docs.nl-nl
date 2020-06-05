---
title: Een Azure notification hub maken met behulp van een Azure Resource Manager-sjabloon
description: Leer een Azure notification hub maken met behulp van een Azure Resource Manager-sjabloon.
services: notification-hubs
author: sethmanheim
ms.service: notification-hubs
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: sethm
ms.date: 05/15/2020
ms.reviewer: thsomasu
ms.lastreviewed: 05/15/2020
ms.openlocfilehash: aefccb831fe35898962893a173c5bd1125877def
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83743535"
---
# <a name="quickstart-create-a-notification-hub-using-an-azure-resource-manager-template"></a>Quickstart: een notification hub maken met behulp van een Azure Resource Manager-sjabloon

Azure Notification Hubs biedt een eenvoudig te gebruiken en uitgeschaalde push-engine waarmee u vanuit elke back-end (cloud of on-premises) meldingen kunt verzenden naar ieder platform (iOS, Android, Windows, Kindle, enzovoort). Zie voor meer informatie over de service [Wat is Azure Notification Hubs?](notification-hubs-push-notification-overview.md).

In deze quickstart wordt gebruikgemaakt van een Azure Resource Manager-sjabloon om een Azure Notification Hubs-naamruimte en een notification hub met de naam 'MyHub' binnen die naamruimte te maken.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Geen.

## <a name="create-a-notification-hubs-namespace-and-hub"></a>Een Notification Hubs-naamruimte en -hub maken

<!-- The second H2 must start with "Create a". For example,  'Create a Key Vault', 'Create a virtual machine', etc. -->

### <a name="review-the-template"></a>De sjabloon controleren

De sjabloon die in deze quickstart wordt gebruikt, komt uit [Azure Quick Start-sjablonen](https://azure.microsoft.com/resources/templates/101-notification-hub/).

:::code language="json" source="~/quickstart-templates/101-notification-hub/azuredeploy.json" range="1-45" highlight="22-40":::

* [Microsoft.NotificationHubs/namespaces](/azure/templates/microsoft.notificationhubs/2017-04-01/namespaces)
* [Microsoft.NotificationHubs/namespaces/notificationHubs](/azure/templates/microsoft.notificationhubs/2017-04-01/namespaces/notificationhubs)

## <a name="deploy-the-template"></a>De sjabloon implementeren

Selecteer de volgende afbeelding om u aan te melden bij Azure en een sjabloon te openen. De sjabloon gebruikt de naam van een Notification Hubs-naamruimte als parameter. De sjabloon maakt vervolgens een naamruimte met die naam en een notification hub met de naam **MyHub** binnen die naamruimte.

[![Implementeren in Azure](./media/create-notification-hub-template/deploy-to-azure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-notification-hub%2Fazuredeploy.json)

## <a name="review-deployed-resources"></a>Geïmplementeerde resources bekijken

U kunt de Azure-portal gebruiken om de geïmplementeerde resources te controleren, of Azure CLI of een Azure PowerShell-script gebruiken om de geïmplementeerde Notification Hubs-naamruimte en -hub weer te geven:

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
Get-AzNotificationHub -Namespace "nhtestns123" -ResourceGroup "ContosoNotificationsGroup"
Get-AzNotificationHubsNamespace -Namespace "nhtestns123"
```

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
az notification-hub show --resource-group ContosoNotificationsGroup --namespace-name nhtestns123 --name MyHub
az notification-hub namespace show --resource-group ContosoNotificationsGroup --name nhtestns123
```

---
The output looks similar to: null
':::image type="content" source="media/create-notification-hub-template/verify-deploy.png" alt-text="Verify deployment"::': null
---

## <a name="clean-up-resources"></a>Resources opschonen

Als u de resourcegroep niet meer nodig hebt, verwijdert u deze. Hierdoor worden ook de resources in de resourcegroep verwijderd.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

## <a name="next-steps"></a>Volgende stappen

Zie voor een stapsgewijze zelfstudie die u door het proces van het maken van een sjabloon leidt:

> [!div class="nextstepaction"]
> [Zelfstudie: Uw eerste Azure Resource Manager-sjabloon maken en implementeren](/azure/azure-resource-manager/templates/template-tutorial-create-first-template)
