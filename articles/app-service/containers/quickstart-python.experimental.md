---
title: 'Snelstart: Python-app maken op Linux - Azure App Service'
description: Leer hoe u in een paar minuten uw eerste Python-app (Hello World) implementeert in Azure App Service on Linux.
services: app-service\web
documentationcenter: ''
author: msangapu-msft
ms.topic: quickstart
ms.date: 1/14/2020
ms.author: msangapu
ms.custom: seo-python-october2019
experimental: false
experiment_id: 01a9132f-eaab-4c
ms.openlocfilehash: 68dc36ce96737fe8395280c3a833e359084d2fee
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "78246858"
---
# <a name="quickstart-create-a-python-app-in-azure-app-service-on-linux"></a>Snelstart: een Python-app maken in Azure App Service op Linux

In deze quickstart implementeert u een Python-webapp [naar App Service op Linux](app-service-linux-intro.md), de zeer schaalbare, zelfpatchende webhostingservice van Azure. U gebruikt de lokale [Azure command-line interface (CLI)](/cli/azure/install-azure-cli) op een Mac, Linux of Windows-computer. De web-app die u configureert, maakt gebruik van een gratis appservicelaag, zodat u in de loop van dit artikel geen kosten hoeft te maken.

Zie [Python-apps implementeren naar app-service implementeren vanuit Visual Studio Code](/azure/python/tutorial-deploy-app-service-on-linux-01)als u apps liever implementeert via een IDE.

## <a name="prerequisites"></a>Vereisten

- Azure-abonnement - [maak er gratis een](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- <a href="https://www.python.org/downloads/" target="_blank">Python 3.7</a> (Python 3.6 wordt ook ondersteund)
- <a href="https://git-scm.com/downloads" target="_blank">Git</a>
- <a href="https://docs.microsoft.com/cli/azure/install-azure-cli" target="_blank">Azure-CLI</a>

## <a name="download-the-sample"></a>Het voorbeeld downloaden

Voer in een terminalvenster de volgende opdracht uit om de voorbeeldtoepassing naar uw lokale computer te klonen. 

```terminal
git clone https://github.com/Azure-Samples/python-docs-hello-world
```

Ga dan naar die map:

```terminal
cd python-docs-hello-world
```

De repository bevat een *application.py* bestand, dat App Service vertelt dat de code een Flask-app bevat. Zie [Opstartprocessen en aanpassingen van container](how-to-configure-python.md) voor meer informatie.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

De Azure CLI biedt u veel handige opdrachten die u gebruikt van een lokale terminal tot het inrichten en beheren van Azure-resources vanaf de opdrachtregel. U opdrachten gebruiken om dezelfde taken uit te voeren die u via de Azure-portal in een browser zou uitvoeren. U cli-opdrachten ook in scripts gebruiken om beheerprocessen te automatiseren.

Als u Azure-opdrachten wilt uitvoeren in azure cli, moet u zich eerst aanmelden met de `az login` opdracht. Met deze opdracht wordt een browser geopend om uw referenties te verzamelen.

```azurecli
az login
```

## <a name="deploy-the-sample"></a>Het voorbeeld implementeren

Met [`az webapp up`](/cli/azure/webapp#az-webapp-up) de opdracht wordt de web-app op App-service gemaakt en wordt uw code geïmplementeerd.

Voer in de map *python-docs-hello-world* met de `az webapp up` voorbeeldcode de volgende opdracht uit. Vervangen `<app-name>` door een wereldwijd unieke app-naam *(geldige tekens zijn `a-z`, `0-9`en `-` *). Ook `<location-name>` vervangen door een Azure regio zoals **centralus**, **eastasia**, **West-Europe**, **Koreasouth**, **Brazilsouth**, **centralindia**, en ga zo maar door. (U een lijst met toegestane regio's [`az account locations-list`](/cli/azure/appservice?view=azure-cli-latest.md#az-appservice-list-locations) voor uw Azure-account ophalen door de opdracht uit te voeren.)


```azurecli
az webapp up --sku F1 -n <app-name> -l <location-name>
```

Deze opdracht kan enkele minuten duren. De opdracht geeft informatie weer die lijkt op het volgende voorbeeld:

```output
The behavior of this command has been altered by the following extension: webapp
Creating Resource group 'appsvc_rg_Linux_centralus' ...
Resource group creation complete
Creating App service plan 'appsvc_asp_Linux_centralus' ...
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
  "resourcegroup": "appsvc_rg_Linux_centralus ",
  "serverfarm": "appsvc_asp_Linux_centralus",
  "sku": "BASIC",
  "src_path": "/home/username/quickstart/python-docs-hello-world ",
  "version_detected": "-",
  "version_to_create": "python|3.7"
}
```

[!INCLUDE [AZ Webapp Up Note](../../../includes/app-service-web-az-webapp-up-note.md)]

## <a name="browse-to-the-app"></a>Bladeren naar de app

Blader naar de geïmplementeerde toepassing in `http://<app-name>.azurewebsites.net`uw webbrowser op de URL.

De Python-voorbeeldcode wordt uitgevoerd met een Linux-container in App-service met behulp van een ingebouwde afbeelding.

![Een voorbeeld van python-app uitvoeren in Azure](./media/quickstart-python/run-hello-world-sample-python-app-in-browser.png)

**Gefeliciteerd!** U hebt uw Python-app geïmplementeerd in App Service op Linux.

## <a name="run-the-sample-app-locally"></a>De voorbeeld-app lokaal uitvoeren

Gebruik in een terminalvenster de onderstaande opdrachten (indien van toepassing voor uw besturingssysteem) om de vereiste afhankelijkheden te installeren en de ingebouwde ontwikkelingsserver te starten. 

# <a name="bash"></a>[Bash](#tab/bash)

```bash
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
FLASK_APP=application.py
flask run
```

# <a name="powershell"></a>[Powershell](#tab/powershell)

```powershell
py -3 -m venv env
env\scripts\activate
pip install -r requirements.txt
Set-Item Env:FLASK_APP ".\application.py"
flask run
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
py -3 -m venv env
env\scripts\activate
pip install -r requirements.txt
SET FLASK_APP=application.py
flask run
```

---

Open een webbrowser en ga naar `http://localhost:5000/`de voorbeeld-app op . De app toont het bericht **Hello World!**.

![Een voorbeeld van python-app lokaal uitvoeren](./media/quickstart-python/run-hello-world-sample-python-app-in-browser.png)

Druk in het terminalvenster op **Ctrl**+**C** om de webserver te verlaten.

## <a name="redeploy-updates"></a>Updates opnieuw implementeren

Open *in* uw favoriete codeeditor `return` application.py en wijzig de instructie op de laatste regel om overeen te komen met de volgende code. De `print` instructie is hier opgenomen om logboeken te genereren waarmee u werkt in de volgende sectie. 

```python
print("Handling request to home page.")
return "Hello Azure!"
```

Sla uw wijzigingen op en sluit de editor af. 

Implementeer de app opnieuw `az webapp up` met de volgende opdracht, met dezelfde opdracht die `<app-name>` u `<location-name>` de app de eerste keer hebt geïmplementeerd, waarbij u de app vervangt en dezelfde namen gebruikt die u eerder hebt gebruikt. 

```azurecli
az webapp up --sku F1 -n <app-name> -l <location-name>
```

Zodra de implementatie is voltooid, schakelt `http://<app-name>.azurewebsites.net` u terug naar het browservenster dat is geopend naar en vernieuwt u de pagina, die het gewijzigde bericht moet weergeven:

![Een bijgewerkte voorbeeld python-app uitvoeren in Azure](./media/quickstart-python/run-updated-hello-world-sample-python-app-in-browser.png)

> [!TIP]
> Visual Studio Code biedt krachtige extensies voor Python en Azure App Service, die het proces van het implementeren van Python-web-apps naar App Service vereenvoudigen. Zie [Python-apps implementeren voor App-service implementeren vanuit Visual Studio Code](/azure/python/tutorial-deploy-app-service-on-linux-01)voor meer informatie.

## <a name="stream-logs"></a>Logboeken streamen

U hebt toegang tot de consolelogboeken die vanuit de app worden gegenereerd en de container waarin deze wordt uitgevoerd. Logboeken bevatten alle `print` uitvoer die wordt gegenereerd met behulp van instructies.

Schakel eerst containerlogboekregistratie in door de volgende opdracht `<app-name>` in een terminal `<resource-group-name>` uit te voeren, te vervangen door `az webapp up` de naam van uw app en door de naam van de resourcegroep die wordt weergegeven in de uitvoer van de opdracht die u hebt gebruikt (zoals 'appsvc_rg_Linux_centralus'):

```azurecli
az webapp log config --name <app-name> --resource-group <resource-group-name> --docker-container-logging filesystem
```

Zodra containerlogboekregistratie is ingeschakeld, voert u de volgende opdracht uit om de logboekstroom weer te geven:

```azurecli
az webapp log tail --name <app-name> --resource-group <resource-group-name>
```

Vernieuw de app in de browser om consolelogboeken te genereren, die regels moeten bevatten die vergelijkbaar zijn met de volgende tekst. Als u de uitvoer niet onmiddellijk ziet, probeert u het opnieuw in 30 seconden.

```output
2019-10-23T12:40:03.815574424Z Handling request to home page.
2019-10-23T12:40:03.815602424Z 172.16.0.1 - - [23/Oct/2019:12:40:03 +0000] "GET / HTTP/1.1" 200 12 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/78.0.3904.63 Safari/537.36 Edg/78.0.276.19"
```

U ook de logbestanden `https://<app-name>.scm.azurewebsites.net/api/logs/docker`vanuit de browser bekijken op.

Typ op elk gewenst moment `Ctrl` + `C`het streamen van logboeken.

## <a name="manage-the-azure-app"></a>De Azure-app beheren

Ga naar <a href="https://portal.azure.com" target="_blank">Azure Portal</a> om de app te beheren die u hebt gemaakt. **App-services**zoeken en selecteren .

![Navigeren naar App Services in de Azure-portal](./media/quickstart-python/navigate-to-app-services-in-the-azure-portal.png)

Selecteer de naam van uw Azure-app.

![Navigeren naar uw Python-app in App Services in de Azure-portal](./media/quickstart-python/navigate-to-app-in-app-services-in-the-azure-portal.png)

De pagina Overzicht van uw app wordt weergegeven. Hier kunt u algemene beheertaken uitvoeren, zoals bladeren, stoppen, starten, opnieuw opstarten en verwijderen.

![Uw Python-app beheren op de pagina Overzicht in de Azure-portal](./media/quickstart-python/manage-an-app-in-app-services-in-the-azure-portal.png)

Het menu App-service bevat verschillende pagina's voor het configureren van uw app.

## <a name="clean-up-resources"></a>Resources opschonen

In de voorgaande stappen hebt u Azure-resources in een resourcegroep gemaakt. De resourcegroep heeft een naam als 'appsvc_rg_Linux_CentralUS' afhankelijk van uw locatie. Als u een Andere App Service SKU gebruikt dan de gratis F1-laag, zullen deze resources blijvende kosten met zich meebrengen.

Als u deze resources in de toekomst niet meer nodig verwacht, verwijdert u `<resource-group-name>` de brongroep door de `az webapp up` volgende opdracht uit te voeren en te vervangen door de resourcegroep die wordt weergegeven in de uitvoer van de opdracht, zoals 'appsvc_rg_Linux_centralus'. Het kan even duren voordat de opdracht is voltooid.

```azurecli
az group delete -n <resource-group-name>
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Zelfstudie: Python (Django) web-app met PostgreSQL](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [Python-app configureren](how-to-configure-python.md)

> [!div class="nextstepaction"]
> [Zelfstudie: Python-app uitvoeren in aangepaste container](tutorial-custom-docker-image.md)
