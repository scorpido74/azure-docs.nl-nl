---
title: Raspberry Pi inrichten voor bewaking op afstand met C - Azure | Microsoft Documenten
description: Beschrijft hoe u een Raspberry Pi-apparaat aansluit op de remote monitoring-oplossingsversneller met behulp van een toepassing die in C is geschreven.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: dobett
ms.openlocfilehash: 3331db51f4d141cf142d1bd0578043ca6681f3cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "61454493"
---
# <a name="connect-your-raspberry-pi-device-to-the-remote-monitoring-solution-accelerator-c"></a>Sluit uw Raspberry Pi-apparaat aan op de Remote Monitoring solution accelerator (C)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

In deze zelfstudie ziet u hoe u een echt apparaat aansluiten op de versneller van de oplossing op afstand. Zoals bij de meeste ingesloten toepassingen die op apparaten met beperkte beperkingen worden uitgevoerd, wordt de clientcode voor de Raspberry Pi-apparaattoepassing in C geschreven. In deze zelfstudie bouw je de applicatie op een Raspberry Pi met het Raspbian OS.

Zie Een nieuw gesimuleerd apparaat maken en testen als u een apparaat liever [simuleert.](iot-accelerators-remote-monitoring-create-simulated-device.md)

### <a name="required-hardware"></a>Vereiste hardware

Een desktopcomputer waarmee u op afstand verbinding maken met de opdrachtregel op de Raspberry Pi.

[Microsoft IoT Starter Kit voor Raspberry Pi 3](https://azure.microsoft.com/develop/iot/starter-kits/) of gelijkwaardige componenten. In deze zelfstudie worden de volgende items uit de kit gebruikt:

- Raspberry Pi 3
- MicroSD-kaart (met NOOBS)
- Een USB Mini-kabel
- Een Ethernet-kabel

### <a name="required-desktop-software"></a>Vereiste desktopsoftware

U hebt SSH-client op uw desktopmachine nodig om op afstand toegang te krijgen tot de opdrachtregel op de Raspberry Pi.

- Windows bevat geen SSH-client. Wij raden u aan [PuTTY te](https://www.putty.org/)gebruiken.
- De meeste Linux-distributies en Mac OS bevatten het ssh-hulpprogramma voor de command-line. Zie [SSH Gebruiken met Linux of Mac OS](https://www.raspberrypi.org/documentation/remote-access/ssh/unix.md)voor meer informatie.

### <a name="required-raspberry-pi-software"></a>Vereiste Raspberry Pi-software

In dit artikel wordt ervan uitgegaan dat u de nieuwste versie van het [Raspbian OS op uw Raspberry Pi](https://www.raspberrypi.org/learning/software-guide/quickstart/)hebt geïnstalleerd.

De volgende stappen laten zien hoe u uw Raspberry Pi voorbereidt op het bouwen van een C-toepassing die verbinding maakt met de oplossingsversneller:

1. Maak verbinding met je Raspberry Pi met **ssh.** Zie [SSH (Secure Shell)](https://www.raspberrypi.org/documentation/remote-access/ssh/README.md) op de [Raspberry Pi-website](https://www.raspberrypi.org/)voor meer informatie.

1. Gebruik de volgende opdracht om je Raspberry Pi bij te werken:

    ```sh
    sudo apt-get update
    ```

1. Om de stappen in deze handleiding te voltooien, volg je de stappen in het [instellen van je Linux-ontwikkelomgeving](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) om de vereiste ontwikkeltools en -bibliotheken toe te voegen aan je Raspberry Pi.

## <a name="view-the-code"></a>De code weergeven

De [voorbeeldcode](https://github.com/Azure/azure-iot-sdk-c/tree/master/samples/solutions/remote_monitoring_client) die in deze handleiding wordt gebruikt, is beschikbaar in de GitHub-repository van Azure IoT C SDKs.

### <a name="download-the-source-code-and-prepare-the-project"></a>Download de broncode en bereid het project voor

Als u het project wilt voorbereiden, kloont of downloadt u de [Azure IoT C SDKs-repository](https://github.com/Azure/azure-iot-sdk-c) van GitHub.

Het voorbeeld bevindt zich in de map **samples/solutions/remote_monitoring_client.**

Open het **remote_monitoring.c-bestand** in de map **samples/solutions/remote_monitoring_client** in een teksteditor.

[!INCLUDE [iot-accelerators-connecting-code](../../includes/iot-accelerators-connecting-code.md)]

## <a name="build-and-run-the-application"></a>De toepassing bouwen en uitvoeren.

In de volgende stappen wordt beschreven hoe *u CMake kunt* gebruiken om de clienttoepassing te bouwen. De clienttoepassing voor externe bewaking is gebouwd als onderdeel van het buildproces voor de SDK.

1. Bewerk het **remote_monitoring.c-bestand** dat u wilt vervangen `<connectionstring>` door de verbindingstekenreeks van het apparaat die u aan het begin van deze handleiding hebt opgemerkt wanneer u een apparaat aan de oplossingsversneller hebt toegevoegd.

1. Navigeer naar de hoofdmap van uw gekloonde kopie van de [Azure IoT C SDKs-repository](https://github.com/Azure/azure-iot-sdk-c) en voer de volgende opdrachten uit om de clienttoepassing te bouwen:

    ```sh
    mkdir cmake
    cd cmake
    cmake ../
    make
    ```

1. Voer de clienttoepassing uit en stuur telemetrie naar IoT Hub:

    ```sh
    ./samples/solutions/remote_monitoring_client/remote_monitoring_client
    ```

    Op de console worden berichten weergegeven als:

    - De toepassing stuurt voorbeeldtelemetrie naar de oplossingsversneller.
    - Reageert op methoden die worden aangeroepen vanuit het oplossingsdashboard.

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
