---
title: Translator Text-API methode BreakSentence
titleSuffix: Azure Cognitive Services
description: Met de Translator Text-API methode BreakSentence wordt de plaatsing van de begrenzing van zinnen in een stuk tekst bepaald.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 02/01/2019
ms.author: swmachan
ms.openlocfilehash: c8ef1d4dacf500c459ae1ab9a534ed118ca9e05a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75446697"
---
# <a name="translator-text-api-30-breaksentence"></a>Translator Text-API 3,0: BreakSentence

Hiermee wordt de positie van zinnen in een stuk tekst bepaald.

## <a name="request-url"></a>Aanvraag-URL

Een `POST` aanvraag verzenden naar:

```HTTP
https://api.cognitive.microsofttranslator.com/breaksentence?api-version=3.0
```

## <a name="request-parameters"></a>Aanvraag parameters

Aanvraag parameters die zijn door gegeven voor de query reeks zijn:

<table width="100%">
  <th width="20%">Query parameter</th>
  <th>Beschrijving</th>
  <tr>
    <td>api-version</td>
    <td>*Vereiste query parameter*.<br/>De versie van de API die door de client is aangevraagd. De waarde moet `3.0`zijn.</td>
  </tr>
  <tr>
    <td>language</td>
    <td>*Optionele query parameter*.<br/>Taal code die de taal van de invoer tekst aangeeft. Als er geen code is opgegeven, wordt automatische taal detectie toegepast.</td>
  </tr>
  <tr>
    <td>uit</td>
    <td>*Optionele query parameter*.<br/>Script label waarmee het script wordt aangeduid dat wordt gebruikt door de invoer tekst. Als er geen script is opgegeven, wordt het standaard script van de taal gebruikt.</td>
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
    <td>*Optioneel*.<br/>Een door de client gegenereerde GUID om de aanvraag uniek te identificeren. Houd er rekening mee dat u deze koptekst kunt weglaten als u de tracerings-ID opneemt in de query reeks met behulp van een query parameter met de naam `ClientTraceId`.</td>
  </tr>
</table> 

## <a name="request-body"></a>Aanvraagbody

De hoofd tekst van de aanvraag is een JSON-matrix. Elk matrix element is een JSON-object met een teken reeks eigenschap met de naam `Text`. De grenzen van zinnen worden berekend voor de waarde van de eigenschap `Text`. Een voor beeld van een aanvraag tekst met een tekst fragment ziet er als volgt uit:

```json
[
    { "Text": "How are you? I am fine. What did you do today?" }
]
```

Hierbij gelden de volgende beperkingen:

* De matrix kan Maxi maal 100 elementen bevatten.
* De tekst waarde van een matrix element mag niet langer zijn dan 10.000 tekens, inclusief spaties.
* De volledige tekst die in de aanvraag is opgenomen, mag niet langer zijn dan 50.000 tekens, inclusief spaties.
* Als de `language` query parameter is opgegeven, moeten alle matrix elementen zich in dezelfde taal bevindt. Anders wordt automatische taal detectie toegepast op elk matrix element onafhankelijk van elkaar.

## <a name="response-body"></a>Antwoord tekst

Een geslaagde reactie is een JSON-matrix met één resultaat voor elke teken reeks in de invoer matrix. Een resultaat object bevat de volgende eigenschappen:

  * `sentLen`: een matrix van gehele getallen die de lengte van de zinnen in het tekst element vertegenwoordigt. De lengte van de matrix is het aantal zinnen en de waarden zijn de lengte van elke zin. 

  * `detectedLanguage`: een object dat de gedetecteerde taal beschrijft via de volgende eigenschappen:

     * `language`: code van de gedetecteerde taal.

     * `score`: een float-waarde waarmee het vertrouwen in het resultaat wordt aangegeven. De Score ligt tussen nul en een en een lage score geeft een lage betrouw baarheid aan.
     
    Houd er rekening mee dat de eigenschap `detectedLanguage` alleen aanwezig is in het resultaat object als automatische taal detectie is aangevraagd.

Een voor beeld van een JSON-antwoord is:

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
    <td>De waarde die door de service is gegenereerd om de aanvraag te identificeren. Dit wordt gebruikt voor het oplossen van problemen.</td>
  </tr>
</table> 

## <a name="response-status-codes"></a>Antwoord status codes

Hier volgen de mogelijke HTTP-status codes die een aanvraag retourneert. 

<table width="100%">
  <th width="20%">Statuscode</th>
  <th>Beschrijving</th>
  <tr>
    <td>200</td>
    <td>Geslaagd.</td>
  </tr>
  <tr>
    <td>400</td>
    <td>Een van de query parameters ontbreekt of is ongeldig. Corrigeer de aanvraag parameters voordat u het opnieuw probeert.</td>
  </tr>
  <tr>
    <td>401</td>
    <td>De aanvraag kan niet worden geverifieerd. Controleer of de referenties zijn opgegeven en geldig zijn.</td>
  </tr>
  <tr>
    <td>403</td>
    <td>De aanvraag is niet gemachtigd. Controleer het fout bericht Details. Dit betekent vaak dat alle gratis vertalingen van een proef abonnement zijn gebruikt.</td>
  </tr>
  <tr>
    <td>429</td>
    <td>De server heeft de aanvraag geweigerd omdat de aanvraag limieten voor de client is overschreden.</td>
  </tr>
  <tr>
    <td>500</td>
    <td>Er is een onverwachte fout opgetreden. Als de fout zich blijft voordoen, meldt u deze met: datum en tijd van de fout, aanvraag-id van de reactie header `X-RequestId`en de client-id van de aanvraag header `X-ClientTraceId`.</td>
  </tr>
  <tr>
    <td>503</td>
    <td>De server is tijdelijk niet beschikbaar. Voer de aanvraag opnieuw uit. Als de fout zich blijft voordoen, meldt u deze met: datum en tijd van de fout, aanvraag-id van de reactie header `X-RequestId`en de client-id van de aanvraag header `X-ClientTraceId`.</td>
  </tr>
</table> 

Als er een fout optreedt, wordt door de aanvraag ook een JSON-fout bericht geretourneerd. De fout code is een getal van 6 cijfers, waarbij de HTTP-status code van 3 cijfers wordt gevolgd door een getal van drie cijfers om de fout verder te categoriseren. Algemene fout codes vindt u op de [pagina v3-Translator text-API-referentie](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#errors). 

## <a name="examples"></a>Voorbeelden

In het volgende voor beeld ziet u hoe u de grenzen van zinnen voor één zin kunt verkrijgen. De taal van de zin wordt automatisch gedetecteerd door de service.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/breaksentence?api-version=3.0" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'How are you? I am fine. What did you do today?'}]"
```

