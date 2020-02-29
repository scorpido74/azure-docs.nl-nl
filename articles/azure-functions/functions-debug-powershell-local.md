---
title: Power shell-Azure Functions lokaal debuggen
description: Meer informatie over het ontwikkelen van functies met behulp van Power shell.
author: tylerleonhardt
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: tyleonha
ms.reviewer: glenga
ms.openlocfilehash: 133e89bd9187ae5e48fa208b407678760d31adfd
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/28/2020
ms.locfileid: "78163757"
---
# <a name="debug-powershell-azure-functions-locally"></a>Power shell-Azure Functions lokaal debuggen

Met Azure Functions kunt u uw functies ontwikkelen als Power shell-scripts.

U kunt fouten opsporen in uw Power shell-functies, lokaal als u Power shell-scripts gebruikt, met de volgende standaard ontwikkelingsprogram ma's:

* [Visual Studio code](https://code.visualstudio.com/): de gratis, licht gewichts-en open-source tekst editor van micro soft met de Power shell-extensie die een volledige Power shell-ontwikkelings ervaring biedt.
* Een Power shell-console: fouten opsporen met dezelfde opdrachten die u zou gebruiken voor het opsporen van fouten in andere Power shell-processen.

[Azure functions core tools](functions-run-local.md) ondersteunt lokale fout opsporing van Azure functions, inclusief Power shell-functies.

## <a name="example-function-app"></a>Voor beeld van functie-app

De functie-app die in dit artikel wordt gebruikt, heeft één door HTTP geactiveerde functie en heeft de volgende bestanden:

```
PSFunctionApp
 | - HttpTriggerFunction
 | | - run.ps1
 | | - function.json
 | - local.settings.json
 | - host.json
 | - profile.ps1
```

Deze functie-app is vergelijkbaar met de toepassing die u krijgt wanneer u de [Power shell-Snelstartgids](functions-create-first-function-powershell.md)voltooit.

De functie code in `run.ps1` ziet eruit als in het volgende script:

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

## <a name="set-the-attach-point"></a>Het koppel punt instellen

Voor het opsporen van fouten in een Power shell-functie moet de functie worden gestopt om het fout opsporingsprogramma te koppelen. De `Wait-Debugger`-cmdlet stopt de uitvoering en wacht op het fout opsporingsprogramma.

U hoeft alleen maar op de volgende manier een aanroep toe te voegen aan de cmdlet `Wait-Debugger` boven de `if`-instructie:

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

Fout opsporing begint bij de `if`-instructie. 

Met `Wait-Debugger` op locatie kunt u nu fouten opsporen in de functies met behulp van Visual Studio code of een Power shell-console.

## <a name="debug-in-visual-studio-code"></a>Fout opsporing in Visual Studio code

Als u fouten wilt opsporen in uw Power shell-functies in Visual Studio code, moet u het volgende hebben geïnstalleerd:

* [Power shell-extensie voor Visual Studio code](/powershell/scripting/components/vscode/using-vscode)
* [Azure Functions extension for Visual Studio Code](functions-create-first-function-vs-code.md) (Azure Functions-extensie voor Visual Studio Code)
* [Power shell Core 6,2 of hoger](/powershell/scripting/install/installing-powershell-core-on-windows)

Na het installeren van deze afhankelijkheden, laadt u een bestaand Power shell functions-project of [maakt u uw eerste Power shell functions-project](functions-create-first-function-powershell.md).

>[!NOTE]
> Als uw project niet de benodigde configuratie bestanden heeft, wordt u gevraagd deze toe te voegen.

### <a name="set-the-powershell-version"></a>De Power shell-versie instellen

Power shell core wordt naast Windows Power shell geïnstalleerd. Stel Power shell core in als de Power shell-versie die moet worden gebruikt met de Power shell-extensie voor Visual Studio code.

1. Druk op F1 om de opdracht pallet weer te geven en zoek vervolgens naar `Session`.

1. Kies **Power shell: menu sessie weer geven**.

1. Als uw **huidige sessie** niet **Power shell Core 6**is, kiest **u overschakelen naar: Power shell Core 6**.

Wanneer u een Power shell-bestand hebt geopend, ziet u de versie die in het groen wordt weer gegeven aan de rechter kant van het venster. Als u deze tekst selecteert, wordt ook het menu sessie weer gegeven. Zie [een versie van Power shell kiezen die u wilt gebruiken met de extensie](/powershell/scripting/components/vscode/using-vscode#choosing-a-version-of-powershell-to-use-with-the-extension)voor meer informatie.

### <a name="start-the-function-app"></a>De functie-app starten

Controleer of `Wait-Debugger` is ingesteld in de functie waaraan u het fout opsporingsprogramma wilt koppelen.  Met `Wait-Debugger` toegevoegd, kunt u fouten opsporen in uw functie-app met Visual Studio code.

Kies het deel venster **fout opsporing** en **Voeg vervolgens toe aan de Power shell-functie**.

![foutopsporingsversie](https://user-images.githubusercontent.com/2644648/56166073-8a7b3780-5f89-11e9-85ce-36ed38e221a2.png)

U kunt ook op de toets F5 drukken om de fout opsporing te starten.

Met de bewerking voor het starten van fout opsporing worden de volgende taken uitgevoerd:

* Voert `func extensions install` uit in de terminal om Azure Functions extensies te installeren die vereist zijn voor uw functie-app.
* Hiermee wordt `func host start` in de Terminal uitgevoerd om de functie-app in de functions-host te starten.
* Koppel de Power Shell-fout opsporing aan de Power shell-runs Pace in de functions-runtime.

>[!NOTE]
> U moet ervoor zorgen dat PSWorkerInProcConcurrencyUpperBound is ingesteld op 1 om ervoor te zorgen dat de juiste ervaring voor fout opsporing in Visual Studio code wordt gegarandeerd. Dit is de standaard instelling.

Als uw functie-app wordt uitgevoerd, hebt u een afzonderlijke Power shell-console nodig om de door HTTP geactiveerde functie aan te roepen.

In dit geval is de Power shell-console de client. De `Invoke-RestMethod` wordt gebruikt om de functie te activeren.

Voer de volgende opdracht uit in een Power shell-console:

```powershell
Invoke-RestMethod "http://localhost:7071/api/HttpTrigger?Name=Functions"
```

U ziet dat een antwoord niet onmiddellijk wordt geretourneerd. Dat komt omdat `Wait-Debugger` de fout opsporing en Power shell-uitvoering heeft bijgevoegd zodra het probleem is opgetreden. Dit komt door het [BreakAll-concept](#breakall-might-cause-your-debugger-to-break-in-an-unexpected-place), dat later wordt uitgelegd. Nadat u op de knop `continue` hebt geklikt, wordt het fout opsporingsprogramma nu na `Wait-Debugger`op de regel afgebroken.

Op dit moment is het fout opsporingsprogramma gekoppeld en kunt u alle normale debugging bewerkingen uitvoeren. Zie [de officiële documentatie](https://code.visualstudio.com/Docs/editor/debugging#_debug-actions)voor meer informatie over het gebruik van het fout opsporingsprogramma in Visual Studio code.

Nadat u het script hebt voortgezet en volledig hebt aangeroepen, ziet u dat:

* De Power shell-console die de `Invoke-RestMethod` heeft heeft een resultaat geretourneerd
* De geïntegreerde Power shell-console in Visual Studio code wacht tot een script wordt uitgevoerd

Later wanneer u dezelfde functie aanroept, wordt het fout opsporingsprogramma in Power shell-uitbrei ding direct na de `Wait-Debugger`.

## <a name="debugging-in-a-powershell-console"></a>Fout opsporing in een Power shell-console

>[!NOTE]
> In deze sectie wordt ervan uitgegaan dat u de [Azure functions core tools docs](functions-run-local.md) hebt gelezen en hoe u de `func host start` opdracht kunt gebruiken om de functie-app te starten.

Open een-console, `cd` in de map van uw functie-app en voer de volgende opdracht uit:

```sh
func host start
```

Met de functie-app die wordt uitgevoerd en de `Wait-Debugger` op locatie, kunt u aan het proces koppelen. U hebt nog twee Power shell-consoles nodig.

Een van de-consoles fungeert als de client. Hier roept u `Invoke-RestMethod` aan om de functie te activeren. U kunt bijvoorbeeld de volgende opdracht uitvoeren:

```powershell
Invoke-RestMethod "http://localhost:7071/api/HttpTrigger?Name=Functions"
```

U ziet dat er geen antwoord wordt geretourneerd. Dit is een resultaat van de `Wait-Debugger`. De Power shell-runs Pace wacht op het toevoegen van een fout opsporingsprogramma. Laten we dat doen.

Voer in de andere Power shell-console de volgende opdracht uit:

```powershell
Get-PSHostProcessInfo
```

Met deze cmdlet wordt een tabel geretourneerd die eruitziet als de volgende uitvoer:

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

Noteer de `ProcessId` voor het item in de tabel met de `ProcessName` als `dotnet`. Dit proces is uw functie-app.

Voer vervolgens het volgende code fragment uit:

```powershell
# This enters into the Azure Functions PowerShell process.
# Put your value of `ProcessId` here.
Enter-PSHostProcess -Id $ProcessId

# This triggers the debugger.
Debug-Runspace 1
```

Zodra het fout opsporingsprogramma is gestart, wordt het debugger afgebroken en ziet u een item zoals in de volgende uitvoer:

```
Debugging Runspace: Runspace1

To end the debugging session type the 'Detach' command at the debugger prompt, or type 'Ctrl+C' otherwise.

At /Path/To/PSFunctionApp/HttpTriggerFunction/run.ps1:13 char:1
+ if($name) { ...
+ ~~~~~~~~~~~
[DBG]: [Process:49988]: [Runspace1]: PS /Path/To/PSFunctionApp>>
```

Op dit moment wordt u gestopt op een onderbrekings punt in het [Power Shell-fout opsporingsprogramma](/powershell/module/microsoft.powershell.core/about/about_debuggers). Hier kunt u alle gebruikelijke probleemoplossings bewerkingen uitvoeren, de stap over, Step Into, door gaan, afsluiten en anderen. Als u de volledige set opdrachten voor fout opsporing wilt zien die beschikbaar zijn in de-console, voert u de opdrachten `h` of `?` uit.

U kunt onderbrekings punten op dit niveau ook instellen met de cmdlet `Set-PSBreakpoint`.

Nadat u het script hebt door lopen en volledig hebt aangeroepen, ziet u het volgende:

* De Power shell-console waar u `Invoke-RestMethod` hebt uitgevoerd, heeft nu een resultaat geretourneerd.
* De Power shell-console waarin u `Debug-Runspace` hebt uitgevoerd, wacht tot een script is uitgevoerd.

U kunt dezelfde functie opnieuw aanroepen (met behulp van `Invoke-RestMethod` bijvoorbeeld) en het fout opsporingsprogramma wordt direct na de `Wait-Debugger` opdracht afgebroken.

## <a name="considerations-for-debugging"></a>Overwegingen voor fout opsporing

Houd de volgende problemen bij het opsporen van fouten in uw functions-code.

### <a name="breakall-might-cause-your-debugger-to-break-in-an-unexpected-place"></a>`BreakAll` kan ertoe leiden dat uw fout opsporingsprogramma op een onverwachte plaats breekt

De Power shell-extensie maakt gebruik van `Debug-Runspace`, die op zijn beurt afhankelijk is van de `BreakAll` functie van Power shell. Deze functie geeft aan dat Power shell stopt bij de eerste opdracht die wordt uitgevoerd. Dit gedrag biedt u de mogelijkheid om onderbrekings punten in te stellen in de runs Pace met fout opsporing.

De Azure Functions runtime voert enkele opdrachten uit voordat u uw `run.ps1` script daad werkelijk aanroept. het is dus mogelijk dat het fout opsporingsprogramma eindigt op de `Microsoft.Azure.Functions.PowerShellWorker.psm1` of de `Microsoft.Azure.Functions.PowerShellWorker.psd1`.

Als deze onderbreking plaatsvindt, voert u de `continue` of `c` opdracht uit om dit onderbrekings punt over te slaan. Vervolgens stopt u bij het verwachte onderbrekings punt.

## <a name="next-steps"></a>Volgende stappen

Zie [Azure functions Power shell-ontwikkelaars handleiding](functions-reference-powershell.md)voor meer informatie over het ontwikkelen van functies met behulp van Power shell.
