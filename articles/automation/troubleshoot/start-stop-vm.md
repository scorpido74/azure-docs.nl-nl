---
title: Problemen met de VM's starten/stoppen tijdens de oplossing voor buitenkantoor
description: In dit artikel vindt u informatie over het oplossen van problemen met de VM-oplossing start/stop.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 04/04/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 003c2c5a2c09957e7a3a4ac0a26b87a9ac43dace
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81679151"
---
# <a name="troubleshoot-the-startstop-vms-during-off-hours-solution"></a>Problemen met de VM's starten/stoppen tijdens de oplossing voor buitenkantoor

In dit artikel vindt u informatie over probleemoplossingsproblemen die zich voordoen tijdens het werken met VM's tijdens de oplossing buiten kantooruren.

>[!NOTE]
>Dit artikel is bijgewerkt voor het gebruik van de nieuwe Azure PowerShell Az-module. De AzureRM-module kan nog worden gebruikt en krijgt bugoplossingen tot ten minste december 2020. Zie voor meer informatie over de nieuwe Az-module en compatibiliteit met AzureRM [Introductie van de nieuwe Az-module van Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Zie [De Azure PowerShell-module installeren](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)voor installatie-instructies voor az-modules op uw hybride runbookworker. Voor uw Automatiseringsaccount u uw modules bijwerken naar de nieuwste versie met [Azure PowerShell-modules bijwerken in Azure Automation.](../automation-update-azure-modules.md)

## <a name="scenario-the-startstop-vms-during-off-hours-solution-fails-to-properly-deploy"></a><a name="deployment-failure"></a>Scenario: De VM's starten/stoppen tijdens urenniet correct worden geïmplementeerd

### <a name="issue"></a>Probleem

Wanneer u de [VM's start/stop tijdens de oplossing voor buitenkantooruren implementeert,](../automation-solution-vm-management.md)ontvangt u een van de volgende fouten:

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

1. Er is al een Automatiseringsaccount met dezelfde naam in de geselecteerde regio.
2. Een beleid is het niet toestaan van de implementatie van de VM's start/stop tijdens de oplossing voor buitenkantooruren.
3. Het `Microsoft.OperationsManagement` `Microsoft.Insights`type `Microsoft.Automation` , of resource is niet geregistreerd.
4. Uw Log Analytics-werkruimte is vergrendeld.
5. U hebt een verouderde versie van de AzureRM-modules of de Vm's start/stop tijdens de oplossing voor buitenkantooruren.

### <a name="resolution"></a>Oplossing

Bekijk de volgende oplossingen voor mogelijke oplossingen voor uw probleem:

* Automatiseringsaccounts moeten uniek zijn binnen een Azure-gebied, zelfs als ze zich in verschillende brongroepen bevinden. Controleer uw bestaande automatiseringsaccounts in de doelregio.
* Een bestaand beleid voorkomt dat een resource die nodig is om de VM's starten/stoppen tijdens de oplossing voor buitenkantooruren te implementeren. Ga naar uw beleidstoewijzingen in de Azure-portal en controleer of u een beleidstoewijzing hebt die de implementatie van deze bron verbiedt. Zie [Beleid aanvragen](../../azure-resource-manager/templates/error-policy-requestdisallowedbypolicy.md)voor meer informatie .
* Als u de VM's start/stop wilt implementeren, moet uw abonnement worden geregistreerd op de volgende naamruimten van Azure-bronnen:

    * `Microsoft.OperationsManagement`
    * `Microsoft.Insights`
    * `Microsoft.Automation`

   Zie [Fouten oplossen voor de registratie van resourceproviders](../../azure-resource-manager/templates/error-register-resource-provider.md) voor meer informatie over fouten bij het registreren van providers.
* Als u een vergrendeling hebt op uw Log Analytics-werkruimte, gaat u naar uw werkruimte in de Azure-portal en verwijdert u eventuele vergrendelingen op de bron.
* Als de bovenstaande oplossingen het probleem niet oplossen, volgt u de instructies onder [Update de oplossing](../automation-solution-vm-management.md#update-the-solution) om de Start/Stop-oplossing opnieuw te implementeren.

## <a name="scenario-all-vms-fail-to-start-or-stop"></a><a name="all-vms-fail-to-startstop"></a>Scenario: Alle VM's kunnen niet worden gestart of gestopt

### <a name="issue"></a>Probleem

U hebt de VM's start/stop geconfigureerd tijdens de oplossing voor buitenkantooruren, maar deze start of stopt niet alle VM's.

### <a name="cause"></a>Oorzaak

Deze fout kan worden veroorzaakt door een van de volgende redenen:

1. Een planning is niet correct geconfigureerd.
2. Het account Uitvoeren als is mogelijk niet correct geconfigureerd.
3. Een runbook kan fouten hebben gemaakt.
4. De VM's zouden kunnen zijn uitgesloten.

### <a name="resolution"></a>Oplossing

Bekijk de volgende lijst voor mogelijke oplossingen voor uw probleem:

* Controleer of u een planning voor de VM's start/stop tijdens de oplossing voor buitenkantoorhebt geconfigureerd. Zie het artikel [Schema's](../automation-schedules.md) voor meer informatie over het configureren van een planning.

* Controleer de [taakstromen](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) om te zoeken naar eventuele fouten. Zoek naar vacatures uit een van de volgende runbooks:

  * **AutoStop_CreateAlert_Child**
  * **AutoStop_CreateAlert_Parent**
  * **AutoStop_Disable**
  * **AutoStop_VM_Child**
  * **ScheduledStartStop_Base_Classic**
  * **ScheduledStartStop_Child_Classic**
  * **ScheduledStartStop_Child**
  * **ScheduledStartStop_Parent**
  * **SequencedStartStop_Parent**

* Controleer of uw [Run As-account](../manage-runas-account.md) de juiste machtigingen heeft voor de VM's die u probeert te starten of te stoppen. Zie [Snelstart: Rollen weergeven die zijn toegewezen aan een gebruiker met de Azure-portal](../../role-based-access-control/check-access.md)voor meer informatie over het controleren van de machtigingen voor een resource. U moet de toepassings-id voor de serviceprincipal die wordt gebruikt door het Run As-account, verstrekken. U deze waarde ophalen door naar uw Automatiseringsaccount te gaan in de Azure-portal, **Uitvoeren als accounts** te selecteren onder **Accountinstellingen**en op het juiste Run As-account te klikken.

* VM's mogen niet worden gestart of gestopt als ze expliciet worden uitgesloten. Uitgesloten VM's worden `External_ExcludeVMNames` ingesteld in de variabele in het automatiseringsaccount waarop de oplossing wordt geïmplementeerd. In het volgende voorbeeld ziet u hoe u die waarde opvragen met PowerShell.

  ```powershell-interactive
  Get-AzAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

## <a name="scenario-some-of-my-vms-fail-to-start-or-stop"></a><a name="some-vms-fail-to-startstop"></a>Scenario: Sommige van mijn VM's kunnen niet starten of stoppen

### <a name="issue"></a>Probleem

U hebt de VM's start/stop geconfigureerd tijdens de oplossing voor buitenkantooruren, maar sommige VM's worden niet gestart of gestopt.

### <a name="cause"></a>Oorzaak

Deze fout kan worden veroorzaakt door een van de volgende redenen:

1. In het scenario van de reeks ontbreekt of onjuist een tag.
2. De VM kan worden uitgesloten.
3. Het run as-account heeft mogelijk niet genoeg machtigingen op de VM.
4. De VM kan een probleem hebben waardoor het niet kan starten of stoppen.

### <a name="resolution"></a>Oplossing

Bekijk de volgende lijst voor mogelijke oplossingen voor uw probleem of plaatsen om te zoeken:

* Wanneer u het [sequentiescenario](../automation-solution-vm-management.md) van de VM's starten/stoppen tijdens de oplossing voor buitenkantooruren gebruikt, moet u ervoor zorgen dat elke vm die u wilt starten of stoppen de juiste tag heeft. Zorg ervoor dat de VM's `sequencestart` die u wilt starten de tag `sequencestop` en de VM's hebben die u wilt stoppen, de tag. Beide tags vereisen een positieve gehele waarde. U een query gebruiken die vergelijkbaar is met het volgende voorbeeld om te zoeken naar alle VM's met de tags en hun waarden.

  ```powershell-interactive
  Get-AzResource | ? {$_.Tags.Keys -contains "SequenceStart" -or $_.Tags.Keys -contains "SequenceStop"} | ft Name,Tags
  ```

* VM's worden mogelijk niet gestart of gestopt als ze expliciet worden uitgesloten. Uitgesloten VM's worden `External_ExcludeVMNames` ingesteld in de variabele in het automatiseringsaccount waarop de oplossing wordt geïmplementeerd. In het volgende voorbeeld ziet u hoe u die waarde opvragen met PowerShell.

  ```powershell-interactive
  Get-AzAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

* Als u VM's wilt starten en stoppen, moet het account Uitvoeren als voor het automatiseringsaccount over de juiste machtigingen voor de VM beschikken. Zie [Snelstart: Rollen weergeven die zijn toegewezen aan een gebruiker met de Azure-portal](../../role-based-access-control/check-access.md)voor meer informatie over het controleren van de machtigingen voor een resource. U moet de toepassings-id voor de serviceprincipal die wordt gebruikt door het Run As-account, verstrekken. U deze waarde ophalen door naar uw Automatiseringsaccount te gaan in de Azure-portal, **Uitvoeren als accounts** te selecteren onder **Accountinstellingen** en op het juiste Run As-account te klikken.

* Als de VM een probleem heeft met het starten of deallocatie, kan er een probleem zijn op de VM zelf. Er wordt bijvoorbeeld een update toegepast wanneer de VM probeert af te sluiten, een service vastloopt en meer. Navigeer naar uw VM-bron en controleer de **activiteitslogboeken** om te zien of er fouten in de logboeken zijn. U ook proberen om u aan te melden bij de VM om te zien of er fouten zijn in de gebeurtenislogboeken. Zie [Probleemoplossing voor azure virtuele machines](../../virtual-machines/troubleshooting/index.yml) voor meer informatie over het oplossen van problemen met uw vm

* Controleer de [taakstromen](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) om te zoeken naar eventuele fouten. Ga in de portal naar uw automatiseringsaccount en selecteer **Vacatures** onder **Procesautomatisering.**

## <a name="scenario-my-custom-runbook-fails-to-start-or-stop-my-vms"></a><a name="custom-runbook"></a>Scenario: Mijn aangepaste runbook kan mijn VM's niet starten of stoppen

### <a name="issue"></a>Probleem

Je hebt een aangepast runbook geschreven of er een gedownload uit de PowerShell Gallery en het werkt niet goed.

### <a name="cause"></a>Oorzaak

Er kunnen vele oorzaken voor de mislukking zijn. Ga naar uw Automatiseringsaccount in de Azure-portal en selecteer **Taken** onder **Procesautomatisering**. Zoek op de pagina Vacatures naar vacatures uit uw runbook om eventuele taakfouten weer te geven.

### <a name="resolution"></a>Oplossing

Het wordt aanbevolen om:

* Gebruik de [VM's starten/stoppen tijdens off-hours-oplossing](../automation-solution-vm-management.md) om VM's in Azure Automation te starten en te stoppen. Deze oplossing is geschreven door Microsoft. 

* Houd er rekening mee dat Microsoft geen aangepaste runbooks ondersteunt. Mogelijk vindt u een oplossing voor uw aangepaste runbook van het oplossen van [problemen met runbook.](runbooks.md) Controleer de [taakstromen](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) om te zoeken naar eventuele fouten. 

## <a name="scenario-vms-dont-start-or-stop-in-the-correct-sequence"></a><a name="dont-start-stop-in-sequence"></a>Scenario: VM's starten of stoppen niet in de juiste volgorde

### <a name="issue"></a>Probleem

De VM's die u in de oplossing hebt geconfigureerd, starten of stoppen niet in de juiste volgorde.

### <a name="cause"></a>Oorzaak

Dit probleem wordt veroorzaakt door onjuiste tags op de VM's.

### <a name="resolution"></a>Oplossing

Neem de volgende stappen om ervoor te zorgen dat de oplossing correct is geconfigureerd.

1. Zorg ervoor dat alle VM's `sequencestart` `sequencestop` worden gestart of gestopt hebben een of tag, afhankelijk van uw situatie. Deze tags hebben een positief geheel getal als waarde nodig. VM's worden in oplopende volgorde verwerkt op basis van deze waarde.
2. Zorg ervoor dat de resourcegroepen voor de VM's die moeten worden gestart of gestopt, zich in de `External_Start_ResourceGroupNames` of `External_Stop_ResourceGroupNames` variabelen bevinden, afhankelijk van uw situatie.
3. Test uw wijzigingen door `SequencedStartStop_Parent` het runbook uit te voeren met de `WHATIF` parameter die is ingesteld op True om een voorbeeld van uw wijzigingen te bekijken.
4. Zie [VM's starten/stoppen in de juiste volgorde voor](../automation-solution-vm-management.md)meer informatie over het gebruik van de oplossing om VM's achter elkaar te starten en te stoppen.

## <a name="scenario-startstop-vms-during-off-hours-job-fails-with-403-forbidden-error"></a><a name="403"></a>Scenario: Start/Stop VM's tijdens off hours taak mislukt met 403 verboden fout

### <a name="issue"></a>Probleem

U vindt taken die `403 forbidden` zijn mislukt met een fout voor VM's starten/stoppen tijdens runbooks voor buitenkantooroplossingsoplossingen.

### <a name="cause"></a>Oorzaak

Dit probleem kan worden veroorzaakt door een onjuist geconfigureerd of verlopen Run As-account. Het kan ook zijn vanwege onvoldoende machtigingen voor de VM-resources door het Run As-account.

### <a name="resolution"></a>Oplossing

Als u wilt controleren of uw Run As-account correct is geconfigureerd, gaat u naar uw Automatiseringsaccount in de Azure-portal en selecteert **u Uitvoeren als accounts** onder **Accountinstellingen**. Als een Run As-account onjuist is geconfigureerd of verlopen, wordt de status weergegeven in de status.

Als uw Run As-account verkeerd is geconfigureerd, moet u uw Run As-account verwijderen en opnieuw maken. Zie [Azure Automation Run As-accounts beheren](../manage-runas-account.md).

Zie stappen in [Zelfondertekende certificaatverlenging](../manage-runas-account.md#cert-renewal) om het certificaat te vernieuwen als het certificaat is verlopen voor uw Run As-account.

Zie [Snelstart: Rollen weergeven die zijn toegewezen aan een gebruiker met de Azure-portal](../../role-based-access-control/check-access.md)als er geen machtigingen zijn. U moet de toepassings-id opgeven voor de serviceprincipal die wordt gebruikt door het Run As-account. U deze waarde ophalen door naar uw Automatiseringsaccount te gaan in de Azure-portal, **Uitvoeren als accounts** te selecteren onder **Accountinstellingen**en op het juiste Run As-account te klikken.

## <a name="scenario-my-problem-isnt-listed-above"></a><a name="other"></a>Scenario: Mijn probleem wordt hierboven niet vermeld

### <a name="issue"></a>Probleem

U ervaart een probleem of onverwacht resultaat wanneer u de VM's start/stop gebruikt tijdens de oplossing voor buitenkantooruren die niet op deze pagina wordt vermeld.

### <a name="cause"></a>Oorzaak

Vele malen fouten kunnen worden veroorzaakt door het gebruik van een oude en verouderde versie van de oplossing.

> [!NOTE]
> De start/stop VM's tijdens off hours-oplossing zijn getest met de Azure-modules die worden geïmporteerd in uw Automatiseringsaccount wanneer u de oplossing implementeert. De oplossing werkt momenteel niet met nieuwere versies van de Azure-module. Dit heeft alleen invloed op het automatiseringsaccount dat u gebruikt om de VM's starten/stoppen uit te voeren tijdens de oplossing voor buitenkantooruren. U nog steeds nieuwere versies van de Azure-module gebruiken in uw andere Automatiseringsaccounts, zoals beschreven in [Azure PowerShell-modules bijwerken in Azure Automation](../automation-update-azure-modules.md)

### <a name="resolution"></a>Oplossing

Om veel fouten op te lossen, is het raadzaam om [de VM's starten/stoppen tijdens de oplossing voor buitenkantooruren](../automation-solution-vm-management.md#update-the-solution)te verwijderen en bij te werken. Daarnaast u de [taakstromen](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) controleren om te zoeken naar eventuele fouten. 

## <a name="next-steps"></a>Volgende stappen

Als je het probleem hierboven niet ziet of het probleem niet oplossen, probeer je een van de volgende kanalen voor extra ondersteuning:

* Krijg antwoorden van Azure-experts via [Azure Forums.](https://azure.microsoft.com/support/forums/)
* Maak [@AzureSupport](https://twitter.com/azuresupport)verbinding met het officiële Microsoft Azure-account voor het verbeteren van de klantervaring door de Azure-community te verbinden met de juiste bronnen: antwoorden, ondersteuning en experts.
* Een Azure-ondersteuningsincident indienen. Ga naar de [Azure-ondersteuningssite](https://azure.microsoft.com/support/options/) en selecteer **Ondersteuning opdoen**.