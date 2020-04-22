---
title: Cloud-to-device-berichten met Azure IoT Hub (.NET) | Microsoft Documenten
description: Cloud-to-device-berichten verzenden naar een apparaat vanaf een Azure IoT-hub met behulp van de Azure IoT SDK's voor .NET. U wijzigt een apparaat-app om berichten van cloud naar apparaat te ontvangen en wijzigt een back-end-app om de berichten van cloud naar apparaat te verzenden.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: robinsh
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 41c29e55f04f9edf06ba375ad4539e5fb3f82c18
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733423"
---
# <a name="send-messages-from-the-cloud-to-your-device-with-iot-hub-net"></a>Berichten vanuit de cloud naar uw apparaat verzenden met IoT Hub (.NET)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Azure IoT Hub is een volledig beheerde service die betrouwbare en veilige bidirectionele communicatie tussen miljoenen apparaten en een back-end van een oplossing mogelijk maakt. Met [Quickstart telemetrie verzenden van een apparaat naar een IoT-hub](quickstart-send-telemetry-dotnet.md) ziet u hoe u een IoT-hub maakt, een apparaatidentiteit indient en een apparaat-app codet die apparaat-naar-cloudberichten verzendt.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Deze zelfstudie bouwt voort op [Telemetrie verzenden van een apparaat naar een IoT-hub.](quickstart-send-telemetry-dotnet.md) Het laat u zien hoe u de volgende taken uitvoert:

* Stuur vanuit uw back-end van oplossing cloud-naar-device-berichten naar één apparaat via IoT Hub.

* Ontvang berichten van cloud naar apparaat op een apparaat.

* Vraag vanaf uw oplossing een bevestiging van de levering *(feedback)* aan voor berichten die vanuit IoT Hub naar een apparaat worden verzonden.

U vindt meer informatie over cloud-to-device-berichten in [D2C en C2D Messaging met IoT Hub.](iot-hub-devguide-messaging.md)

Aan het einde van deze zelfstudie voert u twee .NET-console-apps uit.

* **Gesimuleerd apparaat**. Deze app maakt verbinding met uw IoT-hub en ontvangt berichten van cloud naar apparaat. Deze app is een aangepaste versie van de app die is gemaakt in [Telemetrie verzenden van een apparaat naar een IoT-hub.](quickstart-send-telemetry-dotnet.md)

* **SendCloudToDevice**. Deze app stuurt een cloud-naar-apparaat bericht naar de apparaat-app via IoT Hub, en ontvangt vervolgens de levering bevestiging.

> [!NOTE]
> IoT Hub heeft SDK-ondersteuning voor veel apparaatplatforms en -talen, waaronder C, Java, Python en Javascript, via [Azure IoT-apparaat SDK's.](iot-hub-devguide-sdks.md) Zie de handleiding voor [IoT Hub-ontwikkelaars](iot-hub-devguide.md)voor stapsgewijze instructies over het verbinden van uw apparaat met de code van deze zelfstudie en in het algemeen met Azure IoT Hub.
>

## <a name="prerequisites"></a>Vereisten

* Visual Studio

* Een actief Azure-account. Als je nog geen account hebt, kun je binnen een paar minuten een [gratis account](https://azure.microsoft.com/pricing/free-trial/) aanmaken.

* Zorg ervoor dat poort 8883 is geopend in uw firewall. Het apparaatvoorbeeld in dit artikel maakt gebruik van het MQTT-protocol, dat communiceert via poort 8883. Deze poort kan worden geblokkeerd in sommige bedrijfs- en educatieve netwerkomgevingen. Zie [Verbinding maken met IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)voor meer informatie en manieren om dit probleem te omzeilen.

## <a name="receive-messages-in-the-device-app"></a>Berichten ontvangen in de apparaat-app

Wijzig in deze sectie de apparaat-app die u hebt gemaakt in [Telemetrie verzenden van een apparaat naar een IoT-hub](quickstart-send-telemetry-dotnet.md) om berichten van cloud naar apparaat te ontvangen vanaf de IoT-hub.

1. Voeg in Visual Studio in het project **SimulatedDevice** de volgende methode toe aan de klasse **Programma.**

   ```csharp
    private static async void ReceiveC2dAsync()
    {
        Console.WriteLine("\nReceiving cloud to device messages from service");
        while (true)
        {
            Message receivedMessage = await s_deviceClient.ReceiveAsync();
            if (receivedMessage == null) continue;

            Console.ForegroundColor = ConsoleColor.Yellow;
            Console.WriteLine("Received message: {0}", 
            Encoding.ASCII.GetString(receivedMessage.GetBytes()));
            Console.ResetColor();

            await s_deviceClient.CompleteAsync(receivedMessage);
        }
    }
   ```

1. Voeg de volgende methode toe in `Console.ReadLine()` de **hoofdmethode,** vlak voor de regel:

   ```csharp
   ReceiveC2dAsync();
   ```

De `ReceiveAsync` methode retourneert het ontvangen bericht asynchroon op het moment dat het door het apparaat wordt ontvangen. Het *retourneert null* na een specifieerbare time-outperiode. In dit voorbeeld wordt de standaardwaarde van één minuut gebruikt. Wanneer de app een *null*ontvangt, moet deze blijven wachten op nieuwe berichten. Deze eis is `if (receivedMessage == null) continue` de reden voor de lijn.

De oproep `CompleteAsync()` om IoT Hub te laten weten dat het bericht is verwerkt. Het bericht kan veilig uit de wachtrij van het apparaat worden verwijderd. Als er iets is gebeurd waardoor de apparaat-app de verwerking van het bericht niet kon voltooien, wordt het bericht opnieuw geleverd door IoT Hub. De logica voor het verwerken van berichten in de apparaat-app moet *idempotent*zijn, zodat het ontvangen van hetzelfde bericht meerdere keren hetzelfde resultaat oplevert.

Een toepassing kan ook een bericht tijdelijk verlaten, waardoor de IoT-hub het bericht in de wachtrij behoudt voor toekomstig verbruik. Of de toepassing kan een bericht afwijzen, waardoor het bericht permanent uit de wachtrij wordt verwijderd. Zie [D2C- en C2D-berichten met IoT Hub](iot-hub-devguide-messaging.md)voor meer informatie over de levenscyclus van berichten van cloud naar apparaat.

   > [!NOTE]
   > Bij het gebruik van HTTPS in plaats van MQTT of AMQP als transport, keert de `ReceiveAsync` methode onmiddellijk terug. Het ondersteunde patroon voor cloud-to-device-berichten met HTTPS is met tussenpozen verbonden apparaten die zelden controleren op berichten (minder dan elke 25 minuten). Meer HTTPS-uitgifte ontvangt resultaten in IoT Hub-beperking van de aanvragen. Zie [D2C- en C2D-berichten met IoT Hub](iot-hub-devguide-messaging.md)voor meer informatie over de verschillen tussen MQTT-, AMQP- en HTTPS-ondersteuning en IoT Hub-beperking.
   >

## <a name="get-the-iot-hub-connection-string"></a>De verbindingstekenreeks voor IoT-hub

In dit artikel maakt u een back-endservice om cloud-naar-device-berichten te verzenden via de IoT-hub die u hebt gemaakt in [Telemetrie verzenden van een apparaat naar een IoT-hub.](quickstart-send-telemetry-dotnet.md) Als u berichten van cloud naar apparaat wilt verzenden, heeft uw service de service-machtiging nodig om verbinding **te maken.** Standaard wordt elke IoT-hub gemaakt met een service met gedeelde toegangsbeleid met de naam **service** die deze toestemming verleent.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>Een cloud-naar-apparaatbericht verzenden

Nu schrijft u een .NET-console-app die cloud-to-device-berichten naar de apparaat-app verzendt.

1. Selecteer in de huidige Visual Studio-oplossing **Bestand** > **Nieuw** > **project**. Selecteer **console-app (.NET Framework)** in **Een nieuw project maken**en selecteer **Volgende**.

1. Geef het project *SendCloudToDevice*een naam . Selecteer **onder Oplossing**de optie Toevoegen aan **oplossing** en accepteer de meest recente versie van het .NET Framework. Selecteer **Maken** om het project te maken.

   ![Een nieuw project configureren in Visual Studio](./media/iot-hub-csharp-csharp-c2d/sendcloudtodevice-project-configure.png)

1. Klik in Solution Explorer met de rechtermuisknop op de nieuwe oplossing en selecteer **NuGet-pakketten beheren.**

1. Selecteer **in NuGet-pakketten beheren**de optie **Bladeren**en zoek naar en selecteer **Microsoft.Azure.Devices**. Selecteer **Installeren**.

   Met deze stap wordt een verwijzing naar het [SDK NuGet-pakket voor Azure IoT-service gedownload,](https://www.nuget.org/packages/Microsoft.Azure.Devices/)geïnstalleerd en toegevoegd.

1. Voeg de `using` volgende instructie toe boven aan het **Program.cs** bestand.

   ``` csharp
   using Microsoft.Azure.Devices;
   ```

1. Voeg de volgende velden toe aan de klasse **Program**: Vervang de waarde van de tijdelijke aanduiding door de IoT-hubverbindingstekenreeks die u eerder hebt gekopieerd in [De verbindingstekenreeks van de IoT-hub .](#get-the-iot-hub-connection-string)

   ``` csharp
   static ServiceClient serviceClient;
   static string connectionString = "{iot hub connection string}";
   ```

1. Voeg de volgende methode toe aan de klasse **Programma.** Stel de apparaatnaam in op wat u hebt gebruikt bij het definiëren van het apparaat in [Telemetrie verzenden van een apparaat naar een IoT-hub.](quickstart-send-telemetry-dotnet.md)

   ``` csharp
   private async static Task SendCloudToDeviceMessageAsync()
   {
        var commandMessage = new
         Message(Encoding.ASCII.GetBytes("Cloud to device message."));
        await serviceClient.SendAsync("myFirstDevice", commandMessage);
   }
   ```

   Met deze methode wordt een nieuw cloud-to-device-bericht verzonden naar het apparaat met de ID. `myFirstDevice` Wijzig deze parameter alleen als u deze hebt gewijzigd van de parameter die wordt gebruikt in [Telemetrie verzenden van een apparaat naar een IoT-hub.](quickstart-send-telemetry-dotnet.md)

1. Voeg ten slotte de volgende regels toe aan de **hoofdmethode.**

   ``` csharp
   Console.WriteLine("Send Cloud-to-Device message\n");
   serviceClient = ServiceClient.CreateFromConnectionString(connectionString);

   Console.WriteLine("Press any key to send a C2D message.");
   Console.ReadLine();
   SendCloudToDeviceMessageAsync().Wait();
   Console.ReadLine();
   ```

1. Klik in Solutions Explorer met de rechtermuisknop op uw oplossing en selecteer **StartUp-projecten instellen**.

1. Selecteer **in het opstartproject voor algemene eigenschappen** > **Startup Project** **meerdere opstartprojecten**, selecteer vervolgens de actie **Start** voor **ReadDeviceToCloudMessages**, **SimulatedDevice**en **SendCloudToDevice**. Selecteer **OK** om uw wijzigingen op te slaan.

1. Druk op **F5**. Alle drie de aanvragen moeten beginnen. Selecteer de vensters **SendCloudToDevice** en druk op **Enter**. U ziet het bericht dat door de apparaat-app wordt ontvangen.

   ![Bericht voor app-ontvangst](./media/iot-hub-csharp-csharp-c2d/sendc2d1.png)

## <a name="receive-delivery-feedback"></a>Feedback ontvangen voor levering

Het is mogelijk om leveringsbevestigingen (of vervaldatums) aan te vragen bij IoT Hub voor elk cloud-to-device-bericht. Met deze optie kan de back-end van de oplossing eenvoudig opnieuw proberen of compensatielogica worden geïnformeerd. Zie [D2C en C2D Messaging met IoT Hub](iot-hub-devguide-messaging.md)voor meer informatie over feedback van cloud naar apparaat.

In deze sectie wijzigt u de **SendCloudToDevice-app** om feedback te vragen en deze te ontvangen van de IoT-hub.

1. Voeg in Visual Studio in het **SendCloudToDevice-project** de volgende methode toe aan de klasse **Programma.**

   ```csharp
   private async static void ReceiveFeedbackAsync()
   {
        var feedbackReceiver = serviceClient.GetFeedbackReceiver();

        Console.WriteLine("\nReceiving c2d feedback from service");
        while (true)
        {
            var feedbackBatch = await feedbackReceiver.ReceiveAsync();
            if (feedbackBatch == null) continue;

            Console.ForegroundColor = ConsoleColor.Yellow;
            Console.WriteLine("Received feedback: {0}",
              string.Join(", ", feedbackBatch.Records.Select(f => f.StatusCode)));
            Console.ResetColor();

            await feedbackReceiver.CompleteAsync(feedbackBatch);
        }
    }
    ```

    Houd er rekening mee dat dit ontvangstpatroon hetzelfde patroon is dat wordt gebruikt om cloud-to-device-berichten van de apparaat-app te ontvangen.

1. Voeg direct na **Main** `serviceClient = ServiceClient.CreateFromConnectionString(connectionString)`.

   ``` csharp
   ReceiveFeedbackAsync();
   ```

1. Als u feedback wilt vragen voor de levering van uw cloud-to-device-bericht, moet u een eigenschap opgeven in de **methode SendCloudToDeviceMessageAsync.** Voeg de volgende regel `var commandMessage = new Message(...);` toe, direct na de regel.

   ``` csharp
   commandMessage.Ack = DeliveryAcknowledgement.Full;
   ```

1. Voer de apps uit door op **F5**te drukken . U zou alle drie toepassingen moeten zien beginnen. Selecteer de vensters **SendCloudToDevice** en druk op **Enter**. U ziet het bericht dat door de apparaat-app wordt ontvangen en na enkele seconden wordt het feedbackbericht ontvangen door uw **SendCloudToDevice-toepassing.**

   ![Bericht voor app-ontvangst](./media/iot-hub-csharp-csharp-c2d/sendc2d2.png)

> [!NOTE]
> Voor de eenvoud voert deze zelfstudie geen beleid voor nieuwe try's uit. In productiecode moet u opnieuw proberenbeleid implementeren, zoals exponentiële back-off, zoals voorgesteld in [Tijdelijke foutafhandeling](/azure/architecture/best-practices/transient-faults).
>

## <a name="next-steps"></a>Volgende stappen

In deze how-to heb je geleerd hoe je cloud-to-device-berichten verzenden en ontvangen.

Zie [Azure IoT Remote Monitoring solution accelerator](https://docs.microsoft.com/azure/iot-suite/)voor voorbeelden van complete end-to-end oplossingen die IoT Hub gebruiken.

Zie de [IoT Hub-ontwikkelaarshandleiding](iot-hub-devguide.md)voor meer informatie over het ontwikkelen van oplossingen met IoT Hub.
