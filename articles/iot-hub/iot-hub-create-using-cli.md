---
title: Een IoT-hub maken met Azure CLI | Microsoft Documenten
description: Meer informatie over het gebruik van de Azure CLI-opdrachten om een resourcegroep te maken en vervolgens een IoT-hub in de brongroep te maken. Lees ook hoe u de hub verwijdert.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/23/2018
ms.author: robinsh
ms.openlocfilehash: e7253747d95917e34725cc7c6c034b1306f65e50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284718"
---
# <a name="create-an-iot-hub-using-the-azure-cli"></a>Een IoT-hub maken met de Azure CLI

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

In dit artikel ziet u hoe u een IoT-hub maakt met Azure CLI.

## <a name="prerequisites"></a>Vereisten

Om deze how-to te voltooien, hebt u een Azure-abonnement nodig. Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-and-set-your-azure-account"></a>Aanmelden en uw Azure-account instellen

Als u Azure CLI lokaal uitvoert in plaats van Cloud Shell te gebruiken, moet u zich aanmelden bij uw Azure-account.

Voer bij de opdrachtprompt deze [aanmeldingsopdracht](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) uit:

   ```azurecli
   az login
   ```

Volg de instructies om te verifiëren met de code en meld u aan bij uw Azure-account via een webbrowser.

## <a name="create-an-iot-hub"></a>Een IoT Hub maken

Gebruik de Azure CLI om een brongroep te maken en voeg vervolgens een IoT-hub toe.

1. Wanneer u een IoT-hub maakt, moet u deze maken in een resourcegroep. Gebruik een bestaande resourcegroep of voer de volgende [opdracht voor het maken van een resourcegroep](https://docs.microsoft.com/cli/azure/resource) uit:
    
   ```azurecli
   az group create --name {your resource group name} --location westus
   ```

   > [!TIP]
   > In het voorbeeld wordt de resourcegroep gemaakt in de locatie VS - west. U een lijst met beschikbare locaties bekijken door deze opdracht uit te voeren: 
   >
   >``` bash
   >az account list-locations -o table
   >```
   >

2. Voer de volgende [opdracht uit om een IoT-hub](https://docs.microsoft.com/cli/azure/iot/hub#az-iot-hub-create) in uw resourcegroep te maken, met een wereldwijd unieke naam voor uw IoT-hub:
    
   ```azurecli
   az iot hub create --name {your iot hub name} \
      --resource-group {your resource group name} --sku S1
   ```

   [!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]


Met de vorige opdracht wordt een IoT-hub gemaakt in de prijscategorie S1 waarvoor u wordt gefactureerd. Zie [Azure IoT Hub-prijzen](https://azure.microsoft.com/pricing/details/iot-hub/)voor meer informatie.

## <a name="remove-an-iot-hub"></a>Een IoT-hub verwijderen

U Azure CLI gebruiken om een afzonderlijke bron te [verwijderen,](https://docs.microsoft.com/cli/azure/resource)zoals een IoT-hub, of om een brongroep en al zijn bronnen, inclusief eventuele IoT-hubs, te verwijderen.

Als [u een IoT-hub wilt verwijderen,](https://docs.microsoft.com/cli/azure/iot/hub#az-iot-hub-delete)voert u de volgende opdracht uit:

```azurecli
az iot hub delete --name {your iot hub name} -\
  -resource-group {your resource group name}
```

Voer de volgende opdracht uit om [een brongroep](https://docs.microsoft.com/cli/azure/group#az-group-delete) en al zijn bronnen te verwijderen:

```azurecli
az group delete --name {your resource group name}
```

## <a name="next-steps"></a>Volgende stappen

Zie de volgende artikelen voor meer informatie over het gebruik van een IoT-hub:

* [Handleiding voor IoT Hub-ontwikkelaars](iot-hub-devguide.md)
* [De Azure-portal gebruiken om IoT Hub te beheren](iot-hub-create-through-portal.md)
