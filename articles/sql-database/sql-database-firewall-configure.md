---
title: IP-firewallregels
description: Ip-firewallregels op serverniveau configureren voor een SQL-database of SQL Data Warehouse-firewall. Beheer de IP-firewallregels op databaseniveau en configureer deze voor één of samengevoegde database.
services: sql-database
ms.service: sql-database
ms.subservice: security
titleSuffix: Azure SQL Database and SQL Data Warehouse
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: carlrab
ms.date: 03/18/2019
ms.openlocfilehash: af88fdf3378a6290c773c658ea6dd3469d7c92cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79531274"
---
# <a name="azure-sql-database-and-azure-sql-data-warehouse-ip-firewall-rules"></a>Azure SQL Database en Azure SQL Data Warehouse IP-firewallregels

> [!NOTE]
> Dit artikel is van toepassing op Azure SQL-servers en op zowel Azure SQL Database als Azure SQL Data Warehouse-databases op een Azure SQL-server. Voor de eenvoud wordt *SQL Database* gebruikt om te verwijzen naar zowel SQL Database als SQL Data Warehouse.

> [!IMPORTANT]
> Dit artikel is *niet* van toepassing op *Azure SQL Database Managed Instance*. Zie [Uw toepassing verbinden met Azure SQL Database Managed Instance](sql-database-managed-instance-connect-app.md)voor informatie over netwerkconfiguratie.

Wanneer u bijvoorbeeld een nieuwe Azure SQL-server met de naam *mysqlserver*maakt, blokkeert de SQL Database-firewall alle toegang tot het openbare eindpunt voor de server (die toegankelijk is bij *mysqlserver.database.windows.net).*

> [!IMPORTANT]
> SQL Data Warehouse ondersteunt alleen IP-firewallregels op serverniveau. Het ondersteunt geen IP-firewallregels op databaseniveau.

## <a name="how-the-firewall-works"></a>Hoe de firewall werkt
Verbindingspogingen van internet en Azure moeten door de firewall gaan voordat ze uw SQL-server of SQL-database bereiken, zoals het volgende diagram laat zien.

   ![Firewallconfiguratiediagram][1]

### <a name="server-level-ip-firewall-rules"></a>IP-firewallregels op serverniveau

  Met deze regels kunnen clients toegang krijgen tot uw volledige Azure SQL-server, dat wil zeggen alle databases binnen dezelfde SQL Database-server. De regels worden opgeslagen in de *hoofddatabase.* U maximaal 128 IP-firewallregels op serverniveau hebben voor een Azure SQL Server. Als u azure **services en resources toestaan om toegang te krijgen tot deze serverinstelling** is ingeschakeld, geldt dit als één firewallregel voor Azure SQL Server.
  
  U IP-firewallregels op serverniveau configureren met behulp van de Azure-portal-, PowerShell- of Transact-SQL-instructies.
  - Als u de portal of PowerShell wilt gebruiken, moet u de eigenaar van het abonnement of een abonnementsbijdrager zijn.
  - Als u Transact-SQL wilt gebruiken, moet u verbinding maken met de SQL Database-instantie als hoofdaanmelding op serverniveau of als azure Active Directory-beheerder. (Een IP-firewallregel op serverniveau moet eerst worden gemaakt door een gebruiker met machtigingen op Azure-niveau.)

### <a name="database-level-ip-firewall-rules"></a>IP-firewallregels op databaseniveau

  Met deze regels kunnen clients toegang krijgen tot bepaalde (beveiligde) databases binnen dezelfde SQL Database-server. U maakt de regels voor elke database (inclusief de *hoofddatabase)* en deze worden opgeslagen in de afzonderlijke database.
  
  U alleen IP-firewallregels op databaseniveau maken en beheren met behulp van Transact-SQL-instructies en alleen nadat u de eerste firewall op serverniveau hebt geconfigureerd.
  
  Als u een IP-adresbereik opgeeft in de IP-firewallregel op databaseniveau die buiten het bereik van de IP-firewallregel op serverniveau valt, hebben alleen clients met IP-adressen in het databaseniveaubereik toegang tot de database.
  
  U maximaal 128 IP-firewallregels op databaseniveau voor een database hebben. Zie het voorbeeld later in dit artikel en [zie sp_set_database_firewall_rule (Azure SQL Database)](https://msdn.microsoft.com/library/dn270010.aspx)voor meer informatie over het configureren van IP-firewallregels op databaseniveau.

### <a name="recommendations-for-how-to-set-firewall-rules"></a>Aanbevelingen voor het instellen van firewallregels

We raden u aan waar mogelijk ip-firewallregels op databaseniveau te gebruiken. Deze praktijk verbetert de beveiliging en maakt uw database draagbaarder. Gebruik IP-firewallregels op serverniveau voor beheerders. Gebruik ze ook wanneer u veel databases hebt met dezelfde toegangsvereisten en u niet elke database afzonderlijk wilt configureren.

> [!NOTE]
> Voor meer informatie over draagbare databases in de context van bedrijfscontinuïteit raadpleegt u [Authentication requirements for disaster recovery](sql-database-geo-replication-security-config.md) (Verificatievereisten voor herstel na noodgevallen).

## <a name="server-level-versus-database-level-ip-firewall-rules"></a>Regels voor IP-firewall op serverniveau versus ip-firewall op databaseniveau

*Moeten gebruikers van een database volledig worden geïsoleerd uit een andere database?*

Zo *ja,* gebruik dan IP-firewallregels op databaseniveau om toegang te verlenen. Deze methode voorkomt het gebruik van IP-firewallregels op serverniveau, die toegang via de firewall tot alle databases mogelijk maken. Dat zou de diepte van je verdediging verminderen.

*Hebben gebruikers op de IP-adressen toegang nodig tot alle databases?*

Zo *ja,* gebruik dan IP-firewallregels op serverniveau om het aantal keren dat u IP-firewallregels moet configureren, te verminderen.

*Heeft de persoon of het team die de IP-firewallregels configureert alleen toegang via de Azure-portal, PowerShell of de REST API?*

Als dat het zo is, moet u IP-firewallregels op serverniveau gebruiken. Ip-firewallregels op databaseniveau kunnen alleen worden geconfigureerd via Transact-SQL.  

*Is het de persoon of het team die de IP-firewallregels configureert, verboden om toestemming op hoog niveau te hebben op databaseniveau?*

Gebruik dan IP-firewallregels op serverniveau. U hebt ten minste de machtiging van de *CONTROLEdatabase* op databaseniveau nodig om IP-firewallregels op databaseniveau te configureren via Transact-SQL.  

*Beheert de persoon of het team die de IP-firewallregels configureert of controleert de IP-firewallregels voor veel (misschien wel honderden) databases centraal?*

In dit scenario worden best practices bepaald door uw behoeften en omgeving. Ip-firewallregels op serverniveau zijn mogelijk eenvoudiger te configureren, maar scripting kan regels op databaseniveau configureren. En zelfs als u IP-firewallregels op serverniveau gebruikt, moet u mogelijk IP-firewallregels op databaseniveau controleren om te zien of gebruikers met *control-toestemming* in de database IP-firewallregels op databaseniveau maken.

*Kan ik een mix van IP-firewallregels op serverniveau en databaseniveau gebruiken?*

Ja. Sommige gebruikers, zoals beheerders, hebben mogelijk IP-firewallregels op serverniveau nodig. Andere gebruikers, zoals gebruikers van een databasetoepassing, hebben mogelijk IP-firewallregels op databaseniveau nodig.

### <a name="connections-from-the-internet"></a>Verbindingen vanaf het internet

Wanneer een computer vanaf het internet verbinding probeert te maken met uw databaseserver, controleert de firewall eerst het ip-adres van de aanvraag op basis van de IP-firewallregels op databaseniveau voor de database die de verbinding opvraagt.

- Als het adres zich binnen een bereik bevindt dat is opgegeven in de IP-firewallregels op databaseniveau, wordt de verbinding verleend met de SQL-database die de regel bevat.
- Als het adres zich niet binnen een bereik van de IP-firewallregels op databaseniveau bevindt, controleert de firewall de IP-firewallregels op serverniveau. Als het adres zich binnen een bereik bevindt dat zich in de IP-firewallregels op serverniveau bevindt, wordt de verbinding verleend. Ip-firewallregels op serverniveau zijn van toepassing op alle SQL-databases op de Azure SQL-server.  
- Als het adres zich niet binnen een bereik bevindt dat zich in een van de IP-firewallregels op database- of serverniveau bevindt, mislukt de verbindingsaanvraag.

> [!NOTE]
> Als u SQL Database vanaf uw lokale computer wilt openen, moet u ervoor zorgen dat de firewall op uw netwerk en lokale computer uitgaande communicatie op TCP-poort 1433 toestaan.

### <a name="connections-from-inside-azure"></a>Verbindingen vanuit Azure

Azure-verbindingen moeten zijn ingeschakeld om toepassingen die in Azure worden gehost, verbinding te laten maken met uw SQL-server. Wanneer een toepassing van Azure verbinding probeert te maken met uw databaseserver, controleert de firewall of Azure-verbindingen zijn toegestaan. Een firewall-instelling met start- en eindIP-adressen die gelijk zijn aan *0.0.0.0,* geeft aan dat Azure-verbindingen zijn toegestaan. Dit kan rechtstreeks vanuit het Azure Portal-blad worden ingeschakeld door Firewallregels in te stellen en de **Azure-services en -bronnen toestaan om toegang te krijgen tot deze server in** **de** instellingen **voor firewalls en virtuele netwerken.** Als de verbinding niet is toegestaan, bereikt de aanvraag de SQL Database-server niet.

> [!IMPORTANT]
> Met deze optie configureert u de firewall om alle verbindingen van Azure toe te staan, inclusief verbindingen van de abonnementen van andere klanten. Als u deze optie selecteert, moet u ervoor zorgen dat uw aanmeldings- en gebruikersmachtigingen de toegang beperken tot alleen geautoriseerde gebruikers.

## <a name="create-and-manage-ip-firewall-rules"></a>IP-firewallregels maken en beheren

U maakt de eerste firewallinstelling op serverniveau met behulp van de [Azure-portal](https://portal.azure.com/) of programmatisch met [azure PowerShell,](https://docs.microsoft.com/powershell/module/az.sql) [Azure CLI](https://docs.microsoft.com/cli/azure/sql/server/firewall-rule)of een Azure [REST API.](https://docs.microsoft.com/rest/api/sql/firewallrules/createorupdate) U maakt en beheert aanvullende IP-firewallregels op serverniveau met behulp van deze methoden of Transact-SQL.

> [!IMPORTANT]
> Ip-firewallregels op databaseniveau kunnen alleen worden gemaakt en beheerd met Transact-SQL.

Om de prestaties te verbeteren, worden IP-firewallregels op serverniveau tijdelijk in de cache opgeslagen op databaseniveau. Zie [DBCC FLUSHAUTHCACHE](https://msdn.microsoft.com/library/mt627793.aspx) als u de cache wilt vernieuwen.

> [!TIP]
> U [SQL Database Auditing](sql-database-auditing.md) gebruiken om firewallwijzigingen op serverniveau en firewallop basisniveau te controleren.

### <a name="use-the-azure-portal-to-manage-server-level-ip-firewall-rules"></a>De Azure-portal gebruiken om IP-firewallregels op serverniveau te beheren

Als u een IP-firewallregel op serverniveau wilt instellen in de Azure-portal, gaat u naar de overzichtspagina voor uw Azure SQL-database of uw SQL Database-server.

> [!TIP]
> Zie [Een DB maken met de Azure-portal](sql-database-single-database-get-started.md)voor een zelfstudie.

#### <a name="from-the-database-overview-page"></a>Vanaf de overzichtspagina van de database

1. Als u een IP-firewallregel op serverniveau wilt instellen op de pagina databaseoverzicht, selecteert u **Serverfirewall instellen** op de werkbalk, zoals de volgende afbeelding wordt weergegeven. 

    ![Server IP-firewallregel](./media/sql-database-get-started-portal/sql-database-server-set-firewall-rule.png)

    De pagina **Firewall-instellingen** voor de SQL Database-server wordt geopend.

2. Selecteer **Client-IP toevoegen** op de werkbalk om het IP-adres van de computer toe te voegen dat u gebruikt en selecteer **Vervolgens Opslaan**. Er wordt een IP-firewallregel op serverniveau gemaakt voor uw huidige IP-adres.

    ![IP-firewallregel op serverniveau instellen](./media/sql-database-get-started-portal/sql-database-server-firewall-settings.png)

#### <a name="from-the-server-overview-page"></a>Vanaf de pagina serveroverzicht

De overzichtspagina voor uw server wordt geopend. Het toont de volledig gekwalificeerde servernaam (zoals *mynewserver20170403.database.windows.net)* en biedt opties voor verdere configuratie.

1. Als u op deze pagina een regel op serverniveau wilt instellen, selecteert u **Firewall** in het menu **Instellingen** aan de linkerkant.

2. Selecteer **Client-IP toevoegen** op de werkbalk om het IP-adres van de computer toe te voegen dat u gebruikt en selecteer **Vervolgens Opslaan**. Er wordt een IP-firewallregel op serverniveau gemaakt voor uw huidige IP-adres.

### <a name="use-transact-sql-to-manage-ip-firewall-rules"></a>Transact-SQL gebruiken om IP-firewallregels te beheren

| Catalogusweergave of opgeslagen procedure | Niveau | Beschrijving |
| --- | --- | --- |
| [sys.firewall_rules](https://msdn.microsoft.com/library/dn269980.aspx) |server |Geeft de huidige IP-firewallregels op serverniveau weer |
| [sp_set_firewall_rule](https://msdn.microsoft.com/library/dn270017.aspx) |server |Ip-firewallregels op serverniveau maken of bijwerken |
| [sp_delete_firewall_rule](https://msdn.microsoft.com/library/dn270024.aspx) |server |Ip-firewallregels op serverniveau verwijderen |
| [sys.database_firewall_rules](https://msdn.microsoft.com/library/dn269982.aspx) |Database |Geeft de huidige IP-firewallregels op databaseniveau weer |
| [sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx) |Database |De IP-firewallregels op databaseniveau maken of bijwerken |
| [sp_delete_database_firewall_rule](https://msdn.microsoft.com/library/dn270030.aspx) |Databases |Ip-firewallregels op databaseniveau verwijderen |

In het volgende voorbeeld worden de bestaande regels beoordeeld, wordt een reeks IP-adressen op de server *Contoso*ingeschakeld en wordt een IP-firewallregel verwijderd:

```sql
SELECT * FROM sys.firewall_rules ORDER BY name;
```

Voeg vervolgens een IP-firewallregel op serverniveau toe.

```sql
EXECUTE sp_set_firewall_rule @name = N'ContosoFirewallRule',
   @start_ip_address = '192.168.1.1', @end_ip_address = '192.168.1.10'
```

Als u een IP-firewallregel op serverniveau wilt verwijderen, voert u de *sp_delete_firewall_rule* opgeslagen procedure uit. In het volgende voorbeeld wordt de regel *ContosoFirewallRule verwijderd:*

```sql
EXECUTE sp_delete_firewall_rule @name = N'ContosoFirewallRule'
```

### <a name="use-powershell-to-manage-server-level-ip-firewall-rules"></a>PowerShell gebruiken om IP-firewallregels op serverniveau te beheren 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> De PowerShell Azure Resource Manager-module wordt nog steeds ondersteund door Azure SQL Database, maar alle ontwikkeling is nu voor de Az.Sql-module. Zie [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)voor deze cmdlets. De argumenten voor de opdrachten in de Az- en AzureRm-modules zijn nagenoeg identiek.

| Cmdlet | Niveau | Beschrijving |
| --- | --- | --- |
| [Get-AzSqlServerFirewallRule](/powershell/module/az.sql/get-azsqlserverfirewallrule) |server |Retourneert de huidige firewallregels op serverniveau |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) |server |Maakt een nieuwe firewallregel op serverniveau |
| [Set-azsqlserverfirewallregel instellen](/powershell/module/az.sql/set-azsqlserverfirewallrule) |server |Werkt de eigenschappen van een bestaande firewallregel op serverniveau bij |
| [Verwijder-azsqlserverfirewallregel](/powershell/module/az.sql/remove-azsqlserverfirewallrule) |server |Verwijdert firewallregels op serverniveau |

In het volgende voorbeeld wordt PowerShell gebruikt om een IP-firewallregel op serverniveau in te stellen:

```powershell
New-AzSqlServerFirewallRule -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -FirewallRuleName "ContosoIPRange" -StartIpAddress "192.168.1.0" -EndIpAddress "192.168.1.255"
```
> [!TIP]
> Geef voor $servername de servernaam op en niet de volledig gekwalificeerde DNS-naam, bijvoorbeeld **mysqldbserver** opgeven in plaats van **mysqldbserver.database.windows.net**

> [!TIP]
> Zie [DB maken - PowerShell](sql-database-powershell-samples.md) en [Maak één database en configureer een IP-firewallregel op SQL Database-serverniveau met PowerShell](scripts/sql-database-create-and-configure-database-powershell.md)voor PowerShell-voorbeelden in de context van een quickstart.

### <a name="use-cli-to-manage-server-level-ip-firewall-rules"></a>CLI gebruiken om IP-firewallregels op serverniveau te beheren

| Cmdlet | Niveau | Beschrijving |
| --- | --- | --- |
|[az sql server firewall-rule maken](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create)|server|Hiermee maakt u een server-IP-firewallregel|
|[lijst met firewallregels van az sql server](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-list)|server|Hiermee worden de IP-firewallregels op een server weergegeven|
|[az sql server firewall-rule show](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-show)|server|Toont de details van een IP-firewallregel|
|[update van de firewall-regel van AZ SQL Server](/cli/azure/sql/server/firewall-rule##az-sql-server-firewall-rule-update)|server|Een IP-firewallregel bijwerken|
|[firewall-regel voor az sql-server verwijderen](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-delete)|server|Een IP-firewallregel verwijderen|

In het volgende voorbeeld wordt CLI gebruikt om een IP-firewallregel op serverniveau in te stellen:

```azurecli-interactive
az sql server firewall-rule create --resource-group myResourceGroup --server $servername \
-n ContosoIPRange --start-ip-address 192.168.1.0 --end-ip-address 192.168.1.255
```
> [!TIP]
> Geef voor $servername de servernaam op en niet de volledig gekwalificeerde DNS-naam, bijvoorbeeld **mysqldbserver** opgeven in plaats van **mysqldbserver.database.windows.net**

> [!TIP]
> Zie [DB - Azure CLI maken](sql-database-cli-samples.md) en één database maken en een SQL Database [IP-firewallregel configureren met de Azure CLI](scripts/sql-database-create-and-configure-database-cli.md)voor een CLI-voorbeeld in de context van een quickstart.

### <a name="use-a-rest-api-to-manage-server-level-ip-firewall-rules"></a>Een REST-API gebruiken om IP-firewallregels op serverniveau te beheren

| API | Niveau | Beschrijving |
| --- | --- | --- |
| [Firewallregels weergeven](https://docs.microsoft.com/rest/api/sql/firewallrules/listbyserver) |server |Geeft de huidige IP-firewallregels op serverniveau weer |
| [Firewallregels maken of bijwerken](https://docs.microsoft.com/rest/api/sql/firewallrules/createorupdate) |server |Ip-firewallregels op serverniveau maken of bijwerken |
| [Firewallregels verwijderen](https://docs.microsoft.com/rest/api/sql/firewallrules/delete) |server |Ip-firewallregels op serverniveau verwijderen |
| [Firewallregels krijgen](https://docs.microsoft.com/rest/api/sql/firewallrules/get) | server | Krijgt IP-firewallregels op serverniveau |

## <a name="troubleshoot-the-database-firewall"></a>Problemen met de databasefirewall oplossen

Houd rekening met de volgende punten wanneer de toegang tot de SQL Database-service zich niet gedraagt zoals u verwacht.

- **Lokale firewallconfiguratie:**

  Voordat uw computer toegang heeft tot SQL Database, moet u mogelijk een firewalluitzondering maken op uw computer voor TCP-poort 1433. Als u verbindingen wilt maken binnen de Azure-cloudgrens, moet u mogelijk extra poorten openen. Zie voor meer informatie het gedeelte 'SQL Database: Outside vs inside' van [Ports na 1433 voor ADO.NET 4.5 en SQL Database.](sql-database-develop-direct-route-ports-adonet-v12.md)

- **Netwerkadresvertaling:**

  Vanwege de NAAM (Network Address Translation) kan het IP-adres dat door uw computer wordt gebruikt om verbinding te maken met SQL Database anders zijn dan het IP-adres in de IP-configuratie-instellingen van uw computer. Ga als het gaat om het IP-adres dat uw computer gebruikt om verbinding te maken met Azure:
    1. Meld u aan bij de portal.
    1. Ga naar het tabblad **Configureren** op de server die uw database host.
    1. Het **IP-adres huidige client** wordt weergegeven in de sectie Toegestane **IP-adressen.** Selecteer **Toevoegen** voor **toegestane IP-adressen** om deze computer toegang te geven tot de server.

- **Wijzigingen in de lijst met toegestane zijn nog niet van kracht:**

  Er kan maximaal vijf minuten vertraging optreden voor wijzigingen in de SQL Database-firewallconfiguratie.

- **De aanmelding is niet geautoriseerd of er is een onjuist wachtwoord gebruikt:**

  Als een aanmelding geen machtigingen heeft op de SQL Database-server of als het wachtwoord onjuist is, wordt de verbinding met de server geweigerd. Het maken van een firewall-instelling geeft clients alleen de *mogelijkheid* om verbinding te maken met uw server. De client moet nog steeds de benodigde beveiligingsreferenties verstrekken. Zie [Databasetoegang tot SQL Database en SQL Data Warehouse beheren en verlenen](sql-database-manage-logins.md)voor meer informatie over het voorbereiden van aanmeldingen.

- **Dynamisch IP-adres:**

  Als u een internetverbinding hebt die dynamische IP-adressering gebruikt en u problemen hebt om via de firewall te komen, probeert u een van de volgende oplossingen:
  
  - Vraag uw internetprovider naar het IP-adresbereik dat is toegewezen aan uw clientcomputers die toegang hebben tot de SQL Database-server. Voeg dat IP-adresbereik toe als ip-firewallregel.
  - Download in plaats daarvan statische IP-adressering voor uw clientcomputers. Voeg de IP-adressen toe als IP-firewallregels.

## <a name="next-steps"></a>Volgende stappen

- Controleer of uw bedrijfsnetwerkomgeving binnenkomende communicatie mogelijk maakt vanuit de compute IP-adresbereiken (inclusief SQL-bereiken) die worden gebruikt door de Azure-datacenters. Mogelijk moet u deze IP-adressen toevoegen aan de lijst met toegestane adressen. Zie [IP-bereiken voor Microsoft Azure-datacenters](https://www.microsoft.com/download/details.aspx?id=41653).  
- Zie [Een Azure SQL-database maken](sql-database-single-database-get-started.md)voor een snelle start over het maken van een IP-firewallregel op serverniveau.
- Zie [Client quickstart](https://msdn.microsoft.com/library/azure/ee336282.aspx)code samples naar SQL Database voor hulp bij het maken van verbinding met een Azure SQL-database vanuit open source of toepassingen van derden.
- Zie het gedeelte 'SQL Database: Outside vs inside' van [Ports na 1433 voor ADO.NET 4.5 en SQL Database voor](sql-database-develop-direct-route-ports-adonet-v12.md) informatie over extra poorten die u mogelijk moet openen.
- Zie [Uw database beveiligen](sql-database-security-overview.md)voor een overzicht van azure SQL Database-beveiliging.

<!--Image references-->
[1]: ./media/sql-database-firewall-configure/sqldb-firewall-1.png
