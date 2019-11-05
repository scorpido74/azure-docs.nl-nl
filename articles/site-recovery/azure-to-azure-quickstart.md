---
title: Herstel na noodgeval instellen voor een Azure IaaS-VM’s naar een secundaire Azure-regio
description: Deze quickstart biedt de stappen die zijn vereist voor herstel na noodgeval van een Azure IaaS-VM tussen Azure-regio’s, met behulp van de Azure Site Recovery-service.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: quickstart
ms.date: 08/28/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: b06cae5bcb07831ba79b805802a7851c2b6ad2b3
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73468003"
---
# <a name="set-up-disaster-recovery-to-a-secondary-azure-region-for-an-azure-vm"></a>Herstel na noodgeval instellen naar een secundaire Azure-regio voor een Azure-VM        

De service [Azure Site Recovery](site-recovery-overview.md) draagt bij aan uw strategie voor zakelijke continuïteit en noodherstel (BCDR) door te zorgen dat uw zakelijke apps actief blijven tijdens geplande en ongeplande uitval. Site Recovery beheert en orkestreert noodherstel van on-premises machines en virtuele Azure-machines (VM's), met inbegrip van replicatie, failover en herstel.

In deze Quick Start wordt beschreven hoe u herstel na nood gevallen instelt voor een virtuele Azure-machine door deze te repliceren naar een andere Azure-regio.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

> [!NOTE]
> Dit artikel is een snelle walkthrough voor nieuwe gebruikers. Het eenvoudigste pad wordt gebruikt, met de standaard opties en de minimale aanpassing.  Bekijk [onze zelf studie](azure-to-azure-tutorial-enable-replication.md)voor een uitgebreid overzicht.

## <a name="log-in-to-azure"></a>Meld u aan bij Azure.

Meld u aan bij de Azure-portal op https://portal.azure.com.

## <a name="enable-replication-for-the-azure-vm"></a>Replicatie inschakelen voor de Azure-VM

1. Selecteer **virtuele machines**in het menu Azure portal of zoek en selecteer *virtuele machines* op een wille keurige pagina. Selecteer de virtuele machine die u wilt repliceren.
2. Selecteer in **Bewerkingen** de optie **Herstel na noodgeval**.
3. Selecteer in **Noodherstel configureren** > **Doelregio** de doelregio waarnaar u wilt repliceren.
4. Accepteer voor deze snelstartgids de overige standaardinstellingen.
5. Selecteer **evalueren en replicatie starten**. Selecteer vervolgens **replicatie starten** om een taak te starten om replicatie in te scha kelen voor de virtuele machine.

    ![replicatie inschakelen](media/azure-to-azure-quickstart/enable-replication1.png)

## <a name="verify-settings"></a>Instellingen controleren

Nadat de replicatietaak is voltooid, kunt u de replicatiestatus controleren, de replicatie-instellingen wijzigen en de implementatie testen.

1. Selecteer **virtuele machines**in het menu Azure portal of zoek en selecteer *virtuele machines* op een wille keurige pagina. Selecteer de virtuele machine die u wilt controleren.
2. Selecteer in **Bewerkingen** de optie **Herstel na noodgeval**.

   U kunt de replicatiestatus, de gemaakte herstelpunten, en de bron- en doelregio's op de kaart controleren.

   ![Replicatiestatus](media/azure-to-azure-quickstart/replication-status.png)

## <a name="clean-up-resources"></a>Resources opschonen

De VM in de primaire regio stopt met repliceren wanneer u replicatie uitschakelt:

- De bronreplicatie-instellingen worden automatisch opgeschoond. De uitbrei ding Site Recovery die als onderdeel van de replicatie op de virtuele machine is geïnstalleerd, wordt niet verwijderd en moet hand matig worden verwijderd. 
- Site Recovery facturering voor de virtuele machine wordt gestopt.

Stop de replicatie als volgt

1. Selecteer **virtuele machines**in het menu Azure portal of zoek en selecteer *virtuele machines* op een wille keurige pagina. Selecteer de virtuele machine die u wilt wijzigen.
2. Selecteer **replicatie uitschakelen**bij **herstel na nood gevallen**.

   ![Replicatie uitschakelen](media/azure-to-azure-quickstart/disable2-replication.png)

## <a name="next-steps"></a>Volgende stappen

In deze snelstartgids hebt u één VM gerepliceerd naar een secundaire regio. Probeer nu een Azure-virtuele machine te repliceren met een herstel plan.

> [!div class="nextstepaction"]
> [Herstel na noodgeval voor Azure-VM’s configureren](azure-to-azure-tutorial-enable-replication.md)
