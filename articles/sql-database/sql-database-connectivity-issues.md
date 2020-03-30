---
title: Werken met tijdelijke fouten
description: Meer informatie over het oplossen, diagnosticeren en voorkomen van een SQL-verbindingsfout of tijdelijke fout in Azure SQL Database.
keywords: sql-verbinding, verbindingstekenreeks, verbindingsproblemen, tijdelijke fout, verbindingsfout
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dalechen
manager: dcscontentpm
ms.author: ninarn
ms.reviewer: carlrab, vanto
ms.date: 01/14/2020
ms.openlocfilehash: d2b56e259f551f7655936c975a7a864a27a1df79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79269079"
---
# <a name="troubleshooting-transient-connection-errors-to-sql-database"></a>Tijdelijke verbindingsfouten oplossen in SQL-database

In dit artikel wordt beschreven hoe u verbindingsfouten en tijdelijke fouten die uw clienttoepassing tegenkomt wanneer deze samenwerkt met Azure SQL Database, voorkomen, oplossen, diagnosticeren en beperken. Meer informatie over het configureren van logica voor het opnieuw proberen, het samenstellen van de verbindingstekenreeks en het aanpassen van andere verbindingsinstellingen.

<a id="i-transient-faults" name="i-transient-faults"></a>

## <a name="transient-errors-transient-faults"></a>Tijdelijke fouten (tijdelijke fouten)

Een tijdelijke fout, ook wel een tijdelijke fout genoemd, heeft een onderliggende oorzaak die zichzelf snel oplost. Een incidentele oorzaak van tijdelijke fouten is wanneer het Azure-systeem snel hardwarebronnen verschuift naar een betere load-balance verschillende workloads. De meeste van deze herconfiguratiegebeurtenissen eindigen in minder dan 60 seconden. Tijdens deze tijdspanne voor de configuratie u verbindingsproblemen hebben met SQL Database. Toepassingen die verbinding maken met SQL Database moeten worden gebouwd om deze tijdelijke fouten te verwachten. Om ze te behandelen, implementeert u logica opnieuw proberen in hun code in plaats van ze aan gebruikers te laten zien als toepassingsfouten.

Als uw clientprogramma ADO.NET gebruikt, wordt uw programma verteld over de tijdelijke fout door het gooien van **SqlException.** 

<a id="connection-versus-command" name="connection-versus-command"></a>

### <a name="connection-vs-command"></a>Verbinding versus opdracht

Probeer de SQL-verbinding opnieuw of maak deze opnieuw tot stand, afhankelijk van het volgende:

- **Er treedt een tijdelijke fout op tijdens een verbindingspoging**

Na een vertraging van enkele seconden probeert u de verbinding opnieuw.

- **Er treedt een tijdelijke fout op tijdens een SQL-queryopdracht**

Probeer de opdracht niet onmiddellijk opnieuw. In plaats daarvan, na een vertraging, vers vast te stellen van de verbinding. Probeer vervolgens het commando opnieuw.

<a id="j-retry-logic-transient-faults" name="j-retry-logic-transient-faults"></a>

## <a name="retry-logic-for-transient-errors"></a>Pogingslogica voor tijdelijke problemen

Clientprogramma's die af en toe een tijdelijke fout tegenkomen, zijn robuuster wanneer ze logica voor nieuwe oplossingen bevatten. Wanneer uw programma communiceert met SQL Database via middleware van derden, vraagt u de leverancier of de middleware logica opnieuw probeert voor tijdelijke fouten.

<a id="principles-for-retry" name="principles-for-retry"></a>

### <a name="principles-for-retry"></a>Principes voor het opnieuw proberen

- Als de fout van voorbijgaande aard is, probeert u opnieuw een verbinding te openen.
- Probeer niet direct een `SELECT` SQL-instructie opnieuw die is mislukt met een tijdelijke fout. In plaats daarvan, maak een nieuwe `SELECT`verbinding, en probeer vervolgens de .
- Wanneer een `UPDATE` SQL-instructie mislukt met een tijdelijke fout, maakt u een nieuwe verbinding voordat u de update opnieuw probeert. De logica voor opnieuw proberen moet ervoor zorgen dat de volledige databasetransactie is voltooid of dat de hele transactie wordt teruggedraaid.

### <a name="other-considerations-for-retry"></a>Andere overwegingen voor het opnieuw proberen

- Een batch programma dat automatisch begint na de werkuren en eindigt voor de ochtend kan veroorloven om zeer geduldig met lange tijdintervallen tussen de pogingen om opnieuw te proberen.
- Een user interface programma moet rekening houden met de menselijke neiging om op te geven na te lang wachten. De oplossing mag niet om de paar seconden opnieuw worden geprobeerd, omdat dat beleid het systeem kan overspoelen met verzoeken.

### <a name="interval-increase-between-retries"></a>Intervalverhoging tussen nieuwe pogingen

We raden u aan 5 seconden te wachten voordat u het voor het eerst opnieuw probeert. Opnieuw proberen na een vertraging korter dan 5 seconden dreigt de cloudservice te overweldigen. Voor elke volgende poging, moet de vertraging exponentieel groeien, tot een maximum van 60 seconden.

Zie [SQL Server connection pooling (ADO.NET)](https://msdn.microsoft.com/library/8xx3tyca.aspx)voor een discussie over de blokkeringsperiode voor clients die ADO.NET gebruiken.

U ook een maximum aantal nieuwe pogingen instellen voordat het programma zichzelf beëindigt.

### <a name="code-samples-with-retry-logic"></a>Codevoorbeelden met logica voor nieuwe try

Codevoorbeelden met logica voor nieuwe try zijn beschikbaar op:

- [Flexibel verbinding maken met SQL via ADO.NET][step-4-connect-resiliently-to-sql-with-ado-net-a78n]
- [Flexibel verbinding maken met SQL via PHP][step-4-connect-resiliently-to-sql-with-php-p42h]

<a id="k-test-retry-logic" name="k-test-retry-logic"></a>

### <a name="test-your-retry-logic"></a>Uw logica voor nieuwe try testen

Als u uw logica voor nieuwe try wilt testen, moet u een fout simuleren of veroorzaken die kan worden gecorrigeerd terwijl uw programma nog steeds wordt uitgevoerd.

#### <a name="test-by-disconnecting-from-the-network"></a>Testen door de verbinding met het netwerk te verbreken

Een manier waarop u uw logica voor nieuwe apparaten testen, is door uw clientcomputer los te koppelen van het netwerk terwijl het programma wordt uitgevoerd. De fout is:

- **SqlException.Number** = 11001
- Bericht: "Zo'n host is niet bekend"

Als onderdeel van de eerste poging opnieuw proberen, u uw clientcomputer opnieuw verbinden met het netwerk en vervolgens proberen verbinding te maken.

Als u deze test praktisch wilt maken, trekt u de stekker uit uw computer van het netwerk voordat u uw programma start. Vervolgens herkent uw programma een runtime-parameter die ervoor zorgt dat het programma:

- Voeg tijdelijk 11001 toe aan de lijst met fouten die als tijdelijk moeten worden beschouwd.
- Probeer de eerste verbinding zoals gewoonlijk.
- Nadat de fout is gevangen, verwijdert u 11001 uit de lijst.
- Geef een bericht weer waarin de gebruiker wordt opgestuurd om de computer op het netwerk aan te sluiten.
- Pauzeer verdere uitvoering met de methode **Console.ReadLine** of een dialoogvenster met een OK-knop. De gebruiker drukt op de Enter-toets nadat de computer is aangesloten op het netwerk.
- Probeer opnieuw verbinding te maken, verwacht succes.

#### <a name="test-by-misspelling-the-database-name-when-connecting"></a>Testen door de databasenaam verkeerd te spellen bij het verbinden

Uw programma kan de gebruikersnaam opzettelijk verkeerd spellen vóór de eerste verbindingspoging. De fout is:

- **SqlException.Number** = 18456
- Bericht: 'Inloggen is mislukt voor gebruiker 'WRONG_MyUserName'."

Als onderdeel van de eerste poging tot het opnieuw proberen kan uw programma de spelfout corrigeren en vervolgens proberen verbinding te maken.

Om deze test praktisch te maken, herkent uw programma een runtime-parameter die ervoor zorgt dat het programma:

- Voeg tijdelijk 18456 toe aan de lijst met fouten die als tijdelijk moeten worden beschouwd.
- Voeg met opzet 'WRONG_' toe aan de gebruikersnaam.
- Nadat de fout is gevangen, verwijdert u 18456 uit de lijst.
- Verwijder 'WRONG_' van de gebruikersnaam.
- Probeer opnieuw verbinding te maken, verwacht succes.

<a id="net-sqlconnection-parameters-for-connection-retry" name="net-sqlconnection-parameters-for-connection-retry"></a>

## <a name="net-sqlconnection-parameters-for-connection-retry"></a>.NET SqlConnection-parameters voor het opnieuw proberen van verbindingen

Als uw clientprogramma verbinding maakt met SQL Database met behulp van de .NET Framework class **System.Data.SqlClient.SqlConnection,** gebruikt u .NET 4.6.1 of hoger (of .NET Core) zodat u de functie voor het opnieuw proberen van de verbinding gebruiken. Zie [deze webpagina](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlconnection)voor meer informatie over de functie.

<!--
2015-11-30, FwLink 393996 points to dn632678.aspx, which links to a downloadable .docx related to SqlClient and SQL Server 2014.
-->

Wanneer u de [verbindingstekenreeks](https://msdn.microsoft.com/library/System.Data.SqlClient.SqlConnection.connectionstring.aspx) voor uw **SqlConnection-object** maakt, coördineert u de waarden onder de volgende parameters:

- **ConnectRetryCount**&nbsp;&nbsp;: Standaard is 1. Bereik is 0 tot en met 255.
- **ConnectRetryInterval**&nbsp;&nbsp;: Standaard is 10 seconden. Bereik is 1 tot en met 60.
- **Verbindingstime-out**:&nbsp;&nbsp;Standaard is 15 seconden. Bereik is 0 tot en met 2147483647.

In het bijzonder moeten de door u gekozen waarden de volgende gelijkheid waar maken: Connection Timeout = ConnectRetryCount * ConnectionRetryInterval

Als de telling bijvoorbeeld gelijk is aan 3 en het interval gelijk is aan 10 seconden, geeft een time-out van slechts 29 seconden het systeem niet genoeg tijd voor de derde en laatste poging om verbinding te maken: 29 < 3 * 10.

<a id="connection-versus-command" name="connection-versus-command"></a>

## <a name="connection-vs-command"></a>Verbinding versus opdracht

Met de parameters **ConnectRetryCount** en **ConnectRetryInterval** kunnen uw **SqlConnection-object** de connect-bewerking opnieuw proberen zonder uw programma te vertellen of lastig te vallen, zoals het retourneren van de besturing naar uw programma. De nieuwe pogingen kunnen in de volgende situaties voorkomen:

- mySqlConnection.Open-methodeaanroep
- mySqlConnection.Execute-methodeaanroep

Er is een subtiliteit. Als er een tijdelijke fout optreedt tijdens het uitvoeren van uw *query,* probeert het **sqlconnection-object** de verbindingsbewerking niet opnieuw. Het is zeker niet opnieuw proberen uw zoekopdracht. **SqlConnection** controleert echter zeer snel de verbinding voordat u uw query verzendt voor uitvoering. Als bij de snelle controle een verbindingsprobleem wordt **gedetecteerd,** wordt de verbindingsbewerking opnieuw geprobeerd. Als de nieuwe poging slaagt, wordt uw query verzonden voor uitvoering.

### <a name="should-connectretrycount-be-combined-with-application-retry-logic"></a>Moet ConnectRetryCount worden gecombineerd met logica voor het opnieuw proberen van toepassingen

Stel dat uw toepassing robuuste aangepaste logica voor het opnieuw proberen heeft. Het kan de connect-bewerking vier keer opnieuw proberen. Als u **ConnectRetryInterval** en **ConnectRetryCount** =3 aan uw verbindingstekenreeks toevoegt, verhoogt u het aantal nieuwe pogingen tot 4 * 3 = 12 nieuwe pogingen. Je zou niet van plan zo'n hoog aantal nieuwe pogingen.

<a id="a-connection-connection-string" name="a-connection-connection-string"></a>

## <a name="connections-to-sql-database"></a>Verbindingen met SQL-database

<a id="c-connection-string" name="c-connection-string"></a>

### <a name="connection-connection-string"></a>Verbinding: verbindingstekenreeks

De verbindingstekenreeks die nodig is om verbinding te maken met SQL Database verschilt iets van de tekenreeks die wordt gebruikt om verbinding te maken met SQL Server. U de verbindingstekenreeks voor uw database kopiëren vanuit de [Azure-portal.](https://portal.azure.com/)

[!INCLUDE [sql-database-include-connection-string-20-portalshots](../../includes/sql-database-include-connection-string-20-portalshots.md)]

<a id="b-connection-ip-address" name="b-connection-ip-address"></a>

### <a name="connection-ip-address"></a>Verbinding: IP-adres

U moet de SQL Database-server configureren om communicatie te accepteren vanaf het IP-adres van de computer die uw clientprogramma host. Als u deze configuratie wilt instellen, bewerkt u de firewall-instellingen via de [Azure-portal.](https://portal.azure.com/)

Als u vergeet het IP-adres te configureren, mislukt uw programma met een handig foutbericht waarin het benodigde IP-adres staat.

[!INCLUDE [sql-database-include-ip-address-22-portal](../../includes/sql-database-include-ip-address-22-v12portal.md)]

Zie [Firewall-instellingen configureren in SQL Database voor](sql-database-configure-firewall-settings.md)meer informatie.
<a id="c-connection-ports" name="c-connection-ports"></a>

### <a name="connection-ports"></a>Verbinding: poorten

Doorgaans moet u ervoor zorgen dat alleen poort 1433 open staat voor uitgaande communicatie op de computer die uw clientprogramma host.

Wanneer uw clientprogramma bijvoorbeeld wordt gehost op een Windows-computer, u Windows Firewall op de host gebruiken om poort 1433 te openen.

1. Open het Configuratiescherm.
2. Selecteer **Alle Onderdelen van het Configuratiescherm** > Windows**Firewall** > **Advanced Settings** > **Uitgaande regels** > **Acties** > **Nieuwe regel**.

Als uw clientprogramma wordt gehost op een virtuele Azure-machine (VM), leest u [Poorten na 1433 voor ADO.NET 4.5 en SQL Database.](sql-database-develop-direct-route-ports-adonet-v12.md)

Zie [Azure SQL Database firewall](sql-database-firewall-configure.md)voor achtergrondinformatie over de configuratie van poorten en IP-adressen.

<a id="d-connection-ado-net-4-5" name="d-connection-ado-net-4-5"></a>

### <a name="connection-adonet-462-or-later"></a>Aansluiting: ADO.NET 4.6.2 of hoger

Als uw programma ADO.NET klassen zoals **System.Data.SqlClient.SqlConnection** gebruikt om verbinding te maken met SQL Database, raden we u aan .NET Framework-versie 4.6.2 of hoger te gebruiken.

#### <a name="starting-with-adonet-462"></a>Te beginnen met ADO.NET 4.6.2

- De open verbinding probeert onmiddellijk opnieuw te worden geprobeerd voor Azure SQL-databases, waardoor de prestaties van cloud-apps worden verbeterd.

#### <a name="starting-with-adonet-461"></a>Te beginnen met ADO.NET 4.6.1

- Voor SQL Database wordt de betrouwbaarheid verbeterd wanneer u een verbinding opent met de **sqlconnection.open-methode.** De **open-methode** bevat nu mechanismen voor het proberen van de beste inspanning als reactie op tijdelijke fouten voor bepaalde fouten binnen de time-outperiode van de verbinding.
- Verbinding pooling wordt ondersteund, die een efficiënte verificatie dat de verbinding object geeft uw programma werkt omvat.

Wanneer u een verbindingsobject uit een verbindingsgroep gebruikt, raden we u aan de verbinding tijdelijk te sluiten wanneer het niet onmiddellijk in gebruik is. Het is niet duur om een verbinding te heropenen, maar het is om een nieuwe verbinding te maken.

Als u ADO.NET 4.0 of eerder gebruikt, raden we u aan te upgraden naar de nieuwste ADO.NET. Vanaf augustus 2018 u [ADO.NET 4.6.2 downloaden.](https://blogs.msdn.microsoft.com/dotnet/20../../announcing-the-net-framework-4-7-2/)

<a id="e-diagnostics-test-utilities-connect" name="e-diagnostics-test-utilities-connect"></a>

## <a name="diagnostics"></a>Diagnostiek

<a id="d-test-whether-utilities-can-connect" name="d-test-whether-utilities-can-connect"></a>

### <a name="diagnostics-test-whether-utilities-can-connect"></a>Diagnostiek: test of nutsbedrijven verbinding kunnen maken

Als uw programma geen verbinding maakt met SQL Database, is één diagnostische optie om te proberen verbinding te maken met een hulpprogrammaprogramma. Idealiter maakt het hulpprogramma verbinding met dezelfde bibliotheek die uw programma gebruikt.

Op elke Windows-computer u deze hulpprogramma's uitproberen:

- SQL Server Management Studio (ssms.exe), die verbinding maakt met behulp van ADO.NET
- `sqlcmd.exe`, die verbindt met [odbc](https://msdn.microsoft.com/library/jj730308.aspx)

Nadat uw programma is verbonden, controleert u of een korte SQL SELECT-query werkt.

<a id="f-diagnostics-check-open-ports" name="f-diagnostics-check-open-ports"></a>

### <a name="diagnostics-check-the-open-ports"></a>Diagnostiek: Controleer de geopende poorten

Als u vermoedt dat verbindingspogingen mislukken als gevolg van poortproblemen, u een hulpprogramma op uw computer uitvoeren dat rapporteert over de poortconfiguraties.

Op Linux kunnen de volgende hulpprogramma's nuttig zijn:

- `netstat -nap`
- `nmap -sS -O 127.0.0.1`: Wijzig de voorbeeldwaarde als uw IP-adres.

Op Windows kan het hulpprogramma [PortQry.exe](https://www.microsoft.com/download/details.aspx?id=17148) nuttig zijn. Hier is een voorbeelduitvoering die de poortsituatie op een SQL Database-server opvroeg en die werd uitgevoerd op een laptop:

```cmd
[C:\Users\johndoe\]
>> portqry.exe -n johndoesvr9.database.windows.net -p tcp -e 1433

Querying target system called: johndoesvr9.database.windows.net

Attempting to resolve name to IP address...
Name resolved to 23.100.117.95

querying...
TCP port 1433 (ms-sql-s service): LISTENING

[C:\Users\johndoe\]
>>
```

<a id="g-diagnostics-log-your-errors" name="g-diagnostics-log-your-errors"></a>

### <a name="diagnostics-log-your-errors"></a>Diagnose: Uw fouten registreren

Een intermitterend probleem wordt soms het best gediagnosticeerd door detectie van een algemeen patroon over dagen of weken.

Uw klant kan helpen bij een diagnose door het registreren van alle fouten die het tegenkomt. Mogelijk u de logboekvermeldingen correleren met foutgegevens die SQL Database zichzelf intern registreert.

Enterprise Library 6 (EntLib60) biedt .NET managed classes om te helpen bij het loggen. Zie [5 - Zo eenvoudig als vallen van een logboek: Gebruik het logging-toepassingsblok](https://msdn.microsoft.com/library/dn440731.aspx).

<a id="h-diagnostics-examine-logs-errors" name="h-diagnostics-examine-logs-errors"></a>

### <a name="diagnostics-examine-system-logs-for-errors"></a>Diagnostiek: Systeemlogboeken onderzoeken op fouten

Hier volgen enkele Transact-SQL SELECT-instructies die foutlogboeken en andere informatie opvragen.

| Query van logboek | Beschrijving |
|:--- |:--- |
| `SELECT e.*`<br/>`FROM sys.event_log AS e`<br/>`WHERE e.database_name = 'myDbName'`<br/>`AND e.event_category = 'connectivity'`<br/>`AND 2 >= DateDiff`<br/>&nbsp;&nbsp;`(hour, e.end_time, GetUtcDate())`<br/>`ORDER BY e.event_category,`<br/>&nbsp;&nbsp;`e.event_type, e.end_time;` |De [sys.event_log-weergave](https://msdn.microsoft.com/library/dn270018.aspx) biedt informatie over afzonderlijke gebeurtenissen, waaronder enkele die tijdelijke fouten of verbindingsfouten kunnen veroorzaken.<br/><br/>Idealiter u de **start_time** of **end_time** waarden correleren met informatie over wanneer uw klantprogramma problemen ondervond.<br/><br/>U moet verbinding maken met de *hoofddatabase* om deze query uit te voeren. |
| `SELECT c.*`<br/>`FROM sys.database_connection_stats AS c`<br/>`WHERE c.database_name = 'myDbName'`<br/>`AND 24 >= DateDiff`<br/>&nbsp;&nbsp;`(hour, c.end_time, GetUtcDate())`<br/>`ORDER BY c.end_time;` |De [sys.database_connection_stats-weergave](https://msdn.microsoft.com/library/dn269986.aspx) biedt geaggregeerde tellingen van gebeurtenistypen voor aanvullende diagnostiek.<br/><br/>U moet verbinding maken met de *hoofddatabase* om deze query uit te voeren. |

<a id="d-search-for-problem-events-in-the-sql-database-log" name="d-search-for-problem-events-in-the-sql-database-log"></a>

### <a name="diagnostics-search-for-problem-events-in-the-sql-database-log"></a>Diagnostische gegevens: zoeken naar probleemgebeurtenissen in het SQL-databaselogboek

U zoeken naar items over probleemgebeurtenissen in het SQL-databaselogboek. Probeer de volgende transact-SQL SELECT-instructie in de *hoofddatabase:*

```sql
SELECT
   object_name
  ,CAST(f.event_data as XML).value
      ('(/event/@timestamp)[1]', 'datetime2')                      AS [timestamp]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="error"]/value)[1]', 'int')             AS [error]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="state"]/value)[1]', 'int')             AS [state]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="is_success"]/value)[1]', 'bit')        AS [is_success]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="database_name"]/value)[1]', 'sysname') AS [database_name]
FROM
  sys.fn_xe_telemetry_blob_target_read_file('el', null, null, null) AS f
WHERE
  object_name != 'login_event'  -- Login events are numerous.
  and
  '2015-06-21' < CAST(f.event_data as XML).value
        ('(/event/@timestamp)[1]', 'datetime2')
ORDER BY
  [timestamp] DESC
;
```

#### <a name="a-few-returned-rows-from-sysfn_xe_telemetry_blob_target_read_file"></a>Een paar teruggekeerde rijen van sys.fn_xe_telemetry_blob_target_read_file

In het volgende voorbeeld ziet u hoe een geretourneerde rij eruit kan zien. De weergegeven null-waarden zijn vaak niet null in andere rijen.

```
object_name                   timestamp                    error  state  is_success  database_name

database_xml_deadlock_report  2015-10-16 20:28:01.0090000  NULL   NULL   NULL        AdventureWorks
```

<a id="l-enterprise-library-6" name="l-enterprise-library-6"></a>

## <a name="enterprise-library-6"></a>Bedrijfsbibliotheek 6

Enterprise Library 6 (EntLib60) is een framework van .NET-klassen waarmee u robuuste clients van cloudservices implementeren, waaronder de SQL Database-service. Zie [Enterprise Library 6 - april 2013](https://msdn.microsoft.com/library/dn169621%28v=pandp.60%29.aspx)voor het vinden van onderwerpen die zijn gewijd aan elk gebied waarop EntLib60 kan helpen.

Logica opnieuw proberen voor het verwerken van tijdelijke fouten is een gebied waarop EntLib60 kan helpen. Voor meer informatie, zie [4 - Doorzettingsvermogen, geheim van alle triomfen: Gebruik de Transient Fault Handling Application Block](https://msdn.microsoft.com/library/dn440719%28v=pandp.60%29.aspx).

> [!NOTE]
> De broncode voor EntLib60 is beschikbaar voor openbare download van het [Downloadcenter.](https://go.microsoft.com/fwlink/p/?LinkID=290898) Microsoft heeft geen plannen om verdere functie-updates of onderhoudsupdates voor EntLib uit te voeren.

<a id="entlib60-classes-for-transient-errors-and-retry" name="entlib60-classes-for-transient-errors-and-retry"></a>

### <a name="entlib60-classes-for-transient-errors-and-retry"></a>EntLib60-klassen voor tijdelijke fouten en nieuwe try

De volgende EntLib60-klassen zijn bijzonder nuttig voor het opnieuw proberen van logica. Al deze klassen zijn te vinden in of onder de naamruimte **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling**.

In de naamruimte **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling:**

- **Opnieuw proberenDe** klasse Beleid
  - **UitvoerActiemethode**
- **ExponentieelBackoff-klasse**
- **SqlDatabaseTransientErrorDetectionStrategy,** klasse
- **ReliableSqlConnection,** klasse
  - **Opdracht uitvoeren**

In de naamruimte **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling.TestSupport:**

- **AlwaysTransientErrorDetectionStrategy,** klasse
- **NeverTransientErrorDetectionStrategy,** klasse

Hier zijn enkele links naar informatie over EntLib60:

- Gratis boek downloaden: [Developer's Guide to Microsoft Enterprise Library, 2e editie](https://www.microsoft.com/download/details.aspx?id=41145).
- Best practices: [Retry algemene begeleiding](../best-practices-retry-general.md) heeft een uitstekende diepgaande discussie over het opnieuw proberen logica.
- NuGet download: [Enterprise Library - Transient Fault Handling Application Block 6.0](https://www.nuget.org/packages/EnterpriseLibrary.TransientFaultHandling/).

<a id="entlib60-the-logging-block" name="entlib60-the-logging-block"></a>

### <a name="entlib60-the-logging-block"></a>EntLib60: Het logboekblok

- Het logboekblok is een zeer flexibele en configureerbare oplossing die u gebruiken om:
  - Logberichten maken en opslaan op verschillende locaties.
  - Berichten categoriseren en filteren.
  - Verzamel contextuele informatie die nuttig is voor het opsporen en traceren, evenals voor controle en algemene logboekregistratievereisten.
- Het logboekblok abstraheert de logboekfunctionaliteit van de logboekbestemming, zodat de toepassingscode consistent is, ongeacht de locatie en het type van het doellogboekarchief.

Zie [5 - Zo eenvoudig als vallen van een logboek: Gebruik het logging-toepassingsblok](https://msdn.microsoft.com/library/dn440731%28v=pandp.60%29.aspx).

<a id="entlib60-istransient-method-source-code" name="entlib60-istransient-method-source-code"></a>

### <a name="entlib60-istransient-method-source-code"></a>EntLib60 IsTransient methode broncode

Vervolgens is vanuit de klasse **SqlDatabaseTransientErrorDetectionStrategy** de C#-broncode voor de **methode IsTransient.** De broncode verduidelijkt welke fouten werden beschouwd als tijdelijk en waardig om opnieuw te proberen, met 1april 2013.

```csharp
public bool IsTransient(Exception ex)
{
  if (ex != null)
  {
    SqlException sqlException;
    if ((sqlException = ex as SqlException) != null)
    {
      // Enumerate through all errors found in the exception.
      foreach (SqlError err in sqlException.Errors)
      {
        switch (err.Number)
        {
            // SQL Error Code: 40501
            // The service is currently busy. Retry the request after 10 seconds.
            // Code: (reason code to be decoded).
          case ThrottlingCondition.ThrottlingErrorNumber:
            // Decode the reason code from the error message to
            // determine the grounds for throttling.
            var condition = ThrottlingCondition.FromError(err);

            // Attach the decoded values as additional attributes to
            // the original SQL exception.
            sqlException.Data[condition.ThrottlingMode.GetType().Name] =
              condition.ThrottlingMode.ToString();
            sqlException.Data[condition.GetType().Name] = condition;

            return true;

          case 10928:
          case 10929:
          case 10053:
          case 10054:
          case 10060:
          case 40197:
          case 40540:
          case 40613:
          case 40143:
          case 233:
          case 64:
            // DBNETLIB Error Code: 20
            // The instance of SQL Server you attempted to connect to
            // does not support encryption.
          case (int)ProcessNetLibErrorCode.EncryptionNotSupported:
            return true;
        }
      }
    }
    else if (ex is TimeoutException)
    {
      return true;
    }
    else
    {
      EntityException entityException;
      if ((entityException = ex as EntityException) != null)
      {
        return this.IsTransient(entityException.InnerException);
      }
    }
  }

  return false;
}
```

## <a name="next-steps"></a>Volgende stappen

- [Verbindingsbibliotheken voor SQL Database en SQL Server](sql-database-libraries.md)
- [SQL Server-verbinding pooling (ADO.NET)](https://docs.microsoft.com/dotnet/framework/data/adonet/sql-server-connection-pooling)
- [ *Opnieuw proberen* is een Apache 2.0 licentie algemeen doel opnieuw proberen bibliotheek, geschreven in Python,](https://pypi.python.org/pypi/retrying) om de taak van het toevoegen van opnieuw proberen gedrag te vereenvoudigen om zowat alles.

<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-sql-ado-net

[step-4-connect-resiliently-to-sql-with-php-p42h]: https://docs.microsoft.com/sql/connect/php/step-4-connect-resiliently-to-sql-with-php
