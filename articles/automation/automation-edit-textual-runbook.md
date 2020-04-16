---
title: Tekstuele runbooks bewerken in Azure Automation
description: In dit artikel worden verschillende procedures weergegeven voor het werken met PowerShell- en PowerShell-werkstroomwerkboeken in Azure Automation met behulp van de tekstuele editor.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 08/01/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 2ef7db244057bc8b3b2e4d938b9f3bdd11c7940a
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81406032"
---
# <a name="editing-textual-runbooks-in-azure-automation"></a>Tekstuele runbooks bewerken in Azure Automation

De tekstuele editor in Azure Automation kan worden gebruikt om [PowerShell-runbooks](automation-runbook-types.md#powershell-runbooks) en [PowerShell-werkstroomlogboeken te](automation-runbook-types.md#powershell-workflow-runbooks)bewerken. Deze editor heeft de typische functies van andere codeeditors, zoals IntelliSense. Het heeft ook kleurcodering met extra speciale functies om u te helpen bij het openen van bronnen die gemeenschappelijk zijn voor runbooks. 

De tekstuele editor bevat een functie om code in te voegen voor cmdlets, assets en onderliggende runbooks in een runbook. In plaats van de code zelf in te typen, u kiezen uit een lijst met beschikbare bronnen en voegt de editor de juiste code in het runbook in.

Elk runbook in Azure Automation heeft twee versies, Concept en Gepubliceerd. U bewerkt de conceptversie van het runbook en publiceert deze vervolgens zodat het kan worden uitgevoerd. De gepubliceerde versie kan niet worden bewerkt. Zie [Een runbook publiceren](manage-runbooks.md#publishing-a-runbook)voor meer informatie .

Dit artikel bevat gedetailleerde stappen voor het uitvoeren van verschillende functies met deze editor. Deze zijn niet van toepassing op [grafische runbooks](automation-runbook-types.md#graphical-runbooks). Zie Grafische ontwerpen in [Azure Automation](automation-graphical-authoring-intro.md)als u met deze runbooks wilt werken.

>[!NOTE]
>Dit artikel is bijgewerkt voor het gebruik van de nieuwe Azure PowerShell Az-module. De AzureRM-module kan nog worden gebruikt en krijgt bugoplossingen tot ten minste december 2020. Zie voor meer informatie over de nieuwe Az-module en compatibiliteit met AzureRM [Introductie van de nieuwe Az-module van Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Zie [De Azure PowerShell-module installeren](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)voor installatie-instructies voor az-modules op uw hybride runbookworker. Voor uw Automatiseringsaccount u uw modules bijwerken naar de nieuwste versie met [Azure PowerShell-modules bijwerken in Azure Automation.](automation-update-azure-modules.md)

## <a name="editing-a-runbook-with-the-azure-portal"></a>Een runbook bewerken met de Azure-portal

Gebruik de volgende procedure om een runbook te openen voor bewerking in de tekstuele editor.

1. Selecteer in de Azure-portal uw Automatiseringsaccount.
2. Selecteer **onder PROCESAUTOMATISERING**de optie **Runbooks** om de lijst met runbooks te openen.
3. Kies de runbook die u wilt bewerken en klik op **Bewerken**.
4. Bewerk het runbook.
5. Klik **op Opslaan** wanneer uw bewerkingen zijn voltooid.
6. Klik **op Publiceren** als u de laatste conceptversie van het runbook wilt publiceren.

### <a name="insert-a-cmdlet-into-a-runbook"></a>Een cmdlet invoegen in een runbook

1. Plaats de cursor in het canvas van de tekstuele editor op de plaats waar u de cmdlet wilt plaatsen.
2. Vouw het knooppunt **Cmdlets** uit in het besturingselement Bibliotheek.
3. Vouw de module met de cmdlet uit om te gebruiken.
4. Klik met de rechtermuisknop op de naam van de cmdlet om in te voegen en selecteer **Toevoegen aan canvas**. Als de cmdlet meer dan één parameterset heeft, voegt de editor de standaardset toe. U de cmdlet ook uitbreiden om een andere parameterset te selecteren.
5. Houd er rekening mee dat de code voor de cmdlet wordt ingevoegd met de volledige lijst met parameters.
6. Geef een geschikte waarde in plaats van de waarde omgeven door hoekhaakjes (<>) voor alle vereiste parameters. Verwijder alle parameters die u niet nodig hebt.

### <a name="insert-code-for-a-child-runbook-into-a-runbook"></a>Code invoegen voor een onderliggende runbook in een runbook

1. Plaats de cursor in het canvas van de tekstuele editor waar u de code voor het [onderliggende runbook](automation-child-runbooks.md)wilt plaatsen.
2. Vouw het knooppunt **Runbooks** uit in het besturingselement Bibliotheek.
3. Klik met de rechtermuisknop op het runbook om in te voegen en selecteer **Toevoegen aan canvas**.
4. De code voor het onderliggende runbook wordt ingevoegd met tijdelijke aanduidingen voor eventuele runbook-parameters.
5. Vervang de tijdelijke aanduidingen door de juiste waarden voor elke parameter.

### <a name="insert-an-asset-into-a-runbook"></a>Een element invoegen in een runbook

1. Plaats in het canvasbesturingselement van de tekstuele editor de cursor op de plaats waar u de code voor het onderliggende runbook wilt plaatsen.
2. Het knooppunt **Assets** in het besturingselement Bibliotheek uitvouwen.
3. Vouw het knooppunt uit voor het gewenste assettype.
4. Klik met de rechtermuisknop op de naam van het element om in te voegen en selecteer **Toevoegen aan canvas**. Selecteer voor [variabele elementen](automation-variables.md) **'Variabele toevoegen' toevoegen aan canvas** of **'Variabele instellen' toevoegen aan canvas,** afhankelijk van of u de variabele wilt krijgen of instellen.
5. Houd er rekening mee dat de code voor het item in het runbook wordt ingevoegd.

## <a name="editing-an-azure-automation-runbook-using-windows-powershell"></a>Een Azure Automation-runbook bewerken met Windows PowerShell

Als u een runbook wilt bewerken met Windows PowerShell, gebruikt u de editor van uw keuze en slaat u de runbook op in een **PS1-bestand.** U de cmdlet [Export-AzAutomationRunbook](/powershell/module/Az.Automation/Export-AzAutomationRunbook) gebruiken om de inhoud van het runbook op te halen. U de cmdlet [Import-AzAutomationRunbook](/powershell/module/Az.Automation/import-azautomationrunbook) gebruiken om het bestaande conceptrunbook te vervangen door het gewijzigde.

### <a name="retrieve-the-contents-of-a-runbook-using-windows-powershell"></a>De inhoud van een runbook ophalen met Windows PowerShell

De volgende voorbeeldopdrachten laten zien hoe u het script voor een runbook ophaalt en dit opslaat in een scriptbestand. In dit voorbeeld wordt de conceptversie opgehaald. Het is ook mogelijk om de gepubliceerde versie van het runbook op te halen, hoewel deze versie niet kan worden gewijzigd.

```powershell-interactive
$resourceGroupName = "MyResourceGroup"
$automationAccountName = "MyAutomatonAccount"
$runbookName = "Hello-World"
$scriptFolder = "c:\runbooks"

Export-AzAutomationRunbook -Name $runbookName -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName -OutputFolder $scriptFolder -Slot Draft
```

### <a name="change-the-contents-of-a-runbook-using-windows-powershell"></a>De inhoud van een runbook wijzigen met Windows PowerShell

In de volgende voorbeeldopdrachten wordt aangegeven hoe u de bestaande inhoud van een runbook vervangen door de inhoud van een scriptbestand. Dit is dezelfde voorbeeldprocedure als in [Een runbook importeren uit een scriptbestand met Windows PowerShell](manage-runbooks.md#importing-a-runbook).

```powershell-interactive
$resourceGroupName = "MyResourceGroup"
$automationAccountName = "MyAutomatonAccount"
$runbookName = "Hello-World"
$scriptFolder = "c:\runbooks"

Import-AzAutomationRunbook -Path "$scriptfolder\Hello-World.ps1" -Name $runbookName -Type PowerShell -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName -Force
Publish-AzAutomationRunbook -Name $runbookName -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName
```

## <a name="related-articles"></a>Verwante artikelen:

* [Runbooks beheren in Azure Automation](manage-runbooks.md)
* [PowerShell-werkstroom leren](automation-powershell-workflow.md)
* [Grafische ontwerpen in Azure Automation](automation-graphical-authoring-intro.md)
* [Certificaten](automation-certificates.md)
* [Verbindingen](automation-connections.md)
* [Referenties](automation-credentials.md)
* [Planningen](automation-schedules.md)
* [Variabelen](automation-variables.md)
* [Naslagdocumentatie voor PowerShell-cmdlet](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation)
