---
title: SQL Server virtuele machines in azure beheren met behulp van de Azure Portal | Microsoft Docs
description: Meer informatie over toegang tot de resource van de virtuele SQL-machine in de Azure Portal voor een SQL Server VM gehost op Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 8def7c52f2b1005419a29d35ef122b48f34fdee4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84668998"
---
# <a name="manage-sql-server-vms-in-azure-by-using-the-azure-portal"></a>SQL Server Vm's in azure beheren door gebruik te maken van de Azure Portal
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

In de [Azure Portal](https://portal.azure.com)is de resource van de **virtuele SQL-machine** een onafhankelijke beheer service. U kunt deze gebruiken om al uw SQL Server Vm's tegelijk weer te geven en instellingen te wijzigen die zijn toegewezen aan SQL Server: 

![Resource van virtuele SQL-machines](./media/manage-sql-vm-portal/sql-vm-manage.png)


## <a name="remarks"></a>Opmerkingen

- U wordt aangeraden de resource voor **virtuele SQL-machines** te gebruiken voor het weer geven en beheren van uw SQL Server-Vm's in Azure. Op dit moment biedt de resource van de **virtuele SQL-machine** geen ondersteuning voor het beheer van [End-of-support](sql-server-2008-extend-end-of-support.md) SQL Server vm's. Als u de instellingen voor uw end-of-support SQL Server Vm's wilt beheren, gebruikt u het tabblad afgeschafte [SQL Server configuratie](#access-the-sql-server-configuration-tab) . 
- De resource voor **virtuele SQL-machines** is alleen beschikbaar voor SQL Server vm's die zijn [geregistreerd bij de resource provider van de SQL-VM](sql-vm-resource-provider-register.md). 


## <a name="access-the-sql-virtual-machines-resource"></a>Toegang tot de resource van de virtuele SQL-machines
Ga als volgt te werk om toegang te krijgen tot de resource van de **virtuele SQL-machines** :

1. Open de [Azure Portal](https://portal.azure.com). 
1. Selecteer **alle services**. 
1. Voer **SQL-virtuele machines** in het zoekvak in.
1. (Optioneel): Selecteer de ster naast **virtuele machines van SQL** om deze optie toe te voegen aan uw **Favorieten** menu. 
1. Selecteer **virtuele SQL-machines**. 

   ![SQL Server virtuele machines zoeken in alle services](./media/manage-sql-vm-portal/sql-vm-search.png)

1. In de portal wordt een lijst weer gegeven met alle SQL Server Vm's die beschikbaar zijn in het abonnement. Selecteer het item dat u wilt beheren om de resource van de **virtuele SQL-machines** te openen. Gebruik het zoekvak als uw SQL Server virtuele machine niet wordt weer gegeven. 

   ![Alle beschik bare SQL Server Vm's](./media/manage-sql-vm-portal/all-sql-vms.png)

   Als u uw SQL Server VM selecteert, wordt de resource van de **virtuele SQL-machines** geopend: 


   ![Resource van virtuele SQL-machines](./media/manage-sql-vm-portal/sql-vm-resource.png)

> [!TIP]
> De resource van de **virtuele SQL-machine** is voor toegewezen SQL Server-instellingen. Selecteer de VM-naam in het vak **virtuele machine** om instellingen te openen die specifiek zijn voor de VM, maar niet exclusief voor SQL Server. 

## <a name="access-the-sql-server-configuration-tab"></a>Het tabblad SQL Server configuratie openen
Het tabblad **SQL Server configuratie** is afgeschaft. Op dit moment is het de enige methode om [End-of-support](sql-server-2008-extend-end-of-support.md) te beheren SQL Server vm's en SQL Server virtuele machines die niet zijn [geregistreerd bij de resource provider van de SQL-VM](sql-vm-resource-provider-register.md).

Ga naar de resource van de **virtuele machines** om toegang te krijgen tot het tabblad afgeschafte **SQL Server configuratie** . Voer de volgende stappen uit:

1. Open de [Azure Portal](https://portal.azure.com). 
1. Selecteer **alle services**. 
1. Voer de **virtuele machines** in het zoekvak in.
1. (Optioneel): Selecteer de ster naast **virtuele machines** om deze optie toe te voegen aan uw **Favorieten** menu. 
1. Selecteer **Virtuele machines**. 

   ![Zoeken naar virtuele machines](./media/manage-sql-vm-portal/vm-search.png)

1. In de portal worden alle virtuele machines in het abonnement weer gegeven. Selecteer het item dat u wilt beheren om de resource van de **virtuele machines** te openen. Gebruik het zoekvak als uw SQL Server virtuele machine niet wordt weer gegeven. 
1. Selecteer **SQL Server configuratie** in het deel venster **instellingen** om uw SQL Server virtuele machine te beheren. 

   ![SQL Server-configuratie](./media/manage-sql-vm-portal/sql-vm-configuration.png)

## <a name="next-steps"></a>Volgende stappen

Raadpleeg voor meer informatie de volgende artikelen: 

* [Overzicht van SQL Server op een Windows-VM](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Veelgestelde vragen over SQL Server op een Windows-VM](frequently-asked-questions-faq.md)
* [Prijs informatie voor SQL Server op een Windows-VM](pricing-guidance.md)
* [Release opmerkingen voor SQL Server op een Windows-VM](doc-changes-updates-release-notes.md)


