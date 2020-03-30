---
title: 'Snelstartgids: Een apparaat beheren vanuit Azure IoT Hub (Python) | Microsoft Docs'
description: In deze snelstartgids gaan we twee voorbeeldtoepassingen geschreven in Python uitvoeren. De ene toepassing is een back-endtoepassing waarmee u op afstand apparaten kunt beheren die zijn verbonden met uw hub. De andere toepassing simuleert een apparaat dat is verbonden met uw hub en dat op afstand kan worden beheerd.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: quickstart
ms.custom: mvc
ms.date: 01/09/2020
ms.openlocfilehash: 0ff404e4b13fdb3e174387c17064c5ae58acd246
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "78675553"
---
# <a name="quickstart-control-a-device-connected-to-an-iot-hub-python"></a>Snelstartgids: Een apparaat beheren dat is verbonden met een IoT-hub (Python)

[!INCLUDE [iot-hub-quickstarts-2-selector](../../includes/iot-hub-quickstarts-2-selector.md)]

In deze quickstart gebruikt u een directe methode om een gesimuleerd apparaat te bedienen dat is verbonden met Azure IoT Hub. IoT Hub is een Azure-service waarmee u uw IoT-apparaten vanuit de cloud beheren en grote hoeveelheden apparaattelemetrie naar de cloud innemen voor opslag of verwerking. U kunt directe methoden gebruiken om het gedrag van een apparaat dat is verbonden met uw IoT-hub, op afstand te wijzigen. Deze quickstart maakt gebruik van twee Python-toepassingen: een gesimuleerde apparaattoepassing die reageert op directe methoden die worden aangeroepen vanuit een back-endtoepassing en een back-endtoepassing die de directe methoden op het gesimuleerde apparaat aanroept.

## <a name="prerequisites"></a>Vereisten

* Een Azure-account met een actief abonnement. [Maak er gratis een.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

* [Python 3.7+](https://www.python.org/downloads/). Zie [Azure IoT-apparaatfuncties](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device#azure-iot-device-features)voor andere versies van Python.

* [Een voorbeeld python-project](https://github.com/Azure-Samples/azure-iot-samples-python/archive/master.zip).

* Poort 8883 wordt geopend in uw firewall. Het apparaatvoorbeeld in deze quickstart maakt gebruik van het MQTT-protocol, dat communiceert via poort 8883. Deze poort kan worden geblokkeerd in sommige bedrijfs- en educatieve netwerkomgevingen. Zie [Verbinding maken met IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)voor meer informatie en manieren om dit probleem te omzeilen.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

### <a name="add-azure-iot-extension"></a>Azure IoT-extensie toevoegen

Voer de volgende opdracht uit om de Microsoft Azure IoT-extensie voor Azure CLI toe te voegen aan uw Cloud Shell-exemplaar. De IoT-extensie voegt specifieke opdrachten voor IoT Hub, IoT Edge en IoT Device Provisioning Service (DPS) toe aan Azure CLI.

```azurecli-interactive
az extension add --name azure-iot
```

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Een IoT Hub maken

U kunt deze stap overslaan als u eerder deze zelfstudie hebt voltooid: [Snelstartgids: Telemetriegegevens vanaf een apparaat verzenden naar een IoT-hub en de telemetriegegevens op de hub lezen met een back-endtoepassing (Python)](quickstart-send-telemetry-python.md).

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Een apparaat registreren

U kunt deze stap overslaan als u eerder deze zelfstudie hebt voltooid: [Snelstartgids: Telemetriegegevens vanaf een apparaat verzenden naar een IoT-hub en de telemetriegegevens op de hub lezen met een back-endtoepassing (Python)](quickstart-send-telemetry-python.md).

Een apparaat moet zijn geregistreerd bij uw IoT-hub voordat het verbinding kan maken. In deze snelstart gebruikt u Azure Cloud Shell om een gesimuleerd apparaat te registreren.

1. Voer de volgende opdracht uit in Azure Cloud Shell om de apparaatidentiteit te maken.

    **YourIoTHubName**: vervang deze tijdelijke aanduiding door een door u gekozen naam voor de IoT-hub.

    **MyPythonDevice**: Dit is de naam van het apparaat dat u registreert. Het wordt aanbevolen om **MyPythonDevice** te gebruiken zoals getoond. Als u een andere naam voor uw apparaat kiest, moet u die naam ook in dit artikel gebruiken en de apparaatnaam bijwerken in de voorbeeldtoepassingen voordat u ze uitvoert.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyPythonDevice
    ```

2. Voer de volgende opdrachten uit in Azure Cloud Shell om de _apparaatverbindingsreeks_ op te halen voor het apparaat dat u zojuist hebt geregistreerd:

    **YourIoTHubName**: vervang deze tijdelijke aanduiding door een door u gekozen naam voor de IoT-hub.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyPythonDevice --output table
    ```

    Noteer de apparaatverbindingsreeks. Deze ziet er ongeveer als volgt uit:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    U gebruikt deze waarde verderop in de snelstartgids.

3. U hebt ook een _service-verbindingsreeks_ nodig, zodat de back-end-toepassing verbinding kan maken met de IoT-hub en de berichten kan ophalen. Met de volgende opdracht haalt u de serviceverbindingsreeks voor uw IoT-hub op:

    **YourIoTHubName**: vervang deze tijdelijke aanduiding door een door u gekozen naam voor de IoT-hub.

    ```azurecli-interactive
    az iot hub show-connection-string \
      --policy-name service \
      --name {YourIoTHubName} \
      --output table
    ```

    Noteer de serviceverbindingsreeks. Deze ziet er ongeveer als volgt uit:

   `HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}`

    U gebruikt deze waarde verderop in de snelstartgids. Deze tekenreeks voor serviceverbinding verschilt van de tekenreeks van de apparaatverbinding die u in de vorige stap hebt opgemerkt.

## <a name="listen-for-direct-method-calls"></a>Luisteren naar aanroepen van directe methoden

De toepassing voor het gesimuleerde apparaat maakt verbinding met een apparaatspecifiek eindpunt op uw IoT-hub, verstuurt gesimuleerde telemetrie en luistert naar aanroepen van directe methoden vanuit de hub. In deze snelstartgids geeft de aanroep van de directe methode vanuit de hub het apparaat opdracht om het interval voor het verzenden van telemetrie te wijzigen. Het gesimuleerde apparaat stuurt een bevestiging terug naar uw hub nadat de directe methode is uitgevoerd.

1. Navigeer in een lokaal terminalvenster naar de hoofdmap van het voorbeeldproject in Python. Navigeer vervolgens naar de map **iot-hub\Quickstarts\simulated-device-2**.

1. Open het bestand **SimulatedDevice.py** in een teksteditor van uw keuze.

    Vervang de waarde `CONNECTION_STRING` van de variabele door de verbindingstekenreeks van het apparaat waar u eerder een notitie van hebt gemaakt. Sla uw wijzigingen vervolgens op **in SimulatedDevice.py.**

1. Voer in het lokale terminalvenster de volgende opdrachten uit om de vereiste bibliotheken te installeren voor de toepassing voor het gesimuleerde apparaat:

    ```cmd/sh
    pip install azure-iot-device
    ```

1. Voer in het lokale terminalvenster de volgende opdrachten uit om de toepassing voor het gesimuleerde apparaat uit te voeren:

    ```cmd/sh
    python SimulatedDevice.py
    ```

    In de volgende schermafbeelding ziet u de uitvoer op het moment dat de toepassing voor het gesimuleerde apparaat telemetriegegevens naar uw IoT-hub verzendt:

    ![Het gesimuleerde apparaat uitvoeren](./media/quickstart-control-device-python/simulated-device-1.png)

## <a name="call-the-direct-method"></a>De directe methode aanroepen

De back-endtoepassing maakt verbinding met een eindpunt aan de servicezijde van uw IoT-hub. De toepassing voert rechtstreeks door dat u een apparaat aanbelt via uw IoT-hub en luistert naar bevestigingen. Een back-endtoepassing van IoT Hub wordt meestal in de cloud uitgevoerd.

1. Navigeer in een ander lokaal terminalvenster naar de hoofdmap van het voorbeeldproject in Python. Navigeer vervolgens naar de map **iot-hub\Quickstarts\back-end-application**.

1. Open het bestand **BackEndApplication.py** in een teksteditor van uw keuze.

    Vervang de waarde `CONNECTION_STRING` van de variabele door de tekenreeks serviceverbinding waar u eerder een notitie van hebt gemaakt. Sla uw wijzigingen vervolgens op **in BackEndApplication.py.**

1. Voer in het lokale terminalvenster de volgende opdrachten uit om de vereiste bibliotheken te installeren voor de toepassing voor het gesimuleerde apparaat:

    ```cmd/sh
    pip install azure-iot-hub
    ```

1. Voer in het lokale terminalvenster de volgende opdrachten uit om de back-endtoepassing uit te voeren:

    ```cmd/sh
    python BackEndApplication.py
    ```

    De volgende schermafbeelding toont de uitvoer terwijl de toepassing een directe methodeaanroep naar het apparaat aanroept en een bevestiging ontvangt:

    ![De back-endtoepassing uitvoeren](./media/quickstart-control-device-python/backend-application.png)

    Nadat u de back-endtoepassing hebt uitgevoerd, ziet u een bericht in het consolevenster dat het gesimuleerde apparaat wordt uitgevoerd, en dat het interval voor het verzenden van berichten is gewijzigd:

    ![Wijziging in gesimuleerde client](./media/quickstart-control-device-python/simulated-device-2.png)

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Volgende stappen

In deze snelstartgids hebt u vanuit een back-endtoepassing een directe methode op een apparaat aangeroepen en op de aanroep van de directe methode gereageerd in een toepassing voor een gesimuleerd apparaat.

Ga verder met de volgende zelfstudie als u wilt leren hoe u berichten van een apparaat naar andere bestemmingen in de cloud kunt routeren.

> [!div class="nextstepaction"]
> [Zelfstudie: Routeren van telemetriegegevens naar verschillende eindpunten voor verwerking](tutorial-routing.md)