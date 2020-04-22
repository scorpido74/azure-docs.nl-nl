---
title: De status van uw Azure IoT Hub controleren | Microsoft Documenten
description: Azure Monitor en Azure Resource Health gebruiken om uw IoT-hub te controleren en problemen snel te diagnosticeren
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: kgremban
ms.custom: amqp
ms.openlocfilehash: a1d74085090a3e20764d7b6fee84ffca52d5cb74
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732433"
---
# <a name="monitor-the-health-of-azure-iot-hub-and-diagnose-problems-quickly"></a>De status van Azure IoT Hub bewaken en snel problemen vaststellen

Bedrijven die Azure IoT Hub implementeren, verwachten betrouwbare prestaties van hun bronnen. Om u te helpen uw activiteiten nauwlettend in de gaten te houden, is IoT Hub volledig geïntegreerd met [Azure Monitor](../azure-monitor/index.yml) en Azure [Resource Health.](../service-health/resource-health-overview.md) Deze twee services bieden u de gegevens die u nodig hebt om uw IoT-oplossingen in een gezonde staat te houden.

Azure Monitor is een enkele bron van monitoring en logboekregistratie voor al uw Azure-services. U de diagnostische logboeken die Azure Monitor genereert, verzenden naar Azure Monitor-logboeken, gebeurtenishubs of Azure Storage voor aangepaste verwerking. De statistieken en diagnostische instellingen van Azure Monitor geven u inzicht in de prestaties van uw resources. Lees verder dit artikel voor meer informatie over het [gebruik van Azure Monitor](#use-azure-monitor) met uw IoT-hub. 

> [!IMPORTANT]
> De gebeurtenissen die worden uitgezonden door de IoT Hub-service met behulp van diagnostische logboeken van Azure Monitor, zijn niet gegarandeerd betrouwbaar of geordend. Sommige gebeurtenissen kunnen verloren gaan of buiten de bestelling worden geleverd. Diagnostische logboeken zijn ook niet bedoeld om real-time te zijn, en het kan enkele minuten duren voordat gebeurtenissen worden geregistreerd op uw keuze van bestemming.

Azure Resource Health helpt u bij het diagnosticeren en ondersteuning krijgen wanneer een Azure-probleem gevolgen heeft voor uw resources. Een dashboard biedt de status van de huidige en vroegere status voor elk van uw IoT-hubs. Ga verder naar de sectie onder aan dit artikel voor meer informatie over het [gebruik van Azure Resource Health](#use-azure-resource-health) met uw IoT-hub. 

IoT Hub biedt ook eigen statistieken die u gebruiken om de status van uw IoT-resources te begrijpen. Zie [IoT Hub-statistieken begrijpen](iot-hub-metrics.md)voor meer informatie.

## <a name="use-azure-monitor"></a>Azure Monitor gebruiken

Azure Monitor biedt diagnostische gegevens voor Azure-resources, wat betekent dat u bewerkingen controleren die plaatsvinden binnen uw IoT-hub.

De diagnostische instellingen van Azure Monitor vervangen de IoT Hub-bedrijfsmonitor. Als u momenteel operations monitoring gebruikt, moet u uw werkstromen migreren. Zie [Migreren van operationsmonitoring naar diagnostische instellingen](iot-hub-migrate-to-diagnostics-settings.md)voor meer informatie.

Zie [Ondersteunde statistieken met Azure Monitor-](../azure-monitor/platform/metrics-supported.md) en ondersteunde [services, schema's en categorieën voor Azure Diagnostic Logs voor](../azure-monitor/platform/diagnostic-logs-schema.md)meer informatie over de specifieke statistieken en gebeurtenissen die azure monitor-horloges inhoudt.

[!INCLUDE [iot-hub-diagnostics-settings](../../includes/iot-hub-diagnostics-settings.md)]

### <a name="understand-the-logs"></a>Logboeken begrijpen

Azure Monitor houdt verschillende bewerkingen bij die in IoT Hub plaatsvinden. Elke categorie heeft een schema dat bepaalt hoe gebeurtenissen in die categorie worden gerapporteerd.

#### <a name="connections"></a>Verbindingen

De categorie verbindingen houdt de verbinding tussen apparaten bij en koppelt gebeurtenissen los van een IoT-hub en fouten. Deze categorie is handig voor het identificeren van ongeautoriseerde verbindingspogingen en of waarschuwingen wanneer u de verbinding met apparaten verliest.

> [!NOTE]
> Controleer [apparaatheartbeat](iot-hub-devguide-identity-registry.md#device-heartbeat)voor een betrouwbare verbindingsstatus van apparaten.

```json
{
   "records":
   [
        {
            "time": " UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "deviceConnect",
            "category": "Connections",
            "level": "Information",
            "properties": "{\"deviceId\":\"<deviceId>\",\"protocol\":\"<protocol>\",\"authType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\",\"maskedIpAddress\":\"<maskedIpAddress>\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="cloud-to-device-commands"></a>Opdrachten voor cloud-naar-apparaat

De categorie opdrachten voor cloud-to-device houdt fouten bij die zich voordoen op de IoT-hub en zijn gerelateerd aan de berichtenpijplijn van cloud-to-device. Deze categorie bevat fouten die optreden van:

* Cloud-naar-apparaatberichten verzenden (zoals ongeautoriseerde afzenderfouten),
* Het ontvangen van cloud-to-device-berichten (zoals het aantal overschrijdingen van de bezorging stegen fouten) en
* Feedback van berichten van cloud naar apparaat ontvangen (zoals feedback verlopen fouten).

Deze categorie vangt geen fouten op wanneer het cloud-to-device-bericht met succes wordt geleverd, maar vervolgens onjuist wordt verwerkt door het apparaat.

```json
{
    "records":
    [
        {
            "time": " UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "messageExpired",
            "category": "C2DCommands",
            "level": "Error",
            "resultType": "Event status",
            "resultDescription": "MessageDescription",
            "properties": "{\"deviceId\":\"<deviceId>\",\"messageId\":\"<messageId>\",\"messageSizeInBytes\":\"<messageSize>\",\"protocol\":\"Amqp\",\"deliveryAcknowledgement\":\"<None, NegativeOnly, PositiveOnly, Full>\",\"deliveryCount\":\"0\",\"expiryTime\":\"<timestamp>\",\"timeInSystem\":\"<timeInSystem>\",\"ttl\":<ttl>, \"EventProcessedUtcTime\":\"<UTC timestamp>\",\"EventEnqueuedUtcTime\":\"<UTC timestamp>\", \"maskedIpAddress\": \"<maskedIpAddress>\", \"statusCode\": \"4XX\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="device-identity-operations"></a>Apparaatidentiteitsbewerkingen

De categorie apparaatidentiteitsbewerkingen houdt fouten bij die optreden wanneer u een vermelding in het identiteitsregister van uw IoT-hub probeert te maken, bijwerken of verwijderen. Het bijhouden van deze categorie is handig voor het inrichten van scenario's.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "get",
            "category": "DeviceIdentityOperations",
            "level": "Error",
            "resultType": "Event status",
            "resultDescription": "MessageDescription",
            "properties": "{\"maskedIpAddress\":\"<maskedIpAddress>\",\"deviceId\":\"<deviceId>\", \"statusCode\":\"4XX\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="routes"></a>Routes

De categorie berichtroutering houdt fouten bij die optreden tijdens de evaluatie van de berichtroute en de status van eindpunt en zoals waargenomen door IoT Hub. Deze categorie bevat gebeurtenissen zoals:

* Een regel wordt geëvalueerd op 'ongedefinieerd',
* IoT Hub markeert een eindpunt als dood, of
* Eventuele fouten die van een eindpunt zijn ontvangen. 

Deze categorie bevat geen specifieke fouten over de berichten zelf (zoals apparaatbeperkingsfouten), die worden gerapporteerd onder de categorie 'apparaattelemetrie'.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "endpointUnhealthy",
            "category": "Routes",
            "level": "Error",
            "properties": "{\"deviceId\": \"<deviceId>\",\"endpointName\":\"<endpointName>\",\"messageId\":<messageId>,\"details\":\"<errorDetails>\",\"routeName\": \"<routeName>\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="device-telemetry"></a>Apparaattelemetrie

De categorie telemetrie van het apparaat houdt fouten bij die optreden op de IoT-hub en zijn gerelateerd aan de telemetriepijplijn. Deze categorie bevat fouten die optreden bij het verzenden van telemetriegebeurtenissen (zoals beperking) en het ontvangen van telemetriegebeurtenissen (zoals onbevoegde lezer). Deze categorie kan geen fouten opvangen die worden veroorzaakt door code die op het apparaat zelf wordt uitgevoerd.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "ingress",
            "category": "DeviceTelemetry",
            "level": "Error",
            "resultType": "Event status",
            "resultDescription": "MessageDescription",
            "properties": "{\"deviceId\":\"<deviceId>\",\"batching\":\"0\",\"messageSizeInBytes\":\"<messageSizeInBytes>\",\"EventProcessedUtcTime\":\"<UTC timestamp>\",\"EventEnqueuedUtcTime\":\"<UTC timestamp>\",\"partitionId\":\"1\"}", 
            "location": "Resource location"
        }
    ]
}
```

#### <a name="file-upload-operations"></a>Bestandsuploadbewerkingen

De categorie voor het uploaden van bestanden houdt fouten bij die optreden op de IoT-hub en zijn gerelateerd aan de functionaliteit voor het uploaden van bestanden. Deze categorie omvat:

* Fouten die optreden met de SAS URI, zoals wanneer deze verloopt voordat een apparaat de hub van een voltooide upload op de hoogte stelt.

* Mislukte uploads die door het apparaat worden gerapporteerd.

* Fouten die optreden wanneer een bestand niet wordt gevonden in de opslag tijdens het maken van iot-hubberichten.

Deze categorie kan geen fouten opvangen die rechtstreeks optreden terwijl het apparaat een bestand uploadt naar opslag.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "ingress",
            "category": "FileUploadOperations",
            "level": "Error",
            "resultType": "Event status",
            "resultDescription": "MessageDescription",
            "durationMs": "1",
            "properties": "{\"deviceId\":\"<deviceId>\",\"protocol\":\"<protocol>\",\"authType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\",\"blobUri\":\"http//bloburi.com\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="cloud-to-device-twin-operations"></a>Cloud-to-device twin operations

De categorie twin operations van cloud-to-device houdt door services geïnitieerde gebeurtenissen bij op apparaattweelingen. Deze bewerkingen kunnen bestaan uit het krijgen van dubbele, bijwerken of vervangen tags, en bijwerken of vervangen van de gewenste eigenschappen.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "read",
            "category": "C2DTwinOperations",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"deviceId\":\"<deviceId>\",\"sdkVersion\":\"<sdkVersion>\",\"messageSize\":\"<messageSize>\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="device-to-cloud-twin-operations"></a>Dubbele bewerkingen van device-to-cloud

De categorie device-to-cloud twin operations houdt door apparaten geïnitieerde gebeurtenissen bij op apparaattweelingen. Deze bewerkingen kunnen bestaan uit get twin, update gerapporteerde eigenschappen, en abonneren op de gewenste eigenschappen.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "update",
            "category": "D2CTwinOperations",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"deviceId\":\"<deviceId>\",\"protocol\":\"<protocol>\",\"authenticationType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="twin-queries"></a>Dubbele query's

De categorie twee query's rapporteert over queryaanvragen voor apparaattweelingen die in de cloud worden gestart.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "query",
            "category": "TwinQueries",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"query\":\"<twin query>\",\"sdkVersion\":\"<sdkVersion>\",\"messageSize\":\"<messageSize>\",\"pageSize\":\"<pageSize>\", \"continuation\":\"<true, false>\", \"resultSize\":\"<resultSize>\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="jobs-operations"></a>Taakbewerkingen

De categorie taken rapporten over taakaanvragen om apparaattweelingen bij te werken of directe methoden op meerdere apparaten aan te roepen. Deze aanvragen worden gestart in de cloud.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "jobCompleted",
            "category": "JobsOperations",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"jobId\":\"<jobId>\", \"sdkVersion\": \"<sdkVersion>\",\"messageSize\": <messageSize>,\"filter\":\"DeviceId IN ['1414ded9-b445-414d-89b9-e48e8c6285d5']\",\"startTimeUtc\":\"Wednesday, September 13, 2017\",\"duration\":\"0\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="direct-methods"></a>Directe methoden

De categorie directe methoden houdt interacties met het verzoek-antwoord bij die naar afzonderlijke apparaten worden verzonden. Deze aanvragen worden gestart in de cloud.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "send",
            "category": "DirectMethods",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"deviceId\":<messageSize>, \"RequestSize\": 1, \"ResponseSize\": 1, \"sdkVersion\": \"2017-07-11\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="distributed-tracing-preview"></a>Gedistribueerde tracering (voorbeeld)

De categorie gedistribueerde tracering houdt de correlatie-id's bij voor berichten met de tracecontextkoptekst. Om deze logboeken volledig in te schakelen, moet client-side code worden bijgewerkt door [IoT-toepassingen end-to-end te analyseren en te diagnosticeren met IoT Hub distributed tracing (preview).](iot-hub-distributed-tracing.md)

Houd `correlationId` er rekening mee dat voldoet aan het [W3C Trace](https://github.com/w3c/trace-context) Context-voorstel, waarbij het zowel een `trace-id` als een `span-id`.

##### <a name="iot-hub-d2c-device-to-cloud-logs"></a>IoT Hub D2C -logboeken (device-to-cloud)

IoT Hub registreert dit logboek wanneer een bericht met geldige traceringseigenschappen binnenkomt bij IoT Hub.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "DiagnosticIoTHubD2C",
            "category": "DistributedTracing",
            "correlationId": "00-8cd869a412459a25f5b4f31311223344-0144d2590aacd909-01",
            "level": "Information",
            "resultType": "Success",
            "resultDescription":"Receive message success",
            "durationMs": "",
            "properties": "{\"messageSize\": 1, \"deviceId\":\"<deviceId>\", \"callerLocalTimeUtc\": : \"2017-02-22T03:27:28.633Z\", \"calleeLocalTimeUtc\": \"2017-02-22T03:27:28.687Z\"}",
            "location": "Resource location"
        }
    ]
}
```

Hier `durationMs` wordt niet berekend omdat de klok van IoT Hub mogelijk niet synchroon loopt met de apparaatklok, en dus kan een duurberekening misleidend zijn. We raden u aan logica te `properties` schrijven met behulp van de tijdstempels in de sectie om pieken in de latentie van apparaat tot cloud vast te leggen.

| Eigenschap | Type | Beschrijving |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **berichtGrootte** | Geheel getal | De grootte van device-to-cloud-berichten in bytes |
| **deviceId** | Tekenreeks van Alfanumerieke tekens van ASCII 7-bits | De identiteit van het apparaat |
| **bellerLocalTimeUtc** | UTC-tijdstempel | De aanmaaktijd van het bericht zoals gerapporteerd door de lokale klok van het apparaat |
| **calleeLocalTimeUtc** | UTC-tijdstempel | Het tijdstip van aankomst van berichten op de gateway van de IoT Hub, zoals gerapporteerd door de side clock van de IoT Hub-service |

##### <a name="iot-hub-ingress-logs"></a>IoT Hub-ingress-logboeken

IoT Hub registreert dit logboek wanneer een bericht met geldige traceringseigenschappen naar interne of ingebouwde gebeurtenishub wordt geschreven.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "DiagnosticIoTHubIngress",
            "category": "DistributedTracing",
            "correlationId": "00-8cd869a412459a25f5b4f31311223344-349810a9bbd28730-01",
            "level": "Information",
            "resultType": "Success",
            "resultDescription":"Ingress message success",
            "durationMs": "10",
            "properties": "{\"isRoutingEnabled\": \"true\", \"parentSpanId\":\"0144d2590aacd909\"}",
            "location": "Resource location"
        }
    ]
}
```

In `properties` de sectie bevat dit logboek aanvullende informatie over het binnendringen van berichten.

| Eigenschap | Type | Beschrijving |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **isRoutingEnabled** | Tekenreeks | True of false geeft aan of berichtroutering is ingeschakeld in de IoT-hub |
| **parentSpanId parentSpanId** | Tekenreeks | De [span-id](https://w3c.github.io/trace-context/#parent-id) van het bovenliggende bericht, dat zou de D2C bericht te traceren in dit geval |

##### <a name="iot-hub-egress-logs"></a>IoT Hub-uitgangslogboeken

IoT Hub registreert dit logboek wanneer [routering](iot-hub-devguide-messages-d2c.md) is ingeschakeld en het bericht naar een [eindpunt](iot-hub-devguide-endpoints.md)wordt geschreven. Als routering niet is ingeschakeld, neemt IoT Hub dit logboek niet op.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "DiagnosticIoTHubEgress",
            "category": "DistributedTracing",
            "correlationId": "00-8cd869a412459a25f5b4f31311223344-98ac3578922acd26-01",
            "level": "Information",
            "resultType": "Success",
            "resultDescription":"Egress message success",
            "durationMs": "10",
            "properties": "{\"endpointType\": \"EventHub\", \"endpointName\": \"myEventHub\", \"parentSpanId\":\"349810a9bbd28730\"}",
            "location": "Resource location"
        }
    ]
}
```

In `properties` de sectie bevat dit logboek aanvullende informatie over het binnendringen van berichten.

| Eigenschap | Type | Beschrijving |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **endpointNaam** | Tekenreeks | De naam van het routeringseindpunt |
| **eindpuntType** | Tekenreeks | Het type routeeindpunt |
| **parentSpanId parentSpanId** | Tekenreeks | De [span-id](https://w3c.github.io/trace-context/#parent-id) van het bovenliggende bericht, dat zou de IoT Hub ingress bericht te traceren in dit geval |

#### <a name="configurations"></a>Configuraties

IoT Hub-configuratie registreert gebeurtenissen en fouten voor de functieset Automatisch apparaatbeheer.

```json
{
    "records":
    [
         {
             "time": "2019-09-24T17:21:52Z",
             "resourceId": "Resource Id",
             "operationName": "ReadManyConfigurations",
             "category": "Configurations",
             "resultType": "",
             "resultDescription": "",
             "level": "Information",
             "durationMs": "17",
             "properties": "{\"configurationId\":\"\",\"sdkVersion\":\"2018-06-30\",\"messageSize\":\"0\",\"statusCode\":null}",
             "location": "southcentralus"
         }
    ]
}
```

### <a name="device-streams-preview"></a>Apparaatstreams (voorbeeld)

De categorie streamt categorie interacties met aanvragen en antwoorden die naar afzonderlijke apparaten worden verzonden.

```json
{
    "records":
    [
         {
             "time": "2019-09-19T11:12:04Z",
             "resourceId": "Resource Id",
             "operationName": "invoke",
             "category": "DeviceStreams",
             "resultType": "",
             "resultDescription": "",    
             "level": "Information",
             "durationMs": "74",
             "properties": "{\"deviceId\":\"myDevice\",\"moduleId\":\"myModule\",\"sdkVersion\":\"2019-05-01-preview\",\"requestSize\":\"3\",\"responseSize\":\"5\",\"statusCode\":null,\"requestName\":\"myRequest\",\"direction\":\"c2d\"}",
             "location": "Central US"
         }
    ]
}
```

### <a name="read-logs-from-azure-event-hubs"></a>Logboeken van Azure Event Hubs lezen

Nadat u logboekregistratie via diagnostische instellingen hebt ingesteld, u toepassingen maken die de logboeken uitlezen, zodat u actie ondernemen op basis van de informatie in deze logboeken. Met deze voorbeeldcode worden logboeken opgehaald van een gebeurtenishub:

```csharp
class Program
{ 
    static string connectionString = "{your AMS eventhub endpoint connection string}";
    static string monitoringEndpointName = "{your AMS event hub endpoint name}";
    static EventHubClient eventHubClient;
    //This is the Diagnostic Settings schema
    class AzureMonitorDiagnosticLog
    {
        string time { get; set; }
        string resourceId { get; set; }
        string operationName { get; set; }
        string category { get; set; }
        string level { get; set; }
        string resultType { get; set; }
        string resultDescription { get; set; }
        string durationMs { get; set; }
        string callerIpAddress { get; set; }
        string correlationId { get; set; }
        string identity { get; set; }
        string location { get; set; }
        Dictionary<string, string> properties { get; set; }
    };

    static void Main(string[] args)
    {
        Console.WriteLine("Monitoring. Press Enter key to exit.\n");
        eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, monitoringEndpointName);
        var d2cPartitions = eventHubClient.GetRuntimeInformationAsync().PartitionIds;
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
                var deserializer = new JavaScriptSerializer();
                //deserialize json data to azure monitor object
                AzureMonitorDiagnosticLog message = new JavaScriptSerializer().Deserialize<AzureMonitorDiagnosticLog>(result);
            }
        }
    }
}
```

## <a name="use-azure-resource-health"></a>Azure-bronstatus gebruiken

Gebruik Azure Resource Health om te controleren of uw IoT-hub actief is. U ook leren of een regionale storing van invloed is op de gezondheid van uw IoT-hub. Als u specifieke details wilt weten over de status van uw Azure IoT Hub, raden we u aan [Azure Monitor te gebruiken.](#use-azure-monitor)

Azure IoT Hub geeft de status op regionaal niveau aan. Als een regionale storing van invloed is op uw IoT-hub, wordt de status als **Onbekend weergegeven.** Zie [Resourcetypen en statuscontroles in Azure-bronstatus](../service-health/resource-health-checks-resource-types.md)voor meer informatie.

Voer de volgende stappen uit om de status van uw IoT-hubs te controleren:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

2. Navigeer naar de status van **servicestatus** > **resource**.

3. Selecteer in de vervolgkeuzelijsten uw abonnement en selecteer **IoT Hub** als resourcetype.

Zie overzicht van [azure resource status](../service-health/resource-health-overview.md)voor meer informatie over het interpreteren van statusgegevens.

## <a name="next-steps"></a>Volgende stappen

* [Informatie over IoT Hub-statistieken](iot-hub-metrics.md)
* [IoT-bewaking en meldingen op afstand met Azure Logic Apps die uw IoT-hub en -postvak met elkaar verbinden](iot-hub-monitoring-notifications-with-azure-logic-apps.md)
