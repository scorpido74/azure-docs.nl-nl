---
title: TDE-certificaat migreren - beheerde instantie
description: Certificaat dat databaseversleutelingssleutel van een database beschermt, migreren met transparante gegevensversleuteling naar Azure SQL Database Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: carlrab, jovanpop
ms.date: 04/25/2019
ms.openlocfilehash: 0f6e379287323d9353acd887cf30d5c9c0065959
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74555386"
---
# <a name="migrate-certificate-of-tde-protected-database-to-azure-sql-database-managed-instance"></a>Certificaat van een met TDE beveiligde database migreren naar een beheerd exemplaar van Azure SQL Database

Wanneer u een database migreert die is beveiligd door [Transparante gegevensversleuteling](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption) naar Azure SQL Database Managed Instance met behulp van de native restore-optie, moet het bijbehorende certificaat van de on-premises of IaaS SQL Server worden gemigreerd voordat de database wordt hersteld. Dit artikel leidt u door het proces van handmatige migratie van het certificaat naar een met Azure SQL Database beheerd exemplaar:

> [!div class="checklist"]
> * Certificaat exporteren naar een PFX-bestand (Personal Information Exchange)
> * Certificaat uit bestand uitpakken naar base-64-tekenreeks
> * Het uploaden met behulp van PowerShell-cmdlet

Zie [Uw on-premises database migreren naar een beheerd exemplaar met behulp van Azure Database Migration Service ](../dms/tutorial-sql-server-to-managed-instance.md) voor een alternatieve optie met behulp van een volledig beheerde service voor naadloze migratie van zowel een met TDE beveiligde database als het bijbehorende certificaat.

> [!IMPORTANT]
> Het gemigreerde certificaat wordt alleen gebruikt voor het herstellen van de met TDE beveiligde database. Kort nadat het herstel is uitgevoerd, wordt het gemigreerde certificaat vervangen door een andere protector, een door de service beheerd certificaat of een asymmetrische sleutel uit de sleutelkluis, afhankelijk van het type transparante gegevensversleuteling dat u op de instantie hebt ingesteld.

## <a name="prerequisites"></a>Vereisten

U moet de volgende vereiste zaken hebben om de stappen in dit artikel uit te voeren:

- Het [Pvk2Pfx](https://docs.microsoft.com/windows-hardware/drivers/devtest/pvk2pfx)-opdrachtregelprogramma is geïnstalleerd op de on-premises server of een andere computer met toegang tot het certificaat dat als een bestand wordt geëxporteerd. Het Pvk2Pfx-hulpprogramma maakt deel uit van de [Enterprise Windows Driver Kit](https://docs.microsoft.com/windows-hardware/drivers/download-the-wdk), een onafhankelijke op zichzelf staande opdrachtregelomgeving.
- [Windows PowerShell](/powershell/scripting/install/installing-windows-powershell) versie 5.0 of hoger is geïnstalleerd.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Zorg ervoor dat u over de volgende zaken beschikt:

- Azure PowerShell-module [geïnstalleerd en bijgewerkt.](https://docs.microsoft.com/powershell/azure/install-az-ps)
- [Az.Sql-module](https://www.powershellgallery.com/packages/Az.Sql).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

> [!IMPORTANT]
> De PowerShell Azure Resource Manager-module wordt nog steeds ondersteund door Azure SQL Database, maar alle toekomstige ontwikkelingen zijn voor de Az.Sql-module. Zie [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)voor deze cmdlets. De argumenten voor de opdrachten in de Az-module en in de AzureRm-modules zijn nagenoeg identiek.

Voer de volgende opdrachten uit in PowerShell om de module te installeren/bijwerken:

```azurepowershell
Install-Module -Name Az.Sql
Update-Module -Name Az.Sql
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren](/cli/azure/install-azure-cli).

* * *

## <a name="export-tde-certificate-to-a-personal-information-exchange-pfx-file"></a>TDE-certificaat exporteren naar een PFX-bestand (Personal Information Exchange)

Het certificaat kan rechtstreeks vanuit de SQL-bronserver of uit het certificaatarchief worden geëxporteerd, als het daar wordt bewaard.

### <a name="export-certificate-from-the-source-sql-server"></a>Certificaat exporteren van de SQL-bronserver

Gebruik de volgende stappen om het certificaat te exporteren met SQL Server Management Studio en naar een pfx-indeling te converteren. De algemene namen *TDE_Cert* en *full_path* worden gebruikt voor de certificaat- en de bestandsnamen en -paden in de stappen. Deze moeten worden vervangen door de werkelijke namen.

1. Open een nieuw queryvenster in SSMS en maak verbinding met de SQL-bronserver.

1. Gebruik het volgende script om met TDE beveiligde databases weer te geven en de naam op te halen van het certificaat dat de versleuteling beveiligt van de database die moet worden gemigreerd:

   ```sql
   USE master
   GO
   SELECT db.name as [database_name], cer.name as [certificate_name]
   FROM sys.dm_database_encryption_keys dek
   LEFT JOIN sys.certificates cer
   ON dek.encryptor_thumbprint = cer.thumbprint
   INNER JOIN sys.databases db
   ON dek.database_id = db.database_id
   WHERE dek.encryption_state = 3
   ```

   ![lijst met TDE-certificaten](./media/sql-database-managed-instance-migrate-tde-certificate/onprem-certificate-list.png)

1. Voer het volgende script uit om het certificaat te exporteren naar een bestandspaar (.cer en .pvk), waarbij de gegevens van de openbare en persoonlijke sleutel behouden blijven:

   ```sql
   USE master
   GO
   BACKUP CERTIFICATE TDE_Cert
   TO FILE = 'c:\full_path\TDE_Cert.cer'
   WITH PRIVATE KEY (
     FILE = 'c:\full_path\TDE_Cert.pvk',
     ENCRYPTION BY PASSWORD = '<SomeStrongPassword>'
   )
   ```

   ![back-up maken van TDE-certificaat](./media/sql-database-managed-instance-migrate-tde-certificate/backup-onprem-certificate.png)

1. Gebruik de PowerShell-console om certificaatgegevens uit een nieuw gemaakt bestandspaar naar een PFX-bestand (Personal Information Exchange) te kopiëren met behulp van het Pvk2Pfx-hulpprogramma:

   ```cmd
   .\pvk2pfx -pvk c:/full_path/TDE_Cert.pvk  -pi "<SomeStrongPassword>" -spc c:/full_path/TDE_Cert.cer -pfx c:/full_path/TDE_Cert.pfx
   ```

### <a name="export-certificate-from-certificate-store"></a>Certificaat exporteren uit het certificaatarchief

Als het certificaat is opgeslagen in het certificaatarchief van de lokale computer van de SQL-server, kan het worden geëxporteerd met behulp van de volgende stappen:

1. Open de PowerShell-console en voer de volgende opdracht uit om de invoegtoepassing Certificaten van Microsoft Management Console te openen:

   ```cmd
   certlm
   ```

2. Vouw in de invoegtoepassing Certificaten van MMC het pad Persoonlijke -> Certificaten uit om de lijst met certificaten weer te geven

3. Klik met de rechtermuisknop op het certificaat en klik op Exporteren…

4. Volg de wizard om het certificaat en de persoonlijke sleutel te exporteren naar een PFX-indeling (Personal Information Exchange)

## <a name="upload-certificate-to-azure-sql-database-managed-instance-using-azure-powershell-cmdlet"></a>Certificaat uploaden naar Azure SQL Database Managed Instance met Azure PowerShell-cmdlet

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

1. Begin met de voorbereidingsstappen in PowerShell:

   ```azurepowershell
   # import the module into the PowerShell session
   Import-Module Az
   # connect to Azure with an interactive dialog for sign-in
   Connect-AzAccount
   # list subscriptions available and copy id of the subscription target Managed Instance belongs to
   Get-AzSubscription
   # set subscription for the session
   Select-AzSubscription <subscriptionId>
   ```

2. Wanneer alle voorbereidingsstappen klaar zijn, voert u de volgende opdrachten uit om het met base-64 gecodeerde certificaat te uploaden naar het beheerde doelexemplaar:

   ```azurepowershell
   $fileContentBytes = Get-Content 'C:/full_path/TDE_Cert.pfx' -Encoding Byte
   $base64EncodedCert = [System.Convert]::ToBase64String($fileContentBytes)
   $securePrivateBlob = $base64EncodedCert  | ConvertTo-SecureString -AsPlainText -Force
   $password = "<password>"
   $securePassword = $password | ConvertTo-SecureString -AsPlainText -Force
   Add-AzSqlManagedInstanceTransparentDataEncryptionCertificate -ResourceGroupName "<resourceGroupName>" `
       -ManagedInstanceName "<managedInstanceName>" -PrivateBlob $securePrivateBlob -Password $securePassword
   ```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

U moet eerst [een Azure Key Vault instellen](/azure/key-vault/key-vault-manage-with-cli2) met uw *.pfx-bestand.*

1. Begin met de voorbereidingsstappen in PowerShell:

   ```azurecli
   # connect to Azure with an interactive dialog for sign-in
   az login

   # list subscriptions available and copy id of the subscription target Managed Instance belongs to
   az account list

   # set subscription for the session
   az account set --subscription <subscriptionId>
   ```

1. Wanneer alle voorbereidingsstappen klaar zijn, voert u de volgende opdrachten uit om het met base-64 gecodeerde certificaat te uploaden naar het beheerde doelexemplaar:

   ```azurecli
   az sql mi tde-key set --server-key-type AzureKeyVault --kid "<keyVaultId>" `
       --managed-instance "<managedInstanceName>" --resource-group "<resourceGroupName>"
   ```

* * *

Het certificaat is nu beschikbaar voor het opgegeven beheerde exemplaar en de back-up van de bijbehorende met TDE beveiligde database kan worden teruggezet.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u de versleutelingssleutel van de database migreren met Transparante gegevensversleuteling, van de on-premises of IaaS SQL Server naar Azure SQL Database Managed Instance.

Zie [Een databaseback-up terugzetten naar een met Azure SQL Database beheerd exemplaar](sql-database-managed-instance-get-started-restore.md) voor informatie over het terugzetten van een databaseback-up naar een met Azure SQL Database beheerd exemplaar.
