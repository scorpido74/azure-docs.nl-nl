---
title: Toepassingen toegang verlenen tot een Azure-sleutelkluis - Azure Key Vault | Microsoft Docs
description: Ontdek hoe u veel toepassingen toegang verleent tot een sleutelkluis
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 09/27/2019
ms.author: mbaldwin
ms.openlocfilehash: 0d2666e2b56e73b809a0480d45fa3a4a63f06490
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83652221"
---
# <a name="provide-key-vault-authentication-with-an-access-control-policy"></a>Key Vault-verificatie met een toegangsbeheerbeleid bieden

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

De eenvoudigste manier om een cloudtoepassing toegang te verlenen tot Key Vault, is met een beheerde identiteit. Zie [Een door App Service beheerde identiteit gebruiken om toegang te krijgen tot Azure Key Vault](managed-identity.md) voor meer informatie.  Als u een on-premises toepassing maakt, lokale ontwikkeling uitvoert of op een andere manier geen beheerde identiteit kunt gebruiken, kunt u in plaats daarvan handmatig een service-principal registreren en toegang verlenen tot uw sleutelkluis met behulp van een toegangsbeheerbeleid.  

Key Vault ondersteunt maximaal 1024 toegangsbeleidsregels, waarbij elke vermelding een afzonderlijke set machtigingen verleent aan een ‘principal’:   Dit is bijvoorbeeld hoe de console-app in de [Azure Key Vault-clientbibliotheek voor .NET-quickstart](../secrets/quick-create-net.md) toegang krijgt tot de sleutelkluis.

Zie het volgende artikel voor volledige informatie over toegangsbeheer: [Azure Key Vault-beveiliging: Identiteits- en toegangsbeheer](overview-security.md#identity-and-access-management). Zie de volgende artikelen voor meer informatie over toegangsbeheer: 

- [Sleutels](../keys/index.yml)
- [Toegangsbeheer voor geheimen](../secrets/index.yml)
- [Toegangsbeheer voor certificaten](../certificates/index.yml)

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Vereisten

- Een sleutelkluis. U kunt een bestaande sleutelkluis gebruiken of een nieuwe maken door de stappen in een van deze quickstarts te volgen:
   - [Een sleutelkluis maken met de Azure CLI](../secrets/quick-create-cli.md)
   - [Een sleutelkluis maken met Azure PowerShell](../secrets/quick-create-powershell.md)
   - [Een sleutelkluis maken met de Azure-portal](../secrets/quick-create-portal.md).
- De [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) of [Azure PowerShell](/powershell/azure/overview). U kunt ook de [Azure-portal](https://portal.azure.com) gebruiken.

## <a name="grant-access-to-your-key-vault"></a>Toegang verlenen tot uw sleutelkluis

Elk toegangsbeleid voor een sleutelkluis geeft een afzonderlijke set machtigingen aan een principal:

- **Een toepassing** Als de toepassing zich in de cloud bevindt, moet u [een beheerde identiteit gebruiken om toegang te krijgen tot Azure Key Vault](managed-identity.md), indien mogelijk
- **Een Azure Active Directory-groep** Hoewel Key Vault alleen ondersteuning biedt voor 1024 vermeldingen van toegangsbeleid, kunt u meerdere toepassingen en gebruikers toevoegen aan één Azure Active Directory-groep en deze groep vervolgens als één vermelding toevoegen aan uw toegangscontrolebeleid.
- **Een gebruiker** Het wordt **afgeraden** om gebruikers directe toegang te verlenen. In het ideale geval worden gebruikers toegevoegd aan een Azure Active Directory-groep, die vervolgens toegang krijgt tot de sleutelkluis. Zie [Azure Key Vault-beveiliging: Identiteits- en toegangsbeheer](overview-security.md#identity-and-access-management).


### <a name="get-the-objectid"></a>De object-id ophalen

Als u een toepassing, een Azure Active Directory-groep of een gebruiker toegang wilt geven tot uw sleutelkluis, moet u eerst de object-id ophalen.

#### <a name="applications"></a>Toepassingen

De object-id voor een toepassing komt overeen met de bijbehorende service-principal. Voor volledige informatie over service-principals. raadpleegt u [Toepassings- en service-principal-objecten in Azure Active Directory](../../active-directory/develop/app-objects-and-service-principals.md). 

Er zijn twee manieren om een object-id voor een toepassing te verkrijgen.  Allereerst kunt u de toepassing registreren bij Azure Active Directory. Volg hiervoor de stappen in de quickstart [Een toepassing registreren bij het Microsoft Identity Platform](../../active-directory/develop/quickstart-register-app.md). Wanneer de registratie is voltooid, wordt de object-id vermeld als de 'Toepassings-id (client)'.

De tweede manier is het maken van een service-principal in een terminalvenster. Gebruik de Azure CLI met de opdracht [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) en geef een unieke naam op voor de service-principal via de -n-vlag in de indeling ‘ http://&lt;my-unique-service-principle-name&gt;’.

```azurecli-interactive
az ad sp create-for-rbac -n "http://<my-unique-service-principle-name"
```

De object-id wordt weer gegeven in de uitvoer als `clientID`.

Gebruik met Azure PowerShell de cmdlet [New-AzADServicePrincipal](/powershell/module/Az.Resources/New-AzADServicePrincipal?view=azps-2.7.0).


```azurepowershell-interactive
New-AzADServicePrincipal -DisplayName <my-unique-service-principle-name>
```

De object-id wordt weer gegeven in de uitvoer als `Id` (niet `ApplicationId`).

#### <a name="azure-ad-groups"></a>Azure Active Directory-groepen

U kunt meerdere toepassingen en gebruikers toevoegen aan een Azure Active Directory-groep en vervolgens de groep toegang geven tot uw sleutelkluis.  Zie voor meer informatie de onderstaande sectie [Leden maken en toevoegen aan een Azure Active Directory-groep](#creating-and-adding-members-to-an-azure-ad-group).

Als u de object-id van een Azure Active Directory-groep wilt vinden met de Azure CLI, gebruikt u de opdracht [az ad group list](/cli/azure/ad/group?view=azure-cli-latest#az-ad-group-list). Vanwege het grote aantal groepen dat zich in uw organisatie kan bevinden, moet u ook een zoekreeks opgeven voor de parameter `--display-name`.

```azurecli-interactive
az ad group list --display-name <search-string>
```
De object-id wordt geretourneerd in de JSON:

```json
    "objectId": "48b21bfb-74d6-48d2-868f-ff9eeaf38a64",
    "objectType": "Group",
    "odata.type": "Microsoft.DirectoryServices.Group",
```

Gebruik de cmdlet [Get-AzADGroup](/powershell/module/az.resources/get-azadgroup?view=azps-2.7.0) om de object-id van een Azure Active Directory-groep te vinden met Azure PowerShell. Vanwege het grote aantal groepen dat zich in uw organisatie kan bevinden, is het verstandig om ook een zoekreeks op te geven voor de parameter `-SearchString`.

```azurepowershell-interactive
Get-AzADGroup -SearchString <search-string>
```

In de uitvoer wordt de object-id vermeld als `Id`:

```console
...
Id                    : 1cef38c4-388c-45a9-b5ae-3d88375e166a
...
```

#### <a name="users"></a>Gebruikers

U kunt ook een afzonderlijke gebruiker toevoegen aan het toegangsbeheerbeleid van een sleutelkluis. **Dit wordt niet aangeraden.** In plaats daarvan raden we u aan gebruikers toe te voegen aan een Azure Active Directory-groep en de groep toe te voegen aan de beleidsregels.

Als u toch een gebruiker wilt zoeken met de Azure CLI, gebruikt u de opdracht [az ad user show](/cli/azure/ad/user?view=azure-cli-latest#az-ad-user-show), waarbij het e-mailadres van de gebruiker wordt doorgegeven aan de parameter `--id`.


```azurecli-interactive
az ad user show --id <email-address-of-user>
```

De object-id van de gebruiker wordt geretourneerd in de uitvoer:

```console
  ...
  "objectId": "f76a2a6f-3b6d-4735-9abd-14dccbf70fd9",
  "objectType": "User",
  ...
```

Als u een gebruiker met Azure PowerShell wilt zoeken, gebruikt u de cmdlet [Get-AzADUser](/powershell/module/az.resources/get-azaduser?view=azps-2.7.0) om het e-mailadres van de gebruikers door te geven aan de parameter `-UserPrincipalName`.

```azurepowershell-interactive
 Get-AzAdUser -UserPrincipalName <email-address-of-user>
```

De object-id van de gebruiker wordt geretourneerd in de uitvoer als `Id`.

```console
...
Id                : f76a2a6f-3b6d-4735-9abd-14dccbf70fd9
Type              :
```

### <a name="give-the-principal-access-to-your-key-vault"></a>De principal toegang geven tot uw sleutelkluis

Nu u een object-id hebt van uw principal, kunt u een toegangsbeleid voor de sleutelkluis maken waarmee de machtigingen voor zowel sleutels en geheimen worden opgehaald, weergeven, ingesteld en verwijderd. Daarnaast kunt u eventuele aanvullende machtigingen ophalen.

Met de Azure CLI wordt dit gedaan door de object-id door te geven aan de opdracht [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy).

```azurecli-interactive
az keyvault set-policy -n <your-unique-keyvault-name> --spn <ApplicationID-of-your-service-principal> --secret-permissions get list set delete --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey
```

Met Azure PowerShell wordt dit gedaan door de object-id door te geven aan de cmdlet [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy?view=azps-2.7.0). 

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy –VaultName <your-key-vault-name> -PermissionsToKeys create,decrypt,delete,encrypt,get,list,unwrapKey,wrapKey -PermissionsToSecrets get,list,set,delete -ObjectId <Id>

```

## <a name="creating-and-adding-members-to-an-azure-ad-group"></a>Leden maken en toevoegen aan een Azure Active Directory-groep

U kunt een Azure Active Directory-groep maken, toepassingen en gebruikers toevoegen en vervolgens de groep toegang geven tot uw sleutelkluis.  Hierdoor kunt u een aantal toepassingen toevoegen aan een sleutelkluis als één vermelding in een toegangsbeleid en hoeft u gebruikers geen directe toegang te geven tot uw sleutelkluis (wat wordt afgeraden). Zie [Toegang van apps en resources beheren met behulp van Azure Active Directory-groepen](../../active-directory/fundamentals/active-directory-manage-groups.md) voor meer informatie.

### <a name="additional-prerequisites"></a>Aanvullende vereisten

Naast de [bovenstaande vereisten](#prerequisites) hebt u machtigingen nodig voor het maken/bewerken van groepen in uw Azure Active Directory-tenant. Als u geen machtigingen hebt, moet u mogelijk contact opnemen met uw Azure Active Directory-beheerder.

Als u PowerShell wilt gebruiken, hebt u ook de [Azure Active Directory PowerShell-module nodig](https://www.powershellgallery.com/packages/AzureAD/2.0.2.50)

### <a name="create-an-azure-active-directory-group"></a>Een Azure Active Directory-groep maken

Maak een nieuwe Azure Active Directory-groep met behulp van de opdracht [az ad group create](/cli/azure/ad/group?view=azure-cli-latest#az-ad-group-create) in de Azure CLI of de cmdlet [New-AzureADGroup](/powershell/module/azuread/new-azureadgroup?view=azureadps-2.0) in Azure PowerShell.


```azurecli-interactive
az ad group create --display-name <your-group-display-name> --mail-nickname <your-group-mail-nickname>
```

```powershell
New-AzADGroup -DisplayName <your-group-display-name> -MailNickName <your-group-mail-nickname>
```

In beide gevallen noteert u de groeps-id van de zojuist gemaakte groep, omdat u deze nodig hebt voor de volgende stappen.

### <a name="find-the-objectids-of-your-applications-and-users"></a>De object-id's van uw toepassingen en gebruikers zoeken

U kunt de object-id's van uw toepassingen vinden met behulp van de Azure CLI met de opdracht [az ad sp list](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-list) en de parameter `--show-mine`.

```azurecli-interactive
az ad sp list --show-mine
```

Zoek de object-id's van uw toepassingen met behulp van Azure PowerShell met de cmdlet [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal?view=azps-2.7.0), waarbij een zoekreeks wordt doorgegeven aan de parameter `-SearchString`.

```azurepowershell-interactive
Get-AzADServicePrincipal -SearchString <search-string>
```

Volg de stappen in de sectie [Gebruikers](#users) hierboven om de object-id's van uw gebruikers te vinden.

### <a name="add-your-applications-and-users-to-the-group"></a>Uw toepassingen en gebruikers toevoegen aan de groep

Voeg nu de object-id's toe aan de zojuist gemaakte Azure Active Directory-groep.

Met de Azure CLI gebruikt u [az ad group member add](/cli/azure/ad/group/member?view=azure-cli-latest#az-ad-group-member-add), waarbij de object-id wordt doorgegeven aan de parameter `--member-id`.


```azurecli-interactive
az ad group member add -g <groupId> --member-id <objectId>
```

Met Azure PowerShell gebruikt u de cmdlet [Add-AzADGroupMember](/powershell/module/az.resources/add-azadgroupmember?view=azps-2.7.0) om de object-id door te geven aan de parameter `-MemberObjectId`.

```azurepowershell-interactive
Add-AzADGroupMember -TargetGroupObjectId <groupId> -MemberObjectId <objectId> 
```

### <a name="give-the-ad-group-access-to-your-key-vault"></a>De AD-groep toegang geven tot uw sleutelkluis

Geef de AD-groep tot slot toegang tot uw sleutelkluis met behulp van de Azure CLI-opdracht [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) of de Azure PowerShell-cmdlet [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy?view=azps-2.7.0). Zie voor voorbeelden de bovenstaande sectie [De toepassing, Azure Active Directory-groep of gebruiker toegang geven tot uw sleutelkluis](#give-the-principal-access-to-your-key-vault).

De toepassing moet ook ten minste één IAM-rol (Identiteits- en toegangsbeheer) hebben die is toegewezen aan de sleutelkluis. Anders kan de toepassing zich niet aanmelden en mislukt deze door onvoldoende toegangsrechten tot het abonnement.

> [!WARNING]
> De tokens van Azure Active Directory-groepen met beheerde identiteiten hebben mogelijk 8 uur nodig om te vernieuwen en effectief te worden.

## <a name="next-steps"></a>Volgende stappen

- [Azure Key Vault-beveiliging: Identiteits- en toegangsbeheer](overview-security.md#identity-and-access-management)
- [Key Vault-verificatie bieden met een in App Service beheerde identiteit](managed-identity.md)
- [Uw sleutelkluis beveiligen](secure-your-key-vault.md)).
- [Gids voor Azure Key Vault-ontwikkelaars](developers-guide.md)
- Bekijk de [best practices voor Azure Key Vault](best-practices.md)
