---
title: 'Snelstart: Een aangepaste Linux-container uitvoeren'
description: Ga aan de slag met Linux-containers op Azure App Service door uw eerste aangepaste container te implementeren met Azure Container-registers.
author: msangapu-msft
ms.author: msangapu
ms.date: 08/28/2019
ms.topic: quickstart
ms.openlocfilehash: 62e6b007e89fc6be726d3d971ca838770db9cb6e
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "75422151"
---
# <a name="deploy-a-custom-linux-container-to-azure-app-service"></a>Een aangepaste Linux-container implementeren in Azure App Service

App Service op Linux biedt vooraf gedefinieerde applicatiestacks op Linux met ondersteuning voor talen zoals .NET, PHP, Node.js en anderen. U kunt een aangepaste Docker-installatiekopie ook gebruiken om uw web-app uit te voeren op een toepassingsstack die nog niet in Azure is gedefinieerd. Met deze snelstart ziet u hoe u een afbeelding implementeert van een [Azure Container Registry](/azure/container-registry) (ACR) naar App Service.

## <a name="prerequisites"></a>Vereisten

* Een [Azure-account](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-docker-extension&mktingSource=vscode-tutorial-docker-extension)
* [Docker](https://www.docker.com/community-edition)
* [Visual Studio-code](https://code.visualstudio.com/)
* De [Azure App Service-extensie voor VS-code](vscode:extension/ms-azuretools.vscode-azureappservice). U deze extensie gebruiken om Linux Web Apps te maken, beheren en implementeren op het Azure Platform as a Service (PaaS).
* De [Docker-extensie voor VS-code](vscode:extension/ms-azuretools.vscode-docker). U deze extensie gebruiken om het beheer van lokale Docker-afbeeldingen en -opdrachten te vereenvoudigen en om gebouwde app-afbeeldingen te implementeren in Azure.

## <a name="create-an-image"></a>Een afbeelding maken

Om deze quickstart te voltooien, hebt u een geschikte web-app-afbeelding nodig die is opgeslagen in een [Azure Container Registry.](/azure/container-registry) Volg de instructies in [Quickstart: Maak een privécontainerregister met de Azure-portal,](/azure/container-registry/container-registry-get-started-portal)maar gebruik de `mcr.microsoft.com/azuredocs/go` afbeelding in plaats van de `hello-world` afbeelding. Ter referentie wordt het [voorbeeld Dockerfile gevonden in Azure Samples repo](https://github.com/Azure-Samples/go-docs-hello-world).

> [!IMPORTANT]
> Zorg ervoor dat u de optie **Beheerdergebruiker** instelt **op Inschakelen** wanneer u het containerregister maakt. U het ook instellen via het gedeelte **Toegangssleutels** van uw registerpagina in de Azure-portal. Deze instelling is vereist voor toegang tot App Service.

## <a name="sign-in"></a>Aanmelden

Start vervolgens VS Code en meld u aan bij uw Azure-account met de App Service-extensie. Selecteer hiervoor het Azure-logo in de activiteitsbalk, navigeert naar de **APP SERVICE-verkenner,** selecteer Aanmelden bij **Azure** en volg de instructies.

![aanmelden bij Azure](./media/quickstart-docker/sign-in.png)

## <a name="check-prerequisites"></a>Vereisten controleren

Nu u controleren of u alle vereisten correct hebt geïnstalleerd en geconfigureerd.

In VS-code ziet u uw Azure-e-mailadres in de statusbalk en uw abonnement in de **APP SERVICE-verkenner.**

Controleer vervolgens of Docker is geïnstalleerd en uitgevoerd. In de volgende opdracht wordt de Docker-versie weergegeven als deze wordt uitgevoerd.

```bash
docker --version
```

Controleer ten slotte of uw Azure Container Registry is verbonden. Selecteer hiervoor het Docker-logo in de activiteitsbalk en navigeer vervolgens naar **REGISTERS**.

![Registers](./media/quickstart-docker/registries.png)

## <a name="deploy-the-image-to-azure-app-service"></a>De afbeelding implementeren in Azure App Service

Nu alles is geconfigureerd, u uw afbeelding rechtstreeks vanuit de Docker-extensieverkenner implementeren in [Azure App Service.](https://azure.microsoft.com/services/app-service/)

Zoek de afbeelding onder het knooppunt **Registers** in de **DOCKER-verkenner** en vouw deze uit om de tags weer te geven. Klik met de rechtermuisknop op een tag en selecteer **Vervolgens Afbeelding implementeren voor Azure App Service**.

Volg hier de aanwijzingen om een abonnement, een wereldwijd unieke app-naam, een resourcegroep en een App-serviceplan te kiezen. Kies **B1 Basic** voor de prijscategorie en een regio.

Na de implementatie is `http://<app name>.azurewebsites.net`uw app beschikbaar op .

Een **resourcegroep** is een benoemde verzameling van alle resources van uw toepassing in Azure. Een resourcegroep kan bijvoorbeeld een verwijzing bevatten naar een website, een database en een Azure-functie.

Een **App-serviceplan** definieert de fysieke bronnen die worden gebruikt om uw website te hosten. Deze quickstart maakt gebruik van een **Basic** hosting plan op **Linux** infrastructuur, wat betekent dat de site zal worden gehost op een Linux-machine naast andere websites. Als u begint met het **Basisplan,** u de Azure-portal gebruiken om op te schalen zodat de uwe de enige site is die op een machine wordt uitgevoerd.

## <a name="browse-the-website"></a>Blader door de website

Het deelvenster **Uitvoer** wordt tijdens de implementatie geopend om de status van de bewerking aan te geven. Wanneer de bewerking is voltooid, zoekt u de app die u hebt gemaakt in de **APP SERVICE-verkenner** en klikt u er met de rechtermuisknop op en selecteert u **Website bladeren** om de site in uw browser te openen.

> [!div class="nextstepaction"]
> [Ik liep in een probleem](https://www.research.net/r/PWZWZ52?tutorial=quickstart-docker&step=deploy-app)

## <a name="next-steps"></a>Volgende stappen

Gefeliciteerd, je hebt deze quickstart met succes voltooid!

Bekijk vervolgens de andere Azure-extensies.

* [Cosmos DB](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
* [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
* [Azure CLI-hulpprogramma's](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
* [Hulpprogramma's voor Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

Of haal ze allemaal door het [uitbreidingspakket van Azure Tools](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) te installeren.
