---
title: Create an Azure Blockchain Service member - Azure CLI
description: Create an Azure Blockchain Service member for a blockchain consortium using the Azure CLI.
ms.date: 11/20/2019
ms.topic: quickstart
ms.reviewer: janders
ms.openlocfilehash: ecfe593e00d0b645ad8b73c6fc2aee23ae4aa2b6
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74325245"
---
# <a name="quickstart-create-an-azure-blockchain-service-blockchain-member-using-azure-cli"></a>Quickstart: Create an Azure Blockchain Service blockchain member using Azure CLI

In this quickstart, you deploy a new blockchain member and consortium in Azure Blockchain Service using Azure CLI.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell starten

Azure Cloud Shell is een gratis interactieve shell waarmee u de stappen in dit artikel kunt uitvoeren. In deze shell zijn algemene Azure-hulpprogramma's vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account.

Als u Cloud Shell wilt openen, selecteert u **Proberen** in de rechterbovenhoek van een codeblok. U kunt Cloud Shell ook openen in een afzonderlijk browsertabblad door naar [https://shell.azure.com/bash](https://shell.azure.com/bash) te gaan. Klik op **Kopiëren** om de codeblokken te kopiëren, plak deze in Cloud Shell en druk vervolgens op Enter om de code uit te voeren.

If you prefer to install and use the CLI locally, this quickstart requires Azure CLI version 2.0.51 or later. Voer `az --version` uit om de versie te bekijken. If you need to install or upgrade, see [install Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Een resourcegroep maken met de opdracht [az group create](https://docs.microsoft.com/cli/azure/group). Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* gemaakt op de locatie *eastus*:

```azurecli-interactive
az group create \
                 --name myResourceGroup \
                 --location westus2
```

## <a name="create-a-blockchain-member"></a>Een blockchain-lid maken

Create a blockchain member in Azure Blockchain Service that runs the Quorum ledger protocol in a new consortium. There are several parameters and properties you need to pass. Replace the example parameters with your values.

```azurecli-interactive
az resource create \
                    --resource-group myResourceGroup \
                    --name myblockchainmember \
                    --resource-type Microsoft.Blockchain/blockchainMembers \
                    --is-full-object \
                    --properties '{"location":"westus2", "properties":{"password":"strongMemberAccountPassword@1", "protocol":"Quorum", "consortium":"myConsortiumName", "consortiumManagementAccountPassword":"strongConsortiumManagementPassword@1"}, "sku":{"name":"S0"}}'
```

| Parameter | Beschrijving |
|---------|-------------|
| **resource-group** | Resource group name where Azure Blockchain Service resources are created. Use the resource group you created in the previous section.
| **name** | A unique name that identifies your Azure Blockchain Service blockchain member. The name is used for the public endpoint address. Bijvoorbeeld `myblockchainmember.blockchain.azure.com`.
| **location** | Azure region where the blockchain member is created. Bijvoorbeeld `westus2`. Kies de locatie die zich het dichtst bij uw gebruikers of uw andere Azure-toepassingen bevindt.
| **password** | The password for the member's default transaction node. Use the password for basic authentication when connecting to blockchain member's default transaction node public endpoint.
| **consortium** | Name of the consortium to join or create.
| **consortiumAccountPassword** | The consortium account password is also known as the member account password. The member account password is used to encrypt the private key for the Ethereum account that is created for your member. You use the member account and member account password for consortium management.
| **skuName** | Tier type. Use S0 for Standard and B0 for Basic.

It takes about 10 minutes to create the blockchain member and supporting resources.

## <a name="clean-up-resources"></a>Resources opschonen

You can use the blockchain member you created for the next quickstart or tutorial. When no longer needed, you can delete the resources by deleting the `myResourceGroup` resource group you created by the Azure Blockchain Service.

Run the following command to remove the resource group and all related resources.

```azurecli-interactive
az group delete \
                 --name myResourceGroup \
                 --yes
```

## <a name="next-steps"></a>Volgende stappen

In this quickstart, you deployed an Azure Blockchain Service member and a new consortium. Try the next quickstart to use  Azure Blockchain Development Kit for Ethereum to attach to a consortium on Azure Blockchain Service.

> [!div class="nextstepaction"]
> [Use Visual Studio Code to connect to an Azure Blockchain Service consortium network](connect-vscode.md)
