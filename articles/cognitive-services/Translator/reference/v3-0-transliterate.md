---
title: Methode voor transliteraat van vertalertekst
titleSuffix: Azure Cognitive Services
description: Converteer tekst in de ene taal van het ene script naar het andere script met de methode Transliterate van Translator Text API.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 02/01/2019
ms.author: swmachan
ms.openlocfilehash: e6bb1541b2b668796b352bebc68d59b4ade143e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73837282"
---
# <a name="translator-text-api-30-transliterate"></a>Translator Text API 3.0: Transliterate

Converteert tekst in de ene taal van het ene script naar het andere script.

## <a name="request-url"></a>Aanvraag-URL

Stuur `POST` een verzoek naar:

```HTTP
https://api.cognitive.microsofttranslator.com/transliterate?api-version=3.0
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
  <tr>
    <td>language</td>
    <td>*Vereiste parameter*.<br/>Hiermee geeft u de taal op van de tekst die van het ene script naar het andere moet worden geconverteerd. Mogelijke talen worden vermeld `transliteration` in het bereik dat wordt verkregen door de service voor de [ondersteunde talen op te vragen.](./v3-0-languages.md)</td>
  </tr>
  <tr>
    <td>fromScript</td>
    <td>*Vereiste parameter*.<br/>Hiermee geeft u het script op dat door de invoertekst wordt gebruikt. Zoek [ondersteunde talen](./v3-0-languages.md) op `transliteration` met behulp van het bereik om invoerscripts te vinden die beschikbaar zijn voor de geselecteerde taal.</td>
  </tr>
  <tr>
    <td>toScript</td>
    <td>*Vereiste parameter*.<br/>Hiermee geeft u het uitvoerscript op. Zoek [ondersteunde talen](./v3-0-languages.md) op `transliteration` met behulp van het bereik om uitvoerscripts te vinden die beschikbaar zijn voor de geselecteerde combinatie van invoertaal en invoerscript.</td>
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

De instantie van het verzoek is een JSON array. Elk array-element is een JSON-object met een tekenreekseigenschap met de naam `Text`, die de tekenreeks vertegenwoordigt die moet worden geconverteerd.

```json
[
    {"Text":"こんにちは"},
    {"Text":"さようなら"}
]
```

De volgende beperkingen zijn van toepassing:

* De array kan hoogstens 10 elementen hebben.
* De tekstwaarde van een arrayelement mag niet hoger zijn dan 1.000 tekens, inclusief spaties.
* De volledige tekst in de aanvraag mag niet groter zijn dan 5.000 tekens, inclusief spaties.

## <a name="response-body"></a>Hoofdtekst van de reactie

Een succesvolle respons is een JSON-array met één resultaat voor elk element in de invoerarray. Een resultaatobject bevat de volgende eigenschappen:

  * `text`: Een tekenreeks die het resultaat is van het converteren van de invoertekenreeks naar het uitvoerscript.
  
  * `script`: Een tekenreeks die het script opgeeft dat in de uitvoer wordt gebruikt.

Een voorbeeld json antwoord is:

```json
[
    {"text":"konnnichiha","script":"Latn"},
    {"text":"sayounara","script":"Latn"}
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

In het volgende voorbeeld ziet u hoe u twee Japanse tekenreeksen omzet in geromantiseerd Japans.

De JSON payload voor het verzoek in dit voorbeeld:

```json
[{"text":"こんにちは","script":"jpan"},{"text":"さようなら","script":"jpan"}]
```

Als u cURL gebruikt in een opdrachtregelvenster dat unicode-tekens niet ondersteunt, neemt u `request.txt`de volgende JSON-payload en slaat u deze op in een bestand met de naam . Zorg ervoor dat u `UTF-8` het bestand opslaat met codering.

```
curl -X POST "https://api.cognitive.microsofttranslator.com/transliterate?api-version=3.0&language=ja&fromScript=Jpan&toScript=Latn" -H "X-ClientTraceId: 875030C7-5380-40B8-8A03-63DACCF69C11" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d @request.txt
```
