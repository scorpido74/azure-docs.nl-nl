---
title: Methode voor het verbreken van de api-breuk voor vertalers
titleSuffix: Azure Cognitive Services
description: De methode Translator Text API BreakSentence identificeert de positionering van zinsgrenzen in een stuk tekst.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 01/21/2020
ms.author: swmachan
ms.openlocfilehash: 4c314148b8e1495a8b5a12c42d4989d13cdd6a08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "76548115"
---
# <a name="translator-text-api-30-breaksentence"></a>Translator Text API 3.0: BreakSentence

Hiermee wordt de positionering van zinsgrenzen in een stuk tekst geïdentificeerd.

## <a name="request-url"></a>Aanvraag-URL

Stuur `POST` een verzoek naar:

```HTTP
https://api.cognitive.microsofttranslator.com/breaksentence?api-version=3.0
```

## <a name="request-parameters"></a>Parameters aanvragen

De aangezochtparameters die op de querytekenreeks worden doorgegeven, zijn:

| Queryparameter | Beschrijving |
| -------| ----------- |
| api-versie <img width=200/>   | **Vereiste queryparameter**.<br/>Versie van de API die door de client is aangevraagd. Waarde moet `3.0`zijn . |
| language | **Optionele queryparameter**.<br/>Taaltag die de taal van de invoertekst identificeert. Als een code niet is opgegeven, wordt automatische taaldetectie toegepast. |
| uit    | **Optionele queryparameter**.<br/>Scripttag die het script identificeert dat door de invoertekst wordt gebruikt. Als een script niet is opgegeven, wordt het standaardscript van de taal aangenomen.  | 

Aanvraagkoppen zijn:

| Headers | Beschrijving |
| ------- | ----------- |
| Verificatiekop(en) <img width=200/>  | **Vereiste aanvraagheader**.<br/>Zie <a href="https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication">beschikbare opties voor verificatie</a>. |
| Content-Type | **Vereiste aanvraagheader**.<br/>Hiermee geeft u het inhoudstype van de payload op. Mogelijke waarden `application/json`zijn: . |
| Inhoudslengte    | **Vereiste aanvraagheader**.<br/>De lengte van het verzoek lichaam.  | 
| X-ClientTraceid   | **Optioneel**.<br/>Een door de klant gegenereerde GUID om de aanvraag op unieke wijze te identificeren. Houd er rekening mee dat u deze koptekst weglaten als `ClientTraceId`u de traceid in de querytekenreeks opneemt met een queryparameter met de naam.  | 

## <a name="request-body"></a>Aanvraagbody

De instantie van het verzoek is een JSON array. Elk array-element is een JSON-object met een tekenreekseigenschap met de naam `Text`. Zinsgrenzen worden berekend voor `Text` de waarde van de eigenschap. Een voorbeeldaanvraaginstantie met één stuk tekst ziet er zo uit:

```json
[
    { "Text": "How are you? I am fine. What did you do today?" }
]
```

De volgende beperkingen zijn van toepassing:

* De array kan hoogstens 100 elementen hebben.
* De tekstwaarde van een arrayelement mag niet hoger zijn dan 10.000 tekens, inclusief spaties.
* De volledige tekst in de aanvraag mag niet groter zijn dan 50.000 tekens, inclusief spaties.
* Als `language` de queryparameter is opgegeven, moeten alle matrixelementen in dezelfde taal staan. Anders wordt taalautomatische detectie onafhankelijk toegepast op elk array-element.

## <a name="response-body"></a>Hoofdtekst van de reactie

Een succesvol antwoord is een JSON-array met één resultaat voor elke tekenreeks in de invoerarray. Een resultaatobject bevat de volgende eigenschappen:

  * `sentLen`: Een reeks gehele getallen die de lengtes van de zinnen in het tekstelement weergeven. De lengte van de array is het aantal zinnen en de waarden zijn de lengte van elke zin. 

  * `detectedLanguage`: Een object dat de gedetecteerde taal beschrijft via de volgende eigenschappen:

     * `language`: Code van de gedetecteerde taal.

     * `score`: Een floatwaarde die het vertrouwen in het resultaat aangeeft. De score is tussen nul en een en een lage score geeft een laag vertrouwen.
     
    Houd er `detectedLanguage` rekening mee dat de eigenschap alleen aanwezig is in het resultaatobject wanneer automatische detectie van taal wordt gevraagd.

Een voorbeeld json antwoord is:

```json
[
  {
    "sentLen": [ 13, 11, 22 ]
    "detectedLanguage": {
      "language": "en",
      "score": 401
    },
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

In het volgende voorbeeld ziet u hoe u zinsgrenzen voor één zin verkrijgen. De taal van de zin wordt automatisch gedetecteerd door de service.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/breaksentence?api-version=3.0" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'How are you? I am fine. What did you do today?'}]"
```

