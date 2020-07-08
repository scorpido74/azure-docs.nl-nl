---
title: Wat zijn Device-sjablonen in azure IoT Central | Microsoft Docs
description: Met Azure IoT Central Device-sjablonen kunt u het gedrag opgeven van de apparaten die zijn verbonden met uw toepassing.
author: dominicbetts
ms.author: dobett
ms.date: 05/21/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 2d5009086a24a54c9a2ec4734d3c4dcbebb04475
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84418797"
---
# <a name="what-are-device-templates"></a>Wat zijn apparaatsjablonen?

_Dit artikel is van toepassing op ontwikkel aars van apparaten en oplossingen bouwers._

Een Device-sjabloon in azure IoT Central is een blauw druk die de kenmerken en het gedrag definieert van een type apparaat dat verbinding maakt met uw toepassing. Zo definieert de sjabloon voor het apparaat de telemetrie die een apparaat verzendt, zodat IoT Central visualisaties kunt maken die gebruikmaken van de juiste eenheden en gegevens typen.

Met een oplossings functie voor oplossingen worden apparaatprofielen toegevoegd aan een IoT Central-toepassing. Een ontwikkelaar van het apparaat schrijft de apparaatcode die het gedrag implementeert dat is gedefinieerd in de sjabloon voor het apparaat.

Een sjabloon voor een apparaat bestaat uit de volgende secties:

- _Een mogelijkheidsprofiel (DCM)_. In dit deel van de sjabloon voor het apparaat wordt gedefinieerd hoe het apparaat samenwerkt met uw toepassing. Een ontwikkelaar van het apparaat implementeert het gedrag dat is gedefinieerd in DCM.
- _Eigenschappen_van de Cloud. In dit deel van de sjabloon kunt u de oplossings ontwikkelaar de meta gegevens van het apparaat opgeven die moeten worden opgeslagen. Cloud eigenschappen worden nooit gesynchroniseerd met apparaten en bestaan alleen in de toepassing. Cloud eigenschappen hebben geen invloed op de code die een ontwikkelaar van het apparaat schrijft om de DCM te implementeren.
- _Aanpassingen_. In dit deel van de sjabloon voor het apparaat kan de oplossings ontwikkelaar enkele van de definities in de DCM overschrijven. Aanpassingen zijn handig als de oplossings ontwikkelaar wil verfijnen hoe de toepassing een waarde verwerkt, zoals het wijzigen van de weergave naam voor een eigenschap of de kleur die wordt gebruikt om een telemetrie-waarde weer te geven. Aanpassingen hebben geen invloed op de code die een ontwikkelaar van het apparaat schrijft om de DCM te implementeren.
- _Weer gaven_. In dit deel van de sjabloon kunt u met de oplossings ontwikkelaar visualisaties definiëren om gegevens van het apparaat weer te geven en formulieren voor het beheren en controleren van een apparaat. De weer gaven gebruiken DCM, Cloud eigenschappen en aanpassingen. Weer gaven hebben geen invloed op de code die een ontwikkelaar van het apparaat schrijft om de DCM te implementeren.

## <a name="device-capability-models"></a>Apparaatfuncties

Een DCM bepaalt hoe een apparaat samenwerkt met uw IoT Central-toepassing. De ontwikkelaar van het apparaat moet ervoor zorgen dat het apparaat het gedrag implementeert dat in de DCM is gedefinieerd, zodat IoT Central het apparaat kunt bewaken en beheren. Een DCM bestaat uit een of meer _interfaces_, en elke interface kan een verzameling _telemetrie_ -typen, _Apparaateigenschappen_en _opdrachten_definiëren. Een oplossings ontwikkelaar kan een JSON-bestand importeren dat de DCM in een apparaatprofiel definieert, of u kunt de Web-UI in IoT Central gebruiken om een DCM te maken of te bewerken. Wijzigingen aan een DCM die zijn gemaakt met behulp van de Web-UI, hebben een versie van de [sjabloon](./howto-version-device-template.md).

Een oplossings ontwikkelaar kan ook een JSON-bestand met de DCM exporteren. Een ontwikkelaar van een apparaat kan dit JSON-document gebruiken om te begrijpen hoe het apparaat moet communiceren met de IoT Central-toepassing.

Het JSON-bestand dat de DCM definieert, maakt gebruik van de [Digital-taal (DTDL) v1](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL). IoT Central verwacht dat het JSON-bestand de DCM bevat met de interfaces die in line zijn gedefinieerd, in plaats van afzonderlijke bestanden.

Een typisch IoT-apparaat bestaat uit:

- Aangepaste onderdelen, die de dingen zijn die uw apparaat uniek maken.
- Standaard onderdelen, die voor alle apparaten gebruikelijk zijn.

Deze onderdelen worden _interfaces_ genoemd in een DCM. Interfaces definiëren de details van elk onderdeel dat door uw apparaat wordt geïmplementeerd. Interfaces kunnen worden gebruikt in DCMs.

In het volgende voor beeld wordt het overzicht van het mogelijkheidsprofiel voor een omgevings sensor apparaat met twee interfaces weer gegeven:

```json
{
  "@id": "urn:contoso:sensor_device:1",
  "@type": "CapabilityModel",
  "displayName": "Environment Sensor Capability Model",
  "implements": [
    {
      "@type": "InterfaceInstance",
      "name": "deviceinfo",
      "schema": {
        "@id": "urn:azureiot:DeviceManagement:DeviceInformation:1",
        "@type": "Interface",
        "displayName": "Device Information",
        "@context": "http://azureiot.com/v1/contexts/IoTModel.json",
        "contents": [
          ...
        ]
      }
    },
    {
      "@type": "InterfaceInstance",
      "name": "sensor",
      "schema": {
        "@id": "urn:contoso:EnvironmentalSensor:1",
        "@type": "Interface",
        "displayName": "Environmental Sensor",
        "@context": "http://azureiot.com/v1/contexts/IoTModel.json",
        "contents": [
          ...
        ]
      }
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

Een interface bevat enkele vereiste velden:

- `@id`: een unieke ID in de vorm van een eenvoudige, uniforme resource naam.
- `@type`: declareert dat dit object een interface is.
- `@context`: Hiermee geeft u de DTDL-versie op die voor de interface wordt gebruikt.
- `contents`: hier worden de eigenschappen, telemetrie en opdrachten vermeld die het apparaat vormen.

Er zijn enkele optionele velden die u kunt gebruiken om meer informatie toe te voegen aan het mogelijkheidsprofiel, zoals weergave naam en beschrijving.

### <a name="interface"></a>Interface

Met de DTDL kunt u de mogelijkheden van uw apparaat beschrijven. Gerelateerde mogelijkheden zijn gegroepeerd in interfaces. Interfaces beschrijven de eigenschappen, telemetrie en opdrachten die een deel van uw apparaat implementeert:

- `Properties`. Eigenschappen zijn gegevens velden die de status van uw apparaat vertegenwoordigen. Gebruik eigenschappen om de duurzame status van het apparaat aan te geven, zoals de status aan van een pomp van een koel vloeistof. Eigenschappen kunnen ook basis eigenschappen van apparaten vertegenwoordigen, zoals de firmware versie van het apparaat. U kunt eigenschappen declareren als alleen-lezen of beschrijfbaar.
- `Telemetry`. Telemetrie-velden vertegenwoordigen metingen van Sens oren. Wanneer uw apparaat een sensor meting afneemt, moet er een telemetrie-gebeurtenis worden verzonden die de sensor gegevens bevat.
- `Commands`. Opdrachten vertegenwoordigen methoden die gebruikers van uw apparaat op het apparaat kunnen uitvoeren. Bijvoorbeeld een reset opdracht of een opdracht om een ventilator in of uit te scha kelen.

In het volgende voor beeld ziet u de definitie van de omgevings sensor interface:

```json
{
  "@type": "Property",
  "displayName": "Device State",
  "description": "The state of the device. Two states online/offline are available.",
  "name": "state",
  "schema": "boolean"
},
{
  "@type": "Property",
  "displayName": "Customer Name",
  "description": "The name of the customer currently operating the device.",
  "name": "name",
  "schema": "string",
  "writable": true
},
{
  "@type": [
    "Telemetry",
    "SemanticType/Temperature"
  ],
  "description": "Current temperature on the device",
  "displayName": "Temperature",
  "name": "temp",
  "schema": "double",
  "unit": "Units/Temperature/fahrenheit"
},
{
  "@type": "Command",
  "name": "turnon",
  "comment": "This Commands will turn-on the LED light on the device.",
  "commandType": "synchronous"
},
{
  "@type": "Command",
  "name": "turnoff",
  "comment": "This Commands will turn-off the LED light on the device.",
  "commandType": "synchronous"
}
```

In dit voor beeld ziet u twee eigenschappen, een type telemetrie en twee opdrachten. Een minimale veld Beschrijving heeft een:

- `@type`het type mogelijkheid opgeven: `Telemetry` , `Property` of `Command` .  In sommige gevallen bevat het type een semantisch type om IoT Central in te scha kelen om een aantal veronderstellingen te maken over het afhandelen van de waarde.
- `name`voor de telemetrische waarde.
- `schema`om het gegevens type voor de telemetrie of de eigenschap op te geven. Deze waarde kan een primitief type zijn, zoals double, integer, Boolean of string. Complexe object typen, matrices en Maps worden ook ondersteund.
- `commandType`om op te geven hoe de opdracht moet worden afgehandeld.

Met optionele velden, zoals weergave naam en-beschrijving, kunt u meer details toevoegen aan de interface en de mogelijkheden.

### <a name="properties"></a>Eigenschappen

Eigenschappen zijn standaard alleen-lezen. Alleen-lezen eigenschappen betekenen dat het apparaat de waarde van de eigenschapwaarde bijwerkt naar uw IoT Central-toepassing. Uw IoT Central toepassing kan de waarde van een alleen-lezen eigenschap niet instellen.

U kunt een eigenschap ook markeren als beschrijfbaar op een interface. Een apparaat kan een update ontvangen van een Beschrijf bare eigenschap van uw IoT Central toepassing, evenals het rapporteren van eigenschaps waarden die worden bijgewerkt naar uw toepassing.

Apparaten hoeven geen verbinding te zijn om eigenschaps waarden in te stellen. De bijgewerkte waarden worden overgebracht wanneer het apparaat de volgende keer verbinding maakt met de toepassing. Dit gedrag is van toepassing op zowel alleen-lezen als schrijf bare eigenschappen.

Gebruik eigenschappen niet voor het verzenden van telemetrie van uw apparaat. Een ReadOnly-eigenschap zoals bijvoorbeeld `temperatureSetting=80` moet betekenen dat de temperatuur van het apparaat is ingesteld op 80 en dat het apparaat probeert te krijgen, of op deze Tempe ratuur blijft.

Voor Beschrijf bare eigenschappen retourneert de apparaatnaam een gewenste status code, versie en beschrijving om aan te geven of de toepassing de eigenschaps waarde heeft ontvangen en toegepast.

### <a name="telemetry"></a>Telemetrie

Met IoT Central kunt u telemetrie in dash boards en grafieken weer geven en regels gebruiken om acties te activeren wanneer de drempel waarden zijn bereikt. IoT Central gebruikt de informatie in de DCM, zoals gegevens typen, eenheden en weergave namen, om te bepalen hoe telemetrische waarden moeten worden weer gegeven.

U kunt de functie gegevens export van IoT Central gebruiken om telemetrie te streamen naar andere bestemmingen, zoals opslag of Event Hubs.

### <a name="commands"></a>Opdrachten

Opdrachten zijn ofwel synchroon of asynchroon. Een synchrone opdracht moet standaard binnen 30 seconden worden uitgevoerd en het apparaat moet zijn verbonden als de opdracht binnenkomt. Als het apparaat op tijd reageert of als het apparaat niet is verbonden, mislukt de opdracht.

Gebruik asynchrone opdrachten voor langlopende bewerkingen. Het apparaat verzendt voortgangs informatie met behulp van telemetrie-berichten. Deze voortgangs berichten hebben de volgende header-eigenschappen:

- `iothub-command-name`: de naam van de opdracht, bijvoorbeeld `UpdateFirmware` .
- `iothub-command-request-id`: de aanvraag-ID die is gegenereerd aan de server zijde en die tijdens de eerste aanroep naar het apparaat wordt verzonden.
- `iothub-interface-id`: De ID van de interface waarop deze opdracht is gedefinieerd, bijvoorbeeld `urn:example:AssetTracker:1` .
 `iothub-interface-name`: de naam van de instantie van deze interface, bijvoorbeeld `myAssetTracker` .
- `iothub-command-statuscode`: de status code die is geretourneerd van het apparaat, bijvoorbeeld `202` .

## <a name="cloud-properties"></a>Eigenschappen van Cloud

Cloud eigenschappen maken deel uit van de sjabloon van het apparaat, maar maken geen deel uit van de DCM. Met Cloud eigenschappen kunnen de oplossings ontwikkelaar de meta gegevens van een apparaat opgeven om op te slaan in de IoT Central-toepassing. Cloud eigenschappen hebben geen invloed op de code die een ontwikkelaar van het apparaat schrijft om de DCM te implementeren.

Een oplossings ontwikkelaar kan Cloud eigenschappen toevoegen aan dash boards en weer gaven naast apparaateigenschappen om een operator in staat te stellen de apparaten te beheren die zijn verbonden met de toepassing. Een oplossings ontwikkelaar kan ook Cloud eigenschappen gebruiken als onderdeel van een regel definitie om een drempel waarde te maken die kan worden bewerkt door een operator.

## <a name="customizations"></a>Aanpassingen

Aanpassingen maken deel uit van de sjabloon voor het apparaat, maar maken geen deel uit van de DCM. Met aanpassingen kan de oplossings ontwikkelaar enkele definities in de DCM verbeteren of negeren. Een oplossings ontwikkelaar kan bijvoorbeeld de weergave naam voor een type telemetrie of een eigenschap wijzigen. Ontwikkel aars van oplossingen kunnen ook aanpassingen gebruiken om validatie toe te voegen, zoals een minimum-of maximum lengte voor een eigenschap van een teken reeks apparaat.

Aanpassingen kunnen van invloed zijn op de code die een ontwikkelaar van het apparaat schrijft om de DCM te implementeren. Een aanpassing kan bijvoorbeeld de minimum-en maximum lengte van teken reeksen of minimale en maximale numerieke waarden voor telemetrie instellen.

## <a name="views"></a>Weergaven

Een oplossings ontwikkelaar maakt weer gaven waarmee Opera tors verbonden apparaten kunnen controleren en beheren. Weer gaven maken deel uit van de sjabloon van het apparaat, zodat een weer gave is gekoppeld aan een specifiek apparaattype. Een weer gave kan het volgende omvatten:

- Grafieken voor het uitzetten van telemetrie.
- Tegels om eigenschappen van alleen-lezen apparaten weer te geven.
- Tegels zodat de operator Beschrijf bare apparaateigenschappen kan bewerken.
- Tegels om de operator Cloud eigenschappen te laten bewerken.
- Tegels waarmee de operator opdrachten kan aanroepen, inclusief opdrachten die een Payload verwachten.
- Tegels voor het weer geven van labels, afbeeldingen of tekst verlaging.

De telemetrie, eigenschappen en opdrachten die u aan een weer gave kunt toevoegen, worden bepaald door de DCM, Cloud eigenschappen en aanpassingen in de sjabloon voor het apparaat.

## <a name="next-steps"></a>Volgende stappen

Als ontwikkel aars van apparaten, nu dat u hebt geleerd over sjablonen voor apparaten, kunt u de volgende stappen uitvoeren om [verbinding te maken met Azure IOT Central](./concepts-get-connected.md) om meer te weten te komen over het registreren van apparaten met IOT Central en hoe IOT Central verbindingen met apparaten beveiligt.

Als ontwikkelaar van oplossingen is een voorgestelde volgende stap het lezen [van een nieuw IOT-apparaattype in uw Azure IOT Central-toepassing](./howto-set-up-template.md) . meer informatie over het maken van een sjabloon voor een apparaat.
