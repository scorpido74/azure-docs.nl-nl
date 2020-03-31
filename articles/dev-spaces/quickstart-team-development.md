---
title: Teamontwikkeling op Kubernetes
services: azure-dev-spaces
ms.date: 01/22/2020
ms.topic: quickstart
description: Deze quickstart laat u zien hoe u kubernetes-ontwikkeling doen met containers en microservices met Azure Dev Spaces
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers, Helm, service mesh, service mesh routing, kubectl, k8s
manager: gwallace
ms.openlocfilehash: 0fe177db420913e5d68807dd803df791653c0914
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "78244946"
---
# <a name="quickstart-team-development-on-kubernetes---azure-dev-spaces"></a>Quickstart: Teamontwikkeling op Kubernetes - Azure Dev Spaces

In deze handleiding leert u het volgende:

- Azure Dev Spaces instellen op een beheerd Kubernetes-cluster in Azure.
- Implementeer een grote toepassing met meerdere microservices in een dev-ruimte.
- Test een enkele microservice in een geïsoleerde dev-ruimte in de context van de volledige toepassing.

![Azure Dev Spaces-teamontwikkeling](media/azure-dev-spaces/collaborate-graphic.gif)

## <a name="prerequisites"></a>Vereisten

- Een Azure-abonnement. Als u geen Azure-abonnement hebt, u een [gratis account](https://azure.microsoft.com/free)maken.
- [Azure CLI geïnstalleerd](/cli/azure/install-azure-cli?view=azure-cli-latest).
- [Helm 3 geïnstalleerd][helm-installed].

## <a name="create-an-azure-kubernetes-service-cluster"></a>Een Azure Kubernetes Service-cluster maken

U moet een AKS-cluster maken in een [ondersteund gebied][supported-regions]. Met de onderstaande opdrachten wordt een resourcegroep met de naam *MyResourceGroup* en een AKS-cluster met de naam *MyAKS*gemaakt.

```azurecli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Azure Dev-ruimten inschakelen op uw AKS-cluster

Gebruik `use-dev-spaces` de opdracht om Dev Spaces in te schakelen op uw AKS-cluster en volg de aanwijzingen. Met de onderstaande opdracht schakelt Dev Spaces in op het *MyAKS-cluster* in de groep *MyResourceGroup* en wordt een dev-ruimte met *dev*.

> [!NOTE]
> De `use-dev-spaces` opdracht installeert ook de Azure Dev Spaces CLI als deze nog niet is geïnstalleerd. U de AZURE Dev Spaces CLI niet installeren in de Azure Cloud Shell.

```azurecli
az aks use-dev-spaces -g MyResourceGroup -n MyAKS --space dev --yes
```

## <a name="get-sample-application-code"></a>Voorbeeldtoepassingscode ophalen

In dit artikel gebruikt u de [voorbeeldtoepassing Azure Dev Spaces Bike Sharing](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp) om aan te tonen met Azure Dev Spaces.

Kloon de toepassing uit GitHub en navigeer naar de bijbehorende map:

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/BikeSharingApp/
```

## <a name="retrieve-the-hostsuffix-for-dev"></a>Het hostachtervoegsel voor *dev* ophalen

Gebruik `azds show-context` de opdracht om het hostachtervoegsel voor dev weer te *geven.*

```cmd
$ azds show-context

Name                ResourceGroup     DevSpace  HostSuffix
------------------  ----------------  --------  -----------------------
MyAKS               MyResourceGroup   dev       fedcab0987.eus.azds.io
```

## <a name="update-the-helm-chart-with-your-hostsuffix"></a>De helmdiagram bijwerken met uw hostsuf

Open [grafieken/values.yaml](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/charts/values.yaml) en `<REPLACE_ME_WITH_HOST_SUFFIX>` vervang alle exemplaren van de hostsuffixwaarde die u eerder hebt opgehaald. Sla uw wijzigingen op en sluit het bestand.

## <a name="run-the-sample-application-in-kubernetes"></a>De voorbeeldtoepassing uitvoeren in Kubernetes

De opdrachten voor het uitvoeren van de voorbeeldtoepassing op Kubernetes maken deel uit van een bestaand proces en zijn niet afhankelijk van Azure Dev Spaces-tooling. In dit geval is Helm de tooling die wordt gebruikt om deze voorbeeldtoepassing uit te voeren, maar andere tooling kan worden gebruikt om uw hele toepassing uit te voeren in een naamruimte binnen een cluster. De Helm-opdrachten zijn gericht op de dev-ruimte met *dev die* u eerder hebt gemaakt, maar deze dev-ruimte is ook een Kubernetes-naamruimte. Als gevolg hiervan kunnen dev-ruimten worden getarget door andere gereedschappen die hetzelfde zijn als andere naamruimten.

U Azure Dev Spaces gebruiken voor teamontwikkeling nadat een toepassing wordt uitgevoerd in een cluster, ongeacht de tooling die wordt gebruikt om deze te implementeren.

Gebruik `helm install` de opdracht om de voorbeeldtoepassing op uw cluster in te stellen en te installeren.

```cmd
cd charts/
helm install bikesharingsampleappsampleapp . --dependency-update --namespace dev --atomic
```

Het `helm install` kan enkele minuten duren voordat de opdracht is voltooid. Nadat de voorbeeldtoepassing op uw cluster is geïnstalleerd en u Dev `azds list-uris` Spaces op uw cluster hebt ingeschakeld, gebruikt u de opdracht om de URL's weer te geven voor de voorbeeldtoepassing in *dev* die momenteel is geselecteerd.

```cmd
$ azds list-uris
Uri                                                 Status
--------------------------------------------------  ---------
http://dev.bikesharingweb.fedcab0987.eus.azds.io/  Available
http://dev.gateway.fedcab0987.eus.azds.io/         Available
```

Navigeer naar de *bikesharingweb-service* door `azds list-uris` de openbare URL van de opdracht te openen. In het bovenstaande voorbeeld is `http://dev.bikesharingweb.fedcab0987.eus.azds.io/`de openbare URL voor de *bikesharingweb-service* . Selecteer *Aurelia Briggs (klant)* als gebruiker. Controleer of je de tekst *Hi Aurelia Briggs ziet | Afmelden* aan de bovenkant.

![Voorbeeldtoepassing voor het delen van azure-dev-ruimten](media/quickstart-team-development/bikeshare.png)

## <a name="create-child-dev-spaces"></a>Onderliggende dev-ruimten maken

Gebruik `azds space select` de opdracht om twee onderliggende spaties te maken *onder dev:*

```cmd
azds space select -n dev/azureuser1 -y
azds space select -n dev/azureuser2 -y
```

De bovenstaande opdrachten maken twee onderliggende spaties onder *dev* *azureuser1* en *azureuser2*. Deze twee onderliggende ruimten vertegenwoordigen verschillende dev-ruimten die ontwikkelaars *azureuser1* en *azureuser2* kunnen gebruiken voor het aanbrengen van wijzigingen in de voorbeeldtoepassing.

Gebruik `azds space list` de opdracht om alle dev-ruimten weer te geven en te bevestigen dat *dev/azureuser2* is geselecteerd.

```cmd
$ azds space list
   Name            DevSpacesEnabled
-  --------------  ----------------
   default         False
   dev             True
   dev/azureuser1  True
*  dev/azureuser2  True
```

Gebruik `azds list-uris` de URL's voor de voorbeeldtoepassing in de momenteel geselecteerde ruimte die *dev/azureuser2*is.

```cmd
$ azds list-uris
Uri                                                             Status
--------------------------------------------------              ---------
http://azureuser2.s.dev.bikesharingweb.fedcab0987.eus.azds.io/  Available
http://azureuser2.s.dev.gateway.fedcab0987.eus.azds.io/         Available
```

Controleer of de URL's `azds list-uris` die door de opdracht worden weergegeven, het voorvoegsel *azureuser2.s.dev* hebben. Dit voorvoegsel bevestigt dat de geselecteerde huidige ruimte *azureuser2*is, een onderliggend kind *van dev*.

Navigeer naar de *bikesharingweb-service* voor de *dev/azureuser2-dev-ruimte* door de openbare URL van de `azds list-uris` opdracht te openen. In het bovenstaande voorbeeld is `http://azureuser2.s.dev.bikesharingweb.fedcab0987.eus.azds.io/`de openbare URL voor de *bikesharingweb-service* . Selecteer *Aurelia Briggs (klant)* als gebruiker. Controleer of je de tekst *Hi Aurelia Briggs ziet | Meld je* bovenaan af.

## <a name="update-code"></a>Code bijwerken

Open *BikeSharingWeb/components/Header.js* met een teksteditor en wijzig de tekst in het [spanelement met de `userSignOut` klasseNaam](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/components/Header.js#L16).

```html
<span className="userSignOut">
    <Link href="/devsignin"><span tabIndex="0">Welcome {props.userName} | Sign out</span></Link>
</span>
```

Sla uw wijzigingen op en sluit het bestand.

## <a name="build-and-run-the-updated-bikesharingweb-service-in-the-devazureuser2-dev-space"></a>Bouw en voer de bijgewerkte bikesharingweb-service uit in de *dev/azureuser2* dev-ruimte

Navigeer naar de *map BikeSharingWeb/en* voer de `azds up` opdracht uit.

```cmd
$ cd ../BikeSharingWeb/
$ azds up

Using dev space 'dev/azureuser2' with target 'MyAKS'
Synchronizing files...2s
...
Service 'bikesharingweb' port 'http' is available at http://azureuser2.s.dev.bikesharingweb.fedcab0987.eus.azds.io/
Service 'bikesharingweb' port 80 (http) is available at http://localhost:54256
...
```

Deze opdracht bouwt en voert de *bikesharingweb* service in de *dev / azureuser2* dev ruimte. Deze service wordt uitgevoerd naast de *bikesharingweb-service* die in *dev* wordt uitgevoerd en wordt alleen gebruikt voor aanvragen met het URL-voorvoegsel *azureuser2.s.* Zie [Hoe Azure Dev Spaces werkt en is geconfigureerd](how-dev-spaces-works.md)voor meer informatie over hoe routering werkt tussen bovenliggende en onderliggende dev-ruimten.

Navigeer naar de *bikesharingweb-service* voor de *dev/azureuser2-dev-ruimte* door `azds up` de openbare URL te openen die wordt weergegeven in de uitvoer van de opdracht. Selecteer *Aurelia Briggs (klant)* als gebruiker. Controleer of u de bijgewerkte tekst in de rechterbovenhoek ziet. Mogelijk moet u de pagina vernieuwen of de cache van uw browser wissen als u deze wijziging niet onmiddellijk ziet.

![Voorbeeldtoepassing Azure Dev Spaces Bike Sharing bijgewerkt](media/quickstart-team-development/bikeshare-update.png)

> [!NOTE]
> Wanneer u tijdens het `azds up`uitvoeren naar uw service navigeert, worden `azds up` de HTTP-aanvraagsporen ook weergegeven in de uitvoer van de opdracht. Met deze sporen u uw service oplossen en debuggen. U deze `--disable-http-traces` sporen `azds up`uitschakelen bij het uitvoeren van.

## <a name="verify-other-dev-spaces-are-unchanged"></a>Controleren of andere Dev-spaties ongewijzigd blijven

Als `azds up` de opdracht nog steeds actief is, drukt u op *Ctrl+c*.

```cmd
$ azds list-uris --all
Uri                                                             Status
--------------------------------------------------              ---------
http://azureuser1.s.dev.bikesharingweb.fedcab0987.eus.azds.io/  Available
http://azureuser1.s.dev.gateway.fedcab0987.eus.azds.io/         Available
http://azureuser2.s.dev.bikesharingweb.fedcab0987.eus.azds.io/  Available
http://azureuser2.s.dev.gateway.fedcab0987.eus.azds.io/         Available
http://dev.bikesharingweb.fedcab0987.eus.azds.io/               Available
http://dev.gateway.fedcab0987.eus.azds.io/                      Available
```

Navigeer naar de *dev-versie* van *bikesharingweb* in uw browser, kies *Aurelia Briggs (klant)* als gebruiker en controleer of u de originele tekst in de rechterbovenhoek ziet. Herhaal deze stappen met de *URL dev/azureuser1.* Let op de wijzigingen worden alleen toegepast op de *dev/azureuser2-versie* van *bikesharingweb.* Deze isolatie van wijzigingen in *dev/azureuser2* maakt *azureuser2* mogelijk om wijzigingen aan te brengen zonder *azureuser1*.

Als u deze wijzigingen wilt weergeven in *dev* en *dev/azureuser1,* moet u de bestaande werkstroom of CI/CD-pijplijn van uw team volgen. Deze werkstroom kan bijvoorbeeld betrekking hebben op het vastleggen van uw wijziging in uw versiebeheersysteem en het implementeren van de update met behulp van een CI/CD-pijplijn of tooling zoals Helm.

## <a name="clean-up-your-azure-resources"></a>Uw Azure-bronnen opschonen

```azurecli
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Volgende stappen

Ontdek hoe Azure Dev Spaces u kan helpen om complexere apps te ontwikkelen binnen meerdere containers en hoe u ontwikkeling in samenwerkingsverband vereenvoudigt door in verschillende ruimten met verschillende versies of vertakkingen van uw code te werken.

> [!div class="nextstepaction"]
> [Werken met meerdere containers en teamontwikkeling](multi-service-nodejs.md)

[helm-installed]: https://helm.sh/docs/intro/install/
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
