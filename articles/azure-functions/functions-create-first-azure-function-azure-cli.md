---
title: Uw eerste functie maken met Azure CLI
description: Informatie over het maken van uw eerste Azure-functie met behulp van Azure CLI en Azure Functions Core Tools.
ms.assetid: 674a01a7-fd34-4775-8b69-893182742ae0
ms.date: 11/13/2018
ms.topic: quickstart
ms.custom: mvc
ms.openlocfilehash: 147ad4bd20ee1c7ae8f1529e1b3bc0e4f3e7dbb0
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230844"
---
# <a name="quickstart-create-your-first-function-from-the-command-line-using-azure-cli"></a>Quickstart: Create your first function from the command line using Azure CLI

In dit onderwerp van de zelfstudie wordt stapsgewijs uitgelegd hoe u uw eerste functie maakt vanaf de opdrachtregel of in de terminal. Azure CLI gebruikt u om een functie-app te maken. Het is de [serverloze](https://azure.microsoft.com/solutions/serverless/) infrastructuur die als host fungeert voor uw functie. Het functiecodeproject wordt gegenereerd vanuit een sjabloon met behulp van de [Azure Functions Core Tools](functions-run-local.md), dat ook wordt gebruikt om het functie-app-project te implementeren naar Azure.

U kunt de onderstaande stappen volgen op een Mac-, Windows- of Linux-computer.

## <a name="prerequisites"></a>Vereisten

Voordat u dit voorbeeld kunt uitvoeren moet u ervoor zorgen dat u het volgende hebt:

+ Install [Azure Functions Core Tools](./functions-run-local.md#v2) version 2.6.666 or later.

+ Installeer de [Azure CLI](/cli/azure/install-azure-cli). This     article requires the Azure CLI version 2.0 or later. Voer `az --version` uit om te zien welke versie u hebt. U kunt ook de [Azure Cloud Shell](https://shell.azure.com/bash) gebruiken.

+ Een actief Azure-abonnement.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [functions-create-function-app-cli](../../includes/functions-create-function-app-cli.md)]

## <a name="enable-extension-bundles"></a>Enable extension bundles

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

[!INCLUDE [functions-create-function-core-tools](../../includes/functions-create-function-core-tools.md)]

[!INCLUDE [functions-run-function-test-local](../../includes/functions-run-function-test-local.md)]

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-function-app"></a>Een functie-app maken

U moet een functie-app hebben die als host fungeert voor de uitvoering van uw functies. De functie-app biedt een omgeving waarin uw functiecode zonder server kan worden uitgevoerd. U kunt er functies mee groeperen in een logische eenheid, zodat u resources eenvoudiger kunt beheren, implementeren en delen. Een functie-app maken met behulp van de opdracht [az functionapp create](/cli/azure/functionapp#az-functionapp-create). 

Vervang in de volgende opdracht de plaatsaanduiding `<APP_NAME>` door een unieke functie-appnaam en gebruik de naam van het opslagaccount voor `<STORAGE_NAME>`. De `<APP_NAME>` wordt gebruikt als het standaard DNS-domein voor de functie-app. Om die reden moet de naam uniek zijn in alle apps in Azure. U moet ook de `<language>`-runtime voor uw functie-app instellen, vanuit `dotnet` (C#) of `node` (JavaScript).

```azurecli-interactive
az functionapp create --resource-group myResourceGroup --consumption-plan-location westeurope \
--name <APP_NAME> --storage-account  <STORAGE_NAME> --runtime <language>
```

Als u de parameter _consumption-plan-location_ instelt, betekent dat dat de function-app wordt gehost in een hostingabonnement van het type Consumption. In dit serverloze abonnement worden resources naar behoefte dynamisch door uw functies toegevoegd, en betaalt u alleen wanneer functies worden uitgevoerd. Zie [Het juiste hostingabonnement kiezen](functions-scale.md) voor meer informatie.

Nadat de functie-app is gemaakt, toont Azure CLI soortgelijke informatie als in het volgende voorbeeld:

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "containerSize": 1536,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "quickstart.azurewebsites.net",
  "enabled": true,
  "enabledHostNames": [
    "quickstart.azurewebsites.net",
    "quickstart.scm.azurewebsites.net"
  ],
   ....
    // Remaining output has been truncated for readability.
}
```

[!INCLUDE [functions-publish-project](../../includes/functions-publish-project.md)]

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

[!INCLUDE [functions-quickstart-next-steps-cli](../../includes/functions-quickstart-next-steps-cli.md)]

