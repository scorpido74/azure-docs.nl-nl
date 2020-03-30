---
title: SQL Server VM's beheren in Azure met behulp van de Azure-portal | Microsoft Documenten
description: Meer informatie over hoe u toegang krijgt tot de SQL-bron voor virtuele machines in de Azure-portal voor een SQL Server VM die op Azure wordt gehost.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 244ad7d079fd7baf25f8079557576c42d25ca785
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79243209"
---
# <a name="manage-sql-server-vms-in-azure-by-using-the-azure-portal"></a>SQL Server VM's beheren in Azure met behulp van de Azure-portal

In de [Azure-portal](https://portal.azure.com)is de **SQL virtual machines-bron** een onafhankelijke beheerservice. U het gebruiken om al uw SQL Server VM's tegelijkertijd weer te geven en instellingen te wijzigen die zijn gewijd aan SQL Server: 

![SQL-bron voor virtuele machines](media/virtual-machines-windows-sql-manage-portal/sql-vm-manage.png)


## <a name="remarks"></a>Opmerkingen

- We raden u aan de **SQL virtual machines-bron** te gebruiken om uw SQL Server VM's in Azure weer te geven en te beheren. Maar op dit moment ondersteunt de **SQL virtual machines-bron** het beheer van SQL Server [VM's met end-of-support](virtual-machines-windows-sql-server-2008-eos-extend-support.md) niet. Als u instellingen wilt beheren voor uw SQL Server VM's aan het einde van de ondersteuning, gebruikt u in plaats daarvan het afgeschafte [SQL Server-configuratietabblad.](#access-the-sql-server-configuration-tab) 
- De **SQL virtual machines-bron** is alleen beschikbaar voor SQL Server VM's die zijn geregistreerd bij de [SQL VM-resourceprovider.](virtual-machines-windows-sql-register-with-resource-provider.md) 


## <a name="access-the-sql-virtual-machines-resource"></a>Toegang tot de SQL-bron voor virtuele machines
Ga als volgt te werk om toegang te krijgen tot de **SQL-bron voor virtuele machines:**

1. Open de [Azure Portal](https://portal.azure.com). 
1. Selecteer **Alle services**. 
1. Voer **virtuele SQL-machines** in het zoekvak in.
1. (Optioneel): Selecteer de ster naast **virtuele SQL-machines** om deze optie toe te voegen aan het menu **Favorieten.** 
1. Selecteer **virtuele SQL-machines**. 

   ![SQL Server-virtuele machines zoeken in alle services](media/virtual-machines-windows-sql-manage-portal/sql-vm-search.png)

1. De portal bevat alle SQL Server VM's die beschikbaar zijn in het abonnement. Selecteer de bron die u wilt beheren om de **SQL-bron voor virtuele machines** te openen. Gebruik het zoekvak als uw SQL Server VM niet wordt weergegeven. 

   ![Alle beschikbare SQL Server VM's](media/virtual-machines-windows-sql-manage-portal/all-sql-vms.png)

   Als u uw SQL Server VM selecteert, wordt de **SQL virtual machines-bron** geopend: 


   ![SQL-bron voor virtuele machines](media/virtual-machines-windows-sql-manage-portal/sql-vm-resource.png)

> [!TIP]
> De **SQL virtual machines-bron** is bedoeld voor speciale SQL Server-instellingen. Selecteer de naam van de VM in het vak **Virtuele machine** om instellingen te openen die specifiek zijn voor de VM, maar niet exclusief voor SQL Server. 

## <a name="access-the-sql-server-configuration-tab"></a>Toegang tot het sql-server-configuratietabblad
Het **tabblad SQL Server-configuratie** is afgeschaft. Op dit moment is dit de enige methode voor het beheren van SQL Server VM's aan het einde van de ondersteuning en SQL Server VM's die niet zijn [geregistreerd bij de SQL VM-resourceprovider.](virtual-machines-windows-sql-register-with-resource-provider.md) [end-of-support](virtual-machines-windows-sql-server-2008-eos-extend-support.md)

Als u toegang wilt krijgen tot het afgeschafte **SQL Server-configuratietabblad,** gaat u naar de bron **virtuele machines.** Voer de volgende stappen uit:

1. Open de [Azure Portal](https://portal.azure.com). 
1. Selecteer **Alle services**. 
1. Voer **virtuele machines** in het zoekvak in.
1. (Optioneel): Selecteer de ster naast **virtuele machines** om deze optie toe te voegen aan het menu **Favorieten.** 
1. Selecteer **Virtuele machines**. 

   ![Zoeken naar virtuele machines](media/virtual-machines-windows-sql-manage-portal/vm-search.png)

1. De portal bevat alle virtuele machines in het abonnement. Selecteer de bron die u wilt beheren om de bron **virtuele machines** te openen. Gebruik het zoekvak als uw SQL Server VM niet wordt weergegeven. 
1. Selecteer **SQL Server-configuratie** in het deelvenster **Instellingen** om uw SQL Server VM te beheren. 

   ![SQL Server-configuratie](media/virtual-machines-windows-sql-manage-portal/sql-vm-configuration.png)

## <a name="next-steps"></a>Volgende stappen

Raadpleeg voor meer informatie de volgende artikelen: 

* [Overzicht van SQL Server op een Windows VM](virtual-machines-windows-sql-server-iaas-overview.md)
* [Veelgestelde vragen over SQL Server op een Windows-vm](virtual-machines-windows-sql-server-iaas-faq.md)
* [Richtlijnen voor prijzen voor SQL Server op een Windows-vm](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Notities voor SQL Server vrijgeven op een Windows-vm](virtual-machines-windows-sql-server-iaas-release-notes.md)


