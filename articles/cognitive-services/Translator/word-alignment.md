---
title: Uitlijning van woorden-Translator Text-API
titleSuffix: Azure Cognitive Services
description: Als u informatie over uitlijning wilt ontvangen, gebruikt u de methode vertalen en neemt u de optionele para meter includeAlignment op.
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "73837233"
---
# <a name="how-to-receive-word-alignment-information"></a>Informatie over het afstemmen van een woord ontvangen

## <a name="receiving-word-alignment-information"></a>Informatie over het afstemmen van woorden ontvangen
Als u informatie over uitlijning wilt ontvangen, gebruikt u de methode vertalen en neemt u de optionele para meter includeAlignment op.

## <a name="alignment-information-format"></a>Indeling van uitlijning gegevens
Uitlijning wordt geretourneerd als een teken reeks waarde van de volgende notatie voor elk woord van de bron. De gegevens voor elk woord worden gescheiden door een spatie, met inbegrip van talen die niet met een spatie zijn gescheiden (scripts), zoals Chinees:

[[SourceTextStartIndex]:[SourceTextEndIndex]–[TgtTextStartIndex]:[TgtTextEndIndex]] *

Voor beeld van een uitlijnings teken reeks: "0:0-7:10 1:2-11:20 3:4-0:3 3:4-4:6 5:5-21:21".

Met andere woorden, de dubbele punt scheidt begin-en eind index, het streepje scheidt de talen en de spaties scheidt de woorden. Eén woord kan worden uitgelijnd met nul, één of meerdere woorden in de andere taal en de uitgelijnde woorden kunnen niet-aaneengesloten zijn. Als er geen uitlijnings gegevens beschikbaar zijn, is het element uitlijning leeg. De methode retourneert in dat geval geen fout.

## <a name="restrictions"></a>Beperkingen
Uitlijning wordt alleen geretourneerd voor een subset van de taal paren op dit punt:
* van het Engels naar een andere taal;
* van een andere taal in het Engels, met uitzonde ring van vereenvoudigd Chinees, traditioneel Chinees en Lets in het Engels
* van Japans naar Koreaans of van Koreaans tot Japans u krijgt geen uitlijnings informatie als de zin een ingeblikte vertaling is. Voor beeld van een ' ingevoegde ' vertaling is ' Dit is een test ', ' Ik hou toe ' en andere hoge frequentie zinnen.

## <a name="example"></a>Voorbeeld

Voor beeld JSON

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
