---
title: Azure IoT-apparaatbeheer met IoT-extensie voor Azure CLI | Microsoft Documenten
description: Gebruik de IoT-extensie voor Azure CLI-tool voor Azure IoT Hub-apparaatbeheer, met de Direct-methoden en de gewenste opties voor eigenschappenbeheer van de Tweeling.
author: chrissie926
manager: ''
keywords: azure iot-apparaatbeheer, azure iot-hub-apparaatbeheer, iot-apparaatbeheer voor apparaatbeheer van apparaten
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 01/16/2018
ms.author: menchi
ms.openlocfilehash: 88c3d1f4213b161d5e322349a7f0e1bc1dd952e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80239646"
---
# <a name="use-the-iot-extension-for-azure-cli-for-azure-iot-hub-device-management"></a>De IoT-extensie voor Azure CLI gebruiken voor Azure IoT Hub-apparaatbeheer

![End-to-end diagram](media/iot-hub-get-started-e2e-diagram/2.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

[De IoT-extensie voor Azure CLI](https://github.com/Azure/azure-iot-cli-extension) is een open-source IoT-extensie die bijdraagt aan de mogelijkheden van de [Azure CLI.](https://docs.microsoft.com/cli/azure/overview?view=azure-cli-latest) De Azure CLI bevat opdrachten voor interactie met Azure Resource Manager en beheereindpunten. U Azure CLI bijvoorbeeld gebruiken om een Azure VM of een IoT-hub te maken. Met een CLI-extensie kan een Azure-service de Azure CLI uitbreiden, zodat u toegang hebt tot aanvullende servicespecifieke mogelijkheden. De IoT-extensie geeft IoT-ontwikkelaars command-line toegang tot alle IoT Hub-, IoT Edge- en IoT Hub Device Provisioning Service-mogelijkheden.

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

| Beheeroptie          | Taak  |
|----------------------------|-----------|
| Directe methoden             | Laat een apparaat handelen, zoals het starten of stoppen van het verzenden van berichten of het opnieuw opstarten van het apparaat.                                        |
| Dubbele gewenste eigenschappen    | Plaats een apparaat in bepaalde toestanden, zoals het instellen van een LED op groen of het instellen van het telemetrie-verzendinterval op 30 minuten.         |
| Dubbele gerapporteerde eigenschappen   | De gerapporteerde status van een apparaat opteweeren. Het apparaat meldt bijvoorbeeld dat de LED nu knippert.                                    |
| Dubbele tags                  | Bewaar apparaatspecifieke metagegevens in de cloud. Bijvoorbeeld de inzetlocatie van een automaat.                         |
| Dubbele query's van het apparaat        | Vraag alle apparaattweelingen op om die tweelingen met willekeurige omstandigheden op te halen, zoals het identificeren van de apparaten die beschikbaar zijn voor gebruik. |

Zie [Device-to-cloud communicatiebegeleiding](iot-hub-devguide-d2c-guidance.md) en [communicatiebegeleiding](iot-hub-devguide-c2d-guidance.md)tussen apparaten naar de cloud voor meer gedetailleerde uitleg over de verschillen en richtlijnen voor het gebruik van deze opties.

Apparaatdubbels zijn JSON-documenten waarin statusinformatie van een apparaat (metagegevens, configuraties en voorwaarden) zijn opgeslagen. IoT Hub blijft bestaan voor elk apparaat dat er verbinding mee maakt. Zie Aan de slag [met apparaattweelingen](iot-hub-node-node-twin-getstarted.md)voor meer informatie over apparaattweelingen.

## <a name="what-you-learn"></a>Wat u leert

U leert de IoT-extensie voor Azure CLI te gebruiken met verschillende beheeropties op uw ontwikkelingsmachine.

## <a name="what-you-do"></a>Wat je doet

Voer Azure CLI en de IoT-extensie voor Azure CLI uit met verschillende beheeropties.

## <a name="what-you-need"></a>Wat u nodig hebt

* Voltooi de [Raspberry Pi online simulator](iot-hub-raspberry-pi-web-simulator-get-started.md) tutorial of een van de apparaat tutorials; bijvoorbeeld [Raspberry Pi met node.js](iot-hub-raspberry-pi-kit-node-get-started.md). Deze artikelen voldoen aan de volgende eisen:

  - Een actief Azure-abonnement.
  - Een Azure IoT-hub onder uw abonnement.
  - Een clienttoepassing die berichten verzendt naar uw Azure IoT-hub.

* Controleer tijdens deze zelfstudie of uw apparaat wordt uitgevoerd met de clienttoepassing.

* [Python 2.7x of Python 3.x](https://www.python.org/downloads/)

* De Azure CLI. Zie De Azure CLI [installeren](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)als u deze wilt installeren. Uw Azure CLI-versie moet minimaal 2.0.70 of hoger zijn. Gebruik `az –version` om de versie te valideren.

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

* Installeer de IoT-extensie. De eenvoudigste manier is `az extension add --name azure-iot` uit te voeren. [In het Leesmij-bestand bij de IoT-extensie](https://github.com/Azure/azure-iot-cli-extension/blob/master/README.md) worden verschillende manieren voor het installeren van de extensie beschreven.

## <a name="sign-in-to-your-azure-account"></a>Aanmelden bij uw Azure-account

Meld u aan bij uw Azure-account door de volgende opdracht uit te voeren:

```azurecli
az login
```

## <a name="direct-methods"></a>Directe methoden

```azurecli
az iot hub invoke-device-method --device-id <your device id> \
  --hub-name <your hub name> \
  --method-name <the method name> \
  --method-payload <the method payload>
```

## <a name="device-twin-desired-properties"></a>Apparaat twin gewenste eigenschappen

Stel een gewenst eigenschapsinterval = 3000 in door de volgende opdracht uit te voeren:

```azurecli
az iot hub device-twin update -n <your hub name> \
  -d <your device id> --set properties.desired.interval = 3000
```

Deze eigenschap kan worden gelezen vanaf uw apparaat.

## <a name="device-twin-reported-properties"></a>Gemelde eigenschappen van apparaattweeling

Download de gerapporteerde eigenschappen van het apparaat door de volgende opdracht uit te voeren:

```azurecli
az iot hub device-twin show -n <your hub name> -d <your device id>
```

Een van de twee gerapporteerde eigenschappen is $metadata.$lastUpdated, die de laatste keer dat de apparaat-app de gerapporteerde eigenschapset heeft bijgewerkt, weergeeft.

## <a name="device-twin-tags"></a>Dubbele tags van het apparaat

Geef de tags en eigenschappen van het apparaat weer door de volgende opdracht uit te voeren:

```azurecli
az iot hub device-twin show --hub-name <your hub name> --device-id <your device id>
```

Voeg een veldrol = temperatuur&vochtigheid toe aan het apparaat door de volgende opdracht uit te voeren:

```azurecli
az iot hub device-twin update \
  --hub-name <your hub name> \
  --device-id <your device id> \
  --set tags = '{"role":"temperature&humidity"}}'
```

## <a name="device-twin-queries"></a>Dubbele query's van het apparaat

Queryapparaten met een tag van rol = 'temperatuur&vochtigheid' door de volgende opdracht uit te voeren:

```azurecli
az iot hub query --hub-name <your hub name> \
  --query-command "SELECT * FROM devices WHERE tags.role = 'temperature&humidity'"
```

Bevraag alle apparaten behalve apparaten met een tag van rol = 'temperatuur&vochtigheid' door de volgende opdracht uit te voeren:

```azurecli
az iot hub query --hub-name <your hub name> \
  --query-command "SELECT * FROM devices WHERE tags.role != 'temperature&humidity'"
```

## <a name="next-steps"></a>Volgende stappen

U hebt geleerd hoe u device-to-cloud-berichten controleren en cloud-to-device-berichten verzenden tussen uw IoT-apparaat en Azure IoT Hub.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
