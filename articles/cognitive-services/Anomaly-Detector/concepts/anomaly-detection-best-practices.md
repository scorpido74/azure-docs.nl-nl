---
title: Best practices bij het gebruik van de Anomaly Detector-API
titleSuffix: Azure Cognitive Services
description: Meer informatie over aanbevolen procedures bij het detecteren van afwijkingen met de Api voor anomaliedetector.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: aahi
ms.openlocfilehash: 9407f2fc9375765efb6eb9688b3ebfeef24ba90a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67721622"
---
# <a name="best-practices-for-using-the-anomaly-detector-api"></a>Aanbevolen procedures voor het gebruik van de API voor anomaliedetector

De Anomaly Detector API is een stateless anomalie detectie service. De nauwkeurigheid en prestaties van de resultaten kunnen worden beïnvloed door:

* Hoe uw tijdreeksgegevens worden voorbereid.
* De Api-parameters van de AnomalieDetector die zijn gebruikt.
* Het aantal gegevenspunten in uw API-aanvraag. 

Gebruik dit artikel voor meer informatie over aanbevolen procedures voor het gebruik van de API om de beste resultaten voor uw gegevens te verkrijgen. 

## <a name="when-to-use-batch-entire-or-latest-last-point-anomaly-detection"></a>Wanneer batch (volledige) of laatste (laatste) puntanomaliedetectie te gebruiken

Met het batchdetectie-eindpunt van de Anomalie Detector API u afwijkingen detecteren via uw gegevens uit de gehele tijdreeks. In deze detectiemodus wordt één statistisch model gemaakt en toegepast op elk punt in de gegevensset. Als uw tijdreeks de onderstaande kenmerken heeft, raden we u aan batchdetectie te gebruiken om uw gegevens in één API-aanroep te bekijken.

* Een seizoensgebonden tijdreeks, met af en toe afwijkingen.
* Een vlakke trend tijdreeks, met af en toe spikes/dips. 

We raden u af om batchanomaliedetectie te gebruiken voor realtime gegevensbewaking of deze te gebruiken op tijdreeksgegevens die niet boven kenmerken staan. 

* Batchdetectie maakt en past slechts één model toe, de detectie voor elk punt wordt gedaan in de context van hele reeksen. Als de tijdreeksgegevenstrends op en neer zonder seizoensgebondenheid, kunnen sommige punten van verandering (dips en pieken in de gegevens) worden gemist door het model. Evenzo kunnen sommige punten van verandering die minder belangrijk zijn dan die later in de gegevensset niet worden geteld als belangrijk genoeg om in het model te worden opgenomen.

* Batchdetectie is trager dan het detecteren van de anomaliestatus van het laatste punt bij het doen van realtime gegevensbewaking, vanwege het aantal punten dat wordt geanalyseerd.

Voor real-time gegevensbewaking raden we u aan alleen de anomaliestatus van uw laatste gegevenspunt te detecteren. Door de nieuwste puntdetectie continu toe te passen, kan het monitoren van streaming data efficiënter en nauwkeuriger worden uitgevoerd.

In het onderstaande voorbeeld wordt beschreven welke invloed deze detectiemodi kunnen hebben op de prestaties. De eerste foto toont het resultaat van het continu detecteren van de anomalie status laatste punt langs 28 eerder gezien datapunten. De rode punten zijn anomalieën.

![Een afbeelding met anomaliedetectie met behulp van het laatste punt](../media/last.png)

Hieronder is dezelfde gegevensset met behulp van batch anomalie detectie. Het model dat voor de bewerking is gemaakt, heeft verschillende afwijkingen genegeerd, gemarkeerd door rechthoeken.

![Een afbeelding met anomaliedetectie met behulp van de batchmethode](../media/entire.png)

## <a name="data-preparation"></a>Gegevensvoorbereiding

De Api Anomaly Detector accepteert tijdreeksgegevens die zijn opgemaakt in een JSON-aanvraagobject. Een tijdreeks kan alle numerieke gegevens zijn die in de loop van de tijd in opeenvolgende volgorde worden geregistreerd. U vensters van uw tijdreeksgegevens naar het API-eindpunt van de Anomalie Detector verzenden om de prestaties van de API te verbeteren. Het minimum aantal gegevenspunten dat u verzenden is 12 en het maximum is 8640 punten. [Granulariteit](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.granularity?view=azure-dotnet-preview) wordt gedefinieerd als de snelheid waarmee uw gegevens worden bemonsterd. 

Gegevenspunten die naar de API voor anomaliedetector worden verzonden, moeten een geldige gecoördineerde universele tijdstempel (UTC) en numerieke waarde hebben. 

```json
{
    "granularity": "daily",
    "series": [
      {
        "timestamp": "2018-03-01T00:00:00Z",
        "value": 32858923
      },
      {
        "timestamp": "2018-03-02T00:00:00Z",
        "value": 29615278
      },
    ]
}
```

Als uw gegevens worden bemonsterd met een niet-standaard tijdsinterval, u deze opgeven door het `customInterval` kenmerk in uw aanvraag toe te voegen. Als uw serie bijvoorbeeld elke 5 minuten wordt bemonsterd, u het volgende toevoegen aan uw JSON-aanvraag:

```json
{
    "granularity" : "minutely", 
    "customInterval" : 5
}
```

### <a name="missing-data-points"></a>Ontbrekende gegevenspunten

Ontbrekende gegevenspunten komen vaak voor in gelijkmatig verdeelde tijdreeksgegevenssets, vooral gegevenssets met een fijne granulariteit (een klein bemonsteringsinterval. Bijvoorbeeld, gegevens bemonsterd om de paar minuten). Het missen van minder dan 10% van het verwachte aantal punten in uw gegevens mag geen negatieve invloed hebben op uw detectieresultaten. Overweeg hiaten in uw gegevens op te vullen op basis van de kenmerken ervan, zoals het vervangen van gegevenspunten uit een eerdere periode, lineaire interpolatie of een voortschrijdend gemiddelde.

### <a name="aggregate-distributed-data"></a>Gedistribueerde gegevens samenvoegen

De Anomaly Detector API werkt het beste op een gelijkmatig verdeelde tijdreeks. Als uw gegevens willekeurig worden gedistribueerd, moet u deze samenvoegen met een tijdseenheid, zoals per minuut, per uur of dagelijks.

## <a name="anomaly-detection-on-data-with-seasonal-patterns"></a>Anomaliedetectie op gegevens met seizoenspatronen

Als u weet dat uw tijdreeksgegevens een seizoensgebonden patroon hebben (een gegevens die regelmatig plaatsvinden), u de nauwkeurigheid en api-responstijd verbeteren. 

Als u `period` een JSON-aanvraag opgeeft, kan de latentie van anomaliedetectie met maximaal 50% worden verminderd. Het `period` is een geheel getal dat ongeveer aangeeft hoeveel gegevens punten de tijdreeks en duurt om een patroon te herhalen. Bijvoorbeeld, een tijdreeks met een datapunt `period` per `7`dag zou hebben een als , en een tijdreeks `period` met `7*24`een punt per uur (met hetzelfde wekelijkse patroon) zou hebben een van . Als u niet zeker bent van de patronen van uw gegevens, hoeft u deze parameter niet op te geven.

Voor de beste `period`resultaten, bieden 4 's waard van de gegevens punt, plus een extra. Bijvoorbeeld, uurgegevens met een wekelijks patroon zoals hierboven beschreven moeten 673`7 * 24 * 4 + 1`gegevenspunten in de aanvraaginstantie () bevatten.

### <a name="sampling-data-for-real-time-monitoring"></a>Bemonsteringsgegevens voor realtime monitoring

Als uw streaminggegevens met een korte interval (bijvoorbeeld seconden of minuten) worden bemonsterd, kan het verzenden van het aanbevolen aantal gegevenspunten het maximaal toegestane aantal van de Anomalie Detector API (8640 gegevenspunten) overschrijden. Als uw gegevens een stabiel seizoenspatroon weergeven, u overwegen een voorbeeld van uw tijdreeksgegevens te verzenden met een groter tijdsinterval, zoals uren. Het op deze manier bemonsteren van uw gegevens kan ook de API-responstijd merkbaar verbeteren. 

## <a name="next-steps"></a>Volgende stappen

* [Wat is de Anomaly Detector-API?](../overview.md)
* [Snelstart: afwijkingen in uw tijdreeksgegevens detecteren met behulp van de Anomalie Detector REST API](../quickstarts/detect-data-anomalies-csharp.md)
