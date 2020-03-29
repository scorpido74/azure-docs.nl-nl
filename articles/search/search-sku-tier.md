---
title: Kies een prijscategorie of SKU
titleSuffix: Azure Cognitive Search
description: "Azure Cognitive Search kan worden ingericht in deze SKU's: Gratis, Basic en Standard en Standard is beschikbaar in verschillende resourceconfiguraties en capaciteitsniveaus."
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/30/2020
ms.openlocfilehash: 35dbd064a09a96dae58e1b15a6d8889bda45ee0d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76899844"
---
# <a name="choose-a-pricing-tier-for-azure-cognitive-search"></a>Een prijscategorie kiezen voor Azure Cognitive Search

Wanneer u een Azure Cognitive Search-service maakt, wordt een [resource gemaakt](search-create-service-portal.md) op een prijscategorie (of SKU) die is vastgesteld voor de levensduur van de service. Lagen zijn gratis, basic, standaard en opslag geoptimaliseerd. Standaard en Storage Optimized zijn beschikbaar met verschillende configuraties en capaciteiten.

De meeste klanten beginnen met de gratis laag, zodat ze de service kunnen evalueren. Na evaluatie is het gebruikelijk om een tweede service te maken op een van de hogere niveaus voor ontwikkelings- en productieimplementaties.

## <a name="feature-availability-by-tier"></a>Beschikbaarheid van functies per laag

Bijna elke functie is beschikbaar op elke laag, inclusief Gratis, maar een functie of workflow die resourceintensief is, werkt mogelijk niet goed, tenzij u deze voldoende capaciteit geeft. [AI-verrijking](cognitive-search-concept-intro.md) heeft bijvoorbeeld langlopende vaardigheden die een time-out hebben op een gratis service, tenzij de gegevensset klein is.

In de volgende tabel worden laaggerelateerde functiebeperkingen beschreven.

| Functie | Beperkingen |
|---------|-------------|
| [indexers](search-indexer-overview.md) | Indexers zijn niet beschikbaar op S3 HD. |
| [Door de klant beheerde versleutelingssleutels](search-security-manage-encryption-keys.md) | Niet beschikbaar op de gratis laag. |

## <a name="tiers-skus"></a>SKU's (SKU's)

Lagen worden onderscheiden door:

+ Aantal indexen en indexen die u maken
+ Grootte en snelheid van partities (fysieke opslag)

De laag die u selecteert, bepaalt het factureerbare tarief. De volgende schermafbeelding van azure portal toont de beschikbare lagen, minus prijzen (die u vinden in de portal en op de [prijspagina](https://azure.microsoft.com/pricing/details/search/). **Gratis,** **Basic**en **Standard** zijn de meest voorkomende lagen.

**Gratis** maakt een beperkte zoekservice op een cluster, gedeeld met andere abonnees. U kleine projecten voltooien, waaronder snel starten en zelfstudies, maar u de service niet schalen of aanzienlijke workloads uitvoeren. **Basic** en **Standard** zijn de meest gebruikte factureerbare lagen, waarbij **Standard** de standaard is.

![Prijsniveaus van Azure Cognitive Search](media/search-sku-tier/tiers.png "Prijsniveaus van Azure Cognitive Search")

Sommige lagen zijn geoptimaliseerd voor bepaalde soorten werk. **Standard 3 High Density (S3 HD)** is bijvoorbeeld een *hostingmodus* voor S3, waarbij de onderliggende hardware is geoptimaliseerd voor een groot aantal kleinere indexen en bedoeld is voor multitenancy-scenario's. S3 HD heeft dezelfde kosten per eenheid als S3, maar de hardware is geoptimaliseerd voor snelle bestandsreads op een groot aantal kleinere indexen.

**Storage Optimized** tiers bieden een grotere opslagcapaciteit tegen een lagere prijs per TB dan de standaardlagen. De primaire afweging is een hogere querylatentie, die u moet valideren voor uw specifieke toepassingsvereisten.  Zie [Prestatie- en optimalisatieoverwegingen](search-performance-optimization.md)voor meer informatie over de prestatieoverwegingen van deze laag.

U meer informatie vinden over de verschillende lagen op de [prijspagina,](https://azure.microsoft.com/pricing/details/search/)in het artikel [Servicelimieten in azure cognitive search](search-limits-quotas-capacity.md) en op de portalpagina wanneer u een service indient.

## <a name="billable-events"></a>Factureerbare evenementen

Een oplossing die is gebaseerd op Azure Cognitive Search kan op de volgende manieren kosten met zich meebrengen:

+ Basiskosten van service bij minimale configuratie (een service maken)
+ Incrementele kosten bij het opschalen (replica's of partities toevoegen)
+ Bandbreedtekosten (uitgaande gegevensoverdracht) 
+ Cognitief zoeken (cognitieve services toevoegen voor AI-verrijking, Azure-opslag voor kennisarchief)

### <a name="service-costs"></a>Servicekosten

In tegenstelling tot virtuele machines of andere resources die kunnen worden "onderbroken" om kosten te voorkomen, is een Azure Cognitive Search-service altijd beschikbaar op hardware die is toegewezen voor uw exclusieve gebruik. Als zodanig is het maken van een service een factable event die begint wanneer u de service maakt en eindigt wanneer u de service verwijdert. 

De minimale kosten zijn de eerste zoekeenheid (één replica x één partitie) tegen het factureerbare tarief. Dit minimum is vastgesteld voor de levensduur van de service omdat de service niet kan worden uitgevoerd op iets minder dan deze configuratie. Boven het minimum u onafhankelijk van elkaar replica's en partities toevoegen. Incrementele capaciteitsverhogingen via replica's en partities verhogen uw factuur op basis van de volgende formule: [(replica's x partities x-tarief)](#search-units), waarbij het tarief dat u in rekening wordt gebracht, afhankelijk is van de prijscategorie die u selecteert.

Wanneer u de kosten van een zoekoplossing inschat, moet u er rekening mee houden dat prijzen en capaciteit niet lineair zijn. (Verdubbeling van de capaciteit verdubbelt meer dan de kosten.) Zie [Replica's en partities toewijzen](search-capacity-planning.md#how-to-allocate-replicas-and-partitions)voor een voorbeeld van hoe de formule werkt.

### <a name="bandwidth-charges"></a>Bandbreedtekosten

Het gebruik van [Azure Cognitive Search-indexeerders](search-indexer-overview.md) kan van invloed zijn op facturering, afhankelijk van de locatie van uw services. U kosten voor gegevensuitgangvolledig elimineren als u de Azure Cognitive Search-service maakt in dezelfde regio als uw gegevens. Hier is wat informatie van de [bandbreedte prijspagina:](https://azure.microsoft.com/pricing/details/bandwidth/)

+ Microsoft brengt geen kosten in rekening voor binnenkomende gegevens voor een service op Azure of voor uitgaande gegevens van Azure Cognitive Search.
+ In multiservice-oplossingen worden geen kosten in rekening gebracht voor gegevens die de draad overschrijden wanneer alle services zich in dezelfde regio bevinden.

Er worden wel kosten in rekening gebracht voor uitgaande gegevens als services zich in verschillende regio's bevinden. Deze kosten maken eigenlijk geen deel uit van uw Azure Cognitive Search-factuur. Ze worden hier genoemd, want als u gegevens of met AI-verrijkte indexeerders gebruikt om gegevens uit verschillende regio's op te halen, ziet u kosten weerspiegeld in uw totale factuur.

### <a name="ai-enrichment-with-cognitive-services"></a>AI-verrijking met Cognitive Services

Voor [AI-verrijking](cognitive-search-concept-intro.md)moet u van plan zijn [om een factureerbare Azure Cognitive Services-bron](cognitive-search-attach-cognitive-services.md)in dezelfde regio als Azure Cognitive Search toe te voegen aan de prijscategorie S0 voor verwerking via betaling per gebruik. Er zijn geen vaste kosten verbonden aan het koppelen van cognitieve services. U betaalt alleen voor de verwerking die u nodig hebt.

| Bewerking | Impact op facturering |
|-----------|----------------|
| Documentkraken, tekstextractie | Gratis |
| Documentkraken, beeldextractie | Gefactureerd op basis van het aantal afbeeldingen uit uw documenten. In een [indexerconfiguratie](https://docs.microsoft.com/rest/api/searchservice/create-indexer#indexer-parameters)is **imageAction** de parameter die beeldextractie activeert. Als **imageAction** is ingesteld op 'geen' (de standaardinstelling), worden er geen kosten in rekening gebracht voor het extraheren van afbeeldingen. De snelheid voor afbeeldingsextractie is gedocumenteerd op de [pagina prijsdetails](https://azure.microsoft.com/pricing/details/search/) voor Azure Cognitive Search.|
| [Ingebouwde cognitieve tekstvaardigheden](cognitive-search-predefined-skills.md) | Gefactureerd in hetzelfde tempo als wanneer u de taak hebt uitgevoerd met behulp van Cognitive Services rechtstreeks. |
| Aangepaste vaardigheden | Een aangepaste vaardigheid is functionaliteit die u biedt. De kosten van het gebruik van een aangepaste vaardigheid zijn volledig afhankelijk van de vraag of aangepaste code andere services met een datameter aanroept. |

<a name="search-units"></a>

## <a name="billing-formula-r-x-p--su"></a>Factureringsformule (R x P = SU)

Het belangrijkste factureringsconcept dat u moet begrijpen voor Azure Cognitive Search-bewerkingen is de *zoekeenheid* (SU). Omdat Azure Cognitive Search afhankelijk is van zowel replica's als partities voor indexering en query's, heeft het geen zin om slechts één of het ander te factureren. In plaats daarvan is facturering gebaseerd op een samenstelling van beide.

SU is het product van de *replica's* en *partities* die door een service worden gebruikt: **(R x P = SU)**.

Elke service begint met één SU (één replica vermenigvuldigd met één partitie) als minimum. Het maximum voor elke service is 36 SU's. Dit maximum kan op meerdere manieren worden bereikt: 6 partities x 6 replica's, of 3 partities x 12 replica's, bijvoorbeeld. Het is gebruikelijk om minder dan de totale capaciteit te gebruiken (bijvoorbeeld een 3-replica, 3-partitieservice gefactureerd als 9 SU's). Zie het overzicht [partitie- en replicacombinaties](search-capacity-planning.md#chart) voor geldige combinaties.

Het factureringstarief is per uur per SU. Elke laag heeft een steeds hoger tarief. Hogere lagen worden geleverd met grotere en snellere partities, en dit draagt bij aan een algemeen hoger uurtarief voor die laag. U de tarieven voor elke laag bekijken op de [pagina met prijsdetails.](https://azure.microsoft.com/pricing/details/search/)

De meeste klanten brengen slechts een deel van de totale capaciteit online, met de rest in reserve. Voor facturering bepaalt het aantal partities en replica's dat u online brengt, berekend door de SU-formule, wat u per uur betaalt.

## <a name="how-to-manage-costs"></a>Kosten beheren

De volgende suggesties kunnen u helpen de kosten tot een minimum te beperken:

- Maak alle resources in dezelfde regio, of in zo weinig mogelijk regio's, om bandbreedtekosten te minimaliseren of te elimineren.

- Consolider alle services in één resourcegroep, zoals Azure Cognitive Search, Cognitive Services en andere Azure-services die in uw oplossing worden gebruikt. Zoek in de Azure-portal de brongroep en gebruik de opdrachten **Kostenbeheer** voor inzicht in de werkelijke en verwachte uitgaven.

- Overweeg Azure Web App voor uw front-end toepassing, zodat aanvragen en antwoorden binnen de grenzen van het datacenter blijven.

- Schaal op voor resource-intensieve bewerkingen zoals indexeren en pas vervolgens naar beneden aan voor normale queryworkloads. Begin met de minimale configuratie voor Azure Cognitive Search (één SU bestaande uit één partitie en één replica) en controleer vervolgens de gebruikersactiviteit om gebruikspatronen te identificeren die erop wijzen dat er meer capaciteit nodig is. Als er een voorspelbaar patroon is, u mogelijk de schaal synchroniseren met activiteit (u moet code schrijven om dit te automatiseren).

Ga daarnaast naar [Facturering en kostenbeheer](https://docs.microsoft.com/azure/billing/billing-getting-started) voor ingebouwde tools en functies met betrekking tot uitgaven.

Het tijdelijk afsluiten van een zoekdienst is niet mogelijk. Speciale resources zijn altijd operationeel, toegewezen voor uw exclusieve gebruik voor de levensduur van uw service. Het verwijderen van een service is permanent en verwijdert ook de bijbehorende gegevens.

Wat de service zelf betreft, is de enige manier om uw factuur te verlagen door replica's en partities te verlagen tot een niveau dat nog steeds acceptabele prestaties en [SLA-naleving](https://azure.microsoft.com/support/legal/sla/search/v1_0/)biedt, of een service op een lager niveau te maken (S1-uurtarieven zijn lager dan S2- of S3-tarieven). Ervan uitgaande dat u uw service aan de onderkant van uw belastingsprognoses indient, u, als u de service ontgroeit, een tweede service met grotere niveaus maken, uw indexen op de tweede service opnieuw opbouwen en vervolgens de eerste service verwijderen.

## <a name="how-to-evaluate-capacity-requirements"></a>Capaciteitsvereisten evalueren

In Azure Cognitive Search is de capaciteit gestructureerd als *replica's* en *partities.*

+ Replica's zijn exemplaren van de zoekservice. Elke replica bevat één load-balanced kopie van een index. Een service met zes replica's heeft bijvoorbeeld zes exemplaren van elke index die in de service is geladen.

+ Partities slaan indexen op en splitsen automatisch doorzoekbare gegevens. Twee partities splitsen uw index in de helft, drie partities splitsen het in derden, enzovoort. In termen van capaciteit is *partitiegrootte* de primaire onderscheidende functie tussen lagen.

> [!NOTE]
> Alle standaard- en opslaggeoptimaliseerde lagen ondersteunen [flexibele combinaties van replica's en partities,](search-capacity-planning.md#chart) zodat u uw systeem [optimaliseren voor snelheid of opslag](search-performance-optimization.md) door de balans te wijzigen. De klasse Basic biedt maximaal drie replica's voor hoge beschikbaarheid, maar heeft slechts één partitie. Gratis lagen bieden geen speciale bronnen: computerbronnen worden gedeeld door meerdere abonnees.


### <a name="evaluating-capacity"></a>Evaluatie van de capaciteit

Capaciteit en de kosten van het uitvoeren van de service gaan hand in hand. Lagen leggen beperkingen op aan twee niveaus: opslag en resources. Je moet nadenken over beide, want welke limiet je het eerst bereikt is de effectieve limiet.

Bedrijfsvereisten bepalen doorgaans het aantal indexen dat u nodig hebt. U hebt bijvoorbeeld een globale index nodig voor een grote opslagplaats van documenten. Of u hebt mogelijk meerdere indexen nodig op basis van regio, toepassing of bedrijfsniche.

Om de grootte van een index te bepalen, moet u [er een bouwen.](search-create-index-portal.md) De grootte ervan is gebaseerd op geïmporteerde gegevens en indexconfiguratie, zoals of u suggesties, filtering en sortering inschakelt. Zie [Een basisindex maken ](search-what-is-an-index.md)voor meer informatie over de configuratie-impact op de grootte.

Voor zoeken in volledige tekst is de primaire gegevensstructuur een [omgekeerde indexstructuur,](https://en.wikipedia.org/wiki/Inverted_index) die andere kenmerken heeft dan brongegevens. Voor een omgekeerde index worden grootte en complexiteit bepaald door inhoud, niet noodzakelijkerwijs door de hoeveelheid gegevens die u erin verwerkt. Een grote gegevensbron met een hoge redundantie kan resulteren in een kleinere index dan een kleinere gegevensset die zeer variabele inhoud bevat. Het is dus zelden mogelijk om de indexgrootte af te leiden op basis van de grootte van de oorspronkelijke gegevensset.

> [!NOTE] 
> Hoewel het schatten van toekomstige behoeften aan indexen en opslag kan voelen als giswerk, is het de moeite waard om te doen. Als de capaciteit van een laag te laag blijkt te zijn, moet u een nieuwe service op een hogere laag inrichten en [vervolgens uw indexen opnieuw laden.](search-howto-reindex.md) Er is geen in-place upgrade van een service van de ene SKU naar de andere.
>

### <a name="estimate-with-the-free-tier"></a>Schatting met de laag Gratis

Een benadering voor het schatten van capaciteit is om te beginnen met de gratis laag. Vergeet niet dat de gratis service biedt tot drie indexen, 50 MB opslagruimte, en 2 minuten indexering tijd. Het kan een uitdaging zijn om een verwachte indexgrootte met deze beperkingen te schatten, maar dit zijn de stappen:

+ [Maak een gratis service.](search-create-service-portal.md)
+ Maak een kleine, representatieve dataset.
+ [Bouw een eerste index in de portal](search-create-index-portal.md) en noteer de grootte ervan. Functies en kenmerken hebben een impact op de opslag. Als u bijvoorbeeld suggesters toevoegt (typeahead), wordt de opslagvereisten verhoogd. Met dezelfde gegevensset u proberen meerdere versies van een index te maken, met verschillende kenmerken op elk veld, om te zien hoe de opslagvereisten variëren. Zie ['Implicaties voor opslag' voor](search-what-is-an-index.md#index-size)meer informatie in Een basisindex maken .

Met een ruwe schatting in de hand, u dat bedrag verdubbelen tot budget voor twee indexen (ontwikkeling en productie) en kies vervolgens uw laag dienovereenkomstig.

### <a name="estimate-with-a-billable-tier"></a>Schatting met een factureerbare laag

Speciale resources kunnen grotere bemonsterings- en verwerkingstijden aanpassen voor realistischere schattingen van indexhoeveelheid, grootte en queryvolumes tijdens de ontwikkeling. Sommige klanten springen meteen in met een factureerbare laag en vervolgens opnieuw te evalueren als het ontwikkelingsproject rijpt.

1. [Controleer servicelimieten op elke laag](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity#index-limits) om te bepalen of lagere lagen het aantal indexen kunnen ondersteunen dat u nodig hebt. Op de niveaus Basic, S1 en S2 zijn de indexlimieten respectievelijk 15, 50 en 200. De opslaggeoptimaliseerde laag heeft een limiet van 10 indexen omdat deze is ontworpen om een laag aantal zeer grote indexen te ondersteunen.

1. [Een service maken op een factureerbare laag:](search-create-service-portal.md)

    + Begin laag, bij Basic of S1, als u niet zeker bent over de verwachte belasting.
    + Begin hoog, bij S2 of zelfs S3, als je weet dat je gaat op grote schaal indexering en query belastingen hebben.
    + Begin met Storage Optimized, bij L1 of L2, als u een grote hoeveelheid gegevens indexeert en de querybelasting relatief laag is, zoals bij een interne bedrijfstoepassing.

1. [Stel een eerste index](search-create-index-portal.md) samen om te bepalen hoe brongegevens zich vertalen naar een index. Dit is de enige manier om de indexgrootte te schatten.

1. [Controleer de opslag, servicelimieten, queryvolume en latentie](search-monitor-usage.md) in de portal. De portal toont u query's per seconde, beperkende query's en zoeklatentie. Al deze waarden kunnen u helpen beslissen of u de juiste laag hebt geselecteerd. 

Indexnummer en grootte zijn even belangrijk voor uw analyse. Dit komt omdat maximale limieten worden bereikt door volledig gebruik van opslag (partities) of door maximale limieten voor resources (indexen, indexeerders, enzovoort), afhankelijk van wat het eerst komt. De portal helpt u beide bij te houden, met het huidige gebruik en de maximale limieten naast elkaar op de pagina Overzicht.

> [!NOTE]
> Opslagvereisten kunnen worden opgeblazen als documenten externe gegevens bevatten. In het ideale plaats bevatten documenten alleen de gegevens die u nodig hebt voor de zoekervaring. Binaire gegevens zijn niet doorzoekbaar en moeten afzonderlijk worden opgeslagen (misschien in een Azure-tabel of blob-opslag). Vervolgens moet een veld in de index worden toegevoegd om een URL-verwijzing naar de externe gegevens vast te houden. De maximale grootte van een afzonderlijk document is 16 MB (of minder als u in bulk meerdere documenten uploadt in één aanvraag). Zie [Servicelimieten in Azure Cognitive Search](search-limits-quotas-capacity.md)voor meer informatie.
>

**Overwegingen voor queryvolume**

Query's per seconde (QPS) is een belangrijke statistiek tijdens het afstemmen van de prestaties, maar het is over het algemeen alleen een niveau overweging als u verwacht hoog queryvolume aan het begin.

De standaardlagen kunnen een balans bieden tussen replica's en partities. U de querydoorlooptijd verhogen door replica's toe te voegen voor taakverdeling of partities toe te voegen voor parallelle verwerking. U vervolgens afstemmen op de prestaties nadat de service is ingericht.

Als u vanaf het begin hoge aanhoudende queryvolumes verwacht, moet u hogere standaardlagen overwegen, ondersteund door krachtigere hardware. U vervolgens partities en replica's offline halen of zelfs overschakelen naar een service op een lager niveau als deze queryvolumes niet optreden. Zie [Azure Cognitive Search prestaties en optimalisatie](search-performance-optimization.md)voor meer informatie over het berekenen van querydoorvoer.

De voor opslag geoptimaliseerde lagen zijn handig voor grote gegevensworkloads, waardoor meer algemene beschikbare indexopslag wordt ondersteund voor wanneer querylatentievereisten minder belangrijk zijn. U moet nog steeds extra replica's gebruiken voor taakverdeling en extra partities voor parallelle verwerking. U vervolgens afstemmen op de prestaties nadat de service is ingericht.

**Overeenkomsten op serviceniveau**

De functies Gratis laag en voorbeeld bieden geen [service-level agreements (SLA's).](https://azure.microsoft.com/support/legal/sla/search/v1_0/) Voor alle factureerbare lagen worden SLA's van kracht wanneer u voldoende redundantie voor uw service indient. U moet twee of meer replica's hebben voor query(lees) SLA's. U moet drie of meer replica's hebben voor query- en indexerings-SLA's .You need to have three or more replicas for query and indexing (read-write) SLA's. Het aantal partities heeft geen invloed op SLA's.

## <a name="tips-for-tier-evaluation"></a>Tips voor niveauevaluatie

+ Laat statistieken bouwen rond query's en verzamel gegevens rond gebruikspatronen (query's tijdens kantooruren, indexeren tijdens daluren). Gebruik deze gegevens om beslissingen te nemen over het verlenen van diensten. Hoewel het niet praktisch is op een uur- of dagelijkse cadans, u partities en resources dynamisch aanpassen aan geplande wijzigingen in queryvolumes. U ook ongeplande maar aanhoudende wijzigingen opvangen als de niveaus lang genoeg gelden om actie te ondernemen.

+ Vergeet niet dat het enige nadeel van onderprovisioning is dat u mogelijk een service moet afbreken als de werkelijke vereisten groter zijn dan uw voorspellingen. Om onderbreking van de service te voorkomen, maakt u een nieuwe service op een hoger niveau en voert u deze naast elkaar uit totdat alle apps en aanvragen zich op het nieuwe eindpunt richten.

## <a name="next-steps"></a>Volgende stappen

Begin met een gratis laag en stel een eerste index samen met behulp van een subset van uw gegevens om de kenmerken ervan te begrijpen. De gegevensstructuur in Azure Cognitive Search is een omgekeerde indexstructuur. De grootte en complexiteit van een omgekeerde index wordt bepaald door de inhoud. Vergeet niet dat zeer redundante inhoud meestal resulteert in een kleinere index dan zeer onregelmatige inhoud. Inhoudskenmerken in plaats van de grootte van de gegevensset bepalen dus indexopslagvereisten.

Nadat u een eerste schatting van uw indexgrootte hebt gemaakt, [kunt u een factureerbare service inrichten](search-create-service-portal.md) op een van de niveaus die in dit artikel worden besproken: Basic, Standard of Storage Optimized. Ontspan alle kunstmatige beperkingen op het formaat van gegevens en [herbouw uw index](search-howto-reindex.md) om alle gegevens op te nemen die u wilt doorzoekbaar zijn.

[Wijs partities en replica's](search-capacity-planning.md) toe als dat nodig is om de prestaties en schaal te krijgen die u nodig hebt.

Als de prestaties en capaciteit zijn prima, je bent klaar. Anders maakt u een zoekservice opnieuw op een ander niveau die beter aansluit bij uw behoeften.

> [!NOTE]
> Als u vragen hebt, plaatst u een bericht naar [StackOverflow](https://stackoverflow.com/questions/tagged/azure-search) of [neemt u contact op met Azure-ondersteuning.](https://azure.microsoft.com/support/options/)
