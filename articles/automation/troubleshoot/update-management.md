---
title: Fouten oplossen met Azure Update Management
description: Meer informatie over het oplossen en oplossen van problemen met de oplossing voor updatebeheer in Azure.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/17/2020
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 91bcdc65a7ff3bcaf09f12d69ba4c7aaeb84ffa7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132855"
---
# <a name="troubleshooting-issues-with-update-management"></a>Problemen oplossen met Updatebeheer

In dit artikel worden oplossingen besproken voor problemen die u tegenkomen wanneer u Updatebeheer gebruikt.

Er is een probleemoplosser voor de agent Hybride werknemer om het onderliggende probleem te bepalen. Zie Problemen met [de updateagent oplossen](update-agent-issues.md)voor meer informatie over de probleemoplosser. Voor alle andere problemen gebruikt u de volgende richtlijnen voor het oplossen van problemen.

Als u problemen ondervindt terwijl u de oplossing op een virtuele machine (VM) probeert aan boord te nemen, controleert u het logboek **Operations Manager** onder **Logboeken van toepassing en services** op de lokale machine voor gebeurtenissen met gebeurtenis-id 4502 en gebeurtenisgegevens die bevatten. `Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent`

In de volgende sectie worden specifieke foutberichten en mogelijke oplossingen voor elk gedeelte belicht. Zie Problemen met [oplossingsonboarding oplossen](onboarding.md)voor andere onboarding problemen.

## <a name="scenario-you-receive-the-error-failed-to-enable-the-update-solution"></a>Scenario: U ontvangt de fout 'Kan de update-oplossing niet inschakelen'

### <a name="issue"></a>Probleem

Wanneer u de oplossing Updatebeheer probeert in te schakelen in uw Automatiseringsaccount, komt u de volgende fout tegen:

```error
Error details: Failed to enable the Update solution
```

### <a name="cause"></a>Oorzaak

Deze fout kan optreden om de volgende redenen:

* De vereisten voor netwerkfirewall voor de Log Analytics-agent zijn mogelijk niet correct geconfigureerd, waardoor de agent niet is mislukt bij het oplossen van de DNS-URL's.

* Oplossingstargeting is verkeerd geconfigureerd en de machine ontvangt geen updates zoals verwacht.

* U ook merken dat de `Non-compliant` machine een status van onder **Compliance**toont. Tegelijkertijd rapporteert **de gereedheid van agentupdate de** agent als `Disconnected`.

### <a name="resolution"></a>Oplossing

* Voer de probleemoplosser voor [Windows](update-agent-issues.md#troubleshoot-offline) of [Linux](update-agent-issues-linux.md#troubleshoot-offline)uit, afhankelijk van het besturingssysteem.

* Ga naar [Netwerkplanning](../automation-hybrid-runbook-worker.md#network-planning) om meer te weten te komen over welke adressen en poorten moeten worden toegestaan om UpdateBeheer te laten werken.  

* Ga naar [Netwerkplanning](../../azure-monitor/platform/log-analytics-agent.md#network-requirements) om meer te weten te komen over welke adressen en poorten moeten worden toegestaan om de log-analyse-agent te laten werken.

* Controleer op problemen met de scopeconfiguratie. [Scopeconfiguratie](../automation-onboard-solutions-from-automation-account.md#scope-configuration) bepaalt welke machines worden geconfigureerd voor de oplossing. Als uw machine wordt weergegeven in uw werkruimte, maar niet in de portal **Updatebeheer,** moet u de scopeconfiguratie instellen om de machines te targeten. Zie [Onboard-machines in de werkruimte](../automation-onboard-solutions-from-automation-account.md#onboard-machines-in-the-workspace)voor meer informatie over de scopeconfiguratie.

* Verwijder de werknemersconfiguratie door de stappen te volgen in [Het verwijderen van de hybride runbookworker](../automation-hybrid-runbook-worker.md#remove-a-hybrid-runbook-worker). 

## <a name="scenario-superseded-update-indicated-as-missing-in-update-management"></a>Scenario: Vervangen update als ontbrekend in UpdateBeheer aangegeven

### <a name="issue"></a>Probleem

Oude updates worden weergegeven in Updatebeheer in het automatiseringsaccount als ontbrekende, ook al zijn ze vervangen. Een vervangen update is een update die niet hoeft te worden geïnstalleerd omdat een latere update die hetzelfde beveiligingslek corrigeert beschikbaar is. Update Management negeert de vervangen update en maakt deze niet van toepassing ten gunste van de vervangende update. Zie [Bijwerken is vervangen](https://docs.microsoft.com/windows/deployment/update/windows-update-troubleshooting#the-update-is-not-applicable-to-your-computer)voor informatie over een gerelateerd probleem.

### <a name="cause"></a>Oorzaak

Vervangen updates worden niet correct aangegeven als geweigerd, zodat ze kunnen worden beschouwd als niet van toepassing.

### <a name="resolution"></a>Oplossing

Wanneer een vervangen update 100 procent niet van toepassing wordt, moet `Declined`u de goedkeuringsstatus van die update wijzigen in . Ga als volgt te werk om dit te doen voor al uw updates:

1. Selecteer in het automatiseringsaccount **Updatebeheer** om de status van de machine weer te geven. Zie [Updatebeoordelingen weergeven](../manage-update-multi.md#view-an-update-assessment).

2. Controleer de vervangen update om er zeker van te zijn dat deze 100 procent niet van toepassing is. 

3. Markeer de update als geweigerd, tenzij u een vraag hebt over de update. 

4. Selecteer **Computers** en forceer in de kolom **Naleving** een rescan voor naleving. Zie [Updates voor meerdere machines beheren](../manage-update-multi.md).

5. Herhaal de bovenstaande stappen voor andere vervangen updates.

6. Voer de wizard Opscheerderen uit om bestanden uit de geweigerde updates te verwijderen. 

7. Voor WSUS u alle vervangen updates handmatig opschonen om de infrastructuur te vernieuwen.

8. Herhaal deze procedure regelmatig om het weergaveprobleem te corrigeren en de hoeveelheid schijfruimte die wordt gebruikt voor updatebeheer te minimaliseren.

## <a name="scenario-machines-dont-show-up-in-the-portal-under-update-management"></a><a name="nologs"></a>Scenario: Machines worden niet weergegeven in de portal onder Updatebeheer

### <a name="issue"></a>Probleem

Uw machines hebben de volgende symptomen:

* Uw machine `Not configured` wordt weergegeven in de weergave Updatebeheer van een virtuele machine.

* Uw machines ontbreken in de updatebeheerweergave van uw Azure Automation-account.

* U hebt machines `Not assessed` die worden weergegeven als onder **Compliance**. U ziet echter heartbeatgegevens in Azure Monitor-logboeken voor de hybride runbookworker, maar niet voor Updatebeheer.

### <a name="cause"></a>Oorzaak

Dit probleem kan worden veroorzaakt door lokale configuratieproblemen of door onjuist geconfigureerde scopeconfiguratie.

Mogelijk moet u de hybride runbookworker opnieuw registreren en opnieuw installeren.

Mogelijk hebt u een quotum in uw werkruimte gedefinieerd dat is bereikt en dat verdere gegevensopslag verhindert.

### <a name="resolution"></a>Oplossing

* Voer de probleemoplosser voor [Windows](update-agent-issues.md#troubleshoot-offline) of [Linux](update-agent-issues-linux.md#troubleshoot-offline)uit, afhankelijk van het besturingssysteem.

* Zorg ervoor dat uw machine rapporteert aan de juiste werkruimte. Zie De verbinding van de [agent met Logboekanalyse verifiëren](../../azure-monitor/platform/agent-windows.md#verify-agent-connectivity-to-log-analytics)voor richtlijnen voor het verifiëren van dit aspect. Zorg er ook voor dat deze werkruimte is gekoppeld aan uw Azure Automation-account. Als u dit wilt bevestigen, gaat u naar uw automatiseringsaccount en selecteert u **Gekoppelde werkruimte** onder **Gerelateerde resources**.

* Zorg ervoor dat de machines worden weergegeven in uw Log Analytics-werkruimte. Voer de volgende query uit in de werkruimte Log Analytics die is gekoppeld aan uw Automatiseringsaccount:

  ```loganalytics
  Heartbeat
  | summarize by Computer, Solutions
  ```
 
  Als u uw machine niet ziet in de queryresultaten, is deze niet onlangs ingecheckt, wat betekent dat er waarschijnlijk een lokaal configuratieprobleem is en u de agent opnieuw moet [installeren.](../../azure-monitor/learn/quick-collect-windows-computer.md#install-the-agent-for-windows) Als uw machine wordt weergegeven in de queryresultaten, moet u de scopeconfiguratie verifiëren die is opgegeven in het volgende item met opsommingstekens in deze lijst.

* Controleer op problemen met de scopeconfiguratie. [Scopeconfiguratie](../automation-onboard-solutions-from-automation-account.md#scope-configuration) bepaalt welke machines worden geconfigureerd voor de oplossing. Als uw machine wordt weergegeven in uw werkruimte, maar niet in de portal **Updatebeheer,** moet u de scopeconfiguratie configureren om de machines te targeten. Zie Machines aan boord [in de werkruimte](../automation-onboard-solutions-from-automation-account.md#onboard-machines-in-the-workspace)voor meer informatie over hoe u dit doen.

* Voer in uw werkruimte de volgende query uit:

  ```loganalytics
  Operation
  | where OperationCategory == 'Data Collection Status'
  | sort by TimeGenerated desc
  ```

  Als u `Data collection stopped due to daily limit of free data reached. Ingestion status = OverQuota` een resultaat krijgt, is er een quotum gedefinieerd op uw werkruimte dat is bereikt en waardoor gegevens niet meer worden opgeslagen. Ga in uw werkruimte naar **Beheer en geschatte kosten gegevensvolumebeheer** > **data volume management** en controleer uw quotum of verwijder het.

* Als het probleem met deze stappen niet wordt opgelost, voert u de stappen uit in [Een hybride runbookworker implementeren](../automation-windows-hrw-install.md) om de hybride werknemer voor Windows opnieuw te installeren. Volg voor Linux de stappen in [Een Linux Hybrid Runbook Worker implementeren.](../automation-linux-hrw-install.md)

## <a name="scenario-unable-to-register-automation-resource-provider-for-subscriptions"></a><a name="rp-register"></a>Scenario: Kan automation resourceprovider niet registreren voor abonnementen

### <a name="issue"></a>Probleem

Wanneer u met oplossingen in uw Automatiseringsaccount werkt, komt u de volgende fout tegen:

```error
Error details: Unable to register Automation Resource Provider for subscriptions:
```

### <a name="cause"></a>Oorzaak

De Automation Resource Provider is niet geregistreerd in het abonnement.

### <a name="resolution"></a>Oplossing

Voer de volgende stappen uit in de Azure-portal als u de Automation Resource Provider wilt registreren:

1. Selecteer in de azure-servicelijst onder aan de portal **alle services**en selecteer **Vervolgens Abonnementen** in de servicegroep Algemeen.
2. Selecteer uw abonnement.
3. Selecteer **Resourceproviders**onder **Instellingen**.
4. Controleer in de lijst met `Microsoft.Automation` resourceproviders of de resourceprovider is geregistreerd.
5. Als deze niet wordt vermeld, `Microsoft.Automation` registreert u de provider door de stappen op te volgen bij [Fouten oplossen voor registratie van resourcesprovider.](/azure/azure-resource-manager/resource-manager-register-provider-errors)

## <a name="scenario-components-for-update-management-solution-enabled-while-vm-continues-to-show-as-being-configured"></a><a name="components-enabled-not-working"></a>Scenario: Components for Update Management-oplossing ingeschakeld, terwijl VM blijft worden weergegeven als geconfigureerd

### <a name="issue"></a>Probleem

U ziet het volgende bericht nog steeds op een virtuele machine 15 minuten na het instappen:

```error
The components for the 'Update Management' solution have been enabled, and now this virtual machine is being configured. Please be patient, as this can sometimes take up to 15 minutes.
```

### <a name="cause"></a>Oorzaak

Deze fout kan optreden om de volgende redenen:

* De communicatie met het Automation-account wordt geblokkeerd.

* Er is een dubbele computernaam met verschillende broncomputer-id's. Dit scenario treedt op wanneer een VM met een bepaalde computernaam wordt gemaakt in verschillende resourcegroepen en rapporteert aan dezelfde werkruimte van logistiek agent in het abonnement.

* De VM-afbeelding die aan boord wordt genomen, kan afkomstig zijn van een gekloonde machine die niet is voorbereid met System Preparation (sysprep) met de Microsoft Monitoring Agent (MMA) geïnstalleerd.

### <a name="resolution"></a>Oplossing

Voer de volgende query uit in de werkruimte Log Analytics die is gekoppeld aan uw automatiseringsaccount om het exacte probleem met de VM te bepalen:

```
Update
| where Computer contains "fillInMachineName"
| project TimeGenerated, Computer, SourceComputerId, Title, UpdateState 
```

#### <a name="communication-with-automation-account-blocked"></a>Communicatie met automation-account geblokkeerd

Ga naar [Netwerkplanning](../automation-update-management.md#ports) om meer te weten te komen over welke adressen en poorten moeten worden toegestaan om UpdateBeheer te laten werken.

#### <a name="duplicate-computer-name"></a>Computernaam dupliceren

Wijzig de naam van uw VM's om unieke namen in hun omgeving te garanderen.

#### <a name="onboarded-image-from-cloned-machine"></a>Aan boord van afbeelding van gekloonde machine

Als u een gekloonde afbeelding gebruikt, hebben verschillende computernamen dezelfde broncomputer-id. In dat geval:

1. Verwijder de VM in uw werkruimte Log Analytics `MicrosoftDefaultScopeConfig-Updates` uit de opgeslagen zoekopdracht naar de scopeconfiguratie als deze wordt weergegeven. Opgeslagen zoekopdrachten zijn te vinden onder **Algemeen** in uw werkruimte.

2. Voer de volgende cmdlet uit:

    ```azurepowershell-interactive
    Remove-Item -Path "HKLM:\software\microsoft\hybridrunbookworker" -Recurse -Force
    ```

3. Voer `Restart-Service HealthService` deze uit om de statusservice opnieuw te starten. Deze bewerking maakt de sleutel opnieuw en genereert een nieuwe UUID.

4. Als deze aanpak niet werkt, voert u eerst sysprep op de afbeelding uit en installeert u de MMA.

## <a name="scenario-you-receive-a-linked-subscription-error-when-you-create-an-update-deployment-for-machines-in-another-azure-tenant"></a><a name="multi-tenant"></a>Scenario: U ontvangt een gekoppelde abonnementsfout wanneer u een update-implementatie maakt voor machines in een andere Azure-tenant

### <a name="issue"></a>Probleem

U komt de volgende fout tegen wanneer u een update-implementatie voor machines in een andere Azure-tenant probeert te maken:

```error
The client has permission to perform action 'Microsoft.Compute/virtualMachines/write' on scope '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Automation/automationAccounts/automationAccountName/softwareUpdateConfigurations/updateDeploymentName', however the current tenant '00000000-0000-0000-0000-000000000000' is not authorized to access linked subscription '00000000-0000-0000-0000-000000000000'.
```

### <a name="cause"></a>Oorzaak

Deze fout treedt op wanneer u een update-implementatie maakt met Azure VM's in een andere tenant die is opgenomen in een update-implementatie.

### <a name="resolution"></a>Oplossing

Gebruik de volgende tijdelijke oplossing om deze items gepland te krijgen. U de cmdlet [Nieuw-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) met de `ForUpdate` parameter gebruiken om een planning te maken. Gebruik vervolgens de cmdlet [Nieuw-AzureRmAutomationSoftwareUpdateConfiguration](/powershell/module/azurerm.automation/new-azurermautomationsoftwareupdateconfiguration
) en geef de `NonAzureComputer` machines in de andere tenant door aan de parameter. In het volgende voorbeeld ziet u hoe u dit doet:

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$s = New-AzureRmAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdate

New-AzureRmAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName $aa -Schedule $s -Windows -AzureVMResourceId $azureVMIdsW -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="scenario-unexplained-reboots"></a><a name="node-reboots"></a>Scenario: Onverklaarbare reboots

### <a name="issue"></a>Probleem

Hoewel u de optie **Opnieuw opstarten** hebt ingesteld op Nooit opnieuw **opstarten,** worden machines nog steeds opnieuw opgestart nadat updates zijn geïnstalleerd.

### <a name="cause"></a>Oorzaak

Windows Update kan worden gewijzigd door verschillende registersleutels, die elk van deze kan wijzigen reboot gedrag.

### <a name="resolution"></a>Oplossing

Controleer de registersleutels die worden vermeld onder [Automatische updates configureren door het register](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry) en de registersleutels te bewerken die worden gebruikt om opnieuw te [starten](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) om ervoor te zorgen dat uw machines correct zijn geconfigureerd.

## <a name="scenario-machine-shows-failed-to-start-in-an-update-deployment"></a><a name="failed-to-start"></a>Scenario: Machine toont 'Kan niet starten' in een update-implementatie

### <a name="issue"></a>Probleem

Een machine `Failed to start` toont een status. Wanneer u de specifieke details voor de machine bekijkt, ziet u de volgende fout:

```error
Failed to start the runbook. Check the parameters passed. RunbookName Patch-MicrosoftOMSComputer. Exception You have requested to create a runbook job on a hybrid worker group that does not exist.
```

### <a name="cause"></a>Oorzaak

Deze fout kan een van de volgende oorzaken hebben:

* De machine bestaat niet meer.
* De machine is uitgeschakeld en onbereikbaar.
* De machine heeft een probleem met de netwerkconnectiviteit en daarom is de hybride werknemer op de machine onbereikbaar.
* Er was een update van de MMA die de broncomputer-ID heeft gewijzigd.
* Uw updaterun is beperkt als u de limiet van 2000 gelijktijdige taken in een Automatiseringsaccount hebt bereikt. Elke implementatie wordt beschouwd als een taak en elke machine in een update-implementatie telt als een taak. Elke andere automatiseringstaak of update-implementatie die momenteel in uw Automatiseringsaccount wordt uitgevoerd, telt mee voor de gelijktijdige taaklimiet.

### <a name="resolution"></a>Oplossing

Gebruik indien van toepassing [dynamische groepen](../automation-update-management-groups.md) voor uw update-implementaties. En verder:

* Controleer of de machine nog steeds bestaat en bereikbaar is. 
* Als de machine niet bestaat, bewerkt u de implementatie en verwijdert u de machine.
* Bekijk de sectie [netwerkplanning](../automation-update-management.md#ports) voor een lijst met poorten en adressen die nodig zijn voor Updatebeheer en controleer vervolgens of uw machine aan deze vereisten voldoet.
* Controleer de verbinding met de hybride runbookworker met de probleemoplosser voor hybride runbookworker. Zie Problemen met [de updateagent oplossen](update-agent-issues.md)voor meer informatie over de probleemoplosser.
* Voer de volgende query uit in Log Analytics om machines te zoeken in uw omgeving waarvoor de broncomputer-id is gewijzigd. Zoek naar computers met `Computer` dezelfde waarde, maar een andere `SourceComputerId` waarde.

   ```loganalytics
   Heartbeat | where TimeGenerated > ago(30d) | distinct SourceComputerId, Computer, ComputerIP
   ```

* Nadat u de getroffen machines hebt gevonden, bewerkt u de update-implementaties die `SourceComputerId` deze machines targeten en verwijdert en voegt u deze vervolgens opnieuw toe, zodat de juiste waarde wordt weergegeven.

## <a name="scenario-updates-are-installed-without-a-deployment"></a><a name="updates-nodeployment"></a>Scenario: Updates worden geïnstalleerd zonder implementatie

### <a name="issue"></a>Probleem

Wanneer u een Windows-machine inschrijft in Updatebeheer, ziet u updates die zijn geïnstalleerd zonder implementatie.

### <a name="cause"></a>Oorzaak

In Windows worden updates automatisch geïnstalleerd zodra ze beschikbaar zijn. Dit gedrag kan verwarring veroorzaken als u geen update hebt gepland die naar de machine moet worden geïmplementeerd.

### <a name="resolution"></a>Oplossing

De `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU` registersleutel wordt standaard ingesteld `auto download and install`op een instelling van 4: .

Voor updatemanagementclients raden we u aan `auto download but do not auto install`deze sleutel in te stellen op 3: .

Zie [Automatische updates configureren](https://docs.microsoft.com/windows/deployment/update/waas-wu-settings#configure-automatic-updates)voor meer informatie.

## <a name="scenario-machine-is-already-registered-to-a-different-account"></a><a name="machine-already-registered"></a>Scenario: Machine is al geregistreerd op een ander account

### <a name="issue"></a>Probleem

U ontvangt het volgende foutbericht:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.InvalidOperationException: {"Message":"Machine is already registered to a different account."}
```

### <a name="cause"></a>Oorzaak

De machine is al aan boord van een andere werkruimte voor Update Management.

### <a name="resolution"></a>Oplossing

1. Volg de stappen onder [Machines worden niet weergegeven in de portal onder Updatebeheer](#nologs) om te zien of de machine rapporteert aan de juiste werkruimte.
2. Ruim artefacten op de machine op door [de hybride runbookgroep te verwijderen](../automation-hybrid-runbook-worker.md#remove-a-hybrid-worker-group)en probeer het vervolgens opnieuw.

## <a name="scenario-machine-cant-communicate-with-the-service"></a><a name="machine-unable-to-communicate"></a>Scenario: Machine kan niet communiceren met de service

### <a name="issue"></a>Probleem

U ontvangt een van de volgende foutmeldingen:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.Net.Http.HttpRequestException: An error occurred while sending the request. ---> System.Net.WebException: The underlying connection was closed: An unexpected error occurred on a receive. ---> System.ComponentModel.Win32Exception: The client and server can't communicate, because they do not possess a common algorithm
```

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception Newtonsoft.Json.JsonReaderException: Error parsing positive infinity value.
```

```error
The certificate presented by the service <wsid>.oms.opinsights.azure.com was not issued by a certificate authority used for Microsoft services. Contact your network administrator to see if they are running a proxy that intercepts TLS/SSL communication.
```

```error
Access is denied. (Exception form HRESULT: 0x80070005(E_ACCESSDENIED))
```

### <a name="cause"></a>Oorzaak

Een proxy, gateway of firewall blokkeert mogelijk netwerkcommunicatie. 

### <a name="resolution"></a>Oplossing

Bekijk uw netwerk en zorg ervoor dat geschikte poorten en adressen zijn toegestaan. Zie [netwerkvereisten](../automation-hybrid-runbook-worker.md#network-planning) voor een lijst met poorten en adressen die vereist zijn voor Update Management en Hybrid Runbook Workers.

## <a name="scenario-unable-to-create-self-signed-certificate"></a><a name="unable-to-create-selfsigned-cert"></a>Scenario: Kan niet zelfondertekend certificaat maken

### <a name="issue"></a>Probleem

U ontvangt een van de volgende foutmeldingen:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception AgentService.HybridRegistration. PowerShell.Certificates.CertificateCreationException: Failed to create a self-signed certificate. ---> System.UnauthorizedAccessException: Access is denied.
```

### <a name="cause"></a>Oorzaak

De hybride runbookworker kan geen zelfondertekend certificaat genereren.

### <a name="resolution"></a>Oplossing

Controleer of het systeemaccount leestoegang heeft tot de map **C:\ProgramData\Microsoft\Crypto\RSA** en probeer het opnieuw.

## <a name="scenario-the-scheduled-update-failed-with-a-maintenancewindowexceeded-error"></a><a name="mw-exceeded"></a>Scenario: De geplande update is mislukt met een fout van MaintenanceWindowExceeded

### <a name="issue"></a>Probleem

Het standaardonderhoudsvenster voor updates is 120 minuten. U het onderhoudsvenster verhogen tot maximaal 6 uur of 360 minuten.

### <a name="resolution"></a>Oplossing

Bewerk eventuele niet-geplande update-implementaties en verhoog het onderhoudsvenster.

Zie [Updates installeren](../automation-tutorial-update-management.md#schedule-an-update-deployment)voor meer informatie over onderhoudsvensters.

## <a name="scenario-machine-shows-as-not-assessed-and-shows-an-hresult-exception"></a><a name="hresult"></a>Scenario: Machine wordt weergegeven als "Niet beoordeeld" en toont een HRESULT-uitzondering

### <a name="issue"></a>Probleem

* Je hebt machines `Not assessed` die worden weergegeven als onder **Compliance,** en je ziet een uitzonderingbericht eronder.
* U ziet een HRESULT-foutcode in de portal.

### <a name="cause"></a>Oorzaak

De updateagent (Windows Update Agent op Windows; de package manager voor een Linux-distributie) is niet correct geconfigureerd. Update Management is afhankelijk van de updateagent van de machine om de updates te bieden die nodig zijn, de status van de patch en de resultaten van geïmplementeerde patches. Zonder deze informatie kan Update Management niet correct rapporteren over de patches die nodig zijn of geïnstalleerd.

### <a name="resolution"></a>Oplossing

Probeer lokaal updates uit te voeren op de machine. Als dit niet lukt, betekent dit meestal dat er een configuratiefout is met de updateagent.

Dit probleem wordt vaak veroorzaakt door netwerkconfiguratie- en firewallproblemen. Probeer het volgende:

* Controleer voor Linux de juiste documentatie om te controleren of u het netwerkeindpunt van uw pakketopslagplaats bereiken.
* Controleer voor Windows of de configuratie van uw agent zoals die in [Updates wordt vermeld, niet wordt gedownload vanaf het eindpunt van het intranet (WSUS/SCCM).](/windows/deployment/update/windows-update-troubleshooting#updates-arent-downloading-from-the-intranet-endpoint-wsussccm)
  * Als de machines zijn geconfigureerd voor Windows Update, moet u de eindpunten bereiken die zijn beschreven in [Problemen met betrekking tot HTTP/proxy.](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy)
  * Als de machines zijn geconfigureerd voor Windows Server Update Services (WSUS), controleert u of u de WSUS-server bereiken die is geconfigureerd met de [WUServer-registersleutel.](/windows/deployment/update/waas-wu-settings)

Als u een HRESULT ziet, dubbelklikt u op de uitzondering die in het rood wordt weergegeven om het volledige uitzonderingsbericht weer te geven. Bekijk de volgende tabel voor mogelijke oplossingen of aanbevolen acties:

|Uitzondering  |Oplossing of actie  |
|---------|---------|
|`Exception from HRESULT: 0x……C`     | Zoek in de lijst met relevante foutcode in [Windows-update](https://support.microsoft.com/help/938205/windows-update-error-code-list) om aanvullende details over de oorzaak van de uitzondering te vinden.        |
|`0x8024402C`</br>`0x8024401C`</br>`0x8024402F`      | Deze geven problemen met de netwerkconnectiviteit aan. Controleer of uw machine netwerkconnectiviteit heeft met Update Management. Zie de sectie [netwerkplanning](../automation-update-management.md#ports) voor een lijst met vereiste poorten en adressen.        |
|`0x8024001E`| De updatebewerking is niet voltooid omdat de service of het systeem werd afgesloten.|
|`0x8024002E`| De Windows Update-service is uitgeschakeld.|
|`0x8024402C`     | Als u een WSUS-server gebruikt, controleert `WUServer` u `WUStatusServer` of `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate` de registerwaarden voor en onder de registersleutel de juiste WSUS-server opgeven.        |
|`0x80072EE2`|Er is een probleem met de netwerkverbinding of een probleem bij het praten met een geconfigureerde WSUS-server. Controleer de WSUS-instellingen en controleer of de service toegankelijk is vanaf de client.|
|`The service cannot be started, either because it is disabled or because it has no enabled devices associated with it. (Exception from HRESULT: 0x80070422)`     | Controleer of de Windows Update-service (wuauserv) wordt uitgevoerd en niet is uitgeschakeld.        |
|`0x80070005`| Een fout met toegang geweigerd kan worden veroorzaakt door een van de volgende:<br> Geïnfecteerde computer<br> Instellingen voor Windows Update niet correct geconfigureerd<br> Fout in bestandsmachtiging met de map %WinDir%\SoftwareDistribution<br> Onvoldoende schijfruimte op het systeemstation (C:).
|Elke andere generieke uitzondering     | Voer een zoekopdracht uit op het internet naar mogelijke oplossingen en werk met uw lokale IT-ondersteuning.         |

Als u het bestand %Windir%\Windowsupdate.log bekijkt, u ook mogelijke oorzaken vaststellen. Zie [Het bestand Windowsupdate.log lezen](https://support.microsoft.com/en-ca/help/902093/how-to-read-the-windowsupdate-log-file)voor meer informatie over het lezen van het logboek.

U ook de [probleemoplosser voor Windows Update](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) downloaden en uitvoeren om te controleren op eventuele problemen met Windows Update op de machine.

> [!NOTE]
> De documentatie [over probleemoplossers voor Windows Update](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) geeft aan dat deze voor windows-clients is, maar ook op Windows Server.

## <a name="scenario-update-run-returns-failed-status-linux"></a>Scenario: Update run returns "Failed" status (Linux)

### <a name="issue"></a>Probleem

Een updaterun wordt gestart, maar er zijn fouten tijdens het uitvoeren.

### <a name="cause"></a>Oorzaak

Mogelijke oorzaken:

* Package manager is ongezond.
* Update Agent (WUA voor Windows, distro-specifieke package manager voor Linux) is verkeerd geconfigureerd.
* Specifieke pakketten verstoren cloudgebaseerde patching.
* De machine is onbereikbaar.
* Updates hadden afhankelijkheden die niet waren opgelost.

### <a name="resolution"></a>Oplossing

Als er storingen optreden tijdens een updatedie wordt uitgevoerd nadat deze is gestart, [controleert u de taakuitvoer](../manage-update-multi.md#view-results-of-an-update-deployment) van de getroffen machine in de run. Mogelijk vindt u specifieke foutmeldingen van uw machines waarop u onderzoeken en actie ondernemen. Updatebeheer vereist dat de package manager in orde is voor succesvolle update-implementaties.

Als specifieke patches, pakketten of updates worden gezien vlak voordat de taak mislukt, u proberen deze [uit te sluiten](../automation-tutorial-update-management.md#schedule-an-update-deployment) van de volgende update-implementatie. Zie [Windows Update-logboekbestanden](/windows/deployment/update/windows-update-logs)voor het verzamelen van logboekgegevens uit Windows Update.

Als u een patchprobleem niet oplossen, maakt u een kopie van het volgende logboekbestand en bewaart u het voor probleemoplossingsdoeleinden voordat de volgende update-implementatie wordt gestart:

```bash
/var/opt/microsoft/omsagent/run/automationworker/omsupdatemgmt.log
```

## <a name="patches-arent-installed"></a>Patches zijn niet geïnstalleerd

### <a name="machines-dont-install-updates"></a>Updates worden niet geïnstalleerd op machines

Voer de updates rechtstreeks op de machine uit. Als de machine de updates niet kan toepassen, raadpleegt u de [lijst met mogelijke fouten in de handleiding voor probleemoplossing.](https://docs.microsoft.com/azure/automation/troubleshoot/update-management#hresult)

Als updates lokaal worden uitgevoerd, probeert u de agent op de machine te verwijderen en opnieuw te installeren door de richtlijnen te volgen bij [Een VM verwijderen uit Updatebeheer](https://docs.microsoft.com/azure/automation/automation-onboard-solutions-from-browse#clean-up-resources).

### <a name="i-know-updates-are-available-but-they-dont-show-as-available-on-my-machines"></a>Ik weet dat er updates beschikbaar zijn, maar ze worden niet weergegeven zoals beschikbaar op mijn machines

Dit gebeurt vaak als machines zijn geconfigureerd om updates te ontvangen van WSUS of Microsoft Endpoint Configuration Manager, maar WSUS en Configuration Manager de updates niet hebben goedgekeurd.

U controleren of de machines zijn geconfigureerd voor WSUS en SCCM door de `UseWUServer` registersleutel te vergelijken met de registersleutels in de sectie Automatische updates configureren door de sectie Register van dit artikel te [bewerken.](https://support.microsoft.com/help/328010/how-to-configure-automatic-updates-by-using-group-policy-or-registry-s)

Als updates niet zijn goedgekeurd in WSUS, worden ze niet geïnstalleerd. U controleren op niet-goedgekeurde updates in Log Analytics door de volgende query uit te voeren.

  ```loganalytics
  Update | where UpdateState == "Needed" and ApprovalSource == "WSUS" and Approved == "False" | summarize max(TimeGenerated) by Computer, KBID, Title
  ```

### <a name="updates-show-as-installed-but-i-cant-find-them-on-my-machine"></a>Updates verschijnen na installatie maar ik kan ze niet vinden op mijn machine

Updates worden vaak verdrongen door andere updates. Zie [Bijwerken wordt vervangen](https://docs.microsoft.com/windows/deployment/update/windows-update-troubleshooting#the-update-is-not-applicable-to-your-computer) in de handleiding voor probleemoplossing voor Windows Update voor meer informatie.

### <a name="installing-updates-by-classification-on-linux"></a>Updates per classificatie installeren op Linux

Het per classificatie implementeren van updates naar Linux ('Essentiële en beveiligingsupdates'), heeft belangrijke beperkingen, met name voor CentOS. Deze beperkingen zijn gedocumenteerd op de [overzichtspagina Updatebeheer](https://docs.microsoft.com/azure/automation/automation-update-management#linux-2).

### <a name="kb2267602-is-consistently-missing"></a>KB2267602 ontbreekt consequent

KB2267602 is de [definitie-update voor Windows Defender](https://www.microsoft.com/wdsi/definitions). Het wordt dagelijks bijgewerkt.

## <a name="next-steps"></a>Volgende stappen

Als je je probleem niet hebt gezien of het probleem niet oplossen, kun je een van de volgende kanalen proberen voor extra ondersteuning:

* Krijg antwoorden van Azure-experts via [Azure Forums.](https://azure.microsoft.com/support/forums/)
* Maak [@AzureSupport](https://twitter.com/azuresupport)verbinding met het officiële Microsoft Azure-account voor het verbeteren van de klantervaring door de Azure-community te verbinden met de juiste bronnen: antwoorden, ondersteuning en experts.
* Een Azure-ondersteuningsincident indienen. Ga naar de [Azure-ondersteuningssite](https://azure.microsoft.com/support/options/) en selecteer **Ondersteuning opdoen**.
