---
title: Beheer taken automatiseren op SQL-Vm's (klassiek) | Microsoft Docs
description: In dit onderwerp wordt beschreven hoe u de SQL Server Agent-extensie beheert, waarmee specifieke SQL Server beheer taken worden geautomatiseerd. Dit zijn onder andere automatische back-ups, automatische patching en integratie van Azure Key Vault. In dit onderwerp wordt gebruikgemaakt van de klassieke implementatie modus.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: a9bda2e7-cdba-427c-bc30-77cde4376f3a
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 07/12/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: e58b3f651a7a8f65d3450cf4b069d4e3f252d928
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70100269"
---
# <a name="automate-management-tasks-on-azure-virtual-machines-with-the-sql-server-agent-extension-classic"></a>Beheer taken op Azure Virtual Machines automatiseren met de SQL Server Agent extensie (klassiek)
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-sql-server-agent-extension.md)
> * [Klassiek](../classic/sql-server-agent-extension.md)
> 
>
 
De SQL Server IaaS agent extension (SQLIaaSAgent) wordt uitgevoerd op virtuele machines van Azure om de beheer taken te automatiseren. Dit onderwerp bevat een overzicht van de services die door de extensie worden ondersteund, evenals instructies voor de installatie, status en verwijdering.

> [!IMPORTANT] 
> Azure heeft twee verschillende implementatiemodellen voor het maken van en werken met resources: [Resource Manager en klassiek](../../../azure-resource-manager/resource-manager-deployment-model.md). In dit artikel wordt beschreven hoe u het klassieke implementatie model gebruikt. U doet er verstandig aan voor de meeste nieuwe implementaties het Resource Manager-model te gebruiken. Als u de Resource Manager-versie van dit artikel wilt bekijken, raadpleegt u [SQL Server Agent-extensie voor SQL Server Vm's Resource Manager](../sql/virtual-machines-windows-sql-server-agent-extension.md).

## <a name="supported-services"></a>Ondersteunde services
De SQL Server IaaS agent-extensie ondersteunt de volgende beheer taken:

| Beheer functie | Description |
| --- | --- |
| **Automatische back-up van SQL** |Automatiseert het plannen van back-ups voor alle data bases voor het standaard exemplaar van SQL Server in de virtuele machine. Zie [automatische back-up voor SQL Server in Azure virtual machines (klassiek)](../classic/sql-automated-backup.md)voor meer informatie. |
| **Automatische SQL-patching** |Hiermee configureert u een onderhouds venster waarin belang rijke Windows-updates voor uw virtuele machine kunnen worden uitgevoerd, zodat u updates kunt voor komen tijdens piek tijden voor uw werk belasting. Zie voor meer informatie [automatische patching voor SQL Server in Azure virtual machines (klassiek)](../classic/sql-automated-patching.md). |
| **Integratie van Azure Key Vault** |Hiermee kunt u Azure Key Vault automatisch installeren en configureren op uw SQL Server-VM. Zie [Azure Key Vault-integratie configureren voor SQL Server op virtuele Azure-machines (klassiek)](../classic/ps-sql-keyvault.md)voor meer informatie. |

## <a name="prerequisites"></a>Vereisten
Vereisten voor het gebruik van de SQL Server IaaS agent-extensie op uw VM:

### <a name="operating-system"></a>Besturingssysteem:
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

### <a name="sql-server-versions"></a>SQL Server versies:
* SQL Server 2012
* SQL Server 2014
* SQL Server 2016

### <a name="azure-powershell"></a>Azure PowerShell:
[Down load en configureer de nieuwste Azure PowerShell-opdrachten](/powershell/azure/overview).

Start Windows Power shell en verbind deze met uw Azure-abonnement met de opdracht **add-AzureAccount** .

    Add-AzureAccount

Als u meerdere abonnementen hebt, gebruikt u **Select-abonnement** om het abonnement te selecteren dat de klassieke VM van uw doel bevat.

    Select-AzureSubscription -SubscriptionName <subscriptionname>

Op dit moment kunt u een lijst met de klassieke virtuele machines en de bijbehorende service namen ophalen met de opdracht **Get-AzureVM** .

    Get-AzureVM

## <a name="installation"></a>Installatie
Voor klassieke Vm's moet u Power shell gebruiken om de SQL Server IaaS agent-extensie te installeren en de bijbehorende services te configureren. Gebruik de Power shell **-cmdlet Set-AzureVMSqlServerExtension** om de extensie te installeren. Met de volgende opdracht wordt bijvoorbeeld de uitbrei ding geïnstalleerd op een virtuele Windows Server-machine (klassiek) en de naam ' SQLIaaSExtension '.

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -ReferenceName "SQLIaasExtension" -Version "1.2" | Update-AzureVM

Als u de nieuwste versie van de SQL IaaS agent-extensie bijwerkt, moet u de virtuele machine opnieuw opstarten nadat u de extensie hebt bijgewerkt.

> [!NOTE]
> Klassieke virtuele machines hebben geen optie voor het installeren en configureren van de SQL IaaS agent-extensie via de portal.

> [!NOTE]
> De uitbrei ding voor de SQL Server IaaS-agent wordt alleen ondersteund op [SQL Server VM-galerie kopieën](../sql/virtual-machines-windows-sql-server-iaas-overview.md#get-started-with-sql-vms) (betalen per gebruik of uw eigen licentie). Het wordt niet ondersteund als u SQL Server hand matig installeert op een virtuele Windows Server-machine met alleen een besturings systeem of als u een aangepaste SQL Server VM-VHD implementeert. In dergelijke gevallen is het mogelijk om de uitbrei ding hand matig te installeren en beheren met behulp van Power shell, maar het wordt ten zeerste aanbevolen om in plaats daarvan een SQL Server VM-galerie afbeelding te installeren en deze vervolgens aan te passen.

## <a name="status"></a>Status
Een manier om te controleren of de uitbrei ding is geïnstalleerd, is de agent status weer geven in azure Portal. Selecteer een virtuele machine die wordt weer gegeven op de Blade van de virtuele machine en klik vervolgens op **extensies**. De uitbrei ding **SQLIaaSAgent** wordt weer gegeven.

![IaaS agent-extensie SQL Server in azure Portal](./media/virtual-machines-windows-classic-sql-server-agent-extension/azure-sql-server-iaas-agent-portal.png)

U kunt ook de Azure Power shell **-cmdlet Get-AzureVMSqlServerExtension** gebruiken.

    Get-AzureVM –ServiceName "service" –Name "vmname" | Get-AzureVMSqlServerExtension

## <a name="removal"></a>Verwijdering
In azure Portal kunt u de uitbrei ding verwijderen door op het weglatings teken te klikken op de Blade **uitbrei dingen** van de eigenschappen van de virtuele machine. Klik vervolgens op **verwijderen**.

![De uitbrei ding voor de SQL Server IaaS-agent verwijderen in azure Portal](./media/virtual-machines-windows-classic-sql-server-agent-extension/azure-sql-server-iaas-agent-uninstall.png)

U kunt ook de Power shell **-cmdlet Remove-AzureVMSqlServerExtension** gebruiken.

    Get-AzureVM –ServiceName "service" –Name "vmname" | Remove-AzureVMSqlServerExtension | Update-AzureVM

## <a name="next-steps"></a>Volgende stappen
Gebruik een van de services die door de extensie worden ondersteund. Zie de onderwerpen waarnaar wordt verwezen in de sectie [ondersteunde services](#supported-services) van dit artikel voor meer informatie.

Zie [SQL Server in azure virtual machines Overview](../sql/virtual-machines-windows-sql-server-iaas-overview.md)voor meer informatie over het uitvoeren van SQL Server op Azure virtual machines.

