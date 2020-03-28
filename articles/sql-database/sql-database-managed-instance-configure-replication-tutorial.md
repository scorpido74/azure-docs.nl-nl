---
title: Transactionele replicatie configureren tussen twee beheerde instanties en SQL Server
description: Een zelfstudie die replicatie configureert tussen een door publisher beheerde instantie, een door de distributeur beheerde instantie en een SQL Server-abonnee op een Azure VM, samen met de nodige netwerkcomponenten zoals private DNS-zone en VPN-peering.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: tutorial
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 11/21/2019
ms.openlocfilehash: fa6e393500e9deeb91ee84aa5255320003817f08
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76719888"
---
# <a name="tutorial-configure-transactional-replication-between-two-managed-instances-and-sql-server"></a>Zelfstudie: Transactionele replicatie configureren tussen twee beheerde instanties en SQL Server


In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> - Een beheerde instantie configureren als replicatie-uitgever. 
> - Een beheerde instantie configureren als replicatiedistributeur. 
> - Een SQL Server configureren als abonnee. 

![Replicatie tussen een SQL MI-pub, SQL MI Dist en een SQL Server-sub](media/sql-database-managed-instance-failover-group-tutorial/sqlmi-to-sql-replication.png)

Deze zelfstudie is bedoeld voor een ervaren doelgroep en gaat ervan uit dat de gebruiker bekend is met het implementeren en verbinden met zowel beheerde exemplaren als SQL Server VM's binnen Azure. Als zodanig worden bepaalde stappen in deze zelfstudie overbloemd. 

Zie voor meer informatie het [overzicht van de beheerde instantie](sql-database-managed-instance-index.yml)van Azure SQL Database, de artikelen over [sql](sql-database-managed-instance.md)transactionele replicatie en [SQL Transactional Replication.](sql-database-managed-instance-transactional-replication.md)

Zie [Transactionele replicatie configureren tussen twee beheerde instanties](replication-with-sql-database-managed-instance.md)als u replicatie wilt configureren tussen een beheerde instantieuitgever en een beheerde instantieabonnee. 

## <a name="prerequisites"></a>Vereisten

Zorg dat u over het volgende beschikt als u de zelfstudie wilt uitvoeren:

- Een [Azure-abonnement](https://azure.microsoft.com/free/). 
- Ervaring met het implementeren van twee beheerde exemplaren binnen hetzelfde virtuele netwerk. 
- Een SQL Server-abonnee, on-premises of een Azure VM. Deze zelfstudie maakt gebruik van een Azure VM.  
- [SQL Server Management Studio (SSMS) 18.0 of hoger](/sql/ssms/download-sql-server-management-studio-ssms).
- De nieuwste versie van [Azure Powershell](/powershell/azure/install-az-ps?view=azps-1.7.0).
- Poort 445 en 1433 staan SQL-verkeer toe op zowel de Azure Firewall als de Windows Firewall. 

## <a name="1---create-the-resource-group"></a>1 - De resourcegroep maken
Gebruik het volgende PowerShell-codefragment om een nieuwe brongroep te maken:

```powershell-interactive
# set variables
$ResourceGroupName = "SQLMI-Repl"
$Location = "East US 2"

# Create a new resource group
New-AzResourceGroup -Name  $ResourceGroupName -Location $Location
```

## <a name="2---create-two-managed-instances"></a>2 - Twee beheerde exemplaren maken
Maak twee beheerde exemplaren binnen deze nieuwe brongroep met behulp van de [Azure-portal.](https://portal.azure.com) 

- De naam van de door `sql-mi-publisher` de uitgever beheerde instantie moet zijn: (samen met `vnet-sql-mi-publisher`een paar tekens voor randomisatie) en de naam van het virtuele netwerk moet zijn .
- De naam van de distributeur `sql-mi-distributor` beheerde instantie moet zijn: (samen met een paar tekens voor randomisatie) en het moet _in hetzelfde virtuele netwerk als de uitgever beheerde instantie_.

   ![Gebruik de uitgever vnet voor de distributeur](media/sql-database-managed-instance-configure-replication-tutorial/use-same-vnet-for-distributor.png)

Zie [Een beheerde instantie maken in de portal](sql-database-managed-instance-get-started.md) voor meer informatie over het maken van een beheerde instantie

  > [!NOTE]
  > Omwille van de eenvoud, en omdat het de meest voorkomende configuratie, deze tutorial suggereert het plaatsen van de distributeur beheerd exemplaar binnen hetzelfde virtuele netwerk als de uitgever. Het is echter mogelijk om de distributeur te maken in een apart virtueel netwerk. Om dit te doen, moet u VPN-peering configureren tussen de virtuele netwerken van de uitgever en distributeur en vpn-peering configureren tussen de virtuele netwerken van de distributeur en abonnee. 

## <a name="3---create-a-sql-server-vm"></a>3 - Een SQL Server VM maken
Maak een virtuele SQL Server-machine met behulp van de [Azure-portal.](https://portal.azure.com) De virtuele SQL Server-machine moet de volgende kenmerken hebben:

- Naam:`sql-vm-sub`
- Afbeelding: SQL Server 2016 of hoger
- Resourcegroep: hetzelfde als de beheerde instantie
- Virtueel netwerk:`sql-vm-sub-vnet` 

Zie [Quickstart: SQL Server VM maken](../virtual-machines/windows/sql/quickstart-sql-vm-create-portal.md)voor meer informatie over het implementeren van een SQL Server VM naar Azure.

## <a name="4---configure-vpn-peering"></a>4 - VPN-peering configureren
Configureer VPN-peering om communicatie tussen het virtuele netwerk van de twee beheerde instanties en het virtuele netwerk van SQL Server mogelijk te maken. Gebruik hiervoor dit PowerShell-codefragment:

```powershell-interactive
# Set variables
$SubscriptionId = '<SubscriptionID>'
$resourceGroup = 'SQLMI-Repl'
$pubvNet = 'sql-mi-publisher-vnet'
$subvNet = 'sql-vm-sub-vnet'
$pubsubName = 'Pub-to-Sub-Peer'
$subpubName = 'Sub-to-Pub-Peer'

$virtualNetwork1 = Get-AzVirtualNetwork `
  -ResourceGroupName $resourceGroup `
  -Name $pubvNet 

 $virtualNetwork2 = Get-AzVirtualNetwork `
  -ResourceGroupName $resourceGroup `
  -Name $subvNet  

# Configure VPN peering from publisher to subscriber
Add-AzVirtualNetworkPeering `
  -Name $pubsubName `
  -VirtualNetwork $virtualNetwork1 `
  -RemoteVirtualNetworkId $virtualNetwork2.Id

# Configure VPN peering from subscriber to publisher
Add-AzVirtualNetworkPeering `
  -Name $subpubName `
  -VirtualNetwork $virtualNetwork2 `
  -RemoteVirtualNetworkId $virtualNetwork1.Id

# Check status of peering on the publisher vNet; should say connected
Get-AzVirtualNetworkPeering `
 -ResourceGroupName $resourceGroup `
 -VirtualNetworkName $pubvNet `
 | Select PeeringState

# Check status of peering on the subscriber vNet; should say connected
Get-AzVirtualNetworkPeering `
 -ResourceGroupName $resourceGroup `
 -VirtualNetworkName $subvNet `
 | Select PeeringState

```

Zodra VPN-peering is ingesteld, test u de connectiviteit door SQL Server Management Studio (SSMS) op uw SQL Server te starten en verbinding te maken met beide beheerde exemplaren. Zie [SSMS gebruiken om verbinding te maken met de MI voor](sql-database-managed-instance-configure-p2s.md#use-ssms-to-connect-to-the-managed-instance)meer informatie over het maken van verbinding met een beheerde instantie met SSMS. 

![Connectiviteit met de beheerde instanties testen](media/sql-database-managed-instance-configure-replication-tutorial/test-connectivity-to-mi.png)

## <a name="5---create-private-dns-zone"></a>5 - Privé-DNS-zone maken

Een privé-DNS-zone maakt DNS-routering mogelijk tussen de beheerde instanties en de SQL Server. 

### <a name="create-private-dns-zone"></a>Privé-DNS-zone maken
1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer **Een resource maken** om een nieuwe Azure-bron te maken. 
1. Zoeken `private dns zone` op Azure Marketplace. 
1. Kies de door Microsoft gepubliceerde **privé-DNS-zonebron** en selecteer **Maken** om de DNS-zone te maken. 
1. Kies de abonnements- en resourcegroep in de vervolgkeuzelijst. 
1. Geef een willekeurige naam op voor `repldns.com`uw DNS-zone, zoals . 

   ![Privé-DNS-zone maken](media/sql-database-managed-instance-configure-replication-tutorial/create-private-dns-zone.png)

1. Selecteer **Controleren + maken**. Controleer de parameters voor uw privé-DNS-zone en selecteer **Vervolgens Maken** om uw bron te maken. 

### <a name="create-a-record"></a>Een record maken

1. Ga naar uw nieuwe **Private DNS-zone** en selecteer **Overzicht**. 
1. Selecteer **+ Record instellen** om een nieuwe A-Record te maken. 
1. Geef de naam van uw SQL Server VM en het privé interne IP-adres op. 

   ![Een record configureren](media/sql-database-managed-instance-configure-replication-tutorial/configure-a-record.png)

1. Selecteer **OK** om de A-record te maken. 

### <a name="link-the-virtual-network"></a>Het virtuele netwerk koppelen

1. Ga naar uw nieuwe **private DNS-zone** en selecteer **Virtuele netwerkkoppelingen**. 
1. Selecteer **+ Toevoegen**. 
1. Geef een naam op voor `Pub-link`de koppeling, zoals . 
1. Selecteer uw abonnement in de vervolgkeuzelijst en selecteer vervolgens het virtuele netwerk voor het beheerde exemplaar van uw uitgever. 
1. Schakel het selectievakje in naast **Automatisch registreren inschakelen**. 

   ![Vnet-koppeling maken](media/sql-database-managed-instance-configure-replication-tutorial/configure-vnet-link.png)

1. Selecteer **OK** om uw virtuele netwerk te koppelen. 
1. Herhaal deze stappen om een koppeling toe te voegen `Sub-link`voor het virtuele netwerk van de abonnee, met een naam zoals . 


## <a name="6---create-azure-storage-account"></a>6 - Azure Storage-account maken

[Maak een Azure Storage-account](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account#create-a-storage-account) voor de werkmap en maak vervolgens een [bestandsshare](../storage/files/storage-how-to-create-file-share.md) in het opslagaccount. 

Kopieer het pad voor bestandsshare in de indeling van:`\\storage-account-name.file.core.windows.net\file-share-name`   

Voorbeeld: `\\replstorage.file.core.windows.net\replshare`

Kopieer de verbindingstekenreeks voor opslagtoegang in de indeling:`DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net`   

Voorbeeld: `DefaultEndpointsProtocol=https;AccountName=replstorage;AccountKey=dYT5hHZVu9aTgIteGfpYE64cfis0mpKTmmc8+EP53GxuRg6TCwe5eTYWrQM4AmQSG5lb3OBskhg==;EndpointSuffix=core.windows.net`


Zie [Toegangssleutels voor opslagaccount beheren](../storage/common/storage-account-keys-manage.md)voor meer informatie . 


## <a name="7---create-a-database"></a>7 - Een database maken
Maak een nieuwe database op de uitgever MI. Hiervoor volgt u de volgende stappen:

1. Start SQL Server Management Studio (SSMS) op uw SQL Server. 
1. Verbinding maken `sql-mi-publisher` met de beheerde instantie. 
1. Open een **nieuw queryvenster** en voer de volgende T-SQL-query uit om de database te maken:

```sql
-- Create the databases
USE [master]
GO

-- Drop database if it exists
IF EXISTS (SELECT * FROM sys.sysdatabases WHERE name = 'ReplTutorial')
BEGIN
    DROP DATABASE ReplTutorial
END
GO

-- Create new database
CREATE DATABASE [ReplTutorial]
GO

-- Create table
USE [ReplTutorial]
GO
CREATE TABLE ReplTest (
    ID INT NOT NULL PRIMARY KEY,
    c1 VARCHAR(100) NOT NULL,
    dt1 DATETIME NOT NULL DEFAULT getdate()
)
GO

-- Populate table with data
USE [ReplTutorial]
GO

INSERT INTO ReplTest (ID, c1) VALUES (6, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (2, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (3, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (4, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (5, 'pub')
GO
SELECT * FROM ReplTest
GO
```

## <a name="8---configure-distribution"></a>8 - Distributie configureren 
Zodra de connectiviteit is ingesteld en u een voorbeelddatabase `sql-mi-distributor` hebt, u de distributie configureren op uw beheerde instantie. Hiervoor volgt u de volgende stappen:

1. Start SQL Server Management Studio (SSMS) op uw SQL Server. 
1. Verbinding maken `sql-mi-distributor` met de beheerde instantie. 
1. Open een **nieuw queryvenster** en voer de volgende Transact-SQL-code uit om de distributie op de door de distributeur beheerde instantie te configureren: 

   ```sql
   EXEC sp_adddistpublisher @publisher = 'sql-mi-publisher.b6bf57.database.windows.net', -- primary publisher
        @distribution_db = N'distribution',
        @security_mode = 0,
        @login = N'azureuser',
        @password = N'<publisher_password>',
        @working_directory = N'\\replstorage.file.core.windows.net\replshare',
        @storage_connection_string = N'<storage_connection_string>'
        -- example: @storage_connection_string = N'DefaultEndpointsProtocol=https;AccountName=replstorage;AccountKey=dYT5hHZVu9aTgIteGfpYE64cfis0mpKTmmc8+EP53GxuRg6TCwe5eTYWrQM4AmQSG5lb3OBskhg==;EndpointSuffix=core.windows.net'

   ```

   > [!NOTE]
   > Zorg ervoor dat u alleen`\`backslashes ( ) voor de @working_directory parameter gebruikt. Het gebruik van`/`een voorwaartse slash ( ) kan een fout veroorzaken bij het maken van verbinding met de bestandsshare. 

1. Verbinding maken `sql-mi-publisher` met de beheerde instantie. 
1. Open een **nieuw queryvenster** en voer de volgende Transact-SQL-code uit om de distributeur bij de uitgever te registreren: 

```sql
Use MASTER
EXEC sys.sp_adddistributor @distributor = 'sql-mi-distributor.b6bf57.database.windows.net', @password = '<distributor_admin_password>' 
```


## <a name="9---create-the-publication"></a>9 - Maak de publicatie
Zodra de distributie is geconfigureerd, u de publicatie nu maken. Hiervoor volgt u de volgende stappen: 

1. Start SQL Server Management Studio (SSMS) op uw SQL Server. 
1. Verbinding maken `sql-mi-publisher` met de beheerde instantie. 
1. Vouw in **Object Explorer**het **replicatieknooppunt** uit en klik met de rechtermuisknop op de map **Lokale publicatie.** Selecteer **Nieuwe publicatie...**. 
1. Selecteer **Volgende** om verder te gaan dan de welkomstpagina. 
1. Selecteer **op** de pagina `ReplTutorial` Publicatiedatabase de database die u eerder hebt gemaakt. Selecteer **Volgende**. 
1. Selecteer op de pagina **Publicatietype** de optie **Transactionele publicatie**. Selecteer **Volgende**. 
1. Schakel **op de** pagina Artikelen het selectievakje naast **Tabellen**in. Selecteer **Volgende**. 
1. Selecteer op de pagina **Tabelrijen filteren** de optie **Volgende** zonder filters toe te voegen. 
1. Schakel op de pagina **Momentopnameagent** het selectievakje naast Momentopname maken onmiddellijk in **en houd de momentopname beschikbaar om abonnementen te initialiseren**. Selecteer **Volgende**. 
1. Selecteer op de pagina **Agentbeveiliging** de optie **Beveiligingsinstellingen..**. Geef SQL Server-inloggegevens op om te gebruiken voor de snapshot-agent en om verbinding te maken met de Publisher. Selecteer **OK** om de pagina **Momentopnameagentbeveiliging te** sluiten. Selecteer **Volgende**. 

   ![Beveiliging van momentopnameagent configureren](media/sql-database-managed-instance-configure-replication-tutorial/snapshot-agent-security.png)

1. Kies op de pagina **Wizard Acties** de **publicatie maken** en kies (optioneel) voor Het genereren van **een scriptbestand met stappen om de publicatie te maken** als u dit script voor later wilt opslaan. 
1. Geef op de pagina De `ReplTest` wizard voltooien **een** naam aan uw publicatie en selecteer **Volgende** om uw publicatie te maken. 
1. Zodra uw publicatie is gemaakt, vernieuwt u **het** replicatieknooppunt in **Object Explorer** en vouwt u **Lokale publicaties** uit om uw nieuwe publicatie weer te geven. 


## <a name="10---create-the-subscription"></a>10 - Het abonnement maken 

Zodra de publicatie is gemaakt, u het abonnement maken. Hiervoor volgt u de volgende stappen: 

1. Start SQL Server Management Studio (SSMS) op uw SQL Server. 
1. Verbinding maken `sql-mi-publisher` met de beheerde instantie. 
1. Open een **nieuw queryvenster** en voer de volgende Transact-SQL-code uit om de abonnements- en distributieagent toe te voegen. Gebruik de DNS als onderdeel van de naam van de abonnee. 

```sql
use [ReplTutorial]
exec sp_addsubscription 
@publication = N'ReplTest',
@subscriber = N'sql-vm-sub.repldns.com', -- include the DNS configured in the private DNS zone
@destination_db = N'ReplSub', 
@subscription_type = N'Push', 
@sync_type = N'automatic', 
@article = N'all', 
@update_mode = N'read only', 
@subscriber_type = 0

exec sp_addpushsubscription_agent
@publication = N'ReplTest',
@subscriber = N'sql-vm-sub.repldns.com', -- include the DNS configured in the private DNS zone
@subscriber_db = N'ReplSub', 
@job_login = N'azureuser', 
@job_password = '<Complex Password>', 
@subscriber_security_mode = 0, 
@subscriber_login = N'azureuser', 
@subscriber_password = '<Complex Password>', 
@dts_package_location = N'Distributor'
GO
```

## <a name="11---test-replication"></a>11 - Replicatie testen 

Zodra replicatie is geconfigureerd, u deze testen door nieuwe items op de uitgever in te voegen en de wijzigingen aan de abonnee te zien doorgeven. 

Voer het volgende T-SQL-fragment uit om de rijen op de abonnee weer te geven:

```sql
Use ReplSub
select * from dbo.ReplTest
```

Voer het volgende T-SQL-fragment uit om extra rijen op de uitgever in te voegen en controleer vervolgens de rijen opnieuw op de abonnee. 

```sql
Use ReplTutorial
INSERT INTO ReplTest (ID, c1) VALUES (15, 'pub')
```

## <a name="clean-up-resources"></a>Resources opschonen

1. Navigeer naar uw brongroep in de [Azure-portal.](https://portal.azure.com) 
1. Selecteer de beheerde instantie(s) en selecteer **Verwijderen**. Typ `yes` in het tekstvak om te bevestigen dat u de bron wilt verwijderen en selecteer **Verwijderen**. Dit proces kan enige tijd in beslag nemen op de achtergrond, en totdat het is gedaan, u het *virtuele cluster* of andere afhankelijke bronnen niet verwijderen. Controleer de verwijdering op het tabblad Activiteit om te bevestigen dat uw beheerde instantie is verwijderd. 
1. Zodra de beheerde instantie is verwijderd, verwijdert u het *virtuele cluster* door het te selecteren in uw brongroep en vervolgens **Verwijderen**te kiezen . Typ `yes` in het tekstvak om te bevestigen dat u de bron wilt verwijderen en selecteer **Verwijderen**. 
1. Verwijder de resterende bronnen. Typ `yes` in het tekstvak om te bevestigen dat u de bron wilt verwijderen en selecteer **Verwijderen**. 
1. Verwijder de brongroep door **resourcegroep verwijderen**te selecteren, `myResourceGroup`de naam van de resourcegroep in te typen en vervolgens **Verwijderen**te selecteren . 

## <a name="known-errors"></a>Bekende fouten

### <a name="windows-logins-are-not-supported"></a>Windows-aanmeldingen worden niet ondersteund

`Exception Message: Windows logins are not supported in this version of SQL Server.`

De agent is geconfigureerd met een Windows-login en moet in plaats daarvan een SQL Server-login gebruiken. Gebruik de pagina **Agentbeveiliging** van de **publicatie-eigenschappen** om de inloggegevens te wijzigen in een SQL Server-aanmelding. 


### <a name="failed-to-connect-to-azure-storage"></a>Kan geen verbinding maken met Azure Storage


`Connecting to Azure Files Storage '\\replstorage.file.core.windows.net\replshare' Failed to connect to Azure Storage '' with OS error: 53.`

2019-11-19 02:21:05.07 Verkregen Azure Storage Connection String voor replstorage 2019-11-19 02:21:05.07 Verbinding maken met Azure Files Storage '\\replstorage.file.core.windows.net.replshare' 2019-11-19 02:21:31.21 Kan geen verbinding maken met Azure Storage '' met OS-fout: 53.


Dit is waarschijnlijk omdat poort 445 is gesloten in de Azure-firewall, de Windows-firewall of beide. 

`Connecting to Azure Files Storage '\\replstorage.file.core.windows.net\replshare' Failed to connect to Azure Storage '' with OS error: 55.`

Als u een slash doorsturen in plaats van backslash gebruikt in het bestandspad voor de bestandsshare, kan deze fout worden veroorzaakt. Dit is `\\replstorage.file.core.windows.net\replshare` oke: Dit kan leiden tot een OS 55 fout:`'\\replstorage.file.core.windows.net/replshare'`

### <a name="could-not-connect-to-subscriber"></a>Kan geen verbinding maken met abonnee

`The process could not connect to Subscriber 'SQL-VM-SUB`
`Could not open a connection to SQL Server [53].`
`A network-related or instance-specific error has occurred while establishing a connection to SQL Server. Server is not found or not accessible. Check if instance name is correct and if SQL Server is configured to allow remote connections.`

Mogelijke oplossingen:
- Zorg ervoor dat poort 1433 open is. 
- Zorg ervoor dat TCP/IP is ingeschakeld voor de abonnee. 
- Controleer of de DNS-naam is gebruikt bij het maken van de abonnee. 
- Controleer of uw virtuele netwerken correct zijn gekoppeld in de privé-DNS-zone. 
- Controleer of uw A-record correct is geconfigureerd. 
- Controleer of uw VPN-peering correct is geconfigureerd. 

### <a name="no-publications-to-which-you-can-subscribe"></a>Geen publicaties waarop u zich abonneren

Wanneer u een nieuw abonnement toevoegt met de wizard **Nieuw abonnement,** op de pagina **Publicatie,** u merken dat er geen databases en publicaties worden weergegeven als beschikbare opties en ziet u mogelijk het volgende foutbericht:

`There are no publications to which you can subscribe, either because this server has no publications or because you do not have sufficient privileges to access the publications.`
 
Hoewel het mogelijk is dat deze foutmelding juist is en er echt geen publicaties beschikbaar zijn op de uitgever waarmee u bent verbonden, of dat u onvoldoende machtigingen hebt, kan deze fout ook worden veroorzaakt door een oudere versie van SQL Server Management Studio. Probeer te upgraden naar SQL Server Management Studio 18.0 of hoger om dit uit te sluiten als een hoofdoorzaak. 


## <a name="next-steps"></a>Volgende stappen

### <a name="enable-security-features"></a>Beveiligingsfuncties in te schakelen

Zie het volgende artikel over beveiligingsfuncties voor [beheerde instantie](sql-database-managed-instance.md#azure-sql-database-security-features) voor een uitgebreide lijst met manieren om uw database te beveiligen. De volgende beveiligingsfuncties worden besproken:

- [Beheerde instantiecontrole](sql-database-managed-instance-auditing.md) 
- [Altijd versleuteld](/sql/relational-databases/security/encryption/always-encrypted-database-engine)
- [Detectie van bedreigingen](sql-database-managed-instance-threat-detection.md) 
- [Dynamisch maskeren van gegevens](/sql/relational-databases/security/dynamic-data-masking)
- [Beveiliging op rijniveau](/sql/relational-databases/security/row-level-security) 
- [Transparante gegevensversleuteling (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)

### <a name="managed-instance-capabilities"></a>Mogelijkheden van beheerde exemplaren

Een volledig overzicht van de mogelijkheden van een beheerd exemplaar is hier te vinden:

> [!div class="nextstepaction"]
> [Beheerde instantiemogelijkheden](sql-database-managed-instance.md)
