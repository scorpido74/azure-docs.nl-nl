---
title: Wat is de Anomaly Detector-API?
titleSuffix: Azure Cognitive Services
description: Gebruik de geavanceerde algoritmen van de Anomaly Detector API om afwijkingen in uw tijdreeksgegevens te identificeren.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: overview
ms.date: 12/18/2019
ms.author: aahi
ms.openlocfilehash: 9237e670dd8d43c4036f996c477948944718e3aa
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80053703"
---
# <a name="what-is-the-anomaly-detector-api"></a>Wat is de Anomaly Detector-API?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Met de Anomaly Detector API u afwijkingen in uw tijdreeksgegevens met machine learning monitoren en detecteren. De Api voor Anomaly Detector past zich aan door automatisch de best passende modellen op uw gegevens te identificeren en toe te passen, ongeacht de industrie, het scenario of het gegevensvolume. Met behulp van uw tijdreeksgegevens bepaalt de API grenzen voor anomaliedetectie, verwachte waarden en welke gegevenspunten afwijkingen zijn.

![Patroonwijzigingen in serviceaanvragen detecteren](./media/anomaly_detection2.png)

Het gebruik van de Anomaly Detector vereist geen voorafgaande ervaring in machine learning, en met de RESTful API u de service eenvoudig integreren in uw toepassingen en processen.

## <a name="features"></a>Functies

Met de Anomaliedetector u automatisch afwijkingen detecteren in uw tijdreeksgegevens of wanneer ze zich in realtime voordoen.

|Functie  |Beschrijving  |
|---------|---------|
|Detecteren afwijkingen als ze zich voordoen in real-time. | Detecteer afwijkingen in uw streaminggegevens door eerder geziene gegevenspunten te gebruiken om te bepalen of uw laatste een anomalie is. Deze bewerking genereert een model met behulp van de gegevenspunten die u verzendt en bepaalt of het doelpunt een anomalie is. Door de API aan te roepen bij elk nieuw gegevenspunt dat u genereert, u uw gegevens controleren terwijl deze worden gemaakt. |
|Detecteer afwijkingen in uw gegevensset als een batch. | Gebruik uw tijdreeksen om eventuele afwijkingen in uw gegevens te detecteren. Deze bewerking genereert een model met behulp van uw volledige tijdreeksgegevens, waarbij elk punt wordt geanalyseerd met hetzelfde model.         |
| Meer informatie over uw gegevens. | Krijg nuttige informatie over uw gegevens en waargenomen afwijkingen, waaronder verwachte waarden, anomaliegrenzen en posities. |
| De detectiegrenzen van anomalieën aanpassen. | De Api voor anomaliedetector creëert automatisch grenzen voor anomaliedetectie. Pas deze grenzen aan om de gevoeligheid van de API voor gegevensafwijkingen te vergroten of te verlagen en beter bij uw gegevens te passen. |

## <a name="demo"></a>Demo

Bekijk deze [interactieve demo](https://aka.ms/adDemo) om te begrijpen hoe Anomaly Detector werkt.
Als u de demo wilt uitvoeren, moet u een Anomaly Detector-bron maken en de API-sleutel en het eindpunt krijgen.

## <a name="notebook"></a>Notebook

Probeer deze [Azure Notebook](https://aka.ms/adNotebook)voor meer informatie over het aanroepen van de Api voor anomaliedetector. Deze webgehoste Jupyter-notebook laat u zien hoe u een API-aanvraag verzendt en het resultaat visualiseert.

Voer de volgende stappen uit om het notitieblok uit te voeren:

1. Ontvang een geldige Anomaly Detector API-abonnementssleutel en een API-eindpunt. De sectie hieronder heeft instructies voor het aanmelden.
1. Meld u aan en klik op Kloon in de rechterbovenhoek.
1. Schakel de optie 'openbaar' in het dialoogvenster uit voordat u de kloonbewerking voltooit, anders is uw notitieblok, inclusief abonnementssleutels, openbaar.
1. Klik **op Uitvoeren op vrije rekenkracht**
1. Selecteer een van de notitieblokken.
1. Voeg uw geldige Anomaly Detector `subscription_key` API-abonnementssleutel toe aan de variabele.
1. Wijzig `endpoint` de variabele in uw eindpunt. Bijvoorbeeld: `https://westus2.api.cognitive.microsoft.com/anomalydetector/v1.0/timeseries/last/detect`
1. Klik op de bovenste menubalk op **Cel**en **voer Alles uit.**

## <a name="workflow"></a>Werkstroom

De Anomaly Detector API is een RESTful webservice, waardoor het gemakkelijk is om te bellen vanuit elke programmeertaal die HTTP-verzoeken kan doen en JSON kan ontleden.

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]

Na het aanmelden:

1. Neem uw tijdreeksgegevens en zet deze om in een geldige JSON-indeling. Gebruik [aanbevolen procedures](concepts/anomaly-detection-best-practices.md) bij het voorbereiden van uw gegevens om de beste resultaten te behalen.
1. Stuur een aanvraag naar de Anomaly Detector API met uw gegevens.
1. Verwerk de API-reactie door het geretourneerde JSON-bericht te parseren.

## <a name="algorithms"></a>Algoritmen

* Zie de volgende technische blogs voor informatie over de gebruikte algoritmen:
    * [Inleiding tot de Azure Anomaly Detector-API](https://techcommunity.microsoft.com/t5/AI-Customer-Engineering-Team/Introducing-Azure-Anomaly-Detector-API/ba-p/490162)
    * [Overzicht van SR-CNN algoritme in Azure Anomaly Detector](https://techcommunity.microsoft.com/t5/AI-Customer-Engineering-Team/Overview-of-SR-CNN-algorithm-in-Azure-Anomaly-Detector/ba-p/982798)

U de papieren [Time-Series Anomaly Detection Service bij Microsoft](https://arxiv.org/abs/1906.03821) (geaccepteerd door KDD 2019) lezen voor meer informatie over de SR-CNN-algoritmen die door Microsoft zijn ontwikkeld.


> [!VIDEO https://www.youtube.com/embed/ERTaAnwCarM]

## <a name="join-the-anomaly-detector-community"></a>Lid worden van de Anomaly Detector-community

* Word lid van de [groep Anomalie Detector Advisors op Microsoft Teams](https://aka.ms/AdAdvisorsJoin)
* Geselecteerde [door gebruikers gegenereerde inhoud bekijken](user-generated-content.md)

## <a name="next-steps"></a>Volgende stappen

* [Snelstart: afwijkingen in uw tijdreeksgegevens detecteren met behulp van de Anomalie Detector REST API](quickstarts/detect-data-anomalies-csharp.md)
* De Anomalie Detector API [online demo](https://notebooks.azure.com/AzureAnomalyDetection/projects/anomalydetector)
* De [API-referentie](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect) van de Anomalie Detector REST
