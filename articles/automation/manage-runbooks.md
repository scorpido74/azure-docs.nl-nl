---
title: Runbooks in Azure Automation beheren
description: In dit artikel wordt beschreven hoe u runbooks beheert in Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 02/14/2019
ms.topic: conceptual
ms.openlocfilehash: ec53c4b2f80fb095f58bee9c15ac5daafb8d59ef
ms.sourcegitcommit: 390cfe85629171241e9e81869c926fc6768940a4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2020
ms.locfileid: "78226249"
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

U kunt de cmdlet [New-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationrunbook?view=azps-3.5.0) gebruiken om een leeg [Power shell workflow-runbook](automation-runbook-types.md#powershell-workflow-runbooks)te maken. Gebruik de para meter *type* om een van de typen runbook op te geven die zijn gedefinieerd voor **New-AzAutomationRunbook**.

In het volgende voor beeld ziet u hoe u een nieuw leeg runbook maakt.

```azurepowershell-interactive
New-AzAutomationRunbook -AutomationAccountName MyAccount `
-Name NewRunbook -ResourceGroupName MyResourceGroup -Type PowerShell
```

## <a name="import-a-runbook"></a>Een runbook importeren

U kunt een nieuw runbook maken in Azure Automation door een Power shell-script of Power shell-werk stroom ( **. ps1**), een geëxporteerd grafisch runbook ( **. graphrunbook**) of een python 2-script ( **. py**) te importeren.  U moet het [type runbook](automation-runbook-types.md) opgeven dat tijdens het importeren wordt gemaakt, waarbij rekening wordt gehouden met de volgende overwegingen.

* Een **. ps1** -bestand dat geen werk stroom bevat, kan worden geïmporteerd in een [Power shell-Runbook](automation-runbook-types.md#powershell-runbooks) of een [Power shell workflow-runbook](automation-runbook-types.md#powershell-workflow-runbooks). Als u deze importeert in een Power shell workflow-runbook, wordt dit geconverteerd naar een werk stroom. In dit geval worden opmerkingen in het runbook opgenomen om de wijzigingen te beschrijven die zijn aangebracht.

* Een **. ps1** -bestand met een Power shell-werk stroom kan alleen in een [Power shell workflow-runbook](automation-runbook-types.md#powershell-workflow-runbooks)worden geïmporteerd. Als het bestand meerdere Power shell-werk stromen bevat, mislukt het importeren. U moet elke werk stroom opslaan in een eigen bestand en deze afzonderlijk importeren.

* Een **. ps1** -bestand dat een Power shell-werk stroom bevat, mag niet worden geïmporteerd in een [Power shell-runbook](automation-runbook-types.md#powershell-runbooks), omdat de engine niet kan worden herkend door Power shell.

* Een **. graphrunbook** -bestand kan alleen in een nieuw [grafisch runbook](automation-runbook-types.md#graphical-runbooks)worden geïmporteerd. Houd er rekening mee dat u alleen een grafisch runbook kunt maken van een **. graphrunbook** -bestand.

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
7. Klik op **Create**. Het nieuwe runbook wordt weer gegeven in de lijst met runbooks voor het Automation-account.
8. U moet [het runbook publiceren](#publish-a-runbook) voordat u het kunt uitvoeren.

> [!NOTE]
> Nadat u een grafisch runbook of een grafisch Power shell-werk stroom-runbook hebt geïmporteerd, kunt u deze converteren naar een ander type. U kunt een van deze grafische runbooks echter niet converteren naar een tekst runbook.

### <a name="import-a-runbook-from-a-script-file-with-windows-powershell"></a>Een runbook importeert uit een scriptbestand met Windows PowerShell

Gebruik de cmdlet [import-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/import-azautomationrunbook?view=azps-3.5.0) om een script bestand te importeren als een concept Power shell workflow-runbook. Als het runbook al bestaat, mislukt het importeren tenzij u de para meter *Force* gebruikt met de cmdlet.

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

## <a name="test-a-runbook"></a>Een runbook testen

Wanneer u een runbook test, wordt de [concept versie](#publish-a-runbook) uitgevoerd en de acties die worden uitgevoerd, worden voltooid. Er is geen taak geschiedenis gemaakt, maar de [uitvoer](automation-runbook-output-and-messages.md#output-stream) -en [waarschuwings-en fout](automation-runbook-output-and-messages.md#message-streams) stromen worden weer gegeven in het deel venster test uitvoer. Berichten naar de [uitgebreide stroom](automation-runbook-output-and-messages.md#message-streams) worden alleen weer gegeven in het deel venster uitvoer als de variabele *VerbosePreference* ] (Automation-runbook-output-and-messages. MD # Preferences-Varia bles) is ingesteld om **door te gaan**.

Hoewel de concept versie wordt uitgevoerd, wordt het runbook nog steeds normaal uitgevoerd en worden alle acties uitgevoerd voor resources in de omgeving. Daarom moet u runbooks alleen testen op niet-productie resources.

De procedure voor het testen [van elk type runbook](automation-runbook-types.md) is hetzelfde. Er is geen verschil in het testen tussen de tekst editor en de grafische editor in de Azure Portal.

1. Open de concept versie van het runbook in de [tekst editor](automation-edit-textual-runbook.md) of de [grafische editor](automation-graphical-authoring-intro.md).
1. Klik op de knop **testen** om de pagina test te openen.
1. Als het runbook para meters heeft, worden deze weer gegeven in het linkerdeel venster, waar u waarden kunt opgeven die moeten worden gebruikt voor de test.
1. Als u de test wilt uitvoeren op een [Hybrid Runbook worker](automation-hybrid-runbook-worker.md), wijzigt u de instellingen voor het **uitvoeren** van de **Hybrid worker** en selecteert u de naam van de doel groep.  Als dat niet het geval is, moet u de test in de Cloud uitvoeren met de standaard **Azure** .
1. Klik op de knop **Start** om de test te starten.
1. U kunt de knoppen onder het deel venster uitvoer gebruiken om een [Power shell-werk stroom](automation-runbook-types.md#powershell-workflow-runbooks) of [grafisch](automation-runbook-types.md#graphical-runbooks) runbook te stoppen of te onderbreken tijdens het testen. Wanneer u het runbook onderbreekt, wordt de huidige activiteit voltooid voordat het wordt onderbroken. Zodra het runbook wordt onderbroken, kunt u voorkomen dat deze of het programma opnieuw.
1. Controleer de uitvoer van het runbook in het deel venster uitvoer.

## <a name="publish-a-runbook"></a>Een runbook publiceren

Wanneer u een nieuw runbook maakt of importeert, moet u het publiceren voordat u het kunt uitvoeren. Elk runbook in Azure Automation heeft een concept versie en een gepubliceerde versie. Alleen de gepubliceerde versie is beschikbaar om te worden uitgevoerd, en alleen de conceptversie kan worden bewerkt. De gepubliceerde versie wordt niet beïnvloed door wijzigingen in de conceptversie. Wanneer de concept versie beschikbaar moet worden gemaakt, publiceert u deze en wordt de huidige gepubliceerde versie overschreven met de concept versie.

### <a name="publish-a-runbook-in-the-azure-portal"></a>Een runbook publiceren in de Azure Portal

1. Open het runbook in de Azure Portal.
2. Klik op **Bewerken**.
3. Klik op **publiceren** en vervolgens op **Ja** in antwoord op het verificatie bericht.

### <a name="publish-a-runbook-using-powershell"></a>Publiceren van een runbook met behulp van PowerShell

Gebruik de cmdlet [Publish-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Publish-AzAutomationRunbook?view=azps-3.5.0) om een runbook te publiceren met Windows Power shell. In het volgende voor beeld ziet u hoe u een voor beeld-runbook publiceert.

```azurepowershell-interactive
$automationAccountName =  "AutomationAccount"
$runbookName = "Sample_TestRunbook"
$RGName = "ResourceGroup"

Publish-AzAutomationRunbook -AutomationAccountName $automationAccountName `
-Name $runbookName -ResourceGroupName $RGName
```

## <a name="next-steps"></a>Volgende stappen

* Zie [runbook-en module galerieën voor Azure Automation voor](automation-runbook-gallery.md)meer informatie over hoe u kunt profiteren van de galerie met runbook-en Power shell-modules.
* Zie voor meer informatie over het bewerken van Power shell-en Power shell-werk stroom runbooks met een tekst editor [tekst Runbooks bewerken in azure Automation](automation-edit-textual-runbook.md).
* Zie [grafisch ontwerpen in azure Automation](automation-graphical-authoring-intro.md)voor meer informatie over het ontwerpen van een grafisch runbook.
