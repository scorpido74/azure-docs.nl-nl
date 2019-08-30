---
title: Fouten oplossen met Updatebeheer
description: Meer informatie over het oplossen van problemen met Updatebeheer
services: automation
author: bobbytreed
ms.author: robreed
ms.date: 05/31/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 48d2463eee2caeaae36118bf736d00eed84c897a
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/30/2019
ms.locfileid: "70186224"
---
# <a name="troubleshooting-issues-with-update-management"></a>Problemen met Updatebeheer oplossen

In dit artikel worden oplossingen beschreven om problemen op te lossen die u kunt uitvoeren wanneer u Updatebeheer gebruikt.

Er is een agent probleem oplosser voor Hybrid Worker-agent om het onderliggende probleem te bepalen. Zie problemen [met Update agent oplossen](update-agent-issues.md)voor meer informatie over de probleem Oplosser. Zie de gedetailleerde informatie hieronder over mogelijke problemen voor alle andere problemen.

## <a name="general"></a>Algemeen

### <a name="rp-register"></a>Omstandigheden Kan Automation-resource provider niet registreren voor abonnementen

#### <a name="issue"></a>Probleem

Mogelijk wordt de volgende fout weer gegeven wanneer u werkt met oplossingen in uw Automation-account.

```error
Error details: Unable to register Automation Resource Provider for subscriptions:
```

#### <a name="cause"></a>Oorzaak

De resource provider voor Automation is niet geregistreerd in het abonnement.

#### <a name="resolution"></a>Oplossing

U kunt de resource providers voor automatisering registreren door de volgende stappen uit te voeren in de Azure Portal:

1. Klik op **alle services** onder de lijst van de Azure-service en selecteer vervolgens **abonnementen** in de _algemene_ service groep.
2. Selecteer uw abonnement.
3. Klik op **bron providers** onder _instellingen_.
4. Controleer in de lijst met resource providers of de resource provider **micro soft. Automation** is geregistreerd.
5. Als de provider niet wordt weer gegeven, registreert u de provider **micro soft.** Automation [ ](/azure/azure-resource-manager/resource-manager-register-provider-errors)met de stappen die worden vermeld onder.

### <a name="mw-exceeded"></a>Omstandigheden Het geplande update beheer is mislukt met de fout MaintenanceWindowExceeded

#### <a name="issue"></a>Probleem

Het standaard onderhouds venster voor updates is 120 minuten. U kunt het onderhouds venster verg Roten tot een maximum van zes (6) uur of 360 minuten.

#### <a name="resolution"></a>Oplossing

Bewerk eventuele mislukte geplande update-implementaties en verg root het onderhouds venster.

Zie [updates installeren](../automation-update-management.md#install-updates)voor meer informatie over onderhouds Vensters.

### <a name="components-enabled-not-working"></a>Omstandigheden De onderdelen voor de oplossing ' Updatebeheer ' zijn ingeschakeld en nu wordt deze virtuele machine geconfigureerd

#### <a name="issue"></a>Probleem

Het volgende bericht wordt op een virtuele machine 15 minuten na de onboarding weer gegeven:

```error
The components for the 'Update Management' solution have been enabled, and now this virtual machine is being configured. Please be patient, as this can sometimes take up to 15 minutes.
```

#### <a name="cause"></a>Oorzaak

Deze fout kan de volgende oorzaken hebben:

1. De communicatie met het Automation-account wordt geblokkeerd.
2. De virtuele machine die wordt uitgevoerd, kan afkomstig zijn van een gekloonde computer die niet is Sysprep met micro soft monitoring agent geïnstalleerd.

#### <a name="resolution"></a>Oplossing

1. Bezoek, [netwerk planning](../automation-hybrid-runbook-worker.md#network-planning) voor meer informatie over welke adressen en poorten moeten worden toegestaan om updatebeheer te kunnen werken.
2. Als u een gekloonde installatie kopie gebruikt:
   1. Verwijder in uw log Analytics-werk ruimte de virtuele machine uit de opgeslagen zoek opdracht voor `MicrosoftDefaultScopeConfig-Updates` de scope configuratie als deze wordt weer gegeven. U kunt opgeslagen Zoek opdrachten vinden onder **Algemeen** in uw werk ruimte.
   2. Voer `Remove-Item -Path "HKLM:\software\microsoft\hybridrunbookworker" -Recurse -Force` uit.
   3. Voer `Restart-Service HealthService` uit om de `HealthService`te starten. Hiermee maakt u de sleutel opnieuw en wordt een nieuwe UUID gegenereerd.
   4. Als dit niet werkt, Sysprep de installatie kopie eerst en installeert u de MMA-agent na het feit.

### <a name="multi-tenant"></a>Omstandigheden U ontvangt een fout bij een gekoppeld abonnement bij het maken van een update-implementatie voor computers in een andere Azure-Tenant.

#### <a name="issue"></a>Probleem

U ontvangt de volgende fout bij het maken van een update-implementatie voor computers in een andere Azure-Tenant:

```error
The client has permission to perform action 'Microsoft.Compute/virtualMachines/write' on scope '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Automation/automationAccounts/automationAccountName/softwareUpdateConfigurations/updateDeploymentName', however the current tenant '00000000-0000-0000-0000-000000000000' is not authorized to access linked subscription '00000000-0000-0000-0000-000000000000'.
```

#### <a name="cause"></a>Oorzaak

Deze fout treedt op wanneer u een update-implementatie maakt met Azure virtual machines in een andere Tenant die is opgenomen in een update-implementatie.

#### <a name="resolution"></a>Oplossing

U moet de volgende tijdelijke oplossing gebruiken om ze te laten plannen. U kunt de cmdlet [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) gebruiken met de switch `-ForUpdate` om een planning te maken en de [cmdlet New-AzureRmAutomationSoftwareUpdateConfiguration](/powershell/module/azurerm.automation/new-azurermautomationsoftwareupdateconfiguration
) te gebruiken en de computers in de andere op te geven Tenant met de `-NonAzureComputer` para meter. In het volgende voor beeld ziet u een voor beeld van hoe u dit doet:

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$s = New-AzureRmAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdate

New-AzureRmAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName $aa -Schedule $s -Windows -AzureVMResourceId $azureVMIdsW -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

### <a name="updates-nodeployment"></a>Omstandigheden Installatie van updates zonder implementatie

### <a name="issue"></a>Probleem

Wanneer u een Windows-computer registreert in Updatebeheer, kan de installatie van updates zonder een implementatie worden weer geven.

### <a name="cause"></a>Oorzaak

In Windows worden updates automatisch geïnstalleerd zodra ze beschikbaar zijn. Dit kan leiden tot Verwar ring als u geen update hebt gepland die op de machine moet worden geïmplementeerd.

### <a name="resolution"></a>Oplossing

De Windows-register sleutel `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU` , wordt standaard ingesteld op "4"- **automatisch downloaden en installeren**.

Voor Updatebeheer-clients wordt aangeraden om deze sleutel in te stellen op 3- **automatisch downloaden, maar niet automatisch te installeren**.

Zie [Automatische updates configureren](https://docs.microsoft.com/en-us/windows/deployment/update/waas-wu-settings#configure-automatic-updates)voor meer informatie.

### <a name="nologs"></a>Omstandigheden Computers worden niet weer gegeven in de portal onder Updatebeheer

#### <a name="issue"></a>Probleem

U kunt in de volgende scenario's uitvoeren:

* Uw computer laat zien dat deze **niet is geconfigureerd** vanuit de updatebeheer weer gave van een virtuele machine

* Uw computers ontbreken in de Updatebeheer weer gave van uw Automation-account

* U hebt computers die worden weer gegeven als **niet beoordeeld** onder **naleving**, maar u ziet de heartbeat-gegevens in azure monitor logboeken voor de Hybrid Runbook worker maar niet updatebeheer.

#### <a name="cause"></a>Oorzaak

Dit kan worden veroorzaakt door mogelijke lokale configuratie problemen of door een onjuist geconfigureerde Scope configuratie.

Het Hybrid Runbook Worker moet mogelijk opnieuw worden geregistreerd en opnieuw worden geïnstalleerd.

Mogelijk hebt u een quotum gedefinieerd in uw werk ruimte, waardoor de gegevens niet kunnen worden opgeslagen.

#### <a name="resolution"></a>Oplossing

* Zorg ervoor dat uw computer rapporteert aan de juiste werk ruimte. Controleer in welke werk ruimte uw computer rapporteert. Zie [agent connectiviteit controleren op Log Analytics](../../azure-monitor/platform/agent-windows.md#verify-agent-connectivity-to-log-analytics)voor instructies om dit te controleren. Zorg er vervolgens voor dat dit de werk ruimte is die aan uw Azure Automation-account is gekoppeld. U kunt dit controleren door naar uw Automation-account te gaan en op **gekoppelde werk ruimte** onder **gerelateerde resources**te klikken.

* Controleer of de computers in uw Log Analytics-werk ruimte worden weer gegeven. Voer de volgende query uit in uw Log Analytics-werk ruimte die is gekoppeld aan uw Automation-account. Als uw computer niet wordt weer geven in de query resultaten, wordt de computer niet geheartbeatt, wat betekent dat er waarschijnlijk een lokale configuratie probleem is. U kunt de probleem oplosser voor [Windows](update-agent-issues.md#troubleshoot-offline) of [Linux](update-agent-issues-linux.md#troubleshoot-offline) uitvoeren, afhankelijk van het besturings systeem, of u kunt [de agent opnieuw installeren](../../azure-monitor/learn/quick-collect-windows-computer.md#install-the-agent-for-windows). Als uw computer in de query resultaten wordt weer gegeven, moet u de scope configuratie in het volgende opsommings teken opgeven.

  ```loganalytics
  Heartbeat
  | summarize by Computer, Solutions
  ```

* Controleren op problemen met de scope configuratie. De [Scope configuratie](../automation-onboard-solutions-from-automation-account.md#scope-configuration) bepaalt welke computers voor de oplossing worden geconfigureerd. Als uw computer wordt weer gegeven in uw werk ruimte, maar niet wordt weer gegeven, moet u de scope configuratie configureren om de computers te richten. Zie voor meer informatie over hoe u dit doet, de onboarding- [computers in de werk ruimte](../automation-onboard-solutions-from-automation-account.md#onboard-machines-in-the-workspace).

* Als de bovenstaande stappen het probleem niet verhelpen, volgt u de stappen in [een Windows-Hybrid Runbook worker implementeren](../automation-windows-hrw-install.md) om de Hybrid worker voor Windows opnieuw te installeren of [een Linux-Hybrid Runbook worker](../automation-linux-hrw-install.md) voor Linux te implementeren.

* Voer de volgende query uit in uw werk ruimte. Als u het resultaat `Data collection stopped due to daily limit of free data reached. Ingestion status = OverQuota` ziet, is er een quotum gedefinieerd in uw werk ruimte dat is bereikt en dat er geen gegevens meer kunnen worden opgeslagen. Navigeer in uw werk ruimte naar **gebruik en geschatte kosten** > **volume beheer van gegevens** en controleer uw quotum of verwijder het quotum dat u hebt.

  ```loganalytics
  Operation
  | where OperationCategory == 'Data Collection Status'
  | sort by TimeGenerated desc
  ```

## <a name="windows"></a>Windows

Als u problemen ondervindt bij het ongedaan maken van de oplossing op een virtuele machine, raadpleegt u het gebeurtenis logboek van **Operations Manager** onder Logboeken van **toepassingen en services** op de lokale computer voor gebeurtenissen met gebeurtenis-id **4502** en gebeurtenis bericht met **Microsoft. EnterpriseManagement. HealthService. AzureAutomation. HybridAgent**.

In de volgende sectie worden specifieke fout berichten en een mogelijke oplossing voor elk weer gemarkeerd. Zie voor andere voorbereidings problemen [problemen oplossen met het voorbereiden van oplossingen](onboarding.md).

### <a name="machine-already-registered"></a>Omstandigheden De computer is al geregistreerd voor een ander account

#### <a name="issue"></a>Probleem

Het volgende fout bericht wordt weer gegeven:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.InvalidOperationException: {"Message":"Machine is already registered to a different account."}
```

#### <a name="cause"></a>Oorzaak

De machine is al in een andere werk ruimte voor Updatebeheer geboardd.

#### <a name="resolution"></a>Oplossing

Voer oude artefacten op de machine uit door [de Hybrid runbook-groep te verwijderen](../automation-hybrid-runbook-worker.md#remove-a-hybrid-worker-group) en probeer het opnieuw.

### <a name="machine-unable-to-communicate"></a>Omstandigheden De computer kan niet communiceren met de service

#### <a name="issue"></a>Probleem

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

#### <a name="cause"></a>Oorzaak

Mogelijk is er een proxy, gateway of firewall die netwerk communicatie blokkeert.

#### <a name="resolution"></a>Oplossing

Controleer uw netwerk en zorg ervoor dat de juiste poorten en adressen zijn toegestaan. Zie [netwerk vereisten](../automation-hybrid-runbook-worker.md#network-planning)voor een lijst met poorten en adressen die vereist zijn voor updatebeheer-en Hybrid Runbook-werk rollen.

### <a name="unable-to-create-selfsigned-cert"></a>Omstandigheden Kan geen zelfondertekend certificaat maken

#### <a name="issue"></a>Probleem

U ontvangt een van de volgende fout berichten:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception AgentService.HybridRegistration. PowerShell.Certificates.CertificateCreationException: Failed to create a self-signed certificate. ---> System.UnauthorizedAccessException: Access is denied.
```

#### <a name="cause"></a>Oorzaak

Het Hybrid Runbook Worker kan geen zelfondertekend certificaat genereren

#### <a name="resolution"></a>Oplossing

Controleer of het systeem account lees toegang heeft tot de map **C:\ProgramData\Microsoft\Crypto\RSA** en probeer het opnieuw.

### <a name="failed-to-start"></a>Omstandigheden Een machine kan niet worden gestart in een update-implementatie

#### <a name="issue"></a>Probleem

De status van een computer **kan niet worden gestart** voor een machine. Wanneer u de specifieke Details voor de computer bekijkt, wordt de volgende fout weer gegeven:

```error
Failed to start the runbook. Check the parameters passed. RunbookName Patch-MicrosoftOMSComputer. Exception You have requested to create a runbook job on a hybrid worker group that does not exist.
```

#### <a name="cause"></a>Oorzaak

Deze fout kan optreden als gevolg van een van de volgende redenen:

* De machine bestaat niet meer.
* De computer is uitgeschakeld en kan niet worden bereikt.
* De computer heeft een probleem met de netwerk verbinding en de Hybrid worker op de computer is onbereikbaar.
* Er is een update voor de micro soft monitoring agent die de SourceComputerId heeft gewijzigd
* De update-uitvoering kan worden beperkt als u de limiet van 2.000 gelijktijdige taken in een Automation-account bereikt. Elke implementatie wordt beschouwd als een taak en elke computer in een update-implementatie aantal als een taak. Alle andere Automation-taken of update-implementaties die momenteel worden uitgevoerd in uw Automation-account tellen bij de limiet voor gelijktijdige taken.

#### <a name="resolution"></a>Oplossing

Gebruik [dynamische groepen](../automation-update-management.md#using-dynamic-groups) voor uw update-implementaties wanneer dit van toepassing is.

* Controleer of de computer nog bestaat en bereikbaar is. Als deze niet bestaat, bewerkt u de implementatie en verwijdert u de computer.
* Zie de sectie over het plannen van een [netwerk](../automation-update-management.md#ports) voor een lijst met poorten en adressen die vereist zijn voor updatebeheer en controleer of uw computer voldoet aan deze vereisten.
* Voer de volgende query uit in log Analytics om computers in uw omgeving `SourceComputerId` te vinden die zijn gewijzigd. Zoek naar computers die dezelfde `Computer` waarde hebben, maar een andere `SourceComputerId` waarde. Wanneer u de betrokken computers hebt gevonden, moet u de update-implementaties die zijn gericht op deze machines bewerken en de computers verwijderen en opnieuw toevoegen `SourceComputerId` , zodat de juiste waarde wordt weer gegeven.

   ```loganalytics
   Heartbeat | where TimeGenerated > ago(30d) | distinct SourceComputerId, Computer, ComputerIP
   ```

### <a name="hresult"></a>Omstandigheden Machine wordt weer gegeven als niet beoordeeld en toont een HResult-uitzonde ring

#### <a name="issue"></a>Probleem

U hebt computers die worden weer gegeven als **niet beoordeeld** onder **naleving**en u ziet een uitzonderings bericht hieronder.

#### <a name="cause"></a>Oorzaak

Windows Update of WSUS is niet juist geconfigureerd op de computer. Updatebeheer is afhankelijk van Windows Update of WSUS om te voorzien in de benodigde updates, de status van de patch en de resultaten van geïmplementeerde patches. Zonder deze informatie Updatebeheer kan niet op de juiste wijze rapporteren over de benodigde patches of geïnstalleerd.

#### <a name="resolution"></a>Oplossing

Dubbel klik op de uitzonde ring die rood wordt weer gegeven om het volledige uitzonderings bericht weer te geven. Raadpleeg de volgende tabel voor mogelijke oplossingen of acties die u moet uitvoeren:

|Uitzondering  |Oplossing of actie  |
|---------|---------|
|`Exception from HRESULT: 0x……C`     | Zoek in de [lijst met fout codes voor Windows Update](https://support.microsoft.com/help/938205/windows-update-error-code-list) naar aanvullende informatie over de oorzaak van de uitzonde ring.        |
|`0x8024402C`</br>`0x8024401C`</br>`0x8024402F`      | Deze fouten zijn problemen met de netwerk verbinding. Zorg ervoor dat de computer de juiste netwerk verbinding heeft Updatebeheer. Zie de sectie over [netwerk planning](../automation-update-management.md#ports) voor een lijst met poorten en adressen die vereist zijn.        |
|`0x8024001E`| De update bewerking is niet voltooid omdat de service of het systeem is afgesloten.|
|`0x8024002E`| Windows Update-service is uitgeschakeld.|
|`0x8024402C`     | Als u een WSUS-server gebruikt, moet u ervoor zorgen dat de `WUServer` register `WUStatusServer` waarden voor en onder `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate` de register sleutel de juiste WSUS-server hebben.        |
|`0x80072EE2`|Probleem met de netwerk verbinding of het probleem praten met een geconfigureerde WSUS-server. Controleer de WSUS-instellingen en controleer of deze toegankelijk is vanaf de client.|
|`The service cannot be started, either because it is disabled or because it has no enabled devices associated with it. (Exception from HRESULT: 0x80070422)`     | Zorg ervoor dat de Windows Update service (wuauserv) wordt uitgevoerd en niet is uitgeschakeld.        |
|Een andere algemene uitzonde ring     | Zoek op internet naar de mogelijke oplossingen en werk met uw lokale IT-ondersteuning.         |

Bekijk de `windowsupdate.log` kan u helpen om de mogelijke oorzaak te bepalen. Zie [het bestand WindowsUpdate. log lezen](https://support.microsoft.com/en-ca/help/902093/how-to-read-the-windowsupdate-log-file)voor meer informatie over het lezen van het logboek.

Daarnaast kunt u de [Windows Update probleemoplossings](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) programma downloaden en uitvoeren om te controleren of er problemen zijn met Windows Update op de computer.

> [!NOTE]
> De [Windows Update probleem Oplosser](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) geeft het voor Windows-clients, maar werkt ook op Windows Server.

## <a name="linux"></a>Linux

### <a name="scenario-update-run-fails-to-start"></a>Scenario: De update-uitvoering kan niet worden gestart

#### <a name="issue"></a>Probleem

Het starten van een update wordt niet gestart op een Linux-computer.

#### <a name="cause"></a>Oorzaak

De Linux-Hybrid Worker is beschadigd.

#### <a name="resolution"></a>Oplossing

Maak een kopie van het volgende logboek bestand en bewaar deze voor het oplossen van problemen:

```bash
/var/opt/microsoft/omsagent/run/automationworker/worker.log
```

### <a name="scenario-update-run-starts-but-encounters-errors"></a>Scenario: De update-uitvoering wordt gestart, maar er treden fouten op

#### <a name="issue"></a>Probleem

Er wordt een update-uitvoering gestart, maar er zijn fouten opgetreden tijdens de uitvoering.

#### <a name="cause"></a>Oorzaak

Mogelijke oorzaken zijn:

* Pakket beheer is beschadigd
* Specifieke pakketten kunnen problemen veroorzaken met patches op basis van de Cloud
* Andere oorzaken

#### <a name="resolution"></a>Oplossing

Als er fouten optreden tijdens het uitvoeren van een update nadat deze is gestart op Linux, controleert u de taak uitvoer van de betrokken computer in de uitvoering. Mogelijk vindt u specifieke fout berichten van de pakket manager van uw computer die u kunt onderzoeken en actie moet ondernemen. Voor Updatebeheer moet pakket beheer in orde zijn voor geslaagde update-implementaties.

In sommige gevallen kunnen pakket updates problemen veroorzaken met Updatebeheer voor komen dat een update-implementatie wordt voltooid. Als u dit ziet, moet u deze pakketten uitsluiten van toekomstige update runs of hand matig installeren.

Als u een probleem met een patches niet kunt oplossen, maakt u een kopie van het volgende logboek bestand en behoudt u het **voordat** de volgende update-implementatie wordt gestart voor het oplossen van problemen:

```bash
/var/opt/microsoft/omsagent/run/automationworker/omsupdatemgmt.log
```

## <a name="patches-are-not-installed"></a>Patches zijn niet geïnstalleerd

### <a name="machines-do-not-install-updates"></a>Machines installeren geen updates

* Voer de updates rechtstreeks op de machine uit. Als de machine niet kan worden bijgewerkt, raadpleegt u de [lijst met potentiële fouten in de gids voor probleemoplossing](https://docs.microsoft.com/azure/automation/troubleshoot/update-management#hresult).
* Als updates lokaal worden uitgevoerd, verwijdert u de agent en installeert u deze opnieuw op de machine met behulp van de volgende instructies in [Een VM verwijderen uit Updatebeheer](https://docs.microsoft.com/azure/automation/automation-update-management#remove-a-vm-from-update-management).

### <a name="i-know-updates-are-available-but-they-dont-show-as-needed-on-my-machines"></a>Ik weet dat er updates beschikbaar zijn, maar ze worden niet naar wens weer gegeven op mijn computers

* Dit gebeurt vaak als machines zijn geconfigureerd voor updates van WSUS/SCCM, maar de updates niet door WSUS/SCCM zijn goedgekeurd.
* U kunt controleren of machines voor WSUS/SCCM zijn geconfigureerd door [met de registersleutel 'UseWUServer' kruislings naar de registersleutels in de sectie 'Configuring Automatic Updates by Editing the Registry' (Automatische updates configureren door het register te bewerken) van dit document te verwijzen](https://support.microsoft.com/help/328010/how-to-configure-automatic-updates-by-using-group-policy-or-registry-s)

### <a name="updates-show-as-installed-but-i-cant-find-them-on-my-machine"></a>**Updates verschijnen na installatie maar ik kan ze niet vinden op mijn machine**

* Updates worden vaak verdrongen door andere updates. Zie voor meer informatie ['Update is superseded' (Update wordt verdrongen) in de gids voor probleemoplossing van Windows Update](https://docs.microsoft.com/windows/deployment/update/windows-update-troubleshooting#the-update-is-not-applicable-to-your-computer)

### <a name="installing-updates-by-classification-on-linux"></a>**Updates per classificatie installeren op Linux**

* Het per classificatie implementeren van updates naar Linux ('Essentiële en beveiligingsupdates'), heeft belangrijke beperkingen, met name voor CentOS. Deze [beperkingen worden beschreven op de overzichtspagina Updatebeheer](https://docs.microsoft.com/azure/automation/automation-update-management#linux-2)

### <a name="kb2267602-is-consistently--missing"></a>**KB2267602 ontbreekt consistent**

* KB2267602 is de [definitie-update voor Windows Defender](https://www.microsoft.com/wdsi/definitions). Deze wordt dagelijks bijgewerkt.

## <a name="other"></a>Omstandigheden Mijn probleem komt niet hierboven voor in de lijst

### <a name="issue"></a>Probleem

U hebt een probleem dat niet wordt opgelost door de andere scenario's die worden weer gegeven.

### <a name="cause"></a>Oorzaak

Onjuist geconfigureerde of ontbrekende register sleutels kunnen problemen veroorzaken met Updatebeheer.

### <a name="resolution"></a>Oplossing

Verwijder de register sleutel `HKLM:\SOFTWARE\Microsoft\HybridRunbookWorker` en start de **HealthService**opnieuw op.

U kunt ook de volgende Power shell-opdrachten gebruiken.

```powershell
Remove-Item -Path "HKLM:\software\microsoft\hybridrunbookworker" -Recurse -Force
Restart-Service healthservice
```

## <a name="next-steps"></a>Volgende stappen

Als u het probleem niet ziet of als u het probleem niet kunt oplossen, gaat u naar een van de volgende kanalen voor meer ondersteuning:

* Krijg antwoorden van Azure-experts op [Azure-Forums](https://azure.microsoft.com/support/forums/)
* Maak verbinding met [@AzureSupport](https://twitter.com/azuresupport), het officiële Microsoft Azure-account voor het verbeteren van de gebruikerservaring door de Azure-community in contact te brengen met de juiste resources: antwoorden, ondersteuning en experts.
* Als u meer hulp nodig hebt, kunt u een ondersteunings incident voor Azure opslaan. Ga naar de [ondersteunings site van Azure](https://azure.microsoft.com/support/options/) en selecteer **ondersteuning verkrijgen**.
