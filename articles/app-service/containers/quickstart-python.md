---
title: 'Snelstart: een Linux Python-app maken'
description: Ga aan de slag met Linux-apps in Azure App Service door uw eerste Python-app te implementeren in een Linux-container in App Service.
ms.topic: quickstart
ms.date: 04/03/2020
ms.custom: seo-python-october2019, cli-validate
experimental: true
experiment_id: 01a9132f-eaab-4c
ms.openlocfilehash: 63daecca710e0e4d7b3326cea59c0c025c24f619
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2020
ms.locfileid: "80811157"
---
# <a name="quickstart-create-a-python-app-in-azure-app-service-on-linux"></a>Snelstart: een Python-app maken in Azure App Service op Linux

In deze quickstart implementeert u een Python-webapp [naar App Service op Linux](app-service-linux-intro.md), de zeer schaalbare, zelfpatchende webhostingservice van Azure. U gebruikt de lokale [Azure command-line interface (CLI)](/cli/azure/install-azure-cli) op een Mac, Linux of Windows-computer. De web-app die u configureert, maakt gebruik van een gratis appservicelaag, zodat u in de loop van dit artikel geen kosten hoeft te maken.

Zie [Python-apps implementeren naar app-service implementeren vanuit Visual Studio Code](/azure/python/tutorial-deploy-app-service-on-linux-01)als u apps liever implementeert via een IDE.

## <a name="prerequisites"></a>Vereisten

- Azure-abonnement - [maak er gratis een](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- <a href="https://www.python.org/downloads/" target="_blank">Python 3.7</a> (Python 3.6 wordt ook ondersteund)
- <a href="https://git-scm.com/downloads" target="_blank">Git</a>
- <a href="https://docs.microsoft.com/cli/azure/install-azure-cli" target="_blank">Azure CLI</a> 2.0.80 of hoger. Voer `az --version` uit om uw versie te controleren.

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

## <a name="run-the-sample"></a>De voorbeeldtoepassing uitvoeren

Gebruik in een terminalvenster de onderstaande opdrachten (indien van toepassing voor uw besturingssysteem) om de vereiste afhankelijkheden te installeren en de ingebouwde ontwikkelingsserver te starten. 

# <a name="bash"></a>[Bash](#tab/bash)

```bash
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
export FLASK_APP=application.py
flask run
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

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

![Een voorbeeld van python-app lokaal uitvoeren](./media/quickstart-python/run-hello-world-sample-python-app-in-browser-localhost.png)

Druk in het terminalvenster op **Ctrl**+**C** om de webserver te verlaten.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

De Azure CLI biedt u veel handige opdrachten die u gebruikt van een lokale terminal tot het inrichten en beheren van Azure-resources vanaf de opdrachtregel. U opdrachten gebruiken om dezelfde taken uit te voeren die u via de Azure-portal in een browser zou uitvoeren. U cli-opdrachten ook in scripts gebruiken om beheerprocessen te automatiseren.

Als u Azure-opdrachten wilt uitvoeren in azure cli, moet u zich eerst aanmelden met de `az login` opdracht. Met deze opdracht wordt een browser geopend om uw referenties te verzamelen.

```azurecli
az login
```

## <a name="deploy-the-sample"></a>Het voorbeeld implementeren

Met [`az webapp up`](/cli/azure/webapp#az-webapp-up) de opdracht wordt de web-app op App-service gemaakt en wordt uw code geïmplementeerd.

Voer in de map *python-docs-hello-world* met de `az webapp up` voorbeeldcode de volgende opdracht uit. Vervangen `<app-name>` door een wereldwijd unieke app-naam *(geldige tekens zijn `a-z`, `0-9`en `-` *).


```azurecli
az webapp up --sku F1 -n <app-name>
```

Met `--sku F1` het argument wordt de web-app gemaakt op de prijscategorie Gratis. U dit argument weglaten om in plaats daarvan een premiumlaag te gebruiken, wat per uur kost.

U optioneel het `-l <location-name>` `<location_name>` argument opnemen waar een Azure-regio is, zoals **centralus,** **eastasia**, **West-Europe,** **Koreasouth,** **Brazilsouth,** **CentralIndia**, enzovoort. U een lijst met toegestane regio's [`az account list-locations`](/cli/azure/appservice?view=azure-cli-latest.md#az-appservice-list-locations) voor uw Azure-account ophalen door de opdracht uit te voeren.

Het `az webapp up` kan enkele minuten duren voordat de opdracht volledig is uitgevoerd. Tijdens het uitvoeren wordt informatie weergegeven die `<app_name>` vergelijkbaar is met het volgende voorbeeld, waar de eerder opgegeven naam zal zijn:

<pre>
Creating Resource group 'appsvc_rg_Linux_centralus' ...
Resource group creation complete
Creating App service plan 'appsvc_asp_Linux_centralus' ...
App service plan creation complete
Creating app '<app-name>' ....
Configuring default logging for the app, if not already enabled
Creating zip with contents of dir D:\Examples\python-docs-hello-world ...
Getting scm site credentials for zip deployment
Starting zip deployment. This operation can take a while to complete ...
Deployment endpoint responded with status code 202
You can launch the app at http://<app-name>.azurewebsites.net
{
  "URL": "http://<app-name>.net",
  "appserviceplan": "appsvc_asp_Linux_centralus",
  "location": "eastus",
  "name": "<app-name>",
  "os": "Linux",
  "resourcegroup": "appsvc_rg_Linux_centralus",
  "runtime_version": "python|3.7",
  "runtime_version_detected": "-",
  "sku": "FREE",
  "src_path": "D:\\Examples\\python-docs-hello-world"
}
</pre>

[!INCLUDE [AZ Webapp Up Note](../../../includes/app-service-web-az-webapp-up-note.md)]

## <a name="browse-to-the-app"></a>Bladeren naar de app

Blader naar de geïmplementeerde toepassing in `http://<app-name>.azurewebsites.net`uw webbrowser op de URL.

De Python-voorbeeldcode wordt uitgevoerd met een Linux-container in App-service met behulp van een ingebouwde afbeelding.

![Een voorbeeld van python-app uitvoeren in Azure](./media/quickstart-python/run-hello-world-sample-python-app-in-browser.png)

**Gefeliciteerd!** U hebt uw Python-app geïmplementeerd in App Service op Linux.

## <a name="redeploy-updates"></a>Updates opnieuw implementeren

Open *in* uw favoriete codeeditor `hello` application.py en werk de functie als volgt bij. Met deze `print` wijziging wordt een instructie toegevoegd om logboekuitvoer te genereren waarmee u in de volgende sectie werkt. 

```python
def hello():
    print("Handling request to home page.")
    return "Hello Azure!"
```

Sla uw wijzigingen op en sluit de editor af. 

Implementeer de app `az webapp up` opnieuw met de opdracht:

```azurecli
az webapp up
```

Met deze opdracht worden waarden gebruikt die in het *bestand .azure/config* zijn opgeslagen, waaronder de naam van de app, de brongroep en het App Service-abonnement.

Zodra de implementatie is voltooid, schakelt `http://<app-name>.azurewebsites.net` u terug naar het browservenster dat is geopend naar en vernieuwt u de pagina, die het gewijzigde bericht moet weergeven:

![Een bijgewerkte voorbeeld python-app uitvoeren in Azure](./media/quickstart-python/run-updated-hello-world-sample-python-app-in-browser.png)

> [!TIP]
> Visual Studio Code biedt krachtige extensies voor Python en Azure App Service, die het proces van het implementeren van Python-web-apps naar App Service vereenvoudigen. Zie [Python-apps implementeren voor App-service implementeren vanuit Visual Studio Code](/azure/python/tutorial-deploy-app-service-on-linux-01)voor meer informatie.

## <a name="stream-logs"></a>Logboeken streamen

U hebt toegang tot de consolelogboeken die vanuit de app worden gegenereerd en de container waarin deze wordt uitgevoerd. Logboeken bevatten alle `print` uitvoer die wordt gegenereerd met behulp van instructies.

Voer de volgende opdracht uit om logboeken te streamen:

```azurecli
az webapp log tail
```

Vernieuw de app in de browser om consolelogboeken te genereren, die regels moeten bevatten die vergelijkbaar zijn met de volgende tekst. Als u de uitvoer niet onmiddellijk ziet, probeert u het opnieuw in 30 seconden.

<pre>
2020-04-03T22:54:04.236405938Z Handling request to home page.
2020-04-03T22:54:04.236497641Z 172.16.0.1 - - [03/Apr/2020:22:54:04 +0000] "GET / HTTP/1.1" 200 12 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/81.0.4044.83 Safari/537.36 Edg/81.0.416.41"
</pre>

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

In de voorgaande stappen hebt u Azure-resources in een resourcegroep gemaakt. De resourcegroep heeft een naam als 'appsvc_rg_Linux_CentralUS' afhankelijk van uw locatie. Als u een SKU voor app-service gebruikt, andere dan de gratis F1-laag, gaan deze resources voor voortdurende kosten (zie [App Service-prijzen).](https://azure.microsoft.com/pricing/details/app-service/linux/)

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
