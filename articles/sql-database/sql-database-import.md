---
title: Een BACPAC-bestand importeren om een database te maken
description: Maak een nieuwe Azure SQL-database door een BACPAC-bestand te importeren.
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 06/20/2019
ms.openlocfilehash: 05698596f966f879da1affc58af0122d08d519ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256235"
---
# <a name="quickstart-import-a-bacpac-file-to-a-database-in-azure-sql-database"></a>Snelstart: een BACPAC-bestand importeren in een database in Azure SQL-database

U een SQL Server-database importeren in een database in Azure SQL Database met behulp van een [BACPAC-bestand.](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) U de gegevens `BACPAC` importeren uit een bestand dat is opgeslagen in Azure Blob-opslag (alleen standaardopslag) of uit lokale opslag op een on-premises locatie. Als u de importsnelheid wilt maximaliseren door meer en snellere resources te leveren, schaalt u uw database op naar een hogere servicelaag en rekengrootte tijdens het importproces. U vervolgens omlaag schalen nadat de import is geslaagd.

> [!NOTE]
> Het compatibiliteitsniveau van de geïmporteerde database is gebaseerd op het compatibiliteitsniveau van de brondatabase.

> [!IMPORTANT]
> Na het importeren van uw database u ervoor kiezen om de database te bedienen op het huidige compatibiliteitsniveau (niveau 100 voor de AdventureWorks2008R2-database) of op een hoger niveau. Zie [ALTER DATABASE Compatibility Level](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level) (Compatibiliteitsniveau ALTER DATABASE) voor meer informatie over de implicaties en opties bij het hanteren van een database op een bepaald compatibiliteitsniveau. Zie ook [ALTER DATABASE SCOPED CONFIGURATION](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) voor informatie over aanvullende instellingen op databaseniveau in verband met compatibiliteitsniveaus.

## <a name="using-azure-portal"></a>Azure Portal gebruiken

Bekijk deze video om te zien hoe u importeren uit een BACPAC-bestand in de Azure-portal of lees hieronder verder:

> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Its-just-SQL-Restoring-a-database-to-Azure-SQL-DB-from-backup/player?WT.mc_id=dataexposed-c9-niner]

De [Azure-portal](https://portal.azure.com) ondersteunt *alleen* het maken van één database in Azure SQL Database en *alleen* vanuit een BACPAC-bestand dat is opgeslagen in Azure Blob-opslag.

Het migreren van een database naar een [beheerde instantie](sql-database-managed-instance.md) vanuit een BACPAC-bestand met Azure PowerShell wordt momenteel niet ondersteund. Gebruik in plaats daarvan SQL Server Management Studio of SQLPackage.

> [!NOTE]
> Machines die import-/exportaanvragen verwerken die via de Azure-portal of PowerShell worden ingediend, moeten het BACPAC-bestand en tijdelijke bestanden die worden gegenereerd door het Data-Tier Application Framework (DacFX) opslaan. De vereiste schijfruimte varieert aanzienlijk tussen databases met dezelfde grootte en kan schijfruimte tot 3 keer de grootte van de database vereisen. Machines waarop de import/exportaanvraag wordt uitgevoerd, hebben slechts 450 GB lokale schijfruimte. Als gevolg hiervan kunnen sommige aanvragen `There is not enough space on the disk`mislukken met de fout . In dit geval moet sqlpackage.exe op een machine met voldoende lokale schijfruimte worden uitgevoerd. We moedigen het gebruik van SqlPackage aan om databases van meer dan 150 GB te importeren/exporteren om dit probleem te voorkomen.

1. Als u vanuit een BACPAC-bestand wilt importeren in een nieuwe enkele database met de Azure-portal, opent u de juiste databaseserverpagina en selecteert u vervolgens op de werkbalk **Database importeren**.  

   ![Database importeren1](./media/sql-database-import/sql-server-import-database.png)

1. Selecteer het opslagaccount en de container voor het BACPAC-bestand en selecteer vervolgens het BACPAC-bestand waaruit u wilt importeren.

1. Geef de nieuwe databasegrootte op (meestal hetzelfde als origin) en geef de sql server-referenties op. Zie [Database maken](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current)voor een lijst met mogelijke waarden voor een nieuwe Azure SQL-database.

   ![Database importeren2](./media/sql-database-import/sql-server-import-database-settings.png)

1. Klik op **OK**.

1. Als u de voortgang van een import wilt controleren, opent u de serverpagina van de database en selecteert u onder **Instellingen** **de naam Geschiedenis importeren/exporteren**. Als de import is voltooid, heeft de import de **status Voltooid.**

   ![Status voor het importeren van gegevens](./media/sql-database-import/sql-server-import-database-history.png)

1. Als u wilt controleren of de database live is op de databaseserver, selecteert u **SQL-databases** en controleert u of de nieuwe database **Online**is.

## <a name="using-sqlpackage"></a>SqlPackage gebruiken

Zie [Parameters en eigenschappen importeren](https://docs.microsoft.com/sql/tools/sqlpackage#import-parameters-and-properties)als u een SQL Server-database wilt importeren met het sqlpackage-opdrachtregelhulpprogramma. [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage) SqlPackage heeft de nieuwste [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) en SQL Server Data Tools voor Visual [Studio.](https://msdn.microsoft.com/library/mt204009.aspx) U ook het nieuwste [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876) downloaden vanuit het Microsoft-downloadcentrum.

Voor schaal en prestaties raden we aan sqlpackage te gebruiken in de meeste productieomgevingen in plaats van de Azure-portal te gebruiken. Zie Migreren van SQL Server naar `BACPAC` Azure SQL Database [met BACPAC-bestanden](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/)voor een SQL Server Customer Advisory Team-blog over het migreren met bestanden.

Met de volgende sqlpackage-opdracht importeert u de **AdventureWorks2008R2-database** uit lokale opslag naar een Azure SQL Database-server genaamd **mynewserver20170403**. Het creëert een nieuwe database genaamd **myMigratedDatabase** met een **Premium-servicelaag** en een **P6-servicedoelstelling.** Wijzig deze waarden naar gelang van het geval voor uw omgeving.

```cmd
sqlpackage.exe /a:import /tcs:"Data Source=<serverName>.database.windows.net;Initial Catalog=<migratedDatabase>;User Id=<userId>;Password=<password>" /sf:AdventureWorks2008R2.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
```

> [!IMPORTANT]
> Als u verbinding wilt maken met een SQL Database-server die één database beheert van achter een bedrijfsfirewall, moet de firewall poort 1433 hebben geopend. Als u verbinding wilt maken met een beheerde instantie, moet u een [point-to-site-verbinding](sql-database-managed-instance-configure-p2s.md) of een snelrouteverbinding hebben.

In dit voorbeeld ziet u hoe u een database importeert met SqlPackage met Active Directory Universal Authentication.

```cmd
sqlpackage.exe /a:Import /sf:testExport.bacpac /tdn:NewDacFX /tsn:apptestserver.database.windows.net /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="using-powershell"></a>PowerShell gebruiken

> [!NOTE]
> [Een beheerde instantie](sql-database-managed-instance.md) ondersteunt momenteel geen migratie van een database naar een instantiedatabase vanuit een BACPAC-bestand met Azure PowerShell. Als u wilt importeren in een beheerde instantie, gebruikt u SQL Server Management Studio of SQLPackage.

> [!NOTE]
> De machines die import-/exportaanvragen verwerken die via portal of Powershell worden ingediend, moeten het bacpac-bestand en tijdelijke bestanden die worden gegenereerd door Data-Tier Application Framework (DacFX) opslaan. De vereiste schijfruimte varieert aanzienlijk tussen db's met dezelfde grootte en kan tot 3 keer de databasegrootte in beslag nemen. Machines waarop de import/exportaanvraag wordt uitgevoerd, hebben slechts 450 GB lokale schijfruimte. Als gevolg hiervan kunnen sommige aanvragen mislukken met de fout 'Er is niet genoeg ruimte op de schijf'. In dit geval moet sqlpackage.exe op een machine met voldoende lokale schijfruimte worden uitgevoerd. Wanneer u databases van meer dan 150 GB importeert/exporteert, gebruikt u SqlPackage om dit probleem te voorkomen.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

> [!IMPORTANT]
> De PowerShell Azure Resource Manager (RM)-module wordt nog steeds ondersteund door Azure SQL Database, maar alle toekomstige ontwikkelingen zijn voor de Az.Sql-module. De AzureRM-module blijft bugfixes ontvangen tot ten minste december 2020.  De argumenten voor de opdrachten in de Az-module en in de AzureRm-modules zijn nagenoeg identiek. Zie De nieuwe Azure [PowerShell Az-module introduceren](/powershell/azure/new-azureps-module-az)voor meer informatie over de compatibiliteit ervan.

Gebruik de cmdlet [Nieuw-AzSqlDatabaseImport](/powershell/module/az.sql/new-azsqldatabaseimport) om een importdatabaseaanvraag in te dienen bij de Azure SQL Database-service. Afhankelijk van de databasegrootte kan het enige tijd duren voordat de import is voltooid.

```powershell
$importRequest = New-AzSqlDatabaseImport -ResourceGroupName "<resourceGroupName>" `
    -ServerName "<serverName>" -DatabaseName "<databaseName>" `
    -DatabaseMaxSizeBytes "<databaseSizeInBytes>" -StorageKeyType "StorageAccessKey" `
    -StorageKey $(Get-AzStorageAccountKey `
        -ResourceGroupName "<resourceGroupName>" -StorageAccountName "<storageAccountName>").Value[0] `
        -StorageUri "https://myStorageAccount.blob.core.windows.net/importsample/sample.bacpac" `
        -Edition "Standard" -ServiceObjectiveName "P6" `
        -AdministratorLogin "<userId>" `
        -AdministratorLoginPassword $(ConvertTo-SecureString -String "<password>" -AsPlainText -Force)
```

U de cmdlet [Get-AzSqlDatabaseImportImportStatus](/powershell/module/az.sql/get-azsqldatabaseimportexportstatus) gebruiken om de voortgang van de import te controleren. Het uitvoeren van de cmdlet `Status: InProgress`onmiddellijk na het verzoek keert meestal terug . De import is voltooid `Status: Succeeded`wanneer u .

```powershell
$importStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink

[Console]::Write("Importing")
while ($importStatus.Status -eq "InProgress") {
    $importStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
    [Console]::Write(".")
    Start-Sleep -s 10
}

[Console]::WriteLine("")
$importStatus
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Gebruik de opdracht [az-sql-db-import](/cli/azure/sql/db#az-sql-db-import) om een importdatabaseaanvraag in te dienen bij de Azure SQL Database-service. Afhankelijk van de databasegrootte kan het enige tijd duren voordat de import is voltooid.

```azurecli
# get the storage account key
az storage account keys list --resource-group "<resourceGroup>" --account-name "<storageAccount>"

az sql db import --resource-group "<resourceGroup>" --server "<server>" --name "<database>" `
    --storage-key-type "StorageAccessKey" --storage-key "<storageAccountKey>" `
    --storage-uri "https://myStorageAccount.blob.core.windows.net/importsample/sample.bacpac" `
    -u "<userId>" -p "<password>"
```

* * *

> [!TIP]
> Zie Een database [importeren uit een BACPAC-bestand](scripts/sql-database-import-from-bacpac-powershell.md)voor een ander scriptvoorbeeld.

## <a name="limitations"></a>Beperkingen

Importeren in een database in een elastische groep wordt niet ondersteund. U gegevens importeren in één database en de database vervolgens verplaatsen naar een elastische groep.

## <a name="import-using-wizards"></a>Importeren met wizards

U deze wizards ook gebruiken.

- [Wizard Gegevenslaagtoepassing importeren in SQL Server Management Studio](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database#using-the-import-data-tier-application-wizard).
- [Wizard Sql Server importeren en exporteren](https://docs.microsoft.com/sql/integration-services/import-export-data/start-the-sql-server-import-and-export-wizard).

## <a name="next-steps"></a>Volgende stappen

- Zie [Quickstart: Azure SQL Database: SQL Server Management Studio gebruiken om](sql-database-connect-query-ssms.md)verbinding te maken en querygegevens op te vragen voor meer informatie over het maken van verbinding met een geïmporteerde SQL-database.
- Raadpleeg dit blogartikel van het SQL Server-klantadviesteam over migratie met behulp van BACPAC-bestanden: [Migrating from SQL Server to Azure SQL Database using BACPAC Files](https://techcommunity.microsoft.com/t5/DataCAT/Migrating-from-SQL-Server-to-Azure-SQL-Database-using-Bacpac/ba-p/305407) (Migreren van SQL Server naar Azure SQL Database met BACPAC-bestanden).
- Zie [SQL Server-databasemigratie naar Azure SQL Database](sql-database-single-database-migrate.md)voor een discussie over het volledige SQL Server-databasemigratieproces, inclusief prestatieaanbevelingen.
- Zie [Azure Storage Security Guide](https://docs.microsoft.com/azure/storage/common/storage-security-guide)voor meer informatie over het veilig beheren en delen van opslagsleutels en gedeelde toegangshandtekeningen.
