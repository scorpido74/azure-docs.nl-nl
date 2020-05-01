---
title: Een functie in azure maken die reageert op HTTP-aanvragen
description: Leer hoe u een functie maakt vanaf de opdracht regel en vervolgens het lokale project publiceert op serverloze hosting in Azure Functions.
ms.date: 03/30/2020
ms.topic: quickstart
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: 5022ff7239a040a163d8d6a82864d0dee8e65f1e
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/30/2020
ms.locfileid: "82610901"
---
::: zone pivot="programming-language-csharp"  
# <a name="quickstart-create-a-c-function-in-azure-that-responds-to-http-requests"></a>Quick Start: een C#-functie maken in azure die reageert op HTTP-aanvragen

In dit artikel gebruikt u opdracht regel Programma's voor het maken van een op een C#-klassen bibliotheek gebaseerde functie die reageert op HTTP-aanvragen. Nadat u de code lokaal hebt getest, implementeert u deze in de serverloze omgeving van Azure Functions. Het volt ooien van deze Quick Start brengt een kleine prijs van een paar USD cent of minder in uw Azure-account.
::: zone-end  
::: zone pivot="programming-language-javascript"
# <a name="quickstart-create-a-javascript-function-in-azure-that-responds-to-http-requests"></a>Quick Start: een Java script-functie maken in azure die reageert op HTTP-aanvragen

In dit artikel gebruikt u opdracht regel Programma's voor het maken van een Java script-functie die reageert op HTTP-aanvragen. Nadat u de code lokaal hebt getest, implementeert u deze in de serverloze omgeving van Azure Functions. Het volt ooien van deze Quick Start brengt een kleine prijs van een paar USD cent of minder in uw Azure-account.
::: zone-end
::: zone pivot="programming-language-typescript"
# <a name="quickstart-create-a-typescript-function-in-azure-that-responds-to-http-requests"></a>Quick Start: een type script-functie maken in azure die reageert op HTTP-aanvragen

In dit artikel gebruikt u opdracht regel Programma's voor het maken van een type script-functie die reageert op HTTP-aanvragen. Nadat u de code lokaal hebt getest, implementeert u deze in de serverloze omgeving van Azure Functions. Het volt ooien van deze Quick Start brengt een kleine prijs van een paar USD cent of minder in uw Azure-account.
::: zone-end   
::: zone pivot="programming-language-powershell"
# <a name="quickstart-create-a-powershell-function-in-azure-that-responds-to-http-requests"></a>Quick Start: een Power shell-functie maken in azure die reageert op HTTP-aanvragen

In dit artikel gebruikt u opdracht regel Programma's om een Power shell-functie te maken die reageert op HTTP-aanvragen. Nadat u de code lokaal hebt getest, implementeert u deze in de serverloze omgeving van Azure Functions. Het volt ooien van deze Quick Start brengt een kleine prijs van een paar USD cent of minder in uw Azure-account.
::: zone-end  
::: zone pivot="programming-language-python" 
# <a name="quickstart-create-a-python-function-in-azure-that-responds-to-http-requests"></a>Quick Start: een python-functie maken in azure die reageert op HTTP-aanvragen

In dit artikel gebruikt u opdracht regel Programma's voor het maken van een python-functie die reageert op HTTP-aanvragen. Nadat u de code lokaal hebt getest, implementeert u deze in de serverloze omgeving van Azure Functions. Het volt ooien van deze Quick Start brengt een kleine prijs van een paar USD cent of minder in uw Azure-account.
::: zone-end  
::: zone pivot="programming-language-java" 
# <a name="quickstart-create-a-java-function-in-azure-that-responds-to-http-requests"></a>Quick Start: een Java-functie maken in azure die reageert op HTTP-aanvragen

In dit artikel gebruikt u opdracht regel Programma's voor het maken van een Java-functie die reageert op HTTP-aanvragen. Nadat u de code lokaal hebt getest, implementeert u deze in de serverloze omgeving van Azure Functions. Het volt ooien van deze Quick Start brengt een kleine prijs van een paar USD cent of minder in uw Azure-account.
::: zone-end

In dit artikel gebruikt u opdracht regel Programma's voor het maken van een functie die reageert op HTTP-aanvragen. Nadat u de code lokaal hebt getest, implementeert u deze in de serverloze omgeving van Azure Functions. Het volt ooien van deze Quick Start brengt een kleine prijs van een paar USD cent of minder in uw Azure-account.

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"  
Er is ook een [versie op basis van Visual Studio code](functions-create-first-function-vs-code.md) van dit artikel.
::: zone-end  

::: zone pivot="programming-language-java"  
> [!NOTE]
> Als Maven niet uw voor keur is, Bekijk dan onze vergelijk bare zelf studies voor Java-Ontwikkel aars met [Gradle](/azure/azure-functions/functions-create-first-java-gradle), [IntelliJ idee](/azure/developer/java/toolkit-for-intellij/quickstart-functions) en [VS code](/azure/azure-functions/functions-create-first-function-vs-code?pivots=programming-language-java).
::: zone-end  

[!INCLUDE [functions-requirements-cli](../../includes/functions-requirements-cli.md)]

[!INCLUDE [functions-cli-verify-prereqs](../../includes/functions-cli-verify-prereqs.md)]

[!INCLUDE [functions-cli-create-venv](../../includes/functions-cli-create-venv.md)]

## <a name="create-a-local-function-project"></a>Een lokale functie project maken

In Azure Functions is een functie project een container voor een of meer afzonderlijke functies die elk op een bepaalde trigger reageert. Alle functies in een project delen dezelfde lokale en hosting configuraties. In deze sectie maakt u een functie project dat één functie bevat.

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"  
Voer de `func init` opdracht als volgt uit om een functions-project te maken in een map met de naam *LocalFunctionProj* met de opgegeven runtime:  
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

Maven vraagt u om de waarden die nodig zijn om het project te kunnen genereren tijdens de implementatie.   
Geef de volgende waarden op wanneer u hierom wordt gevraagd:

| Vraag | Waarde | Beschrijving |
| ------ | ----- | ----------- |
| **Groep** | `com.fabrikam` | Een waarde die uw project op unieke wijze identificeert voor alle projecten, volgens de [pakket naamgevings regels](https://docs.oracle.com/javase/specs/jls/se6/html/packages.html#7.7) voor Java. |
| **artifactId** | `fabrikam-functions` | Een waarde die bestaat uit de naam van het jar, zonder een versie nummer. |
| **Versie** | `1.0-SNAPSHOT` | Kies de standaard waarde. |
| **pakket** | `com.fabrikam.functions` | Een waarde die het Java-pakket voor de gegenereerde functie code is. Gebruik de standaard. |

Typ `Y` of druk op ENTER om te bevestigen.

Maven maakt de project bestanden in een nieuwe map met de naam _artifactId_, in dit voor beeld `fabrikam-functions`. 
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
Deze map bevat verschillende bestanden voor het project, met inbegrip van configuratie bestanden met de naam [Local. settings. json](functions-run-local.md#local-settings-file) en [host. json](functions-host-json.md). Omdat *Local. settings. json* geheimen kan bevatten die zijn gedownload van Azure, wordt het bestand standaard uitgesloten van broncode beheer in het *. gitignore* -bestand.

[!INCLUDE [functions-cli-add-function](../../includes/functions-cli-add-function.md)]

### <a name="optional-examine-the-file-contents"></a>Beschrijving De inhoud van het bestand controleren

Desgewenst kunt u [de functie lokaal uitvoeren](#run-the-function-locally) en de bestands inhoud later bekijken.

::: zone pivot="programming-language-csharp"
#### <a name="httpexamplecs"></a>HttpExample.cs

*HttpExample.cs* bevat een `Run` methode waarmee aanvraag gegevens in de `req` variabele worden ontvangen, een [HttpRequest](/dotnet/api/microsoft.aspnetcore.http.httprequest) is dat is gedecoreerd met de **HttpTriggerAttribute**, waarmee het gedrag van de trigger wordt gedefinieerd. 

:::code language="csharp" source="~/functions-docs-csharp/http-trigger-template/HttpExample.cs":::

Het retour object is een [ActionResult](/dotnet/api/microsoft.aspnetcore.mvc.actionresult) die een antwoord bericht retourneert als een [OkObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.okobjectresult) (200) of een [BadRequestObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.badrequestobjectresult) (400). Zie [Azure functions HTTP-triggers en-bindingen](/azure/azure-functions/functions-bindings-http-webhook?tabs=csharp)voor meer informatie.
::: zone-end

::: zone pivot="programming-language-java"
#### <a name="functionjava"></a>Function. java
*Function. java* bevat een `run` methode die aanvraag gegevens ontvangt in de `request` variabele is een [HttpRequestMessage](/java/api/com.microsoft.azure.functions.httprequestmessage) die is voorzien van de [http trigger](/java/api/com.microsoft.azure.functions.annotation.httptrigger) -annotatie, waarmee het gedrag van de trigger wordt gedefinieerd. 

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/main/java/com/function/Function.java":::

Het antwoord bericht wordt gegenereerd door de API [HttpResponseMessage. Builder](/java/api/com.microsoft.azure.functions.httpresponsemessage.builder) .

#### <a name="pomxml"></a>Pom. XML

Instellingen voor de Azure-resources die zijn gemaakt voor het hosten van uw app, worden gedefinieerd in het **configuratie** - `com.microsoft.azure` element van de invoeg toepassing met een **groupid** van in het gegenereerde pom. XML-bestand. Het configuratie-element hieronder geeft bijvoorbeeld een Maven-implementatie voor het maken van een functie-app in `java-functions-group` de resource groep in `westus` de regio. De functie-app zelf wordt uitgevoerd in Windows die `java-functions-app-service-plan` wordt gehost in het plan, wat standaard een Serverloos verbruiks abonnement is.    

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/pom.xml" range="116-155":::

U kunt deze instellingen wijzigen om te bepalen hoe resources in Azure worden gemaakt, bijvoorbeeld door te `runtime.os` wijzigen `windows` van `linux` naar vóór de eerste implementatie. Zie de [Configuratie Details](https://github.com/microsoft/azure-maven-plugins/wiki/Azure-Functions:-Configuration-Details)voor een volledige lijst met instellingen die worden ondersteund door de Maven-invoeg toepassing.

#### <a name="functiontestjava"></a>FunctionTest. java

De archetype genereert ook een eenheids test voor uw functie. Wanneer u de functie wijzigt om bindingen toe te voegen of nieuwe functies aan het project toe te voegen, moet u ook de tests in het bestand *FunctionTest. java* wijzigen.
::: zone-end  
::: zone pivot="programming-language-python"
#### <a name="__init__py"></a>\_\_init\_\_. py

*\_\_\_init\_. py* bevat een `main()` python-functie die wordt geactiveerd volgens de configuratie in *Function. json*.

:::code language="python" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/__init__.py":::

Voor een HTTP-trigger ontvangt de functie aanvraag gegevens in de variabele `req` zoals gedefinieerd in *Function. json*. `req`is een instantie van de [klasse Azure. functions. HttpRequest](/python/api/azure-functions/azure.functions.httprequest). Het retour object, gedefinieerd als `$return` in *Function. json*, is een instantie van de [klasse Azure. functions. HttpResponse](/python/api/azure-functions/azure.functions.httpresponse). Zie [Azure functions HTTP-triggers en-bindingen](/azure/azure-functions/functions-bindings-http-webhook?tabs=python)voor meer informatie.
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

Voor een HTTP-trigger ontvangt de functie gegevens over aanvragen die zijn `$Request` door gegeven aan de para meter die is gedefinieerd in *Function. json*. Het retour object, gedefinieerd als `Response` in *Function. json*, wordt door gegeven aan `Push-OutputBinding` de cmdlet als de reactie. 
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell"
#### <a name="functionjson"></a>function.json

*Function. json* is een configuratie bestand dat de invoer en uitvoer `bindings` definieert voor de functie, met inbegrip van het trigger type. 
::: zone-end

::: zone pivot="programming-language-python"
U kunt zo `scriptFile` nodig wijzigen om een ander python-bestand aan te roepen.

:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json":::
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript"
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-JavaScript/function.json":::
::: zone-end

::: zone pivot="programming-language-powershell"
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-PowerShell/function.json":::
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell"  
Elke binding vereist een richting, een type en een unieke naam. De HTTP-trigger heeft een invoer binding van [`httpTrigger`](functions-bindings-http-webhook-trigger.md) het type en de uitvoer [`http`](functions-bindings-http-webhook-output.md)binding van het type.
::: zone-end  

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell,programming-language-csharp"    
## <a name="create-supporting-azure-resources-for-your-function"></a>Ondersteunende Azure-resources maken voor uw functie

Voordat u uw functie code kunt implementeren in azure, moet u drie resources maken:

- Een resource groep, een logische container voor gerelateerde resources.
- Een opslag account, waarmee de status en andere informatie over uw projecten worden bijgehouden.
- Een functie-app, waarmee u de omgeving voor het uitvoeren van uw functie code kunt gebruiken. Een functie-app wordt toegewezen aan uw lokale functie project en biedt u de mogelijkheid om functies te groeperen als logische eenheid, zodat u resources eenvoudiger kunt beheren, implementeren en delen.

Gebruik de volgende Azure CLI-opdrachten om deze items te maken. Elke opdracht biedt JSON-uitvoer na voltooiing.

Als u dit nog niet hebt gedaan, meldt u zich aan bij Azure met de opdracht [AZ login](/cli/azure/reference-index#az-login) :

```azurecli
az login
```
    
Een resourcegroep maken met de opdracht [az group create](/cli/azure/group#az-group-create). In het volgende voor beeld wordt een resource `AzureFunctionsQuickstart-rg` groep met `westeurope` de naam in de regio gemaakt. (In het algemeen maakt u de resource groep en-resources in een regio bij u in de buurt met `az account list-locations` behulp van een beschik bare regio van de opdracht.)

```azurecli
az group create --name AzureFunctionsQuickstart-rg --location westeurope
```

> [!NOTE]
> U kunt geen Linux-en Windows-apps hosten in dezelfde resource groep. Als u een bestaande resource groep hebt met `AzureFunctionsQuickstart-rg` de naam met een Windows-functie-app of web-app, moet u een andere resource groep gebruiken.
 
    
Maak een opslag account voor algemeen gebruik in uw resource groep en regio met behulp van de opdracht [AZ Storage account create](/cli/azure/storage/account#az-storage-account-create) . Vervang `<STORAGE_NAME>` in het volgende voor beeld door een globaal unieke naam die geschikt is voor u. Namen mogen alleen uit drie tot 24 tekens bestaan en mag alleen kleine letters bevatten. `Standard_LRS`Hiermee geeft u een account voor algemeen gebruik op dat wordt [ondersteund door-functies](storage-considerations.md#storage-account-requirements).

```azurecli
az storage account create --name <STORAGE_NAME> --location westeurope --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
```

Het opslag account heeft slechts een paar cent (USD) voor deze Quick Start.
    
Maak de functie-app met behulp van de opdracht [AZ functionapp Create](/cli/azure/functionapp#az-functionapp-create) . Vervang `<STORAGE_NAME>` in het volgende voor beeld door de naam van het account dat u in de vorige stap hebt gebruikt en `<APP_NAME>` Vervang door een wereld wijd unieke naam die geschikt is voor u. De `<APP_NAME>` is ook het standaard DNS-domein voor de functie-app. 
::: zone-end  

::: zone pivot="programming-language-python"  
Als u gebruikmaakt van python 3,8, wijzigt `--runtime-version` u `3.8` in `--functions_version` en `3`naar.

Als u gebruikmaakt van python 3,6, wijzigt `--runtime-version` u `3.6`in.

```azurecli
az functionapp create --resource-group AzureFunctionsQuickstart-rg --os-type Linux --consumption-plan-location westeurope --runtime python --runtime-version 3.7 --functions-version 2 --name <APP_NAME> --storage-account <STORAGE_NAME>
```
::: zone-end  

::: zone pivot="programming-language-javascript,programming-language-typescript"  
Als u node. js 8 gebruikt, wijzigt `--runtime-version` u ook in `8`.


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
Met deze opdracht maakt u een functie-app die wordt uitgevoerd in uw opgegeven taal runtime onder het [verbruiks abonnement van Azure functions](functions-scale.md#consumption-plan). Dit is gratis voor de hoeveelheid gebruik die u hier maakt. Met deze opdracht wordt ook een gekoppeld Azure-toepassing Insights-exemplaar in dezelfde resource groep ingericht, waarmee u uw functie-app kunt bewaken en logboeken weer geven. Zie [Azure functions bewaken](functions-monitoring.md)voor meer informatie. Er worden geen kosten in rekening gebracht totdat u deze activeert.
    
## <a name="deploy-the-function-project-to-azure"></a>Het functie project implementeren in azure
::: zone-end  

::: zone pivot="programming-language-typescript"  
Voordat u de basis Hulpprogramma's gebruikt om uw project te implementeren in azure, maakt u een productie-gereed build van Java script-bestanden van de type script-bron bestanden.

Met de volgende opdracht wordt uw type script-project voor bereid voor implementatie:

```
npm run build:production 
```
::: zone-end  

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell,programming-language-csharp"  
Met de benodigde resources kunt u nu uw lokale functies project implementeren in de functie-app in azure met behulp van de opdracht [func Azure functionapp Publish](functions-run-local.md#project-file-deployment) . Vervang `<APP_NAME>` in het volgende voor beeld door de naam van uw app.

```
func azure functionapp publish <APP_NAME>
```

Als u de fout melding ' kan de app niet vinden met de naam... ' ziet, wacht u een paar seconden en probeert u het opnieuw. de app is mogelijk niet volledig geïnitialiseerd `az functionapp create` door Azure na de vorige opdracht.

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

::: zone-end  
::: zone pivot="programming-language-java"  
## <a name="deploy-the-function-project-to-azure"></a>Het functie project implementeren in azure

Een functie-app en gerelateerde resources worden in azure gemaakt wanneer u uw functions-project voor het eerst implementeert. De instellingen voor de Azure-resources die zijn gemaakt voor het hosten van uw app, worden gedefinieerd in het [bestand pom. XML](#pomxml). In dit artikel gaat u akkoord met de standaard instellingen.

> [!TIP]
> Als u een functie-app wilt maken die wordt uitgevoerd op Linux in `runtime.os` plaats van Windows, wijzigt u het element `windows` in `linux`het bestand pom. XML van in. Het uitvoeren van Linux in een verbruiks abonnement wordt in [deze regio's](https://github.com/Azure/azure-functions-host/wiki/Linux-Consumption-Regions)ondersteund. U kunt geen apps gebruiken die worden uitgevoerd op Linux en apps die worden uitgevoerd in Windows in dezelfde resource groep.

Voordat u kunt implementeren, gebruikt u de opdracht [AZ login](/cli/azure/authenticate-azure-cli) Azure CLI om u aan te melden bij uw Azure-abonnement. 

```azurecli
az login
```

Gebruik de volgende opdracht om uw project te implementeren in een nieuwe functie-app. 

```
mvn azure-functions:deploy
```

Hiermee maakt u de volgende resources in Azure:

+ Resource groep. Een naam als _Java-functions-groep_.
+ Opslag account. Vereist door-functies. De naam wordt wille keurig gegenereerd op basis van de vereisten van het opslag account.
+ Hosting plan. Serverloze hosting voor uw functie-app in de regio _westus_ . De naam is _Java-functions-app-service-plan_.
+ Functie-app. Een functie-app is de implementatie-en uitvoerings eenheid voor uw functies. De naam wordt wille keurig gegenereerd op basis van uw _artifactId_, toegevoegd met een wille keurig gegenereerd nummer. 

De implementatie verpakt de project bestanden en implementeert deze in de nieuwe functie-app met behulp van [zip-implementatie](functions-deployment-technologies.md#zip-deploy). De code wordt uitgevoerd vanuit het implementatie pakket in Azure.
::: zone-end

## <a name="invoke-the-function-on-azure"></a>De functie aanroepen op Azure

Omdat uw functie gebruikmaakt van een HTTP-trigger, roept u deze aan door een HTTP-aanvraag naar de URL in de browser of met een hulp programma zoals krul te maken. In beide gevallen is de `code` URL-para meter uw unieke [functie sleutel](functions-bindings-http-webhook-trigger.md#authorization-keys) die de aanroep van uw functie-eind punt toestaat.

# <a name="browser"></a>[Browser](#tab/browser)

Kopieer de volledige **invoke-URL** die wordt weer gegeven in de uitvoer van de opdracht publiceren naar een adres balk van de browser `&name=Functions`en voeg de query parameter toe. De browser moet vergelijk bare uitvoer weer geven als u de functie lokaal hebt uitgevoerd.

![De uitvoer van de functie die wordt uitgevoerd op Azure in een browser](./media/functions-create-first-azure-function-azure-cli/function-test-cloud-browser.png)


# <a name="curl"></a>[Ezelsoor](#tab/curl)

Voer [`curl`](https://curl.haxx.se/) uit met de **aanroepen-URL**en voeg `&name=Functions`de para meter toe. De uitvoer van de opdracht moet de tekst ' Hello functions ' zijn.

![De uitvoer van de functie die wordt uitgevoerd op Azure met behulp van krul](./media/functions-create-first-azure-function-azure-cli/function-test-cloud-curl.png)

---

> [!TIP]
> Gebruik de [Application Insights Live Metrics stream](functions-monitoring.md#streaming-logs)om bijna realtime logboeken voor een gepubliceerde functie-app weer te geven.

## <a name="clean-up-resources"></a>Resources opschonen

Als u verdergaat met de volgende stap, [voegt u een Azure Storage wachtrij-uitvoer binding toe](functions-add-output-binding-storage-queue-cli.md). u kunt al uw resources op dezelfde plaats zetten als u op de hoogte bent van wat u al hebt gedaan.

Gebruik anders de volgende opdracht om de resource groep en alle bijbehorende resources te verwijderen om te voor komen dat er verdere kosten in rekening worden gebracht.

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

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Verbinding maken met een Azure Storage wachtrij](functions-add-output-binding-storage-queue-cli.md)
