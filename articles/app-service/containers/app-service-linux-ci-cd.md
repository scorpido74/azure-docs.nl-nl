---
title: CI/CD naar aangepaste Linux-containers
description: Meer informatie over het instellen van continue implementatie naar een aangepaste Linux-container in Azure App Service. Continue implementatie wordt ondersteund voor docker hub en ACR.
keywords: Azure app service, Linux, docker, ACR, oss
author: msangapu-msft
ms.assetid: a47fb43a-bbbd-4751-bdc1-cd382eae49f8
ms.topic: article
ms.date: 11/08/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: d43491de7500204ed470757a1b744017a8180b57
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2019
ms.locfileid: "74687638"
---
# <a name="continuous-deployment-with-web-app-for-containers"></a>Doorlopende implementatie met Web App for Containers

In deze zelf studie configureert u continue implementatie voor een aangepaste container installatie kopie van beheerde [Azure container Registry](https://azure.microsoft.com/services/container-registry/) -opslag plaatsen of [docker-hub](https://hub.docker.com).

## <a name="enable-continuous-deployment-with-acr"></a>Continue implementatie met ACR inschakelen

![Scherm opname van ACR-webhook](./media/app-service-webapp-service-linux-ci-cd/ci-cd-acr-02.png)

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
2. Selecteer de optie **app service** aan de linkerkant van de pagina.
3. Selecteer de naam van de app waarvoor u een continue implementatie wilt configureren.
4. Selecteer op de pagina **container instellingen** de optie **Eén container**
5. **Azure container Registry** selecteren
6. Selecteer **doorlopende implementatie > op**
7. Selecteer **Opslaan** om continue implementatie in te scha kelen.

## <a name="use-the-acr-webhook"></a>De ACR-webhook gebruiken

Zodra de continue implementatie is ingeschakeld, kunt u de zojuist gemaakte webhook weer geven op de pagina webhooks van Azure Container Registry.

![Scherm opname van ACR-webhook](./media/app-service-webapp-service-linux-ci-cd/ci-cd-acr-03.png)

Klik in uw Container Registry op webhooks om de huidige webhooks weer te geven.

## <a name="enable-continuous-deployment-with-docker-hub-optional"></a>Continue implementatie met docker hub inschakelen (optioneel)

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
2. Selecteer de optie **app service** aan de linkerkant van de pagina.
3. Selecteer de naam van de app waarvoor u een continue implementatie wilt configureren.
4. Selecteer op de pagina **container instellingen** de optie **Eén container**
5. **Docker hub** selecteren
6. Selecteer **doorlopende implementatie > op**
7. Selecteer **Opslaan** om continue implementatie in te scha kelen.

![Scherm afbeelding van de app-instelling](./media/app-service-webapp-service-linux-ci-cd/ci-cd-docker-02.png)

Kopieer de webhook-URL. Als u een webhook voor docker hub wilt toevoegen, volgt u <a href="https://docs.docker.com/docker-hub/webhooks/" target="_blank">webhooks voor docker hub</a>.

## <a name="next-steps"></a>Volgende stappen

* [Inleiding tot Azure App Service in Linux](./app-service-linux-intro.md)
* [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)
* [Een .NET Core-web-app maken in App Service in Linux](quickstart-dotnetcore.md)
* [Een Ruby-Web-app maken in App Service in Linux](quickstart-ruby.md)
* [Een docker/go-web-app implementeren in Web App for Containers](quickstart-docker-go.md)
* [Veelgestelde vragen over Azure App Service on Linux](./app-service-linux-faq.md)
* [Web App for Containers beheren met behulp van Azure CLI](./app-service-linux-cli.md)
