---
title: Runbooks beheren in Azure Automation
description: In dit artikel wordt beschreven hoe u runbooks beheert in Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 02/14/2019
ms.topic: conceptual
ms.openlocfilehash: ad2a34691a00f217db6cf6835eefed18c8862d32
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80547921"
---
# <a name="manage-runbooks-in-azure-automation"></a>Runbooks beheren in Azure Automation

U een runbook toevoegen aan Azure Automation door [een nieuwe](#create-a-runbook) te maken of een bestaand boek uit een bestand of de [Runbook Gallery te](automation-runbook-gallery.md) [importeren.](#import-a-runbook) In dit artikel vindt u informatie over het maken en importeren van runbooks uit een bestand. U alle details krijgen van toegang tot communityrunbooks en -modules in [Runbook- en modulegalerieën voor Azure Automation.](automation-runbook-gallery.md)

>[!NOTE]
>Dit artikel is bijgewerkt voor het gebruik van de nieuwe Azure PowerShell Az-module. De AzureRM-module kan nog worden gebruikt en krijgt bugoplossingen tot ten minste december 2020. Zie voor meer informatie over de nieuwe Az-module en compatibiliteit met AzureRM [Introductie van de nieuwe Az-module van Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Zie [De Azure PowerShell-module installeren](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)voor installatie-instructies voor az-modules op uw hybride runbookworker. Voor uw Automatiseringsaccount u uw modules bijwerken naar de nieuwste versie met [Azure PowerShell-modules bijwerken in Azure Automation.](automation-update-azure-modules.md)

## <a name="create-a-runbook"></a>Een runbook maken

U een nieuwe runbook maken in Azure Automation met een van de Azure-portals of Windows PowerShell. Zodra het runbook is gemaakt, u het bewerken met behulp van informatie in [Learning PowerShell Workflow](automation-powershell-workflow.md) en [grafische ontwerpen in Azure Automation](automation-graphical-authoring-intro.md).

### <a name="create-a-runbook-in-the-azure-portal"></a>Een runbook maken in de Azure-portal

1. Open uw Automation-account in Azure Portal.
2. Selecteer in de hub **Runbooks** onder **Procesautomatisering** om de lijst met runbooks te openen.
3. Klik **op Een runbook maken**.
4. Voer een naam in voor het runbook en selecteer [het type .](automation-runbook-types.md) De naam van het runbook moet beginnen met een letter en kan letters, cijfers, underscores en streepjes bevatten.
5. Klik **op Maken** om het runbook te maken en open de editor.

### <a name="create-a-runbook-with-powershell"></a>Een runbook maken met PowerShell

U de cmdlet [Nieuw-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationrunbook?view=azps-3.5.0) gebruiken om een lege [PowerShell-werkstroomrunboek te](automation-runbook-types.md#powershell-workflow-runbooks)maken. Gebruik `Type` de parameter om een van de `New-AzAutomationRunbook`runbook-typen op te geven die zijn gedefinieerd voor .

In het volgende voorbeeld ziet u hoe u een nieuw leeg loopboek maakt.

```azurepowershell-interactive
New-AzAutomationRunbook -AutomationAccountName MyAccount `
-Name NewRunbook -ResourceGroupName MyResourceGroup -Type PowerShell
```

## <a name="import-a-runbook"></a>Een runbook importeren

U een nieuw runbook maken in Azure Automation door een PowerShell-script of PowerShell-werkstroom (**.ps1),** een geëxporteerd grafisch runbook (**.graphrunbook)** of een Python 2-script (**.py ) te**importeren.  U moet het [type runbook](automation-runbook-types.md) opgeven dat tijdens het importeren wordt gemaakt, rekening houdend met de volgende overwegingen.

* Een **.ps1-bestand** dat geen werkstroom bevat, kan worden geïmporteerd in een [PowerShell-runbook](automation-runbook-types.md#powershell-runbooks) of een [PowerShell-werkstroomrunboek](automation-runbook-types.md#powershell-workflow-runbooks). Als u deze importeert in een PowerShell-werkstroom-runbook, wordt deze geconverteerd naar een werkstroom. In dit geval worden opmerkingen opgenomen in het runbook om de wijzigingen te beschrijven die zijn aangebracht.

* Een **.ps1-bestand** met een PowerShell-werkstroom kan alleen worden geïmporteerd in een [PowerShell-werkstroom-runbook](automation-runbook-types.md#powershell-workflow-runbooks). Als het bestand meerdere PowerShell-werkstromen bevat, mislukt de import. U moet elke werkstroom opslaan in een eigen bestand en elk afzonderlijk importeren.

* Een **.ps1-bestand** met een PowerShell-werkstroom mag niet worden geïmporteerd in een [PowerShell-runbook,](automation-runbook-types.md#powershell-runbooks)omdat de PowerShell-scriptengine het niet kan herkennen.

* Een **.graphrunbook-bestand** kan alleen worden geïmporteerd in een nieuw [grafisch runbook](automation-runbook-types.md#graphical-runbooks). Houd er rekening mee dat u alleen een grafisch runbook maken op basis van een **.graphrunbook-bestand.**

### <a name="import-a-runbook-from-a-file-with-the-azure-portal"></a>Een runbook importeren uit een bestand met de Azure-portal

U de volgende procedure gebruiken om een scriptbestand te importeren in Azure Automation.

> [!NOTE]
> U een **PS1-bestand** alleen importeren in een PowerShell-werkstroomrunboek via de portal.

1. Open uw Automation-account in Azure Portal.
2. Selecteer in de hub **Runbooks** onder **Procesautomatisering** om de lijst met runbooks te openen.
3. Klik **op Een runbook importeren**.
4. Klik op **Runbook-bestand** en selecteer het bestand dat u wilt importeren.
5. Als het veld **Naam** is ingeschakeld, u de naam van het runbook wijzigen. De naam moet beginnen met een letter en kan letters, cijfers, underscores en streepjes bevatten.
6. Het [type runbook](automation-runbook-types.md) wordt automatisch geselecteerd, maar u het type wijzigen nadat u rekening hebt gehouden met de toepasselijke beperkingen.
7. Klik **op Maken**. Het nieuwe runbook wordt weergegeven in de lijst met runbooks voor het automatiseringsaccount.
8. U moet [het runbook publiceren](#publish-a-runbook) voordat u het uitvoeren.

> [!NOTE]
> Nadat u een grafisch runbook of een grafisch PowerShell-werkstroomboek hebt geïmporteerd, u deze converteren naar een ander type. U echter een van deze grafische runbooks niet converteren naar een tekstueel runbook.

### <a name="import-a-runbook-from-a-script-file-with-windows-powershell"></a>Een runbook importeren uit een scriptbestand met Windows PowerShell

Gebruik de cmdlet [Import-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/import-azautomationrunbook?view=azps-3.5.0) om een scriptbestand te importeren als een concept-PowerShell-werkstroomwerklogboek. Als het runbook al bestaat, mislukt `Force` de import, tenzij u de parameter met de cmdlet gebruikt.

In het volgende voorbeeld ziet u hoe u een scriptbestand importeert in een runbook.

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

Wanneer u een runbook test, wordt de [conceptversie](#publish-a-runbook) uitgevoerd en worden alle acties die worden uitgevoerd voltooid. Er wordt geen taakgeschiedenis gemaakt, maar de [uitvoer-](automation-runbook-output-and-messages.md#output-stream) en [waarschuwings- en foutstromen](automation-runbook-output-and-messages.md#message-streams) worden weergegeven in het deelvenster Uitvoer van de test. Berichten naar de [Verbose-stream](automation-runbook-output-and-messages.md#message-streams) worden alleen weergegeven `VerbosePreference` in het deelvenster Uitvoer als de variabele(automation-runbook-output-and-messages.md#preference-variables) is ingesteld op Doorgaan.

Hoewel de conceptversie wordt uitgevoerd, wordt het runbook nog steeds normaal uitgevoerd en voert het alle acties uit tegen resources in de omgeving. Daarom moet u alleen runbooks testen op niet-productieresources.

De procedure om elk [type runbook](automation-runbook-types.md) te testen is hetzelfde. Er is geen verschil in testen tussen de tekstuele editor en de grafische editor in de Azure-portal.

1. Open de conceptversie van het runbook in de [tekstuele editor](automation-edit-textual-runbook.md) of de [grafische editor](automation-graphical-authoring-intro.md).
1. Klik **op** de knop Testen om de pagina Testen te openen.
1. Als het runbook parameters heeft, worden deze weergegeven in het linkerdeelvenster, waar u waarden opgeven die voor de test kunnen worden gebruikt.
1. Als u de test wilt uitvoeren op een [hybride runbookworker,](automation-hybrid-runbook-worker.md)wijzigt u **Run-instellingen** in **Hybride werker** en selecteert u de naam van de doelgroep.  Anders behoudt u de **standaardAzure** om de test in de cloud uit te voeren.
1. Klik **op** de knop Start om de test te starten.
1. U de knoppen onder het deelvenster Uitvoer gebruiken om een [PowerShell-werkstroom](automation-runbook-types.md#powershell-workflow-runbooks) of [grafisch](automation-runbook-types.md#graphical-runbooks) runbook te stoppen of op te schorten terwijl deze wordt getest. Wanneer u het runbook onderbreekt, wordt de huidige activiteit voltooid voordat het runbook wordt onderbroken. Als het runbook is onderbroken, kunt u het stoppen of opnieuw starten.
1. Controleer de uitvoer uit de runbook in het deelvenster Uitvoer.

## <a name="publish-a-runbook"></a>Een runbook publiceren

Wanneer u een nieuw runbook maakt of importeert, moet u het publiceren voordat u het uitvoeren. Elk runbook in Azure Automation heeft een conceptversie en een gepubliceerde versie. Alleen de gepubliceerde versie is beschikbaar om te worden uitgevoerd en alleen de conceptversie kan worden bewerkt. De gepubliceerde versie wordt niet beïnvloed door wijzigingen in de conceptversie. Wanneer de conceptversie beschikbaar moet worden gesteld, publiceert u deze en overschrijft u de huidige gepubliceerde versie met de conceptversie.

### <a name="publish-a-runbook-in-the-azure-portal"></a>Een runbook publiceren in de Azure-portal

1. Open het runbook in de Azure-portal.
2. Klik op **Bewerken**.
3. Klik **op Publiceren** en vervolgens **ja** als reactie op het verificatiebericht.

### <a name="publish-a-runbook-using-powershell"></a>Een runbook publiceren met PowerShell

Gebruik de cmdlet [Publish-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Publish-AzAutomationRunbook?view=azps-3.5.0) om een runbook met Windows PowerShell te publiceren. In het volgende voorbeeld ziet u hoe u een voorbeeldrunbook publiceert.

```azurepowershell-interactive
$automationAccountName =  "AutomationAccount"
$runbookName = "Sample_TestRunbook"
$RGName = "ResourceGroup"

Publish-AzAutomationRunbook -AutomationAccountName $automationAccountName `
-Name $runbookName -ResourceGroupName $RGName
```

### <a name="schedule-a-runbook-in-the-azure-portal"></a>Een runbook plannen in de Azure-portal

Wanneer uw runbook is gepubliceerd, u het inplannen voor gebruik.

1. Open het runbook in de Azure-portal.
2. Selecteer **Planningonder** **Resources**.
3. Selecteer **Een planning toevoegen**.
4. Selecteer in het deelvenster Runbook plannen de optie **Een planning koppelen aan uw runbook**.
5. Kies **Een nieuw schema maken** in het deelvenster Planning.
6. Voer een naam, beschrijving en andere parameters in het deelvenster Nieuwe planning in. 
7. Zodra het schema is gemaakt, markeert u deze en klikt u op **OK**. Het moet nu worden gekoppeld aan uw runbook.
8. Zoek naar een e-mail in uw postvak om u op de hoogte te stellen van de status van het runbook.

## <a name="next-steps"></a>Volgende stappen

* Zie [Runbook- en modulegalerieën voor Azure Automation voor](automation-runbook-gallery.md)meer informatie over hoe u profiteren van de Runbook en PowerShell Module Gallery.
* Zie [Tekstuele runbooks](automation-edit-textual-runbook.md)bewerken in Azure Automation voor meer informatie over het bewerken van PowerShell- en PowerShell-werkstroomwerkboeken met een tekstuele editor.
* Zie Grafisch ontwerpen in Azure Automation voor meer informatie over grafische runbook [authoring.](automation-graphical-authoring-intro.md)
