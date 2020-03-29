---
title: Voorbeeldmethode voor voorbeelden van vertaaltekst-API-woordenboek
titleSuffix: Azure Cognitive Services
description: De methode Voorbeelden van voorbeelden van vertalerstekstAPI's bevat voorbeelden die laten zien hoe termen in het woordenboek in context worden gebruikt.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 01/21/2020
ms.author: swmachan
ms.openlocfilehash: b3551a8df19e47178c7bacd9218cfa60b66d81f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "76548064"
---
# <a name="translator-text-api-30-dictionary-examples"></a>Translator Text API 3.0: Voorbeelden van woordenboeken

Geeft voorbeelden die laten zien hoe termen in het woordenboek in context worden gebruikt. Deze bewerking wordt gebruikt in combinatie met [Dictionary lookup](./v3-0-dictionary-lookup.md).

## <a name="request-url"></a>Aanvraag-URL

Stuur `POST` een verzoek naar:

```HTTP
https://api.cognitive.microsofttranslator.com/dictionary/examples?api-version=3.0
```

## <a name="request-parameters"></a>Parameters aanvragen

De aangezochtparameters die op de querytekenreeks worden doorgegeven, zijn:

| Queryparameter | Beschrijving |
| --------- | ----------- |
| api-versie <img width=200/> | **Vereiste parameter**.<br/>Versie van de API die door de client is aangevraagd. Waarde moet `3.0`zijn . |
| from | **Vereiste parameter**.<br/>Hiermee geeft u de taal van de invoertekst op. De brontaal moet een van de ondersteunde `dictionary` [talen](./v3-0-languages.md) zijn die in het toepassingsgebied zijn opgenomen. |
| tot | **Vereiste parameter**.<br/>Hiermee geeft u de taal van de uitvoertekst op. De doeltaal moet een van de ondersteunde `dictionary` [talen](./v3-0-languages.md) zijn die in het toepassingsgebied zijn opgenomen.  | 

Aanvraagkoppen zijn:

| Headers  | Beschrijving |
| ------ | ----------- |
| Verificatiekop(en) <img width=200/>  | **Vereiste aanvraagheader**.<br/>Zie <a href="https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication">beschikbare opties voor verificatie</a>. |
| Content-Type | **Vereiste aanvraagheader**.<br/>Hiermee geeft u het inhoudstype van de payload op. Mogelijke waarden `application/json`zijn: . |
| Inhoudslengte   | **Vereiste aanvraagheader**.<br/>De lengte van het verzoek lichaam. |
| X-ClientTraceid   | **Optioneel**.<br/>Een door de klant gegenereerde GUID om de aanvraag op unieke wijze te identificeren. U deze koptekst weglaten als u de traceid `ClientTraceId`in de querytekenreeks opneemt met een queryparameter met de naam. |

## <a name="request-body"></a>Aanvraagbody

De instantie van het verzoek is een JSON array. Elk array-element is een JSON-object met de volgende eigenschappen:

  * `Text`: Een tekenreeks die de term opzoekt. Dit moet de waarde `normalizedText` zijn van een veld uit de back-translations van een eerdere [opzoekaanvraag van een woordenboek.](./v3-0-dictionary-lookup.md) Het kan ook de `normalizedSource` waarde van het veld zijn.

  * `Translation`: Een tekenreeks die de vertaalde tekst opgeeft die eerder is geretourneerd door de [opzoekbewerking Woordenboek.](./v3-0-dictionary-lookup.md) Dit moet de waarde `normalizedTarget` uit `translations` het veld in de lijst met de [opzoekreactie van](./v3-0-dictionary-lookup.md) het woordenboek zijn. De service retourneert voorbeelden voor het specifieke brondoelwoordpaar.

Een voorbeeld is:

```json
[
    {"Text":"fly", "Translation":"volar"}
]
```

De volgende beperkingen zijn van toepassing:

* De array kan hoogstens 10 elementen hebben.
* De tekstwaarde van een arrayelement mag niet meer dan 100 tekens, inclusief spaties, overschrijden.

## <a name="response-body"></a>Hoofdtekst van de reactie

Een succesvol antwoord is een JSON-array met één resultaat voor elke tekenreeks in de invoerarray. Een resultaatobject bevat de volgende eigenschappen:

  * `normalizedSource`: Een tekenreeks die de genormaliseerde vorm van de bronterm geeft. Over het algemeen moet dit identiek `Text` zijn aan de waarde van het veld op de overeenkomende lijstindex in de hoofdtekst van het verzoek.
    
  * `normalizedTarget`: Een tekenreeks die de genormaliseerde vorm van de doelterm geeft. Over het algemeen moet dit identiek `Translation` zijn aan de waarde van het veld op de overeenkomende lijstindex in de hoofdtekst van het verzoek.
  
  * `examples`: Een lijst met voorbeelden voor het (bronterm, doeltermijn) paar. Elk element van de lijst is een object met de volgende eigenschappen:

    * `sourcePrefix`: De tekenreeks die moet worden samengevoegd `sourceTerm` _vóór_ de waarde van het vormen van een volledig voorbeeld. Voeg geen spatieteken toe, want het is er al wanneer het zou moeten zijn. Deze waarde kan een lege tekenreeks zijn.

    * `sourceTerm`: Een tekenreeks die gelijk is aan de werkelijke term die is opgezocht. De tekenreeks wordt `sourcePrefix` `sourceSuffix` toegevoegd met en om het volledige voorbeeld te vormen. De waarde ervan is gescheiden, zodat deze kan worden gemarkeerd in een gebruikersinterface, bijvoorbeeld door deze vet te maken.

    * `sourceSuffix`: De tekenreeks die moet _after_ worden samengevoegd `sourceTerm` na de waarde van het vormen van een volledig voorbeeld. Voeg geen spatieteken toe, want het is er al wanneer het zou moeten zijn. Deze waarde kan een lege tekenreeks zijn.

    * `targetPrefix`: Een tekenreeks `sourcePrefix` vergelijkbaar met maar voor het doel.

    * `targetTerm`: Een tekenreeks `sourceTerm` vergelijkbaar met maar voor het doel.

    * `targetSuffix`: Een tekenreeks `sourceSuffix` vergelijkbaar met maar voor het doel.

    > [!NOTE]
    > Als er geen voorbeelden in het woordenboek staan, is het `examples` antwoord 200 (OK), maar is de lijst een lege lijst.

## <a name="examples"></a>Voorbeelden

In dit voorbeeld ziet u hoe u voorbeelden `fly` opzoeken voor `volar`het paar dat bestaat uit de Engelse term en de Spaanse vertaling.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/dictionary/examples?api-version=3.0&from=en&to=es" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'fly', 'Translation':'volar'}]"
```

Het responslichaam (afgekort voor duidelijkheid) is:

```
[
    {
        "normalizedSource":"fly",
        "normalizedTarget":"volar",
        "examples":[
            {
                "sourcePrefix":"They need machines to ",
                "sourceTerm":"fly",
                "sourceSuffix":".",
                "targetPrefix":"Necesitan máquinas para ",
                "targetTerm":"volar",
                "targetSuffix":"."
            },      
            {
                "sourcePrefix":"That should really ",
                "sourceTerm":"fly",
                "sourceSuffix":".",
                "targetPrefix":"Eso realmente debe ",
                "targetTerm":"volar",
                "targetSuffix":"."
            },
            //
            // ...list abbreviated for documentation clarity
            //
        ]
    }
]
```
