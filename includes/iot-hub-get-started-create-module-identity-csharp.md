---
title: bestand opnemen
description: bestand opnemen
services: iot-hub
author: chrissie926
ms.service: iot-hub
ms.topic: include
ms.date: 08/07/2019
ms.author: menchi
ms.custom: include file
ms.openlocfilehash: a5c1ddd085ae65b9920d73f50f993f4646785a69
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68883752"
---
## <a name="create-a-module-identity"></a>Een module-id maken

In deze sectie maakt u een .NET-console-app die een apparaatidentiteit en een module-identiteit maakt in het identiteitsregister in uw hub. Een apparaat of module kan geen verbinding maken met de hub, tenzij het een vermelding in het identiteitsregister heeft. Zie het gedeelte [Id-register in de ontwikkelaarshandleiding voor IoT Hub](../articles/iot-hub/iot-hub-devguide-identity-registry.md) voor meer informatie.

Als u deze consoletoepassing uitvoert, worden er een unieke id en een unieke sleutel gemaakt voor zowel het apparaat als de module. Uw apparaat en module gebruiken deze waarden om zich te identificeren wanneer het apparaat-naar-cloudberichten naar IoT Hub verzendt. De id's zijn hoofdlettergevoelig.

1. Open Visual Studio en selecteer **Een nieuw project maken**.

1. Selecteer **console-app (.NET Framework)** in **Een nieuw project maken**.

1. Selecteer **Volgende** om **Uw nieuwe project configureren te openen**. Geef het project de naam *CreateIdentities* en geef de oplossing de naam *IoTHubGetStarted*. Zorg ervoor dat de versie van .NET Framework minimaal 4.6.1 is.

    ![Naam en kader invoeren voor uw Visual Studio-oplossing](./media/iot-hub-get-started-create-module-identity-csharp/configure-createidentities-project.png)

1. Open in Visual Studio **Tools** > **NuGet Package Manager** > **Beheer NuGet-pakketten voor oplossing.** Selecteer het tabblad **Browse**.

1. Zoeken naar **Microsoft.Azure.Devices**. Selecteer deze en selecteer **Installeren**.

    ![Huidige versie Azure IoT Hub .NET-service SDK installeren](./media/iot-hub-get-started-create-module-identity-csharp/install-service-sdk.png)

1. Voeg aan het begin van het bestand **Program.cs** de volgende `using` instructies toe:

   ```csharp
   using Microsoft.Azure.Devices;
   using Microsoft.Azure.Devices.Common.Exceptions;
   ```

1. Voeg de volgende velden toe aan de klasse **Program**: Vervang de tijdelijke aanduidingswaarde met de IoT Hub-verbindingsreeks voor de hub die u hebt gemaakt in de vorige sectie.

   ```csharp
   const string connectionString = "<replace_with_iothub_connection_string>";
   const string deviceID = "myFirstDevice";
   const string moduleID = "myFirstModule";
   ```

1. Voeg de volgende code toe aan de klasse **Hoofd.**

   ```csharp
   static void Main(string[] args)
   {
       AddDeviceAsync().Wait();
       AddModuleAsync().Wait();
   }
   ```

1. Voeg de volgende methoden toe aan de klasse **Program**:

    ```csharp
    private static async Task AddDeviceAsync()
    {
       RegistryManager registryManager = 
         RegistryManager.CreateFromConnectionString(connectionString);
       Device device;

       try
       {
           device = await registryManager.AddDeviceAsync(new Device(deviceID));
       }
       catch (DeviceAlreadyExistsException)
        {
            device = await registryManager.GetDeviceAsync(deviceID);
        }

        Console.WriteLine("Generated device key: {0}", 
          device.Authentication.SymmetricKey.PrimaryKey);
    }

    private static async Task AddModuleAsync()
    {
        RegistryManager registryManager = 
          RegistryManager.CreateFromConnectionString(connectionString);
        Module module;

        try
        {
            module = 
              await registryManager.AddModuleAsync(new Module(deviceID, moduleID));
        }
        catch (ModuleAlreadyExistsException)
        {
            module = await registryManager.GetModuleAsync(deviceID, moduleID);
        }

        Console.WriteLine("Generated module key: {0}", module.Authentication.SymmetricKey.PrimaryKey);
    }
    ```

    De `AddDeviceAsync` methode maakt een apparaatidentiteit met ID **myFirstDevice**. Als die apparaat-id al bestaat in het identiteitsregister, wordt de bestaande apparaatgegevens door de code gewoon opgehaald. De app geeft vervolgens de primaire sleutel voor die identiteit weer. U gebruikt deze sleutel in de gesimuleerde apparaat-app om verbinding te maken met uw hub.

    De `AddModuleAsync` methode maakt een module-identiteit met ID **myFirstModule** onder apparaat **myFirstDevice**. Als die module-id al bestaat in het identiteitsregister, wordt de bestaande module-informatie door de code gewoon opgehaald. De app geeft vervolgens de primaire sleutel voor die identiteit weer. U gebruikt deze sleutel in de gesimuleerde module-app om verbinding te maken met uw hub.

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

1. Voer deze app uit en noteer de apparaatsleutel en modulesleutel.

> [!NOTE]
> Het identiteitsregister van de IoT Hub slaat alleen apparaat- en moduleidentiteiten op om veilige toegang tot de hub mogelijk te maken. In het identiteitsregister worden apparaat-id's en -sleutels opgeslagen die als beveiligingsreferenties worden gebruikt. In het identiteitsregister wordt ook een vlag ingeschakeld/uitgeschakeld voor elk apparaat opgeslagen die u kunt gebruiken om de toegang tot dat apparaat uit te schakelen. Als uw app andere apparaatspecifieke metagegevens moet opslaan, moet deze een toepassingsspecifieke winkel gebruiken. Er is geen vlag voor ingeschakeld/uitgeschakeld voor module-id's. Zie [IoT Hub-ontwikkelaarshandleiding](../articles/iot-hub/iot-hub-devguide-identity-registry.md)voor meer informatie.
