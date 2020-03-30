---
title: 'Debugen en herhalen op Kubernetes: Visual Studio Code & .NET Core'
services: azure-dev-spaces
ms.date: 07/08/2019
ms.topic: quickstart
description: Met deze quickstart u Azure Dev Spaces en Visual Studio Code gebruiken om een .NET Core-toepassing op Azure Kubernetes-service te debuggen en snel te herhalen
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers, Helm, service mesh, service mesh routing, kubectl, k8s
manager: gwallace
ms.openlocfilehash: b38562879fa67d7ee82e3251ea2fcaa57a2075d6
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80240212"
---
# <a name="quickstart-debug-and-iterate-on-kubernetes-visual-studio-code-and-net-core---azure-dev-spaces"></a>Snelstart: Foutopsporing en herhalen op Kubernetes: Visual Studio Code en .NET Core - Azure Dev Spaces

In deze handleiding leert u het volgende:

- Azure Dev Spaces instellen met een beheerd Kubernetes-cluster in Azure.
- Iteratief code ontwikkelen in containers met behulp van Visual Studio Code.
- Debug de code in uw dev-ruimte van Visual Studio Code.

Azure Dev Spaces stelt u ook in staat om te debuggen en herhalen met behulp van:
- [Java- en Visual Studio-code](quickstart-java.md)
- [Node.js en Visual Studio Code](quickstart-nodejs.md)
- [.NET Core en Visual Studio](quickstart-netcore-visualstudio.md)

## <a name="prerequisites"></a>Vereisten

- Een Azure-abonnement. Als u nog geen account hebt, kunt u [een gratis account aanmaken](https://azure.microsoft.com/free).
- [Visual Studio Code geïnstalleerd](https://code.visualstudio.com/download).
- De [Azure Dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) en [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) extensies voor Visual Studio Code geïnstalleerd.
- [Azure CLI geïnstalleerd](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="create-an-azure-kubernetes-service-cluster"></a>Een Azure Kubernetes Service-cluster maken

U moet een AKS-cluster maken in een [ondersteund gebied.][supported-regions] Met de onderstaande opdrachten wordt een resourcegroep met de naam *MyResourceGroup* en een AKS-cluster met de naam *MyAKS*gemaakt.

```azurecli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Azure Dev-ruimten inschakelen op uw AKS-cluster

Gebruik `use-dev-spaces` de opdracht om Dev Spaces in te schakelen op uw AKS-cluster en volg de aanwijzingen. Met de opdracht Dev Spaces op het *MyAKS-cluster* in de groep *MyResourceGroup* wordt een standaarddev-ruimte gemaakt. *default*

> [!NOTE]
> De `use-dev-spaces` opdracht installeert ook de Azure Dev Spaces CLI als deze nog niet is geïnstalleerd. U de AZURE Dev Spaces CLI niet installeren in de Azure Cloud Shell.

```azurecli
az aks use-dev-spaces -g MyResourceGroup -n MyAKS
```

```output
'An Azure Dev Spaces Controller' will be created that targets resource 'MyAKS' in resource group 'MyResourceGroup'. Continue? (y/N): y

Creating and selecting Azure Dev Spaces Controller 'MyAKS' in resource group 'MyResourceGroup' that targets resource 'MyAKS' in resource group 'MyResourceGroup'...2m 24s

Select a dev space or Kubernetes namespace to use as a dev space.
 [1] default
Type a number or a new name: 1

Kubernetes namespace 'default' will be configured as a dev space. This will enable Azure Dev Spaces instrumentation for new workloads in the namespace. Continue? (Y/n): Y

Configuring and selecting dev space 'default'...3s

Managed Kubernetes cluster 'MyAKS' in resource group 'MyResourceGroup' is ready for development in dev space 'default'. Type `azds prep` to prepare a source directory for use with Azure Dev Spaces and `azds up` to run.
```

## <a name="get-sample-application-code"></a>Voorbeeldtoepassingscode ophalen

In dit artikel gebruikt u de [voorbeeldtoepassing Azure Dev Spaces](https://github.com/Azure/dev-spaces) om aan te tonen met Azure Dev Spaces.

Kloon de toepassing van GitHub.

```cmd
git clone https://github.com/Azure/dev-spaces
```

## <a name="prepare-the-sample-application-in-visual-studio-code"></a>De voorbeeldtoepassing voorbereiden in Visual Studio Code

Open Visual Studio Code, klik op *Bestand* en *open...*, navigeer naar de *dev-spaces/samples/dotnetcore/Getting-started/webfrontend* directory en klik op *Openen*.

Je hebt nu het *webfrontend* project geopend in Visual Studio Code. Als u de toepassing wilt uitvoeren in uw ontwikkelaarsruimte, genereert u de grafiekelementen Docker en Helm met de extensie Azure Dev Spaces in het opdrachtpalet.

Als u het opdrachtpalet wilt openen in Visual Studio Code, klikt u op *Weergave* vervolgens *Opdrachtpalet*. Begin `Azure Dev Spaces` met typen `Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces`en klik op .

![Configuratiebestanden voorbereiden voor Azure Dev Spaces](./media/common/command-palette.png)

Wanneer Visual Studio Code u ook vraagt om `Yes` uw openbare eindpunt te configureren, kiest u om een openbaar eindpunt in te schakelen.

![Openbaar eindpunt selecteren](media/common/select-public-endpoint.png)

Met deze opdracht bereidt u uw project voor om in Azure Dev Spaces uit te voeren door een Dockerfile- en Helm-diagram te genereren. Het genereert ook een *.vscode* directory met debugging configuratie aan de basis van uw project.

> [!TIP]
> De [dockerfile- en helmdiagram](how-dev-spaces-works-prep.md#prepare-your-code) voor uw project wordt gebruikt door Azure Dev Spaces om uw code te bouwen en uit te voeren, maar u deze bestanden wijzigen als u de manier waarop het project wordt gebouwd en uitgevoerd wilt wijzigen.

## <a name="build-and-run-code-in-kubernetes-from-visual-studio-code"></a>Code bouwen en uitvoeren in Kubernetes vanuit Visual Studio Code

Klik op het pictogram *Foutopsporing* aan de linkerkant en klik bovenaan op *.NET Core Launch (AZDS).*

![](media/get-started-netcore/debug-configuration.png)

Met deze opdracht worden uw service in Azure Dev Spaces in de foutopsporingsmodus gebouwd en uitgevoerd. Het *venster Terminal* onderaan toont de buildoutput en URL's voor uw service die wordt uitgevoerd in Azure Dev Spaces. De *Foutopsporingsconsole* toont de logboekuitvoer.

> [!Note]
> Als u geen opdrachten voor Azure Dev Spaces ziet in het *opdrachtpalet,* controleert u of u de [visual studiocode-extensie voor Azure Dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds)hebt geïnstalleerd. Controleer ook of u de *map dev-spaces/samples/dotnetcore/Getting-started/webfrontend* hebt geopend in Visual Studio Code.

U de service zien die wordt uitgevoerd door de openbare URL te openen.

> [!Note]
> In eerste instantie kan de openbare URL een *fout van de slechte gateway* weergeven. Wacht een paar seconden voordat u de webpagina vernieuwt en u moet uw service zien draaien.

Klik *op Foutopsporing* *en stop de foutopsporing* om de foutopsporing te stoppen.

## <a name="update-code"></a>Code bijwerken

Als u een bijgewerkte versie van uw service wilt implementeren, u elk bestand in uw project bijwerken en *.NET Core Launch (AZDS)* opnieuw uitvoeren. Bijvoorbeeld:

1. Als uw toepassing nog steeds wordt uitgevoerd, klikt u op *Foutopsporing* *en stopt u foutopsporing* om deze te stoppen.
1. [Update lijn 22 in `Controllers/HomeController.cs` ](https://github.com/Azure/dev-spaces/blob/master/samples/dotnetcore/getting-started/webfrontend/Controllers/HomeController.cs#L22) naar:
    
    ```csharp
    ViewData["Message"] = "Your application description page in Azure.";
    ```

1. Sla uw wijzigingen op.
1. Opnieuw uitvoeren *.NET Core Launch (AZDS)*.
1. Navigeer naar uw hardloopservice en klik op *Informatie over*.
1. Let op uw wijzigingen.
1. Klik *op Foutopsporing* *en stop de foutopsporing* om de toepassing te stoppen.

## <a name="setting-and-using-breakpoints-for-debugging"></a>Breekpunten instellen en gebruiken voor foutopsporing

Start uw service in de foutopsporingsmodus met *.NET Core Launch (AZDS).*

Navigeer terug naar de *Explorer-weergave* door op *Weergave* vervolgens *Verkenner*te klikken . Open `Controllers/HomeController.cs` en klik ergens op lijn 22 om je cursor daar te plaatsen. Als u een breakpoint wilt instellen, klikt *u* op *Foutopsporing en* schakelt *u Breekpunt in*.

Open uw service in een browser en merk dat er geen bericht wordt weergegeven. Ga terug naar Visual Studio Code en observeer lijn 20 wordt gemarkeerd. Het breekpunt dat u instelt, heeft de service op lijn 20 onderbroken. Als u de service wilt hervatten, klikt u op *F5* of klikt u op *Foutopsporing* *en vervolgens Doorgaan*. Keer terug naar uw browser en merk dat het bericht nu wordt weergegeven.

Tijdens het uitvoeren van uw service in Kubernetes met een debugger gekoppeld, hebt u volledige toegang tot foutopsporingsinformatie zoals de callstack, lokale variabelen en uitzonderingsinformatie.

Verwijder het breekpunt door uw cursor `Controllers/HomeController.cs` op lijn 22 in en raken *F9*.

## <a name="update-code-from-visual-studio-code"></a>Code bijwerken vanuit Visual Studio Code

Terwijl de service wordt uitgevoerd in de foutopsporingsmodus, werkt u regel 22 in `Controllers/HomeController.cs`. Bijvoorbeeld:

```csharp
ViewData["Message"] = "Your application description page in Azure while debugging!";
```

Sla het bestand op. Klik *op Foutopsporing* *en start Foutopsporing opnieuw* op of klik op de werkbalk *Foutopsporing*opnieuw starten op de knop *Foutopsporing opnieuw starten.*

![](media/common/debug-action-refresh.png)

Open uw service in een browser en zie dat uw bijgewerkte bericht wordt weergegeven.

In plaats van elke keer dat codebewerkingen worden uitgevoerd, hercompileert en herschikt een nieuwe containerafbeelding, wordt de code binnen de bestaande container stapsgewijs opnieuw gecompileerd om een snellere lus voor bewerken/foutopsporing te bieden.

## <a name="clean-up-your-azure-resources"></a>Uw Azure-bronnen opschonen

```azurecli
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Volgende stappen

Ontdek hoe Azure Dev Spaces u helpt complexere toepassingen te ontwikkelen voor meerdere containers en hoe u de samenwerking vereenvoudigen door te werken met verschillende versies of branches van uw code in verschillende ruimten. 

> [!div class="nextstepaction"]
> [Werken met meerdere containers en teamontwikkeling](multi-service-netcore.md)


[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
