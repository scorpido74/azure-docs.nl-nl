---
title: Geautomatiseerde back-up voor SQL Server 2014 Azure Virtual Machines
description: Hiermee wordt de functie Geautomatiseerde back-up voor SQL Server 2014 VM's uitgelegd die in Azure worden uitgevoerd. Dit artikel is specifiek voor VM's met behulp van Resource Manager.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.assetid: bdc63fd1-db49-4e76-87d5-b5c6a890e53c
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/03/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: c7dea85d8de17a0f65e6e73b5b5fbe619d464d3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77650323"
---
# <a name="automated-backup-for-sql-server-2014-virtual-machines-resource-manager"></a>Geautomatiseerde back-up voor virtuele machines van SQL Server 2014 (Resource Manager)

> [!div class="op_single_selector"]
> * [SQL Server 2014](virtual-machines-windows-sql-automated-backup.md)
> * [SQL Server 2016/2017](virtual-machines-windows-sql-automated-backup-v2.md)

Automated Backup configureert Managed Backup automatisch [naar Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx) voor alle bestaande en nieuwe databases op een Azure VM met SQL Server 2014 Standard of Enterprise. Hiermee u regelmatige databaseback-ups configureren die gebruikmaken van duurzame Azure blob-opslag. Automatische back-up is afhankelijk van de [SQL Server IaaS Agent Extension.](virtual-machines-windows-sql-server-agent-extension.md)

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="prerequisites"></a>Vereisten
Als u Geautomatiseerde back-up wilt gebruiken, moet u rekening houden met de volgende vereisten:

**Besturingssysteem**:

- Windows Server 2012
- Windows Server 2012 R2
- Windows Server 2016

**SQL Server-versie/-editie**:

- SQL Server 2014 Standard
- SQL Server 2014 Enterprise

> [!IMPORTANT]
> Automated Backup werkt met SQL Server 2014. Als u SQL Server 2016/2017 gebruikt, u Automated Backup v2 gebruiken om een back-up van uw databases te maken. Zie [Automated Backup v2 voor SQL Server 2016 Azure Virtual Machines voor](virtual-machines-windows-sql-automated-backup-v2.md)meer informatie.

**Databaseconfiguratie:**

- Doeldatabases moeten het volledige herstelmodel gebruiken. Zie [Back-up onder het volledige herstelmodel voor](https://technet.microsoft.com/library/ms190217.aspx)meer informatie over de impact van het volledige herstelmodel op back-ups.
- Doeldatabases moeten zich op de standaard SQL Server-instantie bevindt. De SQL Server IaaS-extensie biedt geen ondersteuning voor benoemde exemplaren.

> [!NOTE]
> Automated Backup is gebaseerd op de SQL Server IaaS Agent Extension. Huidige SQL-galerieafbeeldingen met virtuele machine voegen deze extensie standaard toe. Zie [SQL Server IaaS Agent Extension](virtual-machines-windows-sql-server-agent-extension.md)voor meer informatie.

## <a name="settings"></a>Instellingen

In de volgende tabel worden de opties beschreven die kunnen worden geconfigureerd voor geautomatiseerde back-up. De werkelijke configuratiestappen zijn afhankelijk van of u de Azure-portal of Azure Windows PowerShell-opdrachten gebruikt.

| Instelling | Bereik (standaard) | Beschrijving |
| --- | --- | --- |
| **Geautomatiseerde back-up** | Inschakelen/uitschakelen (uitgeschakeld) | Hiermee schakelt u Geautomatiseerde back-up in of uit voor een Azure VM met SQL Server 2014 Standard of Enterprise. |
| **Bewaarperiode** | 1-30 dagen (30 dagen) | Het aantal dagen om een back-up te behouden. |
| **Opslagaccount** | Azure Storage-account | Een Azure-opslagaccount dat moet worden gebruikt voor het opslaan van geautomatiseerde back-upbestanden in blob-opslag. Op deze locatie wordt een container gemaakt om alle back-upbestanden op te slaan. De naam van het back-upbestand bevat de datum, tijd en de naam van de machine. |
| **Codering** | Inschakelen/uitschakelen (uitgeschakeld) | Hiermee schakelt u versleuteling in of schakelt deze uit. Wanneer versleuteling is ingeschakeld, bevinden de certificaten die worden gebruikt om `automaticbackup` de back-up te herstellen zich in het opgegeven opslagaccount in dezelfde container met dezelfde naamgevingsconventie. Als het wachtwoord wordt gewijzigd, wordt een nieuw certificaat gegenereerd met dat wachtwoord, maar het oude certificaat blijft om eerdere back-ups te herstellen. |
| **Wachtwoord** | Wachtwoordtekst | Een wachtwoord voor versleutelingssleutels. Dit is alleen vereist als versleuteling is ingeschakeld. Als u een versleutelde back-up wilt herstellen, moet u het juiste wachtwoord en bijbehorende certificaat hebben dat is gebruikt op het moment dat de back-up werd genomen. |


## <a name="configure-new-vms"></a>Nieuwe VM's configureren

Gebruik de Azure-portal om geautomatiseerde back-up te configureren wanneer u een nieuwe SQL Server 2014 Virtual Machine maakt in het implementatiemodel voor Resource Beheer.

Schuif op het tabblad **SQL Server-instellingen** omlaag naar **Automatische back-up** en selecteer **Inschakelen**. In de volgende Azure-portalschermafbeelding worden de SQL **Automated Backup-instellingen** weergegeven.

![SQL Automated Backup-configuratie in Azure-portal](./media/virtual-machines-windows-sql-automated-backup/azure-sql-arm-autobackup.png)

## <a name="configure-existing-vms"></a>Bestaande VM's configureren

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Voor bestaande virtuele SQL Server-machines u geautomatiseerde back-ups in- en uitschakelen, de bewaarperiode wijzigen, het opslagaccount opgeven en versleuteling vanuit de Azure-portal inschakelen. 

Navigeer naar de [SQL virtual machines-bron](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource) voor uw virtuele SQL Server 2014-machine en selecteer **Vervolgens Back-ups**. 

![SQL Automated Backup voor bestaande VM's](./media/virtual-machines-windows-sql-automated-backup/azure-sql-rm-autobackup-existing-vms.png)

Als u klaar bent, selecteert u de knop **Toepassen** onder aan de pagina **Back-ups** om de wijzigingen op te slaan.

Als u voor het eerst Geautomatiseerde back-up inschakelt, configureert Azure de SQL Server IaaS-agent op de achtergrond. Gedurende deze periode wordt de Azure-portal mogelijk niet weergegeven dat Geautomatiseerde back-up is geconfigureerd. Wacht enkele minuten tot de agent is geïnstalleerd en geconfigureerd. Daarna geeft de Azure-portal de nieuwe instellingen weer.

> [!NOTE]
> U ook geautomatiseerde back-up configureren met behulp van een sjabloon. Zie [Azure quickstart-sjabloon voor Geautomatiseerde back-up voor](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-autobackup-update)meer informatie.

## <a name="configure-with-powershell"></a>Configureren met PowerShell

U PowerShell gebruiken om geautomatiseerde back-up te configureren. Voordat u begint, moet u:

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

Als het niet is geïnstalleerd of niet is ingericht, u het installeren met de volgende opdracht. Naast de vm-naam en resourcegroep moet u ook de regio **($region)** opgeven waarin uw vm zich bevindt. Geef het licentietype voor uw SQL Server VM op en kies tussen betalen per gebruik of bring-your-own-license via het [Azure Hybrid Benefit.](https://azure.microsoft.com/pricing/hybrid-benefit/) Zie [licentiemodel voor](virtual-machines-windows-sql-ahb.md)meer informatie over licenties. 

```powershell
New-AzSqlVM  -Name $vmname `
    -ResourceGroupName $resourcegroupname `
    -Location $region -LicenseType <PAYG/AHUB>
```

> [!IMPORTANT]
> Als de extensie nog niet is geïnstalleerd, wordt de SQL Server-service opnieuw gestart door de extensie opnieuw te installeren.

### <a name="verify-current-settings"></a><a id="verifysettings"></a>Huidige instellingen verifiëren

Als u tijdens de inrichting geautomatiseerde back-up hebt ingeschakeld, u PowerShell gebruiken om uw huidige configuratie te controleren. Voer de opdracht **Get-AzVMSqlServerExtension uit** en onderzoek de eigenschap **AutoBackupSettings:**

```powershell
(Get-AzVMSqlServerExtension -VMName $vmname -ResourceGroupName $resourcegroupname).AutoBackupSettings
```

U moet uitvoer krijgen die vergelijkbaar is met het volgende:

```
Enable                      : False
EnableEncryption            : False
RetentionPeriod             : -1
StorageUrl                  : NOTSET
StorageAccessKey            : 
Password                    : 
BackupSystemDbs             : False
BackupScheduleType          : 
FullBackupFrequency         : 
FullBackupStartTime         : 
FullBackupWindowHours       : 
LogBackupFrequency          : 
```

Als uit uw uitvoer blijkt dat **Inschakelen** is ingesteld op **False,** moet u geautomatiseerde back-ups inschakelen. Het goede nieuws is dat u Automated Backup op dezelfde manier inschakelt en configureert. Zie de volgende sectie voor deze informatie.

> [!NOTE] 
> Als u de instellingen onmiddellijk na het aanbrengen van een wijziging controleert, is het mogelijk dat u de oude configuratiewaarden terugkrijgt. Wacht een paar minuten en controleer de instellingen opnieuw om ervoor te zorgen dat uw wijzigingen zijn toegepast.

### <a name="configure-automated-backup"></a>Geautomatiseerde back-up configureren
U PowerShell gebruiken om geautomatiseerde back-up in te schakelen en om de configuratie en het gedrag ervan op elk gewenst moment aan te passen.

Selecteer of maak eerst een opslagaccount voor de back-upbestanden. Het volgende script selecteert een opslagaccount of maakt het als het niet bestaat.

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

Gebruik vervolgens de opdracht **Nieuw-AzVMSqlServerAutoBackupConfig** om de instellingen voor geautomatiseerde back-ups in te schakelen en te configureren om back-ups op te slaan in het Azure-opslagaccount. In dit voorbeeld worden de back-ups gedurende 10 dagen bewaard. De tweede opdracht, **Set-AzVMSqlServerExtension,** werkt de opgegeven Azure VM bij met deze instellingen.

```powershell
$autobackupconfig = New-AzVMSqlServerAutoBackupConfig -Enable `
    -RetentionPeriodInDays 10 -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname

Set-AzVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

Het kan enkele minuten duren voordat de SQL Server IaaS-agent is geïnstalleerd en geconfigureerd.

> [!NOTE]
> Er zijn andere instellingen voor **New-AzVMSqlServerAutoBackupConfig** die alleen van toepassing zijn op SQL Server 2016 en Automated Backup v2. SQL Server 2014 ondersteunt niet de volgende instellingen: **BackupSystemDbs**, **BackupScheduleType**, **FullBackupFrequency**, **FullBackupStartHour**, **FullBackupWindowInHours**en **LogBackupFrequencyInMinutes**. Als u probeert deze instellingen te configureren op een virtuele SQL Server 2014-machine, is er geen fout, maar worden de instellingen niet toegepast. Zie [Automated Backup v2 voor SQL Server 2016 Azure Virtual Machines](virtual-machines-windows-sql-automated-backup-v2.md)als u deze instellingen wilt gebruiken op een virtuele sql server 2016.

Als u versleuteling wilt inschakelen, wijzigt u het vorige script om de parameter **EnableEncryption** te passeren, samen met een wachtwoord (beveiligde tekenreeks) voor de parameter **CertificatePassword.** Met het volgende script worden de instellingen voor geautomatiseerde back-up in het vorige voorbeeld inschakelt en wordt versleuteling toegevoegd.

```powershell
$password = "P@ssw0rd"
$encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force

$autobackupconfig = New-AzVMSqlServerAutoBackupConfig -Enable `
    -EnableEncryption -CertificatePassword $encryptionpassword `
    -RetentionPeriodInDays 10 -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname

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
    -ResourceGroupName $storage_resourcegroupname

# Apply the Automated Backup settings to the VM

Set-AzVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

## <a name="monitoring"></a>Bewaking

Als u Geautomatiseerde back-up op SQL Server 2014 wilt controleren, hebt u twee belangrijke opties. Omdat Automated Backup de SQL Server Managed Backup-functie gebruikt, zijn dezelfde bewakingstechnieken op beide van toepassing.

Eerst u de status peilen door [msdb.smart_admin.sp_get_backup_diagnostics](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/managed-backup-sp-get-backup-diagnostics-transact-sql)te bellen. Of query de [msdb.smart_admin.fn_get_health_status](https://docs.microsoft.com/sql/relational-databases/system-functions/managed-backup-fn-get-health-status-transact-sql) tabel gewaardeerd functie.

> [!NOTE]
> Het schema voor Managed Backup in SQL Server 2014 is **msdb.smart_admin**. In SQL Server 2016 is dit gewijzigd in **msdb.managed_backup**en gebruiken de referentieonderwerpen dit nieuwere schema. Maar voor SQL Server 2014 moet u het **smart_admin-schema** blijven gebruiken voor alle beheerde back-upobjecten.

Een andere optie is om te profiteren van de ingebouwde Database Mail-functie voor meldingen.

1. Bel de opgeslagen [procedure msdb.smart_admin.sp_set_parameter](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/managed-backup-sp-set-parameter-transact-sql) om een e-mailadres toe te wijzen aan de parameter **SSMBackup2WANotificationEmailIds.** 
1. [SendGrid](../../../sendgrid-dotnet-how-to-send-email.md) inschakelen om de e-mails van de Azure VM te verzenden.
1. Gebruik de SMTP-server en gebruikersnaam om Database Mail te configureren. U Database Mail configureren in SQL Server Management Studio of met Transact-SQL-opdrachten. Zie [Databasemail](https://docs.microsoft.com/sql/relational-databases/database-mail/database-mail)voor meer informatie .
1. [Sql Server Agent configureren om Databasemail te gebruiken](https://docs.microsoft.com/sql/relational-databases/database-mail/configure-sql-server-agent-mail-to-use-database-mail).
1. Controleer of de SMTP-poort is toegestaan via de lokale VM-firewall en de netwerkbeveiligingsgroep voor de VM.

## <a name="next-steps"></a>Volgende stappen

Automated Backup configureert Managed Backup op Azure VM's. Het is dus belangrijk om [de documentatie voor Managed Backup op SQL Server 2014](https://msdn.microsoft.com/library/dn449497(v=sql.120).aspx)te bekijken.

In het volgende artikel vindt u aanvullende back-up- en herstelrichtlijnen voor SQL Server in Azure VM's: [Back-up maken en herstellen voor SQL Server in Azure Virtual Machines.](virtual-machines-windows-sql-backup-recovery.md)

Zie [SQL Server IaaS Agent Extension](virtual-machines-windows-sql-server-agent-extension.md)voor informatie over andere beschikbare automatiseringstaken.

Zie [SQL Server op Azure Virtual Machines-overzicht](virtual-machines-windows-sql-server-iaas-overview.md)voor meer informatie over het uitvoeren van SQL Server op Azure VM's.
