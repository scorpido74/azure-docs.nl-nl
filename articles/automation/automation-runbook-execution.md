---
title: Runbook-uitvoering in Azure Automation
description: Beschrijft de details van de manier waarop een runbook in Azure Automation wordt verwerkt.
services: automation
ms.subservice: process-automation
ms.date: 04/04/2019
ms.topic: conceptual
ms.openlocfilehash: de01a7a76a5d225770c273c67f864c83226ecd07
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81261309"
---
# <a name="runbook-execution-in-azure-automation"></a>Runbook-uitvoering in Azure Automation

Met procesautomatisering in Azure Automation u PowerShell-, PowerShell-werkstroom- en grafische runbooks maken en beheren. Zie Azure [Automation runbooks voor](automation-runbook-types.md)meer informatie. 

Automatisering voert uw runbooks uit op basis van de logica die erin is gedefinieerd. Als een runbook wordt onderbroken, wordt deze opnieuw gestart aan het begin. Dit gedrag vereist dat u runbooks schrijft die ondersteunen bij het opnieuw starten als er tijdelijke problemen optreden.

Als u een runbook start in Azure Automation, wordt een taak uitgevoerd, een enkele uitvoeringsinstantie van de runbook. Elke taak heeft toegang tot Azure-bronnen door een verbinding te maken met uw Azure-abonnement. De taak heeft alleen toegang tot bronnen in uw datacenter als deze bronnen toegankelijk zijn vanuit de openbare cloud.

Azure Automation wijst een werknemer toe om elke taak uit te voeren tijdens de uitvoering van de runbook. Hoewel werknemers door veel Azure-accounts worden gedeeld, worden taken van verschillende Automatiseringsaccounts van elkaar geïsoleerd. U niet bepalen welke werknemer uw taakaanvragen bedient.

Wanneer u de lijst met runbooks in de Azure-portal bekijkt, wordt de status weergegeven van elke taak die voor elke runbook is gestart. Azure Automation slaat taaklogboeken op voor maximaal 30 dagen. 

In het volgende diagram wordt de levenscyclus van een runbook-taak voor [PowerShell-runbooks,](automation-runbook-types.md#powershell-runbooks) [PowerShell Workflow-runbooks](automation-runbook-types.md#powershell-workflow-runbooks)en [grafische runbooks weergegeven](automation-runbook-types.md#graphical-runbooks).

![Taakstatussen - PowerShell-werkstroom](./media/automation-runbook-execution/job-statuses.png)

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

>[!NOTE]
>Dit artikel is bijgewerkt voor het gebruik van de nieuwe Azure PowerShell Az-module. De AzureRM-module kan nog worden gebruikt en krijgt bugoplossingen tot ten minste december 2020. Zie voor meer informatie over de nieuwe Az-module en compatibiliteit met AzureRM [Introductie van de nieuwe Az-module van Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Zie [De Azure PowerShell-module installeren](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)voor installatie-instructies voor az-modules op uw hybride runbookworker. Voor uw Automatiseringsaccount u uw modules bijwerken naar de nieuwste versie met [Azure PowerShell-modules bijwerken in Azure Automation.](automation-update-azure-modules.md)

## <a name="where-to-run-your-runbooks"></a>Waar u uw runbooks uitvoeren?

Runbooks in Azure Automation kunnen worden uitgevoerd op een Azure sandbox of een [Hybride Runbook Worker.](automation-hybrid-runbook-worker.md) U de meeste runbooks eenvoudig uitvoeren in een Azure-sandbox, een gedeelde omgeving die meerdere taken kunnen gebruiken. Taken met dezelfde sandbox zijn gebonden aan de resourcebeperkingen van de sandbox.

>[!NOTE]
>De Azure sandbox-omgeving biedt geen ondersteuning voor interactieve bewerkingen. Het vereist ook het gebruik van lokale MOF-bestanden voor runbooks die Win32-gesprekken voeren.

U een hybride runbookworker gebruiken om boeken rechtstreeks uit te voeren op de computer die de rol host en tegen lokale bronnen in de omgeving. Azure Automation slaat runbooks op en beheert deze vervolgens op een of meer toegewezen computers.

In de volgende tabel worden enkele uitvoeringstaken voor runbook weergegeven met de aanbevolen uitvoeringsomgeving voor elk.

|Taak|Beste keuze|Opmerkingen|
|---|---|---|
|Integreren met Azure-bronnen|Azure Sandbox|Gehost in Azure is verificatie eenvoudiger. Als u een hybride runbookworker op een Azure VM gebruikt, u [beheerde identiteiten gebruiken voor Azure-resources.](automation-hrw-run-runbooks.md#managed-identities-for-azure-resources)|
|Optimale prestaties verkrijgen om Azure-resources te beheren|Azure Sandbox|Script wordt uitgevoerd in dezelfde omgeving, die minder latentie heeft.|
|Minimaliseer de operationele kosten|Azure Sandbox|Er is geen rekenkosten en geen vm nodig.|
|Langlopend script uitvoeren|Hybrid Runbook Worker|Azure-sandboxes hebben [beperkingen op resources.](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)|
|Interactie met lokale services|Hybrid Runbook Worker|Kan directe toegang hebben tot de hostmachine.|
|Software en uitvoerbare software van derden vereisen|Hybrid Runbook Worker|U beheert het besturingssysteem en software installeren.|
|Een bestand of map bewaken met een runbook|Hybrid Runbook Worker|Gebruik een [Watcher-taak](automation-watchers-tutorial.md) op een hybride runbookworker.|
|Een resourceintensief script uitvoeren|Hybrid Runbook Worker| Azure-sandboxes hebben [beperkingen op resources.](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)|
|Modules gebruiken met specifieke vereisten| Hybrid Runbook Worker|Een aantal voorbeelden:</br> WinSCP - afhankelijkheid van winscp.exe </br> IISAdministration - afhankelijkheid van het inschakelen van IIS.|
|Een module installeren met een installateur|Hybrid Runbook Worker|Modules voor sandbox moeten kopiëren ondersteunen.|
|Runbooks of modules gebruiken waarvoor een andere .NET Framework-versie nodig is dan 4.7.2|Hybrid Runbook Worker|Automatiseringssandboxes hebben .NET Framework 4.7.2 en er is geen manier om de versie te upgraden.|
|Scripts uitvoeren waarvoor hoogte nodig is|Hybrid Runbook Worker|Zandbakken staan geen hoogte toe. Met een hybride runbookworker u UAC uitschakelen en [Aanroep-command](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/invoke-command?view=powershell-7) gebruiken wanneer u de opdracht uitvoert waarvoor hoogte vereist is.|
|Scripts uitvoeren waarvoor toegang tot WmI (Windows Management Instrumentation) vereist is|Hybrid Runbook Worker|Taken die worden uitgevoerd in zandbakken in de cloud, hebben geen toegang tot WMI. |

## <a name="runbook-behavior"></a>Runbook-gedrag

### <a name="creating-resources"></a>Resources maken

Als uw runbook een resource maakt, moet het script controleren of de bron al bestaat voordat u deze probeert te maken. Hier is een basisvoorbeeld.

```powershell
$vmName = "WindowsVM1"
$resourceGroupName = "myResourceGroup"
$myCred = Get-AutomationPSCredential "MyCredential"
$vmExists = Get-AzResource -Name $vmName -ResourceGroupName $resourceGroupName

if(!$vmExists)
    {
    Write-Output "VM $vmName does not exist, creating"
    New-AzureRMVM -Name $vmName -ResourceGroupName $resourceGroupName -Credential $myCred
    }
else
    {
    Write-Output "VM $vmName already exists, skipping"
    }
```

### <a name="supporting-time-dependent-scripts"></a>Ondersteunde tijdsafhankelijke scripts

Uw runbooks moeten robuust zijn en in staat zijn om tijdelijke fouten te verwerken die ervoor kunnen zorgen dat ze opnieuw worden opgestart of mislukken. Als een runbook mislukt, probeert Azure Automation het opnieuw.

Als uw runbook normaal gesproken binnen een tijdsdruk wordt uitgevoerd, moet u de logica voor het implementeren van het script uitvoeren om de uitvoeringstijd te controleren. Deze controle zorgt ervoor dat bewerkingen zoals opstarten, afsluiten of uitschalen alleen op bepaalde tijdstippen worden uitgevoerd.

> [!NOTE]
> De lokale tijd in het Azure sandbox-proces is ingesteld op UTC. Berekeningen voor datum en tijd in uw runbooks moeten rekening houden met dit feit.

### <a name="tracking-progress"></a>Voortgang bijhouden

Het is een goede gewoonte om uw runbooks auteur te zijn modulair van aard, met logica die gemakkelijk kan worden hergebruikt en opnieuw opgestart. Het bijhouden van de voortgang in een runbook is een goede manier om ervoor te zorgen dat de runbook-logica correct wordt uitgevoerd als er problemen zijn. Het is mogelijk om de voortgang van een runbook bij te houden met behulp van een externe bron, zoals een opslagaccount, een database of gedeelde bestanden. U logica maken in uw runbook om eerst de status van de laatste actie te controleren. Vervolgens kan de logica, op basis van het resultaat van de controle, specifieke taken in het runbook overslaan of voortzetten.

### <a name="preventing-concurrent-jobs"></a>Gelijktijdige taken voorkomen

Sommige runbooks gedragen zich vreemd als ze lopen over meerdere banen op hetzelfde moment. In dit geval is het belangrijk dat een runbook logica implementeert om te bepalen of er al een lopende taak is. Hier is een basisvoorbeeld.

```powershell
# Authenticate to Azure
$connection = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationId $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint

$AzContext = Select-AzSubscription -SubscriptionId $connection.SubscriptionID

# Check for already running or new runbooks
$runbookName = "<RunbookName>"
$rgName = "<ResourceGroupName>"
$aaName = "<AutomationAccountName>"
$jobs = Get-AzAutomationJob -ResourceGroupName $rgName -AutomationAccountName $aaName -RunbookName $runbookName -AzContext $AzureContext

# Check to see if it is already running
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

Om meerdere abonnementen af te handelen, moet je runbook de cmdlet [Disable-AzContextAutosave](https://docs.microsoft.com/powershell/module/Az.Accounts/Disable-AzContextAutosave?view=azps-3.5.0) gebruiken. Deze cmdlet zorgt ervoor dat de verificatiecontext niet wordt opgehaald uit een andere runbook die in dezelfde sandbox wordt uitgevoerd. Het runbook maakt`AzContext` ook gebruik van de parameter op de Az module cmdlets en geeft het de juiste context.

```powershell
# Ensures that you do not inherit an AzContext in your runbook
Disable-AzContextAutosave –Scope Process

$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal `
-Tenant $Conn.TenantID `
-ApplicationId $Conn.ApplicationID `
-CertificateThumbprint $Conn.CertificateThumbprint

$context = Get-AzContext

$ChildRunbookName = 'ChildRunbookDemo'
$AutomationAccountName = 'myAutomationAccount'
$ResourceGroupName = 'myResourceGroup'

Start-AzAutomationRunbook `
    -ResourceGroupName $ResourceGroupName `
    -AutomationAccountName $AutomationAccountName `
    -Name $ChildRunbookName `
    -DefaultProfile $context
```

### <a name="handling-exceptions"></a>Afhandeling van uitzonderingen

In deze sectie worden enkele manieren beschreven om uitzonderingen of onderbroken problemen in uw runbooks af te handelen.

#### <a name="erroractionpreference"></a>ErrorActionPreference

De variabele [ErrorActionPreference](/powershell/module/microsoft.powershell.core/about/about_preference_variables#erroractionpreference) bepaalt hoe PowerShell reageert op een niet-beëindigende fout. Beëindiging van fouten altijd eindigen `ErrorActionPreference`en worden niet beïnvloed door .

Wanneer het runbook wordt gebruikt, `ErrorActionPreference`stopt een `PathNotFound` normaal gesproken niet-beëindigende fout, zoals van de [cmdlet Get-ChildItem,](https://docs.microsoft.com/powershell/module/microsoft.powershell.management/get-childitem?view=powershell-7) waardoor het runbook niet wordt voltooid. In het volgende voorbeeld `ErrorActionPreference`wordt het gebruik van . De laatste opdracht [Schrijfuitvoer](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/write-output?view=powershell-7) wordt nooit uitgevoerd, omdat het script stopt.

```powershell-interactive
$ErrorActionPreference = 'Stop'
Get-ChildItem -path nofile.txt
Write-Output "This message will not show"
```

#### <a name="try-catch-finally"></a>Probeer Catch Tot slot

[Try Catch Finally](/powershell/module/microsoft.powershell.core/about/about_try_catch_finally) wordt gebruikt in PowerShell-scripts om beëindigingsfouten af te handelen. Het script kan dit mechanisme gebruiken om specifieke uitzonderingen of algemene uitzonderingen te vangen. De `catch` instructie moet worden gebruikt om fouten bij te houden of te proberen om te gaan. In het volgende voorbeeld wordt geprobeerd een bestand te downloaden dat niet bestaat. Het vangt `System.Net.WebException` de uitzondering en geeft de laatste waarde voor een andere uitzondering.

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

#### <a name="throw"></a>Gooien

[Gooien](/powershell/module/microsoft.powershell.core/about/about_throw) kan worden gebruikt om een beëindigingsfout te genereren. Dit mechanisme kan handig zijn bij het definiëren van uw eigen logica in een runbook. Als het script voldoet aan een criterium dat `throw` het moet stoppen, kan het de instructie gebruiken om te stoppen. In het volgende voorbeeld wordt deze instructie gebruikt om een vereiste functieparameter weer te geven.

```powershell-interactive
function Get-ContosoFiles
{
  param ($path = $(throw "The Path parameter is required."))
  Get-ChildItem -Path $path\*.txt -recurse
}
```

### <a name="using-executables-or-calling-processes"></a>Uitvoerbare of oproepprocessen gebruiken

Runbooks die worden uitgevoerd in Azure-sandboxes ondersteunen geen oproepprocessen, zoals uitvoerbare bestanden (**.exe-bestanden)** of subprocessen. De reden hiervoor is dat een Azure sandbox een gedeeld proces is dat wordt uitgevoerd in een container die mogelijk geen toegang heeft tot alle onderliggende API's. Voor scenario's waarvoor software van derden of oproepen naar subprocessen vereist zijn, moet u een runbook uitvoeren op een [hybride runbook worker.](automation-hybrid-runbook-worker.md)

### <a name="accessing-device-and-application-characteristics"></a>Toegangsapparaat en toepassingskenmerken

Runbook-taken die worden uitgevoerd in Azure-sandboxes hebben geen toegang tot de kenmerken van een apparaat of toepassing. De meest voorkomende API die wordt gebruikt om prestatiestatistieken op Te vragen op Windows is WMI, waarbij enkele van de algemene statistieken geheugen- en CPU-gebruik zijn. Het maakt echter niet uit welke API wordt gebruikt, omdat taken die in de cloud worden uitgevoerd, geen toegang hebben tot de Microsoft-implementatie van Web-Based Enterprise Management (WBEM). Dit platform is gebouwd op het Common Information Model (CIM), dat de industriestandaarden biedt voor het definiëren van apparaat- en toepassingskenmerken.

## <a name="handling-errors"></a>Afhandeling van fouten

Uw runbooks moeten in staat zijn om fouten te verwerken. PowerShell heeft twee soorten fouten, die eindigen en niet beëindigen. Als u fouten beëindigt, wordt de uitvoering van runbook gestopt wanneer deze zich voordoen. Het runbook stopt met een taakstatus van Mislukt.

Met niet-beëindigende fouten kan een script worden voortgezet, zelfs nadat deze zich hebben voorgedaan. Een voorbeeld van een niet-beëindigende fout is een `Get-ChildItem` fout die optreedt wanneer een runbook de cmdlet gebruikt met een pad dat niet bestaat. PowerShell ziet dat het pad niet bestaat, gooit een fout en gaat door naar de volgende map. De fout in dit geval stelt de status van runbook-taak niet in op Mislukt en de taak kan zelfs worden voltooid. Als u een runbook wilt dwingen om te stoppen `ErrorAction Stop` bij een niet-beëindigende fout, u de cmdlet gebruiken.

## <a name="handling-jobs"></a>Afhandeling van taken

U de uitvoeringsomgeving opnieuw gebruiken voor taken van hetzelfde automatiseringsaccount. Een enkel runbook kan veel taken tegelijk uitvoeren. Hoe meer taken u tegelijkertijd uitvoert, hoe vaker ze naar dezelfde sandbox kunnen worden verzonden.

Taken die in hetzelfde sandbox-proces worden uitgevoerd, kunnen van invloed zijn op elkaar. Een voorbeeld hiervan is het uitvoeren van de [cmdlet Disconnect-AzAccount.](https://docs.microsoft.com/powershell/module/az.accounts/disconnect-azaccount?view=azps-3.7.0) Uitvoering van deze cmdlet verbreekt elke runbook-taak in het gedeelde sandbox-proces.

PowerShell-taken zijn gestart vanuit een runbook die wordt uitgevoerd in een Azure-sandbox, mogelijk niet in de volledige [PowerShell-taalmodus.](/powershell/module/microsoft.powershell.core/about/about_language_modes) Zie [Taakstatus ophalen met PowerShell](#retrieving-job-status-using-powershell)voor meer informatie over interactie met taken in Azure Automation.

### <a name="job-statuses"></a>Functiestatussen

In de volgende tabel worden de statussen beschreven die mogelijk zijn voor een taak.

| Status | Beschrijving |
|:--- |:--- |
| Voltooid |De taak is voltooid. |
| Mislukt |Een grafische of PowerShell Workflow-runbook kan niet worden gecompileerd. Een PowerShell-scriptrunbook kan niet worden gestart of de taak had een uitzondering. Zie [Azure Automation runbook-typen](automation-runbook-types.md).|
| Mislukt, wachten op resources |De taak is mislukt omdat deze de [limiet voor een eerlijk aandeel](#fair-share) drie keer heeft bereikt en telkens vanaf hetzelfde controlepunt of vanaf het begin van het runbook is gestart. |
| In wachtrij |De taak wacht op resources op een automatiseringsmedewerker die beschikbaar is, zodat deze kan worden gestart. |
| Starten |De taak is toegewezen aan een werknemer en het systeem wordt gestart. |
| Hervatten |Het systeem hervat de taak nadat het werd opgeschort. |
| In uitvoering |De taak wordt uitgevoerd. |
| Uitvoeren, wachten op resources |De taak is gelost omdat het de fair share limiet heeft bereikt. Het zal binnenkort worden hervat vanaf zijn laatste controlepunt. |
| Gestopt |De gebruiker heeft de taak gestopt voordat deze is voltooid. |
| Stoppen |Het systeem stopt de klus. |
| Onderbroken |Is alleen van toepassing op [grafische en PowerShell Workflow runbooks.](automation-runbook-types.md) De taak is door de gebruiker, door het systeem of door een opdracht in het runbook onderbroken. Als een runbook geen controlepunt heeft, begint het vanaf het begin. Als het een controlepunt heeft, kan het opnieuw beginnen en vanaf zijn laatste controlepunt hervatten. Het systeem schort het runbook alleen op wanneer er een uitzondering optreedt. Standaard is `ErrorActionPreference` de variabele ingesteld op Doorgaan, wat aangeeft dat de taak blijft draaien op een fout. Als de voorkeursvariabele is ingesteld op Stoppen, wordt de taak op een fout opgeschort.  |
| Onderbreken |Is alleen van toepassing op [grafische en PowerShell Workflow runbooks.](automation-runbook-types.md) Het systeem probeert de taak op te schorten op verzoek van de gebruiker. Runbook moet het volgende controlepunt bereiken voordat de taak kan worden onderbroken. Als het al zijn laatste controlepunt is gepasseerd, wordt het voltooid voordat het kan worden opgeschort. |

### <a name="viewing-job-status-from-the-azure-portal"></a>Taakstatus weergeven vanuit de Azure-portal

U een statusoverzicht bekijken voor alle runbook-taken of inzoomen op details van een specifieke runbook-taak in de Azure-portal. U integratie met uw Log Analytics-werkruimte ook configureren om de status van runbook-taken en taakstromen door te sturen. Zie [Taakstatus en taakstromen doorsturen van Automatisering naar Azure Monitor-logboeken voor](automation-manage-send-joblogs-log-analytics.md)meer informatie over de integratie met Azure Monitor-logboeken.

Rechts van het geselecteerde automatiseringsaccount ziet u een overzicht van alle runbook-taken onder de tegel **Taakstatistieken.**

![Tegel Functiestatistieken](./media/automation-runbook-execution/automation-account-job-status-summary.png)

Deze tegel geeft een telling en grafische weergave weer van de taakstatus voor elke uitgevoerde taak.

Als u op de tegel klikt, wordt de pagina Vacatures weergegeven, die een samengevatte lijst bevat met alle uitgevoerde taken. Op deze pagina worden de status, de naam van het runbook, de begintijd en de voltooiingstijd voor elke taak weergegeven.

![Pagina Vacatures voor automatiseringsaccount](./media/automation-runbook-execution/automation-account-jobs-status-blade.png)

U de lijst met taken filteren door **Taken filteren**te selecteren. Filter op een specifiek runbook, taakstatus of een keuze uit de vervolgkeuzelijst en geef het tijdsbereik voor het zoeken op.

![Functiestatus filteren](./media/automation-runbook-execution/automation-account-jobs-filter.png)

U ook de details van het taakoverzicht voor een specifiek runbook bekijken door dat runbook te selecteren op de pagina Runbooks in uw automatiseringsaccount en vervolgens de tegel **Taken te** selecteren. Met deze actie wordt de pagina Vacatures weergegeven. Vanaf hier u op de taakrecord klikken om de details en uitvoer te bekijken.

![Pagina Vacatures voor automatiseringsaccount](./media/automation-runbook-execution/automation-runbook-job-summary-blade.png)

### <a name="viewing-the-job-summary"></a>Het overzicht van de taak bekijken

Met het hierboven beschreven overzicht van de taak u een lijst bekijken met alle taken die zijn gemaakt voor een bepaald runbook en hun meest recente statussen. Als u gedetailleerde informatie en uitvoer voor een taak wilt zien, klikt u op de naam in de lijst. De gedetailleerde weergave van de taak bevat de waarden voor de runbook-parameters die aan die taak zijn verstrekt.

U kunt de volgende stappen gebruiken om de taken voor een runbook weer te geven.

1. Selecteer in de Azure-portal **Automatisering** en selecteer vervolgens de naam van een automatiseringsaccount.
2. Selecteer in de hub **Runbooks** onder **Procesautomatisering**.
3. Selecteer op de pagina Runbooks een runbook in de lijst.
3. Klik op de pagina voor het geselecteerde runbook op de tegel **Vacatures.**
4. Klik op een van de taken in de lijst en bekijk de details en uitvoer op de pagina taakdetails van runbook.

### <a name="retrieving-job-status-using-powershell"></a>Taakstatus ophalen met PowerShell

Gebruik `Get-AzAutomationJob` de cmdlet om de taken op te halen die zijn gemaakt voor een runbook en de details van een bepaalde taak. Als u een runbook start `Start-AzAutomationRunbook`met PowerShell met PowerShell met , wordt de resulterende taak geretourneerd. Gebruik [Get-AzAutomationJobOutput](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJobOutput?view=azps-3.5.0) om de taakuitvoer op te halen.

In het volgende voorbeeld wordt de laatste taak voor een voorbeeldrunbook weergegeven en wordt de status ervan weergegeven, de waarden die zijn opgegeven voor de parameters van de runbook en de taakuitvoer.

```azurepowershell-interactive
$job = (Get-AzAutomationJob –AutomationAccountName "MyAutomationAccount" `
–RunbookName "Test-Runbook" -ResourceGroupName "ResourceGroup01" | sort LastModifiedDate –desc)[0]
$job.Status
$job.JobParameters
Get-AzAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAcct" -Id $job.JobId –Stream Output
```

In het volgende voorbeeld wordt de uitvoer voor een specifieke taak opgehaald en wordt elke record geretourneerd. Als er een uitzondering is voor een van de records, schrijft het script de uitzondering in plaats van de waarde. Dit gedrag is handig omdat uitzonderingen aanvullende informatie kunnen bieden die mogelijk niet normaal wordt geregistreerd tijdens de uitvoer.

```azurepowershell-interactive
$output = Get-AzAutomationJobOutput -AutomationAccountName <AutomationAccountName> -Id <jobID> -ResourceGroupName <ResourceGroupName> -Stream "Any"
foreach($item in $output)
{
    $fullRecord = Get-AzAutomationJobOutputRecord -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -JobId <jobID> -Id $item.StreamRecordId
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

## <a name="getting-details-from-the-activity-log"></a>Gegevens ophalen in het activiteitenlogboek

U runbook-gegevens, zoals de persoon of het account waarmee het runbook is gestart, ophalen in het activiteitenlogboek voor het automatiseringsaccount. In het volgende PowerShell-voorbeeld wordt de laatste gebruiker opgegeven die de opgegeven runbook uitvoert.

```powershell-interactive
$SubID = "00000000-0000-0000-0000-000000000000"
$AutomationResourceGroupName = "MyResourceGroup"
$AutomationAccountName = "MyAutomationAccount"
$RunbookName = "MyRunbook"
$StartTime = (Get-Date).AddDays(-1)
$JobActivityLogs = Get-AzLog -ResourceGroupName $AutomationResourceGroupName -StartTime $StartTime `
                                | Where-Object {$_.Authorization.Action -eq "Microsoft.Automation/automationAccounts/jobs/write"}

$JobInfo = @{}
foreach ($log in $JobActivityLogs)
{
    # Get job resource
    $JobResource = Get-AzResource -ResourceId $log.ResourceId

    if ($JobInfo[$log.SubmissionTimestamp] -eq $null -and $JobResource.Properties.runbook.name -eq $RunbookName)
    {
        # Get runbook
        $Runbook = Get-AzAutomationJob -ResourceGroupName $AutomationResourceGroupName -AutomationAccountName $AutomationAccountName `
                                            -Id $JobResource.Properties.jobId | ? {$_.RunbookName -eq $RunbookName}

        # Add job information to hashtable
        $JobInfo.Add($log.SubmissionTimestamp, @($Runbook.RunbookName,$Log.Caller, $JobResource.Properties.jobId))
    }
}
$JobInfo.GetEnumerator() | sort key -Descending | Select-Object -First 1
```

## <a name="sharing-resources-among-runbooks"></a><a name="fair-share"></a>Bronnen delen tussen runbooks

Als u resources wilt delen tussen alle runbooks in de cloud, ontlaadt of stopt Azure Automation tijdelijk elke taak die langer dan drie uur is uitgevoerd. Taken voor [PowerShell-runbooks](automation-runbook-types.md#powershell-runbooks) en [Python-runbooks](automation-runbook-types.md#python-runbooks) worden gestopt en niet opnieuw gestart en de taakstatus wordt Gestopt.

Voor langlopende taken wordt aanbevolen om een hybride runbookworker te gebruiken. Hybride Runbook-werknemers worden niet beperkt door een eerlijk aandeel en hebben geen beperking op hoe lang een runbook kan worden uitgevoerd. De andere [taaklimieten](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits) zijn van toepassing op zowel Azure-sandboxes als hybride runbook-werknemers. Hoewel hybride runbook-werknemers niet worden beperkt door de limiet voor eerlijke delen van 3 uur, moet u runbooks ontwikkelen om te draaien op de werknemers die opnieuw opstarten ondersteunen van onverwachte lokale infrastructuurproblemen.

Een andere optie is het optimaliseren van een runbook met behulp van onderliggende runbooks. Uw runbook kan bijvoorbeeld dezelfde functie doorlopen op verschillende bronnen, zoals een databasebewerking in verschillende databases. U deze functie verplaatsen naar een [onderliggend runbook](automation-child-runbooks.md) en uw runbook laten aanroepen met [Start-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.7.0). Onderliggende runbooks worden parallel uitgevoerd in afzonderlijke processen.

Als u onderliggende runbooks gebruikt, wordt de totale tijd die het bovenliggende runbook moet voltooien, beperkt. Uw runbook kan de cmdlet [Get-AzAutomationJob](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationjob?view=azps-3.7.0) gebruiken om de taakstatus voor een onderliggend e-book te controleren als het nog steeds meer bewerkingen heeft nadat het kind is voltooid.

## <a name="next-steps"></a>Volgende stappen

* Zie [Runbooks beheren in Azure Automation](manage-runbooks.md)voor meer informatie over het werken met een runbook.
* Zie [Een runbook starten in Azure Automation](automation-starting-a-runbook.md)voor meer informatie over de methoden die kunnen worden gebruikt om een runbook te starten in Azure Automation.
* Zie de [PowerShell-documenten](https://docs.microsoft.com/powershell/scripting/overview)voor meer informatie over PowerShell, inclusief taalverwijzing en leermodules.
