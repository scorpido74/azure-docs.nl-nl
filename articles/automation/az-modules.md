---
title: Az module support in Azure Automation (Ondersteuning voor Az-module in Azure Automation)
description: In dit artikel vindt u informatie over het gebruik van Az-modules in Azure Automation.
services: automation
ms.subservice: shared-capabilities
ms.date: 02/08/2019
ms.topic: conceptual
ms.openlocfilehash: 21fa1c4faa4a080b9b495e1481fdadcd7e8bea10
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619481"
---
# <a name="az-module-support-in-azure-automation"></a>Az module support in Azure Automation (Ondersteuning voor Az-module in Azure Automation)

Azure Automation ondersteunt het gebruik van [azure PowerShell Az-module](/powershell/azure/new-azureps-module-az?view=azps-1.1.0) in uw runbooks. De rollup Az-module wordt niet automatisch geïmporteerd in nieuwe of bestaande automatiseringsaccounts. 

## <a name="considerations"></a>Overwegingen

Er zijn veel dingen om rekening mee te houden bij het gebruik van de rollup Az-module in Azure Automation. Runbooks en modules kunnen worden gebruikt door oplossingen op een hoger niveau in uw Automation-account. Het bewerken van runbooks of het upgraden van modules kan mogelijk problemen veroorzaken met uw runbooks. U moet alle runbooks en oplossingen zorgvuldig testen in een apart Automation-account voordat u nieuwe Az-modules importeert. Wijzigingen aan modules kunnen een negatieve invloed hebben op de [Start/Stop-oplossing.](automation-solution-vm-management.md) We raden u aan modules en runbooks te wijzigen in automatiseringsaccounts die oplossingen bevatten. Dit gedrag is niet specifiek voor de Az-modules. Hiermee moet rekening worden gehouden bij het invoeren van wijzigingen in uw automatiseringsaccount.

Als u een Az-module importeert in uw Automatiseringsaccount, wordt de module niet automatisch geïmporteerd in de PowerShell-sessie die de runbooks gebruiken. Modules worden in de volgende situaties in de PowerShell-sessie geïmporteerd:

* Wanneer een runbook een cmdlet uit een module aanroept
* Wanneer een runbook de module `Import-Module` expliciet importeert met de cmdlet
* Wanneer een runbook een andere module importeert, afhankelijk van de module

> [!IMPORTANT]
> Zorg ervoor dat runbooks in een Automatiseringsaccount Az-modules of [AzureRM-modules](https://www.powershellgallery.com/packages/AzureRM/6.13.1) importeren, maar niet beide, in een PowerShell-sessie. Als een runbook Az-modules importeert vóór AzureRM-modules, wordt het runbook voltooid. Er wordt echter een fout weergegeven die verwijst naar de [Get_SerializationSettings](troubleshoot/runbooks.md#get-serializationsettings) cmdlet in de taakstromen en cmdlets mogelijk niet goed worden uitgevoerd. Als de runbook AzureRM-modules importeert vóór Az-modules, wordt het runbook ook voltooid. In dit geval ontvangt u echter een fout in de taakstromen waarin staat dat zowel Az als AzureRM niet in dezelfde sessie kunnen worden geïmporteerd of in hetzelfde runbook kunnen worden gebruikt.

## <a name="migrating-to-az-modules"></a>Migreren naar Az-modules

Het wordt aanbevolen om een migratie naar AZ-modules te testen in een testautomation-account. Zodra u dit account hebt gemaakt, u de instructies in deze sectie gebruiken om met de modules te werken.

### <a name="stop-and-unschedule-all-runbooks-that-use-azurerm-modules"></a>Alle runbooks met AzureRM-modules stoppen en uitplannen en uitplannen

Als u ervoor wilt zorgen dat u geen bestaande runbooks uitvoert die AzureRM-modules gebruiken, stopt en maakt u alle betrokken runbooks niet meer. U zien welke schema's er bestaan en welke schema's moeten worden verwijderd door code uit te voeren die vergelijkbaar is met dit voorbeeld:

  ```powershell-interactive
  Get-AzureRmAutomationSchedule -AutomationAccountName "<AutomationAccountName>" -ResourceGroupName "<ResourceGroupName>" | Remove-AzureRmAutomationSchedule -WhatIf
  ```

Het is belangrijk om elk schema afzonderlijk te bekijken om ervoor te zorgen dat u het in de toekomst opnieuw plannen voor uw runbooks, indien nodig.

### <a name="import-the-az-modules"></a>De Az-modules importeren

>[!NOTE]
>Laat uw runbooks alleen vereiste Az-modules importeren. Importeer de rollup Az-module niet, want deze bevat alle Az-modules. Deze richtlijnen zijn voor alle modules hetzelfde.

De module [Az.Accounts](https://www.powershellgallery.com/packages/Az.Accounts/1.1.0) is een afhankelijkheid voor de andere Az-modules. Daarom moeten uw runbooks deze module importeren in uw Automatiseringsaccount voordat u andere modules importeert.

Ga als bedoeld als u de modules in de Azure-portal wilt importeren:

1. Selecteer **modules** onder Gedeelde **bronnen**in uw automatiseringsaccount. 
2. Klik **op Galerie bladeren** om de pagina Bladeren door de galerij te openen.  
3. Voer in de zoekbalk bijvoorbeeld de `Az.Accounts`modulenaam in. 
4. Klik op de pagina PowerShell-module op **Importeren** om de module te importeren in uw automatiseringsaccount.

![Modules importeren uit automatiseringsaccount](media/az-modules/import-module.png)

Dit importproces kan ook via de [PowerShell Gallery](https://www.powershellgallery.com) worden uitgevoerd door te zoeken naar de module voor import. Zodra u de module hebt gevonden, selecteert u deze, kiest u het tabblad **Azure Automation** en klikt u op **Implementeren voor Azure Automation**.

![Modules rechtstreeks importeren vanuit galerie](media/az-modules/import-gallery.png)

## <a name="testing-your-runbooks"></a>Uw runbooks testen

Zodra u de Az-modules hebt geïmporteerd in het Automatiseringsaccount, u beginnen met het bewerken van uw runbooks om de modules te gebruiken. De meeste cmdlets hebben dezelfde namen als voor de AzureRM-module, behalve dat het AzureRM-voorvoegsel (of AzureRm) is gewijzigd in Az. Zie lijst met [uitzonderingen](/powershell/azure/migrate-from-azurerm-to-az#update-cmdlets-modules-and-parameters)voor een lijst met modules die deze naamgevingsconventie niet volgen.

Een manier om de wijziging van een runbook te testen `Enable-AzureRmAlias -Scope Process` om de nieuwe cmdlets te gebruiken is door gebruik te maken van aan het begin van het runbook. Door deze opdracht toe te voegen aan uw runbook, kan het script zonder wijzigingen worden uitgevoerd.

## <a name="after-migration-details"></a>Details na migratie

Nadat de migratie is voltooid, probeert u geen runbooks te starten met AzureRM-modules op het automatiseringsaccount. Het wordt ook aanbevolen om AzureRM-modules in het account niet te importeren of bij te werken. Overweeg het account gemigreerd naar Az, en werken met Az modules alleen. 

Wanneer u een nieuw Automatiseringsaccount maakt, worden de bestaande AzureRM-modules nog steeds geïnstalleerd. U de runbooks van de zelfstudie nog steeds bijwerken met AzureRM-cmdlets. U moet deze runbooks echter niet uitvoeren.

## <a name="next-steps"></a>Volgende stappen

Zie [Aan de slag met az-module](/powershell/azure/get-started-azureps?view=azps-1.1.0)voor meer informatie over het gebruik van Az-modules.
