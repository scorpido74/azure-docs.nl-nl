---
title: Azure CLI-voorbeeldscript - Een SignalR-service maken
description: Meer informatie over het voorbeeld script voor het maken van een nieuwe Azure signalerings service in een nieuwe resource groep met een wille keurige naam.
author: sffamily
ms.service: signalr
ms.devlang: azurecli
ms.topic: sample
ms.date: 11/13/2018
ms.author: zhshang
ms.custom: mvc
ms.openlocfilehash: 45958f020f2e5dbdb0a50f0190aff78568865f74
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "74158075"
---
# <a name="create-a-signalr-service"></a>Een SignalR-service maken 

Met dit voorbeeldscript maakt u een nieuwe resource voor een Azure SignalR-service in een nieuwe resourcegroep met een willekeurige naam.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor dit artikel gebruikmaken van Azure CLI versie 2.0 of hoger. Voer `az --version` uit om de versie te bekijken. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Voorbeeldscript

Met dit script maakt u een nieuwe resource voor een SignalR-service en een nieuwe resourcegroep. 

```azurecli-interactive
#!/bin/bash

# Generate a unique suffix for the service name
let randomNum=$RANDOM*$RANDOM

# Generate a unique service and group name with the suffix
SignalRName=SignalRTestSvc$randomNum
#resource name must be lowercase
mySignalRSvcName=${SignalRName,,}
myResourceGroupName=$SignalRName"Group"

# Create resource group 
az group create --name $myResourceGroupName --location eastus

# Create the Azure SignalR Service resource
az signalr create \
  --name $mySignalRSvcName \
  --resource-group $myResourceGroupName \
  --sku Standard_S1 \
  --unit-count 1 \
  --service-mode Default

# Get the SignalR primary connection string 
primaryConnectionString=$(az signalr key list --name $mySignalRSvcName \
  --resource-group $myResourceGroupName --query primaryConnectionString -o tsv)

echo "$primaryConnectionString"
```

Noteer de naam die wordt gegenereerd voor de nieuwe resourcegroep. U hebt deze naam nodig als u later alle groepsresources wilt verwijderen.

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Uitleg van het script

Elke opdracht in de tabel is gekoppeld aan de specifieke documentatie over de opdracht. In dit script worden de volgende opdrachten gebruikt:

| Opdracht | Opmerkingen |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Hiermee wordt een resourcegroep gemaakt waarin alle resources worden opgeslagen. |
| [az signalr create](/cli/azure/signalr#az-signalr-create) | Hiermee maakt u een resource voor de Azure SignalR-service. |
| [az signalr key list](/cli/azure/signalr/key#az-signalr-key-list) | Hiermee vraagt u de sleutels op die door uw toepassing worden gebruikt om realtime updates van inhoud te pushen met SignalR. |


## <a name="next-steps"></a>Volgende stappen

Zie de [documentatie van Azure CLI](/cli/azure) voor meer informatie over de Azure CLI.

Extra CLI-voorbeeldscripts voor de Azure SignalR-service vindt u in de [documentatie van de Azure SignalR-service](../signalr-reference-cli.md).
