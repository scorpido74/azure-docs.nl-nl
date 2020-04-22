---
title: Azure IoT Hub-bewerkingenbewaking (afgeschaft) | Microsoft Documenten
description: Azure IoT Hub-bewerkingenbewaking gebruiken om de status van bewerkingen op uw IoT-hub in realtime te controleren.
author: nberdy
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: nberdy
ms.custom: amqp
ms.openlocfilehash: edbc3431c860794c7cd1dd8e5011c0d7d11d692d
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732225"
---
# <a name="iot-hub-operations-monitoring-deprecated"></a>Monitoring van IoT Hub-bewerkingen (afgeschaft)

Met iot-hub-bewerkingenbewaking u de status van bewerkingen op uw IoT-hub in realtime controleren. IoT Hub houdt gebeurtenissen bij in verschillende categorieën bewerkingen. U ervoor kiezen om gebeurtenissen van een of meer categorieën naar een eindpunt van uw IoT-hub te verzenden voor verwerking. U de gegevens controleren op fouten of complexere verwerkinginstellen op basis van gegevenspatronen.

>[!NOTE]
>IoT Hub **operations monitoring is afgeschaft en is verwijderd uit IoT Hub op 10 maart 2019**. Zie [De status van Azure IoT Hub controleren en problemen snel diagnosticeren](iot-hub-monitor-resource-health.md)voor het bewaken van de bewerkingen en status van IoT Hub. Zie [Uw Azure IoT-oplossingen met Azure Monitor en Azure Resource Health controleren](https://azure.microsoft.com/blog/monitor-your-azure-iot-solutions-with-azure-monitor-and-azure-resource-health)voor meer informatie over de afschrijvingstijdlijn.

IoT Hub monitort zes categorieën gebeurtenissen:

* Apparaatidentiteitsbewerkingen
* Apparaattelemetrie
* Cloud-to-device berichten
* Verbindingen
* Bestandsuploads
* Berichtroutering

> [!IMPORTANT]
> IoT Hub operations monitoring garandeert geen betrouwbare of bestelde levering van evenementen. Afhankelijk van de onderliggende infrastructuur van iot-hub kunnen sommige gebeurtenissen verloren gaan of buiten de bestelling worden geleverd. Gebruik bedrijfsbewaking om waarschuwingen te genereren op basis van foutsignalen, zoals mislukte verbindingspogingen of hoogfrequente verbindingen voor specifieke apparaten. U moet niet vertrouwen op gebeurtenissen voor het bewaken van bewerkingen om een consistent archief voor apparaatstatus te maken, bijvoorbeeld een winkeldie is verbonden of de verbroken status van een apparaat heeft verbroken. 

## <a name="how-to-enable-operations-monitoring"></a>Hoe operationele monitoring mogelijk te maken

1. Maak een IoT-hub. Instructies voor het maken van een IoT-hub vindt u in de handleiding [Aan de slag.](quickstart-send-telemetry-dotnet.md)

2. Open het blad van uw IoT-hub. Klik van daaruit op **Operations monitoring**.

    ![Configuratie van de bewaking van toegangsbewerkingen in de portal](./media/iot-hub-operations-monitoring/enable-OM-1.png)

3. Selecteer de bewakingscategorieën die u wilt controleren en klik op **Opslaan**. De gebeurtenissen zijn beschikbaar voor het lezen via het eindpunt dat compatibel is met de gebeurtenishub in **de controle-instellingen**. Het Eindpunt van de `messages/operationsmonitoringevents`IoT-hub wordt genoemd .

    ![Operations monitoring configureren op uw IoT-hub](./media/iot-hub-operations-monitoring/enable-OM-2.png)

> [!NOTE]
> Als **u Verbose-bewaking** selecteert voor de categorie **Verbindingen,** genereert IoT Hub extra diagnostische berichten. Voor alle andere categorieën wijzigt de instelling **Verbose** de hoeveelheid informatie die IoT Hub in elk foutbericht bevat.

## <a name="event-categories-and-how-to-use-them"></a>Gebeurteniscategorieën en hoe deze te gebruiken

Elke categorie voor het monitoren van bewerkingen houdt een ander type interactie met IoT Hub bij en elke bewakingscategorie heeft een schema dat bepaalt hoe gebeurtenissen in die categorie zijn gestructureerd.

### <a name="device-identity-operations"></a>Apparaatidentiteitsbewerkingen

De categorie apparaatidentiteitsbewerkingen houdt fouten bij die optreden wanneer u een vermelding in het identiteitsregister van uw IoT-hub probeert te maken, bijwerken of verwijderen. Het bijhouden van deze categorie is handig voor het inrichten van scenario's.

```json
{
    "time": "UTC timestamp",
    "operationName": "create",
    "category": "DeviceIdentityOperations",
    "level": "Error",
    "statusCode": 4XX,
    "statusDescription": "MessageDescription",
    "deviceId": "device-ID",
    "durationMs": 1234,
    "userAgent": "userAgent",
    "sharedAccessPolicy": "accessPolicy"
}
```

### <a name="device-telemetry"></a>Apparaattelemetrie

De categorie telemetrie van het apparaat houdt fouten bij die optreden op de IoT-hub en zijn gerelateerd aan de telemetriepijplijn. Deze categorie bevat fouten die optreden bij het verzenden van telemetriegebeurtenissen (zoals beperking) en het ontvangen van telemetriegebeurtenissen (zoals onbevoegde lezer). Deze categorie kan geen fouten opvangen die worden veroorzaakt door code die op het apparaat zelf wordt uitgevoerd.

```json
{
    "messageSizeInBytes": 1234,
    "batching": 0,
    "protocol": "Amqp",
    "authType": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
    "time": "UTC timestamp",
    "operationName": "ingress",
    "category": "DeviceTelemetry",
    "level": "Error",
    "statusCode": 4XX,
    "statusType": 4XX001,
    "statusDescription": "MessageDescription",
    "deviceId": "device-ID",
    "EventProcessedUtcTime": "UTC timestamp",
    "PartitionId": 1,
    "EventEnqueuedUtcTime": "UTC timestamp"
}
```

### <a name="cloud-to-device-commands"></a>Opdrachten voor cloud-naar-apparaat

De categorie opdrachten voor cloud-to-device houdt fouten bij die zich voordoen op de IoT-hub en zijn gerelateerd aan de berichtenpijplijn van cloud-to-device. Deze categorie bevat fouten die optreden bij het verzenden van cloud-naar-device-berichten (zoals ongeautoriseerde afzender), het ontvangen van cloud-to-device-berichten (zoals het aantal meldingen van bezorging overschreden) en het ontvangen van feedback van cloud naar apparaat (zoals feedback is verlopen). Deze categorie vangt geen fouten op van een apparaat dat een cloud-to-device-bericht onjuist verwerkt als het cloud-to-device-bericht is geleverd.

```json
{
    "messageSizeInBytes": 1234,
    "authType": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
    "deliveryAcknowledgement": 0,
    "protocol": "Amqp",
    "time": " UTC timestamp",
    "operationName": "ingress",
    "category": "C2DCommands",
    "level": "Error",
    "statusCode": 4XX,
    "statusType": 4XX001,
    "statusDescription": "MessageDescription",
    "deviceId": "device-ID",
    "EventProcessedUtcTime": "UTC timestamp",
    "PartitionId": 1,
    "EventEnqueuedUtcTime": "UTC timestamp"
}
```

### <a name="connections"></a>Verbindingen

De categorie verbindingen houdt fouten bij die optreden wanneer apparaten verbinding maken of de verbinding verbreken van een IoT-hub. Het bijhouden van deze categorie is handig voor het identificeren van ongeautoriseerde verbindingspogingen en voor het bijhouden wanneer een verbinding verloren gaat voor apparaten in gebieden met slechte connectiviteit.

```json
{
    "durationMs": 1234,
    "authType": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
    "protocol": "Amqp",
    "time": " UTC timestamp",
    "operationName": "deviceConnect",
    "category": "Connections",
    "level": "Error",
    "statusCode": 4XX,
    "statusType": 4XX001,
    "statusDescription": "MessageDescription",
    "deviceId": "device-ID"
}
```

### <a name="file-uploads"></a>Bestandsuploads

De categorie voor het uploaden van bestanden houdt fouten bij die optreden op de IoT-hub en zijn gerelateerd aan de functionaliteit voor het uploaden van bestanden. Deze categorie omvat:

* Fouten die optreden met de SAS URI, zoals wanneer deze verloopt voordat een apparaat de hub van een voltooide upload op de hoogte stelt.

* Mislukte uploads die door het apparaat worden gerapporteerd.

* Fouten die optreden wanneer een bestand niet wordt gevonden in de opslag tijdens het maken van iot-hubberichten.

Deze categorie kan geen fouten opvangen die rechtstreeks optreden terwijl het apparaat een bestand uploadt naar opslag.

```json
{
    "authType": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
    "protocol": "HTTP",
    "time": " UTC timestamp",
    "operationName": "ingress",
    "category": "fileUpload",
    "level": "Error",
    "statusCode": 4XX,
    "statusType": 4XX001,
    "statusDescription": "MessageDescription",
    "deviceId": "device-ID",
    "blobUri": "http//bloburi.com",
    "durationMs": 1234
}
```

### <a name="message-routing"></a>Berichtroutering

De categorie berichtroutering houdt fouten bij die optreden tijdens de evaluatie van de berichtroute en de status van eindpunt en zoals waargenomen door IoT Hub. Deze categorie bevat gebeurtenissen zoals wanneer een regel evalueert op 'niet gedefinieerd', wanneer IoT Hub een eindpunt als dood markeert en alle andere fouten die van een eindpunt zijn ontvangen. Deze categorie bevat geen specifieke fouten over de berichten zelf (zoals apparaatbeperkingsfouten), die worden gerapporteerd onder de categorie 'apparaattelemetrie'.

```json
{
    "messageSizeInBytes": 1234,
    "time": "UTC timestamp",
    "operationName": "ingress",
    "category": "routes",
    "level": "Error",
    "deviceId": "device-ID",
    "messageId": "ID of message",
    "routeName": "myroute",
    "endpointName": "myendpoint",
    "details": "ExternalEndpointDisabled"
}
```

## <a name="connect-to-the-monitoring-endpoint"></a>Verbinding maken met het bewakingseindpunt

Het bewakingseindpunt op uw IoT-hub is een eventhubcompatibel eindpunt. U elk mechanisme dat werkt met gebeurtenishubs gebruiken om bewakingsberichten vanaf dit eindpunt te lezen. In het volgende voorbeeld wordt een basislezer gemaakt die niet geschikt is voor een implementatie met een hoge doorvoer. Zie voor meer informatie over het verwerken van Event Hubs-berichten de zelfstudie [Aan de slag met Event Hubs](../event-hubs/event-hubs-csharp-ephcs-getstarted.md).

Als u verbinding wilt maken met het bewakingseindpunt, hebt u een verbindingstekenreeks en de naam van het eindpunt nodig. In de volgende stappen ziet u hoe u de benodigde waarden in de portal vinden:

1. Navigeer in de portal naar uw IoT Hub-bronblad.

2. Kies **Operations-bewaking**en noteer de **naam van de Gebeurtenishub** en de **endpoint-waarden die compatibel zijn met gebeurtenishubs:**

    ![Endpoint-waarden die compatibel zijn met gebeurtenishub](./media/iot-hub-operations-monitoring/monitoring-endpoint.png)

3. Kies **Beleid voor gedeelde toegang**en kies **service**. Noteer de waarde **primaire sleutel:**

    ![Primaire sleutel voor gedeeld toegangsbeleid voor gedeelde service](./media/iot-hub-operations-monitoring/service-key.png)

Het volgende c#-codevoorbeeld wordt genomen vanuit een Visual Studio **Windows Classic Desktop** C#-console-app. Het project heeft het **WindowsAzure.ServiceBus** NuGet-pakket geïnstalleerd.

* Vervang de tijdelijke aanduiding voor de verbindingstekenreeks door een verbindingstekenreeks die gebruikmaakt van het **eindpunt** en de **hoofdthemawaarden** voor serviceprimaire die u eerder hebt opgemerkt zoals weergegeven in het volgende voorbeeld:

    ```csharp
    "Endpoint={your Event Hub-compatible endpoint};SharedAccessKeyName=service;SharedAccessKey={your service primary key value}"
    ```

* Vervang de tijdelijke aanduiding voor de tijdelijke aanduiding voor het eindpunt van het bewakingspunt door de waarde die u eerder hebt opgemerkt door de waarde van de gebeurtenishub.Replace the monitoring endpoint name placeholder with the **Event Hub-compatible name** value you noted previously.

```csharp
class Program
{
    static string connectionString = "{your monitoring endpoint connection string}";
    static string monitoringEndpointName = "{your monitoring endpoint name}";
    static EventHubClient eventHubClient;

    static void Main(string[] args)
    {
        Console.WriteLine("Monitoring. Press Enter key to exit.\n");

        eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, monitoringEndpointName);
        var d2cPartitions = eventHubClient.GetRuntimeInformation().PartitionIds;
        CancellationTokenSource cts = new CancellationTokenSource();
        var tasks = new List<Task>();

        foreach (string partition in d2cPartitions)
        {
            tasks.Add(ReceiveMessagesFromDeviceAsync(partition, cts.Token));
        }

        Console.ReadLine();
        Console.WriteLine("Exiting...");
        cts.Cancel();
        Task.WaitAll(tasks.ToArray());
    }

    private static async Task ReceiveMessagesFromDeviceAsync(string partition, CancellationToken ct)
    {
        var eventHubReceiver = eventHubClient.GetDefaultConsumerGroup().CreateReceiver(partition, DateTime.UtcNow);
        while (true)
        {
            if (ct.IsCancellationRequested)
            {
                await eventHubReceiver.CloseAsync();
                break;
            }

            EventData eventData = await eventHubReceiver.ReceiveAsync(new TimeSpan(0,0,10));

            if (eventData != null)
            {
                string data = Encoding.UTF8.GetString(eventData.GetBytes());
                Console.WriteLine("Message received. Partition: {0} Data: '{1}'", partition, data);
            }
        }
    }
}
```

## <a name="next-steps"></a>Volgende stappen

Zie:

* [Handleiding voor IoT Hub-ontwikkelaars](iot-hub-devguide.md)

* [AI implementeren op Edge-apparaten met Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)