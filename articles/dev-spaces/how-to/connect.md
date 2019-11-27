---
title: Uw ontwikkelmachine verbinden met een AKS-cluster
services: azure-dev-spaces
ms.date: 11/04/2019
ms.topic: conceptual
description: Meer informatie over hoe u uw ontwikkel machine verbindt met een AKS-cluster met Azure dev Spaces
keywords: Azure dev Spaces, dev Spaces, docker, Kubernetes, azure, AKS, Azure Kubernetes service, containers
ms.openlocfilehash: c26d159d6f883e1c368b09a82bc53c621c70e281
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2019
ms.locfileid: "74482233"
---
# <a name="connect-your-development-machine-to-an-aks-cluster-preview"></a>Uw ontwikkelmachine verbinden met een AKS-cluster (preview)

Met Azure dev Spaces kunt u code uitvoeren en fouten opsporen met of zonder een container op uw ontwikkel computer, terwijl u verbonden bent met uw Kubernetes-cluster met de rest van uw toepassing of services. Wanneer u uw ontwikkel machine verbindt met uw cluster, kunt u snel uw toepassing ontwikkelen en end-to-end tests uitvoeren zonder dat u een docker-of Kubernetes hoeft te maken. U kunt ook verbinding maken met uw AKS-cluster zonder dat dit van invloed is op andere workloads of gebruikers die gebruikmaken van hetzelfde cluster.

Azure dev Spaces omleidt verkeer tussen uw verbonden AKS-cluster en uw ontwikkel machine. Met deze omleidings functie voor verkeer kan code op uw ontwikkel computer en services die worden uitgevoerd in uw AKS-cluster communiceren alsof ze zich in hetzelfde AKS-cluster bevinden. Omdat uw code wordt uitgevoerd op uw ontwikkel computer, hebt u ook flexibiliteit in de ontwikkel hulpprogramma's die u gebruikt om deze code uit te voeren en fouten op te lossen. Azure dev Spaces biedt een manier om omgevings variabelen en gekoppelde bestanden te repliceren die beschikbaar zijn voor een Peul in uw AKS-cluster in uw ontwikkel machine.

In deze handleiding leert u het volgende:

* Stel Azure-ontwikkel ruimten in op een beheerd Kubernetes-cluster in Azure.
* Implementeer een grote toepassing met meerdere micro Services naar een dev-ruimte.
* Gebruik Azure dev Spaces om verkeer te omleiden tussen uw AKS-cluster en code die wordt uitgevoerd op uw ontwikkel computer.

> [!IMPORTANT]
> Deze functie is momenteel beschikbaar als preview-product. Previews worden voor u beschikbaar gesteld op voorwaarde dat u akkoord gaat met de [aanvullende gebruiksvoorwaarden](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Sommige aspecten van deze functie worden mogelijk nog gewijzigd voordat de functie algemeen beschikbaar wordt.

## <a name="before-you-begin"></a>Voordat u begint

In deze hand leiding wordt gebruikgemaakt van de [voorbeeld toepassing voor delen van Azure dev Spaces Bike](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp) om te demonstreren hoe u uw ontwikkel machine verbindt met een AKS-cluster Volg de instructies in het [Leesmij-bestand van de voorbeeld toepassing delen van Azure dev Spaces Bike](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/README.md) om de voorbeeld toepassing uit te voeren. Als u uw eigen toepassing hebt op een AKS-cluster, kunt u de onderstaande stappen ook volgen en de namen van uw eigen services en peulen gebruiken.

### <a name="limitations"></a>Beperkingen

* UDP wordt op dit moment niet ondersteund.

### <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u geen abonnement op Azure hebt, kunt u een [gratis account](https://azure.microsoft.com/free) maken.
* [Azure CLI geïnstalleerd][azure-cli].
* [Visual Studio code][vs-code] met de [Azure dev Spaces][azds-vs-code] -extensie geïnstalleerd en uitgevoerd op MacOS of Windows 10.
* De [Azure dev Spaces-app voor delen](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp) , of uw eigen toepassing die wordt uitgevoerd op een AKS-cluster.

## <a name="connect-your-development-machine"></a>Verbinding maken met uw ontwikkel computer

Open *dev-Spaces/samples/BikeSharingApps/Bikes* in Visual Studio code en gebruik de Azure dev Space-extensie om uw ontwikkel machine te verbinden met uw AKS-cluster.

Als u de Azure dev Spaces-extensie wilt gebruiken, opent u het opdracht palet in Visual Studio code door te klikken op *weer gave* en *opdracht palet*. Begin met het typen van `Azure Dev Spaces: Redirect` en klik op `Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]`, `Azure Dev Spaces: Redirect an existing Kubernetes pod to my machine [Preview]`of `Azure Dev Spaces: Redirect a new Kubernetes pod to my machine [Preview]`.

![Opdrachten](../media/how-to-connect/connect-commands.png)

### <a name="select-a-redirection-option"></a>Selecteer een omleidings optie

Als u `Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]`uitvoert, wordt u gevraagd een bestaande Kubernetes-service te kiezen:

![Service kiezen](../media/how-to-connect/connect-choose-service.png)

Met deze optie wordt alle verkeer in het AKS-cluster voor deze service omgeleid naar de versie van uw toepassing die wordt uitgevoerd op uw ontwikkel computer. Als voor deze service meerdere peulen worden uitgevoerd in het AKS-cluster, wordt al het verkeer voor deze service alleen doorgestuurd naar uw ontwikkel machine. Azure dev Spaces stuurt ook al het uitgaande verkeer van de toepassing terug naar uw AKS-cluster.

Als u `Azure Dev Spaces: Redirect an existing Kubernetes pod to my machine [Preview]`uitvoert, wordt u gevraagd een specifieke pod te kiezen:

![Pod kiezen](../media/how-to-connect/connect-choose-pod.png)

Met deze optie maakt u verbinding met een specifieke pod. Deze optie is handig voor interactie met een Peul dat geen verkeer verzendt of ontvangt en die niet is beëindigd. Als het Pod verkeer verzendt en ontvangt, gedraagt deze optie zich op een vergelijk bare manier als `Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]` en wordt al het verkeer in het AKS-cluster omgeleid voor alle peulen die betrekking hebben op de service van de geselecteerde pod.

Als u `Azure Dev Spaces: Redirect a new Kubernetes pod to my machine [Preview]`uitvoert, wordt u niet gevraagd om een bestaande Pod of service te selecteren. Met deze optie wordt al het uitgaande verkeer omgeleid van de toepassing die wordt uitgevoerd op uw ontwikkel computer naar het AKS-cluster.

Voor dit voor beeld kiest u `Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]` en selecteert u de service *Bikes* .

### <a name="select-a-connection-mode"></a>Een verbindings modus selecteren

Nadat u de omleidings optie hebt geselecteerd, wordt u gevraagd om de verbindings modus *vervangen* of *kloon* te kiezen.

![Vervangen of klonen](../media/how-to-connect/connect-replace-clone.png)

Met de optie *vervangen* vervangt u de huidige Pod of service in het AKS-cluster, waarna alle verkeer voor die service wordt omgeleid naar uw ontwikkel machine. Deze optie kan storend zijn op andere services in uw AKS-cluster die communiceren met de service die u omleidt, werkt mogelijk niet totdat u de toepassing op uw ontwikkel machine start. Met de *kloon* optie kunt u een bestaande onderliggende ontwikkel ruimte kiezen of een nieuwe onderliggende ontwikkel ruimte maken voor het omleiden van verkeer voor een pod of service naar uw ontwikkel machine. Met deze optie kunt u werken met isolatie en geen andere services verstoren omdat alleen verkeer naar die onderliggende ontwikkel ruimte wordt omgeleid naar uw ontwikkel computer. Voor de *kloon* optie moet uw AKS-cluster Azure dev Spaces hebben ingeschakeld.

Kies voor dit voor beeld *vervangen*.

> [!NOTE]
> Als de pod van uw bestaande service meerdere containers heeft, wordt u ook gevraagd om de container van de toepassing te kiezen.

### <a name="select-a-port-for-your-application"></a>Selecteer een poort voor uw toepassing

Nadat u de verbindings modus hebt geselecteerd, wordt u gevraagd om de TCP-poort van uw lokale toepassing in te voeren. Als uw toepassing meerdere poorten opent, moet u deze scheiden met een komma, bijvoorbeeld *80, 81*. Als uw toepassing geen netwerk aanvragen accepteert, voert u *0*in. Voer voor dit voor beeld *3000*in.

![Poort kiezen voor verbinding](../media/how-to-connect/connect-choose-port.png)

### <a name="confirm-you-are-connected"></a>Bevestig dat u bent verbonden

Nadat u de TCP-poort van uw toepassing hebt geselecteerd, wordt door Azure dev Spaces een verbinding met het AKS-cluster tot stand gebracht. Azure dev Spaces injecteert een agent in uw AKS-cluster om verkeer tussen het AKS-cluster en uw ontwikkel machine om te leiden. Het kan enkele minuten duren voordat deze verbinding tot stand is gebracht. Azure dev Spaces hebben ook beheerders toegang nodig om het *hosts* -bestand in de ontwikkel computer te wijzigen.

> [!IMPORTANT]
> Wanneer Azure dev Spaces een verbinding met uw AKS-cluster tot stand brengt, werken de andere services in uw AKS-cluster mogelijk niet goed totdat u de service in de ontwikkel machine start als u de verbindings modus *vervangen* kiest. U kunt in plaats daarvan de verbindings modus *klonen* kiezen om een onderliggende ontwikkel ruimte voor uw omleiding te maken en eventuele onderbrekingen van de bovenliggende ruimte te voor komen. Als uw service een afhankelijkheid heeft die niet beschikbaar is in uw ontwikkel machine, moet u mogelijk uw toepassing wijzigen of [aanvullende configuratie](#additional-configuration) opgeven

Met Azure dev Spaces wordt een Terminal venster geopend met de titel *AZDS Connect-Bikes* nadat het een verbinding met uw AKS-cluster tot stand heeft gebracht. Dit Terminal venster bevat alle omgevings variabelen en DNS-vermeldingen die zijn geconfigureerd op basis van uw AKS-cluster. Alle code die u uitvoert in dit Terminal venster of het gebruik van de Visual Studio code debugger is verbonden met het AKS-cluster.

![Terminal](../media/how-to-connect/connect-terminal.png)

Daarnaast maakt Azure dev Spaces een venster met de naam *dev Spaces verbinden* met alle uitvoer.

![Uitvoer](../media/how-to-connect/connect-output.png)

Azure dev Spaces heeft ook een status balk-item met de verbindings status.

![Status](../media/how-to-connect/connect-status.png)

Controleer of op de status balk *dev Spaces worden weer gegeven: verbonden met dev/Bikes op lokale poort 3000*.

### <a name="configure-your-application-on-your-development-machine"></a>Uw toepassing configureren op uw ontwikkel computer

Open het Terminal venster *AZDS Connect-Bikes* en voer `npm install`uit:

```console
$ npm install

> fsevents@1.2.9 install ./dev-spaces/samples/BikeSharingApp/Bikes/node_modules/fsevents
> node install
...
```

Klik op *fout opsporing* en vervolgens op *configuratie*. Als u wordt gevraagd om een omgeving te selecteren, kiest u *node. js*. Hiermee maakt u een `.vscode/launch.json` bestand. Vervang de inhoud van het bestand door het volgende:

```json
{
    "configurations": [
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

Uw toepassing wordt gestart en Azure dev Spaces omleidt verkeer tussen uw AKS-cluster en uw ontwikkel machine. In de *console fout opsporing*ziet u berichten die er ongeveer als volgt uitzien:

```console
/usr/local/bin/npm run-script debug 
...
Debugger attached.
Collection: bikes
MongoDB connection string: mongodb://databases-mongo
Connected to MongoDB
Listening on port 3000
```

Ga naar de *bikesharingweb* -service door te klikken op de status balk van Azure dev Spaces en de open bare URL van uw toepassing te kiezen. U kunt ook de open bare URL vinden in de `azds list-uris`-opdracht die u eerder hebt uitgevoerd. Als u geen Azure dev-ruimten in uw cluster gebruikt, gebruikt u het IP-adres of de URL voor de toepassing voor de naam ruimte die u gebruikt. In het bovenstaande voor beeld is de open bare URL voor de *bikesharingweb* -service `http://dev.bikesharingweb.fedcab0987.eus.azds.io/`. Selecteer *Aurelia Briggs (klant)* als de gebruiker en selecteer vervolgens een te huur fiets.

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

Klik op *fout* opsporing en vervolgens op *fout opsporing stoppen* om het fout opsporingsprogramma te stoppen. Klik op de status balk van Azure dev Spaces om de verbinding met het AKS-cluster te verbreken.

## <a name="additional-configuration"></a>Aanvullende configuratie

Azure dev Spaces kunnen routerings verkeer verwerken en omgevings variabelen repliceren zonder extra configuratie. Als u bestanden wilt downloaden die zijn gekoppeld aan de container in uw AKS-cluster, zoals een ConfigMap-bestand, kunt u een `azds-local.env` maken om deze bestanden te downloaden naar uw ontwikkel computer.

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

## <a name="using-logging-and-diagnostics"></a>Logboek registratie en diagnostische gegevens gebruiken

De logboek registratie van de uitvoer wordt geschreven naar het venster *dev Spaces Connect* nadat u uw ontwikkel machine hebt verbonden met uw AKS-cluster.

![Uitvoer](../media/how-to-connect/connect-output.png)

Klik op de status balk van Azure dev Spaces en kies *Diagnostische gegevens weer geven*. Met deze opdracht worden de huidige omgevings variabelen en DNS-gegevens in de logboek registratie uitgevoerd.

![Uitvoer met diagnostische gegevens](../media/how-to-connect/connect-output-diagnostics.png)

Daarnaast kunt u de diagnostische logboeken vinden in `Azure Dev Spaces` Directory in de [map *temp* van uw ontwikkel machine][azds-tmp-dir].

## <a name="next-steps"></a>Volgende stappen

Leer hoe u Azure dev Spaces en GitHub-acties kunt gebruiken om wijzigingen van een pull-aanvraag rechtstreeks in AKS te testen voordat de pull-aanvraag wordt samengevoegd in de hoofd vertakking van de opslag plaats.

> [!div class="nextstepaction"]
> [GitHub-acties & Azure Kubernetes-service][gh-actions]

[azds-tmp-dir]: ../troubleshooting.md#before-you-begin
[azds-vs-code]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[azure-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[bike-sharing-github]: https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp
[gh-actions]: github-actions.md
[supported-regions]: ../about.md#supported-regions-and-configurations
[team-quickstart]: ../quickstart-team-development.md
[vs-code]: https://code.visualstudio.com/download