---
title: IoT Central beheren vanuit Azure CLI | Microsoft Docs
description: In dit artikel wordt beschreven hoe u uw IoT Central-toepassing maakt en beheert met behulp van CLI. U kunt de toepassing weer geven, wijzigen en verwijderen met behulp van CLI.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 08/23/2019
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: c5622f32dbf849b9a21a1fd2e458f35b8aa1d098
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2019
ms.locfileid: "74480389"
---
# <a name="manage-iot-central-from-azure-cli"></a>IoT Central beheren vanuit Azure CLI

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

In plaats van IoT Central-toepassingen te maken en te beheren op de website van [azure IOT Central Application Manager](https://aka.ms/iotcentral) , kunt u [Azure cli](/cli/azure/) gebruiken voor het beheren van uw toepassingen.

## <a name="prerequisites"></a>Vereisten

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Als u liever Azure CLI op uw lokale computer wilt uitvoeren, raadpleegt u [de Azure cli installeren](/cli/azure/install-azure-cli). Wanneer u Azure CLI lokaal uitvoert, gebruikt u de opdracht **AZ login** om u aan te melden bij Azure voordat u de opdrachten in dit artikel probeert uit te voeren.

## <a name="create-an-application"></a>Een app maken

Gebruik de opdracht [AZ iotcentral app Create](/cli/azure/iotcentral/app#az-iotcentral-app-create) om een IOT Central-toepassing te maken in uw Azure-abonnement. Bijvoorbeeld:

```azurecli-interactive
# Create a resource group for the IoT Central application
az group create --location "East US" \
    --name "MyIoTCentralResourceGroup"
```

```azurecli-interactive
# Create an IoT Central application
az iotcentral app create \
  --resource-group "MyIoTCentralResourceGroup" \
  --name "myiotcentralapp" --subdomain "mysubdomain" \
  --sku S1 --template "iotc-demo@1.0.0" \
  --display-name "My Custom Display Name"
```

Met deze opdrachten maakt u eerst een resource groep op de locatie VS-Oost voor de toepassing. In de volgende tabel worden de para meters beschreven die worden gebruikt met de opdracht **AZ iotcentral app Create** :

| Parameter         | Beschrijving |
| ----------------- | ----------- |
| resource-group    | De resource groep die de toepassing bevat. Deze resource groep moet al bestaan in uw abonnement. |
| locatie          | Deze opdracht maakt standaard gebruik van de locatie uit de resource groep. Op dit moment kunt u een IoT Central-toepassing maken in de **Verenigde Staten**, **Australië**, **Azië en Stille Oceaan**of in de **Europe** -locaties. |
| name              | De naam van de toepassing in de Azure Portal. |
| subdomein         | Het subdomein in de URL van de toepassing. In het voor beeld is de toepassings-URL https://mysubdomain.azureiotcentral.com. |
| sku               | Op dit moment is de enige waarde **S1** (Standard-laag). Zie [prijzen voor Azure IOT Central](https://azure.microsoft.com/pricing/details/iot-central/). |
| sjabloon          | De toepassings sjabloon die moet worden gebruikt. Zie de volgende tabel voor meer informatie: |
| weergave naam      | De naam van de toepassing, zoals deze wordt weer gegeven in de gebruikers interface. |

**Toepassings sjablonen met algemeen beschik bare functies**

| Naam van sjabloon            | Beschrijving |
| ------------------------ | ----------- |
| iotc-default@1.0.0       | Hiermee maakt u een lege toepassing die u kunt vullen met uw eigen apparaatsjablonen en apparaten. |
| iotc-demo@1.0.0          | Hiermee maakt u een toepassing die onder andere een vooraf gemaakte apparaatjabloon voor een gekoelde verkoopautomaat bevat. Gebruik deze sjabloon als u wilt beginnen met het verkennen van Azure IoT Central. |
| iotc-devkit-sample@1.0.0 | Hiermee maakt u een toepassing met apparaatsjablonen die u in staat stelt verbinding te maken met een MXChip- of Raspberry Pi-apparaat. Gebruik deze sjabloon als u een ontwikkelaar van een apparaat wilt experimenteren met een van deze apparaten. |


**Toepassings sjablonen met open bare preview-functies**

| Naam van sjabloon            | Beschrijving |
| ------------------------ | ----------- |
| iotc-pnp-preview@1.0.0   | Hiermee maakt u een lege Plug en Play-voorbeeld toepassing die u kunt vullen met uw eigen Apparaatinstellingen en apparaten. |
| iotc-condition@1.0.0     | Hiermee maakt u een toepassing met een sjabloon voor de bewaking van de voor waarden van een in-Store-analyse. Gebruik deze sjabloon om verbinding te maken en de opslag omgeving te bewaken. |
| iotc-consumption@1.0.0   | Hiermee maakt u een toepassing met water verbruik-bewakings sjabloon. Gebruik deze sjabloon om de water stroom te bewaken en te beheren. |
| iotc-distribution@1.0.0  | Hiermee maakt u een toepassing met een sjabloon voor digitale distributie. Gebruik deze sjabloon om de efficiëntie van de opslag van het magazijn te verbeteren door digitalizing van belang rijke activa en acties. |
| iotc-inventory@1.0.0     | Hiermee maakt u een toepassing met een Smart Inventory Management-sjabloon. Gebruik deze sjabloon om de ontvangst, product verplaatsing, cyclus telling en het bijhouden van Sens oren te automatiseren. |
| iotc-logistics@1.0.0     | Hiermee maakt u een toepassing met een verbonden logistiek-sjabloon. Gebruik deze sjabloon om uw verzen ding in realtime in de lucht, het water en de grond te volgen met locatie-en voorwaarde bewaking. |
| iotc-meter@1.0.0         | Hiermee maakt u een toepassing met een sjabloon voor slimme meter bewaking. Gebruik deze sjabloon om het energie verbruik en de netwerk status te bewaken en trends te identificeren voor het verbeteren van de klant ondersteuning en het beheer van slimme software.  |
| iotc-patient@1.0.0       | Hiermee maakt u een toepassing met een continue patiënten-bewakings sjabloon. Gebruik deze sjabloon om patiënten, nieuwe invoer en het beheer van ziekten uit te breiden. |
| iotc-power@1.0.0         | Hiermee maakt u een toepassing met behulp van een zonne-paneel bewakings sjabloon. Gebruik deze sjabloon om de status van een zonne paneel, trends van energie generatie te bewaken. |
| iotc-quality@1.0.0       | Hiermee maakt u een toepassing met bewakings sjabloon voor water kwaliteit. Gebruik deze sjabloon om de water kwaliteit digitaal te bewaken.|
| iotc-store@1.0.0         | Hiermee maakt u een toepassing met een in-Store-analyse sjabloon. Gebruik deze sjabloon om de afhandelings stroom in uw Store te controleren en te beheren. |
| iotc-waste@1.0.0         | Hiermee maakt u een toepassing met een verbonden afval beheer sjabloon. Gebruik deze sjabloon om de afval bakken en de Opera tors van het verzend veld te bewaken. |

> [!NOTE]
> De preview-toepassings sjablonen zijn momenteel alleen beschikbaar in de locaties **Europa** en **Verenigde Staten** .

## <a name="view-your-applications"></a>Uw toepassingen bekijken

Gebruik de opdracht [AZ iotcentral app List](/cli/azure/iotcentral/app#az-iotcentral-app-list) om uw IOT Central toepassingen te vermelden en meta gegevens weer te geven.

## <a name="modify-an-application"></a>Een toepassing wijzigen

Gebruik de opdracht [AZ iotcentral App Update](/cli/azure/iotcentral/app#az-iotcentral-app-update) om de meta gegevens van een IOT Central-toepassing bij te werken. Als u bijvoorbeeld de weergave naam van uw toepassing wilt wijzigen:

```azurecli-interactive
az iotcentral app update --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup \
  --set displayName="My new display name"
```

## <a name="remove-an-application"></a>Een toepassing verwijderen

Gebruik de opdracht [AZ iotcentral app delete](/cli/azure/iotcentral/app#az-iotcentral-app-delete) om een IOT Central-toepassing te verwijderen. Bijvoorbeeld:

```azurecli-interactive
az iotcentral app delete --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup
```

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u Azure IoT Central-toepassingen kunt beheren vanuit Azure CLI, is dit de voorgestelde volgende stap:

> [!div class="nextstepaction"]
> [Uw toepassing beheren](howto-administer.md)
