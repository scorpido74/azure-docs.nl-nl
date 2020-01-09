---
title: Voor beeld van Azure CLI-script-werken met sleutel waarden in app-configuratie opslag
titleSuffix: Azure App Configuration
description: Bevat informatie over het werken met sleutelwaarden in een Azure-app-configuratiearchief
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.service: azure-app-configuration
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: azure-app-configuration
ms.date: 11/08/2019
ms.author: lcozzens
ms.custom: mvc
ms.openlocfilehash: 366485c8c735af5689e1b2c2e63eb192218f5ce0
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75433578"
---
# <a name="work-with-key-values-in-an-azure-app-configuration-store"></a>Werken met sleutelwaarden in een Azure-app-configuratiearchief

Met dit voorbeeldscript wordt een nieuwe sleutelwaarde in een Azure-app-configuratiearchief gemaakt, wordt een lijst met alle bestaande sleutelwaarden weergegeven en wordt de waarde van de zojuist gemaakte sleutel bijgewerkt en de laatste verwijderd.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor dit artikel gebruikmaken van Azure CLI versie 2.0 of hoger. Voer `az --version` uit om de versie te bekijken. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren](/cli/azure/install-azure-cli).

U moet eerst de CLI-extensie voor het Azure App Configuration-archief installeren door de volgende opdracht uit te voeren:

        az extension add -n appconfig

## <a name="sample-script"></a>Voorbeeldscript

```azurecli-interactive
#!/bin/bash

appConfigName=myTestAppConfigStore
newKey="TestKey"
refKey="KeyVaultReferenceTestKey"
uri="[URL to value stored in Key Vault]"
uri2="[URL to another value stored in Key Vault]"

# Create a new key-value 
az appconfig kv set --name $appConfigName --key $newKey --value "Value 1"

# List current key-values
az appconfig kv list --name $appConfigName

# Update new key's value
az appconfig kv set --name $appConfigName --key $newKey --value "Value 2"

# List current key-values
az appconfig kv list --name $appConfigName

# Create a new key-value referencing a value stored in Azure Key Vault
az appconfig kv set --name $appConfigName --key $refKey --content-type "application/vnd.microsoft.appconfig.keyvaultref+json;charset=utf-8" --value "{\"uri\":\"$uri\"}"

# List current key-values
az appconfig kv list --name $appConfigName

# Update Key Vault reference
az appconfig kv set --name $appConfigName --key $refKey --value "{\"uri\":\"$uri2\"}"

# List current key-values
az appconfig kv list --name $appConfigName

# Delete new key
az appconfig kv delete  --name $appConfigName --key $newKey

# Delete Key Vault reference
az appconfig kv delete --name $appConfigName --key $refKey

# List current key-values
az appconfig kv list --name $appConfigName
```

[!INCLUDE [cli-script-cleanup](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt voor het uitvoeren van sleutel waarden in een app-configuratie archief. Elke opdracht in de tabel is gekoppeld aan de specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [az appconfig kv set](/cli/azure/ext/appconfig/appconfig/kv#ext-appconfig-az-appconfig-kv-set) | Hiermee wordt een sleutelwaarde gemaakt of bijgewerkt. |
| [az appconfig kv list](/cli/azure/ext/appconfig/appconfig/kv#ext-appconfig-az-appconfig-kv-list) | Geeft een lijst met sleutel waarden in een app-configuratie archief. |
| [az appconfig kv delete](/cli/azure/ext/appconfig/appconfig/kv#ext-appconfig-az-appconfig-kv-delete) | Hiermee wordt een sleutelwaarde verwijderd. |

## <a name="next-steps"></a>Volgende stappen

Zie de [documentatie van Azure CLI](/cli/azure) voor meer informatie over de Azure CLI.

Extra CLI-script voorbeelden voor configuratie van apps vindt u in de voor beelden van de [Azure-app configuratie-cli](../cli-samples.md).
