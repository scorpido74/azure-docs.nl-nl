---
title: Model- en partitiegegevens op Azure Cosmos DB met een voorbeeld in de praktijk
description: Meer informatie over het modelleren en partitioneren van een voorbeeld in de echte wereld met behulp van de Azure Cosmos DB Core API
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: thweiss
ms.openlocfilehash: 10f8ffd90215a21ca03e112aea463d444c623d06
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75445377"
---
# <a name="how-to-model-and-partition-data-on-azure-cosmos-db-using-a-real-world-example"></a>Meer informatie over het modelleren en partitioneren van gegevens in Azure Cosmos DB aan de hand van een praktijkvoorbeeld

Dit artikel bouwt voort op verschillende Azure Cosmos DB-concepten, zoals [gegevensmodellering,](modeling-data.md) [partitionering](partitioning-overview.md)en [ingerichte doorvoer](request-units.md) om aan te tonen hoe u een oefening voor gegevensontwerp in de echte wereld aanpakken.

Als u meestal met relationele databases werkt, hebt u waarschijnlijk gewoonten en intuïties opgebouwd over het ontwerpen van een gegevensmodel. Vanwege de specifieke beperkingen, maar ook de unieke sterke punten van Azure Cosmos DB, vertalen de meeste van deze best practices niet goed en kunnen ze u naar suboptimale oplossingen slepen. Het doel van dit artikel is om u te begeleiden door het volledige proces van het modelleren van een real-world use-case op Azure Cosmos DB, van item modellering tot entiteit colocatie en container partitionering.

## <a name="the-scenario"></a>Het scenario

Voor deze oefening gaan we het domein overwegen van een blogplatform waar *gebruikers* *berichten*kunnen maken. Gebruikers kunnen *like* ook *graag* en opmerkingen toevoegen aan die berichten.

> [!TIP]
> We hebben een aantal woorden in cursieve aandacht *genoemd;* deze woorden identificeren het soort "dingen" ons model zal moeten manipuleren.

Meer eisen toevoegen aan onze specificatie:

- Op een voorpagina wordt een feed weergegeven van recent gemaakte berichten,
- We kunnen halen alle berichten voor een gebruiker, alle reacties voor een bericht en alle houdt voor een bericht,
- Berichten worden geretourneerd met de gebruikersnaam van hun auteurs en een telling van hoeveel reacties en vind-ik-leuks ze hebben,
- Reacties en vind-ik-leuks worden ook geretourneerd met de gebruikersnaam van de gebruikers die ze hebben gemaakt,
- Wanneer berichten worden weergegeven als lijsten, hoeven ze alleen een afgekaptoverzicht van de inhoud ervan weer te geven.

## <a name="identify-the-main-access-patterns"></a>De belangrijkste toegangspatronen identificeren

Om te beginnen geven we enige structuur aan onze oorspronkelijke specificatie door de toegangspatronen van onze oplossing te identificeren. Bij het ontwerpen van een gegevensmodel voor Azure Cosmos DB is het belangrijk om te begrijpen welke aanvragen ons model moet dienen om ervoor te zorgen dat het model deze aanvragen efficiënt zal dienen.

Om het algemene proces gemakkelijker te volgen, categoriseren we deze verschillende aanvragen als opdrachten of query's, lenen we een aantal woordenschat van [CQRS](https://en.wikipedia.org/wiki/Command%E2%80%93query_separation#Command_query_responsibility_segregation) waar opdrachten schrijfverzoeken zijn (dat wil zeggen, intenties om het systeem bij te werken) en query's zijn alleen-lezen verzoeken.

Hier is de lijst met verzoeken die ons platform zal moeten blootleggen:

- **[C1]** Een gebruiker maken/bewerken
- **[Q1]** Een gebruiker ophalen
- **[C2]** Een bericht maken/bewerken
- **[Q2]** Een bericht ophalen
- **[Q3]** Berichten van een gebruiker in korte vorm weergeven
- **[C3]** Een opmerking maken
- **[Q4]** De opmerkingen van een bericht weergeven
- **[C4]** Net als een bericht
- **[Q5]** De vind-ik-leuks van een bericht weergeven
- **[Q6]** De *x* meest recente berichten weergeven die in korte vorm zijn gemaakt (feed)

Als dit stadium hebben we niet nagedacht over de details van wat elke entiteit (gebruiker, post, enz.) zal bevatten. Deze stap is meestal een van de eersten die worden aangepakt bij het ontwerpen tegen een relationele winkel, omdat we moeten uitzoeken hoe deze entiteiten zullen vertalen in termen van tabellen, kolommen, buitenlandse sleutels enz. Het is veel minder een zorg met een documentdatabase die geen schema afdwingt bij het schrijven.

De belangrijkste reden waarom het belangrijk is om onze toegangspatronen vanaf het begin te identificeren, is omdat deze lijst met verzoeken onze testsuite zal zijn. Elke keer dat we ons gegevensmodel herhalen, zullen we elk van de aanvragen doorlopen en de prestaties en schaalbaarheid ervan controleren.

## <a name="v1-a-first-version"></a>V1: Een eerste versie

We beginnen met `users` twee `posts`containers: en .

### <a name="users-container"></a>Gebruikerscontainer

Deze container slaat alleen gebruikersitems op:

    {
      "id": "<user-id>",
      "username": "<username>"
    }

We verdelen deze `id`container door , wat betekent dat elke logische partitie binnen die container slechts één item zal bevatten.

### <a name="posts-container"></a>Berichten container

In deze container worden berichten, opmerkingen en vind-ik-leuks ontvangen:

    {
      "id": "<post-id>",
      "type": "post",
      "postId": "<post-id>",
      "userId": "<post-author-id>",
      "title": "<post-title>",
      "content": "<post-content>",
      "creationDate": "<post-creation-date>"
    }

    {
      "id": "<comment-id>",
      "type": "comment",
      "postId": "<post-id>",
      "userId": "<comment-author-id>",
      "content": "<comment-content>",
      "creationDate": "<comment-creation-date>"
    }

    {
      "id": "<like-id>",
      "type": "like",
      "postId": "<post-id>",
      "userId": "<liker-id>",
      "creationDate": "<like-creation-date>"
    }

We verdelen deze `postId`container door , wat betekent dat elke logische partitie binnen die container één bericht zal bevatten, alle opmerkingen voor dat bericht en alle vind-ik-leuks voor dat bericht.

Houd er rekening mee `type` dat we een eigenschap hebben geïntroduceerd in de items die in deze container zijn opgeslagen om onderscheid te maken tussen de drie soorten entiteiten die deze container host.

Ook hebben we ervoor gekozen om gerelateerde gegevens te verwijzen in plaats van deze in te bedden (controleer [deze sectie](modeling-data.md) voor meer informatie over deze concepten) omdat:

- er is geen bovengrens aan het aantal berichten dat een gebruiker kan maken,
- berichten kunnen willekeurig lang zijn,
- er is geen bovengrens aan hoeveel reacties en vind-ik-leuks een bericht kan hebben,
- we willen in staat zijn om een reactie of een dergelijke toe te voegen aan een post zonder de post zelf bij te werken.

## <a name="how-well-does-our-model-perform"></a>Hoe goed presteert ons model?

Het is nu tijd om de prestaties en schaalbaarheid van onze eerste versie te beoordelen. Voor elk van de eerder geïdentificeerde aanvragen meten we de latentie en het aantal aanvraageenheden dat het verbruikt. Deze meting wordt gedaan tegen een dummy-gegevensset met 100.000 gebruikers met 5 tot 50 berichten per gebruiker en maximaal 25 reacties en 100 vind-ik-leuks per bericht.

### <a name="c1-createedit-a-user"></a>[C1] Een gebruiker maken/bewerken

Deze aanvraag is eenvoudig te implementeren omdat we `users` alleen een item in de container maken of bijwerken. De verzoeken zullen zich mooi verspreiden over `id` alle partities dankzij de partitiesleutel.

![Eén item schrijven naar de gebruikerscontainer](./media/how-to-model-partition-example/V1-C1.png)

| **Latentie** | **RU-kosten** | **Prestaties** |
| --- | --- | --- |
| 7 ms | 5.71 RU | ✅ |

### <a name="q1-retrieve-a-user"></a>[Q1] Een gebruiker ophalen

Het ophalen van een gebruiker gebeurt door `users` het bijbehorende item uit de container te lezen.

![Eén item ophalen uit de gebruikerscontainer](./media/how-to-model-partition-example/V1-Q1.png)

| **Latentie** | **RU-kosten** | **Prestaties** |
| --- | --- | --- |
| 2 ms | 1 RU | ✅ |

### <a name="c2-createedit-a-post"></a>[C2] Een bericht maken/bewerken

Net als **[C1]**, we hoeven `posts` alleen maar te schrijven naar de container.

![Eén item schrijven naar de berichtencontainer](./media/how-to-model-partition-example/V1-C2.png)

| **Latentie** | **RU-kosten** | **Prestaties** |
| --- | --- | --- |
| 9 ms | 8.76 RU | ✅ |

### <a name="q2-retrieve-a-post"></a>[Q2] Een bericht ophalen

We beginnen met het ophalen `posts` van het bijbehorende document uit de container. Maar dat is niet genoeg, volgens onze specificatie moeten we ook de gebruikersnaam van de auteur van het bericht en de tellingen van hoeveel reacties en hoeveel likes dit bericht heeft, die 3 extra SQL-query's vereist, verzamelen.

![Een bericht ophalen en aanvullende gegevens aggregeren](./media/how-to-model-partition-example/V1-Q2.png)

Elk van de extra query's filters op de partitiesleutel van de respectieve container, dat is precies wat we willen de prestaties en schaalbaarheid te maximaliseren. Maar uiteindelijk moeten we vier bewerkingen uitvoeren om één post terug te sturen, dus we zullen dat verbeteren in een volgende iteratie.

| **Latentie** | **RU-kosten** | **Prestaties** |
| --- | --- | --- |
| 9 ms | 19.54 RU | ⚠ |

### <a name="q3-list-a-users-posts-in-short-form"></a>[Q3] Berichten van een gebruiker in korte vorm weergeven

Ten eerste moeten we de gewenste berichten ophalen met een SQL-query die de berichten ophaalt die overeenkomen met die specifieke gebruiker. Maar we moeten ook extra query's uitgeven om de gebruikersnaam van de auteur en de tellingen van opmerkingen en vind-ik-leuks samen te voegen.

![Alle berichten ophalen voor een gebruiker en hun aanvullende gegevens aggregeren](./media/how-to-model-partition-example/V1-Q3.png)

Deze implementatie brengt veel nadelen met zich mee:

- de query's die de tellingen van opmerkingen en vind-ik-leuks aggregeren, moeten worden uitgegeven voor elk bericht dat door de eerste query wordt geretourneerd;
- de hoofdquery filtert niet op `posts` de partitiesleutel van de container, wat leidt tot een ventilator-out en een partitiescan over de container.

| **Latentie** | **RU-kosten** | **Prestaties** |
| --- | --- | --- |
| 130 ms | 619,41 RU | ⚠ |

### <a name="c3-create-a-comment"></a>[C3] Een opmerking maken

Er wordt een opmerking gemaakt door `posts` het bijbehorende item in de container te schrijven.

![Eén item schrijven naar de berichtencontainer](./media/how-to-model-partition-example/V1-C2.png)

| **Latentie** | **RU-kosten** | **Prestaties** |
| --- | --- | --- |
| 7 ms | 8.57 RU | ✅ |

### <a name="q4-list-a-posts-comments"></a>[Q4] De opmerkingen van een bericht weergeven

We beginnen met een query die alle opmerkingen voor dat bericht ophaalt en nogmaals, we moeten ook gebruikersnamen afzonderlijk samenvoegen voor elke opmerking.

![Alle opmerkingen voor een bericht ophalen en hun aanvullende gegevens aggregeren](./media/how-to-model-partition-example/V1-Q4.png)

Hoewel de hoofdquery filtert op de partitiesleutel van de container, bestraft het afzonderlijk aggregeren van de gebruikersnamen de algehele prestaties. Dat gaan we later verbeteren.

| **Latentie** | **RU-kosten** | **Prestaties** |
| --- | --- | --- |
| 23 ms | 27.72 RU | ⚠ |

### <a name="c4-like-a-post"></a>[C4] Net als een bericht

Net als **[C3]** maken we het `posts` bijbehorende item in de container.

![Eén item schrijven naar de berichtencontainer](./media/how-to-model-partition-example/V1-C2.png)

| **Latentie** | **RU-kosten** | **Prestaties** |
| --- | --- | --- |
| 6 ms | 7.05 RU | ✅ |

### <a name="q5-list-a-posts-likes"></a>[Q5] De vind-ik-leuks van een bericht weergeven

Net als **[Q4]** vragen we de vind-ik-leuks voor dat bericht en voegen we hun gebruikersnamen samen.

![Het ophalen van alle vind-ik-leuks voor een bericht en het aggregeren van hun aanvullende gegevens](./media/how-to-model-partition-example/V1-Q5.png)

| **Latentie** | **RU-kosten** | **Prestaties** |
| --- | --- | --- |
| 59 ms | 58,92 RU | ⚠ |

### <a name="q6-list-the-x-most-recent-posts-created-in-short-form-feed"></a>[Q6] De x meest recente berichten weergeven die in korte vorm zijn gemaakt (feed)

We halen de meest recente `posts` berichten op door de container op te vragen gesorteerd op aflopende aanmaakdatum, vervolgens gebruikersnamen en aantal opmerkingen en vind-ik-leuks voor elk van de berichten samen te voegen.

![Het ophalen van de meest recente berichten en het aggregeren van hun aanvullende gegevens](./media/how-to-model-partition-example/V1-Q6.png)

Nogmaals, onze eerste query filtert niet op `posts` de partitiesleutel van de container, wat een kostbare uitwaaiering activeert. Deze is nog erger als we ons richten op een `ORDER BY` veel groter resultaat set en sorteren van de resultaten met een clausule, waardoor het duurder in termen van aanvraag eenheden.

| **Latentie** | **RU-kosten** | **Prestaties** |
| --- | --- | --- |
| 306 ms | 2063,54 RU | ⚠ |

## <a name="reflecting-on-the-performance-of-v1"></a>Nadenken over de prestaties van V1

Kijkend naar de prestatieproblemen waarmee we in de vorige sectie te maken kregen, kunnen we twee hoofdklassen van problemen identificeren:

- sommige verzoeken vereisen dat er meerdere query's worden uitgegeven om alle gegevens te verzamelen die we moeten retourneren,
- sommige query's filteren niet op de partitiesleutel van de containers die ze targeten, wat leidt tot een uitwaaiering die onze schaalbaarheid belemmert.

Laten we elk van deze problemen oplossen, te beginnen met de eerste.

## <a name="v2-introducing-denormalization-to-optimize-read-queries"></a>V2: Denormalisatie introduceren om leesquery's te optimaliseren

De reden waarom we in sommige gevallen aanvullende aanvragen moeten indienen, is omdat de resultaten van de oorspronkelijke aanvraag niet alle gegevens bevatten die we moeten retourneren. Bij het werken met een niet-relationele gegevensopslag zoals Azure Cosmos DB wordt dit soort problemen vaak opgelost door gegevens in onze gegevensset te denormaliseren.

In ons voorbeeld wijzigen we objecten voor berichten om de gebruikersnaam van de auteur van het bericht, het aantal reacties en het aantal vind-ik-leuks toe te voegen:

    {
      "id": "<post-id>",
      "type": "post",
      "postId": "<post-id>",
      "userId": "<post-author-id>",
      "userUsername": "<post-author-username>",
      "title": "<post-title>",
      "content": "<post-content>",
      "commentCount": <count-of-comments>,
      "likeCount": <count-of-likes>,
      "creationDate": "<post-creation-date>"
    }

We wijzigen ook opmerkingen en vinden het leuk om de gebruikersnaam toe te voegen van de gebruiker die ze heeft gemaakt:

    {
      "id": "<comment-id>",
      "type": "comment",
      "postId": "<post-id>",
      "userId": "<comment-author-id>",
      "userUsername": "<comment-author-username>",
      "content": "<comment-content>",
      "creationDate": "<comment-creation-date>"
    }

    {
      "id": "<like-id>",
      "type": "like",
      "postId": "<post-id>",
      "userId": "<liker-id>",
      "userUsername": "<liker-username>",
      "creationDate": "<like-creation-date>"
    }

### <a name="denormalizing-comment-and-like-counts"></a>Het denormaliseren van commentaar en het likecounts

Wat we willen bereiken is dat elke keer dat we een `commentCount` reactie `likeCount` of een like toe te voegen, we ook verhogen de of de in de bijbehorende post. Als `posts` onze container is `postId`verdeeld door , het nieuwe item (commentaar of like) en de bijbehorende post zitten in dezelfde logische partitie. Als gevolg hiervan kunnen we een [opgeslagen procedure](stored-procedures-triggers-udfs.md) gebruiken om die bewerking uit te voeren.

Nu bij het maken van een opmerking (**[C3]**), in plaats van alleen het toevoegen van een nieuw item in de `posts` container noemen we de volgende opgeslagen procedure op die container:

```javascript
function createComment(postId, comment) {
  var collection = getContext().getCollection();

  collection.readDocument(
    `${collection.getAltLink()}/docs/${postId}`,
    function (err, post) {
      if (err) throw err;

      post.commentCount++;
      collection.replaceDocument(
        post._self,
        post,
        function (err) {
          if (err) throw err;

          comment.postId = postId;
          collection.createDocument(
            collection.getSelfLink(),
            comment
          );
        }
      );
    })
}
```

Deze opgeslagen procedure neemt de ID van de post en het lichaam van de nieuwe opmerking als parameters, dan:

- haalt het bericht op
- stappen de`commentCount`
- vervangt de post
- voegt de nieuwe opmerking toe

Aangezien opgeslagen procedures worden uitgevoerd als atoomtransacties, `commentCount` is het gegarandeerd dat de waarde van en het werkelijke aantal opmerkingen altijd synchroon blijven.

We noemen uiteraard een soortgelijke opgeslagen procedure bij `likeCount`het toevoegen van nieuwe vind-ik-leuks om de .

### <a name="denormalizing-usernames"></a>Gebruikersnamen denormaliseren

Gebruikersnamen vereisen een andere aanpak omdat gebruikers niet alleen in verschillende partities zitten, maar in een andere container. Wanneer we gegevens moeten denormaliseren over partities en containers, kunnen we de [wijzigingsfeed](change-feed.md)van de broncontainer gebruiken.

In ons voorbeeld gebruiken we de `users` wijzigingsfeed van de container om te reageren wanneer gebruikers hun gebruikersnamen bijwerken. Wanneer dat gebeurt, verspreiden we de wijziging door `posts` een andere opgeslagen procedure op de container aan te roepen:

![Gebruikersnamen denormaliseren in de berichtencontainer](./media/how-to-model-partition-example/denormalization-1.png)

```javascript
function updateUsernames(userId, username) {
  var collection = getContext().getCollection();
  
  collection.queryDocuments(
    collection.getSelfLink(),
    `SELECT * FROM p WHERE p.userId = '${userId}'`,
    function (err, results) {
      if (err) throw err;

      for (var i in results) {
        var doc = results[i];
        doc.userUsername = username;

        collection.upsertDocument(
          collection.getSelfLink(),
          doc);
      }
    });
}
```

Deze opgeslagen procedure neemt de ID van de gebruiker en de nieuwe gebruikersnaam van de gebruiker als parameters, dan:

- haalt alle items `userId` die overeenkomen met de (die kunnen worden berichten, opmerkingen, of vind-ik-leuks)
- voor elk van deze items
  - vervangt de`userUsername`
  - vervangt het item

> [!IMPORTANT]
> Deze bewerking is kostbaar omdat deze opgeslagen procedure moet worden `posts` uitgevoerd op elke partitie van de container. We gaan ervan uit dat de meeste gebruikers een geschikte gebruikersnaam kiezen tijdens het aanmelden en deze nooit zullen veranderen, dus deze update wordt zeer zelden uitgevoerd.

## <a name="what-are-the-performance-gains-of-v2"></a>Wat zijn de prestatiewinsten van V2?

### <a name="q2-retrieve-a-post"></a>[Q2] Een bericht ophalen

Nu onze denormalisatie op zijn plaats is, hoeven we maar één item op te halen om dat verzoek af te handelen.

![Eén item ophalen uit de berichtencontainer](./media/how-to-model-partition-example/V2-Q2.png)

| **Latentie** | **RU-kosten** | **Prestaties** |
| --- | --- | --- |
| 2 ms | 1 RU | ✅ |

### <a name="q4-list-a-posts-comments"></a>[Q4] De opmerkingen van een bericht weergeven

Ook hier kunnen we de extra aanvragen sparen die de gebruikersnamen hebben opgehaald en eindigen met een enkele query die op de partitiesleutel filtert.

![Alle reacties voor een bericht ophalen](./media/how-to-model-partition-example/V2-Q4.png)

| **Latentie** | **RU-kosten** | **Prestaties** |
| --- | --- | --- |
| 4 ms | 7.72 RU | ✅ |

### <a name="q5-list-a-posts-likes"></a>[Q5] De vind-ik-leuks van een bericht weergeven

Exact dezelfde situatie bij het aanbieden van de vind-ik-leuks.

![Alle vind-ik-leuks ophalen voor een bericht](./media/how-to-model-partition-example/V2-Q5.png)

| **Latentie** | **RU-kosten** | **Prestaties** |
| --- | --- | --- |
| 4 ms | 8.92 RU | ✅ |

## <a name="v3-making-sure-all-requests-are-scalable"></a>V3: Ervoor zorgen dat alle aanvragen schaalbaar zijn

Kijkend naar onze algemene prestatieverbeteringen, zijn er nog steeds twee verzoeken die we niet volledig hebben geoptimaliseerd: **[Q3]** en **[Q6]**. Dit zijn de aanvragen met query's die niet filteren op de partitiesleutel van de containers die ze targeten.

### <a name="q3-list-a-users-posts-in-short-form"></a>[Q3] Berichten van een gebruiker in korte vorm weergeven

Deze aanvraag profiteert nu al van de verbeteringen die zijn geïntroduceerd in V2, waardoor extra vragen worden gespaard.

![Alle berichten ophalen voor een gebruiker](./media/how-to-model-partition-example/V2-Q3.png)

Maar de resterende query is nog steeds niet `posts` filteren op de partitiesleutel van de container.

De manier om na te denken over deze situatie is eigenlijk eenvoudig:

1. Deze aanvraag *moet* filteren `userId` op de omdat we willen alle berichten voor een bepaalde gebruiker te halen
1. Het presteert niet goed omdat het wordt `posts` uitgevoerd tegen de container, die niet wordt verdeeld door`userId`
1. Met vermelding van de voor de hand liggende, zouden we onze prestaties probleem op te lossen door het uitvoeren van dit verzoek tegen een container die *is* verdeeld door`userId`
1. Het blijkt dat we al zo'n container hebben: de `users` container!

Dus introduceren we een tweede niveau van denormalisatie door hele `users` posten te dupliceren naar de container. Door dat te doen, krijgen we effectief een kopie van onze berichten, alleen verdeeld langs `userId`een andere afmetingen, waardoor ze veel efficiënter op te halen door hun .

De `users` container bevat nu 2 soorten items:

    {
      "id": "<user-id>",
      "type": "user",
      "userId": "<user-id>",
      "username": "<username>"
    }

    {
      "id": "<post-id>",
      "type": "post",
      "postId": "<post-id>",
      "userId": "<post-author-id>",
      "userUsername": "<post-author-username>",
      "title": "<post-title>",
      "content": "<post-content>",
      "commentCount": <count-of-comments>,
      "likeCount": <count-of-likes>,
      "creationDate": "<post-creation-date>"
    }

Opmerking:

- we hebben een `type` veld in het gebruikersitem geïntroduceerd om gebruikers te onderscheiden van berichten,
- we hebben ook `userId` een veld toegevoegd in het `id` gebruikersitem, dat `users` overbodig is met `userId` het veld, maar vereist is omdat de container nu wordt verdeeld door (en niet `id` zoals voorheen)

Om die denormalisatie te bereiken, gebruiken we opnieuw de change feed. Deze keer reageren we op de `posts` wijzigingsfeed van de container `users` om een nieuwe of bijgewerkte post naar de container te verzenden. En omdat berichten die worden weergegeven niet nodig zijn om de volledige inhoud terug te sturen, kunnen we ze in het proces afkappen.

![Berichten in de gebruikerscontainer denormaliseren](./media/how-to-model-partition-example/denormalization-2.png)

We kunnen nu onze `users` query doorsturen naar de container, filteren op de partitiesleutel van de container.

![Alle berichten ophalen voor een gebruiker](./media/how-to-model-partition-example/V3-Q3.png)

| **Latentie** | **RU-kosten** | **Prestaties** |
| --- | --- | --- |
| 4 ms | 6.46 RU | ✅ |

### <a name="q6-list-the-x-most-recent-posts-created-in-short-form-feed"></a>[Q6] De x meest recente berichten weergeven die in korte vorm zijn gemaakt (feed)

We hebben hier te maken met een vergelijkbare situatie: zelfs na het sparen van de extra query's die onnodig zijn achtergelaten door de denormalisatie die in V2 is geïntroduceerd, filtert de resterende query niet op de partitiesleutel van de container:

![Meest recente berichten ophalen](./media/how-to-model-partition-example/V2-Q6.png)

Volgens dezelfde aanpak vereist het maximaliseren van de prestaties en schaalbaarheid van deze aanvraag dat het slechts één partitie raakt. Dit is denkbaar omdat we slechts een beperkt aantal artikelen hoeven terug te sturen; om de startpagina van ons blogplatform te vullen, hoeven we alleen maar de 100 meest recente berichten te krijgen, zonder dat we de hele gegevensset hoeven te bekijken.

Dus om dit laatste verzoek te optimaliseren, introduceren we een derde container in ons ontwerp, volledig gewijd aan het dienen van dit verzoek. We denormaliseren onze berichten naar `feed` die nieuwe container:

    {
      "id": "<post-id>",
      "type": "post",
      "postId": "<post-id>",
      "userId": "<post-author-id>",
      "userUsername": "<post-author-username>",
      "title": "<post-title>",
      "content": "<post-content>",
      "commentCount": <count-of-comments>,
      "likeCount": <count-of-likes>,
      "creationDate": "<post-creation-date>"
    }

Deze container wordt `type`verdeeld door , `post` die altijd zal worden in onze items. Als u dat doet, zorgt u ervoor dat alle items in deze container in dezelfde partitie zitten.

Om de denormalisatie te bereiken, hoeven we alleen maar aan te haken op de change feed pipeline die we eerder hebben geïntroduceerd om de palen naar die nieuwe container te verzenden. Een belangrijk ding om in gedachten te houden is dat we ervoor moeten zorgen dat we alleen de 100 meest recente berichten opslaan; anders kan de inhoud van de container groter worden dan de maximale grootte van een partitie. Dit wordt gedaan door elke keer dat een document in de container wordt toegevoegd een [posttrigger](stored-procedures-triggers-udfs.md#triggers) aan te roepen:

![Denormaliseren van berichten in de voercontainer](./media/how-to-model-partition-example/denormalization-3.png)

Hier is het lichaam van de post-trigger die de collectie afgekapt:

```javascript
function truncateFeed() {
  const maxDocs = 100;
  var context = getContext();
  var collection = context.getCollection();

  collection.queryDocuments(
    collection.getSelfLink(),
    "SELECT VALUE COUNT(1) FROM f",
    function (err, results) {
      if (err) throw err;

      processCountResults(results);
    });

  function processCountResults(results) {
    // + 1 because the query didn't count the newly inserted doc
    if ((results[0] + 1) > maxDocs) {
      var docsToRemove = results[0] + 1 - maxDocs;
      collection.queryDocuments(
        collection.getSelfLink(),
        `SELECT TOP ${docsToRemove} * FROM f ORDER BY f.creationDate`,
        function (err, results) {
          if (err) throw err;

          processDocsToRemove(results, 0);
        });
    }
  }

  function processDocsToRemove(results, index) {
    var doc = results[index];
    if (doc) {
      collection.deleteDocument(
        doc._self,
        function (err) {
          if (err) throw err;

          processDocsToRemove(results, index + 1);
        });
    }
  }
}
```

De laatste stap is om onze `feed` vraag om te leiden naar onze nieuwe container:

![Meest recente berichten ophalen](./media/how-to-model-partition-example/V3-Q6.png)

| **Latentie** | **RU-kosten** | **Prestaties** |
| --- | --- | --- |
| 9 ms | 16.97 RU | ✅ |

## <a name="conclusion"></a>Conclusie

Laten we eens kijken naar de algemene prestaties en schaalbaarheid verbeteringen die we hebben geïntroduceerd over de verschillende versies van ons ontwerp.

| | V1 | V2 | V3 |
| --- | --- | --- | --- |
| **[C1]** | 7 ms / 5,71 RU | 7 ms / 5,71 RU | 7 ms / 5,71 RU |
| **[Q1]** | 2 ms / 1 RU | 2 ms / 1 RU | 2 ms / 1 RU |
| **[C2]** | 9 ms / 8,76 RU | 9 ms / 8,76 RU | 9 ms / 8,76 RU |
| **[Q2]** | 9 ms / 19,54 RU | 2 ms / 1 RU | 2 ms / 1 RU |
| **[Q3]** | 130 ms / 619.41 RU | 28 ms / 201,54 RU | 4 ms / 6,46 RU |
| **[C3]** | 7 ms / 8,57 RU | 7 ms / 15,27 RU | 7 ms / 15,27 RU |
| **[Q4]** | 23 ms / 27,72 RU | 4 ms / 7,72 RU | 4 ms / 7,72 RU |
| **[C4]** | 6 ms / 7,05 RU | 7 ms / 14,67 RU | 7 ms / 14,67 RU |
| **[Q5]** | 59 ms / 58,92 RU | 4 ms / 8,92 RU | 4 ms / 8,92 RU |
| **[Q6]** | 306 ms / 2063,54 RU | 83 ms / 532,33 RU | 9 ms / 16,97 RU |

### <a name="we-have-optimized-a-read-heavy-scenario"></a>We hebben een lees-zwaar scenario geoptimaliseerd

U hebt misschien gemerkt dat we onze inspanningen hebben geconcentreerd om de prestaties van leesverzoeken (query's) te verbeteren ten koste van schrijfverzoeken (opdrachten). In veel gevallen leiden schrijfbewerkingen nu tot latere denormalisatie via change feeds, waardoor ze rekenkundig duurder en langer zijn om te materialiseren.

Dit wordt gerechtvaardigd door het feit dat een blogging platform (zoals de meeste sociale apps) is read-heavy, wat betekent dat het bedrag van de leesverzoeken die het moet dienen is meestal ordes van grootte hoger dan het bedrag van de schrijfverzoeken. Dus is het zinvol om schrijfverzoeken duurder uit te voeren om leesverzoeken goedkoper en beter te laten presteren.

Als we kijken naar de meest extreme optimalisatie die we hebben gedaan, **[Q6]** ging van 2000 + RU's tot slechts 17 R's; we hebben dat bereikt door berichten te denormaliseren tegen een kostprijs van ongeveer 10 BO's per artikel. Aangezien we veel meer feedverzoeken zouden dienen dan het maken of actualiseren van berichten, zijn de kosten van deze denormalisatie verwaarloosbaar gezien de totale besparingen.

### <a name="denormalization-can-be-applied-incrementally"></a>Denormalisatie kan stapsgewijs worden toegepast

De schaalbaarheidsverbeteringen die we in dit artikel hebben onderzocht, omvatten denormalisatie en duplicatie van gegevens in de gegevensset. Opgemerkt moet worden dat deze optimalisaties niet op de plaats hoeven te worden gezet op dag 1. Query's die filteren op partitiesleutels presteren beter op schaal, maar kruispartitiequery's kunnen volledig aanvaardbaar zijn als ze zelden of tegen een beperkte gegevensset worden aangeroepen. Als u gewoon een prototype bouwt of een product lanceert met een kleine en gecontroleerde gebruikersbasis, u deze verbeteringen waarschijnlijk sparen voor later; Wat dan belangrijk is, is om de prestaties van uw model te [controleren,](use-metrics.md) zodat u beslissen of en wanneer het tijd is om ze binnen te brengen.

De wijzigingsfeed die we gebruiken om updates naar andere containers te distribueren, slaat al deze updates voortdurend op. Dit maakt het mogelijk om alle updates aan te vragen sinds het maken van de container en bootstrap gedenormaliseerde weergaven als een eenmalige inhaalactie, zelfs als uw systeem al veel gegevens heeft.

## <a name="next-steps"></a>Volgende stappen

Na deze inleiding tot praktische gegevensmodellering en partitionering, u de volgende artikelen controleren om de concepten te bekijken die wij behandeldhebben:

- [Werken met databases, containers en items](databases-containers-items.md)
- [Partitionering in Azure Cosmos DB](partitioning-overview.md)
- [Feed wijzigen in Azure Cosmos DB](change-feed.md)
