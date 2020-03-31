---
title: Toegang vanuit containerinstanties
description: Meer informatie over het bieden van toegang tot afbeeldingen in uw privécontainerregister vanuit Azure Container Instances met behulp van een Azure Active Directory-serviceprincipal.
ms.topic: article
ms.date: 04/23/2018
ms.openlocfilehash: b1bc8119c495dea99c6bdc4923db198d041a1e9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74456520"
---
# <a name="authenticate-with-azure-container-registry-from-azure-container-instances"></a>Verifiëren met Azure Container Registry vanuit Azure Container Instances

U een Azure Active Directory-serviceprincipal (Azure Ad) gebruiken om toegang te bieden tot uw privécontainerregisters in Azure Container Registry.

In dit artikel leert u een Azure AD-serviceprincipal maken en configureren met *pull-machtigingen* voor uw register. Vervolgens start u een container in Azure Container Instances (ACI) die de afbeelding uit uw privéregister haalt, met behulp van de serviceprincipal voor verificatie.

## <a name="when-to-use-a-service-principal"></a>Wanneer een serviceprincipal gebruiken

U moet een serviceprincipal gebruiken voor verificatie van ACI in **headless-scenario's,** zoals in toepassingen of services die containerexemplaren op een geautomatiseerde of anderszins onbeheerde manier maken.

Als u bijvoorbeeld een geautomatiseerd script hebt dat 's nachts wordt uitgevoerd en een [op taak gebaseerde containerinstantie](../container-instances/container-instances-restart-policy.md) maakt om bepaalde gegevens te verwerken, kan het een serviceprincipal met alleen-in-trekmachtigingen gebruiken om te verifiëren aan het register. U vervolgens de referenties van de serviceprincipal roteren of de toegang volledig intrekken zonder dat dit gevolgen heeft voor andere services en toepassingen.

Serviceprincipals moeten ook worden gebruikt wanneer de [registerbeheerder gebruiker](container-registry-authentication.md#admin-account) is uitgeschakeld.

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

## <a name="authenticate-using-the-service-principal"></a>Verifiëren met de serviceprincipal

Als u een container wilt starten in Azure Container `--registry-username`Instances met `--registry-password`behulp van een serviceprincipal, geeft u de id voor en het wachtwoord voor .

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

U vindt de voorgaande voorbeeldscripts voor Azure CLI op GitHub, evenals versies voor Azure PowerShell:

* [Azure-CLI][acr-scripts-cli]
* [Azure PowerShell][acr-scripts-psh]

## <a name="next-steps"></a>Volgende stappen

De volgende artikelen bevatten aanvullende gegevens over het werken met serviceprincipals en ACR:

* [Azure Container Registry-verificatie met serviceprincipals](container-registry-auth-service-principal.md)
* [Verifiëren met Azure Container Registry van Azure Kubernetes Service (AKS)](../aks/cluster-container-registry-integration.md)

<!-- IMAGES -->

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
