---
title: Lokaal proces met Kubernetes gebruiken met Visual Studio Code (preview)
services: azure-dev-spaces
ms.date: 06/02/2020
ms.topic: conceptual
description: Meer informatie over het gebruik van lokaal proces met Kubernetes om uw ontwikkel computer te verbinden met een Kubernetes-cluster met Azure dev Spaces
keywords: Lokaal proces met Kubernetes, Azure dev Spaces, dev Spaces, docker, Kubernetes, azure, AKS, Azure Kubernetes service, containers
ms.openlocfilehash: 23a94528ffa4e9e412f472349ea26d1a14003616
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84316738"
---
# <a name="use-local-process-with-kubernetes-with-visual-studio-code-preview"></a>Lokaal proces met Kubernetes gebruiken met Visual Studio Code (preview)

Met het lokale proces met Kubernetes kunt u code op uw ontwikkel computer uitvoeren en fouten opsporen, terwijl u nog steeds met uw Kubernetes-cluster verbonden bent met de rest van uw toepassing of services. Als u bijvoorbeeld een grote micro Services-architectuur hebt met veel onderling afhankelijke services en data bases, kan het repliceren van die afhankelijkheden op uw ontwikkel computer lastig zijn. Daarnaast kan het maken en implementeren van code voor uw Kubernetes-cluster voor elke code wijziging tijdens de interne loop tijd traag, tijdrovend en moeilijk te gebruiken zijn met een fout opsporingsprogramma.

Lokaal proces met Kubernetes voor komt dat u uw code hoeft te bouwen en implementeren in uw cluster door in plaats daarvan rechtstreeks een verbinding te maken tussen uw ontwikkel computer en uw cluster. Wanneer u de ontwikkel computer met het cluster verbindt tijdens het opsporen van fouten, kunt u uw service snel testen en ontwikkelen in de context van de volledige toepassing zonder dat u een docker-of Kubernetes-configuratie hoeft te maken.

Lokaal proces met Kubernetes omleidt verkeer tussen uw verbonden Kubernetes-cluster en uw ontwikkel computer. Met deze omleidings functie voor verkeer kan code op uw ontwikkel computer en-services die worden uitgevoerd in uw Kubernetes-cluster communiceren alsof ze zich in hetzelfde Kubernetes-cluster bevinden. Lokaal proces met Kubernetes biedt ook een manier om omgevings variabelen en gekoppelde volumes te repliceren die beschikbaar zijn voor een Peul in uw Kubernetes-cluster in uw ontwikkel computer. Door toegang te bieden tot omgevings variabelen en gekoppelde volumes op uw ontwikkel computer, kunt u snel aan uw code werken zonder dat u deze afhankelijkheden hand matig hoeft te repliceren.

In deze hand leiding vindt u informatie over het gebruik van lokaal proces met Kubernetes om verkeer tussen uw Kubernetes-cluster en code die wordt uitgevoerd op uw ontwikkel computer, om te leiden. Deze hand leiding bevat ook een script voor het implementeren van een grote voorbeeld toepassing met meerdere micro Services op een Kubernetes-cluster.

> [!IMPORTANT]
> Deze functie is momenteel beschikbaar als preview-product. Previews worden voor u beschikbaar gesteld op voorwaarde dat u akkoord gaat met de [aanvullende gebruiksvoorwaarden][preview-terms]. Sommige aspecten van deze functionaliteit kunnen wijzigen voordat deze functionaliteit algemeen beschikbaar wordt.

## <a name="before-you-begin"></a>Voordat u begint

In deze hand leiding wordt gebruikgemaakt van de [voorbeeld toepassing voor delen van Azure dev Spaces Bike][bike-sharing-github] om te demonstreren hoe u uw ontwikkel computer verbindt met een Kubernetes-cluster Als u al uw eigen toepassing hebt uitgevoerd op een Kubernetes-cluster, kunt u de onderstaande stappen volgen en de namen van uw eigen services gebruiken.

### <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u geen Azure-abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free) maken.
* [Azure CLI geïnstalleerd][azure-cli].
* [Visual Studio-code][vs-code] die wordt uitgevoerd op MacOS of Windows 10.
* De [Azure dev Spaces][azds-vs-code] -extensie versie 2.0.220200601 of hoger geïnstalleerd in Visual Studio code.
* [Azure dev Spaces cli geïnstalleerd][azds-cli].

## <a name="create-a-kubernetes-cluster"></a>Een Kubernetes-cluster maken

Maak een AKS-cluster in een [ondersteunde regio][supported-regions]. Met de onderstaande opdrachten maakt u een resourcegroep met de naam *MyResourceGroup* en een AKS-cluster met de naam *MyAKS*.

```azurecli-interactive
az group create \
    --name MyResourceGroup \
    --location eastus

az aks create \
    --resource-group MyResourceGroup \
    --name MyAKS \
    --location eastus \
    --node-count 3 \
    --generate-ssh-keys
```

## <a name="install-the-sample-application"></a>De voorbeeld toepassing installeren

Installeer de voorbeeld toepassing op uw cluster met behulp van het meegeleverde script. U kunt dit script uitvoeren op uw ontwikkel computer of met behulp van de [Azure Cloud shell][azure-cloud-shell].

> [!IMPORTANT]
> U moet *eigenaar* of *Inzender* toegang hebben tot uw cluster om het script uit te voeren.

```azurecli-interactive
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/
chmod +x ./local-process-quickstart.sh
./local-process-quickstart.sh -g MyResourceGroup -n MyAKS
```

Ga naar de voorbeeld toepassing die uw cluster uitvoert door de open bare URL te openen, die wordt weer gegeven in de uitvoer van het installatie script.

```console
$ ./local-process-quickstart.sh -g MyResourceGroup -n MyAKS
Defaulting Dev spaces repository root to current directory : ~/dev-spaces
Setting the Kube context
...
To try out the app, open the url:
dev.bikesharingweb.EXTERNAL_IP.nip.io
```

In het bovenstaande voor beeld is de open bare URL `dev.bikesharingweb.EXTERNAL_IP.nip.io` .

## <a name="connect-to-your-cluster-and-debug-a-service"></a>Verbinding maken met uw cluster en fouten opsporen in een service

Down load en configureer de Kubernetes CLI op uw ontwikkel computer om verbinding te maken met uw Kubernetes-cluster met behulp van [AZ AKS Get-credentials][az-aks-get-credentials].

```azurecli
az aks get-credentials --resource-group MyResourceGroup --name MyAKS
```

Open *dev-Spaces/samples/BikeSharingApps/Bikes* in de [voorbeeld toepassing Azure dev Spaces fiets delen][bike-sharing-github] in Visual Studio code. Open de extensie van de Azure Kubernetes-service en selecteer de naam ruimte voor *ontwikkel aars* in het *MyAKS* -cluster.

![Naam ruimte selecteren](../media/local-process-kubernetes-vs-code/select-namespace.png)

Gebruik de `npm install` opdracht om de afhankelijkheden voor de toepassing te installeren.

```console
npm install
```

Selecteer het pictogram *fout opsporing* aan de linkerkant en selecteer *lokaal proces met Kubernetes (preview)* bovenaan.

![Lokaal proces kiezen met Kubernetes](../media/local-process-kubernetes-vs-code/choose-local-process.png)

Klik op de knop Start naast *lokaal proces met Kubernetes (preview)*. De eerste keer dat u deze start configuratie uitvoert, wordt u gevraagd de service te configureren die u wilt vervangen, de poort die moet worden door gegeven vanaf uw ontwikkel computer en de taak die moet worden gestart.

Kies de service *Bikes* .

![Service kiezen](../media/local-process-kubernetes-vs-code/choose-service.png)

Al het verkeer in het Kubernetes-cluster wordt omgeleid naar *de versie* van uw toepassing die wordt uitgevoerd op uw ontwikkel computer. Lokaal proces met Kubernetes routeert ook al het uitgaande verkeer van de toepassing terug naar uw Kubernetes-cluster.

> [!IMPORTANT]
> U kunt alleen services met één pod omleiden.

Nadat u uw service hebt geselecteerd, wordt u gevraagd om de TCP-poort voor uw lokale toepassing in te voeren. Voer voor dit voor beeld *3000*in.

![Poort kiezen voor verbinding](../media/local-process-kubernetes-vs-code/choose-port.png)

Kies *starten via NPM* als taak starten.

![Verbinding maken kiezen taak](../media/local-process-kubernetes-vs-code/choose-launch.png)

> [!NOTE]
> U wordt gevraagd om de *KubernetesDNSManager* uit te voeren met verhoogde bevoegdheden en uw hosts-bestand te wijzigen.

Uw ontwikkel computer is aangesloten op de status balk oranje en de extensie voor dev Spaces laat zien dat u bent verbonden.

![Ontwikkel computer verbonden](../media/local-process-kubernetes-vs-code/development-computer-connected.png)

> [!NOTE]
> Op subesquent wordt u niet gevraagd naar de service naam, de poort of de taak starten. Deze waarden worden opgeslagen in *. vscode/tasks.jsop*.

Als uw ontwikkel computer is verbonden, wordt het verkeer omgeleid naar uw ontwikkel computer voor de service die u wilt vervangen.

## <a name="set-a-break-point"></a>Een breek punt instellen

Open [server.js][server-js-breakpoint] en klik ergens in regel 233 om de cursor daar te plaatsen. Stel een onderbrekings punt in door op *F9* te klikken of op *uitvoeren* en vervolgens *onderbrekings punt*.

Ga naar de voorbeeld toepassing door de open bare URL te openen. Selecteer *Aurelia Briggs (klant)* als de gebruiker en selecteer vervolgens een te huur fiets. U ziet dat de afbeelding voor de fiets niet wordt geladen. Ga terug naar Visual Studio code en kijk regel 233 is gemarkeerd. Het onderbrekings punt dat u hebt ingesteld, heeft de service onderbroken op regel 233. Als u de service wilt hervatten, druk op *F5* of klik op *uitvoeren* en vervolgens op *door gaan*. Ga terug naar uw browser en controleer of er een tijdelijke aanduiding voor de fiets wordt weer gegeven.

Verwijder het onderbrekings punt door de cursor in regel 233 in `server.js` te zetten en op *F9 te drukken*.

### <a name="update-your-application"></a>Uw toepassing bijwerken

Bewerken `server.js` om de regels 234 en 235 te verwijderen:

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

Sla de wijzigingen op en klik op *uitvoeren* en vervolgens op *fout opsporing opnieuw opstarten*. Nadat u opnieuw verbinding hebt gemaakt, vernieuwt u de browser en controleert u of er geen tijdelijke aanduiding voor de fiets meer wordt weer geven.

Klik op *uitvoeren* en vervolgens op *fout opsporing stoppen* om het fout opsporingsprogramma te stoppen.

> [!NOTE]
> Als u de taak voor fout opsporing stopt, wordt de verbinding van uw ontwikkel computer met uw Kubernetes-cluster standaard verbroken. U kunt dit gedrag wijzigen door te zoeken naar *lokaal proces met Kubernetes: Verbreek de verbinding met* de Visual Studio code-instellingen en de controle naast *verbinding automatisch verbreken wanneer de fout opsporing wordt beëindigd.* Nadat deze instelling is bijgewerkt, blijft de ontwikkel computer verbonden wanneer u de fout opsporing stopt en start. Als u de ontwikkel computer wilt loskoppelen van uw cluster, klikt u op de Azure dev Spaces-extensie op de status balk en kiest u *huidige sessie verbreken*.
>
> Als Visual Studio code plotseling de verbinding met het cluster verbreekt of beëindigt, wordt de service die u omleidt mogelijk niet teruggezet naar de oorspronkelijke staat voordat u met het lokale proces hebt verbonden met Kubernetes. Raadpleeg de [hand leiding][troubleshooting]voor het oplossen van problemen om dit probleem op te lossen.

## <a name="using-logging-and-diagnostics"></a>Logboek registratie en diagnostische gegevens gebruiken

Logboek registratie van de uitvoer wordt naar het venster *dev Spaces* geschreven nadat de ontwikkel computer is verbonden met uw Kubernetes-cluster.

![Uitvoer](../media/local-process-kubernetes-vs-code/output.png)

Klik op de status balk van Azure dev Spaces en kies *Diagnostische gegevens van verbinding weer geven*. Met deze opdracht worden de huidige omgevings variabelen en DNS-gegevens in de logboek registratie uitgevoerd.

![Uitvoer met diagnostische gegevens](../media/local-process-kubernetes-vs-code/output-diagnostics.png)

Daarnaast vindt u de diagnostische Logboeken in de `Azure Dev Spaces` map in de map [ *temp* van uw ontwikkel computer][azds-tmp-dir].

## <a name="remove-the-sample-application-from-your-cluster"></a>De voorbeeld toepassing uit uw cluster verwijderen

Gebruik het meegeleverde script om de voorbeeld toepassing uit uw cluster te verwijderen.

```azurecli-interactive
./local-process-quickstart.sh -c -g MyResourceGroup -n MyAKS
```

## <a name="next-steps"></a>Volgende stappen

Leer hoe u Azure dev Spaces en GitHub-acties kunt gebruiken om wijzigingen van een pull-aanvraag rechtstreeks in AKS te testen voordat de pull-aanvraag wordt samengevoegd in de hoofd vertakking van de opslag plaats.

> [!div class="nextstepaction"]
> [GitHub-acties & Azure Kubernetes-service][gh-actions]

[azds-cli]: install-dev-spaces.md#install-the-client-side-tools
[azds-tmp-dir]: ../troubleshooting.md#before-you-begin
[azds-vs-code]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[azure-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[azure-cloud-shell]: ../../cloud-shell/overview.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-aks-vs-code]: https://marketplace.visualstudio.com/items?itemName=ms-kubernetes-tools.vscode-aks-tools
[bike-sharing-github]: https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp
[gh-actions]: github-actions.md
[preview-terms]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[server-js-breakpoint]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/Bikes/server.js#L233
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[troubleshooting]: ../troubleshooting.md#fail-to-restore-original-configuration-of-deployment-on-cluster
[vs-code]: https://code.visualstudio.com/download