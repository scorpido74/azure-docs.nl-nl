---
author: baanders
ms.author: baanders
ms.service: iot-pnp
ms.topic: include
ms.date: 10/24/2019
ms.openlocfilehash: 6b6c481a7443953a70fe3db45121f5f9982d8710
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2019
ms.locfileid: "74152005"
---
## <a name="prepare-an-iot-hub"></a>Een IoT-hub voorbereiden

U hebt ook een Azure IoT hub in uw Azure-abonnement nodig om deze Quick Start te volt ooien. Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint. Als u geen IoT-hub hebt, volgt u [deze instructies om er een te maken](../articles/iot-hub/iot-hub-create-using-cli.md).

> [!IMPORTANT]
> Tijdens de open bare preview zijn IoT-Plug en Play-functies alleen beschikbaar voor IoT-hubs die zijn gemaakt in de regio's **VS-centraal**, **Europa-Noord**en **Japan-Oost** .

Voer de volgende opdracht uit om de Microsoft Azure IoT-extensie voor Azure CLI toe te voegen aan uw Cloud Shell-exemplaar:

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

Voer de volgende opdracht uit om de apparaat-id in uw IoT-hub te maken. Vervang de tijdelijke aanduidingen **YourIoTHubName** en **YourDeviceID** door uw eigen _IOT hub naam_ en de _apparaat-id_ van uw keuze.

```azurecli-interactive
az iot hub device-identity create --hub-name <YourIoTHubName> --device-id <YourDeviceID>
```

Voer de volgende opdracht uit om de _apparaat-Connection String_ op te halen voor het apparaat dat u zojuist hebt geregistreerd (Houd er rekening mee voor later gebruik):

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDevice> --output table
```

Voer de volgende opdracht uit om de _IOT hub-Connection String_ voor uw hub op te halen (Let op later gebruik):

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```