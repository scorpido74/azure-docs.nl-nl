---
title: Actieve geo-replicatie
description: Gebruik actieve georeplicatie om leesbare secundaire databases van afzonderlijke databases in hetzelfde of verschillende datacenter (regio) te maken.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 02/17/2020
ms.openlocfilehash: fe006cebe9aab30a6aaa0bdf2bf3362a494f64d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77426270"
---
# <a name="creating-and-using-active-geo-replication"></a>Actieve georeplicatie maken en gebruiken

Actieve georeplicatie is een Azure SQL Database-functie waarmee u leesbare secundaire databases van afzonderlijke databases op een SQL Database-server in hetzelfde of verschillende datacenter (regio) maken.

> [!NOTE]
> Actieve georeplicatie wordt niet ondersteund door beheerde instantie. Voor geografische failover van beheerde exemplaren gebruikt u [automatisch failovergroepen](sql-database-auto-failover-group.md).

Actieve geo-replicatie is ontworpen als een bedrijfscontinuïteitsoplossing waarmee de toepassing snel herstel van individuele databases kan uitvoeren in geval van een regionale ramp of grootschalige uitval. Als geo-replicatie is ingeschakeld, kan de toepassing failover starten naar een secundaire database in een andere Azure-regio. Maximaal vier secondaries worden ondersteund in dezelfde of verschillende regio's, en de secondaries kunnen ook worden gebruikt voor alleen-lezen toegangsquery's. De failover moet handmatig worden gestart door de toepassing of de gebruiker. Na failover heeft de nieuwe primaire een ander verbindingseindpunt. In het volgende diagram wordt een typische configuratie van een georedundante cloudtoepassing geïllustreerd met actieve georeplicatie.

![actieve georeplicatie](./media/sql-database-active-geo-replication/geo-replication.png )

> [!IMPORTANT]
> SQL Database ondersteunt ook auto-failovergroepen. Zie voor meer informatie [auto-failovergroepen gebruiken](sql-database-auto-failover-group.md). Actieve georeplicatie wordt ook niet ondersteund voor databases die zijn gemaakt in een beheerde instantie. Overweeg [failovergroepen](sql-database-auto-failover-group.md) te gebruiken met beheerde instanties.

Als uw primaire database om welke reden dan ook mislukt of gewoon offline moet worden gehaald, u failover starten naar een van uw secundaire databases. Wanneer failover wordt geactiveerd in een van de secundaire databases, worden alle andere secondaries automatisch gekoppeld aan de nieuwe primaire.

U replicatie en failover van een afzonderlijke database of een set databases op een server of in een elastische groep beheren met behulp van actieve georeplicatie. Dat kan met:

- De [Azure-portal](sql-database-geo-replication-portal.md)
- [PowerShell: enkele database](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
- [PowerShell: Elastische pool](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)
- [Transact-SQL: Enkele database of elastische pool](/sql/t-sql/statements/alter-database-azure-sql-database)
- [REST API: Enkele database](https://docs.microsoft.com/rest/api/sql/replicationlinks)


Actieve georeplicatie maakt gebruik van de [Always On-technologie](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server) van SQL Server om vastgelegde transacties in de primaire database asynchroon te repliceren naar een secundaire database met behulp van momentopnameisolatie. Auto-failovergroepen bieden de groepsemantiek bovenop actieve georeplicatie, maar hetzelfde asynchrone replicatiemechanisme wordt gebruikt. Terwijl op een bepaald punt, de secundaire database kan iets achter de primaire database, de secundaire gegevens is gegarandeerd nooit gedeeltelijke transacties. Met redundantie tussen gebieden kunnen toepassingen snel herstellen van een permanent verlies van een volledig datacenter of delen van een datacenter dat wordt veroorzaakt door natuurrampen, catastrofale menselijke fouten of kwaadwillige handelingen. De specifieke RPO-gegevens zijn te vinden op [Overview of Business Continuity.](sql-database-business-continuity.md)

> [!NOTE]
> Als er een netwerkstoring is tussen twee regio's, proberen we elke 10 seconden opnieuw verbindingen tot stand te brengen.
> [!IMPORTANT]
> Als u wilt garanderen dat een kritieke wijziging in de primaire database wordt gerepliceerd naar een secundaire voordat u mislukt, u synchronisatie forceren om de replicatie van kritieke wijzigingen (bijvoorbeeld wachtwoordupdates) te garanderen. Gedwongen synchronisatie heeft invloed op de prestaties omdat de aanroepthread wordt blokkeert totdat alle vastgelegde transacties zijn gerepliceerd. Zie [sp_wait_for_database_copy_sync](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync)voor meer informatie. Zie [sys.dm_geo_replication_link_status](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database)om de replicatievertraging tussen de primaire database en geo-secundair te controleren.

De volgende afbeelding toont een voorbeeld van actieve geo-replicatie geconfigureerd met een primaire in de regio Noord Centraal VS en secundair in de regio Zuid Centraal VS.

![georeplicatierelatie](./media/sql-database-active-geo-replication/geo-replication-relationship.png)

Omdat de secundaire databases leesbaar zijn, kunnen ze worden gebruikt om alleen-lezen workloads te ontladen, zoals het rapporteren van taken. Als u actieve georeplicatie gebruikt, is het mogelijk om de secundaire database in dezelfde regio met de primaire regio te maken, maar het verhoogt de veerkracht van de toepassing niet tegen catastrofale fouten. Als u auto-failovergroepen gebruikt, wordt uw secundaire database altijd in een andere regio gemaakt.

Naast disaster recovery kan actieve georeplicatie worden gebruikt in de volgende scenario's:

- **Databasemigratie:** U actieve georeplicatie gebruiken om een database van de ene server naar de andere online te migreren met minimale downtime.
- **Toepassingsupgrades:** U een extra secundaire als een fail back-kopie tijdens toepassingsupgrades maken.

Om echte bedrijfscontinuïteit te bereiken, is het toevoegen van databaseredundantie tussen datacenters slechts een deel van de oplossing. Het herstellen van een toepassing (service) end-to-end na een catastrofale fout vereist herstel van alle onderdelen die de service en eventuele afhankelijke services vormen. Voorbeelden van deze componenten zijn de clientsoftware (bijvoorbeeld een browser met een aangepaste JavaScript), webfrontends, opslag en DNS. Het is van cruciaal belang dat alle componenten bestand zijn tegen dezelfde fouten en beschikbaar komen binnen de hersteltijddoelstelling (RTO) van uw toepassing. Daarom moet u alle afhankelijke services identificeren en de garanties en mogelijkheden begrijpen die ze bieden. Vervolgens moet u voldoende maatregelen nemen om ervoor te zorgen dat uw service functioneert tijdens het mislukken van de services waarvan deze afhankelijk is. Zie [Cloudoplossingen ontwerpen voor noodherstel voor](sql-database-designing-cloud-solutions-for-disaster-recovery.md)meer informatie over het ontwerpen van oplossingen voor noodherstel met behulp van actieve georeplicatie.

## <a name="active-geo-replication-terminology-and-capabilities"></a>Actieve terminologie en mogelijkheden voor georeplicatie

- **Automatische asynchrone replicatie**

  U alleen een secundaire database maken door toe te voegen aan een bestaande database. Het secundaire kan worden gemaakt in elke Azure SQL Database-server. Eenmaal gemaakt, wordt de secundaire database gevuld met de gegevens die zijn gekopieerd uit de primaire database. Dit proces staat bekend als zaaien. Nadat de secundaire database is gemaakt en gezaaid, worden updates van de primaire database automatisch gerepliceerd naar de secundaire database. Asynchrone replicatie betekent dat transacties worden vastgelegd in de primaire database voordat ze worden gerepliceerd naar de secundaire database.

- **Leesbare secundaire databases**

  Een toepassing heeft toegang tot een secundaire database voor alleen-lezen bewerkingen met dezelfde of verschillende beveiligingsprincipals die worden gebruikt voor toegang tot de primaire database. De secundaire databases werken in de snapshot isolatiemodus om ervoor te zorgen dat replicatie van de updates van de primaire (logboekherhaling) niet wordt vertraagd door query's die op het secundaire worden uitgevoerd.

> [!NOTE]
> De herhaling van het logboek wordt vertraagd in de secundaire database als er schema-updates zijn op het primaire werk. Dit laatste vereist een schemavergrendeling in de secundaire database.
> [!IMPORTANT]
> U geo-replicatie gebruiken om een secundaire database te maken in hetzelfde gebied als het primaire. U deze secundaire gebruiken om een alleen-lezen workloads in dezelfde regio te laden. Een secundaire database in dezelfde regio biedt echter geen extra foutbestendigheid en is daarom geen geschikt failover-doel voor noodherstel. Het zal ook geen garantie beschikbaarheid zone isolatie. Gebruik bedrijfskritieke of Premium-servicelaag met [zoneredundante configuratie](sql-database-high-availability.md#zone-redundant-configuration) om de isolatie van de beschikbaarheidszone te bereiken.   
>

- **Geplande failover**

  Geplande failoverschakelt de rollen van primaire en secundaire databases nadat de volledige synchronisatie is voltooid. Het is een online bewerking die niet leidt tot gegevensverlies. De tijd van de bewerking is afhankelijk van de grootte van het transactielogboek op het primaire dat moet worden gesynchroniseerd. Geplande failover is bedoeld voor volgende scenario's: a) dr-drills in productie uit te voeren wanneer het verlies van gegevens niet aanvaardbaar is; b) de databank naar een andere regio te verplaatsen; en (c) om de database terug te sturen naar het primaire gebied nadat de storing is verholpen (failback).

- **Niet-geplande failover**

  Ongeplande of geforceerde failover schakelt onmiddellijk het secundaire naar de primaire rol zonder synchronisatie met de primaire. Alle transacties die zijn vastgelegd in de primaire, maar niet gerepliceerd naar de secundaire zal verloren gaan. Deze bewerking is ontworpen als een herstelmethode tijdens uitval wanneer de primaire niet toegankelijk is, maar de beschikbaarheid van de database moet snel worden hersteld. Wanneer de oorspronkelijke primaire is weer online zal het automatisch opnieuw verbinding maken en uitgegroeid tot een nieuwe secundaire. Alle niet-gesynchroniseerde transacties voordat de failover worden bewaard in het back-upbestand, maar worden niet gesynchroniseerd met het nieuwe primaire om conflicten te voorkomen. Deze transacties moeten handmatig worden samengevoegd met de meest recente versie van de primaire database.
 
- **Meerdere leesbare secondaries**

  Voor elke primaire kunnen maximaal 4 secundaire databases worden gemaakt. Als er slechts één secundaire database is en deze mislukt, wordt de toepassing blootgesteld aan een hoger risico totdat een nieuwe secundaire database is gemaakt. Als er meerdere secundaire databases bestaan, blijft de toepassing beschermd, zelfs als een van de secundaire databases mislukt. De extra secondaries kunnen ook worden gebruikt om de alleen-lezen workloads uit te schalen

  > [!NOTE]
  > Als u actieve georeplicatie gebruikt om een wereldwijd gedistribueerde toepassing te bouwen en alleen-lezen toegang tot gegevens in meer dan vier regio's moet bieden, u secundaire secundaire (een proces dat bekend staat als chaining) maken. Op deze manier u vrijwel onbeperkte schaal van databasereplicatie bereiken. Bovendien vermindert chaining de overhead van replicatie uit de primaire database. De trade-off is de toegenomen replicatievertraging op de meest secundaire databases.

- **Geo-replicatie van databases in een elastische groep**

  Elke secundaire database kan afzonderlijk deelnemen aan een elastische pool of helemaal niet in een elastische pool zitten. De poolkeuze voor elke secundaire database is gescheiden en is niet afhankelijk van de configuratie van een andere secundaire database (primair of secundair). Elke elastische pool is opgenomen in één regio, daarom kunnen meerdere secundaire databases in dezelfde topologie nooit een elastische pool delen.


- **Door de gebruiker gecontroleerde failover en failback**

  Een secundaire database kan op elk moment door de toepassing of de gebruiker expliciet worden overgeschakeld naar de primaire rol. Tijdens een echte uitval moet de "ongeplande" optie worden gebruikt, die onmiddellijk een secundaire bevordert om de primaire te zijn. Wanneer de mislukte primaire herstelt en weer beschikbaar is, markeert het systeem automatisch de herstelde primaire als een secundaire en brengt het up-to-date met de nieuwe primaire. Vanwege de asynchrone aard van replicatie kan een kleine hoeveelheid gegevens verloren gaan tijdens ongeplande failovers als een primaire mislukt voordat de meest recente wijzigingen in het secundaire worden gerepliceerd. Wanneer een primaire met meerdere secondaries mislukt, configureert het systeem automatisch de replicatierelaties en koppelt het de resterende secondaries aan de nieuw gepromoveerde primaire zonder tussenkomst van de gebruiker. Nadat de storing die de failover heeft veroorzaakt, is verholpen, kan het wenselijk zijn om de toepassing terug te sturen naar de primaire regio. Om dat te doen, moet de failover opdracht worden aangeroepen met de optie "gepland".

## <a name="preparing-secondary-database-for-failover"></a>Secundaire database voorbereiden op failover

Om ervoor te zorgen dat uw toepassing direct toegang heeft tot de nieuwe primaire na failover, moet u ervoor zorgen dat de verificatievereisten voor uw secundaire server en database correct zijn geconfigureerd. Zie SQL [Database-beveiliging na herstel na noodgevallen](sql-database-geo-replication-security-config.md)voor meer informatie . Als u naleving na failover wilt garanderen, moet u ervoor zorgen dat het beleid voor het bewaren van back-ups op de secundaire database overeenkomt met dat van de primaire database. Deze instellingen maken geen deel uit van de database en worden niet gerepliceerd. Standaard wordt de secundaire geconfigureerd met een standaard PITR-bewaartermijn van zeven dagen. Zie SQL [Database geautomatiseerde back-ups voor](sql-database-automated-backups.md)meer informatie.

> [!IMPORTANT]
> Als uw database lid is van een failovergroep, u de failover niet starten met de opdracht georeplicatiefaiover. Overweeg de opdracht failover voor de groep te gebruiken. Als u een afzonderlijke database moet failoveren, moet u deze eerst uit de failovergroep verwijderen. Zie [failovergroepen](sql-database-auto-failover-group.md) voor meer informatie. 


## <a name="configuring-secondary-database"></a>Secundaire database configureren

Zowel primaire als secundaire databases moeten dezelfde servicelaag hebben. Het wordt ook sterk aanbevolen dat secundaire database wordt gemaakt met dezelfde compute size (DTU's of vCores) als de primaire. Als de primaire database een zware schrijfwerkbelasting ondervindt, kan een secundaire met een lagere rekengrootte deze mogelijk niet bijhouden. Het zal leiden tot de redo lag op de secundaire en potentiële onbeschikbaarheid. Een secundaire database die achterloopt op de primaire risico's ook een groot verlies van gegevens moet een gedwongen failover nodig zijn. Om deze risico's te beperken, zal effectieve actieve geo-replicatie de logsnelheid van de primaire beperken om de secondaries in te staan om in te halen. Het andere gevolg van een onevenwichtige secundaire configuratie is dat na failover de prestaties van de toepassing zal lijden als gevolg van onvoldoende rekencapaciteit van de nieuwe primaire. Het zal nodig zijn om te upgraden naar een hoger rekenniveau naar het noodzakelijke niveau, wat niet mogelijk zal zijn totdat de storing is beperkt. 


> [!IMPORTANT]
> De gepubliceerde RPO = 5 sec kan niet worden gegarandeerd, tenzij de secundaire database is geconfigureerd met dezelfde rekengrootte als de primaire. 


Als u besluit de secundaire met lagere rekengrootte te maken, biedt de log IO-percentagegrafiek op Azure-portal een goede manier om de minimale rekengrootte van het secundaire te schatten dat nodig is om de replicatiebelasting te ondersteunen. Als uw primaire database bijvoorbeeld P6 (1000 DTU) is en het IO-percentage van het logboek 50% is, moet de secundaire moet ten minste P4 (500 DTU) zijn. U de IO-gegevens van het logboek ook ophalen met [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) of [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) databaseweergaven.  De beperking wordt gerapporteerd als een HADR_THROTTLE_LOG_RATE_MISMATCHED_SLO wachtstatus in de [databaseweergaven sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) en [sys.dm_os_wait_stats.](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql) 

Zie [Wat sql databaseservicelagen zijn SQL Database Service Tiers](sql-database-purchase-models.md)voor meer informatie over de compute sizes van SQL Database.

## <a name="cross-subscription-geo-replication"></a>Georeplicatie met een abonnement

Als u actieve georeplicatie wilt instellen tussen twee databases die behoren tot verschillende abonnementen (al dan niet onder dezelfde tenant), moet u de speciale procedure volgen die in deze sectie wordt beschreven.  De procedure is gebaseerd op SQL-opdrachten en vereist: 

- Een bevoorrechte aanmelding op beide servers maken
- Het IP-adres toevoegen aan de lijst met toegestane functies van de client die de wijziging uitvoert op beide servers (zoals het IP-adres van de host met SQL Server Management Studio). 

De client die de wijzigingen uitvoert, heeft netwerktoegang tot de primaire server nodig. Hoewel hetzelfde IP-adres van de client moet worden toegevoegd aan de lijst met toegestane op de secundaire server, is netwerkconnectiviteit met de secundaire server niet strikt vereist. 

### <a name="on-the-master-of-the-primary-server"></a>Op de master van de primaire server

1. Voeg het IP-adres toe aan de lijst toestaan van de client die de wijzigingen uitvoert (zie voor meer informatie [firewall configureren).](sql-database-firewall-configure.md) 
1. Maak een login die is gewijd aan het instellen van actieve georeplicatie (en pas de referenties aan als dat nodig is):

   ```sql
   create login geodrsetup with password = 'ComplexPassword01'
   ```

1. Maak een overeenkomstige gebruiker en wijs deze toe aan de rol dbmanager: 

   ```sql
   create user geodrsetup for login geodrsetup
   alter role geodrsetup dbmanager add member geodrsetup
   ```

1. Let op de SID van de nieuwe login met behulp van deze query: 

   ```sql
   select sid from sys.sql_logins where name = 'geodrsetup'
   ```

### <a name="on-the-source-database-on-the-primary-server"></a>Op de brondatabase op de primaire server

1. Maak een gebruiker voor dezelfde aanmelding:

   ```sql
   create user geodrsetup for login geodrsetup
   ```

1. Voeg de gebruiker toe aan de db_owner rol:

   ```sql
   alter role db_owner add member geodrsetup
   ```

### <a name="on-the-master-of-the-secondary-server"></a>Op de master van de secundaire server 

1. Voeg het IP-adres toe aan de lijst met toegestane gegevens van de client die de wijzigingen uitvoert. Het moet hetzelfde exacte IP-adres van de primaire server. 
1. Maak dezelfde aanmelding als op de primaire server, met hetzelfde gebruikersnaamwachtwoord en SID: 

   ```sql
   create login geodrsetup with password = 'ComplexPassword01', sid=0x010600000000006400000000000000001C98F52B95D9C84BBBA8578FACE37C3E
   ```

1. Maak een overeenkomstige gebruiker en wijs deze toe aan de rol dbmanager:

   ```sql
   create user geodrsetup for login geodrsetup;
   alter role dbmanager add member geodrsetup
   ```

### <a name="on-the-master-of-the-primary-server"></a>Op de master van de primaire server

1. Log in op de master van de primaire server met behulp van de nieuwe login. 
1. Maak een secundaire replica van de brondatabase op de secundaire server (pas de naam van de database en de servernaam zo nodig aan):

   ```sql
   alter database dbrep add secondary on server <servername>
   ```

Na de eerste installatie kunnen de gebruikers, aanmeldingen en firewallregels die zijn gemaakt, worden verwijderd. 


## <a name="keeping-credentials-and-firewall-rules-in-sync"></a>Referenties en firewallregels synchroon houden

We raden u aan [ip-firewallregels op databaseniveau](sql-database-firewall-configure.md) te gebruiken voor geo-gerepliceerde databases, zodat deze regels met de database kunnen worden gerepliceerd om ervoor te zorgen dat alle secundaire databases dezelfde IP-firewallregels hebben als de primaire. Deze aanpak elimineert de noodzaak voor klanten om handmatig firewallregels te configureren en te onderhouden op servers die zowel de primaire als secundaire databases hosten. Op dezelfde manier zorgt het gebruik van [opgenomen databasegebruikers](sql-database-manage-logins.md) voor gegevenstoegang ervoor dat zowel primaire als secundaire databases altijd dezelfde gebruikersreferenties hebben, zodat er tijdens een fail-over geen onderbrekingen zijn als gevolg van mismatches met aanmeldingen en wachtwoorden. Met de toevoeging van [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md)kunnen klanten gebruikerstoegang tot zowel primaire als secundaire databases beheren en de noodzaak voor het beheren van referenties in databases helemaal elimineren.

## <a name="upgrading-or-downgrading-primary-database"></a>Primaire database upgraden of downgraden

U een primaire database upgraden of downgraden naar een andere rekengrootte (binnen dezelfde servicelaag, niet tussen Algemeen Doel en Bedrijfskritiek) zonder de verbinding met secundaire databases te verbreken. Bij het upgraden raden we u aan eerst de secundaire database te upgraden en vervolgens de primaire database te upgraden. Bij het downgraden de volgorde omkeren: eerst de primaire downgrade en vervolgens de secundaire downgrade. Wanneer u de database upgradet of downgraden naar een andere servicelaag, wordt deze aanbeveling afgedwongen.

> [!NOTE]
> Als u secundaire database hebt gemaakt als onderdeel van de failovergroepconfiguratie, wordt het niet aanbevolen om de secundaire database te downgraden. Dit is om ervoor te zorgen dat uw gegevenslaag voldoende capaciteit heeft om uw reguliere werkbelasting te verwerken nadat failover is geactiveerd.

> [!IMPORTANT]
> De primaire database in een failovergroep kan niet worden geschaald naar een hogere laag, tenzij de secundaire database eerst wordt geschaald naar de hogere laag. Als u de primaire database probeert te schalen voordat de secundaire database wordt geschaald, ontvangt u mogelijk de volgende fout:
>
> `Error message: The source database 'Primaryserver.DBName' cannot have higher edition than the target database 'Secondaryserver.DBName'. Upgrade the edition on the target before upgrading the source.`
>

## <a name="preventing-the-loss-of-critical-data"></a>Het verlies van kritieke gegevens voorkomen

Vanwege de hoge latentie van breedgebiednetwerken maakt continue kopie gebruik van een asynchrone replicatiemechanisme. Asynchrone replicatie maakt sommige gegevensverlies onvermijdelijk als er een fout optreedt. Sommige toepassingen vereisen echter mogelijk geen gegevensverlies. Om deze kritieke updates te beschermen, kan een toepassingsontwikkelaar de [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) systeemprocedure onmiddellijk na het begaan van de transactie aanroepen. Bellen **sp_wait_for_database_copy_sync** blokkeert de aanroepthread totdat de laatst vastgelegde transactie is verzonden naar de secundaire database. Het wacht echter niet tot de verzonden transacties worden afgespeeld en vastgelegd op de secundaire. **sp_wait_for_database_copy_sync** is scoped naar een specifieke continue kopie link. Elke gebruiker met de verbindingsrechten van de primaire database kan deze procedure aanroepen.

> [!NOTE]
> **sp_wait_for_database_copy_sync** voorkomt gegevensverlies na failover, maar garandeert geen volledige synchronisatie voor leestoegang. De vertraging veroorzaakt door een **sp_wait_for_database_copy_sync** proceduregesprek kan aanzienlijk zijn en is afhankelijk van de grootte van het transactielogboek op het moment van de oproep.

## <a name="monitoring-geo-replication-lag"></a>Georeplicatievertraging bewaken

Gebruik *replication_lag_sec* kolom van [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database) op de primaire database om vertraging ten opzichte van RPO te controleren. Het toont vertraging in seconden tussen de transacties die op de primaire en bleef bestaan op de secundaire. Bijvoorbeeld als de waarde van de vertraging 1 seconde is, betekent dit dat als de primaire wordt beïnvloed door een storing op dit moment en failover wordt gestart, 1 seconde van de meest recente overgangen niet worden opgeslagen. 

Als u de vertraging wilt meten met betrekking tot wijzigingen in de primaire database die zijn toegepast op de secundaire database, d.w.z. beschikbaar om te lezen vanaf de secundaire, vergelijkt *u last_commit* tijd in de secundaire database met dezelfde waarde op de primaire database.

> [!NOTE]
> Soms heeft *replication_lag_sec* op de primaire database een NULL-waarde, wat betekent dat de primaire momenteel niet weet hoe ver het secundaire is.   Dit gebeurt meestal na het opnieuw opstarten van het proces en moet een tijdelijke voorwaarde zijn. U de toepassing waarschuwen als de *replication_lag_sec* NULL voor een langere periode retourneert. Het zou erop wijzen dat de secundaire database niet kan communiceren met de primaire als gevolg van een permanente verbindingsfout. Er zijn ook voorwaarden die kunnen leiden tot het verschil tussen *last_commit* tijd op de secundaire en op de primaire database groot te worden. Bijvoorbeeld als een commit wordt gemaakt op de primaire na een lange periode van geen wijzigingen, zal het verschil springen tot een grote waarde voordat snel terug te keren naar 0. Beschouw het als een foutvoorwaarde wanneer het verschil tussen deze twee waarden lange tijd groot blijft.


## <a name="programmatically-managing-active-geo-replication"></a>Programmatisch beheer van actieve georeplicatie

Zoals eerder besproken, kan actieve geo-replicatie ook programmatisch worden beheerd met Azure PowerShell en de REST API. In de volgende tabellen worden de beschikbare set opdrachten beschreven. Actieve georeplicatie omvat een set Azure Resource Manager API's voor beheer, waaronder de [Azure SQL Database REST API](https://docs.microsoft.com/rest/api/sql/) en Azure [PowerShell-cmdlets.](https://docs.microsoft.com/powershell/azure/overview) Deze API's vereisen het gebruik van resourcegroepen en ondersteuningsrole-based security (RBAC). Zie [Azure Role-Based Access Control](../role-based-access-control/overview.md)voor meer informatie over het implementeren van toegangsrollen.

### <a name="t-sql-manage-failover-of-single-and-pooled-databases"></a>T-SQL: Failover van afzonderlijke en gepoolde databases beheren

> [!IMPORTANT]
> Deze Transact-SQL-opdrachten zijn alleen van toepassing op actieve georeplicatie en zijn niet van toepassing op failovergroepen. Als zodanig zijn ze ook niet van toepassing op beheerde instanties, omdat ze alleen failovergroepen ondersteunen.

| Opdracht | Beschrijving |
| --- | --- |
| [DATABASE WIJZIGEN](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current) |Argument SECUNDAIR e-server TOEVOEGEN gebruiken om een secundaire database voor een bestaande database te maken en gegevensreplicatie te starten |
| [DATABASE WIJZIGEN](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current) |Failover gebruiken of FORCE_FAILOVER_ALLOW_DATA_LOSS om een secundaire database over te schakelen om primaire failover te starten |
| [DATABASE WIJZIGEN](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current) |Gebruik SECUNDAIR VERWIJDEREN OP SERVER om een gegevensreplicatie tussen een SQL-database en de opgegeven secundaire database te beëindigen. |
| [sys.geo_replication_links](/sql/relational-databases/system-dynamic-management-views/sys-geo-replication-links-azure-sql-database) |Retourneert informatie over alle bestaande replicatiekoppelingen voor elke database op de Azure SQL Database-server. |
| [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database) |Krijgt de laatste replicatietijd, laatste replicatievertraging en andere informatie over de replicatiekoppeling voor een bepaalde SQL-database. |
| [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) |Hiermee wordt de status weergegeven voor alle databasebewerkingen, inclusief de status van de replicatiekoppelingen. |
| [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) |zorgt ervoor dat de toepassing wacht tot alle toegezegde transacties worden gerepliceerd en erkend door de actieve secundaire database. |
|  | |

### <a name="powershell-manage-failover-of-single-and-pooled-databases"></a>PowerShell: failover van afzonderlijke en gepoolde databases beheren

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> De PowerShell Azure Resource Manager-module wordt nog steeds ondersteund door Azure SQL Database, maar alle toekomstige ontwikkelingen zijn voor de Az.Sql-module. Zie [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)voor deze cmdlets. De argumenten voor de opdrachten in de Az-module en in de AzureRm-modules zijn nagenoeg identiek.

| Cmdlet | Beschrijving |
| --- | --- |
| [Get-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabase) |Hiermee haalt u een of meer databases op. |
| [Nieuw-AzSqlDatabaseSecundair](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabasesecondary) |Hiermee maakt u een secundaire database voor een bestaande database en wordt gegevensreplicatie gestart. |
| [Set-AzSqlDatabaseSecundair](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasesecondary) |Hiermee wordt overgeschakeld op een secundaire database als primaire om de failover te initiëren. |
| [Remove-AzSqlDatabaseSecondary](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabasesecondary) |Hiermee wordt gegevensreplicatie tussen een SQL Database en de opgegeven secundaire database beëindigd. |
| [Get-azsqldatabasereplicatielink](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasereplicationlink) |Hiermee haalt u de geo-replicatiekoppelingen tussen een Azure SQL Database en een resourcegroep of SQL Server op. |
|  | |

> [!IMPORTANT]
> Zie [Een enkele database configureren en failoveren voor](scripts/sql-database-setup-geodr-and-failover-database-powershell.md) voorbeeldscripts met actieve georeplicatie en Een [gepoolde database configureren en mislukken met behulp van actieve georeplicatie.](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)

### <a name="rest-api-manage-failover-of-single-and-pooled-databases"></a>REST API: Failover van afzonderlijke en gepoolde databases beheren

| API | Beschrijving |
| --- | --- |
| [Database maken of bijwerken (createMode=Restore)](https://docs.microsoft.com/rest/api/sql/databases/createorupdate) |Hiermee maakt, wordt een primaire of een secundaire database gemaakt, bijgewerkt of hersteld. |
| [Databasestatus maken of bijwerken](https://docs.microsoft.com/rest/api/sql/databases/createorupdate) |Geeft als resultaat de status tijdens een bewerking maken. |
| [Secundaire database instellen als primair (geplande failover)](https://docs.microsoft.com/rest/api/sql/replicationlinks/failover) |Hiermee stelt u in welke secundaire database primair is door niet meer uit de huidige primaire database te komen. **Deze optie wordt niet ondersteund voor Beheerde instantie.**|
| [Secundaire database instellen als primair (mislukte failover)](https://docs.microsoft.com/rest/api/sql/replicationlinks/failoverallowdataloss) |Hiermee stelt u in welke secundaire database primair is door niet meer uit de huidige primaire database te komen. Deze bewerking kan leiden tot gegevensverlies. **Deze optie wordt niet ondersteund voor Beheerde instantie.**|
| [Replicatiekoppeling weermaken](https://docs.microsoft.com/rest/api/sql/replicationlinks/get) |Krijgt een specifieke replicatiekoppeling voor een bepaalde SQL-database in een geo-replicatiepartnerschap. Hiermee wordt de informatie opgehaald die zichtbaar is in de catalogusweergave van sys.geo_replication_links. **Deze optie wordt niet ondersteund voor Beheerde instantie.**|
| [Replicatiekoppelingen - Lijst per database](https://docs.microsoft.com/rest/api/sql/replicationlinks/listbydatabase) | Haalt alle replicatiekoppelingen voor een bepaalde SQL-database in een geo-replicatiepartnerschap. Hiermee wordt de informatie opgehaald die zichtbaar is in de catalogusweergave van sys.geo_replication_links. |
| [Replicatiekoppeling verwijderen](https://docs.microsoft.com/rest/api/sql/replicationlinks/delete) | Hiermee verwijdert u een koppeling naar databasereplicatie. Kan niet worden gedaan tijdens failover. |
|  | |

## <a name="next-steps"></a>Volgende stappen

- Zie voor voorbeeldscripts:
  - [PowerShell gebruiken voor het configureren van actieve geo-replicatie voor één Azure SQL-database](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
  - [PowerShell gebruiken voor het configureren van actieve geo-replicatie voor een Azure SQL-pooldatabase](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)
- SQL Database ondersteunt ook auto-failovergroepen. Zie voor meer informatie [auto-failovergroepen gebruiken](sql-database-auto-failover-group.md).
- Zie [Bedrijfscontinuïteitsoverzicht](sql-database-business-continuity.md) voor een overzicht van bedrijfscontinuïteiten
- Zie [geautomatiseerde back-ups van SQL Database](sql-database-automated-backups.md)voor meer informatie over geautomatiseerde back-ups van Azure SQL Database.
- Zie [Een database herstellen van de door de service gestarte back-ups](sql-database-recovery-using-backups.md)voor meer informatie over het gebruik van geautomatiseerde back-ups voor herstel.
- Zie [SQL Database-beveiliging na herstel na noodgevallen](sql-database-geo-replication-security-config.md)voor meer informatie over verificatievereisten voor een nieuwe primaire server en database.
