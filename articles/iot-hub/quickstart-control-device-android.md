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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81771034"
---
# <a name="quickstart-control-a-device-connected-to-an-iot-hub-android"></a>Quickstart: Een apparaat beheren dat is verbonden met een IoT-hub (Android)

[!INCLUDE [iot-hub-quickstarts-2-selector](../../includes/iot-hub-quickstarts-2-selector.md)]

In deze Quick Start gebruikt u een directe methode voor het beheren van een gesimuleerd apparaat dat is verbonden met Azure IoT Hub. IoT Hub is een Azure-service waarmee u uw IoT-apparaten kunt beheren vanuit de Cloud en grote hoeveel heden apparaat-telemetrie kunt opnemen in de Cloud voor opslag of verwerking. U kunt directe methoden gebruiken om het gedrag van een apparaat dat is verbonden met uw IoT-hub, op afstand te wijzigen. In deze Quick Start worden twee toepassingen gebruikt: een gesimuleerde apparaat-app die reageert op directe methoden die worden aangeroepen vanuit een back-end-service toepassing en een service toepassing die de directe methode op het Android-apparaat aanroept.

## <a name="prerequisites"></a>Vereisten

* Een Azure-account met een actief abonnement. [Maak er gratis een](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

* [Android Studio met ANDROID SDK 27](https://developer.android.com/studio/). Zie [Install Android Studio](https://developer.android.com/studio/install)(Engelstalig) voor meer informatie.

* [Git](https://git-scm.com/download/).

* [Device SDK-voor beeld Android-toepassing](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/iot-hub/Samples/device/AndroidSample), opgenomen in [Azure IOT-voor beelden (Java)](https://github.com/Azure-Samples/azure-iot-samples-java).

* [Service SDK-voor beeld Android-toepassing](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/iot-hub/Samples/service/AndroidSample), opgenomen in azure IOT-voor beelden (Java).

* Poort 8883 is geopend in de firewall. Het voor beeld van het apparaat in deze Snelstartgids maakt gebruik van het MQTT-protocol, dat communiceert via poort 8883. Deze poort kan worden geblokkeerd in sommige bedrijfs-en educatieve netwerk omgevingen. Zie [verbinding maken met IOT hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)voor meer informatie en manieren om dit probleem te omzeilen.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

### <a name="add-azure-iot-extension"></a>Azure IoT-extensie toevoegen

Voer de volgende opdracht uit om de Microsoft Azure IoT-extensie voor Azure CLI toe te voegen aan uw Cloud Shell-exemplaar. De IoT-extensie voegt IoT Hub, IoT Edge en IoT Device Provisioning Service (DPS)-specifieke opdrachten toe aan Azure CLI.

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

1. Voer de volgende opdracht uit in Azure Cloud Shell om de apparaat-id te maken.

   **YourIoTHubName**: vervang deze tijdelijke aanduiding door een door u gekozen naam voor de IoT-hub.

   **MyAndroidDevice**: dit is de naam van het apparaat dat u wilt registreren. Het is raadzaam om **MyAndroidDevice** te gebruiken zoals wordt weer gegeven. Als u een andere naam kiest voor uw apparaat, moet u deze naam ook in dit artikel gebruiken en de apparaatnaam bijwerken in de voorbeeld toepassingen voordat u ze uitvoert.

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

U gebruikt deze waarde verderop in de snelstartgids. Deze service connection string wijkt af van het apparaat connection string dat u in de vorige stap hebt genoteerd.

## <a name="listen-for-direct-method-calls"></a>Luisteren naar aanroepen van directe methoden

Beide voor beelden voor deze Quick start zijn onderdeel van de opslag plaats Azure-IOT-samples-Java op GitHub. Download of kloon de opslagplaats [azure-iot-samples-java](https://github.com/Azure-Samples/azure-iot-samples-java).

De Device SDK-voorbeeldtoepassing kan worden uitgevoerd op een fysiek Android-apparaat of op een Android-emulator. De voorbeeldtoepassing maakt verbinding met een apparaatspecifiek eindpunt op uw IoT-hub, verstuurt gesimuleerde telemetrie en luistert naar aanroepen van directe methoden vanuit de hub. In deze snelstartgids geeft de aanroep van de directe methode vanuit de hub het apparaat opdracht om het interval voor het verzenden van telemetrie te wijzigen. Het gesimuleerde apparaat verzendt een bevestiging terug naar uw hub nadat de directe methode is uitgevoerd.

1. Open het GitHub-voorbeeldproject voor Android in Android Studio. Het project bevindt zich in de volgende map met uw gekloonde of gedownloade kopie van de opslagplaats [azure-iot-sample-java](https://github.com/Azure-Samples/azure-iot-samples-java).

        \azure-iot-samples-java\iot-hub\Samples\device\AndroidSample

2. Open *gradle. Properties* voor het voorbeeld project in Android Studio en vervang de tijdelijke aanduiding **Device_Connection_String** door het apparaat Connection String u eerder een notitie hebt gemaakt.

    ```
    DeviceConnectionString=HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidDevice;SharedAccessKey={YourSharedAccessKey}
    ```

3. Klik in Android Studio op **bestand** > **synchroniseren project met Gradle-bestanden**. Controleer of de build is voltooid.

   > [!NOTE]
   > Als de project synchronisatie mislukt, kan dit een van de volgende oorzaken hebben:
   >
   > * De versies van de Android Gradle-invoeg toepassing en Gradle waarnaar in het project wordt verwezen, zijn verouderd voor uw versie van Android Studio. Volg [deze instructies](https://developer.android.com/studio/releases/gradle-plugin) om de juiste versies van de invoeg toepassing en Gradle voor uw installatie te raadplegen en te installeren.
   > * De licentie overeenkomst voor de Android SDK is niet ondertekend. Volg de instructies in de uitvoer van de build om de licentie overeenkomst te ondertekenen en de SDK te downloaden.

4. Zodra de build is voltooid, klikt u op**Run-app** **uitvoeren** > . Configureer de app om te worden uitgevoerd op een fysiek Android-apparaat of een Android-emulator. Zie [Uw app uitvoeren](https://developer.android.com/training/basics/firstapp/running-app) voor meer informatie over het uitvoeren van een Android-app op een fysiek apparaat of een emulator.

5. Zodra de app wordt geladen, klikt u op de knop **Start** om telemetrie te verzenden naar de IoT-hub:

    ![Voorbeeld scherm opname van de Android-app van het client apparaat](media/quickstart-control-device-android/sample-screenshot.png)

Deze app moet actief zijn op een fysiek apparaat of een emulator terwijl u het voor beeld van de Service-SDK uitvoert om het telemetrische interval tijdens runtime bij te werken.

## <a name="read-the-telemetry-from-your-hub"></a>De telemetrie van uw hub lezen

In deze sectie gebruikt u de Azure Cloud Shell met de IOT- [extensie](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest) voor het bewaken van de berichten die worden verzonden door het Android-apparaat.

1. Voer met de Azure Cloud Shell de volgende opdracht uit om te verbinden en berichten te lezen uit uw IoT-hub:

   **YourIoTHubName**: vervang deze tijdelijke aanduiding door een door u gekozen naam voor de IoT-hub.

    ```azurecli-interactive
    az iot hub monitor-events --hub-name {YourIoTHubName} --output table
    ```

    In de volgende schermafbeelding ziet u de uitvoer op het moment dat de IoT-hub de telemetrie ontvangt die is verzonden met het Android-apparaat:

      ![Lees de apparaatberichten met de Azure CLI](media/quickstart-control-device-android/read-data.png)

Standaard verzendt de telemetrie-app elke vijf seconden telemetrie van het Android-apparaat. In de volgende sectie gebruikt u de aanroep van de directe methode om het telemetrie-interval voor het Android IoT-apparaat bij te werken.

## <a name="call-the-direct-method"></a>De directe methode aanroepen

De servicetoepassing maakt verbinding met een eindpunt aan de servicezijde van de IoT-hub. De toepassing maakt directe methode aanroepen naar een apparaat via uw IoT-hub en luistert naar bevestigingen.

Voer deze app uit op een afzonderlijk fysiek Android-apparaat of een Android-emulator.

Een IoT Hub back-end-service toepassing wordt doorgaans uitgevoerd in de Cloud, waar het eenvoudiger is om de Risico's te verhelpen die zijn gekoppeld aan de gevoelige connection string waarmee alle apparaten in een IoT Hub worden beheerd. In dit voorbeeld wordt deze servicetoepassing alleen ter demonstratie uitgevoerd als een Android-app. De overige-taal versies van deze Snelstartgids bieden voor beelden die nauw keuriger worden uitgelijnd met een standaard back-end-service toepassing.

1. Open het GitHub-serviceproject voor Android in Android Studio. Het project bevindt zich in de volgende map met uw gekloonde of gedownloade kopie van de opslagplaats [azure-iot-sample-java](https://github.com/Azure-Samples/azure-iot-samples-java).

        \azure-iot-samples-java\iot-hub\Samples\service\AndroidSample

2. Open *gradle. Properties* voor het voorbeeld project in Android Studio. Werk de waarden voor de eigenschappen **Connections Tring** en **DeviceID** bij met de Service Connection String u eerder hebt genoteerd en de Android-apparaat-id die u hebt geregistreerd.

    ```
    ConnectionString=HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}
    DeviceId=MyAndroidDevice
    ```

3. Klik in Android Studio op **bestand** > **synchroniseren project met Gradle-bestanden**. Controleer of de build is voltooid.

   > [!NOTE]
   > Als de project synchronisatie mislukt, kan dit een van de volgende oorzaken hebben:
   >
   > * De versies van de Android Gradle-invoeg toepassing en Gradle waarnaar in het project wordt verwezen, zijn verouderd voor uw versie van Android Studio. Volg [deze instructies](https://developer.android.com/studio/releases/gradle-plugin) om de juiste versies van de invoeg toepassing en Gradle voor uw installatie te raadplegen en te installeren.
   > * De licentie overeenkomst voor de Android SDK is niet ondertekend. Volg de instructies in de uitvoer van de build om de licentie overeenkomst te ondertekenen en de SDK te downloaden.

4. Zodra de build is voltooid, klikt u op**Run-app** **uitvoeren** > . Configureer de app om te worden uitgevoerd op een afzonderlijk fysiek Android-apparaat of een Android-emulator. Zie [Uw app uitvoeren](https://developer.android.com/training/basics/firstapp/running-app) voor meer informatie over het uitvoeren van een Android-app op een fysiek apparaat of een emulator.

5. Zodra de app wordt geladen, werkt u de waarde **Berichtinterval instellen** bij naar **1000** en klikt u op **Aanroepen**.

    Het berichtinterval voor telemetrie wordt berekend in milliseconden. Het standaardinterval voor telemetrie van het apparaatvoorbeeld is ingesteld op 5 seconden. Na deze wijziging is het Android IoT-apparaat bijgewerkt zodat elke seconde telemetrie wordt verzonden.

    ![Telemetrie-interval invoeren](media/quickstart-control-device-android/enter-telemetry-interval.png)

6. De app ontvangt een bevestiging die aangeeft of de methode is uitgevoerd of niet.

    ![Directe bevestigings methode](media/quickstart-control-device-android/direct-method-ack.png)

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Volgende stappen

In deze snelstart hebt u vanuit een back-endtoepassing een directe methode op een apparaat aangeroepen en op de aanroep van de directe methode gereageerd in een toepassing voor een gesimuleerd apparaat.

Ga verder met de volgende zelfstudie als u wilt leren hoe u berichten van een apparaat naar andere bestemmingen in de cloud kunt routeren.

> [!div class="nextstepaction"]
> [Zelfstudie: Routeren van telemetriegegevens naar verschillende eindpunten voor verwerking](tutorial-routing.md)
