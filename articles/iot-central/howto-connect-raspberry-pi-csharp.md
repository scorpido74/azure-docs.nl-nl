---
title: Een Raspberry Pi verbinden met uw Azure IoT Central-toepassingC#() | Microsoft Docs
description: Hoe u als ontwikkelaar van een apparaat een Raspberry Pi verbindt met uw Azure IoT Central-toepassing C#met behulp van.
author: viv-liu
ms.author: viviali
ms.date: 04/15/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 3feb0b2b50851903bbd6799f46d489879e62bf43
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/21/2019
ms.locfileid: "69876224"
---
# <a name="connect-a-raspberry-pi-to-your-azure-iot-central-application-c"></a>Een Raspberry Pi verbinden met uw Azure IoT Central-toepassingC#()

[!INCLUDE [howto-raspberrypi-selector](../../includes/iot-central-howto-raspberrypi-selector.md)]

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

In dit artikel wordt beschreven hoe u als een ontwikkelaar van een apparaat een Raspberry Pi verbindt met uw Microsoft Azure IoT Central toepassing C# met behulp van de programmeer taal.

## <a name="before-you-begin"></a>Voordat u begint

U hebt de volgende onderdelen nodig om de stappen in dit artikel uit te voeren:

* Een Azure IoT Central-toepassing gemaakt op basis van de voor beeld-toepassings sjabloon **Devkits** . Zie voor meer informatie de [snelstart over het maken van een toepassing](quick-deploy-iot-central.md).
* Een Raspberry Pi-apparaat met het Raspbian-besturings systeem. De Raspberry Pi moet verbinding kunnen maken met internet. Zie [uw Raspberry Pi instellen](https://projects.raspberrypi.org/en/projects/raspberry-pi-setting-up/3)voor meer informatie.

## <a name="sample-devkits-application"></a>Voor **beeld van Devkits** -toepassing

Een toepassing die is gemaakt op basis van de voor **beeld-Devkits** -toepassings sjabloon bevat een **Raspberry Pi** -sjabloon met de volgende kenmerken:

- Telemetrie, met inbegrip van de volgende metingen die door het apparaat worden verzameld:
  - Vochtigheid
  - Temperatuur
  - Druk
  - Magnetometer (X, Y, Z)
  - Versnellings meter (X, Y, Z)
  - Gyroscope (X, Y, Z)
- Instellingen
  - Verbruik
  - Huidige
  - Snelheid van ventilator
  - Scha kelen tussen IR.
- properties
  - Eigenschap van het aantal apparaten van de dobbel steen
  - Eigenschap Location Cloud

Zie de [Raspberry Pi-Details](#raspberry-pi-device-template-details)van het apparaat voor meer informatie over de configuratie van de sjabloon.

## <a name="add-a-real-device"></a>Echt apparaat toevoegen

Voeg in uw Azure IoT Central-toepassing een echt apparaat toe vanuit de sjabloon **Raspberry Pi** . Noteer de verbindings Details van het apparaat (**scope-id**, **apparaat-id**en **primaire sleutel**). Zie [een echt apparaat toevoegen aan uw Azure IOT Central-toepassing](tutorial-add-device.md)voor meer informatie.

### <a name="create-your-net-application"></a>Uw .NET-toepassing maken

U maakt en test de toepassing op uw desktop computer.

U kunt Visual Studio code gebruiken om de volgende stappen uit te voeren. Zie [werken met C# ](https://code.visualstudio.com/docs/languages/csharp)voor meer informatie.

> [!NOTE]
> Als u wilt, kunt u de volgende stappen uitvoeren met een andere code-editor.

1. Voer de volgende opdrachten uit om uw .NET-project te initialiseren en de vereiste NuGet-pakketten toe te voegen:

   ```cmd/sh
   mkdir pisample
   cd pisample
   dotnet new console
   dotnet add package Microsoft.Azure.Devices.Client
   dotnet restore
   ```

1. Open de `pisample` map in Visual Studio code. Open vervolgens het project bestand **pisample. csproj** . Voeg de `<RuntimeIdentifiers>` tag toe die wordt weer gegeven in het volgende code fragment:

    ```xml
    <Project Sdk="Microsoft.NET.Sdk">
      <PropertyGroup>
        <OutputType>Exe</OutputType>
        <TargetFramework>netcoreapp2.0</TargetFramework>
        <RuntimeIdentifiers>win-arm;linux-arm</RuntimeIdentifiers>
      </PropertyGroup>
      <ItemGroup>
        <PackageReference Include="Microsoft.Azure.Devices.Client" Version="1.19.0" />
      </ItemGroup>
    </Project>
    ```

    > [!NOTE]
    > Het versie nummer van uw **micro soft. Azure. devices. client** pakket kan hoger zijn dan het aantal dat wordt weer gegeven.

1. Sla **pisample. csproj**op. Als u met Visual Studio code wordt gevraagd om de herstel opdracht uit te voeren, kiest u **herstellen**.

1. Open **Program.cs** en vervang de inhoud door de volgende code:

    ```csharp
    using System;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;

    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using Newtonsoft.Json;

    namespace pisample
    {
      class Program
      {
        static string DeviceConnectionString = "{your device connection string}";
        static DeviceClient Client = null;
        static TwinCollection reportedProperties = new TwinCollection();
        static CancellationTokenSource cts;
        static double baseTemperature = 60;
        static double basePressure = 500;
        static double baseHumidity = 50;
        static void Main(string[] args)
        {
          Console.WriteLine("Raspberry Pi Azure IoT Central example");

          try
          {
            InitClient();
            SendDeviceProperties();

            cts = new CancellationTokenSource();
            SendTelemetryAsync(cts.Token);

            Console.WriteLine("Wait for settings update...");
            Client.SetDesiredPropertyUpdateCallbackAsync(HandleSettingChanged, null).Wait();
            Console.ReadKey();
            cts.Cancel();
          }
          catch (Exception ex)
          {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
          }
        }

        public static void InitClient()
        {
          try
          {
            Console.WriteLine("Connecting to hub");
            Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, TransportType.Mqtt);
          }
          catch (Exception ex)
          {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
          }
        }

        public static async void SendDeviceProperties()
        {
          try
          {
            Console.WriteLine("Sending device properties:");
            Random random = new Random();
            TwinCollection telemetryConfig = new TwinCollection();
            reportedProperties["dieNumber"] = random.Next(1, 6);
            Console.WriteLine(JsonConvert.SerializeObject(reportedProperties));

            await Client.UpdateReportedPropertiesAsync(reportedProperties);
          }
          catch (Exception ex)
          {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
          }
        }

        private static async void SendTelemetryAsync(CancellationToken token)
        {
          try
          {
            Random rand = new Random();

            while (true)
            {
              double currentTemperature = baseTemperature + rand.NextDouble() * 20;
              double currentPressure = basePressure + rand.NextDouble() * 100;
              double currentHumidity = baseHumidity + rand.NextDouble() * 20;

              var telemetryDataPoint = new
              {
                humidity = currentHumidity,
                pressure = currentPressure,
                temp = currentTemperature
              };
              var messageString = JsonConvert.SerializeObject(telemetryDataPoint);
              var message = new Message(Encoding.ASCII.GetBytes(messageString));

              token.ThrowIfCancellationRequested();
              await Client.SendEventAsync(message);

              Console.WriteLine("{0} > Sending telemetry: {1}", DateTime.Now, messageString);

              await Task.Delay(1000);
            }
          }
          catch (Exception ex)
          {
            Console.WriteLine();
            Console.WriteLine("Intentional shutdown: {0}", ex.Message);
          }
        }

        private static async Task HandleSettingChanged(TwinCollection desiredProperties, object userContext)
        {
          try
          {
            Console.WriteLine("Received settings change...");
            Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));

            string setting = "fanSpeed";
            if (desiredProperties.Contains(setting))
            {
              // Act on setting change, then
              AcknowledgeSettingChange(desiredProperties, setting);
            }
            setting = "setVoltage";
            if (desiredProperties.Contains(setting))
            {
              // Act on setting change, then
              AcknowledgeSettingChange(desiredProperties, setting);
            }
            setting = "setCurrent";
            if (desiredProperties.Contains(setting))
            {
              // Act on setting change, then
              AcknowledgeSettingChange(desiredProperties, setting);
            }
            setting = "activateIR";
            if (desiredProperties.Contains(setting))
            {
              // Act on setting change, then
              AcknowledgeSettingChange(desiredProperties, setting);
            }
            await Client.UpdateReportedPropertiesAsync(reportedProperties);
          }

          catch (Exception ex)
          {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
          }
        }

        private static void AcknowledgeSettingChange(TwinCollection desiredProperties, string setting)
        {
          reportedProperties[setting] = new
          {
            value = desiredProperties[setting]["value"],
            status = "completed",
            desiredVersion = desiredProperties["$version"],
            message = "Processed"
          };
        }
      }
    }
    ```

    > [!NOTE]
    > U werkt de tijdelijke `{your device connection string}` aanduiding in de volgende stap bij.

## <a name="run-your-net-application"></a>Uw .NET-toepassing uitvoeren

Voeg uw apparaatspecifieke connection string toe aan de code voor het apparaat om te verifiëren met Azure IoT Central. Volg deze instructies om [het apparaat te genereren Connection String](howto-generate-connection-string.md) met behulp van de **scope-id**, de **apparaat-id**en de **primaire sleutel** die u eerder hebt genoteerd.

1. Vervang `{your device connection string}` in het **Program.cs** -bestand door de Connection String die u hebt gegenereerd.

1. Voer de volgende opdracht uit in de opdracht regel omgeving:

   ```cmd/sh
   dotnet restore
   dotnet publish -r linux-arm
   ```

1. Kopieer de `pisample\bin\Debug\netcoreapp2.1\linux-arm\publish` map naar uw Raspberry Pi-apparaat. U kunt de **SCP** -opdracht gebruiken om de bestanden te kopiëren, bijvoorbeeld:

    ```cmd/sh
    scp -r publish pi@192.168.0.40:publish
    ```

    Zie [Raspberry Pi Remote Access](https://www.raspberrypi.org/documentation/remote-access/)(Engelstalig) voor meer informatie.

1. Meld u aan bij uw Raspberry Pi-apparaat en voer de volgende opdrachten uit in een shell:

    ```cmd/sh
    sudo apt-get update
    sudo apt-get install libc6 libcurl3 libgcc1 libgssapi-krb5-2 liblttng-ust0 libstdc++6 libunwind8 libuuid1 zlib1g
    ```

1. Voer de volgende opdrachten uit op de Raspberry PI:

    ```cmd/sh
    cd publish
    chmod 777 pisample
    ./pisample
    ```

    ![Het programma wordt gestart](./media/howto-connect-raspberry-pi-csharp/device_begin.png)

1. In uw Azure IoT Central-toepassing kunt u zien hoe de code die wordt uitgevoerd op de Raspberry Pi communiceert met de toepassing:

   * Op de pagina **metingen** voor uw echte apparaat ziet u de telemetrie.
   * Op de pagina **Eigenschappen** ziet u de waarde van de gerapporteerde **dobbel steen** -eigenschap.
   * Op de pagina **instellingen** kunt u verschillende instellingen wijzigen op de Raspberry Pi, zoals voltage en ventilator snelheid.

     De volgende scherm afbeelding toont de Raspberry Pi de instellings wijziging ontvangen:

     ![Raspberry Pi ontvangt instellings wijziging](./media/howto-connect-raspberry-pi-csharp/device_switch.png)

## <a name="raspberry-pi-device-template-details"></a>Details van Raspberry Pi-apparaatprofiel

Een toepassing die is gemaakt op basis van de voor **beeld-Devkits** -toepassings sjabloon bevat een **Raspberry Pi** -sjabloon met de volgende kenmerken:

### <a name="telemetry-measurements"></a>Telemetrische metingen

| Veldnaam     | Eenheden  | Minimum | Maximum | Aantal decimalen |
| -------------- | ------ | ------- | ------- | -------------- |
| vochtigheid       | %      | 0       | 100     | 0              |
| temp           | °C     | -40     | 120     | 0              |
| pressure       | hPa    | 260     | 1260    | 0              |
| magnetometerX  | mgauss | -1000   | 1000    | 0              |
| magnetometerY  | mgauss | -1000   | 1000    | 0              |
| magnetometerZ  | mgauss | -1000   | 1000    | 0              |
| accelerometerX | mg     | -2000   | 2000    | 0              |
| versnellings meter | mg     | -2000   | 2000    | 0              |
| accelerometerZ | mg     | -2000   | 2000    | 0              |
| gyroscopeX     | mdps   | -2000   | 2000    | 0              |
| gyroscopeY     | mdps   | -2000   | 2000    | 0              |
| gyroscopeZ     | mdps   | -2000   | 2000    | 0              |

### <a name="settings"></a>Instellingen

Numerieke instellingen

| `Display name` | Veldnaam | Eenheden | Aantal decimalen | Minimum | Maximum | Oorspronkelijk |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| Verbruik      | setVoltage | Volt | 0              | 0       | 240     | 0       |
| Huidige      | setCurrent | Amp  | 0              | 0       | 100     | 0       |
| Snelheid van ventilator    | fanSpeed   | RPM   | 0              | 0       | 1000    | 0       |

Instellingen in-/uitschakelen

| `Display name` | Veldnaam | In tekst | Uit tekst | Oorspronkelijk |
| ------------ | ---------- | ------- | -------- | ------- |
| IR           | activateIR | AAN      | UIT      | Uit     |

### <a name="properties"></a>properties

| type            | `Display name` | Veldnaam | Gegevenstype |
| --------------- | ------------ | ---------- | --------- |
| Eigenschap apparaat | Aantal dobbelten   | dieNumber  | nummer    |
| Text            | Location     | location   | N/A       |

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u een Raspberry Pi verbindt met uw Azure IoT Central-toepassing, is de voorgestelde volgende stap informatie over het [instellen van een sjabloon voor aangepaste apparaten](howto-set-up-template.md) voor uw eigen IOT-apparaat.
