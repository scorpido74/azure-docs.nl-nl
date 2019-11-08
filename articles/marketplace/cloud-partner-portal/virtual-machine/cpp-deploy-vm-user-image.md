---
title: Een Azure-VM implementeren vanaf een gebruikers-VHD | Azure Marketplace
description: Hierin wordt uitgelegd hoe u een VHD-installatie kopie van een gebruiker implementeert voor het maken van een Azure VM-exemplaar.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 11/29/2018
ms.author: pabutler
ms.openlocfilehash: 8421e9b7b7e2b7d13054e977da83be044b4e6af7
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73816631"
---
# <a name="deploy-an-azure-vm-from-a-user-vhd"></a>Een Azure-VM implementeren op basis van een gebruikers-VHD

In dit artikel wordt uitgelegd hoe u een gegeneraliseerde VHD-installatie kopie implementeert om een nieuwe Azure VM-resource te maken met behulp van de meegeleverde Azure Resource Manager sjabloon en Azure PowerShell script.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="vhd-deployment-template"></a>VHD-implementatie sjabloon

Kopieer de Azure Resource Manager sjabloon voor de implementatie van de [VHD](cpp-deploy-json-template.md) naar een lokaal bestand met de naam `VHDtoImage.json`.  Bewerk dit bestand om waarden op te geven voor de volgende para meters. 

|  **Bepaalde**             |   **Beschrijving**                                                              |
|  -------------             |   ---------------                                                              |
| ResourceGroupName          | De naam van de bestaande Azure-resource groep.  Gebruik normaal gesp roken dezelfde RG die is gekoppeld aan uw sleutel kluis  |
| TemplateFile               | Volledige padnaam naar het bestand `VHDtoImage.json`                                    |
| userStorageAccountName     | De naam van het opslag account                                                    |
| sNameForPublicIP           | De DNS-naam voor het open bare IP-adres. Moet een kleine letter zijn                                  |
| subscriptionId             | Azure-abonnements-id                                                  |
| Locatie                   | Standaard geografische locatie van Azure van de resource groep                       |
| vmName                     | De naam van de virtuele machine                                                    |
| vaultName                  | De naam van de sleutel kluis                                                          |
| vaultResourceGroup         | Resource groep van de sleutel kluis
| certificateUrl             | De URL van het certificaat, inclusief de versie die is opgeslagen in de sleutel kluis, bijvoorbeeld: `https://testault.vault.azure.net/secrets/testcert/b621es1db241e56a72d037479xab1r7` |
| vhdUrl                     | URL van de virtuele harde schijf                                                   |
| vmSize                     | Grootte van het exemplaar van de virtuele machine                                           |
| publicIPAddressName        | Naam van het open bare IP-adres                                                  |
| virtualNetworkName         | De naam van het virtuele netwerk                                                    |
| nicName                    | De naam van de netwerk interface kaart voor het virtuele netwerk                     |
| adminUserName              | Gebruikers naam van het beheerders account                                          |
| adminPassword              | Beheerders wachtwoord                                                          |
|  |  |


## <a name="powershell-script"></a>Power shell-script

Kopieer en bewerk het volgende script om waarden op te geven voor de variabelen `$storageaccount` en `$vhdUrl`.  Voer deze uit om een Azure VM-resource te maken op basis van uw bestaande gegeneraliseerde VHD.

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

Nadat uw virtuele machine is geïmplementeerd, bent u klaar om [uw VM-installatie kopie te certificeren](./cpp-certify-vm.md).
