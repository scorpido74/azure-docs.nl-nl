---
title: SQL TDE inschakelen met Azure Key Vault
description: Meer informatie over het configureren van een Azure SQL Database en Data Warehouse om Te beginnen met Het gebruik van Transparent Data Encryption (TDE) voor versleuteling-at-rest met PowerShell of CLI.
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
ms.openlocfilehash: 81927575b99604e71f7b0920bc3a448f7796f565
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80067191"
---
# <a name="powershell-and-cli-enable-transparent-data-encryption-with-customer-managed-key-from-azure-key-vault"></a>PowerShell en CLI: Transparante gegevensversleuteling inschakelen met door de klant beheerde sleutel vanuit Azure Key Vault

In dit artikel wordt uitgelegd hoe u een sleutel van Azure Key Vault voor transparante gegevensversleuteling (TDE) gebruiken in een SQL-database of gegevensmagazijn. Ga naar TDE met door de klant beheerde sleutels in Azure Key Vault voor meer informatie over de TDE met Azure Key Vault-integratie - ByOK-ondersteuning (Bring Your Own Key) voor meer informatie over de [TDE.](transparent-data-encryption-byok-azure-sql.md) 

## <a name="prerequisites-for-powershell"></a>Voorwaarden voor PowerShell

- U moet een Azure-abonnement hebben en een beheerder zijn bij dat abonnement.
- [Aanbevolen, maar optioneel] Heb een hardware security module (HSM) of lokale sleutel op te slaan voor het maken van een lokale kopie van de TDE Protector sleutelmateriaal.
- Azure PowerShell moet zijn geïnstalleerd en uitgevoerd.
- Maak een Azure Key Vault en sleutel die u gebruiken voor TDE.
  - [Instructies voor het gebruik van een hardwarebeveiligingsmodule (HSM) en Key Vault](../key-vault/key-vault-hsm-protected-keys.md)
    - De sleutelkluis moet de volgende eigenschap hebben die voor TDE moet worden gebruikt:
  - [soft-delete](../key-vault/key-vault-ovw-soft-delete.md) en purge bescherming
- De sleutel moet de volgende kenmerken hebben die voor TDE moeten worden gebruikt:
   - Geen vervaldatum
   - Niet uitgeschakeld
   - In staat om te presteren *krijgen,* *wrap key,* *uitpakken van belangrijke* bewerkingen

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Raadpleeg [Azure PowerShell installeren](/powershell/azure/install-az-ps) voor instructies over de installatie van de Az-module. Zie [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)voor specifieke cmdlets.

Zie [PowerShell-instructies uit Key Vault](../key-vault/quick-create-powershell.md) en [Hoe u Key Vault soft-delete gebruiken met PowerShell](../key-vault/key-vault-soft-delete-powershell.md)voor details over Key Vault.

> [!IMPORTANT]
> De PowerShell Azure Resource Manager (RM)-module wordt nog steeds ondersteund door Azure SQL Database, maar alle toekomstige ontwikkelingen zijn voor de Az.Sql-module. De AzureRM-module blijft bugfixes ontvangen tot ten minste december 2020.  De argumenten voor de opdrachten in de Az-module en in de AzureRm-modules zijn nagenoeg identiek. Zie De nieuwe Azure [PowerShell Az-module introduceren](/powershell/azure/new-azureps-module-az)voor meer informatie over de compatibiliteit ervan.

## <a name="assign-an-azure-ad-identity-to-your-server"></a>Een Azure AD-identiteit toewijzen aan uw server

Als u een bestaande server hebt, gebruikt u het volgende om een Azure AD-identiteit aan uw server toe te voegen:

   ```powershell
   $server = Set-AzSqlServer -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> -AssignIdentity
   ```

Als u een server maakt, gebruikt u de cmdlet [Nieuw-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) met de tag -Identiteit om een Azure AD-identiteit toe te voegen tijdens het maken van de server:

   ```powershell
   $server = New-AzSqlServer -ResourceGroupName <SQLDatabaseResourceGroupName> -Location <RegionName> `
       -ServerName <LogicalServerName> -ServerVersion "12.0" -SqlAdministratorCredentials <PSCredential> -AssignIdentity
   ```

## <a name="grant-key-vault-permissions-to-your-server"></a>Key Vault-machtigingen verlenen aan uw server

Gebruik de cmdlet [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) om uw server toegang te verlenen tot de sleutelkluis voordat u een sleutel van deze cachegebruikt voor TDE.

   ```powershell
   Set-AzKeyVaultAccessPolicy -VaultName <KeyVaultName> `
       -ObjectId $server.Identity.PrincipalId -PermissionsToKeys get, wrapKey, unwrapKey
   ```

## <a name="add-the-key-vault-key-to-the-server-and-set-the-tde-protector"></a>Voeg de Key Vault-sleutel toe aan de server en stel de TDE Protector in

- Gebruik de [cmdlet Get-AzKeyVaultKey Om](/powershell/module/az.keyvault/get-azkeyvaultkey?view=azps-2.4.0) de sleutel-ID uit de sleutelkluis op te halen
- Gebruik de cmdlet [Add-AzSqlServerKeyKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) om de sleutel van de Key Vault aan de server toe te voegen.
- Gebruik de [cmdlet Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) om de sleutel in te stellen als TDE-protector voor alle serverbronnen.
- Gebruik de [cmdlet Get-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) om te bevestigen dat de TDE-protector is geconfigureerd zoals bedoeld.

> [!NOTE]
> De gecombineerde lengte voor de naam van de sleutelkluis en de sleutelnaam mag niet groter zijn dan 94 tekens.

> [!TIP]
> Een voorbeeld van KeyId uit Key Vault:https://contosokeyvault.vault.azure.net/keys/Key1/1a1a2b2b3c3c4d4d5e5e6f6f7g7g8h8h

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

Gebruik de cmdlet [Set-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) om TDE in te schakelen.

```powershell
Set-AzSqlDatabaseTransparentDataEncryption -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> -DatabaseName <DatabaseName> -State "Enabled"
```

Nu is de database of data warehouse heeft TDE ingeschakeld met een encryptiesleutel in Key Vault.

## <a name="check-the-encryption-state-and-encryption-activity"></a>De versleutelingsstatus en versleutelingsactiviteit controleren

Gebruik de [Get-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption) om de versleutelingsstatus en de [Get-AzSqlDatabaseTransparentDataEncryptionActivity](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryptionactivity) te krijgen om de voortgang van de versleuteling voor een database of gegevensmagazijn te controleren.

```powershell
# get the encryption state
Get-AzSqlDatabaseTransparentDataEncryption -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> -DatabaseName <DatabaseName> `

# check the encryption progress for a database or data warehouse
Get-AzSqlDatabaseTransparentDataEncryptionActivity -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> -DatabaseName <DatabaseName>  
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Zie [De Opdracht-Line-interface 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)van Azure installeren en configureren als u de vereiste Command-Line Interface versie 2.0 of hoger wilt installeren en aansluiten op uw Azure-abonnement.

Zie Key Vault beheren [met CLI 2.0](../key-vault/key-vault-manage-with-cli2.md) en [Het gebruik van Key Vault softdelete met CLI](../key-vault/key-vault-soft-delete-cli.md)voor details over Key Vault.

## <a name="assign-an-azure-ad-identity-to-your-server"></a>Een Azure AD-identiteit toewijzen aan uw server

```azurecli
# create server (with identity) and database
az sql server create --name <servername> --resource-group <rgname>  --location <location> --admin-user <user> --admin-password <password> --assign-identity
az sql db create --name <dbname> --server <servername> --resource-group <rgname>
```

> [!TIP]
> Houd de "principalID" van het maken van de server, het is het object-id dat wordt gebruikt om belangrijke vault-machtigingen toe te wijzen in de volgende stap

## <a name="grant-key-vault-permissions-to-your-server"></a>Key Vault-machtigingen verlenen aan uw server

```azurecli
# create key vault, key and grant permission
az keyvault create --name <kvname> --resource-group <rgname> --location <location> --enable-soft-delete true
az keyvault key create --name <keyname> --vault-name <kvname> --protection software
az keyvault set-policy --name <kvname>  --object-id <objectid> --resource-group <rgname> --key-permissions wrapKey unwrapKey get
```

> [!TIP]
> Bewaar de sleutel URI of keyID van de nieuwe sleutel voor de volgende stap, bijvoorbeeld:https://contosokeyvault.vault.azure.net/keys/Key1/1a1a2b2b3c3c4d4d5e5e6f6f7g7g8h8h

## <a name="add-the-key-vault-key-to-the-server-and-set-the-tde-protector"></a>Voeg de Key Vault-sleutel toe aan de server en stel de TDE Protector in

```azurecli
# add server key and update encryption protector
az sql server key create --server <servername> --resource-group <rgname> --kid <keyID>
az sql server tde-key set --server <servername> --server-key-type AzureKeyVault  --resource-group <rgname> --kid <keyID>
```

> [!NOTE]
> De gecombineerde lengte voor de naam van de sleutelkluis en de sleutelnaam mag niet groter zijn dan 94 tekens.

## <a name="turn-on-tde"></a>TDE inschakelen

```azurecli
# enable encryption
az sql db tde set --database <dbname> --server <servername> --resource-group <rgname> --status Enabled
```

Nu is het database- of gegevensmagazijn tde ingeschakeld met een door de klant beheerde versleutelingssleutel in Azure Key Vault.

## <a name="check-the-encryption-state-and-encryption-activity"></a>De versleutelingsstatus en versleutelingsactiviteit controleren

```azurecli
# get encryption scan progress
az sql db tde list-activity --database <dbname> --server <servername> --resource-group <rgname>  

# get whether encryption is on or off
az sql db tde show --database <dbname> --server <servername> --resource-group <rgname>
```

* * *

## <a name="useful-powershell-cmdlets"></a>Nuttige PowerShell-cmdlets

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

- Gebruik de cmdlet [Set-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) om TDE uit te schakelen.

   ```powershell
   Set-AzSqlDatabaseTransparentDataEncryption -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName> `
      -DatabaseName <DatabaseName> -State "Disabled"
   ```

- Gebruik de [cmdlet Get-AzSqlServerKeyKey](/powershell/module/az.sql/get-azsqlserverkeyvaultkey) om de lijst met Key Vault-sleutels die aan de server zijn toegevoegd, terug te geven.

   ```powershell
   # KeyId is an optional parameter, to return a specific key version
   Get-AzSqlServerKeyVaultKey -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

- Gebruik de [Delete-AzSqlServerKeyVaultKey](/powershell/module/az.sql/remove-azsqlserverkeyvaultkey) om een Key Vault-sleutel van de server te verwijderen.

   ```powershell
   # the key set as the TDE Protector cannot be removed
   Remove-AzSqlServerKeyVaultKey -KeyId <KeyVaultKeyId> -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

- Zie [az sql](/cli/azure/sql)voor algemene database-instellingen.

- Zie az [sql-servertoets](/cli/azure/sql/server/key)voor instellingen voor vault-sleutels.

- Zie voor [TDE-instellingen az sql server tde-key](/cli/azure/sql/server/tde-key) en [az sql db tde](/cli/azure/sql/db/tde).

* * *

## <a name="troubleshooting"></a>Problemen oplossen

Controleer het volgende als er een probleem optreedt:

- Als de sleutelkluis niet kan worden gevonden, controleert u of u het juiste abonnement hebt.

   # <a name="powershell"></a>[Powershell](#tab/azure-powershell)

   ```powershell
   Get-AzSubscription -SubscriptionId <SubscriptionId>
   ```

   # <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

   ```powershell
   az account show - s <SubscriptionId>
   ```

   * * *

- Als de nieuwe sleutel niet aan de server kan worden toegevoegd of als de nieuwe sleutel niet kan worden bijgewerkt als de TDE Protector, controleert u het volgende:
   - De sleutel mag geen vervaldatum hebben
   - De sleutel moet de *get,* *wrap key*en *uitpakken van sleutelbewerkingen* hebben ingeschakeld.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het roteren van de TDE Protector van een server om te voldoen aan de beveiligingsvereisten: [Draai de transparent Data Encryption protector met PowerShell.](transparent-data-encryption-byok-azure-sql-key-rotation.md)
- In het geval van een veiligheidsrisico, leren hoe u een potentieel gecompromitteerde TDE Protector te verwijderen: [Verwijder een potentieel gecompromitteerde sleutel](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md).
