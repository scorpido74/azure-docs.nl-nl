---
title: Aan de slag met Azure IoT Hub-apparaattweeling (Java) | Microsoft Documenten
description: Azure IoT Hub-apparaattweelingen gebruiken om tags toe te voegen en vervolgens een IoT Hub-query te gebruiken. U gebruikt de Azure IoT-apparaat SDK voor Java om de apparaat-app en de Azure IoT-service SDK voor Java te implementeren om een service-app te implementeren die de tags toevoegt en de IoT Hub-query uitvoert.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 08/26/2019
ms.custom: mqtt
ms.openlocfilehash: 3ea2f0eec12d756a898f1761f6b22fd034c1bc3e
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732459"
---
# <a name="get-started-with-device-twins-java"></a>Aan de slag met apparaattweelingen (Java)

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

In deze zelfstudie maakt u twee Java-console-apps:

* **add-tags-query**, een Java back-end app die tags en query's apparaat tweelingen toevoegt.
* **gesimuleerd-apparaat**, een Java-apparaat app die verbinding maakt met uw IoT-hub en rapporteert de connectiviteit voorwaarde met behulp van een gerapporteerde eigenschap.

> [!NOTE]
> Het artikel [Azure IoT SDKs](iot-hub-devguide-sdks.md) bevat informatie over de Azure IoT SDK's die u gebruiken om zowel apparaat- als back-end-apps te bouwen.

## <a name="prerequisites"></a>Vereisten

* [Java SE Development Kit 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable). Zorg ervoor dat u **Java 8** selecteert onder ondersteuning op **lange termijn** om naar downloads voor JDK 8 te gaan.

* [Maven 3](https://maven.apache.org/download.cgi)

* Een actief Azure-account. (Als u geen account hebt, kunt u binnen een paar minuten een [gratis account](https://azure.microsoft.com/pricing/free-trial/) maken.)

* Zorg ervoor dat poort 8883 is geopend in uw firewall. Het apparaatvoorbeeld in dit artikel maakt gebruik van het MQTT-protocol, dat communiceert via poort 8883. Deze poort kan worden geblokkeerd in sommige bedrijfs- en educatieve netwerkomgevingen. Zie [Verbinding maken met IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)voor meer informatie en manieren om dit probleem te omzeilen.

## <a name="create-an-iot-hub"></a>Een IoT Hub maken

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Een nieuw apparaat registreren in de IoT-hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="get-the-iot-hub-connection-string"></a>De verbindingstekenreeks voor IoT-hub

[!INCLUDE [iot-hub-howto-twin-shared-access-policy-text](../../includes/iot-hub-howto-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-custom-connection-string](../../includes/iot-hub-include-find-custom-connection-string.md)]

## <a name="create-the-service-app"></a>De service-app maken

In deze sectie maakt u een Java-app die locatiemetagegevens als tag toevoegt aan de apparaattweeling in IoT Hub die is gekoppeld aan **myDeviceId.** De app stelt eerst een IoT-hub op voor apparaten in de VS en vervolgens voor apparaten die een mobiele netwerkverbinding rapporteren.

1. Maak op je ontwikkelmachine een lege map met de naam **iot-java-twin-getstarted.**

2. Maak in de map **iot-java-twin-getstarted** een Maven-project met de naam **add-tags-query** met de volgende opdracht op uw opdrachtprompt:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=add-tags-query -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

3. Navigeer bij de opdrachtprompt naar de map **add-tags-query.**

4. Open het **bestand pom.xml** met een teksteditor in de map **add-tags-query** en voeg de volgende afhankelijkheid toe aan het **knooppunt van afhankelijkheden.** Met deze afhankelijkheid u het **iot-service-clientpakket** in uw app gebruiken om te communiceren met uw IoT-hub:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.17.1</version>
      <type>jar</type>
    </dependency>
    ```

    > [!NOTE]
    > U vindt de meest recente versie van **iot-service-client** met [Maven zoeken](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

5. Voeg het volgende **buildknooppunt** toe na het knooppunt **van afhankelijkheden.** Deze configuratie instrueert Maven om Java 1.8 te gebruiken om de app te bouwen.

    ```xml
    <build>
      <plugins>
        <plugin>
          <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-compiler-plugin</artifactId>
          <version>3.3</version>
          <configuration>
            <source>1.8</source>
            <target>1.8</target>
          </configuration>
        </plugin>
      </plugins>
    </build>
    ```

6. Sla het bestand **pom.xml** op en sluit het.

7. Open met behulp van een teksteditor het bestand **add-tags-query\src\main\java\com\mycompany\app\App.java.**

8. Voeg de volgende **import**instructies toe aan het bestand:

    ```java
    import com.microsoft.azure.sdk.iot.service.devicetwin.*;
    import com.microsoft.azure.sdk.iot.service.exceptions.IotHubException;

    import java.io.IOException;
    import java.util.HashSet;
    import java.util.Set;
    ```

9. Voeg de volgende variabelen op klasseniveau toe aan de **App**-klasse. Vervang `{youriothubconnectionstring}` de IoT-hubverbindingstekenreeks die u hebt gekopieerd in [De verbindingstekenreeks voor iot-hub opbrengen](#get-the-iot-hub-connection-string).

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    public static final String region = "US";
    public static final String plant = "Redmond43";
    ```

10. Werk de handtekening van de `throws` **hoofdmethode** bij om de volgende clausule op te nemen:

    ```java
    public static void main( String[] args ) throws IOException
    ```

11. Vervang de code in de **hoofdmethode** door de volgende code om de objecten **DeviceTwin** en **DeviceTwinDevice** te maken. Het **DeviceTwin-object** verzorgt de communicatie met uw IoT-hub. Het object **DeviceTwinDevice** vertegenwoordigt de apparaattweeling met zijn eigenschappen en tags:

    ```java
    // Get the DeviceTwin and DeviceTwinDevice objects
    DeviceTwin twinClient = DeviceTwin.createFromConnectionString(iotHubConnectionString);
    DeviceTwinDevice device = new DeviceTwinDevice(deviceId);
    ```

12. Voeg het `try/catch` volgende blok toe aan de **hoofdmethode:**

    ```java
    try {
      // Code goes here
    } catch (IotHubException e) {
      System.out.println(e.getMessage());
    } catch (IOException e) {
      System.out.println(e.getMessage());
    }
    ```

13. Als u de dubbele tags van het **gebied** en het `try` **apparaat** in uw apparaattwee wilt bijwerken, voegt u de volgende code toe in het blok:

    ```java
    // Get the device twin from IoT Hub
    System.out.println("Device twin before update:");
    twinClient.getTwin(device);
    System.out.println(device);

    // Update device twin tags if they are different
    // from the existing values
    String currentTags = device.tagsToString();
    if ((!currentTags.contains("region=" + region) && !currentTags.contains("plant=" + plant))) {
      // Create the tags and attach them to the DeviceTwinDevice object
      Set<Pair> tags = new HashSet<Pair>();
      tags.add(new Pair("region", region));
      tags.add(new Pair("plant", plant));
      device.setTags(tags);

      // Update the device twin in IoT Hub
      System.out.println("Updating device twin");
      twinClient.updateTwin(device);
    }

    // Retrieve the device twin with the tag values from IoT Hub
    System.out.println("Device twin after update:");
    twinClient.getTwin(device);
    System.out.println(device);
    ```

14. Als u de apparaattweeling wilt opvragen in `try` de IoT-hub, voegt u de volgende code toe aan het blok na de code die u in de vorige stap hebt toegevoegd. De code voert twee query's uit. Elke query retourneert maximaal 100 apparaten.

    ```java
    // Query the device twins in IoT Hub
    System.out.println("Devices in Redmond:");

    // Construct the query
    SqlQuery sqlQuery = SqlQuery.createSqlQuery("*", SqlQuery.FromType.DEVICES, "tags.plant='Redmond43'", null);

    // Run the query, returning a maximum of 100 devices
    Query twinQuery = twinClient.queryTwin(sqlQuery.getQuery(), 100);
    while (twinClient.hasNextDeviceTwin(twinQuery)) {
      DeviceTwinDevice d = twinClient.getNextDeviceTwin(twinQuery);
      System.out.println(d.getDeviceId());
    }

    System.out.println("Devices in Redmond using a cellular network:");

    // Construct the query
    sqlQuery = SqlQuery.createSqlQuery("*", SqlQuery.FromType.DEVICES, "tags.plant='Redmond43' AND properties.reported.connectivityType = 'cellular'", null);

    // Run the query, returning a maximum of 100 devices
    twinQuery = twinClient.queryTwin(sqlQuery.getQuery(), 3);
    while (twinClient.hasNextDeviceTwin(twinQuery)) {
      DeviceTwinDevice d = twinClient.getNextDeviceTwin(twinQuery);
      System.out.println(d.getDeviceId());
    }
    ```

15. Het **add-tags-query\src\main\java\com\mycompany\app\App.java-bestand** opslaan en sluiten

16. Bouw de **add-tags-query-app** en corrigeer eventuele fouten. Navigeer bij de opdrachtprompt naar de map **add-tags-query** en voer de volgende opdracht uit:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="create-a-device-app"></a>Een apparaat-app maken

In deze sectie maakt u een Java-console-app die een gerapporteerde eigenschapswaarde instelt die naar IoT Hub wordt verzonden.

1. Maak in de map **iot-java-twin-getstarted** een Maven-project met de naam **gesimuleerd apparaat** met de volgende opdracht op uw opdrachtprompt:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. Navigeer bij de opdrachtprompt naar de **map met gesimuleerdapparaat.**

3. Open het **bestand pom.xml** met een teksteditor in de map **met gesimuleerdapparaat** en voeg de volgende afhankelijkheden toe aan het **knooppunt van afhankelijkheden.** Met deze afhankelijkheid u het **iot-apparaat-clientpakket** in uw app gebruiken om te communiceren met uw IoT-hub.

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.17.5</version>
    </dependency>
    ```

    > [!NOTE]
    > U vindt de meest recente versie van **iot-device-client** met [Maven zoeken](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-device-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

4. Voeg de volgende afhankelijkheid toe aan het knooppunt **van afhankelijkheden.** Deze afhankelijkheid configureert een NOP voor de Apache [SLF4J-kapgevel,](https://www.slf4j.org/) die door de sdk van de apparaatclient wordt gebruikt om logboekregistratie te implementeren. Deze configuratie is optioneel, maar als u deze weglaat, ziet u mogelijk een waarschuwing in de console wanneer u de app uitvoert. Zie [Logboekregistratie](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/readme.md#logging) in de voorbeelden voor het *Azure IoT-apparaat SDK voor Java-leesmijbestand voor* meer informatie over het inloggen in de SDK van het apparaat.

    ```xml
    <dependency>
      <groupId>org.slf4j</groupId>
      <artifactId>slf4j-nop</artifactId>
      <version>1.7.28</version>
    </dependency>
    ```

5. Voeg het volgende **buildknooppunt** toe na het knooppunt **van afhankelijkheden.** Deze configuratie instrueert Maven om Java 1.8 te gebruiken om de app te bouwen:

    ```xml
    <build>
      <plugins>
        <plugin>
          <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-compiler-plugin</artifactId>
          <version>3.3</version>
          <configuration>
            <source>1.8</source>
            <target>1.8</target>
          </configuration>
        </plugin>
      </plugins>
    </build>
    ```

6. Sla het bestand **pom.xml** op en sluit het.

7. Open met behulp van een teksteditor het **bestand simulated-device\src\main\java\com\mycompany\app\App.java.**

8. Voeg de volgende **import**instructies toe aan het bestand:

    ```java
    import com.microsoft.azure.sdk.iot.device.*;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.*;

    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.util.Scanner;
    ```

9. Voeg de volgende variabelen op klasseniveau toe aan de **App**-klasse. Vervang `{yourdeviceconnectionstring}` de tekenreeks voor apparaatverbinding die u hebt gekopieerd in [Een nieuw apparaat registreren in de IoT-hub.](#register-a-new-device-in-the-iot-hub)

    ```java
    private static String connString = "{yourdeviceconnectionstring}";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static String deviceId = "myDeviceId";
    ```

    Deze voorbeeld-app gebruikt de **protocol**-variabele bij het maken van een **DeviceClient**-object.

10. Voeg de volgende methode toe aan de klasse **App** om informatie over twee updates af te drukken:

    ```java
    protected static class DeviceTwinStatusCallBack implements IotHubEventCallback {
        @Override
        public void execute(IotHubStatusCode status, Object context) {
          System.out.println("IoT Hub responded to device twin operation with status " + status.name());
        }
      }
    ```

11. Vervang de code in de **hoofdmethode** door de volgende code om:

    * Maak een apparaatclient om te communiceren met IoT Hub.

    * Maak een **apparaatobject** om de dubbele eigenschappen van het apparaat op te slaan.

    ```java
    DeviceClient client = new DeviceClient(connString, protocol);

    // Create a Device object to store the device twin properties
    Device dataCollector = new Device() {
      // Print details when a property value changes
      @Override
      public void PropertyCall(String propertyKey, Object propertyValue, Object context) {
        System.out.println(propertyKey + " changed to " + propertyValue);
      }
    };
    ```

12. Voeg de volgende code toe aan de **hoofdmethode** om een **door ConnectivityType** gerapporteerde eigenschap te maken en deze naar IoT Hub te verzenden:

    ```java
    try {
      // Open the DeviceClient and start the device twin services.
      client.open();
      client.startDeviceTwin(new DeviceTwinStatusCallBack(), null, dataCollector, null);

      // Create a reported property and send it to your IoT hub.
      dataCollector.setReportedProp(new Property("connectivityType", "cellular"));
      client.sendReportedProperties(dataCollector.getReportedProp());
    }
    catch (Exception e) {
      System.out.println("On exception, shutting down \n" + " Cause: " + e.getCause() + " \n" + e.getMessage());
      dataCollector.clean();
      client.closeNow();
      System.out.println("Shutting down...");
    }
    ```

13. Voeg de volgende code toe aan het einde van de **hoofdmethode.** Wachten op de **Enter-toets** geeft IoT Hub de tijd om de status van de twee bewerkingen van het apparaat te rapporteren.

    ```java
    System.out.println("Press any key to exit...");

    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();

    dataCollector.clean();
    client.close();
    ```

14. Wijzig de handtekening van de **main**-methode om de uitzonderingen als volgt op te nemen:

     ```java
     public static void main(String[] args) throws URISyntaxException, IOException
     ```

15. Sla het **gesimuleerde apparaat\src\main\java\com\mycompany\app\App.java-bestand** op en sluit deze.

16. Bouw de **app voor gesimuleerd apparaat** en corrigeer eventuele fouten. Navigeer op uw opdrachtprompt naar de **map gesimuleerd apparaat** en voer de volgende opdracht uit:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-apps"></a>De apps uitvoeren

U bent nu klaar om de console-apps uit te voeren.

1. Voer bij een opdrachtprompt in de map **add-tags-query** de volgende opdracht uit om de app **add-tags-queryservice** uit te voeren:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Java IoT Hub-service-app om tagwaarden bij te werken en apparaatquery's uit te voeren](./media/iot-hub-java-java-twin-getstarted/service-app-1.png)

    U de **plant-** en **regiotags** zien die aan de apparaattweeling zijn toegevoegd. De eerste query retourneert uw apparaat, maar de tweede niet.

2. Voer bij een opdrachtprompt in de **map gesimuleerd apparaat** de volgende opdracht uit om de eigenschap **connectivityType** gerapporteerd e.a.p.m. toe te voegen aan de apparaattweeling:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![De apparaatclient voegt de gerapporteerde eigenschap **connectivityType** toe](./media/iot-hub-java-java-twin-getstarted/device-app-1.png)

3. Voer bij een opdrachtprompt in de map **add-tags-query** de volgende opdracht uit om de app **add-tags-queryservice** een tweede keer uit te voeren:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Java IoT Hub-service-app om tagwaarden bij te werken en apparaatquery's uit te voeren](./media/iot-hub-java-java-twin-getstarted/service-app-2.png)

    Nu uw apparaat de eigenschap **connectivityType** naar IoT Hub heeft verzonden, retourneert de tweede query uw apparaat.

## <a name="next-steps"></a>Volgende stappen

In deze handleiding hebt u een nieuwe IoT-hub geconfigureerd in Azure Portal en vervolgens een apparaat-id gemaakt in het id-register van de IoT-hub. U hebt apparaatmetagegevens toegevoegd als tags van een back-end-app en een apparaat-app geschreven om informatie over apparaatconnectiviteit in de apparaattweeling te rapporteren. U hebt ook geleerd hoe u de dubbele informatie van het apparaat opvragen met de SQL-achtige IoT Hub-querytaal.

Gebruik de volgende bronnen om te leren hoe u:

* Verstuur telemetrie vanaf apparaten met de [zelfstudie Aan de slag met IoT Hub.](quickstart-send-telemetry-java.md)

* Bedien apparaten interactief (zoals het inschakelen van een ventilator vanuit een door de gebruiker bestuurde app) met de zelfstudie [Direct Methoden](quickstart-control-device-java.md) gebruiken.
