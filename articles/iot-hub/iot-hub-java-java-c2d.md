---
title: Cloud-to-device-berichten met Azure IoT Hub (Java) | Microsoft Documenten
description: Cloud-naar-device-berichten verzenden naar een apparaat vanaf een Azure IoT-hub met behulp van de Azure IoT SDK's voor Java. U wijzigt een gesimuleerde apparaat-app om berichten van cloud naar apparaat te ontvangen en wijzigt een back-end-app om de berichten van cloud naar apparaat te verzenden.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 06/28/2017
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: e16d0ed264f32746c11d89e88ea1e67f9383b773
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732517"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-java"></a>Cloud-naar-device berichten verzenden met IoT Hub (Java)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Azure IoT Hub is een volledig beheerde service die betrouwbare en veilige bidirectionele communicatie tussen miljoenen apparaten en een back-end van een oplossing mogelijk maakt. Met [Quickstart telemetrie verzenden van een apparaat naar een IoT-hub](quickstart-send-telemetry-java.md) ziet u hoe u een IoT-hub maakt, een apparaatidentiteit indient en een gesimuleerde apparaat-app codet die apparaat-naar-cloudberichten verzendt.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Deze zelfstudie bouwt voort op [Telemetrie verzenden van een apparaat naar een IoT-hub.](quickstart-send-telemetry-java.md) Het laat je zien hoe je het volgende te doen:

* Stuur vanuit uw back-end van oplossing cloud-naar-device-berichten naar één apparaat via IoT Hub.

* Ontvang berichten van cloud naar apparaat op een apparaat.

* Vraag vanaf uw oplossing een bevestiging van de levering *(feedback)* aan voor berichten die vanuit IoT Hub naar een apparaat worden verzonden.

Meer informatie over berichten van cloud tot apparaat vindt u [in de handleiding voor IoT Hub-ontwikkelaars.](iot-hub-devguide-messaging.md)

Aan het einde van deze zelfstudie voert u twee Java-console-apps uit:

* **gesimuleerd apparaat**, een aangepaste versie van de app gemaakt in [Telemetrie verzenden van een apparaat naar een IoT-hub,](quickstart-send-telemetry-java.md)die verbinding maakt met uw IoT-hub en ontvangt cloud-to-device berichten.

* **send-c2d-berichten**, die een cloud-to-device-bericht naar de gesimuleerde apparaat-app stuurt via IoT Hub en vervolgens de leveringsbevestiging ontvangt.

> [!NOTE]
> IoT Hub heeft SDK-ondersteuning voor veel apparaatplatforms en -talen (waaronder C, Java, Python en Javascript) via Azure IoT-apparaat SDK's. Zie het [Azure IoT Developer Center](https://azure.microsoft.com/develop/iot)voor stapsgewijze instructies over het verbinden van uw apparaat met de code van deze zelfstudie en in het algemeen met Azure IoT Hub.

## <a name="prerequisites"></a>Vereisten

* Een volledige werkende versie van de [telemetrie verzenden van een apparaat naar een IoT-hub](quickstart-send-telemetry-java.md) snelstart of de [routeringsfunctie voor berichten configureren met IoT Hub.A](tutorial-routing.md) complete working version of the Send telemetry from a device to a IoT hub quickstart or the Configure message routing with IoT Hub tutorial.

* [Java SE Development Kit 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable). Zorg ervoor dat u **Java 8** selecteert onder ondersteuning op **lange termijn** om naar downloads voor JDK 8 te gaan.

* [Maven 3](https://maven.apache.org/download.cgi)

* Een actief Azure-account. Als je nog geen account hebt, kun je binnen een paar minuten een [gratis account](https://azure.microsoft.com/pricing/free-trial/) aanmaken.

* Zorg ervoor dat poort 8883 is geopend in uw firewall. Het apparaatvoorbeeld in dit artikel maakt gebruik van het MQTT-protocol, dat communiceert via poort 8883. Deze poort kan worden geblokkeerd in sommige bedrijfs- en educatieve netwerkomgevingen. Zie [Verbinding maken met IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)voor meer informatie en manieren om dit probleem te omzeilen.

## <a name="receive-messages-in-the-simulated-device-app"></a>Berichten ontvangen in de gesimuleerde apparaat-app

In deze sectie wijzigt u de gesimuleerde apparaat-app die u hebt gemaakt in [Telemetrie verzenden van een apparaat naar een IoT-hub](quickstart-send-telemetry-java.md) om cloud-to-device-berichten van de IoT-hub te ontvangen.

1. Open het bestand simulated-device\src\main\java\com\mycompany\app\App.java met een teksteditor.

2. Voeg de volgende **messagecallback-klasse** toe als een geneste klasse in de klasse **App.** De **uitvoermethode** wordt aangeroepen wanneer het apparaat een bericht ontvangt van IoT Hub. In dit voorbeeld waarschuwt het apparaat altijd de IoT-hub dat het bericht is voltooid:

    ```java
    private static class AppMessageCallback implements MessageCallback {
      public IotHubMessageResult execute(Message msg, Object context) {
        System.out.println("Received message from hub: "
          + new String(msg.getBytes(), Message.DEFAULT_IOTHUB_MESSAGE_CHARSET));

        return IotHubMessageResult.COMPLETE;
      }
    }
    ```

3. Wijzig de **hoofdmethode** om een **AppMessageCallback-instantie** te maken en bel de **methode setMessageCallback** voordat de client als volgt wordt geopend:

    ```java
    client = new DeviceClient(connString, protocol);

    MessageCallback callback = new AppMessageCallback();
    client.setMessageCallback(callback, null);
    client.open();
    ```

    > [!NOTE]
    > Als u HTTPS gebruikt in plaats van MQTT of AMQP als transport, controleert de **instantie DeviceClient** zelden op berichten van IoT Hub (minder dan elke 25 minuten). Zie het [berichtengedeelte van de IoT Hub-ontwikkelaarshandleiding](iot-hub-devguide-messaging.md)voor meer informatie over de verschillen tussen MQTT-, AMQP- en HTTPS-ondersteuning en IoT Hub-beperking.

4. Als u de app **simulated-device** wilt maken met behulp van Maven, geeft u de volgende opdracht op in het opdrachtvenster in de map simulated-device:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="get-the-iot-hub-connection-string"></a>De verbindingstekenreeks voor IoT-hub

In dit artikel maakt u een backendservice om cloud-naar-device-berichten te verzenden via de IoT-hub die u hebt gemaakt in [Telemetrie verzenden van een apparaat naar een IoT-hub.](quickstart-send-telemetry-java.md) Als u berichten van cloud naar apparaat wilt verzenden, heeft uw service de service-machtiging nodig om verbinding **te maken.** Standaard wordt elke IoT-hub gemaakt met een service met gedeelde toegangsbeleid met de naam **service** die deze toestemming verleent.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>Een cloud-naar-apparaatbericht verzenden

In deze sectie maakt u een Java-console-app die cloud-naar-apparaatberichten verzendt naar de gesimuleerde apparaat-app. U hebt de apparaat-id nodig van het apparaat dat u in de [telemetrie verzenden van een apparaat naar een IoT-hub](quickstart-send-telemetry-java.md) snelstart. U hebt ook de de reeks IoT-hubverbindingen nodig die u eerder hebt gekopieerd in [De verbindingstekenreeks voor iot-hub opbrengen.](#get-the-iot-hub-connection-string)

1. Maak een Maven-project genaamd **send-c2d-berichten** met de volgende opdracht op uw opdrachtprompt. Let op: deze opdracht is een enkele, lange opdracht:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=send-c2d-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. Navigeer bij de opdrachtprompt naar de nieuwe map send-c2d-berichten.

3. Open het bestand pom.xml met een teksteditor in de map send-c2d-berichten en voeg de volgende afhankelijkheid toe aan het knooppunt **van afhankelijkheden.** Door de afhankelijkheid toe te voegen, u het **iothub-java-service-clientpakket** in uw toepassing gebruiken om te communiceren met uw IoT-hubservice:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.7.23</version>
    </dependency>
    ```

    > [!NOTE]
    > U vindt de meest recente versie van **iot-service-client** met [Maven zoeken](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

4. Sla het bestand pom.xml op en sluit het af.

5. Open met behulp van een teksteditor het bestand send-c2d-messages\src\main\java\com\mycompany\app\App.java.

6. Voeg de volgende **import**instructies toe aan het bestand:

    ```java
    import com.microsoft.azure.sdk.iot.service.*;
    import java.io.IOException;
    import java.net.URISyntaxException;
    ```

7. Voeg de volgende klassenvariabelen toe aan de klasse **App** en vervang **{yourhubconnectionstring}** en **{yourdeviceid}** door de waarden die u eerder hebt opgemerkt:

    ```java
    private static final String connectionString = "{yourhubconnectionstring}";
    private static final String deviceId = "{yourdeviceid}";
    private static final IotHubServiceClientProtocol protocol =    
        IotHubServiceClientProtocol.AMQPS;
    ```

8. Vervang de **hoofdmethode** door de volgende code. Deze code maakt verbinding met uw IoT-hub, stuurt een bericht naar uw apparaat en wacht vervolgens op een bevestiging dat het apparaat het bericht heeft ontvangen en verwerkt:

    ```java
    public static void main(String[] args) throws IOException,
        URISyntaxException, Exception {
      ServiceClient serviceClient = ServiceClient.createFromConnectionString(
        connectionString, protocol);

      if (serviceClient != null) {
        serviceClient.open();
        FeedbackReceiver feedbackReceiver = serviceClient
          .getFeedbackReceiver();
        if (feedbackReceiver != null) feedbackReceiver.open();

        Message messageToSend = new Message("Cloud to device message.");
        messageToSend.setDeliveryAcknowledgement(DeliveryAcknowledgement.Full);

        serviceClient.send(deviceId, messageToSend);
        System.out.println("Message sent to device");

        FeedbackBatch feedbackBatch = feedbackReceiver.receive(10000);
        if (feedbackBatch != null) {
          System.out.println("Message feedback received, feedback time: "
            + feedbackBatch.getEnqueuedTimeUtc().toString());
        }

        if (feedbackReceiver != null) feedbackReceiver.close();
        serviceClient.close();
      }
    }
    ```

    > [!NOTE]
    > Voor de eenvoud voert deze zelfstudie geen beleid voor nieuwe try's uit. In productiecode moet u opnieuw proberenbeleid implementeren (zoals exponentiële back-off), zoals voorgesteld in het artikel [Transient Fault Handling](/azure/architecture/best-practices/transient-faults).

9. Als u de app **simulated-device** wilt maken met behulp van Maven, geeft u de volgende opdracht op in het opdrachtvenster in de map simulated-device:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-applications"></a>De toepassingen uitvoeren

U kunt nu de toepassingen gaan uitvoeren.

1. Voer bij een opdrachtprompt in de map gesimuleerd apparaat de volgende opdracht uit om telemetrie naar uw IoT-hub te verzenden en te luisteren naar berichten die vanuit uw hub van cloud naar apparaat worden verzonden:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![De gesimuleerde apparaat-app uitvoeren](./media/iot-hub-java-java-c2d/receivec2d.png)

2. Voer bij een opdrachtprompt in de map send-c2d-berichten de volgende opdracht uit om een cloud-to-device-bericht te verzenden en te wachten op een bevestiging van feedback:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![De opdracht uitvoeren om het cloud-naar-apparaatbericht te verzenden](media/iot-hub-java-java-c2d/sendc2d.png)

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u berichten van cloud naar apparaat verzenden en ontvangen.

Zie [Azure IoT Solution Accelerators](https://azure.microsoft.com/documentation/suites/iot-suite/)voor voorbeelden van complete end-to-end-oplossingen die IoT Hub gebruiken.

Zie de [IoT Hub-ontwikkelaarshandleiding](iot-hub-devguide.md)voor meer informatie over het ontwikkelen van oplossingen met IoT Hub.
