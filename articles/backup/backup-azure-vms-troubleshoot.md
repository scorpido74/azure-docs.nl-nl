---
title: Back-upfouten oplossen met Azure VM's
description: In dit artikel vindt u informatie over het oplossen van fouten die zijn opgetreden bij het back-upen en herstellen van virtuele Azure-machines.
ms.reviewer: srinathv
ms.topic: troubleshooting
ms.date: 08/30/2019
ms.openlocfilehash: 15e4b4c8850798fd2386cd2874b6ab58a18d5406
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79297387"
---
# <a name="troubleshooting-backup-failures-on-azure-virtual-machines"></a>Back-upfouten oplossen op virtuele Azure-machines

U fouten oplossen die zijn opgetreden tijdens het gebruik van Azure Backup met de onderstaande informatie:

## <a name="backup"></a>Back-up

In deze sectie wordt een back-upbewerking uitgevoerd als azure virtual-machine.

### <a name="basic-troubleshooting"></a>Eenvoudige probleemoplossing

* Controleer of de VM-agent (WA-agent) de [nieuwste versie](https://docs.microsoft.com/azure/backup/backup-azure-arm-vms-prepare#install-the-vm-agent)is.
* Controleer of de Windows- of Linux VM OS-versie wordt ondersteund, raadpleeg de [IaaS VM Backup Support Matrix](https://docs.microsoft.com/azure/backup/backup-support-matrix-iaas).
* Controleer of een andere back-upservice niet wordt uitgevoerd.
  * Om ervoor te zorgen dat er geen problemen met de momentopnameextensie zijn, [verwijdert u extensies om opnieuw laden te forceren en probeert u de back-up opnieuw.](https://docs.microsoft.com/azure/backup/backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout)
* Controleer of de VM een internetverbinding heeft.
  * Zorg ervoor dat een andere back-upservice niet wordt uitgevoerd.
* Controleer `Services.msc`vanaf of de **Windows Azure Guest Agent-service** **actief**is. Als de **Windows Azure Guest Agent-service** ontbreekt, installeert u deze vanuit [Azure VM's van Azure in een vault voor Herstelservices.](https://docs.microsoft.com/azure/backup/backup-azure-arm-vms-prepare#install-the-vm-agent)
* In **het gebeurtenislogboek** kunnen back-upfouten worden weergegeven die afkomstig zijn van andere back-upproducten, bijvoorbeeld Windows Server-back-up, en die niet te wijten zijn aan azure-back-ups. Gebruik de volgende stappen om te bepalen of het probleem zich met Azure Backup bevindt:
  * Als er een fout optreedt met een item **Back-up** in de gebeurtenisbron of het bericht, controleert u of Azure IaaS VM Backup backups zijn geslaagd en of er een herstelpunt is gemaakt met het gewenste momentopnametype.
  * Als Azure Backup werkt, is het probleem waarschijnlijk met een andere back-upoplossing.
  * Hier is een voorbeeld van een gebeurtenisviewerfout 517 waarbij Azure-back-up prima werkte, maar 'Windows Server Backup' niet werkte:<br>
    ![Windows Server Backup mislukt](media/backup-azure-vms-troubleshoot/windows-server-backup-failing.png)
  * Als Azure Backup mislukt, zoekt u in dit artikel naar de bijbehorende foutcode in de sectie Algemene VM-back-upfouten.

## <a name="common-issues"></a>Algemene problemen

Hieronder volgen veelvoorkomende problemen met back-upfouten op virtuele Azure-machines.

## <a name="copyingvhdsfrombackupvaulttakinglongtime---copying-backed-up-data-from-vault-timed-out"></a>CopyingVHDsFromBackUpVaultTakingLongTime - Back-upgegevens kopiëren uit een time-out van de kluis

Foutcode: CopyingVHDsFromBackUpVaultTakingLongTime <br/>
Foutbericht: back-upgegevens kopiëren uit een time-out van de kluis

Dit kan gebeuren als gevolg van tijdelijke opslagfouten of onvoldoende opslagaccount IOPS voor back-upservice om gegevens binnen de time-outperiode naar de kluis over te dragen. Configureer VM-back-up met behulp van deze [aanbevolen procedures](backup-azure-vms-introduction.md#best-practices) en probeer de back-upbewerking opnieuw uit.

## <a name="usererrorvmnotindesirablestate---vm-is-not-in-a-state-that-allows-backups"></a>UserErrorVmNotInDesirableState - VM is niet in een staat die back-ups mogelijk maakt

Foutcode: UserErrorVmNotInDesirableState <br/>
Foutbericht: VM bevindt zich niet in een status die back-ups toestaat.<br/>

De back-upbewerking is mislukt omdat de VM de status Mislukt heeft. Voor een geslaagde back-up moet de VM de status Actief, Gestopt of Gestopt (toewijzing opgeheven) hebben.

* Als de vm zich in een tijdelijke status bevindt tussen **Uitvoeren** en **Afsluiten,** wacht u tot de status is gewijzigd. Activeer dan de back-up taak.
* Als de VM een Linux-VM is en de Linux-kernelmodule met beveiliging wordt gebruikt, sluit u het Azure Linux Agent-pad **/var/lib/waagent** uit van het beveiligingsbeleid en controleert u of de back-upextensie is geïnstalleerd.

## <a name="usererrorfsfreezefailed---failed-to-freeze-one-or-more-mount-points-of-the-vm-to-take-a-file-system-consistent-snapshot"></a>UserErrorFsFreezeFailed - Kan een of meer bevestigingspunten van de VM niet bevriezen om een consistente momentopname van een bestandssysteem te maken

Foutcode: UserErrorFsFreeze Mislukt <br/>
Foutbericht: kan een of meer bevestigingspunten van de vm niet bevriezen om een consistente momentopname van een bestandssysteem te maken.

* De apparaten waarvoor de status van het bestandssysteem niet is gereinigd, worden ongedaan gemaakt met behulp van de **opdracht umount.**
* Voer een consistentiecontrole van het bestandssysteem uit op deze apparaten met behulp van de **fsck-opdracht.**
* Monteer de apparaten opnieuw en probeer opnieuw een back-upbewerking.</ol>

## <a name="extensionsnapshotfailedcom--extensioninstallationfailedcom--extensioninstallationfailedmdtc---extension-installationoperation-failed-due-to-a-com-error"></a>ExtensionSnapshotFailedCOM / ExtensionInstallationFailedCOM / ExtensionInstallationFailedMDTC - Installatie/bewerking extensie mislukt door een COM+-fout

Foutcode: ExtensionSnapshotFailedCOM <br/>
Foutbericht: momentopnamebewerking is mislukt door een COM+-fout

Foutcode: ExtensionInstallationFailedCOM  <br/>
Foutbericht: Installatie/bewerking extensie mislukt door een COM+-fout

Foutcode: ExtensionInstallationFailedMDTC <br/>
Foutbericht: Extensie-installatie is mislukt met de fout "COM+ kon niet praten met de Microsoft Distributed Transaction Coordinator <br/>

De back-upbewerking is mislukt vanwege een probleem met de **COM+-systeemtoepassing van** de Windows-service.  Volg deze stappen om dit probleem op te lossen:

* Probeer windows service **COM+ System Application** te starten/opnieuw te starten (van een opdrachtprompt met verhoogde bevoegdheid **- net start COMSysApp).**
* Zorg ervoor dat de service **Distributed Transaction Coordinator** wordt uitgevoerd als **Network Service-account.** Zo niet, wijzigt u deze om te worden uitgevoerd als **Network Service-account** en start **COM+ Systeemtoepassing**opnieuw.
* Als u de service niet opnieuw starten, installeert u de service **Gedistribueerde transactiecoördinator** opnieuw door de onderstaande stappen te volgen:
  * Stop de MSDTC-service
  * Open een opdrachtprompt (cmd)
  * Opdracht 'msdtc -verwijderen' uitvoeren
  * Opdracht 'msdtc -installeren' uitvoeren
  * Start de MSDTC-service
* Start de Windows-service **COM+ Systeemtoepassing**. Nadat de **COM+ Systeemtoepassing** is gestart, activeert u een back-uptaak vanuit de Azure-portal.</ol>

## <a name="extensionfailedvsswriterinbadstate---snapshot-operation-failed-because-vss-writers-were-in-a-bad-state"></a>ExtensionFailedVssWriterInBadState - Snapshot-bewerking is mislukt omdat VSS-schrijvers in een slechte staat waren

Foutcode: ExtensionFailedVssWriterInBadState <br/>
Foutbericht: momentopnamebewerking is mislukt omdat VSS-schrijvers zich in een slechte staat bevonden.

Herstart VSS-schrijvers die in een slechte staat verkeren. Voer vanaf een opdrachtprompt ```vssadmin list writers```met verhoogde bevoegdheid . De output bevat alle VSS-schrijvers en hun status. Voor elke VSS-schrijver met een status die niet **[1] Stabiel**is , om VSS-writer opnieuw op te starten, voert u de volgende opdrachten uit vanaf een opdrachtprompt met verhoogde bevoegdheid:

* ```net stop serviceName```
* ```net start serviceName```

## <a name="extensionconfigparsingfailure--failure-in-parsing-the-config-for-the-backup-extension"></a>ExtensionConfigParsingFailure - Fout in het ontschepen van de config voor de back-up extensie

Foutcode: ExtensionConfigParsingFailure<br/>
Foutbericht: Fout in het ontwalen van de config voor de back-upextensie.

Deze fout treedt op vanwege gewijzigde machtigingen in de **map MachineKeys:** **%systemdrive%\programdata\microsoft\crypto\rsa\machinekeys**.
Voer de volgende opdracht uit en controleer of machtigingen op de **map MachineKeys** standaard zijn:**icacls %systemdrive%\programdata\microsoft\crypto\rsa\machinekeys**.

Standaardmachtigingen zijn als volgt:

* Iedereen: (R,W)
* BUILTIN\Administrators: (F)

Als u machtigingen ziet in de map **MachineKeys** die anders zijn dan de standaardwaarden, voert u de volgende stappen uit om machtigingen te corrigeren, het certificaat te verwijderen en de back-up te activeren:

1. Machtigingen op de **map MachineKeys** herstellen. Door explorer-beveiligingseigenschappen en geavanceerde beveiligingsinstellingen in de map te gebruiken, worden machtigingen opnieuw ingesteld naar de standaardwaarden. Verwijder alle gebruikersobjecten, behalve de standaardinstellingen uit de map, en zorg ervoor dat de machtiging **Iedereen** als volgt speciale toegang heeft:

   * Map/leesgegevens lijst
   * Kenmerken lezen
   * Uitgebreide kenmerken lezen
   * Bestanden/schrijfgegevens maken
   * Mappen maken/gegevens toevoegen
   * Schrijfkenmerken
   * Uitgebreide kenmerken schrijven
   * Leesmachtigingen
2. Verwijder alle certificaten waar **uitgegeven aan** het klassieke implementatiemodel of Windows Azure CRP **Certificate Generator**is:

   * [Open certificaten op een lokale computerconsole](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/how-to-view-certificates-with-the-mmc-snap-in).
   * Verwijder **onder Persoonlijke** > **certificaten**alle certificaten waarin **uitgegeven is** het klassieke implementatiemodel of Windows Azure CRP Certificate **Generator**.
3. Een VM-back-uptaak activeren.

## <a name="extensionstuckindeletionstate---extension-state-is-not-supportive-to-backup-operation"></a>ExtensionStuckInDeletionState - Extensiestatus is geen voorstander van back-upbewerking

Foutcode: ExtensionStuckInDeletionState <br/>
Foutbericht: extensiestatus is geen ondersteuning voor back-upbewerking

De back-upbewerking is mislukt vanwege de inconsistente status van back-upextensie. Volg deze stappen om dit probleem op te lossen:

* Zorg ervoor dat de gastagent geïnstalleerd en responsief is
* Ga vanuit de Azure-portal naar Alle**instellingen voor** > **Extensions** **virtuele machine** > 
* Selecteer de backup-extensie VmSnapshot of VmSnapshotLinux en klik op **Verwijderen**
* Na het verwijderen van back-upextensie probeert u de back-upbewerking opnieuw
* De volgende back-upbewerking installeert de nieuwe extensie in de gewenste status

## <a name="extensionfailedsnapshotlimitreachederror---snapshot-operation-failed-as-snapshot-limit-is-exceeded-for-some-of-the-disks-attached"></a>ExtensionFailedSnapshotLimitReachedError - Snapshot-bewerking is mislukt omdat momentopnamelimiet wordt overschreden voor sommige van de gekoppelde schijven

Foutcode: ExtensionFailedSnapshotLimitReachedError <br/>
Foutbericht: momentopnamebewerking is mislukt omdat momentopnamelimiet wordt overschreden voor sommige van de gekoppelde schijven

De momentopnamebewerking is mislukt omdat de momentopnamelimiet voor sommige van de gekoppelde schijven is overschreden. Voer de onderstaande stappen voor het oplossen van problemen uit en probeer de bewerking opnieuw.

* Verwijder de schijfblob-snapshots die niet vereist zijn. Wees voorzichtig om schijfblob niet te verwijderen, alleen snapshot blobs moeten worden verwijderd.
* Als Soft-delete is ingeschakeld op opslagaccounts van vm-schijven, configureert u de bewaring voor softdelete zodanig dat bestaande momentopnamen op elk moment lager zijn dan het maximaal toegestane aantal.
* Als Azure Site Recovery is ingeschakeld in de vm met een back-up, voert u de onderstaande stappen uit:

  * Zorg ervoor dat de waarde van **isanysnapshotfailed** is ingesteld als false in /etc/azure/vmbackup.conf
  * Plan Azure Site Recovery op een ander tijdstip, zodat deze niet in strijd is met de back-upbewerking.

## <a name="extensionfailedtimeoutvmnetworkunresponsive---snapshot-operation-failed-due-to-inadequate-vm-resources"></a>ExtensionFailedTimeoutVMNetworkUnresponsive - Snapshot-bewerking is mislukt vanwege onvoldoende VM-resources

Foutcode: ExtensionFailedTimeoutVMNetworkReageert niet<br/>
Foutbericht: momentopnamebewerking is mislukt vanwege onvoldoende VM-resources.

De back-upbewerking op de vm is mislukt als gevolg van vertraging in netwerkoproepen tijdens het uitvoeren van de momentopnamebewerking. U kunt dit probleem oplossen door stap 1 uit te voeren. Als het probleem zich blijft voordoen, probeert u stappen 2 en 3.

**Stap 1**: Momentopname maken via Host

Voer vanaf een opdrachtprompt met verhoogde bevoegdheid (beheerder) de onderstaande opdracht uit:

```text
REG ADD "HKLM\SOFTWARE\Microsoft\BcdrAgentPersistentKeys" /v SnapshotMethod /t REG_SZ /d firstHostThenGuest /f
REG ADD "HKLM\SOFTWARE\Microsoft\BcdrAgentPersistentKeys" /v CalculateSnapshotTimeFromHost /t REG_SZ /d True /f
```

Deze opdracht zorgt ervoor dat de momentopnamen worden gemaakt via host in plaats van Guest. Probeer de back-upbewerking opnieuw.

**Stap 2:** Probeer het back-upschema te wijzigen in een tijd waarin de VM minder wordt belast (minder CPU/IOps enz.)

**Stap 3**: Probeer [de grootte van VM te vergroten](https://azure.microsoft.com/blog/resize-virtual-machines/) en probeer de bewerking opnieuw

## <a name="common-vm-backup-errors"></a>Veelvoorkomende back-upfouten van VM's

| Foutdetails | Tijdelijke oplossing |
| ------ | --- |
| **Foutcode**: 320001, ResourceNotFound <br/> **Foutbericht:** kan de bewerking niet uitvoeren omdat VM niet meer bestaat. <br/> <br/> **Foutcode**: 400094, BCMV2VMNotFound <br/> **Foutbericht:** de virtuele machine bestaat niet <br/> <br/>  Er is geen virtuele Azure-machine gevonden.  |Deze fout treedt op wanneer de primaire vm wordt verwijderd, maar in het back-upbeleid wordt nog steeds gezocht naar een back-up van een vm. Ga als volgt te werk om deze fout op te lossen: <ol><li> Maak de virtuele machine opnieuw met dezelfde naam en dezelfde naam van de resourcegroep, **de naam van de cloudservice,**<br>**Of**</li><li> Stop met het beschermen van de virtuele machine met of zonder het verwijderen van de back-upgegevens. Zie [Virtuele machines stoppen met beveiligen](backup-azure-manage-vms.md#stop-protecting-a-vm)voor meer informatie .</li></ol>|
|**Foutcode**: UserErrorBCMPremiumStorageQuotaError<br/> **Foutbericht**: Kan de momentopname van de virtuele machine niet kopiëren vanwege onvoldoende vrije ruimte in het opslagaccount | Voor premium VM's op VM-back-upstack V1 kopiëren we de momentopname naar het opslagaccount. Deze stap zorgt ervoor dat back-upbeheerverkeer, dat werkt op de momentopname, het aantal IOPS dat beschikbaar is voor de toepassing niet beperkt met behulp van premium schijven. <br><br>We raden u aan slechts 50 procent, 17,5 TB, van de totale opslagruimte toe te wijzen. Vervolgens kan de Azure Backup-service de momentopname naar het opslagaccount kopiëren en gegevens van deze gekopieerde locatie in het opslagaccount naar de kluis overbrengen. |
| **Foutcode**: 380008, AzureVmOffline <br/> **Foutbericht:** Kan microsoft Recovery Services-extensie niet installeren omdat de virtuele machine niet wordt uitgevoerd | De VM-agent is een voorwaarde voor de Azure Recovery Services-extensie. Installeer de Azure Virtual Machine Agent en start de registratiebewerking opnieuw. <br> <ol> <li>Controleer of de VM-agent correct is geïnstalleerd. <li>Zorg ervoor dat de vlag op de VM-config correct is ingesteld.</ol> Lees meer over het installeren van de VM-agent en het valideren van de VM Agent-installatie. |
| **Foutcode:** ExtensionSnapshotBitlockerError <br/> **Foutbericht:** de momentopnamebewerking is mislukt met de fout van de werking van de Volume Shadow Copy Service (VSS) **Dit station wordt vergrendeld door BitLocker-stationsversleuteling. U moet dit station ontgrendelen vanuit het Configuratiescherm.** |Schakel BitLocker uit voor alle stations op de vm en controleer of het VSS-probleem is opgelost. |
| **Foutcode**: VmNotInDesirableState <br/> **Foutbericht:** de VM bevindt zich niet in een status waarmee back-ups kunnen worden gemaakt. |<ul><li>Als de vm zich in een tijdelijke status bevindt tussen **Uitvoeren** en **Afsluiten,** wacht u tot de status is gewijzigd. Activeer dan de back-up taak. <li> Als de VM een Linux-VM is en de Linux-kernelmodule met beveiliging wordt gebruikt, sluit u het Azure Linux Agent-pad **/var/lib/waagent** uit van het beveiligingsbeleid en controleert u of de back-upextensie is geïnstalleerd.  |
| De VM-agent is niet aanwezig op de virtuele machine: <br>Installeer een vereiste en de VM-agent. Start vervolgens de bewerking opnieuw. |Lees meer over [de installatie van VM Agent en het valideren van de installatie van VM Agent.](#vm-agent) |
| **Foutcode:** ExtensionSnapshotFailedNoSecureNetwork <br/> **Foutbericht:** de momentopnamebewerking is mislukt omdat er geen beveiligd netwerkcommunicatiekanaal is gemaakt. | <ol><li> Open de registereditor door **regedit.exe** in een verhoogde modus uit te voeren. <li> Identificeer alle versies van het .NET Framework dat in uw systeem aanwezig is. Ze zijn aanwezig onder de hiërarchie van registersleutel **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft**. <li> Voeg voor elke .NET-kaderdie in de registersleutel aanwezig is, de volgende toets toe: <br> **SchuseStrongCrypto"=dword:00000001**. </ol>|
| **Foutcode**: ExtensionVCRedistInstallationFailure <br/> **Foutbericht**: De momentopnamebewerking is mislukt omdat visuele C++ Redistributable voor Visual Studio 2012 niet is geïnstalleerd. | Navigeer naar C:\Packages\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot\agentVersion en installeer vcredist2013_x64.<br/>Zorg ervoor dat de registersleutelwaarde waarmee de service-installatie wordt ingesteld op de juiste waarde is ingesteld. Dat wil zeggen, stel de **startwaarde** in **HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Msiserver** in op **3** en niet **op 4**. <br><br>Als u nog steeds problemen hebt met de installatie, start u de installatieservice opnieuw door **MSIEXEC /UNREGISTER** uit te voeren, gevolgd door **MSIEXEC /REGISTER** vanaf een opdrachtprompt met verhoogde bevoegdheid.  |
| **Foutcode**: UserErrorRequestDisallowedByPolicy <BR> **Foutbericht:** er is een ongeldig beleid geconfigureerd op de VM, waardoor momentopnamebewerking wordt voorkomen. | Als u een Azure-beleid hebt dat tags binnen uw omgeving regelt, u overwegen het beleid te wijzigen van een [effect Weigeren](https://docs.microsoft.com/azure/governance/policy/concepts/effects#deny) in een [effect Wijzigen](https://docs.microsoft.com/azure/governance/policy/concepts/effects#modify)of de brongroep handmatig maken op basis van het [naamgevingsschema](https://docs.microsoft.com/azure/governance/policy/tutorials/govern-tags) [dat vereist is voor Azure Backup.](https://docs.microsoft.com/azure/backup/backup-during-vm-creation#azure-backup-resource-group-for-virtual-machines)
## <a name="jobs"></a>Taken

| Foutdetails | Tijdelijke oplossing |
| --- | --- |
| Annulering wordt niet ondersteund voor dit taaktype: <br>Wacht tot de klus geklaard is. |Geen |
| De taak is niet in een geannuleerde staat: <br>Wacht tot de klus geklaard is. <br>**Of**<br> De geselecteerde taak bevindt zich niet in een status die kan worden geannuleerd: <br>Wacht tot de klus geklaard is. |Het is waarschijnlijk dat de klus bijna geklaard is. Wacht tot de klus geklaard is.|
| Back-up kan de taak niet annuleren omdat deze niet aan de gang is: <br>Annulering wordt alleen ondersteund voor lopende taken. Probeer een lopende taak te annuleren. |Deze fout treedt op vanwege een tijdelijke status. Wacht even en probeer de bewerking annuleren opnieuw. |
| Back-up kan de taak niet annuleren: <br>Wacht tot de klus geklaard is. |Geen |

## <a name="restore"></a>Herstellen

| Foutdetails | Tijdelijke oplossing |
| --- | --- |
| Herstellen is mislukt met een interne fout in de cloud. |<ol><li>De cloudservice die u probeert te herstellen, is geconfigureerd met DNS-instellingen. U controleren: <br>**$deployment = Get-AzureDeployment -ServiceName "ServiceName" -Slot "Productie" Get-AzureDns -DnsSettings $deployment. Dns-instellingen**.<br>Als **adres** is geconfigureerd, worden de DNS-instellingen geconfigureerd.<br> <li>De cloudservice die u probeert te herstellen, is geconfigureerd met **ReservedIP**en bestaande VM's in de cloudservice zijn in de gestopte status. U controleren of een cloudservice een IP heeft gereserveerd met behulp van de volgende PowerShell-cmdlets: **$deployment = Get-AzureDeployment -ServiceName servicename -Slot Productie $dep. ReservedIPName**. <br><li>U probeert een virtuele machine met de volgende speciale netwerkconfiguraties te herstellen naar dezelfde cloudservice: <ul><li>Virtuele machines onder load balancer configuratie, intern en extern.<li>Virtuele machines met meerdere gereserveerde IP's. <li>Virtuele machines met meerdere NIC's. </ul><li>Selecteer een nieuwe cloudservice in de gebruikersinterface of bekijk [hersteloverwegingen](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) voor VM's met speciale netwerkconfiguraties.</ol> |
| De geselecteerde DNS-naam is al gemaakt: <br>Geef een andere DNS-naam op en probeer het opnieuw. |Deze DNS-naam verwijst naar de naam van de cloudservice, meestal eindigend met **.cloudapp.net**. Deze naam moet uniek zijn. Als u deze fout krijgt, moet u tijdens het herstellen een andere VM-naam kiezen. <br><br> Deze fout wordt alleen weergegeven aan gebruikers van de Azure-portal. De herstelbewerking via PowerShell slaagt omdat alleen de schijven worden hersteld en de VM niet wordt gemaakt. De fout wordt geconfronteerd wanneer de VM expliciet door u wordt gemaakt na de schijfherstelbewerking. |
| De opgegeven virtuele netwerkconfiguratie is niet correct: <br>Geef een andere virtuele netwerkconfiguratie op en probeer het opnieuw. |Geen |
| De opgegeven cloudservice gebruikt een gereserveerd IP dat niet overeenkomt met de configuratie van de virtuele machine die wordt hersteld: <br>Geef een andere cloudservice op die geen gereserveerd IP gebruikt. Of kies een ander herstelpunt om te herstellen. |Geen |
| De cloudservice heeft zijn limiet bereikt op het aantal invoereindpunten: <br>Probeer de bewerking opnieuw door een andere cloudservice op te geven of door een bestaand eindpunt te gebruiken. |Geen |
| Het kluis- en doelopslagaccount van Recovery Services bevinden zich in twee verschillende regio's: <br>Controleer of het opslagaccount dat is opgegeven in de herstelbewerking zich in hetzelfde Azure-gebied bevindt als uw vault voor Herstelservices. |Geen |
| Het opslagaccount dat is opgegeven voor de herstelbewerking, wordt niet ondersteund: <br>Alleen basis- of standaardopslagaccounts met lokaal redundante of georedundante replicatie-instellingen worden ondersteund. Selecteer een ondersteund opslagaccount. |Geen |
| Het type opslagaccount dat is opgegeven voor de herstelbewerking, is niet online: <br>Controleer of het opslagaccount dat is opgegeven in de herstelbewerking online is. |Deze fout kan optreden als gevolg van een tijdelijke fout in Azure Storage of als gevolg van een storing. Kies een ander opslagaccount. |
| Het quotum voor de resourcegroep is bereikt: <br>Verwijder sommige brongroepen uit de Azure-portal of neem contact op met Azure Support om de limieten te verhogen. |Geen |
| Het geselecteerde subnet bestaat niet: <br>Selecteer een subnet dat bestaat. |Geen |
| De back-upservice heeft geen toestemming om toegang te krijgen tot bronnen in uw abonnement. |Als u deze fout wilt oplossen, herstelt u schijven eerst met behulp van de stappen in [Back-upschijven herstellen.](backup-azure-arm-restore-vms.md#restore-disks) Gebruik vervolgens de PowerShell-stappen in [Een vm maken van herstelde schijven.](backup-azure-vms-automation.md#restore-an-azure-vm) |

## <a name="backup-or-restore-takes-time"></a>Back-up maken of herstellen kost tijd

Als uw back-up meer dan 12 uur duurt of herstel meer dan 6 uur duurt, bekijkt u [de aanbevolen procedures](backup-azure-vms-introduction.md#best-practices)en [prestatieoverwegingen](backup-azure-vms-introduction.md#backup-performance)

## <a name="vm-agent"></a>VM-agent

### <a name="set-up-the-vm-agent"></a>De VM-agent instellen

De VM-agent is doorgaans al aanwezig in VM's die zijn gemaakt vanuit de Azure-galerie. Maar virtuele machines die zijn gemigreerd uit on-premises datacenters, hebben de VM-agent niet geïnstalleerd. Voor die VM's moet de VM-agent expliciet worden geïnstalleerd.

#### <a name="windows-vms"></a>Virtuele Windows-machines

* Download en installeer de [agent-MSI](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). U hebt beheerdersrechten nodig om de installatie te voltooien.
* Werk [de eigenschap VM bij voor](https://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) virtuele machines die zijn gemaakt met behulp van het klassieke implementatiemodel om aan te geven dat de agent is geïnstalleerd. Deze stap is niet vereist voor virtuele azure resource-machines.

#### <a name="linux-vms"></a>Virtuele Linux-machines

* Installeer de nieuwste versie van de agent vanuit de distributieopslagplaats. Zie de [Linux Agent repository](https://github.com/Azure/WALinuxAgent)voor meer informatie over de pakketnaam.
* Voor VM's die zijn gemaakt met behulp van het klassieke implementatiemodel, [gebruikt u deze blog](https://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) om de eigenschap VM bij te werken en te controleren of de agent is geïnstalleerd. Deze stap is niet vereist voor virtuele machines van Resource Manager.

### <a name="update-the-vm-agent"></a>De VM-agent bijwerken

#### <a name="windows-vms"></a>Virtuele Windows-machines

* Als u de VM-agent wilt bijwerken, installeert u de [vm-agentbinaries](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)opnieuw . Voordat u de agent bijwerkt, moet u ervoor zorgen dat er geen back-upbewerkingen plaatsvinden tijdens de VM Agent-update.

#### <a name="linux-vms"></a>Virtuele Linux-machines

* Als u de Linux VM-agent wilt bijwerken, volgt u de instructies in het artikel [Bijwerken van de Linux VM-agent](../virtual-machines/linux/update-agent.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

    > [!NOTE]
    > Gebruik altijd de distributierepository om de agent bij te werken.

    Download de agentcode niet van GitHub. Als de nieuwste agent niet beschikbaar is voor uw distributie, neemt u contact op met de distributieondersteuning voor instructies om de nieuwste agent te verkrijgen. U ook de nieuwste [Windows Azure Linux-agentgegevens](https://github.com/Azure/WALinuxAgent/releases) controleren in de GitHub-opslagplaats.

### <a name="validate-vm-agent-installation"></a>Installatie vm-agent valideren

Controleer de VM-versie in Windows VM's:

1. Meld u aan bij de virtuele Azure-machine en navigeer naar de map **C:\WindowsAzure\Packages**. Je moet het **WaAppAgent.exe** bestand vinden.
2. Klik met de rechtermuisknop op het bestand en ga naar **Eigenschappen**. Selecteer vervolgens het tabblad **Details.** Het veld **Productversie** moet 2.6.1198.718 of hoger zijn.

## <a name="troubleshoot-vm-snapshot-issues"></a>Problemen met vm-momentopnamen oplossen

VM-back-up is gebaseerd op het uitgeven van momentopnameopdrachten aan onderliggende opslag. Als u geen toegang hebt tot opslag of vertragingen in een momentopnametaak, kan de back-uptaak mislukken. De volgende voorwaarden kunnen leiden tot een fout in momentopnametaken:

* **VM's met SQL Server-back-up geconfigureerd, kunnen leiden tot vertraging van momentopnametaken.** Vm-back-up maakt standaard een VSS volledige back-up op Windows VM's. VM's waarop SQL Server wordt uitgevoerd, waarbij SQL Server-back-up is geconfigureerd, kunnen momentopnamevertragingen ondervinden. Als momentopnamevertragingen back-upfouten veroorzaken, stelt u de volgende registersleutel in:

   ```text
   [HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT]
   "USEVSSCOPYBACKUP"="TRUE"
   ```

* **Vm-status wordt onjuist gerapporteerd omdat de VM is afgesloten in RDP**. Als u het externe bureaublad hebt gebruikt om de virtuele machine uit te schakelen, controleert u of de VM-status in de portal juist is. Als de status niet correct is, gebruikt u de optie **Afsluiten** in het portalVM-dashboard om de vm af te sluiten.
* **Als meer dan vier VM's dezelfde cloudservice delen, spreidt u de VM's over meerdere back-upbeleidsregels.** Stagger de back-up tijden, dus niet meer dan vier VM back-ups beginnen op hetzelfde moment. Probeer de begintijden in het beleid met ten minste een uur te scheiden.
* **De VM draait op een hoge CPU of geheugen.** Als de virtuele machine draait bij een hoog geheugen of CPU-gebruik, meer dan 90 procent, wordt uw momentopnametaak in de wachtrij geplaatst en vertraagd. Uiteindelijk is het een keer uit. Als dit probleem zich voordoet, probeert u een on-demand back-up.

## <a name="networking"></a>Netwerken

DHCP moet in de gast zijn ingeschakeld om iaas VM-back-up te laten werken. Als u een statisch privé-IP-adres nodig hebt, configureert u dit via de Azure-portal of PowerShell. Controleer of de DHCP-optie in de VM is ingeschakeld.
Meer informatie over het instellen van een statisch IP-adres via PowerShell:

* [Een statisch intern IP-adres toevoegen aan een bestaande virtuele machine](https://docs.microsoft.com/powershell/module/az.network/set-aznetworkinterfaceipconfig?view=azps-3.5.0#description)
* [De toewijzingsmethode wijzigen voor een privé-IP-adres dat is toegewezen aan een netwerkinterface](../virtual-network/virtual-networks-static-private-ip-arm-ps.md#change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface)

