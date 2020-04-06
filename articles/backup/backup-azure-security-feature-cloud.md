---
title: Beveiligingsfuncties om cloudworkloads te beschermen
description: Meer informatie over het gebruik van beveiligingsfuncties in Azure Backup om back-ups veiliger te maken.
ms.topic: conceptual
ms.date: 09/13/2019
ms.openlocfilehash: bd7c86e18114513a264a0f9252589533fb7ff2d3
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668742"
---
# <a name="security-features-to-help-protect-cloud-workloads-that-use-azure-backup"></a>Beveiligingsfuncties om cloudworkloads te beschermen die Azure Backup gebruiken

Zorgen over beveiligingsproblemen, zoals malware, ransomware en inbraak, nemen toe. Deze beveiligingsproblemen kunnen duur zijn, zowel in termen van geld als gegevens. Om dergelijke aanvallen te voorkomen, biedt Azure Backup nu beveiligingsfuncties om back-upgegevens te beschermen, zelfs na verwijdering.

Een dergelijke functie is soft delete. Met soft delete, zelfs als een kwaadwillende actor de back-up van een VM verwijdert (of back-upgegevens per ongeluk worden verwijderd), worden de back-upgegevens 14 extra dagen bewaard, waardoor het herstel van dat back-upitem zonder gegevensverlies mogelijk is. De extra 14 dagen bewaren van back-upgegevens in de status 'soft delete' brengen geen kosten met zich mee voor de klant. Azure versleutelt ook alle back-upgegevens in rust met [Storage Service Encryption](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) om uw gegevens verder te beveiligen.

Soft delete-beveiliging voor Azure-virtuele machines is over het algemeen beschikbaar.

>[!NOTE]
>Soft delete for SQL server in Azure VM and soft delete for SAP HANA in Azure VM workloads is now available in preview.<br>
>Als u zich wilt aanmelden voor de preview, schrijft u ons opAskAzureBackupTeam@microsoft.com

## <a name="soft-delete"></a>Zachte verwijderen

### <a name="soft-delete-for-vms"></a>Soft delete voor VM's

Soft delete voor VM's beschermt de back-ups van uw VM's tegen onbedoelde verwijdering. Zelfs nadat de back-ups zijn verwijderd, worden ze gedurende 14 extra dagen in soft-delete-status bewaard.

> [!NOTE]
> Soft delete beschermt alleen verwijderde back-upgegevens. Als een vm zonder back-up wordt verwijderd, worden de gegevens niet bewaard door de functie soft-delete. Alle resources moeten worden beveiligd met Azure Backup om volledige veerkracht te garanderen.
>

### <a name="supported-regions"></a>Ondersteunde regio’s

Soft delete wordt momenteel ondersteund in het Westen van de Centrale VS, Oost-Azië, Canada Centraal, Canada Oost, Frankrijk Centraal, Korea Centraal, Korea Zuid, UK South, UK West, Australia East, Australia South East, North Europe, West US, West US2, Central US, South East Asia, North Central US, South Central US, Japan East, Japan West, India South, India Central, India West, East US 2 , Zwitserland Noord, Zwitserland West, Noorwegen West, Noorwegen Oosten en alle nationale regio's.

### <a name="soft-delete-for-vms-using-azure-portal"></a>Soft delete voor VM's met Azure-portal

1. Als u de back-upgegevens van een virtuele machine wilt verwijderen, moet de back-up worden gestopt. Ga in de Azure-portal naar de kluis van uw herstelservices, klik met de rechtermuisknop op het back-upitem en kies **Back-up stoppen**.

   ![Schermafbeelding van back-upitems van Azure-portal](./media/backup-azure-security-feature-cloud/backup-stopped.png)

2. In het volgende venster krijgt u de keuze om de back-upgegevens te verwijderen of te bewaren. Als u **Back-upgegevens verwijderen** kiest en vervolgens **back-up stopt,** wordt de VM-back-up niet permanent verwijderd. In plaats daarvan worden de back-upgegevens gedurende 14 dagen bewaard in de zachte verwijderde status. Als **back-upgegevens verwijderen** wordt gekozen, wordt een e-mailwaarschuwing voor verwijderen verzonden naar de geconfigureerde e-id waarin de gebruiker wordt geïnformeerd dat er nog 14 dagen langer worden bewaard voor back-upgegevens. Ook wordt een e-mail waarschuwing verzonden op de 12e dag te informeren dat er nog twee dagen over om de verwijderde gegevens te doen herleven. De verwijdering wordt uitgesteld tot de 15e dag, wanneer permanente verwijdering zal plaatsvinden en een laatste e-mailwaarschuwing wordt verzonden met informatie over de permanente verwijdering van de gegevens.

   ![Schermafbeelding van Azure-portal, scherm Back-up stoppen](./media/backup-azure-security-feature-cloud/delete-backup-data.png)

3. Gedurende deze 14 dagen, in de Vault voor Herstelservices, verschijnt de zachte verwijderde VM met een rood pictogram 'soft-delete' ernaast.

   ![Schermafbeelding van Azure-portal, VM in de status soft delete](./media/backup-azure-security-feature-cloud/vm-soft-delete.png)

   > [!NOTE]
   > Als er in de kluis verwijderde back-upitems aanwezig zijn, kan de kluis op dat moment niet worden verwijderd. Probeer het verwijderen van de kluis nadat de back-upitems permanent zijn verwijderd en er geen item in zachte verwijderde status in de kluis is achtergebleven.

4. Als u de vm met zachte verwijderde gegevens wilt herstellen, moet deze eerst worden verwijderd. Als u de verwijdering wilt opheffen, kiest u de vm met zachte verwijderde gegevens en selecteert u de optie **Verwijderen opheffen**.

   ![Schermafbeelding van Azure-portal, VM verwijderen](./media/backup-azure-security-feature-cloud/choose-undelete.png)

   Er verschijnt een venster met de waarschuwing dat als de verwijdering wordt gekozen, alle herstelpunten voor de VM niet worden verwijderd en beschikbaar zijn voor het uitvoeren van een herstelbewerking. De VM zal worden bewaard in een "stop bescherming met gegevens te behouden" staat met back-ups onderbroken en back-upgegevens voor altijd bewaard zonder back-up beleid effectief.

   ![Schermafbeelding van Azure-portal, VM bevestigen verwijderen](./media/backup-azure-security-feature-cloud/undelete-vm.png)

   Op dit punt u de VM ook herstellen door **VM herstellen** te selecteren op het gekozen herstelpunt.  

   ![Schermafbeelding van Azure-portal, optie VM herstellen](./media/backup-azure-security-feature-cloud/restore-vm.png)

   > [!NOTE]
   > Garbage collector zal uitvoeren en schoon verlopen herstelpunten pas nadat de gebruiker de **cv-back-upbewerking** heeft uitgevoerd.

5. Nadat het ontzeggingsproces is voltooid, wordt de status terugnaar 'Back-up stoppen met gegevens bewaren' en vervolgens u **Back-up hervatten**kiezen. De **back-upbewerking hervatten** brengt het back-upitem in de actieve status terug, gekoppeld aan een back-upbeleid dat is geselecteerd door de gebruiker die de back-up- en bewaarschema's definieert.

   ![Schermafbeelding van Azure-portal, optie Back-up hervatten](./media/backup-azure-security-feature-cloud/resume-backup.png)

In dit stroomdiagram worden de verschillende stappen en statussen van een back-upitem weergegeven wanneer Soft Delete is ingeschakeld:

![Levenscyclus van soft-deleted back-up item](./media/backup-azure-security-feature-cloud/lifecycle.png)

Zie de sectie [Veelgestelde vragen](backup-azure-security-feature-cloud.md#frequently-asked-questions) hieronder voor meer informatie.

### <a name="soft-delete-for-vms-using-azure-powershell"></a>Soft delete voor VM's met Azure PowerShell

> [!IMPORTANT]
> De Az.RecoveryServices-versie die nodig is om soft-delete te gebruiken met Azure PS is min 2.2.0. Gebruik ```Install-Module -Name Az.RecoveryServices -Force``` om de nieuwste versie te krijgen.

Zoals hierboven beschreven voor Azure portal, is de reeks stappen hetzelfde tijdens het gebruik van Azure PowerShell.

#### <a name="delete-the-backup-item-using-azure-powershell"></a>Het back-upitem verwijderen met Azure PowerShell

Verwijder het back-upitem met de [PS-cmdlet Disable-AzRecoveryServicesBackupProtection.](https://docs.microsoft.com/powershell/module/az.recoveryservices/Disable-AzRecoveryServicesBackupProtection?view=azps-1.5.0)

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $myBkpItem -RemoveRecoveryPoints -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           DeleteBackupData     Completed            12/5/2019 12:44:15 PM     12/5/2019 12:44:50 PM     0488c3c2-accc-4a91-a1e0-fba09a67d2fb
```

De 'DeleteState' van het back-upitem verandert van 'NotDeleted' naar 'ToBeDeleted'. De back-upgegevens worden 14 dagen bewaard. Als u de verwijderingsbewerking wilt terugdraaien, moet het verwijderen ongedaan worden gemaakt.

#### <a name="undoing-the-deletion-operation-using-azure-powershell"></a>De verwijderingsbewerking ongedaan maken met Azure PowerShell

Haal eerst het relevante back-upitem op dat in de zachte delete-status staat (dat wil zeggen, op het punt staat te worden verwijderd).

```powershell

Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID | Where-Object {$_.DeleteState -eq "ToBeDeleted"}

Name                                     ContainerType        ContainerUniqueName                      WorkloadType         ProtectionStatus     HealthStatus         DeleteState
----                                     -------------        -------------------                      ------------         ----------------     ------------         -----------
VM;iaasvmcontainerv2;selfhostrg;AppVM1    AzureVM             iaasvmcontainerv2;selfhostrg;AppVM1       AzureVM              Healthy              Passed               ToBeDeleted

$myBkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID -Name AppVM1
```

Voer vervolgens de verwijderingsbewerking ongedaan met de [PS-cmdlet Ongedaan maken-AzRecoveryServicesBackupItemDeletion.](https://docs.microsoft.com/powershell/module/az.recoveryservices/undo-azrecoveryservicesbackupitemdeletion?view=azps-3.1.0)

```powershell
Undo-AzRecoveryServicesBackupItemDeletion -Item $myBKpItem -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           Undelete             Completed            12/5/2019 12:47:28 PM     12/5/2019 12:47:40 PM     65311982-3755-46b5-8e53-c82ea4f0d2a2
```

De 'DeleteState' van het back-upitem wordt teruggezet naar 'NotDeleted'. Maar de bescherming is nog steeds gestopt. [Hervat de back-up](https://docs.microsoft.com/azure/backup/backup-azure-vms-automation#change-policy-for-backup-items) om de beveiliging opnieuw in te schakelen.

### <a name="soft-delete-for-vms-using-rest-api"></a>Soft delete voor VM's met REST API

- Verwijder de back-ups met behulp van REST API zoals [hier](backup-azure-arm-userestapi-backupazurevms.md#stop-protection-and-delete-data)vermeld.
- Als de gebruiker deze verwijderingsbewerkingen ongedaan wil maken, raadpleegt u [de hier](backup-azure-arm-userestapi-backupazurevms.md#undo-the-stop-protection-and-delete-data)genoemde stappen.

## <a name="disabling-soft-delete"></a>Soft delete uitschakelen

Soft delete is standaard ingeschakeld op nieuw gemaakte kluizen om back-upgegevens te beschermen tegen onbedoelde of schadelijke verwijderingen.  Het uitschakelen van deze functie wordt afgeraden. De enige omstandigheid waarin u moet overwegen om soft delete uit te schakelen, is als u van plan bent uw beveiligde items naar een nieuwe kluis te verplaatsen en de 14 dagen die nodig zijn voor het verwijderen en opnieuw beveiligen (zoals in een testomgeving.) Alleen de eigenaar van de kluis kan deze functie uitschakelen. Als u deze functie uitschakelt, zullen alle toekomstige verwijderingen van beveiligde items resulteren in onmiddellijke verwijdering, zonder de mogelijkheid om te herstellen. Back-upgegevens die in zachte verwijderde status bestaan voordat deze functie worden uitgeschakeld, blijven gedurende 14 dagen in zachte verwijderde status. Als u deze onmiddellijk permanent wilt verwijderen, moet u deze verwijderen en opnieuw verwijderen om permanent te worden verwijderd.

### <a name="disabling-soft-delete-using-azure-portal"></a>Soft delete uitschakelen met Azure-portal

Voer de volgende stappen uit om soft delete uit te schakelen:

1. Ga in de Azure-portal naar uw kluis en ga vervolgens naar -> **Instellingeneigenschappen**. **Settings**
2. Selecteer in het deelvenster Eigenschappen de optie **Update van beveiligingsinstellingen** -> **.**  
3. Selecteer In het deelvenster Beveiligingsinstellingen onder **Soft Delete**de optie **Uitschakelen**.

![Soft delete uitschakelen](./media/backup-azure-security-feature-cloud/disable-soft-delete.png)

### <a name="disabling-soft-delete-using-azure-powershell"></a>Soft delete uitschakelen met Azure PowerShell

> [!IMPORTANT]
> De Az.RecoveryServices-versie die nodig is om soft-delete te gebruiken met Azure PS is min 2.2.0. Gebruik ```Install-Module -Name Az.RecoveryServices -Force``` om de nieuwste versie te krijgen.

Als u de [CMDlet Set-AzRecoveryServicesVaultBackupProperty](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty?view=azps-3.1.0) PS wilt uitschakelen.

```powershell
Set-AzRecoveryServicesVaultProperty -VaultId $myVaultID -SoftDeleteFeatureState Disable


StorageModelType       :
StorageType            :
StorageTypeState       :
EnhancedSecurityState  : Enabled
SoftDeleteFeatureState : Disabled
```

### <a name="disabling-soft-delete-using-rest-api"></a>Soft delete uitschakelen met REST API

Als u de soft-delete-functionaliteit wilt uitschakelen met restapi, raadpleegt u de [hier](use-restapi-update-vault-properties.md#update-soft-delete-state-using-rest-api)genoemde stappen.

## <a name="permanently-deleting-soft-deleted-backup-items"></a>Soft-verwijderde back-upitems permanent verwijderen

Back-upgegevens in zachte verwijderde status voordat deze functie wordt uitgeschakeld, blijven in zachte verwijderde status. Als u deze onmiddellijk definitief wilt verwijderen, verwijdert u deze en verwijdert u ze opnieuw om permanent te worden verwijderd.

### <a name="using-azure-portal"></a>Azure Portal gebruiken

Volg deze stappen:

1. Volg de stappen om soft delete uit te [schakelen.](#disabling-soft-delete)
2. Ga in de Azure-portal naar uw kluis, ga naar **Back-upitems**en kies de zachte verwijderde VM.

   ![Kies zachte verwijderde VM](./media/backup-azure-security-feature-cloud/vm-soft-delete.png)

3. Selecteer de optie **Verwijderen opheffen**.

   ![Verwijderen kiezen](./media/backup-azure-security-feature-cloud/choose-undelete.png)

4. Er verschijnt een venster. Selecteer **Verwijderen opheffen**.

   ![Verwijderen selecteren](./media/backup-azure-security-feature-cloud/undelete-vm.png)

5. Kies **Back-upgegevens verwijderen** om de back-upgegevens permanent te verwijderen.

   ![Back-upgegevens verwijderen kiezen](https://docs.microsoft.com/azure/backup/media/backup-azure-manage-vms/delete-backup-buttom.png)

6. Typ de naam van het back-upitem om te bevestigen dat u de herstelpunten wilt verwijderen.

   ![Typ de naam van het back-upitem](https://docs.microsoft.com/azure/backup/media/backup-azure-manage-vms/delete-backup-data1.png)

7. Als u de back-upgegevens voor het item wilt verwijderen, selecteert u **Verwijderen**. Een meldingbericht laat u weten dat de back-upgegevens zijn verwijderd.

### <a name="using-azure-powershell"></a>Azure PowerShell gebruiken

Als items zijn verwijderd voordat soft-delete is uitgeschakeld, worden ze in een zachte status verwijderd. Om ze onmiddellijk te verwijderen, moet de verwijderingsbewerking worden omgekeerd en vervolgens opnieuw worden uitgevoerd.

Identificeer de items die in zachte status zijn verwijderd.

```powershell

Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID | Where-Object {$_.DeleteState -eq "ToBeDeleted"}

Name                                     ContainerType        ContainerUniqueName                      WorkloadType         ProtectionStatus     HealthStatus         DeleteState
----                                     -------------        -------------------                      ------------         ----------------     ------------         -----------
VM;iaasvmcontainerv2;selfhostrg;AppVM1    AzureVM             iaasvmcontainerv2;selfhostrg;AppVM1       AzureVM              Healthy              Passed               ToBeDeleted

$myBkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID -Name AppVM1
```

Draai vervolgens de verwijderingsbewerking om die is uitgevoerd toen soft-delete is ingeschakeld.

```powershell
Undo-AzRecoveryServicesBackupItemDeletion -Item $myBKpItem -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           Undelete             Completed            12/5/2019 12:47:28 PM     12/5/2019 12:47:40 PM     65311982-3755-46b5-8e53-c82ea4f0d2a2
```

Aangezien de soft-delete nu is uitgeschakeld, zal de verwijderingsbewerking resulteren in onmiddellijke verwijdering van back-upgegevens.

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $myBkpItem -RemoveRecoveryPoints -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           DeleteBackupData     Completed            12/5/2019 12:44:15 PM     12/5/2019 12:44:50 PM     0488c3c2-accc-4a91-a1e0-fba09a67d2fb
```

### <a name="using-rest-api"></a>REST API gebruiken

Als items zijn verwijderd voordat soft-delete is uitgeschakeld, worden ze in een zachte status verwijderd. Om ze onmiddellijk te verwijderen, moet de verwijderingsbewerking worden omgekeerd en vervolgens opnieuw worden uitgevoerd.

1. Maak eerst de verwijderingsbewerkingen ongedaan met de [hier](backup-azure-arm-userestapi-backupazurevms.md#undo-the-stop-protection-and-delete-data)genoemde stappen.
2. Schakel vervolgens de soft-delete-functionaliteit uit met behulp van REST API met behulp van de [hier](use-restapi-update-vault-properties.md#update-soft-delete-state-using-rest-api)genoemde stappen.
3. Verwijder vervolgens de back-ups met behulp van REST API zoals [hier](backup-azure-arm-userestapi-backupazurevms.md#stop-protection-and-delete-data)vermeld.

## <a name="encryption"></a>Versleuteling

Al uw back-upgegevens worden automatisch versleuteld wanneer ze in de cloud worden opgeslagen met Azure Storage-versleuteling, waarmee u voldoen aan uw beveiligings- en nalevingsverplichtingen. Deze gegevens in rust worden versleuteld met behulp van 256-bits AES-encryptie, een van de sterkste blokcijfers die beschikbaar zijn, en is FIPS 140-2-compatibel.

Naast versleuteling in rust, worden al uw back-upgegevens onderweg overgedragen via HTTPS. Het blijft altijd op het Azure-backbone-netwerk.

Zie [Azure Storage-versleuteling voor gegevens in rust](https://docs.microsoft.com/azure/storage/common/storage-service-encryption). Raadpleeg de [veelgestelde vragen over Azure Backup](https://docs.microsoft.com/azure/backup/backup-azure-backup-faq#encryption) om eventuele vragen over versleuteling te beantwoorden.

### <a name="encryption-of-backup-data-using-platform-managed-keys"></a>Versleuteling van back-upgegevens met behulp van door het platform beheerde sleutels

Standaard worden al uw gegevens versleuteld met behulp van door het platform beheerde sleutels. U hoeft geen expliciete actie van uw kant te ondernemen om deze versleuteling in te schakelen en het is van toepassing op alle workloads die worden geback-upt naar uw Vault Recovery Services.

### <a name="encryption-of-backup-data-using-customer-managed-keys"></a>Versleuteling van back-upgegevens met door de klant beheerde sleutels

Wanneer u een back-up maakt van uw Azure Virtual Machines, u uw gegevens nu versleutelen met sleutels die eigendom zijn van en door u worden beheerd. Met Azure Backup u uw RSA-sleutels gebruiken die zijn opgeslagen in de Azure Key Vault voor het versleutelen van uw back-ups. De versleutelingssleutel die wordt gebruikt voor het versleutelen van back-ups kan afwijken van de sleutel die voor de bron wordt gebruikt. De gegevens worden beveiligd met behulp van een AES 256 gebaseerde data encryptie sleutel (DEK), die op zijn beurt wordt beveiligd met behulp van uw sleutels. Dit geeft u volledige controle over de gegevens en de sleutels. Om versleuteling mogelijk te maken, is het vereist dat de vault van Recovery Services toegang krijgt tot de versleutelingssleutel in de Azure Key Vault. U de sleutel uitschakelen of de toegang intrekken wanneer dat nodig is. U moet echter versleuteling inschakelen met behulp van uw sleutels voordat u probeert om items te beschermen voor de kluis.

>[!NOTE]
>Deze functie is momenteel in beperkte beschikbaarheid. Vul [deze enquête](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapURE9TTDRIUEUyNFhNT1lZS1BNVDdZVllHWi4u) in AskAzureBackupTeam@microsoft.com en stuur ons een e-mail als u uw back-upgegevens wilt versleutelen met door de klant beheerde sleutels. Houd er rekening mee dat de mogelijkheid om deze functie te gebruiken, is onderworpen aan goedkeuring van de Azure Backup-service.

### <a name="backup-of-managed-disk-vms-encrypted-using-customer-managed-keys"></a>Back-up van beheerde schijfVM's versleuteld met door de klant beheerde sleutels

Met Azure Backup u ook een back-up maken van uw Azure VM's die uw sleutel gebruiken voor serverversleuteling. De sleutel die wordt gebruikt voor het versleutelen van de schijven wordt opgeslagen in de Azure Key Vault en door u beheerd. Server-side encryptie met behulp van door de klant beheerde sleutels verschilt van Azure Disk Encryption, omdat ADE BitLocker (voor Windows) en DM-Crypt (voor Linux) gebruikt om in-guest encryptie uit te voeren, versleutelt SSE gegevens in de opslagservice, zodat u elk besturingssysteem of afbeeldingen voor uw VM's gebruiken. Raadpleeg [Versleuteling van beheerde schijven met door de klant beheerde sleutels](https://docs.microsoft.com/azure/virtual-machines/windows/disk-encryption#customer-managed-keys) voor meer details.

### <a name="backup-of-vms-encrypted-using-ade"></a>Back-up van VM's versleuteld met ADE

Met Azure Backup u ook een back-up maken van uw Azure Virtual-machines waarbij hun besturingssysteem- of gegevensschijven zijn versleuteld met Azure Disk Encryption. ADE gebruikt BitLocker voor Windows VM's en DM-Crypt voor Linux VM's om in-guest encryptie uit te voeren. Zie [Back-ups maken en versleutelde virtuele machines herstellen met Azure Backup](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption)voor meer informatie.

## <a name="private-endpoints"></a>Privéeindpunten

[!INCLUDE [Private Endpoints](../../includes/backup-private-endpoints.md)]

## <a name="other-security-features"></a>Andere beveiligingsfuncties

### <a name="protection-of-azure-backup-recovery-points"></a>Beveiliging van Azure Backup-herstelpunten

Opslagaccounts die worden gebruikt door kluizen van herstelservices, zijn geïsoleerd en kunnen niet toegankelijk zijn voor gebruikers voor kwaadwillige doeleinden. De toegang is alleen toegestaan via Azure Backup-beheerbewerkingen, zoals herstellen. Deze beheeractiviteiten worden gecontroleerd via Role-Based Access Control (RBAC).

Zie [Toegangsbeheer op basis van rollen gebruiken om herstelpunten voor Azure Backup te beheren](https://docs.microsoft.com/azure/backup/backup-rbac-rs-vault)voor meer informatie.

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

### <a name="for-soft-delete"></a>Voor Soft verwijderen

#### <a name="do-i-need-to-enable-the-soft-delete-feature-on-every-vault"></a>Moet ik de functie soft-delete op elke kluis inschakelen?

Nee, het is standaard gebouwd en ingeschakeld voor alle kluizen van de herstelservices.

#### <a name="can-i-configure-the-number-of-days-for-which-my-data-will-be-retained-in-soft-deleted-state-after-delete-operation-is-complete"></a>Kan ik het aantal dagen configureren waarvoor mijn gegevens in zachte status worden bewaard nadat de verwijderingsbewerking is voltooid?

Nee, het is vastgesteld op 14 dagen extra retentie na de verwijderingsbewerking.

#### <a name="do-i-need-to-pay-the-cost-for-this-additional-14-day-retention"></a>Moet ik de kosten betalen voor deze extra retentie van 14 dagen?

Nee, deze 14-daagse extra retentie wordt gratis geleverd als onderdeel van soft-delete functionaliteit.

#### <a name="can-i-perform-a-restore-operation-when-my-data-is-in-soft-delete-state"></a>Kan ik een herstelbewerking uitvoeren wanneer mijn gegevens in zachte verwijderingsstatus zijn?

Nee, u moet de verwijderde contactpersoon verwijderen om te kunnen herstellen. De bewerking verwijderen brengt de bron terug in de **stopbeveiliging met de status gegevens behouden,** waar u herstellen naar elk moment. Garbage collector blijft onderbroken in deze staat.

#### <a name="will-my-snapshots-follow-the-same-lifecycle-as-my-recovery-points-in-the-vault"></a>Volgen mijn momentopnamen dezelfde levenscyclus als mijn herstelpunten in de kluis?

Ja.

#### <a name="how-can-i-trigger-the-scheduled-backups-again-for-a-soft-deleted-resource"></a>Hoe kan ik de geplande back-ups opnieuw activeren voor een contactpersoon met zachte verwijderde bronnen?

Als u de verwijdering ongedaan maakt, wordt de resource opnieuw beschermd door de hervatting van de verwijdering. Hervattingsbewerking koppelt een back-upbeleid om de geplande back-ups te activeren met de geselecteerde bewaarperiode. Ook wordt de garbage collector uitgevoerd zodra de cv-bewerking is voltooid. Als u een herstel wilt uitvoeren vanaf een herstelpunt dat de vervaldatum heeft verstreken, wordt u geadviseerd dit te doen voordat u de hervattingsbewerking activeert.

#### <a name="can-i-delete-my-vault-if-there-are-soft-deleted-items-in-the-vault"></a>Kan ik mijn kluis verwijderen als er zachte verwijderde items in de kluis zijn?

De kluis Recovery Services kan niet worden verwijderd als er back-upitems in de status soft-verwijderd zijn in de kluis. De verwijderde items worden 14 dagen na de verwijderingsbewerking definitief verwijderd. Als u niet 14 dagen wachten, [schakelt u soft delete uit,](#disabling-soft-delete)verwijdert u de zachte verwijderde items en verwijdert u ze opnieuw om definitief te worden verwijderd. Nadat u ervoor hebt gezorgd dat er geen beveiligde items en geen zachte verwijderde items zijn, kan de kluis worden verwijderd.  

#### <a name="can-i-delete-the-data-earlier-than-the-14-days-soft-delete-period-after-deletion"></a>Kan ik de gegevens eerder verwijderen dan de 14 dagen soft-delete periode na verwijdering?

Nee. Je de verwijderde items niet dwingen, ze worden na 14 dagen automatisch verwijderd. Deze beveiligingsfunctie is ingeschakeld om de back-upgegevens te beschermen tegen onbedoelde of schadelijke verwijderingen.  U moet 14 dagen wachten voordat u een andere actie op de VM uitvoert.  Er zijn kosten in rekening gebracht met zachte items.  Als u de VM's die zijn gemarkeerd voor soft-delete binnen 14 dagen opnieuw wilt beveiligen in een nieuwe kluis, neemt u contact op met de ondersteuning van Microsoft.

#### <a name="can-soft-delete-operations-be-performed-in-powershell-or-cli"></a>Kunnen soft delete-bewerkingen worden uitgevoerd in PowerShell of CLI?

Soft delete-bewerkingen kunnen worden uitgevoerd met [PowerShell](#soft-delete-for-vms-using-azure-powershell). Op dit moment wordt CLI niet ondersteund.

#### <a name="is-soft-delete-supported-for-other-cloud-workloads-like-sql-server-in-azure-vms-and-sap-hana-in-azure-vms"></a>Wordt soft delete ondersteund voor andere cloudworkloads, zoals SQL Server in Azure VM's en SAP HANA in Azure VM's?

Nee. Momenteel wordt soft delete alleen ondersteund voor virtuele Azure-machines.

## <a name="next-steps"></a>Volgende stappen

- Lees meer over [beveiligingsbesturingselementen voor Azure Backup](backup-security-controls.md).
