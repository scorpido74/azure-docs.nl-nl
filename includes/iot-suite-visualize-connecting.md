---
title: bestand opnemen
description: bestand opnemen
services: iot-suite
author: dominicbetts
ms.service: iot-suite
ms.topic: include
ms.date: 04/24/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 9b9e28f18208674609d0842b0e3a54e3fc661c9f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "67176469"
---
## <a name="view-device-telemetry"></a>Telemetrie van apparaten weergeven

U kunt de telemetrie die u vanaf uw apparaat hebt verzonden, bekijken op de **device Explorer** pagina in de oplossing.

1. Selecteer het apparaat dat u in de lijst met apparaten op de **device Explorer** pagina hebt ingericht. In een paneel wordt informatie over uw apparaat weer gegeven, met inbegrip van een tekening van de telemetrie van het apparaat:

    ![Details van apparaat bekijken](media/iot-suite-visualize-connecting/devicesdetail.png)

1. Kies **Druk** om de telemetrie-weer gave te wijzigen:

    ![Druk-telemetrie weer geven](media/iot-suite-visualize-connecting/devicespressure.png)

1. Als u diagnostische informatie over uw apparaat wilt weer geven, schuift u omlaag naar **Diagnostische**gegevens:

    ![Diagnostische gegevens over het apparaat weergeven](media/iot-suite-visualize-connecting/devicesdiagnostics.png)

## <a name="act-on-your-device"></a>Reageren op uw apparaat

Als u methoden op uw apparaten wilt aanroepen, gebruikt u de pagina **device Explorer** in de oplossing voor controle op afstand. Zo implementeert de **Chiller** -apparaten van de oplossing voor externe controle een methode voor **opnieuw opstarten** .

1. Kies **apparaten** om naar de **device Explorer** pagina in de oplossing te gaan.

1. Selecteer het apparaat dat u in de lijst met apparaten op de **device Explorer** pagina hebt ingericht:

    ![Uw echte apparaat selecteren](media/iot-suite-visualize-connecting/devicesselect.png)

1. Als u een lijst wilt weer geven met de methoden die u kunt aanroepen op uw apparaat, kiest u **taken**en vervolgens **methoden**. Als u een taak wilt plannen om op meerdere apparaten uit te voeren, kunt u meerdere apparaten selecteren in de lijst. In het deel venster **taken** ziet u de typen methode die gemeen schappelijk zijn voor alle apparaten die u hebt geselecteerd.

1. Kies **opnieuw opstarten**, stel de taak naam in op **RebootPhysicalChiller** en kies vervolgens **Toep assen**:

    ![De firmware-update plannen](media/iot-suite-visualize-connecting/deviceschedule.png)

1. Een reeks berichten wordt weer gegeven in de console met uw apparaatcode wanneer het gesimuleerde apparaat de methode verwerkt.

> [!NOTE]
> Kies **taak status weer geven**om de status van de taak in de oplossing bij te houden.

## <a name="next-steps"></a>Volgende stappen

In het artikel [de oplossings versneller voor externe controle aanpassen](../articles/iot-accelerators/iot-accelerators-remote-monitoring-customize.md) worden enkele manieren beschreven om de oplossings versneller aan te passen.