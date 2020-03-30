---
title: Az-modules gebruiken in Azure Automation
description: In dit artikel vindt u informatie met Az-modules in Azure Automation
services: automation
ms.subservice: shared-capabilities
ms.date: 02/08/2019
ms.topic: conceptual
ms.openlocfilehash: dfbf54c19aef00cbda886a4531797cda7ef3a191
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76986101"
---
# <a name="az-module-support-in-azure-automation"></a>Az module support in Azure Automation (Ondersteuning voor Az-module in Azure Automation)

Azure-automatisering ondersteunt de mogelijkheid om de [Azure Powershell Az-module](/powershell/azure/new-azureps-module-az?view=azps-1.1.0) in uw runbooks te gebruiken. De Az-module wordt niet automatisch geïmporteerd in nieuwe of bestaande automatiseringsaccounts. In dit artikel wordt besproken hoe u Az-modules gebruiken met Azure Automation.

## <a name="considerations"></a>Overwegingen

Er zijn veel dingen om rekening mee te houden bij het gebruik van de Az-module in Azure Automation. Runbooks en modules kunnen worden gebruikt door oplossingen op een hoger niveau in uw automatiseringsaccount. Het bewerken van runbooks of het upgraden van modules kan mogelijk problemen veroorzaken met uw runbooks. U moet alle runbooks en oplossingen zorgvuldig testen in `Az` een afzonderlijk automatiseringsaccount voordat u de nieuwe modules importeert. Wijzigingen aan modules kunnen een negatieve invloed hebben op de [Start/Stop-oplossing.](automation-solution-vm-management.md) We raden u aan modules en runbooks te wijzigen in automatiseringsaccounts die oplossingen bevatten. Dit gedrag is niet specifiek voor de Az-modules. Met dit gedrag moet rekening worden gehouden bij het aanbrengen van wijzigingen in uw automatiseringsaccount.

Als u `Az` een module importeert in uw automatiseringsaccount, wordt de module niet automatisch geïmporteerd in de PowerShell-sessie die de runbooks gebruiken. Modules worden in de volgende situaties in de PowerShell-sessie geïmporteerd:

* Wanneer een cmdlet van een module wordt aangeroepen uit een runbook
* Wanneer een runbook het expliciet `Import-Module` importeert met de cmdlet
* Wanneer een andere module afhankelijk van de module wordt geïmporteerd in een PowerShell-sessie

> [!IMPORTANT]
> Het is belangrijk om ervoor te zorgen dat `Az` runbooks in een automatiseringsaccount alleen importeren of `AzureRM` modules in de PowerShell-sessies worden gebruikt door runbooks en niet beide. Als `Az` het runbook eerder `AzureRM` is geïmporteerd in een runbook, wordt het runbook voltooid, maar een fout die verwijst naar de [get_SerializationSettings-methode](troubleshoot/runbooks.md#get-serializationsettings) wordt weergegeven in de taakstromen en cmdlets mogelijk niet goed zijn uitgevoerd. Als u `AzureRM` importeert en vervolgens `Az` uw runbook nog steeds wordt voltooid, `Az` maar `AzureRM` u ziet een fout in de taakstromen waarin staat dat beide en niet kunnen worden geïmporteerd in dezelfde sessie of worden gebruikt in hetzelfde runbook.

## <a name="migrating-to-az-modules"></a>Migreren naar Az-modules

Het wordt aanbevolen om de migratie naar het gebruik van Az-modules te testen in plaats van AzureRM-modules in een testautomatiseringsaccount. Zodra dat automatiseringsaccount is gemaakt, u de volgende stappen gebruiken om ervoor te zorgen dat uw migratie soepel verloopt:

### <a name="stop-and-unschedule-all-runbook-that-uses-azurerm-modules"></a>Alle runbook met AzureRM-modules stoppen en deplanning ongedaan maken

Om ervoor te zorgen dat u geen `AzureRM` bestaande runbooks uitvoert die cmdlets gebruiken, moet u alle runbooks die modules gebruiken `AzureRM` stoppen en uitplannen. U zien welke schema's bestaan en welke schema's moeten worden verwijderd door het volgende voorbeeld uit te voeren:

  ```powershell-interactive
  Get-AzureRmAutomationSchedule -AutomationAccountName "<AutomationAccountName>" -ResourceGroupName "<ResourceGroupName>" | Remove-AzureRmAutomationSchedule -WhatIf
  ```

Het is belangrijk om elk schema afzonderlijk te bekijken om ervoor te zorgen dat u het in de toekomst opnieuw plannen voor uw runbooks indien nodig.

### <a name="import-the-az-modules"></a>De Az-modules importeren

Importeer alleen de Az-modules die nodig zijn voor uw runbooks. Importeer de rollupmodule `Az` niet, omdat deze `Az.*` alle te importeren modules bevat. Deze richtlijnen zijn voor alle modules hetzelfde.

De module [Az.Accounts](https://www.powershellgallery.com/packages/Az.Accounts/1.1.0) is een `Az.*` afhankelijkheid voor de andere modules. Daarom moet deze module worden geïmporteerd in uw automatiseringsaccount voordat u andere modules importeert.

Selecteer **modules** onder Gedeelde **bronnen**in uw automatiseringsaccount. Klik **op Galerie bladeren** om de pagina Bladeren door de galerij te **openen.**  Voer in de zoekbalk de modulenaam in (zoals `Az.Accounts`). Klik op de pagina PowerShell-module op **Importeren** om de module te importeren in uw automatiseringsaccount.

![Modules importeren uit automatiseringsaccount](media/az-modules/import-module.png)

Dit importproces kan ook via de [PowerShell Gallery](https://www.powershellgallery.com) worden uitgevoerd door te zoeken naar de module. Zodra u de module hebt gevonden, selecteert u deze en klikt u onder het tabblad **Azure Automation** op **Implementeren voor Azure Automation**.

![Modules rechtstreeks importeren vanuit galerie](media/az-modules/import-gallery.png)

## <a name="test-your-runbooks"></a>Uw runbooks testen

Zodra `Az` de modules zijn geïmporteerd in uw automatiseringsaccount, u nu beginnen met het bewerken van uw runbooks om in plaats daarvan de Az-module te gebruiken. De meerderheid van de cmdlets hebben `AzureRM` dezelfde naam, behalve voor is veranderd in `Az`. Zie [lijst met uitzonderingen](/powershell/azure/migrate-from-azurerm-to-az#update-cmdlets-modules-and-parameters)voor een lijst met modules die dit proces niet volgen.

Een manier om uw runbooks te testen voordat u uw runbook `Enable-AzureRMAlias -Scope Process` wijzigt om de nieuwe cmdlets te gebruiken, is door aan het begin van een runbook te gebruiken. Door dit aan je runbook toe te voegen, kan je runbook zonder wijzigingen worden uitgevoerd.

## <a name="after-migration-details"></a>Na migratiegegevens

Nadat de migratie is voltooid, start `AzureRM` u geen runbooks meer met modules op het account. Het wordt ook aanbevolen om geen `AzureRM` modules te importeren of bij te werken op dit account. Vanaf dit moment, overwegen dit `Az`account gemigreerd naar , en werken met `Az` modules alleen. Wanneer een nieuw automatiseringsaccount `AzureRM` wordt gemaakt, worden de bestaande modules nog steeds `AzureRM` geïnstalleerd en worden de runbooks van de zelfstudie nog steeds geschreven met cmdlets. Deze runbooks mogen niet worden uitgevoerd.

## <a name="next-steps"></a>Volgende stappen

Zie [Aan de slag met az-module](/powershell/azure/get-started-azureps?view=azps-1.1.0)voor meer informatie over het gebruik van Az-modules.
