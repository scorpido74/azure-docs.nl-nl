---
title: GitHub-acties & Azure Kubernetes-service (voorbeeld)
services: azure-dev-spaces
ms.date: 04/03/2020
ms.topic: conceptual
description: Wijzigingen van een pull-aanvraag rechtstreeks in Azure Kubernetes-service bekijken en testen met GitHub-acties en Azure Dev Spaces
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers, GitHub Actions, Helm, service mesh, service mesh routing, kubectl, k8s
manager: gwallace
ms.openlocfilehash: a83da0ef3958748831eb0eeda1aa5e91efa7ef2e
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80637950"
---
# <a name="github-actions--azure-kubernetes-service-preview"></a>GitHub-acties & Azure Kubernetes-service (voorbeeld)

Azure Dev Spaces biedt een werkstroom met GitHub Actions waarmee u wijzigingen vanaf een pull-aanvraag rechtstreeks in AKS testen voordat de pull-aanvraag wordt samengevoegd in de hoofdvestiging van uw opslagplaats. Het hebben van een lopende toepassing om wijzigingen van een pull-aanvraag te bekijken, kan het vertrouwen van zowel de ontwikkelaar als teamleden vergroten. Deze hardloopapplicatie kan ook teamleden zoals productmanagers en ontwerpers helpen deel uit te maken van het beoordelingsproces in een vroeg stadium van de ontwikkeling.

In deze handleiding leert u het volgende:

* Azure Dev Spaces instellen op een beheerd Kubernetes-cluster in Azure.
* Implementeer een grote toepassing met meerdere microservices in een dev-ruimte.
* Stel CI/CD in met GitHub-acties.
* Test een enkele microservice in een geïsoleerde dev-ruimte in de context van de volledige toepassing.

> [!IMPORTANT]
> Deze functie is momenteel beschikbaar als preview-product. Previews worden voor u beschikbaar gesteld op voorwaarde dat u akkoord gaat met de [aanvullende gebruiksvoorwaarden](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Sommige aspecten van deze functionaliteit kunnen wijzigen voordat deze functionaliteit algemeen beschikbaar wordt.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u geen Azure-abonnement hebt, u een [gratis account](https://azure.microsoft.com/free)maken.
* [Azure CLI geïnstalleerd][azure-cli-installed].
* [Helm 3 geïnstalleerd][helm-installed].
* Een GitHub-account met [GitHub-acties ingeschakeld][github-actions-beta-signup].
* De [voorbeeldtoepassing Azure Dev Spaces Bike Sharing](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp/README.md) die wordt uitgevoerd op een AKS-cluster.

## <a name="create-an-azure-container-registry"></a>Een Azure Container Registry maken

Maak een Azure Container Registry (ACR):

```azurecli
az acr create --resource-group MyResourceGroup --name <acrName> --sku Basic
```

> [!IMPORTANT]
> De naam van uw ACR moet uniek zijn binnen Azure en 5-50 alfanumerieke tekens bevatten. Alle letters die u gebruikt, moeten kleine letters zijn.

Sla de *waarde loginServer* op uit de uitvoer omdat deze in een latere stap wordt gebruikt.

## <a name="create-a-service-principal-for-authentication"></a>Een serviceprincipal voor verificatie maken

Gebruik [az ad sp create-for-rbac][az-ad-sp-create-for-rbac] om een serviceprincipal te maken. Bijvoorbeeld:

```azurecli
az ad sp create-for-rbac --sdk-auth --skip-assignment
```

Sla de JSON-uitvoer op omdat deze in een latere stap wordt gebruikt.

Gebruik [de AZ AKS-show][az-aks-show] om de *ID* van uw AKS-cluster weer te geven:

```azurecli
az aks show -g MyResourceGroup -n MyAKS  --query id
```

Gebruik [az acr show][az-acr-show] om de *ID* van de ACR weer te geven:

```azurecli
az acr show --name <acrName> --query id
```

Gebruik [het maken van az-rollenom][az-role-assignment-create] *inzender* toegang te geven tot je AKS-cluster en *AcrPush* toegang tot je ACR.

```azurecli
az role assignment create --assignee <ClientId> --scope <AKSId> --role Contributor
az role assignment create --assignee <ClientId>  --scope <ACRId> --role AcrPush
```

> [!IMPORTANT]
> U moet de eigenaar zijn van zowel uw AKS-cluster als ACR om uw serviceprincipal toegang te geven tot deze bronnen.

## <a name="configure-your-github-action"></a>Uw GitHub-actie configureren

> [!IMPORTANT]
> GitHub Actions is ingeschakeld voor je repository. Als u GitHub Actions voor uw opslagplaats wilt inschakelen, navigeert u naar uw opslagplaats op GitHub, klikt u op het tabblad Acties en kiest u om acties voor deze opslagplaats in te schakelen.

Navigeer naar uw gevorkte opslagplaats en klik op *Instellingen*. Klik op *Geheimen* in de linker zijbalk. Klik *op Een nieuw geheim toevoegen* om hieronder elk nieuw geheim toe te voegen:

1. *AZURE_CREDENTIALS*: de volledige uitvoer van de serviceprincipalcreatie.
1. *RESOURCE_GROUP:* de brongroep voor uw AKS-cluster, dat in dit voorbeeld *MyResourceGroup*is.
1. *CLUSTER_NAME*: de naam van uw AKS-cluster, die in dit voorbeeld *MyAKS*is.
1. *CONTAINER_REGISTRY*: de *loginServer* voor de ACR.
1. *HOST*: de host voor uw Dev Space, die de vorm *aanneemt<MASTER_SPACE>,<APP_NAME>,<HOST_SUFFIX>, *die in dit voorbeeld *wordt dev.bikesharingweb.fedcab0987.eus.azds.io*.
1. *IMAGE_PULL_SECRET*: de naam van het geheim dat u wilt gebruiken, bijvoorbeeld *demo-secret*.
1. *MASTER_SPACE*: de naam van uw ouder Dev Space, die in dit voorbeeld *is dev*.
1. *REGISTRY_USERNAME*: de *clientId* van de JSON-uitvoer van de serviceprincipalcreatie.
1. *REGISTRY_PASSWORD*: de *clientSecret* van de JSON-uitvoer van de serviceprincipalcreatie.

> [!NOTE]
> Al deze geheimen worden gebruikt door de GitHub actie en zijn geconfigureerd in [.github/workflows/bikes.yml][github-action-yaml].

Als u de hoofdruimte wilt bijwerken nadat uw PR is samengevoegd, voegt u het *GATEWAY_HOST-geheim* toe, dat de vorm aanneemt *<MASTER_SPACE>.gateway.<HOST_SUFFIX>*, die in dit voorbeeld *dev.gateway.fedcab0987.eus.azds.io*is. Zodra u uw wijzigingen samenvoegt in de hoofdbranch in uw vork, wordt er een andere actie uitgevoerd om uw hele toepassing opnieuw op te bouwen en uit te voeren in de master-dev-ruimte. In dit voorbeeld is de hoofdruimte *dev.* Deze actie is geconfigureerd in [.github/workflows/bikesharing.yml][github-action-bikesharing-yaml].

Bovendien, als u wilt dat de wijzigingen in uw PR te draaien in een kleinkind ruimte, update de *MASTER_SPACE* en *HOST* geheimen. Als uw toepassing bijvoorbeeld in *dev met* een onderliggende *spatie dev/azureuser1*wordt uitgevoerd, moet u de PR laten uitvoeren in een onderliggende ruimte van *dev/azureuser1:*

* Werk *MASTER_SPACE* bij naar de onderliggende ruimte die u als bovenliggende ruimte wilt hebben, in dit voorbeeld *azureuser1*.
* Update *HOST* *om GRANDPARENT_SPACE>.<>.<APP_NAME>.<HOST_SUFFIX>* in dit voorbeeld *dev.bikesharingweb.fedcab0987.eus.azds.io*<.

## <a name="create-a-new-branch-for-code-changes"></a>Een nieuwe vertakking maken voor codewijzigingen

Navigeer `BikeSharingApp/` naar en maak een nieuwe tak genaamd *fiets-afbeeldingen*.

```cmd
cd dev-spaces/samples/BikeSharingApp/
git checkout -b bike-images
```

[Bikes/server.js][bikes-server-js] bewerken om de lijnen 232 en 233 te verwijderen:

```javascript
    // Hard code image url *FIX ME*
    theBike.imageUrl = "/static/logo.svg";
```

De sectie moet er nu uitzien als:

```javascript
    var theBike = result;
    theBike.id = theBike._id;
    delete theBike._id;
```

Sla het bestand `git add` `git commit` op en gebruik vervolgens en om uw wijzigingen in scène te zetten.

```cmd
git add Bikes/server.js 
git commit -m "Removing hard coded imageUrl from /bikes/:id route"
```

## <a name="push-your-changes"></a>Druk op uw wijzigingen

Gebruik `git push` om je nieuwe branch naar je gevorkte repository te pushen:

```cmd
git push origin bike-images
```

Nadat de push is voltooid, navigeerje naar je gevorkte repository op GitHub om een pull-aanvraag te maken met de *master* branch in je gevorkte repository als basisbranch in vergelijking met de *fiets-afbeeldingen* branch.

Nadat uw pull-aanvraag is geopend, navigeert u naar het tabblad *Acties.* Controleer of er een nieuwe actie is gestart en de service *Fietsen* wordt gebouwd.

## <a name="view-the-child-space-with-your-changes"></a>De onderliggende ruimte weergeven met uw wijzigingen

Nadat de actie is voltooid, ziet u een opmerking met een URL naar de nieuwe onderliggende ruimte op basis van de wijzigingen in de pull-aanvraag.

> [!div class="mx-imgBorder"]
> ![Url van GitHub-actie](../media/github-actions/github-action-url.png)

Navigeer naar de *bikesharingweb-service* door de URL van de opmerking te openen. Selecteer *Aurelia Briggs (klant)* als gebruiker en selecteer vervolgens een fiets om te huren. Controleer of u de tijdelijke aanduidingafbeelding voor de fiets niet meer ziet.

Als u de wijzigingen *master* samenvoegt in de hoofdvertakking in uw vork, wordt er een andere actie uitgevoerd om uw hele toepassing opnieuw op te bouwen en uit te voeren in de bovenliggende dev-ruimte. In dit voorbeeld is de bovenliggende ruimte *dev*. Deze actie is geconfigureerd in [.github/workflows/bikesharing.yml][github-action-bikesharing-yaml].

## <a name="clean-up-your-azure-resources"></a>Uw Azure-bronnen opschonen

```azurecli
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Volgende stappen

Ontdek hoe Azure Dev Spaces u helpt complexere toepassingen te ontwikkelen voor meerdere containers en hoe u de samenwerking vereenvoudigen door te werken met verschillende versies of branches van uw code in verschillende ruimten.

> [!div class="nextstepaction"]
> [Teamontwikkeling in Azure Dev Spaces][team-quickstart]

[azure-cli-installed]: /cli/azure/install-azure-cli?view=azure-cli-latest
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-aks-show]: /cli/azure/aks?view=azure-cli-latest#az-aks-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[bikes-server-js]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/Bikes/server.js#L232-L233
[bike-sharing-gh]: https://github.com/Azure/dev-spaces/
[bike-sharing-values-yaml]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/charts/values.yaml
[github-actions-beta-signup]: https://github.com/features/actions
[github-action-yaml]: https://github.com/Azure/dev-spaces/blob/master/.github/workflows/bikes.yml
[github-action-bikesharing-yaml]: https://github.com/Azure/dev-spaces/blob/master/.github/workflows/bikesharing.yml
[helm-installed]: https://helm.sh/docs/intro/install/
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[sp-acr]: ../../container-registry/container-registry-auth-service-principal.md
[sp-aks]: ../../aks/kubernetes-service-principal.md
[team-quickstart]: ../quickstart-team-development.md
