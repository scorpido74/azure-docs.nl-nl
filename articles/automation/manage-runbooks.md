---
title: Runbooks in Azure Automation beheren
description: In dit artikel wordt beschreven hoe u runbooks beheert in Azure Automation.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 02/14/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 42b973ce8a25abff60211afb34ef719d42366175
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850496"
---
# <a name="manage-runbooks-in-azure-automation"></a>Runbooks in Azure Automation beheren

U kunt een runbook toevoegen aan Azure Automation door [een nieuwe te maken](#create-a-runbook) of door een bestaand runbook te importeren uit een bestand of de [runbook-galerie](automation-runbook-gallery.md). Dit artikel bevat informatie over het maken en importeren van runbooks vanuit een bestand.  U kunt alle details over het openen van Community-runbooks en-modules in [Runbook en module galerieën voor Azure Automation](automation-runbook-gallery.md)krijgen.

## <a name="create-a-runbook"></a>Een runbook maken

U kunt een nieuw runbook maken in Azure Automation met behulp van een van de Azure-portals of Windows Power shell. Zodra het runbook is gemaakt, kunt u het bewerken met behulp van informatie in de [Power shell-werk stroom](automation-powershell-workflow.md) voor het leren en [grafisch ontwerpen in azure Automation](automation-graphical-authoring-intro.md).

### <a name="create-a-runbook-in-the-azure-portal"></a>Een runbook maken in de Azure Portal

1. Open uw Automation-account in Azure Portal.
2. Selecteer in de hub **Runbooks** om de lijst met Runbooks te openen.
3. Klik op de knop **een Runbook toevoegen** en **Maak een nieuw runbook**.
4. Typ een **naam** voor het runbook en selecteer het bijbehorende [type](automation-runbook-types.md). De runbooknaam moet beginnen met een letter en kan hebben letters, cijfers, onderstrepingstekens en streepjes bevatten.
5. Klik op **maken** om het runbook te maken en de editor te openen.

### <a name="create-a-runbook-with-powershell"></a>Een runbook maken met Power shell

U kunt de cmdlet [New-AzureRmAutomationRunbook](/powershell/module/azurerm.automation/new-azurermautomationrunbook) gebruiken om een leeg [Power shell workflow-runbook](automation-runbook-types.md#powershell-workflow-runbooks)te maken. Gebruik de para meter **type** om een van de vier typen runbook op te geven.

De volgende voorbeeld opdrachten laten zien hoe u een nieuw leeg runbook maakt.

```azurepowershell-interactive
New-AzureRmAutomationRunbook -AutomationAccountName MyAccount `
-Name NewRunbook -ResourceGroupName MyResourceGroup -Type PowerShell
```

## <a name="import-a-runbook"></a>Een runbook importeren

U kunt een nieuw runbook maken in Azure Automation door een Power shell-script of Power shell-werk stroom (. ps1-extensie), een geëxporteerd grafisch runbook (. graphrunbook) of een python 2-script (. py-extensie) te importeren.  U moet het [type runbook](automation-runbook-types.md) opgeven dat tijdens het importeren wordt gemaakt, waarbij rekening wordt gehouden met de volgende overwegingen.

* Een `.graphrunbook` bestand kan alleen in een nieuw [grafisch runbook](automation-runbook-types.md#graphical-runbooks)worden geïmporteerd en grafische runbooks kunnen alleen worden gemaakt op basis van een `.graphrunbook` bestand.
* Een `.ps1`-bestand met een Power shell-werk stroom kan alleen in een [Power shell workflow-runbook](automation-runbook-types.md#powershell-workflow-runbooks)worden geïmporteerd. Als het bestand meerdere Power shell-werk stromen bevat, mislukt het importeren. U moet elke werk stroom opslaan in een eigen bestand en deze afzonderlijk importeren.
* Een `.ps1`-bestand met een Power shell-werk stroom moet niet worden geïmporteerd in een [Power shell-runbook](automation-runbook-types.md#powershell-runbooks), omdat het niet kan worden herkend door de Power shell-script engine.
* Een `.ps1` bestand dat geen werk stroom bevat, kan worden geïmporteerd in een [Power shell-runbook](automation-runbook-types.md#powershell-runbooks) of een [Power shell workflow-runbook](automation-runbook-types.md#powershell-workflow-runbooks).  Als de app wordt geïmporteerd in een Power shell workflow-runbook, wordt deze geconverteerd naar een werk stroom en worden opmerkingen opgenomen in het runbook waarin de wijzigingen zijn opgegeven die zijn aangebracht.

### <a name="to-import-a-runbook-from-a-file-with-the-azure-portal"></a>Een runbook importeren uit een bestand met de Azure Portal

U kunt de volgende procedure gebruiken om een script bestand te importeren in Azure Automation.

> [!NOTE]
> Houd er rekening mee dat u alleen een. ps1-bestand kunt importeren in een Power shell workflow-runbook met behulp van de portal.

1. Open uw Automation-account in Azure Portal.
2. Selecteer in de hub **Runbooks** om de lijst met Runbooks te openen.
3. Klik op de knop **een Runbook toevoegen** en vervolgens op **importeren**.
4. Klik op **Runbook-bestand** om het bestand te selecteren dat u wilt importeren
5. Als het veld **naam** is ingeschakeld, hebt u de mogelijkheid om deze te wijzigen.  De runbooknaam moet beginnen met een letter en kan hebben letters, cijfers, onderstrepingstekens en streepjes bevatten.
6. Het [runbook-type](automation-runbook-types.md) wordt automatisch geselecteerd, maar u kunt het type wijzigen nadat u de toepasselijke beperkingen in het account hebt genomen.
7. Het nieuwe runbook wordt weer gegeven in de lijst met runbooks voor het Automation-account.
8. U moet [het runbook publiceren](#publish-a-runbook) voordat u het kunt uitvoeren.

> [!NOTE]
> Nadat u een grafisch runbook of een grafisch Power shell-werk stroom runbook hebt geïmporteerd, hebt u de mogelijkheid om naar het andere type te converteren, indien gewenst. U kunt niet converteren naar een tekst runbook.

### <a name="to-import-a-runbook-from-a-script-file-with-windows-powershell"></a>Een runbook uit een scriptbestand importeren met Windows PowerShell

U kunt de cmdlet [import-AzureRMAutomationRunbook](https://docs.microsoft.com/powershell/module/azurerm.automation/import-azurermautomationrunbook) gebruiken om een script bestand te importeren als een concept Power shell workflow-runbook. Als het runbook al bestaat, mislukt het importeren tenzij u de para meter *-Force* gebruikt.

De volgende voorbeeld opdrachten laten zien hoe u een script bestand importeert in een runbook.

```azurepowershell-interactive
$automationAccountName =  "AutomationAccount"
$runbookName = "Sample_TestRunbook"
$scriptPath = "C:\Runbooks\Sample_TestRunbook.ps1"
$RGName = "ResourceGroup"

Import-AzureRMAutomationRunbook -Name $runbookName -Path $scriptPath `
-ResourceGroupName $RGName -AutomationAccountName $automationAccountName `
-Type PowerShellWorkflow
```

## <a name="test-a-runbook"></a>Een runbook testen

Wanneer u een runbook test, wordt de [concept versie](#publish-a-runbook) uitgevoerd en de acties die worden uitgevoerd, worden voltooid. Er is geen taak geschiedenis gemaakt, maar de [uitvoer](automation-runbook-output-and-messages.md#output-stream) -en [waarschuwings-en fout](automation-runbook-output-and-messages.md#message-streams) stromen worden weer gegeven in het deel venster test uitvoer. Berichten naar de [uitgebreide stroom](automation-runbook-output-and-messages.md#message-streams) worden alleen weer gegeven in het deel venster uitvoer als de [variabele $VerbosePreference](automation-runbook-output-and-messages.md#preference-variables) is ingesteld op door gaan.

Hoewel de concept versie wordt uitgevoerd, wordt het runbook nog steeds normaal uitgevoerd en worden alle acties uitgevoerd voor resources in de omgeving. Daarom moet u runbooks alleen testen op niet-productie resources.

De procedure voor het testen [van elk type runbook](automation-runbook-types.md) is hetzelfde, en er is geen verschil in het testen tussen de tekst editor en de grafische editor in de Azure Portal.

1. Open de concept versie van het runbook in de [tekst editor](automation-edit-textual-runbook.md) of [grafische editor](automation-graphical-authoring-intro.md).
1. Klik op de knop **testen** om de pagina test te openen.
1. Als het runbook para meters heeft, worden deze in het linkerdeel venster weer gegeven, waar u de waarden kunt opgeven die u voor de test wilt gebruiken.
1. Als u de test wilt uitvoeren op een [Hybrid Runbook worker](automation-hybrid-runbook-worker.md), wijzigt u de **instellingen** voor het uitvoeren van een **Hybrid worker** en selecteert u de naam van de doel groep.  Als dat niet het geval is, moet u de test in de Cloud uitvoeren met de standaard **Azure** .
1. Klik op de knop **Start** om de test te starten.
1. Als het runbook [Power shell-werk stroom](automation-runbook-types.md#powershell-workflow-runbooks) of [grafische afbeelding](automation-runbook-types.md#graphical-runbooks)is, kunt u deze stoppen of onderbreken terwijl het wordt getest met de knoppen onder het deel venster uitvoer. Wanneer u het runbook onderbreekt, wordt de huidige activiteit voltooid voordat het wordt onderbroken. Zodra het runbook wordt onderbroken, kunt u voorkomen dat deze of het programma opnieuw.
1. Controleer de uitvoer van het runbook in het deel venster uitvoer.

## <a name="publish-a-runbook"></a>Een runbook publiceren

Wanneer u een nieuw runbook maakt of importeert, moet u het publiceren voordat u het kunt uitvoeren.  Elk runbook in Automation heeft een concept en een gepubliceerde versie. Alleen de gepubliceerde versie is beschikbaar om te worden uitgevoerd, en alleen de conceptversie kan worden bewerkt. De gepubliceerde versie wordt niet beïnvloed door wijzigingen in de conceptversie. Wanneer de concept versie beschikbaar moet worden gemaakt, publiceert u deze, waardoor de gepubliceerde versie wordt overschreven door de concept versie.

### <a name="azure-portal"></a>Azure Portal

1. Open het runbook in de Azure Portal.
2. Klik op de knop **Bewerken**.
3. Klik op de knop **publiceren** en vervolgens op **Ja** in het bevestigings bericht.

### <a name="powershell"></a>PowerShell

U kunt de cmdlet [Publish-AzureRmAutomationRunbook](/powershell/module/azurerm.automation/publish-azurermautomationrunbook) gebruiken om een runbook te publiceren met Windows Power shell. De volgende voorbeeld opdrachten laten zien hoe u een voor beeld-runbook publiceert.

```azurepowershell-interactive
$automationAccountName =  "AutomationAccount"
$runbookName = "Sample_TestRunbook"
$RGName = "ResourceGroup"

Publish-AzureRmAutomationRunbook -AutomationAccountName $automationAccountName `
-Name $runbookName -ResourceGroupName $RGName
```

## <a name="next-steps"></a>Volgende stappen

* Zie [runbook-en module galerieën voor Azure Automation voor](automation-runbook-gallery.md) meer informatie over hoe u kunt profiteren van de galerie met runbook-en Power shell-modules.
* Zie voor meer informatie over het bewerken van Power shell-en Power shell-werk stroom runbooks met een tekst editor [tekst Runbooks bewerken in azure Automation](automation-edit-textual-runbook.md)
* Zie voor meer informatie over het ontwerpen van een grafisch runbook [grafisch ontwerpen in azure Automation](automation-graphical-authoring-intro.md)
