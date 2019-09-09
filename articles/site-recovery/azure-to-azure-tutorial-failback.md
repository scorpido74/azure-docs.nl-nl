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
ms.openlocfilehash: c8be547790452774992b9226ca8010532263aaff
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/09/2019
ms.locfileid: "70814513"
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
    - **Nieuwste (standaard)** : Verwerkt alle gegevens in de Site Recovery-service en biedt de laagste Recovery Point Objective (RPO).
    - **Laatst verwerkt**: Hiermee wordt de virtuele machine teruggezet naar het laatste herstel punt dat is verwerkt door Site Recovery.
    - **Aangepast**: Failover naar een bepaald herstel punt. Deze optie is handig voor het uitvoeren van een testfailover.
4. Selecteer **computer afsluiten voordat u de failover uitvoert** als u wilt dat site Recovery probeert de vm's in de Dr-regio te afsluiten voordat de failover wordt geactiveerd. De failover wordt voortgezet, zelfs als het afsluiten mislukt. 
5. Volg de voortgang van de failover op de pagina **Taken**.
6. Nadat de failover is voltooid, valideert u de virtuele machine door zich aan te melden. U kunt het herstel punt zo nodig wijzigen.
7. Nadat u de failover hebt geverifieerd, selecteert u **de failover door voeren**. Door het door voeren worden alle beschik bare herstel punten verwijderd. De optie herstel punt wijzigen is niet meer beschikbaar.
8. De VM moet als failover en failback worden weer gegeven.

    ![VM op primaire en secundaire regio's](./media/site-recovery-azure-to-azure-failback/azure-to-azure-failback-vm-view.png)

## <a name="next-steps"></a>Volgende stappen

Meer [informatie](azure-to-azure-how-to-reprotect.md#what-happens-during-reprotection) over de stroom voor opnieuw beveiligen.
