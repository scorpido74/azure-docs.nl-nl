---
title: bestand opnemen
description: bestand opnemen
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.date: 02/14/2020
ms.subservice: language-understanding
ms.topic: include
ms.custom: include file
ms.author: diberry
ms.openlocfilehash: e879afdbd4c34e9d74405644de86421fb2cbab46
ms.sourcegitcommit: 79cbd20a86cd6f516acc3912d973aef7bf8c66e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2020
ms.locfileid: "77279700"
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
