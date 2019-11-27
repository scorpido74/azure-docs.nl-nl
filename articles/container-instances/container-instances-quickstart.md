---
title: Snelstartgids-docker-container implementeren naar container exemplaar-Azure CLI
description: In deze Quick Start gebruikt u de Azure CLI om snel een container web-app te implementeren die wordt uitgevoerd in een geïsoleerd Azure-container exemplaar
ms.topic: quickstart
ms.date: 03/21/2019
ms.custom:
- seo-python-october2019
- seodec18
- mvc
ms.openlocfilehash: ab079c7ad5d741d5a569e042ae8939cbeeff203b
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2019
ms.locfileid: "74533580"
---
# <a name="quickstart-deploy-a-container-instance-in-azure-using-the-azure-cli"></a>Snelstartgids: een container exemplaar implementeren in azure met behulp van Azure CLI

Gebruik Azure Container Instances om serverloze docker-containers in azure uit te voeren met eenvoud en snelheid. Implementeer een toepassing op een container exemplaar op aanvraag wanneer u geen volledig container Orchestration-platform zoals Azure Kubernetes service nodig hebt.

In deze Quick Start gebruikt u de Azure CLI voor het implementeren van een geïsoleerde docker-container en maakt u de toepassing beschikbaar met een Fully Qualified Domain Name (FQDN). Een paar seconden nadat u één implementatie opdracht hebt uitgevoerd, kunt u bladeren naar de toepassing die in de container wordt uitgevoerd:

![Een app weer geven die is geïmplementeerd in Azure Container Instances in browser][aci-app-browser]

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account][azure-account] aan voordat u begint.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

U kunt de Azure Cloud Shell of een lokale installatie van de Azure CLI gebruiken om deze Quick Start uit te voeren. Als u het lokaal wilt gebruiken, wordt versie 2.0.55 of hoger aanbevolen. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren][azure-cli-install] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Azure Container Instances moeten, zoals alle Azure-resources, worden geïmplementeerd in een resourcegroep. Met resourcegroepen kunt u gerelateerde Azure-resources organiseren en beheren.

Maak eerst een resource groep met de naam *myResourceGroup* in de *oostelijke* locatie met de volgende opdracht [AZ Group Create][az-group-create] :

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container"></a>Een container maken

Nu u een resourcegroep hebt, kunt u een container in Azure uitvoeren. Als u een container exemplaar met de Azure CLI wilt maken, geeft u de naam van de resource groep, de naam van het container exemplaar en de docker-container installatie kopie op in de opdracht [AZ container Create][az-container-create] . In deze Quick Start gebruikt u de open bare installatie kopie van `mcr.microsoft.com/azuredocs/aci-helloworld`. Deze installatiekopie bevat een kleine web-app geschreven in Node.js die een statische HTML-pagina levert.

Als u uw containers beschikbaar wilt maken op internet, moet u een of meer poorten om te openen of een DNS-naamlabel opgeven, of beide. In deze quickstart implementeert u een container met een DNS-naamlabel zodat de web-app openbaar bereikbaar is.

Voer een opdracht uit die vergelijkbaar is met de volgende om een container exemplaar te starten. Stel een `--dns-name-label` waarde in die uniek is binnen de Azure-regio waar u het exemplaar maakt. Als u een foutbericht 'DNS-naamlabel niet beschikbaar' ontvangt, probeert u een ander DNS-naamlabel.

```azurecli-interactive
az container create --resource-group myResourceGroup --name mycontainer --image mcr.microsoft.com/azuredocs/aci-helloworld --dns-name-label aci-demo --ports 80
```

Binnen enkele seconden krijgt u een reactie van de Azure-CLI die aangeeft dat de implementatie is voltooid. Controleer de status met de opdracht [AZ container show][az-container-show] :

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer --query "{FQDN:ipAddress.fqdn,ProvisioningState:provisioningState}" --out table
```

Wanneer u de opdracht uitvoert, worden de FQDN (Fully Qualified Domain Name) en de inrichtingsstatus van de container weergegeven.

```console
$ az container show --resource-group myResourceGroup --name mycontainer --query "{FQDN:ipAddress.fqdn,ProvisioningState:provisioningState}" --out table
FQDN                               ProvisioningState
---------------------------------  -------------------
aci-demo.eastus.azurecontainer.io  Succeeded
```

Als de `ProvisioningState` van de container is **voltooid**, gaat u naar de FQDN in uw browser. U moet nu een webpagina zien die lijkt op de volgende. U hebt een toepassing geïmplementeerd die wordt uitgevoerd in een Docker-container voor Azure.

![Een app weer geven die is geïmplementeerd in Azure Container Instances in browser][aci-app-browser]

Als de toepassing niet meteen wordt weergegeven, moet u mogelijk een paar seconden wachten terwijl DNS wordt doorgegeven. Probeer vervolgens de browser te vernieuwen.

## <a name="pull-the-container-logs"></a>De containerlogboeken ophalen

Wanneer u problemen moet oplossen voor een container of de toepassing die daarmee wordt uitgevoerd, of als u alleen de uitvoer wilt zien, kunt u eerst de logboeken van de containerinstantie bekijken.

Haal de logboeken van de container instantie op met de opdracht [AZ container logs][az-container-logs] :

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer
```

De uitvoer geeft de logboeken voor de container weer, evenals de HTTP GET-aanvragen die worden gegenereerd wanneer u de toepassing in uw browser weergeeft.

```console
$ az container logs --resource-group myResourceGroup --name mycontainer
listening on port 80
::ffff:10.240.255.55 - - [21/Mar/2019:17:43:53 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [21/Mar/2019:17:44:36 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [21/Mar/2019:17:44:36 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
```

## <a name="attach-output-streams"></a>Uitvoerstromen koppelen

Behalve het bekijken van de logboeken kunt u ook uw lokale standaarduitvoer- en standaardgegevensstromen met fouten koppelen aan die van de container.

Voer eerst de opdracht [AZ container attach][az-container-attach] uit om uw lokale console te koppelen aan de uitvoer stromen van de container:

```azurecli-interactive
az container attach --resource-group myResourceGroup --name mycontainer
```

Vernieuw de browser na het koppelen een paar keer om wat extra uitvoer te genereren. Wanneer u klaar bent, ontkoppelt u ten slotte de console met `Control+C`. De uitvoer ziet er als volgt uit:

```console
$ az container attach --resource-group myResourceGroup --name mycontainer
Container 'mycontainer' is in state 'Running'...
(count: 1) (last timestamp: 2019-03-21 17:27:20+00:00) pulling image "mcr.microsoft.com/azuredocs/aci-helloworld"
(count: 1) (last timestamp: 2019-03-21 17:27:24+00:00) Successfully pulled image "mcr.microsoft.com/azuredocs/aci-helloworld"
(count: 1) (last timestamp: 2019-03-21 17:27:27+00:00) Created container
(count: 1) (last timestamp: 2019-03-21 17:27:27+00:00) Started container

Start streaming logs:
listening on port 80

::ffff:10.240.255.55 - - [21/Mar/2019:17:43:53 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [21/Mar/2019:17:44:36 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [21/Mar/2019:17:44:36 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [21/Mar/2019:17:47:01 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.56 - - [21/Mar/2019:17:47:12 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
```

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent met de container, verwijdert u deze met behulp van de opdracht [AZ container delete][az-container-delete] :

```azurecli-interactive
az container delete --resource-group myResourceGroup --name mycontainer
```

Om te controleren of de container is verwijderd, voert u de opdracht [az container list](/cli/azure/container#az-container-list) uit:

```azurecli-interactive
az container list --resource-group myResourceGroup --output table
```

De container **mycontainer** mag dan niet worden weergegeven in de uitvoer van de opdracht. Als u geen andere containers in de resourcegroep hebt, wordt er geen uitvoer weergegeven.

Als u klaar bent met de resource groep *myResourceGroup* en alle resources die deze bevat, verwijdert u deze met de opdracht [AZ Group delete][az-group-delete] :

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u een Azure-container exemplaar gemaakt met behulp van een open bare micro soft-installatie kopie. Als u zelf een containerinstallatiekopie wilt bouwen en deze wilt implementeren met behulp van een privé Azure Container-register, gaat u verder met de zelfstudie voor Azure Container Instances.

> [!div class="nextstepaction"]
> [Zelfstudie voor Azure Container Instances](./container-instances-tutorial-prepare-app.md)

Zie de Snelstartgids voor [Azure Kubernetes service (AKS)][container-service] als u opties wilt uitproberen voor het uitvoeren van containers in een Orchestration-systeem op Azure.

<!-- IMAGES -->
[aci-app-browser]: ./media/container-instances-quickstart/view-an-application-running-in-an-azure-container-instance.png

<!-- LINKS - External -->
[app-github-repo]: https://github.com/Azure-Samples/aci-helloworld.git
[azure-account]: https://azure.microsoft.com/free/
[node-js]: https://nodejs.org

<!-- LINKS - Internal -->
[az-container-attach]: /cli/azure/container#az-container-attach
[az-container-create]: /cli/azure/container#az-container-create
[az-container-delete]: /cli/azure/container#az-container-delete
[az-container-list]: /cli/azure/container#az-container-list
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[azure-cli-install]: /cli/azure/install-azure-cli
[container-service]: ../aks/kubernetes-walkthrough.md
