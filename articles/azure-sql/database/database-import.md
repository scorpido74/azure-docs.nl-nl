---
title: Een BACPAC-bestand importeren om een Data Base te maken in Azure SQL Database
description: Maak een nieuwe data base in Azure SQL Database of Azure SQL Managed instance vanuit een BACPAC-bestand.
services: sql-database
ms.service: sql-db-mi
ms.subservice: migrate
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 06/20/2019
ms.openlocfilehash: 7bca179f3140a64923af71199fe4a1db48d2065c
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/06/2020
ms.locfileid: "85982334"
---
# <a name="quickstart-import-a-bacpac-file-to-a-database-in-azure-sql-database-or-azure-sql-managed-instance"></a>Quick Start: een BACPAC-bestand importeren in een data base in Azure SQL Database of een beheerd exemplaar van Azure SQL
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

U kunt een SQL Server-Data Base importeren in Azure SQL Database of een SQL-beheerd exemplaar met behulp van een [BACPAC](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) -bestand. U kunt de gegevens importeren uit een BACPAC-bestand dat is opgeslagen in Azure Blob-opslag (alleen standaardopslag), of vanuit een lokale opslag op een on-premises locatie. Als u de snelheid van het importeren wilt maximaliseren door meer en snellere resources te bieden, schaalt u de database tijdens het importproces naar een hogere servicelaag en een grotere rekenkracht. U kunt vervolgens omlaag schalen nadat het importeren is voltooid.

> [!NOTE]
> Het compatibiliteits niveau van de geïmporteerde data base is gebaseerd op het compatibiliteits niveau van de bron database.

> [!IMPORTANT]
> Nadat u de Data Base hebt geïmporteerd, kunt u ervoor kiezen om de data base te bewerken op het huidige compatibiliteits niveau (niveau 100 voor de AdventureWorks2008R2-data base) of op een hoger niveau. Zie [ALTER DATABASE Compatibility Level](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level) (Compatibiliteitsniveau ALTER DATABASE) voor meer informatie over de implicaties en opties bij het hanteren van een database op een bepaald compatibiliteitsniveau. Zie ook [ALTER DATABASE SCOPED CONFIGURATION](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) voor informatie over aanvullende instellingen op databaseniveau in verband met compatibiliteitsniveaus.

## <a name="using-azure-portal"></a>Azure Portal gebruiken

Bekijk deze video om te zien hoe u kunt importeren uit een BACPAC-bestand in de Azure Portal of door gaan met lezen:

> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Its-just-SQL-Restoring-a-database-to-Azure-SQL-DB-from-backup/player?WT.mc_id=dataexposed-c9-niner]

De [Azure Portal](https://portal.azure.com) biedt *alleen* ondersteuning voor het maken van één data base in Azure SQL database en *alleen* vanuit een BACPAC-bestand dat is opgeslagen in Azure Blob Storage.

Als u een Data Base wilt migreren naar een met [Azure SQL beheerd exemplaar](../managed-instance/sql-managed-instance-paas-overview.md) vanuit een BACPAC-bestand, gebruikt u SQL Server Management Studio of SQLPackage met behulp van de Azure Portal of Azure PowerShell momenteel niet wordt ondersteund.

> [!NOTE]
> Machines voor het verwerken van import/export-aanvragen die zijn ingediend via de Azure Portal of Power shell moeten het BACPAC-bestand opslaan, evenals tijdelijke bestanden die worden gegenereerd door het Framework van de Gegevenslaagtoepassing (DacFX). De benodigde schijf ruimte varieert aanzienlijk tussen data bases met dezelfde grootte en kan Maxi maal drie keer zoveel schijf ruimte vereisen als de data base. Machines met het verzoek om te importeren/exporteren hebben 450GB lokale schijf ruimte. Als gevolg hiervan kunnen sommige aanvragen mislukken met de fout `There is not enough space on the disk` . In dit geval is de tijdelijke oplossing om sqlpackage.exe uit te voeren op een computer met voldoende lokale schijf ruimte. We raden u aan SqlPackage-data bases die groter zijn dan 150 GB te importeren/exporteren om dit probleem te voor komen.

1. Als u vanuit een BACPAC-bestand wilt importeren in een nieuwe Data Base met behulp van de Azure Portal, opent u de juiste server pagina en selecteert u op de werk balk de optie **Data Base importeren**.  

   ![Data base-Import1](./media/database-import/sql-server-import-database.png)

1. Selecteer het opslag account en de container voor het BACPAC-bestand en selecteer vervolgens het BACPAC-bestand waaruit u wilt importeren.

1. Geef de grootte van de nieuwe Data Base op (meestal hetzelfde als die van de oorsprong) en geef de referenties voor de doel SQL Server op. Zie [Create Data Base](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current)(Engelstalig) voor een lijst met mogelijke waarden voor een nieuwe data base in Azure SQL database.

   ![Data base-import2](./media/database-import/sql-server-import-database-settings.png)

1. Klik op **OK**.

1. Als u de voortgang van een import bewerking wilt controleren, opent u de server pagina van de data base en selecteert u de **geschiedenis importeren/exporteren**onder **instellingen**. Wanneer het importeren is gelukt, is de status **voltooid** .

   ![Status van data base importeren](./media/database-import/sql-server-import-database-history.png)

1. Als u wilt controleren of de data base Live is op de server, selecteert u **SQL-data bases** en controleert u of de nieuwe Data Base **online**is.

## <a name="using-sqlpackage"></a>SqlPackage gebruiken

Zie [para meters en eigenschappen importeren](https://docs.microsoft.com/sql/tools/sqlpackage#import-parameters-and-properties)om een SQL Server-Data Base te importeren met behulp van het opdracht regel hulpprogramma [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage) . [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) en [SQL Server Data tools voor Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx) zijn SqlPackage. U kunt de meest recente [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876) ook downloaden via het micro soft Download centrum.

Voor schaal baarheid en prestaties raden we u aan SqlPackage te gebruiken in de meeste productie omgevingen, in plaats van de Azure Portal te gebruiken. `BACPAC`Zie [migreren van SQL Server naar Azure SQL database met behulp van BACPAC-bestanden](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/)voor een blog team van SQL Server klanten over de migratie met behulp van-bestanden.

Met de volgende SqlPackage-opdracht wordt de **AdventureWorks2008R2** -data base uit de lokale opslag geïmporteerd naar een logische SQL-Server met de naam **mynewserver20170403**. Er wordt een nieuwe data base gemaakt met de naam **myMigratedDatabase** met een **Premium** -servicelaag en een **P6** -service doelstelling. Wijzig deze waarden naar wens voor uw omgeving.

```cmd
sqlpackage.exe /a:import /tcs:"Data Source=<serverName>.database.windows.net;Initial Catalog=<migratedDatabase>;User Id=<userId>;Password=<password>" /sf:AdventureWorks2008R2.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
```

> [!IMPORTANT]
> Als u verbinding wilt maken met Azure SQL Database van achter een bedrijfs firewall, moet poort 1433 zijn geopend op de firewall. Als u verbinding wilt maken met een SQL-beheerd exemplaar, moet u een [punt-naar-site-verbinding](../managed-instance/point-to-site-p2s-configure.md) of een snelle route verbinding hebben.

In dit voor beeld ziet u hoe u een Data Base importeert met behulp van SqlPackage met Active Directory universele authenticatie.

```cmd
sqlpackage.exe /a:Import /sf:testExport.bacpac /tdn:NewDacFX /tsn:apptestserver.database.windows.net /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="using-powershell"></a>PowerShell gebruiken

> [!NOTE]
> [Een door SQL beheerd exemplaar](../managed-instance/sql-managed-instance-paas-overview.md) biedt momenteel geen ondersteuning voor het migreren van een Data Base naar een exemplaar database vanuit een BACPAC-bestand met behulp van Azure PowerShell. Als u wilt importeren in een SQL-beheerd exemplaar, gebruikt u SQL Server Management Studio of SQLPackage.

> [!NOTE]
> Op de machines voor het verwerken van import/export-aanvragen die zijn ingediend via de portal of Power shell moet het Bacpac-bestand worden opgeslagen, evenals tijdelijke bestanden die worden gegenereerd door Application Framework (DacFX). De benodigde schijf ruimte varieert aanzienlijk van Db's met dezelfde grootte en kan Maxi maal drie keer van de grootte van de data base duren. Machines met het verzoek om te importeren/exporteren hebben 450GB lokale schijf ruimte. Als gevolg hiervan kunnen sommige aanvragen mislukken met de fout ' er is onvoldoende ruimte op de schijf '. In dit geval is de tijdelijke oplossing om sqlpackage.exe uit te voeren op een computer met voldoende lokale schijf ruimte. Bij het importeren/exporteren van data bases die groter zijn dan 150 GB, gebruikt u SqlPackage om dit probleem te voor komen.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

> [!IMPORTANT]
> De module Power shell Azure Resource Manager (RM) wordt nog steeds ondersteund, maar alle toekomstige ontwikkeling is voor de module AZ. SQL. De AzureRM-module blijft oplossingen ontvangen tot ten minste december 2020.  De argumenten voor de opdrachten in de module AZ en in de AzureRm-modules zijn aanzienlijk identiek. Zie [Inleiding tot de nieuwe Azure PowerShell AZ-module](/powershell/azure/new-azureps-module-az)voor meer informatie over de compatibiliteit.

Gebruik de cmdlet [New-AzSqlDatabaseImport](/powershell/module/az.sql/new-azsqldatabaseimport) om een aanvraag voor het importeren van een Data Base naar Azure te verzenden. Afhankelijk van de grootte van de data base kan het enige tijd duren voordat de import bewerking is voltooid.

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

U kunt de cmdlet [Get-AzSqlDatabaseImportExportStatus](/powershell/module/az.sql/get-azsqldatabaseimportexportstatus) gebruiken om de voortgang van het importeren te controleren. Het uitvoeren van de cmdlet direct nadat de aanvraag wordt geretourneerd `Status: InProgress` . Het importeren is voltooid wanneer u ziet `Status: Succeeded` .

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

Gebruik de opdracht [AZ-SQL-DB-import](/cli/azure/sql/db#az-sql-db-import) om een aanvraag voor het importeren van een Data Base naar Azure te verzenden. Afhankelijk van de grootte van de data base kan het enige tijd duren voordat de import bewerking is voltooid.

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
> Zie [een Data Base importeren uit een BACPAC-bestand](scripts/import-from-bacpac-powershell.md)voor een ander voor beeld van een script.

## <a name="limitations"></a>Beperkingen

- Importeren naar een database in een elastische pool wordt niet ondersteund. U kunt gegevens importeren in één database en deze database vervolgens verplaatsen naar een elastische pool.
- Import export service werkt niet wanneer toegang tot Azure-Services toestaan is ingesteld op uit. U kunt het probleem echter omzeilen door sqlpackage.exe hand matig uit te voeren vanaf een Azure VM of door de export rechtstreeks in uw code uit te voeren met behulp van de DACFx-API.

## <a name="import-using-wizards"></a>Importeren met behulp van wizards

U kunt deze wizards ook gebruiken.

- [Wizard data-tier Application importeren in SQL Server Management Studio](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database#using-the-import-data-tier-application-wizard).
- [SQL Server wizard Importeren en exporteren](https://docs.microsoft.com/sql/integration-services/import-export-data/start-the-sql-server-import-and-export-wizard).

## <a name="next-steps"></a>Volgende stappen

- Voor informatie over het maken van verbinding met en het uitvoeren van een query op een data base in Azure SQL Database raadpleegt u [Quick Start: Azure SQL database: SQL Server Management Studio gebruiken om verbinding te maken met en gegevens](connect-query-ssms.md)op te vragen.
- Raadpleeg dit blogartikel van het SQL Server-klantadviesteam over migratie met behulp van BACPAC-bestanden: [Migrating from SQL Server to Azure SQL Database using BACPAC Files](https://techcommunity.microsoft.com/t5/DataCAT/Migrating-from-SQL-Server-to-Azure-SQL-Database-using-Bacpac/ba-p/305407) (Migreren van SQL Server naar Azure SQL Database met BACPAC-bestanden).
- Zie [SQL Server Data Base Migration to Azure SQL database](migrate-to-database-from-sql-server.md)voor een bespreking van het volledige SQL Server database migratie proces, met inbegrip van de aanbevelingen voor de prestaties.
- Zie [Azure Storage Security Guide (Engelstalig](https://docs.microsoft.com/azure/storage/common/storage-security-guide)) voor meer informatie over het veilig beheren en delen van opslag sleutels en gedeelde toegangs handtekeningen.
