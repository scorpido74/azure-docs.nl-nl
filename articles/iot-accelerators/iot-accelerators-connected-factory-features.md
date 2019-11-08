---
title: Functies van de oplossing Connected Factory-Azure | Microsoft Docs
description: In dit artikel wordt een overzicht gegeven van de functies van de vooraf geconfigureerde oplossing Connected Factory, zoals Cloud dashboard, regels en waarschuwingen.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: dobett
ms.openlocfilehash: c868aa0f1c2449ccf163523c9ded25a31d1d84c4
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73820110"
---
# <a name="what-is-connected-factory-iot-solution-accelerator"></a>Wat is de verbonden Factory IoT-oplossings versneller?

Connected Factory is een implementatie van de Azure Industrial IoT-referentie architectuur van micro soft, verpakt als een open-source oplossing. U kunt deze gebruiken als uitgangs punt voor een commercieel product. U kunt een vooraf ontwikkelde versie van de oplossing Connected Factory implementeren in uw Azure-abonnement van [Azure IOT-oplossings Accelerators](https://www.azureiotsolutions.com/#solutions/types/CF).

![Dash board Connected Factory-oplossing](./media/iot-accelerators-connected-factory-features/dashboard.png)

De Connected Factory Solution Accelerator [code is beschikbaar op github](https://github.com/Azure/azure-iot-connected-factory).

Verbonden Factory bevat de volgende functies:

## <a name="industrial-device-interoperability"></a>Interoperabiliteit van industriële apparaten

- Maak verbinding met industriële activa met een OPC UA-interface.
- Gebruik de gesimuleerde productie lijnen (actieve OPC UA-servers in docker-containers) om live-telemetrie hiervan te bekijken.
- Blader door het OPC UA-informatie model van de OPC UA-servers vanuit een Cloud dashboard.

## <a name="remote-management"></a>Extern beheer

- Configureer uw OPC UA-activa vanuit het clouddashboard (aanroepmethoden, lezen en schrijven van gegevens).
- Publiceer telemetriegegevens van uw OPC UA-activa en maak de publicatie hiervan ongedaan vanuit een clouddashboard.

## <a name="cloud-dashboard"></a>Clouddashboard

- Bekijk de voor beelden van telemetrie rechtstreeks in een Cloud dashboard.
- Geef trends in de telemetriegegevens weer en maak correlaties met het Time Series Insights Explorer-dashboard.
- Zie berekende algemene efficiëntie van apparatuur (OEE) en Key Performance Indica tors (Kpi's) van een Cloud dashboard.
- Bekijk de hiërarchieën van industriële activa in een boom topologie en op een interactieve kaart.
- Waarschuwingen weer geven, erkennen en sluiten vanuit een Cloud dashboard.

## <a name="azure-time-series-insights"></a>Azure Time Series Insights

- [Azure time series Insights](../time-series-insights/time-series-insights-overview.md) is gemaakt voor het opslaan, visualiseren en opvragen van grote hoeveel heden gegevens in de tijd reeks. Connected Factory maakt gebruik van deze service.
- Connected Factory integreert met deze service, zodat u de gegevens van uw apparaten dieper kunt analyseren.

## <a name="rules-and-alerts"></a>Regels en waarschuwingen

[Regels op basis van drempel waarden configureren voor waarschuwingen](iot-accelerators-connected-factory-configure.md).

## <a name="end-to-end-security"></a>End-to-end beveiliging

- Configureer beveiligingsmachtigingen voor gebruikers met op rollen gebaseerd toegangsbeheer (RBAC).
- End-to-end-versleuteling wordt geïmplementeerd met behulp van de OPC UA-verificatie (met behulp van X. 509-certificaten) en beveiligings tokens.

## <a name="customizability"></a>Aanpassings mogelijkheden

- Pas de oplossing aan om te voldoen aan specifieke bedrijfs vereisten.
- Volledige bron code van de oplossing die beschikbaar is op GitHub. Zie de [vooraf geconfigureerde](https://github.com/Azure/azure-iot-connected-factory) opslag plaats voor oplossingen van de verbonden Factory.

## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over de Connected Factory Solution Accelerator raadpleegt u de Quick [Start een Cloud oplossing voor het beheren van mijn industriële IOT-apparaten](quickstart-connected-factory-deploy.md).
