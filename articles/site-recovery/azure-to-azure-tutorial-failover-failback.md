---
title: Failover uitvoeren en de virtuele Azure-machines die zijn gerepliceerd naar een secundaire Azure-regio voor herstel na nood gevallen met de Azure Site Recovery-service opnieuw beveiligen.
description: Meer informatie over het uitvoeren van een failover en het opnieuw beveiligen van virtuele Azure-machines die zijn gerepliceerd naar een secundaire Azure-regio voor herstel na nood gevallen, met de Azure Site Recovery-service.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 08/05/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 9bc0d25e19ad3412e62eb3386b0faf3ae5d2a444
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "68782594"
---
# <a name="fail-over-and-reprotect-azure-vms-between-regions"></a>Failover uitvoeren en Azure-Vm's tussen regio's opnieuw beveiligen

In deze zelf studie wordt beschreven hoe u een failover van een virtuele Azure-machine (VM) naar een secundaire Azure-regio met de [Azure site Recovery](site-recovery-overview.md) -service. Nadat u een failover hebt uitgevoerd, kunt u de virtuele machine opnieuw beveiligen. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een failover uitvoeren van de virtuele Azure-machine
> * Beveilig de secundaire virtuele machine van Azure opnieuw zodat deze wordt gerepliceerd naar de primaire regio.

> [!NOTE]
> Deze zelf studie bevat het eenvoudigste pad met de standaard instellingen en de minimale aanpassing. Gebruik voor complexere scenario's de artikelen onder ' How to ' voor Azure Vm's.


## <a name="prerequisites"></a>Vereisten

- Bekijk de [Veelgestelde vragen](site-recovery-faq.md#failover) over failover voordat u begint.
- Zorg dat u een [herstelanalyse](azure-to-azure-tutorial-dr-drill.md) hebt uitgevoerd om te controleren of alles werkt zoals verwacht.
- Controleer de eigenschappen van de virtuele machine voordat u de testfailover uitvoert. De virtuele machine moet voldoen aan [Azure-vereisten](azure-to-azure-support-matrix.md#replicated-machine-operating-systems).

## <a name="run-a-failover-to-the-secondary-region"></a>Een failover uitvoeren naar de secundaire regio

1. Selecteer in **Gerepliceerde items** de virtuele machine waarnaar u een failover wilt uitvoeren en selecteer **Failover**

   ![Failover](./media/azure-to-azure-tutorial-failover-failback/failover.png)

2. Selecteer in **failover**een **herstel punt** waarvoor u een failover wilt uitvoeren. U kunt een van de volgende opties gebruiken:

   * **Nieuwste** (standaard): verwerkt alle gegevens in de site Recovery-service en biedt de laagste beoogde herstel punt (RPO).
   * **Laatst verwerkte**: Hiermee wordt de virtuele machine teruggezet naar het laatste herstel punt dat door site Recovery service is verwerkt.
   * **Aangepast**: er wordt een failover uitgevoerd naar een bepaald herstel punt. Deze optie is handig voor het uitvoeren van een testfailover.

3. Selecteer **computer afsluiten voordat u de failover uitvoert** als u wilt dat site Recovery probeert om een afsluiting van de bron-vm's uit te voeren voordat de failover wordt geactiveerd. Als u dit afsluit, zorgt u ervoor dat er geen gegevens verloren gaan. De failover wordt voortgezet zelfs als het afsluiten is mislukt. Site Recovery de bron niet opschonen na een failover.

4. Volg de voortgang van de failover op de pagina **Taken**.

5. Valideer de virtuele machine na de failover door u aan te melden bij de virtuele machine. Als u naar een ander herstelpunt voor de virtuele machine wilt gaan, kunt u de optie **Herstelpunt wijzigen** gebruiken.

6. Wanneer u tevreden bent met de virtuele machine waarvoor u de failover hebt uitgevoerd, kunt u de failover **Doorvoeren**.
   Als u de failover doorvoert, worden alle herstelpunten die beschikbaar zijn voor de service verwijderd. U kunt het herstel punt nu niet meer wijzigen.

> [!NOTE]
> Wanneer u een failover van een virtuele machine wilt uitvoeren om een schijf toe te voegen nadat u replicatie voor de virtuele machine hebt ingeschakeld, worden in de replicatie punten de schijven weer gegeven die beschikbaar zijn voor herstel. Als een virtuele machine bijvoorbeeld één schijf heeft en u een nieuwe toevoegt, ziet u dat het replicatie punt bestaat uit ' 1 van 2 schijven ' voor replicatie punten die zijn gemaakt vóór het toevoegen van de schijf.

![Failover met een toegevoegde schijf](./media/azure-to-azure-tutorial-failover-failback/failover-added.png)

## <a name="reprotect-the-secondary-vm"></a>De secundaire virtuele machine opnieuw beveiligen

Na een failover van de virtuele machine moet u deze opnieuw beveiligen zodat deze terug naar de primaire regio wordt gerepliceerd.

1. Zorg ervoor dat de virtuele machine de status **Failover is doorgevoerd** heeft en controleer of de primaire regio beschikbaar is, en of u er nieuwe resources op kunt maken en deze kunt openen.
2. Klik in **kluis** > **gerepliceerde items**met de rechter muisknop op de virtuele machine waarvoor een failover is uitgevoerd en selecteer vervolgens **opnieuw beveiligen**.

   ![Klik met de rechtermuisknop om opnieuw te beveiligen](./media/azure-to-azure-tutorial-failover-failback/reprotect.png)

2. Controleer of de richting van beveiliging, secundaire naar primaire regio, al is geselecteerd.
3. Controleer de informatie over **resourcegroep, netwerk, opslag en beschikbaarheidssets**. Alle resources die als nieuw zijn gemarkeerd, worden gemaakt als onderdeel van de bewerking voor opnieuw beveiligen.
4. Klik op **OK** om het opnieuw beveiligen te activeren. Met deze taak wordt de doelsite van de meest recente gegevens voorzien. Vervolgens worden de verschillen naar de primaire regio gerepliceerd. De virtuele machine heeft nu een beveiligde status.

## <a name="next-steps"></a>Volgende stappen
- Na opnieuw [beveiligen kunt u een failback uitvoeren naar](azure-to-azure-tutorial-failback.md) de primaire regio wanneer deze beschikbaar is.
- Meer [informatie](azure-to-azure-how-to-reprotect.md#what-happens-during-reprotection) over de stroom voor opnieuw beveiligen.
