---
title: Een HTTP-geactiveerde python-functie maken in azure
description: Maak en implementeer serverloze python-code in de Cloud met behulp van Azure Functions.
ms.date: 01/15/2020
ms.topic: quickstart
ms.custom: mvc
ms.openlocfilehash: 01c586c9077fd8cf244d7e26fe55252cc455c6fb
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76710918"
---
# <a name="quickstart-create-an-http-triggered-python-function-in-azure"></a>Quick Start: een HTTP-geactiveerde python-functie maken in azure

In dit artikel gebruikt u opdracht regel Programma's voor het maken van een python-functie die reageert op HTTP-aanvragen. Nadat u de code lokaal hebt getest, implementeert u deze in de serverloze omgeving van Azure Functions. Het volt ooien van deze Quick Start brengt een kleine prijs van een paar USD cent of minder in uw Azure-account.

Er is ook een [versie op basis van Visual Studio code](/azure/python/tutorial-vs-code-serverless-python-01) van dit artikel.

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Maak gratis een account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- De [Azure functions core tools](./functions-run-local.md#v2) versie 2.7.1846 of hoger.
- De [Azure cli](/cli/azure/install-azure-cli) -versie 2.0.76 of hoger. 
- [Python 3.7.4-64-bits](https://www.python.org/downloads/release/python-374/). (Python 3.7.4 wordt gecontroleerd met Azure Functions; Python 3,8 en latere versies worden nog niet ondersteund.)

### <a name="prerequisite-check"></a>Controle van vereisten

1. Voer `func --version` in een Terminal-of opdracht venster uit om te controleren of de Azure Functions Core Tools versie 2.7.1846 of hoger is.
1. Voer `az --version` uit om te controleren of de versie van de Azure CLI 2.0.76 of hoger is.
1. Voer `az login` uit om u aan te melden bij Azure en een actief abonnement te verifiëren.
1. Voer `python --version` (Linux/MacOS) of `py --version` (Windows) uit om uw python-versie rapporten te controleren 3.7. x.

## <a name="create-and-activate-a-virtual-environment"></a>Een virtuele omgeving maken en activeren

In een geschikte map voert u de volgende opdrachten uit om een virtuele omgeving met de naam `.venv`te maken en te activeren. Zorg ervoor dat u python 3,7 gebruikt, die wordt ondersteund door Azure Functions.


# <a name="bashtabbash"></a>[bash](#tab/bash)

```bash
python -m venv .venv
```

```bash
source .venv/bin/activate
```

Als python het venv-pakket niet heeft geïnstalleerd in uw Linux-distributie, voert u de volgende opdracht uit:

```bash
sudo apt-get install python3-venv
```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

```powershell
py -m venv .venv
```

```powershell
.venv\scripts\activate
```

# <a name="cmdtabcmd"></a>[Cmd](#tab/cmd)

```cmd
py -m venv .venv
```

```cmd
.venv\scripts\activate
```

---

U voert alle volgende opdrachten uit in deze geactiveerde virtuele omgeving. (Als u de virtuele omgeving wilt afsluiten, voert u `deactivate`uit.)

## <a name="create-a-local-function-project"></a>Een lokale functie project maken

In Azure Functions is een functie project een container voor een of meer afzonderlijke functies die elk op een bepaalde trigger reageert. Alle functies in een project delen dezelfde lokale en hosting configuraties. In deze sectie maakt u een functie project dat één functie bevat.

1. Voer in de virtuele omgeving de `func init` opdracht uit om een functions-project te maken in een map met de naam *LocalFunctionProj* met de opgegeven runtime:

    ```
    func init LocalFunctionProj --python
    ```
    
    Deze map bevat verschillende bestanden voor het project, met inbegrip van configuratie bestanden met de naam [Local. settings. json](functions-run-local.md#local-settings-file) en [host. json](functions-host-json.md). Omdat *Local. settings. json* geheimen kan bevatten die zijn gedownload van Azure, wordt het bestand standaard uitgesloten van broncode beheer in het *. gitignore* -bestand.

    > [!TIP]
    > Omdat een functie project is gekoppeld aan een specifieke runtime, moeten alle functies in het project met dezelfde taal worden geschreven.

1. Navigeer naar de projectmap:

    ```
    cd LocalFunctionProj
    ```
    
1. Voeg een functie toe aan uw project met behulp van de volgende opdracht, waarbij het argument `--name` de unieke naam van de functie is en het argument `--template` de trigger van de functie specificeert. `func new` een submap te maken die overeenkomt met de naam van de functie die geschikt is voor de gekozen taal van het project en een configuratie bestand met de naam *Function. json*.

    ```
    func new --name HttpExample --template "HTTP trigger"
    ```

### <a name="optional-examine-the-file-contents"></a>Beschrijving De inhoud van het bestand controleren

Desgewenst kunt u [de functie lokaal uitvoeren](#run-the-function-locally) en de bestands inhoud later bekijken.

### <a name="__init__py"></a>\_\_init\_\_. py

*\_\_init\_\_. py* bevat een `main()` python-functie die wordt geactiveerd volgens de configuratie in *Function. json*.

```python
import logging

import azure.functions as func


def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        return func.HttpResponse(f"Hello {name}!")
    else:
        return func.HttpResponse(
             "Please pass a name on the query string or in the request body",
             status_code=400
        )
```

Voor HTTP-trigger ontvangt de functie aanvraag gegevens in de variabele `req` zoals gedefinieerd in *Function. json*. `req` is een instantie van de [klasse Azure. functions. HttpRequest](/python/api/azure-functions/azure.functions.httprequest). Het retour object, gedefinieerd als `$return` in *Function. json*, is een instantie van de [klasse Azure. functions. HttpResponse](/python/api/azure-functions/azure.functions.httpresponse). Zie [Azure functions HTTP-triggers en-bindingen](functions-bindings-http-webhook.md)voor meer informatie.

### <a name="functionjson"></a>function.json

*Function. json* is een configuratie bestand dat de invoer-en uitvoer `bindings` definieert voor de functie, met inbegrip van het trigger type. U kunt `scriptFile` zo nodig wijzigen om een ander python-bestand aan te roepen.

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ]
}
```

Elke binding vereist een richting, een type en een unieke naam. De HTTP-trigger heeft een invoer binding van het type [`httpTrigger`](functions-bindings-http-webhook.md#trigger) en de uitvoer binding van het type [`http`](functions-bindings-http-webhook.md#output).


## <a name="run-the-function-locally"></a>De functie lokaal uitvoeren

Start de functie door de lokale Azure Functions runtime host te starten in de map *LocalFunctionProj* :

```
func start
```

De volgende uitvoer moet worden weer gegeven. (Als HttpExample niet wordt weer gegeven zoals hieronder weer gegeven, start u de host waarschijnlijk vanuit de map *HttpExample* . In dat geval gebruikt u **Ctrl**+**C** om de host te stoppen, gaat u naar de map van het bovenliggende *LocalFunctionProj* en voert u `func start` opnieuw uit.)

```output
Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

Http Functions:

        HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
```

Kopieer de URL van uw `HttpExample`-functie van deze uitvoer naar een browser en voeg de query reeks toe `?name=<your-name>`, waardoor de volledige URL, zoals `http://localhost:7071/api/HttpExample?name=Functions`. In de browser moet een bericht als `Hello Functions`worden weer gegeven:

![Resultaat van de functie lokaal uitgevoerd in de browser](./media/functions-create-first-function-python/function-test-local-browser.png)

De Terminal waarin u `func start` hebt uitgevoerd, toont ook de logboek uitvoer wanneer u aanvragen doet.

Wanneer u klaar bent, **Ctrl**+**C** om de functions-host te stoppen.

## <a name="create-supporting-azure-resources-for-your-function"></a>Ondersteunende Azure-resources maken voor uw functie

Als u uw functie code wilt implementeren in azure, moet u drie resources maken:

- Een resource groep, een logische container voor gerelateerde resources.
- Een Azure Storage account, waarmee de status en andere informatie over uw projecten worden bijgehouden.
- Een Azure functions-app, waarmee u de omgeving voor het uitvoeren van uw functie code kunt gebruiken. Een functie-app wordt toegewezen aan uw lokale functie project en biedt u de mogelijkheid om functies te groeperen als logische eenheid, zodat u resources eenvoudiger kunt beheren, implementeren en delen.

U gebruikt Azure CLI-opdrachten om deze items te maken. Elke opdracht biedt JSON-uitvoer na voltooiing.

1. Als u dit nog niet hebt gedaan, meldt u zich aan bij Azure met de opdracht [AZ login](/cli/azure/group#az-login) :

    ```azurecli
    az login
    ```
    
1. Een resourcegroep maken met de opdracht [az group create](/cli/azure/group#az-group-create). In het volgende voor beeld wordt een resource groep met de naam `AzureFunctionsQuickstart-rg` in de `westeurope` regio gemaakt. (In het algemeen maakt u de resource groep en-resources in een regio bij u in de buurt met behulp van een beschik bare regio van de `az account list-locations`-opdracht.)

    ```azurecli
    az group create --name AzureFunctionsQuickstart-rg --location westeurope
    ```
    
    > [!NOTE]
    > U kunt geen Linux-en Windows-apps hosten in dezelfde resource groep. Als u een bestaande resource groep hebt met de naam `AzureFunctionsQuickstart-rg` met een Windows-functie-app of web-app, moet u een andere resource groep gebruiken.
    
1. Maak een opslag account voor algemeen gebruik in uw resource groep en regio met behulp van de opdracht [AZ Storage account create](/cli/azure/storage/account#az-storage-account-create) . Vervang `<storage_name>` in het volgende voor beeld door een globaal unieke naam die voor u van toepassing is. Namen mogen alleen uit drie tot 24 tekens bestaan en mag alleen kleine letters bevatten. `Standard_LRS` geeft een typisch algemeen account op.

    ```azurecli
    az storage account create --name <storage_name> --location westeurope --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
    ```
    
    Het opslag account heeft slechts een paar USD cent voor deze Quick Start.
    
1. Maak de functions-app met de opdracht [AZ functionapp Create](/cli/azure/functionapp#az-functionapp-create) . Vervang in het volgende voor beeld `<storage_name>` door de naam van het account dat u in de vorige stap hebt gebruikt en vervang `<app_name>` door een globaal unieke naam die voor u van toepassing is. De `<app_name>` is ook het standaard DNS-domein voor de functie-app.

    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --os-type Linux --consumption-plan-location westeurope --runtime python --name <app_name> --storage-account <storage_name>
    ```
    
    Met deze opdracht maakt u een functie-app met de opgegeven taal runtime onder het [verbruiks abonnement van Azure functions](functions-scale.md#consumption-plan). Dit is gratis voor de hoeveelheid gebruik die u hier maakt. Met deze opdracht wordt ook een gekoppeld Azure-toepassing Insights-exemplaar in dezelfde resource groep ingericht, waarmee u uw functie-app kunt bewaken en logboeken weer geven. Zie [Azure functions bewaken](functions-monitoring.md)voor meer informatie. Er worden geen kosten in rekening gebracht totdat u deze activeert.
    
## <a name="deploy-the-function-project-to-azure"></a>Het functie project implementeren in azure

Met de benodigde resources kunt u nu uw lokale functies project implementeren in de functie-app in azure met behulp van de opdracht [func Azure functionapp Publish](functions-run-local.md#project-file-deployment) . Vervang `<app_name>` in het volgende voor beeld door de naam van uw app.

```
func azure functionapp publish <app_name>
```

Als u de fout melding ' kan de app niet vinden met de naam... ' ziet, wacht u een paar seconden en probeert u het opnieuw. de app is mogelijk niet volledig geïnitialiseerd door Azure na de vorige `az functionapp create` opdracht.

Met de opdracht publiceren worden de resultaten weer gegeven die vergelijkbaar zijn met de volgende uitvoer (afgekapt voor eenvoud):

```output
Getting site publishing info...
Creating archive for current directory...
Performing remote build for functions project.

...

Deployment successful.
Remote build succeeded!
Syncing triggers...
Functions in msdocs-azurefunctions-qs:
    HttpExample - [httpTrigger]
        Invoke url: https://msdocs-azurefunctions-qs.azurewebsites.net/api/httpexample?code=KYHrydo4GFe9y0000000qRgRJ8NdLFKpkakGJQfC3izYVidzzDN4gQ==
```

## <a name="invoke-the-function-on-azure"></a>De functie aanroepen op Azure

Omdat uw functie gebruikmaakt van een HTTP-trigger, roept u deze aan door een HTTP-aanvraag naar de URL in de browser of met een hulp programma zoals krul te maken. In beide gevallen is de para meter `code` URL de unieke functie sleutel die de aanroep van uw functie-eind punt toestaat.

# <a name="browsertabbrowser"></a>[Browser](#tab/browser)

Kopieer de volledige **invoke-URL** die wordt weer gegeven in de uitvoer van de opdracht publiceren naar een adres balk van de browser en voeg de query parameter `&name=Azure`toe. De browser moet vergelijk bare uitvoer weer geven als u de functie lokaal hebt uitgevoerd.

![De uitvoer van de functie die wordt uitgevoerd op Azure in een browser](./media/functions-create-first-function-python/function-test-cloud-browser.png)


# <a name="curltabcurl"></a>[curl](#tab/curl)

Voer [krul](https://curl.haxx.se/) uit met de **aanroepende URL**en voeg de para meter `&name=Azure`toe. De uitvoer van de opdracht moet de tekst ' Hello Azure ' zijn.

![De uitvoer van de functie die wordt uitgevoerd op Azure met behulp van krul](./media/functions-create-first-function-python/function-test-cloud-curl.png)

---

> [!TIP]
> Gebruik de [Application Insights Live Metrics stream](functions-monitoring.md#streaming-logs)om bijna realtime logboeken voor een gepubliceerde python-app weer te geven.

## <a name="clean-up-resources"></a>Resources opschonen

Als u verdergaat met de volgende stap, [voegt u een Azure Storage wachtrij-uitvoer binding toe](functions-add-output-binding-storage-queue-python.md). u kunt al uw resources op dezelfde plaats zetten als u op de hoogte bent van wat u al hebt gedaan.

Gebruik anders de volgende opdracht om de resource groep en alle bijbehorende resources te verwijderen om te voor komen dat er verdere kosten in rekening worden gebracht.

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een Azure Storage-wachtrij-uitvoer binding toevoegen](functions-add-output-binding-storage-queue-python.md)
