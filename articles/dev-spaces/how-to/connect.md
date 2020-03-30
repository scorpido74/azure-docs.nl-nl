---
title: Uw ontwikkelcomputer verbinden met een AKS-cluster (voorbeeld)
services: azure-dev-spaces
ms.date: 11/04/2019
ms.topic: conceptual
description: Meer informatie over het verbinden van uw ontwikkelcomputer met een AKS-cluster met Azure Dev Spaces
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers
ms.openlocfilehash: 772f221a8047a71902f36fa98ded6c24b5e02d27
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235001"
---
# <a name="connect-your-development-computer-to-an-aks-cluster-preview"></a>Uw ontwikkelcomputer verbinden met een AKS-cluster (voorbeeld)

Met Azure Dev Spaces u code uitvoeren en debuggen met of zonder container op uw ontwikkelcomputer, terwijl u nog steeds verbonden bent met uw Kubernetes-cluster met de rest van uw toepassing of services. Als u uw ontwikkelcomputer met uw cluster verbindt, u uw toepassing snel ontwikkelen en end-to-end-tests uitvoeren zonder dat u een Docker- of Kubernetes-configuratie hoeft te maken. U ook verbinding maken met uw AKS-cluster zonder dat dit gevolgen heeft voor andere workloads of gebruikers die mogelijk hetzelfde cluster gebruiken.

Azure Dev Spaces leidt het verkeer om tussen uw verbonden AKS-cluster en uw ontwikkelcomputer. Met deze verkeersomleiding kunnen code op uw ontwikkelingscomputer en services die in uw AKS-cluster worden uitgevoerd, communiceren alsof ze zich in hetzelfde AKS-cluster bevinden. Aangezien uw code wordt uitgevoerd op uw ontwikkelingscomputer, hebt u ook flexibiliteit in de ontwikkeltools die u gebruikt om die code uit te voeren en te debuggen. Azure Dev Spaces biedt ook een manier om omgevingsvariabelen en gemonteerde bestanden te repliceren die beschikbaar zijn voor pods in uw AKS-cluster op uw ontwikkelingscomputer.

In deze handleiding leert u het volgende:

* Azure Dev Spaces instellen op een beheerd Kubernetes-cluster in Azure.
* Implementeer een grote toepassing met meerdere microservices in een dev-ruimte.
* Gebruik Azure Dev Spaces om verkeer om te leiden tussen uw AKS-cluster en code die op uw ontwikkelcomputer wordt uitgevoerd.

> [!IMPORTANT]
> Deze functie is momenteel beschikbaar als preview-product. Previews worden voor u beschikbaar gesteld op voorwaarde dat u akkoord gaat met de [aanvullende gebruiksvoorwaarden](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Sommige aspecten van deze functionaliteit kunnen wijzigen voordat deze functionaliteit algemeen beschikbaar wordt.

## <a name="before-you-begin"></a>Voordat u begint

Deze handleiding maakt gebruik van de [voorbeeldtoepassing Azure Dev Spaces Bike Sharing](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp) om aan te tonen dat uw ontwikkelcomputer is verbonden met een AKS-cluster. Volg de instructies in de [azure dev Spaces Bike Sharing voorbeeldtoepassing README](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/README.md) om de voorbeeldtoepassing uit te voeren. Als u ook uw eigen toepassing op een AKS-cluster hebt, u de onderstaande stappen nog steeds volgen en de namen van uw eigen services en pods gebruiken.

### <a name="limitations"></a>Beperkingen

* UDP wordt op dit moment niet ondersteund.

### <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u geen Azure-abonnement hebt, u een [gratis account](https://azure.microsoft.com/free)maken.
* [Azure CLI geïnstalleerd][azure-cli].
* [Visual Studio Code][vs-code] met de [Azure Dev Spaces-extensie][azds-vs-code] geïnstalleerd en uitgevoerd op MacOS of Windows 10.
* De [voorbeeldtoepassing Azure Dev Spaces Bike Sharing](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp) of uw eigen toepassing die wordt uitgevoerd op een AKS-cluster.

## <a name="connect-your-development-computer"></a>Uw ontwikkelcomputer aansluiten

Open *dev-spaces/samples/BikeSharingApp/Bikes* in Visual Studio Code en gebruik de Azure Dev Spaces-extensie om uw ontwikkelcomputer aan te sluiten op uw AKS-cluster.

Als u de extensie Azure Dev Spaces wilt gebruiken, opent u het opdrachtpalet in Visual Studio-code door te klikken op *Weergave* en vervolgens *op Opdrachtpalet*. Begin `Azure Dev Spaces: Redirect` met typen `Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]`en `Azure Dev Spaces: Redirect an existing Kubernetes pod to my machine [Preview]`klik `Azure Dev Spaces: Redirect a new Kubernetes pod to my machine [Preview]`op een van beide , of .

![Opdrachten](../media/how-to-connect/connect-commands.png)

### <a name="select-a-redirection-option"></a>Een omleidingsoptie selecteren

Als u `Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]`uitvoert, wordt u gevraagd om een bestaande Kubernetes-service te kiezen:

![Service kiezen](../media/how-to-connect/connect-choose-service.png)

Met deze optie wordt al het verkeer in het AKS-cluster voor deze service omgeleid naar de versie van uw toepassing die op uw ontwikkelingscomputer wordt uitgevoerd. Als deze service meerdere pods heeft die worden uitgevoerd in het AKS-cluster, wordt al het verkeer voor deze service alleen doorgestuurd naar uw ontwikkelingscomputer. Azure Dev Spaces leidt ook al het uitgaande verkeer van de toepassing terug naar uw AKS-cluster.

Als u `Azure Dev Spaces: Redirect an existing Kubernetes pod to my machine [Preview]`uitvoert, wordt u gevraagd een specifieke pod te kiezen:

![Pod kiezen](../media/how-to-connect/connect-choose-pod.png)

Deze optie maakt verbinding met een specifieke pod. Deze optie is handig voor interactie met pods die geen verkeer verzenden of ontvangen en beëindigd pods repliceren. Als de pod verkeer verzendt en ontvangt, gedraagt deze `Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]` optie zich op een vergelijkbare manier als en wordt al het verkeer in het AKS-cluster omgeleid voor alle pods die betrekking hebben op de service van de geselecteerde pod.

Als u `Azure Dev Spaces: Redirect a new Kubernetes pod to my machine [Preview]`uitvoert, wordt u niet gevraagd een bestaande pod of service te selecteren. Met deze optie wordt al het uitgaande verkeer van de toepassing die op uw ontwikkelingscomputer wordt uitgevoerd, omgeleid naar het AKS-cluster.

Kies en selecteer `Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]` in dit voorbeeld de *fietsservice.*

### <a name="select-a-connection-mode"></a>Een verbindingsmodus selecteren

Nadat u de omleidingsoptie hebt geselecteerd, wordt u gevraagd de verbindingsmodus *Vervangen* of *Kloon* te kiezen.

![Vervangen of klonen](../media/how-to-connect/connect-replace-clone.png)

De optie *Vervangen* vervangt de huidige pod of service in het AKS-cluster en leidt al het verkeer voor die service om naar uw ontwikkelingscomputer. Deze optie kan storend zijn voor andere services in uw AKS-cluster die interactie hebben met de service die u omleidt, kan niet functioneren totdat u de toepassing op uw ontwikkelingscomputer start. Met de optie *Klonen* u een bestaande onderliggende dev-ruimte kiezen of een nieuwe onderliggende dev-ruimte maken voor het omleiden van verkeer voor een pod of service naar uw ontwikkelingscomputer. Met deze optie u geïsoleerd werken en andere services niet verstoren, omdat alleen verkeer naar die onderliggende dev-ruimte wordt doorgestuurd naar uw ontwikkelingscomputer. Met de optie *Klonen* moet uw AKS-cluster Azure Dev Spaces hebben ingeschakeld.

Kies in dit voorbeeld *vervangen*.

> [!NOTE]
> Als de pod van uw bestaande service meerdere containers heeft, wordt u ook gevraagd om de container van de toepassing te kiezen.

### <a name="select-a-port-for-your-application"></a>Een poort voor uw toepassing selecteren

Nadat u de verbindingsmodus hebt geselecteerd, wordt u gevraagd de TCP-poort van uw lokale toepassing in te voeren. Als uw toepassing meerdere poorten opent, scheidt u deze door een komma, bijvoorbeeld *80,81*. Als uw aanvraag geen netwerkaanvragen accepteert, voert u *0*in. Voer in dit voorbeeld *3000*in .

![Verbinding maken kiespoort](../media/how-to-connect/connect-choose-port.png)

### <a name="confirm-you-are-connected"></a>Bevestigen dat u verbinding hebt

Nadat u de TCP-poort van uw toepassing hebt geselecteerd, wordt er in Azure Dev Spaces een verbinding tot stand gebracht met het AKS-cluster. Azure Dev Spaces injecteert een agent in uw AKS-cluster om verkeer tussen het AKS-cluster en uw ontwikkelingscomputer om te leiden. Het kan enkele minuten duren voordat deze verbinding tot stand is gebracht. Azure Dev Spaces vraagt ook beheerderstoegang aan om het *hosts-bestand* op uw ontwikkelcomputer te wijzigen.

> [!IMPORTANT]
> Zodra Azure Dev Spaces een verbinding met uw AKS-cluster tot stand heeft gemaakt, werken de andere services in uw AKS-cluster mogelijk niet meer correct nadat u de service op uw ontwikkelingscomputer hebt gestart als u de verbindingsmodus *vervangen* kiest. U in plaats daarvan de *kloonverbindingsmodus* kiezen om een onderliggende dev-ruimte voor uw omleiding te maken en elke verstoring van de bovenliggende ruimte te voorkomen. Als uw service een afhankelijkheid heeft die niet beschikbaar is in uw ontwikkelingscomputer, moet u mogelijk uw toepassing wijzigen of [een extra configuratie](#additional-configuration) bieden

Azure Dev Spaces opent een terminalvenster met de titel *AZDS Connect - Bikes* nadat het een verbinding met uw AKS-cluster tot gevolg heeft. In dit terminalvenster zijn alle omgevingsvariabelen en DNS-vermeldingen geconfigureerd vanuit uw AKS-cluster. Alle code die u in dit terminalvenster uitvoert of met de foutopsporing van de Visual Studio-code gebruikt, is verbonden met het AKS-cluster.

![Terminal](../media/how-to-connect/connect-terminal.png)

Bovendien maakt Azure Dev Spaces een venster met de titel *Dev Spaces Connect* with all its output.

![Uitvoer](../media/how-to-connect/connect-output.png)

Azure Dev Spaces heeft ook een statusbalkitem met de verbindingsstatus.

![Status](../media/how-to-connect/connect-status.png)

Controleer de statusbalk toont *Dev Spaces: Verbonden met dev/bikes op lokale poort 3000*.

### <a name="configure-your-application-on-your-development-computer"></a>Uw toepassing configureren op uw ontwikkelcomputer

Open de *AZDS Connect -* `npm install`Bikes terminal venster en lopen:

```console
$ npm install

> fsevents@1.2.9 install ./dev-spaces/samples/BikeSharingApp/Bikes/node_modules/fsevents
> node install
...
```

Klik *op Foutopsporing en* open *configuraties*. Als u wordt gevraagd een omgeving te selecteren, kiest u *Node.js*. Hiermee wordt `.vscode/launch.json` een bestand aangenomen. Vervang de inhoud van dat bestand door het volgende:

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

Open [package.json](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/Bikes/package.json) en voeg een foutopsporingsscript toe:

```json
  "devDependencies": {
    "nodemon": "^1.18.10"
  },
  "scripts": {
    "debug": "node --nolazy --inspect-brk=9229 server.js"
  }
```

### <a name="start-your-application-on-your-development-computer"></a>Uw toepassing starten op uw ontwikkelcomputer

Klik op het pictogram *Foutopsporing* aan de linkerkant en klik op de startknop naast *Starten via NPM* bovenaan.

![Starten via NPM](../media/how-to-connect/launch-npm.png)

Uw toepassing wordt gestart en Azure Dev Spaces leidt het verkeer om tussen uw AKS-cluster en uw ontwikkelcomputer. U ziet berichten die vergelijkbaar zijn met de onderstaande in de *Foutopsporingsconsole:*

```console
/usr/local/bin/npm run-script debug 
...
Debugger attached.
Collection: bikes
MongoDB connection string: mongodb://databases-mongo
Connected to MongoDB
Listening on port 3000
```

Navigeer naar de *bikesharingweb-service* door op de statusbalk azure Dev Spaces te klikken en de openbare URL van uw toepassing te kiezen. U de openbare URL `azds list-uris` ook vinden in de opdracht die u eerder hebt uitgevoerd. Als u Azure Dev Spaces niet gebruikt op uw cluster, gebruikt u het IP of de URL voor de toepassing voor de naamruimte die u gebruikt. In het bovenstaande voorbeeld is `http://dev.bikesharingweb.fedcab0987.eus.azds.io/`de openbare URL voor de *bikesharingweb-service* . Selecteer *Aurelia Briggs (klant)* als gebruiker en selecteer vervolgens een fiets om te huren.

### <a name="set-a-break-point"></a>Een breekpunt instellen

Open [server.js](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/Bikes/server.js#L233) en klik ergens op regel 233 om de cursor daar te plaatsen. Stel een breekpunt in door op *F9* te drukken of op *Foutopsporing* te klikken *en Breekpunt in te schakelen*.

Navigeer naar de *bikesharingweb-service* door de openbare URL te openen. Selecteer *Aurelia Briggs (klant)* als gebruiker en selecteer vervolgens een fiets om te huren. Let op het beeld voor de fiets niet laden. Ga terug naar Visual Studio Code en observeer lijn 233 wordt gemarkeerd. Het breekpunt dat u instelt, heeft de service op lijn 233 onderbroken. Als u de service wilt hervatten, klikt u op *F5* of klikt u op *Foutopsporing* *en vervolgens Doorgaan*. Ga terug naar uw browser en controleer of u een tijdelijke aanduiding voor de fiets ziet.

Verwijder het breekpunt door uw cursor `server.js` op lijn 233 in en raken *F9*.

### <a name="update-your-application"></a>Uw toepassing bijwerken

Bewerken `server.js` om de lijnen 232 en 233 te verwijderen:

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

Sla de wijzigingen op en klik op *Foutopsporing* *en start Foutopsporing opnieuw*op. Vernieuw uw browser en controleer of u geen tijdelijke aanduidingsafbeelding meer voor de fiets ziet.

Klik *op Foutopsporing* *en stop de foutopsporing* om de foutopsporing te stoppen. Klik op de statusbalk azure Dev Spaces om de verbinding met het AKS-cluster te verbreken.

## <a name="additional-configuration"></a>Aanvullende configuratie

Azure Dev Spaces kan routeringsverkeer en het repliceren van omgevingsvariabelen verwerken zonder extra configuratie. Als u bestanden moet downloaden die zijn gemonteerd op de container in uw AKS-cluster, `azds-local.env` zoals een ConfigMap-bestand, u een bestand maken om deze bestanden naar uw ontwikkelingscomputer te downloaden.

Hier is `azds-local.env`een voorbeeld:

```
# This downloads the "whitelist" volume from the container,
# saves it to a temporary directory on your development computer,
# and sets the full path to an environment variable called WHITELIST_PATH.

WHITELIST_PATH=${volumes.whitelist}/whitelist

# This downloads a file from the container's 'default-token-<any>' mount directory 
# to /var/run/secrets/kubernetes.io/serviceaccount on your development computer.

KUBERNETES_IN_CLUSTER_CONFIG_OVERWRITE=${volumes.default-token-*|/var/run/secrets/kubernetes.io/serviceaccount}


# This makes the myapp1 service available to your development computer
# regardless of the AKS cluster you are connected to and
# sets the local IP to an environment variable called MYAPP1_SERVICE_HOST.

# If the myapp1 service is made available in this way, 
# you can also access it using "myapp1" and "myapp1.svc.cluster.local"
# in addition to the IP in the MYAPP1_SERVICE_HOST environment variable.

MYAPP1_SERVICE_HOST=${services.myapp1}

# This makes the service myapp2 in namespace mynamespace available to your 
# development computer regardless of the AKS cluster you are connected to and
# sets the local IP to an environment variable called MYAPP2_SERVICE_HOST.

MYAPP2_SERVICE_HOST=${services.mynamespace.myapp2}
```

## <a name="using-logging-and-diagnostics"></a>Logging en diagnostiek gebruiken

Logboekuitvoer wordt naar het venster *Dev Spaces Connect* geschreven nadat u uw ontwikkelcomputer hebt aangesloten op uw AKS-cluster.

![Uitvoer](../media/how-to-connect/connect-output.png)

Klik op de statusbalk azure Dev Spaces en kies *Diagnostische gegevens weergeven*. Met deze opdracht worden de huidige omgevingsvariabelen en DNS-volledigen afgedrukt in de logboekuitvoer.

![Uitvoer met diagnostiek](../media/how-to-connect/connect-output-diagnostics.png)

Bovendien u de diagnostische `Azure Dev Spaces` logboeken in directory vinden in de [ *TEMP-map* van uw ontwikkelingscomputer.][azds-tmp-dir]

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het gebruik van Azure Dev Spaces en GitHub Actions om wijzigingen van een pull-aanvraag rechtstreeks in AKS te testen voordat de pull-aanvraag wordt samengevoegd in de hoofdvestiging van uw opslagplaats.

> [!div class="nextstepaction"]
> [GitHub-acties & Azure Kubernetes-service][gh-actions]

[azds-tmp-dir]: ../troubleshooting.md#before-you-begin
[azds-vs-code]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[azure-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[bike-sharing-github]: https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp
[gh-actions]: github-actions.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[team-quickstart]: ../quickstart-team-development.md
[vs-code]: https://code.visualstudio.com/download