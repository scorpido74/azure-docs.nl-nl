---
title: Beheertaken automatiseren op SQL VM's (Klassiek) | Microsoft Documenten
description: In dit onderwerp wordt beschreven hoe u de SQL Server-agentextensie beheert, die specifieke SQL Server-beheertaken automatiseert. Deze omvatten Geautomatiseerde back-up, geautomatiseerde patching en Azure Key Vault-integratie. In dit onderwerp wordt de klassieke implementatiemodus gebruikt.
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
ms.openlocfilehash: b76ade40db1e85abc0fb42af2e6f4ab88cb092c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75982284"
---
# <a name="automate-management-tasks-on-azure-virtual-machines-with-the-sql-server-agent-extension-classic"></a>Beheertaken op Azure Virtual Machines automatiseren met de SQL Server Agent Extension (Classic)
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-sql-server-agent-extension.md)
> * [Klassiek](../classic/sql-server-agent-extension.md)
> 
>
 
De SQL Server IaaS Agent Extension (SQLIaaSAgent) wordt uitgevoerd op virtuele Azure-machines om beheertaken te automatiseren. Dit onderwerp geeft een overzicht van de services die door de extensie worden ondersteund, evenals instructies voor installatie, status en verwijdering.

> [!IMPORTANT] 
> Azure heeft twee verschillende implementatiemodellen voor het maken en werken met resources: [Resource Manager en Classic.](../../../azure-resource-manager/management/deployment-models.md) In dit artikel wordt het implementatiemodel Classic gebruikt. U doet er verstandig aan voor de meeste nieuwe implementaties het Resource Manager-model te gebruiken. Zie [SQL Server Agent Extension voor SQL Server VMs Resource Manager](../sql/virtual-machines-windows-sql-server-agent-extension.md)als u de versie van Resource Manager van dit artikel wilt weergeven.

## <a name="supported-services"></a>Ondersteunde services
De SQL Server IaaS Agent Extension ondersteunt de volgende beheertaken:

| Beheerfunctie | Beschrijving |
| --- | --- |
| **Automatische back-up van SQL** |Automatiseert de planning van back-ups voor alle databases voor de standaardinstantie van SQL Server in de VM. Zie [Geautomatiseerde back-up voor SQL Server in Azure Virtual Machines (Classic) voor](../classic/sql-automated-backup.md)meer informatie. |
| **Automatisch patchen van SQL** |Hiermee configureert u een onderhoudsvenster waarin belangrijke Windows-updates voor uw vm kunnen plaatsvinden, zodat u updates tijdens piekuren voor uw werkbelasting voorkomen. Zie [Automatisch patchen voor SQL Server in Azure Virtual Machines (Classic) voor](../classic/sql-automated-patching.md)meer informatie. |
| **Azure Key Vault-integratie** |Hiermee u Azure Key Vault automatisch installeren en configureren op uw SQL Server VM. Zie [Azure Key Vault Integration for SQL Server configureren voor SQL Server op Azure VM's (Klassiek) voor](../classic/ps-sql-keyvault.md)meer informatie. |

## <a name="prerequisites"></a>Vereisten
Vereisten om de SQL Server IaaS Agent Extension op uw VM te gebruiken:

### <a name="operating-system"></a>Besturingssysteem:
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

### <a name="sql-server-versions"></a>SQL Server-versies:
* SQL Server 2012
* SQL Server 2014
* SQL Server 2016

### <a name="azure-powershell"></a>Azure PowerShell:
[Download en configureer de nieuwste Azure PowerShell-opdrachten.](/powershell/azure/overview)

Start Windows PowerShell en verbind het met uw Azure-abonnement met de opdracht **Add-AzureAccount.**

    Add-AzureAccount

Als u meerdere abonnementen hebt, gebruikt u **Select-AzureSubscription** om het abonnement te selecteren dat uw doelklassieke VM bevat.

    Select-AzureSubscription -SubscriptionName <subscriptionname>

Op dit punt u een lijst met de klassieke virtuele machines en de bijbehorende servicenamen krijgen met de opdracht **Get-AzureVM.**

    Get-AzureVM

## <a name="installation"></a>Installeren
Voor klassieke VM's moet u PowerShell gebruiken om de SQL Server IaaS Agent Extension te installeren en de bijbehorende services te configureren. Gebruik de **PowerDlet Set-AzureVMSqlServerExtension** om de extensie te installeren. Met de volgende opdracht wordt de extensie bijvoorbeeld geïnstalleerd op een Windows Server VM (klassiek) en wordt de extensie "SQLIaaSExtension" genoemd.

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -ReferenceName "SQLIaasExtension" -Version "1.2" | Update-AzureVM

Als u bijwerkt naar de nieuwste versie van de SQL IaaS-agentextensie, moet u uw virtuele machine opnieuw opstarten nadat de extensie is bijgewerkt.

> [!NOTE]
> Klassieke virtuele machines hebben geen optie om de SQL IaaS Agent Extension via de portal te installeren en configureren.

> [!NOTE]
> De SQL Server IaaS Agent Extension wordt alleen ondersteund op [SQL Server VM-galerijafbeeldingen](../sql/virtual-machines-windows-sql-server-iaas-overview.md#get-started-with-sql-vms) (pay-as-you-go of bring-your-own-license). Deze wordt niet ondersteund als u SQL Server handmatig installeert op een virtuele machine van WINDOWS Server met alleen os of als u een aangepaste SQL Server VM VHD implementeert. In deze gevallen is het mogelijk om de extensie handmatig te installeren en te beheren met PowerShell, maar het wordt ten zeerste aanbevolen om in plaats daarvan een SQL Server VM-galerijafbeelding te installeren en vervolgens aan te passen.

## <a name="status"></a>Status
Een manier om te controleren of de extensie is geïnstalleerd, is door de status van de agent in de Azure Portal weer te geven. Selecteer een virtuele machine die in het virtuele machineblad wordt vermeld en klik op **Extensies**. U ziet de **SQLIaaSAgent-extensie** vermeld.

![SQL Server IaaS-agentextensie in Azure Portal](./media/virtual-machines-windows-classic-sql-server-agent-extension/azure-sql-server-iaas-agent-portal.png)

U ook de cmdlet **Get-AzureVMSqlServerExtension** Azure Powershell gebruiken.

    Get-AzureVM –ServiceName "service" –Name "vmname" | Get-AzureVMSqlServerExtension

## <a name="removal"></a>Verwijdering
In de Azure Portal u de extensie verwijderen door op de ellips op het **extensionsblad** van de eigenschappen van uw virtuele machine te klikken. Klik vervolgens op **Verwijderen**.

![De SQL Server IaaS-agentextensie verwijderen in Azure Portal](./media/virtual-machines-windows-classic-sql-server-agent-extension/azure-sql-server-iaas-agent-uninstall.png)

U ook de cmdlet **Remove-AzureVMSqlServerExtension** Powershell gebruiken.

    Get-AzureVM –ServiceName "service" –Name "vmname" | Remove-AzureVMSqlServerExtension | Update-AzureVM

## <a name="next-steps"></a>Volgende stappen
Begin met het gebruik van een van de services die door de extensie worden ondersteund. Zie voor meer informatie de onderwerpen waarnaar wordt verwezen in het gedeelte [Ondersteunde services](#supported-services) van dit artikel.

Zie [SQL Server op Azure Virtual Machines overzicht](../sql/virtual-machines-windows-sql-server-iaas-overview.md)voor meer informatie over het uitvoeren van SQL Server op Azure Virtual Machines.

