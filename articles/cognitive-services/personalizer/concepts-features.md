---
title: 'Kenmerken: Actie en context - Personalizer'
titleSuffix: Azure Cognitive Services
description: Personalizer gebruikt functies, informatie over acties en context om betere rankingsuggesties te doen. Functies kunnen zeer algemeen zijn, of specifiek voor een item.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: 408501232891a7971d03c89acc647d9ed19609b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77026146"
---
# <a name="features-are-information-about-actions-and-context"></a>Functies zijn informatie over acties en context

De Personalizer-service werkt door te leren wat uw toepassing moet laten zien aan gebruikers in een bepaalde context.

Personalizer maakt gebruik van **functies**, dat is informatie over de **huidige context** om de beste **actie**te kiezen . De functies vertegenwoordigen alle informatie die u denkt te kunnen helpen personaliseren om hogere beloningen te bereiken. Functies kunnen zeer algemeen zijn, of specifiek voor een item. 

U bijvoorbeeld een **functie** hebben over:

* De _gebruiker persona_ `Sports_Shopper`zoals een . Dit mag geen individuele gebruikersnaam zijn. 
* De _inhoud,_ bijvoorbeeld als `Documentary`een `Movie`video een `TV Series`, a , of een , of een , of een retail-item beschikbaar is in de winkel.
* De _huidige_ periode, zoals op welke dag van de week het is.

Personalizer schrijft niet voor welke functies u verzenden voor acties en context:

* U sommige functies verzenden voor sommige acties en niet voor anderen, als u ze niet hebt. Tv-series kunnen bijvoorbeeld kenmerken hebben die films niet hebben.
* U hebt mogelijk slechts enkele keren functies beschikbaar. Een mobiele applicatie kan bijvoorbeeld meer informatie geven dan een webpagina. 
* Na verloop van tijd u functies over context en acties toevoegen en verwijderen. Personalizer blijft leren van de beschikbare informatie.
* Er moet ten minste één functie voor de context zijn. Personalizer ondersteunt geen lege context. Als u alleen elke keer een vaste context verzendt, kiest Personalizer de actie voor ranglijsten alleen met betrekking tot de functies in de acties.
* Voor categorische functies hoeft u de mogelijke waarden niet te definiëren en hoeft u geen bereiken voor numerieke waarden vooraf te definiëren.

## <a name="supported-feature-types"></a>Ondersteunde functietypen

Personalizer ondersteunt functies van tekenreeks-, numerieke en booleaanse typen.

### <a name="how-choice-of-feature-type-affects-machine-learning-in-personalizer"></a>Hoe de keuze van het type functie van invloed is op Machine Learning in Personalizer

* **Tekenreeksen:** Voor tekenreekstypen creëert elke combinatie van toets en waarde nieuwe gewichten in het Personalizer machine learning-model. 
* **Numeriek**: U moet numerieke waarden gebruiken wanneer het getal proportioneel van invloed moet zijn op het personalisatieresultaat. Dit is zeer scenario afhankelijk. In een vereenvoudigd voorbeeld bijvoorbeeld bij het personaliseren van een retail-ervaring, NumberOfPetsOwned kan een functie die numeriek is als je wilt dat mensen met 2 of 3 huisdieren om de personalisatie resultaat twee keer of driemaal zo veel als het hebben van 1 huisdier beïnvloeden. Functies die zijn gebaseerd op numerieke eenheden, maar waar de betekenis niet lineair is - zoals Leeftijd, Temperatuur of Persoonshoogte - worden het best gecodeerd als tekenreeksen en de functiekwaliteit kan doorgaans worden verbeterd met behulp van bereiken. Leeftijd kan bijvoorbeeld worden gecodeerd als "Leeftijd":"0-5", "Leeftijd":"6-10", enz.
* **Booleaanse** waarden verzonden met de waarde van "valse" handelen alsof ze helemaal niet waren verzonden.

Functies die niet aanwezig zijn, moeten uit het verzoek worden weggelaten. Vermijd het verzenden van functies met een null-waarde, omdat deze worden verwerkt als bestaand en met een waarde van "null" bij het trainen van het model.

## <a name="categorize-features-with-namespaces"></a>Functies categoriseren met naamruimten

Personalizer neemt functies in gebruik die zijn ingedeeld in naamruimten. U bepaalt in uw aanvraag of naamruimten worden gebruikt en wat ze zouden moeten zijn. Naamruimten worden gebruikt om functies te groeperen over een vergelijkbaar onderwerp of functies die afkomstig zijn van een bepaalde bron.

Hieronder volgen voorbeelden van functienaamruimten die door toepassingen worden gebruikt:

* User_Profile_from_CRM
* Time
* Mobile_Device_Info
* http_user_agent
* VideoResolutie
* UserDeviceInfo
* Weer
* Product_Recommendation_Ratings
* current_time
* NewsArticle_TextAnalytics

U naamruimten met naamsnaam plaatsen naar uw eigen conventies noemen, zolang het geldige JSON-sleutels zijn. Naamruimten worden gebruikt om functies in verschillende sets te ordenen en om functies met vergelijkbare namen te disambiguateren. U naamruimten zien als een 'voorvoegsel' dat wordt toegevoegd aan functienamen. Naamruimten kunnen niet worden genest.


In de volgende `user`JSON, , `state`en `device` zijn functie naamruimten. 

> [!Note]
> Momenteel raden we ten zeerste aan namen te gebruiken voor functienaamruimten die utf-8 zijn gebaseerd en beginnen met verschillende letters. Bijvoorbeeld , `user` `state`, `device` en `u`beginnen `s`met `d`, , en . Momenteel kan het hebben van naamruimten met dezelfde eerste tekens leiden tot botsingen in indexen die worden gebruikt voor machine learning.

JSON-objecten kunnen geneste JSON-objecten en eenvoudige eigenschap/waarden bevatten. Een array kan alleen worden opgenomen als de arrayitems getallen zijn. 

```JSON
{
    "contextFeatures": [
        { 
            "user": {
                "profileType":"AnonymousUser",
                "latlong": [47.6, -122.1]
            }
        },
        {
            "state": {
                "timeOfDay": "noon",
                "weather": "sunny"
            }
        },
        {
            "device": {
                "mobile":true,
                "Windows":true
            }
        }
    ]
}
```

### <a name="restrictions-in-character-sets-for-namespaces"></a>Beperkingen in tekensets voor naamruimten

De tekenreeks die u gebruikt voor het benoemen van de naamruimte moet een aantal beperkingen volgen: 
* Het kan geen unicode zijn.
* U een aantal van de afdrukbare symbolen met codes < 256 gebruiken voor de namen van de naamruimte. 
* U geen symbolen gebruiken met codes < 32 (niet afdrukbaar), 32 (ruimte), 58 (dikke punt), 124 (pijp) en 126-140.
* Het mag niet beginnen met een underscore "_" of de functie wordt genegeerd.

## <a name="how-to-make-feature-sets-more-effective-for-personalizer"></a>Hoe maak je functiesets effectiever voor Personalizer

Een goede featureset helpt Personalizer te leren hoe hij de actie kan voorspellen die de hoogste beloning oplevert. 

Overweeg functies te verzenden naar de Personalizer Rank API die deze aanbevelingen volgt:

* Er zijn genoeg functies om personalisatie te stimuleren. Hoe specifieker de inhoud moet worden, hoe meer functies nodig zijn.

* Er zijn genoeg kenmerken van diverse *dichtheden.* Een functie is *dicht* als veel items zijn gegroepeerd in een paar emmers. Duizenden video's kunnen bijvoorbeeld worden geclassificeerd als 'Lang' (meer dan 5 minuten lang) en 'Kort' (minder dan 5 minuten lang). Dit is een *zeer dichte* functie. Aan de andere kant kunnen dezelfde duizenden items een attribuut hebben dat "Titel" wordt genoemd, dat bijna nooit dezelfde waarde van het ene artikel naar het andere zal hebben. Dit is een zeer niet-dichte of *schaarse* functie.  

Het hebben van functies met een hoge dichtheid helpt de Personalizer het leren van het ene item naar het andere te extrapoleren. Maar als er slechts een paar functies en ze zijn te dicht, de Personalizer zal proberen om precies richten inhoud met slechts een paar emmers om uit te kiezen.

### <a name="improve-feature-sets"></a>Functiesets verbeteren 

Analyseer het gebruikersgedrag door een offline evaluatie uit te voeren. Hiermee u naar gegevens uit het verleden kijken om te zien welke functies sterk bijdragen aan positieve beloningen ten opzichte van de functies die minder bijdragen. U zien welke functies helpen, en het zal aan u en uw toepassing zijn om betere functies te vinden om naar Personalizer te sturen om de resultaten nog verder te verbeteren.

Deze volgende secties zijn gangbare praktijken voor het verbeteren van functies die naar Personalizer worden verzonden.

#### <a name="make-features-more-dense"></a>Functies dichter maken

Het is mogelijk om uw functiesets te verbeteren door ze te bewerken om ze groter en min of meer dicht te maken.

Bijvoorbeeld, een tijdstempel tot op de tweede is een zeer schaarse functie. Het zou dichter (effectief) kunnen worden gemaakt door tijden in "ochtend", "middag", "middag", enz.

Locatie-informatie profiteert meestal ook van het maken van bredere classificaties. Een Latitude-Lengtecoördiniecoördinie zoals Lat: 47.67402° NB, Long: 122.12154° W is bijvoorbeeld te nauwkeurig en dwingt het model om breedte- en lengtegraad te leren als afzonderlijke afmetingen. Wanneer u probeert te personaliseren op basis van locatie-informatie, helpt het om locatiegegevens in grotere sectoren te groeperen. Een gemakkelijke manier om dat te doen is het kiezen van een passende afronding precisie voor de Lat-Long nummers, en combineren breedte- en lengtegraad in "gebieden" door ze in een string. Een goede manier om bijvoorbeeld 47,67402° NB, Long: 122.12154° W te vertegenwoordigen in regio's van ongeveer een paar kilometer breed zou "locatie":"34.3 , 12,1" zijn.


#### <a name="expand-feature-sets-with-extrapolated-information"></a>Functiesets uitvouwen met geëxtrapoleerd informatie

U ook meer functies krijgen door te denken aan onontgonnen kenmerken die kunnen worden afgeleid uit informatie die u al hebt. Bijvoorbeeld, in een fictieve film lijst personalisatie, is het mogelijk dat een weekend vs weekdag ontlokt ander gedrag van gebruikers? Tijd kan worden uitgebreid tot een "weekend" of "weekdag" attribuut. Hebben nationale culturele feestdagen de aandacht voor bepaalde filmtypes? Een kenmerk 'Halloween' is bijvoorbeeld handig op plaatsen waar het relevant is. Is het mogelijk dat regenachtig weer heeft een aanzienlijke invloed op de keuze van een film voor veel mensen? Met tijd en plaats kan een weerdienst die informatie verstrekken en u deze toevoegen als een extra functie. 

#### <a name="expand-feature-sets-with-artificial-intelligence-and-cognitive-services"></a>Functiesets uitbreiden met kunstmatige intelligentie en cognitieve diensten

Kunstmatige intelligentie en kant-en-klare Cognitieve Diensten kunnen een zeer krachtige aanvulling zijn op de Personalizer. 

Door uw items vooraf te verwerken met behulp van kunstmatige intelligentiediensten, u automatisch informatie extraheren die waarschijnlijk relevant is voor personalisatie.

Bijvoorbeeld:

* U een filmbestand uitvoeren via [Video Indexer](https://azure.microsoft.com/services/media-services/video-indexer/) om scèneelementen, tekst, sentiment en vele andere kenmerken te extraheren. Deze kenmerken kunnen vervolgens dichter worden gemaakt om kenmerken weer te geven die de oorspronkelijke itemmetagegevens niet hadden. 
* Afbeeldingen kunnen worden uitgevoerd door objectdetectie, gezichten door middel van sentiment, enz.
* Informatie in tekst kan worden uitgebreid door entiteiten, sentiment, uitbreiding van entiteiten met Bing kennisgrafiek, enz.

U verschillende andere [Azure Cognitive Services](https://www.microsoft.com/cognitive-services)gebruiken, zoals

* [Entiteiten koppelen](../entitylinking/home.md)
* [Tekstanalyse](../text-analytics/overview.md)
* [Emotion](../emotion/home.md)
* [Computer Visie](../computer-vision/home.md)

## <a name="actions-represent-a-list-of-options"></a>Acties vertegenwoordigen een lijst met opties

Elke actie:

* Heeft _event_ een gebeurtenis-id. Als je al een event-id hebt, moet je dat indienen. Als u geen gebeurtenis-id hebt, stuurt u er geen, maakt Personalizer er een voor u en retourneert deze in het antwoord op het rangverzoek. De id is gekoppeld aan de gebeurtenis Rank, niet aan de gebruiker. Als u een ID maakt, werkt een GUID het beste. 
* Heeft een lijst met functies.
* De lijst met functies kan groot zijn (honderden), maar we raden u aan de effectiviteit van functies te evalueren om functies te verwijderen die niet bijdragen aan het verkrijgen van beloningen. 
* De functies in de **acties** kunnen al dan niet correlatie met functies in de **context** die wordt gebruikt door Personalizer.
* Functies voor acties kunnen aanwezig zijn in sommige acties en niet in andere. 
* Functies voor een bepaalde actie-id zijn mogelijk op een dag beschikbaar, maar worden later niet meer beschikbaar. 

De machine learning-algoritmen van Personalizer presteren beter wanneer er stabiele functiesets zijn, maar Rank-aanroepen mislukken niet als de functieset in de loop van de tijd verandert.

Stuur niet meer dan 50 acties in bij het rangschikken van acties. Dit kunnen elke keer dezelfde 50 acties zijn, of ze kunnen veranderen. Als u bijvoorbeeld een productcatalogus hebt met 10.000 artikelen voor een e-commercetoepassing, u een aanbeveling of filterengine gebruiken om de top 40 te bepalen die een klant leuk vindt en personalistelist gebruiken om de waarde te vinden die de meeste beloning genereert (bijvoorbeeld , zal de gebruiker toevoegen aan het mandje) voor de huidige context.


### <a name="examples-of-actions"></a>Voorbeelden van acties

De acties die u naar de Rank API verzendt, zijn afhankelijk van wat u probeert te personaliseren.

Hier volgen enkele voorbeelden:

|Doel|Actie|
|--|--|
|Personaliseren welk artikel wordt gemarkeerd op een nieuwswebsite.|Elke actie is een mogelijk nieuwsartikel.|
|Optimaliseer de advertentieplaatsing op een website.|Elke actie is een lay-out of regels om een lay-out voor de advertenties te maken (bijvoorbeeld bovenaan, aan de rechterkant, kleine afbeeldingen, grote afbeeldingen).|
|Gepersonaliseerde rangschikking van aanbevolen artikelen weergeven op een winkelwebsite.|Elke actie is een specifiek product.|
|Stel gebruikersinterface-elementen voor, zoals filters die moeten worden toegepast op een specifieke foto.|Elke actie kan een ander filter zijn.|
|Kies het antwoord van een chatbot om de intentie van de gebruiker te verduidelijken of een actie voor te stellen.|Elke actie is een optie voor het interpreteren van het antwoord.|
|Kies boven aan een lijst met zoekresultaten wat u wilt weergeven|Elke actie is een van de belangrijkste zoekresultaten.|


### <a name="examples-of-features-for-actions"></a>Voorbeelden van functies voor acties

Hieronder volgen goede voorbeelden van functies voor acties. Deze zullen veel afhangen van elke toepassing.

* Kenmerken met kenmerken van de acties. Bijvoorbeeld, is het een film of een tv-serie?
* Functies over hoe gebruikers in het verleden mogelijk met deze actie hebben samengewerkt. Bijvoorbeeld, deze film wordt meestal gezien door mensen in demografie A of B, het wordt meestal niet meer dan een keer afgespeeld.
* Kenmerken over de kenmerken van hoe de gebruiker de acties *ziet.* Bevat de poster voor de film in de miniatuur bijvoorbeeld gezichten, auto's of landschappen?

### <a name="load-actions-from-the-client-application"></a>Acties laden vanuit de clienttoepassing

Functies van acties kunnen meestal afkomstig zijn van contentmanagementsystemen, catalogi en aanbevelingssystemen. Uw applicatie is verantwoordelijk voor het laden van de informatie over de acties uit de relevante databases en systemen die u hebt. Als uw acties niet veranderen of elke keer worden geladen, heeft dit een onnodige invloed op de prestaties, u logica in uw toepassing toevoegen om deze informatie in de cache op te slaan.

### <a name="prevent-actions-from-being-ranked"></a>Voorkomen dat acties worden gerangschikt

In sommige gevallen zijn er acties die u niet aan gebruikers wilt weergeven. De beste manier om te voorkomen dat een actie als bovenste wordt gerangschikt, is door deze in de actielijst in de eerste plaats niet op te nemen in de actielijst van de Rank-API.

In sommige gevallen kan het pas later in uw bedrijfslogica worden bepaald als een resulterende _actie_ van een Rank API-aanroep aan een gebruiker moet worden getoond. Voor deze gevallen moet u _Inactieve gebeurtenissen_gebruiken.

## <a name="json-format-for-actions"></a>JSON-indeling voor acties

Wanneer je Rank aanroept, stuur je meerdere acties om uit te kiezen:

JSON-objecten kunnen geneste JSON-objecten en eenvoudige eigenschap/waarden bevatten. Een array kan alleen worden opgenomen als de arrayitems getallen zijn. 

```json
{
    "actions": [
    {
      "id": "pasta",
      "features": [
        {
          "taste": "salty",
          "spiceLevel": "medium",
          "grams": [400,800]
        },
        {
          "nutritionLevel": 5,
          "cuisine": "italian"
        }
      ]
    },
    {
      "id": "ice cream",
      "features": [
        {
          "taste": "sweet",
          "spiceLevel": "none",
          "grams": [150, 300, 450]
        },
        {
          "nutritionalLevel": 2
        }
      ]
    },
    {
      "id": "juice",
      "features": [
        {
          "taste": "sweet",
          "spiceLevel": "none",
          "grams": [300, 600, 900]
        },
        {
          "nutritionLevel": 5
        },
        {
          "drink": true
        }
      ]
    },
    {
      "id": "salad",
      "features": [
        {
          "taste": "salty",
          "spiceLevel": "low",
          "grams": [300, 600]
        },
        {
          "nutritionLevel": 8
        }
      ]
    }
  ]
}
```

## <a name="examples-of-context-information"></a>Voorbeelden van contextinformatie

Informatie voor de _context_ is afhankelijk van elke toepassing en use case, maar kan meestal informatie bevatten zoals:

* Demografische en profielinformatie over uw gebruiker.
* Informatie die is geëxtraheerd uit HTTP-headers, zoals een gebruikersagent, of afkomstig is van HTTP-informatie, zoals reverse geographic-lookups op basis van IP-adressen.
* Informatie over de huidige tijd, zoals dag van de week, weekend of niet, 's ochtends of 's middags, vakantieseizoen of niet, enz.
* Informatie uit mobiele toepassingen, zoals locatie, beweging of batterijniveau.
* Historische aggregaten van het gedrag van gebruikers - zoals wat zijn de filmgenres die deze gebruiker het meest heeft bekeken.

Uw applicatie is verantwoordelijk voor het laden van de informatie over de context van de relevante databases, sensoren en systemen die u mogelijk hebt. Als uw contextgegevens niet worden gewijzigd, u logica in uw toepassing toevoegen om deze gegevens in de cache op te nemen voordat u deze naar de Rank-API verzendt.

## <a name="json-format-for-context"></a>JSON-indeling voor context 

Context wordt uitgedrukt als een JSON-object dat naar de Rank-API wordt verzonden:

JSON-objecten kunnen geneste JSON-objecten en eenvoudige eigenschap/waarden bevatten. Een array kan alleen worden opgenomen als de arrayitems getallen zijn. 

```JSON
{
    "contextFeatures": [
        { 
            "user": {
                "name":"Doug"
            }
        },
        {
            "state": {
                "timeOfDay": "noon",
                "weather": "sunny"
            }
        },
        {
            "device": {
                "mobile":true,
                "Windows":true,
                "screensize": [1680,1050]
                }
            }
        }
    ]
}
```

## <a name="next-steps"></a>Volgende stappen

[Bekrachtigend leren](concepts-reinforcement-learning.md) 
