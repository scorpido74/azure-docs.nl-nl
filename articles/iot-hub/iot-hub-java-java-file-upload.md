---
title: Bestanden uploaden van apparaten naar Azure IoT Hub met Java | Microsoft Documenten
description: Bestanden uploaden van een apparaat naar de cloud met Azure IoT-apparaat SDK voor Java. Geüploade bestanden worden opgeslagen in een Azure-opslagblobcontainer.
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
ms.openlocfilehash: f0753827fe5f7f2b866726683d4cb1f205da4599
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732468"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-java"></a>Bestanden uploaden van uw apparaat naar de cloud met IoT Hub (Java)

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

Deze zelfstudie bouwt voort op de code in de [zelfstudie Cloud-to-device](iot-hub-java-java-c2d.md) verzenden met IoT Hub om u te laten zien hoe u de [mogelijkheden voor het uploaden van bestanden van IoT Hub](iot-hub-devguide-file-upload.md) gebruiken om een bestand te uploaden naar Azure [blob-opslag.](../storage/index.yml) In deze zelfstudie leert u het volgende:

* Zorg veilig voor een apparaat met een Azure blob URI voor het uploaden van een bestand.

* Gebruik de meldingen voor het uploaden van IoT Hub-bestanden om de verwerking van het bestand in de back-end van uw app te activeren.

De [snelstart-berichten van cloud](quickstart-send-telemetry-java.md) naar apparaat verzenden met [iot-hub](iot-hub-java-java-c2d.md) toont de functionaliteit van iot-naar-cloud en cloud-naar-apparaat-berichten van IoT Hub. De [zelfstudie Berichtroutering configureren met IoT Hub](tutorial-routing.md) beschrijft een manier om device-to-cloud-berichten op betrouwbare wijze op te slaan in Azure blob-opslag. In sommige scenario's u de gegevens die uw apparaten verzenden echter niet eenvoudig in kaart brengen in de relatief kleine device-to-cloudberichten die IoT Hub accepteert. Bijvoorbeeld:

* Grote bestanden die afbeeldingen bevatten
* Video's
* Trillingsgegevens bemonsterd op hoge frequentie
* Een vorm van voorbewerkte gegevens.

Deze bestanden worden meestal batch verwerkt in de cloud met behulp van hulpprogramma's zoals [Azure Data Factory](../data-factory/introduction.md) of de [Hadoop](../hdinsight/index.yml) stack. Wanneer u bestanden vanaf een apparaat moet up-landen, u nog steeds de beveiliging en betrouwbaarheid van IoT Hub gebruiken.

Aan het einde van deze zelfstudie voer je twee Java-console-apps uit:

* **gesimuleerd apparaat**, een aangepaste versie van de app gemaakt in de [Cloud-to-device berichten verzenden met IoT Hub] tutorial. Deze app uploadt een bestand naar opslag met behulp van een SAS URI van uw IoT-hub.

* **lees-bestand-upload-melding**, die meldingen voor het uploaden van bestanden ontvangt van uw IoT-hub.

> [!NOTE]
> IoT Hub ondersteunt veel apparaatplatforms en -talen (waaronder C, .NET en Javascript) via Azure IoT-apparaat-SDK's. Raadpleeg het [Azure IoT Developer Center](https://azure.microsoft.com/develop/iot) voor stapsgewijze instructies over het verbinden van uw apparaat met Azure IoT Hub.

## <a name="prerequisites"></a>Vereisten

* [Java SE Development Kit 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable). Zorg ervoor dat u **Java 8** selecteert onder ondersteuning op **lange termijn** om naar downloads voor JDK 8 te gaan.

* [Maven 3](https://maven.apache.org/download.cgi)

* Een actief Azure-account. (Als u geen account hebt, kunt u binnen een paar minuten een [gratis account](https://azure.microsoft.com/pricing/free-trial/) maken.)

* Zorg ervoor dat poort 8883 is geopend in uw firewall. Het apparaatvoorbeeld in dit artikel maakt gebruik van het MQTT-protocol, dat communiceert via poort 8883. Deze poort kan worden geblokkeerd in sommige bedrijfs- en educatieve netwerkomgevingen. Zie [Verbinding maken met IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)voor meer informatie en manieren om dit probleem te omzeilen.

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Een bestand uploaden vanuit een apparaat-app

In deze sectie wijzigt u de apparaat-app die u hebt gemaakt in [Berichten van cloud naar apparaat verzenden met IoT Hub](iot-hub-java-java-c2d.md) om een bestand te uploaden naar de IoT-hub.

1. Kopieer een afbeeldingsbestand naar `simulated-device` de `myimage.png`map en wijzig de naam .

2. Open het `simulated-device\src\main\java\com\mycompany\app\App.java` bestand met een teksteditor.

3. Voeg de variabele declaratie toe aan de klasse **App:**

    ```java
    private static String fileName = "myimage.png";
    ```

4. Als u terugbelberichten met bestandsuploadstatus wilt verwerken, voegt u de volgende geneste klasse toe aan de klasse **App:**

    ```java
    // Define a callback method to print status codes from IoT Hub.
    protected static class FileUploadStatusCallBack implements IotHubEventCallback {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to file upload for " + fileName
            + " operation with status " + status.name());
      }
    }
    ```

5. Als u afbeeldingen wilt uploaden naar IoT Hub, voegt u de volgende methode toe aan de klasse **App** om afbeeldingen naar IoT Hub te uploaden:

    ```java
    // Use IoT Hub to upload a file asynchronously to Azure blob storage.
    private static void uploadFile(String fullFileName) throws FileNotFoundException, IOException
    {
      File file = new File(fullFileName);
      InputStream inputStream = new FileInputStream(file);
      long streamLength = file.length();

      client.uploadToBlobAsync(fileName, inputStream, streamLength, new FileUploadStatusCallBack(), null);
    }
    ```

6. Wijzig de **hoofdmethode** om de **methode uploadFile** aan te roepen zoals in het volgende fragment wordt weergegeven:

    ```java
    client.open();

    try
    {
      // Get the filename and start the upload.
      String fullFileName = System.getProperty("user.dir") + File.separator + fileName;
      uploadFile(fullFileName);
      System.out.println("File upload started with success");
    }
    catch (Exception e)
    {
      System.out.println("Exception uploading file: " + e.getCause() + " \nERROR: " + e.getMessage());
    }

    MessageSender sender = new MessageSender();
    ```

7. Gebruik de volgende opdracht om de **app voor gesimuleerd apparaat** te bouwen en te controleren op fouten:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="get-the-iot-hub-connection-string"></a>De verbindingstekenreeks voor IoT-hub

In dit artikel maakt u een backendservice om meldingen van bestandsuploadmeldingen te ontvangen van de IoT-hub die u hebt gemaakt in [Telemetrie verzenden van een apparaat naar een IoT-hub.](quickstart-send-telemetry-java.md) Als u meldingen voor het uploaden van bestanden wilt ontvangen, heeft uw service de machtiging **voor verbinding met** de service nodig. Standaard wordt elke IoT-hub gemaakt met een service met gedeelde toegangsbeleid met de naam **service** die deze toestemming verleent.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="receive-a-file-upload-notification"></a>Een melding voor het uploaden van bestanden ontvangen

In deze sectie maakt u een Java-console-app die meldingen van bestandsuploadmeldingen van IoT Hub ontvangt.

1. Maak een Maven-project genaamd **read-file-upload-notification** met de volgende opdracht op uw opdrachtprompt. Let op: deze opdracht is een enkele, lange opdracht:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=read-file-upload-notification -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. Navigeer op uw opdrachtprompt `read-file-upload-notification` naar de nieuwe map.

3. Open het bestand in `pom.xml` de `read-file-upload-notification` map met een teksteditor en voeg de volgende afhankelijkheid toe aan het knooppunt **van afhankelijkheden.** Door de afhankelijkheid toe te voegen, u het **iothub-java-service-clientpakket** in uw toepassing gebruiken om te communiceren met uw IoT-hubservice:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.7.23</version>
    </dependency>
    ```

    > [!NOTE]
    > U vindt de meest recente versie van **iot-service-client** met [Maven zoeken](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

4. Sla het `pom.xml` bestand op en sluit deze.

5. Open het `read-file-upload-notification\src\main\java\com\mycompany\app\App.java` bestand met een teksteditor.

6. Voeg de volgende **import**instructies toe aan het bestand:

    ```java
    import com.microsoft.azure.sdk.iot.service.*;
    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.util.concurrent.ExecutorService;
    import java.util.concurrent.Executors;
    ```

7. Voeg de volgende variabelen op klasseniveau toe aan de **App**-klasse. Vervang `{Your IoT Hub connection string}` de tijdelijke aanduidingswaarde door de IoT-hubverbindingstekenreeks die u eerder hebt gekopieerd in [De verbindingstekenreeks van de IoT-hub:](#get-the-iot-hub-connection-string)

    ```java
    private static final String connectionString = "{Your IoT Hub connection string}";
    private static final IotHubServiceClientProtocol protocol = IotHubServiceClientProtocol.AMQPS;
    private static FileUploadNotificationReceiver fileUploadNotificationReceiver = null;
    ```

8. Als u informatie over het uploaden van bestanden wilt afdrukken naar de console, voegt u de volgende geneste klasse toe aan de klasse **App:**

    ```java
    // Create a thread to receive file upload notifications.
    private static class ShowFileUploadNotifications implements Runnable {
      public void run() {
        try {
          while (true) {
            System.out.println("Receive file upload notifications...");
            FileUploadNotification fileUploadNotification = fileUploadNotificationReceiver.receive();
            if (fileUploadNotification != null) {
              System.out.println("File Upload notification received");
              System.out.println("Device Id : " + fileUploadNotification.getDeviceId());
              System.out.println("Blob Uri: " + fileUploadNotification.getBlobUri());
              System.out.println("Blob Name: " + fileUploadNotification.getBlobName());
              System.out.println("Last Updated : " + fileUploadNotification.getLastUpdatedTimeDate());
              System.out.println("Blob Size (Bytes): " + fileUploadNotification.getBlobSizeInBytes());
              System.out.println("Enqueued Time: " + fileUploadNotification.getEnqueuedTimeUtcDate());
            }
          }
        } catch (Exception ex) {
          System.out.println("Exception reading reported properties: " + ex.getMessage());
        }
      }
    }
    ```

9. Als u de thread wilt starten die naar meldingen voor het uploaden van bestanden luistert, voegt u de volgende code toe aan de **hoofdmethode:**

    ```java
    public static void main(String[] args) throws IOException, URISyntaxException, Exception {
      ServiceClient serviceClient = ServiceClient.createFromConnectionString(connectionString, protocol);

      if (serviceClient != null) {
        serviceClient.open();

        // Get a file upload notification receiver from the ServiceClient.
        fileUploadNotificationReceiver = serviceClient.getFileUploadNotificationReceiver();
        fileUploadNotificationReceiver.open();

        // Start the thread to receive file upload notifications.
        ShowFileUploadNotifications showFileUploadNotifications = new ShowFileUploadNotifications();
        ExecutorService executor = Executors.newFixedThreadPool(1);
        executor.execute(showFileUploadNotifications);

        System.out.println("Press ENTER to exit.");
        System.in.read();
        executor.shutdownNow();
        System.out.println("Shutting down sample...");
        fileUploadNotificationReceiver.close();
        serviceClient.close();
      }
    }
    ```

10. Sla het `read-file-upload-notification\src\main\java\com\mycompany\app\App.java` bestand op en sluit deze.

11. Gebruik de volgende opdracht om de app **voor het lezen-bestand-upload-melding** te maken en te controleren op fouten:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-applications"></a>De toepassingen uitvoeren

U kunt nu de toepassingen gaan uitvoeren.

Voer bij een `read-file-upload-notification` opdrachtprompt in de map de volgende opdracht uit:

```cmd/sh
mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
```

Voer bij een `simulated-device` opdrachtprompt in de map de volgende opdracht uit:

```cmd/sh
mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
```

De volgende schermafbeelding toont de uitvoer van de **gesimuleerde app:**

![Uitvoer van gesimuleerde apparaat-app](media/iot-hub-java-java-upload/simulated-device.png)

In de volgende schermafbeelding wordt de uitvoer van de app **voor het lezen-bestand-upload-melding** weergegeven:

![Uitvoer van de app lezen-bestand-upload-melding](media/iot-hub-java-java-upload/read-file-upload-notification.png)

U de portal gebruiken om het geüploade bestand weer te geven in de opslagcontainer die u hebt geconfigureerd:

![Geüpload bestand](media/iot-hub-java-java-upload/uploaded-file.png)

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u de mogelijkheden voor het uploaden van bestanden van IoT Hub gebruiken om het uploaden van bestanden vanaf apparaten te vereenvoudigen. Met de volgende artikelen u de functies en scenario's van de IoT-hub blijven verkennen:

* [Een IoT-hub programmatisch maken](iot-hub-rm-template-powershell.md)

* [Inleiding tot C SDK](iot-hub-device-sdk-c-intro.md)

* [SDK's voor Azure IoT](iot-hub-devguide-sdks.md)

Zie:

* [Een apparaat simuleren met IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
