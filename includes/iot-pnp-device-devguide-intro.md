---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 09/24/2020
ms.openlocfilehash: 706e936590f5f47ee2989bf77b3caad67a8f28fc
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/30/2020
ms.locfileid: "91579793"
---
IoT Plug en Play biedt u de mogelijkheid om slimme apparaten te bouwen die hun mogelijkheden adverteren voor Azure IoT-toepassingen. IoT Plug en Play-apparaten hoeven niet hand matig te worden geconfigureerd wanneer een klant deze verbindt met IoT-Plug en Play toepassingen.

Een Smart Device kan rechtstreeks worden geïmplementeerd, [modules](../articles/iot-hub/iot-hub-devguide-module-twins.md)gebruiken of [IOT Edge modules](../articles/iot-edge/about-iot-edge.md)gebruiken.

In deze hand leiding worden de basis stappen beschreven die nodig zijn om een apparaat, module of IoT Edge-module te maken die volgt op de [IoT Plug en Play-conventies](../articles/iot-pnp/concepts-convention.md).

Voer de volgende stappen uit om een IoT-Plug en Play apparaat, module of IoT Edge module te bouwen:

1. Zorg ervoor dat het apparaat gebruikmaakt van het MQTT-of MQTT over websockets-protocol om verbinding te maken met Azure IoT Hub.
1. Maak een [DTDL-model (Digital Apparaatdubbels Definition Language)](https://github.com/Azure/opendigitaltwins-dtdl) om uw apparaat te beschrijven. Zie [onderdelen begrijpen in IoT Plug en Play-modellen](../articles/iot-pnp/concepts-components.md)voor meer informatie.
1. Werk uw apparaat of module bij om de `model-id` verbinding met het apparaat te maken.
1. Telemetrie, eigenschappen en opdrachten implementeren met behulp van de [IoT Plug en Play-conventies](../articles/iot-pnp/concepts-convention.md)

Zodra de implementatie van uw apparaat of module is voltooid, gebruikt u [Azure IOT Explorer](../articles/iot-pnp/howto-use-iot-explorer.md) om te controleren of het apparaat de IOT Plug en Play-conventies volgt.
