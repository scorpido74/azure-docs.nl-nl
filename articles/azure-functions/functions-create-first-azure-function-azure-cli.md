---
title: Een functie in Azure maken die reageert op HTTP-aanvragen
description: Meer informatie over het maken van een functie vanaf de opdrachtregel en vervolgens het lokale project publiceren naar serverloze hosting in Azure-functies.
ms.date: 01/28/2020
ms.topic: quickstart
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: 89b6a9f31414cbaa9cc92c1a0d881a1354180990
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80282729"
---
# <a name="quickstart-create-a-function-in-azure-that-responds-to-http-requests"></a>Snelstart: een functie in Azure maken die reageert op HTTP-aanvragen

In dit artikel gebruikt u opdrachtregelgereedschappen om een functie te maken die reageert op HTTP-aanvragen. Nadat u de code lokaal hebt getest, implementeert u deze naar de serverloze omgeving van Azure-functies. Het voltooien van deze quickstart brengt een kleine kostenpost van enkele USD cent of minder in uw Azure-account met zich mee.

Er is ook een [Visual Studio Code-gebaseerde versie](functions-create-first-function-vs-code.md) van dit artikel.

## <a name="configure-your-local-environment"></a>Uw lokale omgeving configureren

Voordat u begint, moet u het volgende hebben:

+ Een Azure-account met een actief abonnement. [Maak gratis een account aan.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell"  
+ De [Azure Functions Core Tools](./functions-run-local.md#v2) versie 2.7.1846 of een latere 2.x versie.
::: zone-end  
::: zone pivot="programming-language-python"
+ Python 3.6 en 3.7 vereisen [Azure Functions Core Tools](./functions-run-local.md#v2) versie 2.7.1846 of een latere 2.x-versie. Python 3.8 vereist [versie 3.x](./functions-run-local.md#v2) van de Core Tools.
::: zone-end

+ De [Azure CLI-versie](/cli/azure/install-azure-cli) 2.0.76 of hoger. 
::: zone pivot="programming-language-javascript,programming-language-typescript"
+ [Node.js](https://nodejs.org/), Active LTS en Maintenance LTS versies (8.11.1 en 10.14.1 aanbevolen).
::: zone-end

::: zone pivot="programming-language-python"
+ [Python 3.8](https://www.python.org/downloads/release/python-382/), [Python 3.7](https://www.python.org/downloads/release/python-375/), [Python 3.6](https://www.python.org/downloads/release/python-368/), die worden ondersteund door Azure Functions (x64).
::: zone-end
::: zone pivot="programming-language-powershell"
+ [PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-windows)

+ De [.NET Core SDK 2.2+](https://www.microsoft.com/net/download)
::: zone-end

### <a name="check-your-environment"></a>Controleer uw omgeving

+ Voer in een terminal- `func --version` of opdrachtvenster uit om te controleren of de Azure Functions Core Tools versie 2.7.1846 of een latere 2.x-versie zijn.

+ Voer `az --version` deze uit om te controleren of de Azure CLI-versie 2.0.76 of hoger is.

+ Meld `az login` u aan bij Azure en verifieer een actief abonnement.

::: zone pivot="programming-language-javascript,programming-language-typescript"
+ Voer `node --version` uit om uw Node.js-versierapporten 8.x of 10.x te controleren.
::: zone-end
::: zone pivot="programming-language-python"
+ Voer `python --version` (Linux/MacOS) `py --version` of (Windows) uit om uw Python-versierapporten 3.8.x, 3.7.x of 3.6.x te controleren.

## <a name="create-and-activate-a-virtual-environment"></a><a name="create-venv"></a>Een virtuele omgeving maken en activeren

Voer in een geschikte map de volgende opdrachten uit `.venv`om een virtuele omgeving met de naam te maken en te activeren. Zorg ervoor dat u Python 3.8, 3.7 of 3.6 gebruikt, die worden ondersteund door Azure-functies.


# <a name="bash"></a>[Bash](#tab/bash)

```bash
python -m venv .venv
```

```bash
source .venv/bin/activate
```

Als Python het venv-pakket niet op uw Linux-distributie heeft geïnstalleerd, voert u de volgende opdracht uit:

```bash
sudo apt-get install python3-venv
```

# <a name="powershell"></a>[Powershell](#tab/powershell)

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

U voert alle volgende opdrachten uit in deze geactiveerde virtuele omgeving. (Voer de virtuele omgeving `deactivate`uit om de virtuele omgeving af te sluiten.

::: zone-end

## <a name="create-a-local-function-project"></a>Een lokaal functieproject maken

In Azure Functions is een functieproject een container voor een of meer afzonderlijke functies die elk reageert op een specifieke trigger. Alle functies in een project delen dezelfde lokale en hostingconfiguraties. In deze sectie maakt u een functieproject dat één functie bevat.

1. Voer in de virtuele `func init` omgeving de opdracht uit om een functieproject te maken in een map met de naam *LocalFunctionProj* met de opgegeven runtime:

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


    Deze map bevat verschillende bestanden voor het project, waaronder configuraties bestanden met de naam [local.settings.json](functions-run-local.md#local-settings-file) en [host.json](functions-host-json.md). Omdat *local.settings.json* geheimen kan bevatten die zijn gedownload van Azure, is het bestand standaard uitgesloten van bronbeheer in het *.gitignore-bestand.*

1. Navigeer naar de projectmap:

    ```
    cd LocalFunctionProj
    ```
    
1. Voeg een functie toe aan uw project `--name` met behulp van de volgende `--template` opdracht, waarbij het argument de unieke naam van uw functie is en het argument de trigger van de functie opgeeft. 

    ```
    func new --name HttpExample --template "HTTP trigger"
    ```

    ::: zone pivot="programming-language-csharp"
    `func new`hiermee wordt een HttpExample.cs codebestand aanmaakt.
    ::: zone-end
    ::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell"
    `func new`hiermee wordt een submap gemaakt die overeenkomt met de functienaam die een codebestand bevat dat geschikt is voor de gekozen taal van het project en een configuratiebestand met de naam *function.json*.
    ::: zone-end

### <a name="optional-examine-the-file-contents"></a>(Optioneel) De inhoud van het bestand onderzoeken

Indien gewenst u [de functie lokaal uitvoeren](#run-the-function-locally) en de inhoud van het bestand later onderzoeken.

::: zone pivot="programming-language-csharp"
#### <a name="httpexamplecs"></a>HttpExample.cs

*HttpExample.cs* bevat `Run` een methode die `req` aanvraaggegevens in de variabele ontvangt, is een [HttpRequest](/dotnet/api/microsoft.aspnetcore.http.httprequest) die is versierd met het **HttpTriggerAttribute**, dat het triggergedrag definieert. 

:::code language="csharp" source="~/functions-docs-csharp/http-trigger-template/HttpExample.cs":::

Het retourobject is een [ActionResult](/dotnet/api/microsoft.aspnetcore.mvc.actionresult) dat een antwoordbericht retourneert als een [OkObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.okobjectresult) (200) of een [BadRequestObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.badrequestobjectresult) (400). Zie [HTTP-triggers en bindingen van Azure Functions HTTP](/azure/azure-functions/functions-bindings-http-webhook?tabs=csharp)voor meer informatie.
::: zone-end

::: zone pivot="programming-language-python"
#### <a name="__init__py"></a>\_\_init\_\_.py

*\_\_init\_\_.py* `main()` bevat een Python-functie die wordt geactiveerd volgens de configuratie in *function.json*.

:::code language="python" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/__init__.py":::

Voor een HTTP-trigger ontvangt de functie `req` aanvraaggegevens in de variabele zoals gedefinieerd in *function.json*. `req`is een instantie van de [klasse azure.functions.HttpRequest](/python/api/azure-functions/azure.functions.httprequest). Het retourobject, gedefinieerd `$return` als in *function.json*, is een instantie van [azure.functions.HttpResponse, klasse](/python/api/azure-functions/azure.functions.httpresponse). Zie [HTTP-triggers en bindingen van Azure Functions HTTP](/azure/azure-functions/functions-bindings-http-webhook?tabs=python)voor meer informatie.
::: zone-end

::: zone pivot="programming-language-javascript"
#### <a name="indexjs"></a>index.js

*index.js* exporteert een functie die wordt geactiveerd volgens de configuratie in *function.json*.

:::code language="javascript" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-JavaScript/index.js":::

Voor een HTTP-trigger ontvangt de functie `req` aanvraaggegevens in de variabele zoals gedefinieerd in *function.json*. Het retourobject, gedefinieerd `$return` als in *function.json,* is het antwoord. Zie [HTTP-triggers en bindingen van Azure Functions HTTP](/azure/azure-functions/functions-bindings-http-webhook?tabs=javascript)voor meer informatie.
::: zone-end

::: zone pivot="programming-language-typescript"
#### <a name="indexts"></a>index.ts

*index.ts* exporteert een functie die wordt geactiveerd volgens de configuratie in *function.json*.

:::code language="typescript" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-TypeScript/index.ts":::

Voor een HTTP-trigger ontvangt de functie `req` aanvraaggegevens in de variabele van het type **HttpRequest** zoals gedefinieerd in *function.json*. Het retourobject, gedefinieerd `$return` als in *function.json,* is het antwoord. 
::: zone-end

::: zone pivot="programming-language-powershell"
#### <a name="runps1"></a>run.ps1

*run.ps1* definieert een functiescript dat wordt geactiveerd volgens de configuratie in *function.json*.

:::code language="powershell" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-PowerShell/run.ps1":::

Voor een HTTP-trigger ontvangt de functie `$Request` aanvraaggegevens die worden doorgegeven aan de param die is gedefinieerd in *function.json*. Het retourobject, gedefinieerd `Response` als in *function.json,* wordt als reactie op de cmdlet doorgegeven aan de `Push-OutputBinding` cmdlet. 
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell"
#### <a name="functionjson"></a>function.json

*function.json* is een configuratiebestand dat `bindings` de invoer en uitvoer voor de functie definieert, inclusief het triggertype. 
::: zone-end

::: zone pivot="programming-language-python"
U kunt `scriptFile` wijzigen om een ander Python-bestand aan te roepen indien gewenst.

:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json":::
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript"
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-JavaScript/function.json":::
::: zone-end

::: zone pivot="programming-language-powershell"
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-PowerShell/function.json":::
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell"  
Elke binding vereist een richting, een type en een unieke naam. De HTTP-trigger heeft een [`httpTrigger`](functions-bindings-http-webhook-trigger.md) invoerbinding van [`http`](functions-bindings-http-webhook-output.md)type- en uitvoerbinding van het type .
::: zone-end  

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

## <a name="create-supporting-azure-resources-for-your-function"></a>Ondersteunende Azure-resources maken voor uw functie

Voordat u uw functiecode implementeren in Azure, moet u drie bronnen maken:

- Een resourcegroep, een logische container voor gerelateerde resources.
- Een opslagaccount, dat status- en andere informatie over uw projecten bijhoudt.
- Een functie-app, die de omgeving biedt voor het uitvoeren van uw functiecode. Een functie-app wordt toegewezen aan uw lokale functieproject en stelt u in staat functies te groeperen als een logische eenheid voor eenvoudiger beheer, implementatie en delen van resources.

Gebruik de volgende Azure CLI-opdrachten om deze items te maken. Elke opdracht biedt JSON-uitvoer na voltooiing.

1. Als u dit nog niet hebt gedaan, meldt u zich aan bij Azure met de opdracht [AZ-aanmelding:](/cli/azure/reference-index#az-login)

    ```azurecli
    az login
    ```
    
1. Een resourcegroep maken met de opdracht [az group create](/cli/azure/group#az-group-create). In het volgende voorbeeld `AzureFunctionsQuickstart-rg` wordt `westeurope` een resourcegroep met de naam in de regio genaamerd. (U maakt over het algemeen uw resourcegroep en resources in `az account list-locations` een regio bij u in de buurt, met behulp van een beschikbare regio van de opdracht.)

    ```azurecli
    az group create --name AzureFunctionsQuickstart-rg --location westeurope
    ```
    ::: zone pivot="programming-language-python"  
    > [!NOTE]
    > U geen Linux- en Windows-apps hosten in dezelfde brongroep. Als u een bestaande `AzureFunctionsQuickstart-rg` resourcegroep hebt met de naam Een Windows-functie-app of web-app, moet u een andere brongroep gebruiken.
    ::: zone-end  
    
1. Maak een opslagaccount voor algemene doeleinden in uw resourcegroep en -regio met behulp van de opdracht [AZ-opslagaccount maken.](/cli/azure/storage/account#az-storage-account-create) Vervang in het `<STORAGE_NAME>` volgende voorbeeld een wereldwijd unieke naam die bij u past. Namen moeten alleen drie tot 24 tekens en kleine letters bevatten. `Standard_LRS`hiermee wordt een algemeen doelaccount opgegeven, dat wordt [ondersteund door functies](storage-considerations.md#storage-account-requirements).

    ```azurecli
    az storage account create --name <STORAGE_NAME> --location westeurope --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
    ```
    
    De opslag rekening duurt slechts een paar cent (USD) voor deze quickstart.
    
1. Maak de functie-app met de [opdracht AZ-functieapp create.](/cli/azure/functionapp#az-functionapp-create) Vervang in het `<STORAGE_NAME>` volgende voorbeeld de naam van het account dat `<APP_NAME>` u in de vorige stap hebt gebruikt en vervang een wereldwijd unieke naam die bij u past. De `<APP_NAME>` is ook het standaard DNS-domein voor de functie-app. 

    ::: zone pivot="programming-language-python"  
    Als u Python 3.8 `--runtime-version` gebruikt, gaat u over op `3.8` en `--functions_version` naar `3`.
    
    Als u Python 3.6 `--runtime-version` gebruikt, gaat u over op `3.6`.

    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --os-type Linux --consumption-plan-location westeurope --runtime python --runtime-version 3.7 --functions-version 2 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```
    ::: zone-end  

    ::: zone pivot="programming-language-javascript,programming-language-typescript"  
    Als u Node.js 8 gebruikt, u ook overschakelen `--runtime-version` naar `8`.

    
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime node --runtime-version 10 --functions-version 2 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```
    ::: zone-end  

    ::: zone pivot="programming-language-csharp"  
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime dotnet --functions-version 2 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```
    ::: zone-end  
    
    ::: zone pivot="programming-language-powershell"  
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime powershell --functions-version 2 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```
    ::: zone-end  

    Met deze opdracht wordt een functie-app gemaakt die wordt uitgevoerd in de opgegeven taalruntime onder het [Azure Functions Usage Plan](functions-scale.md#consumption-plan), die gratis is voor de hoeveelheid gebruik die u hier uitvoert. De opdracht voorziet ook in een bijbehorende Instantie Azure Application Insights in dezelfde resourcegroep, waarmee u uw functie-app controleren en logboeken weergeven. Zie [Azure-functies controleren](functions-monitoring.md)voor meer informatie . De instantie brengt geen kosten met zich mee totdat u deze activeert.
    
## <a name="deploy-the-function-project-to-azure"></a>Het functieproject implementeren in Azure

::: zone pivot="programming-language-typescript"  
Voordat u Core Tools gebruikt om uw project te implementeren in Azure, maakt u een productieklare build van JavaScript-bestanden uit de TypeScript-bronbestanden.

Met de volgende opdracht wordt uw TypeScript-project voorbereid op implementatie:

```
npm run build:production 
```
::: zone-end  

Met de benodigde resources u nu uw lokale functieproject implementeren in de functie-app in Azure met behulp van de opdracht [publicering van de func azure-functieapp.](functions-run-local.md#project-file-deployment) Vervang in het `<APP_NAME>` volgende voorbeeld de naam van uw app.

```
func azure functionapp publish <APP_NAME>
```

Als u de fout ziet, u app met naam niet vinden..., wacht u een paar `az functionapp create` seconden en probeert u het opnieuw, omdat Azure de app mogelijk niet volledig heeft geïnitialiseerd na de vorige opdracht.

De opdracht Publiceren toont resultaten die vergelijkbaar zijn met de volgende uitvoer (afgekapt voor eenvoud):

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

## <a name="invoke-the-function-on-azure"></a>De functie op Azure aanroepen

Omdat uw functie een HTTP-trigger gebruikt, roept u deze aan door een HTTP-verzoek in te dienen voor de URL in de browser of met een tool als krul. In beide gevallen `code` is de URL-parameter de unieke [functiesleutel](functions-bindings-http-webhook-trigger.md#authorization-keys) die de aanroep van uw functieeindpunt autoriseert.

# <a name="browser"></a>[Browser](#tab/browser)

Kopieer de volledige **URL van aanroepen** die in de uitvoer van de `&name=Functions`opdracht publiceren wordt weergegeven naar een adresbalk van de browser, waarbij de queryparameter wordt toegevoegd. De browser moet dezelfde uitvoer weergeven als wanneer u de functie lokaal hebt uitgevoerd.

![De uitvoer van de functie wordt uitgevoerd op Azure in een browser](./media/functions-create-first-azure-function-azure-cli/function-test-cloud-browser.png)


# <a name="curl"></a>[Curl](#tab/curl)

Voer [`curl`](https://curl.haxx.se/) uit met de URL `&name=Functions` **aanroepen**, waarbij de parameter wordt toegevoegd . De uitvoer van de opdracht moet de tekst zijn, 'Hallo functies'.

![De uitvoer van de functie wordt uitgevoerd op Azure met behulp van krul](./media/functions-create-first-azure-function-azure-cli/function-test-cloud-curl.png)

---

> [!TIP]
> Als u bijna realtime logboeken voor een gepubliceerde functie-app wilt weergeven, gebruikt u de [Live Metrics Stream van Toepassing-statistieken](functions-monitoring.md#streaming-logs).

## <a name="clean-up-resources"></a>Resources opschonen

Als u doorgaat met de volgende stap, [voegt u een azure storage-wachtrijuitvoerbinding toe,](functions-add-output-binding-storage-queue-cli.md)houdt u al uw resources op hun plaats, omdat u voortbouwt op wat u al hebt gedaan.

Gebruik anders de volgende opdracht om de brongroep en al de opgenomen resources te verwijderen om te voorkomen dat er extra kosten worden gemaakt.

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Verbinding maken met een Azure Storage-wachtrij](functions-add-output-binding-storage-queue-cli.md)
