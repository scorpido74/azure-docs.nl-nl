---
title: Geautomatiseerde patching voor SQL Server VM's (Resource Manager) | Microsoft Documenten
description: Hiermee wordt de functie Automatische patching voor SQL Server Virtual Machines uitgelegd die in Azure worden uitgevoerd met Resource Beheer.
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
ms.openlocfilehash: 292f3e8819f6f9f4b2989423814e02dfcfb4bfdb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127683"
---
# <a name="automated-patching-for-sql-server-in-azure-virtual-machines-resource-manager"></a>Automated Patching voor SQL Server in virtuele machines van Azure (Resource Manager)
> [!div class="op_single_selector"]
> * [Resource Manager](virtual-machines-windows-sql-automated-patching.md)
> * [Klassiek](../sqlclassic/virtual-machines-windows-classic-sql-automated-patching.md)

Met Geautomatiseerd patchen wordt een onderhoudsvenster tot stand gebracht voor een virtuele Azure-machine waarop SQL Server wordt uitgevoerd. Geautomatiseerde updates kunnen alleen worden geïnstalleerd tijdens dit onderhoudsvenster. In SQL Server zorgt deze beperking ervoor dat systeemupdates en eventueel benodigd opnieuw opstarten plaatsvinden op het meest geschikte tijdstip voor de database. 

> [!IMPORTANT]
> Alleen Windows- en SQL Server-updates die zijn gemarkeerd als **Belangrijk** of **Kritisch,** worden geïnstalleerd. Andere SQL Server-updates, zoals servicepacks en cumulatieve updates die niet zijn gemarkeerd als **Belangrijk** of **Kritisch,** moeten handmatig worden geïnstalleerd. 

Geautomatiseerd patchen is afhankelijk van de [extensie voor de SQL Server IaaS-agent](virtual-machines-windows-sql-server-agent-extension.md).

## <a name="prerequisites"></a>Vereisten
Als u Geautomatiseerde patching wilt gebruiken, moet u rekening houden met de volgende vereisten:

**Besturingssysteem**:

* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

**SQL Server-versie:**

* SQL Server 2008 R2
* SQL Server 2012
* SQL Server 2014
* SQL Server 2016
* SQL Server 2017

**Azure PowerShell**:

* [Installeer de nieuwste Azure PowerShell-opdrachten](/powershell/azure/overview) als u van plan bent geautomatiseerde patching te configureren met PowerShell.

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

> [!NOTE]
> Geautomatiseerde patching is gebaseerd op de SQL Server IaaS Agent Extension. Huidige SQL-galerieafbeeldingen met virtuele machine voegen deze extensie standaard toe. Zie [SQL Server IaaS Agent Extension](virtual-machines-windows-sql-server-agent-extension.md)voor meer informatie.
> 
> 

## <a name="settings"></a>Instellingen
In de volgende tabel worden de opties beschreven die kunnen worden geconfigureerd voor geautomatiseerde patching. De werkelijke configuratiestappen zijn afhankelijk van of u de Azure-portal of Azure Windows PowerShell-opdrachten gebruikt.

| Instelling | Mogelijke waarden | Beschrijving |
| --- | --- | --- |
| **Geautomatiseerd patchen** |Inschakelen/uitschakelen (uitgeschakeld) |Hiermee schakelt u Geautomatiseerde patching in of uit voor een virtuele Azure-machine. |
| **Onderhoudsplanning** |Elke dag, maandag, dinsdag, woensdag, donderdag, vrijdag, zaterdag, zondag |Het schema voor het downloaden en installeren van Windows-, SQL Server- en Microsoft-updates voor uw virtuele machine. |
| **Startuur onderhoud** |0-24 |De lokale begintijd om de virtuele machine bij te werken. |
| **Duur onderhoudsvenster** |30-180 |Het aantal minuten dat is toegestaan om de download en installatie van updates te voltooien. |
| **Patchcategorie** |Belangrijk | De categorie Windows-updates die moet worden gedownload en geïnstalleerd.|

## <a name="configuration-in-the-portal"></a>Configuratie in de portal
U de Azure-portal gebruiken om geautomatiseerde patching te configureren tijdens het inrichten of voor bestaande VM's.

### <a name="new-vms"></a>Nieuwe VM's
Gebruik de Azure-portal om geautomatiseerde patching te configureren wanneer u een nieuwe SQL Server Virtual Machine maakt in het implementatiemodel voor Resource Beheer.

Selecteer op het tabblad **SQL Server-instellingen** de optie **Configuratie wijzigen** onder **Automatisch patchen**. De volgende Azure-portalscreenshot toont het **SQL Automated Patching-blade.**

![SQL-geautomatiseerde patching in Azure-portal](./media/virtual-machines-windows-sql-automated-patching/azure-sql-arm-patching.png)

Zie voor context het volledige onderwerp over [het inrichten van een virtuele SQL Server-machine in Azure](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="existing-vms"></a>Bestaande VM's

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Open uw [SQL virtual machines-bron](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource) voor bestaande virtuele SQL-machines en selecteer **Patchen** onder **Instellingen**. 

![SQL-automatische patching voor bestaande VM's](./media/virtual-machines-windows-sql-automated-patching/azure-sql-rm-patching-existing-vms.png)


Als u klaar bent, klikt u op de knop **OK** onder aan het **SQL Server-configuratieblad** om de wijzigingen op te slaan.

Als u Voor het eerst Geautomatiseerde patching inschakelt, configureert Azure de SQL Server IaaS-agent op de achtergrond. Gedurende deze periode wordt de Azure-portal mogelijk niet weergegeven dat Geautomatiseerde patching is geconfigureerd. Wacht enkele minuten tot de agent is geïnstalleerd, geconfigureerd. Daarna geeft de Azure-portal de nieuwe instellingen weer.

## <a name="configuration-with-powershell"></a>Configuratie met PowerShell
Nadat u uw SQL VM hebt ingericht, gebruikt u PowerShell om Geautomatiseerde patching te configureren.

In het volgende voorbeeld wordt PowerShell gebruikt om geautomatiseerde patching te configureren op een bestaande SQL Server VM. De opdracht **Nieuw-AzVMSqlServerAutoPatchingConfig** configureert een nieuw onderhoudsvenster voor automatische updates.

    $vmname = "vmname"
    $resourcegroupname = "resourcegroupname"
    $aps = New-AzVMSqlServerAutoPatchingConfig -Enable -DayOfWeek "Thursday" -MaintenanceWindowStartingHour 11 -MaintenanceWindowDuration 120  -PatchCategory "Important"
s Set-AzVMSqlServerExtension -AutoPatchingSettings $aps -VMName $vmname -ResourceGroupName $resourcegroupname

> [!IMPORTANT]
> Als de extensie nog niet is geïnstalleerd, wordt de SQL Server-service opnieuw gestart door de extensie opnieuw te installeren.

Op basis van dit voorbeeld beschrijft de volgende tabel het praktische effect op de doel-Azure VM:

| Parameter | Effect |
| --- | --- |
| **DayOfWeek (DagOfWeek)** |Patches geïnstalleerd elke donderdag. |
| **OnderhoudsvensterStartuur** |Begin om 11:00 uur met updates. |
| **OnderhoudsWindowsDuur** |Patches moeten binnen 120 minuten worden geïnstalleerd. Op basis van de begintijd moeten ze om 13.00 uur klaar zijn. |
| **PatchCategorie** |De enige mogelijke instelling voor deze parameter is **Belangrijk**. Hiermee wordt Windows-update geïnstalleerd met de vermelding Belangrijk; Het installeert geen SQL Server-updates die niet in deze categorie zijn opgenomen. |

Het kan enkele minuten duren voordat de SQL Server IaaS-agent is geïnstalleerd en geconfigureerd.

Als u Geautomatiseerde patching wilt uitschakelen, voert u hetzelfde script uit zonder de parameter **-Inschakelen** voor de **nieuw-AzVMSqlServerAutoPatchingConfig**. De afwezigheid van de parameter **-Inschakelen** signaleert de opdracht om de functie uit te schakelen.

## <a name="next-steps"></a>Volgende stappen
Zie [SQL Server IaaS Agent Extension](virtual-machines-windows-sql-server-agent-extension.md)voor informatie over andere beschikbare automatiseringstaken.

Zie [SQL Server op Azure Virtual Machines-overzicht](virtual-machines-windows-sql-server-iaas-overview.md)voor meer informatie over het uitvoeren van SQL Server op Azure VM's.

