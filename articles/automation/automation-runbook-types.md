---
title: Azure Automation runbook-typen
description: Beschrijft de verschillende typen runbooks die u gebruiken in Azure Automation en overwegingen waarmee u rekening moet houden bij het bepalen van welk type u wilt gebruiken.
services: automation
ms.subservice: process-automation
ms.date: 03/05/2019
ms.topic: conceptual
ms.openlocfilehash: 10f9c829207dc17fa39711e273ae4fbfab3ecbcd
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010168"
---
# <a name="azure-automation-runbook-types"></a>Azure Automation runbook-typen

De automatiseringsservice azure automation voor automatisering azure automation ondersteunt verschillende typen runbooks. De typen worden kort gedefinieerd in de volgende tabel en in de onderstaande secties nader beschreven. Zie [Runbook-uitvoering in Azure Automation](automation-runbook-execution.md)voor meer informatie over de procesautomatiseringsomgeving.

| Type | Beschrijving |
|:--- |:--- |
| [Grafisch](#graphical-runbooks)|Grafische runbook op basis van Windows PowerShell en volledig gemaakt en bewerkt in de grafische editor in Azure portal. |
| [Grafische PowerShell-werkstroom](#graphical-runbooks)|Grafische runbook op basis van Windows PowerShell Workflow en volledig gemaakt en bewerkt in de grafische editor in Azure portal. |
| [PowerShell](#powershell-runbooks) |Tekstrunbook op basis van Windows PowerShell-scripting. |
| [PowerShell-werkstroom](#powershell-workflow-runbooks)|Tekstrunbook op basis van Windows PowerShell Workflow scripting. |
| [Python](#python-runbooks) |Tekstrunbook op basis van Python-scripting. |

## <a name="graphical-runbooks"></a>Grafische runbooks

U grafische en grafische PowerShell Workflow-runbooks maken en bewerken met behulp van de grafische editor in de Azure-portal. U dit type runbook echter niet maken of bewerken met een ander gereedschap.

Een grafisch runbook heeft de volgende hoofdkenmerken:

* Kan worden geëxporteerd naar een bestand in uw Automatiseringsaccount en vervolgens worden geïmporteerd naar een ander Automatiseringsaccount. 
* Genereert PowerShell-code. 
* Kan tijdens het importeren worden geconverteerd naar of van een grafische PowerShell-werkstroom. 

### <a name="advantages"></a>Voordelen

* Maakt gebruik van het ontwerpmodel voor het configureren van visuele invoegkoppeling.
* Richt zich op hoe gegevens door het proces stromen.
* Staat visueel voor beheerprocessen.
* Bevat andere runbooks als onderliggende runbooks om werkstromen op hoog niveau te maken.
* Moedigt modulaire programmering aan.

### <a name="limitations"></a>Beperkingen

* Kan niet worden gemaakt of bewerkt buiten de Azure-portal.
* Mogelijk is een codeactiviteit nodig die PowerShell-code bevat om complexe logica uit te voeren.
* Kan niet worden geconverteerd naar een van de [tekstindelingen](automation-runbook-types.md)en kan ook niet worden geconverteerd naar grafische indeling. 
* Hiermee u de PowerShell-code die de grafische werkstroom maakt, niet bekijken of rechtstreeks bewerken. U de code die u maakt in codeactiviteiten bekijken.
* Draait niet op een Linux Hybrid Runbook Worker. Zie [Resources automatiseren in uw datacenter of cloud met Hybride Runbook Worker](automation-hybrid-runbook-worker.md).

## <a name="powershell-runbooks"></a>PowerShell-runbooks

PowerShell-runbooks zijn gebaseerd op Windows PowerShell. U bewerkt de code van het runbook rechtstreeks met de teksteditor in de Azure-portal.  U ook een offline teksteditor gebruiken en [het runbook importeren](manage-runbooks.md) in Azure Automation.

### <a name="advantages"></a>Voordelen

* Implementeer alle complexe logica met PowerShell-code zonder de extra complexiteit van PowerShell Workflow.
* Runbook start sneller dan PowerShell Workflow runbooks omdat deze niet hoeft te worden gecompileerd voordat deze wordt uitgevoerd.
* Kan worden uitgevoerd in Azure of op zowel Linux- als Windows Hybrid Runbook Workers

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
* Kan niet draaien op een Linux Hybrid Runbook Worker.

## <a name="python-runbooks"></a>Python runbooks

Python runbooks compileren onder Python 2. U de code van de runbook rechtstreeks bewerken met de teksteditor in de Azure-portal of met een offline teksteditor en [het runbook importeren](manage-runbooks.md) in Azure Automation.

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
