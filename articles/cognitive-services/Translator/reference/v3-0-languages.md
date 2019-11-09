---
title: Methode voor Translator Text-API talen
titleSuffix: Azure Cognitive Services
description: De methode languages haalt de set talen op die momenteel wordt ondersteund door andere bewerkingen van de Translator Text-API.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 02/01/2019
ms.author: swmachan
ms.openlocfilehash: 37f70399e8125db559098869cdfffdf4533498d7
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73835838"
---
# <a name="translator-text-api-30-languages"></a>Translator Text-API 3,0: talen

Hiermee wordt de set talen opgehaald die momenteel door andere bewerkingen van de Translator Text-API worden ondersteund. 

## <a name="request-url"></a>Aanvraag-URL

Een `GET` aanvraag verzenden naar:
```HTTP
https://api.cognitive.microsofttranslator.com/languages?api-version=3.0
```

## <a name="request-parameters"></a>Aanvraag parameters

Aanvraag parameters die zijn door gegeven voor de query reeks zijn:

<table width="100%">
  <th width="20%">Query parameter</th>
  <th>Beschrijving</th>
  <tr>
    <td>API-versie</td>
    <td><em>Vereiste para meter</em>.<br/>De versie van de API die door de client is aangevraagd. De waarde moet `3.0`zijn.</td>
  </tr>
  <tr>
    <td>scope</td>
    <td>*Optionele para meter*.<br/>Een door komma's gescheiden lijst met namen die de groep talen bepalen die moet worden geretourneerd. Toegestane groeps namen zijn: `translation`, `transliteration` en `dictionary`. Als er geen bereik is opgegeven, worden alle groepen geretourneerd, wat gelijk is aan het door geven van `scope=translation,transliteration,dictionary`. Zie de beschrijving van het [antwoord object](#response-body)om te bepalen welke set ondersteunde talen geschikt is voor uw scenario.</td>
  </tr>
</table> 

Aanvraag headers zijn:

<table width="100%">
  <th width="20%">Headers</th>
  <th>Beschrijving</th>
  <tr>
    <td>Accepteren-taal</td>
    <td>*Optionele aanvraag header*.<br/>De taal die moet worden gebruikt voor gebruikersinterfacetekenreeksen. Sommige velden in het antwoord zijn namen van talen of namen van regio's. Gebruik deze para meter om de taal te definiëren waarin deze namen worden geretourneerd. De taal wordt opgegeven door een juist opgemaakte BCP 47-taal code op te geven. Gebruik bijvoorbeeld de waarde `fr` om namen in het Frans aan te vragen of gebruik de waarde `zh-Hant` om namen aan te vragen in traditioneel Chinees.<br/>Namen worden in de Engelse taal weer gegeven wanneer er geen doel taal is opgegeven of wanneer lokalisatie niet beschikbaar is.
    </td>
  </tr>
  <tr>
    <td>X-ClientTraceId</td>
    <td>*Optionele aanvraag header*.<br/>Een door de client gegenereerde GUID om de aanvraag uniek te identificeren.</td>
  </tr>
</table> 

Verificatie is niet vereist om taal bronnen op te halen.

## <a name="response-body"></a>Antwoord tekst

Een client maakt gebruik van de para meter `scope` query om te bepalen van welke groepen talen deze is geïnteresseerd.

* `scope=translation` biedt talen die worden ondersteund om tekst van de ene taal te vertalen naar een andere taal.

* `scope=transliteration` biedt mogelijkheden voor het converteren van tekst in één taal van het ene script naar een ander script;

* `scope=dictionary` biedt taal paren waarvoor `Dictionary` bewerkingen gegevens retour neren.

Een client kan meerdere groepen tegelijkertijd ophalen door een door komma's gescheiden lijst met namen op te geven. `scope=translation,transliteration,dictionary` zou bijvoorbeeld ondersteunde talen voor alle groepen retour neren.

Een geslaagde reactie is een JSON-object met één eigenschap voor elke aangevraagde groep:

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

* `translation` eigenschap

  De waarde van de eigenschap `translation` is een woorden lijst van paren (sleutel, waarde). Elke sleutel is een BCP 47-taal code. Een sleutel geeft een taal aan waarvoor tekst kan worden vertaald of vertaald. De waarde die is gekoppeld aan de sleutel is een JSON-object met eigenschappen die de taal beschrijven:

  * `name`: weergave naam van de taal in de land instelling die via `Accept-Language` header is aangevraagd.

  * `nativeName`: weergave naam van de taal in de systeem eigen land instelling voor deze taal.

  * `dir`: Directionality, dat is `rtl` voor talen die van rechts naar links worden geschreven of `ltr` voor talen van links naar rechts.

  Een voor beeld is:
          
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

* `transliteration` eigenschap

  De waarde van de eigenschap `transliteration` is een woorden lijst van paren (sleutel, waarde). Elke sleutel is een BCP 47-taal code. Een sleutel geeft een taal aan waarvoor tekst kan worden geconverteerd van een script naar een ander script. De waarde die is gekoppeld aan de sleutel is een JSON-object met eigenschappen die de taal en de ondersteunde scripts beschrijven:

  * `name`: weergave naam van de taal in de land instelling die via `Accept-Language` header is aangevraagd.

  * `nativeName`: weergave naam van de taal in de systeem eigen land instelling voor deze taal.

  * `scripts`: lijst met scripts waaruit moet worden geconverteerd. Elk element van de `scripts` lijst heeft eigenschappen:

    * `code`: code waarmee het script wordt geïdentificeerd.

    * `name`: weergave naam van het script in de land instelling die via `Accept-Language` header is aangevraagd.

    * `nativeName`: weergave naam van de taal in de land instellingen systeem eigen voor de taal.

    * `dir`: Directionality, dat is `rtl` voor talen die van rechts naar links worden geschreven of `ltr` voor talen van links naar rechts.

    * `toScripts`: lijst met scripts die beschikbaar zijn voor het converteren van tekst naar. Elk element van de `toScripts` lijst heeft eigenschappen `code`, `name`, `nativeName`en `dir` zoals eerder beschreven.

  Een voor beeld is:

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

* `dictionary` eigenschap

  De waarde van de eigenschap `dictionary` is een woorden lijst van paren (sleutel, waarde). Elke sleutel is een BCP 47-taal code. De sleutel geeft een taal aan waarvoor alternatieve vertalingen en back-vertalingen beschikbaar zijn. De waarde is een JSON-object dat de bron taal en de doel talen beschrijft met beschik bare vertalingen:

  * `name`: weergave naam van de bron taal in de land instelling die via `Accept-Language` header is aangevraagd.

  * `nativeName`: weergave naam van de taal in de systeem eigen land instelling voor deze taal.

  * `dir`: Directionality, dat is `rtl` voor talen die van rechts naar links worden geschreven of `ltr` voor talen van links naar rechts.

  * `translations`: een lijst met talen met alterative-vertalingen en voor beelden voor de query, uitgedrukt in de taal van de bron. Elk element van de `translations` lijst heeft eigenschappen:

    * `name`: weergave naam van de doel taal in de land instelling die via `Accept-Language` header is aangevraagd.

    * `nativeName`: weergave naam van de doel taal in de land instellingen systeem eigen voor de doel taal.

    * `dir`: Directionality, dat is `rtl` voor talen die van rechts naar links worden geschreven of `ltr` voor talen van links naar rechts.
    
    * `code`: taal code die de doel taal aangeeft.

  Een voor beeld is:

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

De structuur van het antwoord object wordt niet gewijzigd zonder een wijziging in de versie van de API. Voor dezelfde versie van de API kan de lijst met beschik bare talen na verloop van tijd veranderen omdat micro soft Translator de lijst met talen die door de services worden ondersteund, doorlopend uitbreidt.

De lijst met ondersteunde talen wordt niet regel matig gewijzigd. Om de netwerk bandbreedte te besparen en de reactie tijd te verbeteren, moet een client toepassing overwegen om taal bronnen en de bijbehorende entiteits code (`ETag`) in de cache te plaatsen. Vervolgens kan de client toepassing periodiek (bijvoorbeeld elke 24 uur) een query uitvoeren op de service om de meest recente set ondersteunde talen op te halen. Door de huidige `ETag`-waarde door te geven in een `If-None-Match` koptekst veld, kan de service de reactie optimaliseren. Als de resource niet is gewijzigd, wordt de status code 304 en een lege antwoord tekst door de service geretourneerd.

## <a name="response-headers"></a>Antwoord headers

<table width="100%">
  <th width="20%">Headers</th>
  <th>Beschrijving</th>
  <tr>
    <td>eTag</td>
    <td>Huidige waarde van de entiteits code voor de aangevraagde groepen met ondersteunde talen. Om volgende aanvragen efficiënter te maken, kan de client de `ETag` waarde verzenden in een `If-None-Match` header-veld.
    </td>
  </tr>
  <tr>
    <td>X-aanvraag-}</td>
    <td>De waarde die door de service is gegenereerd om de aanvraag te identificeren. Dit wordt gebruikt voor het oplossen van problemen.</td>
  </tr>
</table> 

## <a name="response-status-codes"></a>Antwoord status codes

Hier volgen de mogelijke HTTP-status codes die een aanvraag retourneert. 

<table width="100%">
  <th width="20%">Status code</th>
  <th>Beschrijving</th>
  <tr>
    <td>200</td>
    <td>Geslaagd.</td>
  </tr>
  <tr>
    <td>304</td>
    <td>De resource is niet gewijzigd sinds de versie die is opgegeven door de aanvraag headers `If-None-Match`.</td>
  </tr>
  <tr>
    <td>400</td>
    <td>Een van de query parameters ontbreekt of is ongeldig. Corrigeer de aanvraag parameters voordat u het opnieuw probeert.</td>
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

In het volgende voor beeld ziet u hoe u talen kunt ophalen die worden ondersteund voor tekst vertaling.

```curl
curl "https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation"
```
