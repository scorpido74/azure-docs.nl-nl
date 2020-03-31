---
title: Geautomatiseerde patching voor SQL Server VM's (Klassiek) | Microsoft Documenten
description: Hiermee wordt de functie Automatische patching voor SQL Server Virtual Machines uitgelegd die in Azure worden uitgevoerd met behulp van de klassieke implementatiemodus.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75978088"
---
# <a name="automated-patching-for-sql-server-in-azure-virtual-machines-classic"></a>Geautomatiseerde patching voor SQL Server in Azure Virtual Machines (Klassiek)
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-sql-automated-patching.md)
> * [Klassiek](../classic/sql-automated-patching.md)
> 
> 

Met Geautomatiseerd patchen wordt een onderhoudsvenster tot stand gebracht voor een virtuele Azure-machine waarop SQL Server wordt uitgevoerd. Geautomatiseerde updates kunnen alleen worden geïnstalleerd tijdens dit onderhoudsvenster. Voor SQL Server zorgt dit ervoor dat systeemupdates en eventuele bijbehorende opnieuw opstarten plaatsvinden op het best mogelijke moment voor de database. 

> [!IMPORTANT]
> Er worden alleen Windows-updates geïnstalleerd die zijn gemarkeerd als **Belangrijk**. Andere SQL Server-updates, zoals cumulatieve updates, moeten handmatig worden geïnstalleerd. 

Geautomatiseerd patchen is afhankelijk van de [extensie voor de SQL Server IaaS-agent](../classic/sql-server-agent-extension.md).

> [!IMPORTANT] 
> Azure heeft twee verschillende implementatiemodellen voor het maken en werken met resources: [Resource Manager en Classic.](../../../azure-resource-manager/management/deployment-models.md) In dit artikel wordt het implementatiemodel Classic gebruikt. U doet er verstandig aan voor de meeste nieuwe implementaties het Resource Manager-model te gebruiken. Zie [Geautomatiseerde patching voor SQL Server in Azure Virtual Machines Resource Manager](../sql/virtual-machines-windows-sql-automated-patching.md)als u de resourcebeheerversie van dit artikel wilt weergeven.

## <a name="prerequisites"></a>Vereisten
Als u Geautomatiseerde patching wilt gebruiken, moet u rekening houden met de volgende vereisten:

**Besturingssysteem**:

* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

**SQL Server-versie:**

* SQL Server 2012
* SQL Server 2014
* SQL Server 2016

**Azure PowerShell**:

* [Installeer de nieuwste Azure PowerShell-opdrachten.](/powershell/azure/overview)

**SQL Server IaaS-extensie:**

* [Installeer de SQL Server IaaS-extensie](../classic/sql-server-agent-extension.md).

## <a name="settings"></a>Instellingen
In de volgende tabel worden de opties beschreven die kunnen worden geconfigureerd voor geautomatiseerde patching. Voor klassieke VM's moet u PowerShell gebruiken om deze instellingen te configureren.

| Instelling | Mogelijke waarden | Beschrijving |
| --- | --- | --- |
| **Geautomatiseerd patchen** |Inschakelen/uitschakelen (uitgeschakeld) |Hiermee schakelt u Geautomatiseerde patching in of uit voor een virtuele Azure-machine. |
| **Onderhoudsplanning** |Elke dag, maandag, dinsdag, woensdag, donderdag, vrijdag, zaterdag, zondag |Het schema voor het downloaden en installeren van Windows-, SQL Server- en Microsoft-updates voor uw virtuele machine. |
| **Startuur onderhoud** |0-24 |De lokale begintijd om de virtuele machine bij te werken. |
| **Duur onderhoudsvenster** |30-180 |Het aantal minuten dat is toegestaan om de download en installatie van updates te voltooien. |
| **Patchcategorie** |Belangrijk |De categorie updates die moet worden gedownload en geïnstalleerd. |

## <a name="configuration-with-powershell"></a>Configuratie met PowerShell
In het volgende voorbeeld wordt PowerShell gebruikt om geautomatiseerde patching te configureren op een bestaande SQL Server VM. Met de opdracht **Nieuw-AzureVMSqlServerAutoPatchingConfig** configureert u een nieuw onderhoudsvenster voor automatische updates.

    $aps = New-AzureVMSqlServerAutoPatchingConfig -Enable -DayOfWeek "Thursday" -MaintenanceWindowStartingHour 11 -MaintenanceWindowDuration 120  -PatchCategory "Important"

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoPatchingSettings $aps | Update-AzureVM

Op basis van dit voorbeeld beschrijft de volgende tabel het praktische effect op de doel-Azure VM:

| Parameter | Effect |
| --- | --- |
| **DayOfWeek (DagOfWeek)** |Patches geïnstalleerd elke donderdag. |
| **OnderhoudsvensterStartuur** |Begin om 11:00 uur met updates. |
| **OnderhoudsvensterDuur** |Patches moeten binnen 120 minuten worden geïnstalleerd. Op basis van de begintijd moeten ze om 13.00 uur klaar zijn. |
| **PatchCategorie** |De enige mogelijke instelling voor deze parameter is "Belangrijk". |

Het kan enkele minuten duren voordat de SQL Server IaaS-agent is geïnstalleerd en geconfigureerd.

Als u Geautomatiseerde patching wilt uitschakelen, voert u hetzelfde script uit zonder de parameter -Inschakelen naar de nieuw-AzureVMSqlServerAutoPatchingConfig. Net als bij de installatie kan het enkele minuten duren om Automated Patching uit te schakelen.

## <a name="next-steps"></a>Volgende stappen
Zie [SQL Server IaaS Agent Extension](../classic/sql-server-agent-extension.md)voor informatie over andere beschikbare automatiseringstaken.

Zie [SQL Server op Azure Virtual Machines-overzicht](../sql/virtual-machines-windows-sql-server-iaas-overview.md)voor meer informatie over het uitvoeren van SQL Server op Azure VM's.

