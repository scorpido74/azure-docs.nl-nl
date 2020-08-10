---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.custom: devx-track-javascript
ms.openlocfilehash: dfca8bb6f897f532081df7d141112ddbb190d109
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/29/2020
ms.locfileid: "87406538"
---
[!INCLUDE [Prerequisites](prerequisites-nodejs.md)]

[!INCLUDE [Setup and use environment variables](setup-env-variables.md)]

## <a name="create-a-project-and-import-required-modules"></a>Een project maken en de vereiste modules importeren

Maak een nieuw project met uw favoriete IDE of editor. Kopieer dit codefragment naar uw project in een bestand met de naam `detect.js`.

```javascript
const request = require('request');
const uuidv4 = require('uuid/v4');
```

> [!NOTE]
> Als u deze modules nog niet hebt gebruikt, moet u ze installeren voordat u het programma uitvoert. Voer voor het installeren van deze pakketten voert u `npm install request uuidv4` uit.

Deze modules zijn vereist om de HTTP-aanvraag te maken en om een unieke id voor de `'X-ClientTraceId'`-header te maken.

## <a name="set-the-subscription-key-and-endpoint"></a>Abonnementssleutel en eindpunt instellen

In dit voorbeeld wordt geprobeerd de abonnementssleutel en het eindpunt van Translator te lezen vanuit de omgevingsvariabelen `TRANSLATOR_TEXT_SUBSCRIPTION_KEY` en `TRANSLATOR_TEXT_ENDPOINT`. Als u niet bekend bent met omgevingsvariabelen, kunt u `subscriptionKey` en `endpoint` als tekenreeks instellen en een opmerking plaatsen in de voorwaardelijke instructies.

Kopieer deze code naar uw project:

```javascript
var key_var = 'TRANSLATOR_TEXT_SUBSCRIPTION_KEY';
if (!process.env[key_var]) {
    throw new Error('Please set/export the following environment variable: ' + key_var);
}
var subscriptionKey = process.env[key_var];
var endpoint_var = 'TRANSLATOR_TEXT_ENDPOINT';
if (!process.env[endpoint_var]) {
    throw new Error('Please set/export the following environment variable: ' + endpoint_var);
}
var endpoint = process.env[endpoint_var];
```

## <a name="configure-the-request"></a>Aanvraag configureren

Met de methode `request()`, beschikbaar gesteld via de aanvraagmodule, kunt u de HTTP-methode, URL, aanvraagparameters, headers en de JSON-hoofdtekst doorgeven als een `options`-object. In dit codefragment configureert u de aanvraag:

```javascript
let options = {
    method: 'POST',
    baseUrl: endpoint,
    url: 'detect',
    qs: {
      'api-version': '3.0',
    },
    headers: {
      'Ocp-Apim-Subscription-Key': subscriptionKey,
      'Content-type': 'application/json',
      'X-ClientTraceId': uuidv4().toString()
    },
    body: [{
          'text': 'Salve, mondo!'
    }],
    json: true,
};
```
U kunt aanvragen het eenvoudigst verifiëren door uw abonnementssleutel op te geven als `Ocp-Apim-Subscription-Key`-header. Dat doen we in dit voorbeeld dan ook. Als alternatief kunt u in plaats van uw abonnementssleutel een toegangstoken gebruiken en het toegangstoken opgeven als `Authorization`-header voor het valideren van uw aanvraag.

Als u een Cognitive Services-abonnement voor meerdere services gebruikt, moet u ook de `Ocp-Apim-Subscription-Region` in uw aanvraagheaders toevoegen.

Zie [Verificatie](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication) voor meer informatie.

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
node detect.js
```

## <a name="sample-response"></a>Voorbeeldantwoord

Nadat u het voorbeeld hebt uitgevoerd, wordt het volgende uitgevoerd naar de terminal:

> [!NOTE]
> Zoek de afkorting van het land/de regio in deze [lijst met talen](https://docs.microsoft.com/azure/cognitive-services/translator/language-support).

```json
[
    {
        "alternatives": [
            {
                "isTranslationSupported": true,
                "isTransliterationSupported": false,
                "language": "pt",
                "score": 1.0
            },
            {
                "isTranslationSupported": true,
                "isTransliterationSupported": false,
                "language": "en",
                "score": 1.0
            }
        ],
        "isTranslationSupported": true,
        "isTransliterationSupported": false,
        "language": "it",
        "score": 1.0
    }
]
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u uw abonnementssleutel hebt vastgelegd in het programma, verwijdert u deze sleutel wanneer u klaar bent met de snelstart.

## <a name="next-steps"></a>Volgende stappen

Bekijk de API-verwijzing voor meer informatie over wat u met de Translator kunt doen.

> [!div class="nextstepaction"]
> [API-naslaginformatie](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
