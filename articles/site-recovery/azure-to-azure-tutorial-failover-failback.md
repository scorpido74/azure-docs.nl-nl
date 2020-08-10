---
title: Een failover-overschakeling uitvoeren voor Azure-VMs die worden gerepliceerd naar een secundaire Azure-regio als onderdeel van herstel na een noodgeval met de Azure Site Recovery-service en deze VM's opnieuw beveiligen.
description: Lees hier hoe u een failover-overschakeling uitvoert voor Azure-VMs die worden gerepliceerd naar een secundaire Azure-regio als onderdeel van herstel na een noodgeval met de Azure Site Recovery-service en deze VM's opnieuw beveiligen.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 08/05/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 8d38aa513b0829c2626fcd4a92c40faabff1f83e
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/31/2020
ms.locfileid: "87502389"
---
# <a name="fail-over-and-reprotect-azure-vms-between-regions"></a>Failover-overschakeling uitvoeren en Azure-VM's opnieuw beveiligen tussen regio's

In deze zelfstudie wordt beschreven hoe u een failover-overschakeling uitvoert voor een virtuele Azure-machine (VM) naar een secundaire Azure-regio met de [Azure Site Recovery](site-recovery-overview.md) service. Nadat u de failover hebt uitgevoerd, gaat u de VM opnieuw beveiligen. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een failover uitvoeren van de virtuele Azure-machine
> * De secundaire virtuele Azure-machine opnieuw beveiligen, zodat deze wordt gerepliceerd naar de primaire regio

> [!NOTE]
> Deze zelfstudie bevat het eenvoudigste traject, met standaardinstellingen en minimale aanpassing. Gebruik voor complexere scenario's de artikelen met procedures voor Azure-VM's.


## <a name="prerequisites"></a>Vereisten

- Bekijk [de veelgestelde vragen](site-recovery-faq.md#failover) over failover voordat u begint.
- Zorg dat u een [herstelanalyse](azure-to-azure-tutorial-dr-drill.md) hebt uitgevoerd om te controleren of alles werkt zoals verwacht.
- Controleer de eigenschappen van de virtuele machine voordat u de testfailover uitvoert. De virtuele machine moet voldoen aan [Azure-vereisten](azure-to-azure-support-matrix.md#replicated-machine-operating-systems).

## <a name="run-a-failover-to-the-secondary-region"></a>Een failover uitvoeren naar de secundaire regio

1. Selecteer in **Gerepliceerde items** de virtuele machine waarnaar u een failover wilt uitvoeren en selecteer **Failover**

   ![Schermopname met de failover-opties voor een VM.](./media/azure-to-azure-tutorial-failover-failback/failover.png)

2. Selecteer in **Failover** een **Herstelpunt** waarnaar u de failover wilt uitvoeren. U kunt een van de volgende opties gebruiken:

   * **Meest recent** (standaardwaarde): hiermee worden alle gegevens in de Site Recovery-service verwerkt en het laagste Recovery Point Objective (RPO) geboden.
   * **Laatst verwerkt**: Hiermee wordt de virtuele machine teruggezet naar het meest recente herstelpunt dat door de Site Recovery-service is verwerkt.
   * **Aangepast**: voert een failover uit naar een bepaald herstelpunt. Deze optie is handig voor het uitvoeren van een testfailover.

3. Selecteer **Sluit de computer af voordat de failover wordt gestart** als u wilt dat Site Recovery probeert bron-VM's af te sluiten voordat de failover wordt geactiveerd. Deze optie zorgt ervoor dat er geen gegevens verloren gaan. De failover wordt voortgezet zelfs als het afsluiten is mislukt. Site Recovery schoont de bron niet op na een failover.

4. Volg de voortgang van de failover op de pagina **Taken**.

5. Valideer de virtuele machine na de failover door u aan te melden bij de virtuele machine. Als u naar een ander herstelpunt voor de virtuele machine wilt gaan, kunt u de optie **Herstelpunt wijzigen** gebruiken.

6. Wanneer u tevreden bent met de virtuele machine waarvoor u de failover hebt uitgevoerd, kunt u de failover **Doorvoeren**.
   Als u de failover doorvoert, worden alle herstelpunten die beschikbaar zijn voor de service verwijderd. U kunt het herstelpunt nu niet meer wijzigen.

> [!NOTE]
> Wanneer u een failover uitvoert voor een VM waaraan u een schijf hebt toegevoegd na het inschakelen van replicatie voor de VM, bevatten replicatiepunten de schijven die beschikbaar zijn voor herstel. Als een VM bijvoorbeeld één schijf heeft en u een nieuwe toevoegt, ziet u in replicatiepunten die zijn gemaakt vóór het toevoegen van de schijf, dat het replicatiepunt bestaat uit '1 van 2 schijven'.

![Schermopname met een failover met een toegevoegde schijf.](./media/azure-to-azure-tutorial-failover-failback/failover-added.png)

## <a name="reprotect-the-secondary-vm"></a>De secundaire virtuele machine opnieuw beveiligen

Na een failover van de virtuele machine moet u deze opnieuw beveiligen zodat deze terug naar de primaire regio wordt gerepliceerd.

1. Zorg ervoor dat de virtuele machine de status **Failover is doorgevoerd** heeft en controleer of de primaire regio beschikbaar is, en of u er nieuwe resources op kunt maken en deze kunt openen.
2. Klik in **Kluis** > **Gerepliceerde items** met de rechtermuisknop op de virtuele machine waarvoor de failover is uitgevoerd is en selecteer vervolgens **Opnieuw beveiligen**.

   ![Schermopname van de optie voor het opnieuw beveiligen van een VM.](./media/azure-to-azure-tutorial-failover-failback/reprotect.png)

2. Controleer of de richting van beveiliging, secundaire naar primaire regio, al is geselecteerd.
3. Controleer de informatie over **resourcegroep, netwerk, opslag en beschikbaarheidssets**. Alle resources die zijn gemarkeerd als nieuw worden gemaakt als onderdeel van het opnieuw beveiligen.
4. Klik op **OK** om het opnieuw beveiligen te activeren. Met deze taak wordt de doelsite van de meest recente gegevens voorzien. Vervolgens worden de verschillen naar de primaire regio gerepliceerd. De virtuele machine heeft nu een beveiligde status.

## <a name="next-steps"></a>Volgende stappen
- Nadat u een VM opnieuw hebt beveiligd, kunt u [hier lezen](azure-to-azure-tutorial-failback.md) hoe u een failback uitvoert naar de primaire regio uitvoeren wanneer deze beschikbaar is.
- [Meer informatie](azure-to-azure-how-to-reprotect.md#what-happens-during-reprotection) over de stroom voor opnieuw beveiligen.
