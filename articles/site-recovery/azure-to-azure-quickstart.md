---
title: Azure VM-noodherstel instellen op een secundaire regio met Azure Site Recovery
description: Stel snel herstel van noodgevallen in op een andere Azure-regio voor een Azure-vm met de Azure Site Recovery-service.
ms.topic: quickstart
ms.date: 03/27/2020
ms.custom: mvc
ms.openlocfilehash: e26c2a1f24a88dc979f4ec68de65afc618740c00
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371876"
---
# <a name="quickstart-set-up-disaster-recovery-to-a-secondary-azure-region-for-an-azure-vm"></a>Snelstart: noodherstel instellen op een secundairazure-gebied voor een Azure-vm

De [Azure Site Recovery-service](site-recovery-overview.md) draagt bij aan uw BCDR-strategie (Business Continuity and Disaster Recovery) door uw bedrijfstoepassingen online te houden tijdens geplande en ongeplande uitval. Site Recovery beheert en orkestreert noodherstel van on-premises machines en Virtuele Azure-machines (VM), inclusief replicatie, failover en herstel.

In deze snelstart wordt beschreven hoe u noodherstel voor een Azure VM instelt door deze te repliceren naar een secundairAzure-gebied. In het algemeen worden standaardinstellingen gebruikt om replicatie in te schakelen.

## <a name="prerequisites"></a>Vereisten

Als u deze zelfstudie wilt voltooien, hebt u een Azure-abonnement en een VM nodig.

- Als u geen Azure-account met een actief abonnement hebt, u [gratis een account aanmaken.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Een VM met minimaal 1 GB RAM wordt aanbevolen. [Meer informatie](/azure/virtual-machines/windows/quick-create-portal) over het maken van een virtuele machine.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij [Azure Portal](https://portal.azure.com).

## <a name="enable-replication-for-the-azure-vm"></a>Replicatie inschakelen voor de Azure-VM

Met de volgende stappen u VM-replicatie inschakelen naar een secundaire locatie.

1. Selecteer in de Azure-portal, vanuit het menu**Virtuele startapparaten,** **Home** > een VM die u wilt repliceren.
1. Selecteer **In Operations** Disaster **recovery**.
1. Selecteer in het**doelgebied** **Basisdoelen** > het doelgebied.
1. Als u de replicatie-instellingen wilt weergeven, selecteert u **Revisie + Replicatie starten**. Als u standaardinstellingen wilt wijzigen, selecteert u **Geavanceerde instellingen**.
1. Als u de taak wilt starten waarmee vm-replicatie wordt ingemaakt, selecteert u **Replicatie starten**.

   :::image type="content" source="media/azure-to-azure-quickstart/enable-replication1.png" alt-text="Replicatie inschakelen.":::

## <a name="verify-settings"></a>Instellingen controleren

Nadat de replicatietaak is voltooid, u de replicatiestatus controleren, replicatie-instellingen wijzigen en de implementatie testen.

1. Selecteer **virtuele machines** in het menu Azure portal en selecteer de VM die u hebt gerepliceerd.
1. Selecteer **In Operations** Disaster **recovery**.
1. Als u de replicatiegegevens wilt weergeven in **het overzicht** selecteert u **Essentials**. Meer details worden weergegeven in de **status en status**, **Failover-gereedheid**en de kaart van de **infrastructuurweergave.**

   :::image type="content" source="media/azure-to-azure-quickstart/replication-status.png" alt-text="Replicatiestatus.":::

## <a name="clean-up-resources"></a>Resources opschonen

Als u de replicatie van de vm in het primaire gebied wilt stoppen, moet u replicatie uitschakelen:

- De bronreplicatie-instellingen worden automatisch opgeschoond.
- De extensie Siteherstel die tijdens de replicatie op de vm is geïnstalleerd, wordt niet verwijderd.
- Facturering voor siteherstel voor de VM stopt.

Ga als volgt te werk om replicatie uit te schakelen:

1. Selecteer **virtuele machines** in het menu Azure portal en selecteer de VM die u hebt gerepliceerd.
1. Selecteer **In Operations** Disaster **recovery**.
1. Selecteer **Replicatie uitschakelen**in het **overzicht**.
1. Als u de extensie Siteherstel wilt verwijderen, gaat u naar de > **instellingen-extensies**van de VM . **Settings**

   :::image type="content" source="media/azure-to-azure-quickstart/disable2-replication.png" alt-text="Replicatie uitschakelen.":::

## <a name="next-steps"></a>Volgende stappen

In deze snelstartgids hebt u één VM gerepliceerd naar een secundaire regio. Stel vervolgens replicatie in voor meerdere Azure VM's.

> [!div class="nextstepaction"]
> [Disaster recovery instellen voor Azure VM's](azure-to-azure-tutorial-enable-replication.md)
