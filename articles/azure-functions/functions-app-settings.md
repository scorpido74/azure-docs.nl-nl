---
title: Naslaginformatie over app-instellingen voor Azure Functions
description: Referentie documentatie voor de Azure Functions app-instellingen of omgevings variabelen.
ms.topic: conceptual
ms.date: 09/22/2018
ms.openlocfilehash: 6f42c411263575040d4392b85542920e8f2463d4
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/01/2020
ms.locfileid: "82690762"
---
# <a name="app-settings-reference-for-azure-functions"></a>Naslaginformatie over app-instellingen voor Azure Functions

App-instellingen in een functie-app bevatten globale configuratie opties die van invloed zijn op alle functies voor die functie-app. Wanneer u lokaal uitvoert, worden deze instellingen geopend als lokale [omgevings variabelen](functions-run-local.md#local-settings-file). In dit artikel vindt u een overzicht van de app-instellingen die beschikbaar zijn in functie-apps.

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

Er zijn andere globale configuratie opties in het bestand [host. json](functions-host-json.md) en in het bestand [Local. settings. json](functions-run-local.md#local-settings-file) .

## <a name="appinsights_instrumentationkey"></a>APPINSIGHTS_INSTRUMENTATIONKEY

De instrumentatie sleutel voor Application Insights. Gebruik alleen een van `APPINSIGHTS_INSTRUMENTATIONKEY` of `APPLICATIONINSIGHTS_CONNECTIONSTRING`. Zie [Azure functions bewaken](functions-monitoring.md)voor meer informatie. 

|Sleutel|Voorbeeldwaarde|
|---|------------|
|APPINSIGHTS_INSTRUMENTATIONKEY|55555555-af77-484b-9032-64f83bb83bb|

## <a name="applicationinsights_connectionstring"></a>APPLICATIONINSIGHTS_CONNECTIONSTRING

De connection string voor Application Insights. Gebruik `APPLICATIONINSIGHTS_CONNECTIONSTRING` in plaats `APPINSIGHTS_INSTRUMENTATIONKEY` van wanneer uw functie-app de toegevoegde aanpassingen moet worden ondersteund met behulp van de Connection String. Zie [verbindings reeksen](../azure-monitor/app/sdk-connection-string.md)voor meer informatie. 

|Sleutel|Voorbeeldwaarde|
|---|------------|
|APPLICATIONINSIGHTS_CONNECTIONSTRING|InstrumentationKey = [sleutel]; IngestionEndpoint = [URL]; LiveEndpoint = [URL]; ProfilerEndpoint = [URL]; SnapshotEndpoint = [URL];|

## <a name="azure_functions_environment"></a>AZURE_FUNCTIONS_ENVIRONMENT

In versie 2. x en latere versies van de functions runtime configureert het app-gedrag op basis van de runtime-omgeving. Deze waarde wordt [tijdens de initialisatie gelezen](https://github.com/Azure/azure-functions-host/blob/dev/src/WebJobs.Script.WebHost/Program.cs#L43). U kunt elke `AZURE_FUNCTIONS_ENVIRONMENT` waarde instellen, maar [drie waarden](/dotnet/api/microsoft.aspnetcore.hosting.environmentname) worden ondersteund: [ontwikkeling](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.development), [fase ring](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.staging)en [productie](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.production). Als `AZURE_FUNCTIONS_ENVIRONMENT` niet is ingesteld, wordt standaard `Development` de waarde in een lokale `Production` omgeving en op Azure gebruikt. Deze instelling moet worden gebruikt in plaats `ASPNETCORE_ENVIRONMENT` van om de runtime-omgeving in te stellen. 

## <a name="azurefunctionsjobhost__"></a>AzureFunctionsJobHost__\*

In versie 2. x en latere versies van de functions runtime kunnen de instellingen van de [host. json](functions-host-json.md) in de huidige omgeving worden overschreven. Deze onderdrukkingen worden weer gegeven als toepassings instellingen met `AzureFunctionsJobHost__path__to__setting`de naam. Zie [host. json-waarden onderdrukken](functions-host-json.md#override-hostjson-values)voor meer informatie.

## <a name="azurewebjobsdashboard"></a>AzureWebJobsDashboard

Optioneel opslag account connection string voor het opslaan van Logboeken en weer geven hiervan op het tabblad **monitor** in de portal. Deze instelling is alleen geldig voor apps met als doel versie 1. x van de Azure Functions runtime. Het opslag account moet een algemeen doel zijn dat blobs, wacht rijen en tabellen ondersteunt. Zie [vereisten voor opslag accounts](storage-considerations.md#storage-account-requirements)voor meer informatie.

|Sleutel|Voorbeeldwaarde|
|---|------------|
|AzureWebJobsDashboard|DefaultEndpointsProtocol = https; AccountName =<name>; AccountKey =<key>|

> [!NOTE]
> Voor betere prestaties en ervaring gebruiken runtime versie 2. x en latere versies APPINSIGHTS_INSTRUMENTATIONKEY en app Insights voor bewaking in plaats van `AzureWebJobsDashboard`.

## <a name="azurewebjobsdisablehomepage"></a>AzureWebJobsDisableHomepage

`true`betekent dat de standaard landings pagina wordt uitgeschakeld die wordt weer gegeven voor de basis-URL van een functie-app. De standaardwaarde is `false`.

|Sleutel|Voorbeeldwaarde|
|---|------------|
|AzureWebJobsDisableHomepage|waar|

Als deze app-instelling wordt wegge laten of is `false`ingesteld op, wordt een pagina die lijkt op het volgende voor beeld weer gegeven `<functionappname>.azurewebsites.net`als reactie op de URL.

![Landings pagina voor functie-app](media/functions-app-settings/function-app-landing-page.png)

## <a name="azurewebjobsdotnetreleasecompilation"></a>AzureWebJobsDotNetReleaseCompilation

`true`betekent release modus gebruiken bij het compileren van .NET-code. `false` betekent de foutopsporingsmodus gebruiken. De standaardwaarde is `true`.

|Sleutel|Voorbeeldwaarde|
|---|------------|
|AzureWebJobsDotNetReleaseCompilation|waar|

## <a name="azurewebjobsfeatureflags"></a>AzureWebJobsFeatureFlags

Een door komma's gescheiden lijst met bèta functies die u kunt inschakelen. Bèta functies die door deze vlaggen worden ingeschakeld, zijn niet gereed voor productie, maar kunnen worden ingeschakeld voor experimenteel gebruik voordat ze live gaan.

|Sleutel|Voorbeeldwaarde|
|---|------------|
|AzureWebJobsFeatureFlags|feature1,feature2|

## <a name="azurewebjobssecretstoragetype"></a>AzureWebJobsSecretStorageType

Hiermee geeft u de opslag plaats of provider op die moet worden gebruikt voor sleutel opslag. Momenteel zijn de ondersteunde opslag plaatsen Blob Storage (' BLOB ') en het lokale bestands systeem (' bestanden '). De standaard waarde is Blob in versie 2 en bestands systeem in versie 1.

|Sleutel|Voorbeeldwaarde|
|---|------------|
|AzureWebJobsSecretStorageType|Bestanden|

## <a name="azurewebjobsstorage"></a>AzureWebJobsStorage

De Azure Functions runtime gebruikt dit opslag account connection string voor alle functies, met uitzonde ring van HTTP-geactiveerde functies. Het opslag account moet een algemeen doel zijn dat blobs, wacht rijen en tabellen ondersteunt. Zie vereisten voor [opslag accounts](functions-infrastructure-as-code.md#storage-account) en [opslag accounts](storage-considerations.md#storage-account-requirements).

|Sleutel|Voorbeeldwaarde|
|---|------------|
|AzureWebJobsStorage|DefaultEndpointsProtocol = https; AccountName = [name]; AccountKey = [sleutel]|

## <a name="azurewebjobs_typescriptpath"></a>AzureWebJobs_TypeScriptPath

Het pad naar het Compileer programma dat wordt gebruikt voor type script. Hiermee kunt u de standaard instelling onderdrukken als dat nodig is.

|Sleutel|Voorbeeldwaarde|
|---|------------|
|AzureWebJobs_TypeScriptPath|%HOME%\typescript|

## <a name="function_app_edit_mode"></a>\_bewerkings\_modus van functie-app\_

Hiermee wordt bepaald of bewerken in de Azure Portal is ingeschakeld. Geldige waarden zijn ' readwrite ' en ' readonly '.

|Sleutel|Voorbeeldwaarde|
|---|------------|
|\_bewerkings\_modus van functie-app\_|alleen-lezen|

## <a name="functions_extension_version"></a>extensie\_\_versie van functies

De versie van de functions-runtime die moet worden gebruikt in deze functie-app. Een tilde met een primaire versie houdt in dat u de nieuwste versie van die primaire versie gebruikt (bijvoorbeeld "~ 2"). Wanneer er nieuwe versies voor dezelfde primaire versie beschikbaar zijn, worden deze automatisch geïnstalleerd in de functie-app. Als u de app wilt vastmaken aan een specifieke versie, gebruikt u het volledige versie nummer (bijvoorbeeld ' 2.0.12345 '). De standaard waarde is "~ 2". Een waarde voor `~1` het vastpinnen van uw app naar versie 1. x van de runtime.

|Sleutel|Voorbeeldwaarde|
|---|------------|
|extensie\_\_versie van functies|~ 2|

## <a name="functions_v2_compatibility_mode"></a>Function\_v2\_-\_compatibiliteits modus

Met deze instelling kan de functie-app worden uitgevoerd in een modus die compatibel is met versie 2. x in de runtime van versie 3. x. Gebruik deze instelling alleen als er problemen optreden bij [het upgraden van de functie-app van versie 2. x naar 3. x van de runtime](functions-versions.md#migrating-from-2x-to-3x). 

>[!IMPORTANT]
> Deze instelling is alleen bedoeld als tijdelijke oplossing wanneer u uw app bijwerkt zodat deze correct wordt uitgevoerd op versie 3. x. Deze instelling wordt ondersteund zolang de [runtime 2. x wordt ondersteund](functions-versions.md). Als u problemen ondervindt waardoor uw app niet kan worden uitgevoerd op versie 3. x zonder deze instelling te gebruiken, meldt u [uw probleem](https://github.com/Azure/azure-functions-host/issues/new?template=Bug_report.md).

Hiervoor moet [de\_versie\_](functions-app-settings.md#functions_extension_version) van de functie- `~3`extensie worden ingesteld op.

|Sleutel|Voorbeeldwaarde|
|---|------------|
|Function\_v2\_-\_compatibiliteits modus|waar|

## <a name="functions_worker_process_count"></a>aantal\_functies\_werk\_proces

Hiermee geeft u het maximum aantal taal werk processen op, met een standaard `1`waarde van. De toegestane maximum waarde is `10`. Functie aanroepen worden gelijkmatig verdeeld over werk processen van de taal. Werk processen in de taal worden elke tien seconden uitgevoerd\_\_\_tot het aantal dat is ingesteld door functions van het aantal werk processen is bereikt. Het gebruik van werk processen in meerdere talen is niet hetzelfde als het [schalen](functions-scale.md). U kunt deze instelling gebruiken als uw werk belasting een combi natie van aan CPU gebonden en I/O-gebonden aanroepen heeft. Deze instelling is van toepassing op alle non-.NET talen.

|Sleutel|Voorbeeldwaarde|
|---|------------|
|aantal\_functies\_werk\_proces|2|


## <a name="functions_worker_runtime"></a>\_functions\_runtime van worker

De Language worker-runtime die in de functie-app moet worden geladen.  Dit komt overeen met de taal die wordt gebruikt in uw toepassing (bijvoorbeeld ' DotNet '). Voor functies in meerdere talen moet u deze publiceren naar meerdere apps, elk met een bijbehorende runtime-waarde voor werk nemers.  Geldige waarden zijn `dotnet` (C#/f #), `node` (Java script/type script) `java` , (Java) `powershell` , (Power shell) `python` en (python).

|Sleutel|Voorbeeldwaarde|
|---|------------|
|\_functions\_runtime van worker|dotnet|

## <a name="website_contentazurefileconnectionstring"></a>WEBSITE_CONTENTAZUREFILECONNECTIONSTRING

Alleen voor verbruik & Premium-abonnementen. De verbindings reeks voor het opslag account waarin de code en configuratie van de functie-app worden opgeslagen. Zie [een functie-app maken](functions-infrastructure-as-code.md#create-a-function-app).

|Sleutel|Voorbeeldwaarde|
|---|------------|
|WEBSITE_CONTENTAZUREFILECONNECTIONSTRING|DefaultEndpointsProtocol = https; AccountName = [name]; AccountKey = [sleutel]|

## <a name="website_contentshare"></a>WEBSITE\_-CONTENTSHARE

Alleen voor verbruik & Premium-abonnementen. Het bestandspad naar de code en configuratie van de functie-app. Gebruikt met WEBSITE_CONTENTAZUREFILECONNECTIONSTRING. De standaard waarde is een unieke teken reeks die begint met de naam van de functie-app. Zie [een functie-app maken](functions-infrastructure-as-code.md#create-a-function-app).

|Sleutel|Voorbeeldwaarde|
|---|------------|
|WEBSITE_CONTENTSHARE|functionapp091999e2|

## <a name="website_max_dynamic_application_scale_out"></a>WEBSITE\_maximum\_aantal\_\_uitschalen\_van dynamische toepassing

Het maximum aantal exemplaren waarmee de functie-app kan worden uitgeschaald. De standaard waarde is geen limiet.

> [!NOTE]
> Deze instelling is een preview-functie, en alleen betrouwbaar als deze is ingesteld op een waarde <= 5

|Sleutel|Voorbeeldwaarde|
|---|------------|
|WEBSITE\_maximum\_aantal\_\_uitschalen\_van dynamische toepassing|5|

## <a name="website_node_default_version"></a>DEFAULT_VERSION\_van\_website knooppunt

_Alleen Windows._  
Hiermee stelt u de versie van node. js in die moet worden gebruikt voor het uitvoeren van uw functie-app in Windows. U moet een tilde (~) gebruiken om de runtime de meest recente beschik bare versie van de doel primaire versie te laten gebruiken. Wanneer deze bijvoorbeeld is ingesteld op `~10`, wordt de meest recente versie van node. js 10 gebruikt. Wanneer een primaire versie is gericht op een tilde, hoeft u de secundaire versie niet hand matig bij te werken. 

|Sleutel|Voorbeeldwaarde|
|---|------------|
|DEFAULT_VERSION\_van\_website knooppunt|~ 10|

## <a name="website_run_from_package"></a>WEBSITE\_uitvoeren\_vanuit\_pakket

Hiermee kan uw functie-app vanuit een gekoppeld pakket bestand worden uitgevoerd.

|Sleutel|Voorbeeldwaarde|
|---|------------|
|WEBSITE\_uitvoeren\_vanuit\_pakket|1|

Geldige waarden zijn ofwel een URL die wordt omgezet in de locatie van een implementatie pakket bestand of `1`. Als deze is `1`ingesteld op, moet het pakket zich `d:\home\data\SitePackages` in de map bevinden. Bij gebruik van een zip-implementatie met deze instelling wordt het pakket automatisch naar deze locatie geüpload. In de preview-versie heet `WEBSITE_RUN_FROM_ZIP`deze instelling. Zie [uw functies uitvoeren vanuit een pakket bestand](run-functions-from-deployment-package.md)voor meer informatie.

## <a name="azure_function_proxy_disable_local_call"></a>AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL

Standaard functions maken gebruik van een snelkoppeling voor het verzenden van API-aanroepen vanuit proxy's rechtstreeks naar functies in dezelfde functie-app, in plaats van een nieuwe HTTP-aanvraag. Met deze instelling kunt u dit gedrag uitschakelen.

|Sleutel|Waarde|Beschrijving|
|-|-|-|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|waar|Aanroepen met een back-end-URL die verwijst naar een functie in de lokale functie-app worden niet meer rechtstreeks naar de functie verzonden en worden in plaats daarvan teruggestuurd naar de HTTP-front-end voor de functie-app|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|onjuist|Dit is de standaardwaarde. Aanroepen met een back-end-URL die verwijst naar een functie in de lokale functie-app worden rechtstreeks naar die functie doorgestuurd|


## <a name="azure_function_proxy_backend_url_decode_slashes"></a>AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES

Met deze instelling bepaalt u of% 2F wordt gedecodeerd als slash-tekens in route parameters wanneer ze worden ingevoegd in de back-end-URL. 

|Sleutel|Waarde|Beschrijving|
|-|-|-|
|AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES|waar|Voor route parameters met gecodeerde slashes worden ze gedecodeerd. `example.com/api%2ftest`wordt`example.com/api/test`|
|AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES|onjuist|Dit is de standaardinstelling. Alle route parameters worden ongewijzigd door gegeven|

### <a name="example"></a>Voorbeeld

Hier volgt een voor beeld van proxy's. json in een functie-app op de URL myfunction.com

```JSON
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "root": {
            "matchCondition": {
                "route": "/{*all}"
            },
            "backendUri": "example.com/{all}"
        }
    }
}
```
|URL-decodering|Invoer|Uitvoer|
|-|-|-|
|waar|myfunction.com/test%2fapi|example.com/test/api
|onjuist|myfunction.com/test%2fapi|example.com/test%2fapi|


## <a name="next-steps"></a>Volgende stappen

[Meer informatie over het bijwerken van de app-instellingen](functions-how-to-use-azure-function-app-settings.md#settings)

[Zie algemene instellingen in het bestand host. json](functions-host-json.md)

[Andere app-instellingen voor App Service apps bekijken](https://github.com/projectkudu/kudu/wiki/Configurable-settings)
