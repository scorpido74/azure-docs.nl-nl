---
title: Bestaande IoT-hub gebruiken met apparaatsimulatieoplossing - Azure | Microsoft Documenten
description: In dit artikel wordt beschreven hoe u de apparaatsimulatieoplossingsversneller configureert om een bestaande IoT-hub te gebruiken.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/25/2018
ms.topic: conceptual
ms.openlocfilehash: 1f89e23d7bb279e7cce5c104060cc7898517f8b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73889184"
---
# <a name="use-an-existing-iot-hub-with-the-device-simulation-solution-accelerator"></a>Een bestaande IoT-hub gebruiken met de oplossingsverbetering voor apparaatsimulatie

Wanneer u apparaatsimulatie implementeert, u er ook voor kiezen om een IoT-hub te implementeren voor gebruik in uw simulatie. Met deze optie wordt een [S2-tier IoT-hub met één schaaleenheid geïmplementeerd.](../iot-hub/iot-hub-scaling.md) Als u deze optionele IoT-hub implementeert, u er nog steeds voor kiezen om een andere IoT-hub te targeten voor een simulatierun.

Als u ervoor kiest de optionele IoT Hub niet te implementeren, moet u uw eigen hub gebruiken voor alle simulaties die u uitvoert.

Als u geen IoT-hub hebt, u altijd een nieuwe hub maken vanuit de [Azure-portal.](https://portal.azure.com)

Als u een reeds bestaande IoT-hub wilt gebruiken, hebt u de verbindingstekenreeks nodig voor het beleid voor gedeelde toegang voor **iothub-eigenaar.** U deze verbindingstekenreeks ophalen via de [Azure-portal:](https://portal.azure.com)

1. Klik op de configuratiepagina van de hub in de portal op **Beleid voor gedeelde toegang**.

1. Klik **op iothubowner**.

1. Kopieer de primaire of secundaire verbindingstekenreeks.

[![Verbindingstekenreeks ophalen](./media/iot-accelerators-device-simulation-choose-hub/connectionstring-inline.png)](./media/iot-accelerators-device-simulation-choose-hub/connectionstring-expanded.png#lightbox)

Gebruik de verbindingstekenreeks die u hebt gekopieerd wanneer u de simulatie configureert:

![Simulatie configureren](./media/iot-accelerators-device-simulation-choose-hub/configuresimulation.png)

### <a name="next-steps"></a>Volgende stappen

In deze handleiding heb je geleerd hoe je een bestaande IoT-hub in een simulatie gebruiken. Vervolgens wilt u misschien leren hoe [u een geavanceerd apparaatmodel](iot-accelerators-device-simulation-advanced-device.md) maakt voor een simulatie.
