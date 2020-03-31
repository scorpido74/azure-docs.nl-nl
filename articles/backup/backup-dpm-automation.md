---
title: PowerShell gebruiken om een back-up te maken van DPM-workloads
description: Meer informatie over het implementeren en beheren van Azure Backup for Data Protection Manager (DPM) met PowerShell
ms.topic: conceptual
ms.date: 01/23/2017
ms.openlocfilehash: 06c138a4015a0b730369e091fc57a34d2190051d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77616734"
---
# <a name="deploy-and-manage-backup-to-azure-for-data-protection-manager-dpm-servers-using-powershell"></a>Met behulp van PowerShell back-ups implementeren en beheren in Azure voor Data Protection Manager (DPM)-servers

In dit artikel ziet u hoe u PowerShell gebruiken om Azure Backup in te stellen op een DPM-server en back-ups en herstel te beheren.

## <a name="setting-up-the-powershell-environment"></a>De PowerShell-omgeving instellen

Voordat u PowerShell gebruiken om back-ups te beheren van Gegevensbeschermingsbeheer naar Azure, moet u de juiste omgeving in PowerShell hebben. Controleer aan het begin van de PowerShell-sessie of u de volgende opdracht uitvoert om de juiste modules te importeren en u in staat stelt correct naar de DPM-cmdlets te verwijzen:

```powershell
& "C:\Program Files\Microsoft System Center 2012 R2\DPM\DPM\bin\DpmCliInitScript.ps1"
```

```Output
Welcome to the DPM Management Shell!

Full list of cmdlets: Get-Command
Only DPM cmdlets: Get-DPMCommand
Get general help: help
Get help for a cmdlet: help <cmdlet-name> or <cmdlet-name> -?
Get definition of a cmdlet: Get-Command <cmdlet-name> -Syntax
Sample DPM scripts: Get-DPMSampleScript
```

## <a name="setup-and-registration"></a>Instellen en registreren

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Download om te beginnen [de nieuwste Azure PowerShell](/powershell/azure/install-az-ps).

De volgende installatie- en registratietaken kunnen worden geautomatiseerd met PowerShell:

* Een Recovery Services-kluis maken
* De Azure Backup-agent installeren
* Registreren bij de Azure Backup-service
* Netwerkinstellingen
* Versleutelingsinstellingen

## <a name="create-a-recovery-services-vault"></a>Een Recovery Services-kluis maken

De volgende stappen leiden u door het maken van een Vault Recovery Services. Een vault van Recovery Services is anders dan een back-upkluis.

1. Als u Azure Backup voor de eerste keer gebruikt, moet u de cmdlet **Register-AzResourceProvider** gebruiken om de Azure Recovery Service-provider met uw abonnement te registreren.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

2. De kluis Recovery Services is een ARM-bron, dus u moet deze binnen een resourcegroep plaatsen. U een bestaande resourcegroep gebruiken of een nieuwe groep maken. Geef bij het maken van een nieuwe resourcegroep de naam en locatie voor de resourcegroep op.

    ```powershell
    New-AzResourceGroup –Name "test-rg" –Location "West US"
    ```

3. Gebruik de **cmdlet New-AzRecoveryServicesVault** om een nieuwe kluis te maken. Zorg ervoor dat u dezelfde locatie voor de kluis opgeeft als voor de resourcegroep.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName " test-rg" -Location "West US"
    ```

4. Geef het type opslagredundantie op dat moet worden gebruikt; u [Lokaal redundante opslag (LRS)](../storage/common/storage-redundancy-lrs.md) of [Geo Redundant Storage (GRS)](../storage/common/storage-redundancy-grs.md)gebruiken. In het volgende voorbeeld ziet u de optie -BackupStorageRedundancy voor testVault is ingesteld op GeoRedundant.

   > [!TIP]
   > Voor veel Azure Backup-cmdlets is het object Recovery Services-kluis als invoer vereist. Daarom is het handiger het object Backup Recovery Services-kluis in een variabele op te slaan.
   >
   >

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault –Name "testVault"
    Set-AzRecoveryServicesBackupProperties  -vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

## <a name="view-the-vaults-in-a-subscription"></a>Bekijk de kluizen in een abonnement

Gebruik **Get-AzRecoveryServicesVault** om de lijst met alle kluizen in het huidige abonnement weer te geven. U deze opdracht gebruiken om te controleren of er een nieuwe kluis is gemaakt of om te zien welke kluizen beschikbaar zijn in het abonnement.

Voer de opdracht, Get-AzRecoveryServicesVault en alle kluizen in het abonnement uit.

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

## <a name="installing-the-azure-backup-agent-on-a-dpm-server"></a>De Azure Backup-agent installeren op een DPM-server

Voordat u de Azure Backup-agent installeert, moet u het installatieprogramma laten downloaden en presenteren op de Windows Server. U de nieuwste versie van het installatieprogramma downloaden van het [Microsoft Download center](https://aka.ms/azurebackup_agent) of via de dashboardpagina van de Vault Recovery Services. Sla het installatieprogramma op een gemakkelijk toegankelijke\*locatie op, zoals *C:\Downloads .

Als u de agent wilt installeren, voert u de volgende opdracht uit in een verhoogde PowerShell-console **op de DPM-server:**

```powershell
MARSAgentInstaller.exe /q
```

Hiermee wordt de agent geïnstalleerd met alle standaardopties. De installatie duurt een paar minuten op de achtergrond. Als u de optie */nu* niet opgeeft, wordt het **Windows Update-venster** aan het einde van de installatie geopend om te controleren op updates.

De agent wordt weergegeven in de lijst met geïnstalleerde programma's. Als u de lijst met geïnstalleerde programma's wilt bekijken, gaat u naar > **Programma's en** > **-functies**van het **Configuratiescherm**.

![Agent geïnstalleerd](./media/backup-dpm-automation/installed-agent-listing.png)

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

## <a name="registering-dpm-to-a-recovery-services-vault"></a>DPM registreren in een Vault voor Recovery Services

Nadat u de kluis Van Herstelservices hebt gemaakt, downloadt u de nieuwste agent en de vault-referenties en slaat u deze op een handige locatie op, zoals C:\Downloads.

```powershell
$credspath = "C:\downloads"
$credsfilename = Get-AzRecoveryServicesVaultSettingsFile -Backup -Vault $vault1 -Path  $credspath
$credsfilename
```

```Output
C:\downloads\testvault\_Sun Apr 10 2016.VaultCredentials
```

Voer op de DPM-server de [cmdlet Start-OBRegistration](https://docs.microsoft.com/powershell/module/msonlinebackup/start-obregistration?view=winserver2012-ps) uit om de machine bij de kluis te registreren.

```powershell
$cred = $credspath + $credsfilename
Start-OBRegistration-VaultCredentials $cred -Confirm:$false
```

```Output
CertThumbprint      :7a2ef2caa2e74b6ed1222a5e89288ddad438df2
SubscriptionID      : ef4ab577-c2c0-43e4-af80-af49f485f3d1
ServiceResourceName: testvault
Region              :West US
Machine registration succeeded.
```

### <a name="initial-configuration-settings"></a>Initiële configuratie-instellingen

Zodra de DPM-server is geregistreerd bij de kluis Van Herstelservices, begint deze met de standaardabonnementsinstellingen. Deze abonnementsinstellingen omvatten Netwerken, Versleuteling en het staging-gebied. Als u de abonnementsinstellingen wilt wijzigen, moet u eerst de bestaande (standaard) instellingen gebruiken met de cmdlet [Get-DPMCloudSubscriptionSetting:](https://docs.microsoft.com/powershell/module/dataprotectionmanager/get-dpmcloudsubscriptionsetting?view=systemcenter-ps-2019)

```powershell
$setting = Get-DPMCloudSubscriptionSetting -DPMServerName "TestingServer"
```

Alle wijzigingen worden aangebracht in dit ```$setting``` lokale PowerShell-object en vervolgens is het volledige object vastgelegd in DPM en Azure Backup om ze op te slaan met behulp van de cmdlet [Set-DPMCloudSubscriptionSetting.](https://docs.microsoft.com/powershell/module/dataprotectionmanager/set-dpmcloudsubscriptionsetting?view=systemcenter-ps-2019) U moet de ```–Commit``` vlag gebruiken om ervoor te zorgen dat de wijzigingen blijven bestaan. De instellingen worden niet toegepast en gebruikt door Azure Backup, tenzij deze zijn vastgelegd.

```powershell
Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -Commit
```

## <a name="networking"></a>Netwerken

Als de verbinding van de DPM-machine met de Azure Backup-service op internet via een proxyserver is, moeten de proxyserverinstellingen worden verstrekt voor succesvolle back-ups. Dit wordt gedaan ```-ProxyServer```met ```-ProxyPort``` ```-ProxyUsername``` behulp ```ProxyPassword``` van de en , en de parameters met de [cmdlet Set-DPMCloudSubscriptionSetting.](https://docs.microsoft.com/powershell/module/dataprotectionmanager/set-dpmcloudsubscriptionsetting?view=systemcenter-ps-2019) In dit voorbeeld is er geen proxyserver, dus we wissen expliciet proxy-gerelateerde informatie.

```powershell
Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -NoProxy
```

Bandbreedte gebruik kan ook worden ```-WorkHourBandwidth``` ```-NonWorkHourBandwidth``` gecontroleerd met opties van en voor een bepaalde set van dagen van de week. In dit voorbeeld stellen we geen beperking in.

```powershell
Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -NoThrottle
```

## <a name="configuring-the-staging-area"></a>Het faseringsgebied configureren

De Azure Backup-agent die op de DPM-server wordt uitgevoerd, heeft tijdelijke opslag nodig voor gegevens die vanuit de cloud zijn hersteld (lokaal faseringsgebied). Configureer het faseringsgebied met de cmdlet [Set-DPMCloudSubscriptionSetting](https://docs.microsoft.com/powershell/module/dataprotectionmanager/set-dpmcloudsubscriptionsetting?view=systemcenter-ps-2019) en de ```-StagingAreaPath``` parameter.

```powershell
Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -StagingAreaPath "C:\StagingArea"
```

In het bovenstaande voorbeeld wordt het faseringsgebied ingesteld op *C:\StagingArea* in het PowerShell-object ```$setting```. Zorg ervoor dat de opgegeven map al bestaat, anders mislukt de uiteindelijke commit van de abonnementsinstellingen.

### <a name="encryption-settings"></a>Versleutelingsinstellingen

De back-upgegevens die naar Azure Backup worden verzonden, worden versleuteld om de vertrouwelijkheid van de gegevens te beschermen. De encryptie wachtwoordzin is het "wachtwoord" om de gegevens te decoderen op het moment van herstellen. Het is belangrijk om deze informatie veilig te houden zodra deze is ingesteld.

In het onderstaande voorbeeld wordt ```passphrase123456789``` de tekenreeks door de eerste opdracht omgezet ```$Passphrase```in een beveiligde tekenreeks en wordt de beveiligde tekenreeks toewijst aan de variabele met de naam . de tweede opdracht stelt ```$Passphrase``` de beveiligde tekenreeks in als het wachtwoord voor het versleutelen van back-ups.

```powershell
$Passphrase = ConvertTo-SecureString -string "passphrase123456789" -AsPlainText -Force

Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -EncryptionPassphrase $Passphrase
```

> [!IMPORTANT]
> Houd de informatie over de wachtwoordzin veilig en beveiligd zodra deze is ingesteld. U gegevens uit Azure niet herstellen zonder deze wachtwoordzin.
>
>

Op dit punt had u alle vereiste ```$setting``` wijzigingen in het object moeten aanbrengen. Vergeet niet om de veranderingen te plegen.

```powershell
Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -Commit
```

## <a name="protect-data-to-azure-backup"></a>Gegevens beveiligen met Azure Backup

In deze sectie voegt u een productieserver toe aan DPM en beschermt u de gegevens vervolgens naar lokale DPM-opslag en vervolgens naar Azure Backup. In de voorbeelden laten we zien hoe je een back-up maakt van bestanden en mappen. De logica kan eenvoudig worden uitgebreid naar een back-up van elke door DPM ondersteunde gegevensbron. Al uw DPM-back-ups worden geregeld door een Protection Group (PG) met vier delen:

1. **Groepsleden** is een lijst van alle beschermbare objecten (ook bekend als *Datasources* in DPM) die u wilt beschermen in dezelfde beveiligingsgroep. U bijvoorbeeld productie-VM's in één beveiligingsgroep en SQL Server-databases in een andere beveiligingsgroep beveiligen, omdat deze mogelijk verschillende back-upvereisten hebben. Voordat u een back-up maken van een gegevensbron op een productieserver, moet u ervoor zorgen dat de DPM-agent op de server is geïnstalleerd en wordt beheerd door DPM. Volg de stappen voor [het installeren van de DPM-agent](https://docs.microsoft.com/system-center/dpm/deploy-dpm-protection-agent?view=sc-dpm-2019) en het koppelen aan de juiste DPM-server.
2. **Gegevensbeschermingsmethode** geeft de doelback-uplocaties op - tape, schijf en cloud. In ons voorbeeld beschermen we gegevens naar de lokale schijf en naar de cloud.
3. Een **back-upschema** dat aangeeft wanneer back-ups moeten worden gemaakt en hoe vaak de gegevens moeten worden gesynchroniseerd tussen de DPM-server en de productieserver.
4. Een **bewaarschema** dat aangeeft hoe lang de herstelpunten in Azure moeten worden bewaard.

### <a name="creating-a-protection-group"></a>Een beveiligingsgroep maken

Begin met het maken van een nieuwe beveiligingsgroep met de cmdlet [Nieuw-DPMProtectionGroup.](https://docs.microsoft.com/powershell/module/dataprotectionmanager/new-dpmprotectiongroup?view=systemcenter-ps-2019)

```powershell
$PG = New-DPMProtectionGroup -DPMServerName " TestingServer " -Name "ProtectGroup01"
```

De bovenstaande cmdlet maakt een beveiligingsgroep met de naam *ProtectGroup01*. Een bestaande beveiligingsgroep kan later ook worden gewijzigd om back-ups toe te voegen aan de Azure-cloud. Echter, om wijzigingen aan te brengen in de Protection Group - nieuw of bestaand - moeten we een greep op een *aanpasbaar* object te krijgen met behulp van de [Get-DPMModifiableProtectionGroup](https://docs.microsoft.com/powershell/module/dataprotectionmanager/get-dpmmodifiableprotectiongroup?view=systemcenter-ps-2019) cmdlet.

```powershell
$MPG = Get-ModifiableProtectionGroup $PG
```

### <a name="adding-group-members-to-the-protection-group"></a>Groepsleden toevoegen aan de beveiligingsgroep

Elke DPM-agent kent de lijst met gegevensbronnen op de server waarop deze is geïnstalleerd. Als u een gegevensbron wilt toevoegen aan de beveiligingsgroep, moet de DPM-agent eerst een lijst met gegevensbronnen terugsturen naar de DPM-server. Een of meer gegevensbronnen worden vervolgens geselecteerd en toegevoegd aan de beveiligingsgroep. De PowerShell-stappen die nodig zijn om dit te bereiken zijn:

1. Haal een lijst op van alle servers die door DPM worden beheerd via de DPM-agent.
2. Kies een specifieke server.
3. Haal een lijst met alle gegevensbronnen op de server op.
4. Kies een of meer gegevensbronnen en voeg deze toe aan de beveiligingsgroep

De lijst met servers waarop de DPM-agent is geïnstalleerd en wordt beheerd door de DPM-server, wordt verkregen met de [cmdlet Get-DPMProductionServer.](https://docs.microsoft.com/powershell/module/dataprotectionmanager/get-dpmproductionserver?view=systemcenter-ps-2019) In dit voorbeeld filteren en configureren we ALLEEN PS met naam *productionserver01* voor back-up.

```powershell
$server = Get-ProductionServer -DPMServerName "TestingServer" | Where-Object {($_.servername) –contains "productionserver01"}
```

Haal nu de lijst ```$server``` met gegevensbronnen op met behulp van de [cmdlet Get-DPMDatasource.](https://docs.microsoft.com/powershell/module/dataprotectionmanager/get-dpmdatasource?view=systemcenter-ps-2019) In dit voorbeeld filteren we op het volume *D:\\ * dat we willen configureren voor back-up. Deze gegevensbron wordt vervolgens toegevoegd aan de beveiligingsgroep met behulp van de cmdlet [Add-DPMChildDatasource.](https://docs.microsoft.com/powershell/module/dataprotectionmanager/add-dpmchilddatasource?view=systemcenter-ps-2019) Vergeet niet om het *aanpasbare* beschermingsgroepobject ```$MPG``` te gebruiken om de toevoegingen te maken.

```powershell
$DS = Get-Datasource -ProductionServer $server -Inquire | Where-Object { $_.Name -contains "D:\" }

Add-DPMChildDatasource -ProtectionGroup $MPG -ChildDatasource $DS
```

Herhaal deze stap zo vaak als nodig is, totdat u alle gekozen gegevensbronnen aan de beveiligingsgroep hebt toegevoegd. U ook beginnen met slechts één gegevensbron en de werkstroom voor het maken van de beveiligingsgroep voltooien en op een later tijdstip meer gegevensbronnen toevoegen aan de beveiligingsgroep.

### <a name="selecting-the-data-protection-method"></a>De methode voor gegevensbescherming selecteren

Zodra de gegevensbronnen aan de beveiligingsgroep zijn toegevoegd, is de volgende stap het opgeven van de beveiligingsmethode met behulp van de cmdlet [Set-DPMProtectionType.](https://docs.microsoft.com/powershell/module/dataprotectionmanager/set-dpmprotectiontype?view=systemcenter-ps-2019) In dit voorbeeld wordt de beveiligingsgroep ingesteld voor lokale schijf- en cloudback-ups. U moet ook de gegevensbron opgeven die u wilt beveiligen voor de cloud met behulp van de cmdlet [Add-DPMChildDatasource](https://docs.microsoft.com/powershell/module/dataprotectionmanager/add-dpmchilddatasource?view=systemcenter-ps-2019) met -Online-vlag.

```powershell
Set-DPMProtectionType -ProtectionGroup $MPG -ShortTerm Disk –LongTerm Online
Add-DPMChildDatasource -ProtectionGroup $MPG -ChildDatasource $DS –Online
```

### <a name="setting-the-retention-range"></a>Het bewaarbereik instellen

Stel de bewaarslag in voor de back-uppunten met de cmdlet [Set-DPMPolicyObjective.](https://docs.microsoft.com/powershell/module/dataprotectionmanager/set-dpmpolicyobjective?view=systemcenter-ps-2019) Hoewel het misschien vreemd lijkt om de retentie in te ```Set-DPMPolicyObjective``` stellen voordat het back-upschema is gedefinieerd, stelt de cmdlet automatisch een standaard back-upschema in dat vervolgens kan worden gewijzigd. Het is altijd mogelijk om eerst het back-upschema in te stellen en daarna het bewaarbeleid in te stellen.

In het onderstaande voorbeeld stelt de cmdlet de bewaarparameters in voor schijfback-ups. Hierdoor worden back-ups gedurende 10 dagen bewaard en worden gegevens elke 6 uur gesynchroniseerd tussen de productieserver en de DPM-server. Het ```SynchronizationFrequencyMinutes``` bepaalt niet hoe vaak een back-uppunt wordt gemaakt, maar hoe vaak gegevens worden gekopieerd naar de DPM-server.  Met deze instelling voorkomt u dat back-ups te groot worden.

```powershell
Set-DPMPolicyObjective –ProtectionGroup $MPG -RetentionRangeInDays 10 -SynchronizationFrequencyMinutes 360
```

Voor back-ups die naar Azure gaan (DPM verwijst naar hen als Online back-ups) kunnen de bewaarbereiken worden geconfigureerd voor [lange termijn retentie met behulp van een Grandfather-Father-Son-regeling (GFS).](backup-azure-backup-cloud-as-tape.md) Dat wil zeggen dat u een gecombineerd bewaarbeleid definiëren met dagelijks, wekelijks, maandelijks en jaarlijks bewaarbeleid. In dit voorbeeld maken we een array die het complexe bewaarschema weergeeft dat we willen, en configureren we het bewaarbereik met de cmdlet [Set-DPMPolicyObjective.](https://docs.microsoft.com/powershell/module/dataprotectionmanager/set-dpmpolicyobjective?view=systemcenter-ps-2019)

```powershell
$RRlist = @()
$RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 180, Days)
$RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 104, Weeks)
$RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 60, Month)
$RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 10, Years)
Set-DPMPolicyObjective –ProtectionGroup $MPG -OnlineRetentionRangeList $RRlist
```

### <a name="set-the-backup-schedule"></a>Het back-upschema instellen

DPM stelt automatisch een standaard back-upschema in ```Set-DPMPolicyObjective``` als u de beveiligingsdoelstelling opgeeft met behulp van de cmdlet. Als u de standaardschema's wilt wijzigen, gebruikt u de cmdlet [Get-DPMPolicySchedule,](https://docs.microsoft.com/powershell/module/dataprotectionmanager/get-dpmpolicyschedule?view=systemcenter-ps-2019) gevolgd door de cmdlet [Set-DPMPolicySchedule.](https://docs.microsoft.com/powershell/module/dataprotectionmanager/set-dpmpolicyschedule?view=systemcenter-ps-2019)

```powershell
$onlineSch = Get-DPMPolicySchedule -ProtectionGroup $mpg -LongTerm Online
Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[0] -TimesOfDay 02:00
Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[1] -TimesOfDay 02:00 -DaysOfWeek Sa,Su –Interval 1
Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[2] -TimesOfDay 02:00 -RelativeIntervals First,Third –DaysOfWeek Sa
Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[3] -TimesOfDay 02:00 -DaysOfMonth 2,5,8,9 -Months Jan,Jul
Set-DPMProtectionGroup -ProtectionGroup $MPG
```

In het bovenstaande ```$onlineSch``` voorbeeld is een array met vier elementen die het bestaande online beschermingsschema voor de beschermingsgroep in het GFS-schema bevat:

1. ```$onlineSch[0]```bevat het dagelijkse schema
2. ```$onlineSch[1]```bevat het wekelijkse schema
3. ```$onlineSch[2]```bevat het maandelijkse schema
4. ```$onlineSch[3]```bevat het jaarlijkse schema

Dus als je nodig hebt om het wekelijkse ```$onlineSch[1]```schema te wijzigen, moet u verwijzen naar de .

### <a name="initial-backup"></a>Eerste back-up

Wanneer u voor het eerst een back-up maakt van een gegevensbron, moet voor DPM een eerste replica worden gemaakt waarmee een volledige kopie van de gegevensbron wordt gemaakt die moet worden beveiligd op het volume van DPM-replica's. Deze activiteit kan worden gepland voor een bepaalde tijd, of kan handmatig worden geactiveerd met behulp ```-NOW```van de [cmdlet Set-DPMReplicaCreationMethod](https://docs.microsoft.com/powershell/module/dataprotectionmanager/set-dpmreplicacreationmethod?view=systemcenter-ps-2019) met de parameter .

```powershell
Set-DPMReplicaCreationMethod -ProtectionGroup $MPG -NOW
```

### <a name="changing-the-size-of-dpm-replica--recovery-point-volume"></a>De grootte van de DPM-replica wijzigen & herstelpuntvolume

U ook de grootte van het volume van DPM-replica's en het volume van schaduwkopie wijzigen met de cmdlet [Set-DPMDatasourceDiskAllocation](https://docs.microsoft.com/powershell/module/dataprotectionmanager/set-dpmdatasourcediskallocation?view=systemcenter-ps-2019) als in het volgende voorbeeld: Get-DatasourceDiskAllocation -Datasource$DS Set-DatasourceDiskAllocation -Datasource $DS -ProtectionGroup $MPG -manual -ReplicaArea (2gb) -ShadowCopyArea (2gb)

### <a name="committing-the-changes-to-the-protection-group"></a>Het plegen van de wijzigingen in de beschermingsgroep

Ten slotte moeten de wijzigingen worden doorgevoerd voordat DPM de back-up kan maken per de nieuwe configuratie van de beveiligingsgroep. Dit kan worden bereikt met de [Set-DPMProtectionGroup](https://docs.microsoft.com/powershell/module/dataprotectionmanager/set-dpmprotectiongroup?view=systemcenter-ps-2019) cmdlet.

```powershell
Set-DPMProtectionGroup -ProtectionGroup $MPG
```

## <a name="view-the-backup-points"></a>Bekijk de back-uppunten

U de [cmdlet Get-DPMRecoveryPoint](https://docs.microsoft.com/powershell/module/dataprotectionmanager/get-dpmrecoverypoint?view=systemcenter-ps-2019) gebruiken om een lijst met alle herstelpunten voor een gegevensbron te krijgen. In dit voorbeeld zullen we:

* alle PG's ophalen op de DPM-server en opgeslagen in een array```$PG```
* krijgen de gegevensbronnen die overeenkomen met de```$PG[0]```
* krijgen alle herstelpunten voor een datasource.

```powershell
$PG = Get-DPMProtectionGroup –DPMServerName "TestingServer"
$DS = Get-DPMDatasource -ProtectionGroup $PG[0]
$RecoveryPoints = Get-DPMRecoverypoint -Datasource $DS[0] -Online
```

## <a name="restore-data-protected-on-azure"></a>Gegevens herstellen die zijn beveiligd in Azure

Het herstellen van gegevens ```RecoverableItem``` is ```RecoveryOption``` een combinatie van een object en een object. In de vorige sectie kregen we een lijst van de back-uppunten voor een databron.

In het onderstaande voorbeeld laten we zien hoe u een virtuele Hyper-V-machine uit Azure Backup herstellen door back-uppunten te combineren met het doel voor herstel. Dit voorbeeld omvat:

* Een hersteloptie maken met de cmdlet [Nieuw-DPMRecoveryOption.](https://docs.microsoft.com/powershell/module/dataprotectionmanager/new-dpmrecoveryoption?view=systemcenter-ps-2019)
* Het ophalen van de array ```Get-DPMRecoveryPoint``` van back-uppunten met behulp van de cmdlet.
* Het kiezen van een back-up punt te herstellen van.

```powershell
$RecoveryOption = New-DPMRecoveryOption -HyperVDatasource -TargetServer "HVDCenter02" -RecoveryLocation AlternateHyperVServer -RecoveryType Recover -TargetLocation "C:\VMRecovery"

$PG = Get-DPMProtectionGroup –DPMServerName "TestingServer"
$DS = Get-DPMDatasource -ProtectionGroup $PG[0]
$RecoveryPoints = Get-DPMRecoverypoint -Datasource $DS[0] -Online

Restore-DPMRecoverableItem -RecoverableItem $RecoveryPoints[0] -RecoveryOption $RecoveryOption
```

De opdrachten kunnen eenvoudig worden uitgebreid voor elk gegevensbrontype.

## <a name="next-steps"></a>Volgende stappen

* Zie [Inleiding tot DPM-back-up](backup-azure-dpm-introduction.md) voor meer informatie over DPM naar Azure Backup
