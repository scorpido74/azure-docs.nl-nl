---
title: 'Quickstart: Een apparaat beheren vanuit Azure IoT Hub (Android) | Microsoft Docs'
description: In deze snelstartgids gaan we twee in Java geschreven voorbeeldtoepassingen uitvoeren. De ene toepassing is een servicetoepassing waarmee u op afstand apparaten kunt beheren die zijn verbonden met de hub. De andere toepassing wordt uitgevoerd op een fysiek of gesimuleerd apparaat dat is verbonden met de hub en dat op afstand kan worden beheerd.
author: wesmc7777
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: quickstart
ms.custom:
- mvc
- mqtt
ms.date: 06/21/2019
ms.author: wesmc
ms.openlocfilehash: 8c3a9c6c5e835104675239882d2a1929a3c07c82
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81771034"
---
# <a name="quickstart-control-a-device-connected-to-an-iot-hub-android"></a>Quickstart: Een apparaat beheren dat is verbonden met een IoT-hub (Android)

[!INCLUDE [iot-hub-quickstarts-2-selector](../../includes/iot-hub-quickstarts-2-selector.md)]

In deze quickstart gebruikt u een directe methode om een gesimuleerd apparaat te bedienen dat is verbonden met Azure IoT Hub. IoT Hub is een Azure-service waarmee u uw IoT-apparaten vanuit de cloud beheren en grote hoeveelheden apparaattelemetrie naar de cloud innemen voor opslag of verwerking. U kunt directe methoden gebruiken om het gedrag van een apparaat dat is verbonden met uw IoT-hub, op afstand te wijzigen. Deze quickstart maakt gebruik van twee toepassingen: een gesimuleerde apparaattoepassing die reageert op directe methoden die worden aangeroepen vanuit een back-endservicetoepassing en een servicetoepassing die de directe methode op het Android-apparaat aanroept.

## <a name="prerequisites"></a>Vereisten

* Een Azure-account met een actief abonnement. [Maak er gratis een.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

* [Android Studio met Android SDK 27](https://developer.android.com/studio/). Zie [Android Studio installeren](https://developer.android.com/studio/install)voor meer informatie.

* [Git.](https://git-scm.com/download/)

* [Device SDK-voorbeeld Android-toepassing](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/iot-hub/Samples/device/AndroidSample), opgenomen in [Azure IoT-voorbeelden (Java).](https://github.com/Azure-Samples/azure-iot-samples-java)

* [Service SDK-voorbeeld Android-toepassing](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/iot-hub/Samples/service/AndroidSample), opgenomen in Azure IoT Samples (Java).

* Poort 8883 wordt geopend in uw firewall. Het apparaatvoorbeeld in deze quickstart maakt gebruik van het MQTT-protocol, dat communiceert via poort 8883. Deze poort kan worden geblokkeerd in sommige bedrijfs- en educatieve netwerkomgevingen. Zie [Verbinding maken met IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)voor meer informatie en manieren om dit probleem te omzeilen.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

### <a name="add-azure-iot-extension"></a>Azure IoT-extensie toevoegen

Voer de volgende opdracht uit om de Microsoft Azure IoT-extensie voor Azure CLI toe te voegen aan uw Cloud Shell-exemplaar. De IoT-extensie voegt specifieke opdrachten voor IoT Hub, IoT Edge en IoT Device Provisioning Service (DPS) toe aan Azure CLI.

```azurecli-interactive
az extension add --name azure-iot
```

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Een IoT Hub maken

Als u de vorige [Quickstart: Telemetrie vanaf een apparaat verzenden naar een IoT-hub](quickstart-send-telemetry-android.md) hebt afgerond, kunt u deze stap overslaan en de IoT-hub gebruiken die u al hebt gemaakt.

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Een apparaat registreren

Als u de vorige [Quickstart: Telemetrie vanaf een apparaat verzenden naar een IoT-hub](quickstart-send-telemetry-android.md) hebt afgerond, kunt u deze stap overslaan en hetzelfde apparaat gebruiken dat is geregistreerd in de vorige quickstart.

Een apparaat moet zijn geregistreerd bij uw IoT-hub voordat het verbinding kan maken. In deze snelstart gebruikt u Azure Cloud Shell om een gesimuleerd apparaat te registreren.

1. Voer de volgende opdracht uit in Azure Cloud Shell om de apparaatidentiteit te maken.

   **YourIoTHubName**: vervang deze tijdelijke aanduiding door een door u gekozen naam voor de IoT-hub.

   **MyAndroidDevice:** Dit is de naam van het apparaat dat u registreert. Het wordt aanbevolen om **MyAndroidDevice** te gebruiken zoals getoond. Als u een andere naam voor uw apparaat kiest, moet u die naam ook in dit artikel gebruiken en de apparaatnaam bijwerken in de voorbeeldtoepassingen voordat u ze uitvoert.

    ```azurecli-interactive
    az iot hub device-identity create \
      --hub-name {YourIoTHubName} --device-id MyAndroidDevice
    ```

2. Voer de volgende opdrachten uit in Azure Cloud Shell om de _apparaatverbindingsreeks_ op te halen voor het apparaat dat u zojuist hebt geregistreerd:

   **YourIoTHubName**: vervang deze tijdelijke aanduiding door een door u gekozen naam voor de IoT-hub.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string \
      --hub-name {YourIoTHubName} \
      --device-id MyAndroidDevice \
      --output table
    ```

    Noteer de apparaatverbindingsreeks. Deze ziet er ongeveer als volgt uit:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidDevice;SharedAccessKey={YourSharedAccessKey}`

    U gebruikt deze waarde verderop in de snelstartgids.

## <a name="retrieve-the-service-connection-string"></a>De verbindingsreeks voor de service ophalen

U hebt ook een _serviceverbindingsreeks_ nodig, zodat de servicetoepassingen verbinding kunnen maken met de IoT-hub om methoden uit te voeren en berichten op te halen. Met de volgende opdracht haalt u de serviceverbindingsreeks voor uw IoT-hub op:

**YourIoTHubName**: vervang deze tijdelijke aanduiding door een door u gekozen naam voor de IoT-hub.

```azurecli-interactive
az iot hub show-connection-string --policy-name service --name {YourIoTHubName} --output table
```

Noteer de serviceverbindingsreeks. Deze ziet er ongeveer als volgt uit:

`HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}`

U gebruikt deze waarde verderop in de snelstartgids. Deze tekenreeks voor serviceverbinding verschilt van de tekenreeks van de apparaatverbinding die u in de vorige stap hebt opgemerkt.

## <a name="listen-for-direct-method-calls"></a>Luisteren naar aanroepen van directe methoden

Beide voorbeelden voor deze quickstart maken deel uit van de azure-iot-samples-java repository op GitHub. Download of kloon de opslagplaats [azure-iot-samples-java](https://github.com/Azure-Samples/azure-iot-samples-java).

De Device SDK-voorbeeldtoepassing kan worden uitgevoerd op een fysiek Android-apparaat of op een Android-emulator. De voorbeeldtoepassing maakt verbinding met een apparaatspecifiek eindpunt op uw IoT-hub, verstuurt gesimuleerde telemetrie en luistert naar aanroepen van directe methoden vanuit de hub. In deze snelstartgids geeft de aanroep van de directe methode vanuit de hub het apparaat opdracht om het interval voor het verzenden van telemetrie te wijzigen. Het gesimuleerde apparaat stuurt een bevestiging terug naar uw hub nadat de directe methode is uitgevoerd.

1. Open het GitHub-voorbeeldproject voor Android in Android Studio. Het project bevindt zich in de volgende map met uw gekloonde of gedownloade kopie van de opslagplaats [azure-iot-sample-java](https://github.com/Azure-Samples/azure-iot-samples-java).

        \azure-iot-samples-java\iot-hub\Samples\device\AndroidSample

2. Open in Android Studio *gradle.properties* voor het voorbeeldproject en vervang de **Device_Connection_String** tijdelijke aanduiding door de tekenreeks voor apparaatverbinding waar u eerder een notitie van hebt gemaakt.

    ```
    DeviceConnectionString=HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidDevice;SharedAccessKey={YourSharedAccessKey}
    ```

3. Klik in Android Studio op **Project voor** > synchroniseren van bestanden**met Gradle-bestanden**. Controleer of de build is voltooid.

   > [!NOTE]
   > Als de projectsynchronisatie mislukt, kan dit om een van de volgende redenen zijn:
   >
   > * De versies van de Android Gradle-plug-in en Gradle waarnaar in het project wordt verwezen, zijn verouderd voor uw versie van Android Studio. Volg [deze instructies](https://developer.android.com/studio/releases/gradle-plugin) om de juiste versies van de plugin en Gradle voor uw installatie te verwijzen en te installeren.
   > * De licentieovereenkomst voor de Android SDK is niet ondertekend. Volg de instructies in de Build-uitvoer om de licentieovereenkomst te ondertekenen en download de SDK.

4. Zodra de build is voltooid, klikt u op **'app** > **uitvoeren' uitvoeren.** Configureer de app om te worden uitgevoerd op een fysiek Android-apparaat of een Android-emulator. Zie [Uw app uitvoeren](https://developer.android.com/training/basics/firstapp/running-app) voor meer informatie over het uitvoeren van een Android-app op een fysiek apparaat of een emulator.

5. Zodra de app wordt geladen, klikt u op de knop **Start** om telemetrie te verzenden naar de IoT-hub:

    ![Voorbeeld schermafbeelding van de Android-app voor clientapparaten](media/quickstart-control-device-android/sample-screenshot.png)

Deze app moet worden overgelaten aan het uitvoeren van een fysiek apparaat of emulator terwijl u het SDK-voorbeeld van de service uitvoert om het telemetrie-interval tijdens de uitvoering bij te werken.

## <a name="read-the-telemetry-from-your-hub"></a>De telemetrie van uw hub lezen

In deze sectie gebruikt u de Azure Cloud Shell met de [IoT-extensie](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest) om de berichten te controleren die door het Android-apparaat worden verzonden.

1. Voer met de Azure Cloud Shell de volgende opdracht uit om te verbinden en berichten te lezen uit uw IoT-hub:

   **YourIoTHubName**: vervang deze tijdelijke aanduiding door een door u gekozen naam voor de IoT-hub.

    ```azurecli-interactive
    az iot hub monitor-events --hub-name {YourIoTHubName} --output table
    ```

    In de volgende schermafbeelding ziet u de uitvoer op het moment dat de IoT-hub de telemetrie ontvangt die is verzonden met het Android-apparaat:

      ![Lees de apparaatberichten met de Azure CLI](media/quickstart-control-device-android/read-data.png)

Standaard verstuurt de telemetrie-app elke vijf seconden telemetrie vanaf het Android-apparaat. In de volgende sectie gebruikt u de aanroep van de directe methode om het telemetrie-interval voor het Android IoT-apparaat bij te werken.

## <a name="call-the-direct-method"></a>De directe methode aanroepen

De servicetoepassing maakt verbinding met een eindpunt aan de servicezijde van de IoT-hub. De toepassing voert rechtstreeks door dat u een apparaat aanbelt via uw IoT-hub en luistert naar bevestigingen.

Voer deze app uit op een afzonderlijk fysiek Android-apparaat of een Android-emulator.

Een IoT Hub back-end servicetoepassing wordt meestal uitgevoerd in de cloud, waarbij het eenvoudiger is om de risico's te beperken die verbonden zijn aan de gevoelige verbindingstekenreeks die alle apparaten op een IoT Hub aanstuurt. In dit voorbeeld wordt deze servicetoepassing alleen ter demonstratie uitgevoerd als een Android-app. De andere taalversies van deze quickstart bieden voorbeelden die beter aansluiten bij een typische back-endservicetoepassing.

1. Open het GitHub-serviceproject voor Android in Android Studio. Het project bevindt zich in de volgende map met uw gekloonde of gedownloade kopie van de opslagplaats [azure-iot-sample-java](https://github.com/Azure-Samples/azure-iot-samples-java).

        \azure-iot-samples-java\iot-hub\Samples\service\AndroidSample

2. Open in Android Studio *gradle.properties* voor het voorbeeldproject. Werk de waarden voor de eigenschappen **ConnectionString** en **DeviceId bij** met de tekenreeks serviceverbinding die u eerder hebt opgemerkt en de Android-apparaat-id die u hebt geregistreerd.

    ```
    ConnectionString=HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}
    DeviceId=MyAndroidDevice
    ```

3. Klik in Android Studio op **Project voor** > synchroniseren van bestanden**met Gradle-bestanden**. Controleer of de build is voltooid.

   > [!NOTE]
   > Als de projectsynchronisatie mislukt, kan dit om een van de volgende redenen zijn:
   >
   > * De versies van de Android Gradle-plug-in en Gradle waarnaar in het project wordt verwezen, zijn verouderd voor uw versie van Android Studio. Volg [deze instructies](https://developer.android.com/studio/releases/gradle-plugin) om de juiste versies van de plugin en Gradle voor uw installatie te verwijzen en te installeren.
   > * De licentieovereenkomst voor de Android SDK is niet ondertekend. Volg de instructies in de Build-uitvoer om de licentieovereenkomst te ondertekenen en download de SDK.

4. Zodra de build is voltooid, klikt u op **'app** > **uitvoeren' uitvoeren.** Configureer de app om te worden uitgevoerd op een afzonderlijk fysiek Android-apparaat of een Android-emulator. Zie [Uw app uitvoeren](https://developer.android.com/training/basics/firstapp/running-app) voor meer informatie over het uitvoeren van een Android-app op een fysiek apparaat of een emulator.

5. Zodra de app wordt geladen, werkt u de waarde **Berichtinterval instellen** bij naar **1000** en klikt u op **Aanroepen**.

    Het berichtinterval voor telemetrie wordt berekend in milliseconden. Het standaardinterval voor telemetrie van het apparaatvoorbeeld is ingesteld op 5 seconden. Na deze wijziging is het Android IoT-apparaat bijgewerkt zodat elke seconde telemetrie wordt verzonden.

    ![Telemetrie-interval invoeren](media/quickstart-control-device-android/enter-telemetry-interval.png)

6. De app ontvangt een bevestiging dat de methode is uitgevoerd of niet.

    ![Directe methodebevestiging](media/quickstart-control-device-android/direct-method-ack.png)

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Volgende stappen

In deze snelstart hebt u vanuit een back-endtoepassing een directe methode op een apparaat aangeroepen en op de aanroep van de directe methode gereageerd in een toepassing voor een gesimuleerd apparaat.

Ga verder met de volgende zelfstudie als u wilt leren hoe u berichten van een apparaat naar andere bestemmingen in de cloud kunt routeren.

> [!div class="nextstepaction"]
> [Zelfstudie: Routeren van telemetriegegevens naar verschillende eindpunten voor verwerking](tutorial-routing.md)
