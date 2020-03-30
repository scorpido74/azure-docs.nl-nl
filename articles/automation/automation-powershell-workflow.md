---
title: PowerShell-werkstroom voor Azure-automatisering leren
description: Dit artikel is bedoeld als een snelle les voor auteurs die bekend zijn met PowerShell om de specifieke verschillen tussen PowerShell en PowerShell Workflow en concepten die van toepassing zijn op Automation runbooks te begrijpen.
services: automation
ms.subservice: process-automation
ms.date: 12/14/2018
ms.topic: conceptual
ms.openlocfilehash: 6e4c8057322b6208ea3b447b264e2bde1344540c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278686"
---
# <a name="learning-key-windows-powershell-workflow-concepts-for-automation-runbooks"></a>De belangrijkste Windows PowerShell-werkstroomconcepten voor runbooks voor automatisering leren

Runbooks in Azure Automation worden geïmplementeerd als Windows PowerShell-werkstromen.  Een Windows PowerShell-werkstroom is vergelijkbaar met een Windows PowerShell-script, maar heeft een aantal belangrijke verschillen die verwarrend kunnen zijn voor een nieuwe gebruiker.  Hoewel dit artikel bedoeld is om u te helpen runbooks te schrijven met powershell-werkstroom, raden we u aan runbooks te schrijven met PowerShell, tenzij u controlepunten nodig hebt.  Er zijn verschillende syntaxisverschillen bij het maken van PowerShell Workflow-runbooks en deze verschillen vereisen iets meer werk om effectieve werkstromen te schrijven.

Een werkstroom is een opeenvolging van geprogrammeerde, met elkaar verbonden stappen waarmee langlopende taken worden uitgevoerd of die de coördinatie vereisen van meerdere stappen op meerdere apparaten of beheerde knooppunten. De voordelen van een werkstroom op een normaal script omvatten de mogelijkheid om gelijktijdig een actie tegen meerdere apparaten uit te voeren en de mogelijkheid om automatisch te herstellen van fouten. Een Windows PowerShell-werkstroom is een Windows PowerShell-script dat windows workflow foundation gebruikt. Terwijl de werkstroom is geschreven met Windows PowerShell-syntaxis en gelanceerd door Windows PowerShell, wordt het verwerkt door Windows Workflow Foundation.

Zie [Aan de slag met Windows PowerShell-workflow](https://technet.microsoft.com/library/jj134242.aspx)voor meer informatie over de onderwerpen in dit artikel.

## <a name="basic-structure-of-a-workflow"></a>Basisstructuur van een werkstroom

De eerste stap om een PowerShell-script om te zetten naar een PowerShell-werkstroom, wordt bijgesloten met het trefwoord **Werkstroom.**  Een werkstroom begint met het trefwoord **Werkstroom,** gevolgd door de hoofdtekst van het script dat is ingesloten in accolades. De naam van de werkstroom volgt het trefwoord **Werkstroom** zoals weergegeven in de volgende syntaxis:

```powershell
Workflow Test-Workflow
{
    <Commands>
}
```

De naam van de werkstroom moet overeenkomen met de naam van het runbook Automatisering. Als de runbook wordt geïmporteerd, moet de bestandsnaam overeenkomen met de werkstroomnaam en moet deze eindigen op *.ps1*.

Als u parameters aan de werkstroom wilt toevoegen, gebruikt u het trefwoord **Param** zoals u dat zou doen met een script.

## <a name="code-changes"></a>Codewijzigingen

PowerShell-werkstroomcode ziet er bijna identiek uit aan PowerShell-scriptcode, met uitzondering van een paar belangrijke wijzigingen.  In de volgende secties worden wijzigingen beschreven die u moet aanbrengen in een PowerShell-script dat moet worden uitgevoerd in een werkstroom.

### <a name="activities"></a>Activiteiten

Een activiteit is een specifieke taak in een werkstroom. Zoals een script uit een of meer opdrachten bestaat, bestaat een werkstroom uit een of meer activiteiten die achter elkaar worden uitgevoerd. Windows PowerShell-werkstroom converteert automatisch veel van de Windows PowerShell cmdlets in activiteiten wanneer het een werkstroom uitvoert. Wanneer u een van deze cmdlets opgeeft in uw runbook, wordt de bijbehorende activiteit uitgevoerd door Windows Workflow Foundation. Voor die cmdlets zonder bijbehorende activiteit voert Windows PowerShell Workflow de cmdlet automatisch uit binnen een [InlineScript-activiteit.](#inlinescript) Er is een set cmdlets die zijn uitgesloten en niet kan worden gebruikt in een werkstroom, tenzij u ze expliciet opneemt in een InlineScript-blok. Zie Activiteiten gebruiken in [scriptwerkstromen voor](https://technet.microsoft.com/library/jj574194.aspx)meer informatie over deze concepten.

Werkstroomactiviteiten delen een aantal gemeenschappelijke parameters om hun werking te configureren. Zie [about_WorkflowCommonParameters](https://technet.microsoft.com/library/jj129719.aspx)voor meer informatie over de algemene werkstroomparameters .

### <a name="positional-parameters"></a>Positionele parameters

U geen positionele parameters gebruiken met activiteiten en cmdlets in een workflow.  Dit alles betekent dat u parameternamen moet gebruiken.

Houd bijvoorbeeld rekening met de volgende code waarmee alle services worden uitgevoerd.

```azurepowershell-interactive
Get-Service | Where-Object {$_.Status -eq "Running"}
```

Als u dezelfde code probeert uit te voeren in een werkstroom, ontvangt u een bericht als 'Parameterset kan niet worden opgelost met de opgegeven benoemde parameters'.  Geef de parameternaam op als volgt om dit te corrigeren.

```powershell
Workflow Get-RunningServices
{
    Get-Service | Where-Object -FilterScript {$_.Status -eq "Running"}
}
```

### <a name="deserialized-objects"></a>Gedeserialiseerde objecten

Objecten in werkstromen worden gedeserialiseerd.  Dit betekent dat hun eigenschappen nog steeds beschikbaar zijn, maar niet hun methoden.  Denk bijvoorbeeld aan de volgende PowerShell-code waarmee een service wordt gestopt met de methode Stoppen van het object Service.

```azurepowershell-interactive
$Service = Get-Service -Name MyService
$Service.Stop()
```

Als u dit probeert uit te voeren in een werkstroom, ontvangt u een foutmelding 'Methodeaanroep wordt niet ondersteund in een Windows PowerShell-werkstroom'.

Een optie is om deze twee regels code in een [InlineScript-blok](#inlinescript) te verpakken, in welk geval $Service een serviceobject in het blok zou zijn.

```powershell
Workflow Stop-Service
{
    InlineScript {
        $Service = Get-Service -Name MyService
        $Service.Stop()
    }
}
```

Een andere optie is om een andere cmdlet te gebruiken die dezelfde functionaliteit uitvoert als de methode, als er een beschikbaar is.  In ons voorbeeld biedt de Stop-Service-cmdlet dezelfde functionaliteit als de methode Stoppen en u het volgende gebruiken voor een werkstroom.

```powershell
Workflow Stop-MyService
{
    $Service = Get-Service -Name MyService
    Stop-Service -Name $Service.Name
}
```

## <a name="inlinescript"></a>InlineScript

De **InlineScript-activiteit** is handig wanneer u een of meer opdrachten moet uitvoeren als traditioneel PowerShell-script in plaats van powershell-werkstroom.  Waar opdrachten in een werkstroom naar Windows Workflow Foundation worden verzonden voor verwerking, worden opdrachten in een InlineScript blok verwerkt door Windows PowerShell.

InlineScript gebruikt de volgende syntaxis hieronder.

```powershell
InlineScript
{
    <Script Block>
} <Common Parameters>
```

U de uitvoer van een InlineScript retourneren door de uitvoer toe te wijs aan een variabele. In het volgende voorbeeld wordt een service gestopt en wordt de servicenaam uitgevoerd.

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

U waarden doorgeven aan een InlineScript-blok, maar u moet **$Using** scopemodifier gebruiken.  Het volgende voorbeeld is identiek aan het vorige voorbeeld, behalve dat de servicenaam wordt geleverd door een variabele.

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

Hoewel InlineScript-activiteiten van cruciaal belang kunnen zijn in bepaalde werkstromen, ondersteunen ze geen werkstroomconstructies en mogen ze alleen worden gebruikt wanneer dat nodig is om de volgende redenen:

* U [geen controlepunten](#checkpoints) gebruiken in een InlineScript-blok. Als er een fout optreedt binnen het blok, moet deze vanaf het begin van het blok worden hervat.
* U geen [parallelle uitvoering](#parallel-processing) gebruiken in een InlineScriptBlock.
* InlineScript beïnvloedt de schaalbaarheid van de werkstroom omdat de Windows PowerShell-sessie gedurende de gehele lengte van het InlineScript-blok wordt gehouden.

Zie [Windows PowerShell-opdrachten uitvoeren in een werkstroom](https://technet.microsoft.com/library/jj574197.aspx) en [about_InlineScript](https://technet.microsoft.com/library/jj649082.aspx)voor meer informatie over het gebruik van InlineScript.

## <a name="parallel-processing"></a>Parallelle verwerking

Een voordeel van Windows PowerShell-werkstromen is de mogelijkheid om een ​​reeks opdrachten parallel uit te voeren in plaats van opeenvolgend zoals bij een typische script.

U het trefwoord **Parallel** gebruiken om een scriptblok te maken met meerdere opdrachten die gelijktijdig worden uitgevoerd. Hiermee wordt de volgende syntaxis gebruikt. In dit geval beginnen Activiteit1 en Activiteit2 tegelijkertijd. Activity3 begint pas nadat zowel Activiteit1 als Activiteit2 zijn voltooid.

```powershell
Parallel
{
    <Activity1>
    <Activity2>
}
<Activity3>
```

Denk bijvoorbeeld aan de volgende PowerShell-opdrachten die meerdere bestanden naar een netwerkbestemming kopiëren.  Deze opdrachten worden opeenvolgend uitgevoerd, zodat één bestand moet worden gekopieerd voordat het volgende wordt gestart.

```azurepowershell-interactive
Copy-Item -Path C:\LocalPath\File1.txt -Destination \\NetworkPath\File1.txt
Copy-Item -Path C:\LocalPath\File2.txt -Destination \\NetworkPath\File2.txt
Copy-Item -Path C:\LocalPath\File3.txt -Destination \\NetworkPath\File3.txt
```

De volgende werkstroom voert dezelfde opdrachten parallel uit, zodat ze allemaal tegelijkertijd beginnen te kopiëren.  Pas nadat ze allemaal zijn gekopieerd, wordt het voltooiingsbericht weergegeven.

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

U de **foreach-parallelconstructie** gebruiken om opdrachten voor elk item in een verzameling gelijktijdig te verwerken. De items in de verzameling worden parallel verwerkt, terwijl de opdrachten in het scriptblok sequentieel worden uitgevoerd. Hiermee wordt de volgende syntaxis gebruikt. In dit geval begint Activiteit1 op hetzelfde moment voor alle items in de collectie. Voor elk item wordt Activiteit2 gestart nadat Activiteit1 is voltooid. Activiteit3 wordt pas gestart nadat zowel Activiteit1 als Activiteit2 voor alle items zijn voltooid. We gebruiken `ThrottleLimit` de parameter om het parallellisme te beperken. Te hoog `ThrottleLimit` van een kan problemen veroorzaken. De ideale waarde `ThrottleLimit` voor de parameter is afhankelijk van vele factoren in uw omgeving. Probeer te beginnen met een lage waarde en probeer verschillende stijgende waarden totdat u er een vindt die werkt voor uw specifieke omstandigheid.

```powershell
ForEach -Parallel -ThrottleLimit 10 ($<item> in $<collection>)
{
    <Activity1>
    <Activity2>
}
<Activity3>
```

Het volgende voorbeeld is vergelijkbaar met het vorige voorbeeld het kopiëren van bestanden parallel.  In dit geval wordt voor elk bestand een bericht weergegeven nadat het is kopieën.  Pas nadat ze allemaal volledig zijn gekopieerd, wordt het definitieve voltooiingsbericht weergegeven.

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
> We raden het uitvoeren van onderliggende runbooks niet parallel aan, omdat dit is aangetoond dat dit onbetrouwbare resultaten oplevert. De uitvoer van het onderliggende runbook wordt soms niet weergegeven en instellingen in een onderliggende runbook kunnen van invloed zijn op de andere parallelle onderliggende runbooks. Variabelen zoals $VerbosePreference, $WarningPreference en andere mogen niet worden gepropageerd naar de runbooks voor kinderen. En als het onderliggende runbook deze waarden wijzigt, worden ze mogelijk niet goed hersteld na aanroep.

## <a name="checkpoints"></a>Controlepunten

Een *controlepunt* is een momentopname van de huidige status van de werkstroom die de huidige waarde voor variabelen en eventuele uitvoer die tot dat punt wordt gegenereerd, bevat. Als een werkstroom ten onrechte eindigt of wordt opgeschort, begint de volgende keer dat deze wordt uitgevoerd, vanaf het laatste controlepunt in plaats van het begin van de werkstroom.  U een controlepunt instellen in een werkstroom met de activiteit **Controlepunt-Werkstroom.** Azure Automation heeft een functie genaamd [fair share,](automation-runbook-execution.md#fair-share)waarbij elke runbook die 3 uur wordt uitgevoerd, wordt gelost om andere runbooks uit te voeren. Uiteindelijk wordt het geloste runbook opnieuw geladen en wanneer dit het is, wordt de uitvoering hervat vanaf het laatste controlepunt in het runbook. Om te garanderen dat het runbook uiteindelijk wordt voltooid, moet u controlepunten toevoegen met intervallen die minder dan 3 uur duren. Als tijdens elke run een nieuw controlepunt wordt toegevoegd en als het runbook na 3 uur wordt uitgezet vanwege een fout, wordt het runbook voor onbepaalde tijd hervat.

In de volgende voorbeeldcode treedt een uitzondering op nadat Activiteit2 ervoor zorgt dat de werkstroom is beëindigd. Wanneer de werkstroom opnieuw wordt uitgevoerd, wordt activiteit2 gestart, omdat dit vlak na de laatste controlepuntset was.

```powershell
<Activity1>
Checkpoint-Workflow
<Activity2>
<Exception>
<Activity3>
```

U moet controlepunten instellen in een werkstroom na activiteiten die mogelijk kunnen worden geuitzondering en mogen niet worden herhaald als de werkstroom wordt hervat. Uw werkstroom kan bijvoorbeeld een virtuele machine maken. U moet een controlepunt vastleggen zowel voor als na de opdrachten voor het aanmaken van de virtuele machine. Als de creatie mislukt, worden de opdrachten herhaald als de werkstroom opnieuw wordt gestart. Als de werkstroom mislukt nadat de creatie is geslaagd, wordt de virtuele machine niet opnieuw gemaakt wanneer de werkstroom wordt hervat.

In het volgende voorbeeld worden meerdere bestanden naar een netwerklocatie gekopieerd en wordt na elk bestand een controlepunt ingesteld.  Als de netwerklocatie verloren gaat, eindigt de werkstroom ten onrechte.  Wanneer het opnieuw wordt gestart, wordt deze hervat bij het laatste controlepunt, wat betekent dat alleen de bestanden die al zijn gekopieerd, worden overgeslagen.

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

Omdat gebruikersnaamreferenties niet worden gehandhaafd nadat u de activiteit [Onderbreken-werkstroom](https://technet.microsoft.com/library/jj733586.aspx) of na het laatste controlepunt hebt aangeroepen, moet u de referenties op null instellen en deze vervolgens opnieuw ophalen in het assetstore nadat **Suspend-Workflow** of controlepunt is aangeroepen.  Anders ontvangt u mogelijk het volgende foutbericht: *de werkstroomtaak kan niet worden hervat, omdat persistentiegegevens niet volledig kunnen worden opgeslagen of opgeslagen persistentiegegevens zijn beschadigd. U moet de werkstroom opnieuw opstarten.*

Dezelfde code laat zien hoe u hiermee omgaat in uw PowerShell Workflow-runbooks.

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
> **Add-AzureRmAccount** is nu een alias voor **Connect-AzureRMAccount**. Wanneer u uw bibliotheekitems zoekt, als u **Connect-AzureRMAccount**niet ziet, u **Add-AzureRmAccount**gebruiken of u uw modules bijwerken in uw automatiseringsaccount.

Dit is niet vereist als u een Run As-account gebruikt dat is geconfigureerd met een serviceprincipal.

Zie [Controlepunten toevoegen aan een scriptwerkstroomvoor](https://technet.microsoft.com/library/jj574114.aspx)meer informatie over controlepunten.

## <a name="next-steps"></a>Volgende stappen

* Zie [Mijn eerste PowerShell-werkstroomrunboek](automation-first-runbook-textual.md) om aan de slag te gaan met PowerShell-werkstroomrunboeken

