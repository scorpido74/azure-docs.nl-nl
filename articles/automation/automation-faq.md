---
title: Veelgestelde vragen over Azure Automation | Microsoft Documenten
description: Antwoorden op veelgestelde vragen over Azure Automation.
services: automation
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 02/25/2020
ms.openlocfilehash: 129a5316c2e7be329b479c79706791e993d20b74
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77925811"
---
# <a name="azure-automation-frequently-asked-questions"></a>Veelgestelde vragen over Azure Automation

Deze veelgestelde vragen van Microsoft is een lijst met veelgestelde vragen over Azure Automation. Als u aanvullende vragen over de mogelijkheden, ga naar de discussie forum en post uw vragen. Wanneer een vraag vaak wordt gesteld, voegen we deze toe aan dit artikel, zodat deze snel en gemakkelijk kan worden gevonden.

## <a name="update-management-solution"></a>Oplossing voor updatebeheer

### <a name="can-i-prevent-unexpected-os-level-upgrades"></a>Kan ik onverwachte upgrades op OS-niveau voorkomen?

Op sommige Linux-varianten, zoals Red Hat Enterprise Linux, kunnen upgrades op OS-niveau plaatsvinden via pakketten. Dit kan leiden tot updatebeheer waar het besturingssysteemnummer verandert. Omdat Update Management dezelfde methoden gebruikt om pakketten bij te werken die een beheerder lokaal op de Linux-machine zou gebruiken, is dit gedrag opzettelijk.

Gebruik de functie **Uitsluiting** om te voorkomen dat de versie van het besturingssysteem wordt bijgewerkt via implementaties van Updatebeheer.

In Red Hat Enterprise Linux is de pakketnaam die u wilt uitsluiten redhat-release-server.x86_64.

### <a name="why-arent-criticalsecurity-updates-applied"></a>Waarom worden er geen kritieke/beveiligingsupdates toegepast?

Wanneer u updates implementeert voor een Linux-machine, u updateclassificaties selecteren. Met deze optie worden de updates die op de machine worden toegepast, gefilterd die aan de opgegeven criteria voldoen. Dit filter wordt lokaal op de machine toegepast wanneer de update wordt geïmplementeerd.

Omdat Update Management updateverrijking uitvoert in de cloud, kunnen sommige updates worden gemarkeerd in Update Beheer als een beveiligingseffect, ook al heeft de lokale machine die informatie niet. Als u kritieke updates toepast op een Linux-machine, kunnen er updates zijn die niet zijn gemarkeerd als een beveiligingseffect op die machine en daarom worden de updates niet toegepast. Updatebeheer kan deze machine echter nog steeds melden als niet-compatibel omdat deze aanvullende informatie over de relevante update bevat.

Het implementeren van updates door updateclassificatie werkt niet op RTM-versies van CentOS. Als u updates voor CentOS correct wilt implementeren, selecteert u alle classificaties om ervoor te zorgen dat updates worden toegepast. Voor SUSE kan het selecteren *van alleen* **andere updates** als classificatie ertoe leiden dat sommige beveiligingsupdates ook worden geïnstalleerd als beveiligingsupdates met betrekking tot zypper (package manager) of de afhankelijkheden ervan eerst vereist zijn. Dit gedrag is een beperking van zypper. In sommige gevallen moet u de update-implementatie opnieuw uitvoeren. Als u het updatelogboek wilt verifiëren, controleert u het updatelogboek.

### <a name="can-i-deploy-updates-across-azure-tenants"></a>Kan ik updates implementeren voor Azure-tenants?

Als u machines in een andere Azure-tenant hebt die rapporteren aan Updatebeheer die u moet patchen, moet u de volgende tijdelijke oplossing gebruiken om ze te laten in-erden. U de cmdlet [Nieuw-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) met de `-ForUpdate` switch gebruiken om een planning te maken en de cmdlet [Nieuw-AzureRmAutomationSoftwareUpdateConfiguration](/powershell/module/azurerm.automation/new-azurermautomationsoftwareupdateconfiguration
) gebruiken en de machines in de andere tenant doorgeven aan de `-NonAzureComputer` parameter. In het volgende voorbeeld ziet u hoe u dit doet:

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$sched = New-AzureRmAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdate

New-AzureRmAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName <automationAccountName> -Schedule $sched -Windows -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="next-steps"></a>Volgende stappen

Als uw vraag hier niet wordt beantwoord, u verwijzen naar het volgende forum voor aanvullende vragen en antwoorden.

- [Azure Automation](https://social.msdn.microsoft.com/Forums/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc)

Ga naar het [feedbackforum](https://feedback.azure.com/forums/905242-update-management)voor algemene feedback over de oplossing voor het beheer van updates.