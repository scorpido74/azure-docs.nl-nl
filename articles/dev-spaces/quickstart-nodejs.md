---
title: 'Debug en herhalen op Kubernetes: Visual Studio Code & Node.js'
services: azure-dev-spaces
ms.date: 07/08/2019
ms.topic: quickstart
description: Met deze quickstart u Azure Dev Spaces en Visual Studio Code gebruiken om een Node.js-toepassing op Azure Kubernetes Service te debuggen en snel te herhalen
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers, Helm, service mesh, service mesh routing, kubectl, k8s
manager: gwallace
ms.openlocfilehash: 18171a2f8d13bfcf3df76b1453c39c59cab89d7c
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80240200"
---
# <a name="quickstart-debug-and-iterate-on-kubernetes-with-visual-studio-code-and-nodejs---azure-dev-spaces"></a>Snelstart: Debugen en herhalen op Kubernetes met Visual Studio Code en Node.js - Azure Dev Spaces

In deze quickstart stelt u Azure Dev Spaces in met een beheerd Kubernetes-cluster en gebruikt u een Node.js-app in Visual Studio Code om iteratief code in containers te ontwikkelen en te debuggen. Met Azure Dev Spaces u alle onderdelen van uw toepassing debuggen en testen in Azure Kubernetes Service (AKS) met minimale installatie van ontwikkelingsmachines. 

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Maak gratis een account aan.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) 
- [Nieuwste versie van Node.js](https://nodejs.org/download/).
- [Visual Studio Code](https://code.visualstudio.com/download).
- De [Azure Dev Spaces-extensie](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) voor Visual Studio Code.
- [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).
- [Git.](https://www.git-scm.com/downloads)

## <a name="create-an-azure-kubernetes-service-cluster"></a>Een Azure Kubernetes Service-cluster maken

U moet een AKS-cluster maken in een [ondersteund gebied.][supported-regions] Met de volgende opdrachten wordt een resourcegroep met de naam *MyResourceGroup* en een AKS-cluster met de naam *MyAKS*gemaakt.

```azurecli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Azure Dev-ruimten inschakelen op uw AKS-cluster

Gebruik `use-dev-spaces` de opdracht om Dev Spaces in te schakelen op uw AKS-cluster en volg de aanwijzingen. Met de volgende opdracht schakelt U Spaties in op het *MyAKS-cluster* in de groep *MyResourceGroup* in en wordt een standaarddev-ruimte gemaakt. *default*

> [!NOTE]
> De `use-dev-spaces` opdracht installeert ook de Azure Dev Spaces CLI als deze nog niet is geïnstalleerd. U de Azure Dev Spaces CLI niet installeren in de Azure Cloud Shell.

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

Open Visual Studio Code, selecteer **Bestand** en **open,** navigeer naar de *dev-spaces/samples/nodejs/getting-started/webfrontend* directory en selecteer **Openen**.

Je hebt nu het *webfrontend* project geopend in Visual Studio Code. Als u de toepassing wilt uitvoeren in uw ontwikkelaarsruimte, genereert u de grafiekelementen Docker en Helm met de extensie Azure Dev Spaces in het opdrachtpalet.

Als u het opdrachtpalet wilt openen in Visual Studio Code, selecteert u **Weergave** vervolgens **Opdrachtpalet**. Begin `Azure Dev Spaces` met typen en selecteer **Azure Dev Spaces: Configuratiebestanden voorbereiden voor Azure Dev Spaces**.

![Configuratiebestanden voorbereiden voor Azure Dev Spaces](./media/common/command-palette.png)

Wanneer Visual Studio Code u ook vraagt om `Yes` uw openbare eindpunt te configureren, kiest u om een openbaar eindpunt in te schakelen.

![Openbaar eindpunt selecteren](media/common/select-public-endpoint.png)

Met deze opdracht bereidt u uw project voor om in Azure Dev Spaces uit te voeren door een Dockerfile- en Helm-diagram te genereren. Het genereert ook een *.vscode* directory met debugging configuratie aan de basis van uw project.

> [!TIP]
> De [dockerfile- en helmdiagram](how-dev-spaces-works-prep.md#prepare-your-code) voor uw project wordt gebruikt door Azure Dev Spaces om uw code te bouwen en uit te voeren, maar u deze bestanden wijzigen als u de manier waarop het project wordt gebouwd en uitgevoerd wilt wijzigen.

## <a name="build-and-run-code-in-kubernetes-from-visual-studio-code"></a>Code bouwen en uitvoeren in Kubernetes vanuit Visual Studio Code

Selecteer het pictogram **Foutopsporing** aan de linkerkant en selecteer **Server starten (AZDS)** bovenaan.

![Server starten](media/get-started-node/debug-configuration-nodejs.png)

Met deze opdracht worden uw service gebouwd en uitgevoerd in Azure Dev Spaces. Het **venster Terminal** onderaan toont de buildoutput en URL's voor uw service met Azure Dev Spaces. De **Foutopsporingsconsole** toont de logboekuitvoer.

> [!Note]
> Als u geen opdrachten voor Azure Dev Spaces ziet in het **opdrachtpalet,** controleert u of u de [visual studiocode-extensie voor Azure Dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds)hebt geïnstalleerd. Controleer ook of u de *map dev-spaces/samples/nodejs/Getting-started/webfrontend* hebt geopend in Visual Studio Code.

U de service zien die wordt uitgevoerd door de openbare URL te openen.

Selecteer **Foutopsporing** **en stop foutopsporing** om de foutopsporing te stoppen.

## <a name="update-code"></a>Code bijwerken

Als u een bijgewerkte versie van uw service wilt implementeren, u elk bestand in uw project bijwerken en **Startserver**opnieuw uitvoeren. Bijvoorbeeld:

1. Als uw toepassing nog steeds actief is, selecteert u **Foutopsporing** **en stopt u Foutopsporing** om deze te stoppen.
1. [Regel 13 `server.js` bijwerken in:](https://github.com/Azure/dev-spaces/blob/master/samples/nodejs/getting-started/webfrontend/server.js#L13)
    
    ```javascript
        res.send('Hello from webfrontend in Azure');
    ```

1. Sla uw wijzigingen op.
1. Startserver **Launch Server**opnieuw uitvoeren .
1. Navigeer naar uw hardloopservice en observeer uw wijzigingen.
1. Selecteer **Foutopsporing** **en stop foutopsporing** om uw toepassing te stoppen.

## <a name="setting-and-using-breakpoints-for-debugging"></a>Breekpunten instellen en gebruiken voor foutopsporing

Start uw service met **Launch Server (AZDS).**

Navigeer terug naar de Explorer-weergave door **Weergave** te selecteren en vervolgens **Explorer**. Open *server.js* en klik ergens op regel 13 om de cursor daar te plaatsen. Als u een breekpunt wilt instellen, drukt u op **F9** of selecteert **u Foutopsporing** en schakelt **u Breekpunt in**.

Open uw service in een browser en merk dat er geen bericht wordt weergegeven. Ga terug naar Visual Studio Code en observeer lijn 13 wordt gemarkeerd. Het breekpunt dat u instelt, heeft de service op lijn 13 onderbroken. Als u de service wilt hervatten, drukt u op **F5** of selecteert u **Foutopsporing** **en gaat u verder**. Keer terug naar uw browser en merk dat het bericht nu wordt weergegeven.

Tijdens het uitvoeren van uw service in Kubernetes met een debugger gekoppeld, hebt u volledige toegang tot foutopsporingsinformatie zoals de callstack, lokale variabelen en uitzonderingsinformatie.

Verwijder het breekpunt door de cursor op lijn 13 in *server.js* te zetten en op **F9**te drukken.

Selecteer **Foutopsporing** **en stop foutopsporing** om de foutopsporing te stoppen.

## <a name="update-code-from-visual-studio-code"></a>Code bijwerken vanuit Visual Studio Code

Wijzig de foutopsporingsmodus **in Koppelen aan een server (AZDS)** en start de service:

![](media/get-started-node/attach-nodejs.png)

Met deze opdracht worden uw service gebouwd en uitgevoerd in Azure Dev Spaces. Het start ook een [nodemon](https://nodemon.io) proces in de container van uw service en hecht VS Code aan. Het *nodemon-proces* zorgt voor een automatische herstart wanneer broncodewijzigingen worden aangebracht, waardoor een snellere ontwikkeling van de binnenlus mogelijk is die vergelijkbaar is met het ontwikkelen op uw lokale machine.

Nadat de service is gestart, navigeert u ernaar met uw browser en communiceert u ermee.

Terwijl de service wordt uitgevoerd, gaat u terug naar VS Code en updateregel 13 in *server.js*. Bijvoorbeeld:
```javascript
    res.send('Hello from webfrontend in Azure while debugging!');
```

Sla het bestand op en keer terug naar uw service in een browser. Communiceer met de service en zie dat uw bijgewerkte bericht wordt weergegeven.

Tijdens het uitvoeren van *nodemon*wordt het nodeproces automatisch opnieuw gestart zodra er codewijzigingen worden gedetecteerd. Dit automatische herstartproces is vergelijkbaar met de ervaring van het bewerken en opnieuw opstarten van uw service op uw lokale machine, waardoor een innerlijke lus ontwikkelingservaring wordt geboden.

## <a name="clean-up-your-azure-resources"></a>Uw Azure-bronnen opschonen

```azurecli
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Volgende stappen

Ontdek hoe Azure Dev Spaces u helpt complexere toepassingen te ontwikkelen voor meerdere containers en hoe u de samenwerking vereenvoudigen door te werken met verschillende versies of branches van uw code in verschillende ruimten.

> [!div class="nextstepaction"]
> [Werken met meerdere containers en teamontwikkeling](multi-service-nodejs.md)


[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
