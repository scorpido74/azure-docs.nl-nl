---
title: Uw Azure IoT Central-gegevens exporteren | Microsoft Docs
description: Gegevens exporteren uit uw Azure IoT Central-toepassing naar Azure Event Hubs, Azure Service Bus en Azure Blob Storage
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 01/30/2019
ms.topic: conceptual
ms.service: iot-central
manager: corywink
ms.openlocfilehash: 058fe9aea87879fe85dcbc6dcb864fd841fcb049
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77026794"
---
# <a name="export-your-azure-iot-central-data"></a>Uw Azure IoT Central-gegevens exporteren



*Dit onderwerp is van toepassing op beheerders.*

In dit artikel wordt beschreven hoe u de functie continue gegevens export in azure IoT Central kunt gebruiken om uw gegevens te exporteren naar **azure Event hubs**, **Azure service bus**of **Azure Blob Storage** -exemplaren. Gegevens worden geëxporteerd in JSON-indeling en kunnen gegevens over telemetrie, apparaatgegevens en apparaatinstellingen bevatten. De geëxporteerde gegevens gebruiken voor:

- Inzichten en analyse van warme paden. Deze optie omvat het activeren van aangepaste regels in Azure Stream Analytics, het activeren van aangepaste werk stromen in Azure Logic Apps, of het door geven via Azure Functions door lopen om te worden getransformeerd.
- Analyse van koude paden zoals trainings modellen in de analyse van Azure Machine Learning of lange termijn in micro soft Power BI.

> [!Note]
> Wanneer u continue gegevens export inschakelt, worden er vanaf dat moment alleen gegevens opgehaald. Op dit moment kunnen geen gegevens worden opgehaald voor een tijd dat de continue gegevens export is uitgeschakeld. Als u meer historische gegevens wilt behouden, moet u de continue gegevens export voor tijdig inschakelen.

## <a name="prerequisites"></a>Vereisten

U moet een beheerder zijn in uw IoT Central-toepassing of machtigingen voor het exporteren van gegevens hebben.

## <a name="set-up-export-destination"></a>Export bestemming instellen

Het export doel moet bestaan voordat u de continue gegevens export configureert.

### <a name="create-event-hubs-namespace"></a>Event Hubs naam ruimte maken

Als u geen bestaande Event Hubs naam ruimte hebt om naar te exporteren, voert u de volgende stappen uit:

1. Maak een [nieuwe event hubs naam ruimte in de Azure Portal](https://ms.portal.azure.com/#create/Microsoft.EventHub). Meer informatie vindt u in [Azure Event hubs docs](../../event-hubs/event-hubs-create.md).

2. Kies een abonnement. U kunt gegevens exporteren naar andere abonnementen die zich niet in hetzelfde abonnement bevinden als uw IoT Central-toepassing. In dit geval maakt u verbinding met behulp van een connection string.

3. Maak een Event Hub in uw Event Hubs naam ruimte. Ga naar uw naam ruimte en selecteer **+ Event hub** aan de bovenkant om een event hub-exemplaar te maken.

### <a name="create-service-bus-namespace"></a>Service Bus naam ruimte maken

Als u geen bestaande Service Bus naam ruimte hebt om naar te exporteren, voert u de volgende stappen uit:

1. Maak een [nieuwe service bus naam ruimte in de Azure Portal](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5). Meer informatie vindt u in [Azure service bus docs](../../service-bus-messaging/service-bus-create-namespace-portal.md).
2. Kies een abonnement. U kunt gegevens exporteren naar andere abonnementen die zich niet in hetzelfde abonnement bevinden als uw IoT Central-toepassing. In dit geval maakt u verbinding met behulp van een connection string.

3. Ga naar uw Service Bus-naam ruimte en selecteer **+ wachtrij** of **+ onderwerp** bovenaan om een wachtrij of onderwerp te maken waarnaar u wilt exporteren.

Wanneer u Service Bus als een export bestemming kiest, moeten de wacht rijen en onderwerpen geen sessies of duplicaten detectie zijn ingeschakeld. Als een van deze opties is ingeschakeld, arriveert sommige berichten niet in uw wachtrij of onderwerp.

### <a name="create-storage-account"></a>Een opslagaccount maken

Als u geen bestaand Azure Storage-account hebt om naar te exporteren, voert u de volgende stappen uit:

1. Maak een [Nieuw opslag account in de Azure Portal](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). Meer informatie over het maken van nieuwe [Azure Blob Storage-accounts](https://aka.ms/blobdocscreatestorageaccount) of [Azure data Lake Storage v2-opslag accounts](../../storage/blobs/data-lake-storage-quickstart-create-account.md).

    - Als u ervoor kiest om gegevens te exporteren naar een Azure Data Lake Storage v2-opslag account, moet u **BlobStorage** als het **soort account**kiezen.
    - U kunt gegevens exporteren naar opslag accounts in andere abonnementen dan die voor uw IoT Central-toepassing. In dit geval kunt u verbinding maken met behulp van een connection string.

2. Maak een container in uw opslag account. Ga naar uw opslagaccount. Selecteer onder **BLOB**-service **Bladeren door blobs**. Selecteer **+ container** aan de bovenkant om een nieuwe container te maken.

## <a name="set-up-continuous-data-export"></a>Continue gegevens export instellen

Nu u een bestemming hebt voor het exporteren van gegevens, voert u de volgende stappen uit om continue gegevens export in te stellen.

1. Meld u aan bij uw IoT Central-toepassing.

2. Selecteer in het linkerdeel venster **gegevens export**.

    > [!Note]
    > Als het exporteren van gegevens niet in het linkerdeel venster wordt weer gegeven, bent u niet gemachtigd om de gegevens export in uw app te configureren. Neem contact op met een beheerder om het exporteren van gegevens in te stellen.

3. Selecteer de knop **+ Nieuw** in de rechter bovenhoek. Kies een van de **Azure-Event hubs**, **Azure service bus**of **Azure Blob-opslag** als bestemming voor de export. Het maximum aantal uitvoer bewerkingen per toepassing is vijf.

    ![Nieuwe continue gegevens export maken](media/howto-export-data/new-export-definition.png)

4. Selecteer in de vervolg keuzelijst uw **Event hubs naam ruimte**, **Service Bus naam**ruimte, **naam ruimte van het opslag account**of **Voer een Connection String**in.

    - U ziet alleen opslag accounts, Event Hubs naam ruimten en Service Bus naam ruimten in hetzelfde abonnement als uw IoT Central-toepassing. Als u wilt exporteren naar een bestemming buiten dit abonnement, kiest u **een Connection String invoeren** en gaat u naar stap 5.
    - Voor apps die zijn gemaakt met het gratis prijs plan, is de enige manier om continue gegevens export te configureren via een connection string. Apps voor het gratis prijs plan hebben geen bijbehorend Azure-abonnement.

    ![Nieuwe event hub maken](media/howto-export-data/export-event-hub.png)

5. Beschrijving Als u **een connection string invoert**, wordt er een nieuw vak weer gegeven waarin u uw Connection String kunt plakken. Ga als volgt te connection string:
    - Event Hubs of Service Bus gaat u naar de naam ruimte in de Azure Portal.
        - Onder **instellingen**selecteert u **beleid voor gedeelde toegang**
        - De standaard **RootManageSharedAccessKey** kiezen of een nieuwe maken
        - De primaire of secundaire connection string kopiëren
    - Opslag account, gaat u naar het opslag account in de Azure Portal:
        - Selecteer onder **instellingen** **toegangs sleutels**
        - Kopieer de Key1-connection string of de Key2-connection string

6. Kies een Event Hub, wachtrij, onderwerp of container in de vervolg keuzelijst.

7. Kies onder te **exporteren gegevens**de typen gegevens die u wilt exporteren door het type in te stellen **op on**.

8. Als u continue gegevens export wilt inschakelen, schakelt u de **ingeschakelde** wissel knop **in**. Selecteer **Opslaan**.

9. Na enkele minuten worden uw gegevens weer gegeven in de bestemming die u hebt gekozen.

## <a name="export-contents-and-format"></a>Inhoud en indeling exporteren

Geëxporteerde telemetriegegevens bevatten het volledige bericht dat uw apparaten naar IoT Central zijn verzonden, niet alleen de telemetrie-waarden zelf. Gegevens van geëxporteerde apparaten bevatten wijzigingen in eigenschappen en meta gegevens van alle apparaten, en geëxporteerde Apparaatinstellingen bevatten wijzigingen in alle sjablonen voor apparaten.

Voor Event Hubs en Service Bus worden gegevens in bijna realtime geëxporteerd. De gegevens bevinden zich in de eigenschap Body en bevinden zich in JSON-indeling (zie hieronder voor beelden).

Voor Blob Storage worden gegevens eenmaal per minuut geëxporteerd, waarbij elk bestand met de batch wijzigingen sinds het laatste geëxporteerde bestand. Geëxporteerde gegevens worden in drie mappen in JSON-indeling geplaatst. De standaard paden in uw opslag account zijn:

- Telemetrie: _{container}/{app-id}/Telemetry/{yyyy}/{mm}/{dd}/{hh}/{mm}/{filename}_
- Apparaten: _{container}/{App-ID}/devices/{yyyy}/{mm}/{dd}/{hh}/{mm}/{filename}_
- Apparaatinstellingen: _{container}/{app-id}/deviceTemplates/{yyyy}/{mm}/{dd}/{hh}/{mm}/{filename}_

U kunt door de geëxporteerde bestanden in de Azure Portal bladeren door naar het bestand te navigeren en op het tabblad **BLOB bewerken** te klikken.


## <a name="telemetry"></a>Telemetrie

Voor Event Hubs en Service Bus wordt een nieuw bericht snel geëxporteerd nadat IoT Central het bericht van een apparaat ontvangt en elk geëxporteerd bericht het volledige bericht bevat dat is verzonden in de eigenschap Body in JSON-indeling.

Voor Blob Storage worden berichten per minuut in batch verzonden en geëxporteerd. De geëxporteerde bestanden gebruiken dezelfde indeling als de bericht bestanden die worden geëxporteerd door [IOT hub bericht routering](../../iot-hub/tutorial-routing.md) naar Blob Storage. 

> [!NOTE]
> Zorg ervoor dat uw apparaten berichten verzenden met `contentType: application/JSON` en `contentEncoding:utf-8` (of `utf-16`, `utf-32`) voor Blob Storage. Raadpleeg de [documentatie van IOT hub](../../iot-hub/iot-hub-devguide-routing-query-syntax.md#message-routing-query-based-on-message-body) voor een voor beeld.

Het apparaat dat de telemetrie heeft verzonden, wordt vertegenwoordigd door de apparaat-ID (Zie de volgende secties). Als u de namen van de apparaten wilt ophalen, gegevens van apparaten wilt exporteren en elk bericht wilt correleren met behulp van de **connectionDeviceId** die overeenkomt met de **deviceId** van het apparaat-bericht.

Dit is een voor beeld van een bericht dat is ontvangen in een Event Hub of Service Bus wachtrij of onderwerp.

```json
{
  "body":{
    "temp":67.96099945281145,
    "humid":58.51139305465015,
    "pm25":36.91162432340187
  },
  "annotations":{
    "iothub-connection-device-id":"<deviceId>",
    "iothub-connection-auth-method":"{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
    "iothub-connection-auth-generation-id":"<generationId>",
    "iothub-enqueuedtime":1539381029965,
    "iothub-message-source":"Telemetry",
    "x-opt-sequence-number":25325,
    "x-opt-offset":"<offset>",
    "x-opt-enqueued-time":1539381030200
  },
  "sequenceNumber":25325,
  "enqueuedTimeUtc":"2018-10-12T21:50:30.200Z",
  "offset":"<offset>",
  "properties":{
    "content_type":"application/json",
    "content_encoding":"utf-8"
  }
}
```

Dit is een voorbeeld record dat wordt geëxporteerd naar Blob Storage:

```json
{
  "EnqueuedTimeUtc":"2019-09-26T17:46:09.8870000Z",
  "Properties":{

  },
  "SystemProperties":{
    "connectionDeviceId":"<deviceid>",
    "connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
    "connectionDeviceGenerationId":"637051167384630591",
    "contentType":"application/json",
    "contentEncoding":"utf-8",
    "enqueuedTime":"2019-09-26T17:46:09.8870000Z"
  },
  "Body":{
    "temp":49.91322758395974,
    "humid":49.61214852573155,
    "pm25":25.87332214661367
  }
}
```

## <a name="devices"></a>Apparaten

Elk bericht of record in een moment opname vertegenwoordigt een of meer wijzigingen aan een apparaat en het apparaat en de Cloud eigenschappen sinds het laatste geëxporteerde bericht. Dit omvat:

- `id` van het apparaat in IoT Central
- `displayName` van het apparaat
- Id van de Device-sjabloon in `instanceOf`
- `simulated` vlag, waar als het apparaat een gesimuleerd apparaat is
- `provisioned` vlag, waar als het apparaat is ingericht
- `approved` vlag, waar als het apparaat is goedgekeurd voor het verzenden van gegevens
- Waarden van eigenschappen
- `properties` inclusief waarden van het apparaat en de Cloud eigenschappen

Verwijderde apparaten worden niet geëxporteerd. Er zijn momenteel geen indica toren voor het exporteren van berichten voor verwijderde apparaten.

Voor Event Hubs en Service Bus worden berichten met apparaatgegevens in bijna realtime naar uw Event Hub of Service Bus-wachtrij of onderwerp verzonden, zoals deze in IoT Central worden weer gegeven. 

Voor Blob Storage wordt een nieuwe moment opname met alle wijzigingen sinds de laatste geschreven één keer per minuut geëxporteerd.

Dit is een voorbeeld bericht over apparaten en eigenschappen van gegevens in Event Hub of Service Bus wachtrij of onderwerp:

```json
{
  "body":{
    "id": "<device Id>",
    "etag": "<etag>",
    "displayName": "Sensor 1",
    "instanceOf": "<device template Id>",
    "simulated": false,
    "provisioned": true,
    "approved": true,
    "properties": {
        "sensorComponent": {
            "setTemp": "30",
            "fwVersion": "2.0.1",
            "status": { "first": "first", "second": "second" },
            "$metadata": {
                "setTemp": {
                    "desiredValue": "30",
                    "desiredVersion": 3,
                    "desiredTimestamp": "2020-02-01T17:15:08.9284049Z",
                    "ackVersion": 3
                },
                "fwVersion": { "ackVersion": 3 },
                "status": {
                    "desiredValue": {
                        "first": "first",
                        "second": "second"
                    },
                    "desiredVersion": 2,
                    "desiredTimestamp": "2020-02-01T17:15:08.9284049Z",
                    "ackVersion": 2
                }
            },
            
        }
    },
    "installDate": { "installDate": "2020-02-01" }
},
  "annotations":{
    "iotcentral-message-source":"devices",
    "x-opt-partition-key":"<partitionKey>",
    "x-opt-sequence-number":39740,
    "x-opt-offset":"<offset>",
    "x-opt-enqueued-time":1539274959654
  },
  "partitionKey":"<partitionKey>",
  "sequenceNumber":39740,
  "enqueuedTimeUtc":"2020-02-01T18:14:49.3820326Z",
  "offset":"<offset>"
}
```

Dit is een voor beeld van een moment opname met de gegevens apparaten en eigenschappen in Blob Storage. Geëxporteerde bestanden bevatten één regel per record.

```json
{
  "id": "<device Id>",
  "etag": "<etag>",
  "displayName": "Sensor 1",
  "instanceOf": "<device template Id>",
  "simulated": false,
  "provisioned": true,
  "approved": true,
  "properties": {
      "sensorComponent": {
          "setTemp": "30",
          "fwVersion": "2.0.1",
          "status": { "first": "first", "second": "second" },
          "$metadata": {
              "setTemp": {
                  "desiredValue": "30",
                  "desiredVersion": 3,
                  "desiredTimestamp": "2020-02-01T17:15:08.9284049Z",
                  "ackVersion": 3
              },
              "fwVersion": { "ackVersion": 3 },
              "status": {
                  "desiredValue": {
                      "first": "first",
                      "second": "second"
                  },
                  "desiredVersion": 2,
                  "desiredTimestamp": "2020-02-01T17:15:08.9284049Z",
                  "ackVersion": 2
              }
          },
          
      }
  },
  "installDate": { "installDate": "2020-02-01" }
}
```

## <a name="device-templates"></a>Apparaatinstellingen

Elk bericht of momentopname record vertegenwoordigt een of meer wijzigingen aan een sjabloon van een gepubliceerd apparaat sinds het laatste geëxporteerde bericht. De informatie die in elk bericht of record wordt verzonden, omvat:

- `id` van de apparaatprofiel die overeenkomt met de `instanceOf` van de bovenstaande apparaten
- `displayName` van de sjabloon voor het apparaat
- Het apparaat `capabilityModel` met inbegrip van de `interfaces`en de definities van telemetrie, eigenschappen en opdrachten
- `cloudProperties` definities
- Onderdrukkingen en initiële waarden, inline met de `capabilityModel`

Verwijderde Apparaatinstellingen worden niet geëxporteerd. Er zijn momenteel geen indica toren in geëxporteerde berichten voor verwijderde Apparaatinstellingen.

Voor Event Hubs en Service Bus worden berichten met gegevens over de apparaatgegevens bijna in realtime naar uw Event Hub of Service Bus-wachtrij of onderwerp verzonden, zoals deze in IoT Central worden weer gegeven. 

Voor Blob Storage wordt een nieuwe moment opname met alle wijzigingen sinds de laatste geschreven één keer per minuut geëxporteerd.

Dit is een voorbeeld bericht over de gegevens van Device-sjablonen in Event Hub of Service Bus wachtrij of onderwerp:

```json
{
  "body":{
      "id": "<device template id>",
      "etag": "<etag>",
      "types": ["DeviceModel"],
      "displayName": "Sensor template",
      "capabilityModel": {
          "@id": "<capability model id>",
          "@type": ["CapabilityModel"],
          "contents": [],
          "implements": [
              {
                  "@id": "<component Id>",
                  "@type": ["InterfaceInstance"],
                  "name": "sensorComponent",
                  "schema": {
                      "@id": "<interface Id>",
                      "@type": ["Interface"],
                      "displayName": "Sensor interface",
                      "contents": [
                          {
                              "@id": "<id>",
                              "@type": ["Telemetry"],
                              "displayName": "Humidity",
                              "name": "humidity",
                              "schema": "double"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Telemetry", "SemanticType/Event"],
                              "displayName": "Error event",
                              "name": "error",
                              "schema": "integer"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Set temperature",
                              "name": "setTemp",
                              "writable": true,
                              "schema": "integer",
                              "unit": "Units/Temperature/fahrenheit",
                              "initialValue": "30"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Firmware version read only",
                              "name": "fwversion",
                              "schema": "string"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Display status",
                              "name": "status",
                              "writable": true,
                              "schema": {
                                  "@id": "urn:testInterface:status:obj:ka8iw8wka:1",
                                  "@type": ["Object"]
                              }
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Command"],
                              "commandType": "synchronous",
                              "request": {
                                  "@id": "<id>",
                                  "@type": ["SchemaField"],
                                  "displayName": "Configuration",
                                  "name": "config",
                                  "schema": "string"
                              },
                              "response": {
                                  "@id": "<id>",
                                  "@type": ["SchemaField"],
                                  "displayName": "Response",
                                  "name": "response",
                                  "schema": "string"
                              },
                              "displayName": "Configure sensor",
                              "name": "sensorConfig"
                          }
                      ]
                  }
              }
          ],
          "displayName": "Sensor capability model"
      },
      "solutionModel": {
          "@id": "<id>",
          "@type": ["SolutionModel"],
          "cloudProperties": [
              {
                  "@id": "<id>",
                  "@type": ["CloudProperty"],
                  "displayName": "Install date",
                  "name": "installDate",
                  "schema": "dateTime",
                  "valueDetail": {
                      "@id": "<id>",
                      "@type": ["ValueDetail/DateTimeValueDetail"]
                  }
              }
          ]
      }
  },
    "annotations":{
      "iotcentral-message-source":"deviceTemplates",
      "x-opt-partition-key":"<partitionKey>",
      "x-opt-sequence-number":25315,
      "x-opt-offset":"<offset>",
      "x-opt-enqueued-time":1539274985085
    },
    "partitionKey":"<partitionKey>",
    "sequenceNumber":25315,
    "enqueuedTimeUtc":"2019-10-02T16:23:05.085Z",
    "offset":"<offset>"
  }
}
```

Dit is een voor beeld van een moment opname met de gegevens apparaten en eigenschappen in Blob Storage. Geëxporteerde bestanden bevatten één regel per record.

```json
{
      "id": "<device template id>",
      "etag": "<etag>",
      "types": ["DeviceModel"],
      "displayName": "Sensor template",
      "capabilityModel": {
          "@id": "<capability model id>",
          "@type": ["CapabilityModel"],
          "contents": [],
          "implements": [
              {
                  "@id": "<component Id>",
                  "@type": ["InterfaceInstance"],
                  "name": "Sensor component",
                  "schema": {
                      "@id": "<interface Id>",
                      "@type": ["Interface"],
                      "displayName": "Sensor interface",
                      "contents": [
                          {
                              "@id": "<id>",
                              "@type": ["Telemetry"],
                              "displayName": "Humidity",
                              "name": "humidity",
                              "schema": "double"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Telemetry", "SemanticType/Event"],
                              "displayName": "Error event",
                              "name": "error",
                              "schema": "integer"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Set temperature",
                              "name": "setTemp",
                              "writable": true,
                              "schema": "integer",
                              "unit": "Units/Temperature/fahrenheit",
                              "initialValue": "30"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Firmware version read only",
                              "name": "fwversion",
                              "schema": "string"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Display status",
                              "name": "status",
                              "writable": true,
                              "schema": {
                                  "@id": "urn:testInterface:status:obj:ka8iw8wka:1",
                                  "@type": ["Object"]
                              }
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Command"],
                              "commandType": "synchronous",
                              "request": {
                                  "@id": "<id>",
                                  "@type": ["SchemaField"],
                                  "displayName": "Configuration",
                                  "name": "config",
                                  "schema": "string"
                              },
                              "response": {
                                  "@id": "<id>",
                                  "@type": ["SchemaField"],
                                  "displayName": "Response",
                                  "name": "response",
                                  "schema": "string"
                              },
                              "displayName": "Configure sensor",
                              "name": "sensorconfig"
                          }
                      ]
                  }
              }
          ],
          "displayName": "Sensor capability model"
      },
      "solutionModel": {
          "@id": "<id>",
          "@type": ["SolutionModel"],
          "cloudProperties": [
              {
                  "@id": "<id>",
                  "@type": ["CloudProperty"],
                  "displayName": "Install date",
                  "name": "installDate",
                  "schema": "dateTime",
                  "valueDetail": {
                      "@id": "<id>",
                      "@type": ["ValueDetail/DateTimeValueDetail"]
                  }
              }
          ]
      }
  }
```
## <a name="data-format-change-notice"></a>Wijzigings waarschuwing voor gegevens indeling

> [!Note]
> De gegevens indeling van de telemetrie-stroom wordt niet beïnvloed door deze wijziging. Alleen de apparaten en apparaatnamen van de gegevens stromen worden beïnvloed.

Als u een bestaande gegevens export in uw preview-toepassing hebt waarbij de streams *apparaten* en *apparaat-sjablonen* zijn ingeschakeld, moet u de export op **30 juni 2020**bijwerken. Dit geldt voor export naar Azure Blob Storage, Azure Event Hubs en Azure Service Bus.

Vanaf 3 februari 2020 heeft alle nieuwe exports in toepassingen met apparaten en apparaatinstellingen ingeschakeld de hierboven beschreven gegevens indeling. Alle exports die eerder zijn gemaakt, blijven op de oude gegevens indeling tot en met 30 juni 2020, waarna de exports automatisch naar de nieuwe gegevens indeling worden gemigreerd. De nieuwe gegevens indeling komt overeen met het [apparaat](https://docs.microsoft.com/rest/api/iotcentral/devices/get), de [eigenschap](https://docs.microsoft.com/rest/api/iotcentral/devices/getproperties)apparaat, de eigenschappen van de [apparaat-Cloud](https://docs.microsoft.com/rest/api/iotcentral/devices/getcloudproperties) en de object [sjabloon](https://docs.microsoft.com/rest/api/iotcentral/devicetemplates/get) objecten in de IOT Central open bare API. 
 
Voor **apparaten**zijn er belang rijke verschillen tussen de oude gegevens indeling en de nieuwe gegevens indeling:
- `@id` voor het apparaat wordt verwijderd, wordt de naam van `deviceId` gewijzigd in `id` 
- `provisioned` markering is toegevoegd om de inrichtings status van het apparaat te beschrijven
- `approved` vlag is toegevoegd om de goedkeurings status van het apparaat te beschrijven
- `properties` inclusief apparaat-en Cloud eigenschappen, komt overeen met entiteiten in de open bare API

Voor **apparaatprofielen**zijn de verschillen tussen de oude gegevens indeling en de nieuwe gegevens indeling als volgt:

- de naam van `@id` voor de apparaatprofiel is gewijzigd in `id`
- de naam van `@type` voor de sjabloon van het apparaat is gewijzigd in `types`en nu een matrix

### <a name="devices-format-deprecated-as-of-3-february-2020"></a>Apparaten (indeling afgeschaft op 3 februari 2020)
```json
{
  "@id":"<id-value>",
  "@type":"Device",
  "displayName":"Airbox",
  "data":{
    "$cloudProperties":{
        "Color":"blue"
    },
    "EnvironmentalSensor":{
      "thsensormodel":{
        "reported":{
          "value":"Neque quia et voluptatem veritatis assumenda consequuntur quod.",
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      },
      "pm25sensormodel":{
        "reported":{
          "value":"Aut alias odio.",
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      }
    },
    "urn_azureiot_DeviceManagement_DeviceInformation":{
      "totalStorage":{
        "reported":{
          "value":27900.9730905171,
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      },
      "totalMemory":{
        "reported":{
          "value":4667.82916715811,
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      }
    }
  },
  "instanceOf":"<template-id>",
  "deviceId":"<device-id>",
  "simulated":true
}
```

### <a name="device-templates-format-deprecated-as-of-3-february-2020"></a>Apparaatbeheer (indeling afgeschaft op 3 februari 2020)
```json
{
  "@id":"<template-id>",
  "@type":"DeviceModelDefinition",
  "displayName":"Airbox",
  "capabilityModel":{
    "@id":"<id>",
    "@type":"CapabilityModel",
    "implements":[
      {
        "@id":"<id>",
        "@type":"InterfaceInstance",
        "name":"EnvironmentalSensor",
        "schema":{
          "@id":"<id>",
          "@type":"Interface",
          "comment":"Requires temperature and humidity sensors.",
          "description":"Provides functionality to report temperature, humidity. Provides telemetry, commands and read-write properties",
          "displayName":"Environmental Sensor",
          "contents":[
            {
              "@id":"<id>",
              "@type":"Telemetry",
              "description":"Current temperature on the device",
              "displayName":"Temperature",
              "name":"temp",
              "schema":"double",
              "unit":"Units/Temperature/celsius",
              "valueDetail":{
                "@id":"<id>",
                "@type":"ValueDetail/NumberValueDetail",
                "minValue":{
                  "@value":"50"
                }
              },
              "visualizationDetail":{
                "@id":"<id>",
                "@type":"VisualizationDetail"
              }
            },
            {
              "@id":"<id>",
              "@type":"Telemetry",
              "description":"Current humidity on the device",
              "displayName":"Humidity",
              "name":"humid",
              "schema":"integer"
            },
            {
              "@id":"<id>",
              "@type":"Telemetry",
              "description":"Current PM2.5 on the device",
              "displayName":"PM2.5",
              "name":"pm25",
              "schema":"integer"
            },
            {
              "@id":"<id>",
              "@type":"Property",
              "description":"T&H Sensor Model Name",
              "displayName":"T&H Sensor Model",
              "name":"thsensormodel",
              "schema":"string"
            },
            {
              "@id":"<id>",
              "@type":"Property",
              "description":"PM2.5 Sensor Model Name",
              "displayName":"PM2.5 Sensor Model",
              "name":"pm25sensormodel",
              "schema":"string"
            }
          ]
        }
      },
      {
        "@id":"<id>",
        "@type":"InterfaceInstance",
        "name":"urn_azureiot_DeviceManagement_DeviceInformation",
        "schema":{
          "@id":"<id>",
          "@type":"Interface",
          "displayName":"Device information",
          "contents":[
            {
              "@id":"<id>",
              "@type":"Property",
              "comment":"Total available storage on the device in kilobytes. Ex. 20480000 kilobytes.",
              "displayName":"Total storage",
              "name":"totalStorage",
              "displayUnit":"kilobytes",
              "schema":"long"
            },
            {
              "@id":"<id>",
              "@type":"Property",
              "comment":"Total available memory on the device in kilobytes. Ex. 256000 kilobytes.",
              "displayName":"Total memory",
              "name":"totalMemory",
              "displayUnit":"kilobytes",
              "schema":"long"
            }
          ]
        }
      }
    ],
    "displayName":"AAEONAirbox52"
  },
  "solutionModel":{
    "@id":"<id>",
    "@type":"SolutionModel",
    "cloudProperties":[
      {
        "@id":"<id>",
        "@type":"CloudProperty",
        "displayName":"Color",
        "name":"Color",
        "schema":"string",
        "valueDetail":{
          "@id":"<id>",
          "@type":"ValueDetail/StringValueDetail"
        },
        "visualizationDetail":{
          "@id":"<id>",
          "@type":"VisualizationDetail"
        }
      }
    ]
  }
}
```
## <a name="next-steps"></a>Volgende stappen

Nu u weet hoe u uw gegevens naar Azure Event Hubs, Azure Service Bus en Azure Blob Storage kunt exporteren, gaat u verder met de volgende stap:

> [!div class="nextstepaction"]
> [Webhooks maken](./howto-create-webhooks.md)
