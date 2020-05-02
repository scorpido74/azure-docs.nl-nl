---
title: 'Snelstartgids: een Linux python-app maken'
description: Ga aan de slag met Linux-apps op Azure App Service door uw eerste python-app te implementeren in een Linux-container in App Service.
ms.topic: quickstart
ms.date: 04/03/2020
ms.custom: seo-python-october2019, cli-validate
ms.openlocfilehash: 5b055c3ed93d5f093295b52c7a28a73e242bfe75
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/01/2020
ms.locfileid: "82690884"
---
# <a name="quickstart-create-a-python-app-in-azure-app-service-on-linux"></a>Snelstartgids: een python-app maken in Azure App Service in Linux

In deze Quick Start implementeert u een Python-web-app voor [app service op Linux](app-service-linux-intro.md), de uiterst schaal bare webhostingservice met self-patch functie. U gebruikt de lokale [Azure-opdracht regel interface (CLI)](/cli/azure/install-azure-cli) op een Mac-, Linux-of Windows-computer. De web-app die u configureert, maakt gebruik van een gratis App Service laag, zodat u geen kosten in de loop van dit artikel opdoet.

Als u liever apps implementeert via een IDE, raadpleegt u [python-Apps implementeren in app service van Visual Studio code](/azure/python/tutorial-deploy-app-service-on-linux-01).

## <a name="prerequisites"></a>Vereisten

- Azure-abonnement: [Maak er gratis een](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- <a href="https://www.python.org/downloads/" target="_blank">Python 3,7</a> (python 3,6 wordt ook ondersteund)
- <a href="https://git-scm.com/downloads" target="_blank">Git</a>
- <a href="https://docs.microsoft.com/cli/azure/install-azure-cli" target="_blank">Azure cli</a> 2.0.80 of hoger. Voer `az --version` uit om uw versie te controleren.

## <a name="download-the-sample"></a>Het voorbeeld downloaden

Voer in een Terminal venster de volgende opdracht uit om de voorbeeld toepassing te klonen op uw lokale computer. 

```terminal
git clone https://github.com/Azure-Samples/python-docs-hello-world
```

Ga vervolgens naar die map:

```terminal
cd python-docs-hello-world
```

De opslag plaats bevat een *Application.py* -bestand dat aangeeft app service dat de code een kolf-app bevat. Zie [Opstartprocessen en aanpassingen van container](how-to-configure-python.md) voor meer informatie.

## <a name="run-the-sample"></a>De voorbeeldtoepassing uitvoeren

Gebruik in een Terminal venster de onderstaande opdrachten (afhankelijk van uw besturings systeem) om de vereiste afhankelijkheden te installeren en de ingebouwde ontwikkel server te starten. 

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

Open een webbrowser en ga naar de voor beeld-app op `http://localhost:5000/`. In de app wordt het bericht **Hallo wereld!** weer gegeven.

![Een voor beeld van een python-app lokaal uitvoeren](./media/quickstart-python/run-hello-world-sample-python-app-in-browser-localhost.png)

Druk in het Terminal venster op **CTRL**+**C** om de webserver af te sluiten.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

De Azure CLI biedt u een groot aantal handige opdrachten die u vanaf een lokale terminal kunt gebruiken om Azure-resources in te richten en te beheren vanaf de opdracht regel. U kunt opdrachten gebruiken om dezelfde taken uit te voeren als de Azure Portal in een browser. U kunt ook CLI-opdrachten in scripts gebruiken om beheer processen te automatiseren.

Als u Azure-opdrachten in de Azure CLI wilt uitvoeren, moet u zich eerst `az login` aanmelden met de opdracht. Met deze opdracht wordt een browser geopend om uw referenties te verzamelen.

```azurecli
az login
```

## <a name="deploy-the-sample"></a>Het voor beeld implementeren

Met [`az webapp up`](/cli/azure/webapp#az-webapp-up) de opdracht maakt u de web-app op app service en implementeert u de code.

Voer de volgende `az webapp up` opdracht uit in de map *python-docs-Hello-World* die de voorbeeld code bevat. Vervang `<app-name>` door een globaal unieke app-naam (*geldige tekens `a-z`zijn `0-9`, en `-` *).


```azurecli
az webapp up --sku F1 -n <app-name>
```
> [!CAUTION]
> Als u **Azure-CLI versie 2.5.0** gebruikt, is er sprake van `az webapp up` een regressie waarin bepaalde scenario's mislukken als de `-l <location-name>` para meter niet is opgenomen. Dit probleem wordt [hier bijgehouden](https://github.com/Azure/azure-cli/issues/13257).  
> 
>U kunt controleren welke versie van de Azure-CLI u met de `az --version` opdracht gebruikt.
>

Met `--sku F1` het argument maakt u de web-app op de prijs categorie gratis. U kunt dit argument weglaten voor het gebruik van een Premium-laag, waardoor kosten per uur in rekening worden gebracht.

U kunt `-l <location-name>` eventueel het argument toevoegen waarbij `<location_name>` een Azure-regio is, zoals **middenus**, **EastAsia**, **Europa West**, **koreasouth**, **brazilsouth**, **centralindia**, enzovoort. U kunt een lijst met toegestane regio's voor uw Azure-account ophalen door de [`az account list-locations`](/cli/azure/appservice?view=azure-cli-latest.md#az-appservice-list-locations) opdracht uit te voeren.

Het `az webapp up` kan enkele minuten duren voordat de opdracht volledig is uitgevoerd. Tijdens het uitvoeren wordt de informatie weer gegeven die vergelijkbaar is met het `<app-name>` volgende voor beeld, waarbij de naam is die u eerder hebt gegeven:

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

In de python-voorbeeld code wordt een Linux-container uitgevoerd in App Service met behulp van een ingebouwde installatie kopie.

![Een voor beeld van een python-app in azure uitvoeren](./media/quickstart-python/run-hello-world-sample-python-app-in-browser.png)

**Voltooid!** U hebt uw python-app geïmplementeerd op App Service op Linux.

## <a name="redeploy-updates"></a>Updates opnieuw implementeren

Open *Application.py* in uw favoriete code-editor en werk de `hello` functie als volgt bij. Met deze wijziging wordt `print` een instructie toegevoegd voor het genereren van logboek registratie-uitvoer die u in de volgende sectie gebruikt. 

```python
def hello():
    print("Handling request to home page.")
    return "Hello Azure!"
```

Sla uw wijzigingen op en sluit de editor af. 

Implementeer de app opnieuw met behulp van de `az webapp up` opdracht:

```azurecli
az webapp up
```

Met deze opdracht worden waarden gebruikt die in de cache zijn opgeslagen in het *. Azure/config* -bestand, met inbegrip van de app-naam, de resource groep en het app service plan.

Zodra de implementatie is voltooid, gaat u terug naar het browser venster `http://<app-name>.azurewebsites.net` open naar en vernieuwt u de pagina, waarin het gewijzigde bericht moet worden weer gegeven:

![Een bijgewerkte voor beeld-app voor python uitvoeren in azure](./media/quickstart-python/run-updated-hello-world-sample-python-app-in-browser.png)

> [!TIP]
> Visual Studio code biedt krachtige extensies voor python en Azure App Service, waarmee het proces van het implementeren van Python-web-apps naar App Service wordt vereenvoudigd. Zie voor meer informatie [python-Apps implementeren in app service van Visual Studio code](/azure/python/tutorial-deploy-app-service-on-linux-01).

## <a name="stream-logs"></a>Logboeken streamen

U hebt toegang tot de console logboeken die zijn gegenereerd in de app en de container waarin deze wordt uitgevoerd. Logboeken bevatten een uitvoer die `print` wordt gegenereerd met behulp van-instructies.

Voer de volgende opdracht uit om logboeken te streamen:

```azurecli
az webapp log tail
```

Vernieuw de app in de browser om console logboeken te genereren, die regels moeten bevatten die vergelijkbaar zijn met de volgende tekst. Als u de uitvoer niet onmiddellijk ziet, probeert u het over 30 seconden opnieuw.

<pre>
2020-04-03T22:54:04.236405938Z Handling request to home page.
2020-04-03T22:54:04.236497641Z 172.16.0.1 - - [03/Apr/2020:22:54:04 +0000] "GET / HTTP/1.1" 200 12 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/81.0.4044.83 Safari/537.36 Edg/81.0.416.41"
</pre>

U kunt de logboek bestanden ook vanuit de browser controleren op `https://<app-name>.scm.azurewebsites.net/api/logs/docker`.

Als u het streamen van Logboeken op `Ctrl` + `C`elk gewenst moment wilt stoppen, typt u.

## <a name="manage-the-azure-app"></a>De Azure-app beheren

Ga naar <a href="https://portal.azure.com" target="_blank">Azure Portal</a> om de app te beheren die u hebt gemaakt. Zoek en selecteer **app Services**.

![Navigeer naar App Services in het Azure Portal](./media/quickstart-python/navigate-to-app-services-in-the-azure-portal.png)

Selecteer de naam van uw Azure-app.

![Navigeer naar uw python-app in App Services in het Azure Portal](./media/quickstart-python/navigate-to-app-in-app-services-in-the-azure-portal.png)

De pagina Overzicht van uw app wordt weergegeven. Hier kunt u algemene beheertaken uitvoeren, zoals bladeren, stoppen, starten, opnieuw opstarten en verwijderen.

![Beheer uw python-app op de pagina overzicht in de Azure Portal](./media/quickstart-python/manage-an-app-in-app-services-in-the-azure-portal.png)

Het App Service menu bevat verschillende pagina's voor het configureren van uw app.

## <a name="clean-up-resources"></a>Resources opschonen

In de voorgaande stappen hebt u Azure-resources in een resourcegroep gemaakt. De resource groep heeft een naam als ' appsvc_rg_Linux_CentralUS ', afhankelijk van uw locatie. Als u een andere App Service SKU gebruikt dan de gratis F1-laag, nemen deze resources lopende kosten (Zie de [app service prijzen](https://azure.microsoft.com/pricing/details/app-service/linux/)).

Als u deze resources in de toekomst niet meer nodig hebt, verwijdert u de resource groep door de volgende opdracht uit te `<resource-group-name>` voeren, waarbij u vervangt door de resource groep die `az webapp up` wordt weer gegeven in de uitvoer van de opdracht, zoals "appsvc_rg_Linux_centralus". Het volt ooien van de opdracht kan een minuut duren.

```azurecli
az group delete -n <resource-group-name>
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Zelf studie: python (Django) Web-app met PostgreSQL](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [Gebruikers aanmelding toevoegen aan een Python-web-app](../../active-directory/develop/quickstart-v2-python-webapp.md)

> [!div class="nextstepaction"]
> [Python-app configureren](how-to-configure-python.md)

> [!div class="nextstepaction"]
> [Zelf studie: python-app uitvoeren in een aangepaste container](tutorial-custom-docker-image.md)
