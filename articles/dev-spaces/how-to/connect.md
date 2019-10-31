---
title: Azure dev Spaces Connect
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 10/24/2019
ms.topic: conceptual
description: Meer informatie over het gebruik van Azure dev Spaces Connect
keywords: Azure dev Spaces, dev Spaces, docker, Kubernetes, azure, AKS, Azure Kubernetes service, containers, Connect
ms.openlocfilehash: 2e1984b838e961239e0533179c34eccb118abd8e
ms.sourcegitcommit: f7f70c9bd6c2253860e346245d6e2d8a85e8a91b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73065871"
---
# <a name="azure-dev-spaces-connect-preview"></a>Azure dev Spaces Connect (preview-versie)

Met Azure dev Spaces Connect kunt u code uitvoeren en fouten opsporen met of zonder een container op uw ontwikkel computer, terwijl u verbonden bent met uw Kubernetes-cluster met de rest van uw toepassing of services. Wanneer u uw ontwikkel machine verbindt met uw cluster, kunt u snel uw toepassing ontwikkelen en end-to-end tests uitvoeren zonder dat u een docker-of Kubernetes hoeft te maken. U kunt ook verbinding maken met uw AKS-cluster zonder dat dit van invloed is op andere workloads of gebruikers die gebruikmaken van hetzelfde cluster.

Met Azure dev Spaces maakt u verkeer tussen uw verbonden AKS-cluster en uw ontwikkel computer. Met deze omleidings functie voor verkeer kan code op uw ontwikkel computer en services die worden uitgevoerd in uw AKS-cluster communiceren alsof ze zich in hetzelfde AKS-cluster bevinden. Omdat uw code wordt uitgevoerd op uw ontwikkel computer, hebt u ook flexibiliteit in de ontwikkel hulpprogramma's die u gebruikt om deze code uit te voeren en fouten op te lossen. Azure dev Spaces Connect biedt ook een manier om omgevings variabelen en gekoppelde bestanden te repliceren die beschikbaar zijn voor een Peul in uw AKS-cluster in uw ontwikkel machine.

In deze handleiding leert u het volgende:

- Stel Azure-ontwikkel ruimten in op een beheerd Kubernetes-cluster in Azure.
- Implementeer een grote toepassing met meerdere micro Services naar een dev-ruimte.
- Gebruik Azure dev Spaces om verkeer te omleiden tussen uw AKS-cluster en code die wordt uitgevoerd op uw ontwikkel computer.

> [!IMPORTANT]
> Deze functie is momenteel beschikbaar als preview-product. Previews worden voor u beschikbaar gesteld op voorwaarde dat u akkoord gaat met de [aanvullende gebruiksvoorwaarden](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Sommige aspecten van deze functie worden mogelijk nog gewijzigd voordat de functie algemeen beschikbaar wordt.

## <a name="before-you-begin"></a>Voordat u begint

In deze hand leiding wordt gebruikgemaakt van de [Azure dev Spaces-voorbeeld toepassing voor delen](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp) voor het demonstreren van Azure dev Spaces Connect. Als u uw eigen toepassing in AKS-cluster hebt die u in plaats daarvan wilt gebruiken, kunt u [hier](#use-azure-dev-spaces-connect)beginnen.

### <a name="limitations"></a>Beperkingen

* UDP wordt niet ondersteund met Azure dev Spaces Connect op dit moment.

### <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u geen Azure-abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free) maken.
* [Azure CLI geïnstalleerd][azure-cli].
* [Helm 2,13 of hoger is geïnstalleerd][helm-installed].
* [Visual Studio code][vs-code] met de [Azure dev Spaces][azds-vs-code] -extensie geïnstalleerd en uitgevoerd op MacOS of Windows 10.

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

U kunt Azure dev Spaces gebruiken voor ontwikkeling nadat een toepassing wordt uitgevoerd in een cluster, ongeacht het hulp programma dat wordt gebruikt om het te implementeren.

### <a name="use-helm-to-install-the-sample-application"></a>Gebruik helm om de voorbeeld toepassing te installeren

Gebruik de opdrachten `helm init` en `helm install` om de voorbeeld toepassing in uw cluster in te stellen en te installeren.

```cmd
cd charts/
helm init --wait
helm install -n bikesharing . --dep-up --namespace dev --atomic 
```

> [!Note]
> **Als u een cluster met RBAC-functionaliteit gebruikt**, moet u [een service account configureren voor Tiller](https://helm.sh/docs/using_helm/#role-based-access-control). Als dat niet het geval is, mislukken `helm`-opdrachten.

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

### <a name="navigate-to-your-sample-application"></a>Navigeer naar uw voorbeeld toepassing

Nadat de voorbeeld toepassing op uw cluster is geïnstalleerd en omdat er ontwikkel ruimten zijn ingeschakeld op uw cluster, gebruikt u de opdracht `azds list-uris` om de Url's voor de voorbeeld toepassing weer te geven in *dev* die momenteel is geselecteerd.

```cmd
$ azds list-uris
Uri                                                 Status
--------------------------------------------------  ---------
http://dev.bikesharingweb.fedcab0987.eus.azds.io/  Available
http://dev.gateway.fedcab0987.eus.azds.io/         Available
```

Ga naar de *bikesharingweb* -service door de open bare URL te openen via de `azds list-uris` opdracht. In het bovenstaande voor beeld is de open bare URL voor de *bikesharingweb* -service `http://dev.bikesharingweb.fedcab0987.eus.azds.io/`. Selecteer *Aurelia Briggs (klant)* als de gebruiker en selecteer vervolgens een te huur fiets. Controleer of er een tijdelijke afbeelding voor de fiets wordt weer geven.

## <a name="use-azure-dev-spaces-connect"></a>Gebruik Azure dev Spaces Connect

Open *dev-Spaces/samples/BikeSharingApps/Bikes* in Visual Studio code en gebruik de Azure dev Space Connect-extensie om uw ontwikkel machine te verbinden met uw AKS-cluster.

Als u de Azure dev Spaces Connect-extensie wilt gebruiken, opent u het opdracht palet in Visual Studio code door te klikken op *weer gave* en *opdracht palet*. Begin met het typen van `Azure Dev Spaces: Redirect` en klik op `Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]`, `Azure Dev Spaces: Redirect an existing Kubernetes pod to my machine [Preview]`of `Azure Dev Spaces: Redirect a new Kubernetes pod to my machine [Preview]`.

![Verbindings opdrachten](../media/how-to-connect/connect-commands.png)

### <a name="select-a-redirection-option"></a>Selecteer een omleidings optie

Als u `Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]`uitvoert, wordt u gevraagd een bestaande Kubernetes-service te kiezen:

![Verbinding kiezen service](../media/how-to-connect/connect-choose-service.png)

Met deze optie wordt alle verkeer in het AKS-cluster voor deze service omgeleid naar de versie van uw toepassing die wordt uitgevoerd op uw ontwikkel computer. Als voor deze service meerdere peulen worden uitgevoerd in het AKS-cluster, wordt al het verkeer voor deze service alleen doorgestuurd naar uw ontwikkel machine. Azure dev Spaces Connect stuurt ook al het uitgaande verkeer van de toepassing terug naar uw AKS-cluster.

Als u `Azure Dev Spaces: Redirect an existing Kubernetes pod to my machine [Preview]`uitvoert, wordt u gevraagd een specifieke pod te kiezen:

![Verbinding maken Kies pod](../media/how-to-connect/connect-choose-pod.png)

Met deze optie maakt u verbinding met een specifieke pod. Deze optie is handig voor interactie met een Peul dat geen verkeer verzendt of ontvangt en die niet is beëindigd. Als het Pod verkeer verzendt en ontvangt, gedraagt deze optie zich op een vergelijk bare manier als `Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]` en wordt al het verkeer in het AKS-cluster omgeleid voor alle peulen die betrekking hebben op de service van de geselecteerde pod.

Als u `Azure Dev Spaces: Redirect a new Kubernetes pod to my machine [Preview]`uitvoert, wordt u niet gevraagd om een bestaande Pod of service te selecteren. Met deze optie wordt al het uitgaande verkeer omgeleid van de toepassing die wordt uitgevoerd op uw ontwikkel computer naar het AKS-cluster.

Voor dit voor beeld kiest u `Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]` en selecteert u de service *Bikes* .

### <a name="select-a-connection-mode"></a>Een verbindings modus selecteren

Nadat u de omleidings optie hebt geselecteerd, wordt u gevraagd om de verbindings modus *vervangen* of *kloon* te kiezen.

![Vervangen of kloon verbinden](../media/how-to-connect/connect-replace-clone.png)

Met de optie *vervangen* vervangt u de huidige Pod of service in het AKS-cluster, waarna alle verkeer voor die service wordt omgeleid naar uw ontwikkel machine. Deze optie kan storend zijn op andere services in uw AKS-cluster die communiceren met de service die u omleidt, werkt mogelijk niet totdat u de toepassing op uw ontwikkel machine start. Met de *kloon* optie kunt u een bestaande onderliggende ontwikkel ruimte kiezen of een nieuwe onderliggende ontwikkel ruimte maken voor het omleiden van verkeer voor een pod of service naar uw ontwikkel machine. Met deze optie kunt u werken met isolatie en geen andere services verstoren omdat alleen verkeer naar die onderliggende ontwikkel ruimte wordt omgeleid naar uw ontwikkel computer. Voor de *kloon* optie moet uw AKS-cluster Azure dev Spaces hebben ingeschakeld.

Kies voor dit voor beeld *vervangen*.

> [!NOTE]
> Als de pod van uw bestaande service meerdere containers heeft, wordt u ook gevraagd om de container van de toepassing te kiezen.

### <a name="select-a-port-for-your-application"></a>Selecteer een poort voor uw toepassing

Nadat u de verbindings modus hebt geselecteerd, wordt u gevraagd om de TCP-poort van uw lokale toepassing in te voeren. Als uw toepassing meerdere poorten opent, moet u deze scheiden met een komma, bijvoorbeeld *80, 81*. Als uw toepassing geen netwerk aanvragen accepteert, voert u *0*in. Voer voor dit voor beeld *8080*in.

![Poort kiezen voor verbinding](../media/how-to-connect/connect-choose-port.png)

### <a name="confirm-you-are-connected"></a>Bevestig dat u bent verbonden

Nadat u de TCP-poort van uw toepassing hebt geselecteerd, wordt met Azure dev Spaces Connect een verbinding met het AKS-cluster tot stand gebracht. Met Azure dev Spaces Connect wordt een agent in uw AKS-cluster geïnjecteerd om verkeer tussen het AKS-cluster en uw ontwikkel machine om te leiden. Het kan enkele minuten duren voordat deze verbinding tot stand is gebracht. Azure dev Spaces Connect heeft ook beheerders toegang nodig om het *hosts* -bestand in de ontwikkel machine te wijzigen.

> [!IMPORTANT]
> Als Azure dev Spaces Connect een verbinding met uw AKS-cluster tot stand brengt, werkt de andere services in uw AKS-cluster mogelijk niet goed totdat u de service in de ontwikkel machine start. Als uw service een afhankelijkheid heeft die niet beschikbaar is in uw ontwikkel machine, moet u mogelijk uw toepassing wijzigen of [aanvullende configuratie](#additional-configuration) opgeven

Met Azure dev Spaces Connect wordt een Terminal venster geopend met de titel *AZDS Connect-Bikes* nadat het een verbinding met uw AKS-cluster tot stand heeft gebracht. Dit Terminal venster bevat alle omgevings variabelen en DNS-vermeldingen die zijn geconfigureerd op basis van uw AKS-cluster. Alle code die u uitvoert in dit Terminal venster of het gebruik van de Visual Studio code debugger is verbonden met het AKS-cluster.

![Verbinding maken met Terminal](../media/how-to-connect/connect-terminal.png)

Daarnaast maakt Azure dev Spaces Connect een venster met de naam *dev Spaces verbinden* met alle uitvoer.

![Verbinding maken met uitvoer](../media/how-to-connect/connect-output.png)

Azure dev Spaces Connect heeft ook een status balk-item met de verbindings status.

![Verbindings status](../media/how-to-connect/connect-status.png)

Controleer of op de status balk *dev Spaces worden weer gegeven: verbonden met dev/Bikes op lokale poort 8080*.

### <a name="configure-your-application-on-your-development-machine"></a>Uw toepassing configureren op uw ontwikkel computer

Open het Terminal venster *AZDS Connect-Bikes* en voer `npm install`uit:

```console
$ npm install

> fsevents@1.2.9 install ./dev-spaces/samples/BikeSharingApp/Bikes/node_modules/fsevents
> node install
...
```

Open [server. js](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/Bikes/server.js#L352) en op regel 352 update *poort* tot *8080*:

```javascript
var port = 8080;
var server = null;
```

Klik op *fout opsporing* en vervolgens op *configuratie*. Hiermee maakt u een `.vscode/launch.json` bestand. Vervang de inhoud van het bestand door het volgende:

```json
{
    "configurations": [
        ...
        {
            "type": "node",
            "request": "launch",
            "name": "Launch via NPM",
            "runtimeExecutable": "npm",
            "runtimeArgs": [
                "run-script",
                "debug"
            ],
            "port": 9229
        }
    ]
}
```

Open [package. json](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/Bikes/package.json) en voeg een debug-script toe:

```json
  "devDependencies": {
    "nodemon": "^1.18.10"
  },
  "scripts": {
    "debug": "node --nolazy --inspect-brk=9229 server.js"
  }
```

### <a name="start-your-application-on-your-development-machine"></a>Start uw toepassing op uw ontwikkel computer

Klik op het pictogram voor *fout opsporing* aan de linkerkant en klik op de knop Start naast *starten via NPM* bovenaan.

![Starten via NPM](../media/how-to-connect/launch-npm.png)

Uw toepassing wordt gestart en Azure dev Spaces Connect stuurt verkeer tussen uw AKS-cluster en uw ontwikkel computer. In de *console fout opsporing*ziet u berichten die er ongeveer als volgt uitzien:

```console
/usr/local/bin/npm run-script debug 
...
Debugger attached.
Collection: bikes
MongoDB connection string: mongodb://databases-mongo
Connected to MongoDB
Listening on port 8080
```

Ga naar de *bikesharingweb* -service door te klikken op de status balk van Azure dev Spaces Connect en de open bare URL van uw toepassing te kiezen. U kunt ook de open bare URL vinden in de `azds list-uris`-opdracht die u eerder hebt uitgevoerd. Als u geen Azure dev-ruimten in uw cluster gebruikt, gebruikt u het IP-adres of de URL voor de toepassing voor de naam ruimte die u gebruikt. In het bovenstaande voor beeld is de open bare URL voor de *bikesharingweb* -service `http://dev.bikesharingweb.fedcab0987.eus.azds.io/`. Selecteer *Aurelia Briggs (klant)* als de gebruiker en selecteer vervolgens een te huur fiets.

### <a name="set-a-break-point"></a>Een breek punt instellen

Open [server. js](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/Bikes/server.js#L233) en klik ergens op regel 233 om de cursor daar te plaatsen. Stel een onderbrekings punt in door op *F9* te klikken of op *fout opsporing* en vervolgens *onderbrekings punt*.

Ga naar de *bikesharingweb* -service door de open bare URL te openen. Selecteer *Aurelia Briggs (klant)* als de gebruiker en selecteer vervolgens een te huur fiets. U ziet dat de afbeelding voor de fiets niet wordt geladen. Ga terug naar Visual Studio code en kijk regel 233 is gemarkeerd. Het onderbrekings punt dat u hebt ingesteld, heeft de service onderbroken op regel 233. Als u de service wilt hervatten, druk op *F5* of klik op *debug* en vervolgens op *door gaan*. Ga terug naar uw browser en controleer of er een tijdelijke aanduiding voor de fiets wordt weer gegeven.

Verwijder het onderbrekings punt door de cursor op regel 233 in `server.js` te plaatsen en op *F9 te drukken*.

### <a name="update-your-application"></a>Uw toepassing bijwerken

Bewerk `server.js` om de regels 232 en 233 te verwijderen:

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

Sla de wijzigingen op en klik op *fouten opsporen* en vervolgens op fout *opsporing opnieuw starten*. Vernieuw uw browser en controleer of u geen tijdelijke aanduiding voor de fiets meer ziet.

Klik op *fout* opsporing en vervolgens op *fout opsporing stoppen* om het fout opsporingsprogramma te stoppen. Klik op de status balk van Azure dev Spaces Connect om de verbinding met het AKS-cluster te verbreken.

## <a name="additional-configuration"></a>Aanvullende configuratie

Azure dev Spaces Connect kunnen routerings verkeer verwerken en omgevings variabelen repliceren zonder extra configuratie. Als u bestanden wilt downloaden die zijn gekoppeld aan de container in uw AKS-cluster, zoals een ConfigMap-bestand, kunt u een `azds-local.env` maken om deze bestanden te downloaden naar uw ontwikkel computer.

Hier volgt een voor beeld `azds-local.env`:

```
# This downloads the "whitelist" volume from the container,
# saves it to a temporary directory on your development machine,
# and sets the full path to an environment variable called WHITELIST_PATH.

WHITELIST_PATH=${volumes.whitelist}/whitelist

# This downloads a file from the container's 'default-token-<any>' mount directory 
# to /var/run/secrets/kubernetes.io/serviceaccount on your development machine.

KUBERNETES_IN_CLUSTER_CONFIG_OVERWRITE=${volumes.default-token-*|/var/run/secrets/kubernetes.io/serviceaccount}


# This makes the myapp1 service available to your development machine
# regardless of the AKS cluster you are connected to and
# sets the local IP to an environment variable called MYAPP1_SERVICE_HOST.

MYAPP1_SERVICE_HOST=${services.myapp1}

# If the myapp1 service is made available in this way, 
# you can also access it using "myapp1" and "myapp1.svc.cluster.local"
# in addition to the IP in the MYAPP1_SERVICE_HOST environment variable.
```

## <a name="next-steps"></a>Volgende stappen

Leer hoe u Azure dev Spaces en GitHub-acties kunt gebruiken om wijzigingen van een pull-aanvraag rechtstreeks in AKS te testen voordat de pull-aanvraag wordt samengevoegd in de hoofd vertakking van de opslag plaats.

> [!div class="nextstepaction"]
> [GitHub-acties & Azure Kubernetes-service][gh-actions]

[azds-vs-code]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[azure-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[bike-sharing-github]: https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp
[gh-actions]: github-actions.md
[helm-installed]: https://helm.sh/docs/using_helm/#installing-helm
[supported-regions]: ../about.md#supported-regions-and-configurations
[team-quickstart]: ../quickstart-team-development.md
[vs-code]: https://code.visualstudio.com/download