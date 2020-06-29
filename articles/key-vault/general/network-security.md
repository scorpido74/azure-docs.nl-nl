---
title: Key Vault-firewalls en virtuele netwerken configureren - Azure Key Vault
description: Stapsgewijze instructies om Key Vault-firewalls en virtuele netwerken te configureren
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 04/22/2020
ms.author: sudbalas
ms.openlocfilehash: 19a7cf2ec3a8a7a95952fcebfcf3a127c4dfd013
ms.sourcegitcommit: 6571e34e609785e82751f0b34f6237686470c1f3
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/15/2020
ms.locfileid: "84792180"
---
# <a name="configure-azure-key-vault-firewalls-and-virtual-networks"></a>Azure Key Vault-firewalls en virtuele netwerken configureren

In dit artikel vindt u stapsgewijze instructies voor het configureren van Azure Key Vault-firewalls en virtuele netwerken om de toegang tot uw sleutelkluis te beperken. Met de [service-eindpunten van het virtuele netwerk voor Key Vault](overview-vnet-service-endpoints.md)) kunt u de toegang tot een opgegeven virtueel netwerk en een aantal IPv4-adresbereiken (internetprotocol versie 4) beperken.

> [!IMPORTANT]
> Als de firewallregels van kracht zijn, kunnen gebruikers alleen Key Vault-[gegevenslaagbewerkingen](secure-your-key-vault.md#data-plane-access-control) uitvoeren wanneer hun aanvragen afkomstig zijn van toegestane virtuele netwerken of IPv4-adresbereiken. Dit is tevens van toepassing voor toegang tot Key Vault vanuit Azure Portal. Hoewel gebruikers kunnen bladeren naar een sleutelkluis van Azure Portal, kunnen ze mogelijk geen sleutels, geheimen of certificaten weergeven als hun clientcomputer niet in de lijst met toegestane clients staat. Dit is ook van invloed op de Key Vault-kiezer door andere Azure-Services. Gebruikers zien mogelijk een lijst met sleutelkluizen, maar geen lijst met sleutels als firewallregels hun clientcomputer weigeren.

> [!NOTE]
> Houd rekening met de volgende configuratielimieten:
> * Er zijn maximaal 127 regels voor virtuele netwerken en 127 IPv4-regels toegestaan. 
> * Kleine adresbereiken die de voorvoegselgrootten /31 of /32 gebruiken, worden niet ondersteund. Configureer deze bereiken in plaats hiervan door afzonderlijke IP-adresregels te gebruiken.
> * IP-netwerkregels zijn alleen toegestaan voor openbare IP-adressen. IP-adresbereiken die zijn gereserveerd voor privénetwerken (zoals gedefinieerd in RFC 1918) zijn niet toegestaan in IP-regels. Privénetwerken omvatten adressen die beginnen met **10.** , **172.16-31**, en **192.168.** . 
> * Momenteel worden alleen IPv4-adressen ondersteund.

## <a name="use-the-azure-portal"></a>Azure Portal gebruiken

U kunt als volgt Key Vault-firewalls en virtuele netwerken configureren met behulp van Azure Portal:

1. Blader naar de sleutelkluis die u wilt beveiligen.
2. Selecteer **Netwerken** en het tabblad **Firewalls en virtuele netwerken**.
3. Selecteer **Geselecteerde netwerken** onder **Toegang toestaan uit**.
4. Als u bestaande virtuele netwerken wilt toevoegen aan firewalls en regels voor virtuele netwerken, selecteert u **+ Bestaande virtuele netwerken toevoegen**.
5. Selecteer op de nieuwe blade die wordt geopend het abonnement, de virtuele netwerken en de subnetten die u toegang wilt geven tot deze sleutelkluis. Als voor de virtuele netwerken en subnetten die u selecteert geen service-eindpunten zijn ingeschakeld, bevestigt u dat u service-eindpunten wilt inschakelen en selecteert u **Inschakelen**. Het kan tot vijftien minuten duren voordat deze instelling van kracht is.
6. Voeg onder **IP-netwerken** IPv4-adresbereiken toe door IPv4-adresbereiken in [CIDR-notatie (Classless Inter-Domain Routing)](https://tools.ietf.org/html/rfc4632) of afzonderlijke IP-adressen in te voeren.
7. Als u wilt toestaan dat vertrouwde services van Microsoft de Key Vault-firewall overslaan, selecteert u Ja. Klik op de volgende koppeling voor een volledige lijst van de huidige vertrouwde Key Vault-services. [Vertrouwde services van Azure Key Vault](https://docs.microsoft.com/azure/key-vault/general/overview-vnet-service-endpoints#trusted-services)
7. Selecteer **Opslaan**.

U kunt ook nieuwe virtuele netwerken en subnetten toevoegen en vervolgens service-eindpunten inschakelen voor de zojuist gemaakte virtuele netwerken en subnetten door **+ Nieuw virtuele netwerk toevoegen** te selecteren. Volg daarna de aanwijzingen.

## <a name="use-the-azure-cli"></a>Azure CLI gebruiken 

U kunt als volgt Key Vault-firewalls en virtuele netwerken configureren met behulp van Azure CLI

1. [Installeer Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) en [meld u aan](https://docs.microsoft.com/cli/azure/authenticate-azure-cli).

2. Bekijk de beschikbare regels voor virtuele netwerken. Als u geen regels voor deze sleutelkluis hebt ingesteld, is de lijst leeg.
   ```azurecli
   az keyvault network-rule list --resource-group myresourcegroup --name mykeyvault
   ```

3. Schakel een service-eindpunt in voor Key Vault op een bestaand virtueel netwerk en subnet.
   ```azurecli
   az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.KeyVault"
   ```

4. Voeg een netwerkregel toe voor een virtueel netwerk en subnet.
   ```azurecli
   subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
   az keyvault network-rule add --resource-group "demo9311" --name "demo9311premium" --subnet $subnetid
   ```

5. Voeg een IP-adresbereik toe waaruit verkeer kan worden toegestaan.
   ```azurecli
   az keyvault network-rule add --resource-group "myresourcegroup" --name "mykeyvault" --ip-address "191.10.18.0/24"
   ```

6. Als deze sleutelkluis toegankelijk moet zijn voor vertrouwde services, stelt u `bypass` in op `AzureServices`.
   ```azurecli
   az keyvault update --resource-group "myresourcegroup" --name "mykeyvault" --bypass AzureServices
   ```

7. Schakel de netwerkregels in door de standaardactie in te stellen op `Deny`.
   ```azurecli
   az keyvault update --resource-group "myresourcegroup" --name "mekeyvault" --default-action Deny
   ```

## <a name="use-azure-powershell"></a>Azure PowerShell gebruiken

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

U kunt als volgt Key Vault-firewalls en virtuele netwerken configureren met behulp van PowerShell:

1. Installeer de nieuwste versie van [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps) en [meld u aan](https://docs.microsoft.com/powershell/azure/authenticate-azureps).

2. Bekijk de beschikbare regels voor virtuele netwerken. Als u geen regels voor deze sleutelkluis hebt ingesteld, is de lijst leeg.
   ```powershell
   (Get-AzKeyVault -VaultName "mykeyvault").NetworkAcls
   ```

3. Schakel een service-eindpunt in voor Key Vault op een bestaand virtueel netwerk en subnet.
   ```powershell
   Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" -AddressPrefix "10.1.1.0/24" -ServiceEndpoint "Microsoft.KeyVault" | Set-AzVirtualNetwork
   ```

4. Voeg een netwerkregel toe voor een virtueel netwerk en subnet.
   ```powershell
   $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
   Add-AzKeyVaultNetworkRule -VaultName "mykeyvault" -VirtualNetworkResourceId $subnet.Id
   ```

5. Voeg een IP-adresbereik toe waaruit verkeer kan worden toegestaan.
   ```powershell
   Add-AzKeyVaultNetworkRule -VaultName "mykeyvault" -IpAddressRange "16.17.18.0/24"
   ```

6. Als deze sleutelkluis toegankelijk moet zijn voor vertrouwde services, stelt u `bypass` in op `AzureServices`.
   ```powershell
   Update-AzKeyVaultNetworkRuleSet -VaultName "mykeyvault" -Bypass AzureServices
   ```

7. Schakel de netwerkregels in door de standaardactie in te stellen op `Deny`.
   ```powershell
   Update-AzKeyVaultNetworkRuleSet -VaultName "mykeyvault" -DefaultAction Deny
   ```

## <a name="references"></a>Verwijzingen
* ARM-sjabloonreferentie: [ARM-sjabloonreferentie voor Azure Key Vault](https://docs.microsoft.com/azure/templates/Microsoft.KeyVault/vaults)
* Azure CLI-opdrachten: [az keyvault network-rule](https://docs.microsoft.com/cli/azure/keyvault/network-rule?view=azure-cli-latest)
* Azure PowerShell-cmdlets: [Get-AzKeyVault](https://docs.microsoft.com/powershell/module/az.keyvault/get-azkeyvault), [Add-AzKeyVaultNetworkRule](https://docs.microsoft.com/powershell/module/az.KeyVault/Add-azKeyVaultNetworkRule), [Remove-AzKeyVaultNetworkRule](https://docs.microsoft.com/powershell/module/az.KeyVault/Remove-azKeyVaultNetworkRule), [Update-AzKeyVaultNetworkRuleSet](https://docs.microsoft.com/powershell/module/az.KeyVault/Update-azKeyVaultNetworkRuleSet)

## <a name="next-steps"></a>Volgende stappen

* [Service-eindpunten voor virtuele netwerken voor Key Vault](overview-vnet-service-endpoints.md))
* [Uw sleutelkluis beveiligen](secure-your-key-vault.md))
