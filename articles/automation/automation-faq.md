---
title: Veelgestelde vragen over Azure Automation | Microsoft Documenten
description: Antwoorden op veelgestelde vragen over Azure Automation.
services: automation
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 02/25/2020
ms.openlocfilehash: 3fa29f3df5f0434c4c61e8d12adbb3f55156a29f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81405965"
---
# <a name="azure-automation-frequently-asked-questions"></a>Veelgestelde vragen over Azure Automation

Deze veelgestelde vragen van Microsoft is een lijst met veelgestelde vragen over Azure Automation. Als u aanvullende vragen over de mogelijkheden, ga naar de discussie forum en post uw vragen. Wanneer een vraag vaak wordt gesteld, voegen we deze toe aan dit artikel, zodat deze snel en gemakkelijk kan worden gevonden.

>[!NOTE]
>Dit artikel is bijgewerkt voor het gebruik van de nieuwe Azure PowerShell Az-module. De AzureRM-module kan nog worden gebruikt en krijgt bugoplossingen tot ten minste december 2020. Zie voor meer informatie over de nieuwe Az-module en compatibiliteit met AzureRM [Introductie van de nieuwe Az-module van Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Zie [De Azure PowerShell-module installeren](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)voor installatie-instructies voor az-modules op uw hybride runbookworker. Voor uw Automatiseringsaccount u uw modules bijwerken naar de nieuwste versie met [Azure PowerShell-modules bijwerken in Azure Automation.](automation-update-azure-modules.md)

## <a name="update-management-solution"></a>Oplossing voor updatebeheer

### <a name="can-i-prevent-unexpected-os-level-upgrades"></a>Kan ik onverwachte upgrades op OS-niveau voorkomen?

Op sommige Linux-varianten, zoals Red Hat Enterprise Linux, kunnen upgrades op OS-niveau plaatsvinden via pakketten. Dit kan leiden tot updatebeheerruns waarbij het versienummer van het besturingssysteem verandert. Omdat Updatemanagement dezelfde methoden gebruikt om pakketten bij te werken die een beheerder lokaal op een Linux-machine gebruikt, is dit gedrag opzettelijk.

Gebruik de functie **Uitsluiting** om te voorkomen dat de versie van het besturingssysteem wordt bijgewerkt via implementaties van Updatebeheer.

In Red Hat Enterprise Linux, het `redhat-release-server.x86_64`pakket naam uit te sluiten is .

### <a name="why-arent-criticalsecurity-updates-applied"></a>Waarom worden er geen kritieke/beveiligingsupdates toegepast?

Wanneer u updates implementeert voor een Linux-machine, u updateclassificaties selecteren. Met deze optie worden de updates gefilterd die aan de opgegeven criteria voldoen. Dit filter wordt lokaal op de machine toegepast wanneer de update wordt geïmplementeerd.

Omdat UpdateManagement updateverrijking uitvoert in de cloud, u sommige updates in Updatebeheer markeren als een beveiligingseffect, ook al beschikt de lokale machine niet over die informatie. Als u kritieke updates toepast op een Linux-machine, zijn er mogelijk updates die niet zijn gemarkeerd als een beveiligingseffect op die machine en daarom niet worden toegepast. Updatebeheer kan deze machine echter nog steeds melden als niet-compatibel omdat deze aanvullende informatie over de relevante update bevat.

Het implementeren van updates door updateclassificatie werkt niet op RTM-versies van CentOS. Als u updates voor CentOS correct wilt implementeren, selecteert u alle classificaties om ervoor te zorgen dat updates worden toegepast. Voor SUSE kan het selecteren van ONLY **Other updates** als classificatie ertoe leiden dat er eerst extra beveiligingsupdates worden geïnstalleerd als beveiligingsupdates met betrekking tot zypper (package manager) of de afhankelijkheden ervan eerst vereist zijn. Dit gedrag is een beperking van zypper. In sommige gevallen moet u de update-implementatie opnieuw uitvoeren en vervolgens de implementatie verifiëren via het updatelogboek.

### <a name="can-i-deploy-updates-across-azure-tenants"></a>Kan ik updates implementeren voor Azure-tenants?

Als u machines hebt die moeten worden gepatcht in een andere Azure-tenant die rapporteert aan Updatebeheer, moet u een volgende tijdelijke oplossing gebruiken om ze in te stellen. U de cmdlet [Nieuw-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationSchedule?view=azps-3.7.0) gebruiken met de `ForUpdateConfiguration` opgegeven parameter om een planning te maken. U de cmdlet [Nieuw-AzAutomationSoftwareUpdateConfiguration](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationSoftwareUpdateConfiguration?view=azps-3.7.0) gebruiken en de `NonAzureComputer` machines in de andere tenant doorgeven aan de parameter. In het volgende voorbeeld ziet u hoe u dit doet.

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$sched = New-AzAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdateConfiguration

New-AzAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName <automationAccountName> -Schedule $sched -Windows -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="next-steps"></a>Volgende stappen

Als uw vraag hier niet wordt beantwoord, u de volgende bronnen voor aanvullende vragen en antwoorden verwijzen.

- [Azure Automation](https://social.msdn.microsoft.com/Forums/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc)
- [Feedbackforum](https://feedback.azure.com/forums/905242-update-management)
