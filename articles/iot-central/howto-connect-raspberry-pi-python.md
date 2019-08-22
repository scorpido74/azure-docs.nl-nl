---
title: Een Raspberry Pi verbinden met uw Azure IoT Central-toepassing (python) | Microsoft Docs
description: Hoe u als ontwikkelaar van een apparaat een Raspberry Pi verbindt met uw Azure IoT Central-toepassing met behulp van python.
author: dominicbetts
ms.author: dobett
ms.date: 04/05/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: bd506bf1210692feb017f3b526c3b6d4bca36004
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/21/2019
ms.locfileid: "69877426"
---
# <a name="connect-a-raspberry-pi-to-your-azure-iot-central-application-python"></a>Een Raspberry Pi verbinden met uw Azure IoT Central-toepassing (python)

[!INCLUDE [howto-raspberrypi-selector](../../includes/iot-central-howto-raspberrypi-selector.md)]

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

In dit artikel wordt beschreven hoe u als een ontwikkelaar van een apparaat een Raspberry Pi verbindt met uw Microsoft Azure IoT Central toepassing met behulp van de python-programmeer taal.

## <a name="before-you-begin"></a>Voordat u begint

U hebt de volgende onderdelen nodig om de stappen in dit artikel uit te voeren:

* Een Azure IoT Central-toepassing gemaakt op basis van de voor beeld-toepassings sjabloon **Devkits** . Zie voor meer informatie de [snelstart over het maken van een toepassing](quick-deploy-iot-central.md).
* Een Raspberry Pi-apparaat met het Raspbian-besturings systeem. De Raspberry Pi moet verbinding kunnen maken met internet. Zie [uw Raspberry Pi instellen](https://projects.raspberrypi.org/en/projects/raspberry-pi-setting-up/3)voor meer informatie.

## <a name="sample-devkits-application"></a>Voor **beeld van Devkits** -toepassing

Een toepassing die is gemaakt op basis van de voor **beeld-Devkits** -toepassings sjabloon bevat een **Raspberry Pi** -sjabloon met de volgende kenmerken:

- Telemetrie, met inbegrip van de volgende metingen die door het apparaat worden verzameld:
  - Vochtigheid
  - Temperatuur
  - Druk
  - Magnetometer (X, Y, Z)
  - Versnellings meter (X, Y, Z)
  - Gyroscope (X, Y, Z)
- Instellingen
  - Verbruik
  - Huidige
  - Snelheid van ventilator
  - Scha kelen tussen IR.
- properties
  - Eigenschap van het aantal apparaten van de dobbel steen
  - Eigenschap Location Cloud

Zie de [Raspberry Pi-Details](howto-connect-raspberry-pi-python.md#raspberry-pi-device-template-details)van het apparaat voor meer informatie over de configuratie van de sjabloon.

## <a name="add-a-real-device"></a>Echt apparaat toevoegen

Voeg in uw Azure IoT Central-toepassing een echt apparaat toe vanuit de sjabloon **Raspberry Pi** . Noteer de verbindings Details van het apparaat (**scope-id**, **apparaat-id**en **primaire sleutel**). Zie [een echt apparaat toevoegen aan uw Azure IOT Central-toepassing](tutorial-add-device.md)voor meer informatie.

### <a name="configure-the-raspberry-pi"></a>De Raspberry Pi configureren

In de volgende stappen wordt beschreven hoe u de voor beeld-python-toepassing downloadt en configureert vanuit GitHub. Deze voorbeeld toepassing:

* Verzendt telemetrie en eigenschaps waarden naar Azure IoT Central.
* Reageert op het instellen van wijzigingen die zijn aangebracht in azure IoT Central.

Als u het apparaat wilt configureren, [volgt u de stapsgewijze instructies op github](https://github.com/Azure/iot-central-firmware/blob/master/RaspberryPi/README.md).

1. Wanneer het apparaat is geconfigureerd, wordt het verzenden van telemetrie-metingen naar Azure IoT Central gestart op het apparaat.
1. In uw Azure IoT Central-toepassing kunt u zien hoe de code die wordt uitgevoerd op de Raspberry Pi communiceert met de toepassing:

    * Op de pagina **metingen** voor uw echte apparaat ziet u de telemetrie die is verzonden vanuit de Raspberry pi.
    * Op de pagina **instellingen** kunt u de instellingen wijzigen op de Raspberry Pi, zoals spanning en ventilator snelheid. Wanneer de Raspberry Pi de wijziging bevestigt, wordt de instelling weer gegeven als **gesynchroniseerd**.

## <a name="raspberry-pi-device-template-details"></a>Details van Raspberry Pi-apparaatprofiel

Een toepassing die is gemaakt op basis van de voor **beeld-Devkits** -toepassings sjabloon bevat een **Raspberry Pi** -sjabloon met de volgende kenmerken:

### <a name="telemetry-measurements"></a>Telemetrische metingen

| Veldnaam     | Eenheden  | Minimum | Maximum | Aantal decimalen |
| -------------- | ------ | ------- | ------- | -------------- |
| vochtigheid       | %      | 0       | 100     | 0              |
| temp           | °C     | -40     | 120     | 0              |
| pressure       | hPa    | 260     | 1260    | 0              |
| magnetometerX  | mgauss | -1000   | 1000    | 0              |
| magnetometerY  | mgauss | -1000   | 1000    | 0              |
| magnetometerZ  | mgauss | -1000   | 1000    | 0              |
| accelerometerX | mg     | -2000   | 2000    | 0              |
| versnellings meter | mg     | -2000   | 2000    | 0              |
| accelerometerZ | mg     | -2000   | 2000    | 0              |
| gyroscopeX     | mdps   | -2000   | 2000    | 0              |
| gyroscopeY     | mdps   | -2000   | 2000    | 0              |
| gyroscopeZ     | mdps   | -2000   | 2000    | 0              |

### <a name="settings"></a>Instellingen

Numerieke instellingen

| `Display name` | Veldnaam | Eenheden | Aantal decimalen | Minimum | Maximum | Oorspronkelijk |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| Verbruik      | setVoltage | Volt | 0              | 0       | 240     | 0       |
| Huidige      | setCurrent | Amp  | 0              | 0       | 100     | 0       |
| Snelheid van ventilator    | fanSpeed   | RPM   | 0              | 0       | 1000    | 0       |

Instellingen in-/uitschakelen

| `Display name` | Veldnaam | In tekst | Uit tekst | Oorspronkelijk |
| ------------ | ---------- | ------- | -------- | ------- |
| IR           | activateIR | AAN      | UIT      | Uit     |

### <a name="properties"></a>properties

| type            | `Display name` | Veldnaam | Gegevenstype |
| --------------- | ------------ | ---------- | --------- |
| Eigenschap apparaat | Aantal dobbelten   | dieNumber  | nummer    |
| Text            | Location     | location   | N/A       |

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u een Raspberry Pi verbindt met uw Azure IoT Central-toepassing, is de voorgestelde volgende stap informatie over het [instellen van een sjabloon voor aangepaste apparaten](howto-set-up-template.md) voor uw eigen IOT-apparaat.
