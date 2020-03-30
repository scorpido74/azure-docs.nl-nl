---
title: SaaS-patronen met meerdere huurders
description: Meer informatie over de vereisten en algemene gegevensarchitectuurpatronen van multi-tenant software as a service (SaaS) databasetoepassingen die worden uitgevoerd in de Azure-cloudomgeving.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: billgib, sstein
ms.date: 01/25/2019
ms.custom: seoapril2019
ms.openlocfilehash: 956d74467c69d9924d26f9cae8d902a6ddd84496
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80067491"
---
# <a name="multi-tenant-saas-database-tenancy-patterns"></a>Huurpatronen voor SaaS-database met meerdere tenantn

In dit artikel worden de verschillende huurmodellen beschreven die beschikbaar zijn voor een SaaS-toepassing met meerdere tenantn.

Bij het ontwerpen van een multi-tenant SaaS-toepassing moet u zorgvuldig het huurmodel kiezen dat het beste past bij de behoeften van uw toepassing.  Een huurmodel bepaalt hoe de gegevens van elke tenant worden toegewezen aan opslag.  Uw keuze van het huurmodel heeft invloed op het ontwerp en beheer van toepassingen.  Later overstappen op een ander model is soms duur.

## <a name="a-saas-concepts-and-terminology"></a>A. SaaS concepten en terminologie

In het Software as a Service (SaaS) model verkoopt uw bedrijf geen *licenties* aan uw software. In plaats daarvan, elke klant maakt huurbetalingen aan uw bedrijf, waardoor elke klant een *huurder* van uw bedrijf.

In ruil voor het betalen van huur, elke huurder krijgt toegang tot uw SaaS applicatie componenten, en heeft zijn gegevens opgeslagen in het SaaS-systeem.

Het term *huurmodel* verwijst naar de manier waarop de opgeslagen gegevens van tenants zijn georganiseerd:

- *Een pacht:* &nbsp; elke database slaat gegevens op van slechts één tenant.
- *Multi-tenancy:* &nbsp; Elke database slaat gegevens op van meerdere afzonderlijke tenants (met mechanismen om de privacy van gegevens te beschermen).
- Hybride huurmodellen zijn ook beschikbaar.

## <a name="b-how-to-choose-the-appropriate-tenancy-model"></a>B. Het juiste huurmodel kiezen

In het algemeen heeft het huurmodel geen invloed op de functie van een toepassing, maar het heeft waarschijnlijk invloed op andere aspecten van de algemene oplossing.  De volgende criteria worden gebruikt om elk van de modellen te beoordelen:

- **Schaalbaarheid:**
    - Aantal huurders.
    - Opslag per huurder.
    - Opslag in totaal.
    - Werklast.

- **Tenantisolatie:** &nbsp; gegevensisolatie en -prestaties (of de werkbelasting van één tenant gevolgen heeft voor anderen).

- **Kosten per tenant:** &nbsp; databasekosten.

- **Complexiteit van ontwikkeling:**
    - Wijzigingen in schema.
    - Wijzigingen in query's (vereist door het patroon).

- **Operationele complexiteit:**
    - Monitoren en beheren van prestaties.
    - Schemabeheer.
    - Een huurder herstellen.
    - Herstel na noodgevallen.

- **Aanpasbaarheid:** &nbsp; het gemak van het ondersteunen van schemaaanpassingen die tenantspecifiek of tenantklassespecifiek zijn.

De huurdiscussie is gericht op de *gegevenslaag.*  Maar overweeg even de *toepassingslaag.*  De toepassingslaag wordt behandeld als een monolithische entiteit.  Als u de toepassing in veel kleine componenten verdeelt, kan uw keuze van het huurmodel veranderen.  U sommige componenten anders behandelen dan andere met betrekking tot zowel huur als de gebruikte opslagtechnologie of -platform.

## <a name="c-standalone-single-tenant-app-with-single-tenant-database"></a>C. Zelfstandige app met één tenant met database met één tenant

#### <a name="application-level-isolation"></a>Isolatie op toepassingsniveau

In dit model wordt de hele toepassing herhaaldelijk geïnstalleerd, eenmaal voor elke tenant.  Elk exemplaar van de app is een zelfstandige instantie, zodat deze nooit samenwerkt met een andere zelfstandige instantie.  Elk exemplaar van de app heeft slechts één tenant en heeft daarom slechts één database nodig.  De huurder heeft de database helemaal voor zichzelf.

![Ontwerp van standalone app met precies één single-tenant database.][image-standalone-app-st-db-111a]

Elke app-instantie is geïnstalleerd in een afzonderlijke Azure-brongroep.  De resourcegroep kan deel uitmaken van een abonnement dat eigendom is van de softwareleverancier of de tenant.  In beide gevallen kan de leverancier de software voor de tenant beheren.  Elke toepassingsinstantie is geconfigureerd om verbinding te maken met de bijbehorende database.

Elke tenantdatabase wordt geïmplementeerd als één database.  Dit model biedt de grootste databaseisolatie.  Maar de isolatie vereist dat er voldoende resources worden toegewezen aan elke database om de piekbelastingen te verwerken.  Hier gaat het er om dat elastische pools niet kunnen worden gebruikt voor databases die zijn geïmplementeerd in verschillende resourcegroepen of voor verschillende abonnementen.  Deze beperking maakt dit standalone single-tenant app-model de duurste oplossing vanuit het oogpunt van de totale databasekosten.

#### <a name="vendor-management"></a>Leveranciersbeheer

De leverancier heeft toegang tot alle databases in alle zelfstandige app-exemplaren, zelfs als de app-exemplaren in verschillende tenantabonnementen zijn geïnstalleerd.  De toegang wordt bereikt via SQL-verbindingen.  Met deze toegang tussen eenheden kan de leverancier schemabeheer en cross-databasequery centraliseren voor rapportage- of analysedoeleinden.  Als dit soort gecentraliseerd beheer gewenst is, moet een catalogus worden geïmplementeerd die tenant-id's in kaart brengt aan database-URI's.  Azure SQL Database biedt een shardingbibliotheek die samen met een SQL-database wordt gebruikt om een catalogus te bieden.  De shardingbibliotheek wordt formeel de [Elastic Database Client Library][docu-elastic-db-client-library-536r]genoemd.

## <a name="d-multi-tenant-app-with-database-per-tenant"></a>D. Multi-tenant-app met database per tenant

Dit volgende patroon maakt gebruik van een multi-tenant toepassing met veel databases, allemaal single-tenant databases.  Voor elke nieuwe tenant wordt een nieuwe database ingericht.  De toepassingslaag wordt verticaal *opgeschaald* door meer resources per knooppunt toe te voegen.  Of de app wordt horizontaal *uitgeschaald* door meer knooppunten toe te voegen.  De schaling is gebaseerd op werkbelasting en is onafhankelijk van het aantal of de schaal van de afzonderlijke databases.

![Ontwerp van multi-tenant app met database per tenant.][image-mt-app-db-per-tenant-132d]

#### <a name="customize-for-a-tenant"></a>Aanpassen voor een tenant

Net als het zelfstandige app-patroon geeft het gebruik van databases met één tenant een sterke tenantisolatie.  In elke app waarvan het model alleen databases met één tenant opgeeft, kan het schema voor een bepaalde database worden aangepast en geoptimaliseerd voor de tenant.  Deze aanpassing heeft geen invloed op andere tenants in de app. Misschien heeft een tenant gegevens nodig die verder gaan dan de basisgegevensvelden die alle tenants nodig hebben.  Verder kan het extra gegevensveld een index nodig hebben.

Met database-per-tenant is het aanpassen van het schema voor een of meer afzonderlijke tenants eenvoudig te bereiken.  De leverancier van de toepassing moet procedures ontwerpen om schemaaanpassingen op schaal zorgvuldig te beheren.

#### <a name="elastic-pools"></a>Pools voor Elastic Database

Wanneer databases in dezelfde resourcegroep worden geïmplementeerd, kunnen ze worden gegroepeerd in elastische groepen.  De groepen bieden een kosteneffectieve manier om bronnen te delen in veel databases.  Deze pooloptie is goedkoper dan dat elke database groot genoeg is om de gebruikspieken die het ervaart, op te vangen.  Hoewel gepoolde databases toegang tot bronnen delen, kunnen ze nog steeds een hoge mate van prestatieisolatie bereiken.

![Ontwerp van multi-tenant app met database per tenant, met behulp van elastische pool.][image-mt-app-db-per-tenant-pool-153p]

Azure SQL Database biedt de hulpprogramma's die nodig zijn om het delen te configureren, te bewaken en te beheren.  Prestatiestatistieken op poolniveau en op databaseniveau zijn beschikbaar in de Azure-portal en via Azure Monitor-logboeken.  De statistieken kunnen geweldige inzichten geven in zowel geaggregeerde als tenantspecifieke prestaties.  Afzonderlijke databases kunnen tussen groepen worden verplaatst om gereserveerde resources aan een specifieke tenant te verstrekken.  Met deze tools u op een kosteneffectieve manier goede prestaties garanderen.

#### <a name="operations-scale-for-database-per-tenant"></a>Operations schaal voor database-per-tenant

Het Azure SQL Database-platform heeft veel beheerfuncties die zijn ontworpen om grote aantallen databases op schaal te beheren, zoals meer dan 100.000 databases.  Deze functies maken het database-per-tenant patroon plausibel.

Stel dat een systeem een database met 1000 tenant heeft als enige database.  De database heeft mogelijk 20 indexen.  Als het systeem wordt omgezet in 1000 databases met één tenant, stijgt het aantal indexen tot 20.000.  In SQL Database als onderdeel van [Automatisch stemmen][docu-sql-db-automatic-tuning-771a]zijn de automatische indexeringsfuncties standaard ingeschakeld.  Automatische indexering beheert voor u alle 20.000 indexen en hun lopende create en drop optimalisaties.  Deze geautomatiseerde acties vinden plaats in een afzonderlijke database en worden niet gecoördineerd of beperkt door soortgelijke acties in andere databases.  Automatische indexering behandelt indexen anders in een drukke database dan in een minder drukke database.  Dit type van indexbeheer aanpassing zou onpraktisch zijn op de database-per-tenant schaal als deze enorme beheertaak handmatig moest worden gedaan.

Andere beheerfuncties die goed schalen zijn onder andere:

- Ingebouwde back-ups.
- Hoge beschikbaarheid.
- On-disk encryptie.
- Prestaties telemetrie.

#### <a name="automation"></a>Automation

De beheerbewerkingen kunnen worden gescript en aangeboden via een [devops-model.][http-visual-studio-devops-485m]  De bewerkingen kunnen zelfs worden geautomatiseerd en blootgesteld in de toepassing.

U bijvoorbeeld het herstel van een enkele tenant automatiseren naar een eerder tijdstip.  Het herstel hoeft alleen de database met één tenant te herstellen die de tenant opslaat.  Dit herstel heeft geen invloed op andere tenants, wat bevestigt dat beheeractiviteiten zich op het fijnkorrelige niveau van elke afzonderlijke tenant bevinden.

## <a name="e-multi-tenant-app-with-multi-tenant-databases"></a>E. Multi-tenant-app met databases met meerdere tenant

Een ander beschikbaar patroon is om veel tenants op te slaan in een multi-tenant database.  De toepassingsinstantie kan een willekeurig aantal multi-tenantdatabases hebben.  Het schema van een multi-tenantdatabase moet een of meer tenant-idkolommen bevatten, zodat de gegevens van een bepaalde tenant selectief kunnen worden opgehaald.  Verder kan het schema vereisen een paar tabellen of kolommen die worden gebruikt door slechts een subset van tenants.  Statische code- en referentiegegevens worden echter slechts één keer opgeslagen en worden gedeeld door alle tenants.

#### <a name="tenant-isolation-is-sacrificed"></a>Huurder isolatie wordt opgeofferd

*Gegevens:* &nbsp; Een multi-tenant database offert per noodzakelijkerwijs tenant isolatie op.  De gegevens van meerdere tenants worden samen opgeslagen in één database.  Zorg er tijdens de ontwikkeling voor dat query's nooit gegevens van meer dan één tenant blootleggen.  SQL Database ondersteunt [beveiliging op rijniveau,][docu-sql-svr-db-row-level-security-947w]die kan afdwingen dat gegevens die van een query worden geretourneerd, worden gescoped naar één tenant.

*Verwerking:* &nbsp; een multi-tenant database deelt reken- en opslagbronnen voor al zijn tenants.  De database als geheel kan worden gecontroleerd om ervoor te zorgen dat het aanvaardbaar presteert.  Het Azure-systeem heeft echter geen ingebouwde manier om het gebruik van deze resources door een afzonderlijke tenant te controleren of te beheren.  Daarom heeft de multi-tenantdatabase een verhoogd risico op het tegenkomen van luidruchtige buren, waarbij de werklast van een overactieve tenant van invloed is op de prestatie-ervaring van andere tenants in dezelfde database.  Aanvullende bewaking op toepassingsniveau kan de prestaties op tenantniveau controleren.

#### <a name="lower-cost"></a>Lagere kosten

Over het algemeen hebben databases met meerdere tenant's de laagste kosten per tenant.  Resourcekosten voor één database zijn lager dan voor een elastische groep met een equivalent formaat.  Bovendien kunnen voor scenario's waarin tenants slechts beperkte opslag nodig hebben, mogelijk miljoenen tenants in één database worden opgeslagen.  Geen enkele elastische pool kan miljoenen databases bevatten.  Een oplossing met 1000 databases per groep, met 1000 pools, kan echter de omvang van miljoenen bereiken die het risico loopt onhandelbaar te worden om te beheren.

Twee varianten van een multi-tenant databasemodel worden besproken in wat volgt, waarbij het geshardmulti-tenant model het meest flexibele en schaalbare is.

## <a name="f-multi-tenant-app-with-a-single-multi-tenant-database"></a>F. Multi-tenant-app met één database met meerdere tenant's

Het eenvoudigste databasepatroon met meerdere tenants maakt gebruik van één database om gegevens voor alle tenants te hosten.  Naarmate er meer tenants worden toegevoegd, wordt de database opgeschaald met meer opslag- en rekenbronnen.  Deze opschaing is misschien alles wat nodig is, hoewel er altijd een ultieme schaallimiet is.  Echter, lang voordat die limiet is bereikt, wordt de database onhandig om te beheren.

Beheerbewerkingen die zijn gericht op individuele tenants zijn complexer te implementeren in een multi-tenant database.  En op schaal kunnen deze operaties onaanvaardbaar traag worden.  Een voorbeeld is een point-in-time herstel van de gegevens voor slechts één tenant.

## <a name="g-multi-tenant-app-with-sharded-multi-tenant-databases"></a>G. Multi-tenant app met geshard multi-tenant databases

De meeste SaaS-toepassingen hebben toegang tot de gegevens van slechts één tenant tegelijk.  Met dit toegangspatroon kunnen tenantgegevens worden verspreid over meerdere databases of shards, waarbij alle gegevens voor één tenant in één shard zijn opgenomen.  In combinatie met een multi-tenant database patroon, een geshard model maakt bijna grenzeloze schaal.

![Ontwerp van multi-tenant app met geshard multi-tenant databases.][image-mt-app-sharded-mt-db-174s]

#### <a name="manage-shards"></a>Shards beheren

Sharding voegt complexiteit toe aan zowel het ontwerp als het operationele beheer.  Er is een catalogus vereist waarin de toewijzing tussen tenants en databases kan worden gehandhaafd.  Daarnaast zijn beheerprocedures nodig om de scherven en de huurderspopulatie te beheren.  Procedures moeten bijvoorbeeld zijn ontworpen om shards toe te voegen en te verwijderen en tenantgegevens tussen shards te verplaatsen.  Een manier om te schalen is door het toevoegen van een nieuwe scherf en bevolken met nieuwe huurders.  Op andere momenten u een dichtbevolkte scherf splitsen in twee minder dichtbevolkte scherven.  Nadat verschillende tenants zijn verplaatst of stopgezet, u dunbevolkte shards samenvoegen.  De samenvoeging zou resulteren in een kostenefficiënter gebruik van resources.  Huurders kunnen ook worden verplaatst tussen scherven om workloads in evenwicht te brengen.

SQL Database biedt een split/merge-tool die werkt in combinatie met de shardingsbibliotheek en de catalogusdatabase.  De meegeleverde app kan shards splitsen en samenvoegen en tenantgegevens tussen shards verplaatsen.  De app onderhoudt ook de catalogus tijdens deze bewerkingen, waarbij de betreffende tenants offline worden gemarkeerd voordat ze worden verplaatst.  Na de verhuizing werkt de app de catalogus opnieuw bij met de nieuwe toewijzing en markeert de tenant weer online.

#### <a name="smaller-databases-more-easily-managed"></a>Kleinere databases eenvoudiger te beheren

Door tenants over meerdere databases te distribueren, resulteert de geshard multi-tenant oplossing in kleinere databases die gemakkelijker kunnen worden beheerd.  Als u bijvoorbeeld een specifieke tenant herstelt naar een eerder tijdstip, moet u nu één kleinere database herstellen vanuit een back-up, in plaats van een grotere database die alle tenants bevat. De databasegrootte en het aantal tenants per database kunnen worden gekozen om de werkbelasting en de beheerinspanningen in evenwicht te brengen.

#### <a name="tenant-identifier-in-the-schema"></a>Tenant-id in het schema

Afhankelijk van de gebruikte shardingsaanpak kunnen extra beperkingen worden opgelegd aan het databaseschema.  De sql-database split/merge-toepassing vereist dat het schema de shardingsleutel bevat, wat meestal de tenant-id is.  De tenant-id is het voorloopelement in de primaire sleutel van alle geshard tabellen.  Met de tenant-id kan de gesplitste/samenvoegtoepassing snel gegevens vinden en verplaatsen die zijn gekoppeld aan een specifieke tenant.

#### <a name="elastic-pool-for-shards"></a>Elastisch zwembad voor scherven

Gesharde multi-tenant databases kunnen in elastische pools worden geplaatst.  In het algemeen is het hebben van veel databases met één tenant in een groep net zo kostenefficiënt als het hebben van veel tenants in een paar databases met meerdere tenants.  Multi-tenant databases zijn voordelig wanneer er een groot aantal relatief inactieve tenants zijn.

## <a name="h-hybrid-sharded-multi-tenant-database-model"></a>H. Hybride geshard databasemodel met meerdere tenant

In het hybridemodel hebben alle databases de tenant-id in hun schema.  De databases zijn allemaal in staat om meer dan één tenant op te slaan en de databases kunnen worden geshard.  Dus in de schema-zin, ze zijn allemaal multi-tenant databases.  Toch bevatten sommige van deze databases in de praktijk slechts één tenant.  Hoe dan ook, de hoeveelheid tenants die in een bepaalde database is opgeslagen, heeft geen effect op het databaseschema.

#### <a name="move-tenants-around"></a>Huurders verplaatsen

U een bepaalde tenant op elk gewenst moment verplaatsen naar de eigen multi-tenantdatabase.  En op elk gewenst moment u van gedachten veranderen en de tenant terug verplaatsen naar een database met meerdere tenants.  U ook een tenant toewijzen aan een nieuwe database met één tenant wanneer u de nieuwe database indient.

Het hybride model schittert wanneer er grote verschillen zijn tussen de resourcebehoeften van identificeerbare groepen huurders.  Stel dat huurders die deelnemen aan een gratis proefperiode niet hetzelfde hoge prestatieniveau garanderen als een abonnement op huurders.  Het beleid kan zijn dat huurders in de gratis proefperiode worden opgeslagen in een multi-tenant database die wordt gedeeld tussen alle gratis proeftenants.  Wanneer een gratis proeftenant zich abonneert op de basisservicelaag, kan de tenant worden verplaatst naar een andere multi-tenantdatabase met mogelijk minder tenants.  Een abonnee die betaalt voor de premium servicelaag kan worden verplaatst naar zijn eigen nieuwe single-tenant database.

#### <a name="pools"></a>Pools

In dit hybride model kunnen de databases met één tenant voor abonnees worden geplaatst in resourcepools om databasekosten per tenant te verlagen.  Dit gebeurt ook in het database-per-tenant model.

## <a name="i-tenancy-models-compared"></a>I. Huurmodellen vergeleken

In de volgende tabel worden de verschillen tussen de belangrijkste huurmodellen samengevat.

| Meting | Zelfstandige app | Database per tenant | Geshard multi-tenant |
| :---------- | :------------- | :------------------ | :------------------- |
| Schalen | Middelgroot<br />1-100s | Zeer hoog <br />1-100.000 s | Onbeperkt<br />1-1.000.000 s |
| Isolatie van tenants | Zeer hoog  | Hoog | Laag; behalve voor een enkele huurder (dat is alleen in een MT db). |
| Databasekosten per tenant | Hoog; is formaat voor pieken. | Laag; zwembaden gebruikt. | Laagste, voor kleine huurders in MT DBs. |
| Prestatiebewaking en -beheer | Alleen per tenant | Aggregaat + per tenant | Aggregaat; hoewel is per-huurder alleen voor alleenstaanden. |
| Complexiteit van ontwikkeling | Laag | Laag | Gemiddeld; als gevolg van sharding. |
| Operationele complexiteit | Laag-Hoog. Individueel eenvoudig, complex op schaal. | Laag medium. Patronen richten zich op complexiteit op schaal. | Laag-Hoog. Individueel tenant management is complex. |
| &nbsp; ||||

## <a name="next-steps"></a>Volgende stappen

- [Een wingtip-toepassing met meerdere tenant's implementeren en verkennen die gebruikmaakt van het SaaS-model voor database per tenant - Azure SQL-database][docu-sql-db-saas-tutorial-deploy-wingtip-db-per-tenant-496y]

- [Welkom bij de Wingtip Tickets voorbeeld SaaS Azure SQL Database huurapp][docu-saas-tenancy-welcome-wingtip-tickets-app-384w]


<!--  Article link references.  -->

[http-visual-studio-devops-485m]: https://www.visualstudio.com/devops/

[docu-sql-svr-db-row-level-security-947w]: https://docs.microsoft.com/sql/relational-databases/security/row-level-security

[docu-elastic-db-client-library-536r]: sql-database-elastic-database-client-library.md
[docu-sql-db-saas-tutorial-deploy-wingtip-db-per-tenant-496y]: sql-database-saas-tutorial.md
[docu-sql-db-automatic-tuning-771a]: sql-database-automatic-tuning.md
[docu-saas-tenancy-welcome-wingtip-tickets-app-384w]: saas-tenancy-welcome-wingtip-tickets-app.md


<!--  Image references.  -->

[image-standalone-app-st-db-111a]: media/saas-tenancy-app-design-patterns/saas-standalone-app-single-tenant-database-11.png "Ontwerp van standalone app met precies één single-tenant database."

[image-mt-app-db-per-tenant-132d]: media/saas-tenancy-app-design-patterns/saas-multi-tenant-app-database-per-tenant-13.png "Ontwerp van multi-tenant app met database per tenant."

[image-mt-app-db-per-tenant-pool-153p]: media/saas-tenancy-app-design-patterns/saas-multi-tenant-app-database-per-tenant-pool-15.png "Ontwerp van multi-tenant app met database per tenant, met behulp van elastische pool."

[image-mt-app-sharded-mt-db-174s]: media/saas-tenancy-app-design-patterns/saas-multi-tenant-app-sharded-multi-tenant-databases-17.png "Ontwerp van multi-tenant app met geshard multi-tenant databases."

