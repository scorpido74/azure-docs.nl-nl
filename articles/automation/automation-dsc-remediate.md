---
title: Niet-compatibele Azure Automation State Configuration-servers herstellen
description: Configuraties op aanvraag opnieuw toepassen op servers waar de configuratiestatus is afgedreven
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.topic: conceptual
ms.date: 07/17/2019
manager: nirb
ms.openlocfilehash: f4ca76f4be9d00e185f8774fc33296d1af1aeece
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585504"
---
# <a name="remediate-non-compliant-dsc-servers"></a>Niet-compatibele DSC-servers herstellen

Wanneer servers zijn geregistreerd bij Azure Automation State Configuration, is de 'Configuratiemodus' ingesteld op ApplyOnly, ApplyandMonitor of ApplyAndAutoCorrect.
Als de modus niet is ingesteld op AutoCorrectie, blijven servers die om welke reden dan ook van een compatibele status afglijden niet-compatibel totdat ze handmatig zijn gecorrigeerd.

Azure compute biedt een functie met de naam Run Command waarmee klanten scripts in virtuele machines kunnen uitvoeren.
Dit document biedt voorbeeldscripts voor deze functie wanneer de configuratiedrift handmatig wordt gecorrigeerd.

## <a name="correct-drift-of-windows-virtual-machines-using-powershell"></a>Drift van Virtuele Windows-machines corrigeren met PowerShell

Zie de documentatiepagina [PowerShell-scripts uitvoeren in uw Windows VM met Opdracht](/azure/virtual-machines/windows/run-command)uitvoeren voor stapsgewijze instructies met de functie Opdracht uitvoeren op virtuele Windows-machines.

Als u een configuratieknooppunt azure automation state forceert om `Update-DscConfiguration` de nieuwste configuratie te downloaden en toe te passen, gebruikt u de cmdlet.
Zie voor meer informatie de cmdlet-documentatie [Update-DscConfiguration](/powershell/module/psdesiredstateconfiguration/update-dscconfiguration).

```powershell
Update-DscConfiguration -Wait -Verbose
```

## <a name="correct-drift-of-linux-virtual-machines"></a>Juiste drift van Linux virtuele machines

Vergelijkbare functionaliteit is momenteel niet beschikbaar voor Linux-servers.
De enige optie is om het registratieproces te herhalen.
Voor Azure-knooppunten kan driftcorrectie worden uitgevoerd vanaf de portal of met az-automatisering-cmdlets.
Details over dit proces worden gedocumenteerd in de pagina [Onboarding-machines voor beheer door Azure Automation State Configuration](/azure/automation/automation-dsc-onboarding#onboard-a-vm-using-azure-portal).
Voor hybride knooppunten kan driftcorrectie worden uitgevoerd met behulp van de meegeleverde Python-scripts.
Zie de documentatie in [PowerShell DSC voor Linux repo](https://github.com/Microsoft/PowerShell-DSC-for-Linux#performing-dsc-operations-from-the-linux-computer).

## <a name="next-steps"></a>Volgende stappen

- Zie [Cmdlets Azure Automation State Configuration cmdlets](/powershell/module/azurerm.automation/#automation) voor PowerShell-cmdlet
- Zie [Continue implementatie met Azure Automation State Configuration en Chocolatey](automation-dsc-cd-chocolatey.md) voor een voorbeeld van het gebruik van Azure Automation State Configuration in a continuous deployment pipeline.
