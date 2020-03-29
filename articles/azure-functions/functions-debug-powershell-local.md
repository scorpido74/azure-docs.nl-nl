---
title: PowerShell Azure-functies opsporen
description: Begrijp hoe u functies ontwikkelen met PowerShell.
author: tylerleonhardt
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: tyleonha
ms.reviewer: glenga
ms.openlocfilehash: 133e89bd9187ae5e48fa208b407678760d31adfd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78163757"
---
# <a name="debug-powershell-azure-functions-locally"></a>PowerShell Azure-functies opsporen

Met Azure Functions u uw functies ontwikkelen als PowerShell-scripts.

U uw PowerShell-functies lokaal debuggen, net als bij powershell-scripts met behulp van de volgende standaardhulpprogramma's voor ontwikkeling:

* [Visual Studio Code](https://code.visualstudio.com/): Microsoft's gratis, lichtgewicht en open-source teksteditor met de PowerShell-extensie die een volledige PowerShell-ontwikkelervaring biedt.
* Een PowerShell-console: Foutopsporing met dezelfde opdrachten die u zou gebruiken om elk ander PowerShell-proces te debuggen.

[Azure Functions Core Tools](functions-run-local.md) ondersteunt lokale foutopsporing van Azure-functies, waaronder PowerShell-functies.

## <a name="example-function-app"></a>Voorbeeldfunctie-app

De functie-app die in dit artikel wordt gebruikt, heeft één HTTP-geactiveerde functie en heeft de volgende bestanden:

```
PSFunctionApp
 | - HttpTriggerFunction
 | | - run.ps1
 | | - function.json
 | - local.settings.json
 | - host.json
 | - profile.ps1
```

Deze functie-app is vergelijkbaar met de app die u krijgt wanneer u de [PowerShell-quickstart voltooit.](functions-create-first-function-powershell.md)

De functiecode `run.ps1` in ziet eruit als het volgende script:

```powershell
param($Request)

$name = $Request.Query.Name

if($name) {
    $status = 200
    $body = "Hello $name"
}
else {
    $status = 400
    $body = "Please pass a name on the query string or in the request body."
}

Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{
    StatusCode = $status
    Body = $body
})
```

## <a name="set-the-attach-point"></a>Het aankoppelpunt instellen

Als u een PowerShell-functie wilt debuggen, moet de functie stoppen om de foutopsporing te kunnen bevestigen. De `Wait-Debugger` cmdlet stopt de uitvoering en wacht op de foutopsporing.

Het enige wat u hoeft te `Wait-Debugger` doen is een `if` oproep toe te voegen aan de cmdlet net boven de instructie, als volgt:

```powershell
param($Request)

$name = $Request.Query.Name

# This is where we will wait for the debugger to attach
Wait-Debugger

if($name) {
    $status = 200
    $body = "Hello $name"
}
# ...
```

Foutopsporing begint bij `if` de instructie. 

Met `Wait-Debugger` op zijn plaats u nu de functies debuggen met behulp van Visual Studio Code of een PowerShell-console.

## <a name="debug-in-visual-studio-code"></a>Foutopsporing in Visual Studio-code

Als u uw PowerShell-functies in Visual Studio Code wilt debuggen, moet u het volgende hebben geïnstalleerd:

* [PowerShell-extensie voor Visual Studio Code](/powershell/scripting/components/vscode/using-vscode)
* [Azure Functions extension for Visual Studio Code](functions-create-first-function-vs-code.md) (Azure Functions-extensie voor Visual Studio Code)
* [PowerShell Core 6.2 of hoger](/powershell/scripting/install/installing-powershell-core-on-windows)

Nadat u deze afhankelijkheden hebt geïnstalleerd, laadt u een bestaand PowerShell-functieproject of [maakt u uw eerste PowerShell-functieproject.](functions-create-first-function-powershell.md)

>[!NOTE]
> Als uw project niet over de benodigde configuratiebestanden beschikt, wordt u gevraagd deze toe te voegen.

### <a name="set-the-powershell-version"></a>De PowerShell-versie instellen

PowerShell Core wordt naast Windows PowerShell geïnstalleerd. Stel PowerShell Core in als de PowerShell-versie die kan worden gebruikt met de PowerShell-extensie voor Visual Studio Code.

1. Druk op F1 om de opdrachtpallet weer te geven en zoek `Session`vervolgens naar .

1. Kies **PowerShell: Menu Sessie weergeven**.

1. Als uw **huidige sessie** niet **PowerShell Core 6**is, kiest u **Overschakelen naar: PowerShell Core 6**.

Wanneer u een PowerShell-bestand hebt geopend, ziet u de versie die rechtsonder in het venster in het groen wordt weergegeven. Als u deze tekst selecteert, wordt ook het sessiemenu weergegeven. Zie Voor meer informatie de [versie kiezen van PowerShell die u wilt gebruiken met de extensie.](/powershell/scripting/components/vscode/using-vscode#choosing-a-version-of-powershell-to-use-with-the-extension)

### <a name="start-the-function-app"></a>De functie-app starten

Controleer `Wait-Debugger` of dit is ingesteld in de functie waar u de foutopsporing wilt koppelen.  Met `Wait-Debugger` toegevoegd kun je je functie-app debuggen met Visual Studio Code.

Kies het deelvenster **Foutopsporing** en voeg **toe aan de functie PowerShell**.

![Debugger](https://user-images.githubusercontent.com/2644648/56166073-8a7b3780-5f89-11e9-85ce-36ed38e221a2.png)

U ook op de F5-toets drukken om te beginnen met foutopsporing.

De foutopsporingsbewerking van start doet de volgende taken:

* Wordt `func extensions install` uitgevoerd in de terminal om azure-functies-extensies te installeren die vereist zijn door uw functie-app.
* Wordt `func host start` uitgevoerd in de terminal om de functie-app te starten in de host functies.
* Koppel de PowerShell-foutopsporing aan de PowerShell-runspace in de runtime van de functies.

>[!NOTE]
> U moet ervoor zorgen dat PSWorkerInProcConcurrencyUpperBound is ingesteld op 1 om de juiste foutopsporingservaring in Visual Studio Code te garanderen. Dit is de standaardinstelling.

Terwijl de functie-app wordt uitgevoerd, hebt u een aparte PowerShell-console nodig om de HTTP-geactiveerde functie aan te roepen.

In dit geval is de PowerShell-console de client. Het `Invoke-RestMethod` wordt gebruikt om de functie te activeren.

Voer in een PowerShell-console de volgende opdracht uit:

```powershell
Invoke-RestMethod "http://localhost:7071/api/HttpTrigger?Name=Functions"
```

U zult merken dat een antwoord niet onmiddellijk wordt geretourneerd. Dat komt `Wait-Debugger` omdat de debugger en PowerShell uitvoering ging in break mode zo snel als het kon heeft bevestigd. Dit komt door het [BreakAll concept](#breakall-might-cause-your-debugger-to-break-in-an-unexpected-place), dat later wordt uitgelegd. Nadat u `continue` op de knop hebt gedrukt, breekt `Wait-Debugger`de foutopsporing nu direct na .

Op dit punt is de foutopsporing gekoppeld en u alle normale foutopsporingsbewerkingen uitvoeren. Zie [de officiële documentatie](https://code.visualstudio.com/Docs/editor/debugging#_debug-actions)voor meer informatie over het gebruik van de foutopsporing in Visual Studio Code.

Nadat u uw script hebt aangenomen en volledig hebt aangenomen, zult u merken dat:

* De PowerShell-console `Invoke-RestMethod` die de ed heeft een resultaat geretourneerd
* De PowerShell Integrated Console in Visual Studio Code wacht op een script dat wordt uitgevoerd

Wanneer u later dezelfde functie aanroept, wordt de foutopsporing in de PowerShell-extensie direct na de `Wait-Debugger`.

## <a name="debugging-in-a-powershell-console"></a>Foutopsporing in een PowerShell-console

>[!NOTE]
> In deze sectie wordt ervan uitgegaan dat u de documenten `func host start` Azure Functions Core [Tools](functions-run-local.md) hebt gelezen en weet hoe u de opdracht moet gebruiken om uw functie-app te starten.

Open een console `cd` in de map van uw functie-app en voer de volgende opdracht uit:

```sh
func host start
```

Met de functie-app `Wait-Debugger` draait en de op zijn plaats, u koppelen aan het proces. Je hebt nog twee PowerShell consoles nodig.

Een van de consoles fungeert als de klant. Hieruit belt `Invoke-RestMethod` u om de functie te activeren. U bijvoorbeeld de volgende opdracht uitvoeren:

```powershell
Invoke-RestMethod "http://localhost:7071/api/HttpTrigger?Name=Functions"
```

U zult merken dat het geen antwoord retourneert, `Wait-Debugger`wat een gevolg is van de . De PowerShell runspace wacht nu op een debugger worden bevestigd. Laten we dat bevestigen.

Voer in de andere PowerShell-console de volgende opdracht uit:

```powershell
Get-PSHostProcessInfo
```

Met deze cmdlet wordt een tabel geretourneerd die op de volgende uitvoer lijkt:

```output
ProcessName ProcessId AppDomainName
----------- --------- -------------
dotnet          49988 None
pwsh            43796 None
pwsh            49970 None
pwsh             3533 None
pwsh            79544 None
pwsh            34881 None
pwsh            32071 None
pwsh            88785 None
```

Noteer de `ProcessId` voor het item in `ProcessName` `dotnet`de tabel met de als . Dit proces is uw functie-app.

Voer vervolgens het volgende fragment uit:

```powershell
# This enters into the Azure Functions PowerShell process.
# Put your value of `ProcessId` here.
Enter-PSHostProcess -Id $ProcessId

# This triggers the debugger.
Debug-Runspace 1
```

Eenmaal gestart, de foutopsporing breekt en toont iets als de volgende uitvoer:

```
Debugging Runspace: Runspace1

To end the debugging session type the 'Detach' command at the debugger prompt, or type 'Ctrl+C' otherwise.

At /Path/To/PSFunctionApp/HttpTriggerFunction/run.ps1:13 char:1
+ if($name) { ...
+ ~~~~~~~~~~~
[DBG]: [Process:49988]: [Runspace1]: PS /Path/To/PSFunctionApp>>
```

Op dit moment wordt u gestopt bij een breekpunt in de [PowerShell-foutopsporing.](/powershell/module/microsoft.powershell.core/about/about_debuggers) Vanaf hier u alle gebruikelijke foutopsporingsbewerkingen uitvoeren, stap over, stap in, verder, stoppen en anderen. Voer de `h` opdrachten uit `?` om de volledige set foutopsporingsopdrachten in de console weer te geven.

U ook breekpunten op `Set-PSBreakpoint` dit niveau instellen met de cmdlet.

Zodra u verder gaat en uw script volledig aanroept, zult u merken dat:

* De PowerShell-console waar `Invoke-RestMethod` u hebt uitgevoerd, heeft nu een resultaat geretourneerd.
* De PowerShell-console waar `Debug-Runspace` u hebt uitgevoerd, wacht op een script dat wordt uitgevoerd.

U dezelfde functie opnieuw `Invoke-RestMethod` aanroepen (bijvoorbeeld met behulp van) en de foutopsporing breekt direct na de `Wait-Debugger` opdracht in.

## <a name="considerations-for-debugging"></a>Overwegingen voor foutopsporing

Houd rekening met de volgende problemen bij het debuggen van uw functiecode.

### <a name="breakall-might-cause-your-debugger-to-break-in-an-unexpected-place"></a>`BreakAll`kan ervoor zorgen dat uw foutopsporing op een onverwachte plaats breekt

De PowerShell-extensie gebruikt `Debug-Runspace`, die op zijn `BreakAll` beurt is gebaseerd op de functie van PowerShell. Met deze functie moet PowerShell stoppen bij de eerste opdracht die wordt uitgevoerd. Dit gedrag geeft u de mogelijkheid om breekpunten in te stellen binnen de gedebugged runspace.

De runtime van Azure Functions voert een `run.ps1` paar opdrachten uit voordat u een beroep doet `Microsoft.Azure.Functions.PowerShellWorker.psm1` op `Microsoft.Azure.Functions.PowerShellWorker.psd1`uw script, dus het is mogelijk dat de foutopsporing binnen de of .

Mocht dit einde gebeuren, voer de `continue` opdracht of `c` opdracht om over te slaan dit breekpunt. Je stopt dan bij het verwachte breakpoint.

## <a name="next-steps"></a>Volgende stappen

Zie [Azure Functions PowerShell-ontwikkelaarshandleiding](functions-reference-powershell.md)voor meer informatie over het ontwikkelen van functies met PowerShell.
