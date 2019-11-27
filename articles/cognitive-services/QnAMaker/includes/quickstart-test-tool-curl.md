---
title: bestand opnemen
description: bestand opnemen
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.custom: include file
ms.date: 11/20/2019
ms.author: diberry
ms.openlocfilehash: 0677a361e853f778894b6a62a054636e3276b364
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/23/2019
ms.locfileid: "74424410"
---
In deze op krul gebaseerde Snelstartgids wordt u begeleid bij het verkrijgen van een antwoord uit uw Knowledge Base.

## <a name="prerequisites"></a>Vereisten

* Meest recente [**krul**](https://curl.haxx.se/).
* U moet over een [QnA Maker-service](../How-To/set-up-qnamaker-service-azure.md) en een [knowledge base met vragen en antwoorden](../Tutorials/create-publish-query-in-portal.md) beschikken.

## <a name="publish-to-get-endpoint"></a>Publiceren om eindpunten te verkrijgen

Wanneer u klaar bent voor het genereren van een antwoord op een vraag uit uw knowledge base, [publiceert](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base) u uw knowledge base.

## <a name="use-production-endpoint-with-curl"></a>Productie-eind punt met krul gebruiken

Wanneer uw knowledge base is gepubliceerd, geeft de pagina **Publiceren** de instellingen van de HTTP-aanvraag weer voor het genereren van een antwoord. Het tabblad **krul** bevat de instellingen die nodig zijn om een antwoord te genereren vanuit het opdracht regel programma, [krul](https://www.getpostman.com).

[![Resultaten publiceren](../media/qnamaker-use-to-generate-answer/curl-command-on-publish-page.png)](../media/qnamaker-use-to-generate-answer/curl-command-on-publish-page.png#lightbox)

Als u een antwoord met krul wilt genereren, voert u de volgende stappen uit:

1. Kopieer de tekst op het tabblad krul. 
1. Open een opdracht regel of Terminal en plak de tekst.
1. Bewerk de vraag die relevant is voor uw Knowledge Base. Zorg ervoor dat u de bovenliggende JSON rond de vraag niet verwijdert.
1. Voer de opdracht in. 
1. Het antwoord bevat de relevante informatie over het antwoord. 

    ```bash
    > curl -X POST https://qnamaker-f0.azurewebsites.net/qnamaker/knowledgebases/1111f8c-d01b-4698-a2de-85b0dbf3358c/generateAnswer -H "Authorization: EndpointKey 111841fb-c208-4a72-9412-03b6f3e55ca1" -H "Content-type: application/json" -d "{'question':'How do I programmatically update my Knowledge Base?'}"
    {
      "answers": [
        {
          "questions": [
            "How do I programmatically update my Knowledge Base?"
          ],
          "answer": "You can use our REST APIs to manage your Knowledge Base. See here for details: https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update",
          "score": 100.0,
          "id": 18,
          "source": "Custom Editorial",
          "metadata": [
            {
              "name": "category",
              "value": "api"
            }
          ]
        }
      ]
    }
    ```

## <a name="use-staging-endpoint-with-curl"></a>Faseringseindpunt gebruiken met cURL

Als u een antwoord wilt ontvangen van het staging-eind punt, gebruikt u de eigenschap `isTest` body.

```json
isTest:true
```


