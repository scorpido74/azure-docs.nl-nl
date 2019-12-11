---
title: Naslaginformatie over App-instellingen voor Azure Functions
description: Referentiedocumentatie voor de Azure Functions-app-instellingen of omgevingsvariabelen.
ms.topic: conceptual
ms.date: 09/22/2018
ms.openlocfilehash: 1c7f5f9f8f6f198c5fe74baa613306732fa9b55b
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74977264"
---
# <a name="app-settings-reference-for-azure-functions"></a>Naslaginformatie over App-instellingen voor Azure Functions

App-instellingen in een functie-app bevatten globale configuratie-opties die invloed hebben op alle functies voor die functie-app. Wanneer u lokaal uitvoert, worden deze instellingen geopend als lokale [omgevings variabelen](functions-run-local.md#local-settings-file). In dit artikel geeft een lijst van de app-instellingen die beschikbaar in de functie-apps zijn.

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

Er zijn andere globale configuratie-opties in de [host.json](functions-host-json.md) bestand en klik in de [local.settings.json](functions-run-local.md#local-settings-file) bestand.

## <a name="appinsights_instrumentationkey"></a>APPINSIGHTS_INSTRUMENTATIONKEY

De Application Insights-instrumentatiesleutel als u Application Insights. Zie [Azure Functions controleren](functions-monitoring.md).

|Sleutel|Voorbeeldwaarde|
|---|------------|
|APPINSIGHTS_INSTRUMENTATIONKEY|5dbdd5e9-af77-484b-9032-64f83bb83bb|

## <a name="azure_functions_environment"></a>AZURE_FUNCTIONS_ENVIRONMENT

In versie 2. x en latere versies van de functions runtime configureert het app-gedrag op basis van de runtime-omgeving. Deze waarde wordt [tijdens de initialisatie gelezen](https://github.com/Azure/azure-functions-host/blob/dev/src/WebJobs.Script.WebHost/Program.cs#L43). U kunt `AZURE_FUNCTIONS_ENVIRONMENT` op elke waarde instellen, maar [drie waarden](/dotnet/api/microsoft.aspnetcore.hosting.environmentname) worden ondersteund: [ontwikkeling](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.development), [fase ring](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.staging)en [productie](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.production). Als `AZURE_FUNCTIONS_ENVIRONMENT` niet is ingesteld, wordt standaard `Development` op een lokale omgeving en `Production` op Azure. Deze instelling moet worden gebruikt in plaats van `ASPNETCORE_ENVIRONMENT` om de runtime-omgeving in te stellen. 

## <a name="azurewebjobsdashboard"></a>AzureWebJobsDashboard

Optionele tekenreeks opslagaccountverbinding voor het opslaan van Logboeken en weergegeven in de **Monitor** tabblad in de portal. Het opslagaccount moet een die ondersteuning biedt voor blobs, wachtrijen en tabellen voor algemeen gebruik. Zie [opslagaccount](functions-infrastructure-as-code.md#storage-account) en [vereisten voor een Opslagaccount](functions-create-function-app-portal.md#storage-account-requirements).

|Sleutel|Voorbeeldwaarde|
|---|------------|
|AzureWebJobsDashboard|DefaultEndpointsProtocol = https; AccountName = [name]; AccountKey = [-toets]|

> [!TIP]
> Voor prestaties en ervaring verdient het APPINSIGHTS_INSTRUMENTATIONKEY en App Insights gebruiken voor bewaking in plaats van AzureWebJobsDashboard

## <a name="azurewebjobsdisablehomepage"></a>AzureWebJobsDisableHomepage

`true` betekent dat uitschakelen de standaard landingspagina die wordt weergegeven voor de basis-URL van een functie-app. De standaardwaarde is `false`.

|Sleutel|Voorbeeldwaarde|
|---|------------|
|AzureWebJobsDisableHomepage|waar|

Wanneer deze app-instelling is weggelaten of ingesteld op `false`, een pagina zoals in het volgende voorbeeld wordt weergegeven in reactie op de URL `<functionappname>.azurewebsites.net`.

![Landingspagina van de functie-app](media/functions-app-settings/function-app-landing-page.png)

## <a name="azurewebjobsdotnetreleasecompilation"></a>AzureWebJobsDotNetReleaseCompilation

`true` betekent releasemodus gebruikt bij het compileren van .NET-code. `false` betekent foutopsporingsmodus gebruiken. De standaardwaarde is `true`.

|Sleutel|Voorbeeldwaarde|
|---|------------|
|AzureWebJobsDotNetReleaseCompilation|waar|

## <a name="azurewebjobsfeatureflags"></a>AzureWebJobsFeatureFlags

Een door komma's gescheiden lijst van bèta-functies om in te schakelen. Bèta-functies ingeschakeld door deze vlaggen zijn niet klaar voor productie, maar kunnen worden ingeschakeld voor experimentele gebruiken voordat ze live gaan.

|Sleutel|Voorbeeldwaarde|
|---|------------|
|AzureWebJobsFeatureFlags|feature1, feature2|

## <a name="azurewebjobssecretstoragetype"></a>AzureWebJobsSecretStorageType

Hiermee geeft u de opslagplaats of de provider moet worden gebruikt voor opslag van sleutels. De ondersteunde opslagplaatsen zijn momenteel blob-opslag ('Blob') en het lokale bestandssysteem ('Files'). De standaardwaarde is een blob in versie 2 en het bestandssysteem in versie 1.

|Sleutel|Voorbeeldwaarde|
|---|------------|
|AzureWebJobsSecretStorageType|Bestanden|

## <a name="azurewebjobsstorage"></a>AzureWebJobsStorage

De Azure Functions-runtime maakt gebruik van deze tekenreeks opslagaccountverbinding voor alle functies, met uitzondering van HTTP-geactiveerde functies. Het opslagaccount moet een die ondersteuning biedt voor blobs, wachtrijen en tabellen voor algemeen gebruik. Zie [opslagaccount](functions-infrastructure-as-code.md#storage-account) en [vereisten voor een Opslagaccount](functions-create-function-app-portal.md#storage-account-requirements).

|Sleutel|Voorbeeldwaarde|
|---|------------|
|AzureWebJobsStorage|DefaultEndpointsProtocol = https; AccountName = [name]; AccountKey = [-toets]|

## <a name="azurewebjobs_typescriptpath"></a>AzureWebJobs_TypeScriptPath

Pad naar de gebruikt voor TypeScript-compiler. Hiermee kunt u de standaardinstelling negeren als u wilt.

|Sleutel|Voorbeeldwaarde|
|---|------------|
|AzureWebJobs_TypeScriptPath|%Home%\typescript|

## <a name="function_app_edit_mode"></a>FUNCTIE\_APP\_BEWERKEN\_MODUS

Hiermee wordt bepaald of bewerken in de Azure Portal is ingeschakeld. Geldige waarden zijn 'readwrite' en 'alleen-lezen'.

|Sleutel|Voorbeeldwaarde|
|---|------------|
|FUNCTIE\_APP\_BEWERKEN\_MODUS|alleen-lezen|

## <a name="functions_extension_version"></a>FUNCTIES\_EXTENSIE\_VERSIE

De versie van de Functions-runtime voor gebruik in deze functie-app. Een tilde met hoofdversie betekent dat de meest recente versie van die primaire versie (bijvoorbeeld ' ~ 2') gebruiken. Wanneer er nieuwe versies van dezelfde primaire versie beschikbaar zijn, worden ze automatisch geïnstalleerd in de functie-app. Als u wilt de app vastmaken aan een specifieke versie, gebruik het volledige versienummer (bijvoorbeeld ' 2.0.12345'). De standaardwaarde is '~ 2'. Een waarde van `~1` kledingwinkelketen van uw app naar versie 1.x van de runtime.

|Sleutel|Voorbeeldwaarde|
|---|------------|
|FUNCTIES\_EXTENSIE\_VERSIE|~ 2|

## <a name="functions_worker_process_count"></a>FUNCTIES\_werk\_proces\_aantal

Hiermee geeft u het maximum aantal taal werk processen op, met een standaard waarde van `1`. De toegestane maximum waarde is `10`. Functie aanroepen worden gelijkmatig verdeeld over werk processen van de taal. Werk processen in de taal worden elke tien seconden uitgevoerd tot het aantal dat is ingesteld door FUNCTIONs\_WORKer\_proces\_aantal is bereikt. Het gebruik van werk processen in meerdere talen is niet hetzelfde als het [schalen](functions-scale.md). U kunt deze instelling gebruiken als uw werk belasting een combi natie van aan CPU gebonden en I/O-gebonden aanroepen heeft. Deze instelling is van toepassing op alle non-.NET talen.

|Sleutel|Voorbeeldwaarde|
|---|------------|
|FUNCTIES\_werk\_proces\_aantal|2|


## <a name="functions_worker_runtime"></a>FUNCTIES\_WORKER\_RUNTIME

De werknemer language runtime worden geladen in de functie-app.  Dit komt overeen met de taal die wordt gebruikt in uw toepassing (bijvoorbeeld ' dotnet'). Voor functies in meerdere talen moet u deze publiceren naar meerdere apps, elk met een overeenkomende waarde van de worker-runtime.  Geldige waarden zijn `dotnet` (C#/F#), `node` (Java script/type script), `java` (Java), `powershell` (Power shell) en `python` (python).

|Sleutel|Voorbeeldwaarde|
|---|------------|
|FUNCTIES\_WORKER\_RUNTIME|DotNet|

## <a name="website_contentazurefileconnectionstring"></a>WEBSITE_CONTENTAZUREFILECONNECTIONSTRING

Alleen voor verbruik & Premium-abonnementen. Verbindingsreeks voor het opslagaccount waarin de functie-app-code en de configuratie zijn opgeslagen. Zie [maken van een functie-app](functions-infrastructure-as-code.md#create-a-function-app).

|Sleutel|Voorbeeldwaarde|
|---|------------|
|WEBSITE_CONTENTAZUREFILECONNECTIONSTRING|DefaultEndpointsProtocol = https; AccountName = [name]; AccountKey = [-toets]|

## <a name="website_contentshare"></a>WEBSITE\_CONTENTSHARE

Alleen voor verbruik & Premium-abonnementen. Het pad naar de functie-app-code en configuratie. Met WEBSITE_CONTENTAZUREFILECONNECTIONSTRING gebruikt. Standaard is een unieke tekenreeks die met de naam van de functie-app begint. Zie [maken van een functie-app](functions-infrastructure-as-code.md#create-a-function-app).

|Sleutel|Voorbeeldwaarde|
|---|------------|
|WEBSITE_CONTENTSHARE|functionapp091999e2|

## <a name="website_max_dynamic_application_scale_out"></a>WEBSITE\_MAX\_DYNAMISCHE\_TOEPASSING\_SCHAAL\_UIT

Het maximum aantal exemplaren die de functie-app naar uitschalen kunt. Standaard is geen limiet.

> [!NOTE]
> Deze instelling is een preview-functie- en alleen betrouwbare indien ingesteld op een waarde < = 5

|Sleutel|Voorbeeldwaarde|
|---|------------|
|WEBSITE\_MAX\_DYNAMISCHE\_TOEPASSING\_SCHAAL\_UIT|5|

## <a name="website_node_default_version"></a>WEBSITE\_KNOOPPUNT\_DEFAULT_VERSION

_Alleen Windows._  
Hiermee stelt u de versie van node. js in die moet worden gebruikt voor het uitvoeren van uw functie-app in Windows. U moet een tilde (~) gebruiken om de runtime de meest recente beschik bare versie van de doel primaire versie te laten gebruiken. Als u bijvoorbeeld instelt op `~10`, wordt de meest recente versie van node. js 10 gebruikt. Wanneer een primaire versie is gericht op een tilde, hoeft u de secundaire versie niet hand matig bij te werken. 

|Sleutel|Voorbeeldwaarde|
|---|------------|
|WEBSITE\_KNOOPPUNT\_DEFAULT_VERSION|~ 10|

## <a name="website_run_from_package"></a>WEBSITE\_UITVOEREN\_FROM\_PAKKET

Hiermee kunt uw functie-app om uit te voeren vanuit een gekoppelde pakketbestand.

|Sleutel|Voorbeeldwaarde|
|---|------------|
|WEBSITE\_UITVOEREN\_FROM\_PAKKET|1|

Geldige waarden zijn een URL die wordt omgezet naar de locatie van een pakketbestand implementatie of `1`. Als de waarde `1`, het pakket moet zich in de `d:\home\data\SitePackages` map. Wanneer u zip-implementatie met deze instelling gebruikt, wordt het pakket automatisch geüpload naar deze locatie. In preview, deze instelling is de naam `WEBSITE_RUN_FROM_ZIP`. Zie voor meer informatie, [uw functies worden uitgevoerd vanuit een pakketbestand](run-functions-from-deployment-package.md).

## <a name="azure_function_proxy_disable_local_call"></a>AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL

Standaard wordt Functions-proxy's gebruikmaken van een snelkoppeling naar de API-aanroepen van proxy's rechtstreeks naar de functies in dezelfde functie-App, in plaats van het maken van een nieuwe HTTP-aanvraag verzonden. Deze instelling kunt u uitschakelen dat gedrag.

|Sleutel|Waarde|Beschrijving|
|-|-|-|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|waar|Aanroepen met een back-end-URL die verwijst naar een functie in de lokale functie-app worden niet meer rechtstreeks naar de functie verzonden en worden in plaats daarvan teruggestuurd naar de HTTP-front-end voor de functie-app|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|onwaar|Dit is de standaardwaarde. Aanroepen met een back-end-url die verwijst naar een functie van de lokale functie-App wordt rechtstreeks naar deze functie worden doorgestuurd|


## <a name="azure_function_proxy_backend_url_decode_slashes"></a>AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES

Deze instelling wordt bepaald of % 2F is gedecodeerd als slashes in de Routeparameters wanneer ze in de back-end-URL worden ingevoegd. 

|Sleutel|Waarde|Beschrijving|
|-|-|-|
|AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES|waar|Parameters voor route met gecodeerde slashes hebben ze ontsleuteld. `example.com/api%2ftest` wordt `example.com/api/test`|
|AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES|onwaar|Dit is de standaardinstelling. Alle route parameters worden doorgegeven ongewijzigd|

### <a name="example"></a>Voorbeeld

Hier volgt een voorbeeld van de proxies.json in een functie-app op de URL-myfunction.com

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
|waar|myFunction.com/test%2fapi|example.com/test/API
|onwaar|myFunction.com/test%2fapi|example.com/test%2fapi|


## <a name="next-steps"></a>Volgende stappen

[Meer informatie over het bijwerken van app-instellingen](functions-how-to-use-azure-function-app-settings.md#settings)

[Zie globale instellingen in het bestand host.json](functions-host-json.md)

[Zie andere appinstellingen voor App Service-apps](https://github.com/projectkudu/kudu/wiki/Configurable-settings)
