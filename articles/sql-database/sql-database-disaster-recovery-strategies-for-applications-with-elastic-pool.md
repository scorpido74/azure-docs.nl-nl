---
title: Oplossingen voor noodherstel ontwerpen
description: Ontdek hoe u uw cloudoplossing ontwerpen voor herstel na noodgevallen door het juiste failoverpatroon te kiezen.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: carlrab
ms.date: 01/25/2019
ms.openlocfilehash: 4eeaa187142a6d0d97b12f685ebc455f3844606f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73825876"
---
# <a name="disaster-recovery-strategies-for-applications-using-sql-database-elastic-pools"></a>Noodherstelstrategieën voor toepassingen die sql-databaseelastische pools gebruiken

In de loop der jaren hebben we geleerd dat clouddiensten niet waterdicht zijn en catastrofale incidenten gebeuren. SQL Database biedt verschillende mogelijkheden om te voorzien in de bedrijfscontinuïteit van uw toepassing wanneer deze incidenten zich voordoen. [Elastische pools](sql-database-elastic-pool.md) en afzonderlijke databases ondersteunen dezelfde soort DISASTER Recovery (DR) mogelijkheden. In dit artikel worden verschillende DR-strategieën beschreven voor elastische pools die gebruikmaken van deze SQL Database-functies voor bedrijfscontinuïteit.

In dit artikel wordt het volgende canonieke SaaS ISV-toepassingspatroon gebruikt:

Een moderne cloudgebaseerde webapplicatie voorziet in één SQL-database voor elke eindgebruiker. De ISV heeft veel klanten en maakt daarom gebruik van vele databases, bekend als tenant databases. Omdat de tenantdatabases doorgaans onvoorspelbare activiteitspatronen hebben, gebruikt de ISV een elastische pool om de databasekosten gedurende langere perioden zeer voorspelbaar te maken. De elastische pool vereenvoudigt ook het prestatiebeheer wanneer de gebruikersactiviteit piekt. Naast de tenantdatabases gebruikt de applicatie ook verschillende databases om gebruikersprofielen, beveiliging, het verzamelen van gebruikspatronen etc. te beheren. Beschikbaarheid van de individuele tenants heeft geen invloed op de beschikbaarheid van de toepassing als geheel. De beschikbaarheid en prestaties van beheerdatabases zijn echter essentieel voor de functie van de toepassing en als de beheerdatabases offline zijn, is de hele toepassing offline.

In dit artikel worden DR-strategieën besproken die betrekking hebben op een reeks scenario's, van kostengevoelige opstarttoepassingen tot scenario's met strenge beschikbaarheidsvereisten.

> [!NOTE]
> Als u Premium- of Business Critical-databases en elastische pools gebruikt, u deze bestand weermaken tegen regionale uitval door ze om te zetten naar zoneredundante implementatieconfiguratie. Zie [Zoneredundante databases](sql-database-high-availability.md).

## <a name="scenario-1-cost-sensitive-startup"></a>Scenario 1. Kostengevoelig opstarten

Ik ben een startup bedrijf en ben zeer kostengevoelig.  Ik wil de implementatie en het beheer van de applicatie vereenvoudigen en ik kan een beperkte SLA hebben voor individuele klanten. Maar ik wil ervoor zorgen dat de applicatie als geheel nooit offline is.

Als u aan de eenvoudsvereisten wilt voldoen, implementeert u alle tenantdatabases in één elastische groep in het Azure-gebied van uw keuze en implementeert u beheerdatabases als geogerepliceerde afzonderlijke databases. Voor de noodherstel van huurders, gebruik geo-restore, die komt zonder extra kosten. Om de beschikbaarheid van de beheerdatabases te garanderen, moet u deze geo-repliceren naar een andere regio met behulp van een groep automatische failover (stap 1). De lopende kosten van de configuratie van noodherstel in dit scenario zijn gelijk aan de totale kosten van de secundaire databases. Deze configuratie wordt geïllustreerd op het volgende diagram.

![Afbeelding 1](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-1.png)

Als er een storing optreedt in het primaire gebied, worden de herstelstappen om uw toepassing online te brengen geïllustreerd door het volgende diagram.

* De failovergroep initieert automatische failover van de beheerdatabase naar de DR-regio. De toepassing wordt automatisch opnieuw verbonden met de nieuwe primaire en alle nieuwe accounts en tenant databases worden gemaakt in de DR-regio. De bestaande klanten zien hun gegevens tijdelijk niet beschikbaar.
* Maak de elastische pool met dezelfde configuratie als de oorspronkelijke groep (2).
* Gebruik geo-restore om kopieën van de tenantdatabases te maken (3). U overwegen de afzonderlijke herstelverbindingen te activeren via de verbindingen van de eindgebruiker of een ander toepassingsspecifiek prioriteitsschema te gebruiken.

Op dit moment is uw toepassing weer online in de DR-regio, maar sommige klanten ervaren vertraging bij het openen van hun gegevens.

![Afbeelding 2](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-2.png)

Als de storing tijdelijk was, is het mogelijk dat het primaire gebied wordt hersteld door Azure voordat alle databaseherstels zijn voltooid in het DR-gebied. In dit geval orkestreer en verplaats de toepassing naar het primaire gebied. Het proces neemt de stappen geïllustreerd op het volgende diagram.

* Annuleer alle openstaande aanvragen voor geoherstel.
* De beheerdatabases naar de primaire regio niet overhalen (5). Na het herstel van de regio zijn de oude voorverkiezingen automatisch secondaries geworden. Nu wisselen ze weer van rol.
* Wijzig de verbindingstekenreeks van de toepassing om terug te wijzen op het primaire gebied. Nu worden alle nieuwe accounts en tenantdatabases gemaakt in de primaire regio. Sommige bestaande klanten zien hun gegevens tijdelijk niet beschikbaar.
* Stel alle databases in de DR-groep in op alleen-lezen om te voorkomen dat ze in het DR-gebied kunnen worden gewijzigd (6).
* Voor elke database in de DR-groep die sinds het herstel is gewijzigd, wijzigt u de naam of verwijdert u de bijbehorende databases in de primaire groep (7).
* Kopieer de bijgewerkte databases uit de DR-groep naar de primaire groep (8).
* De DR-groep verwijderen (9)

Op dit moment is uw aanvraag online in de primaire regio met alle tenantdatabases die beschikbaar zijn in de primaire groep.

![Figuur 3](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-3.png)

Het belangrijkste **voordeel** van deze strategie is lage lopende kosten voor redundantie van gegevenslagen. Back-ups worden automatisch genomen door de SQL Database-service zonder herschrijven van toepassingen en zonder extra kosten.  De kosten worden alleen gemaakt wanneer de elastische databases worden hersteld. De **afweging** is dat het volledige herstel van alle tenantdatabases veel tijd kost. De duur van de tijd is afhankelijk van het totale aantal herstelberichten dat u in het DR-gebied en de totale grootte van de tenantdatabases initieert. Zelfs als u prioriteit geeft aan herstel van sommige tenants ten opzichte van andere, concurreert u met alle andere herstelbewegingen die in dezelfde regio worden gestart als de servicearbiters en -beperkingen om de algehele impact op de databases van de bestaande klanten te minimaliseren. Bovendien kan het herstel van de tenantdatabases pas beginnen nadat de nieuwe elastische pool in het DR-gebied is gemaakt.

## <a name="scenario-2-mature-application-with-tiered-service"></a>Scenario 2. Volwassen toepassing met gelaagde service

Ik ben een volwassen SaaS applicatie met gelaagde service aanbiedingen en verschillende SLA's voor trial klanten en voor betalende klanten. Voor de proefklanten moet ik de kosten zo veel mogelijk verlagen. Trial klanten kunnen downtime nemen, maar ik wil de kans op de kans te verminderen. Voor de betalende klanten is elke uitvaltijd een vluchtrisico. Ik wil er dus zeker van zijn dat betalende klanten altijd toegang hebben tot hun gegevens.

Om dit scenario te ondersteunen, scheidt u de proefhuurders van betaalde tenants door ze in afzonderlijke elastische pools te plaatsen. De testklanten hebben een lagere eDTU of vCores per tenant en een lagere SLA met een langere hersteltijd. De betalende klanten bevinden zich in een pool met hogere eDTU of vCores per tenant en een hogere SLA. Om de laagste hersteltijd te garanderen, worden de tenantdatabases van de betalende klanten geo-gerepliceerd. Deze configuratie wordt geïllustreerd op het volgende diagram.

![Figuur 4](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-4.png)

Net als in het eerste scenario zijn de beheerdatabases vrij actief, zodat u er één geo-gerepliceerde database voor gebruikt (1). Dit zorgt voor de voorspelbare prestaties voor nieuwe klantabonnementen, profielupdates en andere beheerbewerkingen. De regio waarin de voorverkiezingen van de beheerdatabases zich bevinden, is de primaire regio en de regio waarin de secondaries van de beheerdatabases zich bevinden, is de DR-regio.

De tenantdatabases van betalende klanten hebben actieve databases in de 'betaalde' groep die in de primaire regio is ingericht. Een secundaire pool met dezelfde naam in het DR-gebied inrichten. Elke tenant wordt geo-gerepliceerd naar de secundaire groep (2). Dit maakt snel herstel van alle tenantdatabases mogelijk met failover.

Als er een storing optreedt in het primaire gebied, worden de herstelstappen om uw toepassing online te brengen geïllustreerd in het volgende diagram:

![Figuur 5](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-5.png)

* Onmiddellijk falen over de beheerdatabases naar de DR-regio (3).
* Wijzig de verbindingstekenreeks van de toepassing om naar het DR-gebied te wijzen. Nu worden alle nieuwe accounts en tenantdatabases gemaakt in de DR-regio. De bestaande proefklanten zien dat hun gegevens tijdelijk niet beschikbaar zijn.
* Verzuim over de databases van de betaalde tenant naar de groep in de DR-regio om de beschikbaarheid onmiddellijk te herstellen (4). Aangezien de failover een snelle wijziging van het metagegevensniveau is, moet u een optimalisatie overwegen waarbij de afzonderlijke failovers op aanvraag worden geactiveerd door de verbindingen van de eindgebruiker.
* Als uw eDTU-grootte of vCore-waarde voor uw secundaire groep lager was dan de primaire omdat de secundaire databases alleen de capaciteit vereisten om de wijzigingslogboeken te verwerken terwijl ze secondaries waren, verhoogt u onmiddellijk de poolcapaciteit om de volledige werkbelasting aan te passen van alle huurders (5).
* Maak de nieuwe elastische groep met dezelfde naam en dezelfde configuratie in het DR-gebied voor de databases van de proefklanten (6).
* Zodra de groep van de proefklanten is gemaakt, gebruikt u geo-herstel om de afzonderlijke databases met proeftenantdatabases in de nieuwe groep te herstellen (7). Overweeg de afzonderlijke herstelverbindingen te activeren door de verbindingen van de eindgebruiker of gebruik een ander toepassingsspecifiek prioriteitsschema.

Op dit moment is uw aanvraag weer online in de DR regio. Alle betalende klanten hebben toegang tot hun gegevens, terwijl de proefklanten vertraging ondervinden bij het openen van hun gegevens.

Wanneer het primaire gebied wordt hersteld door Azure *nadat* u de toepassing in het DR-gebied hebt hersteld, u de toepassing in dat gebied blijven uitvoeren of u besluiten terug te gaan naar het primaire gebied. Als het primaire gebied wordt hersteld *voordat* het failoverproces is voltooid, u overwegen meteen terug te gaan. De failback neemt de stappen die in het volgende diagram worden geïllustreerd:

![Figuur 6](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-6.png)

* Annuleer alle openstaande aanvragen voor geoherstel.
* Fail over de beheerdatabases (8). Na het herstel van de regio wordt de oude primaire automatisch de secundaire. Nu wordt het weer de primaire.  
* Fail over de betaalde tenant databases (9). Ook na het herstel van de regio worden de oude voorverkiezingen automatisch de secondaries. Nu worden ze weer de voorverkiezingen.
* Stel de herstelde proefdatabases in die in het DR-gebied zijn gewijzigd in alleen-lezen (10).
* Voor elke database in de DR-groep voor proefklanten die sinds het herstel is gewijzigd, wijzigt u de naam of verwijdert u de bijbehorende database in de primaire groep voor proefklanten (11).
* Kopieer de bijgewerkte databases uit de DR-groep naar de primaire groep (12).
* Verwijder de DR-groep (13).

> [!NOTE]
> De failoverbewerking is asynchroon. Om de hersteltijd te minimaliseren is het belangrijk dat u de failoveropdracht van de tenantdatabases uitvoert in batches van ten minste 20 databases.

Het belangrijkste **voordeel** van deze strategie is dat het de hoogste SLA biedt voor de betalende klanten. Het garandeert ook dat de nieuwe proeven worden gedeblokkeerd zodra de proef DR pool is gemaakt. De afweging is dat deze instelling de totale kosten van de tenantdatabases verhoogt met de kosten van de secundaire **DR-pool** voor betaalde klanten. Bovendien, als de secundaire pool een andere grootte heeft, ervaren de betalende klanten lagere prestaties na failover totdat de poolupgrade in de DR-regio is voltooid.

## <a name="scenario-3-geographically-distributed-application-with-tiered-service"></a>Scenario 3. Geografisch gedistribueerde toepassing met gelaagde service

Ik heb een volwassen SaaS applicatie met gelaagde service aanbiedingen. Ik wil een zeer agressieve SLA aanbieden aan mijn betaalde klanten en het risico op impact minimaliseren wanneer storingen optreden, omdat zelfs korte onderbreking ontevredenheid van de klant kan veroorzaken. Het is van cruciaal belang dat de betalende klanten altijd toegang hebben tot hun gegevens. De proeven zijn gratis en een SLA wordt niet aangeboden tijdens de proefperiode.

Gebruik drie afzonderlijke elastische pools om dit scenario te ondersteunen. Informeer twee groepen met gelijke grootte met hoge eDTU's of vCores per database in twee verschillende regio's om de tenantdatabases van de betaalde klanten te bevatten. De derde groep met de proeftenants kan lagere eDTU's of vCores per database hebben en worden ingericht in een van de twee regio's.

Om de laagste hersteltijd tijdens uitval te garanderen, worden de tenantdatabases van de betalende klanten geo-gerepliceerd met 50% van de primaire databases in elk van de twee regio's. Op dezelfde manier heeft elke regio 50% van de secundaire databases. Op deze manier, als een regio offline is, wordt slechts 50% van de databases van de betaalde klanten beïnvloed en moet deze mislukken. De andere databases blijven intact. Deze configuratie wordt geïllustreerd in het volgende diagram:

![Figuur 4](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-7.png)

Net als in de vorige scenario's zijn de beheerdatabases vrij actief, zodat ze worden geconfigureerd als afzonderlijke geo-gerepliceerde databases (1). Dit zorgt voor de voorspelbare prestaties van de nieuwe klantabonnementen, profielupdates en andere beheeractiviteiten. Regio A is de primaire regio voor de beheerdatabases en regio B wordt gebruikt voor het herstel van de beheerdatabases.

De tenantdatabases van de betalende klanten worden ook geo-gerepliceerd, maar met voorverkiezingen en secondaries verdeeld tussen regio A en regio B (2). Op deze manier kunnen de primaire tenantdatabases die door de storing worden beïnvloed, niet naar de andere regio worden uitgevoerd en beschikbaar komen. De andere helft van de tenantdatabases wordt helemaal niet beïnvloed.

Het volgende diagram illustreert de herstelstappen die moeten worden genomen als er een storing optreedt in regio A.

![Figuur 5](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-8.png)

* Onmiddellijk mislukken over het beheer databases naar regio B (3).
* Wijzig de verbindingstekenreeks van de toepassing om te wijzen op de beheerdatabases in regio B. Wijzig de beheerdatabases om ervoor te zorgen dat de nieuwe accounts en tenantdatabases worden gemaakt in regio B en dat de bestaande tenantdatabases ook daar worden gevonden. De bestaande proefklanten zien dat hun gegevens tijdelijk niet beschikbaar zijn.
* Verzuim over de databases van de betaalde tenant om 2 in regio B te bundelen om de beschikbaarheid onmiddellijk te herstellen (4). Aangezien de failover een snelle wijziging van het metagegevensniveau is, u een optimalisatie overwegen waarbij de afzonderlijke failovers op aanvraag worden geactiveerd door de verbindingen van de eindgebruiker.
* Aangezien groep 2 nu alleen primaire databases bevat, neemt de totale werkbelasting in de groep toe en kan de eDTU-grootte (5) of het aantal vCores onmiddellijk toenemen.
* Maak de nieuwe elastische pool met dezelfde naam en dezelfde configuratie in regio B voor de databases van de proefklanten (6).
* Zodra de pool is gemaakt, gebruikt u geo-herstel om de database met afzonderlijke proeftenants in de groep te herstellen (7). U overwegen de afzonderlijke herstelverbindingen te activeren via de verbindingen van de eindgebruiker of een ander toepassingsspecifiek prioriteitsschema te gebruiken.

> [!NOTE]
> De failoverbewerking is asynchroon. Om de hersteltijd te minimaliseren, is het belangrijk dat u de failoveropdracht van de tenantdatabases uitvoert in batches van ten minste 20 databases.

Op dit moment is uw aanvraag weer online in regio B. Alle betalende klanten hebben toegang tot hun gegevens, terwijl de proefklanten vertraging ondervinden bij het openen van hun gegevens.

Wanneer regio A is hersteld, moet u beslissen of u regio B wilt gebruiken voor proefklanten of failback wilt gebruiken om de proefklantenpool in regio A te gebruiken. Een van de criteria zou het % van de proeftenantdatabases kunnen zijn die sinds het herstel zijn gewijzigd. Ongeacht die beslissing, moet u de betaalde huurders tussen twee zwembaden opnieuw in evenwicht brengen. het volgende diagram illustreert het proces wanneer de databases met proeftenantdatabases niet teruggaan naar regio A.  

![Figuur 6](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-9.png)

* Annuleer alle openstaande aanvragen voor geoherstel voor de DR-groep.
* Fail over de management database (8). Na het herstel van de regio werd de oude primaire automatisch de secundaire. Nu wordt het weer de primaire.  
* Selecteer welke betaalde tenantdatabases niet terug naar groep 1 gaan en failover naar hun secondaries starten (9). Na het herstel van de regio werden alle databases in groep 1 automatisch secondaries. Nu wordt 50% van hen weer voorverkiezingen.
* Verklein de grootte van groep 2 tot de oorspronkelijke eDTU (10) of het aantal vCores.
* Stel alle herstelde proefdatabases in de regio B in op alleen-lezen (11).
* Voor elke database in de DR-groep die is gewijzigd sinds het herstel, wijzigt u de naam of verwijdert u de bijbehorende database in de primaire groep van de proef (12).
* Kopieer de bijgewerkte databases uit de DR-groep naar de primaire groep (13).
* Verwijder de DR-groep (14).

De belangrijkste **voordelen** van deze strategie zijn:

* Het ondersteunt de meest agressieve SLA voor de betalende klanten omdat het ervoor zorgt dat een storing niet meer dan 50% van de tenantdatabases kan beïnvloeden.
* Het garandeert dat de nieuwe proeven worden gedeblokkeerd zodra de trail DR-pool wordt gemaakt tijdens het herstel.
* Het maakt een efficiënter gebruik van de poolcapaciteit mogelijk, aangezien 50% van de secundaire databases in groep 1 en groep 2 gegarandeerd minder actief zijn dan de primaire databases.

De belangrijkste **afwegingen** zijn:

* De CRUD-bewerkingen ten opzichte van de beheerdatabases hebben een lagere latentie voor de eindgebruikers die zijn verbonden met regio A dan voor de eindgebruikers die zijn verbonden met regio B, omdat ze worden uitgevoerd tegen de primaire beheerdatabases.
* Het vereist een complexer ontwerp van de managementdatabase. Elke tenantrecord heeft bijvoorbeeld een locatietag die moet worden gewijzigd tijdens failover en failback.  
* De betalende klanten kunnen lagere prestaties ervaren dan normaal totdat de zwembadupgrade in regio B is voltooid.

## <a name="summary"></a>Samenvatting

Dit artikel richt zich op de strategieën voor noodherstel voor de databaselaag die wordt gebruikt door een SaaS ISV-multitenanttoepassing. De strategie die u kiest is gebaseerd op de behoeften van de toepassing, zoals het bedrijfsmodel, de SLA die u aan uw klanten wilt aanbieden, budgetbeperking enz. Elke beschreven strategie schetst de voordelen en trade-off, zodat u een weloverwogen beslissing kon nemen. Uw specifieke toepassing bevat waarschijnlijk ook andere Azure-componenten. Zo bekijkt u hun bedrijfscontinuïteitsbegeleiding en orkestreert u het herstel van de databaselaag met hen. Zie Het [ontwerpen van cloudoplossingen voor noodherstel voor](sql-database-designing-cloud-solutions-for-disaster-recovery.md)meer informatie over het beheren van herstel van databasetoepassingen in Azure.  

## <a name="next-steps"></a>Volgende stappen

* Zie [geautomatiseerde back-ups van SQL Database](sql-database-automated-backups.md)voor meer informatie over geautomatiseerde back-ups van Azure SQL Database.
* Zie [Bedrijfscontinuïteitsoverzicht](sql-database-business-continuity.md)voor een overzicht van bedrijfscontinuïteiten.
* Zie [een database herstellen van de door de service gestarte back-ups](sql-database-recovery-using-backups.md)voor meer informatie over het gebruik van geautomatiseerde back-ups voor herstel.
* Zie [Actieve georeplicatie-](sql-database-active-geo-replication.md) en [autofailovergroepen voor](sql-database-auto-failover-group.md)meer informatie over snellere herstelopties.
* Zie [databasekopiëren](sql-database-copy.md)voor meer informatie over het gebruik van geautomatiseerde back-ups voor archivering.
