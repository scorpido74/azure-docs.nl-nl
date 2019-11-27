---
title: Een functie-app maken met DevOps-implementatie-Azure CLI
description: Een functie-app maken en functiecode implementeren vanuit Azure DevOps
ms.date: 07/03/2018
ms.topic: sample
ms.custom: mvc
ms.openlocfilehash: 3fa11d5cd81d93b89b6e8ae63fd491842be78633
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2019
ms.locfileid: "74532792"
---
# <a name="create-a-function-in-azure-that-is-deployed-from-azure-devops"></a>Een functie maken in azure die wordt geïmplementeerd vanuit Azure DevOps

In dit onderwerp leest u hoe u Azure Functions kunt gebruiken om een functie-app [zonder server](https://azure.microsoft.com/solutions/serverless/) te maken met behulp van het [verbruiksabonnement](../functions-scale.md#consumption-plan). De functie-app, een container voor uw functies, wordt continu geïmplementeerd vanuit een Azure DevOps-opslag plaats. 

Voor het voltooien van dit onderwerp hebt u het volgende nodig:

* Een Azure DevOps-opslagplaats die uw functie-app-project bevat en waarvoor u beheerdersmachtigingen hebt.
* Een [persoonlijk toegangstoken (PAT)](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate) voor toegang tot uw Azure DevOps-opslagplaats.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Als u de Azure CLI liever lokaal gebruikt, moet u versie 2.0 of hoger installeren en gebruiken. Voer `az --version` uit om te bepalen welke versie van Azure CLI u hebt. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Voorbeeldscript

In dit voorbeeld maakt u een Azure-functie-app en implementeert u functiecode vanuit Azure DevOps.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/deploy-function-app-with-function-vsts/deploy-function-app-with-function-vsts.sh?highlight=3-4 "Azure Service")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Uitleg van het script

Dit script maakt gebruik van de volgende opdrachten voor het maken van een resourcegroep, opslagaccount, functie-app en alle gerelateerde resources. Elke opdracht in de tabel is gekoppeld aan de specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Hiermee maakt u een resourcegroep waarin alle resources worden opgeslagen. |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | Hiermee maakt u het opslagaccount dat vereist is voor de functie-app. |
| [az functionapp create](/cli/azure/functionapp#az-functionapp-create) | Hiermee maakt u een functie-app in het serverloze [verbruiksabonnement](../functions-scale.md#consumption-plan). |
| [az functionapp deployment source config](/cli/azure/functionapp/deployment/source#az-functionapp-deployment-source-config) | Hiermee koppelt u een functie-app aan een Git- of Mercurial-opslagplaats. |

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [documentatie van Azure CLI](/cli/azure) voor meer informatie over de Azure CLI.

Aanvullende CLI-voorbeeldscripts voor Azure Functions vindt u in de [Azure Functions-documentatie](../functions-cli-samples.md).
