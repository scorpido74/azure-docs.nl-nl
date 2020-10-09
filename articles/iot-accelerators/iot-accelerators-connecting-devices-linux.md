---
title: Linux-apparaten inrichten voor externe bewaking in C-Azure | Microsoft Docs
description: Hierin wordt beschreven hoe u een apparaat verbindt met de oplossings versneller voor externe bewaking met behulp van een toepassing die is geschreven in C onder Linux.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 08/31/2018
ms.author: dobett
ms.openlocfilehash: 91d4eda566c8b534daa10c62637db28ccb01bbb6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "61454488"
---
# <a name="connect-your-device-to-the-remote-monitoring-solution-accelerator-linux"></a>Uw apparaat verbinden met de externe controle oplossings versneller (Linux)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

In deze zelf studie leert u hoe u een echt apparaat verbindt met de oplossings versneller voor externe controle.

Net als bij de meeste Inge sloten toepassingen die op beperkte apparaten worden uitgevoerd, wordt de client code voor de apparaat-app geschreven in C. In deze zelf studie bouwt u de toepassing op een computer met Ubuntu (Linux).

Als u liever een apparaat simuleert, raadpleegt u [een nieuw gesimuleerd apparaat maken en testen](iot-accelerators-remote-monitoring-create-simulated-device.md).

## <a name="prerequisites"></a>Vereisten

Als u de stappen in deze hand leiding wilt uitvoeren, hebt u een apparaat met Ubuntu versie 15,04 of hoger nodig. Voordat u doorgaat, [moet u uw Linux-ontwikkel omgeving instellen](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux).

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
