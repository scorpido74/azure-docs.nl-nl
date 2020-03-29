---
title: Word-uitlijning - Api voor vertalertekst
titleSuffix: Azure Cognitive Services
description: Als u uitlijningsinformatie wilt ontvangen, gebruikt u de methode Vertalen en neemt u de optionele parameter includeAlignment op.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.custom: seodec18
ms.openlocfilehash: dd4ff1e39c062910f4627973c801dc3c51f345e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73837233"
---
# <a name="how-to-receive-word-alignment-information"></a>Informatie over woorduitlijning ontvangen

## <a name="receiving-word-alignment-information"></a>Informatie over woorduitlijning ontvangen
Als u uitlijningsinformatie wilt ontvangen, gebruikt u de methode Vertalen en neemt u de optionele parameter includeAlignment op.

## <a name="alignment-information-format"></a>Indelingsinformatie-indeling
Uitlijning wordt geretourneerd als een tekenreekswaarde van de volgende notatie voor elk woord van de bron. De informatie voor elk woord wordt gescheiden door een ruimte, ook voor niet-ruimte-gescheiden talen (scripts) zoals Chinees:

[[SourceTextStartIndex]:[SourceTextEndIndex]–[TgtTextStartIndex]:[TgtTextEndIndex]] *

Voorbeelduitlijningstekenreeks: "0:0-7:10 1:2-11:20 3:4-0:3 3:4-4:6 5:5-21:21".

Met andere woorden, de dubbele punt scheidt begin- en eindindex, het streepje scheidt de talen en de ruimte scheidt de woorden. Een woord kan worden uitgelijnd met nul, een of meerdere woorden in de andere taal, en de uitgelijnde woorden kunnen niet-aaneengesloten zijn. Wanneer er geen uitlijningsinformatie beschikbaar is, is het element Uitlijning leeg. De methode geeft geen fout in dat geval.

## <a name="restrictions"></a>Beperkingen
Uitlijning wordt op dit punt alleen geretourneerd voor een subset van de taalparen:
* van Engels naar een andere taal;
* van elke andere taal naar het Engels, behalve voor Chinees vereenvoudigd, Chinees traditioneel en Lets naar Engels
* van Japans naar Koreaans of van Koreaans naar Japans U ontvangt geen uitlijningsinformatie als de zin een ingeblikte vertaling is. Voorbeeld van een ingeblikte vertaling is "Dit is een test", "Ik hou van je", en andere hoge frequentie zinnen.

## <a name="example"></a>Voorbeeld

Voorbeeld JSON

```json
[
  {
    "translations": [
      {
        "text": "Kann ich morgen Ihr Auto fahren?",
        "to": "de",
        "alignment": {
          "proj": "0:2-0:3 4:4-5:7 6:10-25:30 12:15-16:18 17:19-20:23 21:28-9:14 29:29-31:31"
        }
      }
    ]
  }
]
```
