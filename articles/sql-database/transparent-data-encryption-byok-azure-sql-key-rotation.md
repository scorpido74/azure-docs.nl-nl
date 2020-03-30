---
title: TDE-beschermer draaien - PowerShell
description: Meer informatie over het roteren van de TDE-beveiliging (Transparent Data Encryption) voor een Azure SQL-server.
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
ms.openlocfilehash: aaed06ac086893f63fde530e46b936b3fb637766
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80067171"
---
# <a name="rotate-the-transparent-data-encryption-tde-protector-using-powershell"></a>De TDE-beschermer (Transparent Data Encryption) roteren met PowerShell

In dit artikel wordt de sleutelrotatie voor een Azure SQL-server beschreven met behulp van een TDE-protector van Azure Key Vault. Het roteren van de TDE-beveiliging van een Azure SQL-server betekent overschakelen naar een nieuwe asymmetrische sleutel die de databases op de server beschermt. Sleutelrotatie is een online bewerking en duurt slechts een paar seconden om te voltooien, omdat dit alleen decodeert en hercodeert van de database data encryptie sleutel, niet de hele database.

Deze handleiding bespreekt twee opties om de TDE-beveiliging op de server te draaien.

> [!NOTE]
> Een onderbroken SQL Data Warehouse moet worden hervat voordat de toets wordt uitgevoerd.

> [!IMPORTANT]
> Verwijder eerdere versies van de sleutel niet na een rollover. Wanneer sleutels worden omgerold, worden sommige gegevens nog steeds versleuteld met de vorige sleutels, zoals oudere databaseback-ups.

## <a name="prerequisites"></a>Vereisten

- In deze handleiding wordt ervan uitgegaan dat u al een sleutel van Azure Key Vault gebruikt als TDE-protector voor een Azure SQL Database of Data Warehouse. Zie [Transparante gegevensversleuteling met BYOK-ondersteuning](transparent-data-encryption-byok-azure-sql.md).
- Azure PowerShell moet zijn geïnstalleerd en uitgevoerd.
- [Aanbevolen, maar optioneel] Maak eerst het belangrijkste materiaal voor de TDE-beveiliging in een Hardware Security Module (HSM) of lokale sleutelopslag en importeer het belangrijkste materiaal naar Azure Key Vault. Volg de [instructies voor het gebruik van een hardwarebeveiligingsmodule (HSM) en Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-get-started) voor meer informatie.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Raadpleeg [Azure PowerShell installeren](/powershell/azure/install-az-ps) voor instructies over de installatie van de Az-module. Zie [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)voor specifieke cmdlets.

> [!IMPORTANT]
> De PowerShell Azure Resource Manager (RM)-module wordt nog steeds ondersteund door Azure SQL Database, maar alle toekomstige ontwikkelingen zijn voor de Az.Sql-module. De AzureRM-module blijft bugfixes ontvangen tot ten minste december 2020.  De argumenten voor de opdrachten in de Az-module en in de AzureRm-modules zijn nagenoeg identiek. Zie De nieuwe Azure [PowerShell Az-module introduceren](/powershell/azure/new-azureps-module-az)voor meer informatie over de compatibiliteit ervan.

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Zie Azure [CLI installeren](/cli/azure/install-azure-cli)voor installatie .

* * *

## <a name="manual-key-rotation"></a>Handmatige sleutelrotatie

Handmatige sleutelrotatie maakt gebruik van de volgende opdrachten om een volledig nieuwe sleutel toe te voegen, die onder een nieuwe sleutelnaam of zelfs een andere sleutelkluis kan zijn. Het gebruik van deze aanpak ondersteunt het toevoegen van dezelfde sleutel aan verschillende sleutelkluizen ter ondersteuning van hoge beschikbaarheid en geo-dr scenario's.

> [!NOTE]
> De gecombineerde lengte voor de naam van de sleutelkluis en de sleutelnaam mag niet groter zijn dan 94 tekens.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Gebruik de cmdlets [Add-AzKeyVaultKey](/powershell/module/az.keyvault/Add-AzKeyVaultKey), [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey)en [Set-AzSqlServerTransparentDataEncryptionProtector.](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector)

```powershell
# add a new key to Key Vault
Add-AzKeyVaultKey -VaultName <keyVaultName> -Name <keyVaultKeyName> -Destination <hardwareOrSoftware>

# add the new key from Key Vault to the server
Add-AzSqlServerKeyVaultKey -KeyId <keyVaultKeyId> -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
  
# set the key as the TDE protector for all resources under the server
Set-AzSqlServerTransparentDataEncryptionProtector -Type AzureKeyVault -KeyId <keyVaultKeyId> `
   -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Gebruik de [az keyvault key create](/cli/azure/keyvault/key#az-keyvault-key-create), az sql server key [create](/cli/azure/sql/server/key#az-sql-server-key-create)en az sql [server tde-key set](/cli/azure/sql/server/tde-key#az-sql-server-tde-key-set) commands.

```azurecli
# add a new key to Key Vault
az keyvault key create --name <keyVaultKeyName> --vault-name <keyVaultName> --protection <hsmOrSoftware>

# add the new key from Key Vault to the server
az sql server key create --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>

# set the key as the TDE protector for all resources under the server
az sql server tde-key set --server-key-type AzureKeyVault --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
```

* * *

## <a name="useful-powershell-cmdlets"></a>Nuttige PowerShell-cmdlets

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

- Als u de TDE-protector wilt overschakelen van microsoft-managed naar BYOK-modus, gebruikt u de cmdlet [Set-AzSqlServerTransparentDataEncryptionProtector.](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector)

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector -Type AzureKeyVault `
       -KeyId <keyVaultKeyId> -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
   ```

- Als u de TDE-beveiliging wilt overschakelen van byok-modus naar microsoft-beheerd, gebruikt u de cmdlet [Set-AzSqlServerTransparentDataEncryptionProtector.](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector)

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector -Type ServiceManaged `
       -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
   ```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

In de volgende voorbeelden wordt gebruik gemaakt [van de az sql server tde-key set](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector).

- Om de TDE-beschermer over te schakelen van Microsoft-managed naar BYOK-modus,

   ```azurecli
   az sql server tde-key set --server-key-type AzureKeyVault --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
   ```

- Om de TDE-beschermer over te schakelen van BYOK-modus naar Door Microsoft beheerde,

   ```azurecli
   az sql server tde-key set --server-key-type ServiceManaged --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
   ```

* * *

## <a name="next-steps"></a>Volgende stappen

- In het geval van een veiligheidsrisico, leer hoe u een mogelijk gecompromitteerde TDE-beschermer verwijderen: [Verwijder een mogelijk gecompromitteerde sleutel](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md)

- Aan de slag met Azure Key Vault-integratie en Bring Your Own Key-ondersteuning voor TDE: [TDE inschakelen met uw eigen sleutel van Key Vault met PowerShell](transparent-data-encryption-byok-azure-sql-configure.md)
