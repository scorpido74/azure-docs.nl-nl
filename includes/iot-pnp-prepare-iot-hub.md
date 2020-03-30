---
author: baanders
ms.author: baanders
ms.service: iot-pnp
ms.topic: include
ms.date: 10/24/2019
ms.openlocfilehash: a3340eb9e53afa83c35109bad7d22f81413dd644
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80234251"
---
## <a name="prepare-an-iot-hub"></a>Een IoT-hub voorbereiden

U hebt ook een Azure IoT-hub in uw Azure-abonnement nodig om deze quickstart te voltooien. Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint. Als u geen IoT-hub hebt, volgt u [deze instructies om er een te maken.](../articles/iot-hub/iot-hub-create-using-cli.md)

Als u de Azure CLI lokaal gebruikt, meldt u `az login`zich eerst aan bij uw Azure-abonnement met behulp van . Als u deze opdrachten uitvoert in de Azure Cloud Shell, wordt u automatisch aangemeld.

Als u de Azure CLI lokaal `az` gebruikt, moet de versie **2.0.73** of hoger zijn. de Azure Cloud Shell gebruikt de nieuwste versie. Gebruik `az --version` de opdracht om de versie te controleren die op uw machine is geïnstalleerd.

Voer de volgende opdracht uit om de Microsoft Azure IoT-extensie voor Azure CLI aan uw instantie toe te voegen:

```azurecli-interactive
az extension add --name azure-iot
```

Voer de volgende opdracht uit om de apparaatidentiteit in uw IoT-hub te maken. Vervang de **placeholders YourIoTHubName** en **YourDeviceID** door uw eigen _IoT Hub-naam_ en een _apparaat-id_ naar keuze.

```azurecli-interactive
az iot hub device-identity create --hub-name <YourIoTHubName> --device-id <YourDeviceID>
```

Voer de volgende opdracht uit om de _tekenreeks voor apparaatverbinding te_ krijgen voor het apparaat dat u zojuist hebt geregistreerd (let op voor gebruik later):

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output table
```
