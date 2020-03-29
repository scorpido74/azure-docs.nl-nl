---
title: Sluit uw Azure IoT Central-toepassing aan met Dynamics 365 Field Services | Microsoft Documenten
description: Meer informatie over het bouwen van een end-to-end oplossing met Azure IoT Central en Dynamics 365 Field Service
author: miriambrus
ms.author: miriamb
ms.date: 10/23/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 7c708268eaeade1cfb625c73ff8758a1941ff5b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80157446"
---
# <a name="build-end-to-end-solution-with-azure-iot-central-and-dynamics-365-field-service"></a>End-to-end oplossing bouwen met Azure IoT Central en Dynamics 365 Field Service 



Als bouwer u de integratie van uw Azure IoT Central-toepassing naar andere bedrijfssystemen inschakelen. 


In een connected waste management oplossing u bijvoorbeeld de verzending van vuilniswagens optimaliseren. De optimalisatie kan worden gedaan op basis van IoT-sensoren gegevens van aangesloten afvalbakken. In uw [IoT Central connected waste management applicatie](./tutorial-connected-waste-management.md) u regels en acties configureren en deze instellen om waarschuwingen te maken in Dynamics Field Service. Dit scenario wordt uitgevoerd met Microsoft Flow, dat rechtstreeks in IoT Central wordt geconfigureerd voor het automatiseren van workflows voor toepassingen en services. Bovendien kan informatie op basis van serviceactiviteiten in Field Service worden teruggestuurd naar Azure IoT Central. 

## <a name="how-to-connect-your-azure-iot-central-application-with-dynamics-365-field-services"></a>Uw Azure IoT Central-toepassing verbinden met Dynamics 365 Field Services 

De onderstaande integratieprocessen kunnen eenvoudig worden geïmplementeerd op basis van een pure configuratie-ervaring:
* Azure IoT Central kan informatie over apparaatafwijkingen verzenden naar Connected Field Service (als IoT-waarschuwing) voor diagnose.
* Connected Field Service kan aanvragen of werkorders maken die zijn geactiveerd door apparaatafwijkingen.
* Connected Field Service kan technici inplannen voor inspectie om de downtime-incidenten te voorkomen.
* Het Azure IoT Central-apparaatdashboard kan worden bijgewerkt met relevante service- en planningsgegevens.


## <a name="next-steps"></a>Volgende stappen
* Meer informatie over [iot-sjablonen voor centrale overheden](./overview-iot-central-government.md)
* Meer informatie over [IoT Central](https://docs.microsoft.com/azure/iot-central/core/overview-iot-central)
* Meer informatie over [Dynamics 365 Field Services](https://docs.microsoft.com/dynamics365/field-service/cfs-iot-overview)
