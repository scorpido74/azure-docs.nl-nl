---
title: Een prijs categorie of SKU kiezen
titleSuffix: Azure Cognitive Search
description: "Azure Cognitive Search kan in de volgende Sku's worden ingericht: gratis, basis en standaard, en standaard is beschikbaar in verschillende resource configuraties en capaciteits niveaus."
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 9caadddf281c6df29f6e36057c7b28abcef0e1c4
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/15/2019
ms.locfileid: "74112522"
---
# <a name="choose-a-pricing-tier-for-azure-cognitive-search"></a>Een prijs categorie kiezen voor Azure Cognitive Search

Wanneer u een Azure Cognitive Search-service maakt, [wordt er een resource gemaakt op basis](search-create-service-portal.md) van een prijs categorie (of SKU) die is vastgesteld voor de levens duur van de service. Lagen zijn onder meer gratis, Basic, Standard en opslag geoptimaliseerd. De geoptimaliseerde standaard-en opslag ruimte zijn beschikbaar met verschillende configuraties en capaciteit.

De meeste klanten beginnen met de gratis laag zodat ze de service kunnen evalueren. Na de evaluatie is het gebruikelijk om een tweede service te maken in een van de hogere lagen voor ontwikkelings-en productie-implementaties.

Hoewel alle lagen, met inbegrip van de gratis laag, over het algemeen de functie pariteit bieden, kunnen grotere werk belastingen een nood zaak voor hogere lagen vereisen. [AI-verrijking](cognitive-search-concept-intro.md) heeft bijvoorbeeld langlopende vaardig heden waarvoor een time-out optreedt op een gratis service, tenzij de gegevensset klein is.

> [!NOTE] 
> De uitzonde ring op de functie pariteit is [Indexeer](search-indexer-overview.md)functies, die niet beschikbaar zijn op S3 HD.
>

<!-- For Basic tier and up, you can [adjust replica and partition resources](search-capacity-planning.md) to increase or decrease scale. You could start with one or two of each and then temporarily raise your computational power for a heavy indexing workload. The ability to tune resource levels within a tier adds flexibility, but also slightly complicates your analysis. You might have to experiment to see whether a lower tier with more resources/replicas offers better value and performance than a higher tier with fewer resources. To learn more about when and why you would adjust capacity, see [Performance and optimization considerations](search-performance-optimization.md). -->

## <a name="available-tiers"></a>Beschik bare lagen

Lagen hebben betrekking op de kenmerken van de hardware die als host fungeert voor de service (in plaats van functies) en worden onderscheiden door:

+ Aantal indexen en Indexeer functies dat u kunt maken
+ Grootte en snelheid van partities (fysieke opslag)

De laag die u selecteert, bepaalt het factureer bare percentage. De volgende scherm afbeelding van Azure Portal toont de beschik bare lagen, minus prijzen (die u kunt vinden in de portal en op de [pagina met prijzen](https://azure.microsoft.com/pricing/details/search/). **Gratis**, **basis**en **standaard** zijn de meest voorkomende lagen.

Met **gratis** maakt u een beperkte zoek service in een cluster, gedeeld met andere abonnees. U kunt kleine projecten, inclusief Quick starts en zelf studies, volt ooien, maar u kunt de service niet schalen of aanzienlijke werk belastingen uitvoeren. **Basic** en **Standard** zijn de meest gebruikte factureer bare lagen, waarbij **standaard** de standaard instelling is.

![Prijs categorieën van Azure Cognitive Search](media/search-sku-tier/tiers.png "Prijs categorieën van Azure Cognitive Search")

Sommige lagen zijn geoptimaliseerd voor bepaalde typen werk. **Standaard 3 High density (S3 HD)** is bijvoorbeeld een *hosting modus* voor S3, waarbij de onderliggende hardware is geoptimaliseerd voor een groot aantal kleinere indexen en die is bedoeld voor multitenancy-scenario's. S3 HD heeft dezelfde kosten per eenheid als S3, maar de hardware is geoptimaliseerd voor snelle bestands Lees bewerkingen op een groot aantal kleinere indexen.

**Opslag geoptimaliseerde** lagen bieden een grotere opslag capaciteit tegen een lagere prijs per TB dan de standaard lagen. De primaire balans is een hogere query latentie, die u moet valideren voor uw specifieke toepassings vereisten.  Zie [overwegingen voor prestaties en optimalisatie](search-performance-optimization.md)voor meer informatie over de prestatie overwegingen van deze laag.

Meer informatie over de verschillende lagen op de [pagina met prijzen](https://azure.microsoft.com/pricing/details/search/)vindt u in het artikel [service limieten in azure Cognitive Search](search-limits-quotas-capacity.md) en op de portal pagina wanneer u een service inricht.

## <a name="billable-events"></a>Factureer bare gebeurtenissen

Een oplossing op basis van Azure Cognitive Search kan op de volgende manieren kosten in rekening worden gebracht:

+ Basis kosten van service op minimale configuratie (een service maken)
+ Incrementele kosten bij omhoog schalen (replica's of partities toevoegen)
+ Bandbreedte kosten (uitgaande gegevens overdracht) 
+ Cognitieve zoek opdracht (Cognitive Services koppelen voor AI-verrijking, Azure Storage voor kennis opslag)

### <a name="service-costs"></a>Service kosten

In tegens telling tot virtuele machines of andere bronnen die kunnen worden onderbroken om te voor komen dat er kosten worden bespaard, is een Azure Cognitive Search-service altijd beschikbaar op hardware die is toegewezen voor exclusief gebruik. Als zodanig is het maken van een service een factureer bare gebeurtenis die begint wanneer u de service maakt en eindigt wanneer u de service verwijdert. 

De minimale kosten zijn de eerste Zoek eenheid (één replica x één partitie) met het factureer bare percentage. Dit minimum is vastgesteld voor de levens duur van de service, omdat de service niet kan worden uitgevoerd op een waarde die lager is dan deze configuratie. Naast het minimum kunt u replica's en partities onafhankelijk van elkaar toevoegen. Incrementele toename van capaciteit via replica's en partities verhoogt uw factuur op basis van de volgende formule: [(replica's x partities x-tarief)](#search-units), waarbij het tarief dat u in rekening brengt, afhankelijk is van de prijs categorie die u selecteert.

Wanneer u de kosten van een zoek oplossing wilt schatten, houd er dan rekening mee dat de prijzen en capaciteit niet lineair zijn. (De capaciteit verdubbelt meer dan de kosten.) Zie [replica's en partities toewijzen](search-capacity-planning.md#how-to-allocate-replicas-and-partitions)voor een voor beeld van de werking van de formule.

### <a name="bandwidth-charges"></a>Bandbreedte kosten

Het gebruik van [Azure Cognitive Search Indexeer functies](search-indexer-overview.md) kan van invloed zijn op facturering, afhankelijk van de locatie van uw services. U kunt de kosten voor het uitvallen van gegevens volledig elimineren als u de Azure Cognitive Search-service in dezelfde regio maakt als uw gegevens. Hier vindt u informatie op de [pagina met bandbreedte prijzen](https://azure.microsoft.com/pricing/details/bandwidth/):

+ Micro soft brengt geen kosten in rekening voor alle inkomende gegevens van een service op Azure, of voor uitgaande gegevens van Azure Cognitive Search.
+ In oplossingen voor meer dan één service worden er geen kosten in rekening gebracht voor gegevens die de bedrading overschrijden wanneer alle services zich in dezelfde regio bevinden.

Kosten zijn van toepassing op uitgaande gegevens als de services zich in verschillende regio's bevinden. Deze kosten maken geen deel uit van uw Azure Cognitive Search-factuur. Deze worden hier vermeld, want als u gebruikmaakt van gegevens of AI-verrijkte Indexeer functies om gegevens uit verschillende regio's te halen, worden de kosten weer gegeven in uw algemene factuur.

### <a name="ai-enrichment-with-cognitive-services"></a>AI-verrijking met Cognitive Services

Voor [AI-verrijking](cognitive-search-concept-intro.md)moet u plannen om [een factureer bare Azure Cognitive Services-resource te koppelen](cognitive-search-attach-cognitive-services.md), in dezelfde regio als Azure Cognitive Search, op de prijs categorie S0 voor betalen naar gebruik. Er zijn geen vaste kosten verbonden aan het koppelen van Cognitive Services. U betaalt alleen voor de verwerking die u nodig hebt.

| Bewerking | Facturerings impact |
|-----------|----------------|
| Document kraken, tekst extractie | Gratis |
| Document kraken, afbeeldings extractie | Gefactureerd op basis van het aantal afbeeldingen dat is geëxtraheerd uit uw documenten. In een [Indexeer functie](https://docs.microsoft.com/rest/api/searchservice/create-indexer#indexer-parameters) **imageAction** is de para meter die het ophalen van de installatie kopie activeert. Als **imageAction** is ingesteld op ' geen ' (de standaard instelling), worden er geen kosten in rekening gebracht voor het ophalen van afbeeldingen. De frequentie voor het extra heren van afbeeldingen wordt beschreven op de pagina [prijs informatie](https://azure.microsoft.com/pricing/details/search/) voor Azure Cognitive Search.|
| [Ingebouwde cognitieve vaardig heden](cognitive-search-predefined-skills.md) | Gefactureerd tegen hetzelfde aantal als dat u de taak met Cognitive Services rechtstreeks hebt uitgevoerd. |
| Aangepaste vaardigheden | Een aangepaste vaardigheid is de functionaliteit die u opgeeft. De kosten voor het gebruik van een aangepaste vaardigheid zijn geheel afhankelijk van of aangepaste code andere services met data limieten aanroept. |

<a name="search-units"></a>

## <a name="billing-formula-r-x-p--su"></a>Facturerings formule (R x P = SU)

Het belangrijkste facturerings concept dat u moet begrijpen voor Azure Cognitive Search bewerkingen is de *Zoek eenheid* (su). Omdat Azure Cognitive Search afhankelijk is van replica's en partities voor indexering en query's, is het niet logisch dat u slechts een factuur hoeft te maken van één van beide. In plaats daarvan wordt de facturering gebaseerd op een combi natie van beide.

SU is het product van de *replica's* en *partities* die worden gebruikt door een service: **(R x P = su)** .

Elke service begint met één SU (één replica vermenigvuldigd met één partitie) als mini maal. Het maximum voor elke service is 36 SUs. Dit maximum kan op verschillende manieren worden bereikt: 6 partities x 6 replica's of drie partities x 12 replica's, bijvoorbeeld. Het is gebruikelijk om minder dan de totale capaciteit te gebruiken (bijvoorbeeld een service met drie replica's, 3 partities die is gefactureerd als negen SUs). Zie de grafiek [partitie-en replica combinaties](search-capacity-planning.md#chart) voor geldige combi Naties.

Het facturerings tarief is elk uur per SU. Elke laag heeft een geleidelijk hogere frequentie. Hogere lagen worden geleverd met grotere en snellere partities. Dit draagt bij aan een totaal hoger uurtarief voor die laag. U kunt de tarieven voor elke laag bekijken op de pagina [prijs informatie](https://azure.microsoft.com/pricing/details/search/) .

De meeste klanten brengen slechts een deel van de totale capaciteit online, waarbij de rest in de reserve wordt gehouden. Het aantal partities en replica's dat u online brengt, berekend met behulp van de SU-formule, bepaalt wat u per uur betaalt.

## <a name="how-to-manage-and-reduce-costs"></a>Kosten beheren en verminderen

Naast de volgende suggesties gaat u naar [Facturering en kosten beheer](https://docs.microsoft.com/azure/billing/billing-getting-started).

- Maak alle resources in dezelfde regio, of in zo weinig mogelijk regio's, om bandbreedte kosten te minimaliseren of te elimineren.

- Consolideer alle services in één resource groep, zoals Azure Cognitive Search, Cognitive Services en andere Azure-Services die worden gebruikt in uw oplossing. Zoek in de Azure Portal de resource groep en gebruik de **Cost Management** opdrachten om inzicht te krijgen in de werkelijke en verwachte uitgaven.

- Denk eens aan de Azure-web-app voor uw front-end-toepassing, zodat aanvragen en antwoorden binnen de grenzen van het Data Center blijven.

- Schaal omhoog voor resource-intensieve bewerkingen, zoals indexeren, en pas vervolgens de voor normale query werkbelastingen omlaag aan. Begin met de minimale configuratie voor Azure Cognitive Search (een SU die bestaat uit één partitie en één replica) en controleer vervolgens de gebruikers activiteiten om gebruiks patronen te identificeren die aangeven dat er meer capaciteit nodig is. Als er sprake is van een voorspelbaar patroon, kunt u de schaal aanpassen met de activiteit (u moet code schrijven om dit te automatiseren).

U kunt een zoek service niet afsluiten om uw factuur te verminderen. Toegewezen resources zijn altijd operationeel en worden toegewezen voor de levens duur van uw service. In termen van de service zelf is de enige manier om uw factuur te verlagen, het beperken van replica's en partities tot een niveau dat nog acceptabele prestaties en [Sla-naleving](https://azure.microsoft.com/support/legal/sla/search/v1_0/)biedt, of het maken van een service in een lagere laag (uurtarieven per uur zijn lager dan S2 of S3-tarieven). Als u uw service aan het lagere einde van de belasting prognoses hebt ingericht, kunt u, als u de service uitbreidt, een tweede service met meer lagen maken, uw indexen opnieuw bouwen op de tweede service en vervolgens de eerste verwijderen.

## <a name="how-to-evaluate-capacity-requirements"></a>Capaciteits vereisten evalueren

In azure Cognitive Search is de capaciteit gestructureerd als *replica's* en *partities*.

+ Replica's zijn exemplaren van de zoek service. Elke replica fungeert als host voor één taak verdeling van een index. Bijvoorbeeld: een service met zes replica's heeft zes kopieën van elke index die in de service wordt geladen.

+ Partities slaan indexen op en splitsen automatisch Doorzoek bare gegevens. Twee partities splitsen uw index in tweeën, drie partities splitsen deze in derde, enzovoort. Voor de capaciteit is de *partitie grootte* de primaire onderscheidings functie tussen lagen.

> [!NOTE]
> Alle standaard-en opslag geoptimaliseerde lagen ondersteunen [flexibele combi Naties van replica's en partities](search-capacity-planning.md#chart) , zodat u [uw systeem kunt optimaliseren voor snelheid of opslag](search-performance-optimization.md) door het saldo te wijzigen. De laag basis biedt Maxi maal drie replica's voor hoge Beschik baarheid, maar heeft slechts één partitie. Gratis lagen bieden geen specifieke resources: computer bronnen worden gedeeld door meerdere abonnees.

<!-- ## Consumption patterns

On the low and high ends, Basic and S3 HD are for important but atypical consumption patterns. Basic is for small production workloads. It offers SLAs, dedicated resources, and high availability, but it provides modest storage, topping out at 2 GB total. This tier was engineered for customers that consistently underutilize available capacity. At the high end, S3 HD is for workloads typical of ISVs, partners, [multitenant solutions](search-modeling-multitenant-saas-applications.md), or any configuration that calls for a large number of small indexes. It's often clear when Basic or S3 HD is the right tier. If you want confirmation, you can post to [StackOverflow](https://stackoverflow.com/questions/tagged/azure-search) or [contact Azure support](https://azure.microsoft.com/support/options/) for guidance.

The more commonly used standard tiers, S1 through S3, make up a progression of increasing levels of capacity. There are inflection points on partition size and limits on numbers of indexes, indexers, and corollary resources:

|  | S1 | S2 | S3 |  |  |  |  |
|--|----|----|----|--|--|--|--|
| Partition size|  25 GB | 100 GB | 200 GB |  |  |  |  |
| Index and indexer limits| 50 | 200 | 200 |  |  |  |  |

S1 is a common choice for customers that need dedicated resources and multiple partitions. S1 offers partitions of 25 GB and up to 12 partitions, providing a per-service limit of 300 GB if you maximize partitions over replicas. (See [Allocate partitions and replicas](search-capacity-planning.md#chart) for more balanced allocations.)

The portal and pricing pages put the focus on partition size and storage, but, for each tier, all compute capabilities (disk capacity, speed, CPUs) generally increase linearly with price. An S2 replica is faster than S1, and S3 is faster than S2. S3 tiers break from the linear compute-pricing pattern with disproportionately faster I/O. If you expect I/O to be the bottleneck, keep in mind that you can get much more IOPS with S3 than you can get with lower tiers.

S3 and S3 HD are backed by identical high-capacity infrastructure, but they reach their maximum limits in different ways. S3 targets a smaller number of very large indexes, so its maximum limit is resource-bound (2.4 TB for each service). S3 HD targets a large number of very small indexes. At 1,000 indexes, S3 HD reaches its limits in the form of index constraints. If you're an S3 HD customer and you need more than 1,000 indexes, contact Microsoft Support for information about how to proceed.

> [!NOTE]
> Document limits were a consideration at one time, but they're no longer applicable for new services. For information about conditions in which document limits still apply, see [Document limits](search-limits-quotas-capacity.md#document-limits).
>

Storage Optimized tiers, L1 and L2, are ideal for applications with large data requirements but a relatively low number of end users, when minimizing query latency isn't the top priority.  

|  | L1 | L2 |  |  |  |  |  |
|--|----|----|--|--|--|--|--|
| Partition size|  1 TB | 2 TB |  |  |  |  |  |
| Index and indexer limits| 10 | 10 |  |  |  |  |  |

L2 offers twice the overall storage capacity of L1.  Choose your tier based on the maximum amount of data that you think your index needs. The L1 tier partitions scale up in 1-TB increments to a maximum of 12 TB. The L2 partitions increase by 2 TBs per partition up to a maximum of 24 TB. -->

### <a name="evaluating-capacity"></a>De capaciteit evalueren

De capaciteit en de kosten voor het uitvoeren van de service hand matig. De lagen leggen limieten op twee niveaus vast: opslag en bronnen. U moet nadenken over beide omdat de limiet die u eerst bereikt de daad werkelijke limiet is.

Bedrijfs vereisten bepalen doorgaans het aantal indexen dat u nodig hebt. U hebt bijvoorbeeld een algemene index nodig voor een grote opslag ruimte van documenten. Of u hebt meerdere indexen nodig op basis van de regio-, toepassings-of Business-niche.

Als u de grootte van een index wilt bepalen, moet u er [een maken](search-create-index-portal.md). De gegevens structuur in azure Cognitive Search is hoofd zakelijk een [omgekeerde index](https://en.wikipedia.org/wiki/Inverted_index) structuur, die andere kenmerken heeft dan de bron gegevens. Voor een omgekeerde index worden de grootte en complexiteit bepaald door de inhoud, niet noodzakelijkerwijs door de hoeveelheid gegevens die u in de feed invoert. Een grote gegevens bron met hoge redundantie kan resulteren in een kleinere index dan een kleinere gegevensset die zeer variabele inhoud bevat. Het is dus zelden mogelijk de index grootte af te leiden op basis van de grootte van de oorspronkelijke gegevensset.

> [!NOTE] 
> Hoewel de toekomstige behoeften voor indices en opslag in de toekomst kunnen worden geschat, is het een goed idee om te doen. Als de capaciteit van een laag te laag wordt, moet u een nieuwe service inrichten op een hogere laag en vervolgens [uw indexen opnieuw laden](search-howto-reindex.md). Er is geen in-place upgrade van een service van de ene SKU naar een andere.
>

### <a name="estimate-with-the-free-tier"></a>Een schatting maken van de gratis laag

Een benadering voor het schatten van capaciteit is om te beginnen met de laag gratis. Houd er rekening mee dat de gratis service Maxi maal drie indexen, 50 MB aan opslag ruimte en twee minuten aan indexerings tijd biedt. Het kan lastig zijn om een schatting te maken van de verwachte index grootte met deze beperkingen, maar dit zijn de stappen:

+ [Maak een gratis service](search-create-service-portal.md).
+ Bereid een kleine, representatieve gegevensset voor.
+ [Bouw een initiële index in de portal](search-create-index-portal.md) en noteer de grootte ervan. Functies en kenmerken hebben invloed op de opslag. Als u bijvoorbeeld Voorst Ellen toevoegt (typeahead), worden de opslag vereisten verhoogd. Met dezelfde gegevensset kunt u proberen meerdere versies van een index te maken met verschillende kenmerken voor elk veld om te zien hoe opslag vereisten variëren. Zie [' implicaties voor opslag ' in Create a Basic index](search-what-is-an-index.md#storage-implications)(Engelstalig) voor meer informatie.

Met een ruwe schatting kunt u de hoeveelheid die u wilt budget teren voor twee indexen (ontwikkeling en productie) en vervolgens uw laag vervolgens kiezen.

### <a name="estimate-with-a-billable-tier"></a>Ramen met een factureer bare laag

Toegewezen resources kunnen grotere sampling-en verwerkings tijden bieden voor realistischere schattingen van de index hoeveelheid, grootte en query volumes tijdens de ontwikkeling. Sommige klanten springen direct in met een factureer bare laag en evalueren vervolgens opnieuw als het ontwikkelings project verouderd is.

1. [Bekijk de service limieten per laag](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity#index-limits) om te bepalen of lagere lagen het aantal indexen kunnen ondersteunen dat u nodig hebt. In de lagen Basic, S1 en S2 zijn de index limieten respectievelijk 15, 50 en 200. De laag geoptimaliseerd voor opslag heeft een limiet van 10 indexen, omdat deze is ontworpen voor het ondersteunen van een laag aantal zeer grote indexen.

1. [Een service op een factureer bare laag maken](search-create-service-portal.md):

    + Start laag, op basis of S1, als u niet zeker weet wat de geprojecteerde belasting is.
    + Start hoog, op S2 of zelfs S3, als u weet dat u grote hoeveel heden indexen en query's kunt laden.
    + Begin met opslag die is geoptimaliseerd, bij L1 of L2, als u een grote hoeveelheid gegevens wilt indexeren en de belasting van query's relatief laag is, net als bij een interne zakelijke toepassing.

1. [Bouw een initiële index](search-create-index-portal.md) om te bepalen hoe bron gegevens worden omgezet in een index. Dit is de enige manier om de index grootte te schatten.

1. [Bewaak opslag, service limieten, query volume en latentie](search-monitor-usage.md) in de portal. In de portal worden query's per seconde, vertraagde query's en zoek latentie weer gegeven. Al deze waarden kunnen u helpen bij het bepalen of u de juiste laag hebt geselecteerd. 

Het index nummer en de grootte zijn even belang rijk voor uw analyse. Dit komt doordat de maximum limieten worden bereikt door het volledige gebruik van opslag (partities) of door maximale limieten voor resources (indexen, Indexeer functies enzovoort), afhankelijk van wat het eerste komt. De portal helpt u bij te houden, met het huidige gebruik en maximum aantal limieten naast elkaar op de pagina overzicht.

> [!NOTE]
> Opslag vereisten kunnen worden verkleind als documenten overbodige gegevens bevatten. In het ideale geval bevatten documenten alleen de gegevens die u nodig hebt voor de zoek ervaring. Binaire gegevens worden niet doorzocht en moeten afzonderlijk worden opgeslagen (wellicht in een Azure-tabel of Blob-opslag). Er moet een veld in de index worden toegevoegd om een URL-verwijzing naar de externe gegevens te bevatten. De maximale grootte van een afzonderlijk document is 16 MB (of minder als u meerdere documenten tegelijk uploadt in één aanvraag). Zie [service limieten in Azure Cognitive Search](search-limits-quotas-capacity.md)voor meer informatie.
>

**Overwegingen bij het opvragen van volumes**

Query's per seconde (QPS) is een belang rijke metrische gegevens tijdens het afstemmen van de prestaties, maar het is doorgaans slechts een laag overweging als u een hoog query volume verwacht.

De Standard-lagen kunnen een evenwicht hebben tussen replica's en partities. U kunt de query-oplever bewerking verhogen door replica's voor taak verdeling toe te voegen of partities toe te voegen voor parallelle verwerking. U kunt vervolgens de prestaties afstemmen nadat de service is ingericht.

Als u veel gewoon query volumes van het begin verwacht, moet u rekening houden met hogere standaard lagen, ondersteund door krachtigere hardware. U kunt vervolgens partities en replica's offline halen of zelfs overschakelen naar een service met een lagere laag als deze query volumes niet worden uitgevoerd. Zie [Azure Cognitive Search prestaties en optimalisatie](search-performance-optimization.md)voor meer informatie over het berekenen van de door Voer van query's.

De geoptimaliseerde opslag lagen zijn handig voor grote gegevens workloads, zodat er meer algemene beschik bare index opslag wordt ondersteund voor wanneer de vereisten voor query latentie minder belang rijk zijn. U moet nog steeds extra replica's gebruiken voor taak verdeling en aanvullende partities voor parallelle verwerking. U kunt vervolgens de prestaties afstemmen nadat de service is ingericht.

**Service Level-overeenkomsten**

De functies gratis en preview bieden geen [Service Level Agreements (sla's)](https://azure.microsoft.com/support/legal/sla/search/v1_0/). Voor alle factureer bare lagen gelden de Sla's wanneer u voldoende redundantie voor uw service inricht. U moet twee of meer replica's hebben voor de opdracht query (lezen). U moet drie of meer replica's hebben voor het uitvoeren van query's en indexen (lezen/schrijven). Het aantal partities heeft geen invloed op Sla's.

## <a name="tips-for-tier-evaluation"></a>Tips voor het evalueren van lagen

+ Sta toe dat metrische query's kunnen worden gebruikt voor het samen stellen en verzamelen van gegevens rondom gebruiks patronen (query's tijdens kantoor uren, indexeren tijdens daluren). Gebruik deze gegevens om beslissingen over service-inrichtingen te informeren. Hoewel het niet praktisch is om een uur-of dagelijks uitgebracht, kunt u partities en resources dynamisch aanpassen om geplande wijzigingen in query volumes aan te passen. U kunt ook ongeplande wijzigingen aanbrengen als de niveaus lang genoeg zijn om te garanderen dat er actie wordt ondernomen.

+ Houd er rekening mee dat het enige nadeel van onderinrichting is dat u een service mogelijk moet afbreken als de werkelijke vereisten groter zijn dan uw voor spellingen. Om service onderbrekingen te voor komen, maakt u een nieuwe service in hetzelfde abonnement op een hogere laag en voert u deze naast elkaar uit totdat alle apps en aanvragen gericht zijn op het nieuwe eind punt.

## <a name="next-steps"></a>Volgende stappen

Begin met een gratis laag en bouw een initiële index met behulp van een subset van uw gegevens om de kenmerken ervan te begrijpen. De gegevens structuur in azure Cognitive Search is een omgekeerde index structuur. De grootte en complexiteit van een omgekeerde index worden bepaald door de inhoud. Houd er rekening mee dat uiterst redundante inhoud een kleinere index zou kunnen opleveren dan zeer onregelmatige inhoud. Daarom bepalen inhouds kenmerken in plaats van de grootte van de gegevensset vereisten voor de opslag van de index.

Nadat u een eerste schatting hebt gemaakt van uw index grootte, moet u [een factureer bare service inrichten](search-create-service-portal.md) op een van de lagen die in dit artikel worden besproken: Basic, Standard of Storage Optimized. Verlaag eventuele kunst matige beperkingen voor het wijzigen van de gegevens en [bouw uw index opnieuw](search-howto-reindex.md) op om alle gegevens te bevatten die u wilt doorzoeken.

[Wijs partities en replica's](search-capacity-planning.md) zo nodig toe om de prestaties en schaal die u nodig hebt te verkrijgen.

Als de prestaties en capaciteit nauw keurig zijn, bent u klaar. Anders moet u een zoek service opnieuw maken op een andere laag die nauw keuriger is afgestemd op uw behoeften.

> [!NOTE]
> Als u vragen hebt, plaatst u een bericht op [stack overflow](https://stackoverflow.com/questions/tagged/azure-search) of [neemt u contact op met de ondersteuning van Azure](https://azure.microsoft.com/support/options/).
