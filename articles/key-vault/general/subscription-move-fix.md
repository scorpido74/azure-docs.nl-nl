---
title: De key vault tenant ID wijzigen na een verhuizing van een abonnement - Azure Key Vault | Microsoft Documenten
description: Meer informatie over het overschakelen op een ander tenant-ID voor een Key Vault nadat een abonnement is verplaatst naar een andere tenant
services: key-vault
author: amitbapat
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: ambapat
ms.openlocfilehash: 94bcba80e5768d57e3dc97bed1a74a8369ac60b9
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81422891"
---
# <a name="change-a-key-vault-tenant-id-after-a-subscription-move"></a>De tenant-ID van de Key Vault wijzigen na een verplaatsing van een abonnement

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]


Wanneer u een nieuwe Key Vault maakt in een abonnement, is het automatisch gekoppeld aan de tenant-ID van Azure Active Directory voor dit abonnement. Alle vermeldingen van het toegangsbeleid zijn ook gekoppeld aan deze tenant-ID. 

Als u uw Azure-abonnement verplaatst van tenant A naar tenant B, zijn uw bestaande sleutelkluizen niet toegankelijk voor de principals (gebruikers en toepassingen) in tenant B. Als u dit probleem wilt oplossen, moet u het:

* Wijzig de tenant-id die is gekoppeld aan alle bestaande sleutelkluizen in het abonnement op tenant B.
* Alle bestaande vermeldingen van het toegangsbeleid te verwijderen.
* Voeg nieuwe toegangsbeleidsvermeldingen toe die zijn gekoppeld aan tenant B.

Als u bijvoorbeeld 'myvault' in een abonnement hebt dat is verplaatst van tenant A naar tenant B, u Azure PowerShell gebruiken om de tenant-id te wijzigen en oude toegangsbeleidsregels te verwijderen.

```azurepowershell
Select-AzSubscription -SubscriptionId <your-subscriptionId>                # Select your Azure Subscription
$vaultResourceId = (Get-AzKeyVault -VaultName myvault).ResourceId          # Get your key vault's Resource ID 
$vault = Get-AzResource –ResourceId $vaultResourceId -ExpandProperties     # Get the properties for your key vault
$vault.Properties.TenantId = (Get-AzContext).Tenant.TenantId               # Change the Tenant that your key vault resides in
$vault.Properties.AccessPolicies = @()                                     # Access policies can be updated with real
                                                                           # applications/users/rights so that it does not need to be                             # done after this whole activity. Here we are not setting 
                                                                           # any access policies. 
Set-AzResource -ResourceId $vaultResourceId -Properties $vault.Properties  # Modifies the key vault's properties.
````

U de Azure CLI ook gebruiken.

```azurecli
az account set -s <your-subscriptionId>                                    # Select your Azure Subscription
tenantId=$(az account show --query tenantId)                               # Get your tenantId
az keyvault update -n myvault --remove Properties.accessPolicies           # Remove the access policies
az keyvault update -n myvault --set Properties.tenantId=$tenantId          # Update the key vault tenantId
```

Nu uw kluis is gekoppeld aan de juiste tenant-id en oude toegangsbeleidsvermeldingen zijn verwijderd, stelt u nieuwe toegangsbeleidsvermeldingen in met de cmdlet Azure PowerShell [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/Set-azKeyVaultAccessPolicy) of de opdracht Azure CLI [az keyvault-setbeleidsbeleids.](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy)

Als u een beheerde identiteit voor Azure-bronnen gebruikt, moet u deze ook bijwerken naar de nieuwe Azure AD-tenant. Zie [Key Vault-verificatie met een beheerde identiteit opgeven voor](managed-identity.md)meer informatie over beheerde identiteiten.


Als u MSI gebruikt, moet u ook de MSI-identiteit bijwerken, omdat de oude identiteit niet langer in de juiste AAD-tenant staat.

## <a name="next-steps"></a>Volgende stappen

Ga naar de [forums van Azure Key Vault](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault) als u vragen hebt over Azure Key Vault.
