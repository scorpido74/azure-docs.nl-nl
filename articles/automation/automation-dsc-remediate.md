---
title: Niet-compatibele Azure Automation status configuratie servers herstellen
description: Configuraties op aanvraag opnieuw Toep assen op servers waar de configuratie status is gedrijfd
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.topic: conceptual
ms.date: 07/17/2019
manager: nirb
ms.openlocfilehash: f871b406793e455c857ca14c83434c9ed3e004df
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/09/2020
ms.locfileid: "82993846"
---
# <a name="remediate-noncompliant-dsc-servers"></a>Niet-compatibele DSC-servers herstellen

Als servers zijn geregistreerd met Azure Automation status configuratie, wordt de configuratie modus ingesteld op `ApplyOnly`, `ApplyandMonitor`of `ApplyAndAutoCorrect`. Als de modus niet is ingesteld `ApplyAndAutoCorrect`op, blijven servers die van een compatibele status zijn, om welke reden dan ook niet-compatibel.

Azure Compute biedt een functie met de naam run-opdracht waarmee klanten scripts in virtuele machines kunnen uitvoeren.
Dit document bevat voorbeeld scripts voor deze functie wanneer de configuratie-drift hand matig wordt gecorrigeerd.

## <a name="correct-drift-of-windows-virtual-machines-using-powershell"></a>Juiste drift van virtuele Windows-machines met behulp van Power shell

Zie de documentatie pagina [Power shell-scripts uitvoeren in uw Windows-VM met de opdracht uitvoeren](/azure/virtual-machines/windows/run-command)voor stapsgewijze instructies voor het uitvoeren van de opdracht functie op virtuele Windows-machines.

Gebruik de cmdlet [Update-DscConfiguration](/powershell/module/psdesiredstateconfiguration/update-dscconfiguration) om een configuratie knooppunt van Azure Automation status af te dwingen om de nieuwste configuratie te downloaden en toe te passen.

```powershell
Update-DscConfiguration -Wait -Verbose
```

## <a name="correct-drift-of-linux-virtual-machines"></a>Juiste drift van virtuele Linux-machines

Vergelijk bare functionaliteit is momenteel niet beschikbaar voor Linux-servers.
De enige optie is om het registratie proces te herhalen.
Voor Azure-knoop punten kunt u de drift in het Azure Portal corrigeren of met AZ module-cmdlets. Details over dit proces worden beschreven in [onboarding-machines voor beheer door Azure Automation status configuratie](automation-dsc-onboarding.md#enable-a-vm-using-azure-portal).
Voor hybride knoop punten kunt u de drift corrigeren met behulp van de meegeleverde python-scripts.
Zie [Power shell DSC voor Linux opslag plaats](https://github.com/Microsoft/PowerShell-DSC-for-Linux#performing-dsc-operations-from-the-linux-computer).

## <a name="next-steps"></a>Volgende stappen

- Zie [AZ. Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
)(Engelstalig) voor een Power shell-cmdlet-verwijzing.
- Voor een voor beeld van het gebruik van Azure Automation status configuratie in een pijp lijn voor continue implementatie raadpleegt u [continue implementatie met behulp van Azure Automation-status configuratie en Choco lade](automation-dsc-cd-chocolatey.md).