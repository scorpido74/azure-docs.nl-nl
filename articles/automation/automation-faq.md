---
title: Azure Automation Veelgestelde vragen | Microsoft Docs
description: Antwoorden op veelgestelde vragen over Azure Automation.
services: automation
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 02/25/2020
ms.openlocfilehash: 129a5316c2e7be329b479c79706791e993d20b74
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/28/2020
ms.locfileid: "77925811"
---
# <a name="azure-automation-frequently-asked-questions"></a>Veelgestelde vragen over Azure Automation

Deze veelgestelde vragen over micro soft vindt u een lijst met veel gestelde antwoorden over Azure Automation. Als u aanvullende vragen over de mogelijkheden hebt, gaat u naar het discussie forum en plaatst u uw vragen. Wanneer u een vraag is vaak wordt gevraagd, toevoegen we deze aan dit artikel zodat snel en eenvoudig kunnen worden gevonden.

## <a name="update-management-solution"></a>Updatebeheer oplossing

### <a name="can-i-prevent-unexpected-os-level-upgrades"></a>Kan ik onverwachte upgrades op besturingssysteem niveau voor komen?

Bij sommige Linux-varianten, zoals Red Hat Enterprise Linux, kunnen upgrades op besturingssysteem niveau worden uitgevoerd via pakketten. Dit kan leiden tot Updatebeheer worden uitgevoerd, waarbij het versie nummer van het besturings systeem verandert. Omdat Updatebeheer dezelfde methoden gebruikt voor het bijwerken van pakketten die een beheerder lokaal zou gebruiken op de Linux-computer, is dit gedrag opzettelijk.

Gebruik de functie **uitsluiting** om te voor komen dat u de versie van het besturings systeem bijwerkt via updatebeheer-implementaties.

In Red Hat Enterprise Linux is de pakket naam die moet worden uitgesloten, RedHat-release-server. x86_64.

### <a name="why-arent-criticalsecurity-updates-applied"></a>Waarom worden essentiële/beveiligings updates niet toegepast?

Wanneer u updates op een Linux-machine implementeert, kunt u update classificaties selecteren. Met deze optie worden de updates die op de computer worden toegepast, gefilterd die voldoen aan de opgegeven criteria. Dit filter wordt lokaal op de computer toegepast wanneer de update wordt geïmplementeerd.

Omdat Updatebeheer verrijking update uitvoert in de Cloud, kunnen sommige updates worden gemarkeerd in Updatebeheer als gevolg van een beveiligings effect, zelfs als de lokale computer deze informatie niet bevat. Als u essentiële updates toepast op een Linux-computer, zijn er mogelijk updates die niet zijn gemarkeerd als een beveiligings effect op die computer en daarom worden de updates niet toegepast. Updatebeheer kan de computer echter toch als niet-compatibel melden, omdat deze aanvullende informatie over de relevante update heeft.

Het implementeren van updates op update classificatie werkt niet in de RTM-versies van CentOS. Als u updates voor CentOS correct wilt implementeren, selecteert u alle classificaties om er zeker van te zijn dat er updates worden toegepast. Voor SUSE selecteert u *alleen* **andere updates** als de classificatie kan ertoe leiden dat bepaalde beveiligings updates ook worden geïnstalleerd als de beveiligings updates met betrekking tot Zypper (pakket beheer) of de afhankelijkheden hiervan eerst zijn vereist. Dit gedrag is een beperking van Zypper. In sommige gevallen kan het nodig zijn om de update-implementatie opnieuw uit te voeren. Controleer het Update logboek om dit te controleren.

### <a name="can-i-deploy-updates-across-azure-tenants"></a>Kan ik updates implementeren in azure-tenants?

Als u computers in een andere Azure-Tenant rapporteert om Updatebeheer die u moet patchen, moet u de volgende tijdelijke oplossing gebruiken om ze te laten plannen. U kunt de cmdlet [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) met de `-ForUpdate` switch gebruiken om een schema te maken en de cmdlet [New-AzureRmAutomationSoftwareUpdateConfiguration](/powershell/module/azurerm.automation/new-azurermautomationsoftwareupdateconfiguration
) te gebruiken en de computers in de andere Tenant door te geven aan de `-NonAzureComputer`-para meter. In het volgende voor beeld ziet u hoe u dit doet:

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$sched = New-AzureRmAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdate

New-AzureRmAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName <automationAccountName> -Schedule $sched -Windows -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="next-steps"></a>Volgende stappen

Als uw vraag hier niet wordt beantwoord, kunt u het volgende forum raadplegen voor aanvullende vragen en antwoorden.

- [Azure Automation](https://social.msdn.microsoft.com/Forums/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc)

Ga voor algemene feedback over de Updatebeheer-oplossing naar het [Feedback forum](https://feedback.azure.com/forums/905242-update-management).