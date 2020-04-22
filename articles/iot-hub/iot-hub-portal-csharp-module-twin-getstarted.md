---
title: Azure IoT Hub-module-identiteit & moduletwee (portal en .NET)
description: Informatie over het maken van module-id's en het bijwerken van moduledubbels met de portal en .NET.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/20/2019
ms.custom: amqp
ms.openlocfilehash: a3258de2ed7269ab50e6feca3c421d55de5a9d91
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759778"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-using-the-portal-and-net-device"></a>Aan de slag met IoT Hub-module-id's en moduledubbels met behulp van de portal en .NET

[!INCLUDE [iot-hub-selector-module-twin-getstarted](../../includes/iot-hub-selector-module-twin-getstarted.md)]

> [!NOTE]
> [Module-id's en moduledubbels](iot-hub-devguide-module-twins.md) zijn vergelijkbaar met Azure IoT Hub-apparaat-id's en apparaatdubbels, maar bieden een hogere granulariteit. Terwijl de identiteit van azure IoT Hub-apparaten en apparaattweeling de back-endtoepassing in staat stellen om een apparaat te configureren en inzicht te bieden in de omstandigheden van het apparaat, bieden een module-identiteit en moduletwee deze mogelijkheden voor afzonderlijke onderdelen van een apparaat. Op geschikte apparaten met meerdere componenten, zoals op het besturingssysteem gebaseerde apparaten of firmware-apparaten, moduleidentiteiten en moduletweelingen, kunnen geïsoleerde configuraties en omstandigheden voor elk onderdeel worden mogelijk.
>

In deze zelfstudie leert u het volgende:

* Hoe maak je een module-identiteit in de portal.

* Een SDK van een .NET-apparaat gebruiken om de moduletwin vanaf uw apparaat bij te werken.

> [!NOTE]
> Zie [Azure IoT SDKs](iot-hub-devguide-sdks.md)voor informatie over de Azure IoT SDKs die u gebruiken om beide toepassingen te bouwen die op apparaten en uw oplossingback-end worden uitgevoerd.
>

## <a name="prerequisites"></a>Vereisten

* Visual Studio.

* Een actief Azure-account. Als je nog geen account hebt, kun je binnen een paar minuten een [gratis account](https://azure.microsoft.com/pricing/free-trial/) aanmaken.

## <a name="create-a-hub"></a>Een hub maken

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-hub"></a>Een nieuw apparaat registreren in de hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-a-module-identity-in-the-portal"></a>Een module-id in de portal maken

Binnen één apparaat-id kunt u 20 module-entiteiten maken. Voer de volgende stappen uit om een identiteit toe te voegen:

1. Kies **Module-identiteit toevoegen** om uw eerste module-identiteit toe te voegen voor het apparaat dat u in de vorige sectie hebt gemaakt.

1. Voer de naam *myFirstModule in*. Sla uw module-identiteit op.

    ![Module-identiteit toevoegen](./media/iot-hub-portal-csharp-module-twin-getstarted/add-module-identity.png)

    Uw nieuwe module-identiteit wordt onder aan het scherm weergegeven. Selecteer deze optie om de identiteitsgegevens van de module te bekijken.

    ![Zie module identiteitsgegevens](./media/iot-hub-portal-csharp-module-twin-getstarted/module-identity-details.png)

Sla de **tekenreeks Verbinden op - primaire toets**. U gebruikt het in het volgende gedeelte om uw module op het apparaat in te stellen.

## <a name="update-the-module-twin-using-net-device-sdk"></a>De moduledubbel bijwerken met de SDK voor .NET-apparaten

U hebt nu de module-id in uw IoT Hub gemaakt. Laten we met de cloud proberen te communiceren vanaf het gesimuleerde apparaat. Door de module-id te maken, wordt impliciet ook een moduledubbel in de IoT Hub gemaakt. In deze sectie maakt u een .NET-consoletoepassing op het gesimuleerde apparaat waarmee de gerapporteerde eigenschappen van de moduledubbel worden bijgewerkt.

### <a name="create-a-visual-studio-project"></a>Een Visual Studio-project maken

Voer de volgende stappen uit om een app te maken die de dubbele gerapporteerde eigenschappen van de module bijwerkt:

1. Selecteer in Visual Studio **Een nieuw project maken,** kies **console-app (.NET Framework)** en selecteer **Volgende**.

1. Voer in **Uw nieuwe project bijwerken** *updateModuleTwinReportedProperties* in als **projectnaam**. Selecteer **Maken** om door te gaan.

    ![Configureren dat u een visual studio-project bent](./media/iot-hub-portal-csharp-module-twin-getstarted/configure-twins-project.png)

### <a name="install-the-latest-azure-iot-hub-net-device-sdk"></a>Installeer de nieuwste Azure IoT Hub .NET-apparaat SDK

Module identiteit en module twin is in openbare preview. Het is alleen beschikbaar in de IoT Hub pre-release apparaat SDKs. Voer de volgende stappen uit om het te installeren:

1. Open in Visual Studio **Tools** > **NuGet Package Manager** > **Beheer NuGet-pakketten voor oplossing.**

1. Selecteer **Bladeren**en selecteer **Prerelease opnemen**. Zoeken naar *Microsoft.Azure.Devices.Client*. Selecteer de nieuwste versie en installeer.

    ![Azure IoT Hub .NET-service SDK-voorbeeld installeren](./media/iot-hub-csharp-csharp-module-twin-getstarted/install-sdk.png)

    U hebt nu toegang tot alle modulefuncties.

### <a name="get-your-module-connection-string"></a>De verbindingstekenreeks van de module oppakken

U hebt de tekenreeks voor de moduleverbinding nodig voor uw console-app. Volg deze stappen:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).

1. Navigeer naar uw IoT-hub en selecteer **IoT-apparaten.** Open **myFirstDevice** en je ziet dat **myFirstModule** is gemaakt.

1. Selecteer **myFirstModule** onder **Moduleidentiteiten**. Kopieer in **moduleidentiteitsgegevens**de **verbindingstekenreeks (primaire toets).**

    ![Details van de Azure Portal-module](./media/iot-hub-portal-csharp-module-twin-getstarted/module-identity-details.png)

### <a name="create-updatemoduletwinreportedproperties-console-app"></a>UpdateModuleTwinReportedProperties-console-app maken

Voer de volgende stappen uit om uw app te maken:

1. Voeg aan het begin van het bestand **Program.cs** de volgende `using` instructies toe:

  ```csharp
  using Microsoft.Azure.Devices.Client;
  using Microsoft.Azure.Devices.Shared;
  using Newtonsoft.Json;
  ```

2. Voeg de volgende velden toe aan de klasse **Program**: Vervang de waarde van de tijdelijke aanduiding door de moduleverbindingsreeks.

  ```csharp
  private const string ModuleConnectionString = "<Your module connection string>";
  private static ModuleClient Client = null;
  ```

3. Voeg de methode **OnDesiredPropertyChanged** toe aan de klasse **Program**:

  ```csharp
  private static async Task OnDesiredPropertyChanged(TwinCollection desiredProperties, object userContext)
      {
          Console.WriteLine("desired property change:");
          Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));
          Console.WriteLine("Sending current time as reported property");
          TwinCollection reportedProperties = new TwinCollection
          {
              ["DateTimeLastDesiredPropertyChangeReceived"] = DateTime.Now
          };
  
          await Client.UpdateReportedPropertiesAsync(reportedProperties).ConfigureAwait(false);
      }
  ```

4. Vervang ten slotte de **hoofdmethode** door de volgende code:

  ```csharp
  static void Main(string[] args)
  {
      Microsoft.Azure.Devices.Client.TransportType transport = Microsoft.Azure.Devices.Client.TransportType.Amqp;
  
      try
      {
          Client = ModuleClient.CreateFromConnectionString(ModuleConnectionString, transport);
          Client.SetConnectionStatusChangesHandler(ConnectionStatusChangeHandler);
          Client.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertyChanged, null).Wait();
  
          Console.WriteLine("Retrieving twin");
          var twinTask = Client.GetTwinAsync();
          twinTask.Wait();
          var twin = twinTask.Result;
          Console.WriteLine(JsonConvert.SerializeObject(twin));
  
          Console.WriteLine("Sending app start time as reported property");
          TwinCollection reportedProperties = new TwinCollection();
          reportedProperties["DateTimeLastAppLaunch"] = DateTime.Now;
  
          Client.UpdateReportedPropertiesAsync(reportedProperties);
      }
      catch (AggregateException ex)
      {
          Console.WriteLine("Error in sample: {0}", ex);
      }
  
      Console.WriteLine("Waiting for Events.  Press enter to exit...");
      Console.ReadKey();
      Client.CloseAsync().Wait();
  }
  
  private static void ConnectionStatusChangeHandler(ConnectionStatus status, ConnectionStatusChangeReason reason)
  {
      Console.WriteLine($"Status {status} changed: {reason}");
  }
  ```
  
  U deze app bouwen en uitvoeren met **F5.**

In dit codevoorbeeld ziet u hoe u de gerapporteerde eigenschappen van de moduledubbel kunt ophalen en bijwerken met het AMQP-protocol. In de openbare preview-versie wordt alleen AMQP ondersteund voor moduledubbelbewerkingen.

## <a name="next-steps"></a>Volgende stappen

Als u aan de slag wilt gaan met IoT Hub en andere IoT-scenario's wilt verkennen, leest u deze artikelen:

* [Aan de slag met IoT Hub module identity and module twin met .NET backup and .NET device](iot-hub-csharp-csharp-module-twin-getstarted.md)

* [Aan de slag met IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
