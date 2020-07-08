---
title: Ontwikkelaars handleiding-IoT Plug en Play preview | Microsoft Docs
description: Beschrijving van Device Modeling voor IoT Plug en Play-ontwikkel aars
author: dominicbetts
ms.author: dobett
ms.date: 12/26/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 5fda51e6d2f62b9cbef0fcac22d5bb2ea0df905b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "77605219"
---
# <a name="iot-plug-and-play-preview-modeling-developer-guide"></a>Ontwikkelaars handleiding voor IoT Plug en Play preview-modellen

Met IoT Plug en Play preview kunt u apparaten bouwen die hun mogelijkheden adverteren voor Azure IoT-toepassingen. IoT Plug en Play-apparaten hoeven niet hand matig te worden geconfigureerd wanneer een klant deze verbindt met IoT-Plug en Play toepassingen. IoT Central is een voor beeld van een IoT-Plug en Play toepassing.

Als u een IoT Plug en Play-apparaat wilt bouwen, moet u een apparaatbeschrijving maken. De beschrijving wordt uitgevoerd met een eenvoudige definitie taal met de naam Digital Apparaatdubbels Definition Language (DTDL).

## <a name="device-capability-model"></a>Apparaatondersteuningsprofiel

Met DTDL maakt u een _hulp middel_ voor het maken van een apparaat om de onderdelen van uw apparaat te beschrijven. Een typisch IoT-apparaat bestaat uit:

- Aangepaste onderdelen, die de dingen zijn die uw apparaat uniek maken.
- Standaard onderdelen, die voor alle apparaten gebruikelijk zijn.

Deze onderdelen worden _interfaces_ genoemd in een model voor het maken van een apparaat. Interfaces definiëren de details van elk onderdeel dat door uw apparaat wordt geïmplementeerd.

In het volgende voor beeld wordt het mogelijkheidsprofiel voor een thermo staat weer gegeven:

```json
{
  "@id": "urn:example:Thermostat_T_1000:1",
  "@type": "CapabilityModel",
  "implements": [
    {
      "name": "thermostat",
      "schema": "urn:example:Thermostat:1"
    },
    {
      "name": "urn_azureiot_deviceManagement_DeviceInformation",
      "schema": "urn:azureiot:deviceManagement:DeviceInformation:1"
    }
  ],
  "@context": "http://azureiot.com/v1/contexts/IoTModel.json"
}
```

Een mogelijkheidsprofiel heeft enkele vereiste velden:

- `@id`: een unieke ID in de vorm van een eenvoudige, uniforme resource naam.
- `@type`: declareert dat dit object een mogelijkheidsprofiel is.
- `@context`: Hiermee geeft u de DTDL-versie op die wordt gebruikt voor het functionaliteits model.
- `implements`: hier worden de interfaces weer gegeven die uw apparaat implementeert.

Elk item in de lijst met interfaces in de sectie implements heeft een:

- `name`: de programmeer naam van de interface.
- `schema`: de interface die het functionaliteits model implementeert.

Er zijn aanvullende optionele velden die u kunt gebruiken om meer informatie toe te voegen aan het mogelijkheidsprofiel, zoals weergave naam en beschrijving. Interfaces die zijn gedeclareerd in een mogelijkheidsprofiel, kunnen worden beschouwd als onderdelen van het apparaat. Voor de open bare preview mag de interface lijst slechts één item per schema bevatten.

## <a name="interface"></a>Interface

Met DTDL beschrijft u de mogelijkheden van uw apparaat met behulp van interfaces. Interfaces beschrijven de _Eigenschappen_, _telemetrie_en _opdrachten_ die een deel van uw apparaat implementeert:

- `Properties`. Eigenschappen zijn gegevens velden die de status van uw apparaat vertegenwoordigen. Gebruik eigenschappen om de duurzame status van het apparaat aan te geven, zoals de status aan van een pomp van een koel vloeistof. Eigenschappen kunnen ook basis eigenschappen van apparaten vertegenwoordigen, zoals de firmware versie van het apparaat. U kunt eigenschappen declareren als alleen-lezen of beschrijfbaar.
- `Telemetry`. Telemetrie-velden vertegenwoordigen metingen van Sens oren. Wanneer uw apparaat een sensor meting afneemt, moet er een telemetrie-gebeurtenis worden verzonden die de sensor gegevens bevat.
- `Commands`. Opdrachten vertegenwoordigen methoden die gebruikers van uw apparaat op het apparaat kunnen uitvoeren. Bijvoorbeeld een reset opdracht of een opdracht om een ventilator in of uit te scha kelen.

In het volgende voor beeld wordt de interface voor een thermo staat weer gegeven:

```json
{
  "@id": "urn:example:Thermostat:1",
  "@type": "Interface",
  "contents": [
    {
      "@type": "Telemetry",
      "name": "temperature",
      "schema": "double"
    }
  ],
  "@context": "http://azureiot.com/v1/contexts/IoTModel.json"
}
```

Een interface bevat enkele vereiste velden:

- `@id`: een unieke ID in de vorm van een eenvoudige, uniforme resource naam.
- `@type`: declareert dat dit object een interface is.
- `@context`: Hiermee geeft u de DTDL-versie op die voor de interface wordt gebruikt.
- `contents`: hier worden de eigenschappen, telemetrie en opdrachten vermeld die het apparaat vormen.

In dit eenvoudige voor beeld is er slechts één telemetrie-veld. Een minimale veld Beschrijving heeft een:

- `@type`: Hiermee geeft u het type mogelijkheid: `Telemetry` , `Property` of `Command` .
- `name`: Hiermee geeft u de naam van de telemetrie-waarde op.
- `schema`: Hiermee geeft u het gegevens type voor de telemetrie op. Deze waarde kan een primitief type zijn, zoals double, integer, Boolean of string. Complexe object typen, matrices en Maps worden ook ondersteund.

Met andere optionele velden, zoals weergave naam en-beschrijving, kunt u meer details toevoegen aan de interface en de mogelijkheden.

### <a name="properties"></a>Eigenschappen

Eigenschappen zijn standaard alleen-lezen. Alleen-lezen eigenschappen betekenen dat het apparaat de waarde-instellingen van de eigenschapwaarde rapporteert aan uw IoT-hub. Uw IoT-hub kan de waarde van een alleen-lezen eigenschap niet instellen.

U kunt een eigenschap ook markeren als beschrijfbaar op een interface. Een apparaat kan een update ontvangen van een Beschrijf bare eigenschap van uw IoT-hub en het rapporteren van eigenschaps waarden voor uw hub.

Apparaten hoeven geen verbinding te zijn om eigenschaps waarden in te stellen. De bijgewerkte waarden worden overgebracht wanneer het apparaat de volgende keer verbinding maakt met de hub. Dit gedrag is van toepassing op zowel alleen-lezen als schrijf bare eigenschappen.

Gebruik eigenschappen niet voor het verzenden van telemetrie van uw apparaat. Een ReadOnly-eigenschap zoals bijvoorbeeld `temperatureSetting=80` moet betekenen dat de temperatuur van het apparaat is ingesteld op 80 en dat het apparaat probeert te krijgen, of op deze Tempe ratuur blijft.

Voor Beschrijf bare eigenschappen retourneert de apparaatnaam een gewenste status code, versie en beschrijving om aan te geven of de toepassing de eigenschaps waarde heeft ontvangen en toegepast.

### <a name="telemetry"></a>Telemetrie

IoT Hub routert standaard alle telemetrie-berichten van apparaten naar het [ingebouwde eind punt**messages/events**](../iot-hub/iot-hub-devguide-messages-read-builtin.md) dat compatibel is met [Event hubs](https://azure.microsoft.com/documentation/services/event-hubs/).

U kunt [de aangepaste eind punten en routerings regels van IOT hub](../iot-hub/iot-hub-devguide-messages-d2c.md) gebruiken om telemetrie te verzenden naar andere bestemmingen, zoals Blob Storage of andere Event hubs. Routerings regels gebruiken bericht eigenschappen om berichten te selecteren.

### <a name="commands"></a>Opdrachten

Opdrachten zijn ofwel synchroon of asynchroon. Een synchrone opdracht moet standaard binnen 30 seconden worden uitgevoerd en het apparaat moet zijn verbonden als de opdracht binnenkomt. Als het apparaat op tijd reageert of als het apparaat niet is verbonden, mislukt de opdracht.

Gebruik asynchrone opdrachten voor langlopende bewerkingen. Het apparaat verzendt voortgangs informatie met behulp van telemetrie-berichten. Deze voortgangs berichten hebben de volgende header-eigenschappen:

- `iothub-command-name`: de naam van de opdracht, bijvoorbeeld `UpdateFirmware` .
- `iothub-command-request-id`: de aanvraag-ID die is gegenereerd aan de server zijde en die tijdens de eerste aanroep naar het apparaat wordt verzonden.
- `iothub-interface-id`: De ID van de interface waarop deze opdracht is gedefinieerd, bijvoorbeeld `urn:example:AssetTracker:1` .
 `iothub-interface-name`: de naam van de instantie van deze interface, bijvoorbeeld `myAssetTracker` .
- `iothub-command-statuscode`: de status code die is geretourneerd van het apparaat, bijvoorbeeld `202` .

## <a name="register-a-device"></a>Een apparaat registreren

IoT Plug en Play maakt het eenvoudig om de mogelijkheden van uw apparaat te adverteren. Met IoT Plug en Play, nadat uw apparaat verbinding heeft gemaakt met IoT Hub, moet u het mogelijkheidsprofiel registreren. Met registratie kunnen klanten gebruikmaken van de IoT Plug en Play mogelijkheden van uw apparaat.

In deze hand leiding wordt beschreven hoe u een apparaat registreert met behulp van de Azure IoT Device SDK voor C.

Voor elke interface die uw apparaat implementeert, moet u een interface maken en deze verbinden met de implementatie.

Voor de weer gegeven Thermo staat-interface gebruikt u de C SDK om de Thermo staat-interface te maken en te verbinden met de implementatie:

```c
DIGITALTWIN_INTERFACE_HANDLE thermostatInterfaceHandle;

DIGITALTWIN_CLIENT_RESULT result = DigitalTwin_InterfaceClient_Create(
    "thermostat",
    "urn:example:Thermostat:1",
    null, null,
    &thermostatInterfaceHandle);

result = DigitalTwin_Interface_SetCommandsCallbacks(
    thermostatInterfaceHandle,
    commandsCallbackTable);

result = DigitalTwin_Interface_SetPropertiesUpdatedCallbacks(
    thermostatInterfaceHandle,
    propertiesCallbackTable);

```

Herhaal deze code voor elke interface die uw apparaat implementeert.

Nadat u een interface hebt gemaakt, registreert u het mogelijkheidsprofiel en de interfaces van uw IoT-hub:

```c
DIGITALTWIN_INTERFACE_CLIENT_HANDLE interfaces[2];
interfaces[0] = thermostatInterfaceHandle;
interfaces[1] = deviceInfoInterfaceHandle;

result = DigitalTwin_DeviceClient_RegisterInterfacesAsync(
    digitalTwinClientHandle, // The handle for the connection to Azure IoT
    "urn:example:Thermostat_T_1000:1",
    interfaces, 2,
    null, null);
```

## <a name="use-a-device"></a>Een apparaat gebruiken

Met IoT Plug en Play kunt u apparaten gebruiken die hun mogelijkheden hebben geregistreerd bij uw IoT-hub. U kunt bijvoorbeeld rechtstreeks toegang krijgen tot de eigenschappen en opdrachten van een apparaat.

Als u een IoT-Plug en Play apparaat wilt gebruiken dat is verbonden met uw IoT-hub, gebruikt u de IoT Hub REST API of een van de IoT-taal-Sdk's. De volgende voor beelden gebruiken de IoT Hub REST API. De huidige versie van de API is `2019-07-01-preview` . Voeg `?api-version=2019-07-01-preview` toe aan uw rest Pi-aanroepen.

Als u de waarde van een eigenschap apparaat wilt ophalen, zoals de firmware versie ( `fwVersion` ) in de `DeviceInformation` interface van de Thermo staat, gebruikt u de digitale apparaatdubbels rest API.

Als uw Thermo staat-apparaat wordt aangeroepen `t-123` , krijgt u de alle eigenschappen van alle interfaces die door uw apparaat worden geïmplementeerd met een rest API Get-aanroep:

```REST
GET /digitalTwins/t-123/interfaces
```

Meer in het algemeen zijn alle eigenschappen op alle interfaces toegankelijk met deze REST API sjabloon, waarbij `{device-id}` de id is van het apparaat:

```REST
GET /digitalTwins/{device-id}/interfaces
```

Als u de naam van de interface kent, zoals `deviceInformation` en u eigenschappen voor die specifieke interface wilt ophalen, moet u de aanvraag op naam bereiken voor een specifieke interface:

```REST
GET /digitalTwins/t-123/interfaces/deviceInformation
```

Meer in het algemeen zijn eigenschappen van een specifieke interface toegankelijk via deze REST API sjabloon, waarbij `device-id` de id voor het apparaat is en `{interface-name}` de naam van de interface is:

```REST
GET /digitalTwins/{device-id}/interfaces/{interface-name}
```

U kunt IoT Plug en Play-opdrachten rechtstreeks aanroepen. Als de `Thermostat` interface in het `t-123` apparaat een `restart` opdracht heeft, kunt u deze aanroepen met een rest API post-aanroep:

```REST
POST /digitalTwins/t-123/interfaces/thermostat/commands/restart
```

Meer in het algemeen kunnen opdrachten worden aangeroepen via deze REST API sjabloon:

- `device-id`: de id voor het apparaat.
- `interface-name`: de naam van de interface in de sectie implements in het model van het apparaat.
- `command-name`: de naam van de opdracht.

```REST
/digitalTwins/{device-id}/interfaces/{interface-name}/commands/{command-name}
```

## <a name="next-steps"></a>Volgende stappen

Nu u over Apparaatbeheer hebt geleerd, zijn hier enkele aanvullende bronnen:

- [Digital-dubbele-definitie taal (DTDL)](https://aka.ms/DTDL)
- [Apparaat-SDK voor C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
- [IoT-REST API](https://docs.microsoft.com/rest/api/iothub/device)
