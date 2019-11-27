---
title: Back-ups maken en herstellen Azure Files met Power shell
description: In dit artikel vindt u informatie over het maken van back-ups en het herstellen van Azure Files met behulp van de Azure Backup-service en Power shell.
ms.topic: conceptual
ms.date: 08/20/2019
ms.openlocfilehash: 78000bc669eb7a61f8698ad8c39ef49f65b245a2
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74224164"
---
# <a name="back-up-and-restore-azure-files-with-powershell"></a>Back-ups maken en herstellen Azure Files met Power shell

In dit artikel wordt beschreven hoe u Azure PowerShell gebruikt voor het maken van back-ups en het herstellen van een Azure Files bestands share met behulp van een [Azure Backup](backup-overview.md) Recovery Services kluis.

In dit artikel wordt uitgelegd hoe u:

> [!div class="checklist"]
>
> * Stel Power shell in en registreer de Azure Recovery Services-provider.
> * Maak een Recovery Services-kluis.
> * Configureer de back-up voor een Azure-bestands share.
> * Een back-uptaak uitvoeren.
> * Een back-up van een Azure-bestands share of een afzonderlijk bestand van een share herstellen.
> * Back-up-en herstel taken bewaken.

## <a name="before-you-start"></a>Voordat u begint

* [Meer informatie](backup-azure-recovery-services-vault-overview.md) over Recovery Services-kluizen.
* Meer informatie over de preview-mogelijkheden voor het [maken van back-ups van Azure-bestands shares](backup-azure-files.md).
* Controleer de Power shell-object hiërarchie voor Recovery Services.

## <a name="recovery-services-object-hierarchy"></a>Object hiërarchie Recovery Services

De object hiërarchie wordt in het volgende diagram samenvatten.

![Object hiërarchie Recovery Services](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Raadpleeg de naslag informatie **AZ. Recovery Services** [cmdlet](/powershell/module/az.recoveryservices) in de Azure-bibliotheek.

## <a name="set-up-and-install"></a>Instellen en installeren

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Stel Power shell als volgt in:

1. [Down load de nieuwste versie van AZ Power shell](/powershell/azure/install-az-ps). De mini maal vereiste versie is 1.0.0.

2. Zoek de Azure Backup Power shell-cmdlets met de volgende opdracht:

    ```powershell
    Get-Command *azrecoveryservices*
    ```

3. Controleer de aliassen en cmdlets voor Azure Backup, Azure Site Recovery en de Recovery Services kluis worden weer gegeven. Hier volgt een voor beeld van wat u kunt zien. Het is geen volledige lijst met cmdlets.

    ![Lijst met Recovery Services-cmdlets](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

4. Meld u aan bij uw Azure-account met **Connect-AzAccount**.
5. Op de webpagina die wordt weer gegeven, wordt u gevraagd uw account referenties in te voeren.

    * U kunt ook uw account referenties als een para meter in de cmdlet **Connect-AzAccount** met **-Credential**toevoegen.
    * Als u een CSP-partner bent die namens een Tenant werkt, geeft u de klant op als Tenant met behulp van hun tenantID of Tenant primaire domein naam. Een voor beeld is **Connect-AzAccount-Tenant** fabrikam.com.

6. Koppel het abonnement dat u wilt gebruiken met het account, omdat een account verschillende abonnementen kan hebben.

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

7. Als u Azure Backup voor de eerste keer gebruikt, gebruikt u de cmdlet **REGI ster-AzResourceProvider** om de Azure Recovery Services provider bij uw abonnement te registreren.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

8. Controleer of de providers zijn geregistreerd:

    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

9. Controleer in de uitvoer van de opdracht of **RegistrationState** is gewijzigd in **geregistreerd**. Als dat niet het geval is, voert u de cmdlet **REGI ster-AzResourceProvider** opnieuw uit.

## <a name="create-a-recovery-services-vault"></a>Een Recovery Services-kluis maken

De Recovery Services kluis is een resource manager-resource, dus u moet deze in een resource groep plaatsen. U kunt een bestaande resource groep gebruiken, maar u kunt ook een resource groep maken met de cmdlet **New-AzResourceGroup** . Wanneer u een resource groep maakt, geeft u de naam en de locatie voor de resource groep op.

Volg deze stappen om een Recovery Services kluis te maken.

1. Een kluis wordt in een resource groep geplaatst. Als u geen bestaande resource groep hebt, maakt u een nieuwe met de [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-1.4.0). In dit voor beeld maken we een nieuwe resource groep in de regio vs-West.

   ```powershell
   New-AzResourceGroup -Name "test-rg" -Location "West US"
   ```

2. Gebruik de cmdlet [New-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/New-AzRecoveryServicesVault?view=azps-1.4.0) om de kluis te maken. Geef dezelfde locatie op als de kluis die voor de resource groep is gebruikt.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```

3. Geef het type redundantie op dat moet worden gebruikt voor de kluis opslag.

   * U kunt [lokaal redundante opslag](../storage/common/storage-redundancy-lrs.md) of [geografisch redundante opslag](../storage/common/storage-redundancy-grs.md)gebruiken.
   * In het volgende voor beeld wordt de optie **-BackupStorageRedundancy** ingesteld voor de[set-AzRecoveryServicesBackupProperties](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) cmd voor **testvault** ingesteld op **georedundant**.

     ```powershell
     $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
     Set-AzRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
     ```

### <a name="view-the-vaults-in-a-subscription"></a>De kluizen in een abonnement weer geven

Als u alle kluizen in het abonnement wilt weer geven, gebruikt u [Get-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault?view=azps-1.4.0).

```powershell
Get-AzRecoveryServicesVault
```

De uitvoer ziet er ongeveer als volgt uit. Houd er rekening mee dat de bijbehorende resource groep en locatie worden gegeven.

```powershell
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

### <a name="set-the-vault-context"></a>De kluis context instellen

Sla het kluis object op in een variabele en stel de kluis context in.

* Voor veel Azure Backup-cmdlets is het Recovery Services kluis-object vereist als invoer, zodat het handig is om het kluis object op te slaan in een variabele.
* De context van de kluis is het type gegevens dat in de kluis wordt beveiligd. Stel deze in met [set-AzRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext?view=azps-1.4.0). Nadat de context is ingesteld, is deze van toepassing op alle volgende cmdlets.

In het volgende voor beeld wordt de kluis context voor **testvault**ingesteld.

```powershell
Get-AzRecoveryServicesVault -Name "testvault" | Set-AzRecoveryServicesVaultContext
```

### <a name="fetch-the-vault-id"></a>De kluis-ID ophalen

We zijn van plan de kluis context instelling af te nemen volgens Azure PowerShell richt lijnen. In plaats daarvan kunt u de kluis-ID opslaan of ophalen, en deze door geven aan relevante opdrachten. Als u dus de kluis context niet hebt ingesteld of de opdracht wilt opgeven die moet worden uitgevoerd voor een bepaalde kluis, geeft u de kluis-ID als volgt door aan alle relevante opdracht:

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewAFSPolicy" -WorkloadType "AzureFiles" -RetentionPolicy $retPol -SchedulePolicy $schPol -VaultID $vaultID
```

## <a name="configure-a-backup-policy"></a>Een back-upbeleid configureren

Met een back-upbeleid kunt u het schema voor back-ups opgeven en bepalen hoe lang back-ups van herstel punten moeten worden bewaard:

* Een back-upbeleid is gekoppeld aan ten minste één Bewaar beleid. Een Bewaar beleid bepaalt hoe lang een herstel punt wordt bewaard voordat het wordt verwijderd.
* Bekijk de standaard retentie van het back-upbeleid met [Get-AzRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject?view=azps-1.4.0).
* Bekijk het standaard schema voor back-upbeleid met [Get-AzRecoveryServicesBackupSchedulePolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject?view=azps-1.4.0).
* U kunt de cmdlet [New-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0) gebruiken om een nieuw back-upbeleid te maken. U het schema en de Bewaar beleidsobjecten invoert.

Standaard wordt een begin tijd gedefinieerd in het object plannings beleid. Gebruik het volgende voor beeld om de begin tijd te wijzigen in de gewenste start tijd. De gewenste start tijd moet ook in UTC zijn. In het onderstaande voor beeld wordt ervan uitgegaan dat de gewenste start tijd 01:00 uur UTC is voor dagelijkse back-ups.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureFiles"
$UtcTime = Get-Date -Date "2019-03-20 01:30:00Z"
$UtcTime = $UtcTime.ToUniversalTime()
$schpol.ScheduleRunTimes[0] = $UtcTime
```

> [!IMPORTANT]
> U moet de start tijd binnen 30 minuten meerdere keer opgeven. In het bovenstaande voor beeld kan de waarde alleen ' 01:00:00 ' of ' 02:30:00 ' zijn. De begin tijd mag niet ' 01:15:00 ' zijn

In het volgende voor beeld worden het plannings beleid en het Bewaar beleid opgeslagen in variabelen. Vervolgens worden deze variabelen gebruikt als para meters voor een nieuw beleid (**NewAFSPolicy**). **NewAFSPolicy** neemt dagelijks een back-up en behoudt deze 30 dagen.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureFiles"
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureFiles"
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewAFSPolicy" -WorkloadType "AzureFiles" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

De uitvoer ziet er ongeveer als volgt uit.

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewAFSPolicy           AzureFiles            AzureStorage              10/24/2019 1:30:00 AM
```

## <a name="enable-backup"></a>Back-up inschakelen

Nadat u het back-upbeleid hebt gedefinieerd, kunt u de beveiliging van de Azure-bestands share inschakelen met behulp van het beleid.

### <a name="retrieve-a-backup-policy"></a>Een back-upbeleid ophalen

U haalt het relevante beleids object op met [Get-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0). Gebruik deze cmdlet om een specifiek beleid op te halen of om de beleids regels weer te geven die zijn gekoppeld aan een type werk belasting.

#### <a name="retrieve-a-policy-for-a-workload-type"></a>Een beleid voor een type werk belasting ophalen

In het volgende voor beeld worden beleids regels opgehaald voor het type werk belasting **Azure files**.

```powershell
Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureFiles"
```

De uitvoer ziet er ongeveer als volgt uit.

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
dailyafs             AzureFiles         AzureStorage         1/10/2018 12:30:00 AM
```

> [!NOTE]
> De tijd zone van het veld **BackupTime** in Power shell is Universal Coordinated Time (UTC). Wanneer de back-uptijd wordt weer gegeven in de Azure Portal, wordt de tijd aangepast aan uw lokale tijd zone.

### <a name="retrieve-a-specific-policy"></a>Een specifiek beleid ophalen

Het volgende beleid haalt het back-upbeleid met de naam **dailyafs**.

```powershell
$afsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "dailyafs"
```

### <a name="enable-backup-and-apply-policy"></a>Back-ups inschakelen en beleid Toep assen

Schakel de beveiliging in met [Enable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection?view=azps-1.4.0). Nadat het beleid is gekoppeld aan de kluis, worden back-ups geactiveerd volgens het beleids schema.

In het volgende voor beeld wordt de beveiliging van de Azure-bestands share **testAzureFileShare** in het opslag account **testStorageAcct**ingeschakeld met het beleid **dailyafs**.

```powershell
Enable-AzRecoveryServicesBackupProtection -StorageAccountName "testStorageAcct" -Name "testAzureFS" -Policy $afsPol
```

De opdracht wacht totdat de taak beveiliging configureren is voltooid en een vergelijk bare uitvoer geeft, zoals wordt weer gegeven.

```cmd
WorkloadName       Operation            Status                 StartTime                                                                                                         EndTime                   JobID
------------             ---------            ------               ---------                                  -------                   -----
testAzureFS       ConfigureBackup      Completed            11/12/2018 2:15:26 PM     11/12/2018 2:16:11 PM     ec7d4f1d-40bd-46a4-9edb-3193c41f6bf6
```

## <a name="trigger-an-on-demand-backup"></a>Een back-up op aanvraag activeren

Gebruik [Backup-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem?view=azps-1.4.0) om een back-up op aanvraag uit te voeren voor een beveiligde Azure-bestands share.

1. Haal het opslag account en de bestands share op uit de container in de kluis die uw back-upgegevens bevat met [Get-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-Azrecoveryservicesbackupcontainer).
2. Als u een back-uptaak wilt starten, haalt u informatie op over de virtuele machine met [Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem).
3. Voer een back-up op aanvraag uit met[Backup-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-Azrecoveryservicesbackupitem).

Voer de back-up op aanvraag als volgt uit:

```powershell
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType "AzureFiles" -Name "testAzureFS"
$job =  Backup-AzRecoveryServicesBackupItem -Item $afsBkpItem
```

De opdracht retourneert een taak met een ID die moet worden bijgehouden, zoals wordt weer gegeven in het volgende voor beeld.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS       Backup               Completed            11/12/2018 2:42:07 PM     11/12/2018 2:42:11 PM     8bdfe3ab-9bf7-4be6-83d6-37ff1ca13ab6
```

De moment opnamen van Azure-bestands shares worden gebruikt tijdens het maken van de back-ups, dus de taak wordt uitgevoerd op het moment dat de opdracht deze uitvoer retourneert.

### <a name="using-on-demand-backups-to-extend-retention"></a>Back-ups op aanvraag gebruiken om Bewaar perioden uit te breiden

Back-ups op aanvraag kunnen worden gebruikt om uw moment opnamen gedurende tien jaar te bewaren. U kunt planners gebruiken om Power shell-scripts op aanvraag uit te voeren met de gekozen Bewaar periode en zo elke week, maand of jaar moment opnamen te maken. Raadpleeg de [beperkingen van back-ups op aanvraag](https://docs.microsoft.com/azure/backup/backup-azure-files-faq#how-many-on-demand-backups-can-i-take-per-file-share) met Azure backup bij het maken van reguliere moment opnamen.

Als u een voor beeld van scripts zoekt, kunt u het voorbeeld script in GitHub (<https://github.com/Azure-Samples/Use-PowerShell-for-long-term-retention-of-Azure-Files-Backup>) raadplegen met Azure Automation runbook waarmee u periodiek back-ups kunt plannen en ze zelfs tot wel tien jaar behoudt.

### <a name="modify-the-protection-policy"></a>Het beveiligings beleid wijzigen

Gebruik [Enable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection?view=azps-1.4.0)om het beleid te wijzigen dat wordt gebruikt voor het maken van een back-up van de Azure-bestands share. Geef het relevante back-upitem en het nieuwe back-upbeleid op.

In het volgende voor beeld wordt het **testAzureFS** -beveiligings beleid gewijzigd van **dailyafs** in **monthlyafs**.

```powershell
$monthlyafsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "monthlyafs"
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType AzureFiles -Name "testAzureFS"
Enable-AzRecoveryServicesBackupProtection -Item $afsBkpItem -Policy $monthlyafsPol
```

## <a name="restore-azure-file-shares-and-files"></a>Azure-bestands shares en-bestanden herstellen

U kunt een volledige bestands share of specifieke bestanden op de share herstellen. U kunt herstellen naar de oorspronkelijke locatie of naar een andere locatie.

### <a name="fetch-recovery-points"></a>Herstel punten ophalen

Gebruik [Get-AzRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint?view=azps-1.4.0) om alle herstel punten voor het back-upitem weer te geven.

In het volgende script:

* De variabele **$RP** is een matrix met herstel punten voor het geselecteerde back-upitem van de afgelopen zeven dagen.
* De matrix wordt in omgekeerde volg orde gesorteerd met het laatste herstel punt bij index **0**.
* Gebruik standaard-Power shell-matrix indexie om het herstel punt te kiezen.
* In het voor beeld selecteert **$RP [0]** het meest recente herstel punt.

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $afsBkpItem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()

$rp[0] | fl
```

De uitvoer ziet er ongeveer als volgt uit.

```powershell
FileShareSnapshotUri : https://testStorageAcct.file.core.windows.net/testAzureFS?sharesnapshot=2018-11-20T00:31:04.00000
                       00Z
RecoveryPointType    : FileSystemConsistent
RecoveryPointTime    : 11/20/2018 12:31:05 AM
RecoveryPointId      : 86593702401459
ItemName             : testAzureFS
Id                   : /Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Micros                      oft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;teststorageRG;testStorageAcct/protectedItems/AzureFileShare;testAzureFS/recoveryPoints/86593702401462
WorkloadType         : AzureFiles
ContainerName        : storage;teststorageRG;testStorageAcct
ContainerType        : AzureStorage
BackupManagementType : AzureStorage
```

Nadat het relevante herstel punt is geselecteerd, herstelt u de bestands share of het bestand naar de oorspronkelijke locatie of naar een andere locatie.

### <a name="restore-an-azure-file-share-to-an-alternate-location"></a>Een Azure-bestands share herstellen naar een alternatieve locatie

Gebruik [Restore-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem?view=azps-1.4.0) om het geselecteerde herstel punt te herstellen. Geef deze para meters op om de alternatieve locatie te identificeren:

* **TargetStorageAccountName**: het opslag account waarnaar de inhoud van de back-up wordt teruggezet. Het doel-opslag account moet zich op dezelfde locatie berichten als de kluis.
* **TargetFileShareName**: de bestands shares binnen het doel Storage-account waarnaar de inhoud van de back-up wordt teruggezet.
* **TargetFolder**: de map onder de bestands share waarop de gegevens worden teruggezet. Als de inhoud waarvan een back-up is gemaakt, moet worden hersteld naar een hoofdmap, geeft u de waarden van de doelmap op als een lege teken reeks.
* **ResolveConflict**: instructie als er een conflict is met de herstelde gegevens. Accepteert **overschrijven** of **overs Laan**.

Voer de cmdlet als volgt uit met de para meters:

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -ResolveConflict Overwrite
```

De opdracht retourneert een taak met een ID die moet worden bijgehouden, zoals wordt weer gegeven in het volgende voor beeld.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS        Restore              InProgress           12/10/2018 9:56:38 AM                               9fd34525-6c46-496e-980a-3740ccb2ad75
```

### <a name="restore-an-azure-file-to-an-alternate-location"></a>Een Azure-bestand terugzetten op een andere locatie

Gebruik [Restore-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem?view=azps-1.4.0) om het geselecteerde herstel punt te herstellen. Geef deze para meters op om de alternatieve locatie aan te duiden en het bestand dat u wilt herstellen, uniek te identificeren.

* **TargetStorageAccountName**: het opslag account waarnaar de inhoud van de back-up wordt teruggezet. Het doel-opslag account moet zich op dezelfde locatie berichten als de kluis.
* **TargetFileShareName**: de bestands shares binnen het doel Storage-account waarnaar de inhoud van de back-up wordt teruggezet.
* **TargetFolder**: de map onder de bestands share waarop de gegevens worden teruggezet. Als de inhoud waarvan een back-up is gemaakt, moet worden hersteld naar een hoofdmap, geeft u de waarden van de doelmap op als een lege teken reeks.
* **SourceFilePath**: het absolute pad van het bestand dat in de bestands share moet worden hersteld, als een teken reeks. Dit pad is hetzelfde pad dat wordt gebruikt in de Power shell **-cmdlet Get-AzStorageFile** .
* **SourceFileType**: Hiermee wordt aangegeven of een map of een bestand is geselecteerd. De **map** of het **bestand**wordt geaccepteerd.
* **ResolveConflict**: instructie als er een conflict is met de herstelde gegevens. Accepteert **overschrijven** of **overs Laan**.

De aanvullende para meters (SourceFilePath en SourceFileType) zijn alleen gerelateerd aan het afzonderlijke bestand dat u wilt herstellen.

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

Met deze opdracht wordt een taak geretourneerd met een ID die moet worden bijgehouden, zoals wordt weer gegeven in de vorige sectie.

### <a name="restore-azure-file-shares-and-files-to-the-original-location"></a>Azure-bestands shares en-bestanden naar de oorspronkelijke locatie herstellen

Wanneer u naar een oorspronkelijke locatie herstelt, hoeft u geen doel-en doel-gerelateerde para meters op te geven. Alleen **ResolveConflict** moet worden gegeven.

#### <a name="overwrite-an-azure-file-share"></a>Een Azure-bestands share overschrijven

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -ResolveConflict Overwrite
```

#### <a name="overwrite-an-azure-file"></a>Een Azure-bestand overschrijven

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

## <a name="track-backup-and-restore-jobs"></a>Back-up-en herstel taken bijhouden

Back-ups op aanvraag en herstel bewerkingen geven een taak samen met een ID, zoals wordt weer gegeven wanneer u [een back-up op aanvraag hebt uitgevoerd](#trigger-an-on-demand-backup). Gebruik de cmdlet [Get-AzRecoveryServicesBackupJobDetails](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob?view=azps-1.4.0) om de voortgang en Details van de taak bij te houden.

```powershell
$job = Get-AzRecoveryServicesBackupJob -JobId 00000000-6c46-496e-980a-3740ccb2ad75 -VaultId $vaultID

 $job | fl


IsCancellable        : False
IsRetriable          : False
ErrorDetails         : {Microsoft.Azure.Commands.RecoveryServices.Backup.Cmdlets.Models.AzureFileShareJobErrorInfo}
ActivityId           : 00000000-5b71-4d73-9465-8a4a91f13a36
JobId                : 00000000-6c46-496e-980a-3740ccb2ad75
Operation            : Restore
Status               : Failed
WorkloadName         : testAFS
StartTime            : 12/10/2018 9:56:38 AM
EndTime              : 12/10/2018 11:03:03 AM
Duration             : 01:06:24.4660027
BackupManagementType : AzureStorage

$job.ErrorDetails

 ErrorCode ErrorMessage                                          Recommendations
 --------- ------------                                          ---------------
1073871825 Microsoft Azure Backup encountered an internal error. Wait for a few minutes and then try the operation again. If the issue persists, please contact Microsoft support.
```

## <a name="next-steps"></a>Volgende stappen

[Meer informatie over](backup-azure-files.md) het maken van een back-up van Azure files in de Azure Portal.
