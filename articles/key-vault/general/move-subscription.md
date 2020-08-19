---
title: Azure Key Vault een kluis naar een ander abonnement verplaatsen | Microsoft Docs
description: Richt lijnen voor het verplaatsen van een sleutel kluis naar een ander abonnement.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 05/05/2020
ms.author: sudbalas
Customer intent: As a key vault administrator, I want to move my vault to another subscription.
ms.openlocfilehash: d37fae18cd2f3e3bfad647cc176253dc6bb101ab
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/19/2020
ms.locfileid: "88585743"
---
# <a name="moving-an-azure-key-vault-to-another-subscription"></a>Een Azure Key Vault verplaatsen naar een ander abonnement

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>Overzicht

> [!IMPORTANT]
> **Als u een sleutel kluis verplaatst naar een ander abonnement, veroorzaakt dit een ernstige wijziging in uw omgeving.**
> Zorg ervoor dat u bekend bent met de gevolgen van deze wijziging en volg de richt lijnen in dit artikel zorgvuldig voordat u besluit sleutel kluis naar een nieuw abonnement te verplaatsen.
> Als u gebruikmaakt van Managed Service Identities (MSI), raadpleegt u de instructies na het verplaatsen aan het einde van het document. 

Wanneer u een sleutel kluis maakt, wordt deze automatisch gebonden aan de standaard Azure Active Directory Tenant-ID voor het abonnement waarin deze is gemaakt. Alle vermeldingen van het toegangsbeleid zijn ook gekoppeld aan deze tenant-ID. Als u uw Azure-abonnement verplaatst van Tenant A naar Tenant B, worden uw bestaande sleutel kluizen niet toegankelijk voor de service-principals (gebruikers en toepassingen) in Tenant B. U moet het volgende doen om dit probleem op te lossen:

* De tenant-ID die is gekoppeld aan alle bestaande sleutelkluizen in het abonnement te wijzigen in tenant B.
* Alle bestaande vermeldingen van het toegangsbeleid te verwijderen.
* Nieuwe aan tenant B gekoppelde vermeldingen van het toegangsbeleid toe te voegen.

## <a name="limitations"></a>Beperkingen

Sommige service-principals (gebruikers en toepassingen) zijn gekoppeld aan een specifieke Tenant. Als u de sleutel kluis verplaatst naar een abonnement in een andere Tenant, is er een kans dat u de toegang tot een specifieke Service-Principal niet kunt herstellen. Controleer of alle essentiële service-principals aanwezig zijn in de Tenant waar u de sleutel kluis wilt verplaatsen.

## <a name="design-considerations"></a>Overwegingen bij het ontwerpen

Uw organisatie heeft mogelijk Azure Policy geïmplementeerd met afdwinging of uitsluitingen op abonnements niveau. Er is mogelijk een andere set beleids toewijzingen in het abonnement waar uw sleutel kluis momenteel bestaat en het abonnement waar u de sleutel kluis wilt verplaatsen. Een conflict in beleids vereisten is het mogelijk om uw toepassingen te onderbreken.

### <a name="example"></a>Voorbeeld

U hebt een toepassing die is verbonden met een sleutel kluis die certificaten maakt die twee jaar geldig zijn. Het abonnement waarin u de sleutel kluis probeert te verplaatsen, heeft een beleids toewijzing die het maken van certificaten blokkeert die langer dan één jaar geldig zijn. Nadat u de sleutel kluis naar het nieuwe abonnement hebt verplaatst, wordt de bewerking voor het maken van een certificaat dat twee jaar geldig is, geblokkeerd door een Azure-beleids toewijzing.

### <a name="solution"></a>Oplossing

Zorg ervoor dat u naar de pagina Azure Policy gaat in de Azure Portal en Bekijk de beleids toewijzingen voor uw huidige abonnement, evenals het abonnement dat u gaat verplaatsen naar en zorg ervoor dat er geen verschillen zijn.

## <a name="prerequisites"></a>Vereisten

* Toegang op Inzender niveau of hoger voor het huidige abonnement waar uw sleutel kluis bestaat.
* Toegang op Inzender niveau of hoger voor het abonnement waar u de sleutel kluis wilt verplaatsen.
* Een resource groep in het nieuwe abonnement.

## <a name="procedure"></a>Procedure

### <a name="initial-steps-moving-key-vault"></a>Eerste stappen (verplaatsen Key Vault)

1. Aanmelden bij Azure Portal
2. Navigeer naar uw sleutelkluis
3. Klik op het tabblad Overzicht
4. Selecteer de knop "verplaatsen"
5. Selecteer ' verplaatsen naar een ander abonnement ' in de vervolg keuzelijst opties
6. Selecteer de resource groep waarnaar u de sleutel kluis wilt verplaatsen
7. De waarschuwing bevestigen over het verplaatsen van resources
8. Selecteer OK

### <a name="additional-steps-post-move"></a>Aanvullende stappen (post verplaatsing)

Nu u de sleutel kluis naar het nieuwe abonnement hebt verplaatst, moet u de Tenant-ID bijwerken en het oude toegangs beleid verwijderen. Hier vindt u zelf studies voor deze stappen in Power shell en Azure CLI.

```azurepowershell
Select-AzSubscription -SubscriptionId <your-subscriptionId>                # Select your Azure Subscription
$vaultResourceId = (Get-AzKeyVault -VaultName myvault).ResourceId          # Get your key vault's Resource ID 
$vault = Get-AzResource –ResourceId $vaultResourceId -ExpandProperties     # Get the properties for your key vault
$vault.Properties.TenantId = (Get-AzContext).Tenant.TenantId               # Change the Tenant that your key vault resides in
$vault.Properties.AccessPolicies = @()                                     # Access policies can be updated with real
                                                                           # applications/users/rights so that it does not need to be                             # done after this whole activity. Here we are not setting 
                                                                           # any access policies. 
Set-AzResource -ResourceId $vaultResourceId -Properties $vault.Properties  # Modifies the key vault's properties.

Clear-AzContext                                                            #Clear the context from PowerShell
Connect-AzAccount                                                          #Log in again to confirm you have the correct tenant id
````

```azurecli
az account set -s <your-subscriptionId>                                    # Select your Azure Subscription
tenantId=$(az account show --query tenantId)                               # Get your tenantId
az keyvault update -n myvault --remove Properties.accessPolicies           # Remove the access policies
az keyvault update -n myvault --set Properties.tenantId=$tenantId          # Update the key vault tenantId
```

Nu uw kluis is gekoppeld aan de juiste tenant-ID en de oude toegangsbeleidsregels zijn verwijderd, kunt u nieuwe toegangsbeleidsregels instellen met de Azure PowerShell-cmdlet [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/Set-azKeyVaultAccessPolicy) of de Azure CLI-opdracht [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy).

Als u een beheerde identiteit voor Azure-resources gebruikt, moet u deze ook bijwerken naar de nieuwe Azure AD-tenant. Zie [Key Vault-verificatie bieden met een beheerde identiteit](managed-identity.md)voor meer informatie over beheerde identiteiten.

Als u MSI gebruikt, moet u ook de MSI-identiteit bijwerken, omdat de oude identiteit niet meer in de juiste AAD-tenant voorkomt. Raadpleeg de volgende documenten voor informatie over het oplossen van dit probleem. 

* [MSI bijwerken](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/known-issues#transferring-a-subscription-between-azure-ad-directories)
* [Abonnement overdragen naar nieuwe map](https://docs.microsoft.com/azure/role-based-access-control/transfer-subscription)


