---
title: Herstel na nood gevallen voor Azure VM instellen op een secundaire regio met Azure Site Recovery
description: Stel snel herstel na nood gevallen in op een andere Azure-regio voor een Azure-VM met behulp van de Azure Site Recovery-service.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: quickstart
ms.date: 01/08/2020
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: de4d3ce11e23d7ec4f6ad26852e7d7d01eebe590
ms.sourcegitcommit: 5b073caafebaf80dc1774b66483136ac342f7808
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/09/2020
ms.locfileid: "75780008"
---
# <a name="set-up-disaster-recovery-to-a-secondary-azure-region-for-an-azure-vm"></a>Herstel na noodgeval instellen naar een secundaire Azure-regio voor een Azure-VM

De service [Azure Site Recovery](site-recovery-overview.md) draagt bij aan uw strategie voor zakelijke continuïteit en noodherstel (BCDR) door te zorgen dat uw zakelijke apps actief blijven tijdens geplande en ongeplande uitval. Site Recovery beheert en orkestreert noodherstel van on-premises machines en virtuele Azure-machines (VM's), met inbegrip van replicatie, failover en herstel.

In deze Quick Start wordt beschreven hoe u herstel na nood gevallen instelt voor een virtuele Azure-machine door deze te repliceren naar een andere Azure-regio.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

> [!NOTE]
> Dit artikel bevat een kort overzicht van nieuwe gebruikers. Het eenvoudigste pad wordt gebruikt, met de standaard opties en de minimale aanpassing. Raadpleeg de zelf studie [replicatie inschakelen](azure-to-azure-tutorial-enable-replication.md)voor een volledige walkthrough.

## <a name="log-in-to-azure"></a>Meld u aan bij Azure.

Meld u aan bij [Azure Portal](https://portal.azure.com).

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

Stop de replicatie als volgt:

1. Selecteer **virtuele machines**in het menu Azure portal of zoek en selecteer *virtuele machines* op een wille keurige pagina. Selecteer de virtuele machine die u wilt wijzigen.
2. Selecteer **replicatie uitschakelen**bij **herstel na nood gevallen**.

   ![Replicatie uitschakelen](media/azure-to-azure-quickstart/disable2-replication.png)

## <a name="next-steps"></a>Volgende stappen

In deze snelstartgids hebt u één VM gerepliceerd naar een secundaire regio. Probeer nu meerdere virtuele Azure-machines te repliceren met een herstel plan.

> [!div class="nextstepaction"]
> [Herstel na nood geval instellen voor virtuele Azure-machines](azure-to-azure-tutorial-enable-replication.md)
