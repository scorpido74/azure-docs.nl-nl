---
title: 'CLI: SSL-certificaat uploaden en binden aan een app'
description: Meer informatie over het gebruik van de Azure CLI voor het automatiseren van de implementatie en het beheer van uw App Service-app. Dit voor beeld laat zien hoe u een aangepast SSL-certificaat verbindt met een app.
tags: azure-service-management
ms.assetid: eb95d350-81ea-4145-a1e2-6eea3b7469b2
ms.devlang: azurecli
ms.topic: sample
ms.date: 12/11/2017
ms.custom: seodec18
ms.openlocfilehash: 1ef07cb55af4b9fe9f54d58bbd496789928c1cec
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2019
ms.locfileid: "74686690"
---
# <a name="bind-a-custom-ssl-certificate-to-an-app-service-app-using-cli"></a>Een aangepast SSL-certificaat koppelen aan een App Service-app met CLI

Met dit voorbeeldscript wordt een app in App Service gemaakt met de bijbehorende resources, waarna het SSL-certificaat van een aangepaste domeinnaam daaraan wordt gekoppeld. Voor dit voorbeeld hebt u het volgende nodig:

* Toegang tot de pagina voor DNS-configuratie van uw domeinregistrar.
* Een geldig .PFX-bestand en het bijbehorende wachtwoord voor het SSL-certificaat dat u wilt uploaden en koppelen.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, hebt u Azure CLI versie 2.0 of hoger nodig. Voer `az --version` uit om de versie te bekijken. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Voorbeeldscript

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Bind a custom SSL certificate to an app")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt. Elke opdracht in de tabel is gekoppeld aan de specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [`az group create`](/cli/azure/group?view=azure-cli-latest#az-group-create) | Hiermee maakt u een resourcegroep waarin alle resources worden opgeslagen. |
| [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create) | Hiermee maakt u een App Service-abonnement. |
| [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) | Hiermee maakt u een App Service-app. |
| [`az webapp config hostname add`](/cli/azure/webapp/config/hostname?view=azure-cli-latest#az-webapp-config-hostname-add) | Hiermee wijst u een aangepast domein toe aan een App Service-app. |
| [`az webapp config ssl upload`](/cli/azure/webapp/config/ssl?view=azure-cli-latest#az-webapp-config-ssl-upload) | Hiermee wordt een SSL-certificaat geüpload naar een App Service-app. |
| [`az webapp config ssl bind`](/cli/azure/webapp/config/ssl?view=azure-cli-latest#az-webapp-config-ssl-bind) | Hiermee wordt een geüpload SSL-certificaat gekoppeld aan een App Service-app. |

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [documentatie van Azure CLI](https://docs.microsoft.com/cli/azure) voor meer informatie over de Azure CLI.

Meer voorbeelden van App Service CLI-scripts vindt u in de [documentatie van Azure App Service](../samples-cli.md).
