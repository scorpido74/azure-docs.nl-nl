---
title: Machtigingen verlenen aan toepassingen om toegang te krijgen tot een Azure-sleutel kluis-Azure Key Vault | Microsoft Docs
description: Meer informatie over het verlenen van machtigingen aan veel toepassingen om toegang te krijgen tot een sleutel kluis
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 09/27/2019
ms.author: mbaldwin
ms.openlocfilehash: 008058e42dfeb84cb2812ac4e8378cb5a8b5913a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81422597"
---
# <a name="provide-key-vault-authentication-with-an-access-control-policy"></a>Key Vault verificatie met een toegangscontrole beleid bieden

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

De eenvoudigste manier om een Cloud toepassing te verifiëren voor Key Vault is met een beheerde identiteit. Zie [een beheerde app Service-id gebruiken voor toegang tot Azure Key Vault](managed-identity.md) voor meer informatie.  Als u een on-premises toepassing maakt, lokale ontwikkeling doet of op een andere manier geen beheerde identiteit kan gebruiken, kunt u in plaats daarvan een Service-Principal hand matig registreren en toegang verlenen tot uw sleutel kluis met behulp van een toegangs beheer beleid.  

Sleutel kluis ondersteunt Maxi maal 1024 toegangs beleidsregels, waarbij elke vermelding een afzonderlijke set machtigingen verleent aan een principal: dit is bijvoorbeeld hoe de console-app in de [Azure Key Vault-client bibliotheek voor .net Quick](../secrets/quick-create-net.md) start toegang heeft tot de sleutel kluis.

Zie [Azure Key Vault Security: identiteits-en toegangs beheer](overview-security.md#identity-and-access-management)voor volledige informatie over Key Vault toegangs beheer. Zie voor volledige informatie over toegangs beheer: 

- [Sleutels](../keys/index.yml)
- [Toegangs beheer voor geheimen](../secrets/index.yml)
- [Certificaten toegangs beheer](../certificates/index.yml)

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Vereisten

- Een sleutel kluis. U kunt een bestaande sleutel kluis gebruiken of een nieuwe maken door de stappen in een van deze Quick starts te volgen:
   - [Een sleutel kluis maken met Azure CLI](../secrets/quick-create-cli.md)
   - [Een sleutel kluis maken met Azure PowerShell](../secrets/quick-create-powershell.md)
   - [Een sleutel kluis maken met de Azure Portal](../secrets/quick-create-portal.md).
- De [Azure cli](/cli/azure/install-azure-cli?view=azure-cli-latest) of [Azure PowerShell](/powershell/azure/overview). U kunt ook de [Azure Portal](https://portal.azure.com)gebruiken.

## <a name="grant-access-to-your-key-vault"></a>Toegang verlenen tot uw sleutel kluis

Elk sleutel kluis toegangs beleid wordt een afzonderlijke set machtigingen verleend aan een principal:

- **Een toepassing** Als de toepassing is gebaseerd op de Cloud, moet u in plaats daarvan [een beheerde identiteit gebruiken om toegang te krijgen tot Azure Key Vault](managed-identity.md), indien mogelijk
- **Een Azure AD-groep** Hoewel sleutel kluis alleen 1024-toegangs beleidsitems ondersteunt, kunt u meerdere toepassingen en gebruikers toevoegen aan één Azure AD-groep en deze groep vervolgens als één vermelding toevoegen aan uw toegangscontrole beleid.
- **Een gebruiker** Gebruikers direct toegang verlenen tot een sleutel kluis wordt **afgeraden**. In het ideale geval moeten gebruikers worden toegevoegd aan een Azure AD-groep, die op zijn beurt toegang krijgt tot de sleutel kluis. Zie [Azure Key Vault beveiliging: identiteits-en toegangs beheer](overview-security.md#identity-and-access-management).


### <a name="get-the-objectid"></a>De objectID ophalen

Als u een toepassing, een Azure AD-groep of een gebruiker toegang wilt geven tot uw sleutel kluis, moet u eerst de objectId ophalen.

#### <a name="applications"></a>Toepassingen

De objectId voor een toepassing komt overeen met de bijbehorende service-principal. Voor volledige informatie over service-principals. Zie [toepassings-en Service-Principal-objecten in azure Active Directory](../../active-directory/develop/app-objects-and-service-principals.md). 

Er zijn twee manieren om een objectId voor een toepassing te verkrijgen.  Allereerst moet u uw toepassing registreren bij Azure Active Directory. Volg hiervoor de stappen in de Snelstartgids [een toepassing registreren bij het micro soft Identity-platform](../../active-directory/develop/quickstart-register-app.md). Wanneer de registratie is voltooid, wordt de objectID vermeld als de ' toepassing (client)-ID '.

De tweede is het maken van een Service-Principal in een Terminal venster. Met de Azure CLI, gebruikt u de opdracht [AZ AD SP create-for-RBAC](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) .

```azurecli-interactive
az ad sp create-for-rbac -n "http://mySP"
```

De objectId wordt weer gegeven in de uitvoer als `clientID`.

Gebruik met Azure PowerShell de cmdlet [New-AzADServicePrincipal](/powershell/module/Az.Resources/New-AzADServicePrincipal?view=azps-2.7.0) .


```azurepowershell-interactive
New-AzADServicePrincipal -DisplayName mySP
```

De objectId wordt weer gegeven in de uitvoer als `Id` (niet `ApplicationId`).

#### <a name="azure-ad-groups"></a>Azure AD-groepen

U kunt meerdere toepassingen en gebruikers toevoegen aan een Azure AD-groep en vervolgens de groep toegang geven tot uw sleutel kluis.  Zie de sectie [leden maken en toevoegen aan een Azure AD-groep](#creating-and-adding-members-to-an-azure-ad-group) hieronder voor meer informatie.

Als u wilt zoeken naar de objectId van een Azure AD-groep met de Azure CLI, gebruikt u de opdracht [AZ Ad Group List](/cli/azure/ad/group?view=azure-cli-latest#az-ad-group-list) . Vanwege het grote aantal groepen dat zich in uw organisatie kan bevinden, moet u ook een zoek reeks voor de `--display-name` para meter opgeven.

```azurecli-interactive
az ad group list --display-name <search-string>
```
De objectId wordt geretourneerd in de JSON:

```json
    "objectId": "48b21bfb-74d6-48d2-868f-ff9eeaf38a64",
    "objectType": "Group",
    "odata.type": "Microsoft.DirectoryServices.Group",
```

Als u de objectId van een Azure AD-groep met Azure PowerShell wilt zoeken, gebruikt u de cmdlet [Get-AzADGroup](/powershell/module/az.resources/get-azadgroup?view=azps-2.7.0) . Vanwege het grote aantal groepen dat zich in uw organisatie kan bevinden, wilt u waarschijnlijk ook een zoek reeks voor de `-SearchString` para meter opgeven.

```azurepowershell-interactive
Get-AzADGroup -SearchString <search-string>
```

In de uitvoer wordt de objectId vermeld als `Id`:

```console
...
Id                    : 1cef38c4-388c-45a9-b5ae-3d88375e166a
...
```

#### <a name="users"></a>Gebruikers

U kunt ook een afzonderlijke gebruiker toevoegen aan het toegangs beheer beleid van een sleutel kluis. **Dit wordt niet aangeraden.** In plaats daarvan raden we u aan gebruikers toe te voegen aan een Azure AD-groep en de groep toe te voegen aan de beleids regels.

Als u echter een gebruiker wilt vinden met de Azure CLI, gebruikt u de opdracht [AZ AD User show](/cli/azure/ad/user?view=azure-cli-latest#az-ad-user-show) en geeft u het e-mail adres `--id` van de gebruikers door aan de para meter.


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

Als u een gebruiker met Azure PowerShell wilt zoeken, gebruikt u de cmdlet [Get-AzADUser](/powershell/module/az.resources/get-azaduser?view=azps-2.7.0) en geeft u het e `-UserPrincipalName` -mail adres van de gebruikers door aan de para meter.

```azurepowershell-interactive
 Get-AzAdUser -UserPrincipalName <email-address-of-user>
```

De objectId van de gebruiker wordt geretourneerd in de uitvoer als `Id`.

```console
...
Id                : f76a2a6f-3b6d-4735-9abd-14dccbf70fd9
Type              :
```

### <a name="give-the-principal-access-to-your-key-vault"></a>De belangrijkste toegang geven tot uw sleutel kluis

Nu u een objectID hebt van uw Principal, kunt u een toegangs beleid voor de sleutel kluis maken waarmee de machtigingen voor zowel sleutels en geheimen worden opgehaald, weer geven, ingesteld en verwijderd, plus eventuele aanvullende machtigingen die u wenst.

Met de Azure CLI wordt dit gedaan door de objectId door te geven aan de opdracht [AZ-sleutel kluis instellen-Policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) .

```azurecli-interactive
az keyvault set-policy -n <your-unique-keyvault-name> --spn <ApplicationID-of-your-service-principal> --secret-permissions get list set delete --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey
```

Met Azure PowerShell wordt dit gedaan door de objectId door te geven aan de cmdlet [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy?view=azps-2.7.0) . 

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy –VaultName <your-key-vault-name> -PermissionsToKeys create,decrypt,delete,encrypt,get,list,unwrapKey,wrapKey -PermissionsToSecrets get,list,set,delete -ObjectId <Id>

```

## <a name="creating-and-adding-members-to-an-azure-ad-group"></a>Leden maken en toevoegen aan een Azure AD-groep

U kunt een Azure AD-groep maken, toepassingen en gebruikers toevoegen aan de groep en de groep toegang verlenen tot uw sleutel kluis.  Hierdoor kunt u een aantal toepassingen toevoegen aan een sleutel kluis als één vermelding in een toegangs beleid en hoeft u gebruikers geen directe toegang te geven tot uw sleutel kluis (wat er wordt voor komen). Zie [toegang tot apps en bronnen beheren met Azure Active Directory groepen](../../active-directory/fundamentals/active-directory-manage-groups.md)voor meer informatie.

### <a name="additional-prerequisites"></a>Aanvullende vereisten

Naast de [bovenstaande vereisten](#prerequisites)hebt u machtigingen nodig voor het maken/bewerken van groepen in uw Azure Active Directory-Tenant. Als u geen machtigingen hebt, moet u mogelijk contact opnemen met uw Azure Active Directory-beheerder.

Als u Power shell wilt gebruiken, hebt u ook de [Azure AD Power shell-module](https://www.powershellgallery.com/packages/AzureAD/2.0.2.50) nodig

### <a name="create-an-azure-active-directory-group"></a>Een Azure Active Directory groep maken

Maak een nieuwe Azure Active Directory groep met behulp van de Azure CLI [AZ Ad Group Create](/cli/azure/ad/group?view=azure-cli-latest#az-ad-group-create) opdracht of de cmdlet Azure PowerShell [New-AzureADGroup](/powershell/module/azuread/new-azureadgroup?view=azureadps-2.0) .


```azurecli-interactive
az ad group create --display-name <your-group-display-name> --mail-nickname <your-group-mail-nickname>
```

```powershell
New-AzADGroup -DisplayName <your-group-display-name> -MailNickName <your-group-mail-nickname>
```

In beide gevallen noteert u de zojuist gemaakte groep GroupId, zoals u dat nodig hebt voor de volgende stappen.

### <a name="find-the-objectids-of-your-applications-and-users"></a>De Objectid's van uw toepassingen en gebruikers zoeken

U kunt de Objectid's van uw toepassingen vinden met behulp van de Azure CLI met de opdracht [AZ AD SP List](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-list) , met de `--show-mine` para meter.

```azurecli-interactive
az ad sp list --show-mine
```

Zoek de Objectid's van uw toepassingen met behulp van Azure PowerShell met de cmdlet [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal?view=azps-2.7.0) , waarbij een zoek `-SearchString` reeks wordt door gegeven aan de para meter.

```azurepowershell-interactive
Get-AzADServicePrincipal -SearchString <search-string>
```

Volg de stappen in de sectie [gebruikers](#users) hierboven om de objectid's van uw gebruikers te vinden.

### <a name="add-your-applications-and-users-to-the-group"></a>Uw toepassingen en gebruikers toevoegen aan de groep

Voeg nu de Objectid's toe aan de zojuist gemaakte Azure AD-groep.

Met de Azure CLI, gebruikt u de [AZ AD groepslid add](/cli/azure/ad/group/member?view=azure-cli-latest#az-ad-group-member-add), waarbij de objectId wordt door `--member-id` gegeven aan de para meter.


```azurecli-interactive
az ad group member add -g <groupId> --member-id <objectId>
```

Gebruik met Azure PowerShell de cmdlet [add-AzADGroupMember](/powershell/module/az.resources/add-azadgroupmember?view=azps-2.7.0) om de objectId door te geven aan `-MemberObjectId` de para meter.

```azurepowershell-interactive
Add-AzADGroupMember -TargetGroupObjectId <groupId> -MemberObjectId <objectId> 
```

### <a name="give-the-ad-group-access-to-your-key-vault"></a>De AD-groep toegang geven tot uw sleutel kluis

Geef tot slot de AD-groep machtigingen voor uw sleutel kluis met behulp van de Azure CLI [AZ set-Policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) opdracht, of de Azure PowerShell cmdlet [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy?view=azps-2.7.0) . Zie de sectie [de toepassing, Azure AD-groep of gebruikers toegang geven tot uw sleutel kluis](#give-the-principal-access-to-your-key-vault) hierboven voor voor beelden.

De toepassing moet ook ten minste één IAM-rol (Identity and Access Management) hebben die is toegewezen aan de sleutel kluis. Anders is het niet mogelijk om zich aan te melden en mislukt met onvoldoende rechten voor toegang tot het abonnement.

## <a name="next-steps"></a>Volgende stappen

- [Azure Key Vault beveiliging: identiteits-en toegangs beheer](overview-security.md#identity-and-access-management)
- [Key Vault-verificatie bieden met een App Service beheerde identiteit](managed-identity.md)
- [Beveilig uw sleutel kluis](secure-your-key-vault.md)).
- [Ontwikkelaars handleiding Azure Key Vault](developers-guide.md)
- [Azure Key Vault aanbevolen procedures](best-practices.md) controleren
