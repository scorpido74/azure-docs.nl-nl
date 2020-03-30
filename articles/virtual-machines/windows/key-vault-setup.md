---
title: Key Vault instellen voor Windows VM's in Azure Resource Manager
description: Key Vault instellen voor gebruik met een virtuele azure resource manager.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79243144"
---
# <a name="set-up-key-vault-for-virtual-machines-in-azure-resource-manager"></a>Key Vault instellen voor virtuele machines in Azure Resource Manager

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-rm-include.md)]

In de stapel Azure Resource Manager worden geheimen/certificaten gemodelleerd als resources die worden geleverd door de resourceprovider van Key Vault. Zie Wat is Azure [Key Vault voor](../../key-vault/key-vault-overview.md) meer informatie over Key Vault?

> [!NOTE]
> 1. Als Key Vault kan worden gebruikt met virtuele azure resource-machines, moet de eigenschap **EnabledForDeployment** op Key Vault worden ingesteld op true. U dit doen bij verschillende klanten.
> 2. De Key Vault moet worden gemaakt in hetzelfde abonnement en dezelfde locatie als de Virtual Machine.
>
>

## <a name="use-powershell-to-set-up-key-vault"></a>PowerShell gebruiken om Key Vault in te stellen
Zie [Een geheim uit Azure Key Vault instellen en ophalen met PowerShell](../../key-vault/quick-create-powershell.md)als u een sleutelkluis wilt maken met PowerShell.

Voor nieuwe sleutelkluizen u deze PowerShell-cmdlet gebruiken:

    New-AzKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia' -EnabledForDeployment

Voor bestaande sleutelkluizen u deze PowerShell-cmdlet gebruiken:

    Set-AzKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment

## <a name="use-cli-to-set-up-key-vault"></a>CLI gebruiken om Key Vault in te stellen
Zie [Sleutelkluis beheren met CLI](../../key-vault/key-vault-manage-with-cli2.md#create-a-key-vault)als u een sleutelkluis wilt maken met behulp van de command-line interface (CLI).

Voor CLI moet u de sleutelkluis maken voordat u het implementatiebeleid toewijst. U gebruikt hiervoor de volgende opdracht:

    az keyvault create --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --location "EastAsia"
    
Voer vervolgens de volgende opdracht uit om Key Vault in te schakelen voor gebruik met sjabloonimplementatie:

    az keyvault update --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --enabled-for-deployment "true"

## <a name="use-templates-to-set-up-key-vault"></a>Sjablonen gebruiken om Key Vault in te stellen
Terwijl u een sjabloon gebruikt, `enabledForDeployment` moet `true` u de eigenschap instellen op voor de Key Vault-bron.

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

Zie [Een sleutelkluis maken](https://azure.microsoft.com/documentation/templates/101-key-vault-create/)voor andere opties die u configureren wanneer u een sleutelkluis maakt met behulp van sjablonen.
