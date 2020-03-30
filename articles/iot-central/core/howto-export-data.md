---
title: Uw Azure IoT Central-gegevens exporteren | Microsoft Documenten
description: Gegevens exporteren vanuit uw Azure IoT Central-toepassing naar Azure Event Hubs, Azure Service Bus en Azure Blob Storage
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 01/30/2019
ms.topic: how-to
ms.service: iot-central
manager: corywink
ms.openlocfilehash: 725c5acf961fffb1fd4cf9bc17e37a5940f871cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80157905"
---
# <a name="export-iot-data-to-destinations-in-azure"></a>IoT-gegevens exporteren naar bestemmingen in Azure

*Dit onderwerp is van toepassing op beheerders.*

In dit artikel wordt beschreven hoe u de functie voor het exporteren van continue gegevens in Azure IoT Central gebruiken om uw gegevens te exporteren naar **Azure Event Hubs,** **Azure Service Bus**of Azure **Blob-opslagexemplaren.** Gegevens worden geëxporteerd in JSON-indeling en kunnen telemetrie, apparaatinformatie en apparaatsjabloongegevens bevatten. Gebruik de geëxporteerde gegevens voor:

- Inzichten en analyses van warme paden. Deze optie omvat het activeren van aangepaste regels in Azure Stream Analytics, het activeren van aangepaste werkstromen in Azure Logic Apps of het doorgeven via Azure-functies om te worden getransformeerd.
- Cold-path analytics zoals trainingsmodellen in Azure Machine Learning of langetermijntrendanalyse in Microsoft Power BI.

> [!Note]
> Wanneer u continue gegevensexport inschakelt, krijgt u vanaf dat moment alleen de gegevens. Momenteel kunnen gegevens niet worden opgehaald voor een tijd waarin continue gegevensexporteren was uitgeschakeld. Als u meer historische gegevens wilt bewaren, schakelt u continue gegevensexport vroegtijdig in.

## <a name="prerequisites"></a>Vereisten

U moet een beheerder zijn in uw IoT Central-toepassing of machtigingen voor het exporteren van gegevens hebben.

## <a name="set-up-export-destination"></a>Exportbestemming instellen

Uw exportbestemming moet bestaan voordat u uw continue gegevensexport configureert.

### <a name="create-event-hubs-namespace"></a>De Event Hubs-naamruimte maken

Als u geen bestaande naamruimte voor gebeurtenishubs hebt om naar te exporteren, voert u de volgende stappen uit:

1. Maak een [nieuwe naamruimte voor gebeurtenishubs in de Azure-portal.](https://ms.portal.azure.com/#create/Microsoft.EventHub) Meer informatie vindt u in [Azure Event Hubs-documenten](../../event-hubs/event-hubs-create.md).

2. Kies een abonnement. U gegevens exporteren naar andere abonnementen die niet in hetzelfde abonnement zitten als uw IoT Central-toepassing. U maakt in dit geval verbinding via een verbindingstekenreeks.

3. Maak een gebeurtenishub in de naamruimte van uw gebeurtenishubs. Ga naar je naamruimte en selecteer **+ Gebeurtenishub** bovenaan om een gebeurtenishub-exemplaar te maken.

### <a name="create-service-bus-namespace"></a>Naamruimte servicebus maken

Als u geen bestaande naamruimte van servicebus hebt om naar te exporteren, voert u de volgende stappen uit:

1. Maak een [nieuwe naamruimte voor servicebus in de Azure-portal.](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5) Meer informatie vindt u in [Azure Service Bus-documenten](../../service-bus-messaging/service-bus-create-namespace-portal.md).
2. Kies een abonnement. U gegevens exporteren naar andere abonnementen die niet in hetzelfde abonnement zitten als uw IoT Central-toepassing. U maakt in dit geval verbinding via een verbindingstekenreeks.

3. Ga naar de naamruimte van uw servicebus en selecteer **+ Wachtrij** of **+ Onderwerp** bovenaan om een wachtrij of onderwerp te maken om naar te exporteren.

Wanneer u ServiceBus als exportbestemming kiest, mogen de wachtrijen en onderwerpen geen sessies of dubbele detectie hebben ingeschakeld. Als een van deze opties is ingeschakeld, worden sommige berichten niet in uw wachtrij of onderwerp weergegeven.

### <a name="create-storage-account"></a>Een opslagaccount maken

Als u geen bestaand Azure Storage-account hebt om naar te exporteren, voert u de volgende stappen uit:

1. Maak een [nieuw opslagaccount in de Azure-portal.](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM) Meer informatie over het maken van nieuwe [Azure Blob Storage-accounts](https://aka.ms/blobdocscreatestorageaccount) of [Azure Data Lake Storage v2-opslagaccounts](../../storage/blobs/data-lake-storage-quickstart-create-account.md). Het exporteren van gegevens kan alleen gegevens schrijven naar opslagaccounts die blokblobs ondersteunen. Hieronder volgt een lijst met bekende compatibele typen opslagaccounts: 

    |Prestatielaag|Accounttype|
    |-|-|
    |Standard|Algemeen doel V2|
    |Standard|Algemeen doel V1|
    |Standard|Blob Storage|
    |Premium|Blob-opslag blokkeren|

2. Maak een container in uw opslagaccount. Ga naar uw opslagaccount. Selecteer Blobs onder **Blob-service**door **Blobs bladeren**. Selecteer **+ Container** bovenaan om een nieuwe container te maken.

## <a name="set-up-continuous-data-export"></a>Continue gegevensexport instellen

Nu u een bestemming hebt om gegevens naar te exporteren, voert u deze stappen uit om continue gegevensexport in te stellen.

1. Meld u aan bij uw IoT Central-toepassing.

2. Selecteer **Gegevens exporteren**in het linkerdeelvenster .

    > [!Note]
    > Als u Gegevensexporteren niet in het linkerdeelvenster ziet, hebt u geen machtigingen om gegevensexport in uw app te configureren. Praat met een beheerder om gegevensexport in te stellen.

3. Selecteer de knop **+ Nieuw** rechtsboven. Kies een van **Azure Event Hubs,** **Azure Service Bus**of Azure **Blob-opslag** als bestemming van uw export. Het maximum aantal uitvoer per aanvraag is vijf.

    ![Nieuwe continue gegevensexport maken](media/howto-export-data/new-export-definition.png)

4. Selecteer in de vervolgkeuzelijst de **naamruimte van gebeurtenishubs,** **naamruimte servicebus,** **naamruimte voor opslagaccount**of **Voer een verbindingstekenreeks in**.

    - U ziet alleen naamruimten voor opslagaccounts, gebeurtenishubs en naamruimten van servicebus in hetzelfde abonnement als uw IoT Central-toepassing. Als u wilt exporteren naar een bestemming buiten dit abonnement, kiest u **Een verbindingstekenreeks invoeren** en ziet u stap 5.
    - Voor apps die zijn gemaakt met behulp van het gratis prijsplan, is de enige manier om continue gegevensexport te configureren via een verbindingstekenreeks. Apps met het gratis prijsplan hebben geen gekoppeld Azure-abonnement.

    ![Nieuwe gebeurtenishub maken](media/howto-export-data/export-event-hub.png)

5. (Optioneel) Als u **Een verbindingstekenreeks invoeren**hebt gekozen, wordt er een nieuw vak weergegeven dat u de verbindingstekenreeks plakken. Ga als u op zoek naar de verbindingstekenreeks voor uw:
    - Gebeurtenishubs of servicebus, ga naar de naamruimte in de Azure-portal.
        - Selecteer **Onder Instellingen**de optie Beleid voor gedeelde **toegang**
        - Kies de standaard **RootManageSharedAccessKey** of maak een nieuwe
        - De primaire of secundaire verbindingstekenreeks kopiëren
    - Opslagaccount, ga naar het opslagaccount in de Azure-portal:
        - Selecteer **Access-toetsen** **onder Instellingen**
        - De toets1-verbindingstekenreeks of de toets2-verbindingstekenreeks kopiëren

6. Kies een gebeurtenishub, wachtrij, onderwerp of container in de vervolgkeuzelijst.

7. Kies **onder Gegevens om te exporteren**de typen gegevens die u wilt exporteren door het type in te stellen op **Aan**.

8. Als u continue gegevensexport wilt inschakelen, controleert u of de **ingeschakelde** toggle ingeschakeld is **ingeschakeld**. Selecteer **Opslaan**.

9. Na een paar minuten worden uw gegevens weergegeven in de door u gekozen bestemming.

## <a name="export-contents-and-format"></a>Inhoud en indeling exporteren

Geëxporteerde telemetriegegevens bevatten het volledige bericht dat uw apparaten naar IoT Central hebben verzonden, niet alleen de telemetriewaarden zelf. Geëxporteerde apparatengegevens bevatten wijzigingen in eigenschappen en metagegevens van alle apparaten en geëxporteerde apparaatsjablonen bevatten wijzigingen in alle apparaatsjablonen.

Voor eventhubs en servicebus worden gegevens in bijna realtime geëxporteerd. De gegevens zitten in de eigenschap body en zijn in JSON-formaat (zie hieronder voor voorbeelden).

Voor Blob Storage worden gegevens één keer per minuut geëxporteerd, waarbij elk bestand de batch met wijzigingen bevat sinds het laatst geëxporteerde bestand. Geëxporteerde gegevens worden in drie mappen in JSON-indeling geplaatst. De standaardpaden in uw opslagaccount zijn:

- Telemetrie: _{container}/{app-id}/telemetrie/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}_
- Apparaten: _{container}/{app-id}/devices/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}_
- Apparaatsjablonen: _{container}/{app-id}/deviceTemplates/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}_

U door de geëxporteerde bestanden in de Azure-portal bladeren door naar het bestand te navigeren en het **tabblad Blob bewerken** te kiezen.


## <a name="telemetry"></a>Telemetrie

Voor gebeurtenishubs en servicebus wordt een nieuw bericht snel geëxporteerd nadat IoT Central het bericht van een apparaat heeft ontvangen en elk geëxporteerd bericht het volledige bericht bevat dat het apparaat in de eigenschap Body in JSON-indeling heeft verzonden.

Voor Blob Storage worden berichten één keer per minuut gebatcht en geëxporteerd. De geëxporteerde bestanden gebruiken dezelfde indeling als de berichtbestanden die worden geëxporteerd door [de routeringsroute van het IoT Hub-bericht](../../iot-hub/tutorial-routing.md) naar blobopslag. 

> [!NOTE]
> Controleer bij Blob-opslag of uw apparaten `contentType: application/JSON` berichten `contentEncoding:utf-8` verzenden `utf-16` `utf-32`die berichten verzenden en (of , ). Zie de [IoT Hub-documentatie](../../iot-hub/iot-hub-devguide-routing-query-syntax.md#message-routing-query-based-on-message-body) voor een voorbeeld.

Het apparaat dat de telemetrie heeft verzonden, wordt vertegenwoordigd door de apparaat-id (zie de volgende secties). Als u de namen van de apparaten wilt krijgen, exporteert u apparaatgegevens en correleert u elk bericht met behulp van de **verbindingDeviceId** die overeenkomt met de **deviceId** van het apparaatbericht.

Dit is een voorbeeldbericht dat is ontvangen in een gebeurtenishub of servicebuswachtrij of -onderwerp.

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

Dit is een voorbeeldrecord dat naar blobopslag wordt geëxporteerd:

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

Elk bericht of record in een momentopname vertegenwoordigt een of meer wijzigingen in een apparaat en de eigenschappen van het apparaat en de cloud sinds het laatst geëxporteerde bericht. Dit omvat:

- `id`van het apparaat in IoT Central
- `displayName`van het apparaat
- Apparaatsjabloon-id in`instanceOf`
- `simulated`vlag, true als het apparaat een gesimuleerd apparaat is
- `provisioned`vlag, waar als het apparaat is ingericht
- `approved`vlag, true als het apparaat is goedgekeurd om gegevens te verzenden
- Eigenschapswaarden
- `properties`inclusief waardevan apparaat- en cloudeigenschappen

Verwijderde apparaten worden niet geëxporteerd. Momenteel zijn er geen indicatoren in geëxporteerde berichten voor verwijderde apparaten.

Voor gebeurtenishubs en servicebus worden berichten met apparaatgegevens in bijna realtime verzonden naar uw gebeurtenishub of servicebuswachtrij of -onderwerp, zoals deze wordt weergegeven in IoT Central. 

Voor Blob Storage wordt een nieuwe momentopname met alle wijzigingen sinds de laatste geschreven keer per minuut geëxporteerd.

Dit is een voorbeeldbericht over gegevens over apparaten en eigenschappen in de gebeurtenishub of servicebuswachtrij of -onderwerp:

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

Dit is een voorbeeldmomentopname met apparaten en eigenschappengegevens in Blob Storage. Geëxporteerde bestanden bevatten één regel per record.

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

## <a name="device-templates"></a>Apparaatsjablonen

Elk bericht of momentopnamerecord vertegenwoordigt een of meer wijzigingen in een gepubliceerde apparaatsjabloon sinds het laatst geëxporteerde bericht. Informatie die in elk bericht of record wordt verzonden, omvat:

- `id`van de apparaatsjabloon `instanceOf` die overeenkomt met de stroom van apparaten hierboven
- `displayName`van de apparaatsjabloon
- Het `capabilityModel` apparaat `interfaces`met zijn - en de definities van telemetrie, eigenschappen en opdrachten
- `cloudProperties`Definities
- Overschrijft en initiële waarden, in lijn met de`capabilityModel`

Verwijderde apparaatsjablonen worden niet geëxporteerd. Momenteel zijn er geen indicatoren in geëxporteerde berichten voor verwijderde apparaatsjablonen.

Voor gebeurtenishubs en servicebus worden berichten met apparaatsjabloongegevens in bijna realtime verzonden naar uw gebeurtenishub of servicebuswachtrij of -onderwerp, zoals deze wordt weergegeven in IoT Central. 

Voor Blob Storage wordt een nieuwe momentopname met alle wijzigingen sinds de laatste geschreven keer per minuut geëxporteerd.

Dit is een voorbeeldbericht over gegevens over apparaatsjablonen in gebeurtenishub of servicebuswachtrij of -onderwerp:

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

Dit is een voorbeeldmomentopname met apparaten en eigenschappengegevens in Blob Storage. Geëxporteerde bestanden bevatten één regel per record.

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
## <a name="data-format-change-notice"></a>Melding van wijziging van gegevensindeling

> [!Note]
> De gegevensindeling van de telemetriestroom wordt niet beïnvloed door deze wijziging. Alleen de gegevensstromen van apparaten en apparaatsjablonen worden beïnvloed.

Als u een bestaande gegevensexport in uw preview-toepassing hebt met de streams *voor apparaten* en *apparaatsjablonen* ingeschakeld, moet u uw export v:v. **30 juni 2020 bijwerken.** Dit geldt voor export naar Azure Blob Storage, Azure Event Hubs en Azure Service Bus.

Vanaf 3 februari 2020 wordt in alle nieuwe exporten in toepassingen met apparaten en apparaatsjablonen de hierboven beschreven gegevensindeling beschreven. Alle exporten die hiervoor zijn gemaakt, blijven tot 30 juni 2020 in het oude gegevensformaat, waarna deze export automatisch wordt gemigreerd naar het nieuwe gegevensformaat. De nieuwe gegevensindeling komt overeen met de eigenschap van het [apparaat,](https://docs.microsoft.com/rest/api/iotcentral/devices/get)de [eigenschap van de apparaatwolk](https://docs.microsoft.com/rest/api/iotcentral/devices/getproperties)en de [device cloud property](https://docs.microsoft.com/rest/api/iotcentral/devices/getcloudproperties) [objectobjecten van de apparaatsjabloon](https://docs.microsoft.com/rest/api/iotcentral/devicetemplates/get) in de openbare IOT Central-API. 
 
Voor **apparaten**zijn opmerkelijke verschillen tussen de oude gegevensindeling en de nieuwe gegevensindeling:
- `@id`voor apparaat wordt `deviceId` verwijderd, wordt hernoemd naar`id` 
- `provisioned`vlag wordt toegevoegd om de inrichtingsstatus van het apparaat te beschrijven
- `approved`vlag wordt toegevoegd om de goedkeuringsstatus van het apparaat te beschrijven
- `properties`inclusief apparaat- en cloudeigenschappen, overeenkomsten met entiteiten in de openbare API

Voor **apparaatsjablonen**zijn opmerkelijke verschillen tussen de oude gegevensindeling en de nieuwe gegevensindeling:

- `@id`voor apparaatsjabloon wordt hernoemd naar`id`
- `@type`voor de apparaatsjabloon wordt `types`hernoemd naar , en is nu een array

### <a name="devices-format-deprecated-as-of-3-february-2020"></a>Apparaten (formaat afgeschaft vanaf 3 februari 2020)
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

### <a name="device-templates-format-deprecated-as-of-3-february-2020"></a>Apparaatsjablonen (indeling afgeschaft vanaf 3 februari 2020)
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

Nu u weet hoe u uw gegevens exporteren naar Azure Event Hubs, Azure Service Bus en Azure Blob Storage, gaat u verder met de volgende stap:

> [!div class="nextstepaction"]
> [Webhooks maken](./howto-create-webhooks.md)
