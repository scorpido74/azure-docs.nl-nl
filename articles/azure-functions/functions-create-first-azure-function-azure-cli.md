---
title: Een functie in Azure maken die reageert op HTTP-aanvragen
description: Ontdek hoe u een functie maakt vanaf de opdrachtregel en vervolgens het lokale project publiceert op serverloze hosting in Azure Functions.
ms.date: 03/30/2020
ms.topic: quickstart
ms.custom: tracking-python
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: 86be7ec73d8e19597062f3fa3777f3aa422082c3
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86506346"
---
# <a name="quickstart-create-a-function-in-azure-that-responds-to-http-requests"></a>Quickstart: Een functie in Azure maken die reageert op HTTP-aanvragen

::: zone pivot="programming-language-csharp"  
In dit artikel gebruikt u opdrachtregelprogramma's om een op een C#-klassenbibliotheek gebaseerde functie te maken die reageert op HTTP-aanvragen. Nadat u de code lokaal hebt getest, implementeert u deze in de serverloze omgeving van Azure Functions. 
::: zone-end  
::: zone pivot="programming-language-javascript"
In dit artikel gebruikt u opdrachtregelprogramma’s om een JavaScript-functie te maken die reageert op HTTP-aanvragen. Nadat u de code lokaal hebt getest, implementeert u deze in de serverloze omgeving van Azure Functions. 
::: zone-end
::: zone pivot="programming-language-typescript"
In dit artikel gebruikt u opdrachtregelprogramma’s om een TypeScript-functie te maken die reageert op HTTP-aanvragen. Nadat u de code lokaal hebt getest, implementeert u deze in de serverloze omgeving van Azure Functions. 
::: zone-end   
::: zone pivot="programming-language-powershell"
In dit artikel gebruikt u opdrachtregelprogramma’s om een PowerShell-functie te maken die reageert op HTTP-aanvragen. Nadat u de code lokaal hebt getest, implementeert u deze in de serverloze omgeving van Azure Functions. 
::: zone-end  
::: zone pivot="programming-language-python" 
In dit artikel gebruikt u opdrachtregelprogramma’s om een Python-functie te maken die reageert op HTTP-aanvragen. Nadat u de code lokaal hebt getest, implementeert u deze in de serverloze omgeving van Azure Functions. 
::: zone-end  
::: zone pivot="programming-language-java" 
In dit artikel gebruikt u opdrachtregelprogramma's om een Java-functie te maken die reageert op HTTP-aanvragen. Nadat u de code lokaal hebt getest, implementeert u deze in de serverloze omgeving van Azure Functions. 
::: zone-end

Voor het voltooien van deze quickstart worden kosten van een paar dollarcent of minder in rekening gebracht bij uw Azure-account.

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"  
Er is ook een [Versie op basis van Visual Studio Code](functions-create-first-function-vs-code.md) van dit artikel.
::: zone-end  
::: zone pivot="programming-language-java"  
> [!NOTE]
> Als u liever niet ontwikkelt met Maven, raadpleegt u onze vergelijkbare zelfstudies voor Java-ontwikkelaars met [Gradle](./functions-create-first-java-gradle.md), [IntelliJ IDEA](/azure/developer/java/toolkit-for-intellij/quickstart-functions) en [Visual Studio Code](./functions-create-first-function-vs-code.md?pivots=programming-language-java).
::: zone-end  

[!INCLUDE [functions-requirements-cli](../../includes/functions-requirements-cli.md)]

[!INCLUDE [functions-cli-verify-prereqs](../../includes/functions-cli-verify-prereqs.md)]

[!INCLUDE [functions-cli-create-venv](../../includes/functions-cli-create-venv.md)]

## <a name="create-a-local-function-project"></a>Een lokaal functieproject maken

In Azure Functions is een functieproject een container voor een of meer afzonderlijke functies die elk op een bepaalde trigger reageren. Alle functies in een project delen dezelfde lokale configuratie en hostingconfiguratie. In deze sectie maakt u een functieproject dat één functie bevat.

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"  
Voer de opdracht `func init` als volgt uit om een functieproject te maken in een map met de naam *LocalFunctionProj* met de opgegeven runtime:  
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

# <a name="bash"></a>[bash](#tab/bash)
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

U wordt door Maven gevraagd om de waarden die nodig zijn om het project op het moment van implementatie te kunnen genereren.   
Geef de volgende waarden op als daarom wordt gevraagd:

| Vraag | Waarde | Beschrijving |
| ------ | ----- | ----------- |
| **groupId** | `com.fabrikam` | Een waarde die uw project uniek identificeert binnen alle projecten, overeenkomstig de [regels voor de naamgeving van pakketten](https://docs.oracle.com/javase/specs/jls/se6/html/packages.html#7.7) voor Java. |
| **artifactId** | `fabrikam-functions` | Een waarde die bestaat uit de naam van het JAR-bestand, zonder een versienummer. |
| **version** | `1.0-SNAPSHOT` | Kies de standaardwaarde. |
| **package** | `com.fabrikam` | Een waarde die het Java-pakket aangeeft voor de gegenereerde functiecode. Gebruik de standaard. |

Typ `Y` of druk op Enter om te bevestigen.

Maven maakt de projectbestanden in een nieuwe map met de naam van _artifactId_; in dit voorbeeld is dat `fabrikam-functions`. 
::: zone-end  
Navigeer naar de projectmap:

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"  
```
cd LocalFunctionProj
```
::: zone-end  
::: zone pivot="programming-language-java"  
```
cd fabrikam-functions
```
::: zone-end  
Deze map bevat verschillende bestanden voor het project,waaronder configuratiebestanden met de naam [local.settings.json](functions-run-local.md#local-settings-file) en [host.json](functions-host-json.md). Omdat *local.settings.json* geheimen kan bevatten die zijn gedownload vanuit Azure, wordt het bestand standaard uitgesloten van broncodebeheer in het bestand *.gitignore*.

[!INCLUDE [functions-cli-add-function](../../includes/functions-cli-add-function.md)]

### <a name="optional-examine-the-file-contents"></a>(Optioneel) De inhoud van het bestand bekijken

Desgewenst kunt u doorgaan naar [De functie lokaal uitvoeren](#run-the-function-locally) en de inhoud van het bestand later bekijken.

::: zone pivot="programming-language-csharp"
#### <a name="httpexamplecs"></a>HttpExample.cs

*HttpExample.cs* bevat een `Run`-methode waarmee aanvraaggegevens worden ontvangen in de `req`-variabele. Dit is een [HttpRequest](/dotnet/api/microsoft.aspnetcore.http.httprequest) die is gedecoreerd met **HttpTriggerAttribute**, waarmee het gedrag van de trigger wordt gedefinieerd. 

:::code language="csharp" source="~/functions-docs-csharp/http-trigger-template/HttpExample.cs":::

Het retourobject is een [ActionResult](/dotnet/api/microsoft.aspnetcore.mvc.actionresult) die een antwoordbericht retourneert als een [OkObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.okobjectresult) (200) of een [BadRequestObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.badrequestobjectresult) (400). Zie [Azure Functions HTTP-triggers en -bindingen](./functions-bindings-http-webhook.md?tabs=csharp) voor meer informatie.
::: zone-end

::: zone pivot="programming-language-java"
#### <a name="functionjava"></a>Function.java
*Function.java* bevat een `run`-methode waarmee aanvraaggegevens in de `request`-variabele worden ontvangen. Dit is een [HttpRequestMessage](/java/api/com.microsoft.azure.functions.httprequestmessage) die is gedecoreerd met de aantekening [HttpTrigger](/java/api/com.microsoft.azure.functions.annotation.httptrigger), waarmee het gedrag van de trigger wordt gedefinieerd. 

:::code language="java" source="~/azure-functions-samples-java/src/main/java/com/functions/Function.java":::

Het antwoordbericht wordt gegenereerd door de API [HttpResponseMessage.Builder](/java/api/com.microsoft.azure.functions.httpresponsemessage.builder).

#### <a name="pomxml"></a>pom.xml

Instellingen voor de Azure-resources die zijn gemaakt voor het hosten van uw app, worden gedefinieerd in het element **Configuratie** van de invoegtoepassing met een **groupId** van `com.microsoft.azure` in het gegenereerde bestand pom.xml. Het configuratie-element hieronder geeft bijvoorbeeld een Maven-implementatie de instructie om een functie-app te maken in de resourcegroep `java-functions-group` in de regio `westus`. De functie-app zelf wordt uitgevoerd in Windows binnen het `java-functions-app-service-plan`-plan. Dit is standaard een serverloos verbruiksabonnement.    

:::code language="java" source="~/azure-functions-samples-java/pom.xml" range="62-102":::

U kunt deze instellingen wijzigen om te bepalen hoe resources worden gemaakt in Azure, zoals door `runtime.os` van `windows` te wijzigen in `linux` vóór de eerste implementatie. Zie de [Configuratiedetails](https://github.com/microsoft/azure-maven-plugins/wiki/Azure-Functions:-Configuration-Details)voor een volledige lijst met instellingen die worden ondersteund door de Maven-invoegtoepassing.

#### <a name="functiontestjava"></a>FunctionTest.java

Het archetype genereert ook een moduletest voor uw functie. Wanneer u de functie wijzigt om bindingen of nieuwe functies aan het project toe te voegen, moet u ook de tests in het bestand *FunctionTest.java* wijzigen.
::: zone-end  
::: zone pivot="programming-language-python"
#### <a name="__init__py"></a>\_\_init\_\_.py

*\_\_init\_\_.py* bevat de Python-functie`main()` die wordt geactiveerd op basis van de configuratie in *function.json*.

:::code language="python" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/__init__.py":::

Voor een HTTP-trigger ontvangt de functie aanvraaggegevens in de variabele `req` zoals gedefinieerd in *function.json*. `req` is een instantie van de [azure.functions.HttpRequest-klasse](/python/api/azure-functions/azure.functions.httprequest). Het retourobject, gedefinieerd als `$return` in *function.json*, is een instantie van [azure.functions.HttpResponse-klasse](/python/api/azure-functions/azure.functions.httpresponse). Zie [Azure Functions HTTP-triggers en -bindingen](./functions-bindings-http-webhook.md?tabs=python) voor meer informatie.
::: zone-end

::: zone pivot="programming-language-javascript"
#### <a name="indexjs"></a>index.js

*index.js* exporteert een functie die wordt geactiveerd op basis van de configuratie in *function.json*.

:::code language="javascript" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-JavaScript/index.js":::

Voor een HTTP-trigger ontvangt de functie aanvraaggegevens in de variabele `req` zoals gedefinieerd in *function.json*. Het retourobject, gedefinieerd als `$return` in *function.json*, is de reactie. Zie [Azure Functions HTTP-triggers en -bindingen](./functions-bindings-http-webhook.md?tabs=javascript) voor meer informatie.
::: zone-end

::: zone pivot="programming-language-typescript"
#### <a name="indexts"></a>index.ts

*index.ts* exporteert een functie die wordt geactiveerd op basis van de configuratie in *function.json*.

:::code language="typescript" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-TypeScript/index.ts":::

Voor een HTTP-trigger ontvangt de functie aanvraaggegevens in de variabele `req` van het type **HttpRequest** zoals gedefinieerd in *function.json*. Het retourobject, gedefinieerd als `$return` in *function.json*, is de reactie. 
::: zone-end

::: zone pivot="programming-language-powershell"
#### <a name="runps1"></a>run.ps1

*run.ps1* definieert een functiescript die wordt geactiveerd op basis van de configuratie in *function.json*.

:::code language="powershell" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-PowerShell/run.ps1":::

Voor een HTTP-trigger ontvangt de functie aanvraaggegevens die worden verzonden naar de parameter `$Request` zoals gedefinieerd in *function.json*. Het retourobject, gedefinieerd als `Response` in *function.json*, wordt doorgestuurd naar de cmdlet `Push-OutputBinding` als reactie. 
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell"
#### <a name="functionjson"></a>function.json

*function.json* is een configuratiebestand dat de invoer en uitvoer `bindings` definieert voor de functie, met inbegrip van het triggertype. 
::: zone-end

::: zone pivot="programming-language-python"
U kunt `scriptFile` zo nodig wijzigen om een ander Python-bestand aan te roepen.

:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json":::
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript"
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-JavaScript/function.json":::
::: zone-end

::: zone pivot="programming-language-powershell"
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-PowerShell/function.json":::
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell"  
Elke binding vereist een richting, een type en een unieke naam. De HTTP-trigger heeft een invoerbinding van het type [`httpTrigger`](functions-bindings-http-webhook-trigger.md) en uitvoerbinding van het type [`http`](functions-bindings-http-webhook-output.md).
::: zone-end  

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell,programming-language-csharp"    
## <a name="create-supporting-azure-resources-for-your-function"></a>Ondersteunende Azure-resources maken voor uw functie

Voordat u uw functiecode kunt implementeren in Azure, moet u drie resources maken:

- Een resourcegroep, wat een logische container is voor gerelateerde resources.
- Een Storage-account, waarin de status en andere gegevens van uw projecten worden onderhouden.
- Een functie-app, die de omgeving biedt voor het uitvoeren van uw functiecode. Een functie-app wordt gekoppeld aan uw lokale functieproject en maakt het mogelijk om functies te groeperen als een logische eenheid, zodat u resources eenvoudiger kunt beheren, implementeren en delen.

Gebruik de volgende opdrachten van de Azure CLI om deze items te maken. Elke opdracht biedt JSON-uitvoer bij voltooiing.

Als u dit nog niet hebt gedaan, meldt u zich aan bij Azure met de opdracht [az login](/cli/azure/reference-index#az-login):

```azurecli
az login
```
    
Een resourcegroep maken met de opdracht [az group create](/cli/azure/group#az-group-create). In het volgende voorbeeld wordt een resourcegroep met de naam `AzureFunctionsQuickstart-rg` gemaakt in de regio `westeurope`. (Over het algemeen maakt u een resourcegroep en resources in een regio bij u in de buurt, waarbij u een beschikbare regio kiest met behulp van de opdracht `az account list-locations`.)

```azurecli
az group create --name AzureFunctionsQuickstart-rg --location westeurope
```

> [!NOTE]
> Het is niet mogelijk om Linux- en Windows-apps in dezelfde resourcegroep te hosten. Als u een bestaande resourcegroep met de naam `AzureFunctionsQuickstart-rg` hebt die een Windows-functie-app of web-app bevat, moet u een andere resourcegroep gebruiken.
 
    
Maak een algemeen opslagaccount in de resourcegroep en regio met behulp van de opdracht [az storage account create](/cli/azure/storage/account#az-storage-account-create). Vervang in het volgende voorbeeld `<STORAGE_NAME>` door een globaal unieke naam van uw keuze. Namen mogen drie tot 24 tekens bevatten en u mag alleen kleine letters gebruiken. Met `Standard_LRS` geeft u een account voor algemeen gebruik op dat wordt [ondersteund door Functions](storage-considerations.md#storage-account-requirements).

```azurecli
az storage account create --name <STORAGE_NAME> --location westeurope --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
```

Voor het opslagaccount worden gedurende deze quickstart slechts een paar dollarcenten in rekening gebracht.
    
Maak de functie-app met behulp van de opdracht [az functionapp create](/cli/azure/functionapp#az-functionapp-create). Vervang in het volgende voor beeld `<STORAGE_NAME>` door de naam van het account dat u in de vorige stap hebt gebruikt en vervang `<APP_NAME>` door een unieke naam die voor u van betekenis is. De `<APP_NAME>` is ook het standaard DNS-domein voor de functie-app. 
::: zone-end  

::: zone pivot="programming-language-python"  
Als u gebruikmaakt van Python 3.8, wijzigt u `--runtime-version` in `3.8` en `--functions_version` in `3`.

Als u gebruikmaakt van Python 3.6, wijzigt u `--runtime-version` in `3.6`.

```azurecli
az functionapp create --resource-group AzureFunctionsQuickstart-rg --os-type Linux --consumption-plan-location westeurope --runtime python --runtime-version 3.7 --functions-version 2 --name <APP_NAME> --storage-account <STORAGE_NAME>
```
::: zone-end  

::: zone pivot="programming-language-javascript,programming-language-typescript"  
Als u Node.js 8 gebruikt, wijzigt u ook `--runtime-version` in `8`.


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
Met deze opdracht maakt u een functie-app die wordt uitgevoerd in de runtime van uw opgegeven taal binnen het [Azure Functions-verbruiksplan](functions-scale.md#consumption-plan). Dit is gratis voor het gebruik dat u hier maakt. Met deze opdracht wordt in dezelfde resourcegroep ook een gekoppelde instantie van Azure Application Insights ingericht, waarmee u uw functie-app kunt bewaken en logboeken kunt weergeven. Zie [Monitor Azure Functions](functions-monitoring.md) (Azure Functions bewaken) voor meer informatie. Er worden pas kosten in rekening gebracht voor de instantie als u deze activeert.
    
## <a name="deploy-the-function-project-to-azure"></a>Het functieproject implementeren in Azure
::: zone-end  

::: zone pivot="programming-language-typescript"  
Voordat u kernhulpprogramma's gebruikt om uw project te implementeren in Azure, maakt u een build die gereed voor productie is van JavaScript-bestanden van de TypeScript-bronbestanden.

Met de volgende opdracht wordt uw TypeScript-project voorbereid voor implementatie:

```
npm run build:production 
```
::: zone-end  

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell,programming-language-csharp"  
Met de benodigde resources kunt u nu uw lokale functieproject implementeren in de functie-app in Azure met behulp van de opdracht [func azure functionapp publish](functions-run-local.md#project-file-deployment). Vervang `<APP_NAME>` in het volgende voorbeeld door de naam van uw app.

```
func azure functionapp publish <APP_NAME>
```

Als u de foutmelding 'Kan de app met de naam ... niet vinden' ziet, wacht u een paar seconden en probeert u het opnieuw. De app is mogelijk niet volledig geïnitialiseerd door Azure na de vorige opdracht `az functionapp create`.

Met de publicatieopdracht worden de resultaten weergegeven die vergelijkbaar zijn met de volgende uitvoer (afgekapt voor de leesbaarheid):

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

Er worden een functie-app en gerelateerde resources in Azure gemaakt wanneer u uw functieproject voor het eerst implementeert. De instellingen voor de Azure-resources die zijn gemaakt om uw app te hosten, worden gedefinieerd in het [pom.xml-bestand](#pomxml). In dit artikel gaat u akkoord met de standaardinstellingen.

> [!TIP]
> Als u een functie-app wilt maken die wordt uitgevoerd op Linux in plaats van Windows, wijzigt u het `runtime.os`-element in het pom.xml-bestand van `windows` in `linux`. Het uitvoeren van Linux in een verbruiksplan wordt ondersteund in [deze regio's](https://github.com/Azure/azure-functions-host/wiki/Linux-Consumption-Regions). U kunt apps die worden uitgevoerd op Linux en apps die worden uitgevoerd in Windows niet dezelfde resourcegroep gebruiken.

Voordat u de implementeren kunt starten moet u de Azure CLI-opdracht [az login](/cli/azure/authenticate-azure-cli) gebruiken om u aan te melden bij uw Azure-abonnement. 

```azurecli
az login
```

Gebruik de volgende opdracht om uw project te implementeren in een nieuwe functie-app. 

```
mvn azure-functions:deploy
```

Hiermee maakt u de volgende resources in Azure:

+ Resourcegroep. Met de naam _java-functions-group_.
+ Opslagaccount. Vereist door Funtions. De naam wordt willekeurig gegenereerd op basis van de vereisten van het opslagaccount.
+ Hostingabonnement. Serverloze hosting voor uw functie-app in de regio _westus_. De naam is _java-functions-app-service-plan_.
+ Functie-app. Een functie-app is de implementatie- en uitvoeringseenheid voor uw functies. De naam wordt willekeurig gegenereerd op basis van uw _artifactId_,waaraan een willekeurig gegenereerd nummer wordt toegevoegd. 

De implementatie verpakt de projectbestanden en implementeert deze in de nieuwe functie-app met behulp van [zip implementation](functions-deployment-technologies.md#zip-deploy). De code wordt uitgevoerd vanuit het implementatiepakket in Azure.
::: zone-end

## <a name="invoke-the-function-on-azure"></a>De functie aanroepen in Azure

Omdat uw functie gebruikmaakt van een HTTP-trigger, roept u deze aan door een HTTP-aanvraag naar de URL in de browser of met een hulpprogramma zoals curl. In beide gevallen is de parameter-URL `code` uw unieke [functiesleutel](functions-bindings-http-webhook-trigger.md#authorization-keys) die de aanroep van uw functie-eindpunt toestaat.

# <a name="browser"></a>[Browser](#tab/browser)

Kopieer de volledige **Aanroep-URL** die wordt weergegeven in de uitvoer van de publicatieopdracht naar de adresbalk van een browser en voeg de queryparameter `&name=Functions` toe. De browser moet vergelijkbare uitvoer weergeven als u de functie lokaal hebt uitgevoerd.

![De uitvoer van de functie die wordt uitgevoerd op Azure in een browser](./media/functions-create-first-azure-function-azure-cli/function-test-cloud-browser.png)


# <a name="curl"></a>[curl](#tab/curl)

Voer [`curl`](https://curl.haxx.se/) uit met de **aanroep-URL** en voeg de parameter `&name=Functions` toe. De uitvoer van de opdracht moet de tekst ‘Hallo Functions’ zijn.

![De uitvoer van de functie die wordt uitgevoerd op Azure met behulp van curl](./media/functions-create-first-azure-function-azure-cli/function-test-cloud-curl.png)

---

> [!TIP]
> Als u bijna realtime logboeken voor een gepubliceerde functie-app wilt weergeven, gebruikt u de [Application Insights Live Metrics Stream](functions-monitoring.md#streaming-logs).
>
> Voer de volgende opdracht uit om de live gegevensstroom in een browser te openen.
>   ```
>   func azure functionapp logstream <APP_NAME> --browser
>   ```

## <a name="clean-up-resources"></a>Resources opschonen

Als u verdergaat met de volgende stap, [Een uitvoerbinding voor een Azure Storage-wachtrij toevoegen](functions-add-output-binding-storage-queue-cli.md), kunt u alle resources het beste op dezelfde plaats laten staan. U gaat ze namelijk gebruiken in deze stap.

Gebruik anders de volgende opdracht om de resourcegroep en alle bijbehorende resources te verwijderen om te voorkomen dat er verdere kosten in rekening worden gebracht.

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell,programming-language-csharp" 
```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```
::: zone-end
::: zone pivot="programming-language-java"
```azurecli
az group delete --name java-functions-group
```
::: zone-end
::: zone pivot="programming-language-python"
Voer `deactivate` uit om de virtuele omgeving te sluiten.
::: zone-end

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Verbinding maken met een Azure Storage-wachtrij](functions-add-output-binding-storage-queue-cli.md)
 
