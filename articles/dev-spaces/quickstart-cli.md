---
title: Een applicatie ontwikkelen op Kubernetes
services: azure-dev-spaces
ms.date: 02/20/2020
ms.topic: quickstart
description: Met deze snelle start u Azure Dev Spaces en de opdrachtregel gebruiken om een toepassing op Azure Kubernetes Service te ontwikkelen
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers, Helm, service mesh, service mesh routing, kubectl, k8s
manager: gwallace
ms.openlocfilehash: 8ee5cba06d9a526640d9057ee88a681d46392f4f
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80239695"
---
# <a name="quickstart-develop-an-application-on-kubernetes---azure-dev-spaces"></a>Snelstart: een toepassing ontwikkelen op Kubernetes - Azure Dev Spaces
In deze handleiding leert u het volgende:

- Azure Dev Spaces instellen met een beheerd Kubernetes-cluster in Azure.
- Code in containers ontwikkelen en uitvoeren met de opdrachtregel.

## <a name="prerequisites"></a>Vereisten

- Een Azure-abonnement. Als u geen Azure-abonnement hebt, u een [gratis account](https://azure.microsoft.com/free)maken.
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

Kloon de toepassing van GitHub en navigeer naar *de dev-spaces/samples/nodejs/getting-started/webfrontend* directory:

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/nodejs/getting-started/webfrontend
```

## <a name="prepare-the-application"></a>De toepassing voorbereiden

Als u uw toepassing wilt uitvoeren op Azure Dev Spaces, hebt u een Dockerfile- en Helm-diagram nodig. Voor sommige talen, zoals [Java][java-quickstart], [.NET-kern][netcore-quickstart]en [Node.js,][nodejs-quickstart]kan de clienttooling azure Dev Spaces alle elementen genereren die u nodig hebt. Voor veel andere talen, zoals Go, PHP en Python, kan de clienttooling de Helm-grafiek genereren, zolang u een geldig Dockerbestand leveren.

Genereer de docker- en helmgrafiekelementen `azds prep` voor het uitvoeren van de toepassing in Kubernetes met de opdracht:

```cmd
azds prep --enable-ingress
```

U moet `prep` de opdracht uitvoeren vanuit de *map dev-spaces/samples/nodejs/getting-started/webfrontend* om de docker- en helmdiagramelementen correct te genereren.

> [!TIP]
> De `prep` opdracht probeert [een Dockerfile- en Helm-diagram](how-dev-spaces-works-prep.md#prepare-your-code) voor uw project te genereren. Azure Dev Spaces gebruikt deze bestanden om uw code te bouwen en uit te voeren, maar u deze bestanden wijzigen als u de manier waarop het project is opgebouwd en uitgevoerd wilt wijzigen.

## <a name="build-and-run-code-in-kubernetes"></a>Code schrijven en uitvoeren in Kubernetes

Bouw en voer uw code `azds up` uit in AKS met de opdracht:

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
Service 'webfrontend' port 'http' is available at http://webfrontend.1234567890abcdef1234.eus.azds.io/
Service 'webfrontend' port 80 (http) is available at http://localhost:54256
...
```

U de service zien die wordt uitgevoerd door de openbare `azds up` URL te openen, die wordt weergegeven in de uitvoer van de opdracht. In dit voorbeeld is *http://webfrontend.1234567890abcdef1234.eus.azds.io/* de openbare URL .

> [!NOTE]
> Wanneer u tijdens het `azds up`uitvoeren naar uw service navigeert, worden `azds up` de HTTP-aanvraagsporen ook weergegeven in de uitvoer van de opdracht. Met deze sporen u uw service oplossen en debuggen. U deze `--disable-http-traces` sporen `azds up`uitschakelen bij het uitvoeren van.

Als u `azds up` de opdracht stopt met *Ctrl+c,* blijft de service in AKS worden uitgevoerd en blijft de openbare URL beschikbaar.

## <a name="update-code"></a>Code bijwerken

Als u een bijgewerkte versie van uw service wilt implementeren, `azds up` kunt u elk bestand in uw project bijwerken en de opdracht opnieuw uitvoeren. Bijvoorbeeld:

1. Als `azds up` deze nog steeds wordt uitgevoerd, drukt u op *Ctrl+c*.
1. [Regel 13 `server.js` bijwerken in:](https://github.com/Azure/dev-spaces/blob/master/samples/nodejs/getting-started/webfrontend/server.js#L13)
    
    ```javascript
        res.send('Hello from webfrontend in Azure');
    ```

1. Sla uw wijzigingen op.
1. Voer de `azds up` opdracht opnieuw uit:

    ```cmd
    $ azds up
    Using dev space 'default' with target 'MyAKS'
    Synchronizing files...1s
    Installing Helm chart...3s
    Waiting for container image build...
    ...    
    ```

1. Navigeer naar uw hardloopservice en observeer uw wijzigingen.
1. Druk *op Ctrl+c* om de `azds up` opdracht te stoppen.

## <a name="clean-up-your-azure-resources"></a>Uw Azure-bronnen opschonen

```azurecli
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Volgende stappen

Ontdek hoe Azure Dev Spaces u helpt complexere toepassingen te ontwikkelen voor meerdere containers en hoe u de samenwerking vereenvoudigen door te werken met verschillende versies of branches van uw code in verschillende ruimten.

> [!div class="nextstepaction"]
> [Teamontwikkeling in Azure Dev Spaces][team-quickstart]

[java-quickstart]: quickstart-java.md
[nodejs-quickstart]: quickstart-nodejs.md
[netcore-quickstart]: quickstart-netcore.md
[team-quickstart]: quickstart-team-development.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service