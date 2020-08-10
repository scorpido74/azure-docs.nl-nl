---
title: Een bestandsshare koppelen aan een Python-functie-app - Azure CLI
description: Lees hoe u een serverloze Python-functie-app maakt en een bestaande bestandsshare koppelt met behulp van de Azure CLI.
ms.topic: sample
ms.date: 03/01/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 63ab9ba3219dc600187e73bbf124d62d3f51317a
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/31/2020
ms.locfileid: "87498205"
---
# <a name="mount-a-file-share-to-a-python-function-app-using-azure-cli"></a>Een bestandsshare koppelen aan een Python-functie-app met behulp van Azure CLI

Met dit voorbeeldscript voor Azure Functions maakt u een functie-app en een share in Azure Files. Vervolgens wordt de share gekoppeld zodat de gegevens toegankelijk zijn voor uw functies.  

>[!NOTE]
>De functie-app die wordt gemaakt, wordt uitgevoerd met Python versie 3.7. Azure Functions [ondersteunt ook Python-versies 3.6 en 3.8](../functions-reference-python.md#python-version).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor dit artikel gebruikmaken van Azure CLI versie 2.0 of hoger. Voer `az --version` uit om de versie te bekijken. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren]( /cli/azure/install-azure-cli). De voorbeelden zijn geschreven voor de Bash shell en moeten worden aangepast om te worden uitgevoerd vanaf een Windows-opdrachtprompt. 

## <a name="sample-script"></a>Voorbeeldscript

Met dit script wordt een Azure Function-app gemaakt met behulp van het [Verbruiksabonnement](../functions-scale.md#consumption-plan).

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/functions-cli-mount-files-storage-linux/functions-cli-mount-files-storage-linux.sh "Create an Azure Function on a Consumption plan")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Uitleg van het script

Elke opdracht in de tabel is een koppeling naar specifieke documentatie over de opdracht. In dit script worden de volgende opdrachten gebruikt:

| Opdracht | Opmerkingen |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Hiermee maakt u een resourcegroep waarin alle resources worden opgeslagen. |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | Hiermee wordt een Azure Storage-account gemaakt. |
| [az functionapp create](/cli/azure/functionapp#az-functionapp-create) | Hiermee maakt u een functie-app. |
| [az storage share create](/cli/azure/storage/share#az-storage-share-create) | Hiermee maakt u een Azure Files-share in het opslagaccount. | 
| [az storage directory create](/cli/azure/storage/directory#az-storage-directory-create) | Hiermee maakt u een map in de share. |
| [az webapp config storage-account add](/cli/azure/webapp/config/storage-account#az-webapp-config-storage-account-add) | Hiermee koppelt u de share aan de functie-app. |
| [az webapp config storage-account list](/cli/azure/webapp/config/storage-account#az-webapp-config-storage-account-list) | Hiermee worden de bestandsshares weergegeven die zijn gekoppeld aan de functie-app. | 

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [documentatie van Azure CLI](/cli/azure) voor meer informatie over de Azure CLI.

Aanvullende CLI-voorbeeldscripts voor Azure Functions vindt u in de [Azure Functions-documentatie](../functions-cli-samples.md).
