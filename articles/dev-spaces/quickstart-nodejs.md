---
title: 'Fouten opsporen en herhalen op Kubernetes: Visual Studio Code en Node.js installeren'
services: azure-dev-spaces
ms.date: 07/08/2019
ms.topic: quickstart
description: In deze quickstart ziet u hoe u Azure Dev Spaces en Visual Studio Code gebruikt om fouten op te sporen en snel een Node.js-toepassing te herhalen in Azure Kubernetes Service
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers, Helm, servicemesh, servicemeshroutering, kubectl, k8s
manager: gwallace
ms.openlocfilehash: 370d49b5287a4d5beafc8c2cc5d8f48c269715db
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87006941"
---
# <a name="quickstart-debug-and-iterate-on-kubernetes-with-visual-studio-code-and-nodejs---azure-dev-spaces"></a>Quickstart: Fouten opsporen en herhalen op Kubernetes met Visual Studio Code en Node.js - Azure Dev Spaces

In deze quickstart stelt u Azure Dev Spaces in met een beheerd Kubernetes-cluster en gebruikt u een Node.js-app in Visual Studio Code om iteratief code in containers te ontwikkelen en fouten in code in containers op te sporen. Met Azure Dev Spaces kunt u ook fouten opsporen en alle onderdelen van uw toepassing in Azure Kubernetes Service (AKS) testen met minimale instellingen van de ontwikkelcomputer. 

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) 
- [Nieuwste versie van Node.js](https://nodejs.org/download/).
- [Visual Studio Code](https://code.visualstudio.com/download).
- De [Azure Dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds)-extensie voor Visual Studio Code.
- [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).
- [Git](https://www.git-scm.com/downloads).

## <a name="create-an-azure-kubernetes-service-cluster"></a>Een upgrade maken van een Azure Kubernetes Service-cluster

U moet een AKS-cluster maken in een [ondersteunde regio][supported-regions]. Met de volgende opdrachten maakt u een resourcegroep met de naam *MyResourceGroup* en een AKS-cluster met de naam *MyAKS*.

```azurecli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Azure Dev Spaces instellen op uw AKS-cluster inschakelen

Gebruik de opdracht `use-dev-spaces` om Dev Spaces in te schakelen voor uw AKS-cluster en volg de prompts. Met de volgende opdracht schakelt u Dev Spaces in voor het cluster *MyAKS* in de groep *MyResourceGroup* en maakt u een *standaard* dev-ruimte.

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

Open Visual Studio Code, selecteer achtereenvolgens **Bestand** en **Openen**, navigeer naar de map *dev-spaces/samples/nodejs/getting-started/webfrontend* en selecteer **Openen**.

U hebt nu het project *webfrontend* geopend in Visual Studio Code. Als u de toepassing in uw ontwikkelaarsruimte wilt uitvoeren, genereert u de Docker- en Helm-grafiekassets met behulp van de extensie Azure Dev Spaces in het Opdrachtpalet.

Open het opdrachtpalet in Visual Studio Code door achtereenvolgens **Beeld** en **Opdrachtpalet** te selecteren. Begin met het typen van `Azure Dev Spaces` en selecteer **Azure Dev Spaces: Configuratiebestanden voorbereiden voor Azure Dev Spaces**.

![Configuratiebestanden voorbereiden voor Azure Dev Spaces](./media/common/command-palette.png)

Wanneer Visual Studio Code u ook vraagt uw openbare eindpunt te definiëren, kiest u `Yes` om een openbaar eindpunt in te schakelen.

![Openbaar eindpunt selecteren](media/common/select-public-endpoint.png)

Met deze opdracht wordt het project voorbereid om te worden uitgevoerd in Azure Dev Spaces door een Dockerfile en Helm-grafiek te genereren. Ook wordt er in de hoofdmap van uw project een *.vscode*-map gegenereerd met de configuratie voor foutopsporing.

> [!TIP]
> De [Dockerfile en Helm-grafiek](how-dev-spaces-works-prep.md#prepare-your-code) voor uw project worden gebruikt in Azure Dev Spaces om uw code te bouwen en uit te voeren, maar u kunt deze bestanden wijzigen als u wilt wijzigen hoe het project wordt gebouwd en uitgevoerd.

## <a name="build-and-run-code-in-kubernetes-from-visual-studio-code"></a>Code compileren en uitvoeren in Kubernetes vanuit Visual Studio Code

Selecteer het pictogram **Fouten opsporen** aan de linkerkant en selecteer **Server starten (AZDS)** bovenaan.

![Server starten](media/get-started-node/debug-configuration-nodejs.png)

Met deze opdracht wordt uw service in Azure Dev Spaces gecompileerd en uitgevoerd. In het venster **Terminal** onderaan ziet u de uitvoer van de build en de URL's voor uw service die wordt uitgevoerd in Azure Dev Spaces. In de **Console voor foutopsporing** wordt de logboekuitvoer weergegeven.

> [!Note]
> Als u geen Azure Dev Spaces-opdrachten ziet in het **Opdrachtenpalet**, controleert u of u de [Visual Studio Code-extensie voor Azure Dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) hebt geïnstalleerd. Controleer ook of u de map *dev-spaces/samples/nodejs/getting-started/webfrontend* hebt geopend in Visual Studio Code.

U kunt zien dat de service wordt uitgevoerd door de openbare URL te openen.

Selecteer **Fouten opsporen** en selecteer vervolgens **Foutopsporing stoppen** om het foutopsporingsprogramma te stoppen.

## <a name="update-code"></a>Code bijwerken

Als u een bijgewerkte versie van uw service wilt implementeren, kunt u een bestand in uw project bijwerken en **Server starten** nogmaals uitvoeren. Bijvoorbeeld:

1. Als uw toepassing nog wordt uitgevoerd, selecteert u **Fouten opsporen** en vervolgens **Foutopsporing stoppen** om deze te stoppen.
1. Wijzig [regel 13 in `server.js`](https://github.com/Azure/dev-spaces/blob/master/samples/nodejs/getting-started/webfrontend/server.js#L13) in:
    
    ```javascript
        res.send('Hello from webfrontend in Azure');
    ```

1. Sla uw wijzigingen op.
1. Voer **Server starten** opnieuw uit.
1. Navigeer naar uw actieve service en bekijk uw wijzigingen.
1. Selecteer **Fouten opsporen** en vervolgens **Foutopsporing stoppen** om uw toepassing te stoppen.

## <a name="setting-and-using-breakpoints-for-debugging"></a>Onderbrekingspunten instellen en gebruiken voor fouten opsporen

Start uw service met **Server starten (AZDS)** .

Ga terug naar de Explorer-weergave door achtereenvolgens **Beeld** en **Explorer** te selecteren. Open *server.js* en klik ergens op regel 13 om de cursor daar te plaatsen. Als u een onderbrekingspunt wilt instellen, drukt u op **F9** of selecteert u **Fouten opsporen** en vervolgens **Onderbrekingspunt inschakelen**.

Open uw service in een browser en let op dat er geen bericht wordt weergegeven. Ga terug naar Visual Studio Code. U ziet dat regel 13 is gemarkeerd. Het onderbrekingspunt dat u hebt ingesteld, heeft de service op regel 13 onderbroken. Als u de service wilt hervatten, drukt u op **F5** of selecteert u **Fouten opsporen** en vervolgens **Doorgaan**. Ga terug naar uw browser en u ziet dat het bericht nu wordt weergegeven.

Tijdens het uitvoeren van uw service in Kubernetes met een foutopsporingsprogramma, hebt u volledige toegang tot foutopsporingsgegevens, zoals de aanroepstack, lokale variabelen en uitzonderingsgegevens.

Verwijder het onderbrekingspunt door de cursor op regels 13 in *server.js* te plaatsen en op **F9** te drukken.

Selecteer **Fouten opsporen** en selecteer vervolgens **Foutopsporing stoppen** om het foutopsporingsprogramma te stoppen.

## <a name="update-code-from-visual-studio-code"></a>Code bijwerken vanuit Visual Studio Code

Wijzig de foutopsporingsmodus in **Koppelen aan een server (AZDS)** en start de service:

![Schermopname van de gebruikersinterface van Visual Studio Code met de optie Koppelen aan server (AZDS) geselecteerd.](media/get-started-node/attach-nodejs.png)

Met deze opdracht wordt uw service in Azure Dev Spaces gecompileerd en uitgevoerd. Er wordt ook een [nodemon](https://nodemon.io)-proces in de container van de service gestart en er wordt VS-code aan gekoppeld. Met het proces *nodemon* kunnen virtuele machines opnieuw worden opgestart als de broncode is gewijzigd, om de interne ontwikkelingslus te versnellen zoals bij ontwikkeling op een lokale machine.

Nadat de service is gestart, navigeert u naar de service met behulp van uw browser en gaat u ermee werken.

Terwijl de service wordt uitgevoerd, gaat u terug naar Visual Studio Code en werkt u regel 13 bij in *server.js*. Bijvoorbeeld:
```javascript
    res.send('Hello from webfrontend in Azure while debugging!');
```

Sla het bestand op en ga terug naar uw service in een browser. Werk met uw service. U ziet dat uw bijgewerkte bericht wordt weergegeven.

Als *nodemon* actief is, wordt het proces Node automatisch opnieuw gestart zodra er codewijzigingen worden gedetecteerd. Dit proces voor automatisch opnieuw opstarten is vergelijkbaar met de ervaring van het bewerken en opnieuw starten van uw service op uw lokale computer, en biedt een soortgelijk ervaring als een interne ontwikkelingslus.

## <a name="clean-up-your-azure-resources"></a>Uw Azure-resources opschonen

```azurecli
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Volgende stappen

Ontdek hoe Azure Dev Spaces u kan helpen om complexere toepassingen te ontwikkelen binnen meerdere containers en hoe u ontwikkeling in samenwerkingsverband vereenvoudigt door in verschillende ruimten met verschillende versies of vertakkingen van uw code te werken.

> [!div class="nextstepaction"]
> [Werken met meerdere containers en teamontwikkeling](multi-service-nodejs.md)


[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
