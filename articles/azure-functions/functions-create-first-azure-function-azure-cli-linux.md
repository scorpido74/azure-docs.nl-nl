---
title: Uw eerste functie in Linux maken in Azure
description: Informatie over het maken van uw eerste functie die wordt gehost in Linux in azure met behulp van de opdracht regel Programma's, Azure Functions Core Tools en de Azure CLI.
ms.date: 03/12/2019
ms.topic: quickstart
ms.custom: mvc, fasttrack-edit
ms.openlocfilehash: 57c1b4a0ef5e8f030360bca6d69f66e8752f3da1
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74964137"
---
# <a name="quickstart-create-your-first-function-hosted-on-linux-using-command-line-tools"></a>Snelstartgids: uw eerste functie maken die wordt gehost in Linux met behulp van de opdracht regel Programma's

Met Azure Functions kunt u uw code in een [serverloze](https://azure.com/serverless) Linux-omgeving uitvoeren zonder dat u eerst een virtuele machine moet maken of een webtoepassing publiceren. Linux-hosting vereist [de functies 2. x en latere runtime](functions-versions.md). Serverloze functies worden uitgevoerd in het [verbruiks abonnement](functions-scale.md#consumption-plan).

In dit snelstartartikel leert u hoe u de Azure CLI gebruikt om uw eerste functie-app te maken die in Linux wordt uitgevoerd. De functiecode wordt lokaal gemaakt en vervolgens naar Azure geïmplementeerd met behulp van de [Azure Functions Core Tools](functions-run-local.md).

De volgende stappen worden ondersteund op een Mac-, Windows- of Linux-computer. In dit artikel leest u hoe u functies maakt in JavaScript of C#. Zie [uw eerste python-functie maken met kern hulpprogramma's en Azure cli](functions-create-first-function-python.md)voor meer informatie over het maken van python-functies.

## <a name="prerequisites"></a>Vereisten

Voordat u dit voorbeeld kunt uitvoeren moet u ervoor zorgen dat u het volgende hebt:

+ Installeer [Azure functions core tools](./functions-run-local.md#v2) versie 2.6.666 of hoger.

+ Installeer de [Azure CLI]( /cli/azure/install-azure-cli). In dit artikel is Azure CLI versie 2.0 of hoger vereist. Voer `az --version` uit om te zien welke versie u hebt. U kunt ook de [Azure Cloud Shell](https://shell.azure.com/bash) gebruiken.

+ Een actief Azure-abonnement.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [functions-create-function-app-cli](../../includes/functions-create-function-app-cli.md)]

## <a name="enable-extension-bundles"></a>Uitbreidings bundels inschakelen

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

[!INCLUDE [functions-create-function-core-tools](../../includes/functions-create-function-core-tools.md)]

[!INCLUDE [functions-run-function-test-local](../../includes/functions-run-function-test-local.md)]

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-linux-function-app-in-azure"></a>Een Linux-functie-app maken in Azure

U moet beschikken over een functie-app om de uitvoering van uw functies in Linux te hosten. De functie-app biedt een serverloze omgeving voor de uitvoering van uw functiecode. U kunt er functies mee groeperen in een logische eenheid, zodat u resources eenvoudiger kunt beheren, implementeren en delen. U maakt een functie-app die in Linux wordt uitgevoerd met behulp van de opdracht [az functionapp create](/cli/azure/functionapp#az-functionapp-create).

Gebruik in de volgende opdracht een unieke functie-appnaam in plaats van de tijdelijke plaatsaanduiding `<app_name>` en gebruik de naam van het opslagaccount in plaats van `<storage_name>`. De `<app_name>` is ook het standaard DNS-domein voor de functie-app. Deze naam moet uniek zijn in alle apps in Azure. U moet ook de `<language>` runtime voor uw functie-app instellen, van `dotnet`C#(), `node` (Java script/type script) of `python`.

```azurecli-interactive
az functionapp create --resource-group myResourceGroup --consumption-plan-location westus --os-type Linux \
--name <app_name> --storage-account  <storage_name> --runtime <language>
```

Nadat de functie-app is gemaakt, ziet u het volgende bericht:

```output
Your serverless Linux function app 'myfunctionapp' has been successfully created.
To active this function app, publish your app content using Azure Functions Core Tools or the Azure portal.
```

Nu kunt u uw project publiceren naar de nieuwe functie-app in Azure.

[!INCLUDE [functions-publish-project](../../includes/functions-publish-project.md)]

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

[!INCLUDE [functions-quickstart-next-steps-cli](../../includes/functions-quickstart-next-steps-cli.md)]