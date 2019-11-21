---
title: Set up Azure IoT Hub Device Provisioning Service using Azure CLI
description: Quickstart - Set up the Azure IoT Hub Device Provisioning Service using Azure CLI
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: 6406929c3abc3612da2c27edc45e10fd84883d73
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74228549"
---
# <a name="quickstart-set-up-the-iot-hub-device-provisioning-service-with-azure-cli"></a>Quickstart: Set up the IoT Hub Device Provisioning Service with Azure CLI

De Azure CLI wordt gebruikt voor het maken en beheren van Azure-resources vanaf de opdrachtregel of in scripts. This quickstart details using the Azure CLI to create an IoT hub and an IoT Hub Device Provisioning Service, and to link the two services together. 

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

> [!IMPORTANT]
> Both the IoT hub and the provisioning service you create in this quickstart will be publicly discoverable as DNS endpoints. Voorkom dat u gevoelige informatie wijzigt als u de namen wijzigt die voor deze resources worden gebruikt.
>

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]


## <a name="create-a-resource-group"></a>Een resourcegroep maken

Een resourcegroep maken met de opdracht [az group create](/cli/azure/group#az-group-create). Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. 

In het volgende voorbeeld wordt een resourcegroep met de naam *my-sample-resource-group* gemaakt op de locatie *westus*.

```azurecli-interactive 
az group create --name my-sample-resource-group --location westus
```

> [!TIP]
> In het voorbeeld wordt de resourcegroep gemaakt op de locatie US - west. U kunt een lijst met beschikbare locaties weergeven met de opdracht `az account list-locations -o table`.
>
>

## <a name="create-an-iot-hub"></a>Een IoT Hub maken

Maak een IoT Hub met de opdracht [az iot hub create](/cli/azure/iot/hub#az-iot-hub-create).

In het volgende voorbeeld wordt een IoT-hub met de naam *my-sample-hub* op de locatie *westus* gemaakt. An IoT hub name must be globally unique in Azure, so you may want to add a unique prefix or suffix to the example name, or choose a new name altogether. Make sure your name follows proper naming conventions for an IoT hub: it should be 3-50 characters in length, and can contain only upper or lower case alphanumeric characters or hyphens ('-'). 

```azurecli-interactive 
az iot hub create --name my-sample-hub --resource-group my-sample-resource-group --location westus
```

## <a name="create-a-device-provisioning-service"></a>Create a Device Provisioning Service

Create a Device Provisioning Service with the [az iot dps create](/cli/azure/iot/dps#az-iot-dps-create) command. 

The following example creates a provisioning service named *my-sample-dps* in the *westus* location. You will also need to choose a globally unique name for your own provisioning service. Make sure it follows proper naming conventions for an IoT Hub Device Provisioning Service: it should be 3-64 characters in length and can contain only upper or lower case alphanumeric characters or hyphens ('-').

```azurecli-interactive 
az iot dps create --name my-sample-dps --resource-group my-sample-resource-group --location westus
```

> [!TIP]
> In het voorbeeld wordt de inrichtingsservice gemaakt op de locatie US - west. U kunt een lijst met beschikbare locaties weergeven door de opdracht `az provider show --namespace Microsoft.Devices --query "resourceTypes[?resourceType=='ProvisioningServices'].locations | [0]" --out table` uit te voeren of door naar de pagina [Status van Azure](https://azure.microsoft.com/status/) te gaan en te zoeken naar 'device provisioning service'. In commands, locations can be specified either in one word or multi-word format; for example: westus, West US, WEST US, etc. The value is not case sensitive. Als u meerdere woorden gebruikt om een locatie op te geven, voeg dan de waarde toe tussen aanhalingstekens; bijvoorbeeld: `-- location "West US"`.
>

## <a name="get-the-connection-string-for-the-iot-hub"></a>De verbindingsreeks voor de IoT-hub ophalen

U hebt de verbindingsreeks voor uw IoT-hub nodig om deze aan de Device Provisioning Service te koppelen. Gebruik de opdracht [az iot hub show-connection-string](/cli/azure/iot/hub#az-iot-hub-show-connection-string) om de verbindingsreeks op te halen en om met de uitvoer ervan een variabele in te stellen die u gaat gebruiken om de twee resources aan elkaar te koppelen. 

The following example sets the *hubConnectionString* variable to the value of the connection string for the primary key of the hub's *iothubowner* policy (the `--policy-name` parameter can be used to specify a different policy). Trade out *my-sample-hub* for the unique IoT hub name you chose earlier. De opdracht maakt gebruik van de [query-](/cli/azure/query-azure-cli) en [uitvoer](/cli/azure/format-output-azure-cli#tsv-output-format)opties van de Azure CLI om de verbindingsreeks op te halen uit de uitvoer van de opdracht.

```azurecli-interactive 
hubConnectionString=$(az iot hub show-connection-string --name my-sample-hub --key primary --query connectionString -o tsv)
```

U kunt de opdracht `echo` gebruiken om de verbindingsreeks weer te geven.

```azurecli-interactive 
echo $hubConnectionString
```

> [!NOTE]
> Deze twee opdrachten zijn geldig voor een host die wordt uitgevoerd onder Bash. Als u van een lokale Windows-/CMD-shell of een PowerShell-host gebruikmaakt, moet u de opdrachten wijzigen zodat de juiste syntaxis voor deze omgeving wordt gebruikt.
>

## <a name="link-the-iot-hub-and-the-provisioning-service"></a>De IoT-hub en de inrichtingsservice aan elkaar koppelen

U kunt de IoT-hub en uw inrichtingsservice aan elkaar koppelen met de opdracht [az iot dps linked-hub create](/cli/azure/iot/dps/linked-hub#az-iot-dps-linked-hub-create). 

The following example links an IoT hub named *my-sample-hub* in the *westus* location and a Device Provisioning Service named *my-sample-dps*. Trade out these names for the unique IoT hub and Device Provisioning Service names you chose earlier. The command uses the connection string for your IoT hub that was stored in the *hubConnectionString* variable in the previous step.

```azurecli-interactive 
az iot dps linked-hub create --dps-name my-sample-dps --resource-group my-sample-resource-group --connection-string $hubConnectionString --location westus
```

The command may take a few minutes to complete.

## <a name="verify-the-provisioning-service"></a>De inrichtingsservice controleren

De details ophalen uit uw inrichtingsservice met de opdracht [az iot dps show](/cli/azure/iot/dps#az-iot-dps-show).

In het volgende voorbeeld worden de details van een inrichtingsservice met de naam *my-sample-dps* opgehaald. Trade out this name for your own Device Provisioning Service name.

```azurecli-interactive
az iot dps show --name my-sample-dps
```
De gekoppelde IoT-hub wordt weergegeven in de verzameling *properties.iotHubs*.

![Verify Provisioning Service](./media/quick-setup-auto-provision-cli/verify-provisioning-service.png)

## <a name="clean-up-resources"></a>Resources opschonen

Andere Quick Starts in deze verzameling zijn op deze Quick Start gebaseerd. Als u van plan bent om door te gaan met andere Quick Starts of met de zelfstudies, verwijdert u de resources die u in deze Quick Start hebt gemaakt niet. Als u niet van plan bent om door te gaan, kunt u de volgende opdrachten gebruiken om de inrichtingsservice, de IoT-hub of de resourcegroep en alle bijbehorende resources te verwijderen. Replace the names of the resources written below with the names of your own resources.

Voer de opdracht [az iot dps delete](/cli/azure/iot/dps#az-iot-dps-delete) uit om de inrichtingsservice te verwijderen:

```azurecli-interactive
az iot dps delete --name my-sample-dps --resource-group my-sample-resource-group
```
Voer de opdracht [az iot hub delete](/cli/azure/iot/hub#az-iot-hub-delete) uit om de IoT-hub te verwijderen:

```azurecli-interactive
az iot hub delete --name my-sample-hub --resource-group my-sample-resource-group
```

Voer de opdracht [az group delete](/cli/azure/group#az-group-delete) uit om de resourcegroep en alle bijbehorende resources te verwijderen:

```azurecli-interactive
az group delete --name my-sample-resource-group
```

## <a name="next-steps"></a>Volgende stappen

In this quickstart, you’ve deployed an IoT hub and a Device Provisioning Service instance, and linked the two resources. To learn how to use this setup to provision a simulated device, continue to the quickstart for creating a simulated device.

> [!div class="nextstepaction"]
> [Quickstart to create a simulated device](./quick-create-simulated-device.md)
