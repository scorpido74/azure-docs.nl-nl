---
title: 'Snelstartgids: een Device Provisioning Service instellen met behulp van Azure CLI'
description: Azure-snelstart - Azure IoT Hub Device Provisioning-service instellen met de Azure CLI
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 20ad503582a35c1b47e90ca1c8c53af2c10fc6f8
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151941"
---
# <a name="quickstart-set-up-the-iot-hub-device-provisioning-service-with-azure-cli"></a>Snelstartgids: de IoT Hub Device Provisioning Service instellen met Azure CLI

De Azure CLI wordt gebruikt voor het maken en beheren van Azure-resources vanaf de opdrachtregel of in scripts. In deze snelstartgids vindt u informatie over het gebruik van de Azure CLI om een IoT-hub en een IoT Hub Device Provisioning Service te maken en om de twee services samen te koppelen. 

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

> [!IMPORTANT]
> De IoT-hub en de inrichtings service die u in deze Quick Start maakt, kunnen openbaar worden gedetecteerd als DNS-eind punten. Voorkom dat u gevoelige informatie wijzigt als u de namen wijzigt die voor deze resources worden gebruikt.
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

In het volgende voorbeeld wordt een IoT-hub met de naam *my-sample-hub* op de locatie *westus* gemaakt. De naam van een IoT-hub moet globaal uniek zijn in azure, dus u wilt mogelijk een uniek voor voegsel of achtervoegsel toevoegen aan de voorbeeld naam of een nieuwe naam kiezen. Zorg ervoor dat uw naam voldoet aan de juiste naam conventies voor een IoT-hub: deze moet 3-50 tekens lang zijn en mag alleen alfanumerieke tekens of afbreek streepjes ('-') bevatten. 

```azurecli-interactive 
az iot hub create --name my-sample-hub --resource-group my-sample-resource-group --location westus
```

## <a name="create-a-device-provisioning-service"></a>Een Device Provisioning Service maken

Maak een Device Provisioning Service met de opdracht [AZ IOT DPS Create](/cli/azure/iot/dps#az-iot-dps-create) . 

In het volgende voor beeld wordt een inrichtings service met de naam *My-Sample-DPS* gemaakt op de locatie *westus* . U moet ook een wereld wijd unieke naam voor uw eigen inrichtings service kiezen. Zorg ervoor dat het juiste naam conventies voor een IoT Hub Device Provisioning Service heeft: het moet 3-64 tekens lang zijn en mag alleen alfanumerieke tekens of afbreek streepjes ('-') bevatten.

```azurecli-interactive 
az iot dps create --name my-sample-dps --resource-group my-sample-resource-group --location westus
```

> [!TIP]
> In het voorbeeld wordt de inrichtingsservice gemaakt op de locatie US - west. U kunt een lijst met beschikbare locaties weergeven door de opdracht `az provider show --namespace Microsoft.Devices --query "resourceTypes[?resourceType=='ProvisioningServices'].locations | [0]" --out table` uit te voeren of door naar de pagina [Status van Azure](https://azure.microsoft.com/status/) te gaan en te zoeken naar 'device provisioning service'. In opdrachten kunnen locaties worden opgegeven in één woord of in een indeling met meerdere woorden. bijvoorbeeld: westus, VS-West, VS-West, enzovoort. De waarde is niet hoofdletter gevoelig. Als u meerdere woorden gebruikt om een locatie op te geven, voeg dan de waarde toe tussen aanhalingstekens; bijvoorbeeld: `-- location "West US"`.
>

## <a name="get-the-connection-string-for-the-iot-hub"></a>De verbindingsreeks voor de IoT-hub ophalen

U hebt de verbindingsreeks voor uw IoT-hub nodig om deze aan de Device Provisioning Service te koppelen. Gebruik de opdracht [az iot hub show-connection-string](/cli/azure/iot/hub#az-iot-hub-show-connection-string) om de verbindingsreeks op te halen en om met de uitvoer ervan een variabele in te stellen die u gaat gebruiken om de twee resources aan elkaar te koppelen. 

In het volgende voor beeld wordt de variabele *hubConnectionString* ingesteld op de waarde van de Connection String voor de primaire sleutel van het *iothubowner* -beleid van de hub (de para meter `--policy-name` kan worden gebruikt om een ander beleid op te geven). Mijn voor *beeld-hub* afhandelen voor de unieke IOT hub-naam die u eerder hebt gekozen. De opdracht maakt gebruik van de [query-](/cli/azure/query-azure-cli) en [uitvoer](/cli/azure/format-output-azure-cli#tsv-output-format)opties van de Azure CLI om de verbindingsreeks op te halen uit de uitvoer van de opdracht.

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

In het volgende voor beeld wordt een IoT-hub met de naam *My-Sample-hub* in de vestiging *westus* en een Device Provisioning Service met *de naam My-Sample-DPS*gekoppeld. Verhandel deze namen voor de unieke IoT hub-en Device Provisioning Service-namen die u eerder hebt gekozen. De opdracht maakt gebruik van de connection string voor uw IoT-hub die is opgeslagen in de variabele *hubConnectionString* in de vorige stap.

```azurecli-interactive 
az iot dps linked-hub create --dps-name my-sample-dps --resource-group my-sample-resource-group --connection-string $hubConnectionString --location westus
```

Het kan enkele minuten duren voordat de opdracht is voltooid.

## <a name="verify-the-provisioning-service"></a>De inrichtingsservice controleren

De details ophalen uit uw inrichtingsservice met de opdracht [az iot dps show](/cli/azure/iot/dps#az-iot-dps-show).

In het volgende voorbeeld worden de details van een inrichtingsservice met de naam *my-sample-dps* opgehaald. Verhandel deze naam voor uw eigen Device Provisioning Service-naam.

```azurecli-interactive
az iot dps show --name my-sample-dps
```
De gekoppelde IoT-hub wordt weergegeven in de verzameling *properties.iotHubs*.

![De inrichtings service controleren](./media/quick-setup-auto-provision-cli/verify-provisioning-service.png)

## <a name="clean-up-resources"></a>Resources opschonen

Andere Quick Starts in deze verzameling zijn op deze Quick Start gebaseerd. Als u van plan bent om door te gaan met andere Quick Starts of met de zelfstudies, verwijdert u de resources die u in deze Quick Start hebt gemaakt niet. Als u niet van plan bent om door te gaan, kunt u de volgende opdrachten gebruiken om de inrichtingsservice, de IoT-hub of de resourcegroep en alle bijbehorende resources te verwijderen. Vervang de namen van de resources die hieronder zijn geschreven door de namen van uw eigen resources.

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

In deze Quick Start hebt u een IoT-hub en een Device Provisioning service-exemplaar geïmplementeerd en zijn de twee resources gekoppeld. Als u wilt weten hoe u deze installatie kunt gebruiken om een gesimuleerd apparaat in te richten, gaat u verder met de Quick start voor het maken van een gesimuleerd apparaat.

> [!div class="nextstepaction"]
> [Quick start voor het maken van een gesimuleerd apparaat](./quick-create-simulated-device.md)
