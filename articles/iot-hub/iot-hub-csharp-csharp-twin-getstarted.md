---
title: Aan de slag met Azure IoT Hub-apparaattweeling (.NET/.NET) | Microsoft Documenten
description: Azure IoT Hub-apparaattweelingen gebruiken om tags toe te voegen en vervolgens een IoT Hub-query te gebruiken. U gebruikt de Azure IoT-apparaat SDK voor .NET om de gesimuleerde apparaat-app en de Azure IoT-service SDK voor .NET te implementeren om een service-app te implementeren die de tags toevoegt en de IoT Hub-query uitvoert.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/26/2019
ms.author: robinsh
ms.custom: mqtt
ms.openlocfilehash: 620e0213733d278a28ec1bcad4b031f5764ccda9
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733135"
---
# <a name="get-started-with-device-twins-net"></a>Aan de slag met apparaattweelingen (.NET)

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

In deze zelfstudie maakt u de volgende .NET-console-apps:

* **CreateDeviceIdentity**. Deze app maakt een apparaatidentiteit en bijbehorende beveiligingssleutel om uw gesimuleerde apparaat-app met elkaar te verbinden.

* **AddTagsandQuery**. Deze back-end app voegt tags en query's apparaat tweelingen.

* **ReportConnectivity**. Deze apparaat-app simuleert een apparaat dat verbinding maakt met uw IoT-hub met de apparaatidentiteit die eerder is gemaakt en rapporteert de verbindingsconditie.

> [!NOTE]
> Het artikel [Azure IoT SDKs](iot-hub-devguide-sdks.md) bevat informatie over de Azure IoT SDK's die u gebruiken om zowel apparaat- als back-end-apps te bouwen.
>

## <a name="prerequisites"></a>Vereisten

* Visual Studio.

* Een actief Azure-account. Als je nog geen account hebt, kun je binnen een paar minuten een [gratis account](https://azure.microsoft.com/pricing/free-trial/) aanmaken.

* Zorg ervoor dat poort 8883 is geopend in uw firewall. Het apparaatvoorbeeld in dit artikel maakt gebruik van het MQTT-protocol, dat communiceert via poort 8883. Deze poort kan worden geblokkeerd in sommige bedrijfs- en educatieve netwerkomgevingen. Zie [Verbinding maken met IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)voor meer informatie en manieren om dit probleem te omzeilen.

## <a name="create-an-iot-hub"></a>Een IoT Hub maken

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Een nieuw apparaat registreren in de IoT-hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="get-the-iot-hub-connection-string"></a>De verbindingstekenreeks voor IoT-hub

[!INCLUDE [iot-hub-howto-twin-shared-access-policy-text](../../includes/iot-hub-howto-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-custom-connection-string](../../includes/iot-hub-include-find-custom-connection-string.md)]

## <a name="create-the-service-app"></a>De service-app maken

In deze sectie maakt u een .NET-console-app met C#, die locatiemetagegevens toevoegt aan de apparaattweeling die is gekoppeld aan **myDeviceId.** Vervolgens wordt het apparaat twins opgevraagd die zijn opgeslagen in de IoT-hub, waarbij de apparaten in de VS worden geselecteerd en de apparaten die een mobiele verbinding hebben gemeld.

1. Selecteer in Visual Studio De optie **Een nieuw project maken**. Selecteer in **Nieuw project maken**de optie **Console-app (.NET Framework)** en selecteer **Volgende**.

1. Geef in **Uw nieuwe project configureren**de naam van het project **AddTagsAndQuery**.

    ![Uw addtagsandquery-project configureren](./media/iot-hub-csharp-csharp-twin-getstarted/config-addtagsandquery-app.png)

1. Klik in Solution Explorer met de rechtermuisknop op het project **AddTagsAndQuery** en selecteer **NuGet-pakketten beheren**.

1. Selecteer **Bladeren** en zoeken naar en selecteer **Microsoft.Azure.Devices**. Selecteer **Installeren**.

    ![Sluit het venster Nuget Package Manager.](./media/iot-hub-csharp-csharp-twin-getstarted/nuget-package-addtagsandquery-app.png)

   Met deze stap wordt een verwijzing naar het SDK NuGet-pakket voor [Azure IoT-services](https://www.nuget.org/packages/Microsoft.Azure.Devices/) en de afhankelijkheden ervan gedownload, geïnstalleerd en toegevoegd.

1. Voeg aan het begin van het bestand **Program.cs** de volgende `using` instructies toe:

    ```csharp  
    using Microsoft.Azure.Devices;
    ```

1. Voeg de volgende velden toe aan de klasse **Program**: Vervang `{iot hub connection string}` de Verbindingstekenreeks IoT Hub die u hebt gekopieerd in [De verbindingstekenreeks van de IoT-hub .](#get-the-iot-hub-connection-string)

    ```csharp  
    static RegistryManager registryManager;
    static string connectionString = "{iot hub connection string}";
    ```

1. Voeg de volgende methode toe aan de klasse **Program**:

    ```csharp  
    public static async Task AddTagsAndQuery()
    {
        var twin = await registryManager.GetTwinAsync("myDeviceId");
        var patch =
            @"{
                tags: {
                    location: {
                        region: 'US',
                        plant: 'Redmond43'
                    }
                }
            }";
        await registryManager.UpdateTwinAsync(twin.DeviceId, patch, twin.ETag);

        var query = registryManager.CreateQuery(
          "SELECT * FROM devices WHERE tags.location.plant = 'Redmond43'", 100);
        var twinsInRedmond43 = await query.GetNextAsTwinAsync();
        Console.WriteLine("Devices in Redmond43: {0}", 
          string.Join(", ", twinsInRedmond43.Select(t => t.DeviceId)));

        query = registryManager.CreateQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43' AND properties.reported.connectivity.type = 'cellular'", 100);
        var twinsInRedmond43UsingCellular = await query.GetNextAsTwinAsync();
        Console.WriteLine("Devices in Redmond43 using cellular network: {0}", 
          string.Join(", ", twinsInRedmond43UsingCellular.Select(t => t.DeviceId)));
    }
    ```

    De klasse **RegistryManager** onthult alle methoden die nodig zijn om te communiceren met apparaattweelingen van de service. De vorige code initialiseert eerst het **object registryManager,** haalt vervolgens de apparaattweeling voor **myDeviceId**op en werkt de tags uiteindelijk bij met de gewenste locatie-informatie.

    Na het bijwerken voert het twee query's uit: de eerste selecteert alleen de apparaattweeling van apparaten in de **Redmond43-fabriek** en de tweede verfijnt de query om alleen de apparaten te selecteren die ook via het mobiele netwerk zijn verbonden.

    De vorige code geeft bij het maken van het **queryobject** een maximum aantal geretourneerde documenten op. Het **queryobject** bevat een Booleaanse eigenschap **Van HasMoreResults** die u gebruiken om de **GetNextAsTwinAsync-methoden** meerdere keren aan te roepen om alle resultaten op te halen. Een methode genaamd **GetNextAsJson** is beschikbaar voor resultaten die geen apparaattweeling zijn, bijvoorbeeld resultaten van aggregatiequery's.

1. Voeg tot slot de volgende regels toe aan de methode **Main**:

    ```csharp  
    registryManager = RegistryManager.CreateFromConnectionString(connectionString);
    AddTagsAndQuery().Wait();
    Console.WriteLine("Press Enter to exit.");
    Console.ReadLine();
    ```

1. Voer deze toepassing uit door met de rechtermuisknop op het **addtagsandquery-project** te klikken en **Foutopsporing te**selecteren, gevolgd door **Nieuwe instantie starten**. U ziet één apparaat in de resultaten voor de query waarin wordt gevraagd naar alle apparaten in **Redmond43** en geen voor de query die de resultaten beperkt tot apparaten die een mobiel netwerk gebruiken.

    ![Queryresultaten in venster](./media/iot-hub-csharp-csharp-twin-getstarted/addtagapp.png)

In de volgende sectie maakt u een apparaat-app die de verbindingsinformatie rapporteert en het resultaat van de query in de vorige sectie wijzigt.

## <a name="create-the-device-app"></a>De apparaat-app maken

In deze sectie maakt u een .NET-console-app die verbinding maakt met uw hub als **myDeviceId**en vervolgens de gerapporteerde eigenschappen ervan bijwerkt om de informatie te bevatten die is verbonden met behulp van een mobiel netwerk.

1. Selecteer **in** > Visual Studio Bestand**Nieuw** > **project**. Kies in **Nieuw project maken**de optie **Console-app (.NET Framework)** en selecteer **Volgende**.

1. Geef in **Uw nieuwe project configureren**de naam van het project **ReportConnectivity**. Kies Bij **Oplossing** **toevoegen aan oplossing**en selecteer Vervolgens **Maken**.

1. Klik in Solution Explorer met de rechtermuisknop op het **project ReportConnectivity** en selecteer **NuGet-pakketten beheren.**

1. Selecteer **Bladeren** en zoeken naar en kies **Microsoft.Azure.Devices.Client**. Selecteer **Installeren**.

   Met deze stap wordt een verwijzing naar het SDK NuGet-pakket voor [Azure IoT-apparaten](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/) en de afhankelijkheden ervan gedownload, geïnstalleerd en toegevoegd.

1. Voeg aan het begin van het bestand **Program.cs** de volgende `using` instructies toe:

    ```csharp  
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using Newtonsoft.Json;
    ```

1. Voeg de volgende velden toe aan de klasse **Program**: Vervang `{device connection string}` de tekenreeks apparaatverbinding die u hebt opgemerkt in [Een nieuw apparaat registreren in de IoT-hub.](#register-a-new-device-in-the-iot-hub)

    ```csharp  
    static string DeviceConnectionString = "HostName=<yourIotHubName>.azure-devices.net;DeviceId=<yourIotDeviceName>;SharedAccessKey=<yourIotDeviceAccessKey>";
    static DeviceClient Client = null;
    ```

1. Voeg de volgende methode toe aan de klasse **Program**:

    ```csharp
    public static async void InitClient()
    {
        try
        {
            Console.WriteLine("Connecting to hub");
            Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, 
              TransportType.Mqtt);
            Console.WriteLine("Retrieving twin");
            await Client.GetTwinAsync();
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
        }
    }
    ```

    Het **clientobject** onthult alle methoden die u nodig hebt om vanaf het apparaat met apparaattweelingen te communiceren. De bovenstaande code initialiseert het **clientobject** en haalt vervolgens de apparaattweeling voor **myDeviceId op.**

1. Voeg de volgende methode toe aan de klasse **Program**:

    ```csharp  
    public static async void ReportConnectivity()
    {
        try
        {
            Console.WriteLine("Sending connectivity data as reported property");

            TwinCollection reportedProperties, connectivity;
            reportedProperties = new TwinCollection();
            connectivity = new TwinCollection();
            connectivity["type"] = "cellular";
            reportedProperties["connectivity"] = connectivity;
            await Client.UpdateReportedPropertiesAsync(reportedProperties);
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
        }
    }
    ```

   De bovenstaande code werkt de gerapporteerde eigenschap van **myDeviceId bij** met de connectiviteitsinformatie.

1. Voeg tot slot de volgende regels toe aan de methode **Main**:

    ```csharp
    try
    {
        InitClient();
        ReportConnectivity();
    }
    catch (Exception ex)
    {
        Console.WriteLine();
        Console.WriteLine("Error in sample: {0}", ex.Message);
    }
    Console.WriteLine("Press Enter to exit.");
    Console.ReadLine();
    ```

1. Klik in Solution Explorer met de rechtermuisknop op uw oplossing en selecteer **StartUp-projecten instellen.**

1. Selecteer meerdere opstartprojecten in **common properties,** > **Startup Project**selecteer **Meerdere opstartprojecten**. Selecteer **Start** als **actie**voor **ReportConnectivity**. Selecteer **OK** om uw wijzigingen op te slaan.  

1. Voer deze app uit door met de rechtermuisknop op het **project ReportConnectivity** te klikken en **Foutopsporing te**selecteren en vervolgens **Een nieuwe instantie start**. U moet zien dat de app de dubbele informatie krijgt en vervolgens de verbinding als ***gerapporteerde eigenschap***verzendt.

    ![Apparaat-app uitvoeren om connectiviteit te melden](./media/iot-hub-csharp-csharp-twin-getstarted/rundeviceapp.png)

   Nadat het apparaat de verbindingsinformatie heeft gerapporteerd, moet het in beide query's worden weergegeven.

1. Klik met de rechtermuisknop op het project **AddTagsAndQuery** en selecteer **Foutopsporing** > **Start nieuwe instantie** om de query's opnieuw uit te voeren. Deze keer moet **myDeviceId** worden weergegeven in beide queryresultaten.

    ![Apparaatconnectiviteit gerapporteerd](./media/iot-hub-csharp-csharp-twin-getstarted/tagappsuccess.png)

## <a name="next-steps"></a>Volgende stappen

In deze handleiding hebt u een nieuwe IoT-hub geconfigureerd in Azure Portal en vervolgens een apparaat-id gemaakt in het id-register van de IoT-hub. U hebt apparaatmetagegevens toegevoegd als tags van een back-end-app en een gesimuleerde apparaat-app geschreven om informatie over apparaatconnectiviteit in de apparaattweeling te rapporteren. U hebt ook geleerd hoe u deze informatie opvragen met de SQL-achtige IoT Hub-querytaal.

U meer leren van de volgende bronnen:

* Zie [Telemetrie verzenden van een apparaat naar een IoT-hub](quickstart-send-telemetry-dotnet.md) voor meer informatie over het verzenden van telemetrie vanaf apparaten.

* Zie de [gewenste eigenschappen gebruiken om](tutorial-device-twins.md) de zelfstudie van apparaten te configureren voor meer informatie over het configureren van apparaten met de gewenste eigenschappen van apparaattweeling.

* Zie de zelfstudie [Direct methoden](quickstart-control-device-dotnet.md) gebruiken voor meer informatie over het interactief bedienen van apparaten, zoals het inschakelen van een ventilator vanuit een door de gebruiker bestuurbare app.
