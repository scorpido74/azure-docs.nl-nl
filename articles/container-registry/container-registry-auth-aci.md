---
title: Toegang tot Azure Container Registry vanuit Container Instances
description: Meer informatie over het verlenen van toegang tot installatie kopieën in uw persoonlijke container register van Azure Container Instances met behulp van een Azure Active Directory Service-Principal.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 04/23/2018
ms.author: danlep
ms.openlocfilehash: 53cdca72587dafd39abd3f13ad488dcb460bf622
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/12/2019
ms.locfileid: "73931661"
---
# <a name="authenticate-with-azure-container-registry-from-azure-container-instances"></a>Verifiëren met Azure Container Registry vanuit Azure Container Instances

U kunt een service-principal voor Azure Active Directory (Azure AD) gebruiken om toegang te verlenen tot uw persoonlijke container registers in Azure Container Registry.

In dit artikel leert u hoe u een Azure AD-Service-Principal maakt en configureert met *pull* -machtigingen voor uw REGI ster. Vervolgens start u een container in Azure Container Instances (ACI) die de installatie kopie ophaalt uit uw persoonlijke REGI ster, met behulp van de service-principal voor authenticatie.

## <a name="when-to-use-a-service-principal"></a>Wanneer moet u een Service-Principal gebruiken?

U moet een Service-Principal gebruiken voor verificatie van ACI in **headless scenario's**, zoals in toepassingen of services die container instanties maken in een geautomatiseerde of op een andere manier zonder toezicht.

Als u bijvoorbeeld een geautomatiseerd script hebt dat in de nacht wordt uitgevoerd en een [container exemplaar op basis van taken](../container-instances/container-instances-restart-policy.md) maakt om bepaalde gegevens te verwerken, kan de Service-Principal gebruikmaken van alleen pull-machtigingen voor verificatie bij het REGI ster. U kunt vervolgens de referenties van de Service-Principal draaien of de toegang intrekken zonder dat dit van invloed is op andere services en toepassingen.

Service-principals moeten ook worden gebruikt wanneer de [gebruiker](container-registry-authentication.md#admin-account) van het register beheer is uitgeschakeld.

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

## <a name="authenticate-using-the-service-principal"></a>Verificatie met behulp van de Service-Principal

Als u een container in Azure Container Instances wilt starten met behulp van een Service-Principal, geeft u de ID op voor `--registry-username`en het bijbehorende wacht woord voor `--registry-password`.

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image mycontainerregistry.azurecr.io/myimage:v1 \
    --registry-login-server mycontainerregistry.azurecr.io \
    --registry-username <service-principal-ID> \
    --registry-password <service-principal-password>
```

## <a name="sample-scripts"></a>Voorbeeldscripts

U kunt de voor gaande voorbeeld scripts voor Azure CLI vinden op GitHub, evenals versies voor Azure PowerShell:

* [Azure CLI][acr-scripts-cli]
* [Azure PowerShell][acr-scripts-psh]

## <a name="next-steps"></a>Volgende stappen

De volgende artikelen bevatten aanvullende informatie over het werken met Service-principals en ACR:

* [Verificatie Azure Container Registry met Service-principals](container-registry-auth-service-principal.md)
* [Verifiëren met Azure Container Registry van de Azure Kubernetes-service (AKS)](../aks/cluster-container-registry-integration.md)

<!-- IMAGES -->

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
