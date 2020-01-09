---
title: Transactionele replicatie tussen twee beheerde instanties en SQL Server configureren
description: Een zelf studie voor het configureren van replicatie tussen een beheerd exemplaar van Publisher, een beheerd exemplaar van een distributie server en een SQL Server-abonnee op een Azure VM, samen met de benodigde netwerk onderdelen, zoals persoonlijke DNS-zone en VPN-peering.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: tutorial
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 11/21/2019
ms.openlocfilehash: f61c99db3f2c2569705e3a2df4b7134fdd29065a
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/02/2020
ms.locfileid: "75614908"
---
# <a name="tutorial-configure-transactional-replication-between-two-managed-instances-and-sql-server"></a>Zelf studie: transactionele replicatie tussen twee beheerde instanties en SQL Server configureren


In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> - Configureer een beheerd exemplaar als een replicatie-Uitgever. 
> - Een beheerd exemplaar configureren als een replicatie distributie server. 
> - Een SQL Server als abonnee configureren. 

![Replicatie tussen een SQL MI-publicatie, SQL MI-VERD en een SQL Server sub](media/sql-database-managed-instance-failover-group-tutorial/sqlmi-to-sql-replication.png)

Deze zelf studie is bedoeld voor een ervaren doel groep en er wordt van uitgegaan dat de gebruiker bekend is met het implementeren en verbinden met zowel beheerde exemplaren als SQL Server Vm's in Azure. Als zodanig worden bepaalde stappen in deze zelf studie glanzend. 

Zie voor meer informatie de [Azure SQL database Managed instance-overzicht](sql-database-managed-instance-index.yml), [mogelijkheden en functies](sql-database-managed-instance.md)voor het [transactionele SQL-transactie](sql-database-managed-instance-transactional-replication.md) artikel.

Zie [transactionele replicatie tussen twee beheerde instanties configureren](replication-with-sql-database-managed-instance.md)voor het configureren van replicatie tussen een beheerde exemplaar van een uitgever en een beheerde exemplaar-abonnee. 

## <a name="prerequisites"></a>Vereisten

Zorg dat u over het volgende beschikt als u de zelfstudie wilt uitvoeren:

- Een [Azure-abonnement](https://azure.microsoft.com/free/). 
- Ervaring met het implementeren van twee beheerde exemplaren binnen hetzelfde virtuele netwerk. 
- Een SQL Server-abonnee, een on-premises of een Azure VM. In deze zelf studie wordt een virtuele Azure-machine gebruikt.  
- [SQL Server Management Studio (SSMS) 18,0 of hoger](/sql/ssms/download-sql-server-management-studio-ssms).
- De nieuwste versie van [Azure Power shell](/powershell/azure/install-az-ps?view=azps-1.7.0).
- Poort 445 en 1433 staan SQL-verkeer toe op zowel de Azure Firewall als de Windows Firewall. 

## <a name="1---create-the-resource-group"></a>1: de resource groep maken
Gebruik het volgende Power shell-code fragment om een nieuwe resource groep te maken:

```powershell-interactive
# set variables
$ResourceGroupName = "SQLMI-Repl"
$Location = "East US 2"

# Create a new resource group
New-AzResourceGroup -Name  $ResourceGroupName -Location $Location
```

## <a name="2---create-two-managed-instances"></a>2-twee beheerde instanties maken
Maak twee beheerde exemplaren binnen deze nieuwe resource groep met behulp van de [Azure Portal](https://portal.azure.com). 

- De naam van het beheerde exemplaar van de uitgever moet zijn: `sql-mi-publisher` (samen met een paar tekens voor wille keurige) en de naam van het virtuele netwerk moet `vnet-sql-mi-publisher`zijn.
- De naam van het beheerde exemplaar van de distributie server moet zijn: `sql-mi-distributor` (samen met een paar tekens voor wille keurige) en moet zich _in hetzelfde virtuele netwerk bevindt als het beheerde exemplaar van de uitgever_.

   ![Het vnet van de uitgever voor de Distributor gebruiken](media/sql-database-managed-instance-configure-replication-tutorial/use-same-vnet-for-distributor.png)

Zie [een beheerd exemplaar maken in de portal](sql-database-managed-instance-get-started.md) voor meer informatie over het maken van een beheerd exemplaar.

  > [!NOTE]
  > Omwille van de eenvoud, en omdat dit de meest voorkomende configuratie is, wordt in deze zelf studie geadviseerd om het beheerde exemplaar van de distributie server te plaatsen binnen hetzelfde virtuele netwerk als de uitgever. Het is echter mogelijk om de Distributor te maken in een afzonderlijk virtueel netwerk. Hiervoor moet u VPN-peering configureren tussen de virtuele netwerken van de uitgever en de Distributor en vervolgens VPN-peering configureren tussen de virtuele netwerken van de Distributor en de abonnee. 

## <a name="3---create-a-sql-server-vm"></a>3-een SQL Server VM maken
Maak een SQL Server virtuele machine met behulp van de [Azure Portal](https://portal.azure.com). De virtuele machine van SQL Server moet de volgende kenmerken hebben:

- Naam: `sql-vm-sub`
- Afbeelding: SQL Server 2016 of hoger
- Resource groep: hetzelfde als het beheerde exemplaar
- Virtueel netwerk: `sql-vm-sub-vnet` 

Voor meer informatie over het implementeren van een SQL Server VM naar Azure raadpleegt u [Quick Start: Create SQL Server VM](../virtual-machines/windows/sql/quickstart-sql-vm-create-portal.md).

## <a name="4---configure-vpn-peering"></a>4-VPN-peering configureren
Configureer VPN-peering om communicatie mogelijk te maken tussen het virtuele netwerk van de twee beheerde instanties en het virtuele netwerk van SQL Server. Gebruik hiervoor dit Power shell-code fragment:

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

Wanneer VPN-peering tot stand is gebracht, kunt u de connectiviteit testen door SQL Server Management Studio (SSMS) te starten op uw SQL Server en verbinding te maken met beide beheerde exemplaren. Zie [SSMS gebruiken om verbinding te maken met de mi](sql-database-managed-instance-configure-p2s.md#use-ssms-to-connect-to-the-managed-instance)voor meer informatie over het maken van verbinding met een beheerd exemplaar met behulp van SSMS. 

![De verbinding met de beheerde instanties testen](media/sql-database-managed-instance-configure-replication-tutorial/test-connectivity-to-mi.png)

## <a name="5---create-private-dns-zone"></a>5-een persoonlijke DNS-zone maken

Een privé-DNS-zone staat DNS-route ring tussen de beheerde instanties en de SQL Server toe. 

### <a name="create-private-dns-zone"></a>Een persoonlijke DNS-zone maken
1. Meld u aan bij de [Azure Portal](https://portal.azure.com).
1. Selecteer **een resource maken** om een nieuwe Azure-resource te maken. 
1. Zoek naar `private dns zone` op Azure Marketplace. 
1. Kies de **privé-DNS zone** resource die door micro soft is gepubliceerd en selecteer vervolgens **maken** om de DNS-zone te maken. 
1. Kies het abonnement en de resource groep in de vervolg keuzelijst. 
1. Geef een wille keurige naam op voor uw DNS-zone, zoals `repldns.com`. 

   ![Een persoonlijke DNS-zone maken](media/sql-database-managed-instance-configure-replication-tutorial/create-private-dns-zone.png)

1. Selecteer **Controleren + maken**. Controleer de para meters voor uw privé-DNS-zone en selecteer vervolgens **maken** om de resource te maken. 

### <a name="create-a-record"></a>Een record maken

1. Ga naar de nieuwe **privé-DNS zone** en selecteer **overzicht**. 
1. Selecteer **+ Recordset** om een nieuwe a-record te maken. 
1. Geef de naam van uw SQL Server virtuele machine en het persoonlijke interne IP-adres op. 

   ![Een record configureren](media/sql-database-managed-instance-configure-replication-tutorial/configure-a-record.png)

1. Selecteer **OK** om de A-record te maken. 

### <a name="link-the-virtual-network"></a>Het virtuele netwerk koppelen

1. Ga naar de nieuwe **privé-DNS zone** en selecteer **koppelingen naar het virtuele netwerk**. 
1. Selecteer **+ Toevoegen**. 
1. Geef een naam op voor de koppeling, bijvoorbeeld `Pub-link`. 
1. Selecteer uw abonnement in de vervolg keuzelijst en selecteer vervolgens het virtuele netwerk voor uw beheerde instantie van de uitgever. 
1. Schakel het selectie vakje naast **automatische registratie inschakelen**in. 

   ![Vnet-koppeling maken](media/sql-database-managed-instance-configure-replication-tutorial/configure-vnet-link.png)

1. Selecteer **OK** om het virtuele netwerk te koppelen. 
1. Herhaal deze stappen om een koppeling toe te voegen voor het virtuele netwerk van de abonnee, met een naam zoals `Sub-link`. 


## <a name="6---create-azure-storage-account"></a>6-Azure Storage account maken

[Maak een Azure Storage-account](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account#create-a-storage-account) voor de werkmap en maak een [Bestands share](../storage/files/storage-how-to-create-file-share.md) in het opslag account. 

Kopieer het pad naar de bestands share met de volgende indeling: `\\storage-account-name.file.core.windows.net\file-share-name`   

Voorbeeld: `\\replstorage.file.core.windows.net\replshare`

Kopieer de toegangs sleutel voor opslag connection string in de indeling van: `DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net`   

Voorbeeld: `DefaultEndpointsProtocol=https;AccountName=replstorage;AccountKey=dYT5hHZVu9aTgIteGfpYE64cfis0mpKTmmc8+EP53GxuRg6TCwe5eTYWrQM4AmQSG5lb3OBskhg==;EndpointSuffix=core.windows.net`


Zie [toegangs sleutels voor opslag accounts beheren](../storage/common/storage-account-keys-manage.md)voor meer informatie. 


## <a name="7---create-a-database"></a>7-een Data Base maken
Maak een nieuwe Data Base op de Publisher MI. Voer hiertoe de volgende stappen uit:

1. Start SQL Server Management Studio (SSMS) op uw SQL Server. 
1. Verbinding maken met het beheerde exemplaar van `sql-mi-publisher`. 
1. Open een **Nieuw query** venster en voer de volgende T-SQL-query uit om de data base te maken:

```sql
-- Create the databases
USE [master]
GO

-- Drop database if it exists
IF EXISTS (SELECT *FROM sys.sysdatabases WHERE name = 'ReplTutorial')
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

## <a name="8---configure-distribution"></a>8-distributie configureren 
Zodra de verbinding tot stand is gebracht en u een voorbeeld database hebt, kunt u distributie op uw `sql-mi-distributor` beheerde instantie configureren. Voer hiertoe de volgende stappen uit:

1. Start SQL Server Management Studio (SSMS) op uw SQL Server. 
1. Verbinding maken met het beheerde exemplaar van `sql-mi-distributor`. 
1. Open een **Nieuw query** venster en voer de volgende Transact-SQL-code uit om distributie te configureren op het beheerde exemplaar van de distributie server: 

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
   > Zorg ervoor dat u alleen backslashes (`\`) gebruikt voor de para meter @working_directory. Het gebruik van een slash (`/`) kan een fout veroorzaken wanneer er verbinding wordt gemaakt met de bestands share. 

1. Verbinding maken met het beheerde exemplaar van `sql-mi-publisher`. 
1. Open een **Nieuw query** venster en voer de volgende Transact-SQL-code uit om de Distributor bij de uitgever te registreren: 

```sql
Use MASTER
EXEC sys.sp_adddistributor @distributor = 'sql-mi-distributor.b6bf57.database.windows.net', @password = '<distributor_admin_password>' 
```


## <a name="9---create-the-publication"></a>9: de publicatie maken
Zodra de distributie is geconfigureerd, kunt u de publicatie nu maken. Voer hiertoe de volgende stappen uit: 

1. Start SQL Server Management Studio (SSMS) op uw SQL Server. 
1. Verbinding maken met het beheerde exemplaar van `sql-mi-publisher`. 
1. Vouw in **objectverkenner**het knoop punt **replicatie** uit en klik met de rechter muisknop op de map voor de **lokale publicatie** . **Nieuwe publicatie selecteren...** . 
1. Selecteer **volgende** om de welkomst pagina te verplaatsen. 
1. Selecteer op de pagina **publicatiedata base** de `ReplTutorial`-data base die u eerder hebt gemaakt. Selecteer **Next**. 
1. Selecteer op de pagina **publicatie type** **transactionele publicatie**. Selecteer **Next**. 
1. Schakel op de pagina **artikelen** het selectie vakje naast **tabellen**in. Selecteer **Next**. 
1. Selecteer op de pagina **tabel rijen filteren** de optie **volgende** zonder filters toe te voegen. 
1. Schakel op de pagina **momentopname agent** het selectie vakje naast **moment opname direct maken in en behoud de moment opname voor het initialiseren van abonnementen**. Selecteer **Next**. 
1. Selecteer op de pagina **agent beveiliging** de optie **beveiligings instellingen..** . Geef SQL Server aanmeldings referenties op die moeten worden gebruikt voor de momentopname agent en om verbinding te maken met de uitgever. Selecteer **OK** om de beveiligings pagina van de **momentopname agent** te sluiten. Selecteer **Next**. 

   ![Beveiliging van snap shot agent configureren](media/sql-database-managed-instance-configure-replication-tutorial/snapshot-agent-security.png)

1. Kies op de pagina **wizard acties** de optie voor **het maken van de publicatie** en (optioneel) Kies voor het **genereren van een script bestand met stappen voor het maken van de publicatie** als u dit script wilt opslaan voor later. 
1. Geef op de pagina **de wizard volt ooien** uw publicatie een naam `ReplTest` en selecteer **volgende** om uw publicatie te maken. 
1. Nadat de publicatie is gemaakt, vernieuwt u het **replicatie** knooppunt in **objectverkenner** en vouwt u **lokale publicaties** uit om uw nieuwe publicatie te bekijken. 


## <a name="10---create-the-subscription"></a>10: het abonnement maken 

Als de publicatie eenmaal is gemaakt, kunt u het abonnement maken. Voer hiertoe de volgende stappen uit: 

1. Start SQL Server Management Studio (SSMS) op uw SQL Server. 
1. Verbinding maken met het beheerde exemplaar van `sql-mi-publisher`. 
1. Open een **Nieuw query** venster en voer de volgende Transact-SQL-code uit om het abonnement en de distributie agent toe te voegen. Gebruik de DNS als onderdeel van de naam van de abonnee. 

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

## <a name="11---test-replication"></a>11-replicatie testen 

Zodra de replicatie is geconfigureerd, kunt u deze testen door nieuwe items in te voegen op de Publisher en de wijzigingen te bekijken die worden door gegeven aan de abonnee. 

Voer het volgende T-SQL-code fragment uit om de rijen op de abonnee weer te geven:

```sql
Use ReplSub
select * from dbo.ReplTest
```

Voer het volgende T-SQL-code fragment uit om extra rijen in te voegen op de Publisher en controleer de rijen vervolgens opnieuw op de abonnee. 

```sql
Use ReplTutorial
INSERT INTO ReplTest (ID, c1) VALUES (15, 'pub')
```

## <a name="clean-up-resources"></a>Resources opschonen

1. Navigeer naar uw resource groep in de [Azure Portal](https://portal.azure.com). 
1. Selecteer een of meer beheerde exemplaren en selecteer vervolgens **verwijderen**. Typ `yes` in het tekstvak om te bevestigen dat u de resource wilt verwijderen en selecteer vervolgens **verwijderen**. Het kan enige tijd duren voordat dit proces op de achtergrond is voltooid en tot de ' s ' klaar is, kunt u het *virtuele cluster* of andere afhankelijke resources niet verwijderen. Controleer het verwijderen op het tabblad activiteit om te bevestigen dat uw beheerde exemplaar is verwijderd. 
1. Wanneer het beheerde exemplaar is verwijderd, verwijdert u het *virtuele cluster* door het te selecteren in de resource groep en vervolgens **verwijderen**te kiezen. Typ `yes` in het tekstvak om te bevestigen dat u de resource wilt verwijderen en selecteer vervolgens **verwijderen**. 
1. Alle resterende resources verwijderen. Typ `yes` in het tekstvak om te bevestigen dat u de resource wilt verwijderen en selecteer vervolgens **verwijderen**. 
1. Verwijder de resource groep door **resource groep verwijderen**te selecteren, typ de naam van de resource groep `myResourceGroup`en selecteer vervolgens **verwijderen**. 

## <a name="known-errors"></a>Bekende fouten

### <a name="windows-logins-are-not-supported"></a>Windows-aanmeldingen worden niet ondersteund

`Exception Message: Windows logins are not supported in this version of SQL Server.`

De agent is geconfigureerd met een Windows-aanmelding en moet in plaats daarvan een SQL Server aanmelding gebruiken. Gebruik de pagina **agent beveiliging** van de **publicatie-eigenschappen** om de aanmeldings referenties te wijzigen in een SQL Server aanmelding. 


### <a name="failed-to-connect-to-azure-storage"></a>Kan geen verbinding maken met Azure Storage


`Connecting to Azure Files Storage '\\replstorage.file.core.windows.net\replshare' Failed to connect to Azure Storage '' with OS error: 53.`

2019-11-19 02:21:05.07 verkregen Azure Storage verbindings reeks voor replstorage 2019-11-19 02:21:05.07 verbinding maken met Azure Files opslag '\\replstorage. file. core. Windows. net\replshare ' 2019-11-19 02:21:31.21 kan geen verbinding maken met Azure Storage ' ' met een besturingssysteem fout: 53.


Dit komt waarschijnlijk doordat poort 445 is gesloten in de Azure-firewall, de Windows Firewall of beide. 

`Connecting to Azure Files Storage '\\replstorage.file.core.windows.net\replshare' Failed to connect to Azure Storage '' with OS error: 55.`

Het gebruik van een slash in plaats van een back slash in het bestandspad voor de bestands share kan deze fout veroorzaken. Dit is het volgende: `\\replstorage.file.core.windows.net\replshare` dit kan leiden tot een besturings systeem 55 fout: `'\\replstorage.file.core.windows.net/replshare'`

### <a name="could-not-connect-to-subscriber"></a>Kan geen verbinding maken met de abonnee

`The process could not connect to Subscriber 'SQL-VM-SUB`
`Could not open a connection to SQL Server [53].`
`A network-related or instance-specific error has occurred while establishing a connection to SQL Server. Server is not found or not accessible. Check if instance name is correct and if SQL Server is configured to allow remote connections.`

Mogelijke oplossingen:
- Zorg ervoor dat poort 1433 is geopend. 
- Zorg ervoor dat TCP/IP is ingeschakeld op de abonnee. 
- Controleer of de DNS-naam is gebruikt bij het maken van de abonnee. 
- Controleer of de virtuele netwerken correct zijn gekoppeld in de privé-DNS-zone. 
- Controleer of uw A-record juist is geconfigureerd. 
- Controleer of uw VPN-peering correct is geconfigureerd. 

### <a name="no-publications-to-which-you-can-subscribe"></a>Geen publicaties waarop u zich kunt abonneren

Wanneer u een nieuw abonnement toevoegt met behulp van de wizard **Nieuw abonnement** , is het mogelijk dat er op de **publicatie** pagina geen data bases en publicaties worden weer gegeven als beschik bare opties. mogelijk ziet u het volgende fout bericht:

`There are no publications to which yuo can subscribe, either because this server has no publications or because you do not have sufficient privileges to access the publications.`
 
Hoewel het mogelijk is dat dit fout bericht nauw keurig is en er geen publicaties beschikbaar zijn op de Publisher waarmee u verbinding hebt gemaakt, of omdat u niet voldoende machtigingen hebt, kan deze fout ook worden veroorzaakt door een oudere versie van SQL Server Management Studio. Voer een upgrade uit naar SQL Server Management Studio 18,0 of hoger om dit uit te proberen als hoofd oorzaak. 


## <a name="next-steps"></a>Volgende stappen

### <a name="enable-security-features"></a>Beveiligingsfuncties in te schakelen

Zie het artikel [Beveiligingsfuncties voor mogelijkheden van beheerde exemplaren](sql-database-managed-instance.md#azure-sql-database-security-features) voor een uitgebreide lijst met manieren om uw database te beveiligen. De volgende beveiligingsfuncties worden besproken:

- [Controle van beheerde exemplaren](sql-database-managed-instance-auditing.md) 
- [Altijd versleuteld](/sql/relational-databases/security/encryption/always-encrypted-database-engine)
- [Detectie van bedreigingen](sql-database-managed-instance-threat-detection.md) 
- [Dynamische gegevensmaskering](/sql/relational-databases/security/dynamic-data-masking)
- [Beveiliging op rijniveau](/sql/relational-databases/security/row-level-security) 
- [TDE (Transparent Data Encryption)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)

### <a name="managed-instance-capabilities"></a>Mogelijkheden van beheerde exemplaren

Een volledig overzicht van de mogelijkheden van een beheerd exemplaar is hier te vinden:

> [!div class="nextstepaction"]
> [Mogelijkheden van beheerde exemplaren](sql-database-managed-instance.md)
