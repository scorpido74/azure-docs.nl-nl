---
title: Een VM implementeren vanuit de Azure Marketplace
description: Hier wordt uitgelegd hoe u een virtuele machine implementeert vanaf een vooraf geconfigureerde virtuele computer van Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: dsindona
ms.openlocfilehash: 6e39f1c70cd94c14b12e54817941ea9106aacfdd
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273865"
---
# <a name="deploy-a-virtual-machine-from-the-azure-marketplace"></a>Een virtuele machine implementeren vanuit de Azure Marketplace

> [!IMPORTANT]
> Vanaf 13 april 2020 beginnen we met het verplaatsen van het beheer van uw Azure Virtual Machine-aanbiedingen naar partnercentrum. Na de migratie maak en beheer je je aanbiedingen in partnercentrum. Volg de instructies in [Azure VM-afbeeldingscertificering](https://aks.ms/CertifyVMimage) om uw gemigreerde aanbiedingen te beheren.

In dit artikel wordt uitgelegd hoe u een vooraf geconfigureerde virtuele machine (VM) implementeert vanuit een Azure Marketplace, met behulp van het meegeleverde Azure PowerShell-script.  Dit script legt ook de WinRM HTTP- en HTTPS-eindpunten op de VM bloot.  Het script vereist dat u al een certificaat hebt geüpload naar Azure Key Vault, zoals beschreven in [Certificaten maken voor Azure Key Vault.](./cpp-create-key-vault-cert.md) 

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="vm-deployment-template"></a>VM-implementatiesjabloon

De sjabloon voor de snelle start van Azure VM-implementatie is beschikbaar als het online bestand [azuredeploy.json](https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-vm-winrm-keyvault-windows/azuredeploy.json).  Het bevat de volgende parameters:

|  **Parameter**        |   **Beschrijving**                                 |
|  -------------        |   ---------------                                 |
| newStorageAccountName    | Naam van het opslagaccount                       |
| dnsNameForPublicIP    | DNS Name for the public IP. Moet kleine letters zijn.    |
| adminUserName            | Gebruikersnaam van de beheerder                          |
| adminPassword            | Beheerderswachtwoord                          |
| imagePublisher        | Afbeeldingsuitgever                                   |
| afbeeldingAangeboden            | Afbeeldingsaanbieding                                       |
| imageSKU                | Afbeelding SKU                                         |
| vmSize                | Grootte van de VM                                    |
| vmName                | Naam van de VM                                    |
| vaultName                | Naam van de sleutelkluis                             |
| vaultResourceGroup    | Resourcegroep van de sleutelkluis                   |
| certificateUrl certificateUrl certificateUrl certificateUrl        | URL voor het certificaat, inclusief versie in KeyVault, bijvoorbeeld`https://testault.vault.azure.net/secrets/testcert/b621es1db241e56a72d037479xab1r7` |
|  |  |


## <a name="deployment-script"></a>Implementatiescript

Bewerk het volgende Azure PowerShell-script en voer het uit om de opgegeven Azure Marketplace VM te implementeren.

```powershell

New-AzResourceGroupDeployment -Name "dplvm$postfix" -ResourceGroupName "$rgName" -TemplateUri "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-vm-winrm-keyvault-windows/azuredeploy.json" -newStorageAccountName "test$postfix" -dnsNameForPublicIP $vmName -adminUserName "isv" -adminPassword $pwd -vmSize "Standard_A2" -vmName $vmName -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl $objAzureKeyVaultSecret.Id 

```


## <a name="next-steps"></a>Volgende stappen

Zodra u een vooraf geconfigureerde VM hebt geïmplementeerd, u de oplossingen en services die deze bevat configureren en openen of gebruiken voor verdere ontwikkeling. 
