---
title: Problemen oplossen met het starten en stoppen van Vm's-Azure Automation
description: Dit artikel bevat informatie over het oplossen van problemen met het starten en stoppen van Vm's in Azure Automation.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 04/04/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 97ea98fc38fc8d06dc1bc65ee057241da6f15488
ms.sourcegitcommit: f5e4d0466b417fa511b942fd3bd206aeae0055bc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/06/2020
ms.locfileid: "78851392"
---
# <a name="troubleshoot-the-startstop-vms-during-off-hours-solution"></a>Problemen oplossen met het starten/stoppen van Vm's buiten kantoor uren

## <a name="deployment-failure"></a>Scenario: de oplossing VM starten/stoppen kan niet goed worden geïmplementeerd

### <a name="issue"></a>Probleem

Bij het implementeren van de oplossing voor het [starten/stoppen van vm's tijdens een off-periode](../automation-solution-vm-management.md), wordt een van de volgende fouten weer gegeven:

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

Implementaties kunnen mislukken vanwege een van de volgende redenen:

1. Er is al een Automation-account met dezelfde naam in de geselecteerde regio.
2. Er is een beleid aanwezig waarmee de implementatie van de virtuele machines van het starten/stoppen van Vm's niet is toegestaan.
3. De resource typen `Microsoft.OperationsManagement`, `Microsoft.Insights`of `Microsoft.Automation` zijn niet geregistreerd.
4. Er is een vergren deling van uw Log Analytics-werk ruimte.
5. U hebt een verouderde versie van AzureRM modules of de oplossing starten/stoppen.

### <a name="resolution"></a>Oplossing

Raadpleeg de volgende lijst voor mogelijke oplossingen voor uw probleem of voor het zoeken naar:

1. Automation-accounts moeten uniek zijn binnen een Azure-regio, zelfs als ze zich in verschillende resource groepen bevinden. Controleer uw bestaande Automation-accounts in de doel regio.
2. Een bestaand beleid voor komt dat een resource die vereist is voor het implementeren van de oplossing VM starten/stoppen. Ga naar de beleids toewijzingen in de Azure Portal en controleer of u een beleids toewijzing hebt die de implementatie van deze resource niet toestaat. Zie [RequestDisallowedByPolicy](../../azure-resource-manager/templates/error-policy-requestdisallowedbypolicy.md)voor meer informatie over dit onderwerp.
3. Als u de oplossing voor het starten/stoppen van de VM wilt implementeren, moet uw abonnement worden geregistreerd bij de volgende Azure-resource-naam ruimten:
    * `Microsoft.OperationsManagement`
    * `Microsoft.Insights`
    * `Microsoft.Automation`

   Zie [fouten voor de registratie van de resource provider oplossen](../../azure-resource-manager/templates/error-register-resource-provider.md) voor meer informatie over fouten bij het registreren van providers.
4. Als u een vergren deling op uw Log Analytics-werk ruimte hebt, gaat u naar uw werk ruimte in de Azure Portal en verwijdert u de vergren delingen van de resource.
5. Als de bovenstaande oplossingen het probleem niet oplossen, volgt u de instructies onder [Update de oplossing](../automation-solution-vm-management.md#update-the-solution) om de oplossing start/stop opnieuw te implementeren.

## <a name="all-vms-fail-to-startstop"></a>Scenario: alle Vm's kunnen niet worden gestart/gestopt

### <a name="issue"></a>Probleem

U hebt de oplossing VM starten/stoppen geconfigureerd, maar niet alle geconfigureerde Vm's worden gestart of gestopt.

### <a name="cause"></a>Oorzaak

Deze fout kan een van de volgende oorzaken hebben:

1. Een planning is niet juist geconfigureerd
2. Het runas-account is mogelijk niet juist geconfigureerd
3. Er kunnen fouten in een runbook worden uitgevoerd
4. De Vm's zijn mogelijk uitgesloten

### <a name="resolution"></a>Oplossing

Raadpleeg de volgende lijst voor mogelijke oplossingen voor uw probleem of voor het zoeken naar:

* Controleer of u een planning hebt geconfigureerd voor de oplossing VM starten/stoppen. Zie het artikel [planningen](../automation-schedules.md) voor meer informatie over het configureren van een schema.

* Controleer de [taak stromen](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) om te controleren of er fouten zijn opgetreden. Ga in de portal naar uw Automation-account en selecteer **taken** onder **proces automatisering**. Ga op de pagina **taken** naar taken van een van de volgende runbooks:

  * AutoStop_CreateAlert_Child
  * AutoStop_CreateAlert_Parent
  * AutoStop_Disable
  * AutoStop_VM_Child
  * ScheduledStartStop_Base_Classic
  * ScheduledStartStop_Child_Classic
  * ScheduledStartStop_Child
  * ScheduledStartStop_Parent
  * SequencedStartStop_Parent

* Controleer of uw [runas-account](../manage-runas-account.md) de juiste machtigingen heeft voor de vm's die u probeert te starten of te stoppen. Zie voor meer informatie over het controleren van de machtigingen voor een resource [Quick Start: rollen weer geven die zijn toegewezen aan een gebruiker met behulp van de Azure Portal](../../role-based-access-control/check-access.md). U moet de toepassings-ID opgeven voor de service-principal die wordt gebruikt door het run as-account. U kunt deze waarde ophalen door naar uw Automation-account in de Azure Portal te gaan en **uitvoeren als-accounts** te selecteren onder **account instellingen** en te klikken op het desbetreffende run as-account.

* Vm's mogen niet worden gestart of gestopt als ze expliciet worden uitgesloten. Uitgesloten Vm's op ingesteld in de variabele **External_ExcludeVMNames** in het Automation-account waarop de oplossing is geïmplementeerd. In het volgende voor beeld ziet u hoe u een query kunt uitvoeren op die waarde met Power shell.

  ```powershell-interactive
  Get-AzureRmAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

## <a name="some-vms-fail-to-startstop"></a>Scenario: sommige Vm's kunnen niet worden gestart of gestopt

### <a name="issue"></a>Probleem

U hebt de oplossing VM starten/stoppen geconfigureerd, maar er worden geen enkele geconfigureerde Vm's gestart of gestopt.

### <a name="cause"></a>Oorzaak

Deze fout kan een van de volgende oorzaken hebben:

1. Als u het sequentie scenario gebruikt, kan een tag ontbreken of onjuist zijn
2. De virtuele machine kan worden uitgesloten
3. Het runas-account heeft mogelijk onvoldoende machtigingen voor de virtuele machine
4. Er is een fout opgetreden bij het starten of stoppen van de virtuele machine

### <a name="resolution"></a>Oplossing

Raadpleeg de volgende lijst voor mogelijke oplossingen voor uw probleem of voor het zoeken naar:

* Wanneer u het [Sequence-scenario](../automation-solution-vm-management.md#scenario-2-startstop-vms-in-sequence-by-using-tags) van de oplossing voor het starten/stoppen van de virtuele machine gedurende een vrije tijd gebruikt, moet u ervoor zorgen dat elke VM die u wilt starten of stoppen de juiste tag heeft. Zorg ervoor dat de virtuele machines die u wilt starten de `sequencestart`-tag hebben en dat de virtuele machines die u wilt stoppen, de `sequencestop`-tag hebben. Voor beide tags is een positief geheel getal vereist. U kunt een query gebruiken die vergelijkbaar is met het volgende voor beeld om te zoeken naar alle virtuele machines met de labels en hun waarden.

  ```powershell-interactive
  Get-AzureRmResource | ? {$_.Tags.Keys -contains "SequenceStart" -or $_.Tags.Keys -contains "SequenceStop"} | ft Name,Tags
  ```

* Vm's mogen niet worden gestart of gestopt als ze expliciet worden uitgesloten. Uitgesloten Vm's op ingesteld in de variabele **External_ExcludeVMNames** in het Automation-account waarop de oplossing is geïmplementeerd. In het volgende voor beeld ziet u hoe u een query kunt uitvoeren op die waarde met Power shell.

  ```powershell-interactive
  Get-AzureRmAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

* Voor het starten en stoppen van Vm's moet het runas-account voor het Automation-account de juiste machtigingen hebben voor de virtuele machine. Zie voor meer informatie over het controleren van de machtigingen voor een resource [Quick Start: rollen weer geven die zijn toegewezen aan een gebruiker met behulp van de Azure Portal](../../role-based-access-control/check-access.md). U moet de toepassings-ID opgeven voor de service-principal die wordt gebruikt door het run as-account. U kunt deze waarde ophalen door naar uw Automation-account in de Azure Portal te gaan en **uitvoeren als-accounts** te selecteren onder **account instellingen** en te klikken op het desbetreffende run as-account.

* Als de virtuele machine een probleem ondervindt met het starten of ongedaan maken van de toewijzing, kan dit gedrag worden veroorzaakt door een probleem op de VM zelf. Sommige voor beelden of mogelijke problemen zijn, een update wordt toegepast wanneer wordt geprobeerd om af te breken, een service loopt vast en meer). Ga naar de VM-resource en controleer de **activiteiten logboeken** om te zien of er fouten in de logboeken zijn. U kunt zich ook aanmelden bij de virtuele machine om te controleren of er fouten zijn opgetreden in de gebeurtenis Logboeken. Zie [problemen met virtuele Azure-machines oplossen](../../virtual-machines/troubleshooting/index.yml) voor meer informatie over het oplossen van problemen met uw virtuele machine

* Controleer de [taak stromen](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) om te controleren of er fouten zijn opgetreden. Ga in de portal naar uw Automation-account en selecteer **taken** onder **proces automatisering**.

## <a name="custom-runbook"></a>Scenario: mijn aangepaste runbook kan mijn Vm's niet starten of stoppen

### <a name="issue"></a>Probleem

U hebt een aangepast runbook gemaakt of een gedownloade versie van het PowerShell Gallery en deze werkt niet goed.

### <a name="cause"></a>Oorzaak

De oorzaak van de fout kan een van de vele dingen zijn. Ga naar uw Automation-account in de Azure Portal en selecteer **taken** onder **proces automatisering**. Zoek op de pagina **taken** naar taken van uw runbook om eventuele taak fouten weer te geven.

### <a name="resolution"></a>Oplossing

Het is raadzaam om de virtuele [machines voor het starten/stoppen van vm's](../automation-solution-vm-management.md) te gebruiken voor het starten en stoppen van vm's in azure Automation. Deze oplossing is gemaakt door micro soft. Aangepaste runbooks worden niet ondersteund door micro soft. U kunt een oplossing voor uw aangepaste runbook vinden door het artikel [over het oplossen](runbooks.md) van het runbook te bezoeken. Dit artikel bevat algemene richt lijnen en probleem oplossing voor runbooks van alle typen. Controleer de [taak stromen](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) om te controleren of er fouten zijn opgetreden. Ga in de portal naar uw Automation-account en selecteer **taken** onder **proces automatisering**.

## <a name="dont-start-stop-in-sequence"></a>Scenario: Vm's worden niet in de juiste volg orde gestart of gestopt

### <a name="issue"></a>Probleem

De Vm's die u in de oplossing hebt geconfigureerd, worden niet in de juiste volg orde gestart of gestopt.

### <a name="cause"></a>Oorzaak

Dit wordt veroorzaakt door onjuiste tagging op de Vm's.

### <a name="resolution"></a>Oplossing

Voer de volgende stappen uit om ervoor te zorgen dat de oplossing correct is geconfigureerd.

1. Zorg ervoor dat alle Vm's die moeten worden gestart of gestopt, een `sequencestart` of `sequencestop`-tag hebben, afhankelijk van uw situatie. Voor deze tags is een positief geheel getal vereist als waarde. Vm's worden in oplopende volg orde verwerkt op basis van deze waarde.
2. Zorg ervoor dat de resource groepen voor de Vm's die moeten worden gestart of gestopt, zich in de `External_Start_ResourceGroupNames`-of `External_Stop_ResourceGroupNames` variabelen bevinden, afhankelijk van uw situatie.
3. Test uw wijzigingen door het `SequencedStartStop_Parent` runbook uit te voeren waarbij de para meter WHATIF is ingesteld op True om een voor beeld van de wijzigingen weer te geven.

Zie [vm's in volg orde starten/stoppen](../automation-solution-vm-management.md#scenario-2-startstop-vms-in-sequence-by-using-tags)voor meer gedetailleerde en aanvullende instructies voor het gebruik van de oplossing om vm's op volg orde te starten en te stoppen.

## <a name="403"></a>Scenario: taak VM starten/stoppen mislukt met status van 403 verboden

### <a name="issue"></a>Probleem

U vindt taken die zijn mislukt met een `403 forbidden` fout voor de runbooks voor het starten/stoppen van Vm's tijdens off-banen oplossingen.

### <a name="cause"></a>Oorzaak

Dit probleem kan worden veroorzaakt door een onjuist geconfigureerd of verlopen uitvoeren als-account. Dit kan ook worden veroorzaakt door ontoereikende machtigingen voor de VM-bronnen door het run as-account voor Automation-accounts.

### <a name="resolution"></a>Oplossing

Als u wilt controleren of het run as-account correct is geconfigureerd, gaat u naar uw Automation-account in het Azure Portal en selecteert u **uitvoeren als-accounts** onder **account instellingen**. Hier ziet u de status van uw uitvoeren als-accounts, als een uitvoeren als-account onjuist is geconfigureerd of is verlopen. de status wordt hier weer gegeven.

Als uw uitvoeren als-account onjuist is geconfigureerd, moet u het uitvoeren als-account verwijderen en opnieuw maken. Zie [Azure Automation uitvoeren als-accounts beheren](../manage-runas-account.md).

Als het certificaat is verlopen voor uw uitvoeren als-account, volgt u de stappen die worden vermeld bij [zelfondertekend certificaat vernieuwen](../manage-runas-account.md#cert-renewal) om het certificaat te vernieuwen.

Het probleem wordt mogelijk veroorzaakt door ontbrekende machtigingen. Zie voor meer informatie over het controleren van de machtigingen voor een resource [Quick Start: rollen weer geven die zijn toegewezen aan een gebruiker met behulp van de Azure Portal](../../role-based-access-control/check-access.md). U moet de toepassings-ID opgeven voor de service-principal die wordt gebruikt door het run as-account. U kunt deze waarde ophalen door naar uw Automation-account in de Azure Portal te gaan en **uitvoeren als-accounts** te selecteren onder **account instellingen** en te klikken op het desbetreffende run as-account.

## <a name="other"></a>Scenario: mijn probleem komt niet voor in de lijst hierboven

### <a name="issue"></a>Probleem

U ondervindt een probleem of onverwacht resultaat wanneer u de VM's buiten bedrijfsuren starten/stoppen oplossing gebruikt die niet op deze pagina wordt vermeld.

### <a name="cause"></a>Oorzaak

Vaak kunnen fouten worden veroorzaakt door gebruik te maken van een oude en verouderde versie van de oplossing.

> [!NOTE]
> De VM's buiten bedrijfsuren starten/stoppen oplossing is getest met de Azure-modules die worden geïmporteerd in uw Automation-account wanneer u de oplossing implementeert. De oplossing werkt momenteel niet met nieuwere versies van de Azure-module. Dit is alleen van invloed op het Automation-account dat u gebruikt om de VM's buiten bedrijfsuren starten/stoppen-oplossing uit te voeren. U kunt nog steeds nieuwere versies van de Azure-module gebruiken in uw andere Automation-accounts, zoals beschreven in [Azure PowerShell-modules bijwerken in azure Automation](../automation-update-azure-modules.md)

### <a name="resolution"></a>Oplossing

Om veel fouten op te lossen, is het raadzaam om de oplossing te verwijderen en bij te werken. Voor informatie over het bijwerken van de oplossing raadpleegt u [de oplossing Vm's starten/stoppen tijdens buiten kantoor uren bijwerken](../automation-solution-vm-management.md#update-the-solution). Daarnaast kunt u de [taak stromen](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) controleren om fouten op te sporen. Ga in de portal naar uw Automation-account en selecteer **taken** onder **proces automatisering**.

## <a name="next-steps"></a>Volgende stappen

Als u het probleem niet ziet of als u het probleem niet kunt oplossen, gaat u naar een van de volgende kanalen voor meer ondersteuning:

* Krijg antwoorden van Azure-experts op [Azure-Forums](https://azure.microsoft.com/support/forums/)
* Maak verbinding met [@AzureSupport](https://twitter.com/azuresupport), het officiële Microsoft Azure-account voor het verbeteren van de gebruikerservaring door de Azure-community in contact te brengen met de juiste resources: antwoorden, ondersteuning en experts.
* Als u meer hulp nodig hebt, kunt u een ondersteunings incident voor Azure opslaan. Ga naar de [ondersteunings site van Azure](https://azure.microsoft.com/support/options/) en selecteer **ondersteuning verkrijgen**.
