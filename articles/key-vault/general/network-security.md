---
title: Azure Key Vault-firewalls en virtuele netwerken configureren - Azure Key Vault
description: Stapsgewijze instructies voor het configureren van Key Vault-firewalls en virtuele netwerken
services: key-vault
author: amitbapat
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: ambapat
ms.openlocfilehash: 93fb718c28d015162d3c43d0936bbf4de9cad2fe
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81422576"
---
# <a name="configure-azure-key-vault-firewalls-and-virtual-networks"></a>Azure Key Vault-firewalls en virtuele netwerken configureren

In dit artikel worden stapsgewijze instructies gegeven om Azure Key Vault-firewalls en virtuele netwerken te configureren om de toegang tot uw sleutelkluis te beperken. Met de [eindpunten voor de virtuele netwerkservice voor Key Vault](overview-vnet-service-endpoints.md)u de toegang tot een bepaald virtueel netwerk en de set IPv4-adresbereiken (internetprotocol versie 4) beperken.

> [!IMPORTANT]
> Nadat firewallregels van kracht zijn, kunnen gebruikers alleen key [vault-gegevensvlakbewerkingen](secure-your-key-vault.md#data-plane-access-control) uitvoeren wanneer hun aanvragen afkomstig zijn van toegestane virtuele netwerken of IPv4-adresbereiken. Dit geldt ook voor toegang tot Key Vault vanuit de Azure-portal. Hoewel gebruikers vanuit de Azure-portal naar een sleutelkluis kunnen bladeren, kunnen ze mogelijk geen sleutels, geheimen of certificaten vermelden als hun clientmachine niet in de toegestane lijst staat. Dit heeft ook gevolgen voor de Key Vault Picker van andere Azure-services. Gebruikers kunnen mogelijk een lijst met sleutelkluizen zien, maar geen lijstsleutels, als firewallregels hun clientmachine verhinderen.

## <a name="use-the-azure-portal"></a>Azure Portal gebruiken

U key vault-firewalls en virtuele netwerken als u deze configureren met behulp van de Azure-portal:

1. Blader naar de sleutelkluis die u wilt beveiligen.
2. Selecteer **Netwerken**en selecteer vervolgens het tabblad **Firewalls en virtuele netwerken.**
3. Selecteer geselecteerde netwerken **onder Toegang toestaan vanuit**selecteer Geselecteerde **netwerken**.
4. Als u bestaande virtuele netwerken wilt toevoegen aan firewalls en virtuele netwerkregels, selecteert u **+ Bestaande virtuele netwerken toevoegen.**
5. Selecteer in het nieuwe blad dat wordt geopend het abonnement, virtuele netwerken en subnetten waarmee u toegang wilt verlenen tot deze sleutelkluis. Als de virtuele netwerken en subnetten die u selecteert, geen serviceeindpunten hebben ingeschakeld, bevestigt u dat u serviceeindpunten wilt inschakelen en selecteert u **Inschakelen**. Het kan tot 15 minuten duren voordat deze van kracht wordt.
6. Voeg onder **IP-netwerken**IPv4-adresbereiken toe door IPv4-adresbereiken te typen in [CIDR-notatie (Classless Inter-domain Routing)](https://tools.ietf.org/html/rfc4632) of afzonderlijke IP-adressen.
7. Selecteer **Opslaan**.

U ook nieuwe virtuele netwerken en subnetten toevoegen en serviceeindpunten voor de nieuw gemaakte virtuele netwerken en subnetten inschakelen door **nieuw virtueel netwerk toevoegen te**selecteren. Volg dan de aanwijzingen.

## <a name="use-the-azure-cli"></a>Azure CLI gebruiken 

Zo configureer je Key Vault-firewalls en virtuele netwerken met behulp van de Azure CLI

1. [Installeer Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) en [meld u aan](https://docs.microsoft.com/cli/azure/authenticate-azure-cli).

2. Lijst beschikbare virtuele netwerkregels. Als u geen regels hebt ingesteld voor deze sleutelkluis, is de lijst leeg.
   ```azurecli
   az keyvault network-rule list --resource-group myresourcegroup --name mykeyvault
   ```

3. Schakel een serviceeindpunt in voor Key Vault op een bestaand virtueel netwerk en subnet.
   ```azurecli
   az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.KeyVault"
   ```

4. Voeg een netwerkregel toe voor een virtueel netwerk en subnet.
   ```azurecli
   subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
   az keyvault network-rule add --resource-group "demo9311" --name "demo9311premium" --subnet $subnetid
   ```

5. Voeg een IP-adresbereik toe van waaruit verkeer mogelijk is.
   ```azurecli
   az keyvault network-rule add --resource-group "myresourcegroup" --name "mykeyvault" --ip-address "191.10.18.0/24"
   ```

6. Als deze sleutelkluis toegankelijk moet zijn `bypass` voor `AzureServices`vertrouwde services, stel u in op .
   ```azurecli
   az keyvault update --resource-group "myresourcegroup" --name "mykeyvault" --bypass AzureServices
   ```

7. Schakel de netwerkregels in door `Deny`de standaardactie in te stellen op .
   ```azurecli
   az keyvault update --resource-group "myresourcegroup" --name "mekeyvault" --default-action Deny
   ```

## <a name="use-azure-powershell"></a>Azure PowerShell gebruiken

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

U key vault-firewalls en virtuele netwerken als een opdracht configureren met PowerShell:

1. Installeer de nieuwste [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps)en [meld u aan](https://docs.microsoft.com/powershell/azure/authenticate-azureps).

2. Lijst beschikbare virtuele netwerkregels. Als u geen regels hebt ingesteld voor deze sleutelkluis, is de lijst leeg.
   ```powershell
   (Get-AzKeyVault -VaultName "mykeyvault").NetworkAcls
   ```

3. Serviceeindpunt voor Key Vault inschakelen op een bestaand virtueel netwerk en subnet.
   ```powershell
   Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" -AddressPrefix "10.1.1.0/24" -ServiceEndpoint "Microsoft.KeyVault" | Set-AzVirtualNetwork
   ```

4. Voeg een netwerkregel toe voor een virtueel netwerk en subnet.
   ```powershell
   $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
   Add-AzKeyVaultNetworkRule -VaultName "mykeyvault" -VirtualNetworkResourceId $subnet.Id
   ```

5. Voeg een IP-adresbereik toe van waaruit verkeer mogelijk is.
   ```powershell
   Add-AzKeyVaultNetworkRule -VaultName "mykeyvault" -IpAddressRange "16.17.18.0/24"
   ```

6. Als deze sleutelkluis toegankelijk moet zijn `bypass` voor `AzureServices`vertrouwde services, stel u in op .
   ```powershell
   Update-AzKeyVaultNetworkRuleSet -VaultName "mykeyvault" -Bypass AzureServices
   ```

7. Schakel de netwerkregels in door `Deny`de standaardactie in te stellen op .
   ```powershell
   Update-AzKeyVaultNetworkRuleSet -VaultName "mykeyvault" -DefaultAction Deny
   ```

## <a name="references"></a>Verwijzingen

* Azure [CLI-opdrachten: az keyvault-netwerkregel](https://docs.microsoft.com/cli/azure/keyvault/network-rule?view=azure-cli-latest)
* Azure PowerShell-cmdlets: [Get-AzKeyVault](https://docs.microsoft.com/powershell/module/az.keyvault/get-azkeyvault), [Add-AzKeyVaultNetworkRule](https://docs.microsoft.com/powershell/module/az.KeyVault/Add-azKeyVaultNetworkRule), [Remove-AzKeyVaultNetworkRule](https://docs.microsoft.com/powershell/module/az.KeyVault/Remove-azKeyVaultNetworkRule), [Update-AzKeyVaultNetworkRuleSet](https://docs.microsoft.com/powershell/module/az.KeyVault/Update-azKeyVaultNetworkRuleSet)

## <a name="next-steps"></a>Volgende stappen

* [Eindpunten voor virtuele netwerkservice voor Key Vault](overview-vnet-service-endpoints.md))
* [Beveilig uw sleutelkluis)](secure-your-key-vault.md)
