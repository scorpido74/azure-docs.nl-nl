---
title: De status van uw Azure-IoT Hub controleren | Microsoft Docs
description: Azure Monitor en Azure Resource Health gebruiken om snel uw IoT Hub te controleren en problemen vast te stellen
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: kgremban
ms.openlocfilehash: f801abc40caf273c28a0c01dedf9735f5198c2af
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/12/2019
ms.locfileid: "73929696"
---
# <a name="monitor-the-health-of-azure-iot-hub-and-diagnose-problems-quickly"></a>De status van Azure IoT Hub controleren en snel problemen vaststellen

Bedrijven die Azure IoT Hub implementeren, verwachten betrouw bare prestaties van hun resources. IoT Hub is volledig geïntegreerd met [Azure monitor](../azure-monitor/index.yml) en [Azure resource Health](../service-health/resource-health-overview.md)om u te helpen bij het bewaken van uw activiteiten. Deze twee services zijn geschikt om u de gegevens te geven die u nodig hebt om uw IoT-oplossingen te laten werken met een goede status.

Azure Monitor is een enkele bron van bewaking en logboek registratie voor al uw Azure-Services. U kunt de diagnostische logboeken verzenden die Azure Monitor gegenereerd voor Azure Monitor logboeken, Event Hubs of Azure Storage voor aangepaste verwerking. Met de instellingen voor gegevens en diagnostische gegevens van Azure Monitor krijgt u inzicht in de prestaties van uw resources. Ga verder met het lezen van dit artikel voor meer informatie over het [gebruik van Azure monitor](#use-azure-monitor) met uw IOT-hub. 

> [!IMPORTANT]
> De gebeurtenissen die worden verzonden door de IoT Hub-service met behulp van Azure Monitor Diagnostische logboeken zijn niet betrouwbaar of besteld. Sommige gebeurtenissen kunnen verloren gaan of in de juiste volg orde worden geleverd. Diagnostische logboeken zijn ook niet geschikt voor realtime en het kan enkele minuten duren voordat gebeurtenissen worden geregistreerd in het doel van uw keuze.

Azure Resource Health helpt u bij het vaststellen en verkrijgen van ondersteuning wanneer een Azure-probleem van invloed is op uw resources. Een dash board biedt de huidige en vorige integriteits status voor elk van uw IoT-hubs. Ga verder naar de sectie onder aan dit artikel voor meer informatie over het [gebruik van Azure resource Health](#use-azure-resource-health) met uw IOT-hub. 

IoT Hub biedt ook een eigen metrische gegevens die u kunt gebruiken om de status van uw IoT-resources te begrijpen. Zie [inzicht IOT hub metrische gegevens](iot-hub-metrics.md)voor meer informatie.

## <a name="use-azure-monitor"></a>Azure Monitor gebruiken

Azure Monitor voorziet in diagnostische gegevens voor Azure-resources, wat betekent dat u bewerkingen kunt bewaken die plaatsvinden in uw IoT-hub.

De diagnostische instellingen van Azure Monitor vervangt de IoT Hub Operations monitor. Als u momenteel bewerkingen bewaken gebruikt, moet u uw werk stromen migreren. Zie voor meer informatie [migreren van bewerkingen bewaken naar Diagnostische instellingen](iot-hub-migrate-to-diagnostics-settings.md).

Zie [ondersteunde metrische gegevens met Azure monitor](../azure-monitor/platform/metrics-supported.md) en [ondersteunde services, schema's en categorieën voor Diagnostische logboeken van Azure voor](../azure-monitor/platform/diagnostic-logs-schema.md)meer informatie over de specifieke metrische gegevens en gebeurtenissen die Azure monitor Watches.

[!INCLUDE [iot-hub-diagnostics-settings](../../includes/iot-hub-diagnostics-settings.md)]

### <a name="understand-the-logs"></a>Meer informatie over de logboeken

Azure Monitor registreert verschillende bewerkingen die in IoT Hub optreden. Elke categorie heeft een schema dat definieert hoe gebeurtenissen in die categorie worden gerapporteerd.

#### <a name="connections"></a>Verbindingen

De categorie verbindingen houdt het apparaat Connect en verbreekt gebeurtenissen van een IoT-hub en van fouten. Deze categorie is handig voor het identificeren van niet-geautoriseerde Verbindings pogingen en of waarschuwingen wanneer u de verbinding met apparaten verliest.

> [!NOTE]
> Voor een betrouw bare verbindings status van apparaten controleert u de heartbeat van het [apparaat](iot-hub-devguide-identity-registry.md#device-heartbeat).

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

#### <a name="cloud-to-device-commands"></a>Cloud-naar-apparaat-opdrachten

De categorie Cloud-naar-apparaat-opdrachten houdt fouten bij die zich op de IoT-hub voordoen en zijn gerelateerd aan de Cloud-naar-apparaat-berichten pijplijn. Deze categorie bevat fouten die zich voordoen vanaf:

* Cloud-naar-apparaat-berichten verzenden (zoals niet-geautoriseerde Sender fouten),
* Cloud-naar-apparaat-berichten ontvangen (zoals het aantal leverings fouten is overschreden), en
* Cloud-naar-apparaat-bericht feedback ontvangen (zoals verlopen feedback fouten).

In deze categorie worden geen fouten onderschept wanneer het Cloud-naar-apparaat-bericht wordt bezorgd, maar vervolgens niet goed wordt verwerkt door het apparaat.

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

#### <a name="device-identity-operations"></a>Bewerkingen voor apparaat-id's

De categorie apparaat-id bewerkingen houdt fouten bij die optreden wanneer u probeert een vermelding te maken, bij te werken of te verwijderen in het id-REGI ster van uw IoT-hub. Het bijhouden van deze categorie is handig voor het inrichten van scenario's.

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

In de categorie bericht routering worden fouten bijgehouden die optreden tijdens de bericht route-evaluatie en de eindpunt status, zoals wordt waargenomen door IoT Hub. Deze categorie bevat gebeurtenissen zoals:

* Een regel resulteert in "niet-gedefinieerd",
* IoT Hub markeert een eind punt als Dead of
* Eventuele fouten die zijn ontvangen van een eind punt. 

Deze categorie bevat geen specifieke fouten voor de berichten zelf (zoals storingen in het apparaat), die worden gerapporteerd in de categorie telemetrie van het apparaat.

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

#### <a name="device-telemetry"></a>Telemetrie van apparaat

De telemetrie-categorie van het apparaat houdt fouten bij die zich op de IoT-hub voordoen en zijn gerelateerd aan de telemetrie-pijp lijn. Deze categorie bevat fouten die optreden bij het verzenden van telemetrie-gebeurtenissen (zoals het beperken) en het ontvangen van telemetrie-gebeurtenissen (zoals niet-geautoriseerde lezer). Deze categorie kan geen fouten ondervangen die worden veroorzaakt door code die op het apparaat wordt uitgevoerd.

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

De categorie bestand uploaden houdt fouten bij die zich op de IoT-hub voordoen en zijn gerelateerd aan de functionaliteit voor het uploaden van bestanden. Deze categorie omvat:

* Fouten die zich voordoen met de SAS-URI, zoals wanneer deze verlopen voordat een apparaat de hub van een voltooide upload ontvangt.

* Mislukte uploads die zijn gerapporteerd door het apparaat.

* Fouten die optreden wanneer een bestand niet wordt gevonden in de opslag tijdens het maken van IoT Hub meldings bericht.

Deze categorie kan geen fouten ondervangen die rechtstreeks optreden terwijl het apparaat een bestand naar de opslag uploadt.

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

#### <a name="cloud-to-device-twin-operations"></a>Dubbele bewerkingen van het Cloud naar het apparaat

De categorie met dubbele bewerkingen van Cloud naar apparaat houdt de door de service geïnitieerde gebeurtenissen op apparaat apparaatdubbels. Deze bewerkingen kunnen bestaan uit het ophalen van dubbele, update of vervangen Tags en het bijwerken of vervangen van gewenste eigenschappen.

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

#### <a name="device-to-cloud-twin-operations"></a>Dubbele bewerkingen van het apparaat naar de Cloud

Met de categorie apparaat-naar-Cloud dubbele bewerkingen worden door het apparaat geïnitieerde gebeurtenissen op apparaat apparaatdubbels bijgehouden. Tot deze bewerkingen behoren onder andere ophalen, gerapporteerde eigenschappen bijwerken en abonneren op gewenste eigenschappen.

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

De categorie dubbele query's rapporteert over query aanvragen voor apparaatdubbels die in de cloud worden gestart.

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

De categorie taken bewerkingen rapporteert over taak aanvragen voor het bijwerken van apparaatdubbels of het aanroepen van directe methoden op meerdere apparaten. Deze aanvragen worden gestart in de Cloud.

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

In de categorie directe methoden worden aanvraag-antwoord interacties bijgehouden die naar afzonderlijke apparaten worden verzonden. Deze aanvragen worden gestart in de Cloud.

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

#### <a name="distributed-tracing-preview"></a>Gedistribueerde tracering (preview-versie)

De categorie gedistribueerde tracering houdt de correlatie-Id's bij voor berichten die de header van de tracering context bevatten. Als u deze logboeken volledig wilt inschakelen, moet de client code worden bijgewerkt door het [end-to-end van IOT-toepassingen te analyseren en te diagnosticeren met IOT hub Distributed tracing (preview)](iot-hub-distributed-tracing.md).

Houd er rekening mee dat `correlationId` voldoet aan het voor stel van de [W3C-tracering](https://github.com/w3c/trace-context) , waar het een `trace-id` en een `span-id`bevat.

##### <a name="iot-hub-d2c-device-to-cloud-logs"></a>IoT Hub D2C-Logboeken (apparaat-naar-Cloud)

IoT Hub registreert dit logboek wanneer een bericht met geldige traceer eigenschappen arriveert bij IoT Hub.

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

Hier wordt `durationMs` niet berekend omdat de klok van het IoT Hub mogelijk niet synchroon is met de klok van het apparaat en daarom kan de duur van de berekening misleidend zijn. U wordt aangeraden logica te schrijven met behulp van de tijds tempels in het gedeelte `properties` om pieken vast te leggen in latentie van apparaat naar Cloud.

| Eigenschap | Type | Beschrijving |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **messageSize** | Geheel getal | De grootte van het apparaat-naar-Cloud bericht in bytes |
| **deviceId** | Teken reeks van ASCII 7-bits alfanumerieke tekens | De identiteit van het apparaat |
| **callerLocalTimeUtc** | UTC-tijds tempel | De aanmaak tijd van het bericht zoals gerapporteerd door de lokale klok van het apparaat |
| **calleeLocalTimeUtc** | UTC-tijds tempel | De tijd van de aankomst van het bericht op de gateway van de IoT Hub, zoals gerapporteerd door de IoT Hub klok van de service |

##### <a name="iot-hub-ingress-logs"></a>IoT Hub ingangs logboeken

IoT Hub registreert dit logboek wanneer een bericht met geldige traceer eigenschappen naar een interne of ingebouwde Event hub schrijft.

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

In het gedeelte `properties` bevat dit logboek extra informatie over bericht binnenkomend.

| Eigenschap | Type | Beschrijving |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **isRoutingEnabled** | Tekenreeks | Waar of onwaar, geeft aan of bericht routering is ingeschakeld in de IoT Hub |
| **parentSpanId** | Tekenreeks | De [reeks-id](https://w3c.github.io/trace-context/#parent-id) van het bovenliggende bericht, die de D2C-bericht tracering in dit geval zou zijn |

##### <a name="iot-hub-egress-logs"></a>IoT Hub uitgangs logboeken

IoT Hub registreert dit logboek wanneer [route ring](iot-hub-devguide-messages-d2c.md) is ingeschakeld en het bericht naar een [eind punt](iot-hub-devguide-endpoints.md)wordt geschreven. Als route ring niet is ingeschakeld, wordt dit logboek IoT Hub niet geregistreerd.

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

In het gedeelte `properties` bevat dit logboek extra informatie over bericht binnenkomend.

| Eigenschap | Type | Beschrijving |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **endpointName** | Tekenreeks | De naam van het eind punt van de route ring |
| **endpointType** | Tekenreeks | Het type van het eind punt van de route ring |
| **parentSpanId** | Tekenreeks | De [reeks-id](https://w3c.github.io/trace-context/#parent-id) van het bovenliggende bericht, dat de IOT hub bericht tracering in dit geval zou zijn |

#### <a name="configurations"></a>Configuraties

In IoT Hub configuratie logboeken worden gebeurtenissen en fouten bijgehouden voor de functie voor het automatisch instellen van Apparaatbeheer.

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

### <a name="device-streams-preview"></a>Apparaatversleuteling (preview-versie)

De categorie Device streams traceert aanvraag-antwoord interacties die naar afzonderlijke apparaten worden verzonden.

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

Nadat u gebeurtenis logboek registratie via Diagnostische instellingen hebt ingesteld, kunt u toepassingen maken die de logboeken lezen, zodat u actie kunt ondernemen op basis van de informatie in hen. Met deze voorbeeld code worden logboeken van een Event Hub opgehaald:

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

## <a name="use-azure-resource-health"></a>Azure Resource Health gebruiken

Gebruik Azure Resource Health om te controleren of uw IoT-hub actief is. U kunt ook zien of een regionale storing invloed heeft op de status van uw IoT-hub. Voor specifieke details over de status van uw Azure IoT Hub, raden we u aan Azure Monitor te [gebruiken](#use-azure-monitor).

Met Azure IoT Hub wordt de status op een regionaal niveau aangegeven. Als een regionale storing gevolgen heeft voor uw IoT-hub, wordt de status weer gegeven als **onbekend**. Zie [resource typen en status controles in azure resource Health](../service-health/resource-health-checks-resource-types.md)voor meer informatie.

Voer de volgende stappen uit om de status van uw IoT-hubs te controleren:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com).

2. Navigeer naar **Service Health** > **resource Health**.

3. Selecteer uw abonnement in de vervolg keuzelijsten en selecteer vervolgens **IOT hub** als resource type.

Zie [overzicht van Azure resource Health](../service-health/resource-health-overview.md)voor meer informatie over het interpreteren van status gegevens.

## <a name="next-steps"></a>Volgende stappen

* [IoT Hub metrische gegevens begrijpen](iot-hub-metrics.md)
* [IoT-externe bewaking en meldingen met Azure Logic Apps verbinding maken met uw IoT hub en Postvak](iot-hub-monitoring-notifications-with-azure-logic-apps.md)
