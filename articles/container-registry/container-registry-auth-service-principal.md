---
title: Verificatie Azure Container Registry met een Service-Principal
description: Toegang bieden tot installatie kopieën in uw persoonlijke container register met behulp van een Azure Active Directory Service-Principal.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 12/13/2018
ms.author: danlep
ms.openlocfilehash: bee8b801f46c0018e75d58f941470adcc271daf0
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/26/2019
ms.locfileid: "70032367"
---
# <a name="azure-container-registry-authentication-with-service-principals"></a>Verificatie Azure Container Registry met Service-principals

U kunt een service-principal voor Azure Active Directory (Azure AD) gebruiken om een `docker push` container `pull` installatie kopie te bieden en toegang te krijgen tot uw container register. Met behulp van een Service-Principal kunt u toegang bieden tot ' headless ' Services en toepassingen.

## <a name="what-is-a-service-principal"></a>Wat is een service-principal?

Azure AD- *service* -principals bieden toegang tot Azure-resources binnen uw abonnement. U kunt een Service-Principal beschouwen als een gebruikers-id voor een service, waarbij ' service ' een toepassing, service of platform is die toegang nodig heeft tot de resources. U kunt een service-principal met toegangs rechten instellen die alleen zijn gericht op de resources die u opgeeft. Configureer vervolgens uw toepassing of service voor het gebruik van de referenties van de service-principal voor toegang tot deze resources.

In de context van Azure Container Registry kunt u een Azure AD-service-principal maken met pull-, push-en pull-toegang of andere machtigingen voor uw persoonlijke REGI ster in Azure. Zie [Azure container Registry rollen en machtigingen](container-registry-roles.md)voor een volledige lijst.

## <a name="why-use-a-service-principal"></a>Waarom een Service-Principal gebruiken?

Als u een service-principal voor Azure AD gebruikt, kunt u toegang tot uw persoonlijke container register verlenen. Maak verschillende service-principals voor elk van uw toepassingen of services, elk met aangepaste toegangs rechten voor uw REGI ster. En omdat u het delen van referenties tussen services en toepassingen kunt voor komen, kunt u referenties draaien of de toegang intrekken voor alleen de Service-Principal (en dus de toepassing) die u kiest.

Configureer bijvoorbeeld uw webtoepassing voor het gebruik van een service-principal die hiermee alleen toegang `pull` geeft tot de installatie kopie, terwijl uw build-systeem een Service-Principal gebruikt `push` die `pull` deze zowel heeft als toegang verschaft. Als de ontwikkeling van uw toepassing wordt gewijzigd, kunt u de referenties van het Service principe draaien zonder dat dit van invloed is op het build-systeem.

## <a name="when-to-use-a-service-principal"></a>Wanneer moet u een Service-Principal gebruiken?

U moet een Service-Principal gebruiken om toegang te krijgen tot het REGI ster in **headless scenario's**. Dat wil zeggen, elke toepassing, service of script dat container installatie kopieën moet pushen of ophalen in een geautomatiseerde of op een andere manier zonder toezicht. Bijvoorbeeld:

  * *Pull*: Implementeer containers vanuit een REGI ster naar Orchestration-systemen, waaronder Kubernetes, DC/OS en docker Swarm. U kunt ook uit container registers halen voor gerelateerde Azure-Services, zoals [Azure Kubernetes service (AKS)](container-registry-auth-aks.md), [Azure container instances](container-registry-auth-aci.md), [app service](../app-service/index.yml), [batch](../batch/index.yml), [service Fabric](/azure/service-fabric/)en anderen.

  * *Push*: Bouw container installatie kopieën en push ze naar een REGI ster met behulp van doorlopende integratie-en implementatie oplossingen zoals Azure pipelines of Jenkins.

Voor individuele toegang tot een REGI ster, zoals wanneer u hand matig een container installatie kopie naar uw ontwikkel werkstation haalt, wordt u aangeraden uw eigen [Azure AD-identiteit](container-registry-authentication.md#individual-login-with-azure-ad) te gebruiken in plaats van toegang tot het REGI ster (bijvoorbeeld met [AZ ACR login][az-acr-login]).

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

### <a name="sample-scripts"></a>Voorbeeldscripts

U kunt de voor gaande voorbeeld scripts voor Azure CLI vinden op GitHub, evenals versies voor Azure PowerShell:

* [Azure-CLI][acr-scripts-cli]
* [Azure PowerShell][acr-scripts-psh]

## <a name="authenticate-with-the-service-principal"></a>Verifiëren met de Service-Principal

Zodra u een Service-Principal hebt die u toegang tot uw container register hebt verleend, kunt u de referenties voor toegang tot ' headless ' Services en toepassingen configureren of ze invoeren met behulp `docker login` van de opdracht. Gebruik de volgende waarden:

* **Gebruikers naam** -id van de Service-Principal-toepassing (ook wel *client-id*genoemd)
* **Wacht** woord-Service-Principal-wacht woord (ook wel *client geheim*genoemd)

Elke waarde is een GUID van het formulier `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`. 

> [!TIP]
> U kunt het wacht woord van een Service-Principal opnieuw genereren door de opdracht [AZ AD SP reset-credentials](/cli/azure/ad/sp/credential#az-ad-sp-credential-reset) uit te voeren.
>

### <a name="use-credentials-with-azure-services"></a>Referenties gebruiken met Azure-Services

U kunt de referenties van de service-principal van een Azure-service gebruiken die kan worden geverifieerd met een Azure container Registry. Voorbeelden zijn:

* [Verifiëren met Azure Container Registry van de Azure Kubernetes-service (AKS)](container-registry-auth-aks.md)
* [Verifiëren met Azure Container Registry van Azure Container Instances (ACI)](container-registry-auth-aci.md)

### <a name="use-with-docker-login"></a>Gebruiken met aanmelden bij docker

U kunt ook uitvoeren `docker login` met behulp van een service-principal. In het volgende voor beeld wordt de toepassings-id van de Service-Principal door `$SP_APP_ID`gegeven in de omgevings variabele en `$SP_PASSWD`het wacht woord in de variabele. Zie voor aanbevolen procedures voor het beheren van docker-referenties de koppeling naar de opdracht [docker login](https://docs.docker.com/engine/reference/commandline/login/) .

```bash
# Log in to Docker with service principal credentials
docker login myregistry.azurecr.io --username $SP_APP_ID --password $SP_PASSWD
```

Zodra de gegevens zijn aangemeld, worden deze door docker in de cache opgeslagen.

## <a name="next-steps"></a>Volgende stappen

* Zie het [verificatie overzicht](container-registry-authentication.md) voor andere scenario's voor verificatie met een Azure container Registry.

* Raadpleeg de zelf studie voor het [maken en implementeren van een container installatie kopie met behulp van ACR-taken](container-registry-tutorial-quick-task.md)voor een voor beeld van het gebruik van een Azure-sleutel kluis voor het opslaan en ophalen van referenties voor de service-principal voor een container register.

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
[az-acr-login]: /cli/azure/acr#az-acr-login
