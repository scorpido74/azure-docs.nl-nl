---
title: Cloud business continuity - database recovery
description: Ontdek hoe Azure SQL Database bedrijfscontinuïteit en databaseherstel met behulp van de cloud ondersteunt en u helpt om essentiële cloudtoepassingen ononderbroken uit te voeren.
keywords: bedrijfscontinuïteit, bedrijfscontinuïteit met de cloud, databasenoodherstel, databaseherstel
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 06/25/2019
ms.openlocfilehash: 4f30bf112175742566c2957d78154e5a7abd1733
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79096864"
---
# <a name="overview-of-business-continuity-with-azure-sql-database"></a>Overzicht van bedrijfscontinuïteit met Azure SQL Database

**Bedrijfscontinuïteit** in Azure SQL Database verwijst naar de mechanismen, beleidsregels en procedures waarmee uw bedrijf kan blijven werken in het licht van een storing, met name naar de computerinfrastructuur. In de meeste gevallen behandelt Azure SQL Database de disruptieve gebeurtenissen die zich in de cloudomgeving kunnen voordoen en houdt het uw toepassingen en bedrijfsprocessen draaiende. Er zijn echter enkele storende gebeurtenissen die niet automatisch door SQL Database kunnen worden afgehandeld, zoals:

- Gebruiker per ongeluk een rij in een tabel verwijderd of bijgewerkt.
- Kwaadwillende aanvaller is erin geslaagd om gegevens te verwijderen of een database te laten vallen.
- Aardbeving veroorzaakt een stroomstoring en tijdelijke uitgeschakeld datacenter.

In dit overzicht worden de mogelijkheden beschreven die Azure SQL Database biedt voor bedrijfscontinuïteit en herstel na noodgevallen. Meer informatie over opties, aanbevelingen en zelfstudies voor het herstellen van storende gebeurtenissen die kunnen leiden tot gegevensverlies of waardoor uw database en toepassing niet meer beschikbaar zijn. Lees wat u moet doen wanneer een gebruikers- of toepassingsfout van invloed is op de gegevensintegriteit, een Azure-regio een storing heeft of uw toepassing onderhoud vereist.

## <a name="sql-database-features-that-you-can-use-to-provide-business-continuity"></a>SQL Database-functies die u kunt gebruiken voor bedrijfscontinuïteit

Vanuit databaseperspectief zijn er vier belangrijke potentiële verstoringsscenario's:

- Lokale hardware- of softwarefouten die van invloed zijn op het databaseknooppunt, zoals een schijffout.
- Gegevensbeschadiging of verwijdering wordt meestal veroorzaakt door een toepassingsfout of menselijke fout. Dergelijke fouten zijn toepassingsspecifiek en kunnen doorgaans niet worden gedetecteerd door de databaseservice.
- Datacenter storing, mogelijk veroorzaakt door een natuurramp. Dit scenario vereist een zekere mate van georedundantie met een failover van toepassingen naar een alternatief datacenter.
- Upgrade- of onderhoudsfouten, onverwachte problemen die optreden tijdens gepland onderhoud van de infrastructuur of upgrades vereisen mogelijk een snelle terugdraainaar een eerdere databasestatus.

Om de lokale hardware- en softwarefouten te beperken, bevat SQL Database een [architectuur met hoge beschikbaarheid](sql-database-high-availability.md), die automatisch herstel van deze fouten garandeert met een SLA-beschikbaarheid tot 99,995%.  

Om uw bedrijf te beschermen tegen gegevensverlies, maakt SQL Database automatisch wekelijks volledige databaseback-ups, elke 12 uur differentiële databaseback-ups en elke 5 - 10 minuten back-ups van transactielogboeken. De back-ups worden gedurende ten minste 7 dagen opgeslagen in RA-GRS-opslag voor alle servicelagen. Alle servicelagen, behalve de configureerbare bewaarperiode voor basisondersteuning voor point-in-time herstel, tot 35 dagen. 

SQL Database biedt ook verschillende functies voor bedrijfscontinuïteit, die u gebruiken om verschillende ongeplande scenario's te beperken. 

- [Tijdelijke tabellen](sql-database-temporal-tables.md) maken het mogelijk rijversies te herstellen naar ieder gewenst tijdstip.
- [Met ingebouwde geautomatiseerde back-ups](sql-database-automated-backups.md) en [Point in Time Restore](sql-database-recovery-using-backups.md#point-in-time-restore) u de volledige database tot een bepaald moment in de tijd herstellen binnen de geconfigureerde bewaarperiode tot 35 dagen.
- U [een verwijderde database herstellen](sql-database-recovery-using-backups.md#deleted-database-restore) naar het punt waarop deze is verwijderd als de SQL **Database-server niet is verwijderd.**
- [Met het bewaren van back-ups op lange termijn](sql-database-long-term-retention.md) u de back-ups tot 10 jaar houden.
- [Met actieve georeplicatie](sql-database-active-geo-replication.md) u leesbare replica's maken en handmatig mislukken naar een replica in het geval van een uitval van datacenters of een upgrade van toepassingen.
- [Met de groep Auto-failover](sql-database-auto-failover-group.md#auto-failover-group-terminology-and-capabilities) kan de toepassing automatisch herstellen in het geval van een uitval van het datacenter.

## <a name="recover-a-database-within-the-same-azure-region"></a>Een database herstellen binnen dezelfde Azure-regio

U automatische databaseback-ups gebruiken om een database te herstellen naar een tijdstip in het verleden. Op deze manier u herstellen van gegevensbeschadigingen veroorzaakt door menselijke fouten. Met point-in-time restore u een nieuwe database maken in dezelfde server die de status van gegevens vertegenwoordigt voorafgaand aan de corrumperende gebeurtenis. Voor de meeste databases duurt het herstellen van bewerkingen minder dan 12 uur. Het kan langer duren om een zeer grote of zeer actieve database te herstellen. Zie [de hersteltijd van de database](sql-database-recovery-using-backups.md#recovery-time)voor meer informatie over hersteltijd. 

Als de maximale ondersteunde back-upbewaarperiode voor point-in-time restore (PITR) niet voldoende is voor uw toepassing, u deze verlengen door een LTR-beleid (Long Term Retention) voor de database(s) te configureren. Zie Voor meer informatie voor het [bewaren van back-ups op lange termijn](sql-database-long-term-retention.md).

## <a name="compare-geo-replication-with-failover-groups"></a>Geo-replicatie vergelijken met failovergroepen

[Groepen voor automatische failover](sql-database-auto-failover-group.md#auto-failover-group-terminology-and-capabilities) vereenvoudigen de implementatie en het gebruik van [georeplicatie](sql-database-active-geo-replication.md) en voegen de extra mogelijkheden toe zoals beschreven in de volgende tabel:

|                                              | Geo-replicatie | Failover-groepen  |
|:---------------------------------------------| :-------------- | :----------------|
| Automatische failover                           |     Nee          |      Ja         |
| Meerdere databases tegelijk mislukken  |     Nee          |      Ja         |
| Gebruiker moet verbindingstekenreeks bijwerken na failover      |     Ja         |      Nee          |
| Ondersteunde beheerde instantie                   |     Nee          |      Ja         |
| Kan zich in dezelfde regio bevinden als primaire             |     Ja         |      Nee          |
| Meerdere replica's                            |     Ja         |      Nee          |
| Ondersteunt leesschaal                          |     Ja         |      Ja         |
| &nbsp; | &nbsp; | &nbsp; |


## <a name="recover-a-database-to-the-existing-server"></a>Een database herstellen naar de bestaande server

Hoewel zeldzaam, kan er een storing optreden in een Azure-datacenter. Wanneer er een storing optreedt, veroorzaakt deze een bedrijfsonderbreking die mogelijk slechts een paar minuten maar ook enkele uren kan duren.

- Een optie is om te wachten tot de database weer online komt wanneer de storing in het datacenter is verholpen. Dit werkt voor toepassingen waarvoor het niet erg is dat de database offline is. Bijvoorbeeld een ontwikkelingsproject of een gratis proefversie waaraan u niet voortdurend hoeft te werken. Wanneer een datacenter een storing heeft, weet u niet hoe lang de storing kan duren, dus deze optie werkt alleen als u uw database een tijdje niet nodig hebt.
- Een andere optie is het herstellen van een database op elke server in een Azure-regio met behulp van [georedundante databaseback-ups](sql-database-recovery-using-backups.md#geo-restore) (geo-restore). Geo-restore maakt gebruik van een geo-redundante back-up als bron en kan worden gebruikt om een database te herstellen, zelfs als de database of het datacenter niet toegankelijk is als gevolg van een storing.
- Ten slotte u snel herstellen van een storing als u geo-secundair hebt geconfigureerd met behulp van [actieve georeplicatie](sql-database-active-geo-replication.md) of een [groep voor automatische failover](sql-database-auto-failover-group.md) voor uw database of databases. Afhankelijk van uw keuze van deze technologieën, u handmatige of automatische failover gebruiken. Hoewel failover zelf slechts een paar seconden duurt, duurt het minstens 1 uur voordat de service deze activeert. Dit is nodig om ervoor te zorgen dat de failover wordt gerechtvaardigd door de omvang van de storing. Ook kan de failover leiden tot klein gegevensverlies als gevolg van de aard van asynchrone replicatie. 

Tijdens het ontwikkelen van uw plan voor bedrijfscontinuïteit moet u weten wat de maximaal acceptabele tijd is voordat de toepassing volledig is hersteld na de verstorende gebeurtenis. De tijd die nodig is voor de toepassing om volledig te herstellen staat bekend als Recovery time objective (RTO). U moet ook de maximale periode van recente gegevensupdates (tijdsinterval) begrijpen die de toepassing kan tolereren wanneer deze wordt hersteld van een ongeplande storende gebeurtenis. Het potentiële gegevensverlies staat bekend als Recovery point objective (RPO).

Verschillende herstelmethoden bieden verschillende niveaus van RPO en RTO. U een specifieke herstelmethode kiezen of een combinatie van methoden gebruiken om volledig toepassingsherstel te bereiken. In de volgende tabel worden RPO en RTO van elke hersteloptie vergeleken. Auto-failovergroepen vereenvoudigen de implementatie en het gebruik van georeplicatie en voegen de extra mogelijkheden toe zoals beschreven in de volgende tabel.

| Herstelmethode | Rto | RPO |
| --- | --- | --- | 
| Geo-herstel van geo-gerepliceerde back-ups | 12 uur | 1 uur |
| Automatische failover-groepen | 1 uur | 5 s |
| Handmatige database-failover | 30 s | 5 s |

> [!NOTE]
> *Handmatige database failover* verwijst naar failover van een enkele database om de geo-gerepliceerde secundaire met behulp van de [ongeplande modus](sql-database-active-geo-replication.md#active-geo-replication-terminology-and-capabilities).
Zie de tabel eerder in dit artikel voor meer informatie over de auto-failover RTO en RPO.


Gebruik groepen voor automatische failoverals als uw toepassing aan een van deze criteria voldoet:

- Is bedrijfskritiek.
- Heeft een service level agreement (SLA) die geen 12 uur of meer downtime toestaat.
- Downtime kan leiden tot financiële aansprakelijkheid.
- Heeft een hoge mate van gegevens te veranderen en 1 uur verlies van gegevens is niet aanvaardbaar.
- De extra kosten van actieve geo-replicatie zijn lager dan de mogelijke financiële verplichting en het bijbehorende bedrijfsverlies.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-protecting-important-DBs-from-regional-disasters-is-easy/player]
>

Afhankelijk van uw toepassingsvereisten u ervoor kiezen om een combinatie van databaseback-ups en actieve georeplicatie te gebruiken. Zie [Ontwerp een toepassing voor clouddisaster recovery](sql-database-designing-cloud-solutions-for-disaster-recovery.md) en Elastic pool disaster recovery [strategies](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)voor een bespreking van ontwerpoverwegingen voor zelfstandige databases en voor elastische pools met behulp van deze functies voor bedrijfscontinuïteit.

De volgende secties geven een overzicht van de stappen die moeten worden hersteld met behulp van databaseback-ups of actieve georeplicatie. Zie [Een SQL-database herstellen van een storing](sql-database-disaster-recovery.md)voor gedetailleerde stappen, inclusief planningsvereisten, herstelstappen na het herstel en informatie over het simuleren van een storing om een noodhersteloefening uit te voeren.

### <a name="prepare-for-an-outage"></a>Voorbereiden op een storing

Ongeacht de functie voor bedrijfscontinuïteit die u gebruikt, moet u:

- Identificeer en bereid de doelserver voor, inclusief IP-firewallregels op serverniveau, aanmeldingen en machtigingen op masterdatabaseniveau.
- Bepalen hoe clients en clienttoepassingen naar de nieuwe server worden omgeleid.
- Andere afhankelijkheden documenteren, zoals controle-instellingen en waarschuwingen.

Als u zich niet goed voorbereidt, kost het online zetten van uw toepassingen na een failover of een databaseherstel extra tijd en vereist het waarschijnlijk ook het oplossen van problemen in een tijd van stress - een slechte combinatie.

### <a name="fail-over-to-a-geo-replicated-secondary-database"></a>Niet overgaan naar een geo-gerepliceerde secundaire database

Als u actieve georeplicatie- of auto-failovergroepen gebruikt als herstelmechanisme, u een automatisch failoverbeleid configureren of [handmatige ongeplande failover](sql-database-active-geo-replication-portal.md#initiate-a-failover)gebruiken. Eenmaal gestart, de failover zorgt ervoor dat de secundaire wordt de nieuwe primaire en klaar om nieuwe transacties op te nemen en te reageren op query's - met minimale gegevensverlies voor de gegevens nog niet gerepliceerd. Zie [Een toepassing ontwerpen voor clouddisaster recovery voor](sql-database-designing-cloud-solutions-for-disaster-recovery.md)informatie over het ontwerpen van het failoverproces.

> [!NOTE]
> Wanneer het datacenter weer online komt, maken de oude voorverkiezingen automatisch opnieuw verbinding met de nieuwe primaire en worden ze secundaire databases. Als u de primaire terug naar de oorspronkelijke regio moet verplaatsen, u een geplande failover handmatig starten (failback).

### <a name="perform-a-geo-restore"></a>Geo-herstel uitvoeren

Als u de geautomatiseerde back-ups met georedundante opslag gebruikt (standaard ingeschakeld), u de database herstellen met behulp van [geoherstel.](sql-database-disaster-recovery.md#recover-using-geo-restore) Herstel vindt meestal plaats binnen 12 uur - met gegevensverlies van maximaal een uur bepaald door wanneer de laatste log back-up werd genomen en gerepliceerd. Totdat het herstellen is voltooid, kan de database geen transacties registreren en niet reageren op query's. Let op, geo-restore herstelt de database alleen naar het laatst beschikbare moment.

> [!NOTE]
> Als het datacenter weer online komt voordat u uw aanvraag overschakelt naar de herstelde database, u het herstel annuleren.

### <a name="perform-post-failover--recovery-tasks"></a>Taken na failover/hersteltaken uitvoeren

Na herstel via een van beide herstelmechanismen moet u de volgende aanvullende taken uitvoeren voordat uw gebruikers en toepassingen opnieuw actief zijn:

- Clients en clienttoepassingen omleiden naar de nieuwe server en herstelde database
- Zorg ervoor dat er geschikte IP-firewallregels op serverniveau zijn voor gebruikers om firewalls op [databaseniveau](sql-database-firewall-configure.md#use-the-azure-portal-to-manage-server-level-ip-firewall-rules) aan te sluiten of te gebruiken om geschikte regels mogelijk te maken.
- Ervoor zorgen dat er geschikte aanmeldingen en machtigingen op hoofddatabaseniveau aanwezig zijn (of [ingesloten gebruikers](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable) gebruiken)
- Controles configureren, indien van toepassing
- Waarschuwingen configureren, indien van toepassing

> [!NOTE]
> Als u een failovergroep gebruikt en verbinding maakt met de databases met behulp van de read-write-lstener, gebeurt de omleiding na failover automatisch en transparant naar de toepassing.

## <a name="upgrade-an-application-with-minimal-downtime"></a>Een toepassing upgraden met minimale uitvaltijd

Soms moet een toepassing offline worden gehaald vanwege gepland onderhoud, zoals een upgrade van de toepassing. [Met toepassingsupgrades beheren,](sql-database-manage-application-rolling-upgrade.md) wordt beschreven hoe u actieve georeplicatie gebruiken om rolling-upgrades van uw cloudtoepassing mogelijk te maken om downtime tijdens upgrades te minimaliseren en een herstelpad te bieden als er iets misgaat.

## <a name="next-steps"></a>Volgende stappen

Zie [Een toepassing voor clouddisaster recovery](sql-database-designing-cloud-solutions-for-disaster-recovery.md) en Elastic pool disaster recovery strategies ontwerpen voor een bespreking van overwegingen voor het ontwerpen van toepassingen voor zelfstandige databases en voor elastische [zwembadherstel.](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)
