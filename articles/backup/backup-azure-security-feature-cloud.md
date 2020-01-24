---
title: Beveiligings functies voor het beveiligen van Cloud werkbelastingen
description: Meer informatie over het gebruik van beveiligings functies in Azure Backup om back-ups veiliger te maken.
ms.topic: conceptual
ms.date: 09/13/2019
ms.openlocfilehash: 20cf322dec0827c00b15a62bf4f7695fc4ed0992
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76705493"
---
# <a name="security-features-to-help-protect-cloud-workloads-that-use-azure-backup"></a>Beveiligings functies voor het beveiligen van Cloud werkbelastingen die gebruikmaken van Azure Backup

Zorgen over beveiligingsproblemen, zoals malware, ransomware en inbraak nemen toe. Deze beveiligingsproblemen kunnen kostbaar zijn, zowel met betrekking tot geld als gegevens. Azure Backup biedt nu beveiligings functies die u helpen bij het beveiligen van back-upgegevens, zelfs na het verwijderen.

Een dergelijke functie is zacht verwijderen. Met zacht verwijderen, zelfs als een schadelijke actor de back-up van een virtuele machine verwijdert (of als er per ongeluk back-upgegevens worden verwijderd), worden de back-upgegevens 14 extra dagen bewaard, zodat het back-upitem zonder gegevens verlies kan worden hersteld. De extra 14 dagen retentie van back-upgegevens in de status ' voorlopig verwijderen ' maken geen kosten voor de klant. Azure versleutelt ook alle back-upgegevens op rest met behulp van [Storage service Encryption](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) om uw gegevens verder te beveiligen.

Tijdelijke verwijderings beveiliging voor virtuele machines van Azure is algemeen beschikbaar.

>[!NOTE]
>Voorlopig verwijderen voor SQL Server in azure VM en voorlopig verwijderen voor SAP HANA in azure VM-workloads is nu beschikbaar als preview-versie.<br>
>Als u zich wilt aanmelden voor de preview, schrijft u voor AskAzureBackupTeam@microsoft.com

## <a name="soft-delete"></a>Voorlopig verwijderen

### <a name="soft-delete-for-vms"></a>Voorlopig verwijderen voor Vm's

Zacht verwijderen voor Vm's beveiligt de back-ups van uw Vm's tegen onbedoeld verwijderen. Zelfs nadat de back-ups zijn verwijderd, blijven ze gedurende 14 extra dagen bewaard in de status van zacht verwijderen.

> [!NOTE]
> Met zacht verwijderen worden verwijderde back-upgegevens alleen beveiligd. Als een virtuele machine wordt verwijderd zonder een back-up, worden de gegevens niet bewaard met de functie voor voorlopig verwijderen. Alle resources moeten worden beveiligd met Azure Backup om volledige tolerantie te garanderen.
>

### <a name="supported-regions"></a>Ondersteunde regio’s

Voorlopig verwijderen wordt momenteel ondersteund in de West-Centraal VS, Azië-oost, Canada-centraal, Canada-oost, Frankrijk-centraal, Frankrijk-zuid, Korea-centraal, Korea-zuid, UK-zuid, UK-west, Australië-oost, Australië-Zuid-Oost, Europa-noord, VS-West, West VS2, VS-midden, Zuid Azië-oost, Noord-Centraal VS, Zuid-Centraal VS, Japan-Oost, Japan-West, India-Zuid, India-midden, India-West, VS-Oost 2, Zwitserland-noord, Zwitserland-west en alle nationale regio's.

### <a name="soft-delete-for-vms-using-azure-portal"></a>Voorlopig verwijderen voor Vm's met behulp van Azure Portal

1. Voor het verwijderen van de back-upgegevens van een virtuele machine moet de back-up worden gestopt. Ga in het Azure Portal naar uw Recovery Services-kluis, klik met de rechter muisknop op het back-upitem en kies **back-up stoppen**.

   ![Scherm opname van Azure Portal back-upitems](./media/backup-azure-security-feature-cloud/backup-stopped.png)

2. In het volgende venster krijgt u de mogelijkheid om de back-upgegevens te verwijderen of te bewaren. Als u **back-upgegevens verwijderen** kiest en vervolgens **back-up stoppen**, wordt de back-up van de virtuele machine niet definitief verwijderd. In plaats daarvan worden de back-upgegevens 14 dagen in de modus voorlopig verwijderd bewaard. Als er **back-upgegevens verwijderen** wordt gekozen, wordt een waarschuwing voor verwijderen van e-mail verzonden naar de geconfigureerde e-mail-id waarmee de gebruiker 14 dagen op de hoogte blijft van de uitgebreide Bewaar periode voor back-upgegevens. Er wordt ook een e-mail waarschuwing verzonden op de twaalfde dag, waarin wordt gemeld dat er nog twee resterende dagen zijn om de verwijderde gegevens te Resurrect. De verwijdering wordt uitgesteld tot de vijftiende dag, wanneer er permanent wordt verwijderd en er wordt een definitieve e-mail waarschuwing verzonden over het permanent verwijderen van de gegevens.

   ![Scherm opname van Azure Portal, back-upvenster stoppen](./media/backup-azure-security-feature-cloud/delete-backup-data.png)

3. Gedurende die 14 dagen, in de Recovery Services kluis, wordt de voorlopig verwijderde virtuele machine weer gegeven met een rood pictogram ' zacht verwijderen ' ernaast.

   ![Scherm opname van Azure Portal, VM in de status zacht verwijderen](./media/backup-azure-security-feature-cloud/vm-soft-delete.png)

   > [!NOTE]
   > Als er tijdelijke, verwijderde back-upitems aanwezig zijn in de kluis, kan de kluis op dat moment niet worden verwijderd. Probeer het verwijderen van de kluis nadat de back-upitems definitief zijn verwijderd en er is geen item met de status zacht verwijderd links in de kluis.

4. Als u de voorlopig verwijderde virtuele machine wilt herstellen, moet deze eerst worden verwijderd. Als u de verwijdering ongedaan wilt maken, kiest u de voorlopig verwijderde virtuele machine en selecteert u vervolgens de optie **verwijderen ongedaan**maken.

   ![Scherm opname van Azure Portal, verwijderen van virtuele machine ongedaan maken](./media/backup-azure-security-feature-cloud/choose-undelete.png)

   Er wordt een venster weer gegeven waarin wordt vermeld dat als ongedaan maken is gekozen, alle herstel punten voor de virtuele machine worden verwijderd en beschikbaar zijn voor het uitvoeren van een herstel bewerking. De virtuele machine wordt bewaard in de status ' beveiliging met behoud van gegevens stoppen ' met back-ups onderbroken en back-upgegevens blijven behouden zonder dat er back-upbeleid van kracht is.

   ![Scherm opname van Azure Portal, bevestig de verwijdering van de virtuele machine](./media/backup-azure-security-feature-cloud/undelete-vm.png)

   Op dit moment kunt u ook de virtuele machine herstellen door **VM herstellen** uit het gekozen herstel punt te selecteren.  

   ![Scherm opname van Azure Portal, VM-optie herstellen](./media/backup-azure-security-feature-cloud/restore-vm.png)

   > [!NOTE]
   > De garbage collector voert alleen verlopen herstel punten uit en reinigt nadat de gebruiker de **back-** upbewerking voor hervatten heeft uitgevoerd.

5. Nadat het verwijderen is voltooid, wordt de status weer gegeven als back-up stoppen met gegevens behouden en vervolgens kunt u **back-up hervatten**kiezen. Met de **back-** upbewerking hervatten wordt het back-upitem teruggezet in de actieve status, gekoppeld aan een back-upbeleid dat is geselecteerd door de gebruiker die de back-up-en bewaar schema's definieert.

   ![Scherm opname van Azure Portal, back-upoptie hervatten](./media/backup-azure-security-feature-cloud/resume-backup.png)

In dit stroom diagram worden de verschillende stappen en statussen van een back-upitem weer gegeven wanneer zacht verwijderen is ingeschakeld:

![Levens cyclus van een zacht verwijderd back-upitem](./media/backup-azure-security-feature-cloud/lifecycle.png)

Zie het gedeelte Veelgestelde [vragen](backup-azure-security-feature-cloud.md#frequently-asked-questions) hieronder voor meer informatie.

### <a name="soft-delete-for-vms-using-azure-powershell"></a>Voorlopig verwijderen voor Vm's met behulp van Azure Power shell

> [!IMPORTANT]
> De AZ. Recovery Services-versie die is vereist voor het gebruik van voorlopig verwijderen met Azure PS, is mini maal 2.2.0. Gebruik ```Install-Module -Name Az.RecoveryServices -Force``` om de nieuwste versie te verkrijgen.

Zoals hierboven wordt beschreven voor Azure Portal, is de volg orde van de stappen hetzelfde wanneer u ook Azure Power shell gebruikt.

#### <a name="delete-the-backup-item-using-azure-powershell"></a>Het back-upitem verwijderen met Azure Power shell

Verwijder het back-upitem met de cmdlet [Disable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Disable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) PS.

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $myBkpItem -RemoveRecoveryPoints -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           DeleteBackupData     Completed            12/5/2019 12:44:15 PM     12/5/2019 12:44:50 PM     0488c3c2-accc-4a91-a1e0-fba09a67d2fb
```

Het ' DeleteState ' van het back-upitem wordt gewijzigd van ' NotDeleted ' in ' ToBeDeleted '. De back-upgegevens worden 14 dagen bewaard. Als u de Verwijder bewerking wilt terugdraaien, moet u het ongedaan maken verwijderen.

#### <a name="undoing-the-deletion-operation-using-azure-powershell"></a>De Verwijder bewerking ongedaan maken met Azure Power shell

U moet eerst het relevante back-upitem ophalen dat zich in de status voorlopig verwijderen bevindt (dat wil zeggen, dat wil zeggen dat het wordt verwijderd).

```powershell

Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID | Where-Object {$_.DeleteState -eq "ToBeDeleted"}

Name                                     ContainerType        ContainerUniqueName                      WorkloadType         ProtectionStatus     HealthStatus         DeleteState
----                                     -------------        -------------------                      ------------         ----------------     ------------         -----------
VM;iaasvmcontainerv2;selfhostrg;AppVM1    AzureVM             iaasvmcontainerv2;selfhostrg;AppVM1       AzureVM              Healthy              Passed               ToBeDeleted

$myBkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID -Name AppVM1
```

Vervolgens voert u de bewerking voor ongedaan maken uit met de PS [-cmdlet Undo-AzRecoveryServicesBackupItemDeletion](https://docs.microsoft.com/powershell/module/az.recoveryservices/undo-azrecoveryservicesbackupitemdeletion?view=azps-3.1.0) .

```powershell
Undo-AzRecoveryServicesBackupItemDeletion -Item $myBKpItem -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           Undelete             Completed            12/5/2019 12:47:28 PM     12/5/2019 12:47:40 PM     65311982-3755-46b5-8e53-c82ea4f0d2a2
```

De ' DeleteState ' van het back-upitem wordt teruggezet op ' NotDeleted '. De beveiliging is echter nog steeds gestopt. U moet [de back-up hervatten](https://docs.microsoft.com/azure/backup/backup-azure-vms-automation#change-policy-for-backup-items) om de beveiliging opnieuw in te scha kelen.

### <a name="soft-delete-for-vms-using-rest-api"></a>Voorlopig verwijderen voor Vm's met behulp van REST API

- Verwijder de back-ups met REST API zoals [hier](backup-azure-arm-userestapi-backupazurevms.md#stop-protection-and-delete-data)wordt vermeld.
- Als de gebruiker deze Verwijder bewerkingen ongedaan wil maken, raadpleegt u de stappen die [hier](backup-azure-arm-userestapi-backupazurevms.md#undo-the-stop-protection-and-delete-data)worden beschreven.

## <a name="disabling-soft-delete"></a>Tijdelijke verwijdering uitschakelen

Voorlopig verwijderen is standaard ingeschakeld op nieuwe kluizen om back-upgegevens te beschermen tegen onbedoelde of schadelijke verwijderingen.  Het uitschakelen van deze functie wordt niet aanbevolen. De enige omstandigheid waarbij het uitschakelen van de functie voor het verwijderen van uw beveiligde items naar een nieuwe kluis moet worden aangeraden, is dat de 14 dagen die vereist zijn voor het verwijderen en opnieuw beveiligen van de gegevens (zoals in een test omgeving), niet kunnen worden gewacht. Alleen een back-upbeheerder kan deze functie uitschakelen. Als u deze functie uitschakelt, worden alle verwijderde beveiligde items onmiddellijk verwijderd, zonder dat u de mogelijkheid hebt om te herstellen. Back-upgegevens in de modus zacht verwijderd voordat deze functie wordt uitgeschakeld, blijft de status voorlopig verwijderd. Als u deze onmiddellijk permanent wilt verwijderen, moet u het verwijderen ongedaan maken en opnieuw verwijderen om het permanent te laten worden verwijderd.

### <a name="disabling-soft-delete-using-azure-portal"></a>Zacht verwijderen uitschakelen met Azure Portal

Voer de volgende stappen uit om de tijdelijke verwijdering uit te scha kelen:

1. Ga in het Azure Portal naar uw kluis en ga naar **instellingen** -> **Eigenschappen**.
2. Selecteer in het deel venster Eigenschappen de optie **beveiligings instellingen** -> **Update**.  
3. Selecteer in het deel venster beveiligings instellingen onder **voorlopig verwijderen**de optie **uitschakelen**.

![Tijdelijke verwijdering uitschakelen](./media/backup-azure-security-feature-cloud/disable-soft-delete.png)

### <a name="disabling-soft-delete-using-azure-powershell"></a>Zacht verwijderen uitschakelen met Azure Power shell

> [!IMPORTANT]
> De AZ. Recovery Services-versie die is vereist voor het gebruik van voorlopig verwijderen met Azure PS, is mini maal 2.2.0. Gebruik ```Install-Module -Name Az.RecoveryServices -Force``` om de nieuwste versie te verkrijgen.

Als u wilt uitschakelen, gebruikt u de [set-AzRecoveryServicesVaultBackupProperty](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty?view=azps-3.1.0) PS-cmdlet.

```powershell
Set-AzRecoveryServicesVaultProperty -VaultId $myVaultID -SoftDeleteFeatureState Disable


StorageModelType       :
StorageType            :
StorageTypeState       :
EnhancedSecurityState  : Enabled
SoftDeleteFeatureState : Disabled
```

### <a name="disabling-soft-delete-using-rest-api"></a>Zacht verwijderen uitschakelen met REST API

Als u de functie voor voorlopig verwijderen wilt uitschakelen met behulp van REST API, raadpleegt u de stappen die [hier](use-restapi-update-vault-properties.md#update-soft-delete-state-using-rest-api)worden beschreven.

## <a name="permanently-deleting-soft-deleted-backup-items"></a>Voorlopig verwijderde back-upitems permanent verwijderen

Back-upgegevens in de modus zacht verwijderd voordat deze functie wordt uitgeschakeld, blijft de status voorlopig verwijderd. Als u deze onmiddellijk permanent wilt verwijderen, moet u deze verwijderen en weer verwijderen om het permanent te verwijderen.

### <a name="using-azure-portal"></a>Azure Portal gebruiken

Volg deze stappen:

1. Volg de stappen om [zacht verwijderen uit te scha kelen](#disabling-soft-delete).
2. Ga in het Azure Portal naar uw kluis, ga naar **Back-upitems**en kies de voorlopig verwijderde virtuele machine.

![Zacht verwijderde virtuele machine kiezen](./media/backup-azure-security-feature-cloud/vm-soft-delete.png)

3. Selecteer de optie **verwijderen ongedaan**maken.

![Kies verwijderen ongedaan maken](./media/backup-azure-security-feature-cloud/choose-undelete.png)


4. Er wordt een venster weer gegeven. Selecteer **verwijderen ongedaan**maken.

![Selecteer verwijderen ongedaan maken](./media/backup-azure-security-feature-cloud/undelete-vm.png)

5. Kies **back-upgegevens verwijderen** om de back-upgegevens permanent te verwijderen.

![Back-upgegevens verwijderen kiezen](https://docs.microsoft.com/azure/backup/media/backup-azure-manage-vms/delete-backup-buttom.png)

6. Typ de naam van het back-upitem om te bevestigen dat u de herstel punten wilt verwijderen.

![Typ de naam van het back-upitem](https://docs.microsoft.com/azure/backup/media/backup-azure-manage-vms/delete-backup-data1.png)

7. Selecteer **verwijderen**als u de back-upgegevens voor het item wilt verwijderen. Een meldings bericht laat u weten dat de back-upgegevens zijn verwijderd.

### <a name="using-azure-powershell"></a>Azure Power shell gebruiken

Als er items zijn verwijderd voordat de Soft-verwijdering werd uitgeschakeld, wordt de status voorlopig verwijderd. Als u deze onmiddellijk wilt verwijderen, moet de verwijderings bewerking worden omgekeerd en opnieuw worden uitgevoerd.

Identificeer de items met de status zacht verwijderd.

```powershell

Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID | Where-Object {$_.DeleteState -eq "ToBeDeleted"}

Name                                     ContainerType        ContainerUniqueName                      WorkloadType         ProtectionStatus     HealthStatus         DeleteState
----                                     -------------        -------------------                      ------------         ----------------     ------------         -----------
VM;iaasvmcontainerv2;selfhostrg;AppVM1    AzureVM             iaasvmcontainerv2;selfhostrg;AppVM1       AzureVM              Healthy              Passed               ToBeDeleted

$myBkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID -Name AppVM1
```

Vervolgens keert u de verwijderings bewerking terug die is uitgevoerd tijdens het inschakelen van de functie voor het verwijderen van de software.

```powershell
Undo-AzRecoveryServicesBackupItemDeletion -Item $myBKpItem -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           Undelete             Completed            12/5/2019 12:47:28 PM     12/5/2019 12:47:40 PM     65311982-3755-46b5-8e53-c82ea4f0d2a2
```

Omdat de tijdelijke verwijdering nu is uitgeschakeld, wordt de back-upgegevens onmiddellijk verwijderd wanneer de bewerking wordt verwijderd.

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $myBkpItem -RemoveRecoveryPoints -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           DeleteBackupData     Completed            12/5/2019 12:44:15 PM     12/5/2019 12:44:50 PM     0488c3c2-accc-4a91-a1e0-fba09a67d2fb
```

### <a name="using-rest-api"></a>REST API gebruiken

Als er items zijn verwijderd voordat de Soft-verwijdering werd uitgeschakeld, wordt de status voorlopig verwijderd. Als u deze onmiddellijk wilt verwijderen, moet de verwijderings bewerking worden omgekeerd en opnieuw worden uitgevoerd.

1. Verwijder eerst de stappen die [hier](backup-azure-arm-userestapi-backupazurevms.md#undo-the-stop-protection-and-delete-data)worden beschreven.
2. Schakel vervolgens de functie voor voorlopig verwijderen met REST API uit met behulp van de stappen die [hier](use-restapi-update-vault-properties.md#update-soft-delete-state-using-rest-api)worden beschreven.
3. Verwijder vervolgens de back-ups met REST API zoals [hier](backup-azure-arm-userestapi-backupazurevms.md#stop-protection-and-delete-data)wordt vermeld.

## <a name="encryption"></a>Versleuteling

### <a name="encryption-of-backup-data-using-microsoft-managed-keys"></a>Versleuteling van back-upgegevens met door micro soft beheerde sleutels

Back-upgegevens worden automatisch versleuteld met Azure Storage versleuteling. Versleuteling beschermt uw gegevens en helpt u om te voldoen aan de beveiligings-en nalevings verplichtingen van uw organisatie. Gegevens worden op transparante wijze versleuteld en ontsleuteld met 256-bits AES-versleuteling, een van de krach tigste blok cijfers die beschikbaar zijn en FIPS 140-2 compatibel is. Azure Storage versleuteling lijkt op BitLocker-versleuteling in Windows.

Binnen Azure worden gegevens in transit tussen Azure Storage en de kluis beveiligd door HTTPS. Deze gegevens blijven op het Azure-backbone-netwerk.

Zie [Azure Storage versleuteling voor Data-at-rest](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)voor meer informatie. Raadpleeg de [Veelgestelde vragen over Azure backup](https://docs.microsoft.com/azure/backup/backup-azure-backup-faq#encryption) voor het beantwoorden van vragen die u mogelijk hebt over versleuteling.

### <a name="encryption-of-backup-data-using-customer-managed-keys"></a>Versleuteling van back-upgegevens met door de klant beheerde sleutels

Tijdens het maken van een back-up van Azure Virtual Machines hebt u ook de mogelijkheid om uw back-upgegevens te versleutelen in de Recovery Services kluis met de versleutelings sleutels die zijn opgeslagen in de Azure Key Vault.

>[!NOTE]
>Deze functie wordt momenteel gebruikt. Vul [deze enquête](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapURE9TTDRIUEUyNFhNT1lZS1BNVDdZVllHWi4u) in als u uw back-upgegevens wilt versleutelen met door de klant beheerde sleutels. Houd er rekening mee dat de mogelijkheid om deze functie te gebruiken afhankelijk is van de Azure Backup service.

### <a name="backup-of-managed-disk-vm-encrypted-using-customer-managed-keys"></a>Back-up van de beheerde schijf-VM is versleuteld met door de klant beheerde sleutels

Met Azure Backup kunt u een back-up maken van Azure Virtual Machines met schijven die zijn versleuteld met door de klant beheerde sleutels. Raadpleeg [Encrypting of Managed disks with door de klant beheerde sleutels](https://docs.microsoft.com/azure/virtual-machines/windows/disk-encryption#customer-managed-keys)voor meer informatie.

### <a name="backup-of-encrypted-vms"></a>Back-up van versleutelde Vm's

U kunt met behulp van de Azure Backup-Service back-ups maken van virtuele Windows-of Linux Azure-machines (Vm's) met versleutelde schijven. Zie [back-up en herstel van versleutelde virtuele machines met Azure backup](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption)voor instructies.

## <a name="other-security-features"></a>Andere beveiligings functies

### <a name="protection-of-azure-backup-recovery-points"></a>Beveiliging van Azure Backup herstel punten

Opslag accounts die worden gebruikt door Recovery Services-kluizen, zijn geïsoleerd en kunnen niet worden geopend door gebruikers voor schadelijke doel einden. De toegang is alleen toegestaan via Azure Backup beheer bewerkingen, zoals herstellen. Deze beheer bewerkingen worden beheerd via op rollen gebaseerde Access Control (RBAC).

Zie [Access Control op rollen gebaseerd gebruiken om Azure backup herstel punten te beheren](https://docs.microsoft.com/azure/backup/backup-rbac-rs-vault)voor meer informatie.

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

### <a name="for-soft-delete"></a>Voor zacht verwijderen

#### <a name="do-i-need-to-enable-the-soft-delete-feature-on-every-vault"></a>Moet ik de functie voor voorlopig verwijderen inschakelen voor elke kluis?

Nee, het is standaard gemaakt en ingeschakeld voor alle Recovery Services-kluizen.

#### <a name="can-i-configure-the-number-of-days-for-which-my-data-will-be-retained-in-soft-deleted-state-after-delete-operation-is-complete"></a>Kan ik het aantal dagen configureren dat mijn gegevens behouden blijven in de modus voor zacht verwijderen nadat de bewerking is voltooid?

Nee, het is vast tot 14 dagen na het verwijderen van de bewerking.

#### <a name="do-i-need-to-pay-the-cost-for-this-additional-14-day-retention"></a>Moet ik de kosten voor deze extra retentie van 14 dagen betalen?

Nee, deze extra Bewaar periode van 14 dagen is gratis als onderdeel van de functie voor zacht verwijderen.

#### <a name="can-i-perform-a-restore-operation-when-my-data-is-in-soft-delete-state"></a>Kan ik een herstel bewerking uitvoeren wanneer mijn gegevens de status zacht verwijderen hebben?

Nee, u moet de tijdelijke verwijderde resource verwijderen om te herstellen. Met de bewerking voor het ongedaan maken van de verwijdering wordt de resource weer **gestopt met de status beveiliging stoppen met gegevens behouden** , waarin u naar een wille keurig moment kunt herstellen. De garbage collector blijft onderbroken in deze status.

#### <a name="will-my-snapshots-follow-the-same-lifecycle-as-my-recovery-points-in-the-vault"></a>Volgen mijn moment opnamen dezelfde levens cyclus als mijn herstel punten in de kluis?

Ja.

#### <a name="how-can-i-trigger-the-scheduled-backups-again-for-a-soft-deleted-resource"></a>Hoe kan ik de geplande back-ups opnieuw activeren voor een tijdelijke verwijderde resource?

Als u de verwijdering opheffen, gevolgd door de bewerking hervatten wordt de resource opnieuw beveiligd. Met de bewerking hervatten wordt een back-upbeleid gekoppeld om de geplande back-ups te activeren met de geselecteerde Bewaar periode. Daarnaast wordt de garbage collector uitgevoerd zodra de bewerking hervatten is voltooid. Als u een herstel bewerking wilt uitvoeren vanaf een herstel punt dat voorbij de verloop datum ligt, wordt u geadviseerd om het te doen voordat u de hervatting hervat.

#### <a name="can-i-delete-my-vault-if-there-are-soft-deleted-items-in-the-vault"></a>Kan ik mijn kluis verwijderen als er sprake is van tijdelijke verwijderde items in de kluis?

De Recovery Services kluis kan niet worden verwijderd als er back-upitems in de kluis worden verwijderd. De voorlopig verwijderde items worden definitief verwijderd 14 dagen na de verwijderings bewerking. Als u 14 dagen niet kunt wachten, schakelt u de [optie voorlopig verwijderen uit](#disabling-soft-delete), verwijdert u de voorlopig verwijderde items en verwijdert u deze opnieuw om ze definitief te verwijderen. Nadat u hebt gecontroleerd of er geen beveiligde items zijn en geen tijdelijke verwijderde items, kan de kluis worden verwijderd.  

#### <a name="can-i-delete-the-data-earlier-than-the-14-days-soft-delete-period-after-deletion"></a>Kan ik de gegevens die ouder zijn dan de 14 dagen na het verwijderen verwijderen?

Nee. U kunt het verwijderen van de voorlopig verwijderde items niet afdwingen. deze worden na 14 dagen automatisch verwijderd. Deze beveiligings functie is ingeschakeld om de back-upgegevens te beschermen tegen onbedoelde of schadelijke verwijderingen.  U moet 14 dagen wachten voordat u een andere bewerking op de virtuele machine uitvoert.  Voor tijdelijke verwijderde items worden er geen kosten in rekening gebracht.  Neem contact op met micro soft ondersteuning als u de virtuele machines die binnen 14 dagen zijn gemarkeerd voor zacht verwijderen, opnieuw moet beveiligen.

#### <a name="can-soft-delete-operations-be-performed-in-powershell-or-cli"></a>Kunnen er tijdelijke Verwijder bewerkingen worden uitgevoerd in Power shell of CLI?

U kunt tijdelijke Verwijder bewerkingen uitvoeren met behulp van [Power shell](#soft-delete-for-vms-using-azure-powershell). CLI wordt momenteel niet ondersteund.

#### <a name="is-soft-delete-supported-for-other-cloud-workloads-like-sql-server-in-azure-vms-and-sap-hana-in-azure-vms"></a>Wordt zacht verwijderen ondersteund voor andere werk belastingen in de Cloud, zoals SQL Server in azure-Vm's en SAP HANA in azure-Vm's?

Nee. Momenteel wordt een tijdelijke verwijdering alleen ondersteund voor virtuele machines van Azure.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [beveiligings controles voor Azure backup](backup-security-controls.md).
