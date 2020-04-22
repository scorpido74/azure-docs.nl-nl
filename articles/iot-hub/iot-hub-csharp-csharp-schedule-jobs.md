---
title: Taken plannen met Azure IoT Hub (.NET/.NET) | Microsoft Documenten
description: Een Azure IoT Hub-taak plannen om een directe methode op meerdere apparaten aan te roepen. U gebruikt de Azure IoT-apparaat SDK voor .NET om de gesimuleerde apparaat-apps en een service-app te implementeren om de taak uit te voeren.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: robinsh
ms.custom: mqtt
ms.openlocfilehash: 4c71a108d1967027465d127db50737119af3e2c1
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733373"
---
# <a name="schedule-and-broadcast-jobs-net"></a>Taken plannen en uitzenden (.NET)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Gebruik Azure IoT Hub om taken te plannen en bij te houden die miljoenen apparaten bijwerken. Taken gebruiken om:

* Gewenste eigenschappen bijwerken

* Tags bijwerken

* Directe methoden aanroepen

Een taak omsluit een van deze acties en volgt de uitvoering op een set apparaten die wordt gedefinieerd door een dubbele query van het apparaat. Een back-end-app kan bijvoorbeeld een taak gebruiken om een directe methode aan te roepen op 10.000 apparaten waarmee de apparaten opnieuw worden opgestart. U geeft de set apparaten met een dubbele apparaatquery op en plant de taak die op een toekomstig tijdstip moet worden uitgevoerd. De taak houdt de voortgang bij wanneer elk van de apparaten de directe herstartmethode ontvangt en uitvoert.

Zie voor meer informatie over elk van deze mogelijkheden:

* Apparaattweeling en eigenschappen: [Aan de slag met apparaattweelingen](iot-hub-csharp-csharp-twin-getstarted.md) en [zelfstudie: dubbele eigenschappen van het apparaat gebruiken](tutorial-device-twins.md)

* Directe methoden: [IoT Hub-ontwikkelaarshandleiding - directe methoden](iot-hub-devguide-direct-methods.md) en [zelfstudie: gebruik directe methoden](quickstart-control-device-dotnet.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

In deze handleiding ontdekt u hoe u:

* Maak een apparaat-app die een directe methode genaamd **LockDoor**implementeert, die kan worden aangeroepen door de back-end-app.

* Maak een back-end-app die een taak maakt om de **LockDoor-methode** op meerdere apparaten aan te roepen. Een andere taak verzendt gewenste eigenschapsupdates naar meerdere apparaten.

Aan het einde van deze zelfstudie heb je twee .NET (C#) console-apps:

* **SimulateDeviceMethods**. Deze app maakt verbinding met uw IoT-hub en implementeert de **LockDoor-directe** methode.

* **ScheduleJob**. Deze app maakt gebruik van taken om de **LockDoor-directe** methode aan te roepen en de gewenste eigenschappen van het apparaat op meerdere apparaten bij te werken.

## <a name="prerequisites"></a>Vereisten

* Visual Studio.

* Een actief Azure-account. Als je nog geen account hebt, kun je binnen een paar minuten een [gratis account](https://azure.microsoft.com/pricing/free-trial/) aanmaken.

* Zorg ervoor dat poort 8883 is geopend in uw firewall. Het apparaatvoorbeeld in dit artikel maakt gebruik van het MQTT-protocol, dat communiceert via poort 8883. Deze poort kan worden geblokkeerd in sommige bedrijfs- en educatieve netwerkomgevingen. Zie [Verbinding maken met IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)voor meer informatie en manieren om dit probleem te omzeilen.

## <a name="create-an-iot-hub"></a>Een IoT Hub maken

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Een nieuw apparaat registreren in de IoT-hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-a-simulated-device-app"></a>Een gesimuleerde apparaattoepassing maken

In deze sectie maakt u een .NET-console-app die reageert op een directe methode die door de back-end van de oplossing wordt aangeroepen.

1. Selecteer in Visual Studio **Een nieuw project maken**en kies vervolgens de projectsjabloon **console-app (.NET Framework).** Selecteer **Volgende** om door te gaan.

1. Geef in **Uw nieuwe project configureren**de naam van het project *SimulateDeviceMethods*en selecteer **Vervolgens Maken**.

    ![Uw project SimulateDeviceMethods configureren](./media/iot-hub-csharp-csharp-schedule-jobs/configure-device-app.png)

1. Klik in Solution Explorer met de rechtermuisknop op het project **SimulateDeviceMethods** en selecteer **NuGet-pakketten beheren**.

1. Selecteer **in NuGet Package Manager** **Bladeren** en zoeken naar en kies **Microsoft.Azure.Devices.Client**. Selecteer **Installeren**.

    ![NuGet Package Manager-vensterclient-app](./media/iot-hub-csharp-csharp-schedule-jobs/device-app-nuget.png)

    Met deze stap wordt een verwijzing naar het SDK NuGet-pakket voor [Azure IoT-apparaten](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/) en de afhankelijkheden ervan gedownload, geïnstalleerd en toegevoegd.

1. Voeg aan het begin van het bestand **Program.cs** de volgende `using` instructies toe:

    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using Newtonsoft.Json;
    ```

1. Voeg de volgende velden toe aan de klasse **Program**: Vervang de tijdelijke aanduidingswaarde door de tekenreeks apparaatverbinding die u in de vorige sectie hebt opgemerkt:

    ```csharp
    static string DeviceConnectionString = "<yourDeviceConnectionString>";
    static DeviceClient Client = null;
    ```

1. Voeg de volgende code toe om de directe methode op het apparaat te implementeren:

    ```csharp
    static Task<MethodResponse> LockDoor(MethodRequest methodRequest, object userContext)
    {
        Console.WriteLine();
        Console.WriteLine("Locking Door!");
        Console.WriteLine("\nReturning response for method {0}", methodRequest.Name);

        string result = "'Door was locked.'";
        return Task.FromResult(new MethodResponse(Encoding.UTF8.GetBytes(result), 200));
    }
    ```

1. Voeg de volgende methode toe om de apparaattweelinglistener op het apparaat te implementeren:

    ```csharp
    private static async Task OnDesiredPropertyChanged(TwinCollection desiredProperties, 
      object userContext)
    {
        Console.WriteLine("Desired property change:");
        Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));
    }
    ```

1. Voeg ten slotte de volgende code toe aan de **hoofdmethode** om de verbinding met uw IoT-hub te openen en de methodelistener te initialiseren:

    ```csharp
    try
    {
        Console.WriteLine("Connecting to hub");
        Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, 
          TransportType.Mqtt);

        Client.SetMethodHandlerAsync("LockDoor", LockDoor, null);
        Client.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertyChanged, null);

        Console.WriteLine("Waiting for direct method call and device twin update\n Press enter to exit.");
        Console.ReadLine();

        Console.WriteLine("Exiting...");

        Client.SetMethodHandlerAsync("LockDoor", null, null);
        Client.CloseAsync().Wait();
    }
    catch (Exception ex)
    {
        Console.WriteLine();
        Console.WriteLine("Error in sample: {0}", ex.Message);
    }
    ```

1. Sla uw werk op en bouw uw oplossing.

> [!NOTE]
> Om het simpel te houden, voert deze zelfstudie geen beleid voor nieuwe maatregelen uit. In productiecode moet u opnieuw proberenbeleid implementeren (zoals verbinding opnieuw proberen), zoals voorgesteld in [Tijdelijke foutafhandeling](/azure/architecture/best-practices/transient-faults).
>

## <a name="get-the-iot-hub-connection-string"></a>De verbindingstekenreeks voor IoT-hub

[!INCLUDE [iot-hub-howto-schedule-jobs-shared-access-policy-text](../../includes/iot-hub-howto-schedule-jobs-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-registryrw-connection-string](../../includes/iot-hub-include-find-registryrw-connection-string.md)]

## <a name="schedule-jobs-for-calling-a-direct-method-and-sending-device-twin-updates"></a>Taken plannen voor het aanroepen van een directe methode en het verzenden van dubbele updates van het apparaat

In deze sectie maakt u een .NET-console-app (met C#) die taken gebruikt om de **direct-methode LockDoor** aan te roepen en gewenste eigenschapsupdates naar meerdere apparaten te verzenden.

1. Selecteer **in** > Visual Studio Bestand**Nieuw** > **project**. Kies in **Een nieuw project maken**de optie **Console-app (.NET Framework)** en selecteer **Volgende**.

1. Geef in **Uw nieuwe project configureren**de naam van het project *ScheduleJob*. Kies Bij **Oplossing** **toevoegen aan oplossing**en selecteer Vervolgens **Maken**.

    ![Naam en configureren van u ScheduleJob-project](./media/iot-hub-csharp-csharp-schedule-jobs/config-schedule-job-app.png)

1. Klik in Solution Explorer met de rechtermuisknop op het project **ScheduleJob** en selecteer **NuGet-pakketten beheren**.

1. Selecteer **in NuGet Package Manager** **Bladeren,** zoeken naar en kies **Microsoft.Azure.Devices**en selecteer **Vervolgens Installeren**.

   Met deze stap wordt een verwijzing naar het SDK NuGet-pakket voor [Azure IoT-services](https://www.nuget.org/packages/Microsoft.Azure.Devices/) en de afhankelijkheden ervan gedownload, geïnstalleerd en toegevoegd.

1. Voeg aan het begin van het bestand **Program.cs** de volgende `using` instructies toe:

    ```csharp
    using Microsoft.Azure.Devices;
    using Microsoft.Azure.Devices.Shared;
    ```

1. Voeg de `using` volgende instructie toe als deze nog niet aanwezig is in de standaardinstructies.

    ```csharp
    using System.Threading;
    using System.Threading.Tasks;
    ```

1. Voeg de volgende velden toe aan de klasse **Program**: Vervang de tijdelijke aanduidingen door de IDo-hubverbindingstekenreeks die u eerder hebt gekopieerd in [De verbindingstekenreeks Van IoT-hub](#get-the-iot-hub-connection-string) en de naam van uw apparaat.

    ```csharp
    static JobClient jobClient;
    static string connString = "<yourIotHubConnectionString>";
    static string deviceId = "<yourDeviceId>";
    ```

1. Voeg de volgende methode toe aan de klasse **Program**:

    ```csharp
    public static async Task MonitorJob(string jobId)
    {
        JobResponse result;
        do
        {
            result = await jobClient.GetJobAsync(jobId);
            Console.WriteLine("Job Status : " + result.Status.ToString());
            Thread.Sleep(2000);
        } while ((result.Status != JobStatus.Completed) && 
          (result.Status != JobStatus.Failed));
    }
    ```

1. Voeg de volgende methode toe aan de klasse **Program**:

    ```csharp
    public static async Task StartMethodJob(string jobId)
    {
        CloudToDeviceMethod directMethod = 
          new CloudToDeviceMethod("LockDoor", TimeSpan.FromSeconds(5), 
          TimeSpan.FromSeconds(5));

        JobResponse result = await jobClient.ScheduleDeviceMethodAsync(jobId,
            $"DeviceId IN ['{deviceId}']",
            directMethod,
            DateTime.UtcNow,
            (long)TimeSpan.FromMinutes(2).TotalSeconds);

        Console.WriteLine("Started Method Job");
    }
    ```

1. Een andere methode toevoegen aan de klasse **Programma:**

    ```csharp
    public static async Task StartTwinUpdateJob(string jobId)
    {
        Twin twin = new Twin(deviceId);
        twin.Tags = new TwinCollection();
        twin.Tags["Building"] = "43";
        twin.Tags["Floor"] = "3";
        twin.ETag = "*";

        twin.Properties.Desired["LocationUpdate"] = DateTime.UtcNow;

        JobResponse createJobResponse = jobClient.ScheduleTwinUpdateAsync(
            jobId,
            $"DeviceId IN ['{deviceId}']", 
            twin, 
            DateTime.UtcNow, 
            (long)TimeSpan.FromMinutes(2).TotalSeconds).Result;

        Console.WriteLine("Started Twin Update Job");
    }
    ```

    > [!NOTE]
    > Zie [IoT Hub-querytaal](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-query-language)voor meer informatie over de syntaxis van query's .
    >

1. Voeg tot slot de volgende regels toe aan de methode **Main**:

    ```csharp
    Console.WriteLine("Press ENTER to start running jobs.");
    Console.ReadLine();

    jobClient = JobClient.CreateFromConnectionString(connString);

    string methodJobId = Guid.NewGuid().ToString();

    StartMethodJob(methodJobId);
    MonitorJob(methodJobId).Wait();
    Console.WriteLine("Press ENTER to run the next job.");
    Console.ReadLine();

    string twinUpdateJobId = Guid.NewGuid().ToString();

    StartTwinUpdateJob(twinUpdateJobId);
    MonitorJob(twinUpdateJobId).Wait();
    Console.WriteLine("Press ENTER to exit.");
    Console.ReadLine();
    ```

1. Sla uw werk op en bouw uw oplossing.

## <a name="run-the-apps"></a>De apps uitvoeren

U kunt nu de apps uitvoeren.

1. Klik in de Visual Studio Solution Explorer met de rechtermuisknop op uw oplossing en selecteer **Vervolgens StartUp-projecten instellen.**

1. Selecteer **Common Properties** > **Opstartproject**en selecteer vervolgens Meerdere **opstartprojecten**.

1. Zorg `SimulateDeviceMethods` ervoor dat is aan de `ScheduleJob`bovenkant van de lijst, gevolgd door . Stel beide acties in **op Start** en selecteer **OK**.

1. Voer de projecten uit door op **Start** te klikken of ga naar het menu **Foutopsporing** en klik op **Foutopsporing starten**.

   U ziet de uitvoer van zowel apparaat- als back-end-apps.

    ![De apps uitvoeren om taken te plannen](./media/iot-hub-csharp-csharp-schedule-jobs/schedule-jobs-console-results.png)

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een taak gebruikt om een directe methode te plannen voor een apparaat en de update van de eigenschappen van de apparaattweeling.

* Als u verder wilt gaan met IoT Hub- en apparaatbeheerpatronen, zoals firmware-update op afstand via de ether, leest u [Zelfstudie: Hoe een firmware-update te doen.](tutorial-firmware-update.md)

* Zie Aan de slag met IoT Edge voor meer informatie over het implementeren van AI naar edge-apparaten met Azure [IoT Edge.](../iot-edge/tutorial-simulate-device-linux.md)
