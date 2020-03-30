---
title: PowerShell gebruiken om een back-up te maken van Windows Server naar Azure
description: Lees in dit artikel hoe u PowerShell gebruiken voor het instellen van Azure Backup op Windows Server of een Windows-client en het beheren van back-ups en herstel.
ms.topic: conceptual
ms.date: 12/2/2019
ms.openlocfilehash: efe0b93fe1e37990422ffbd2256e38c12401dca5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78673205"
---
# <a name="deploy-and-manage-backup-to-azure-for-windows-serverwindows-client-using-powershell"></a>Met behulp van PowerShell back-ups implementeren en beheren in Azure voor een Windows-server/Windows-client

In dit artikel ziet u hoe u PowerShell gebruiken voor het instellen van Azure Backup op Windows Server of een Windows-client en het beheren van back-ups en herstel.

## <a name="install-azure-powershell"></a>Azure PowerShell installeren

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Installeer de [nieuwste PowerShell-release](/powershell/azure/install-az-ps)om aan de slag te gaan.

## <a name="create-a-recovery-services-vault"></a>Een Recovery Services-kluis maken

De volgende stappen leiden u door het maken van een Vault Recovery Services. Een vault van Recovery Services is anders dan een back-upkluis.

1. Als u Azure Backup voor de eerste keer gebruikt, moet u de cmdlet **Register-AzResourceProvider** gebruiken om de Azure Recovery Service-provider met uw abonnement te registreren.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

2. De kluis Recovery Services is een ARM-bron, dus u moet deze binnen een resourcegroep plaatsen. U een bestaande resourcegroep gebruiken of een nieuwe groep maken. Geef bij het maken van een nieuwe resourcegroep de naam en locatie voor de resourcegroep op.  

    ```powershell
    New-AzResourceGroup –Name "test-rg" –Location "WestUS"
    ```

3. Gebruik de **cmdlet New-AzRecoveryServicesVault** om de nieuwe kluis te maken. Zorg ervoor dat u dezelfde locatie voor de kluis opgeeft als voor de resourcegroep.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName " test-rg" -Location "WestUS"
    ```

4. Geef het type opslagredundantie op dat moet worden gebruikt; u [Lokaal redundante opslag (LRS)](../storage/common/storage-redundancy-lrs.md) of [Geo Redundant Storage (GRS)](../storage/common/storage-redundancy-grs.md)gebruiken. In het volgende voorbeeld ziet u de optie -BackupStorageRedundancy voor testVault is ingesteld op GeoRedundant.

   > [!TIP]
   > Voor veel Azure Backup-cmdlets is het object Recovery Services-kluis als invoer vereist. Daarom is het handiger het object Backup Recovery Services-kluis in een variabele op te slaan.
   >
   >

    ```powershell
    $Vault1 = Get-AzRecoveryServicesVault –Name "testVault"
    Set-AzRecoveryServicesBackupProperties -Vault $Vault1 -BackupStorageRedundancy GeoRedundant
    ```

## <a name="view-the-vaults-in-a-subscription"></a>Bekijk de kluizen in een abonnement

Gebruik **Get-AzRecoveryServicesVault** om de lijst met alle kluizen in het huidige abonnement weer te geven. U deze opdracht gebruiken om te controleren of er een nieuwe kluis is gemaakt of om te zien welke kluizen beschikbaar zijn in het abonnement.

Voer de opdracht, **Get-AzRecoveryServicesVault**en alle kluizen in het abonnement uit.

```powershell
Get-AzRecoveryServicesVault
```

```Output
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="installing-the-azure-backup-agent"></a>De Azure Backup-agent installeren

Voordat u de Azure Backup-agent installeert, moet u het installatieprogramma laten downloaden en presenteren op de Windows Server. U de nieuwste versie van het installatieprogramma downloaden van het [Microsoft Download center](https://aka.ms/azurebackup_agent) of via de dashboardpagina van de Vault Recovery Services. Sla het installatieprogramma op een gemakkelijk toegankelijke\*locatie op, zoals *C:\Downloads .

U powershell ook gebruiken om de downloader te krijgen:

 ```powershell
 $MarsAURL = 'https://aka.ms/Azurebackup_Agent'
 $WC = New-Object System.Net.WebClient
 $WC.DownloadFile($MarsAURL,'C:\downloads\MARSAgentInstaller.EXE')
 C:\Downloads\MARSAgentInstaller.EXE /q
 ```

Als u de agent wilt installeren, voert u de volgende opdracht uit in een verhoogde PowerShell-console:

```powershell
MARSAgentInstaller.exe /q
```

Hiermee wordt de agent geïnstalleerd met alle standaardopties. De installatie duurt een paar minuten op de achtergrond. Als u de optie */nu* niet opgeeft, wordt het **venster Windows Update** aan het einde van de installatie geopend om te controleren op updates. Eenmaal geïnstalleerd, zal de agent worden weergegeven in de lijst met geïnstalleerde programma's.

Als u de lijst met geïnstalleerde programma's wilt bekijken, gaat u naar > **Programma's en** > **-functies**van het **Configuratiescherm**.

![Agent geïnstalleerd](./media/backup-client-automation/installed-agent-listing.png)

### <a name="installation-options"></a>Installatieopties

Als u alle opties wilt bekijken die beschikbaar zijn via de opdrachtregel, gebruikt u de volgende opdracht:

```powershell
MARSAgentInstaller.exe /?
```

De beschikbare opties zijn:

| Optie | Details | Standaard |
| --- | --- | --- |
| /q |Stille installatie |- |
| /p:"locatie" |Ga naar de installatiemap voor de Azure Backup-agent. |C:\Program Files\Microsoft Azure Recovery Services Agent |
| /s:"locatie" |Pad naar de cachemap voor de Azure Backup-agent. |C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch |
| /m |Opt-in voor Microsoft Update |- |
| /nu |Controleer niet of er updates zijn uitgevoerd nadat de installatie is voltooid |- |
| /d |Verwijdert Microsoft Azure Recovery Services Agent |- |
| /ph |Proxyhostadres |- |
| /po |Proxyhostpoortnummer |- |
| /pu |UserName van proxyhost |- |
| /pw |Proxywachtwoord |- |

## <a name="registering-windows-server-or-windows-client-machine-to-a-recovery-services-vault"></a>Windows Server of Windows-clientmachine registreren in een Vault voor Recovery Services

Nadat u de kluis Van Herstelservices hebt gemaakt, downloadt u de nieuwste agent en de vault-referenties en slaat u deze op een handige locatie op, zoals C:\Downloads.

```powershell
$CredsPath = "C:\downloads"
$CredsFilename = Get-AzRecoveryServicesVaultSettingsFile -Backup -Vault $Vault1 -Path $CredsPath
```

### <a name="registering-using-the-ps-az-module"></a>Registreren met de PS Az module

> [!NOTE]
> Een bug met generatie van kluis certificaat is opgelost in Az 3.5.0 release. Gebruik de az 3.5.0-releaseversie of meer om een vault-certificaat te downloaden.

In de nieuwste Az-module van PowerShell, als gevolg van onderliggende platformbeperkingen, vereist het downloaden van de vault credentials een zelfondertekend certificaat. In het volgende voorbeeld ziet u hoe u een zelfondertekend certificaat opgeeft en de vault credentials downloadt.

```powershell
$dt = $(Get-Date).ToString("M-d-yyyy")
$cert = New-SelfSignedCertificate -CertStoreLocation Cert:\CurrentUser\My -FriendlyName 'test-vaultcredentials' -subject "Windows Azure Tools" -KeyExportPolicy Exportable -NotAfter $(Get-Date).AddHours(48) -NotBefore $(Get-Date).AddHours(-24) -KeyProtection None -KeyUsage None -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2") -Provider "Microsoft Enhanced Cryptographic Provider v1.0"
$certficate = [convert]::ToBase64String($cert.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pfx))
$CredsFilename = Get-AzRecoveryServicesVaultSettingsFile -Backup -Vault $Vault -Path $CredsPath -Certificate $certficate
```

Voer op de Windows Server- of Windows-clientmachine de [cmdlet Start-OBRegistration](https://docs.microsoft.com/powershell/module/msonlinebackup/start-obregistration?view=winserver2012-ps) uit om de machine bij de kluis te registreren.
Dit, en andere cmdlets gebruikt voor back-up, zijn van de MSONLINE module, die de Mars AgentInstaller toegevoegd als onderdeel van het installatieproces.

Het installatieprogramma van de agent werkt de variabele $Env:PSModulePath niet bij. Dit betekent dat module automatisch laden mislukt. Als u dit wilt oplossen, u het volgende doen:

```powershell
$Env:PSModulePath += ';C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules'
```

U de module ook handmatig als volgt in uw script laden:

```powershell
Import-Module -Name 'C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules\MSOnlineBackup'
```

Zodra u de cmdlets Online Backup hebt geladen, registreert u de kluisreferenties:

```powershell
Start-OBRegistration -VaultCredentials $CredsFilename.FilePath -Confirm:$false
```

```Output
CertThumbprint      : 7a2ef2caa2e74b6ed1222a5e89288ddad438df2
SubscriptionID      : ef4ab577-c2c0-43e4-af80-af49f485f3d1
ServiceResourceName : testvault
Region              : WestUS
Machine registration succeeded.
```

> [!IMPORTANT]
> Gebruik geen relatieve paden om het bestand met vaultcredentials op te geven. U moet een absoluut pad als input voor de cmdlet opgeven.
>
>

## <a name="networking-settings"></a>Netwerkinstellingen

Wanneer de verbinding van de Windows-machine met het internet via een proxyserver is, kunnen de proxy-instellingen ook aan de agent worden verstrekt. In dit voorbeeld is er geen proxyserver, dus we wissen expliciet proxy-gerelateerde informatie.

Bandbreedte gebruik kan ook worden `work hour bandwidth` gecontroleerd `non-work hour bandwidth` met de opties van en voor een bepaalde set van dagen van de week.

Het instellen van de proxy- en bandbreedtegegevens gebeurt met de cmdlet [Set-OBMachineSetting:](https://docs.microsoft.com/powershell/module/msonlinebackup/set-obmachinesetting?view=winserver2012-ps)

```powershell
Set-OBMachineSetting -NoProxy
```

```Output
Server properties updated successfully.
```

```powershell
Set-OBMachineSetting -NoThrottle
```

```Output
Server properties updated successfully.
```

## <a name="encryption-settings"></a>Versleutelingsinstellingen

De back-upgegevens die naar Azure Backup worden verzonden, worden versleuteld om de vertrouwelijkheid van de gegevens te beschermen. De encryptie wachtwoordzin is het "wachtwoord" om de gegevens te decoderen op het moment van herstellen.

U moet een beveiligingspincode genereren door **Genereren**te selecteren onder de**beveiligingspincode** **Voor Instellingen** > **eigenschappen** > in de **kluissectie Van Herstelservices** van de Azure-portal. Gebruik dit vervolgens `generatedPIN` als de opdracht:

```powershell
$PassPhrase = ConvertTo-SecureString -String "Complex!123_STRING" -AsPlainText -Force
Set-OBMachineSetting -EncryptionPassPhrase $PassPhrase -SecurityPin "<generatedPIN>"
```

```Output
Server properties updated successfully
```

> [!IMPORTANT]
> Houd de informatie over de wachtwoordzin veilig en beveiligd zodra deze is ingesteld. U gegevens uit Azure niet herstellen zonder deze wachtwoordzin.
>
>

## <a name="back-up-files-and-folders"></a>Back-ups maken van bestanden en mappen

Alle back-ups van Windows-servers en clients naar Azure Backup worden geregeld door een beleid. Het beleid bestaat uit drie delen:

1. Een **back-upschema** dat aangeeft wanneer back-ups moeten worden gemaakt en gesynchroniseerd met de service.
2. Een **bewaarschema** dat aangeeft hoe lang de herstelpunten in Azure moeten worden bewaard.
3. Een **specificatie voor bestandsopname/uitsluiting** die bepaalt waar een back-up van moet worden gemaakt.

In dit document, omdat we back-up automatiseren, gaan we ervan uit dat er niets is geconfigureerd. We beginnen met het maken van een nieuw back-upbeleid met de cmdlet [Nieuw-OBBeleid.](https://docs.microsoft.com/powershell/module/msonlinebackup/new-obpolicy?view=winserver2012-ps)

```powershell
$NewPolicy = New-OBPolicy
```

Op dit moment is het beleid leeg en zijn andere cmdlets nodig om te bepalen welke items worden opgenomen of uitgesloten, wanneer back-ups worden uitgevoerd en waar de back-ups worden opgeslagen.

### <a name="configuring-the-backup-schedule"></a>Het back-upschema configureren

De eerste van de drie onderdelen van een beleid is het back-upschema, dat wordt gemaakt met de cmdlet [Nieuw-OBSchedule.](https://docs.microsoft.com/powershell/module/msonlinebackup/new-obschedule?view=winserver2012-ps) Het back-upschema bepaalt wanneer back-ups moeten worden gemaakt. Wanneer u een planning maakt, moet u twee invoerparameters opgeven:

* **Dagen van de week** die de back-up moet uitvoeren. U de back-uptaak uitvoeren op slechts één dag, of elke dag van de week, of een combinatie daartussenin.
* **Tijden van de dag** waarop de back-up moet worden uitgevoerd. U maximaal drie verschillende tijden van de dag definiëren waarop de back-up wordt geactiveerd.

U bijvoorbeeld elke zaterdag en zondag om 16.00 uur een back-upbeleid configureren dat om 16.00 uur wordt uitgevoerd.

```powershell
$Schedule = New-OBSchedule -DaysOfWeek Saturday, Sunday -TimesOfDay 16:00
```

Het back-upschema moet worden gekoppeld aan een beleid en dit kan worden bereikt met behulp van de cmdlet [Set-OBSchedule.](https://docs.microsoft.com/powershell/module/msonlinebackup/set-obschedule?view=winserver2012-ps)

```powershell
Set-OBSchedule -Policy $NewPolicy -Schedule $Schedule
```

```Output
BackupSchedule : 4:00 PM Saturday, Sunday, Every 1 week(s) DsList : PolicyName : RetentionPolicy : State : New PolicyState : Valid
```

### <a name="configuring-a-retention-policy"></a>Een bewaarbeleid configureren

Het bewaarbeleid bepaalt hoe lang herstelpunten die zijn gemaakt met back-uptaken behouden blijven. Wanneer u een nieuw bewaarbeleid maakt met de cmdlet [Nieuw-OBRetentionPolicy,](https://docs.microsoft.com/powershell/module/msonlinebackup/new-obretentionpolicy?view=winserver2012-ps) u het aantal dagen opgeven dat de back-upherstelpunten moeten worden behouden met Azure Backup. In het onderstaande voorbeeld wordt een bewaarbeleid van zeven dagen ingesteld.

```powershell
$RetentionPolicy = New-OBRetentionPolicy -RetentionDays 7
```

Het bewaarbeleid moet worden gekoppeld aan het hoofdbeleid met behulp van het cmdlet [Set-OBRetentionPolicy:](https://docs.microsoft.com/powershell/module/msonlinebackup/set-obretentionpolicy?view=winserver2012-ps)

```powershell
Set-OBRetentionPolicy -Policy $NewPolicy -RetentionPolicy $RetentionPolicy
```

```Output
BackupSchedule  : 4:00 PM
                  Saturday, Sunday,
                  Every 1 week(s)
DsList          :
PolicyName      :
RetentionPolicy : Retention Days : 7

                  WeeklyLTRSchedule :
                  Weekly schedule is not set

                  MonthlyLTRSchedule :
                  Monthly schedule is not set

                  YearlyLTRSchedule :
                  Yearly schedule is not set

State           : New
PolicyState     : Valid
```

### <a name="including-and-excluding-files-to-be-backed-up"></a>Bestanden die moeten worden geback-upt en uitgezonderd

Een `OBFileSpec` object definieert de bestanden die moeten worden opgenomen en uitgesloten in een back-up. Dit is een set regels die de beveiligde bestanden en mappen op een machine uitwerken. U zoveel regels voor bestandsopname of uitsluiting hebben als vereist, en deze koppelen aan een beleid. Wanneer u een nieuw OBFileSpec-object maakt, u het volgende doen:

* De bestanden en mappen opgeven die moeten worden opgenomen
* De bestanden en mappen opgeven die moeten worden uitgesloten
* Geef een recursieve back-up op van gegevens in een map (of) of alleen de bestanden op het hoogste niveau in de opgegeven map een back-up moeten maken.

Dit laatste wordt bereikt door gebruik te maken van de -NonRecursive vlag in de opdracht Nieuw-OBFileSpec.

In het onderstaande voorbeeld maken we een back-up van deel C: en D: en sluiten de besturingssysteembinaire bestanden in de Windows-map en eventuele tijdelijke mappen uit. Hiervoor maken we twee bestandsspecificaties met de [cmdlet Nieuw-OBFileSpec](https://docs.microsoft.com/powershell/module/msonlinebackup/new-obfilespec?view=winserver2012-ps) - een voor opname en één voor uitsluiting. Zodra de bestandsspecificaties zijn gemaakt, worden ze gekoppeld aan het beleid met behulp van de [cmdlet Add-OBFileSpec.](https://docs.microsoft.com/powershell/module/msonlinebackup/add-obfilespec?view=winserver2012-ps)

```powershell
$Inclusions = New-OBFileSpec -FileSpec @("C:\", "D:\")
$Exclusions = New-OBFileSpec -FileSpec @("C:\windows", "C:\temp") -Exclude
Add-OBFileSpec -Policy $NewPolicy -FileSpec $Inclusions
```

```Output
BackupSchedule  : 4:00 PM
                  Saturday, Sunday,
                  Every 1 week(s)
DsList          : {DataSource
                  DatasourceId:0
                  Name:C:\
                  FileSpec:FileSpec
                  FileSpec:C:\
                  IsExclude:False
                  IsRecursive:True

                  , DataSource
                  DatasourceId:0
                  Name:D:\
                  FileSpec:FileSpec
                  FileSpec:D:\
                  IsExclude:False
                  IsRecursive:True

                  }
PolicyName      :
RetentionPolicy : Retention Days : 7

                  WeeklyLTRSchedule :
                  Weekly schedule is not set

                  MonthlyLTRSchedule :
                  Monthly schedule is not set

                  YearlyLTRSchedule :
                  Yearly schedule is not set

State           : New
PolicyState     : Valid
```

```powershell
Add-OBFileSpec -Policy $NewPolicy -FileSpec $Exclusions
```

```Output
BackupSchedule  : 4:00 PM
                  Saturday, Sunday,
                  Every 1 week(s)
DsList          : {DataSource
                  DatasourceId:0
                  Name:C:\
                  FileSpec:FileSpec
                  FileSpec:C:\
                  IsExclude:False
                  IsRecursive:True
                  ,FileSpec
                  FileSpec:C:\windows
                  IsExclude:True
                  IsRecursive:True
                  ,FileSpec
                  FileSpec:C:\temp
                  IsExclude:True
                  IsRecursive:True

                  , DataSource
                  DatasourceId:0
                  Name:D:\
                  FileSpec:FileSpec
                  FileSpec:D:\
                  IsExclude:False
                  IsRecursive:True

                  }
PolicyName      :
RetentionPolicy : Retention Days : 7

                  WeeklyLTRSchedule :
                  Weekly schedule is not set

                  MonthlyLTRSchedule :
                  Monthly schedule is not set

                  YearlyLTRSchedule :
                  Yearly schedule is not set

State           : New
PolicyState     : Valid
```

### <a name="applying-the-policy"></a>Het beleid toepassen

Nu is het beleidsobject voltooid en heeft het een bijbehorend back-upschema, bewaarbeleid en een lijst met bestanden voor opname/uitsluiting. Dit beleid kan nu worden vastgelegd voor Azure Backup te gebruiken. Voordat u het nieuw gemaakte beleid toepast, moet u ervoor zorgen dat er geen bestaand back-upbeleid aan de server is gekoppeld met de cmdlet [Remove-OBPolicy.](https://docs.microsoft.com/powershell/module/msonlinebackup/remove-obpolicy?view=winserver2012-ps) Als u het beleid verwijdert, wordt dit nodig om bevestiging. Gebruik de `-Confirm:$false` vlag met de cmdlet om de bevestiging over te slaan.

```powershell
Get-OBPolicy | Remove-OBPolicy
```

```Output
Microsoft Azure Backup Are you sure you want to remove this backup policy? This will delete all the backed up data. [Y] Yes [A] Yes to All [N] No [L] No to All [S] Suspend [?] Help (default is "Y"):
```

Het plegen van het beleidsobject gebeurt met de cmdlet [Set-OBPolicy.](https://docs.microsoft.com/powershell/module/msonlinebackup/set-obpolicy?view=winserver2012-ps) Dit zal ook vragen om bevestiging. Gebruik de `-Confirm:$false` vlag met de cmdlet om de bevestiging over te slaan.

```powershell
Set-OBPolicy -Policy $NewPolicy
```

```Output
Microsoft Azure Backup Do you want to save this backup policy ? [Y] Yes [A] Yes to All [N] No [L] No to All [S] Suspend [?] Help (default is "Y"):
BackupSchedule : 4:00 PM Saturday, Sunday, Every 1 week(s)
DsList : {DataSource
         DatasourceId:4508156004108672185
         Name:C:\
         FileSpec:FileSpec
         FileSpec:C:\
         IsExclude:False
         IsRecursive:True,

         FileSpec
         FileSpec:C:\windows
         IsExclude:True
         IsRecursive:True,

         FileSpec
         FileSpec:C:\temp
         IsExclude:True
         IsRecursive:True,

         DataSource
         DatasourceId:4508156005178868542
         Name:D:\
         FileSpec:FileSpec
         FileSpec:D:\
         IsExclude:False
         IsRecursive:True
    }
PolicyName : c2eb6568-8a06-49f4-a20e-3019ae411bac
RetentionPolicy : Retention Days : 7
              WeeklyLTRSchedule :
              Weekly schedule is not set

              MonthlyLTRSchedule :
              Monthly schedule is not set

              YearlyLTRSchedule :
              Yearly schedule is not set
State : Existing PolicyState : Valid
```

U de details van het bestaande back-upbeleid bekijken met de [cmdlet Get-OBPolicy.](https://docs.microsoft.com/powershell/module/msonlinebackup/get-obpolicy?view=winserver2012-ps) U verder inzoomen met de [cmdlet Get-OBSchedule](https://docs.microsoft.com/powershell/module/msonlinebackup/get-obschedule?view=winserver2012-ps) voor het back-upschema en de cmdlet [Get-OBRetentionPolicy](https://docs.microsoft.com/powershell/module/msonlinebackup/get-obretentionpolicy?view=winserver2012-ps) voor het bewaarbeleid

```powershell
Get-OBPolicy | Get-OBSchedule
```

```Output
SchedulePolicyName : 71944081-9950-4f7e-841d-32f0a0a1359a
ScheduleRunDays : {Saturday, Sunday}
ScheduleRunTimes : {16:00:00}
State : Existing
```

```powershell
Get-OBPolicy | Get-OBRetentionPolicy
```

```Output
RetentionDays : 7
RetentionPolicyName : ca3574ec-8331-46fd-a605-c01743a5265e
State : Existing
```

```powershell
Get-OBPolicy | Get-OBFileSpec
```

```Output
FileName : *
FilePath : \?\Volume{b835d359-a1dd-11e2-be72-2016d8d89f0f}\
FileSpec : D:\
IsExclude : False
IsRecursive : True

FileName : *
FilePath : \?\Volume{cdd41007-a22f-11e2-be6c-806e6f6e6963}\
FileSpec : C:\
IsExclude : False
IsRecursive : True

FileName : *
FilePath : \?\Volume{cdd41007-a22f-11e2-be6c-806e6f6e6963}\windows
FileSpec : C:\windows
IsExclude : True
IsRecursive : True

FileName : *
FilePath : \?\Volume{cdd41007-a22f-11e2-be6c-806e6f6e6963}\temp
FileSpec : C:\temp
IsExclude : True
IsRecursive : True
```

### <a name="performing-an-on-demand-backup"></a>Een on-demand back-up uitvoeren

Zodra een back-upbeleid is ingesteld, worden de back-ups volgens de planning uitgevoerd. Het activeren van een on-demand back-up is ook mogelijk met de [Start-OBBackup-cmdlet:](https://docs.microsoft.com/powershell/module/msonlinebackup/start-obbackup?view=winserver2012-ps)

```powershell
Get-OBPolicy | Start-OBBackup
```

```Output
Initializing
Taking snapshot of volumes...
Preparing storage...
Generating backup metadata information and preparing the metadata VHD...
Data transfer is in progress. It might take longer since it is the first backup and all data needs to be transferred...
Data transfer completed and all backed up data is in the cloud. Verifying data integrity...
Data transfer completed
In progress...
Job completed.
The backup operation completed successfully.
```

## <a name="back-up-windows-server-system-state-in-mabs-agent"></a>Back-ups maken van windows serversysteemstatus in MABS-agent

Deze sectie behandelt de PowerShell-opdracht voor het instellen van systeemstatus in MABS-agent

### <a name="schedule"></a>Planning

```powershell
$sched = New-OBSchedule -DaysOfWeek Sunday,Monday,Tuesday,Wednesday,Thursday,Friday,Saturday -TimesOfDay 2:00
```

### <a name="retention"></a>Bewaartermijn

```powershell
$rtn = New-OBRetentionPolicy -RetentionDays 32 -RetentionWeeklyPolicy -RetentionWeeks 13 -WeekDaysOfWeek Sunday -WeekTimesOfDay 2:00  -RetentionMonthlyPolicy -RetentionMonths 13 -MonthDaysOfMonth 1 -MonthTimesOfDay 2:00
```

### <a name="configuring-schedule-and-retention"></a>Planning en retentie configureren

```powershell
New-OBPolicy | Add-OBSystemState |  Set-OBRetentionPolicy -RetentionPolicy $rtn | Set-OBSchedule -Schedule $sched | Set-OBSystemStatePolicy
 ```

### <a name="verifying-the-policy"></a>Het beleid verifiëren

```powershell
Get-OBSystemStatePolicy
 ```

## <a name="restore-data-from-azure-backup"></a>Gegevens herstellen vanuit Azure Backup

In deze sectie wordt u door de stappen geleid voor het automatiseren van het herstel van gegevens van Azure Backup. Dit omvat de volgende stappen:

1. Het bronvolume kiezen
2. Een back-uppunt kiezen om te herstellen
3. Een item opgeven dat moet worden hersteld
4. Het herstelproces activeren

### <a name="picking-the-source-volume"></a>Het bronvolume kiezen

Als u een item wilt herstellen vanuit Azure Backup, moet u eerst de bron van het item identificeren. Aangezien we de opdrachten uitvoeren in de context van een Windows Server of een Windows-client, is de machine al geïdentificeerd. De volgende stap bij het identificeren van de bron is het identificeren van het volume dat deze bevat. Een lijst met volumes of bronnen die een back-up van deze machine maken, kan worden opgehaald door de [cmdlet Get-OBRecoverableSource](https://docs.microsoft.com/powershell/module/msonlinebackup/get-obrecoverablesource?view=winserver2012-ps) uit te voeren. Met deze opdracht retourneert een array van alle bronnen waarvan een back-up van deze server/client is back-upt.

```powershell
$Source = Get-OBRecoverableSource
$Source
```

```Output
FriendlyName : C:\
RecoverySourceName : C:\
ServerName : myserver.microsoft.com

FriendlyName : D:\
RecoverySourceName : D:\
ServerName : myserver.microsoft.com
```

### <a name="choosing-a-backup-point-from-which-to-restore"></a>Een back-uppunt kiezen om te herstellen

U haalt een lijst met back-uppunten op door de cmdlet [Get-OBRecoverableItem](https://docs.microsoft.com/powershell/module/msonlinebackup/get-obrecoverableitem?view=winserver2012-ps) uit te voeren met de juiste parameters. In ons voorbeeld kiezen we het nieuwste back-uppunt voor het bronvolume *C:* en gebruiken we het om een specifiek bestand te herstellen.

```powershell
$Rps = Get-OBRecoverableItem $Source[0]
$Rps
```

```Output

IsDir                : False
ItemNameFriendly     : C:\
ItemNameGuid         : \\?\Volume{297cbf7a-0000-0000-0000-401f00000000}\
LocalMountPoint      : C:\
MountPointName       : C:\
Name                 : C:\
PointInTime          : 10/17/2019 7:52:13 PM
ServerName           : myserver.microsoft.com
ItemSize             :
ItemLastModifiedTime :

IsDir                : False
ItemNameFriendly     : C:\
ItemNameGuid         : \\?\Volume{297cbf7a-0000-0000-0000-401f00000000}\
LocalMountPoint      : C:\
MountPointName       : C:\
Name                 : C:\
PointInTime          : 10/16/2019 7:00:19 PM
ServerName           : myserver.microsoft.com
ItemSize             :
ItemLastModifiedTime :
```

Het `$Rps` object is een array van back-uppunten. Het eerste element is het laatste punt en het Nth-element is het oudste punt. Om het laatste punt te `$Rps[0]`kiezen, zullen we gebruik maken van .

### <a name="specifying-an-item-to-restore"></a>Een item opgeven dat moet worden hersteld

Als u een specifiek bestand wilt herstellen, geeft u de bestandsnaam op ten opzichte van het hoofdvolume. Als u bijvoorbeeld C:\Test\Cat.job wilt ophalen, voert u de volgende opdracht uit.

```powershell
$Item = New-OBRecoverableItem $Rps[0] "Test\cat.jpg" $FALSE
$Item
```

```Output
IsDir                : False
ItemNameFriendly     : C:\Test\cat.jpg
ItemNameGuid         :
LocalMountPoint      : C:\
MountPointName       : C:\
Name                 : cat.jpg
PointInTime          : 10/17/2019 7:52:13 PM
ServerName           : myserver.microsoft.com
ItemSize             :
ItemLastModifiedTime : 21-Jun-14 6:43:02 AM

```

### <a name="triggering-the-restore-process"></a>Het herstelproces activeren

Om het herstelproces te activeren, moeten we eerst de herstelopties opgeven. Dit kan met behulp van de [New-OBRecoveryOption](https://docs.microsoft.com/powershell/module/msonlinebackup/new-obrecoveryoption?view=winserver2012-ps) cmdlet. Laten we in dit voorbeeld aannemen dat we de bestanden willen herstellen naar *C:\temp*. Laten we er ook van uitgaan dat we bestanden die al bestaan op de doelmap *C:\temp*willen overslaan. Als u een dergelijke hersteloptie wilt maken, gebruikt u de volgende opdracht:

```powershell
$RecoveryOption = New-OBRecoveryOption -DestinationPath "C:\temp" -OverwriteType Skip
```

Activeer nu het herstelproces met de opdracht [Start-OBRecovery](https://docs.microsoft.com/powershell/module/msonlinebackup/start-obrecovery?view=winserver2012-ps) op de geselecteerde `$Item` uit de uitvoer van de `Get-OBRecoverableItem` cmdlet:

```powershell
Start-OBRecovery -RecoverableItem $Item -RecoveryOption $RecoveryOption
```

```Output
Estimating size of backup items...
Estimating size of backup items...
Estimating size of backup items...
Estimating size of backup items...
Job completed.
The recovery operation completed successfully.
```

## <a name="uninstalling-the-azure-backup-agent"></a>De installatie van de Azure Backup-agent verwijderen

Het verwijderen van de Azure Backup-agent kan met de volgende opdracht:

```powershell
.\MARSAgentInstaller.exe /d /q
```

Het verwijderen van de agent binaries van de machine heeft een aantal gevolgen te overwegen:

* Het verwijdert het bestandsfilter uit de machine en het bijhouden van wijzigingen wordt gestopt.
* Alle beleidsinformatie wordt uit de machine verwijderd, maar de beleidsinformatie blijft worden opgeslagen in de service.
* Alle back-upschema's worden verwijderd en er worden geen back-ups meer gemaakt.

De gegevens die in Azure zijn opgeslagen, blijven echter behouden volgens de door u instellingen voor het bewaarbeleid. Oudere punten worden automatisch ouder.

## <a name="remote-management"></a>Extern beheer

Al het beheer rond de Azure Backup-agent, het beleid en de gegevensbronnen kan op afstand worden uitgevoerd via PowerShell. De machine die op afstand wordt beheerd, moet correct worden voorbereid.

Standaard is de WinRM-service geconfigureerd voor handmatig opstarten. Het opstarttype moet worden ingesteld op *Automatisch* en de service moet worden gestart. Als u wilt controleren of de WinRM-service wordt uitgevoerd, moet de waarde van de eigenschap Status *actief*zijn .

```powershell
Get-Service -Name WinRM
```

```Output
Status   Name               DisplayName
------   ----               -----------
Running  winrm              Windows Remote Management (WS-Manag...
```

PowerShell moet worden geconfigureerd voor remoting.

```powershell
Enable-PSRemoting -Force
```

```Output
WinRM is already set up to receive requests on this computer.
WinRM has been updated for remote management.
WinRM firewall exception enabled.
```

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force
```

De machine kan nu op afstand worden beheerd - vanaf de installatie van de agent. Het volgende script kopieert de agent bijvoorbeeld naar de externe machine en installeert deze.

```powershell
$DLoc = "\\REMOTESERVER01\c$\Windows\Temp"
$Agent = "\\REMOTESERVER01\c$\Windows\Temp\MARSAgentInstaller.exe"
$Args = "/q"
Copy-Item "C:\Downloads\MARSAgentInstaller.exe" -Destination $DLoc -Force

$Session = New-PSSession -ComputerName REMOTESERVER01
Invoke-Command -Session $Session -Script { param($D, $A) Start-Process -FilePath $D $A -Wait } -ArgumentList $Agent, $Args
```

## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over Azure Backup voor Windows Server/Client:

* [Kennismaking met Azure Backup](backup-introduction-to-azure-backup.md)
* [Een back-up maken van Windows-servers](backup-windows-with-mars-agent.md)
