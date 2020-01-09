---
title: Power shell-werk stroom voor Azure Automation leren
description: Dit artikel is bedoeld als snelle les voor auteurs die bekend zijn met Power shell om inzicht te krijgen in de specifieke verschillen tussen Power shell en Power shell workflow en concepten die van toepassing zijn op Automation-runbooks.
services: automation
ms.subservice: process-automation
ms.date: 12/14/2018
ms.topic: conceptual
ms.openlocfilehash: 6e4c8057322b6208ea3b447b264e2bde1344540c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75421557"
---
# <a name="learning-key-windows-powershell-workflow-concepts-for-automation-runbooks"></a>Hoofd concepten van Windows Power shell-werk stromen voor Automation-runbooks

Runbooks in Azure Automation worden geïmplementeerd als Windows Power shell-werk stromen.  Een Windows Power shell-werk stroom is vergelijkbaar met een Windows Power shell-script, maar heeft een aantal belang rijke verschillen die verwarrend kunnen zijn voor een nieuwe gebruiker.  Hoewel dit artikel is bedoeld om u te helpen bij het schrijven van runbooks met behulp van Power shell workflow, raden we u aan runbooks te schrijven met Power shell tenzij u controle punten nodig hebt.  Er zijn verschillende syntaxis verschillen bij het ontwerpen van Power shell workflow-runbooks en deze verschillen vereisen wat meer werk om effectief werk stromen te schrijven.

Een werkstroom is een opeenvolging van geprogrammeerde, verbonden stappen waarmee langlopende taken wordt uitgevoerd of de coördinatie vereisen van meerdere stappen op meerdere apparaten of beheerde knooppunten. De voordelen van een werkstroom op een normaal script omvatten de mogelijkheid een actie tegen meerdere apparaten tegelijkertijd uitvoeren en de mogelijkheid voor het automatisch herstellen van fouten. Een Windows Power shell-werk stroom is een Windows Power shell-script dat gebruikmaakt van Windows Workflow Foundation. Terwijl de werkstroom is geschreven met Windows PowerShell-syntaxis en gelanceerd door Windows PowerShell, wordt verwerkt door Windows Workflow Foundation.

Zie aan de slag [met Windows Power shell-werk stroom](https://technet.microsoft.com/library/jj134242.aspx)voor meer informatie over de onderwerpen in dit artikel.

## <a name="basic-structure-of-a-workflow"></a>Basis structuur van een werk stroom

De eerste stap voor het converteren van een Power shell-script naar een Power shell-werk stroom is het insluiten van het sleutel woord van de **werk stroom** .  Een werk stroom begint met het tref woord **workflow** , gevolgd door de hoofd tekst van het script tussen accolades. De naam van de werk stroom volgt het **werk stroom** sleutelwoord, zoals wordt weer gegeven in de volgende syntaxis:

```powershell
Workflow Test-Workflow
{
    <Commands>
}
```

De naam van de werkstroom moet overeenkomen met de naam van het Automation-runbook. Als het runbook wordt geïmporteerd, moet de bestands naam overeenkomen met de naam van de werk stroom en moet eindigen op *. ps1*.

Als u para meters wilt toevoegen aan de werk stroom, gebruikt u het sleutel woord **param** net zoals u zou doen met een script.

## <a name="code-changes"></a>Codewijzigingen

Power shell-werk stroom code lijkt bijna identiek te zijn met Power shell-script code, met uitzonde ring van enkele belang rijke wijzigingen.  In de volgende secties wordt beschreven welke wijzigingen u moet aanbrengen in een Power shell-script om deze in een werk stroom uit te voeren.

### <a name="activities"></a>Activiteiten

Een activiteit is een specifieke taak in een werkstroom. Net zoals een script van een of meer opdrachten bestaat, wordt een werkstroom bestaat uit een of meer activiteiten die worden uitgevoerd in een reeks. Windows PowerShell-werkstroom converteert automatisch veel van de Windows PowerShell-cmdlets voor activiteiten wanneer deze een werkstroom wordt uitgevoerd. Wanneer u een van deze cmdlets in uw runbook opgeeft, wordt de bijbehorende activiteit uitgevoerd door Windows Workflow Foundation. Voor die cmdlets zonder een bijbehorende activiteit, voert Windows PowerShell-werkstroom automatisch de cmdlet uit binnen een [InlineScript](#inlinescript) activiteit. Er is een set van cmdlets die zijn uitgesloten en niet kan worden gebruikt in een werkstroom, tenzij u ze expliciet opneemt in een InlineScript blok. Zie voor meer informatie over deze concepten [met behulp van activiteiten in Script Workflows](https://technet.microsoft.com/library/jj574194.aspx).

Werkstroomactiviteiten delen een aantal gemeenschappelijke parameters configureren hun werking. Zie voor meer informatie over de algemene werkstroomparameters [about_WorkflowCommonParameters](https://technet.microsoft.com/library/jj129719.aspx).

### <a name="positional-parameters"></a>Positionele para meters

U kunt geen positionele para meters gebruiken met activiteiten en cmdlets in een werk stroom.  Dit betekent dat u parameter namen moet gebruiken.

Neem bijvoorbeeld de volgende code op waarmee alle actieve services worden opgehaald.

```azurepowershell-interactive
Get-Service | Where-Object {$_.Status -eq "Running"}
```

Als u probeert dezelfde code in een werk stroom uit te voeren, ontvangt u een bericht als de para meter set kan niet worden omgezet met de opgegeven benoemde para meters.  Als u dit wilt corrigeren, geeft u de parameter naam op zoals in het volgende.

```powershell
Workflow Get-RunningServices
{
    Get-Service | Where-Object -FilterScript {$_.Status -eq "Running"}
}
```

### <a name="deserialized-objects"></a>Gedeserialiseerd objecten

Objecten in werk stromen worden gedeserialiseerd.  Dit betekent dat hun eigenschappen nog steeds beschikbaar zijn, maar niet hun methoden.  Denk bijvoorbeeld aan de volgende Power shell-code die een service stopt met de methode stop van het object service.

```azurepowershell-interactive
$Service = Get-Service -Name MyService
$Service.Stop()
```

Als u probeert dit in een werk stroom uit te voeren, wordt een fout bericht weer gegeven dat het aanroepen van de methode niet wordt ondersteund in een Windows Power shell-werk stroom.

Een optie is om deze twee regels code in een [InlineScript](#inlinescript) -blok te laten teruglopen in welk geval $service een service object in het blok zou zijn.

```powershell
Workflow Stop-Service
{
    InlineScript {
        $Service = Get-Service -Name MyService
        $Service.Stop()
    }
}
```

Een andere optie is het gebruik van een andere cmdlet die dezelfde functionaliteit als de-methode uitvoert, als er een beschikbaar is.  In ons voor beeld biedt de stop-service-cmdlet dezelfde functionaliteit als de methode stop en kunt u het volgende gebruiken voor een werk stroom.

```powershell
Workflow Stop-MyService
{
    $Service = Get-Service -Name MyService
    Stop-Service -Name $Service.Name
}
```

## <a name="inlinescript"></a>InlineScript

De **InlineScript** -activiteit is handig wanneer u een of meer opdrachten moet uitvoeren als traditioneel Power shell-script in plaats van Power shell-werk stroom.  Terwijl de opdrachten in een werkstroom voor verwerking naar de Windows Workflow Foundation worden verzonden, worden door Windows PowerShell-opdrachten in een InlineScript-blok verwerkt.

InlineScript maakt gebruik van de volgende syntaxis die hieronder wordt weer gegeven.

```powershell
InlineScript
{
    <Script Block>
} <Common Parameters>
```

U kunt de uitvoer van een InlineScript retour neren door de uitvoer toe te wijzen aan een variabele. In het volgende voor beeld wordt een service gestopt en wordt de naam van de service vervolgens uitgevoerd.

```powershell
Workflow Stop-MyService
{
    $Output = InlineScript {
        $Service = Get-Service -Name MyService
        $Service.Stop()
        $Service
    }

    $Output.Name
}
```

U kunt waarden door geven in een InlineScript-blok, maar u moet **$using** bereik wijzigings functie gebruiken.  Het volgende voor beeld is identiek aan het vorige voor beeld, behalve dat de service naam wordt verschaft door een variabele.

```powershell
Workflow Stop-MyService
{
    $ServiceName = "MyService"

    $Output = InlineScript {
        $Service = Get-Service -Name $Using:ServiceName
        $Service.Stop()
        $Service
    }

    $Output.Name
}
```

Hoewel InlineScript-activiteiten in bepaalde werk stromen kritiek kunnen zijn, bieden ze geen ondersteuning voor werk stroom constructies en mogen ze alleen worden gebruikt wanneer dit nodig is om de volgende redenen:

* U kunt geen [controle punten](#checkpoints) gebruiken in een InlineScript-blok. Als er een fout optreedt in het blok, moet dit worden hervat vanaf het begin van het blok.
* U kunt geen [parallelle uitvoering](#parallel-processing) in een InlineScriptBlock gebruiken.
* InlineScript is van invloed op de schaal baarheid van de werk stroom, omdat deze de Windows Power shell-sessie voor de volledige lengte van het blok InlineScript bevat.

Zie [Windows Power shell-opdrachten uitvoeren in een werk stroom](https://technet.microsoft.com/library/jj574197.aspx) en [about_InlineScript](https://technet.microsoft.com/library/jj649082.aspx)voor meer informatie over het gebruik van InlineScript.

## <a name="parallel-processing"></a>Parallelle verwerking

Een voordeel van Windows PowerShell-werkstromen is de mogelijkheid om te een reeks opdrachten parallel in plaats van opeenvolgend voeren net als bij een typische script.

U kunt het sleutel woord **parallel** gebruiken om een script blok te maken met meerdere opdrachten die gelijktijdig worden uitgevoerd. Dit maakt gebruik van de volgende syntaxis die hieronder wordt weer gegeven. In dit geval worden Activiteit1 en Activiteit2 op hetzelfde moment gestart. Activiteit3 start pas als zowel Activiteit1 als Activiteit2 zijn voltooid.

```powershell
Parallel
{
    <Activity1>
    <Activity2>
}
<Activity3>
```

Denk bijvoorbeeld aan de volgende Power shell-opdrachten waarmee meerdere bestanden naar een netwerk bestemming worden gekopieerd.  Deze opdrachten worden sequentieel uitgevoerd, zodat één bestand moet worden gekopieerd voordat de volgende wordt gestart.

```azurepowershell-interactive
Copy-Item -Path C:\LocalPath\File1.txt -Destination \\NetworkPath\File1.txt
Copy-Item -Path C:\LocalPath\File2.txt -Destination \\NetworkPath\File2.txt
Copy-Item -Path C:\LocalPath\File3.txt -Destination \\NetworkPath\File3.txt
```

In de volgende werk stroom worden dezelfde opdrachten parallel uitgevoerd, zodat ze allemaal op hetzelfde moment worden gekopieerd.  Alleen als deze zijn gekopieerd, wordt het voltooiings bericht weer gegeven.

```powershell
Workflow Copy-Files
{
    Parallel
    {
        Copy-Item -Path "C:\LocalPath\File1.txt" -Destination "\\NetworkPath"
        Copy-Item -Path "C:\LocalPath\File2.txt" -Destination "\\NetworkPath"
        Copy-Item -Path "C:\LocalPath\File3.txt" -Destination "\\NetworkPath"
    }

    Write-Output "Files copied."
}
```

U kunt de **ForEach-Parallel** constructie verwerken van opdrachten voor elk item in een verzameling gelijktijdig. De items in de verzameling worden parallel verwerkt, terwijl de opdrachten in het scriptblok worden opeenvolgend uitgevoerd. Dit maakt gebruik van de volgende syntaxis die hieronder wordt weer gegeven. In dit geval wordt Activiteit1 op hetzelfde moment gestart voor alle items in de verzameling. Activiteit2 wordt voor elk item gestart nadat Activiteit1 is voltooid. Activiteit3 start pas als zowel Activiteit1 als Activiteit2 zijn voltooid voor alle items. We gebruiken de para meter `ThrottleLimit` om de parallellisme te beperken. Te hoog van een `ThrottleLimit` kan problemen veroorzaken. De ideale waarde voor de para meter `ThrottleLimit` hangt af van veel factoren in uw omgeving. U moet beginnen met een lage waarde en verschillende waarden verhogen totdat u er een hebt gevonden die geschikt is voor uw specifieke omstandigheid.

```powershell
ForEach -Parallel -ThrottleLimit 10 ($<item> in $<collection>)
{
    <Activity1>
    <Activity2>
}
<Activity3>
```

Het volgende voor beeld is vergelijkbaar met het vorige voor beeld van het kopiëren van bestanden parallel.  In dit geval wordt een bericht weer gegeven voor elk bestand nadat het is gekopieerd.  Alleen als ze volledig zijn gekopieerd, wordt het laatste voltooiings bericht weer gegeven.

```powershell
Workflow Copy-Files
{
    $files = @("C:\LocalPath\File1.txt","C:\LocalPath\File2.txt","C:\LocalPath\File3.txt")

    ForEach -Parallel -ThrottleLimit 10 ($File in $Files)
    {
        Copy-Item -Path $File -Destination \\NetworkPath
        Write-Output "$File copied."
    }

    Write-Output "All files copied."
}
```

> [!NOTE]
> Het is niet raadzaam om onderliggende runbooks parallel uit te voeren, omdat deze is weer gegeven om onbetrouwbare resultaten te geven. De uitvoer van het onderliggende runbook wordt soms niet weer gegeven en instellingen in één onderliggend runbook kunnen van invloed zijn op de andere parallelle onderliggende runbooks. Variabelen zoals $VerbosePreference, $WarningPreference en anderen mogen niet worden door gegeven aan de onderliggende runbooks. En als het onderliggende runbook deze waarden wijzigt, zijn ze mogelijk niet juist teruggezet na het aanroepen.

## <a name="checkpoints"></a>Controlepunten

Een *controle punt* is een moment opname van de huidige status van de werk stroom die de huidige waarde voor variabelen bevat en een uitvoer die op dat punt is gegenereerd. Als een werk stroom eindigt met een fout of wordt onderbroken, wordt deze in de volgende keer uitgevoerd vanaf het laatste controle punt in plaats van aan het begin van de werk stroom.  U kunt een controlepunt instellen in een werkstroom met de **Checkpoint-Workflow** activiteit. Azure Automation heeft een functie met de naam [fair share](automation-runbook-execution.md#fair-share), waarbij elk runbook dat drie uur wordt uitgevoerd, wordt verwijderd zodat andere runbooks kunnen worden uitgevoerd. Uiteindelijk wordt het niet-geladen runbook opnieuw geladen en wanneer dit het geval is, wordt de uitvoering hervat vanaf het laatste controle punt dat in het runbook is gemaakt. Om ervoor te zorgen dat het runbook uiteindelijk wordt voltooid, moet u controle punten toevoegen met intervallen die minder dan drie uur worden uitgevoerd. Als tijdens elke uitvoering een nieuw controle punt wordt toegevoegd en als het runbook na 3 uur wordt verwijderd als gevolg van een fout, wordt het runbook voor onbepaalde tijd hervat.

In de volgende voorbeeld code treedt een uitzonde ring op nadat de werk stroom is beëindigd door Activiteit2. Wanneer de werk stroom opnieuw wordt uitgevoerd, wordt deze gestart door Activiteit2 uit te voeren, aangezien dit net nadat het laatste controle punt is ingesteld.

```powershell
<Activity1>
Checkpoint-Workflow
<Activity2>
<Exception>
<Activity3>
```

U moet controle punten instellen in een werk stroom na activiteiten die mogelijk gevoelig zijn voor uitzonde ring en niet moeten worden herhaald als de werk stroom wordt hervat. Uw werk stroom kan bijvoorbeeld een virtuele machine maken. U kunt een controlepunt kan instellen, zowel vóór als na de opdrachten voor het maken van de virtuele machine. Als het maken mislukt, worden de opdrachten herhaald als de werk stroom opnieuw wordt gestart. Als de werk stroom mislukt nadat het maken is voltooid, wordt de virtuele machine niet opnieuw gemaakt wanneer de werk stroom wordt hervat.

In het volgende voor beeld worden meerdere bestanden gekopieerd naar een netwerk locatie en wordt na elk bestand een controle punt ingesteld.  Als de netwerk locatie is verbroken, eindigt de werk stroom met de fout.  Wanneer het opnieuw wordt gestart, wordt het op het laatste controle punt hervat, wat betekent dat alleen de bestanden die al zijn gekopieerd, worden overgeslagen.

```powershell
Workflow Copy-Files
{
    $files = @("C:\LocalPath\File1.txt","C:\LocalPath\File2.txt","C:\LocalPath\File3.txt")

    ForEach ($File in $Files)
    {
        Copy-Item -Path $File -Destination \\NetworkPath
        Write-Output "$File copied."
        Checkpoint-Workflow
    }

    Write-Output "All files copied."
}
```

Omdat de referenties van de gebruikers naam niet worden bewaard nadat u de activiteit [suspend-werk stroom](https://technet.microsoft.com/library/jj733586.aspx) aanroept of na het laatste controle punt, moet u de referenties instellen op null en ze vervolgens opnieuw ophalen uit de Asset Store nadat **suspend-workflow** of Check Point is aangeroepen.  Anders wordt het volgende fout bericht weer gegeven: *de werk stroom taak kan niet worden hervat, omdat persistentie gegevens niet volledig kunnen worden opgeslagen of de opgeslagen persistentie gegevens zijn beschadigd. U moet de werk stroom opnieuw starten.*

In de volgende code ziet u hoe u dit kunt afhandelen in uw Power shell workflow-runbooks.

```powershell
workflow CreateTestVms
{
    $Cred = Get-AzureAutomationCredential -Name "MyCredential"
    $null = Connect-AzureRmAccount -Credential $Cred

    $VmsToCreate = Get-AzureAutomationVariable -Name "VmsToCreate"

    foreach ($VmName in $VmsToCreate)
        {
        # Do work first to create the VM (code not shown)

        # Now add the VM
        New-AzureRmVm -VM $Vm -Location "WestUs" -ResourceGroupName "ResourceGroup01"

        # Checkpoint so that VM creation is not repeated if workflow suspends
        $Cred = $null
        Checkpoint-Workflow
        $Cred = Get-AzureAutomationCredential -Name "MyCredential"
        $null = Connect-AzureRmAccount -Credential $Cred
        }
}
```

> [!IMPORTANT]
> **Add-AzureRmAccount** is nu een alias voor **Connect-AzureRmAccount**. Als u de bibliotheek items zoekt en u geen **Connect-AzureRMAccount**ziet, kunt u **add-AzureRMAccount**gebruiken, maar u kunt ook uw modules bijwerken in uw Automation-account.

Dit is niet vereist als u verificatie uitvoert met een run as-account dat is geconfigureerd met een service-principal.

Zie voor meer informatie over controlepunten, [controlepunten toevoegen aan een Scriptwerkstroom](https://technet.microsoft.com/library/jj574114.aspx).

## <a name="next-steps"></a>Volgende stappen

* Zie [Mijn eerste PowerShell Workflow-runbook](automation-first-runbook-textual.md) om aan de slag te gaan met PowerShell Workflow-runbooks

