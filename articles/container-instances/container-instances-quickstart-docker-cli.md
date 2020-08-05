---
title: 'Quickstart: Docker-container implementeren naar een containerinstantie met de Docker CLI'
description: In deze quickstart gebruikt u de Docker CLI om snel een in een container geplaatste web-app te implementeren die wordt uitgevoerd in een geïsoleerde instantie van een Azure-container.
ms.topic: quickstart
ms.date: 07/16/2020
ms.custom: devx-track-javascript
ms.openlocfilehash: 0e4569904ef6aee304518ce012889d10dc2ecbce
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/29/2020
ms.locfileid: "87408081"
---
# <a name="quickstart-deploy-a-container-instance-in-azure-using-the-docker-cli"></a>Quickstart: Een containerinstantie in Azure implementeren met behulp van de Docker CLI

Gebruik Azure Container Instances om snel en eenvoudig serverloze Docker-containers uit te voeren in Azure. Implementeer op aanvraag naar een containerinstantie wanneer u cloud-apps ontwikkelt en u naadloos wilt kunnen overschakelen van lokaal ontwikkelen naar cloudimplementatie.

In deze quickstart gebruikt u opdrachten van de ingebouwde Docker CLI voor het implementeren van een Docker-container, om daarna de bijbehorende toepassing beschikbaar te maken in Azure Container Instances. Deze mogelijkheid wordt ingeschakeld door [integratie tussen Docker en Azure](https://docs.docker.com/engine/context/aci-integration/) (bèta). Een paar seconden na het uitvoeren van de opdracht `docker run` kunt u browsen naar de toepassing die wordt uitgevoerd in de container:

:::image type="content" source="media/container-instances-quickstart-docker-cli/view-application-running-in-an-azure-container-instance.png" alt-text="App die is geïmplementeerd met Azure Container Instances, weergegeven in de browser":::

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account][azure-account] aan voordat u begint.

Voor deze quickstart moet u Docker Desktop Edge versie 2.3.2.0 of hoger installeren. Deze versie is beschikbaar voor [Windows](https://desktop.docker.com/win/edge/Docker%20Desktop%20Installer.exe) en [macOS](https://desktop.docker.com/mac/edge/Docker.dmg). U kunt ook de [Docker ACI Integration CLI voor Linux](https://docs.docker.com/engine/context/aci-integration/#install-the-docker-aci-integration-cli-on-linux) (bèta) installeren. 

> [!IMPORTANT]
> Deze functie is momenteel in preview en vereist bèta-functies (preview) in Docker. Lees hier meer over [stabiele en Edge-versies van Docker Desktop](https://docs.docker.com/desktop/#stable-and-edge-versions). Niet alle functies van Azure Container Instances worden ondersteund. Geef feedback over de integratie van Docker en Azure door een probleem aan te maken in de GitHub-opslagplaats [aci-integration-beta](https://github.com/docker/aci-integration-beta).

## <a name="create-azure-context"></a>Azure-context maken

Als u Docker-opdrachten wilt gebruiken voor het uitvoeren van containers in Azure Container Instances, meldt u zich eerst aan bij Azure:

```bash
docker login azure
```

Voer uw Azure-referenties in of selecteer deze wanneer dit wordt gevraagd.


Voer `docker context create aci` uit om een ACI-context te maken. Deze context koppelt Docker aan uw Azure-abonnement, zodat u containerinstanties kunt maken. Stel dat u een context wilt maken met de naam *myacicontext*:

```
docker context create aci myacicontext
```

Wanneer u hierom wordt gevraagd, selecteert u de id van uw Azure-abonnement en selecteert u een bestaande resourcegroep of **maakt u een nieuwe**. Als u een nieuwe resourcegroep maakt, krijgt deze een door het systeem gegenereerde naam. Azure Container Instances moeten, zoals alle Azure-resources, worden geïmplementeerd in een resourcegroep. Met resourcegroepen kunt u gerelateerde Azure-resources organiseren en beheren.


Voer `docker context ls` uit om te bevestigen dat u de ACI-context hebt toegevoegd aan uw Docker-contexten:

```
docker context ls
```

## <a name="create-a-container"></a>Een container maken

Nadat u een Docker-context hebt gemaakt, kunt u een container maken in Azure. In deze quickstart gebruikt u de openbare installatiekopie `mcr.microsoft.com/azuredocs/aci-helloworld`. Deze installatiekopie bevat een kleine web-app die is geschreven in Node.js en die een statische HTML-pagina dient.

Ga eerst naar de ACI-context. Alle volgende Docker-opdrachten worden uitgevoerd in deze context.

```
docker context use myacicontext
```

Voer de volgende `docker run`-opdracht uit om de Azure-containerinstantie te maken met poort 80 toegankelijk op internet:

```
docker run -p 80:80 mcr.microsoft.com/azuredocs/aci-helloworld
```

Voorbeeld van uitvoer bij een geslaagde implementatie:

```
[+] Running 2/2
 ⠿ hungry-kirch            Created                                                                               5.1s
 ⠿ single--container--aci  Done                                                                                 11.3s
hungry-kirch
```

Voer `docker ps` uit om meer informatie te krijgen over de container die wordt uitgevoerd, zoals het openbare IP-adres:

```
docker ps
```


Voorbeeld van uitvoer met een openbaar IP-adres, in dit geval *52.230.225.232*:

```
CONTAINER ID        IMAGE                                        COMMAND             STATUS              PORTS
hungry-kirch        mcr.microsoft.com/azuredocs/aci-helloworld                       Running             52.230.225.232:80->80/tcp
```

 Ga nu naar het IP-adres in uw browser. U moet nu een webpagina zien die lijkt op de volgende. U hebt een toepassing geïmplementeerd die wordt uitgevoerd in een Docker-container voor Azure.

:::image type="content" source="media/container-instances-quickstart-docker-cli/view-application-running-in-an-azure-container-instance.png" alt-text="App die is geïmplementeerd met Azure Container Instances, weergegeven in de browser":::

## <a name="pull-the-container-logs"></a>De containerlogboeken ophalen

Wanneer u problemen moet oplossen voor een container of de toepassing die daarmee wordt uitgevoerd, of als u alleen de uitvoer wilt zien, kunt u eerst de logboeken van de containerinstantie bekijken.

Voer bijvoorbeeld de `docker logs` opdracht uit om de logboeken van de container *hungry-kirch* te bekijken in de ACI-context:

```azurecli-interactive
docker logs hungry-kirch
```

De uitvoer geeft de logboeken voor de container weer, evenals de HTTP GET-aanvragen die worden gegenereerd wanneer u de toepassing in uw browser weergeeft.

```output
listening on port 80
::ffff:10.240.255.55 - - [07/Jul/2020:17:43:53 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [07/Jul/2020:17:44:36 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [07/Jul/2020:17:44:36 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
```


## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent met de container, voert u `docker rm` uit om deze te verwijderen. Met deze opdracht wordt de Azure-containerinstantie gestopt en verwijderd.

```
docker rm hungry-kirch
```


## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een Azure-containerinstantie van een openbare installatiekopie gemaakt door gebruik te maken van de integratie tussen Docker en Azure. Meer informatie over integratiescenario's vindt u in de [documentatie van Docker](https://docs.docker.com/engine/context/aci-integration/). 

U kunt ook de [Docker-extensie](https://aka.ms/VSCodeDocker) voor Visual Studio Code gebruiken om te werken met een geïntegreerde ervaring voor het ontwikkelen, uitvoeren en beheren van containers, installatiekopieën en contexten.

Als u de Azure-hulpprogramma's wilt gebruiken om containerinstanties te maken en beheren, raadpleegt u de quickstarts voor de [Azure CLI](container-instances-quickstart.md), [Azure PowerShell](container-instances-quickstart-powershell.md), de [Azure-portal](container-instances-quickstart-portal.md)en de [Azure Resource Manager-sjabloon](container-instances-quickstart-template.md).

Als u zelf een containerinstallatiekopie wilt bouwen en deze wilt implementeren met behulp van een privé Azure Container-register, gaat u verder met de zelfstudie voor Azure Container Instances.

> [!div class="nextstepaction"]
> [Zelfstudie voor Azure Container Instances](./container-instances-tutorial-prepare-app.md)

<!-- LINKS - External -->

[azure-account]: https://azure.microsoft.com/free/
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

