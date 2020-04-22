---
title: Aan de slag met Azure IoT Hub-apparaatbeheer (.NET/.NET) | Microsoft Documenten
description: Azure IoT Hub-apparaatbeheer gebruiken om een herstart van een extern apparaat te starten. U gebruikt de Azure IoT-apparaat SDK voor .NET om een gesimuleerde apparaat-app te implementeren die een directe methode en de Azure IoT-service SDK voor .NET bevat om een service-app te implementeren die de directe methode aanroept.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: robinsh
ms.custom: mqtt
ms.openlocfilehash: 3cc74faa39b21b1ab275149db4f85de8f55fd07e
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733477"
---
# <a name="get-started-with-device-management-net"></a>Aan de slag met apparaatbeheer (.NET)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

In deze handleiding ontdekt u hoe u:

* Gebruik de Azure-portal om een IoT-hub te maken en een apparaatidentiteit te maken in uw IoT-hub.

* Maak een gesimuleerde apparaat-app die een directe methode bevat die dat apparaat opnieuw opstart. Directe methoden worden aangeroepen vanuit de cloud.

* Maak een .NET-console-app die de directe herstartmethode aanroept in de gesimuleerde apparaat-app via uw IoT-hub.

Aan het eind van deze zelfstudie beschikt u over twee .NET-consoletoepassingen:

* **SimulateManagedDevice**. Deze app maakt verbinding met uw IoT-hub met de apparaatidentiteit die eerder is gemaakt, ontvangt een directe herstartmethode, simuleert een fysieke herstart en rapporteert de tijd voor de laatste herstart.

* **TriggerReboot**. Deze app roept een directe methode aan in de gesimuleerde apparaat-app, geeft het antwoord weer en geeft de bijgewerkte gerapporteerde eigenschappen weer.

## <a name="prerequisites"></a>Vereisten

* Visual Studio.

* Een actief Azure-account. Als je nog geen account hebt, kun je binnen een paar minuten een [gratis account](https://azure.microsoft.com/pricing/free-trial/) aanmaken.

* Zorg ervoor dat poort 8883 is geopend in uw firewall. Het apparaatvoorbeeld in dit artikel maakt gebruik van het MQTT-protocol, dat communiceert via poort 8883. Deze poort kan worden geblokkeerd in sommige bedrijfs- en educatieve netwerkomgevingen. Zie [Verbinding maken met IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)voor meer informatie en manieren om dit probleem te omzeilen.

## <a name="create-an-iot-hub"></a>Een IoT Hub maken

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Een nieuw apparaat registreren in de IoT-hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="get-the-iot-hub-connection-string"></a>De verbindingstekenreeks voor IoT-hub

[!INCLUDE [iot-hub-howto-device-management-shared-access-policy-text](../../includes/iot-hub-howto-device-management-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Een externe herstart op het apparaat activeren met een directe methode

In deze sectie maakt u een .NET-console-app met C#, waarmee u een externe herstart op een apparaat initieert met behulp van een directe methode. De app gebruikt apparaatdubbele query's om de laatste herstarttijd voor dat apparaat te ontdekken.

1. Selecteer in Visual Studio De optie **Een nieuw project maken**.

1. Zoek en selecteer in **Een nieuw project maken**de projectsjabloon Console App **(.NET Framework)** en selecteer **Volgende**.

1. Geef **in Uw nieuwe project configureren**een naam aan het project *TriggerReboot*en selecteer .NET Framework-versie 4.5.1 of hoger. Selecteer **Maken**.

    ![Nieuw Windows Classic Desktop-project in Visual C#](./media/iot-hub-csharp-csharp-device-management-get-started/create-trigger-reboot-configure.png)

1. Klik in **Solution Explorer**met de rechtermuisknop op het **triggerreboot-project** en selecteer **NuGet-pakketten beheren**.

1. Selecteer **Bladeren**en zoek naar en selecteer **Microsoft.Azure.Devices**. Selecteer **Installeren** om het pakket **Microsoft.Azure.Devices** te installeren.

    ![Sluit het venster Nuget Package Manager.](./media/iot-hub-csharp-csharp-device-management-get-started/create-trigger-reboot-nuget-devices.png)

   Met deze stap wordt een verwijzing naar het SDK NuGet-pakket voor [Azure IoT-services](https://www.nuget.org/packages/Microsoft.Azure.Devices/) en de afhankelijkheden ervan gedownload, geïnstalleerd en toegevoegd.

1. Voeg aan het begin van het bestand **Program.cs** de volgende `using` instructies toe:

   ```csharp
   using Microsoft.Azure.Devices;
   using Microsoft.Azure.Devices.Shared;
   ```

1. Voeg de volgende velden toe aan de klasse **Program**: Vervang `{iot hub connection string}` de tijdelijke aanduidingswaarde door de Verbindingstekenreeks iot-hub die u eerder hebt gekopieerd in [De verbindingstekenreeks van de IoT-hub .](#get-the-iot-hub-connection-string)

   ```csharp
   static RegistryManager registryManager;
   static string connString = "{iot hub connection string}";
   static ServiceClient client;
   static string targetDevice = "myDeviceId";
   ```

1. Voeg de volgende methode toe aan de klasse **Programma.**  Deze code krijgt de apparaattweeling voor het herstartapparaat en geeft de gerapporteerde eigenschappen een uitvoer.

   ```csharp
   public static async Task QueryTwinRebootReported()
   {
       Twin twin = await registryManager.GetTwinAsync(targetDevice);
       Console.WriteLine(twin.Properties.Reported.ToJson());
   }
   ```

1. Voeg de volgende methode toe aan de klasse **Programma.**  Deze code initieert de herstart op het apparaat met behulp van een directe methode.

   ```csharp
   public static async Task StartReboot()
   {
       client = ServiceClient.CreateFromConnectionString(connString);
       CloudToDeviceMethod method = new CloudToDeviceMethod("reboot");
       method.ResponseTimeout = TimeSpan.FromSeconds(30);

       CloudToDeviceMethodResult result = await 
         client.InvokeDeviceMethodAsync(targetDevice, method);

       Console.WriteLine("Invoked firmware update on device.");
   }
   ```

1. Voeg tot slot de volgende regels toe aan de methode **Main**:

   ```csharp
   registryManager = RegistryManager.CreateFromConnectionString(connString);
   StartReboot().Wait();
   QueryTwinRebootReported().Wait();
   Console.WriteLine("Press ENTER to exit.");
   Console.ReadLine();
   ```

1. Selecteer **Build** > **Build-oplossing**.

> [!NOTE]
> In deze zelfstudie wordt slechts één query uitgevoerd voor de gerapporteerde eigenschappen van het apparaat. In productiecode raden we u aan om te peilen om wijzigingen in de gerapporteerde eigenschappen te detecteren.

## <a name="create-a-simulated-device-app"></a>Een gesimuleerde apparaattoepassing maken

In deze sectie doet u het volgende:

* Maak een .NET-console-app die reageert op een directe methode die door de cloud wordt aangeroepen.

* Activeer een gesimuleerde apparaatherstart.

* Gebruik de gerapporteerde eigenschappen om apparaatdubbele query's in te schakelen om apparaten te identificeren en wanneer ze voor het laatst zijn opgestart.

Voer de volgende stappen uit om de gesimuleerde apparaat-app te maken:

1. Selecteer in Visual Studio in de TriggerReboot-oplossing die u al hebt gemaakt de optie **Nieuw project** > **New** > **bestand**. Zoek en selecteer in **Een nieuw project maken**de projectsjabloon Console App **(.NET Framework)** en selecteer **Volgende**.

1. Selecteer **In Uw nieuwe project configureren**het project *SimulateManagedDevice*en voor **Oplossing**de optie Toevoegen **aan oplossing**. Selecteer **Maken**.

    ![Uw project een naam geven en toevoegen aan de oplossing](./media/iot-hub-csharp-csharp-device-management-get-started/configure-device-app.png)

1. Klik in Solution Explorer met de rechtermuisknop op het nieuwe **project SimulateManagedDevice** en selecteer **NuGet-pakketten beheren**.

1. Selecteer **Bladeren**en zoek naar en selecteer **Microsoft.Azure.Devices.Client**. Selecteer **Installeren**.

    ![NuGet Package Manager-vensterclient-app](./media/iot-hub-csharp-csharp-device-management-get-started/create-device-nuget-devices-client.png)

   Met deze stap wordt een verwijzing naar het SDK NuGet-pakket voor [Azure IoT-apparaten](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/) en de afhankelijkheden ervan gedownload, geïnstalleerd en toegevoegd.

1. Voeg aan het begin van het bestand **Program.cs** de volgende `using` instructies toe:

    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    ```

1. Voeg de volgende velden toe aan de klasse **Program**: Vervang `{device connection string}` de tijdelijke aanduidingswaarde door de tekenreeks voor apparaatverbinding die u eerder hebt opgemerkt in [Een nieuw apparaat registreren in de IoT-hub](#register-a-new-device-in-the-iot-hub).

    ```csharp
    static string DeviceConnectionString = "{device connection string}";
    static DeviceClient Client = null;
    ```

1. Voeg het volgende toe om de directe methode op het apparaat te implementeren:

   ```csharp
   static Task<MethodResponse> onReboot(MethodRequest methodRequest, object userContext)
   {
       // In a production device, you would trigger a reboot 
       //   scheduled to start after this method returns.
       // For this sample, we simulate the reboot by writing to the console
       //   and updating the reported properties.
       try
       {
           Console.WriteLine("Rebooting!");

           // Update device twin with reboot time. 
           TwinCollection reportedProperties, reboot, lastReboot;
           lastReboot = new TwinCollection();
           reboot = new TwinCollection();
           reportedProperties = new TwinCollection();
           lastReboot["lastReboot"] = DateTime.Now;
           reboot["reboot"] = lastReboot;
           reportedProperties["iothubDM"] = reboot;
           Client.UpdateReportedPropertiesAsync(reportedProperties).Wait();
       }
       catch (Exception ex)
       {
           Console.WriteLine();
           Console.WriteLine("Error in sample: {0}", ex.Message);
       }

       string result = @"{""result"":""Reboot started.""}";
       return Task.FromResult(new MethodResponse(Encoding.UTF8.GetBytes(result), 200));
   }
   ```

1. Voeg ten slotte de volgende code toe aan de **hoofdmethode** om de verbinding met uw IoT-hub te openen en de methodelistener te initialiseren:

   ```csharp
   try
   {
       Console.WriteLine("Connecting to hub");
       Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, 
         TransportType.Mqtt);

       // setup callback for "reboot" method
       Client.SetMethodHandlerAsync("reboot", onReboot, null).Wait();
       Console.WriteLine("Waiting for reboot method\n Press enter to exit.");
       Console.ReadLine();

       Console.WriteLine("Exiting...");

       // as a good practice, remove the "reboot" handler
       Client.SetMethodHandlerAsync("reboot", null, null).Wait();
       Client.CloseAsync().Wait();
   }
   catch (Exception ex)
   {
       Console.WriteLine();
       Console.WriteLine("Error in sample: {0}", ex.Message);
   }
   ```

1. Klik in Solution Explorer met de rechtermuisknop op uw oplossing en selecteer **Startup-projecten instellen**.

1. Selecteer project Voor **Common Properties** > **Opstarten**, Selecteer Project Voor één **opstart**project en selecteer vervolgens het project **SimulateManagedDevice.** Selecteer **OK** om uw wijzigingen op te slaan.

1. Selecteer **Build** > **Build-oplossing**.

> [!NOTE]
> Om de zaken niet nodeloos ingewikkeld te maken, is in deze handleiding geen beleid voor opnieuw proberen geïmplementeerd. In productiecode moet u opnieuw proberenbeleid implementeren (zoals een exponentiële back-off), zoals voorgesteld in [Tijdelijke foutafhandeling](/azure/architecture/best-practices/transient-faults).

## <a name="run-the-apps"></a>De apps uitvoeren

Je bent nu klaar om de apps uit te voeren.

1. Als u de **SimulateManagedDevice .NET-apparaat-app SimulateManagedDevice**wilt uitvoeren, klikt u met de rechtermuisknop op het project **SimulateManagedDevice,** selecteert u **Foutopsporing**en selecteert u Vervolgens **Nieuwe instantie starten**. De app moet beginnen met luisteren naar methodeoproepen vanaf uw IoT-hub.

1. Nadat het apparaat is verbonden en wacht op aanroepen van methoden, klikt u met de rechtermuisknop op het **triggerreboot-project,** selecteert u **Foutopsporing**en selecteert u **Vervolgens Nieuwe instantie starten**.

   Je zou "Rebooten!" moeten zien. geschreven in de **SimulatedManagedDevice-console** en de gerapporteerde eigenschappen van het apparaat, waaronder de laatste herstarttijd, geschreven in de **TriggerReboot-console.**

    ![Service- en apparaat-app uitgevoerd](./media/iot-hub-csharp-csharp-device-management-get-started/combinedrun.png)

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]
