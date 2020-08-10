---
title: Een toepassing ontwikkelen op Kubernetes
services: azure-dev-spaces
ms.date: 02/20/2020
ms.topic: quickstart
description: In deze quickstart leert u hoe u met Azure Dev Spaces en de opdrachtregel een toepassing kunt ontwikkelen in Azure Kubernetes Service
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers, Helm, servicemesh, servicemeshroutering, kubectl, k8s
manager: gwallace
ms.custom: devx-track-javascript
ms.openlocfilehash: 82e7e73a91b07770881353465af063497deae085
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/29/2020
ms.locfileid: "87414799"
---
# <a name="quickstart-develop-an-application-on-kubernetes---azure-dev-spaces"></a>Quickstart: Een toepassing ontwikkelen op Kubernetes - Azure Dev Spaces
In deze handleiding leert u het volgende:

- Azure Dev Spaces instellen met een beheerd Kubernetes-cluster in Azure.
- Code in containers ontwikkelen en uitvoeren met behulp van de opdrachtregel.

## <a name="prerequisites"></a>Vereisten

- Een Azure-abonnement. Als u geen Azure-abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free) maken.
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

Kloon de toepassing van GitHub en ga naar de map *dev-spaces/samples/nodejs/getting-started/webfrontend*:

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/nodejs/getting-started/webfrontend
```

## <a name="prepare-the-application"></a>De toepassing voorbereiden

Als u de toepassing wilt uitvoeren in Azure Dev Spaces, hebt u een Dockerfile en een Helm-grafiek nodig. Voor sommige talen, zoals [Java][java-quickstart], [.NET Core][netcore-quickstart]en [Node.js][nodejs-quickstart], kunnen de hulpprogramma's van de Azure Dev Spaces-client alle assets genereren die u nodig hebt. Voor veel andere talen, zoals Go, PHP en Python, kunnen de hulpprogramma's de Helm-grafiek genereren, mits u een geldige Dockerfile opgeeft.

Genereer de assets Docker- en Helm-grafiek voor het uitvoeren van de toepassing in Kubernetes met behulp van de `azds prep`-opdracht:

```cmd
azds prep --enable-ingress
```

U moet de opdracht `prep` uitvoeren vanuit de map *dev-spaces/samples/nodejs/getting-started/webfrontend* voor het correct genereren van de assets Docker en Helm-grafiek.

> [!TIP]
> Met de opdracht `prep` wordt geprobeerd [een Dockerfile en Helm-grafiek](how-dev-spaces-works-prep.md#prepare-your-code) voor uw project te genereren. Azure Dev Spaces maakt gebruik van deze bestanden om uw code te compileren en uit te voeren, maar u kunt deze bestanden wijzigen als u de opzet en uitvoering van het project wilt wijzigen.

## <a name="build-and-run-code-in-kubernetes"></a>Code schrijven en uitvoeren in Kubernetes

Gebruik de opdracht `azds up` om de code in AKS te compileren en uit te voeren:

```cmd
$ azds up
Using dev space 'default' with target 'MyAKS'
Synchronizing files...2s
Installing Helm chart...2s
Waiting for container image build...2m 25s
Building container image...
Step 1/8 : FROM node
Step 2/8 : ENV PORT 80
Step 3/8 : EXPOSE 80
Step 4/8 : WORKDIR /app
Step 5/8 : COPY package.json .
Step 6/8 : RUN npm install
Step 7/8 : COPY . .
Step 8/8 : CMD ["npm", "start"]
Built container image in 6m 17s
Waiting for container...13s
Service 'webfrontend' port 'http' is available at `http://webfrontend.1234567890abcdef1234.eus.azds.io/`
Service 'webfrontend' port 80 (http) is available at http://localhost:54256
...
```

U kunt de service zien die wordt uitgevoerd door de openbare URL te openen. Deze wordt weergegeven in de uitvoer van de opdracht `azds up`. In dit voorbeeld is de openbare URL *`http://webfrontend.1234567890abcdef1234.eus.azds.io/`* .

> [!NOTE]
> Wanneer u naar uw service navigeert terwijl `azds up` wordt uitgevoerd, worden de traceringen van de HTTP-aanvraag ook weergegeven in de uitvoer van de opdracht `azds up`. Deze traceringen kunnen helpen bij het opsporen en oplossen van problemen met de service. U kunt deze traceringen uitschakelen met behulp van `--disable-http-traces` wanneer u `azds up`uitvoert.

Als u de opdracht `azds up` stopt met behulp van *Ctrl + C*, wordt de service nog steeds uitgevoerd in AKS en blijft de openbare URL beschikbaar.

## <a name="update-code"></a>Code bijwerken

Als u een bijgewerkte versie van uw service wilt implementeren, kunt u een bestand in uw project bijwerken en `azds up` nogmaals uitvoeren. Bijvoorbeeld:

1. Als `azds up` nog steeds wordt uitgevoerd, drukt u op *Ctrl + C*.
1. Wijzig [regel 13 in `server.js`](https://github.com/Azure/dev-spaces/blob/master/samples/nodejs/getting-started/webfrontend/server.js#L13) in:
    
    ```javascript
        res.send('Hello from webfrontend in Azure');
    ```

1. Sla uw wijzigingen op.
1. Voer de opdracht `azds up` opnieuw uit:

    ```cmd
    $ azds up
    Using dev space 'default' with target 'MyAKS'
    Synchronizing files...1s
    Installing Helm chart...3s
    Waiting for container image build...
    ...    
    ```

1. Navigeer naar uw actieve service en bekijk uw wijzigingen.
1. Druk op *Ctrl + C* om de opdracht `azds up` te stoppen.

## <a name="clean-up-your-azure-resources"></a>Uw Azure-resources opschonen

```azurecli
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Volgende stappen

Ontdek hoe Azure Dev Spaces u kan helpen om complexere toepassingen te ontwikkelen binnen meerdere containers en hoe u ontwikkeling in samenwerkingsverband vereenvoudigt door in verschillende ruimten met verschillende versies of vertakkingen van uw code te werken.

> [!div class="nextstepaction"]
> [Teamontwikkeling in Azure Dev Spaces][team-quickstart]

[java-quickstart]: quickstart-java.md
[nodejs-quickstart]: quickstart-nodejs.md
[netcore-quickstart]: quickstart-netcore.md
[team-quickstart]: quickstart-team-development.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service