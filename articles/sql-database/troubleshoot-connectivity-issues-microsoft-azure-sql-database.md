---
title: Verbindings problemen oplossen met Microsoft Azure SQL Database | Microsoft Docs
description: Hierin wordt beschreven hoe u verbindings problemen in Azure SQL Database kunt oplossen.
services: sql-database
ms.service: sql-database
ms.topic: troubleshooting
author: v-miegge
ms.author: ramakoni
ms.reviewer: ''
ms.date: 09/27/2019
ms.openlocfilehash: 9de6d85e1fc54d60f999cfa18665067b3998a432
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/16/2019
ms.locfileid: "72390665"
---
# <a name="troubleshooting-connectivity-issues-with-microsoft-azure-sql-database"></a>Verbindings problemen oplossen met Microsoft Azure SQL Database

U ontvangt foutberichten wanneer het verbinden met Azure SQL Database mislukt. Deze verbindings problemen kunnen worden veroorzaakt door SQL Azure herconfiguratie van de data base, Firewall instellingen, time-out voor verbindingen of onjuiste aanmeldings gegevens. Daarnaast kunt u geen verbinding maken met Azure SQL Database als de maximum limiet van sommige Azure SQL Database resources is bereikt.

## <a name="error-40613-database--x--on-server--y--is-not-currently-available"></a>Fout 40613: data base < x > op server < y > is momenteel niet beschikbaar

**Gedetailleerde fout**

``40613: Database <DBname> on server < server name > is not currently available. Please retry the connection later. If the problem persists, contact customer support, and provide them the session tracing ID of '< Tracing ID >'.``

Los dit probleem als volgt op:

1. Raadpleeg het [dash board](https://status.azure.com/status) van de Microsoft Azure-service voor eventuele bekende storingen. 
2. Als er geen bekende storingen zijn, gaat u naar de [ondersteunings website van Microsoft Azure](http://azure.microsoft.com/support/options) om een ondersteunings aanvraag te openen.

Zie [problemen oplossen met de fout ' Data Base op server is momenteel niet beschikbaar '](https://docs.microsoft.com/azure/sql-database/sql-database-troubleshoot-common-connection-issues#troubleshoot-transient-errors)voor meer informatie.

## <a name="a-network-related-or-instance-specific-error-occurred-while-establishing-a-connection-to-sql-server"></a>Er is een netwerkgerelateerde of exemplaar-specifieke fout opgetreden bij het maken van een verbinding met SQL Server

Het probleem treedt op als de toepassing geen verbinding kan maken met de server.

U kunt dit probleem oplossen door de stappen (in de weer gegeven volg orde) in de [stappen voor het oplossen van veelvoorkomende problemen met de verbinding te](#steps-to-fix-common-connection-issues) verhelpen.

## <a name="the-server-was-not-found-or-was-not-accessible-errors-26-40-10053"></a>De server is niet gevonden of niet toegankelijk (fouten 26, 40, 10053)

### <a name="error-26-error-locating-serverinstance-specified"></a>Fout 26: er is een fout opgetreden bij het zoeken naar de opgegeven server/instantie

**Gedetailleerde fout**

``System.Data.SqlClient.SqlException: A network-related or instance-specific error occurred while establishing a connection to SQL Server. The server was not found or was not accessible. Verify that the instance name is correct and that SQL Server is configured to allow remote connections.(provider: SQL Network Interfaces, error: 26 – Error Locating Server/Instance Specified)``

### <a name="error-40-could-not-open-a-connection-to-sql-server"></a>Fout 40: kan geen verbinding met SQL Server openen

**Gedetailleerde fout**

``A network-related or instance-specific error occurred while establishing a connection to SQL Server. The server was not found or was not accessible. Verify that the instance name is correct and that SQL Server is configured to allow remote connections. (provider: Named Pipes Provider, error: 40 - Could not open a connection to SQL Server)``

### <a name="error-10053-a-transport-level-error-has-occurred-when-receiving-results-from-the-server"></a>Fout 10053: er is een fout op transport niveau opgetreden bij het ontvangen van resultaten van de server

**Gedetailleerde fout**

``10053: A transport-level error has occurred when receiving results from the server. (Provider: TCP Provider, error: 0 - An established connection was aborted by the software in your host machine)``

Deze fouten treden op omdat de toepassing geen verbinding kan maken met de server.

U kunt dit probleem oplossen door de stappen (in de weer gegeven volg orde) in de [stappen voor het oplossen van veelvoorkomende problemen met de verbinding te](#steps-to-fix-common-connection-issues) verhelpen.

## <a name="cannot-connect-to-servername-due-to-firewall-issues"></a>Kan geen verbinding maken met <servername> vanwege problemen met de firewall

### <a name="error-40615-cannot-connect-to--servername-"></a>Fout 40615: er kan geen verbinding worden gemaakt met < ServerName >

U kunt dit probleem oplossen door [firewall instellingen op SQL database te configureren via de Azure Portal.](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)

### <a name="error-5-cannot-connect-to--servername-"></a>Fout 5: er kan geen verbinding worden gemaakt met < ServerName >

U kunt dit probleem oplossen door ervoor te zorgen dat poort 1433 is geopend voor uitgaande verbindingen op alle firewalls tussen de client en Internet.

Zie [Configure the Windows firewall to allow SQL Server Access](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)(Engelstalig) voor meer informatie.

## <a name="unable-to-log-in-to-the-server-errors-18456-40531"></a>Kan niet aanmelden bij de server (fouten 18456, 40531)

### <a name="login-failed-for-user--user-name-"></a>De aanmelding is mislukt voor de gebruiker ' < gebruikers naam > '

**Gedetailleerde fout**

``Login failed for user '<User name>'.This session has been assigned a tracing ID of '<Tracing ID>'. Provide this tracing ID to customer support when you need assistance. (Microsoft SQL Server, Error: 18456)``

Als u dit probleem wilt oplossen, neemt u contact op met de service beheerder om u een geldige SQL Server gebruikers naam en wacht woord op te geven.

De service beheerder kan doorgaans de volgende stappen gebruiken om de aanmeldings referenties toe te voegen:

1. Meld u aan bij de server met behulp van SQL Server Management Studio (SSMS).
2. Voer de volgende SQL-query uit om te controleren of de aanmeldings naam is uitgeschakeld:

   ```
   SELECT name, is_disabled FROM sys.sql_logins
   ```

3. Als de bijbehorende naam is uitgeschakeld, schakelt u deze in met behulp van de volgende instructie: 

   ```
   Alter login <User name> enable
   ```

4. Als de gebruikers naam voor SQL-aanmelding niet bestaat, maakt u deze door de volgende stappen uit te voeren:

   1. In SSMS dubbelklikt u op **beveiliging** om het uit te vouwen. 
   2. Klik met de rechtermuisknop op **Aanmeldingen**, en selecteer vervolgens **Nieuwe aanmelding**. 
   3. Bewerk en voer de volgende SQL-query uit in het gegenereerde script met tijdelijke aanduidingen:
 
   ```
   CREATE LOGIN <SQL_login_name, sysname, login_name>
   WITH PASSWORD = ‘<password, sysname, Change_Password>’
   GO
   ```       
5. Dubbelklik op **database**. 
6. Selecteer de data base waaraan u de gebruiker toestemming wilt verlenen.
7. Dubbelklik op **Beveiliging**. 
8. Klik met de rechtermuisknop op **Gebruikers**, en selecteer vervolgens **Nieuwe gebruiker**. 
9. Bewerk en voer de volgende SQL-query uit in het gegenereerde script met tijdelijke aanduidingen: 

   ```
   CREATE USER <user_name, sysname, user_name>          
   FOR LOGIN <login_name, sysname, login_name>
   WITH DEFAULT_SCHEMA = <default_schema, sysname, dbo>
   GO
   
   -- Add user to the database owner role

   EXEC sp_addrolemember N’db_owner’, N’<user_name, sysname, user_name>’
   GO
   ```
   
   > [!NOTE]
   > U kunt ook `sp_addrolemember` gebruiken om specifieke gebruikers toe te wijzen aan specifieke database rollen.

Zie [data bases en aanmeldingen beheren in Azure SQL database](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins)voor meer informatie.

## <a name="connection-timeout-expired-errors"></a>Time-out tijdens verbindingstime-out

### <a name="systemdatasqlclientsqlexception-0x80131904-connection-timeout-expired"></a>System. data. SqlClient. SqlException (0x80131904): time-out voor verbinding verlopen

**Gedetailleerde fout**

``System.Data.SqlClient.SqlException (0x80131904): Connection Timeout Expired. The timeout period elapsed while attempting to consume the pre-login handshake acknowledgement. This could be because the pre-login handshake failed or the server was unable to respond back in time. The duration spent while attempting to connect to this server was - [Pre-Login] initialization=3; handshake=29995;``

### <a name="systemdatasqlclientsqlexception-0x80131904-timeout-expired"></a>System. data. SqlClient. SqlException (0x80131904): time-out verlopen

**Gedetailleerde fout**

``System.Data.SqlClient.SqlException (0x80131904): Timeout expired. The timeout period elapsed prior to completion of the operation or the server is not responding.``

### <a name="systemdataentitycoreentityexception-the-underlying-provider-failed-on-open"></a>System. data. entity. core. EntityException: de onderliggende provider is niet geopend

**Gedetailleerde fout**

``System.Data.Entity.Core.EntityException: The underlying provider failed on Open. -> System.Data.SqlClient.SqlException: Timeout expired. The timeout period elapsed prior to completion of the operation or the server is not responding. -> System.ComponentModel.Win32Exception: The wait operation timed out``

### <a name="cannot-connect-to--server-name-"></a>Kan geen verbinding maken met < server naam >

**Gedetailleerde fout**

``Cannot connect to <server name>.ADDITIONAL INFORMATION:Connection Timeout Expired. The timeout period elapsed during the post-login phase. The connection could have timed out while waiting for server to complete the login process and respond; Or it could have timed out while attempting to create multiple active connections. The duration spent while attempting to connect to this server was - [Pre-Login] initialization=231; handshake=983; [Login] initialization=0; authentication=0; [Post-Login] complete=13000; (Microsoft SQL Server, Error: -2) For help, click: http://go.microsoft.com/fwlink?ProdName=Microsoft%20SQL%20Server&EvtSrc=MSSQLServer&EvtID=-2&LinkId=20476 The wait operation timed out``

Deze uitzonde ringen kunnen worden veroorzaakt door verbindings-of query problemen. Zie [bevestigen of een fout wordt veroorzaakt door een verbindings probleem](#confirm-whether-an-error-is-caused-by-a-connectivity-issue)om te controleren of deze fout wordt veroorzaakt door verbindings problemen.

Verbindingstime-outs doen zich voor omdat de toepassing geen verbinding kan maken met de server. U kunt dit probleem oplossen door de stappen (in de weer gegeven volg orde) in de [stappen voor het oplossen van veelvoorkomende problemen met de verbinding te](#steps-to-fix-common-connection-issues) verhelpen.

## <a name="transient-errors-errors-40197-40545"></a>Tijdelijke fouten (fouten 40197, 40545)

### <a name="error-40197-the-service-has-encountered-an-error-processing-your-request-please-try-again-error-code--code-"></a>Fout 40197: de service heeft een fout aangetroffen tijdens het verwerken van uw aanvraag. Probeer het opnieuw. Fout code < code >

Dit probleem treedt op vanwege een tijdelijke fout die is opgetreden tijdens een herconfiguratie of failover op de back-end.

U kunt dit probleem oplossen door een korte periode te wachten en opnieuw te proberen. Er is geen ondersteunings aanvraag vereist tenzij het probleem zich blijft voordoen.

Als best practice, moet u ervoor zorgen dat logica voor nieuwe pogingen is ingesteld. Zie [problemen met tijdelijke fouten en verbindings SQL database fouten oplossen](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-issues)voor meer informatie over de logica voor opnieuw proberen.

## <a name="connection-terminated-because-of-a-system-defined-limit"></a>De verbinding is verbroken vanwege een door het systeem gedefinieerde limiet

### <a name="error-10928-resource-id-d"></a>Fout 10928: Resource-ID:% d

**Gedetailleerde fout**

``10928: Resource ID: %d. The %s limit for the database is %d and has been reached. See http://go.microsoft.com/fwlink/?LinkId=267637 for assistance. The Resource ID value in error message indicates the resource for which limit has been reached. For sessions, Resource ID = 2.``

Ga op een van de volgende manieren te werk om dit probleem op te lossen:

* Controleer of er langlopende query's zijn.

  > [!NOTE]
  > Dit is een minimale aanpak waarmee het probleem mogelijk niet kan worden opgelost.

  1. Voer de volgende SQL-query uit om de weer gave [sys. DM _exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) te controleren om blokkerings aanvragen weer te geven:

             ```
             SELECT * FROM dm_exec_requests
             ```

  2. Bepaal de **invoer buffer** voor de hoofd blok kering.
  3. Stem de hoofd blok-query af.

    Voor een uitgebreide probleemoplossings procedure, Zie [is mijn query wordt in de Cloud verfijnd?](http://blogs.msdn.com/b/sqlblog/archive/2013/11/01/is-my-query-running-fine-in-the-cloud.aspx).

* Als de data base consequent de limiet heeft bereikt ondanks het blok keren van blokkeringen en langlopende query's, kunt u overwegen om een upgrade uit te voeren naar een van de nieuwe Preview-versies (zoals [Standard of Premium Edition](https://azure.microsoft.com/pricing/details/sql-database/)).

Zie [Azure SQL database prijzen](https://azure.microsoft.com/pricing/details/sql-database/single/)voor meer informatie over SQL database prijs opties.

Zie [dynamische systeem beheer weergaven](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views)voor meer informatie over dynamische beheer weergaven.

Zie voor meer informatie over dit fout bericht [SQL database resource limieten voor Azure SQL database-server](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server).

### <a name="error-10929-resource-id-1"></a>Fout 10929: Resource-ID: 1

**Gedetailleerde fout**

``10929: Resource ID: 1. The %s minimum guarantee is %d, maximum limit is %d and the current usage for the database is %d. However, the server is currently too busy to support requests greater than %d for this database. See http://go.microsoft.com/fwlink/?LinkId=267637 for assistance. Otherwise, please try again later.``

Raadpleeg voor meer informatie over deze fout [fout berichten voor SQL database-client Programma's](https://docs.microsoft.com/azure/sql-database/sql-database-develop-error-messages).

### <a name="error-40501-the-service-is-currently-busy"></a>Fout 40501: de service is momenteel bezet

**Gedetailleerde fout**

``40501: The service is currently busy. Retry the request after 10 seconds. Incident ID: %ls. Code: %d.``

Dit is een beperkings fout van de engine, een indicatie dat de resource limieten worden overschreden.

Zie [resource limieten voor Data Base Server](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server)voor meer informatie over resource limieten.

### <a name="error-40544-the-database-has-reached-its-size-quota"></a>Fout 40544: de grootte van de data base is bereikt

**Gedetailleerde fout**

``40544: The database has reached its size quota. Partition or delete data, drop indexes, or consult the documentation for possible resolutions. Incident ID: <ID>. Code: <code>.``

Deze fout treedt op wanneer de data base het quotum voor de grootte heeft bereikt.

U kunt de volgende stappen gebruiken om het probleem op te lossen of om extra opties te bieden:

1. Controleer de huidige grootte van de data base met behulp van het dash board in de Azure Portal.

   > [!NOTE]
   > Voer de volgende SQL-query uit om te bepalen welke tabellen de meeste ruimte gebruiken en daarom potentiële kandidaten voor opschoning hebben:

   ```
   SELECT o.name,
    a.SUM(p.row_count) AS 'Row Count',
    b.SUM(p.reserved_page_count) * 8.0 / 1024 AS 'Table Size (MB)'
   FROM sys.objects o
   JOIN sys.dm_db_partition_stats p on p.object_id = o.object_id
   GROUP BY o.name
   ORDER BY [Table Size (MB)] DESC
   ```

2. Als de huidige grootte niet groter is dan de maximale grootte die voor uw editie wordt ondersteund, kunt u ALTER Data Base gebruiken om de waarde voor MAXSIZE te verhogen. 
3. Als de data base al de Maxi maal ondersteunde grootte voor uw versie overschrijdt, kunt u een of meer van de volgende stappen uitvoeren:
   - Normale activiteiten voor het opschonen van data bases uitvoeren. U kunt bijvoorbeeld de ongewenste gegevens opschonen met behulp van afkap ping/verwijderen of gegevens verplaatsen met behulp van SQL Server Integration Services (SSIS) of het hulp programma voor het bulksgewijs kopiëren van Program ma's (BCP).
   - Partitioneer of verwijder gegevens, verwijder indexen of Raadpleeg de documentatie voor mogelijke oplossingen.

   - Zie voor het schalen van de Data Base [afzonderlijke database resources schalen](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-scale) en [bronnen voor elastische Pools schalen](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool-scale).

### <a name="error-40549-session-is-terminated-because-you-have-a-long-running-transaction"></a>Fout 40549: de sessie is beëindigd omdat u een langlopende trans actie hebt

**Gedetailleerde fout**

``40549: Session is terminated because you have a long-running transaction. Try shortening your transaction.``

Als u deze fout herhaaldelijk ondervindt, kunt u proberen om het probleem op te lossen door de volgende stappen uit te voeren: 

1. Controleer de weer gave sys. DM _exec_requests om geopende sessies met een hoge waarde voor de kolom total_elapsed_time te bekijken. Voer deze controle uit door het volgende SQL-script uit te voeren:

   ```
   SELECT * FROM dm_exec_requests
   ```
2. Bepaal de invoer buffer voor de langlopende query. 
3. De query afstemmen.

U kunt ook uw query's batcheren. Zie voor meer informatie over batch verwerking [batch verwerking gebruiken om de prestaties van SQL database-toepassingen te verbeteren](https://docs.microsoft.com/azure/sql-database/sql-database-use-batching-to-improve-performance).

Voor een uitgebreide probleemoplossings procedure, Zie [is mijn query wordt in de Cloud verfijnd?](http://blogs.msdn.com/b/sqlblog/archive/2013/11/01/is-my-query-running-fine-in-the-cloud.aspx).

### <a name="error-40551-the-session-has-been-terminated-because-of-excessive-tempdb-usage"></a>Fout 40551: de sessie is beëindigd vanwege het overmatige gebruik van TEMPDB

**Gedetailleerde fout**

``40551: The session has been terminated because of excessive TEMPDB usage. Try modifying your query to reduce the temporary table space usage.``

Voer de volgende stappen uit om dit probleem te omzeilen:

1. Wijzig de query's om het gebruik van tijdelijke tabel ruimte te verminderen. 
2. Tijdelijke objecten verwijderen nadat ze niet meer nodig zijn. 
3. Tabellen afkappen of ongebruikte tabellen verwijderen.

### <a name="error-40552-the-session-has-been-terminated-because-of-excessive-transaction-log-space-usage"></a>Fout 40552: de sessie is beëindigd vanwege het overmatige gebruik van het transactie logboek

**Gedetailleerde fout**

``40552: The session has been terminated because of excessive transaction log space usage. Try modifying fewer rows in a single transaction.``

Probeer de volgende methoden om dit op te lossen:

* Het probleem kan worden veroorzaakt door invoeg-, update-of verwijderings bewerkingen. Probeer het aantal rijen dat onmiddellijk wordt geëxploiteerd, te verminderen door batches te implementeren of te splitsen in meerdere kleinere trans acties.
* Het probleem kan zich voordoen als gevolg van het opnieuw opbouwen van de index. U kunt dit probleem omzeilen door te controleren of het aantal rijen dat in de tabel wordt beïnvloed * (gemiddelde grootte van het veld dat wordt bijgewerkt in bytes + 80) < 2 gigabytes (GB).

  > [!NOTE]
  > Voor een opnieuw opgebouwde index moet de gemiddelde grootte van het veld dat wordt bijgewerkt, worden vervangen door de gemiddelde index grootte.

### <a name="error-40553-the-session-has-been-terminated-because-of-excessive-memory-usage"></a>Fout 40553: de sessie is beëindigd vanwege het overmatige geheugen gebruik

**Gedetailleerde fout**

``40553 : The session has been terminated because of excessive memory usage. Try modifying your query to process fewer rows.``

U kunt dit probleem omzeilen door de query te optimaliseren.

Voor een uitgebreide probleemoplossings procedure, Zie [is mijn query wordt in de Cloud verfijnd?](http://blogs.msdn.com/b/sqlblog/archive/2013/11/01/is-my-query-running-fine-in-the-cloud.aspx).


### <a name="cannot-open-database-master-requested-by-the-login-the-login-failed"></a>Kan de data base ' Master ' die door de aanmelding is aangevraagd, niet openen. De aanmelding is mislukt.

Dit probleem treedt op omdat het account geen machtiging heeft voor toegang tot de hoofd database. SQL Server Management Studio (SSMS) probeert standaard verbinding te maken met de hoofd database.

Volg deze stappen om dit probleem op te lossen:

1. Selecteer **Opties**in het aanmeldings scherm van SSMS en selecteer vervolgens **verbindings eigenschappen**. 
2. Voer in het veld **verbinding maken met data base** de naam van de standaard database van de gebruiker in als de standaard aanmeldings database en selecteer vervolgens **verbinding maken**.

   ![Verbindingseigenschappen](media/troubleshoot-connectivity-issues-microsoft-azure-sql-database/cannot-open-database-master.png)

## <a name="confirm-whether-an-error-is-caused-by-a-connectivity-issue"></a>Controleren of een fout wordt veroorzaakt door een verbindings probleem

Als u wilt controleren of een fout wordt veroorzaakt door een probleem met de verbinding, bekijkt u de stack tracering voor frames die aanroepen weer geven om een verbinding te openen, zoals de volgende: (Let op de verwijzing naar de klasse **SqlConnection** ).

```
System.Data.SqlClient.SqlConnection.TryOpen(TaskCompletionSource`1 retry)
 at System.Data.SqlClient.SqlConnection.Open()
 at AzureConnectionTest.Program.Main(String[] args)
ClientConnectionId:<Client connection ID>
```

Wanneer de uitzonde ring wordt geactiveerd door query problemen, ziet u een aanroep stack die er ongeveer als volgt uitzien (Let op de verwijzing naar de klasse **SqlCommand** ). In dit geval kunt [u uw query's afstemmen](http://blogs.msdn.com/b/sqlblog/archive/2013/11/01/is-my-query-running-fine-in-the-cloud.aspx).

```
  at System.Data.SqlClient.SqlCommand.ExecuteReader()
  at AzureConnectionTest.Program.Main(String[] args)
  ClientConnectionId:<Client ID>
```
Raadpleeg de volgende bronnen voor meer informatie over de prestaties van fijnafstelling:

* [Azure SQL-indexen en-statistieken onderhouden](https://techcommunity.microsoft.com/t5/Azure-Database-Support-Blog/How-to-maintain-Azure-SQL-Indexes-and-Statistics/ba-p/368787)
* [De prestaties van de query in Azure SQL Database hand matig afstemmen](https://docs.microsoft.com/azure/sql-database/sql-database-performance-guidance)
* [Prestaties bewaken Azure SQL Database door dynamische beheer weergaven te gebruiken](https://docs.microsoft.com/azure/sql-database/sql-database-monitoring-with-dmvs)
* [Het query archief in Azure SQL Database gebruiken](https://docs.microsoft.com/azure/sql-database/sql-database-operate-query-store)


## <a name="steps-to-fix-common-connection-issues"></a>Stappen voor het oplossen van veelvoorkomende verbindings problemen

1. Zorg ervoor dat TCP/IP is ingeschakeld als een client protocol op de toepassings server. Zie [client protocollen configureren](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-client-protocols)voor meer informatie. Op toepassings servers waarop u geen SQL Server-hulpprogram ma's hebt geïnstalleerd, controleert u of TCP/IP is ingeschakeld door **cliconfg. exe** (SQL Server Client Network Utility) uit te voeren. 
2. Controleer de connection string van de toepassing om te controleren of deze correct is geconfigureerd. Zorg er bijvoorbeeld voor dat de connection string de juiste poort (1433) en de volledig gekwalificeerde server naam opgeeft.
Zie [SQL Server verbindings gegevens ophalen](https://docs.microsoft.com/azure/sql-database/sql-database-connect-query-ssms#get-sql-server-connection-information).
3. Probeer de time-outwaarde voor de verbinding te verhogen. Het is raadzaam om een time-out voor de verbinding van ten minste 30 seconden te gebruiken. 
4. Test de connectiviteit tussen de toepassings server en de Azure-SQL database met behulp van [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/azure/sql-database/sql-database-connect-query-ssms), een udl-bestand, ping of Telnet. Zie [problemen met SQL Server connectiviteit](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server) en [Diagnostische gegevens over verbindings problemen](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-issues#diagnostics)oplossen voor meer informatie.

   > [!NOTE]
   > Als stap voor het oplossen van problemen kunt u ook de connectiviteit op een andere client computer testen.

5. Zorg ervoor dat de logica voor opnieuw proberen is ingesteld als best practice. Zie [problemen met tijdelijke fouten en verbindings SQL database fouten oplossen](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-issues)voor meer informatie over logica voor opnieuw proberen.

Als u met deze stappen het probleem niet kunt oplossen, kunt u proberen om meer gegevens te verzamelen en vervolgens contact op te nemen met de ondersteuning. Als uw toepassing een Cloud service is, schakelt u logboek registratie in. Deze stap retourneert een UTC-tijds tempel van de fout. Daarnaast retourneert SQL Azure de tracerings-ID. [Micro soft Customer Support Services](http://azure.microsoft.com/support/options/) kan deze informatie gebruiken. 

Zie [logboek registratie van diagnostische gegevens inschakelen voor apps in azure app service](https://azure.microsoft.com/documentation/articles/web-sites-enable-diagnostic-log/)voor meer informatie over het inschakelen van logboek registratie.

**Gerelateerde documenten**

* [Architectuur van Azure SQL-connectiviteit](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-architecture)<br>
* [Netwerk toegangs beheer van Azure SQL Database en Data Warehouse](https://docs.microsoft.com/azure/sql-database/sql-database-networkaccess-overview)
