---
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 01/22/2020
ms.author: trbye
ms.openlocfilehash: d97a1bf00d722414bedda4be79adc0c26d02c751
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81421919"
---
## <a name="speech-modes"></a>Spraak modi

**SBSI**
- Bedoeld voor opdracht-en controle scenario's.
- Heeft een time-outwaarde van X.
- Aan het einde van een herkende utterance stopt de service de verwerking van de audio van die aanvraag-ID en eindigt de beurt. De verbinding is niet gesloten.
- De maximum limiet voor herkenning is 20s.
- Typische Carbon-aanroep naar invoke `RecognizeOnceAsync`is.

**Gesprek**
- Bedoeld om meer herkennings handelingen uit te voeren.
- Heeft een time-outwaarde voor de segmentatie van Y. (Y! = X)
- Er worden meerdere volledige uitingen verwerkt zonder de beurt te beëindigen.
- Beëindigt de turn voor te veel stilte.
- Carbon gaat door met een nieuwe aanvraag-ID en de audio opnieuw af te spelen als dat nodig is.
- De verbinding met de service wordt na 10 minuten van spraak herkenning afgedwongen.
- Kool wordt opnieuw verbonden en niet-bevestigde audio opnieuw afgespeeld.
- Aangeroepen in carbon with `StartContinuousRecognition`.

**Dicteren**
- Hiermee kunnen gebruikers interpunctie opgeven door het te spreken.
- Wordt aangeroepen in kooldioxyde door op `EnableDictation` het `SpeechConfig` object op te geven, ongeacht de API-aanroep die de herkenning start.
- De 1<sup>St</sup> partij-cluster `speech.fragment` retourneert berichten voor tussenliggende resultaten, de<sup>drie retour</sup> `speech.hypothesis` berichten van de derde partij.