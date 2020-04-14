---
title: Een Azure VM implementeren van een vhd-gebruiker | Azure Marketplace
description: Hier wordt uitgelegd hoe u een VHD-afbeelding van een gebruiker implementeert om een Azure VM-exemplaar te maken.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: dsindona
ms.openlocfilehash: 79754b4ce7c3dfe2a5c549f4a39ef3160be423d8
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273882"
---
# <a name="deploy-an-azure-vm-from-a-user-vhd"></a>Een Azure VM implementeren van een vhd-gebruiker

> [!IMPORTANT]
> Vanaf 13 april 2020 beginnen we met het verplaatsen van het beheer van uw Azure Virtual Machine-aanbiedingen naar partnercentrum. Na de migratie maak en beheer je je aanbiedingen in partnercentrum. Volg de instructies in [Azure VM-afbeeldingscertificering](https://aks.ms/CertifyVMimage) om uw gemigreerde aanbiedingen te beheren.

In dit artikel wordt uitgelegd hoe u een gegeneraliseerde VHD-afbeelding implementeert om een nieuwe Azure VM-bron te maken, met behulp van de meegeleverde Azure Resource Manager-sjabloon en Azure PowerShell-script.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="vhd-deployment-template"></a>VHD-implementatiesjabloon

Kopieer de azure resource manager-sjabloon voor [VHD-implementatie](cpp-deploy-json-template.md) naar een lokaal bestand met de naam `VHDtoImage.json`.  Bewerk dit bestand om waarden voor de volgende parameters op te geven. 

|  **Parameter**             |   **Beschrijving**                                                              |
|  -------------             |   ---------------                                                              |
| ResourceGroupName          | Bestaande naam van azure-bronnengroep.  Gebruik meestal dezelfde RG die is gekoppeld aan uw sleutelkluis  |
| Sjabloonbestand               | Volledige pathname naar het bestand`VHDtoImage.json`                                    |
| userStorageAccountName     | Naam van het opslagaccount                                                    |
| sNameForPublicIP           | DNS name for the public IP. Moet kleine letters                                  |
| subscriptionId             | Azure-abonnements-id                                                  |
| Locatie                   | Standaard Azure geografische locatie van de resourcegroep                       |
| vmName                     | Naam van de virtuele machine                                                    |
| vaultName                  | Naam van de sleutelkluis                                                          |
| vaultResourceGroup         | Resourcegroep van de sleutelkluis
| certificateUrl certificateUrl certificateUrl certificateUrl             | Url van het certificaat, inclusief versie die is opgeslagen in de sleutelkluis, bijvoorbeeld:`https://testault.vault.azure.net/secrets/testcert/b621es1db241e56a72d037479xab1r7` |
| vhdUrl                     | URL van de virtuele harde schijf                                                   |
| vmSize                     | Grootte van de virtuele machine-instantie                                           |
| publicIPAddressName        | Naam van het openbare IP-adres                                                  |
| virtualNetworkName         | Naam van het virtuele netwerk                                                    |
| nicNaam                    | Naam van de netwerkinterfacekaart voor het virtuele netwerk                     |
| adminUserName              | Gebruikersnaam van het beheerdersaccount                                          |
| adminPassword              | Beheerderswachtwoord                                                          |
|  |  |


## <a name="powershell-script"></a>Powershell-script

Kopieer en bewerk het volgende script `$storageaccount` `$vhdUrl` om waarden voor de en variabelen te leveren.  Voer deze uit om een Azure VM-bron te maken op basis van uw bestaande gegeneraliseerde VHD.

```powershell
# storage account of existing generalized VHD 
$storageaccount = "testwinrm11815"

# generalized VHD URL
$vhdUrl = "https://testwinrm11815.blob.core.windows.net/vhds/testvm1234562016651857.vhd"

echo "New-AzResourceGroupDeployment -Name "dplisvvm$postfix" -ResourceGroupName "$rgName" -TemplateFile "C:\certLocation\VHDtoImage.json" -userStorageAccountName "$storageaccount" -dnsNameForPublicIP "$vmName" -subscriptionId "$mysubid" -location "$location" -vmName "$vmName" -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl $objAzureKeyVaultSecret.Id  -vhdUrl "$vhdUrl" -vmSize "Standard_A2" -publicIPAddressName "myPublicIP1" -virtualNetworkName "myVNET1" -nicName "myNIC1" -adminUserName "isv" -adminPassword $pwd"

#deploying VM with existing VHD
New-AzResourceGroupDeployment -Name "dplisvvm$postfix" -ResourceGroupName "$rgName" -TemplateFile "C:\certLocation\VHDtoImage.json" -userStorageAccountName "$storageaccount" -dnsNameForPublicIP "$vmName" -subscriptionId "$mysubid" -location "$location" -vmName "$vmName" -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl $objAzureKeyVaultSecret.Id  -vhdUrl "$vhdUrl" -vmSize "Standard_A2" -publicIPAddressName "myPublicIP1" -virtualNetworkName "myVNET1" -nicName "myNIC1" -adminUserName "isv" -adminPassword $pwd 

```

## <a name="next-steps"></a>Volgende stappen

Nadat uw VM is geïmplementeerd, bent u klaar om [uw VM-afbeelding](./cpp-certify-vm.md)te certificeren.
