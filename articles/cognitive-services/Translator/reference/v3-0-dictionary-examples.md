---
title: Voor beelden van Translator Text-API Dictionary-methode
titleSuffix: Azure Cognitive Services
description: De methode Translator Text-API Dictionary-voor beelden biedt voor beelden die laten zien hoe voor waarden in de woorden lijst worden gebruikt in de context.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/29/2018
ms.author: swmachan
ms.openlocfilehash: daa3ff7cb9006a0ec940a57a4db31746dcb0550a
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/09/2019
ms.locfileid: "73888105"
---
# <a name="translator-text-api-30-dictionary-examples"></a>Translator Text-API 3,0: woordenlijst voorbeelden

Biedt voor beelden die laten zien hoe termen in de woorden lijst in de context worden gebruikt. Deze bewerking wordt samen gebruikt bij het [opzoeken van woorden lijsten](./v3-0-dictionary-lookup.md).

## <a name="request-url"></a>Aanvraag-URL

Een `POST` aanvraag verzenden naar:

```HTTP
https://api.cognitive.microsofttranslator.com/dictionary/examples?api-version=3.0
```

## <a name="request-parameters"></a>Aanvraag parameters

Aanvraag parameters die zijn door gegeven voor de query reeks zijn:

<table width="100%">
  <th width="20%">Query parameter</th>
  <th>Beschrijving</th>
  <tr>
    <td>API-versie</td>
    <td>*Vereiste para meter*.<br/>De versie van de API die door de client is aangevraagd. De waarde moet `3.0`zijn.</td>
  </tr>
  <tr>
    <td>Van</td>
    <td>*Vereiste para meter*.<br/>Geeft de taal van de invoer tekst aan. De bron taal moet een van de [ondersteunde talen](./v3-0-languages.md) zijn die is opgenomen in het `dictionary` bereik.</td>
  </tr>
  <tr>
    <td>tot</td>
    <td>*Vereiste para meter*.<br/>Hiermee geeft u de taal van de uitvoer tekst op. De doel taal moet een van de [ondersteunde talen](./v3-0-languages.md) zijn die is opgenomen in het `dictionary` bereik.</td>
  </tr>
</table>

Aanvraag headers zijn onder andere:

<table width="100%">
  <th width="20%">Headers</th>
  <th>Beschrijving</th>
  <tr>
    <td>Verificatie header (s)</td>
    <td>De <em>vereiste aanvraag header</em>.<br/>Bekijk de <a href="https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication">beschik bare opties voor authenticatie</a>.</td>
  </tr>
  <tr>
    <td>Content-Type</td>
    <td>De *vereiste aanvraag header*.<br/>Hiermee geeft u het inhouds type van de payload op. Mogelijke waarden zijn: `application/json`.</td>
  </tr>
  <tr>
    <td>Content-length</td>
    <td>De *vereiste aanvraag header*.<br/>De lengte van de aanvraag tekst.</td>
  </tr>
  <tr>
    <td>X-ClientTraceId</td>
    <td>*Optioneel*.<br/>Een door de client gegenereerde GUID om de aanvraag uniek te identificeren. U kunt deze koptekst weglaten als u de tracerings-ID in de query reeks opneemt met behulp van een query parameter met de naam `ClientTraceId`.</td>
  </tr>
</table> 

## <a name="request-body"></a>Aanvraagbody

De hoofd tekst van de aanvraag is een JSON-matrix. Elk matrix element is een JSON-object met de volgende eigenschappen:

  * `Text`: een teken reeks die de term aangeeft die moet worden gezocht. Dit moet de waarde zijn van een `normalizedText` veld van de back-vertalingen van een eerdere lookup-aanvraag voor een [woorden lijst](./v3-0-dictionary-lookup.md) . Dit kan ook de waarde van het `normalizedSource` veld zijn.

  * `Translation`: een teken reeks waarmee de vertaalde tekst wordt opgegeven die eerder door de zoek bewerking van de [woorden lijst](./v3-0-dictionary-lookup.md) is geretourneerd. Dit moet de waarde zijn uit het `normalizedTarget` veld in de `translations` lijst van het zoek antwoord in de [woorden lijst](./v3-0-dictionary-lookup.md) . Met de service worden voor beelden geretourneerd voor het specifieke woord paar van de doel groep.

Een voor beeld is:

```json
[
    {"Text":"fly", "Translation":"volar"}
]
```

De volgende beperkingen zijn van toepassing:

* De matrix kan Maxi maal 10 elementen bevatten.
* De tekst waarde van een matrix element mag niet langer zijn dan 100 tekens, inclusief spaties.

## <a name="response-body"></a>Antwoord tekst

Een geslaagde reactie is een JSON-matrix met één resultaat voor elke teken reeks in de invoer matrix. Een resultaat object bevat de volgende eigenschappen:

  * `normalizedSource`: een teken reeks met de genormaliseerde vorm van de bron term. Over het algemeen moet dit gelijk zijn aan de waarde van het `Text` veld in de lijst index in de hoofd tekst van de aanvraag.
    
  * `normalizedTarget`: een teken reeks waarmee de genormaliseerde vorm van de doel term wordt aangeduid. Over het algemeen moet dit gelijk zijn aan de waarde van het `Translation` veld in de lijst index in de hoofd tekst van de aanvraag.
  
  * `examples`: een lijst met voor beelden voor het paar (bron term, doel term). Elk element van de lijst is een object met de volgende eigenschappen:

    * `sourcePrefix`: de teken reeks die moet worden samengevoegd _vóór_ de waarde van `sourceTerm` om een volledig voor beeld te vormen. Voeg geen spatie toe, omdat deze al aanwezig is wanneer dit moet zijn. Deze waarde kan een lege teken reeks zijn.

    * `sourceTerm`: een teken reeks die gelijk is aan de werkelijke term die wordt opgezocht. De teken reeks wordt toegevoegd met `sourcePrefix` en `sourceSuffix` om het volledige voor beeld te vormen. De waarde ervan wordt gescheiden, zodat deze kan worden gemarkeerd in een gebruikers interface, bijvoorbeeld door het vet te maken.

    * `sourceSuffix`: de teken reeks die moet worden samengevoegd _na_ de waarde van `sourceTerm` om een volledig voor beeld te vormen. Voeg geen spatie toe, omdat deze al aanwezig is wanneer dit moet zijn. Deze waarde kan een lege teken reeks zijn.

    * `targetPrefix`: een teken reeks die vergelijkbaar is met `sourcePrefix` maar voor het doel.

    * `targetTerm`: een teken reeks die vergelijkbaar is met `sourceTerm` maar voor het doel.

    * `targetSuffix`: een teken reeks die vergelijkbaar is met `sourceSuffix` maar voor het doel.

    > [!NOTE]
    > Als er geen voor beelden in de woorden lijst staan, is de reactie 200 (OK), maar is de `examples` lijst een lege lijst.

## <a name="examples"></a>Voorbeelden

In dit voor beeld ziet u hoe u voor beelden kunt opzoeken voor het paar dat is gemaakt met de Engelse termijn `fly` en de Spaanse Vertaal `volar`.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/dictionary/examples?api-version=3.0&from=en&to=es" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'fly', 'Translation':'volar'}]"
```

De antwoord tekst (afgekort voor de duidelijkheid) is:

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
