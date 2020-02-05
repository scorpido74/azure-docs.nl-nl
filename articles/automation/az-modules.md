---
title: Az-modules gebruiken in Azure Automation
description: Dit artikel bevat informatie over het gebruik van AZ-modules in Azure Automation
services: automation
ms.subservice: shared-capabilities
ms.date: 02/08/2019
ms.topic: conceptual
ms.openlocfilehash: dfbf54c19aef00cbda886a4531797cda7ef3a191
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/04/2020
ms.locfileid: "76986101"
---
# <a name="az-module-support-in-azure-automation"></a>AZ-module ondersteuning in Azure Automation

Azure Automation ondersteunt de mogelijkheid om de [Azure Power shell AZ-module](/powershell/azure/new-azureps-module-az?view=azps-1.1.0) in uw runbooks te gebruiken. De AZ-module wordt niet automatisch geïmporteerd in nieuwe of bestaande Automation-accounts. In dit artikel wordt beschreven hoe u AZ-modules gebruikt met Azure Automation.

## <a name="considerations"></a>Overwegingen

Er zijn veel dingen waarmee u rekening moet houden bij het gebruik van de AZ-module in Azure Automation. Runbooks en modules kunnen worden gebruikt door oplossingen van een hoger niveau in uw Automation-account. Het bewerken van runbooks of het bijwerken van modules kan leiden tot problemen met runbooks. U moet alle runbooks en oplossingen zorgvuldig testen in een afzonderlijk Automation-account voordat u de nieuwe `Az`-modules importeert. Wijzigingen in modules kunnen een negatieve invloed hebben op de oplossing voor [starten/stoppen](automation-solution-vm-management.md) . Het is niet raadzaam om modules en runbooks te wijzigen in Automation-accounts die oplossingen bevatten. Dit gedrag is niet specifiek voor de AZ-modules. Dit gedrag moet worden meegenomen bij het introduceren van wijzigingen aan uw Automation-account.

Bij het importeren van een `Az` module in uw Automation-account wordt de module niet automatisch geïmporteerd in de Power shell-sessie die door de runbooks wordt gebruikt. Modules worden in de volgende situaties geïmporteerd in de Power shell-sessie:

* Wanneer een cmdlet van een module wordt aangeroepen vanuit een runbook
* Wanneer een runbook het expliciet importeert met de cmdlet `Import-Module`
* Wanneer een andere module, afhankelijk van de module, wordt geïmporteerd in een Power shell-sessie

> [!IMPORTANT]
> Het is belang rijk om ervoor te zorgen dat runbooks in een Automation-account alleen `Az`-of `AzureRM` modules importeren in de Power shell-sessies die worden gebruikt door runbooks en niet beide. Als `Az` wordt geïmporteerd voordat `AzureRM` in een runbook, wordt het runbook voltooid, maar wordt er een fout weer gegeven [die verwijst naar de get_SerializationSettings-methode](troubleshoot/runbooks.md#get-serializationsettings) in de taak stromen en-cmdlets mogelijk niet correct uitgevoerd. Als u `AzureRM` importeert en vervolgens `Az` uw runbook nog steeds is voltooid, wordt er een fout weer gegeven in de taak stromen met de mede deling dat zowel `Az` als `AzureRM` niet in dezelfde sessie kunnen worden geïmporteerd of in hetzelfde runbook worden gebruikt.

## <a name="migrating-to-az-modules"></a>Migreren naar AZ-modules

Het is raadzaam om de migratie te testen naar het gebruik van AZ-modules in plaats van AzureRM-modules in een test Automation-account. Zodra het Automation-account is gemaakt, kunt u de volgende stappen uitvoeren om ervoor te zorgen dat de migratie probleemloos verloopt:

### <a name="stop-and-unschedule-all-runbook-that-uses-azurerm-modules"></a>Alle runbook die gebruikmaakt van AzureRM-modules stoppen en de planning ervan opheffen

Om ervoor te zorgen dat u geen bestaande runbooks uitvoert die gebruikmaken van `AzureRM`-cmdlets, moet u alle runbooks die gebruikmaken van `AzureRM` modules stoppen en de planning ervan opheffen. U kunt zien welke schema's bestaan en welke schema's moeten worden verwijderd door het volgende voor beeld uit te voeren:

  ```powershell-interactive
  Get-AzureRmAutomationSchedule -AutomationAccountName "<AutomationAccountName>" -ResourceGroupName "<ResourceGroupName>" | Remove-AzureRmAutomationSchedule -WhatIf
  ```

Het is belang rijk om elk schema afzonderlijk te controleren, zodat u er zeker van kunt zijn dat het in de toekomst opnieuw kan worden gepland voor uw runbooks, indien nodig.

### <a name="import-the-az-modules"></a>De AZ-modules importeren

Importeer alleen de AZ-modules die vereist zijn voor uw runbooks. Importeer de module Rollup `Az` niet, omdat deze alle `Az.*` modules bevat die moeten worden geïmporteerd. Deze richt lijnen zijn hetzelfde voor alle modules.

De [AZ. accounts](https://www.powershellgallery.com/packages/Az.Accounts/1.1.0) -module is een afhankelijkheid voor de andere `Az.*`-modules. Daarom moet deze module worden geïmporteerd in uw Automation-account voordat u andere modules kunt importeren.

Selecteer in uw Automation-account **modules** onder **gedeelde bronnen**. Klik op **Bladeren galerie** om de pagina **Blader galerie** te openen.  Voer in de zoek balk de module naam in (bijvoorbeeld `Az.Accounts`). Klik op de pagina Power shell-module op **importeren** om de module te importeren in uw Automation-account.

![Modules importeren uit het Automation-account](media/az-modules/import-module.png)

Dit import proces kan ook worden uitgevoerd via de [PowerShell Gallery](https://www.powershellgallery.com) door te zoeken naar de module. Wanneer u de module hebt gevonden, selecteert u deze en klikt u op het tabblad **Azure Automation** op **implementeren in azure Automation**.

![Modules rechtstreeks vanuit de galerie importeren](media/az-modules/import-gallery.png)

## <a name="test-your-runbooks"></a>Uw runbooks testen

Zodra de `Az`-modules in uw Automation-account zijn geïmporteerd, kunt u nu beginnen met het bewerken van uw runbooks om in plaats daarvan de AZ-module te gebruiken. De meeste cmdlets hebben dezelfde naam, met uitzonde ring van `AzureRM` is gewijzigd in `Az`. Zie [lijst met uitzonde ringen](/powershell/azure/migrate-from-azurerm-to-az#update-cmdlets-modules-and-parameters)voor een lijst met modules die dit proces niet volgen.

Een manier om uw runbooks te testen voordat u uw runbook wijzigt voor het gebruik van de nieuwe cmdlets, is door `Enable-AzureRMAlias -Scope Process` aan het begin van een runbook te gebruiken. Door dit toe te voegen aan uw runbook, kan uw runbook zonder wijzigingen worden uitgevoerd.

## <a name="after-migration-details"></a>Na migratie Details

Nadat de migratie is voltooid, start u runbooks niet meer met `AzureRM` modules op het account. Het is ook raadzaam om `AzureRM`-modules voor dit account niet te importeren of bij te werken. Bekijk vanaf dit moment dat dit account is gemigreerd naar `Az`en alleen `Az` modules. Wanneer er een nieuw Automation-account wordt gemaakt, worden de bestaande `AzureRM` modules nog steeds geïnstalleerd en de zelf studie runbooks worden nog steeds gemaakt met `AzureRM`-cmdlets. Deze runbooks mogen niet worden uitgevoerd.

## <a name="next-steps"></a>Volgende stappen

Zie aan de slag [met AZ module](/powershell/azure/get-started-azureps?view=azps-1.1.0)voor meer informatie over het gebruik van AZ-modules.
