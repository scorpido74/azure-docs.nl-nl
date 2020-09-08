---
title: 'Quickstart: Een Python-app maken'
description: Ga aan de slag met Azure App Service door uw eerste Python-app te implementeren in een Linux-container in App Service.
ms.topic: quickstart
ms.date: 06/30/2020
ms.custom: seo-python-october2019, cli-validate, devx-track-python
ms.openlocfilehash: 3bfe927b2c9dc16de8712f6c1793e850df92c201
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2020
ms.locfileid: "89078449"
---
# <a name="quickstart-create-a-python-app-in-azure-app-service-on-linux"></a>Quickstart: Een Python-app maken in Azure App Service op Linux

In deze quickstart implementeert u een Python-web-app op [App Service op Linux](overview.md#app-service-on-linux), een uiterst schaalbare webhostingservice van Azure. U gebruikt de lokale [Azure CLI (opdrachtregelinterface)](/cli/azure/install-azure-cli) op een Mac-, Linux- of Windows-computer. De web-app die u configureert, maakt gebruik van een gratis App Service-laag, zodat u geen kosten opdoet in de loop van dit artikel.

Als u liever apps wilt implementeren via een IDE, raadpleegt u [Python-apps implementeren in App Service vanuit Visual Studio Code](/azure/developer/python/tutorial-deploy-app-service-on-linux-01).

## <a name="set-up-your-initial-environment"></a>Uw eerste omgeving instellen

Voordat u begint, moet u het volgende hebben:

1. U moet beschikken over een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
1. Installeer <a href="https://www.python.org/downloads/" target="_blank">Python 3.6 of hoger</a>.
1. Installeer de <a href="/cli/azure/install-azure-cli" target="_blank">Azure CLI</a> 2.0.80 of hoger, waarmee u opdrachten kunt uitvoeren in elke willekeurige shell voor het inrichten en configureren van Azure-resources.

Open een terminalvenster en controleer of uw Python-versie 3.6 of hoger is:

# <a name="bash"></a>[Bash](#tab/bash)

```bash
python3 --version
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```cmd
py -3 --version
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
py -3 --version
```

---

Controleer of uw Azure CLI-versie 2.0.80 of hoger is:

```azurecli
az --version
```

Meld u vervolgens aan bij Azure via de CLI:

```azurecli
az login
```

Met deze opdracht wordt een browser geopend om uw referenties te verzamelen. Wanneer de opdracht is voltooid, wordt JSON-uitvoer weergegeven met informatie over uw abonnementen.

Zodra u bent aangemeld, kunt u Azure-opdrachten uitvoeren met de Azure CLI om te werken met resources in uw abonnement.

[Ondervindt u problemen? Laat het ons weten.](https://aka.ms/FlaskCLIQuickstartHelp)

## <a name="clone-the-sample"></a>Het voorbeeld klonen

Kloon de voorbeeldopslagplaats met de volgende opdracht. ([Installeer git](https://git-scm.com/downloads) als u nog geen git hebt.)

```terminal
git clone https://github.com/Azure-Samples/python-docs-hello-world
```

Ga vervolgens naar die map:

```terminal
cd python-docs-hello-world
```

De voorbeeldcode bevat een bestand *application.py*, waarmee aan App Service wordt doorgegeven dat de code een Flask-app bevat. Zie [Opstartprocessen van container](configure-language-python.md#container-startup-process) voor meer informatie.

[Ondervindt u problemen? Laat het ons weten.](https://aka.ms/FlaskCLIQuickstartHelp)

## <a name="run-the-sample"></a>De voorbeeldtoepassing uitvoeren

# <a name="bash"></a>[Bash](#tab/bash)

Maak eerst een virtuele omgeving en installeer afhankelijkheden:

```bash
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
```

Stel vervolgens de `FLASK_APP`-omgevingsvariabele in op de invoermodule van de app en voer de Flask-ontwikkelingsserver uit:

```
export FLASK_APP=application.py
flask run
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Maak eerst een virtuele omgeving en installeer afhankelijkheden:

```powershell
py -3 -m venv env
env\scripts\activate
pip install -r requirements.txt
```

Stel vervolgens de `FLASK_APP`-omgevingsvariabele in op de invoermodule van de app en voer de Flask-ontwikkelingsserver uit:

```powershell
Set-Item Env:FLASK_APP ".\application.py"
flask run
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

Maak eerst een virtuele omgeving en installeer afhankelijkheden:

```cmd
py -3 -m venv env
env\scripts\activate
pip install -r requirements.txt
```

Stel vervolgens de `FLASK_APP`-omgevingsvariabele in op de invoermodule van de app en voer de Flask-ontwikkelingsserver uit:

```cmd
SET FLASK_APP=application.py
flask run
```

---

Open een webbrowser en ga naar de voorbeeld-app op `http://localhost:5000/`. In de app wordt het bericht **Hallo wereld** weergegeven.

![Een Python-voorbeeld-app uitvoeren](./media/quickstart-python/run-hello-world-sample-python-app-in-browser-localhost.png)

Druk in het terminalvenster op **Ctrl**+**C** om de Flask-ontwikkelingsserver af te sluiten.

[Ondervindt u problemen? Laat het ons weten.](https://aka.ms/FlaskCLIQuickstartHelp)

## <a name="deploy-the-sample"></a>Het voorbeeld implementeren

Implementeer de code in uw lokale map (*python-docs-hello-world*) met behulp van de `az webapp up`-opdracht:

```azurecli
az webapp up --sku F1 -n <app-name>
```

- Als de `az`-opdracht niet wordt herkend, controleert u of de Azure CLI is geïnstalleerd volgens de beschrijving in [Uw initiële omgeving instellen](#set-up-your-initial-environment).
- Vervang `<app_name>` door een naam die in de volledige Azure-omgeving uniek is (*geldige tekens zijn `a-z`, `0-9` en `-`* ). Het is handig om een combinatie van uw bedrijfsnaam en een app-id te gebruiken.
- Met het argument `--sku F1` maakt u de web-app in de prijscategorie Gratis. Laat dit argument weg om een snellere Premium-laag te gebruiken, waarmee u kosten per uur in rekening worden gebracht.
- U kunt optioneel het argument toevoegen `-l <location-name>` waarbij `<location_name>` een Azure-regio is, zoals **centralus**, **eastasia**, **westeurope**, **koreasouth**, **brazilsouth**, **centralindia**, enzovoort. U kunt een lijst met toegestane regio's voor uw Azure-account ophalen door de [`az account list-locations`](/cli/azure/appservice?view=azure-cli-latest.md#az-appservice-list-locations)-opdracht uit te voeren.
- Als u de fout "Kan de runtimestack van uw app niet automatisch detecteren" wordt weergegeven, moet u controleren of u de opdracht uitvoert in de map *python-docs-hello-world* die het bestand *requirements.txt* bevat. (Zie [Problemen met automatische detectie oplossen met az webapp up](https://github.com/Azure/app-service-linux-docs/blob/master/AzWebAppUP/runtime_detection.md) (GitHub) voor meer informatie.)

Het volledig uitvoeren van de opdracht kan even duren. Terwijl de opdracht wordt uitgevoerd, krijgt u berichten over het maken van de resourcegroep, het App Service-plan en de hosting-app, het configureren van de logboekfunctie en het vervolgens uitvoeren van de ZIP-implementatie. Vervolgens krijgt u het volgende bericht: 'U kunt de app starten op http://&lt;app-name&gt;.azurewebsites.net'. Dit is de URL van de app op Azure.

![Voorbeelduitvoer van de opdracht az webapp up](./media/quickstart-python/az-webapp-up-output.png)

[Ondervindt u problemen? Laat het ons weten.](https://aka.ms/FlaskCLIQuickstartHelp)

[!INCLUDE [AZ Webapp Up Note](../../includes/app-service-web-az-webapp-up-note.md)]

## <a name="browse-to-the-app"></a>Bladeren naar de app

Blader naar de geïmplementeerde toepassing in uw webbrowser op de URL `http://<app-name>.azurewebsites.net`.

Met de Python-voorbeeldcode wordt een Linux-container uitgevoerd in App Service via een ingebouwde installatiekopie.

![Een Python-voorbeeld-app uitvoeren in Azure](./media/quickstart-python/run-hello-world-sample-python-app-in-browser.png)

**Gefeliciteerd!** U hebt uw Python-app geïmplementeerd in App Service.

[Ondervindt u problemen? Laat het ons weten.](https://aka.ms/FlaskCLIQuickstartHelp)

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

Met deze opdracht worden waarden gebruikt die lokaal in de cache worden opgeslagen in het bestand *.azure/config*, met inbegrip van de app-naam, de resourcegroep en het App Service-plan.

Zodra de implementatie is voltooid, schakelt u terug naar het browservenster om `http://<app-name>.azurewebsites.net` te openen. Vernieuw de pagina. Als het goed is, wordt hier het volgende aangepast bericht weergegeven:

![Een bijgewerkte Python-voorbeeld-app uitvoeren in Azure](./media/quickstart-python/run-updated-hello-world-sample-python-app-in-browser.png)

[Ondervindt u problemen? Laat het ons weten.](https://aka.ms/FlaskCLIQuickstartHelp)

> [!TIP]
> Visual Studio Code biedt krachtige extensies voor Python en Azure App Service, waarmee het implementatieproces van Python-web-apps in App Service wordt vereenvoudigd. Zie [Python-apps implementeren in App Service vanuit Visual Studio Code](/azure/python/tutorial-deploy-app-service-on-linux-01) voor meer informatie.

## <a name="stream-logs"></a>Logboeken streamen

U hebt toegang tot de consolelogboeken die zijn gegenereerd vanuit de app en de container waarin deze wordt uitgevoerd. Logboeken bevatten uitvoer die wordt gegenereerd met behulp van `print`-instructies.

Voer de volgende opdracht uit om logboeken te streamen:

```azurecli
az webapp log tail
```

Vernieuw de app in de browser om consolelogboeken te genereren. Deze omvatten berichten waarin HTTP-aanvragen voor de app worden beschreven. Als er niet direct uitvoer wordt weergegeven, probeert u het over 30 seconden nog eens.

U kunt ook de logboekbestanden van de browser inspecteren op `https://<app-name>.scm.azurewebsites.net/api/logs/docker`.

U kunt op elk gewenst moment stoppen met logboekstreaming door **Ctrl**+**C** te typen.

[Ondervindt u problemen? Laat het ons weten.](https://aka.ms/FlaskCLIQuickstartHelp)

## <a name="manage-the-azure-app"></a>De Azure-app beheren

Ga naar <a href="https://portal.azure.com" target="_blank">Azure Portal</a> om de app te beheren die u hebt gemaakt. Zoek en selecteer **App Services**.

![Naar App Services navigeren in Azure Portal](./media/quickstart-python/navigate-to-app-services-in-the-azure-portal.png)

Selecteer de naam van uw Azure-app.

![Naar uw Python-app in App Services navigeren in Azure Portal](./media/quickstart-python/navigate-to-app-in-app-services-in-the-azure-portal.png)

Wanneer u de app selecteert, wordt de pagina **Overzicht** van deze app weergegeven. Hier kunt u algemene beheertaken uitvoeren, zoals bladeren, stoppen, starten, opnieuw starten en verwijderen.

![Uw Python-app beheren op de pagina Overzicht in Azure Portal](./media/quickstart-python/manage-an-app-in-app-services-in-the-azure-portal.png)

Het App Service-menu biedt verschillende pagina's voor het configureren van uw app.

[Ondervindt u problemen? Laat het ons weten.](https://aka.ms/FlaskCLIQuickstartHelp)

## <a name="clean-up-resources"></a>Resources opschonen

In de voorgaande stappen hebt u Azure-resources in een resourcegroep gemaakt. De naam van de resourcegroep lijkt op 'appsvc_rg_Linux_CentralUS', afhankelijk van uw locatie. Als u een andere App Service-SKU gebruikt dan de gratis F1-laag, worden voor deze resources doorlopende kosten in rekening gebracht (zie [App Service-prijzen](https://azure.microsoft.com/pricing/details/app-service/linux/)).

Als u denkt dat u deze resources niet meer gaat gebruiken, verwijdert u de resourcegroep door de volgende opdracht uit te voeren:

```azurecli
az group delete
```

Voor de opdracht wordt de resourcegroepnaam gebruikt die in het bestand *.azure/config* in de cache is opgeslagen.

Het kan een minuut duren voordat de opdracht is voltooid.

[Ondervindt u problemen? Laat het ons weten.](https://aka.ms/FlaskCLIQuickstartHelp)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Zelfstudie: Python-web-app (Django) met PostgreSQL](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [Gebruikersaanmelding toevoegen aan een Python-web-app](../active-directory/develop/quickstart-v2-python-webapp.md)

> [!div class="nextstepaction"]
> [Python-app configureren](configure-language-python.md)

> [!div class="nextstepaction"]
> [Zelfstudie: Een Python-app in een aangepaste container uitvoeren](tutorial-custom-container.md)
