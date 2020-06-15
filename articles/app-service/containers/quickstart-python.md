---
title: 'Quickstart: Een Python-app in Linux maken'
description: Ga aan de slag met Linux-apps op Azure App Service door uw eerste Python-app te implementeren in een Linux-container in App Service.
ms.topic: quickstart
ms.date: 04/03/2020
ms.custom: seo-python-october2019, cli-validate, tracking-python
ms.openlocfilehash: 622709098d8b45f6c7badfddd3006dc730a6bf14
ms.sourcegitcommit: 964af22b530263bb17fff94fd859321d37745d13
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/09/2020
ms.locfileid: "84561706"
---
# <a name="quickstart-create-a-python-app-in-azure-app-service-on-linux"></a>Quickstart: Een Python-app maken in Azure App Service op Linux

In deze quickstart implementeert u een Python-web-app op [App Service op Linux](app-service-linux-intro.md), een uiterst schaalbare webhostingservice van Azure. U gebruikt de lokale [Azure CLI (opdrachtregelinterface)](/cli/azure/install-azure-cli) op een Mac-, Linux- of Windows-computer. De web-app die u configureert, maakt gebruik van een gratis App Service-laag, zodat u geen kosten opdoet in de loop van dit artikel.

Als u liever apps wilt implementeren via een IDE, raadpleegt u [Python-apps implementeren in App Service vanuit Visual Studio Code](/azure/python/tutorial-deploy-app-service-on-linux-01).

## <a name="prerequisites"></a>Vereisten

- Azure-abonnement: [u kunt een gratis abonnement nemen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- <a href="https://www.python.org/downloads/" target="_blank">Python 3.7</a> (Python 3.6 wordt ook ondersteund)
- <a href="https://git-scm.com/downloads" target="_blank">Git</a>
- <a href="https://docs.microsoft.com/cli/azure/install-azure-cli" target="_blank">Azure CLI</a> 2.0.80 of hoger. Voer `az --version` uit om uw versie te controleren.

## <a name="download-the-sample"></a>Het voorbeeld downloaden

Voer in een terminalvenster de volgende opdracht uit om de voorbeeldtoepassing te klonen op uw lokale computer. 

```terminal
git clone https://github.com/Azure-Samples/python-docs-hello-world
```

Ga vervolgens naar die map:

```terminal
cd python-docs-hello-world
```

De opslagplaats bevat een bestand *application.py*, waarmee aan App Service wordt doorgegeven dat de code een Flask-app bevat. Zie [Opstartprocessen en aanpassingen van container](how-to-configure-python.md) voor meer informatie.

## <a name="run-the-sample"></a>De voorbeeldtoepassing uitvoeren

Gebruik in een terminalvenster de opdrachten hieronder (voor zover van toepassing voor uw besturingssysteem) om de vereiste afhankelijkheden te installeren en de ingebouwde ontwikkelserver te starten. 

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

Open een webbrowser en ga naar de voorbeeld-app op `http://localhost:5000/`. In de app wordt het bericht **Hallo wereld** weergegeven.

![Een Python-voorbeeld-app uitvoeren](./media/quickstart-python/run-hello-world-sample-python-app-in-browser-localhost.png)

Druk in uw terminalvenster op **Ctrl**+**C** om de webserver af te sluiten.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

De Azure CLI biedt u een groot aantal handige opdrachten die u vanaf een lokale terminal kunt gebruiken om Azure-resources in te richten en te beheren vanuit de opdrachtregel. U kunt opdrachten gebruiken om dezelfde taken uit te voeren als via Azure Portal in een browser. U kunt ook CLI-opdrachten in scripts gebruiken om beheerprocessen te automatiseren.

Als u Azure-opdrachten in de Azure CLI wilt uitvoeren, moet u zich eerst aanmelden met de opdracht `az login`. Met deze opdracht wordt een browser geopend om uw referenties te verzamelen.

```azurecli
az login
```

## <a name="deploy-the-sample"></a>Het voorbeeld implementeren

Met de [`az webapp up`](/cli/azure/webapp#az-webapp-up)-opdracht maakt u de web-app in App Service en implementeert u uw code.

Voer de volgende `az webapp up`-opdracht uit in de map *python-docs-hello-world* die de voorbeeldcode bevat. Vervang `<app-name>` door een wereldwijd unieke naam (*geldige tekens zijn `a-z`, `0-9` en `-`* ).


```azurecli
az webapp up --sku F1 -n <app-name>
```

Met het argument `--sku F1` maakt u de web-app in de prijscategorie Gratis. U kunt dit argument weglaten en in plaats daarvan een Premium-laag gebruiken, waarmee u kosten per uur in rekening worden gebracht.

U kunt optioneel het argument toevoegen `-l <location-name>` waarbij `<location_name>` een Azure-regio is, zoals **centralus**, **eastasia**, **westeurope**, **koreasouth**, **brazilsouth**, **centralindia**, enzovoort. U kunt een lijst met toegestane regio's voor uw Azure-account ophalen door de [`az account list-locations`](/cli/azure/appservice?view=azure-cli-latest.md#az-appservice-list-locations)-opdracht uit te voeren.

Het volledig uitvoeren van de `az webapp up`-opdracht kan even duren. Bij de uitvoering geeft de opdracht informatie weer die lijkt op het volgende voorbeeld, waarbij `<app-name>` de naam is die u eerder hebt opgegeven:

<pre>
Creating Resource group 'appsvc_rg_Linux_centralus' ...
Resource group creation complete
Creating App service plan 'appsvc_asp_Linux_centralus' ...
App service plan creation complete
Creating app '&lt;app-name&gt;' ....
Configuring default logging for the app, if not already enabled
Creating zip with contents of dir D:\Examples\python-docs-hello-world ...
Getting scm site credentials for zip deployment
Starting zip deployment. This operation can take a while to complete ...
Deployment endpoint responded with status code 202
You can launch the app at http://&lt;app-name&gt;.azurewebsites.net
{
  "URL": "http://&lt;app-name&gt;.net",
  "appserviceplan": "appsvc_asp_Linux_centralus",
  "location": "eastus",
  "name": "&lt;app-name&gt;",
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

Blader naar de geïmplementeerde toepassing in uw webbrowser op de URL `http://<app-name>.azurewebsites.net`.

Met de Python-voorbeeldcode wordt een Linux-container uitgevoerd in App Service via een ingebouwde installatiekopie.

![Een Python-voorbeeld-app uitvoeren in Azure](./media/quickstart-python/run-hello-world-sample-python-app-in-browser.png)

**Gefeliciteerd!** U hebt uw Python-app geïmplementeerd in App Service in Linux.

## <a name="redeploy-updates"></a>Updates opnieuw implementeren

Open in uw favoriete code-editor *application.py* en werk de `hello`-functie als volgt bij. Met deze wijziging wordt een `print`-instructie toegevoegd waarmee u logboekuitvoer genereert waarmee u in de volgende sectie gaat werken. 

```python
def hello():
    print("Handling request to home page.")
    return "Hello Azure!"
```

Sla uw wijzigingen op en sluit de editor af. 

Implementeer de app opnieuw met de `az webapp up`-opdracht:

```azurecli
az webapp up
```

Met deze opdracht worden waarden gebruikt die in de cache worden opgeslagen in het bestand *.azure/config*, met inbegrip van de app-naam, de resourcegroep en het App Service-plan.

Zodra de implementatie is voltooid, gaat u terug naar het browservenster waarin `http://<app-name>.azurewebsites.net` is geopend, en vernieuwt u de pagina waarin het gewijzigde bericht wordt weergegeven:

![Een bijgewerkte Python-voorbeeld-app uitvoeren in Azure](./media/quickstart-python/run-updated-hello-world-sample-python-app-in-browser.png)

> [!TIP]
> Visual Studio Code biedt krachtige extensies voor Python en Azure App Service, waarmee het implementatieproces van Python-web-apps in App Service wordt vereenvoudigd. Zie [Python-apps implementeren in App Service vanuit Visual Studio Code](/azure/python/tutorial-deploy-app-service-on-linux-01) voor meer informatie.

## <a name="stream-logs"></a>Logboeken streamen

U hebt toegang tot de consolelogboeken die zijn gegenereerd vanuit de app en de container waarin deze wordt uitgevoerd. Logboeken bevatten uitvoer die wordt gegenereerd met behulp van `print`-instructies.

Voer de volgende opdracht uit om logboeken te streamen:

```azurecli
az webapp log tail
```

Vernieuw de app in de browser om consolelogboeken te genereren die regels bevatten die vergelijkbaar zijn met de volgende tekst. Probeer het over 30 seconden opnieuw als u de uitvoer niet onmiddellijk ziet.

<pre>
2020-04-03T22:54:04.236405938Z Handling request to home page.
2020-04-03T22:54:04.236497641Z 172.16.0.1 - - [03/Apr/2020:22:54:04 +0000] "GET / HTTP/1.1" 200 12 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/81.0.4044.83 Safari/537.36 Edg/81.0.416.41"
</pre>

U kunt de logboekbestanden ook vanuit de browser controleren op `https://<app-name>.scm.azurewebsites.net/api/logs/docker`.

U kunt op elk gewenst moment `Ctrl`+`C` typen om te stoppen met logboekstreaming.

## <a name="manage-the-azure-app"></a>De Azure-app beheren

Ga naar <a href="https://portal.azure.com" target="_blank">Azure Portal</a> om de app te beheren die u hebt gemaakt. Zoek en selecteer **App Services**.

![Naar App Services navigeren in Azure Portal](./media/quickstart-python/navigate-to-app-services-in-the-azure-portal.png)

Selecteer de naam van uw Azure-app.

![Naar uw Python-app in App Services navigeren in Azure Portal](./media/quickstart-python/navigate-to-app-in-app-services-in-the-azure-portal.png)

De pagina Overzicht van uw app wordt weergegeven. Hier kunt u algemene beheertaken uitvoeren, zoals bladeren, stoppen, starten, opnieuw opstarten en verwijderen.

![Uw Python-app beheren op de pagina Overzicht in Azure Portal](./media/quickstart-python/manage-an-app-in-app-services-in-the-azure-portal.png)

Het App Service-menu biedt verschillende pagina's voor het configureren van uw app.

## <a name="clean-up-resources"></a>Resources opschonen

In de voorgaande stappen hebt u Azure-resources in een resourcegroep gemaakt. De naam van de resourcegroep lijkt op 'appsvc_rg_Linux_CentralUS', afhankelijk van uw locatie. Als u een andere App Service-SKU gebruikt dan de gratis F1-laag, worden voor deze resources doorlopende kosten in rekening gebracht (zie [App Service-prijzen](https://azure.microsoft.com/pricing/details/app-service/linux/)).

Als u verwacht deze resources in de toekomst niet meer nodig te hebben, verwijdert u de resourcegroep door de volgende opdracht uit te voeren, waarbij u `<resource-group-name>` vervangt door de resourcegroep die wordt weergegeven in de uitvoer van de `az webapp up`-opdracht, zoals 'appsvc_rg_Linux_centralus'. Het kan een minuut duren voordat de opdracht is voltooid.

```azurecli
az group delete -n <resource-group-name>
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Zelfstudie: Python-web-app (Django) met PostgreSQL](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [Gebruikersaanmelding toevoegen aan een Python-web-app](../../active-directory/develop/quickstart-v2-python-webapp.md)

> [!div class="nextstepaction"]
> [Python-app configureren](how-to-configure-python.md)

> [!div class="nextstepaction"]
> [Zelfstudie: Een Python-app in een aangepaste container uitvoeren](tutorial-custom-docker-image.md)
