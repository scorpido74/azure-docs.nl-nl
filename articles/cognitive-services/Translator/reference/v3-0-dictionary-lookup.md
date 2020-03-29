---
title: Opzoekmethode voor het opzoeken van de tekst-API-woordenboek voor vertalers
titleSuffix: Azure Cognitive Services
description: De Dictionary Lookup methode biedt alternatieve vertalingen voor een woord en een klein aantal idiomatische zinnen.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 01/21/2020
ms.author: swmachan
ms.openlocfilehash: bd27827441082698bb4e0b43e7dd22d5b7e66539
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "76548948"
---
# <a name="translator-text-api-30-dictionary-lookup"></a>Translator Text API 3.0: Dictionary Lookup

Biedt alternatieve vertalingen voor een woord en een klein aantal idiomatische zinnen. Elke vertaling heeft een deel van de toespraak en een lijst van back-vertalingen. De back-translations stellen een gebruiker in staat om de vertaling in context te begrijpen. Met de bewerking [Voorbeeld woordenboek](./v3-0-dictionary-examples.md) u verder inzoomen om voorbeeldtoepassingen van elk vertaalpaar te zien.

## <a name="request-url"></a>Aanvraag-URL

Stuur `POST` een verzoek naar:

```HTTP
https://api.cognitive.microsofttranslator.com/dictionary/lookup?api-version=3.0
```

## <a name="request-parameters"></a>Parameters aanvragen

De aangezochtparameters die op de querytekenreeks worden doorgegeven, zijn:

| Queryparameter  | Beschrijving |
| ------ | ----------- |
| api-versie <img width=200/>   | **Vereiste parameter**.<br/>Versie van de API die door de client is aangevraagd. Waarde moet worden`3.0` |
| from | **Vereiste parameter**.<br/>Hiermee geeft u de taal van de invoertekst op. De brontaal moet een van de ondersteunde `dictionary` [talen](./v3-0-languages.md) zijn die in het toepassingsgebied zijn opgenomen. |
| tot   | **Vereiste parameter**.<br/>Hiermee geeft u de taal van de uitvoertekst op. De doeltaal moet een van de ondersteunde `dictionary` [talen](v3-0-languages.md) zijn die in het toepassingsgebied zijn opgenomen. |


Aanvraagkoppen zijn:

| Headers  | Beschrijving |
| ------ | ----------- |
| Verificatiekop(en) <img width=200/>  | **Vereiste aanvraagheader**.<br/>Zie <a href="https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication">beschikbare opties voor verificatie</a>. |
| Content-Type | **Vereiste aanvraagheader**.<br/>Hiermee geeft u het inhoudstype van de payload op. Mogelijke waarden `application/json`zijn: . |
| Inhoudslengte   | **Vereiste aanvraagheader**.<br/>De lengte van het verzoek lichaam. |
| X-ClientTraceid   | **Optioneel**.<br/>Een door de klant gegenereerde GUID om de aanvraag op unieke wijze te identificeren. U deze koptekst weglaten als u de traceid `ClientTraceId`in de querytekenreeks opneemt met een queryparameter met de naam. |

## <a name="request-body"></a>Aanvraagbody

De instantie van het verzoek is een JSON array. Elk array-element is een JSON-object met een tekenreekseigenschap met de naam `Text`, die de term vertegenwoordigt die moet worden opzoekd.

```json
[
    {"Text":"fly"}
]
```

De volgende beperkingen zijn van toepassing:

* De array kan hoogstens 10 elementen hebben.
* De tekstwaarde van een arrayelement mag niet meer dan 100 tekens, inclusief spaties, overschrijden.

## <a name="response-body"></a>Hoofdtekst van de reactie

Een succesvol antwoord is een JSON-array met één resultaat voor elke tekenreeks in de invoerarray. Een resultaatobject bevat de volgende eigenschappen:

  * `normalizedSource`: Een tekenreeks die de genormaliseerde vorm van de bronterm geeft. Als het verzoek bijvoorbeeld 'JOHN' is, wordt de genormaliseerde vorm 'john' weergegeven. De inhoud van dit veld wordt de input voor [het opzoeken van voorbeelden.](./v3-0-dictionary-examples.md)
    
  * `displaySource`: Een tekenreeks die de bronterm geeft in een vorm die het meest geschikt is voor de weergave van eindgebruikers. Als de invoer bijvoorbeeld 'JOHN' is, wordt de gebruikelijke spelling van de naam 'John' weergegeven. 

  * `translations`: Een lijst met vertalingen voor de bronterm. Elk element van de lijst is een object met de volgende eigenschappen:

    * `normalizedTarget`: Een tekenreeks die de genormaliseerde vorm van deze term in de doeltaal geeft. Deze waarde moet worden gebruikt als input voor [het opzoeken van voorbeelden](./v3-0-dictionary-examples.md).

    * `displayTarget`: Een tekenreeks die de term in de doeltaal geeft en in een vorm die het meest geschikt is voor de weergave van eindgebruikers. Over het algemeen zal dit `normalizedTarget` alleen verschillen van de in termen van kapitalisatie. Bijvoorbeeld, een goed naamwoord als "Juan" zal hebben `normalizedTarget = "juan"` en `displayTarget = "Juan"`.

    * `posTag`: Een tekenreeks die deze term associeert met een part-of-speech tag.

        | Tagnaam | Beschrijving  |
        |----------|--------------|
        | Bn      | Bijvoeglijke naamwoorden   |
        | Adv      | Bewerkingsparameters      |
        | CONJ CONJ     | Voegwoorden |
        | Det      | Bepalende nemers  |
        | Modale    | Termen        |
        | Zelfstandig naamwoord     | Zelfstandige naamwoorden        |
        | Prep     | Voorzetsels |
        | Uitspr     | Voornaamwoorden     |
        | Werkwoord     | Termen        |
        | Andere    | Overige        |

        Als een implementatie nota, deze tags werden bepaald door een deel van de toespraak tagging de Engelse kant, en vervolgens het nemen van de meest voorkomende tag voor elke bron / doel paar. Dus als mensen vaak een Spaans woord te vertalen naar een ander deel van de toespraak tag in het Engels, tags kunnen uiteindelijk verkeerd (met betrekking tot het Spaanse woord).

    * `confidence`: Een waarde tussen 0,0 en 1,0 die het "vertrouwen" (of misschien nauwkeuriger, "waarschijnlijkheid in de trainingsgegevens") van dat vertaalpaar vertegenwoordigt. De som van de betrouwbaarheidsscores voor één bronwoord kan al dan niet oplopen tot 1,0. 

    * `prefixWord`: Een tekenreeks die het woord geeft om als voorvoegsel van de vertaling weer te geven. Momenteel is dit de gendered determiner van het knooppunt, in talen die gendered determiners hebben. Bijvoorbeeld, het voorvoegsel van het Spaanse woord "mosca" is "la", omdat "mosca" is een vrouwelijk knooppunt in het Spaans. Dit is alleen afhankelijk van de vertaling, en niet van de bron. Als er geen voorvoegsel is, is het de lege tekenreeks.
    
    * `backTranslations`: Een lijst van "terugvertalingen" van het doel. Bronwoorden waarnaar het doel bijvoorbeeld kan vertalen. De lijst is gegarandeerd het bronwoord dat is aangevraagd (bijvoorbeeld als het bronwoord wordt opgezocht is "vliegen", dan is `backTranslations` het gegarandeerd dat "vliegen" zal worden in de lijst). Het is echter niet gegarandeerd in de eerste positie, en vaak ook niet. Elk element `backTranslations` van de lijst is een object dat wordt beschreven door de volgende eigenschappen:

        * `normalizedText`: Een tekenreeks die de genormaliseerde vorm van de bronterm geeft die een back-vertaling van het doel is. Deze waarde moet worden gebruikt als input voor [het opzoeken van voorbeelden](./v3-0-dictionary-examples.md).        

        * `displayText`: Een tekenreeks die de bronterm geeft die een back-vertaling van het doel is in een vorm die het meest geschikt is voor de weergave van eindgebruikers.

        * `numExamples`: Een geheel getal dat het aantal voorbeelden weergeeft dat beschikbaar is voor dit vertaalpaar. Werkelijke voorbeelden moeten worden opgehaald met een afzonderlijke aanroep om voorbeelden op te [zoeken.](./v3-0-dictionary-examples.md) Het nummer is vooral bedoeld om weergave in een UX te vergemakkelijken. Een gebruikersinterface kan bijvoorbeeld een hyperlink toevoegen aan de back-translation als het aantal voorbeelden groter is dan nul en de achterkant als platte tekst weergeven als er geen voorbeelden zijn. Houd er rekening mee dat het werkelijke aantal voorbeelden dat door `numExamples`een aanroep wordt geretourneerd om voorbeelden op te [zoeken,](./v3-0-dictionary-examples.md) kleiner kan zijn dan , omdat extra filtering op de vlieg kan worden toegepast om "slechte" voorbeelden te verwijderen.
        
        * `frequencyCount`: Een geheel getal dat de frequentie van dit vertaalpaar in de gegevens weergeeft. Het belangrijkste doel van dit veld is om een gebruikersinterface te voorzien van een middel om back-translations te sorteren, zodat de meest voorkomende termen eerst zijn.

    > [!NOTE]
    > Als de term die wordt opgezocht niet in het woordenboek staat, is `translations` het antwoord 200 (OK), maar is de lijst een lege lijst.

## <a name="examples"></a>Voorbeelden

In dit voorbeeld ziet u hoe u alternatieve `fly` vertalingen in het Spaans van de Engelse term opzoeken.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/dictionary/lookup?api-version=3.0&from=en&to=es" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'fly'}]"
```

Het responslichaam (afgekort voor duidelijkheid) is:

```
[
    {
        "normalizedSource":"fly",
        "displaySource":"fly",
        "translations":[
            {
                "normalizedTarget":"volar",
                "displayTarget":"volar",
                "posTag":"VERB",
                "confidence":0.4081,
                "prefixWord":"",
                "backTranslations":[
                    {"normalizedText":"fly","displayText":"fly","numExamples":15,"frequencyCount":4637},
                    {"normalizedText":"flying","displayText":"flying","numExamples":15,"frequencyCount":1365},
                    {"normalizedText":"blow","displayText":"blow","numExamples":15,"frequencyCount":503},
                    {"normalizedText":"flight","displayText":"flight","numExamples":15,"frequencyCount":135}
                ]
            },
            {
                "normalizedTarget":"mosca",
                "displayTarget":"mosca",
                "posTag":"NOUN",
                "confidence":0.2668,
                "prefixWord":"",
                "backTranslations":[
                    {"normalizedText":"fly","displayText":"fly","numExamples":15,"frequencyCount":1697},
                    {"normalizedText":"flyweight","displayText":"flyweight","numExamples":0,"frequencyCount":48},
                    {"normalizedText":"flies","displayText":"flies","numExamples":9,"frequencyCount":34}
                ]
            },
            //
            // ...list abbreviated for documentation clarity
            //
        ]
    }
]
```

In dit voorbeeld ziet u wat er gebeurt wanneer de term die wordt opgezocht niet bestaat voor het geldige woordenboekpaar.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/dictionary/lookup?api-version=3.0&from=en&to=es" -H "X-ClientTraceId: 875030C7-5380-40B8-8A03-63DACCF69C11" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'fly123456'}]"
```

Aangezien de term niet in het woordenboek wordt `translations` gevonden, bevat de antwoordtekst een lege lijst.

```
[
    {
        "normalizedSource":"fly123456",
        "displaySource":"fly123456",
        "translations":[]
    }
]
```
