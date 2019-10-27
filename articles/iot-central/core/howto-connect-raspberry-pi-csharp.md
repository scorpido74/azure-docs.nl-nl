---
title: Een Raspberry Pi verbinden met uw Azure IoT Central-toepassingC#() | Microsoft Docs
description: Hoe u als ontwikkelaar van een apparaat een Raspberry Pi verbindt met uw Azure IoT Central-toepassing C#met behulp van.
author: viv-liu
ms.author: viviali
ms.date: 09/13/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: ba903d75707be91bb8af1271b52eb260ffcde306
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72951235"
---
# <a name="connect-a-raspberry-pi-to-your-azure-iot-central-application-c"></a>Een Raspberry Pi verbinden met uw Azure IoT Central-toepassingC#()

[!INCLUDE [howto-raspberrypi-selector](../../../includes/iot-central-howto-raspberrypi-selector.md)]

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

In dit artikel wordt beschreven hoe u als een ontwikkelaar van een apparaat een Raspberry Pi verbindt met uw Microsoft Azure IoT Central toepassing C# met behulp van de programmeer taal.

## <a name="before-you-begin"></a>Voordat u begint

U hebt de volgende onderdelen nodig om de stappen in dit artikel uit te voeren:

* Een Azure IoT Central-toepassing gemaakt op basis van de voor beeld-toepassings sjabloon **Devkits** . Zie voor meer informatie de [snelstart over het maken van een toepassing](quick-deploy-iot-central.md).
* Een Raspberry Pi-apparaat met het Raspbian-besturings systeem. De Raspberry Pi moet verbinding kunnen maken met internet. Zie [uw Raspberry Pi instellen](https://projects.raspberrypi.org/en/projects/raspberry-pi-setting-up/3)voor meer informatie.

## <a name="sample-devkits-application"></a>Voor **beeld van Devkits** -toepassing

Een toepassing die is gemaakt op basis van de voor **beeld-Devkits** -toepassings sjabloon bevat een **Raspberry Pi** -sjabloon met de volgende kenmerken:

* Telemetrie, met inbegrip van de volgende metingen die door het apparaat worden verzameld:
  * Vochtigheid
  * Temperatuur
  * Tegen
  * Magnetometer (X, Y, Z)
  * Versnellings meter (X, Y, Z)
  * Gyroscope (X, Y, Z)
* Instellingen
  * Verbruik
  * VLOTT
  * Snelheid van ventilator
  * Scha kelen tussen IR.
* Eigenschappen
  * Eigenschap van het aantal apparaten van de dobbel steen
  * Eigenschap Location Cloud

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
   dotnet add package Microsoft.Azure.Devices.Provisioning.Client
   dotnet add package Microsoft.Azure.Devices.Provisioning.Transport.Mqtt
   dotnet restore
   ```

1. Open de map `pisample` in Visual Studio code. Open vervolgens het project bestand **pisample. csproj** . Voeg de `<RuntimeIdentifiers>`-tag toe die wordt weer gegeven in het volgende code fragment:

    ```xml
    <Project Sdk="Microsoft.NET.Sdk">
      <PropertyGroup>
        <OutputType>Exe</OutputType>
        <TargetFramework>netcoreapp2.1</TargetFramework>
        <RootNamespace>pisample</RootNamespace>
        <RuntimeIdentifiers>win-arm;linux-arm</RuntimeIdentifiers>
      </PropertyGroup>
      <ItemGroup>
        <PackageReference Include="Microsoft.Azure.Devices.Client" Version="1.21.0" />
        <PackageReference Include="Microsoft.Azure.Devices.Provisioning.Client" Version="1.4.0" />
        <PackageReference Include="Microsoft.Azure.Devices.Provisioning.Transport.Mqtt" Version="1.1.8" />
      </ItemGroup>
    </Project>
    ```

    > [!NOTE]
    > De pakket versie nummers kunnen hoger zijn dan de pakketten die worden weer gegeven.

1. Sla **pisample. csproj**op. Als u met Visual Studio code wordt gevraagd om de herstel opdracht uit te voeren, kiest u **herstellen**.

1. Open **Program.cs** en vervang de inhoud door de volgende code. Werk de `{your Scope ID}`, `{your Device ID}`en `{your Device Primary Key}` bij met de waarden die u eerder hebt genoteerd:

    ```csharp
    using System;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;

    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using Microsoft.Azure.Devices.Provisioning.Client;
    using Microsoft.Azure.Devices.Provisioning.Client.Transport;

    using Newtonsoft.Json;

    namespace pisample
    {
      class Program
      {
        static string ScopeID = "{your Scope ID}";
        static string DeviceID = "{your Device ID}";
        static string PrimaryKey = "{your Device Primary Key}";
        static string GlobalDeviceEndpoint = "global.azure-devices-provisioning.net";
        static DeviceClient Client = null;
        static TwinCollection reportedProperties = new TwinCollection();
        static CancellationTokenSource cts;
        static double baseTemperature = 60;
        static double basePressure = 500;
        static double baseHumidity = 50;

        static async Task Main(string[] args)
        {
          Console.WriteLine("== Raspberry Pi Azure IoT Central example ==");

          try
          {

            using (var security = new SecurityProviderSymmetricKey(DeviceID, PrimaryKey, null))
            {
              DeviceRegistrationResult result = await RegisterDeviceAsync(security);
              if (result.Status != ProvisioningRegistrationStatusType.Assigned) {
                Console.WriteLine("Failed to register device");
                return;
              }
              IAuthenticationMethod auth = new DeviceAuthenticationWithRegistrySymmetricKey(result.DeviceId, (security as SecurityProviderSymmetricKey).GetPrimaryKey());
              Client = DeviceClient.Create(result.AssignedHub, auth, TransportType.Mqtt);
            }

            await SendDevicePropertiesAsync();

            Console.Write("Register settings changed handler...");
            await Client.SetDesiredPropertyUpdateCallbackAsync(HandleSettingChanged, null);
            Console.WriteLine("Done");

            cts = new CancellationTokenSource();
            Task task = SendTelemetryAsync(cts.Token);

            Console.WriteLine("Press any key to exit...");
            Console.ReadKey();
            cts.Cancel();
            await task;
          }
          catch (Exception ex)
          {
            Console.WriteLine();
            Console.WriteLine(ex.Message);
          }
        }

        public static async Task<DeviceRegistrationResult> RegisterDeviceAsync(SecurityProviderSymmetricKey security)
        {
            Console.WriteLine("Register device...");

            using (var transport = new ProvisioningTransportHandlerMqtt(TransportFallbackType.TcpOnly))
            {
              ProvisioningDeviceClient provClient =
                        ProvisioningDeviceClient.Create(GlobalDeviceEndpoint, ScopeID, security, transport);

              Console.WriteLine($"RegistrationID = {security.GetRegistrationID()}");


              Console.Write("ProvisioningClient RegisterAsync...");
              DeviceRegistrationResult result = await provClient.RegisterAsync();

              Console.WriteLine($"{result.Status}");
              Console.WriteLine($"ProvisioningClient AssignedHub: {result.AssignedHub}; DeviceID: {result.DeviceId}");

              return result;
            }
        }

        public static async Task SendDevicePropertiesAsync()
        {
            Console.WriteLine("Send device properties...");
            Random random = new Random();
            TwinCollection telemetryConfig = new TwinCollection();
            reportedProperties["dieNumber"] = random.Next(1, 6);
            Console.WriteLine(JsonConvert.SerializeObject(reportedProperties));

            await Client.UpdateReportedPropertiesAsync(reportedProperties);
        }

        private static async Task SendTelemetryAsync(CancellationToken token)
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


        private static async Task HandleSettingChanged(TwinCollection desiredProperties, object userContext)
        {
          Console.WriteLine("Received settings change...");
          Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));

          string setting = "fanSpeed";
          if (desiredProperties.Contains(setting))
          {
            // Act on setting change, then
            BuildAcknowledgement(desiredProperties, setting);
          }
          setting = "setVoltage";
          if (desiredProperties.Contains(setting))
          {
            // Act on setting change, then
            BuildAcknowledgement(desiredProperties, setting);
          }
          setting = "setCurrent";
          if (desiredProperties.Contains(setting))
          {
            // Act on setting change, then
            BuildAcknowledgement(desiredProperties, setting);
          }
          setting = "activateIR";
          if (desiredProperties.Contains(setting))
          {
            // Act on setting change, then
            BuildAcknowledgement(desiredProperties, setting);
          }
          Console.WriteLine("Send settings changed acknowledgement...");
          await Client.UpdateReportedPropertiesAsync(reportedProperties);
        }

        private static void BuildAcknowledgement(TwinCollection desiredProperties, string setting)
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

## <a name="run-your-net-application"></a>Uw .NET-toepassing uitvoeren

De voorbeeld toepassing bouwen en uitvoeren:

1. Voer de volgende opdracht uit in de opdracht regel omgeving:

   ```cmd/sh
   dotnet restore
   dotnet publish -r linux-arm
   ```

1. Kopieer de map `pisample\bin\Debug\netcoreapp2.1\linux-arm\publish` naar het Raspberry Pi-apparaat. U kunt de **SCP** -opdracht gebruiken om de bestanden te kopiëren, bijvoorbeeld:

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
| ratuur           | D20     | -40     | 120     | 0              |
| pressure       | hPa    | 260     | 1260    | 0              |
| magnetometerX  | mgauss | -1000   | 1000    | 0              |
| magnetometerY  | mgauss | -1000   | 1000    | 0              |
| magnetometerZ  | mgauss | -1000   | 1000    | 0              |
| versnellings meterx | mg     | -2000   | 2000    | 0              |
| versnellings meter | mg     | -2000   | 2000    | 0              |
| accelerometerZ | mg     | -2000   | 2000    | 0              |
| gyroscopeX     | mdps   | -2000   | 2000    | 0              |
| gyroscopeY     | mdps   | -2000   | 2000    | 0              |
| gyroscopeZ     | mdps   | -2000   | 2000    | 0              |

### <a name="settings"></a>Instellingen

Numerieke instellingen

| Weergavenaam | Veldnaam | Eenheden | Aantal decimalen | Minimum | Maximum | Itiaal |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| Verbruik      | setVoltage | Volt | 0              | 0       | 240     | 0       |
| VLOTT      | setCurrent | Amp  | 0              | 0       | 100     | 0       |
| Snelheid van ventilator    | fanSpeed   | RPM   | 0              | 0       | 1000    | 0       |

Instellingen in-/uitschakelen

| Weergavenaam | Veldnaam | In tekst | Uit tekst | Itiaal |
| ------------ | ---------- | ------- | -------- | ------- |
| IR           | activateIR | AAN      | UIT      | Uit     |

### <a name="properties"></a>Eigenschappen

| Type            | Weergavenaam | Veldnaam | Gegevenstype                              |
| --------------- | ------------ | ---------- | -------------------------------------- |
| Eigenschap apparaat | Aantal dobbelten   | dieNumber  | getal                                 |
| Locatie        | Locatie     | location   | {lat: float, Long: float, Alt?: float} |

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u een Raspberry Pi verbindt met uw Azure IoT Central-toepassing, is de voorgestelde volgende stap informatie over het [instellen van een sjabloon voor aangepaste apparaten](howto-set-up-template.md) voor uw eigen IOT-apparaat.
