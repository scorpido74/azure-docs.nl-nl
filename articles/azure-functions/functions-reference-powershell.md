---
title: Naslag informatie over Power shell-ontwikkel aars voor Azure Functions
description: Meer informatie over het ontwikkelen van functies met behulp van Power shell.
services: functions
documentationcenter: na
author: tylerleonhardt
manager: jeconnoc
ms.service: azure-functions
ms.devlang: powershell
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: tyleonha
ms.reviewer: glenga
ms.openlocfilehash: 36d24e798e73ef336324eedadee1ba3fec4c0e1d
ms.sourcegitcommit: a4b5d31b113f520fcd43624dd57be677d10fc1c0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/06/2019
ms.locfileid: "70773036"
---
# <a name="azure-functions-powershell-developer-guide"></a>Azure Functions Power shell-ontwikkelaars handleiding

In dit artikel vindt u informatie over hoe u Azure Functions schrijft met behulp van Power shell.

[!INCLUDE [functions-powershell-preview-note](../../includes/functions-powershell-preview-note.md)]

Een Power shell Azure-functie (functie) wordt weer gegeven als een Power shell-script dat wordt uitgevoerd wanneer het wordt geactiveerd. Elk functie script heeft een gerelateerd `function.json` bestand dat definieert hoe de functie zich gedraagt, zoals hoe deze wordt geactiveerd en de invoer-en uitvoer parameters. Zie het [artikel triggers en binding](functions-triggers-bindings.md)voor meer informatie. 

Net als andere soorten functies voeren Power shell-script functies in para meters die overeenkomen met de namen van alle invoer bindingen die in het `function.json` bestand zijn gedefinieerd. Er `TriggerMetadata` wordt ook een para meter door gegeven met aanvullende informatie over de trigger die de functie heeft gestart.

In dit artikel wordt ervan uitgegaan dat u de [Azure functions Naslag informatie voor ontwikkel aars](functions-reference.md)al hebt gelezen. U moet ook de Quick Start [van functions voor Power shell](functions-create-first-function-powershell.md) hebben voltooid om uw eerste Power shell-functie te maken.

## <a name="folder-structure"></a>Mapstructuur

De vereiste mapstructuur voor een Power Shell-project ziet er als volgt uit. Deze standaard instelling kan worden gewijzigd. Zie het gedeelte [script](#configure-function-scriptfile) voor meer informatie.

```
PSFunctionApp
 | - MyFirstFunction
 | | - run.ps1
 | | - function.json
 | - MySecondFunction
 | | - run.ps1
 | | - function.json
 | - Modules
 | | - myFirstHelperModule
 | | | - myFirstHelperModule.psd1
 | | | - myFirstHelperModule.psm1
 | | - mySecondHelperModule
 | | | - mySecondHelperModule.psd1
 | | | - mySecondHelperModule.psm1
 | - local.settings.json
 | - host.json
 | - requirements.psd1
 | - profile.ps1
 | - extensions.csproj
 | - bin
```

In de hoofdmap van het project is er een gedeeld [`host.json`](functions-host-json.md) bestand dat kan worden gebruikt voor het configureren van de functie-app. Elke functie heeft een map met een eigen code bestand (. ps1) en een bindings configuratie`function.json`bestand (). De naam van de bovenliggende map van de functie. JSON-bestand is altijd de naam van uw functie.

Voor bepaalde bindingen moet een `extensions.csproj` bestand aanwezig zijn. Bindings uitbreidingen, vereist in [versie 2. x](functions-versions.md) van de functions runtime, worden gedefinieerd `extensions.csproj` in het bestand met de daad werkelijke bibliotheek bestanden `bin` in de map. Wanneer u lokaal ontwikkelt, moet u [bindings uitbreidingen registreren](functions-bindings-register.md#extension-bundles). Bij het ontwikkelen van functies in de Azure Portal, wordt deze registratie voor u uitgevoerd.

In de Power shell-functie-apps kunt u eventueel `profile.ps1` een van de uitvoeringen uitvoeren wanneer een functie-app wordt gestart (anders weet u als een *[koude start](#cold-start)* . Zie [Power shell profile](#powershell-profile)(Engelstalig) voor meer informatie.

## <a name="defining-a-powershell-script-as-a-function"></a>Een Power shell-script als functie definiëren

De functies runtime zoekt standaard naar uw functie in `run.ps1`, waarbij `run.ps1` dezelfde bovenliggende map wordt gedeeld als de bijbehorende `function.json`.

Het script heeft een aantal argumenten door gegeven bij de uitvoering. Als u deze para meters wilt `param` afhandelen, voegt u een blok toe aan de bovenkant van uw script, zoals in het volgende voor beeld:

```powershell
# $TriggerMetadata is optional here. If you don't need it, you can safely remove it from the param block
param($MyFirstInputBinding, $MySecondInputBinding, $TriggerMetadata)
```

### <a name="triggermetadata-parameter"></a>TriggerMetadata-para meter

De `TriggerMetadata` para meter wordt gebruikt om aanvullende informatie over de trigger op te geven. De aanvullende meta gegevens zijn afhankelijk van binding met binding, maar ze `sys` bevatten allemaal een eigenschap die de volgende gegevens bevat:

```powershell
$TriggerMetadata.sys
```

| Eigenschap   | Description                                     | type     |
|------------|-------------------------------------------------|----------|
| UtcNow     | Wanneer, in UTC, de functie is geactiveerd        | DateTime |
| MethodName | De naam van de functie die is geactiveerd     | string   |
| RandGuid   | een unieke GUID voor deze uitvoering van de functie | string   |

Elk trigger type heeft een andere set meta gegevens. Bijvoorbeeld `$TriggerMetadata` : de for `InsertionTime` `Id`bevat de ,`DequeueCount`,, onder andere. `QueueTrigger` Ga naar de [officiële documentatie voor wachtrij Triggers](functions-bindings-storage-queue.md#trigger---message-metadata)voor meer informatie over de meta gegevens van de trigger van de wachtrij. Raadpleeg de documentatie op de [Triggers](functions-triggers-bindings.md) waarmee u werkt om te zien wat er in de meta gegevens van de trigger zit.

## <a name="bindings"></a>Bindingen

In Power shell worden [bindingen](functions-triggers-bindings.md) geconfigureerd en gedefinieerd in de functie Function. json van een functie. Functies werken op een aantal manieren met bindingen.

### <a name="reading-trigger-and-input-data"></a>Trigger-en invoer gegevens lezen

Trigger-en invoer bindingen worden gelezen als para meters die zijn door gegeven aan de functie. Invoer bindingen hebben een `direction` ingesteld op `in` in function. json. De `name` eigenschap die is `function.json` gedefinieerd in, is de naam van de para `param` meter in het blok. Aangezien Power shell benoemde para meters voor binding gebruikt, is de volg orde van de para meters niet van belang. Het is echter een best practice om de volg orde te volgen van de bindingen die zijn `function.json`gedefinieerd in de.

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)
```

### <a name="writing-output-data"></a>Uitvoer gegevens schrijven

In functions is `direction` een uitvoer binding ingesteld op `out` in de function. json. U kunt schrijven naar een uitvoer binding met behulp `Push-OutputBinding` van de-cmdlet, die beschikbaar is voor de functions-runtime. In alle gevallen is de `name` eigenschap van de binding zoals gedefinieerd in `function.json` overeenkomt met `Name` de para meter `Push-OutputBinding` van de cmdlet.

Hieronder ziet u hoe u een `Push-OutputBinding` functie script aanroept:

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)

Push-OutputBinding -Name myQueue -Value $myValue
```

U kunt ook een waarde voor een specifieke binding door geven via de pijp lijn.

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)

Produce-MyOutputValue | Push-OutputBinding -Name myQueue
```

`Push-OutputBinding`gedraagt zich anders op basis van de waarde `-Name`die is opgegeven voor:

* Als de opgegeven naam niet kan worden omgezet in een geldige uitvoer binding, wordt een fout gegenereerd.

* Wanneer de uitvoer binding een verzameling waarden accepteert, kunt u herhaaldelijk aanroepen `Push-OutputBinding` om meerdere waarden te pushen.

* Wanneer de uitvoer binding alleen een singleton waarde accepteert en een `Push-OutputBinding` tweede keer aanroept, treedt er een fout op.

#### <a name="push-outputbinding-syntax"></a>`Push-OutputBinding`syntaxis

De volgende para meters zijn geldig `Push-OutputBinding`voor het aanroepen van:

| Name | type | Positie | Description |
| ---- | ---- |  -------- | ----------- |
| **`-Name`** | Tekenreeks | 1 | De naam van de uitvoer binding die u wilt instellen. |
| **`-Value`** | Object | 2 | De waarde van de uitvoer binding die u wilt instellen, die wordt geaccepteerd vanuit de pipeline-ByValue. |
| **`-Clobber`** | SwitchParameter | Beveiligingscommunity | Beschrijving Indien opgegeven, dwingt de waarde voor een opgegeven uitvoer binding te worden ingesteld. | 

De volgende algemene para meters worden ook ondersteund: 
* `Verbose`
* `Debug`
* `ErrorAction`
* `ErrorVariable`
* `WarningAction`
* `WarningVariable`
* `OutBuffer`
* `PipelineVariable`
* `OutVariable` 

Zie [about CommonParameters](https://go.microsoft.com/fwlink/?LinkID=113216)(Engelstalig) voor meer informatie.

#### <a name="push-outputbinding-example-http-responses"></a>Voor beeld van push-OutputBinding: HTTP-antwoorden

Een HTTP-trigger retourneert een antwoord met behulp van `response`een uitvoer binding met de naam. In het volgende voor beeld heeft de uitvoer binding `response` van de waarde "uitvoer #1":

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #1"
})
```

Omdat de uitvoer naar http gaat, waardoor alleen een singleton waarde wordt geaccepteerd, wordt een fout gegenereerd `Push-OutputBinding` wanneer een tweede keer wordt aangeroepen.

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #2"
})
```

Voor uitvoer die alleen Singleton-waarden accepteert, kunt u de `-Clobber` para meter gebruiken om de oude waarde te overschrijven in plaats van toe te voegen aan een verzameling. In het volgende voor beeld wordt ervan uitgegaan dat u al een waarde hebt toegevoegd. Met behulp `-Clobber`van de reactie van het volgende voor beeld wordt de bestaande waarde overschreven om de waarde ' output #3 ' te retour neren:

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #3"
}) -Clobber
```

#### <a name="push-outputbinding-example-queue-output-binding"></a>Voor beeld van push-OutputBinding: Uitvoer binding wachtrij

`Push-OutputBinding`wordt gebruikt voor het verzenden van gegevens naar uitvoer bindingen, zoals een [Azure Queue Storage-uitvoer binding](functions-bindings-storage-queue.md#output). In het volgende voor beeld heeft het bericht dat naar de wachtrij is geschreven de waarde "uitvoer #1":

```powershell
PS >Push-OutputBinding -Name outQueue -Value "output #1"
```

De uitvoer binding voor een opslag wachtrij accepteert meerdere uitvoer waarden. In dit geval moet u het volgende voor beeld aanroepen nadat de eerste keer naar de wachtrij is geschreven een lijst met twee items: "uitvoer #1" en "uitvoer #2".

```powershell
PS >Push-OutputBinding -Name outQueue -Value "output #2"
```

In het volgende voor beeld, wanneer aangeroepen na de vorige twee, worden twee meer waarden toegevoegd aan de uitvoer verzameling:

```powershell
PS >Push-OutputBinding -Name outQueue -Value @("output #3", "output #4")
```

Wanneer het bericht naar de wachtrij wordt geschreven, bevat deze vier waarden: "uitvoer #1", "uitvoer #2", "uitvoer #3" en "uitvoer #4".

#### <a name="get-outputbinding-cmdlet"></a>`Get-OutputBinding`cmdlet

U kunt de `Get-OutputBinding` cmdlet gebruiken om de waarden op te halen die momenteel zijn ingesteld voor uw uitvoer bindingen. Met deze cmdlet wordt een hashtabel opgehaald die de namen van de uitvoer bindingen met hun respectieve waarden bevat. 

Hier volgt een voor beeld van het `Get-OutputBinding` gebruik van om huidige bindings waarden te retour neren:

```powershell
Get-OutputBinding
```

```Output
Name                           Value
----                           -----
MyQueue                        myData
MyOtherQueue                   myData
```

`Get-OutputBinding`bevat ook een para meter `-Name`met de naam, die kan worden gebruikt voor het filteren van de geretourneerde binding, zoals in het volgende voor beeld:

```powershell
Get-OutputBinding -Name MyQ*
```

```Output
Name                           Value
----                           -----
MyQueue                        myData
```

Joker tekens (*) worden ondersteund in `Get-OutputBinding`.

## <a name="logging"></a>Logboekregistratie

Logboek registratie in Power shell-functies werkt zoals bij normale Power shell-logboek registratie. U kunt de logboek registratie-cmdlets gebruiken om naar elke uitvoer stroom te schrijven. Elke cmdlet wordt toegewezen aan een logboek niveau dat wordt gebruikt door-functies.

| Niveau van de functie logboek registratie | Logboek registratie-cmdlet |
| ------------- | -------------- |
| Fout | **`Write-Error`** |
| Waarschuwing | **`Write-Warning`**  | 
| Information | **`Write-Information`** <br/> **`Write-Host`** <br /> **`Write-Output`**      | Information | Schrijft naar logboek registratie op _informatie_ niveau. |
| fouten opsporen | **`Write-Debug`** |
| Trace | **`Write-Progress`** <br /> **`Write-Verbose`** |

Naast deze cmdlets wordt alles wat naar de pijp lijn is geschreven, omgeleid naar het `Information` logboek niveau en weer gegeven met de standaard Power shell-opmaak.

> [!IMPORTANT]
> Het gebruik `Write-Verbose` van `Write-Debug` de cmdlets of is niet voldoende om de logboek registratie voor uitgebreid en fout opsporing te controleren. U moet ook de drempel voor het niveau van het logboek configureren, waarmee wordt aangegeven welk niveau van Logboeken u eigenlijk vindt. Zie [het logboek niveau van de functie-app configureren](#configure-the-function-app-log-level)voor meer informatie.

### <a name="configure-the-function-app-log-level"></a>Het logboek niveau van de functie-app configureren

Met Azure Functions kunt u het drempel niveau definiëren, zodat de manier waarop functies naar de logboeken worden geschreven eenvoudig kan worden beheerd. Als u de drempel waarde wilt instellen voor alle traceringen die naar de- `logging.logLevel.default` console worden geschreven, gebruikt u de eigenschap in de [ `host.json` ]JSON-verwijzing voor de bestands[host.] Deze instelling is van toepassing op alle functies in uw functie-app.

In het volgende voor beeld wordt de drempel ingesteld om uitgebreide logboek registratie in te scha kelen voor alle functies, maar wordt de drempel ingesteld op `MyFunction`het inschakelen van logboek registratie voor fout opsporing voor een functie met de naam:

```json
{
    "logging": {
        "logLevel": {
            "Function.MyFunction": "Debug",
            "default": "Trace"
        }
    }
}  
```

Zie voor meer informatie [Naslag informatie over host. json].

### <a name="viewing-the-logs"></a>De logboeken weer geven

Als uw functie-app wordt uitgevoerd in azure, kunt u Application Insights gebruiken om het te controleren. Lees de [controle Azure functions](functions-monitoring.md) voor meer informatie over het weer geven en opvragen van functie Logboeken.

Als u uw functie-app lokaal uitvoert voor ontwikkeling, registreert de standaard instellingen voor het bestands systeem. Als u de logboeken wilt weer geven in de `AZURE_FUNCTIONS_ENVIRONMENT` -console, `Development` stelt u de omgevings variabele in op voordat u begint met de functie-app.

## <a name="triggers-and-bindings-types"></a>Typen triggers en bindingen

Er zijn een aantal triggers en bindingen die u kunt gebruiken met uw functie-app. De volledige lijst met triggers en bindingen vindt u [hier](functions-triggers-bindings.md#supported-bindings).

Alle triggers en bindingen worden in code weer gegeven als enkele echte gegevens typen:

* Hashtabel
* string
* byte[]
* int
* double
* HttpRequestContext
* HttpResponseContext

De eerste vijf typen in deze lijst zijn standaard .NET-typen. De laatste twee worden alleen gebruikt door de [trigger http trigger](#http-triggers-and-bindings).

Elke bindings parameter in uw functies moet een van deze typen zijn.

### <a name="http-triggers-and-bindings"></a>HTTP-triggers en-bindingen

HTTP-en webhook-triggers en HTTP-uitvoer bindingen gebruiken aanvraag-en antwoord objecten om de HTTP-berichten te vertegenwoordigen.

#### <a name="request-object"></a>Aanvraag object

Het object Request dat is door gegeven aan het script, is van `HttpRequestContext`het type, dat de volgende eigenschappen heeft:

| Eigenschap  | Description                                                    | type                      |
|-----------|----------------------------------------------------------------|---------------------------|
| **`Body`**    | Een object dat de hoofd tekst van de aanvraag bevat. `Body`wordt geserialiseerd in het beste type op basis van de gegevens. Als de gegevens bijvoorbeeld JSON zijn, wordt deze als een hashtabel door gegeven. Als de gegevens een teken reeks is, wordt deze als een teken reeks door gegeven. | object |
| **`Headers`** | Een woorden lijst die de aanvraag headers bevat.                | Dictionary < teken reeks, teken reeks ><sup>*</sup> |
| **`Method`** | De HTTP-methode van de aanvraag.                                | string                    |
| **`Params`**  | Een object dat de routerings parameters van de aanvraag bevat. | Dictionary < teken reeks, teken reeks ><sup>*</sup> |
| **`Query`** | Een object dat de query parameters bevat.                  | Dictionary < teken reeks, teken reeks ><sup>*</sup> |
| **`Url`** | De URL van de aanvraag.                                        | string                    |

<sup>*</sup>Alle `Dictionary<string,string>` sleutels zijn niet hoofdletter gevoelig.

#### <a name="response-object"></a>Responsobject

Het antwoord object dat u moet terugsturen, is van het `HttpResponseContext`type, dat de volgende eigenschappen heeft:

| Eigenschap      | Description                                                 | type                      |
|---------------|-------------------------------------------------------------|---------------------------|
| **`Body`**  | Een object dat de hoofd tekst van het antwoord bevat.           | object                    |
| **`ContentType`** | Een korte hand voor het instellen van het inhouds type voor de reactie. | string                    |
| **`Headers`** | Een object dat de antwoord headers bevat.               | Woorden lijst of hashtabel   |
| **`StatusCode`**  | De HTTP-status code van het antwoord.                       | teken reeks of int             |

#### <a name="accessing-the-request-and-response"></a>De aanvraag en het antwoord openen

Wanneer u met HTTP-triggers werkt, kunt u de HTTP-aanvraag op dezelfde manier benaderen als andere invoer bindingen. Het is in het `param` blok.

Gebruik een `HttpResponseContext` object om een antwoord te retour neren, zoals wordt weer gegeven in het volgende:

`function.json`

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "authLevel": "anonymous"
    },
    {
      "type": "http",
      "direction": "out"
    }
  ]
}
```

`run.ps1`

```powershell
param($req, $TriggerMetadata)

$name = $req.Query.Name

Push-OutputBinding -Name res -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "Hello $name!"
})
```

Het resultaat van het aanroepen van deze functie zou zijn:

```
PS > irm http://localhost:5001?Name=Functions
Hello Functions!
```

### <a name="type-casting-for-triggers-and-bindings"></a>Type-cast voor triggers en bindingen

U kunt voor bepaalde bindingen, zoals de BLOB-binding, het type van de para meter opgeven.

Als u bijvoorbeeld gegevens uit Blob Storage wilt opgeven als een teken reeks, voegt u het volgende type cast toe `param` aan mijn blok:

```powershell
param([string] $myBlob)
```

## <a name="powershell-profile"></a>Power shell-profiel

In Power shell is het concept van een Power shell-profiel. Zie [about Profiles](/powershell/module/microsoft.powershell.core/about/about_profiles)(Engelstalig) als u niet bekend bent met Power shell-profielen.

In Power shell-functies wordt het profiel script uitgevoerd wanneer de functie-app wordt gestart. Functie-apps worden gestart wanneer deze voor het eerst worden geïmplementeerd en na een inactiviteit van het systeem ([koude start](#cold-start)).

Wanneer u een functie-app maakt met behulp van hulpprogram ma's, zoals Visual Studio code en `profile.ps1` Azure functions core tools, wordt er een standaard voor u gemaakt. Het standaard profiel wordt beheerd [op basis van de kern Hulpprogramma's github-opslag plaats](https://github.com/Azure/azure-functions-core-tools/blob/dev/src/Azure.Functions.Cli/StaticResources/profile.ps1) en bevat:

* Automatische MSI-verificatie naar Azure.
* De mogelijkheid om de Azure PowerShell `AzureRM` Power shell-aliassen in te scha kelen als u dat wilt.

## <a name="powershell-version"></a>Power shell-versie

In de volgende tabel ziet u de Power shell-versie die wordt gebruikt door elke primaire versie van de functions runtime:

| Functie versie | Power shell-versie                             |
|-------------------|------------------------------------------------|
| 1.x               | Windows Power shell 5,1 (vergrendeld door de runtime) |
| 2.x               | Power shell Core 6                              |

U kunt de huidige versie bekijken door af `$PSVersionTable` te drukken vanuit een functie.

## <a name="dependency-management"></a>Beheer van afhankelijkheden

Power shell-functies ondersteunen het downloaden en beheren van [Power shell Gallery](https://www.powershellgallery.com) -modules door de service. Door het wijzigen van de host. json en het instellen van de eigenschap managedDependency enabled op True, wordt het bestand requirements. psd1 verwerkt. De opgegeven modules worden automatisch gedownload en beschikbaar gesteld voor de functie. 

Het maximum aantal ondersteunde modules is 10. De ondersteunde syntaxis is MajorNumber. * of de exacte module versie zoals hieronder wordt weer gegeven. De Azure AZ-module is standaard opgenomen wanneer een nieuwe Power shell-functie-app wordt gemaakt.

De werk nemer van de taal haalt bijgewerkte modules op die opnieuw moeten worden opgestart.

host. json
```json
{
  "managedDependency": {
          "enabled": true
       }
}
```

requirements.psd1

```powershell
@{
    Az = '1.*'
    SqlServer = '21.1.18147'
}
```

Het gebruik van uw eigen aangepaste modules wijkt af van de manier waarop u het normaal zou doen.

Wanneer u de module op uw lokale computer installeert, wordt deze in een van de wereld wijd beschik bare `$env:PSModulePath`mappen in uw weer. Omdat uw functie wordt uitgevoerd in azure, hebt u geen toegang tot de modules die op uw computer zijn geïnstalleerd. Hiervoor moet de `$env:PSModulePath` for a Power shell-functie-app afwijken `$env:PSModulePath` van een normaal Power shell-script.

In functions `PSModulePath` bevat twee paden:

* Een `Modules` map die zich in de hoofdmap van uw functie-app bevindt.
* Een pad naar een `Modules` map die zich in de Power shell-werk nemer bevindt.

### <a name="function-app-level-modules-folder"></a>Functie app-niveau `Modules` -map

Als u aangepaste modules wilt gebruiken, kunt u modules plaatsen waarvoor uw functies afhankelijk zijn `Modules` van een map. Vanuit deze map zijn modules automatisch beschikbaar voor de functions-runtime. Elke functie in de functie-app kan deze modules gebruiken. 

> [!NOTE]
> Modules die zijn opgegeven in het bestand requirements. psd1, worden automatisch gedownload en opgenomen in het pad, zodat u ze niet hoeft op te nemen in de map modules. Deze worden lokaal opgeslagen in de map $env: LOCALAPPDATA/AzureFunctions en in de map/data/ManagedDependencies wanneer ze worden uitgevoerd in de Cloud.

Als u wilt profiteren van de functie aangepaste module, maakt `Modules` u een map in de hoofdmap van de functie-app. Kopieer de modules die u wilt gebruiken in uw functies naar deze locatie.

```powershell
mkdir ./Modules
Copy-Item -Path /mymodules/mycustommodule -Destination ./Modules -Recurse
```

Met een modules-map moet uw functie-app de volgende mapstructuur hebben:

```
PSFunctionApp
 | - MyFunction
 | | - run.ps1
 | | - function.json
 | - Modules
 | | - MyCustomModule
 | | - MyOtherCustomModule
 | | - MySpecialModule.psm1
 | - local.settings.json
 | - host.json
 | - requirements.psd1
```

Wanneer u de functie-app start, voegt de Power shell- `Modules` taal werk nemer `$env:PSModulePath` deze map toe aan de zodat u kunt vertrouwen op het automatisch laden van module, net zoals u dat zou doen in een gewoon Power shell-script.

### <a name="language-worker-level-modules-folder"></a>Map language worker level `Modules`

Diverse modules worden vaak gebruikt door de Power shell-werk nemer. Deze modules worden gedefinieerd op de laatste positie van `PSModulePath`. 

De huidige lijst met modules is als volgt:

* [Micro soft. Power shell. Archive](https://www.powershellgallery.com/packages/Microsoft.PowerShell.Archive): module die wordt gebruikt voor het `.zip`werken `.nupkg`met archieven, zoals, en anderen.
* **ThreadJob**: Een op thread gebaseerde implementatie van de Power shell-taak-Api's.

De meest recente versie van deze modules wordt gebruikt door-functies. Als u een specifieke versie van deze modules wilt gebruiken, kunt u de specifieke versie in `Modules` de map van uw functie-app plaatsen.

## <a name="environment-variables"></a>Omgevingsvariabelen

In functions worden [app-instellingen](functions-app-settings.md), zoals teken reeksen voor service verbindingen, weer gegeven als omgevings variabelen tijdens de uitvoering. U kunt deze instellingen openen met `$env:NAME_OF_ENV_VAR`, zoals wordt weer gegeven in het volgende voor beeld:

```powershell
param($myTimer)

Write-Host "PowerShell timer trigger function ran! $(Get-Date)"
Write-Host $env:AzureWebJobsStorage
Write-Host $env:WEBSITE_SITE_NAME
```

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

Wanneer u lokaal uitvoert, worden de app-instellingen gelezen uit het bestand [Local. settings. json](functions-run-local.md#local-settings-file) project.

## <a name="concurrency"></a>Gelijktijdigheid

De functies van Power shell runtime kunnen standaard slechts één aanroep van een functie tegelijk verwerken. Dit gelijktijdigheids niveau is echter mogelijk niet voldoende in de volgende situaties:

* Wanneer u een groot aantal aanroepen tegelijk probeert af te handelen.
* Wanneer u functies hebt die andere functies binnen dezelfde functie-app aanroepen.

U kunt dit gedrag wijzigen door de volgende omgevings variabele in te stellen op een geheel getal:

```
PSWorkerInProcConcurrencyUpperBound
```

U stelt deze omgevings variabele in de [app-instellingen](functions-app-settings.md) van uw functie-app in.

### <a name="considerations-for-using-concurrency"></a>Overwegingen voor het gebruik van gelijktijdigheid

Power shell is standaard een script taal met _één thread_ . Gelijktijdigheid kan echter worden toegevoegd met behulp van meerdere Power shell-runspaces in hetzelfde proces. De hoeveelheid runspaces die wordt gemaakt, komt overeen met de instelling van de PSWorkerInProcConcurrencyUpperBound-toepassing. De door Voer wordt beïnvloed door de hoeveelheid CPU en het geheugen die beschikbaar is in het geselecteerde abonnement.

Azure PowerShell maakt gebruik van bepaalde contexten op _proces niveau_ en de status om u te helpen bij het besparen van het overschrijven van typen. Als u echter gelijktijdig gebruik in uw functie-app inschakelt en acties aanroept die de status wijzigen, kunt u de timing van race problemen beëindigen. Deze race voorwaarden zijn moeilijk te debuggen omdat een aanroep afhankelijk is van een bepaalde status en de andere aanroep de status heeft gewijzigd.

Er is een enorme waarde in gelijktijdigheid met Azure PowerShell, omdat sommige bewerkingen veel tijd in beslag kunnen nemen. U moet echter wel voorzichtig door gaan. Als u vermoedt dat u een race voorwaarde ondervindt, stelt u de PSWorkerInProcConcurrencyUpperBound `1` -app-instelling in op en gebruikt u in plaats daarvan [taal werk proces niveau isolatie](functions-app-settings.md#functions_worker_process_count) voor gelijktijdigheid.

## <a name="configure-function-scriptfile"></a>Functie configureren`scriptFile`

Standaard wordt een Power shell-functie uitgevoerd vanuit `run.ps1`, een bestand dat dezelfde bovenliggende map als de corresponderende `function.json`Directory deelt.

De `scriptFile` eigenschap in de `function.json` kan worden gebruikt om een mapstructuur te verkrijgen die eruitziet als in het volgende voor beeld:

```
FunctionApp
 | - host.json
 | - myFunction
 | | - function.json
 | - lib
 | | - PSFunction.ps1
```

In dit geval bevat de `function.json` voor `myFunction` een `scriptFile` -eigenschap die verwijst naar het bestand met de geëxporteerde functie om uit te voeren.

```json
{
  "scriptFile": "../lib/PSFunction.ps1",
  "bindings": [
    // ...
  ]
}
```

## <a name="use-powershell-modules-by-configuring-an-entrypoint"></a>Power shell-modules gebruiken door een ingangs punt te configureren

In dit artikel worden Power shell-functies weer `run.ps1` gegeven in het standaard script bestand dat door de sjablonen wordt gegenereerd.
U kunt echter ook uw functies in Power shell-modules toevoegen. U kunt verwijzen naar uw specifieke functie code in de module met behulp `entryPoint` van de `scriptFile` velden en in het configuratie bestand function. json.

In dit geval `entryPoint` is de naam van een functie of cmdlet in de Power shell-module waarnaar wordt `scriptFile`verwezen in.

Houd rekening met de volgende mapstructuur:

```
FunctionApp
 | - host.json
 | - myFunction
 | | - function.json
 | - lib
 | | - PSFunction.psm1
```

Waar `PSFunction.psm1` bevat:

```powershell
function Invoke-PSTestFunc {
    param($InputBinding, $TriggerMetadata)

    Push-OutputBinding -Name OutputBinding -Value "output"
}

Export-ModuleMember -Function "Invoke-PSTestFunc"
```

In dit voor beeld bevat de configuratie `myFunction` voor een `scriptFile` eigenschap die verwijst `PSFunction.psm1`naar een Power shell-module in een andere map.  De `entryPoint` eigenschap verwijst naar `Invoke-PSTestFunc` de functie. Dit is het toegangs punt in de module.

```json
{
  "scriptFile": "../lib/PSFunction.psm1",
  "entryPoint": "Invoke-PSTestFunc",
  "bindings": [
    // ...
  ]
}
```

Met deze configuratie worden de `Invoke-PSTestFunc` bewerkingen precies uitgevoerd als een `run.ps1` zou.

## <a name="considerations-for-powershell-functions"></a>Overwegingen voor Power shell-functies

Wanneer u werkt met Power shell-functies, moet u rekening houden met de overwegingen in de volgende secties.

### <a name="cold-start"></a>Koude start

Bij het ontwikkelen van Azure Functions in het [serverloze hosting model](functions-scale.md#consumption-plan)is koude start een werkelijkheid. *Koude start* verwijst naar de tijd die nodig is om de functie-app uit te voeren om een aanvraag te verwerken. Koude start treedt vaker op in het verbruiks abonnement, omdat uw functie-app wordt afgesloten tijdens peri Oden van inactiviteit.

### <a name="bundle-modules-instead-of-using-install-module"></a>Bundel modules in plaats van gebruik te maken van`Install-Module`

Uw script wordt uitgevoerd op elke aanroep. Vermijd het `Install-Module` gebruik in uw script. Gebruik `Save-Module` in plaats daarvan vóór het publiceren zodat uw functie geen tijd verspilde bij het downloaden van de module. Als koude start invloed heeft op uw functies, kunt u overwegen om uw functie-app te implementeren in een [app service plan](functions-scale.md#app-service-plan) dat is ingesteld op *altijd* of op een [Premium-abonnement](functions-scale.md#premium-plan).

## <a name="next-steps"></a>Volgende stappen

Zie de volgende bronnen voor meer informatie:

* [Aanbevolen procedures voor Azure Functions](functions-best-practices.md)
* [Naslaginformatie over Azure Functions voor ontwikkelaars](functions-reference.md)
* [Azure Functions triggers en bindingen](functions-triggers-bindings.md)

[Naslag informatie over host. json]: functions-host-json.md
