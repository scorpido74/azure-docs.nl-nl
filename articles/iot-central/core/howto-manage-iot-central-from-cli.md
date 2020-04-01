---
title: IoT Central beheren vanuit Azure CLI | Microsoft Documenten
description: In dit artikel wordt beschreven hoe u uw IoT Central-toepassing maakt en beheert met CLI. U de toepassing bekijken, wijzigen en verwijderen met CLI.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 03/27/2020
ms.topic: how-to
manager: philmea
ms.openlocfilehash: df24a2dc6e9bd058a2f8b1355b8760653ed3128a
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80365523"
---
# <a name="manage-iot-central-from-azure-cli"></a>IoT Central beheren vanuit Azure CLI

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

In plaats van IoT Central-toepassingen te maken en te beheren op de azure [IoT Central-toepassingsbeheerwebsite,](https://aka.ms/iotcentral) u [Azure CLI](/cli/azure/) gebruiken om uw toepassingen te beheren.

## <a name="prerequisites"></a>Vereisten

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Zie [Azure CLI installeren](/cli/azure/install-azure-cli)als u Azure CLI liever op uw lokale machine uitvoert. Wanneer u Azure CLI lokaal uitvoert, gebruikt u de opdracht **az-aanmelding** om u aan te melden bij Azure voordat u de opdrachten in dit artikel probeert.

> [!TIP]
> Zie [Het actieve abonnement wijzigen](/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest#change-the-active-subscription)als u uw CLI-opdrachten in een ander Azure-abonnement moet uitvoeren.

## <a name="install-the-extension"></a>De extensie installeren

De opdrachten in dit artikel maken deel uit van de **azure-iot CLI-extensie.** Voer de volgende opdracht uit om de extensie te installeren:

```azurecli-interactive
az extension add --name azure-iot
```

## <a name="create-an-application"></a>Een app maken

Gebruik de opdracht [az iotcentral-app om](/cli/azure/iotcentral/app#az-iotcentral-app-create) een IoT Central-toepassing in uw Azure-abonnement te maken. Bijvoorbeeld:

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
  --sku ST1 --template "iotc-pnp-preview@1.0.0" \
  --display-name "My Custom Display Name"
```

Met deze opdrachten wordt eerst een resourcegroep in de regio Oost-VS voor de toepassing gemaakt. In de volgende tabel worden de parameters beschreven die worden gebruikt met de opdracht **az iotcentral app create:**

| Parameter         | Beschrijving |
| ----------------- | ----------- |
| resource-group    | De resourcegroep die de toepassing bevat. Deze brongroep moet al in uw abonnement bestaan. |
| location          | Standaard gebruikt deze opdracht de locatie uit de resourcegroep. Momenteel u een IoT Central-toepassing maken in de regio's **Australië,** **Azië-Pacific,** **Europa,** **de Verenigde Staten,** **het Verenigd Koninkrijk**en **Japan.** |
| name              | De naam van de toepassing in de Azure-portal. |
| Subdomein         | Het subdomein in de URL van de toepassing. In het voorbeeld is `https://mysubdomain.azureiotcentral.com`de URL van de toepassing . |
| sku               | Momenteel u gebruik maken van **ST1** of **ST2**. Zie [Azure IoT Central-prijzen](https://azure.microsoft.com/pricing/details/iot-central/). |
| sjabloon          | De toepassingssjabloon die u wilt gebruiken. Zie de volgende tabel voor meer informatie. |
| weergavenaam      | De naam van de toepassing zoals weergegeven in de gebruikersinterface. |

[!INCLUDE [iot-central-template-list](../../../includes/iot-central-template-list.md)]

## <a name="view-your-applications"></a>Uw toepassingen bekijken

Gebruik de [opdracht lijst met az iotcentral-apps](/cli/azure/iotcentral/app#az-iotcentral-app-list) om uw IoT Central-toepassingen weer te geven en metagegevens weer te geven.

## <a name="modify-an-application"></a>Een toepassing wijzigen

Gebruik de opdracht voor het updaten van [az iotcentral-apps](/cli/azure/iotcentral/app#az-iotcentral-app-update) om de metadata van een IoT Central-toepassing bij te werken. U bijvoorbeeld de weergavenaam van uw toepassing wijzigen:

```azurecli-interactive
az iotcentral app update --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup \
  --set displayName="My new display name"
```

## <a name="remove-an-application"></a>Een toepassing verwijderen

Gebruik de opdracht [verwijderen van de AZ IotCentral-app](/cli/azure/iotcentral/app#az-iotcentral-app-delete) om een IoT Central-toepassing te verwijderen. Bijvoorbeeld:

```azurecli-interactive
az iotcentral app delete --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup
```

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u Azure IoT Central-toepassingen vanuit Azure CLI beheren, volgt hier de voorgestelde volgende stap:

> [!div class="nextstepaction"]
> [Uw toepassing beheren](howto-administer.md)
