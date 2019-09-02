---
title: Back-upfouten met virtuele machines van Azure oplossen
description: Problemen met back-up en herstel van virtuele machines van Azure oplossen
ms.reviewer: srinathv
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 08/30/2019
ms.author: dacurwin
ms.openlocfilehash: 2f645d290175db9692649d825323313fc207a014
ms.sourcegitcommit: d470d4e295bf29a4acf7836ece2f10dabe8e6db2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/02/2019
ms.locfileid: "70210279"
---
# <a name="troubleshoot-azure-virtual-machine-backup"></a>Problemen oplossen met back-ups van virtuele Azure-machines
U kunt fouten oplossen die zijn opgetreden tijdens het gebruik van Azure Backup met de onderstaande informatie:

## <a name="backup"></a>Back-up
In deze sectie wordt de back-upbewerking voor de virtuele machine van Azure beschreven.

## <a name="copyingvhdsfrombackupvaulttakinglongtime---copying-backed-up-data-from-vault-timed-out"></a>CopyingVHDsFromBackUpVaultTakingLongTime: er is een time-out opgetreden bij het kopiëren van back-ups van gegevens

Fout code: CopyingVHDsFromBackUpVaultTakingLongTime <br/>
Foutbericht: Time-out bij het kopiëren van back-ups van gegevens uit de kluis

Dit kan gebeuren als gevolg van tijdelijke opslag fouten of onvoldoende opslag account-IOPS voor de back-upservice om binnen de time-outperiode gegevens over te dragen naar de kluis. Configureer VM-back-ups met behulp van deze [Aanbevolen procedures](backup-azure-vms-introduction.md#best-practices) en voer de back-upbewerking opnieuw uit.

## <a name="usererrorvmnotindesirablestate---vm-is-not-in-a-state-that-allows-backups"></a>UserErrorVmNotInDesirableState-VM heeft geen status die back-ups mogelijk maakt.

Fout code: UserErrorVmNotInDesirableState <br/>
Foutbericht: VM heeft een status die back-ups niet toestaat.<br/>

De back-upbewerking is mislukt, omdat de VM de status Mislukt heeft. Voor een geslaagde back-up moet de VM-status worden uitgevoerd, gestopt of gestopt (toewijzing ongedaan gemaakt).

* Als de virtuele machine zich in een tijdelijke status bevindt tussen het **uitvoeren** en **Afsluiten**, wacht u totdat de status is gewijzigd. Activeer vervolgens de back-uptaak.
*  Als de virtuele machine een virtuele Linux-machine is en gebruikmaakt van de Security Enhanced Linux kernel-module, sluit u het pad van de Azure Linux-agent uit het beveiligings beleid uit en zorgt u ervoor dat de back-upextensie is geïnstalleerd.

## <a name="usererrorfsfreezefailed---failed-to-freeze-one-or-more-mount-points-of-the-vm-to-take-a-file-system-consistent-snapshot"></a>UserErrorFsFreezeFailed: een of meer koppel punten van de virtuele machine kunnen niet worden geblokkeerd om een consistente moment opname van het bestands systeem te maken

Fout code: UserErrorFsFreezeFailed <br/>
Foutbericht: Kan een of meer koppelpunten van de VM niet stilzetten om een bestandssysteemconsistente momentopname te maken.

* Controleer de status van het bestands systeem van alle gekoppelde apparaten met behulp van de **tune2fs** -opdracht, bijvoorbeeld\| **tune2fs-l \\/dev/sdb1** . grep- **bestandssysteem status**.
* Ontkoppel de apparaten waarvoor de bestandssysteem status niet is opgeschoond, met behulp van de **umount** -opdracht.
* Voer een consistentie controle van het bestands systeem op deze apparaten uit met behulp van de **fsck** -opdracht.
* Koppel de apparaten opnieuw en voer de back-upbewerking opnieuw uit.</ol>

## <a name="extensionsnapshotfailedcom--extensioninstallationfailedcom--extensioninstallationfailedmdtc---extension-installationoperation-failed-due-to-a-com-error"></a>ExtensionSnapshotFailedCOM/ExtensionInstallationFailedCOM/ExtensionInstallationFailedMDTC-extensie is niet geïnstalleerd/bewerking mislukt vanwege een COM+-fout

Fout code: ExtensionSnapshotFailedCOM <br/>
Foutbericht: De momentopname bewerking is mislukt vanwege een COM+-fout

Fout code: ExtensionInstallationFailedCOM  <br/>
Foutbericht: De installatie van de extensie of bewerking is mislukt vanwege een COM+-fout

Fout code: ExtensionInstallationFailedMDTC <br/>
Foutbericht: De installatie van de extensie is mislukt met de fout ' COM+ kan niet communiceren met de micro soft-Distributed Transaction Coordinator <br/>

De back-upbewerking is mislukt vanwege een probleem met de Windows service **com+-systeem** toepassing.  Volg deze stappen om dit probleem op te lossen:

* Probeer de Windows service **com+-systeem toepassing** te starten/opnieuw te starten (vanaf een opdracht prompt met verhoogde bevoegdheid **-net start COMSysApp**).
* Zorg ervoor dat **Distributed Transaction Coordinator** Services wordt uitgevoerd als **netwerk service** account. Als dat niet het geval is, wijzigt u deze in uitvoeren als **netwerk service** account en start u **com+-systeem toepassing**opnieuw.
* Als de service niet opnieuw kan worden gestart, installeert u **Distributed Transaction Coordinator** -service opnieuw door de onderstaande stappen te volgen:
    * Stop de MSDTC-service
    * Open een opdrachtprompt (cmd)
    * Opdracht ' MSDTC-Uninstall ' uitvoeren
    * opdracht ' MSDTC-install ' ongedaan maken
    * Start de MSDTC-service
* Start de Windows service **com+-systeem toepassing**. Nadat de **com+-systeem toepassing** is gestart, wordt een back-uptaak geactiveerd vanuit het Azure Portal.</ol>

## <a name="extensionfailedvsswriterinbadstate---snapshot-operation-failed-because-vss-writers-were-in-a-bad-state"></a>ExtensionFailedVssWriterInBadState-momentopname bewerking is mislukt omdat de status van de VSS-schrijvers ongeldig was

Fout code: ExtensionFailedVssWriterInBadState <br/>
Foutbericht: De momentopname bewerking is mislukt, omdat de status van de VSS-schrijvers ongeldig is.

Herstart de VSS-schrijvers die een slechte status hebben. Voer ```vssadmin list writers```uit vanaf een opdracht prompt met verhoogde bevoegdheid. De uitvoer bevat alle VSS-schrijvers en hun status. Voer de volgende opdrachten uit vanaf een opdracht prompt met verhoogde bevoegdheid voor elke VSS Writer met een status die niet **[1] stabiel**is, om VSS Writer opnieuw op te starten:

  * ```net stop serviceName```
  * ```net start serviceName```

## <a name="extensionconfigparsingfailure--failure-in-parsing-the-config-for-the-backup-extension"></a>ExtensionConfigParsingFailure-fout bij het parseren van de configuratie voor de back-upextensie

Fout code: ExtensionConfigParsingFailure<br/>
Foutbericht: Er is een fout opgetreden bij het parseren van de configuratie voor de back-upextensie.

Deze fout treedt op als gevolg van gewijzigde machtigingen voor de map **MachineKeys** : **%systemdrive%\programdata\microsoft\crypto\rsa\machinekeys**.
Voer de volgende opdracht uit en controleer of de machtigingen voor de map **MachineKeys** standaard zijn:**icacls%SystemDrive%\programdata\microsoft\crypto\rsa\machinekeys**.

Standaard machtigingen zijn als volgt:
* Iedereen (R, W)
* BUILTIN\Administrators: (F)

Als u de machtigingen in de map **MachineKeys** ziet die afwijkt van de standaard instellingen, voert u de volgende stappen uit om machtigingen te corrigeren, het certificaat te verwijderen en de back-up te activeren:

1. Herstel machtigingen op de **MachineKeys** -map. Als u de beveiligings eigenschappen van Explorer en geavanceerde beveiligings instellingen in de map gebruikt, stelt u de machtigingen weer in op de standaard waarden. Verwijder alle gebruikers objecten, behalve de standaard instellingen uit de Directory, en zorg ervoor dat de machtiging **iedereen** als volgt speciale toegang heeft:

    * Map weer geven/gegevens lezen
    * Kenmerken lezen
    * Uitgebreide kenmerken lezen
    * Bestanden maken/gegevens schrijven
    * Mappen maken/gegevens toevoegen
    * Schrijf kenmerken
    * Uitgebreide kenmerken schrijven
    * Lees machtigingen
2. Alle certificaten verwijderen waarnaar wordt **uitgegeven in** is het klassieke implementatie model of de **Windows Azure CRP-certificaat Generator**:
    * [Certificaten op een lokale computer console openen](https://msdn.microsoft.com/library/ms788967(v=vs.110).aspx).
    * Onder **persoonlijke** > **certificaten**verwijdert u alle certificaten waarbij **uitgegeven aan** is het klassieke implementatie model of de **Windows Azure CRP-certificaat Generator**.
3. Activeer een VM-back-uptaak.

## <a name="extensionstuckindeletionstate---extension-state-is-not-supportive-to-backup-operation"></a>De ExtensionStuckInDeletionState-extensie status is geen ondersteuning voor de back-upbewerking

Fout code: ExtensionStuckInDeletionState <br/>
Foutbericht: De extensie status is geen ondersteuning voor de back-upbewerking

De back-upbewerking is mislukt vanwege een inconsistente status van de back-upextensie. Volg deze stappen om dit probleem op te lossen:

* Zorg ervoor dat de gastagent geïnstalleerd en responsief is
* Ga van Azure-portal naar **Virtuele machine** > **Alle instellingen** > **Extensies**
* Selecteer de backup-extensie VmSnapshot of VmSnapshotLinux en klik op **Verwijderen**
* Probeer na verwijdering van de back-upextensie de back-upbewerking opnieuw
* De volgende back-upbewerking installeert de nieuwe extensie in de gewenste status

## <a name="extensionfailedsnapshotlimitreachederror---snapshot-operation-failed-as-snapshot-limit-is-exceeded-for-some-of-the-disks-attached"></a>ExtensionFailedSnapshotLimitReachedError-momentopname bewerking mislukt omdat de limiet voor moment opnamen is overschreden voor een aantal gekoppelde schijven

Fout code: ExtensionFailedSnapshotLimitReachedError <br/>
Foutbericht: De momentopname bewerking is mislukt omdat de limiet voor moment opnamen is overschreden voor een aantal gekoppelde schijven

De momentopname bewerking is mislukt omdat de limiet voor de moment opname is overschreden voor sommige van de gekoppelde schijven. Voer de onderstaande stappen voor probleem oplossing uit en voer de bewerking opnieuw uit.

* Verwijder de schijf-BLOB-moment opnamen die niet vereist zijn. Wees voorzichtig met het verwijderen van de schijf-blob, alleen de moment opname-blobs moeten worden verwijderd.
* Als de functie voor het voorlopig verwijderen van VM-opslag accounts is ingeschakeld, moet u de Bewaar periode voor tijdelijke verwijdering zodanig configureren dat bestaande moment opnamen kleiner zijn dan het maximum aantal dat is toegestaan op elk gewenst moment.
* Als Azure Site Recovery is ingeschakeld in de virtuele machine waarvan een back-up is gemaakt, voert u de onderstaande stappen uit:

    * Zorg ervoor dat de waarde van **isanysnapshotfailed** is ingesteld op False in/etc/Azure/vmbackup.conf
    * Plan Azure Site Recovery op een ander tijdstip, zodat de back-upbewerking geen conflict veroorzaakt.

## <a name="extensionfailedtimeoutvmnetworkunresponsive---snapshot-operation-failed-due-to-inadequate-vm-resources"></a>ExtensionFailedTimeoutVMNetworkUnresponsive-momentopname bewerking is mislukt vanwege onvoldoende VM-resources.

Fout code: ExtensionFailedTimeoutVMNetworkUnresponsive<br/>
Foutbericht: De momentopname bewerking is mislukt vanwege onvoldoende VM-resources.

De back-upbewerking op de virtuele machine is mislukt vanwege een vertraging in netwerk aanroepen tijdens het uitvoeren van de momentopname bewerking. U kunt dit probleem oplossen door stap 1 uit te voeren. Als het probleem zich blijft voordoen, probeert u stappen 2 en 3.

**Stap 1**: Momentopname maken via host

Voer vanaf een opdrachtprompt met verhoogde bevoegdheid (beheerder) de onderstaande opdracht uit:

```text
REG ADD "HKLM\SOFTWARE\Microsoft\BcdrAgentPersistentKeys" /v SnapshotMethod /t REG_SZ /d firstHostThenGuest /f
REG ADD "HKLM\SOFTWARE\Microsoft\BcdrAgentPersistentKeys" /v CalculateSnapshotTimeFromHost /t REG_SZ /d True /f
```

Deze opdracht zorgt ervoor dat de momentopnamen worden gemaakt via host in plaats van Guest. Probeer de back-upbewerking opnieuw.

**Stap 2**: Wijzig het back-upschema naar een tijd wanneer de virtuele machine minder belasting heeft (minder CPU/IOps enzovoort.)

**Stap 3**: [Verhoog de grootte van de virtuele machine](https://azure.microsoft.com/blog/resize-virtual-machines/) en voer de bewerking opnieuw uit

## <a name="common-vm-backup-errors"></a>Veelvoorkomende fouten bij VM-back-ups

| Details van fout | Tijdelijke oplossing |
| ------ | --- |
| **Fout code**: 320001<br/> **Fout bericht**: Kan de bewerking niet uitvoeren omdat de VM niet meer bestaat. <br/> <br/> **Fout code**: 400094 <br/> **Fout bericht**: De virtuele machine bestaat niet <br/> <br/>  Een virtuele machine van Azure is niet gevonden.  |Deze fout treedt op wanneer de primaire virtuele machine wordt verwijderd, maar er door het back-upbeleid wordt gezocht naar een virtuele machine waarvan een back-up moet worden gemaakt. Voer de volgende stappen uit om deze fout op te lossen: <ol><li> Maak de virtuele machine opnieuw met dezelfde naam en dezelfde naam voor de resource groep, naam van **Cloud service**,<br>**or**</li><li> Stop de beveiliging van de virtuele machine met of zonder de back-upgegevens te verwijderen. Zie [beveiliging van virtuele machines stoppen](backup-azure-manage-vms.md#stop-protecting-a-vm)voor meer informatie.</li></ol>|
| De inrichtings status van de virtuele machine is mislukt: <br>Start de virtuele machine opnieuw op en controleer of de VM wordt uitgevoerd of wordt afgesloten. | Deze fout treedt op wanneer een van de uitbrei dingen de virtuele machine in een mislukte inrichtings status zet. Ga naar de lijst extensies, Controleer of er een uitbrei ding is mislukt, verwijder deze en probeer de virtuele machine opnieuw op te starten. Als alle uitbrei dingen de status bezig met uitvoeren hebben, controleert u of de VM-Agent service wordt uitgevoerd. Als dat niet het geval is, start u de VM-Agent service opnieuw. |
|**Fout code**: UserErrorBCMPremiumStorageQuotaError<br/> **Fout bericht**: Kan de moment opname van de virtuele machine niet kopiëren omdat het opslag account onvoldoende beschik bare ruimte heeft | Voor Premium-Vm's in VM-back-upstack v1 wordt de moment opname naar het opslag account gekopieerd. Met deze stap zorgt u ervoor dat het verkeer voor back-upbeheer, dat werkt op de moment opname, het aantal IOPS dat beschikbaar is voor de toepassing niet beperkt met Premium-schijven. <br><br>We raden u aan om slechts 50 procent, 17,5 TB, van de totale opslag account ruimte toe te wijzen. Vervolgens kan de Azure Backup-service de moment opname naar het opslag account kopiëren en gegevens van deze gekopieerde locatie in het opslag account naar de kluis overdragen. |
| **Fout code: 380008** <br/> **Fout bericht**: Kan de micro soft Recovery Services-extensie niet installeren omdat de virtuele machine niet actief is | De VM-agent is een vereiste voor de Azure Recovery Services-extensie. Installeer de Azure virtual machine agent en start de registratie bewerking opnieuw. <br> <ol> <li>Controleer of de VM-agent correct is geïnstalleerd. <li>Zorg ervoor dat de vlag op de configuratie van de virtuele machine juist is ingesteld.</ol> Lees meer informatie over het installeren van de VM-agent en het valideren van de installatie van de VM-agent. |
| **Fout code**: ExtensionSnapshotBitlockerError <br/> **Fout bericht**: De momentopname bewerking is mislukt vanwege een fout **in de Volume Shadow Copy service (VSS)-bewerking dit station is vergrendeld door BitLocker-stationsversleuteling. U moet dit station ontgrendelen via het configuratie scherm.** |Schakel BitLocker uit voor alle stations op de VM en controleer of het VSS-probleem is opgelost. |
| **Fout code**: VmNotInDesirableState <br/> **Fout bericht**:  De VM bevindt zich niet in een status waarin back-ups zijn toegestaan. |<ul><li>Als de virtuele machine zich in een tijdelijke status bevindt tussen het **uitvoeren** en **Afsluiten**, wacht u totdat de status is gewijzigd. Activeer vervolgens de back-uptaak. <li> Als de virtuele machine een virtuele Linux-machine is en gebruikmaakt van de Security Enhanced Linux kernel-module, sluit u het pad van de Azure Linux-agent uit het beveiligings beleid uit en zorgt u ervoor dat de back-upextensie is geïnstalleerd.  |
| De VM-agent is niet aanwezig op de virtuele machine: <br>Installeer de vereiste onderdelen en de VM-agent. Start vervolgens de bewerking opnieuw. |Meer informatie over de installatie van de [VM-agent en het valideren](#vm-agent)van de installatie van de VM-agent. |
| **Fout code**: ExtensionSnapshotFailedNoSecureNetwork <br/> **Fout bericht**: De momentopname bewerking is mislukt vanwege een fout bij het maken van een beveiligd kanaal voor netwerk communicatie. | <ol><li> Open de REGI ster-editor door **regedit. exe** uit te voeren in een modus met verhoogde bevoegdheden. <li> Alle versies van de .NET Framework in uw systeem identificeren. Ze bevinden zich in de hiërarchie van register sleutel **HKEY_LOCAL_MACHINE\Software\Microsoft**. <li> Voor elke .NET Framework die in de register sleutel aanwezig is, voegt u de volgende sleutel toe: <br> **SchUseStrongCrypto"=dword:00000001**. </ol>|
| **Fout code**: ExtensionVCRedistInstallationFailure <br/> **Fout bericht**: De momentopname bewerking is mislukt vanwege een fout bij het C++ installeren van Visual Redistributable voor visual studio 2012. | Navigeer naar C:\Packages\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot\agentVersion en installeer vcredist2012_x64.<br/>Zorg ervoor dat de waarde van de register sleutel die de service-installatie toestaat, is ingesteld op de juiste waarde. Stel de **begin** waarde in **HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Msiserver** in op **3** en niet **4**. <br><br>Als u nog steeds problemen ondervindt met de installatie, start u de installatie service opnieuw door **Msiexec/unregister** gevolgd door **Msiexec/register** vanaf een opdracht prompt met verhoogde bevoegdheid uit te voeren.  |


## <a name="jobs"></a>Taken

| Details van fout | Tijdelijke oplossing |
| --- | --- |
| Annulering wordt niet ondersteund voor dit taak type: <br>Wacht tot de taak is voltooid. |Geen |
| De taak heeft een niet-Annuleer bare status: <br>Wacht tot de taak is voltooid. <br>**or**<br> De geselecteerde taak heeft een niet-Annuleer bare status: <br>Wacht tot de taak is voltooid. |Het is waarschijnlijk dat de taak bijna is voltooid. Wacht tot de taak is voltooid.|
| De taak kan niet worden geannuleerd omdat deze niet wordt uitgevoerd: <br>Annulering wordt alleen ondersteund voor taken die worden uitgevoerd. Probeer een taak die in voortgang is te annuleren. |Deze fout treedt op vanwege een tijdelijke status. Wacht een minuut en voer de annulerings bewerking opnieuw uit. |
| De back-up kan de taak niet annuleren: <br>Wacht tot de taak is voltooid. |Geen |

## <a name="restore"></a>Herstellen

| Details van fout | Tijdelijke oplossing |
| --- | --- |
| Het herstellen is mislukt vanwege een interne cloud fout. |<ol><li>De Cloud service waarnaar u wilt herstellen, is geconfigureerd met DNS-instellingen. U kunt het volgende controleren: <br>**$Deployment = Get-Azure-servicenaam "servicenaam"-sleuf "productie" Get-AzureDns-DnsSettings $Deployment. DnsSettings**.<br>Als het **adres** is geconfigureerd, worden de DNS-instellingen geconfigureerd.<br> <li>De Cloud service waarnaar u wilt herstellen, is geconfigureerd met **ReservedIP**, en bestaande vm's in de Cloud service hebben de status gestopt. U kunt controleren of een Cloud service een IP-adres heeft gereserveerd met behulp van de volgende Power shell-cmdlets: **$Deployment = Get-Azure-ServiceName "servicenaam"-sleuf "Production" $DEP. ReservedIPName**. <br><li>U probeert een virtuele machine met de volgende speciale netwerk configuraties te herstellen in dezelfde Cloud service: <ul><li>Virtuele machines onder load balancer configuratie, intern en extern.<li>Virtuele machines met meerdere gereserveerde Ip's. <li>Virtuele machines met meerdere Nic's. </ul><li>Selecteer een nieuwe Cloud service in de gebruikers interface of Zie [herstel overwegingen](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) voor vm's met speciale netwerk configuraties.</ol> |
| De geselecteerde DNS-naam wordt al gebruikt: <br>Geef een andere DNS-naam op en probeer het opnieuw. |Deze DNS-naam verwijst naar de naam van de Cloud service, meestal eindigt op **. cloudapp.net**. Deze naam moet uniek zijn. Als u deze fout ontvangt, moet u tijdens het herstellen een andere VM-naam kiezen. <br><br> Deze fout wordt alleen weer gegeven voor gebruikers van de Azure Portal. De herstel bewerking via Power shell slaagt omdat hiermee alleen de schijven worden hersteld en de virtuele machine niet wordt gemaakt. De fout wordt weer wanneer de virtuele machine expliciet door u is gemaakt nadat de bewerking voor het terugzetten van de schijf is uitgevoerd. |
| De opgegeven virtuele-netwerk configuratie is niet juist: <br>Geef een andere virtuele-netwerk configuratie op en probeer het opnieuw. |Geen |
| De opgegeven Cloud service maakt gebruik van een gereserveerde IP die niet overeenkomt met de configuratie van de virtuele machine die wordt hersteld: <br>Geef een andere Cloud service op die geen gereserveerd IP-adres gebruikt. Of kies een ander herstel punt waarvan u wilt herstellen. |Geen |
| De limiet voor het aantal invoer eindpunten voor de Cloud service is bereikt: <br>Voer de bewerking opnieuw uit door een andere Cloud service op te geven of door een bestaand eind punt te gebruiken. |Geen |
| De Recovery Services kluis en het doel-opslag account bevinden zich in twee verschillende regio's: <br>Zorg ervoor dat het opslag account dat is opgegeven in de herstel bewerking zich in dezelfde Azure-regio bevindt als uw Recovery Services kluis. |Geen |
| Het opslag account dat is opgegeven voor de herstel bewerking, wordt niet ondersteund: <br>Alleen Basic-of Standard-opslag accounts met lokaal redundante of geo-redundante replicatie-instellingen worden ondersteund. Selecteer een ondersteund opslag account. |Geen |
| Het type opslag account dat is opgegeven voor de herstel bewerking is niet online: <br>Zorg ervoor dat het opslag account dat is opgegeven in de herstel bewerking online is. |Deze fout kan optreden vanwege een tijdelijke fout in Azure Storage of vanwege een storing. Kies een ander opslag account. |
| Het quotum voor de resource groep is bereikt: <br>Verwijder enkele resource groepen uit het Azure Portal of neem contact op met de ondersteuning van Azure om de limieten te verhogen. |Geen |
| Het geselecteerde subnet bestaat niet: <br>Selecteer een bestaand subnet. |Geen |
| De back-upservice heeft geen toestemming voor toegang tot resources in uw abonnement. |Om deze fout op te lossen, moet u eerst schijven terugzetten met behulp van de stappen in [back-ups van schijven herstellen](backup-azure-arm-restore-vms.md#restore-disks). Gebruik vervolgens de Power shell-stappen in [een VM maken van herstelde schijven](backup-azure-vms-automation.md#restore-an-azure-vm). |

## <a name="backup-or-restore-takes-time"></a>Back-up of herstel kost tijd
Als uw back-up meer dan 12 uur duurt of als de herstel bewerking langer dan 6 uur duurt, raadpleegt u de [Aanbevolen procedures](backup-azure-vms-introduction.md#best-practices) en [prestatie overwegingen](backup-azure-vms-introduction.md#backup-performance)

## <a name="vm-agent"></a>VM-agent
### <a name="set-up-the-vm-agent"></a>De VM-agent instellen
Normaal gesp roken is de VM-agent al aanwezig in de Vm's die zijn gemaakt in de Azure-galerie. Virtuele machines die zijn gemigreerd vanuit on-premises data centers, hebben geen VM-agent geïnstalleerd. Voor deze Vm's moet de VM-agent expliciet worden geïnstalleerd.

#### <a name="windows-vms"></a>Windows-VM's

* Download en installeer de [agent-MSI](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). U hebt beheerders bevoegdheden nodig om de installatie te volt ooien.
* Voor virtuele machines die zijn gemaakt met behulp van het klassieke implementatie model, [werkt u de eigenschap VM](https://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) bij om aan te geven dat de agent is geïnstalleerd. Deze stap is niet vereist voor het Azure Resource Manager van virtuele machines.

#### <a name="linux-vms"></a>Linux VM's

* Installeer de nieuwste versie van de agent vanuit de distributie opslagplaats. Raadpleeg de [opslag plaats](https://github.com/Azure/WALinuxAgent)van de Linux-agent voor meer informatie over de pakket naam.
* Voor virtuele machines die zijn gemaakt met behulp van het klassieke implementatie model, [gebruikt u deze blog](https://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) om de VM-eigenschap bij te werken en te controleren of de agent is geïnstalleerd. Deze stap is niet vereist voor virtuele machines van Resource Manager.

### <a name="update-the-vm-agent"></a>De VM-agent bijwerken
#### <a name="windows-vms"></a>Windows-VM's

* Als u de VM-agent wilt bijwerken, installeert u de [binaire bestanden](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)van de VM-agent opnieuw. Voordat u de agent bijwerkt, moet u ervoor zorgen dat er geen back-upbewerkingen worden uitgevoerd tijdens de VM-agent update.

#### <a name="linux-vms"></a>Linux VM's

* Als u de Linux VM-agent wilt bijwerken, volgt u de instructies in het artikel [de Linux VM-agent bijwerken](../virtual-machines/linux/update-agent.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

    > [!NOTE]
    > Gebruik altijd de distributie opslagplaats om de agent bij te werken.

    Down load de agent code niet van GitHub. Als de meest recente agent niet beschikbaar is voor uw distributie, neemt u contact op met de distributie ondersteuning voor instructies voor het verkrijgen van de nieuwste agent. U kunt ook de meest recente informatie over [Windows Azure Linux](https://github.com/Azure/WALinuxAgent/releases) -agents in de GitHub-opslag plaats controleren.

### <a name="validate-vm-agent-installation"></a>Installatie van VM-agent valideren

Controleer de versie van de VM-agent op Windows-Vm's:

1. Meld u aan bij de virtuele machine van Azure en navigeer naar de map **C:\WindowsAzure\Packages**. U vindt het bestand **WaAppAgent. exe** .
2. Klik met de rechter muisknop op het bestand en ga naar **Eigenschappen**. Selecteer vervolgens het tabblad **Details** . Het veld **product versie** moet 2.6.1198.718 of hoger zijn.

## <a name="troubleshoot-vm-snapshot-issues"></a>Problemen met VM-moment opnamen oplossen
VM-back-up is afhankelijk van het uitgeven van momentopname opdrachten aan onderliggende opslag. Als u geen toegang hebt tot opslag of vertragingen tijdens het uitvoeren van een taak voor een moment opname, kan de back-uptaak mislukken. De volgende voor waarden kunnen leiden tot een fout in een moment opname taak:

- **Netwerk toegang tot opslag wordt geblokkeerd met behulp van NSG**. Meer informatie over het [tot stand brengen van netwerk toegang](backup-azure-arm-vms-prepare.md#establish-network-connectivity) tot opslag met behulp van de lijst met toegestane IP-adressen of een proxy server.
- **Vm's met SQL Server back-up die is geconfigureerd, kunnen een vertraging voor de taak moment opname veroorzaken**. Standaard maakt VM-back-up een volledige VSS-back-up op Windows-Vm's. Vm's met SQL Server, met SQL Server back-up geconfigureerd, kunnen vertragingen in moment opnamen ondervinden. Als de vertraging van moment opnamen back-upfouten veroorzaken, stelt u de volgende register sleutel in:

   ```text
   [HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT]
   "USEVSSCOPYBACKUP"="TRUE"
   ```

- **De status van de virtuele machine wordt onjuist gerapporteerd, omdat de VM is afgesloten in RDP**. Als u het extern bureau blad hebt gebruikt om de virtuele machine af te sluiten, controleert u of de VM-status in de portal juist is. Als de status niet juist is, gebruikt u de optie voor **Afsluiten** in het dash board van de portal-VM om de virtuele machine af te sluiten.
- **Als meer dan vier vm's dezelfde Cloud service delen, verspreidt u de virtuele machines over meerdere back-upbeleid**. Spreid de back-uptijden zodat er niet meer dan vier VM-back-ups tegelijk worden gestart. Probeer de start tijden in het beleid met ten minste een uur te scheiden.
- **De virtuele machine wordt uitgevoerd op een hoog CPU-of geheugen niveau**. Als de virtuele machine wordt uitgevoerd op een hoog geheugen of CPU-gebruik, meer dan 90 procent, wordt uw momentopname taak in de wachtrij geplaatst en vertraagd. Er is uiteindelijk een time-out opgegaan. Als dit probleem optreedt, probeert u een back-up op aanvraag uit te voeren.

## <a name="networking"></a>Netwerken
Net als alle uitbrei dingen hebben back-upextensies toegang tot het open bare Internet nodig. Geen toegang tot het open bare Internet kan zich op verschillende manieren manifesteren:

* De installatie van de extensie kan niet worden uitgevoerd.
* Back-upbewerkingen zoals moment opnamen van schijven kunnen mislukken.
* Het weer geven van de status van de back-upbewerking kan mislukken.

De nood zaak om open bare Internet adressen op te lossen wordt beschreven in [deze Azure](https://blogs.msdn.com/b/mast/archive/2014/06/18/azure-vm-provisioning-stuck-on-quot-installing-extensions-on-virtual-machine-quot.aspx)-ondersteunings blog. Controleer de DNS-configuraties voor het VNET en controleer of de Azure-Uri's kunnen worden omgezet.

Nadat de naam omzetting correct is uitgevoerd, moet u ook toegang tot de Azure-Ip's geven. Voer een van de volgende stappen uit om de toegang tot de Azure-infra structuur te blok keren:

- Lijst met IP-bereiken van Azure Data Center toestaan:
   1. Haal de lijst met [IP-adressen van data centers](https://www.microsoft.com/download/details.aspx?id=41653) op die zich in de acceptatie lijst bevinden.
   1. Deblokkeren van de IP-adressen met behulp van de cmdlet [New-netroute](https://docs.microsoft.com/powershell/module/nettcpip/new-netroute) . Voer deze cmdlet uit in een Power shell-venster met verhoogde bevoegdheden in de Azure-VM. Als administrator uitvoeren.
   1. Voeg regels toe aan de NSG, als u er een hebt in, om toegang tot de IP-adressen toe te staan.
- Maak een pad voor het HTTP-verkeer dat moet worden geplaatst:
   1. Als er een netwerk beperking aanwezig is, implementeert u een HTTP-proxy server om het verkeer te routeren. Een voor beeld is een netwerk beveiligings groep. Zie de stappen voor het implementeren van een HTTP-proxy server bij het [maken van een netwerk verbinding](backup-azure-arm-vms-prepare.md#establish-network-connectivity).
   1. Voeg regels toe aan de NSG, als u er één hebt, om toegang tot internet vanaf de HTTP-proxy toe te staan.

> [!NOTE]
> DHCP moet zijn ingeschakeld in de gast voor het werken met IaaS VM-back-up. Als u een statisch privé IP-adres nodig hebt, configureert u dit via de Azure Portal of Power shell. Zorg ervoor dat de DHCP-optie in de virtuele machine is ingeschakeld.
> Meer informatie over het instellen van een statisch IP-adres via Power shell:
> - [Een statisch intern IP-adres toevoegen aan een bestaande virtuele machine](../virtual-network/virtual-networks-reserved-private-ip.md#how-to-add-a-static-internal-ip-to-an-existing-vm)
> - [De toewijzings methode wijzigen voor een privé-IP-adres dat is toegewezen aan een netwerk interface](../virtual-network/virtual-networks-static-private-ip-arm-ps.md#change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface)
>
>