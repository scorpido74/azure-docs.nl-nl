---
title: Azure Automation typen Runbook
description: 'Hierin worden de verschillende typen runbooks beschreven die u kunt gebruiken in Azure Automation en overwegingen waarmee u rekening moet houden bij het bepalen welk type u wilt gebruiken. '
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 03/05/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 6c29015e2fd327d74183d5fbbd6214152507e517
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/09/2019
ms.locfileid: "73886770"
---
# <a name="azure-automation-runbook-types"></a>Azure Automation typen runbook

Azure Automation ondersteunt diverse typen runbooks die kort worden beschreven in de volgende tabel.  In de volgende secties vindt u meer informatie over elk type, met inbegrip van overwegingen voor het gebruik ervan.

| Type | Beschrijving |
|:--- |:--- |
| [Grafisch](#graphical-runbooks)|Op basis van Windows Power shell en worden volledig gemaakt en bewerkt in de grafische editor in Azure Portal. |
| [Grafische power shell-werk stroom](#graphical-runbooks)|Op basis van een Windows Power shell-werk stroom en worden volledig gemaakt en bewerkt in de grafische editor in Azure Portal. |
| [PowerShell](#powershell-runbooks) |Tekst runbook op basis van een Windows Power shell-script. |
| [PowerShell-werkstroom](#powershell-workflow-runbooks)|Tekst runbook op basis van een Windows Power shell-werk stroom. |
| [Python](#python-runbooks) |Tekst runbook op basis van python. |

## <a name="graphical-runbooks"></a>Grafische runbooks

[Grafisch](automation-runbook-types.md#graphical-runbooks) en grafisch runbooks voor grafische power shell-werk stromen worden gemaakt en bewerkt met de grafische editor in de Azure Portal.  U kunt ze exporteren naar een bestand en vervolgens importeren in een ander Automation-account. Maar u kunt ze niet maken of bewerken met een ander hulp programma. Grafische runbooks genereren Power shell-code, maar u kunt de code niet rechtstreeks weer geven of wijzigen. Grafische runbooks kunnen niet worden geconverteerd naar een van de [tekst indelingen](automation-runbook-types.md)en kunnen niet worden geconverteerd naar een grafische vorm van een tekst. Grafische runbooks kunnen tijdens het importeren worden omgezet in grafische power shell-werk stroom-runbooks en op een andere manier.

### <a name="advantages"></a>Voordelen

* Visueel element voor invoegen en koppelen-ontwerp model configureren
* Focussen op de manier waarop gegevens door het proces stromen
* Beheer processen visueel weer geven
* Andere runbooks als onderliggende runbooks toevoegen om werk stromen op hoog niveau te maken
* Stimuleert modulaire programmering

### <a name="limitations"></a>Beperkingen

* Kan het runbook niet bewerken buiten Azure Portal.
* Voor het uitvoeren van complexe logica is mogelijk een code activiteit vereist die Power shell-code bevat.
* Kan de Power shell-code die door de grafische werk stroom is gemaakt, niet weer geven of rechtstreeks bewerken. U kunt de code die u in code-activiteiten maakt, bekijken.
* Kan niet worden uitgevoerd op een Linux-Hybrid Runbook Worker

## <a name="powershell-runbooks"></a>Power shell-runbooks

Power shell-runbooks zijn gebaseerd op Windows Power shell.  U bewerkt de code van het runbook rechtstreeks met behulp van de tekst editor in het Azure Portal.  U kunt ook een editor voor offline tekst gebruiken en [het runbook importeren](manage-runbooks.md) in azure Automation.

### <a name="advantages"></a>Voordelen

* Implementeer alle complexe logica met Power shell-code zonder de extra complexiteit van Power shell-werk stroom.
* Het Runbook start sneller dan Power shell workflow-runbooks omdat het niet moet worden gecompileerd voordat het wordt uitgevoerd.
* Kan worden uitgevoerd in azure of op zowel Linux-als Windows Hybrid Runbook Workers

### <a name="limitations"></a>Beperkingen

* Moet bekend zijn met Power shell-scripts.
* Kan [parallelle verwerking](automation-powershell-workflow.md#parallel-processing) niet gebruiken om meerdere acties parallel uit te voeren.
* Kan geen [controle punten](automation-powershell-workflow.md#checkpoints) gebruiken om het runbook te hervatten als er een fout optreedt.
* Power shell workflow-runbooks en grafische runbooks kunnen alleen worden opgenomen als onderliggende runbooks met behulp van de start-AzureAutomationRunbook-cmdlet, waarmee een nieuwe taak wordt gemaakt.

### <a name="known-issues"></a>Bekende problemen

Hieronder vindt u actuele bekende problemen met Power shell-runbooks.

* Power shell-runbooks kunnen een niet-versleutelde [variabele Asset](automation-variables.md) niet ophalen met een null-waarde.
* Power shell-runbooks kunnen geen [variabele activa](automation-variables.md) ophalen met *~* in de naam.
* Get-process in een lus in een Power shell-runbook kan na ongeveer 80 iteraties vastlopen.
* Een Power shell-runbook kan mislukken als wordt geprobeerd een grote hoeveelheid gegevens naar de uitvoer stroom tegelijk te schrijven.   Normaal gesp roken kunt u dit probleem omzeilen door alleen de informatie die u nodig hebt bij het werken met grote objecten uit te voeren.  In plaats van iets zoals *Get-process*uit te voeren, kunt u bijvoorbeeld alleen de vereiste velden uitvoeren met *Get-process | Selecteer verwerkings-en CPU*.

## <a name="powershell-workflow-runbooks"></a>Power shell workflow-runbooks

Power shell workflow-runbooks zijn tekst-runbooks op basis van [Windows Power shell-werk stroom](automation-powershell-workflow.md).  U bewerkt de code van het runbook rechtstreeks met behulp van de tekst editor in het Azure Portal.  U kunt ook een editor voor offline tekst gebruiken en [het runbook importeren](manage-runbooks.md) in azure Automation.

### <a name="advantages"></a>Voordelen

* Implementeer alle complexe logica met Power shell-werk stroom code.
* Gebruik [controle punten](automation-powershell-workflow.md#checkpoints) om het runbook te hervatten als er een fout optreedt.
* Gebruik [parallelle verwerking](automation-powershell-workflow.md#parallel-processing) om meerdere acties parallel uit te voeren.
* Kan andere grafische runbooks en Power shell-werk stroom runbooks als onderliggende runbooks bevatten om werk stromen op hoog niveau te maken.

### <a name="limitations"></a>Beperkingen

* De auteur moet bekend zijn met de Power shell-werk stroom.
* Runbook moet de extra complexiteit van Power shell-werk stroom, zoals [gedeserialiseerd objecten](automation-powershell-workflow.md#code-changes), afhandelen.
* Het starten van het Runbook duurt langer dan Power shell-runbooks sinds het moet worden gecompileerd voordat het wordt uitgevoerd.
* Power shell-runbooks kunnen alleen worden opgenomen als onderliggende runbooks met behulp van de cmdlet start-AzureAutomationRunbook, waarmee een nieuwe taak wordt gemaakt.
* Kan niet worden uitgevoerd op een Linux-Hybrid Runbook Worker

## <a name="python-runbooks"></a>Python-runbooks

Python runbooks compileren onder python 2.  U kunt de code van het runbook rechtstreeks bewerken met behulp van de tekst editor in het Azure Portal, of met een editor voor offline tekst en [het runbook importeren](manage-runbooks.md) in azure Automation.

### <a name="advantages"></a>Voordelen

* Gebruik de robuuste python-bibliotheken.
* Kan worden uitgevoerd in azure of op zowel Linux Hybrid Runbook Workers. Windows Hybrid Runbook Workers worden ondersteund met [python 2.7](https://www.python.org/downloads/release/latest/python2) geïnstalleerd.

### <a name="limitations"></a>Beperkingen

* Moet bekend zijn met python-scripts.
* Alleen python 2 wordt op het moment ondersteund, wat betekent dat er geen python 3-specifieke functies kunnen worden uitgevoerd.
* Als u bibliotheken van derden wilt gebruiken, moet u [het pakket importeren](python-packages.md) in het Automation-account dat moet worden gebruikt.

## <a name="considerations"></a>Overwegingen

Houd rekening met de volgende aanvullende overwegingen bij het bepalen van het type dat voor een bepaald runbook moet worden gebruikt.

* U kunt geen runbooks converteren van grafisch naar tekst type of andersom.
* Er zijn beperkingen bij het gebruik van runbooks van verschillende typen als onderliggend runbook. Zie voor meer informatie [onderliggende runbooks in azure Automation](automation-child-runbooks.md).

## <a name="next-steps"></a>Volgende stappen

* Zie voor meer informatie over het ontwerpen van een grafisch runbook [grafisch ontwerpen in azure Automation](automation-graphical-authoring-intro.md)
* Zie [Windows Power shell-werk stroom leren](automation-powershell-workflow.md) voor informatie over de verschillen tussen Power shell-en Power shell-werk stromen voor runbooks.
* Zie [een Runbook maken of importeren](manage-runbooks.md) voor meer informatie over het maken of importeren van een runbook
* Raadpleeg de [Power shell-documenten](https://docs.microsoft.com/powershell/scripting/overview)voor meer informatie over Power shell, inclusief taal referentie-en leer modules.
