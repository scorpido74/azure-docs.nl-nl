---
title: Runbook-typen azure automation
description: 'Beschrijft de verschillende typen runbooks die u gebruiken in Azure Automation en overwegingen waarmee u rekening moet houden bij het bepalen van welk type u wilt gebruiken. '
services: automation
ms.subservice: process-automation
ms.date: 03/05/2019
ms.topic: conceptual
ms.openlocfilehash: 6346c29210b6390f11c884ff51e0b60af89bbbb7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278608"
---
# <a name="azure-automation-runbook-types"></a>Azure Automation runbook-typen

Azure Automation ondersteunt verschillende typen runbooks die kort worden beschreven in de volgende tabel.  De onderstaande secties geven meer informatie over elk type, inclusief overwegingen over wanneer elk te gebruiken.

| Type | Beschrijving |
|:--- |:--- |
| [Grafisch](#graphical-runbooks)|Gebaseerd op Windows PowerShell en volledig gemaakt en bewerkt in grafische editor in Azure portal. |
| [Grafische PowerShell-werkstroom](#graphical-runbooks)|Gebaseerd op Windows PowerShell Workflow en volledig gemaakt en bewerkt in de grafische editor in Azure portal. |
| [Powershell](#powershell-runbooks) |Tekstrunbook op basis van Windows PowerShell-script. |
| [PowerShell-werkstroom](#powershell-workflow-runbooks)|Tekstrunbook op basis van Windows PowerShell-werkstroom. |
| [Python](#python-runbooks) |Tekstrunbook op basis van Python. |

## <a name="graphical-runbooks"></a>Grafische runbooks

[Grafische](automation-runbook-types.md#graphical-runbooks) en grafische PowerShell Workflow-runbooks worden gemaakt en bewerkt met de grafische editor in de Azure-portal.  U ze exporteren naar een bestand en ze vervolgens importeren in een ander automatiseringsaccount. Maar je ze niet maken of bewerken met een ander gereedschap. Grafische runbooks genereren PowerShell-code, maar u de code niet rechtstreeks weergeven of wijzigen. Grafische runbooks kunnen niet worden geconverteerd naar een van de [tekstindelingen](automation-runbook-types.md)en kan ook niet worden omgezet in grafische indeling. Grafische runbooks kunnen worden geconverteerd naar grafische PowerShell Workflow runbooks tijdens import en andersom.

### <a name="advantages"></a>Voordelen

* Ontwerpmodel voor het configureren van visuele invoegkoppeling configureren
* Focus op hoe gegevens door het proces stromen
* Managementprocessen visueel weergeven
* Andere runbooks opnemen als onderliggende runbooks om werkstromen op hoog niveau te maken
* Stimuleert modulair programmeren

### <a name="limitations"></a>Beperkingen

* Kan runbook niet bewerken buiten Azure portal.
* Mogelijk is een codeactiviteit nodig die PowerShell-code bevat om complexe logica uit te voeren.
* Kan de PowerShell-code die is gemaakt door de grafische workflow niet bekijken of rechtstreeks bewerken. U de code die u maakt in codeactiviteiten bekijken.
* Kan niet worden uitgevoerd op een Linux Hybrid Runbook Worker

## <a name="powershell-runbooks"></a>PowerShell-runbooks

PowerShell-runbooks zijn gebaseerd op Windows PowerShell.  U bewerkt de code van het runbook rechtstreeks met de teksteditor in de Azure-portal.  U ook een offline teksteditor gebruiken en [het runbook importeren](manage-runbooks.md) in Azure Automation.

### <a name="advantages"></a>Voordelen

* Implementeer alle complexe logica met PowerShell-code zonder de extra complexiteit van PowerShell Workflow.
* Runbook start sneller dan PowerShell Workflow runbooks omdat deze niet hoeft te worden gecompileerd voordat deze wordt uitgevoerd.
* Kan worden uitgevoerd in Azure of op zowel Linux als Windows Hybrid Runbook Workers

### <a name="limitations"></a>Beperkingen

* Moet bekend zijn met PowerShell scripting.
* Kan geen [parallelle verwerking](automation-powershell-workflow.md#parallel-processing) gebruiken om meerdere acties parallel uit te voeren.
* Kan geen [controlepunten](automation-powershell-workflow.md#checkpoints) gebruiken om runbook te hervatten als er een fout is.
* PowerShell Workflow runbooks en Grafische runbooks kunnen alleen worden opgenomen als onderliggende runbooks met behulp van de cmdlet Start-AzureAutomationRunbook, waarmee een nieuwe taak wordt gemaakt.

### <a name="known-issues"></a>Bekende problemen

Hieronder volgen actuele problemen met PowerShell runbooks.

* PowerShell-runbooks kunnen geen onversleutelde [variabele asset](automation-variables.md) met een null-waarde ophalen.
* PowerShell-runbooks kunnen geen [variabel](automation-variables.md) *~* actief ophalen met in de naam.
* Get-Process in een lus in een PowerShell-runbook kan crashen na ongeveer 80 iteraties.
* Een PowerShell-runbook kan mislukken als het probeert om een grote hoeveelheid gegevens in een keer naar de uitvoerstroom te schrijven.   U dit probleem doorgaans omzeilen door alleen de informatie uit te delen die u nodig hebt bij het werken met grote objecten.  In plaats van bijvoorbeeld iets als *Get-Process*uit te brengen, u alleen de vereiste velden uitvoeren met *Get-Process | Selecteer ProcessName, CPU*.

## <a name="powershell-workflow-runbooks"></a>PowerShell-werkstroomrunboeken

PowerShell-werkstroomrunboeken zijn tekstrunbooks op basis van [Windows PowerShell-werkstroom.](automation-powershell-workflow.md)  U bewerkt de code van het runbook rechtstreeks met de teksteditor in de Azure-portal.  U ook een offline teksteditor gebruiken en [het runbook importeren](manage-runbooks.md) in Azure Automation.

### <a name="advantages"></a>Voordelen

* Implementeer alle complexe logica met PowerShell-werkstroomcode.
* Gebruik [controlepunten](automation-powershell-workflow.md#checkpoints) om de runbook te hervatten als er een fout optreedt.
* Gebruik [parallelle verwerking](automation-powershell-workflow.md#parallel-processing) om meerdere acties parallel uit te voeren.
* Kan andere grafische runbooks en PowerShell Workflow runbooks bevatten als onderliggende runbooks om werkstromen op hoog niveau te maken.

### <a name="limitations"></a>Beperkingen

* De auteur moet bekend zijn met PowerShell Workflow.
* Runbook moet omgaan met de extra complexiteit van PowerShell-werkstroom, zoals [gedeserialiseerde objecten.](automation-powershell-workflow.md#code-changes)
* Runbook duurt langer om te starten dan PowerShell runbooks omdat het moet worden gecompileerd voordat het wordt uitgevoerd.
* PowerShell-runbooks kunnen alleen worden opgenomen als onderliggende runbooks met behulp van de cmdlet Start-AzureAutomationRunbook, waarmee een nieuwe taak wordt gemaakt.
* Kan niet worden uitgevoerd op een Linux Hybrid Runbook Worker

## <a name="python-runbooks"></a>Python runbooks

Python runbooks compileren onder Python 2.  U de code van de runbook rechtstreeks bewerken met de teksteditor in de Azure-portal of met een offline teksteditor en [het runbook importeren](manage-runbooks.md) in Azure Automation.

### <a name="advantages"></a>Voordelen

* Maak gebruik van de robuuste Python-bibliotheken.
* Kan worden uitgevoerd in Azure of op beide Linux Hybrid Runbook Workers. Windows Hybrid Runbook Workers worden ondersteund met [python2.7](https://www.python.org/downloads/release/latest/python2) geïnstalleerd.

### <a name="limitations"></a>Beperkingen

* Moet bekend zijn met Python scripting.
* Alleen Python 2 wordt op dit moment ondersteund, wat betekent dat specifieke functies van Python 3 mislukken.
* Als u bibliotheken van derden wilt gebruiken, moet u [het pakket importeren](python-packages.md) in het automatiseringsaccount voor het te gebruiken.

## <a name="considerations"></a>Overwegingen

Houd rekening met de volgende aanvullende overwegingen bij het bepalen van welk type u voor een bepaald runbook moet gebruiken.

* U runbooks niet converteren van grafisch naar tekstueel type of andersom.
* Er zijn beperkingen met behulp van runbooks van verschillende typen als een onderliggende runbook. Zie [Onderliggende runbooks in Azure Automation](automation-child-runbooks.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

* Zie [Grafisch ontwerpen in Azure Automation](automation-graphical-authoring-intro.md) voor meer informatie over grafische runbookauthoring
* Zie [Windows PowerShell-werkstroom](automation-powershell-workflow.md) leren voor meer informatie over de verschillen tussen PowerShell- en PowerShell-werkstromen voor runbooks
* Zie Een runbook maken of importeren voor meer informatie over het maken of importeren [van een runbook](manage-runbooks.md)
* Zie voor meer informatie over PowerShell, inclusief taalverwijzingen en leermodules, de [PowerShell-documenten](https://docs.microsoft.com/powershell/scripting/overview).
