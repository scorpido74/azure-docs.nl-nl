---
title: Azure CLI Script-voorbeeld - Routeverkeer voor hoge beschikbaarheid van toepassingen | Microsoft Documenten
description: Azure CLI Script-voorbeeld - Routeverkeer voor een hoge beschikbaarheid van toepassingen
services: traffic-manager
documentationcenter: traffic-manager
author: asudbring
manager: KumudD
ms.service: traffic-manager
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: traffic-manager
ms.date: 06/26/2018
ms.author: allensu
ms.openlocfilehash: 2a083520a0da324d7033d4597a6b734b43c025b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74049262"
---
# <a name="route-traffic-for-high-availability-of-applications---azure-cli"></a>Routeverkeer voor hoge beschikbaarheid van toepassingen - Azure CLI

Met dit script wordt een resourcegroep, twee app-serviceplannen, twee web-apps, een profiel van verkeersbeheeren en twee eindpunten voor verkeersbeheergemaakt. Traffic Manager leidt het verkeer naar de toepassing in één regio als primaire regio en naar het secundaire gebied wanneer de toepassing in de primaire regio niet beschikbaar is. Voordat u het script uitvoert, moet u de waarden MyWebApp, MyWebAppL1 en MyWebAppL2 wijzigen in unieke waarden in Azure. Nadat u het script hebt uitgevoerd, hebt u toegang tot de app in het primaire gebied met de URL-mywebapp.trafficmanager.net.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Voorbeeldscript

[!code-azurecli-interactive[main](../../../cli_scripts/traffic-manager/direct-traffic-for-increased-application-availability/direct-traffic-for-increased-application-availability.sh "Route traffic for high availability")]


## <a name="clean-up-deployment"></a>Opschonen van implementatie 

Nadat het scriptvoorbeeld is uitgevoerd, kan de opdracht volgen worden gebruikt om de brongroep, de App Service-app en alle gerelateerde bronnen te verwijderen.

```azurecli
az group delete --name myResourceGroup1 --yes
az group delete --name myResourceGroup2 --yes
```

## <a name="script-explanation"></a>Uitleg van het script

Dit script gebruikt de volgende opdrachten voor het maken van een resourcegroep, web-app, Traffic Manager-profiel en alle gerelateerde resources. Elke opdracht in de tabel is gekoppeld aan de specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group) | Hiermee wordt een resourcegroep gemaakt waarin alle resources worden opgeslagen. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan) | Hiermee maakt u een App Service-plan. Dit is als een serverfarm voor uw Azure-web-app. |
| [az webapp create](https://docs.microsoft.com/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) | Hiermee maakt u een Azure-web-app in het App-serviceplan. |
| [az netwerk traffic-manager profiel maken](https://docs.microsoft.com/cli/azure/network/traffic-manager/profile) | Hiermee maakt u een Azure Traffic Manager-profiel. |
| [eindpunt az-netwerkverkeer](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint) | Hiermee voegt u een eindpunt toe aan een Azure Traffic Manager-profiel. |

## <a name="next-steps"></a>Volgende stappen

Zie de [documentatie van Azure CLI](https://docs.microsoft.com/cli/azure) voor meer informatie over de Azure CLI.

Aanvullende APP Service CLI-scriptvoorbeelden zijn te vinden in de [Azure Networking-documentatie.](../cli-samples.md)
