---
title: Een toepassing ontwikkelen met een bestaand helm-diagram op Kubernetes
services: azure-dev-spaces
ms.date: 04/21/2020
ms.topic: quickstart
description: In deze Quick Start leert u hoe u Azure dev Spaces en de opdracht regel gebruikt om een toepassing te ontwikkelen met een bestaande helm-grafiek in de Azure Kubernetes-service
keywords: Docker, Kubernetes, azure, AKS, Azure Kubernetes service, containers, helm, service-net, service mesh routing, kubectl, K8S
manager: gwallace
ms.openlocfilehash: c37ea0b04e99cf1bba555e098bdf33b8a8558cfa
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/09/2020
ms.locfileid: "82996684"
---
# <a name="quickstart-develop-an-application-with-an-existing-helm-chart-on-kubernetes---azure-dev-spaces"></a>Quick Start: een toepassing ontwikkelen met een bestaande helm-grafiek op Kubernetes-Azure dev Spaces
In deze handleiding leert u het volgende:

- Azure Dev Spaces instellen met een beheerd Kubernetes-cluster in Azure.
- Voer een toepassing uit met een bestaand helm-diagram in AKS met behulp van Azure dev Spaces op de opdracht regel.

## <a name="prerequisites"></a>Vereisten

- Een Azure-abonnement. Als u geen Azure-abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free)maken.
- [Azure CLI geïnstalleerd](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="create-an-azure-kubernetes-service-cluster"></a>Een Azure Kubernetes service-cluster maken

U moet een AKS-cluster maken in een [ondersteunde regio][supported-regions]. Met de onderstaande opdrachten maakt u een resource groep met de naam *MyResourceGroup* en een AKS-cluster met de naam *MyAKS*.

```azurecli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Azure dev Spaces inschakelen op uw AKS-cluster

Gebruik de `use-dev-spaces` opdracht om ontwikkel ruimten in uw AKS-cluster in te scha kelen en de prompts te volgen. De onderstaande opdracht maakt ontwikkel ruimten in het *MyAKS* -cluster in de *MyResourceGroup* -groep mogelijk en maakt een ontwikkel ruimte met de naam *dev*.

> [!NOTE]
> De `use-dev-spaces` opdracht installeert ook de Azure dev Space cli als deze nog niet is geïnstalleerd. U kunt de CLI voor Azure dev Spaces niet installeren in de Azure Cloud Shell.

```azurecli
az aks use-dev-spaces -g MyResourceGroup -n MyAKS --space dev --yes
```

## <a name="get-sample-application-code"></a>Voorbeeld toepassings code ophalen

In dit artikel gebruikt u de [voorbeeld toepassing Azure dev Spaces](https://github.com/Azure/dev-spaces) om te demonstreren hoe u Azure dev Spaces gebruikt.

Kloon de toepassing van GitHub en navigeer naar de map *dev-Spaces/samples/python/Getting-Started/Webfront-front-front-end* :

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/python/getting-started/webfrontend
```

## <a name="prepare-the-application"></a>De toepassing voorbereiden

Als u uw toepassing wilt uitvoeren op Azure dev Spaces, hebt u een Dockerfile-en helm-grafiek nodig. Voor sommige talen, zoals [Java][java-quickstart], [.net core][netcore-quickstart]en [node. js][nodejs-quickstart], kunnen met het client hulpprogramma van Azure dev Spaces alle benodigde assets worden gegenereerd. Voor veel andere talen, zoals go, PHP en Python, kan de-client hulpprogramma het helm-diagram genereren, mits u een geldige Dockerfile kunt opgeven. In dit geval heeft de voorbeeld toepassing een bestaand Dockerfile-en helm-grafiek

Genereer de configuratie voor het uitvoeren van de toepassing met Azure-ontwikkel ruimten met de bestaande helm- `azds prep` grafiek en Dockerfile met behulp van de opdracht:

```cmd
azds prep --enable-ingress --chart webfrontend/
```

U moet de `prep` opdracht uitvoeren vanuit de map *dev-Spaces/samples/python/Getting-Started/webfrontend* en de locatie van de helm `--chart`-grafiek opgeven met.

> [!NOTE]
> Mogelijk wordt de volgende waarschuwing weer gegeven: *Waarschuwing: Dockerfile kan niet worden gegenereerd als gevolg* van een niet-ondersteunde taal. Wanneer deze `azds prep`wordt uitgevoerd. Met `azds prep` deze opdracht wordt geprobeerd [een Dockerfile-en helm-grafiek](how-dev-spaces-works-prep.md#prepare-your-code) te genereren voor uw project, maar worden bestaande Dockerfiles-of helm-grafieken niet overschreven.

## <a name="build-and-run-code-in-kubernetes"></a>Code schrijven en uitvoeren in Kubernetes

Bouw en voer uw code in AKS met behulp van de `azds up` opdracht:

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

U ziet de service die wordt uitgevoerd door de open bare URL te openen, die wordt weer gegeven in `azds up` de uitvoer van de opdracht. In dit voor beeld is `http://dev.service.1234567890abcdef1234.eus.azds.io/`de open bare URL.

> [!NOTE]
> Wanneer u tijdens het uitvoeren `azds up`naar uw service navigeert, worden de traceringen van de HTTP-aanvraag ook weer `azds up` gegeven in de uitvoer van de opdracht. Deze traceringen kunnen u helpen bij het oplossen van problemen met uw service en fout opsporing. U kunt deze traceringen uitschakelen met `--disable-http-traces` wanneer u `azds up`uitvoert.

Als u de `azds up` opdracht met *CTRL + c*stopt, wordt de service nog steeds uitgevoerd in AKS en blijft de open bare URL beschikbaar.

## <a name="update-code"></a>Code bijwerken

Als u een bijgewerkte versie van uw service wilt implementeren, kunt u elk bestand in het project bijwerken en `azds up` de opdracht opnieuw uitvoeren. Bijvoorbeeld:

1. Als `azds up` het nog steeds wordt uitgevoerd, drukt u op *CTRL + c*.
1. Update [regel 13 in `webfrontend.py` ](https://github.com/Azure/dev-spaces/blob/master/samples/python/getting-started/webfrontend/webfrontend.py#L13) op:
    
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

1. Navigeer naar uw actieve service en Bekijk uw wijzigingen.
1. Druk op *CTRL + c* om de `azds up` opdracht te stoppen.

## <a name="clean-up-your-azure-resources"></a>Uw Azure-resources opschonen

```azurecli
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over hoe Azure dev Spaces u helpt om complexere toepassingen te ontwikkelen in meerdere containers en hoe u samenwerkings ontwikkeling kunt vereenvoudigen door te werken met verschillende versies of vertakkingen van uw code in verschillende ruimten.

> [!div class="nextstepaction"]
> [Team ontwikkeling in azure dev Spaces][team-quickstart]

[java-quickstart]: quickstart-java.md
[nodejs-quickstart]: quickstart-nodejs.md
[netcore-quickstart]: quickstart-netcore.md
[team-quickstart]: quickstart-team-development.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service