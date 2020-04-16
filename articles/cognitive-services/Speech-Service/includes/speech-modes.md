---
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 01/22/2020
ms.author: trbye
ms.openlocfilehash: d97a1bf00d722414bedda4be79adc0c26d02c751
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81421919"
---
## <a name="speech-modes"></a>Spraakmodi

**Interactieve**
- Bedoeld voor commando- en controlescenario's.
- Heeft een segmentatie time-out waarde van X.
- Aan het einde van een herkende utterance stopt de service met het verwerken van audio van die aanvraag-id en beëindigt de beurt. De verbinding is niet gesloten.
- De maximale limiet voor erkenning is 20s.
- Typische Carbon oproep `RecognizeOnceAsync`aan te roepen is .

**Gesprek**
- Bedoeld voor langere lopende erkenningen.
- Heeft een segmentatie time out waarde van Y. (Y != X)
- Zal meerdere volledige uitingen verwerken zonder de beurt te beëindigen.
- Zal de beurt beëindigen voor te veel stilte.
- Carbon gaat verder met een nieuwe aanvraag-ID en speelt audio opnieuw af als dat nodig is.
- De service zal met geweld de verbinding verbreken na 10 minuten spraakherkenning.
- Carbon zal opnieuw verbinding maken en niet-erkende audio opnieuw afspelen.
- Aangeroepen in `StartContinuousRecognition`Carbon met .

**Dicteren**
- Hiermee kunnen gebruikers interpunctie opgeven door het te spreken.
- InGeroepen in Carbon `EnableDictation` door `SpeechConfig` op te geven op het object, ongeacht de API-aanroep die de herkenning start.
- Het<sup>cluster</sup> van `speech.fragment` de 1 e party retourneert `speech.hypothesis` berichten voor tussentijdse resultaten, de<sup>3rd party</sup> retourberichten.