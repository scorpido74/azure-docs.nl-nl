---
title: Een functie in azure maken die reageert op HTTP-aanvragen
description: Leer hoe u een functie maakt vanaf de opdracht regel en vervolgens het lokale project publiceert op serverloze hosting in Azure Functions.
ms.date: 01/28/2020
ms.topic: quickstart
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: c8648a0bdfaeeb8a89ea24b0f49610e5bf5c0491
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/29/2020
ms.locfileid: "78190837"
---
# <a name="quickstart-create-a-function-in-azure-that-responds-to-http-requests"></a>Quick Start: een functie maken in azure die reageert op HTTP-aanvragen

In dit artikel gebruikt u opdracht regel Programma's voor het maken van een functie die reageert op HTTP-aanvragen. Nadat u de code lokaal hebt getest, implementeert u deze in de serverloze omgeving van Azure Functions. Het volt ooien van deze Quick Start brengt een kleine prijs van een paar USD cent of minder in uw Azure-account.

Er is ook een [versie op basis van Visual Studio code](functions-create-first-function-vs-code.md) van dit artikel.

## <a name="configure-your-local-environment"></a>Uw lokale omgeving configureren

Voordat u begint, moet u het volgende hebben:

+ Een Azure-account met een actief abonnement. [Maak gratis een account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ De [Azure functions core tools](./functions-run-local.md#v2) versie 2.7.1846 of een latere versie van 2. x.

+ De [Azure cli](/cli/azure/install-azure-cli) -versie 2.0.76 of hoger. 
::: zone pivot="programming-language-javascript,programming-language-typescript"
+ [Node. js](https://nodejs.org/), Active LTS en onderhoud LTS-versies (8.11.1 en 10.14.1 aanbevolen).
::: zone-end

::: zone pivot="programming-language-python"
+ [Python 3,7](https://www.python.org/downloads/release/python-375/) of [python 3,6](https://www.python.org/downloads/release/python-368/), die door Azure functions worden ondersteund. Python 3,8 en latere versies worden nog niet ondersteund. 
::: zone-end
::: zone pivot="programming-language-powershell"
+ [Power shell core](/powershell/scripting/install/installing-powershell-core-on-windows)

+ De [.net core SDK 2.2 +](https://www.microsoft.com/net/download)
::: zone-end

### <a name="check-your-environment"></a>Uw omgeving controleren

+ Voer `func --version` in een Terminal-of opdracht venster uit om te controleren of de Azure Functions Core Tools versie 2.7.1846 of een latere versie van 2. x zijn.

+ Voer `az --version` uit om te controleren of de versie van de Azure CLI 2.0.76 of hoger is.

+ Voer `az login` uit om u aan te melden bij Azure en een actief abonnement te verifiëren.

::: zone pivot="programming-language-javascript,programming-language-typescript"
+ Voer `node --version` uit om uw node. js-versie rapporten te controleren 8. x of 10. x.
::: zone-end
::: zone pivot="programming-language-python"
+ Voer `python --version` (Linux/MacOS) of `py --version` (Windows) uit om uw python-versie rapporten te controleren 3.7. x of 3.6. x.

## <a name="create-venv"></a>Een virtuele omgeving maken en activeren

In een geschikte map voert u de volgende opdrachten uit om een virtuele omgeving met de naam `.venv`te maken en te activeren. Zorg ervoor dat u python 3,7 of 3,6 gebruikt, die wordt ondersteund door Azure Functions.


# <a name="bash"></a>[bash](#tab/bash)

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

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
py -m venv .venv
```

```powershell
.venv\scripts\activate
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
py -m venv .venv
```

```cmd
.venv\scripts\activate
```

---

U voert alle volgende opdrachten uit in deze geactiveerde virtuele omgeving. (Als u de virtuele omgeving wilt afsluiten, voert u `deactivate`uit.)

::: zone-end

## <a name="create-a-local-function-project"></a>Een lokale functie project maken

In Azure Functions is een functie project een container voor een of meer afzonderlijke functies die elk op een bepaalde trigger reageert. Alle functies in een project delen dezelfde lokale en hosting configuraties. In deze sectie maakt u een functie project dat één functie bevat.

1. Voer in de virtuele omgeving de `func init` opdracht uit om een functions-project te maken in een map met de naam *LocalFunctionProj* met de opgegeven runtime:

    ::: zone pivot="programming-language-python"
    ```
    func init LocalFunctionProj --python
    ```
    ::: zone-end
    ::: zone pivot="programming-language-csharp"
    ```
    func init LocalFunctionProj --dotnet
    ```
    ::: zone-end
    ::: zone pivot="programming-language-javascript"
    ```
    func init LocalFunctionProj --javascript
    ```
    ::: zone-end
    ::: zone pivot="programming-language-typescript"
    ```
    func init LocalFunctionProj --typescript
    ```
    ::: zone-end
    ::: zone pivot="programming-language-powershell"
    ```
    func init LocalFunctionProj --powershell
    ```
    ::: zone-end


    Deze map bevat verschillende bestanden voor het project, met inbegrip van configuratie bestanden met de naam [Local. settings. json](functions-run-local.md#local-settings-file) en [host. json](functions-host-json.md). Omdat *Local. settings. json* geheimen kan bevatten die zijn gedownload van Azure, wordt het bestand standaard uitgesloten van broncode beheer in het *. gitignore* -bestand.

1. Navigeer naar de projectmap:

    ```
    cd LocalFunctionProj
    ```
    
1. Voeg een functie toe aan uw project met behulp van de volgende opdracht, waarbij het argument `--name` de unieke naam van de functie is en het argument `--template` de trigger van de functie specificeert. 

    ```
    func new --name HttpExample --template "HTTP trigger"
    ```

    ::: zone pivot="programming-language-csharp"
    `func new` maakt een HttpExample.cs-code bestand.
    ::: zone-end
    ::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell"
    `func new` maakt een submap die overeenkomt met de naam van de functie die geschikt is voor de gekozen taal van het project en een configuratie bestand met de naam *Function. json*.
    ::: zone-end

### <a name="optional-examine-the-file-contents"></a>Beschrijving De inhoud van het bestand controleren

Desgewenst kunt u [de functie lokaal uitvoeren](#run-the-function-locally) en de bestands inhoud later bekijken.

::: zone pivot="programming-language-csharp"
#### <a name="httpexamplecs"></a>HttpExample.cs

*HttpExample.cs* bevat een `Run` methode waarmee aanvraag gegevens in de `req` variabele worden ontvangen, een [HttpRequest](/dotnet/api/microsoft.aspnetcore.http.httprequest) is dat is voorzien van de **HttpTriggerAttribute**, waarmee het gedrag van de trigger wordt gedefinieerd. 

:::code language="csharp" source="~/functions-docs-csharp/http-trigger-template/HttpExample.cs":::

Het retour object is een [ActionResult](/dotnet/api/microsoft.aspnetcore.mvc.actionresult) die een antwoord bericht retourneert als een [OkObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.okobjectresult) (200) of een [BadRequestObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.badrequestobjectresult) (400). Zie [Azure functions HTTP-triggers en-bindingen](/azure/azure-functions/functions-bindings-http-webhook?tabs=csharp)voor meer informatie.
::: zone-end

::: zone pivot="programming-language-python"
#### <a name="__init__py"></a>\_\_init\_\_. py

*\_\_init\_\_. py* bevat een `main()` python-functie die wordt geactiveerd volgens de configuratie in *Function. json*.

:::code language="python" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/__init__.py":::

Voor een HTTP-trigger ontvangt de functie aanvraag gegevens in de variabele `req` zoals gedefinieerd in *Function. json*. `req` is een instantie van de [klasse Azure. functions. HttpRequest](/python/api/azure-functions/azure.functions.httprequest). Het retour object, gedefinieerd als `$return` in *Function. json*, is een instantie van de [klasse Azure. functions. HttpResponse](/python/api/azure-functions/azure.functions.httpresponse). Zie [Azure functions HTTP-triggers en-bindingen](/azure/azure-functions/functions-bindings-http-webhook?tabs=python)voor meer informatie.
::: zone-end

::: zone pivot="programming-language-javascript"
#### <a name="indexjs"></a>index. js

*index. js* exporteert een functie die wordt geactiveerd volgens de configuratie in *Function. json*.

:::code language="javascript" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-JavaScript/index.js":::

Voor een HTTP-trigger ontvangt de functie aanvraag gegevens in de variabele `req` zoals gedefinieerd in *Function. json*. Het retour object, gedefinieerd als `$return` in *Function. json*, is de reactie. Zie [Azure functions HTTP-triggers en-bindingen](/azure/azure-functions/functions-bindings-http-webhook?tabs=javascript)voor meer informatie.
::: zone-end

::: zone pivot="programming-language-typescript"
#### <a name="indexts"></a>index. TS

*index. TS* exporteert een functie die wordt geactiveerd volgens de configuratie in *Function. json*.

:::code language="typescript" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-TypeScript/index.ts":::

Voor een HTTP-trigger ontvangt de functie aanvraag gegevens in de variabele `req` van het type **HttpRequest** zoals gedefinieerd in *Function. json*. Het retour object, gedefinieerd als `$return` in *Function. json*, is de reactie. 
::: zone-end

::: zone pivot="programming-language-powershell"
#### <a name="runps1"></a>run. ps1

*Run. ps1* definieert een functie script dat wordt geactiveerd volgens de configuratie in *Function. json*.

:::code language="powershell" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-PowerShell/run.ps1":::

Voor een HTTP-trigger ontvangt de functie aanvraag gegevens die zijn door gegeven aan de `$Request` para meter die is gedefinieerd in *Function. json*. Het retour object, gedefinieerd als `Response` in *Function. json*, wordt door gegeven aan de `Push-OutputBinding` cmdlet als antwoord. 
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell"
#### <a name="functionjson"></a>function.json

*Function. json* is een configuratie bestand dat de invoer-en uitvoer `bindings` definieert voor de functie, met inbegrip van het trigger type. 
::: zone-end

::: zone pivot="programming-language-python"
U kunt `scriptFile` zo nodig wijzigen om een ander python-bestand aan te roepen.

:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json":::
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript"
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-JavaScript/function.json":::
::: zone-end

::: zone pivot="programming-language-powershell"
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-PowerShell/function.json":::
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell"  
Elke binding vereist een richting, een type en een unieke naam. De HTTP-trigger heeft een invoer binding van het type [`httpTrigger`](functions-bindings-http-webhook-trigger.md) en de uitvoer binding van het type [`http`](functions-bindings-http-webhook-output.md).
::: zone-end  

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

## <a name="create-supporting-azure-resources-for-your-function"></a>Ondersteunende Azure-resources maken voor uw functie

Voordat u uw functie code kunt implementeren in azure, moet u drie resources maken:

- Een resource groep, een logische container voor gerelateerde resources.
- Een opslag account, waarmee de status en andere informatie over uw projecten worden bijgehouden.
- Een functie-app, waarmee u de omgeving voor het uitvoeren van uw functie code kunt gebruiken. Een functie-app wordt toegewezen aan uw lokale functie project en biedt u de mogelijkheid om functies te groeperen als logische eenheid, zodat u resources eenvoudiger kunt beheren, implementeren en delen.

Gebruik de volgende Azure CLI-opdrachten om deze items te maken. Elke opdracht biedt JSON-uitvoer na voltooiing.

1. Als u dit nog niet hebt gedaan, meldt u zich aan bij Azure met de opdracht [AZ login](/cli/azure/reference-index#az-login) :

    ```azurecli
    az login
    ```
    
1. Een resourcegroep maken met de opdracht [az group create](/cli/azure/group#az-group-create). In het volgende voor beeld wordt een resource groep met de naam `AzureFunctionsQuickstart-rg` in de `westeurope` regio gemaakt. (In het algemeen maakt u de resource groep en-resources in een regio bij u in de buurt met behulp van een beschik bare regio van de `az account list-locations`-opdracht.)

    ```azurecli
    az group create --name AzureFunctionsQuickstart-rg --location westeurope
    ```
    ::: zone pivot="programming-language-python"  
    > [!NOTE]
    > U kunt geen Linux-en Windows-apps hosten in dezelfde resource groep. Als u een bestaande resource groep hebt met de naam `AzureFunctionsQuickstart-rg` met een Windows-functie-app of web-app, moet u een andere resource groep gebruiken.
    ::: zone-end  
    
1. Maak een opslag account voor algemeen gebruik in uw resource groep en regio met behulp van de opdracht [AZ Storage account create](/cli/azure/storage/account#az-storage-account-create) . Vervang `<STORAGE_NAME>` in het volgende voor beeld door een globaal unieke naam die voor u van toepassing is. Namen mogen alleen uit drie tot 24 tekens bestaan en mag alleen kleine letters bevatten. `Standard_LRS` Hiermee geeft u een account voor algemeen gebruik op dat wordt [ondersteund door-functies](storage-considerations.md#storage-account-requirements).

    ```azurecli
    az storage account create --name <STORAGE_NAME> --location westeurope --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
    ```
    
    Het opslag account heeft slechts een paar cent (USD) voor deze Quick Start.
    
1. Maak de functions-app met de opdracht [AZ functionapp Create](/cli/azure/functionapp#az-functionapp-create) . Vervang in het volgende voor beeld `<STORAGE_NAME>` door de naam van het account dat u in de vorige stap hebt gebruikt en vervang `<APP_NAME>` door een globaal unieke naam die voor u van toepassing is. De `<APP_NAME>` is ook het standaard DNS-domein voor de functie-app. 

    ::: zone pivot="programming-language-python"  
    Als u gebruikmaakt van python 3,6, wijzigt u ook de `--runtime-version` in `3.6`.

    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --os-type Linux --consumption-plan-location westeurope --runtime python --runtime-version 3.7 --functions_version 2 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```
    ::: zone-end  

    ::: zone pivot="programming-language-javascript,programming-language-typescript"  
    Als u node. js 8 gebruikt, wijzigt u ook de `--runtime-version` in `8`.

    
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime node --runtime-version 10 --functions_version 2 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```
    ::: zone-end  

    ::: zone pivot="programming-language-csharp"  
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime dotnet --functions_version 2 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```
    ::: zone-end  
    
    ::: zone pivot="programming-language-powershell"  
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime powershell --functions_version 2 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```
    ::: zone-end  

    Met deze opdracht maakt u een functie-app die wordt uitgevoerd in uw opgegeven taal runtime onder het [verbruiks abonnement van Azure functions](functions-scale.md#consumption-plan). Dit is gratis voor de hoeveelheid gebruik die u hier maakt. Met deze opdracht wordt ook een gekoppeld Azure-toepassing Insights-exemplaar in dezelfde resource groep ingericht, waarmee u uw functie-app kunt bewaken en logboeken weer geven. Zie [Azure functions bewaken](functions-monitoring.md)voor meer informatie. Er worden geen kosten in rekening gebracht totdat u deze activeert.
    
## <a name="deploy-the-function-project-to-azure"></a>Het functie project implementeren in azure

::: zone pivot="programming-language-typescript"  
Voordat u de basis Hulpprogramma's gebruikt om uw project te implementeren in azure, maakt u een productie-gereed build van Java script-bestanden van de type script-bron bestanden.

Met de volgende opdracht wordt uw type script-project voor bereid voor implementatie:

```
npm run build:production 
```
::: zone-end  

Met de benodigde resources kunt u nu uw lokale functies project implementeren in de functie-app in azure met behulp van de opdracht [func Azure functionapp Publish](functions-run-local.md#project-file-deployment) . Vervang `<APP_NAME>` in het volgende voor beeld door de naam van uw app.

```
func azure functionapp publish <APP_NAME>
```

Als u de fout melding ' kan de app niet vinden met de naam... ' ziet, wacht u een paar seconden en probeert u het opnieuw. de app is mogelijk niet volledig geïnitialiseerd door Azure na de vorige `az functionapp create` opdracht.

Met de opdracht publiceren worden de resultaten weer gegeven die vergelijkbaar zijn met de volgende uitvoer (afgekapt voor eenvoud):

<pre>
...

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
</pre>

## <a name="invoke-the-function-on-azure"></a>De functie aanroepen op Azure

Omdat uw functie gebruikmaakt van een HTTP-trigger, roept u deze aan door een HTTP-aanvraag naar de URL in de browser of met een hulp programma zoals krul te maken. In beide gevallen is de para meter `code` URL uw unieke [functie sleutel](functions-bindings-http-webhook-trigger.md#authorization-keys) die de aanroep van uw functie-eind punt toestaat.

# <a name="browser"></a>[Browser](#tab/browser)

Kopieer de volledige **invoke-URL** die wordt weer gegeven in de uitvoer van de opdracht publiceren naar een adres balk van de browser en voeg de query parameter `&name=Functions`toe. De browser moet vergelijk bare uitvoer weer geven als u de functie lokaal hebt uitgevoerd.

![De uitvoer van de functie die wordt uitgevoerd op Azure in een browser](./media/functions-create-first-azure-function-azure-cli/function-test-cloud-browser.png)


# <a name="curl"></a>[Ezelsoor](#tab/curl)

Voer [`curl`](https://curl.haxx.se/) uit met de **aanroepen-URL**en voeg de para meter `&name=Functions`toe. De uitvoer van de opdracht moet de tekst ' Hello functions ' zijn.

![De uitvoer van de functie die wordt uitgevoerd op Azure met behulp van krul](./media/functions-create-first-azure-function-azure-cli/function-test-cloud-curl.png)

---

> [!TIP]
> Gebruik de [Application Insights Live Metrics stream](functions-monitoring.md#streaming-logs)om bijna realtime logboeken voor een gepubliceerde functie-app weer te geven.

## <a name="clean-up-resources"></a>Resources opschonen

Als u verdergaat met de volgende stap, [voegt u een Azure Storage wachtrij-uitvoer binding toe](functions-add-output-binding-storage-queue-python.md). u kunt al uw resources op dezelfde plaats zetten als u op de hoogte bent van wat u al hebt gedaan.

Gebruik anders de volgende opdracht om de resource groep en alle bijbehorende resources te verwijderen om te voor komen dat er verdere kosten in rekening worden gebracht.

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Verbinding maken met een Azure Storage wachtrij](functions-add-output-binding-storage-queue-cli.md)
