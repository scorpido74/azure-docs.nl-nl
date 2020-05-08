---
title: Runbooks in Azure Automation beheren
description: In dit artikel wordt beschreven hoe u runbooks beheert in Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 02/14/2019
ms.topic: conceptual
ms.openlocfilehash: e2f66f94415b7a10fe540cf1c796f4b93349895a
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/06/2020
ms.locfileid: "82855464"
---
# <a name="manage-runbooks-in-azure-automation"></a>Runbooks in Azure Automation beheren

U kunt een runbook toevoegen aan Azure Automation door ofwel [een nieuwe te maken](#create-a-runbook) of [een bestaande te importeren](#import-a-runbook) uit een bestand of de [runbook-galerie](automation-runbook-gallery.md). Dit artikel bevat informatie over het maken en importeren van runbooks vanuit een bestand. U kunt alle Details ophalen van toegang tot de Community-runbooks en-modules in [Runbook en module galerieën voor Azure Automation](automation-runbook-gallery.md).

>[!NOTE]
>Dit artikel is bijgewerkt voor het gebruik van de nieuwe Azure PowerShell Az-module. De AzureRM-module kan nog worden gebruikt en krijgt bugoplossingen tot ten minste december 2020. Zie voor meer informatie over de nieuwe Az-module en compatibiliteit met AzureRM [Introductie van de nieuwe Az-module van Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Zie [de module Azure PowerShell installeren](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)voor de installatie-instructies voor AZ module op uw Hybrid Runbook Worker. Voor uw Automation-account kunt u uw modules bijwerken naar de nieuwste versie met behulp van [het bijwerken van Azure PowerShell-modules in azure Automation](automation-update-azure-modules.md).

## <a name="create-a-runbook"></a>Een runbook maken

U kunt een nieuw runbook maken in Azure Automation met behulp van een van de Azure-portals of Windows Power shell. Zodra het runbook is gemaakt, kunt u het bewerken met behulp van informatie in de [Power shell-werk stroom](automation-powershell-workflow.md) voor het leren en [grafisch ontwerpen in azure Automation](automation-graphical-authoring-intro.md).

### <a name="create-a-runbook-in-the-azure-portal"></a>Een runbook maken in de Azure Portal

1. Open uw Automation-account in Azure Portal.
2. Selecteer in de hub **Runbooks** onder **proces automatisering** om de lijst met Runbooks te openen.
3. Klik op **een Runbook maken**.
4. Voer een naam in voor het runbook en selecteer het bijbehorende [type](automation-runbook-types.md). De naam van het runbook moet beginnen met een letter en mag letters, cijfers, onderstrepings tekens en streepjes bevatten.
5. Klik op **maken** om het runbook te maken en de editor te openen.

### <a name="create-a-runbook-with-powershell"></a>Een runbook maken met Power shell

U kunt de cmdlet [New-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationrunbook?view=azps-3.5.0) gebruiken om een leeg [Power shell workflow-runbook](automation-runbook-types.md#powershell-workflow-runbooks)te maken. Gebruik de `Type` para meter om een van de typen runbook op te `New-AzAutomationRunbook`geven die zijn gedefinieerd voor.

In het volgende voor beeld ziet u hoe u een nieuw leeg runbook maakt.

```azurepowershell-interactive
New-AzAutomationRunbook -AutomationAccountName MyAccount `
-Name NewRunbook -ResourceGroupName MyResourceGroup -Type PowerShell
```

## <a name="import-a-runbook"></a>Een runbook importeren

U kunt een nieuw runbook maken in Azure Automation door een Power shell-script of Power shell-werk stroom (**. ps1**), een geëxporteerd grafisch runbook (**. graphrunbook**) of een Python2-script (**. py**) te importeren.  U moet het [type runbook](automation-runbook-types.md) opgeven dat tijdens het importeren wordt gemaakt, waarbij rekening wordt gehouden met de volgende overwegingen.

* U kunt een **. ps1** -bestand importeren dat geen werk stroom bevat in een [Power shell-Runbook](automation-runbook-types.md#powershell-runbooks) of een [Power shell workflow-runbook](automation-runbook-types.md#powershell-workflow-runbooks). Als u deze importeert in een Power shell workflow-runbook, wordt dit geconverteerd naar een werk stroom. In dit geval worden opmerkingen in het runbook opgenomen om de wijzigingen te beschrijven die zijn aangebracht.

* U kunt alleen een **. ps1** -bestand met een Power shell-werk stroom importeren in een [Power shell workflow-runbook](automation-runbook-types.md#powershell-workflow-runbooks). Als het bestand meerdere Power shell-werk stromen bevat, mislukt het importeren. U moet elke werk stroom opslaan in een eigen bestand en deze afzonderlijk importeren.

* Importeer geen **. ps1** -bestand met een Power shell-werk stroom in een [Power shell-runbook](automation-runbook-types.md#powershell-runbooks), omdat het niet door de Power shell-script engine kan worden herkend.

* U kunt een **. graphrunbook** -bestand alleen importeren in een nieuw [grafisch runbook](automation-runbook-types.md#graphical-runbooks). Houd er rekening mee dat u alleen een grafisch runbook kunt maken van een **. graphrunbook** -bestand.

### <a name="import-a-runbook-from-a-file-with-the-azure-portal"></a>Een runbook importeren vanuit een bestand met de Azure Portal

U kunt de volgende procedure gebruiken om een script bestand te importeren in Azure Automation.

> [!NOTE]
> U kunt een **. ps1** -bestand alleen importeren in een Power shell workflow-runbook met behulp van de portal.

1. Open uw Automation-account in Azure Portal.
2. Selecteer in de hub **Runbooks** onder **proces automatisering** om de lijst met Runbooks te openen.
3. Klik op **een Runbook importeren**.
4. Klik op **Runbook-bestand** en selecteer het bestand dat u wilt importeren.
5. Als het veld **naam** is ingeschakeld, kunt u de naam van het runbook wijzigen. De naam moet beginnen met een letter en mag letters, cijfers, onderstrepings tekens en streepjes bevatten.
6. Het [runbook-type](automation-runbook-types.md) wordt automatisch geselecteerd, maar u kunt het type wijzigen nadat u de toepasselijke beperkingen in het account hebt genomen.
7. Klik op **Maken**. Het nieuwe runbook wordt weer gegeven in de lijst met runbooks voor het Automation-account.
8. U moet [het runbook publiceren](#publish-a-runbook) voordat u het kunt uitvoeren.

> [!NOTE]
> Nadat u een grafisch runbook of een grafisch Power shell-werk stroom-runbook hebt geïmporteerd, kunt u deze converteren naar een ander type. U kunt een van deze grafische runbooks echter niet converteren naar een tekst runbook.

### <a name="import-a-runbook-from-a-script-file-with-windows-powershell"></a>Een runbook importeren vanuit een script bestand met Windows Power shell

Gebruik de cmdlet [import-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/import-azautomationrunbook?view=azps-3.5.0) om een script bestand te importeren als een concept Power shell workflow-runbook. Als het runbook al bestaat, mislukt het importeren tenzij u de `Force` para meter met de cmdlet gebruikt.

In het volgende voor beeld ziet u hoe u een script bestand importeert in een runbook.

```azurepowershell-interactive
$automationAccountName =  "AutomationAccount"
$runbookName = "Sample_TestRunbook"
$scriptPath = "C:\Runbooks\Sample_TestRunbook.ps1"
$RGName = "ResourceGroup"

Import-AzAutomationRunbook -Name $runbookName -Path $scriptPath `
-ResourceGroupName $RGName -AutomationAccountName $automationAccountName `
-Type PowerShellWorkflow
```

## <a name="handle-resources"></a>Resources verwerken

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

## <a name="retrieve-details-from-activity-log"></a>Details ophalen uit het activiteiten logboek

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

## <a name="track-progress"></a>Voortgang bijhouden

Het is een goed idee om uw runbooks te maken die kunnen worden gemigreerd, met logica die opnieuw kan worden gebruikt en eenvoudig opnieuw kan worden gestart. Het bijhouden van de voortgang in een runbook is een goede manier om ervoor te zorgen dat de runbook-logica correct wordt uitgevoerd als er problemen zijn. Het is mogelijk om de voortgang van een runbook bij te houden met behulp van een externe bron, zoals een opslag account, een Data Base of gedeelde bestanden. U kunt logica in uw runbook maken om eerst de status van de laatste uitgevoerde actie te controleren. Op basis van het resultaat van de controle kan de logica specifieke taken in het runbook overs Laan of voortzetten.

## <a name="prevent-concurrent-jobs"></a>Gelijktijdige taken voor komen

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

## <a name="handle-transient-errors-in-a-time-dependent-script"></a>Tijdelijke fouten verwerken in een script dat afhankelijk is van tijd

Uw runbooks moeten robuust en geschikt zijn voor het afhandelen van tijdelijke fouten die ertoe kunnen leiden dat ze opnieuw worden opgestart of mislukken. Als een runbook is mislukt, Azure Automation opnieuw proberen.

Als uw runbook normaal gesp roken binnen een tijds beperking wordt uitgevoerd, laat u het script de logica implementeren om de uitvoerings tijd te controleren. Met deze controle wordt de uitvoering van bewerkingen, zoals opstarten, afsluiten of uitschalen, alleen tijdens bepaalde tijden gegarandeerd.

> [!NOTE]
> De lokale tijd op het Azure sandbox-proces is ingesteld op UTC. Berekeningen voor de datum en tijd in uw runbooks moeten in overweging worden genomen.

## <a name="work-with-multiple-subscriptions"></a>Met meerdere abonnementen werken

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

## <a name="work-with-a-custom-script"></a>Werken met een aangepast script

U kunt doorgaans geen aangepaste scripts en runbooks uitvoeren op de host waarop een Log Analytics-agent is geïnstalleerd. Als u dit moet doen:

1. Een Automation-account maken en een rol voor Inzender verkrijgen.
2. [Koppel het account aan de Azure-werk ruimte](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection.md).
3. Schakel de Hybrid Runbook Worker, Updatebeheer of een andere functie voor automatisering in. 
4. Als u op een Linux-computer beschikt over hoge machtigingen. Meld u aan om [handtekening controles uit te scha kelen](automation-linux-hrw-install.md#turn-off-signature-validation).

## <a name="test-a-runbook"></a>Een runbook testen

Wanneer u een runbook test, wordt de [concept versie](#publish-a-runbook) uitgevoerd en de acties die worden uitgevoerd, worden voltooid. Er is geen taak geschiedenis gemaakt, maar de [uitvoer](automation-runbook-output-and-messages.md#output-stream) -en [waarschuwings-en fout](automation-runbook-output-and-messages.md#message-streams) stromen worden weer gegeven in het deel venster test uitvoer. Berichten naar de [uitgebreide stroom](automation-runbook-output-and-messages.md#message-streams) worden alleen weer gegeven in het deel venster uitvoer als de variabele [VerbosePreference](automation-runbook-output-and-messages.md#preference-variables) is ingesteld `Continue`op.

Hoewel de concept versie wordt uitgevoerd, wordt het runbook nog steeds normaal uitgevoerd en worden alle acties uitgevoerd voor resources in de omgeving. Daarom moet u runbooks alleen testen op niet-productie resources.

De procedure voor het testen [van elk type runbook](automation-runbook-types.md) is hetzelfde. Er is geen verschil in het testen tussen de tekst editor en de grafische editor in de Azure Portal.

1. Open de concept versie van het runbook in de [tekst editor](automation-edit-textual-runbook.md) of de [grafische editor](automation-graphical-authoring-intro.md).
1. Klik op de knop **testen** om de pagina test te openen.
1. Als het runbook para meters heeft, worden deze weer gegeven in het linkerdeel venster, waar u waarden kunt opgeven die moeten worden gebruikt voor de test.
1. Als u de test wilt uitvoeren op een [Hybrid Runbook worker](automation-hybrid-runbook-worker.md), wijzigt u de instellingen voor het **uitvoeren** van de **Hybrid worker** en selecteert u de naam van de doel groep.  Als dat niet het geval is, moet u de test in de Cloud uitvoeren met de standaard **Azure** .
1. Klik op de knop **Start** om de test te starten.
1. U kunt de knoppen onder het deel venster uitvoer gebruiken om een [Power shell-werk stroom](automation-runbook-types.md#powershell-workflow-runbooks) of [grafisch](automation-runbook-types.md#graphical-runbooks) runbook te stoppen of te onderbreken tijdens het testen. Wanneer u het runbook onderbreekt, wordt de huidige activiteit voltooid voordat het runbook wordt onderbroken. Als het runbook is onderbroken, kunt u het stoppen of opnieuw starten.
1. Controleer de uitvoer van het runbook in het deel venster uitvoer.

## <a name="publish-a-runbook"></a>Een runbook publiceren

Wanneer u een nieuw runbook maakt of importeert, moet u het publiceren voordat u het kunt uitvoeren. Elk runbook in Azure Automation heeft een concept versie en een gepubliceerde versie. Alleen de gepubliceerde versie is beschikbaar om te worden uitgevoerd en alleen de conceptversie kan worden bewerkt. De gepubliceerde versie wordt niet beïnvloed door wijzigingen in de conceptversie. Wanneer de concept versie beschikbaar moet worden gemaakt, publiceert u deze en wordt de huidige gepubliceerde versie overschreven met de concept versie.

### <a name="publish-a-runbook-in-the-azure-portal"></a>Een runbook publiceren in de Azure Portal

1. Open het runbook in de Azure Portal.
2. Klik op **Bewerken**.
3. Klik op **publiceren** en vervolgens op **Ja** in antwoord op het verificatie bericht.

### <a name="publish-a-runbook-using-powershell"></a>Een runbook publiceren met Power shell

Gebruik de cmdlet [Publish-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Publish-AzAutomationRunbook?view=azps-3.5.0) om een runbook te publiceren met Windows Power shell. In het volgende voor beeld ziet u hoe u een voor beeld-runbook publiceert.

```azurepowershell-interactive
$automationAccountName =  "AutomationAccount"
$runbookName = "Sample_TestRunbook"
$RGName = "ResourceGroup"

Publish-AzAutomationRunbook -AutomationAccountName $automationAccountName `
-Name $runbookName -ResourceGroupName $RGName
```

## <a name="schedule-a-runbook-in-the-azure-portal"></a>Een runbook plannen in de Azure Portal

Als uw runbook is gepubliceerd, kunt u dit plannen voor de bewerking.

1. Open het runbook in de Azure Portal.
2. Selecteer **schema's** onder **resources**.
3. Selecteer **een schema toevoegen**.
4. Selecteer **een planning aan uw Runbook koppelen**in het deel venster Runbook plannen.
5. Kies **een nieuw schema maken** in het deel venster planning.
6. Voer een naam, beschrijving en andere para meters in het deel venster nieuwe planning in. 
7. Wanneer de planning is gemaakt, markeert u deze en klikt u op **OK**. Deze moet nu zijn gekoppeld aan uw runbook.
8. Zoek een e-mail in uw postvak om u te informeren over de status van het runbook.

## <a name="obtain-job-statuses"></a>Taak statussen ophalen

### <a name="view-statuses-in-the-azure-portal"></a>Statussen weer geven in de Azure Portal

Rechts van het geselecteerde Automation-account ziet u een samen vatting van alle runbook-taken onder de tegel **taak statistieken** . De details van het verwerken van taken in Azure Automation worden gegeven in de uitvoering van een [Runbook in azure Automation](automation-runbook-execution.md#jobs).

![Taak statistieken tegel](./media/manage-runbooks/automation-account-job-status-summary.png)

Deze tegel bevat een aantal en een grafische weer gave van de taak status van elke uitgevoerde taak.

Als u op de tegel klikt, wordt de pagina taken weer gegeven, die een overzicht bevat van alle uitgevoerde taken. Op deze pagina worden de status, de runbook-naam, de start tijd en de voltooiings tijd voor elke taak weer gegeven.

![Pagina Automation-account taken](./media/manage-runbooks/automation-account-jobs-status-blade.png)

U kunt de lijst met taken filteren door **taken filteren**te selecteren. Filter op een specifiek runbook, de taak status of een keuze in de vervolg keuzelijst en geef het tijds bereik voor de zoek opdracht op.

![Taak status filteren](./media/manage-runbooks/automation-account-jobs-filter.png)

U kunt ook samenvattings Details van taken weer geven voor een specifiek runbook door dat runbook te selecteren op de pagina Runbooks in uw Automation-account en vervolgens de tegel **taken** te selecteren. Deze actie geeft de pagina taken weer. Hier kunt u klikken op de taak record om de details en de uitvoer weer te geven.

![Pagina Automation-account taken](./media/manage-runbooks/automation-runbook-job-summary-blade.png)

### <a name="retrieve-job-statuses-using-powershell"></a>Taak statussen ophalen met behulp van Power shell

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

## <a name="next-steps"></a>Volgende stappen

* Zie [Runbook-uitvoering in azure Automation](automation-runbook-execution.md)voor meer informatie over het uitvoeren van runbooks.
* Zie [runbook-en module galerieën voor Azure Automation voor](automation-runbook-gallery.md)meer informatie over hoe u kunt profiteren van de galerie met runbook-en Power shell-modules.
* Zie voor meer informatie over het bewerken van Power shell-en Power shell-werk stroom runbooks met een tekst editor [tekst Runbooks bewerken in azure Automation](automation-edit-textual-runbook.md).
* Zie [grafisch ontwerpen in azure Automation](automation-graphical-authoring-intro.md)voor meer informatie over het ontwerpen van een grafisch runbook.
