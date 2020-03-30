---
title: Herstel na noodgeval
description: Meer informatie over het herstellen van een database van een regionale datacenterstoring of -fout met de actieve georeplicatie van Azure SQL Database en mogelijkheden voor geoherstel.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 06/21/2019
ms.openlocfilehash: d28edd28dcbe31bfe63c2d0a9c3e975967efef04
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256378"
---
# <a name="restore-an-azure-sql-database-or-failover-to-a-secondary"></a>Een Azure SQL-database of failover herstellen naar een secundaire

Azure SQL Database biedt de volgende mogelijkheden voor het herstellen van een storing:

- [Actieve georeplicatie](sql-database-active-geo-replication.md)
- [Automatische failover-groepen](sql-database-auto-failover-group.md)
- [Geo-herstel](sql-database-recovery-using-backups.md#point-in-time-restore)
- [Zoneredundante databases](sql-database-high-availability.md)

Zie [Bedrijfscontinuïteit](sql-database-business-continuity.md)voor meer informatie over bedrijfscontinuïteitsscenario's en de functies die deze scenario's ondersteunen.

> [!NOTE]
> Als u zoneredundante Premium- of Business Critical-databases of -groepen gebruikt, wordt het herstelproces geautomatiseerd en is de rest van dit materiaal niet van toepassing.

> [!NOTE]
> Zowel primaire als secundaire databases moeten dezelfde servicelaag hebben. Het wordt ook sterk aanbevolen dat de secundaire database wordt gemaakt met dezelfde rekengrootte (DTU's of vCores) als de primaire. Zie [Upgraden of downgraden als primaire database voor](sql-database-active-geo-replication.md#upgrading-or-downgrading-primary-database)meer informatie.

> [!NOTE]
> Gebruik een of meer failovergroepen om failover van meerdere databases te beheren.
> Als u een bestaande georeplicatierelatie toevoegt aan de failovergroep, controleert u of de geo-secundaire is geconfigureerd met dezelfde servicelaag en rekengrootte als de primaire. Zie [Autofailovergroepen gebruiken om een transparante en gecoördineerde failover van meerdere databases mogelijk te maken](sql-database-auto-failover-group.md)voor meer informatie.

## <a name="prepare-for-the-event-of-an-outage"></a>Voorbereiden op het geval van een storing

Voor succes met herstel naar een ander gegevensgebied met behulp van failovergroepen of georedundante back-ups, moet u een server voorbereiden in een andere datacenterstoring om de nieuwe primaire server te worden, mocht de noodzaak zich voordoen en goed gedefinieerde stappen hebben gedocumenteerd en getest om een soepel herstel te garanderen. Deze voorbereidingsstappen omvatten:

- Identificeer de SQL Database-server in een andere regio om de nieuwe primaire server te worden. Voor geo-herstel is dit over het algemeen een server in het [gekoppelde gebied](../best-practices-availability-paired-regions.md) voor het gebied waarin uw database zich bevindt. Dit elimineert de extra verkeerskosten tijdens de geo-herstelbewerkingen.
- Identificeer en definieer optioneel de IP-firewallregels op serverniveau die nodig zijn voor gebruikers om toegang te krijgen tot de nieuwe primaire database.
- Bepaal hoe u gebruikers gaat doorleiden naar de nieuwe primaire server, bijvoorbeeld door verbindingstekenreeksen te wijzigen of door DNS-vermeldingen te wijzigen.
- Identificeer en maak eventueel de aanmeldingen die aanwezig moeten zijn in de hoofddatabase op de nieuwe primaire server en zorg ervoor dat deze aanmeldingen de juiste machtigingen hebben in de hoofddatabase, indien aanwezig. Zie [SQL Database-beveiliging na herstel na een ramp voor](sql-database-geo-replication-security-config.md) meer informatie
- Identificeer waarschuwingsregels die moeten worden bijgewerkt om naar de nieuwe primaire database te worden toegewezen.
- De controleconfiguratie op de huidige primaire database documenteren
- Voer een [noodhersteloefening uit.](sql-database-disaster-recovery-drills.md) Als u een storing voor geoherstel wilt simuleren, u de brondatabase verwijderen of de naam van de naam wijzigen om de verbindingsprocedure van toepassingen te mislukken. Als u een storing wilt simuleren met failovergroepen, u de webtoepassing of virtuele machine uitschakelen die is aangesloten op de database of de database mislukken om fouten in de verbindingsmogelijkheden van toepassingen te veroorzaken.

## <a name="when-to-initiate-recovery"></a>Wanneer herstel te starten

De herstelbewerking heeft gevolgen voor de toepassing. Het vereist het wijzigen van de SQL-verbindingstekenreeks of omleiding met DNS en kan leiden tot permanent gegevensverlies. Daarom moet dit alleen worden gedaan wanneer de storing waarschijnlijk langer duurt dan de hersteltijddoelstelling van uw toepassing. Wanneer de toepassing wordt geïmplementeerd in de productie, moet u regelmatig de status van de toepassing controleren en de volgende gegevenspunten gebruiken om te beweren dat het herstel gerechtvaardigd is:

1. Permanente verbindingsfout van de toepassingslaag naar de database.
2. De Azure-portal toont een waarschuwing over een incident in de regio met brede impact.

> [!NOTE]
> Als u failovergroepen gebruikt en automatische failover kiest, wordt het herstelproces geautomatiseerd en transparant voor de toepassing.

Afhankelijk van uw toepassingstolerantie voor downtime en mogelijke zakelijke aansprakelijkheid u de volgende herstelopties overwegen.

Gebruik de [Herstelbare database](https://msdn.microsoft.com/library/dn800985.aspx) *(LastAvailableBackupDate)* om het nieuwste geo-gerepliceerde herstelpunt te krijgen.

## <a name="wait-for-service-recovery"></a>Wachten op serviceherstel

De Azure-teams werken hard om de beschikbaarheid van de service zo snel mogelijk te herstellen, maar afhankelijk van de hoofdoorzaak kan het uren of dagen duren.  Als uw toepassing aanzienlijke downtime kan verdragen, u gewoon wachten tot het herstel is voltooid. In dit geval is er geen actie van uw kant vereist. U de huidige servicestatus bekijken op ons [Azure Service Health Dashboard.](https://azure.microsoft.com/status/) Na het herstel van de regio wordt de beschikbaarheid van uw toepassing hersteld.

## <a name="fail-over-to-geo-replicated-secondary-server-in-the-failover-group"></a>Overgaan naar geo-gerepliceerde secundaire server in de failovergroep

Als de downtime van uw toepassing kan leiden tot zakelijke aansprakelijkheid, moet u failovergroepen gebruiken. Het stelt de toepassing in staat om de beschikbaarheid in een andere regio snel te herstellen in geval van een storing. Zie [Een geogedistribueerde database implementeren](sql-database-implement-geo-distributed-database.md)voor een zelfstudie.

Als u de beschikbaarheid van de database(s) wilt herstellen, moet u de failover naar de secundaire server starten met behulp van een van de ondersteunde methoden.

Gebruik een van de volgende hulplijnen om niet over te gaan naar een geo-gerepliceerde secundaire database:

- [Niet overgaan naar een geo-gerepliceerde secundaire server met behulp van de Azure-portal](sql-database-geo-replication-portal.md)
- [Overgaan naar de secundaire server met PowerShell](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
- [Overgaan naar een secundaire server met Transact-SQL (T-SQL)](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#e-failover-to-a-geo-replication-secondary)

## <a name="recover-using-geo-restore"></a>Herstellen met geo-herstel

Als de downtime van uw toepassing niet leidt tot bedrijfsaansprakelijkheid, u [geo-restore](sql-database-recovery-using-backups.md) gebruiken als methode om uw toepassingsdatabase(s) te herstellen. Het maakt een kopie van de database van de nieuwste geo-redundante back-up.

## <a name="configure-your-database-after-recovery"></a>Uw database configureren na herstel

Als u geo-herstel gebruikt om te herstellen van een storing, moet u ervoor zorgen dat de verbinding met de nieuwe databases correct is geconfigureerd, zodat de normale toepassingsfunctie kan worden hervat. Dit is een checklist van taken om uw herstelde databaseproductie gereed te krijgen.

### <a name="update-connection-strings"></a>Verbindingstekenreeksen bijwerken

Omdat uw herstelde database zich op een andere server bevindt, moet u de verbindingstekenreeks van uw toepassing bijwerken om naar die server te wijzen.

Zie de juiste ontwikkeltaal voor uw [verbindingsbibliotheek](sql-database-libraries.md)voor meer informatie over het wijzigen van verbindingstekenreeksen.

### <a name="configure-firewall-rules"></a>Firewallregels configureren

U moet ervoor zorgen dat de firewallregels die op de server en in de database zijn geconfigureerd, overeenkomen met de regels die zijn geconfigureerd op de primaire server en primaire database. Zie [Firewall-instellingen configureren (Azure SQL Database)](sql-database-configure-firewall-settings.md)voor meer informatie.

### <a name="configure-logins-and-database-users"></a>Aanmeldingen en databasegebruikers configureren

U moet ervoor zorgen dat alle aanmeldingen die door uw toepassing worden gebruikt, bestaan op de server die uw herstelde database host. Zie [Beveiligingsconfiguratie voor georeplicatie voor](sql-database-geo-replication-security-config.md)meer informatie.

> [!NOTE]
> U moet uw serverfirewallregels en aanmeldingen (en hun machtigingen) configureren en testen tijdens een noodhersteloefening. Deze objecten op serverniveau en de configuratie ervan zijn mogelijk niet beschikbaar tijdens de storing.

### <a name="setup-telemetry-alerts"></a>Telemetriewaarschuwingen instellen

U moet ervoor zorgen dat uw bestaande waarschuwingsregelinstellingen worden bijgewerkt om de herstelde database en de verschillende server toe te passen.

Zie [Waarschuwingsmeldingen en](../monitoring-and-diagnostics/insights-receive-alert-notifications.md) Track Service Status ontvangen voor meer informatie over regels voor [databasewaarschuwingen.](../monitoring-and-diagnostics/insights-service-health.md)

### <a name="enable-auditing"></a>Controle inschakelen

Als controle vereist is om toegang te krijgen tot uw database, moet u Controle inschakelen na het herstel van de database. Zie [Databasecontrole](sql-database-auditing.md)voor meer informatie .

## <a name="next-steps"></a>Volgende stappen

- Zie [geautomatiseerde back-ups van SQL Database](sql-database-automated-backups.md) voor meer informatie over geautomatiseerde back-ups van Azure SQL Database
- Zie [Continuïteitsscenario's](sql-database-business-continuity.md) voor meer informatie over ontwerp- en herstelscenario's voor bedrijfscontinuïteit
- Zie [een database herstellen van de door de service gestarte back-ups](sql-database-recovery-using-backups.md) voor meer informatie over het gebruik van geautomatiseerde back-ups voor herstel
