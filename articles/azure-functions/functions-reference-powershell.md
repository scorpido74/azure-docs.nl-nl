---
title: PowerShell-ontwikkelaarsreferentie voor Azure-functies
description: Begrijp hoe u functies ontwikkelen met PowerShell.
author: eamonoreilly
ms.topic: conceptual
ms.date: 04/22/2019
ms.openlocfilehash: 41f977e7e7c23c2f49fd656461b7a3920802997e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276736"
---
# <a name="azure-functions-powershell-developer-guide"></a>PowerShell-ontwikkelaarshandleiding voor Azure Functions

In dit artikel vindt u meer informatie over hoe u Azure-functies schrijft met PowerShell.

Een PowerShell Azure-functie (functie) wordt weergegeven als een PowerShell-script dat wordt uitgevoerd wanneer deze wordt geactiveerd. Elk functiescript heeft `function.json` een gerelateerd bestand dat bepaalt hoe de functie zich gedraagt, zoals hoe het wordt geactiveerd en de invoer- en uitvoerparameters. Zie het artikel [Triggers en bindend voor](functions-triggers-bindings.md)meer informatie. 

Net als andere soorten functies nemen PowerShell-scriptfuncties parameters op die overeenkomen met `function.json` de namen van alle invoerbindingen die in het bestand zijn gedefinieerd. Er `TriggerMetadata` wordt ook een parameter doorgegeven die aanvullende informatie bevat over de trigger waarmee de functie is gestart.

In dit artikel wordt ervan uitgegaan dat u de verwijzing naar de [ontwikkelaar van Azure-functies](functions-reference.md)al hebt gelezen. U had ook de [snelstartfuncties voor PowerShell](functions-create-first-function-powershell.md) moeten voltooien om uw eerste PowerShell-functie te maken.

## <a name="folder-structure"></a>Mapstructuur

De vereiste mapstructuur voor een PowerShell-project ziet er als volgt uit. Deze standaardinstelling kan worden gewijzigd. Zie het gedeelte [scriptBestand](#configure-function-scriptfile) hieronder voor meer informatie.

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

Aan de basis van het project [`host.json`](functions-host-json.md) is er een gedeeld bestand dat kan worden gebruikt om de functie-app te configureren. Elke functie heeft een map met een eigen codebestand (.ps1) en bindend configuratiebestand (`function.json`). De naam van de bovenliggende map van het function.json-bestand is altijd de naam van uw functie.

Bepaalde bindingen vereisen de `extensions.csproj` aanwezigheid van een bestand. Bindende extensies, vereist in [versie 2.x en latere versies](functions-versions.md) van `extensions.csproj` de runtime Functies, worden `bin` gedefinieerd in het bestand, met de werkelijke bibliotheekbestanden in de map. Wanneer u lokaal ontwikkelt, moet u [bindende extensies registreren.](functions-bindings-register.md#extension-bundles) Bij het ontwikkelen van functies in de Azure-portal wordt deze registratie voor u uitgevoerd.

In PowerShell Function Apps u `profile.ps1` optioneel een functie-app uitvoeren wanneer een functie-app begint uit te voeren (anders bekend als een *[koude start.](#cold-start)* Zie [PowerShell-profiel](#powershell-profile)voor meer informatie .

## <a name="defining-a-powershell-script-as-a-function"></a>Een PowerShell-script definiëren als functie

Standaard zoekt de runtime Functies naar `run.ps1`uw `run.ps1` functie in , waarin `function.json`dezelfde bovenliggende map wordt gedeeld als de bijbehorende .

Uw script wordt doorgegeven een aantal argumenten over uitvoering. Als u deze parameters `param` wilt verwerken, voegt u een blok toe aan de bovenkant van uw script zoals in het volgende voorbeeld:

```powershell
# $TriggerMetadata is optional here. If you don't need it, you can safely remove it from the param block
param($MyFirstInputBinding, $MySecondInputBinding, $TriggerMetadata)
```

### <a name="triggermetadata-parameter"></a>Parameter TriggerMetadata

De `TriggerMetadata` parameter wordt gebruikt om aanvullende informatie over de trigger te verstrekken. De aanvullende metagegevens variëren van bindend `sys` tot bindend, maar ze bevatten allemaal een eigenschap die de volgende gegevens bevat:

```powershell
$TriggerMetadata.sys
```

| Eigenschap   | Beschrijving                                     | Type     |
|------------|-------------------------------------------------|----------|
| UtcNow (UtcNow)     | Wanneer in UTC de functie werd geactiveerd        | DateTime |
| MethodName | De naam van de functie die is geactiveerd     | tekenreeks   |
| RandGuid RandGuid   | een unieke guid aan deze uitvoering van de functie | tekenreeks   |

Elk triggertype heeft een andere set metagegevens. Bijvoorbeeld, de `$TriggerMetadata` `QueueTrigger` voor `InsertionTime`bevat `Id` `DequeueCount`de , , onder andere dingen. Ga voor meer informatie over de metagegevens van de wachtrijtrigger naar de [officiële documentatie voor wachtrijtriggers.](functions-bindings-storage-queue-trigger.md#message-metadata) Controleer de documentatie over de [triggers](functions-triggers-bindings.md) waarmee u werkt om te zien wat er in de triggermetagegevens komt.

## <a name="bindings"></a>Bindingen

In PowerShell worden [bindingen](functions-triggers-bindings.md) geconfigureerd en gedefinieerd in de function.json van een functie. Functies werken op een aantal manieren samen met bindingen.

### <a name="reading-trigger-and-input-data"></a>Trigger- en invoergegevens lezen

Trigger- en invoerbindingen worden gelezen als parameters die aan uw functie worden doorgegeven. Invoerbindingen hebben `direction` een `in` set in function.json. De `name` eigenschap gedefinieerd `function.json` in is de naam `param` van de parameter, in het blok. Aangezien PowerShell benoemde parameters gebruikt voor binding, maakt de volgorde van de parameters niet uit. Het is echter een beste gewoonte om de volgorde van `function.json`de bindingen gedefinieerd in de .

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)
```

### <a name="writing-output-data"></a>Uitvoergegevens schrijven

In Functies heeft een `direction` uitvoerbinding `out` een set in de function.json. U naar een uitvoerbinding `Push-OutputBinding` schrijven met behulp van de cmdlet, die beschikbaar is voor de runtime van de functies. In alle gevallen `name` komt de eigenschap van `function.json` de binding `Name` zoals `Push-OutputBinding` gedefinieerd in overeen met de parameter van de cmdlet.

Het volgende laat `Push-OutputBinding` zien hoe u uw functiescript aanroepen:

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)

Push-OutputBinding -Name myQueue -Value $myValue
```

U ook een waarde voor een specifieke binding door de pijplijn halen.

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)

Produce-MyOutputValue | Push-OutputBinding -Name myQueue
```

`Push-OutputBinding`zich anders gedraagt op basis `-Name`van de waarde die is opgegeven voor :

* Wanneer de opgegeven naam niet kan worden opgelost tot een geldige uitvoerbinding, wordt er een fout gegenereerd.

* Wanneer de uitvoerbinding een verzameling waarden accepteert, u herhaaldelijk bellen `Push-OutputBinding` om meerdere waarden te pushen.

* Wanneer de uitvoerbinding slechts één singletonwaarde accepteert, leidt het aanroepen van `Push-OutputBinding` een tweede keer tot een fout.

#### <a name="push-outputbinding-syntax"></a>`Push-OutputBinding`Syntaxis

De volgende zijn geldige `Push-OutputBinding`parameters voor het aanroepen:

| Name | Type | Positie | Beschrijving |
| ---- | ---- |  -------- | ----------- |
| **`-Name`** | Tekenreeks | 1 | De naam van de uitvoerbinding die u wilt instellen. |
| **`-Value`** | Object | 2 | De waarde van de uitvoerbinding die u wilt instellen, die wordt geaccepteerd vanuit de pijplijn ByValue. |
| **`-Clobber`** | SwitchParameter | Naam | (Optioneel) Wanneer dit is opgegeven, wordt de waarde ingesteld voor een opgegeven uitvoerbinding. | 

De volgende algemene parameters worden ook ondersteund: 
* `Verbose`
* `Debug`
* `ErrorAction`
* `ErrorVariable`
* `WarningAction`
* `WarningVariable`
* `OutBuffer`
* `PipelineVariable`
* `OutVariable` 

Zie [Algemene parameters voor](https://go.microsoft.com/fwlink/?LinkID=113216)meer informatie .

#### <a name="push-outputbinding-example-http-responses"></a>Push-OutputBinding voorbeeld: HTTP-antwoorden

Een HTTP-trigger retourneert een `response`antwoord met een uitvoerbinding met de naam . In het volgende voorbeeld heeft `response` de outputbinding de waarde van "output #1":

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #1"
})
```

Omdat de uitvoer naar HTTP is, dat alleen een singleton-waarde accepteert, wordt er een fout gegenereerd wanneer `Push-OutputBinding` een tweede keer wordt aangeroepen.

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #2"
})
```

Voor uitvoer die alleen singleton-waarden accepteren, u de `-Clobber` parameter gebruiken om de oude waarde te overschrijven in plaats van te proberen toe te voegen aan een verzameling. In het volgende voorbeeld wordt ervan uitgegaan dat u al een toegevoegde waarde hebt. Met `-Clobber`behulp van het antwoord uit het volgende voorbeeld wordt de bestaande waarde overschreven om een waarde van 'output #3' terug te geven:

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #3"
}) -Clobber
```

#### <a name="push-outputbinding-example-queue-output-binding"></a>Voorbeeld push-outputBinding: binding voor wachtrijuitvoer

`Push-OutputBinding`wordt gebruikt om gegevens naar uitvoerbindingen te verzenden, zoals een [Azure Queue-opslagbinding](functions-bindings-storage-queue-output.md). In het volgende voorbeeld heeft het bericht dat naar de wachtrij is geschreven een waarde van 'uitvoer #1':

```powershell
PS >Push-OutputBinding -Name outQueue -Value "output #1"
```

De uitvoerbinding voor een opslagwachtrij accepteert meerdere uitvoerwaarden. In dit geval wordt het volgende voorbeeld aanroepen nadat de eerste teksten naar de wachtrij worden geschreven, een lijst met twee items: "uitvoer #1" en "uitvoer#2".

```powershell
PS >Push-OutputBinding -Name outQueue -Value "output #2"
```

In het volgende voorbeeld voegt u, wanneer u naar de vorige twee wordt aangeroepen, nog twee waarden toe aan de uitvoerverzameling:

```powershell
PS >Push-OutputBinding -Name outQueue -Value @("output #3", "output #4")
```

Wanneer het bericht naar de wachtrij wordt geschreven, bevat het deze vier waarden: "uitvoer #1", "output #2", "output #3" en "output #4".

#### <a name="get-outputbinding-cmdlet"></a>`Get-OutputBinding`Cmdlet

U `Get-OutputBinding` de cmdlet gebruiken om de waarden op te halen die momenteel zijn ingesteld voor uw uitvoerbindingen. Deze cmdlet haalt een hashtable op die de namen van de uitvoerbindingen met hun respectieve waarden bevat. 

Het volgende is een `Get-OutputBinding` voorbeeld van het gebruik van huidige bindingswaarden:

```powershell
Get-OutputBinding
```

```Output
Name                           Value
----                           -----
MyQueue                        myData
MyOtherQueue                   myData
```

`Get-OutputBinding`bevat ook een `-Name`parameter genaamd , die kan worden gebruikt om de geretourneerde binding te filteren, zoals in het volgende voorbeeld:

```powershell
Get-OutputBinding -Name MyQ*
```

```Output
Name                           Value
----                           -----
MyQueue                        myData
```

Jokertekens (*) worden `Get-OutputBinding`ondersteund in .

## <a name="logging"></a>Logboekregistratie

Inloggen powershell functies werkt als gewone PowerShell logging. U de logboeken gebruiken om naar elke uitvoerstroom te schrijven. Elke cmdlet wordt toegewezen aan een logboekniveau dat door Functies wordt gebruikt.

| Logboekregistratieniveau voor functies | Logging cmdlet |
| ------------- | -------------- |
| Fout | **`Write-Error`** |
| Waarschuwing | **`Write-Warning`**  | 
| Informatie | **`Write-Information`** <br/> **`Write-Host`** <br /> **`Write-Output`**      | Informatie | Schrijft naar _Logboekregistratie op informatieniveau._ |
| Fouten opsporen | **`Write-Debug`** |
| Tracering | **`Write-Progress`** <br /> **`Write-Verbose`** |

Naast deze cmdlets wordt alles wat naar de `Information` pijplijn is geschreven, doorgestuurd naar het logboekniveau en weergegeven met de standaard PowerShell-opmaak.

> [!IMPORTANT]
> Het `Write-Verbose` gebruik `Write-Debug` van de of cmdlets is niet genoeg om verbose en debug level logging te zien. U moet ook de drempelwaarde voor logboekniveau configureren, die aangeeft welk niveau van logboeken u daadwerkelijk om geeft. Zie Het [logboekniveau van de functie-app configureren](#configure-the-function-app-log-level)voor meer informatie.

### <a name="configure-the-function-app-log-level"></a>Het logboekniveau van de functie-app configureren

Met Azure Functions u het drempelniveau definiëren om eenvoudig te bepalen hoe functies naar de logboeken worden geschreven. Als u de drempelwaarde wilt instellen voor `logging.logLevel.default` alle traces die naar de console zijn geschreven, gebruikt u de eigenschap in de [ `host.json` ]referentie file[host.json]. Deze instelling is van toepassing op alle functies in uw functie-app.

In het volgende voorbeeld wordt de drempel ingesteld om verboselogboeking in te schakelen `MyFunction`voor alle functies, maar hiermee wordt de drempelwaarde ingesteld om foutopsporingslogboekregistratie in te schakelen voor een functie met de naam :

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

Zie [host.json referentie voor]meer informatie .

### <a name="viewing-the-logs"></a>De logboeken weergeven

Als uw functie-app wordt uitgevoerd in Azure, u Toepassingsinzichten gebruiken om deze te controleren. Lees [Azure-functies voor het bewaken van Azure-functies](functions-monitoring.md) voor meer informatie over het weergeven en opvragen van functielogboeken.

Als u de functie-app lokaal uitvoert voor ontwikkeling, wordt standaard logboeken opgeslagen in het bestandssysteem. Als u de logboeken in `AZURE_FUNCTIONS_ENVIRONMENT` de `Development` console wilt bekijken, stelt u de omgevingsvariabele in voordat u de functie-app start.

## <a name="triggers-and-bindings-types"></a>Typen triggers en bindingen

Er zijn een aantal triggers en bindingen beschikbaar voor u om te gebruiken met uw functie-app. De volledige lijst met triggers en bindingen [vindt u hier.](functions-triggers-bindings.md#supported-bindings)

Alle triggers en bindingen worden weergegeven in code als een paar echte gegevenstypen:

* Hashtable
* tekenreeks
* byte[]
* int
* double
* HttpRequestContext
* HttpResponseContext

De eerste vijf typen in deze lijst zijn standaard .NET-typen. De laatste twee worden alleen gebruikt door de [HttpTrigger trigger](#http-triggers-and-bindings).

Elke bindingsparameter in uw functies moet een van deze typen zijn.

### <a name="http-triggers-and-bindings"></a>HTTP-triggers en bindingen

HTTP- en webhook-triggers en HTTP-uitvoerbindingen gebruiken aanvraag- en antwoordobjecten om de HTTP-berichten weer te geven.

#### <a name="request-object"></a>Object Aanvragen

Het aangezochte object dat in het `HttpRequestContext`script is doorgegeven, is van het type , dat de volgende eigenschappen heeft:

| Eigenschap  | Beschrijving                                                    | Type                      |
|-----------|----------------------------------------------------------------|---------------------------|
| **`Body`**    | Een object dat de hoofdtekst van het verzoek bevat. `Body`wordt geserialiseerd tot het beste type op basis van de gegevens. Als de gegevens bijvoorbeeld JSON zijn, wordt deze doorgegeven als een hashtabel. Als de gegevens een tekenreeks zijn, worden deze doorgegeven als een tekenreeks. | object |
| **`Headers`** | Een woordenboek met de aanvraagkoppen.                | Woordenboek<tekenreeks,><sup>*</sup> |
| **`Method`** | De HTTP-methode van de aanvraag.                                | tekenreeks                    |
| **`Params`**  | Een object dat de routeringsparameters van de aanvraag bevat. | Woordenboek<tekenreeks,><sup>*</sup> |
| **`Query`** | Een object dat de queryparameters bevat.                  | Woordenboek<tekenreeks,><sup>*</sup> |
| **`Url`** | De URL van het verzoek.                                        | tekenreeks                    |

<sup>*</sup>Alle `Dictionary<string,string>` sleutels zijn case-ongevoelig.

#### <a name="response-object"></a>Responsobject

Het antwoordobject dat u moet terugsturen, is van het type `HttpResponseContext`, dat de volgende eigenschappen heeft:

| Eigenschap      | Beschrijving                                                 | Type                      |
|---------------|-------------------------------------------------------------|---------------------------|
| **`Body`**  | Een object dat de hoofdtekst van de reactie bevat.           | object                    |
| **`ContentType`** | Een korte hand voor het instellen van het inhoudstype voor het antwoord. | tekenreeks                    |
| **`Headers`** | Een object dat de antwoordkoppen bevat.               | Woordenboek of hashtable   |
| **`StatusCode`**  | De HTTP-statuscode van het antwoord.                       | tekenreeks of int             |

#### <a name="accessing-the-request-and-response"></a>Toegang tot het verzoek en antwoord

Wanneer u met HTTP-triggers werkt, hebt u toegang tot het HTTP-verzoek op dezelfde manier als bij elke andere invoerbinding. Het is in `param` het blok.

Gebruik `HttpResponseContext` een object om een antwoord terug te sturen, zoals in het volgende wordt weergegeven:

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

### <a name="type-casting-for-triggers-and-bindings"></a>Typecasting voor triggers en bindingen

Voor bepaalde bindingen, zoals de blobbinding, u het type parameter opgeven.

Als u bijvoorbeeld gegevens uit blob-opslag als tekenreeks wilt laten `param` leveren, voegt u het volgende typecast toe aan mijn blok:

```powershell
param([string] $myBlob)
```

## <a name="powershell-profile"></a>PowerShell-profiel

In PowerShell is er het concept van een PowerShell profiel. Zie [Over profielen](/powershell/module/microsoft.powershell.core/about/about_profiles)als u niet bekend bent met PowerShell-profielen.

In PowerShell-functies wordt het profielscript uitgevoerd wanneer de functie-app wordt gestart. Functie-apps starten wanneer ze voor het eerst worden geïmplementeerd en nadat ze niet actief zijn[(koude start).](#cold-start)

Wanneer u een functie-app maakt met hulpprogramma's, zoals Visual `profile.ps1` Studio Code en Azure Functions Core Tools, wordt een standaard-app voor u gemaakt. Het standaardprofiel wordt onderhouden [op de Core Tools GitHub-repository](https://github.com/Azure/azure-functions-core-tools/blob/dev/src/Azure.Functions.Cli/StaticResources/profile.ps1) en bevat:

* Automatische MSI-verificatie naar Azure.
* De mogelijkheid om de Azure `AzureRM` PowerShell PowerShell-aliassen in te schakelen als u dat wilt.

## <a name="powershell-version"></a>PowerShell-versie

In de volgende tabel wordt de PowerShell-versie weergegeven die wordt gebruikt door elke belangrijke versie van de runtime Functies:

| Versie functies | PowerShell-versie                             |
|-------------------|------------------------------------------------|
| 1.x               | Windows PowerShell 5.1 (vergrendeld door de runtime) |
| 2.x               | PowerShell Core 6                              |

U de huidige `$PSVersionTable` versie bekijken door vanuit elke functie af te drukken.

## <a name="dependency-management"></a>Beheer van afhankelijkheden

Met Functions u [PowerShell-galerie](https://www.powershellgallery.com) gebruiken voor het beheren van afhankelijkheden. Als dependency management is ingeschakeld, wordt het requirements.psd1-bestand gebruikt om automatisch vereiste modules te downloaden. U schakelt dit gedrag `managedDependency` in `true` door de eigenschap in te stellen in de hoofdmap van het [bestand host.json,](functions-host-json.md)zoals in het volgende voorbeeld:

```json
{
  "managedDependency": {
          "enabled": true
       }
}
```

Wanneer u een nieuw PowerShell-functieproject maakt, is afhankelijkheidsbeheer standaard ingeschakeld, waarbij de [ `Az` Azure-module](/powershell/azure/new-azureps-module-az) is inbegrepen. Het maximum aantal modules dat momenteel wordt ondersteund is 10. De ondersteunde syntaxis is _`MajorNumber`_ `.*` of exacte moduleversie zoals weergegeven in het volgende voorbeeld requirements.psd1:

```powershell
@{
    Az = '1.*'
    SqlServer = '21.1.18147'
}
```

Wanneer u het requirements.psd1-bestand bijwerkt, worden bijgewerkte modules geïnstalleerd na een herstart.

> [!NOTE]
> Beheerde afhankelijkheden vereisen toegang tot www.powershellgallery.com om modules te downloaden. Wanneer u lokaal wordt uitgevoerd, moet u ervoor zorgen dat de runtime toegang heeft tot deze URL door de vereiste firewallregels toe te voegen. 

De volgende toepassingsinstellingen kunnen worden gebruikt om de manier waarop de beheerde afhankelijkheden worden gedownload en geïnstalleerd te wijzigen. Uw app-upgrade `MDMaxBackgroundUpgradePeriod`begint binnen, en het upgradeproces wordt voltooid binnen ongeveer de `MDNewSnapshotCheckPeriod`.

| Functie-app-instelling              | Standaardwaarde             | Beschrijving                                         |
|   -----------------------------   |   -------------------     |  -----------------------------------------------    |
| **`MDMaxBackgroundUpgradePeriod`**      | `7.00:00:00`(7 dagen)     | Elk PowerShell-werkproces initieert het controleren op module-upgrades in de PowerShell-galerie bij de start van het proces en elke `MDMaxBackgroundUpgradePeriod` daarna. Wanneer een nieuwe moduleversie beschikbaar is in de PowerShell Gallery, wordt deze geïnstalleerd op het bestandssysteem en beschikbaar gesteld aan PowerShell-werknemers. Door deze waarde te verlagen, kan uw functie-app sneller nieuwere moduleversies krijgen, maar het verhoogt ook het gebruik van app-bronnen (netwerk-I/O, CPU, opslag). Als u deze waarde verhoogt, vermindert u het resourcegebruik van de app, maar het kan ook het leveren van nieuwe moduleversies naar uw app vertragen. | 
| **`MDNewSnapshotCheckPeriod`**         | `01:00:00`(1 uur)       | Nadat nieuwe moduleversies in het bestandssysteem zijn geïnstalleerd, moet elk PowerShell-werkproces opnieuw worden gestart. PowerShell-werknemers opnieuw opstarten is van invloed op de beschikbaarheid van uw app, omdat deze de huidige functie-uitvoering kan onderbreken. Totdat alle PowerShell-werkprocessen opnieuw zijn gestart, kunnen functie-aanroepen de oude of de nieuwe moduleversies gebruiken. Alle PowerShell-werknemers opnieuw `MDNewSnapshotCheckPeriod`opstarten binnen . Het verhogen van deze waarde vermindert de frequentie van onderbrekingen, maar kan ook de periode verlengen waarin functie-aanroepen de oude of de nieuwe moduleversies niet-deterministisch gebruiken. |
| **`MDMinBackgroundUpgradePeriod`**      | `1.00:00:00`(1 dag)     | Om overmatige module-upgrades op frequente werknemer opnieuw opstarten te voorkomen, wordt niet gecontroleerd op `MDMinBackgroundUpgradePeriod`module-upgrades uitgevoerd wanneer een werknemer die controle al in de laatste heeft gestart. |

Gebruik te maken van uw eigen aangepaste modules is een beetje anders dan hoe je het normaal zou doen.

Op uw lokale computer wordt de module geïnstalleerd in een `$env:PSModulePath`van de wereldwijd beschikbare mappen in uw . Wanneer u in Azure wordt uitgevoerd, hebt u geen toegang tot de modules die op uw machine zijn geïnstalleerd. Dit betekent `$env:PSModulePath` dat de voor een PowerShell-functie-app verschilt van `$env:PSModulePath` in een gewoon PowerShell-script.

In Functies `PSModulePath` bevat het twee paden:

* Een `Modules` map die aan de basis van uw functie-app bestaat.
* Een pad `Modules` naar een map die wordt beheerd door de PowerShell-taalwerker.

### <a name="function-app-level-modules-folder"></a>Map op `Modules` functie-app-niveau

Als u aangepaste modules wilt gebruiken, u `Modules` modules plaatsen waarvan uw functies in een map zijn. Vanuit deze map zijn modules automatisch beschikbaar voor de runtime van de functies. Elke functie in de functie-app kan deze modules gebruiken. 

> [!NOTE]
> Modules die zijn opgegeven in het requirements.psd1-bestand worden automatisch gedownload en opgenomen in het pad, zodat u ze niet hoeft op te nemen in de modulesmap. Deze worden lokaal opgeslagen `$env:LOCALAPPDATA/AzureFunctions` in de `/data/ManagedDependencies` map en in de map wanneer deze in de cloud worden uitgevoerd.

Als u wilt profiteren van de `Modules` aangepaste modulefunctie, maakt u een map in de hoofdmap van uw functie-app. Kopieer de modules die u in uw functies wilt gebruiken naar deze locatie.

```powershell
mkdir ./Modules
Copy-Item -Path /mymodules/mycustommodule -Destination ./Modules -Recurse
```

Met `Modules` een map moet uw functie-app de volgende mapstructuur hebben:

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

Wanneer u de functie-app start, voegt `Modules` de `$env:PSModulePath` PowerShell-taalwerker deze map toe aan de module die automatisch wordt geladen, net zoals in een gewoon PowerShell-script.

### <a name="language-worker-level-modules-folder"></a>Map taalwerkniveau `Modules`

Verschillende modules worden vaak gebruikt door de PowerShell-taalwerker. Deze modules worden gedefinieerd in `PSModulePath`de laatste positie van . 

De huidige lijst van modules is als volgt:

* [Microsoft.PowerShell.Archive](https://www.powershellgallery.com/packages/Microsoft.PowerShell.Archive): module die wordt `.zip`gebruikt `.nupkg`voor het werken met archieven, zoals , en anderen.
* **ThreadJob**: Een thread-gebaseerde implementatie van de PowerShell job API's.

Standaard gebruikt Functions de meest recente versie van deze modules. Als u een specifieke moduleversie wilt `Modules` gebruiken, plaatst u die specifieke versie in de map van uw functie-app.

## <a name="environment-variables"></a>Omgevingsvariabelen

In Functies worden [app-instellingen](functions-app-settings.md), zoals serviceverbindingstekenreeksen, tijdens de uitvoering weergegeven als omgevingsvariabelen. U deze `$env:NAME_OF_ENV_VAR`instellingen openen via, zoals in het volgende voorbeeld wordt weergegeven:

```powershell
param($myTimer)

Write-Host "PowerShell timer trigger function ran! $(Get-Date)"
Write-Host $env:AzureWebJobsStorage
Write-Host $env:WEBSITE_SITE_NAME
```

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

Wanneer u lokaal wordt uitgevoerd, worden de app-instellingen gelezen in het projectbestand [local.settings.json.](functions-run-local.md#local-settings-file)

## <a name="concurrency"></a>Gelijktijdigheid

Standaard kan de PowerShell-runtime van functies slechts één aanroep van een functie tegelijk verwerken. Dit gelijktijdigheidsniveau is echter mogelijk niet voldoende in de volgende situaties:

* Wanneer u probeert om een groot aantal aanroepen op hetzelfde moment te behandelen.
* Wanneer u functies hebt die andere functies in dezelfde functie-app aanroepen.

U dit gedrag wijzigen door de volgende omgevingsvariabele in te stellen op een gehele waarde:

```
PSWorkerInProcConcurrencyUpperBound
```

U stelt deze omgevingsvariabele in in de [app-instellingen](functions-app-settings.md) van uw functie-app.

### <a name="considerations-for-using-concurrency"></a>Overwegingen voor het gebruik van gelijktijdigheid

PowerShell is standaard een _enkele scripttaal_ met één thread. Gelijktijdigheid kan echter worden toegevoegd door meerdere PowerShell-runspaces in hetzelfde proces te gebruiken. De hoeveelheid runspaces die is gemaakt, komt overeen met de psworkerinprocConcurrencyUpperBound-toepassingsinstelling. De doorvoer wordt beïnvloed door de hoeveelheid CPU en geheugen die beschikbaar is in het geselecteerde plan.

Azure PowerShell gebruikt een aantal contexten en statusen _op procesniveau_ om u te helpen voorkomen van overmatig typen. Als u echter gelijktijdigheid in uw functie-app inschakelt en acties aanroept die de status wijzigen, u uiteindelijk racevoorwaarden krijgen. Deze rasvoorwaarden zijn moeilijk te debuggen omdat één aanroep op een bepaalde staat vertrouwt en andere aanroep de staat veranderde.

Er is een enorme waarde in gelijktijdigheid met Azure PowerShell, omdat sommige bewerkingen een aanzienlijke hoeveelheid tijd in beslag kunnen nemen. U moet echter voorzichtig te werk gaan. Als u vermoedt dat u een racevoorwaarde ondervindt, stelt u de `1` psworkerInProcConcurrencyUpperBound-app-instelling in en gebruikt u in plaats daarvan [de isolatie van taalwerkprocesprocesniveau](functions-app-settings.md#functions_worker_process_count) voor gelijktijdigheid.

## <a name="configure-function-scriptfile"></a>Functie configureren`scriptFile`

Standaard wordt een PowerShell-functie `run.ps1`uitgevoerd vanuit een bestand dat dezelfde `function.json`bovenliggende map deelt als de bijbehorende .

De `scriptFile` eigenschap `function.json` in de kan worden gebruikt om een mapstructuur te krijgen die lijkt op het volgende voorbeeld:

```
FunctionApp
 | - host.json
 | - myFunction
 | | - function.json
 | - lib
 | | - PSFunction.ps1
```

In dit geval `function.json` `myFunction` bevat `scriptFile` de voor-een eigenschap die verwijst naar het bestand met de geëxporteerde functie die moet worden uitgevoerd.

```json
{
  "scriptFile": "../lib/PSFunction.ps1",
  "bindings": [
    // ...
  ]
}
```

## <a name="use-powershell-modules-by-configuring-an-entrypoint"></a>PowerShell-modules gebruiken door een entryPoint te configureren

In dit artikel worden PowerShell-functies weergegeven in het standaardscriptbestand `run.ps1` dat door de sjablonen wordt gegenereerd.
U uw functies echter ook opnemen in PowerShell-modules. U uw specifieke functiecode in `scriptFile` de `entryPoint` module verwijzen met behulp van de velden en velden in het configuratiebestand van function.json.

In dit `entryPoint` geval wordt de naam van een functie of cmdlet in de PowerShell-module waarnaar in `scriptFile`wordt verwezen .

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

In dit voorbeeld bevat `myFunction` de `scriptFile` configuratie voor `PSFunction.psm1`een eigenschap waarnaar verwijst , een PowerShell-module in een andere map.  De `entryPoint` eigenschap verwijst `Invoke-PSTestFunc` naar de functie, het ingangspunt in de module.

```json
{
  "scriptFile": "../lib/PSFunction.psm1",
  "entryPoint": "Invoke-PSTestFunc",
  "bindings": [
    // ...
  ]
}
```

Met deze configuratie, de `Invoke-PSTestFunc` wordt `run.ps1` uitgevoerd precies zoals een zou.

## <a name="considerations-for-powershell-functions"></a>Overwegingen voor PowerShell-functies

Wanneer u met PowerShell-functies werkt, moet u rekening houden met de overwegingen in de volgende secties.

### <a name="cold-start"></a>Koude start

Bij het ontwikkelen van Azure-functies in het [serverloze hostingmodel](functions-scale.md#consumption-plan)zijn koude starts een realiteit. *Koude start* verwijst naar de periode die nodig is voordat uw functie-app wordt uitgevoerd om een aanvraag te verwerken. Koude start gebeurt vaker in het verbruiksplan omdat uw functie-app wordt afgesloten tijdens perioden van inactiviteit.

### <a name="bundle-modules-instead-of-using-install-module"></a>Bundelmodules in plaats van het gebruik`Install-Module`

Je script wordt op elke aanroep uitgevoerd. Vermijd `Install-Module` het gebruik in uw script. Gebruik `Save-Module` in plaats daarvan voor publicatie, zodat uw functie geen tijd hoeft te verspillen aan het downloaden van de module. Als koude starts van invloed zijn op uw functies, u overwegen uw functie-app te implementeren op een [App Service-abonnement](functions-scale.md#app-service-plan) dat is ingesteld *op altijd op* of op een [Premium-abonnement.](functions-scale.md#premium-plan)

## <a name="next-steps"></a>Volgende stappen

Zie de volgende bronnen voor meer informatie:

* [Aanbevolen procedures voor Azure Functions](functions-best-practices.md)
* [Naslaginformatie over Azure Functions voor ontwikkelaars](functions-reference.md)
* [Azure Functions triggers en bindingen](functions-triggers-bindings.md)

[Naslaginformatie voor host.json]: functions-host-json.md
