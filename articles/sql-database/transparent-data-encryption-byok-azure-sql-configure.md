---
title: SQL-TDE met Azure Key Vault inschakelen
description: Meer informatie over het configureren van een Azure SQL Database en een Data Warehouse om Transparent Data Encryption (TDE) te gebruiken voor versleuteling-at-rest met behulp van Power shell of CLI.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto
ms.date: 03/12/2019
ms.openlocfilehash: 5b1c985eeec9af25ec576f4e2375c417dc376f95
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81452754"
---
# <a name="powershell-and-cli-enable-transparent-data-encryption-with-customer-managed-key-from-azure-key-vault"></a>Power shell en CLI: Transparent Data Encryption met door de klant beheerde sleutel inschakelen vanuit Azure Key Vault

In dit artikel wordt beschreven hoe u een sleutel gebruikt uit Azure Key Vault voor Transparent Data Encryption (TDE) in een SQL Database of Data Warehouse. Voor meer informatie over de TDE met ondersteuning voor Azure Key Vault Integration-Bring Your Own Key (BYOK), gaat u naar [TDe met door de klant beheerde sleutels in azure Key Vault](transparent-data-encryption-byok-azure-sql.md). 

## <a name="prerequisites-for-powershell"></a>Vereisten voor Power shell

- U moet een Azure-abonnement hebben en een beheerder van dat abonnement zijn.
- [Aanbevolen, maar optioneel] Een HSM (Hardware Security module) of een lokaal sleutel archief hebben voor het maken van een lokale kopie van het TDE-beveiligings sleutel materiaal.
- Azure PowerShell moet zijn geïnstalleerd en worden uitgevoerd.
- Maak een Azure Key Vault en een sleutel om te gebruiken voor TDE.
  - [Instructies voor het gebruik van een Hardware Security module (HSM) en Key Vault](../key-vault/keys/hsm-protected-keys.md)
    - De sleutel kluis moet de volgende eigenschap hebben die moet worden gebruikt voor TDE:
  - [zacht verwijderen](../key-vault/general/overview-soft-delete.md) en beveiliging opschonen
- De sleutel moet de volgende kenmerken hebben om te kunnen worden gebruikt voor TDE:
   - Geen verval datum
   - Niet uitgeschakeld
   - Kan de *Get*-, *Terugloop*-, *sleutel bewerking uitpakken*

# <a name="powershell"></a>[Zo](#tab/azure-powershell)

Raadpleeg [Azure PowerShell installeren](/powershell/azure/install-az-ps) voor instructies over de installatie van de Az-module. Zie [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)voor specifieke cmdlets.

Voor specifieke informatie over Key Vault raadpleegt u [Power shell-instructies van Key Vault](../key-vault/secrets/quick-create-powershell.md) en [hoe u Key Vault Soft-verwijderings opdracht kunt gebruiken met Power shell](../key-vault/general/soft-delete-powershell.md).

> [!IMPORTANT]
> De module Power shell Azure Resource Manager (RM) wordt nog steeds ondersteund door Azure SQL Database, maar alle toekomstige ontwikkeling is voor de module AZ. SQL. De AzureRM-module blijft oplossingen ontvangen tot ten minste december 2020.  De argumenten voor de opdrachten in de module AZ en in de AzureRm-modules zijn aanzienlijk identiek. Zie [Inleiding tot de nieuwe Azure PowerShell AZ-module](/powershell/azure/new-azureps-module-az)voor meer informatie over de compatibiliteit.

## <a name="assign-an-azure-ad-identity-to-your-server"></a>Een Azure AD-identiteit aan uw server toewijzen

Als u een bestaande server hebt, gebruikt u het volgende om een Azure AD-identiteit toe te voegen aan uw server:

   ```powershell
   $server = Set-AzSqlServer -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> -AssignIdentity
   ```

Als u een server maakt, gebruikt u de cmdlet [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) met de tag-Identity om een Azure AD-identiteit toe te voegen tijdens het maken van de server:

   ```powershell
   $server = New-AzSqlServer -ResourceGroupName <SQLDatabaseResourceGroupName> -Location <RegionName> `
       -ServerName <LogicalServerName> -ServerVersion "12.0" -SqlAdministratorCredentials <PSCredential> -AssignIdentity
   ```

## <a name="grant-key-vault-permissions-to-your-server"></a>Key Vault machtigingen verlenen aan uw server

Gebruik de cmdlet [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) om uw server toegang te geven tot de sleutel kluis voordat u een sleutel van de-service gebruikt voor TDe.

   ```powershell
   Set-AzKeyVaultAccessPolicy -VaultName <KeyVaultName> `
       -ObjectId $server.Identity.PrincipalId -PermissionsToKeys get, wrapKey, unwrapKey
   ```

## <a name="add-the-key-vault-key-to-the-server-and-set-the-tde-protector"></a>Voeg de Key Vault sleutel toe aan de server en stel de TDE-beveiliging in

- Gebruik de cmdlet [Get-AzKeyVaultKey](/powershell/module/az.keyvault/get-azkeyvaultkey?view=azps-2.4.0) om de sleutel-id op te halen uit de sleutel kluis
- Gebruik de cmdlet [add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) om de sleutel van de Key Vault toe te voegen aan de-server.
- Gebruik de cmdlet [set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) om de sleutel in te stellen als de TDe-Protector voor alle Server bronnen.
- Gebruik de cmdlet [Get-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) om te controleren of de TDe-Protector is geconfigureerd zoals bedoeld.

> [!NOTE]
> De gecombineerde lengte van de naam van de sleutel kluis en de naam van de sleutel mag niet langer zijn dan 94 tekens.

> [!TIP]
> Een voor beeld van een KeyId van Key Vault:https://contosokeyvault.vault.azure.net/keys/Key1/1a1a2b2b3c3c4d4d5e5e6f6f7g7g8h8h

```powershell
# add the key from Key Vault to the server
Add-AzSqlServerKeyVaultKey -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> -KeyId <KeyVaultKeyId>

# set the key as the TDE protector for all resources under the server
Set-AzSqlServerTransparentDataEncryptionProtector -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> `
   -Type AzureKeyVault -KeyId <KeyVaultKeyId>

# confirm the TDE protector was configured as intended
Get-AzSqlServerTransparentDataEncryptionProtector -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName>
```

## <a name="turn-on-tde"></a>TDE inschakelen

Gebruik de cmdlet [set-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) om TDe in te scha kelen.

```powershell
Set-AzSqlDatabaseTransparentDataEncryption -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> -DatabaseName <DatabaseName> -State "Enabled"
```

Nu is in de data base of het Data Warehouse TDE ingeschakeld met een versleutelings sleutel in Key Vault.

## <a name="check-the-encryption-state-and-encryption-activity"></a>De versleutelings status en versleutelings activiteit controleren

Gebruik [Get-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption) om de versleutelings status en [Get-AzSqlDatabaseTransparentDataEncryptionActivity](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryptionactivity) te krijgen om de versleutelings voortgang voor een Data Base of Data Warehouse te controleren.

```powershell
# get the encryption state
Get-AzSqlDatabaseTransparentDataEncryption -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> -DatabaseName <DatabaseName> `

# check the encryption progress for a database or data warehouse
Get-AzSqlDatabaseTransparentDataEncryptionActivity -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> -DatabaseName <DatabaseName>  
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Zie [de Azure-opdracht regel 2,0 interface voor meerdere platformen installeren en configureren](https://docs.microsoft.com/cli/azure/install-azure-cli)voor meer informatie over het installeren van de vereiste opdracht regel interface versie 2,0 of hoger en het maken van een verbinding met uw Azure-abonnement.

Voor specifieke informatie over Key Vault raadpleegt [u Key Vault beheren met cli 2,0](../key-vault/general/manage-with-cli2.md) en [hoe u Key Vault zacht-Delete gebruikt met CLI](../key-vault/general/soft-delete-cli.md).

## <a name="assign-an-azure-ad-identity-to-your-server"></a>Een Azure AD-identiteit aan uw server toewijzen

```azurecli
# create server (with identity) and database
az sql server create --name <servername> --resource-group <rgname>  --location <location> --admin-user <user> --admin-password <password> --assign-identity
az sql db create --name <dbname> --server <servername> --resource-group <rgname>
```

> [!TIP]
> Behoud de ' principalID ' van het maken van de server, het is de object-id die wordt gebruikt voor het toewijzen van sleutel kluis machtigingen in de volgende stap

## <a name="grant-key-vault-permissions-to-your-server"></a>Key Vault machtigingen verlenen aan uw server

```azurecli
# create key vault, key and grant permission
az keyvault create --name <kvname> --resource-group <rgname> --location <location> --enable-soft-delete true
az keyvault key create --name <keyname> --vault-name <kvname> --protection software
az keyvault set-policy --name <kvname>  --object-id <objectid> --resource-group <rgname> --key-permissions wrapKey unwrapKey get
```

> [!TIP]
> Behoud de sleutel-URI of keyID van de nieuwe sleutel voor de volgende stap, bijvoorbeeld:https://contosokeyvault.vault.azure.net/keys/Key1/1a1a2b2b3c3c4d4d5e5e6f6f7g7g8h8h

## <a name="add-the-key-vault-key-to-the-server-and-set-the-tde-protector"></a>Voeg de Key Vault sleutel toe aan de server en stel de TDE-beveiliging in

```azurecli
# add server key and update encryption protector
az sql server key create --server <servername> --resource-group <rgname> --kid <keyID>
az sql server tde-key set --server <servername> --server-key-type AzureKeyVault  --resource-group <rgname> --kid <keyID>
```

> [!NOTE]
> De gecombineerde lengte van de naam van de sleutel kluis en de naam van de sleutel mag niet langer zijn dan 94 tekens.

## <a name="turn-on-tde"></a>TDE inschakelen

```azurecli
# enable encryption
az sql db tde set --database <dbname> --server <servername> --resource-group <rgname> --status Enabled
```

De data base of het Data Warehouse heeft nu TDE ingeschakeld met een door de klant beheerde versleutelings sleutel in Azure Key Vault.

## <a name="check-the-encryption-state-and-encryption-activity"></a>De versleutelings status en versleutelings activiteit controleren

```azurecli
# get encryption scan progress
az sql db tde list-activity --database <dbname> --server <servername> --resource-group <rgname>  

# get whether encryption is on or off
az sql db tde show --database <dbname> --server <servername> --resource-group <rgname>
```

* * *

## <a name="useful-powershell-cmdlets"></a>Handige Power shell-cmdlets

# <a name="powershell"></a>[Zo](#tab/azure-powershell)

- Gebruik de cmdlet [set-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) om TDe uit te scha kelen.

   ```powershell
   Set-AzSqlDatabaseTransparentDataEncryption -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName> `
      -DatabaseName <DatabaseName> -State "Disabled"
   ```

- Gebruik de cmdlet [Get-AzSqlServerKeyVaultKey](/powershell/module/az.sql/get-azsqlserverkeyvaultkey) om de lijst met Key Vault sleutels te retour neren die zijn toegevoegd aan de server.

   ```powershell
   # KeyId is an optional parameter, to return a specific key version
   Get-AzSqlServerKeyVaultKey -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

- Gebruik [Remove-AzSqlServerKeyVaultKey](/powershell/module/az.sql/remove-azsqlserverkeyvaultkey) om een Key Vault sleutel van de server te verwijderen.

   ```powershell
   # the key set as the TDE Protector cannot be removed
   Remove-AzSqlServerKeyVaultKey -KeyId <KeyVaultKeyId> -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

- Zie [AZ SQL](/cli/azure/sql)(Engelstalig) voor algemene data base-instellingen.

- Zie [AZ SQL Server key](/cli/azure/sql/server/key)(Engelstalig) voor de instellingen van de kluis sleutel.

- Zie [AZ SQL Server TDe-Key](/cli/azure/sql/server/tde-key) en [AZ SQL DB TDe](/cli/azure/sql/db/tde)voor TDe-instellingen.

* * *

## <a name="troubleshooting"></a>Problemen oplossen

Controleer het volgende als er een probleem optreedt:

- Als de sleutel kluis niet kan worden gevonden, controleert u of u zich in het juiste abonnement bevindt.

   # <a name="powershell"></a>[Zo](#tab/azure-powershell)

   ```powershell
   Get-AzSubscription -SubscriptionId <SubscriptionId>
   ```

   # <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

   ```powershell
   az account show - s <SubscriptionId>
   ```

   * * *

- Als de nieuwe sleutel niet kan worden toegevoegd aan de server, of als de nieuwe sleutel niet kan worden bijgewerkt als de TDE-Protector, controleert u het volgende:
   - De sleutel mag geen verval datum hebben
   - Voor de sleutel moet de bewerking *Get*, *wrap*en *uitpakken van sleutel* bewerkingen zijn ingeschakeld.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het draaien van de TDE-Protector van een server om te voldoen aan de beveiligings vereisten: [Roteer de transparent Data Encryption-Protector met Power shell](transparent-data-encryption-byok-azure-sql-key-rotation.md).
- In het geval van een beveiligings risico leert u hoe u een mogelijk aangetast TDE-Protector kunt verwijderen: [Verwijder een mogelijk versleutelde code](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md).
