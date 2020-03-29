---
title: Linux-apparaten inrichten op bewaking op afstand in C - Azure | Microsoft Documenten
description: Beschrijft hoe u een apparaat aansluit op de accelerator voor externe bewaking-oplossingen met behulp van een toepassing die is geschreven in C die op Linux wordt uitgevoerd.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 08/31/2018
ms.author: dobett
ms.openlocfilehash: 91d4eda566c8b534daa10c62637db28ccb01bbb6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "61454488"
---
# <a name="connect-your-device-to-the-remote-monitoring-solution-accelerator-linux"></a>Sluit uw apparaat aan op de Remote Monitoring solution accelerator (Linux)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

In deze zelfstudie ziet u hoe u een echt apparaat aansluiten op de versneller van de oplossing op afstand.

Net als bij de meeste ingesloten toepassingen die op beperkte apparaten worden uitgevoerd, wordt de clientcode voor de apparaattoepassing in C geschreven. In deze zelfstudie bouw je de applicatie op een machine met Ubuntu (Linux).

Zie Een nieuw gesimuleerd apparaat maken en testen als u een apparaat liever [simuleert.](iot-accelerators-remote-monitoring-create-simulated-device.md)

## <a name="prerequisites"></a>Vereisten

Om de stappen in deze handleiding te voltooien, hebt u een apparaat nodig waarop Ubuntu-versie 15.04 of hoger wordt uitgevoerd. Voordat u verdergaat, [stelt u uw Linux-ontwikkelomgeving in.](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux)

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
