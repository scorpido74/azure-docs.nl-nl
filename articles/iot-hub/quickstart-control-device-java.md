---
title: 'Snelstartgids: een apparaat beheren vanuit Azure IoT Hub met Java'
description: In deze snelstartgids gaan we twee in Java geschreven voorbeeldtoepassingen uitvoeren. De ene toepassing is een back-endtoepassing waarmee u op afstand apparaten kunt beheren die zijn verbonden met uw hub. De andere toepassing simuleert een apparaat dat is verbonden met uw hub en dat op afstand kan worden beheerd.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: quickstart
ms.custom: mvc, seo-java-august2019, seo-java-september2019
ms.date: 06/21/2019
ms.openlocfilehash: 96d30d283ec1565e512be77f55d15305fa226db4
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/06/2020
ms.locfileid: "78675353"
---
# <a name="quickstart-control-a-device-connected-to-an-azure-iot-hub-with-java"></a>Snelstartgids: een apparaat beheren dat is verbonden met een Azure IoT hub met Java

[!INCLUDE [iot-hub-quickstarts-2-selector](../../includes/iot-hub-quickstarts-2-selector.md)]

In deze Quick Start gebruikt u een directe methode voor het beheren van een gesimuleerd apparaat dat is verbonden met Azure IoT Hub met een Java-toepassing. IoT Hub is een Azure-service waarmee u uw IoT-apparaten kunt beheren vanuit de Cloud en grote hoeveel heden apparaat-telemetrie kunt opnemen in de Cloud voor opslag of verwerking. U kunt directe methoden gebruiken om het gedrag van een apparaat dat is verbonden met uw IoT-hub, op afstand te wijzigen. In deze Quick Start worden twee Java-toepassingen gebruikt: een gesimuleerde apparaat-app die reageert op directe methoden die worden aangeroepen vanuit een back-endtoepassing en een service toepassing die de directe methode op het gesimuleerde apparaat aanroept.

## <a name="prerequisites"></a>Vereisten

* Een Azure-account met een actief abonnement. [Maak er gratis een](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

* Java SE Development Kit 8. In [Java-ondersteuning op lange termijn voor Azure en Azure stack](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable), onder **lange termijn ondersteuning**, selecteert u **Java 8**.

* [Apache Maven 3](https://maven.apache.org/download.cgi).

* [Een voor beeld van een Java-project](https://github.com/Azure-Samples/azure-iot-samples-java/archive/master.zip).

* Poort 8883 is geopend in de firewall. Het voor beeld van het apparaat in deze Snelstartgids maakt gebruik van het MQTT-protocol, dat communiceert via poort 8883. Deze poort kan worden geblokkeerd in sommige bedrijfs-en educatieve netwerk omgevingen. Zie [verbinding maken met IOT hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)voor meer informatie en manieren om dit probleem te omzeilen.

Gebruik de volgende opdracht om de huidige versie van Java op uw ontwikkelcomputer te controleren:

```cmd/sh
java -version
```

Gebruik de volgende opdracht om de huidige versie van Maven op uw ontwikkelcomputer te controleren:

```cmd/sh
mvn --version
```

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

### <a name="add-azure-iot-extension"></a>Azure IoT-extensie toevoegen

Voer de volgende opdracht uit om de Microsoft Azure IoT-extensie voor Azure CLI toe te voegen aan uw Cloud Shell-exemplaar. De IoT-extensie voegt IoT Hub, IoT Edge en IoT Device Provisioning Service (DPS)-specifieke opdrachten toe aan Azure CLI.

```azurecli-interactive
az extension add --name azure-iot
```

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Een IoT Hub maken

U kunt deze stap overslaan als u eerder deze zelfstudie hebt voltooid: [Snelstartgids: Telemetriegegevens vanaf een apparaat verzenden naar een IoT-hub en de telemetriegegevens op de hub lezen met een back-endtoepassing (Node.js)](quickstart-send-telemetry-java.md).

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Een apparaat registreren

U kunt deze stap overslaan als u eerder deze zelfstudie hebt voltooid: [Snelstartgids: Telemetriegegevens vanaf een apparaat verzenden naar een IoT-hub en de telemetriegegevens op de hub lezen met een back-endtoepassing (Node.js)](quickstart-send-telemetry-java.md).

Een apparaat moet zijn geregistreerd bij uw IoT-hub voordat het verbinding kan maken. In deze snelstart gebruikt u Azure Cloud Shell om een gesimuleerd apparaat te registreren.

1. Voer de volgende opdracht uit in Azure Cloud Shell om de apparaat-id te maken.

   **YourIoTHubName**: vervang deze tijdelijke aanduiding door een door u gekozen naam voor de IoT-hub.

   **MyJavaDevice**: dit is de naam van het apparaat dat u wilt registreren. Het is raadzaam om **MyJavaDevice** te gebruiken zoals wordt weer gegeven. Als u een andere naam kiest voor uw apparaat, moet u deze naam ook in dit artikel gebruiken en de apparaatnaam bijwerken in de voorbeeld toepassingen voordat u ze uitvoert.

    ```azurecli-interactive
    az iot hub device-identity create \
      --hub-name {YourIoTHubName} --device-id MyJavaDevice
    ```

2. Voer de volgende opdrachten uit in Azure Cloud Shell om de _apparaatverbindingsreeks_ op te halen voor het apparaat dat u zojuist hebt geregistreerd:

   **YourIoTHubName**: vervang deze tijdelijke aanduiding door een door u gekozen naam voor de IoT-hub.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string \
      --hub-name {YourIoTHubName} \
      --device-id MyJavaDevice \
      --output table
    ```

    Noteer de apparaatverbindingsreeks, die er ongeveer zo uitziet:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    U gebruikt deze waarde verderop in de snelstartgids.

## <a name="retrieve-the-service-connection-string"></a>De verbindingsreeks voor de service ophalen

U hebt ook een _service-verbindingsreeks_ nodig, zodat de back-end-toepassing verbinding kan maken met de IoT-hub en de berichten kan ophalen. Met de volgende opdracht haalt u de serviceverbindingsreeks voor uw IoT-hub op:

**YourIoTHubName**: vervang deze tijdelijke aanduiding door een door u gekozen naam voor de IoT-hub.

```azurecli-interactive
az iot hub show-connection-string --policy-name service --name {YourIoTHubName} --output table
```

Noteer de serviceverbindingsreeks, die er ongeveer zo uitziet:

`HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}`

U gebruikt deze waarde verderop in de snelstartgids. Deze service connection string wijkt af van het apparaat connection string dat u in de vorige stap hebt genoteerd.

## <a name="listen-for-direct-method-calls"></a>Luisteren naar aanroepen van directe methoden

De toepassing voor het gesimuleerde apparaat maakt verbinding met een apparaatspecifiek eindpunt op uw IoT-hub, verstuurt gesimuleerde telemetrie en luistert naar aanroepen van directe methoden vanuit de hub. In deze snelstartgids geeft de aanroep van de directe methode vanuit de hub het apparaat opdracht om het interval voor het verzenden van telemetrie te wijzigen. Het gesimuleerde apparaat verzendt een bevestiging terug naar uw hub nadat de directe methode is uitgevoerd.

1. Navigeer in een lokaal terminalvenster naar de hoofdmap van het voorbeeldproject in Java. Navigeer vervolgens naar de map **iot-hub\Quickstarts\simulated-device-2**.

2. Open het bestand **src/main/java/com/microsoft/docs/iothub/samples/SimulatedDevice.java** in een teksteditor van uw keuze.

    Vervang de waarde van de variabele `connString` door het apparaat connection string u eerder een notitie hebt gemaakt. Sla de wijzigingen vervolgens op in **SimulatedDevice. java**.

3. Voer in het lokale terminalvenster de volgende opdrachten uit om de vereiste bibliotheken te installeren en de toepassing voor het gesimuleerde apparaat te compileren:

    ```cmd/sh
    mvn clean package
    ```

4. Voer in het lokale terminalvenster de volgende opdrachten uit om de toepassing voor het gesimuleerde apparaat uit te voeren:

    ```cmd/sh
    java -jar target/simulated-device-2-1.0.0-with-deps.jar
    ```

    In de volgende schermafbeelding ziet u de uitvoer op het moment dat de toepassing voor het gesimuleerde apparaat telemetriegegevens naar uw IoT-hub verzendt:

    ![Uitvoer van de telemetrie die door het apparaat wordt verzonden naar uw IoT-hub](./media/quickstart-control-device-java/iot-hub-application-send-telemetry-output.png)

## <a name="call-the-direct-method"></a>De directe methode aanroepen

De back-endtoepassing maakt verbinding met een eindpunt aan de servicezijde van uw IoT-hub. De toepassing maakt directe methode aanroepen naar een apparaat via uw IoT-hub en luistert naar bevestigingen. Een back-endtoepassing van IoT Hub wordt meestal in de cloud wordt uitgevoerd.

1. Navigeer in een ander lokaal terminalvenster naar de hoofdmap van het voorbeeldproject in Java. Navigeer vervolgens naar de map **iot-hub\Quickstarts\back-end-application**.

2. Open het bestand **src/main/java/com/microsoft/docs/iothub/samples/BackEndApplication.java** in een teksteditor van uw keuze.

    Vervang de waarde van de variabele `iotHubConnectionString` door de service connection string u eerder een notitie hebt gemaakt. Sla de wijzigingen vervolgens op in **BackEndApplication. java**.

3. Voer in het lokale terminalvenster de volgende opdrachten uit om de vereiste bibliotheken te installeren en de back-endtoepassing te compileren:

    ```cmd/sh
    mvn clean package
    ```

4. Voer in het lokale terminalvenster de volgende opdrachten uit om de back-endtoepassing uit te voeren:

    ```cmd/sh
    java -jar target/back-end-application-1.0.0-with-deps.jar
    ```

    In de volgende scherm afbeelding ziet u de uitvoer wanneer de toepassing een directe methode aanroept naar het apparaat en ontvangt u een bevestiging:

    ![Uitvoer als de toepassing een directe methode oproep via uw IoT-hub maakt](./media/quickstart-control-device-java/iot-hub-direct-method-call-output.png)

    Nadat u de back-endtoepassing hebt uitgevoerd, ziet u een bericht in het consolevenster dat het gesimuleerde apparaat wordt uitgevoerd, en dat het interval voor het verzenden van berichten is gewijzigd:

    ![Console bericht van apparaat toont de snelheid waarmee het wordt gewijzigd](./media/quickstart-control-device-java/iot-hub-sent-message-change-rate.png)

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Volgende stappen

In deze snelstart hebt u vanuit een back-endtoepassing een directe methode op een apparaat aangeroepen en op de aanroep van de directe methode gereageerd in een toepassing voor een gesimuleerd apparaat.

Ga verder met de volgende zelfstudie als u wilt leren hoe u berichten van een apparaat naar andere bestemmingen in de cloud kunt routeren.

> [!div class="nextstepaction"]
> [Zelfstudie: Routeren van telemetriegegevens naar verschillende eindpunten voor verwerking](tutorial-routing.md)
