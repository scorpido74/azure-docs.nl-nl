---
title: Uitgangen en eindpunten - Azure Digital Twins | Microsoft Documenten
description: Meer informatie over het maken en uitplaatsen van gebeurteniseindpunten in Azure Digital Twins.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/21/2020
ms.openlocfilehash: 3803802a3d81655091d8be543ae9cb17221a98d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76511566"
---
# <a name="egress-and-endpoints-in-azure-digital-twins"></a>Uitgangen en eindpunten in Azure Digital Twins

Azure Digital *Twins-eindpunten* vertegenwoordigen een bericht- of gebeurtenismakelaar binnen het Azure-abonnement van een gebruiker. Gebeurtenissen en berichten kunnen worden verzonden naar azure-gebeurtenishubs, Azure Event Grid en Azure Service Bus-onderwerpen.

Gebeurtenissen worden doorgestuurd naar eindpunten volgens vooraf gedefinieerde routevoorkeuren. Gebruikers geven aan welke *gebeurtenistypen* elk eindpunt kan ontvangen.

Raadpleeg [gebeurtenissen en berichten routeren in Azure Digital Twins](./concepts-events-routing.md)voor meer informatie over gebeurtenissen, routering en gebeurtenistypen.

## <a name="events"></a>Gebeurtenissen

Gebeurtenissen worden verzonden door IoT-objecten (zoals apparaten en sensoren) voor verwerking door Azure-berichten- en gebeurtenismakelaars. Gebeurtenissen worden gedefinieerd door de volgende [overzichtsverwijzing azure event grid-gebeurtenisschema](../event-grid/event-schema.md).

```JSON
{
  "id": "00000000-0000-0000-0000-000000000000",
  "subject": "ExtendedPropertyKey",
  "data": {
    "SpacesToNotify": [
      "3a16d146-ca39-49ee-b803-17a18a12ba36"
    ],
    "Id": "00000000-0000-0000-0000-000000000000",
      "Type": "ExtendedPropertyKey",
    "AccessType": "Create"
  },
  "eventType": "TopologyOperation",
  "eventTime": "2018-04-17T17:41:54.9400177Z",
  "dataVersion": "1",
  "metadataVersion": "1",
  "topic": "/subscriptions/YOUR_TOPIC_NAME"
}
```

| Kenmerk | Type | Beschrijving |
| --- | --- | --- |
| id | tekenreeks | Unieke id voor de gebeurtenis. |
| Onderwerp | tekenreeks | Het door de uitgever gedefinieerde pad naar het gebeurtenisonderwerp. |
| data | object | Gebeurtenisgegevens die specifiek zijn voor de resourceprovider. |
| eventType | tekenreeks | Een van de geregistreerde gebeurtenistypen voor deze gebeurtenisbron. |
| eventTime | tekenreeks | De tijd dat de gebeurtenis wordt gegenereerd op basis van de UTC-tijd van de provider. |
| dataVersion | tekenreeks | De schemaversie van het gegevensobject. De uitgever definieert de schemaversie. |
| metadataVersion | tekenreeks | De schemaversie van de metagegevens van de gebeurtenis. Event Grid definieert het schema voor de eigenschappen op het hoogste niveau. Event Grid biedt deze waarde. |
| onderwerp | tekenreeks | Volledig resourcepad naar de gebeurtenisbron. Dit veld is niet schrijfbaar. Event Grid biedt deze waarde. |

Voor meer informatie over het gebeurtenisschema eventgrid:

- Controleer de [naslagverwijzing naar het gebeurtenisschema azure-gebeurtenisraster](../event-grid/event-schema.md).
- Lees de [referentie van Azure EventGrid Node.js SDK EventGridEvent](https://docs.microsoft.com/javascript/api/@azure/eventgrid/eventgridevent?view=azure-node-latest).

## <a name="event-types"></a>Gebeurtenistypen

Gebeurtenistypen classificeren de aard van de gebeurtenis en zijn ingesteld in het veld **eventType.** Beschikbare gebeurtenistypen worden gegeven door de volgende lijst:

- TopologieOperatie
- UdfCustom
- Sensorverandering
- Ruimtewijzigen
- DeviceMessage DeviceMessage

De gebeurtenisnotaties voor elk gebeurtenistype worden verder beschreven in de volgende subsecties.

### <a name="topologyoperation"></a>TopologieOperatie

**TopologieBewerking** is van toepassing op grafiekwijzigingen. De eigenschap **subject** geeft het type object aan dat wordt beïnvloed. De volgende typen objecten kunnen deze gebeurtenis activeren:

- Apparaat
- DeviceBlobMetadata
- DeviceExtendedProperty
- ExtendedPropertyKey ExtendedPropertyKey
- ExtendedType
- Sleutelbewaarplaats
- Rapport
- Roldefinitie
- Sensor
- SensorBlobMetadata
- SensorExtendedProperty
- Space
- SpaceBlobMetadata
- SpaceExtendedEigenschap
- SpaceResource
- SpaceRoleAssignment
- Systeem
- Gebruiker
- UserBlobMetadata
- UserExtendedProperty

#### <a name="example"></a>Voorbeeld

```JSON
{
  "id": "00000000-0000-0000-0000-000000000000",
  "subject": "ExtendedPropertyKey",
  "data": {
    "SpacesToNotify": [
      "3a16d146-ca39-49ee-b803-17a18a12ba36"
    ],
    "Id": "00000000-0000-0000-0000-000000000000",
      "Type": "ExtendedPropertyKey",
    "AccessType": "Create"
  },
  "eventType": "TopologyOperation",
  "eventTime": "2018-04-17T17:41:54.9400177Z",
  "dataVersion": "1",
  "metadataVersion": "1",
  "topic": "/subscriptions/YOUR_TOPIC_NAME"
}
```

| Waarde | Vervangen door |
| --- | --- |
| YOUR_TOPIC_NAME | De naam van uw aangepaste onderwerp |

### <a name="udfcustom"></a>UdfCustom

**UdfCustom** is een gebeurtenis die wordt verzonden door een door de gebruiker gedefinieerde functie (UDF).
  
> [!IMPORTANT]  
> Deze gebeurtenis moet expliciet worden verzonden vanuit de UDF zelf.

#### <a name="example"></a>Voorbeeld

```JSON
{
  "id": "568fd394-380b-46fa-925a-ebb96f658cce",
  "subject": "UdfCustom",
  "data": {
    "TopologyObjectId": "7c799bfc-1bff-4b9e-b15a-669933969d20",
    "ResourceType": "Space",
    "Payload": "\"Room is not available or air quality is poor\"",
    "CorrelationId": "568fd394-380b-46fa-925a-ebb96f658cce"
  },
  "eventType": "UdfCustom",
  "eventTime": "2018-10-02T06:50:15.198Z",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "topic": "/subscriptions/YOUR_TOPIC_NAME"
}
```

| Waarde | Vervangen door |
| --- | --- |
| YOUR_TOPIC_NAME | De naam van uw aangepaste onderwerp |

### <a name="sensorchange"></a>Sensorverandering

**SensorChange** is een update van de status van een sensor op basis van telemetriewijzigingen.

#### <a name="example"></a>Voorbeeld

```JSON
{
  "id": "60bf5336-2929-45b4-bb4c-b45699dfe95f",
  "subject": "SensorChange",
  "data": {
    "Type": "Classic",
    "DataType": "Motion",
    "Id": "60bf5336-2929-45b4-bb4c-b45699dfe95f",
    "Value": "False",
    "PreviousValue": "True",
    "EventTimestamp": "2018-04-17T17:46:15.4964262Z",
    "MessageType": "sensor",
    "Properties": {
      "ms-client-request-id": "c9e576b7-5eea-4f61-8617-92a57add5179",
      "ms-activity-id": "ct22YwXEGJ5u.605.0"
    }
  },
  "eventType": "SensorChange",
  "eventTime": "2018-04-17T17:46:18.5452993Z",
  "dataVersion": "1",
  "metadataVersion": "1",
  "topic": "/subscriptions/YOUR_TOPIC_NAME"
}
```

| Waarde | Vervangen door |
| --- | --- |
| YOUR_TOPIC_NAME | De naam van uw aangepaste onderwerp |

### <a name="spacechange"></a>Ruimtewijzigen

**SpaceChange** is een update van de status van een ruimte op basis van telemetriewijzigingen.

#### <a name="example"></a>Voorbeeld

```JSON
{
  "id": "42522e10-b1aa-42ff-a5e7-7181788ffc4b",
  "subject": "SpaceChange",
  "data": {
    "Type": null,
    "DataType": "AvailableAndFresh",
    "Id": "7c799bfc-1bff-4b9e-b15a-669933969d20",
    "Value": "Room is not available or air quality is poor",
    "PreviousValue": null,
    "RawData": null,
    "transactionId": null,
    "EventTimestamp": null,
    "MessageType": null,
    "Properties": null,
    "CorrelationId": "42522e10-b1aa-42ff-a5e7-7181788ffc4b"
  },
  "eventType": "SpaceChange",
  "eventTime": "2018-10-02T06:50:20.128Z",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "topic": "/subscriptions/YOUR_TOPIC_NAME"
}
```

| Waarde | Vervangen door |
| --- | --- |
| YOUR_TOPIC_NAME | De naam van uw aangepaste onderwerp |

### <a name="devicemessage"></a>DeviceMessage DeviceMessage

Met **DeviceMessage**u een **EventHub-verbinding** opgeven waarop ook raw-telemetriegebeurtenissen kunnen worden gerouterd vanuit Azure Digital Twins.

> [!NOTE]
> - **DeviceMessage** is alleen te combineren met **EventHub**. U **DeviceMessage** niet combineren met een van de andere gebeurtenistypen.
> - U slechts één eindpunt opgeven van de combinatie van type **EventHub** of **DeviceMessage**.

## <a name="configure-endpoints"></a>Eindpunten configureren

Endpoint-beheer wordt uitgeoefend via de API Endpoints.

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

In de volgende voorbeelden wordt uitgelegd hoe u de ondersteunde eindpunten configureert.

>[!IMPORTANT]
> Besteed zorgvuldig aandacht aan het kenmerk **eventTypes.** Het definieert welke gebeurtenistypen worden behandeld door het eindpunt en bepaalt dus de routering.

Een geverifieerd HTTP POST-verzoek tegen:

```URL
YOUR_MANAGEMENT_API_URL/endpoints
```

- Route naar servicebus-gebeurtenistypen **SensorChange,** **SpaceChange**en **TopologyOperation:**

  ```JSON
  {
    "type": "ServiceBus",
    "eventTypes": [
      "SensorChange",
      "SpaceChange",
      "TopologyOperation"
    ],
    "connectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_PRIMARY_KEY",
    "secondaryConnectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_SECONDARY_KEY",
    "path": "YOUR_TOPIC_NAME"
  }
  ```

    | Waarde | Vervangen door |
    | --- | --- |
    | YOUR_NAMESPACE | De naamruimte van uw eindpunt |
    | YOUR_PRIMARY_KEY | De primaire verbindingstekenreeks die wordt gebruikt om te verifiëren |
    | YOUR_SECONDARY_KEY | De secundaire verbindingstekenreeks die wordt gebruikt om te verifiëren |
    | YOUR_TOPIC_NAME | De naam van uw aangepaste onderwerp |

- Route naar gebeurtenistypen **SensorChange,** **SpaceChange**en **TopologyOperation:**

  ```JSON
  {
    "type": "EventGrid",
    "eventTypes": [
      "SensorChange",
      "SpaceChange",
      "TopologyOperation"
    ],
    "connectionString": "YOUR_PRIMARY_KEY",
    "secondaryConnectionString": "YOUR_SECONDARY_KEY",
    "path": "YOUR_TOPIC_NAME.westus-1.eventgrid.azure.net"
  }
  ```

    | Waarde | Vervangen door |
    | --- | --- |
    | YOUR_PRIMARY_KEY | De primaire verbindingstekenreeks die wordt gebruikt om te verifiëren|
    | YOUR_SECONDARY_KEY | De secundaire verbindingstekenreeks die wordt gebruikt om te verifiëren |
    | YOUR_TOPIC_NAME | De naam van uw aangepaste onderwerp |

- Route naar gebeurtenishubs- typen **SensorChange,** **SpaceChange**en **TopologyOperation:**

  ```JSON
  {
    "type": "EventHub",
    "eventTypes": [
      "SensorChange",
      "SpaceChange",
      "TopologyOperation"
    ],
    "connectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_PRIMARY_KEY",
    "secondaryConnectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_SECONDARY_KEY",
    "path": "YOUR_EVENT_HUB_NAME"
  }
  ```

    | Waarde | Vervangen door |
    | --- | --- |
    | YOUR_NAMESPACE | De naamruimte van uw eindpunt |
    | YOUR_PRIMARY_KEY | De primaire verbindingstekenreeks die wordt gebruikt om te verifiëren |
    | YOUR_SECONDARY_KEY | De secundaire verbindingstekenreeks die wordt gebruikt om te verifiëren |
    | YOUR_EVENT_HUB_NAME | De naam van uw gebeurtenishub |

- Route naar gebeurtenishubs, type **DeviceMessage**. De opname `EntityPath` in de **verbindingString** is verplicht:

  ```JSON
  {
    "type": "EventHub",
    "eventTypes": [
      "DeviceMessage"
    ],
    "connectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_PRIMARY_KEY;EntityPath=YOUR_EVENT_HUB_NAME",
    "secondaryConnectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_SECONDARY_KEY;EntityPath=YOUR_EVENT_HUB_NAME",
    "path": "YOUR_EVENT_HUB_NAME"
  }
  ```

    | Waarde | Vervangen door |
    | --- | --- |
    | YOUR_NAMESPACE | De naamruimte van uw eindpunt |
    | YOUR_PRIMARY_KEY | De primaire verbindingstekenreeks die wordt gebruikt om te verifiëren |
    | YOUR_SECONDARY_KEY | De secundaire verbindingstekenreeks die wordt gebruikt om te verifiëren |
    | YOUR_EVENT_HUB_NAME | De naam van uw gebeurtenishub |

> [!NOTE]  
> Bij het maken van een nieuw eindpunt kan het tot 5 tot 10 minuten duren voordat gebeurtenissen op het eindpunt worden ontvangen.

## <a name="primary-and-secondary-connection-keys"></a>Primaire en secundaire verbindingstoetsen

Wanneer een primaire verbindingssleutel onbevoegd wordt, probeert het systeem automatisch de secundaire verbindingssleutel. Dat biedt een back-up en maakt het mogelijk om de primaire sleutel op een elegante manier te verifiëren en bij te werken via de Endpoints API.

Als zowel de primaire als de secundaire verbindingssleutels ongeautoriseerd zijn, voert het systeem een exponentiële wachttijd voor back-off in van maximaal 30 minuten. Gebeurtenissen worden gedropt op elke geactiveerde back-off wachttijd.

Wanneer het systeem zich in een back-off wachtstatus bevindt, kan het tot 30 minuten duren voordat het bijwerken van verbindingssleutels via de Endpoints API van kracht wordt.

## <a name="unreachable-endpoints"></a>Onbereikbare eindpunten

Wanneer een eindpunt onbereikbaar wordt, voert het systeem een exponentiële back-off wachttijd van maximaal 30 minuten in. Gebeurtenissen worden gedropt op elke geactiveerde back-off wachttijd.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [het gebruik van Azure Digital Twins Swagger](how-to-use-swagger.md).

- Meer informatie over [het routeren van gebeurtenissen en berichten](concepts-events-routing.md) in Azure Digital Twins.
