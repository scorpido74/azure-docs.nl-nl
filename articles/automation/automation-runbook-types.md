---
title: Azure Automation typen runbook
description: Hierin worden de verschillende typen runbooks beschreven die u kunt gebruiken in Azure Automation en overwegingen om te bepalen welk type u moet gebruiken.
services: automation
ms.subservice: process-automation
ms.date: 03/05/2019
ms.topic: conceptual
ms.openlocfilehash: 1ac6347bd8e723f356da4803da54a6ea45a4a71a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81535516"
---
# <a name="azure-automation-runbook-types"></a>Azure Automation typen runbook

De Azure Automation Process Automation-Service ondersteunt diverse typen runbooks, zoals gedefinieerd in de volgende tabel. Zie [Runbook-uitvoering in azure Automation](automation-runbook-execution.md)voor meer informatie over de proces automatiserings omgeving.

| Type | Beschrijving |
|:--- |:--- |
| [Grafisch](#graphical-runbooks)|Grafisch runbook dat is gebaseerd op Windows Power shell en is volledig gemaakt en bewerkt in de grafische editor in Azure Portal. |
| [Grafische power shell-werk stroom](#graphical-runbooks)|Grafisch runbook op basis van een Windows Power shell-werk stroom en is volledig gemaakt en bewerkt in de grafische editor in Azure Portal. |
| [Zo](#powershell-runbooks) |Tekst runbook op basis van Windows Power shell-scripts. |
| [PowerShell-werkstroom](#powershell-workflow-runbooks)|Tekst runbook op basis van Windows Power shell workflow scripting. |
| [Python](#python-runbooks) |Tekst runbook op basis van python-scripts. |

Houd rekening met de volgende overwegingen wanneer u bepaalt welk type moet worden gebruikt voor een bepaald runbook.

* U kunt geen runbooks converteren van grafisch naar tekst type of andersom.
* Er zijn beperkingen bij het gebruik van runbooks van verschillende typen als onderliggende runbooks. Zie voor meer informatie [onderliggende runbooks in azure Automation](automation-child-runbooks.md).

>[!NOTE]
>Dit artikel is bijgewerkt voor het gebruik van de nieuwe Azure PowerShell Az-module. De AzureRM-module kan nog worden gebruikt en krijgt bugoplossingen tot ten minste december 2020. Zie voor meer informatie over de nieuwe Az-module en compatibiliteit met AzureRM [Introductie van de nieuwe Az-module van Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Zie [de module Azure PowerShell installeren](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)voor de installatie-instructies voor AZ module op uw Hybrid Runbook Worker. Voor uw Automation-account kunt u uw modules bijwerken naar de nieuwste versie met behulp van [het bijwerken van Azure PowerShell-modules in azure Automation](automation-update-azure-modules.md).

## <a name="graphical-runbooks"></a>Grafische runbooks

U kunt grafisch en grafisch Power shell-werk stroom-runbooks maken en bewerken met behulp van de grafische editor in de Azure Portal. U kunt dit type runbook echter niet maken of bewerken met een ander hulp programma. Belangrijkste functies van grafische runbooks:

* Kan worden geëxporteerd naar bestanden in uw Automation-account en vervolgens worden geïmporteerd in een ander Automation-account. 
* Power shell-code genereren. 
* Kan tijdens het importeren worden geconverteerd naar of vanuit grafische power shell-werk stroom-runbooks. 

### <a name="advantages"></a>Voordelen

* Gebruik het ontwerp model voor invoegen en configureren van de Visual.
* Richt u op de manier waarop gegevens het proces door lopen.
* Visuele weer gave van beheer processen.
* Neem andere runbooks op als onderliggende runbooks om werk stromen op hoog niveau te maken.
* Stimuleer modulaire programmering.

### <a name="limitations"></a>Beperkingen

* Kan niet buiten de Azure Portal maken of bewerken.
* Voor het uitvoeren van complexe logica is mogelijk een code activiteit vereist die Power shell-code bevat.
* Kan niet converteren naar een van de [tekst indelingen](automation-runbook-types.md), noch kunt u een tekst runbook omzetten in een grafische indeling. 
* Kan Power shell-code die door de grafische werk stroom wordt gemaakt, niet weer geven of rechtstreeks bewerken. U kunt de code die u in code-activiteiten maakt, bekijken.
* Kan geen runbooks uitvoeren op een Linux-Hybrid Runbook Worker. Zie [resources automatiseren in uw Data Center of in de Cloud met behulp van Hybrid Runbook worker](automation-hybrid-runbook-worker.md).

## <a name="powershell-runbooks"></a>Power shell-runbooks

Power shell-runbooks zijn gebaseerd op Windows Power shell. U bewerkt de code van het runbook rechtstreeks met behulp van de tekst editor in het Azure Portal.  U kunt ook een editor voor offline tekst gebruiken en [het runbook importeren](manage-runbooks.md) in azure Automation.

### <a name="advantages"></a>Voordelen

* Implementeer alle complexe logica met Power shell-code zonder de extra complexiteit van Power shell-werk stroom.
* Start sneller dan Power shell workflow-runbooks, omdat ze niet hoeven te worden gecompileerd voordat ze worden uitgevoerd.
* Voer in Azure en in Hybrid Runbook Workers uit voor zowel Windows als Linux.

### <a name="limitations"></a>Beperkingen

* U moet vertrouwd zijn met het uitvoeren van Power shell-scripts.
* Runbooks kunnen geen [parallelle verwerking](automation-powershell-workflow.md#parallel-processing) gebruiken om meerdere acties parallel uit te voeren.
* Runbooks kunnen geen [controle punten](automation-powershell-workflow.md#checkpoints) gebruiken om het runbook te hervatten als er een fout optreedt.
* U kunt alleen Power shell workflow-runbooks en grafische runbooks als onderliggende runbooks toevoegen met behulp van de cmdlet [Start-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.7.0) , waarmee een nieuwe taak wordt gemaakt.

### <a name="known-issues"></a>Bekende problemen

Hieronder vindt u actuele bekende problemen met Power shell-runbooks:

* Power shell-runbooks kunnen een niet-versleutelde [variabele Asset](automation-variables.md) niet ophalen met een null-waarde.
* Power shell-runbooks kunnen een variabele Asset `*~*` met in de naam niet ophalen.
* Een [Get-process-](https://docs.microsoft.com/powershell/module/microsoft.powershell.management/get-process?view=powershell-7) bewerking in een lus in een Power shell-runbook kan vastlopen na ongeveer 80 iteraties.
* Een Power shell-runbook kan mislukken als wordt geprobeerd een grote hoeveelheid gegevens naar de uitvoer stroom tegelijk te schrijven. Normaal gesp roken kunt u dit probleem omzeilen door het runbook uit te voeren alleen de informatie die nodig is om met grote objecten te werken. In plaats van zonder beperkingen te `Get-Process` gebruiken, kunt u bijvoorbeeld de cmdlet uitvoer alleen de vereiste para meters hebben als `Get-Process | Select ProcessName, CPU`in.

## <a name="powershell-workflow-runbooks"></a>Power shell workflow-runbooks

Power shell workflow-runbooks zijn tekst-runbooks op basis van [Windows Power shell-werk stroom](automation-powershell-workflow.md). U bewerkt de code van het runbook rechtstreeks met behulp van de tekst editor in het Azure Portal. U kunt ook een editor voor offline tekst gebruiken en [het runbook importeren](manage-runbooks.md) in azure Automation.

### <a name="advantages"></a>Voordelen

* Implementeer alle complexe logica met Power shell-werk stroom code.
* Gebruik [controle punten](automation-powershell-workflow.md#checkpoints) om de bewerking te hervatten als er een fout optreedt.
* Gebruik [parallelle verwerking](automation-powershell-workflow.md#parallel-processing) om meerdere acties parallel uit te voeren.
* Kan andere grafische runbooks en Power shell-werk stroom runbooks als onderliggende runbooks bevatten om werk stromen op hoog niveau te maken.

### <a name="limitations"></a>Beperkingen

* U moet bekend zijn met de Power shell-werk stroom.
* Runbooks moeten de extra complexiteit van Power shell-werk stroom, zoals [gedeserialiseerd objecten](automation-powershell-workflow.md#code-changes), afhandelen.
* Runbooks nemen meer tijd in beslag dan Power shell-runbooks sinds ze moeten worden gecompileerd voordat ze kunnen worden uitgevoerd.
* U kunt Power shell-runbooks alleen als onderliggende runbooks toevoegen met `Start-AzAutomationRunbook` behulp van de-cmdlet.
* Runbooks kunnen niet worden uitgevoerd op een Linux-Hybrid Runbook Worker.

## <a name="python-runbooks"></a>Python-runbooks

Python runbooks compileren onder python 2. U kunt de code van het runbook rechtstreeks bewerken met behulp van de tekst editor in het Azure Portal. U kunt ook een editor voor offline tekst gebruiken en [het runbook importeren](manage-runbooks.md) in azure Automation.

### <a name="advantages"></a>Voordelen

* Gebruik de robuuste python-bibliotheken.
* Kan worden uitgevoerd in azure of op hybride Runbook-werk rollen van Linux. Windows Hybrid Runbook Workers worden ondersteund met [python 2.7](https://www.python.org/downloads/release/latest/python2) geïnstalleerd.

### <a name="limitations"></a>Beperkingen

* U moet bekend zijn met python-scripts.
* Alleen python 2 wordt momenteel ondersteund. Alle python 3-specifieke functies mislukken.
* Als u bibliotheken van derden wilt gebruiken, moet u [de pakketten importeren](python-packages.md) in het Automation-account.

## <a name="next-steps"></a>Volgende stappen

* Zie [grafisch ontwerpen in azure Automation](automation-graphical-authoring-intro.md)voor meer informatie over het ontwerpen van een grafisch runbook.
* Zie [Windows Power shell-werk stroom leren](automation-powershell-workflow.md)voor meer informatie over de verschillen tussen Power shell-en Power shell-werk stromen voor runbooks.
* Zie [Runbooks beheren in azure Automation](manage-runbooks.md)voor meer informatie over het maken of importeren van een runbook.
* Raadpleeg de [Power shell-documenten](https://docs.microsoft.com/powershell/scripting/overview)voor meer informatie over Power shell, inclusief taal referentie-en leer modules.
