---
title: Lokaal proces met Kubernetes gebruiken met Visual Studio (preview)
services: azure-dev-spaces
ms.date: 06/02/2020
ms.topic: conceptual
description: Meer informatie over het gebruik van een lokaal proces met Kubernetes met Visual Studio om uw ontwikkel computer te verbinden met een Kubernetes-cluster met Azure dev Spaces
keywords: Lokaal proces met Kubernetes, Azure dev Spaces, dev Spaces, docker, Kubernetes, azure, AKS, Azure Kubernetes service, containers
ms.openlocfilehash: 8e89baeac8d9042a87685ed1268ed694db8d9455
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84316801"
---
# <a name="use-local-process-with-kubernetes-with-visual-studio-preview"></a>Lokaal proces met Kubernetes gebruiken met Visual Studio (preview)

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
* [Visual Studio 2019][visual-studio] versie 16,7 Preview 2 of hoger wordt uitgevoerd op Windows 10 met de *ASP.net-en Web Development* -en *Azure Development* -workloads geïnstalleerd.
* [Azure dev Spaces cli geïnstalleerd][azds-cli].

### <a name="enable-the-local-process-with-kubernetes-preview-feature-in-visual-studio"></a>Het lokale proces met de preview-functie voor Kubernetes in Visual Studio inschakelen

Als u lokaal proces met Kubernetes in Visual Studio wilt inschakelen, klikt u op *extra*  >  *Opties*  >  *omgeving*  >  *Preview-functies*. Selecteer *lokale fout opsporing inschakelen voor Kubernetes Services*.

Installeer ook voor .NET-console toepassingen het pakket *micro soft. Visual Studio. Azure. Kubernetes. tools. targets* NuGet.

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

Open *dev-Spaces/samples/BikeSharingApp/ReservationEngine/app. csproj* van de [Azure dev Spaces Bike-voorbeeld toepassing delen][bike-sharing-github] in Visual Studio.

Selecteer in uw project *lokaal proces met Kubernetes* in de vervolg keuzelijst start instellingen zoals hieronder wordt weer gegeven.

![Lokaal proces kiezen met Kubernetes](../media/local-process-kubernetes-visual-studio/choose-local-process.png)

Klik op de knop Start naast *lokaal proces met Kubernetes*. In het dialoog venster *lokaal proces met Kubernetes* :

* Selecteer uw abonnement.
* Selecteer *MyAKS* voor uw cluster.
* Selecteer *dev* voor uw naam ruimte.
* Selecteer *reservationengine* voor de service die u wilt omleiden.
* Selecteer een *app* voor het start profiel.
* Selecteer `http://dev.bikesharingweb.EXTERNAL_IP.nip.io` voor de URL om uw browser te starten.

![Lokaal proces kiezen met Kubernetes-cluster](../media/local-process-kubernetes-visual-studio/choose-local-process-cluster.png)

> [!IMPORTANT]
> U kunt alleen services met één pod omleiden.

Klik op *opslaan en start de fout opsporing*.

Al het verkeer in het Kubernetes-cluster wordt omgeleid naar de *reservationengine* -service voor de versie van uw toepassing die wordt uitgevoerd op uw ontwikkel computer. Lokaal proces met Kubernetes routeert ook al het uitgaande verkeer van de toepassing terug naar uw Kubernetes-cluster.

> [!NOTE]
> U wordt gevraagd om de *KubernetesDNSManager* uit te voeren met verhoogde bevoegdheden en uw hosts-bestand te wijzigen.

Uw ontwikkel computer is verbonden als op de status balk wordt aangegeven dat u verbinding hebt met de *reservationengine* -service.

![Ontwikkel computer verbonden](../media/local-process-kubernetes-visual-studio/development-computer-connected.png)

> [!NOTE]
> Bij het starten van subesquent wordt u niet gevraagd met het dialoog venster *lokaal proces met Kubernetes* . U kunt deze instellingen bijwerken in het deel venster *fout opsporing* in de project eigenschappen.

Als uw ontwikkel computer is verbonden, wordt het verkeer omgeleid naar uw ontwikkel computer voor de service die u wilt vervangen.

## <a name="set-a-break-point"></a>Een breek punt instellen

Open [BikesHelper.cs][bikeshelper-cs-breakpoint] en klik ergens op regel 26 om de cursor daar te plaatsen. Stel een onderbrekings punt in door op *F9* te klikken of op *fout opsporing* en vervolgens *onderbrekings punt*.

Ga naar de voorbeeld toepassing door de open bare URL te openen. Selecteer *Aurelia Briggs (klant)* als de gebruiker en selecteer vervolgens een te huur fiets. Klik op *fiets*. Ga terug naar Visual Studio en kijk regel 26 is gemarkeerd. Het onderbrekings punt dat u instelt, heeft de service op regel 26 onderbroken. Als u de service wilt hervatten, drukt u op *F5* of klikt u op *Fouten opsporen* vervolgens *Doorgaan*. Ga terug naar uw browser en controleer of de pagina laat zien dat u de fiets hebt gehuurd.

Verwijder het onderbrekings punt door de cursor op regel 26 in `BikesHelper.cs` te zetten en op *F9 te drukken*.

> [!NOTE]
> Als u de taak voor fout opsporing stopt, wordt de verbinding van uw ontwikkel computer met uw Kubernetes-cluster standaard verbroken. U kunt dit gedrag wijzigen door het wijzigen van de *verbinding verbreken na fout opsporing* in *Onwaar* in het gedeelte *hulpprogram Ma's* voor fout opsporing in Kubernetes van de opties voor fout opsporing. Nadat deze instelling is bijgewerkt, blijft de ontwikkel computer verbonden wanneer u de fout opsporing stopt en start. Klik op de knop *verbinding verbreken* op de werk balk om uw ontwikkel computer van het cluster los te koppelen.
>
> Als Visual Studio de verbinding met het cluster plotseling beëindigt of beëindigt, wordt de service die u omleidt mogelijk niet teruggezet naar de oorspronkelijke staat voordat u met het lokale proces hebt verbonden met Kubernetes. Raadpleeg de [hand leiding][troubleshooting]voor het oplossen van problemen om dit probleem op te lossen.

## <a name="using-logging-and-diagnostics"></a>Logboek registratie en diagnostische gegevens gebruiken

U vindt de diagnostische Logboeken in de map van de map `Azure Dev Spaces` Temp van uw [ontwikkel computer *TEMP* ][azds-tmp-dir].

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
[bikeshelper-cs-breakpoint]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/ReservationEngine/BikesHelper.cs#L26
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[troubleshooting]: ../troubleshooting.md#fail-to-restore-original-configuration-of-deployment-on-cluster
[visual-studio]: https://www.visualstudio.com/vs/