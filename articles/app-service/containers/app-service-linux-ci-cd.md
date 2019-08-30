---
title: Doorlopende implementatie met Web App for Containers-Azure App Service | Microsoft Docs
description: Continue implementatie instellen in Web App for Containers.
keywords: Azure app service, Linux, docker, ACR, oss
services: app-service
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
ms.assetid: a47fb43a-bbbd-4751-bdc1-cd382eae49f8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/08/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 1dc776f0a61ac1a29ab3fe3ebdd542469863cd50
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70071358"
---
# <a name="continuous-deployment-with-web-app-for-containers"></a>Doorlopende implementatie met Web App for Containers

In deze zelf studie configureert u continue implementatie voor een aangepaste container installatie kopie van beheerde [Azure container Registry](https://azure.microsoft.com/services/container-registry/) -opslag plaatsen of docker- [hub](https://hub.docker.com).

## <a name="enable-continuous-deployment-with-acr"></a>Continue implementatie met ACR inschakelen

![Scherm opname van ACR-webhook](./media/app-service-webapp-service-linux-ci-cd/ci-cd-acr-02.png)

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
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

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Selecteer de optie **app service** aan de linkerkant van de pagina.
3. Selecteer de naam van de app waarvoor u een continue implementatie wilt configureren.
4. Selecteer op de pagina **container instellingen** de optie **Eén container**
5. **Docker hub** selecteren
6. Selecteer **doorlopende implementatie > op**
7. Selecteer **Opslaan** om continue implementatie in te scha kelen.

![Scherm afbeelding van de app-instelling](./media/app-service-webapp-service-linux-ci-cd/ci-cd-docker-02.png)

Kopieer de webhook-URL. Als u een webhook voor docker hub wilt toevoegen, volgt u webhooks <a href="https://docs.docker.com/docker-hub/webhooks/" target="_blank">voor docker hub</a>.

## <a name="next-steps"></a>Volgende stappen

* [Inleiding tot Azure App Service in Linux](./app-service-linux-intro.md)
* [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)
* [Een .NET Core-web-app maken in App Service in Linux](quickstart-dotnetcore.md)
* [Een Ruby-Web-app maken in App Service in Linux](quickstart-ruby.md)
* [Een docker/go-web-app implementeren in Web App for Containers](quickstart-docker-go.md)
* [Veelgestelde vragen over Azure App Service on Linux](./app-service-linux-faq.md)
* [Web App for Containers beheren met behulp van Azure CLI](./app-service-linux-cli.md)
