---
title: Beheertaken automatiseren met de IaaS Agent-extensie
description: In dit artikel wordt beschreven hoe u de SQL Server IaaS-agentextensie beheert, die specifieke SQL Server-beheertaken automatiseert. Deze omvatten geautomatiseerde back-up, geautomatiseerde patching en Azure Key Vault-integratie.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: jroth
editor: ''
tags: azure-resource-manager
ms.assetid: effe4e2f-35b5-490a-b5ef-b06746083da4
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/30/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 3d16c1950cbae0bcc7dd858e5520eb8bfc6e496d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77030775"
---
# <a name="automate-management-tasks-on-azure-virtual-machines-by-using-the-sql-server-iaas-agent-extension"></a>Beheertaken op virtuele Azure-machines automatiseren met de SQL Server IaaS-agentextensie
> [!div class="op_single_selector"]
> * [Resource Manager](virtual-machines-windows-sql-server-agent-extension.md)
> * [Klassiek](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md)

De SQL Server IaaS Agent-extensie (SqlIaasExtension) wordt uitgevoerd op virtuele Azure-machines beheertaken te automatiseren. In dit artikel vindt u een overzicht van de services die de extensie ondersteunt. Dit artikel bevat ook instructies voor de installatie, status en verwijdering van de extensie.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Zie [SQL Server IaaS Agent Extension voor SQL Server VM's (klassiek)](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md)om de klassieke versie van dit artikel te bekijken.


## <a name="supported-services"></a>Ondersteunde services
De SQL Server IaaS Agent Extension ondersteunt de volgende beheertaken:

| Beheerfunctie | Beschrijving |
| --- | --- |
| **Automatische back-up van SQL Server** |Automatiseert de planning van back-ups voor alle databases voor de standaardinstantie of een [correct geïnstalleerde](virtual-machines-windows-sql-server-iaas-faq.md#administration) instantie van SQL Server op de VM. Zie [Geautomatiseerde back-up voor SQL Server in Azure virtual machines (Resource Manager) voor](virtual-machines-windows-sql-automated-backup.md)meer informatie. |
| **Geautomatiseerd patchen van SQL Server** |Hiermee configureert u een onderhoudsvenster waarin belangrijke Windows-updates voor uw vm kunnen plaatsvinden, zodat u updates tijdens piekuren voor uw werkbelasting voorkomen. Zie [Automatisch patchen voor SQL Server in Azure virtual machines (Resource Manager) voor](virtual-machines-windows-sql-automated-patching.md)meer informatie. |
| **Integratie van Azure Sleutelkluis** |Hiermee u Azure Key Vault automatisch installeren en configureren op uw SQL Server VM. Zie [Azure Key Vault-integratie configureren voor SQL Server op Azure Virtual Machines (Resource Manager) voor](virtual-machines-windows-ps-sql-keyvault.md)meer informatie. |

Nadat de SQL Server Iaas Agent Extension is geïnstalleerd en uitgevoerd, worden de beheerfuncties beschikbaar gemaakt:

* Op het SQL Server-paneel van de virtuele machine in de Azure-portal en via Azure PowerShell voor SQL Server-afbeeldingen op Azure Marketplace.
* Via Azure PowerShell voor handmatige installaties van de extensie. 

## <a name="prerequisites"></a>Vereisten
Dit zijn de vereisten voor het gebruik van de SQL Server IaaS Agent Extension op uw VM:

**Besturingssysteem**:

* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016
* Windows Server 2019 

**SQL Server-versie:**

* SQL Server 2008 
* SQL Server 2008 R2
* SQL Server 2012
* SQL Server 2014
* SQL Server 2016
* SQL Server 2017
* SQL Server 2019

**Azure PowerShell**:

* [De nieuwste Azure PowerShell-opdrachten downloaden en configureren](/powershell/azure/overview)

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]


##  <a name="installation"></a>Installeren
De SQL Server IaaS-extensie wordt geïnstalleerd wanneer u uw SQL Server VM registreert bij de [SQL VM-resourceprovider.](virtual-machines-windows-sql-register-with-resource-provider.md) Indien nodig u de SQL Server IaaS-agent handmatig installeren met de onderstaande PowerShell-opdracht: 

  ```powershell-interactive
    Set-AzVMSqlServerExtension -VMName "sql2017" `
    -ResourceGroupName "LabsqlIAASagent" -Name "SQLIaasExtension" `
    -Version "2.0" -Location "Central US";  
  ```

> [!NOTE]
> Als u de extensie installeert, wordt de SQL Server-service opnieuw gestart. 


### <a name="install-on-a-vm-with-a-single-named-sql-server-instance"></a>Installeren op een VM met één SQL Server-exemplaar met de naam
De SQL Server IaaS-extensie werkt met een benoemde instantie op SQL Server als de standaardinstantie is verwijderd en de IaaS-extensie opnieuw is geïnstalleerd.

Voer de volgende stappen uit om een benoemde instantie van SQL Server te gebruiken:
   1. Een SQL Server VM implementeren vanuit Azure Marketplace. 
   1. Verwijder de IaaS-extensie van de [Azure-portal.](https://portal.azure.com)
   1. Verwijder SQL Server volledig binnen de SQL Server VM.
   1. Installeer SQL Server met een benoemde instantie binnen de SQL Server VM. 
   1. Installeer de IaaS-extensie vanuit de Azure-portal.  


## <a name="get-the-status-of-the-sql-server-iaas-extension"></a>De status van de SQL Server IaaS-extensie krijgen
Een manier om te controleren of de extensie is geïnstalleerd, is door de status van de agent in de Azure-portal weer te geven. Selecteer **Alle instellingen** in het venster van de virtuele machine en selecteer **Extensies**. U ziet de **SqlIaasExtension-extensie** vermeld.

![Status van de SQL Server IaaS-agentextensie in de Azure-portal](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-portal.png)

U ook de cmdlet **Get-AzVMSqlServerExtension** Azure PowerShell gebruiken:

   ```powershell-interactive
   Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
   ```

De vorige opdracht bevestigt dat de agent is geïnstalleerd en geeft algemene statusinformatie. U specifieke statusinformatie over geautomatiseerde back-upen en patchen krijgen met behulp van de volgende opdrachten:

   ```powershell-interactive
    $sqlext = Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
    $sqlext.AutoPatchingSettings
    $sqlext.AutoBackupSettings
   ```

## <a name="removal"></a>Verwijdering
In de Azure-portal u de extensie verwijderen door de ellips te selecteren in het **venster Extensies** van de eigenschappen van uw virtuele machine. Selecteer vervolgens **Verwijderen**.

![De SQL Server IaaS-agentextensie verwijderen in Azure-portal](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-uninstall.png)

U ook de PowerDlet **Remove-AzVMSqlServerExtension** PowerShell gebruiken:

   ```powershell-interactive
    Remove-AzVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SqlIaasExtension"
   ```

## <a name="next-steps"></a>Volgende stappen
Begin met het gebruik van een van de services die de extensie ondersteunt. Zie voor meer informatie de artikelen waarnaar verwezen wordt in het gedeelte [Ondersteunde services](#supported-services) van dit artikel.

Zie [Sql Server op Azure Virtual Machines?](virtual-machines-windows-sql-server-iaas-overview.md).
