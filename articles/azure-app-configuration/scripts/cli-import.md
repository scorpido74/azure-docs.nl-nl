---
title: Voor beeld van Azure CLI-script-importeren naar een app-configuratie archief
titleSuffix: Azure App Configuration
description: Azure CLI-script gebruiken voor het importeren van configuratie naar Azure-app configuratie
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.devlang: azurecli
ms.topic: sample
ms.date: 02/19/2020
ms.author: lcozzens
ms.openlocfilehash: 71d6aafa82f647b9c6164ee9a06b43ed7e9a66af
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "77523592"
---
# <a name="import-to-an-azure-app-configuration-store"></a>Importeren in een Azure-app-configuratiearchief

Met dit voorbeeld script worden sleutel waarde-instellingen in een Azure-app configuratie archief geïmporteerd.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om de Azure CLI lokaal te installeren en te gebruiken, moet u voor dit artikel gebruikmaken van Azure CLI versie 2,0 of hoger. Voer `az --version` uit om de versie te bekijken. Zie [de Azure cli installeren](/cli/azure/install-azure-cli)als u wilt installeren of upgraden.

## <a name="sample-script"></a>Voorbeeldscript

```azurecli-interactive
#!/bin/bash

# Import key-values from a file
az appconfig kv import --name myTestAppConfigStore --source file --path ~/Import.json
```

[!INCLUDE [cli-script-cleanup](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt om een app-configuratie archief te importeren. Elke opdracht in de tabel is een koppeling naar opdracht-specifieke documentatie.

| Opdracht | Opmerkingen |
|---|---|
| [AZ appconfig KV import](/cli/azure/appconfig/kv#az-appconfig-kv-import) | Hiermee worden geïmporteerd naar een app-configuratie opslag resource. |

## <a name="next-steps"></a>Volgende stappen

Zie de [Azure cli-documentatie](/cli/azure)voor meer informatie over de Azure cli.

Extra CLI-script voorbeelden voor configuratie van apps vindt u in de voor beelden van de [Azure-app configuratie-cli](../cli-samples.md).
