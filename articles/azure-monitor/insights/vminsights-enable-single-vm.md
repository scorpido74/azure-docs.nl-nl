---
title: Azure-monitor voor VM's inschakelen in de Azure-portal
description: Meer informatie over het evalueren van Azure Monitor voor VM's op één Azure virtuele machine of op een virtuele machineschaalset.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2020
ms.openlocfilehash: 45bc8f16a547d4a95820f9dcd02132844b3be83c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480705"
---
# <a name="enable-azure-monitor-for-vms-in-the-azure-portal"></a>Azure-monitor voor VM's inschakelen in de Azure-portal

In dit artikel wordt beschreven hoe u Azure Monitor voor VM's inschakelt op een klein aantal Virtuele Azure-machines (VM's) met behulp van de Azure-portal. Uw doel is om uw VM's te controleren en eventuele problemen met prestaties of beschikbaarheid te ontdekken. 

Voordat u begint, controleert u de [vereisten](vminsights-enable-overview.md) en controleert u of uw abonnement en resources aan de vereisten voldoen.  

## <a name="enable-monitoring-for-a-single-azure-vm"></a>Bewaking inschakelen voor één Azure VM
Ga als volgt te werk om de bewaking van uw Azure VM in te schakelen:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

1. Selecteer **Virtuele machines**.

1. Selecteer een VM in de lijst.

1. Selecteer op de VM-pagina in de sectie **Monitoring** de optie **Inzicht** en **schakel .**

    ![Azure-monitor voor VM's inschakelen voor een vm](media/vminsights-enable-single-vm/enable-vminsights-vm-portal.png)

1. Als u op de **onboarding-pagina Azure Monitor Insights** een bestaande Log Analytics-werkruimte in hetzelfde abonnement hebt, selecteert u deze in de vervolgkeuzelijst.  

    De lijst selecteert vooraf de standaardwerkruimte en locatie waar de VM in het abonnement wordt geïmplementeerd. 

    >[!NOTE]
    >Zie [Een werkruimte logboekanalyse maken](../../azure-monitor/learn/quick-create-workspace.md)als u een nieuwe werkruimte Log Analytics wilt maken om de bewakingsgegevens van de vm op te slaan. Uw loganalytics-werkruimte moet deel uitmaken van een van de [ondersteunde regio's](vminsights-enable-overview.md#log-analytics).

6. U ontvangt statusberichten wanneer de configuratie wordt uitgevoerd.

    ![Azure Monitor voor VM's-controle-implementatieverwerking inschakelen](media/vminsights-enable-single-vm/onboard-vminsights-vm-portal-status.png)

## <a name="enable-monitoring-for-a-single-virtual-machine-scale-set"></a>Bewaking inschakelen voor één virtuele machineschaalset

Ga als volgt te werk om de bewaking van de azure-schaalset voor virtuele machines in te schakelen:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

2. Selecteer **Virtuele machineschaalsets**.

3. Selecteer in de lijst een virtuele machineschaalset.

4. Selecteer op de pagina met de virtuele machineschaal set in de sectie **Monitoring** de optie **Insights** en selecteer **vervolgens Inschakelen**.

5. Als u op de pagina **Insights** een bestaande loganalytics-werkruimte wilt gebruiken, selecteert u deze in de vervolgkeuzelijst.

    De lijst selecteert vooraf de standaardwerkruimte en locatie waaraan de VM in het abonnement is geïmplementeerd. 

    ![Azure Monitor voor VM's inschakelen voor een virtuele machineschaalset](media/vminsights-enable-single-vm/enable-vminsights-vmss-portal.png)

    >[!NOTE]
    >Zie Een werkruimte voor [Logboekanalyse maken](../learn/quick-create-workspace.md)als u een nieuwe werkruimte Log Analytics wilt maken om de bewakingsgegevens van de virtuele machineschaalset op te slaan. Uw loganalytics-werkruimte moet deel uitmaken van een van de [ondersteunde regio's](vminsights-enable-overview.md#log-analytics).

6. U ontvangt statusberichten wanneer de configuratie wordt uitgevoerd.

    >[!NOTE]
    >Als u een handmatig upgrademodel gebruikt voor uw schaalset, upgradet u de instanties om de installatie te voltooien. U de upgrades starten vanaf de pagina **Instanties** in de sectie **Instellingen.**
    
    ![Azure Monitor voor VM's-controle-implementatieverwerking inschakelen](media/vminsights-enable-single-vm/onboard-vminsights-vmss-portal-status.png)

Nu u bewaking voor uw VM- of virtuele machineschaalset hebt ingeschakeld, is de bewakingsinformatie beschikbaar voor analyse in Azure Monitor voor VM's. 

## <a name="next-steps"></a>Volgende stappen

* Zie Azure Monitor voor [VM-toewijzing gebruiken](vminsights-maps.md)om gedetecteerde toepassingsafhankelijkheden weer te geven. 
* Zie Azure [VM-prestaties weergeven](vminsights-performance.md)om knelpunten, algemeen gebruik en de prestaties van uw VM te identificeren.
