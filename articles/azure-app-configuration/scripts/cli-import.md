---
title: Voor beeld van Azure CLI-script-importeren naar een app-configuratie archief
titleSuffix: Azure App Configuration
description: Bevat informatie en voorbeeldscripts voor het importeren in een Azure-app-configuratiearchief
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
ms.openlocfilehash: 28c04859f23b3a560166aa62ac903e1204fc5d97
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75413289"
---
# <a name="import-to-an-azure-app-configuration-store"></a>Importeren in een Azure-app-configuratiearchief

Met dit voorbeeld script worden sleutel waarde-instellingen in een Azure-app configuratie archief geïmporteerd.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om de Azure CLI lokaal te installeren en te gebruiken, moet u voor dit artikel gebruikmaken van Azure CLI versie 2,0 of hoger. Voer `az --version` uit om de versie te bekijken. Zie [de Azure cli installeren](/cli/azure/install-azure-cli)als u wilt installeren of upgraden.

U moet eerst de CLI-extensie voor het Azure App Configuration-archief installeren door de volgende opdracht uit te voeren:

        az extension add -n appconfig

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
| [AZ appconfig KV import](/cli/azure/ext/appconfig/appconfig/kv#ext-appconfig-az-appconfig-kv-import) | Hiermee worden geïmporteerd naar een app-configuratie opslag resource. |

## <a name="next-steps"></a>Volgende stappen

Zie de [Azure cli-documentatie](/cli/azure)voor meer informatie over de Azure cli.

Extra CLI-script voorbeelden voor configuratie van apps vindt u in de voor beelden van de [Azure-app configuratie-cli](../cli-samples.md).
