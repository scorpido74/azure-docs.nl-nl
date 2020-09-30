---
title: Hand leiding voor service ontwikkelaars-IoT Plug en Play | Microsoft Docs
description: Beschrijving van IoT-Plug en Play voor service ontwikkelaars
author: dominicbetts
ms.author: dobett
ms.date: 09/24/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: e7728af831b26bff19f347e5b85db6420e7966ed
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/30/2020
ms.locfileid: "91579989"
---
# <a name="iot-plug-and-play-service-developer-guide"></a>Ontwikkelaars handleiding voor IoT Plug en Play-service

IoT Plug en Play biedt u de mogelijkheid om slimme apparaten te bouwen die hun mogelijkheden adverteren voor Azure IoT-toepassingen. IoT Plug en Play-apparaten hoeven niet hand matig te worden geconfigureerd wanneer een klant deze verbindt met IoT-Plug en Play toepassingen.

Met IoT Plug en Play kunt u apparaten gebruiken die hun model-ID hebben aangekondigd bij uw IoT-hub. U kunt bijvoorbeeld rechtstreeks toegang krijgen tot de eigenschappen en opdrachten van een apparaat.

Als u een IoT-Plug en Play apparaat wilt gebruiken dat is verbonden met uw IoT-hub, gebruikt u een van de IoT-service-Sdk's of de IoT Hub REST API:

## <a name="service-sdks"></a>Service-SDK's

Gebruik de Azure IoT-service-Sdk's in uw oplossing om te communiceren met apparaten en modules. U kunt bijvoorbeeld de service-Sdk's gebruiken om dubbele eigenschappen te lezen en bij te werken en opdrachten aan te roepen. Ondersteunde talen zijn C#, Java, Node.js en python.

Met de Sdk's van de service krijgt u toegang tot apparaatgegevens van een oplossing, zoals een bureau blad of webtoepassing. De service-Sdk's bevatten twee naam ruimten en object modellen die u kunt gebruiken om de model-ID op te halen:

- IOT hub-service-client.
- Digital Apparaatdubbels service-client.

| Taal | IoT Hub-serviceclient | Digital Apparaatdubbels service-client |
| -------- | ---------------------- | ---------------------------- |
| C#       | [Documentatie](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.shared.twin.modelid?view=azure-dotnet#Microsoft_Azure_Devices_Shared_Twin_ModelId&preserve-view=true) <br/> [Voorbeeld](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Samples/service/PnpServiceSamples/Thermostat/Program.cs)| [Voorbeeld](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Samples/service/DigitalTwinClientSamples) |
| Java     | [Documentatie](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.service.devicetwin.devicetwindevice?view=azure-java-stable&preserve-view=true) <br/> [Voorbeeld](https://github.com/Azure/azure-iot-sdk-java/blob/master/service/iot-service-samples/pnp-service-sample/thermostat-service-sample/src/main/java/samples/com/microsoft/azure/sdk/iot/service/Thermostat.java)| [Voorbeeld](https://github.com/Azure/azure-iot-sdk-java/tree/master/service/iot-service-samples/digitaltwin-service-samples) |
| Node.js  | [Documentatie](https://docs.microsoft.com/javascript/api/azure-iothub/twin?view=azure-node-latest&preserve-view=true) <br/> [Voorbeeld](https://github.com/Azure/azure-iot-sdk-node/blob/master/service/samples/javascript/twin.js)| [Documentatie](https://docs.microsoft.com/javascript/api/azure-iot-digitaltwins-service/?view=azure-node-latest&preserve-view=true) |
| Python   | [Documentatie](https://docs.microsoft.com/python/api/azure-iot-hub/azure.iot.hub.iothubregistrymanager?view=azure-python&preserve-view=true) <br/> [Voorbeeld](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-hub/samples/iothub_registry_manager_method_sample.py)| [Documentatie](https://docs.microsoft.com/python/api/azure-iot-hub/azure.iot.hub.iothubdigitaltwinmanager?view=azure-python&preserve-view=true) | 

## <a name="rest-api"></a>REST-API

De volgende voor beelden gebruiken de IoT Hub REST API om te communiceren met een verbonden IoT Plug en Play-apparaat. De huidige versie van de API is `2020-09-30` . Voeg `?api-version=2020-05-31` toe aan uw rest Pi-aanroepen.

> [!NOTE]
> Module apparaatdubbels worden momenteel niet ondersteund door de `digitalTwins` API.

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

- [DTDL (Digital Twins Definition Language)](https://github.com/Azure/opendigitaltwins-dtdl)
- [Apparaat-SDK voor C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
- [IoT-REST API](https://docs.microsoft.com/rest/api/iothub/device)
- [Model onderdelen](./concepts-components.md)
- [De DTDL-hulpprogram ma's voor ontwerpen installeren en gebruiken](howto-use-dtdl-authoring-tools.md)
