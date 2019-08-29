---
title: Automatische patching voor SQL Server Vm's (Resource Manager) | Microsoft Docs
description: In dit onderwerp wordt de functie voor automatische patching voor SQL Server Virtual Machines uitgevoerd in azure met behulp van Resource Manager.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-resource-manager
ms.assetid: 58232e92-318f-456b-8f0a-2201a541e08d
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/07/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 0900dd1809ecb1e93906b57483f334d0f12f6582
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70102082"
---
# <a name="automated-patching-for-sql-server-in-azure-virtual-machines-resource-manager"></a>Automated Patching voor SQL Server in virtuele machines van Azure (Resource Manager)
> [!div class="op_single_selector"]
> * [Resource Manager](virtual-machines-windows-sql-automated-patching.md)
> * [Klassiek](../sqlclassic/virtual-machines-windows-classic-sql-automated-patching.md)

Met de functie voor automatische patching wordt een onderhouds venster voor een virtuele Azure-machine met SQL Server tot stand gebracht. Geautomatiseerde updates kunnen alleen worden geïnstalleerd tijdens dit onderhoudsvenster. In SQL Server zorgt deze beperking ervoor dat systeemupdates en eventueel benodigd opnieuw opstarten plaatsvinden op het meest geschikte tijdstip voor de database. 

> [!IMPORTANT]
> Er worden alleen Windows-updates geïnstalleerd die zijn gemarkeerd als **Belangrijk**. Andere SQL Server-updates, zoals cumulatieve updates, moeten handmatig worden geïnstalleerd. 

Geautomatiseerd patchen is afhankelijk van de [extensie voor de SQL Server IaaS-agent](virtual-machines-windows-sql-server-agent-extension.md).

## <a name="prerequisites"></a>Vereisten
Als u automatische patching wilt gebruiken, moet u rekening houden met de volgende vereisten:

**Besturings systeem**:

* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

**SQL Server versie**:

* SQL Server 2008 R2
* SQL Server 2012
* SQL Server 2014
* SQL Server 2016
* SQL Server 2017

**Azure PowerShell**:

* [Installeer de meest recente Azure PowerShell-opdrachten](/powershell/azure/overview) als u automatische patches wilt configureren met Power shell.

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

> [!NOTE]
> Automatische patching is afhankelijk van de SQL Server IaaS agent-extensie. Huidige installatie kopieën van de virtuele machine van SQL-machines deze extensie standaard toevoegen. Zie [SQL Server IaaS agent extension](virtual-machines-windows-sql-server-agent-extension.md)(Engelstalig) voor meer informatie.
> 
> 

## <a name="settings"></a>Instellingen
In de volgende tabel worden de opties beschreven die kunnen worden geconfigureerd voor automatische patching. De werkelijke configuratie stappen variëren, afhankelijk van of u de Azure Portal-of Azure Windows Power shell-opdrachten gebruikt.

| Instelling | Mogelijke waarden | Description |
| --- | --- | --- |
| **Automatisch patch toepassen** |Inschakelen/uitschakelen (uitgeschakeld) |Hiermee schakelt u automatische patching voor een virtuele machine van Azure in of uit. |
| **Onderhouds planning** |Dagelijks, maandag, dinsdag, woensdag, donderdag, vrijdag, zaterdag, zondag |Het schema voor het downloaden en installeren van Windows-, SQL Server-en micro soft-updates voor uw virtuele machine. |
| **Start-uur onderhoud** |0-24 |De lokale start tijd voor het bijwerken van de virtuele machine. |
| **Duur van onderhouds venster** |30-180 |Het aantal minuten dat het downloaden en installeren van updates is voltooid. |
| **Patch categorie** |Belangrijk | De categorie van Windows-updates die u wilt downloaden en installeren.|

## <a name="configuration-in-the-portal"></a>Configuratie in de portal
U kunt de Azure Portal gebruiken om automatische patches te configureren tijdens het inrichten of voor bestaande Vm's.

### <a name="new-vms"></a>Nieuwe Vm's
Gebruik de Azure Portal om automatische patches te configureren wanneer u een nieuwe SQL Server virtuele machine maakt in het Resource Manager-implementatie model.

Selecteer in het tabblad **SQL Server instellingen** de optie **configuratie wijzigen** onder **automatische patching**. De volgende Azure Portal scherm afbeelding toont de Blade **SQL** Automated patching.

![Automatische patches voor SQL in Azure Portal](./media/virtual-machines-windows-sql-automated-patching/azure-sql-arm-patching.png)

Zie voor context het volledige onderwerp over het [inrichten van een SQL Server virtuele machine in azure](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="existing-vms"></a>Bestaande Vm's

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Voor bestaande SQL Server virtuele machines opent u de [resource virtuele SQL-machines](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource) en selecteert u patches onder **instellingen**. 

![Automatische patches voor SQL voor bestaande Vm's](./media/virtual-machines-windows-sql-automated-patching/azure-sql-rm-patching-existing-vms.png)


Wanneer u klaar bent, klikt u op de knop **OK** onder aan de blade **SQL Server configuratie** om uw wijzigingen op te slaan.

Als u automatische patching voor de eerste keer inschakelt, configureert Azure de SQL Server IaaS-agent op de achtergrond. Gedurende deze Azure Portal tijd wordt mogelijk niet weer gegeven dat automatische patches zijn geconfigureerd. Wacht enkele minuten totdat de agent is geïnstalleerd en geconfigureerd. Nadat de Azure Portal de nieuwe instellingen weerspiegelt.

## <a name="configuration-with-powershell"></a>Configuratie met Power shell
Nadat u uw SQL-VM hebt ingericht, gebruikt u Power shell om automatische patches te configureren.

In het volgende voor beeld wordt Power shell gebruikt voor het configureren van automatische patches op een bestaande SQL Server VM. Met de opdracht **New-AzVMSqlServerAutoPatchingConfig** wordt een nieuwe onderhouds venster voor automatische updates geconfigureerd.

    $vmname = "vmname"
    $resourcegroupname = "resourcegroupname"
    $aps = New-AzVMSqlServerAutoPatchingConfig -Enable -DayOfWeek "Thursday" -MaintenanceWindowStartingHour 11 -MaintenanceWindowDuration 120  -PatchCategory "Important"
s Set-AzVMSqlServerExtension -AutoPatchingSettings $aps -VMName $vmname -ResourceGroupName $resourcegroupname

> [!IMPORTANT]
> Als de extensie nog niet is geïnstalleerd, start de installatie van de extensie de SQL Server-service opnieuw.

Op basis van dit voor beeld wordt in de volgende tabel het praktische effect van de Azure-doel-VM beschreven:

| Parameter | Effect |
| --- | --- |
| **DayOfWeek** |Geïnstalleerde patches elke donderdag. |
| **MaintenanceWindowStartingHour** |Start updates op 11: am. |
| **MaintenanceWindowsDuration** |Patches moeten binnen 120 minuten worden geïnstalleerd. Op basis van de begin tijd moeten ze worden voltooid met 1:13:00. |
| **PatchCategory** |De enige mogelijke instelling voor deze para meter is **belang rijk**. Hiermee wordt Windows Update als belang rijk gemarkeerd. Er worden geen SQL Server updates geïnstalleerd die niet in deze categorie zijn opgenomen. |

Het kan enkele minuten duren om de SQL Server IaaS-agent te installeren en configureren.

Als u automatische patching wilt uitschakelen, voert u hetzelfde script uit zonder de para meter **-Enable** voor de **New-AzVMSqlServerAutoPatchingConfig**. Als de para meter **-Enable ontbreekt,** wordt de opdracht voor het uitschakelen van de functie door gesignaleerd.

## <a name="next-steps"></a>Volgende stappen
Zie [SQL Server IaaS agent extension](virtual-machines-windows-sql-server-agent-extension.md)(Engelstalig) voor meer informatie over andere beschik bare automatiserings taken.

Zie [SQL Server op azure virtual machines Overview](virtual-machines-windows-sql-server-iaas-overview.md)voor meer informatie over het uitvoeren van SQL Server op virtuele machines in Azure.

