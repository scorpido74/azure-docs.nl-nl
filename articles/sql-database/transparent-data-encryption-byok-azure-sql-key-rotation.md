---
title: TDE-Protector draaien-Power shell
description: Meer informatie over het roteren van de Transparent Data Encryption-Protector (TDE) voor een Azure SQL-Server.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: aliceku
ms.author: aliceku
ms.reviewer: vanto
ms.date: 03/12/2019
ms.openlocfilehash: 5bfcacb9348940e0b36947f6e4e0d27839de35bb
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73824684"
---
# <a name="rotate-the-transparent-data-encryption-tde-protector-using-powershell"></a>De Transparent Data Encryption-Protector (TDE) draaien met Power shell

In dit artikel wordt de belangrijkste draaiing beschreven van een Azure SQL-Server met behulp van een TDE-Protector van Azure Key Vault. Als u de TDE-Protector van Azure SQL Server roteert, schakelt u over naar een nieuwe asymmetrische sleutel die de data bases op de server beveiligt. Het draaien van sleutels is een online bewerking die slechts enkele seconden kan duren, omdat hiermee alleen de gegevens versleutelings sleutel van de data base wordt gedecodeerd en opnieuw versleuteld, niet de volledige data base.

In deze hand leiding worden twee opties besproken voor het draaien van de TDE-protector op de-server.

> [!NOTE]
> Een onderbroken SQL Data Warehouse moet worden hervat voordat de sleutel wordt gedraaid.
>

> [!IMPORTANT]
> **Verwijder geen** eerdere versies van de sleutel na een rollover.  Wanneer sleutels worden doorgevoerd, worden er nog enkele gegevens versleuteld met de vorige sleutels, zoals oudere back-ups van de data base. 
>

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> De Power shell-Azure Resource Manager module wordt nog steeds ondersteund door Azure SQL Database, maar alle toekomstige ontwikkeling is voor de module AZ. SQL. Zie [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)voor deze cmdlets. De argumenten voor de opdrachten in de module AZ en in de AzureRm-modules zijn aanzienlijk identiek.

- In deze hand leiding wordt ervan uitgegaan dat u al een sleutel gebruikt uit Azure Key Vault als de TDE-Protector voor een Azure SQL Database of Data Warehouse. Zie [transparent Data Encryption met BYOK-ondersteuning](transparent-data-encryption-byok-azure-sql.md).
- Azure PowerShell moet zijn geïnstalleerd en worden uitgevoerd.
- [Aanbevolen, maar optioneel] Maak eerst het sleutel materiaal voor de TDE-Protector in een HSM (Hardware Security module) of een lokaal sleutel archief en importeer het sleutel materiaal in Azure Key Vault. Volg de [instructies voor het gebruik van een Hardware Security module (hsm) en Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-get-started) voor meer informatie.

## <a name="manual-key-rotation"></a>Hand matige rotatie van de sleutel

Hand matige rotatie van de sleutel maakt gebruik van de cmdlets [add-AzKeyVaultKey](/powershell/module/az.keyvault/Add-AzKeyVaultKey), [add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey)en [set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) om een volledig nieuwe sleutel toe te voegen, die onder een nieuwe sleutel naam kan staan of zelfs een andere sleutel kluis. Het gebruik van deze aanpak biedt ondersteuning voor het toevoegen van dezelfde sleutel aan verschillende sleutel kluizen ter ondersteuning van scenario's met hoge Beschik baarheid en geo-Dr.

>[!NOTE]
>De gecombineerde lengte van de naam van de sleutel kluis en de naam van de sleutel mag niet langer zijn dan 94 tekens.

   ```powershell
   # Add a new key to Key Vault
   Add-AzKeyVaultKey `
   -VaultName <KeyVaultName> `
   -Name <KeyVaultKeyName> `
   -Destination <HardwareOrSoftware>

   # Add the new key from Key Vault to the server
   Add-AzSqlServerKeyVaultKey `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName>
  
   <# Set the key as the TDE protector for all resources under the server #>
   Set-AzSqlServerTransparentDataEncryptionProtector `
   -Type AzureKeyVault `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName>
   ```


## <a name="other-useful-powershell-cmdlets"></a>Andere handige Power shell-cmdlets

- Gebruik de cmdlet [set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) om de TDe-Protector te scha kelen van micro soft die wordt beheerd naar de BYOK-modus.

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector `
   -Type AzureKeyVault `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName>
   ```

- Als u de TDE-Protector wilt overschakelen van de BYOK-modus naar micro soft-beheerd, gebruikt u de cmdlet [set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) .

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector `
   -Type ServiceManaged `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName> 
   ``` 

## <a name="next-steps"></a>Volgende stappen

- In het geval van een beveiligings risico leert u hoe u een mogelijk aangetast TDE-Protector verwijdert: [een mogelijk versleutelde code verwijderen](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md) 

- Aan de slag met Azure Key Vault-integratie en Bring Your Own Key ondersteuning voor TDE: [Schakel TDe met behulp van uw eigen sleutel in Key Vault met behulp van Power shell](transparent-data-encryption-byok-azure-sql-configure.md)
