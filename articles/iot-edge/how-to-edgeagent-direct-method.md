---
title: Ingebouwde edgeAgent-directe methoden - Azure IoT Edge
description: Een IoT Edge-implementatie bewaken en beheren met behulp van ingebouwde directe methoden in de Runtime-module van de IoT Edge-agent
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/02/2020
ms.topic: conceptual
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 57b9d46918414cef9e8cbcffb941b98c98f985ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240345"
---
# <a name="communicate-with-edgeagent-using-built-in-direct-methods"></a>Communiceren met edgeAgent met behulp van ingebouwde directe methoden

IoT Edge-implementaties bewaken en beheren met behulp van de directe methoden die zijn opgenomen in de IoT Edge-agentmodule. Directe methoden worden geïmplementeerd op het apparaat en kunnen vervolgens vanuit de cloud worden aangeroepen. De IoT Edge-agent bevat directe methoden waarmee u uw IoT Edge-apparaten op afstand controleren en beheren.

Zie [Directe methoden van IoT Hub](../iot-hub/iot-hub-devguide-direct-methods.md)begrijpen en aanroepen voor meer informatie over directe methoden, hoe deze te gebruiken en hoe u deze in uw eigen modules implementeren.

De namen van deze directe methoden worden behandeld case-ongevoelig.

## <a name="ping"></a>Ping

De **ping-methode** is handig om te controleren of IoT Edge op een apparaat wordt uitgevoerd of dat het apparaat een open verbinding met IoT Hub heeft. Gebruik deze directe methode om de IoT Edge-agent te pingen en de status ervan te krijgen. Een succesvolle ping retourneert een lege payload en **"status": 200**.

Bijvoorbeeld:

```azurecli
az iot hub invoke-module-method --method-name 'ping' -n <hub name> -d <device name> -m '$edgeAgent'
```

Roep in de Azure-portal de `ping` methode aan met `{}`de methodenaam en een lege JSON-payload.

![Directe methode 'ping' in Azure-portal aanroepen](./media/how-to-edgeagent-direct-method/ping-direct-method.png)

## <a name="restart-module"></a>Module opnieuw opstarten

De **methode RestartModule** maakt extern beheer van modules die op een IoT Edge-apparaat worden uitgevoerd, mogelijk. Als een module een mislukte status of ander ongezond gedrag rapporteert, u de IoT Edge-agent activeren om deze opnieuw te starten. Een opdracht voor een succesvolle herstart retourneert een lege payload en **'status': 200**.

De methode RestartModule is beschikbaar in IoT Edge-versie 1.0.9 en hoger. 

U de direct-methode RestartModule gebruiken op elke module die wordt uitgevoerd op een IoT Edge-apparaat, inclusief de edgeAgent-module zelf. Als u deze directe methode echter gebruikt om de edgeAgent uit te schakelen, ontvangt u geen succesresultaat omdat de verbinding wordt verstoord terwijl de module opnieuw wordt opgestart.

Bijvoorbeeld:

```azurecli
az iot hub invoke-module-method --method-name 'RestartModule' -n <hub name> -d <device name> -m '$edgeAgent' --method-payload \
'
    {
        "schemaVersion": "1.0",
        "id": "<module name>"
    }
'
```

Roep in de Azure-portal de `RestartModule` methode aan met de methodenaam en de volgende JSON-payload:

```json
{
    "schemaVersion": "1.0",
    "id": "<module name>"
}
```

![Directe methode 'RestartModule' in Azure-portal aanroepen](./media/how-to-edgeagent-direct-method/restartmodule-direct-method.png)

## <a name="experimental-methods"></a>Experimentele methoden

Nieuwe directe methodeopties zijn beschikbaar als experimentele functies om te testen, waaronder:

* [Uploadlogs:](https://github.com/Azure/iotedge/blob/master/doc/built-in-logs-pull.md)Haal modulelogboeken op en upload ze naar Azure Blob Storage.
* [GetTaskStatus:](https://github.com/Azure/iotedge/blob/master/doc/built-in-logs-pull.md#gettaskstatus)controleer de status van een aanvraag voor uploadlogboeken.
* [GetLogs:](https://github.com/Azure/iotedge/blob/master/doc/built-in-logs-pull.md#getlogs)Modulelogs inline ophalen in de reactie van de directe methode.

## <a name="next-steps"></a>Volgende stappen

[Eigenschappen van de IoT Edge-agent en IoT Edge-hubmoduletwins](module-edgeagent-edgehub.md)
