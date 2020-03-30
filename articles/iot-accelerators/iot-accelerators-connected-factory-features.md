---
title: Functies voor connected factory-oplossingen - Azure | Microsoft Documenten
description: In dit artikel wordt een overzicht beschreven van de functies van de vooraf geconfigureerde oplossing van Connected Factory, zoals clouddashboard, regels en waarschuwingen.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: dobett
ms.openlocfilehash: c868aa0f1c2449ccf163523c9ded25a31d1d84c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73820110"
---
# <a name="what-is-connected-factory-iot-solution-accelerator"></a>Wat is De Versneller van De Oplossing van Verbonden Fabriek IoT?

Connected Factory is een implementatie van Microsoft's Azure Industrial IoT-referentiearchitectuur, verpakt als open-source-oplossing. U het gebruiken als uitgangspunt voor een commercieel product. U een vooraf gebouwde versie van de Connected Factory-oplossing implementeren in uw Azure-abonnement via [Azure IoT-oplossingsversnellers.](https://www.azureiotsolutions.com/#solutions/types/CF)

![Dashboard verbonden fabrieksoplossing](./media/iot-accelerators-connected-factory-features/dashboard.png)

De Connected Factory oplossingsversnellercode [is beschikbaar op GitHub.](https://github.com/Azure/azure-iot-connected-factory)

Connected Factory bevat de volgende functies:

## <a name="industrial-device-interoperability"></a>Interoperabiliteit van industriële apparatuur

- Maak verbinding met industriële assets met een OPC UA-interface.
- Gebruik de gesimuleerde productielijnen (met OPC UA-servers in Docker-containers) om live telemetrie van hen te zien.
- Blader door het OPC UA-informatiemodel van de OPC UA-servers vanuit een clouddashboard.

## <a name="remote-management"></a>Extern beheer

- Configureer uw OPC UA-assets vanuit het clouddashboard (oproepmethoden, lees- en schrijfgegevens).
- Telemetriegegevens van uw OPC UA-assets publiceren en ongedaan maken vanuit een clouddashboard.

## <a name="cloud-dashboard"></a>Clouddashboard

- Bekijk telemetrievoorbeelden rechtstreeks in een clouddashboard.
- Bekijk trends in telemetriegegevens en maak correlaties met het dashboard Time Series Insights Explorer.
- Zie berekende Algemene Infrastructuurefficiëntie (OEE) en Key Performance Indicators (KPI's) vanuit een clouddashboard.
- Bekijk industriële activahiërarchieën in een boomtopologie en op een interactieve kaart.
- Waarschuwingen van een clouddashboard weergeven, erkennen en sluiten.

## <a name="azure-time-series-insights"></a>Azure Time Series Insights

- [Azure Time Series Insights](../time-series-insights/time-series-insights-overview.md) is gemaakt voor het opslaan, visualiseren en opvragen van grote hoeveelheden tijdreeksgegevens. Connected Factory maakt gebruik van deze service.
- Connected Factory integreert met deze service, zodat u diepgaande, realtime analyses van uw apparaatgegevens uitvoeren.

## <a name="rules-and-alerts"></a>Regels en waarschuwingen

[Regels op basis van drempelwaarden configureren voor waarschuwingen](iot-accelerators-connected-factory-configure.md).

## <a name="end-to-end-security"></a>End-to-end beveiliging

- Beveiligingsmachtigingen configureren voor gebruikers die RBAC (Role-Based Access Control) gebruiken.
- End-to-end encryptie wordt geïmplementeerd met OPC UA-authenticatie (met Behulp van X.509-certificaten) en beveiligingstokens.

## <a name="customizability"></a>Customizability

- Pas de oplossing aan om aan specifieke bedrijfsvereisten te voldoen.
- Volledige oplossing broncode beschikbaar op GitHub. Zie de [voorgeconfigureerde oplossingsopslagplaats verbonden factory.](https://github.com/Azure/azure-iot-connected-factory)

## <a name="next-steps"></a>Volgende stappen

Zie de Quickstart-oplossing voor meer informatie over de Connected Factory-oplossingsversneller [om mijn industriële IoT-apparaten te beheren.](quickstart-connected-factory-deploy.md)
