---
title: Bestanden uploaden van apparaten naar Azure IoT Hub met .NET | Microsoft Documenten
description: Bestanden uploaden van een apparaat naar de cloud met Azure IoT-apparaat SDK voor .NET. Geüploade bestanden worden opgeslagen in een Azure-opslagblobcontainer.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 07/04/2017
ms.author: robinsh
ms.openlocfilehash: b379f158672a9df3056acb09c63c392869a53283
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77108708"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-net"></a>Bestanden uploaden van uw apparaat naar de cloud met IoT Hub (.NET)

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

Deze zelfstudie bouwt voort op de code in de [zelfstudie Van cloud naar apparaat verzenden met IoT Hub](iot-hub-csharp-csharp-c2d.md) om u te laten zien hoe u de mogelijkheden voor het uploaden van bestanden van IoT Hub gebruiken. Het laat je zien hoe je:

* Zorg veilig voor een apparaat met een Azure blob URI voor het uploaden van een bestand.

* Gebruik de meldingen voor het uploaden van IoT Hub-bestanden om de verwerking van het bestand in de back-end van uw app te activeren.

De [snelstart-berichten van cloud](quickstart-send-telemetry-dotnet.md) naar apparaat verzenden met [iot-hub](iot-hub-csharp-csharp-c2d.md) toont de functionaliteit van iot-naar-cloud en cloud-naar-apparaat-berichten van IoT Hub. De [zelfstudie Berichtroutering configureren met IoT Hub](tutorial-routing.md) beschrijft een manier om device-to-cloud-berichten betrouwbaar op te slaan in microsoft Azure Blob-opslag. In sommige scenario's u de gegevens die uw apparaten verzenden echter niet eenvoudig in kaart brengen in de relatief kleine device-to-cloudberichten die IoT Hub accepteert. Bijvoorbeeld:

* Grote bestanden die afbeeldingen bevatten

* Video's

* Trillingsgegevens bemonsterd op hoge frequentie

* Een vorm van vooraf verwerkte gegevens

Deze bestanden worden meestal batch verwerkt in de cloud met behulp van hulpprogramma's zoals [Azure Data Factory](../data-factory/introduction.md) of de [Hadoop](../hdinsight/index.yml) stack. Wanneer u bestanden vanaf een apparaat moet uploaden, u nog steeds de beveiliging en betrouwbaarheid van IoT Hub gebruiken.

Aan het einde van deze zelfstudie voer je twee .NET-console-apps uit:

* **Gesimuleerd apparaat**. Deze app uploadt een bestand naar opslag met behulp van een SAS URI van uw IoT-hub. Het is een aangepaste versie van de app die is gemaakt in de [berichten van Cloud-to-Device verzenden met de zelfstudie van IoT Hub.](iot-hub-csharp-csharp-c2d.md)

* **ReadFileUploadNotification**. Deze app ontvangt meldingen voor het uploaden van bestanden van uw IoT-hub.

> [!NOTE]
> IoT Hub ondersteunt veel apparaatplatforms en -talen, waaronder C, Java, Python en Javascript, via Azure IoT-apparaat SDKs. Raadpleeg het [Azure IoT Developer Center](https://azure.microsoft.com/develop/iot) voor stapsgewijze instructies over het verbinden van uw apparaat met Azure IoT Hub.

## <a name="prerequisites"></a>Vereisten

* Visual Studio

* Een actief Azure-account. Als je nog geen account hebt, kun je binnen een paar minuten een [gratis account](https://azure.microsoft.com/pricing/free-trial/) aanmaken.

* Zorg ervoor dat poort 8883 is geopend in uw firewall. Het apparaatvoorbeeld in dit artikel maakt gebruik van het MQTT-protocol, dat communiceert via poort 8883. Deze poort kan worden geblokkeerd in sommige bedrijfs- en educatieve netwerkomgevingen. Zie [Verbinding maken met IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)voor meer informatie en manieren om dit probleem te omzeilen.

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Een bestand uploaden vanuit een apparaat-app

In deze sectie wijzigt u de apparaat-app die u hebt gemaakt in [Berichten van cloud naar apparaat verzenden met IoT Hub](iot-hub-csharp-csharp-c2d.md) om berichten van cloud naar apparaat te ontvangen van de IoT-hub.

1. Klik in Visual Studio Solution Explorer met de rechtermuisknop op het project **Gesimuleerd apparaat** en selecteer**Bestaand item** **toevoegen** > . Zoek een afbeeldingsbestand en neem het op in uw project. In deze zelfstudie wordt `image.jpg`ervan uitgegaan dat de afbeelding de naam is .

1. Klik met de rechtermuisknop op de afbeelding en selecteer **Eigenschappen**. Controleer of **Kopiëren naar uitvoermap** is ingesteld **op Altijd kopiëren**.

    ![Weergeven waar de eigenschap afbeelding voor Kopiëren naar uitvoermap moet worden bijgewerkt](./media/iot-hub-csharp-csharp-file-upload/image-properties.png)

1. Voeg in het **Program.cs-bestand** de volgende instructies boven aan het bestand toe:

    ```csharp
    using System.IO;
    ```

1. Voeg de volgende methode toe aan de klasse **Program**:

    ```csharp
    private static async void SendToBlobAsync()
    {
        string fileName = "image.jpg";
        Console.WriteLine("Uploading file: {0}", fileName);
        var watch = System.Diagnostics.Stopwatch.StartNew();

        using (var sourceData = new FileStream(@"image.jpg", FileMode.Open))
        {
            await deviceClient.UploadToBlobAsync(fileName, sourceData);
        }

        watch.Stop();
        Console.WriteLine("Time to upload file: {0}ms\n", watch.ElapsedMilliseconds);
    }
    ```

    De `UploadToBlobAsync` methode neemt de bestandsnaam en streambron van het bestand in beslag dat moet worden geüpload en verwerkt de upload naar opslag. De console-app geeft de tijd weer die nodig is om het bestand te uploaden.

1. Voeg de volgende regel toe aan `Console.ReadLine()`de **hoofdmethode,** vlak voor :

    ```csharp
    SendToBlobAsync();
    ```

> [!NOTE]
> Omwille van de eenvoud, deze tutorial niet de uitvoering van een retry beleid. In productiecode moet u opnieuw proberenbeleid implementeren, zoals exponentiële back-off, zoals voorgesteld in [Tijdelijke foutafhandeling](/azure/architecture/best-practices/transient-faults).

## <a name="get-the-iot-hub-connection-string"></a>De verbindingstekenreeks voor IoT-hub

In dit artikel maakt u een back-endservice om meldingen van bestandsuploadmeldingen te ontvangen van de IoT-hub die u hebt gemaakt in [Telemetrie verzenden van een apparaat naar een IoT-hub.](quickstart-send-telemetry-dotnet.md) Als u meldingen voor het uploaden van bestanden wilt ontvangen, heeft uw service de machtiging **voor verbinding met** de service nodig. Standaard wordt elke IoT-hub gemaakt met een service met gedeelde toegangsbeleid met de naam **service** die deze toestemming verleent.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="receive-a-file-upload-notification"></a>Een melding voor het uploaden van bestanden ontvangen

In deze sectie schrijft u een .NET-console-app die meldingen van bestandsuploadmeldingen van IoT Hub ontvangt.

1. Selecteer in de huidige Visual Studio-oplossing **Bestand** > **Nieuw** > **project**. Selecteer **console-app (.NET Framework)** in **Een nieuw project maken**en selecteer **Volgende**.

1. Geef het project *ReadFileUploadNotification een*naam . Selecteer **Onder Oplossing**de optie Toevoegen aan **oplossing**. Selecteer **Maken** om het project te maken.

    ![Het project ReadFileUploadNotification configureren in Visual Studio](./media/iot-hub-csharp-csharp-file-upload/read-file-upload-project-configure.png)

1. Klik in Solution Explorer met de rechtermuisknop op het project **ReadFileUploadNotification** en selecteer **NuGet-pakketten beheren**.

1. Selecteer **Bladeren**in **NuGet Package Manager**. Zoek naar en selecteer **Microsoft.Azure.Devices**en selecteer **Vervolgens Installeren**.

    Met deze stap wordt een verwijzing naar het [Sdk NuGet-pakket van Azure IoT-service](https://www.nuget.org/packages/Microsoft.Azure.Devices/) in het **ReadFileUploadNotification-project** gedownload, geïnstalleerd en toegevoegd.

1. Voeg in het **Program.cs** bestand voor dit project de volgende instructie boven aan het bestand toe:

    ```csharp
    using Microsoft.Azure.Devices;
    ```

1. Voeg de volgende velden toe aan de klasse **Program**: Vervang `{iot hub connection string}` de tijdelijke aanduidingswaarde door de IoT-hubverbindingstekenreeks die u eerder hebt gekopieerd in [De verbindingstekenreeks van de IoT-hub:](#get-the-iot-hub-connection-string)

    ```csharp
    static ServiceClient serviceClient;
    static string connectionString = "{iot hub connection string}";
    ```

1. Voeg de volgende methode toe aan de klasse **Program**:

    ```csharp
    private async static void ReceiveFileUploadNotificationAsync()
    {
        var notificationReceiver = serviceClient.GetFileNotificationReceiver();

        Console.WriteLine("\nReceiving file upload notification from service");
        while (true)
        {
            var fileUploadNotification = await notificationReceiver.ReceiveAsync();
            if (fileUploadNotification == null) continue;

            Console.ForegroundColor = ConsoleColor.Yellow;
            Console.WriteLine("Received file upload notification: {0}", 
              string.Join(", ", fileUploadNotification.BlobName));
            Console.ResetColor();

            await notificationReceiver.CompleteAsync(fileUploadNotification);
        }
    }
    ```

    Houd er rekening mee dat dit ontvangstpatroon hetzelfde patroon is dat wordt gebruikt om cloud-to-device-berichten van de apparaat-app te ontvangen.

1. Voeg tot slot de volgende regels toe aan de methode **Main**:

    ```csharp
    Console.WriteLine("Receive file upload notifications\n");
    serviceClient = ServiceClient.CreateFromConnectionString(connectionString);
    ReceiveFileUploadNotificationAsync();
    Console.WriteLine("Press Enter to exit\n");
    Console.ReadLine();
    ```

## <a name="run-the-applications"></a>De toepassingen uitvoeren

U kunt nu de toepassingen gaan uitvoeren.

1. Klik in Solutions Explorer met de rechtermuisknop op uw oplossing en selecteer **StartUp-projecten instellen**.

1. Selecteer **in het opstartproject voor algemene eigenschappen** > **Startup Project** **meerdere opstartprojecten**en selecteer vervolgens de actie **Start** voor **ReadFileUploadNotification** en **SimulatedDevice**. Selecteer **OK** om uw wijzigingen op te slaan.

1. Druk op **F5**. Beide toepassingen moeten starten. U ziet de upload voltooid in een console-app en het bericht voor uploadmeldingen dat door de andere console-app is ontvangen. U de [Azure-portal](https://portal.azure.com/) of Visual Studio Server Explorer gebruiken om te controleren op de aanwezigheid van het geüploade bestand in uw Azure Storage-account.

    ![Schermafbeelding van het uitvoerscherm](./media/iot-hub-csharp-csharp-file-upload/run-apps1.png)

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u de mogelijkheden voor het uploaden van bestanden van IoT Hub gebruiken om het uploaden van bestanden vanaf apparaten te vereenvoudigen. U de functies en scenario's van iot-hub blijven verkennen met de volgende artikelen:

* [Een IoT-hub programmatisch maken](iot-hub-rm-template-powershell.md)

* [Inleiding tot C SDK](iot-hub-device-sdk-c-intro.md)

* [SDK’s voor Azure IoT](iot-hub-devguide-sdks.md)

Zie:

* [AI implementeren op Edge-apparaten met Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
