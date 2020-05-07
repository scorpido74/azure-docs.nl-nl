---
title: Problemen oplossen met het starten/stoppen van Vm's buiten kantoor uren
description: In dit artikel vindt u informatie over het oplossen van problemen met het starten/stoppen van de virtuele machine tijdens een daluren oplossing.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 04/04/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 611e8441fab56114ca010d0b555c9ed156ae9d40
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/06/2020
ms.locfileid: "82855057"
---
# <a name="troubleshoot-the-startstop-vms-during-off-hours-solution"></a>Problemen oplossen met het starten/stoppen van Vm's buiten kantoor uren

In dit artikel vindt u informatie over het oplossen van problemen die zich voordoen wanneer u werkt met de Azure Automation Vm's starten/stoppen buiten kantoor uren.

>[!NOTE]
>Dit artikel is bijgewerkt voor het gebruik van de nieuwe Azure PowerShell Az-module. De AzureRM-module kan nog worden gebruikt en krijgt bugoplossingen tot ten minste december 2020. Zie voor meer informatie over de nieuwe Az-module en compatibiliteit met AzureRM [Introductie van de nieuwe Az-module van Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Zie [de module Azure PowerShell installeren](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)voor de installatie-instructies voor AZ module op uw Hybrid Runbook Worker. Voor uw Azure Automation-account kunt u uw modules bijwerken naar de nieuwste versie door gebruik te maken van de [Azure PowerShell-modules in azure Automation bij te werken](../automation-update-azure-modules.md).

## <a name="scenario-the-startstop-vms-during-off-hours-solution-fails-to-properly-deploy"></a><a name="deployment-failure"></a>Scenario: de implementatie van Vm's voor het starten/stoppen van de oplossing is mislukt

### <a name="issue"></a>Probleem

Wanneer u de oplossing voor het [starten/stoppen van vm's tijdens een buiten](../automation-solution-vm-management.md)-en-uur implementeert, wordt een van de volgende fouten weer gegeven:

```error
Account already exists in another resourcegroup in a subscription. ResourceGroupName: [MyResourceGroup].
```

```error
Resource 'StartStop_VM_Notification' was disallowed by policy. Policy identifiers: '[{\\\"policyAssignment\\\":{\\\"name\\\":\\\"[MyPolicyName]".
```

```error
The subscription is not registered to use namespace 'Microsoft.OperationsManagement'.
```

```error
The subscription is not registered to use namespace 'Microsoft.Insights'.
```

```error
The scope '/subscriptions/000000000000-0000-0000-0000-00000000/resourcegroups/<ResourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<WorkspaceName>/views/StartStopVMView' cannot perform write operation because following scope(s) are locked: '/subscriptions/000000000000-0000-0000-0000-00000000/resourceGroups/<ResourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<WorkspaceName>/views/StartStopVMView'. Please remove the lock and try again
```

```error
A parameter cannot be found that matches parameter name 'TagName'
```

```error
Start-AzureRmVm : Run Login-AzureRmAccount to login
```

### <a name="cause"></a>Oorzaak

Implementaties kunnen om een van de volgende redenen mislukken:

- Er is al een Automation-account met dezelfde naam in de geselecteerde regio.
- Met een beleid is de implementatie van de Vm's starten/stoppen buiten kantoor uren niet toegestaan.
- Het `Microsoft.OperationsManagement`resource `Microsoft.Insights`type, `Microsoft.Automation` of is niet geregistreerd.
- Uw Log Analytics-werk ruimte is vergrendeld.
- Er is een verouderde versie van de AzureRM-modules of de oplossing voor het starten/stoppen van Vm's buiten kantoor uren.

### <a name="resolution"></a>Oplossing

Bekijk de volgende oplossingen voor mogelijke oplossingen voor het probleem:

* Automation-accounts moeten uniek zijn binnen een Azure-regio, zelfs als ze zich in verschillende resource groepen bevinden. Controleer uw bestaande Automation-accounts in de doel regio.
* Een bestaand beleid voor komt dat een resource die is vereist voor de oplossing voor het starten/stoppen van Vm's tijdens een buiten-tijd implementatie, kan worden geïmplementeerd. Ga naar de beleids toewijzingen in de Azure Portal en controleer of u een beleids toewijzing hebt die de implementatie van deze resource niet toestaat. Zie [RequestDisallowedByPolicy-fout](../../azure-resource-manager/templates/error-policy-requestdisallowedbypolicy.md)voor meer informatie.
* Als u de oplossing voor het starten/stoppen van Vm's wilt implementeren, moet uw abonnement worden geregistreerd bij de volgende Azure-resource-naam ruimten:

    * `Microsoft.OperationsManagement`
    * `Microsoft.Insights`
    * `Microsoft.Automation`

   Zie [fouten voor de registratie van de resource provider oplossen](../../azure-resource-manager/templates/error-register-resource-provider.md)voor meer informatie over fouten bij het registreren van providers.
* Als u een vergren deling op uw Log Analytics-werk ruimte hebt, gaat u naar uw werk ruimte in de Azure Portal en verwijdert u de vergren delingen van de resource.
* Als u met deze oplossingen het probleem niet kunt oplossen, volgt u de instructies onder [Update de oplossing](../automation-solution-vm-management.md#update-the-solution) om de vm's voor het starten en stoppen van de virtuele machines te implementeren tijdens de oplossing buiten kantoor uren.

## <a name="scenario-all-vms-fail-to-start-or-stop"></a><a name="all-vms-fail-to-startstop"></a>Scenario: alle Vm's kunnen niet worden gestart of gestopt

### <a name="issue"></a>Probleem

U hebt de oplossing voor het starten/stoppen van Vm's geconfigureerd tijdens een offline-tijd, maar de virtuele machines worden niet gestart of gestopt.

### <a name="cause"></a>Oorzaak

Deze fout kan een van de volgende oorzaken hebben:

- Een planning is niet correct geconfigureerd.
- Het run as-account is mogelijk niet juist geconfigureerd.
- Een runbook kan worden uitgevoerd als er fouten zijn opgetreden.
- De Vm's zijn mogelijk uitgesloten.

### <a name="resolution"></a>Oplossing

Bekijk de volgende lijst voor mogelijke oplossingen voor het probleem:

* Controleer of u een planning voor de oplossing voor het starten/stoppen van Vm's hebt geconfigureerd tijdens de offline-tijd. Zie [schema's](../automation-schedules.md)voor meer informatie over het configureren van een schema.

* Controleer de [taak stromen](../automation-runbook-execution.md#job-statuses) om te controleren of er fouten zijn opgetreden. Zoek naar taken uit een van de volgende runbooks:

  * **AutoStop_CreateAlert_Child**
  * **AutoStop_CreateAlert_Parent**
  * **AutoStop_Disable**
  * **AutoStop_VM_Child**
  * **ScheduledStartStop_Base_Classic**
  * **ScheduledStartStop_Child_Classic**
  * **ScheduledStartStop_Child**
  * **ScheduledStartStop_Parent**
  * **SequencedStartStop_Parent**

* Controleer of het [uitvoeren als-account](../manage-runas-account.md) de juiste machtigingen heeft voor de vm's die u probeert te starten of te stoppen. Zie voor meer informatie over het controleren van de machtigingen voor een resource [Quick Start: rollen weer geven die zijn toegewezen aan een gebruiker met behulp van de Azure Portal](../../role-based-access-control/check-access.md). U moet de toepassings-ID opgeven voor de service-principal die wordt gebruikt door het run as-account. U kunt deze waarde ophalen door naar uw Automation-account te gaan in de Azure Portal. Selecteer **uitvoeren als-accounts** onder **account instellingen**en selecteer het juiste uitvoeren als-account.

* Vm's kunnen niet worden gestart of gestopt als ze expliciet worden uitgesloten. Uitgesloten Vm's worden ingesteld in de `External_ExcludeVMNames` variabele in het Automation-account waarop de oplossing is geïmplementeerd. In het volgende voor beeld ziet u hoe u een query kunt uitvoeren op die waarde met Power shell.

  ```powershell-interactive
  Get-AzAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

## <a name="scenario-some-of-my-vms-fail-to-start-or-stop"></a><a name="some-vms-fail-to-startstop"></a>Scenario: sommige Vm's kunnen niet worden gestart of gestopt

### <a name="issue"></a>Probleem

U hebt de oplossing voor het starten/stoppen van Vm's geconfigureerd tijdens een offline-tijd, maar er worden geen enkele geconfigureerde Vm's gestart of gestopt.

### <a name="cause"></a>Oorzaak

Deze fout kan een van de volgende oorzaken hebben:

- In het Sequence-scenario is het mogelijk dat een tag ontbreekt of onjuist is.
- De virtuele machine is mogelijk uitgesloten.
- Het uitvoeren als-account heeft mogelijk onvoldoende machtigingen voor de virtuele machine.
- De virtuele machine kan een probleem hebben waardoor het starten of stoppen van de VM is gestopt.

### <a name="resolution"></a>Oplossing

Raadpleeg de volgende lijst voor mogelijke oplossingen voor uw probleem of voor het zoeken naar:

* Wanneer u het [volgorde scenario](../automation-solution-vm-management.md) van de oplossing vm's starten/stoppen gebruikt, moet u ervoor zorgen dat elke VM die u wilt starten of stoppen de juiste tag heeft. Zorg ervoor dat de virtuele machines die u wilt starten, `sequencestart` de tag hebben en de vm's die u wilt stoppen `sequencestop` , de tag hebben. Voor beide tags is een positief geheel getal vereist. U kunt een query gebruiken die vergelijkbaar is met het volgende voor beeld om te zoeken naar alle virtuele machines met de labels en hun waarden.

  ```powershell-interactive
  Get-AzResource | ? {$_.Tags.Keys -contains "SequenceStart" -or $_.Tags.Keys -contains "SequenceStop"} | ft Name,Tags
  ```

* Vm's kunnen niet worden gestart of gestopt als ze expliciet worden uitgesloten. Uitgesloten Vm's worden ingesteld in de `External_ExcludeVMNames` variabele in het Automation-account waarop de oplossing is geïmplementeerd. In het volgende voor beeld ziet u hoe u een query kunt uitvoeren op die waarde met Power shell.

  ```powershell-interactive
  Get-AzAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

* Voor het starten en stoppen van Vm's moet het uitvoeren als-account voor het Automation-account de juiste machtigingen hebben voor de virtuele machine. Zie voor meer informatie over het controleren van de machtigingen voor een resource [Quick Start: rollen weer geven die zijn toegewezen aan een gebruiker met behulp van de Azure Portal](../../role-based-access-control/check-access.md). U moet de toepassings-ID opgeven voor de service-principal die wordt gebruikt door het run as-account. U kunt deze waarde ophalen door naar uw Automation-account te gaan in de Azure Portal. Selecteer **uitvoeren als-accounts** onder **account instellingen** en selecteer het juiste uitvoeren als-account.
* Als de virtuele machine een probleem ondervindt bij het starten of ongedaan maken van de toewijzing, is er mogelijk een probleem op de VM zelf. Voor beelden zijn een update die wordt toegepast wanneer de virtuele machine wordt afgesloten, een service die vastloopt en nog veel meer. Ga naar de VM-resource en controleer de **activiteiten logboeken** om te zien of er fouten in de logboeken zijn. U kunt ook proberen u aan te melden bij de virtuele machine om te zien of er fouten zijn in de gebeurtenis Logboeken. Zie [problemen met virtuele Azure-machines oplossen](../../virtual-machines/troubleshooting/index.yml)voor meer informatie over het oplossen van problemen met uw VM.
* Controleer de [taak stromen](../automation-runbook-execution.md#job-statuses) om te controleren of er fouten zijn opgetreden. Ga in de portal naar uw Automation-account en selecteer **taken** onder **proces automatisering**.

## <a name="scenario-my-custom-runbook-fails-to-start-or-stop-my-vms"></a><a name="custom-runbook"></a>Scenario: mijn aangepaste runbook kan mijn Vm's niet starten of stoppen

### <a name="issue"></a>Probleem

U hebt een aangepast runbook gemaakt of een gedownloade versie van het PowerShell Gallery, en deze werkt niet goed.

### <a name="cause"></a>Oorzaak

Er kunnen veel oorzaken zijn voor de fout. Ga naar uw Automation-account in het Azure Portal en selecteer **taken** onder **proces automatisering**. Zoek op de pagina **taken** naar taken van uw runbook om eventuele taak fouten weer te geven.

### <a name="resolution"></a>Oplossing

U wordt aangeraden dat u:

* Gebruik de [oplossing Vm's starten/stoppen](../automation-solution-vm-management.md) voor het starten en stoppen van vm's in azure Automation. Deze oplossing is gemaakt door micro soft. 
* Houd er rekening mee dat micro soft geen ondersteuning biedt voor aangepaste runbooks. Mogelijk vindt u een oplossing voor uw aangepaste runbook in het [oplossen van problemen met runbook](runbooks.md). Controleer de [taak stromen](../automation-runbook-execution.md#job-statuses) om te controleren of er fouten zijn opgetreden. 

## <a name="scenario-vms-dont-start-or-stop-in-the-correct-sequence"></a><a name="dont-start-stop-in-sequence"></a>Scenario: Vm's worden niet in de juiste volg orde gestart of gestopt

### <a name="issue"></a>Probleem

De Vm's die u in de oplossing hebt geconfigureerd, worden niet in de juiste volg orde gestart of gestopt.

### <a name="cause"></a>Oorzaak

Dit probleem wordt veroorzaakt door onjuiste tagging op de Vm's.

### <a name="resolution"></a>Oplossing

Volg deze stappen om ervoor te zorgen dat de oplossing correct is geconfigureerd.

1. Zorg ervoor dat alle Vm's die moeten worden gestart of `sequencestart` gestopt `sequencestop` , een or-tag hebben, afhankelijk van uw situatie. Voor deze tags is een positief geheel getal vereist als waarde. Vm's worden in oplopende volg orde verwerkt op basis van deze waarde.
1. Zorg ervoor dat de resource groepen voor de Vm's die moeten worden gestart of gestopt, `External_Start_ResourceGroupNames` zich `External_Stop_ResourceGroupNames` in de or-variabelen bevinden, afhankelijk van uw situatie.
1. Test uw wijzigingen door het `SequencedStartStop_Parent` runbook uit te voeren met `WHATIF` de para meter ingesteld op True om een voor beeld van de wijzigingen weer te geven.

Voor meer informatie over het gebruik van de oplossing voor het starten en stoppen van Vm's op volg orde raadpleegt u [Vm's starten/stoppen in volg orde](../automation-solution-vm-management.md).

## <a name="scenario-startstop-vms-during-off-hours-job-fails-with-403-forbidden-error"></a><a name="403"></a>Scenario: het starten/stoppen van Vm's buiten kantoor uren mislukt met 403 verboden fout

### <a name="issue"></a>Probleem

U vindt taken die zijn mislukt met `403 forbidden` een fout voor het starten/stoppen van vm's tijdens daluren van de oplossing.

### <a name="cause"></a>Oorzaak

Dit probleem kan worden veroorzaakt door een onjuist geconfigureerd of verlopen uitvoeren als-account. Dit kan ook worden veroorzaakt door ontoereikende machtigingen voor de VM-resources door het run as-account.

### <a name="resolution"></a>Oplossing

Als u wilt controleren of uw uitvoeren als-account correct is geconfigureerd, gaat u naar uw Automation-account in het Azure Portal en selecteert u **uitvoeren als-accounts** onder **account instellingen**. Als een uitvoeren als-account onjuist is geconfigureerd of is verlopen, wordt de voor waarde in de status weer gegeven.

Als uw uitvoeren als-account onjuist is geconfigureerd, verwijdert u het uitvoeren als-account en maakt u het opnieuw. Zie [Azure Automation uitvoeren als-accounts beheren](../manage-runas-account.md)voor meer informatie.

Als het certificaat is verlopen voor uw uitvoeren als-account, volgt u de stappen in [zelfondertekend certificaat vernieuwen](../manage-runas-account.md#cert-renewal) om het certificaat te vernieuwen.

Als er ontbrekende machtigingen zijn, raadpleegt u [Quick Start: rollen weer geven die aan een gebruiker zijn toegewezen met behulp van de Azure Portal](../../role-based-access-control/check-access.md). U moet de toepassings-ID opgeven voor de service-principal die wordt gebruikt door het run as-account. U kunt deze waarde ophalen door naar uw Automation-account te gaan in de Azure Portal. Selecteer **uitvoeren als-accounts** onder **account instellingen**en selecteer het juiste uitvoeren als-account.

## <a name="scenario-my-problem-isnt-listed-here"></a><a name="other"></a>Scenario: mijn probleem wordt hier niet vermeld

### <a name="issue"></a>Probleem

U ondervindt een probleem of onverwacht resultaat wanneer u de oplossing Vm's starten/stoppen buiten kantoor uren gebruikt die niet op deze pagina worden weer gegeven.

### <a name="cause"></a>Oorzaak

Vaak kunnen fouten worden veroorzaakt door gebruik te maken van een oude en verouderde versie van de oplossing.

> [!NOTE]
> De oplossing voor het starten/stoppen van Vm's buiten kantoor uren is getest met de Azure-modules die worden geïmporteerd in uw Automation-account wanneer u de oplossing implementeert. De oplossing werkt momenteel niet met nieuwere versies van de Azure-module. Deze beperking is alleen van invloed op het Automation-account dat u gebruikt voor het uitvoeren van de oplossing voor het starten/stoppen van Vm's tijdens een buiten kantoor uren. U kunt nog steeds nieuwere versies van de Azure-module gebruiken in uw andere Automation-accounts, zoals beschreven in [Azure PowerShell-modules bijwerken in azure Automation](../automation-update-azure-modules.md).

### <a name="resolution"></a>Oplossing

Als u veel fouten wilt oplossen, verwijdert u [de virtuele machines starten en stoppen tijdens de oplossing buiten kantoor uren](../automation-solution-vm-management.md#update-the-solution). U kunt ook de [taak stromen](../automation-runbook-execution.md#job-statuses) controleren om fouten op te sporen. 

## <a name="next-steps"></a>Volgende stappen

Als uw probleem hier niet wordt weer gegeven of u het probleem niet kunt oplossen, kunt u een van de volgende kanalen proberen voor aanvullende ondersteuning:

* Krijg antwoorden van Azure-experts via [Azure-forums](https://azure.microsoft.com/support/forums/).
* Maak verbinding [@AzureSupport](https://twitter.com/azuresupport)met, het officiële Microsoft Azure account voor het verbeteren van de gebruikers ervaring. Azure-ondersteuning verbindt de Azure-community met antwoorden, ondersteuning en experts.
* Een ondersteunings incident voor Azure. Ga naar de [ondersteunings site van Azure](https://azure.microsoft.com/support/options/)en selecteer **ondersteuning verkrijgen**.