---
title: Failover-groepen
description: Auto-failovergroepen is een SQL Database-functie waarmee u replicatie en automatische / gecoördineerde failover van een groep databases op een SQL Database-server of alle databases in beheerde instantie beheren.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 2/10/2020
ms.openlocfilehash: 6d87d3373711d12df3f2cced26ef35ae951ad41e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79269833"
---
# <a name="use-auto-failover-groups-to-enable-transparent-and-coordinated-failover-of-multiple-databases"></a>Auto-failovergroepen gebruiken om een transparante en gecoördineerde failover van meerdere databases mogelijk te maken

Auto-failovergroepen is een SQL Database-functie waarmee u replicatie en failover van een groep databases op een SQL Database-server of alle databases in een beheerde instantie naar een andere regio beheren. Het is een declaratieve abstractie bovenop de bestaande [actieve georeplicatiefunctie,](sql-database-active-geo-replication.md) ontworpen om de implementatie en het beheer van geo-gerepliceerde databases op schaal te vereenvoudigen. U failover handmatig starten of u deze delegeren aan de SQL Database-service op basis van een door de gebruiker gedefinieerd beleid. Met deze laatste optie u automatisch meerdere gerelateerde databases in een secundaire regio herstellen na een catastrofale fout of een andere ongeplande gebeurtenis die resulteert in volledig of gedeeltelijk verlies van de beschikbaarheid van de SQL Database-service in de primaire regio. Een failovergroep kan een of meerdere databases bevatten, die doorgaans door dezelfde toepassing worden gebruikt. Bovendien u de leesbare secundaire databases gebruiken om alleen-lezen queryworkloads te ontladen. Omdat groepen met automatische failover meerdere databases bevatten, moeten deze databases worden geconfigureerd op de primaire server. Groepen met automatische failover ondersteunen replicatie van alle databases in de groep naar slechts één secundaire server in een andere regio.

> [!NOTE]
> Wanneer u werkt met afzonderlijke of gepoolde databases op een SQL Database-server en u wilt dat meerdere secondaries in dezelfde of verschillende regio's worden gebruikt, gebruikt u [actieve georeplicatie.](sql-database-active-geo-replication.md) 

Wanneer u automatische failovergroepen gebruikt met een automatisch failoverbeleid, resulteert elke storing die gevolgen heeft voor een of meer van de databases in de groep in een automatische failover. Dit zijn meestal incidenten die niet zelf kunnen worden beperkt door de ingebouwde automatische bewerkingen met hoge beschikbaarheid. De voorbeelden van failovertriggers zijn een incident dat wordt veroorzaakt doordat een SQL-tenantring of controlering is uitgeschakeld als gevolg van een geheugenlek in de OS-kernel op verschillende compute-knooppunten, of een incident dat wordt veroorzaakt doordat een of meer tenantringen uitvallen omdat er een verkeerde netwerkkabel is doorgesneden tijdens routine hardware ontmanteling.  Zie [SQL Database Hoge beschikbaarheid](sql-database-high-availability.md)voor meer informatie.

Bovendien bieden auto-failovergroepen lees-schrijf- en alleen-lezen listener-eindpunten die ongewijzigd blijven tijdens failovers. Of u nu handmatige of automatische failoveractivering gebruikt, failover schakelt alle secundaire databases in de groep over naar primair. Nadat de databasefailover is voltooid, wordt de DNS-record automatisch bijgewerkt om de eindpunten om te leiden naar de nieuwe regio. Zie [Overzicht van bedrijfscontinuïteit](sql-database-business-continuity.md)voor de specifieke RPO- en RTO-gegevens.

Wanneer u automatische failovergroepen gebruikt met een automatisch failoverbeleid, leidt elke storing die gevolgen heeft voor databases in de SQL Database-server of beheerde instantie tot automatische failover. U de groep voor automatische failover beheren met behulp van:

- [Azure-portal](sql-database-implement-geo-distributed-database.md)
- [Azure CLI: Failover-groep](scripts/sql-database-add-single-db-to-failover-group-cli.md)
- [PowerShell: Failover-groep](scripts/sql-database-add-single-db-to-failover-group-powershell.md)
- [REST API: Failovergroep](/rest/api/sql/failovergroups).

Zorg ervoor dat de verificatievereisten voor uw server en database na failover zijn geconfigureerd op het nieuwe primaire. Zie SQL [Database-beveiliging na herstel na noodgevallen](sql-database-geo-replication-security-config.md)voor meer informatie .

Om echte bedrijfscontinuïteit te bereiken, is het toevoegen van databaseredundantie tussen datacenters slechts een deel van de oplossing. Het herstellen van een toepassing (service) end-to-end na een catastrofale fout vereist herstel van alle onderdelen die de service en eventuele afhankelijke services vormen. Voorbeelden van deze componenten zijn de clientsoftware (bijvoorbeeld een browser met een aangepaste JavaScript), webfrontends, opslag en DNS. Het is van cruciaal belang dat alle componenten bestand zijn tegen dezelfde fouten en beschikbaar komen binnen de hersteltijddoelstelling (RTO) van uw toepassing. Daarom moet u alle afhankelijke services identificeren en de garanties en mogelijkheden begrijpen die ze bieden. Vervolgens moet u voldoende maatregelen nemen om ervoor te zorgen dat uw service functioneert tijdens het mislukken van de services waarvan deze afhankelijk is. Zie [Cloudoplossingen ontwerpen voor noodherstel voor](sql-database-designing-cloud-solutions-for-disaster-recovery.md)meer informatie over het ontwerpen van oplossingen voor noodherstel met behulp van actieve georeplicatie.

## <a name="auto-failover-group-terminology-and-capabilities"></a>Terminologie en mogelijkheden voor groep autofailover

- **Failovergroep (MIST)**

  Een failovergroep is een benoemde groep databases die worden beheerd door één SQL Database-server of binnen één beheerde instantie die kan mislukken als een eenheid naar een ander gebied voor het geval alle of bepaalde primaire databases niet beschikbaar komen door een storing in het primaire gebied. Wanneer een failovergroep wordt gemaakt voor beheerde instanties, bevat deze alle gebruikersdatabases in de instantie en kan daarom slechts één failovergroep op een instantie worden geconfigureerd.
  
  > [!IMPORTANT]
  > De naam van de failovergroep moet `.database.windows.net` wereldwijd uniek zijn binnen het domein.

- **SQL Database-servers**

     Met SQL Database-servers kunnen sommige of alle gebruikersdatabases op één SQL Database-server in een failovergroep worden geplaatst. Een SQL Database-server ondersteunt ook meerdere failovergroepen op één SQL Database-server.

- **Primair**

  De SQL Database-server of beheerde instantie die de primaire databases in de failovergroep host.

- **Secundair**

  De SQL Database-server of beheerde instantie die de secundaire databases in de failovergroep host. De secundaire kan niet in dezelfde regio als de primaire.

- **Afzonderlijke databases toevoegen aan failovergroep**

  U meerdere afzonderlijke databases op dezelfde SQL Database-server in dezelfde failovergroep plaatsen. Als u één database toevoegt aan de failovergroep, wordt automatisch een secundaire database gemaakt met dezelfde editie en rekengrootte op de secundaire server.  U hebt die server opgegeven toen de failovergroep is gemaakt. Als u een database toevoegt die al een secundaire database in de secundaire server heeft, wordt die georeplicatiekoppeling overgenomen door de groep. Wanneer u een database toevoegt met een secundaire database in een server die geen deel uitmaakt van de failovergroep, wordt er een nieuw secundair e-mail gemaakt in de secundaire server.

  > [!IMPORTANT]
  > Zorg ervoor dat de secundaire server geen database met dezelfde naam heeft, tenzij het een bestaande secundaire database is. In failovergroepen voor beheerde bijvoorbeeld worden alle gebruikersdatabases gerepliceerd. U geen subset van gebruikersdatabases kiezen voor replicatie in de failovergroep.

- **Databases in elastische groep toevoegen aan failovergroep**

  U alle of meerdere databases in een elastische groep in dezelfde failovergroep plaatsen. Als de primaire database zich in een elastische groep bevindt, wordt de secundaire automatisch gemaakt in de elastische groep met dezelfde naam (secundaire groep). U moet ervoor zorgen dat de secundaire server een elastische pool bevat met dezelfde exacte naam en voldoende vrije capaciteit om de secundaire databases te hosten die worden gemaakt door de failovergroep. Als u een database toevoegt in de groep die al een secundaire database in de secundaire groep heeft, wordt die georeplicatiekoppeling overgenomen door de groep. Wanneer u een database toevoegt met een secundaire database in een server die geen deel uitmaakt van de failovergroep, wordt er een nieuwe secundaire groep gemaakt in de secundaire groep.
  
- **Initiële zaaien** 

  Wanneer databases, elastische groepen of beheerde exemplaren worden toegevoegd aan een failovergroep, is er een eerste zaaifase voordat de gegevensreplicatie wordt gestart. De eerste zaaifase is de langste en duurste bewerking. Zodra het eerste zaaien is voltooid, worden gegevens gesynchroniseerd en worden alleen latere gegevenswijzigingen gerepliceerd. De tijd die nodig is om het eerste zaad te voltooien, is afhankelijk van de grootte van uw gegevens, het aantal gerepliceerde databases en de snelheid van de koppeling tussen de entiteiten in de failovergroep. Onder normale omstandigheden is de typische zaaisnelheid 50-500 GB per uur voor een enkele database of elastische pool, en 18-35 GB per uur voor een beheerde instantie. Zaaien wordt uitgevoerd voor alle databases parallel. U de opgegeven zaaisnelheid gebruiken, samen met het aantal databases en de totale grootte van gegevens om in te schatten hoe lang de eerste zaaifase zal duren voordat de gegevensreplicatie begint.

  Voor beheerde exemplaren moet ook de snelheid van de verbinding Express Route tussen de twee instanties in aanmerking worden genomen bij het schatten van de tijd van de eerste zaaifase. Als de snelheid van het verband tussen de twee instanties langzamer is dan nodig is, wordt de tijd om te zaaien waarschijnlijk met name beïnvloed. U de opgegeven zaaisnelheid, het aantal databases, de totale grootte van gegevens en de koppelingssnelheid gebruiken om in te schatten hoe lang de eerste zaaifase zal duren voordat de gegevensreplicatie begint. Bijvoorbeeld, voor een enkele 100 GB database, de eerste zaadfase zou overal duren van 2,8 - 5,5 uur als de link in staat is te duwen 35 GB per uur. Als de koppeling slechts 10 GB per uur kan overbrengen, duurt het zaaien van een database van 100 GB ongeveer 10 uur. Als er meerdere databases te repliceren zijn, wordt het zaaien parallel uitgevoerd en, in combinatie met een langzame koppelingssnelheid, kan de initiële zaaifase aanzienlijk langer duren, vooral als het parallelle zaaien van gegevens uit alle databases de beschikbare bandbreedte koppelen. Als de netwerkbandbreedte tussen twee exemplaren beperkt is en u meerdere beheerde exemplaren toevoegt aan een failovergroep, u overwegen om meerdere beheerde exemplaren achtereenvolgens aan de failovergroep toe te voegen.

  
- **DNS-zone**

  Een unieke ID die automatisch wordt gegenereerd wanneer een nieuw exemplaar wordt gemaakt. Een SAN-certificaat (multidomain) voor dit exemplaar is ingericht om de clientverbindingen met een instantie in dezelfde DNS-zone te verifiëren. De twee beheerde exemplaren in dezelfde failovergroep moeten de DNS-zone delen.
  
  > [!NOTE]
  > Een DNS-zone-id is niet vereist voor failovergroepen die zijn gemaakt voor SQL Database-servers.

- **Lees-schrijflistener voor failovergroepen**

  Een DNS CNAME-record die verwijst naar de URL van het huidige primaire primair. Het wordt automatisch gemaakt wanneer de failovergroep wordt gemaakt en stelt de read-write SQL-workload in staat om op transparante wijze opnieuw verbinding te maken met de primaire database wanneer de primaire wijzigingen na failover. Wanneer de failovergroep wordt gemaakt op een SQL Database-server, wordt `<fog-name>.database.windows.net`de DNS CNAME-record voor de listener-URL gevormd als . Wanneer de failovergroep wordt gemaakt op een beheerde instantie, wordt `<fog-name>.zone_id.database.windows.net`de DNS CNAME-record voor de listener-URL gevormd als .

- **Alleen-lezen voorlezen van failovergroepen**

  Er is een DNS CNAME-record gevormd die verwijst naar de alleen-lezen listener die verwijst naar de URL van de secundaire. Het wordt automatisch gemaakt wanneer de failovergroep wordt gemaakt en stelt de alleen-lezen SQL-workload in staat om op transparante wijze verbinding te maken met het secundaire met behulp van de opgegeven load-balancing-regels. Wanneer de failovergroep wordt gemaakt op een SQL Database-server, wordt `<fog-name>.secondary.database.windows.net`de DNS CNAME-record voor de listener-URL gevormd als . Wanneer de failovergroep wordt gemaakt op een beheerde instantie, wordt `<fog-name>.zone_id.secondary.database.windows.net`de DNS CNAME-record voor de listener-URL gevormd als .

- **Automatisch failoverbeleid**

  Standaard wordt een failovergroep geconfigureerd met een automatisch failoverbeleid. De SQL Database-service activeert failover nadat de fout is gedetecteerd en de respijtperiode is verstreken. Het systeem moet controleren of de storing niet kan worden beperkt door de ingebouwde [infrastructuur met hoge beschikbaarheid van de SQL Database-service](sql-database-high-availability.md) vanwege de omvang van de impact. Als u de failoverworkflow vanuit de toepassing wilt beheren, u automatische failover uitschakelen.
  
  > [!NOTE]
  > Omdat verificatie van de omvang van de storing en hoe snel deze kan worden beperkt, menselijke acties door het operationele team vereist, kan de respijtperiode niet onder een uur worden ingesteld.  Deze beperking is van toepassing op alle databases in de failovergroep, ongeacht de status van gegevenssynchronisatie. 

- **Alleen-lezen failoverbeleid**

  Standaard is de failover van de alleen-lezen listener uitgeschakeld. Het zorgt ervoor dat de prestaties van de primaire niet wordt beïnvloed wanneer de secundaire offline is. Het betekent echter ook dat de alleen-lezen sessies niet in staat zullen zijn om verbinding te maken totdat de secundaire is hersteld. Als u downtime voor de alleen-lezensessies niet tolereren en u het primaire voor zowel alleen-lezen als lees-schrijfverkeer tijdelijk gebruiken ten koste van de `AllowReadOnlyFailoverToPrimary` mogelijke prestatiedegradatie van de primaire, u failover voor de alleen-lezen listener inschakelen door de eigenschap te configureren. In dat geval wordt het alleen-lezen verkeer automatisch doorgestuurd naar de primaire als het secundaire niet beschikbaar is.

- **Geplande failover**

   Geplande failover voert volledige synchronisatie uit tussen primaire en secundaire databases voordat de secundaire switches naar de primaire rol worden uitgevoerd. Dit garandeert geen verlies van gegevens. Geplande failover wordt gebruikt in de volgende scenario's:

  - Dr-drills (disaster recovery) uitvoeren wanneer het gegevensverlies niet acceptabel is
  - De databases verplaatsen naar een andere regio
  - Retourneer de databases naar het primaire gebied nadat de storing is verholpen (failback).

- **Niet-geplande failover**

   Ongeplande of geforceerde failover schakelt onmiddellijk het secundaire naar de primaire rol zonder synchronisatie met de primaire. Deze bewerking zal leiden tot gegevensverlies. Ongeplande failover wordt gebruikt als herstelmethode tijdens uitval wanneer de primaire niet toegankelijk is. Wanneer het oorspronkelijke primaire primair weer online is, wordt het automatisch opnieuw verbinding gemaakt zonder synchronisatie en wordt het een nieuwe secundaire.

- **Handmatige failover**

  U failover handmatig starten op elk gewenst moment, ongeacht de automatische failoverconfiguratie. Als het automatische failoverbeleid niet is geconfigureerd, is handmatige failover vereist om databases in de failovergroep te herstellen naar het secundaire. U gedwongen of vriendelijke failover starten (met volledige gegevenssynchronisatie). Deze laatste kan worden gebruikt om de primaire naar de secundaire regio te verplaatsen. Wanneer failover is voltooid, worden de DNS-records automatisch bijgewerkt om ervoor te zorgen dat de verbinding met de nieuwe primaire

- **Respijtperiode met gegevensverlies**

  Omdat de primaire en secundaire databases worden gesynchroniseerd met behulp van asynchrone replicatie, kan de failover leiden tot gegevensverlies. U het automatische failoverbeleid aanpassen om de tolerantie van uw toepassing voor gegevensverlies weer te geven. Door te `GracePeriodWithDataLossHours`configureren, u bepalen hoe lang het systeem wacht voordat u de failover start die waarschijnlijk tot gegevensverlies leidt.

- **Meerdere failovergroepen**

  U meerdere failovergroepen configureren voor hetzelfde paar servers om de schaal van failovers te beheren. Elke groep faalt onafhankelijk van elkaar. Als uw multi-tenanttoepassing elastische groepen gebruikt, u deze mogelijkheid gebruiken om primaire en secundaire databases in elke groep te mengen. Op deze manier u de impact van een storing beperken tot slechts de helft van de huurders.

  > [!NOTE]
  > Beheerde instantie biedt geen ondersteuning voor meerdere failovergroepen.
  
## <a name="permissions"></a>Machtigingen

Machtigingen voor een failovergroep worden beheerd via [rbac (role-based access control).](../role-based-access-control/overview.md) De rol [SQL Server-inzender](../role-based-access-control/built-in-roles.md#sql-server-contributor) heeft alle benodigde machtigingen om failovergroepen te beheren.

### <a name="create-failover-group"></a>Failovergroep maken

Als u een failovergroep wilt maken, hebt u RBAC-schrijftoegang nodig tot zowel de primaire als secundaire servers en tot alle databases in de failovergroep. Voor een beheerde instantie hebt u RBAC-schrijftoegang nodig tot zowel de primaire als de secundaire beheerde instantie, maar machtigingen voor afzonderlijke databases zijn niet relevant omdat afzonderlijke beheerde instantiedatabases niet kunnen worden toegevoegd aan of verwijderd uit een failovergroep. 

### <a name="update-a-failover-group"></a>Een failovergroep bijwerken

Als u een failovergroep wilt bijwerken, hebt u RBAC-schrijftoegang nodig tot de failovergroep en alle databases op de huidige primaire server of beheerde instantie.  

### <a name="failover-a-failover-group"></a>Failover een failovergroep

Als u een failovergroep wilt mislukken, hebt u RBAC-schrijftoegang nodig tot de failovergroep op de nieuwe primaire server of beheerde instantie.

## <a name="best-practices-of-using-failover-groups-with-single-databases-and-elastic-pools"></a>Aanbevolen procedures voor het gebruik van failovergroepen met afzonderlijke databases en elastische pools

De groep auto-failover moet zijn geconfigureerd op de primaire SQL Database-server en verbindt deze met de secundaire SQL Database-server in een andere Azure-regio. De groepen kunnen alle of sommige databases in deze servers bevatten. Het volgende diagram illustreert een typische configuratie van een georedundante cloudtoepassing met meerdere databases en een groep voor automatische failover.

![automatische failover](./media/sql-database-auto-failover-group/auto-failover-group.png)

> [!NOTE]
> Zie [Enkele database toevoegen aan een failovergroep](sql-database-single-database-failover-group-tutorial.md) voor een gedetailleerde stapsgewijze zelfstudie die één database toevoegt aan een failovergroep.

Volg bij het ontwerpen van een service met bedrijfscontinuïteit in het achterhoofd de volgende algemene richtlijnen:

### <a name="using-one-or-several-failover-groups-to-manage-failover-of-multiple-databases"></a>Een of meer failovergroepen gebruiken om failover van meerdere databases te beheren

Er kunnen één of veel failovergroepen worden gemaakt tussen twee servers in verschillende regio's (primaire en secundaire servers). Elke groep kan een of meerdere databases bevatten die als eenheid worden hersteld voor het geval alle of sommige primaire databases niet meer beschikbaar komen als gevolg van een storing in de primaire regio. De failovergroep maakt een geosecundaire database met dezelfde servicedoelstelling als de primaire. Als u een bestaande georeplicatierelatie toevoegt aan de failovergroep, controleert u of de geo-secundaire is geconfigureerd met dezelfde servicelaag en rekengrootte als de primaire.
  
> [!IMPORTANT]
> Het maken van failovergroepen tussen twee servers in verschillende abonnementen wordt momenteel niet ondersteund voor afzonderlijke databases en elastische groepen. Als u de primaire of secundaire server naar een ander abonnement verplaatst nadat de failovergroep is gemaakt, kan dit leiden tot fouten in de failoveraanvragen en andere bewerkingen.

### <a name="using-read-write-listener-for-oltp-workload"></a>Lees-schrijflistener gebruiken voor OLTP-werkbelasting

Wanneer oltp-bewerkingen `<fog-name>.database.windows.net` worden uitgevoerd, gebruikt u als url van de server en worden de verbindingen automatisch naar de primaire geleid. Deze URL verandert niet na de failover. Let op: de failover houdt in dat de DNS-record wordt bijgewerkt, zodat de clientverbindingen pas worden doorgestuurd naar het nieuwe primaire punt nadat de DNS-cache van de client is vernieuwd.

### <a name="using-read-only-listener-for-read-only-workload"></a>Alleen-lezen listener gebruiken voor alleen-lezen werkbelasting

Als u een logisch geïsoleerde alleen-lezen werkbelasting hebt die tolerant is voor bepaalde gegevens, u de secundaire database in de toepassing gebruiken. Voor alleen-lezen sessies `<fog-name>.secondary.database.windows.net` wordt de URL van de server gebruikt en wordt de verbinding automatisch naar het secundaire geleid. Het wordt ook aanbevolen dat u in verband `ApplicationIntent=ReadOnly`string leesintentie aan te geven met behulp van . Als u ervoor wilt zorgen dat de alleen-lezen workload opnieuw verbinding kan maken na failover of in het geval dat de secundaire server offline gaat, moet u de `AllowReadOnlyFailoverToPrimary` eigenschap van het failoverbeleid configureren.

### <a name="preparing-for-performance-degradation"></a>Voorbereiding op prestatiedegradatie

Een typische Azure-toepassing maakt gebruik van meerdere Azure-service en bestaat uit meerdere componenten. De geautomatiseerde failover van de failovergroep wordt geactiveerd op basis van de status van de Azure SQL-componenten alleen. Andere Azure-services in het primaire gebied worden mogelijk niet beïnvloed door de storing en de onderdelen ervan zijn mogelijk nog steeds beschikbaar in die regio. Zodra de primaire databases overschakelen naar het DR-gebied, kan de latentie tussen de afhankelijke componenten toenemen. Om de impact van een hogere latentie op de prestaties van de toepassing te voorkomen, moet u ervoor zorgen dat alle onderdelen van de toepassing in de DR-regio worden ontslagen en deze [richtlijnen voor netwerkbeveiliging](#failover-groups-and-network-security)volgen.

### <a name="preparing-for-data-loss"></a>Voorbereiden op gegevensverlies

Als er een storing wordt gedetecteerd, wacht SQL `GracePeriodWithDataLossHours`op de periode die u hebt opgegeven door . De standaardwaarde is 1 uur. Als u zich geen gegevensverlies `GracePeriodWithDataLossHours` kunt veroorloven, moet u instellen op een voldoende groot aantal, zoals 24 uur. Gebruik handmatige groepsfailover om terug te mislukken van de secundaire naar de primaire.

> [!IMPORTANT]
> Elastische pools met 800 of minder DTU's en meer dan 250 databases met geo-replicatie kunnen problemen ondervinden, waaronder langere geplande failovers en gedegradeerde prestaties.  Deze problemen komen vaker voor bij schrijfintensieve workloads, wanneer georeplicatieeindpunten op grote schaal worden gescheiden door geografie of wanneer meerdere secundaire eindpunten voor elke database worden gebruikt.  Symptomen van deze problemen worden aangegeven wanneer de geo-replicatie vertraging toeneemt in de tijd.  Deze vertraging kan worden gecontroleerd met behulp van [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database).  Als deze problemen optreden, omvatten oplossingen het verhogen van het aantal dto's voor groepen of het verminderen van het aantal geo-gerepliceerde databases in dezelfde groep.

### <a name="changing-secondary-region-of-the-failover-group"></a>Secundaire regio van de failovergroep wijzigen

Om de wijzigingsvolgorde te illustreren, gaan we ervan uit dat server A de primaire server is, dat server B de bestaande secundaire server is en dat server C de nieuwe secundaire is in het derde gebied.  Voer de volgende stappen uit om de overgang te maken:

1.  Maak extra secondaries van elke database op server A naar server C met behulp van [actieve georeplicatie.](sql-database-active-geo-replication.md) Elke database op server A heeft twee secondaries, een op server B en een op server C. Dit garandeert dat de primaire databases tijdens de overgang beschermd blijven.
2.  Verwijder de failovergroep. Op dit punt zullen de logins ontbreken. Dit komt omdat de SQL-aliassen voor de failovergroeplisteners zijn verwijderd en de gateway de naam van de failovergroep niet herkent.
3.  Maak de failovergroep met dezelfde naam opnieuw tussen servers A en C. Op dit punt zullen de logins niet meer mislukken.
4.  Voeg alle primaire databases op server A toe aan de nieuwe failovergroep.
5.  Laat server B vallen. Alle databases op B worden automatisch verwijderd. 


### <a name="changing-primary-region-of-the-failover-group"></a>Primaire regio van de failovergroep wijzigen

Om de wijzigingsvolgorde te illustreren, gaan we ervan uit dat server A de primaire server is, dat server B de bestaande secundaire server is en server C de nieuwe primaire in het derde gebied is.  Voer de volgende stappen uit om de overgang te maken:

1.  Voer een geplande failover uit om de primaire server over te schakelen naar B. Server A wordt de nieuwe secundaire server. De failover kan resulteren in enkele minuten downtime. De werkelijke tijd is afhankelijk van de grootte van de failovergroep.
2.  Maak extra secondaries van elke database op server B naar server C met behulp van [actieve georeplicatie.](sql-database-active-geo-replication.md) Elke database op server B heeft twee secondaries, een op server A en een op server C. Dit garandeert dat de primaire databases tijdens de overgang beschermd blijven.
3.  Verwijder de failovergroep. Op dit punt zullen de logins ontbreken. Dit komt omdat de SQL-aliassen voor de failovergroeplisteners zijn verwijderd en de gateway de naam van de failovergroep niet herkent.
4.  Maak de failovergroep met dezelfde naam opnieuw tussen servers A en C. Op dit punt zullen de logins niet meer mislukken.
5.  Voeg alle primaire databases op B toe aan de nieuwe failovergroep. 
6.  Voer een geplande failover van de failovergroep uit om Over te schakelen met B en C. Nu server C zal de primaire en B - de secundaire. Alle secundaire databases op server A worden automatisch gekoppeld aan de voorverkiezingen op C. Net als in stap 1 kan de failover resulteren in enkele minuten downtime.
6.  Laat de server A vallen. Alle databases op A worden automatisch verwijderd.

> [!IMPORTANT]
> Wanneer de failovergroep wordt verwijderd, worden ook de DNS-records voor de listenereindpunten verwijderd. Op dat moment is er een niet-nul kans dat iemand anders een failovergroep of serveralias met dezelfde naam maakt, waardoor u deze niet opnieuw gebruiken. Gebruik geen algemene failovergroepsnamen om het risico te minimaliseren.

## <a name="best-practices-of-using-failover-groups-with-managed-instances"></a>Aanbevolen procedures voor het gebruik van failovergroepen met beheerde exemplaren

De groep auto-failover moet zijn geconfigureerd op de primaire instantie en deze verbinden met de secundaire instantie in een andere Azure-regio.  Alle databases in de instantie worden gerepliceerd naar de secundaire instantie.

Het volgende diagram illustreert een typische configuratie van een georedundante cloudtoepassing met beheerde instantie en auto-failovergroep.

![automatische failover](./media/sql-database-auto-failover-group/auto-failover-group-mi.png)

> [!NOTE]
> Zie [Beheerde instantie toevoegen aan een failovergroep](sql-database-managed-instance-failover-group-tutorial.md) voor een gedetailleerde stapsgewijze zelfstudie die een beheerde instantie toevoegt om failovergroep te gebruiken.

Als uw toepassing beheerde instantie als gegevenslaag gebruikt, volgt u deze algemene richtlijnen bij het ontwerpen voor bedrijfscontinuïteit:

### <a name="creating-the-secondary-instance"></a>De secundaire instantie maken 

Om ervoor te zorgen dat de verbinding met de primaire instantie na het mislukken niet wordt onderbroken, moeten zowel de primaire als de secundaire instanties zich in dezelfde DNS-zone bevinden. Dit garandeert dat hetzelfde SAN-certificaat (multi-domain) kan worden gebruikt om de clientverbindingen met een van de twee instanties in de failovergroep te verifiëren. Wanneer uw toepassing klaar is voor productie-implementatie, maakt u een secundaire instantie in een andere regio en controleert u of deze de DNS-zone deelt met de primaire instantie. U dit doen `DNS Zone Partner` door een optionele parameter op te geven met behulp van de Azure-portal, PowerShell of de REST API.

> [!IMPORTANT]
> Eerste instantie die in het subnet is gemaakt, bepaalt de DNS-zone voor alle volgende exemplaren in hetzelfde subnet. Dit betekent dat twee exemplaren van hetzelfde subnet niet tot verschillende DNS-zones kunnen behoren.

Zie [Een secundairbeheerde instantie maken](sql-database-managed-instance-failover-group-tutorial.md#3---create-a-secondary-managed-instance)voor meer informatie over het maken van de secundaire instantie in dezelfde DNS-zone als de primaire instantie.

### <a name="enabling-replication-traffic-between-two-instances"></a>Replicatieverkeer tussen twee instanties inschakelen

Omdat elke instantie is geïsoleerd in zijn eigen VNet, moet tweerichtingsverkeer tussen deze VNets worden toegestaan. Zie [Azure VPN-gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md)

### <a name="creating-a-failover-group-between-managed-instances-in-different-subscriptions"></a>Een failovergroep maken tussen beheerde exemplaren in verschillende abonnementen

U een failovergroep maken tussen beheerde exemplaren in twee verschillende abonnementen. Wanneer u PowerShell API gebruikt, kunt `PartnerSubscriptionId` u dit doen door de parameter voor de secundaire instantie op te geven. Bij gebruik van REST API kan `properties.managedInstancePairs` elke instantie-id die in de parameter is opgenomen, een eigen abonnementhebben.
  
> [!IMPORTANT]
> Azure-portal biedt geen ondersteuning voor het maken van failovergroepen voor verschillende abonnementen. Ook voor de bestaande failovergroepen in verschillende abonnementen en/of resourcegroepen kan failover niet handmatig worden gestart via portal vanuit de primaire instantie. In plaats daarvan starten vanuit de geosecundaire instantie.

### <a name="managing-failover-to-secondary-instance"></a>Failover naar secundaire instantie beheren

De failovergroep beheert de failover van alle databases in de instantie. Wanneer een groep wordt gemaakt, wordt elke database in de instantie automatisch gegeo-gerepliceerd naar de secundaire instantie. U failovergroepen niet gebruiken om een gedeeltelijke failover van een subset van de databases te starten.

> [!IMPORTANT]
> Als een database uit de primaire instantie wordt verwijderd, wordt deze ook automatisch verwijderd op de geo secundaire instantie.

### <a name="using-read-write-listener-for-oltp-workload"></a>Lees-schrijflistener gebruiken voor OLTP-werkbelasting

Wanneer oltp-bewerkingen `<fog-name>.zone_id.database.windows.net` worden uitgevoerd, gebruikt u als url van de server en worden de verbindingen automatisch naar de primaire geleid. Deze URL verandert niet na de failover. De failover omvat het bijwerken van de DNS-record, zodat de clientverbindingen worden doorgestuurd naar de nieuwe primaire pas nadat de client DNS-cache is vernieuwd. Omdat de secundaire instantie de DNS-zone deelt met de primaire, kan de clienttoepassing opnieuw verbinding maken met hetzelfde SAN-certificaat.

### <a name="using-read-only-listener-to-connect-to-the-secondary-instance"></a>Alleen-lezen listener gebruiken om verbinding te maken met de secundaire instantie

Als u een logisch geïsoleerde alleen-lezen werkbelasting hebt die tolerant is voor bepaalde gegevens, u de secundaire database in de toepassing gebruiken. Als u rechtstreeks verbinding wilt maken `server.secondary.zone_id.database.windows.net` met de geo-gerepliceerde secundaire, gebruikt u als url van de server en wordt de verbinding rechtstreeks gemaakt met de geo-gerepliceerde secundaire.

> [!NOTE]
> In bepaalde servicelagen ondersteunt Azure SQL Database het gebruik van [alleen-lezen replica's](sql-database-read-scale-out.md) om alleen-lezen queryworkloads `ApplicationIntent=ReadOnly` te laden met behulp van de capaciteit van één alleen-lezen replica en het gebruik van de parameter in de verbindingstekenreeks. Wanneer u een geo-gerepliceerde secundaire functie hebt geconfigureerd, u deze mogelijkheid gebruiken om verbinding te maken met een alleen-lezen replica op de primaire locatie of op de geo-gerepliceerde locatie.
> - Als u verbinding wilt maken met een `<fog-name>.zone_id.database.windows.net`alleen-lezen replica op de primaire locatie, gebruikt u .
> - Als u verbinding wilt maken met een `<fog-name>.secondary.zone_id.database.windows.net`alleen-lezen replica op de secundaire locatie, gebruikt u .

### <a name="preparing-for-performance-degradation"></a>Voorbereiding op prestatiedegradatie

Een typische Azure-toepassing maakt gebruik van meerdere Azure-service en bestaat uit meerdere componenten. De geautomatiseerde failover van de failovergroep wordt geactiveerd op basis van de status van de Azure SQL-componenten alleen. Andere Azure-services in het primaire gebied worden mogelijk niet beïnvloed door de storing en de onderdelen ervan zijn mogelijk nog steeds beschikbaar in die regio. Zodra de primaire databases overschakelen naar het DR-gebied, kan de latentie tussen de afhankelijke componenten toenemen. Om de impact van een hogere latentie op de prestaties van de toepassing te voorkomen, moet u ervoor zorgen dat alle onderdelen van de toepassing in de DR-regio worden ontslagen en deze [richtlijnen voor netwerkbeveiliging](#failover-groups-and-network-security)volgen.

### <a name="preparing-for-data-loss"></a>Voorbereiden op gegevensverlies

Als een storing wordt gedetecteerd, activeert SQL automatisch read-write failover als er naar beste weten geen gegevensverlies is. Anders wacht het op de periode `GracePeriodWithDataLossHours`die u hebt opgegeven door . Als u `GracePeriodWithDataLossHours`hebt opgegeven, moet u voorbereid zijn op gegevensverlies. Over het algemeen is Azure tijdens uitval voorstander van beschikbaarheid. Als u zich geen gegevensverlies veroorloven, moet u GracePeriodWithDataLossHours instellen op een voldoende groot aantal, zoals 24 uur.

De DNS-update van de lees-schrijfluisteraar gebeurt onmiddellijk nadat de failover is gestart. Deze bewerking leidt niet tot gegevensverlies. Het proces van het schakelen van databaserollen kan echter tot 5 minuten duren onder normale omstandigheden. Totdat deze is voltooid, worden sommige databases in de nieuwe primaire instantie nog steeds alleen-lezen. Als failover wordt gestart met PowerShell, is de hele bewerking synchroon. Als deze wordt gestart met behulp van de Azure-portal, geeft de gebruikersinterface de voltooiingsstatus aan. Als deze wordt gestart met de REST API, gebruikt u het pollingmechanisme van standaard Azure Resource Manager om te controleren op voltooiing.

> [!IMPORTANT]
> Gebruik handmatige groepsfailover om voorverkiezingen terug te verplaatsen naar de oorspronkelijke locatie. Wanneer de storing die de failover heeft veroorzaakt, wordt verholpen, u uw primaire databases naar de oorspronkelijke locatie verplaatsen. Om dat te doen moet u de handmatige failover van de groep starten.
  
### <a name="changing-secondary-region-of-the-failover-group"></a>Secundaire regio van de failovergroep wijzigen

Laten we aannemen dat instantie A de primaire instantie is, instantie B de bestaande secundaire instantie en instantie C de nieuwe secundaire instantie in het derde gebied.  Voer de volgende stappen uit om de overgang te maken:

1.  Instantie C maken met dezelfde grootte als A en in dezelfde DNS-zone. 
2.  Verwijder de failovergroep tussen instanties A en B. Op dit moment worden de aanmeldingen mislukt omdat de SQL-aliassen voor de failovergroeplisteners zijn verwijderd en de gateway de naam van de failovergroep niet herkent. De secundaire databases worden losgekoppeld van de voorverkiezingen en worden lees-schrijven databases. 
3.  Maak een failovergroep met dezelfde naam tussen instantie A en C. Volg de instructies in [failovergroep met beheerde instantiezelfstudie](sql-database-managed-instance-failover-group-tutorial.md). Dit is een grootte-van-gegevensbewerking en wordt voltooid wanneer alle databases van instantie A zijn gezaaid en gesynchroniseerd.
4.  Verwijder instantie B als dit niet nodig is om onnodige kosten te voorkomen.

> [!NOTE]
> Na stap 2 en tot stap 3 is voltooid, blijven de databases in instantie A onbeschermd tegen een catastrofale fout van instantie A.

### <a name="changing-primary-region-of-the-failover-group"></a>Primaire regio van de failovergroep wijzigen

Stel dat instantie A de primaire instantie is, instantie B de bestaande secundaire instantie en instantie C de nieuwe primaire instantie in het derde gebied.  Voer de volgende stappen uit om de overgang te maken:

1.  Instantie C maken met dezelfde grootte als B en in dezelfde DNS-zone. 
2.  Maak verbinding met instantie B en handmatig failover om de primaire instantie over te schakelen naar B. Instantie A wordt automatisch de nieuwe secundaire instantie.
3.  Verwijder de failovergroep tussen instanties A en B. Op dit moment worden de aanmeldingen mislukt omdat de SQL-aliassen voor de failovergroeplisteners zijn verwijderd en de gateway de naam van de failovergroep niet herkent. De secundaire databases worden losgekoppeld van de voorverkiezingen en worden lees-schrijven databases. 
4.  Maak een failovergroep met dezelfde naam tussen instantie A en C. Volg de instructies in de [failovergroep met beheerde instantiezelfstudie](sql-database-managed-instance-failover-group-tutorial.md). Dit is een grootte-van-gegevensbewerking en wordt voltooid wanneer alle databases van instantie A zijn gezaaid en gesynchroniseerd.
5.  Verwijder instantie A, indien niet nodig om onnodige kosten te voorkomen.

> [!NOTE] 
> Na stap 3 en tot stap 4 is voltooid, blijven de databases in instantie A onbeschermd tegen een catastrofale fout van instantie A.

> [!IMPORTANT]
> Wanneer de failovergroep wordt verwijderd, worden ook de DNS-records voor de listenereindpunten verwijderd. Op dat moment is er een niet-nul kans dat iemand anders een failovergroep of serveralias met dezelfde naam maakt, waardoor u deze niet opnieuw gebruiken. Gebruik geen algemene failovergroepsnamen om het risico te minimaliseren.

## <a name="failover-groups-and-network-security"></a>Failovergroepen en netwerkbeveiliging

Voor sommige toepassingen vereisen de beveiligingsregels dat de netwerktoegang tot de gegevenslaag is beperkt tot een specifieke component of onderdelen zoals een VM, webservice enz. Deze eis brengt een aantal uitdagingen met zich mee voor het ontwerp van bedrijfscontinuïteit en het gebruik van de failovergroepen. Overweeg de volgende opties bij het implementeren van dergelijke beperkte toegang.

### <a name="using-failover-groups-and-virtual-network-rules"></a>Failovergroepen en virtuele netwerkregels gebruiken

Als u [eindpunten en regels van de Virtual Network-service](sql-database-vnet-service-endpoint-rule-overview.md) gebruikt om de toegang tot uw SQL-database te beperken, moet u er rekening mee houden dat elk eindpunt van de Virtual Network-service slechts op één Azure-regio van toepassing is. Het eindpunt stelt andere regio's niet in staat om communicatie van het subnet te accepteren. Daarom kunnen alleen de clienttoepassingen die in dezelfde regio zijn geïmplementeerd, verbinding maken met de primaire database. Aangezien de failover ertoe leidt dat de SQL-clientsessies worden omgeleid naar een server in een ander (secundair) gebied, mislukken deze sessies als ze afkomstig zijn van een client buiten dat gebied. Om die reden kan het automatische failoverbeleid niet worden ingeschakeld als de deelnemende servers zijn opgenomen in de regels voor virtueel netwerk. Voer de volgende stappen uit om handmatige failover te ondersteunen:

1. Inrichten van de redundante kopieën van de front-end componenten van uw toepassing (webservice, virtuele machines, enz.) in de secundaire regio
2. De [virtuele netwerkregels](sql-database-vnet-service-endpoint-rule-overview.md) afzonderlijk configureren voor primaire en secundaire server
3. De [front-end failover](sql-database-designing-cloud-solutions-for-disaster-recovery.md#scenario-1-using-two-azure-regions-for-business-continuity-with-minimal-downtime) inschakelen met behulp van een Configuratie van verkeersbeheer
4. Handmatige failover starten wanneer de storing wordt gedetecteerd. Deze optie is geoptimaliseerd voor de toepassingen die consistente latentie tussen de front-end en de gegevenslaag vereisen en ondersteunt herstel wanneer front-end, gegevenslaag of beide worden beïnvloed door de storing.

> [!NOTE]
> Als u de **alleen-lezen listener** gebruikt om een alleen-lezen werkbelasting te laden, moet u ervoor zorgen dat deze werkbelasting wordt uitgevoerd in een VM of andere resource in het secundaire gebied, zodat deze verbinding kan maken met de secundaire database.

### <a name="using-failover-groups-and-sql-database-firewall-rules"></a>Failovergroepen en SQL-databasefirewallregels gebruiken

Als uw bedrijfscontinuïteitsplan failover vereist met behulp van groepen met automatische failover, u de toegang tot uw SQL-database beperken met behulp van de traditionele firewallregels. Voer de volgende stappen uit om automatische failover te ondersteunen:

1. [Een openbaar IP maken](../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address)
2. [Maak een public load balancer](../load-balancer/quickstart-load-balancer-standard-public-portal.md) en wijs het openbare IP toe.
3. [Maak een virtueel netwerk en de virtuele machines](../load-balancer/quickstart-load-balancer-standard-public-portal.md) voor uw front-end componenten
4. [Maak netwerkbeveiligingsgroep](../virtual-network/security-overview.md) en configureer binnenkomende verbindingen.
5. Controleer of de uitgaande verbindingen openstaan voor Azure SQL-database met behulp van ['Sql'-servicetag.](../virtual-network/security-overview.md#service-tags)
6. Maak een [SQL-databasefirewallregel](sql-database-firewall-configure.md) om binnenkomend verkeer toe te staan vanaf het openbare IP-adres dat u in stap 1 maakt.

Zie Uitgaande verbindingen laden [balancer](../load-balancer/load-balancer-outbound-connections.md)voor meer informatie over het configureren van uitgaande toegang en welk IP-adres u moet gebruiken in de firewallregels.

De bovenstaande configuratie zorgt ervoor dat de automatische failover geen verbindingen van de front-endcomponenten blokkeert en gaat ervan uit dat de toepassing de langere latentie tussen de front-end en de gegevenslaag kan verdragen.

> [!IMPORTANT]
> Om de bedrijfscontinuïteit voor regionale uitval te garanderen, moet u zorgen voor geografische redundantie voor zowel front-end componenten als de databases.

## <a name="enabling-geo-replication-between-managed-instances-and-their-vnets"></a>Geo-replicatie tussen beheerde instanties en hun VNets inschakelen

Wanneer u een failovergroep instelt tussen primaire en secundaire beheerde instanties in twee verschillende regio's, wordt elke instantie geïsoleerd met behulp van een onafhankelijk virtueel netwerk. Ga als u ervoor dat replicatieverkeer tussen deze VNets wordt voldaan:

1. De twee beheerde exemplaren moeten zich in verschillende Azure-regio's bevinden.
2. De twee beheerde exemplaren moeten dezelfde servicelaag hebben en dezelfde opslaggrootte hebben.
3. Uw secundaire beheerde instantie moet leeg zijn (geen gebruikersdatabases).
4. De virtuele netwerken die door de beheerde exemplaren worden gebruikt, moeten worden verbonden via een [VPN-gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md) of [expresroute.](../expressroute/expressroute-howto-circuit-portal-resource-manager.md) Wanneer twee virtuele netwerken verbinding maken via een on-premises netwerk, moet u ervoor zorgen dat er geen firewallregel is die poorten 5022 en 11000-11999 blokkeert. Global VNet Peering wordt niet ondersteund.
5. De twee beheerde instantie VNets mogen geen overlappende IP-adressen hebben.
6. U moet uw Network Security Groups (NSG) zodanig instellen dat poorten 5022 en het bereik 11000 ~ 12000 inkomende en uitgaande zijn voor verbindingen van het subnet van het andere beheerde exemplaar. Dit is om replicatieverkeer tussen de instanties mogelijk te maken.

   > [!IMPORTANT]
   > Verkeerd geconfigureerde NSG-beveiligingsregels leiden tot vastzittende databasekopieerbewerkingen.

7. De secundaire instantie is geconfigureerd met de juiste DNS-zone-id. DNS-zone is een eigenschap van een beheerde instantie en virtueel cluster en de ID is opgenomen in het adres hostnaam. De zone-ID wordt gegenereerd als een willekeurige tekenreeks wanneer de eerste beheerde instantie in elke VNet wordt gemaakt en dezelfde ID wordt toegewezen aan alle andere instanties in hetzelfde subnet. Eenmaal toegewezen kan de DNS-zone niet worden gewijzigd. Beheerde exemplaren die in dezelfde failovergroep zijn opgenomen, moeten de DNS-zone delen. U bereikt dit door de zone-id van de primaire instantie door te geven als de waarde van de parameter DnsZonePartner bij het maken van de secundaire instantie. 

   > [!NOTE]
   > Zie [Een beheerde instantie toevoegen aan een failovergroep voor](sql-database-managed-instance-failover-group-tutorial.md)een gedetailleerde zelfstudie over het configureren van failovergroepen met beheerde instantie.

## <a name="upgrading-or-downgrading-a-primary-database"></a>Een primaire database upgraden of downgraden

U een primaire database upgraden of downgraden naar een andere rekengrootte (binnen dezelfde servicelaag, niet tussen Algemeen Doel en Bedrijfskritiek) zonder de verbinding met secundaire databases te verbreken. Bij het upgraden raden we u aan eerst alle secundaire databases te upgraden en vervolgens de primaire te upgraden. Bij het downgraden de volgorde omkeren: eerst de primaire downgrade en vervolgens alle secundaire databases downgraden. Wanneer u de database upgradet of downgraden naar een andere servicelaag, wordt deze aanbeveling afgedwongen.

Deze volgorde wordt specifiek aanbevolen om het probleem te voorkomen waarbij de secundaire bij een lagere SKU overbelast raakt en opnieuw moet worden gezaaid tijdens een upgrade- of downgradeproces. U het probleem ook voorkomen door de primaire alleen-lezen te maken, ten koste van het beïnvloeden van alle lees-schrijfworkloads ten opzichte van de primaire.

> [!NOTE]
> Als u secundaire database hebt gemaakt als onderdeel van de failovergroepconfiguratie, wordt het niet aanbevolen om de secundaire database te downgraden. Dit is om ervoor te zorgen dat uw gegevenslaag voldoende capaciteit heeft om uw reguliere werkbelasting te verwerken nadat failover is geactiveerd.

## <a name="preventing-the-loss-of-critical-data"></a>Het verlies van kritieke gegevens voorkomen

Vanwege de hoge latentie van breedgebiednetwerken maakt continue kopie gebruik van een asynchrone replicatiemechanisme. Asynchrone replicatie maakt sommige gegevensverlies onvermijdelijk als er een fout optreedt. Sommige toepassingen vereisen echter mogelijk geen gegevensverlies. Om deze kritieke updates te beschermen, kan een toepassingsontwikkelaar de [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) systeemprocedure onmiddellijk na het begaan van de transactie aanroepen. Bellen `sp_wait_for_database_copy_sync` blokkeert de aanroepdraad totdat de laatst vastgelegde transactie is verzonden naar de secundaire database. Het wacht echter niet tot de verzonden transacties worden afgespeeld en vastgelegd op de secundaire. `sp_wait_for_database_copy_sync`wordt gekoppeld aan een specifieke continue kopiekoppeling. Elke gebruiker met de verbindingsrechten van de primaire database kan deze procedure aanroepen.

> [!NOTE]
> `sp_wait_for_database_copy_sync`voorkomt gegevensverlies na failover, maar garandeert geen volledige synchronisatie voor leestoegang. De vertraging veroorzaakt `sp_wait_for_database_copy_sync` door een proceduregesprek kan aanzienlijk zijn en is afhankelijk van de grootte van het transactielogboek op het moment van de oproep.

## <a name="failover-groups-and-point-in-time-restore"></a>Failovergroepen en point-in-time herstellen

Zie [Point in Time Recovery (PITR)](sql-database-recovery-using-backups.md#point-in-time-restore)voor informatie over het gebruik van point-in-time restore met failovergroepen.

## <a name="limitations-of-failover-groups"></a>Beperkingen van failovergroepen

Wees u bewust van de volgende beperkingen:

- Failovergroep kan niet worden gemaakt tussen twee servers of instanties in dezelfde Azure-regio's.
- Failovergroep kan niet worden hernoemd. U moet de groep verwijderen en opnieuw maken met een andere naam. 
- De naam van de database wordt niet ondersteund voor instanties in failovergroep. U moet failovergroep tijdelijk verwijderen om de naam van een database te kunnen wijzigen.

## <a name="programmatically-managing-failover-groups"></a>Programmatisch beheren van failovergroepen

Zoals eerder besproken, kunnen auto-failovergroepen en actieve geo-replicatie ook programmatisch worden beheerd met Azure PowerShell en de REST API. In de volgende tabellen worden de beschikbare set opdrachten beschreven. Actieve georeplicatie omvat een set Azure Resource Manager API's voor beheer, waaronder de [Azure SQL Database REST API](https://docs.microsoft.com/rest/api/sql/) en Azure [PowerShell-cmdlets.](https://docs.microsoft.com/powershell/azure/overview) Deze API's vereisen het gebruik van resourcegroepen en ondersteuningsrole-based security (RBAC). Zie [Azure Role-Based Access Control](../role-based-access-control/overview.md)voor meer informatie over het implementeren van toegangsrollen.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

### <a name="manage-sql-database-failover-with-single-databases-and-elastic-pools"></a>SQL-databasefailover beheren met afzonderlijke databases en elastische pools

| Cmdlet | Beschrijving |
| --- | --- |
| [Nieuwe AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) |Met deze opdracht wordt een failovergroep gesmaakt en deze geregistreerd op zowel primaire als secundaire servers|
| [Verwijder-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup) | Hiermee verwijdert u een failovergroep van de server |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Hiermee haalt u de configuratie van een failovergroep op |
| [Set-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/set-azsqldatabasefailovergroup) |Wijzigt de configuratie van een failovergroep |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) | Triggers failover van een failovergroep naar de secundaire server |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup)|Voegt een of meer databases toe aan een failovergroep|

### <a name="manage-sql-database-failover-groups-with-managed-instances"></a>SQL-databasefailovergroepen beheren met beheerde exemplaren

| Cmdlet | Beschrijving |
| --- | --- |
| [Nieuw-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup) |Met deze opdracht wordt een failovergroep maakt en deze geregistreerd op zowel primaire als secundaire instanties|
| [Set-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/set-azsqldatabaseinstancefailovergroup) |Wijzigt de configuratie van een failovergroep|
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) |Hiermee haalt u de configuratie van een failovergroep op|
| [Switch-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) |Triggers failover van een failovergroep naar de secundaire instantie|
| [Remove-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/remove-azsqldatabaseinstancefailovergroup) | Een failovergroep verwijderen|

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

### <a name="manage-sql-database-failover-with-single-databases-and-elastic-pools"></a>SQL-databasefailover beheren met afzonderlijke databases en elastische pools

| Opdracht | Beschrijving |
| --- | --- |
| [az sql failover-group maken](/cli/azure/sql/failover-group#az-sql-failover-group-create) |Met deze opdracht wordt een failovergroep gesmaakt en deze geregistreerd op zowel primaire als secundaire servers|
| [az sql failover-group delete](/cli/azure/sql/failover-group#az-sql-failover-group-delete) | Hiermee verwijdert u een failovergroep van de server |
| [az sql failover-group show](/cli/azure/sql/failover-group#az-sql-failover-group-show) | Hiermee haalt u een failovergroepconfiguratie op |
| [az sql failover-group update](/cli/azure/sql/failover-group#az-sql-failover-group-update) |Wijzigt de configuratie van een failovergroep en/of voegt een of meer databases toe aan een failovergroep|
| [az sql failover-group set-primary](/cli/azure/sql/failover-group#az-sql-failover-group-set-primary) | Triggers failover van een failovergroep naar de secundaire server |

### <a name="manage-sql-database-failover-groups-with-managed-instances"></a>SQL-databasefailovergroepen beheren met beheerde exemplaren

| Opdracht | Beschrijving |
| --- | --- |
| [az sql instance-failover-group maken](/cli/azure/sql/instance-failover-group#az-sql-instance-failover-group-create) | Met deze opdracht wordt een failovergroep maakt en deze geregistreerd op zowel primaire als secundaire instanties |
| [update az sql instance-failover-group](/cli/azure/sql/instance-failover-group#az-sql-instance-failover-group-update) | Wijzigt de configuratie van een failovergroep|
| [az sql instance-failover-group show](/cli/azure/sql/instance-failover-group#az-sql-instance-failover-group-show) | Hiermee haalt u de configuratie van een failovergroep op|
| [az sql instance-failover-group set-primary](/cli/azure/sql/instance-failover-group#az-sql-instance-failover-group-set-primary) | Triggers failover van een failovergroep naar de secundaire instantie|
| [az sql instance-failover-group delete](/cli/azure/sql/instance-failover-group#az-sql-instance-failover-group-delete) | Een failovergroep verwijderen |

* * *

> [!IMPORTANT]
> Zie [Een failovergroep voor één database configureren en failoveren voor](scripts/sql-database-add-single-db-to-failover-group-powershell.md)een voorbeeldscript .

### <a name="rest-api-manage-sql-database-failover-groups-with-single-and-pooled-databases"></a>REST API: SQL-databasefailovergroepen beheren met afzonderlijke en gepoolde databases

| API | Beschrijving |
| --- | --- |
| [Failovergroep maken of bijwerken](https://docs.microsoft.com/rest/api/sql/failovergroups/createorupdate) | Een failovergroep maken of bijwerken |
| [Failovergroep verwijderen](https://docs.microsoft.com/rest/api/sql/failovergroups/delete) | Hiermee verwijdert u een failovergroep van de server |
| [Failover (gepland)](https://docs.microsoft.com/rest/api/sql/failovergroups/failover) | Activeert failover van de huidige primaire server naar de secundaire server met volledige gegevenssynchronisatie.|
| [Force Failover Toestaan gegevensverlies](https://docs.microsoft.com/rest/api/sql/failovergroups/forcefailoverallowdataloss) | Hiermee wordt failover geactiveerd van de huidige primaire server naar de secundaire server zonder gegevens te synchroniseren. Deze bewerking kan leiden tot gegevensverlies. |
| [Failovergroep krijgen](https://docs.microsoft.com/rest/api/sql/failovergroups/get) | Hiermee haalt u de configuratie van een failovergroep op. |
| [Failovergroepen op server weergeven](https://docs.microsoft.com/rest/api/sql/failovergroups/listbyserver) | Hiermee worden de failovergroepen op een server weergegeven. |
| [Failovergroep bijwerken](https://docs.microsoft.com/rest/api/sql/failovergroups/update) | Werkt de configuratie van een failovergroep bij. |

### <a name="rest-api-manage-failover-groups-with-managed-instances"></a>REST-API: failovergroepen beheren met beheerde exemplaren

| API | Beschrijving |
| --- | --- |
| [Failovergroep maken of bijwerken](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/createorupdate) | De configuratie van een failovergroep maken of bijwerken |
| [Failovergroep verwijderen](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/delete) | Hiermee verwijdert u een failovergroep uit de instantie |
| [Failover (gepland)](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/failover) | Hiermee wordt failover geactiveerd van de huidige primaire instantie naar deze instantie met volledige gegevenssynchronisatie. |
| [Force Failover Toestaan gegevensverlies](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/forcefailoverallowdataloss) | Hiermee wordt failover geactiveerd van de huidige primaire instantie naar de secundaire instantie zonder gegevens te synchroniseren. Deze bewerking kan leiden tot gegevensverlies. |
| [Failovergroep krijgen](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/get) | hiermee wordt de configuratie van een failovergroep opgehaald. |
| [Failovergroepen weergeven - Lijst op locatie](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/listbylocation) | Hiermee worden de failovergroepen op een locatie weergegeven. |

## <a name="next-steps"></a>Volgende stappen

- Zie voor gedetailleerde zelfstudies
    - [Eén database toevoegen aan een failovergroep](sql-database-single-database-failover-group-tutorial.md)
    - [Elastische pool aan een failovergroep toevoegen](sql-database-elastic-pool-failover-group-tutorial.md)
    - [Een beheerde instantie toevoegen aan een failovergroep](sql-database-managed-instance-failover-group-tutorial.md)
- Zie voor voorbeeldscripts:
  - [PowerShell gebruiken voor het configureren van actieve geo-replicatie voor een enkele database in Azure SQL Database](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
  - [PowerShell gebruiken voor het configureren van actieve geo-replicatie voor een pooldatabase in Azure SQL Database](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)
  - [PowerShell gebruiken om een enkele database van Azure SQL Database toe te voegen aan een failovergroep](scripts/sql-database-add-single-db-to-failover-group-powershell.md)
- Zie [Bedrijfscontinuïteitsoverzicht](sql-database-business-continuity.md) voor een overzicht van bedrijfscontinuïteiten
- Zie [geautomatiseerde back-ups van SQL Database](sql-database-automated-backups.md)voor meer informatie over geautomatiseerde back-ups van Azure SQL Database.
- Zie [Een database herstellen van de door de service gestarte back-ups](sql-database-recovery-using-backups.md)voor meer informatie over het gebruik van geautomatiseerde back-ups voor herstel.
- Zie [SQL Database-beveiliging na herstel na noodgevallen](sql-database-geo-replication-security-config.md)voor meer informatie over verificatievereisten voor een nieuwe primaire server en database.
