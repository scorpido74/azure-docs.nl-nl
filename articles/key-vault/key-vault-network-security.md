---
title: Azure Key Vault firewalls en virtuele netwerken configureren-Azure Key Vault
description: Stapsgewijze instructies voor het configureren van Key Vault firewalls en virtuele netwerken
services: key-vault
author: amitbapat
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: ambapat
ms.openlocfilehash: 8f483f8d383da1f9ba05eb172db185bec9406c7e
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/29/2020
ms.locfileid: "78195155"
---
# <a name="configure-azure-key-vault-firewalls-and-virtual-networks"></a>Azure Key Vault firewalls en virtuele netwerken configureren

In dit artikel vindt u stapsgewijze instructies voor het configureren van Azure Key Vault firewalls en virtuele netwerken om de toegang tot uw sleutel kluis te beperken. Met de [service-eind punten voor virtuele netwerken voor Key Vault](key-vault-overview-vnet-service-endpoints.md) kunt u de toegang beperken tot een opgegeven virtueel netwerk en een set van IPv4-adresbereiken (Internet Protocol versie 4).

> [!IMPORTANT]
> Nadat de firewall regels van kracht zijn, kunnen gebruikers alleen Key Vault [gegevensvlak](../key-vault/key-vault-secure-your-key-vault.md#data-plane-access-control) bewerkingen uitvoeren wanneer hun aanvragen afkomstig zijn van toegestane virtuele netwerken of IPv4-adresbereiken. Dit geldt ook voor toegang tot Key Vault vanuit de Azure Portal. Hoewel gebruikers kunnen bladeren naar een sleutel kluis van de Azure Portal, kunnen ze mogelijk geen sleutels, geheimen of certificaten weer geven als hun client computer niet in de lijst met toegestane clients staat. Dit is ook van invloed op de Key Vault kiezer door andere Azure-Services. Gebruikers kunnen mogelijk een lijst met sleutel kluizen zien, maar geen lijst met sleutels als firewall regels hun client computer verhinderen.

## <a name="use-the-azure-portal"></a>Azure Portal gebruiken

U kunt als volgt Key Vault firewalls en virtuele netwerken configureren met behulp van de Azure Portal:

1. Blader naar de sleutel kluis die u wilt beveiligen.
2. Selecteer **netwerken**en selecteer vervolgens het tabblad **firewalls en virtuele netwerken** .
3. Selecteer onder **toegang toestaan vanuit**de optie **geselecteerde netwerken**.
4. Als u bestaande virtuele netwerken wilt toevoegen aan firewalls en regels voor virtuele netwerken, selecteert u **+ bestaande virtuele netwerken toevoegen**.
5. Selecteer op de nieuwe blade die wordt geopend, het abonnement, de virtuele netwerken en de subnetten die u toegang wilt geven tot deze sleutel kluis. Als voor de virtuele netwerken en subnetten die u selecteert, geen service-eind punten zijn ingeschakeld, bevestigt u dat u service-eind punten wilt inschakelen en selecteert u **inschakelen**. Het kan tot vijf tien minuten duren voordat deze is doorgevoerd.
6. Voeg onder **IP-netwerken**IPv4-adresbereiken toe door IPv4-adresbereiken te typen in [CIDR (Klasseloze route ring tussen domeinen)](https://tools.ietf.org/html/rfc4632) of afzonderlijke IP-adressen.
7. Selecteer **Opslaan**.

U kunt ook nieuwe virtuele netwerken en subnetten toevoegen en vervolgens service-eind punten inschakelen voor de zojuist gemaakte virtuele netwerken en subnetten door **+ nieuw virtueel netwerk toevoegen**te selecteren. Volg vervolgens de aanwijzingen.

## <a name="use-the-azure-cli"></a>Azure CLI gebruiken 

U kunt als volgt Key Vault firewalls en virtuele netwerken configureren met behulp van de Azure CLI

1. [Installeer Azure cli](https://docs.microsoft.com/cli/azure/install-azure-cli) en [Meld u aan](https://docs.microsoft.com/cli/azure/authenticate-azure-cli).

2. Beschik bare regels voor virtuele netwerken weer geven. Als u geen regels voor deze sleutel kluis hebt ingesteld, is de lijst leeg.
   ```azurecli
   az keyvault network-rule list --resource-group myresourcegroup --name mykeyvault
   ```

3. Schakel een service-eind punt in voor Key Vault op een bestaand virtueel netwerk en subnet.
   ```azurecli
   az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.KeyVault"
   ```

4. Voeg een regel voor een virtueel netwerk en subnet toe.
   ```azurecli
   subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
   az keyvault network-rule add --resource-group "demo9311" --name "demo9311premium" --subnet $subnetid
   ```

5. Een IP-adres bereik toevoegen waaruit verkeer kan worden toegestaan.
   ```azurecli
   az keyvault network-rule add --resource-group "myresourcegroup" --name "mykeyvault" --ip-address "191.10.18.0/24"
   ```

6. Als deze sleutel kluis toegankelijk moet zijn voor vertrouwde services, stelt u `bypass` in op `AzureServices`.
   ```azurecli
   az keyvault update --resource-group "myresourcegroup" --name "mykeyvault" --bypass AzureServices
   ```

7. Schakel de netwerk regels in door de standaard actie in te stellen op `Deny`.
   ```azurecli
   az keyvault update --resource-group "myresourcegroup" --name "mekeyvault" --default-action Deny
   ```

## <a name="use-azure-powershell"></a>Azure PowerShell gebruiken

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

U kunt als volgt Key Vault firewalls en virtuele netwerken configureren met behulp van Power shell:

1. Installeer de nieuwste [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps)en [Meld](https://docs.microsoft.com/powershell/azure/authenticate-azureps)u aan.

2. Beschik bare regels voor virtuele netwerken weer geven. Als u geen regels voor deze sleutel kluis hebt ingesteld, is de lijst leeg.
   ```powershell
   (Get-AzKeyVault -VaultName "mykeyvault").NetworkAcls
   ```

3. Service-eind punt inschakelen voor Key Vault op een bestaand virtueel netwerk en subnet.
   ```powershell
   Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" -AddressPrefix "10.1.1.0/24" -ServiceEndpoint "Microsoft.KeyVault" | Set-AzVirtualNetwork
   ```

4. Voeg een regel voor een virtueel netwerk en subnet toe.
   ```powershell
   $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
   Add-AzKeyVaultNetworkRule -VaultName "mykeyvault" -VirtualNetworkResourceId $subnet.Id
   ```

5. Een IP-adres bereik toevoegen waaruit verkeer kan worden toegestaan.
   ```powershell
   Add-AzKeyVaultNetworkRule -VaultName "mykeyvault" -IpAddressRange "16.17.18.0/24"
   ```

6. Als deze sleutel kluis toegankelijk moet zijn voor vertrouwde services, stelt u `bypass` in op `AzureServices`.
   ```powershell
   Update-AzKeyVaultNetworkRuleSet -VaultName "mykeyvault" -Bypass AzureServices
   ```

7. Schakel de netwerk regels in door de standaard actie in te stellen op `Deny`.
   ```powershell
   Update-AzKeyVaultNetworkRuleSet -VaultName "mykeyvault" -DefaultAction Deny
   ```

## <a name="references"></a>Verwijzingen

* Azure CLI-opdrachten: [AZ-inkluis netwerk-regel](https://docs.microsoft.com/cli/azure/keyvault/network-rule?view=azure-cli-latest)
* Azure PowerShell-cmdlets: [Get-AzKeyVault](https://docs.microsoft.com/powershell/module/az.keyvault/get-azkeyvault), [add-AzKeyVaultNetworkRule](https://docs.microsoft.com/powershell/module/az.KeyVault/Add-azKeyVaultNetworkRule), [Remove-AzKeyVaultNetworkRule](https://docs.microsoft.com/powershell/module/az.KeyVault/Remove-azKeyVaultNetworkRule), [Update-AzKeyVaultNetworkRuleSet](https://docs.microsoft.com/powershell/module/az.KeyVault/Update-azKeyVaultNetworkRuleSet)

## <a name="next-steps"></a>Volgende stappen

* [Virtuele netwerk service-eind punten voor Key Vault](key-vault-overview-vnet-service-endpoints.md)
* [Uw Key Vault beveiligen](key-vault-secure-your-key-vault.md)
