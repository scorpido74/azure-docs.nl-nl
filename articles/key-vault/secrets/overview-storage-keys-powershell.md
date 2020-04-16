---
title: Azure Key Vault managed storage-account - PowerShell-versie
description: De functie beheerde opslagaccount biedt een naadloze integratie tussen Azure Key Vault en een Azure-opslagaccount.
ms.topic: conceptual
ms.service: key-vault
ms.subservice: secrets
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/10/2019
ms.openlocfilehash: f8c526148e37ba1b716aafd32dcc3f242358f1eb
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81427781"
---
# <a name="manage-storage-account-keys-with-key-vault-and-azure-powershell"></a>Opslagaccountsleutels beheren met Key Vault en Azure PowerShell

Een Azure-opslagaccount gebruikt referenties met een accountnaam en een sleutel. De sleutel is automatisch gegenereerd en dient als een wachtwoord, in plaats van een als een cryptografische sleutel. Key Vault beheert opslagaccountsleutels door ze op te slaan als Key Vault-geheimen. 

U de functie Key Vault managed storage account key gebruiken om sleutels met een Azure-opslagaccount te vermelden (synchroniseren) en de sleutels periodiek te regenereren (roteren). U sleutels beheren voor zowel opslagaccounts als Klassieke opslagaccounts.

Wanneer u de sleutelfunctie voor beheerde opslagaccount gebruikt, moet u rekening houden met de volgende punten:

- Belangrijke waarden worden nooit geretourneerd als reactie op een beller.
- Alleen Key Vault moet uw opslagaccountsleutels beheren. Beheer de sleutels niet zelf en voorkom dat u de Key Vault-processen verstoort.
- Slechts één Key Vault-object mag opslagaccountsleutels beheren. Sta geen sleutelbeheer van meerdere objecten toe.
- U Key Vault aanvragen om uw opslagaccount te beheren met een gebruikersprincipal, maar niet met een serviceprincipal.
- Regenereren van sleutels door alleen Key Vault te gebruiken. Genereer uw opslagaccountsleutels niet handmatig.

We raden u aan azure storage-integratie te gebruiken met Azure Active Directory (Azure AD), de cloudgebaseerde cloud- en toegangsbeheerservice van Microsoft. Azure AD-integratie is beschikbaar voor [Azure-blobs en wachtrijen](../../storage/common/storage-auth-aad.md)en biedt Op OAuth2-token gebaseerde toegang tot Azure Storage (net als Azure Key Vault).

Met Azure AD u uw clienttoepassing verifiëren met behulp van een toepassing of gebruikersidentiteit, in plaats van opslagaccountreferenties. U een [door Azure AD beheerde identiteit](/azure/active-directory/managed-identities-azure-resources/) gebruiken wanneer u op Azure wordt uitgevoerd. Beheerde identiteiten verwijderen de noodzaak voor clientverificatie en het opslaan van referenties in of met uw toepassing.

Azure AD gebruikt rbac (role-based access control) om autorisatie te beheren, die ook wordt ondersteund door Key Vault.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="service-principal-application-id"></a>Servicehoofdtoepassings-id

Een Azure AD-tenant biedt elke geregistreerde toepassing een [serviceprincipal](/azure/active-directory/develop/developer-glossary#service-principal-object). De serviceprincipal fungeert als de toepassings-id, die wordt gebruikt tijdens autorisatie-instellingen voor toegang tot andere Azure-bronnen via RBAC.

Key Vault is een Microsoft-toepassing die vooraf is geregistreerd in alle Azure AD-tenants. Key Vault wordt geregistreerd onder dezelfde toepassings-id in elke Azure-cloud.

| Tenants | Cloud | Toepassings-id |
| --- | --- | --- |
| Azure AD | Azure Government | `7e7c393b-45d0-48b1-a35e-2905ddf8183c` |
| Azure AD | Openbare Azure-peering | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |
| Overige  | Alle | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |

## <a name="prerequisites"></a>Vereisten

Als u deze handleiding wilt voltooien, moet u eerst het volgende doen:

- [Installeer de Azure PowerShell-module](/powershell/azure/install-az-ps?view=azps-2.6.0).
- [Een sleutelkluis maken](quick-create-powershell.md)
- [Maak een Azure-opslagaccount](../../storage/common/storage-account-create.md?tabs=azure-powershell). De naam van het opslagaccount mag alleen kleine letters en cijfers gebruiken. De lengte van de naam moet tussen de 3 en 24 tekens liggen.
      

## <a name="manage-storage-account-keys"></a>Opslagaccountsleutels beheren

### <a name="connect-to-your-azure-account"></a>Verbinding maken met uw Azure-account

Verifieer uw PowerShell-sessie met de [cmdlet Connect-AzAccount.](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) 

```azurepowershell-interactive
Connect-AzAccount
```
Als u meerdere Azure-abonnementen hebt, u deze aanbieden met de cmdlet [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription?view=azps-2.5.0) en het abonnement opgeven dat u wilt gebruiken met de [cmdlet Set-AzContext.](/powershell/module/az.accounts/set-azcontext?view=azps-2.5.0) 

```azurepowershell-interactive
Set-AzContext -SubscriptionId <subscriptionId>
```

### <a name="set-variables"></a>Variabelen instellen

Stel eerst de variabelen in die door de PowerShell-cmdlets moeten worden gebruikt in de volgende stappen. Zorg ervoor dat <YourResourceGroupName> <YourStorageAccountName>u <YourKeyVaultName> de , , en `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` tijdelijke aanduidingen bijwerkt en $keyVaultSpAppId instellen op (zoals opgegeven in [servicehoofdtoepassings-id](#service-principal-application-id), hierboven).

We gebruiken ook de cmdlets Azure PowerShell [Get-AzContext](/powershell/module/az.accounts/get-azcontext?view=azps-2.6.0) en [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount?view=azps-2.6.0) om uw gebruikersnaam en de context van uw Azure-opslagaccount te krijgen.

```azurepowershell-interactive
$resourceGroupName = <YourResourceGroupName>
$storageAccountName = <YourStorageAccountName>
$keyVaultName = <YourKeyVaultName>
$keyVaultSpAppId = "cfa8b339-82a2-471a-a3c9-0fc0be7a4093"
$storageAccountKey = "key1"

# Get your User Id
$userId = (Get-AzContext).Account.Id

# Get a reference to your Azure storage account
$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName
```

### <a name="give-key-vault-access-to-your-storage-account"></a>Key Vault toegang geven tot uw opslagaccount

Voordat Key Vault toegang heeft tot uw opslagaccountsleutels en deze beheert, moet u de toegang tot uw opslagaccount autoriseren. De Key Vault-toepassing vereist machtigingen *om* sleutels voor uw opslagaccount weer te geven en *te regenereren.* Deze machtigingen zijn ingeschakeld via de ingebouwde RBAC-rol [Storage Account Key Operator Service Role](/azure/role-based-access-control/built-in-roles#storage-account-key-operator-service-role). 

Wijs deze rol toe aan de principal key vault service, waarbij het bereik wordt beperkt tot uw opslagaccount met behulp van de Azure [PowerShell New-AzRoleAssignment-cmdlet.](/powershell/module/az.resources/new-azroleassignment?view=azps-2.6.0)

```azurepowershell-interactive
# Assign RBAC role "Storage Account Key Operator Service Role" to Key Vault, limiting the access scope to your storage account. For a classic storage account, use "Classic Storage Account Key Operator Service Role." 
New-AzRoleAssignment -ApplicationId $keyVaultSpAppId -RoleDefinitionName 'Storage Account Key Operator Service Role' -Scope $storageAccount.Id
```

Bij een succesvolle roltoewijzing ziet u de uitvoer vergelijkbaar met het volgende voorbeeld:

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

Als Key Vault al is toegevoegd aan de rol in je opslagaccount, ontvang je een *'De roltoewijzing bestaat al'.* fout. U de roltoewijzing ook verifiëren met behulp van de pagina 'Toegangsbeheer (IAM)) in de Azure-portal.  

### <a name="give-your-user-account-permission-to-managed-storage-accounts"></a>Geef uw gebruikersaccount toestemming aan beheerde opslagaccounts

Gebruik de cmdlet Azure PowerShell [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy?view=azps-2.6.0) om het toegangsbeleid voor sleutelkluis bij te werken en opslagaccountmachtigingen toe te kennen aan uw gebruikersaccount.

```azurepowershell-interactive
# Give your user principal access to all storage account permissions, on your Key Vault instance

Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -UserPrincipalName $userId -PermissionsToStorage get, list, delete, set, update, regeneratekey, getsas, listsas, deletesas, setsas, recover, backup, restore, purge
```

Houd er rekening mee dat machtigingen voor opslagaccounts niet beschikbaar zijn op de pagina 'Toegangsbeleid' voor opslagaccounts in de Azure-portal.

### <a name="add-a-managed-storage-account-to-your-key-vault-instance"></a>Een beheerde opslagaccount toevoegen aan uw Key Vault-exemplaar

Gebruik de cmdlet Azure PowerShell [Add-AzKeyVaultManagedStorageAccount](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount?view=azps-2.6.0) om een beheerd opslagaccount te maken in uw Key Vault-exemplaar. De `-DisableAutoRegenerateKey` schakelaar geeft aan de opslagaccountsleutels NIET opnieuw te genereren.

```azurepowershell-interactive
# Add your storage account to your Key Vault's managed storage accounts

Add-AzKeyVaultManagedStorageAccount -VaultName $keyVaultName -AccountName $storageAccountName -AccountResourceId $storageAccount.Id -ActiveKeyName $storageAccountKey -DisableAutoRegenerateKey
```

Bij een succesvolle toevoeging van het opslagaccount zonder sleutelregeneratie, ziet u uitvoer vergelijkbaar met het volgende voorbeeld:

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

### <a name="enable-key-regeneration"></a>Sleutelregeneratie inschakelen

Als u wilt dat Key Vault uw opslagaccountsleutels periodiek regenereert, u de azure PowerShell [Add-AzKeyVaultManagedStorageAccount-cmdlet](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount?view=azps-2.6.0) gebruiken om een regeneratieperiode in te stellen. In dit voorbeeld stellen we een regeneratieperiode van drie dagen vast. Na drie dagen regenereert Key Vault 'key2' en wisselt de actieve sleutel van 'key2' naar 'key1'.

```azurepowershell-interactive
$regenPeriod = [System.Timespan]::FromDays(3)

Add-AzKeyVaultManagedStorageAccount -VaultName $keyVaultName -AccountName $storageAccountName -AccountResourceId $storageAccount.Id -ActiveKeyName $storageAccountKey -RegenerationPeriod $regenPeriod
```

Bij een succesvolle toevoeging van het opslagaccount met sleutelregeneratie ziet u uitvoer vergelijkbaar met het volgende voorbeeld:

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

## <a name="shared-access-signature-tokens"></a>Tokens voor gedeelde toeganghandtekeningen

Je Key Vault ook vragen om tokens voor gedeelde toegangshandtekeningen te genereren. Een handtekening voor gedeelde toegang biedt gedelegeerde toegang tot bronnen in uw opslagaccount. U klanten toegang verlenen tot bronnen in uw opslagaccount zonder uw accountsleutels te delen. Een handtekening voor gedeelde toegang biedt u een veilige manier om uw opslagbronnen te delen zonder uw accountsleutels in gevaar te brengen.

De opdrachten in deze sectie voltooien de volgende acties:

- Stel de definitie van een account voor gedeelde toegangshandtekening in. 
- Maak een handtekeningtoken voor gedeelde accountbeheer voor blob-, bestands-, tabel- en wachtrijservices. Het token wordt gemaakt voor resourcetypen Service, Container en Object. Het token wordt gemaakt met alle machtigingen, via https en met de opgegeven begin- en einddatums.
- Stel een key vault managed storage shared access signature definition in de kluis in. De definitie heeft de sjabloon URI van het token voor gedeelde toegangshandtekeningen dat is gemaakt. De definitie heeft het `account` type handtekening van gedeelde toegang en is geldig voor N-dagen.
- Controleer of de handtekening van gedeelde toegang als geheim is opgeslagen in uw sleutelkluis.
- 
### <a name="set-variables"></a>Variabelen instellen

Stel eerst de variabelen in die door de PowerShell-cmdlets moeten worden gebruikt in de volgende stappen. Zorg ervoor dat <YourStorageAccountName> <YourKeyVaultName> u de tijdelijke aanduidingen bijwerkt.

We gebruiken ook de Azure PowerShell [New-AzStorageContext-cmdlets](/powershell/module/az.storage/new-azstoragecontext?view=azps-2.6.0) om de context van uw Azure-opslagaccount te krijgen.

```azurepowershell-interactive
$storageAccountName = <YourStorageAccountName>
$keyVaultName = <YourKeyVaultName>

$storageContext = New-AzStorageContext -StorageAccountName $storageAccountName -Protocol Https -StorageAccountKey Key1
```

### <a name="create-a-shared-access-signature-token"></a>Een handtekeningtoken voor gedeelde toegang maken

Maak een definitie van gedeelde toegangshandtekeningen met de azure PowerShell [New-AzStorageAccountSASToken-cmdlets.](/powershell/module/az.storage/new-azstorageaccountsastoken?view=azps-2.6.0)
 
```azurepowershell-interactive
$start = [System.DateTime]::Now.AddDays(-1)
$end = [System.DateTime]::Now.AddMonths(1)

$sasToken = New-AzStorageAccountSasToken -Service blob,file,Table,Queue -ResourceType Service,Container,Object -Permission "racwdlup" -Protocol HttpsOnly -StartTime $start -ExpiryTime $end -Context $storageContext
```
De waarde van $sasToken zal hier op lijken.

```console
?sv=2018-11-09&sig=5GWqHFkEOtM7W9alOgoXSCOJO%2B55qJr4J7tHQjCId9S%3D&spr=https&st=2019-09-18T18%3A25%3A00Z&se=2019-10-19T18%3A25%3A00Z&srt=sco&ss=bfqt&sp=racupwdl
```

### <a name="generate-a-shared-access-signature-definition"></a>Een definitie van een handtekening voor gedeelde toegang genereren

Gebruik de cmdlet Azure PowerShell [Set-AzKeyVaultManagedStorageSasDefinition](/powershell/module/az.keyvault/set-azkeyvaultmanagedstoragesasdefinition?view=azps-2.6.0) om een definitie van handtekening voor gedeelde toegang te maken.  U de naam van `-Name` uw keuze aan de parameter opgeven.

```azurepowershell-interactive
Set-AzKeyVaultManagedStorageSasDefinition -AccountName $storageAccountName -VaultName $keyVaultName -Name <YourSASDefinitionName> -TemplateUri $sasToken -SasType 'account' -ValidityPeriod ([System.Timespan]::FromDays(30))
```

### <a name="verify-the-shared-access-signature-definition"></a>De definitie van de handtekening van gedeelde toegang verifiëren

U controleren of de definitie van handtekening voor gedeelde toegang is opgeslagen in uw sleutelkluis met de Azure PowerShell [Get-AzKeyVaultSecret-cmdlet.](/powershell/module/az.keyvault/get-azkeyvaultsecret?view=azps-2.6.0)

Zoek eerst de definitie van gedeelde toegangshandtekeningen in uw sleutelkluis.

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

U nu de [get-AzKeyVaultSecret-cmdlet](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) en de geheime `Name` eigenschap gebruiken om de inhoud van dat geheim te bekijken.

```azurepowershell-interactive
$secret = Get-AzKeyVaultSecret -VaultName <YourKeyVaultName> -Name <SecretName>

Write-Host $secret.SecretValueText
```

De uitvoer van deze opdracht toont uw SAS-definitietekenreeks.


## <a name="next-steps"></a>Volgende stappen

- [Sleutels voor beheerde opslagaccount](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=)
- [Key Vault PowerShell-referentie](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault)
