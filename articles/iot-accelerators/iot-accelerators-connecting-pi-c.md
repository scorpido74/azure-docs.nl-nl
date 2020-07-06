---
title: Raspberry Pi naar externe bewaking inrichten met C-Azure | Microsoft Docs
description: Hierin wordt beschreven hoe u een Raspberry Pi-apparaat verbindt met de oplossings versneller voor externe bewaking met behulp van een toepassing die is geschreven in C.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: dobett
ms.openlocfilehash: 3331db51f4d141cf142d1bd0578043ca6681f3cd
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "61454493"
---
# <a name="connect-your-raspberry-pi-device-to-the-remote-monitoring-solution-accelerator-c"></a>Sluit uw Raspberry Pi-apparaat aan op de oplossings versneller voor externe controle (C)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

In deze zelf studie leert u hoe u een echt apparaat verbindt met de oplossings versneller voor externe controle. Net als bij de meeste Inge sloten toepassingen die op beperkte apparaten worden uitgevoerd, wordt de client code voor de Raspberry Pi Device-toepassing geschreven in C. In deze zelf studie bouwt u de toepassing op een Raspberry Pi met het besturings systeem Raspbian.

Als u liever een apparaat simuleert, raadpleegt u [een nieuw gesimuleerd apparaat maken en testen](iot-accelerators-remote-monitoring-create-simulated-device.md).

### <a name="required-hardware"></a>Vereiste hardware

Een desktop computer waarmee u extern verbinding kunt maken met de opdracht regel op de Raspberry pi.

[Micro soft IOT Starter Kit voor Raspberry Pi 3](https://azure.microsoft.com/develop/iot/starter-kits/) of gelijkwaardige onderdelen. In deze zelf studie wordt gebruikgemaakt van de volgende items uit de kit:

- Raspberry Pi 3
- MicroSD-kaart (met NOOBS)
- Een USB-mini kabel
- Een Ethernet-kabel

### <a name="required-desktop-software"></a>Vereiste bureaublad software

U hebt SSH-client op uw computer nodig om u in staat te stellen op afstand toegang te krijgen tot de opdracht regel op de Raspberry pi.

- Windows bevat geen SSH-client. U kunt het beste [putty](https://www.putty.org/)gebruiken.
- De meeste Linux-distributies en Mac OS bevatten het opdracht regel SSH-hulp programma. Zie [SSH using Linux of Mac OS](https://www.raspberrypi.org/documentation/remote-access/ssh/unix.md)voor meer informatie.

### <a name="required-raspberry-pi-software"></a>Vereiste Raspberry Pi-software

In dit artikel wordt ervan uitgegaan dat u de nieuwste versie van het [Raspbian-besturings systeem hebt geïnstalleerd op uw Raspberry Pi](https://www.raspberrypi.org/learning/software-guide/quickstart/).

De volgende stappen laten zien hoe u uw Raspberry Pi voorbereidt voor het bouwen van een C-toepassing die verbinding maakt met de oplossings versneller:

1. Maak verbinding met uw Raspberry Pi met **SSH**. Zie [SSH (Secure Shell)](https://www.raspberrypi.org/documentation/remote-access/ssh/README.md) op de [Raspberry Pi-website](https://www.raspberrypi.org/)voor meer informatie.

1. Gebruik de volgende opdracht om uw Raspberry Pi bij te werken:

    ```sh
    sudo apt-get update
    ```

1. Volg de stappen in [uw Linux-ontwikkel omgeving instellen](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) om de vereiste ontwikkel hulpprogramma's en-bibliotheken toe te voegen aan uw Raspberry pi voor meer informatie over het uitvoeren van de stappen in deze hand leiding.

## <a name="view-the-code"></a>De code weer geven

De [voorbeeld code](https://github.com/Azure/azure-iot-sdk-c/tree/master/samples/solutions/remote_monitoring_client) die in deze hand leiding wordt gebruikt, is beschikbaar in de Azure IOT C sdk's github-opslag plaats.

### <a name="download-the-source-code-and-prepare-the-project"></a>De bron code downloaden en het project voorbereiden

Om het project voor te bereiden, moet u de [Azure IOT C-opslag plaats](https://github.com/Azure/azure-iot-sdk-c) van github klonen of downloaden.

Het voor beeld bevindt zich in de map **samples/Solutions/remote_monitoring_client** .

Open het bestand **remote_monitoring. c** in de map **samples/Solutions/remote_monitoring_client** in een tekst editor.

[!INCLUDE [iot-accelerators-connecting-code](../../includes/iot-accelerators-connecting-code.md)]

## <a name="build-and-run-the-application"></a>De toepassing bouwen en uitvoeren.

In de volgende stappen wordt beschreven hoe u *cmake* kunt gebruiken om de client toepassing te bouwen. De client toepassing voor externe controle is gebouwd als onderdeel van het bouw proces voor de SDK.

1. Bewerk het **remote_monitoring. c** -bestand dat `<connectionstring>` u wilt vervangen door het apparaat Connection String u hebt genoteerd aan het begin van deze hand leiding wanneer u een apparaat aan de oplossings versneller hebt toegevoegd.

1. Ga naar de hoofdmap van de gekloonde kopie van de [Azure IOT C-opslagplaats](https://github.com/Azure/azure-iot-sdk-c) opslagplaats en voer de volgende opdrachten uit om de client toepassing te bouwen:

    ```sh
    mkdir cmake
    cd cmake
    cmake ../
    make
    ```

1. Voer de client toepassing uit en verzend telemetrie naar IoT Hub:

    ```sh
    ./samples/solutions/remote_monitoring_client/remote_monitoring_client
    ```

    De console geeft berichten weer als:

    - De toepassing verzendt voor beeld-telemetrie naar de oplossings versneller.
    - Reageert op methoden die zijn aangeroepen vanuit het dash board van de oplossing.

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
