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
ms.openlocfilehash: dfe62c54bfb10d70f1dbf19daec90eec68e66431
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383156"
---
# <a name="remediate-noncompliant-dsc-servers"></a>Niet-compatibele DSC-servers herstellen

Wanneer servers zijn geregistreerd bij Azure Automation State `ApplyOnly`Configuration, is de configuratiemodus ingesteld op , `ApplyandMonitor`of `ApplyAndAutoCorrect`. Als de modus niet `ApplyAndAutoCorrect`is ingesteld op , blijven servers die om welke reden dan ook van een compatibele status afglijden niet-compatibel totdat ze handmatig zijn gecorrigeerd.

Azure compute biedt een functie met de naam Run Command waarmee klanten scripts in virtuele machines kunnen uitvoeren.
Dit document biedt voorbeeldscripts voor deze functie wanneer de configuratiedrift handmatig wordt gecorrigeerd.

## <a name="correct-drift-of-windows-virtual-machines-using-powershell"></a>Drift van Virtuele Windows-machines corrigeren met PowerShell

Zie de documentatiepagina [PowerShell-scripts uitvoeren in uw Windows VM met Opdracht](/azure/virtual-machines/windows/run-command)Uitvoeren voor stapsgewijze instructies met de functie Opdracht uitvoeren op virtuele Windows-machines.

Als u een configuratieknooppunt azure automation state forceert om de nieuwste configuratie te downloaden en toe te passen, gebruikt u de cmdlet [Update-DscConfiguration.](/powershell/module/psdesiredstateconfiguration/update-dscconfiguration)

```powershell
Update-DscConfiguration -Wait -Verbose
```

## <a name="correct-drift-of-linux-virtual-machines"></a>Juiste drift van Linux virtuele machines

Vergelijkbare functionaliteit is momenteel niet beschikbaar voor Linux-servers.
De enige optie is om het registratieproces te herhalen.
Voor Azure-knooppunten u drift vanaf de Azure-portal of met cmdlets van de Az-module corrigeren. Details over dit proces worden gedocumenteerd in [Onboarding-machines voor beheer door Azure Automation State Configuration](automation-dsc-onboarding.md#onboard-a-vm-using-azure-portal).
Voor hybride knooppunten u drift corrigeren met behulp van de meegeleverde Python-scripts.
Zie [PowerShell DSC voor Linux repo](https://github.com/Microsoft/PowerShell-DSC-for-Linux#performing-dsc-operations-from-the-linux-computer).

## <a name="next-steps"></a>Volgende stappen

- Zie [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
)voor een PowerShell-cmdletreferentie.
- Zie [Continue implementatie met Azure Automation State Configuration en Chocolatey](automation-dsc-cd-chocolatey.md)voor een voorbeeld van het gebruik van Azure Automation State Configuration in a continuous deployment pipeline.