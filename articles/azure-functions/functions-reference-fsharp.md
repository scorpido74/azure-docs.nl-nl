---
title: Naslaginformatie over Azure Functions F#-ontwikkelaars
description: Meer informatie over het ontwikkelen van Azure-functies met f#-script.
author: sylvanc
ms.assetid: e60226e5-2630-41d7-9e5b-9f9e5acc8e50
ms.topic: reference
ms.date: 10/09/2018
ms.author: syclebsc
ms.openlocfilehash: 669701f91ab28a4eb734b0346be6515dc44e8685
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276762"
---
# <a name="azure-functions-f-developer-reference"></a>Naslaginformatie over Azure Functions F# Developer

F# voor Azure Functions is een oplossing voor het eenvoudig uitvoeren van kleine stukjes code of 'functies' in de cloud. Gegevens stromen via functieargumenten in uw F#-functie binnen. Argumentnamen zijn `function.json`opgegeven in , en er zijn vooraf gedefinieerde namen voor toegang tot zaken als de functielogger en annuleringstokens. 

>[!IMPORTANT]
>F# script (.fsx) wordt alleen ondersteund door [versie 1.x](functions-versions.md#creating-1x-apps) van de runtime van Azure Functions. Als u F# wilt gebruiken met versie 2.x en latere versies van de runtime, moet u een vooraf gecompileerd F# klassebibliotheekproject (.fs) gebruiken. U maakt, beheert en publiceert een F#-klassebibliotheekproject met Visual Studio zoals u een [c#-klassebibliotheekproject](functions-dotnet-class-library.md)zou doen. Zie overzicht van Azure [Functions runtime-versies](functions-versions.md)voor meer informatie over versies van functies.

In dit artikel wordt ervan uitgegaan dat u de verwijzing naar de [ontwikkelaar van Azure-functies](functions-reference.md)al hebt gelezen.

## <a name="how-fsx-works"></a>Hoe .fsx werkt
Een `.fsx` bestand is een F#-script. Het kan worden gezien als een F# project dat is opgenomen in een enkel bestand. Het bestand bevat zowel de code voor uw programma (in dit geval uw Azure-functie) als richtlijnen voor het beheren van afhankelijkheden.

Wanneer u `.fsx` een Azure-functie voor een Azure-functie gebruikt, worden vaak vereiste verzamelingen automatisch voor u opgenomen, zodat u zich concentreren op de functie in plaats van op de code voor boilerplate.

## <a name="folder-structure"></a>Mapstructuur

De mapstructuur voor een F#-scriptproject ziet er als volgt uit:

```
FunctionsProject
 | - MyFirstFunction
 | | - run.fsx
 | | - function.json
 | | - function.proj
 | - MySecondFunction
 | | - run.fsx
 | | - function.json
 | | - function.proj
 | - host.json
 | - extensions.csproj
 | - bin
```

Er is een gedeeld [host.json-bestand](functions-host-json.md) dat kan worden gebruikt om de functie-app te configureren. Elke functie heeft zijn eigen codebestand (.fsx) en bindend configuratiebestand (function.json).

De bindende extensies die nodig zijn in [versie 2.x en latere versies](functions-versions.md) van de runtime functies worden gedefinieerd in het `extensions.csproj` bestand, met de werkelijke bibliotheekbestanden in de `bin` map. Wanneer u lokaal ontwikkelt, moet u [bindende extensies registreren.](./functions-bindings-register.md#extension-bundles) Bij het ontwikkelen van functies in de Azure-portal wordt deze registratie voor u uitgevoerd.

## <a name="binding-to-arguments"></a>Binding aan argumenten
Elke binding ondersteunt een aantal reeks argumenten, zoals beschreven in de [verwijzing naar azure-functies voor ontwikkelaars.](functions-triggers-bindings.md) Een van de argumentbindingen die een blobtrigger ondersteunt, is bijvoorbeeld een POCO, die kan worden uitgedrukt met behulp van een F#-record. Bijvoorbeeld:

```fsharp
type Item = { Id: string }

let Run(blob: string, output: byref<Item>) =
    let item = { Id = "Some ID" }
    output <- item
```

Uw F# Azure-functie heeft een of meer argumenten nodig. Wanneer we het hebben over argumenten voor Azure-functies, verwijzen we naar *invoerargumenten* en *uitvoerargumenten.* Een invoerargument is precies hoe het klinkt: invoer naar uw F# Azure-functie. Een *uitvoerargument* is veranderlijke `byref<>` gegevens of een argument dat dient als een manier om gegevens terug te geven *uit* uw functie.

In het bovenstaande `blob` voorbeeld is een `output` invoerargument en een uitvoerargument. Merk op `byref<>` dat `output` we gebruikt voor (er `[<Out>]` is geen noodzaak om de annotatie toe te voegen). Met `byref<>` een type kan uw functie wijzigen naar welk record of object het argument verwijst.

Wanneer een F#-record wordt gebruikt als invoertype, `[<CLIMutable>]` moet de recorddefinitie worden gemarkeerd met om het Azure-functieframework in staat te stellen de velden op de juiste manier in te stellen voordat de record wordt doorgespeeld op uw functie. Onder de `[<CLIMutable>]` motorkap, genereert setters voor de record eigenschappen. Bijvoorbeeld:

```fsharp
[<CLIMutable>]
type TestObject =
    { SenderName : string
      Greeting : string }

let Run(req: TestObject, log: ILogger) =
    { req with Greeting = sprintf "Hello, %s" req.SenderName }
```

Een F#-klasse kan ook worden gebruikt voor zowel in- als uitargumenten. Voor een klasse hebben eigenschappen meestal getters en setters nodig. Bijvoorbeeld:

```fsharp
type Item() =
    member val Id = "" with get,set
    member val Text = "" with get,set

let Run(input: string, item: byref<Item>) =
    let result = Item(Id = input, Text = "Hello from F#!")
    item <- result
```

## <a name="logging"></a>Logboekregistratie
Als u de uitvoer wilt registreren bij uw [streaminglogs](../app-service/troubleshoot-diagnostic-logs.md) in F#, moet uw functie een argument van het type [ILogger nemen.](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger) Voor consistentie raden we aan `log`dat dit argument de naam is . Bijvoorbeeld:

```fsharp
let Run(blob: string, output: byref<string>, log: ILogger) =
    log.LogInformation(sprintf "F# Azure Function processed a blob: %s" blob)
    output <- input
```

## <a name="async"></a>Async
De `async` werkstroom kan worden gebruikt, maar `Task`het resultaat moet een . Dit kan bijvoorbeeld `Async.StartAsTask`met:

```fsharp
let Run(req: HttpRequestMessage) =
    async {
        return new HttpResponseMessage(HttpStatusCode.OK)
    } |> Async.StartAsTask
```

## <a name="cancellation-token"></a>Token voor annulering
Als uw functie moet afsluiten sierlijk te behandelen, u het een [`CancellationToken`](/dotnet/api/system.threading.cancellationtoken) argument. Dit kan worden `async`gecombineerd met , bijvoorbeeld:

```fsharp
let Run(req: HttpRequestMessage, token: CancellationToken)
    let f = async {
        do! Async.Sleep(10)
        return new HttpResponseMessage(HttpStatusCode.OK)
    }
    Async.StartAsTask(f, token)
```

## <a name="importing-namespaces"></a>Naamruimten importeren
Naamruimten kunnen op de gebruikelijke manier worden geopend:

```fsharp
open System.Net
open System.Threading.Tasks
open Microsoft.Extensions.Logging

let Run(req: HttpRequestMessage, log: ILogger) =
    ...
```

De volgende naamruimten worden automatisch geopend:

* `System`
* `System.Collections.Generic`
* `System.IO`
* `System.Linq`
* `System.Net.Http`
* `System.Threading.Tasks`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`.

## <a name="referencing-external-assemblies"></a>Verwijzen naar externe samenstellingen
Evenzo kunnen verwijzingen naar de kaderassemblage aan de `#r "AssemblyName"` richtlijn worden toegevoegd.

```fsharp
#r "System.Web.Http"

open System.Net
open System.Net.Http
open System.Threading.Tasks
open Microsoft.Extensions.Logging

let Run(req: HttpRequestMessage, log: ILogger) =
    ...
```

De volgende samenstellingen worden automatisch toegevoegd door de hostingomgeving azure functions:

* `mscorlib`,
* `System`
* `System.Core`
* `System.Xml`
* `System.Net.Http`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`
* `Microsoft.Azure.WebJobs.Extensions`
* `System.Web.Http`
* `System.Net.Http.Formatting`.

Bovendien zijn de volgende samenstellingen speciaal voor de hand en kunnen zij `#r "AssemblyName"`worden aangeduid met een eenvoudige naam (bijv. ):

* `Newtonsoft.Json`
* `Microsoft.WindowsAzure.Storage`
* `Microsoft.ServiceBus`
* `Microsoft.AspNet.WebHooks.Receivers`
* `Microsoft.AspNEt.WebHooks.Common`.

Als u naar een privévergadering moet verwijzen, kunt `bin` u het assemblagebestand uploaden naar een map ten opzichte van uw functie en ernaar verwijzen met behulp van de bestandsnaam (bijv.  `#r "MyAssembly.dll"`). Zie het volgende gedeelte over pakketbeheer voor informatie over het uploaden van bestanden naar uw functiemap.

## <a name="editor-prelude"></a>Redacteur Prelude
Een editor die F# Compiler Services ondersteunt, is niet op de hoogte van de naamruimten en samenstellingen die Azure Functions automatisch bevat. Als zodanig kan het handig zijn om een prelude op te nemen die de editor helpt bij het vinden van de samenstellingen die u gebruikt, en om naamruimten expliciet te openen. Bijvoorbeeld:

```fsharp
#if !COMPILED
#I "../../bin/Binaries/WebJobs.Script.Host"
#r "Microsoft.Azure.WebJobs.Host.dll"
#endif

open System
open Microsoft.Azure.WebJobs.Host
open Microsoft.Extensions.Logging

let Run(blob: string, output: byref<string>, log: ILogger) =
    ...
```

Wanneer Azure Functions uw code uitvoert, `COMPILED` verwerkt deze de bron met gedefinieerd, zodat de editorprelude wordt genegeerd.

<a name="package"></a>

## <a name="package-management"></a>Pakketbeheer
Als u NuGet-pakketten wilt gebruiken `project.json` in een F#-functie, voegt u een bestand toe aan de map van de functie in het bestandssysteem van de functie-app. Hier is `project.json` een voorbeeldbestand dat een `Microsoft.ProjectOxford.Face` NuGet-pakketverwijzing naar versie 1.1.0 toevoegt:

```json
{
  "frameworks": {
    "net46":{
      "dependencies": {
        "Microsoft.ProjectOxford.Face": "1.1.0"
      }
    }
   }
}
```

Alleen het .NET Framework 4.6 wordt ondersteund, `project.json` dus `net46` zorg ervoor dat uw bestand aangeeft zoals hier wordt weergegeven.

Wanneer u `project.json` een bestand uploadt, krijgt de runtime de pakketten en voegt automatisch verwijzingen toe naar de pakketsamenstellingen. U hoeft geen richtlijnen `#r "AssemblyName"` toe te voegen. Voeg gewoon `open` de vereiste `.fsx` instructies toe aan uw bestand.

U automatisch referenties in uw editor prelude, om uw editor interactie met F # Compile Services te verbeteren.

### <a name="how-to-add-a-projectjson-file-to-your-azure-function"></a>Een `project.json` bestand toevoegen aan uw Azure-functie
1. Begin met ervoor te zorgen dat uw functie-app wordt uitgevoerd, wat u doen door uw functie te openen in de Azure-portal. Dit geeft ook toegang tot de streaming logs waar pakket installatie output zal worden weergegeven.
2. Als u `project.json` een bestand wilt uploaden, gebruikt u een van de methoden die zijn beschreven in [het bijwerken van functie-app-bestanden.](functions-reference.md#fileupdate) Als u [Continue implementatie voor Azure-functies](functions-continuous-deployment.md) `project.json` gebruikt, u een bestand toevoegen aan uw faseringsbranch om ermee te experimenteren voordat u het toevoegt aan uw implementatiebranch.
3. Nadat `project.json` het bestand is toegevoegd, ziet u uitvoer die vergelijkbaar is met het volgende voorbeeld in het streaminglogboek van uw functie:

```
2016-04-04T19:02:48.745 Restoring packages.
2016-04-04T19:02:48.745 Starting NuGet restore
2016-04-04T19:02:50.183 MSBuild auto-detection: using msbuild version '14.0' from 'D:\Program Files (x86)\MSBuild\14.0\bin'.
2016-04-04T19:02:50.261 Feeds used:
2016-04-04T19:02:50.261 C:\DWASFiles\Sites\facavalfunctest\LocalAppData\NuGet\Cache
2016-04-04T19:02:50.261 https://api.nuget.org/v3/index.json
2016-04-04T19:02:50.261
2016-04-04T19:02:50.511 Restoring packages for D:\home\site\wwwroot\HttpTriggerCSharp1\Project.json...
2016-04-04T19:02:52.800 Installing Newtonsoft.Json 6.0.8.
2016-04-04T19:02:52.800 Installing Microsoft.ProjectOxford.Face 1.1.0.
2016-04-04T19:02:57.095 All packages are compatible with .NETFramework,Version=v4.6.
2016-04-04T19:02:57.189
2016-04-04T19:02:57.189
2016-04-04T19:02:57.455 Packages restored.
```

## <a name="environment-variables"></a>Omgevingsvariabelen
`System.Environment.GetEnvironmentVariable`Gebruik bijvoorbeeld:

```fsharp
open System.Environment
open Microsoft.Extensions.Logging

let Run(timer: TimerInfo, log: ILogger) =
    log.LogInformation("Storage = " + GetEnvironmentVariable("AzureWebJobsStorage"))
    log.LogInformation("Site = " + GetEnvironmentVariable("WEBSITE_SITE_NAME"))
```

## <a name="reusing-fsx-code"></a>.fsx-code opnieuw gebruiken
U code `.fsx` uit andere `#load` bestanden gebruiken met behulp van een richtlijn. Bijvoorbeeld:

`run.fsx`

```fsharp
#load "logger.fsx"

let Run(timer: TimerInfo, log: ILogger) =
    mylog log (sprintf "Timer: %s" DateTime.Now.ToString())
```

`logger.fsx`

```fsharp
let mylog(log: ILogger, text: string) =
    log.LogInformation(text);
```

Paden die `#load` aan de richtlijn voldoen, `.fsx` zijn relatief ten opzichte van de locatie van uw bestand.

* `#load "logger.fsx"`laadt een bestand in de functiemap.
* `#load "package\logger.fsx"`hiermee wordt een `package` bestand in de map in de functiemap geladen.
* `#load "..\shared\mylogger.fsx"`laadt een bestand `shared` in de map op hetzelfde niveau als `wwwroot`de functiemap, dat wil zeggen direct onder .

De `#load` richtlijn werkt `.fsx` alleen met (F# script) bestanden, en niet met `.fs` bestanden.

## <a name="next-steps"></a>Volgende stappen
Zie de volgende bronnen voor meer informatie:

* [F# Gids](/dotnet/articles/fsharp/index)
* [Aanbevolen procedures voor Azure-functies](functions-best-practices.md)
* [Naslaginformatie over Azure Functions voor ontwikkelaars](functions-reference.md)
* [Azure Functions triggers en bindingen](functions-triggers-bindings.md)
* [Azure-functies testen](functions-test-a-function.md)
* [Azure-functies schalen](functions-scale.md)

