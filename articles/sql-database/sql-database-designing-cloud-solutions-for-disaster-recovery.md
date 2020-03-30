---
title: Ontwerp wereldwijd beschikbare services
description: Meer informatie over toepassingsontwerp voor zeer beschikbare services met Azure SQL Database.
keywords: cloud disaster recovery, disaster recovery oplossingen, app data backup, geo-replicatie, business continuity planning
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: carlrab
ms.date: 12/04/2018
ms.openlocfilehash: 348bd2b92801217a5aea2ef4d1426c020085e4c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79269066"
---
# <a name="designing-globally-available-services-using-azure-sql-database"></a>Services wereldwijd beschikbaar ontwerpen met Azure SQL Database

Bij het bouwen en implementeren van cloudservices met Azure SQL Database gebruikt u [actieve georeplicatie-](sql-database-active-geo-replication.md) of [autofailovergroepen](sql-database-auto-failover-group.md) om veerkracht te bieden voor regionale storingen en catastrofale storingen. Met dezelfde functie u wereldwijd gedistribueerde toepassingen maken die zijn geoptimaliseerd voor lokale toegang tot de gegevens. In dit artikel worden gemeenschappelijke toepassingspatronen besproken, inclusief de voordelen en afwegingen van elke optie.

> [!NOTE]
> Als u Premium- of Business Critical-databases en elastische pools gebruikt, u deze bestand weermaken tegen regionale uitval door ze om te zetten naar zoneredundante implementatieconfiguratie. Zie [Zoneredundante databases](sql-database-high-availability.md).  

## <a name="scenario-1-using-two-azure-regions-for-business-continuity-with-minimal-downtime"></a>Scenario 1: Twee Azure-regio's gebruiken voor bedrijfscontinuïteit met minimale downtime

In dit scenario hebben de toepassingen de volgende kenmerken:

* Toepassing is actief in één Azure-regio
* Voor alle databasesessies is lees- en schrijftoegang (RW) tot gegevens vereist
* Weblaag en gegevenslaag moeten worden gecollocated om de latentie en verkeerskosten te verlagen
* In wezen is downtime een hoger bedrijfsrisico voor deze toepassingen dan gegevensverlies

In dit geval is de topologie van de implementatie van toepassingen geoptimaliseerd voor het afhandelen van regionale rampen wanneer alle toepassingsonderdelen samen moeten mislukken. Het onderstaande diagram toont deze topologie. Voor geografische redundantie worden de resources van de toepassing geïmplementeerd in regio A en B. De middelen in regio B worden echter pas gebruikt als regio A uitvalt. Er is een failovergroep geconfigureerd tussen de twee regio's om databaseconnectiviteit, replicatie en failover te beheren. De webservice in beide regio's is geconfigureerd om toegang te krijgen tot de database via de ** &lt;&gt;failover-groepsnaam van** de leesschrijflistener .database.windows.net (1). Verkeersbeheerder is ingesteld om [de prioriteitsrouteringsmethode](../traffic-manager/traffic-manager-configure-priority-routing-method.md) te gebruiken (2).  

> [!NOTE]
> [Azure-beheerbeheer](../traffic-manager/traffic-manager-overview.md) wordt in dit artikel alleen gebruikt voor illustratiedoeleinden. U elke load-balancing oplossing gebruiken die de prioriteitsrouteringsmethode ondersteunt.

In het volgende diagram wordt deze configuratie weergegeven voordat een storing wordt uitgevoerd:

![Scenario 1. Configuratie vóór de storing.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario1-a.png)

Na een storing in het primaire gebied detecteert de SQL Database-service dat de primaire database niet toegankelijk is en activeert failover naar het secundaire gebied op basis van de parameters van het automatische failoverbeleid (1). Afhankelijk van de SLA van uw toepassing u een respijtperiode configureren die de tijd tussen de detectie van de storing en de failover zelf regelt. Het is mogelijk dat verkeersbeheerder de failover van het eindpunt initieert voordat de failovergroep de failover van de database activeert. In dat geval kan de webtoepassing niet onmiddellijk opnieuw verbinding maken met de database. Maar de reverbindingen zullen automatisch slagen zodra de database failover is voltooid. Wanneer het mislukte gebied is hersteld en weer online is, wordt de oude primaire automatisch opnieuw verbonden als een nieuw secundair. Het onderstaande diagram illustreert de configuratie na failover.

> [!NOTE]
> Alle transacties die na de failover zijn vastgelegd, gaan verloren tijdens de heraansluiting. Nadat de failover is voltooid, kan de toepassing in regio B de verwerking van de gebruikersverzoeken opnieuw verbinden en opnieuw verwerken. Zowel de webapplicatie als de primaire database bevinden zich nu in regio B en blijven medegevestigd.

![Scenario 1. Configuratie na failover](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario1-b.png)

Als er een storing optreedt in regio B, wordt het replicatieproces tussen de primaire en de secundaire database opgeschort, maar blijft de koppeling tussen de twee intact (1). Beheerd verkeer detecteert dat de verbinding met regio B is verbroken en markeert de endpoint web app 2 als Gedegradeerd (2). De prestaties van de toepassing worden in dit geval niet beïnvloed, maar de database wordt blootgesteld en heeft dus een hoger risico op gegevensverlies in geval dat regio A achter elkaar faalt.

> [!NOTE]
> Voor herstel na noodgevallen raden we de configuratie aan met toepassingsimplementatie beperkt tot twee regio's. Dit komt omdat de meeste Azure-regio's slechts twee regio's hebben. Deze configuratie beschermt uw toepassing niet tegen een gelijktijdige catastrofale storing van beide regio's. In een onwaarschijnlijk geval van een dergelijke fout u uw databases in een derde regio herstellen met behulp van [geoherstelbewerking.](sql-database-disaster-recovery.md#recover-using-geo-restore)
>

 Zodra de storing is verholpen, synchroniseert de secundaire database automatisch met de primaire. Tijdens synchronisatie kunnen de prestaties van de primaire worden beïnvloed. De specifieke impact is afhankelijk van de hoeveelheid gegevens die de nieuwe primaire heeft verkregen sinds de failover. Het volgende diagram illustreert een storing in het secundaire gebied:

![Scenario 1. Configuratie na een storing in de secundaire regio.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario1-c.png)

De belangrijkste **voordelen** van dit ontwerppatroon zijn:

* Dezelfde webtoepassing wordt geïmplementeerd in beide regio's zonder een regiospecifieke configuratie en vereist geen extra logica om failover te beheren.
* De prestaties van toepassingen worden niet beïnvloed door failover als de webapplicatie en de database zijn altijd co-locatie.

De belangrijkste **afweging** is dat de toepassingsbronnen in regio B meestal onderbenut zijn.

## <a name="scenario-2-azure-regions-for-business-continuity-with-maximum-data-preservation"></a>Scenario 2: Azure-regio's voor bedrijfscontinuïteit met maximale gegevensbehoud

Deze optie is het meest geschikt voor toepassingen met de volgende kenmerken:

* Elk gegevensverlies is een hoog bedrijfsrisico. De database failover kan alleen worden gebruikt als een laatste redmiddel als de storing wordt veroorzaakt door een catastrofale storing.
* De applicatie ondersteunt alleen-lezen en lezen-schrijven modi van de bewerkingen en kan werken in "read-only mode" voor een periode van tijd.

In dit patroon schakelt de toepassing over naar de alleen-lezenmodus wanneer de leesschrijfverbindingen time-outfouten krijgen. De webtoepassing wordt geïmplementeerd in beide regio's en bevat een verbinding met het eindpunt van de leesschrijflistener en een andere verbinding met het eindpunt van alleen-lezenlistener (1). Het profiel van de verkeersmanager moet [prioriteitsroutering](../traffic-manager/traffic-manager-configure-priority-routing-method.md)gebruiken. [Eindpuntbewaking](../traffic-manager/traffic-manager-monitoring.md) moet worden ingeschakeld voor het eindpunt van de toepassing in elke regio (2).

In het volgende diagram wordt deze configuratie vóór een storing geïllustreerd:

![Scenario 2. Configuratie vóór de storing.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario2-a.png)

Wanneer de verkeersbeheerder een verbindingsfout met regio A detecteert, schakelt deze automatisch het gebruikersverkeer over naar de toepassingsinstantie in regio B. Met dit patroon is het belangrijk dat u de respijtperiode met gegevensverlies instelt op een voldoende hoge waarde, bijvoorbeeld 24 uur. Het zorgt ervoor dat gegevensverlies wordt voorkomen als de storing binnen die tijd wordt beperkt. Wanneer de webtoepassing in regio B is geactiveerd, mislukken de leesbewerkingen. Op dat moment moet het overschakelen naar de alleen-lezen modus (1). In deze modus worden de aanvragen automatisch doorgestuurd naar de secundaire database. Als de storing wordt veroorzaakt door een catastrofale storing, kan deze waarschijnlijk niet worden beperkt binnen de respijtperiode. Wanneer deze verloopt, activeert de failovergroep de failover. Daarna komt de lees-schrijfluisteraar beschikbaar en de verbindingen erin mislukken niet meer (2). Het volgende diagram illustreert de twee fasen van het herstelproces.

> [!NOTE]
> Als de storing in het primaire gebied binnen de respijtperiode wordt verholpen, detecteert de verkeersbeheerder het herstel van de connectiviteit in het primaire gebied en schakelt het gebruikersverkeer terug naar de toepassingsinstantie in regio A. Die toepassingsinstantie wordt hervat en werkt in de leesschrijfmodus met behulp van de primaire database in regio A, zoals blijkt uit het vorige diagram.

![Scenario 2. Ramp herstel fasen.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario2-b.png)

Als er een storing optreedt in regio B, detecteert de verkeersbeheerder het uitvallen van het eindpunt web-app-2 in regio B en markeert deze gedegradeerd (1). In de tussentijd schakelt de failovergroep de alleen-lezen luisteraar over naar regio A (2). Deze storing heeft geen invloed op de gebruikerservaring, maar de primaire database wordt blootgesteld tijdens de storing. In het volgende diagram wordt een fout in het secundaire gebied geïllustreerd:

![Scenario 2. Uitval van de secundaire regio.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario2-c.png)

Zodra de storing is verholpen, wordt de secundaire database onmiddellijk gesynchroniseerd met de primaire en wordt de alleen-lezen listener teruggezet naar de secundaire database in regio B. Tijdens de synchronisatie kunnen de prestaties van de primaire enigszins worden beïnvloed, afhankelijk van de hoeveelheid gegevens die moet worden gesynchroniseerd.

Dit ontwerppatroon heeft verschillende **voordelen:**

* Het voorkomt gegevensverlies tijdens de tijdelijke uitval.
* Downtime is alleen afhankelijk van hoe snel traffic manager detecteert de verbindingsfout, die configureerbaar is.

De **afweging** is dat de toepassing moet kunnen werken in alleen-lezen modus.

## <a name="scenario-3-application-relocation-to-a-different-geography-without-data-loss-and-near-zero-downtime"></a>Scenario 3: Herplaatsing van toepassingen naar een andere geografie zonder gegevensverlies en bijna nul downtime

In dit scenario heeft de toepassing de volgende kenmerken:

* De eindgebruikers hebben toegang tot de toepassing vanuit verschillende regio's
* De toepassing bevat alleen-lezen workloads die niet afhankelijk zijn van volledige synchronisatie met de nieuwste updates
* Schrijftoegang tot gegevens moet worden ondersteund in dezelfde geografie voor de meerderheid van de gebruikers
* Leeslatentie is essentieel voor de gebruikerservaring

Om aan deze vereisten te voldoen, moet u garanderen dat het gebruikersapparaat **altijd** verbinding maakt met de toepassing die in dezelfde geografie wordt geïmplementeerd voor de alleen-lezen bewerkingen, zoals browsegegevens, analyses enz. Overwegende dat de OLTP-bewerkingen meestal in dezelfde **geografie**worden verwerkt . Bijvoorbeeld, gedurende de dag tijd OLTP operaties worden verwerkt in dezelfde geografie, maar tijdens de off-uren kunnen ze worden verwerkt in een andere geografie. Als de activiteit van de eindgebruiker meestal plaatsvindt tijdens de werkuren, u de optimale prestaties voor de meeste gebruikers het grootste deel van de tijd garanderen. Het volgende diagram toont deze topologie.

De resources van de toepassing moeten worden geïmplementeerd in elke geografie waar u een aanzienlijke gebruiksvraag hebt. Als uw aanvraag bijvoorbeeld actief wordt gebruikt in de Verenigde Staten, de Europese Unie en Zuidoost-Azië, moet de toepassing worden geïmplementeerd in al deze regio's. De primaire database moet dynamisch worden overgeschakeld van de ene geografie naar de volgende aan het einde van de werkuren. Deze methode heet "volg de zon". De OLTP-workload maakt altijd verbinding met de database via de ** &lt;&gt;failover-groupnaam van** de read-write listener .database.windows.net (1). De alleen-lezen workload maakt rechtstreeks verbinding met de lokale database met behulp van de ** &lt;serverendpointservernaam&gt;.database.windows.net** (2) van de databaseserver. Verkeersmanager is geconfigureerd met de [methode voor prestatieroutering](../traffic-manager/traffic-manager-configure-performance-routing-method.md). Het zorgt ervoor dat het apparaat van de eindgebruiker is verbonden met de webservice in de dichtstbijzijnde regio. Verkeersbeheerder moet worden ingesteld met eindpuntbewaking ingeschakeld voor elk eindpunt van de webservice (3).

> [!NOTE]
> De configuratie van de failovergroep bepaalt welk gebied wordt gebruikt voor failover. Omdat de nieuwe primaire is in een andere geografie de failover resulteert in langere latentie voor zowel OLTP en alleen-lezen workloads totdat de getroffen regio is weer online.

![Scenario 3. Configuratie met primaire in Oost-VS.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario3-a.png)

Aan het eind van de dag, bijvoorbeeld om 23.00 uur lokale tijd, moeten de actieve databases worden overgeschakeld naar de volgende regio (Noord-Europa). Deze taak kan volledig worden geautomatiseerd met [Azure Logic Apps.](../logic-apps/logic-apps-overview.md) De taak omvat de volgende stappen:

* Schakel over van de primaire server in de failovergroep naar Noord-Europa met behulp van vriendschappelijke failover (1)
* Verwijder de failovergroep tussen Oost-VS en Noord-Europa
* Maak een nieuwe failovergroep met dezelfde naam, maar tussen Noord-Europa en Oost-Azië (2).
* Voeg de primaire in Noord-Europa en secundaire in Oost-Azië toe aan deze failovergroep (3).

In het volgende diagram wordt de nieuwe configuratie na de geplande failover geïllustreerd:

![Scenario 3. De overgang van de primaire naar Noord-Europa.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario3-b.png)

Als er bijvoorbeeld een storing optreedt in Noord-Europa, wordt de automatische databasefailover geïnitieerd door de failovergroep, wat er effectief toe leidt dat de toepassing eerder dan gepland naar de volgende regio wordt verplaatst (1).  In dat geval is het Amerikaanse Oosten de enige resterende secundaire regio totdat Noord-Europa weer online is. De resterende twee gebieden dienen de klanten in alle drie gebieden door rollen te schakelen. Azure Logic Apps moet dienovereenkomstig worden aangepast. Omdat de overige regio's extra gebruikersverkeer uit Europa genereren, worden de prestaties van de toepassing niet alleen beïnvloed door extra latentie, maar ook door een groter aantal eindgebruikersverbindingen. Zodra de storing is verholpen in Noord-Europa, wordt de secundaire database daar onmiddellijk gesynchroniseerd met de huidige primaire. Het volgende diagram illustreert een storing in Noord-Europa:

![Scenario 3. Uitval in Noord-Europa.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario3-c.png)

> [!NOTE]
> U de tijd verkorten waarop de ervaring van de eindgebruiker in Europa wordt aangetast door de lange latentie. Om dat te doen moet u proactief een toepassingskopie implementeren en de secundaire database(s) in een andere lokale regio (West-Europa) maken als vervanging van de offline toepassingsinstantie in Noord-Europa. Wanneer deze laatste weer online is, u beslissen of u West-Europa wilt blijven gebruiken of de kopie van de applicatie daar wilt verwijderen en terug wilt schakelen naar het gebruik van Noord-Europa.

De belangrijkste **voordelen** van dit ontwerp zijn:

* De alleen-lezen applicatie workload toegang tot gegevens in de kasten regio te allen tijde.
* De lees-write applicatie workload toegang tot gegevens in het dichtstbijzijnde gebied tijdens de periode van de hoogste activiteit in elke geografie
* Omdat de toepassing wordt geïmplementeerd in meerdere regio's, kan het een verlies van een van de regio's overleven zonder significante downtime.

Maar er zijn enkele **afwegingen:**

* Een regionale uitval resulteert in de geografie te worden beïnvloed door langere latentie. Zowel read-write als read-only workloads worden bediend door de toepassing in een andere geografie.
* De alleen-lezen workloads moeten verbinding maken met een ander eindpunt in elke regio.

## <a name="business-continuity-planning-choose-an-application-design-for-cloud-disaster-recovery"></a>Bedrijfscontinuïteitsplanning: kies een toepassingsontwerp voor clouddisaster recovery

Uw specifieke cloud disaster recovery-strategie kan deze ontwerppatronen combineren of uitbreiden om het beste aan de behoeften van uw toepassing te voldoen.  Zoals eerder vermeld, is de strategie die u kiest gebaseerd op de SLA die u aan uw klanten wilt aanbieden en de topologie van de implementatie van toepassingen. Om uw beslissing te begeleiden, worden in de volgende tabel de keuzes op basis van herstelpuntdoelstelling (RPO) en geschatte hersteltijd (ERT) vergeleken.

| Patroon | RPO | Ert |
|:--- |:--- |:--- |
| Actief-passieve implementatie voor disaster recovery met co-locatie databasetoegang |Lees-schrijftoegang < 5 sec |Tijd voor foutdetectie + DNS TTL |
| Actiefactieve implementatie voor taakverdeling voor toepassingen |Lees-schrijftoegang < 5 sec |Tijd voor foutdetectie + DNS TTL |
| Actief-passieve implementatie voor gegevensbewaring |Alleen-lezen toegang < 5 sec | Alleen-lezen toegang = 0 |
||Lees-schrijftoegang = nul | Lees-schrijftoegang = Faaldetectietijd + respijtperiode met gegevensverlies |
|||

## <a name="next-steps"></a>Volgende stappen

* Zie [Bedrijfscontinuïteitsoverzicht](sql-database-business-continuity.md) voor een overzicht van bedrijfscontinuïteiten
* Zie [Actieve georeplicatie](sql-database-active-geo-replication.md)voor meer informatie over actieve georeplicatie .
* Zie [Groepen automatisch failoveren](sql-database-auto-failover-group.md)voor meer informatie over groepen voor automatisch failoveren .
* Zie Strategieën voor herstel van de [elastische groep](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)voor informatie over actieve georeplicatie met elastische groepen .
