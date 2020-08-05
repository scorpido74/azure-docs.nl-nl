---
title: 'Fouten opsporen en herhalen op Kubernetes: Visual Studio Code en .NET Core installeren'
services: azure-dev-spaces
ms.date: 07/08/2019
ms.topic: quickstart
description: In deze quickstart ziet u hoe u Azure Dev Spaces en Visual Studio Code gebruikt om fouten op te sporen en snel een .NET Core-toepassing te herhalen in Azure Kubernetes Service
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers, Helm, servicemesh, servicemeshroutering, kubectl, k8s
manager: gwallace
ms.openlocfilehash: 104e812cc9e792ce71f263085ba1a3474cc2226b
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87002774"
---
# <a name="quickstart-debug-and-iterate-on-kubernetes-visual-studio-code-and-net-core---azure-dev-spaces"></a>Quickstart: Fouten opsporen en herhalen op Kubernetes: Visual Studio Code en .NET Core - Azure Dev Spaces

In deze handleiding leert u het volgende:

- Azure Dev Spaces instellen met een beheerd Kubernetes-cluster in Azure.
- Iteratief code ontwikkelen in containers met Visual Studio Code.
- Spoor fouten in de code van uw ontwikkelomgeving op met Visual Studio Code.

Met Azure Dev Spaces kunt u ook fouten opsporen en herhalen met:
- [Java en Visual Studio Code](quickstart-java.md)
- [Node.js en Microsoft Visual Studio Code](quickstart-nodejs.md)
- [.NET Core en Visual Studio](quickstart-netcore-visualstudio.md)

## <a name="prerequisites"></a>Vereisten

- Een Azure-abonnement. Als u nog geen account hebt, kunt u [een gratis account aanmaken](https://azure.microsoft.com/free).
- [Visual Studio Code geïnstalleerd](https://code.visualstudio.com/download).
- De extensies [Azure Dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) en [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) voor Visual Studio Code geïnstalleerd.
- [Azure CLI geïnstalleerd](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="create-an-azure-kubernetes-service-cluster"></a>Een upgrade maken van een Azure Kubernetes Service-cluster

U moet een AKS-cluster maken in een [ondersteunde regio][supported-regions]. Met de onderstaande opdrachten maakt u een resourcegroep met de naam *MyResourceGroup* en een AKS-cluster met de naam *MyAKS*.

```azurecli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Azure Dev Spaces instellen op uw AKS-cluster inschakelen

Gebruik de opdracht `use-dev-spaces` om Dev Spaces in te schakelen voor uw AKS-cluster en volg de prompts. Met de onderstaande opdracht schakelt u Dev Spaces in voor het cluster *MyAKS* in de groep *MyResourceGroup* en maakt u een *standaard* dev-ruimte.

> [!NOTE]
> Met de opdracht `use-dev-spaces` wordt ook de CLI voor Azure Dev Spaces geïnstalleerd als die nog niet is geïnstalleerd. U kunt de CLI voor Azure Dev Spaces niet installeren in Azure Cloud Shell.

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

In dit artikel gebruikt u de [voorbeeldtoepassing van Azure Dev Spaces](https://github.com/Azure/dev-spaces) om het gebruiken van Azure Dev Spaces te demonstreren.

Kloon de toepassing vanuit GitHub.

```cmd
git clone https://github.com/Azure/dev-spaces
```

## <a name="prepare-the-sample-application-in-visual-studio-code"></a>De voorbeeldtoepassing voorbereiden in Visual Studio

Open Visual Studio Code, klik op *Bestand* en *Openen...* , navigeer naar de map *dev-spaces/samples/dotnetcore/getting-started/webfrontend* en klik op *Openen*.

U hebt nu het project *webfrontend* geopend in Visual Studio Code. Als u de toepassing in uw ontwikkelaarsruimte wilt uitvoeren, genereert u de Docker- en Helm-grafiekassets met behulp van de extensie Azure Dev Spaces in het Opdrachtpalet.

Open het opdrachtpalet in Visual Studio Code door op *Beeld* en vervolgens op *Opdrachtpalet* te klikken. Begin met het typen van `Azure Dev Spaces` en klik op `Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces`.

![Configuratiebestanden voorbereiden voor Azure Dev Spaces](./media/common/command-palette.png)

Wanneer Visual Studio Code u ook vraagt uw openbare eindpunt te definiëren, kiest u `Yes` om een openbaar eindpunt in te schakelen.

![Openbaar eindpunt selecteren](media/common/select-public-endpoint.png)

Met deze opdracht wordt het project voorbereid om te worden uitgevoerd in Azure Dev Spaces door een Dockerfile en Helm-grafiek te genereren. Ook wordt er in de hoofdmap van uw project een *.vscode*-map gegenereerd met de configuratie voor foutopsporing.

> [!TIP]
> De [Dockerfile en Helm-grafiek](how-dev-spaces-works-prep.md#prepare-your-code) voor uw project worden gebruikt in Azure Dev Spaces om uw code te bouwen en uit te voeren, maar u kunt deze bestanden wijzigen als u wilt wijzigen hoe het project wordt gebouwd en uitgevoerd.

## <a name="build-and-run-code-in-kubernetes-from-visual-studio-code"></a>Code compileren en uitvoeren in Kubernetes vanuit Visual Studio Code

Klik op het pictogram *Fouten opsporen* aan de linkerkant en klik bovenaan op *.NET Core Launch (AZDS)* .

![De schermopname is van de linkerbovenhoek van het venster van Visual Studio Code. Het pictogram voor foutopsporing is gemarkeerd, het linkerdeelvenster heeft de titel 'DEBUG' (fouten opsporen) en een vervolgkeuzelijst rechts van de titel toont 'dot NET Core Launch (A Z D S).](media/get-started-netcore/debug-configuration.png)

Met deze opdracht wordt uw service in Azure Dev Spaces gecompileerd en uitgevoerd in de foutopsporingsmodus. In het venster *Terminal* onderaan ziet u de uitvoer van de build en de URL's voor uw service die wordt uitgevoerd in Azure Dev Spaces. In de *Console voor foutopsporing* wordt de logboekuitvoer weergegeven.

> [!Note]
> Als u geen Azure Dev Spaces-opdrachten ziet in het *Opdrachtenpalet*, controleert u of u de [Visual Studio Code-extensie voor Azure Dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) hebt geïnstalleerd. Controleer ook of u de map *dev-spaces/samples/dotnetcore/getting-started/webfrontend* hebt geopend in Visual Studio Code.

U kunt zien dat de service wordt uitgevoerd door de openbare URL te openen.

> [!Note]
> In eerste instantie kan de openbare URL een *Ongeldige gateway*-fout weergeven. Wacht een paar seconden en vernieuw de webpagina, en u zou moeten zien dat uw service wordt uitgevoerd.

Klik op *Fouten opsporen* en vervolgens op *Foutopsporing stoppen* om het foutopsporingsprogramma te stoppen.

## <a name="update-code"></a>Code bijwerken

Als u een bijgewerkte versie van uw service wilt implementeren, kunt u een bestand in uw project bijwerken en *.NET Core Launch (AZDS)* nogmaals uitvoeren. Bijvoorbeeld:

1. Als uw toepassing nog wordt uitgevoerd, klikt u op *Fouten opsporen* en vervolgens op *Foutopsporing stoppen* om deze te stoppen.
1. Wijzig [regel 22 in `Controllers/HomeController.cs`](https://github.com/Azure/dev-spaces/blob/master/samples/dotnetcore/getting-started/webfrontend/Controllers/HomeController.cs#L22) in:
    
    ```csharp
    ViewData["Message"] = "Your application description page in Azure.";
    ```

1. Sla uw wijzigingen op.
1. Voer *.NET Core Launch (AZDS)* opnieuw uit.
1. Navigeer naar uw actieve service en klik op *Info*.
1. Bekijk uw wijzigingen.
1. Klik op *Fouten opsporen* en vervolgens op *Foutopsporing stoppen* om uw toepassing te stoppen.

## <a name="setting-and-using-breakpoints-for-debugging"></a>Onderbrekingspunten instellen en gebruiken voor fouten opsporen

Start uw service in de foutopsporingsmodus met behulp van *.NET Core Launch (AZDS)* .

Ga terug naar de *Explorer*-weergave door op *Beeld* en vervolgens op *Explorer* te klikken. Open `Controllers/HomeController.cs` en klik ergens op regel 22 om de cursor daar te plaatsen. Als u een onderbrekingspunt wilt instellen, klik op *F9* of *Fouten opsporen* en vervolgens *Onderbrekingspunt inschakelen*.

Open uw service in een browser en let op dat er geen bericht wordt weergegeven. Ga terug naar Visual Studio Code. U ziet dat regel 20 is gemarkeerd. Het onderbrekingspunt dat u hebt ingesteld, heeft de service op regel 20 onderbroken. Als u de service wilt hervatten, drukt u op *F5* of klikt u op *Fouten opsporen* vervolgens *Doorgaan*. Ga terug naar uw browser en u ziet dat het bericht nu wordt weergegeven.

Tijdens het uitvoeren van uw service in Kubernetes met een foutopsporingsprogramma, hebt u volledige toegang tot foutopsporingsgegevens, zoals de aanroepstack, lokale variabelen en uitzonderingsgegevens.

Verwijder het onderbrekingspunt door de cursor op regel 22 in `Controllers/HomeController.cs` te plaatsen en op *F9* te drukken.

## <a name="update-code-from-visual-studio-code"></a>Code bijwerken vanuit Visual Studio Code

Terwijl de service wordt uitgevoerd in de foutopsporingsmodus, werkt u regel 22 in `Controllers/HomeController.cs` bij. Bijvoorbeeld:

```csharp
ViewData["Message"] = "Your application description page in Azure while debugging!";
```

Sla het bestand op. Klik op *Fouten opsporen* en vervolgens op *Foutopsporing opnieuw starten* of klik op de werkbalk *Foutopsporing* op de knop *Foutopsporing opnieuw starten*.

![De werkbalk Foutopsporing is een klein venster bovenaan in het midden van de pagina (vlak onder de paginatitel). Op de knop Opnieuw opstarten wordt een cirkelvormige pijl weergegeven, en de knop is gemarkeerd. De knop wordt aangewezen, en de tekst 'Restart (Ctrl+Shift+F5)' wordt weergegeven.](media/common/debug-action-refresh.png)

Open uw service in een browser. U ziet dat uw bijgewerkte bericht wordt weergegeven.

In plaats van telkens als codewijzigingen zijn aangebracht een nieuwe containerinstallatiekopie opnieuw te bouwen en opnieuw te implementeren, hercompileert Azure Dev Spaces incrementeel code binnen de bestaande container voor een snellere bewerkings-/foutopsporingslus.

## <a name="clean-up-your-azure-resources"></a>Uw Azure-resources opschonen

```azurecli
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Volgende stappen

Ontdek hoe Azure Dev Spaces u kan helpen om complexere toepassingen te ontwikkelen binnen meerdere containers en hoe u ontwikkeling in samenwerkingsverband vereenvoudigt door in verschillende ruimten met verschillende versies of vertakkingen van uw code te werken. 

> [!div class="nextstepaction"]
> [Werken met meerdere containers en teamontwikkeling](multi-service-netcore.md)


[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
