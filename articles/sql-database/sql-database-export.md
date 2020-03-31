---
title: Een enkele of gepoolde database exporteren naar een BACPAC-bestand
description: Een Azure SQL-database exporteren naar een BACPAC-bestand met behulp van de Azure-portal
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 07/16/2019
ms.topic: conceptual
ms.openlocfilehash: 0bc72f0ad58829a3ff6545b5c4741ddc20916c31
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061635"
---
# <a name="export-an-azure-sql-database-to-a-bacpac-file"></a>Een Azure SQL-database exporteren naar een BACPAC-bestand

Wanneer u een database moet exporteren voor archivering of voor het verplaatsen naar een ander platform, u het databaseschema en de gegevens exporteren naar een [BACPAC-bestand.](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) Een BACPAC-bestand is een ZIP-bestand met een uitbreiding van BACPAC met de metagegevens en gegevens uit een SQL Server-database. Een BACPAC-bestand kan worden opgeslagen in Azure Blob-opslag of in lokale opslag op een on-premises locatie en later opnieuw worden geïmporteerd in Azure SQL Database of in een on-premises SQL Server-installatie.

## <a name="considerations-when-exporting-an-azure-sql-database"></a>Overwegingen bij het exporteren van een Azure SQL-database

- Als u wilt dat een export transactioneel consistent is, moet u ervoor zorgen dat er geen schrijfactiviteit optreedt tijdens de export of dat u exporteert vanuit een [transactioneel consistente kopie](sql-database-copy.md) van uw Azure SQL-database.
- Als u exporteert naar blobopslag, is de maximale grootte van een BACPAC-bestand 200 GB. Als u een groter BACPAC-bestand wilt archiveren, exporteert u naar lokale opslag.
- Het exporteren van een BACPAC-bestand naar Azure premium storage met behulp van de methoden die in dit artikel worden besproken, wordt niet ondersteund.
- Opslag achter een firewall wordt momenteel niet ondersteund.
- Als de exportbewerking van Azure SQL Database meer dan 20 uur bedraagt, kan deze worden geannuleerd. Als u de prestaties tijdens het exporteren wilt verhogen, u het als:

  - Verhoog tijdelijk uw rekengrootte.
  - Stop alle lees- en schrijfactiviteiten tijdens de export.
  - Gebruik een [geclusterde index](https://msdn.microsoft.com/library/ms190457.aspx) met niet-null-waarden op alle grote tabellen. Zonder geclusterde indexen kan een export mislukken als het langer duurt dan 6-12 uur. Dit komt omdat de exportservice een tabelscan moet voltooien om te proberen de hele tabel te exporteren. Een goede manier om te bepalen of uw tabellen zijn geoptimaliseerd voor export is het uitvoeren van **DBCC SHOW_STATISTICS** en ervoor te zorgen dat de *RANGE_HI_KEY* niet null is en de waarde ervan een goede verdeling heeft. Zie [DBCC SHOW_STATISTICS](https://msdn.microsoft.com/library/ms174384.aspx)voor meer informatie.

> [!NOTE]
> BACPACs zijn niet bedoeld om te worden gebruikt voor back-up- en herstelbewerkingen. Azure SQL Database maakt automatisch back-ups voor elke gebruikersdatabase. Zie voor meer informatie [het overzicht van bedrijfscontinuïteit](sql-database-business-continuity.md) en [SQL Database-back-ups](sql-database-automated-backups.md).

## <a name="export-to-a-bacpac-file-using-the-azure-portal"></a>Exporteren naar een BACPAC-bestand met behulp van de Azure-portal

Het exporteren van een BACPAC van een database vanuit een [beheerde instantie](sql-database-managed-instance.md) met Azure PowerShell wordt momenteel niet ondersteund. Gebruik in plaats daarvan SQL Server Management Studio of SQLPackage.

> [!NOTE]
> Machines die import-/exportaanvragen verwerken die via de Azure-portal of PowerShell worden ingediend, moeten het BACPAC-bestand en tijdelijke bestanden die worden gegenereerd door het Data-Tier Application Framework (DacFX) opslaan. De vereiste schijfruimte varieert aanzienlijk tussen databases met dezelfde grootte en kan schijfruimte tot 3 keer de grootte van de database vereisen. Machines waarop de import/exportaanvraag wordt uitgevoerd, hebben slechts 450 GB lokale schijfruimte. Als gevolg hiervan kunnen sommige aanvragen `There is not enough space on the disk`mislukken met de fout . In dit geval moet sqlpackage.exe op een machine met voldoende lokale schijfruimte worden uitgevoerd. We moedigen het gebruik van [SqlPackage](#export-to-a-bacpac-file-using-the-sqlpackage-utility) aan om databases van meer dan 150 GB te importeren/exporteren om dit probleem te voorkomen.

1. Als u een database wilt exporteren met de [Azure-portal,](https://portal.azure.com)opent u de pagina voor uw database en klikt u op **Exporteren** op de werkbalk.

   ![Database-export](./media/sql-database-export/database-export1.png)

2. Geef de BACPAC-bestandsnaam op, selecteer een bestaand Azure-opslagaccount en een bestaande container voor de export en geef vervolgens de juiste referenties op voor toegang tot de brondatabase. Een **SQL Server-beheerlogin** is hier nodig, zelfs als u de Azure-beheerder bent, omdat het zijn van een Azure-beheerder niet gelijk staat aan sql server-beheerdersmachtigingen.

    ![Database-export](./media/sql-database-export/database-export2.png)

3. Klik op **OK**.

4. Als u de voortgang van de exportbewerking wilt controleren, opent u de pagina voor de SQL Database-server die de database bevat die wordt geëxporteerd. Klik onder naar **Instellingen** en klik vervolgens op **Geschiedenis importeren/exporteren**.

   ![exportgeschiedenis](./media/sql-database-export/export-history.png)

## <a name="export-to-a-bacpac-file-using-the-sqlpackage-utility"></a>Exporteren naar een BACPAC-bestand met het SQLPackage-hulpprogramma

Zie [Parameters en eigenschappen exporteren](https://docs.microsoft.com/sql/tools/sqlpackage#export-parameters-and-properties)als u een SQL-database wilt exporteren met het sqlpackage-opdrachtregelhulpprogramma. [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage) Het SQLPackage-hulpprogramma wordt geleverd met de nieuwste versies van [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) en SQL Server Data Tools voor Visual [Studio,](https://msdn.microsoft.com/library/mt204009.aspx)of u de nieuwste versie van [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876) rechtstreeks downloaden vanuit het Microsoft-downloadcentrum.

We raden het gebruik van het SQLPackage-hulpprogramma aan voor schaal en prestaties in de meeste productieomgevingen. Raadpleeg dit blogartikel van het SQL Server-klantadviesteam over migratie met behulp van BACPAC-bestanden: [Migrating from SQL Server to Azure SQL Database using BACPAC Files](https://blogs.msdn.microsoft.com/sqlcat/20../../migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/) (Migreren van SQL Server naar Azure SQL Database met BACPAC-bestanden).

In dit voorbeeld ziet u hoe u een database exporteert met SqlPackage.exe met Active Directory Universal Authentication:

```cmd
SqlPackage.exe /a:Export /tf:testExport.bacpac /scs:"Data Source=apptestserver.database.windows.net;Initial Catalog=MyDB;" /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="export-to-a-bacpac-file-using-sql-server-management-studio-ssms"></a>Exporteren naar een BACPAC-bestand met SQL Server Management Studio (SSMS)

De nieuwste versies van SQL Server Management Studio bieden een wizard om een Azure SQL-database te exporteren naar een BACPAC-bestand. Zie de [toepassing Een gegevenslaag exporteren](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application).

## <a name="export-to-a-bacpac-file-using-powershell"></a>Exporteren naar een BACPAC-bestand met PowerShell

> [!NOTE]
> [Een beheerde instantie](sql-database-managed-instance.md) ondersteunt momenteel geen exporteren van een database naar een BACPAC-bestand met Azure PowerShell. Als u een beheerde instantie wilt exporteren naar een BACPAC-bestand, gebruikt u SQL Server Management Studio of SQLPackage.

Gebruik de cmdlet [Nieuw-AzSqlDatabaseExport](/powershell/module/az.sql/new-azsqldatabaseexport) om een exportdatabaseaanvraag in te dienen bij de Azure SQL Database-service. Afhankelijk van de grootte van uw database kan het enige tijd duren voordat de exportbewerking is voltooid.

```powershell
$exportRequest = New-AzSqlDatabaseExport -ResourceGroupName $ResourceGroupName -ServerName $ServerName `
  -DatabaseName $DatabaseName -StorageKeytype $StorageKeytype -StorageKey $StorageKey -StorageUri $BacpacUri `
  -AdministratorLogin $creds.UserName -AdministratorLoginPassword $creds.Password
```

Als u de status van de exportaanvraag wilt controleren, gebruikt u de cmdlet [Get-AzSqlDatabaseImportExportStatus.](/powershell/module/az.sql/get-azsqldatabaseimportexportstatus) Als u dit onmiddellijk uitvoert nadat de aanvraag de status meestal retourneert: **InProgress**. Wanneer u **Status: Geslaagd ziet,** is de export voltooid.

```powershell
$exportStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink
[Console]::Write("Exporting")
while ($exportStatus.Status -eq "InProgress")
{
    Start-Sleep -s 10
    $exportStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink
    [Console]::Write(".")
}
[Console]::WriteLine("")
$exportStatus
```

## <a name="next-steps"></a>Volgende stappen

- Zie [Langetermijnback-upretentie](sql-database-long-term-retention.md)voor meer informatie over het behoud van back-ups op lange termijn van één database en samengevoegde databases als alternatief voor het exporteren van een database voor archiefdoeleinden. U SQL Agent-taken gebruiken om [alleen-kopiëren-databaseback-ups](https://docs.microsoft.com/sql/relational-databases/backup-restore/copy-only-backups-sql-server) te plannen als alternatief voor het bewaren van back-ups op lange termijn.
- Raadpleeg dit blogartikel van het SQL Server-klantadviesteam over migratie met behulp van BACPAC-bestanden: [Migrating from SQL Server to Azure SQL Database using BACPAC Files](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/) (Migreren van SQL Server naar Azure SQL Database met BACPAC-bestanden).
- Zie [Een BACPAC importeren in een SQL Server-database voor](https://msdn.microsoft.com/library/hh710052.aspx)meer informatie over het importeren van een BACPAC naar een SQL Server-database.
- Zie Een toepassing op gegevenslagen exporteren voor meer informatie over het exporteren van een BACPAC uit een SQL [Server-database](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application)
- Zie SQL Server migreren naar Azure SQL [Database offline met DMS](../dms/tutorial-sql-server-to-azure-sql.md)voor meer informatie over het gebruik van de Gegevensmigratieservice om een database te migreren.
- Zie [Een SQL Server-database](sql-database-single-database-migrate.md)migreren naar Azure SQL Database als een voorbode van migratie naar Azure SQL Database.
- Zie [Azure Storage Security Guide](https://docs.microsoft.com/azure/storage/common/storage-security-guide)voor meer informatie over het veilig beheren en delen van opslagsleutels en gedeelde toegangshandtekeningen.
