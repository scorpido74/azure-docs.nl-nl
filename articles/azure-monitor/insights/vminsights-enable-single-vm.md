---
title: Azure Monitor voor VM's in het Azure Portal inschakelen
description: Meer informatie over het evalueren van Azure Monitor voor VM's op één virtuele machine in azure of op een schaalset voor virtuele machines.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/25/2020
ms.openlocfilehash: d4f14280f38a2389809477b84461eb3174909316
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87323890"
---
# <a name="enable-azure-monitor-for-single-vm-or-vmss-in-the-azure-portal"></a>Azure Monitor inschakelen voor één virtuele machine of VMSS in de Azure Portal
In dit artikel wordt beschreven hoe u Azure Monitor voor VM's in te scha kelen voor één virtuele machine of een schaalset voor virtuele machines met behulp van de Azure Portal. Deze procedure kan worden gebruikt voor het volgende:

- Azure virtuele machine
- Schaalset voor virtuele Azure-machines
- Azure Arc-computer

Voordat u begint, controleert u [de vereisten](vminsights-enable-overview.md) en zorgt u ervoor dat uw abonnement en de resources voldoen aan de vereisten.  

## <a name="enable-azure-monitor-for-vms"></a>Azure Monitor voor VM's inschakelen

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

1. Selecteer **virtuele machines**, **virtuele-machine schaal sets**of **machines-Azure-boog**.

1. Selecteer een resource in de lijst.

1. Selecteer in de sectie **bewaking** van het menu **inzichten** en **Schakel**vervolgens in. In het volgende voor beeld ziet u een virtuele machine van Azure, maar het menu is vergelijkbaar voor Azure VMSS of Azure Arc.

    ![Azure Monitor voor VM's voor een VM inschakelen](media/vminsights-enable-single-vm/enable-vminsights-vm-portal.png)

1. Als de virtuele machine nog niet is verbonden met een Log Analytics-werk ruimte, wordt u gevraagd om er een te selecteren. Als u nog geen [werk ruimte hebt gemaakt](../learn/quick-create-workspace.md), kunt u een standaard waarde selecteren voor de locatie waar de virtuele machine of VMSS in het abonnement wordt geïmplementeerd. Deze werk ruimte wordt gemaakt en geconfigureerd als deze nog niet bestaat.

2. U ontvangt status berichten wanneer de configuratie wordt uitgevoerd.

    >[!NOTE]
    >Als u een hand matig upgrade model gebruikt voor uw schaalset, moet u de exemplaren bijwerken om de installatie te volt ooien. U kunt de upgrades starten vanaf de pagina **instanties** , in de sectie **instellingen** .

    ![Verwerking van de implementatie van de bewaking van Azure Monitor voor VM's inschakelen](media/vminsights-enable-single-vm/onboard-vminsights-vm-portal-status.png)



## <a name="next-steps"></a>Volgende stappen

* Zie [Azure monitor voor VM's kaart gebruiken](vminsights-maps.md)om gedetecteerde toepassings afhankelijkheden weer te geven. 
* Zie [Azure-VM-prestaties weer geven](vminsights-performance.md)om knel punten, algemeen gebruik en de prestaties van uw virtuele machine te identificeren.

