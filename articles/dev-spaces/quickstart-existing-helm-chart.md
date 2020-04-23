---
title: Een applicatie ontwikkelen met een bestaande Helm-grafiek op Kubernetes
services: azure-dev-spaces
ms.date: 04/21/2020
ms.topic: quickstart
description: Deze quickstart laat zien hoe u Azure Dev Spaces en de opdrachtregel gebruiken om een toepassing te ontwikkelen met een bestaande Helm-grafiek op Azure Kubernetes Service
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers, Helm, service mesh, service mesh routing, kubectl, k8s
manager: gwallace
ms.openlocfilehash: e767b1ade2a80882ee33ff1fdd718c691dcefcf3
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "82033562"
---
# <a name="quickstart-develop-an-application-with-an-existing-helm-chart-on-kubernetes---azure-dev-spaces"></a>Snelstart: een toepassing ontwikkelen met een bestaande Helm-grafiek op Kubernetes - Azure Dev Spaces
In deze handleiding leert u het volgende:

- Azure Dev Spaces instellen met een beheerd Kubernetes-cluster in Azure.
- Voer een toepassing uit met een bestaand Helmdiagram in AKS met Azure Dev Spaces op de opdrachtregel.

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

Gebruik `use-dev-spaces` de opdracht om Dev Spaces in te schakelen op uw AKS-cluster en volg de aanwijzingen. Met de onderstaande opdracht schakelt Dev Spaces in op het *MyAKS-cluster* in de groep *MyResourceGroup* en wordt een dev-ruimte met *dev*.

> [!NOTE]
> De `use-dev-spaces` opdracht installeert ook de Azure Dev Spaces CLI als deze nog niet is geïnstalleerd. U de AZURE Dev Spaces CLI niet installeren in de Azure Cloud Shell.

```azurecli
az aks use-dev-spaces -g MyResourceGroup -n MyAKS --space dev --yes
```

## <a name="get-sample-application-code"></a>Voorbeeldtoepassingscode ophalen

In dit artikel gebruikt u de [voorbeeldtoepassing Azure Dev Spaces](https://github.com/Azure/dev-spaces) om aan te tonen met Azure Dev Spaces.

Kloon de toepassing van GitHub en navigeer naar de *map dev-spaces/samples/python/getting-started/webfrontend:*

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/python/getting-started/webfrontend
```

## <a name="prepare-the-application"></a>De toepassing voorbereiden

Als u uw toepassing wilt uitvoeren op Azure Dev Spaces, hebt u een Dockerfile- en Helm-diagram nodig. Voor sommige talen, zoals [Java][java-quickstart], [.NET-kern][netcore-quickstart]en [Node.js,][nodejs-quickstart]kan de clienttooling azure Dev Spaces alle elementen genereren die u nodig hebt. Voor veel andere talen, zoals Go, PHP en Python, kan de clienttooling de Helm-grafiek genereren, zolang u een geldig Dockerbestand leveren. In dit geval heeft de voorbeeldtoepassing een bestaand Dockerfile- en Helm-diagram

Genereer de configuratie voor het uitvoeren van de toepassing met Azure `azds prep` Dev Spaces met de bestaande Helm-grafiek en Dockerfile met de opdracht:

```cmd
azds prep --enable-ingress --chart webfrontend/
```

U moet `prep` de opdracht uitvoeren vanuit de *map dev-spaces/samples/python/getting-started/webfrontend* `--chart`en de locatie van de Helm-grafiek opgeven met behulp van .

> [!NOTE]
> Mogelijk ziet u de waarschuwing: *WAARSCHUWING: Dockerfile kan niet worden gegenereerd vanwege niet-ondersteunde taal.* tijdens `azds prep`het hardlopen . De `azds prep` opdracht probeert [een Dockerfile- en Helm-diagram](how-dev-spaces-works-prep.md#prepare-your-code) voor uw project te genereren, maar overschrijft geen bestaande Dockerfiles- of Helm-grafieken.

## <a name="build-and-run-code-in-kubernetes"></a>Code schrijven en uitvoeren in Kubernetes

Bouw en voer uw code `azds up` uit in AKS met de opdracht:

```cmd
$ azds up
Using dev space 'dev' with target 'MyAKS'
Synchronizing files...14s
Installing Helm chart...2s
Waiting for container image build...3s
Building container image...
Step 1/7 : FROM python:3
Step 2/7 : WORKDIR /python/webfrontend
Step 3/7 : RUN pip install flask
Step 4/7 : COPY webfrontend.py webfrontend.py
Step 5/7 : COPY public/ public/
Step 6/7 : EXPOSE 80
Step 7/7 : CMD ["python", "./webfrontend.py"]
Built container image in 45s
Waiting for container...25s
Service 'azds-543eae-dev-webfrontend' port 'http' is available at http://dev.service.1234567890abcdef1234.eus.azds.io/
Service 'azds-543eae-dev-webfrontend' port 80 (http) is available via port forwarding at http://localhost:52382
Press Ctrl+C to detach
...
```

U de service zien die wordt uitgevoerd door de openbare `azds up` URL te openen, die wordt weergegeven in de uitvoer van de opdracht. In dit voorbeeld is *http://dev.service.1234567890abcdef1234.eus.azds.io/* de openbare URL .

> [!NOTE]
> Wanneer u tijdens het `azds up`uitvoeren naar uw service navigeert, worden `azds up` de HTTP-aanvraagsporen ook weergegeven in de uitvoer van de opdracht. Met deze sporen u uw service oplossen en debuggen. U deze `--disable-http-traces` sporen `azds up`uitschakelen bij het uitvoeren van.

Als u `azds up` de opdracht stopt met *Ctrl+c,* blijft de service in AKS worden uitgevoerd en blijft de openbare URL beschikbaar.

## <a name="update-code"></a>Code bijwerken

Als u een bijgewerkte versie van uw service wilt implementeren, `azds up` kunt u elk bestand in uw project bijwerken en de opdracht opnieuw uitvoeren. Bijvoorbeeld:

1. Als `azds up` deze nog steeds wordt uitgevoerd, drukt u op *Ctrl+c*.
1. [Regel 13 `webfrontend.py` bijwerken in:](https://github.com/Azure/dev-spaces/blob/master/samples/python/getting-started/webfrontend/webfrontend.py#L13)
    
    ```javascript
        res.send('Hello from webfrontend in Azure');
    ```

1. Sla uw wijzigingen op.
1. Voer de `azds up` opdracht opnieuw uit:

    ```cmd
    $ azds up
    Using dev space 'dev' with target 'MyAKS'
    Synchronizing files...11s
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