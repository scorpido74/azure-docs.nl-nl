---
title: Taken plannen met Azure IoT Hub (Java) | Microsoft Documenten
description: Een Azure IoT Hub-taak plannen om een directe methode aan te roepen en een gewenste eigenschap op meerdere apparaten in te stellen. U gebruikt de Azure IoT-apparaat SDK voor Java om de gesimuleerde apparaatapps en de Azure IoT-service SDK voor Java te implementeren om een service-app te implementeren om de taak uit te voeren.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 08/16/2019
ms.openlocfilehash: 9227192b2f7c554943fb3716ba1d1066f814c447
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77110316"
---
# <a name="schedule-and-broadcast-jobs-java"></a>Vacatures plannen en uitzenden (Java)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Gebruik Azure IoT Hub om taken te plannen en bij te houden die miljoenen apparaten bijwerken. Taken gebruiken om:

* Gewenste eigenschappen bijwerken
* Tags bijwerken
* Directe methoden aanroepen

Een taak omsluit een van deze acties en volgt de uitvoering op een set apparaten. Een dubbele query voor apparaten definieert de set apparaten waartegen de taak wordt uitgevoerd. Een back-end-app kan bijvoorbeeld een taak gebruiken om een directe methode aan te roepen op 10.000 apparaten waarmee de apparaten opnieuw worden opgestart. U geeft de set apparaten met een dubbele apparaatquery op en plant de taak die op een toekomstig tijdstip moet worden uitgevoerd. De taak houdt de voortgang bij wanneer elk van de apparaten de directe herstartmethode ontvangt en uitvoert.

Zie voor meer informatie over elk van deze mogelijkheden:

* Apparaattweeling en eigenschappen: [Aan de slag met apparaattweelingen](iot-hub-java-java-twin-getstarted.md)

* Directe methoden: [IoT Hub-ontwikkelaarshandleiding - directe methoden](iot-hub-devguide-direct-methods.md) en [zelfstudie: gebruik directe methoden](quickstart-control-device-java.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

In deze handleiding ontdekt u hoe u:

* Maak een apparaat-app die een directe methode implementeert die **lockDoor**wordt genoemd. De apparaat-app ontvangt ook gewenste eigenschapswijzigingen van de back-end-app.

* Maak een back-end-app die een taak maakt om de **lockDoor-methode** op meerdere apparaten aan te roepen. Een andere taak verzendt gewenste eigenschapsupdates naar meerdere apparaten.

Aan het einde van deze tutorial, heb je een java console apparaat app en een java console back-end app:

**gesimuleerd apparaat** dat verbinding maakt met uw IoT-hub, de **lockDoor-directe** methode implementeert en gewenste eigenschapswijzigingen verwerkt.

**planning-taken** die taken gebruiken om de **lockDoor-directe** methode aan te roepen en de gewenste eigenschappen van het apparaat op meerdere apparaten bij te werken.

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

U de [IoT-extensie voor Azure CLI](https://github.com/Azure/azure-iot-cli-extension) ook gebruiken om een apparaat toe te voegen aan uw IoT-hub.

## <a name="get-the-iot-hub-connection-string"></a>De verbindingstekenreeks voor IoT-hub

[!INCLUDE [iot-hub-howto-schedule-jobs-shared-access-policy-text](../../includes/iot-hub-howto-schedule-jobs-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-registryrw-connection-string](../../includes/iot-hub-include-find-registryrw-connection-string.md)]

## <a name="create-the-service-app"></a>De service-app maken

In deze sectie maakt u een Java-console-app die taken gebruikt om:

* Roep de **lockDoor** direct-methode op meerdere apparaten aan.

* De gewenste eigenschappen naar meerdere apparaten verzenden.

Ga als u de app maken:

1. Maak op uw ontwikkelingsmachine een lege map genaamd **iot-java-schedule-jobs.**

2. Maak in de map **iot-java-schedule-jobs** een Maven-project genaamd **schedule-jobs** met de volgende opdracht op uw opdrachtprompt. Let op: dit is één enkele, lange opdracht:

   ```cmd/sh
   mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=schedule-jobs -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
   ```

3. Navigeer bij de opdrachtprompt naar de map **planning-jobs.**

4. Open het **bestand pom.xml** met een teksteditor in de map **planning-jobs** en voeg de volgende afhankelijkheid toe aan het **knooppunt van afhankelijkheden.** Met deze afhankelijkheid u het **iot-service-clientpakket** in uw app gebruiken om te communiceren met uw IoT-hub:

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

7. Open met behulp van een teksteditor het **bestand schedule-jobs\src\main\java\com\mycompany\app\App.java.**

8. Voeg de volgende **import**instructies toe aan het bestand:

    ```java
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceTwinDevice;
    import com.microsoft.azure.sdk.iot.service.devicetwin.Pair;
    import com.microsoft.azure.sdk.iot.service.devicetwin.Query;
    import com.microsoft.azure.sdk.iot.service.devicetwin.SqlQuery;
    import com.microsoft.azure.sdk.iot.service.jobs.JobClient;
    import com.microsoft.azure.sdk.iot.service.jobs.JobResult;
    import com.microsoft.azure.sdk.iot.service.jobs.JobStatus;

    import java.util.Date;
    import java.time.Instant;
    import java.util.HashSet;
    import java.util.Set;
    import java.util.UUID;
    ```

9. Voeg de volgende variabelen op klasseniveau toe aan de **App**-klasse. Vervang `{youriothubconnectionstring}` de verbindingstekenreeks van de IoT-hub die u eerder hebt gekopieerd in [De verbindingstekenreeks voor IoT-hub:](#get-the-iot-hub-connection-string)

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    // How long the job is permitted to run without
    // completing its work on the set of devices
    private static final long maxExecutionTimeInSeconds = 30;
    ```

10. Voeg de volgende methode toe aan de klasse **App** om een taak in te plannen om de gewenste eigenschappen **van gebouw** en **vloer** in de apparaattweeling bij te werken:

    ```java
    private static JobResult scheduleJobSetDesiredProperties(JobClient jobClient, String jobId) {
      DeviceTwinDevice twin = new DeviceTwinDevice(deviceId);
      Set<Pair> desiredProperties = new HashSet<Pair>();
      desiredProperties.add(new Pair("Building", 43));
      desiredProperties.add(new Pair("Floor", 3));
      twin.setDesiredProperties(desiredProperties);
      // Optimistic concurrency control
      twin.setETag("*");

      // Schedule the update twin job to run now
      // against a single device
      System.out.println("Schedule job " + jobId + " for device " + deviceId);
      try {
        JobResult jobResult = jobClient.scheduleUpdateTwin(jobId, 
          "deviceId='" + deviceId + "'",
          twin,
          new Date(),
          maxExecutionTimeInSeconds);
        return jobResult;
      } catch (Exception e) {
        System.out.println("Exception scheduling desired properties job: " + jobId);
        System.out.println(e.getMessage());
        return null;
      }
    }
    ```

11. Als u een taak wilt plannen om de **lockDoor-methode** aan te roepen, voegt u de volgende methode toe aan de klasse **App:**

    ```java
    private static JobResult scheduleJobCallDirectMethod(JobClient jobClient, String jobId) {
      // Schedule a job now to call the lockDoor direct method
      // against a single device. Response and connection
      // timeouts are set to 5 seconds.
      System.out.println("Schedule job " + jobId + " for device " + deviceId);
      try {
        JobResult jobResult = jobClient.scheduleDeviceMethod(jobId,
          "deviceId='" + deviceId + "'",
          "lockDoor",
          5L, 5L, null,
          new Date(),
          maxExecutionTimeInSeconds);
        return jobResult;
      } catch (Exception e) {
        System.out.println("Exception scheduling direct method job: " + jobId);
        System.out.println(e.getMessage());
        return null;
      }
    };
    ```

12. Als u een taak wilt controleren, voegt u de volgende methode toe aan de klasse **App:**

    ```java
    private static void monitorJob(JobClient jobClient, String jobId) {
      try {
        JobResult jobResult = jobClient.getJob(jobId);
        if(jobResult == null)
        {
          System.out.println("No JobResult for: " + jobId);
          return;
        }
        // Check the job result until it's completed
        while(jobResult.getJobStatus() != JobStatus.completed)
        {
          Thread.sleep(100);
          jobResult = jobClient.getJob(jobId);
          System.out.println("Status " + jobResult.getJobStatus() + " for job " + jobId);
        }
        System.out.println("Final status " + jobResult.getJobStatus() + " for job " + jobId);
      } catch (Exception e) {
        System.out.println("Exception monitoring job: " + jobId);
        System.out.println(e.getMessage());
        return;
      }
    }
    ```

13. Als u wilt zoeken naar de details van de taken die u hebt uitgevoerd, voegt u de volgende methode toe:

    ```java
    private static void queryDeviceJobs(JobClient jobClient, String start) throws Exception {
      System.out.println("\nQuery device jobs since " + start);

      // Create a jobs query using the time the jobs started
      Query deviceJobQuery = jobClient
          .queryDeviceJob(SqlQuery.createSqlQuery("*", SqlQuery.FromType.JOBS, "devices.jobs.startTimeUtc > '" + start + "'", null).getQuery());

      // Iterate over the list of jobs and print the details
      while (jobClient.hasNextJob(deviceJobQuery)) {
        System.out.println(jobClient.getNextJob(deviceJobQuery));
      }
    }
    ```

14. Werk de handtekening van de `throws` **hoofdmethode** bij om de volgende clausule op te nemen:

    ```java
    public static void main( String[] args ) throws Exception
    ```

15. Als u twee taken achtereenvolgens wilt uitvoeren en controleren, vervangt u de code in de **hoofdmethode** door de volgende code:

    ```java
    // Record the start time
    String start = Instant.now().toString();

    // Create JobClient
    JobClient jobClient = JobClient.createFromConnectionString(iotHubConnectionString);
    System.out.println("JobClient created with success");

    // Schedule twin job desired properties
    // Maximum concurrent jobs is 1 for Free and S1 tiers
    String desiredPropertiesJobId = "DPCMD" + UUID.randomUUID();
    scheduleJobSetDesiredProperties(jobClient, desiredPropertiesJobId);
    monitorJob(jobClient, desiredPropertiesJobId);

    // Schedule twin job direct method
    String directMethodJobId = "DMCMD" + UUID.randomUUID();
    scheduleJobCallDirectMethod(jobClient, directMethodJobId);
    monitorJob(jobClient, directMethodJobId);

    // Run a query to show the job detail
    queryDeviceJobs(jobClient, start);

    System.out.println("Shutting down schedule-jobs app");
    ```

16. Opslaan en sluiten van de **schedule-jobs\src\main\java\com\mycompany\app\App.java-bestand**

17. Bouw de **app schedule-jobs** en corrigeer eventuele fouten. Ga bij de opdrachtprompt naar de map **planning-jobs** en voer de volgende opdracht uit:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="create-a-device-app"></a>Een apparaat-app maken

In deze sectie maakt u een Java-console-app die de gewenste eigenschappen verwerkt die vanuit IoT Hub worden verzonden en de directe methodeaanroep implementeert.

1. Maak in de map **iot-java-schedule-jobs** een **Maven-project** genaamd gesimuleerd apparaat met de volgende opdracht op uw opdrachtprompt. Let op: dit is één enkele, lange opdracht:

   ```cmd/sh
   mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
   ```

2. Navigeer bij de opdrachtprompt naar de **map met gesimuleerdapparaat.**

3. Open het **bestand pom.xml** met een teksteditor in de map **met gesimuleerdapparaat** en voeg de volgende afhankelijkheden toe aan het **knooppunt van afhankelijkheden.** Met deze afhankelijkheid u het **iot-apparaat-clientpakket** in uw app gebruiken om te communiceren met uw IoT-hub:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.17.5</version>
    </dependency>
    ```

    > [!NOTE]
    > U vindt de meest recente versie van **iot-device-client** met [Maven zoeken](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-device-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

4. Voeg de volgende afhankelijkheid toe aan het knooppunt **van afhankelijkheden.** Deze afhankelijkheid configureert een NOP voor de Apache [SLF4J-kapgevel,](https://www.slf4j.org/) die door de sdk van de apparaatclient wordt gebruikt om logboekregistratie te implementeren. Deze configuratie is optioneel, maar als u deze weglaat, ziet u mogelijk een waarschuwing in de console wanneer u de app uitvoert. Zie [Logboekregistratie](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/readme.md#logging)in de voorbeelden voor het *Azure IoT-apparaat SDK voor Java-leesmijbestand voor* meer informatie over het inloggen in de SDK van het apparaat.

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

9. Voeg de volgende variabelen op klasseniveau toe aan de **App**-klasse. Vervang `{yourdeviceconnectionstring}` de tekenreeks voor apparaatverbinding die u eerder hebt gekopieerd in het register van een nieuw apparaat in de sectie [IoT-hub:](#register-a-new-device-in-the-iot-hub)

    ```java
    private static String connString = "{yourdeviceconnectionstring}";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static final int METHOD_SUCCESS = 200;
    private static final int METHOD_NOT_DEFINED = 404;
    ```

    Deze voorbeeld-app gebruikt de **protocol**-variabele bij het maken van een **DeviceClient**-object.

10. Als u twee meldingen van apparaten op de console wilt afdrukken, voegt u de volgende geneste klasse toe aan de klasse **App:**

    ```java
    // Handler for device twin operation notifications from IoT Hub
    protected static class DeviceTwinStatusCallBack implements IotHubEventCallback {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to device twin operation with status " + status.name());
      }
    }
    ```

11. Als u directe methodemeldingen naar de console wilt afdrukken, voegt u de volgende geneste klasse toe aan de klasse **App:**

    ```java
    // Handler for direct method notifications from IoT Hub
    protected static class DirectMethodStatusCallback implements IotHubEventCallback {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to direct method operation with status " + status.name());
      }
    }
    ```

12. Als u directe methodeoproepen vanuit IoT Hub wilt verwerken, voegt u de volgende geneste klasse toe aan de klasse **App:**

    ```java
    // Handler for direct method calls from IoT Hub
    protected static class DirectMethodCallback
        implements DeviceMethodCallback {
      @Override
      public DeviceMethodData call(String methodName, Object methodData, Object context) {
        DeviceMethodData deviceMethodData;
        switch (methodName) {
          case "lockDoor": {
            System.out.println("Executing direct method: " + methodName);
            deviceMethodData = new DeviceMethodData(METHOD_SUCCESS, "Executed direct method " + methodName);
            break;
          }
          default: {
            deviceMethodData = new DeviceMethodData(METHOD_NOT_DEFINED, "Not defined direct method " + methodName);
          }
        }
        // Notify IoT Hub of result
        return deviceMethodData;
      }
    }
    ```

13. Werk de handtekening van de `throws` **hoofdmethode** bij om de volgende clausule op te nemen:

    ```java
    public static void main( String[] args ) throws IOException, URISyntaxException
    ```

14. Vervang de code in de **hoofdmethode** door de volgende code om:
    * Maak een apparaatclient om te communiceren met IoT Hub.
    * Maak een **apparaatobject** om de dubbele eigenschappen van het apparaat op te slaan.

    ```java
    // Create a device client
    DeviceClient client = new DeviceClient(connString, protocol);

    // An object to manage device twin desired and reported properties
    Device dataCollector = new Device() {
      @Override
      public void PropertyCall(String propertyKey, Object propertyValue, Object context)
      {
        System.out.println("Received desired property change: " + propertyKey + " " + propertyValue);
      }
    };
    ```

15. Als u de clientservices van het apparaat wilt starten, voegt u de volgende code toe aan de **hoofdmethode:**

    ```java
    try {
      // Open the DeviceClient
      // Start the device twin services
      // Subscribe to direct method calls
      client.open();
      client.startDeviceTwin(new DeviceTwinStatusCallBack(), null, dataCollector, null);
      client.subscribeToDeviceMethod(new DirectMethodCallback(), null, new DirectMethodStatusCallback(), null);
    } catch (Exception e) {
      System.out.println("Exception, shutting down \n" + " Cause: " + e.getCause() + " \n" + e.getMessage());
      dataCollector.clean();
      client.closeNow();
      System.out.println("Shutting down...");
    }
    ```

16. Als u wilt wachten tot de gebruiker op de **Enter-toets** drukt voordat u afsluit, voegt u de volgende code toe aan het einde van de **hoofdmethode:**

    ```java
    // Close the app
    System.out.println("Press any key to exit...");
    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();
    dataCollector.clean();
    client.closeNow();
    scanner.close();
    ```

17. Sla het **gesimuleerde apparaat\src\main\java\com\mycompany\app\App.java-bestand** op en sluit deze.

18. Bouw de **app voor gesimuleerd apparaat** en corrigeer eventuele fouten. Navigeer op uw opdrachtprompt naar de **map gesimuleerd apparaat** en voer de volgende opdracht uit:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-apps"></a>De apps uitvoeren

U bent nu klaar om de console-apps uit te voeren.

1. Voer bij een opdrachtprompt in de map **gesimuleerd apparaat** de volgende opdracht uit om de apparaat-app te starten om te luisteren naar gewenste eigenschapswijzigingen en directe methodeaanroepen:

   ```cmd/sh
   mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
   ```

   ![De apparaatclient wordt gestart](./media/iot-hub-java-java-schedule-jobs/device-app-1.png)

2. Voer bij een `schedule-jobs` opdrachtprompt in de map de volgende opdracht uit om de app voor de service **voor planning-taken** uit te voeren om twee taken uit te voeren. De eerste stelt de gewenste eigenschapswaarden in, de tweede noemt de directe methode:

   ```cmd\sh
   mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
   ```

   ![Java IoT Hub-service-app creëert twee taken](./media/iot-hub-java-java-schedule-jobs/service-app-1.png)

3. De apparaat-app verwerkt de gewenste eigenschapswijziging en de directe methodeaanroep:

   ![De apparaatclient reageert op de wijzigingen](./media/iot-hub-java-java-schedule-jobs/device-app-2.png)

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een taak gebruikt om een directe methode te plannen voor een apparaat en de update van de eigenschappen van de apparaattweeling.

Gebruik de volgende bronnen om te leren hoe u:

* Verstuur telemetrie vanaf apparaten met de [zelfstudie Aan de slag met IoT Hub.](quickstart-send-telemetry-java.md)

* Apparaten interactief bedienen (zoals het inschakelen van een ventilator vanuit een door de gebruiker bestuurde app) met de zelfstudie [met directe methoden gebruiken.s](quickstart-control-device-java.md)
