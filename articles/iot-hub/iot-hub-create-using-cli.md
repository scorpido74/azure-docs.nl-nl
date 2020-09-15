---
title: Een IoT Hub maken met behulp van Azure CLI | Microsoft Docs
description: Meer informatie over het gebruik van de Azure CLI-opdrachten voor het maken van een resource groep en het maken van een IoT-hub in de resource groep. Meer informatie over het verwijderen van de hub.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/23/2018
ms.author: robinsh
ms.openlocfilehash: 69372e4c212e2ce81bcd4c91d460aa191a1d3476
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/15/2020
ms.locfileid: "90087844"
---
# <a name="create-an-iot-hub-using-the-azure-cli"></a>Een IoT-hub maken met behulp van de Azure CLI

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

In dit artikel wordt beschreven hoe u een IoT-hub maakt met behulp van Azure CLI.

## <a name="prerequisites"></a>Vereisten

U hebt een Azure-abonnement nodig om deze procedure te volt ooien. Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-and-set-your-azure-account"></a>Meld u aan en stel uw Azure-account in

Als u Azure CLI lokaal uitvoert in plaats van Cloud Shell, moet u zich aanmelden bij uw Azure-account.

Voer bij de opdrachtprompt deze [aanmeldingsopdracht](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) uit:

   ```azurecli
   az login
   ```

Volg de instructies om te verifiëren met de code en meld u aan bij uw Azure-account via een webbrowser.

## <a name="create-an-iot-hub"></a>Een IoT Hub maken

Gebruik de Azure CLI om een resource groep te maken en vervolgens een IoT-hub toe te voegen.

1. Wanneer u een IoT-hub maakt, moet u deze maken in een resource groep. Gebruik een bestaande resourcegroep of voer de volgende [opdracht voor het maken van een resourcegroep](https://docs.microsoft.com/cli/azure/resource) uit:
    
   ```azurecli-interactive
   az group create --name {your resource group name} --location westus
   ```

   > [!TIP]
   > In het voorbeeld wordt de resourcegroep gemaakt in de locatie VS - west. U kunt een lijst met beschik bare locaties weer geven door de volgende opdracht uit te voeren: 
   >
   > ```azurecli-interactive
   > az account list-locations -o table
   > ```
   >

2. Voer de volgende [opdracht uit om een IOT-hub](https://docs.microsoft.com/cli/azure/iot/hub#az-iot-hub-create) in uw resource groep te maken met behulp van een wereld wijd unieke naam voor uw IOT-hub:
    
   ```azurecli-interactive
   az iot hub create --name {your iot hub name} \
      --resource-group {your resource group name} --sku S1
   ```

   [!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]


Met de vorige opdracht maakt u een IoT-hub in de prijs categorie S1 waarvoor u een factuur wilt maken. Zie [prijzen van Azure IOT hub](https://azure.microsoft.com/pricing/details/iot-hub/)voor meer informatie.

## <a name="remove-an-iot-hub"></a>Een IoT Hub verwijderen

U kunt Azure CLI gebruiken voor [het verwijderen van een afzonderlijke resource](https://docs.microsoft.com/cli/azure/resource), zoals een IOT-hub, of het verwijderen van een resource groep en alle bijbehorende resources, inclusief IOT-hubs.

Als u [een IOT-hub wilt verwijderen](https://docs.microsoft.com/cli/azure/iot/hub#az-iot-hub-delete), voert u de volgende opdracht uit:

```azurecli-interactive
az iot hub delete --name {your iot hub name} -\
  -resource-group {your resource group name}
```

Als u [een resource groep](https://docs.microsoft.com/cli/azure/group#az-group-delete) en alle bijbehorende resources wilt verwijderen, voert u de volgende opdracht uit:

```azurecli-interactive
az group delete --name {your resource group name}
```

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer informatie over het gebruik van een IoT hub:

* [Ontwikkelaars handleiding IoT Hub](iot-hub-devguide.md)
* [De Azure Portal gebruiken om IoT Hub te beheren](iot-hub-create-through-portal.md)
