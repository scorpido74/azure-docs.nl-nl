---
author: baanders
ms.author: baanders
ms.service: iot-pnp
ms.topic: include
ms.date: 10/24/2019
ms.openlocfilehash: 3f3e7c54ec59c42ed102da682fc9aa077709ef3f
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2020
ms.locfileid: "78261949"
---
## <a name="prepare-an-iot-hub"></a>Een IoT-hub voorbereiden

U hebt ook een Azure IoT hub in uw Azure-abonnement nodig om deze Quick Start te volt ooien. Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint. Als u geen IoT-hub hebt, volgt u [deze instructies om er een te maken](../articles/iot-hub/iot-hub-create-using-cli.md).

> [!IMPORTANT]
> Tijdens de open bare preview zijn IoT-Plug en Play-functies alleen beschikbaar voor IoT-hubs die zijn gemaakt in de regio's **VS-centraal**, **Europa-Noord**en **Japan-Oost** .

Als u de Azure CLI lokaal gebruikt, meldt u zich eerst aan bij uw Azure-abonnement met behulp van `az login`. Als u deze opdrachten uitvoert in de Azure Cloud Shell, bent u automatisch aangemeld.

Als u de Azure CLI lokaal gebruikt, moet de `az` versie **2.0.73** of hoger zijn. de Azure Cloud Shell maakt gebruik van de nieuwste versie. Gebruik de `az --version` opdracht om de versie te controleren die op uw computer is geïnstalleerd.

Voer de volgende opdracht uit om de Microsoft Azure IoT-extensie voor Azure CLI toe te voegen aan uw exemplaar:

```azurecli-interactive
az extension add --name azure-iot
```

Voer de volgende opdracht uit om de apparaat-id in uw IoT-hub te maken. Vervang de tijdelijke aanduidingen **YourIoTHubName** en **YourDeviceID** door uw eigen _IOT hub naam_ en de _apparaat-id_ van uw keuze.

```azurecli-interactive
az iot hub device-identity create --hub-name <YourIoTHubName> --device-id <YourDeviceID>
```

Voer de volgende opdracht uit om de _apparaat-Connection String_ op te halen voor het apparaat dat u zojuist hebt geregistreerd (Houd er rekening mee voor later gebruik):

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output table
```