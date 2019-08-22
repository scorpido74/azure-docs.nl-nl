---
title: Taken plannen met Azure IoT Hub (.NET/.NET) | Microsoft Docs
description: Een Azure IoT Hub-taak plannen voor het aanroepen van een directe methode op meerdere apparaten. U gebruikt de Azure IoT Device SDK voor .NET voor het implementeren van de gesimuleerde apparaat-apps en een service-app om de taak uit te voeren.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/16/2019
ms.author: robinsh
ms.openlocfilehash: 3594828ff3a79242e1cfd4663c415d8de502a329
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/21/2019
ms.locfileid: "69872775"
---
# <a name="schedule-and-broadcast-jobs-net"></a>Taken plannen en uitzenden (.NET)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Gebruik Azure IoT Hub om taken te plannen en bij te houden waarmee miljoenen apparaten worden bijgewerkt. Taken gebruiken voor:

* Gewenste eigenschappen bijwerken
* Tags bijwerken
* Directe methoden aanroepen

Een taak verpakt een van deze acties en traceert de uitvoering op basis van een set apparaten die is gedefinieerd door een dubbele query voor een apparaat. Een back-end-app kan bijvoorbeeld een taak gebruiken om een directe methode aan te roepen op 10.000-apparaten die de apparaten opnieuw opstarten. U geeft de set apparaten met een dubbele query voor een apparaat op en plant de taak op een later tijdstip. De taak houdt de voortgang bij wanneer elk apparaat wordt ontvangen en de methode voor opnieuw opstarten direct wordt uitgevoerd.

Zie voor meer informatie over elk van deze mogelijkheden:

* Dubbele en eigenschappen van apparaat: [Aan de slag met apparaatdubbels](iot-hub-csharp-csharp-twin-getstarted.md) en [zelf studie: De dubbele eigenschappen van een apparaat gebruiken](tutorial-device-twins.md)

* Directe methoden: [IOT hub ontwikkelaars handleiding-directe methoden](iot-hub-devguide-direct-methods.md) en [zelf studie: Directe methoden gebruiken](quickstart-control-device-dotnet.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

In deze zelfstudie ontdekt u hoe u:

* Maak een apparaat-app die een directe methode met de naam **LockDoor** implementeert die kan worden aangeroepen door de back-end-app.

* Maak een back-end-app die een taak maakt om de **LockDoor** direct-methode aan te roepen op meerdere apparaten. Een andere taak verzendt gewenste eigenschaps updates naar meerdere apparaten.

Aan het einde van deze zelf studie hebt u twee .NET (C#)-console-apps:

**SimulateDeviceMethods** die verbinding maakt met uw IOT-hub en de methode **LockDoor** direct implementeert.

**ScheduleJob** die gebruikmaakt van taken om de **LockDoor** direct-methode aan te roepen en de dubbele gewenste eigenschappen van het apparaat op meerdere apparaten bij te werken.

## <a name="prerequisites"></a>Vereisten

Voor deze zelfstudie hebt u het volgende nodig:

* Visual Studio. In deze zelf studie wordt Visual Studio 2017 gebruikt.

* Een actief Azure-account. Als u geen account hebt, kunt u in slechts een paar minuten een [gratis account](https://azure.microsoft.com/pricing/free-trial/) maken.

## <a name="create-an-iot-hub"></a>Een IoT Hub maken

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Een nieuw apparaat registreren in de IoT-hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-a-simulated-device-app"></a>Een gesimuleerde apparaattoepassing maken

In deze sectie maakt u een .NET-console-app die reageert op een directe methode die wordt aangeroepen door de back-end van de oplossing.

1. Voeg in Visual Studio een klassiek C# Windows-bureau blad-project toe aan een nieuwe of bestaande oplossing met behulp van de project sjabloon **console toepassing** . Geef het project de naam **SimulateDeviceMethods**.

    ![Nieuwe app C# voor klassieke Windows-apparaten](./media/iot-hub-csharp-csharp-schedule-jobs/create-device-app.png)

2. Klik in Solution Explorer met de rechter muisknop op het project **SimulateDeviceMethods** en selecteer vervolgens **NuGet-pakketten beheren...** .

3. Klik in het venster **NuGet package manager** op **Browse** en zoek naar **micro soft. Azure. devices. client**. Selecteer **installeren** om het **micro soft. Azure. devices. client** -pakket te installeren en accepteer de gebruiks voorwaarden. Met deze procedure wordt een verwijzing naar het [Azure IOT Device SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/) NuGet-pakket en de bijbehorende afhankelijkheden gedownload, geïnstalleerd en toegevoegd.

    ![NuGet Package Manager-venster Client-App](./media/iot-hub-csharp-csharp-schedule-jobs/device-app-nuget.png)

4. Voeg aan het begin van het bestand **Program.cs** de volgende `using` instructies toe:

    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using Newtonsoft.Json;
    ```

5. Voeg de volgende velden toe aan de klasse **Program**: Vervang de waarde van de tijdelijke aanduiding door het apparaat connection string dat u in de vorige sectie hebt genoteerd:

    ```csharp
    static string DeviceConnectionString = "<yourDeviceConnectionString>";
    static DeviceClient Client = null;
    ```

6. Voeg het volgende toe om de directe methode op het apparaat te implementeren:

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

7. Voeg het volgende toe om de apparaatdubbels-listener voor het apparaat te implementeren op het apparaat:

    ```csharp
    private static async Task OnDesiredPropertyChanged(TwinCollection desiredProperties, 
      object userContext)
    {
        Console.WriteLine("Desired property change:");
        Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));
    }
    ```

8. Voeg tot slot de volgende code toe aan de methode **Main** om de verbinding met uw IOT-hub te openen en de methode-listener te initialiseren:

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

9. Sla uw werk op en bouw uw oplossing.

> [!NOTE]
> Om de zaken niet nodeloos ingewikkeld te maken, is in deze handleiding geen beleid voor opnieuw proberen geïmplementeerd. In productie code moet u beleid voor opnieuw proberen implementeren (zoals opnieuw proberen van verbinding), zoals wordt voorgesteld in het artikel, [tijdelijke fout afhandeling](/azure/architecture/best-practices/transient-faults).
>

## <a name="get-the-iot-hub-connection-string"></a>De IoT hub-connection string ophalen

[!INCLUDE [iot-hub-howto-schedule-jobs-shared-access-policy-text](../../includes/iot-hub-howto-schedule-jobs-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-registryrw-connection-string](../../includes/iot-hub-include-find-registryrw-connection-string.md)]

## <a name="schedule-jobs-for-calling-a-direct-method-and-sending-device-twin-updates"></a>Taken plannen voor het aanroepen van een directe methode en het verzenden van dubbele updates voor een apparaat

In deze sectie maakt u een .NET-console-app ( C#met) die gebruikmaakt van taken om de **LockDoor** direct-methode aan te roepen en gewenste eigenschaps updates naar meerdere apparaten te verzenden.

1. Voeg in Visual Studio een Visual C# Classic Windows Desktop-project toe aan de huidige oplossing met behulp van de projectsjabloon **Console Application**. Geef het project de naam **ScheduleJob**.

    ![Nieuw Windows Classic Desktop-project in Visual C#](./media/iot-hub-csharp-csharp-schedule-jobs/createnetapp.png)

2. Klik in Solution Explorer met de rechter muisknop op het project **ScheduleJob** en selecteer vervolgens **NuGet-pakketten beheren...** .

3. Selecteer in het venster **NuGet package manager** **Bladeren**, zoek naar **micro soft. Azure. devices**, selecteer **installeren** om het **micro soft. Azure. devices** -pakket te installeren en accepteer de gebruiks voorwaarden. Met deze stap wordt een verwijzing naar het [Azure IOT Service SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices/) NuGet-pakket en de bijbehorende afhankelijkheden gedownload, geïnstalleerd en toegevoegd.

    ![Sluit het venster Nuget Package Manager.](./media/iot-hub-csharp-csharp-schedule-jobs/servicesdknuget.png)

4. Voeg aan het begin van het bestand **Program.cs** de volgende `using` instructies toe:

    ```csharp
    using Microsoft.Azure.Devices;
    using Microsoft.Azure.Devices.Shared;
    ```

5. Voeg de volgende `using` instructie toe als deze niet al aanwezig is in de standaard-instructies.

    ```csharp
    using System.Threading;
    using System.Threading.Tasks;
    ```

6. Voeg de volgende velden toe aan de klasse **Program**: Vervang de tijdelijke aanduidingen door de IoT Hub connection string die u eerder hebt gekopieerd in [de IOT hub-Connection String](#get-the-iot-hub-connection-string) en de naam van uw apparaat ophalen.

    ```csharp
    static JobClient jobClient;
    static string connString = "<yourIotHubConnectionString>";
    static string deviceId = "<yourDeviceId>";
    ```

7. Voeg de volgende methode toe aan de klasse **Program**:

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

8. Voeg de volgende methode toe aan de klasse **Program**:

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

9. Voeg een andere methode toe aan de klasse **Program** :

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
    > Zie [IOT hub query language (Engelstalig](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-query-language)) voor meer informatie over de syntaxis van query's.
    >

10. Voeg tot slot de volgende regels toe aan de methode **Main**:

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

11. Sla uw werk op en bouw uw oplossing.

## <a name="run-the-apps"></a>De apps uitvoeren

U kunt nu de apps uitvoeren.

1. Klik in Visual Studio Solution Explorer met de rechter muisknop op uw oplossing en selecteer vervolgens **opstart projecten instellen**. Selecteer vervolgens **meerdere opstart projecten**. Zorg ervoor dat **SimulateDeviceMethods** boven aan de lijst staat, gevolgd door **ScheduleJob**. Stel beide acties in op **Start** en selecteer **OK**.

2. Voer de projecten uit door op **Start** te klikken of door naar het menu **fout opsporing** te gaan en **fout opsporing starten**te selecteren.

3. U ziet de uitvoer van het apparaat en de back-end-apps.

    ![De apps uitvoeren om taken te plannen](./media/iot-hub-csharp-csharp-schedule-jobs/schedulejobs.png)

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u een taak gebruikt voor het plannen van een directe methode op een apparaat en het bijwerken van de eigenschappen van het apparaat.

Lees [zelf studie als u aan de slag wilt gaan met IOT hub en patronen voor Apparaatbeheer, zoals extern via de firmware-update: Een firmware-update](tutorial-firmware-update.md)uitvoeren.

Zie aan de slag [met IOT Edge](../iot-edge/tutorial-simulate-device-linux.md)voor meer informatie over het implementeren van AI naar edge-apparaten met Azure IOT Edge.
