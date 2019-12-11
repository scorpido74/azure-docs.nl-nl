---
title: TDE-Protector verwijderen-Power shell
description: Instructies voor het reageren op een mogelijk gemanipuleerde TDE-Protector voor een Azure SQL Database of Data Warehouse met behulp van TDE met uw eigen Key-ondersteuning (BYOK).
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
ms.openlocfilehash: d4c9b926afe93f52946c5f1adf40835f72812f2a
ms.sourcegitcommit: d614a9fc1cc044ff8ba898297aad638858504efa
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74995819"
---
# <a name="remove-a-transparent-data-encryption-tde-protector-using-powershell"></a>Een Transparent Data Encryption-Protector (TDE) verwijderen met behulp van Power shell

## <a name="prerequisites"></a>Vereisten

- U moet een Azure-abonnement hebben en een beheerder zijn voor dat abonnement
- Azure PowerShell moet zijn geïnstalleerd en worden uitgevoerd.
- In deze hand leiding wordt ervan uitgegaan dat u al een sleutel gebruikt uit Azure Key Vault als de TDE-Protector voor een Azure SQL Database of Data Warehouse. Zie [transparent Data Encryption met BYOK-ondersteuning](transparent-data-encryption-byok-azure-sql.md) voor meer informatie.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

 Raadpleeg [Azure PowerShell installeren](/powershell/azure/install-az-ps) voor instructies over de installatie van de Az-module. Zie [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)voor specifieke cmdlets.

> [!IMPORTANT]
> De module Power shell Azure Resource Manager (RM) wordt nog steeds ondersteund door Azure SQL Database, maar alle toekomstige ontwikkeling is voor de module AZ. SQL. De AzureRM-module blijft oplossingen ontvangen tot ten minste december 2020.  De argumenten voor de opdrachten in de module AZ en in de AzureRm-modules zijn aanzienlijk identiek. Zie [Inleiding tot de nieuwe Azure PowerShell AZ-module](/powershell/azure/new-azureps-module-az)voor meer informatie over de compatibiliteit.

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Zie [Azure cli installeren](/cli/azure/install-azure-cli)voor installatie.

* * *

## <a name="overview"></a>Overzicht

In deze hand leiding wordt beschreven hoe u kunt reageren op een mogelijk aangetast TDE-Protector voor een Azure SQL Database of Data Warehouse dat TDE gebruikt met door de klant beheerde sleutels in Azure Key Vault-Bring Your Own Key (BYOK)-ondersteuning. Zie de [pagina overzicht](transparent-data-encryption-byok-azure-sql.md)voor meer informatie over BYOK-ondersteuning voor TDe.

De volgende procedures mogen alleen worden uitgevoerd in uitzonderlijke gevallen of in een test omgeving. Lees de hand leiding aandachtig door als u actieve gebruikte TDE-beveiligingen uit Azure Key Vault kunt verwijderen, wat tot **gegevens verlies**kan leiden.

Als een sleutel wordt vermoed om te worden aangetast, bijvoorbeeld dat een service of gebruiker ongeoorloofde toegang tot de sleutel had, is het raadzaam om de sleutel te verwijderen.

Wanneer de TDE-Protector wordt verwijderd in Key Vault, **worden alle verbindingen met de versleutelde data bases op de server geblokkeerd en worden deze data bases offline gezet en binnen 24 uur verwijderd**. Oude back-ups die zijn versleuteld met de gemanipuleerde sleutel, zijn niet meer toegankelijk.

In de volgende stappen wordt beschreven hoe u kunt controleren of de TDE-beveiligings vingerafdrukten nog steeds worden gebruikt door virtuele logboek bestanden (VLF) van een bepaalde data base.
De vinger afdruk van de huidige TDE-Protector van de data base en de data base-ID kunt u vinden door het volgende uit te voeren:

```sql
SELECT [database_id], 
       [encryption_state], 
       [encryptor_type], /*asymmetric key means AKV, certificate means service-managed keys*/ 
       [encryptor_thumbprint], 
 FROM [sys].[dm_database_encryption_keys]
```

Met de volgende query worden de VLFs en de respectieve vinger afdrukken van de versleutelings functie geretourneerd. Elke andere vinger afdruk verwijst naar een andere sleutel in Azure Key Vault (Azure):

```sql
SELECT * FROM sys.dm_db_log_info (database_id)
```

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

De Power shell **-opdracht Get-AzureRmSqlServerKeyVaultKey** biedt de vinger afdruk van de TDe-Protector die in de query wordt gebruikt, zodat u kunt zien welke sleutels moeten worden bewaard en welke sleutels moeten worden verwijderd in Azure. Alleen sleutels die niet meer door de Data Base worden gebruikt, kunnen veilig worden verwijderd uit Azure Key Vault.

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

De Power shell-opdracht **AZ SQL Server key show** biedt de vinger afdruk van de TDe-Protector die in de query wordt gebruikt, zodat u kunt zien welke sleutels moeten worden bewaard en welke sleutels moeten worden verwijderd in Azure. Alleen sleutels die niet meer door de Data Base worden gebruikt, kunnen veilig worden verwijderd uit Azure Key Vault.

* * *

Deze hand leiding gaat over twee benaderingen, afhankelijk van het gewenste resultaat na de reactie op incidenten:

- De Azure SQL-data bases/data warehouses **toegankelijk** te maken
- De Azure SQL-data bases/data warehouses **ontoegankelijk** maken

## <a name="to-keep-the-encrypted-resources-accessible"></a>De versleutelde bronnen toegankelijk te maken

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

1. Maak een [nieuwe sleutel in Key Vault](/powershell/module/az.keyvault/add-azkeyvaultkey). Zorg ervoor dat deze nieuwe sleutel wordt gemaakt in een afzonderlijke sleutel kluis van de mogelijk aangetast TDE-beveiliging, omdat toegangs beheer is ingericht op het niveau van de kluis.

2. Voeg de nieuwe sleutel toe aan de server met de cmdlets [add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) en [set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) en werk deze bij als de nieuwe TDe-beveiliging van de server.

   ```powershell
   # add the key from Key Vault to the server  
   Add-AzSqlServerKeyVaultKey -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> -KeyId <KeyVaultKeyId>

   # set the key as the TDE protector for all resources under the server
   Set-AzSqlServerTransparentDataEncryptionProtector -ResourceGroupName <SQLDatabaseResourceGroupName> `
       -ServerName <LogicalServerName> -Type AzureKeyVault -KeyId <KeyVaultKeyId>
   ```

3. Zorg ervoor dat de server en eventuele replica's zijn bijgewerkt naar de nieuwe TDE-beveiliging met behulp van de cmdlet [Get-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) .

   > [!NOTE]
   > Het kan enkele minuten duren voordat de nieuwe TDE-Protector is door gegeven aan alle data bases en secundaire data bases op de server.

   ```powershell
   Get-AzSqlServerTransparentDataEncryptionProtector -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

4. Maak een [back-up van de nieuwe sleutel](/powershell/module/az.keyvault/backup-azkeyvaultkey) in Key Vault.

   ```powershell
   # -OutputFile parameter is optional; if removed, a file name is automatically generated.
   Backup-AzKeyVaultKey -VaultName <KeyVaultName> -Name <KeyVaultKeyName> -OutputFile <DesiredBackupFilePath>
   ```

5. Verwijder de aangetaste sleutel uit Key Vault met de cmdlet [Remove-AzKeyVaultKey](/powershell/module/az.keyvault/remove-azkeyvaultkey) .

   ```powershell
   Remove-AzKeyVaultKey -VaultName <KeyVaultName> -Name <KeyVaultKeyName>
   ```

6. Een sleutel herstellen naar Key Vault in de toekomst met de cmdlet [Restore-AzKeyVaultKey](/powershell/module/az.keyvault/restore-azkeyvaultkey) :

   ```powershell
   Restore-AzKeyVaultKey -VaultName <KeyVaultName> -InputFile <BackupFilePath>
   ```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Zie de [Azure cli](/cli/azure/keyvault/key)-sleutel kluis voor naslag informatie over de opdracht.

1. Maak een [nieuwe sleutel in Key Vault](/cli/azure/keyvault/key#az-keyvault-key-create). Zorg ervoor dat deze nieuwe sleutel wordt gemaakt in een afzonderlijke sleutel kluis van de mogelijk aangetast TDE-beveiliging, omdat toegangs beheer is ingericht op het niveau van de kluis.

2. Voeg de nieuwe sleutel toe aan de server en werk deze bij als de nieuwe TDE-beveiliging van de server.

   ```powershell
   # add the key from Key Vault to the server  
   az sql server key create --kid <KeyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>

   # set the key as the TDE protector for all resources under the server
   az sql server tde-key set --server-key-type AzureKeyVault --kid <KeyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>
   ```

3. Zorg ervoor dat de server en eventuele replica's zijn bijgewerkt naar de nieuwe TDE-beveiliging.

   > [!NOTE]
   > Het kan enkele minuten duren voordat de nieuwe TDE-Protector is door gegeven aan alle data bases en secundaire data bases op de server.

   ```powershell
   az sql server tde-key show --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>
   ```

4. Maak een back-up van de nieuwe sleutel in Key Vault.

   ```powershell
   # --file parameter is optional; if removed, a file name is automatically generated.
   az keyvault key backup --file <DesiredBackupFilePath> --name <KeyVaultKeyName> --vault-name <KeyVaultName>
   ```

5. Verwijder de aangetaste sleutel uit Key Vault.

   ```powershell
   az keyvault key delete --name <KeyVaultKeyName> --vault-name <KeyVaultName>
   ```

6. Een sleutel herstellen naar Key Vault in de toekomst.

   ```powershell
   az keyvault key restore --file <BackupFilePath> --vault-name <KeyVaultName>
   ```

* * *

## <a name="to-make-the-encrypted-resources-inaccessible"></a>De versleutelde bronnen ontoegankelijk maken

1. Verwijder de data bases die zijn versleuteld door de code die mogelijk is aangetast.

   Er wordt automatisch een back-up van de data base en logboek bestanden gemaakt, zodat de Data Base op elk gewenst moment kan worden hersteld (op voor waarde dat u de sleutel opgeeft). De data bases moeten worden verwijderd voordat een actieve TDE-Protector kan worden verwijderd om te voor komen dat er Maxi maal tien minuten van de meest recente trans acties verloren gaan.

2. Maak een back-up van het sleutel materiaal van de TDE-Protector in Key Vault.
3. De mogelijk gemanipuleerde sleutel uit Key Vault verwijderen

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het draaien van de TDE-Protector van een server om te voldoen aan de beveiligings vereisten: [de transparent Data Encryption-Protector draaien met Power shell](transparent-data-encryption-byok-azure-sql-key-rotation.md)
- Aan de slag met Bring Your Own Key ondersteuning voor TDE: [Schakel TDe met behulp van uw eigen sleutel in vanuit Key Vault met behulp van Power shell](transparent-data-encryption-byok-azure-sql-configure.md)
