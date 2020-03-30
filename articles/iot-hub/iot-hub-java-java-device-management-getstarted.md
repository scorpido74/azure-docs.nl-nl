---
title: Aan de slag met Azure IoT Hub-apparaatbeheer (Java) | Microsoft Documenten
description: Azure IoT Hub-apparaatbeheer gebruiken om een herstart van een extern apparaat te starten. U gebruikt de Azure IoT-apparaat SDK voor Java om een gesimuleerde apparaatapp te implementeren die een directe methode bevat en de Azure IoT-service SDK voor Java om een service-app te implementeren die de directe methode aanroept.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 08/20/2019
ms.openlocfilehash: f68e25a618f5c6499ccc9d76c510eab8f1650330
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77110882"
---
# <a name="get-started-with-device-management-java"></a>Aan de slag met apparaatbeheer (Java)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

In deze handleiding ontdekt u hoe u:

* Gebruik de Azure-portal om een IoT-hub te maken en een apparaatidentiteit te maken in uw IoT-hub.

* Maak een gesimuleerde apparaat-app die een directe methode implementeert om het apparaat opnieuw op te starten. Directe methoden worden aangeroepen vanuit de cloud.

* Maak een app die de directe herstartmethode aanroept in de gesimuleerde apparaat-app via uw IoT-hub. Deze app controleert vervolgens de gerapporteerde eigenschappen van het apparaat om te zien wanneer de herstartbewerking is voltooid.

Aan het einde van deze zelfstudie heb je twee Java-console-apps:

**gesimuleerd apparaat**. Deze app:

* Maakt verbinding met uw IoT-hub met de apparaatidentiteit die eerder is gemaakt.

* Ontvangt een directe aanroep voor een rebootdirect methode.

* Simuleert een fysieke reboot.

* Rapporteert de tijd van de laatste reboot via een gerapporteerde eigenschap.

**trigger-reboot**. Deze app:

* Hiermee wordt een directe methode aanroept in de gesimuleerde apparaat-app.

* Hiermee wordt het antwoord weergegeven op de directe methodeaanroep die door het gesimuleerde apparaat wordt verzonden.

* Hiermee worden de bijgewerkte gerapporteerde eigenschappen weergegeven.

> [!NOTE]
> Zie [Azure IoT SDKs](iot-hub-devguide-sdks.md)voor informatie over de SDK's die u gebruiken om toepassingen te bouwen die op apparaten en uw oplossing back-end worden uitgevoerd.

## <a name="prerequisites"></a>Vereisten

* [Java SE Development Kit 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable). Zorg ervoor dat u **Java 8** selecteert onder ondersteuning op **lange termijn** om naar downloads voor JDK 8 te gaan.

* [Maven 3](https://maven.apache.org/download.cgi)

* Een actief Azure-account. (Als u geen account hebt, kunt u binnen een paar minuten een [gratis account](https://azure.microsoft.com/pricing/free-trial/) maken.)

* Zorg ervoor dat poort 8883 is geopend in uw firewall. Het apparaatvoorbeeld in dit artikel maakt gebruik van het MQTT-protocol, dat communiceert via poort 8883. Deze poort kan worden geblokkeerd in sommige bedrijfs- en educatieve netwerkomgevingen. Zie [Verbinding maken met IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)voor meer informatie en manieren om dit probleem te omzeilen.

## <a name="create-an-iot-hub"></a>Een IoT Hub maken

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Een nieuw apparaat registreren in de IoT-hub

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="get-the-iot-hub-connection-string"></a>De verbindingstekenreeks voor IoT-hub

[!INCLUDE [iot-hub-howto-device-management-shared-access-policy-text](../../includes/iot-hub-howto-device-management-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Een externe herstart op het apparaat activeren met een directe methode

In deze sectie maakt u een Java-console-app die:

1. Roept de directe herstartmethode in de gesimuleerde apparaat-app.

2. Hiermee wordt het antwoord weergegeven.

3. Polls de gerapporteerde eigenschappen verzonden vanaf het apparaat om te bepalen wanneer de reboot is voltooid.

Deze console-app maakt verbinding met uw IoT-hub om de directe methode aan te roepen en de gerapporteerde eigenschappen te lezen.

1. Maak een lege map genaamd **dm-get-started**.

2. Maak in de map **dm-get-started** een Maven-project genaamd **trigger-reboot** met de volgende opdracht op uw opdrachtprompt:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=trigger-reboot -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

3. Navigeer bij de opdrachtprompt naar de map voor het opnieuw opstarten van **trigger.**

4. Open met een teksteditor het **bestand pom.xml** in de map **trigger-reboot** en voeg de volgende afhankelijkheid toe aan het **knooppunt van afhankelijkheden.** Met deze afhankelijkheid u het iot-service-clientpakket in uw app gebruiken om te communiceren met uw IoT-hub:

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

7. Open met behulp van een teksteditor het **bronbestand trigger-reboot\src\main\java\com\mycompany\app\App.java.**

8. Voeg de volgende **import**instructies toe aan het bestand:

    ```java
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceMethod;
    import com.microsoft.azure.sdk.iot.service.devicetwin.MethodResult;
    import com.microsoft.azure.sdk.iot.service.exceptions.IotHubException;
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceTwin;
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceTwinDevice;

    import java.io.IOException;
    import java.util.concurrent.TimeUnit;
    import java.util.concurrent.Executors;
    import java.util.concurrent.ExecutorService;
    ```

9. Voeg de volgende variabelen op klasseniveau toe aan de **App**-klasse. Vervang `{youriothubconnectionstring}` de Verbindingstekenreeks IoT Hub die u eerder hebt gekopieerd in [De verbindingstekenreeks van de IoT-hub:](#get-the-iot-hub-connection-string)

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    private static final String methodName = "reboot";
    private static final Long responseTimeout = TimeUnit.SECONDS.toSeconds(30);
    private static final Long connectTimeout = TimeUnit.SECONDS.toSeconds(5);
    ```

10. Als u een thread wilt implementeren die de gerapporteerde eigenschappen van de apparaattweeling elke 10 seconden leest, voegt u de volgende geneste klasse toe aan de klasse **App:**

    ```java
    private static class ShowReportedProperties implements Runnable {
      public void run() {
        try {
          DeviceTwin deviceTwins = DeviceTwin.createFromConnectionString(iotHubConnectionString);
          DeviceTwinDevice twinDevice = new DeviceTwinDevice(deviceId);
          while (true) {
            System.out.println("Get reported properties from device twin");
            deviceTwins.getTwin(twinDevice);
            System.out.println(twinDevice.reportedPropertiesToString());
            Thread.sleep(10000);
          }
        } catch (Exception ex) {
          System.out.println("Exception reading reported properties: " + ex.getMessage());
        }
      }
    }
    ```

11. Wijzig de handtekening van de **hoofdmethode** om de volgende uitzondering te maken:

    ```java
    public static void main(String[] args) throws IOException
    ```

12. Als u de directe herstartmethode op het gesimuleerde apparaat wilt aanroepen, vervangt u de code in de **hoofdmethode** door de volgende code:

    ```java
    System.out.println("Starting sample...");
    DeviceMethod methodClient = DeviceMethod.createFromConnectionString(iotHubConnectionString);

    try
    {
      System.out.println("Invoke reboot direct method");
      MethodResult result = methodClient.invoke(deviceId, methodName, responseTimeout, connectTimeout, null);

      if(result == null)
      {
        throw new IOException("Invoke direct method reboot returns null");
      }
      System.out.println("Invoked reboot on device");
      System.out.println("Status for device:   " + result.getStatus());
      System.out.println("Message from device: " + result.getPayload());
    }
    catch (IotHubException e)
    {
        System.out.println(e.getMessage());
    }
    ```

13. Als u de thread wilt starten om de gerapporteerde eigenschappen van het gesimuleerde apparaat te peilen, voegt u de volgende code toe aan de **hoofdmethode:**

    ```java
    ShowReportedProperties showReportedProperties = new ShowReportedProperties();
    ExecutorService executor = Executors.newFixedThreadPool(1);
    executor.execute(showReportedProperties);
    ```

14. Als u de app wilt stoppen, voegt u de volgende code toe aan de **hoofdmethode:**

    ```java
    System.out.println("Press ENTER to exit.");
    System.in.read();
    executor.shutdownNow();
    System.out.println("Shutting down sample...");
    ```

15. Sla het **trigger-reboot\src\main\java\com\mycompany\app\App.java-bestand** op en sluit deze.

16. Bouw de back-end-back-end-app **voor trigger-reboot** en corrigeer eventuele fouten. Ga bij de opdrachtprompt naar de map voor het opnieuw opstarten van **triggeren** en voer de volgende opdracht uit:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="create-a-simulated-device-app"></a>Een gesimuleerde apparaattoepassing maken

In deze sectie maakt u een Java-console-app die een apparaat simuleert. De app luistert naar de directe oproep voor het opnieuw opstarten van uw IoT-hub en reageert onmiddellijk op die oproep. De app slaapt vervolgens een tijdje om het rebootproces te simuleren voordat deze een gerapporteerde eigenschap gebruikt om de **back-end-app trigger-reboot** te melden dat de reboot is voltooid.

1. Maak in de map **dm-get-started** een Maven-project genaamd **gesimuleerd apparaat** met de volgende opdracht op uw opdrachtprompt:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. Navigeer bij de opdrachtprompt naar de **map met gesimuleerdapparaat.**

3. Open het **bestand pom.xml** met een teksteditor in de map **met gesimuleerd apparaat** en voeg de volgende afhankelijkheid toe aan het knooppunt van **afhankelijkheden.** Met deze afhankelijkheid u het iot-service-clientpakket in uw app gebruiken om te communiceren met uw IoT-hub:

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

7. Open met behulp van een teksteditor het **bronbestand gesimuleerd apparaat\src\main\java\com\mycompany\app\App.java.**

8. Voeg de volgende **import**instructies toe aan het bestand:

    ```java
    import com.microsoft.azure.sdk.iot.device.*;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.*;

    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.time.LocalDateTime;
    import java.util.Scanner;
    import java.util.Set;
    import java.util.HashSet;
    ```

9. Voeg de volgende variabelen op klasseniveau toe aan de **App**-klasse. Vervang `{yourdeviceconnectionstring}` de tekenreeks apparaatverbinding die u in het register van een nieuw apparaat in de sectie [IoT-hub](#register-a-new-device-in-the-iot-hub) hebt opgemerkt:

    ```java
    private static final int METHOD_SUCCESS = 200;
    private static final int METHOD_NOT_DEFINED = 404;

    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static String connString = "{yourdeviceconnectionstring}";
    private static DeviceClient client;
    ```

10. Als u een callbackhandler wilt implementeren voor directe functiestatusgebeurtenissen, voegt u de volgende geneste klasse toe aan de klasse **App:**

    ```java
    protected static class DirectMethodStatusCallback implements IotHubEventCallback
    {
      public void execute(IotHubStatusCode status, Object context)
      {
        System.out.println("IoT Hub responded to device method operation with status " + status.name());
      }
    }
    ```

11. Als u een callbackhandler wilt implementeren voor dubbele statusgebeurtenissen van het apparaat, voegt u de volgende geneste klasse toe aan de klasse **App:**

    ```java
    protected static class DeviceTwinStatusCallback implements IotHubEventCallback
    {
        public void execute(IotHubStatusCode status, Object context)
        {
            System.out.println("IoT Hub responded to device twin operation with status " + status.name());
        }
    }
    ```

12. Als u een callbackhandler voor eigenschapsgebeurtenissen wilt implementeren, voegt u de volgende geneste klasse toe aan de klasse **App:**

    ```java
    protected static class PropertyCallback implements PropertyCallBack<String, String>
    {
      public void PropertyCall(String propertyKey, String propertyValue, Object context)
      {
        System.out.println("PropertyKey:     " + propertyKey);
        System.out.println("PropertyKvalue:  " + propertyKey);
      }
    }
    ```

13. Als u een thread wilt implementeren om het opnieuw opstarten van het apparaat te simuleren, voegt u de volgende geneste klasse toe aan de klasse **App.** De thread slaapt vijf seconden en stelt vervolgens de **laatsteReboot gerapporteerde** eigenschap in:

    ```java
    protected static class RebootDeviceThread implements Runnable {
      public void run() {
        try {
          System.out.println("Rebooting...");
          Thread.sleep(5000);
          Property property = new Property("lastReboot", LocalDateTime.now());
          Set<Property> properties = new HashSet<Property>();
          properties.add(property);
          client.sendReportedProperties(properties);
          System.out.println("Rebooted");
        }
        catch (Exception ex) {
          System.out.println("Exception in reboot thread: " + ex.getMessage());
        }
      }
    }
    ```

14. Als u de directe methode op het apparaat wilt implementeren, voegt u de volgende geneste klasse toe aan de klasse **App.** Wanneer de gesimuleerde app een aanroep naar de directe **herstartmethode** ontvangt, wordt een bevestiging geretourneerd aan de beller en wordt een thread gestart om de herstart te verwerken:

    ```java
    protected static class DirectMethodCallback implements com.microsoft.azure.sdk.iot.device.DeviceTwin.DeviceMethodCallback
    {
      @Override
      public DeviceMethodData call(String methodName, Object methodData, Object context)
      {
        DeviceMethodData deviceMethodData;
        switch (methodName)
        {
          case "reboot" :
          {
            int status = METHOD_SUCCESS;
            System.out.println("Received reboot request");
            deviceMethodData = new DeviceMethodData(status, "Started reboot");
            RebootDeviceThread rebootThread = new RebootDeviceThread();
            Thread t = new Thread(rebootThread);
            t.start();
            break;
          }
          default:
          {
            int status = METHOD_NOT_DEFINED;
            deviceMethodData = new DeviceMethodData(status, "Not defined direct method " + methodName);
          }
        }
        return deviceMethodData;
      }
    }
    ```

15. Wijzig de handtekening van de **hoofdmethode** om de volgende uitzonderingen te maken:

    ```java
    public static void main(String[] args) throws IOException, URISyntaxException
    ```

16. Als u een **DeviceClient wilt instantiëren,** vervangt u de code in de **hoofdmethode** door de volgende code:

    ```java
    System.out.println("Starting device client sample...");
    client = new DeviceClient(connString, protocol);
    ```

17. Als u wilt beginnen met luisteren naar directe methodeaanroepen, voegt u de volgende code toe aan de **hoofdmethode:**

    ```java
    try
    {
      client.open();
      client.subscribeToDeviceMethod(new DirectMethodCallback(), null, new DirectMethodStatusCallback(), null);
      client.startDeviceTwin(new DeviceTwinStatusCallback(), null, new PropertyCallback(), null);
      System.out.println("Subscribed to direct methods and polling for reported properties. Waiting...");
    }
    catch (Exception e)
    {
      System.out.println("On exception, shutting down \n" + " Cause: " + e.getCause() + " \n" +  e.getMessage());
      client.close();
      System.out.println("Shutting down...");
    }
    ```

18. Als u de apparaatsimulator wilt afsluiten, voegt u de volgende code toe aan de **hoofdmethode:**

    ```java
    System.out.println("Press any key to exit...");
    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();
    scanner.close();
    client.close();
    System.out.println("Shutting down...");
    ```

19. Sla het gesimuleerde apparaat\src\main\java\com\mycompany\app\App.java-bestand op en sluit deze.

20. Bouw de **app voor gesimuleerd apparaat** en corrigeer eventuele fouten. Navigeer op uw opdrachtprompt naar de **map gesimuleerd apparaat** en voer de volgende opdracht uit:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-apps"></a>De apps uitvoeren

Je bent nu klaar om de apps uit te voeren.

1. Voer bij een opdrachtprompt in de map gesimuleerd apparaat de volgende opdracht uit om te beginnen met luisteren naar oproepen met een rebootmethode vanaf uw **IoT-hub:**

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Java IoT Hub gesimuleerde apparaat app om te luisteren voor reboot directe methode oproepen](./media/iot-hub-java-java-device-management-getstarted/launchsimulator.png)

2. Voer bij een opdrachtprompt in de **trigger-rebootmap** de volgende opdracht uit om de rebootmethode op uw gesimuleerde apparaat aan te roepen vanaf uw IoT-hub:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Java IoT Hub-service-app om de directe rebootmethode aan te roepen](./media/iot-hub-java-java-device-management-getstarted/triggerreboot.png)

3. Het gesimuleerde apparaat reageert op de directe aanroep voor het opnieuw opstarten van de methode:

    ![Java IoT Hub gesimuleerde apparaat app reageert op de directe methode oproep](./media/iot-hub-java-java-device-management-getstarted/respondtoreboot.png)

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]
