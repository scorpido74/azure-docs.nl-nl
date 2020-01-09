---
title: 'Fouten opsporen en herhalen op Kubernetes: Visual Studio code & node. js'
services: azure-dev-spaces
ms.date: 07/08/2019
ms.topic: quickstart
description: In deze Quick start ziet u hoe u Azure dev Spaces en Visual Studio code kunt gebruiken om fouten op te sporen en snel een node. js-toepassing te herhalen in azure Kubernetes service
keywords: Docker, Kubernetes, azure, AKS, Azure Kubernetes service, containers, helm, service-net, service mesh routing, kubectl, K8S
manager: gwallace
ms.openlocfilehash: 1b2101c5e7a59105b2c1da876e9a66294ce3a021
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75438186"
---
# <a name="quickstart-debug-and-iterate-on-kubernetes-with-visual-studio-code-and-nodejs---azure-dev-spaces"></a>Quick Start: fouten opsporen en herhalen op Kubernetes met Visual Studio code en node. js-Azure dev Spaces

In deze handleiding leert u het volgende:

- Azure Dev Spaces instellen met een beheerd Kubernetes-cluster in Azure.
- Ontwikkel iteratieve code in containers met Visual Studio code.
- Fout opsporing voor de code in uw dev-ruimte vanuit Visual Studio code.

Met Azure dev Spaces kunt u ook fouten opsporen en herhalen met:
- [Java-en Visual Studio code](quickstart-java.md)
- [.NET core en Visual Studio code](quickstart-netcore.md)
- [.NET core en Visual Studio](quickstart-netcore-visualstudio.md)

## <a name="prerequisites"></a>Vereisten

- Een Azure-abonnement. Als u geen Azure-abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free) maken.
- [Visual Studio code is geïnstalleerd](https://code.visualstudio.com/download).
- De [Azure dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) -extensie voor Visual Studio code is geïnstalleerd.
- [Azure CLI geïnstalleerd](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="create-an-azure-kubernetes-service-cluster"></a>Een Azure Kubernetes service-cluster maken

U moet een AKS-cluster maken in een [ondersteunde regio][supported-regions]. Met de onderstaande opdrachten maakt u een resource groep met de naam *MyResourceGroup* en een AKS-cluster met de naam *MyAKS*.

```cmd
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --disable-rbac --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Azure dev Spaces inschakelen op uw AKS-cluster

Gebruik de `use-dev-spaces` opdracht om ontwikkel ruimten in uw AKS-cluster in te scha kelen en de prompts te volgen. Met de onderstaande opdracht maakt u ontwikkel ruimten in het *MyAKS* -cluster in de groep *MyResourceGroup* en maakt u een *standaard* dev-ruimte.

> [!NOTE]
> De `use-dev-spaces`-opdracht installeert ook de Azure dev Space CLI als deze nog niet is geïnstalleerd. U kunt de CLI voor Azure dev Spaces niet installeren in de Azure Cloud Shell.

```cmd
$ az aks use-dev-spaces -g MyResourceGroup -n MyAKS

'An Azure Dev Spaces Controller' will be created that targets resource 'MyAKS' in resource group 'MyResourceGroup'. Continue? (y/N): y

Creating and selecting Azure Dev Spaces Controller 'MyAKS' in resource group 'MyResourceGroup' that targets resource 'MyAKS' in resource group 'MyResourceGroup'...2m 24s

Select a dev space or Kubernetes namespace to use as a dev space.
 [1] default
Type a number or a new name: 1

Kubernetes namespace 'default' will be configured as a dev space. This will enable Azure Dev Spaces instrumentation for new workloads in the namespace. Continue? (Y/n): Y

Configuring and selecting dev space 'default'...3s

Managed Kubernetes cluster 'MyAKS' in resource group 'MyResourceGroup' is ready for development in dev space 'default'. Type `azds prep` to prepare a source directory for use with Azure Dev Spaces and `azds up` to run.
```

## <a name="get-sample-application-code"></a>Voorbeeld toepassings code ophalen

In dit artikel gebruikt u de [voorbeeld toepassing Azure dev Spaces](https://github.com/Azure/dev-spaces) om te demonstreren hoe u Azure dev Spaces gebruikt.

Kloon de toepassing vanuit GitHub.

```cmd
git clone https://github.com/Azure/dev-spaces
```

## <a name="prepare-the-sample-application-in-visual-studio-code"></a>De voorbeeld toepassing voorbereiden in Visual Studio code

Open Visual Studio code, klik op *bestand* en vervolgens op *openen...* Navigeer naar de map *dev-Spaces/samples/nodejs/Getting-Started/webfrontend* en klik op *Open*.

U hebt nu het *webfrontend* -project geopend in Visual Studio code. Als u de toepassing in uw ontwikkelaars ruimte wilt uitvoeren, genereert u de helm-grafiek assets met de extensie Azure dev Spaces in het opdracht palet.

Als u het opdracht palet wilt openen in Visual Studio code, klikt u op *weer gave* en vervolgens op *opdracht palet*. Begin met het typen van `Azure Dev Spaces` en klik op `Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces`.

![Configuratie bestanden voorbereiden voor Azure-ontwikkel ruimten](./media/common/command-palette.png)

Wanneer Visual Studio code ook u vraagt om uw open bare eind punt te configureren, kiest u `Yes` om een openbaar eind punt in te scha kelen.

![Openbaar eind punt selecteren](media/common/select-public-endpoint.png)

Met deze opdracht wordt het project voor bereid om te worden uitgevoerd in azure dev Spaces door een Dockerfile-en helm-grafiek te genereren. Er wordt ook een *vscode* -map gegenereerd met de configuratie van fout opsporing in de hoofdmap van uw project.

## <a name="build-and-run-code-in-kubernetes-from-visual-studio-code"></a>Code maken en uitvoeren in Kubernetes vanuit Visual Studio code

Klik aan de linkerkant op het pictogram *fout opsporing* en klik bovenaan op *Start Server (AZDS)* .

![Server starten](media/get-started-node/debug-configuration-nodejs.png)

Met deze opdracht bouwt en voert u uw service uit in azure dev Spaces. In het *Terminal* venster onderaan ziet u de uitvoer en url's van de build voor uw service met Azure dev Spaces. In de *console fout opsporing* wordt de logboek uitvoer weer gegeven.

> [!Note]
> Als er geen Azure dev Space-opdrachten in het *opdracht palet*worden weer gegeven, moet u ervoor zorgen dat u de [Visual Studio code Extension voor Azure dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds)hebt geïnstalleerd. Controleer ook of u de map *dev-Spaces/samples/nodejs/Getting-Started/webfrontend* hebt geopend in Visual Studio code.

U ziet de service die wordt uitgevoerd door de open bare URL te openen.

Klik op *fout* opsporing en vervolgens op *fout opsporing stoppen* om het fout opsporingsprogramma te stoppen.

## <a name="update-code"></a>Code bijwerken

Als u een bijgewerkte versie van uw service wilt implementeren, kunt u elk bestand in het project bijwerken en de *server opnieuw starten*. Bijvoorbeeld:

1. Als uw toepassing nog steeds wordt uitgevoerd, klikt u op *fout* opsporing en *stopt u fout opsporing* .
1. Werk [regel 13 in `server.js`](https://github.com/Azure/dev-spaces/blob/master/samples/nodejs/getting-started/webfrontend/server.js#L13) bij naar:
    
    ```javascript
        res.send('Hello from webfrontend in Azure');
    ```

1. Sla uw wijzigingen op.
1. *Start Server*opnieuw op.
1. Navigeer naar uw actieve service en Bekijk uw wijzigingen.
1. Klik op *fout* opsporing en vervolgens op *fout opsporing stoppen* om uw toepassing te stoppen.

## <a name="setting-and-using-breakpoints-for-debugging"></a>Onderbrekings punten instellen en gebruiken voor fout opsporing

Start uw service met behulp van *Start Server (AZDS)* .

Ga terug naar de weer gave van de *Verkenner* door te klikken op *weer gave* en vervolgens op *Explorer*. Open `server.js` en klik ergens op regel 13 om de cursor daar te plaatsen. Stel een onderbrekings punt in op *F9* of klik op *debug* en vervolgens op *onderbrekings punt*.

Open uw service in een browser en Let op dat er geen bericht wordt weer gegeven. Ga terug naar Visual Studio code en kijk regel 13 is gemarkeerd. Het onderbrekings punt dat u instelt, heeft de service op regel 13 onderbroken. Als u de service wilt hervatten, druk op *F5* of klik op *debug* en vervolgens op *door gaan*. Ga terug naar uw browser en u ziet dat het bericht nu wordt weer gegeven.

Tijdens het uitvoeren van uw service in Kubernetes met een fout opsporingsprogramma, hebt u volledige toegang tot fout opsporingsgegevens, zoals de aanroep stack, lokale variabelen en uitzonderings gegevens.

Verwijder het onderbrekings punt door de cursor op regel 13 in `server.js` te plaatsen en op *F9 te drukken*.

Klik op *fout* opsporing en vervolgens op *fout opsporing stoppen* om het fout opsporingsprogramma te stoppen.

## <a name="update-code-from-visual-studio-code"></a>Code bijwerken vanuit Visual Studio code

Wijzig de foutopsporingsmodus om *aan een server te koppelen (AZDS)* en start de service:

![](media/get-started-node/attach-nodejs.png)

Met deze opdracht bouwt en voert u uw service uit in azure dev Spaces. Er wordt ook een niet- [demo](https://nodemon.io) proces in de container van de service gestart en er worden VS-code aan gekoppeld. Het niet- *demo* proces zorgt ervoor dat automatisch opnieuw wordt opgestart wanneer er wijzigingen in de bron code worden aangebracht, waardoor de binnenste lus sneller kan worden ontwikkeld, vergelijkbaar met het ontwikkelen op uw lokale machine.

Nadat de service is gestart, navigeert u naar deze met behulp van uw browser en gaat u ermee werken.

Wanneer de service wordt uitgevoerd, keert u terug naar VS code en regel 13 bijwerken in `server.js`. Bijvoorbeeld:
```javascript
    res.send('Hello from webfrontend in Azure while debugging!');
```

Sla het bestand op en ga terug naar uw service in een browser. Interactie met de service en u ziet dat er een bijgewerkt bericht wordt weer gegeven.

Tijdens het uitvoeren van geen enkele *demonstratie*wordt het knooppunt proces automatisch opnieuw opgestart zodra er code wijzigingen worden gedetecteerd. Dit proces voor automatisch opnieuw opstarten is vergelijkbaar met de ervaring met het bewerken en opnieuw starten van uw service op uw lokale computer, zodat u een binnenste lus kunt ontwikkelen.

## <a name="clean-up-your-azure-resources"></a>Uw Azure-resources opschonen

```cmd
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over hoe Azure dev Spaces u helpt om complexere toepassingen te ontwikkelen in meerdere containers en hoe u samenwerkings ontwikkeling kunt vereenvoudigen door te werken met verschillende versies of vertakkingen van uw code in verschillende ruimten.

> [!div class="nextstepaction"]
> [Werken met meerdere containers en teamontwikkeling](multi-service-nodejs.md)


[supported-regions]: about.md#supported-regions-and-configurations
