---
title: Failback van virtuele Azure-Vm's naar een secundaire Azure-regio voor herstel na nood gevallen met de Azure Site Recovery-service.
description: Meer informatie over het uitvoeren van back-ups van virtuele Azure-machines met de Azure Site Recovery-service.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 09/09/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 57f37808f3f90863079ba73f7899f142d32951d3
ms.sourcegitcommit: 3486e2d4eb02d06475f26fbdc321e8f5090a7fac
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/31/2019
ms.locfileid: "73242965"
---
# <a name="fail-back-an-azure-vm-between-azure-regions"></a>Een Azure-VM tussen Azure-regio's herstellen

De [Azure Site Recovery](site-recovery-overview.md)-service draagt bij aan uw strategie voor herstel na noodgeval, door de replicatie, failover en failback van on-premises machines en Azure-VM’s (virtuele machines) te beheren en in te delen.

In deze zelf studie wordt beschreven hoe u een failback van één virtuele Azure-machine kunt uitvoeren. Nadat u een failover hebt uitgevoerd, moet u een failback uitvoeren naar de primaire regio wanneer deze beschikbaar is. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> 
> * Een failback uitvoeren voor de virtuele machine in de secundaire regio.
> * De primaire VM opnieuw beveiligen naar de secundaire regio.
> 
> [!NOTE]
> 
> Deze zelf studie helpt u bij het uitvoeren van een failover van een aantal virtuele machines naar een doel regio en om terug te gaan naar de bron regio met minimale aanpassingen. Raadpleeg de [hand leidingen op Azure vm's](https://docs.microsoft.com/azure/virtual-machines/windows/)voor meer gedetailleerde instructies.

## <a name="before-you-start"></a>Voordat u begint

* Zorg ervoor dat de status van de virtuele machine wordt **doorgevoerd**in de failover.
* Controleer of de primaire regio beschikbaar is en of u nieuwe resources kunt maken en gebruiken.
* Zorg ervoor dat de beveiliging is ingeschakeld.

## <a name="fail-back-to-the-primary-region"></a>Een failback naar de primaire regio uitvoeren

Nadat de Vm's opnieuw zijn beveiligd, kunt u indien nodig een failback uitvoeren naar de primaire regio.

1. Selecteer in de kluis **gerepliceerde items**en selecteer vervolgens de VM die opnieuw is beveiligd.

    ![Failback naar primair](./media/site-recovery-azure-to-azure-failback/azure-to-azure-failback.png)

2. Selecteer de virtuele machine in **gerepliceerde items**en selecteer vervolgens **failover**.
3. Selecteer in **failover**een herstel punt waarvoor failover moet worden uitgevoerd:
    - **Nieuwste (standaard)** : verwerkt alle gegevens in de site Recovery-service en biedt de laagste Recovery Point OBJECTIVE (RPO).
    - **Laatst verwerkte**: Hiermee wordt de VM teruggezet naar het laatste herstel punt dat is verwerkt door site Recovery.
    - **Aangepast**: er wordt een failover uitgevoerd naar een bepaald herstel punt. Deze optie is handig voor het uitvoeren van een testfailover.
4. Selecteer **computer afsluiten voordat u de failover uitvoert** als u wilt dat site Recovery probeert de vm's in de Dr-regio te afsluiten voordat de failover wordt geactiveerd. De failover wordt voortgezet, zelfs als het afsluiten mislukt. 
5. Volg de voortgang van de failover op de pagina **Taken**.
6. Nadat de failover is voltooid, valideert u de virtuele machine door zich aan te melden. U kunt het herstel punt zo nodig wijzigen.
7. Nadat u de failover hebt geverifieerd, selecteert u **de failover door voeren**. Door het door voeren worden alle beschik bare herstel punten verwijderd. De optie herstel punt wijzigen is niet meer beschikbaar.
8. De VM moet als failover en failback worden weer gegeven.

    ![VM op primaire en secundaire regio's](./media/site-recovery-azure-to-azure-failback/azure-to-azure-failback-vm-view.png)

> [!NOTE]
> Voor machines met de Site Recovery extensie versie 9.28. x. x gaat u naar [Update pakket 40](https://support.microsoft.com/help/4521530/update-rollup-40-for-azure-site-recovery) site Recovery de computers in de secundaire nood herstel regio opschonen, nadat de failback is voltooid en de vm's opnieuw zijn beveiligd. Het is niet nodig om Vm's en Nic's hand matig te verwijderen in de secundaire regio. Als u de replicatie volledig uitschakelt nadat u een failback hebt uitgevoerd, Site Recovery de schijven in de nood herstel regio, naast de Vm's en Nic's, opschonen.

## <a name="next-steps"></a>Volgende stappen

Meer [informatie](azure-to-azure-how-to-reprotect.md#what-happens-during-reprotection) over de stroom voor opnieuw beveiligen.
