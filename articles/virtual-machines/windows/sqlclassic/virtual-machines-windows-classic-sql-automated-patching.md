---
title: Automatische patching voor SQL Server Vm's (klassiek) | Microsoft Docs
description: In dit onderwerp wordt de functie voor automatische patching voor SQL Server Virtual Machines uitgevoerd in azure met behulp van de klassieke implementatie modus.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 737b2f65-08b9-4f54-b867-e987730265a8
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/07/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: efc6d0c25c5186b391deb08ee0e41dcb8ae6edf0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75978088"
---
# <a name="automated-patching-for-sql-server-in-azure-virtual-machines-classic"></a>Automatische patching voor SQL Server in azure Virtual Machines (klassiek)
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-sql-automated-patching.md)
> * [Klassiek](../classic/sql-automated-patching.md)
> 
> 

Met Geautomatiseerd patchen wordt een onderhoudsvenster tot stand gebracht voor een virtuele Azure-machine waarop SQL Server wordt uitgevoerd. Geautomatiseerde updates kunnen alleen worden geïnstalleerd tijdens dit onderhoudsvenster. Voor SQL Server zorgt dit ervoor dat systeem updates en eventuele bijbehorende herstarts op de best mogelijke tijd worden uitgevoerd voor de data base. 

> [!IMPORTANT]
> Er worden alleen Windows-updates geïnstalleerd die zijn gemarkeerd als **Belangrijk**. Andere SQL Server-updates, zoals cumulatieve updates, moeten handmatig worden geïnstalleerd. 

Geautomatiseerd patchen is afhankelijk van de [extensie voor de SQL Server IaaS-agent](../classic/sql-server-agent-extension.md).

> [!IMPORTANT] 
> Azure heeft twee verschillende implementatie modellen voor het maken van en werken met resources: [Resource Manager en klassiek](../../../azure-resource-manager/management/deployment-models.md). In dit artikel wordt beschreven hoe u het klassieke implementatie model gebruikt. U doet er verstandig aan voor de meeste nieuwe implementaties het Resource Manager-model te gebruiken. Als u de Resource Manager-versie van dit artikel wilt bekijken, raadpleegt u [automatische patching voor SQL Server in Azure virtual machines Resource Manager](../sql/virtual-machines-windows-sql-automated-patching.md).

## <a name="prerequisites"></a>Vereisten
Als u automatische patching wilt gebruiken, moet u rekening houden met de volgende vereisten:

**Besturings systeem**:

* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

**SQL Server versie**:

* SQL Server 2012
* SQL Server 2014
* SQL Server 2016

**Azure PowerShell**:

* [Installeer de meest recente Azure PowerShell-opdrachten](/powershell/azure/overview).

**IaaS-extensie SQL Server**:

* [Installeer de SQL Server IaaS-extensie](../classic/sql-server-agent-extension.md).

## <a name="settings"></a>Instellingen
In de volgende tabel worden de opties beschreven die kunnen worden geconfigureerd voor automatische patching. Voor klassieke Vm's moet u Power shell gebruiken om deze instellingen te configureren.

| Instelling | Mogelijke waarden | Beschrijving |
| --- | --- | --- |
| **Automatische patching** |Inschakelen/uitschakelen (uitgeschakeld) |Hiermee schakelt u automatische patching voor een virtuele machine van Azure in of uit. |
| **Onderhoudsplanning** |Dagelijks, maandag, dinsdag, woensdag, donderdag, vrijdag, zaterdag, zondag |Het schema voor het downloaden en installeren van Windows-, SQL Server-en micro soft-updates voor uw virtuele machine. |
| **Start-uur onderhoud** |0-24 |De lokale start tijd voor het bijwerken van de virtuele machine. |
| **Duur van onderhouds venster** |30-180 |Het aantal minuten dat het downloaden en installeren van updates is voltooid. |
| **Patch categorie** |Belangrijk |De categorie van updates die moeten worden gedownload en geïnstalleerd. |

## <a name="configuration-with-powershell"></a>Configuratie met Power shell
In het volgende voor beeld wordt Power shell gebruikt voor het configureren van automatische patches op een bestaande SQL Server VM. Met de opdracht **New-AzureVMSqlServerAutoPatchingConfig** wordt een nieuwe onderhouds venster voor automatische updates geconfigureerd.

    $aps = New-AzureVMSqlServerAutoPatchingConfig -Enable -DayOfWeek "Thursday" -MaintenanceWindowStartingHour 11 -MaintenanceWindowDuration 120  -PatchCategory "Important"

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoPatchingSettings $aps | Update-AzureVM

Op basis van dit voor beeld wordt in de volgende tabel het praktische effect van de Azure-doel-VM beschreven:

| Parameter | Effect |
| --- | --- |
| **DayOfWeek** |Geïnstalleerde patches elke donderdag. |
| **MaintenanceWindowStartingHour** |Start updates op 11: am. |
| **MaintenanceWindowDuration** |Patches moeten binnen 120 minuten worden geïnstalleerd. Op basis van de begin tijd moeten ze worden voltooid met 1:13:00. |
| **PatchCategory** |De enige mogelijke instelling voor deze para meter is ' belang rijk '. |

Het kan enkele minuten duren om de SQL Server IaaS-agent te installeren en configureren.

Als u automatische patching wilt uitschakelen, voert u hetzelfde script uit zonder de para meter-Enable voor de New-AzureVMSqlServerAutoPatchingConfig. Net als bij de installatie kan het enkele minuten duren om automatische patching uit te scha kelen.

## <a name="next-steps"></a>Volgende stappen
Zie [SQL Server IaaS agent extension](../classic/sql-server-agent-extension.md)(Engelstalig) voor meer informatie over andere beschik bare automatiserings taken.

Zie [SQL Server op azure virtual machines Overview](../sql/virtual-machines-windows-sql-server-iaas-overview.md)voor meer informatie over het uitvoeren van SQL Server op virtuele machines in Azure.

