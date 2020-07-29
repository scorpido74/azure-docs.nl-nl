---
title: IoT Plug en Play preview model-detectie implementeren | Microsoft Docs
description: Meer informatie over hoe u IoT-Plug en Play model detectie kunt implementeren in uw oplossing.
author: prashmo
ms.author: prashmo
ms.date: 07/23/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 364b85a8ead09858b97d5d7e6ca8c130b9960b2c
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87337378"
---
# <a name="implement-iot-plug-and-play-preview-model-discovery-in-an-iot-solution"></a>IoT Plug en Play preview-model detectie implementeren in een IoT-oplossing

In dit artikel wordt beschreven hoe u als een oplossings functie voor IoT Plug en Play preview-model detectie kunt implementeren in een IoT-oplossing. Model detectie beschrijft hoe:

- IoT Plug en Play-apparaten registreren hun model-ID.
- Met een IoT-oplossing worden de interfaces opgehaald die door het apparaat worden geïmplementeerd.

Er zijn twee algemene categorieën van IoT-oplossingen:

- Een met *doel opgemaakte IOT-oplossing* werkt met een bekende set IOT Plug en Play-modellen.

- Een op *modellen gebaseerde IOT-oplossing* kan worden gebruikt met een IOT Plug en Play-apparaat. Het bouwen van een model gerichte oplossing is complexer, maar het voor deel is dat uw oplossing werkt met apparaten die in de toekomst worden toegevoegd.

    Als u een model-gebaseerde IoT-oplossing wilt bouwen, moet u logica bouwen op basis van de IoT-Plug en Play interface primitieven: telemetrie, eigenschappen en opdrachten. De logica van uw oplossing vertegenwoordigt een apparaat door meerdere telemetrie, eigenschappen en opdracht mogelijkheden te combi neren.

In dit artikel wordt beschreven hoe u model detectie in beide typen oplossingen implementeert.

## <a name="model-discovery"></a>Modeldetectie

Om het model te detecteren dat een apparaat implementeert, kan een oplossing de model-ID ophalen met behulp van op gebeurtenissen gebaseerde detectie of op dubbele basis detectie:

### <a name="event-based-discovery"></a>Detectie op basis van gebeurtenissen

Wanneer een IoT Plug en Play-apparaat verbinding maakt met IoT Hub, registreert het het model dat wordt geïmplementeerd. Deze registratie resulteert in een [digitale dubbele wijzigings gebeurtenis](concepts-digital-twin.md#digital-twin-change-events) melding. Zie [IOT hub bericht routering gebruiken om apparaat-naar-Cloud-berichten te verzenden naar verschillende eind punten](../iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events)voor meer informatie over het inschakelen van route ring voor digitale dubbele gebeurtenissen.

De oplossing kan gebruikmaken van de gebeurtenis die wordt weer gegeven in het volgende code fragment voor meer informatie over IoT Plug en Play apparaat dat verbinding maakt en de model-ID krijgt:

```json
iothub-connection-device-id:sample-device
iothub-enqueuedtime:7/22/2020 8:02:27 PM
iothub-message-source:digitalTwinChangeEvents
correlation-id:100f322dc2c5
content-type:application/json-patch+json
content-encoding:utf-8
[
  {
    "op": "replace",
    "path": "/$metadata/$model",
    "value": "dtmi:com:example:TemperatureController;1"
  }
]
```

Deze gebeurtenis wordt geactiveerd wanneer de model-ID van het apparaat wordt toegevoegd of bijgewerkt.

### <a name="twin-based-discovery"></a>Detectie op basis van dubbele

Als de oplossing over mogelijkheden van een bepaald apparaat wil weten, kan deze de informatie ophalen met behulp van de [digitale dubbele API ophalen](https://docs.microsoft.com/rest/api/iothub/service/digitaltwin/getdigitaltwin) .

In het volgende digitale dubbele fragment `$metadata.$model` bevat de model-id van een IOT-Plug en Play apparaat:

```json
{
    "$dtId": "sample-device",
    "$metadata": {
        "$model": "dtmi:com:example:TemperatureController;1",
        "serialNumber": {
            "lastUpdateTime": "2020-07-17T06:10:31.9609233Z"
        }
    }
}
```

De oplossing kan ook de methode **Get** dubbel gebruiken om de model-id op te halen van het apparaat, twee zoals wordt weer gegeven in het volgende code fragment:

```json
{
    "deviceId": "sample-device",
    "etag": "AAAAAAAAAAc=",
    "deviceEtag": "NTk0ODUyODgx",
    "status": "enabled",
    "statusUpdateTime": "0001-01-01T00:00:00Z",
    "connectionState": "Disconnected",
    "lastActivityTime": "2020-07-17T06:12:26.8402249Z",
    "cloudToDeviceMessageCount": 0,
    "authenticationType": "sas",
    "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
    },
    "modelId": "dtmi:com:example:TemperatureController;1",
    "version": 15,
    "properties": {...}
    }
}
```

## <a name="model-resolution"></a>Model resolutie

Een oplossing maakt gebruik van model oplossingen om toegang te krijgen tot de interfaces die een model uit de model-ID opstellen. 

- Oplossingen kunnen ervoor kiezen deze interfaces op te slaan als bestanden in een lokale map. 
- Oplossingen kunnen gebruikmaken van de [model opslagplaats](concepts-model-repository.md).

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd over model detectie een IoT-oplossing, kunt u meer te weten komen over het [Azure IOT-platform](overview-iot-plug-and-play.md) om andere mogelijkheden voor uw oplossing te gebruiken.

- [Communiceren met een apparaat vanuit uw oplossing](quickstart-service-node.md)
- [IoT digitale dubbele REST API](https://docs.microsoft.com/rest/api/iothub/service/digitaltwin)
- [Azure IoT Explorer](howto-use-iot-explorer.md)
