---
title: 'Azure Cosmos DB-ontwerppatroon: apps voor sociale media'
description: Meer informatie over een ontwerppatroon voor sociale netwerken door gebruik te maken van de opslagflexibiliteit van Azure Cosmos DB en andere Azure-services.
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: maquaran
ms.openlocfilehash: 8428e417f5f86edca77edae6ca4b7ef84e5ff425
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73827300"
---
# <a name="going-social-with-azure-cosmos-db"></a>Sociaal worden met Azure Cosmos DB

Leven in een massaal met elkaar verbonden samenleving betekent dat, op een bepaald punt in het leven, je deel wordt van een **sociaal netwerk**. Je gebruikt sociale netwerken om in contact te blijven met vrienden, collega's, familie, of soms om je passie te delen met mensen met gemeenschappelijke interesses.

Als ingenieurs of ontwikkelaars hebt u zich misschien afgevraagd hoe deze netwerken uw gegevens opslaan en met elkaar verbinden. Of je zou zelfs de opdracht hebben gekregen om een nieuw sociaal netwerk te creëren of te ontwerpen voor een specifieke nichemarkt. Dat is wanneer de belangrijke vraag rijst: Hoe wordt al deze gegevens opgeslagen?

Stel dat u een nieuw en glanzend sociaal netwerk maakt waar uw gebruikers artikelen kunnen plaatsen met gerelateerde media, zoals foto's, video's of zelfs muziek. Gebruikers kunnen reageren op berichten en punten geven voor beoordelingen. Er zal een feed van berichten die gebruikers zullen zien en interactie met op de belangrijkste website bestemmingspagina. Deze methode klinkt niet complex op het eerste, maar omwille van de eenvoud, laten we daar stoppen. (U zich verdiepen in aangepaste gebruikersfeeds die worden beïnvloed door relaties, maar het gaat verder dan het doel van dit artikel.)

Dus, hoe sla je deze gegevens op en waar?

U hebt mogelijk ervaring met SQL-databases of hebt een notie van [relationele modellering van gegevens.](https://en.wikipedia.org/wiki/Relational_model) U beginnen met het tekenen van iets als volgt:

![Diagram dat een relatief relationeel model illustreert](./media/social-media-apps/social-media-apps-sql.png)

Een perfect genormaliseerde en mooie datastructuur... dat schaalt niet op.

Begrijp me niet verkeerd, ik heb mijn hele leven met SQL-databases gewerkt. Ze zijn geweldig, maar zoals elk patroon, praktijk en softwareplatform, is het niet perfect voor elk scenario.

Waarom is SQL niet de beste keuze in dit scenario? Laten we eens kijken naar de structuur van een enkele post. Als ik de post in een website of toepassing wilde tonen, zou ik een vraag met... moeten doen door deel te nemen aan acht tafels(!) gewoon om een enkele post te tonen. Stel je nu een stroom van berichten voor die dynamisch laden en op het scherm verschijnen, en je zou kunnen zien waar ik naartoe ga.

U een enorme SQL-instantie gebruiken met voldoende kracht om duizenden query's op te lossen met veel joins om uw inhoud te dienen. Maar waarom zou je, als er een eenvoudigere oplossing bestaat?

## <a name="the-nosql-road"></a>De NoSQL-weg

In dit artikel u de gegevens van uw sociale platform op kosteneffectieve wijze modelleren met Azure's NoSQL-database [Azure Cosmos DB.](https://azure.microsoft.com/services/cosmos-db/) Het vertelt u ook hoe u andere Azure Cosmos DB-functies gebruiken, zoals de [Gremlin API.](../cosmos-db/graph-introduction.md) Met behulp van een [NoSQL-benadering,](https://en.wikipedia.org/wiki/NoSQL) het opslaan van gegevens, in JSON-formaat en het toepassen van [denormalisatie,](https://en.wikipedia.org/wiki/Denormalization)kan de eerder gecompliceerde post worden omgezet in één [document:](https://en.wikipedia.org/wiki/Document-oriented_database)

    {
        "id":"ew12-res2-234e-544f",
        "title":"post title",
        "date":"2016-01-01",
        "body":"this is an awesome post stored on NoSQL",
        "createdBy":User,
        "images":["https://myfirstimage.png","https://mysecondimage.png"],
        "videos":[
            {"url":"https://myfirstvideo.mp4", "title":"The first video"},
            {"url":"https://mysecondvideo.mp4", "title":"The second video"}
        ],
        "audios":[
            {"url":"https://myfirstaudio.mp3", "title":"The first audio"},
            {"url":"https://mysecondaudio.mp3", "title":"The second audio"}
        ]
    }

En het kan worden gekregen met een enkele query, en zonder joins. Deze query is veel eenvoudig en eenvoudig, en, qua budget, het vereist minder middelen om een beter resultaat te bereiken.

Azure Cosmos DB zorgt ervoor dat alle eigenschappen worden geïndexeerd met de automatische indexering. De automatische indexering kan zelfs worden [aangepast.](index-policy.md) Met de schemavrije aanpak kunnen we documenten opslaan met verschillende en dynamische structuren. Misschien wil je morgen dat berichten een lijst met categorieën of hashtags bevatten die eraan zijn gekoppeld? Cosmos DB zal de nieuwe documenten behandelen met de toegevoegde attributen zonder extra werk dat door ons vereist is.

Reacties op een bericht kunnen worden behandeld als andere berichten met een bovenliggende eigenschap. (Deze praktijk vereenvoudigt het toewijzen van objecten.)

    {
        "id":"1234-asd3-54ts-199a",
        "title":"Awesome post!",
        "date":"2016-01-02",
        "createdBy":User2,
        "parent":"ew12-res2-234e-544f"
    }

    {
        "id":"asd2-fee4-23gc-jh67",
        "title":"Ditto!",
        "date":"2016-01-03",
        "createdBy":User3,
        "parent":"ew12-res2-234e-544f"
    }

En alle sociale interacties kunnen worden opgeslagen op een apart object als tellers:

    {
        "id":"dfe3-thf5-232s-dse4",
        "post":"ew12-res2-234e-544f",
        "comments":2,
        "likes":10,
        "points":200
    }

Het maken van feeds is slechts een kwestie van het maken van documenten die een lijst met post-geïdentificeerde-identiteitsbewijzen met een bepaalde relevantievolgorde kunnen bevatten:

    [
        {"relevance":9, "post":"ew12-res2-234e-544f"},
        {"relevance":8, "post":"fer7-mnb6-fgh9-2344"},
        {"relevance":7, "post":"w34r-qeg6-ref6-8565"}
    ]

Je zou een "laatste" stream kunnen hebben met berichten die zijn geordend op de oprichtingsdatum. Of je zou kunnen hebben een "heetste" stream met die berichten met meer houdt in de afgelopen 24 uur. U zelfs een aangepaste stream voor elke gebruiker implementeren op basis van logica zoals volgers en interesses. Het zou nog steeds een lijst van berichten. Het is een kwestie van hoe deze lijsten te bouwen, maar de leesprestaties blijft ongehinderd. Zodra u een van deze lijsten hebt aangeschaft, geeft u één query uit aan Cosmos DB met behulp van het [IN-trefwoord](sql-query-keywords.md#in) om pagina's met berichten tegelijk te krijgen.

De feedstreams kunnen worden gebouwd met behulp van de achtergrondprocessen van [Azure App Services:](https://azure.microsoft.com/services/app-service/) [Webjobs](../app-service/webjobs-create.md). Zodra een bericht is gemaakt, kan achtergrondverwerking worden geactiveerd door [Azure Storage](https://azure.microsoft.com/services/storage/) [Queues](../storage/queues/storage-dotnet-how-to-use-queues.md) en Webjobs te gebruiken die worden geactiveerd met behulp van de [Azure Webjobs SDK,](https://github.com/Azure/azure-webjobs-sdk/wiki)waarbij de napropagatie binnen streams worden geïmplementeerd op basis van uw eigen aangepaste logica.

Punten en vind-ik-leuks over een bericht kunnen op een uitgestelde manier worden verwerkt met behulp van dezelfde techniek om een uiteindelijk consistente omgeving te creëren.

Volgers zijn lastiger. Cosmos DB heeft een limiet voor de documentgrootte en het lezen/schrijven van grote documenten kan van invloed zijn op de schaalbaarheid van uw toepassing. Dus je zou kunnen denken over het opslaan van volgelingen als een document met deze structuur:

    {
        "id":"234d-sd23-rrf2-552d",
        "followersOf": "dse4-qwe2-ert4-aad2",
        "followers":[
            "ewr5-232d-tyrg-iuo2",
            "qejh-2345-sdf1-ytg5",
            //...
            "uie0-4tyg-3456-rwjh"
        ]
    }

Deze structuur kan werken voor een gebruiker met een paar duizend volgers. Als sommige beroemdheden zich bij de gelederen voegt, zal deze aanpak echter leiden tot een grote documentgrootte en kan het uiteindelijk de limiet voor documentgrootte raken.

Om dit probleem op te lossen, u een gemengde aanpak gebruiken. Als onderdeel van het document Gebruikersstatistieken u het aantal volgers opslaan:

    {
        "id":"234d-sd23-rrf2-552d",
        "user": "dse4-qwe2-ert4-aad2",
        "followers":55230,
        "totalPosts":452,
        "totalPoints":11342
    }

U de werkelijke grafiek met volgers opslaan met Azure Cosmos DB [Gremlin API](../cosmos-db/graph-introduction.md) om [hoekpunten](http://mathworld.wolfram.com/GraphVertex.html) te maken voor elke gebruiker en [randen](http://mathworld.wolfram.com/GraphEdge.html) die de relaties A-follows-B onderhouden. Met de Gremlin API u de volgers van een bepaalde gebruiker krijgen en complexere query's maken om mensen met elkaar voor te stellen. Als u aan de grafiek de inhoudscategorieën toevoegt die mensen leuk vinden of leuk vinden, u beginnen met het weven van ervaringen die slimme inhoudsdetectie bevatten, inhoud voorstellen die de mensen die u volgt leuk vinden of mensen vinden waarmee u mogelijk veel gemeen hebt.

Het document Gebruikersstatistieken kan nog steeds worden gebruikt om kaarten te maken in de gebruikersinterface of snelle profielvoorbeelden.

## <a name="the-ladder-pattern-and-data-duplication"></a>Het "Ladder"-patroon en duplicatie van gegevens

Zoals u misschien hebt gemerkt in het JSON-document dat verwijst naar een bericht, zijn er veel exemplaren van een gebruiker. En je zou goed geraden hebben, deze duplicaten betekenen dat de informatie die een gebruiker beschrijft, gezien deze denormalisatie, op meer dan één plaats aanwezig kan zijn.

Als u snellere query's wilt aanvragen, loopt u tot dubbele gegevens. Het probleem met deze bijwerking is dat als door sommige actie, de gegevens van een gebruiker verandert, u alle activiteiten moet vinden die de gebruiker ooit heeft gedaan en ze allemaal moet bijwerken. Klinkt niet praktisch, toch?

U lost het op door de belangrijkste kenmerken van een gebruiker te identificeren die u voor elke activiteit in uw toepassing weerlaat. Als u een bericht visueel weergeeft in uw toepassing en alleen de naam en afbeelding van de maker weergeeft, waarom slaat u dan alle gegevens van de gebruiker op in het kenmerk 'createdBy'? Als u voor elke opmerking gewoon de foto van de gebruiker laat zien, hebt u niet echt de rest van de informatie van de gebruiker nodig. Dat is waar iets wat ik noem de "Ladder patroon" betrokken raakt.

Neem gebruikersinformatie als voorbeeld:

    {
        "id":"dse4-qwe2-ert4-aad2",
        "name":"John",
        "surname":"Doe",
        "address":"742 Evergreen Terrace",
        "birthday":"1983-05-07",
        "email":"john@doe.com",
        "twitterHandle":"\@john",
        "username":"johndoe",
        "password":"some_encrypted_phrase",
        "totalPoints":100,
        "totalPosts":24
    }

Door te kijken naar deze informatie, u snel detecteren welke is kritische informatie en welke niet, waardoor een Ladder:

![Diagram van een ladderpatroon](./media/social-media-apps/social-media-apps-ladder.png)

De kleinste stap wordt een UserChunk genoemd, het minimale stukje informatie dat een gebruiker identificeert en het wordt gebruikt voor het dupliceren van gegevens. Door de dubbele gegevensgrootte te verkleinen tot alleen de informatie die u zult "weergeven", vermindert u de kans op massale updates.

De middelste stap wordt de gebruiker genoemd. Het zijn de volledige gegevens die worden gebruikt op de meeste prestatieafhankelijke query's op Cosmos DB, de meest toegankelijke en kritieke. Het bevat de informatie vertegenwoordigd door een UserChunk.

De grootste is de Extended User. Het bevat de kritieke gebruikersinformatie en andere gegevens die niet snel hoeven te worden gelezen of uiteindelijk worden gebruikt, zoals het aanmeldingsproces. Deze gegevens kunnen worden opgeslagen buiten Cosmos DB, in Azure SQL Database of Azure Storage Tables.

Waarom zou je de gebruiker splitsen en zelfs deze informatie op verschillende plaatsen opslaan? Want vanuit het oogpunt van de prestaties, hoe groter de documenten, hoe duurder de query's. Houd documenten slank, met de juiste informatie om al uw prestatieafhankelijke query's voor uw sociale netwerk uit te brengen. Sla de andere extra informatie op voor eventuele scenario's zoals volledige profielbewerkingen, aanmeldingen en datamining voor gebruiksanalyses en Big Data-initiatieven. Het kan u echt niet schelen of het verzamelen van gegevens voor datamining trager verloopt, omdat deze wordt uitgevoerd op Azure SQL Database. U heeft wel zorgen dat uw gebruikers een snelle en slanke ervaring hebben. Een gebruiker die is opgeslagen op Cosmos DB zou er uitzien als deze code:

    {
        "id":"dse4-qwe2-ert4-aad2",
        "name":"John",
        "surname":"Doe",
        "username":"johndoe"
        "email":"john@doe.com",
        "twitterHandle":"\@john"
    }

En een post zou eruit zien als:

    {
        "id":"1234-asd3-54ts-199a",
        "title":"Awesome post!",
        "date":"2016-01-02",
        "createdBy":{
            "id":"dse4-qwe2-ert4-aad2",
            "username":"johndoe"
        }
    }

Wanneer een bewerking ontstaat wanneer een chunk-kenmerk wordt beïnvloed, u de betreffende documenten gemakkelijk vinden. Gebruik alleen query's die naar de geïndexeerde kenmerken wijzen, zoals `SELECT * FROM posts p WHERE p.createdBy.id == "edited_user_id"`, en werk de segmenten vervolgens bij.

## <a name="the-search-box"></a>Het zoekvak

Gebruikers zullen, gelukkig, veel inhoud genereren. En je moet in staat zijn om de mogelijkheid om te zoeken en te vinden inhoud die misschien niet direct in hun inhoud streams, misschien omdat je niet volgen van de makers, of misschien ben je gewoon proberen te vinden dat oude bericht je deed zes maanden geleden.

Omdat u Azure Cosmos DB gebruikt, u een zoekmachine eenvoudig implementeren met [Azure Cognitive Search](https://azure.microsoft.com/services/search/) in een paar minuten zonder code te typen, met andere code dan het zoekproces en de gebruikersinterface.

Waarom is dit proces zo eenvoudig?

Azure Cognitive Search implementeert wat ze [indexers](https://msdn.microsoft.com/library/azure/dn946891.aspx)noemen, achtergrondprocessen die in uw gegevensopslagplaatsen worden aangesloten en automatisch uw objecten in de indexen toevoegen, bijwerken of verwijderen. Ze ondersteunen een [Azure SQL Database-indexeerders,](https://blogs.msdn.microsoft.com/kaevans/2015/03/06/indexing-azure-sql-database-with-azure-search/) [Azure Blobs-indexers](../search/search-howto-indexing-azure-blob-storage.md) en gelukkig [Azure Cosmos DB-indexers](../search/search-howto-index-documentdb.md). De overgang van informatie van Cosmos DB naar Azure Cognitive Search is eenvoudig. Beide technologieën slaan informatie op in JSON-indeling, dus u hoeft alleen maar [uw index](../search/search-create-index-portal.md) te maken en de kenmerken van uw documenten die u wilt indexeren in kaart te brengen. Dat is alles. Afhankelijk van de grootte van uw gegevens, zal al uw inhoud beschikbaar zijn om binnen enkele minuten te worden doorzocht door de beste Search-as-a-Service-oplossing in de cloudinfrastructuur.

Voor meer informatie over Azure Cognitive Search u terecht op de [Hitchhiker's Guide to Search.](https://blogs.msdn.microsoft.com/mvpawardprogram/2016/02/02/a-hitchhikers-guide-to-search/)

## <a name="the-underlying-knowledge"></a>De onderliggende kennis

Na het opslaan van al deze inhoud die groeit en groeit elke dag, zou je kunnen denken: Wat kan ik doen met al deze stroom van informatie van mijn gebruikers?

Het antwoord is eenvoudig: Zet het aan het werk en leer ervan.

Maar wat kun je leren? Een paar eenvoudige voorbeelden zijn [sentimentanalyse,](https://en.wikipedia.org/wiki/Sentiment_analysis)inhoudsaanbevelingen op basis van de voorkeuren van een gebruiker of zelfs een geautomatiseerde inhoudsmoderator die ervoor zorgt dat de inhoud die door uw sociale netwerk wordt gepubliceerd, veilig is voor het gezin.

Nu ik je verslaafd heb, zul je waarschijnlijk denken dat je wat doctoraat in wiskundewetenschap nodig hebt om deze patronen en informatie uit eenvoudige databases en bestanden te halen, maar je zou het mis hebben.

[Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/), onderdeel van de [Cortana Intelligence Suite,](https://social.technet.microsoft.com/wiki/contents/articles/36688.introduction-to-cortana-intelligence-suite.aspx)is een volledig beheerde cloudservice waarmee u workflows maken met behulp van algoritmen in een eenvoudige interface voor slepen en neerzetten, uw eigen algoritmen in [R](https://en.wikipedia.org/wiki/R_\(programming_language\))coderen of een aantal van de reeds gebouwde en gebruiksklare API's gebruiken, zoals: [Text Analytics](https://gallery.cortanaanalytics.com/MachineLearningAPI/Text-Analytics-2), [ContentModerator of [Aanbevelingen](https://gallery.azure.ai/Solution/Recommendations-Solution).

Om een van deze Machine Learning-scenario's te realiseren, u [Azure Data Lake](https://azure.microsoft.com/services/data-lake-store/) gebruiken om de informatie uit verschillende bronnen in te nemen. U [U-SQL](https://azure.microsoft.com/documentation/videos/data-lake-u-sql-query-execution/) ook gebruiken om de informatie te verwerken en een uitvoer te genereren die kan worden verwerkt door Azure Machine Learning.

Een andere beschikbare optie is het gebruik van [Azure Cognitive Services](https://www.microsoft.com/cognitive-services) om de inhoud van uw gebruikers te analyseren. niet alleen u ze beter begrijpen (door te analyseren wat ze schrijven met [Text Analytics API),](https://www.microsoft.com/cognitive-services/en-us/text-analytics-api)maar je ook ongewenste of volwassen inhoud detecteren en dienovereenkomstig handelen met [Computer Vision API.](https://www.microsoft.com/cognitive-services/en-us/computer-vision-api) Cognitive Services bevat veel kant-en-klare oplossingen die geen machine learning-kennis vereisen om te gebruiken.

## <a name="a-planet-scale-social-experience"></a>Een sociale ervaring op planeetschaal

Er is een laatste, maar niet in het minst, belangrijk artikel dat ik moet aanpakken: **schaalbaarheid**. Wanneer u een architectuur ontwerpt, moet elk onderdeel op zichzelf schalen. U zult uiteindelijk meer gegevens moeten verwerken, of u zult een grotere geografische dekking willen hebben. Gelukkig is het bereiken van beide taken een **turnkey ervaring** met Cosmos DB.

Cosmos DB ondersteunt dynamische partitionering out-of-the-box. Het maakt automatisch partities op basis van een bepaalde **partitiesleutel,** die wordt gedefinieerd als een attribuut in uw documenten. Het definiëren van de juiste partitiesleutel moet worden gedaan op het ontwerp tijd. Zie [Partitioneren in Azure Cosmos DB](partitioning-overview.md)voor meer informatie.

Voor een sociale ervaring moet u uw partitioneringsstrategie afstemmen op de manier waarop u query's en schrijf. (Bijvoorbeeld, leest binnen dezelfde partitie wenselijk zijn, en voorkomen dat "hot spots" door het verspreiden van schrijft op meerdere partities.) Enkele opties zijn: partities op basis van een tijdelijke sleutel (dag/maand/week), per inhoudscategorie, per geografische regio of per gebruiker. Het hangt allemaal af van hoe u de gegevens opvraagt en de gegevens in uw sociale ervaring weergeeft.

Cosmos DB voert uw query's (inclusief [aggregaten)](https://azure.microsoft.com/blog/planet-scale-aggregates-with-azure-documentdb/)transparant uit over al uw partities, zodat u geen logica hoeft toe te voegen naarmate uw gegevens groeien.

Na verloop van tijd groeit u uiteindelijk in het verkeer en neemt uw resourceverbruik (gemeten in [R's](request-units.md)of Aanvraageenheden) toe. U zult lezen en schrijven vaker als uw user base groeit. Het gebruikersbestand zal beginnen met het maken en lezen van meer inhoud. Dus het vermogen van het schalen van **uw doorvoer** is van vitaal belang. Het verhogen van uw RU's is eenvoudig. U dit doen met een paar klikken op de Azure-portal of door [opdrachten uit te geven via de API.](https://docs.microsoft.com/rest/api/cosmos-db/replace-an-offer)

![Een partitiesleutel opschalen en definiëren](./media/social-media-apps/social-media-apps-scaling.png)

Wat gebeurt er als het steeds beter wordt? Stel dat gebruikers uit een andere regio, land of continent uw platform opmerken en het gaan gebruiken. Wat een grote verrassing!

Maar wacht eens even! U realiseert zich al snel dat hun ervaring met uw platform niet optimaal is. Ze zijn zo ver weg van uw operationele regio dat de latentie verschrikkelijk is. Je wilt duidelijk niet dat ze stoppen. Was er maar een makkelijke manier om **je wereldwijde bereik uit te breiden?** Die is er wel!

Met Cosmos DB u [uw gegevens wereldwijd](../cosmos-db/tutorial-global-distribution-sql-api.md) en transparant repliceren met een paar klikken en automatisch kiezen uit de beschikbare regio's uit uw [clientcode.](../cosmos-db/tutorial-global-distribution-sql-api.md) Dit proces betekent ook dat u [meerdere failoverregio's](high-availability.md)hebben.

Wanneer u uw gegevens wereldwijd repliceert, moet u ervoor zorgen dat uw klanten er gebruik van kunnen maken. Als u een webfrontend gebruikt of API's van mobiele clients opent, u [Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/) implementeren en uw Azure App Service klonen op alle gewenste regio's, met behulp van een prestatieconfiguratie om uw uitgebreide wereldwijde dekking te ondersteunen. Wanneer uw klanten toegang krijgen tot uw frontend of API's, worden ze doorgestuurd naar de dichtstbijzijnde App-service, die op zijn beurt verbinding maakt met de lokale Cosmos DB-replica.

![Wereldwijde dekking toevoegen aan uw sociale platform](./media/social-media-apps/social-media-apps-global-replicate.png)

## <a name="conclusion"></a>Conclusie

Dit artikel werpt enig licht in de alternatieven van het creëren van sociale netwerken volledig op Azure met low-cost services. het levert resultaten op door het gebruik van een meerlaagse opslagoplossing en gegevensdistributie genaamd "Ladder" aan te moedigen.

![Diagram voor interactie tussen Azure-services voor sociale netwerken](./media/social-media-apps/social-media-apps-azure-solution.png)

De waarheid is dat er geen zilveren kogel is voor dit soort scenario's. Het is de synergie die wordt gecreëerd door de combinatie van geweldige services die ons in staat stellen om geweldige ervaringen op te bouwen: de snelheid en vrijheid van Azure Cosmos DB om een geweldige sociale toepassing te bieden, de intelligentie achter een eersteklas zoekoplossing zoals Azure Cognitive Search, de flexibiliteit van Azure App Services om niet eens taal-agnostische toepassingen te hosten, maar krachtige achtergrondprocessen en de uitbreidbare Azure Storage en Azure SQL Database voor het opslaan van enorme hoeveelheden gegevens en de analytische kracht van Azure Machine Learning kennis en intelligentie creëren die feedback kunnen geven aan uw processen en ons kunnen helpen de juiste inhoud aan de juiste gebruikers te leveren.

## <a name="next-steps"></a>Volgende stappen

Zie [Common Cosmos DB-use cases](use-cases.md)voor meer informatie over use cases voor Cosmos DB.
