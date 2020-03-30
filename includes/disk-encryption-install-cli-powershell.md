---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: msmbaldwin
ms.service: virtual-machines
ms.topic: include
ms.date: 10/06/2019
ms.author: mbaldwin
ms.custom: include file
ms.openlocfilehash: 05794a046fdcb15a91145a75717a6a454d15a8da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "72511465"
---
Azure Disk Encryption kan worden ingeschakeld en beheerd via de [Azure CLI](/cli/azure) en [Azure PowerShell.](/powershell/azure/new-azureps-module-az) Hiervoor moet u de hulpprogramma's lokaal installeren en verbinding maken met uw Azure-abonnement.

### <a name="azure-cli"></a>Azure-CLI

De [Azure CLI 2.0](/cli/azure) is een opdrachtregelhulpmiddel voor het beheer van Azure-resources. De CLI is ontworpen om gegevens flexibel op te vragen, langdurige bewerkingen te ondersteunen als niet-blokkerende processen en scripting eenvoudig te maken. U het lokaal installeren door de stappen in [Azure CLI installeren](/cli/azure/install-azure-cli?view=azure-cli-latest)te volgen.

Als [u zich wilt aanmelden bij uw Azure-account met de Azure CLI,](/cli/azure/authenticate-azure-cli)gebruikt u de opdracht [AZ-aanmelding.](/cli/azure/reference-index?view=azure-cli-latest#az-login)

```azurecli
az login
```

Als u een tenant wilt selecteren om zich onder aan te melden, gebruikt u het als volgt:
    
```azurecli
az login --tenant <tenant>
```

Als je meerdere abonnementen hebt en een specifieke wilt opgeven, kun je je abonnementslijst met de lijst met [AZ-accounten](/cli/azure/account#az-account-list) opvragen en opgeven met [de AZ-accountset](/cli/azure/account#az-account-set).
     
```azurecli
az account list
az account set --subscription "<subscription name or ID>"
```

Zie [Aan de slag met Azure CLI 2.0 voor](/cli/azure/get-started-with-azure-cli)meer informatie. 

### <a name="azure-powershell"></a>Azure PowerShell
De [Azure PowerShell az-module](/powershell/azure/new-azureps-module-az) biedt een set cmdlets waarmee het [Azure Resource Manager-model](/azure/azure-resource-manager/resource-group-overview) wordt gebruikt voor het beheer van uw Azure-resources. U het in uw browser gebruiken met [Azure Cloud Shell,](/azure/cloud-shell/overview)of u het installeren op uw lokale machine met behulp van de instructies in [Installeer de Azure PowerShell-module.](/powershell/azure/install-az-ps) 

Als u het al lokaal hebt geïnstalleerd, controleert u of u de nieuwste versie van azure PowerShell SDK-versie gebruikt om Azure Disk Encryption te configureren. Download de nieuwste versie van [Azure PowerShell-release.](https://github.com/Azure/azure-powershell/releases)

Als [u zich wilt aanmelden bij uw Azure-account met Azure PowerShell,](/powershell/azure/authenticate-azureps?view=azps-2.5.0)gebruikt u de cmdlet [Connect-AzAccount.](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0)

```powershell
Connect-AzAccount
```

Als u meerdere abonnementen hebt en er een wilt opgeven, gebruikt u de cmdlet [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription) om ze weer te geven, gevolgd door de cmdlet [Set-AzContext:](/powershell/module/az.accounts/set-azcontext?view=azps-2.5.0)

```powershell
Set-AzContext -Subscription -Subscription <SubscriptionId>
```

Als u de [cmdlet Get-AzContext](/powershell/module/Az.Accounts/Get-AzContext) uitvoert, wordt gecontroleerd of het juiste abonnement is geselecteerd.

Als u wilt controleren of de cmdlets azure-schijfversleuteling zijn geïnstalleerd, gebruikt u de cmdlet [Get-command:](/powershell/module/microsoft.powershell.core/get-command?view=powershell-6)
     
```powershell
Get-command *diskencryption*
```
Zie [Aan de slag met Azure PowerShell](/powershell/azure/get-started-azureps)voor meer informatie. 