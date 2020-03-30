---
title: Geautomatiseerde back-up v2 voor SQL Server 2016/2017 Azure VM's | Microsoft Documenten
description: Hiermee wordt de functie Geautomatiseerde back-up voor SQL Server 2016/2017 VM's weergegeven in Azure. Dit artikel is specifiek voor VM's met behulp van Resource Manager.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.assetid: ebd23868-821c-475b-b867-06d4a2e310c7
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/03/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 458012982531e228f7c4968f29e79e8b2e29aa48
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77651425"
---
# <a name="automated-backup-v2-for-azure-virtual-machines-resource-manager"></a>Geautomatiseerde back-up v2 voor Azure Virtual Machines (Resource Manager)

> [!div class="op_single_selector"]
> * [SQL Server 2014](virtual-machines-windows-sql-automated-backup.md)
> * [SQL Server 2016/2017](virtual-machines-windows-sql-automated-backup-v2.md)

Automated Backup v2 configureert Managed Backup automatisch [naar Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx) voor alle bestaande en nieuwe databases op een Azure VM met SQL Server 2016/2017 Standard, Enterprise of Developer-edities. Hiermee u regelmatige databaseback-ups configureren die gebruikmaken van duurzame Azure blob-opslag. Geautomatiseerde back-up v2 is afhankelijk van de [SQL Server IaaS Agent Extension.](virtual-machines-windows-sql-server-agent-extension.md)

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="prerequisites"></a>Vereisten
Als u Automated Backup v2 wilt gebruiken, raadpleegt u de volgende vereisten:

**Besturingssysteem**:

- Windows Server 2012 R2
- Windows Server 2016

**SQL Server-versie/-editie**:

- SQL Server 2016: ontwikkelaar, standaard of onderneming
- SQL Server 2017: ontwikkelaar, standaard of onderneming

> [!IMPORTANT]
> Automated Backup v2 werkt met SQL Server 2016 of hoger. Als u SQL Server 2014 gebruikt, u Automated Backup v1 gebruiken om een back-up van uw databases te maken. Zie [Geautomatiseerde back-up voor SQL Server 2014 Azure Virtual Machines voor](virtual-machines-windows-sql-automated-backup.md)meer informatie.

**Databaseconfiguratie:**

- Doeldatabases moeten het volledige herstelmodel gebruiken. Zie [Back-up onder het volledige herstelmodel voor](https://technet.microsoft.com/library/ms190217.aspx)meer informatie over de impact van het volledige herstelmodel op back-ups.
- Systeemdatabases hoeven geen volledig herstelmodel te gebruiken. Als u echter wilt dat er logboekback-ups worden gemaakt voor Model of MSDB, moet u het volledige herstelmodel gebruiken.
- Doeldatabases moeten zich op de standaard SQL Server-instantie of een [correct geïnstalleerde](virtual-machines-windows-sql-server-iaas-faq.md#administration) instantie van de naam begeven. 

> [!NOTE]
> Automated Backup is gebaseerd op de **SQL Server IaaS Agent Extension.** Huidige SQL-galerieafbeeldingen met virtuele machine voegen deze extensie standaard toe. Zie [SQL Server IaaS Agent Extension](virtual-machines-windows-sql-server-agent-extension.md)voor meer informatie.

## <a name="settings"></a>Instellingen
In de volgende tabel worden de opties beschreven die kunnen worden geconfigureerd voor Geautomatiseerde back-up v2. De werkelijke configuratiestappen zijn afhankelijk van of u de Azure-portal of Azure Windows PowerShell-opdrachten gebruikt.

### <a name="basic-settings"></a>Basisinstellingen

| Instelling | Bereik (standaard) | Beschrijving |
| --- | --- | --- |
| **Geautomatiseerde back-up** | Inschakelen/uitschakelen (uitgeschakeld) | Hiermee schakelt u Geautomatiseerde back-up in of uit voor een Azure VM met SQL Server 2016/2017 Developer, Standard of Enterprise. |
| **Bewaarperiode** | 1-30 dagen (30 dagen) | Het aantal dagen om back-ups te behouden. |
| **Opslagaccount** | Azure Storage-account | Een Azure-opslagaccount dat moet worden gebruikt voor het opslaan van geautomatiseerde back-upbestanden in blob-opslag. Op deze locatie wordt een container gemaakt om alle back-upbestanden op te slaan. De naamovereenkomst voor back-upbestanden bevat de datum, tijd en database-GUID. |
| **Codering** |Inschakelen/uitschakelen (uitgeschakeld) | Hiermee schakelt u versleuteling in of schakelt deze uit. Wanneer versleuteling is ingeschakeld, bevinden de certificaten die worden gebruikt om de back-up te herstellen zich in het opgegeven opslagaccount. Het maakt gebruik van dezelfde **automatische back-up** container met dezelfde naamgeving conventie. Als het wachtwoord wordt gewijzigd, wordt een nieuw certificaat gegenereerd met dat wachtwoord, maar het oude certificaat blijft om eerdere back-ups te herstellen. |
| **Wachtwoord** |Wachtwoordtekst | Een wachtwoord voor versleutelingssleutels. Dit wachtwoord is alleen vereist als versleuteling is ingeschakeld. Als u een versleutelde back-up wilt herstellen, moet u het juiste wachtwoord en bijbehorende certificaat hebben dat is gebruikt op het moment dat de back-up werd genomen. |

### <a name="advanced-settings"></a>Geavanceerde instellingen

| Instelling | Bereik (standaard) | Beschrijving |
| --- | --- | --- |
| **Back-ups van systeemdatabase** | Inschakelen/uitschakelen (uitgeschakeld) | Wanneer deze functie is ingeschakeld, maakt deze functie ook een back-up van de systeemdatabases: Master, MSDB en Model. Controleer voor de MSDB- en Modeldatabases of ze in de volledige herstelmodus zijn als u logboekback-ups wilt maken. Log back-ups worden nooit genomen voor Master. En er worden geen back-ups gemaakt voor TempDB. |
| **Back-upschema** | Handleiding/geautomatiseerd (geautomatiseerd) | Standaard wordt het back-upschema automatisch bepaald op basis van de logboekgroei. Met een handmatig back-upschema kan de gebruiker het tijdvenster voor back-ups opgeven. In dit geval vinden back-ups alleen plaats op de opgegeven frequentie en tijdens het opgegeven tijdvenster van een bepaalde dag. |
| **Volledige back-upfrequentie** | Dagelijks/Wekelijks | Frequentie van volledige back-ups. In beide gevallen beginnen volledige back-ups tijdens het volgende geplande tijdvenster. Wanneer wekelijks is geselecteerd, kunnen back-ups meerdere dagen duren totdat een back-up van alle databases is gemaakt. |
| **Volledige starttijd voor back-up** | 00:00 – 23:00 (01:00) | Begintijd van een bepaalde dag waarin volledige back-ups kunnen plaatsvinden. |
| **Tijdvenster voor volledige back-up** | 1 – 23 uur (1 uur) | Duur van het tijdvenster van een bepaalde dag waarin volledige back-ups kunnen plaatsvinden. |
| **Frequentie van logboekback-up** | 5 – 60 minuten (60 minuten) | Frequentie van logboekback-ups. |

## <a name="understanding-full-backup-frequency"></a>Volledige back-upfrequentie begrijpen
Het is belangrijk om het verschil tussen dagelijkse en wekelijkse volledige back-ups te begrijpen. Houd rekening met de volgende twee voorbeeldscenario's.

### <a name="scenario-1-weekly-backups"></a>Scenario 1: Wekelijkse back-ups
U hebt een SQL Server VM die een aantal grote databases bevat.

Op maandag schakelt u Automated Backup v2 in met de volgende instellingen:

- Back-upschema: **handleiding**
- Volledige back-upfrequentie: **Wekelijks**
- Volledige starttijd voor back-up: **01:00**
- Full backup time window: **1 uur**

Dit betekent dat de volgende beschikbare back-up venster is dinsdag om 1 uur voor 1 uur. Op dat moment maakt Automated Backup een back-up van uw databases, één voor één. In dit scenario zijn uw databases groot genoeg dat volledige back-ups zijn voltooid voor de eerste paar databases. Echter, na een uur niet alle van de databases zijn een back-up.

Wanneer dit gebeurt, automated backup begint een back-up van de resterende databases de volgende dag, woensdag om 1 uur voor een uur. Als niet alle databases in die tijd een back-up hebben gemaakt, probeert het de volgende dag opnieuw op hetzelfde moment. Dit gaat door totdat er een back-up van alle databases is gemaakt.

Nadat het dinsdag opnieuw bereikt, automated backup begint een back-up van alle databases weer.

Dit scenario toont aan dat Geautomatiseerde back-up alleen binnen het opgegeven tijdsvenster werkt en dat elke database één keer per week wordt geback-upt. Dit toont ook aan dat het mogelijk is voor back-ups om meerdere dagen te overspannen in het geval dat het niet mogelijk is om alle back-ups in één dag te voltooien.

### <a name="scenario-2-daily-backups"></a>Scenario 2: Dagelijkse back-ups
U hebt een SQL Server VM die een aantal grote databases bevat.

Op maandag schakelt u Automated Backup v2 in met de volgende instellingen:

- Back-upschema: handleiding
- Volledige back-upfrequentie: Dagelijks
- Volledige starttijd voor back-up: 22:00 uur
- Full backup time window: 6 uur

Dit betekent dat de volgende beschikbare back-up venster is maandag om 22.00 uur voor 6 uur. Op dat moment maakt Automated Backup een back-up van uw databases, één voor één.

Dan, op dinsdag om 10 voor 6 uur, volledige back-ups van alle databases opnieuw beginnen.

> [!IMPORTANT]
> Bij het plannen van dagelijkse back-ups wordt aanbevolen dat u een breed tijdvenster plant om ervoor te zorgen dat er binnen deze tijd een back-up van alle databases kan worden gemaakt. Dit is vooral belangrijk in het geval dat u een grote hoeveelheid gegevens hebt om een back-up te maken.

## <a name="configure-new-vms"></a>Nieuwe VM's configureren

Gebruik de Azure-portal om Automated Backup v2 te configureren wanneer u een nieuwe SQL Server 2016 of 2017 Virtual Machine maakt in het implementatiemodel Resource Manager.

Selecteer op het tabblad **SQL Server-instellingen** de optie **Inschakelen** onder **Automatische back-up**. In de volgende Azure-portalschermafbeelding worden de SQL **Automated Backup-instellingen** weergegeven.

![SQL Automated Backup-configuratie in Azure-portal](./media/virtual-machines-windows-sql-automated-backup-v2/automated-backup-blade.png)

> [!NOTE]
> Geautomatiseerde back-up v2 is standaard uitgeschakeld.

## <a name="configure-existing-vms"></a>Bestaande VM's configureren

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Voor bestaande virtuele SQL Server-machines navigeert u naar de [SQL-bron voor virtuele machines](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource) en selecteert u **Back-ups** om uw geautomatiseerde back-ups te configureren.

![SQL Automated Backup voor bestaande VM's](./media/virtual-machines-windows-sql-automated-backup-v2/sql-server-configuration.png)


Als u klaar bent, klikt u op de knop **Toepassen** onder aan de **instellingenpagina Back-ups** om de wijzigingen op te slaan.

Als u voor het eerst Geautomatiseerde back-up inschakelt, configureert Azure de SQL Server IaaS-agent op de achtergrond. Gedurende deze periode wordt de Azure-portal mogelijk niet weergegeven dat Geautomatiseerde back-up is geconfigureerd. Wacht enkele minuten tot de agent is geïnstalleerd, geconfigureerd. Daarna geeft de Azure-portal de nieuwe instellingen weer.

## <a name="configure-with-powershell"></a>Configureren met PowerShell

U PowerShell gebruiken om Automated Backup v2 te configureren. Voordat u begint, moet u:

- [Download en installeer de nieuwste Azure PowerShell.](https://aka.ms/webpi-azps)
- Open Windows PowerShell en koppel het aan uw account met de opdracht **Connect-AzAccount.**

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

### <a name="install-the-sql-iaas-extension"></a>De SQL IaaS-extensie installeren
Als u een virtuele SQL Server-machine hebt ingericht vanuit de Azure-portal, moet de SQL Server IaaS-extensie al zijn geïnstalleerd. U bepalen of deze voor uw vm is geïnstalleerd door de opdracht **Get-AzVM** te bellen en de eigenschap **Extensies te** onderzoeken.

```powershell
$vmname = "vmname"
$resourcegroupname = "resourcegroupname"

(Get-AzVM -Name $vmname -ResourceGroupName $resourcegroupname).Extensions 
```

Als de SQL Server IaaS Agent-extensie is geïnstalleerd, moet u deze als "SqlIaaSAgent" of "SQLIaaSExtension" zien. **ProvisioningState** voor de uitbreiding moet ook "Geslaagd" weergeven. 

Als het niet is geïnstalleerd of niet is ingericht, u het installeren met de volgende opdracht. Naast de vm-naam en resourcegroep moet u ook de regio **($region)** opgeven waarin uw vm zich bevindt.

```powershell
$region = "EASTUS2"
Set-AzVMSqlServerExtension -VMName $vmname `
    -ResourceGroupName $resourcegroupname -Name "SQLIaasExtension" `
    -Version "2.0" -Location $region 
```

### <a name="verify-current-settings"></a><a id="verifysettings"></a>Huidige instellingen verifiëren
Als u tijdens de inrichting geautomatiseerde back-up hebt ingeschakeld, u PowerShell gebruiken om uw huidige configuratie te controleren. Voer de opdracht **Get-AzVMSqlServerExtension uit** en onderzoek de eigenschap **AutoBackupSettings:**

```powershell
(Get-AzVMSqlServerExtension -VMName $vmname -ResourceGroupName $resourcegroupname).AutoBackupSettings
```

U moet uitvoer krijgen die vergelijkbaar is met het volgende:

```
Enable                      : True
EnableEncryption            : False
RetentionPeriod             : 30
StorageUrl                  : https://test.blob.core.windows.net/
StorageAccessKey            :  
Password                    : 
BackupSystemDbs             : False
BackupScheduleType          : Manual
FullBackupFrequency         : WEEKLY
FullBackupStartTime         : 2
FullBackupWindowHours       : 2
LogBackupFrequency          : 60
```

Als uit uw uitvoer blijkt dat **Inschakelen** is ingesteld op **False,** moet u geautomatiseerde back-ups inschakelen. Het goede nieuws is dat u Automated Backup op dezelfde manier inschakelt en configureert. Zie de volgende sectie voor deze informatie.

> [!NOTE] 
> Als u de instellingen onmiddellijk na het aanbrengen van een wijziging controleert, is het mogelijk dat u de oude configuratiewaarden terugkrijgt. Wacht een paar minuten en controleer de instellingen opnieuw om ervoor te zorgen dat uw wijzigingen zijn toegepast.

### <a name="configure-automated-backup-v2"></a>Geautomatiseerde back-up v2 configureren
U PowerShell gebruiken om geautomatiseerde back-up in te schakelen en om de configuratie en het gedrag ervan op elk gewenst moment aan te passen. 

Selecteer eerst een opslagaccount voor de back-upbestanden of maak deze. Het volgende script selecteert een opslagaccount of maakt het als het niet bestaat.

```powershell
$storage_accountname = "yourstorageaccount"
$storage_resourcegroupname = $resourcegroupname

$storage = Get-AzStorageAccount -ResourceGroupName $resourcegroupname `
    -Name $storage_accountname -ErrorAction SilentlyContinue
If (-Not $storage)
    { $storage = New-AzStorageAccount -ResourceGroupName $storage_resourcegroupname `
    -Name $storage_accountname -SkuName Standard_GRS -Location $region } 
```

> [!NOTE]
> Automated Backup biedt geen ondersteuning voor het opslaan van back-ups in premium opslag, maar het kan back-ups maken van VM-schijven die Premium Storage gebruiken.

Gebruik vervolgens de opdracht **Nieuw-AzVMSqlServerAutoBackupConfig** om de instellingen voor geautomatiseerde back-upv2 in te schakelen en te configureren om back-ups op te slaan in het Azure-opslagaccount. In dit voorbeeld worden de back-ups 10 dagen bewaard. Back-ups van de systeemdatabase zijn ingeschakeld. Volledige back-ups zijn gepland voor wekelijksmet een tijdvenster vanaf 20:00 uur voor twee uur. Log back-ups zijn gepland voor elke 30 minuten. De tweede opdracht, **Set-AzVMSqlServerExtension,** werkt de opgegeven Azure VM bij met deze instellingen.

```powershell
$autobackupconfig = New-AzVMSqlServerAutoBackupConfig -Enable `
    -RetentionPeriodInDays 10 -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname -BackupSystemDbs `
    -BackupScheduleType Manual -FullBackupFrequency Weekly `
    -FullBackupStartHour 20 -FullBackupWindowInHours 2 `
    -LogBackupFrequencyInMinutes 30 

Set-AzVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname 
```

Het kan enkele minuten duren voordat de SQL Server IaaS-agent is geïnstalleerd en geconfigureerd. 

Als u versleuteling wilt inschakelen, wijzigt u het vorige script om de parameter **EnableEncryption** te passeren, samen met een wachtwoord (beveiligde tekenreeks) voor de parameter **CertificatePassword.** Met het volgende script worden de instellingen voor geautomatiseerde back-up in het vorige voorbeeld inschakelt en wordt versleuteling toegevoegd.

```powershell
$password = "P@ssw0rd"
$encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force  

$autobackupconfig = New-AzVMSqlServerAutoBackupConfig -Enable `
    -EnableEncryption -CertificatePassword $encryptionpassword `
    -RetentionPeriodInDays 10 -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname -BackupSystemDbs `
    -BackupScheduleType Manual -FullBackupFrequency Weekly `
    -FullBackupStartHour 20 -FullBackupWindowInHours 2 `
    -LogBackupFrequencyInMinutes 30 

Set-AzVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

Als u wilt bevestigen dat uw instellingen worden toegepast, [controleert u de configuratie Automatische back-up](#verifysettings).

### <a name="disable-automated-backup"></a>Automatische back-up uitschakelen
Als u Geautomatiseerde back-up wilt uitschakelen, voert u hetzelfde script uit zonder de parameter **-Inschakelen** voor de opdracht **Nieuw-AzVMSqlServerAutoBackConfig.** De afwezigheid van de parameter **-Inschakelen** signaleert de opdracht om de functie uit te schakelen. Net als bij de installatie kan het enkele minuten duren om geautomatiseerde back-up uit te schakelen.

```powershell
$autobackupconfig = New-AzVMSqlServerAutoBackupConfig -ResourceGroupName $storage_resourcegroupname

Set-AzVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

### <a name="example-script"></a>Voorbeeldscript
Het volgende script bevat een set variabelen die u aanpassen om geautomatiseerde back-up voor uw vm in te schakelen en te configureren. In uw geval moet u het script mogelijk aanpassen op basis van uw vereisten. U moet bijvoorbeeld wijzigingen aanbrengen als u de back-up van systeemdatabases wilt uitschakelen of versleuteling wilt inschakelen.

```powershell
$vmname = "yourvmname"
$resourcegroupname = "vmresourcegroupname"
$region = "Azure region name such as EASTUS2"
$storage_accountname = "storageaccountname"
$storage_resourcegroupname = $resourcegroupname
$retentionperiod = 10
$backupscheduletype = "Manual"
$fullbackupfrequency = "Weekly"
$fullbackupstarthour = "20"
$fullbackupwindow = "2"
$logbackupfrequency = "30"

# ResourceGroupName is the resource group which is hosting the VM where you are deploying the SQL IaaS Extension 

Set-AzVMSqlServerExtension -VMName $vmname `
    -ResourceGroupName $resourcegroupname -Name "SQLIaasExtension" `
    -Version "2.0" -Location $region

# Creates/use a storage account to store the backups

$storage = Get-AzStorageAccount -ResourceGroupName $resourcegroupname `
    -Name $storage_accountname -ErrorAction SilentlyContinue
If (-Not $storage)
    { $storage = New-AzStorageAccount -ResourceGroupName $storage_resourcegroupname `
    -Name $storage_accountname -SkuName Standard_GRS -Location $region }

# Configure Automated Backup settings

$autobackupconfig = New-AzVMSqlServerAutoBackupConfig -Enable `
    -RetentionPeriodInDays $retentionperiod -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname -BackupSystemDbs `
    -BackupScheduleType $backupscheduletype -FullBackupFrequency $fullbackupfrequency `
    -FullBackupStartHour $fullbackupstarthour -FullBackupWindowInHours $fullbackupwindow `
    -LogBackupFrequencyInMinutes $logbackupfrequency

# Apply the Automated Backup settings to the VM

Set-AzVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

## <a name="monitoring"></a>Bewaking

Als u Automated Backup op SQL Server 2016/2017 wilt controleren, hebt u twee belangrijke opties. Omdat Automated Backup de SQL Server Managed Backup-functie gebruikt, zijn dezelfde bewakingstechnieken op beide van toepassing.

Eerst u de status peilen door [msdb.managed_backup.sp_get_backup_diagnostics](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/managed-backup-sp-get-backup-diagnostics-transact-sql)te bellen. Of vraag de functie [msdb.managed_backup.fn_get_health_status](https://docs.microsoft.com/sql/relational-databases/system-functions/managed-backup-fn-get-health-status-transact-sql) tabelwaarde.

Een andere optie is om te profiteren van de ingebouwde Database Mail-functie voor meldingen.

1. Bel de opgeslagen [procedure msdb.managed_backup.sp_set_parameter](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/managed-backup-sp-set-parameter-transact-sql) om een e-mailadres toe te wijzen aan de parameter **SSMBackup2WANotificationEmailIds.** 
1. [SendGrid](../../../sendgrid-dotnet-how-to-send-email.md) inschakelen om de e-mails van de Azure VM te verzenden.
1. Gebruik de SMTP-server en gebruikersnaam om Database Mail te configureren. U Database Mail configureren in SQL Server Management Studio of met Transact-SQL-opdrachten. Zie [Databasemail](https://docs.microsoft.com/sql/relational-databases/database-mail/database-mail)voor meer informatie .
1. [Sql Server Agent configureren om Databasemail te gebruiken](https://docs.microsoft.com/sql/relational-databases/database-mail/configure-sql-server-agent-mail-to-use-database-mail).
1. Controleer of de SMTP-poort is toegestaan via de lokale VM-firewall en de netwerkbeveiligingsgroep voor de VM.

## <a name="next-steps"></a>Volgende stappen
Automated Backup v2 configureert Managed Backup op Azure VM's. Het is dus belangrijk om [de documentatie voor Managed Backup](https://msdn.microsoft.com/library/dn449496.aspx) te bekijken om het gedrag en de implicaties te begrijpen.

In het volgende artikel vindt u aanvullende back-up- en herstelrichtlijnen voor SQL Server in Azure VM's: [Back-up maken en herstellen voor SQL Server in Azure Virtual Machines.](virtual-machines-windows-sql-backup-recovery.md)

Zie [SQL Server IaaS Agent Extension](virtual-machines-windows-sql-server-agent-extension.md)voor informatie over andere beschikbare automatiseringstaken.

Zie [SQL Server op Azure Virtual Machines-overzicht](virtual-machines-windows-sql-server-iaas-overview.md)voor meer informatie over het uitvoeren van SQL Server op Azure VM's.

