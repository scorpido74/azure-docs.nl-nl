---
title: Azure IoT Hub Device Provisioning Service instellen met Azure CLI
description: Snelstart - De DPS (Azure IoT Hub Device Provisioning Service) instellen met Azure CLI
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: ea1cae1f5a30d4cd76df39fec43f3818178fc213
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77484193"
---
# <a name="quickstart-set-up-the-iot-hub-device-provisioning-service-with-azure-cli"></a>Snelstart: de Service voor het inrichten van IoT-hub-apparaten instellen met Azure CLI

De Azure CLI wordt gebruikt voor het maken en beheren van Azure-resources vanaf de opdrachtregel of in scripts. Met deze quickstart wordt met behulp van de Azure CLI een IoT-hub en een IoT Hub Device Provisioning Service gemaakt en worden de twee services aan elkaar gekoppeld. 

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

> [!IMPORTANT]
> Zowel de IoT-hub als de inprovisioningservice die u in deze quickstart maakt, zijn openbaar te ontdekken als DNS-eindpunten. Voorkom dat u gevoelige informatie wijzigt als u de namen wijzigt die voor deze resources worden gebruikt.
>

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]


## <a name="create-a-resource-group"></a>Een resourcegroep maken

Een resourcegroep maken met de opdracht [az group create](/cli/azure/group#az-group-create). Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. 

In het volgende voorbeeld wordt een resourcegroep met de naam *my-sample-resource-group* gemaakt op de locatie *westus*.

```azurecli-interactive 
az group create --name my-sample-resource-group --location westus
```

> [!TIP]
> In het voorbeeld wordt de resourcegroep gemaakt op de locatie VS - west. U kunt een lijst met beschikbare locaties weergeven met de opdracht `az account list-locations -o table`.
>
>

## <a name="create-an-iot-hub"></a>Een IoT Hub maken

Maak een IoT Hub met de opdracht [az iot hub create](/cli/azure/iot/hub#az-iot-hub-create).

In het volgende voorbeeld wordt een IoT-hub met de naam *my-sample-hub* op de locatie *westus* gemaakt. Een IoT-hubnaam moet wereldwijd uniek zijn in Azure, dus u een uniek voorvoegsel of achtervoegsel aan de voorbeeldnaam toevoegen of een nieuwe naam helemaal kiezen. Zorg ervoor dat uw naam de juiste naamgevingsconventies voor een IoT-hub volgt: het moet 3-50 tekens lang zijn en alleen alfanumerieke tekens of koppeltekens ('-') in hoofdletters of kleine letters bevatten. 

```azurecli-interactive 
az iot hub create --name my-sample-hub --resource-group my-sample-resource-group --location westus
```

## <a name="create-a-device-provisioning-service"></a>Een service voor apparaatinrichting maken

Maak een Device Provisioning Service met de [opdracht az iot dps create.](/cli/azure/iot/dps#az-iot-dps-create) 

In het volgende voorbeeld wordt een inrichtingsservice met de naam *my-sample-dps* op de *locatie Westus* gemaakt. U moet ook een wereldwijd unieke naam kiezen voor uw eigen inrichtingsservice. Zorg ervoor dat het de juiste naamgevingsconventies voor een IoT Hub Device Provisioning Service volgt: het moet 3-64 tekens lang zijn en alleen alfanumerieke tekens of koppeltekens ('-') in hoofdletters of kleine letters bevatten.

```azurecli-interactive 
az iot dps create --name my-sample-dps --resource-group my-sample-resource-group --location westus
```

> [!TIP]
> In het voorbeeld wordt de inrichtingsservice gemaakt op de locatie VS - west. U kunt een lijst met beschikbare locaties weergeven door de opdracht `az provider show --namespace Microsoft.Devices --query "resourceTypes[?resourceType=='ProvisioningServices'].locations | [0]" --out table` uit te voeren of door naar de pagina [Status van Azure](https://azure.microsoft.com/status/) te gaan en te zoeken naar 'device provisioning service'. In opdrachten kunnen locaties worden opgegeven in één woord- of meerdere woordindeling. bijvoorbeeld: westus, West US, WEST US, enz. De waarde is niet hoofdlettergevoelig. Als u meerdere woorden gebruikt om een locatie op te geven, voeg dan de waarde toe tussen aanhalingstekens; bijvoorbeeld: `--location "West US"`.
>

## <a name="get-the-connection-string-for-the-iot-hub"></a>De verbindingsreeks voor de IoT-hub ophalen

U hebt de verbindingsreeks voor uw IoT-hub nodig om deze aan de Device Provisioning Service te koppelen. Gebruik de opdracht [az iot hub show-connection-string](/cli/azure/iot/hub#az-iot-hub-show-connection-string) om de verbindingsreeks op te halen en om met de uitvoer ervan een variabele in te stellen die u gaat gebruiken om de twee resources aan elkaar te koppelen. 

In het volgende voorbeeld wordt de variabele *hubConnectionString* ingesteld op de waarde van de verbindingstekenreeks voor de primaire sleutel van het *iothub-beleid* van de hub (de `--policy-name` parameter kan worden gebruikt om een ander beleid op te geven). Ruil *mijn-sample-hub* in voor de unieke IoT-hubnaam die u eerder hebt gekozen. De opdracht maakt gebruik van de [query-](/cli/azure/query-azure-cli) en [uitvoer](/cli/azure/format-output-azure-cli#tsv-output-format)opties van de Azure CLI om de verbindingsreeks op te halen uit de uitvoer van de opdracht.

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

In het volgende voorbeeld wordt een IoT-hub met de naam *my-sample-hub* op de *locatie Westus* gekoppeld en een apparaatinrichtingsservice met de naam *my-sample-dps*. Ruil deze namen in voor de unieke IoT-hub- en Apparaatinrichtingsservicenamen die u eerder hebt gekozen. De opdracht gebruikt de verbindingstekenreeks voor uw IoT-hub die in de vorige stap is opgeslagen in de variabele *hubConnectionString.*

```azurecli-interactive 
az iot dps linked-hub create --dps-name my-sample-dps --resource-group my-sample-resource-group --connection-string $hubConnectionString --location westus
```

Het kan enkele minuten duren voordat de opdracht is voltooid.

## <a name="verify-the-provisioning-service"></a>De inrichtingsservice controleren

De details ophalen uit uw inrichtingsservice met de opdracht [az iot dps show](/cli/azure/iot/dps#az-iot-dps-show).

In het volgende voorbeeld worden de details van een inrichtingsservice met de naam *my-sample-dps* opgehaald. Ruil deze naam in voor uw eigen naam van de Service voor apparaatvoorziening.

```azurecli-interactive
az iot dps show --name my-sample-dps
```
De gekoppelde IoT-hub wordt weergegeven in de verzameling *properties.iotHubs*.

![De inrichtingsservice controleren](./media/quick-setup-auto-provision-cli/verify-provisioning-service.png)

## <a name="clean-up-resources"></a>Resources opschonen

Andere Quick Starts in deze verzameling zijn op deze Quick Start gebaseerd. Als u van plan bent om door te gaan met andere Quick Starts of met de zelfstudies, verwijdert u de resources die u in deze Quick Start hebt gemaakt niet. Als u niet van plan bent om door te gaan, kunt u de volgende opdrachten gebruiken om de inrichtingsservice, de IoT-hub of de resourcegroep en alle bijbehorende resources te verwijderen. Vervang de namen van de onderstaande bronnen door de namen van uw eigen bronnen.

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

In deze quickstart hebt u een IoT-hub en een instantie voor apparaatinrichtingsservice geïmplementeerd en de twee resources gekoppeld. Ga door met de snelle start voor het maken van een gesimuleerd apparaat om deze instelling te gebruiken voor het inrichten van een gesimuleerd apparaat.

> [!div class="nextstepaction"]
> [Snel start om een gesimuleerd apparaat te maken](./quick-create-simulated-device.md)
