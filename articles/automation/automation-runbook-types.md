---
title: Azure Automation runbook-typen
description: Beschrijft de verschillende typen runbooks die u gebruiken in Azure Automation en overwegingen om te bepalen welk type u wilt gebruiken.
services: automation
ms.subservice: process-automation
ms.date: 03/05/2019
ms.topic: conceptual
ms.openlocfilehash: 1ac6347bd8e723f356da4803da54a6ea45a4a71a
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535516"
---
# <a name="azure-automation-runbook-types"></a>Azure Automation runbook-typen

De automatiseringsservice azure automation voor automatisering ondersteunt verschillende typen runbooks, zoals gedefinieerd in de volgende tabel. Zie [Runbook-uitvoering in Azure Automation](automation-runbook-execution.md)voor meer informatie over de procesautomatiseringsomgeving.

| Type | Beschrijving |
|:--- |:--- |
| [Grafisch](#graphical-runbooks)|Grafische runbook op basis van Windows PowerShell en volledig gemaakt en bewerkt in de grafische editor in Azure portal. |
| [Grafische PowerShell-werkstroom](#graphical-runbooks)|Grafische runbook op basis van Windows PowerShell Workflow en volledig gemaakt en bewerkt in de grafische editor in Azure portal. |
| [PowerShell](#powershell-runbooks) |Tekstrunbook op basis van Windows PowerShell-scripting. |
| [PowerShell-werkstroom](#powershell-workflow-runbooks)|Tekstrunbook op basis van Windows PowerShell Workflow scripting. |
| [Python](#python-runbooks) |Tekstrunbook op basis van Python-scripting. |

Houd rekening met de volgende overwegingen bij het bepalen welk type moet worden gebruikt voor een bepaald runbook.

* U runbooks niet converteren van grafisch naar teksttype of andersom.
* Er zijn beperkingen bij het gebruik van runbooks van verschillende typen als onderliggende runbooks. Zie [Onderliggende runbooks in Azure Automation](automation-child-runbooks.md)voor meer informatie.

>[!NOTE]
>Dit artikel is bijgewerkt voor het gebruik van de nieuwe Azure PowerShell Az-module. De AzureRM-module kan nog worden gebruikt en krijgt bugoplossingen tot ten minste december 2020. Zie voor meer informatie over de nieuwe Az-module en compatibiliteit met AzureRM [Introductie van de nieuwe Az-module van Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Zie [De Azure PowerShell-module installeren](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)voor installatie-instructies voor az-modules op uw hybride runbookworker. Voor uw Automatiseringsaccount u uw modules bijwerken naar de nieuwste versie met [Azure PowerShell-modules bijwerken in Azure Automation.](automation-update-azure-modules.md)

## <a name="graphical-runbooks"></a>Grafische runbooks

U grafische en grafische PowerShell Workflow-runbooks maken en bewerken met behulp van de grafische editor in de Azure-portal. U dit type runbook echter niet maken of bewerken met een ander gereedschap. Belangrijkste kenmerken van grafische runbooks:

* Kan worden geëxporteerd naar bestanden in uw Automatiseringsaccount en vervolgens worden geïmporteerd naar een ander Automatiseringsaccount. 
* PowerShell-code genereren. 
* Kan tijdens het importeren worden geconverteerd naar of van grafische PowerShell Workflow-runbooks. 

### <a name="advantages"></a>Voordelen

* Gebruik het ontwerpmodel voor het configureren van visuele invoegkoppeling.
* Focus op hoe gegevens door het proces stromen.
* Vertegenwoordig en geef managementprocessen visueel weer.
* Andere runbooks opnemen als onderliggende runbooks om werkstromen op hoog niveau te maken.
* Moedig modulair programmeren aan.

### <a name="limitations"></a>Beperkingen

* Kan niet buiten de Azure-portal maken of bewerken.
* Mogelijk is een codeactiviteit nodig die PowerShell-code bevat om complexe logica uit te voeren.
* U niet converteren naar een van de [tekstindelingen](automation-runbook-types.md)en u een tekstrunbook ook niet converteren naar grafische indeling. 
* Kan powershell-code die de grafische werkstroom maakt, niet bekijken of rechtstreeks bewerken. U de code die u maakt in codeactiviteiten bekijken.
* Kan geen runbooks uitvoeren op een Linux Hybrid Runbook Worker. Zie [Resources automatiseren in uw datacenter of cloud met Hybride Runbook Worker](automation-hybrid-runbook-worker.md).

## <a name="powershell-runbooks"></a>PowerShell-runbooks

PowerShell-runbooks zijn gebaseerd op Windows PowerShell. U bewerkt de code van het runbook rechtstreeks met de teksteditor in de Azure-portal.  U ook een offline teksteditor gebruiken en [het runbook importeren](manage-runbooks.md) in Azure Automation.

### <a name="advantages"></a>Voordelen

* Implementeer alle complexe logica met PowerShell-code zonder de extra complexiteit van PowerShell Workflow.
* Start sneller dan PowerShell Workflow runbooks, omdat ze niet hoeven te worden gecompileerd voordat ze worden uitgevoerd.
* Uitvoeren in Azure en op Hybride Runbook Workers voor zowel Windows als Linux.

### <a name="limitations"></a>Beperkingen

* U moet bekend zijn met PowerShell-scripting.
* Runbooks kunnen geen [parallelle verwerking](automation-powershell-workflow.md#parallel-processing) gebruiken om meerdere acties parallel uit te voeren.
* Runbooks kunnen [geen controlepunten](automation-powershell-workflow.md#checkpoints) gebruiken om runbook te hervatten als er een fout optreedt.
* U alleen PowerShell Workflow runbooks en grafische runbooks opnemen als onderliggende runbooks met behulp van de cmdlet [Start-AzAutomationRunbook,](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.7.0) waarmee een nieuwe taak wordt gemaakt.

### <a name="known-issues"></a>Bekende problemen

De volgende zijn actuele bekende problemen met PowerShell-runbooks:

* PowerShell-runbooks kunnen geen onversleutelde [variabele asset](automation-variables.md) met een null-waarde ophalen.
* PowerShell-runbooks kunnen geen variabel `*~*` actief ophalen met in de naam.
* Een [Get-Process-bewerking](https://docs.microsoft.com/powershell/module/microsoft.powershell.management/get-process?view=powershell-7) in een lus in een PowerShell-runbook kan crashen na ongeveer 80 iteraties.
* Een PowerShell-runbook kan mislukken als het probeert om een grote hoeveelheid gegevens in een keer naar de uitvoerstroom te schrijven. U dit probleem meestal oplossen door de uitvoer van de runbook alleen de informatie te laten uitvoeren die nodig is om met grote objecten te werken. Bijvoorbeeld, in plaats `Get-Process` van het gebruik zonder beperkingen, u de `Get-Process | Select ProcessName, CPU`cmdlet output alleen de vereiste parameters zoals in.

## <a name="powershell-workflow-runbooks"></a>PowerShell-werkstroomrunboeken

PowerShell-werkstroomrunboeken zijn tekstrunbooks op basis van [Windows PowerShell-werkstroom.](automation-powershell-workflow.md) U bewerkt de code van het runbook rechtstreeks met de teksteditor in de Azure-portal. U ook een offline teksteditor gebruiken en [het runbook importeren](manage-runbooks.md) in Azure Automation.

### <a name="advantages"></a>Voordelen

* Implementeer alle complexe logica met PowerShell-werkstroomcode.
* Gebruik [controlepunten](automation-powershell-workflow.md#checkpoints) om de bewerking te hervatten als er een fout optreedt.
* Gebruik [parallelle verwerking](automation-powershell-workflow.md#parallel-processing) om meerdere acties parallel uit te voeren.
* Kan andere grafische runbooks en PowerShell Workflow runbooks bevatten als onderliggende runbooks om werkstromen op hoog niveau te maken.

### <a name="limitations"></a>Beperkingen

* U moet bekend zijn met PowerShell Workflow.
* Runbooks moeten omgaan met de extra complexiteit van PowerShell-werkstroom, zoals [gedeserialiseerde objecten](automation-powershell-workflow.md#code-changes).
* Runbooks doen er langer over om te starten dan PowerShell-runbooks, omdat ze moeten worden gecompileerd voordat ze worden uitgevoerd.
* U PowerShell-runbooks alleen opnemen als `Start-AzAutomationRunbook` onderliggende runbooks met behulp van de cmdlet.
* Runbooks kunnen niet worden uitgevoerd op een Linux Hybrid Runbook Worker.

## <a name="python-runbooks"></a>Python runbooks

Python runbooks compileren onder Python 2. U de code van de runbook rechtstreeks bewerken met de teksteditor in de Azure-portal. U ook een offline teksteditor gebruiken en [het runbook importeren](manage-runbooks.md) in Azure Automation.

### <a name="advantages"></a>Voordelen

* Gebruik de robuuste Python-bibliotheken.
* Kan worden uitgevoerd in Azure of op Linux Hybrid Runbook Workers. Windows Hybrid Runbook Workers worden ondersteund met [python2.7](https://www.python.org/downloads/release/latest/python2) geïnstalleerd.

### <a name="limitations"></a>Beperkingen

* Je moet bekend zijn met Python scripting.
* Alleen Python 2 wordt momenteel ondersteund. Python 3-specifieke functies mislukken.
* Als u bibliotheken van derden wilt gebruiken, moet u [de pakketten importeren](python-packages.md) in het automatiseringsaccount.

## <a name="next-steps"></a>Volgende stappen

* Zie Grafisch ontwerpen in Azure Automation voor meer informatie over grafische runbook [authoring.](automation-graphical-authoring-intro.md)
* Zie [Windows PowerShell-werkstroom leren](automation-powershell-workflow.md)om inzicht te krijgen in de verschillen tussen PowerShell- en PowerShell-werkstromen voor runbooks.
* Zie [Runbooks](manage-runbooks.md)beheren in Azure Automation voor meer informatie over het maken of importeren van een runbook.
* Zie de [PowerShell-documenten](https://docs.microsoft.com/powershell/scripting/overview)voor meer informatie over PowerShell, inclusief taalverwijzing en leermodules.
