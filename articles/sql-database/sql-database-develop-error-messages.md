---
title: SQL-fout codes-database verbindings fout | Microsoft Docs
description: 'Meer informatie over SQL-fout codes voor SQL Database-client toepassingen, zoals veelvoorkomende database verbindings fouten, problemen met database kopieën en algemene fouten. '
keywords: SQL-fout code, Access SQL, database verbindings fout, SQL-fout codes
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 10/02/2019
ms.openlocfilehash: 8d4e7fa314ce3a5f8534e7742880114ccc7f7144
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/18/2019
ms.locfileid: "72598145"
---
# <a name="sql-error-codes-for-sql-database-client-applications-database-connection-errors-and-other-issues"></a>SQL-fout codes voor SQL Database-client toepassingen: database verbindings fouten en andere problemen

In dit artikel vindt u een overzicht van SQL-fout codes voor SQL Database-client toepassingen, waaronder database verbindings fouten, tijdelijke fouten (ook wel tijdelijke fouten genoemd), fouten in de resource governance, problemen met de data base kopiëren, elastische groepen en andere fouten. De meeste categorieën zijn specifiek voor Azure SQL Database en zijn niet van toepassing op Microsoft SQL Server. Zie ook [systeem fout berichten](https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors?view=sql-server-ver15).

## <a name="database-connection-errors-transient-errors-and-other-temporary-errors"></a>Database verbindings fouten, tijdelijke fouten en andere tijdelijke fouten

De volgende tabel bevat de SQL-fout codes voor problemen met de verbindings verlies en andere tijdelijke fouten die kunnen optreden wanneer uw toepassing probeert toegang te krijgen tot SQL Database. Zie [verbinding maken met Azure SQL database](sql-database-libraries.md)voor aan de slag-zelf studies over het maken van verbinding met Azure SQL database.

### <a name="most-common-database-connection-errors-and-transient-fault-errors"></a>Meest voorkomende database verbindings fouten en tijdelijke fout fouten

De Azure-infrastructuur biedt de mogelijkheid om servers dynamisch opnieuw te configureren wanneer zware workloads optreden in de SQL Database-service.  Dit dynamische gedrag kan ervoor zorgen dat uw clientprogramma de verbinding met SQL Database verliest. Dit type fout wordt een *tijdelijke fout*genoemd.

Het wordt ten zeerste aangeraden dat uw client programma logica voor nieuwe pogingen heeft, zodat er een verbinding tot stand kan worden gebracht nadat de tijdelijke fout tijd is gecorrigeerd.  U wordt aangeraden vijf seconden vóór uw eerste nieuwe poging uit te stellen. Opnieuw proberen na een vertraging die korter is dan 5 seconden Risico's voor de Cloud service. Voor elke volgende poging moet de vertraging exponentieel toenemen, tot een maximum van 60 seconden.

Tijdelijke fouten worden meestal als een van de volgende fout berichten van uw client Programma's manifesteren:

* Data base &lt;db_name &gt; op server &lt;Azure_instance &gt; is momenteel niet beschikbaar. Probeer de verbinding later opnieuw. Als het probleem zich blijft voordoen, neemt u contact op met de klant ondersteuning en geeft u de sessie tracering-ID van &lt;session_id &gt;
* Data base &lt;db_name &gt; op server &lt;Azure_instance &gt; is momenteel niet beschikbaar. Probeer de verbinding later opnieuw. Als het probleem zich blijft voordoen, neemt u contact op met de klant ondersteuning en geeft u de sessie tracering-ID van &lt;session_id &gt;. (Microsoft SQL Server, fout: 40613)
* Een bestaande verbinding is geforceerd gesloten door de externe host.
* System. data. entity. core. EntityCommandExecutionException: er is een fout opgetreden tijdens het uitvoeren van de opdracht definitie. Zie de interne uitzonde ring voor meer informatie. ---> System. data. SqlClient. SqlException: er is een fout op transport niveau opgetreden bij het ontvangen van resultaten van de server. (provider: sessie provider, fout: 19 fysieke verbinding is niet bruikbaar)
* Een verbindings poging met een secundaire data base is mislukt omdat de data base opnieuw wordt geconfigureerd en er bezig is met het Toep assen van nieuwe pagina's in het midden van een actieve trans actie op de primaire data base. 

Zie voor code voorbeelden van de logica voor opnieuw proberen:

* [Verbindings bibliotheken voor SQL Database en SQL Server](sql-database-libraries.md) 
* [Acties voor het oplossen van verbindings fouten en tijdelijke fouten in SQL Database](sql-database-connectivity-issues.md)

Een bespreking van de *blokkerings periode* voor clients die gebruikmaken van ADO.net is beschikbaar in [SQL Server Connection pooling (ADO.net)](https://msdn.microsoft.com/library/8xx3tyca.aspx).

### <a name="transient-fault-error-codes"></a>Tijdelijke fout codes

De volgende fouten zijn tijdelijk en moeten opnieuw worden geprobeerd in toepassings logica: 

| Foutcode | Ernst | Beschrijving |
| ---:| ---:|:--- |
| 4060 |16 |Kan de data base% niet openen. &#x2a;LS, aangevraagd door de aanmelding. De aanmelding is mislukt. Zie [fouten 4000 tot 4999](https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors#errors-4000-to-4999) voor meer informatie.|
| 40197 |17 |De service heeft een fout aangetroffen bij het verwerken van uw aanvraag. Probeer het opnieuw. Fout code% d.<br/><br/>U ontvangt deze fout melding wanneer de service niet beschikbaar is vanwege software-of hardware-upgrades, hardwarefouten of andere failover-problemen. De fout code (% d) [Inge sloten in het bericht van fout 40197](sql-database-develop-error-messages.md#embedded-error-codes) bevat aanvullende informatie over het soort fout of failover dat heeft plaatsgevonden. Enkele voor beelden van de fout codes zijn Inge sloten in het bericht met de fout 40197 zijn 40020, 40143, 40166 en 40540.<br/><br/>Als u opnieuw verbinding maakt met uw SQL Database Server, wordt u automatisch verbonden met een gezonde kopie van uw data base. Uw toepassing moet fout 40197 opvangen, de Inge sloten fout code (% d) in het bericht voor het oplossen van problemen registreren en opnieuw proberen verbinding te maken met SQL Database totdat de resources beschikbaar zijn en uw verbinding opnieuw tot stand is gebracht. Zie [tijdelijke fouten](sql-database-connectivity-issues.md#transient-errors-transient-faults)voor meer informatie.|
| 40501 |20 |De service is momenteel bezet. Voer de aanvraag na 10 seconden opnieuw uit. Incident-ID:% ls. Code:% d. Zie voor meer informatie: <br/>limieten voor &bull; &nbsp;[Data Base-server resources](sql-database-resource-limits-database-server.md)<br/>&bull; &nbsp;[op DTU gebaseerde limieten voor afzonderlijke data bases](sql-database-service-tiers-dtu.md)<br/>&bull; &nbsp;[op DTU gebaseerde limieten voor elastische Pools](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[vCore limieten voor afzonderlijke data bases](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull; &nbsp;[vCore limieten voor elastische Pools](sql-database-vcore-resource-limits-elastic-pools.md)<br/>de[resource limieten van de beheerde instantie](sql-database-managed-instance-resource-limits.md)&bull; &nbsp;.|
| 40613 |17 |Data Base%. &#x2a;ls op server%. &#x2a;LS is momenteel niet beschikbaar. Probeer de verbinding later opnieuw. Als het probleem zich blijft voordoen, neemt u contact op met de klant ondersteuning en geeft u de sessie tracering-ID%. &#x2a;ls.<br/><br/> Deze fout kan optreden als er al een DAC (dedicated Administrator Connection) is ingesteld op de data base. Zie [tijdelijke fouten](sql-database-connectivity-issues.md#transient-errors-transient-faults)voor meer informatie.|
| 49918 |16 |Kan aanvraag niet verwerken. Er zijn onvoldoende bronnen om de aanvraag te verwerken.<br/><br/>De service is momenteel bezet. Voer de aanvraag later opnieuw uit. Zie voor meer informatie: <br/>limieten voor &bull; &nbsp;[Data Base-server resources](sql-database-resource-limits-database-server.md)<br/>&bull; &nbsp;[op DTU gebaseerde limieten voor afzonderlijke data bases](sql-database-service-tiers-dtu.md)<br/>&bull; &nbsp;[op DTU gebaseerde limieten voor elastische Pools](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[vCore limieten voor afzonderlijke data bases](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull; &nbsp;[vCore limieten voor elastische Pools](sql-database-vcore-resource-limits-elastic-pools.md)<br/>de[resource limieten van de beheerde instantie](sql-database-managed-instance-resource-limits.md)&bull; &nbsp;. |
| 49919 |16 |Kan de aanvraag voor maken of bijwerken niet verwerken. Er zijn te veel bewerkingen voor het maken of bijwerken van het abonnement% ld.<br/><br/>De service is bezig met het verwerken van meerdere aanvragen voor maken of bijwerken voor uw abonnement of server. Aanvragen zijn momenteel geblokkeerd voor het optimaliseren van resources. Query [sys. DM _operation_status](https://msdn.microsoft.com/library/dn270022.aspx) voor bewerkingen die in behandeling zijn. Wacht tot er aanvragen voor maken of bijwerken zijn voltooid of verwijder een van de in behandeling zijnde aanvragen en voer de aanvraag later opnieuw uit. Zie voor meer informatie: <br/>limieten voor &bull; &nbsp;[Data Base-server resources](sql-database-resource-limits-database-server.md)<br/>&bull; &nbsp;[op DTU gebaseerde limieten voor afzonderlijke data bases](sql-database-service-tiers-dtu.md)<br/>&bull; &nbsp;[op DTU gebaseerde limieten voor elastische Pools](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[vCore limieten voor afzonderlijke data bases](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull; &nbsp;[vCore limieten voor elastische Pools](sql-database-vcore-resource-limits-elastic-pools.md)<br/>de[resource limieten van de beheerde instantie](sql-database-managed-instance-resource-limits.md)&bull; &nbsp;. |
| 49920 |16 |Kan aanvraag niet verwerken. Er worden te veel bewerkingen uitgevoerd voor het abonnement% ld.<br/><br/>De service is bezig met het verwerken van meerdere aanvragen voor dit abonnement. Aanvragen zijn momenteel geblokkeerd voor het optimaliseren van resources. Query [sys. DM _operation_status](https://msdn.microsoft.com/library/dn270022.aspx) voor bewerkings status. Wacht totdat aanvragen in behandeling zijn voltooid of verwijder een van de in behandeling zijnde aanvragen en voer de aanvraag later opnieuw uit. Zie voor meer informatie: <br/>limieten voor &bull; &nbsp;[Data Base-server resources](sql-database-resource-limits-database-server.md)<br/>&bull; &nbsp;[op DTU gebaseerde limieten voor afzonderlijke data bases](sql-database-service-tiers-dtu.md)<br/>&bull; &nbsp;[op DTU gebaseerde limieten voor elastische Pools](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[vCore limieten voor afzonderlijke data bases](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull; &nbsp;[vCore limieten voor elastische Pools](sql-database-vcore-resource-limits-elastic-pools.md)<br/>de[resource limieten van de beheerde instantie](sql-database-managed-instance-resource-limits.md)&bull; &nbsp;. |
| 4221 |16 |Aanmelden bij Lees bewerking is mislukt wegens een lange wacht tijd op ' HADR_DATABASE_WAIT_FOR_TRANSITION_TO_VERSIONING '. De replica is niet beschikbaar voor aanmelding omdat er geen rijkoppen ontbreken voor trans acties die in de vlucht waren toen de replica werd gerecycled. Het probleem kan worden opgelost door de actieve trans acties op de primaire replica te herstellen of door te voeren. Exemplaren van deze voor waarde kunnen worden geminimaliseerd door lange schrijf transacties op de primaire te vermijden. |

## <a name="embedded-error-codes"></a>Inge sloten fout codes

De volgende fouten zijn Inge sloten in de algemenere fout code 40197:

```
The service has encountered an error processing your request. Please try again. Error code %d.
```

| Foutcode | Ernst | Beschrijving | 
| ---:| ---:|:---|
|  1104 |17 |Er is onvoldoende ruimte beschikbaar op TEMPDB tijdens het overvloeien. Maak ruimte door objecten te verwijderen en/of Herschrijf de query om minder rijen te gebruiken. Als het probleem zich blijft voordoen, kunt u overwegen om een upgrade uit te voeren naar een hogere serviceniveau doelstelling.|
| 40020 |16 |De Data Base bevindt zich in de overgangs fase en de trans acties worden beëindigd.|
| 40143 |16 |De replica die op het gegevens knooppunt voor de gevraagde partitie wordt gehost, is niet primair.|
| 40166 |16 |Er wordt een CloudDB-herconfiguratie door lopen en alle nieuwe gebruikers transacties worden afgebroken.|
| 40540 |16 |De trans actie is afgebroken omdat de data base is verplaatst naar de alleen-lezen modus. Dit is een tijdelijke situatie en voer de bewerking opnieuw uit.|

Meer informatie over andere Inge sloten fouten kunt u vinden in `sys.messages`:

```sql
SELECT * FROM sys.[messages] WHERE [message_id] = <error_code>
```

## <a name="database-copy-errors"></a>Database kopie fouten

De volgende fouten zijn opgetreden tijdens het kopiëren van een data base in Azure SQL Database. Zie [Een Azure SQL Database kopiëren](sql-database-copy.md) voor meer informatie.

| Foutcode | Ernst | Beschrijving |
| ---:| ---:|:--- |
| 40635 |16 |Client met IP-adres%. &#x2a;LS is tijdelijk uitgeschakeld. |
| 40637 |16 |Het maken van de data base is momenteel uitgeschakeld. |
| 40561 |16 |Het kopiëren van de data base is mislukt. De bron-of doel database bestaat niet. |
| 40562 |16 |Het kopiëren van de data base is mislukt. De bron database is verwijderd. |
| 40563 |16 |Het kopiëren van de data base is mislukt. De doel database is verwijderd. |
| 40564 |16 |Het kopiëren van de data base is mislukt vanwege een interne fout. Verwijder de doel database en probeer het opnieuw. |
| 40565 |16 |Het kopiëren van de data base is mislukt. Er is niet meer dan één gelijktijdige database kopie van dezelfde bron toegestaan. Verwijder de doel database en probeer het later opnieuw. |
| 40566 |16 |Het kopiëren van de data base is mislukt vanwege een interne fout. Verwijder de doel database en probeer het opnieuw. |
| 40567 |16 |Het kopiëren van de data base is mislukt vanwege een interne fout. Verwijder de doel database en probeer het opnieuw. |
| 40568 |16 |Het kopiëren van de data base is mislukt. De bron database is niet meer beschikbaar. Verwijder de doel database en probeer het opnieuw. |
| 40569 |16 |Het kopiëren van de data base is mislukt. De doel database is niet meer beschikbaar. Verwijder de doel database en probeer het opnieuw. |
| 40570 |16 |Het kopiëren van de data base is mislukt vanwege een interne fout. Verwijder de doel database en probeer het later opnieuw. |
| 40571 |16 |Het kopiëren van de data base is mislukt vanwege een interne fout. Verwijder de doel database en probeer het later opnieuw. |

## <a name="resource-governance-errors"></a>Resource governance-fouten

De volgende fouten worden veroorzaakt door overmatig gebruik van resources tijdens het werken met Azure SQL Database. Bijvoorbeeld:

* Een trans actie is te lang geopend.
* Een trans actie houdt te veel vergren delingen.
* Een toepassing verbruikt te veel geheugen.
* Een toepassing verbruikt te veel `TempDb` ruimte.

Verwante onderwerpen:

* Zie voor meer informatie:
  * [Limieten voor database server resources](sql-database-resource-limits-database-server.md)
  * [Op DTU gebaseerde limieten voor afzonderlijke data bases](sql-database-service-tiers-dtu.md)
  * [Op DTU gebaseerde limieten voor elastische Pools](sql-database-dtu-resource-limits-elastic-pools.md)
  * [limieten op basis van vCore voor afzonderlijke data bases](sql-database-vcore-resource-limits-single-databases.md)
  * [limieten op basis van vCore voor elastische Pools](sql-database-vcore-resource-limits-elastic-pools.md)
  * [Resource limieten voor beheerd exemplaar](sql-database-managed-instance-resource-limits.md). 

| Foutcode | Ernst | Beschrijving |
| ---:| ---:|:--- |
| 10928 |20 |Resource-ID:% d. De% s-limiet voor de data base is% d en is bereikt. Zie [SQL database resource limieten voor één en gepoolde data bases](sql-database-resource-limits-database-server.md)voor meer informatie.<br/><br/>De resource-ID geeft de resource aan die de limiet heeft bereikt. Voor worker-threads, de resource-ID = 1. Voor sessies, de resource-ID = 2.<br/><br/>Zie voor meer informatie over deze fout en hoe u deze kunt oplossen: <br/>limieten voor &bull; &nbsp;[Data Base-server resources](sql-database-resource-limits-database-server.md)<br/>&bull; &nbsp;[op DTU gebaseerde limieten voor afzonderlijke data bases](sql-database-service-tiers-dtu.md)<br/>&bull; &nbsp;[op DTU gebaseerde limieten voor elastische Pools](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[vCore limieten voor afzonderlijke data bases](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull; &nbsp;[vCore limieten voor elastische Pools](sql-database-vcore-resource-limits-elastic-pools.md)<br/>de[resource limieten van de beheerde instantie](sql-database-managed-instance-resource-limits.md)&bull; &nbsp;. |
| 10929 |20 |Resource-ID:% d. De minimale garantie van% s is% d, de maximum limiet is% d en het huidige gebruik van de data base is% d. De server is momenteel echter bezet om aanvragen te ondersteunen die groter zijn dan% d voor deze data base. De resource-ID geeft de resource aan die de limiet heeft bereikt. Voor worker-threads, de resource-ID = 1. Voor sessies, de resource-ID = 2. Zie voor meer informatie: <br/>limieten voor &bull; &nbsp;[Data Base-server resources](sql-database-resource-limits-database-server.md)<br/>&bull; &nbsp;[op DTU gebaseerde limieten voor afzonderlijke data bases](sql-database-service-tiers-dtu.md)<br/>&bull; &nbsp;[op DTU gebaseerde limieten voor elastische Pools](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[vCore limieten voor afzonderlijke data bases](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull; &nbsp;[vCore limieten voor elastische Pools](sql-database-vcore-resource-limits-elastic-pools.md)<br/>de[resource limieten van de beheerde instantie](sql-database-managed-instance-resource-limits.md)&bull; &nbsp;. <br/>Probeer het later opnieuw. |
| 40544 |20 |De data base heeft het quotum voor de grootte bereikt. Partitioneer of verwijder gegevens, verwijder indexen of Raadpleeg de documentatie voor mogelijke oplossingen. Zie voor het schalen van de Data Base [afzonderlijke database resources schalen](sql-database-single-database-scale.md) en [bronnen voor elastische Pools schalen](sql-database-elastic-pool-scale.md).|
| 40549 |16 |De sessie is beëindigd omdat u een langlopende trans actie hebt. Probeer uw trans actie te verkorten. Zie voor meer informatie over batch verwerking [batch verwerking gebruiken om de prestaties van SQL database-toepassingen te verbeteren](sql-database-use-batching-to-improve-performance.md).|
| 40550 |16 |De sessie is beëindigd omdat er te veel vergren delingen zijn verkregen. Probeer minder rijen in één trans actie te lezen of te wijzigen. Zie voor meer informatie over batch verwerking [batch verwerking gebruiken om de prestaties van SQL database-toepassingen te verbeteren](sql-database-use-batching-to-improve-performance.md).|
| 40551 |16 |De sessie is beëindigd vanwege het overmatige `TEMPDB` gebruik. Wijzig uw query om het gebruik van de tijdelijke tabel ruimte te verminderen.<br/><br/>Als u tijdelijke objecten gebruikt, bespaart u ruimte in de `TEMPDB`-data base door tijdelijke objecten te verwijderen nadat deze niet langer nodig zijn voor de sessie. Zie [tempdb-data base in SQL database](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database)voor meer informatie over het gebruik van tempdb in SQL database.|
| 40552 |16 |De sessie is beëindigd vanwege het overmatige gebruik van het transactie logboek. Probeer minder rijen in één trans actie te wijzigen. Zie voor meer informatie over batch verwerking [batch verwerking gebruiken om de prestaties van SQL database-toepassingen te verbeteren](sql-database-use-batching-to-improve-performance.md).<br/><br/>Als u bulksgewijze toevoegingen uitvoert met behulp van het `bcp.exe`-hulp programma of de `System.Data.SqlClient.SqlBulkCopy`-klasse, gebruikt u de opties `-b batchsize` of `BatchSize` om het aantal rijen te beperken dat naar de server in elke trans actie is gekopieerd. Als u een index opnieuw wilt samen stellen met de `ALTER INDEX`-instructie, kunt u de `REBUILD WITH ONLINE = ON` optie gebruiken. Voor informatie over de grootte van het transactie logboek voor het vCore-aankoop model raadpleegt u: <br/>&bull; &nbsp;[vCore limieten voor afzonderlijke data bases](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull; &nbsp;[vCore limieten voor elastische Pools](sql-database-vcore-resource-limits-elastic-pools.md)<br/>de[resource limieten van de beheerde instantie](sql-database-managed-instance-resource-limits.md)&bull; &nbsp;.|
| 40553 |16 |De sessie is beëindigd vanwege het overmatige geheugen gebruik. Wijzig uw query om minder rijen te verwerken.<br/><br/>Het verminderen van het aantal `ORDER BY`-en `GROUP BY`-bewerkingen in uw Transact-SQL-code vermindert de geheugen vereisten van uw query. Zie voor het schalen van de Data Base [afzonderlijke database resources schalen](sql-database-single-database-scale.md) en [bronnen voor elastische Pools schalen](sql-database-elastic-pool-scale.md).|

## <a name="elastic-pool-errors"></a>Fouten in elastische Pools

De volgende fouten zijn gerelateerd aan het maken en gebruiken van elastische Pools:

| Foutcode | Ernst | Beschrijving | Corrigerende actie |
|:--- |:--- |:--- |:--- |
| 1132 | 17 |De elastische pool heeft de opslag limiet bereikt. Het opslag gebruik voor de elastische pool mag niet groter zijn dan (% d) MB. Poging tot het schrijven van gegevens naar een Data Base als de opslag limiet van de elastische pool is bereikt. Zie voor informatie over resource limieten: <br/>&bull; &nbsp;[op DTU gebaseerde limieten voor elastische Pools](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull;[limieten voor elastische Pools op vCore](sql-database-vcore-resource-limits-elastic-pools.md)&nbsp;. <br/> |U kunt overwegen de Dtu's van en/of opslag ruimte toe te voegen aan de elastische pool, indien mogelijk om de opslag limiet te verhogen, de opslag die wordt gebruikt door afzonderlijke data bases in de elastische pool te verminderen of data bases uit de elastische pool te verwijderen. Zie [elastische pool resources schalen](sql-database-elastic-pool-scale.md)voor elastische Pools schalen.|
| 10929 | 16 |De minimale garantie van% s is% d, de maximum limiet is% d en het huidige gebruik van de data base is% d. De server is momenteel echter bezet om aanvragen te ondersteunen die groter zijn dan% d voor deze data base. Zie voor informatie over resource limieten: <br/>&bull; &nbsp;[op DTU gebaseerde limieten voor elastische Pools](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull;[limieten voor elastische Pools op vCore](sql-database-vcore-resource-limits-elastic-pools.md)&nbsp;. <br/> Probeer het later opnieuw. DTU/vCore min per data base; DTU/vCore Max per data base. Het totale aantal gelijktijdige werk rollen (aanvragen) voor alle data bases in de elastische pool heeft geprobeerd de limiet van de groep te overschrijden. |U kunt eventueel de Dtu's of vCores van de elastische pool verg Roten, zodat de werknemers limiet kan worden verhoogd of data bases uit de elastische pool kunnen worden verwijderd. |
| 40844 | 16 |De data base% ls op server% LS is een% ls Edition-data base in een elastische pool en kan geen doorlopende Kopieer relatie hebben.  |N/A |
| 40857 | 16 |Kan de elastische groep niet vinden voor de server:% ls, naam van elastische groep:% ls. Opgegeven elastische groep bestaat niet in de opgegeven server. | Geef een geldige naam op voor een elastische pool. |
| 40858 | 16 |Elastische pool% ls bestaat al op de server:% ls. Opgegeven elastische groep bestaat al in de opgegeven SQL Database Server. | Geef een nieuwe naam voor de elastische groep op. |
| 40859 | 16 |De elastische pool biedt geen ondersteuning voor de servicelaag% ls. De opgegeven servicelaag wordt niet ondersteund voor het inrichten van elastische Pools. |Geef de juiste editie op of laat de servicelaag leeg om de standaard servicelaag te gebruiken. |
| 40860 | 16 |De combi natie van elastische pool% LS en service doelstelling% LS is ongeldig. Elastische pool en servicelaag kunnen alleen samen worden opgegeven als het resource type is opgegeven als ' ElasticPool '. |Geef een juiste combi natie van elastische pool en servicelaag op. |
| 40861 | 16 |De data base-editie%. *ls kan niet verschillen van de servicelaag van de elastische pool. deze is%.* ls. De data base-editie wijkt af van de servicelaag van de elastische pool. |Geef geen data base-editie op die verschilt van de servicelaag van de elastische pool.  Houd er rekening mee dat de data base-editie niet hoeft te worden opgegeven. |
| 40862 | 16 |De naam van de elastische groep moet worden opgegeven als de service doelstelling van de elastische groep is opgegeven. De service doelstelling van de elastische groep biedt geen unieke identificatie voor een elastische pool. |Geef de naam van de elastische groep op als u gebruikmaakt van de service doelstelling van de elastische groep. |
| 40864 | 16 |De Dtu's voor de elastische pool moet ten minste (% d) Dtu's zijn voor de servicelaag %1!. Er wordt geprobeerd de Dtu's voor de elastische pool in te stellen beneden de minimale limiet. |Stel de Dtu's voor de elastische pool opnieuw in op ten minste de minimale limiet. |
| 40865 | 16 |De Dtu's voor de elastische pool mag niet groter zijn dan (% d) Dtu's voor servicelaag %1!. Er wordt geprobeerd de Dtu's voor de elastische pool in te stellen boven de maximum limiet. |Stel de Dtu's voor de elastische pool in op een waarde die niet groter is dan de maximum limiet. |
| 40867 | 16 |Het maximum aantal DTU per data base moet ten minste (% d) zijn voor de servicelaag %1!. Er wordt geprobeerd het maximale DTU-maximum per data base in te stellen onder de ondersteunde limiet. | Overweeg het gebruik van de servicelaag van de elastische pool die de gewenste instelling ondersteunt. |
| 40868 | 16 |Het maximum aantal DTU per data base mag niet groter zijn dan (% d) voor servicelaag %1!. Er wordt geprobeerd het maximale DTU-maximum per data base in te stellen buiten de ondersteunde limiet. | Overweeg het gebruik van de servicelaag van de elastische pool die de gewenste instelling ondersteunt. |
| 40870 | 16 |Het DTU-minimum per data base mag niet groter zijn dan (% d) voor servicelaag %1!. Poging tot het instellen van de maximale DTU-minimum per data base dan de ondersteunde limiet. | Overweeg het gebruik van de servicelaag van de elastische pool die de gewenste instelling ondersteunt. |
| 40873 | 16 |Het aantal data bases (% d) en de DTU min per data base (% d) mag de Dtu's van de elastische pool (% d) niet overschrijden. Poging tot opgeven van DTU min voor data bases in de elastische pool die groter is dan de Dtu's van de elastische pool. | Overweeg de Dtu's van de elastische pool te verhogen, of verklein de DTU min per data base, of verklein het aantal data bases in de elastische pool. |
| 40877 | 16 |Een elastische pool kan alleen worden verwijderd als deze geen data bases bevat. De elastische pool bevat een of meer data bases en kan daarom niet worden verwijderd. |Verwijder data bases uit de elastische pool om deze te verwijderen. |
| 40881 | 16 |De elastische pool %1! ls heeft de limiet voor het aantal data bases bereikt.  De limiet voor het aantal data bases voor de elastische pool mag niet groter zijn dan (% d) voor een elastische pool met (% d) Dtu's. Poging om een Data Base te maken of toe te voegen aan een elastische pool wanneer de limiet voor het aantal data bases van de elastische pool is bereikt. | Overweeg zo mogelijk de Dtu's van de elastische pool te verhogen om de database limiet te verhogen of om data bases uit de elastische pool te verwijderen. |
| 40889 | 16 |De Dtu's of opslag limiet voor de elastische pool %1!!, kan niet worden verlaagd omdat er onvoldoende opslag ruimte beschikbaar is voor de data bases. Er wordt geprobeerd de opslag limiet van de elastische pool onder het opslag gebruik te verlagen. | Overweeg het opslag gebruik van afzonderlijke data bases in de elastische pool te verminderen of data bases uit de groep te verwijderen om de Dtu's of opslag limiet te verminderen. |
| 40891 | 16 |Het maximum aantal DTU per data base (% d) kan niet groter zijn dan de maximale DTU per data base (% d). Poging om de DTU min per data base in te stellen hoger dan het maximum aantal DTU per data base. |Zorg ervoor dat de minimale DTU per data base het maximale DTU-maximum per data base niet overschrijdt. |
| Nader te bepalen | 16 |De opslag grootte voor een afzonderlijke data base in een elastische pool kan niet groter zijn dan de maximale grootte die is toegestaan door %1! de elastische pool van de service tier. De maximale grootte voor de data base overschrijdt de Maxi maal toegestane grootte van de servicelaag van de elastische groep. |Stel de maximale grootte van de data base in binnen de limieten van de Maxi maal toegestane grootte door de servicelaag van de elastische pool. |

Verwante onderwerpen:

* [Een elastische pool makenC#()](sql-database-elastic-pool-manage-csharp.md)
* [Een elastische pool beherenC#()](sql-database-elastic-pool-manage-csharp.md)
* [Een elastische pool maken (Power shell)](sql-database-elastic-pool-manage-powershell.md)
* [Een elastische Pool controleren en beheren (Power shell)](sql-database-elastic-pool-manage-powershell.md)

## <a name="general-errors"></a>Algemene fouten

De volgende fouten vallen niet onder een eerdere categorie.

| Foutcode | Ernst | Beschrijving |
| ---:| ---:|:--- |
| [15006](https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors#errors-15000-to-15999) |16 |(Administrator login) is geen geldige naam omdat deze ongeldige tekens bevat.|
| [18452](https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors#errors-18000-to-18999) |14 |Aanmelden mislukt. De aanmelding is afkomstig uit een niet-vertrouwd domein en kan niet worden gebruikt met Windows-verificatie .%. &#x2a;ls (Windows-aanmeldingen worden niet ondersteund in deze versie van SQL Server.) |
| [18456](https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors#errors-18000-to-18999) |14 |De aanmelding is mislukt voor de gebruiker%. &#x2a;ls .%. &#x2a;ls%. &#x2a;ls (de aanmelding is mislukt voor gebruiker%.&#x2a; ls.) |
| [18470](https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors#errors-18000-to-18999) |14 |De aanmelding is mislukt voor de gebruiker%. &#x2a;ls. Reden: het account is uitgeschakeld .%. &#x2a;ls |
| 40014 |16 |In dezelfde trans actie kunnen niet meerdere data bases worden gebruikt. |
| 40054 |16 |Tabellen zonder geclusterde index worden niet ondersteund in deze versie van SQL Server. Maak een geclusterde index en probeer het opnieuw. |
| 40133 |15 |Deze bewerking wordt niet ondersteund in deze versie van SQL Server. |
| 40506 |16 |De opgegeven SID is ongeldig voor deze versie van SQL Server. |
| 40507 |16 |'%. &#x2a;ls kan niet worden aangeroepen met para meters in deze versie van SQL Server. |
| 40508 |16 |De instructie USE wordt niet ondersteund voor scha kelen tussen data bases. Gebruik een nieuwe verbinding om verbinding te maken met een andere data base. |
| 40510 |16 |Instructie%. &#x2a;ls wordt niet ondersteund in deze versie van SQL Server |
| 40511 |16 |Ingebouwde functie%. &#x2a;ls wordt niet ondersteund in deze versie van SQL Server. |
| 40512 |16 |Afgeschafte functie% ls wordt niet ondersteund in deze versie van SQL Server. |
| 40513 |16 |Server variabele%. &#x2a;ls wordt niet ondersteund in deze versie van SQL Server. |
| 40514 |16 |% ls wordt niet ondersteund in deze versie van SQL Server. |
| 40515 |16 |Verwijzing naar de data base en/of server naam in%. &#x2a;ls wordt niet ondersteund in deze versie van SQL Server. |
| 40516 |16 |Algemene tijdelijke objecten worden niet ondersteund in deze versie van SQL Server. |
| 40517 |16 |Tref woord-of instructie optie%. &#x2a;ls wordt niet ondersteund in deze versie van SQL Server. |
| 40518 |16 |DBCC-opdracht%. &#x2a;ls wordt niet ondersteund in deze versie van SQL Server. |
| 40520 |16 |De Beveilig bare klasse% S_MSG wordt niet ondersteund in deze versie van SQL Server. |
| 40521 |16 |De Beveilig bare klasse% S_MSG wordt niet ondersteund in het server bereik in deze versie van SQL Server. |
| 40522 |16 |Databaseprincipal%. &#x2a;ls-type wordt niet ondersteund in deze versie van SQL Server. |
| 40523 |16 |Impliciete gebruiker%. &#x2a;ls wordt niet ondersteund in deze versie van SQL Server. Maak de gebruiker expliciet voordat u deze gebruikt. |
| 40524 |16 |Gegevens type%. &#x2a;ls wordt niet ondersteund in deze versie van SQL Server. |
| 40525 |16 |MET %1! wordt niet ondersteund in deze versie van SQL Server. |
| 40526 |16 |'%. &#x2a;ls ' Rijset-provider wordt niet ondersteund in deze versie van SQL Server. |
| 40527 |16 |Gekoppelde servers worden niet ondersteund in deze versie van SQL Server. |
| 40528 |16 |Gebruikers kunnen niet worden toegewezen aan certificaten, asymmetrische sleutels of Windows-aanmeldingen in deze versie van SQL Server. |
| 40529 |16 |Ingebouwde functie%. &#x2a;ls in de context van de imitatie wordt niet ondersteund in deze versie van SQL Server. |
| 40532 |11 |Kan de server% niet openen. &#x2a;LS, aangevraagd door de aanmelding. De aanmelding is mislukt. |
| 40553 |16 |De sessie is beëindigd vanwege het overmatige geheugen gebruik. Wijzig uw query om minder rijen te verwerken.<br/><br/> Het verminderen van het aantal `ORDER BY`-en `GROUP BY`-bewerkingen in uw Transact-SQL-code helpt de geheugen vereisten van uw query te verminderen. |
| 40604 |16 |Kan de data base niet maken/wijzigen omdat deze het quotum van de server zou overschrijden. |
| 40606 |16 |Het koppelen van data bases wordt niet ondersteund in deze versie van SQL Server. |
| 40607 |16 |Windows-aanmeldingen worden niet ondersteund in deze versie van SQL Server. |
| 40611 |16 |Voor servers kunnen Maxi maal 128 firewall regels worden gedefinieerd. |
| 40614 |16 |IP-begin adres van de firewall regel kan het laatste IP-adres niet overschrijden. |
| 40615 |16 |Kan de server {0} die door de aanmelding is aangevraagd, niet openen. De client met het IP-adres {1} heeft geen toegang tot de server.<br /><br />Als u toegang wilt inschakelen, gebruikt u de SQL Database portal of voert u SP \_set \_firewall \_rule uit op de hoofd database om een firewall regel te maken voor dit IP-adres of adres bereik. Het kan tot vijf minuten duren voordat deze wijziging van kracht wordt. |
| 40617 |16 |De naam van de firewall regel die begint met (regel naam) is te lang. De maximum lengte is 128. |
| 40618 |16 |De naam van de firewall regel mag niet leeg zijn. |
| 40620 |16 |De aanmelding is mislukt voor gebruiker%. &#x2a;ls. Het wijzigen van het wacht woord is mislukt. Het wijzigen van het wacht woord tijdens het aanmelden wordt niet ondersteund in deze versie van SQL Server. |
| 40627 |20 |De bewerking op de server {0} en de data base {1} wordt uitgevoerd. Wacht een paar minuten voordat u het opnieuw probeert. |
| 40630 |16 |Wachtwoord validatie is mislukt. Het wacht woord voldoet niet aan de beleids vereisten omdat het te kort is. |
| 40631 |16 |Het wacht woord dat u hebt opgegeven, is te lang. Het wacht woord mag niet langer zijn dan 128 tekens. |
| 40632 |16 |Wachtwoord validatie is mislukt. Het wacht woord voldoet niet aan de beleids vereisten omdat het niet complex genoeg is. |
| 40636 |16 |Kan de naam van de gereserveerde data base% niet gebruiken. &#x2a;ls ' in deze bewerking. |
| 40638 |16 |Ongeldige abonnements-id (abonnements-id). Het abonnement bestaat niet. |
| 40639 |16 |De aanvraag voldoet niet aan het schema: (schema fout). |
| 40640 |20 |Er is een onverwachte uitzonde ring opgetreden op de server. |
| 40641 |16 |De opgegeven locatie is ongeldig. |
| 40642 |17 |De server is momenteel bezet. Probeer het later opnieuw. |
| 40643 |16 |De opgegeven waarde voor de x-MS-version-header is ongeldig. |
| 40644 |14 |Kan geen toegang verlenen aan het opgegeven abonnement. |
| 40645 |16 |Servername (servername) mag niet leeg of null zijn. De naam kan alleen bestaan uit kleine letters a-z, de getallen 0-9 en het afbreek streepje. Het koppel teken mag niet leiden naar of volgen in de naam. |
| 40646 |16 |Abonnements-ID mag niet leeg zijn. |
| 40647 |16 |Het abonnement (abonnement-id) heeft geen server (servername). |
| 40648 |17 |Er zijn te veel aanvragen uitgevoerd. Probeer het later opnieuw. |
| 40649 |16 |Er is een ongeldig inhouds type opgegeven. Alleen Application/XML wordt ondersteund. |
| 40650 |16 |Het abonnement (abonnement-id) bestaat niet of is niet gereed voor de bewerking. |
| 40651 |16 |Het maken van de server is mislukt omdat het abonnement (abonnements-id) is uitgeschakeld. |
| 40652 |16 |Kan de server niet verplaatsen of maken. Het abonnement (abonnements-id) overschrijdt het quotum van de server. |
| 40671 |17 |Communicatie fout tussen de gateway en de beheer service. Probeer het later opnieuw. |
| 40852 |16 |Kan de data base% niet openen. \*ls op server%. \*ls ' aangevraagd door de aanmelding. Toegang tot de data base is alleen toegestaan met behulp van een connection string met ingeschakelde beveiliging. Als u toegang wilt krijgen tot deze data base, wijzigt u de verbindings reeksen zodat deze ' Secure ' bevat in de server-FQDN-' server naam '. data base. Windows. net moet worden gewijzigd in ' server naam '. data base. `secure`. windows.net. |
| 40914 | 16 | Kan de server *[Server naam]* die door de aanmelding is aangevraagd, niet openen. De client is niet gemachtigd om toegang te krijgen tot de server.<br /><br />U kunt dit probleem oplossen door een regel voor het [virtuele netwerk](sql-database-vnet-service-endpoint-rule-overview.md)toe te voegen. |
| 45168 |16 |Het SQL Azure-systeem wordt geladen en er wordt een bovengrens voor het uitvoeren van gelijktijdige DB-bewerkingen op een enkele SQL Database Server (bijvoorbeeld data base maken) geplaatst. De server die in het fout bericht is opgegeven, overschrijdt het maximum aantal gelijktijdige verbindingen. Probeer het later opnieuw. |
| 45169 |16 |Het SQL Azure-systeem wordt geladen en er wordt een bovengrens voor het aantal gelijktijdige server ruwe bewerkingen voor één abonnement (bijvoorbeeld server maken) geplaatst. Het in het fout bericht opgegeven abonnement heeft het maximum aantal gelijktijdige verbindingen overschreden en de aanvraag is geweigerd. Probeer het later opnieuw. |

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [Azure SQL database-functies](sql-database-features.md).
* Meer informatie over [het op DTU gebaseerde aankoop model](sql-database-service-tiers-dtu.md).
* Meer informatie over [het op vCore gebaseerde aankoop model](sql-database-service-tiers-vcore.md).

