---
title: Voor beeld van Azure CLI-script-een Azure-app configuratie archief maken
titleSuffix: Azure App Configuration
description: 'Azure CLI-voorbeeldscript: een Azure-app-configuratiearchief verwijderen'
services: azure-app-configuration
author: jpconnock
ms.service: azure-app-configuration
ms.topic: sample
ms.date: 01/24/2020
ms.author: jeconnoc
ms.openlocfilehash: 44c381da8648fea74059c9110438cfeb4c366116
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2020
ms.locfileid: "76766464"
---
# <a name="create-an-azure-app-configuration-store"></a>Een Azure App-configuratiearchief maken

Met dit voorbeeldscript maakt u een nieuw exemplaar van de Azure-app-configuratie in een nieuwe resourcegroep met een willekeurige naam.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor dit artikel gebruikmaken van Azure CLI versie 2.0 of hoger. Voer `az --version` uit om de versie te bekijken. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren](/cli/azure/install-azure-cli).

U moet eerst de CLI-extensie voor het Azure App Configuration-archief installeren door de volgende opdracht uit te voeren:

        az extension add -n appconfig

## <a name="sample-script"></a>Voorbeeldscript

```azurecli-interactive
#!/bin/bash

appConfigName=myTestAppConfigStore
#resource name must be lowercase
myAppConfigStoreName=${appConfigName,,}
myResourceGroupName=$appConfigName"Group"

# Create resource group 
az group create --name $myResourceGroupName --location eastus

# Create the Azure AppConfig Service resource and query the hostName
appConfigHostname=$(az appconfig create \
  --name $myAppConfigStoreName \
  --location eastus \
  --resource-group $myResourceGroupName \
  --query hostName \
  -o tsv)

# Get the AppConfig connection string 
appConfigConnectionString=$(az appconfig credential list \
--resource-group $myResourceGroupName \
--name $myAppConfigStoreName \
--query "[?name=='Secondary Read Only'] .connectionString" -o tsv)

# Echo the connection string for use in your application
echo "$appConfigConnectionString"
```

Noteer de naam die wordt gegenereerd voor de nieuwe resourcegroep. U hebt deze naam nodig als u later alle groepsresources wilt verwijderen.

[!INCLUDE [cli-script-cleanup](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt om een nieuwe resource groep en een app-configuratie archief te maken. Elke opdracht in de tabel is gekoppeld aan de specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Hiermee wordt een resourcegroep gemaakt waarin alle resources worden opgeslagen. |
| [az appconfig create](/cli/azure/ext/appconfig/appconfig#ext-appconfig-az-appconfig-create) | Hiermee maakt u een app-configuratie opslag resource. |
| [AZ appconfig Credential List](/cli/azure/ext/appconfig/appconfig/credential?view=azure-cli-latest) | Lijst met toegangs sleutels voor een app-configuratie archief. |

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [documentatie van Azure CLI](/cli/azure) voor meer informatie over de Azure CLI.

Extra CLI-script voorbeelden voor configuratie van apps vindt u in de voor beelden van de [Azure-app configuratie-cli](../cli-samples.md).
