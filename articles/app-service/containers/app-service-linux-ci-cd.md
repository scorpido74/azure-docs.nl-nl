---
title: CI/CD naar aangepaste Linux containers
description: Meer informatie over het instellen van continue implementatie op een aangepaste Linux-container in Azure App Service. Continue implementatie wordt ondersteund voor Docker Hub en ACR.
keywords: azure app service, linux, docker, acr, oss
author: msangapu-msft
ms.assetid: a47fb43a-bbbd-4751-bdc1-cd382eae49f8
ms.topic: article
ms.date: 11/08/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: d43491de7500204ed470757a1b744017a8180b57
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74687638"
---
# <a name="continuous-deployment-with-web-app-for-containers"></a>Doorlopende implementatie met Web App for Containers

In deze zelfstudie configureert u continue implementatie voor een aangepaste containerafbeelding van beheerde [Azure Container Registry-repositories](https://azure.microsoft.com/services/container-registry/) of [Docker Hub.](https://hub.docker.com)

## <a name="enable-continuous-deployment-with-acr"></a>Continue implementatie inschakelen met ACR

![Schermafbeelding van ACR-webhook](./media/app-service-webapp-service-linux-ci-cd/ci-cd-acr-02.png)

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Selecteer de optie **App-service** aan de linkerkant van de pagina.
3. Selecteer de naam van de app waarvoor u continue implementatie wilt configureren.
4. Selecteer op de pagina **Containerinstellingen** de optie **Eén container**
5. **Azure-containerregister selecteren**
6. Selecteer **> Continue implementatie op**
7. Selecteer **Opslaan** om continue implementatie in te schakelen.

## <a name="use-the-acr-webhook"></a>Gebruik de ACR webhook

Zodra Continue implementatie is ingeschakeld, u de nieuw gemaakte webhook bekijken op uw Azure Container Registry webhooks-pagina.

![Schermafbeelding van ACR-webhook](./media/app-service-webapp-service-linux-ci-cd/ci-cd-acr-03.png)

Klik in uw containerregister op Webhooks om de huidige webhooks weer te geven.

## <a name="enable-continuous-deployment-with-docker-hub-optional"></a>Continue implementatie inschakelen met Docker Hub (optioneel)

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Selecteer de optie **App-service** aan de linkerkant van de pagina.
3. Selecteer de naam van de app waarvoor u continue implementatie wilt configureren.
4. Selecteer op de pagina **Containerinstellingen** de optie **Eén container**
5. **DockerHub selecteren**
6. Selecteer **> Continue implementatie op**
7. Selecteer **Opslaan** om continue implementatie in te schakelen.

![Schermafbeelding van app-instelling](./media/app-service-webapp-service-linux-ci-cd/ci-cd-docker-02.png)

Kopieer de WEBhook-URL. Als u een webhook voor Docker Hub wilt toevoegen, volgt u <a href="https://docs.docker.com/docker-hub/webhooks/" target="_blank">webhooks voor Docker Hub.</a>

## <a name="next-steps"></a>Volgende stappen

* [Inleiding tot App Service onder Linux](./app-service-linux-intro.md)
* [Azure-containerregister](https://azure.microsoft.com/services/container-registry/)
* [Een .NET Core-web-app maken in App Service in Linux](quickstart-dotnetcore.md)
* [Een Ruby-web-app maken in App-service op Linux](quickstart-ruby.md)
* [Een Docker/Go-web-app in Web App for Containers implementeren](quickstart-docker-go.md)
* [Veelgestelde vragen over Azure App Service on Linux](./app-service-linux-faq.md)
* [Web App for Containers beheren met behulp van Azure CLI](./app-service-linux-cli.md)
