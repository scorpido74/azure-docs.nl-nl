---
title: Een functie-app maken met GitHub-implementatie-Azure CLI
description: Maak een functie-app en implementeer functiecode vanuit een GitHub-opslagplaats met behulp van Azure Functions.
ms.date: 07/03/2018
ms.topic: sample
ms.custom: mvc
ms.openlocfilehash: e424737b67be58ac0c3880ad2454c87fab803a15
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "75922721"
---
# <a name="create-a-function-app-in-azure-that-is-deployed-from-github"></a>Een functie-app in Azure maken die wordt geïmplementeerd vanuit GitHub

Met deze Azure Functions voorbeeld script wordt een functie-app gemaakt met behulp van het [verbruiks abonnement](../functions-scale.md#consumption-plan), samen met de bijbehorende resources. Het script configureert ook de functiecode voor continue implementatie vanuit een GitHub-opslagplaats. 

Voor dit voorbeeld hebt u het volgende nodig:

* Een GitHub-opslagplaats met functiecode waarvoor u beheerdersbevoegdheden hebt.
* Een [Personal Access Token (PAT)](https://help.github.com/articles/creating-an-access-token-for-command-line-use) voor uw GitHub-account.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Als u de Azure CLI liever lokaal gebruikt, moet u versie 2.0 of hoger installeren en gebruiken. Voer `az --version` uit om te bepalen welke versie van Azure CLI u hebt. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Voorbeeldscript

In dit voorbeeld maakt u een Azure-functie-app en implementeert u functiecode vanuit GitHub.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/deploy-function-app-with-function-github-continuous/deploy-function-app-with-function-github-continuous.sh?highlight=3-4 "Azure Service")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Uitleg van het script

Elke opdracht in de tabel is gekoppeld aan de specifieke documentatie over de opdracht. In dit script worden de volgende opdrachten gebruikt:

| Opdracht | Opmerkingen |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Hiermee wordt een resourcegroep gemaakt waarin alle resources worden opgeslagen. |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | Hiermee maakt u het opslagaccount dat vereist is voor de functie-app. |
| [az functionapp create](/cli/azure/functionapp#az-functionapp-create) | Hiermee maakt u een functie-app in het serverloze [verbruiks abonnement](../functions-scale.md#consumption-plan) en koppelt u deze aan een Git-of mercurial-opslag plaats. |

## <a name="next-steps"></a>Volgende stappen

Zie de [documentatie van Azure CLI](/cli/azure) voor meer informatie over de Azure CLI.

Aanvullende CLI-voorbeeldscripts voor Azure Functions vindt u in de [Azure Functions-documentatie](../functions-cli-samples.md).
