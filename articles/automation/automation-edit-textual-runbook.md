---
title: Het bewerken van tekst runbooks in Azure Automation
description: In dit artikel worden verschillende procedures beschreven voor het werken met Power shell-en Power shell-werk stroom-runbooks in Azure Automation met behulp van de tekst editor.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 08/01/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 5a7eec0a7650f9c8e04a8d1062d32b6feb7d1d99
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850853"
---
# <a name="editing-textual-runbooks-in-azure-automation"></a>Het bewerken van tekst runbooks in Azure Automation

De tekst editor in Azure Automation kan worden gebruikt voor het bewerken van [Power shell-runbooks](automation-runbook-types.md#powershell-runbooks) en [Power shell workflow-runbooks](automation-runbook-types.md#powershell-workflow-runbooks). Dit heeft de typische functies van andere code-editors, zoals IntelliSense en kleur codering met extra speciale functies om u te helpen bij het openen van resources die gemeen schappelijk zijn voor runbooks. In dit artikel vindt u gedetailleerde stappen voor het uitvoeren van verschillende functies met deze editor.

De tekst editor bevat een functie voor het invoegen van code voor cmdlets, assets en onderliggende runbooks in een runbook. In plaats van de code zelf zelf te typen, kunt u een keuze uit een lijst met beschik bare resources selecteren en de juiste code in het runbook invoegen.

Elk runbook in Azure Automation heeft twee versies: concept en gepubliceerd. U bewerkt de concept versie van het runbook en publiceert deze zodat het kan worden uitgevoerd. De gepubliceerde versie kan niet worden bewerkt. Zie [een Runbook publiceren](manage-runbooks.md#publish-a-runbook)voor meer informatie.

Als u met [grafische Runbooks](automation-runbook-types.md#graphical-runbooks)wilt werken, raadpleegt u [grafisch ontwerpen in azure Automation](automation-graphical-authoring-intro.md).

## <a name="to-edit-a-runbook-with-the-azure-portal"></a>Een runbook bewerken met de Azure Portal

Gebruik de volgende procedure om een runbook te openen dat u wilt bewerken in de tekst editor.

1. Selecteer uw Automation-account in het Azure Portal.
2. Selecteer **runbooks** onder **proces automatisering**om de lijst met Runbooks te openen.
3. Selecteer het runbook dat u wilt bewerken en klik vervolgens op de knop **bewerken** .
4. Bewerk het runbook.
5. Klik op **opslaan** wanneer de bewerkingen zijn voltooid.
6. Klik op **publiceren** als u wilt dat de meest recente conceptversie van het runbook wordt gepubliceerd.

### <a name="to-insert-a-cmdlet-into-a-runbook"></a>Een cmdlet in een runbook invoegen

1. In het canvas van de tekst editor plaatst u de cursor waar u de cmdlet wilt plaatsen.
2. Vouw het knoop punt **cmdlets** uit in het besturings element bibliotheek.
3. Vouw de module uit die de cmdlet bevat die u wilt gebruiken.
4. Klik met de rechter muisknop op de cmdlet die u wilt invoegen en selecteer **toevoegen aan canvas**. Als de cmdlet meer dan één para meter set heeft, wordt de standaardset toegevoegd. U kunt ook de cmdlet uitvouwen om een andere parameterset te selecteren.
5. De code voor de cmdlet wordt ingevoegd met de volledige lijst met para meters.
6. Geef een geschikte waarde op in plaats van het gegevens type tussen accolades < > voor vereiste para meters. Verwijder alle para meters die u niet nodig hebt.

### <a name="to-insert-code-for-a-child-runbook-into-a-runbook"></a>Code voor een onderliggend runbook invoegen in een runbook

1. Plaats de cursor in het canvas van de tekst editor op de plaats waar u de code voor het [onderliggende runbook](automation-child-runbooks.md)wilt plaatsen.
2. Vouw het knoop punt **Runbooks** uit in het besturings element bibliotheek.
3. Klik met de rechter muisknop op het runbook dat u wilt invoegen en selecteer **toevoegen aan canvas**.
4. De code voor het onderliggende runbook wordt ingevoegd met eventuele tijdelijke aanduidingen voor runbook-para meters.
5. Vervang de tijdelijke aanduidingen door de juiste waarden voor elke para meter.

### <a name="to-insert-an-asset-into-a-runbook"></a>Een asset in een runbook invoegen

1. Plaats de cursor in het canvas van de tekst editor op de plaats waar u de code voor het onderliggende runbook wilt plaatsen.
2. Vouw het knoop punt **assets** uit in het besturings element bibliotheek.
3. Vouw het knoop punt uit voor het type Asset dat u wilt.
4. Klik met de rechter muisknop op de Asset die u wilt invoegen en selecteer **toevoegen aan canvas**. Voor [variabele assets](automation-variables.md)selecteert u **' variabele ophalen ' toevoegen aan canvas** of **' variabele instellen ' toevoegen aan canvas** , afhankelijk van of u de variabele wilt ophalen of instellen.
5. De code voor de Asset wordt ingevoegd in het runbook.

## <a name="to-edit-an-azure-automation-runbook-using-windows-powershell"></a>Een Azure Automation runbook bewerken met Windows Power shell

Als u een runbook wilt bewerken met Windows Power shell, gebruikt u de editor van uw keuze en slaat u deze op in een `.ps1` bestand. U kunt de cmdlet [export-AzureRmAutomationRunbook](/powershell/module/AzureRM.Automation/Export-AzureRmAutomationRunbook) gebruiken om de inhoud van het runbook op te halen en vervolgens de cmdlet [import-AzureRmAutomationRunbook](/powershell/module/AzureRM.Automation/import-azurermautomationrunbook) om het bestaande concept-runbook te vervangen door de wijziging.

### <a name="to-retrieve-the-contents-of-a-runbook-using-windows-powershell"></a>De inhoud van een runbook ophalen met Windows PowerShell

De volgende voorbeeldopdrachten laten zien hoe het script voor een runbook ophalen en sla deze op een scriptbestand. In dit voorbeeld wordt de conceptversie opgehaald. Het is ook mogelijk om de gepubliceerde versie van het runbook op te halen, hoewel deze versie niet kan worden gewijzigd.

```powershell-interactive
$resourceGroupName = "MyResourceGroup"
$automationAccountName = "MyAutomatonAccount"
$runbookName = "Hello-World"
$scriptFolder = "c:\runbooks"

Export-AzureRmAutomationRunbook -Name $runbookName -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName -OutputFolder $scriptFolder -Slot Draft
```

### <a name="to-change-the-contents-of-a-runbook-using-windows-powershell"></a>De inhoud van een runbook wijzigen met Windows PowerShell

De volgende voorbeeldopdrachten laten zien hoe de bestaande inhoud van een draaiboek te vervangen door de inhoud van een script bestand. Dit is dezelfde voorbeeld procedure als in [om een Runbook te importeren uit een script bestand met Windows Power shell](manage-runbooks.md#import-a-runbook).

```powershell-interactive
$resourceGroupName = "MyResourceGroup"
$automationAccountName = "MyAutomatonAccount"
$runbookName = "Hello-World"
$scriptFolder = "c:\runbooks"

Import-AzureRmAutomationRunbook -Path "$scriptfolder\Hello-World.ps1" -Name $runbookName -Type PowerShell -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName -Force
Publish-AzureRmAutomationRunbook -Name $runbookName -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName
```

## <a name="related-articles"></a>Verwante artikelen:

* [Runbooks in Azure Automation beheren](manage-runbooks.md)
* [Power shell-werk stroom leren](automation-powershell-workflow.md)
* [Grafisch ontwerpen in Azure Automation](automation-graphical-authoring-intro.md)
* [Certificaten](automation-certificates.md)
* [Verbindingen](automation-connections.md)
* [Referenties](automation-credentials.md)
* [Schema's](automation-schedules.md)
* [Variabelen](automation-variables.md)

