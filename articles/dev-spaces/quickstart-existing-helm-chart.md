---
title: Een toepassing ontwikkelen met een bestaande Helm-grafiek in Kubernetes
services: azure-dev-spaces
ms.date: 04/21/2020
ms.topic: quickstart
description: In deze quickstart leert u hoe u met Azure Dev Spaces en de opdrachtregel een toepassing kunt ontwikkelen met een bestaande Helm-grafiek in Azure Kubernetes Service
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers, Helm, servicemesh, servicemeshroutering, kubectl, k8s
manager: gwallace
ms.custom: devx-track-javascript
ms.openlocfilehash: 7d1d782203401607d3909d79d1f0f3a499712c32
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/29/2020
ms.locfileid: "87414306"
---
# <a name="quickstart-develop-an-application-with-an-existing-helm-chart-on-kubernetes---azure-dev-spaces"></a>Quickstart: Een toepassing ontwikkelen met een bestaande Helm-grafiek in Kubernetes - Azure Dev Spaces
In deze handleiding leert u het volgende:

- Azure Dev Spaces instellen met een beheerd Kubernetes-cluster in Azure.
- Een toepassing uitvoeren met een bestaande Helm-grafiek in AKS met behulp van Azure Sev Spaces op de opdrachtregel.

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

Gebruik de opdracht `use-dev-spaces` om Dev Spaces in te schakelen voor uw AKS-cluster en volg de prompts. Met de onderstaande opdracht schakelt u Dev Spaces in voor het cluster *MyAKS* in de groep *MyResourceGroup* en maakt u een dev-ruimte met de naam *dev*.

> [!NOTE]
> Met de opdracht `use-dev-spaces` wordt ook de CLI voor Azure Dev Spaces geïnstalleerd als die nog niet is geïnstalleerd. U kunt de CLI voor Azure Dev Spaces niet installeren in Azure Cloud Shell.

```azurecli
az aks use-dev-spaces -g MyResourceGroup -n MyAKS --space dev --yes
```

## <a name="get-sample-application-code"></a>Voorbeeldtoepassingscode ophalen

In dit artikel gebruikt u de [voorbeeldtoepassing van Azure Dev Spaces](https://github.com/Azure/dev-spaces) om het gebruiken van Azure Dev Spaces te demonstreren.

Kloon de toepassing van GitHub en ga naar de map *dev-spaces/samples/python/getting-started/webfrontend*:

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/python/getting-started/webfrontend
```

## <a name="prepare-the-application"></a>De toepassing voorbereiden

Als u de toepassing wilt uitvoeren in Azure Dev Spaces, hebt u een Dockerfile en een Helm-grafiek nodig. Voor sommige talen, zoals [Java][java-quickstart], [.NET core][netcore-quickstart]en [Node.js][nodejs-quickstart], kunnen de hulpprogramma's van de Azure Dev Spaces-client alle assets genereren die u nodig hebt. Voor veel andere talen, zoals Go, PHP en Python, kunnen de hulpprogramma's de Helm-grafiek genereren, op voorwaarde dat u een geldige Dockerfile opgeeft. In dit geval beschikt de voorbeeldtoepassing over een bestaand Dockerfile en een Helm-grafiek.

Genereer de configuratie voor het uitvoeren van de toepassing met Azure Dev Spaces met de bestaande Helm-grafiek en het Dockerfile met behulp van de opdracht `azds prep`:

```cmd
azds prep --enable-ingress --chart webfrontend/
```

U moet de opdracht `prep` uitvoeren vanuit de map *dev-spaces/samples/python/getting-started/webfrontend* en de locatie van de Helm-grafiek opgeven met `--chart`.

> [!NOTE]
> Er kan een waarschuwing van de volgende strekking worden weergegeven (mogelijk in het Engels): *WAARSCHUWING: Dockerfile kan niet worden gegenereerd vanwege een niet-ondersteunde taal.* Bij het uitvoeren van `azds prep`, probeert de opdracht `azds prep` [een Dockerfile-en Helm-grafiek](how-dev-spaces-works-prep.md#prepare-your-code) te genereren voor uw project, maar worden bestaande Dockerfiles of Helm-grafieken niet overschreven.

## <a name="build-and-run-code-in-kubernetes"></a>Code schrijven en uitvoeren in Kubernetes

Gebruik de opdracht `azds up` om de code in AKS te compileren en uit te voeren.

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

U kunt de service zien die wordt uitgevoerd door de openbare URL te openen. Deze wordt weergegeven in de uitvoer van de opdracht `azds up`. In dit voorbeeld is `http://dev.service.1234567890abcdef1234.eus.azds.io/` de openbare URL.

> [!NOTE]
> Wanneer u naar uw service navigeert terwijl `azds up` wordt uitgevoerd, worden de traceringen van de HTTP-aanvraag ook weergegeven in de uitvoer van de opdracht `azds up`. Deze traceringen kunnen helpen bij het opsporen en oplossen van problemen met de service. U kunt deze traceringen uitschakelen met behulp van `--disable-http-traces` wanneer u `azds up`uitvoert.

Als u de opdracht `azds up` stopt met behulp van *Ctrl + C*, wordt de service nog steeds uitgevoerd in AKS en blijft de openbare URL beschikbaar.

## <a name="update-code"></a>Code bijwerken

Als u een bijgewerkte versie van uw service wilt implementeren, kunt u een bestand in uw project bijwerken en `azds up` nogmaals uitvoeren. Bijvoorbeeld:

1. Als `azds up` nog steeds wordt uitgevoerd, drukt u op *Ctrl + C*.
1. Wijzig [regel 13 in `webfrontend.py`](https://github.com/Azure/dev-spaces/blob/master/samples/python/getting-started/webfrontend/webfrontend.py#L13) in:
    
    ```javascript
        res.send('Hello from webfrontend in Azure');
    ```

1. Sla uw wijzigingen op.
1. Voer de opdracht `azds up` opnieuw uit:

    ```cmd
    $ azds up
    Using dev space 'dev' with target 'MyAKS'
    Synchronizing files...11s
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