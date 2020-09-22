---
title: Wat is de Metrics Advisor-service?
titleSuffix: Azure Cognitive Services
description: Wat is Metrics Advisor?
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: overview
ms.date: 09/14/2020
ms.author: aahi
ms.openlocfilehash: 408bdd948977218d9b39a39bf97391a4141e545c
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90943717"
---
# <a name="what-is-metrics-advisor-preview"></a>Wat is Metrics Advisor (preview)? 

Metrics Advisor is een onderdeel van Azure Cognitive Services dat AI gebruikt voor het uitvoeren van gegevensbewaking en anomaliedetectie in tijdreeksgegevens. De service automatiseert het proces van het toepassen van modellen op uw gegevens en biedt een werkruimte met een set web-API's voor gegevensopname, anomaliedetectie en diagnoses. Hiervoor is geen kennis van machine learning vereist. Met Metrics Advisor kunt u:

* Multidimensionale gegevens van meerdere gegevensbronnen analyseren 
* Anomalieën identificeren en correleren
* Het anomaliedetectiemodel dat wordt gebruikt voor uw gegevens configureren en afstemmen
* Anomalieën diagnosticeren en helpen bij hoofdoorzaakanalsye. 

:::image type="content" source="media/metrics-advisor-overview.png" alt-text="Overzicht van Metrics Advisor":::

## <a name="connect-to-a-variety-of-data-sources"></a>Verbinding maken met verschillende gegevensbronnen

Met Metrics Advisor kunt u verbinding maken met en [multidimensionale metrische gegevens opnemen](how-tos/onboard-your-data.md) uit allerlei gegevensarchieven, met inbegrip van: SQL Server, Azure Blob Storage, MongoDB en meer. 

## <a name="easy-to-use-and-customizable-anomaly-detection"></a>Gebruiksvriendelijke en aanpasbare anomaliedetectie

* Metrics Advisor selecteert automatisch het beste model voor uw gegevens, zonder dat u enige kennis van machine learning hoeft te hebben. 
* Bewaak automatisch elke tijdreeks in [multidimensionale metrische gegevens](glossary.md#multi-dimensional-metric).
* Gebruik [parameterafstemming](how-tos/configure-metrics.md) en [interactieve feedback](how-tos/anomaly-feedback.md) om het model dat op uw gegevens en toekomstige anomaliedetectieresultaten wordt toegepast, naar wens aan te passen.


## <a name="real-time-alerts-through-multiple-channels"></a>Realtime waarschuwingen via meerdere kanalen

Wanneer anomalieën worden gedetecteerd, kan Metrics Advisor via meerdere kanalen [realtime waarschuwingen verzenden](how-tos/alerts.md) met behulp van hooks, zoals e-mailhooks, webhooks en Azure DevOps-hooks. Met flexibele waarschuwingsregels kunt u aanpassen welke waarschuwingen worden verzonden, en waar.

## <a name="smart-diagnostic-insights-by-analyzing-anomalies"></a>Slimme diagnostische inzichten door anomalieën te analyseren

Analyseer anomalieën die zijn gedetecteerd in multidimensionale metrische gegevens en genereer [slimme diagnostische inzichten](how-tos/diagnose-incident.md), met inbegrip van de meest waarschijnlijke hoofdoorzaak, diagnostische structuren, metrische analyses, en meer. Door de [grafiek met metrische gegevens](how-tos/metrics-graph.md) te configureren, kan analyse van meerdere metrische gegevens worden ingeschakeld zodat u incidenten kunt visualiseren.


## <a name="typical-workflow"></a>Standaardwerkstroom

De werkstroom is eenvoudig: na onboarding van de gegevens kunt u de anomaliedetectie afstemmen en configuraties maken die passen bij uw scenario.

1. [Maak een Azure-resource](../cognitive-services-apis-create-account.md) voor Metrics Advisor. 
2. Probeer de demosite om een voorbeeld van een Metrics Advisor-instantie te bekijken met vooraf geconfigureerde voorbeeldgegevens. 
3. Bouw uw eerste gegevensset met behulp van de webportal.
    1. Onboarding van uw gegevens
    2. Anomaliedetectie afstemmen
    3. Abonneren op waarschuwingen
    4. Diagnostische inzichten weergeven
1. Gebruik de REST API om uw exemplaar aan te passen.

## <a name="next-steps"></a>Volgende stappen

* De [demosite](quickstarts/explore-demo.md) proberen.
* Een quickstart verkennen: [Uw eerste metriek op het web controleren](quickstarts/web-portal.md).
* Een quickstart verkennen: [De REST API's gebruiken om uw oplossing aan te passen](quickstarts/rest-api.md).
