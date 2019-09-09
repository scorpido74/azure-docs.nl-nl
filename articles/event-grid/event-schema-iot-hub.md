---
title: Azure Event Grid-schema voor IoT Hub | Microsoft Docs
description: Referentiepagina voor de schema-indeling voor gebeurtenissen en de eigenschappen van IoT Hub
services: iot-hub
documentationcenter: ''
author: kgremban
manager: timlt
editor: ''
ms.service: event-grid
ms.topic: reference
ms.date: 01/17/2019
ms.author: kgremban
ms.openlocfilehash: 4e96276a862844cea1d0800eafb952d4a0df97ab
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67076359"
---
# <a name="azure-event-grid-event-schema-for-iot-hub"></a>Azure Event Grid-gebeurtenisschema voor IoT-Hub

Dit artikel bevat de eigenschappen en het schema voor Azure IoT Hub-gebeurtenissen. Zie voor een inleiding tot gebeurtenisschema's, [Azure Event Grid-gebeurtenisschema](event-schema.md). 

Zie voor een lijst met zelfstudies en voorbeelden van scripts, [IoT Hub-gebeurtenisbron](event-sources.md#iot-hub).

## <a name="available-event-types"></a>Typen van de gebeurtenis berichten beschikbaar

Azure IoT Hub verzendt de volgende typen gebeurtenissen:

| Gebeurtenistype | Description |
| ---------- | ----------- |
| Microsoft.Devices.DeviceCreated | Gepubliceerd wanneer een apparaat is geregistreerd bij een IoT-hub. |
| Microsoft.Devices.DeviceDeleted | Wanneer een apparaat wordt verwijderd uit een IoT-hub gepubliceerd. | 
| Microsoft.Devices.DeviceConnected | Wanneer een apparaat is verbonden met een IoT-hub gepubliceerd. |
| Microsoft.Devices.DeviceDisconnected | Gepubliceerd wanneer een apparaat niet is verbonden met een IoT-hub. | 
| Microsoft.Devices.DeviceTelemetry | Wanneer een telemetrie-bericht is verzonden naar een IoT hub gepubliceerd. |

Alle apparaatgebeurtenissen, met uitzondering van apparaat telemetriegebeurtenissen zijn algemeen beschikbaar in alle regio's die worden ondersteund door Event Grid. Apparaat telemetrie gebeurtenis is in openbare preview en is beschikbaar in alle regio's behalve VS-Oost, VS-West, West-Europa, [Azure Government](../azure-government/documentation-government-welcome.md), [Azure China 21Vianet](/azure/china/china-welcome), en [Azure Duitsland](https://azure.microsoft.com/global-infrastructure/germany/).

## <a name="example-event"></a>Voorbeeld van de gebeurtenis

Het schema voor gebeurtenissen DeviceConnected en DeviceDisconnected hebben dezelfde structuur. Deze voorbeeldgebeurtenis ziet u het schema van een gebeurtenis treedt op wanneer een apparaat is verbonden met een IoT-hub:

```json
[{
  "id": "f6bbf8f4-d365-520d-a878-17bf7238abd8", 
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>", 
  "subject": "devices/LogicAppTestDevice", 
  "eventType": "Microsoft.Devices.DeviceConnected", 
  "eventTime": "2018-06-02T19:17:44.4383997Z", 
  "data": {
    "deviceConnectionStateEventInfo": {
      "sequenceNumber":
        "000000000000000001D4132452F67CE200000002000000000000000000000001"
    },
    "hubName": "egtesthub1",
    "deviceId": "LogicAppTestDevice",
    "moduleId" : "DeviceModuleID"
  }, 
  "dataVersion": "1", 
  "metadataVersion": "1" 
}]
```

De DeviceTelemetry-gebeurtenis wordt geactiveerd wanneer een gebeurtenis telemetrie wordt verzonden naar een IoT-Hub. Hieronder ziet u een voorbeeldschema voor deze gebeurtenis.

```json
[{
  "id": "9af86784-8d40-fe2g-8b2a-bab65e106785",
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>", 
  "subject": "devices/LogicAppTestDevice", 
  "eventType": "Microsoft.Devices.DeviceTelemetry",
  "eventTime": "2019-01-07T20:58:30.48Z",
  "data": {        
      "body": {            
          "Weather": {                
              "Temperature": 900            
          },
          "Location": "USA"        
      },
        "properties": {            
          "Status": "Active"        
        },
        "systemProperties": {            
            "iothub-content-type": "application/json",
            "iothub-content-encoding": "utf-8",
            "iothub-connection-device-id": "d1",
            "iothub-connection-auth-method": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
            "iothub-connection-auth-generation-id": "123455432199234570",
            "iothub-enqueuedtime": "2019-01-07T20:58:30.48Z",
            "iothub-message-source": "Telemetry"        
        }    
    },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

Het schema voor gebeurtenissen DeviceCreated en DeviceDeleted hebben dezelfde structuur. Deze voorbeeldgebeurtenis ziet u het schema van een gebeurtenis treedt op wanneer een apparaat is geregistreerd bij een IoT-hub:

```json
[{
  "id": "56afc886-767b-d359-d59e-0da7877166b2",
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
  "subject": "devices/LogicAppTestDevice",
  "eventType": "Microsoft.Devices.DeviceCreated",
  "eventTime": "2018-01-02T19:17:44.4383997Z",
  "data": {
    "twin": {
      "deviceId": "LogicAppTestDevice",
      "etag": "AAAAAAAAAAE=",
      "deviceEtag": "null",
      "status": "enabled",
      "statusUpdateTime": "0001-01-01T00:00:00",
      "connectionState": "Disconnected",
      "lastActivityTime": "0001-01-01T00:00:00",
      "cloudToDeviceMessageCount": 0,
      "authenticationType": "sas",
      "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
      },
      "version": 2,
      "properties": {
        "desired": {
          "$metadata": {
            "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
          },
          "$version": 1
        },
        "reported": {
          "$metadata": {
            "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
          },
          "$version": 1
        }
      }
    },
    "hubName": "egtesthub1",
    "deviceId": "LogicAppTestDevice"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="event-properties"></a>Eigenschappen van gebeurtenis

Alle gebeurtenissen bevatten de dezelfde gegevens op het hoogste niveau: 

| Eigenschap | Type | Description |
| -------- | ---- | ----------- |
| id | string | De unieke id voor de gebeurtenis. |
| topic | string | Volledige resource-pad naar de bron van de gebeurtenis. Dit veld is niet beschrijfbaar. Event Grid biedt deze waarde. |
| subject | string | Uitgever gedefinieerde pad naar het onderwerp van de gebeurtenis. |
| eventType | string | Een van de geregistreerde gebeurtenis-typen voor de bron van deze gebeurtenis. |
| eventTime | string | Het moment waarop dat de gebeurtenis is gegenereerd, is afhankelijk van de UTC-tijd van de provider. |
| data | object | Gebeurtenisgegevens van de IoT Hub.  |
| dataVersion | string | De schemaversie van het gegevensobject. De uitgever definieert de schemaversie. |
| metadataVersion | string | De schemaversie van de metagegevens van de gebeurtenis. Event Grid definieert het schema van de eigenschappen op het hoogste niveau. Event Grid biedt deze waarde. |

Het gegevensobject bevat voor alle gebeurtenissen van IoT Hub, de volgende eigenschappen:

| Eigenschap | Type | Description |
| -------- | ---- | ----------- |
| hubName | string | De naam van de IoT-Hub waar het apparaat is gemaakt of verwijderd. |
| deviceId | string | De unieke id van het apparaat. Deze hoofdlettergevoelige tekenreeks mag maximaal 128 tekens lang en ondersteunt de ASCII-7-bits alfanumerieke tekens, plus de volgende speciale tekens bevatten: `- : . + % _ # * ? ! ( ) , = @ ; $ '`. |

De inhoud van het gegevensobject zijn verschillend voor elke uitgever van gebeurtenissen. 

Voor **apparaat aangesloten** en **apparaat losgekoppeld** IoT Hub-gebeurtenissen, het gegevensobject bevat de volgende eigenschappen:

| Eigenschap | Type | Description |
| -------- | ---- | ----------- |
| moduleId | string | De unieke id van de module. Dit veld wordt uitgevoerd alleen voor apparaten van de module. Deze hoofdlettergevoelige tekenreeks mag maximaal 128 tekens lang en ondersteunt de ASCII-7-bits alfanumerieke tekens, plus de volgende speciale tekens bevatten: `- : . + % _ # * ? ! ( ) , = @ ; $ '`. |
| deviceConnectionStateEventInfo | object | Verbinding gebeurtenis informatie over de apparaatstatus
| sequenceNumber | string | Een getal waarmee de volgorde van het apparaat is verbonden of het apparaat geven gebeurtenissen verbroken. Laatste gebeurtenis heeft een volgnummer dat hoger is dan de vorige gebeurtenis. Dit nummer kan worden gewijzigd door meer dan 1, maar alleen toeneemt. Zie [over het gebruik van volgnummer](../iot-hub/iot-hub-how-to-order-connection-state-events.md). |

Voor **Apparaattelemetrie** IoT Hub-gebeurtenis, de gegevensobject bevat het bericht apparaat-naar-cloud in [IoT hub-berichtindeling](../iot-hub/iot-hub-devguide-messages-construct.md) en heeft de volgende eigenschappen:

| Eigenschap | Type | Description |
| -------- | ---- | ----------- |
| body | string | De inhoud van het bericht van het apparaat. |
| properties | string | Eigenschappen voor de toepassing zijn zelfgedefinieerde tekenreeksen die kunnen worden toegevoegd aan het bericht. Deze velden zijn optioneel. |
| system properties | string | [Systeemeigenschappen](../iot-hub/iot-hub-devguide-routing-query-syntax.md#system-properties) helpen bij het identificeren van de inhoud en de bron van de berichten. Apparaat telemetrie bericht moet een geldige JSON-indeling hebben met de contentType instellen in JSON en beperking contentEncoding ingesteld op UTF-8 in de eigenschappen van het bericht. Als dit niet is ingesteld, kunnen de IoT Hub wordt de berichten in de base 64 gecodeerde indeling schrijven.  |

Voor **apparaat gemaakt** en **apparaat verwijderd** IoT Hub-gebeurtenissen, het gegevensobject bevat de volgende eigenschappen:

| Eigenschap | Type | Description |
| -------- | ---- | ----------- |
| twin | object | Informatie over het dubbele apparaat, de cloud-weergave van de metagegevens van apparaten van toepassing is. | 
| deviceID | string | De unieke id van het dubbele apparaat. | 
| etag | string | Een validator om ervoor te zorgen consistentie van updates voor een apparaatdubbel. Elke etag is gegarandeerd uniek zijn per apparaatdubbel. |  
| deviceEtag| string | Een validator om ervoor te zorgen consistentie van updates voor een apparaatregister. Elke deviceEtag is gegarandeerd uniek per apparaatregister. |
| status | string | Of het dubbele apparaat is ingeschakeld of uitgeschakeld. | 
| statusUpdateTime | string | De ISO8601-timestamp van de laatste status van het dubbele apparaat bijwerken. |
| connectionState | string | Of het apparaat is verbonden of niet verbonden. | 
| lastActivityTime | string | De ISO8601-timestamp van de laatste activiteit. | 
| cloudToDeviceMessageCount | geheel getal | Telling van cloud naar apparaat-berichten verzonden naar dit apparaat. | 
| authenticationType | string | Verificatietype dat wordt gebruikt voor dit apparaat: beide `SAS`, `SelfSigned`, of `CertificateAuthority`. |
| x509Thumbprint | string | De vingerafdruk is een unieke waarde voor de x509 certificaat, meestal gebruikt voor een bepaald certificaat niet vinden in een certificaatarchief. De vingerafdruk wordt dynamisch gegenereerd met behulp van het SHA1-algoritme en bestaat niet fysiek in het certificaat. | 
| primaryThumbprint | string | Primaire vingerafdruk voor de x509 certificaat. |
| secondaryThumbprint | string | Secundaire vingerafdruk voor de x509 certificaat. | 
| version | geheel getal | Een geheel getal dat wordt verhoogd door één voor elke keer dat het apparaat dubbele wordt bijgewerkt. |
| desired | object | Een gedeelte van de eigenschappen die kunnen worden alleen door de back-endtoepassing geschreven en gelezen door het apparaat. | 
| reported | object | Een gedeelte van de eigenschappen die kunnen worden alleen door het apparaat geschreven en gelezen door back-end van de toepassing. |
| lastUpdated | string | De ISO8601-timestamp van de laatste dubbele eigenschap bijwerken. | 

## <a name="next-steps"></a>Volgende stappen

* Zie voor een inleiding tot Azure Event Grid, [wat is Event Grid?](overview.md)
* Zie voor meer informatie over hoe IoT Hub en Event Grid samenwerken, [reageren op gebeurtenissen van IoT-Hub met behulp van Event Grid om acties starten](../iot-hub/iot-hub-event-grid.md).