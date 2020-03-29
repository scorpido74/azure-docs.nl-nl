---
title: Methode voor talen van de vertalertekst-API
titleSuffix: Azure Cognitive Services
description: De methode Talen krijgt de set talen die momenteel worden ondersteund door andere bewerkingen van de Translator Text API.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 02/01/2019
ms.author: swmachan
ms.openlocfilehash: 37f70399e8125db559098869cdfffdf4533498d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73835838"
---
# <a name="translator-text-api-30-languages"></a>Translator Text API 3.0: Talen

Krijgt de set talen die momenteel worden ondersteund door andere bewerkingen van de Translator Text API. 

## <a name="request-url"></a>Aanvraag-URL

Stuur `GET` een verzoek naar:
```HTTP
https://api.cognitive.microsofttranslator.com/languages?api-version=3.0
```

## <a name="request-parameters"></a>Parameters aanvragen

De aangezochtparameters die op de querytekenreeks worden doorgegeven, zijn:

<table width="100%">
  <th width="20%">Queryparameter</th>
  <th>Beschrijving</th>
  <tr>
    <td>api-versie</td>
    <td><em>Vereiste parameter</em>.<br/>Versie van de API die door de client is aangevraagd. Waarde moet `3.0`zijn .</td>
  </tr>
  <tr>
    <td>scope</td>
    <td>*Optionele parameter*.<br/>Een door komma's gescheiden lijst met namen die de groep talen definiëren om terug te keren. Toegestane groepsnamen `translation`zijn: `transliteration` `dictionary`, en . Als er geen bereik wordt gegeven, worden alle `scope=translation,transliteration,dictionary`groepen geretourneerd, wat gelijk staat aan het passeren van . Zie de beschrijving van het [antwoordobject](#response-body)om te bepalen welke set ondersteunde talen geschikt is voor uw scenario.</td>
  </tr>
</table> 

De headers van de vraag zijn:

<table width="100%">
  <th width="20%">Headers</th>
  <th>Beschrijving</th>
  <tr>
    <td>Accept-Language</td>
    <td>*Optionele aanvraagkoptekst*.<br/>De taal die moet worden gebruikt voor gebruikersinterfacetekenreeksen. Sommige velden in het antwoord zijn namen van talen of namen van regio's. Gebruik deze parameter om de taal te definiëren waarin deze namen worden geretourneerd. De taal wordt gespecificeerd door het verstrekken van een goed gevormde BCP 47 taaltag. Gebruik bijvoorbeeld de `fr` waarde om namen in het `zh-Hant` Frans op te vragen of gebruik de waarde om namen op te vragen in het Chinese Traditioneel.<br/>Namen worden in het Engels opgegeven wanneer een doeltaal niet is opgegeven of wanneer lokalisatie niet beschikbaar is.
    </td>
  </tr>
  <tr>
    <td>X-ClientTraceid</td>
    <td>*Optionele aanvraagkoptekst*.<br/>Een door de klant gegenereerde GUID om de aanvraag op unieke wijze te identificeren.</td>
  </tr>
</table> 

Verificatie is niet vereist om taalbronnen op te halen.

## <a name="response-body"></a>Hoofdtekst van de reactie

Een client `scope` gebruikt de queryparameter om te bepalen in welke groepen talen hij geïnteresseerd is.

* `scope=translation`biedt talen die worden ondersteund om tekst van de ene taal naar de andere te vertalen;

* `scope=transliteration`biedt mogelijkheden voor het converteren van tekst in de ene taal van het ene script naar het andere script;

* `scope=dictionary`biedt taalparen `Dictionary` waarvoor bewerkingen gegevens retourneren.

Een client kan meerdere groepen tegelijk ophalen door een door komma's gescheiden lijst met namen op te geven. Als `scope=translation,transliteration,dictionary` u bijvoorbeeld ondersteunde talen voor alle groepen retourneert.

Een succesvol antwoord is een JSON-object met één eigenschap voor elke aangevraagde groep:

```json
{
    "translation": {
        //... set of languages supported to translate text (scope=translation)
    },
    "transliteration": {
        //... set of languages supported to convert between scripts (scope=transliteration)
    },
    "dictionary": {
        //... set of languages supported for alternative translations and examples (scope=dictionary)
    }
}
```

De waarde voor elke eigenschap is als volgt.

* `translation`Eigenschap

  De waarde `translation` van de eigenschap is een woordenboek van (sleutel, waarde) paren. Elke toets is een BCP 47-taaltag. Een sleutel identificeert een taal waarnaar tekst kan worden vertaald of vertaald. De waarde die aan de sleutel is gekoppeld, is een JSON-object met eigenschappen die de taal beschrijven:

  * `name`: Geef de naam van de taal `Accept-Language` weer in de landine die via de kopwordt gevraagd.

  * `nativeName`: Geef de naam van de taal weer in de landinstelling voor deze taal.

  * `dir`: Directionaliteit, `rtl` die is voor talen `ltr` van rechts naar links of voor talen van links naar rechts.

  Een voorbeeld is:
          
  ```json
  {
    "translation": {
      ...
      "fr": {
        "name": "French",
        "nativeName": "Français",
        "dir": "ltr"
      },
      ...
    }
  }
  ```

* `transliteration`Eigenschap

  De waarde `transliteration` van de eigenschap is een woordenboek van (sleutel, waarde) paren. Elke toets is een BCP 47-taaltag. Een sleutel identificeert een taal waarvoor tekst kan worden geconverteerd van het ene script naar het andere script. De waarde die aan de sleutel is gekoppeld, is een JSON-object met eigenschappen die de taal en de ondersteunde scripts beschrijven:

  * `name`: Geef de naam van de taal `Accept-Language` weer in de landine die via de kopwordt gevraagd.

  * `nativeName`: Geef de naam van de taal weer in de landinstelling voor deze taal.

  * `scripts`: Lijst met scripts om van te converteren. Elk element `scripts` van de lijst heeft eigenschappen:

    * `code`: Code die het script identificeert.

    * `name`: Weergave naam van het script in `Accept-Language` de landine gevraagd via header.

    * `nativeName`: Geef de naam van de taal weer in de landnaam voor de taal.

    * `dir`: Directionaliteit, `rtl` die is voor talen `ltr` van rechts naar links of voor talen van links naar rechts.

    * `toScripts`: Lijst met scripts die beschikbaar zijn om tekst naar om te zetten. Elk element `toScripts` van de `code` `name`lijst `nativeName`heeft `dir` eigenschappen , , en zoals eerder beschreven.

  Een voorbeeld is:

  ```json
  {
    "transliteration": {
      ...
      "ja": {
        "name": "Japanese",
        "nativeName": "日本語",
        "scripts": [
          {
            "code": "Jpan",
            "name": "Japanese",
            "nativeName": "日本語",
            "dir": "ltr",
            "toScripts": [
              {
                "code": "Latn",
                "name": "Latin",
                "nativeName": "ラテン語",
                "dir": "ltr"
              }
            ]
          },
          {
            "code": "Latn",
            "name": "Latin",
            "nativeName": "ラテン語",
            "dir": "ltr",
            "toScripts": [
              {
                "code": "Jpan",
                "name": "Japanese",
                "nativeName": "日本語",
                "dir": "ltr"
              }
            ]
          }
        ]
      },
      ...
    }
  }
  ```

* `dictionary`Eigenschap

  De waarde `dictionary` van de eigenschap is een woordenboek van (sleutel, waarde) paren. Elke toets is een BCP 47-taaltag. De sleutel identificeert een taal waarvoor alternatieve vertalingen en back-translations beschikbaar zijn. De waarde is een JSON-object dat de brontaal en de doeltalen beschrijft met beschikbare vertalingen:

  * `name`: Geef de naam van de brontaal `Accept-Language` weer in de landine die via koptekst wordt aangevraagd.

  * `nativeName`: Geef de naam van de taal weer in de landinstelling voor deze taal.

  * `dir`: Directionaliteit, `rtl` die is voor talen `ltr` van rechts naar links of voor talen van links naar rechts.

  * `translations`: Lijst van talen met wisselende vertalingen en voorbeelden voor de query uitgedrukt in de brontaal. Elk element `translations` van de lijst heeft eigenschappen:

    * `name`: Geef de naam van de doeltaal `Accept-Language` weer in de landtaal die via de koptekst wordt aangevraagd.

    * `nativeName`: Geef de naam van de doeltaal weer in de landtaal voor de doeltaal.

    * `dir`: Directionaliteit, `rtl` die is voor talen `ltr` van rechts naar links of voor talen van links naar rechts.
    
    * `code`: Taalcode die de doeltaal identificeert.

  Een voorbeeld is:

  ```json
  "es": {
    "name": "Spanish",
    "nativeName": "Español",
    "dir": "ltr",
    "translations": [
      {
        "name": "English",
        "nativeName": "English",
        "dir": "ltr",
        "code": "en"
      }
    ]
  },
  ```

De structuur van het antwoordobject verandert niet zonder een wijziging in de versie van de API. Voor dezelfde versie van de API kan de lijst met beschikbare talen in de loop van de tijd veranderen omdat Microsoft Translator de lijst met talen die door zijn services worden ondersteund, voortdurend uitbreidt.

De lijst met ondersteunde talen wordt niet vaak gewijzigd. Als u de netwerkbandbreedte wilt besparen en de reactievermogen wilt verbeteren,`ETag`moet een clienttoepassing overwegen om taalbronnen en de bijbehorende entiteitstag ( ) in cache te plaatsen. Vervolgens kan de clienttoepassing periodiek (bijvoorbeeld eenmaal per 24 uur) de service opvragen om de nieuwste set ondersteunde talen op te halen. Als u `ETag` de `If-None-Match` huidige waarde in een kopveld doorgeeft, kan de service de respons optimaliseren. Als de resource niet is gewijzigd, retourneert de service statuscode 304 en een lege antwoordinstantie.

## <a name="response-headers"></a>Antwoordheaders

<table width="100%">
  <th width="20%">Headers</th>
  <th>Beschrijving</th>
  <tr>
    <td>ETag (ETag)</td>
    <td>Huidige waarde van de entiteitstag voor de gevraagde groepen ondersteunde talen. Om volgende aanvragen efficiënter te maken, `ETag` kan `If-None-Match` de client de waarde verzenden in een kopveld.
    </td>
  </tr>
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
    <td>304</td>
    <td>De bron is niet gewijzigd sinds de `If-None-Match`versie die is opgegeven door aanvraagkoppen .</td>
  </tr>
  <tr>
    <td>400</td>
    <td>Een van de queryparameters ontbreekt of is ongeldig. De aanvraagparameters corrigeren voordat u het opnieuw probeert.</td>
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
curl "https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation"
```
