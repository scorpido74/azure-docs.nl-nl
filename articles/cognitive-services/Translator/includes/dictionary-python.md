---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: d52c4ba9bc4fdb5a696b43aa2b8358992182c553
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83586951"
---
[!INCLUDE [Prerequisites](prerequisites-python.md)]

[!INCLUDE [Set up and use environment variables](setup-env-variables.md)]

## <a name="create-a-project-and-import-required-modules"></a>Een project maken en de vereiste modules importeren

Maak een nieuw python-project met uw favoriete IDE of editor, of maak een nieuwe map op uw bureau blad. Kopieer dit code fragment naar het project/de map in een bestand met de naam `dictionary-lookup.py` .

```python
# -*- coding: utf-8 -*-
import os, requests, uuid, json
```

> [!NOTE]
> Als u deze modules nog niet hebt gebruikt, moet u ze installeren voordat u het programma uitvoert. Voer voor het installeren van deze pakketten voert u `pip install requests uuid` uit.

Met de eerste opmerking laat u de Python-vertaler weten dat UTF-8-codering moet worden gebruikt. Vervolgens worden de vereiste modules geïmporteerd om uw abonnements sleutel van een omgevings variabele te lezen, de HTTP-aanvraag te maken, een unieke id te creëren en het JSON-antwoord te verwerken dat door de vertaler is geretourneerd.

## <a name="set-the-subscription-key-endpoint-and-path"></a>De abonnements sleutel, het eind punt en het pad instellen

In dit voor beeld wordt geprobeerd om de sleutel van het Vertaal abonnement en het eind punt te lezen uit de omgevings variabelen: `TRANSLATOR_TEXT_KEY` en `TRANSLATOR_TEXT_ENDPOINT` . Als u niet bekend bent met omgevings variabelen, kunt u `subscription_key` en `endpoint` als teken reeksen instellen en de voorwaardelijke instructies van commentaar voorzien.

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

Het globale eind punt van het conversie programma is ingesteld als `endpoint` . Met `path` wordt de `dictionary/lookup`-route ingesteld en wordt bepaald dat we versie 3 van de API willen gebruiken.

De `params` worden gebruikt voor het instellen van de bron- en uitvoertalen. In dit voorbeeld wordt Engels en Spaans gebruikt: `en` en `es`.

>[!NOTE]
> Zie [Translator 3,0: Dictionary Lookup](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-dictionary-lookup)(Engelstalig) voor meer informatie over eind punten, routes en aanvraag parameters.

```python
path = '/dictionary/lookup?api-version=3.0'
params = '&from=en&to=es'
constructed_url = endpoint + path + params
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

## <a name="create-a-request-to-find-alternate-translations"></a>Een aanvraag maken om alternatieve vertalingen te zoeken

Definieer de tekenreeks (of tekenreeksen) die u wilt laten vertalen:

```python
# You can pass more than one object in body.
body = [{
    'text': 'Elephants'
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

Dat is alles. u hebt een eenvoudig programma samengesteld waarmee het conversie programma wordt aangeroepen en een JSON-antwoord wordt geretourneerd. Het is nu tijd om uw programma uit te voeren:

```console
python alt-translations.py
```

Als u uw code graag wilt vergelijken met de onze, kunt u het volledige voorbeeld vinden op [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Python).

## <a name="sample-response"></a>Voorbeeldantwoord

```json
[
    {
        "displaySource": "elephants",
        "normalizedSource": "elephants",
        "translations": [
            {
                "backTranslations": [
                    {
                        "displayText": "elephants",
                        "frequencyCount": 1207,
                        "normalizedText": "elephants",
                        "numExamples": 5
                    }
                ],
                "confidence": 1.0,
                "displayTarget": "elefantes",
                "normalizedTarget": "elefantes",
                "posTag": "NOUN",
                "prefixWord": ""
            }
        ]
    }
]
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u uw abonnementssleutel hebt vastgelegd in het programma, verwijdert u deze sleutel wanneer u klaar bent met de snelstart.

## <a name="next-steps"></a>Volgende stappen

Bekijk de API-verwijzing voor meer informatie over wat u met de vertaler kunt doen.

> [!div class="nextstepaction"]
> [API-verwijzing](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
