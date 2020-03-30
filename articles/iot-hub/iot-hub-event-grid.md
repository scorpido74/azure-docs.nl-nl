---
title: Azure IoT-hub en gebeurtenisraster | Microsoft Documenten
description: Gebruik Azure Event Grid om processen te activeren op basis van acties die plaatsvinden in IoT Hub.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: robinsh
ms.openlocfilehash: a1fd99ee595c4ae91ccd06aa41fa421ca8fcc074
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284549"
---
# <a name="react-to-iot-hub-events-by-using-event-grid-to-trigger-actions"></a>Reageren op IoT Hub-gebeurtenissen door gebeurtenisraster te gebruiken om acties te activeren

Azure IoT Hub integreert met Azure Event Grid, zodat u gebeurtenismeldingen naar andere services verzenden en downstreamprocessen activeren. Configureer uw bedrijfstoepassingen om te luisteren naar IoT Hub-gebeurtenissen, zodat u op een betrouwbare, schaalbare en veilige manier reageren op kritieke gebeurtenissen.Bouw bijvoorbeeld een toepassing die een database bijwerkt, een werkticket maakt en elke keer een e-mailmelding levert wanneer een nieuw IoT-apparaat is geregistreerd op uw IoT-hub.

[Azure Event Grid](../event-grid/overview.md) is een volledig beheerde gebeurtenisrouteringsservice die gebruik maakt van een model voor het abonneren op publiceren. Event Grid heeft ingebouwde ondersteuning voor Azure-services zoals [Azure-functies](../azure-functions/functions-overview.md) en [Azure Logic Apps](../logic-apps/logic-apps-what-are-logic-apps.md)en kan gebeurteniswaarschuwingen leveren aan niet-Azure-services met behulp van webhooks. Zie [Een inleiding tot Azure Event Grid](../event-grid/overview.md)voor een volledige lijst met de gebeurtenishandlers die Event Grid ondersteunt.

![Azure Event Grid-architectuur](./media/iot-hub-event-grid/event-grid-functional-model.png)

## <a name="regional-availability"></a>Regionale beschikbaarheid

De integratie van eventgrid is beschikbaar voor IoT-hubs in de regio's waar Event Grid wordt ondersteund. Zie Een inleiding tot [Azure Event Grid](../event-grid/overview.md)voor de laatste lijst met regio's .

## <a name="event-types"></a>Gebeurtenistypen

IoT Hub publiceert de volgende gebeurtenistypen:

| Gebeurtenistype | Beschrijving |
| ---------- | ----------- |
| Microsoft.Devices.DeviceGemaakt | Gepubliceerd wanneer een apparaat is geregistreerd op een IoT-hub. |
| Microsoft.Devices.DeviceVerwijderd | Gepubliceerd wanneer een apparaat wordt verwijderd uit een IoT-hub. |
| Microsoft.Devices.DeviceConnected | Gepubliceerd wanneer een apparaat is verbonden met een IoT-hub. |
| Microsoft.Devices.DeviceDisconnected | Gepubliceerd wanneer een apparaat is losgekoppeld van een IoT-hub. |
| Microsoft.Devices.DeviceTelemetry | Gepubliceerd wanneer een telemetriebericht van een apparaat naar een IoT-hub wordt verzonden |

Gebruik de Azure-portal of Azure CLI om te configureren welke gebeurtenissen vanaf elke IoT-hub moeten worden gepubliceerd. Probeer bijvoorbeeld de zelfstudie [E-mailmeldingen verzenden over Azure IoT Hub-gebeurtenissen met Logic Apps](../event-grid/publish-iot-hub-events-to-logic-apps.md).

## <a name="event-schema"></a>Gebeurtenisschema

IoT Hub-gebeurtenissen bevatten alle informatie die u nodig hebt om te reageren op wijzigingen in de levenscyclus van uw apparaat. U een IoT Hub-gebeurtenis identificeren door te controleren of de eigenschap eventType begint met **Microsoft.Devices**. For more information about how to use Event Grid event properties, see the [Event Grid event schema](../event-grid/event-schema.md).

### <a name="device-connected-schema"></a>Apparaat verbonden schema

In het volgende voorbeeld wordt het schema van een apparaatverbonden gebeurtenis weergegeven:

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
    "moduleId" : "DeviceModuleID",
  }, 
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="device-telemetry-schema"></a>Apparaattelemetrieschema

Apparaattelemetriebericht moet in een geldige JSON-indeling staan met de contentType ingesteld op **toepassing/json** en contentEncoding ingesteld op **UTF-8** in de eigenschappen van het [berichtensysteem](iot-hub-devguide-routing-query-syntax.md#system-properties). Beide eigenschappen zijn geval ongevoelig. Als de inhoudscodering niet is ingesteld, schrijft IoT Hub de berichten in de basis64 gecodeerde indeling.

U apparaattelemetriegebeurtenissen verrijken voordat ze worden gepubliceerd in gebeurtenisraster door het eindpunt te selecteren als gebeurtenisraster. Zie [Overzicht van berichtverrijkingen](iot-hub-message-enrichments-overview.md)voor meer informatie .

In het volgende voorbeeld wordt het schema van een telemetriegebeurtenis voor het apparaat weergegeven:

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

### <a name="device-created-schema"></a>Apparaat gemaakt schema

In het volgende voorbeeld wordt het schema van een apparaat weergegeven dat is gemaakt:

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
      "deviceEtag":"null",
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

Zie [Azure Event Grid-gebeurtenisschema voor IoT Hub voor](../event-grid/event-schema-iot-hub.md)een gedetailleerde beschrijving van elke eigenschap.

## <a name="filter-events"></a>Gebeurtenissen filteren

IoT Hub-gebeurtenisabonnementen kunnen gebeurtenissen filteren op basis van gebeurtenistype, gegevensinhoud en onderwerp, de naam van het apparaat.

Event Grid maakt [filteren](../event-grid/event-filtering.md) op gebeurtenistypen, onderwerpen en gegevensinhoud mogelijk. Tijdens het maken van het Abonnement op het Event Grid u zich abonneren op geselecteerde IoT-gebeurtenissen. Onderwerpfilters in gebeurtenisrasterwerk op basis van begin-met-voorvoegsels en **overeenkomsten met** (achtervoegsel). **Begins With** Het filter `AND` maakt gebruik van een operator, zodat gebeurtenissen met een onderwerp dat zowel het voorvoegsel als het achtervoegsel overeenkomen, aan de abonnee worden geleverd.

Het onderwerp IoT-gebeurtenissen maakt gebruik van de indeling:

```json
devices/{deviceId}
```

Event Grid maakt het ook mogelijk om te filteren op kenmerken van elke gebeurtenis, inclusief de gegevensinhoud. Hiermee u kiezen welke gebeurtenissen worden geleverd op basis van de inhoud van het telemetriebericht. Zie [geavanceerd filteren](../event-grid/event-filtering.md#advanced-filtering) om voorbeelden weer te geven. Als u wilt filteren op de telemetrieberichttekst, moet u de inhoudTypen instellen op **toepassing/json** en contentEncoding op **UTF-8** in de eigenschappen van het [berichtensysteem](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax#system-properties). Beide eigenschappen zijn geval ongevoelig.

Voor niet-telemetriegebeurtenissen zoals DeviceConnected, DeviceDisconnected, DeviceCreated en DeviceDeleted kan de gebeurtenisrasterfiltering worden gebruikt bij het maken van het abonnement. Voor telemetriegebeurtenissen kunnen gebruikers naast het filteren in gebeurtenisraster ook filteren op apparaattweelingen, berichteigenschappen en hoofdtekst via de routeringsquery voor berichten. 

Wanneer u zich abonneert op telemetriegebeurtenissen via gebeurtenisraster, maakt IoT Hub een standaardberichtroute om apparaatberichten van gegevensbrontypen naar gebeurtenisraster te verzenden. Zie [IoT Hub-berichtroutering](iot-hub-devguide-messages-d2c.md)voor meer informatie over het routeren van berichten. Deze route is zichtbaar in de portal onder IoT Hub > Message Routing. Er wordt slechts één route naar gebeurtenisraster gemaakt, ongeacht het aantal EG-abonnementen dat is gemaakt voor telemetriegebeurtenissen. Als u dus meerdere abonnementen met verschillende filters nodig hebt, u de OPERATOR OR gebruiken in deze query's op dezelfde route. Het maken en verwijderen van de route wordt geregeld door middel van een abonnement op telemetriegebeurtenissen via Event Grid. U een route naar gebeurtenisraster niet maken of verwijderen met behulp van IoT Hub Message Routing.

Als u berichten wilt filteren voordat telemetriegegevens worden verzonden, u uw [routeringsquery bijwerken.](iot-hub-devguide-routing-query-syntax.md) Houd er rekening mee dat routeringsquery alleen op de berichttekst kan worden toegepast als de hoofdtekst JSON is. U moet de contentType ook instellen op **toepassing/json** en contentEncoding op **UTF-8** in de eigenschappen van het [berichtensysteem.](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax#system-properties)

## <a name="limitations-for-device-connected-and-device-disconnected-events"></a>Beperkingen voor gebeurtenissen bij verbinden van apparaat en verbreken van verbinding van apparaat

Als u de statusgebeurtenissen van de apparaatverbinding wilt ontvangen, moet een apparaat een 'D2C Send Telemetry' OF een 'C2D Receive Message'-bewerking uitvoeren met Iot Hub. Houd er echter rekening mee dat als een apparaat het AMQP-protocol gebruikt om verbinding te maken met Iot Hub, het wordt aanbevolen dat ze een 'C2D Receive Message'-bewerking uitvoeren, anders kunnen hun meldingen van de verbindingsstatus enkele minuten worden vertraagd. Als uw apparaat het MQTT-protocol gebruikt, houdt IoT Hub de C2D-koppeling open. Voor AMQP u de C2D-koppeling openen door de [Async-API ontvangen](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.receiveasync?view=azure-dotnet), voor IoT Hub C# SDK of [apparaatclient voor AMQP aan](iot-hub-amqp-support.md#device-client)te roepen.

De D2C-koppeling is geopend als u telemetrie verzendt. 

Als de apparaatverbinding flikkert, wat betekent dat het apparaat regelmatig verbinding maakt en de verbinding verbreekt, sturen we niet elke verbindingsstatus, maar publiceren we de huidige verbindingsstatus die wordt genomen bij een periodieke momentopname, totdat het flikkeren doorgaat. Het ontvangen van dezelfde verbindingsstatusgebeurtenis met verschillende volgnummers of verschillende verbindingsstatusgebeurtenissen betekent dat er een wijziging is opgekomen in de status van de apparaatverbinding.

## <a name="tips-for-consuming-events"></a>Tips voor het consumeren van gebeurtenissen

Toepassingen die IoT Hub-gebeurtenissen verwerken, moeten de volgende aanbevolen praktijken volgen:

* Meerdere abonnementen kunnen worden geconfigureerd om gebeurtenissen naar dezelfde gebeurtenishandler te leiden, dus ga er niet van uit dat gebeurtenissen van een bepaalde bron zijn. Controleer altijd het berichtonderwerp om ervoor te zorgen dat het afkomstig is van de IoT-hub die u verwacht.

* Ga er niet van uit dat alle gebeurtenissen die u ontvangt de typen zijn die u verwacht. Controleer altijd het eventType voordat u het bericht verwerkt.

* Berichten kunnen buiten de orde komen of na een vertraging. Gebruik het veld etag om te begrijpen of uw informatie over objecten up-to-date is voor het apparaat dat is gemaakt of dat apparaten zijn verwijderd.

## <a name="next-steps"></a>Volgende stappen

* [Probeer de zelfstudie gebeurtenissen van iot-hub](../event-grid/publish-iot-hub-events-to-logic-apps.md)

* [Informatie over het rangschikken van gebeurtenissen ‘apparaat verbonden’ en ‘verbinding met apparaat verbroken’.](iot-hub-how-to-order-connection-state-events.md)

* [Meer informatie over gebeurtenisraster](../event-grid/overview.md)

* [Vergelijk de verschillen tussen het routeren van IoT Hub-gebeurtenissen en berichten](iot-hub-event-grid-routing-comparison.md)

* [Meer informatie over het gebruik van IoT-telemetriegebeurtenissen om Ruimtelijke Analyses van IoT te implementeren met Azure Maps](../azure-maps/tutorial-iot-hub-maps.md#create-an-azure-function-and-add-an-event-grid-subscription)
