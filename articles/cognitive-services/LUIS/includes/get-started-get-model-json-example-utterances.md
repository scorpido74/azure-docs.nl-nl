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
ms.date: 10/19/2019
ms.author: diberry
ms.openlocfilehash: ba400beb13d0bd4b29eee0c60617ec7ad128dd77
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73503736"
---
Het voor beeld uitingen volgt een specifieke indeling. 

Het veld `text` bevat de tekst van de voorbeeldutterance. Het veld `intentName` moet overeenkomen met de naam van een bestaande intentie in de LUIS-app. Het veld `entityLabels` is vereist. Als u niet alle entiteiten een label wilt geven, geeft u een lege matrix op.

Als de entityLabels-matrix niet leeg is, moeten `startCharIndex` en `endCharIndex` de entiteit markeren waarnaar wordt verwezen in het veld `entityName`. De index is nul, wat betekent dat 6 in het bovenste voorbeeld verwijst naar de "S" van Seattle en niet naar de spatie vóór de hoofdletter S. Als u het label begint of eindigt bij een spatie in de tekst, mislukt de API-aanroep om de utterances toe te voegen.

```JSON
[
  {
    "text": "go to Seattle today",
    "intentName": "BookFlight",
    "entityLabels": [
      {
        "entityName": "Location::LocationTo",
        "startCharIndex": 6,
        "endCharIndex": 12
      }
    ]
  },
  {
    "text": "purple dogs are difficult to work with",
    "intentName": "BookFlight",
    "entityLabels": []
  }
]
```
