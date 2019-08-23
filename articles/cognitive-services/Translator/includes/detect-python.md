---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: 9207814c921f51b10939c6e9d1747e1e124f9890
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69907199"
---
[!INCLUDE [Prerequisites](prerequisites-python.md)]

[!INCLUDE [Set up and use environment variables](setup-env-variables.md)]

## <a name="create-a-project-and-import-required-modules"></a>Een project maken en de vereiste modules importeren

Maak een nieuw Python-project met uw favoriete IDE of editor. Kopieer dit codefragment naar uw project in een bestand met de naam `detect.py`.

```python
# -*- coding: utf-8 -*-
import os, requests, uuid, json
```

> [!NOTE]
> Als u deze modules nog niet hebt gebruikt, moet u ze installeren voordat u het programma uitvoert. Voer voor het installeren van deze pakketten voert u `pip install requests uuid` uit.

Met de eerste opmerking laat u de Python-vertaler weten dat UTF-8-codering moet worden gebruikt. De vereiste modules worden dan geïmporteerd voor het lezen van uw abonnementssleutel uit een omgevingsvariabele, voor het opstellen van de HTTP-aanvraag, voor het maken van een unieke id en voor het verwerken van het JSON-antwoord dat door de Translator Text-API wordt geretourneerd.

## <a name="set-the-subscription-key-endpoint-and-path"></a>De abonnements sleutel, het eind punt en het pad instellen

In dit voor beeld wordt geprobeerd uw Translator text-abonnements sleutel en-eind punt te lezen `TRANSLATOR_TEXT_KEY` uit `TRANSLATOR_TEXT_ENDPOINT`de omgevings variabelen: en. Als u niet bekend bent met omgevings variabelen, kunt u `subscription_key` en `endpoint` als teken reeksen instellen en de voorwaardelijke instructies van commentaar voorzien.

Kopieer deze code naar uw project:

```python
key_var_name = 'TRANSLATOR_TEXT_SUBSCRIPTION_KEY'
if not key_var_name in os.environ:
    raise Exception('Please set/export the environment variable: {}'.format(key_var_name))
subscription_key = os.environ[key_var_name]

endpoint_var_name = 'TRANSLATOR_TEXT_ENDPOINT'
if not endpoint_var_name in os.environ:
    raise Exception('Please set/export the environment variable: {}'.format(endpoint_var_name))
endpoint = os.environ[endpoint_var_name]
```

Het globaal eindpunt voor de Translator Text is ingesteld als de `endpoint`. Met `path` wordt de `detect`-route ingesteld en wordt bepaald dat we versie 3 van de API willen gebruiken.

>[!NOTE]
> Meer informatie over eindpunten, routes en aanvraagparameters vindt u in [Translator Text-API 3.0: Detecteren](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-detect).

```python
path = '/detect?api-version=3.0'
constructed_url = endpoint + path
```

## <a name="add-headers"></a>Headers toevoegen

U kunt aanvragen het eenvoudigst verifiëren door uw abonnementssleutel op te geven als `Ocp-Apim-Subscription-Key`-header. Dat doen we in dit voorbeeld dan ook. Als alternatief kunt u in plaats van uw abonnementssleutel een toegangstoken gebruiken en het toegangstoken opgeven als `Authorization`-header voor het valideren van uw aanvraag. Zie [Verificatie](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication) voor meer informatie.

Kopieer dit codefragment naar uw project:

```python
headers = {
    'Ocp-Apim-Subscription-Key': subscription_key,
    'Content-type': 'application/json',
    'X-ClientTraceId': str(uuid.uuid4())
}
```

Als u een Cognitive Services abonnement op meerdere services gebruikt, moet u ook de `Ocp-Apim-Subscription-Region` in uw aanvraag parameters toevoegen. Meer [informatie over verificatie met het multi-service-abonnement](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

## <a name="create-a-request-to-detect-text-language"></a>Een aanvraag maken om teksttaal te detecteren

Definieer de tekenreeks (of tekenreeksen) waarvan u de taal wilt detecteren:

```python
# You can pass more than one object in body.
body = [{
    'text': 'Salve, mondo!'
}]
```

Vervolgens maakt u een POST-aanvraag met de `requests`-module. Hier zijn drie argumenten voor nodig: de samengevoegde URL, de aanvraagheaders en de aanvraagbody:

```python
request = requests.post(constructed_url, headers=headers, json=body)
response = request.json()
```

## <a name="print-the-response"></a>Het antwoord weergeven

De laatste stap is het weergeven van de antwoorden. Met dit codefragment worden de resultaten verfraaid: de sleutels worden gesorteerd, er wordt gebruikgemaakt van inspringing en er worden item- en sleutelscheidingstekens opgegeven.

```python
print(json.dumps(response, sort_keys=True, indent=4,
                 ensure_ascii=False, separators=(',', ': ')))
```

## <a name="put-it-all-together"></a>Alles samenvoegen

Dat was het. U hebt een eenvoudig programma gemaakt dat we de Translator Text-API zullen noemen. Er is een JSON-antwoord geretourneerd. Het is nu tijd om uw programma uit te voeren:

```console
python detect.py
```

Als u uw code graag wilt vergelijken met de onze, kunt u het volledige voorbeeld vinden op [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Python).

## <a name="sample-response"></a>Voorbeeldantwoord

Zoek de afkorting van het land/de regio in deze [lijst met talen](https://docs.microsoft.com/azure/cognitive-services/translator/language-support).

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

Bekijk de API-verwijzing voor meer informatie over wat u met de Translator Text-API kunt doen.

> [!div class="nextstepaction"]
> [API-naslaginformatie](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
