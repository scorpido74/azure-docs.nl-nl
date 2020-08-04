---
title: Beheerd opslag account Azure Key Vault-Power shell-versie
description: De functie Managed Storage-account biedt een naadloze integratie tussen Azure Key Vault en een Azure Storage-account.
ms.topic: conceptual
ms.service: key-vault
ms.subservice: secrets
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/10/2019
ms.openlocfilehash: 87dc1ccb887638226607a1e398c7532de8d2c94f
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/03/2020
ms.locfileid: "87534529"
---
# <a name="manage-storage-account-keys-with-key-vault-and-azure-powershell"></a>Sleutels voor opslag accounts beheren met Key Vault en Azure PowerShell

Een Azure-opslag account gebruikt referenties die bestaan uit een account naam en een sleutel. De sleutel wordt automatisch gegenereerd en fungeert als een wacht woord in plaats van een als cryptografische sleutel. Key Vault beheert de sleutels van het opslag account door ze periodiek opnieuw te genereren in het opslag account en de tokens voor gedeelde toegangs handtekeningen te bieden voor gedelegeerde toegang tot resources in uw opslag account.

U kunt met behulp van de sleutel functie voor de Key Vault beheerde opslag accounts een lijst (synchronisatie) met een Azure-opslag account maken en de sleutels periodiek opnieuw genereren (draaien). U kunt sleutels voor zowel opslag accounts als klassieke opslag accounts beheren.

Houd rekening met de volgende punten wanneer u de sleutel functie beheerde opslag account gebruikt:

- Sleutel waarden worden nooit geretourneerd als antwoord op een aanroeper.
- Alleen Key Vault moet de sleutels van uw opslag account beheren. Beheer de sleutels niet zelf en voorkom conflicten met Key Vault processen.
- Alleen een enkel Key Vault-object moet de sleutels van het opslag account beheren. Geen sleutel beheer van meerdere objecten toestaan.
- U kunt Key Vault aanvragen om uw opslag account te beheren met een gebruikers-principal, maar niet met een service-principal.
- Sleutels opnieuw genereren met behulp van alleen Key Vault. De sleutels van uw opslag account niet hand matig opnieuw genereren.

U kunt het beste Azure Storage Integration gebruiken met Azure Active Directory (Azure AD), de cloud-gebaseerde identiteits-en toegangs beheer service van micro soft. Azure AD-integratie is beschikbaar voor [Azure-blobs en-wacht rijen](../../storage/common/storage-auth-aad.md)en biedt toegang tot Azure Storage op basis van OAuth2-tokens (net als Azure Key Vault).

Met Azure AD kunt u uw client toepassing verifiëren met behulp van een toepassings-of gebruikers-id in plaats van de referenties van het opslag account. U kunt een door [Azure AD beheerde identiteit](/azure/active-directory/managed-identities-azure-resources/) gebruiken wanneer u in azure uitvoert. Beheerde identiteiten verwijderen de nood zaak voor client verificatie en het opslaan van referenties in of met uw toepassing.

Azure AD gebruikt op rollen gebaseerd toegangs beheer (RBAC) voor het beheren van autorisatie, die ook door Key Vault wordt ondersteund.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="service-principal-application-id"></a>Service-Principal-toepassings-ID

Een Azure AD-Tenant voorziet elke geregistreerde toepassing van een [Service-Principal](/azure/active-directory/develop/developer-glossary#service-principal-object). De Service-Principal fungeert als de toepassings-ID, die wordt gebruikt tijdens het instellen van de autorisatie voor toegang tot andere Azure-resources via RBAC.

Key Vault is een micro soft-toepassing die vooraf is geregistreerd in alle Azure AD-tenants. Key Vault is geregistreerd onder dezelfde toepassings-ID in elke Azure-Cloud.

| Tenants | Cloud | Toepassings-id |
| --- | --- | --- |
| Azure AD | Azure Government | `7e7c393b-45d0-48b1-a35e-2905ddf8183c` |
| Azure AD | Openbare Azure-peering | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |
| Anders  | Alle | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |

## <a name="prerequisites"></a>Vereisten

Als u deze hand leiding wilt volt ooien, moet u eerst het volgende doen:

- [Installeer de Azure PowerShell-module](/powershell/azure/install-az-ps?view=azps-2.6.0).
- [Een sleutel kluis maken](quick-create-powershell.md)
- [Maak een Azure-opslag account](../../storage/common/storage-account-create.md?tabs=azure-powershell). De naam van het opslag account mag alleen kleine letters en cijfers bevatten. De naam moet tussen de 3 en 24 tekens lang zijn.
      

## <a name="manage-storage-account-keys"></a>Sleutels voor opslag accounts beheren

### <a name="connect-to-your-azure-account"></a>Verbinding maken met uw Azure-account

Verifieer uw Power shell-sessie met de cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) . 

```azurepowershell-interactive
Connect-AzAccount
```
Als u meerdere Azure-abonnementen hebt, kunt u deze weer geven met behulp van de cmdlet [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription?view=azps-2.5.0) en het abonnement opgeven dat u wilt gebruiken met de cmdlet [set-AzContext](/powershell/module/az.accounts/set-azcontext?view=azps-2.5.0) . 

```azurepowershell-interactive
Set-AzContext -SubscriptionId <subscriptionId>
```

### <a name="set-variables"></a>Variabelen instellen

Stel eerst de variabelen in die moeten worden gebruikt door de Power shell-cmdlets in de volgende stappen. Zorg ervoor dat u de <YourResourceGroupName> <YourStorageAccountName> tijdelijke aanduidingen,,, en <YourKeyVaultName> $keyVaultSpAppId instelt op `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` (zoals opgegeven in de [Service Principal Application id](#service-principal-application-id)hierboven).

We gebruiken ook de cmdlets Azure PowerShell [Get-AzContext](/powershell/module/az.accounts/get-azcontext?view=azps-2.6.0) en [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount?view=azps-2.6.0) om uw gebruikers-id en de context van uw Azure Storage-account op te halen.

```azurepowershell-interactive
$resourceGroupName = <YourResourceGroupName>
$storageAccountName = <YourStorageAccountName>
$keyVaultName = <YourKeyVaultName>
$keyVaultSpAppId = "cfa8b339-82a2-471a-a3c9-0fc0be7a4093"
$storageAccountKey = "key1" #(key1 or key2 are allowed)

# Get your User Id
$userId = (Get-AzContext).Account.Id

# Get a reference to your Azure storage account
$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName

```
>[!Note]
> Gebruik voor klassiek opslag account ' primair ' en ' secundair ' voor $storageAccountKey <br>
> Gebruik ' Get-AzResource-name ' ClassicStorageAccountName '-ResourceGroupName $resourceGroupName ' in plaats of'Get-AzStorageAccount ' voor een klassiek opslag account

### <a name="give-key-vault-access-to-your-storage-account"></a>Key Vault toegang geven tot uw opslag account

Voordat Key Vault toegang tot de sleutels van uw opslag account kunt krijgen en beheren, moet u de toegang tot uw opslag account autoriseren. Voor de Key Vault toepassing zijn machtigingen vereist om sleutels voor uw opslag account *weer te geven* en *opnieuw te genereren* . Deze machtigingen worden ingeschakeld via de rol van Azure ingebouwde rol [opslag account voor de operator service](/azure/role-based-access-control/built-in-roles#storage-account-key-operator-service-role). 

Wijs deze rol toe aan de Key Vault Service-Principal, waardoor het bereik wordt beperkt tot uw opslag account met behulp van de Azure PowerShell [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment?view=azps-2.6.0) cmdlet.

```azurepowershell-interactive
# Assign Azure role "Storage Account Key Operator Service Role" to Key Vault, limiting the access scope to your storage account. For a classic storage account, use "Classic Storage Account Key Operator Service Role." 
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

Als Key Vault al is toegevoegd aan de rol van uw opslag account, ontvangt u een *' de roltoewijzing bestaat al. '* fout. U kunt ook de roltoewijzing controleren met behulp van de pagina toegangs beheer (IAM) van het opslag account in de Azure Portal.  

### <a name="give-your-user-account-permission-to-managed-storage-accounts"></a>Uw gebruikers account machtigingen geven voor beheerde opslag accounts

Gebruik de Azure PowerShell [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy?view=azps-2.6.0) cmdlet om het toegangs beleid voor Key Vault bij te werken en machtigingen voor het opslag account te verlenen aan uw gebruikers account.

```azurepowershell-interactive
# Give your user principal access to all storage account permissions, on your Key Vault instance

Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -UserPrincipalName $userId -PermissionsToStorage get, list, delete, set, update, regeneratekey, getsas, listsas, deletesas, setsas, recover, backup, restore, purge
```

Houd er rekening mee dat machtigingen voor opslag accounts niet beschikbaar zijn op de pagina toegangs beleid van het opslag account in de Azure Portal.

### <a name="add-a-managed-storage-account-to-your-key-vault-instance"></a>Een beheerd opslag account toevoegen aan uw Key Vault-exemplaar

Gebruik de Azure PowerShell cmdlet [add-AzKeyVaultManagedStorageAccount](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount?view=azps-2.6.0) voor het maken van een beheerd opslag account in uw Key Vault-exemplaar. `-DisableAutoRegenerateKey`Met de schakel optie wordt de sleutel van het opslag account niet opnieuw gegenereerd.

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

Als u wilt dat Key Vault de sleutels van uw opslag account periodiek opnieuw genereert, kunt u de cmdlet Azure PowerShell [add-AzKeyVaultManagedStorageAccount](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount?view=azps-2.6.0) gebruiken om een nieuwe periode in te stellen. In dit voor beeld stellen we een regeneratie periode van drie dagen in. Wanneer het tijd is om te draaien, Key Vault opnieuw genereren van de sleutel die niet actief is. vervolgens wordt de zojuist gemaakte sleutel als actief ingesteld. Slechts een van de sleutels wordt gebruikt voor het verlenen van SAS-tokens tegelijk. Dit is de actieve sleutel.

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

## <a name="shared-access-signature-tokens"></a>Shared Access Signature-tokens

U kunt ook Key Vault vragen om de tokens voor Shared Access-hand tekeningen te genereren. Een Shared Access Signature biedt gedelegeerde toegang tot resources in uw opslag account. U kunt clients toegang verlenen tot resources in uw opslag account zonder uw account sleutels te delen. Een Shared Access Signature biedt u een veilige manier om uw opslag resources te delen zonder in te boeten voor uw account sleutels.

De opdrachten in deze sectie voeren de volgende acties uit:

- Stel de definitie van een gedeelde toegangs handtekening voor een account in. 
- Maak een account voor Shared Access Signature-token voor blob-, bestands-, tabel-en wachtrij Services. Het token is gemaakt voor resource type-service,-container en-object. Het token wordt gemaakt met alle machtigingen, via https en met de opgegeven begin-en eind datum.
- Stel in de kluis een definitie in van een Key Vault Managed Storage-hand tekening voor gedeelde opslag. De definitie heeft de sjabloon-URI van het Shared Access Signature-token dat is gemaakt. De definitie heeft het type Shared Access Signature `account` en is N dagen geldig.
- Controleer of de Shared Access-hand tekening is opgeslagen in de sleutel kluis als een geheim.
- 
### <a name="set-variables"></a>Variabelen instellen

Stel eerst de variabelen in die moeten worden gebruikt door de Power shell-cmdlets in de volgende stappen. Zorg ervoor dat u de <YourStorageAccountName> en <YourKeyVaultName> tijdelijke aanduidingen bijwerkt.

We gebruiken ook de Azure PowerShell [New-AzStorageContext](/powershell/module/az.storage/new-azstoragecontext?view=azps-2.6.0) -cmdlets om de context van uw Azure Storage-account op te halen.

```azurepowershell-interactive
$storageAccountName = <YourStorageAccountName>
$keyVaultName = <YourKeyVaultName>

$storageContext = New-AzStorageContext -StorageAccountName $storageAccountName -Protocol Https -StorageAccountKey Key1 #(or "Primary" for Classic Storage Account)
```

### <a name="create-a-shared-access-signature-token"></a>Een token voor een Shared Access-hand tekening maken

Maak een definitie van een hand tekening voor gedeelde toegang met behulp van de Azure PowerShell [New-AzStorageAccountSASToken-](/powershell/module/az.storage/new-azstorageaccountsastoken?view=azps-2.6.0) cmdlets.
 
```azurepowershell-interactive
$start = [System.DateTime]::Now.AddDays(-1)
$end = [System.DateTime]::Now.AddMonths(1)

$sasToken = New-AzStorageAccountSasToken -Service blob,file,Table,Queue -ResourceType Service,Container,Object -Permission "racwdlup" -Protocol HttpsOnly -StartTime $start -ExpiryTime $end -Context $storageContext
```
De waarde van $sasToken ziet er ongeveer als volgt uit.

```console
?sv=2018-11-09&sig=5GWqHFkEOtM7W9alOgoXSCOJO%2B55qJr4J7tHQjCId9S%3D&spr=https&st=2019-09-18T18%3A25%3A00Z&se=2019-10-19T18%3A25%3A00Z&srt=sco&ss=bfqt&sp=racupwdl
```

### <a name="generate-a-shared-access-signature-definition"></a>Een definitie van een gedeelde Access-hand tekening genereren

Gebruik de Azure PowerShell [set-AzKeyVaultManagedStorageSasDefinition](/powershell/module/az.keyvault/set-azkeyvaultmanagedstoragesasdefinition?view=azps-2.6.0) cmdlet om een definitie van een Shared Access-hand tekening te maken.  U kunt de naam van uw keuze opgeven voor de `-Name` para meter.

```azurepowershell-interactive
Set-AzKeyVaultManagedStorageSasDefinition -AccountName $storageAccountName -VaultName $keyVaultName -Name <YourSASDefinitionName> -TemplateUri $sasToken -SasType 'account' -ValidityPeriod ([System.Timespan]::FromDays(30))
```

### <a name="verify-the-shared-access-signature-definition"></a>De definitie van de hand tekening voor gedeelde toegang controleren

U kunt controleren of de definitie van de hand tekening voor gedeelde toegang is opgeslagen in uw sleutel kluis met behulp van de cmdlet Azure PowerShell [Get-AzKeyVaultSecret](/powershell/module/az.keyvault/get-azkeyvaultsecret?view=azps-2.6.0) .

Zoek eerst de definitie van de hand tekening voor gedeelde toegang in uw sleutel kluis.

```azurepowershell-interactive
Get-AzKeyVaultSecret -VaultName <YourKeyVaultName>
```

Het geheim dat overeenkomt met uw SAS-definitie heeft de volgende eigenschappen:

```console
Vault Name   : <YourKeyVaultName>
Name         : <SecretName>
...
Content Type : application/vnd.ms-sastoken-storage
Tags         :
```

U kunt nu de cmdlet [Get-AzKeyVaultSecret](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) en de eigenschap Secret gebruiken `Name` om de inhoud van dat geheim weer te geven.

```azurepowershell-interactive
$secret = Get-AzKeyVaultSecret -VaultName <YourKeyVaultName> -Name <SecretName>

Write-Host $secret.SecretValueText
```

Met deze opdracht wordt de SAS-definitie teken reeks weer gegeven.


## <a name="next-steps"></a>Volgende stappen

- [Sleutel voorbeelden van beheerde opslag accounts](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=)
- [Key Vault Power shell-referentie](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault)
