---
title: De Api voor anomaliedetector gebruiken voor uw tijdreeksgegevens
titleSuffix: Azure Cognitive Services
description: Meer informatie over het detecteren van afwijkingen in uw gegevens als batch of op streaminggegevens.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: aahi
ms.openlocfilehash: ca93de71f64efaf21c78b37b9c9aee193d13b28d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "71840228"
---
# <a name="how-to-use-the-anomaly-detector-api-on-your-time-series-data"></a>How to: Gebruik de Anomaly Detector API op uw tijdreeksgegevens  

De [Anomaly Detector API](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect) biedt twee methoden voor anomaliedetectie. U afwijkingen detecteren als een batch in uw tijdreeks, of als uw gegevens worden gegenereerd door het detecteren van de anomaliestatus van het nieuwste gegevenspunt. Het detectiemodel retourneert anomalieresultaten, samen met de verwachte waarde van elk gegevenspunt en de bovenste en onderste anomaliedetectiegrenzen. u deze waarden gebruiken om het bereik van normale waarden en afwijkingen in de gegevens te visualiseren.

## <a name="anomaly-detection-modes"></a>Anomaliedetectiemodi 

De Anomaly Detector API biedt detectiemodi: batch en streaming.

> [!NOTE]
> De volgende aanvraag-URL's moeten worden gecombineerd met het juiste eindpunt voor uw abonnement. Bijvoorbeeld: `https://<your-custom-subdomain>.api.cognitive.microsoft.com/anomalydetector/v1.0/timeseries/entire/detect`


### <a name="batch-detection"></a>Batchdetectie

Als u afwijkingen in een batch gegevenspunten over een bepaald tijdsbereik wilt detecteren, gebruikt u de volgende aanvraag-URI met uw tijdreeksgegevens: 

`/timeseries/entire/detect`. 

Door uw tijdreeksgegevens in één keer te verzenden, genereert de API een model met de hele reeks en analyseert u elk gegevenspunt ermee.  

### <a name="streaming-detection"></a>Streamingdetectie

Als u afwijkingen op streaminggegevens continu wilt detecteren, gebruikt u de volgende aanvraag URI met uw nieuwste gegevenspunt: 

`/timeseries/last/detect'`. 

Door nieuwe gegevenspunten te verzenden terwijl u ze genereert, u uw gegevens in realtime controleren. Er wordt een model gegenereerd met de gegevenspunten die u verzendt en de API bepaalt of het laatste punt in de tijdreeks een anomalie is.

## <a name="adjusting-lower-and-upper-anomaly-detection-boundaries"></a>De detectiegrenzen van de onderste en bovenste anomalie aanpassen

Standaard worden de boven- en ondergrenzen `expectedValue`voor `upperMargin`anomaliedetectie berekend met behulp van , en `lowerMargin`. Als u andere grenzen nodig hebt, `upperMargin` `lowerMargin`raden we u aan een `marginScale` to of . De grenzen worden als volgt berekend:

|Grens  |Berekening  |
|---------|---------|
|`upperBoundary` | `expectedValue + (100 - marginScale) * upperMargin`        |
|`lowerBoundary` | `expectedValue - (100 - marginScale) * lowerMargin`        |

De volgende voorbeelden tonen een Api-resultaat van de Anomalie Detector bij verschillende gevoeligheden.

### <a name="example-with-sensitivity-at-99"></a>Voorbeeld met gevoeligheid op 99

![Standaardgevoeligheid](../media/sensitivity_99.png)

### <a name="example-with-sensitivity-at-95"></a>Voorbeeld met gevoeligheid op 95

![Gevoeligheid 99](../media/sensitivity_95.png)

### <a name="example-with-sensitivity-at-85"></a>Voorbeeld met gevoeligheid op 85

![Gevoeligheid 85](../media/sensitivity_85.png)

## <a name="next-steps"></a>Volgende stappen

* [Wat is de Anomaly Detector-API?](../overview.md)
* [Snelstart: afwijkingen in uw tijdreeksgegevens detecteren met behulp van de Anomalie Detector REST API](../quickstarts/detect-data-anomalies-csharp.md)
