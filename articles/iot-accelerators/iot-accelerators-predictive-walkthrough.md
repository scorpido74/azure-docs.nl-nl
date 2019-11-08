---
title: Overzicht van de oplossingsversneller Voorspeld onderhoud - Azure | Microsoft Docs
description: Een overzicht van de oplossing voor voor speld onderhoud van Azure IoT waarmee het moment wordt voor speld dat er waarschijnlijk een fout optreedt voor een bedrijfs scenario.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: dobett
ms.openlocfilehash: 0661503dce7ac2707065f60c3952da866ce9dcf3
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827420"
---
# <a name="predictive-maintenance-solution-accelerator-overview"></a>Overzicht van de oplossingsversneller Voorspeld onderhoud

De oplossingsversneller Voorspeld onderhoud is een totaaloplossing voor een bedrijfsscenario die het punt voorspelt waarop naar verwachting een fout optreedt. U kunt deze oplossingsversneller proactief gebruiken voor activiteiten zoals het optimaliseren van onderhoud. De oplossing combineert belang rijke Azure IoT Solution Accelerators Services, zoals IoT Hub en een [Azure machine learning][lnk-machine-learning] -werk ruimte. Deze werkruimte bevat een model, gebaseerd op een openbare verzameling voorbeeldgegevens, om de resterende bruikbare levensduur (RUL) van een vliegtuigmotor te voorspellen. De oplossing implementeert het IoT-bedrijfsscenario volledig als een beginpunt zodat u een oplossing kunt plannen en implementeren die voldoet aan uw eigen specifieke zakelijke vereisten.

De oplossings versnellers code voor voorspellende onderhoud [is beschikbaar op github](https://github.com/Azure/azure-iot-predictive-maintenance).

## <a name="logical-architecture"></a>Logische architectuur

Het volgende diagram geeft een overzicht van de logische onderdelen van de oplossingsversneller:

![Logische architectuur][img-architecture]

De blauwe items zijn Azure-services die zijn ingericht in de regio waar u de oplossingsversneller hebt geïmplementeerd. De lijst met regio's waar u de oplossings versneller kunt implementeren, wordt weer gegeven op de [pagina inrichting][lnk-azureiotsolutions].

Het groene item is een gesimuleerde vliegtuig motor. Meer informatie over deze gesimuleerde apparaten vindt u in het gedeelte [Gesimuleerde apparaten](#simulated-devices).

De grijze items zijn onderdelen waarmee mogelijkheden voor het *beheer van apparaten* worden geïmplementeerd. In de huidige release van de oplossingsversneller Voorspeld onderhoud worden deze resources niet ingericht. Raadpleeg de [oplossings versneller voor externe bewaking][lnk-remote-monitoring]voor meer informatie over Apparaatbeheer.

## <a name="azure-resources"></a>Azure-resources

Navigeer in Azure Portal naar de resourcegroep met de naam van de oplossing die u hebt gekozen om de ingerichte resources weer te geven.

![Accelerator bronnen][img-resource-group]

Wanneer u de oplossingsversneller inricht, krijgt u een e-mailbericht met een koppeling naar de Machine Learning-werkruimte. U kunt ook naar de Machine Learning-werk ruimte navigeren vanaf de pagina [Microsoft Azure IOT-oplossings versnellers][lnk-azureiotsolutions] . Op deze pagina is een tegel beschikbaar wanneer de oplossing de status **Gereed** heeft.

![Machine learning-model][img-machine-learning]

## <a name="simulated-devices"></a>Gesimuleerde apparaten

In de oplossings versneller is een gesimuleerd apparaat een vliegtuig motor. De oplossing is ingericht met twee motoren die aan één vliegtuig zijn toegewezen. Elke motor verzendt vier typen telemetrie: Sensor 9, Sensor 11, Sensor 14 en Sensor 15 leveren de benodigde gegevens waarmee het Machine Learning-model de RUL voor de motor berekent. Elk gesimuleerd apparaat verzendt de volgende telemetrieberichten naar IoT Hub:

*Aantal maal gebruikt*. Een cyclus is een voltooide vlucht met een duur tussen twee en tien uur. Tijdens de vlucht worden elk half uur telemetriegegevens vastgelegd.

*Telemetrie*. Er zijn vier Sens oren die engine kenmerken registreren. Deze sensoren worden doorgaans Sensor 9, Sensor 11 Sensor 14 en Sensor 15 genoemd. Deze vier Sens oren sturen voldoende telemetrie om nuttige resultaten te verkrijgen van het resterende levens duur-model. Het model dat wordt gebruikt in de oplossingsversneller, wordt gemaakt op basis van een openbare gegevensset die echte motorsensorgegevens bevat. Zie voor meer informatie over hoe het model is gemaakt op basis van de oorspronkelijke gegevensset de [Cortana Intelligence Gallery voorspellende onderhouds sjabloon][lnk-cortana-analytics].

De gesimuleerde apparaten kunnen de volgende opdrachten verwerken die zijn verzonden vanaf de IoT Hub in de oplossing:

| Opdracht | Beschrijving |
| --- | --- |
| StartTelemetry |Bepaalt de status van de simulatie.<br/>Start het verzenden van telemetrie vanaf het apparaat |
| StopTelemetry |Bepaalt de status van de simulatie.<br/>Stopt het verzenden van telemetrie vanaf het apparaat |

IoT Hub biedt een bevestiging van apparaatopdrachten.

## <a name="azure-stream-analytics-job"></a>Azure Stream Analytics-job

**Job: Telemetrie** verwerkt de inkomende telemetriestroom van het apparaat met behulp van twee instructies:

* De eerste selecteert alle telemetrie van de apparaten en verzendt deze gegevens naar Blob Storage. Hier ziet u dat in de web-app wordt gevisualiseerd.
* De tweede instructie berekent de gemiddelde sensorwaarden gedurende een sliding window van twee minuten en verzendt deze gegevens via Event Hub naar een **gebeurtenisprocessor**.

## <a name="event-processor"></a>Gebeurtenisprocessor
De **gebeurtenisprocessorhost** wordt uitgevoerd in een Azure-webtaak. De **gebeurtenisverwerking** neemt de gemiddelde sensorwaarden voor een voltooide cyclus. Vervolgens worden deze waarden door gegeven aan een getraind model waarmee de resterende levens duur voor een engine wordt berekend. Een API biedt toegang tot het model in een Machine Learning-werk ruimte die deel uitmaakt van de oplossing.

## <a name="machine-learning"></a>Machine Learning
Het Machine Learning-onderdeel maakt gebruikt van een model dat is afgeleid van gegevens die zijn verzameld bij echte vliegtuigmotoren. U kunt naar de Machine Learning-werk ruimte navigeren vanaf de tegel van uw oplossing op de pagina [azureiotsolutions.com][lnk-azureiotsolutions] . De tegel is beschikbaar wanneer de oplossing de status **Gereed** heeft.

Het Machine Learning model is beschikbaar als sjabloon die laat zien hoe u met de telemetrie kunt werken die is verzameld via IoT Solution Accelerator Services. Micro soft heeft een [regressie model][lnk_regression_model] van een vliegtuig motor ontwikkeld op basis van openbaar beschik bare gegevens<sup>\[1\]</sup>en stapsgewijze richt lijnen voor het gebruik van het model.

De Azure IoT-oplossingsversneller Voorspeld onderhoud maakt gebruik van het regressiemodel dat op basis van deze sjabloon is gemaakt. Het model wordt geïmplementeerd in uw Azure-abonnement en beschikbaar gesteld via een automatisch gegenereerde API. De oplossing omvat een subset van de test gegevens voor 4 (van 100 totaal) motoren en de 4 (van 21 totale) gegevens stromen van sensors. Deze gegevens leveren een accuraat resultaat op van het getrainde model.

*\[1\] A. Saxena en K. Goebel (2008). "Turbofan engine-simulatie gegevensset voor degradatie", NASA Ames Prognostics data repository (https://c3.nasa.gov/dashlink/resources/139/), NASA Ames Research Center, Moffett Field, CA*

## <a name="next-steps"></a>Volgende stappen
Nu u de belangrijke onderdelen van de oplossingsversneller Voorspeld onderhoud hebt gezien, wilt u deze misschien aanpassen.

U kunt ook enkele van de andere functies van de IoT-oplossings Accelerators verkennen:

* [Veelgestelde vragen over Accelerators voor IoT-oplossingen][lnk-faq]
* [IoT-beveiliging vanaf de grond][lnk-security-groundup]

[img-architecture]: media/iot-accelerators-predictive-walkthrough/architecture.png
[img-resource-group]: media/iot-accelerators-predictive-walkthrough/resource-group.png
[img-machine-learning]: media/iot-accelerators-predictive-walkthrough/machine-learning.png

[lnk-remote-monitoring]: quickstart-predictive-maintenance-deploy.md
[lnk-cortana-analytics]: https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Template-3
[lnk-azureiotsolutions]: https://www.azureiotsolutions.com/
[lnk-faq]: iot-accelerators-faq.md
[lnk-security-groundup]:/azure/iot-fundamentals/iot-security-ground-up
[lnk-machine-learning]: https://azure.microsoft.com/services/machine-learning/
[lnk_regression_model]: https://gallery.cortanaanalytics.com/Collection/Predictive-Maintenance-Template-3
