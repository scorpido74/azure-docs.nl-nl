---
title: Geautomatiseerde back-up voor virtuele SQL Server-machines (klassiek) | Microsoft Documenten
description: 'Hiermee wordt de functie Geautomatiseerde back-up voor SQL Server uitgelegd die in Azure Virtual Machines wordt uitgevoerd met Resource Beheer. '
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75978100"
---
# <a name="automated-backup-for-sql-server-in-azure-virtual-machines-classic"></a>Geautomatiseerde back-up voor SQL Server in Azure Virtual Machines (Klassiek)
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-sql-automated-backup.md)
> * [Klassiek](../classic/sql-automated-backup.md)
> 
> 

Automated Backup configureert Managed Backup automatisch [naar Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx) voor alle bestaande en nieuwe databases op een Azure VM met SQL Server 2014 Standard of Enterprise. Hiermee u regelmatige databaseback-ups configureren die gebruikmaken van duurzame Azure blob-opslag. Automatische back-up is afhankelijk van de [SQL Server IaaS Agent Extension.](../classic/sql-server-agent-extension.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

> [!IMPORTANT] 
> Azure heeft twee verschillende implementatiemodellen voor het maken en werken met resources: [Resource Manager en Classic.](../../../azure-resource-manager/management/deployment-models.md) In dit artikel wordt het implementatiemodel Classic gebruikt. U doet er verstandig aan voor de meeste nieuwe implementaties het Resource Manager-model te gebruiken. Zie [Geautomatiseerde back-up voor SQL Server in Azure Virtual Machines Resource Manager](../sql/virtual-machines-windows-sql-automated-backup.md)als u de resourcebeheerversie van dit artikel wilt weergeven.

## <a name="prerequisites"></a>Vereisten
Als u Geautomatiseerde back-up wilt gebruiken, moet u rekening houden met de volgende vereisten:

**Besturingssysteem**:

* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

**SQL Server-versie/-editie**:

* SQL Server 2014 Standard
* SQL Server 2014 Enterprise

> [!NOTE]
> Geautomatiseerde back-up voor SQL Server 2016 wordt ondersteund met virtuele machines van Resource Manager. Zie [Automated Backup v2 voor SQL Server 2016 Azure Virtual Machines (Resource Manager) voor](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-automated-backup-v2)meer informatie.

**Databaseconfiguratie:**

* Doeldatabases moeten het volledige herstelmodel gebruiken.

**Azure PowerShell**:

* [Installeer de nieuwste Azure PowerShell-opdrachten.](/powershell/azure/overview)

**SQL Server IaaS-extensie:**

* [Installeer de SQL Server IaaS-extensie](../classic/sql-server-agent-extension.md).

## <a name="settings"></a>Instellingen
In de volgende tabel worden de opties beschreven die kunnen worden geconfigureerd voor geautomatiseerde back-up. Voor klassieke VM's moet u PowerShell gebruiken om deze instellingen te configureren.

| Instelling | Bereik (standaard) | Beschrijving |
| --- | --- | --- |
| **Geautomatiseerde back-up** |Inschakelen/uitschakelen (uitgeschakeld) |Hiermee schakelt u Geautomatiseerde back-up in of uit voor een Azure VM met SQL Server 2014 Standard of Enterprise. |
| **Bewaarperiode** |1-30 dagen (30 dagen) |Het aantal dagen om een back-up te behouden. |
| **Opslagaccount** |Azure-opslagaccount (het opslagaccount dat is gemaakt voor de opgegeven vm) |Een Azure-opslagaccount dat moet worden gebruikt voor het opslaan van geautomatiseerde back-upbestanden in blob-opslag. Op deze locatie wordt een container gemaakt om alle back-upbestanden op te slaan. De naam van het back-upbestand bevat de datum, tijd en de naam van de machine. |
| **Codering** |Inschakelen/uitschakelen (uitgeschakeld) |Hiermee schakelt u versleuteling in of schakelt deze uit. Wanneer versleuteling is ingeschakeld, bevinden de certificaten die worden gebruikt om de back-up te herstellen zich in het opgegeven opslagaccount in dezelfde automatisch back-upcontainer met dezelfde naamgevingsconventie. Als het wachtwoord wordt gewijzigd, wordt een nieuw certificaat gegenereerd met dat wachtwoord, maar het oude certificaat blijft om eerdere back-ups te herstellen. |
| **Wachtwoord** |Wachtwoordtekst (Geen) |Een wachtwoord voor versleutelingssleutels. Dit is alleen vereist als versleuteling is ingeschakeld. Als u een versleutelde back-up wilt herstellen, moet u het juiste wachtwoord en bijbehorende certificaat hebben dat is gebruikt op het moment dat de back-up werd genomen. |
| **Back-up maken van systeemdatabases** | Inschakelen/uitschakelen (uitgeschakeld) | Maak volledige back-ups van Master, Model en MSDB |
| **Back-upschema configureren** | Handleiding/geautomatiseerd (geautomatiseerd) | Selecteer **Geautomatiseerd** om automatisch volledige back-ups te nemen en back-ups in te loggen op basis van logboekgroei. Selecteer **Handmatig** om het schema voor volledige en logboekback-ups op te geven. |

## <a name="configuration-with-powershell"></a>Configuratie met PowerShell
In het volgende PowerShell-voorbeeld is Geautomatiseerde back-up geconfigureerd voor een bestaande SQL Server 2014 VM. Met de opdracht **Nieuw-AzureVMSqlServerAutoBackupConfig** configureert u de instellingen voor geautomatiseerde back-ups om back-ups op te slaan in het Azure-opslagaccount dat is opgegeven door de variabele $storageaccount. Deze back-ups worden gedurende 10 dagen bewaard. Met de opdracht **Set-AzureVMSqlServerExtension** wordt de opgegeven Azure VM met deze instellingen bijgewerkt.

    $storageaccount = "<storageaccountname>"
    $storageaccountkey = (Get-AzureStorageKey -StorageAccountName $storageaccount).Primary
    $storagecontext = New-AzureStorageContext -StorageAccountName $storageaccount -StorageAccountKey $storageaccountkey
    $autobackupconfig = New-AzureVMSqlServerAutoBackupConfig -StorageContext $storagecontext -Enable -RetentionPeriod 10

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoBackupSettings $autobackupconfig | Update-AzureVM

Het kan enkele minuten duren voordat de SQL Server IaaS-agent is geïnstalleerd en geconfigureerd.

Als u versleuteling wilt inschakelen, wijzigt u het vorige script om de parameter EnableEncryption te passeren, samen met een wachtwoord (beveiligde tekenreeks) voor de parameter CertificatePassword. Met het volgende script worden de instellingen voor geautomatiseerde back-up in het vorige voorbeeld inschakelt en wordt versleuteling toegevoegd.

    $storageaccount = "<storageaccountname>"
    $storageaccountkey = (Get-AzureStorageKey -StorageAccountName $storageaccount).Primary
    $storagecontext = New-AzureStorageContext -StorageAccountName $storageaccount -StorageAccountKey $storageaccountkey
    $password = "P@ssw0rd"
    $encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force  
    $autobackupconfig = New-AzureVMSqlServerAutoBackupConfig -StorageContext $storagecontext -Enable -RetentionPeriod 10 -EnableEncryption -CertificatePassword $encryptionpassword

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoBackupSettings $autobackupconfig | Update-AzureVM

Als u automatische back-up wilt uitschakelen, voert u hetzelfde script uit zonder de parameter **-Inschakelen** naar de **nieuw-AzureVMSqlServerAutoBackupConfig**. Net als bij de installatie kan het enkele minuten duren om geautomatiseerde back-up uit te schakelen.

> [!NOTE]
> Als u de SQL Server IaaS-agent uitschakelt en verwijdert, worden de eerder geconfigureerde beheerde back-upinstellingen niet verwijderd. U moet Geautomatiseerde back-up uitschakelen voordat u de SQL Server IaaS-agent uitschakelt of ontzegt.
> 
> 

## <a name="next-steps"></a>Volgende stappen
Automated Backup configureert Managed Backup op Azure VM's. Het is dus belangrijk om [de documentatie voor Managed Backup](https://msdn.microsoft.com/library/dn449496.aspx) te bekijken om het gedrag en de implicaties te begrijpen.

In het volgende onderwerp vindt u aanvullende back-up- en herstelrichtlijnen voor SQL Server in Azure VM's: [Back-up maken en herstellen voor SQL Server in Azure Virtual Machines.](../sql/virtual-machines-windows-sql-backup-recovery.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fsqlclassic%2ftoc.json)

Zie [SQL Server IaaS Agent Extension](../classic/sql-server-agent-extension.md)voor informatie over andere beschikbare automatiseringstaken.

Zie [SQL Server op Azure Virtual Machines-overzicht](../sql/virtual-machines-windows-sql-server-iaas-overview.md)voor meer informatie over het uitvoeren van SQL Server op Azure VM's.

