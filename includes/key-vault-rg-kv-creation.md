---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: 2917854da679816796a7f7748c3877c062995168
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/05/2020
ms.locfileid: "87512793"
---
In deze quickstart wordt gebruikgemaakt van een vooraf gemaakte Azure-sleutelkluis. U kunt een sleutelkluis maken met behulp van de stappen in de [quickstart voor Azure CLI](/azure/key-vault/general/quick-create-cli), de [quickstart voor Azure PowerShell](/azure/key-vault/general/quick-create-powershell) of de [quickstart voor de Azure-portal](/azure/key-vault/general/quick-create-portal). 

U kunt ook de onderstaande Azure CLI- of Azure PowerShell-opdrachten uitvoeren.

> [!Important]
> Elke sleutelkluis moet een unieke naam hebben. Vervang <your-unique-keyvault-name> door de naam van uw sleutelkluis in de volgende voorbeelden.

```azurecli
az group create --name "myResourceGroup" -l "EastUS"

az keyvault create --name "<your-unique-keyvault-name>" -g "myResourceGroup"
```

```azurepowershell
New-AzResourceGroup -Name myResourceGroup -Location EastUS

New-AzKeyVault -Name "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup" -Location "EastUS"
```