---
title: SQL DB in Azure VM-back-up & herstellen via PowerShell
description: Maak een back-up en herstel SQL-databases in Azure VM's met Azure Backup en PowerShell.
ms.topic: conceptual
ms.date: 03/15/2019
ms.assetid: 57854626-91f9-4677-b6a2-5d12b6a866e1
ms.openlocfilehash: 9608b02869b1d41d901ec77a42cfaa6d882040e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131813"
---
# <a name="back-up-and-restore-sql-databases-in-azure-vms-with-powershell"></a>Back-ups maken en SQL-databases herstellen in Azure VM's met PowerShell

In dit artikel wordt beschreven hoe u Azure PowerShell gebruiken om een SQL DB te back-upen en te herstellen binnen een Azure VM met azure [backup](backup-overview.md) recovery services.

In dit artikel wordt uitgelegd hoe u:

> [!div class="checklist"]
>
> * Stel PowerShell in en registreer de Azure Recovery Services Provider.
> * Maak een Recovery Services-kluis.
> * Back-up configureren voor SQL DB binnen een Azure VM.
> * Voer een back-uptaak uit.
> * Een back-up sql DB herstellen.
> * Controleer back-ups en hersteltaken.

## <a name="before-you-start"></a>Voordat u begint

* [Meer informatie](backup-azure-recovery-services-vault-overview.md) over kluizen van Recovery Services.
* Lees meer over de functiemogelijkheden voor [het maken van back-ups van SQL DB's binnen Azure VM's.](backup-azure-sql-database.md#before-you-start)
* Controleer de PowerShell-objecthiërarchie voor herstelservices.

### <a name="recovery-services-object-hierarchy"></a>Objecthiërarchie van Herstelservices

De objecthiërarchie wordt samengevat in het volgende diagram.

![Objecthiërarchie van Herstelservices](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Bekijk de verwijzing naar [de cmdlet-verwijzing](/powershell/module/az.recoveryservices) **naar AZ.RecoveryServices** in de Azure-bibliotheek.

### <a name="set-up-and-install"></a>Instellen en installeren

PowerShell als volgt instellen:

1. [Download de nieuwste versie van Az PowerShell.](/powershell/azure/install-az-ps) De vereiste minimumversie is 1.5.0.

2. Zoek de Azure Backup PowerShell-cmdlets met deze opdracht:

    ```powershell
    Get-Command *azrecoveryservices*
    ```

3. Bekijk de aliassen en cmdlets voor Azure Backup en de vault Recovery Services. Hier is een voorbeeld van wat je zou kunnen zien. Het is geen volledige lijst van cmdlets.

    ![Lijst van cmdlets van herstelservices](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

4. Meld u aan bij uw **Azure-account met Connect-AzAccount**.
5. Op de webpagina die wordt weergegeven, wordt u gevraagd uw accountgegevens in te voeren.

    * U uw accountreferenties als parameter opnemen in de cmdlet **Connect-AzAccount** met **-Referentie.**
    * Als u een CSP-partner bent die voor een tenant werkt, geeft u de klant op als tenant met behulp van de primaire domeinnaam tenant- of tenant. Een voorbeeld is **Connect-AzAccount -Tenant** fabrikam.com.

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

Volg deze stappen om een kluis van Recovery Services te maken.

De kluis Recovery Services is een resourcemanager-bron, dus u moet deze in een resourcegroep plaatsen. U een bestaande resourcegroep gebruiken of u een resourcegroep maken met de cmdlet **Nieuw-AzResourceGroep.** Wanneer u een resourcegroep maakt, geeft u de naam en locatie voor de resourcegroep op.

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
    * In het volgende voorbeeld wordt de optie **-BackupStorageRedundancy** ingesteld voor de[cmd Set-AzRecoveryServicesBackupProperty](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) voor **testvault** ingesteld op **GeoRedundant**.

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
    Set-AzRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

### <a name="view-the-vaults-in-a-subscription"></a>Bekijk de kluizen in een abonnement

Als u alle kluizen in het abonnement wilt bekijken, gebruikt u [Get-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault?view=azps-1.4.0).

```powershell
Get-AzRecoveryServicesVault
```

De uitvoer is vergelijkbaar met de volgende. De bijbehorende resourcegroep en locatie worden verstrekt.

```output
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

We zijn van plan de instelling voor de kluiscontext te deprecating in overeenstemming met azure PowerShell-richtlijnen. In plaats daarvan u de kluis-id opslaan of ophalen en deze als volgt doorgeven aan relevante opdrachten:

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
```

## <a name="configure-a-backup-policy"></a>Een back-upbeleid configureren

Een back-upbeleid geeft het schema voor back-ups op en hoe lang back-upherstelpunten moeten worden bewaard:

* Een back-upbeleid is gekoppeld aan ten minste één bewaarbeleid. Een bewaarbeleid bepaalt hoe lang een herstelpunt wordt bewaard voordat het wordt verwijderd.
* Bekijk de standaardbehoud van back-upbeleid met [Get-AzRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject?view=azps-1.4.0).
* Het standaard beleidsschema voor back-ups weergeven met [Get-AzRecoveryServicesBackupSchedulePolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject?view=azps-1.4.0).
* U gebruikt de cmdlet [Nieuw-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0) om een nieuw back-upbeleid te maken. U voert de objecten plannings- en bewaarbeleid in.

Standaard wordt een begintijd gedefinieerd in het beleidsobject Schema. Gebruik het volgende voorbeeld om de begintijd te wijzigen in de gewenste begintijd. De gewenste starttijd moet ook in UTC zijn. In het onderstaande voorbeeld wordt ervan uitgegaan dat de gewenste begintijd 01:00 AM UTC is voor dagelijkse back-ups.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "MSSQL"
$UtcTime = Get-Date -Date "2019-03-20 01:30:00Z"
$UtcTime = $UtcTime.ToUniversalTime()
$schpol.ScheduleRunTimes[0] = $UtcTime
```

> [!IMPORTANT]
> U moet de starttijd in 30 minuten veelvouden alleen. In het bovenstaande voorbeeld kan het alleen "01:00:00" of "02:30:00" zijn. De begintijd kan niet "01:15:00" zijn

In het volgende voorbeeld worden het planningsbeleid en het bewaarbeleid opgeslagen in variabelen. Vervolgens gebruikt het deze variabelen als parameters voor een nieuw beleid **(NewSQLPolicy).** **NewSQLPolicy** neemt een dagelijkse "Volledige" back-up, behoudt deze gedurende 180 dagen en neemt elke 2 uur een logboekback-up

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "MSSQL"
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "MSSQL"
$NewSQLPolicy = New-AzRecoveryServicesBackupProtectionPolicy -Name "NewSQLPolicy" -WorkloadType "MSSQL" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

De uitvoer is vergelijkbaar met de volgende.

```output
Name                 WorkloadType       BackupManagementType BackupTime                Frequency                                IsDifferentialBackup IsLogBackupEnabled
                                                                                                                                Enabled
----                 ------------       -------------------- ----------                ---------                                -------------------- ------------------
NewSQLPolicy         MSSQL              AzureWorkload        3/15/2019 01:30:00 AM      Daily                                    False                True
```

## <a name="enable-backup"></a>Back-up inschakelen

### <a name="registering-the-sql-vm"></a>De SQL VM registreren

Voor Azure VM-back-ups en Azure File-shares kan de back-upservice verbinding maken met deze Azure Resource Manager-bronnen en de relevante gegevens ophalen. Aangezien SQL een toepassing is binnen een Azure VM, heeft back-upservice toestemming nodig om toegang te krijgen tot de toepassing en de benodigde gegevens op te halen. Om dat te doen, moet u de Azure VM die de SQL-toepassing bevat *'registreren'* met een Vault voor herstelservices. Zodra u een SQL VM registreert met een kluis, u de SQL DB's alleen beschermen tegen die kluis. Gebruik [register-AzRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/az.recoveryservices/Register-AzRecoveryServicesBackupContainer?view=azps-1.5.0) PS-cmdlet om de VM te registreren.

```powershell
 $myVM = Get-AzVM -ResourceGroupName <VMRG Name> -Name <VMName>
Register-AzRecoveryServicesBackupContainer -ResourceId $myVM.ID -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID -Force
```

De opdracht retourneert een 'back-upcontainer' van deze resource en de status wordt 'geregistreerd'

> [!NOTE]
> Als de krachtparameter niet wordt gegeven, wordt de gebruiker gevraagd om te bevestigen met de tekst 'Wilt u de beveiliging voor deze container uitschakelen'. Negeer deze tekst en zeg "Y" om te bevestigen. Dit is een bekend probleem en we werken aan het verwijderen van de tekst en de vereiste voor de krachtparameter.

### <a name="fetching-sql-dbs"></a>SQL DB's ophalen

Zodra de registratie is voltooid, kan de back-upservice alle beschikbare SQL-componenten binnen de VM weergeven. Als u alle SQL-componenten wilt bekijken die nog moeten worden geback-upt naar deze kluis, gebruikt [u Get-AzRecoveryServicesBackupProtectableItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupProtectableItem?view=azps-1.5.0) PS-cmdlet

```powershell
Get-AzRecoveryServicesBackupProtectableItem -WorkloadType MSSQL -VaultId $targetVault.ID
```

De uitvoer toont alle onbeveiligde SQL-componenten in alle SQL VM's die zijn geregistreerd bij deze kluis met itemtype en ServerName. U verder filteren op een bepaalde SQL VM door de parameter '-Container' door te geven of de combinatie 'Naam' en 'ServerName' samen met de vlag ItemType gebruiken om tot een uniek SQL-item te komen.

```powershell
$SQLDB = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLDataBase -VaultId $targetVault.ID -Name "<Item Name>" -ServerName "<Server Name>"
```

### <a name="configuring-backup"></a>Back-up configureren

Nu we de vereiste SQL DB en het beleid hebben waarmee een back-up moet worden gemaakt, kunnen we de cmdlet [Enable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Enable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) gebruiken om back-ups voor deze SQL DB te configureren.

```output
Enable-AzRecoveryServicesBackupProtection -ProtectableItem $SQLDB -Policy $NewSQLPolicy
```

De opdracht wacht totdat de geconfigureerde back-up is voltooid en retourneert de volgende uitvoer.

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
master           ConfigureBackup      Completed            3/18/2019 6:00:21 PM      3/18/2019 6:01:35 PM      654e8aa2-4096-402b-b5a9-e5e71a496c4e
```

### <a name="fetching-new-sql-dbs"></a>Nieuwe SQL DB's ophalen

Zodra de machine is geregistreerd, haalt back-upservice de details van de beschikbare DB's op. Als de gebruiker later SQL DBs/SQL-exemplaren aan de geregistreerde machine toevoegt, moet men de back-upservice handmatig activeren om een nieuw 'onderzoek' uit te voeren om ALLE onbeschermde DB's (inclusief de nieuw toegevoegde) opnieuw te krijgen. Gebruik de [CMdlet Initialize-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Initialize-AzRecoveryServicesBackupProtectableItem?view=azps-1.5.0) ps op de SQL VM om een nieuw onderzoek uit te voeren. De opdracht wacht tot de bewerking is voltooid. Gebruik later de [cmdlet Get-AzRecoveryServicesBackupProtectableItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupProtectableItem?view=azps-1.5.0) PS om de lijst met nieuwste onbeschermde SQL-componenten te krijgen

```powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
Initialize-AzRecoveryServicesBackupProtectableItem -Container $SQLContainer -WorkloadType MSSQL -VaultId $targetvault.ID
Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLDataBase -VaultId $targetVault.ID
```

Zodra de relevante beschermbare items zijn opgehaald, schakelt u de back-ups in zoals geïnstrueerd in de [bovenstaande sectie](#configuring-backup).
Als men niet handmatig nieuwe DB's wil detecteren, kunnen ze kiezen voor autoprotectie zoals [hieronder](#enable-autoprotection)uitgelegd.

## <a name="enable-autoprotection"></a>Automatische beveiliging inschakelen

Een gebruiker kan een back-up zodanig configureren dat alle db's die in de toekomst zijn toegevoegd, automatisch worden beveiligd met een bepaald beleid. Als u automatische beveiliging wilt inschakelen, gebruikt u [de PS-cmdlet enable-AzRecoveryServicesBackupAutoProtection.](https://docs.microsoft.com/powershell/module/az.recoveryservices/Enable-AzRecoveryServicesBackupAutoProtection?view=azps-1.5.0)

Aangezien de instructie is om een back-up van alle toekomstige DB's, de operatie wordt gedaan op SQLInstance-niveau.

```powershell
$SQLInstance = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLInstance -VaultId $targetVault.ID -Name "<Protectable Item name>" -ServerName "<Server Name>"
Enable-AzRecoveryServicesBackupAutoProtection -InputItem $SQLInstance -BackupManagementType AzureWorkload -WorkloadType MSSQL -Policy $NewSQLPolicy -VaultId $targetvault.ID
```

Zodra de intentie voor autoprotectie is gegeven, vindt het onderzoek naar de machine om nieuw toegevoegde DB's op te halen elke 8 uur plaats als een geplande achtergrondtaak.

## <a name="restore-sql-dbs"></a>SQL DB's herstellen

Azure Backup kan SQL Server-databases die op Azure VM's worden uitgevoerd als volgt herstellen:

* Herstellen naar een specifieke datum of tijd (naar de tweede) met behulp van back-ups van het transactielogboek. Azure Backup bepaalt automatisch de juiste volledige differentiële back-up en de keten van logboekback-ups die moeten worden hersteld op basis van de geselecteerde tijd.
* Herstel een specifieke volledige of differentiële back-up om te herstellen naar een specifiek herstelpunt.

Controleer de [hier](restore-sql-database-azure-vm.md#prerequisites) genoemde vereisten voordat u SQL DB's herstelt.

Haal eerst de relevante back-up SQL DB op met de [Get-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem?view=azps-1.5.0) PS-cmdlet.

```powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
```

### <a name="fetch-the-relevant-restore-time"></a>Haal de relevante hersteltijd op

Zoals hierboven beschreven, kan de gebruiker de back-up SQL DB herstellen naar een volledige/differentiële kopie **OF** naar een log point-in-time.

#### <a name="fetch-distinct-recovery-points"></a>Afzonderlijke herstelpunten ophalen

Gebruik [Get-AzRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupRecoveryPoint?view=azps-1.5.0) om afzonderlijke (Volledige/differentiële) herstelpunten op te halen voor een back-up SQL DB.

```powershell
$startDate = (Get-Date).AddDays(-7).ToUniversalTime()
$endDate = (Get-Date).ToUniversalTime()
Get-AzRecoveryServicesBackupRecoveryPoint -Item $bkpItem -VaultId $targetVault.ID -StartDate $startdate -EndDate $endDate
```

De uitvoer is vergelijkbaar met het volgende voorbeeld

```output
RecoveryPointId    RecoveryPointType  RecoveryPointTime      ItemName                             BackupManagemen
                                                                                                  tType
---------------    -----------------  -----------------      --------                             ---------------
6660368097802      Full               3/18/2019 8:09:35 PM   MSSQLSERVER;model             AzureWorkload
```

Gebruik het filter 'RecoveryPointId' of een arrayfilter om het relevante herstelpunt op te halen.

```powershell
$FullRP = Get-AzRecoveryServicesBackupRecoveryPoint -Item $bkpItem -VaultId $targetVault.ID -RecoveryPointId "6660368097802"
```

#### <a name="fetch-point-in-time-recovery-point"></a>Punt-in-tijd herstelpunt ophalen

Als de gebruiker de DB naar een bepaald punt in de tijd wil herstellen, gebruikt u [Get-AzRecoveryServicesBackupRecoveryLogChain](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupRecoveryLogChain?view=azps-1.5.0) PS-cmdlet. De cmdlet retourneert een lijst met datums die begin- en eindtijden van een ononderbroken, doorlopende logboekketen voor dat SQL-back-upitem weergeven. De gewenste point-in-time moet binnen dit bereik liggen.

```powershell
Get-AzRecoveryServicesBackupRecoveryLogChain -Item $bkpItem -Item -VaultId $targetVault.ID
```

De uitvoer zal vergelijkbaar zijn met het volgende voorbeeld.

```output
ItemName                       StartTime                      EndTime
--------                       ---------                      -------
SQLDataBase;MSSQLSERVER;azu... 3/18/2019 8:09:35 PM           3/19/2019 12:08:32 PM
```

De bovenstaande uitvoer betekent dat de gebruiker kan herstellen naar elk point-in-time tussen de weergegeven begintijd en eindtijd. De tijden zijn in UTC. Bouw alle point-in-time in PS dat binnen het bovenstaande bereik valt.

> [!NOTE]
> Wanneer een inlogpoint-in-time is geselecteerd voor herstel, hoeft de gebruiker niet het beginpunt op te geven, d.w.z. volledige back-up van waaruit de DB is hersteld. Azure Backup service zorgt voor het volledige herstelplan dat wil zeggen, welke volledige back-up te kiezen, welke log back-ups toe te passen, enz.

### <a name="determine-recovery-configuration"></a>Herstelconfiguratie bepalen

In het geval van SQL DB-herstel worden de volgende herstelscenario's ondersteund.

* Het overschrijven van de back-up SQL DB met gegevens van een ander herstelpunt - OriginalWorkloadRestore
* De SQL DB herstellen als een nieuwe DB in dezelfde SQL-instantie - AlternateWorkloadRestore
* De SQL DB herstellen als een nieuwe DB in een andere SQL-instantie in een andere SQL VM - AlternateWorkloadRestore
* De SQL DB herstellen als .bak-bestanden -RestoreAsFiles

Nadat u het relevante herstelpunt (afzonderlijk of log point-in-time) hebt opgehaald, gebruikt u [Get-AzRecoveryServicesBackupBackupRecoveryRecoveryConfig](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupWorkloadRecoveryConfig?view=azps-1.5.0) PS-cmdlet om het herstelconfig-object op te halen volgens het gewenste herstelplan.

#### <a name="original-workload-restore"></a>Oorspronkelijke werkbelasting herstellen

Als u de back-up DB wilt overschrijven met gegevens van het herstelpunt, geeft u alleen de juiste vlag en het relevante herstelpunt op, zoals in het volgende voorbeeld(en).

##### <a name="original-restore-with-distinct-recovery-point"></a>Origineel herstel met duidelijk herstelpunt

```powershell
$OverwriteWithFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -RecoveryPoint $FullRP -OriginalWorkloadRestore -VaultId $targetVault.ID
```

##### <a name="original-restore-with-log-point-in-time"></a>Origineel herstel met inlogpoint-in-time

```powershell
$OverwriteWithLogConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -Item $bkpItem  -OriginalWorkloadRestore -VaultId $targetVault.ID
```

#### <a name="alternate-workload-restore"></a>Alternatieve werkbelasting herstellen

> [!IMPORTANT]
> Een back-up SQL DB kan worden hersteld als een nieuwe DB naar een andere SQLInstance alleen, in een Azure VM 'geregistreerd' om deze kluis.

Zoals hierboven beschreven, moet u, als het doel SQLInstance binnen een andere Azure-VM ligt, ervoor zorgen dat deze is [geregistreerd bij deze kluis](#registering-the-sql-vm) en dat de relevante SQLInstance wordt weergegeven als een beschermd item.

```powershell
$TargetInstance = Get-AzRecoveryServicesBackupProtectableItem -WorkloadType MSSQL -ItemType SQLInstance -Name "<SQLInstance Name>" -ServerName "<SQL VM name>" -VaultId $targetVault.ID
```

Ga dan gewoon voorbij het relevante herstelpunt, richt SQL-instantie met de juiste vlag zoals hieronder weergegeven.

##### <a name="alternate-restore-with-distinct-recovery-point"></a>Afwisselend herstel met afzonderlijk herstelpunt

```powershell
$AnotherInstanceWithFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -RecoveryPoint $FullRP -TargetItem $TargetInstance -AlternateWorkloadRestore -VaultId $targetVault.ID
```

##### <a name="alternate-restore-with-log-point-in-time"></a>Afwisselend herstellen met inlogpoint-in-time

```powershell
$AnotherInstanceWithLogConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -Item $bkpItem -AlternateWorkloadRestore -VaultId $targetVault.ID
```

##### <a name="restore-as-files"></a>Herstellen als bestanden

Als u de back-upgegevens wilt herstellen als .bak-bestanden in plaats van een database, kiest u de optie **Herstellen als bestanden.** De back-up SQL DB kan worden hersteld naar elke doel-VM die is geregistreerd bij deze kluis.

```powershell
$TargetContainer= Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName "VM name" -VaultId $vaultID
```

##### <a name="restore-as-files-with-distinct-recovery-point"></a>Herstellen als bestanden met een duidelijk herstelpunt

```powershell
$FileRestoreWithFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -RecoveryPoint $FullRP -TargetContainer $TargetContainer -RestoreAsFiles -FilePath "<>" -VaultId $targetVault.ID
```

##### <a name="restore-as-files-with-log-point-in-time-from-latest-full"></a>Herstellen als bestanden met log point-in-time vanaf de laatste volledige

```powershell
$FileRestoreWithLogConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -TargetContainer $TargetContainer -RestoreAsFiles -FilePath "<>" -VaultId $targetVault.ID
```

##### <a name="restore-as-files-with-log-point-in-time-from-a-specified-full"></a>Herstellen als bestanden met aanmeldingspoint-in-time vanuit een opgegeven volledige

Als u een specifieke volledige wilt geven die moet worden gebruikt voor herstel, gebruikt u de volgende opdracht:

```powershell
$FileRestoreWithLogAndSpecificFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -FromFull $FullRP -TargetContainer $TargetContainer -RestoreAsFiles -FilePath "<>" -VaultId $targetVault.ID
```

Het uiteindelijke herstelpuntconfiguratieobject verkregen van [Get-AzRecoveryServicesBackupBackRecoveryConfig](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupWorkloadRecoveryConfig?view=azps-1.5.0) PS cmdlet heeft alle relevante informatie voor herstel en is zoals hieronder weergegeven.

```output
TargetServer         : <SQL server name>
TargetInstance       : <Target Instance name>
RestoredDBName       : <Target Instance name>/azurebackup1_restored_3_19_2019_1850
OverwriteWLIfpresent : No
NoRecoveryMode       : Disabled
targetPhysicalPath   : {azurebackup1, azurebackup1_log}
ContainerId          : /Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/vmappcontainer;compute;computeRG;SQLVMName
SourceResourceId     : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/computeRG/VMAppContainer/SQLVMName
RestoreRequestType   : Alternate WL Restore
RecoveryPoint        : Microsoft.Azure.Commands.RecoveryServices.Backup.Cmdlets.Models.AzureWorkloadRecoveryPoint
PointInTime          : 1/1/0001 12:00:00 AM
```

U de velden Restored DB-naam, OverschrijfWLIfpresent, NoRecoveryMode en targetPhysicalPath bewerken. Meer informatie over de doelbestandspaden zoals hieronder weergegeven.

```powershell
$AnotherInstanceWithFullConfig.targetPhysicalPath
```

```output
MappingType SourceLogicalName SourcePath                  TargetPath
----------- ----------------- ----------                  ----------
Data        azurebackup1      F:\Data\azurebackup1.mdf    F:\Data\azurebackup1_1553001753.mdf
Log         azurebackup1_log  F:\Log\azurebackup1_log.ldf F:\Log\azurebackup1_log_1553001753.ldf
```

Stel de relevante PS-eigenschappen in als tekenreekswaarden zoals hieronder weergegeven.

```powershell
$AnotherInstanceWithFullConfig.OverwriteWLIfpresent = "Yes"
$AnotherInstanceWithFullConfig | fl
```

```output
TargetServer         : <SQL server name>
TargetInstance       : <Target Instance name>
RestoredDBName       : <Target Instance name>/azurebackup1_restored_3_19_2019_1850
OverwriteWLIfpresent : Yes
NoRecoveryMode       : Disabled
targetPhysicalPath   : {azurebackup1, azurebackup1_log}
ContainerId          : /Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/vmappcontainer;compute;computeRG;SQLVMName
SourceResourceId     : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/computeRG/VMAppContainer/SQLVMName
RestoreRequestType   : Alternate WL Restore
RecoveryPoint        : Microsoft.Azure.Commands.RecoveryServices.Backup.Cmdlets.Models.AzureWorkloadRecoveryPoint
PointInTime          : 1/1/0001 12:00:00 AM
```

> [!IMPORTANT]
> Zorg ervoor dat het uiteindelijke herstelobject over alle noodzakelijke en juiste waarden beschikt, aangezien de herstelbewerking gebaseerd is op het config-object.

### <a name="restore-with-relevant-configuration"></a>Herstellen met relevante configuratie

Zodra het relevante herstel-Config-object is verkregen en geverifieerd, gebruikt u de [CMDlet Restore-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Restore-AzRecoveryServicesBackupItem?view=azps-1.5.0) PS om het herstelproces te starten.

```powershell
Restore-AzRecoveryServicesBackupItem -WLRecoveryConfig $AnotherInstanceWithLogConfig -VaultId $targetVault.ID
```

Als u de herstelbewerking retourneert, wordt een taak geretourneerd die moet worden bijgehouden.

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
MSSQLSERVER/m... Restore              InProgress           3/17/2019 10:02:45 AM                                3274xg2b-e4fg-5952-89b4-8cb566gc1748
```

## <a name="manage-sql-backups"></a>SQL-back-ups beheren

### <a name="on-demand-backup"></a>Back-up op aanvraag

Zodra back-up is ingeschakeld voor een DB, kan de gebruiker ook een on-demand back-up voor de DB activeren met behulp van [Backup-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Backup-AzRecoveryServicesBackupItem?view=azps-1.5.0) PS cmdlet. In het volgende voorbeeld wordt een volledige back-up geactiveerd op een SQL DB met compressie ingeschakeld en moet de volledige back-up 60 dagen worden bewaard.

```powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
$endDate = (Get-Date).AddDays(60).ToUniversalTime()
Backup-AzRecoveryServicesBackupItem -Item $bkpItem -BackupType Full -EnableCompression -VaultId $targetVault.ID -ExpiryDateTimeUTC $endDate
```

De on-demand back-upopdracht retourneert een taak die moet worden bijgehouden.

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
MSSQLSERVER/m... Backup               InProgress           3/18/2019 8:41:27 PM                                2516bb1a-d3ef-4841-97a3-9ba455fb0637
```

Als de uitvoer verloren gaat of als u de relevante taak-id wilt ophalen, [krijgt u de lijst met taken van de](#track-azure-backup-jobs) Azure Backup-service en volgt u deze en de details ervan.

### <a name="change-policy-for-backup-items"></a>Beleid wijzigen voor back-upitems

De gebruiker kan het bestaande beleid wijzigen of het beleid van het back-upitem wijzigen van Beleid1 naar Beleid2. Als u van beleid wilt wisselen voor een back-upitem, haalt u het desbetreffende beleid en maakt u een back-up van item en gebruikt u de opdracht [Enable-AzRecoveryServices](https://docs.microsoft.com/powershell/module/az.recoveryservices/Enable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) met back-upitem als parameter.

```powershell
$TargetPol1 = Get-AzRecoveryServicesBackupProtectionPolicy -Name <PolicyName>
$anotherBkpItem = Get-AzRecoveryServicesBackupItem -WorkloadType MSSQL -BackupManagementType AzureWorkload -Name "<BackupItemName>"
Enable-AzRecoveryServicesBackupProtection -Item $anotherBkpItem -Policy $TargetPol1
```

De opdracht wacht totdat de geconfigureerde back-up is voltooid en retourneert de volgende uitvoer.

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
master           ConfigureBackup      Completed            3/18/2019 8:00:21 PM      3/18/2019 8:02:16 PM      654e8aa2-4096-402b-b5a9-e5e71a496c4e
```

### <a name="re-register-sql-vms"></a>SQL VM's opnieuw registreren

> [!WARNING]
> Zorg ervoor dat u dit [document](backup-sql-server-azure-troubleshoot.md#re-registration-failures) leest om de symptomen en oorzaken van de fouten te begrijpen voordat u opnieuw wordt geregistreerd

Als u de opnieuw registratie van de SQL VM wilt activeren, haalt u de relevante back-upcontainer op en geeft deze door aan de registercmdlet.

```powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
Register-AzRecoveryServicesBackupContainer -Container $SQLContainer -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID
```

### <a name="stop-protection"></a>Beveiliging stoppen

#### <a name="retain-data"></a>Gegevens behouden

Als de gebruiker de bescherming wil stoppen, kan hij de [CMDlet Disable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Disable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) GEBRUIKEN. Dit stopt de geplande back-ups, maar de gegevens die tot nu toe zijn gemaakt, worden voor altijd bewaard.

```powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID
```

#### <a name="delete-backup-data"></a>Back-upgegevens verwijderen

Om de opgeslagen back-upgegevens in de kluis volledig te verwijderen, voegt u de vlag 'RemoveRecoveryPoints' /switch toe aan de [beveiligingsopdracht 'uitschakelen'.](#retain-data)

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID -RemoveRecoveryPoints
```

#### <a name="disable-auto-protection"></a>Automatische beveiliging uitschakelen

Als autoprotectie is geconfigureerd op een SQLInstance, kan de gebruiker deze uitschakelen met de [PS-cmdlet Disable-AzRecoveryServicesBackupAutoProtection.](https://docs.microsoft.com/powershell/module/az.recoveryservices/Disable-AzRecoveryServicesBackupAutoProtection?view=azps-1.5.0)

```powershell
$SQLInstance = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLInstance -VaultId $targetVault.ID -Name "<Protectable Item name>" -ServerName "<Server Name>"
Disable-AzRecoveryServicesBackupAutoProtection -InputItem $SQLInstance -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetvault.ID
```

#### <a name="unregister-sql-vm"></a>SQL VM uitschrijven

Als alle DB's van een SQL-server [niet langer zijn beveiligd en er geen back-upgegevens bestaan,](#delete-backup-data)kan de gebruiker de SQL VM uit deze kluis uitschrijven. Alleen dan kan de gebruiker DBs beschermen tot een andere kluis. Gebruik [PS-cmdlet Unregister-AzRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/az.recoveryservices/Unregister-AzRecoveryServicesBackupContainer?view=azps-1.5.0) om de SQL VM uit te schrijven.

```powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
 Unregister-AzRecoveryServicesBackupContainer -Container $SQLContainer -VaultId $targetvault.ID
```

### <a name="track-azure-backup-jobs"></a>Azure Backup-taken bijhouden

Het is belangrijk op te merken dat Azure Backup alleen door gebruikers geactiveerde taken in SQL-back-up bijhoudt. Geplande back-ups (inclusief logboekback-ups) zijn niet zichtbaar in portal/powershell. Als geplande taken echter mislukken, wordt er een [back-upwaarschuwing](backup-azure-monitoring-built-in-monitor.md#backup-alerts-in-recovery-services-vault) gegenereerd en weergegeven in de portal. [Gebruik Azure Monitor](backup-azure-monitoring-use-azuremonitor.md) om alle geplande taken en andere relevante informatie bij te houden.

Gebruikers kunnen on-demand/user triggered operations bijhouden met de JobID die wordt geretourneerd in de [uitvoer](#on-demand-backup) van asynchrone taken zoals back-up. Gebruik [Get-AzRecoveryServicesBackupJobDetail](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupJobDetail) PS cmdlet om de taak en de details ervan bij te houden.

```powershell
 Get-AzRecoveryServicesBackupJobDetails -JobId 2516bb1a-d3ef-4841-97a3-9ba455fb0637 -VaultId $targetVault.ID
```

Als u de lijst met on-demand taken en hun statussen wilt ophalen van azure backup-service, gebruikt u [Get-AzRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupJob?view=azps-1.5.0) PS-cmdlet. In het volgende voorbeeld worden alle SQL-taken in uitvoering geretourneerd.

```powershell
Get-AzRecoveryServicesBackupJob -Status InProgress -BackupManagementType AzureWorkload
```

Als u een lopende taak wilt annuleren, gebruikt u de [CMDlet Stop-AzRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/Stop-AzRecoveryServicesBackupJob?view=azps-1.5.0) PS.

## <a name="managing-sql-always-on-availability-groups"></a>SQL Always On Availability-groepen beheren

Controleer bij SQL Always On Availability Groups [alle knooppunten](#registering-the-sql-vm) van de groep Beschikbaarheid (AG). Zodra de registratie voor alle knooppunten is uitgevoerd, wordt een SQL-beschikbaarheidsgroepobject logischerwijs gemaakt onder beschermbare items. De databases onder de SQL AG worden vermeld als 'SQLDatabase'. De knooppunten worden weergegeven als zelfstandige exemplaren en de standaard SQL-databases eronder worden ook vermeld als SQL-databases.

Laten we er bijvoorbeeld van uitgaan dat een SQL AG twee knooppunten heeft: 'sql-server-0' en 'sql-server-1' en 1 SQL AG DB. Zodra beide knooppunten zijn geregistreerd, als de gebruiker [de beschermbare items vermeldt,](#fetching-sql-dbs)worden de volgende onderdelen weergegeven

* Een SQL AG-object - beschermbaar itemtype als SQLAvailabilityGroup
* Een SQL AG DB - beschermbaar itemtype als SQLDatabase
* sql-server-0 - beschermbaar itemtype als SQLInstance
* sql-server-1 - beschermbaar itemtype als SQLInstance
* Standaard SQL DBs (master, model, msdb) onder sql-server-0 - beschermbaar itemtype als SQLDatabase
* Standaard SQL DBs (master, model, msdb) onder sql-server-1 - beschermbaar itemtype als SQLDatabase

sql-server-0, sql-server-1, wordt ook vermeld als "AzureVMAppContainer" wanneer [back-upcontainers worden vermeld.](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupContainer?view=azps-1.5.0)

Haal gewoon de relevante SQL-database [om back-up in](#configuring-backup) te schakelen en de [on-demand back-up](#on-demand-backup) en [herstel PS-cmdlets](#restore-sql-dbs) zijn identiek.
