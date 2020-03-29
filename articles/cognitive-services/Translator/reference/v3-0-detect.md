---
title: Methode voor het detecteren van vertalertekst
titleSuffix: Azure Cognitive Services
description: Identificeer de taal van een stuk tekst met de methode Azure Cognitive Services Translator Text API Detect.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 02/01/2019
ms.author: swmachan
ms.openlocfilehash: 370f3b14c12fc05f181d6497b7069bbf1cf3c9cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73837299"
---
# <a name="translator-text-api-30-detect"></a>Translator Text API 3.0: Detecteren

Hiermee wordt de taal van een stuk tekst geïdentificeerd.

## <a name="request-url"></a>Aanvraag-URL

Stuur `POST` een verzoek naar:

```HTTP
https://api.cognitive.microsofttranslator.com/detect?api-version=3.0
```

## <a name="request-parameters"></a>Parameters aanvragen

De aangezochtparameters die op de querytekenreeks worden doorgegeven, zijn:

<table width="100%">
  <th width="20%">Queryparameter</th>
  <th>Beschrijving</th>
  <tr>
    <td>api-versie</td>
    <td>*Vereiste parameter*.<br/>Versie van de API die door de client is aangevraagd. Waarde moet `3.0`zijn .</td>
  </tr>
</table> 

Aanvraagkoppen zijn:

<table width="100%">
  <th width="20%">Headers</th>
  <th>Beschrijving</th>
  <tr>
    <td>Verificatiekop(en)</td>
    <td><em>Vereiste aanvraagheader</em>.<br/>Zie <a href="https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication">beschikbare opties voor verificatie</a>.</td>
  </tr>
  <tr>
    <td>Content-Type</td>
    <td>*Vereiste aanvraagheader*.<br/>Hiermee geeft u het inhoudstype van de payload op. Mogelijke waarden `application/json`zijn: .</td>
  </tr>
  <tr>
    <td>Inhoudslengte</td>
    <td>*Vereiste aanvraagheader*.<br/>De lengte van het verzoek lichaam.</td>
  </tr>
  <tr>
    <td>X-ClientTraceid</td>
    <td>*Optioneel*.<br/>Een door de klant gegenereerde GUID om de aanvraag op unieke wijze te identificeren. Houd er rekening mee dat u deze koptekst weglaten als `ClientTraceId`u de traceid in de querytekenreeks opneemt met een queryparameter met de naam.</td>
  </tr>
</table> 

## <a name="request-body"></a>Aanvraagbody

De instantie van het verzoek is een JSON array. Elk array-element is een JSON-object met een tekenreekseigenschap met de naam `Text`. Taaldetectie wordt toegepast op `Text` de waarde van de eigenschap. Een voorbeeldaanvraaginstantie ziet er zo uit:

```json
[
    { "Text": "Ich würde wirklich gern Ihr Auto um den Block fahren ein paar Mal." }
]
```

De volgende beperkingen zijn van toepassing:

* De array kan hoogstens 100 elementen hebben.
* De tekstwaarde van een arrayelement mag niet hoger zijn dan 10.000 tekens, inclusief spaties.
* De volledige tekst in de aanvraag mag niet groter zijn dan 50.000 tekens, inclusief spaties.

## <a name="response-body"></a>Hoofdtekst van de reactie

Een succesvol antwoord is een JSON-array met één resultaat voor elke tekenreeks in de invoerarray. Een resultaatobject bevat de volgende eigenschappen:

  * `language`: Code van de gedetecteerde taal.

  * `score`: Een floatwaarde die het vertrouwen in het resultaat aangeeft. De score is tussen nul en een en een lage score geeft een laag vertrouwen.

  * `isTranslationSupported`: Een booleaanse waarde die waar is als de gedetecteerde taal een van de talen is die worden ondersteund voor tekstvertaling.

  * `isTransliterationSupported`: Een booleaanse waarde die waar is als de gedetecteerde taal een van de talen is die worden ondersteund voor transliteratie.
  
  * `alternatives`: Een scala aan andere mogelijke talen. Elk element van de array is een ander `language`object `score` `isTranslationSupported` met `isTransliterationSupported`dezelfde eigenschappen hierboven vermeld: , , en .

Een voorbeeld json antwoord is:

```json
[
  {
    "language": "de",
    "score": 0.92,
    "isTranslationSupported": true,
    "isTransliterationSupported": false,
    "alternatives": [
      {
        "language": "pt",
        "score": 0.23,
        "isTranslationSupported": true,
        "isTransliterationSupported": false
      },
      {
        "language": "sk",
        "score": 0.23,
        "isTranslationSupported": true,
        "isTransliterationSupported": false
      }
    ]
  }
]
```

## <a name="response-headers"></a>Antwoordheaders

<table width="100%">
  <th width="20%">Headers</th>
  <th>Beschrijving</th>
  <tr>
    <td>X-RequestId</td>
    <td>Waarde gegenereerd door de service om de aanvraag te identificeren. Het wordt gebruikt voor het oplossen van problemen.</td>
  </tr>
</table> 

## <a name="response-status-codes"></a>Statuscodes voor antwoord

Hieronder volgen de mogelijke HTTP-statuscodes die een aanvraag retourneert. 

<table width="100%">
  <th width="20%">Statuscode</th>
  <th>Beschrijving</th>
  <tr>
    <td>200</td>
    <td>Geslaagd.</td>
  </tr>
  <tr>
    <td>400</td>
    <td>Een van de queryparameters ontbreekt of is ongeldig. De aanvraagparameters corrigeren voordat u het opnieuw probeert.</td>
  </tr>
  <tr>
    <td>401</td>
    <td>De aanvraag kan niet worden geverifieerd. Controleer of referenties zijn opgegeven en geldig zijn.</td>
  </tr>
  <tr>
    <td>403</td>
    <td>Het verzoek is niet geautoriseerd. Controleer de foutmelding details. Dit geeft vaak aan dat alle gratis vertalingen met een proefabonnement zijn opgebruikt.</td>
  </tr>
  <tr>
    <td>429</td>
    <td>De server heeft de aanvraag afgewezen omdat de client de aanvraaglimieten heeft overschreden.</td>
  </tr>
  <tr>
    <td>500</td>
    <td>Er is een onverwachte fout opgetreden. Als de fout blijft bestaan, meldt u deze met: datum `X-RequestId`en tijd van de `X-ClientTraceId`fout, aanvraag-id van antwoordkop en client-id van de hoofdtekst van de aanvraag .</td>
  </tr>
  <tr>
    <td>503</td>
    <td>Server tijdelijk niet beschikbaar. Probeer het verzoek opnieuw. Als de fout blijft bestaan, meldt u deze met: datum `X-RequestId`en tijd van de `X-ClientTraceId`fout, aanvraag-id van antwoordkop en client-id van de hoofdtekst van de aanvraag .</td>
  </tr>
</table> 

Als er een fout optreedt, retourneert de aanvraag ook een JSON-foutantwoord. De foutcode is een 6-cijferig getal dat de 3-cijferige HTTP-statuscode combineert, gevolgd door een 3-cijferig getal om de fout verder te categoriseren. Veelvoorkomende foutcodes zijn te vinden op de [referentiepagina v3 Translator Text API.](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#errors) 

## <a name="examples"></a>Voorbeelden

In het volgende voorbeeld ziet u hoe u talen ophaalt die worden ondersteund voor tekstvertaling.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/detect?api-version=3.0" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'What language is this text written in?'}]"
```
