---
title: Beheerd opslag account Azure Key Vault-Power shell-versie
description: De functie Managed Storage-account biedt een schijnloze integratie tussen Azure Key Vault en een Azure-opslag account.
ms.topic: conceptual
ms.service: key-vault
author: msmbaldwin
ms.author: mbaldwin
manager: barbkess
ms.date: 03/01/2019
ms.openlocfilehash: 530f38289afb3fce85bbb025e7b699862eedbadc
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/05/2019
ms.locfileid: "70382702"
---
# <a name="azure-key-vault-managed-storage-account---powershell"></a>Beheerd opslag account Azure Key Vault-Power shell

> [!NOTE]
> [Azure Storage-integratie met Azure Active Directory (Azure AD) is nu](../storage/common/storage-auth-aad.md)beschikbaar als preview-versie. We raden u aan Azure AD te gebruiken voor verificatie en autorisatie, waarmee u toegang krijgt tot Azure-opslag op basis van OAuth2-tokens, net als bij Azure Key Vault. Hierdoor kunt u:
> - Verifieer uw client toepassing met behulp van een toepassings-of gebruikers-id in plaats van de referenties van het opslag account. 
> - Gebruik een door [Azure AD beheerde identiteit](/azure/active-directory/managed-identities-azure-resources/) als deze wordt uitgevoerd op Azure. Beheerde identiteiten verwijderen de nood zaak voor client verificatie helemaal en het opslaan van referenties in of met uw toepassing.
> - Gebruik op rollen gebaseerd Access Control (RBAC) voor het beheren van autorisatie, die ook door Key Vault wordt ondersteund.
> - AAD-toegang tot het opslag account werkt niet voor toegang tot tabellen vanaf nog.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Een [Azure-opslag account](/azure/storage/storage-create-storage-account) gebruikt een referentie die bestaat uit een account naam en een sleutel. De sleutel wordt automatisch gegenereerd en fungeert als een ' wacht woord ' in plaats van een cryptografische sleutel. Key Vault kunt deze sleutels voor opslag accounts beheren door ze op te slaan als [Key Vault geheimen](/azure/key-vault/about-keys-secrets-and-certificates#key-vault-secrets). 

## <a name="overview"></a>Overzicht

Met de functie Managed Storage account Key Vault worden namens u verschillende beheer taken uitgevoerd:

- Lijst met sleutels (synchroniseert) met een Azure-opslag account.
- Hiermee worden de sleutels periodiek opnieuw gegenereerd (gedraaid).
- Beheert sleutels voor zowel opslag accounts als klassieke opslag accounts.
- Sleutel waarden worden nooit geretourneerd als antwoord op de aanroeper.

Wanneer u de sleutel functie voor beheerde opslag accounts gebruikt:

- **Alleen Key Vault toestaan om de sleutels van uw opslag account te beheren.** Probeer ze zelf niet zelf te beheren, omdat u de processen van Key Vault verstoort.
- **Niet toestaan dat opslag account sleutels worden beheerd door meer dan één Key Vault-object**.
- **De sleutels van uw opslag account niet hand matig opnieuw genereren**. We raden u aan deze opnieuw te genereren via Key Vault.

In het volgende voor beeld ziet u hoe u Key Vault kunt toestaan om de sleutels van uw opslag account te beheren.

## <a name="connect-to-your-azure-account"></a>Verbinding maken met uw Azure-account

Verifieer uw Power shell-sessie met de cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) . 
```azurepowershell-interactive
Connect-AzAccount
```
Als u meerdere Azure-abonnementen hebt, kunt u deze weer geven met behulp van de cmdlet [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription?view=azps-2.5.0) en het abonnement opgeven dat u wilt gebruiken met de cmdlet [set-AzContext](/powershell/module/az.accounts/set-azcontext?view=azps-2.5.0) . 

```azurepowershell-interactive
Set-AzContext -SubscriptionId <subscriptionId>
```

## <a name="authorize-key-vault-to-access-to-your-storage-account"></a>Key Vault toestemming geven om toegang te krijgen tot uw opslag account

> [!IMPORTANT]
> Een Azure AD-Tenant voorziet elke geregistreerde toepassing van een **[Service-Principal](/azure/active-directory/develop/developer-glossary#service-principal-object)** , die fungeert als de identiteit van de toepassing. De toepassings-ID van de service-principal wordt gebruikt bij het verlenen van autorisatie voor toegang tot andere Azure-resources, via op rollen gebaseerd toegangs beheer (RBAC). Omdat Key Vault een micro soft-toepassing is, wordt deze vooraf geregistreerd in alle Azure AD-tenants onder dezelfde toepassings-ID, binnen elke Azure-Cloud:
> - Azure AD-tenants in de Azure Government-Cloud `7e7c393b-45d0-48b1-a35e-2905ddf8183c`gebruiken toepassings-id.
> - Azure AD-tenants in de open bare Azure-Cloud en alle `cfa8b339-82a2-471a-a3c9-0fc0be7a4093`andere toepassingen gebruiken toepassings-id.

Voordat Key Vault toegang tot de sleutels van uw opslag account kunt krijgen en beheren, moet u de toegang tot uw opslag account autoriseren. Voor de Key Vault toepassing zijn machtigingen vereist om sleutels voor uw opslag account *weer te geven* en *opnieuw te genereren* . Deze machtigingen worden ingeschakeld via de ingebouwde rol van de provider voor het [opslag account](/azure/role-based-access-control/built-in-roles#storage-account-key-operator-service-role)van de rol van RBAC Role. 

Wijs deze rol toe aan de Key Vault Service-Principal, waardoor het bereik wordt beperkt tot uw opslag account, met behulp van de volgende stappen. Zorg ervoor dat u de `$resourceGroupName`variabelen `$storageAccountName`, `$storageAccountKey`, en `$keyVaultName` bijwerkt voordat u het script uitvoert:

```azurepowershell-interactive
# TODO: Update with the resource group where your storage account resides, your storage account name, the name of your active storage account key, and your Key Vault instance name
$resourceGroupName = "rgContoso"
$storageAccountName = "sacontoso"
$storageAccountKey = "key1"
$keyVaultName = "kvContoso"
$keyVaultSpAppId = "cfa8b339-82a2-471a-a3c9-0fc0be7a4093" # See "IMPORTANT" block above for information on Key Vault Application IDs

# Get your User Id for later commands
$userId = (Get-AzContext).Account.Id

# Get a reference to your Azure storage account
$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName

# Assign RBAC role "Storage Account Key Operator Service Role" to Key Vault, limiting the access scope to your storage account. For a classic storage account, use "Classic Storage Account Key Operator Service Role." 
New-AzRoleAssignment -ApplicationId $keyVaultSpAppId -RoleDefinitionName 'Storage Account Key Operator Service Role' -Scope $storageAccount.Id
```

Bij een geslaagde roltoewijzing ziet u uitvoer die lijkt op het volgende voor beeld:

```console
RoleAssignmentId   : /subscriptions/03f0blll-ce69-483a-a092-d06ea46dfb8z/resourceGroups/rgContoso/providers/Microsoft.Storage/storageAccounts/sacontoso/providers/Microsoft.Authorization/roleAssignments/189cblll-12fb-406e-8699-4eef8b2b9ecz
Scope              : /subscriptions/03f0blll-ce69-483a-a092-d06ea46dfb8z/resourceGroups/rgContoso/providers/Microsoft.Storage/storageAccounts/sacontoso
DisplayName        : Azure Key Vault
SignInName         :
RoleDefinitionName : storage account Key Operator Service Role
RoleDefinitionId   : 81a9662b-bebf-436f-a333-f67b29880f12
ObjectId           : 93c27d83-f79b-4cb2-8dd4-4aa716542e74
ObjectType         : ServicePrincipal
CanDelegate        : False
```

Als Key Vault al is toegevoegd aan de rol van uw opslag account, ontvangt u een *' de roltoewijzing bestaat al. '* optreedt. U kunt ook de roltoewijzing controleren met behulp van de pagina toegangs beheer (IAM) van het opslag account in de Azure Portal.  

## <a name="give-your-user-account-permission-to-managed-storage-accounts"></a>Uw gebruikers account machtigingen geven voor beheerde opslag accounts

>[!TIP] 
> Net zoals Azure AD voorziet in een **Service-Principal** voor de identiteit van een toepassing, wordt een gebruikers- **Principal** verstrekt voor de identiteit van een gebruiker. De gebruikers-principal kan vervolgens worden gemachtigd om toegang te krijgen tot Key Vault via Key Vault machtigingen voor toegangs beleid.

Werk met dezelfde Power shell-sessie het Key Vault toegangs beleid voor beheerde opslag accounts bij. Met deze stap worden machtigingen voor opslag accounts toegepast op uw gebruikers account, zodat u toegang hebt tot de functies van het Managed Storage-account: 

```azurepowershell-interactive
# Give your user principal access to all storage account permissions, on your Key Vault instance

Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -UserPrincipalName $userId -PermissionsToStorage get, list, delete, set, update, regeneratekey, getsas, listsas, deletesas, setsas, recover, backup, restore, purge
```

Houd er rekening mee dat machtigingen voor opslag accounts niet beschikbaar zijn op de pagina toegangs beleid van het opslag account in de Azure Portal.

## <a name="add-a-managed-storage-account-to-your-key-vault-instance"></a>Een beheerd opslag account toevoegen aan uw Key Vault-exemplaar

Maak met dezelfde Power shell-sessie een beheerd opslag account in uw Key Vault-exemplaar. Met `-DisableAutoRegenerateKey` de schakel optie wordt de sleutel van het opslag account niet opnieuw gegenereerd.

```azurepowershell-interactive
# Add your storage account to your Key Vault's managed storage accounts
Add-AzKeyVaultManagedStorageAccount -VaultName $keyVaultName -AccountName $storageAccountName -AccountResourceId $storageAccount.Id -ActiveKeyName $storageAccountKey -DisableAutoRegenerateKey
```

Wanneer het opslag account zonder sleutel opnieuw genereren is toegevoegd, ziet u uitvoer die lijkt op het volgende voor beeld:

```console
Id                  : https://kvcontoso.vault.azure.net:443/storage/sacontoso
Vault Name          : kvcontoso
AccountName         : sacontoso
Account Resource Id : /subscriptions/03f0blll-ce69-483a-a092-d06ea46dfb8z/resourceGroups/rgContoso/providers/Microsoft.Storage/storageAccounts/sacontoso
Active Key Name     : key1
Auto Regenerate Key : False
Regeneration Period : 90.00:00:00
Enabled             : True
Created             : 11/19/2018 11:54:47 PM
Updated             : 11/19/2018 11:54:47 PM
Tags                : 
```

### <a name="enable-key-regeneration"></a>Sleutel opnieuw genereren inschakelen

Als u wilt dat Key Vault de sleutels van uw opslag account periodiek opnieuw genereert, kunt u een periode voor opnieuw genereren instellen. In het volgende voor beeld wordt een periode voor het opnieuw genereren van drie dagen ingesteld. Na drie dagen genereert Key Vault Key2 opnieuw en wordt de actieve sleutel van Key2 naar Key1 gewisseld.

```azurepowershell-interactive
$regenPeriod = [System.Timespan]::FromDays(3)
Add-AzKeyVaultManagedStorageAccount -VaultName $keyVaultName -AccountName $storageAccountName -AccountResourceId $storageAccount.Id -ActiveKeyName $storageAccountKey -RegenerationPeriod $regenPeriod
```

Wanneer het opslag account is toegevoegd aan het opnieuw genereren van sleutels, ziet u uitvoer die lijkt op het volgende voor beeld:

```console
Id                  : https://kvcontoso.vault.azure.net:443/storage/sacontoso
Vault Name          : kvcontoso
AccountName         : sacontoso
Account Resource Id : /subscriptions/03f0blll-ce69-483a-a092-d06ea46dfb8z/resourceGroups/rgContoso/providers/Microsoft.Storage/storageAccounts/sacontoso
Active Key Name     : key1
Auto Regenerate Key : True
Regeneration Period : 3.00:00:00
Enabled             : True
Created             : 11/19/2018 11:54:47 PM
Updated             : 11/19/2018 11:54:47 PM
Tags                : 
```

## <a name="next-steps"></a>Volgende stappen

- [Sleutel voorbeelden van beheerde opslag accounts](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=)
- [Informatie over sleutels, geheimen en certificaten](about-keys-secrets-and-certificates.md)
- [Key Vault Power shell-referentie](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault)
