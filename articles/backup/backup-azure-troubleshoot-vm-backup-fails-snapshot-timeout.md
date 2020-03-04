---
title: Problemen met agent-en uitbrei dingen oplossen
description: Symptomen, oorzaken en oplossingen voor Azure Backup fouten met betrekking tot agent, uitbrei ding en schijven.
ms.reviewer: saurse
ms.topic: troubleshooting
ms.date: 07/05/2019
ms.service: backup
ms.openlocfilehash: beb20518d1350335ceed285f4d5cd9da135132e5
ms.sourcegitcommit: d4a4f22f41ec4b3003a22826f0530df29cf01073
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2020
ms.locfileid: "78255736"
---
# <a name="troubleshoot-azure-backup-failure-issues-with-the-agent-or-extension"></a>Azure Backup fout oplossen: problemen met de agent of extensie

Dit artikel bevat probleemoplossings stappen die u kunnen helpen bij het oplossen van Azure Backup fouten met betrekking tot communicatie met de VM-agent en-extensie.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="UserErrorGuestAgentStatusUnavailable-vm-agent-unable-to-communicate-with-azure-backup"></a>UserErrorGuestAgentStatusUnavailable: de VM-agent kan niet communiceren met Azure Backup

**Fout code**: UserErrorGuestAgentStatusUnavailable <br>
**Fout bericht**: de VM-agent kan niet communiceren met Azure backup<br>

De Azure VM-agent is mogelijk gestopt, verouderd, in een inconsistente status of niet geïnstalleerd. Deze statussen verhinderen dat de Azure Backup-Service moment opnamen kan starten.

- **Open Azure Portal > VM-> instellingen > deel venster eigenschappen** > Controleer of de **status** van de VM **actief** is en de **agent status** **gereed**is. Als de VM-agent is gestopt of een inconsistente status heeft, start u de agent opnieuw op<br>
  - Voor virtuele Windows-machines voert u de volgende [stappen uit](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms) om de gast agent opnieuw te starten.<br>
  - Voor Linux Vm's voert u deze [stappen uit](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms) om de gast agent opnieuw te starten.
- **Open Azure Portal > VM > instellingen > extensies** > ervoor te zorgen dat alle uitbrei dingen de status **voltooid** hebben. Als dat niet het geval is, volgt u deze [stappen](https://docs.microsoft.com/azure/backup/backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout#usererrorvmprovisioningstatefailed---the-vm-is-in-failed-provisioning-state) om het probleem op te lossen.

## <a name="guestagentsnapshottaskstatuserror---could-not-communicate-with-the-vm-agent-for-snapshot-status"></a>GuestAgentSnapshotTaskStatusError: kan niet communiceren met de VM-agent voor de status van de moment opname

**Fout code**: GuestAgentSnapshotTaskStatusError<br>
**Fout bericht**: kan niet communiceren met de VM-agent voor de status van de moment opname <br>

Nadat u een virtuele machine voor de Azure Backup-service hebt geregistreerd en gepland, wordt de taak door de back-up gestart door te communiceren met de back-upextensie van de VM om een moment opname van een bepaald tijdstip te maken. Een van de volgende situaties kan verhinderen dat de moment opname wordt geactiveerd. Als de moment opname niet wordt geactiveerd, kan er een back-upfout optreden. Voer de volgende stappen voor probleem oplossing in de aangegeven volg orde uit en voer de bewerking opnieuw uit:  

**Oorzaak 1: [de agent is geïnstalleerd op de VM, maar reageert niet (voor Windows-vm's)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  

**Oorzaak 2: [de agent die is geïnstalleerd in de virtuele machine is verouderd (voor Linux-vm's)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**

**Oorzaak 3: [de status van de moment opname kan niet worden opgehaald of een moment opname kan niet worden gemaakt](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**

**Oorzaak 4: er [zijn geen configuratie opties voor de VM-agent ingesteld (voor Linux-vm's)](#vm-agent-configuration-options-are-not-set-for-linux-vms)**

## <a name="usererrorvmprovisioningstatefailed---the-vm-is-in-failed-provisioning-state"></a>UserErrorVmProvisioningStateFailed: de virtuele machine heeft een mislukte inrichtings status

**Fout code**: UserErrorVmProvisioningStateFailed<br>
**Fout bericht**: de inrichtings status van de virtuele machine is mislukt<br>

Deze fout treedt op wanneer een van de uitbrei dingen de virtuele machine inricht bij het inrichten van de status mislukt.<br>**Open Azure Portal > VM > instellingen > extensies > uitbrei dingen status** en controleer of alle uitbrei dingen de status **geslaagd** hebben.

- Als de extensie VMSnapshot de status Mislukt heeft, klikt u met de rechter muisknop op de uitbrei ding die is mislukt en verwijdert u deze. Activeer een back-up op aanvraag. Met deze actie worden de uitbrei dingen opnieuw geïnstalleerd en wordt de back-uptaak uitgevoerd.  <br>
- Als een andere uitbrei ding een mislukte status heeft, kan deze de back-up verstoren. Zorg ervoor dat de problemen met de extensie zijn opgelost en voer de back-upbewerking opnieuw uit.  

## <a name="usererrorrpcollectionlimitreached---the-restore-point-collection-max-limit-has-reached"></a>UserErrorRpCollectionLimitReached-de maximale limiet voor de verzameling met herstel punten is bereikt

**Fout code**: UserErrorRpCollectionLimitReached <br>
**Fout bericht**: de maximale limiet voor de verzameling met herstel punten is bereikt. <br>

- Dit probleem kan zich voordoen als er een vergren deling van de resource groep herstel punt wordt voor komen dat het automatisch opruimen van herstel punten wordt verhinderd.
- Dit probleem kan ook optreden als meerdere back-ups per dag worden geactiveerd. Momenteel wordt slechts één back-up per dag aanbevolen, omdat de onmiddellijke herstel punten gedurende 1-5 dagen worden bewaard per de geconfigureerde moment opname en slechts 18 direct RPs kunnen worden gekoppeld aan een VM op een bepaald moment. <br>
- Het aantal herstel punten tussen herstel punt verzamelingen en resource groepen voor een virtuele machine mag niet groter zijn dan 18. Als u een nieuw herstel punt wilt maken, verwijdert u de bestaande herstel punten.

Aanbevolen actie:<br>
U kunt dit probleem oplossen door de vergren deling van de resource groep van de virtuele machine te verwijderen en de bewerking opnieuw uit te voeren om het opschonen te activeren.
> [!NOTE]
> Backup-service maakt een afzonderlijke resource groep dan de resource groep van de virtuele machine om de herstel punt verzameling op te slaan. Klanten wordt aangeraden de resource groep die is gemaakt voor gebruik door de back-upservice niet te vergren delen. De naamgevings indeling van de resource groep die is gemaakt door de back-upservice is: AzureBackupRG_`<Geo>`_`<number>` bijvoorbeeld: AzureBackupRG_northeurope_1

**Stap 1: [de vergren deling van de resource groep voor het herstel punt verwijderen](#remove_lock_from_the_recovery_point_resource_group)** <br>
**Stap 2: [herstel punt verzameling opschonen](#clean_up_restore_point_collection)**<br>

## <a name="usererrorkeyvaultpermissionsnotconfigured---backup-doesnt-have-sufficient-permissions-to-the-key-vault-for-backup-of-encrypted-vms"></a>UserErrorKeyvaultPermissionsNotConfigured-backup heeft niet voldoende machtigingen voor de sleutel kluis voor het maken van een back-up van versleutelde Vm's

**Fout code**: UserErrorKeyvaultPermissionsNotConfigured <br>
**Fout bericht**: back-up beschikt niet over voldoende machtigingen voor de sleutel kluis voor het maken van back-ups van versleutelde vm's. <br>

Een back-upbewerking kan alleen worden uitgevoerd op versleutelde Vm's als deze over machtigingen voor toegang tot de sleutel kluis beschikt. Machtigingen kunnen worden ingesteld via de [Azure Portal](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption) of via [Power shell](https://docs.microsoft.com/azure/backup/backup-azure-vms-automation#enable-protection).

## <a name="ExtensionSnapshotFailedNoNetwork-snapshot-operation-failed-due-to-no-network-connectivity-on-the-virtual-machine"></a>ExtensionSnapshotFailedNoNetwork-momentopname bewerking is mislukt omdat er geen netwerk verbinding is op de virtuele machine

**Fout code**: ExtensionSnapshotFailedNoNetwork<br>
**Fout bericht**: de momentopname bewerking is mislukt omdat er geen netwerk verbinding is op de virtuele machine<br>

Nadat u een virtuele machine voor de Azure Backup-service hebt geregistreerd en gepland, wordt de taak door de back-up gestart door te communiceren met de back-upextensie van de VM om een moment opname van een bepaald tijdstip te maken. Een van de volgende situaties kan verhinderen dat de moment opname wordt geactiveerd. Als de moment opname niet wordt geactiveerd, kan er een back-upfout optreden. Voer de volgende stap voor het oplossen van problemen uit en voer de bewerking opnieuw uit:

**[De status van de moment opname kan niet worden opgehaald, of een moment opname kan niet worden gemaakt](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  

## <a name="ExtensionOperationFailed-vmsnapshot-extension-operation-failed"></a>Bewerking voor ExtensionOperationFailedForManagedDisks-VMSnapshot-extensie is mislukt

**Fout code**: ExtensionOperationFailedForManagedDisks <br>
**Fout bericht**: de VMSnapshot-extensie bewerking is mislukt<br>

Nadat u een virtuele machine voor de Azure Backup-service hebt geregistreerd en gepland, wordt de taak door de back-up gestart door te communiceren met de back-upextensie van de VM om een moment opname van een bepaald tijdstip te maken. Een van de volgende situaties kan verhinderen dat de moment opname wordt geactiveerd. Als de moment opname niet wordt geactiveerd, kan er een back-upfout optreden. Voer de volgende stappen voor probleem oplossing in de aangegeven volg orde uit en voer de bewerking opnieuw uit:  
**Oorzaak 1: [de status van de moment opname kan niet worden opgehaald of een moment opname kan niet worden gemaakt](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**Oorzaak 2: [de agent is geïnstalleerd in de VM, maar reageert niet (voor Windows-vm's)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**Oorzaak 3: [de agent die is geïnstalleerd in de virtuele machine is verouderd (voor Linux-vm's)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**

## <a name="backupoperationfailed--backupoperationfailedv2---backup-fails-with-an-internal-error"></a>BackUpOperationFailed/BackUpOperationFailedV2-back-up mislukt, met een interne fout

**Fout code**: BackUpOperationFailed/BackUpOperationFailedV2 <br>
**Fout bericht**: de back-up is mislukt met een interne fout. Voer de bewerking over een paar minuten opnieuw uit <br>

Nadat u een virtuele machine voor de Azure Backup-service hebt geregistreerd en gepland, wordt met de back-up de taak gestart door te communiceren met de back-upextensie van de VM om een tijdgebonden moment opname te maken. Een van de volgende situaties kan verhinderen dat de moment opname wordt geactiveerd. Als de moment opname niet wordt geactiveerd, kan er een back-upfout optreden. Voer de volgende stappen voor probleem oplossing in de aangegeven volg orde uit en voer de bewerking opnieuw uit:  
**Oorzaak 1: [de agent die is geïnstalleerd op de VM, maar die niet reageert (voor Windows-vm's)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**Oorzaak 2: [de agent die is geïnstalleerd in de virtuele machine is verouderd (voor Linux-vm's)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**Oorzaak 3: [de status van de moment opname kan niet worden opgehaald of een moment opname kan niet worden gemaakt](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**Oorzaak 4: [de back-upservice heeft geen machtiging voor het verwijderen van de oude herstel punten vanwege een vergren deling van een resource groep](#remove_lock_from_the_recovery_point_resource_group)**<br>

## <a name="usererrorunsupporteddisksize---the-configured-disk-sizes-is-currently-not-supported-by-azure-backup"></a>UserErrorUnsupportedDiskSize: de geconfigureerde schijf grootte (s) wordt momenteel niet ondersteund door de Azure Backup

**Fout code**: UserErrorUnsupportedDiskSize <br>
**Fout bericht**: de geconfigureerde schijf grootte (s) wordt momenteel niet ondersteund door Azure backup. <br>

De back-upbewerking kan mislukken bij het maken van een back-up van een virtuele machine met een schijf grootte van meer dan 32 TB. Het maken van een back-up van versleutelde schijven met een grootte van meer dan 4 TB wordt momenteel niet ondersteund. Zorg ervoor dat de schijf grootte (s) kleiner is dan of gelijk is aan de ondersteunde limiet door de schijven te splitsen.

## <a name="usererrorbackupoperationinprogress---unable-to-initiate-backup-as-another-backup-operation-is-currently-in-progress"></a>UserErrorBackupOperationInProgress: kan geen back-up initiëren omdat er momenteel een andere back-upbewerking wordt uitgevoerd

**Fout code**: UserErrorBackupOperationInProgress <br>
**Fout bericht**: kan de back-up niet starten omdat er momenteel een andere back-upbewerking wordt uitgevoerd<br>

De recente back-uptaak is mislukt, omdat er een bestaande back-uptaak wordt uitgevoerd. U kunt pas een nieuwe back-uptaak starten als de huidige taak is voltooid. Controleer of de back-upbewerking die momenteel wordt uitgevoerd, is voltooid voordat u een andere back-up maakt of plant. Als u de status van de back-uptaken wilt controleren, voert u de volgende stappen uit:

1. Meld u aan bij de Azure Portal en klik op **alle services**. Typ Recovery Services en klik op **Recovery Services-kluizen**. De lijst met Recovery Services-kluizen wordt weergegeven.
2. Selecteer in de lijst met Recovery Services-kluizen een kluis waarin de back-up is geconfigureerd.
3. Klik in het menu van het kluis dashboard op **back-uptaken** . alle back-uptaken worden weer gegeven.
   - Als er een back-uptaak wordt uitgevoerd, wacht u totdat deze klaar is of annuleert u de back-uptaak.
     - Als u de back-uptaak wilt annuleren, klikt u met de rechter muisknop op de back-uptaak en klikt u op **Annuleren** of [Power shell](https://docs.microsoft.com/powershell/module/az.recoveryservices/stop-azrecoveryservicesbackupjob?view=azps-1.4.0)gebruiken.
   - Als u de back-up opnieuw hebt geconfigureerd in een andere kluis, controleert u of er geen back-uptaken worden uitgevoerd in de oude kluis. Als deze bestaat, annuleert u de back-uptaak.
     - Als u de back-uptaak wilt annuleren, klikt u met de rechtermuisknop op de back-uptaak en klikt u op **Annuleren**, of gebruikt u [PowerShell](https://docs.microsoft.com/powershell/module/az.recoveryservices/stop-azrecoveryservicesbackupjob?view=azps-1.4.0)
4. Voer de back-upbewerking opnieuw uit.

Als de geplande back-upbewerking langer duurt, conflicteert met de volgende back-upconfiguratie, raadpleegt u de [Aanbevolen procedures](backup-azure-vms-introduction.md#best-practices), [back-upprestaties](backup-azure-vms-introduction.md#backup-performance)en [Restore-overwegingen](backup-azure-vms-introduction.md#backup-and-restore-considerations).

## <a name="usererrorcrpreportedusererror---backup-failed-due-to-an-error-for-details-see-job-error-message-details"></a>UserErrorCrpReportedUserError-back-up is mislukt vanwege een fout. Zie voor meer informatie taak fout bericht Details

**Fout code**: UserErrorCrpReportedUserError <br>
**Fout bericht**: het maken van de back-up is mislukt vanwege een fout. Zie Details van het taak fout bericht voor meer informatie.

Deze fout wordt gerapporteerd vanuit de IaaS-VM. Als u de hoofd oorzaak van het probleem wilt vaststellen, gaat u naar de Recovery Services kluis instellingen. Selecteer in de sectie **controle** de optie **back-uptaken** om de status te filteren en weer te geven. Klik op **fouten** om de details van het onderliggende fout bericht weer te geven. Onderneem verdere acties op basis van de aanbevelingen op de pagina met fout Details.

## <a name="causes-and-solutions"></a>Oorzaken en oplossingen

### <a name="the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>De agent is geïnstalleerd op de VM, maar reageert niet (voor Windows-Vm's)

#### <a name="solution"></a>Oplossing

De VM-agent is mogelijk beschadigd of de service is gestopt. Het opnieuw installeren van de VM-agent helpt de meest recente versie op te halen. Het helpt ook om communicatie met de service opnieuw te starten.

1. Bepaal of de Windows Azure Guest Agent-service wordt uitgevoerd in de VM-Services (Services. msc). Probeer de service Windows Azure Guest agent opnieuw te starten en de back-up te initiëren.
2. Als de Windows Azure Guest Agent-service niet wordt weer gegeven in Services, gaat u in het configuratie scherm naar **Program ma's en onderdelen** om te bepalen of de service Windows Azure Guest agent is geïnstalleerd.
3. Als de Windows Azure Guest-agent wordt weer gegeven in **Program ma's en onderdelen**, verwijdert u de Windows Azure-gast agent.
4. Down load en installeer de [nieuwste versie van de agent-MSI](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). U moet over beheerders rechten beschikken om de installatie te volt ooien.
5. Controleer of de services van de Windows Azure Guest agent in Services worden weer gegeven.
6. Een back-up op aanvraag uitvoeren:
   - Selecteer in de portal **Nu back-up maken**.

Controleer ook of [Microsoft .NET 4,5 is geïnstalleerd](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) in de VM. .NET 4,5 is vereist voor de VM-agent om te communiceren met de service.

### <a name="the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>De agent die is geïnstalleerd in de virtuele machine is verouderd (voor Linux-Vm's)

#### <a name="solution"></a>Oplossing

De meeste fout-en extensie-gerelateerde storingen voor Linux-Vm's worden veroorzaakt door problemen die van invloed zijn op een verouderde VM-agent. Volg deze algemene richt lijnen om dit probleem op te lossen:

1. Volg de instructies voor [het bijwerken van de Linux VM-agent](../virtual-machines/linux/update-agent.md).

   > [!NOTE]
   > We *raden u ten zeerste* aan de agent alleen bij te werken via een distributie opslagplaats. Het is niet raadzaam de agent code rechtstreeks vanuit GitHub te downloaden en bij te werken. Als de meest recente agent voor uw distributie niet beschikbaar is, neemt u contact op met de distributie ondersteuning voor instructies over het installeren ervan. Als u de meest recente agent wilt controleren, gaat u naar de pagina van de [Windows Azure Linux-agent](https://github.com/Azure/WALinuxAgent/releases) in de GitHub-opslag plaats.

2. Zorg ervoor dat de Azure-agent op de virtuele machine wordt uitgevoerd door de volgende opdracht uit te voeren: `ps -e`

   Als het proces niet wordt uitgevoerd, start u het opnieuw met behulp van de volgende opdrachten:

   - Voor Ubuntu: `service walinuxagent start`
   - Voor andere distributies: `service waagent start`

3. [Configureer de agent voor automatisch opnieuw opstarten](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash).
4. Voer een nieuwe test back-up uit. Als de fout zich blijft voordoen, verzamelt u de volgende logboeken van de VM:

   - /var/lib/waagent/*. XML
   - /var/log/waagent.log
   - /var/log/azure/*

Als u uitgebreide logboek registratie voor waagent nodig hebt, voert u de volgende stappen uit:

1. Zoek in het bestand/etc/waagent.conf de volgende regel: **uitgebreide logboek registratie inschakelen (y | n)**
2. Wijzig de **Logboeken. uitgebreide** waarde van *n* tot en met *y*.
3. Sla de wijziging op en start waagent opnieuw door de stappen uit te voeren die eerder in deze sectie zijn beschreven.

### <a name="vm-agent-configuration-options-are-not-set-for-linux-vms"></a>VM-agent configuratie opties zijn niet ingesteld (voor Linux-Vm's)

Een configuratie bestand (/etc/waagent.conf) regelt de acties van waagent. Extensie van configuratie bestand opties **. inschakelen** en **inrichten.** voor het werken moet de agent worden ingesteld op **y** voor back-up.
Zie <https://github.com/Azure/WALinuxAgent#configuration-file-options> voor een volledige lijst met opties voor het configuratie bestand van de VM-agent.

### <a name="the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken"></a>De status van de moment opname kan niet worden opgehaald, of een moment opname kan niet worden gemaakt

De back-up van de virtuele machine is afhankelijk van het uitgeven van een momentopname opdracht aan het onderliggende opslag account. Het maken van een back-up kan mislukken omdat deze geen toegang heeft tot het opslag account, of omdat de uitvoering van de momentopname taak is vertraagd.

#### <a name="solution"></a>Oplossing

De volgende voor waarden kunnen ertoe leiden dat de momentopname taak mislukt:

| Oorzaak | Oplossing |
| --- | --- |
| De status van de virtuele machine wordt onjuist gerapporteerd, omdat de VM wordt afgesloten in Remote Desktop Protocol (RDP). | Als u de virtuele machine in RDP afsluit, controleert u de portal om te bepalen of de status van de virtuele machine juist is. Als dat niet het geval is, sluit u de virtuele machine in de portal af met de optie **Afsluiten** in het VM-dash board. |
| De VM kan de host of het infrastructuur adres niet ophalen van DHCP. | DHCP moet zijn ingeschakeld in de gast voor het werken met de IaaS VM-back-up. Als de VM de host of het infrastructuur adres niet kan ophalen van DHCP-antwoord 245, kan er geen uitbrei dingen worden gedownload of uitgevoerd. Als u een statisch privé IP-adres nodig hebt, moet u dit configureren via de **Azure Portal** of **Power shell** en ervoor zorgen dat de DHCP-optie in de virtuele machine is ingeschakeld. Meer [informatie](../virtual-network/virtual-networks-static-private-ip-arm-ps.md#change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface) over het instellen van een statisch IP-adres met Power shell.

### <a name="remove_lock_from_the_recovery_point_resource_group"></a>Vergren deling van de resource groep herstel punt verwijderen

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
2. Ga naar **alle resources optie**, selecteer de resource groep voor de verzameling van herstel punten in de volgende indeling AzureBackupRG_`<Geo>`_`<number>`.
3. Selecteer in de sectie **instellingen** de optie **vergren** delen om de vergren delingen weer te geven.
4. Als u de vergren deling wilt verwijderen, selecteert u het weglatings teken en klikt u op **verwijderen**.

    ![Vergren deling verwijderen](./media/backup-azure-arm-vms-prepare/delete-lock.png)

### <a name="clean_up_restore_point_collection"></a>Herstel punt verzameling opruimen

Na het verwijderen van de vergren deling, moeten de herstel punten worden opgeruimd.

Als u de resource groep van de virtuele machine of de virtuele machine zelf verwijdert, blijven de moment opnamen voor direct terugzetten van beheerde schijven actief en verlopen op basis van de Bewaar termijn. Als u de moment opnamen voor direct terugzetten wilt verwijderen (als u deze niet meer nodig hebt) die zijn opgeslagen in de herstel punt verzameling, reinigt u de verzameling met herstel punten op basis van de onderstaande stappen.

Volg een van de volgende methoden om de herstel punten op te schonen:<br>

- [Herstel punt verzameling opschonen door back-ups op aanvraag uit te voeren](#clean-up-restore-point-collection-by-running-on-demand-backup)<br>
- [Herstel punt verzameling opschonen van Azure Portal](#clean-up-restore-point-collection-from-azure-portal)<br>

#### <a name="clean-up-restore-point-collection-by-running-on-demand-backup"></a>Herstel punt verzameling opschonen door back-ups op aanvraag uit te voeren

Nadat u de vergren deling hebt verwijderd, moet u een back-up op aanvraag starten. Met deze actie wordt ervoor gezorgd dat de herstel punten automatisch worden opgeruimd. Verwacht dat deze on-demand-bewerking de eerste keer mislukt. het zorgt er echter voor dat automatisch opschonen in plaats van het hand matig verwijderen van herstel punten. Na het opschonen moet de volgende geplande back-up slagen.

> [!NOTE]
> Automatische opschoning gebeurt na enkele uren dat de back-up op aanvraag wordt geactiveerd. Als uw geplande back-up nog steeds mislukt, probeert u de verzameling met herstel punten hand matig te verwijderen met behulp van de stappen die [hier](#clean-up-restore-point-collection-from-azure-portal)worden beschreven.

#### <a name="clean-up-restore-point-collection-from-azure-portal"></a>Herstel punt verzameling opschonen van Azure Portal <br>

Voer de volgende stappen uit om de verzameling met herstel punten hand matig te wissen, wat niet wordt gewist vanwege de vergren deling van de resource groep:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
2. Klik in het menu **hub** op **alle resources**en selecteer de resource groep met de volgende indeling AzureBackupRG_`<Geo>`_`<number>` waar de virtuele machine zich bevindt.

    ![Vergren deling verwijderen](./media/backup-azure-arm-vms-prepare/resource-group.png)

3. Klik op resource groep, het deel venster **overzicht** wordt weer gegeven.
4. Selecteer de optie **verborgen typen weer geven** om alle verborgen resources weer te geven. Selecteer de herstel punt verzamelingen met de volgende indeling AzureBackupRG_`<VMName>`_`<number>`.

    ![Vergren deling verwijderen](./media/backup-azure-arm-vms-prepare/restore-point-collection.png)

5. Klik op **verwijderen** om de verzameling met herstel punten op te schonen.
6. Voer de back-upbewerking opnieuw uit.

> [!NOTE]
 >Als de resource (RP-verzameling) een groot aantal herstel punten heeft, kan de time-out van de-portal worden verwijderd en mislukt. Dit is een bekend CRP-probleem, waarbij alle herstel punten niet worden verwijderd binnen de vastgestelde tijd en er een time-out optreedt voor de bewerking. de verwijderings bewerking slaagt echter normaal gesp roken na 2 of 3 nieuwe pogingen.
