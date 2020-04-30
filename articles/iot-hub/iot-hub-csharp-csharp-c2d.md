---
title: Cloud-naar-apparaat-berichten met Azure IoT Hub (.NET) | Microsoft Docs
description: Cloud-naar-apparaat-berichten verzenden naar een apparaat vanuit een Azure IoT-hub met behulp van de Azure IoT Sdk's voor .NET. U wijzigt een apparaat-app om Cloud-naar-apparaat-berichten te ontvangen en een back-end-app te wijzigen om de Cloud-naar-apparaat-berichten te verzenden.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81733423"
---
# <a name="send-messages-from-the-cloud-to-your-device-with-iot-hub-net"></a>Berichten vanuit de Cloud naar uw apparaat verzenden met IoT Hub (.NET)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Azure IoT Hub is een volledig beheerde service die zorgt voor betrouw bare en veilige bidirectionele communicatie tussen miljoenen apparaten en een back-end van een oplossing. Het [verzenden van telemetrie van een apparaat naar een IOT hub](quickstart-send-telemetry-dotnet.md) Quick Start laat zien hoe u een IOT-hub maakt, een apparaat-id inricht en een apparaat-app codeert die apparaat-naar-Cloud-berichten verzendt.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Deze zelf studie is gebaseerd op het [verzenden van telemetrie van een apparaat naar een IOT-hub](quickstart-send-telemetry-dotnet.md). U ziet hoe u de volgende taken kunt uitvoeren:

* Via de back-end van uw oplossing kunt u Cloud-naar-apparaat-berichten verzenden naar één apparaat via IoT Hub.

* Cloud-naar-apparaat-berichten op een apparaat ontvangen.

* Van de back-end van uw oplossing, aanvraag bezorgings bevestiging (*feedback*) aanvragen voor berichten die worden verzonden naar een apparaat vanuit IOT hub.

U kunt meer informatie vinden over Cloud-naar-apparaat-berichten in [D2C en C2D Messa ging met IOT hub](iot-hub-devguide-messaging.md).

Aan het einde van deze zelf studie voert u twee .NET-console-apps uit.

* **SimulatedDevice**. Met deze app wordt verbinding gemaakt met uw IoT-hub en worden Cloud-naar-apparaat-berichten ontvangen. Deze app is een gewijzigde versie van de app die is gemaakt in [telemetrie verzenden van een apparaat naar een IOT-hub](quickstart-send-telemetry-dotnet.md).

* **SendCloudToDevice**. Deze app verzendt een Cloud-naar-apparaat-bericht naar de apparaat-app via IoT Hub en ontvangt vervolgens de ontvangst bevestiging.

> [!NOTE]
> IoT Hub heeft SDK-ondersteuning voor veel platformen en talen, waaronder C, Java, python en Java script, via een [Azure IOT-apparaat-sdk's](iot-hub-devguide-sdks.md). Raadpleeg de [IOT hub ontwikkelaars handleiding](iot-hub-devguide.md)voor stapsgewijze instructies voor het verbinden van uw apparaat met de code van deze zelf studie en over het algemeen tot Azure IOT hub.
>

## <a name="prerequisites"></a>Vereisten

* Visual Studio

* Een actief Azure-account. Als u geen account hebt, kunt u in slechts een paar minuten een [gratis account](https://azure.microsoft.com/pricing/free-trial/) maken.

* Zorg ervoor dat poort 8883 is geopend in uw firewall. Het voor beeld van het apparaat in dit artikel maakt gebruik van het MQTT-protocol, dat communiceert via poort 8883. Deze poort kan worden geblokkeerd in sommige bedrijfs-en educatieve netwerk omgevingen. Zie [verbinding maken met IOT hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)voor meer informatie en manieren om dit probleem te omzeilen.

## <a name="receive-messages-in-the-device-app"></a>Berichten ontvangen in de apparaat-app

In deze sectie wijzigt u de apparaat-app die u hebt gemaakt in [telemetrie verzenden van een apparaat naar een IOT-hub](quickstart-send-telemetry-dotnet.md) om Cloud-naar-apparaat-berichten van de IOT-hub te ontvangen.

1. Voeg in Visual Studio in het project **SimulatedDevice** de volgende methode toe aan de klasse **Program** .

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

1. Voeg de volgende methode toe in de methode **Main** , rechts voor `Console.ReadLine()` de regel:

   ```csharp
   ReceiveC2dAsync();
   ```

De `ReceiveAsync` methode retourneert het ontvangen bericht asynchroon op het moment dat het wordt ontvangen door het apparaat. Het retourneert *Null* na een bepaalde time-outperiode. In dit voor beeld wordt de standaard waarde van één minuut gebruikt. Wanneer de app een *Null-waarde*ontvangt, moet deze blijven wachten op nieuwe berichten. Deze vereiste is de reden voor de `if (receivedMessage == null) continue` regel.

De oproep om `CompleteAsync()` IOT hub dat het bericht is verwerkt. Het bericht kan veilig uit de wachtrij van het apparaat worden verwijderd. Als er iets is gebeurd waardoor de apparaat-app de verwerking van het bericht niet kan volt ooien, wordt het opnieuw door IoT Hub bezorgd. De logica voor bericht verwerking in de apparaat-app moet *idempotent*zijn, zodat hetzelfde bericht meerdere keren wordt ontvangen.

Een toepassing kan ook tijdelijk een bericht afbreken, wat leidt tot een IoT-hub en het bericht in de wachtrij voor toekomstig gebruik behoudt. Of de toepassing kan een bericht afwijzen, waardoor het bericht definitief uit de wachtrij wordt verwijderd. Zie [D2C and C2D Messa ging with IOT hub](iot-hub-devguide-messaging.md)voor meer informatie over de levens cyclus van Cloud-naar-apparaat-berichten.

   > [!NOTE]
   > Wanneer u HTTPS gebruikt in plaats van MQTT of AMQP als Trans Port `ReceiveAsync` , retourneert de methode direct. Het ondersteunde patroon voor Cloud-naar-apparaat-berichten met HTTPS is op een regel matig verbonden apparaten die op berichten te controleren zijn (minder dan elke 25 minuten). Het uitgeven van meer HTTPS resulteert in IoT Hub het beperken van de aanvragen. Zie [D2C and C2D Messa ging with IOT hub](iot-hub-devguide-messaging.md)(Engelstalig) voor meer informatie over de verschillen tussen MQTT, AMQP en HTTPS-ondersteuning en IOT hub beperking.
   >

## <a name="get-the-iot-hub-connection-string"></a>De IoT hub-connection string ophalen

In dit artikel maakt u een back-end-service om Cloud-naar-apparaat-berichten te verzenden via de IoT-hub die u hebt gemaakt in [telemetrie van een apparaat naar een IOT-hub verzenden](quickstart-send-telemetry-dotnet.md). Als u Cloud-naar-apparaat-berichten wilt verzenden, moet u de service **Connect** -machtiging hebben. Standaard wordt elke IoT Hub gemaakt met een gedeeld toegangs beleid met de naam **service** dat deze machtiging verleent.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>Een Cloud-naar-apparaat-bericht verzenden

Nu schrijft u een .NET-console-app die Cloud-naar-apparaat-berichten naar de apparaat-app verzendt.

1. Selecteer in de huidige Visual Studio-oplossing **bestand** > **Nieuw** > **project**. Selecteer in **een nieuw project maken de**optie **console-app (.NET Framework)** en selecteer vervolgens **volgende**.

1. Geef het project de naam *SendCloudToDevice*. Selecteer onder **oplossing**de optie **toevoegen aan oplossing** en accepteer de meest recente versie van de .NET Framework. Selecteer **Maken** om het project te maken.

   ![Een nieuw project configureren in Visual Studio](./media/iot-hub-csharp-csharp-c2d/sendcloudtodevice-project-configure.png)

1. Klik in Solution Explorer met de rechter muisknop op de nieuwe oplossing en selecteer vervolgens **NuGet-pakketten beheren**.

1. Selecteer in **NuGet-pakketten beheren**de optie **Bladeren**, en zoek vervolgens naar **micro soft. Azure. devices**en selecteer deze. Selecteer **installeren**.

   Met deze stap wordt een verwijzing naar het [Azure IOT Service SDK NuGet-pakket](https://www.nuget.org/packages/Microsoft.Azure.Devices/)gedownload, geïnstalleerd en toegevoegd.

1. Voeg de volgende `using` instructie toe boven aan het **Program.cs** -bestand.

   ``` csharp
   using Microsoft.Azure.Devices;
   ```

1. Voeg de volgende velden toe aan de klasse **Program**: Vervang de waarde van de tijdelijke aanduiding door de IoT hub-connection string die u eerder hebt gekopieerd in [de IOT hub-Connection String ophalen](#get-the-iot-hub-connection-string).

   ``` csharp
   static ServiceClient serviceClient;
   static string connectionString = "{iot hub connection string}";
   ```

1. Voeg de volgende methode toe aan de klasse **Program** . Stel de apparaatnaam in op de naam die u hebt gebruikt bij het definiëren van het apparaat in [telemetrie verzenden van een apparaat naar een IOT-hub](quickstart-send-telemetry-dotnet.md).

   ``` csharp
   private async static Task SendCloudToDeviceMessageAsync()
   {
        var commandMessage = new
         Message(Encoding.ASCII.GetBytes("Cloud to device message."));
        await serviceClient.SendAsync("myFirstDevice", commandMessage);
   }
   ```

   Met deze methode wordt een nieuw Cloud-naar-apparaat-bericht naar het apparaat verzonden met `myFirstDevice`de id,. Wijzig deze para meter alleen als u deze hebt gewijzigd van de waarde die wordt gebruikt voor het [verzenden van telemetrie van een apparaat naar een IOT-hub](quickstart-send-telemetry-dotnet.md).

1. Voeg ten slotte de volgende regels toe aan de methode **Main** .

   ``` csharp
   Console.WriteLine("Send Cloud-to-Device message\n");
   serviceClient = ServiceClient.CreateFromConnectionString(connectionString);

   Console.WriteLine("Press any key to send a C2D message.");
   Console.ReadLine();
   SendCloudToDeviceMessageAsync().Wait();
   Console.ReadLine();
   ```

1. Klik in Solution Explorer met de rechter muisknop op uw oplossing en selecteer vervolgens **opstart projecten instellen**.

1. Selecteer **in algemene eigenschappen** > **opstart project** **meerdere opstart projecten**en selecteer vervolgens de actie **starten** voor **ReadDeviceToCloudMessages**, **SimulatedDevice**en **SendCloudToDevice**. Selecteer **OK** om uw wijzigingen op te slaan.

1. Druk op **F5**. Alle drie de toepassingen moeten worden gestart. Selecteer de Windows- **SendCloudToDevice** en druk op **Enter**. Het bericht dat wordt ontvangen door de apparaat-app wordt weer gegeven.

   ![App-ontvangst bericht](./media/iot-hub-csharp-csharp-c2d/sendc2d1.png)

## <a name="receive-delivery-feedback"></a>Leverings feedback ontvangen

U kunt ontvangst bevestigingen (of verlopen) aanvragen van IoT Hub voor elk bericht van de Cloud naar het apparaat. Met deze optie kan de back-end van de oplossing eenvoudig nieuwe pogingen of compensatie logica informeren. Zie [D2C and C2D Messa ging with IOT hub](iot-hub-devguide-messaging.md)voor meer informatie over Cloud-naar-apparaat-feedback.

In deze sectie wijzigt u de **SendCloudToDevice** -app voor het aanvragen van feedback en ontvangt u deze van de IOT-hub.

1. Voeg in Visual Studio in het project **SendCloudToDevice** de volgende methode toe aan de klasse **Program** .

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

    Houd er rekening mee dat dit ontvangst patroon hetzelfde is als het ontvangen van Cloud-naar-apparaat-berichten van de apparaat-app.

1. Voeg de volgende regel toe aan de methode **Main** , direct `serviceClient = ServiceClient.CreateFromConnectionString(connectionString)`na.

   ``` csharp
   ReceiveFeedbackAsync();
   ```

1. Als u feedback wilt aanvragen voor het leveren van uw Cloud-naar-apparaat-bericht, moet u een eigenschap opgeven in de methode **SendCloudToDeviceMessageAsync** . Voeg de volgende regel toe, direct na `var commandMessage = new Message(...);` de regel.

   ``` csharp
   commandMessage.Ack = DeliveryAcknowledgement.Full;
   ```

1. Voer de apps uit door op **F5**te drukken. U ziet dat alle drie de toepassingen worden gestart. Selecteer de Windows- **SendCloudToDevice** en druk op **Enter**. Het bericht dat wordt ontvangen door de app van het apparaat en na een paar seconden wordt weer gegeven, wordt het feedback bericht ontvangen door uw **SendCloudToDevice** -toepassing.

   ![App-ontvangst bericht](./media/iot-hub-csharp-csharp-c2d/sendc2d2.png)

> [!NOTE]
> Voor het gemak implementeert deze zelf studie geen beleid voor opnieuw proberen. In productie code moet u beleid voor opnieuw proberen implementeren, zoals exponentiële uitstel, zoals wordt voorgesteld in [tijdelijke fout afhandeling](/azure/architecture/best-practices/transient-faults).
>

## <a name="next-steps"></a>Volgende stappen

In deze procedure hebt u geleerd hoe u Cloud-naar-apparaat-berichten kunt verzenden en ontvangen.

Voor voor beelden van complete end-to-end-oplossingen die gebruikmaken van IoT Hub, raadpleegt u de [Azure IOT-oplossing voor externe controle](https://docs.microsoft.com/azure/iot-suite/).

Raadpleeg de [IOT hub ontwikkelaars handleiding](iot-hub-devguide.md)voor meer informatie over het ontwikkelen van oplossingen met IOT hub.
