---
title: Azure Automation Veelgestelde vragen | Microsoft Docs
description: In dit artikel vindt u antwoorden op veelgestelde vragen over Azure Automation.
services: automation
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 02/25/2020
ms.openlocfilehash: 76c8d09ef2ef0130ddac856a1f37f8b68d977494
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/09/2020
ms.locfileid: "86186228"
---
# <a name="azure-automation-frequently-asked-questions"></a>Veelgestelde vragen over Azure Automation

Deze veelgestelde vragen over micro soft vindt u een lijst met veel gestelde antwoorden over Azure Automation. Als u aanvullende vragen over de mogelijkheden hebt, gaat u naar het discussie forum en plaatst u uw vragen. Wanneer een vraag regel matig wordt gesteld, voegen we deze toe aan dit artikel zodat het snel en eenvoudig kan worden gevonden.

## <a name="update-management"></a>Updatebeheer

### <a name="can-i-prevent-unexpected-os-level-upgrades"></a>Kan ik onverwachte upgrades op besturingssysteem niveau voor komen?

Bij sommige Linux-varianten, zoals Red Hat Enterprise Linux, kunnen upgrades op besturingssysteem niveau worden uitgevoerd via pakketten. Dit kan leiden tot Updatebeheer uitvoeringen waarin het versie nummer van het besturings systeem wordt gewijzigd. Omdat Updatebeheer dezelfde methoden gebruikt voor het bijwerken van pakketten die een beheerder lokaal op een Linux-computer gebruikt, is dit gedrag opzettelijk.

Gebruik de functie **uitsluiting** om te voor komen dat u de versie van het besturings systeem bijwerkt via updatebeheer-implementaties.

In Red Hat Enterprise Linux is de naam van het pakket die moet worden uitgesloten `redhat-release-server.x86_64` .

### <a name="why-arent-criticalsecurity-updates-applied"></a>Waarom worden essentiële/beveiligings updates niet toegepast?

Wanneer u updates op een Linux-machine implementeert, kunt u update classificaties selecteren. Met deze optie worden de updates die voldoen aan de opgegeven criteria, gefilterd. Dit filter wordt lokaal op de computer toegepast wanneer de update wordt geïmplementeerd.

Omdat Updatebeheer verrijking update uitvoert in de Cloud, kunt u sommige updates in Updatebeheer markeren als gevolg van een beveiligings effect, zelfs als de lokale computer deze informatie niet bevat. Als u essentiële updates toepast op een Linux-computer, zijn er mogelijk updates die niet zijn gemarkeerd als een beveiligings effect op die computer en daarom niet worden toegepast. Updatebeheer kan de computer echter toch als niet-compatibel rapporteren omdat deze aanvullende informatie over de relevante update heeft.

Het implementeren van updates op update classificatie werkt niet in de RTM-versies van CentOS. Als u updates voor CentOS correct wilt implementeren, selecteert u alle classificaties om er zeker van te zijn dat er updates worden toegepast. Voor SUSE selecteert u alleen **andere updates** als de classificatie kan ertoe leiden dat er extra beveiligings updates worden geïnstalleerd als de beveiligings updates met betrekking tot Zypper (pakket beheer) of de afhankelijkheden hiervan eerst zijn vereist. Dit gedrag is een beperking van Zypper. In sommige gevallen kan het nodig zijn om de update-implementatie opnieuw uit te voeren en de implementatie te controleren via het Update logboek.

### <a name="can-i-deploy-updates-across-azure-tenants"></a>Kan ik updates implementeren in azure-tenants?

Als u computers hebt die patches in een andere Azure-Tenant rapportage nodig hebben om Updatebeheer, moet u een volgende tijdelijke oplossing gebruiken om ze te laten plannen. U kunt de cmdlet [New-AzAutomationSchedule](/powershell/module/Az.Automation/New-AzAutomationSchedule?view=azps-3.7.0) gebruiken met de `ForUpdateConfiguration` para meter die is opgegeven voor het maken van een schema. U kunt de cmdlet [New-AzAutomationSoftwareUpdateConfiguration](/powershell/module/Az.Automation/New-AzAutomationSoftwareUpdateConfiguration?view=azps-3.7.0) gebruiken en de computers in de andere Tenant door geven aan de `NonAzureComputer` para meter. In het volgende voor beeld ziet u hoe u dit doet.

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$sched = New-AzAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdateConfiguration

New-AzAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName <automationAccountName> -Schedule $sched -Windows -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="next-steps"></a>Volgende stappen

Als uw vraag hier niet wordt beantwoord, kunt u de volgende bronnen raadplegen voor aanvullende vragen en antwoorden.

- [Azure Automation](/answers/topics/azure-automation.html)
- [Feedbackforum](https://feedback.azure.com/forums/905242-update-management)
