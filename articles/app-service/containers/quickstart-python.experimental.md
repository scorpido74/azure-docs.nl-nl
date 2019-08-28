---
title: Een Python-app maken in Linux - Azure App Service | Microsoft Docs
description: Leer hoe u in een paar minuten uw eerste Python-app (Hello World) implementeert in Azure App Service on Linux.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 08/23/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 08b1b85b980f992e799fc5198891290ec0d55c5d
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70071007"
---
# <a name="create-a-python-app-in-azure-app-service-on-linux-preview"></a>Een Python-app maken in Azure App Service op Linux (preview)

[App Service onder Linux](app-service-linux-intro.md) biedt een uiterst schaalbare webhostingservice met self-patchfunctie onder het Linux-besturingssysteem. Deze quickstart laat zien hoe een Python-app bovenop de ingebouwde Python-installatiekopie (preview) in de App-service op Linux kan worden geïmplementeerd met behulp van [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview).

U kunt de stappen in dit artikel volgen met behulp van een Mac-, Windows- of Linux-computer.

![Voorbeeld-app die wordt uitgevoerd in Azure](media/quickstart-python/hello-world-in-browser.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="download-the-sample"></a>Het voorbeeld downloaden

Maak een map 'snelstart' in de Cloud Shell en ga er vervolgens naartoe.

```bash
mkdir quickstart

cd $HOME/quickstart
```

Voer vervolgens de volgende opdracht uit om de voorbeeld-app-opslagplaats te klonen naar de map 'snelstart'.

```bash
git clone https://github.com/Azure-Samples/python-docs-hello-world
```

De opdracht geeft informatie weer die lijkt op het volgende voorbeeld:

```bash
Cloning into 'python-docs-hello-world'...
remote: Enumerating objects: 43, done.
remote: Total 43 (delta 0), reused 0 (delta 0), pack-reused 43
Unpacking objects: 100% (43/43), done.
Checking connectivity... done.
```

## <a name="create-a-web-app"></a>Een web-app maken

Ga naar de map die de voorbeeldcode bevat en voer de opdracht `az webapp up` uit.

Vervang in het volgende voorbeeld *\<app_name>* door een unieke naam (geldige tekens zijn `a-z`, `0-9` en `-`).

```bash
cd python-docs-hello-world

az webapp up -n <app_name>
```

Het kan enkele minuten duren voor deze opdracht is uitgevoerd. De opdracht geeft informatie weer die lijkt op het volgende voorbeeld:

```json
The behavior of this command has been altered by the following extension: webapp
Creating Resource group 'appsvc_rg_Linux_CentralUS' ...
Resource group creation complete
Creating App service plan 'appsvc_asp_Linux_CentralUS' ...
App service plan creation complete
Creating app '<app_name>' ....
Webapp creation complete
Creating zip with contents of dir /home/username/quickstart/python-docs-hello-world ...
Preparing to deploy contents to app.
All done.
{
  "app_url": "https:/<app_name>.azurewebsites.net",
  "location": "Central US",
  "name": "<app_name>",
  "os": "Linux",
  "resourcegroup": "appsvc_rg_Linux_CentralUS ",
  "serverfarm": "appsvc_asp_Linux_CentralUS",
  "sku": "BASIC",
  "src_path": "/home/username/quickstart/python-docs-hello-world ",
  "version_detected": "-",
  "version_to_create": "python|3.7"
}
```

Met de opdracht `az webapp up` worden de volgende acties uitgevoerd:

- Er wordt een standaardresourcegroep gemaakt.

- Er wordt een standaardserviceplan voor de app gemaakt.

- Er wordt een app met de opgegeven naam gemaakt.

- Er worden via zip bestanden van de huidige werkmap naar de app [geïmplementeerd](https://docs.microsoft.com/azure/app-service/deploy-zip).

## <a name="browse-to-the-app"></a>Bladeren naar de app

Blader naar de geïmplementeerde toepassing via uw webbrowser.

```bash
http://<app_name>.azurewebsites.net
```

De Python-voorbeeldcode wordt uitgevoerd in een App Service op Linux met een ingebouwde installatiekopie.

![Voorbeeld-app die wordt uitgevoerd in Azure](media/quickstart-python/hello-world-in-browser.png)

**Gefeliciteerd!** U hebt uw eerste Python-app geïmplementeerd naar App Service op Linux.

## <a name="update-locally-and-redeploy-the-code"></a>De code lokaal bijwerken en opnieuw implementeren

Typ `code application.py` in Cloud Shell om de Cloud Shell-editor te openen.

![Code application.py](media/quickstart-python/code-applicationpy.png)

 Breng een kleine wijziging aan in de tekst in de aanroep voor `return`:

```python
return "Hello Azure!"
```

Sla uw wijzigingen op en sluit de editor af. Sla op met de opdracht `^S` en sluit af met `^Q`.

U gaat nu de app opnieuw implementeren. Vervang `<app_name>` door uw app.

```bash
az webapp up -n <app_name>
```

Wanneer de implementatie is voltooid, gaat u terug naar het browservenster dat is geopend in de stap **Bladeren naar de app** en vernieuwt u de pagina.

![Bijgewerkte voorbeeld-app die wordt uitgevoerd in Azure](media/quickstart-python/hello-azure-in-browser.png)

## <a name="manage-your-new-azure-app"></a>Uw nieuwe Azure-app beheren

Ga naar <a href="https://portal.azure.com" target="_blank">Azure Portal</a> om de app te beheren die u hebt gemaakt.

Klik in het linkermenu op **App Services** en klik op de naam van uw Azure-app.

![Navigatie naar Azure-app in de portal](./media/quickstart-python/app-service-list.png)

De pagina Overzicht van uw app wordt weergegeven. Hier kunt u algemene beheertaken uitvoeren, zoals bladeren, stoppen, starten, opnieuw opstarten en verwijderen.

![App Service-pagina in Azure Portal](media/quickstart-python/app-service-detail.png)

Het linkermenu bevat een aantal pagina's voor het configureren van uw app. 

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Volgende stappen

De ingebouwde python-installatie kopie in App Service op Linux is momenteel beschikbaar als preview-versie en u kunt de opdracht aanpassen die wordt gebruikt om uw app te starten. U kunt ook in plaats daarvan productie-Python-apps maken met behulp van een aangepaste container.

> [!div class="nextstepaction"]
> [Zelfstudie: Python-app met PostgreSQL](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [Python-app configureren](how-to-configure-python.md)

> [!div class="nextstepaction"]
> [Zelfstudie: Implementeren vanuit een persoonlijke container opslagplaats](tutorial-custom-docker-image.md)
