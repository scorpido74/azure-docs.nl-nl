---
title: Azure Monitor voor VM's inschakelen (preview) voor evaluatie | Microsoft Docs
description: Meer informatie over het evalueren van Azure Monitor voor VM's op één virtuele machine in azure of op een schaalset voor virtuele machines.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/14/2019
ms.openlocfilehash: c77fabf0220ecfcb889ce7cb71fa075fde27226f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75400581"
---
# <a name="enable-azure-monitor-for-vms-preview-for-evaluation"></a>Azure Monitor voor VM's inschakelen (preview) voor evaluatie

U kunt Azure Monitor voor VM's (preview) evalueren op een klein aantal virtuele machines (Vm's) van Azure of op één virtuele machine of VM-schaalset. De eenvoudigste en meest directe manier om bewaking in te scha kelen, is van de Azure Portal. Het doel is uw Vm's te bewaken en eventuele problemen met prestaties of Beschik baarheid te detecteren. 

Voordat u begint, controleert u [de vereisten](vminsights-enable-overview.md) en zorgt u ervoor dat uw abonnement en de resources voldoen aan de vereisten.  

## <a name="enable-monitoring-for-a-single-azure-vm"></a>Bewaking inschakelen voor één Azure VM
Controle van uw virtuele Azure-machine inschakelen:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com).

1. Selecteer **virtuele Machines**.

1. Selecteer een VM in de lijst.

1. Op de pagina virtuele machine in de **bewaking** sectie, selecteer **inzichten (preview)** .

1. Op de **inzichten (preview)** weergeeft, schakelt **Probeer nu**.

    ![Azure Monitor voor virtuele machines voor een virtuele machine inschakelen](./media/vminsights-enable-single-vm/enable-vminsights-vm-portal.png)

1. Op de **Azure Monitor Insights voorbereiden** pagina, hebt u een bestaande Log Analytics-werkruimte in hetzelfde abonnement, selecteert u deze in de vervolgkeuzelijst.  

    De lijst preselecteert de standaard werkruimte en de locatie waar de virtuele machine in het abonnement is geïmplementeerd. 

    >[!NOTE]
    >Zie [een log Analytics-werk ruimte maken](../../azure-monitor/learn/quick-create-workspace.md)om een nieuwe log Analytics-werk ruimte te maken voor het opslaan van de bewakings gegevens van de virtuele machine. Uw Log Analytics-werk ruimte moet deel uitmaken van een van de [ondersteunde regio's](vminsights-enable-overview.md#log-analytics).

Nadat u bewaking hebt ingeschakeld, moet u mogelijk ongeveer 10 minuten wachten voordat u de metrische gegevens van de status voor de virtuele machine kunt weer geven.

![Azure Monitor inschakelen voor virtuele machines verwerking van de implementatie controleren](./media/vminsights-enable-single-vm/onboard-vminsights-vm-portal-status.png)

## <a name="enable-monitoring-for-a-single-virtual-machine-scale-set"></a>Bewaking inschakelen voor één schaalset voor virtuele machines

De bewaking van uw Azure virtual machine-schaalset inschakelen:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com).

2. Selecteer **Virtual Machine Scale sets**.

3. Selecteer een schaalset voor virtuele machines in de lijst.

4. Selecteer op de pagina schaalset voor virtuele machines in de sectie **controle** de optie **inzichten (preview-versie)** .

5. Als u een bestaande Log Analytics-werk ruimte wilt gebruiken, selecteert u op de pagina **inzichten (preview)** de optie in de vervolg keuzelijst.

    De lijst preselecteert de standaardwerk ruimte en de standaard locatie waar de virtuele machine wordt geïmplementeerd in het abonnement. 

    ![Azure Monitor voor VM's inschakelen voor een schaalset voor virtuele machines](./media/vminsights-enable-single-vm/enable-vminsights-vmss-portal.png)

    >[!NOTE]
    >Zie [een log Analytics-werk ruimte maken](../learn/quick-create-workspace.md)om een nieuwe log Analytics-werk ruimte te maken voor het opslaan van de bewakings gegevens uit de virtuele-machine schaalset. Uw Log Analytics-werk ruimte moet deel uitmaken van een van de [ondersteunde regio's](vminsights-enable-overview.md#log-analytics).

Nadat u bewaking hebt ingeschakeld, moet u mogelijk ongeveer 10 minuten wachten voordat u de bewakings gegevens voor de schaalset kunt weer geven.

>[!NOTE]
>Als u een hand matig upgrade model gebruikt voor uw schaalset, moet u de exemplaren bijwerken om de installatie te volt ooien. U kunt de upgrades starten vanaf de pagina **instanties** , in de sectie **instellingen** .

![Azure Monitor inschakelen voor virtuele machines verwerking van de implementatie controleren](./media/vminsights-enable-single-vm/onboard-vminsights-vmss-portal-status-01.png)

Nu u bewaking hebt ingeschakeld voor uw VM of virtuele-machine schaalset, zijn de bewakings gegevens beschikbaar voor analyse in Azure Monitor voor VM's. 

## <a name="next-steps"></a>Volgende stappen

* Zie [Azure monitor voor VM's kaart gebruiken](vminsights-maps.md)om gedetecteerde toepassings afhankelijkheden weer te geven. 
* Zie [Azure-VM-prestaties weer geven](vminsights-performance.md)om knel punten, algemeen gebruik en de prestaties van uw virtuele machine te identificeren.
