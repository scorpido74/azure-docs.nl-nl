---
title: Azure Event Grid-schema voor IoT-hub | Microsoft Documenten
description: In dit artikel vindt u de eigenschappen en het schema voor Azure IoT Hub-gebeurtenissen. Hierworden de beschikbare gebeurtenistypen, een voorbeeldgebeurtenis en gebeurtenis-eigenschappen weergegeven.
services: iot-hub
documentationcenter: ''
author: kgremban
manager: timlt
editor: ''
ms.service: event-grid
ms.topic: reference
ms.date: 01/21/2020
ms.author: kgremban
ms.openlocfilehash: cfbd46ad961bd1dc914bae98e761cd83d445ff88
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76513028"
---
# <a name="azure-event-grid-event-schema-for-iot-hub"></a>Azure Event Grid-gebeurtenisschema voor IoT-hub

In dit artikel vindt u de eigenschappen en het schema voor Azure IoT Hub-gebeurtenissen. Zie Azure Event Grid-gebeurtenisschema voor een inleiding tot gebeurtenisschema ['Azure Event Grid'.](event-schema.md) 

Zie [IoT Hub-gebeurtenisbron](event-sources.md#iot-hub)voor een lijst met voorbeeldscripts en -zelfstudies.

## <a name="available-event-types"></a>Beschikbare gebeurtenistypen

Azure IoT Hub zendt de volgende gebeurtenistypen uit:

| Gebeurtenistype | Beschrijving |
| ---------- | ----------- |
| Microsoft.Devices.DeviceGemaakt | Gepubliceerd wanneer een apparaat is geregistreerd op een IoT-hub. |
| Microsoft.Devices.DeviceVerwijderd | Gepubliceerd wanneer een apparaat wordt verwijderd uit een IoT-hub. | 
| Microsoft.Devices.DeviceConnected | Gepubliceerd wanneer een apparaat is verbonden met een IoT-hub. |
| Microsoft.Devices.DeviceDisconnected | Gepubliceerd wanneer een apparaat is losgekoppeld van een IoT-hub. | 
| Microsoft.Devices.DeviceTelemetry | Gepubliceerd wanneer een telemetriebericht naar een IoT-hub wordt verzonden. |

Alle apparaatgebeurtenissen, behalve telemetriegebeurtenissen van het apparaat, zijn over het algemeen beschikbaar in alle regio's die worden ondersteund door gebeurtenisraster. De gebeurtenis Telemetrie van het apparaat is in openbare preview en is beschikbaar in alle regio's behalve Oost-VS, West-VS, West-Europa, [Azure Government](../azure-government/documentation-government-welcome.md), Azure [China 21Vianet](/azure/china/china-welcome)en [Azure Germany](https://azure.microsoft.com/global-infrastructure/germany/).

## <a name="example-event"></a>Voorbeeldgebeurtenis

Het schema voor deviceconnected- en devicedisconnected-gebeurtenissen hebben dezelfde structuur. In deze voorbeeldgebeurtenis wordt het schema weergegeven van een gebeurtenis die wordt verhoogd wanneer een apparaat is verbonden met een IoT-hub:

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

De gebeurtenis DeviceTelemetry wordt verhoogd wanneer een telemetriegebeurtenis naar een IoT-hub wordt verzonden. Hieronder ziet u een voorbeeldschema voor deze gebeurtenis.

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

Het schema voor devicecreated en DeviceDeleted-gebeurtenissen hebben dezelfde structuur. In deze voorbeeldgebeurtenis wordt het schema weergegeven van een gebeurtenis die wordt opgehaald wanneer een apparaat is geregistreerd op een IoT-hub:

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

### <a name="event-properties"></a>Gebeurtenis-eigenschappen

Alle gebeurtenissen bevatten dezelfde gegevens op het hoogste niveau: 

| Eigenschap | Type | Beschrijving |
| -------- | ---- | ----------- |
| id | tekenreeks | Unieke id voor de gebeurtenis. |
| onderwerp | tekenreeks | Volledig resourcepad naar de gebeurtenisbron. Dit veld is niet schrijfbaar. Event Grid biedt deze waarde. |
| Onderwerp | tekenreeks | Het door de uitgever gedefinieerde pad naar het gebeurtenisonderwerp. |
| eventType | tekenreeks | Een van de geregistreerde gebeurtenistypen voor deze gebeurtenisbron. |
| eventTime | tekenreeks | De tijd dat de gebeurtenis wordt gegenereerd op basis van de UTC-tijd van de provider. |
| data | object | IoT Hub-gebeurtenisgegevens.  |
| dataVersion | tekenreeks | De schemaversie van het gegevensobject. De uitgever definieert de schemaversie. |
| metadataVersion | tekenreeks | De schemaversie van de metagegevens van de gebeurtenis. Event Grid definieert het schema voor de eigenschappen op het hoogste niveau. Event Grid biedt deze waarde. |

Voor alle IoT Hub-gebeurtenissen bevat het gegevensobject de volgende eigenschappen:

| Eigenschap | Type | Beschrijving |
| -------- | ---- | ----------- |
| hubNaam | tekenreeks | Naam van de IoT-hub waar het apparaat is gemaakt of verwijderd. |
| deviceId | tekenreeks | De unieke id van het apparaat. Deze hoofdlettergevoelige tekenreeks kan maximaal 128 tekens lang zijn en ondersteunt ASCII 7-bits `- : . + % _ # * ? ! ( ) , = @ ; $ '`alfanumerieke tekens plus de volgende speciale tekens: . |

De inhoud van het gegevensobject verschilt per gebeurtenis-uitgever. 

Voor **apparaten die zijn verbonden** en met apparaat **losgekoppelde** IoT-hubgebeurtenissen, bevat het gegevensobject de volgende eigenschappen:

| Eigenschap | Type | Beschrijving |
| -------- | ---- | ----------- |
| moduleId | tekenreeks | De unieke id van de module. Dit veld is alleen uitvoer voor moduleapparaten. Deze hoofdlettergevoelige tekenreeks kan maximaal 128 tekens lang zijn en ondersteunt ASCII 7-bits `- : . + % _ # * ? ! ( ) , = @ ; $ '`alfanumerieke tekens plus de volgende speciale tekens: . |
| deviceConnectionStateEventInfo | object | Gebeurtenisgegevens van de status van apparaatverbinding
| volgordeAantal | tekenreeks | Een nummer waarmee de volgorde van de aangesloten apparaten of de verbinding tussen apparaten en de verbinding tussen apparaten wordt aangegeven. Laatste gebeurtenis heeft een volgnummer dat hoger is dan de vorige gebeurtenis. Dit aantal kan met meer dan 1 veranderen, maar neemt strikt toe. Bekijk [hoe u het volgnummer gebruikt](../iot-hub/iot-hub-how-to-order-connection-state-events.md). |

Voor de gebeurtenis IoT Hub **voor apparaattelemetrie** bevat het gegevensobject het apparaat-naar-cloudbericht in [de IoT-hubberichtindeling](../iot-hub/iot-hub-devguide-messages-construct.md) en heeft het de volgende eigenschappen:

| Eigenschap | Type | Beschrijving |
| -------- | ---- | ----------- |
| body | tekenreeks | De inhoud van het bericht van het apparaat. |
| properties | tekenreeks | Toepassingseigenschappen zijn door de gebruiker gedefinieerde tekenreeksen die aan het bericht kunnen worden toegevoegd. Deze velden zijn optioneel. |
| systeemeigenschappen | tekenreeks | [Systeemeigenschappen](../iot-hub/iot-hub-devguide-routing-query-syntax.md#system-properties) helpen bij het identificeren van de inhoud en de bron van de berichten. Apparaattelemetriebericht moet in een geldige JSON-indeling staan met de inhoudType ingesteld op JSON en contentEncoding ingesteld op UTF-8 in de eigenschappen van het berichtensysteem. Als dit niet is ingesteld, zal IoT Hub de berichten in basis 64 gecodeerde indeling schrijven.  |

Voor **door apparaten gemaakte** en door apparaten **verwijderde** IoT-hubgebeurtenissen bevat het gegevensobject de volgende eigenschappen:

| Eigenschap | Type | Beschrijving |
| -------- | ---- | ----------- |
| Twin | object | Informatie over de apparaattweeling, de cloudweergave van metagegevens van toepassingsapparaten. | 
| deviceID | tekenreeks | De unieke id van de apparaattweeling. | 
| etag etag | tekenreeks | Een validator voor het waarborgen van consistentie van updates voor een apparaattweeling. Elke etag is gegarandeerd uniek per apparaat twin. |  
| deviceEtag deviceEtag| tekenreeks | Een validator voor het waarborgen van consistentie van updates voor een apparaatregister. Elk apparaatEtag is gegarandeerd uniek per apparaatregister. |
| status | tekenreeks | Of de apparaattweeling is ingeschakeld of uitgeschakeld. | 
| statusUpdateTime | tekenreeks | De ISO8601-tijdstempel van de laatste apparaattwinstatusupdate. |
| verbindingStaat | tekenreeks | Of het apparaat is aangesloten of losgekoppeld. | 
| laatsteActivityTime | tekenreeks | De ISO8601-tijdstempel van de laatste activiteit. | 
| cloudToDeviceMessageCount | geheel getal | Aantal cloud- en apparaatberichten die naar dit apparaat worden verzonden. | 
| authenticationType | tekenreeks | Verificatietype dat voor dit `SAS` `SelfSigned`apparaat `CertificateAuthority`wordt gebruikt: , of . |
| x509Duimafdruk | tekenreeks | De duimafdruk is een unieke waarde voor het x509-certificaat, dat vaak wordt gebruikt om een bepaald certificaat in een certificaatarchief te vinden. De duimafdruk wordt dynamisch gegenereerd met behulp van het SHA1-algoritme en bestaat fysiek niet in het certificaat. | 
| primaireDuimafdruk | tekenreeks | Primaire duimafdruk voor het x509-certificaat. |
| secundairDuimafdruk | tekenreeks | Secundaire duimafdruk voor het x509-certificaat. | 
| versie | geheel getal | Een geheel getal dat telkens met één wordt verhoogd wanneer de apparaattweeling wordt bijgewerkt. |
| Gewenste | object | Een deel van de eigenschappen dat alleen kan worden geschreven door de back-end van de toepassing en door het apparaat kan worden gelezen. | 
| Gemeld | object | Een deel van de eigenschappen die alleen door het apparaat kunnen worden geschreven en gelezen door de back-end van de toepassing. |
| lastUpdated | tekenreeks | De ISO8601-tijdstempel van de laatste apparaattwin-propertyupdate. | 

## <a name="next-steps"></a>Volgende stappen

* Zie [Wat is gebeurtenisraster voor](overview.md) een inleiding tot Azure Event Grid?
* Zie [Reageren op IoT Hub-gebeurtenissen door Gebeurtenisraster te gebruiken om acties te activeren voor](../iot-hub/iot-hub-event-grid.md)meer informatie over hoe IoT Hub en Event Grid samenwerken.