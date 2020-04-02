---
title: Az module support in Azure Automation (Ondersteuning voor Az-module in Azure Automation)
description: In dit artikel vindt u informatie over het gebruik van Az-modules in Azure Automation.
services: automation
ms.subservice: shared-capabilities
ms.date: 02/08/2019
ms.topic: conceptual
ms.openlocfilehash: 776834937d81a3ba84e3c1db56496a7d951d7982
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548348"
---
# <a name="az-module-support-in-azure-automation"></a>Az module support in Azure Automation (Ondersteuning voor Az-module in Azure Automation)

Azure Automation ondersteunt het gebruik van [azure PowerShell Az-module](/powershell/azure/new-azureps-module-az?view=azps-1.1.0) in uw runbooks. De Az-module wordt niet automatisch geïmporteerd in nieuwe of bestaande automatiseringsaccounts. 

## <a name="considerations"></a>Overwegingen

Er zijn veel dingen om rekening mee te houden bij het gebruik van de Az-module in Azure Automation. Runbooks en modules kunnen worden gebruikt door oplossingen op een hoger niveau in uw Automation-account. Het bewerken van runbooks of het upgraden van modules kan mogelijk problemen veroorzaken met uw runbooks. U moet alle runbooks en oplossingen zorgvuldig testen in `Az` een afzonderlijk Automation-account voordat u de nieuwe modules importeert. Wijzigingen aan modules kunnen een negatieve invloed hebben op de [Start/Stop-oplossing.](automation-solution-vm-management.md) We raden u aan modules en runbooks te wijzigen in automatiseringsaccounts die oplossingen bevatten. Dit gedrag is niet specifiek voor de Az-modules. Met dit gedrag moet rekening worden gehouden bij het aanbrengen van wijzigingen in uw Automatiseringsaccount.

Als u `Az` een module importeert in uw Automatiseringsaccount, wordt de module niet automatisch geïmporteerd in de PowerShell-sessie die de runbooks gebruiken. Modules worden in de volgende situaties in de PowerShell-sessie geïmporteerd:

* Wanneer een cmdlet van een module wordt aangeroepen uit een runbook
* Wanneer een runbook het expliciet `Import-Module` importeert met de cmdlet
* Wanneer een andere module afhankelijk van de module wordt geïmporteerd in een PowerShell-sessie

> [!IMPORTANT]
> Het is belangrijk om ervoor te zorgen dat `Az` runbooks in een Automation-account alleen importeren of `AzureRM` modules in de PowerShell-sessies worden gebruikt door runbooks en niet beide. Als `Az` het runbook eerder `AzureRM` in een runbook is geïmporteerd, wordt deze voltooid, maar een fout die verwijst naar de [Get_SerializationSettings](troubleshoot/runbooks.md#get-serializationsettings) cmdlet wordt weergegeven in de taakstromen en cmdlets mogelijk niet goed wordt uitgevoerd. Als u `AzureRM` importeert en vervolgens, `Az`wordt uw runbook nog steeds voltooid, `Az` `AzureRM` maar ontvangt u een foutmelding in de taakstromen waarin staat dat beide en niet in dezelfde sessie kunnen worden geïmporteerd of in hetzelfde runbook kunnen worden gebruikt.

## <a name="migrating-to-az-modules"></a>Migreren naar Az-modules

Het wordt aanbevolen om de migratie naar AZ-modules te testen in een testautomation-account. Zodra dat Automatiseringsaccount is gemaakt, u de instructies in deze sectie gebruiken om met de modules te werken.

### <a name="stop-and-unschedule-all-runbooks-that-use-azurerm-cmdlets"></a>Alle runbooks met AzureRM-cmdlets stoppen en uitplannen

Om ervoor te zorgen dat u geen `AzureRM` bestaande runbooks uitvoert die cmdlets gebruiken, moet u alle runbooks die modules gebruiken `AzureRM` stoppen en uitplannen. U zien welke schema's bestaan en welke schema's moeten worden verwijderd door code uit te voeren die vergelijkbaar is met dit voorbeeld.

  ```powershell-interactive
  Get-AzureRmAutomationSchedule -AutomationAccountName "<AutomationAccountName>" -ResourceGroupName "<ResourceGroupName>" | Remove-AzureRmAutomationSchedule -WhatIf
  ```

Het is belangrijk om elk schema afzonderlijk te bekijken om ervoor te zorgen dat u het in de toekomst opnieuw plannen voor uw runbooks indien nodig.

### <a name="import-the-az-modules"></a>De Az-modules importeren

Importeer alleen de Az-modules die nodig zijn voor uw runbooks. Importeer de rollupmodule `Az` niet, want `Az.*` deze bevat alle modules. Deze richtlijnen zijn voor alle modules hetzelfde.

De module [Az.Accounts](https://www.powershellgallery.com/packages/Az.Accounts/1.1.0) is een `Az.*` afhankelijkheid voor de andere modules. Daarom moet deze module worden geïmporteerd in uw Automatiseringsaccount voordat u andere modules importeert.

Selecteer **modules** onder Gedeelde **bronnen**in uw automatiseringsaccount. Klik **op Galerie bladeren** om de pagina Bladeren door de galerij te **openen.**  Voer in de zoekbalk de modulenaam in (zoals `Az.Accounts`). Klik op de pagina PowerShell-module op **Importeren** om de module te importeren in uw automatiseringsaccount.

![Modules importeren uit automatiseringsaccount](media/az-modules/import-module.png)

Dit importproces kan ook via de [PowerShell Gallery](https://www.powershellgallery.com) worden uitgevoerd door te zoeken naar de module voor import. Zodra u de module hebt gevonden, selecteert u deze en klikt u onder het tabblad **Azure Automation** op **Implementeren voor Azure Automation**.

![Modules rechtstreeks importeren vanuit galerie](media/az-modules/import-gallery.png)

## <a name="testing-your-runbooks"></a>Uw runbooks testen

Zodra `Az` de modules zijn geïmporteerd in uw Automation-account, u beginnen met het bewerken van uw runbooks om de Az-modules te gebruiken. De meerderheid van de cmdlets hebben `AzureRM` dezelfde namen, behalve dat is veranderd in `Az`. Zie lijst met [uitzonderingen](/powershell/azure/migrate-from-azurerm-to-az#update-cmdlets-modules-and-parameters)voor een lijst met modules die deze naamgevingsconventie niet volgen.

Een manier om de wijziging van een runbook te testen `Enable-AzureRMAlias -Scope Process` om de nieuwe cmdlets te gebruiken is door gebruik te maken van aan het begin van het runbook. Door deze opdracht toe te voegen aan uw runbook, kan het script zonder wijzigingen worden uitgevoerd.

## <a name="after-migration-details"></a>Details na migratie

Nadat de migratie is voltooid, probeert u `AzureRM` geen runbooks meer te starten met modules op het automatiseringsaccount. Het wordt ook aanbevolen om geen `AzureRM` modules op het account te importeren of bij te werken. Overweeg het account `Az`gemigreerd `Az` naar , en werken met modules alleen. 

Wanneer een nieuw Automation-account `AzureRM` wordt gemaakt, worden de bestaande modules nog steeds geïnstalleerd. U de zelfstudierunboeken nog steeds bijwerken met `AzureRM` cmdlets. Je moet deze runbooks niet uitvoeren.

## <a name="next-steps"></a>Volgende stappen

Zie [Aan de slag met az-module](/powershell/azure/get-started-azureps?view=azps-1.1.0)voor meer informatie over het gebruik van Az-modules.
