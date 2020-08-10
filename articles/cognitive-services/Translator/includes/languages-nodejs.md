---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.custom: devx-track-javascript
ms.openlocfilehash: 1f2159f3f4c88c7ddafab63a7780f4dbf41ca130
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/29/2020
ms.locfileid: "87405417"
---
[!INCLUDE [Prerequisites](prerequisites-nodejs.md)]

[!INCLUDE [Set up and use environment variables](setup-env-variables.md)]

## <a name="create-a-project-and-import-required-modules"></a>Een project maken en de vereiste modules importeren

Maak een nieuw project met uw favoriete IDE of editor. Kopieer dit codefragment naar uw project in een bestand met de naam `get-languages.js`.

```javascript
const request = require('request');
const uuidv4 = require('uuid/v4');
```

> [!NOTE]
> Als u deze modules nog niet hebt gebruikt, moet u ze installeren voordat u het programma uitvoert. Voer voor het installeren van deze pakketten voert u `npm install request uuidv4` uit.

Deze modules zijn vereist om de HTTP-aanvraag te maken en om een unieke id voor de `'X-ClientTraceId'`-header te maken.

## <a name="set-the-endpoint"></a>Het eindpunt instellen

In dit voorbeeld wordt geprobeerd om uw Translator-eindpunt te lezen vanuit de omgevingsvariabele: `TRANSLATOR_TEXT_ENDPOINT`. Als u niet bekend bent met omgevingsvariabelen, kunt u `endpoint` als tekenreeks instellen en een opmerking plaatsen in de voorwaardelijke instructie.

```javascript
lorum ipsum
```

## <a name="configure-the-request"></a>Aanvraag configureren

Met de methode `request()`, beschikbaar gesteld via de aanvraagmodule, kunt u de HTTP-methode, URL, aanvraagparameters, headers en de JSON-hoofdtekst doorgeven als een `options`-object. In dit codefragment configureert u de aanvraag:

>[!NOTE]
> Meer informatie over eindpunten, routes en aanvraagparameters vindt u in [Translator 3.0: talen](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-languages).

```javascript
let options = {
    method: 'GET',
    baseUrl: endpoint,
    url: 'languages',
    qs: {
      'api-version': '3.0',
    },
    headers: {
      'Content-type': 'application/json',
      'X-ClientTraceId': uuidv4().toString()
    },
    json: true,
};
```

Als u een Cognitive Services-abonnement voor meerdere services gebruikt, moet u ook de `Ocp-Apim-Subscription-Region` in uw aanvraagparameters toevoegen. [Meer informatie over verificatie met het abonnement voor meerdere services](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

## <a name="make-the-request-and-print-the-response"></a>De aanvraag maken en het antwoord afdrukken

Vervolgens maakt u de aanvraag via de methode `request()`. Deze gebruikt het `options`-object dat in de vorige sectie als eerste argument is gemaakt, waarna het opgemaakte JSON-antwoord wordt afgedrukt.

```javascript
request(options, function(err, res, body){
    console.log(JSON.stringify(body, null, 4));
});
```

>[!NOTE]
> In dit voorbeeld definieert u de HTTP-aanvraag in het `options`-object. De aanvraagmodule ondersteunt echter ook helpermethoden, zoals `.post` en `.get`. Zie [Helpermethoden](https://github.com/request/request#convenience-methods) voor meer informatie.

## <a name="put-it-all-together"></a>Alles samenvoegen

Dat was het. U hebt een eenvoudig programma gemaakt dat we Translator zullen noemen. Er is een JSON-antwoord geretourneerd. Het is nu tijd om uw programma uit te voeren:

```console
node get-languages.js
```

Als u uw code graag wilt vergelijken met de onze, kunt u het volledige voorbeeld vinden op [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-NodeJS).

## <a name="sample-response"></a>Voorbeeldantwoord

Zoek de afkorting van het land/de regio in deze [lijst met talen](https://docs.microsoft.com/azure/cognitive-services/translator/language-support).

Dit voorbeeld is ingekort en u ziet een fragment van het resultaat:

```json
{
    "translation": {
        "af": {
            "name": "Afrikaans",
            "nativeName": "Afrikaans",
            "dir": "ltr"
        },
        "ar": {
            "name": "Arabic",
            "nativeName": "العربية",
            "dir": "rtl"
        },
        ...
    },
    "transliteration": {
        "ar": {
            "name": "Arabic",
            "nativeName": "العربية",
            "scripts": [
                {
                    "code": "Arab",
                    "name": "Arabic",
                    "nativeName": "العربية",
                    "dir": "rtl",
                    "toScripts": [
                        {
                            "code": "Latn",
                            "name": "Latin",
                            "nativeName": "اللاتينية",
                            "dir": "ltr"
                        }
                    ]
                },
                {
                    "code": "Latn",
                    "name": "Latin",
                    "nativeName": "اللاتينية",
                    "dir": "ltr",
                    "toScripts": [
                        {
                            "code": "Arab",
                            "name": "Arabic",
                            "nativeName": "العربية",
                            "dir": "rtl"
                        }
                    ]
                }
            ]
        },
      ...
    },
    "dictionary": {
        "af": {
            "name": "Afrikaans",
            "nativeName": "Afrikaans",
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
        "ar": {
            "name": "Arabic",
            "nativeName": "العربية",
            "dir": "rtl",
            "translations": [
                {
                    "name": "English",
                    "nativeName": "English",
                    "dir": "ltr",
                    "code": "en"
                }
            ]
        },
      ...
    }
}
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u uw abonnementssleutel hebt vastgelegd in het programma, verwijdert u deze sleutel wanneer u klaar bent met de snelstart.

## <a name="next-steps"></a>Volgende stappen

Bekijk de API-verwijzing voor meer informatie over wat u met de Translator kunt doen.

> [!div class="nextstepaction"]
> [API-naslaginformatie](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
