---
title: Problemen met agenten en extensieoplossen
description: Symptomen, oorzaken en oplossingen van Azure Backup-fouten met betrekking tot agent, extensie en schijven.
ms.reviewer: saurse
ms.topic: troubleshooting
ms.date: 07/05/2019
ms.service: backup
ms.openlocfilehash: a3eedb5440711c7a45a13dcd53dd489c490588fc
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81677412"
---
# <a name="troubleshoot-azure-backup-failure-issues-with-the-agent-or-extension"></a>Azure Backup-fout oplossen: problemen met de agent of extensie

In dit artikel vindt u stappen voor het oplossen van problemen die u kunnen helpen azure back-upfouten op te lossen die verband houden met de communicatie met de VM-agent en extensie.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="usererrorguestagentstatusunavailable---vm-agent-unable-to-communicate-with-azure-backup"></a><a name="UserErrorGuestAgentStatusUnavailable-vm-agent-unable-to-communicate-with-azure-backup"></a>UserErrorGuestAgentStatusUnavailable - VM-agent kan niet communiceren met Azure Backup

**Foutcode**: UserErrorGuestAgentStatusNiet beschikbaar <br>
**Foutbericht:** VM-agent kan niet communiceren met Azure Backup<br>

De Azure VM-agent kan worden gestopt, verouderd, in een inconsistente status of niet geïnstalleerd. Deze statussen voorkomen dat de Azure Backup-service momentopnamen activeert.

- **Open het deelvenster Azure-portal > VM >->-instellingen > eigenschappen** > ervoor te zorgen dat de VM-status **wordt uitgevoerd** en de **agentstatus** **gereed**is. **Status** Als de VM-agent is gestopt of in een inconsistente toestand verkeert, start u de agent opnieuw<br>
  - Voer voor Windows VM's de volgende [stappen](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms) uit om de gastagent opnieuw te starten.<br>
  - Volg voor Linux-VM's deze [stappen](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms) om de gastagent opnieuw te starten.
- **Open Azure portal > VM > Instellingen > Extensies** > Zorg ervoor dat alle extensies de gewenste **status** inrichten. Als dit niet het zo is, volgt u deze [stappen](https://docs.microsoft.com/azure/backup/backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout#usererrorvmprovisioningstatefailed---the-vm-is-in-failed-provisioning-state) om het probleem op te lossen.

## <a name="guestagentsnapshottaskstatuserror---could-not-communicate-with-the-vm-agent-for-snapshot-status"></a>GuestAgentSnapshotTaskStatusError: Kan niet communiceren met de VM-agent voor de status van de momentopname

**Foutcode**: GuestAgentSnapshotTaskStatusError<br>
**Foutbericht:** kan niet communiceren met de VM-agent voor momentopnamestatus <br>

Nadat u een VM voor de Azure Backup-service hebt geregistreerd en gepland, start Back-up de taak door te communiceren met de VM-back-upextensie om een point-in-time momentopname te maken. Een van de volgende voorwaarden kan voorkomen dat de momentopname wordt geactiveerd. Als de momentopname niet wordt geactiveerd, kan er een back-upfout optreden. Voer de volgende stappen voor het oplossen van problemen in de vermelde volgorde uit en probeer de bewerking opnieuw:  

**Oorzaak 1: [de agent is geïnstalleerd in de VM, maar reageert niet (voor Windows VM's)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  

**Oorzaak 2: [De agent die in de VM is geïnstalleerd, is verouderd (voor Linux VM's)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**

**Oorzaak 3: [De momentopnamestatus kan niet worden opgehaald of een momentopname kan niet worden gemaakt](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**

**Oorzaak 4: [VM-Agent-configuratieopties zijn niet ingesteld (voor Linux VM's)](#vm-agent-configuration-options-are-not-set-for-linux-vms)**

## <a name="usererrorvmprovisioningstatefailed---the-vm-is-in-failed-provisioning-state"></a>UserErrorVmProvisioningStateFailed - De VM is in de status van inrichting

**Foutcode**: UserErrorVmProvisioningState Failed<br>
**Foutbericht:** de VM is in de status van inrichting<br>

Deze fout treedt op wanneer een van de extensiefouten de VM in de status inrichting saneren.<br>**Open de status van Azure-portal > VM > Instellingen > Extensies > extensies** en controleer of alle extensies de status van **inprovisionering hebben.** Zie [Provisioning-staten](https://docs.microsoft.com/azure/virtual-machines/windows/states-lifecycle#provisioning-states)voor meer informatie.

- Als de VMSnapshot-extensie in een mislukte status verkeert, klikt u met de rechtermuisknop op de mislukte extensie en verwijdert u deze. Activeer een on-demand back-up. Met deze actie worden de extensies opnieuw geïnstalleerd en wordt de back-uptaak uitgevoerd.  <br>
- Als een andere extensie is in een mislukte staat, dan kan interfereren met de back-up. Zorg ervoor dat deze uitbreidingsproblemen zijn opgelost en probeer de back-upbewerking opnieuw.
- Als de status van vm-inrichting in een updatestatus is, kan deze de back-up verstoren. Zorg ervoor dat deze in orde is en probeer de back-upbewerking opnieuw.

## <a name="usererrorrpcollectionlimitreached---the-restore-point-collection-max-limit-has-reached"></a>UserErrorRpCollectionLimitReached - De limiet voor de verzameling herstelpunten is bereikt

**Foutcode**: UserErrorRpCollectionLimitReached <br>
**Foutbericht:** de maximumlimiet voor de verzameling Herstelpunt is bereikt. <br>

- Dit probleem kan optreden als er een vergrendeling is op de resourcegroep herstelpunt die voorkomt dat herstelpunten automatisch worden opschonen.
- Dit probleem kan ook optreden als er meerdere back-ups per dag worden geactiveerd. Momenteel raden we slechts één back-up per dag aan, omdat de directe herstelpunten gedurende 1-5 dagen per de geconfigureerde momentopnameretentie worden bewaard en slechts 18 instant RPs op een bepaald moment aan een VM kunnen worden gekoppeld. <br>
- Het aantal herstelpunten voor herstelpuntverzamelingen en resourcegroepen voor een virtuele machine kan niet hoger zijn dan 18. Als u een nieuw herstelpunt wilt maken, verwijdert u bestaande herstelpunten.

Aanbevolen actie:<br>
Als u dit probleem wilt oplossen, verwijdert u het slot op de resourcegroep van de virtuele machine en probeert u de bewerking opnieuw om het opschonen te activeren.
> [!NOTE]
> Back-upservice maakt een afzonderlijke resourcegroep dan de resourcegroep van de VM om het herstellen van puntenverzameling op te slaan. Klanten wordt geadviseerd de resourcegroep die is gemaakt voor gebruik door de back-upservice niet te vergrendelen. De naamgevingsindeling van de resourcegroep die`<Geo>``<number>` is gemaakt door back-upservice is: AzureBackupRG_ _ Bijv: AzureBackupRG_northeurope_1

**Stap 1: [Vergrendeling verwijderen uit de brongroep herstelpunt](#remove_lock_from_the_recovery_point_resource_group)** <br>
**Stap 2: [Herstelpuntverzameling opruimen](#clean_up_restore_point_collection)**<br>

## <a name="usererrorkeyvaultpermissionsnotconfigured---backup-doesnt-have-sufficient-permissions-to-the-key-vault-for-backup-of-encrypted-vms"></a>UserErrorKeyvaultPermissionsNotConfigured - Back-up heeft onvoldoende machtigingen voor de sleutelkluis voor back-ups van versleutelde VM's

**Foutcode**: UserErrorKeyvaultPermissionsNotConfigured <br>
**Foutbericht:** Back-up heeft onvoldoende machtigingen voor de sleutelkluis voor back-ups van versleutelde VM's. <br>

Als een back-upbewerking kan worden uitgevoerd op versleutelde VM's, moet deze machtigingen hebben om toegang te krijgen tot de sleutelkluis. Machtigingen kunnen worden ingesteld via de [Azure-portal](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption) of via [PowerShell.](https://docs.microsoft.com/azure/backup/backup-azure-vms-automation#enable-protection)

## <a name="extensionsnapshotfailednonetwork---snapshot-operation-failed-due-to-no-network-connectivity-on-the-virtual-machine"></a><a name="ExtensionSnapshotFailedNoNetwork-snapshot-operation-failed-due-to-no-network-connectivity-on-the-virtual-machine"></a>ExtensionSnapshotFailedNoNetwork - Momentopname mislukt omdat er geen netwerkverbinding beschikbaar is op de virtuele machine

**Foutcode:** ExtensionSnapshotFailedNoNetwork<br>
**Foutbericht:** momentopnamebewerking is mislukt omdat er geen netwerkverbinding op de virtuele machine is<br>

Nadat u een VM voor de Azure Backup-service hebt geregistreerd en gepland, start Back-up de taak door te communiceren met de VM-back-upextensie om een point-in-time momentopname te maken. Een van de volgende voorwaarden kan voorkomen dat de momentopname wordt geactiveerd. Als de momentopname niet wordt geactiveerd, kan er een back-upfout optreden. Voer de volgende probleemoplossingsstap uit en probeer uw bewerking opnieuw:

**[De momentopnamestatus kan niet worden opgehaald of een momentopname kan niet worden gemaakt](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  

## <a name="extensionoperationfailedformanageddisks---vmsnapshot-extension-operation-failed"></a><a name="ExtensionOperationFailed-vmsnapshot-extension-operation-failed"></a>ExtensionOperationFailedForManagedDisks - VMSnapshot-extensiebewerking is mislukt

**Foutcode**: ExtensionOperationFailedForManagedDisks <br>
**Foutbericht:** VMSnapshot-extensiebewerking is mislukt<br>

Nadat u een VM voor de Azure Backup-service hebt geregistreerd en gepland, start Back-up de taak door te communiceren met de VM-back-upextensie om een point-in-time momentopname te maken. Een van de volgende voorwaarden kan voorkomen dat de momentopname wordt geactiveerd. Als de momentopname niet wordt geactiveerd, kan er een back-upfout optreden. Voer de volgende stappen voor het oplossen van problemen in de vermelde volgorde uit en probeer de bewerking opnieuw:  
**Oorzaak 1: [de momentopnamestatus kan niet worden opgehaald of een momentopname kan niet worden gemaakt](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**Oorzaak 2: [De agent is geïnstalleerd in de VM, maar reageert niet (voor Windows VM's)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**Oorzaak 3: [De agent die in de VM is geïnstalleerd, is verouderd (voor Linux VM's)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**

## <a name="backupoperationfailed--backupoperationfailedv2---backup-fails-with-an-internal-error"></a>BackUpOperationFailed / BackUpOperationFailedV2 - Back-up mislukt met een interne fout

**Foutcode:** BackUpOperationFailed / BackUpOperationFailedV2 <br>
**Foutbericht:** Back-up is mislukt met een interne fout - Probeer de bewerking binnen enkele minuten opnieuw <br>

Nadat u een VM voor de Azure Backup-service hebt geregistreerd en gepland, initieert Backup de taak door te communiceren met de VM-back-upextensie om een point-in-time momentopname te maken. Een van de volgende voorwaarden kan voorkomen dat de momentopname wordt geactiveerd. Als de momentopname niet wordt geactiveerd, kan er een back-upfout optreden. Voer de volgende stappen voor het oplossen van problemen in de vermelde volgorde uit en probeer de bewerking opnieuw:  
**Oorzaak 1: [de agent die in de VM is geïnstalleerd, maar deze reageert niet (voor Windows VM's)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**Oorzaak 2: [De agent die in de VM is geïnstalleerd, is verouderd (voor Linux VM's)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**Oorzaak 3: [De momentopnamestatus kan niet worden opgehaald of een momentopname kan niet worden gemaakt](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**Oorzaak 4: [Back-upservice heeft geen toestemming om de oude herstelpunten te verwijderen vanwege een brongroepvergrendeling](#remove_lock_from_the_recovery_point_resource_group)**<br>

## <a name="usererrorunsupporteddisksize---the-configured-disk-sizes-is-currently-not-supported-by-azure-backup"></a>UserErrorUnsupportedDiskSize - De geconfigureerde schijfgrootte(s) wordt momenteel niet ondersteund door Azure Backup

**Foutcode**: UserErrorUnsupportedDiskSize <br>
**Foutbericht:** de geconfigureerde schijfgrootte(s) wordt momenteel niet ondersteund door Azure Backup. <br>

Uw back-upbewerking kan mislukken bij het maken van een back-up van een vm met een schijfgrootte van meer dan 32 TB. Ook wordt back-up van versleutelde schijven groter dan 4 TB momenteel niet ondersteund. Zorg ervoor dat de schijfgrootte(s) kleiner is dan of gelijk is aan de ondersteunde limiet door de schijf(en) te splitsen.

## <a name="usererrorbackupoperationinprogress---unable-to-initiate-backup-as-another-backup-operation-is-currently-in-progress"></a>UserErrorBackupOperationInProgress - Kan geen back-up starten omdat er momenteel een andere back-upbewerking wordt uitgevoerd

**Foutcode**: UserErrorBackupOperationInProgress <br>
**Foutbericht:** Kan geen back-up starten omdat er momenteel een andere back-upbewerking wordt uitgevoerd<br>

Uw recente back-uptaak is mislukt omdat er een bestaande back-uptaak aan de gang is. U geen nieuwe back-uptaak starten totdat de huidige taak is voltooid. Zorg ervoor dat de back-upbewerking die momenteel loopt, is voltooid voordat u een andere back-upbewerking activeert of plant. Ga als volgt te werk om de status van back-uptaken te controleren:

1. Meld u aan bij de Azure-portal en klik op **Alle services**. Typ Recovery Services en klik op **Recovery Services-kluizen**. De lijst met Recovery Services-kluizen wordt weergegeven.
2. Selecteer in de lijst met kluizen van herstelservices een kluis waarin de back-up is geconfigureerd.
3. Klik in het menu van het vault-dashboard op **Back-uptaken** waarop alle back-uptaken worden weergegeven.
   - Als er een back-uptaak aan de gang is, wacht u tot deze is voltooid of annuleert u de back-uptaak.
     - Als u de back-uptaak wilt annuleren, klikt u met de rechtermuisknop op de back-uptaak en klikt u op **Annuleren** of [powershell](https://docs.microsoft.com/powershell/module/az.recoveryservices/stop-azrecoveryservicesbackupjob?view=azps-1.4.0)gebruiken.
   - Als u de back-up opnieuw hebt geconfigureerd in een andere kluis, moet u ervoor zorgen dat er geen back-uptaken worden uitgevoerd in de oude kluis. Als deze bestaat, annuleert u de back-uptaak.
     - Als u de back-uptaak wilt annuleren, klikt u met de rechtermuisknop op de back-uptaak en klikt u op **Annuleren**, of gebruikt u [PowerShell](https://docs.microsoft.com/powershell/module/az.recoveryservices/stop-azrecoveryservicesbackupjob?view=azps-1.4.0)
4. Probeer een back-upbewerking opnieuw.

Als de geplande back-upbewerking langer duurt, in strijd met de volgende back-upconfiguratie, controleert u de [aanbevolen procedures](backup-azure-vms-introduction.md#best-practices), [back-upprestaties](backup-azure-vms-introduction.md#backup-performance)en [hersteloverweging](backup-azure-vms-introduction.md#backup-and-restore-considerations).

## <a name="usererrorcrpreportedusererror---backup-failed-due-to-an-error-for-details-see-job-error-message-details"></a>UserErrorCrpReportedUserError - Back-up is mislukt als gevolg van een fout. Zie Berichtgegevens taakfout voor meer informatie

**Foutcode**: UserErrorCrpReportedUserError <br>
**Foutbericht:** Back-up is mislukt als gevolg van een fout. Zie Details van het bericht taakfout voor meer informatie.

Deze fout wordt gemeld door de IaaS VM. Als u de hoofdoorzaak van het probleem wilt identificeren, gaat u naar de kluisinstellingen van Recovery Services. Selecteer **onder** de sectie Controle de optie **Back-uptaken** filteren en de status weergeven. Klik op **Fouten** om de onderliggende foutberichtgegevens te bekijken. Verdere acties uitvoeren volgens de aanbevelingen op de pagina foutdetails.

## <a name="usererrorbcmdatasourcenotpresent---backup-failed-this-virtual-machine-is-not-actively-protected-by-azure-backup"></a>UserErrorBcmDatasourceNotPresent - Back-up mislukt: deze virtuele machine wordt niet (actief) beschermd door Azure Backup

**Foutcode**: UserErrorBcmDatasourceNotPresent <br>
**Foutbericht:** back-up is mislukt: deze virtuele machine wordt niet (actief) beschermd door Azure Backup.

Controleer of de gegeven virtuele machine actief is (niet in pauzestatus) die wordt beschermd door Azure Backup. Om dit probleem op te lossen, moet u ervoor zorgen dat de virtuele machine actief is en vervolgens de bewerking opnieuw proberen.

## <a name="causes-and-solutions"></a>Oorzaken en oplossingen

### <a name="the-agent-is-installed-in-the-vm-but-its-unresponsive-for-windows-vms"></a><a name="the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>De agent is geïnstalleerd in de VM, maar reageert niet (voor Windows VM's)

#### <a name="solution"></a>Oplossing

De VM-agent is mogelijk beschadigd of de service is mogelijk gestopt. Het opnieuw installeren van de VM-agent helpt om de nieuwste versie te krijgen. Het helpt ook de communicatie met de service opnieuw op te starten.

1. Bepaal of de Windows Azure Guest Agent-service wordt uitgevoerd in de VM-services (services.msc). Probeer de Windows Azure Guest Agent-service opnieuw te starten en start de back-up.
2. Als de Windows Azure Guest Agent-service niet zichtbaar is in services, gaat u in het Configuratiescherm naar **Programma's en onderdelen** om te bepalen of de Windows Azure Guest Agent-service is geïnstalleerd.
3. Als de Windows Azure Guest Agent wordt weergegeven in **Programma's en onderdelen,** verwijdert u de Windows Azure Guest Agent.
4. Download en installeer de [nieuwste versie van de agent MSI](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). U moet beheerdersrechten hebben om de installatie te voltooien.
5. Controleer of de Windows Azure Guest Agent-services worden weergegeven in services.
6. Voer een on-demand back-up uit:
   - Selecteer **nu back-up**in de portal .

Controleer ook of [Microsoft .NET 4.5 is geïnstalleerd](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) in de VM. .NET 4.5 is vereist dat de VM-agent met de service communiceert.

### <a name="the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>De agent die in de VM is geïnstalleerd, is verouderd (voor Linux VM's)

#### <a name="solution"></a>Oplossing

De meeste agent-gerelateerde of extensie-gerelateerde fouten voor Linux VM's worden veroorzaakt door problemen die van invloed zijn op een verouderde VM-agent. Volg de volgende algemene richtlijnen om dit probleem op te lossen:

1. Volg de instructies voor [het bijwerken van de Linux VM-agent.](../virtual-machines/linux/update-agent.md)

   > [!NOTE]
   > We raden u *ten zeerste aan* de agent alleen te updaten via een distributieopslagplaats. We raden niet aan om de agentcode rechtstreeks van GitHub te downloaden en bij te werken. Als de nieuwste agent voor uw distributie niet beschikbaar is, neemt u contact op met de distributieondersteuning voor instructies over het installeren ervan. Als u wilt controleren op de meest recente agent, gaat u naar de Windows [Azure Linux-agentpagina](https://github.com/Azure/WALinuxAgent/releases) in de GitHub-opslagplaats.

2. Controleer of de Azure-agent op de VM wordt uitgevoerd door de volgende opdracht uit te voeren:`ps -e`

   Als het proces niet wordt uitgevoerd, start u het opnieuw op met de volgende opdrachten:

   - Voor Ubuntu:`service walinuxagent start`
   - Voor andere distributies:`service waagent start`

3. [De automatische herstartagent configureren](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash).
4. Voer een nieuwe testback-up uit. Als de fout zich voortduurt, verzamelt u de volgende logboeken van de vm:

   - /var/lib/waagent/*.xml
   - /var/log/waagent.log
   - /var/log/azure/*

Als u verbose logging voor waagent nodig hebt, voert u de volgende stappen uit:

1. Zoek in het bestand /etc/waagent.conf de volgende regel: **Verboselogging inschakelen (y|n)**
2. Wijzig de waarde **Logs.Verbose** van *n* naar *y*.
3. Sla de wijziging op en start waagent opnieuw door de eerder in deze sectie beschreven stappen in te vullen.

### <a name="vm-agent-configuration-options-are-not-set-for-linux-vms"></a>VM-Agent-configuratieopties zijn niet ingesteld (voor Linux VM's)

Een configuratiebestand (/etc/waagent.conf) regelt de acties van waagent. Configuration File Options **Extensions.Enable** en **Provisioning.Agent** moeten worden ingesteld op **y** voor Back-up om te werken.
Zie Voor de volledige lijst met configuratiebestandsopties van VM-agent<https://github.com/Azure/WALinuxAgent#configuration-file-options>

### <a name="the-snapshot-status-cant-be-retrieved-or-a-snapshot-cant-be-taken"></a><a name="the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken"></a>De momentopnamestatus kan niet worden opgehaald of een momentopname kan niet worden gemaakt

De VM-back-up is gebaseerd op het uitgeven van een momentopnameopdracht naar het onderliggende opslagaccount. Back-up kan mislukken omdat deze geen toegang heeft tot het opslagaccount of omdat de uitvoering van de momentopnametaak is vertraagd.

#### <a name="solution"></a>Oplossing

De volgende voorwaarden kunnen ertoe leiden dat de momentopnametaak mislukt:

| Oorzaak | Oplossing |
| --- | --- |
| De VM-status wordt onjuist gerapporteerd omdat de VM is afgesloten in Extern bureaublad-protocol (RDP). | Als u de VM in RDP afsluit, controleert u de portal om te bepalen of de VM-status juist is. Als dit niet klopt, sluit u de VM in de portal af met de optie **Afsluiten** op het VM-dashboard. |
| De VM kan het host- of fabricadres niet van DHCP krijgen. | DHCP moet in de gast zijn ingeschakeld om de IaaS VM-back-up te laten werken. Als de vm het host- of fabricadres van DHCP-antwoord 245 niet kan ophalen, kan deze geen extensies downloaden of uitvoeren. Als u een statisch privé-IP-adres nodig hebt, moet u dit configureren via de **Azure-portal** of **PowerShell** en ervoor zorgen dat de DHCP-optie in de VM is ingeschakeld. [Meer informatie](../virtual-network/virtual-networks-static-private-ip-arm-ps.md#change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface) over het instellen van een statisch IP-adres met PowerShell.

### <a name="remove-lock-from-the-recovery-point-resource-group"></a><a name="remove_lock_from_the_recovery_point_resource_group"></a>Vergrendeling verwijderen uit de brongroep herstelpunt

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
2. Ga naar **de optie Alle bronnen,** selecteer de brongroep bronvan het herstelpuntverzameling in de volgende indeling AzureBackupRG_`<Geo>`_`<number>`.
3. Selecteer **In** de sectie Instellingen de optie **Sloten** om de vergrendelingen weer te geven.
4. Als u het slot wilt verwijderen, selecteert u de ellips en klikt u op **Verwijderen**.

    ![Vergrendeling verwijderen](./media/backup-azure-arm-vms-prepare/delete-lock.png)

### <a name="clean-up-restore-point-collection"></a><a name="clean_up_restore_point_collection"></a>Herstelpuntverzameling opruimen

Na het verwijderen van het slot moeten de herstelpunten worden opgeruimd.

Als u de resourcegroep van de vm of de VM zelf verwijdert, blijven de momentopnamen van beheerde schijven direct hersteld en verlopen ze volgens de bewaarset. Als u de momentopnamen voor het direct herstellen (als u ze niet meer nodig hebt) wilt verwijderen die zijn opgeslagen in de herstelpuntverzameling, ruimt u de verzameling herstelpunten op volgens de onderstaande stappen.

Volg een van de methoden om de herstelpunten op te ruimen:<br>

- [Herstelpuntverzameling opschonen door on-demand back-up uit te voeren](#clean-up-restore-point-collection-by-running-on-demand-backup)<br>
- [Herstelpuntverzameling ophalen vanuit Azure-portal](#clean-up-restore-point-collection-from-azure-portal)<br>

#### <a name="clean-up-restore-point-collection-by-running-on-demand-backup"></a><a name="clean-up-restore-point-collection-by-running-on-demand-backup"></a>Herstelpuntverzameling opschonen door on-demand back-up uit te voeren

Nadat u het slot hebt verwijderd, activeert u een on-demand back-up. Deze actie zorgt ervoor dat de herstelpunten automatisch worden opgeschoond. Verwacht dat deze on-demand bewerking de eerste keer mislukt; het zorgt echter voor automatische opruiming in plaats van handmatig verwijderen van herstelpunten. Na het opruimen moet uw volgende geplande back-up slagen.

> [!NOTE]
> Automatisch opruimen gebeurt na enkele uren van het activeren van de on-demand back-up. Als uw geplande back-up nog steeds mislukt, probeert u de verzameling herstelpunten handmatig te verwijderen met behulp van de [hier](#clean-up-restore-point-collection-from-azure-portal)vermelde stappen.

#### <a name="clean-up-restore-point-collection-from-azure-portal-br"></a><a name="clean-up-restore-point-collection-from-azure-portal"></a>Herstelpuntverzameling ophalen vanuit Azure-portal <br>

Als u de verzameling herstelpunten handmatig wilt wissen, die niet is gewist vanwege het slot op de resourcegroep, probeert u de volgende stappen:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
2. Klik **in** het menu Hub op **Alle bronnen**,`<Geo>`selecteer`<number>` de groep Resource met de volgende indeling AzureBackupRG_ _ waar uw vm zich bevindt.

    ![Vergrendeling verwijderen](./media/backup-azure-arm-vms-prepare/resource-group.png)

3. Klik op Resourcegroep, het deelvenster **Overzicht** wordt weergegeven.
4. Selecteer de optie **Verborgen typen weergeven** om alle verborgen bronnen weer te geven. Selecteer de herstelpuntverzamelingen met`<VMName>``<number>`de volgende indeling AzureBackupRG_ _ .

    ![Vergrendeling verwijderen](./media/backup-azure-arm-vms-prepare/restore-point-collection.png)

5. Klik **op Verwijderen** om de verzameling herstelpunten op te schonen.
6. Probeer de back-upbewerking opnieuw.

> [!NOTE]
 >Als de resource (RP-verzameling) een groot aantal herstelpunten heeft, kan het verwijderen van deze resources uit de portal een time-out hebben en mislukken. Dit is een bekend CRP-probleem, waarbij alle herstelpunten niet worden verwijderd in de afgesproken tijd en de werking een time-out heeft; nochtans slaagt de schrapbewerking gewoonlijk na 2 of 3 opnieuwpogingen.
