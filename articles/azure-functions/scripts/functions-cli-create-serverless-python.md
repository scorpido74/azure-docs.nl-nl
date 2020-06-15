---
title: Een serverloze Python-functie-app maken - Azure CLI
description: Een serverloze Python-functie-app maken met behulp van de Azure CLI
ms.assetid: 0e221db6-ee2d-4e16-9bf6-a456cd05b6e7
ms.topic: sample
ms.date: 11/23/2019
ms.custom: tracking-python
ms.openlocfilehash: 3bcefe59bfec5f3d76ecbc04a5b46a9285fc381b
ms.sourcegitcommit: 964af22b530263bb17fff94fd859321d37745d13
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/09/2020
ms.locfileid: "84561200"
---
# <a name="create-a-serverless-python-function-app-using-azure-cli"></a>Een serverloze Python-functie-app maken met behulp van Azure CLI

Met dit voorbeeldscript voor Azure Functions maakt u een functie-app die een container vormt voor uw functies. 

>[!NOTE]
>De functie-app die wordt gemaakt, wordt uitgevoerd op Python versie 3.6. Python-versie 3.7 wordt ook ondersteund door Azure Functions.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor dit artikel gebruikmaken van Azure CLI versie 2.0 of hoger. Voer `az --version` uit om de versie te bekijken. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Voorbeeldscript

Met dit script wordt een Azure Function-app gemaakt met behulp van het [Verbruiksabonnement](../functions-scale.md#consumption-plan).

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/create-function-app-consumption-python/create-function-app-consumption-python.sh "Create an Azure Function on a Consumption plan")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Uitleg van het script

Elke opdracht in de tabel is een koppeling naar specifieke documentatie over de opdracht. In dit script worden de volgende opdrachten gebruikt:

| Opdracht | Opmerkingen |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Hiermee maakt u een resourcegroep waarin alle resources worden opgeslagen. |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | Hiermee wordt een Azure Storage-account gemaakt. |
| [az functionapp create](/cli/azure/functionapp#az-functionapp-create) | Hiermee maakt u een functie-app. |

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [documentatie van Azure CLI](/cli/azure) voor meer informatie over de Azure CLI.

Aanvullende CLI-voorbeeldscripts voor Azure Functions vindt u in de [Azure Functions-documentatie](../functions-cli-samples.md).
