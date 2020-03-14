---
title: Key Vault instellen voor Windows-Vm's in Azure Resource Manager
description: Key Vault instellen voor gebruik met een virtuele machine van Azure Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: singhkays
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 33a483e2-cfbc-4c62-a588-5d9fd52491e2
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 01/24/2017
ms.author: kasing
ms.openlocfilehash: a64163da1dee2bceb567436dc18ba0fa5274cfcb
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79243144"
---
# <a name="set-up-key-vault-for-virtual-machines-in-azure-resource-manager"></a>Key Vault instellen voor virtuele machines in Azure Resource Manager

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-rm-include.md)]

In Azure Resource Manager stack worden geheimen/certificaten gemodelleerd als bronnen die worden verschaft door de resource provider van Key Vault. Zie [Wat is Azure Key Vault?](../../key-vault/key-vault-overview.md) voor meer informatie over Key Vault.

> [!NOTE]
> 1. Als Key Vault moet worden gebruikt met Azure Resource Manager virtuele machines, moet de eigenschap **EnabledForDeployment** op Key Vault worden ingesteld op True. U kunt dit doen in verschillende clients.
> 2. De Key Vault moet worden gemaakt in hetzelfde abonnement en dezelfde locatie als de virtuele machine.
>
>

## <a name="use-powershell-to-set-up-key-vault"></a>Power shell gebruiken voor het instellen van Key Vault
Zie [een geheim instellen en ophalen van Azure Key Vault met behulp van Power shell](../../key-vault/quick-create-powershell.md)om een sleutel kluis te maken met behulp van Power shell.

Voor nieuwe sleutel kluizen kunt u deze Power shell-cmdlet gebruiken:

    New-AzKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia' -EnabledForDeployment

Voor bestaande sleutel kluizen kunt u deze Power shell-cmdlet gebruiken:

    Set-AzKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment

## <a name="use-cli-to-set-up-key-vault"></a>CLI gebruiken om Key Vault in te stellen
Zie [Key Vault beheren met CLI](../../key-vault/key-vault-manage-with-cli2.md#create-a-key-vault)voor informatie over het maken van een sleutel kluis met behulp van de opdracht regel interface (CLI).

Voor CLI moet u de sleutel kluis maken voordat u het implementatie beleid toewijst. U gebruikt hiervoor de volgende opdracht:

    az keyvault create --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --location "EastAsia"
    
Voer vervolgens de volgende opdracht uit om Key Vault in te scha kelen voor gebruik met sjabloon implementatie:

    az keyvault update --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --enabled-for-deployment "true"

## <a name="use-templates-to-set-up-key-vault"></a>Sjablonen gebruiken om Key Vault in te stellen
Wanneer u een sjabloon gebruikt, moet u de eigenschap `enabledForDeployment` instellen op `true` voor de Key Vault resource.

    {
      "type": "Microsoft.KeyVault/vaults",
      "name": "ContosoKeyVault",
      "apiVersion": "2015-06-01",
      "location": "<location-of-key-vault>",
      "properties": {
        "enabledForDeployment": "true",
        ....
        ....
      }
    }

Zie [een sleutel kluis maken](https://azure.microsoft.com/documentation/templates/101-key-vault-create/)voor andere opties die u kunt configureren wanneer u een sleutel kluis maakt met behulp van sjablonen.
