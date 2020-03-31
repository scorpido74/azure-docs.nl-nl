---
title: Overzicht van de oplossingsversneller Voorspeld onderhoud - Azure | Microsoft Docs
description: Een overzicht van de Azure IoT Predictive Maintenance-oplossingsversneller die het punt voorspelt waarop een fout waarschijnlijk optreedt voor een bedrijfsscenario.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: dobett
ms.openlocfilehash: 0661503dce7ac2707065f60c3952da866ce9dcf3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73827420"
---
# <a name="predictive-maintenance-solution-accelerator-overview"></a>Overzicht van de oplossingsversneller Voorspeld onderhoud

De oplossingsversneller Voorspeld onderhoud is een totaaloplossing voor een bedrijfsscenario die het punt voorspelt waarop naar verwachting een fout optreedt. U kunt deze oplossingsversneller proactief gebruiken voor activiteiten zoals het optimaliseren van onderhoud. De oplossing combineert belangrijke Azure IoT-oplossingsversnellersservices, zoals IoT Hub en een [Azure Machine Learning-werkruimte.][lnk-machine-learning] Deze werkruimte bevat een model, gebaseerd op een openbare verzameling voorbeeldgegevens, om de resterende bruikbare levensduur (RUL) van een vliegtuigmotor te voorspellen. De oplossing implementeert het IoT-bedrijfsscenario volledig als een beginpunt zodat u een oplossing kunt plannen en implementeren die voldoet aan uw eigen specifieke zakelijke vereisten.

De Predictive Maintenance oplossingsversnellercode [is beschikbaar op GitHub.](https://github.com/Azure/azure-iot-predictive-maintenance)

## <a name="logical-architecture"></a>Logische architectuur

Het volgende diagram geeft een overzicht van de logische onderdelen van de oplossingsversneller:

![Logische architectuur][img-architecture]

De blauwe items zijn Azure-services die zijn ingericht in de regio waar u de oplossingsversneller hebt geïmplementeerd. De lijst met regio's waar u de oplossingsversneller kunt implementeren, wordt weergegeven op de pagina [Inrichting][lnk-azureiotsolutions].

Het groene item is een gesimuleerde vliegtuigmotor. Meer informatie over deze gesimuleerde apparaten vindt u in het gedeelte [Gesimuleerde apparaten](#simulated-devices).

De grijze items zijn onderdelen die *apparaatbeheermogelijkheden* implementeren. In de huidige release van de oplossingsversneller Voorspeld onderhoud worden deze resources niet ingericht. Raadpleeg de [remote monitoring oplossingaccelerator][lnk-remote-monitoring]voor meer informatie over apparaatbeheer.

## <a name="azure-resources"></a>Azure-resources

Navigeer in Azure Portal naar de resourcegroep met de naam van de oplossing die u hebt gekozen om de ingerichte resources weer te geven.

![Acceleratorresources][img-resource-group]

Wanneer u de oplossingsversneller inricht, krijgt u een e-mailbericht met een koppeling naar de Machine Learning-werkruimte. U ook naar de werkruimte Machine Learning navigeren vanaf de pagina [Microsoft Azure IoT Solution Accelerators.][lnk-azureiotsolutions] Op deze pagina is een tegel beschikbaar wanneer de oplossing de status **Gereed** heeft.

![Machine learning-model][img-machine-learning]

## <a name="simulated-devices"></a>Gesimuleerde apparaten

In de oplossingsversneller is een gesimuleerd apparaat een vliegtuigmotor. De oplossing is ingericht met twee motoren die aan één vliegtuig zijn toegewezen. Elke motor verzendt vier typen telemetrie: Sensor 9, Sensor 11, Sensor 14 en Sensor 15 leveren de benodigde gegevens waarmee het Machine Learning-model de RUL voor de motor berekent. Elk gesimuleerd apparaat verzendt de volgende telemetrieberichten naar IoT Hub:

*Aantal maal gebruikt*. Een cyclus is een voltooide vlucht met een duur tussen twee en tien uur. Tijdens de vlucht worden elk half uur telemetriegegevens vastgelegd.

*Telemetrie.* Er zijn vier sensoren die motorattributen registreren. Deze sensoren worden doorgaans Sensor 9, Sensor 11 Sensor 14 en Sensor 15 genoemd. Deze vier sensoren sturen telemetrie voldoende om nuttige resultaten van het RUL-model te krijgen. Het model dat wordt gebruikt in de oplossingsversneller, wordt gemaakt op basis van een openbare gegevensset die echte motorsensorgegevens bevat. Zie [Cortana Intelligence Gallery Predictive Maintenance Template][lnk-cortana-analytics] (Cortana Intelligence Gallery-sjabloon voor voorspellend onderhoud) voor meer informatie over hoe het model wordt gemaakt op basis van de oorspronkelijke gegevensset.

De gesimuleerde apparaten kunnen de volgende opdrachten verwerken die zijn verzonden vanaf de IoT Hub in de oplossing:

| Opdracht | Beschrijving |
| --- | --- |
| StartTelemetry |Bepaalt de status van de simulatie.<br/>Start het verzenden van telemetrie vanaf het apparaat |
| StopTelemetry |Bepaalt de status van de simulatie.<br/>Stopt het verzenden van telemetrie vanaf het apparaat |

IoT Hub biedt een bevestiging van apparaatopdrachten.

## <a name="azure-stream-analytics-job"></a>Azure Stream Analytics-job

**Taak: Telemetrie** werkt op de telemetriestroom van het binnenkomende apparaat met behulp van twee instructies:

* De eerste selecteert alle telemetrie van de apparaten en verzendt deze gegevens naar Blob Storage. Vanaf hier wordt het gevisualiseerd in de web-app.
* De tweede instructie berekent de gemiddelde sensorwaarden gedurende een sliding window van twee minuten en verzendt deze gegevens via Event Hub naar een **gebeurtenisprocessor**.

## <a name="event-processor"></a>Gebeurtenisprocessor
De **gebeurtenisprocessorhost** wordt uitgevoerd in een Azure-webtaak. De **gebeurtenisverwerking** neemt de gemiddelde sensorwaarden voor een voltooide cyclus. Vervolgens geeft het deze waarden door aan een getraind model dat de RUL voor een motor berekent. Een API biedt toegang tot het model in een Machine Learning-werkruimte die deel uitmaakt van de oplossing.

## <a name="machine-learning"></a>Machine Learning
Het Machine Learning-onderdeel maakt gebruikt van een model dat is afgeleid van gegevens die zijn verzameld bij echte vliegtuigmotoren. U naar de machine learning-werkruimte navigeren [azureiotsolutions.com][lnk-azureiotsolutions] vanuit de tegel van uw oplossing op de azureiotsolutions.com-pagina. De tegel is beschikbaar wanneer de oplossing de status **Gereed** heeft.

Het Machine Learning-model is beschikbaar als een sjabloon die laat zien hoe u werken met telemetrie verzameld via IoT-oplossingsversnellerservices. Microsoft heeft een [regressiemodel][lnk_regression_model] van een vliegtuigengine gebouwd op basis van openbaar beschikbare gegevens<sup>\[1\]</sup>en stapsgewijze richtlijnen voor het gebruik van het model.

De Azure IoT-oplossingsversneller Voorspeld onderhoud maakt gebruik van het regressiemodel dat op basis van deze sjabloon is gemaakt. Het model wordt geïmplementeerd in uw Azure-abonnement en beschikbaar gesteld via een automatisch gegenereerde API. De oplossing omvat een subset van de testgegevens voor 4 (van 100 totaal) motoren en de 4 (van in totaal) sensorgegevensstromen. Deze gegevens leveren een accuraat resultaat op van het getrainde model.

*\[1\] A. Saxena en K. Goebel (2008). "Turbofan Engine Degradation Simulation Data Set", NASA Ameshttps://c3.nasa.gov/dashlink/resources/139/)Prognostics Data Repository ( , NASA Ames Research Center, Moffett Field, CA*

## <a name="next-steps"></a>Volgende stappen
Nu u de belangrijke onderdelen van de oplossingsversneller Voorspeld onderhoud hebt gezien, wilt u deze misschien aanpassen.

U ook enkele andere functies van de Versnellers van IoT-oplossingen verkennen:

* [Veelgestelde vragen over IoT-oplossingsversnellers][lnk-faq]
* [Fundamentele IoT-beveiliging][lnk-security-groundup]

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
