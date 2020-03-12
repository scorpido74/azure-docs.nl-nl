---
title: Een bestands share koppelen aan een python-functie-app-Azure CLI
description: Maak een serverloze python-functie-app en koppel een bestaande bestands share met behulp van de Azure CLI.
ms.topic: sample
ms.date: 03/01/2020
ms.openlocfilehash: 3d66f84d124b36f1be335c3a2204f21690510ee8
ms.sourcegitcommit: 20429bc76342f9d365b1ad9fb8acc390a671d61e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2020
ms.locfileid: "79086458"
---
# <a name="mount-a-file-share-to-a-python-function-app-using-azure-cli"></a>Een bestands share koppelen aan een python-functie-app met behulp van Azure CLI

Met deze Azure Functions voorbeeld script maakt u een functie-app en maakt u een share in Azure Files. Ze koppelt de share zodat de gegevens kunnen worden geopend door uw functies.  

>[!NOTE]
>De functie-app die wordt gemaakt, wordt uitgevoerd op python versie 3,7. Azure Functions [biedt ook ondersteuning voor python-versies 3,6 en 3,8](../functions-reference-python.md#python-version).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor dit artikel gebruikmaken van Azure CLI versie 2.0 of hoger. Voer `az --version` uit om de versie te bekijken. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren]( /cli/azure/install-azure-cli). Voor beelden zijn geschreven voor bash shell en moeten worden gewijzigd om te worden uitgevoerd in een Windows-opdracht prompt. 

## <a name="sample-script"></a>Voorbeeldscript

Met dit script maakt u een Azure-functie-app met behulp van het [verbruiks abonnement](../functions-scale.md#consumption-plan).

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/functions-cli-mount-files-storage-linux/functions-cli-mount-files-storage-linux.sh "Create an Azure Function on a Consumption plan")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Uitleg van het script

Elke opdracht in de tabel is een koppeling naar specifieke documentatie over de opdracht. In dit script worden de volgende opdrachten gebruikt:

| Opdracht | Opmerkingen |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Hiermee maakt u een resourcegroep waarin alle resources worden opgeslagen. |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | Hiermee maakt u een Azure-opslagaccount. |
| [az functionapp create](/cli/azure/functionapp#az-functionapp-create) | Hiermee maakt u een functie-app. |
| [az storage share create](/cli/azure/storage/share#az-storage-share-create) | Hiermee maakt u een Azure Files-share in het opslag account. | 
| [AZ Storage Directory Create](/cli/azure/storage/directory#az-storage-directory-create) | Hiermee maakt u een map in de share. |
| [AZ webapp config Storage-account add](/cli/azure/webapp/config/storage-account#az-webapp-config-storage-account-add) | Koppelt de share aan de functie-app. |
| [AZ webapp config Storage-account list](/cli/azure/webapp/config/storage-account#az-webapp-config-storage-account-list) | Hier worden de bestands shares weer gegeven die zijn gekoppeld aan de functie-app. | 

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [documentatie van Azure CLI](/cli/azure) voor meer informatie over de Azure CLI.

Aanvullende CLI-voorbeeldscripts voor Azure Functions vindt u in de [Azure Functions-documentatie](../functions-cli-samples.md).
