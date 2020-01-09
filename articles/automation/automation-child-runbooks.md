---
title: Onderliggende runbooks in Azure Automation
description: Hierin worden de verschillende methoden beschreven voor het starten van een runbook in Azure Automation vanuit een ander runbook en het delen van gegevens ertussen.
services: automation
ms.subservice: process-automation
ms.date: 01/17/2019
ms.topic: conceptual
ms.openlocfilehash: a35ee69e6a167f4907294c88710d0484353d4cb2
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75367006"
---
# <a name="child-runbooks-in-azure-automation"></a>Onderliggende runbooks in Azure Automation

Het is een aanbevolen procedure in Azure Automation om herbruikbare, modulaire runbooks te schrijven met een discrete functie van andere runbooks. Een bovenliggend runbook roept vaak een of meer onderliggende runbooks aan om de vereiste functionaliteit uit te voeren. Er zijn twee manieren om een onderliggend runbook aan te roepen, en elk heeft verschillende verschillen die u moet begrijpen, zodat u kunt bepalen welke voor uw verschillende scenario's het meest geschikt zijn.

## <a name="invoking-a-child-runbook-using-inline-execution"></a>Aanroepen van een onderliggend runbook met inline-uitvoering

Als u wilt een runbook inline vanuit een ander runbook aanroepen, gebruikt u de naam van het runbook en geef waarden op voor de parameters ervan precies zoals wanneer u een activiteit of cmdlet.  Alle runbooks in hetzelfde Automation-account zijn beschikbaar voor alle anderen die op deze manier moeten worden gebruikt. Het bovenliggende runbook wacht tot het onderliggende runbook gereed is voordat het naar de volgende regel en eventuele uitvoer rechtstreeks naar het bovenliggende object wordt geretourneerd.

Als u een runbook inline aanroept, wordt deze uitgevoerd in dezelfde taak als het bovenliggende runbook. Er is geen indicatie van de taak geschiedenis van het onderliggende runbook dat is uitgevoerd. Eventuele uitzonde ringen en alle stroom uitvoer van het onderliggende runbook zijn gekoppeld aan de bovenliggende. Dit gedrag resulteert in minder taken en maakt het eenvoudiger om te volgen en problemen op te lossen, omdat eventuele uitzonde ringen die worden veroorzaakt door het onderliggende runbook en een van de stroom uitvoer ervan zijn gekoppeld aan de bovenliggende taak.

Wanneer een runbook wordt gepubliceerd, moeten alle onderliggende runbooks die worden aangeroepen, al worden gepubliceerd. Dit komt omdat Azure Automation een koppeling bouwt met een wille keurige onderliggende runbooks wanneer een runbook wordt gecompileerd. Als dat niet het geval is, wordt het bovenliggende runbook op de juiste manier gepubliceerd, maar er wordt een uitzonde ring gegenereerd wanneer het wordt gestart. Als dit het geval is, kunt u het bovenliggende runbook opnieuw publiceren om naar behoren naar de onderliggende runbooks te verwijzen. U hoeft het bovenliggende runbook niet opnieuw te publiceren als een van de onderliggende runbooks is gewijzigd, omdat de koppeling al is gemaakt.

De para meters van een onderliggend runbook met de naam inline kunnen elk gegevens type met complexe objecten zijn. Er is geen [JSON-serialisatie](start-runbooks.md#runbook-parameters) mogelijk wanneer u het runbook start met behulp van de Azure portal of met de cmdlet start-AzureRmAutomationRunbook.

### <a name="runbook-types"></a>Typen runbooks

Welke typen kunnen elkaar aanroepen:

* Een [Power shell-runbook](automation-runbook-types.md#powershell-runbooks) en [grafische runbooks](automation-runbook-types.md#graphical-runbooks) kunnen elkaar inline aanroepen (beide zijn Power shell gebaseerd).
* Een [Power shell workflow-runbook](automation-runbook-types.md#powershell-workflow-runbooks) en grafische Runbooks van Power shell kunnen elke andere inline aanroepen (beide zijn Power shell-werk stroom gebaseerd)
* De Power shell-typen en de Power shell-werk stroom typen kunnen niet elk ander inline aanroepen en moeten start-AzureRmAutomationRunbook gebruiken.

Wanneer is de publicatie order van belang:

* De publicatie volgorde van runbooks is alleen van belang voor Power shell-werk stromen en grafische runbooks in Power shell-werk stromen.

Wanneer u een onderliggend runbook voor grafische of Power shell-werk stromen aanroept met inline-uitvoering, gebruikt u de naam van het runbook.  Wanneer u een onderliggend Power shell-runbook aanroept, moet u de naam starten met *.\\* om op te geven dat het script zich in de lokale map bevindt.

### <a name="example"></a>Voorbeeld

In het volgende voor beeld wordt een onderliggend runbook voor een test gestart dat drie para meters, een complex object, een geheel getal en een Booleaanse waarde accepteert. De uitvoer van het onderliggende runbook is toegewezen aan een variabele.  In dit geval is het onderliggende runbook een Power shell workflow-runbook.

```azurepowershell-interactive
$vm = Get-AzureRmVM –ResourceGroupName "LabRG" –Name "MyVM"
$output = PSWF-ChildRunbook –VM $vm –RepeatCount 2 –Restart $true
```

Hier volgt een voor beeld van het gebruik van een Power shell-runbook als onderliggend object.

```azurepowershell-interactive
$vm = Get-AzureRmVM –ResourceGroupName "LabRG" –Name "MyVM"
$output = .\PS-ChildRunbook.ps1 –VM $vm –RepeatCount 2 –Restart $true
```

## <a name="starting-a-child-runbook-using-cmdlet"></a>Een onderliggend runbook starten met een cmdlet

> [!IMPORTANT]
> Als u een onderliggend runbook aanroept met de cmdlet `Start-AzureRmAutomationRunbook` met de `-Wait` switch en de resultaten van het onderliggende runbook een object zijn, kunnen er fouten optreden. Als u de fout wilt omzeilen, raadpleegt u [onderliggende runbooks met object uitvoer](troubleshoot/runbooks.md#child-runbook-object) voor meer informatie over het implementeren van de logica voor het controleren van de resultaten en het gebruik van de [Get-AzureRmAutomationJobOutputRecord](/powershell/module/azurerm.automation/get-azurermautomationjoboutputrecord)

U kunt de cmdlet [Start-AzureRmAutomationRunbook](/powershell/module/AzureRM.Automation/Start-AzureRmAutomationRunbook) gebruiken om een runbook te starten zoals beschreven in [om een runbook te starten met Windows Power shell](start-runbooks.md#start-a-runbook-with-powershell). Er zijn twee modi voor het gebruik van deze cmdlet.  In de ene modus retourneert de cmdlet de taak-id wanneer de onderliggende taak is gemaakt voor het onderliggende runbook.  In de andere modus, die u inschakelt door de **-wait-** para meter op te geven, wacht de cmdlet totdat de onderliggende taak is voltooid en de uitvoer van het onderliggende runbook wordt geretourneerd.

De taak van een onderliggend runbook dat is gestart met een cmdlet, wordt uitgevoerd in een afzonderlijke taak van het bovenliggende runbook. Dit gedrag resulteert in meer taken dan bij het starten van het runbook inline en maakt het lastig om het bij te houden. De bovenliggende site kan meer dan een onderliggend runbook asynchroon starten zonder te wachten tot de bewerking is voltooid. Voor dezelfde soort parallelle uitvoering de onderliggende runbooks inline aanroept, moet het bovenliggende runbook gebruiken de [parallelle sleutelwoord](automation-powershell-workflow.md#parallel-processing).

De uitvoer van onderliggende runbooks wordt op een betrouw bare manier niet geretourneerd naar het bovenliggende runbook vanwege een time-out. Ook kunnen bepaalde variabelen, zoals $VerbosePreference, $WarningPreference en anderen, niet worden door gegeven aan de onderliggende runbooks. Als u deze problemen wilt voor komen, kunt u de onderliggende runbooks starten als afzonderlijke automatiserings taken met behulp van de cmdlet `Start-AzureRmAutomationRunbook` met de `-Wait`-switch. Hiermee wordt het bovenliggende runbook geblokkeerd totdat het onderliggende runbook is voltooid.

Als u niet wilt dat het bovenliggende runbook wordt geblokkeerd tijdens het wachten, kunt u het onderliggende runbook starten met `Start-AzureRmAutomationRunbook` cmdlet zonder de `-Wait`-switch. U moet vervolgens `Get-AzureRmAutomationJob` gebruiken om te wachten tot de taak is voltooid en `Get-AzureRmAutomationJobOutput` en `Get-AzureRmAutomationJobOutputRecord` om de resultaten op te halen.

Parameters voor een onderliggend runbook gestart met een cmdlet worden opgegeven als hashtabel, zoals beschreven in [Runbookparameters](start-runbooks.md#runbook-parameters). Alleen eenvoudige gegevens typen kunnen worden gebruikt. Als het runbook een parameter met een complex gegevenstype heeft, klikt u vervolgens het moet dit inline worden aangeroepen.

De context van het abonnement kan verloren gaan bij het starten van onderliggende runbooks als afzonderlijke taken. Om het onderliggende runbook Azure RM-cmdlets uit te voeren op basis van een specifiek Azure-abonnement, moet het onderliggende runbook worden geverifieerd bij dit abonnement onafhankelijk van het bovenliggende runbook.

Als taken binnen hetzelfde Automation-account werken met meer dan één abonnement, kan het selecteren van een abonnement in de ene taak de momenteel geselecteerde abonnements context voor andere taken wijzigen. Als u dit probleem wilt voor komen, gebruikt u `Disable-AzureRmContextAutosave –Scope Processsave` aan het begin van elk runbook. Met deze actie wordt de context alleen opgeslagen bij de uitvoering van het runbook.

### <a name="example"></a>Voorbeeld

In het volgende voor beeld wordt een onderliggend runbook met para meters gestart en wordt gewacht tot het is voltooid met de para meter start-AzureRmAutomationRunbook-wait. Zodra dit is voltooid, wordt de uitvoer van het onderliggende runbook verzameld. Als u `Start-AzureRmAutomationRunbook`wilt gebruiken, moet u zich verifiëren bij uw Azure-abonnement.

```azurepowershell-interactive
# Ensures you do not inherit an AzureRMContext in your runbook
Disable-AzureRmContextAutosave –Scope Process

# Connect to Azure with RunAs account
$ServicePrincipalConnection = Get-AutomationConnection -Name 'AzureRunAsConnection'

Add-AzureRmAccount `
    -ServicePrincipal `
    -TenantId $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint

$AzureContext = Select-AzureRmSubscription -SubscriptionId $ServicePrincipalConnection.SubscriptionID

$params = @{"VMName"="MyVM";"RepeatCount"=2;"Restart"=$true}

Start-AzureRmAutomationRunbook `
    –AutomationAccountName 'MyAutomationAccount' `
    –Name 'Test-ChildRunbook' `
    -ResourceGroupName 'LabRG' `
    -AzureRMContext $AzureContext `
    –Parameters $params –wait
```

## <a name="comparison-of-methods-for-calling-a-child-runbook"></a>Vergelijking van methoden voor het aanroepen van een onderliggend runbook

De volgende tabel geeft een overzicht van de verschillen tussen de twee methoden voor het aanroepen van een runbook vanuit een ander runbook.

|  | Inline | Cmdlet |
|:--- |:--- |:--- |
| Taak |Onderliggende runbooks worden uitgevoerd in dezelfde taak als het bovenliggende item. |Een afzonderlijke taak is voor het onderliggende runbook gemaakt. |
| Uitvoering |Bovenliggend runbook wacht tot het onderliggende runbook om te voltooien voordat u doorgaat. |Het bovenliggende runbook blijft onmiddellijk nadat het onderliggende runbook is gestart *of* het bovenliggende runbook wacht totdat de onderliggende taak is voltooid. |
| Uitvoer |Bovenliggend runbook kan uitvoer rechtstreeks van onderliggend runbook ophalen. |Bovenliggend runbook moet uitvoer ophalen van onderliggend runbook-taak *of* bovenliggend runbook kan rechtstreeks uitvoer ophalen van onderliggend runbook. |
| Parameters |Waarden voor de parameters van onderliggend runbook worden afzonderlijk opgegeven en elk gegevenstype kunnen gebruiken. |Waarden voor de para meters van het onderliggende runbook moeten worden gecombineerd in één hashtabel. Deze hashtabel kan alleen eenvoudige, matrix-en object gegevens typen bevatten die gebruikmaken van JSON-serialisatie. |
| Automation-account |Bovenliggend runbook kan alleen een onderliggend runbook gebruiken in hetzelfde Automation-account. |Bovenliggende runbooks kunnen een onderliggend runbook gebruiken uit elk Automation-account uit hetzelfde Azure-abonnement en zelfs een ander abonnement waarmee u verbinding hebt. |
| Publiceren |Onderliggend runbook moet worden gepubliceerd voordat bovenliggend runbook wordt gepubliceerd. |Onderliggend runbook moet worden gepubliceerd op het moment dat het bovenliggende runbook wordt gestart. |

## <a name="next-steps"></a>Volgende stappen

* [Een runbook starten in Azure Automation](start-runbooks.md)
* [Runbook-uitvoer en berichten in Azure Automation](automation-runbook-output-and-messages.md)

