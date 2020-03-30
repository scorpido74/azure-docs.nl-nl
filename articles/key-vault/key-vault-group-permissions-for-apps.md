---
title: Toestemming verlenen aan toepassingen voor toegang tot een Azure-sleutelkluis - Azure Key Vault | Microsoft Documenten
description: Meer informatie over het verlenen van toestemming aan veel toepassingen om toegang te krijgen tot een sleutelkluis
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 09/27/2019
ms.author: mbaldwin
ms.openlocfilehash: d5086377b0bb7f3ca2ece643f82a4e45156f1955
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78184856"
---
# <a name="provide-key-vault-authentication-with-an-access-control-policy"></a>Key Vault-verificatie bieden met een toegangscontrolebeleid

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

De eenvoudigste manier om een cloudtoepassing te verifiëren voor Key Vault is met een beheerde identiteit; zie [Een door Een App-service beheerde identiteit gebruiken om toegang te krijgen tot Azure Key Vault](managed-identity.md) voor meer informatie.  Als u een on-prem-toepassing maakt, lokale ontwikkeling doet of anderszins geen beheerde identiteit gebruiken, u in plaats daarvan een serviceprincipal handmatig registreren en toegang bieden tot uw sleutelkluis met behulp van een toegangscontrolebeleid.  

Key vault ondersteunt maximaal 1024 toegangsbeleidsitems, waarbij elk item een afzonderlijke set machtigingen verleent aan een 'principal': dit is bijvoorbeeld hoe de console-app in de [Azure Key Vault-clientbibliotheek voor .NET snel toegang](quick-create-net.md) geeft tot de sleutelkluis.

Zie [Azure Key Vault-beveiliging: Identiteits- en toegangsbeheer voor](overview-security.md#identity-and-access-management)meer informatie over toegangsbeheer van Key Vault. Zie voor meer informatie over [toegangsbeheer sleutels, geheimen en certificaten:](about-keys-secrets-and-certificates.md) 

- [Toegangsbeheer sleutels](about-keys-secrets-and-certificates.md#key-access-control)
- [Geheimen toegangscontrole](about-keys-secrets-and-certificates.md#secret-access-control)
- [Toegangsbeheer voor certificaten](about-keys-secrets-and-certificates.md#certificate-access-control)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Vereisten

- Een sleutelkluis. U een bestaand sleutelkluis gebruiken of een nieuwe maken door de stappen in een van deze snelstarts te volgen:
   - [Een sleutelkluis maken met de Azure CLI](quick-create-cli.md)
   - [Een sleutelkluis maken met Azure PowerShell](quick-create-powershell.md)
   - [Maak een sleutelkluis met de Azure-portal.](quick-create-portal.md)
- De [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) of Azure [PowerShell](/powershell/azure/overview). U ook de [Azure-portal](https://portal.azure.com)gebruiken.

## <a name="grant-access-to-your-key-vault"></a>Geef toegang tot uw sleutelkluis

Elke toegang tot het toegangsbeleid voor belangrijke kluizen verleent een afzonderlijke set machtigingen aan een hoofdsom:

- **Een toepassing** Als de toepassing cloudgebaseerd is, moet u in plaats daarvan [een beheerde identiteit gebruiken om toegang te krijgen tot Azure Key Vault,](managed-identity.md)indien mogelijk
- **Een Azure AD-groep** Hoewel key vault slechts 1024 toegangsbeleidsvermeldingen ondersteunt, u meerdere toepassingen en gebruikers toevoegen aan één Azure AD-groep en deze groep vervolgens toevoegen als één vermelding voor uw toegangscontrolebeleid.
- **Een gebruiker** Het geven van gebruikers directe toegang tot een sleutel kluis wordt **ontmoedigd.** Idealiter moeten gebruikers worden toegevoegd aan een Azure AD-groep, die op zijn beurt toegang krijgt tot de sleutelkluis. Zie [Azure Key Vault-beveiliging: identiteits- en toegangsbeheer](overview-security.md#identity-and-access-management).


### <a name="get-the-objectid"></a>De object-ID oppakken

Als u een toepassing, Azure AD-groep of gebruiker toegang wilt geven tot uw sleutelkluis, moet u eerst de objectId verkrijgen.

#### <a name="applications"></a>Toepassingen

De objectId voor een toepassing komt overeen met de bijbehorende serviceprincipal. Voor alle details over service principals. zie [Hoofdobjecten voor toepassingen en services in Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md). 

Er zijn twee manieren om een objectId voor een toepassing te verkrijgen.  De eerste is het registreren van uw toepassing bij Azure Active Directory. Volg hiervoor de stappen in de werkbalk [Een toepassing snel registreren bij het Microsoft-identiteitsplatform](../active-directory/develop/quickstart-register-app.md). Wanneer de registratie is voltooid, wordt de objectID vermeld als de "Application (client) ID".

De tweede is het maken van een service principal in een terminal venster. Gebruik met de azure CLI de opdracht [AZ Ad SP create-for-rbac.](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac)

```azurecli-interactive
az ad sp create-for-rbac -n "http://mySP"
```

De objectId wordt in de `clientID`uitvoer weergegeven als .

Gebruik met Azure PowerShell de cmdlet [Nieuw-AzADServicePrincipal.](/powershell/module/Az.Resources/New-AzADServicePrincipal?view=azps-2.7.0)


```azurepowershell-interactive
New-AzADServicePrincipal -DisplayName mySP
```

De objectId wordt in de `Id` uitvoer `ApplicationId`weergegeven als (niet ).

#### <a name="azure-ad-groups"></a>Azure AD-groepen

U meerdere toepassingen en gebruikers toevoegen aan een Azure AD-groep en de groep vervolgens toegang geven tot uw sleutelkluis.  Zie hieronder de sectie [Leden maken en toevoegen aan een Azure AD-groep.](#creating-and-adding-members-to-an-azure-ad-group)

Als u de objectId van een Azure AD-groep met de Azure CLI wilt vinden, gebruikt u de [opdracht lijst met AZ-advertentiegroepen.](/cli/azure/ad/group?view=azure-cli-latest#az-ad-group-list) Vanwege het grote aantal groepen dat zich in uw organisatie bevindt, `--display-name` moet u ook een zoektekenreeks aan de parameter verstrekken.

```azurecli-interactive
az ad group list --display-name <search-string>
```
De objectId wordt geretourneerd in de JSON:

```json
    "objectId": "48b21bfb-74d6-48d2-868f-ff9eeaf38a64",
    "objectType": "Group",
    "odata.type": "Microsoft.DirectoryServices.Group",
```

Als u de objectId van een Azure AD-groep met Azure PowerShell wilt vinden, gebruikt u de cmdlet [Get-AzADGroup.](/powershell/module/az.resources/get-azadgroup?view=azps-2.7.0) Vanwege het grote aantal groepen dat zich in uw organisatie bevindt, wilt `-SearchString` u waarschijnlijk ook een zoektekenreeks aan de parameter verstrekken.

```azurepowershell-interactive
Get-AzADGroup -SearchString <search-string>
```

In de uitvoer wordt de objectid vermeld als `Id`:

```console
...
Id                    : 1cef38c4-388c-45a9-b5ae-3d88375e166a
...
```

#### <a name="users"></a>Gebruikers

U ook een individuele gebruiker toevoegen aan het toegangscontrolebeleid van een sleutelkluis. **Wij raden dit niet aan.** In plaats daarvan raden we u aan om gebruikers toe te voegen aan een Azure AD-groep en de groep toe te voegen aan het beleid.

Als u toch een gebruiker met de Azure CLI wilt vinden, gebruikt u de `--id` opdracht [az-advertentiegebruikers,](/cli/azure/ad/user?view=azure-cli-latest#az-ad-user-show) waarbij het e-mailadres van de gebruikers wordt doorgegeven aan de parameter.


```azurecli-interactive
az ad user show --id <email-address-of-user>
```

De objectId van de gebruiker wordt geretourneerd in de uitvoer:

```console
  ...
  "objectId": "f76a2a6f-3b6d-4735-9abd-14dccbf70fd9",
  "objectType": "User",
  ...
```

Als u een gebruiker met Azure PowerShell wilt vinden, gebruikt u de cmdlet [Get-AzADUser](/powershell/module/az.resources/get-azaduser?view=azps-2.7.0) en geeft u het e-mailadres van de gebruikers door aan de `-UserPrincipalName` parameter.

```azurepowershell-interactive
 Get-AzAdUser -UserPrincipalName <email-address-of-user>
```

De objectId van de gebruiker wordt `Id`geretourneerd in de uitvoer als .

```console
...
Id                : f76a2a6f-3b6d-4735-9abd-14dccbf70fd9
Type              :
```

### <a name="give-the-principal-access-to-your-key-vault"></a>Geef de opdrachtgever toegang tot uw sleutelkluis

Nu u een objectID van uw opdrachtgever hebt, u een toegangsbeleid voor uw sleutelkluis maken waarmee machtigingen voor zowel sleutels als geheimen worden weergegeven, weergegeven, ingesteld en verwijderd, plus eventuele extra machtigingen die u wilt.

Met de Azure CLI wordt dit gedaan door de objectId door te geven aan de opdracht [set-policy van AZ Keyvault.](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy)

```azurecli-interactive
az keyvault set-policy -n <your-unique-keyvault-name> --spn <ApplicationID-of-your-service-principal> --secret-permissions get list set delete --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey
```

Met Azure PowerShell wordt dit gedaan door de objectId door te geven aan de cmdlet [Set-AzKeyVaultAccessPolicy.](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy?view=azps-2.7.0) 

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy –VaultName <your-key-vault-name> -PermissionsToKeys create,decrypt,delete,encrypt,get,list,unwrapKey,wrapKey -PermissionsToSecrets get,list,set,delete -ApplicationId <Id>

```

## <a name="creating-and-adding-members-to-an-azure-ad-group"></a>Leden maken en toevoegen aan een Azure AD-groep

U een Azure AD-groep maken, toepassingen en gebruikers aan de groep toevoegen en de groep toegang geven tot uw sleutelkluis.  Hiermee u een aantal toepassingen toevoegen aan een sleutelkluis als één toegangsbeleid invoer, en elimineert u de noodzaak om gebruikers directe toegang te geven tot uw sleutelkluis (die we ontmoedigen). Zie [App- en brontoegang beheren met Azure Active Directory-groepen](../active-directory/fundamentals/active-directory-manage-groups.md)voor meer informatie.

### <a name="additional-prerequisites"></a>Aanvullende vereisten

Naast de [bovenstaande vereisten](#prerequisites)hebt u machtigingen nodig om groepen te maken/bewerken in uw Azure Active Directory-tenant. Als u geen machtigingen hebt, moet u mogelijk contact opnemen met uw Azure Active Directory-beheerder.

Als u PowerShell wilt gebruiken, hebt u ook de [Azure AD PowerShell-module](https://www.powershellgallery.com/packages/AzureAD/2.0.2.50) nodig

### <a name="create-an-azure-active-directory-group"></a>Een Azure Active Directory-groep maken

Maak een nieuwe Azure Active Directory-groep met de opdracht Azure CLI [az-advertentiegroep maken](/cli/azure/ad/group?view=azure-cli-latest#az-ad-group-create) of de azure PowerShell [New-AzureADGroup-cmdlet.](/powershell/module/azuread/new-azureadgroup?view=azureadps-2.0)


```azurecli-interactive
az ad group create --display-name <your-group-display-name> --mail-nickname <your-group-mail-nickname>
```

```powershell
New-AzADGroup -DisplayName <your-group-display-name> -MailNickName <your-group-mail-nickname>
```

Noteer in beide gevallen de nieuw gemaakte groepen GroupId, omdat u deze nodig hebt voor de onderstaande stappen.

### <a name="find-the-objectids-of-your-applications-and-users"></a>Zoek de objectIds van uw toepassingen en gebruikers

U de objectids van uw toepassingen vinden met behulp van `--show-mine` de Azure CLI met de [lijstopdracht AZ Ad SP,](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-list) met de parameter.

```azurecli-interactive
az ad sp list --show-mine
```

Zoek de objectids van uw toepassingen met Azure PowerShell met de [cmdlet Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal?view=azps-2.7.0) en geef een zoektekenreeks door aan de `-SearchString` parameter.

```azurepowershell-interactive
Get-AzADServicePrincipal -SearchString <search-string>
```

Als u de objectids van uw gebruikers wilt vinden, volgt u de stappen in de sectie [Gebruikers](#users) hierboven.

### <a name="add-your-applications-and-users-to-the-group"></a>Uw toepassingen en gebruikers toevoegen aan de groep

Voeg nu de objectIds toe aan de nieuw gemaakte Azure AD-groep.

Gebruik met azure cli de [add-up van de AZ-advertentiegroep,](/cli/azure/ad/group/member?view=azure-cli-latest#az-ad-group-member-add)waarbij de objectId wordt doorgegeven aan de `--member-id` parameter.


```azurecli-interactive
az ad group member add -g <groupId> --member-id <objectId>
```

Gebruik met Azure PowerShell de cmdlet [Add-AzADGroupMember](/powershell/module/az.resources/add-azadgroupmember?view=azps-2.7.0) en `-MemberObjectId` geef de objectId door aan de parameter.

```azurepowershell-interactive
Add-AzADGroupMember -TargetGroupObjectId <groupId> -MemberObjectId <objectId> 
```

### <a name="give-the-ad-group-access-to-your-key-vault"></a>Geef de AD-groep toegang tot uw sleutelkluis

Geef ten slotte de AD-groepsmachtigingen aan uw sleutelkluis met de opdracht Azure CLI [az keyvault-setbeleid](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) of de cmdlet Azure PowerShell [Set-AzKeyVaultAccessPolicy.](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy?view=azps-2.7.0) Zie bijvoorbeeld de [sectie De toepassing, de Azure AD-groep of de gebruiker toegang geven tot de](#give-the-principal-access-to-your-key-vault) sectie Sleutelkluis hierboven.

De toepassing heeft ook ten minste één IAM-rol (Identity and Access Management) nodig die is toegewezen aan de sleutelkluis. Anders zal het niet in staat zijn om in te loggen en zal mislukken met onvoldoende rechten om toegang te krijgen tot het abonnement.

## <a name="next-steps"></a>Volgende stappen

- [Azure Key Vault-beveiliging: identiteits- en toegangsbeheer](overview-security.md#identity-and-access-management)
- [Key Vault-verificatie bieden met een door App Service beheerde identiteit](managed-identity.md)
- [Informatie over sleutels, geheimen en certificaten](about-keys-secrets-and-certificates.md)
- [Beveilig je sleutelkluis.](key-vault-secure-your-key-vault.md)
- [Handleiding azure Key Vault-ontwikkelaar](key-vault-developers-guide.md)
- Aanbevolen [procedures voor Azure Key Vault bekijken](key-vault-best-practices.md)
