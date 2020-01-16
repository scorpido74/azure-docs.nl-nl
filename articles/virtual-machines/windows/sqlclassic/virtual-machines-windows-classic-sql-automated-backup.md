---
title: Automatische back-up voor SQL Server Virtual Machines (klassiek) | Microsoft Docs
description: 'Hierin wordt de functie voor automatische back-ups beschreven voor SQL Server die worden uitgevoerd in azure Virtual Machines met behulp van Resource Manager. '
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 3333e830-8a60-42f5-9f44-8e02e9868d7b
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/23/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 43ff230d4769a23c9007b3da29858d2105366f9f
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75978100"
---
# <a name="automated-backup-for-sql-server-in-azure-virtual-machines-classic"></a>Automatische back-up voor SQL Server in azure Virtual Machines (klassiek)
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-sql-automated-backup.md)
> * [Klassiek](../classic/sql-automated-backup.md)
> 
> 

Automatische back-up configureert automatisch [beheerde back-ups voor Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx) voor alle bestaande en nieuwe data bases op een virtuele Azure-machine met SQL Server 2014 Standard of ENTER prise. Zo kunt u regel matige back-ups van de data base configureren die gebruikmaken van duurzame Azure Blob-opslag. Automatische back-up is afhankelijk van de [uitbrei ding IaaS agent van SQL Server](../classic/sql-server-agent-extension.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

> [!IMPORTANT] 
> Azure heeft twee verschillende implementatie modellen voor het maken van en werken met resources: [Resource Manager en klassiek](../../../azure-resource-manager/management/deployment-models.md). In dit artikel wordt beschreven hoe u het klassieke implementatie model gebruikt. U doet er verstandig aan voor de meeste nieuwe implementaties het Resource Manager-model te gebruiken. Als u de Resource Manager-versie van dit artikel wilt bekijken, raadpleegt u [automatische back-up voor SQL Server in Azure virtual machines Resource Manager](../sql/virtual-machines-windows-sql-automated-backup.md).

## <a name="prerequisites"></a>Vereisten
Als u automatische back-ups wilt gebruiken, moet u rekening houden met de volgende vereisten:

**Besturings systeem**:

* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

**SQL Server versie/editie**:

* SQL Server 2014 Standard
* SQL Server 2014 Enterprise

> [!NOTE]
> Automatische back-up voor SQL Server 2016 wordt ondersteund bij virtuele machines van Resource Manager. Zie voor meer informatie [automatische back-up v2 voor SQL Server 2016 Azure virtual machines (Resource Manager)](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-automated-backup-v2).

**Database configuratie**:

* Doel databases moeten het volledige herstel model gebruiken.

**Azure PowerShell**:

* [Installeer de meest recente Azure PowerShell-opdrachten](/powershell/azure/overview).

**IaaS-extensie SQL Server**:

* [Installeer de SQL Server IaaS-extensie](../classic/sql-server-agent-extension.md).

## <a name="settings"></a>Instellingen
In de volgende tabel worden de opties beschreven die kunnen worden geconfigureerd voor automatische back-ups. Voor klassieke Vm's moet u Power shell gebruiken om deze instellingen te configureren.

| Instelling | Bereik (standaard) | Beschrijving |
| --- | --- | --- |
| **Automatische back-up** |Inschakelen/uitschakelen (uitgeschakeld) |Hiermee wordt automatische back-ups voor een Azure-VM met SQL Server 2014 Standard of ENTER prise in-of uitgeschakeld. |
| **Bewaar periode** |1-30 dagen (30 dagen) |Het aantal dagen dat een back-up moet worden bewaard. |
| **Opslagaccount** |Azure Storage-account (het opslag account dat is gemaakt voor de opgegeven VM) |Een Azure-opslag account dat moet worden gebruikt voor het opslaan van automatische back-upbestanden in Blob Storage. Er wordt een container gemaakt op deze locatie om alle back-upbestanden op te slaan. De naamgevings regels voor back-ups bevatten de datum, de tijd en de machine naam. |
| **Versleuteling** |Inschakelen/uitschakelen (uitgeschakeld) |Hiermee wordt versleuteling in-of uitgeschakeld. Als versleuteling is ingeschakeld, bevinden de certificaten die worden gebruikt voor het herstellen van de back-up zich in het opgegeven opslag account in dezelfde automaticbackup-container met dezelfde naam Conventie. Als het wacht woord wordt gewijzigd, wordt er een nieuw certificaat met dat wacht woord gegenereerd, maar blijft het oude certificaat voor het herstellen van eerdere back-ups. |
| **Wachtwoord** |Wachtwoord tekst (geen) |Een wacht woord voor versleutelings sleutels. Dit is alleen vereist als versleuteling is ingeschakeld. Als u een versleutelde back-up wilt herstellen, moet u het juiste wacht woord en het bijbehorende certificaat hebben dat is gebruikt op het moment dat de back-up werd gemaakt. |
| **Back-up van systeem databases** | Inschakelen/uitschakelen (uitgeschakeld) | Volledige back-ups maken van Master, model en MSDB |
| **Back-upschema configureren** | Hand matig/automatisch (automatisch) | Selecteer automatisch om volledige en logboek back-ups automatisch te **maken op basis** van de logboek groei. Selecteer **hand matig** om de planning op te geven voor volledige en logboek back-ups. |

## <a name="configuration-with-powershell"></a>Configuratie met Power shell
In het volgende Power shell-voor beeld wordt automatische back-up geconfigureerd voor een bestaande SQL Server 2014 VM. De opdracht **New-AzureVMSqlServerAutoBackupConfig** configureert de automatische back-upinstellingen voor het opslaan van back-ups in het Azure-opslag account dat is opgegeven door de $storageaccount variabele. Deze back-ups worden 10 dagen bewaard. Met de opdracht **set-AzureVMSqlServerExtension** wordt de opgegeven virtuele machine van Azure met deze instellingen bijgewerkt.

    $storageaccount = "<storageaccountname>"
    $storageaccountkey = (Get-AzureStorageKey -StorageAccountName $storageaccount).Primary
    $storagecontext = New-AzureStorageContext -StorageAccountName $storageaccount -StorageAccountKey $storageaccountkey
    $autobackupconfig = New-AzureVMSqlServerAutoBackupConfig -StorageContext $storagecontext -Enable -RetentionPeriod 10

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoBackupSettings $autobackupconfig | Update-AzureVM

Het kan enkele minuten duren om de SQL Server IaaS-agent te installeren en configureren.

Als u versleuteling wilt inschakelen, wijzigt u het vorige script om de para meter EnableEncryption samen met een wacht woord (beveiligde teken reeks) door te geven voor de para meter CertificatePassword. Met het volgende script kunt u de automatische back-upinstellingen in het vorige voor beeld inschakelen en versleuteling toevoegen.

    $storageaccount = "<storageaccountname>"
    $storageaccountkey = (Get-AzureStorageKey -StorageAccountName $storageaccount).Primary
    $storagecontext = New-AzureStorageContext -StorageAccountName $storageaccount -StorageAccountKey $storageaccountkey
    $password = "P@ssw0rd"
    $encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force  
    $autobackupconfig = New-AzureVMSqlServerAutoBackupConfig -StorageContext $storagecontext -Enable -RetentionPeriod 10 -EnableEncryption -CertificatePassword $encryptionpassword

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoBackupSettings $autobackupconfig | Update-AzureVM

Als u automatische back-ups wilt uitschakelen, voert u hetzelfde script uit zonder de para meter **-Enable** voor de **New-AzureVMSqlServerAutoBackupConfig**. Net als bij de installatie kan het enkele minuten duren om automatische back-ups uit te scha kelen.

> [!NOTE]
> Als u de SQL Server IaaS-agent uitschakelt en verwijdert, worden de eerder geconfigureerde beheerde back-upinstellingen niet verwijderd. U moet automatische back-up uitschakelen voordat u de SQL Server IaaS-agent uitschakelt of verwijdert.
> 
> 

## <a name="next-steps"></a>Volgende stappen
Met geautomatiseerde back-up wordt beheerde back-up geconfigureerd op virtuele machines van Azure. Het is dus belang rijk dat u [de documentatie voor beheerde back-up bekijkt](https://msdn.microsoft.com/library/dn449496.aspx) om het gedrag en de implicaties ervan te begrijpen.

Meer richt lijnen voor back-up en herstel van SQL Server op virtuele Azure-machines vindt u in het volgende onderwerp: [back-up maken en herstellen voor SQL Server in azure virtual machines](../sql/virtual-machines-windows-sql-backup-recovery.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fsqlclassic%2ftoc.json).

Zie [SQL Server IaaS agent extension](../classic/sql-server-agent-extension.md)(Engelstalig) voor meer informatie over andere beschik bare automatiserings taken.

Zie [SQL Server op azure virtual machines Overview](../sql/virtual-machines-windows-sql-server-iaas-overview.md)voor meer informatie over het uitvoeren van SQL Server op virtuele machines in Azure.

