---
title: 'Fouten opsporen en herhalen op Kubernetes: Visual Studio code & node. js'
services: azure-dev-spaces
ms.date: 07/08/2019
ms.topic: quickstart
description: In deze Quick start ziet u hoe u Azure dev Spaces en Visual Studio code kunt gebruiken om fouten op te sporen en snel een node. js-toepassing te herhalen in azure Kubernetes service
keywords: Docker, Kubernetes, azure, AKS, Azure Kubernetes service, containers, helm, service-net, service mesh routing, kubectl, K8S
manager: gwallace
ms.openlocfilehash: cd784ed616b2938f4c57bad47045f0d44ad25a69
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77022525"
---
# <a name="quickstart-debug-and-iterate-on-kubernetes-with-visual-studio-code-and-nodejs---azure-dev-spaces"></a>Quick Start: fouten opsporen en herhalen op Kubernetes met Visual Studio code en node. js-Azure dev Spaces

In deze Snelstartgids stelt u Azure dev Spaces in met een beheerd Kubernetes-cluster en gebruikt u een node. js-app in Visual Studio code om iteratieve code in containers te ontwikkelen en fouten op te sporen. Met Azure dev Spaces kunt u fouten opsporen en testen van alle onderdelen van uw toepassing in azure Kubernetes service (AKS) met minimale ontwikkel machine-instellingen. 

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Maak gratis een account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). 
- [Meest recente versie van node. js](https://nodejs.org/download/).
- [Visual Studio Code](https://code.visualstudio.com/download).
- De [Azure dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) -extensie voor Visual Studio code.
- [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).
- [Git](https://www.git-scm.com/downloads).

## <a name="create-an-azure-kubernetes-service-cluster"></a>Een Azure Kubernetes service-cluster maken

U moet een AKS-cluster maken in een [ondersteunde regio][supported-regions]. Met de volgende opdrachten maakt u een resource groep met de naam *MyResourceGroup* en een AKS-cluster met de naam *MyAKS*.

```cmd
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --disable-rbac --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Azure dev Spaces inschakelen op uw AKS-cluster

Gebruik de `use-dev-spaces` opdracht om ontwikkel ruimten in uw AKS-cluster in te scha kelen en de prompts te volgen. Met de volgende opdracht worden ontwikkel ruimten in het *MyAKS* -cluster in de *MyResourceGroup* -groep ingeschakeld en wordt er een *standaard* -ontwikkel ruimte gemaakt.

> [!NOTE]
> De `use-dev-spaces`-opdracht installeert ook de Azure dev Space CLI als deze nog niet is geïnstalleerd. Het is niet mogelijk om de Azure dev Spaces CLI te installeren in de Azure Cloud Shell.

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

Open Visual Studio code, selecteer **bestand** en vervolgens **openen**, navigeer naar de map *dev-Spaces/samples/nodejs/Getting-Started/webfrontend* en selecteer **openen**.

U hebt nu het *webfrontend* -project geopend in Visual Studio code. Als u de toepassing in uw ontwikkelaars ruimte wilt uitvoeren, genereert u de helm-grafiek assets met de extensie Azure dev Spaces in het opdracht palet.

Selecteer **weer gave** en **opdracht palet**om het opdracht palet te openen in Visual Studio code. Begin met het typen van `Azure Dev Spaces` en selecteer **Azure dev Spaces: configuratie bestanden voorbereiden voor Azure dev Spaces**.

![Configuratie bestanden voorbereiden voor Azure-ontwikkel ruimten](./media/common/command-palette.png)

Wanneer Visual Studio code ook u vraagt om uw open bare eind punt te configureren, kiest u `Yes` om een openbaar eind punt in te scha kelen.

![Openbaar eind punt selecteren](media/common/select-public-endpoint.png)

Met deze opdracht wordt het project voor bereid om te worden uitgevoerd in azure dev Spaces door een Dockerfile-en helm-grafiek te genereren. Er wordt ook een *vscode* -map gegenereerd met de configuratie van fout opsporing in de hoofdmap van uw project.

> [!TIP]
> De [Dockerfile-en helm-grafiek](how-dev-spaces-works.md#prepare-your-code) voor uw project wordt gebruikt door Azure dev Spaces om uw code te bouwen en uit te voeren, maar u kunt deze bestanden wijzigen als u wilt wijzigen hoe het project wordt gemaakt en uitgevoerd.

## <a name="build-and-run-code-in-kubernetes-from-visual-studio-code"></a>Code maken en uitvoeren in Kubernetes vanuit Visual Studio code

Selecteer het pictogram voor **fout opsporing** aan de linkerkant en selecteer bovenaan **starten server (AZDS)** .

![Server starten](media/get-started-node/debug-configuration-nodejs.png)

Met deze opdracht bouwt en voert u uw service uit in azure dev Spaces. In het **Terminal** venster onderaan ziet u de uitvoer en url's van de build voor uw service met Azure dev Spaces. In de **console fout opsporing** wordt de logboek uitvoer weer gegeven.

> [!Note]
> Als er geen Azure dev Space-opdrachten in het **opdracht palet**worden weer gegeven, moet u ervoor zorgen dat u de [Visual Studio code Extension voor Azure dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds)hebt geïnstalleerd. Controleer ook of u de map *dev-Spaces/samples/nodejs/Getting-Started/webfrontend* hebt geopend in Visual Studio code.

U ziet de service die wordt uitgevoerd door de open bare URL te openen.

Selecteer **fout** opsporing en **Stop fout opsporing** om het fout opsporingsprogramma te stoppen.

## <a name="update-code"></a>Code bijwerken

Als u een bijgewerkte versie van uw service wilt implementeren, kunt u elk bestand in het project bijwerken en de **server opnieuw starten**. Bijvoorbeeld:

1. Als uw toepassing nog steeds wordt uitgevoerd, selecteert u **fout** opsporing en **stopt u fout opsporing** .
1. Werk [regel 13 in `server.js`](https://github.com/Azure/dev-spaces/blob/master/samples/nodejs/getting-started/webfrontend/server.js#L13) bij naar:
    
    ```javascript
        res.send('Hello from webfrontend in Azure');
    ```

1. Sla uw wijzigingen op.
1. **Start Server**opnieuw op.
1. Navigeer naar uw actieve service en Bekijk uw wijzigingen.
1. Selecteer **fout opsporing** en **Stop fout opsporing** om uw toepassing te stoppen.

## <a name="setting-and-using-breakpoints-for-debugging"></a>Onderbrekings punten instellen en gebruiken voor fout opsporing

Start uw service met behulp van **Start Server (AZDS)** .

Ga terug naar de weer gave van de Verkenner door **weer gave** en vervolgens **Verkenner**te selecteren. Open *server. js* en klik ergens op regel 13 om de cursor daar te plaatsen. Als u een onderbrekings punt wilt instellen, drukt u op **F9** of selecteert u **fout opsporing** in-en **uitschakelen**.

Open uw service in een browser en Let op dat er geen bericht wordt weer gegeven. Ga terug naar Visual Studio code en kijk regel 13 is gemarkeerd. Het onderbrekings punt dat u instelt, heeft de service op regel 13 onderbroken. Als u de service wilt hervatten, drukt u op **F5** of selecteert u **fout opsporing** en vervolgens **door gaan**. Ga terug naar uw browser en u ziet dat het bericht nu wordt weer gegeven.

Tijdens het uitvoeren van uw service in Kubernetes met een fout opsporingsprogramma, hebt u volledige toegang tot fout opsporingsgegevens, zoals de aanroep stack, lokale variabelen en uitzonderings gegevens.

Verwijder het onderbrekings punt door de cursor op regel 13 in *server. js* te plaatsen en op **F9**te drukken.

Selecteer **fout** opsporing en **Stop fout opsporing** om het fout opsporingsprogramma te stoppen.

## <a name="update-code-from-visual-studio-code"></a>Code bijwerken vanuit Visual Studio code

Wijzig de foutopsporingsmodus om **aan een server te koppelen (AZDS)** en start de service:

![](media/get-started-node/attach-nodejs.png)

Met deze opdracht bouwt en voert u uw service uit in azure dev Spaces. Er wordt ook een niet- [demo](https://nodemon.io) proces in de container van de service gestart en er worden VS-code aan gekoppeld. Het niet- *demo* proces zorgt ervoor dat automatisch opnieuw wordt opgestart wanneer er wijzigingen in de bron code worden aangebracht, waardoor de binnenste lus sneller kan worden ontwikkeld, vergelijkbaar met het ontwikkelen op uw lokale machine.

Nadat de service is gestart, navigeert u naar deze met behulp van uw browser en gaat u ermee werken.

Wanneer de service wordt uitgevoerd, gaat u terug naar VS code en regel 13 bijwerken in *server. js*. Bijvoorbeeld:
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


[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
