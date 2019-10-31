---
title: GitHub-acties & Azure Kubernetes-service
titleSuffix: Azure Dev Spaces
author: zr-msft
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.author: zarhoads
ms.date: 10/24/2019
ms.topic: conceptual
description: Bekijk en test wijzigingen van een pull-aanvraag rechtstreeks in azure Kubernetes service met behulp van GitHub-acties en Azure dev Spaces.
keywords: Docker, Kubernetes, azure, AKS, Azure Kubernetes service, containers, GitHub acties, helm, Service-Mesh, Service-Mesh-route ring, kubectl, K8S
manager: gwallace
ms.openlocfilehash: 2638fe2cd12407e43d3b3b698cdfca5231362db4
ms.sourcegitcommit: f7f70c9bd6c2253860e346245d6e2d8a85e8a91b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73065936"
---
# <a name="github-actions--azure-kubernetes-service-preview"></a>GitHub acties & Azure Kubernetes service (preview)

Azure dev Spaces biedt een werk stroom met behulp van GitHub-acties waarmee u wijzigingen van een pull-aanvraag rechtstreeks in AKS kunt testen voordat de pull-aanvraag wordt samengevoegd in de hoofd vertakking van de opslag plaats. Als een toepassing wordt uitgevoerd om de wijzigingen van een pull-aanvraag te bekijken, kan het vertrouwen van zowel de ontwikkelaar als de team leden toenemen. Deze actieve toepassing kan ook team leden, zoals product managers en ontwerpers, deel nemen aan het beoordelings proces tijdens de eerste fase van de ontwikkeling.

In deze handleiding leert u het volgende:

- Stel Azure-ontwikkel ruimten in op een beheerd Kubernetes-cluster in Azure.
- Implementeer een grote toepassing met meerdere micro Services naar een dev-ruimte.
- Stel CI/CD in met GitHub-acties.
- Test één micro service in een geïsoleerde ontwikkel ruimte binnen de context van de volledige toepassing.

> [!IMPORTANT]
> Deze functie is momenteel beschikbaar als preview-product. Previews worden voor u beschikbaar gesteld op voorwaarde dat u akkoord gaat met de [aanvullende gebruiksvoorwaarden](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Sommige aspecten van deze functie worden mogelijk nog gewijzigd voordat de functie algemeen beschikbaar wordt.

## <a name="prerequisites"></a>Vereisten

- Een Azure-abonnement. Als u geen Azure-abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free) maken.
- [Azure CLI geïnstalleerd][azure-cli-installed].
- [Helm 2,13 of hoger is geïnstalleerd][helm-installed].
- Een GitHub-account waarvoor [github-acties zijn ingeschakeld][github-actions-beta-signup].

## <a name="create-an-azure-kubernetes-service-cluster"></a>Een Azure Kubernetes service-cluster maken

U moet een AKS-cluster in een [ondersteunde regio][supported-regions]maken. Met de onderstaande opdrachten maakt u een resource groep met de naam *MyResourceGroup* en een AKS-cluster met de naam *MyAKS*.

```cmd
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --disable-rbac --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Azure dev Spaces inschakelen op uw AKS-cluster

Gebruik de `use-dev-spaces` opdracht om ontwikkel ruimten in uw AKS-cluster in te scha kelen en de prompts te volgen. De onderstaande opdracht maakt ontwikkel ruimten in het *MyAKS* -cluster in de *MyResourceGroup* -groep mogelijk en maakt een ontwikkel ruimte met de naam *dev*.

> [!NOTE]
> De `use-dev-spaces`-opdracht installeert ook de Azure dev Space CLI als deze nog niet is geïnstalleerd. U kunt de CLI voor Azure dev Spaces niet installeren in de Azure Cloud Shell.

```cmd
az aks use-dev-spaces -g MyResourceGroup -n MyAKS --space dev --yes
```

## <a name="create-an-azure-container-registry"></a>Een Azure Container Registry maken

Een Azure Container Registry maken (ACR):

```cmd
az acr create --resource-group MyResourceGroup --name <acrName> --sku Basic
```

> [!IMPORTANT]
> De naam van uw ACR moet uniek zijn binnen Azure en mag 5-50 alfanumerieke tekens bevatten. De letters die u gebruikt, moeten kleine letters zijn.

Sla de waarde *login server* op uit de uitvoer omdat deze wordt gebruikt in een latere stap.

## <a name="create-a-service-principal-for-authentication"></a>Een service-principal voor verificatie maken

Gebruik [AZ AD SP create-for-RBAC][az-ad-sp-create-for-rbac] om een service-principal te maken. Bijvoorbeeld:

```cmd
az ad sp create-for-rbac --sdk-auth --skip-assignment
```

Sla de JSON-uitvoer op omdat deze wordt gebruikt in een latere stap.


Gebruik [AZ AKS show][az-aks-show] om de id van uw AKS *-* cluster weer te geven:

```cmd
az aks show -g MyResourceGroup -n MyAKS  --query id
```

Gebruik [AZ ACR show][az-acr-show] om de *id* van de ACR weer te geven:

```cmd
az acr show --name <acrName> --query id
```

Gebruik [AZ Role Assignment Create][az-role-assignment-create] om *Inzender* toegang te geven tot uw AKS-cluster en *AcrPush* toegang tot uw ACR.

```cmd
az role assignment create --assignee <ClientId> --scope <AKSId> --role Contributor
az role assignment create --assignee <ClientId>  --scope <ACRId> --role AcrPush
```

> [!IMPORTANT]
> U moet de eigenaar zijn van uw AKS-cluster en ACR om uw Service-Principal toegang te geven tot deze resources.

## <a name="get-sample-application-code"></a>Voorbeeld toepassings code ophalen

In dit artikel gebruikt u de [voorbeeld toepassing delen van Azure dev Spaces Bike][bike-sharing-gh] om te demonstreren hoe u Azure dev Spaces gebruikt met github-acties.

Splits de Azure dev Spaces-voorbeeld opslagplaats en navigeer vervolgens naar uw gevorkte opslag plaats. Klik op het tabblad *acties* en kies ervoor om acties voor deze opslag plaats in te scha kelen.

Kloon uw gevorkte opslag plaats en navigeer naar de map:

```cmd
git clone https://github.com/USERNAME/dev-spaces
cd dev-spaces/samples/BikeSharingApp/
```

## <a name="retrieve-the-hostsuffix-for-dev"></a>De HostSuffix voor *dev* ophalen

Gebruik de `azds show-context` opdracht om de HostSuffix voor *dev*weer te geven.

```cmd
$ azds show-context

Name                ResourceGroup     DevSpace  HostSuffix
------------------  ----------------  --------  -----------------------
MyAKS               MyResourceGroup   dev       fedcab0987.eus.azds.io
```

## <a name="update-the-helm-chart-with-your-hostsuffix"></a>Het helm-diagram bijwerken met uw HostSuffix

Open [grafieken/waarden. yaml][bike-sharing-values-yaml] en vervang alle exemplaren van `<REPLACE_ME_WITH_HOST_SUFFIX>` door de HostSuffix-waarde die u eerder hebt opgehaald. Sla de wijzigingen op en sluit het bestand.

## <a name="run-the-sample-application-in-kubernetes"></a>De voorbeeld toepassing uitvoeren in Kubernetes

De opdrachten voor het uitvoeren van de voorbeeld toepassing op Kubernetes maken deel uit van een bestaand proces en hebben geen afhankelijkheid van Azure dev Spaces-hulpprogram ma's. In dit geval is helm het hulp programma dat wordt gebruikt om deze voorbeeld toepassing uit te voeren, maar andere hulp middelen kunnen worden gebruikt om uw hele toepassing uit te voeren in een naam ruimte binnen een cluster. De helm-opdrachten zijn gericht op de ontwikkel ruimte met de naam *dev* die u eerder hebt gemaakt, maar deze dev Space is ook een Kubernetes-naam ruimte. Als gevolg hiervan kunnen ontwikkel ruimten worden gericht door andere hulp middelen die hetzelfde zijn als andere naam ruimten.

U kunt Azure dev Spaces gebruiken voor ontwikkeling nadat een toepassing wordt uitgevoerd in een cluster, ongeacht het hulp programma dat wordt gebruikt om het te implementeren.

Gebruik de opdrachten `helm init` en `helm install` om de voorbeeld toepassing in uw cluster in te stellen en te installeren.

```cmd
cd charts/
helm init --wait
helm install -n bikesharing . --dep-up --namespace dev --atomic
```

> [!Note]
> **Als u een cluster met RBAC-functionaliteit gebruikt**, moet u [een service account configureren voor Tiller][tiller-rbac]. Als dat niet het geval is, mislukken `helm`-opdrachten.

Het kan enkele minuten duren voordat de `helm install` opdracht is voltooid. De uitvoer van de opdracht toont de status van alle services die tijdens de voltooiing van het cluster zijn geïmplementeerd:

```cmd
$ cd charts/
$ helm init --wait
...
Happy Helming!

$ helm install -n bikesharing . --dep-up --namespace dev --atomic

Hang tight while we grab the latest from your chart repositories...
...
NAME               READY  UP-TO-DATE  AVAILABLE  AGE
bikes              1/1    1           1          4m32s
bikesharingweb     1/1    1           1          4m32s
billing            1/1    1           1          4m32s
gateway            1/1    1           1          4m32s
reservation        1/1    1           1          4m32s
reservationengine  1/1    1           1          4m32s
users              1/1    1           1          4m32s
```

Nadat de voorbeeld toepassing op uw cluster is geïnstalleerd en omdat er ontwikkel ruimten zijn ingeschakeld op uw cluster, gebruikt u de opdracht `azds list-uris` om de Url's voor de voorbeeld toepassing weer te geven in *dev* die momenteel is geselecteerd.

```cmd
$ azds list-uris
Uri                                                 Status
--------------------------------------------------  ---------
http://dev.bikesharingweb.fedcab0987.eus.azds.io/  Available
http://dev.gateway.fedcab0987.eus.azds.io/         Available
```

Ga naar de *bikesharingweb* -service door de open bare URL te openen via de `azds list-uris` opdracht. In het bovenstaande voor beeld is de open bare URL voor de *bikesharingweb* -service `http://dev.bikesharingweb.fedcab0987.eus.azds.io/`. Selecteer *Aurelia Briggs (klant)* als de gebruiker en selecteer vervolgens een te huur fiets. Controleer of er een tijdelijke afbeelding voor de fiets wordt weer geven.

## <a name="configure-your-github-action"></a>Uw GitHub-actie configureren

Navigeer naar uw gevorkte opslag plaats en klik op *instellingen*. Klik op *geheimen* in de zijbalk links. Klik op *een nieuw geheim toevoegen* om elk nieuw geheim hieronder toe te voegen:

1. *AZURE_CREDENTIALS*: de volledige uitvoer van het maken van de Service-Principal.
1. *RESOURCE_GROUP*: de resource groep voor uw AKS-cluster, in dit voor beeld *MyResourceGroup*.
1. *CLUSTER_NAME*: de naam van uw AKS-cluster, in dit voor beeld *MyAKS*.
1. *CONTAINER_REGISTRY*: de *login server* voor de ACR.
1. *Host*: de host voor uw dev-ruimte, die de vorm *< MASTER_SPACE >. < APP_NAME >. < HOST_SUFFIX >* , die in dit voor beeld is *dev.bikesharingweb.fedcab0987.Eus.azds.io*.
1. *IMAGE_PULL_SECRET*: de naam van het geheim dat u wilt gebruiken, bijvoorbeeld *demo-geheim*.
1. *MASTER_SPACE*: de naam van de bovenliggende ontwikkel ruimte, die in dit voor beeld *dev*is.
1. *REGISTRY_USERNAME*: de *CLIENTID* van de JSON-uitvoer van de Service-Principal is gemaakt.
1. *REGISTRY_PASSWORD*: de *CLIENTSECRET* van de JSON-uitvoer van het maken van de Service-Principal.

> [!NOTE]
> Al deze geheimen worden gebruikt door de GitHub-actie en zijn geconfigureerd in [. github/workflows/Bikes. yml][github-action-yaml].

## <a name="create-a-new-branch-for-code-changes"></a>Een nieuwe vertakking maken voor code wijzigingen

Ga terug naar `BikeSharingApp/` en maak een nieuwe vertakking met de naam *fiets installatie kopieën*.

```cmd
cd ..
git checkout -b bike-images
```

Edit [Bikes/server. js][bikes-server-js] om de regels 232 en 233 te verwijderen:

```javascript
    // Hard code image url *FIX ME*
    theBike.imageUrl = "/static/logo.svg";
```

De sectie moet er nu als volgt uitzien:

```javascript
    var theBike = result;
    theBike.id = theBike._id;
    delete theBike._id;
```

Sla het bestand op en gebruik `git add` en `git commit` om uw wijzigingen te bewerken.

```cmd
git add Bikes/server.js 
git commit -m "Removing hard coded imageUrl from /bikes/:id route"
```

## <a name="push-your-changes"></a>Uw wijzigingen pushen

Gebruik `git push` om uw nieuwe vertakking naar uw gevorkte opslag plaats te pushen:

```cmd
git push origin bike-images
```

Nadat de push is voltooid, gaat u naar de gesplitste opslag plaats op GitHub maakt u een pull-aanvraag met de *Master* in uw gevorkte opslag plaats als basis vertakking in vergelijking met de vertakking van de *fiets installatie kopieën* .

Nadat uw pull-aanvraag is geopend, gaat u naar het tabblad *acties* . Controleer of de nieuwe actie is gestart en bouwt de *Bikes* -service.

## <a name="view-the-child-space-with-your-changes"></a>De onderliggende ruimte met uw wijzigingen weer geven

Nadat de actie is voltooid, ziet u een opmerking met een URL naar uw nieuwe onderliggende ruimte op basis van de wijzigingen in de pull-aanvraag.

> [!div class="mx-imgBorder"]
> ![actie-URL voor GitHub](../media/github-actions/github-action-url.png)

Ga naar de *bikesharingweb* -service door de URL te openen in de opmerking. Selecteer *Aurelia Briggs (klant)* als de gebruiker en selecteer vervolgens een te huur fiets. Controleer of de tijdelijke aanduiding voor de fiets niet meer wordt weer geven.

## <a name="clean-up-your-azure-resources"></a>Uw Azure-resources opschonen

```cmd
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over hoe Azure dev Spaces u helpt om complexere toepassingen te ontwikkelen in meerdere containers en hoe u samenwerkings ontwikkeling kunt vereenvoudigen door te werken met verschillende versies of vertakkingen van uw code in verschillende ruimten.

> [!div class="nextstepaction"]
> [Team ontwikkeling in azure dev Spaces][team-quickstart]

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
[helm-installed]: https://helm.sh/docs/using_helm/#installing-helm
[tiller-rbac]: https://helm.sh/docs/using_helm/#role-based-access-control
[supported-regions]: ../about.md#supported-regions-and-configurations
[sp-acr]: ../../container-registry/container-registry-auth-service-principal.md
[sp-aks]: ../../aks/kubernetes-service-principal.md
[team-quickstart]: ../quickstart-team-development.md