---
title: Wat is de Anomaly Detector-API?
titleSuffix: Azure Cognitive Services
description: Gebruik de geavanceerde algoritmen van de anomalie detectie-API om afwijkingen in uw time series-gegevens te identificeren.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: overview
ms.date: 09/05/2019
ms.author: aahi
ms.openlocfilehash: 3bd60ff1e732940bbb13c2e224084cf7e331266b
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/12/2019
ms.locfileid: "70934863"
---
# <a name="what-is-the-anomaly-detector-api"></a>Wat is de Anomaly Detector-API?

Met de anomalie detectie-API kunt u afwijkingen in uw time series-gegevens controleren en detecteren met machine learning. De anomalie detectie-API wordt aangepast door automatisch de beste modellen te identificeren en toe te passen op uw gegevens, ongeacht de branche, het scenario of het gegevens volume. Met behulp van uw time series-gegevens bepaalt de API grenzen voor anomalie detectie, verwachte waarden en welke gegevens punten afwijkingen zijn.

![Wijzigingen in het patroon van service aanvragen detecteren](./media/anomaly_detection2.png)

Het gebruik van de anomalie detectie vereist geen eerdere ervaring in machine learning. met de REST-API kunt u de service eenvoudig integreren in uw toepassingen en processen.

## <a name="features"></a>Functies

Met de anomalie detectie kunt u automatisch afwijkingen in uw tijdreeks gegevens detecteren of wanneer deze in realtime optreden. 

|Functie  |Description  |
|---------|---------|
|Detecteer afwijkingen die in realtime optreden. | Detecteer afwijkingen in uw streaminggegevens met behulp van eerder waargenomen gegevens punten om te bepalen of uw laatste een afwijkend item is. Met deze bewerking wordt een model gegenereerd met de gegevens punten die u verzendt, en wordt bepaald of het doel punt een afwijkend is. Door de API aan te roepen met elk nieuw gegevens punt dat u genereert, kunt u uw gegevens bewaken terwijl deze worden gemaakt. |
|Detecteer afwijkingen in uw gegevensset als een batch. | Gebruik uw tijd reeks om eventuele afwijkingen op te sporen die in uw gegevens mogelijk zijn. Met deze bewerking wordt een model gegenereerd met uw volledige tijdreeks gegevens, waarbij elk punt wordt geanalyseerd met hetzelfde model.         |
| Ontvang aanvullende informatie over uw gegevens. | Krijg nuttige informatie over uw gegevens en eventuele afwijkingen, waaronder verwachte waarden, afwijkende grenzen en posities. |
| De grenzen voor afwijkings detectie aanpassen. | De anomalie detector API maakt automatisch grenzen voor anomalie detectie. U kunt deze grenzen aanpassen om de gevoeligheid van de API voor gegevens afwijkingen te verhogen of verlagen en uw gegevens beter aan te passen. |

## <a name="demo"></a>Demo

Bekijk deze [interactieve demo](https://aka.ms/adDemo) om te begrijpen hoe anomalie detectie werkt.
Als u de demo wilt uitvoeren, moet u een afwijkende detector-resource maken en de API-sleutel en het eind punt ophalen.

## <a name="notebook"></a>Notebook

Probeer dit [Azure-notitie blok](https://aka.ms/adNotebook)voor meer informatie over het aanroepen van de anomalie detectie-API. Dit webhoste Jupyter Notebook laat zien hoe u een API-aanvraag verzendt en het resultaat kunt visualiseren.

Als u het notitie blok wilt uitvoeren, voert u de volgende stappen uit:

1. Haal een geldige Anomaliey-API-abonnements sleutel en een API-eind punt op. In de volgende sectie vindt u instructies voor het aanmelden.
1. Meld u aan en klik op klonen in de rechter bovenhoek.
1. Schakel de optie ' openbaar ' in het dialoog venster uit voordat u de kloon bewerking voltooit, anders is uw notitie blok, inclusief abonnements sleutels, openbaar.
1. Klik op **uitvoeren op gratis Compute**
1. Selecteer een van de notitie blokken.
1. Voeg uw geldige anomaliey API-abonnements sleutel toe aan `subscription_key` de variabele. 
1. Wijzig de `endpoint` variabele in uw eind punt. Bijvoorbeeld: `https://westus2.api.cognitive.microsoft.com/anomalydetector/v1.0/timeseries/last/detect`
1. Klik in de bovenste menu balk op **cel**en **Voer alles uit**.

## <a name="workflow"></a>Werkstroom

De anomalie detectie-API is een REST-webservice, waarmee u eenvoudig kunt aanroepen vanuit elke programmeer taal die HTTP-aanvragen kan maken en JSON kan parseren.

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]

Nadat u zich hebt aangemeld:

1. Neem uw time series-gegevens mee en converteer deze naar een geldige JSON-indeling. Gebruik [Aanbevolen procedures](concepts/anomaly-detection-best-practices.md) voor het voorbereiden van uw gegevens om de beste resultaten te krijgen.
1. Verzend een aanvraag naar de anomalie detectie-API met uw gegevens.
1. Verwerk de API-reactie door het geretourneerde JSON-bericht te parseren.

## <a name="algorithms"></a>Algoritmen

* Raadpleeg deze technische blog Inleiding tot de [Azure anomalie detector-API](https://techcommunity.microsoft.com/t5/AI-Customer-Engineering-Team/Introducing-Azure-Anomaly-Detector-API/ba-p/490162) over de algoritmen onder de schermen.
* Raadpleeg deze service voor anomalie detectie van de werk [tijd van micro soft](https://arxiv.org/abs/1906.03821) (goedgekeurd door KDD 2019) voor de geavanceerde SR-CNN-algoritmen die zijn ontwikkeld door micro soft.

> [!VIDEO https://www.youtube.com/embed/ERTaAnwCarM]

## <a name="join-the-anomaly-detector-community"></a>Word lid van de anomalie detector-Community

* Word lid [van de groep afwijkingen van de detectorshim van micro soft-teams](https://aka.ms/AdAdvisorsJoin)
* Geselecteerde door [gebruiker gegenereerde inhoud](user-generated-content.md) weer geven

## <a name="next-steps"></a>Volgende stappen

* [Snelstart: Afwijkingen in uw time series-gegevens detecteren met behulp van de anomalie detectie REST API](quickstarts/detect-data-anomalies-csharp.md)
* De [online demo](https://notebooks.azure.com/AzureAnomalyDetection/projects/anomalydetector) over anomalie detectie-API
* De afwijkings detector [rest API referentie](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect)
