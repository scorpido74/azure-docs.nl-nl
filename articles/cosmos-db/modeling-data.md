---
title: Gegevens modelleren in Azure Cosmos DB
titleSuffix: Azure Cosmos DB
description: Meer informatie over gegevensmodellering in NoSQL-databases, verschillen tussen modelleringsgegevens in een relationele database en een documentdatabase.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: 523049ea3286445117f41147f3dd12a2c911d1ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72755021"
---
# <a name="data-modeling-in-azure-cosmos-db"></a>Gegevensmodellering in Azure Cosmos DB

Hoewel schemavrije databases, zoals Azure Cosmos DB, het super eenvoudig maken om ongestructureerde en semi-gestructureerde gegevens op te slaan en op te vragen, moet u wat tijd besteden aan het nadenken over uw gegevensmodel om het meeste uit de service te halen op het gebied van prestaties en schaalbaarheid en de laagste Kosten.

Hoe worden gegevens opgeslagen? Hoe gaat uw toepassing gegevens ophalen en opvragen? Is uw toepassing lees-zwaar, of schrijf-zwaar?

Na het lezen van dit artikel u de volgende vragen beantwoorden:

* Wat is data modellering en waarom zou ik zorg?
* Hoe verschilt het modelleren van gegevens in Azure Cosmos DB van een relationele database?
* Hoe druk ik gegevensrelaties uit in een niet-relationele database?
* Wanneer sluit ik gegevens in en wanneer koppel ik aan gegevens?

## <a name="embedding-data"></a>Gegevens insluiten

Wanneer u gegevens in Azure Cosmos DB gaat modelleren, probeert u uw entiteiten te behandelen als zelfstandige items die worden weergegeven als **JSON-documenten.**

Ter vergelijking, laten we eerst eens kijken hoe we gegevens kunnen modelleren in een relationele database. In het volgende voorbeeld ziet u hoe een persoon kan worden opgeslagen in een relationele database.

![Relationeel databasemodel](./media/sql-api-modeling-data/relational-data-model.png)

Bij het werken met relationele databases, de strategie is om al uw gegevens te normaliseren. Het normaliseren van uw gegevens houdt meestal in dat u een entiteit, zoals een persoon, gebruikt en deze opsplitsen in afzonderlijke componenten. In het bovenstaande voorbeeld kan een persoon meerdere contactgegevensrecords hebben, evenals meerdere adresrecords. Contactgegevens kunnen verder worden uitgesplitst door algemene velden als een type verder te extraheren. Hetzelfde geldt voor adres, elke record kan van het type *Home* of *Business*zijn.

Het leidende uitgangspunt bij het normaliseren van gegevens is om te **voorkomen dat redundante gegevens** op elke record worden opgeslagen en eerder naar gegevens te verwijzen. In dit voorbeeld moet u JOINS gebruiken om uw gegevens tijdens het uitvoeren van de gegevens effectief terug te stellen (of te denormaliseren).

    SELECT p.FirstName, p.LastName, a.City, cd.Detail
    FROM Person p
    JOIN ContactDetail cd ON cd.PersonId = p.Id
    JOIN ContactDetailType cdt ON cdt.Id = cd.TypeId
    JOIN Address a ON a.PersonId = p.Id

Voor het bijwerken van één persoon met hun contactgegevens en adressen is schrijfbewerkingen in veel afzonderlijke tabellen vereist.

Laten we nu eens kijken hoe we dezelfde gegevens zouden modelleren als een zelfstandige entiteit in Azure Cosmos DB.

    {
        "id": "1",
        "firstName": "Thomas",
        "lastName": "Andersen",
        "addresses": [
            {
                "line1": "100 Some Street",
                "line2": "Unit 1",
                "city": "Seattle",
                "state": "WA",
                "zip": 98012
            }
        ],
        "contactDetails": [
            {"email": "thomas@andersen.com"},
            {"phone": "+1 555 555-5555", "extension": 5555}
        ]
    }

Met behulp van de bovenstaande aanpak hebben we de persoon record **gedenormaliseerd,** door **het inbedden van** alle informatie met betrekking tot deze persoon, zoals hun contactgegevens en adressen, in *een enkel JSON-document.*
Bovendien, omdat we niet beperkt zijn tot een vast schema hebben we de flexibiliteit om dingen te doen, zoals het hebben van contactgegevens van verschillende vormen volledig.

Het ophalen van een volledige persoonsrecord uit de database is nu **één leesbewerking** tegen één container en voor één item. Het bijwerken van een persoonrecord, met hun contactgegevens en adressen, is ook een **enkele schrijfbewerking** tegen één item.

Als u gegevens denormaliseert, hoeft uw toepassing mogelijk minder query's en updates uit te geven om algemene bewerkingen te voltooien.

### <a name="when-to-embed"></a>Wanneer moet u insluiten

Gebruik in het algemeen ingesloten gegevensmodellen wanneer:

* Er zijn **opgenomen** relaties tussen entiteiten.
* Er zijn **een-op-weinig** relaties tussen entiteiten.
* Er zijn ingesloten gegevens die **zelden veranderen.**
* Er zijn ingesloten gegevens die niet zullen groeien **zonder gebonden**.
* Er zijn ingesloten gegevens die **vaak samen worden opgevraagd.**

> [!NOTE]
> Doorgaans gedenormaliseerde gegevensmodellen bieden betere **leesprestaties.**

### <a name="when-not-to-embed"></a>Wanneer niet insluiten

Hoewel de vuistregel in Azure Cosmos DB is om alles te denormaliseren en alle gegevens in te sluiten in één item, kan dit leiden tot een aantal situaties die moeten worden vermeden.

Neem dit JSON fragment.

    {
        "id": "1",
        "name": "What's new in the coolest Cloud",
        "summary": "A blog post by someone real famous",
        "comments": [
            {"id": 1, "author": "anon", "comment": "something useful, I'm sure"},
            {"id": 2, "author": "bob", "comment": "wisdom from the interwebs"},
            …
            {"id": 100001, "author": "jane", "comment": "and on we go ..."},
            …
            {"id": 1000000001, "author": "angry", "comment": "blah angry blah angry"},
            …
            {"id": ∞ + 1, "author": "bored", "comment": "oh man, will this ever end?"},
        ]
    }

Dit kan zijn wat een post entiteit met ingesloten opmerkingen eruit zou zien als we modelleren van een typische blog, of CMS, systeem. Het probleem met dit voorbeeld is dat de opmerkingen array is **onbegrensd,** wat betekent dat er geen (praktische) limiet aan het aantal opmerkingen een enkel bericht kan hebben. Dit kan een probleem worden als de grootte van het item zou kunnen groeien oneindig groot.

Naarmate de grootte van het item groeit, wordt de mogelijkheid beïnvloed om de gegevens over de draad te verzenden en het item op schaal te lezen en bij te werken.

In dit geval zou het beter zijn om het volgende gegevensmodel te overwegen.

    Post item:
    {
        "id": "1",
        "name": "What's new in the coolest Cloud",
        "summary": "A blog post by someone real famous",
        "recentComments": [
            {"id": 1, "author": "anon", "comment": "something useful, I'm sure"},
            {"id": 2, "author": "bob", "comment": "wisdom from the interwebs"},
            {"id": 3, "author": "jane", "comment": "....."}
        ]
    }

    Comment items:
    {
        "postId": "1"
        "comments": [
            {"id": 4, "author": "anon", "comment": "more goodness"},
            {"id": 5, "author": "bob", "comment": "tails from the field"},
            ...
            {"id": 99, "author": "angry", "comment": "blah angry blah angry"}
        ]
    },
    {
        "postId": "1"
        "comments": [
            {"id": 100, "author": "anon", "comment": "yet more"},
            ...
            {"id": 199, "author": "bored", "comment": "will this ever end?"}
        ]
    }

Dit model heeft de drie meest recente opmerkingen ingebed in de postcontainer, dat is een array met een vaste set van kenmerken. De andere opmerkingen worden gegroepeerd in batches van 100 opmerkingen en opgeslagen als afzonderlijke items. De grootte van de partij werd gekozen als 100 omdat onze fictieve applicatie de gebruiker in staat stelt om 100 reacties tegelijk te laden.  

Een ander geval waarin het insluiten van gegevens geen goed idee is, is wanneer de ingesloten gegevens vaak worden gebruikt voor items en vaak zullen veranderen.

Neem dit JSON fragment.

    {
        "id": "1",
        "firstName": "Thomas",
        "lastName": "Andersen",
        "holdings": [
            {
                "numberHeld": 100,
                "stock": { "symbol": "zaza", "open": 1, "high": 2, "low": 0.5 }
            },
            {
                "numberHeld": 50,
                "stock": { "symbol": "xcxc", "open": 89, "high": 93.24, "low": 88.87 }
            }
        ]
    }

Dit kan de aandelenportefeuille van een persoon vertegenwoordigen. We hebben ervoor gekozen om de voorraadinformatie in te bedden in elk portfoliodocument. In een omgeving waar gerelateerde gegevens vaak veranderen, zoals een toepassing voor de handel in aandelen, zal het insluiten van gegevens die vaak worden gewijzigd, betekenen dat u elk portfoliodocument voortdurend bijwerkt elke keer dat een aandeel wordt verhandeld.

Voorraad *zaza* kan worden verhandeld vele honderden keren in een enkele dag en duizenden gebruikers kunnen *zaza* hebben op hun portefeuille. Met een gegevensmodel als het bovenstaande zouden we vele duizenden portfoliodocumenten vele malen per dag moeten bijwerken, wat leidt tot een systeem dat niet goed zal schalen.

## <a name="referencing-data"></a>Verwijzen naar gegevens

Het insluiten van gegevens werkt voor veel gevallen goed, maar er zijn scenario's waarin het denormaliseren van uw gegevens meer problemen zal veroorzaken dan het waard is. Wat doen we nu?

Relationele databases zijn niet de enige plaats waar u relaties tussen entiteiten maken. In een documentdatabase u informatie in één document hebben die betrekking heeft op gegevens in andere documenten. We raden het bouwen van systemen die beter geschikt zijn voor een relationele database in Azure Cosmos DB, of een andere documentdatabase, maar eenvoudige relaties zijn prima en nuttig.

In de JSON hieronder hebben we ervoor gekozen om het voorbeeld van een aandelenportefeuille van vroeger te gebruiken, maar deze keer verwijzen we naar de voorraadpost op de portefeuille in plaats van het in te bedden. Op deze manier, wanneer de voorraad item verandert vaak gedurende de dag het enige document dat moet worden bijgewerkt is de single stock document.

    Person document:
    {
        "id": "1",
        "firstName": "Thomas",
        "lastName": "Andersen",
        "holdings": [
            { "numberHeld":  100, "stockId": 1},
            { "numberHeld":  50, "stockId": 2}
        ]
    }

    Stock documents:
    {
        "id": "1",
        "symbol": "zaza",
        "open": 1,
        "high": 2,
        "low": 0.5,
        "vol": 11970000,
        "mkt-cap": 42000000,
        "pe": 5.89
    },
    {
        "id": "2",
        "symbol": "xcxc",
        "open": 89,
        "high": 93.24,
        "low": 88.87,
        "vol": 2970200,
        "mkt-cap": 1005000,
        "pe": 75.82
    }

Een onmiddellijk nadeel aan deze benadering is echter als uw toepassing is vereist om informatie over elke voorraad die wordt gehouden bij het weergeven van een persoon portefeuille weer te geven; in dit geval moet u meerdere reizen naar de database maken om de informatie voor elk voorraaddocument te laden. Hier hebben we een beslissing genomen om de efficiëntie van schrijfbewerkingen te verbeteren, die vaak gedurende de dag plaatsvinden, maar op hun beurt gecompromitteerd op de leesbewerkingen die mogelijk minder invloed hebben op de prestaties van dit specifieke systeem.

> [!NOTE]
> Genormaliseerde gegevensmodellen **kunnen meer retourvluchten** naar de server vereisen.

### <a name="what-about-foreign-keys"></a>Hoe zit het met buitenlandse sleutels?

Omdat er momenteel geen concept van een beperking, buitenlandse sleutel of anderszins, geen inter-document relaties die je hebt in documenten zijn effectief "zwakke links" en zal niet worden geverifieerd door de database zelf. Als u ervoor wilt zorgen dat de gegevens waarnaar een document verwijst, daadwerkelijk bestaan, moet u dit doen in uw toepassing of door het gebruik van servertriggers of opgeslagen procedures op Azure Cosmos DB.

### <a name="when-to-reference"></a>Wanneer moet worden verwezen

Gebruik in het algemeen genormaliseerde gegevensmodellen wanneer:

* **Een-op-veel** relaties vertegenwoordigen.
* Die **veel-op-veel** relaties.
* Gerelateerde gegevens **wijzigingen vaak**.
* Gegevens waarnaar wordt verwezen, kunnen **onbegrensd**zijn .

> [!NOTE]
> Doorgaans biedt normaliseren betere **schrijfprestaties.**

### <a name="where-do-i-put-the-relationship"></a>Waar moet ik de relatie neerzetten?

De groei van de relatie zal helpen bepalen in welk document de referentie op te slaan.

Als we kijken naar de JSON hieronder die modellen uitgevers en boeken.

    Publisher document:
    {
        "id": "mspress",
        "name": "Microsoft Press",
        "books": [ 1, 2, 3, ..., 100, ..., 1000]
    }

    Book documents:
    {"id": "1", "name": "Azure Cosmos DB 101" }
    {"id": "2", "name": "Azure Cosmos DB for RDBMS Users" }
    {"id": "3", "name": "Taking over the world one JSON doc at a time" }
    ...
    {"id": "100", "name": "Learn about Azure Cosmos DB" }
    ...
    {"id": "1000", "name": "Deep Dive into Azure Cosmos DB" }

Als het aantal boeken per uitgever klein is met een beperkte groei, kan het nuttig zijn om de boekverwijzing in het uitgeversdocument op te slaan. Als het aantal boeken per uitgever echter niet is begrensd, leidt dit gegevensmodel tot veranderlijke, groeiende arrays, zoals in het bovenstaande voorbeeld-uitgeversdocument.

Schakelen dingen rond een beetje zou resulteren in een model dat nog steeds dezelfde gegevens vertegenwoordigt, maar nu vermijdt deze grote veranderlijke collecties.

    Publisher document:
    {
        "id": "mspress",
        "name": "Microsoft Press"
    }

    Book documents:
    {"id": "1","name": "Azure Cosmos DB 101", "pub-id": "mspress"}
    {"id": "2","name": "Azure Cosmos DB for RDBMS Users", "pub-id": "mspress"}
    {"id": "3","name": "Taking over the world one JSON doc at a time"}
    ...
    {"id": "100","name": "Learn about Azure Cosmos DB", "pub-id": "mspress"}
    ...
    {"id": "1000","name": "Deep Dive into Azure Cosmos DB", "pub-id": "mspress"}

In het bovenstaande voorbeeld hebben we de niet-begrensde verzameling op het uitgeversdocument laten vallen. In plaats daarvan hebben we alleen een verwijzing naar de uitgever op elk boekdocument.

### <a name="how-do-i-model-manymany-relationships"></a>Hoe modelleer ik veel: veel relaties?

In een relationele database *worden veel:veel* relaties vaak gemodelleerd met jointabellen, die alleen records uit andere tabellen samenvoegen.

![Deelnemen aan tabellen](./media/sql-api-modeling-data/join-table.png)

U in de verleiding komen om hetzelfde te repliceren met behulp van documenten en een gegevensmodel te produceren dat lijkt op het volgende.

    Author documents:
    {"id": "a1", "name": "Thomas Andersen" }
    {"id": "a2", "name": "William Wakefield" }

    Book documents:
    {"id": "b1", "name": "Azure Cosmos DB 101" }
    {"id": "b2", "name": "Azure Cosmos DB for RDBMS Users" }
    {"id": "b3", "name": "Taking over the world one JSON doc at a time" }
    {"id": "b4", "name": "Learn about Azure Cosmos DB" }
    {"id": "b5", "name": "Deep Dive into Azure Cosmos DB" }

    Joining documents:
    {"authorId": "a1", "bookId": "b1" }
    {"authorId": "a2", "bookId": "b1" }
    {"authorId": "a1", "bookId": "b2" }
    {"authorId": "a1", "bookId": "b3" }

Dit zou werken. Echter, het laden van een auteur met hun boeken, of het laden van een boek met de auteur, zou altijd ten minste twee extra query's tegen de database. Een query naar het verbindingsdocument en vervolgens een andere query om het werkelijke document te ophalen dat wordt samengevoegd.

Als al deze join tabel doet is lijmen samen twee stukken van de gegevens, waarom dan niet volledig laten vallen?
Overweeg het volgende.

    Author documents:
    {"id": "a1", "name": "Thomas Andersen", "books": ["b1, "b2", "b3"]}
    {"id": "a2", "name": "William Wakefield", "books": ["b1", "b4"]}

    Book documents:
    {"id": "b1", "name": "Azure Cosmos DB 101", "authors": ["a1", "a2"]}
    {"id": "b2", "name": "Azure Cosmos DB for RDBMS Users", "authors": ["a1"]}
    {"id": "b3", "name": "Learn about Azure Cosmos DB", "authors": ["a1"]}
    {"id": "b4", "name": "Deep Dive into Azure Cosmos DB", "authors": ["a2"]}

Nu, als ik een auteur had, weet ik onmiddellijk welke boeken zij hebben geschreven, en omgekeerd als ik een geladen boekdocument had zou ik de IDs van de auteur(s) kennen. Dit bespaart die tussenquery tegen de join-tabel waardoor het aantal serverretouren dat uw toepassing moet maken, wordt verminderd.

## <a name="hybrid-data-models"></a>Hybride gegevensmodellen

We hebben nu gekeken inbedding (of denormaliseren) en verwijzen naar (of normaliseren) gegevens, elk hebben hun voordelen en elk hebben compromissen zoals we hebben gezien.

Het hoeft niet altijd of, wees niet bang om dingen een beetje door elkaar te halen.

Op basis van de specifieke gebruikspatronen en workloads van uw toepassing kunnen er gevallen zijn waarin het mengen van ingesloten en naar de verwijzing genoemde gegevens zinvol is en kan leiden tot eenvoudigere toepassingslogica met minder serverretouren, terwijl ze toch een goed prestatieniveau behouden.

Overweeg de volgende JSON.

    Author documents:
    {
        "id": "a1",
        "firstName": "Thomas",
        "lastName": "Andersen",
        "countOfBooks": 3,
        "books": ["b1", "b2", "b3"],
        "images": [
            {"thumbnail": "https://....png"}
            {"profile": "https://....png"}
            {"large": "https://....png"}
        ]
    },
    {
        "id": "a2",
        "firstName": "William",
        "lastName": "Wakefield",
        "countOfBooks": 1,
        "books": ["b1"],
        "images": [
            {"thumbnail": "https://....png"}
        ]
    }

    Book documents:
    {
        "id": "b1",
        "name": "Azure Cosmos DB 101",
        "authors": [
            {"id": "a1", "name": "Thomas Andersen", "thumbnailUrl": "https://....png"},
            {"id": "a2", "name": "William Wakefield", "thumbnailUrl": "https://....png"}
        ]
    },
    {
        "id": "b2",
        "name": "Azure Cosmos DB for RDBMS Users",
        "authors": [
            {"id": "a1", "name": "Thomas Andersen", "thumbnailUrl": "https://....png"},
        ]
    }

Hier hebben we (meestal) het ingesloten model gevolgd, waarbij gegevens van andere entiteiten zijn ingesloten in het topdocument, maar naar andere gegevens wordt verwezen.

Als je kijkt naar het boek document, kunnen we een paar interessante velden te zien als we kijken naar de array van auteurs. Er is `id` een veld dat is het veld dat we gebruiken om terug te verwijzen naar `name` `thumbnailUrl`een auteur document, standaard praktijk in een genormaliseerd model, maar dan hebben we ook en . We hadden kunnen `id` blijven hangen met en liet de toepassing om eventuele aanvullende informatie die nodig is van de respectieve auteur document met behulp van de "link", maar omdat onze applicatie geeft de naam van de auteur en een miniatuur foto met elk boek weergegeven kunnen we een retournaar de server per boek in een lijst op te slaan door het denormaliseren van **een aantal** gegevens van de auteur.

Tuurlijk, als de naam van de auteur veranderd of ze wilden hun foto te werken zouden we moeten gaan en update elk boek dat ze ooit gepubliceerd, maar voor onze aanvraag, op basis van de veronderstelling dat auteurs niet vaak veranderen hun namen, dit is een aanvaardbaar ontwerp beslissing.  

In het voorbeeld zijn er **vooraf berekende aggregatenwaarden** om dure verwerking op een leesbewerking op te slaan. In het voorbeeld zijn sommige gegevens die in het auteursdocument zijn ingesloten, gegevens die op run-time worden berekend. Telkens wanneer een nieuw boek wordt gepubliceerd, wordt een boekdocument gemaakt **en** wordt het veld countOfBooks ingesteld op een berekende waarde op basis van het aantal boekdocumenten dat voor een bepaalde auteur bestaat. Deze optimalisatie zou goed zijn in het lezen van zware systemen waar we ons kunnen veroorloven om berekeningen te doen op schrijft om te optimaliseren leest.

De mogelijkheid om een model met vooraf berekende velden te hebben, wordt mogelijk gemaakt omdat Azure Cosmos DB **transacties met meerdere documenten**ondersteunt. Veel NoSQL-winkels kunnen geen transacties uitvoeren in verschillende documenten en pleiten daarom voor ontwerpbeslissingen, zoals "altijd alles insluiten", vanwege deze beperking. Met Azure Cosmos DB u servertriggers of opgeslagen procedures gebruiken die boeken invoegen en auteurs bijwerken binnen een ACID-transactie. Nu hoeft u **niet** alles in één document in te sluiten om er zeker van te zijn dat uw gegevens consistent blijven.

## <a name="distinguishing-between-different-document-types"></a>Onderscheid maken tussen verschillende documenttypen

In sommige scenario's u verschillende documenttypen in dezelfde verzameling mengen. dit is meestal het geval wanneer u meerdere gerelateerde documenten in dezelfde [partitie](partitioning-overview.md)wilt plaatsen. U bijvoorbeeld zowel boeken als boekrecensies in dezelfde `bookId`verzameling plaatsen en deze op de hoogte brengen door. In een dergelijke situatie wilt u meestal toevoegen aan uw documenten met een veld dat hun type identificeert om ze te onderscheiden.

    Book documents:
    {
        "id": "b1",
        "name": "Azure Cosmos DB 101",
        "bookId": "b1",
        "type": "book"
    }

    Review documents:
    {
        "id": "r1",
        "content": "This book is awesome",
        "bookId": "b1",
        "type": "review"
    },
    {
        "id": "r2",
        "content": "Best book ever!",
        "bookId": "b1",
        "type": "review"
    }

## <a name="next-steps"></a>Volgende stappen

De grootste afhaalmaaltijden uit dit artikel zijn om te begrijpen dat data modellering in een schema-vrije wereld is net zo belangrijk als altijd.

Net zoals er geen enkele manier is om een stukje gegevens op een scherm weer te geven, is er geen enkele manier om uw gegevens te modelleren. U moet uw toepassing begrijpen en hoe deze de gegevens zal produceren, consumeren en verwerken. Vervolgens u, door een aantal van de hier gepresenteerde richtlijnen toe te passen, een model maken dat voldoet aan de onmiddellijke behoeften van uw toepassing. Wanneer uw toepassingen moeten veranderen, u gebruikmaken van de flexibiliteit van een schemavrije database om die verandering te omarmen en uw gegevensmodel eenvoudig te ontwikkelen.

Raadpleeg de [documentatiepagina](https://azure.microsoft.com/documentation/services/cosmos-db/) van de service voor meer informatie over Azure Cosmos DB.

Als u wilt weten hoe u uw gegevens over meerdere partities verharden, raadpleegt u [Partitioneringsgegevens in Azure Cosmos DB](sql-api-partition-data.md).

Als u wilt weten hoe u gegevens op Azure Cosmos DB modelleren en beheren met behulp van een voorbeeld in de echte wereld, raadpleegt u [Gegevensmodellering en partitionering - een voorbeeld van real-world](how-to-model-partition-example.md).
