---
title: Naslaginformatie over app-instellingen voor Azure Functions
description: Referentiedocumentatie voor de instellingen van de Azure Functions-app of omgevingsvariabelen.
ms.topic: conceptual
ms.date: 09/22/2018
ms.openlocfilehash: e2d168d8828d17e13f875e3b2555c7db0d4ba32d
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656788"
---
# <a name="app-settings-reference-for-azure-functions"></a>Naslaginformatie over app-instellingen voor Azure Functions

App-instellingen in een functie-app bevatten algemene configuratieopties die van invloed zijn op alle functies voor die functie-app. Wanneer u lokaal wordt uitgevoerd, worden deze instellingen geopend als variabelen voor lokale [omgeving.](functions-run-local.md#local-settings-file) In dit artikel worden de app-instellingen weergegeven die beschikbaar zijn in functie-apps.

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

Er zijn andere algemene configuratieopties in het [bestand host.json](functions-host-json.md) en in het bestand [local.settings.json.](functions-run-local.md#local-settings-file)

## <a name="appinsights_instrumentationkey"></a>APPINSIGHTS_INSTRUMENTATIONKEY

De instrumentatiesleutel voor Application Insights. Gebruik er `APPINSIGHTS_INSTRUMENTATIONKEY` maar `APPLICATIONINSIGHTS_CONNECTIONSTRING`één van of . Zie [Azure-functies controleren](functions-monitoring.md)voor meer informatie . 

|Sleutel|Voorbeeldwaarde|
|---|------------|
|APPINSIGHTS_INSTRUMENTATIONKEY|55555555-af77-484b-9032-64f83bb83bb|

## <a name="applicationinsights_connectionstring"></a>APPLICATIONINSIGHTS_CONNECTIONSTRING

De verbindingstekenreeks voor Application Insights. Gebruik `APPLICATIONINSIGHTS_CONNECTIONSTRING` in `APPINSIGHTS_INSTRUMENTATIONKEY` plaats van wanneer de functie-app de toegevoegde aanpassingen vereist die worden ondersteund met behulp van de verbindingstekenreeks. Zie [Verbindingstekenreeksen](../azure-monitor/app/sdk-connection-string.md)voor meer informatie . 

|Sleutel|Voorbeeldwaarde|
|---|------------|
|APPLICATIONINSIGHTS_CONNECTIONSTRING|InstrumentationKey=[toets];IngestionEndpoint=[url]; LiveEndpoint=[url]; ProfilerEndpoint=[url]; SnapshotEndpoint=[url];|

## <a name="azure_functions_environment"></a>AZURE_FUNCTIONS_ENVIRONMENT

Configureert in versie 2.x en latere versies van de runtime Functies het gedrag van apps op basis van de runtime-omgeving. Deze waarde wordt [gelezen tijdens de initialisatie](https://github.com/Azure/azure-functions-host/blob/dev/src/WebJobs.Script.WebHost/Program.cs#L43). U kunt `AZURE_FUNCTIONS_ENVIRONMENT` instellen op elke waarde, maar [drie waarden](/dotnet/api/microsoft.aspnetcore.hosting.environmentname) worden ondersteund: [Ontwikkeling,](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.development) [Fasering](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.staging)en [Productie](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.production). Wanneer `AZURE_FUNCTIONS_ENVIRONMENT` is niet ingesteld, wordt `Development` dit standaard `Production` ingesteld op een lokale omgeving en op Azure. Deze instelling moet worden `ASPNETCORE_ENVIRONMENT` gebruikt in plaats van de runtime-omgeving in te stellen. 

## <a name="azurewebjobsdashboard"></a>AzureWebJobsDashboard

Optionele tekenreeks voor opslagaccountverbindingen voor het opslaan van logboeken en het weergeven ervan op het tabblad **Monitor** in de portal. Deze instelling is alleen geldig voor apps die zich richten op versie 1.x van de runtime van Azure-functies. Het opslagaccount moet een algemeen doel zijn dat blobs, wachtrijen en tabellen ondersteunt. Zie [Vereisten voor opslagaccount](storage-considerations.md#storage-account-requirements)voor meer informatie.

|Sleutel|Voorbeeldwaarde|
|---|------------|
|AzureWebJobsDashboard|DefaultEndpointsProtocol=https; AccountName=<name>; AccountSleutel=<key>|

> [!NOTE]
> Voor betere prestaties en ervaring gebruiken runtime versie 2.x en latere `AzureWebJobsDashboard`versies APPINSIGHTS_INSTRUMENTATIONKEY en App Insights voor monitoring in plaats van .

## <a name="azurewebjobsdisablehomepage"></a>AzureWebJobsDisableHomepage

`true`betekent de standaardbestemmingspagina uitschakelen die wordt weergegeven voor de hoofd-URL van een functie-app. De standaardwaarde is `false`.

|Sleutel|Voorbeeldwaarde|
|---|------------|
|AzureWebJobsDisableHomepage|waar|

Wanneer deze app-instelling wordt `false`weggelaten of ingesteld op , wordt een pagina `<functionappname>.azurewebsites.net`weergegeven die vergelijkbaar is met het volgende voorbeeld als reactie op de URL .

![Bestemmingspagina functie-app](media/functions-app-settings/function-app-landing-page.png)

## <a name="azurewebjobsdotnetreleasecompilation"></a>AzureWebJobsDotNetReleaseCompilatie

`true`gebruik smaken van de releasemodus bij het samenstellen van .NET-code; `false` betekent de foutopsporingsmodus gebruiken. De standaardwaarde is `true`.

|Sleutel|Voorbeeldwaarde|
|---|------------|
|AzureWebJobsDotNetReleaseCompilatie|waar|

## <a name="azurewebjobsfeatureflags"></a>AzureWebJobsFeatureFlags

Een door komma's afgebakende lijst met bètafuncties die u wilt inschakelen. Bètafuncties die door deze vlaggen zijn ingeschakeld, zijn niet productieklaar, maar kunnen worden ingeschakeld voor experimenteel gebruik voordat ze live gaan.

|Sleutel|Voorbeeldwaarde|
|---|------------|
|AzureWebJobsFeatureFlags|functie1,feature2|

## <a name="azurewebjobssecretstoragetype"></a>AzureWebJobsSecretStorageType

Hiermee geeft u de opslagplaats of provider op die moet worden gebruikt voor sleutelopslag. Momenteel zijn de ondersteunde opslagplaatsen blobopslag ('Blob') en het lokale bestandssysteem ('Bestanden'). De standaardinstelling is blob in versie 2 en bestandssysteem in versie 1.

|Sleutel|Voorbeeldwaarde|
|---|------------|
|AzureWebJobsSecretStorageType|Bestanden|

## <a name="azurewebjobsstorage"></a>AzureWebJobsStorage

De runtime azure-functies gebruikt deze tekenreeks voor opslagaccountverbindingen voor alle functies, behalve voor http-geactiveerde functies. Het opslagaccount moet een algemeen doel zijn dat blobs, wachtrijen en tabellen ondersteunt. Zie [Vereisten voor opslagaccount](functions-infrastructure-as-code.md#storage-account) en [opslagaccount](storage-considerations.md#storage-account-requirements).

|Sleutel|Voorbeeldwaarde|
|---|------------|
|AzureWebJobsStorage|DefaultEndpointsProtocol=https; AccountName=[naam]; AccountKey=[sleutel]|

## <a name="azurewebjobs_typescriptpath"></a>AzureWebJobs_TypeScriptPath

Pad naar de compiler die wordt gebruikt voor TypeScript. Hiermee u de standaardwaarde overschrijven als dat nodig is.

|Sleutel|Voorbeeldwaarde|
|---|------------|
|AzureWebJobs_TypeScriptPath|%HOME%\typescript|

## <a name="function_app_edit_mode"></a>FUNCTIE-APP-BEWERKINGSMODUS\_\_\_

Bepaalt of bewerken in de Azure-portal is ingeschakeld. Geldige waarden zijn "readwrite" en "readonly".

|Sleutel|Voorbeeldwaarde|
|---|------------|
|FUNCTIE-APP-BEWERKINGSMODUS\_\_\_|alleen-lezen|

## <a name="functions_extension_version"></a>VERSIE\_\_VAN FUNCTIESEXTENSIE

De versie van de runtime functies die in deze functie-app moet worden gebruikt. Een tilde met grote versie betekent gebruik maken van de nieuwste versie van die grote versie (bijvoorbeeld ,,~2'). Wanneer er nieuwe versies voor dezelfde hoofdversie beschikbaar zijn, worden ze automatisch geïnstalleerd in de functie-app. Als u de app wilt vastmaken aan een specifieke versie, gebruikt u het volledige versienummer (bijvoorbeeld '2.0.12345'). Standaard is "~2". Een waarde `~1` van pins van uw app aan versie 1.x van de runtime.

|Sleutel|Voorbeeldwaarde|
|---|------------|
|VERSIE\_\_VAN FUNCTIESEXTENSIE|~2.|

## <a name="functions_v2_compatibility_mode"></a>FUNCTIES\_V2-COMPATIBILITEITSMODUS\_\_

Met deze instelling kan uw functie-app worden uitgevoerd in een versie 2.x-compatibele modus op de runtime van versie 3.x. Gebruik deze instelling alleen als u problemen ondervindt bij het [upgraden van uw functie-app van versie 2.x naar 3.x van de runtime](functions-versions.md#migrating-from-2x-to-3x). 

>[!IMPORTANT]
> Deze instelling is alleen bedoeld als tijdelijke oplossing op korte termijn terwijl u uw app bijwerkt om correct te worden uitgevoerd op versie 3.x. Deze instelling wordt ondersteund zolang de [2.x runtime wordt ondersteund.](functions-versions.md) Als u problemen ondervindt waardoor uw app niet kan worden uitgevoerd op versie 3.x zonder deze instelling te gebruiken, dient u [uw probleem te melden.](https://github.com/Azure/azure-functions-host/issues/new?template=Bug_report.md)

Vereist dat [functies\_extensie\_versie](functions-app-settings.md#functions_extension_version) worden ingesteld op `~3`.

|Sleutel|Voorbeeldwaarde|
|---|------------|
|FUNCTIES\_V2-COMPATIBILITEITSMODUS\_\_|waar|

## <a name="functions_worker_process_count"></a>AANTAL\_\_FUNCTIES\_WERKPROCES

Hiermee geeft u het maximumaantal taalwerkprocessen `1`op, met een standaardwaarde van . De maximaal toegestane `10`waarde is . Functie-aanroepingen worden gelijkmatig verdeeld over taalwerkprocessen. Taalwerkprocessen worden elke 10 seconden voortgebracht totdat\_\_het\_aantal functies werknemerprocestelling is bereikt. Het gebruik van werkprocessen met meerdere talen is niet hetzelfde als [schalen.](functions-scale.md) Overweeg deze instelling te gebruiken wanneer uw werkbelasting een mix van CPU-gebonden en I/O-gebonden aanroepen heeft. Deze instelling is van toepassing op alle non-.NET talen.

|Sleutel|Voorbeeldwaarde|
|---|------------|
|AANTAL\_\_FUNCTIES\_WERKPROCES|2|


## <a name="functions_worker_runtime"></a>RUNTIME\_VAN FUNCTIESWERKER\_

De runtime van de taalwerker om te laden in de functie-app.  Dit komt overeen met de taal die in uw toepassing wordt gebruikt (bijvoorbeeld 'dotnet'). Voor functies in meerdere talen moet u deze publiceren naar meerdere apps, elk met een bijbehorende runtime-waarde voor werknemers.  Geldige waarden `dotnet` `node` zijn (C#/F#), `java` (JavaScript/TypeScript), (Java), `python` `powershell` (PowerShell) en (Python).

|Sleutel|Voorbeeldwaarde|
|---|------------|
|RUNTIME\_VAN FUNCTIESWERKER\_|dotnet|

## <a name="website_contentazurefileconnectionstring"></a>WEBSITE_CONTENTAZUREFILECONNECTIONSTRING

Alleen voor verbruik & Premium-abonnementen. Verbindingstekenreeks voor opslagaccount waar de functie-app-code en -configuratie worden opgeslagen. Zie [Een functie-app maken](functions-infrastructure-as-code.md#create-a-function-app).

|Sleutel|Voorbeeldwaarde|
|---|------------|
|WEBSITE_CONTENTAZUREFILECONNECTIONSTRING|DefaultEndpointsProtocol=https; AccountName=[naam]; AccountKey=[sleutel]|

## <a name="website_contentshare"></a>WEBSITE\_CONTENTSHARE

Alleen voor verbruik & Premium-abonnementen. Het bestandspad naar de functie-app-code en -configuratie. Gebruikt bij WEBSITE_CONTENTAZUREFILECONNECTIONSTRING. Standaard is een unieke tekenreeks die begint met de naam van de functie-app. Zie [Een functie-app maken](functions-infrastructure-as-code.md#create-a-function-app).

|Sleutel|Voorbeeldwaarde|
|---|------------|
|WEBSITE_CONTENTSHARE|functieapp091999e2|

## <a name="website_max_dynamic_application_scale_out"></a>WEBSITE\_MAX\_DYNAMIC\_APPLICATION\_SCALE\_OUT

Het maximum aantal exemplaren waarop de functie-app kan worden uitgeschaald. Standaard is geen limiet.

> [!NOTE]
> Deze instelling is een voorbeeldfunctie - en alleen betrouwbaar als deze is ingesteld op een waarde <= 5

|Sleutel|Voorbeeldwaarde|
|---|------------|
|WEBSITE\_MAX\_DYNAMIC\_APPLICATION\_SCALE\_OUT|5|

## <a name="website_node_default_version"></a>WEBSITE\_NODE\_DEFAULT_VERSION

_Alleen Windows._  
Hiermee stelt u de versie van Node.js in die u wilt gebruiken bij het uitvoeren van uw functie-app op Windows. U moet gebruik maken van een tilde (~) om de runtime gebruik maken van de nieuwste beschikbare versie van de beoogde grote versie. Wanneer u bijvoorbeeld `~10`bent ingesteld op , wordt de nieuwste versie van Node.js 10 gebruikt. Wanneer een grote versie is gericht met een tilde, hoeft u de secundaire versie niet handmatig bij te werken. 

|Sleutel|Voorbeeldwaarde|
|---|------------|
|WEBSITE\_NODE\_DEFAULT_VERSION|~10|

## <a name="website_run_from_package"></a>WEBSITE\_RUN\_FROM\_PACKAGE

Hiermee kan uw functie-app worden uitgevoerd vanuit een gemonteerd pakketbestand.

|Sleutel|Voorbeeldwaarde|
|---|------------|
|WEBSITE\_RUN\_FROM\_PACKAGE|1|

Geldige waarden zijn een URL die wordt opgelost naar de `1`locatie van een implementatiepakketbestand, of . Wanneer u `1`bent ingesteld op `d:\home\data\SitePackages` , moet het pakket zich in de map bevinden. Wanneer u zip-implementatie gebruikt met deze instelling, wordt het pakket automatisch naar deze locatie geüpload. In preview kreeg deze `WEBSITE_RUN_FROM_ZIP`instelling de naam . Zie [Uw functies uitvoeren vanuit een pakketbestand](run-functions-from-deployment-package.md)voor meer informatie.

## <a name="azure_function_proxy_disable_local_call"></a>AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL

Standaard gebruiken functieproxy's een snelkoppeling om API-aanroepen van proxy's rechtstreeks naar functies in dezelfde functie-app te verzenden, in plaats van een nieuwe HTTP-aanvraag te maken. Met deze instelling u dat gedrag uitschakelen.

|Sleutel|Waarde|Beschrijving|
|-|-|-|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|waar|Oproepen met een backend-URL die naar een functie in de lokale functie-app wijzen, worden niet langer rechtstreeks naar de functie verzonden en worden in plaats daarvan teruggeleid naar de HTTP-front-end voor de functie-app|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|false|Dit is de standaardwaarde. Oproepen met een backend-URL die naar een functie in de lokale functie-app wijzen, worden rechtstreeks naar die functie doorgestuurd|


## <a name="azure_function_proxy_backend_url_decode_slashes"></a>AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES

Met deze instelling bepaalt u of %2F is gedecodeerd als slashes in routeparameters wanneer deze in de backend-URL worden ingevoegd. 

|Sleutel|Waarde|Beschrijving|
|-|-|-|
|AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES|waar|Routeparameters met gecodeerde slashes laten ze decoderen. `example.com/api%2ftest`zal worden`example.com/api/test`|
|AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES|false|Dit is de standaardinstelling. Alle routeparameters worden ongewijzigd doorgegeven|

### <a name="example"></a>Voorbeeld

Hier is een voorbeeld proxyxies.json in een functie-app op de URL myfunction.com

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
|false|myfunction.com/test%2fapi|example.com/test%2fapi|


## <a name="next-steps"></a>Volgende stappen

[Meer informatie over het bijwerken van app-instellingen](functions-how-to-use-azure-function-app-settings.md#settings)

[Bekijk globale instellingen in het bestand host.json](functions-host-json.md)

[Andere app-instellingen voor App Service-apps bekijken](https://github.com/projectkudu/kudu/wiki/Configurable-settings)
