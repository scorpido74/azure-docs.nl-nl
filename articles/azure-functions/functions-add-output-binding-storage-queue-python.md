---
title: Een Azure Storage wachtrij binding toevoegen aan uw python-functie
description: Een Azure Storage wachtrij integreren met een python-functie met behulp van een uitvoer binding.
ms.date: 01/15/2020
ms.topic: quickstart
ms.openlocfilehash: f5527e0e636c3f8c9ee3723570ed9811f0df3641
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198476"
---
# <a name="add-an-azure-storage-queue-binding-to-your-python-function"></a>Een Azure Storage wachtrij binding toevoegen aan uw python-functie

In dit artikel integreert u een Azure Storage wachtrij met het functie-en opslag account dat u hebt gemaakt in [een HTTP-geactiveerde python-functie maken](functions-create-first-function-python.md). U verkrijgt deze integratie met behulp van een *uitvoer binding* waarmee gegevens worden geschreven van een HTTP-aanvraag naar een bericht in de wachtrij. In dit artikel worden geen extra kosten in rekening gebracht tot meer dan de enige USD cent van de vorige Snelstartgids.

## <a name="prerequisites"></a>Vereisten

- Voltooi de Snelstartgids door [een HTTP-geactiveerde python-functie te maken](functions-create-first-function-python.md). Als u al resources aan het einde van dat artikel hebt opgeruimd, volgt u de stappen opnieuw om de functions-app opnieuw te maken in azure, maar blijft de resources aanwezig.

## <a name="retrieve-the-azure-storage-connection-string"></a>De Azure Storage ophalen connection string

Als u in de vorige Snelstartgids een functie-app hebt gemaakt in azure, hebt u ook een opslag account gemaakt. De connection string voor dit account wordt veilig opgeslagen in de app-instellingen in Azure. Door de instelling in het bestand *Local. settings. json* te downloaden, kunt u deze verbinding schrijven naar een opslag wachtrij in hetzelfde account wanneer u de functie lokaal uitvoert. 

1. Voer in de hoofdmap van het project de volgende opdracht uit en vervang `<app_name>` door de naam van uw functie-app uit de vorige Snelstartgids. Met deze opdracht worden alle bestaande waarden in het bestand overschreven.

    ```
    func azure functionapp fetch-app-settings <app_name>
    ```
    
1. Open *Local. settings. json* en zoek de waarde met de naam `AzureWebJobsStorage`, het opslag account Connection String. U gebruikt de naam `AzureWebJobsStorage` en de connection string in andere secties van dit artikel.

> [!IMPORTANT]
> Omdat *Local. settings. json* bevat geheimen die zijn gedownload van Azure, moet u dit bestand altijd uitsluiten van broncode beheer. Het *. gitignore* -bestand dat is gemaakt met een lokale functie project, sluit het bestand standaard uit.

## <a name="add-an-output-binding-to-functionjson"></a>Een uitvoer binding toevoegen aan function. json

Hoewel een functie slechts één trigger kan hebben, kan deze meerdere invoer-en uitvoer bindingen hebben, waarmee u verbinding kunt maken met andere Azure-Services en-resources zonder aangepaste integratie code te schrijven. U declareert deze bindingen in het bestand *Function. json* in de map function, afhankelijk van de taal die u voor de functie gebruikt.

In de vorige Snelstartgids bevat uw *Function. json* -bestand in de map *HttpExample* twee bindingen in de verzameling `bindings`:

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

Elke binding heeft ten minste een type, een richting en een naam. In het bovenstaande voor beeld is de eerste binding van het type `httpTrigger` met de richting `in`. Voor de `in` richting geeft `name` de naam op van een invoer parameter die wordt verzonden naar de functie wanneer deze wordt aangeroepen door de trigger.

De tweede binding is van het type `http` met de richtings `out`. in dat geval wordt met de speciale `name` van `$return` aangegeven dat deze binding de retour waarde van de functie gebruikt in plaats van een invoer parameter op te geven.

Als u vanuit deze functie naar een Azure Storage wachtrij wilt schrijven, voegt u een `out` binding van het type `queue` met de naam `msg`toe, zoals wordt weer gegeven in de onderstaande code:

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
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "msg",
      "queueName": "outqueue",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

In dit geval wordt `msg` als uitvoer argument aan de functie gegeven. Voor een `queue` type moet u ook de naam van de wachtrij in `queueName` opgeven en de *naam* opgeven van de Azure Storage verbinding (van *Local. settings. json*) in `connection`.

Zie voor meer informatie over de details van bindingen [Azure functions triggers en bindingen](functions-triggers-bindings.md) en de configuratie van de [wachtrij-uitvoer](functions-bindings-storage-queue.md#output---configuration).

## <a name="add-code-to-use-the-output-binding"></a>Code toevoegen voor het gebruik van de uitvoer binding

Als de wachtrij binding is opgegeven in *Function. json*, kunt u de functie nu bijwerken om de `msg` uitvoer parameter te ontvangen en berichten te schrijven naar de wachtrij.

Werk *HttpExample\\\_\_init\_\_. py* met de volgende code en voeg de `msg`-para meter toe aan de functie definitie en `msg.set(name)` onder de `if name:`-instructie.

```python
import logging

import azure.functions as func


def main(req: func.HttpRequest, msg: func.Out[func.QueueMessage]) -> str:

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        msg.set(name)
        return func.HttpResponse(f"Hello {name}!")
    else:
        return func.HttpResponse(
            "Please pass a name on the query string or in the request body",
            status_code=400
        )
```

De para meter `msg` is een exemplaar van de [`azure.functions.InputStream class`](/python/api/azure-functions/azure.functions.httprequest). De `set` methode schrijft een teken reeks bericht naar de wachtrij, in dit geval de naam die is door gegeven aan de functie in de URL-query teken reeks.

Houd er rekening mee dat u *geen* code hoeft te schrijven voor verificatie, het ophalen van een wachtrij verwijzing of het schrijven van gegevens. Al deze integratie taken kunnen worden verwerkt in de Azure Functions runtime-en wachtrij-uitvoer binding.

## <a name="run-and-test-the-function-locally"></a>De functie lokaal uitvoeren en testen

1. Navigeer in een Terminal-of opdracht prompt naar de map van het functie project, *LocalFunctionProj*.

1. Start de Azure Functions-runtime host met behulp van de volgende opdracht.

    ```
    func host start
    ```

1. Zodra het opstarten is voltooid en u de URL voor het `HttpExample`-eind punt ziet, kopieert u de URL naar een browser en voegt u de query reeks `?name=<your-name>`toe, waarbij u de volledige URL maakt, zoals `http://localhost:7071/api/HttpExample?name=Guido`. In de browser moet een bericht worden weer gegeven zoals `Hello Guido` zoals in het vorige artikel.

    Als het `HttpExample` eind punt niet wordt weer gegeven, stopt u de host met **Ctrl**+**C** en controleert u de uitvoer op fouten. De host activeert bijvoorbeeld het eind punt niet als er een fout is opgetreden in *Function. json*. Controleer ook of u `func host start` uitvoert vanuit de project-map functions en niet op de map *HttpExample* .

1. Wanneer u klaar bent, stopt u de host met **Ctrl**+**C**.

> [!TIP]
> Tijdens het opstarten downloadt en installeert de host de [uitbrei ding voor opslag bindingen](functions-bindings-storage-blob.md#add-to-your-functions-app) en andere micro soft-bindings extensies. Deze installatie treedt op omdat bindings uitbreidingen standaard zijn ingeschakeld in het bestand *host. json* met de volgende eigenschappen:
>
> ```json
> {
>     "version": "2.0",
>     "extensionBundle": {
>         "id": "Microsoft.Azure.Functions.ExtensionBundle",
>         "version": "[1.*, 2.0.0)"
>     }
> }
> ```
>
> Als u fouten ondervindt met betrekking tot bindings uitbreidingen, controleert u of de bovenstaande eigenschappen aanwezig zijn in de *host. json*.

## <a name="view-the-message-in-the-azure-storage-queue"></a>Het bericht in de wachtrij van Azure Storage weer geven

Als uw functie een HTTP-antwoord voor de webbrowser genereert, wordt er ook `msg.set(name)`aangeroepen, die een bericht schrijft naar een Azure Storage wachtrij met de naam `outqueue`, zoals opgegeven in de binding van de wachtrij. U kunt de wachtrij weer geven in de [Azure Portal](../storage/queues/storage-quickstart-queues-portal.md) of in de [Microsoft Azure Storage Explorer](https://storageexplorer.com/). U kunt de wachtrij ook weer geven in de Azure CLI, zoals wordt beschreven in de volgende stappen:

1. Open het bestand *Local. setting. json* van het functie project en kopieer de Connection String waarde. Voer in een Terminal-of opdracht venster de volgende opdracht uit om een omgevings variabele met de naam `AZURE_STORAGE_CONNECTION_STRING`te maken en uw specifieke connection string in plaats van `<connection_string>`te plakken. (Deze omgevings variabele houdt in dat u de connection string niet hoeft op te geven bij elke volgende opdracht met behulp van het argument `--connection-string`.)

    # <a name="bashtabbash"></a>[bash](#tab/bash)
    
    ```bash
    AZURE_STORAGE_CONNECTION_STRING="<connection_string>"
    ```
    
    # <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    $env:AZURE_STORAGE_CONNECTION_STRING = "<connection_string>"
    ```
    
    # <a name="cmdtabcmd"></a>[Cmd](#tab/cmd)
    
    ```cmd
    set AZURE_STORAGE_CONNECTION_STRING="<connection_string>"
    ```
    
    ---
    
1. Beschrijving Gebruik de [`az storage queue list`](/cli/azure/storage/queue#az-storage-queue-list) opdracht om de opslag wachtrijen in uw account weer te geven. De uitvoer van deze opdracht moet een wachtrij bevatten met de naam `outqueue`, die is gemaakt toen de functie het eerste bericht naar die wachtrij heeft geschreven.
    
    # <a name="bashtabbash"></a>[bash](#tab/bash)
    
    ```bash
    az storage queue list --output tsv
    ```
    
    # <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    az storage queue list --output tsv
    ```
    
    # <a name="cmdtabcmd"></a>[Cmd](#tab/cmd)
    
    ```cmd
    az storage queue list --output tsv
    ```
    
    ---


1. Gebruik de [`az storage message peek`](/cli/azure/storage/message#az-storage-message-peek) opdracht om de berichten in deze wachtrij weer te geven. dit moet de eerste naam zijn die u hebt gebruikt bij het testen van de functie. De opdracht haalt het eerste bericht in de wachtrij op in [Base64-code ring](functions-bindings-storage-queue.md#encoding), dus u moet het bericht ook decoderen om als tekst weer te geven.

    # <a name="bashtabbash"></a>[bash](#tab/bash)
    
    ```bash
    echo `echo $(az storage message peek --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
    ```
    
    # <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($(az storage message peek --queue-name outqueue -o tsv --query '[].{Message:content}')))
    ```
    
    # <a name="cmdtabcmd"></a>[Cmd](#tab/cmd)
    
    Omdat u de verwijzing naar de berichten verzameling moet decoderen en van base64 wilt ontsleutelen, voert u Power shell uit en gebruikt u de Power shell-opdracht.

    ---
    
## <a name="redeploy-the-project-to-azure"></a>Het project opnieuw implementeren in azure

Nu u de functie lokaal hebt getest en hebt gecontroleerd of een bericht naar de Azure Storage wachtrij is geschreven, kunt u uw project opnieuw implementeren om het eind punt bij te werken dat op Azure wordt uitgevoerd.

1. Gebruik in de map *LocalFunctionsProj* de opdracht [`func azure functionapp publish`](functions-run-local.md#project-file-deployment) om het project opnieuw te implementeren, waarbij u`<app_name>` vervangt door de naam van uw app.

    ```
    func azure functionapp publish <app_name>
    ```
    
1. Net als in de vorige Snelstartgids gebruikt u een browser of krul om de opnieuw geïmplementeerde functie te testen.

    # <a name="browsertabbrowser"></a>[Browser](#tab/browser)
    
    Kopieer de volledige **invoke-URL** die wordt weer gegeven in de uitvoer van de opdracht publiceren naar een adres balk van de browser en voeg de query parameter `&name=Azure`toe. De browser moet vergelijk bare uitvoer weer geven als u de functie lokaal hebt uitgevoerd.

    ![De uitvoer van de functie die wordt uitgevoerd op Azure in een browser](./media/functions-create-first-function-python/function-test-cloud-browser.png)

    # <a name="curltabcurl"></a>[Ezelsoor](#tab/curl)
    
    Voer [krul](https://curl.haxx.se/) uit met de **aanroepende URL**en voeg de para meter `&name=Azure`toe. De uitvoer van de opdracht moet de tekst ' Hello Azure ' zijn.
    
    ![De uitvoer van de functie die wordt uitgevoerd op Azure met behulp van krul](./media/functions-create-first-function-python/function-test-cloud-curl.png)

    --- 

1. Controleer de opslag wachtrij opnieuw, zoals beschreven in de vorige sectie, om te controleren of deze het nieuwe bericht bevat dat naar de wachtrij is geschreven.

    Als u de Azure CLI gebruikt om de wachtrij te controleren, wordt in de `az storage message peek`-opdracht alleen het eerste bericht in de wachtrij weer gegeven. Gebruik `az storage message get` in plaats daarvan met dezelfde argumenten om de verwerking van de berichten te simuleren. De `get` opdracht retourneert het bericht en verwijdert het uit de wachtrij. U kunt dezelfde opdracht herhalen totdat de wachtrij leeg is (en de opdracht een fout geeft).

## <a name="clean-up-resources"></a>Resources opschonen

Als u verdergaat met de volgende stap, [schakelt u Application Insights-integratie](functions-monitoring.md#manually-connect-an-app-insights-resource)in, behoudt u al uw resources op dezelfde locatie als u gaat bouwen op wat u al hebt gedaan.

Gebruik anders de volgende opdracht om de resource groep en alle bijbehorende resources te verwijderen om te voor komen dat er verdere kosten in rekening worden gebracht.

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Application Insights integratie met een Azure Functions-app inschakelen](functions-monitoring.md#manually-connect-an-app-insights-resource)

Andere bronnen:

- [Voor beelden van complete functie projecten in python](/samples/browse/?products=azure-functions&languages=python).
- [Azure Functions python-ontwikkelaars handleiding](functions-reference-python.md)
- [Azure functions triggers en bindingen](functions-triggers-bindings.md).
- [Pagina met prijzen voor functies](https://azure.microsoft.com/pricing/details/functions/)
- Het artikel [kosten voor verbruiks plan schatten](functions-consumption-costs.md) .
