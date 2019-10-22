---
title: Fouten oplossen met Updatebeheer
description: Meer informatie over het oplossen van problemen met Updatebeheer.
services: automation
author: bobbytreed
ms.author: robreed
ms.date: 05/31/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 952bcb85484e885d45876de1e4cf3326db0a146a
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693392"
---
# <a name="troubleshooting-issues-with-update-management"></a>Problemen met Updatebeheer oplossen

In dit artikel worden oplossingen beschreven voor problemen die u kunt tegen komen wanneer u Updatebeheer gebruikt.

Er is een agent probleem oplosser voor de Hybrid Worker-agent om het onderliggende probleem te bepalen. Zie problemen [met Update agent oplossen](update-agent-issues.md)voor meer informatie over de probleem Oplosser. Gebruik de volgende richt lijnen voor probleem oplossing voor alle andere problemen.

Als u problemen ondervindt tijdens het uitvoeren van de oplossing op een virtuele machine (VM), raadpleegt u het **Operations Manager** logboek onder **toepassing en services-logboeken** op de lokale computer voor gebeurtenissen met gebeurtenis-id 4502 en gebeurtenis details die bevatten **Microsoft. EnterpriseManagement. HealthService. AzureAutomation. HybridAgent**.

In de volgende sectie worden specifieke fout berichten en mogelijke oplossingen voor elk weer gemarkeerd. Zie [problemen oplossen met onboarding van oplossingen](onboarding.md)voor andere voorbereidings problemen.

## <a name="nologs"></a>Scenario: computers worden niet weer gegeven in de portal onder Updatebeheer

### <a name="issue"></a>Probleem

De volgende symptomen treden op:

* Uw computer laat zien dat deze **niet is geconfigureerd** in de updatebeheer weergave van een virtuele machine.

* Uw computers ontbreken in de Updatebeheer weer gave van uw Azure Automation-account.

* U hebt computers die worden weer gegeven als **niet beoordeeld** onder **naleving**. U ziet echter heartbeat-gegevens in Azure Monitor logboeken voor de Hybrid Runbook Worker maar niet voor Updatebeheer.

### <a name="cause"></a>Oorzaak

Dit probleem kan worden veroorzaakt door lokale configuratie problemen of door een onjuist geconfigureerde Scope configuratie.

Mogelijk moet u de Hybrid Runbook Worker opnieuw registreren en opnieuw installeren.

U hebt mogelijk een quotum gedefinieerd in uw werk ruimte die is bereikt en waardoor verdere gegevens opslag wordt voor komen.

### <a name="resolution"></a>Resolutie

* Voer de probleem oplosser voor [Windows](update-agent-issues.md#troubleshoot-offline) of [Linux](update-agent-issues-linux.md#troubleshoot-offline)uit, afhankelijk van het besturings systeem.

* Zorg ervoor dat uw computer rapporteert aan de juiste werk ruimte. Zie [agent connectiviteit controleren op Log Analytics](../../azure-monitor/platform/agent-windows.md#verify-agent-connectivity-to-log-analytics)voor meer informatie over hoe u dit aspect kunt controleren. Zorg er ook voor dat deze werk ruimte is gekoppeld aan uw Azure Automation-account. Als u wilt bevestigen, gaat u naar uw Automation-account en selecteert u **gekoppelde werk ruimte** onder **gerelateerde resources**.

* Zorg ervoor dat de computers in uw Log Analytics-werk ruimte worden weer gegeven. Voer de volgende query uit in de Log Analytics-werk ruimte die is gekoppeld aan uw Automation-account:

  ```loganalytics
  Heartbeat
  | summarize by Computer, Solutions
  ```
  Als uw computer niet in de query resultaten wordt weer geven, is deze niet recent ingecheckt. Dit betekent dat er waarschijnlijk een probleem is met de lokale configuratie en dat u [de agent opnieuw moet installeren](../../azure-monitor/learn/quick-collect-windows-computer.md#install-the-agent-for-windows). Als uw computer in de query resultaten wordt weer gegeven, moet u de scope configuratie controleren die is opgegeven in het volgende item in de lijst met opsommings tekens.

* Controleren op problemen met de scope configuratie. De [Scope configuratie](../automation-onboard-solutions-from-automation-account.md#scope-configuration) bepaalt welke computers voor de oplossing worden geconfigureerd. Als uw machine wordt weer gegeven in uw werk ruimte, maar niet in de **updatebeheer** Portal, moet u de scope configuratie configureren om de computers te richten. Zie voor meer informatie over hoe u dit doet, [de onboarding-computers in de werk ruimte](../automation-onboard-solutions-from-automation-account.md#onboard-machines-in-the-workspace).

* Voer de volgende query uit in uw werk ruimte:

  ```loganalytics
  Operation
  | where OperationCategory == 'Data Collection Status'
  | sort by TimeGenerated desc
  ```
  Als er een `Data collection stopped due to daily limit of free data reached. Ingestion status = OverQuota` resultaat wordt weer gegeven, is er een quotum gedefinieerd in uw werk ruimte en is het niet meer mogelijk om gegevens op te slaan. Ga in uw werk ruimte naar **verbruik en geschatte kosten**  > **gegevens volume beheer** en controleer het quotum of verwijder dit.

* Als u met deze stappen het probleem niet kunt oplossen, volgt u de stappen in [een Windows-Hybrid Runbook worker implementeren](../automation-windows-hrw-install.md) om de Hybrid worker voor Windows opnieuw te installeren. Of [Implementeer een Linux-Hybrid Runbook worker](../automation-linux-hrw-install.md)voor Linux.

## <a name="rp-register"></a>Scenario: kan Automation-resource provider niet registreren voor abonnementen

### <a name="issue"></a>Probleem

Wanneer u werkt met oplossingen in uw Automation-account, treedt de volgende fout op:

```error
Error details: Unable to register Automation Resource Provider for subscriptions:
```

### <a name="cause"></a>Oorzaak

De resource provider voor Automation is niet geregistreerd in het abonnement.

### <a name="resolution"></a>Resolutie

Voer de volgende stappen uit in de Azure Portal om de resource provider Automation te registreren:

1. Selecteer in de lijst van de Azure-service aan de onderkant van de portal **alle services**en selecteer vervolgens **abonnementen** in de algemene service groep.
2. Selecteer uw abonnement.
3. Onder **instellingen**selecteert u **resource providers**.
4. Controleer in de lijst met resource providers of de resource provider **micro soft. Automation** is geregistreerd.
5. Als dit niet het geval is, registreert u de provider van **micro soft. Automation** door de stappen te volgen om [fouten bij de registratie van de resource provider op te lossen](/azure/azure-resource-manager/resource-manager-register-provider-errors).

## <a name="components-enabled-not-working"></a>Scenario: de onderdelen voor de Updatebeheer-oplossing zijn ingeschakeld en nu wordt deze virtuele machine geconfigureerd

### <a name="issue"></a>Probleem

Het volgende bericht wordt op een virtuele machine 15 minuten na de onboarding weer gegeven:

```error
The components for the 'Update Management' solution have been enabled, and now this virtual machine is being configured. Please be patient, as this can sometimes take up to 15 minutes.
```

### <a name="cause"></a>Oorzaak

Deze fout kan de volgende oorzaken hebben:

- De communicatie met het Automation-account wordt geblokkeerd.
- De virtuele machine die wordt vrijgegeven, kan afkomstig zijn van een gekloonde computer die niet is Sysprep met micro soft Monitoring Agent (MMA) geïnstalleerd.

### <a name="resolution"></a>Resolutie

1. Ga naar [netwerk planning](../automation-hybrid-runbook-worker.md#network-planning) voor meer informatie over welke adressen en poorten moeten worden toegestaan om updatebeheer te kunnen werken.
2. Als u een gekloonde installatie kopie gebruikt:
   1. In uw Log Analytics-werk ruimte verwijdert u de virtuele machine uit de opgeslagen zoek opdracht voor de `MicrosoftDefaultScopeConfig-Updates` Scope configuratie als deze wordt weer gegeven. U kunt opgeslagen Zoek opdrachten vinden onder **Algemeen** in uw werk ruimte.
   2. Voer `Remove-Item -Path "HKLM:\software\microsoft\hybridrunbookworker" -Recurse -Force` uit.
   3. Voer `Restart-Service HealthService` uit om de `HealthService` opnieuw te starten. Hiermee wordt de sleutel opnieuw gemaakt en wordt een nieuwe UUID gegenereerd.
   4. Als deze aanpak niet werkt, voert u eerst Sysprep uit op de installatie kopie en installeert u vervolgens de MMA.

## <a name="multi-tenant"></a>Scenario: er wordt een fout bij een gekoppeld abonnement weer gegeven wanneer u een update-implementatie maakt voor computers in een andere Azure-Tenant

### <a name="issue"></a>Probleem

U ontvangt de volgende fout melding wanneer u probeert een update-implementatie te maken voor computers in een andere Azure-Tenant:

```error
The client has permission to perform action 'Microsoft.Compute/virtualMachines/write' on scope '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Automation/automationAccounts/automationAccountName/softwareUpdateConfigurations/updateDeploymentName', however the current tenant '00000000-0000-0000-0000-000000000000' is not authorized to access linked subscription '00000000-0000-0000-0000-000000000000'.
```

### <a name="cause"></a>Oorzaak

Deze fout treedt op wanneer u een update-implementatie maakt met Azure-Vm's in een andere Tenant die is opgenomen in een update-implementatie.

### <a name="resolution"></a>Resolutie

Gebruik de volgende tijdelijke oplossing om deze items te laten plannen. U kunt de cmdlet [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) gebruiken met de schakel optie `-ForUpdate` om een planning te maken. Vervolgens gebruikt u de cmdlet [New-AzureRmAutomationSoftwareUpdateConfiguration](/powershell/module/azurerm.automation/new-azurermautomationsoftwareupdateconfiguration
) en geeft u de computers in de andere Tenant door aan de para meter `-NonAzureComputer`. In het volgende voor beeld ziet u hoe u dit doet:

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$s = New-AzureRmAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdate

New-AzureRmAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName $aa -Schedule $s -Windows -AzureVMResourceId $azureVMIdsW -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="node-reboots"></a>Scenario: niet-uitleg bij opnieuw opstarten

### <a name="issue"></a>Probleem

Hoewel u de optie voor het **opnieuw** opstarten van het systeem hebt ingesteld op **nooit opnieuw opstarten**, worden machines nog steeds opnieuw opgestart nadat updates zijn geïnstalleerd.

### <a name="cause"></a>Oorzaak

Windows Update kunnen worden gewijzigd door verschillende register sleutels, waarmee u het gedrag voor opnieuw opstarten kunt wijzigen.

### <a name="resolution"></a>Resolutie

Controleer de register sleutels die worden vermeld onder [Automatische updates configureren door het REGI ster en de](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-rej7uijui7jgistry) [register sleutels te bewerken die worden gebruikt voor het beheren van de herstart](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) om ervoor te zorgen dat uw computers correct zijn geconfigureerd.

## <a name="failed-to-start"></a>Scenario: in een update-implementatie wordt weer gegeven dat het starten van de computer is mislukt

### <a name="issue"></a>Probleem

De status van een computer **kan niet worden gestart** . Wanneer u de specifieke Details voor de computer bekijkt, ziet u de volgende fout:

```error
Failed to start the runbook. Check the parameters passed. RunbookName Patch-MicrosoftOMSComputer. Exception You have requested to create a runbook job on a hybrid worker group that does not exist.
```

### <a name="cause"></a>Oorzaak

Deze fout kan een van de volgende oorzaken hebben:

* De machine bestaat niet meer.
* De computer is uitgeschakeld en kan niet worden bereikt.
* De computer heeft een probleem met de netwerk verbinding en daarom is de Hybrid worker op de computer onbereikbaar.
* Er is een update voor de MMA die de SourceComputerId heeft gewijzigd.
* De update-uitvoering is beperkt als u de limiet van 2.000 gelijktijdige taken in een Automation-account bereikt. Elke implementatie wordt beschouwd als een taak en elke computer in een update-implementatie telt als een taak. Alle andere Automation-taken of update-implementaties die momenteel worden uitgevoerd in uw Automation-account, tellen mee voor de limiet voor gelijktijdige taken.

### <a name="resolution"></a>Resolutie

Gebruik, indien van toepassing, [dynamische groepen](../automation-update-management-groups.md) voor uw update-implementaties. Let

* Controleer of de computer nog bestaat en bereikbaar is. Als deze niet bestaat, bewerkt u de implementatie en verwijdert u de computer.
* Zie de sectie [netwerk planning](../automation-update-management.md#ports) voor een lijst met poorten en adressen die vereist zijn voor updatebeheer en controleer vervolgens of uw computer aan deze vereisten voldoet.
* Voer de volgende query uit in Log Analytics om computers in uw omgeving te zoeken waarvan de `SourceComputerId` is gewijzigd. Zoek naar computers die dezelfde `Computer` waarde hebben, maar een andere `SourceComputerId` waarde. 

   ```loganalytics
   Heartbeat | where TimeGenerated > ago(30d) | distinct SourceComputerId, Computer, ComputerIP
   ```
   Nadat u de betrokken computers hebt gevonden, bewerkt u de update-implementaties die zijn gericht op die computers en vervolgens verwijdert u deze en voegt u deze opnieuw toe zodat `SourceComputerId` de juiste waarde weergeeft.

## <a name="updates-nodeployment"></a>Scenario: updates worden geïnstalleerd zonder een implementatie

### <a name="issue"></a>Probleem

Wanneer u een Windows-computer registreert in Updatebeheer, ziet u dat er updates zijn geïnstalleerd zonder een implementatie.

### <a name="cause"></a>Oorzaak

In Windows worden updates automatisch geïnstalleerd zodra ze beschikbaar zijn. Dit gedrag kan leiden tot Verwar ring als u geen update hebt gepland voor implementatie op de computer.

### <a name="resolution"></a>Resolutie

De register sleutel `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU` is standaard ingesteld op 4: **automatisch downloaden en installeren**.

Voor Updatebeheer-clients wordt aangeraden om deze sleutel in te stellen op 3: **automatisch downloaden, maar niet automatisch installeren**.

Zie [Automatische updates configureren](https://docs.microsoft.com/windows/deployment/update/waas-wu-settings#configure-automatic-updates)voor meer informatie.

## <a name="machine-already-registered"></a>Scenario: de computer is al geregistreerd voor een ander account

### <a name="issue"></a>Probleem

Het volgende fout bericht wordt weer gegeven:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.InvalidOperationException: {"Message":"Machine is already registered to a different account."}
```

### <a name="cause"></a>Oorzaak

De machine is al onboarded naar een andere werk ruimte voor Updatebeheer.

### <a name="resolution"></a>Resolutie

1. Volg de stappen onder [machines die niet worden weer gegeven in de portal onder updatebeheer](#nologs) om ervoor te zorgen dat de computer aan de juiste werk ruimte rapporteert.
2. Verwijder de oude artefacten op de machine door [de Hybrid runbook Group te verwijderen](../automation-hybrid-runbook-worker.md#remove-a-hybrid-worker-group)en probeer het opnieuw.

## <a name="machine-unable-to-communicate"></a>Scenario: de computer kan niet communiceren met de service

### <a name="issue"></a>Probleem

U ontvangt een van de volgende fout berichten:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.Net.Http.HttpRequestException: An error occurred while sending the request. ---> System.Net.WebException: The underlying connection was closed: An unexpected error occurred on a receive. ---> System.ComponentModel.Win32Exception: The client and server can't communicate, because they do not possess a common algorithm
```

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception Newtonsoft.Json.JsonReaderException: Error parsing positive infinity value.
```

```error
The certificate presented by the service <wsid>.oms.opinsights.azure.com was not issued by a certificate authority used for Microsoft services. Contact your network administrator to see if they are running a proxy that intercepts TLS/SSL communication.
```

### <a name="cause"></a>Oorzaak

Een proxy, gateway of firewall blokkeert mogelijk netwerk communicatie.

### <a name="resolution"></a>Resolutie

Controleer uw netwerk en zorg ervoor dat de juiste poorten en adressen zijn toegestaan. Zie [netwerk vereisten](../automation-hybrid-runbook-worker.md#network-planning) voor een lijst met poorten en adressen die vereist zijn voor updatebeheer-en Hybrid Runbook-werk rollen.

## <a name="unable-to-create-selfsigned-cert"></a>Scenario: kan geen zelfondertekend certificaat maken

### <a name="issue"></a>Probleem

U ontvangt een van de volgende fout berichten:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception AgentService.HybridRegistration. PowerShell.Certificates.CertificateCreationException: Failed to create a self-signed certificate. ---> System.UnauthorizedAccessException: Access is denied.
```

### <a name="cause"></a>Oorzaak

Het Hybrid Runbook Worker kan geen zelfondertekend certificaat genereren.

### <a name="resolution"></a>Resolutie

Controleer of het systeem account lees toegang heeft tot de map **C:\ProgramData\Microsoft\Crypto\RSA** en probeer het opnieuw.

## <a name="mw-exceeded"></a>Scenario: de geplande update is mislukt met een MaintenanceWindowExceeded-fout

### <a name="issue"></a>Probleem

Het standaard onderhouds venster voor updates is 120 minuten. U kunt het onderhouds venster tot Maxi maal 6 uur of 360 minuten verhogen.

### <a name="resolution"></a>Resolutie

Bewerk eventuele mislukte geplande update-implementaties en verg root het onderhouds venster.

Zie [updates installeren](../automation-tutorial-update-management.md#schedule-an-update-deployment)voor meer informatie over onderhouds Vensters.

## <a name="hresult"></a>Scenario: de machine wordt weer gegeven als ' niet beoordeeld ' en toont een HResult-uitzonde ring

### <a name="issue"></a>Probleem

* U hebt computers die worden weer gegeven als **niet beoordeeld** onder **naleving**en u ziet een uitzonderings bericht hieronder.
* U hebt computers die als niet-beoordeeld worden weer gegeven.
* Er wordt een HRESULT-fout code weer geven in de portal.

### <a name="cause"></a>Oorzaak

De Update Agent (Windows Update-agent in Windows; de Package Manager voor een Linux-distributie) is niet correct geconfigureerd. Updatebeheer is afhankelijk van de Update Agent van de machine om de benodigde updates, de status van de patch en de resultaten van geïmplementeerde patches op te geven. Zonder deze informatie kan Updatebeheer niet op de juiste manier rapporteren over de benodigde patches of geïnstalleerd.

### <a name="resolution"></a>Resolutie

Probeer updates lokaal op de computer uit te voeren. Als dit mislukt, betekent dit doorgaans dat er een configuratie fout is opgetreden bij de Update-Agent.

Dit probleem wordt vaak veroorzaakt door netwerk configuratie-en Firewall problemen. Probeer het volgende:

* Raadpleeg voor Linux de juiste documentatie om ervoor te zorgen dat u het netwerk eindpunt van uw pakket opslagplaats kunt bereiken.
* Controleer voor Windows of uw agent configuratie wordt weer gegeven in [updates die niet worden gedownload vanaf het intranet eindpunt (WSUS/SCCM)](/windows/deployment/update/windows-update-troubleshooting#updates-arent-downloading-from-the-intranet-endpoint-wsussccm).
  * Als de computers zijn geconfigureerd voor Windows Update, moet u ervoor zorgen dat u de eind punten kunt bereiken [die worden beschreven in problemen die betrekking hebben op http/proxy](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy).
  * Als de computers zijn geconfigureerd voor Windows Server Update Services (WSUS), moet u ervoor zorgen dat u de WSUS-server kunt bereiken die is geconfigureerd met de [register sleutel WUServer](/windows/deployment/update/waas-wu-settings).

Als u een HRESULT ziet, dubbelklikt u op de uitzonde ring die rood wordt weer gegeven om het volledige uitzonderings bericht weer te geven. Raadpleeg de volgende tabel voor mogelijke oplossingen of aanbevolen acties:

|Uitzondering  |Oplossing of actie  |
|---------|---------|
|`Exception from HRESULT: 0x……C`     | Zoek in de [lijst met fout codes voor Windows Update](https://support.microsoft.com/help/938205/windows-update-error-code-list) naar aanvullende informatie over de oorzaak van de uitzonde ring.        |
|`0x8024402C`</br>`0x8024401C`</br>`0x8024402F`      | Deze duiden op problemen met de netwerk verbinding. Zorg ervoor dat uw computer netwerk verbinding heeft met Updatebeheer. Zie de sectie [netwerk planning](../automation-update-management.md#ports) voor een lijst met vereiste poorten en adressen.        |
|`0x8024001E`| De update bewerking is niet voltooid omdat de service of het systeem is afgesloten.|
|`0x8024002E`| Windows Update-service is uitgeschakeld.|
|`0x8024402C`     | Als u een WSUS-server gebruikt, moet u ervoor zorgen dat de register waarden voor `WUServer` en `WUStatusServer` onder de register sleutel `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate` de juiste WSUS-server opgeven.        |
|`0x80072EE2`|Er is een probleem met de netwerk verbinding of een probleem in het praten met een geconfigureerde WSUS-server. Controleer de WSUS-instellingen en controleer of de service toegankelijk is vanaf de client.|
|`The service cannot be started, either because it is disabled or because it has no enabled devices associated with it. (Exception from HRESULT: 0x80070422)`     | Zorg ervoor dat de Windows Update-service (wuauserv) wordt uitgevoerd en niet is uitgeschakeld.        |
|Een andere algemene uitzonde ring     | Voer een zoek opdracht op Internet uit voor mogelijke oplossingen en werk samen met uw lokale IT-ondersteuning.         |

Door het bestand WindowsUpdate. log te bekijken, kunt u ook mogelijke oorzaken bepalen. Zie [het bestand WindowsUpdate. log lezen](https://support.microsoft.com/en-ca/help/902093/how-to-read-the-windowsupdate-log-file)voor meer informatie over het lezen van het logboek.

U kunt de [Windows Update probleemoplossings](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) programma ook downloaden en uitvoeren om te controleren of er problemen zijn met Windows Update op de computer.

> [!NOTE]
> De documentatie van de [Windows Update-probleemoplossings](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) functie geeft aan dat deze is voor gebruik op Windows-clients, maar ook werkt op Windows Server.

## <a name="scenario-update-run-returns-failed-status"></a>Scenario: de update-uitvoering retourneert de status failed

### <a name="issue"></a>Probleem

Er wordt een update-uitvoering gestart, maar er zijn fouten opgetreden tijdens de uitvoering.

### <a name="cause"></a>Oorzaak

Mogelijke oorzaken:

* Pakket beheer is beschadigd.
* Update Agent (WUA voor Windows, distributie pakket beheer voor Linux) is onjuist geconfigureerd.
* Specifieke pakketten zijn van invloed op cloud-gebaseerde patches.
* De computer is onbereikbaar.
* Updates bevatten afhankelijkheden die niet zijn opgelost.

### <a name="resolution"></a>Resolutie

Als er fouten optreden tijdens het uitvoeren van een update nadat deze is gestart, [controleert u de taak uitvoer](../manage-update-multi.md#view-results-of-an-update-deployment) van de betrokken computer in de uitvoering. Mogelijk vindt u specifieke fout berichten van uw computers die u kunt onderzoeken en actie onderneemt. Voor Updatebeheer moet pakket beheer in orde zijn voor geslaagde update-implementaties.

Als specifieke patches, pakketten of updates onmiddellijk worden weer gegeven voordat de taak mislukt, kunt u proberen deze uit te [sluiten](../automation-tutorial-update-management.md#schedule-an-update-deployment) van de volgende update-implementatie. Zie [Windows Update-logboek bestanden](/windows/deployment/update/windows-update-logs)voor informatie over het verzamelen van logboek gegevens van Windows Update.

Als u een probleem met een patches niet kunt oplossen, maakt u een kopie van het volgende logboek bestand en behoudt u dit voor het oplossen van problemen *voordat* de volgende update-implementatie wordt gestart:

```bash
/var/opt/microsoft/omsagent/run/automationworker/omsupdatemgmt.log
```

## <a name="patches-arent-installed"></a>Patches zijn niet geïnstalleerd

### <a name="machines-dont-install-updates"></a>Computers installeren geen updates

* Voer de updates rechtstreeks op de machine uit. Als de computer de updates niet kan Toep assen, raadpleegt u de [lijst met mogelijke fouten in de hand leiding voor het oplossen van problemen](https://docs.microsoft.com/azure/automation/troubleshoot/update-management#hresult).
* Als updates lokaal worden uitgevoerd, kunt u de agent op de machine verwijderen en opnieuw installeren door de richt lijnen te volgen in [een VM verwijderen uit updatebeheer](https://docs.microsoft.com/azure/automation/automation-update-management#remove-a-vm-from-update-management).

### <a name="i-know-updates-are-available-but-they-dont-show-as-available-on-my-machines"></a>Ik weet dat er updates beschikbaar zijn, maar ze worden niet weer gegeven als beschikbaar op mijn computers

* Dit gebeurt vaak als machines zijn geconfigureerd voor het ophalen van updates van WSUS of System Center Configuration Manager (SCCM), maar WSUS en SCCM hebben de updates niet goedgekeurd.
* U kunt controleren of de computers zijn geconfigureerd voor WSUS en SCCM door [kruis verwijzingen te maken van de register sleutel UseWUServer naar de register sleutels in de sectie ' configuratie automatische updates door het REGI ster te bewerken ' in dit artikel](https://support.microsoft.com/help/328010/how-to-configure-automatic-updates-by-using-group-policy-or-registry-s).
* Als updates niet zijn goedgekeurd in WSUS, worden ze niet geïnstalleerd. U kunt controleren op niet-goedgekeurde updates in Log Analytics door de volgende query uit te voeren:

  ```loganalytics
  Update | where UpdateState == "Needed" and ApprovalSource == "WSUS" and Approved == "False" | summarize max(TimeGenerated) by Computer, KBID, Title
  ```

### <a name="updates-show-as-installed-but-i-cant-find-them-on-my-machine"></a>Updates worden weer gegeven als geïnstalleerd, maar ze kunnen niet worden gevonden op mijn machine

* Updates worden vaak verdrongen door andere updates. Zie voor meer informatie [update is vervangen](https://docs.microsoft.com/windows/deployment/update/windows-update-troubleshooting#the-update-is-not-applicable-to-your-computer) in de Windows Update probleemoplossings handleiding.

### <a name="installing-updates-by-classification-on-linux"></a>Updates per classificatie installeren op Linux

* Het per classificatie implementeren van updates naar Linux ('Essentiële en beveiligingsupdates'), heeft belangrijke beperkingen, met name voor CentOS. Deze beperkingen worden beschreven op de [pagina overzicht van updatebeheer](https://docs.microsoft.com/azure/automation/automation-update-management#linux-2).

### <a name="kb2267602-is-consistently-missing"></a>KB2267602 ontbreekt consistent

* KB2267602 is de [definitie-update voor Windows Defender](https://www.microsoft.com/wdsi/definitions). Het wordt dagelijks bijgewerkt.

## <a name="next-steps"></a>Volgende stappen

Als u het probleem niet hebt zien of als u het probleem niet kunt oplossen, kunt u een van de volgende kanalen proberen voor aanvullende ondersteuning:

* Krijg antwoorden van Azure-experts via [Azure-forums](https://azure.microsoft.com/support/forums/).
* Maak verbinding met [@AzureSupport](https://twitter.com/azuresupport), het officiële Microsoft Azure account voor het verbeteren van de gebruikers ervaring door de Azure-community te verbinden met de juiste resources: antwoorden, ondersteuning en experts.
* Een ondersteunings incident voor Azure. Ga naar de [ondersteunings site van Azure](https://azure.microsoft.com/support/options/) en selecteer **ondersteuning verkrijgen**.
