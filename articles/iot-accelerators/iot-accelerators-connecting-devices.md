---
title: Windows-apparaten inrichten voor externe bewaking in C-Azure | Microsoft Docs
description: Hierin wordt beschreven hoe u een apparaat verbindt met de oplossings versneller voor externe bewaking met behulp van een toepassing die is geschreven in C onder Windows.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: dobett
ms.openlocfilehash: 2a8a0bf1e63f06bbe6b6a073af6b3da8904dcaeb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "61450212"
---
# <a name="connect-your-device-to-the-remote-monitoring-solution-accelerator-windows"></a>Uw apparaat aansluiten op de oplossings versneller voor externe controle (Windows)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

In deze zelf studie leert u hoe u een echt apparaat verbindt met de oplossings versneller voor externe controle.

Net als bij de meeste Inge sloten toepassingen die op beperkte apparaten worden uitgevoerd, wordt de client code voor de apparaat-app geschreven in C. In deze zelf studie bouwt u de client toepassing van het apparaat op een computer waarop Windows wordt uitgevoerd.

Als u liever een apparaat simuleert, raadpleegt u [een nieuw gesimuleerd apparaat maken en testen](iot-accelerators-remote-monitoring-create-simulated-device.md).

## <a name="prerequisites"></a>Vereisten

Volg de stappen in [uw Windows-ontwikkel omgeving instellen](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#set-up-a-windows-development-environment) om de vereiste ontwikkel hulpprogramma's en-bibliotheken toe te voegen aan de Windows-computer om de stappen in deze hand leiding te volt ooien.

## <a name="view-the-code"></a>De code weer geven

De [voorbeeld code](https://github.com/Azure/azure-iot-sdk-c/tree/master/samples/solutions/remote_monitoring_client) die in deze hand leiding wordt gebruikt, is beschikbaar in de Azure IOT C sdk's github-opslag plaats.

### <a name="download-the-source-code-and-prepare-the-project"></a>De bron code downloaden en het project voorbereiden

Als u het project wilt voorbereiden, [kloont u de Azure IOT C-opslag plaats voor sdk's](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#set-up-a-windows-development-environment) vanuit github.

Het voor beeld bevindt zich in de map **samples/Solutions/remote_monitoring_client** .

Open het bestand **remote_monitoring. c** in de map **samples/Solutions/remote_monitoring_client** in een tekst editor.

[!INCLUDE [iot-accelerators-connecting-code](../../includes/iot-accelerators-connecting-code.md)]

## <a name="build-and-run-the-sample"></a>Het voorbeeldproject compileren en uitvoeren

1. Bewerk het **remote_monitoring. c** -bestand dat `<connectionstring>` u wilt vervangen door het apparaat Connection String u hebt genoteerd aan het begin van deze hand leiding wanneer u een apparaat aan de oplossings versneller hebt toegevoegd.

1. Volg de stappen in [Build the C SDK in Windows](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#build-the-c-sdk-in-windows) om de SDK en de client toepassing voor externe bewaking te maken.

1. Voer de volgende opdracht uit om de oplossing te bouwen:

    ```cmd
    samples\solutions\remote_monitoring_client\Release\remote_monitoring_client.exe
    ```

    De console geeft berichten weer als:

    - De toepassing verzendt voor beeld-telemetrie naar de oplossings versneller.
    - Reageert op methoden die zijn aangeroepen vanuit het dash board van de oplossing.

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
