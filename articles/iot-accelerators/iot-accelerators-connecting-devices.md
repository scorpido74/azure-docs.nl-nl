---
title: Windows-apparaten inrichten voor bewaking op afstand in C - Azure | Microsoft Documenten
description: Beschrijft hoe u een apparaat aansluit op de accelerator voor externe bewaking-oplossingen met behulp van een toepassing die is geschreven in C die op Windows wordt uitgevoerd.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: dobett
ms.openlocfilehash: 2a8a0bf1e63f06bbe6b6a073af6b3da8904dcaeb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "61450212"
---
# <a name="connect-your-device-to-the-remote-monitoring-solution-accelerator-windows"></a>Sluit uw apparaat aan op de remote monitoring oplossingsversneller (Windows)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

In deze zelfstudie ziet u hoe u een echt apparaat aansluiten op de versneller van de oplossing op afstand.

Net als bij de meeste ingesloten toepassingen die op beperkte apparaten worden uitgevoerd, wordt de clientcode voor de apparaattoepassing in C geschreven. In deze zelfstudie bouwt u de apparaatclienttoepassing op een machine met Windows.

Zie Een nieuw gesimuleerd apparaat maken en testen als u een apparaat liever [simuleert.](iot-accelerators-remote-monitoring-create-simulated-device.md)

## <a name="prerequisites"></a>Vereisten

Als u de stappen in deze handleiding wilt uitvoeren, voert u de stappen uit bij [het instellen van uw Windows-ontwikkelomgeving](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#set-up-a-windows-development-environment) om de vereiste ontwikkeltools en -bibliotheken toe te voegen aan uw Windows-machine.

## <a name="view-the-code"></a>De code weergeven

De [voorbeeldcode](https://github.com/Azure/azure-iot-sdk-c/tree/master/samples/solutions/remote_monitoring_client) die in deze handleiding wordt gebruikt, is beschikbaar in de GitHub-repository van Azure IoT C SDKs.

### <a name="download-the-source-code-and-prepare-the-project"></a>Download de broncode en bereid het project voor

Als u het project wilt voorbereiden, [kloont u de Azure IoT C SDKs-repository](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#set-up-a-windows-development-environment) van GitHub.

Het voorbeeld bevindt zich in de map **samples/solutions/remote_monitoring_client.**

Open het **remote_monitoring.c-bestand** in de map **samples/solutions/remote_monitoring_client** in een teksteditor.

[!INCLUDE [iot-accelerators-connecting-code](../../includes/iot-accelerators-connecting-code.md)]

## <a name="build-and-run-the-sample"></a>Het voorbeeldproject compileren en uitvoeren

1. Bewerk het **remote_monitoring.c-bestand** dat u wilt vervangen `<connectionstring>` door de verbindingstekenreeks van het apparaat die u aan het begin van deze handleiding hebt opgemerkt wanneer u een apparaat aan de oplossingsversneller hebt toegevoegd.

1. Volg de stappen in [Build the C SDK in Windows](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#build-the-c-sdk-in-windows) om de SDK en de clienttoepassing voor externe bewaking te bouwen.

1. Op de opdrachtprompt die u gebruikt om de oplossing te bouwen, voer je het als:

    ```cmd
    samples\solutions\remote_monitoring_client\Release\remote_monitoring_client.exe
    ```

    Op de console worden berichten weergegeven als:

    - De toepassing stuurt voorbeeldtelemetrie naar de oplossingsversneller.
    - Reageert op methoden die worden aangeroepen vanuit het oplossingsdashboard.

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
