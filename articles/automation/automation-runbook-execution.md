---
title: Uitvoering van Runbook in Azure Automation
description: Hierin worden de details van het verwerken van een runbook in Azure Automation beschreven.
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 04/04/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 2f8fa4c378ed394930a4018c58b99ed919cbc2c2
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/09/2019
ms.locfileid: "73886970"
---
# <a name="runbook-execution-in-azure-automation"></a>Uitvoering van Runbook in Azure Automation

Wanneer u een runbook start in Azure Automation, wordt er een taak gemaakt. Een taak is één uitvoerings exemplaar van een runbook. Een Azure Automation-werk nemer is toegewezen om elke taak uit te voeren. Terwijl werk rollen worden gedeeld door veel Azure-accounts, zijn taken van verschillende Automation-accounts van elkaar geïsoleerd. U hebt geen controle over welke Worker-Services de aanvraag indienen voor uw taak. Eén runbook kan meerdere taken tegelijk uitvoeren. De uitvoerings omgeving voor taken uit hetzelfde Automation-account kan opnieuw worden gebruikt. Hoe meer taken u tegelijkertijd uitvoert, hoe vaak deze naar dezelfde sandbox kunnen worden verzonden. Taken die worden uitgevoerd in hetzelfde sandbox-proces, kunnen van invloed zijn op elkaar, een voor beeld van het uitvoeren van de `Disconnect-AzureRMAccount`-cmdlet. Als u deze cmdlet uitvoert, wordt de verbinding van elke runbook-taak in het gedeelde sandbox-proces verbroken. Wanneer u de lijst met runbooks in de Azure Portal bekijkt, wordt de status weer gegeven van alle taken die voor elk runbook zijn gestart. U kunt de lijst met taken voor elk runbook weer geven om de status van elke te volgen. Taak logboeken worden Maxi maal 30 dagen opgeslagen. Voor een beschrijving van de taak statussen van de verschillende [taken.](#job-statuses)

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

In het volgende diagram ziet u de levens cyclus van een runbook-taak voor [Power shell-runbooks](automation-runbook-types.md#powershell-runbooks), [grafische Runbooks](automation-runbook-types.md#graphical-runbooks) en [Power shell workflow-runbooks](automation-runbook-types.md#powershell-workflow-runbooks).

![Taak statussen-Power shell-werk stroom](./media/automation-runbook-execution/job-statuses.png)

Uw taken hebben toegang tot uw Azure-resources door verbinding te maken met uw Azure-abonnement. Ze hebben alleen toegang tot resources in uw Data Center als deze bronnen toegankelijk zijn vanuit de open bare Cloud.

## <a name="where-to-run-your-runbooks"></a>Waar worden uw runbooks uitgevoerd?

Runbooks in Azure Automation kunnen worden uitgevoerd op een sandbox in azure of op een [Hybrid Runbook worker](automation-hybrid-runbook-worker.md). Een sandbox is een gedeelde omgeving in azure die kan worden gebruikt door meerdere taken. Taken die gebruikmaken van dezelfde sandbox zijn gebonden aan de resource beperkingen van de sandbox. Hybrid Runbook Workers kunnen runbooks rechtstreeks uitvoeren op de computer die als host fungeert voor de rol en voor resources in de omgeving om deze lokale resources te beheren. Runbooks worden opgeslagen en beheerd in Azure Automation en vervolgens aan een of meer toegewezen computers geleverd. De meeste runbooks kunnen eenvoudig worden uitgevoerd in de Azure-sandboxes. Er zijn specifieke scenario's waarbij het kiezen van een hybride Runbook over een Azure-sandbox voor het uitvoeren van uw Runbook wordt aanbevolen. Zie de volgende tabel voor een lijst met enkele voor beelden van scenario's:

|Taak|Beste keuze|Opmerkingen|
|---|---|---|
|Integreren met Azure-resources|Azure sandbox|Verificatie is eenvoudiger in Azure. Als u een Hybrid Runbook Worker op een virtuele Azure-machine gebruikt, kunt u [beheerde identiteiten gebruiken voor Azure-resources](automation-hrw-run-runbooks.md#managed-identities-for-azure-resources)|
|Optimale prestaties om Azure-resources te beheren|Azure sandbox|Script wordt uitgevoerd in dezelfde omgeving, die op zijn beurt minder latentie heeft|
|Beperk operationele kosten|Azure sandbox|Er is geen reken belasting, geen behoefte aan een VM|
|Lang uitgevoerd script|Hybrid Runbook Worker|Azure-sandboxes hebben [beperkingen op resources](../azure-subscription-service-limits.md#automation-limits)|
|Interactie met lokale services|Hybrid Runbook Worker|Kan rechtstreeks toegang hebben tot de hostcomputer|
|Externe software en uitvoer bare bestanden van derden vereisen|Hybrid Runbook Worker|U beheert het besturings systeem en kan software installeren|
|Een bestand of map bewaken met een runbook|Hybrid Runbook Worker|Een [Watcher-taak](automation-watchers-tutorial.md) gebruiken op een Hybrid Runbook worker|
|Resource-intensieve script|Hybrid Runbook Worker| Azure-sandboxes hebben [beperkingen op resources](../azure-subscription-service-limits.md#automation-limits)|
|Modules met specifieke vereisten gebruiken| Hybrid Runbook Worker|Een aantal voorbeelden:</br> **WinSCP** -afhankelijkheid op WinSCP. exe </br> **IISAdministration** -IIS moet zijn ingeschakeld|
|Module installeren waarvoor Installer is vereist|Hybrid Runbook Worker|Modules voor sandbox moeten worden copiable|
|Runbooks of modules gebruiken waarvoor .NET Framework anders is dan 4.7.2|Hybrid Runbook Worker|Er zijn .NET Framework 4.7.2 voor Automation-sandboxes en er is geen manier om deze te upgraden|
|Scripts waarvoor uitbrei ding van bevoegdheden is vereist|Hybrid Runbook Worker|Sandboxes staan geen uitbrei ding van bevoegdheden toe. Als u dit wilt oplossen, gebruikt u een Hybrid Runbook Worker en kunt u UAC uitschakelen en `Invoke-Command` gebruiken bij het uitvoeren van de opdracht die uitbrei ding vereist|
|Scripts waarvoor toegang tot WMI is vereist|Hybrid Runbook Worker|Taken die worden uitgevoerd in sandboxes in de Cloud, [hebben geen toegang tot de WMI](#device-and-application-characteristics)|

## <a name="runbook-behavior"></a>Gedrag van Runbook

Runbooks worden uitgevoerd op basis van de logica die erin is gedefinieerd. Als een runbook wordt onderbroken, wordt het runbook aan het begin opnieuw opgestart. Dit gedrag vereist dat runbooks worden geschreven op een manier waar ze worden ondersteund als er tijdelijke problemen zijn.

Power shell-taken die zijn gestart vanuit een Runbook dat is uitgevoerd in een Azure-sandbox, worden mogelijk niet uitgevoerd in de volledige taal modus. Zie [Power shell-taal modi](/powershell/module/microsoft.powershell.core/about/about_language_modes)voor meer informatie over Power shell-taal modi. Zie de [taak status ophalen met Power shell](#retrieving-job-status-using-powershell) voor meer informatie over het werken met taken in azure Automation.

### <a name="creating-resources"></a>Resources maken

Als uw script resources maakt, moet u controleren of de resource al bestaat voordat u deze opnieuw probeert te maken. In het volgende voor beeld wordt een eenvoudig voor beeld weer gegeven:

```powershell
$vmName = "WindowsVM1"
$resourceGroupName = "myResourceGroup"
$myCred = Get-AutomationPSCredential "MyCredential"
$vmExists = Get-AzureRmResource -Name $vmName -ResourceGroupName $resourceGroupName

if(!$vmExists)
    {
    Write-Output "VM $vmName does not exists, creating"
    New-AzureRmVM -Name $vmName -ResourceGroupName $resourceGroupName -Credential $myCred
    }
else
    {
    Write-Output "VM $vmName already exists, skipping"
    }
```

### <a name="time-dependant-scripts"></a>Tijd afhankelijke scripts

U moet zorgvuldig rekening houden bij het ontwerpen van runbooks. Zoals eerder vermeld, moeten runbooks worden ontworpen op een manier die ze robuust zijn en tijdelijke fouten kunnen afhandelen die ertoe kunnen leiden dat het runbook opnieuw wordt opgestart of mislukt. Als een runbook is mislukt, wordt het opnieuw geprobeerd. Als een runbook normaal gesp roken binnen een tijds beperking wordt uitgevoerd, moet de logica om te controleren of de uitvoerings tijd in het runbook wordt geïmplementeerd om ervoor te zorgen dat bewerkingen zoals opstarten, afsluiten of uitschalen alleen worden uitgevoerd tijdens bepaalde tijdstippen.

> [!NOTE]
> De lokale tijd op het Azure sandbox-proces is ingesteld op UTC-tijd. Berekeningen voor de datum en tijd in uw runbooks moeten hiervan in overweging worden genomen.

### <a name="tracking-progress"></a>Voortgang bijhouden

Het is een goed idee om runbooks te ontwerpen die kunnen worden gemodulareerd. Dit betekent dat u de logica in het runbook kunt structureren zodat deze eenvoudig opnieuw kan worden gebruikt en opnieuw kan worden gestart. Het bijhouden van de voortgang in een runbook is een goede manier om ervoor te zorgen dat de logica in een runbook correct wordt uitgevoerd als er problemen zijn. Enkele manieren om de voortgang van het runbook bij te houden, is door gebruik te maken van een externe bron, zoals opslag accounts, een Data Base of gedeelde bestanden. Door de status extern op te volgen, kunt u in uw runbook logica maken om eerst de status van de laatste actie te controleren die het runbook heeft genomen. Op basis van de resultaten kunt u specifieke taken in het runbook overs Laan of voortzetten.

### <a name="prevent-concurrent-jobs"></a>Gelijktijdige taken voor komen

Sommige runbooks gedragen zich mogelijk vreemd als ze in meerdere taken tegelijk worden uitgevoerd. In dit geval is het belang rijk om logica te implementeren om te controleren of een runbook al een actieve taak heeft. Een eenvoudig voor beeld van hoe u dit gedrag kunt doen, wordt in het volgende voor beeld weer gegeven:

```powershell
# Authenticate to Azure
$connection = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzureRmAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint

$AzureContext = Select-AzureRmSubscription -SubscriptionId $connection.SubscriptionID

# Check for already running or new runbooks
$runbookName = "<RunbookName>"
$rgName = "<ResourceGroupName>"
$aaName = "<AutomationAccountName>"
$jobs = Get-AzureRmAutomationJob -ResourceGroupName $rgName -AutomationAccountName $aaName -RunbookName $runbookName -AzureRmContext $AzureContext

# If then check to see if it is already running
$runningCount = ($jobs | ? {$_.Status -eq "Running"}).count

If (($jobs.status -contains "Running" -And $runningCount -gt 1 ) -Or ($jobs.Status -eq "New")) {
    # Exit code
    Write-Output "Runbook is already running"
    Exit 1
} else {
    # Insert Your code here
}
```

### <a name="working-with-multiple-subscriptions"></a>Met meerdere abonnementen werken

Bij het ontwerpen van runbooks die zijn opgetreden met meerdere abonnementen, moet uw runbook de cmdlet [Disable-AzureRmContextAutosave](/powershell/module/azurerm.profile/disable-azurermcontextautosave) gebruiken om ervoor te zorgen dat uw verificatie context niet wordt opgehaald uit een ander runbook dat in dezelfde sandbox kan worden uitgevoerd. Vervolgens moet u de para meter `-AzureRmContext` gebruiken op de `AzureRM`-cmdlets en de juiste context door geven.

```powershell
# Ensures you do not inherit an AzureRMContext in your runbook
Disable-AzureRmContextAutosave –Scope Process

$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzureRmAccount -ServicePrincipal `
-Tenant $Conn.TenantID `
-ApplicationID $Conn.ApplicationID `
-CertificateThumbprint $Conn.CertificateThumbprint

$context = Get-AzureRmContext

$ChildRunbookName = 'ChildRunbookDemo'
$AutomationAccountName = 'myAutomationAccount'
$ResourceGroupName = 'myResourceGroup'

Start-AzureRmAutomationRunbook `
    -ResourceGroupName $ResourceGroupName `
    -AutomationAccountName $AutomationAccountName `
    -Name $ChildRunbookName `
    -DefaultProfile $context
```

### <a name="handling-exceptions"></a>Afhandeling van uitzonderingen

Bij het ontwerpen van scripts is het belang rijk om uitzonde ringen en mogelijke storingen af te handelen. Hier volgen enkele verschillende manieren voor het afhandelen van uitzonde ringen of onregelmatige problemen met runbooks:

#### <a name="erroractionpreference"></a>$ErrorActionPreference

De [$ErrorActionPreference](/powershell/module/microsoft.powershell.core/about/about_preference_variables#erroractionpreference) voorkeurs variabele bepaalt hoe Power shell reageert op een niet-afsluit fout. Het beëindigen van fouten wordt niet beïnvloed door `$ErrorActionPreference`. deze worden altijd beëindigd. Door `$ErrorActionPreference`te gebruiken, wordt een normaal niet-afsluit bare fout als `PathNotFound` van de `Get-ChildItem` cmdlet, wordt het runbook niet meer voltooid. In het volgende voor beeld ziet u hoe u `$ErrorActionPreference`gebruikt. De laatste `Write-Output`-regel wordt nooit uitgevoerd wanneer het script wordt gestopt.

```powershell-interactive
$ErrorActionPreference = 'Stop'
Get-Childitem -path nofile.txt
Write-Output "This message will not show"
```

#### <a name="try-catch-finally"></a>Probeer catch ten slotte

[Try-catch](/powershell/module/microsoft.powershell.core/about/about_try_catch_finally) wordt gebruikt in Power shell-scripts voor het afhandelen van afsluit fouten. Met behulp van try catch kunt u specifieke uitzonde ringen of algemene uitzonde ringen ondervangen. De instructie catch moet worden gebruikt voor het volgen van fouten of voor het afhandelen van de fout. In het volgende voor beeld wordt geprobeerd een bestand te downloaden dat niet bestaat. Als er een andere uitzonde ring is, wordt de uitzonde ring `System.Net.WebException` als de laatste waarde wordt geretourneerd.

```powershell-interactive
try
{
   $wc = new-object System.Net.WebClient
   $wc.DownloadFile("http://www.contoso.com/MyDoc.doc")
}
catch [System.Net.WebException]
{
    "Unable to download MyDoc.doc from http://www.contoso.com."
}
catch
{
    "An error occurred that could not be resolved."
}
```

#### <a name="throw"></a>Sprong

[Throw](/powershell/module/microsoft.powershell.core/about/about_throw) kan worden gebruikt om een afsluit fout te genereren. Dit kan handig zijn bij het definiëren van uw eigen logica in een runbook. Als aan een bepaald criterium wordt voldaan om het script te stoppen, kunt u `throw` gebruiken om het script te stoppen. In het volgende voor beeld wordt computer een functie parameter weer gegeven die vereist is voor het gebruik van `throw`.

```powershell-interactive
function Get-ContosoFiles
{
  param ($path = $(throw "The Path parameter is required."))
  Get-ChildItem -Path $path\*.txt -recurse
}
```

### <a name="using-executables-or-calling-processes"></a>Uitvoer bare bestanden of aanroepende processen gebruiken

Runbooks die worden uitgevoerd in azure-sandboxes bieden geen ondersteuning voor oproep processen (zoals. exe of subproces. Call). Dit komt doordat Azure-sandboxs gedeelde processen worden uitgevoerd in containers, die mogelijk geen toegang hebben tot alle onderliggende Api's. Voor scenario's waarin u software van derden of het aanroepen van subprocessen nodig hebt, is het raadzaam om het runbook uit te voeren op een [Hybrid Runbook worker](automation-hybrid-runbook-worker.md).

### <a name="device-and-application-characteristics"></a>Kenmerken van apparaten en toepassingen

Runbook-taken die worden uitgevoerd in azure-sandboxes hebben geen toegang tot kenmerken van apparaten of toepassingen. De meest voorkomende API voor het uitvoeren van query's op prestatie gegevens in Windows is WMI. Enkele van deze algemene meet waarden zijn geheugen en CPU-gebruik. Het maakt echter niet uit welke API wordt gebruikt. Taken die in de cloud worden uitgevoerd, hebben geen toegang tot de micro soft-implementatie van web based Enter prise Management (WBEM), dat is gebaseerd op de Common Information Model (CIM), wat de industrie normen zijn voor het definiëren van kenmerken van apparaten en toepassingen.

## <a name="job-statuses"></a>Taak status

In de volgende tabel worden de verschillende statussen beschreven die mogelijk zijn voor een taak. Power Shell heeft twee typen fouten, afsluitende en niet-afsluit fouten. Als u fouten afsluit, wordt de status van het runbook ingesteld op **mislukt** als deze zich voordoen. Met niet-afsluit fouten kan het script worden voortgezet, zelfs nadat het is uitgevoerd. Een voor beeld van een niet-afsluit fout is het gebruik van de `Get-ChildItem` cmdlet met een pad dat niet bestaat. Power shell ziet dat het pad niet bestaat, een fout genereert en naar de volgende map gaat. Bij deze fout zou de runbook-status **niet** kunnen worden ingesteld op mislukt en kunnen worden gemarkeerd als **voltooid**. Als u wilt afdwingen dat een runbook stopt bij een niet-afsluit fout, kunt u `-ErrorAction Stop` gebruiken voor de cmdlet.

| Status | Beschrijving |
|:--- |:--- |
| Voltooid |De taak is voltooid. |
| Mislukt |Voor [grafische en Power shell-werk stroom-runbooks](automation-runbook-types.md)kan het runbook niet worden gecompileerd. Voor [Power shell-script-runbooks](automation-runbook-types.md)kan het runbook niet worden gestart of is er een uitzonde ring opgetreden in de taak. |
| Mislukt, wachten op resources |De taak is mislukt omdat de limiet voor de [billijke share](#fair-share) drie keer is bereikt en vanaf hetzelfde controle punt of vanaf het begin van het runbook elke keer is gestart. |
| In de wachtrij |Er wordt gewacht tot resources op een Automation-werk nemer beschikbaar zijn, zodat deze kunnen worden gestart. |
| Starten |De taak is toegewezen aan een werk nemer en het systeem wordt gestart. |
| Hervatten |Het systeem hervat de taak nadat deze is onderbroken. |
| In uitvoering |De taak wordt uitgevoerd. |
| Uitvoeren, wachten op resources |De taak is uit het geheugen verwijderd omdat de limiet voor de [billijke share](#fair-share) is bereikt. Het wordt binnenkort hervat vanaf het laatste controle punt. |
| Gestopt |De gebruiker heeft de taak gestopt voordat deze is voltooid. |
| Stoppen |Het systeem stopt de taak. |
| Gebroken |De taak is door de gebruiker, door het systeem of door een opdracht in het runbook onderbroken. Als een runbook geen controle punt heeft, begint het vanaf het begin van het runbook. Als er een controle punt is, kan het opnieuw worden gestart en wordt hervat vanaf het laatste controle punt. Het runbook wordt alleen door het systeem onderbroken als er een uitzonde ring optreedt. ErrorActionPreference is standaard ingesteld om door te **gaan**, wat inhoudt dat de taak wordt uitgevoerd op een fout. Als deze voorkeurs variabele is ingesteld op **Stop**, wordt een fout door de taak onderbroken. Is alleen van toepassing op [grafische en Power shell-werk stroom-runbooks](automation-runbook-types.md) . |
| Onderbreken |Het systeem probeert de taak op verzoek van de gebruiker te onderbreken. Het runbook moet het volgende controle punt bereiken voordat het kan worden onderbroken. Als het laatste controle punt al is door gegeven, wordt het voltooid voordat het kan worden onderbroken. Is alleen van toepassing op [grafische en Power shell-werk stroom-runbooks](automation-runbook-types.md) . |

## <a name="viewing-job-status-from-the-azure-portal"></a>Taak status weer geven vanuit de Azure Portal

U kunt een samenvattings status van alle runbook-taken weer geven of Details van een specifieke runbook-taak in het Azure Portal bekijken. U kunt ook integratie met uw Log Analytics-werk ruimte configureren om de taak status en taak stromen van een runbook door te sturen. Zie voor meer informatie over het integreren met Azure Monitor-logboeken [taak status door sturen van automatisering naar Azure monitor-logboeken](automation-manage-send-joblogs-log-analytics.md).

### <a name="automation-runbook-jobs-summary"></a>Overzicht van Automation-runbook-taken

Rechts van het geselecteerde Automation-account ziet u een samen vatting van alle runbook-taken onder de tegel **taak statistieken** .

![Taak statistieken tegel](./media/automation-runbook-execution/automation-account-job-status-summary.png)

Deze tegel toont een aantal en grafische weer gave van de taak status voor alle uitgevoerde taken.

Als u op de tegel klikt, wordt de pagina **taken** weer gegeven, die een overzicht bevat van alle uitgevoerde taken. Op deze pagina worden de status, begin tijden en voltooiings tijden weer gegeven.

![Pagina Automation-account taken](./media/automation-runbook-execution/automation-account-jobs-status-blade.png)

U kunt de lijst met taken filteren door **taken filteren** te selecteren en te filteren op een specifiek runbook, een taak status of uit de vervolg keuzelijst en het tijds bereik waarin u wilt zoeken.

![Taak status filteren](./media/automation-runbook-execution/automation-account-jobs-filter.png)

U kunt ook samenvattings Details van taken weer geven voor een specifiek runbook door dat runbook te selecteren op de pagina **Runbooks** in uw Automation-account en vervolgens de tegel **taken** te selecteren. Met deze actie wordt de pagina **taken** weer gegeven. hier kunt u op de taak record klikken om de details en uitvoer te bekijken.

![Pagina Automation-account taken](./media/automation-runbook-execution/automation-runbook-job-summary-blade.png)

### <a name="job-summary"></a>Taakoverzicht

U kunt een lijst weer geven met alle taken die voor een bepaald runbook zijn gemaakt en de meest recente status. U kunt deze lijst filteren op de taak status en het datum bereik voor de laatste wijziging van de taak. Als u gedetailleerde informatie en uitvoer wilt weer geven, klikt u op de naam van een taak. De gedetailleerde weer gave van de taak bevat de waarden voor de runbook-para meters die zijn opgegeven voor die taak.

U kunt de volgende stappen gebruiken om de taken voor een runbook weer te geven.

1. Selecteer in de Azure Portal **Automation** en selecteer vervolgens de naam van een Automation-account.
2. Selecteer op de hub **Runbooks** en selecteer vervolgens op de pagina **Runbooks** een runbook in de lijst.
3. Klik op de pagina voor het geselecteerde runbook op de tegel **taken** .
4. Klik op een van de taken in de lijst en op de pagina Details van runbook-taak om de details en de uitvoer weer te geven.

## <a name="retrieving-job-status-using-powershell"></a>De taak status ophalen met behulp van Power shell

U kunt de [Get-AzureRmAutomationJob](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationjob) gebruiken om de taken op te halen die voor een runbook zijn gemaakt en de details van een bepaalde taak. Als u een runbook start met Power shell met [Start-AzureRmAutomationRunbook](https://docs.microsoft.com/powershell/module/azurerm.automation/start-azurermautomationrunbook), wordt de resulterende taak geretourneerd. Gebruik [Get-AzureRmAutomationJobOutput](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationjoboutput) om de uitvoer van een taak op te halen.

Met de volgende voorbeeld opdrachten wordt de laatste taak voor een voor beeld-runbook opgehaald en wordt de status weer gegeven, de waarden die zijn opgegeven voor de runbook-para meters en de uitvoer van de taak.

```azurepowershell-interactive
$job = (Get-AzureRmAutomationJob –AutomationAccountName "MyAutomationAccount" `
–RunbookName "Test-Runbook" -ResourceGroupName "ResourceGroup01" | sort LastModifiedDate –desc)[0]
$job.Status
$job.JobParameters
Get-AzureRmAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAcct" -Id $job.JobId –Stream Output
```

In het volgende voor beeld wordt de uitvoer voor een specifieke taak opgehaald en wordt elke record geretourneerd. Als er een uitzonde ring is opgetreden voor een van de records, wordt de uitzonde ring afgeschreven in plaats van de waarde. Dit gedrag is handig als uitzonde ringen aanvullende informatie kunnen bevatten, die mogelijk niet normaal wordt geregistreerd tijdens de uitvoer.

```azurepowershell-interactive
$output = Get-AzureRmAutomationJobOutput -AutomationAccountName <AutomationAccountName> -Id <jobID> -ResourceGroupName <ResourceGroupName> -Stream "Any"
foreach($item in $output)
{
    $fullRecord = Get-AzureRmAutomationJobOutputRecord -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -JobId <jobID> -Id $item.StreamRecordId
    if ($fullRecord.Type -eq "Error")
    {
        $fullRecord.Value.Exception
    }
    else
    {
    $fullRecord.Value
    }
}
```

## <a name="get-details-from-activity-log"></a>Details ophalen uit het activiteiten logboek

Andere details, zoals de persoon of het account waarmee het runbook is gestart, kunnen worden opgehaald uit het activiteiten logboek voor het Automation-account. Het volgende Power shell-voor beeld bevat de laatste gebruiker die het runbook heeft uitgevoerd:

```powershell-interactive
$SubID = "00000000-0000-0000-0000-000000000000"
$AutomationResourceGroupName = "MyResourceGroup"
$AutomationAccountName = "MyAutomationAccount"
$RunbookName = "MyRunbook"
$StartTime = (Get-Date).AddDays(-1)
$JobActivityLogs = Get-AzureRmLog -ResourceGroupName $AutomationResourceGroupName -StartTime $StartTime `
                                | Where-Object {$_.Authorization.Action -eq "Microsoft.Automation/automationAccounts/jobs/write"}

$JobInfo = @{}
foreach ($log in $JobActivityLogs)
{
    # Get job resource
    $JobResource = Get-AzureRmResource -ResourceId $log.ResourceId

    if ($JobInfo[$log.SubmissionTimestamp] -eq $null -and $JobResource.Properties.runbook.name -eq $RunbookName)
    {
        # Get runbook
        $Runbook = Get-AzureRmAutomationJob -ResourceGroupName $AutomationResourceGroupName -AutomationAccountName $AutomationAccountName `
                                            -Id $JobResource.Properties.jobId | ? {$_.RunbookName -eq $RunbookName}

        # Add job information to hash table
        $JobInfo.Add($log.SubmissionTimestamp, @($Runbook.RunbookName,$Log.Caller, $JobResource.Properties.jobId))
    }
}
$JobInfo.GetEnumerator() | sort key -Descending | Select-Object -First 1
```

## <a name="fair-share"></a>Billijke share

Als u resources wilt delen tussen alle runbooks in de Cloud, wordt met Azure Automation tijdelijk een taak verwijderd die langer dan drie uur is uitgevoerd. Taken voor [runbooks op basis van Power shell](automation-runbook-types.md#powershell-runbooks) en [python-runbooks](automation-runbook-types.md#python-runbooks) worden gestopt en niet opnieuw gestart en de taak status wordt gestopt weer gegeven.

Voor langlopende taken kunt u het beste een [Hybrid Runbook worker](automation-hrw-run-runbooks.md#job-behavior)gebruiken. Hybrid Runbook Workers worden niet beperkt door een billijke share en hebben geen beperking voor de manier waarop een Runbook kan worden uitgevoerd. De overige taak [limieten](../azure-subscription-service-limits.md#automation-limits) zijn van toepassing op zowel Azure-sandboxes als Hybrid Runbook Workers. Hoewel Hybrid Runbook Workers niet beperkt zijn tot een billijke share limiet van drie uur, moeten runbooks die op deze werk nemers worden uitgevoerd, worden ontwikkeld ter ondersteuning van het opnieuw opstarten van de problemen met de lokale infra structuur.

Een andere optie is om het runbook te optimaliseren door onderliggende runbooks te gebruiken. Als uw runbook met dezelfde functie wordt uitgevoerd op verschillende resources, zoals een database bewerking op verschillende data bases, kunt u die functie verplaatsen naar een [onderliggend runbook](automation-child-runbooks.md) en aanroepen met de cmdlet [Start-AzureRMAutomationRunbook](/powershell/module/azurerm.automation/start-azurermautomationrunbook) . Elk van deze onderliggende runbooks wordt parallel uitgevoerd in afzonderlijke processen. Dit gedrag vermindert de totale tijd voor het volt ooien van het bovenliggende runbook. U kunt de cmdlet [Get-AzureRmAutomationJob](/powershell/module/azurerm.automation/Get-AzureRmAutomationJob) in uw runbook gebruiken om de taak status voor elk onderliggend item te controleren als er bewerkingen zijn die worden uitgevoerd nadat het onderliggende runbook is voltooid.

## <a name="next-steps"></a>Volgende stappen

* Zie [starten van een runbook in azure Automation](automation-starting-a-runbook.md) voor meer informatie over de verschillende methoden die kunnen worden gebruikt om een runbook te starten in azure Automation.
* Raadpleeg de [Power shell-documenten](https://docs.microsoft.com/powershell/scripting/overview)voor meer informatie over Power shell, inclusief taal referentie-en leer modules.
