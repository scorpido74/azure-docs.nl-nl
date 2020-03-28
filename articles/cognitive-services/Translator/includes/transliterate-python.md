---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: 86ef8f3730fe7ae3ab3428956aaafb86331c5cf5
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "69906512"
---
[!INCLUDE [Prerequisites](prerequisites-python.md)]

[!INCLUDE [Set up and use environment variables](setup-env-variables.md)]

## <a name="create-a-project-and-import-required-modules"></a>Een project maken en de vereiste modules importeren

Maak een nieuw project met uw favoriete IDE of editor `transliterate-text.py` of een nieuwe map met een bestand met de naam op uw bureaublad. Kopieer dit codefragment vervolgens naar uw project/bestand:

```python
# -*- coding: utf-8 -*-
import os, requests, uuid, json
```

> [!NOTE]
> Als u deze modules nog niet hebt gebruikt, moet u ze installeren voordat u het programma uitvoert. Voer voor het installeren van deze pakketten voert u `pip install requests uuid` uit.

Met de eerste opmerking laat u de Python-vertaler weten dat UTF-8-codering moet worden gebruikt. De vereiste modules worden dan geïmporteerd voor het lezen van uw abonnementssleutel uit een omgevingsvariabele, voor het opstellen van de HTTP-aanvraag, voor het maken van een unieke id en voor het verwerken van het JSON-antwoord dat door de Translator Text-API wordt geretourneerd.

## <a name="set-the-subscription-key-endpoint-and-path"></a>De abonnementssleutel, het eindpunt en het pad instellen

In dit voorbeeld wordt geprobeerd uw abonnementssleutel voor vertalerstekst `TRANSLATOR_TEXT_KEY` en `TRANSLATOR_TEXT_ENDPOINT`eindpunt te lezen uit de omgevingsvariabelen: en . Als u niet bekend bent met omgevingsvariabelen, u instellen `subscription_key` en `endpoint` als een tekenreeks en commentaar op de voorwaardelijke instructies.

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

Het globaal eindpunt voor de Translator Text is ingesteld als de `endpoint`. Met `path` wordt de `transliterate`-route ingesteld en wordt bepaald dat we versie 3 van de API willen gebruiken.

De `params` worden gebruikt om de invoertaal, en het invoer- en uitvoerscript in te stellen. In dit voorbeeld is sprake van transliteratie van Japans naar het Latijnse alfabet.

>[!NOTE]
> Zie [Translator Text-API 3.0: Omzetten](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-transliterate) voor meer informatie over eindpunten, routes en aanvraagparameters.

```python
path = '/transliterate?api-version=3.0'
params = '&language=ja&fromScript=jpan&toScript=latn'
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

Als u een multiserviceabonnement voor Cognitive Services gebruikt, moet u ook de `Ocp-Apim-Subscription-Region` parameters voor uw aanvraag opnemen. [Meer informatie over authenticeren met het multiservice-abonnement](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

## <a name="create-a-request-to-transliterate-text"></a>Een aanvraag maken voor de transliteratie van tekst

Definieer de tekenreeks (of tekenreeksen) die u wilt translitereren:

```python
# Transliterate "good afternoon" from source Japanese.
# Note: You can pass more than one object in body.
body = [{
    'text': 'こんにちは'
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
python transliterate-text.py
```

Als u uw code graag wilt vergelijken met de onze, kunt u het volledige voorbeeld vinden op [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Python).

## <a name="sample-response"></a>Voorbeeldantwoord

```json
[
    {
        "script": "latn",
        "text": "konnichiwa"
    }
]
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u uw abonnementssleutel hebt vastgelegd in het programma, verwijdert u deze sleutel wanneer u klaar bent met de snelstart.

## <a name="next-steps"></a>Volgende stappen

Bekijk de API-verwijzing om alles te begrijpen wat u doen met de Translator Text API.

> [!div class="nextstepaction"]
> [API-verwijzing](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
