---
title: Een functie in Azure maken die reageert op HTTP-aanvragen
description: Meer informatie over het maken van een functie vanaf de opdrachtregel en vervolgens het lokale project publiceren naar serverloze hosting in Azure-functies.
ms.date: 03/30/2020
ms.topic: quickstart
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: a131feab91816a6fdd5075a903cf53651f0de555
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673126"
---
# <a name="quickstart-create-a-function-in-azure-that-responds-to-http-requests"></a>Snelstart: een functie in Azure maken die reageert op HTTP-aanvragen

In dit artikel gebruikt u opdrachtregelgereedschappen om een functie te maken die reageert op HTTP-aanvragen. Nadat u de code lokaal hebt getest, implementeert u deze naar de serverloze omgeving van Azure-functies. Het voltooien van deze quickstart brengt een kleine kostenpost van enkele USD cent of minder in uw Azure-account met zich mee.

Er is ook een [Visual Studio Code-gebaseerde versie](functions-create-first-function-vs-code.md) van dit artikel.

[!INCLUDE [functions-requirements-cli](../../includes/functions-requirements-cli.md)]

[!INCLUDE [functions-cli-verify-prereqs](../../includes/functions-cli-verify-prereqs.md)]

[!INCLUDE [functions-cli-create-venv](../../includes/functions-cli-create-venv.md)]

## <a name="create-a-local-function-project"></a>Een lokaal functieproject maken

In Azure Functions is een functieproject een container voor een of meer afzonderlijke functies die elk reageert op een specifieke trigger. Alle functies in een project delen dezelfde lokale en hostingconfiguraties. In deze sectie maakt u een functieproject dat één functie bevat.

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"  
Voer `func init` de opdracht als volgt uit om een functieproject te maken in een map met de naam *LocalFunctionProj* met de opgegeven runtime:  
::: zone-end  
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
::: zone pivot="programming-language-java"  
Voer in een lege map de volgende opdracht uit om het Functions-project te genereren op basis van een [Maven-archetype](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html).

# <a name="bash"></a>[Bash](#tab/bash)
```bash
mvn archetype:generate -DarchetypeGroupId=com.microsoft.azure -DarchetypeArtifactId=azure-functions-archetype 
```
# <a name="powershell"></a>[PowerShell](#tab/powershell)
```powershell
mvn archetype:generate "-DarchetypeGroupId=com.microsoft.azure" "-DarchetypeArtifactId=azure-functions-archetype" 
```
# <a name="cmd"></a>[Cmd](#tab/cmd)
```cmd
mvn archetype:generate "-DarchetypeGroupId=com.microsoft.azure" "-DarchetypeArtifactId=azure-functions-archetype" 
```
---

Maven vraagt u om waarden die nodig zijn om het genereren van het project op implementatie te voltooien.   
Geef de volgende waarden op wanneer daarom wordt gevraagd:

| Vraag | Waarde | Beschrijving |
| ------ | ----- | ----------- |
| **groupId** | `com.fabrikam` | Een waarde die uw project op unieke wijze identificeert voor alle projecten, volgens de regels voor [pakketnaamgeving](https://docs.oracle.com/javase/specs/jls/se6/html/packages.html#7.7) voor Java. |
| **artefactId** | `fabrikam-functions` | Een waarde die de naam van de pot is, zonder versienummer. |
| **Versie** | `1.0-SNAPSHOT` | Kies de standaardwaarde. |
| **Pakket** | `com.fabrikam.functions` | Een waarde die het Java-pakket is voor de gegenereerde functiecode. Gebruik de standaard. |

Typ `Y` of druk op Enter om dit te bevestigen.

Maven maakt de projectbestanden in een nieuwe map met een `fabrikam-functions`naam van _artefactId_, die in dit voorbeeld is . 
::: zone-end  
Navigeer naar de projectmap:

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"  
```
cd LocalFunctionsProject
```
::: zone-end  
::: zone pivot="programming-language-java"  
```
cd fabrikam-functions
```
::: zone-end  
Deze map bevat verschillende bestanden voor het project, waaronder configuraties bestanden met de naam [local.settings.json](functions-run-local.md#local-settings-file) en [host.json](functions-host-json.md). Omdat *local.settings.json* geheimen kan bevatten die zijn gedownload van Azure, is het bestand standaard uitgesloten van bronbeheer in het *.gitignore-bestand.*

[!INCLUDE [functions-cli-add-function](../../includes/functions-cli-add-function.md)]

### <a name="optional-examine-the-file-contents"></a>(Optioneel) De inhoud van het bestand onderzoeken

Indien gewenst u [de functie lokaal uitvoeren](#run-the-function-locally) en de inhoud van het bestand later onderzoeken.

::: zone pivot="programming-language-csharp"
#### <a name="httpexamplecs"></a>HttpExample.cs

*HttpExample.cs* bevat `Run` een methode die `req` aanvraaggegevens in de variabele ontvangt, is een [HttpRequest](/dotnet/api/microsoft.aspnetcore.http.httprequest) die is versierd met het **HttpTriggerAttribute**, dat het triggergedrag definieert. 

:::code language="csharp" source="~/functions-docs-csharp/http-trigger-template/HttpExample.cs":::

Het retourobject is een [ActionResult](/dotnet/api/microsoft.aspnetcore.mvc.actionresult) dat een antwoordbericht retourneert als een [OkObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.okobjectresult) (200) of een [BadRequestObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.badrequestobjectresult) (400). Zie [HTTP-triggers en bindingen van Azure Functions HTTP](/azure/azure-functions/functions-bindings-http-webhook?tabs=csharp)voor meer informatie.
::: zone-end

::: zone pivot="programming-language-java"
#### <a name="functionjava"></a>Function.java
*Function.java* bevat `run` een methode die `request` aanvraaggegevens in de variabele ontvangt, is een [HttpRequestMessage](/java/api/com.microsoft.azure.functions.httprequestmessage) die is versierd met de HttpTrigger-annotatie, die het triggergedrag definieert. [HttpTrigger](/java/api/com.microsoft.azure.functions.annotation.httptrigger) 

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/main/java/com/function/Function.java":::

Het antwoordbericht wordt gegenereerd door de [HttpResponseMessage.Builder](/java/api/com.microsoft.azure.functions.httpresponsemessage.builder) API.

#### <a name="pomxml"></a>pom.xml

Instellingen voor de Azure-resources die zijn gemaakt om uw app te hosten, worden gedefinieerd in het **configuratie-element** van de plug-in met een **groupId** van `com.microsoft.azure` in het gegenereerde pom.xml-bestand. Het configuratie-element hieronder instrueert bijvoorbeeld een op Maven gebaseerde implementatie om een functie-app te maken in de `java-functions-group` resourcegroep in de `westus` regio. De functie-app zelf wordt `java-functions-app-service-plan` uitgevoerd op Windows gehost in het plan, dat standaard is een serverless Verbruik plan.    

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/pom.xml" range="116-155":::

U deze instellingen wijzigen om te bepalen hoe `runtime.os` resources `windows` `linux` in Azure worden gemaakt, bijvoorbeeld door over te schakelen van naar vóór de eerste implementatie. Zie de [configuratiegegevens](https://github.com/microsoft/azure-maven-plugins/wiki/Azure-Functions:-Configuration-Details)voor een volledige lijst met instellingen die worden ondersteund door de Maven-plug-in.

#### <a name="functiontestjava"></a>FunctionTest.java

Het archetype genereert ook een eenheidstest voor uw functie. Wanneer u uw functie wijzigt om bindingen toe te voegen of nieuwe functies aan het project toe te voegen, moet u de tests ook wijzigen in het bestand *FunctionTest.java.*
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

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell,programming-language-csharp"    
## <a name="create-supporting-azure-resources-for-your-function"></a>Ondersteunende Azure-resources maken voor uw functie

Voordat u uw functiecode implementeren in Azure, moet u drie bronnen maken:

- Een resourcegroep, een logische container voor gerelateerde resources.
- Een opslagaccount, dat status- en andere informatie over uw projecten bijhoudt.
- Een functie-app, die de omgeving biedt voor het uitvoeren van uw functiecode. Een functie-app wordt toegewezen aan uw lokale functieproject en stelt u in staat functies te groeperen als een logische eenheid voor eenvoudiger beheer, implementatie en delen van resources.

Gebruik de volgende Azure CLI-opdrachten om deze items te maken. Elke opdracht biedt JSON-uitvoer na voltooiing.

Als u dit nog niet hebt gedaan, meldt u zich aan bij Azure met de opdracht [AZ-aanmelding:](/cli/azure/reference-index#az-login)

    ```azurecli
    az login
    ```
    
Een resourcegroep maken met de opdracht [az group create](/cli/azure/group#az-group-create). In het volgende voorbeeld `AzureFunctionsQuickstart-rg` wordt `westeurope` een resourcegroep met de naam in de regio genaamerd. (U maakt over het algemeen uw resourcegroep en resources in `az account list-locations` een regio bij u in de buurt, met behulp van een beschikbare regio van de opdracht.)

```azurecli
az group create --name AzureFunctionsQuickstart-rg --location westeurope
```

> [!NOTE]
> U geen Linux- en Windows-apps hosten in dezelfde brongroep. Als u een bestaande `AzureFunctionsQuickstart-rg` resourcegroep hebt met de naam Een Windows-functie-app of web-app, moet u een andere brongroep gebruiken.
 
    
Maak een opslagaccount voor algemene doeleinden in uw resourcegroep en -regio met behulp van de opdracht [AZ-opslagaccount maken.](/cli/azure/storage/account#az-storage-account-create) Vervang in het `<STORAGE_NAME>` volgende voorbeeld een wereldwijd unieke naam die bij u past. Namen moeten alleen drie tot 24 tekens en kleine letters bevatten. `Standard_LRS`hiermee wordt een algemeen doelaccount opgegeven, dat wordt [ondersteund door functies](storage-considerations.md#storage-account-requirements).

```azurecli
az storage account create --name <STORAGE_NAME> --location westeurope --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
```

De opslag rekening duurt slechts een paar cent (USD) voor deze quickstart.
    
Maak de functie-app met de [opdracht AZ-functieapp create.](/cli/azure/functionapp#az-functionapp-create) Vervang in het `<STORAGE_NAME>` volgende voorbeeld de naam van het account dat `<APP_NAME>` u in de vorige stap hebt gebruikt en vervang een wereldwijd unieke naam die bij u past. De `<APP_NAME>` is ook het standaard DNS-domein voor de functie-app. 
::: zone-end  

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

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell,programming-language-csharp"  
Met deze opdracht wordt een functie-app gemaakt die wordt uitgevoerd in de opgegeven taalruntime onder het [Azure Functions Usage Plan](functions-scale.md#consumption-plan), die gratis is voor de hoeveelheid gebruik die u hier uitvoert. De opdracht voorziet ook in een bijbehorende Instantie Azure Application Insights in dezelfde resourcegroep, waarmee u uw functie-app controleren en logboeken weergeven. Zie [Azure-functies controleren](functions-monitoring.md)voor meer informatie . De instantie brengt geen kosten met zich mee totdat u deze activeert.
    
## <a name="deploy-the-function-project-to-azure"></a>Het functieproject implementeren in Azure
::: zone-end  

::: zone pivot="programming-language-typescript"  
Voordat u Core Tools gebruikt om uw project te implementeren in Azure, maakt u een productieklare build van JavaScript-bestanden uit de TypeScript-bronbestanden.

Met de volgende opdracht wordt uw TypeScript-project voorbereid op implementatie:

```
npm run build:production 
```
::: zone-end  

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell,programming-language-csharp"  
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

::: zone-end  
::: zone pivot="programming-language-java"  
## <a name="deploy-the-function-project-to-azure"></a>Het functieproject implementeren in Azure

Een functie-app en gerelateerde resources worden gemaakt in Azure wanneer u uw functieproject voor het eerst implementeert. Instellingen voor de Azure-resources die zijn gemaakt om uw app te hosten, worden gedefinieerd in het [pom.xml-bestand](#pomxml). In dit artikel accepteert u de standaardinstellingen.

> [!TIP]
> Als u een functie-app wilt maken `runtime.os` die wordt uitgevoerd op Linux `windows` `linux`in plaats van Windows, wijzigt u het element in het pom.xml-bestand van . Het uitvoeren van Linux in een consumptieplan wordt ondersteund in [deze regio's.](https://github.com/Azure/azure-functions-host/wiki/Linux-Consumption-Regions) U geen apps hebben die worden uitgevoerd op Linux en apps die op Windows worden uitgevoerd in dezelfde brongroep.

Voordat u implementeren, gebruikt u de opdracht Azure CLI voor [AZ-aanmelding](/cli/azure/authenticate-azure-cli) om u aan te melden bij uw Azure-abonnement. 

```azurecli
az login
```

Gebruik de volgende opdracht om uw project te implementeren in een nieuwe functie-app. 

```
mvn azure-functions:deploy
```

Hiermee worden de volgende bronnen in Azure gemaakt:

+ Resourcegroep. Genoemd als _java-functies-groep_.
+ Opslagaccount. Vereist door Functies. De naam wordt willekeurig gegenereerd op basis van de vereisten voor de naam van het opslagaccount.
+ Hosting plan. Serverloze hosting voor uw functie-app in de _westusregio._ De naam is _java-functions-app-service-plan._
+ Functie-app. Een functie-app is de implementatie- en uitvoeringseenheid voor uw functies. De naam wordt willekeurig gegenereerd op basis van je _artefactId,_ toegevoegd met een willekeurig gegenereerd nummer. 

De implementatie verpakt de projectbestanden en implementeert deze naar de nieuwe functie-app met [zip-implementatie.](functions-deployment-technologies.md#zip-deploy) De code wordt uitgevoerd vanuit het implementatiepakket in Azure.
::: zone-end

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
