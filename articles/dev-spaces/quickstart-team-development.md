---
title: Team ontwikkeling op Kubernetes met behulp van Azure dev Spaces
titleSuffix: Azure Dev Spaces
author: zr-msft
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.author: zarhoads
ms.date: 04/25/2019
ms.topic: quickstart
description: Ontwikkeling van team Kubernetes met containers en micro Services op Azure
keywords: Docker, Kubernetes, azure, AKS, Azure Kubernetes service, containers, helm, service-net, service mesh routing, kubectl, K8S
manager: gwallace
ms.openlocfilehash: 9bdd52f2c120d338859259b046f7f4b97bbc4eed
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73585200"
---
# <a name="quickstart-team-development-on-kubernetes-using-azure-dev-spaces"></a>Snelstartgids: team ontwikkeling op Kubernetes met behulp van Azure dev Spaces

In deze handleiding leert u het volgende:

- Stel Azure-ontwikkel ruimten in op een beheerd Kubernetes-cluster in Azure.
- Implementeer een grote toepassing met meerdere micro Services naar een dev-ruimte.
- Test één micro service in een geïsoleerde ontwikkel ruimte binnen de context van de volledige toepassing.

![Ontwikkeling van Azure dev Spaces-team](media/azure-dev-spaces/collaborate-graphic.gif)

## <a name="prerequisites"></a>Vereisten

- Een Azure-abonnement. Als u geen abonnement op Azure hebt, kunt u een [gratis account](https://azure.microsoft.com/free) maken.
- [Azure CLI geïnstalleerd](/cli/azure/install-azure-cli?view=azure-cli-latest).
- [Helm 2,13 of hoger is geïnstalleerd][helm-installed].

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

## <a name="get-sample-application-code"></a>Voorbeeld toepassings code ophalen

In dit artikel gebruikt u de [voorbeeld toepassing delen van Azure dev Spaces Bike](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp) om te demonstreren hoe u Azure dev Spaces gebruikt.

Kloon de toepassing uit GitHub en navigeer naar de bijbehorende map:

```cmd
git clone https://github.com/Azure/dev-spaces
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

Open [grafieken/waarden. yaml](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/charts/values.yaml) en vervang alle exemplaren van `<REPLACE_ME_WITH_HOST_SUFFIX>` door de HostSuffix-waarde die u eerder hebt opgehaald. Sla de wijzigingen op en sluit het bestand.

## <a name="run-the-sample-application-in-kubernetes"></a>De voorbeeld toepassing uitvoeren in Kubernetes

De opdrachten voor het uitvoeren van de voorbeeld toepassing op Kubernetes maken deel uit van een bestaand proces en hebben geen afhankelijkheid van Azure dev Spaces-hulpprogram ma's. In dit geval is helm het hulp programma dat wordt gebruikt om deze voorbeeld toepassing uit te voeren, maar andere hulp middelen kunnen worden gebruikt om uw hele toepassing uit te voeren in een naam ruimte binnen een cluster. De helm-opdrachten zijn gericht op de ontwikkel ruimte met de naam *dev* die u eerder hebt gemaakt, maar deze dev Space is ook een Kubernetes-naam ruimte. Als gevolg hiervan kunnen ontwikkel ruimten worden gericht door andere hulp middelen die hetzelfde zijn als andere naam ruimten.

U kunt Azure dev Spaces gebruiken voor team ontwikkeling nadat een toepassing in een cluster wordt uitgevoerd, ongeacht het hulp programma dat wordt gebruikt om het te implementeren.

Gebruik de opdrachten `helm init` en `helm install` om de voorbeeld toepassing in uw cluster in te stellen en te installeren.

```cmd
cd charts/
helm init --wait
helm install -n bikesharing . --dep-up --namespace dev --atomic 
```
> [!Note]
> **Als u een cluster met RBAC-functionaliteit gebruikt**, moet u [een service account configureren voor Tiller](https://helm.sh/docs/using_helm/#role-based-access-control). Anders worden `helm`-opdrachten niet uitgevoerd.

Het kan enkele minuten duren voordat de `helm install`-opdracht is voltooid. De uitvoer van de opdracht toont de status van alle services die tijdens de voltooiing van het cluster zijn geïmplementeerd:

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

Ga naar de *bikesharingweb* -service door de open bare URL te openen via de `azds list-uris` opdracht. In het bovenstaande voor beeld is de open bare URL voor de *bikesharingweb* -service `http://dev.bikesharingweb.fedcab0987.eus.azds.io/`. Selecteer *Aurelia Briggs (klant)* als de gebruiker. Controleer of de tekst *Hi Aurelia Briggs | Meld* u aan de bovenkant aan.

![Voorbeeld toepassing voor delen van Azure dev Spaces](media/quickstart-team-development/bikeshare.png)

## <a name="create-child-dev-spaces"></a>Onderliggende ontwikkel ruimten maken

Gebruik de `azds space select` opdracht om twee onderliggende ruimten te maken onder *dev*:

```cmd
azds space select -n dev/azureuser1 -y
azds space select -n dev/azureuser2 -y
```

Met de bovenstaande opdrachten maakt u twee onderliggende ruimten onder *dev* met de naam *azureuser1* en *azureuser2*. Deze twee onderliggende ruimten vertegenwoordigen afzonderlijke ontwikkel ruimten voor de *azureuser1* en *azureuser2* van ontwikkel aars voor het aanbrengen van wijzigingen aan de voorbeeld toepassing.

Gebruik de `azds space list` opdracht om alle ontwikkel ruimten weer te geven en te bevestigen dat *dev/azureuser2* is geselecteerd.

```cmd
$ azds space list
   Name            DevSpacesEnabled
-  --------------  ----------------
   default         False
   dev             True
   dev/azureuser1  True
*  dev/azureuser2  True
```

Gebruik de `azds list-uris` om de Url's voor de voorbeeld toepassing weer te geven in de momenteel geselecteerde ruimte die *dev/azureuser2*is.

```cmd
$ azds list-uris
Uri                                                             Status
--------------------------------------------------              ---------
http://azureuser2.s.dev.bikesharingweb.fedcab0987.eus.azds.io/  Available
http://azureuser2.s.dev.gateway.fedcab0987.eus.azds.io/         Available
```

Controleer of de Url's die worden weer gegeven door de `azds list-uris` opdracht het voor voegsel *azureuser2. s. dev* hebben. Met dit voor voegsel wordt bevestigd dat de huidige geselecteerde ruimte *azureuser2*is. Dit is een onderliggend element van *dev*.

Ga naar de *bikesharingweb* -service voor de dev */azureuser2 dev-* ruimte door de open bare URL te openen via de `azds list-uris` opdracht. In het bovenstaande voor beeld is de open bare URL voor de *bikesharingweb* -service `http://azureuser2.s.dev.bikesharingweb.fedcab0987.eus.azds.io/`. Selecteer *Aurelia Briggs (klant)* als de gebruiker. Controleer of de tekst *Hi Aurelia Briggs | Meld* u aan de bovenkant aan.

## <a name="update-code"></a>Code bijwerken

Open *BikeSharingWeb/Components/header. js* met een tekst editor en wijzig de tekst in het [SPAN-element met de `userSignOut` className](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/components/Header.js#L16).

```html
<span className="userSignOut">
    <Link href="/devsignin"><span tabIndex="0">Welcome {props.userName} | Sign out</span></Link>
</span>
```

Sla de wijzigingen op en sluit het bestand.

## <a name="build-and-run-the-updated-bikesharingweb-service-in-the-devazureuser2-dev-space"></a>De bijgewerkte bikesharingweb-service bouwen en uitvoeren in de ontwikkel ruimte *dev/azureuser2*

Ga naar de *BikeSharingWeb/* map en voer de opdracht `azds up` uit.

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

Met deze opdracht wordt de *bikesharingweb* -service in de ontwikkel ruimte *dev/azureuser2* gemaakt en uitgevoerd. Deze service wordt uitgevoerd naast de *bikesharingweb* -service die wordt uitgevoerd in *dev* en wordt alleen gebruikt voor aanvragen met het URL-voor voegsel *azureuser2. s* . Zie [hoe Azure dev Spaces werkt en is geconfigureerd](how-dev-spaces-works.md)voor meer informatie over de werking van route ring tussen bovenliggende en onderliggende ontwikkel ruimten.

Ga naar de *bikesharingweb* -service voor de dev */azureuser2 dev-* ruimte door de open bare URL te openen die wordt weer gegeven in de uitvoer van de `azds up` opdracht. Selecteer *Aurelia Briggs (klant)* als de gebruiker. Controleer of de bijgewerkte tekst in de rechter bovenhoek wordt weer geven. Mogelijk moet u de pagina vernieuwen of de cache van de browser wissen als u deze wijziging niet meteen ziet.

![De voorbeeld toepassing is bijgewerkt door Azure dev Spaces fiets delen](media/quickstart-team-development/bikeshare-update.png)

> [!NOTE]
> Wanneer u naar uw service navigeert tijdens het uitvoeren van `azds up`, worden de traceringen van de HTTP-aanvraag ook weer gegeven in de uitvoer van de opdracht `azds up`. Deze traceringen kunnen u helpen bij het oplossen van problemen met uw service en fout opsporing. U kunt deze traceringen uitschakelen met `--disable-http-traces` wanneer u `azds up` uitvoert.

## <a name="verify-other-dev-spaces-are-unchanged"></a>Controleren of andere ontwikkel ruimten ongewijzigd zijn

Als de `azds up` opdracht nog steeds wordt uitgevoerd, drukt u op *CTRL + c*.

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

Ga in uw browser naar de *dev* -versie van *Bikesharingweb* , kies *Aurelia Briggs (klant)* als de gebruiker en controleer of de oorspronkelijke tekst in de rechter bovenhoek wordt weer geven. Herhaal deze stappen met de URL van *dev/azureuser1* . U ziet dat de wijzigingen alleen worden toegepast op de *dev/azureuser2-* versie van *bikesharingweb*. Door deze isolatie van wijzigingen in *dev/azureuser2* kunnen *azureuser2* wijzigingen aanbrengen zonder dat dit van invloed is op *azureuser1*.

Als u wilt dat deze wijzigingen worden weer gegeven in *dev* en *dev/azureuser1*, moet u de bestaande werk stroom van uw team of CI/cd-pijp lijn volgen. Deze werk stroom kan bijvoorbeeld betrekking hebben op het door voeren van uw wijziging in uw versie beheersysteem en het implementeren van de update met een CI/CD-pijp lijn of hulp programma zoals helm.

## <a name="clean-up-your-azure-resources"></a>Uw Azure-resources opschonen

```cmd
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Volgende stappen

Ontdek hoe Azure Dev Spaces u kan helpen om complexere apps te ontwikkelen binnen meerdere containers en hoe u ontwikkeling in samenwerkingsverband vereenvoudigt door in verschillende ruimten met verschillende versies of vertakkingen van uw code te werken.

> [!div class="nextstepaction"]
> [Werken met meerdere containers en teamontwikkeling](multi-service-nodejs.md)

[helm-installed]: https://helm.sh/docs/using_helm/#installing-helm
[supported-regions]: about.md#supported-regions-and-configurations
