---
title: De Tenant-ID van de sleutel kluis wijzigen nadat een abonnement is verplaatst-Azure Key Vault | Microsoft Docs
description: Meer informatie over het overschakelen op een ander tenant-ID voor een Key Vault nadat een abonnement is verplaatst naar een andere tenant
services: key-vault
author: amitbapat
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: ambapat
ms.openlocfilehash: bc2e73d037b05c012002d7a07e2a2af2431423fa
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/16/2019
ms.locfileid: "72428902"
---
# <a name="change-a-key-vault-tenant-id-after-a-subscription-move"></a>De tenant-ID van de Key Vault wijzigen na een verplaatsing van een abonnement

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]


Wanneer u een nieuwe Key Vault maakt in een abonnement, is het automatisch gekoppeld aan de tenant-ID van Azure Active Directory voor dit abonnement. Alle vermeldingen van het toegangsbeleid zijn ook gekoppeld aan deze tenant-ID. 

Als u uw Azure-abonnement van Tenant A naar Tenant B verplaatst, zijn uw bestaande sleutel kluizen niet toegankelijk voor de principals (gebruikers en toepassingen) in Tenant B. U moet het volgende doen om dit probleem op te lossen:

* Wijzig de Tenant-ID die is gekoppeld aan alle bestaande sleutel kluizen in het abonnement op Tenant B.
* Alle bestaande vermeldingen van het toegangsbeleid te verwijderen.
* Nieuwe beleids regels voor toegang toevoegen die zijn gekoppeld aan Tenant B.

Als u bijvoorbeeld sleutel kluis myvault hebt in een abonnement dat is verplaatst van Tenant A naar Tenant B, kunt u Azure PowerShell gebruiken om de Tenant-ID te wijzigen en het oude toegangs beleid te verwijderen.

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

U kunt ook de Azure CLI gebruiken.

```azurecli
az account set <your-subscriptionId>                                       # Select your Azure Subscription
tenantId=$(az account show --query tenantId)                               # Get your tenantId
az keyvault update -n myvault --remove Properties.accessPolicies           # Remove the access policies
az keyvault update -n myvault --set Properties.tenantId=$tenantId          # Update the key vault tenantId
```

Nu de kluis is gekoppeld aan de juiste Tenant-ID en de oude vermeldingen van het toegangs beleid worden verwijderd, stelt u nieuwe vermeldingen voor het toegangs beleid in met de Azure PowerShell [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/Set-azKeyVaultAccessPolicy) cmdlet of de Azure cli [AZ set-Policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) opdracht.

Als u een beheerde identiteit voor Azure-resources gebruikt, moet u deze ook bijwerken naar de nieuwe Azure AD-Tenant. Zie voor meer informatie over beheerde identiteiten [Key Vault verificatie bieden met een beheerde identiteit](managed-identity.md).


Als u MSI gebruikt, moet u ook de MSI-identiteit bijwerken omdat de oude identiteit niet meer in de juiste AAD-Tenant voor komt.

## <a name="next-steps"></a>Volgende stappen

Ga naar de [forums van Azure Key Vault](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault) als u vragen hebt over Azure Key Vault.
