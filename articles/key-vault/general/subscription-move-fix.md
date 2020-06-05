---
title: De tenant-ID van de sleutelkluis wijzigen na een verplaatsing van een abonnement - Azure Key Vault | Microsoft Docs
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
ms.openlocfilehash: 9e35f5c9288860056a910f54f9601b2178a628bb
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/25/2020
ms.locfileid: "83828080"
---
# <a name="change-a-key-vault-tenant-id-after-a-subscription-move"></a>De tenant-ID van de Key Vault wijzigen na een verplaatsing van een abonnement

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]


Wanneer u een nieuwe Key Vault maakt in een abonnement, is het automatisch gekoppeld aan de tenant-ID van Azure Active Directory voor dit abonnement. Alle vermeldingen van het toegangsbeleid zijn ook gekoppeld aan deze tenant-ID. 

Als u uw Azure-abonnement van tenant A naar tenant B verplaatst, hebben de principals (gebruikers en toepassingen) geen toegang tot de bestaande sleutelkluizen in tenant B. U kunt dit probleem oplossen door:

* De tenant-ID die is gekoppeld aan alle bestaande sleutelkluizen in het abonnement te wijzigen in tenant B.
* Alle bestaande vermeldingen van het toegangsbeleid te verwijderen.
* Nieuwe aan tenant B gekoppelde vermeldingen van het toegangsbeleid toe te voegen.

Als u bijvoorbeeld sleutelkluis 'mijnkluis' hebt in een abonnement dat is verplaatst van tenant A naar B, kunt u Azure PowerShell gebruiken om de tenant-id te wijzigen en oud toegangsbeleid te verwijderen.

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
az account set -s <your-subscriptionId>                                    # Select your Azure Subscription
tenantId=$(az account show --query tenantId)                               # Get your tenantId
az keyvault update -n myvault --remove Properties.accessPolicies           # Remove the access policies
az keyvault update -n myvault --set Properties.tenantId=$tenantId          # Update the key vault tenantId
```

Nu uw kluis is gekoppeld aan de juiste tenant-ID en de oude toegangsbeleidsregels zijn verwijderd, kunt u nieuwe toegangsbeleidsregels instellen met de Azure PowerShell-cmdlet [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/Set-azKeyVaultAccessPolicy) of de Azure CLI-opdracht [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy).

Als u een beheerde identiteit voor Azure-resources gebruikt, moet u deze ook bijwerken naar de nieuwe Azure AD-tenant. Zie [Key Vault-verificatie bieden met een beheerde identiteit](managed-identity.md)voor meer informatie over beheerde identiteiten.


Als u MSI gebruikt, moet u ook de MSI-identiteit bijwerken, omdat de oude identiteit niet meer in de juiste AAD-tenant voorkomt.

## <a name="next-steps"></a>Volgende stappen

Als u vragen hebt over Azure Key Vault, ga dan naar de [Microsoft-pagina met vragen en antwoorden voor Azure Key Vault](https://docs.microsoft.com/answers/topics/azure-key-vault.html).
