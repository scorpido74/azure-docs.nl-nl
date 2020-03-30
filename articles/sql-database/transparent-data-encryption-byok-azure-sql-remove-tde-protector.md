---
title: Verwijder TDE protector - PowerShell
description: Handleiding voor het reageren op een mogelijk gecompromitteerde TDE-protector voor een Azure SQL Database of Data Warehouse met Behulp van TDE met ByOK-ondersteuning (Bring YOur Own Key).
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto
ms.date: 02/24/2020
ms.openlocfilehash: 5a89c3f7d52c5717b902a69e9c64b3fcc422c481
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80067200"
---
# <a name="remove-a-transparent-data-encryption-tde-protector-using-powershell"></a>Een TDE-beschermer (Transparent Data Encryption) verwijderen met PowerShell

## <a name="prerequisites"></a>Vereisten

- U moet een Azure-abonnement hebben en beheerder zijn van dat abonnement
- Azure PowerShell moet zijn geïnstalleerd en uitgevoerd.
- In deze handleiding wordt ervan uitgegaan dat u al een sleutel van Azure Key Vault gebruikt als TDE-protector voor een Azure SQL Database of Data Warehouse. Zie [Transparante gegevensversleuteling met BYOK-ondersteuning](transparent-data-encryption-byok-azure-sql.md) voor meer informatie.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

 Raadpleeg [Azure PowerShell installeren](/powershell/azure/install-az-ps) voor instructies over de installatie van de Az-module. Zie [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)voor specifieke cmdlets.

> [!IMPORTANT]
> De PowerShell Azure Resource Manager (RM)-module wordt nog steeds ondersteund door Azure SQL Database, maar alle toekomstige ontwikkelingen zijn voor de Az.Sql-module. De AzureRM-module blijft bugfixes ontvangen tot ten minste december 2020.  De argumenten voor de opdrachten in de Az-module en in de AzureRm-modules zijn nagenoeg identiek. Zie De nieuwe Azure [PowerShell Az-module introduceren](/powershell/azure/new-azureps-module-az)voor meer informatie over de compatibiliteit ervan.

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Zie Azure [CLI installeren](/cli/azure/install-azure-cli)voor installatie .

* * *

## <a name="overview"></a>Overzicht

In deze handleiding wordt beschreven hoe u reageren op een mogelijk gecompromitteerde TDE-protector voor een Azure SQL Database of Data Warehouse die TDE gebruikt met door de klant beheerde sleutels in Azure Key Vault - ByOK-ondersteuning (Bring Your Own Key). Zie de [overzichtspagina](transparent-data-encryption-byok-azure-sql.md)voor meer informatie over BYOK-ondersteuning voor TDE.

De volgende procedures mogen alleen worden uitgevoerd in extreme gevallen of in testomgevingen. Bekijk de handleiding zorgvuldig, want het verwijderen van actief gebruikte TDE-beschermers uit Azure Key Vault zal resulteren in onbeschikbaarheid van **de database.**

Als een sleutel ooit wordt vermoed dat deze is gecompromitteerd, zodat een service of gebruiker ongeautoriseerde toegang tot de sleutel heeft gehad, u de sleutel het beste verwijderen.

Houd er rekening mee dat zodra de TDE-beveiliging is verwijderd in Key Vault, in maximaal 10 minuten alle versleutelde databases alle verbindingen met het bijbehorende foutbericht beginnen te ontkennen en de status ervan wijzigen in [Ontoegankelijk](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-byok-azure-sql#inaccessible-tde-protector).

In de volgende stappen wordt uitgelegd hoe u de TDE Protector duimafdrukken controleert die nog steeds worden gebruikt door Virtual Log Files (VLF) van een bepaalde database.
De duimafdruk van de huidige TDE-beveiliging van de database en de database-id kunnen worden gevonden door het uitvoeren van:

```sql
SELECT [database_id],
       [encryption_state],
       [encryptor_type], /*asymmetric key means AKV, certificate means service-managed keys*/
       [encryptor_thumbprint],
 FROM [sys].[dm_database_encryption_keys]
```

De volgende query retourneert de VLF's en de respectievelijke duimafdrukken van de encryptor. Elke verschillende duimafdruk verwijst naar een andere sleutelin Azure Key Vault (AKV):

```sql
SELECT * FROM sys.dm_db_log_info (database_id)
```

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

De **PowerShell-opdracht Get-AzureRmSqlServerKeyVaultKey** biedt de duimafdruk van de TDE Protector die in de query wordt gebruikt, zodat u zien welke toetsen u moet bewaren en welke toetsen u in AKV moet verwijderen. Alleen sleutels die niet meer door de database worden gebruikt, kunnen veilig worden verwijderd uit Azure Key Vault.

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

De PowerShell **opdracht az sql server key show** biedt de duimafdruk van de TDE Protector die in de query wordt gebruikt, zodat u zien welke toetsen u moet bewaren en welke toetsen u in AKV moet verwijderen. Alleen sleutels die niet meer door de database worden gebruikt, kunnen veilig worden verwijderd uit Azure Key Vault.

* * *

Deze handleiding gaat over twee benaderingen, afhankelijk van het gewenste resultaat na de incidentrespons:

- De Azure SQL-databases / Data Warehouses **toegankelijk** houden
- De Azure SQL-databases / Gegevensmagazijnen **ontoegankelijk** maken

## <a name="to-keep-the-encrypted-resources-accessible"></a>De versleutelde bronnen toegankelijk houden

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

1. Maak een [nieuwe sleutel in Key Vault.](/powershell/module/az.keyvault/add-azkeyvaultkey) Zorg ervoor dat deze nieuwe sleutel is gemaakt in een aparte sleutelkluis van de mogelijk gecompromitteerde TDE-beschermer, omdat toegangscontrole is ingericht op een kluisniveau.

2. Voeg de nieuwe sleutel toe aan de server met behulp van de cmdlets [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) en [Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) en werk deze bij als de nieuwe TDE-beveiliging van de server.

   ```powershell
   # add the key from Key Vault to the server  
   Add-AzSqlServerKeyVaultKey -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> -KeyId <KeyVaultKeyId>

   # set the key as the TDE protector for all resources under the server
   Set-AzSqlServerTransparentDataEncryptionProtector -ResourceGroupName <SQLDatabaseResourceGroupName> `
       -ServerName <LogicalServerName> -Type AzureKeyVault -KeyId <KeyVaultKeyId>
   ```

3. Zorg ervoor dat de server en eventuele replica's zijn bijgewerkt naar de nieuwe TDE-protector met behulp van de [cmdlet Get-AzSqlServerTransparentDataEncryptionProtector.](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector)

   > [!NOTE]
   > Het kan enkele minuten duren voordat de nieuwe TDE-beveiliging zich verspreidt naar alle databases en secundaire databases onder de server.

   ```powershell
   Get-AzSqlServerTransparentDataEncryptionProtector -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

4. Neem een [back-up van de nieuwe sleutel](/powershell/module/az.keyvault/backup-azkeyvaultkey) in Key Vault.

   ```powershell
   # -OutputFile parameter is optional; if removed, a file name is automatically generated.
   Backup-AzKeyVaultKey -VaultName <KeyVaultName> -Name <KeyVaultKeyName> -OutputFile <DesiredBackupFilePath>
   ```

5. Verwijder de gecompromitteerde sleutel uit Key Vault met de cmdlet [Remove-AzKeyVaultKey.](/powershell/module/az.keyvault/remove-azkeyvaultkey)

   ```powershell
   Remove-AzKeyVaultKey -VaultName <KeyVaultName> -Name <KeyVaultKeyName>
   ```

6. Ga als volgt te werk om in de toekomst een sleutel van Key Vault te herstellen met de cmdlet [Restore-AzKeyVaultKey:](/powershell/module/az.keyvault/restore-azkeyvaultkey)

   ```powershell
   Restore-AzKeyVaultKey -VaultName <KeyVaultName> -InputFile <BackupFilePath>
   ```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Zie de Azure [CLI-toetskluis voor](/cli/azure/keyvault/key)opdrachtverwijzing.

1. Maak een [nieuwe sleutel in Key Vault.](/cli/azure/keyvault/key#az-keyvault-key-create) Zorg ervoor dat deze nieuwe sleutel is gemaakt in een aparte sleutelkluis van de mogelijk gecompromitteerde TDE-beschermer, omdat toegangscontrole is ingericht op een kluisniveau.

2. Voeg de nieuwe sleutel toe aan de server en werk deze bij als de nieuwe TDE-beveiliging van de server.

   ```azurecli
   # add the key from Key Vault to the server  
   az sql server key create --kid <KeyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>

   # set the key as the TDE protector for all resources under the server
   az sql server tde-key set --server-key-type AzureKeyVault --kid <KeyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>
   ```

3. Zorg ervoor dat de server en eventuele replica's zijn bijgewerkt naar de nieuwe TDE-beveiliging.

   > [!NOTE]
   > Het kan enkele minuten duren voordat de nieuwe TDE-beveiliging zich verspreidt naar alle databases en secundaire databases onder de server.

   ```azurecli
   az sql server tde-key show --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>
   ```

4. Neem een back-up van de nieuwe sleutel in Key Vault.

   ```azurecli
   # --file parameter is optional; if removed, a file name is automatically generated.
   az keyvault key backup --file <DesiredBackupFilePath> --name <KeyVaultKeyName> --vault-name <KeyVaultName>
   ```

5. Verwijder de gecompromitteerde sleutel uit Key Vault.

   ```azurecli
   az keyvault key delete --name <KeyVaultKeyName> --vault-name <KeyVaultName>
   ```

6. Om in de toekomst een sleutel van Key Vault te herstellen.

   ```azurecli
   az keyvault key restore --file <BackupFilePath> --vault-name <KeyVaultName>
   ```

* * *

## <a name="to-make-the-encrypted-resources-inaccessible"></a>De versleutelde bronnen ontoegankelijk maken

1. Laat de databases vallen die worden versleuteld door de mogelijk gecompromitteerde sleutel.

   Er wordt automatisch een back-up van de database en de logboekbestanden gemaakt, zodat de database op elk moment kan worden hersteld (zolang u de sleutel verstrekt). De databases moeten worden verwijderd voordat een actieve TDE-beschermer wordt verwijderd om mogelijk gegevensverlies van maximaal 10 minuten van de meest recente transacties te voorkomen.

2. Een back-up van het belangrijkste materiaal van de TDE-beschermer in Key Vault.
3. Verwijder de mogelijk gecompromitteerde sleutel uit Key Vault

[!INCLUDE [sql-database-akv-permission-delay](includes/sql-database-akv-permission-delay.md)]

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het roteren van de TDE-beveiliging van een server om te voldoen aan de beveiligingsvereisten: [Draai de transparent Data Encryption protector met PowerShell](transparent-data-encryption-byok-azure-sql-key-rotation.md)
- Aan de slag met Bring Your Own Key-ondersteuning voor TDE: [TDE inschakelen met je eigen sleutel van Key Vault met PowerShell](transparent-data-encryption-byok-azure-sql-configure.md)
