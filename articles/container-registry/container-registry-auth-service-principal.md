---
title: Verifiëren met service-principal
description: Geef toegang tot afbeeldingen in uw privécontainerregister met behulp van een Azure Active Directory-serviceprincipal.
ms.topic: article
ms.date: 10/04/2019
ms.openlocfilehash: 37da784c8e95a5f5b924532e4a019552924a1a3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74455404"
---
# <a name="azure-container-registry-authentication-with-service-principals"></a>Azure Container Registry-verificatie met serviceprincipals

U een Azure Active Directory -serviceprincipal (Azure AD) gebruiken om containerafbeelding `docker push` en `pull` toegang tot uw containerregister te bieden. Door gebruik te maken van een serviceprincipal u toegang bieden tot headless services en applicaties.

## <a name="what-is-a-service-principal"></a>Wat is een service-principal?

Azure *AD-serviceprincipals* bieden toegang tot Azure-bronnen binnen uw abonnement. U een serviceprincipal zien als een gebruikersidentiteit voor een service, waarbij 'service' een toepassing, service of platform is die toegang nodig heeft tot de bronnen. U een serviceprincipal configureren met toegangsrechten die alleen worden afgestemd op de resources die u opgeeft. Configureer vervolgens uw toepassing of service om de referenties van de serviceprincipal te gebruiken om toegang te krijgen tot deze bronnen.

In de context van Azure Container Registry u een Azure AD-serviceprincipal maken met pull, push and pull of andere machtigingen voor uw privéregister in Azure. Zie Azure Container [Registry-rollen en machtigingen](container-registry-roles.md)voor een volledige lijst.

## <a name="why-use-a-service-principal"></a>Waarom een serviceprincipal gebruiken?

Door een Azure AD-serviceprincipal te gebruiken, u scoped-toegang bieden tot uw privécontainerregister. Maak verschillende serviceprincipals voor elk van uw toepassingen of services, elk met op maat gemaakte toegangsrechten voor uw register. En omdat u voorkomen dat referenties worden gedeeld tussen services en toepassingen, u referenties roteren of de toegang intrekken voor alleen de serviceprincipal (en dus de toepassing) die u kiest.

Configureer bijvoorbeeld uw webtoepassing om een serviceprincipal `pull` te gebruiken die deze alleen voorziet van toegang `push` `pull` tot afbeeldingen, terwijl uw buildsysteem een serviceprincipal gebruikt die deze service-hoofd biedt voor beide en toegang. Als de ontwikkeling van uw toepassing van eigenaar verandert, u de servicehoofdreferenties roteren zonder dat dit gevolgen heeft voor het buildsysteem.

## <a name="when-to-use-a-service-principal"></a>Wanneer een serviceprincipal gebruiken

U moet een serviceprincipal gebruiken om registertoegang te bieden in **headless-scenario's.** Dat wil zeggen, elke toepassing, service of script die containerafbeeldingen op een geautomatiseerde of anderszins onbeheerde manier moet pushen of trekken. Bijvoorbeeld:

  * *Pull:* Implementeer containers vanuit een register naar orchestration-systemen, waaronder Kubernetes, DC/OS en Docker Swarm. U ook uit containerregisters naar gerelateerde Azure-services, zoals [Azure Kubernetes Service (AKS),](../aks/cluster-container-registry-integration.md) [Azure Container Instances,](container-registry-auth-aci.md) [App Service](../app-service/index.yml), [Batch,](../batch/index.yml) [Service Fabric](/azure/service-fabric/)en anderen.

  * *Push:* Bouw containerafbeeldingen en duw ze naar een register met behulp van oplossingen voor continue integratie en implementatie, zoals Azure Pipelines of Jenkins.

Voor individuele toegang tot een register, zoals wanneer u handmatig een containerafbeelding naar uw ontwikkelingswerkstation trekt, raden we u aan uw eigen [Azure AD-identiteit](container-registry-authentication.md#individual-login-with-azure-ad) te gebruiken in plaats daarvan voor registertoegang (bijvoorbeeld met [az acr-login).][az-acr-login]

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

### <a name="sample-scripts"></a>Voorbeeldscripts

U de voorgaande voorbeeldscripts voor Azure CLI vinden op GitHub en versies voor Azure PowerShell:

* [Azure-CLI][acr-scripts-cli]
* [Azure PowerShell][acr-scripts-psh]

## <a name="authenticate-with-the-service-principal"></a>Verifiëren met de serviceprincipal

Zodra u een serviceprincipal hebt die u toegang hebt verleend tot uw containerregister, u de referenties configureren `docker login` voor toegang tot 'headless'-services en -toepassingen of deze invoeren met behulp van de opdracht. Gebruik de volgende waarden:

* **Gebruikersnaam** - servicehoofdtoepassings-id (ook wel *client-id*genoemd)
* **Wachtwoord** - service principal password (ook wel *client secret*genoemd)

Elke waarde is een GUID van het formulier `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`. 

> [!TIP]
> U het wachtwoord van een serviceprincipal opnieuw genereren door de opdracht [az-ad sp reset-credentials](/cli/azure/ad/sp/credential#az-ad-sp-credential-reset) uit te voeren.
>

### <a name="use-credentials-with-azure-services"></a>Referenties gebruiken met Azure-services

U serviceprincipal-referenties gebruiken van elke Azure-service die wordt geverifieerd met een Azure-containerregister.  Gebruik servicehoofdreferenties in plaats van de beheerdersreferenties van het register voor verschillende scenario's.

Gebruik bijvoorbeeld de referenties om een afbeelding uit een Azure-containerregister naar [Azure Container Instances](container-registry-auth-aci.md)te halen.

### <a name="use-with-docker-login"></a>Gebruik met docker-aanmelding

U kunt `docker login` uitvoeren met behulp van een serviceprincipal. In het volgende voorbeeld wordt de serviceprincipal-toepassings-id doorgegeven in de omgevingsvariabele `$SP_APP_ID`en het wachtwoord in de variabele `$SP_PASSWD`. Zie de verwijzing naar de [inlogopdracht voor docker](https://docs.docker.com/engine/reference/commandline/login/) voor aanbevolen procedures voor het beheren van docker-inloggegevens.

```bash
# Log in to Docker with service principal credentials
docker login myregistry.azurecr.io --username $SP_APP_ID --password $SP_PASSWD
```

Eenmaal ingelogd, slaat Docker de referenties in de cache.

### <a name="use-with-certificate"></a>Gebruiken met certificaat

Als u een certificaat aan uw serviceprincipal hebt toegevoegd, u zich aanmelden bij de Azure CLI met verificatie op basis van certificaten en vervolgens de [inlogopdracht az acr][az-acr-login] gebruiken om toegang te krijgen tot een register. Het gebruik van een certificaat als geheim in plaats van een wachtwoord biedt extra beveiliging wanneer u de CLI gebruikt. 

Er kan een zelfondertekend certificaat worden gemaakt wanneer u [een serviceprincipal maakt.](/cli/azure/create-an-azure-service-principal-azure-cli) Of voeg een of meer certificaten toe aan een bestaande serviceprincipal. Als u bijvoorbeeld een van de scripts in dit artikel gebruikt om een serviceprincipal te maken of bij te werken met rechten om afbeeldingen uit een register te halen of te pushen, voegt u een certificaat toe met de opdracht [az-ad sp-referenties opnieuw instellen.][az-ad-sp-credential-reset]

Als u de serviceprincipal met certificaat wilt gebruiken om u aan te [melden bij de Azure CLI,](/cli/azure/authenticate-azure-cli#sign-in-with-a-service-principal)moet het certificaat in PEM-indeling zijn en de privésleutel bevatten. Als uw certificaat niet in de vereiste indeling `openssl` is, gebruikt u een hulpmiddel om het te converteren. Wanneer u [az-inlogt][az-login] om u aan te melden bij de CLI met behulp van de serviceprincipal, moet u ook de toepassings-id van de serviceprincipal en de Active Directory-tenant-id verstrekken. In het volgende voorbeeld worden deze waarden weergegeven als omgevingsvariabelen:

```azurecli
az login --service-principal --username $SP_APP_ID --tenant $SP_TENANT_ID  --password /path/to/cert/pem/file
```

Voer vervolgens [az acr-login][az-acr-login] uit om te verifiëren bij het register:

```azurecli
az acr login --name myregistry
```

De CLI gebruikt het token `az login` dat is gemaakt toen u uw sessie verifieerde met het register.

## <a name="next-steps"></a>Volgende stappen

* Zie het [verificatieoverzicht](container-registry-authentication.md) voor andere scenario's om te verifiëren met een Azure-containerregister.

* Zie de zelfstudie voor het bouwen en implementeren van [een containerafbeelding met ACR-taken](container-registry-tutorial-quick-task.md)voor een voorbeeld van het gebruik van een Azure-sleutelkluis om servicehoofdreferenties voor een containerregister op te slaan en op te halen.

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-login]: /cli/azure/reference-index#az-login
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset
