---
title: IoT-opties voor Microsoft Azure | Microsoft Docs
description: Kies hoe u uw Azure IoT-oplossing wilt implementeren met behulp van Azure IoT Central, IoT-oplossingsversnellers of IoT Hub.
author: dominicbetts
ms.author: dobett
ms.date: 06/09/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: timlt
ms.openlocfilehash: f57d36f6f24aab44d13ea07d8706bf40b7dcf552
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72958159"
---
# <a name="compare-azure-iot-central-and-azure-iot-options"></a>Opties van Azure IoT Central vergelijken met die van Azure IoT

Microsoft Azure IoT Central en Azure IoT bieden verschillende opties voor het bouwen van een IoT-oplossing. Deze opties zijn geschikt voor verschillende klantvereisten:

* [Azure IoT Central](overview-iot-central.md) is een SaaS-oplossing (Software as a Service) die gebruikmaakt van een op modellen gebaseerde benadering waarmee u hoogwaardige IoT-oplossingen kunt bouwen zonder kennis van het ontwikkelen van cloudoplossingen.

* [Azure IoT-oplossingsversnellers](https://docs.microsoft.com/azure/iot-accelerators/) is een verzameling geavanceerde [oplossingsversnellers](../../iot-accelerators/iot-accelerators-what-are-solution-accelerators.md), gebouwd op Azure PaaS (Platform as a Service), waarmee u sneller aangepaste IoT-oplossingen kunt ontwikkelen.

## <a name="azure-iot-hub"></a>Azure IoT Hub

Azure IoT Hub is de kern-PaaS van Azure waarvan zowel Azure IoT Central als Azure IoT-oplossingsversnellers gebruikmaken. IoT Hub ondersteunt stabiele en veilige tweerichtingscommunicatie tussen miljoenen IoT-apparaten en een cloudoplossing. Met IoT Hub kunt u de uitdagingen van IoT-implementatie aan, zoals:

* Connectiviteit en beheer van grote volumes aan apparaten
* Telemetrieopname van groot volume
* Besturen en bedienen van apparaten
* Afdwinging van apparaatbeveiliging

## <a name="compare-azure-iot-central-and-azure-iot-solution-accelerators"></a>Azure IoT Central en Azure IoT-oplossingsversnellers met elkaar vergelijken

Het kiezen van uw Azure IoT-product is een belangrijk onderdeel van het plannen van uw IoT-oplossing. IoT Hub is een afzonderlijke Azure-service die zelfstandig geen volledige IoT-oplossing biedt. U kunt IoT Hub als uitgangspunt gebruiken voor elke IoT-oplossing. U hoeft ook niet Azure IoT-oplossingsversnellers of Azure IoT Central te gebruiken om IoT Hub te gebruiken. Zowel IoT-oplossingsversnellers als Azure IoT Central maken gebruik van IoT Hub, samen met andere Azure-services.

De volgende tabel geeft een overzicht van de belangrijkste verschillen tussen IoT-oplossingsversnellers en Azure IoT Central, om u te helpen de juiste oplossing voor uw vereisten te kiezen:

|     | Azure IoT Central | Accelerators voor Azure IoT-oplossing |
| --- | ----------- | --------- |
| Primair gebruik                      | Kortere time-to-market voor eenvoudige IoT-oplossingen waarvoor geen uitgebreide serviceaanpassing is vereist.                                                    | Snellere ontwikkeling van een aangepaste IoT-oplossing die maximale flexibiliteit nodig heeft.                                                                                                                             |
| Toegang tot onderliggende PaaS-services | SaaS. Omdat het een volledig beheerde oplossing is, worden de onderliggende services niet weergegeven.                                                                                            | U hebt toegang tot de onderliggende Azure-services om deze te beheren of indien nodig te vervangen.                                                                                                                    |
| Flexibiliteit                        | Gemiddeld. U kunt de ingebouwde browsergebaseerde gebruikerservaring gebruiken om het oplossingsmodel en aspecten van de gebruikersinterface aan te passen. De infrastructuur kan niet worden aangepast, omdat de verschillende onderdelen niet worden weergegeven. | Hoog. De code voor de microservices is open-source en u kunt deze op elke gewenste manier wijzigen. Daarnaast kunt u de implementatie-infrastructuur aanpassen.                                               |
| Kennisniveau                        | Laag. U heeft modelleerervaring nodig om de oplossing aan te passen. Ervaring met codering is niet vereist.                                                                          | Gemiddeld-hoog. U heeft ervaring met Java of .NET nodig om de back-end van de oplossing aan te passen. U heeft ervaring met JavaScript nodig om de visualisatie aan te passen.                                                                       |
| Benodigde ervaring             | Toepassingssjablonen en apparaatsjablonen bieden vooraf samengestelde modellen. Kan in enkele minuten worden geïmplementeerd.                                                                                                  | Vooraf geconfigureerde oplossingen implementeren algemene IoT-scenario's. Kan in enkele minuten worden geïmplementeerd.                                                                                                                            |
| Prijzen                            | Eenvoudige, voorspelbare prijsstructuur.                                                                                                                           | U kunt de services afstellen om de kosten te beperken.                                                                                                                                                            |

Welk product u het beste kunt gebruiken voor het bouwen van uw IoT-oplossing is afhankelijk van:

* Uw zakelijke vereisten.
* Het type oplossing dat u wilt maken.
* De kwalificaties van uw organisatie voor het maken en op de lange termijn onderhouden van de oplossing.

## <a name="next-steps"></a>Volgende stappen

Op basis van uw gekozen product en benadering, zijn dit de voorgestelde volgende stappen:

* **Azure IOT Central**: [wat is Azure IOT Central?](overview-iot-central.md)
* **IOT-oplossings Accelerators**: [Wat zijn Azure IOT-oplossings Accelerators?](../../iot-accelerators/iot-accelerators-what-are-solution-accelerators.md)
* **IOT hub**: [wat is Azure IOT hub?](https://docs.microsoft.com/azure/iot-hub/iot-hub-what-is-iot-hub)