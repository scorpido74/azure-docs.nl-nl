---
title: Een bestandsshare weergeven in een Python-functie-app - Azure CLI
description: Maak een serverloze Python-functie-app en monteer een bestaande bestandsshare met de Azure CLI.
ms.topic: sample
ms.date: 03/01/2020
ms.openlocfilehash: 3d66f84d124b36f1be335c3a2204f21690510ee8
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79086458"
---
# <a name="mount-a-file-share-to-a-python-function-app-using-azure-cli"></a>Een bestandsshare weergeven op een Python-functie-app met Azure CLI

Met dit voorbeeldscript voor Azure Functions wordt een functie-app gemaakt en wordt een share gemaakt in Azure-bestanden. Het hen mounts het aandeel, zodat de gegevens kunnen worden benaderd door uw functies.  

>[!NOTE]
>De functie-app die is gemaakt, draait op Python-versie 3.7. Azure Functions ondersteunt ook [Python-versies 3.6 en 3.8](../functions-reference-python.md#python-version).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor dit artikel gebruikmaken van Azure CLI versie 2.0 of hoger. Voer `az --version` uit om de versie te bekijken. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren]( /cli/azure/install-azure-cli). Voorbeelden zijn geschreven voor Bash shell en moeten worden aangepast om uit te voeren in een Windows-opdrachtprompt. 

## <a name="sample-script"></a>Voorbeeldscript

Met dit script wordt een Azure-functie-app gemaakt met behulp van het [verbruiksplan](../functions-scale.md#consumption-plan).

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/functions-cli-mount-files-storage-linux/functions-cli-mount-files-storage-linux.sh "Create an Azure Function on a Consumption plan")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Uitleg van het script

Elke opdracht in de tabel is gekoppeld aan de specifieke documentatie over de opdracht. In dit script worden de volgende opdrachten gebruikt:

| Opdracht | Opmerkingen |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Hiermee wordt een resourcegroep gemaakt waarin alle resources worden opgeslagen. |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | Hiermee wordt een Azure Storage-account gemaakt. |
| [az functionapp create](/cli/azure/functionapp#az-functionapp-create) | Hiermee maakt u een functie-app. |
| [az storage share create](/cli/azure/storage/share#az-storage-share-create) | Hiermee maakt u een Azure Files-share in opslagaccount. | 
| [az-opslagmap maken](/cli/azure/storage/directory#az-storage-directory-create) | Hiermee maakt u een map in het aandeel. |
| [az webapp config storage-account toevoegen](/cli/azure/webapp/config/storage-account#az-webapp-config-storage-account-add) | Monteert het aandeel in de functie-app. |
| [az webapp config storage-account lijst](/cli/azure/webapp/config/storage-account#az-webapp-config-storage-account-list) | Toont bestandsshares die zijn gemonteerd op de functie-app. | 

## <a name="next-steps"></a>Volgende stappen

Zie de [documentatie van Azure CLI](/cli/azure) voor meer informatie over de Azure CLI.

Aanvullende CLI-voorbeeldscripts voor Azure Functions vindt u in de [Azure Functions-documentatie](../functions-cli-samples.md).
