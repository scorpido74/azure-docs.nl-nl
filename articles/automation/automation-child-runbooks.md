---
title: Onderliggende runbooks in Azure Automation
description: Beschrijft de verschillende methoden voor het starten van een runbook in Azure Automation vanuit een ander runbook en het delen van informatie tussen deze boeken.
services: automation
ms.subservice: process-automation
ms.date: 01/17/2019
ms.topic: conceptual
ms.openlocfilehash: 42362a170f493afd51a5d4ee139620ad25b54e79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367360"
---
# <a name="child-runbooks-in-azure-automation"></a>Onderliggende runbooks in Azure Automation

Het is een aanbevolen praktijk in Azure Automation om herbruikbare, modulaire runbooks te schrijven met een discrete functie die wordt aangeroepen door andere runbooks. Een bovenliggende runbook roept vaak een of meer onderliggende runbooks aan om de vereiste functionaliteit uit te voeren. Er zijn twee manieren om een onderliggende runbook aan te roepen, en er zijn verschillende verschillen die u moet begrijpen om te kunnen bepalen welke het beste is voor uw scenario's.

>[!NOTE]
>Dit artikel is bijgewerkt voor het gebruik van de nieuwe Azure PowerShell Az-module. De AzureRM-module kan nog worden gebruikt en krijgt bugoplossingen tot ten minste december 2020. Zie voor meer informatie over de nieuwe Az-module en compatibiliteit met AzureRM [Introductie van de nieuwe Az-module van Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Zie [De Azure PowerShell-module installeren](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)voor installatie-instructies voor az-modules op uw hybride runbookworker. Voor uw Automatiseringsaccount u uw modules bijwerken naar de nieuwste versie met [Azure PowerShell-modules bijwerken in Azure Automation.](automation-update-azure-modules.md)

## <a name="invoking-a-child-runbook-using-inline-execution"></a>Een onderliggende runbook aanroepen met inline uitvoering

Als u een runbook-inline uit een ander runbook wilt aanroepen, gebruikt u de naam van het runbook en geeft u waarden op voor de parameters, net zoals u een activiteit of een cmdlet zou gebruiken. Alle runbooks in hetzelfde Automation-account zijn beschikbaar voor alle andere boeken die op deze manier kunnen worden gebruikt. De bovenliggende runbook wacht tot het onderliggende runbook is voltooid voordat u naar de volgende regel gaat en de uitvoer wordt rechtstreeks naar de bovenliggende lijn geretourneerd.

Als u een runbook inline aanroept, wordt dit uitgevoerd in dezelfde taak als het bovenliggende runbook. Er is geen indicatie in de taakgeschiedenis van het onderliggende runbook. Eventuele uitzonderingen en streamuitvoer uit het onderliggende runbook zijn gekoppeld aan de bovenliggende. Dit gedrag resulteert in minder taken en maakt ze gemakkelijker te volgen en op te lossen.

Wanneer een runbook wordt gepubliceerd, moeten alle onderliggende runbooks die het aanroept al worden gepubliceerd. De reden hiervoor is dat Azure Automation een koppeling opbouwt met onderliggende runbooks wanneer het een runbook compileert. Als de onderliggende runbooks nog niet zijn gepubliceerd, wordt het bovenliggende runbook correct gepubliceerd, maar genereert het een uitzondering wanneer het wordt gestart. Als dit gebeurt, u het bovenliggende runbook opnieuw publiceren om naar behoren naar de onderliggende runbooks te verwijzen. U hoeft het bovenliggende runbook niet opnieuw te publiceren als een onderliggende runbook is gewijzigd omdat de koppeling al is gemaakt.

De parameters van een onderliggende runbook genaamd inline kunnen van elk gegevenstype zijn, inclusief complexe objecten. Er is geen [JSON serialisatie,](start-runbooks.md#runbook-parameters)zoals wanneer u de runbook start met behulp van de Azure-portal of met de [cmdlet Start-AzAutomationRunbook.](/powershell/module/Az.Automation/Start-AzAutomationRunbook)

### <a name="runbook-types"></a>Runbooktypen

Welke runbook types kunnen elkaar bellen?

* Een [PowerShell-runbook](automation-runbook-types.md#powershell-runbooks) en een [grafisch runbook](automation-runbook-types.md#graphical-runbooks) kunnen elkaar inline noemen, omdat beide op PowerShell zijn gebaseerd.
* Een [PowerShell Workflow-runbook](automation-runbook-types.md#powershell-workflow-runbooks) en een grafische PowerShell Workflow-runbook kunnen elkaar inline aanroepen, omdat beide op PowerShell-workflow zijn gebaseerd.
* De PowerShell-typen en de PowerShell-werkstroomtypen kunnen elkaar `Start-AzAutomationRunbook`niet inline aanroepen en moeten .

Wanneer doet het publiceren van bestellingen ertoe?

De publicatievolgorde van runbooks is alleen van belang voor PowerShell Workflow en grafische PowerShell Workflow runbooks.

Wanneer uw runbook een grafische of PowerShell Workflow onderliggende runbook aanroept met inline uitvoering, wordt de naam van het runbook gebruikt. De naam moet `.\\` beginnen met op te geven dat het script zich in de lokale map bevindt.

### <a name="example"></a>Voorbeeld

In het volgende voorbeeld wordt een testonderliggende boek gestart dat een complex object, een gehele waarde en een booleaanse waarde accepteert. De uitvoer van het onderliggende runbook is toegewezen aan een variabele. In dit geval is de onderliggende runbook een PowerShell-werkstroomrunboek.

```azurepowershell-interactive
$vm = Get-AzVM –ResourceGroupName "LabRG" –Name "MyVM"
$output = PSWF-ChildRunbook –VM $vm –RepeatCount 2 –Restart $true
```

Hier is hetzelfde voorbeeld met behulp van een PowerShell runbook als het kind.

```azurepowershell-interactive
$vm = Get-AzVM –ResourceGroupName "LabRG" –Name "MyVM"
$output = .\PS-ChildRunbook.ps1 –VM $vm –RepeatCount 2 –Restart $true
```

## <a name="starting-a-child-runbook-using-a-cmdlet"></a>Een onderliggend runbook starten met een cmdlet

> [!IMPORTANT]
> Als uw runbook een onderliggend runbook aanroept met de `Start-AzAutomationRunbook` cmdlet met de `Wait` parameter en het onderliggende runbook een objectresultaat oplevert, kan er een fout in de bewerking ontstaan. Zie [Onderliggende runbooks met objectuitvoer](troubleshoot/runbooks.md#child-runbook-object) om te leren hoe u de logica implementeert die u wilt peilen voor de resultaten met de cmdlet [Get-AzAutomationJobOutputRecord](/powershell/module/az.automation/get-azautomationjoboutputrecord) om de fout te omzeilen.

U kunt `Start-AzAutomationRunbook` een runbook starten zoals beschreven in [Een runbook starten met Windows PowerShell](start-runbooks.md#start-a-runbook-with-powershell). Er zijn twee gebruikswijzen voor deze cmdlet. In één modus retourneert de cmdlet de taak-id wanneer de taak wordt gemaakt voor het onderliggende runbook. In de andere modus, die uw script inschakelt door de parameter *Wachten* op te geven, wacht de cmdlet totdat de onderliggende taak is voltooid en retourneert de uitvoer uit het onderliggende runbook.

De taak van een onderliggende runbook is gestart met een cmdlet wordt apart uitgevoerd van de bovenliggende runbook-taak. Dit gedrag resulteert in meer taken dan het starten van de runbook inline, en maakt de taken moeilijker te volgen. De ouder kan meer dan één onderliggende runbook asynchroon starten zonder te wachten tot elk van deze is voltooid. Voor deze parallelle uitvoering die de onderliggende runbooks inline aanroept, moet het [bovenliggende](automation-powershell-workflow.md#parallel-processing)runbook het parallelle trefwoord gebruiken.

Onderliggende runbook-uitvoer keert niet op betrouwbare wijze terug naar het bovenliggende runbook vanwege de timing. Bovendien worden variabelen zoals `$VerbosePreference` `$WarningPreference`, en andere niet gepropageerd naar de onderliggende runbooks. Om deze problemen te voorkomen, u de `Start-AzAutomationRunbook` onderliggende `Wait` runbooks starten als afzonderlijke automatiseringstaken met behulp van de parameter. Deze techniek blokkeert het bovenliggende runbook totdat het onderliggende runbook is voltooid.

Als u niet wilt dat het bovenliggende runbook wordt geblokkeerd bij het `Start-AzAutomationRunbook` wachten, u het onderliggende runbook starten zonder de `Wait` parameter. In dit geval moet uw runbook [Get-AzAutomationJob](/powershell/module/az.automation/get-azautomationjob) gebruiken om te wachten op voltooiing van de taak. Het moet ook [Get-AzAutomationJobOutput](/powershell/module/az.automation/get-azautomationjoboutput) en [Get-AzAutomationJobOutputRecord](/powershell/module/az.automation/get-azautomationjoboutputrecord) gebruiken om de resultaten op te halen.

Parameters voor een onderliggend e-book die met een cmdlet zijn gestart, worden geleverd als een hashtabel, zoals beschreven in [runbook-parameters.](start-runbooks.md#runbook-parameters) Alleen eenvoudige gegevenstypen kunnen worden gebruikt. Als het runbook een parameter met een complex gegevenstype heeft, moet dit inline worden aangeroepen.

De abonnementscontext kan verloren gaan bij het starten van onderliggende runbooks als afzonderlijke taken. Voor het onderliggende runbook om az-modulecmdlets uit te voeren tegen een specifiek Azure-abonnement, moet het kind zich onafhankelijk van het bovenliggende runbook aan dit abonnement verifiëren.

Als taken binnen hetzelfde Automatiseringsaccount met meer dan één abonnement werken, kan het selecteren van een abonnement in één taak de momenteel geselecteerde abonnementscontext voor andere taken wijzigen. Gebruik `Disable-AzContextAutosave –Scope Process` aan het begin van elk runbook aan het begin van elk runbook. Deze actie slaat alleen de context op in die runbook-uitvoering.

### <a name="example"></a>Voorbeeld

In het volgende voorbeeld wordt een onderliggende runbook met `Start-AzAutomationRunbook` parameters gestart en `Wait` wordt vervolgens gewacht tot deze is voltooid met behulp van de cmdlet met de parameter. Eenmaal voltooid, verzamelt het voorbeeld cmdlet-uitvoer uit het onderliggende runbook. Als `Start-AzAutomationRunbook`u het script wilt gebruiken, moet het zich verifiëren voor uw Azure-abonnement.

```azurepowershell-interactive
# Ensure that the runbook does not inherit an AzContext
Disable-AzContextAutosave –Scope Process

# Connect to Azure with Run As account
$ServicePrincipalConnection = Get-AutomationConnection -Name 'AzureRunAsConnection'

Connect-AzAccount `
    -ServicePrincipal `
    -Tenant $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint

$AzureContext = Get-AzSubscription -SubscriptionId $ServicePrincipalConnection.SubscriptionID

$params = @{"VMName"="MyVM";"RepeatCount"=2;"Restart"=$true}

Start-AzAutomationRunbook `
    –AutomationAccountName 'MyAutomationAccount' `
    –Name 'Test-ChildRunbook' `
    -ResourceGroupName 'LabRG' `
    -AzContext $AzureContext `
    –Parameters $params –Wait
```

## <a name="comparison-of-methods-for-calling-a-child-runbook"></a>Vergelijking van methoden voor het aanroepen van een onderliggend runbook

In de volgende tabel worden de verschillen tussen de twee manieren samengevat om een runbook uit een ander runbook aan te roepen.

|  | Inline | Cmdlet |
|:--- |:--- |:--- |
| Taak |Onderliggende runbooks worden uitgevoerd in dezelfde taak als het bovenliggende runbook. |Er wordt een afzonderlijke taak gemaakt voor het onderliggende runbook. |
| Uitvoering |Bovenliggend runbook wacht tot het onderliggende runbook is voltooid voordat het verdergaat. |Bovenliggende runbook gaat verder onmiddellijk nadat onderliggende runbook is gestart *of* ouder runbook wacht tot de onderliggende taak is voltooid. |
| Uitvoer |Bovenliggend runbook kan uitvoer rechtstreeks uit onderliggend runbook verkrijgen. |Bovenliggende runbook moet uitvoer ophalen uit onderliggende runbook-taak *of* bovenliggende runbook kan rechtstreeks uitvoer krijgen uit onderliggende runbook. |
| Parameters |Waarden voor de parameters van onderliggend runbook worden afzonderlijk opgegeven en kunnen elk gegevenstype hebben. |Waarden voor de onderliggende runbook-parameters moeten worden gecombineerd tot één hashtabel. Deze hashtable kan alleen eenvoudige, array- en objectgegevenstypen bevatten die JSON-serialisatie gebruiken. |
| Automation-account |Bovenliggende runbook kan alleen onderliggende runbook gebruiken in hetzelfde Automatiseringsaccount. |Bovenliggende runbooks kunnen een onderliggende runbook gebruiken van elk Automatiseringsaccount, van hetzelfde Azure-abonnement en zelfs vanaf een ander abonnement waarmee u een verbinding hebt. |
| Publiceren |Onderliggend runbook moet zijn gepubliceerd voordat bovenliggend runbook wordt gepubliceerd. |Onderliggend eerboek wordt op elk moment gepubliceerd voordat het bovenliggende runbook wordt gestart. |

## <a name="next-steps"></a>Volgende stappen

* [Een runbook starten in Azure Automation](start-runbooks.md)
* [Uitvoer van runbook en berichten in Azure Automation](automation-runbook-output-and-messages.md)