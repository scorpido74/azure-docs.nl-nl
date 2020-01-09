---
title: Voor beeld van Azure CLI-script-een Azure-app configuratie archief maken
titleSuffix: Azure App Configuration
description: 'Azure CLI-voorbeeldscript: een Azure-app-configuratiearchief verwijderen'
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.service: azure-app-configuration
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: azure-app-configuration
ms.date: 02/24/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: d57de8219cb73864ed722c6906a1bd75fec51a50
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75433596"
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

# Get the AppConfig primary key 
appConfigPrimaryKey=$(az appconfig key list --name $myAppConfigStoreName \
  --resource-group $myResourceGroupName --query primaryKey -o tsv)

# Form the connection string for use in your application
connstring="Endpoint=https://$appConfigHostname;AccessKey=$appConfigPrimaryKey;"
echo "$connstring"
```

Noteer de naam die wordt gegenereerd voor de nieuwe resourcegroep. U hebt deze naam nodig als u later alle groepsresources wilt verwijderen.

[!INCLUDE [cli-script-cleanup](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt om een nieuwe resource groep en een app-configuratie archief te maken. Elke opdracht in de tabel is gekoppeld aan de specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Hiermee wordt een resourcegroep gemaakt waarin alle resources worden opgeslagen. |
| [az appconfig create](/cli/azure/ext/appconfig/appconfig#ext-appconfig-az-appconfig-create) | Hiermee maakt u een app-configuratie opslag resource. |
| [az appconfig kv list](/cli/azure/ext/appconfig/appconfig/kv#ext-appconfig-az-appconfig-kv-list) | Een lijst met sleutels die zijn opgeslagen in een app-configuratie archief. |

## <a name="next-steps"></a>Volgende stappen

Zie de [documentatie van Azure CLI](/cli/azure) voor meer informatie over de Azure CLI.

Extra CLI-script voorbeelden voor configuratie van apps vindt u in de voor beelden van de [Azure-app configuratie-cli](../cli-samples.md).
