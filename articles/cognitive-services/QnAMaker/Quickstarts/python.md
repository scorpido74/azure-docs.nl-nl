---
title: REST-API (V4) - Python - QnA Maker
titleSuffix: Azure Cognitive Services
description: Get Python informatie en code-voorbeelden om u te helpen snel aan de slag met behulp van de Microsoft Translator Text-API in Microsoft Cognitive Services op Azure.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 09/05/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: db04dc6369fa6f66467efa64e5787d3f5ddb0b3c
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/06/2019
ms.locfileid: "70390879"
---
# <a name="how-to-use-the-qna-maker-rest-api-with-python"></a>De QnA Maker REST-API gebruiken met Python
<a name="HOLTop"></a>

In dit artikel leest u hoe u de [QnA Maker-API van Microsoft](../Overview/overview.md) met Python om het volgende te doen.

- [Een nieuwe knowledge base maken.](#Create)
- [Een bestaande knowledge base bijwerken.](#Update)
- [De status van een aanvraag ophalen om een knowledge base te maken of bij te werken.](#Status)
- [Een bestaande knowledge base publiceren.](#Publish)
- [De inhoud van een bestaande knowledge base vervangen.](#Replace)
- [De inhoud van een knowledge base downloaden.](#GetQnA)
- [Antwoorden vinden op vragen met behulp van een knowledge base.](#GetAnswers)
- [Informatie ophalen over een knowledge base.](#GetKB)
- [Informatie ophalen over alle knowledge bases van de opgegeven gebruiker.](#GetKBsByUser)
- [Een knowledge base verwijderen.](#Delete)
- [De huidige eindpuntsleutels ophalen.](#GetKeys)
- [De huidige eindpuntsleutels opnieuw genereren.](#PutKeys)
- [De huidige set hoofdletter gevoelige woord wijzigingen ophalen.](#GetAlterations)
- [De huidige set hoofdletter gevoelige woord wijzigingen vervangen.](#PutAlterations)

[!INCLUDE [Code is available in Azure-Samples GitHub repo](../../../../includes/cognitive-services-qnamaker-python-repo-note.md)]

## <a name="prerequisites"></a>Vereisten

U hebt [Python 3.x](https://www.python.org/downloads/) nodig om deze code uit te voeren.

U moet beschikken over een [account voor Cognitive Services-API](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) met **Microsoft QnA Maker-API**. U hebt een betaalde abonnementssleutel nodig op het [Azure-dashboard](https://portal.azure.com/#create/Microsoft.CognitiveServices).

<a name="Create"></a>

## <a name="create-knowledge-base"></a>Een Knowledge Base maken

Met de volgende code wordt een nieuwe knowledge base gemaakt met behulp van de methode [Create](https://go.microsoft.com/fwlink/?linkid=2092179).

1. Maak een nieuw Python-project in uw favoriete IDE.
2. Voeg de onderstaande code toe.
3. Vervang de waarde `key` door een geldige toegangssleutel voor uw abonnement.
4. Voer het programma uit.

```python
# -*- coding: utf-8 -*-

import http.client
import urllib.parse
import json
import time

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace this with a valid subscription key.
subscriptionKey = 'ENTER KEY HERE'

host = 'westus.api.cognitive.microsoft.com'
service = '/qnamaker/v4.0'
method = '/knowledgebases/create'


def pretty_print(content):
    # Note: We convert content to and from an object so we can pretty-print it.
    return json.dumps(json.loads(content), indent=4)


def create_kb(path, content):
    print('Calling ' + host + path + '.')
    headers = {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
        'Content-Type': 'application/json',
        'Content-Length': len(content)
    }
    conn = http.client.HTTPSConnection(host)
    conn.request("POST", path, content, headers)
    response = conn.getresponse()
# /knowledgebases/create returns an HTTP header named Location that contains a URL
# to check the status of the operation to create the knowledgebase.
    return response.getheader('Location'), response.read()


def check_status(path):
    print('Calling ' + host + path + '.')
    headers = {'Ocp-Apim-Subscription-Key': subscriptionKey}
    conn = http.client.HTTPSConnection(host)
    conn.request("GET", path, None, headers)
    response = conn.getresponse()
# If the operation is not finished, /operations returns an HTTP header named Retry-After
# that contains the number of seconds to wait before we query the operation again.
    return response.getheader('Retry-After'), response.read()


req = {
    "name": "QnA Maker FAQ",
    "qnaList": [
      {
          "id": 0,
          "answer": "You can use our REST APIs to manage your Knowledge Base. See here for details: https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update",
          "source": "Custom Editorial",
          "questions": [
              "How do I programmatically update my Knowledge Base?"
          ],
          "metadata": [
              {
                  "name": "category",
                  "value": "api"
              }
          ]
      }
    ],
    "urls": [
        "https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs",
        "https://docs.microsoft.com/bot-framework/resources-bot-framework-faq"
    ],
    "files": []
}

path = service + method
# Convert the request to a string.
content = json.dumps(req)
operation, result = create_kb(path, content)
print(pretty_print(result))

done = False
while False == done:
    path = service + operation
    wait, status = check_status(path)
    print(pretty_print(status))

# Convert the JSON response into an object and get the value of the operationState field.
    state = json.loads(status)['operationState']
# If the operation isn't finished, wait and query again.
    if state == 'Running' or state == 'NotStarted':
        print('Waiting ' + wait + ' seconds...')
        time.sleep(int(wait))
    else:
        done = True
```

**Antwoord van methode Create knowledge base**

Een geslaagd antwoord wordt geretourneerd in de JSON-indeling, zoals u kunt zien in het volgende voorbeeld: 

```json
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-04-13T01:52:30Z",
  "lastActionTimestamp": "2018-04-13T01:52:30Z",
  "userId": "2280ef5917bb4ebfa1aae41fb1cebb4a",
  "operationId": "e88b5b23-e9ab-47fe-87dd-3affc2fb10f3"
}
...
{
  "operationState": "Running",
  "createdTimestamp": "2018-04-13T01:52:30Z",
  "lastActionTimestamp": "2018-04-13T01:52:30Z",
  "userId": "2280ef5917bb4ebfa1aae41fb1cebb4a",
  "operationId": "e88b5b23-e9ab-47fe-87dd-3affc2fb10f3"
}
...
{
  "operationState": "Succeeded",
  "createdTimestamp": "2018-04-13T01:52:30Z",
  "lastActionTimestamp": "2018-04-13T01:52:46Z",
  "resourceLocation": "/knowledgebases/b0288f33-27b9-4258-a304-8b9f63427dad",
  "userId": "2280ef5917bb4ebfa1aae41fb1cebb4a",
  "operationId": "e88b5b23-e9ab-47fe-87dd-3affc2fb10f3"
}
```

[Terug naar boven](#HOLTop)

<a name="Update"></a>

## <a name="update-knowledge-base"></a>Knowledge base bijwerken

Met de volgende code wordt een bestaande knowledge base bijgewerkt met behulp van de methode [Update](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update).

1. Maak een nieuw Python-project in uw favoriete IDE.
2. Voeg de onderstaande code toe.
3. Vervang de waarde `key` door een geldige toegangssleutel voor uw abonnement.
4. Voer het programma uit.

```python
# -*- coding: utf-8 -*-

import http.client
import urllib.parse
import json
import time

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace this with a valid subscription key.
subscriptionKey = 'ENTER KEY HERE'

# Replace this with a valid knowledge base ID.
kb = 'ENTER ID HERE'

host = 'westus.api.cognitive.microsoft.com'
service = '/qnamaker/v4.0'
method = '/knowledgebases/'


def pretty_print(content):
    # Note: We convert content to and from an object so we can pretty-print it.
    return json.dumps(json.loads(content), indent=4)


def update_kb(path, content):
    print('Calling ' + host + path + '.')
    headers = {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
        'Content-Type': 'application/json',
        'Content-Length': len(content)
    }
    conn = http.client.HTTPSConnection(host)
    conn.request("PATCH", path, content, headers)
    response = conn.getresponse()
# PATCH /knowledgebases returns an HTTP header named Location that contains a URL
# to check the status of the operation to create the knowledgebase.
    return response.getheader('Location'), response.read()


def check_status(path):
    print('Calling ' + host + path + '.')
    headers = {'Ocp-Apim-Subscription-Key': subscriptionKey}
    conn = http.client.HTTPSConnection(host)
    conn.request("GET", path, None, headers)
    response = conn.getresponse()
# If the operation is not finished, /operations returns an HTTP header named Retry-After
# that contains the number of seconds to wait before we query the operation again.
    return response.getheader('Retry-After'), response.read()


req = {
    'add': {
        'qnaList': [
            {
                'id': 1,
                'answer': 'You can change the default message if you use the QnAMakerDialog. See this for details: https://docs.botframework.com/azure-bot-service/templates/qnamaker/#navtitle',
                'source': 'Custom Editorial',
                'questions': [
                    'How can I change the default message from QnA Maker?'
                ],
                'metadata': []
            }
        ],
        'urls': [
            'https://docs.microsoft.com/azure/cognitive-services/Emotion/FAQ'
        ]
    },
    'update': {
        'name': 'New KB Name'
    },
    'delete': {
        'ids': [
            0
        ]
    }
}

path = service + method + kb
# Convert the request to a string.
content = json.dumps(req)
operation, result = update_kb(path, content)
print(pretty_print(result))

done = False
while False == done:
    path = service + operation
    wait, status = check_status(path)
    print(pretty_print(status))

# Convert the JSON response into an object and get the value of the operationState field.
    state = json.loads(status)['operationState']
# If the operation isn't finished, wait and query again.
    if state == 'Running' or state == 'NotStarted':
        print('Waiting ' + wait + ' seconds...')
        time.sleep(int(wait))
    else:
        done = True
```

**Antwoord van methode Update knowledge base**

Een geslaagd antwoord wordt geretourneerd in de JSON-indeling, zoals u kunt zien in het volgende voorbeeld: 

```json
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-04-13T01:49:48Z",
  "lastActionTimestamp": "2018-04-13T01:49:48Z",
  "userId": "2280ef5917bb4ebfa1aae41fb1cebb4a",
  "operationId": "5156f64e-e31d-4638-ad7c-a2bdd7f41658"
}
...
{
  "operationState": "Succeeded",
  "createdTimestamp": "2018-04-13T01:49:48Z",
  "lastActionTimestamp": "2018-04-13T01:49:50Z",
  "resourceLocation": "/knowledgebases/140a46f3-b248-4f1b-9349-614bfd6e5563",
  "userId": "2280ef5917bb4ebfa1aae41fb1cebb4a",
  "operationId": "5156f64e-e31d-4638-ad7c-a2bdd7f41658"
}
Press any key to continue.
```

[Terug naar boven](#HOLTop)

<a name="Status"></a>

## <a name="get-request-status"></a>De aanvraagstatus ophalen

U kunt de methode [Operation](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/operations/getdetails) aanroepen om de status van een aanvraag voor het maken of bijwerken van een kennisdatabase te controleren. Bekijk de voorbeeldcode voor de methode [Create](#Create) of [Update](#Update) om te zien hoe deze methode wordt gebruikt.

[Terug naar boven](#HOLTop)

<a name="Publish"></a>

## <a name="publish-knowledge-base"></a>Knowledge base publiceren

Met de volgende code wordt een bestaande knowledge base gepubliceerd met behulp van de methode [Publish](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish).

1. Maak een nieuw Python-project in uw favoriete IDE.
2. Voeg de onderstaande code toe.
3. Vervang de waarde `key` door een geldige toegangssleutel voor uw abonnement.
4. Voer het programma uit.

```python
# -*- coding: utf-8 -*-

import http.client
import urllib.parse
import json
import time

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace this with a valid subscription key.
subscriptionKey = 'ENTER KEY HERE'

# Replace this with a valid knowledge base ID.
kb = 'ENTER ID HERE'

host = 'westus.api.cognitive.microsoft.com'
service = '/qnamaker/v4.0'
method = '/knowledgebases/'


def pretty_print(content):
    # Note: We convert content to and from an object so we can pretty-print it.
    return json.dumps(json.loads(content), indent=4)


def publish_kb(path, content):
    print('Calling ' + host + path + '.')
    headers = {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
        'Content-Type': 'application/json',
        'Content-Length': len(content)
    }
    conn = http.client.HTTPSConnection(host)
    conn.request("POST", path, content, headers)
    response = conn.getresponse()

    if response.status == 204:
        return json.dumps({'result': 'Success.'})
    else:
        return response.read()


path = service + method + kb
result = publish_kb(path, '')
print(pretty_print(result))
```

**Knowledge base-antwoord publiceren**

Een geslaagd antwoord wordt geretourneerd in de JSON-indeling, zoals u kunt zien in het volgende voorbeeld: 

```json
{
  "result": "Success."
}
```

[Terug naar boven](#HOLTop)

<a name="Replace"></a>

## <a name="replace-knowledge-base"></a>Knowledge base vervangen

Met de volgende code wordt de inhoud van de opgegeven knowledge base vervangen. Hierbij wordt gebruikgemaakt van de methode [Replace](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/replace).

1. Maak een nieuw Python-project in uw favoriete IDE.
2. Voeg de onderstaande code toe.
3. Vervang de waarde `key` door een geldige toegangssleutel voor uw abonnement.
4. Voer het programma uit.

```python
# -*- coding: utf-8 -*-

import http.client
import urllib.parse
import json
import time

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace this with a valid subscription key.
subscriptionKey = 'ENTER KEY HERE'

# Replace this with a valid knowledge base ID.
kb = 'ENTER ID HERE'

host = 'westus.api.cognitive.microsoft.com'
service = '/qnamaker/v4.0'
method = '/knowledgebases/'


def pretty_print(content):
    # Note: We convert content to and from an object so we can pretty-print it.
    return json.dumps(json.loads(content), indent=4)


def replace_kb(path, content):
    print('Calling ' + host + path + '.')
    headers = {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
        'Content-Type': 'application/json',
        'Content-Length': len(content)
    }
    conn = http.client.HTTPSConnection(host)
    conn.request("PUT", path, content, headers)
    response = conn.getresponse()

    if response.status == 204:
        return json.dumps({'result': 'Success.'})
    else:
        return response.read()


req = {
    'qnaList': [
      {
          'id': 0,
          'answer': 'You can use our REST APIs to manage your Knowledge Base. See here for details: https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update',
          'source': 'Custom Editorial',
          'questions': [
              'How do I programmatically update my Knowledge Base?'
          ],
          'metadata': [
              {
                  'name': 'category',
                  'value': 'api'
              }
          ]
      }
    ]
}

path = service + method + kb
# Convert the request to a string.
content = json.dumps(req)
result = replace_kb(path, content)
print(pretty_print(result))
```

**Antwoord van methode Replace knowledge base**

Een geslaagd antwoord wordt geretourneerd in de JSON-indeling, zoals u kunt zien in het volgende voorbeeld: 

```json
{
    "result": "Success."
}
```

[Terug naar boven](#HOLTop)

<a name="GetQnA"></a>

## <a name="download-the-contents-of-a-knowledge-base"></a>De inhoud van een knowledge base downloaden

Met de volgende code wordt de inhoud van de opgegeven knowledge base gedownload. Hierbij wordt gebruikgemaakt van de methode [Download knowledge base](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/download).

1. Maak een nieuw Python-project in uw favoriete IDE.
2. Voeg de onderstaande code toe.
3. Vervang de waarde `key` door een geldige toegangssleutel voor uw abonnement.
4. Voer het programma uit.

```python
# -*- coding: utf-8 -*-

import http.client
import urllib.parse
import json
import time

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace this with a valid subscription key.
subscriptionKey = 'ENTER KEY HERE'

# Replace this with a valid knowledge base ID.
kb = 'ENTER ID HERE'

# Replace this with "test" or "prod".
env = 'test'

host = 'westus.api.cognitive.microsoft.com'
service = '/qnamaker/v4.0'
method = '/knowledgebases/{0}/{1}/qna/'.format(kb, env)


def pretty_print(content):
    # Note: We convert content to and from an object so we can pretty-print it.
    return json.dumps(json.loads(content), indent=4)


def get_qna(path):
    print('Calling ' + host + path + '.')
    headers = {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
    }
    conn = http.client.HTTPSConnection(host)
    conn.request("GET", path, '', headers)
    response = conn.getresponse()
    return response.read()


path = service + method
result = get_qna(path)
print(pretty_print(result))
```

**Antwoord van methode Download knowledge base**

Een geslaagd antwoord wordt geretourneerd in de JSON-indeling, zoals u kunt zien in het volgende voorbeeld: 

```json
{
  "qnaDocuments": [
    {
      "id": 1,
      "answer": "You can use our REST APIs to manage your Knowledge Base. See here for details: https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update",
      "source": "Custom Editorial",
      "questions": [
        "How do I programmatically update my Knowledge Base?"
      ],
      "metadata": [
        {
          "name": "category",
          "value": "api"
        }
      ]
    },
    {
      "id": 2,
      "answer": "QnA Maker provides an FAQ data source that you can query from your bot or application. Although developers will find this useful, content owners will especially benefit from this tool. QnA Maker is a completely no-code way of managing the content that powers your bot or application.",
      "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs",
      "questions": [
        "Who is the target audience for the QnA Maker tool?"
      ],
      "metadata": []
    },
...
  ]
}
```

[Terug naar boven](#HOLTop)

<a name="GetAnswers"></a>

## <a name="get-answers-to-a-question-using-a-knowledge-base"></a>Antwoorden vinden op vragen met behulp van een knowledge base

Met de volgende code worden antwoorden op een vraag opgehaald uit de opgegeven knowledge base. Hiervoor wordt gebruikgemaakt van de methode **Generate answers**.

1. Maak een nieuw Python-project in uw favoriete IDE.
1. Voeg de onderstaande code toe.
1. Vervang de waarde `host` door de websitenaam voor uw QnA Maker-abonnement. Zie [Een QnA Maker-service maken](../How-To/set-up-qnamaker-service-azure.md) voor meer informatie.
1. Vervang de waarde `endpoint_key` door een geldige eindpuntsleutel voor uw abonnement. Deze sleutel is niet hetzelfde als uw abonnementssleutel. U kunt uw eindpuntsleutels ophalen met de methode [Get endpoint keys](#GetKeys).
1. Vervang de waarde `kb` door de id van de knowledge base waaruit u antwoorden wilt ophalen. Deze knowledge base moet al zijn gepubliceerd met behulp van de methode [Publish](#Publish).
1. Voer het programma uit.

```python
# -*- coding: utf-8 -*-

import http.client
import urllib.parse
import json
import time

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# NOTE: Replace this with a valid host name.
host = "ENTER HOST HERE"

# NOTE: Replace this with a valid endpoint key.
# This is not your subscription key.
# To get your endpoint keys, call the GET /endpointkeys method.
endpoint_key = "ENTER KEY HERE"

# NOTE: Replace this with a valid knowledge base ID.
# Make sure you have published the knowledge base with the
# POST /knowledgebases/{knowledge base ID} method.
kb = "ENTER KB ID HERE"

method = "/qnamaker/knowledgebases/" + kb + "/generateAnswer"

question = {
    'question': 'Is the QnA Maker Service free?',
    'top': 3
}


def pretty_print(content):
    # Note: We convert content to and from an object so we can pretty-print it.
    return json.dumps(json.loads(content), indent=4)


def get_answers(path, content):
    print('Calling ' + host + path + '.')
    headers = {
        'Authorization': 'EndpointKey ' + endpoint_key,
        'Content-Type': 'application/json',
        'Content-Length': len(content)
    }
    conn = http.client.HTTPSConnection(host)
    conn.request("POST", path, content, headers)
    response = conn.getresponse()
    return response.read()


# Convert the request to a string.
content = json.dumps(question)
result = get_answers(method, content)
print(pretty_print(result))
```

**Antwoord van methode Get answers**

Een geslaagd antwoord wordt geretourneerd in de JSON-indeling, zoals u kunt zien in het volgende voorbeeld: 

```json
{
  "answers": [
    {
      "questions": [
        "Can I use BitLocker with the Volume Shadow Copy Service?"
      ],
      "answer": "Yes. However, shadow copies made prior to enabling BitLocker will be automatically deleted when BitLocker is enabled on software-encrypted drives. If you are using a hardware encrypted drive, the shadow copies are retained.",
      "score": 17.3,
      "id": 62,
      "source": "https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-frequently-asked-questions",
      "metadata": []
    },
...
  ]
}
```

[Terug naar boven](#HOLTop)

<a name="GetKB"></a>

## <a name="get-information-about-a-knowledge-base"></a>Informatie ophalen over een knowledge base

Met de volgende code wordt informatie over de opgegeven knowledge base opgehaald. Hiervoor wordt gebruikgemaakt van de methode [Get knowledge base details](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/getdetails).

1. Maak een nieuw Python-project in uw favoriete IDE.
2. Voeg de onderstaande code toe.
3. Vervang de waarde `key` door een geldige toegangssleutel voor uw abonnement.
4. Voer het programma uit.

```python
# -*- coding: utf-8 -*-

import http.client
import urllib.parse
import json
import time

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace this with a valid subscription key.
subscriptionKey = 'ENTER KEY HERE'

# Replace this with a valid knowledge base ID.
kb = 'ENTER ID HERE'

host = 'westus.api.cognitive.microsoft.com'
service = '/qnamaker/v4.0'
method = '/knowledgebases/'


def pretty_print(content):
    # Note: We convert content to and from an object so we can pretty-print it.
    return json.dumps(json.loads(content), indent=4)


def get_kb(path):
    print('Calling ' + host + path + '.')
    headers = {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
    }
    conn = http.client.HTTPSConnection(host)
    conn.request("GET", path, '', headers)
    response = conn.getresponse()
    return response.read()


path = service + method + kb
result = get_kb(path)
print(pretty_print(result))
```

**Antwoord van methode Get knowledge base Details**

Een geslaagd antwoord wordt geretourneerd in de JSON-indeling, zoals u kunt zien in het volgende voorbeeld: 

```json
{
  "id": "140a46f3-b248-4f1b-9349-614bfd6e5563",
  "hostName": "https://qna-docs.azurewebsites.net",
  "lastAccessedTimestamp": "2018-04-12T22:58:01Z",
  "lastChangedTimestamp": "2018-04-12T22:58:01Z",
  "name": "QnA Maker FAQ",
  "userId": "2280ef5917bb4ebfa1aae41fb1cebb4a",
  "urls": [
    "https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs",
    "https://docs.microsoft.com/bot-framework/resources-bot-framework-faq"
  ],
  "sources": [
    "Custom Editorial"
  ]
}
```

[Terug naar boven](#HOLTop)

<a name="GetKBsByUser"></a>

## <a name="get-all-knowledge-bases-for-a-user"></a>Alle knowledge bases van een gebruiker ophalen

Met de volgende code wordt informatie over alle knowledge bases van een opgegeven gebruiker opgehaald. Hierbij wordt gebruikgemaakt van de methode [Get knowledge bases for user](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/listall).

1. Maak een nieuw Python-project in uw favoriete IDE.
2. Voeg de onderstaande code toe.
3. Vervang de waarde `key` door een geldige toegangssleutel voor uw abonnement.
4. Voer het programma uit.

```python
# -*- coding: utf-8 -*-

import http.client
import urllib.parse
import json
import time

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace this with a valid subscription key.
subscriptionKey = 'ENTER KEY HERE'

host = 'westus.api.cognitive.microsoft.com'
service = '/qnamaker/v4.0'
method = '/knowledgebases/'


def pretty_print(content):
    # Note: We convert content to and from an object so we can pretty-print it.
    return json.dumps(json.loads(content), indent=4)


def get_kbs(path):
    print('Calling ' + host + path + '.')
    headers = {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
    }
    conn = http.client.HTTPSConnection(host)
    conn.request("GET", path, '', headers)
    response = conn.getresponse()
    return response.read()


path = service + method
result = get_kbs(path)
print(pretty_print(result))
```

**Antwoord Get knowledge bases for user**

Een geslaagd antwoord wordt geretourneerd in de JSON-indeling, zoals u kunt zien in het volgende voorbeeld: 

```json
{
  "knowledgebases": [
    {
      "id": "081c32a7-bd05-4982-9d74-07ac736df0ac",
      "hostName": "https://qna-docs.azurewebsites.net",
      "lastAccessedTimestamp": "2018-04-12T11:51:58Z",
      "lastChangedTimestamp": "2018-04-12T11:51:58Z",
      "name": "QnA Maker FAQ",
      "userId": "2280ef5917bb4ebfa1aae41fb1cebb4a",
      "urls": [],
      "sources": []
    },
    {
      "id": "140a46f3-b248-4f1b-9349-614bfd6e5563",
      "hostName": "https://qna-docs.azurewebsites.net",
      "lastAccessedTimestamp": "2018-04-12T22:58:01Z",
      "lastChangedTimestamp": "2018-04-12T22:58:01Z",
      "name": "QnA Maker FAQ",
      "userId": "2280ef5917bb4ebfa1aae41fb1cebb4a",
      "urls": [
        "https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs",
        "https://docs.microsoft.com/bot-framework/resources-bot-framework-faq"
      ],
      "sources": [
        "Custom Editorial"
      ]
    },
...
  ]
}
Press any key to continue.
```

[Terug naar boven](#HOLTop)

<a name="Delete"></a>

## <a name="delete-a-knowledge-base"></a>Een knowledge base verwijderen

Met de volgende code wordt de opgegeven knowledge base verwijderd. Hierbij wordt gebruikgemaakt van de methode [Delete knowledge base](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/delete).

1. Maak een nieuw Python-project in uw favoriete IDE.
2. Voeg de onderstaande code toe.
3. Vervang de waarde `key` door een geldige toegangssleutel voor uw abonnement.
4. Voer het programma uit.

```python
# -*- coding: utf-8 -*-

import http.client
import urllib.parse
import json
import time

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace this with a valid subscription key.
subscriptionKey = 'ENTER KEY HERE'

# Replace this with a valid knowledge base ID.
kb = 'ENTER ID HERE'

host = 'westus.api.cognitive.microsoft.com'
service = '/qnamaker/v4.0'
method = '/knowledgebases/'


def pretty_print(content):
    # Note: We convert content to and from an object so we can pretty-print it.
    return json.dumps(json.loads(content), indent=4)


def delete_kb(path, content):
    print('Calling ' + host + path + '.')
    headers = {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
        'Content-Type': 'application/json',
        'Content-Length': len(content)
    }
    conn = http.client.HTTPSConnection(host)
    conn.request("DELETE", path, content, headers)
    response = conn.getresponse()

    if response.status == 204:
        return json.dumps({'result': 'Success.'})
    else:
        return response.read()


path = service + method + kb
result = delete_kb(path, '')
print(pretty_print(result))
```

**Antwoord van methode Delete knowledge base**

Een geslaagd antwoord wordt geretourneerd in de JSON-indeling, zoals u kunt zien in het volgende voorbeeld: 

```json
{
  "result": "Success."
}
```

[Terug naar boven](#HOLTop)

<a name="GetKeys"></a>

## <a name="get-endpoint-keys"></a>Eindpuntsleutels ophalen

Met de volgende code worden de huidige eindpuntsleutels opgehaald. Hierbij wordt gebruikgemaakt van de methode [Get endpoint keys](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/endpointkeys/getkeys).

1. Maak een nieuw Python-project in uw favoriete IDE.
2. Voeg de onderstaande code toe.
3. Vervang de waarde `key` door een geldige toegangssleutel voor uw abonnement.
4. Voer het programma uit.

```python
# -*- coding: utf-8 -*-

import http.client
import urllib.parse
import json
import time

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace this with a valid subscription key.
subscriptionKey = 'ENTER KEY HERE'

host = 'westus.api.cognitive.microsoft.com'
service = '/qnamaker/v4.0'
method = '/endpointkeys/'


def pretty_print(content):
    # Note: We convert content to and from an object so we can pretty-print it.
    return json.dumps(json.loads(content), indent=4)


def get_keys(path):
    print('Calling ' + host + path + '.')
    headers = {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
    }
    conn = http.client.HTTPSConnection(host)
    conn.request("GET", path, '', headers)
    response = conn.getresponse()
    return response.read()


path = service + method
result = get_keys(path)
print(pretty_print(result))
```

**Antwoord van methode Get endpoint keys**

Een geslaagd antwoord wordt geretourneerd in de JSON-indeling, zoals u kunt zien in het volgende voorbeeld: 

```json
{
  "primaryEndpointKey": "ac110bdc-34b7-4b1c-b9cd-b05f9a6001f3",
  "secondaryEndpointKey": "1b4ed14e-614f-444a-9f3d-9347f45a9206"
}
```

[Terug naar boven](#HOLTop)

<a name="PutKeys"></a>

## <a name="refresh-endpoint-keys"></a>Eindpuntsleutels vernieuwen

Met de volgende code worden de huidige eindpuntsleutels opnieuw gegenereerd. Hierbij wordt gebruikgemaakt van de methode [Refresh endpoint keys](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/endpointkeys/refreshkeys).

1. Maak een nieuw Python-project in uw favoriete IDE.
2. Voeg de onderstaande code toe.
3. Vervang de waarde `key` door een geldige toegangssleutel voor uw abonnement.
4. Voer het programma uit.

```python
# -*- coding: utf-8 -*-

import http.client
import urllib.parse
import json
import time

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace this with a valid subscription key.
subscriptionKey = 'ENTER KEY HERE'

# Replace this with "PrimaryKey" or "SecondaryKey."
key_type = "PrimaryKey"

host = 'westus.api.cognitive.microsoft.com'
service = '/qnamaker/v4.0'
method = '/endpointkeys/'


def pretty_print(content):
    # Note: We convert content to and from an object so we can pretty-print it.
    return json.dumps(json.loads(content), indent=4)


def refresh_keys(path, content):
    print('Calling ' + host + path + '.')
    headers = {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
        'Content-Type': 'application/json',
        'Content-Length': len(content)
    }
    conn = http.client.HTTPSConnection(host)
    conn.request("PATCH", path, content, headers)
    response = conn.getresponse()

    if response.status == 204:
        return json.dumps({'result': 'Success.'})
    else:
        return response.read()


path = service + method + key_type
result = refresh_keys(path, '')
print(pretty_print(result))
```

**Antwoord van methode Refresh endpoint keys**

Een geslaagd antwoord wordt geretourneerd in de JSON-indeling, zoals u kunt zien in het volgende voorbeeld: 

```json
{
  "primaryEndpointKey": "ac110bdc-34b7-4b1c-b9cd-b05f9a6001f3",
  "secondaryEndpointKey": "1b4ed14e-614f-444a-9f3d-9347f45a9206"
}
```

[Terug naar boven](#HOLTop)

<a name="GetAlterations"></a>

## <a name="get-word-alterations"></a>Woordwijzigingen ophalen

Met de volgende code worden de huidige woordwijzigingen opgehaald. Hierbij wordt gebruikgemaakt van de methode [Download alterations](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/alterations/get).

1. Maak een nieuw Python-project in uw favoriete IDE.
2. Voeg de onderstaande code toe.
3. Vervang de waarde `key` door een geldige toegangssleutel voor uw abonnement.
4. Voer het programma uit.

```python
# -*- coding: utf-8 -*-

import http.client
import urllib.parse
import json
import time

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace this with a valid subscription key.
subscriptionKey = 'ENTER KEY HERE'

host = 'westus.api.cognitive.microsoft.com'
service = '/qnamaker/v4.0'
method = '/alterations/'


def pretty_print(content):
    # Note: We convert content to and from an object so we can pretty-print it.
    return json.dumps(json.loads(content), indent=4)


def get_alterations(path):
    print('Calling ' + host + path + '.')
    headers = {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
    }
    conn = http.client.HTTPSConnection(host)
    conn.request("GET", path, '', headers)
    response = conn.getresponse()
    return response.read()


path = service + method
result = get_alterations(path)
print(pretty_print(result))
```

**Antwoord van methode Get word alterations**

Een geslaagd antwoord wordt geretourneerd in de JSON-indeling, zoals u kunt zien in het volgende voorbeeld: 

```json
{
  "wordAlterations": [
    {
      "alterations": [
        "botframework",
        "bot frame work"
      ]
    }
  ]
}
```

[Terug naar boven](#HOLTop)

<a name="PutAlterations"></a>

## <a name="replace-word-alterations"></a>Woordwijzigingen vervangen

Met de volgende code worden de huidige woordwijzigingen vervangen. Hierbij wordt gebruikgemaakt van de methode [Replace alterations](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/alterations/replace).

1. Maak een nieuw Python-project in uw favoriete IDE.
2. Voeg de onderstaande code toe.
3. Vervang de waarde `key` door een geldige toegangssleutel voor uw abonnement.
4. Voer het programma uit.

```python
# -*- coding: utf-8 -*-

import http.client
import urllib.parse
import json
import time

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace this with a valid subscription key.
subscriptionKey = 'ENTER KEY HERE'

host = 'westus.api.cognitive.microsoft.com'
service = '/qnamaker/v4.0'
method = '/alterations/'


def pretty_print(content):
    # Note: We convert content to and from an object so we can pretty-print it.
    return json.dumps(json.loads(content), indent=4)


def put_alterations(path, content):
    print('Calling ' + host + path + '.')
    headers = {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
        'Content-Type': 'application/json',
        'Content-Length': len(content)
    }
    conn = http.client.HTTPSConnection(host)
    conn.request("PUT", path, content, headers)
    response = conn.getresponse()

    if response.status == 204:
        return json.dumps({'result': 'Success.'})
    else:
        return response.read()


req = {
    'wordAlterations': [
      {
          'alterations': [
              'botframework',
              'bot frame work'
          ]
      }
    ]
}

path = service + method
# Convert the request to a string.
content = json.dumps(req)
result = put_alterations(path, content)
print(pretty_print(result))
```

**Antwoord van methode Replace word alterations**

Een geslaagd antwoord wordt geretourneerd in de JSON-indeling, zoals u kunt zien in het volgende voorbeeld: 

```json
{
  "result": "Success."
}
```

[Terug naar boven](#HOLTop)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Naslaginformatie over REST API voor QnA Maker (V4)](https://go.microsoft.com/fwlink/?linkid=2092179)

## <a name="see-also"></a>Zie ook 

[Overzicht van QnA Maker](../Overview/overview.md)
