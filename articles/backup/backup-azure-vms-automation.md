---
title: Back-ups maken en Azure VM's herstellen met PowerShell
description: Beschrijft hoe u een back-up maken en Azure VM's herstellen met Azure Backup met PowerShell
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: 1d1074eea3d530b17904e2f49fba7c0d24e84e59
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80743290"
---
# <a name="back-up-and-restore-azure-vms-with-powershell"></a>Back-ups maken en Azure VM's herstellen met PowerShell

In dit artikel wordt uitgelegd hoe u een Azure VM back-ups maken en herstellen in een Azure [Backup](backup-overview.md) Recovery Services-kluis met PowerShell-cmdlets.

In dit artikel leert u het volgende:

> [!div class="checklist"]
>
> * Maak een vault van Recovery Services en stel de kluiscontext in.
> * Een back-upbeleid definiëren
> * Het back-upbeleid toepassen voor de beveiliging van meerdere virtuele machines
> * Activeer een on-demand back-uptaak voor de beveiligde virtuele machines Voordat u een back-up maken (of beschermen) van een virtuele machine, moet u de [vereisten](backup-azure-arm-vms-prepare.md) voltooien om uw omgeving voor te bereiden op het beschermen van uw VM's.

## <a name="before-you-start"></a>Voordat u begint

* [Meer informatie](backup-azure-recovery-services-vault-overview.md) over kluizen van Recovery Services.
* [Bekijk](backup-architecture.md#architecture-built-in-azure-vm-backup) de architectuur voor Azure VM-back-up, [lees meer over](backup-azure-vms-introduction.md) het back-upproces en [bekijk](backup-support-matrix-iaas.md) ondersteuning, beperkingen en vereisten.
* Controleer de PowerShell-objecthiërarchie voor herstelservices.

## <a name="recovery-services-object-hierarchy"></a>Objecthiërarchie van Herstelservices

De objecthiërarchie wordt samengevat in het volgende diagram.

![Objecthiërarchie van Herstelservices](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Bekijk de verwijzing naar [de cmdlet-verwijzing](https://docs.microsoft.com/powershell/module/Az.RecoveryServices/?view=azps-1.4.0) **naar AZ.RecoveryServices** in de Azure-bibliotheek.

## <a name="set-up-and-register"></a>Instellen en registreren

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

U gaat als volgt aan de slag:

1. [Download de nieuwste versie van PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps)

2. Zoek de Azure Backup PowerShell-cmdlets die beschikbaar zijn door de volgende opdracht te typen:

    ```powershell
    Get-Command *azrecoveryservices*
    ```

    De aliassen en cmdlets voor Azure Backup, Azure Site Recovery en de vault Recovery Services worden weergegeven. De volgende afbeelding is een voorbeeld van wat u zult zien. Het is niet de volledige lijst van cmdlets.

    ![lijst van herstelservices](./media/backup-azure-vms-automation/list-of-recoveryservices-ps.png)

3. Meld u aan bij uw **Azure-account via Connect-AzAccount**. Deze cmdlet brengt een webpagina vraagt u voor uw accountreferenties:

    * U uw accountreferenties als parameter opnemen in de cmdlet **Connect-AzAccount** met behulp van de parameter **-Credential.**
    * Als u CSP-partner bent die namens een tenant werkt, geeft u de klant op als tenant, met behulp van hun tenantID of tenant primaire domeinnaam. Bijvoorbeeld: **Connect-AzAccount -Tenant "fabrikam.com"**

4. Koppel het abonnement dat u wilt gebruiken aan het account, omdat een account meerdere abonnementen kan hebben:

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

5. Als u Azure Backup voor de eerste keer gebruikt, moet u de cmdlet **[Register-AzResourceProvider](https://docs.microsoft.com/powershell/module/az.resources/register-azresourceprovider)** gebruiken om de Azure Recovery Service-provider met uw abonnement te registreren.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

6. U controleren of de providers zich hebben geregistreerd met de volgende opdrachten:

    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

    In de opdrachtuitvoer moet de **registratiestaat** worden gewijzigd in **Geregistreerd**. Zo niet, voer dan gewoon de **[cmdlet Register-AzResourceProvider](https://docs.microsoft.com/powershell/module/az.resources/register-azresourceprovider)** opnieuw uit.

## <a name="create-a-recovery-services-vault"></a>Een Recovery Services-kluis maken

De volgende stappen leiden u door het maken van een Vault Recovery Services. Een vault van Recovery Services is anders dan een back-upkluis.

1. De kluis Recovery Services is een resourcemanager-bron, dus u moet deze in een resourcegroep plaatsen. U een bestaande resourcegroep gebruiken of een resourcegroep maken met de cmdlet **[Nieuw-AzResourceGroep.](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup)** Geef bij het maken van een resourcegroep de naam en locatie voor de resourcegroep op.  

    ```powershell
    New-AzResourceGroup -Name "test-rg" -Location "West US"
    ```

2. Gebruik de cmdlet [New-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/new-azrecoveryservicesvault?view=azps-1.4.0) om de kluis Recovery Services te maken. Zorg ervoor dat u dezelfde locatie voor de kluis opgeeft als voor de resourcegroep.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```

3. Geef het type opslagredundantie op dat moet worden gebruikt; u [Lokaal redundante opslag (LRS)](../storage/common/storage-redundancy-lrs.md) of [Geo Redundant Storage (GRS)](../storage/common/storage-redundancy-grs.md)gebruiken. In het volgende voorbeeld ziet u de optie -BackupStorageRedundancy voor testvault is ingesteld op GeoRedundant.

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
    Set-AzRecoveryServicesBackupProperty  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

   > [!TIP]
   > Voor veel Azure Backup-cmdlets is het object Recovery Services-kluis als invoer vereist. Daarom is het handiger het object Backup Recovery Services-kluis in een variabele op te slaan.
   >
   >

## <a name="view-the-vaults-in-a-subscription"></a>Bekijk de kluizen in een abonnement

Als u alle kluizen in het abonnement wilt bekijken, gebruikt u [Get-AzRecoveryServicesVault:](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault?view=azps-1.4.0)

```powershell
Get-AzRecoveryServicesVault
```

De uitvoer is vergelijkbaar met het volgende voorbeeld, let op dat de bijbehorende ResourceGroupName en Locatie worden verstrekt.

```output
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

## <a name="back-up-azure-vms"></a>Back-ups maken van Azure-VM's

Gebruik een kluis van Recovery Services om uw virtuele machines te beschermen. Voordat u de beveiliging toepast, stelt u de kluiscontext in (het type gegevens dat in de kluis wordt beschermd) in en controleert u het beveiligingsbeleid. Het beveiligingsbeleid is de planning wanneer de back-uptaken worden uitgevoerd en hoe lang elke back-upmomentopname wordt bewaard.

### <a name="set-vault-context"></a>Vault-context instellen

Voordat u beveiliging op een vm inschakelt, gebruikt u [Set-AzRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext?view=azps-1.4.0) om de vault-context in te stellen. Zodra deze is ingesteld, is deze op alle navolgende cmdlets van toepassing. In het volgende voorbeeld wordt de context van de kluis ingesteld voor de *kluis, testvault*.

```powershell
Get-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "Contoso-docs-rg" | Set-AzRecoveryServicesVaultContext
```

### <a name="fetch-the-vault-id"></a>De kluis-id ophalen

We zijn van plan de instelling voor de kluiscontext te deprecating in overeenstemming met azure PowerShell-richtlijnen. In plaats daarvan u de kluis-id opslaan of ophalen en deze doorgeven aan relevante opdrachten. Dus als u de vaultcontext niet hebt ingesteld of de opdracht wilt opgeven die moet worden uitgevoerd voor een bepaalde kluis, geeft u de kluis-ID als '-vaultID' als volgt door aan alle relevante opdrachten:

```powershell
$targetVault = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault"
$targetVault.ID
```

of

```powershell
$targetVaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
```

### <a name="modifying-storage-replication-settings"></a>Instellingen voor opslagreplicatie wijzigen

De opdracht [Set-AzRecoveryServicesBackupProperty gebruiken](https://docs.microsoft.com/powershell/module/az.recoveryservices/Set-AzRecoveryServicesBackupProperty) om de replicatieconfiguratie van de opslag van de kluis in te stellen op LRS/GRS

```powershell
Set-AzRecoveryServicesBackupProperty -Vault $targetVault -BackupStorageRedundancy GeoRedundant/LocallyRedundant
```

> [!NOTE]
> Opslagredundantie kan alleen worden gewijzigd als er geen back-upitems zijn die zijn beveiligd met de kluis.

### <a name="create-a-protection-policy"></a>Een beveiligingsbeleid maken

Als u een Recovery Services-kluis maakt, gaat deze gepaard met standaardbeleid voor beveiliging en retentie. Volgens het standaardbeveiligingsbeleid wordt elke dag op een bepaald tijdstip een back-uptaak getriggerd. Volgens het standaardbewaarbeleid wordt het dagelijkse herstelpunt gedurende dertig dagen bewaard. U het standaardbeleid gebruiken om uw vm snel te beveiligen en het beleid later met verschillende details te bewerken.

Gebruik **[Get-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy)** om het beveiligingsbeleid te bekijken dat beschikbaar is in de kluis. U deze cmdlet gebruiken om een specifiek beleid te krijgen of om het beleid te bekijken dat is gekoppeld aan een werkbelastingtype. In het volgende voorbeeld wordt beleid voor het type werkbelasting, AzureVM, opdeed.

```powershell
Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureVM" -VaultId $targetVault.ID
```

De uitvoer lijkt op die in het volgende voorbeeld:

```output
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
DefaultPolicy        AzureVM            AzureVM              4/14/2016 5:00:00 PM
```

> [!NOTE]
> De tijdzone van het veld BackupTime in PowerShell is UTC. Wanneer de back-uptijd echter wordt weergegeven in de Azure-portal, wordt de tijd aangepast aan uw lokale tijdzone.
>
>

Een beleid voor back-upbeveiliging is gekoppeld aan ten minste één bewaarbeleid. Een bewaarbeleid bepaalt hoe lang een herstelpunt wordt bewaard voordat het wordt verwijderd.

* Gebruik [Get-AzRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject) om het standaardbewaarbeleid weer te geven.
* Op dezelfde manier u [Get-AzRecoveryServicesBackupScheduleScheduleObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject) gebruiken om het standaardplanningsbeleid te verkrijgen.
* De cmdlet [Nieuw-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy) maakt een PowerShell-object met back-upbeleidsinformatie.
* De opties voor het plannings- en bewaarbeleid worden gebruikt als ingangen van de cmdlet Nieuw-AzRecoveryServicesBackupProtectionPolicy.

Standaard wordt een begintijd gedefinieerd in het beleidsobject Schema. Gebruik het volgende voorbeeld om de begintijd te wijzigen in de gewenste begintijd. De gewenste starttijd moet ook in UTC zijn. In het onderstaande voorbeeld wordt ervan uitgegaan dat de gewenste begintijd 01:00 AM UTC is voor dagelijkse back-ups.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureVM" 
$UtcTime = Get-Date -Date "2019-03-20 01:00:00Z"
$UtcTime = $UtcTime.ToUniversalTime()
$schpol.ScheduleRunTimes[0] = $UtcTime
```

> [!IMPORTANT]
> U moet de starttijd in 30 minuten veelvouden alleen. In het bovenstaande voorbeeld kan het alleen "01:00:00" of "02:30:00" zijn. De begintijd kan niet "01:15:00" zijn

In het volgende voorbeeld worden het planningsbeleid en het bewaarbeleid opgeslagen in variabelen. In het voorbeeld worden deze variabelen gebruikt om de parameters te definiëren bij het maken van een beveiligingsbeleid, *NewPolicy*.

```powershell
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM" 
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -WorkloadType "AzureVM" -RetentionPolicy $retPol -SchedulePolicy $schPol -VaultId $targetVault.ID
```

De uitvoer lijkt op die in het volgende voorbeeld:

```output
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewPolicy           AzureVM            AzureVM              4/24/2016 1:30:00 AM
```

### <a name="enable-protection"></a>Beveiliging inschakelen

Nadat u het beveiligingsbeleid hebt gedefinieerd, moet u het beleid voor een item nog steeds inschakelen. Gebruik [Enable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection) om bescherming mogelijk te maken. Voor het inschakelen van beveiliging zijn twee objecten vereist: het item en het beleid. Zodra het beleid is gekoppeld aan de kluis, wordt de back-upwerkstroom geactiveerd op het tijdstip dat is gedefinieerd in het beleidsschema.

> [!IMPORTANT]
> Terwijl ps wordt gebruikt om back-ups voor meerdere VM's tegelijk in te schakelen, moet u ervoor zorgen dat er niet meer dan 100 VM's aan zijn gekoppeld. Dit is een [aanbevolen best practice](https://docs.microsoft.com/azure/backup/backup-azure-vm-backup-faq#is-there-a-limit-on-number-of-vms-that-can-beassociated-with-a-same-backup-policy). Momenteel blokkeert de PS-client niet expliciet als er meer dan 100 VM's zijn, maar de controle is gepland om in de toekomst te worden toegevoegd.

De volgende voorbeelden maken bescherming voor het item, V2VM, met behulp van het beleid, NewPolicy. De voorbeelden verschillen op basis van de vraag of de VM is versleuteld en welk type versleuteling.

Ga als u de beveiliging inschakelen op **niet-versleutelde VM's van Resource Manager:**

```powershell
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -VaultId $targetVault.ID
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1" -VaultId $targetVault.ID
```

Als u de beveiliging op versleutelde VM's (versleuteld met BEK en KEK) wilt inschakelen, moet u de Azure Backup-service toestemming geven om sleutels en geheimen uit de sleutelkluis te lezen.

```powershell
Set-AzKeyVaultAccessPolicy -VaultName "KeyVaultName" -ResourceGroupName "RGNameOfKeyVault" -PermissionsToKeys backup,get,list -PermissionsToSecrets get,list -ServicePrincipalName 262044b1-e2ce-469f-a196-69ab7ada62d3
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -VaultId $targetVault.ID
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1" -VaultId $targetVault.ID
```

Als u de beveiliging op **versleutelde VM's wilt inschakelen (alleen versleuteld met BEK),** moet u de Azure Backup-service toestemming geven om geheimen uit de sleutelkluis te lezen.

```powershell
Set-AzKeyVaultAccessPolicy -VaultName "KeyVaultName" -ResourceGroupName "RGNameOfKeyVault" -PermissionsToSecrets backup,get,list -ServicePrincipalName 262044b1-e2ce-469f-a196-69ab7ada62d3
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -VaultId $targetVault.ID
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1" -VaultId $targetVault.ID
```

> [!NOTE]
> Als u de Azure Government-cloud gebruikt, gebruikt u de waarde ff281ffe-705c-4f53-9f37-a40e6f2c68f3 voor de parameter ServicePrincipalName in [set-AzKeyVaultAccessPolicy-cmdlet.](https://docs.microsoft.com/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy)
>

## <a name="monitoring-a-backup-job"></a>Een back-uptaak controleren

U langlopende bewerkingen, zoals back-uptaken, controleren zonder de Azure-portal te gebruiken. Gebruik de cmdlet [Get-AzRecoveryservicesBackupJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob) om de status van een lopende taak te krijgen. Deze cmdlet krijgt de back-uptaken voor een specifieke kluis en die kluis is opgegeven in de kluiscontext. In het volgende voorbeeld wordt de status van een lopende taak als array en wordt de status opgeslagen in de variabele $joblist.

```powershell
$joblist = Get-AzRecoveryservicesBackupJob –Status "InProgress" -VaultId $targetVault.ID
$joblist[0]
```

De uitvoer lijkt op die in het volgende voorbeeld:

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM             Backup               InProgress            4/23/2016                5:00:30 PM                cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

In plaats van deze taken te peilen voor voltooiing - wat onnodige extra code is - gebruik je de [Wait-AzRecoveryServicesBackupJob-cmdlet.](https://docs.microsoft.com/powershell/module/az.recoveryservices/wait-azrecoveryservicesbackupjob) Met deze cmdlet wordt de uitvoering onderbroken totdat de taak is voltooid of de opgegeven time-outwaarde is bereikt.

```powershell
Wait-AzRecoveryServicesBackupJob -Job $joblist[0] -Timeout 43200 -VaultId $targetVault.ID
```

## <a name="manage-azure-vm-backups"></a>Back-ups van Azure-VM's beheren

### <a name="modify-a-protection-policy"></a>Een beveiligingsbeleid wijzigen

Als u het beveiligingsbeleid wilt wijzigen, gebruikt u [Set-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy) om de objecten Schemabeleid of Bewaarbeleid te wijzigen.

#### <a name="modifying-scheduled-time"></a>Geplande tijd wijzigen

Wanneer u een beveiligingsbeleid maakt, krijgt u standaard een begintijd toegewezen. In de volgende voorbeelden ziet u hoe u de begintijd van een beveiligingsbeleid wijzigt.

````powershell
$SchPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureVM"
$UtcTime = Get-Date -Date "2019-03-20 01:00:00Z" (This is the time that the customer wants to start the backup)
$UtcTime = $UtcTime.ToUniversalTime()
$SchPol.ScheduleRunTimes[0] = $UtcTime
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -VaultId $targetVault.ID
Set-AzRecoveryServicesBackupProtectionPolicy -Policy $pol  -SchedulePolicy $SchPol -VaultId $targetVault.ID
````

#### <a name="modifying-retention"></a>Retentie wijzigen

In het volgende voorbeeld wordt het herstelpunt behoud gewijzigd in 365 dagen.

```powershell
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
$retPol.DailySchedule.DurationCountInDays = 365
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -VaultId $targetVault.ID
Set-AzRecoveryServicesBackupProtectionPolicy -Policy $pol  -RetentionPolicy $RetPol -VaultId $targetVault.ID
```

#### <a name="configuring-instant-restore-snapshot-retention"></a>Het configureren van het behouden van momentopnamen voor instant herstellen

> [!NOTE]
> Vanaf AZ PS versie 1.6.0 verder, kan men de instant restore snapshot retentieperiode bijwerken in het beleid met Powershell

````powershell
$bkpPol = Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureVM" -VaultId $targetVault.ID
$bkpPol.SnapshotRetentionInDays=7
Set-AzRecoveryServicesBackupProtectionPolicy -policy $bkpPol -VaultId $targetVault.ID
````

De standaardwaarde is 2, de gebruiker kan de waarde instellen met een min van 1 en max van 5. Voor het wekelijkse back-upbeleid is de periode ingesteld op 5 en kan deze niet worden gewijzigd.

#### <a name="creating-azure-backup-resource-group-during-snapshot-retention"></a>Azure Backup-brongroep maken tijdens het bewaren van momentopnamen

> [!NOTE]
> Vanaf Azure PS-versie 3.7.0 kan men de brongroep maken en bewerken die is gemaakt voor het opslaan van directe momentopnamen.

Raadpleeg de [azure backup-brongroep voor virtual machines-documentatie](https://docs.microsoft.com/azure/backup/backup-during-vm-creation#azure-backup-resource-group-for-virtual-machines) voor meer informatie over regels voor het maken van resources en andere relevante gegevens.

```powershell
$bkpPol = Get-AzureRmRecoveryServicesBackupProtectionPolicy -name "DefaultPolicyForVMs"
$bkpPol.AzureBackupRGName="Contosto_"
$bkpPol.AzureBackupRGNameSuffix="ForVMs"
Set-AzureRmRecoveryServicesBackupProtectionPolicy -policy $bkpPol
```

### <a name="trigger-a-backup"></a>Een back-up activeren

Gebruik [Backup-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem) om een back-uptaak te activeren. Als het de eerste back-up is, is het een volledige back-up. Latere back-ups nemen een incrementele kopie. In het volgende voorbeeld wordt een VM-back-up gedurende 60 dagen bewaard.

```powershell
$namedContainer = Get-AzRecoveryServicesBackupContainer -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM" -VaultId $targetVault.ID
$item = Get-AzRecoveryServicesBackupItem -Container $namedContainer -WorkloadType "AzureVM" -VaultId $targetVault.ID
$endDate = (Get-Date).AddDays(60).ToUniversalTime()
$job = Backup-AzRecoveryServicesBackupItem -Item $item -VaultId $targetVault.ID -ExpiryDateTimeUTC $endDate
```

De uitvoer lijkt op die in het volgende voorbeeld:

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM              Backup              InProgress          4/23/2016                  5:00:30 PM                cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

> [!NOTE]
> De tijdzone van de velden StartTime en EndTime in PowerShell is UTC. Wanneer de tijd echter wordt weergegeven in de Azure-portal, wordt de tijd aangepast aan uw lokale tijdzone.
>
>

### <a name="change-policy-for-backup-items"></a>Beleid wijzigen voor back-upitems

De gebruiker kan het bestaande beleid wijzigen of het beleid van het back-upitem wijzigen van Beleid1 naar Beleid2. Als u van beleid wilt wisselen voor een back-upitem, haalt u het desbetreffende beleid en maakt u een back-up van item en gebruikt u de opdracht [Enable-AzRecoveryServices](https://docs.microsoft.com/powershell/module/az.recoveryservices/Enable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) met back-upitem als parameter.

````powershell
$TargetPol1 = Get-AzRecoveryServicesBackupProtectionPolicy -Name <PolicyName> -VaultId $targetVault.ID
$anotherBkpItem = Get-AzRecoveryServicesBackupItem -WorkloadType AzureVM -BackupManagementType AzureVM -Name "<BackupItemName>" -VaultId $targetVault.ID
Enable-AzRecoveryServicesBackupProtection -Item $anotherBkpItem -Policy $TargetPol1 -VaultId $targetVault.ID
````

De opdracht wacht totdat de geconfigureerde back-up is voltooid en retourneert de volgende uitvoer.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
TestVM           ConfigureBackup      Completed            3/18/2019 8:00:21 PM      3/18/2019 8:02:16 PM      654e8aa2-4096-402b-b5a9-e5e71a496c4e
```

### <a name="stop-protection"></a>Beveiliging stoppen

#### <a name="retain-data"></a>Gegevens behouden

Als de gebruiker de bescherming wil stoppen, kan hij de [CMDlet Disable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Disable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) GEBRUIKEN. Dit stopt de geplande back-ups, maar de gegevens die tot nu toe zijn gemaakt, worden voor altijd bewaard.

````powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -Name "<backup item name>" -VaultId $targetVault.ID
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID
````

#### <a name="delete-backup-data"></a>Back-upgegevens verwijderen

Om de opgeslagen back-upgegevens in de kluis volledig te verwijderen, voegt u de vlag 'RemoveRecoveryPoints' /switch toe aan de [beveiligingsopdracht 'uitschakelen'.](#retain-data)

````powershell
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID -RemoveRecoveryPoints
````

## <a name="restore-an-azure-vm"></a>Een Azure-vm herstellen

Er is een belangrijk verschil tussen het herstellen van een VM met behulp van de Azure-portal en het herstellen van een VM met PowerShell. Met PowerShell is de herstelbewerking voltooid zodra de schijven en configuratiegegevens van het herstelpunt zijn gemaakt. De herstelbewerking maakt de virtuele machine niet. Als u een virtuele machine vanaf schijf wilt maken, raadpleegt u de sectie [De VM maken van herstelde schijven.](backup-azure-vms-automation.md#create-a-vm-from-restored-disks) Als u niet de hele virtuele machine wilt herstellen, maar wel een paar bestanden wilt herstellen of herstellen van een Azure VM-back-up, raadpleegt u de [sectie bestandsherstel](backup-azure-vms-automation.md#restore-files-from-an-azure-vm-backup).

> [!Tip]
> De herstelbewerking maakt de virtuele machine niet.
>
>

In de volgende afbeelding wordt de objecthiërarchie weergegeven van de RecoveryServicesVault tot het BackupRecoveryPoint.

![Objecthiërarchie Herstelservices met BackupContainer](./media/backup-azure-vms-arm-automation/backuprecoverypoint-only.png)

Als u back-upgegevens wilt herstellen, identificeert u het back-upitem en het herstelpunt dat de point-in-time-gegevens bevat. Gebruik [Restore-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem) om gegevens uit de kluis naar uw account te herstellen.

De basisstappen voor het herstellen van een Azure VM zijn:

* Selecteer de VM.
* Kies een herstelpunt.
* Herstel de schijven.
* Maak de VM op opgeslagen schijven.

### <a name="select-the-vm"></a>Selecteer de VM

Als u het PowerShell-object wilt ophalen dat het juiste back-upitem identificeert, start u vanaf de container in de kluis en werkt u zich een weg naar beneden in de objecthiërarchie. Als u de container wilt selecteren die de VM vertegenwoordigt, gebruikt u de cmdlet [Get-AzRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupcontainer) en pipet die naar de cmdlet [Get-AzRecoveryServicesBackupItem.](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem)

```powershell
$namedContainer = Get-AzRecoveryServicesBackupContainer  -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM" -VaultId $targetVault.ID
$backupitem = Get-AzRecoveryServicesBackupItem -Container $namedContainer  -WorkloadType "AzureVM" -VaultId $targetVault.ID
```

### <a name="choose-a-recovery-point"></a>Een herstelpunt kiezen

Gebruik de cmdlet [Get-AzRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint) om alle herstelpunten voor het back-upitem weer te geven. Kies vervolgens het herstelpunt om te herstellen. Als u niet zeker weet welk herstelpunt u wilt gebruiken, is het een goede gewoonte om het meest recente RecoveryPointType = AppConsistent-punt in de lijst te kiezen.

In het volgende script is de variabele, **$rp,** een array met herstelpunten voor het geselecteerde back-upitem van de afgelopen zeven dagen. De array wordt gesorteerd in omgekeerde volgorde van tijd met het laatste herstelpunt op index 0. Gebruik standaard PowerShell-arrayindexering om het herstelpunt te kiezen. In het voorbeeld selecteert $rp[0] het laatste herstelpunt.

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime() -VaultId $targetVault.ID
$rp[0]
```

De uitvoer lijkt op die in het volgende voorbeeld:

```output
RecoveryPointAdditionalInfo :
SourceVMStorageType         : NormalStorage
Name                        : 15260861925810
ItemName                    : VM;iaasvmcontainer;RGName1;V2VM
RecoveryPointId             : /subscriptions/XX/resourceGroups/ RGName1/providers/Microsoft.RecoveryServices/vaults/testvault/backupFabrics/Azure/protectionContainers/IaasVMContainer;iaasvmcontainer;RGName1;V2VM/protectedItems/VM;iaasvmcontainer; RGName1;V2VM/recoveryPoints/15260861925810
RecoveryPointType           : AppConsistent
RecoveryPointTime           : 4/23/2016 5:02:04 PM
WorkloadType                : AzureVM
ContainerName               : IaasVMContainer;iaasvmcontainer; RGName1;V2VM
ContainerType               : AzureVM
BackupManagementType        : AzureVM
```

### <a name="restore-the-disks"></a>De schijven herstellen

Gebruik de cmdlet [Restore-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem) om de gegevens en configuratie van een back-upitem te herstellen naar een herstelpunt. Zodra u een herstelpunt hebt geïdentificeerd, gebruikt u het als waarde voor de parameter **-RecoveryPoint.** In het bovenstaande monster was **$rp[0]** het te gebruiken herstelpunt. In de volgende voorbeeldcode is **$rp[0]** het herstelpunt dat moet worden gebruikt voor het herstellen van de schijf.

Ga als volgt te werk om de schijven en configuratiegegevens te herstellen:

```powershell
$restorejob = Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG" -VaultId $targetVault.ID
$restorejob
```

#### <a name="restore-managed-disks"></a>Beheerde schijven herstellen

> [!NOTE]
> Als de back-vm schijven heeft beheerd en u deze wilt herstellen als beheerde schijven, hebben we de mogelijkheid van Azure PowerShell RM-module v 6.7.0 geïntroduceerd. verder
>
>

Geef een extra parameter **TargetResourceGroupName** op om de RG op te geven waaraan beheerde schijven worden hersteld.

> [!NOTE]
> Het wordt ten zeerste aanbevolen om de parameter **TargetResourceGroupName** te gebruiken voor het herstellen van beheerde schijven, omdat dit resulteert in aanzienlijke prestatieverbeteringen. Vanaf Azure Powershell Az module 1.0 is deze parameter ook verplicht in het geval van een herstel met beheerde schijven
>
>

```powershell
$restorejob = Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG" -TargetResourceGroupName "DestRGforManagedDisks" -VaultId $targetVault.ID
```

Het **VMConfig.JSON-bestand** wordt hersteld naar het opslagaccount en de beheerde schijven worden hersteld naar het opgegeven doel-RG.

De uitvoer lijkt op die in het volgende voorbeeld:

```output
WorkloadName     Operation          Status               StartTime                 EndTime            JobID
------------     ---------          ------               ---------                 -------          ----------
V2VM              Restore           InProgress           4/23/2016 5:00:30 PM                        cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

Gebruik de cmdlet [Wait-AzRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/wait-azrecoveryservicesbackupjob) om te wachten tot de taak Herstellen is voltooid.

```powershell
Wait-AzRecoveryServicesBackupJob -Job $restorejob -Timeout 43200
```

Zodra de hersteltaak is voltooid, gebruikt u de cmdlet [Get-AzRecoveryServicesBackupJobDetails](https://docs.microsoft.com/powershell/module/az.recoveryservices/wait-azrecoveryservicesbackupjob) om de details van de herstelbewerking te krijgen. De eigenschap JobDetails beschikt over de informatie die nodig is om de VM opnieuw op te bouwen.

```powershell
$restorejob = Get-AzRecoveryServicesBackupJob -Job $restorejob -VaultId $targetVault.ID
$details = Get-AzRecoveryServicesBackupJobDetails -Job $restorejob -VaultId $targetVault.ID
```

Zodra u de schijven hebt hersteld, gaat u naar de volgende sectie om de vm te maken.

## <a name="replace-disks-in-azure-vm"></a>Schijven vervangen in Azure VM

Voer de volgende stappen uit om de schijven en configuratiegegevens te vervangen:

* Stap 1: [De schijven herstellen](backup-azure-vms-automation.md#restore-the-disks)
* Stap 2: [Gegevensschijf loskoppelen met PowerShell](https://docs.microsoft.com/azure/virtual-machines/windows/detach-disk#detach-a-data-disk-using-powershell)
* Stap 3: [Gegevensschijf koppelen aan Windows VM met PowerShell](https://docs.microsoft.com/azure/virtual-machines/windows/attach-disk-ps)

## <a name="create-a-vm-from-restored-disks"></a>Een VM maken op basis van herstelde schijven

Nadat u de schijven hebt hersteld, gebruikt u de volgende stappen om de virtuele machine vanaf schijf te maken en te configureren.

> [!NOTE]
>
> 1. AzureAz module 3.0.0 of hoger is vereist. <br>
> 2. Als u versleutelde VM's wilt maken van herstelde schijven, moet uw Azure-rol toestemming hebben om de actie uit te voeren, **Microsoft.KeyVault/vaults/deploy/action**. Als uw rol deze machtiging niet heeft, maakt u een aangepaste rol met deze actie. Zie [Aangepaste rollen in Azure RBAC](../role-based-access-control/custom-roles.md)voor meer informatie. <br>
> 3. Nadat u schijven hebt hersteld, u nu een implementatiesjabloon krijgen waarmee u direct een nieuwe virtuele machine maken. Geen verschillende PS-cmdlets meer om beheerde/onbeheerde VM's te maken die versleuteld/onversleuteld zijn.<br>
> <br>

### <a name="create-a-vm-using-the-deployment-template"></a>Een VM maken met de implementatiesjabloon

De resulterende taakgegevens geven de sjabloon URI die kan worden opgevraagd en geïmplementeerd.

```powershell
   $properties = $details.properties
   $storageAccountName = $properties["Target Storage Account Name"]
   $containerName = $properties["Config Blob Container Name"]
   $templateBlobURI = $properties["Template Blob Uri"]
```

De sjabloon is niet direct toegankelijk omdat deze onder het opslagaccount van een klant en de opgegeven container valt. We hebben de volledige URL (samen met een tijdelijk SAS-token) nodig om toegang te krijgen tot deze sjabloon.

1. Haal eerst de sjabloonnaam uit de sjabloonBlobURI. Het formaat wordt hieronder vermeld. U de gesplitste bewerking in Powershell gebruiken om de uiteindelijke sjabloonnaam uit deze URL te halen.

```http
https://<storageAccountName.blob.core.windows.net>/<containerName>/<templateName>
```

2. Dan kan de volledige URL worden gegenereerd zoals [hier](https://docs.microsoft.com/azure/azure-resource-manager/templates/secure-template-with-sas-token?tabs=azure-powershell#provide-sas-token-during-deployment)uitgelegd.

```powershell
Set-AzCurrentStorageAccount -Name $storageAccountName -ResourceGroupName <StorageAccount RG name>
$templateBlobFullURI = New-AzStorageBlobSASToken -Container $containerName -Blob <templateName> -Permission r -FullUri
```

3. Implementeer de sjabloon om een nieuwe virtuele machine te maken zoals [hier](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)uitgelegd .

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment ResourceGroupName ExampleResourceGroup -TemplateUri $templateBlobFullURI -storageAccountType Standard_GRS
```

### <a name="create-a-vm-using-the-config-file"></a>Een VM maken met het config-bestand

In de volgende sectie worden stappen weergegeven die nodig zijn om een VM te maken met het bestand 'VMConfig'.

> [!NOTE]
> Het wordt ten zeerste aanbevolen om de hierboven beschreven implementatiesjabloon te gebruiken om een VM te maken. Deze sectie (Punten 1-6) zal binnenkort worden afgeschaft.

1. Queryer de herstelde schijfeigenschappen voor de taakgegevens.

   ```powershell
   $properties = $details.properties
   $storageAccountName = $properties["Target Storage Account Name"]
   $containerName = $properties["Config Blob Container Name"]
   $configBlobName = $properties["Config Blob Name"]
   ```

2. Stel de Azure-opslagcontext in en herstel het JSON-configuratiebestand.

   ```powershell
   Set-AzCurrentStorageAccount -Name $storageaccountname -ResourceGroupName "testvault"
   $destination_path = "C:\vmconfig.json"
   Get-AzStorageBlobContent -Container $containerName -Blob $configBlobName -Destination $destination_path
   $obj = ((Get-Content -Path $destination_path -Raw -Encoding Unicode)).TrimEnd([char]0x00) | ConvertFrom-Json
   ```

3. Gebruik het JSON-configuratiebestand om de VM-configuratie te maken.

   ```powershell
   $vm = New-AzVMConfig -VMSize $obj.'properties.hardwareProfile'.vmSize -VMName "testrestore"
   ```

4. Voeg de osschijf en gegevensschijven toe. Deze stap bevat voorbeelden voor verschillende beheerde en versleutelde VM-configuraties. Gebruik het voorbeeld dat bij uw VM-configuratie past.

* **Niet-beheerde en niet-versleutelde VM's** - Gebruik het volgende voorbeeld voor niet-beheerde, niet-versleutelde VM's.

```powershell
       Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
       $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
       foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
       {
        $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
       }
```

* **Niet-beheerde en versleutelde VM's met Azure AD (alleen BEK)** - Voor niet-beheerde, versleutelde VM's met Azure AD (alleen versleuteld met BEK), moet u het geheim van de sleutelkluis herstellen voordat u schijven koppelen. Zie Een versleutelde [virtuele machine herstellen vanaf een herstelpunt voor Azure Backup](backup-azure-restore-key-secret.md)voor meer informatie. In het volgende voorbeeld ziet u hoe u besturingssysteem- en gegevensschijven voor versleutelde VM's koppelen. Wanneer u de osschijf instelt, moet u het relevante besturingssysteemtype vermelden.

```powershell
      $dekUrl = "https://ContosoKeyVault.vault.azure.net:443/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
      $dekUrl = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
      Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.storageProfile'.osDisk.vhd.uri -DiskEncryptionKeyUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -CreateOption "Attach" -Windows/Linux
      $vm.StorageProfile.OsDisk.OsType = $obj.'properties.storageProfile'.osDisk.osType
      foreach($dd in $obj.'properties.storageProfile'.dataDisks)
      {
       $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
      }
```

* **Niet-beheerde en versleutelde VM's met Azure AD (BEK en KEK)** - Voor niet-beheerde, versleutelde VM's met Azure AD (versleuteld met BEK en KEK), herstelt u de sleutel en het geheim van de sleutelkluis voordat u de schijven koppelt. Zie [Een versleutelde virtuele machine herstellen vanaf een herstelpunt voor Azure Backup](backup-azure-restore-key-secret.md)voor meer informatie. In het volgende voorbeeld ziet u hoe u besturingssysteem- en gegevensschijven voor versleutelde VM's koppelen.

```powershell
      $dekUrl = "https://ContosoKeyVault.vault.azure.net:443/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
      $kekUrl = "https://ContosoKeyVault.vault.azure.net:443/keys/ContosoKey007/x9xxx00000x0000x9b9949999xx0x006"
      $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
      Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.storageProfile'.osDisk.vhd.uri -DiskEncryptionKeyUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -CreateOption "Attach" -Windows
      $vm.StorageProfile.OsDisk.OsType = $obj.'properties.storageProfile'.osDisk.osType
      foreach($dd in $obj.'properties.storageProfile'.dataDisks)
     {
     $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
     }
```

* **Niet-beheerde en versleutelde VM's zonder Azure AD (alleen BEK)** - Voor niet-beheerde, versleutelde VM's zonder Azure AD (alleen versleuteld met BEK), als **bronsleutelVault/secret niet beschikbaar is,** herstelt u de geheimen van sleutelkluis met behulp van de procedure in [Een niet-versleutelde virtuele machine herstellen vanaf een herstelpunt voor Azure Backup.](backup-azure-restore-key-secret.md) Voer vervolgens de volgende scripts uit om versleutelingsdetails in te stellen op de herstelde blob van het besturingssysteem (deze stap is niet vereist voor gegevensblob). De $dekurl kan worden opgehaald uit de gerestaureerde keyVault.

Het onderstaande script hoeft alleen te worden uitgevoerd wanneer de bronsleutelVault/secret niet beschikbaar is.

```powershell
      $dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
      $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
      $encSetting = "{""encryptionEnabled"":true,""encryptionSettings"":[{""diskEncryptionKey"":{""sourceVault"":{""id"":""$keyVaultId""},""secretUrl"":""$dekUrl""}}]}"
      $osBlobName = $obj.'properties.StorageProfile'.osDisk.name + ".vhd"
      $osBlob = Get-AzStorageBlob -Container $containerName -Blob $osBlobName
      $osBlob.ICloudBlob.Metadata["DiskEncryptionSettings"] = $encSetting
      $osBlob.ICloudBlob.SetMetadata()
```

Nadat de **geheimen beschikbaar zijn** en de encryptie details zijn ook ingesteld op de OS Blob, bevestig de schijven met behulp van het script hieronder gegeven.

Als de bronsleutelVault/geheimen al beschikbaar zijn, hoeft het bovenstaande script niet te worden uitgevoerd.

```powershell
      Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
      $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
      foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
      {
      $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
      }
```

* **Niet-beheerde en versleutelde VM's zonder Azure AD (BEK en KEK)** - Voor niet-beheerde, versleutelde VM's zonder Azure AD (versleuteld met BEK & KEK), als **bronsleutel/sleutel/geheim niet beschikbaar is,** herstelt u de sleutel en geheimen naar sleutelkluis met behulp van de procedure in [Een niet-versleutelde virtuele machine herstellen vanaf een Azure Backup-herstelpunt.](backup-azure-restore-key-secret.md) Voer vervolgens de volgende scripts uit om versleutelingsdetails in te stellen op de herstelde blob van het besturingssysteem (deze stap is niet vereist voor gegevensblob). De $dekurl en $kekurl kunnen worden opgehaald uit de gerestaureerde keyVault.

Het onderstaande script hoeft alleen te worden uitgevoerd wanneer de bronsleutelVault/sleutel/secret niet beschikbaar is.

```powershell
      $dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
      $kekUrl = "https://ContosoKeyVault.vault.azure.net/keys/ContosoKey007/x9xxx00000x0000x9b9949999xx0x006"
      $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
      $encSetting = "{""encryptionEnabled"":true,""encryptionSettings"":[{""diskEncryptionKey"":{""sourceVault"":{""id"":""$keyVaultId""},""secretUrl"":""$dekUrl""},""keyEncryptionKey"":{""sourceVault"":{""id"":""$keyVaultId""},""keyUrl"":""$kekUrl""}}]}"
      $osBlobName = $obj.'properties.StorageProfile'.osDisk.name + ".vhd"
      $osBlob = Get-AzStorageBlob -Container $containerName -Blob $osBlobName
      $osBlob.ICloudBlob.Metadata["DiskEncryptionSettings"] = $encSetting
      $osBlob.ICloudBlob.SetMetadata()
```

Nadat de **sleutel/geheimen beschikbaar zijn** en de versleutelingsdetails zijn ingesteld op de OS Blob, bevestigt u de schijven met behulp van het onderstaande script.

Als de bronsleutelVault/key/secrets beschikbaar is, hoeft het bovenstaande script niet te worden uitgevoerd.

```powershell
      Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
      $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
      foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
      {
      $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
      }
```

* **Beheerde en niet-versleutelde VM's** - Voor beheerde niet-versleutelde VM's koppelt u de beheerde schijven. Zie [Een gegevensschijf koppelen aan een Windows-vm met PowerShell](../virtual-machines/windows/attach-disk-ps.md)voor uitgebreide informatie.

* **Beheerde en versleutelde VM's met Azure AD (alleen BEK)** - Voor beheerde versleutelde VM's met Azure AD (alleen versleuteld met BEK) koppelt u de herstelde beheerde schijven. Zie [Een gegevensschijf koppelen aan een Windows-vm met PowerShell](../virtual-machines/windows/attach-disk-ps.md)voor uitgebreide informatie.

* **Beheerde en versleutelde VM's met Azure AD (BEK en KEK)** - Voor beheerde versleutelde VM's met Azure AD (versleuteld met BEK en KEK) koppelt u de herstelde beheerde schijven. Zie [Een gegevensschijf koppelen aan een Windows-vm met PowerShell](../virtual-machines/windows/attach-disk-ps.md)voor uitgebreide informatie.

* **Beheerde en versleutelde VM's zonder Azure AD (alleen BEK)** -Voor beheerde, versleutelde VM's zonder Azure AD (alleen versleuteld met BEK), als **bronsleutelVault/secret niet beschikbaar is,** herstel de geheimen van sleutelkluis met behulp van de procedure in [Een niet-versleutelde virtuele machine herstellen vanaf een Azure Backup-herstelpunt.](backup-azure-restore-key-secret.md) Voer vervolgens de volgende scripts uit om versleutelingsgegevens in te stellen op de herstelde osschijf (deze stap is niet vereist voor gegevensschijf). De $dekurl kan worden opgehaald uit de gerestaureerde keyVault.

Het onderstaande script hoeft alleen te worden uitgevoerd wanneer de bronsleutelVault/secret niet beschikbaar is.  

```powershell
$dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
$keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
$diskupdateconfig = New-AzDiskUpdateConfig -EncryptionSettingsEnabled $true
$encryptionSettingsElement = New-Object Microsoft.Azure.Management.Compute.Models.EncryptionSettingsElement
$encryptionSettingsElement.DiskEncryptionKey = New-Object Microsoft.Azure.Management.Compute.Models.KeyVaultAndSecretReference
$encryptionSettingsElement.DiskEncryptionKey.SourceVault = New-Object Microsoft.Azure.Management.Compute.Models.SourceVault
$encryptionSettingsElement.DiskEncryptionKey.SourceVault.Id = $keyVaultId
$encryptionSettingsElement.DiskEncryptionKey.SecretUrl = $dekUrl
$diskupdateconfig.EncryptionSettingsCollection.EncryptionSettings = New-Object System.Collections.Generic.List[Microsoft.Azure.Management.Compute.Models.EncryptionSettingsElement]
$diskupdateconfig.EncryptionSettingsCollection.EncryptionSettings.Add($encryptionSettingsElement)
$diskupdateconfig.EncryptionSettingsCollection.EncryptionSettingsVersion = "1.1"
Update-AzDisk -ResourceGroupName "testvault" -DiskName $obj.'properties.StorageProfile'.osDisk.name -DiskUpdate $diskupdateconfig
```

Nadat de geheimen beschikbaar zijn en de versleutelingsdetails zijn ingesteld op de OS-schijf, raadpleegt u [Een gegevensschijf koppelen aan een Windows-VM met PowerShell](../virtual-machines/windows/attach-disk-ps.md).

* **Beheerde en versleutelde VM's zonder Azure AD (BEK en KEK)** - Voor beheerde, versleutelde VM's zonder Azure AD (versleuteld met BEK-& KEK), als **bronkeyVault/key/secret niet beschikbaar is,** herstelt u de sleutel en geheimen naar sleutelkluis met behulp van de procedure in [Een niet-versleutelde virtuele machine herstellen vanuit een Azure Backup-herstelpunt.](backup-azure-restore-key-secret.md) Voer vervolgens de volgende scripts uit om versleutelingsgegevens in te stellen op de herstelde osschijf (deze stap is niet vereist voor gegevensschijven). De $dekurl en $kekurl kunnen worden opgehaald uit de gerestaureerde keyVault.

Het onderstaande script hoeft alleen te worden uitgevoerd wanneer de bronsleutelVault/sleutel/secret niet beschikbaar is.

```powershell
$dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
$kekUrl = "https://ContosoKeyVault.vault.azure.net/keys/ContosoKey007/x9xxx00000x0000x9b9949999xx0x006"
$keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
$diskupdateconfig = New-AzDiskUpdateConfig -EncryptionSettingsEnabled $true
$encryptionSettingsElement = New-Object Microsoft.Azure.Management.Compute.Models.EncryptionSettingsElement
$encryptionSettingsElement.DiskEncryptionKey = New-Object Microsoft.Azure.Management.Compute.Models.KeyVaultAndSecretReference
$encryptionSettingsElement.DiskEncryptionKey.SourceVault = New-Object Microsoft.Azure.Management.Compute.Models.SourceVault
$encryptionSettingsElement.DiskEncryptionKey.SourceVault.Id = $keyVaultId
$encryptionSettingsElement.DiskEncryptionKey.SecretUrl = $dekUrl
$encryptionSettingsElement.KeyEncryptionKey = New-Object Microsoft.Azure.Management.Compute.Models.KeyVaultAndKeyReference
$encryptionSettingsElement.KeyEncryptionKey.SourceVault = New-Object Microsoft.Azure.Management.Compute.Models.SourceVault
$encryptionSettingsElement.KeyEncryptionKey.SourceVault.Id = $keyVaultId
$encryptionSettingsElement.KeyEncryptionKey.KeyUrl = $kekUrl
$diskupdateconfig.EncryptionSettingsCollection.EncryptionSettings = New-Object System.Collections.Generic.List[Microsoft.Azure.Management.Compute.Models.EncryptionSettingsElement]
$diskupdateconfig.EncryptionSettingsCollection.EncryptionSettings.Add($encryptionSettingsElement)
$diskupdateconfig.EncryptionSettingsCollection.EncryptionSettingsVersion = "1.1"
Update-AzDisk -ResourceGroupName "testvault" -DiskName $obj.'properties.StorageProfile'.osDisk.name -DiskUpdate $diskupdateconfig
```

Nadat de sleutel/geheimen beschikbaar zijn en de versleutelingsdetails zijn ingesteld op de OS-schijf, raadpleegt u [Een gegevensschijf koppelen aan een Windows-VM met PowerShell](../virtual-machines/windows/attach-disk-ps.md).

5. Stel de netwerkinstellingen in.

    ```powershell
    $nicName="p1234"
    $pip = New-AzPublicIpAddress -Name $nicName -ResourceGroupName "test" -Location "WestUS" -AllocationMethod Dynamic
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName "test" -Location "WestUS" -Name "testvNET" -AddressPrefix 10.0.0.0/16
    $virtualNetwork | Set-AzVirtualNetwork
    $vnet = Get-AzVirtualNetwork -Name "testvNET" -ResourceGroupName "test"
    $subnetindex=0
    $nic = New-AzNetworkInterface -Name $nicName -ResourceGroupName "test" -Location "WestUS" -SubnetId $vnet.Subnets[$subnetindex].Id -PublicIpAddressId $pip.Id
    $vm=Add-AzVMNetworkInterface -VM $vm -Id $nic.Id
    ```

6. Maak de virtuele machine.

    ```powershell  
    New-AzVM -ResourceGroupName "test" -Location "WestUS" -VM $vm
    ```

7. Push ADE extensie.
   Als de ADE-extensies niet worden gepusht, worden de gegevensschijven gemarkeerd als onversleuteld, dus het is verplicht dat de onderstaande stappen worden uitgevoerd:

   * **Voor VM met Azure AD** - Gebruik de volgende opdracht om handmatig versleuteling voor de gegevensschijven in te schakelen  

     **ALLEEN BEK**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm.Name -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -VolumeType Data
      ```

     **BEK en KEK**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm.Name -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId  -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -VolumeType Data
      ```

   * **Voor VM zonder Azure AD** - Gebruik de volgende opdracht om handmatig versleuteling voor de gegevensschijven in te schakelen.

     Als tijdens de opdrachtuitvoering aadclientid wordt aangevraagd, moet u uw Azure PowerShell bijwerken.

     **ALLEEN BEK**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm.Name -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -SkipVmBackup -VolumeType "All"
      ```

      **BEK en KEK**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm.Name -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -SkipVmBackup -VolumeType "All"
      ```

> [!NOTE]
> Zorg ervoor dat u de JASON-bestanden die zijn gemaakt als onderdeel van het versleutelde vm-herstelschijfproces handmatig verwijdert.

## <a name="restore-files-from-an-azure-vm-backup"></a>Bestanden herstellen vanuit een Azure VM-back-up

Naast het herstellen van schijven u ook afzonderlijke bestanden herstellen vanuit een Azure VM-back-up. De functionaliteit voor herstelbestanden biedt toegang tot alle bestanden in een herstelpunt. Beheer de bestanden via File Explorer zoals u dat voor normale bestanden zou doen.

De basisstappen om een bestand te herstellen vanaf een Azure VM-back-up zijn:

* Selecteer de VM
* Een herstelpunt kiezen
* De schijven van het herstelpunt monteren
* De vereiste bestanden kopiëren
* De schijf loskoppelen

### <a name="select-the-vm"></a>Selecteer de VM

Als u het PowerShell-object wilt ophalen dat het juiste back-upitem identificeert, start u vanaf de container in de kluis en werkt u zich een weg naar beneden in de objecthiërarchie. Als u de container wilt selecteren die de VM vertegenwoordigt, gebruikt u de cmdlet [Get-AzRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupcontainer) en pipet die naar de cmdlet [Get-AzRecoveryServicesBackupItem.](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem)

```powershell
$namedContainer = Get-AzRecoveryServicesBackupContainer  -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM" -VaultId $targetVault.ID
$backupitem = Get-AzRecoveryServicesBackupItem -Container $namedContainer  -WorkloadType "AzureVM" -VaultId $targetVault.ID
```

### <a name="choose-a-recovery-point"></a>Een herstelpunt kiezen

Gebruik de cmdlet [Get-AzRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint) om alle herstelpunten voor het back-upitem weer te geven. Kies vervolgens het herstelpunt om te herstellen. Als u niet zeker weet welk herstelpunt u wilt gebruiken, is het een goede gewoonte om het meest recente RecoveryPointType = AppConsistent-punt in de lijst te kiezen.

In het volgende script is de variabele, **$rp,** een array met herstelpunten voor het geselecteerde back-upitem van de afgelopen zeven dagen. De array wordt gesorteerd in omgekeerde volgorde van tijd met het laatste herstelpunt op index 0. Gebruik standaard PowerShell-arrayindexering om het herstelpunt te kiezen. In het voorbeeld selecteert $rp[0] het laatste herstelpunt.

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime() -VaultId $targetVault.ID
$rp[0]
```

De uitvoer lijkt op die in het volgende voorbeeld:

```output
RecoveryPointAdditionalInfo :
SourceVMStorageType         : NormalStorage
Name                        : 15260861925810
ItemName                    : VM;iaasvmcontainer;RGName1;V2VM
RecoveryPointId             : /subscriptions/XX/resourceGroups/ RGName1/providers/Microsoft.RecoveryServices/vaults/testvault/backupFabrics/Azure/protectionContainers/IaasVMContainer;iaasvmcontainer;RGName1;V2VM/protectedItems/VM;iaasvmcontainer; RGName1;V2VM/recoveryPoints/15260861925810
RecoveryPointType           : AppConsistent
RecoveryPointTime           : 4/23/2016 5:02:04 PM
WorkloadType                : AzureVM
ContainerName               : IaasVMContainer;iaasvmcontainer; RGName1;V2VM
ContainerType               : AzureVM
BackupManagementType        : AzureVM
```

### <a name="mount-the-disks-of-recovery-point"></a>De schijven van het herstelpunt monteren

Gebruik de cmdlet [Get-AzRecoveryServicesBackupRPMountScript](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprpmountscript) om het script te laten monteren om alle schijven van het herstelpunt te monteren.

> [!NOTE]
> De schijven worden gemonteerd als iSCSI-schijven aan de machine waar het script wordt uitgevoerd. Montage gebeurt onmiddellijk, en je hoeft geen kosten.
>
>

```powershell
Get-AzRecoveryServicesBackupRPMountScript -RecoveryPoint $rp[0] -VaultId $targetVault.ID
```

De uitvoer lijkt op die in het volgende voorbeeld:

```output
OsType  Password        Filename
------  --------        --------
Windows e3632984e51f496 V2VM_wus2_8287309959960546283_451516692429_cbd6061f7fc543c489f1974d33659fed07a6e0c2e08740.exe
```

Voer het script uit op de machine waar u de bestanden wilt herstellen. Als u het script wilt uitvoeren, moet u het opgegeven wachtwoord invoeren. Nadat de schijven zijn gekoppeld, gebruikt u Windows Verkenner om door de nieuwe volumes en bestanden te bladeren. Zie het artikel Back-up herstellen [van bestanden uit Azure-back-up](backup-azure-restore-files-from-vm.md)voor virtuele machines voor meer informatie.

### <a name="unmount-the-disks"></a>De schijven loskoppelen

Nadat de vereiste bestanden zijn gekopieerd, gebruikt u [Disable-AzRecoveryServicesBackupRPMountScript](https://docs.microsoft.com/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackuprpmountscript) om de schijven te demonteren. Zorg ervoor dat u de schijven loskoppelt, zodat de toegang tot de bestanden van het herstelpunt wordt verwijderd.

```powershell
Disable-AzRecoveryServicesBackupRPMountScript -RecoveryPoint $rp[0] -VaultId $targetVault.ID
```

## <a name="next-steps"></a>Volgende stappen

Zie het PowerShell-artikel [Implementeren en Back-up beheren voor Windows Server](backup-client-automation.md)als u PowerShell liever gebruikt om met uw Azure-bronnen te communiceren. Als u DPM-back-ups beheert, raadpleegt u het artikel [Implementeren en Back-up beheren voor DPM.](backup-dpm-automation.md)
