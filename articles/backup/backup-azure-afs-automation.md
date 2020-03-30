---
title: Back-ups maken van Azure-bestanden met PowerShell
description: In dit artikel leest u hoe u een back-up maakt van Azure-bestanden met de Azure Backup-service en PowerShell.
ms.topic: conceptual
ms.date: 08/20/2019
ms.openlocfilehash: f85451e0da6458de34aea936836b46781f4c4a21
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273538"
---
# <a name="back-up-azure-files-with-powershell"></a>Back-ups maken van Azure-bestanden met PowerShell

In dit artikel wordt beschreven hoe u Azure PowerShell gebruiken om een back-up te maken van een Azure Files-bestandsshare met behulp van een Azure [Backup](backup-overview.md) Recovery Services-kluis.

In dit artikel wordt uitgelegd hoe u:

> [!div class="checklist"]
>
> * Stel PowerShell in en registreer de Azure Recovery Services Provider.
> * Maak een Recovery Services-kluis.
> * Back-up configureren voor een Azure-bestandsshare.
> * Voer een back-uptaak uit.

## <a name="before-you-start"></a>Voordat u begint

* [Meer informatie](backup-azure-recovery-services-vault-overview.md) over kluizen van Recovery Services.
* Lees meer over de preview-mogelijkheden voor [het maken van back-ups van Azure-bestandsshares](backup-afs.md).
* Controleer de PowerShell-objecthiërarchie voor herstelservices.

## <a name="recovery-services-object-hierarchy"></a>Objecthiërarchie van Herstelservices

De objecthiërarchie wordt samengevat in het volgende diagram.

![Objecthiërarchie van Herstelservices](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Bekijk de verwijzing naar [de cmdlet-verwijzing](/powershell/module/az.recoveryservices) **naar AZ.RecoveryServices** in de Azure-bibliotheek.

## <a name="set-up-and-install"></a>Instellen en installeren

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

PowerShell als volgt instellen:

1. [Download de nieuwste versie van Az PowerShell.](/powershell/azure/install-az-ps) De vereiste minimumversie is 1.0.0.

> [!WARNING]
> De minimale versie van PS vereist voor preview was 'Az 1.0.0'. Vanwege de komende wijzigingen voor GA is de vereiste minimale PS-versie 'Az.RecoveryServices 2.6.0'. Het is erg belangrijk om alle bestaande PS-versies te upgraden naar deze versie. Anders worden de bestaande scripts na GA afgebroken. De minimale versie installeren met de volgende PS-opdrachten

```powershell
Install-module -Name Az.RecoveryServices -RequiredVersion 2.6.0
```

2. Zoek de Azure Backup PowerShell-cmdlets met deze opdracht:

    ```powershell
    Get-Command *azrecoveryservices*
    ```

3. Bekijk de aliassen en cmdlets voor Azure Backup, Azure Site Recovery en de vault Recovery Services. Hier is een voorbeeld van wat je zou kunnen zien. Het is geen volledige lijst van cmdlets.

    ![Lijst van cmdlets van herstelservices](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

4. Meld u aan bij uw **Azure-account met Connect-AzAccount**.
5. Op de webpagina die wordt weergegeven, wordt u gevraagd uw accountgegevens in te voeren.

    * U uw accountreferenties als parameter opnemen in de cmdlet **Connect-AzAccount** met **-Referentie.**
    * Als u een CSP-partner bent die namens een tenant werkt, geeft u de klant op als tenant met behulp van de primaire domeinnaam tenant- of tenant. Een voorbeeld is **Connect-AzAccount -Tenant** fabrikam.com.

6. Koppel het abonnement dat u wilt gebruiken aan het account, omdat een account meerdere abonnementen kan hebben.

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

7. Als u Azure Backup voor de eerste keer gebruikt, gebruikt u de cmdlet **Register-AzResourceProvider** om de Azure Recovery Services-provider met uw abonnement te registreren.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

8. Controleer of de providers zich hebben geregistreerd:

    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

9. Controleer in de opdrachtuitvoer of **RegistrationState** wordt gewijzigd in **Geregistreerd**. Als dit niet het zo is, voert u de cmdlet **Register-AzResourceProvider** opnieuw uit.

## <a name="create-a-recovery-services-vault"></a>Een Recovery Services-kluis maken

De kluis Recovery Services is een resourcemanager-bron, dus u moet deze in een resourcegroep plaatsen. U een bestaande resourcegroep gebruiken of u een resourcegroep maken met de cmdlet **Nieuw-AzResourceGroep.** Wanneer u een resourcegroep maakt, geeft u de naam en locatie voor de resourcegroep op.

Volg deze stappen om een kluis van Recovery Services te maken.

1. Een kluis wordt in een resourcegroep geplaatst. Als u geen bestaande resourcegroep hebt, maakt u een nieuwe groep met de [Nieuwe-AzResourceGroep](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-1.4.0). In dit voorbeeld maken we een nieuwe resourcegroep in de regio West-VS.

   ```powershell
   New-AzResourceGroup -Name "test-rg" -Location "West US"
   ```

2. Gebruik de cmdlet [New-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/New-AzRecoveryServicesVault?view=azps-1.4.0) om de kluis te maken. Geef dezelfde locatie op voor de kluis als voor de resourcegroep.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```

3. Geef het type redundantie op dat moet worden gebruikt voor de kluisopslag.

   * U [lokaal redundante opslag](../storage/common/storage-redundancy-lrs.md) of [georedundante opslag](../storage/common/storage-redundancy-grs.md)gebruiken.
   * In het volgende voorbeeld wordt de optie **-BackupStorageRedundancy** ingesteld voor de cmd[Set-AzRecoveryServicesBackupProperties](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) voor **testvault** set op **GeoRedundant**.

     ```powershell
     $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
     Set-AzRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
     ```

### <a name="view-the-vaults-in-a-subscription"></a>Bekijk de kluizen in een abonnement

Als u alle kluizen in het abonnement wilt bekijken, gebruikt u [Get-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault?view=azps-1.4.0).

```powershell
Get-AzRecoveryServicesVault
```

De uitvoer is vergelijkbaar met de volgende. Houd er rekening mee dat de bijbehorende resourcegroep en locatie zijn opgegeven.

```powershell
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

### <a name="set-the-vault-context"></a>De context van de kluis instellen

Sla het kluisobject in een variabele op en stel de kluiscontext in.

* Veel Azure Backup-cmdlets vereisen het kluisobject Recovery Services als invoer, dus het is handig om het kluisobject in een variabele op te slaan.
* De context van de kluis is het type gegevens dat in de kluis wordt beveiligd. Stel deze in met [Set-AzRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext?view=azps-1.4.0). Nadat de context is ingesteld, is deze van toepassing op alle volgende cmdlets.

In het volgende voorbeeld wordt de context van de kluis ingesteld voor **testvault**.

```powershell
Get-AzRecoveryServicesVault -Name "testvault" | Set-AzRecoveryServicesVaultContext
```

### <a name="fetch-the-vault-id"></a>De kluis-id ophalen

We zijn van plan de instelling voor de kluiscontext te deprecating in overeenstemming met azure PowerShell-richtlijnen. In plaats daarvan u de kluis-id opslaan of ophalen en deze doorgeven aan relevante opdrachten. Dus als u de vaultcontext niet hebt ingesteld of de opdracht wilt opgeven die moet worden uitgevoerd voor een bepaalde kluis, geeft u de kluis-ID als '-vaultID' als volgt door:

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewAFSPolicy" -WorkloadType "AzureFiles" -RetentionPolicy $retPol -SchedulePolicy $schPol -VaultID $vaultID
```

## <a name="configure-a-backup-policy"></a>Een back-upbeleid configureren

Een back-upbeleid geeft het schema voor back-ups op en hoe lang back-upherstelpunten moeten worden bewaard:

* Een back-upbeleid is gekoppeld aan ten minste één bewaarbeleid. Een bewaarbeleid bepaalt hoe lang een herstelpunt wordt bewaard voordat het wordt verwijderd.
* Bekijk de standaardbehoud van back-upbeleid met [Get-AzRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject?view=azps-1.4.0).
* Het standaard beleidsschema voor back-ups weergeven met [Get-AzRecoveryServicesBackupSchedulePolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject?view=azps-1.4.0).
* U gebruikt de cmdlet [Nieuw-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0) om een nieuw back-upbeleid te maken. U voert de objecten plannings- en bewaarbeleid in.

Standaard wordt een begintijd gedefinieerd in het beleidsobject Schema. Gebruik het volgende voorbeeld om de begintijd te wijzigen in de gewenste begintijd. De gewenste starttijd moet ook in UTC zijn. In het onderstaande voorbeeld wordt ervan uitgegaan dat de gewenste begintijd 01:00 AM UTC is voor dagelijkse back-ups.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureFiles"
$UtcTime = Get-Date -Date "2019-03-20 01:30:00Z"
$UtcTime = $UtcTime.ToUniversalTime()
$schpol.ScheduleRunTimes[0] = $UtcTime
```

> [!IMPORTANT]
> U moet de starttijd in 30 minuten veelvouden alleen. In het bovenstaande voorbeeld kan het alleen "01:00:00" of "02:30:00" zijn. De begintijd kan niet "01:15:00" zijn

In het volgende voorbeeld worden het planningsbeleid en het bewaarbeleid opgeslagen in variabelen. Vervolgens gebruikt het deze variabelen als parameters voor een nieuw beleid (**NewAFSPolicy**). **NewAFSPolicy** neemt een dagelijkse back-up en behoudt deze gedurende 30 dagen.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureFiles"
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureFiles"
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewAFSPolicy" -WorkloadType "AzureFiles" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

De uitvoer is vergelijkbaar met de volgende.

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewAFSPolicy           AzureFiles            AzureStorage              10/24/2019 1:30:00 AM
```

## <a name="enable-backup"></a>Back-up inschakelen

Nadat u het back-upbeleid hebt gedefinieerd, u de beveiliging voor het Azure-bestandsaandeel inschakelen met behulp van het beleid.

### <a name="retrieve-a-backup-policy"></a>Een back-upbeleid ophalen

U haalt het relevante beleidsobject op met [Get-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0). Gebruik deze cmdlet om een specifiek beleid te krijgen of om het beleid te bekijken dat is gekoppeld aan een werkbelastingtype.

#### <a name="retrieve-a-policy-for-a-workload-type"></a>Een beleid ophalen voor een werkbelastingtype

In het volgende voorbeeld wordt het beleid voor het werkbelastingtype **AzureFiles opgehaald.**

```powershell
Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureFiles"
```

De uitvoer is vergelijkbaar met de volgende.

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
dailyafs             AzureFiles         AzureStorage         1/10/2018 12:30:00 AM
```

> [!NOTE]
> De tijdzone van het veld **BackupTime** in PowerShell is Universal Coordinated Time (UTC). Wanneer de back-uptijd wordt weergegeven in de Azure-portal, wordt de tijd aangepast aan uw lokale tijdzone.

### <a name="retrieve-a-specific-policy"></a>Een specifiek beleid ophalen

In het volgende beleid wordt het back-upbeleid met de naam **dailyafs**opgehaald.

```powershell
$afsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "dailyafs"
```

### <a name="enable-backup-and-apply-policy"></a>Back-up maken en beleid toepassen

Schakel beveiliging in met [Enable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection?view=azps-1.4.0). Nadat het beleid is gekoppeld aan de kluis, worden back-ups geactiveerd in overeenstemming met het beleidsschema.

In het volgende voorbeeld wordt beveiliging mogelijk gemaakt voor de Azure file share **testAzureFileShare** in storage account **testStorageAcct**, met het beleid **dailyafs**.

```powershell
Enable-AzRecoveryServicesBackupProtection -StorageAccountName "testStorageAcct" -Name "testAzureFS" -Policy $afsPol
```

De opdracht wacht tot de beveiligingstaak configureren is voltooid en geeft een vergelijkbare uitvoer, zoals weergegeven.

```cmd
WorkloadName       Operation            Status                 StartTime                                                                                                         EndTime                   JobID
------------             ---------            ------               ---------                                  -------                   -----
testAzureFS       ConfigureBackup      Completed            11/12/2018 2:15:26 PM     11/12/2018 2:16:11 PM     ec7d4f1d-40bd-46a4-9edb-3193c41f6bf6
```

## <a name="important-notice---backup-item-identification-for-afs-backups"></a>Belangrijke mededeling - Identificatie van back-upitems voor AFS-back-ups

In dit gedeelte wordt een belangrijke wijziging in AFS-back-up beschreven ter voorbereiding op GA.

Terwijl de back-up voor AFS wordt inschakelt, levert de gebruiker de klantvriendelijke naam voor bestandsshare als de naam van de entiteit en wordt een back-upitem gemaakt. De 'naam' van het back-upitem is een unieke id die is gemaakt door de Azure Backup-service. Meestal is de id gaat gebruiksvriendelijke naam. Maar om het belangrijke scenario van soft-delete te verwerken, waarbij een bestandsshare kan worden verwijderd en een andere bestandsshare met dezelfde naam kan worden gemaakt, is de unieke identiteit van Azure-bestandsshare nu een id in plaats van klantvriendelijke naam. Om de unieke identiteit/naam van elk item ```Get-AzRecoveryServicesBackupItem``` te weten, voert u de opdracht uit met de relevante filters voor backupManagementType en WorkloadType om alle relevante items te krijgen en vervolgens het naamveld in het geretourneerde PS-object/-antwoord te observeren. Het wordt altijd aanbevolen om items te vermelden en vervolgens hun unieke naam op te halen uit het veld 'naam' als reactie. Gebruik deze waarde om de items te filteren met de parameter 'Naam'. Gebruik anders de parameter FriendlyName om het artikel op te halen met de klantvriendelijke naam/id.

> [!WARNING]
> Zorg ervoor dat de PS-versie is geüpgraded naar de minimale versie voor 'Az.RecoveryServices 2.6.0' voor AFS-back-ups. Met deze versie is het filter 'friendlyName' beschikbaar voor ```Get-AzRecoveryServicesBackupItem``` opdracht. Geef de naam Azure File Share door aan de parameter FriendlyName. Als u de naam Azure File Share doorgeeft aan de parameter 'Name', geeft deze versie een waarschuwing om deze vriendelijke naam door te geven aan de parameter vriendelijke naam. Het niet installeren van deze minimale versie kan leiden tot een storing van bestaande scripts. Installeer de minimale versie van PS met de volgende opdracht.

```powershell
Install-module -Name Az.RecoveryServices -RequiredVersion 2.6.0
```

## <a name="trigger-an-on-demand-backup"></a>Een on-demand back-up activeren

Gebruik [Backup-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem?view=azps-1.4.0) om een on-demand back-up uit te voeren voor een beveiligde Azure-bestandsshare.

1. Haal het opslagaccount op uit de container in de kluis met uw back-upgegevens met [Get-AzRecoveryServicesBackupContainer.](/powershell/module/az.recoveryservices/get-Azrecoveryservicesbackupcontainer)
2. Als u een back-uptaak wilt starten, krijgt u informatie over het Azure-bestandsaandeel met [Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem).
3. Voer een on-demand back-up uit met[Backup-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-Azrecoveryservicesbackupitem).

Voer de on-demand back-up als volgt uit:

```powershell
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType "AzureFiles" -FriendlyName "testAzureFS"
$job =  Backup-AzRecoveryServicesBackupItem -Item $afsBkpItem
```

De opdracht retourneert een taak met een id die moet worden bijgehouden, zoals in het volgende voorbeeld wordt weergegeven.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS       Backup               Completed            11/12/2018 2:42:07 PM     11/12/2018 2:42:11 PM     8bdfe3ab-9bf7-4be6-83d6-37ff1ca13ab6
```

Azure file share snapshots worden gebruikt terwijl de back-ups worden gemaakt, dus meestal is de taak voltooid tegen de tijd dat de opdracht deze uitvoer retourneert.

### <a name="using-on-demand-backups-to-extend-retention"></a>On-demand back-ups gebruiken om retentie uit te breiden

On-demand back-ups kunnen worden gebruikt om uw snapshots 10 jaar te bewaren. Planners kunnen worden gebruikt om on-demand PowerShell-scripts uit te voeren met gekozen retentie en zo elke week, maand of jaar regelmatig momentopnamen te maken. Raadpleeg tijdens het maken van regelmatige momentopnamen de [beperkingen van on-demand back-ups met Azure-back-ups.](https://docs.microsoft.com/azure/backup/backup-azure-files-faq#how-many-on-demand-backups-can-i-take-per-file-share)

Als u voorbeeldscripts zoekt, u verwijzen naar het<https://github.com/Azure-Samples/Use-PowerShell-for-long-term-retention-of-Azure-Files-Backup>voorbeeldscript op GitHub ( ) met Azure Automation-runbook waarmee u periodiek back-ups plannen en deze zelfs tot 10 jaar behouden.

> [!WARNING]
> Zorg ervoor dat de PS-versie is geüpgraded naar de minimale versie voor 'Az.RecoveryServices 2.6.0' voor AFS-back-ups in uw automatiseringsrunbooks. U moet de oude 'AzureRM'-module vervangen door 'Az'-module. Met deze versie is het filter 'friendlyName' beschikbaar voor ```Get-AzRecoveryServicesBackupItem``` opdracht. Geef de naam azure-bestandsshare door aan de parameter FriendlyName. Als u de naam azure file share doorgeeft aan de parameter 'Name', geeft deze versie een waarschuwing om deze vriendelijke naam door te geven aan de parameter vriendelijke naam.

## <a name="next-steps"></a>Volgende stappen

[Meer informatie over](backup-afs.md) het maken van back-ups van Azure-bestanden in de Azure-portal.
