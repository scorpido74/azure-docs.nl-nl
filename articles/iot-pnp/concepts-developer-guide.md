---
title: Ontwikkelaarshandleiding - IoT Plug and Play Preview | Microsoft Documenten
description: Beschrijving van apparaatmodellering voor IoT-ontwikkelaars plug- en play-ontwikkelaars
author: dominicbetts
ms.author: dobett
ms.date: 12/26/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 5fda51e6d2f62b9cbef0fcac22d5bb2ea0df905b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77605219"
---
# <a name="iot-plug-and-play-preview-modeling-developer-guide"></a>IoT Plug and Play Preview-modelleringshandleiding

Met IoT Plug and Play Preview u apparaten bouwen die reclame maken voor hun mogelijkheden voor Azure IoT-toepassingen. IoT Plug and Play-apparaten vereisen geen handmatige configuratie wanneer een klant ze verbindt met IoT Plug en Play-toepassingen. IoT Central is een voorbeeld van een IoT Plug and Play-applicatie.

Als u een IoT Plug and Play-apparaat wilt bouwen, moet u een apparaatbeschrijving maken. De beschrijving wordt gedaan met een eenvoudige definitie taal genaamd Digital Twins Definition Language (DTDL).

## <a name="device-capability-model"></a>Apparaatcapaciteitsmodel

Met DTDL maakt u een _apparaatcapaciteitsmodel_ om de onderdelen van uw apparaat te beschrijven. Een typisch IoT-apparaat bestaat uit:

- Aangepaste onderdelen, dat zijn de dingen die uw apparaat uniek maken.
- Standaardonderdelen, dingen die voor alle apparaten gebruikelijk zijn.

Deze onderdelen worden _interfaces_ genoemd in een apparaatcapaciteitsmodel. Interfaces definiëren de details van elk onderdeel dat uw apparaat implementeert.

In het volgende voorbeeld wordt het apparaatcapaciteitsmodel voor een thermostaatapparaat weergegeven:

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

Een capaciteitsmodel heeft een aantal vereiste velden:

- `@id`: een unieke ID in de vorm van een eenvoudige uniform resourcenaam.
- `@type`: verklaart dat dit object een capaciteitsmodel is.
- `@context`: geeft de DTDL-versie op die voor het capaciteitsmodel wordt gebruikt.
- `implements`: geeft een overzicht van de interfaces die uw apparaat implementeert.

Elke vermelding in de lijst met interfaces in de sectie Werktuigen heeft een:

- `name`: de programmeernaam van de interface.
- `schema`: de interface die het capaciteitsmodel implementeert.

Er zijn extra optionele velden die u gebruiken om meer details toe te voegen aan het capaciteitsmodel, zoals weergavenaam en beschrijving. Interfaces die binnen een capaciteitsmodel worden gedeclareerd, kunnen worden beschouwd als onderdelen van het apparaat. Voor openbare preview kan de interfacelijst slechts één vermelding per schema bevatten.

## <a name="interface"></a>Interface

Met DTDL beschrijft u de mogelijkheden van uw apparaat met behulp van interfaces. Interfaces beschrijven de _eigenschappen_, _telemetrie_en _opdrachten_ een deel van uw apparaat implementeert:

- `Properties`. Eigenschappen zijn gegevensvelden die de status van uw apparaat vertegenwoordigen. Gebruik eigenschappen om de duurzame toestand van het apparaat weer te geven, zoals de aan-uit-staat van een koelvloeistofpomp. Eigenschappen kunnen ook basiseigenschappen van het apparaat weergeven, zoals de firmwareversie van het apparaat. U eigenschappen declareren als alleen-lezen of beschrijfbaar.
- `Telemetry`. Telemetrievelden vertegenwoordigen metingen van sensoren. Wanneer uw apparaat een sensormeting uitvoert, moet het een telemetriegebeurtenis met de sensorgegevens verzenden.
- `Commands`. Opdrachten vertegenwoordigen methoden die gebruikers van uw apparaat op het apparaat kunnen uitvoeren. Bijvoorbeeld een resetopdracht of een opdracht om een ventilator in- of uit te schakelen.

In het volgende voorbeeld wordt de interface voor een thermostaatapparaat weergegeven:

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

Een interface heeft een aantal vereiste velden:

- `@id`: een unieke ID in de vorm van een eenvoudige uniform resourcenaam.
- `@type`: verklaart dat dit object een interface is.
- `@context`: geeft de DTDL-versie op die voor de interface wordt gebruikt.
- `contents`: geeft een overzicht van de eigenschappen, telemetrie en opdrachten die deel uitmaken van uw apparaat.

In dit eenvoudige voorbeeld is er slechts één telemetrieveld. Een minimale veldbeschrijving heeft een:

- `@type`: geeft het type `Telemetry`vermogen `Property`aan: , , of `Command`.
- `name`: geeft de naam van de telemetriewaarde.
- `schema`: geeft het gegevenstype voor de telemetrie op. Deze waarde kan een primitief type zijn, zoals dubbel, geheel getal, booleaan of tekenreeks. Complexe objecttypen, arrays en kaarten worden ook ondersteund.

In andere optionele velden, zoals weergavenaam en beschrijving, u meer details toevoegen aan de interface en mogelijkheden.

### <a name="properties"></a>Eigenschappen

Standaard zijn eigenschappen alleen-lezen. Alleen-lezen eigenschappen betekenen dat het apparaat waarde-updates voor de eigenschap rapporteert aan uw IoT-hub. Uw IoT-hub kan de waarde van een alleen-lezen eigenschap niet instellen.

U een eigenschap ook markeren als schrijfbaar op een interface. Een apparaat kan een update ontvangen naar een schrijfbare eigenschap van uw IoT-hub en waarde-updates van de eigenschap rapporteren aan uw hub.

Apparaten hoeven niet te worden verbonden met het instellen van eigenschapswaarden. De bijgewerkte waarden worden overgedragen wanneer het apparaat vervolgens verbinding maakt met de hub. Dit gedrag is van toepassing op zowel alleen-lezen als schrijfbare eigenschappen.

Gebruik geen eigenschappen om telemetrie vanaf uw apparaat te verzenden. Bijvoorbeeld, een alleen-lezen `temperatureSetting=80` eigenschap, zoals moet betekenen dat het apparaat temperatuur is ingesteld op 80, en het apparaat probeert te krijgen of verblijf op, deze temperatuur.

Voor beschrijfbare eigenschappen retourneert de apparaattoepassing een gewenste statusstatuscode, -versie en -beschrijving om aan te geven of de eigenschapswaarde is ontvangen en toegepast.

### <a name="telemetry"></a>Telemetrie

Standaard leidt IoT Hub alle telemetrieberichten van apparaten naar het [ingebouwde servicegerichte eindpunt **(berichten/gebeurtenissen)**](../iot-hub/iot-hub-devguide-messages-read-builtin.md) dat compatibel is met [gebeurtenishubs.](https://azure.microsoft.com/documentation/services/event-hubs/)

U de [aangepaste eindpunten en routeringsregels van IoT Hub](../iot-hub/iot-hub-devguide-messages-d2c.md) gebruiken om telemetrie naar andere bestemmingen te verzenden, zoals blob-opslag of andere gebeurtenishubs. Routeringsregels gebruiken berichteigenschappen om berichten te selecteren.

### <a name="commands"></a>Opdrachten

Opdrachten zijn synchroon of asynchroon. Een synchrone opdracht moet standaard binnen 30 seconden worden uitgevoerd en het apparaat moet zijn aangesloten wanneer de opdracht arriveert. Als het apparaat op tijd reageert of als het apparaat niet is aangesloten, mislukt de opdracht.

Gebruik asynchrone opdrachten voor langlopende bewerkingen. Het apparaat verzendt voortgangsgegevens via telemetrieberichten. Deze voortgangsberichten hebben de volgende kopteksteigenschappen:

- `iothub-command-name`: de opdrachtnaam, `UpdateFirmware`bijvoorbeeld .
- `iothub-command-request-id`: de aanvraag-id die aan de serverzijde wordt gegenereerd en in het eerste gesprek naar het apparaat wordt verzonden.
- `iothub-interface-id`: De id van de interface waarop deze `urn:example:AssetTracker:1`opdracht is gedefinieerd, bijvoorbeeld .
 `iothub-interface-name`: de instantienaam van deze `myAssetTracker`interface, bijvoorbeeld .
- `iothub-command-statuscode`: de statuscode die van `202`het apparaat wordt geretourneerd, bijvoorbeeld .

## <a name="register-a-device"></a>Een apparaat registreren

IoT Plug and Play maakt het eenvoudig om reclame te maken voor de mogelijkheden van uw apparaat. Met IoT Plug and Play moet u, nadat uw apparaat verbinding maakt met IoT Hub, uw apparaatcapaciteitsmodel registreren. Registratie stelt klanten in staat om de IoT Plug and Play-mogelijkheden van uw apparaat te gebruiken.

In deze handleiding ziet u hoe u een apparaat registreert met de Azure IoT Device SDK voor C.

Voor elke interface die uw apparaat implementeert, moet u een interface maken en deze verbinden met de implementatie ervan.

Voor de eerder getoonde thermostaatinterface, met behulp van de C SDK, maakt en verbindt u de thermostaatinterface met de implementatie ervan:

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

Nadat u een interface hebt gemaakt, registreert u het model en de interfaces van uw apparaatmogelijkheid met uw IoT-hub:

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

Met IoT Plug and Play u apparaten gebruiken die hun mogelijkheden hebben geregistreerd met uw IoT-hub. U hebt bijvoorbeeld rechtstreeks toegang tot de eigenschappen en opdrachten van een apparaat.

Als u een IoT Plug and Play-apparaat wilt gebruiken dat is verbonden met uw IoT-hub, gebruikt u de IoT Hub REST API of een van de SDK's in de IoT-taal. In de volgende voorbeelden wordt de IoT Hub REST API gebruikt. De huidige versie van `2019-07-01-preview`de API is . Toevoegen `?api-version=2019-07-01-preview` aan uw REST PI-oproepen.

Om de waarde van een apparaateigenschap te`fwVersion`krijgen, `DeviceInformation` zoals de firmwareversie ( ) in de interface in de thermostaat, gebruikt u de digitale tweelingEN REST API.

Als uw thermostaatapparaat `t-123`wordt aangeroepen, krijgt u alle eigenschappen op alle interfaces die door uw apparaat zijn geïmplementeerd met een REST API GET-aanroep:

```REST
GET /digitalTwins/t-123/interfaces
```

Meer in het algemeen worden alle eigenschappen van alle `{device-id}` interfaces geopend met deze REST API-sjabloon waar de id voor het apparaat is:

```REST
GET /digitalTwins/{device-id}/interfaces
```

Als u de naam van de `deviceInformation`interface kent, zoals , en eigenschappen voor die specifieke interface wilt opvragen, moet u het verzoek op naam naar een specifieke interface scopen:

```REST
GET /digitalTwins/t-123/interfaces/deviceInformation
```

Meer in het algemeen kunnen eigenschappen voor een specifieke interface `device-id` worden geopend via `{interface-name}` deze REST API-sjabloon waar de id voor het apparaat is en de naam van de interface is:

```REST
GET /digitalTwins/{device-id}/interfaces/{interface-name}
```

U iot-plug- en play-apparaatopdrachten rechtstreeks bellen. Als `Thermostat` de interface `t-123` in `restart` het apparaat een opdracht heeft, u deze aanroepen met een REST API POST-aanroep:

```REST
POST /digitalTwins/t-123/interfaces/thermostat/commands/restart
```

Meer in het algemeen kunnen opdrachten worden aangeroepen via deze REST API-sjabloon:

- `device-id`: de id voor het apparaat.
- `interface-name`: de naam van de interface van de sectie implementaties in het apparaatcapaciteitsmodel.
- `command-name`: de naam van de opdracht.

```REST
/digitalTwins/{device-id}/interfaces/{interface-name}/commands/{command-name}
```

## <a name="next-steps"></a>Volgende stappen

Nu u meer hebt geleerd over apparaatmodellering, volgen hier enkele extra bronnen:

- [Digital Twin Definition Language (DTDL)](https://aka.ms/DTDL)
- [Apparaat-SDK voor C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
- [IoT REST-API](https://docs.microsoft.com/rest/api/iothub/device)
