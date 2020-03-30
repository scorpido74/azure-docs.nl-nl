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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67176469"
---
## <a name="view-device-telemetry"></a>Telemetrie van apparaten weergeven

U de telemetrie die vanaf uw apparaat wordt verzonden, bekijken op de pagina **Device Explorer** in de oplossing.

1. Selecteer het apparaat dat u hebt ingericht in de lijst met apparaten op de pagina **Device Explorer.** Een paneel geeft informatie over uw apparaat weer, inclusief een plot van de telemetrie van het apparaat:

    ![Apparaatdetails bekijken](media/iot-suite-visualize-connecting/devicesdetail.png)

1. Kies **Druk** om het telemetriescherm te wijzigen:

    ![Druktelemetrie weergeven](media/iot-suite-visualize-connecting/devicespressure.png)

1. Als u diagnostische informatie over uw apparaat wilt weergeven, bladert u omlaag naar **Diagnostics:**

    ![Diagnostische gegevens over het apparaat weergeven](media/iot-suite-visualize-connecting/devicesdiagnostics.png)

## <a name="act-on-your-device"></a>Handelen op uw apparaat

Als u methoden op uw apparaten wilt aanroepen, gebruikt u de pagina **Device Explorer** in de oplossing voor externe bewaking. In de Remote Monitoring-oplossing implementeren **Chiller-apparaten** bijvoorbeeld een **Reboot-methode.**

1. Kies **Apparaten** om naar de pagina **Device Explorer** in de oplossing te navigeren.

1. Selecteer het apparaat dat u hebt ingerichte in de lijst met apparaten op de pagina **Device Explorer:**

    ![Selecteer uw echte apparaat](media/iot-suite-visualize-connecting/devicesselect.png)

1. Als u een lijst wilt weergeven met de methoden die u op uw apparaat aanroepen, kiest u **Vacatures**en **vervolgens Methoden**. Als u een taak wilt plannen die op meerdere apparaten moet worden uitgevoerd, u meerdere apparaten in de lijst selecteren. In het deelvenster **Taken** worden de typen methoden weergegeven die alle apparaten die u hebt geselecteerd, gemeen hebben.

1. Kies **Opnieuw opstarten,** stel de taaknaam in **op RebootPhysicalChiller** en kies **Vervolgens Toepassen:**

    ![De firmware-update plannen](media/iot-suite-visualize-connecting/deviceschedule.png)

1. Er wordt een reeks berichten weergegeven in de console waarop de apparaatcode wordt uitgevoerd terwijl het gesimuleerde apparaat de methode verwerkt.

> [!NOTE]
> Als u de status van de taak in de oplossing wilt bijhouden, kiest u **Taakstatus weergeven**.

## <a name="next-steps"></a>Volgende stappen

In het artikel [past u de versneller voor externe bewaking-oplossingen](../articles/iot-accelerators/iot-accelerators-remote-monitoring-customize.md) aan, worden enkele manieren beschreven om de oplossingsversneller aan te passen.