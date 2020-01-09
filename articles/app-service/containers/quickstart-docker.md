---
title: 'Snelstartgids: een aangepaste Linux-container uitvoeren'
description: Ga aan de slag met Linux-containers op Azure App Service door uw eerste aangepaste container te implementeren met behulp van Azure-container registers.
author: msangapu-msft
ms.author: msangapu
ms.date: 08/28/2019
ms.topic: quickstart
ms.openlocfilehash: 62e6b007e89fc6be726d3d971ca838770db9cb6e
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75422151"
---
# <a name="deploy-a-custom-linux-container-to-azure-app-service"></a>Een aangepaste Linux-container implementeren op Azure App Service

App Service op Linux biedt vooraf gedefinieerde toepassings stacks op Linux met ondersteuning voor talen zoals .NET, PHP, node. js en andere. U kunt een aangepaste Docker-installatiekopie ook gebruiken om uw web-app uit te voeren op een toepassingsstack die nog niet in Azure is gedefinieerd. In deze Quick start ziet u hoe u een installatie kopie van een [Azure container Registry](/azure/container-registry) (ACR) implementeert naar app service.

## <a name="prerequisites"></a>Vereisten

* Een [Azure-account](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-docker-extension&mktingSource=vscode-tutorial-docker-extension)
* [Docker](https://www.docker.com/community-edition)
* [Visual Studio Code](https://code.visualstudio.com/)
* De [uitbrei ding van de Azure app service voor VS code](vscode:extension/ms-azuretools.vscode-azureappservice). U kunt deze extensie gebruiken voor het maken, beheren en implementeren van Linux-Web Apps op het Azure-Platform as a Service (PaaS).
* De [docker-extensie voor VS code](vscode:extension/ms-azuretools.vscode-docker). U kunt deze uitbrei ding gebruiken om het beheer te vereenvoudigen van lokale docker-installatie kopieën en-opdrachten en om installatie kopieën van gebouwde apps te implementeren in Azure.

## <a name="create-an-image"></a>Een installatiekopie maken

Voor het volt ooien van deze Snelstartgids hebt u een geschikte installatie kopie van de web-app die is opgeslagen in een [Azure container Registry](/azure/container-registry). Volg de instructies in [Quick Start: Maak een persoonlijk container register met behulp van de Azure Portal](/azure/container-registry/container-registry-get-started-portal), maar gebruik de `mcr.microsoft.com/azuredocs/go` installatie kopie in plaats van de `hello-world` installatie kopie. Ter referentie is de voor [beeld-Dockerfile te vinden in azure samples opslag plaats](https://github.com/Azure-Samples/go-docs-hello-world).

> [!IMPORTANT]
> Zorg ervoor dat u de optie **gebruikers beheerder** instelt om in te **scha kelen** wanneer u het container register maakt. U kunt deze ook instellen via de sectie **toegangs sleutels** van uw register pagina in de Azure Portal. Deze instelling is vereist voor toegang tot App Service.

## <a name="sign-in"></a>Aanmelden

Vervolgens start u VS code en meldt u zich aan bij uw Azure-account met behulp van de extensie App Service. Als u dit wilt doen, selecteert u het Azure-logo op de activiteiten balk, gaat u naar **app service** Explorer en selecteert **u aanmelden bij Azure** en volgt u de instructies.

![aanmelden bij Azure](./media/quickstart-docker/sign-in.png)

## <a name="check-prerequisites"></a>Vereisten controleren

U kunt nu controleren of alle vereiste onderdelen zijn geïnstalleerd en correct zijn geconfigureerd.

In VS code ziet u uw Azure-e-mail adres in de status balk en uw abonnement in de **app service** Explorer.

Controleer vervolgens of docker is geïnstalleerd en wordt uitgevoerd. Met de volgende opdracht wordt de docker-versie weer gegeven als deze wordt uitgevoerd.

```bash
docker --version
```

Ten slotte moet u controleren of uw Azure Container Registry is verbonden. Als u dit wilt doen, selecteert u het docker-logo in de activiteiten balk en navigeert u vervolgens naar **registers**.

![Registers](./media/quickstart-docker/registries.png)

## <a name="deploy-the-image-to-azure-app-service"></a>De installatie kopie implementeren naar Azure App Service

Nu alles is geconfigureerd, kunt u uw installatie kopie rechtstreeks vanuit de docker-extensie Verkenner naar [Azure app service](https://azure.microsoft.com/services/app-service/) implementeren.

Zoek de afbeelding onder het knoop punt **registers** in de **docker** -Verkenner en vouw deze uit om de bijbehorende labels weer te geven. Klik met de rechter muisknop op een tag en selecteer vervolgens **installatie kopie implementeren om Azure app service**.

Volg hier de aanwijzingen voor het kiezen van een abonnement, een globaal unieke app-naam, een resource groep en een App Service plan. Kies **B1 Basic** voor de prijs categorie en een regio.

Na de implementatie is uw app beschikbaar op `http://<app name>.azurewebsites.net`.

Een **resource groep** is een benoemde verzameling van alle resources van uw toepassing in Azure. Een resource groep kan bijvoorbeeld een verwijzing bevatten naar een website, een Data Base en een Azure-functie.

Een **app service plan** definieert de fysieke resources die worden gebruikt voor het hosten van uw website. In deze Quick Start wordt gebruikgemaakt van een **basis** hosting plan op **Linux** -infra structuur, wat betekent dat de site wordt gehost op een Linux-machine naast andere websites. Als u begint met het **basis** abonnement, kunt u de Azure Portal gebruiken om omhoog te schalen, zodat u de enige site bent die op een computer wordt uitgevoerd.

## <a name="browse-the-website"></a>Door de website bladeren

Het deel venster **uitvoer** wordt geopend tijdens de implementatie om de status van de bewerking aan te geven. Wanneer de bewerking is voltooid, zoekt u de app die u in de **app service** Explorer hebt gemaakt, klikt u erop met de rechter muisknop en selecteert u vervolgens **Bladeren website** om de site in uw browser te openen.

> [!div class="nextstepaction"]
> [Ik heb een probleem ondertreden](https://www.research.net/r/PWZWZ52?tutorial=quickstart-docker&step=deploy-app)

## <a name="next-steps"></a>Volgende stappen

Gefeliciteerd, u hebt deze Snelstartgids voltooid.

Bekijk vervolgens de andere Azure-extensies.

* [Cosmos DB](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
* [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
* [Azure CLI-Hulpprogram Ma's](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
* [Azure Resource Manager-Hulpprogram Ma's](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

U kunt ze ook downloaden door de [Azure tools](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) Extension Pack te installeren.
