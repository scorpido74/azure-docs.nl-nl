---
title: Ontwikkelaars handleiding-IoT Plug en Play preview | Microsoft Docs
description: Beschrijving van IoT-Plug en Play voor ontwikkel aars
author: rido-min
ms.author: rmpablos
ms.date: 07/16/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: ef221ea068f2786a4a84f20a29e80dd7176f06c6
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87337412"
---
# <a name="iot-plug-and-play-preview-developer-guide"></a>Ontwikkelaars handleiding voor IoT Plug en Play

Met IoT Plug en Play preview kunt u slimme apparaten bouwen die hun mogelijkheden adverteren voor Azure IoT-toepassingen. IoT Plug en Play-apparaten hoeven niet hand matig te worden geconfigureerd wanneer een klant deze verbindt met IoT-Plug en Play toepassingen.

In deze hand leiding worden de basis stappen beschreven die nodig zijn om een apparaat te maken dat volgt op de [IoT Plug en Play-conventies](concepts-convention.md)en de beschik bare rest api's die u kunt gebruiken om met het apparaat te communiceren.

Voer de volgende stappen uit om een IoT Plug en Play-apparaat te bouwen:

1. Zorg ervoor dat het apparaat gebruikmaakt van het MQTT-of MQTT over websockets-protocol om verbinding te maken met Azure IoT Hub.
1. Maak een [DTDL-model (Digital Apparaatdubbels Definition Language)](https://github.com/Azure/opendigitaltwins-dtdl) om uw apparaat te beschrijven. Zie [onderdelen begrijpen in IoT Plug en Play-modellen](concepts-components.md)voor meer informatie.
1. Werk uw apparaat bij om het te kunnen aankondigen `model-id` als onderdeel van de verbinding met het apparaat.
1. Telemetrie, eigenschappen en opdrachten implementeren met behulp van de [IoT Plug en Play-conventies](concepts-convention.md)

Nadat de implementatie van het apparaat is voltooid, gebruikt u [Azure IOT Explorer](howto-use-iot-explorer.md) om te controleren of het apparaat de IOT Plug en Play-conventies volgt.

> [!Tip]
> Alle code fragmenten in dit artikel maken gebruik van C#, maar de concepten zijn van toepassing op een van de beschik bare Sdk's voor C, Python, node en Java.

## <a name="model-id-announcement"></a>Aankondiging van model-ID

Als u de model-ID wilt aankondigen, moet het apparaat deze in de verbindings informatie bevatten:

```csharp
DeviceClient.CreateFromConnectionString(
  connectionString,
  TransportType.Mqtt,
  new ClientOptions() { ModelId = modelId })
```

De nieuwe `ClientOptions` overbelasting is beschikbaar in alle `DeviceClient` methoden die worden gebruikt voor het initialiseren van een verbinding.

De aankondiging van de model-ID is toegevoegd aan de volgende versies van de Sdk's

|SDK|Versie|
|---|-------|
|C-SDK|1.3.9|
|.NET|1.27.0|
|Java|1.14.0|
|Knooppunt|1.17.0|
|Python|2.1.4|

## <a name="implement-telemetry-properties-and-commands"></a>Telemetrie, eigenschappen en opdrachten implementeren

Zoals beschreven in [inzicht in onderdelen in IoT Plug en Play-modellen](concepts-components.md), moeten apparaten kunnen bepalen of ze onderdelen willen gebruiken om hun apparaten te beschrijven. Bij het gebruik van onderdelen moeten apparaten voldoen aan de regels die in deze sectie worden beschreven.

### <a name="telemetry"></a>Telemetrie

Voor modellen zonder onderdelen is geen speciale eigenschap vereist.

Bij het gebruik van onderdelen moeten apparaten een bericht eigenschap met de naam van het onderdeel instellen:

```c#
public async Task SendComponentTelemetryValueAsync(string componentName, string serializedTelemetry)
{
  var message = new Message(Encoding.UTF8.GetBytes(serializedTelemetry));
  message.Properties.Add("$.sub", componentName);
  message.ContentType = "application/json";
  message.ContentEncoding = "utf-8";
  await deviceClient.SendEventAsync(message);
}
```

### <a name="read-only-properties"></a>Alleen-lezen eigenschappen

Voor modellen zonder onderdelen is geen speciale construct vereist:

```csharp
TwinCollection reportedProperties = new TwinCollection();
reportedProperties["maxTemperature"] = 38.7;
await client.UpdateReportedPropertiesAsync(reportedProperties);
```

Het dubbele apparaat wordt bijgewerkt met de volgende gerapporteerde eigenschap:

```json
{
  "reported": {
      "maxTemperature" : 38.7
  }
}
```

Bij het gebruik van componenten moeten eigenschappen worden gemaakt binnen de naam van het onderdeel:

```csharp
TwinCollection reportedProperties = new TwinCollection();
TwinCollection component = new TwinCollection();
component["maxTemperature"] = 38.7;
component["__t"] = "c"; // marker to identify a component
reportedProperties["thermostat1"] = component;
await client.UpdateReportedPropertiesAsync(reportedProperties);
```

Het dubbele apparaat wordt bijgewerkt met de volgende gerapporteerde eigenschap:

```json
{
  "reported": {
    "thermostat1" : {  
      "__t" : "c",  
      "maxTemperature" : 38.7
     } 
  }
}
```

### <a name="writable-properties"></a>Beschrijf bare eigenschappen

Deze eigenschappen kunnen worden ingesteld door het apparaat of worden bijgewerkt door de oplossing. Als met de oplossing een eigenschap wordt bijgewerkt, ontvangt de client een melding als een retour aanroep in de `DeviceClient` . Als u de IoT Plug en Play-conventies wilt volgen, moet het apparaat de service op de hoogte stellen dat de eigenschap is ontvangen.

#### <a name="report-a-writable-property"></a>Een schrijf bare eigenschap rapporteren

Wanneer een apparaat een Beschrijf bare eigenschap rapporteert, moet dit de waarden bevatten die zijn `ack` gedefinieerd in de conventies.

Een schrijf bare eigenschap zonder onderdelen rapporteren:

```csharp
TwinCollection reportedProperties = new TwinCollection();
TwinCollection ackProps = new TwinCollection();
ackProps["value"] = 23.2;
ackProps["ac"] = 200; // using HTTP status codes
ackProps["av"] = 0; // not readed from a desired property
ackProps["ad"] = "reported default value";
reportedProperties["targetTemperature"] = ackProps;
await client.UpdateReportedPropertiesAsync(reportedProperties);
```

Het dubbele apparaat wordt bijgewerkt met de volgende gerapporteerde eigenschap:

```json
{
  "reported": {
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "complete"
      }
  }
}
```

Voor het rapporteren van een Beschrijf bare eigenschap van een component moet de dubbele markering een Markeer teken bevatten:

```csharp
TwinCollection reportedProperties = new TwinCollection();
TwinCollection component = new TwinCollection();
TwinCollection ackProps = new TwinCollection();
component["__t"] = "c"; // marker to identify a component
ackProps["value"] = 23.2;
ackProps["ac"] = 200; // using HTTP status codes
ackProps["av"] = 0; // not read from a desired property
ackProps["ad"] = "reported default value";
component["targetTemperature"] = ackProps;
reportedProperties["thermostat1"] = component;
await client.UpdateReportedPropertiesAsync(reportedProperties);
```

Het dubbele apparaat wordt bijgewerkt met de volgende gerapporteerde eigenschap:

```json
{
  "reported": {
    "thermostat1": {
      "__t" : "c",
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "complete"
      }
    }
  }
}
```

#### <a name="subscribe-to-desired-property-updates"></a>Abonneren op de gewenste eigenschaps updates

Services kunnen de gewenste eigenschappen bijwerken waarmee een melding op de verbonden apparaten wordt geactiveerd. Deze melding bevat de bijgewerkte gewenste eigenschappen, inclusief het versie nummer waarmee de update wordt geïdentificeerd. Apparaten moeten reageren met hetzelfde `ack` bericht als gerapporteerde eigenschappen.

Modellen zonder onderdelen zien de enkele eigenschap en maken de melding `ack` met de ontvangen versie:

```csharp
await client.SetDesiredPropertyUpdateCallbackAsync(async (desired, ctx) => 
{
  JValue targetTempJson = desired["targetTemperature"];
  double targetTemperature = targetTempJson.Value<double>();

  TwinCollection reportedProperties = new TwinCollection();
  TwinCollection ackProps = new TwinCollection();
  ackProps["value"] = targetTemperature;
  ackProps["ac"] = 200;
  ackProps["av"] = desired.Version; 
  ackProps["ad"] = "desired property received";
  reportedProperties["targetTemperature"] = ackProps;

  await client.UpdateReportedPropertiesAsync(reportedProperties);
}, null);
```

Het apparaat dubbele toont de eigenschap in de gewenste en gerapporteerde secties:

```json
{
  "desired" : {
    "targetTemperature": 23.2,
    "$version" : 3
  },
  "reported": {
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "complete"
      }
  }
}
```

Modellen met onderdelen ontvangen de gewenste eigenschappen met de naam van het onderdeel en moeten de gerapporteerde eigenschap weer geven `ack` :

```csharp
await client.SetDesiredPropertyUpdateCallbackAsync(async (desired, ctx) =>
{
  JObject thermostatComponent = desired["thermostat1"];
  JToken targetTempProp = thermostatComponent["targetTemperature"];
  double targetTemperature = targetTempProp.Value<double>();

  TwinCollection reportedProperties = new TwinCollection();
  TwinCollection component = new TwinCollection();
  TwinCollection ackProps = new TwinCollection();
  component["__t"] = "c"; // marker to identify a component
  ackProps["value"] = targetTemperature;
  ackProps["ac"] = 200; // using HTTP status codes
  ackProps["av"] = desired.Version; // not readed from a desired property
  ackProps["ad"] = "desired property received";
  component["targetTemperature"] = ackProps;
  reportedProperties["thermostat1"] = component;

  await client.UpdateReportedPropertiesAsync(reportedProperties);
}, null);
```

Voor de onderdelen van het apparaat worden de gewenste en gerapporteerde secties als volgt weer gegeven:

```json
{
  "desired" : {
    "thermostat1" : {
        "__t" : "c",
        "targetTemperature": 23.2,
    }
    "$version" : 3
  },
  "reported": {
    "thermostat1" : {
        "__t" : "c",
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "complete"
      }
    }
  }
}
```

### <a name="commands"></a>Opdracht

Modellen zonder onderdelen ontvangen de opdracht naam zoals deze is aangeroepen door de service.

Modellen met onderdelen ontvangen de naam van de opdracht die wordt voorafgegaan door het onderdeel en het `*` scheidings teken.

```csharp
await client.SetMethodHandlerAsync("themostat*reboot", (MethodRequest req, object ctx) =>
{
  Console.WriteLine("REBOOT");
  return Task.FromResult(new MethodResponse(200));
},
null);
```

#### <a name="request-and-response-payloads"></a>Nettoladingen voor aanvragen en antwoorden

Opdrachten gebruiken typen om hun aanvraag-en reactie-nettoladingen te definiëren. Een apparaat moet de binnenkomende invoer parameter deserialiseren en het antwoord serialiseren. In het volgende voor beeld ziet u hoe u een opdracht implementeert met complexe typen die zijn gedefinieerd in de payloads:

```json
{
  "@type": "Command",
  "name": "start",
  "request": {
    "name": "startRequest",
    "schema": {
      "@type": "Object",
      "fields": [
        {
          "name": "startPriority",
          "schema": "integer"
        },
        {
          "name": "startMessage",
          "schema" : "string"
        }
      ]
    }
  },
  "response": {
    "name": "startReponse",
    "schema": {
      "@type": "Object",
      "fields": [
        {
            "name": "startupTime",
            "schema": "integer" 
        },
        {
          "name": "startupMessage",
          "schema": "string"
        }
      ]
    }
  }
}
```

De volgende code fragmenten laten zien hoe een apparaat deze opdracht definitie implementeert, met inbegrip van de typen die worden gebruikt voor het inschakelen van serialisatie en deserialisatie:

```csharp
class startRequest
{
  public int startPriority { get; set; }
  public string startMessage { get; set; }
}

class startResponse
{
  public int startupTime { get; set; }
  public string startupMessage { get; set; }
}

// ... 

await client.SetMethodHandlerAsync("start", (MethodRequest req, object ctx) =>
{
  var startRequest = JsonConvert.DeserializeObject<startRequest>(req.DataAsJson);
  Console.WriteLine($"Received start command with priority ${startRequest.startPriority} and ${startRequest.startMessage}");

  var startResponse = new startResponse
  {
    startupTime = 123,
    startupMessage = "device started with message " + startRequest.startMessage
  };

  string responsePayload = JsonConvert.SerializeObject(startResponse);
  MethodResponse response = new MethodResponse(Encoding.UTF8.GetBytes(responsePayload), 200);
  return Task.FromResult(response);
},null);
```

> [!Tip]
> De namen van de aanvraag en de reactie zijn niet aanwezig in de geserialiseerde nettoladingen die via de kabel worden verzonden.

## <a name="interact-with-the-device"></a>Communiceren met het apparaat 

Met IoT Plug en Play kunt u apparaten gebruiken die hun model-ID hebben aangekondigd bij uw IoT-hub. U kunt bijvoorbeeld rechtstreeks toegang krijgen tot de eigenschappen en opdrachten van een apparaat.

Als u een IoT-Plug en Play apparaat wilt gebruiken dat is verbonden met uw IoT-hub, gebruikt u de IoT Hub REST API of een van de IoT-taal-Sdk's. De volgende voor beelden gebruiken de IoT Hub REST API. De huidige versie van de API is `2020-05-31-preview` . Voeg `?api-version=2020-05-31` toe aan uw rest Pi-aanroepen.

Als uw Thermo staat-apparaat wordt aangeroepen `t-123` , krijgt u de alle eigenschappen van alle interfaces die door uw apparaat worden geïmplementeerd met een rest API Get-aanroep:

```REST
GET /digitalTwins/t-123
```

Deze aanroep bevat de JSON-eigenschap `$metadata.$model` met de model-id die door het apparaat wordt aangekondigd.

Alle eigenschappen voor alle interfaces zijn toegankelijk met de `GET /DigitalTwin/{device-id}` rest API-sjabloon, waarbij `{device-id}` de id voor het apparaat is:

```REST
GET /digitalTwins/{device-id}
```

U kunt IoT Plug en Play-opdrachten rechtstreeks aanroepen. Als het `Thermostat` onderdeel op het `t-123` apparaat een `restart` opdracht heeft, kunt u dit aanroepen met een rest API post-aanroep:

```REST
POST /digitalTwins/t-123/components/Thermostat/commands/restart
```

Meer in het algemeen kunnen opdrachten worden aangeroepen via deze REST API sjabloon:

- `device-id`: de id voor het apparaat.
- `component-name`: de naam van de interface in de sectie implements in het model van het apparaat.
- `command-name`: de naam van de opdracht.

```REST
/digitalTwins/{device-id}/components/{component-name}/commands/{command-name}
```

## <a name="next-steps"></a>Volgende stappen

Nu u over Apparaatbeheer hebt geleerd, zijn hier enkele aanvullende bronnen:

- [Digital Apparaatdubbels Definition Language (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl)
- [Apparaat-SDK voor C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
- [IoT-REST API](https://docs.microsoft.com/rest/api/iothub/device)
- [Model onderdelen](./concepts-components.md)
