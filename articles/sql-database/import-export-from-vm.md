---
title: Een SQL-database importeren of exporteren
description: Een Azure SQL-database importeren of exporteren zonder Azure-services toegang te geven tot de server.
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/08/2020
ms.openlocfilehash: 9f694f3f0ec740d0a4e8dc4e6bf8845c408802c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75897843"
---
# <a name="import-or-export-an-azure-sql-database-without-allowing-azure-services-to-access-the-server"></a>Een Azure SQL-database importeren of exporteren zonder Azure-services toegang te geven tot de server

In dit artikel ziet u hoe u een Azure SQL-database importeert of exporteert wanneer *Azure Services* toestaan is ingesteld op *UIT* op de Azure SQL-server. De werkstroom gebruikt een virtuele Azure-machine om SqlPackage uit te voeren om de import- of exportbewerking uit te voeren.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij [Azure Portal](https://portal.azure.com/).

## <a name="create-the-azure-virtual-machine"></a>De virtuele Azure-machine maken

Maak een virtuele Azure-machine door de knop **Implementeren naar Azure te** selecteren.

Met deze sjabloon u een eenvoudige virtuele Windows-machine implementeren met behulp van een paar verschillende opties voor de Windows-versie, met behulp van de nieuwste gepatchte versie. Hiermee wordt een A2-formaat VM geïmplementeerd in de locatie van de resourcegroep en wordt de volledig gekwalificeerde domeinnaam van de VM teruggegeven.
<br><br>

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-vm-simple-windows%2Fazuredeploy.json" target="_blank">
    <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/>
</a>

Zie [Zeer eenvoudige implementatie van een Windows VM](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows)voor meer informatie.


## <a name="connect-to-the-virtual-machine"></a>Verbinding maken met de virtuele machine

In de volgende stappen ziet u hoe u verbinding maakt met uw virtuele machine via een verbinding met extern bureaublad.

1. Nadat de implementatie is voltooid, gaat u naar de virtuele machine-resource.

    ![VM](./media/import-export-from-vm/vm.png)  

2. Selecteer **Verbinden**.

   Er wordt een extern bureaublad-protocolbestandformulier (.rdp-bestand) weergegeven met het openbare IP-adres en het poortnummer voor de virtuele machine.

   ![RDP-formulier](./media/import-export-from-vm/rdp.png)  

3. Selecteer **RDP-bestand downloaden**.

   > [!NOTE]
   > U SSH ook gebruiken om verbinding te maken met uw VM.

4. Sluit het **formulier Verbinding met virtuele machine.**
5. Open het gedownloade RDP-bestand om verbinding met de VM te maken.
6. Selecteer **Verbinding maken**wanneer u daarom wordt gevraagd. Op een Mac hebt u een RDP-client nodig, zoals deze [Extern-bureaubladclient](https://itunes.apple.com/us/app/microsoft-remote-desktop/id715768417?mt=12) uit de Mac App Store.

7. Voer de gebruikersnaam en het wachtwoord in die u hebt opgegeven bij het maken van de virtuele machine en kies **OK**.

8. Er wordt mogelijk een certificaatwaarschuwing weergegeven tijdens het aanmelden. Kies **Ja** of **Doorgaan** om door te gaan met de verbinding.



## <a name="install-sqlpackage"></a>SqlPackage installeren

[Download en installeer de nieuwste versie van SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage-download).




Zie [SqlPackage.exe](https://docs.microsoft.com/sql/tools/sqlpackage)voor meer informatie.

## <a name="create-a-firewall-rule-to-allow-the-vm-access-to-the-database"></a>Een firewallregel maken om de VM toegang tot de database toe te staan

Voeg het openbare IP-adres van de virtuele machine toe aan de SQL Database-serverfirewall.

De volgende stappen maken een IP-firewallregel op serverniveau voor het openbare IP-adres van uw virtuele machine en maken connectiviteit vanaf de virtuele machine mogelijk.

1. Selecteer **SQL-databases** in het linkermenu en selecteer vervolgens uw database op de pagina **SQL-databases.** De overzichtspagina voor uw database wordt geopend, waarin u de volledig gekwalificeerde servernaam (zoals **servername.database.windows.net)** toont en opties biedt voor verdere configuratie.

2. Kopieer deze volledig gekwalificeerde servernaam om te gebruiken wanneer u verbinding maakt met uw server en de databases.

   ![servernaam](./media/sql-database-get-started-portal/server-name.png)

3. Selecteer **Serverfirewall instellen** op de werkbalk. De pagina **Firewallinstellingen** voor de databaseserver wordt geopend.

   ![IP-firewallregel op serverniveau](./media/sql-database-get-started-portal/server-firewall-rule.png)

4. Kies **Client-IP toevoegen** op de werkbalk om het openbare IP-adres van uw virtuele machine toe te voegen aan een nieuwe IP-firewallregel op serverniveau. Een IP-firewallregel op serverniveau kan poort 1433 openen voor een individueel IP-adres of voor een bereik van IP-adressen.

5. Selecteer **Opslaan**. Er wordt een IP-firewallregel op serverniveau gemaakt voor de openbare IP-adresopeningpoort 1433 van uw virtuele machine op de SQL Database-server.

6. Sluit de pagina **Firewallinstellingen**.



## <a name="export-a-database-using-sqlpackage"></a>Een database exporteren met SqlPackage

Zie [Parameters en eigenschappen exporteren](https://docs.microsoft.com/sql/tools/sqlpackage#export-parameters-and-properties)als u een SQL-database wilt exporteren met het sqlpackage-opdrachtregelhulpprogramma. [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage) Het SqlPackage-hulpprogramma wordt geleverd met de nieuwste versies van [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) en SQL Server Data [Tools,](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt)of u de nieuwste versie van [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage-download)downloaden.

We raden het gebruik van het SqlPackage-hulpprogramma aan voor schaal en prestaties in de meeste productieomgevingen. Raadpleeg dit blogartikel van het SQL Server-klantadviesteam over migratie met behulp van BACPAC-bestanden: [Migrating from SQL Server to Azure SQL Database using BACPAC Files](https://blogs.msdn.microsoft.com/sqlcat/20../../migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/) (Migreren van SQL Server naar Azure SQL Database met BACPAC-bestanden).

In dit voorbeeld ziet u hoe u een database exporteert met SqlPackage.exe met Active Directory Universal Authentication. Vervang door waarden die specifiek zijn voor uw omgeving.

```cmd
SqlPackage.exe /a:Export /tf:testExport.bacpac /scs:"Data Source=<servername>.database.windows.net;Initial Catalog=MyDB;" /ua:True /tid:"apptest.onmicrosoft.com"
```




## <a name="import-a-database-using-sqlpackage"></a>Een database importeren met SqlPackage

Zie [Parameters en eigenschappen importeren](https://docs.microsoft.com/sql/tools/sqlpackage#import-parameters-and-properties)als u een SQL Server-database wilt importeren met het sqlpackage-opdrachtregelhulpprogramma. [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage) SqlPackage heeft de nieuwste [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) en SQL Server Data [Tools.](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt) U ook de nieuwste versie van [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage-download)downloaden.

Voor schaal en prestaties raden we aan sqlpackage te gebruiken in de meeste productieomgevingen in plaats van de Azure-portal te gebruiken. Zie Migreren van SQL Server naar `BACPAC` Azure SQL Database [met BACPAC-bestanden](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/)voor een SQL Server Customer Advisory Team-blog over het migreren met bestanden.

Met de volgende opdracht SqlPackage importeert u de **AdventureWorks2017-database** uit lokale opslag naar een Azure SQL Database-server. Het creëert een nieuwe database genaamd **myMigratedDatabase** met een **Premium-servicelaag** en een **P6-servicedoelstelling.** Wijzig deze waarden naar gelang van het geval voor uw omgeving.

```cmd
sqlpackage.exe /a:import /tcs:"Data Source=<serverName>.database.windows.net;Initial Catalog=myMigratedDatabase>;User Id=<userId>;Password=<password>" /sf:AdventureWorks2017.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
```

> [!IMPORTANT]
> Als u verbinding wilt maken met een SQL Database-server die één database beheert van achter een bedrijfsfirewall, moet de firewall poort 1433 hebben geopend. Als u verbinding wilt maken met een beheerde instantie, moet u een [point-to-site-verbinding](sql-database-managed-instance-configure-p2s.md) of een snelrouteverbinding hebben.

In dit voorbeeld ziet u hoe u een database importeert met SqlPackage met Active Directory Universal Authentication.

```cmd
sqlpackage.exe /a:Import /sf:testExport.bacpac /tdn:NewDacFX /tsn:apptestserver.database.windows.net /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="performance-considerations"></a>Prestatieoverwegingen

Exportsnelheden variëren als gevolg van vele factoren (bijvoorbeeld gegevensvorm), dus het is onmogelijk om te voorspellen welke snelheid moet worden verwacht. SqlPackage kan veel tijd in beslag nemen, vooral voor grote databases.

Om de beste prestaties te krijgen, u de volgende strategieën proberen:

1. Zorg ervoor dat er geen andere werkbelasting wordt uitgevoerd in de database. Een kopie maken voordat exporteren mogelijk de beste oplossing is om ervoor te zorgen dat er geen andere workloads worden uitgevoerd.
2. Verhoog de doelstelling op databaseserviceniveau (SLO) om de exportworkload beter te kunnen verwerken (lees voornamelijk I/O). Als de database momenteel GP_Gen5_4 is, zou een bedrijfskritieke laag misschien helpen bij het lezen van werkbelasting.
3. Zorg ervoor dat er geclusterde indexen zijn, met name voor grote tabellen. 
4. Virtuele machines (VM's) moeten zich in dezelfde regio bevinden als de database om netwerkbeperkingen te voorkomen.
5. VM's moeten SSD hebben met voldoende grootte voor het genereren van tijdelijke artefacten voordat ze worden geüpload naar blob-opslag.
6. VM's moeten voldoende kern- en geheugenconfiguratie hebben voor de specifieke database.

## <a name="store-the-imported-or-exported-bacpac-file"></a>Sla de geïmporteerde of geëxporteerde . BACPAC-bestand

De. BACPAC-bestand kan worden opgeslagen in [Azure Blobs](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview)of [Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction). 

Gebruik Azure Files om de beste prestaties te bereiken. SqlPackage werkt met het bestandssysteem, zodat het rechtstreeks toegang heeft tot Azure Files.

Gebruik Azure Blobs, die minder kosten dan een premium Azure-bestandsshare, om de kosten te verlagen. Echter, het zal vereisen dat u de [. BACPAC-bestand](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) tussen de blob en het lokale bestandssysteem vóór de import- of exportbewerking. Hierdoor duurt het proces langer.

Upload en download . BACPAC-bestanden, zie [Gegevens overbrengen met AzCopy- en Blob-opslag](../storage/common/storage-use-azcopy-blobs.md)en [Gegevens overbrengen met AzCopy en bestandsopslag](../storage/common/storage-use-azcopy-files.md).

Afhankelijk van uw omgeving moet u mogelijk [Azure Storage-firewalls en virtuele netwerken configureren.](../storage/common/storage-network-security.md)

## <a name="next-steps"></a>Volgende stappen

- Zie [Quickstart: Azure SQL Database: SQL Server Management Studio gebruiken om](sql-database-connect-query-ssms.md)verbinding te maken en querygegevens op te vragen voor meer informatie over het maken van verbinding met een geïmporteerde SQL-database.
- Raadpleeg dit blogartikel van het SQL Server-klantadviesteam over migratie met behulp van BACPAC-bestanden: [Migrating from SQL Server to Azure SQL Database using BACPAC Files](https://techcommunity.microsoft.com/t5/DataCAT/Migrating-from-SQL-Server-to-Azure-SQL-Database-using-Bacpac/ba-p/305407) (Migreren van SQL Server naar Azure SQL Database met BACPAC-bestanden).
- Zie [SQL Server-databasemigratie naar Azure SQL Database](sql-database-single-database-migrate.md)voor een discussie over het volledige SQL Server-databasemigratieproces, inclusief prestatieaanbevelingen.
- Zie [Azure Storage Security Guide](https://docs.microsoft.com/azure/storage/common/storage-security-guide)voor meer informatie over het veilig beheren en delen van opslagsleutels en gedeelde toegangshandtekeningen.
