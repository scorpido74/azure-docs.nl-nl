---
title: 'Power shell en CLI: Schakel SQL-TDE in met Azure Key Vault uw eigen sleutel te halen Azure SQL Database '
description: Meer informatie over het configureren van een Azure SQL Database en een Data Warehouse om Transparent Data Encryption (TDE) te gebruiken voor versleuteling-at-rest met behulp van Power shell of CLI.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aliceku
ms.author: aliceku
ms.reviewer: vanto
ms.date: 03/12/2019
ms.openlocfilehash: 232f38b239090356ae3ad1a70b522188f6ab6a4f
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73686757"
---
# <a name="powershell-and-cli-enable-transparent-data-encryption-with-customer-managed-key-from-azure-key-vault"></a>Power shell en CLI: Transparent Data Encryption met door de klant beheerde sleutel inschakelen vanuit Azure Key Vault

In dit artikel wordt beschreven hoe u een sleutel gebruikt uit Azure Key Vault voor Transparent Data Encryption (TDE) in een SQL Database of Data Warehouse. Voor meer informatie over de TDE met ondersteuning voor Azure Key Vault Integration-Bring Your Own Key (BYOK), gaat u naar [TDe met door de klant beheerde sleutels in azure Key Vault](transparent-data-encryption-byok-azure-sql.md). 

## <a name="prerequisites-for-powershell"></a>Vereisten voor Power shell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> De Power shell-Azure Resource Manager module wordt nog steeds ondersteund door Azure SQL Database, maar alle toekomstige ontwikkeling is voor de module AZ. SQL. Zie [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)voor deze cmdlets. De argumenten voor de opdrachten in de module AZ en in de AzureRm-modules zijn aanzienlijk identiek.

- U moet een Azure-abonnement hebben en een beheerder van dat abonnement zijn.
- [Aanbevolen, maar optioneel] Een HSM (Hardware Security module) of een lokaal sleutel archief hebben voor het maken van een lokale kopie van het TDE-beveiligings sleutel materiaal.
- Azure PowerShell moet zijn geïnstalleerd en worden uitgevoerd. 
- Maak een Azure Key Vault en een sleutel om te gebruiken voor TDE.
  - [Power shell-instructies van Key Vault](../key-vault/quick-create-powershell.md)
  - [Instructies voor het gebruik van een Hardware Security module (HSM) en Key Vault](../key-vault/key-vault-hsm-protected-keys.md)
    - De sleutel kluis moet de volgende eigenschap hebben die moet worden gebruikt voor TDE:
  - [zacht verwijderen](../key-vault/key-vault-ovw-soft-delete.md) en beveiliging opschonen
  - [De Key Vault-functie voor voorlopig verwijderen gebruiken met PowerShell](../key-vault/key-vault-soft-delete-powershell.md) 
- De sleutel moet de volgende kenmerken hebben om te kunnen worden gebruikt voor TDE:
   - Geen verval datum
   - Niet uitgeschakeld
   - Kan de *Get*-, *Terugloop*-, *sleutel bewerking uitpakken*

## <a name="step-1-assign-an-azure-ad-identity-to-your-server"></a>Step 1. Een Azure AD-identiteit aan uw server toewijzen 

Als u een bestaande server hebt, gebruikt u het volgende om een Azure AD-identiteit toe te voegen aan uw server:

   ```powershell
   $server = Set-AzSqlServer `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -AssignIdentity
   ```

Als u een server maakt, gebruikt u de cmdlet [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) met de tag-Identity om een Azure AD-identiteit toe te voegen tijdens het maken van de server:

   ```powershell
   $server = New-AzSqlServer `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -Location <RegionName> `
   -ServerName <LogicalServerName> `
   -ServerVersion "12.0" `
   -SqlAdministratorCredentials <PSCredential> `
   -AssignIdentity 
   ```

## <a name="step-2-grant-key-vault-permissions-to-your-server"></a>Stap 2. Key Vault machtigingen verlenen aan uw server

Gebruik de cmdlet [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) om uw server toegang te geven tot de sleutel kluis voordat u een sleutel van de-service gebruikt voor TDe.

   ```powershell
   Set-AzKeyVaultAccessPolicy  `
   -VaultName <KeyVaultName> `
   -ObjectId $server.Identity.PrincipalId `
   -PermissionsToKeys get, wrapKey, unwrapKey
   ```

## <a name="step-3-add-the-key-vault-key-to-the-server-and-set-the-tde-protector"></a>Stap 3. Voeg de Key Vault sleutel toe aan de server en stel de TDE-beveiliging in


- Gebruik de cmdlet [Get-AzKeyVaultKey](/powershell/module/az.keyvault/get-azkeyvaultkey?view=azps-2.4.0) om de sleutel-id op te halen uit de sleutel kluis
- Gebruik de cmdlet [add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) om de sleutel van de Key Vault toe te voegen aan de-server.
- Gebruik de cmdlet [set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) om de sleutel in te stellen als de TDe-Protector voor alle Server bronnen.
- Gebruik de cmdlet [Get-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) om te controleren of de TDe-Protector is geconfigureerd zoals bedoeld.

> [!Note]
> De gecombineerde lengte van de naam van de sleutel kluis en de naam van de sleutel mag niet langer zijn dan 94 tekens.
> 

>[!Tip]
>Een voor beeld van een KeyId van Key Vault: https://contosokeyvault.vault.azure.net/keys/Key1/1a1a2b2b3c3c4d4d5e5e6f6f7g7g8h8h
>

   ```powershell
   <# Add the key from Key Vault to the server #>
   Add-AzSqlServerKeyVaultKey `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -KeyId <KeyVaultKeyId>

   <# Set the key as the TDE protector for all resources under the server #>
   Set-AzSqlServerTransparentDataEncryptionProtector `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -Type AzureKeyVault `
   -KeyId <KeyVaultKeyId> 

   <# To confirm that the TDE protector was configured as intended: #>
   Get-AzSqlServerTransparentDataEncryptionProtector `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> 
   ```

## <a name="step-4-turn-on-tde"></a>Stap 4. TDE inschakelen 

Gebruik de cmdlet [set-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) om TDe in te scha kelen.

   ```powershell
   Set-AzSqlDatabaseTransparentDataEncryption `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -DatabaseName <DatabaseName> `
   -State "Enabled"
   ```

Nu is in de data base of het Data Warehouse TDE ingeschakeld met een versleutelings sleutel in Key Vault.

## <a name="step-5-check-the-encryption-state-and-encryption-activity"></a>Stap 5. De versleutelings status en versleutelings activiteit controleren

Gebruik [Get-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption) om de versleutelings status en [Get-AzSqlDatabaseTransparentDataEncryptionActivity](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryptionactivity) te krijgen om de versleutelings voortgang voor een Data Base of Data Warehouse te controleren.

   ```powershell
   # Get the encryption state
   Get-AzSqlDatabaseTransparentDataEncryption `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -DatabaseName <DatabaseName> `

   <# Check the encryption progress for a database or data warehouse #>
   Get-AzSqlDatabaseTransparentDataEncryptionActivity `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -DatabaseName <DatabaseName>  
   ```

## <a name="other-useful-powershell-cmdlets"></a>Andere handige Power shell-cmdlets

- Gebruik de cmdlet [set-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) om TDe uit te scha kelen.

   ```powershell
   Set-AzSqlDatabaseTransparentDataEncryption `
   -ServerName <LogicalServerName> `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -DatabaseName <DatabaseName> `
   -State "Disabled"
   ```
 
- Gebruik de cmdlet [Get-AzSqlServerKeyVaultKey](/powershell/module/az.sql/get-azsqlserverkeyvaultkey) om de lijst met Key Vault sleutels te retour neren die zijn toegevoegd aan de server.

   ```powershell
   <# KeyId is an optional parameter, to return a specific key version #>
   Get-AzSqlServerKeyVaultKey `
   -ServerName <LogicalServerName> `
   -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```
 
- Gebruik [Remove-AzSqlServerKeyVaultKey](/powershell/module/az.sql/remove-azsqlserverkeyvaultkey) om een Key Vault sleutel van de server te verwijderen.

   ```powershell
   <# The key set as the TDE Protector cannot be removed. #>
   Remove-AzSqlServerKeyVaultKey `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroupName <SQLDatabaseResourceGroupName>   
   ```
 
## <a name="troubleshooting"></a>Problemen oplossen

Controleer het volgende als er een probleem optreedt:
- Als de sleutel kluis niet kan worden gevonden, controleert u of u zich in het juiste abonnement bevindt met de cmdlet [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription) .

   ```powershell
   Get-AzSubscription `
   -SubscriptionId <SubscriptionId>
   ```

- Als de nieuwe sleutel niet kan worden toegevoegd aan de server, of als de nieuwe sleutel niet kan worden bijgewerkt als de TDE-Protector, controleert u het volgende:
   - De sleutel mag geen verval datum hebben
   - Voor de sleutel moet de bewerking *Get*, *wrap*en *uitpakken van sleutel* bewerkingen zijn ingeschakeld.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het draaien van de TDE-Protector van een server om te voldoen aan de beveiligings vereisten: [Roteer de transparent Data Encryption-Protector met Power shell](transparent-data-encryption-byok-azure-sql-key-rotation.md).
- In het geval van een beveiligings risico leert u hoe u een mogelijk aangetast TDE-Protector kunt verwijderen: [Verwijder een mogelijk versleutelde code](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md). 

## <a name="prerequisites-for-cli"></a>Vereisten voor CLI

- U moet een Azure-abonnement hebben en een beheerder van dat abonnement zijn.
- [Aanbevolen, maar optioneel] Een HSM (Hardware Security module) of een lokaal sleutel archief hebben voor het maken van een lokale kopie van het TDE-beveiligings sleutel materiaal.
- Opdracht regel interface versie 2,0 of hoger. Zie [de Azure-opdracht regel 2,0 interface voor meerdere platforms installeren en configureren](https://docs.microsoft.com/cli/azure/install-azure-cli)voor meer informatie over het installeren van de nieuwste versie en het maken van een verbinding met uw Azure-abonnement. 
- Maak een Azure Key Vault en een sleutel om te gebruiken voor TDE.
  - [Key Vault beheren met CLI 2,0](../key-vault/key-vault-manage-with-cli2.md)
  - [Instructies voor het gebruik van een Hardware Security module (HSM) en Key Vault](../key-vault/key-vault-hsm-protected-keys.md)
    - De sleutel kluis moet de volgende eigenschap hebben die moet worden gebruikt voor TDE:
  - [voorlopig verwijderen](../key-vault/key-vault-ovw-soft-delete.md)
  - [De Key Vault-functie voor voorlopig verwijderen gebruiken met CLI](../key-vault/key-vault-soft-delete-cli.md) 
- De sleutel moet de volgende kenmerken hebben om te kunnen worden gebruikt voor TDE:
   - Geen verval datum
   - Niet uitgeschakeld
   - Kan de *Get*-, *Terugloop*-, *sleutel bewerking uitpakken*
   
## <a name="step-1-create-a-server-with-an-azure-ad-identity"></a>Step 1. Een server maken met een Azure AD-identiteit
      cli
      # create server (with identity) and database
      az sql server create --name <servername> --resource-group <rgname>  --location <location> --admin-user <user> --admin-password <password> --assign-identity
      az sql db create --name <dbname> --server <servername> --resource-group <rgname>  
 
 
>[!Tip]
>Behoud de ' principalID ' van het maken van de server, het is de object-id die wordt gebruikt voor het toewijzen van sleutel kluis machtigingen in de volgende stap
>
 
## <a name="step-2-grant-key-vault-permissions-to-the-logical-sql-server"></a>Stap 2. Key Vault machtigingen verlenen aan de logische SQL-Server
      cli
      # create key vault, key and grant permission
       az keyvault create --name <kvname> --resource-group <rgname> --location <location> --enable-soft-delete true
       az keyvault key create --name <keyname> --vault-name <kvname> --protection software
       az keyvault set-policy --name <kvname>  --object-id <objectid> --resource-group <rgname> --key-permissions wrapKey unwrapKey get 


>[!Tip]
>Behoud de sleutel-URI of keyID van de nieuwe sleutel voor de volgende stap, bijvoorbeeld: https://contosokeyvault.vault.azure.net/keys/Key1/1a1a2b2b3c3c4d4d5e5e6f6f7g7g8h8h
>
 
       
## <a name="step-3-add-the-key-vault-key-to-the-server-and-set-the-tde-protector"></a>Stap 3. Voeg de Key Vault sleutel toe aan de server en stel de TDE-beveiliging in
  
     cli
     # add server key and update encryption protector
     az sql server key create --server <servername> --resource-group <rgname> --kid <keyID>
     az sql server tde-key set --server <servername> --server-key-type AzureKeyVault  --resource-group <rgname> --kid <keyID>

        
  > [!Note]
> De gecombineerde lengte van de naam van de sleutel kluis en de naam van de sleutel mag niet langer zijn dan 94 tekens.
> 

  
## <a name="step-4-turn-on-tde"></a>Stap 4. TDE inschakelen 
      cli
      # enable encryption
      az sql db tde set --database <dbname> --server <servername> --resource-group <rgname> --status Enabled 
      

De data base of het Data Warehouse heeft nu TDE ingeschakeld met een door de klant beheerde versleutelings sleutel in Azure Key Vault.

## <a name="step-5-check-the-encryption-state-and-encryption-activity"></a>Stap 5. De versleutelings status en versleutelings activiteit controleren

     cli
      # get encryption scan progress
      az sql db tde list-activity --database <dbname> --server <servername> --resource-group <rgname>  

      # get whether encryption is on or off
      az sql db tde show --database <dbname> --server <servername> --resource-group <rgname> 

## <a name="sql-cli-references"></a>Naslag informatie over SQL CLI

https://docs.microsoft.com/cli/azure/sql 

https://docs.microsoft.com/cli/azure/sql/server/key 

https://docs.microsoft.com/cli/azure/sql/server/tde-key 

https://docs.microsoft.com/cli/azure/sql/db/tde 

