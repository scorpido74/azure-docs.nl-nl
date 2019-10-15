---
title: 'Quick Start: een python-app maken op Linux-Azure App Service'
description: Leer hoe u in een paar minuten uw eerste Python-app (Hello World) implementeert in Azure App Service on Linux.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 08/23/2019
ms.author: cephalin
ms.custom: seo-python-october2019
experimental: true
experiment_id: 1e304dc9-5add-4b
ms.openlocfilehash: 7a0aded07c2cd4da6ada72bdb967b1d485a56b85
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72329974"
---
# <a name="create-a-python-app-in-azure-app-service-on-linux"></a>Een python-app maken in Azure App Service in Linux

In dit artikel implementeert u een eenvoudige python-app voor [app service op Linux](app-service-linux-intro.md), die een uiterst schaal bare webhostingservice met self-patch functie biedt. U gebruikt de Azure-opdracht regel interface (de [Azure cli](/cli/azure/install-azure-cli)) via het interactieve, op een browser gebaseerde Azure Cloud shell, zodat u de stappen kunt volgen op een Mac-, Linux-of Windows-computer.

![Voorbeeld-app die wordt uitgevoerd in Azure](media/quickstart-python/hello-world-in-browser.png)

## <a name="prerequisites"></a>Vereisten

Dit zijn de vereisten voor het voltooien van deze snelstartgids:

* <a href="https://www.python.org/downloads/" target="_blank">Python 3.7 installeren</a>
* <a href="https://git-scm.com/" target="_blank">Git installeren</a>
* Een Azure-abonnement. Als u er nog geen hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) voordat u begint.

## <a name="download-the-sample-locally"></a>Het voorbeeld lokaal downloaden

Voer in een Terminal venster de volgende opdrachten uit om de voorbeeld toepassing te klonen op uw lokale computer en ga naar de map met de voorbeeld code.

```bash
git clone https://github.com/Azure-Samples/python-docs-hello-world
cd python-docs-hello-world
```

De opslagplaats bevat een *application.py*, die App Service laat weten dat de opslagplaats een Flask-app bevat. Zie [Opstartprocessen en aanpassingen van container](how-to-configure-python.md) voor meer informatie.

## <a name="run-the-app-locally"></a>De app lokaal uitvoeren

Voer de toepassing lokaal uit zodat u kunt zien hoe deze eruit ziet wanneer u de toepassing implementeert naar Azure. Open een terminalvenster en gebruik de opdrachten hieronder om de vereiste afhankelijkheden te installeren en de ingebouwde ontwikkelserver te starten. 

```bash
# In Bash (for Linux or Mac)
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
FLASK_APP=application.py flask run
```
```powershell
# In Powershell (for Windows)
py -3 -m venv env
env\scripts\activate
pip install -r requirements.txt
Set-Item Env:FLASK_APP ".\application.py"
flask run
```

Open een webbrowser en ga naar de voor beeld-app op `http://localhost:5000/`.

Het bericht **Hallo wereld** uit de voorbeeld-app wordt weergegeven op de pagina.

![Voorbeeld-app die lokaal wordt uitgevoerd](media/quickstart-python/hello-world-in-browser.png)

Druk in uw terminalvenster op **Ctrl + C** om de webserver af te sluiten.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="download-the-sample"></a>Het voorbeeld downloaden

Maak een map 'quickstart' in de Cloud Shell en ga er vervolgens naartoe.

```bash
mkdir quickstart

cd $HOME/quickstart
```

Voer vervolgens de volgende opdracht uit om de voorbeeld-app-opslagplaats te klonen naar de map 'quickstart'.

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

## <a name="create-a-web-app"></a>Een webtoepassing maken

Ga naar de map die de voorbeeldcode bevat en voer de opdracht `az webapp up` uit.

Vervang in het volgende voor beeld `<app-name>` door de naam van een wereld wijd unieke app (*geldige tekens zijn `a-z`, `0-9` en `-`* ).

```bash
cd python-docs-hello-world

az webapp up -n <app-name>
```

Het kan enkele minuten duren voor deze opdracht is uitgevoerd. De opdracht geeft informatie weer die lijkt op het volgende voorbeeld:

```json
The behavior of this command has been altered by the following extension: webapp
Creating Resource group 'appsvc_rg_Linux_CentralUS' ...
Resource group creation complete
Creating App service plan 'appsvc_asp_Linux_CentralUS' ...
App service plan creation complete
Creating app '<app-name>' ....
Webapp creation complete
Creating zip with contents of dir /home/username/quickstart/python-docs-hello-world ...
Preparing to deploy contents to app.
All done.
{
  "app_url": "https:/<app-name>.azurewebsites.net",
  "location": "Central US",
  "name": "<app-name>",
  "os": "Linux",
  "resourcegroup": "appsvc_rg_Linux_CentralUS ",
  "serverfarm": "appsvc_asp_Linux_CentralUS",
  "sku": "BASIC",
  "src_path": "/home/username/quickstart/python-docs-hello-world ",
  "version_detected": "-",
  "version_to_create": "python|3.7"
}
```

[!INCLUDE [AZ Webapp Up Note](../../../includes/app-service-web-az-webapp-up-note.md)]

## <a name="browse-to-the-app"></a>Bladeren naar de app

Blader naar de geïmplementeerde toepassing via uw webbrowser.

```bash
http://<app-name>.azurewebsites.net
```

De Python-voorbeeldcode wordt uitgevoerd in een App Service op Linux met een ingebouwde installatiekopie.

![Voorbeeld-app die wordt uitgevoerd in Azure](media/quickstart-python/hello-world-in-browser.png)

**Gefeliciteerd!** U hebt uw eerste Python-app geïmplementeerd naar App Service op Linux.

## <a name="update-locally-and-redeploy-the-code"></a>De code lokaal bijwerken en opnieuw implementeren

Voer in het Cloud Shell `code application.py` in om de Cloud Shell editor te openen.

![Code application.py](media/quickstart-python/code-applicationpy.png)

 Breng een kleine wijziging aan in de tekst in de aanroep voor `return`:

```python
return "Hello Azure!"
```

Sla uw wijzigingen op en sluit de editor af. Sla op met de opdracht `^S` en sluit af met `^Q`.

Implementeer de app opnieuw met behulp van de opdracht [`az webapp up`](/cli/azure/webapp#az-webapp-up) . Vervang de naam van uw app door `<app-name>` en geef een locatie op voor `<location-name>` (met behulp van een van de waarden die worden weer gegeven in de [`az account list-locations`-](/cli/azure/appservice?view=azure-cli-latest.md#az-appservice-list-locations) opdracht).

```bash
az webapp up -n <app-name> -l <location-name>
```

Wanneer de implementatie is voltooid, gaat u terug naar het browservenster dat is geopend in de stap **Bladeren naar de app** en vernieuwt u de pagina.

![Bijgewerkte voorbeeld-app die wordt uitgevoerd in Azure](media/quickstart-python/hello-azure-in-browser.png)

## <a name="manage-your-new-azure-app"></a>Uw nieuwe Azure-app beheren

Ga naar <a href="https://portal.azure.com" target="_blank">Azure Portal</a> om de app te beheren die u hebt gemaakt.

Selecteer in het linkermenu **App Services** en selecteer de naam van uw Azure-app.

![Navigatie naar Azure-app in de portal](./media/quickstart-python/app-service-list.png)

De pagina Overzicht van uw app wordt weergegeven. Hier kunt u algemene beheertaken uitvoeren, zoals bladeren, stoppen, starten, opnieuw opstarten en verwijderen.

![App Service-pagina in Azure Portal](media/quickstart-python/app-service-detail.png)

Het linkermenu bevat een aantal pagina's voor het configureren van uw app. 

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Zelf studie: python (Django) Web-app met PostgreSQL](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [Python-app configureren](how-to-configure-python.md)

> [!div class="nextstepaction"]
> [Zelf studie: python-app uitvoeren in een aangepaste container](tutorial-custom-docker-image.md)
