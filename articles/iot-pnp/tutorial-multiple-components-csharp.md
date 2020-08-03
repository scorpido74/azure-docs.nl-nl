---
title: C#-voorbeeldcode voor een IoT Plug and Play Preview-apparaat met meerdere onderdelen koppelen aan IoT Hub | Microsoft Docs
description: Schrijf C#-voorbeeldcode voor een IoT Plug and Play Preview-apparaat dat meerdere onderdelen gebruikt en verbinding maakt met een IoT-hub, en voer deze uit. Gebruik het hulpprogramma Azure IoT Explorer om de gegevens te bekijken die door het apparaat naar de hub worden verzonden.
author: ericmitt
ms.author: ericmitt
ms.date: 07/14/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: cf2662bef194bc2b7afdb07f55809ba63f40fd6e
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87351786"
---
# <a name="tutorial-connect-an-iot-plug-and-play-multiple-component-device-application-running-on-windows-to-iot-hub-c"></a>Zelfstudie: Een IoT Plug and Play-apparaattoepassing met meerdere onderdelen die in Windows wordt uitgevoerd, verbinden met IoT Hub (C#)

[!INCLUDE [iot-pnp-tutorials-device-selector.md](../../includes/iot-pnp-tutorials-device-selector.md)]

In deze zelfstudie ziet u hoe u een voorbeeld van een IoT Plug and Play-apparaattoepassing met onderdelen en een hoofdinterface maakt, hoe u de toepassing verbindt met uw IoT-hub en hoe u het hulpprogramma Azure IoT Explorer gebruikt om de gegevens weer te geven die naar de hub worden verzonden. De voorbeeldtoepassing wordt geschreven in C# en is inbegrepen in de Azure IoT device-SDK voor C#. Een ontwikkelaar van oplossingen kan het hulpprogramma Azure IoT Explorer gebruiken om inzicht te krijgen in de mogelijkheden van een IoT Plug and Play-apparaat zonder apparaatcode weer te geven.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Vereisten

Om deze zelfstudie in Windows te voltooien, installeert u de volgende software in uw lokale Windows-omgeving:

* [Visual Studio (Community, Professional of Enterprise)](https://visualstudio.microsoft.com/downloads/).
* [Git](https://git-scm.com/download/).
* [CMake](https://cmake.org/download/).

### <a name="azure-iot-explorer"></a>Azure IoT Explorer

Om in het tweede deel van deze zelfstudie te communiceren met het voorbeeldapparaat, gebruikt u het hulpprogramma **Azure IoT Explorer**. [Download en installeer de nieuwste release van Azure IoT Explorer](./howto-use-iot-explorer.md) voor uw besturingssysteem.

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Voer de volgende opdracht uit om de _verbindingsreeks voor IoT Hub_ op te halen voor uw hub. Noteer deze verbindingsreeks voor later gebruik in deze zelfstudie:

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

> [!TIP]
> U kunt het hulpprogramma Azure IoT Explorer ook gebruiken om de verbindingsreeks voor IoT Hub te vinden.

Voer de volgende opdracht uit om de _apparaatverbindingsreeks_ op te halen voor het apparaat dat u aan de hub hebt toegevoegd. Noteer deze verbindingsreeks voor later gebruik in deze zelfstudie:

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output table
```

[!INCLUDE [iot-pnp-download-models.md](../../includes/iot-pnp-download-models.md)]

## <a name="download-the-code"></a>De code downloaden

In deze zelfstudie bereidt u een ontwikkelomgeving voor die kan worden gebruikt voor het klonen en compileren van de Azure IoT Hub Device C#-SDK.

Open een opdrachtprompt in de map van uw keuze. Voer de volgende opdracht uit om de GitHub-opslagplaats [Azure IoT C#-SDK’s en -bibliotheken](https://github.com/Azure/azure-iot-sdk-csharp) te klonen op deze locatie:

```cmd
git clone https://github.com/Azure/azure-iot-sdk-c.git
```

## <a name="build-the-code"></a>De code bouwen

Open het oplossingsbestand **azureiot.sln** in Visual Studio 2019 en stel het project **TemperatureController** in als het opstartproject. In **Solution Explorer** kunt u dit projectbestand vinden in **iothub > device > samples**.

U kunt het voorbeeld nu in Visual Studio bouwen en uitvoeren in foutopsporingsmodus.

## <a name="run-the-device-sample"></a>Het apparaatvoorbeeld uitvoeren

Maak een omgevingsvariabele genaamd **IOTHUB_DEVICE_CONNECTION_STRING** om de apparaatverbindingsreeks op te slaan die u eerder hebt genoteerd.

Om de code-uitvoering in Visual Studio in Windows bij te houden, voegt u een onderbrekingspunt toe aan de functie `main` in het bestand program.cs.

Het apparaat is nu klaar om opdrachten en updates van eigenschappen te ontvangen, en is begonnen met het verzenden van telemetriegegevens naar de hub. Laat het voorbeeld actief tijdens het uitvoeren van de volgende stappen.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Code valideren met Azure IoT Explorer

Nadat het voorbeeld van de apparaatclient is gestart, gebruikt u het hulpprogramma Azure IoT Explorer om te verifiëren dat het werkt.

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>De code bekijken

Met dit voorbeeld wordt een IoT Plug and Play-temperatuurregelingsapparaat geïmplementeerd. Het model dat met dit voorbeeld wordt geïmplementeerd, maakt gebruik van [meerdere onderdelen](concepts-components.md). Het [Digital Twins Definition Language-modelbestand (DTDL) voor het temperatuurregelingsapparaat](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) definieert de telemetrie, eigenschappen en opdrachten die het apparaat implementeert.

De apparaatcode maakt verbinding met uw IoT-hub met behulp van de standaardmethode `CreateFromConnectionString`. Het apparaat verzendt de model-id van het DTDL-model dat het in de verbindingsaanvraag implementeert. Een apparaat dat een model-id verzendt, is een IoT Plug and Play-apparaat:

```csharp
private static void InitializeDeviceClientAsync()
{
  var options = new ClientOptions
  {
      ModelId = ModelId,
  };
  s_deviceClient = DeviceClient.CreateFromConnectionString(s_deviceConnectionString, TransportType.Mqtt, options);
  s_deviceClient.SetConnectionStatusChangesHandler((status, reason) =>
  {
      s_logger.LogDebug($"Connection status change registered - status={status}, reason={reason}.");
  });
}
```

De model-id wordt in de code opgeslagen, zoals weergegeven in het volgende fragment:

```csharp
private const string ModelId = "dtmi:com:example:Thermostat;1";
```

Nadat het apparaat verbinding heeft gemaakt met uw IoT-hub, registreert de code de opdrachthandlers. De opdracht `reboot` wordt gedefinieerd in de hoofdinterface. De opdracht `getMaxMinReport` wordt gedefinieerd in elk van de twee thermostaatonderdelen:

```csharp
await s_deviceClient.SetMethodHandlerAsync("reboot", HandleRebootCommandAsync, s_deviceClient);
await s_deviceClient.SetMethodHandlerAsync("thermostat1*getMaxMinReport", HandleMaxMinReportCommandAsync, Thermostat1);
await s_deviceClient.SetMethodHandlerAsync("thermostat2*getMaxMinReport", HandleMaxMinReportCommandAsync, Thermostat2);
```

Er zijn afzonderlijke handlers voor de gewenste updates voor eigenschappen in de twee thermostaatonderdelen:

```csharp
s_desiredPropertyUpdateCallbacks.Add(Thermostat1, TargetTemperatureUpdateCallbackAsync);
s_desiredPropertyUpdateCallbacks.Add(Thermostat2, TargetTemperatureUpdateCallbackAsync);
```

De voorbeeldcode verzendt telemetrie vanuit elk thermostaatonderdeel:

```csharp
await SendTemperatureAsync(Thermostat1);
await SendTemperatureAsync(Thermostat2);
```

De `SendTemperature`-methode maakt gebruik van de `PnpHhelper`-klasse om berichten voor elk onderdeel te maken:

```csharp
Message msg = PnpHelper.CreateIothubMessageUtf8(telemetryName, JsonConvert.SerializeObject(currentTemperature), componentName);
```

De `PnpHelper`-klasse bevat andere voorbeeldmethoden die u kunt gebruiken met een model met meerdere onderdelen.

Gebruik het hulpprogramma Azure IoT Explorer om de telemetrie en eigenschappen uit de twee thermostaatonderdelen weer te geven:

:::image type="content" source="media/tutorial-multiple-components-csharp/multiple-component.png" alt-text="Apparaat met meerdere onderdelen in Azure IoT Explorer":::

U kunt het hulpprogramma Azure IoT Explorer ook gebruiken om opdrachten aan te roepen in een van de twee thermostaatonderdelen of in de hoofdinterface.

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u een IoT Plug and Play-apparaat met onderdelen kunt verbinden met een IoT-hub. Voor meer informatie over IoT Plug and Play-apparaatmodellen raadpleegt u:

> [!div class="nextstepaction"]
> [Handleiding voor ontwikkelaars van IoT Plug and Play Preview-modellen](concepts-developer-guide.md)
