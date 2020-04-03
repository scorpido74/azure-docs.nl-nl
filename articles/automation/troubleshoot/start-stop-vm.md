---
title: Problemen met het starten en stoppen van VM's oplossen - Azure Automation
description: In dit artikel vindt u informatie over het oplossen van problemen met het starten en stoppen van VM's in Azure Automation.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 04/04/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 73a9680cc570179c47b527a4844488da69193cb3
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80586099"
---
# <a name="troubleshoot-the-startstop-vms-during-off-hours-solution"></a>Problemen met de VM's starten/stoppen tijdens de oplossing voor buitenkantoor

## <a name="scenario-the-startstop-vm-solution-fails-to-properly-deploy"></a><a name="deployment-failure"></a>Scenario: De VM-oplossing Start/Stop wordt niet correct geïmplementeerd

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

1. Er is al een automatiseringsaccount met dezelfde naam in de geselecteerde regio.
2. Er is een beleid dat de implementatie van de VM's start/stop-v.c. niet kan implementeren.
3. De `Microsoft.OperationsManagement` `Microsoft.Insights`, `Microsoft.Automation` - of resourcetypen worden niet geregistreerd.
4. Op uw Log Analytics-werkruimte wordt een vergrendeling gesloten.
5. U hebt een verouderde versie van AzureRM-modules of de Start/Stop-oplossing.

### <a name="resolution"></a>Oplossing

Bekijk de volgende lijst voor mogelijke oplossingen voor uw probleem of plaatsen om te zoeken:

1. Automatiseringsaccounts moeten uniek zijn binnen een Azure-gebied, zelfs als ze zich in verschillende brongroepen bevinden. Controleer uw bestaande automatiseringsaccounts in de doelregio.
2. Een bestaand beleid voorkomt dat een resource nodig is om de VM-oplossing start/stop te implementeren. Ga naar uw beleidstoewijzingen in de Azure-portal en controleer of u een beleidstoewijzing hebt die de implementatie van deze bron verbiedt. Zie [Beleid aanvragen](../../azure-resource-manager/templates/error-policy-requestdisallowedbypolicy.md)voor meer informatie .
3. Als u de VM-oplossing Start/Stop wilt implementeren, moet uw abonnement worden geregistreerd op de volgende naamruimten van Azure-bronnen:
    * `Microsoft.OperationsManagement`
    * `Microsoft.Insights`
    * `Microsoft.Automation`

   Zie, [Fouten oplossen voor de registratie van resourceproviders](../../azure-resource-manager/templates/error-register-resource-provider.md) voor meer informatie over fouten bij het registreren van providers.
4. Als u een vergrendeling hebt op uw Log Analytics-werkruimte, gaat u naar uw werkruimte in de Azure-portal en verwijdert u eventuele vergrendelingen op de bron.
5. Als de bovenstaande oplossingen het probleem niet oplossen, volgt u de instructies onder [Update de oplossing](../automation-solution-vm-management.md#update-the-solution) om de Start/Stop-oplossing opnieuw te implementeren.

## <a name="scenario-all-vms-fail-to-startstop"></a><a name="all-vms-fail-to-startstop"></a>Scenario: Alle VM's kunnen niet starten/stoppen

### <a name="issue"></a>Probleem

U hebt de VM-oplossing Start/Stop geconfigureerd, maar deze start of stopt niet alle geconfigureerde VM's.

### <a name="cause"></a>Oorzaak

Deze fout kan worden veroorzaakt door een van de volgende redenen:

1. Een planning is niet correct geconfigureerd
2. Het RunAs-account is mogelijk niet correct geconfigureerd
3. Een runbook kan fouten hebben opgelopen
4. De VM's kunnen zijn uitgesloten

### <a name="resolution"></a>Oplossing

Bekijk de volgende lijst voor mogelijke oplossingen voor uw probleem of plaatsen om te zoeken:

* Controleer of u een planning voor de VM-oplossing start/stop hebt geconfigureerd. Zie het artikel [Schema's](../automation-schedules.md) voor meer informatie over het configureren van een planning.

* Controleer de [taakstromen](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) om te zoeken naar eventuele fouten. Ga in de portal naar uw automatiseringsaccount en selecteer **Taken** onder **Procesautomatisering.** Zoek op de pagina **Vacatures** naar vacatures uit een van de volgende runbooks:

  * AutoStop_CreateAlert_Child
  * AutoStop_CreateAlert_Parent
  * AutoStop_Disable
  * AutoStop_VM_Child
  * ScheduledStartStop_Base_Classic
  * ScheduledStartStop_Child_Classic
  * ScheduledStartStop_Child
  * ScheduledStartStop_Parent
  * SequencedStartStop_Parent

* Controleer of je [RunAs-account](../manage-runas-account.md) de juiste machtigingen heeft voor de VM's die je probeert te starten of te stoppen. Zie [Snelstart: Rollen weergeven die zijn toegewezen aan een gebruiker met de Azure-portal](../../role-based-access-control/check-access.md)voor meer informatie over het controleren van de machtigingen voor een resource. U moet de toepassings-id voor de serviceprincipal die wordt gebruikt door het run As-account, verstrekken. U deze waarde ophalen door naar uw automatiseringsaccount te gaan in de Azure-portal, **Uitvoeren als accounts** te selecteren onder **Accountinstellingen** en op de juiste Run As-account te klikken.

* VM's mogen niet worden gestart of gestopt als ze expliciet worden uitgesloten. Uitgesloten VM's in de **External_ExcludeVMNames** variabele in het automatiseringsaccount waar de oplossing naar wordt geïmplementeerd. In het volgende voorbeeld ziet u hoe u die waarde opvragen met PowerShell.

  ```powershell-interactive
  Get-AzureRmAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

## <a name="scenario-some-of-my-vms-fail-to-start-or-stop"></a><a name="some-vms-fail-to-startstop"></a>Scenario: Sommige van mijn VM's kunnen niet starten of stoppen

### <a name="issue"></a>Probleem

U hebt de VM-oplossing Start/Stop geconfigureerd, maar deze start of stopt sommige VM's niet.

### <a name="cause"></a>Oorzaak

Deze fout kan worden veroorzaakt door een van de volgende redenen:

1. Als u het sequentiescenario gebruikt, kan een tag ontbreken of onjuist zijn
2. De VM kan worden uitgesloten
3. Het RunAs-account heeft mogelijk niet genoeg machtigingen op de VM
4. De VM kan iets hebben dat het stoppen of stoppen ervan weerhield

### <a name="resolution"></a>Oplossing

Bekijk de volgende lijst voor mogelijke oplossingen voor uw probleem of plaatsen om te zoeken:

* Wanneer u het [sequentiescenario](../automation-solution-vm-management.md) van de VM Start/Stop tijdens de oplossing voor buitenkantooruren gebruikt, moet u ervoor zorgen dat elke vm die u wilt starten of stoppen de juiste tag heeft. Zorg ervoor dat de VM's `sequencestart` die u wilt starten de tag `sequencestop` en de VM's hebben die u wilt stoppen, de tag. Beide tags vereisen een positieve gehele waarde. U een query gebruiken die vergelijkbaar is met het volgende voorbeeld om te zoeken naar alle VM's met de tags en hun waarden.

  ```powershell-interactive
  Get-AzureRmResource | ? {$_.Tags.Keys -contains "SequenceStart" -or $_.Tags.Keys -contains "SequenceStop"} | ft Name,Tags
  ```

* VM's mogen niet worden gestart of gestopt als ze expliciet worden uitgesloten. Uitgesloten VM's in de **External_ExcludeVMNames** variabele in het automatiseringsaccount waar de oplossing naar wordt geïmplementeerd. In het volgende voorbeeld ziet u hoe u die waarde opvragen met PowerShell.

  ```powershell-interactive
  Get-AzureRmAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

* Als u VM's wilt starten en stoppen, moet het RunAs-account voor het automatiseringsaccount over de juiste machtigingen voor de VM beschikken. Zie [Snelstart: Rollen weergeven die zijn toegewezen aan een gebruiker met de Azure-portal](../../role-based-access-control/check-access.md)voor meer informatie over het controleren van de machtigingen voor een resource. U moet de toepassings-id voor de serviceprincipal die wordt gebruikt door het run As-account, verstrekken. U deze waarde ophalen door naar uw automatiseringsaccount te gaan in de Azure-portal, **Uitvoeren als accounts** te selecteren onder **Accountinstellingen** en op de juiste Run As-account te klikken.

* Als de VM een probleem heeft met het starten of deallocatie, kan dit gedrag worden veroorzaakt door een probleem op de VM zelf. Enkele voorbeelden of mogelijke problemen zijn, een update wordt toegepast wanneer u probeert af te sluiten, een service vastloopt en meer). Navigeer naar uw VM-bron en controleer de **activiteitslogboeken** om te zien of er fouten in de logboeken zijn. U ook proberen u bij de VM aan te melden om te zien of er fouten zijn in de gebeurtenislogboeken. Zie [Probleemoplossing voor azure virtuele machines](../../virtual-machines/troubleshooting/index.yml) voor meer informatie over het oplossen van problemen met uw vm

* Controleer de [taakstromen](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) om te zoeken naar eventuele fouten. Ga in de portal naar uw automatiseringsaccount en selecteer **Taken** onder **Procesautomatisering.**

## <a name="scenario-my-custom-runbook-fails-to-start-or-stop-my-vms"></a><a name="custom-runbook"></a>Scenario: Mijn aangepaste runbook kan mijn VM's niet starten of stoppen

### <a name="issue"></a>Probleem

Je hebt een aangepast runbook geschreven of er een gedownload uit de PowerShell Gallery en het werkt niet goed.

### <a name="cause"></a>Oorzaak

De oorzaak van de storing kan een van de vele dingen zijn. Ga naar uw automatiseringsaccount in de Azure-portal en selecteer **Taken** onder **Procesautomatisering**. Zoek op de pagina **Vacatures** naar vacatures uit uw runbook om eventuele taakfouten weer te geven.

### <a name="resolution"></a>Oplossing

Het wordt aanbevolen om de [VM's starten/stoppen tijdens de oplossing voor uren](../automation-solution-vm-management.md) buiten kantooruren te gebruiken om VM's in Azure Automation te starten en te stoppen. Deze oplossing is geschreven door Microsoft. Aangepaste runbooks worden niet ondersteund door Microsoft. U vindt mogelijk een oplossing voor uw aangepaste runbook door het artikel [over probleemoplossing voor runbook](runbooks.md) te bezoeken. Dit artikel biedt algemene richtlijnen en probleemoplossing voor alle soorten runbooks. Controleer de [taakstromen](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) om te zoeken naar eventuele fouten. Ga in de portal naar uw automatiseringsaccount en selecteer **Taken** onder **Procesautomatisering.**

## <a name="scenario-vms-dont-start-or-stop-in-the-correct-sequence"></a><a name="dont-start-stop-in-sequence"></a>Scenario: VM's starten of stoppen niet in de juiste volgorde

### <a name="issue"></a>Probleem

De VM's die u in de oplossing hebt geconfigureerd, starten of stoppen niet in de juiste volgorde.

### <a name="cause"></a>Oorzaak

Dit wordt veroorzaakt door onjuiste tagging op de VM's.

### <a name="resolution"></a>Oplossing

Neem de volgende stappen om ervoor te zorgen dat de oplossing correct is geconfigureerd.

1. Zorg ervoor dat alle VM's `sequencestart` `sequencestop` worden gestart of gestopt hebben een of tag, afhankelijk van uw situatie. Deze tags hebben een positief geheel getal als waarde nodig. VM's worden in oplopende volgorde verwerkt op basis van deze waarde.
2. Zorg ervoor dat de resourcegroepen voor de VM's die moeten worden gestart of gestopt, zich in de `External_Start_ResourceGroupNames` of `External_Stop_ResourceGroupNames` variabelen bevinden, afhankelijk van uw situatie.
3. Test uw wijzigingen door `SequencedStartStop_Parent` het runbook uit te voeren met de parameter WHATIF ingesteld op True om een voorbeeld van uw wijzigingen te bekijken.

Zie [VM's starten/stoppen in de juiste volgorde voor](../automation-solution-vm-management.md)meer gedetailleerde en aanvullende instructies over het gebruik van de oplossing om VM's achter elkaar te starten en te stoppen.

## <a name="scenario-startstop-vm-job-fails-with-403-forbidden-status"></a><a name="403"></a>Scenario: Start/Stop VM-taak mislukt met 403 verboden status

### <a name="issue"></a>Probleem

U vindt taken die `403 forbidden` zijn mislukt met een fout voor de VM's starten/stoppen tijdens runbooks voor buitenkantooroplossings.

### <a name="cause"></a>Oorzaak

Dit probleem kan worden veroorzaakt door een onjuist geconfigureerde of verlopen Run As-account. Het kan ook zijn vanwege onvoldoende machtigingen voor de VM-resources door de automatiseringsaccounts die als account worden uitgevoerd.

### <a name="resolution"></a>Oplossing

Als u wilt controleren of uw Run As-account goed is geconfigureerd, gaat u naar uw automatiseringsaccount in de Azure-portal en selecteert **u Uitvoeren als accounts** onder **Accountinstellingen**. Hier ziet u de status van uw run als accounts, als een Run As-account onjuist is geconfigureerd of verlopen, wordt de status weergegeven.

Als uw Run As-account verkeerd is geconfigureerd, moet u uw Run As-account verwijderen en opnieuw maken. Zie [Azure Automation Run As-accounts beheren](../manage-runas-account.md).

Als het certificaat is verlopen voor uw Run As-account, voert u de stappen uit die worden aangeboden bij [zelfondertekende certificaatverlenging](../manage-runas-account.md#cert-renewal) om het certificaat te vernieuwen.

Het probleem kan worden veroorzaakt door ontbrekende machtigingen. Zie [Snelstart: Rollen weergeven die zijn toegewezen aan een gebruiker met de Azure-portal](../../role-based-access-control/check-access.md)voor meer informatie over het controleren van de machtigingen voor een resource. U moet de toepassings-id voor de serviceprincipal die wordt gebruikt door het run As-account, verstrekken. U deze waarde ophalen door naar uw automatiseringsaccount te gaan in de Azure-portal, **Uitvoeren als accounts** te selecteren onder **Accountinstellingen** en op de juiste Run As-account te klikken.

## <a name="scenario-my-problem-isnt-listed-above"></a><a name="other"></a>Scenario: Mijn probleem wordt hierboven niet vermeld

### <a name="issue"></a>Probleem

U ervaart een probleem of onverwacht resultaat wanneer u de VM's start/stop gebruikt tijdens de oplossing buiten kantooruren die niet op deze pagina wordt vermeld.

### <a name="cause"></a>Oorzaak

Vele malen fouten kunnen worden veroorzaakt door het gebruik van een oude en verouderde versie van de oplossing.

> [!NOTE]
> De start/stop VM's tijdens off-hours oplossing is getest met de Azure-modules die worden geïmporteerd in uw automatiseringsaccount wanneer u de oplossing implementeert. De oplossing werkt momenteel niet met nieuwere versies van de Azure-module. Dit heeft alleen invloed op het automatiseringsaccount dat u gebruikt om de VM's starten/stoppen uit te voeren tijdens off-hours oplossing. U nog steeds nieuwere versies van de Azure-module gebruiken in uw andere automatiseringsaccounts, zoals beschreven in [Hoe Azure PowerShell-modules bijwerken in Azure Automation](../automation-update-azure-modules.md)

### <a name="resolution"></a>Oplossing

Om veel fouten op te lossen, is het raadzaam om de oplossing te verwijderen en bij te werken. Zie [Vm's starten/stoppen tijdens de oplossing voor buitenkantoor uren](../automation-solution-vm-management.md#update-the-solution)bijwerken voor meer informatie over het bijwerken van de oplossing. Daarnaast u de [taakstromen](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) controleren om te zoeken naar eventuele fouten. Ga in de portal naar uw automatiseringsaccount en selecteer **Taken** onder **Procesautomatisering.**

## <a name="next-steps"></a>Volgende stappen

Als je je probleem niet hebt gezien of niet in staat bent om je probleem op te lossen, ga je naar een van de volgende kanalen voor meer ondersteuning:

* Krijg antwoorden van Azure-experts op [Azure-Forums](https://azure.microsoft.com/support/forums/)
* Maak [@AzureSupport](https://twitter.com/azuresupport) verbinding met – het officiële Microsoft Azure-account voor het verbeteren van de klantervaring door de Azure-community te verbinden met de juiste bronnen: antwoorden, ondersteuning en experts.
* Als u meer hulp nodig hebt, u een Azure-ondersteuningsincident indienen. Ga naar de [Azure-ondersteuningssite](https://azure.microsoft.com/support/options/) en selecteer **Ondersteuning opdoen**.
