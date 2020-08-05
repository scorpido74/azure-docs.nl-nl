---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 03/17/2020
ms.custom: references_regions
ms.openlocfilehash: adc621f932462422202d9f16fd539f5ecc7c3d8e
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87336890"
---
## <a name="prepare-an-iot-hub"></a>Een IoT-hub voorbereiden

U hebt ook een Iot-hub van Azure in uw Azure-abonnement nodig om de stappen in dit artikel uit te voeren. Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

Als u de Azure CLI lokaal gebruikt, meldt u zich eerst aan bij uw Azure-abonnement met behulp van `az login`. Als u deze opdrachten uitvoert in de Azure Cloud Shell, bent u automatisch aangemeld.

Als u de Azure CLI lokaal gebruikt, moet de `az`-versie **2.8.0** of hoger zijn. De Azure Cloud Shell maakt gebruik van de nieuwste versie. Gebruik de opdracht `az --version` om de versie te controleren die op uw computer is geïnstalleerd.

Voer de volgende opdracht uit om de Microsoft Azure IoT-extensie voor Azure CLI toe te voegen aan uw exemplaar:

```azurecli-interactive
az extension add --name azure-iot
```

Als u nog geen IoT-hub hebt om te gebruiken, voert u de volgende opdrachten uit om een resourcegroep en een IoT-hub met een gratis laag in uw abonnement te maken. Vervang `<YourIoTHubName>` door een hub-naam van uw keuze:

```azurecli-interactive
az group create --name my-pnp-resourcegroup \
    --location centralus
az iot hub create --name <YourIoTHubName> \
    --resource-group my-pnp-resourcegroup --sku F1
```

> [!NOTE]
> IoT Plug en Play is momenteel beschikbaar in IoT-hubs die zijn gemaakt in de regio's VS - centraal, Europa - noord en Japan - oost. IoT Plug en Play-ondersteuning is niet opgenomen in de basislaag van IoT-hubs.

Voer de volgende opdracht uit om de apparaat-id in uw IoT-hub te maken. Vervang de tijdelijke aanduidingen `<YourIoTHubName>` en `<YourDeviceID>` door uw eigen _IoT Hub-naam_ en een _apparaat-id_ van uw keuze.

```azurecli-interactive
az iot hub device-identity create --hub-name <YourIoTHubName> --device-id <YourDeviceID>
```
