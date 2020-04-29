---
title: Herstel na nood gevallen voor Azure VM instellen op een secundaire regio met Azure Site Recovery
description: Stel snel herstel na nood gevallen in op een andere Azure-regio voor een Azure-VM met behulp van de Azure Site Recovery-service.
ms.topic: quickstart
ms.date: 03/27/2020
ms.custom: mvc
ms.openlocfilehash: e26c2a1f24a88dc979f4ec68de65afc618740c00
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80371876"
---
# <a name="quickstart-set-up-disaster-recovery-to-a-secondary-azure-region-for-an-azure-vm"></a>Snelstartgids: herstel na nood gevallen instellen voor een secundaire Azure-regio voor een Azure-VM

De [Azure site Recovery](site-recovery-overview.md) -service draagt bij aan uw strategie voor bedrijfs continuïteit en herstel na nood gevallen (BCDR) door uw zakelijke toepassingen online te houden tijdens geplande en ongeplande uitval. Site Recovery kunt het herstel na nood gevallen van on-premises machines en Azure virtual machines (VM) beheren en organiseren, met inbegrip van replicatie, failover en herstel.

In deze Quick Start wordt beschreven hoe u herstel na nood gevallen instelt voor een virtuele Azure-machine door deze te repliceren naar een secundaire Azure-regio. In het algemeen worden de standaard instellingen gebruikt om replicatie in te scha kelen.

## <a name="prerequisites"></a>Vereisten

U hebt een Azure-abonnement en een VM nodig om deze zelf studie te volt ooien.

- Als u geen Azure-account hebt met een actief abonnement, kunt u [gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Een virtuele machine met mini maal 1 GB RAM wordt aanbevolen. Meer [informatie](/azure/virtual-machines/windows/quick-create-portal) over het maken van een virtuele machine.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de [Azure-portal](https://portal.azure.com).

## <a name="enable-replication-for-the-azure-vm"></a>Replicatie inschakelen voor de Azure-VM

Met de volgende stappen schakelt u VM-replicatie naar een secundaire locatie.

1. Selecteer in het menu Azure portal van**virtuele machines** **thuis** > een te repliceren virtuele machine.
1. In **bewerkingen** selecteert u **herstel na nood gevallen**.
1. Selecteer de doel regio in de**doel regio**voor **basis beginselen** > .
1. Als u de replicatie-instellingen wilt weer geven, selecteert u **controleren + replicatie starten**. Als u de standaard waarden wilt wijzigen, selecteert u **Geavanceerde instellingen**.
1. Als u de taak wilt starten waarmee VM-replicatie wordt ingeschakeld, selecteert u **replicatie starten**.

   :::image type="content" source="media/azure-to-azure-quickstart/enable-replication1.png" alt-text="Schakel replicatie in.":::

## <a name="verify-settings"></a>Instellingen controleren

Nadat de replicatie taak is voltooid, kunt u de replicatie status controleren, replicatie-instellingen wijzigen en de implementatie testen.

1. Selecteer **virtuele machines** in het menu Azure Portal en selecteer de virtuele machine die u hebt gerepliceerd.
1. In **bewerkingen** selecteert u **herstel na nood gevallen**.
1. Als u de replicatie details uit het **overzicht** wilt weer geven, selecteert u **essentiële**elementen. Meer details worden weer gegeven in **status en status**, **failover-gereedheid**en de **infrastructuur weergave** kaart.

   :::image type="content" source="media/azure-to-azure-quickstart/replication-status.png" alt-text="Replicatie status.":::

## <a name="clean-up-resources"></a>Resources opschonen

Als u de replicatie van de virtuele machine in de primaire regio wilt stoppen, moet u de replicatie uitschakelen:

- De bronreplicatie-instellingen worden automatisch opgeschoond.
- De Site Recovery-extensie die tijdens de replicatie op de virtuele machine is geïnstalleerd, wordt niet verwijderd.
- Site Recovery facturering voor de virtuele machine wordt gestopt.

Als u de replicatie wilt uitschakelen, voert u de volgende stappen uit:

1. Selecteer **virtuele machines** in het menu Azure Portal en selecteer de virtuele machine die u hebt gerepliceerd.
1. In **bewerkingen** selecteert u **herstel na nood gevallen**.
1. Selecteer in het **overzicht**de optie **replicatie uitschakelen**.
1. Als u de**uitbrei ding**van de site Recovery wilt verwijderen, gaat u naar de **instellingen** > van de VM-extensie.

   :::image type="content" source="media/azure-to-azure-quickstart/disable2-replication.png" alt-text="Schakel replicatie uit.":::

## <a name="next-steps"></a>Volgende stappen

In deze snelstartgids hebt u één VM gerepliceerd naar een secundaire regio. Vervolgens stelt u de replicatie in voor meerdere virtuele Azure-machines.

> [!div class="nextstepaction"]
> [Herstel na nood geval instellen voor virtuele Azure-machines](azure-to-azure-tutorial-enable-replication.md)
