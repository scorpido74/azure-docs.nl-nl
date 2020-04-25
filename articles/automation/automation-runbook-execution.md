---
title: Uitvoering van runbooks in Azure Automation
description: Hierin worden de details van het verwerken van een runbook in Azure Automation beschreven.
services: automation
ms.subservice: process-automation
ms.date: 04/14/2020
ms.topic: conceptual
ms.openlocfilehash: 09122581a3ade4e741a29996b7202ce0f96d074b
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82145533"
---
# <a name="runbook-execution-in-azure-automation"></a>Uitvoering van runbooks in Azure Automation

Proces automatisering in Azure Automation kunt u Power shell-, Power shell-werk stroom en grafische runbooks maken en beheren. Zie [Azure Automation runbooks](automation-runbook-types.md)voor meer informatie. 

Met Automation worden uw runbooks uitgevoerd op basis van de logica die hierin is gedefinieerd. Als een runbook wordt onderbroken, wordt het aan het begin opnieuw opgestart. Dit gedrag vereist dat u runbooks schrijft die ondersteuning bieden voor opnieuw opstarten als tijdelijke problemen optreden.

Als u een runbook in Azure Automation start, wordt er een taak gemaakt. Dit is één uitvoerings instantie van het runbook. Elke taak heeft toegang tot Azure-resources door verbinding te maken met uw Azure-abonnement. De taak kan alleen toegang krijgen tot resources in uw Data Center als deze bronnen toegankelijk zijn vanuit de open bare Cloud.

Azure Automation wijst een werk nemer toe om elke taak uit te voeren tijdens de uitvoering van het runbook. Terwijl werk rollen worden gedeeld door veel Azure-accounts, zijn taken van verschillende Automation-accounts van elkaar geïsoleerd. U kunt niet bepalen welke Worker-Services uw taak aanvragen.

Wanneer u de lijst met runbooks in de Azure Portal weergeeft, wordt de status weer gegeven van elke taak die voor elk runbook is gestart. Met Azure Automation worden taak logboeken Maxi maal 30 dagen opgeslagen.

In het volgende diagram ziet u de levens cyclus van een runbook-taak voor [Power shell-runbooks](automation-runbook-types.md#powershell-runbooks), [Power shell-werk stroom runbooks](automation-runbook-types.md#powershell-workflow-runbooks)en [grafische runbooks](automation-runbook-types.md#graphical-runbooks).

![Taak statussen-Power shell-werk stroom](./media/automation-runbook-execution/job-statuses.png)

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

>[!NOTE]
>Dit artikel is bijgewerkt voor het gebruik van de nieuwe Azure PowerShell Az-module. De AzureRM-module kan nog worden gebruikt en krijgt bugoplossingen tot ten minste december 2020. Zie voor meer informatie over de nieuwe Az-module en compatibiliteit met AzureRM [Introductie van de nieuwe Az-module van Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Zie [de module Azure PowerShell installeren](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)voor de installatie-instructies voor AZ module op uw Hybrid Runbook Worker. Voor uw Automation-account kunt u uw modules bijwerken naar de nieuwste versie met behulp van [het bijwerken van Azure PowerShell-modules in azure Automation](automation-update-azure-modules.md).

## <a name="where-to-run-your-runbooks"></a>Waar worden uw runbooks uitgevoerd?

Runbooks in Azure Automation kunnen worden uitgevoerd op een Azure sandbox of een [Hybrid Runbook worker](automation-hybrid-runbook-worker.md). Wanneer runbooks zijn ontworpen om te verifiëren en uit te voeren op resources in azure, worden ze uitgevoerd in een Azure-sandbox. Dit is een gedeelde omgeving die door meerdere taken kan worden gebruikt. Taken die gebruikmaken van dezelfde sandbox zijn gebonden aan de resource beperkingen van de sandbox.

>[!NOTE]
>De Azure sandbox-omgeving biedt geen ondersteuning voor interactieve bewerkingen. Ook is het gebruik van lokale MOF-bestanden vereist voor runbooks die Win32-aanroepen maken.

U kunt een Hybrid Runbook Worker gebruiken om runbooks rechtstreeks uit te voeren op de computer die de rol host en de lokale resources in de omgeving. Azure Automation runbooks opslaat en beheert en vervolgens aan een of meer toegewezen computers worden geleverd.

De volgende tabel bevat een aantal uitvoerings taken voor runbook met de aanbevolen uitvoerings omgeving die voor elk wordt vermeld.

|Taak|Aanbeveling|Opmerkingen|
|---|---|---|
|Integreren met Azure-resources|Azure sandbox|Verificatie is eenvoudiger in Azure. Als u een Hybrid Runbook Worker op een virtuele Azure-machine gebruikt, kunt u [beheerde identiteiten gebruiken voor Azure-resources](automation-hrw-run-runbooks.md#managed-identities-for-azure-resources).|
|Optimale prestaties verkrijgen om Azure-resources te beheren|Azure sandbox|Script wordt uitgevoerd in dezelfde omgeving, met minder latentie.|
|Beperk operationele kosten|Azure sandbox|Er zijn geen berekenings overhead en er is geen virtuele machine nodig.|
|Langlopend script uitvoeren|Hybrid Runbook Worker|Azure-sandboxes hebben [resource limieten](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits).|
|Interactie met lokale services|Hybrid Runbook Worker|Kan rechtstreeks toegang krijgen tot de hostmachine of bronnen in andere Cloud omgevingen of in uw on-premises omgeving. |
|Software en uitvoer bare bestanden van derden vereisen|Hybrid Runbook Worker|U beheert het besturings systeem en kan software installeren.|
|Een bestand of map bewaken met een runbook|Hybrid Runbook Worker|Een [Watcher-taak](automation-watchers-tutorial.md) gebruiken op een Hybrid Runbook Worker.|
|Een resource-intensieve script uitvoeren|Hybrid Runbook Worker| Azure-sandboxes hebben [resource limieten](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits).|
|Modules met specifieke vereisten gebruiken| Hybrid Runbook Worker|Een aantal voorbeelden:</br> WinSCP-afhankelijkheid op WinSCP. exe </br> IIS-beheer: afhankelijkheid van het inschakelen of beheren van IIS.|
|Een module installeren met een installatie programma|Hybrid Runbook Worker|Modules voor sandbox moeten kopiëren ondersteunen.|
|Runbooks of modules gebruiken waarvoor .NET Framework versie anders is dan 4.7.2|Hybrid Runbook Worker|Automation-sandboxes ondersteunen .NET Framework 4.7.2 en de upgrade naar een andere versie wordt niet ondersteund.|
|Scripts uitvoeren waarvoor uitbrei ding van bevoegdheden is vereist|Hybrid Runbook Worker|Sandboxes staan geen uitbrei ding van bevoegdheden toe. Met een Hybrid Runbook Worker kunt u UAC uitschakelen en [invoke-opdracht](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/invoke-command?view=powershell-7) gebruiken bij het uitvoeren van de opdracht die uitbrei ding vereist.|
|Scripts uitvoeren waarvoor toegang tot Windows Management Instrumentation is vereist (WMI)|Hybrid Runbook Worker|Taken die worden uitgevoerd in sandboxes in de Cloud hebben geen toegang tot de WMI-provider. |

## <a name="using-modules-in-your-runbooks"></a>Modules in uw runbooks gebruiken

Azure Automation ondersteunt een aantal standaard modules, met inbegrip van de AzureRM-modules (AzureRM. Automation) en een module met meerdere interne cmdlets. Ook worden Installeer bare modules ondersteund, met inbegrip van de AZ-modules (AZ. Automation), die momenteel worden gebruikt in de voor keur voor AzureRM-modules. Zie [modules beheren in azure Automation](shared-resources/modules.md)voor meer informatie over de modules die beschikbaar zijn voor RUNBOOKS en DSC-configuraties.

## <a name="creating-resources"></a>Resources maken

Als uw runbook een resource maakt, moet u controleren of de resource al bestaat voordat u deze probeert te maken. Hier volgt een voor beeld van een basis.

```powershell
$vmName = "WindowsVM1"
$resourceGroupName = "myResourceGroup"
$myCred = Get-AutomationPSCredential "MyCredential"
$vmExists = Get-AzResource -Name $vmName -ResourceGroupName $resourceGroupName

if(!$vmExists)
    {
    Write-Output "VM $vmName does not exist, creating"
    New-AzVM -Name $vmName -ResourceGroupName $resourceGroupName -Credential $myCred
    }
else
    {
    Write-Output "VM $vmName already exists, skipping"
    }
```

## <a name="supporting-time-dependent-scripts"></a>Ondersteunende scripts voor tijd afhankelijke

Uw runbooks moeten robuust en geschikt zijn voor het afhandelen van tijdelijke fouten die ertoe kunnen leiden dat ze opnieuw worden opgestart of mislukken. Als een runbook is mislukt, Azure Automation opnieuw proberen.

Als uw runbook normaal gesp roken binnen een tijds beperking wordt uitgevoerd, laat u het script de logica implementeren om de uitvoerings tijd te controleren. Met deze controle wordt de uitvoering van bewerkingen, zoals opstarten, afsluiten of uitschalen, alleen tijdens bepaalde tijden gegarandeerd.

> [!NOTE]
> De lokale tijd op het Azure sandbox-proces is ingesteld op UTC. Berekeningen voor de datum en tijd in uw runbooks moeten in overweging worden genomen.

## <a name="tracking-progress"></a>Voortgang bijhouden

Het is een goed idee om uw runbooks te maken die kunnen worden gemigreerd, met logica die opnieuw kan worden gebruikt en eenvoudig opnieuw kan worden gestart. Het bijhouden van de voortgang in een runbook is een goede manier om ervoor te zorgen dat de runbook-logica correct wordt uitgevoerd als er problemen zijn. Het is mogelijk om de voortgang van een runbook bij te houden met behulp van een externe bron, zoals een opslag account, een Data Base of gedeelde bestanden. U kunt logica in uw runbook maken om eerst de status van de laatste uitgevoerde actie te controleren. Op basis van het resultaat van de controle kan de logica specifieke taken in het runbook overs Laan of voortzetten.

## <a name="preventing-concurrent-jobs"></a>Gelijktijdige taken voor komen

Sommige runbooks gedragen zich niet alleen als ze in meerdere taken tegelijk worden uitgevoerd. In dit geval is het belang rijk dat een runbook logica implementeert om te bepalen of er al een actieve taak is. Hier volgt een voor beeld van een basis.

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

## <a name="working-with-multiple-subscriptions"></a>Met meerdere abonnementen werken

Voor het oplossen van meerdere abonnementen moet uw runbook de cmdlet [Disable-AzContextAutosave](https://docs.microsoft.com/powershell/module/Az.Accounts/Disable-AzContextAutosave?view=azps-3.5.0) gebruiken. Met deze cmdlet zorgt u ervoor dat de verificatie context niet wordt opgehaald uit een ander runbook dat wordt uitgevoerd in dezelfde sandbox. Het runbook gebruikt ook de`AzContext` para meter in de AZ module-cmdlets en geeft deze de juiste context.

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

## <a name="handling-exceptions"></a>Afhandeling van uitzonderingen

In deze sectie worden enkele manieren beschreven voor het afhandelen van uitzonde ringen of onregelmatige problemen in uw runbooks. Een voor beeld is een WebSocket-uitzonde ring. Door de verwerking van uitzonde ringen te corrigeren, kunnen tijdelijke netwerk fouten ertoe leiden dat uw runbooks mislukken. 

### <a name="erroractionpreference"></a>ErrorActionPreference

De variabele [ErrorActionPreference](/powershell/module/microsoft.powershell.core/about/about_preference_variables#erroractionpreference) bepaalt hoe Power shell reageert op een niet-afsluit fout. Afsluit fouten worden altijd beëindigd en worden niet beïnvloed door `ErrorActionPreference`.

Wanneer het runbook wordt `ErrorActionPreference`gebruikt, wordt een normaal niet-afsluit fout, `PathNotFound` zoals van de cmdlet [Get-Child item](https://docs.microsoft.com/powershell/module/microsoft.powershell.management/get-childitem?view=powershell-7) , het runbook gestopt. In het volgende voor beeld ziet u `ErrorActionPreference`het gebruik van. De laatste opdracht voor [Write-output](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/write-output?view=powershell-7) wordt nooit uitgevoerd, omdat het script stopt.

```powershell-interactive
$ErrorActionPreference = 'Stop'
Get-ChildItem -path nofile.txt
Write-Output "This message will not show"
```

### <a name="try-catch-finally"></a>Probeer catch ten slotte

[Try catch finally](/powershell/module/microsoft.powershell.core/about/about_try_catch_finally) wordt gebruikt in Power shell-scripts voor het afhandelen van afsluit fouten. Het script kan dit mechanisme gebruiken om specifieke uitzonde ringen of algemene uitzonde ringen te ondervangen. De `catch` instructie moet worden gebruikt om fouten bij te houden of te proberen af te handelen. In het volgende voor beeld wordt geprobeerd een bestand te downloaden dat niet bestaat. De `System.Net.WebException` uitzonde ring wordt onderschept en de laatste waarde voor een andere uitzonde ring wordt geretourneerd.

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

### <a name="throw"></a>Sprong

[Throw](/powershell/module/microsoft.powershell.core/about/about_throw) kan worden gebruikt om een afsluit fout te genereren. Dit mechanisme kan nuttig zijn bij het definiëren van uw eigen logica in een runbook. Als het script voldoet aan een criterium waardoor het wordt gestopt, kan het `throw` de instructie gebruiken om te stoppen. In het volgende voor beeld wordt deze instructie gebruikt om een vereiste functie parameter weer te geven.

```powershell-interactive
function Get-ContosoFiles
{
  param ($path = $(throw "The Path parameter is required."))
  Get-ChildItem -Path $path\*.txt -recurse
}
```

## <a name="using-executables-or-calling-processes"></a>Uitvoer bare bestanden of aanroepende processen gebruiken

Runbooks die worden uitgevoerd in azure-sandboxes bieden geen ondersteuning voor het aanroepen van processen, zoals uitvoer bare bestanden (**. exe** ) of subprocessen. De reden hiervoor is dat een Azure sandbox een gedeeld proces is dat wordt uitgevoerd in een container die mogelijk geen toegang heeft tot alle onderliggende Api's. Voor scenario's waarvoor software van derden of aanroepen naar subprocessen is vereist, moet u een runbook uitvoeren op een [Hybrid Runbook worker](automation-hybrid-runbook-worker.md).

## <a name="accessing-device-and-application-characteristics"></a>Eigenschappen van het apparaat en de toepassing openen

Runbook-taken die worden uitgevoerd in azure-sandboxes hebben geen toegang tot de kenmerken van apparaten of toepassingen. De meest voorkomende API die wordt gebruikt om de metrische gegevens over prestaties in Windows op te vragen, is WMI, met een aantal algemene metrische gegevens die geheugen en CPU-gebruik zijn. Het maakt echter niet uit welke API wordt gebruikt, omdat taken die in de cloud worden uitgevoerd, geen toegang hebben tot de micro soft-implementatie van Web-Based Enterprise Management (WBEM). Dit platform is gebaseerd op de Common Information Model (CIM) en biedt de industrie normen voor het definiëren van kenmerken van apparaten en toepassingen.

## <a name="handling-errors"></a>Afhandeling van fouten

Uw runbooks moeten fouten kunnen afhandelen. Power Shell heeft twee typen fouten, eindigend en niet-beëindigen. Bij het beëindigen van fouten wordt de uitvoering van het runbook gestopt wanneer deze zich voordoen. Het runbook stopt met de taak status mislukt.

Met niet-afsluit fouten kan een script worden voortgezet, zelfs nadat het is uitgevoerd. Een voor beeld van een niet-afsluit fout is een van de volgende situaties wanneer een runbook `Get-ChildItem` gebruikmaakt van de cmdlet met een pad dat niet bestaat. Power shell ziet dat het pad niet bestaat, een fout genereert en naar de volgende map gaat. De fout in dit geval stelt de status van de runbook-taak niet in op mislukt en de taak kan zelfs zijn voltooid. Als u wilt afdwingen dat een runbook stopt bij een niet-afsluit fout, kunt `ErrorAction Stop` u gebruiken op de cmdlet.

## <a name="handling-jobs"></a>Taken verwerken

U kunt de uitvoerings omgeving hergebruiken voor taken van hetzelfde Automation-account. Eén runbook kan meerdere taken tegelijk uitvoeren. Hoe meer taken u tegelijkertijd uitvoert, hoe vaak deze naar dezelfde sandbox kunnen worden verzonden.

Taken die worden uitgevoerd in hetzelfde sandbox-proces, kunnen van invloed zijn op elkaar. De cmdlet [Disconnect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/disconnect-azaccount?view=azps-3.7.0) wordt uitgevoerd op een voor beeld. Uitvoering van deze cmdlet verbreekt de verbinding van elke runbook-taak in het gedeelde sandbox-proces.

Power shell-taken die zijn gestart vanuit een runbook dat wordt uitgevoerd in een Azure-sandbox, worden mogelijk niet uitgevoerd in de volledige [Power shell-taal modus](/powershell/module/microsoft.powershell.core/about/about_language_modes). Zie de [taak status ophalen met Power shell](#retrieving-job-status-using-powershell)voor meer informatie over interactie met taken in azure Automation.

### <a name="job-statuses"></a>Taak status

De volgende tabel beschrijft de statussen die mogelijk zijn voor een taak.

| Status | Beschrijving |
|:--- |:--- |
| Voltooid |De taak is voltooid. |
| Mislukt |Het compileren van een grafisch of Power shell-werk stroom runbook is mislukt. Een Power shell-script-runbook kan niet worden gestart of er is een uitzonde ring opgetreden in de taak. Zie [Azure Automation typen runbook](automation-runbook-types.md).|
| Mislukt, wachten op resources |De taak is mislukt omdat de limiet voor de [billijke share](#fair-share) drie keer is bereikt en vanaf hetzelfde controle punt of vanaf het begin van het runbook elke keer is gestart. |
| In wachtrij |Er wordt gewacht tot resources op een Automation-werk nemer beschikbaar zijn, zodat deze kunnen worden gestart. |
| Starten |De taak is toegewezen aan een werk nemer en het systeem wordt gestart. |
| Hervatten |Het systeem hervat de taak nadat deze is onderbroken. |
| In uitvoering |De taak wordt uitgevoerd. |
| Uitvoeren, wachten op resources |De taak is uit het geheugen verwijderd omdat de limiet voor de billijke share is bereikt. Het wordt binnenkort hervat vanaf het laatste controle punt. |
| Gestopt |De gebruiker heeft de taak gestopt voordat deze is voltooid. |
| Stoppen |Het systeem stopt de taak. |
| Onderbroken |Is alleen van toepassing op [grafische en Power shell-werk stroom-runbooks](automation-runbook-types.md) . De taak is door de gebruiker, door het systeem of door een opdracht in het runbook onderbroken. Als een runbook geen controle punt heeft, begint het vanaf het begin. Als er een controle punt is, kan het opnieuw worden gestart en wordt hervat vanaf het laatste controle punt. Het systeem onderbreekt alleen het runbook wanneer er een uitzonde ring optreedt. De `ErrorActionPreference` variabele wordt standaard ingesteld om door te gaan, wat aangeeft dat de taak wordt uitgevoerd op een fout. Als de voorkeurs variabele is ingesteld op Stop, wordt een fout door de taak onderbroken.  |
| Onderbreken |Is alleen van toepassing op [grafische en Power shell-werk stroom-runbooks](automation-runbook-types.md) . Het systeem probeert de taak op verzoek van de gebruiker te onderbreken. Runbook moet het volgende controlepunt bereiken voordat de taak kan worden onderbroken. Als het laatste controle punt al is door gegeven, wordt het voltooid voordat het kan worden onderbroken. |

### <a name="viewing-job-status-from-the-azure-portal"></a>Taak status weer geven vanuit de Azure Portal

U kunt een status overzicht weer geven voor alle runbook-taken of Details van een specifieke runbook-taak in de Azure Portal. U kunt ook integratie met uw Log Analytics-werk ruimte configureren om de taak status en taak stromen van een runbook door te sturen. Zie voor meer informatie over het integreren met Azure Monitor-logboeken [taak status door sturen van automatisering naar Azure monitor-logboeken](automation-manage-send-joblogs-log-analytics.md).

Rechts van het geselecteerde Automation-account ziet u een samen vatting van alle runbook-taken onder de tegel **taak statistieken** .

![Taak statistieken tegel](./media/automation-runbook-execution/automation-account-job-status-summary.png)

Deze tegel bevat een aantal en een grafische weer gave van de taak status van elke uitgevoerde taak.

Als u op de tegel klikt, wordt de pagina taken weer gegeven, die een overzicht bevat van alle uitgevoerde taken. Op deze pagina worden de status, de runbook-naam, de start tijd en de voltooiings tijd voor elke taak weer gegeven.

![Pagina Automation-account taken](./media/automation-runbook-execution/automation-account-jobs-status-blade.png)

U kunt de lijst met taken filteren door **taken filteren**te selecteren. Filter op een specifiek runbook, de taak status of een keuze in de vervolg keuzelijst en geef het tijds bereik voor de zoek opdracht op.

![Taak status filteren](./media/automation-runbook-execution/automation-account-jobs-filter.png)

U kunt ook samenvattings Details van taken weer geven voor een specifiek runbook door dat runbook te selecteren op de pagina Runbooks in uw Automation-account en vervolgens de tegel **taken** te selecteren. Deze actie geeft de pagina taken weer. Hier kunt u klikken op de taak record om de details en de uitvoer weer te geven.

![Pagina Automation-account taken](./media/automation-runbook-execution/automation-runbook-job-summary-blade.png)

### <a name="viewing-the-job-summary"></a>De samen vatting van de taak weer geven

Met het eerder beschreven taak overzicht kunt u een lijst bekijken met alle taken die voor een bepaald runbook zijn gemaakt en de meest recente statussen. Als u gedetailleerde informatie en uitvoer voor een taak wilt weer geven, klikt u op de naam in de lijst. De gedetailleerde weer gave van de taak bevat de waarden voor de runbook-para meters die zijn opgegeven voor die taak.

U kunt de volgende stappen gebruiken om de taken voor een runbook weer te geven.

1. Selecteer in de Azure Portal **Automation** en selecteer vervolgens de naam van een Automation-account.
2. Selecteer in de hub **Runbooks** onder **proces automatisering**.
3. Selecteer op de pagina Runbooks een runbook in de lijst.
3. Klik op de pagina voor het geselecteerde runbook op de tegel **taken** .
4. Klik op een van de taken in de lijst en Bekijk de details en uitvoer op de pagina met details van de runbook-taak.

### <a name="retrieving-job-status-using-powershell"></a>De taak status ophalen met behulp van Power shell

Gebruik de cmdlet [Get-AzAutomationJob](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJob?view=azps-3.7.0) om de taken op te halen die voor een runbook zijn gemaakt en de details van een bepaalde taak. Als u een runbook start met Power shell `Start-AzAutomationRunbook`met, wordt de resulterende taak geretourneerd. Gebruik [Get-AzAutomationJobOutput](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJobOutput?view=azps-3.5.0) om taak uitvoer op te halen.

In het volgende voor beeld wordt de laatste taak voor een voor beeld-runbook opgehaald en worden de status, de opgegeven waarden voor de runbook-para meters en de taak uitvoer weer gegeven.

```azurepowershell-interactive
$job = (Get-AzAutomationJob –AutomationAccountName "MyAutomationAccount" `
–RunbookName "Test-Runbook" -ResourceGroupName "ResourceGroup01" | sort LastModifiedDate –desc)[0]
$job.Status
$job.JobParameters
Get-AzAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAcct" -Id $job.JobId –Stream Output
```

In het volgende voor beeld wordt de uitvoer voor een specifieke taak opgehaald en wordt elke record geretourneerd. Als er een uitzonde ring is voor een van de records, schrijft het script de uitzonde ring in plaats van de waarde. Dit gedrag is nuttig omdat uitzonde ringen aanvullende informatie kunnen bieden die mogelijk niet normaal in de uitvoer wordt geregistreerd.

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

## <a name="getting-details-from-the-activity-log"></a>Details ophalen uit het activiteiten logboek

U kunt de details van het runbook ophalen, zoals de persoon of het account waarmee het runbook is gestart, uit het activiteiten logboek voor het Automation-account. Het volgende Power shell-voor beeld bevat de laatste gebruiker om het opgegeven runbook uit te voeren.

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

## <a name="sharing-resources-among-runbooks"></a><a name="fair-share"></a>Resources delen tussen runbooks

Als u resources wilt delen tussen alle runbooks in de Cloud, wordt met Azure Automation tijdelijk een taak verwijderd die langer dan drie uur is uitgevoerd. Taken voor [Power shell-runbooks](automation-runbook-types.md#powershell-runbooks) en [python-runbooks](automation-runbook-types.md#python-runbooks) worden gestopt en niet opnieuw gestart en de taak status wordt gestopt.

Voor langlopende taken kunt u het beste een Hybrid Runbook Worker gebruiken. Hybrid Runbook Workers worden niet beperkt door een billijke share en hebben geen beperking voor de manier waarop een Runbook kan worden uitgevoerd. De overige taak [limieten](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits) zijn van toepassing op zowel Azure-sandboxes als Hybrid Runbook Workers. Hoewel Hybrid Runbook Workers niet worden beperkt door de limiet van 3 uur, billijke delen, moet u runbooks ontwikkelen die worden uitgevoerd op de werk nemers die het opnieuw opstarten van onverwachte problemen met de lokale infra structuur ondersteunen.

Een andere optie is om een runbook te optimaliseren door onderliggende runbooks te gebruiken. Uw runbook kan bijvoorbeeld een lus door lopen op verschillende resources, zoals een database bewerking in verschillende data bases. U kunt deze functie verplaatsen naar een [onderliggend runbook](automation-child-runbooks.md) en het runbook aanroepen met [Start-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.7.0). Onderliggende runbooks worden parallel uitgevoerd in afzonderlijke processen.

Als u onderliggende runbooks gebruikt, wordt de totale hoeveelheid tijd voor het volt ooien van het bovenliggende runbook verkleind. Uw runbook kan de cmdlet [Get-AzAutomationJob](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationjob?view=azps-3.7.0) gebruiken om de taak status voor een onderliggend runbook te controleren als deze nog meer bewerkingen heeft nadat het onderliggende proces is voltooid.

## <a name="next-steps"></a>Volgende stappen

* Zie [Runbooks beheren in azure Automation](manage-runbooks.md)voor meer informatie over het werken met een runbook.
* Zie [starten van een runbook in azure Automation](automation-starting-a-runbook.md)voor meer informatie over de methoden die kunnen worden gebruikt om een runbook te starten in azure Automation.
* Zie de [Power shell-documenten](https://docs.microsoft.com/powershell/scripting/overview)voor meer informatie over Power shell, inclusief taal referentie-en leer modules.
* Zie [AZ. Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
)(Engelstalig) voor een Power shell-cmdlet-verwijzing.
