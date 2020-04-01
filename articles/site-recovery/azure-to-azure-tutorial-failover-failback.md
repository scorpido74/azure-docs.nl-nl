---
title: Azure VM's die zijn gerepliceerd naar een secundairAzure-gebied opnieuw beveiligen en opnieuw beveiligen voor herstel na noodgevallen met de Azure Site Recovery-service.
description: Lees hoe u Azure VM's overwerken en opnieuw beveiligen, gerepliceerd naar een secundairAzure-gebied voor herstel na noodgevallen, met de Azure Site Recovery-service.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 08/05/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 9bc0d25e19ad3412e62eb3386b0faf3ae5d2a444
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "68782594"
---
# <a name="fail-over-and-reprotect-azure-vms-between-regions"></a>Azure VM's tussen regio's niet meer beveiligen en beveiligen

In deze zelfstudie wordt beschreven hoe u falen over een virtuele Azure-machine (VM) naar een secundairazure-gebied met de [Azure Site Recovery-service.](site-recovery-overview.md) Nadat u hebt gefaald, beschermt u de vm opnieuw. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een failover uitvoeren van de virtuele Azure-machine
> * Bescherm de secundaire Azure VM opnieuw, zodat deze wordt gerepliceerd naar het primaire gebied.

> [!NOTE]
> Deze zelfstudie bevat het eenvoudigste pad met standaardinstellingen en minimale aanpassing. Voor complexere scenario's gebruikt u de artikelen onder 'How To' voor Azure VM's.


## <a name="prerequisites"></a>Vereisten

- Voordat u begint, bekijkt u [veelgestelde vragen](site-recovery-faq.md#failover) over failover.
- Zorg dat u een [herstelanalyse](azure-to-azure-tutorial-dr-drill.md) hebt uitgevoerd om te controleren of alles werkt zoals verwacht.
- Controleer de eigenschappen van de virtuele machine voordat u de testfailover uitvoert. De virtuele machine moet voldoen aan [Azure-vereisten](azure-to-azure-support-matrix.md#replicated-machine-operating-systems).

## <a name="run-a-failover-to-the-secondary-region"></a>Een failover uitvoeren naar de secundaire regio

1. Selecteer in **Gerepliceerde items** de virtuele machine waarnaar u een failover wilt uitvoeren en selecteer **Failover**

   ![Failover](./media/azure-to-azure-tutorial-failover-failback/failover.png)

2. Selecteer **in Failover**een **herstelpunt** om niet naar over te gaan. U kunt een van de volgende opties gebruiken:

   * **Laatste** (standaard): Verwerkt alle gegevens in de siteherstelservice en biedt de laagste Herstelpuntdoelstelling (RPO).
   * **Laatste verwerkt**: Hiermee wordt de virtuele machine teruggezet naar het laatste herstelpunt dat is verwerkt door de siteherstelservice.
   * **Aangepast:** mislukt naar een bepaald herstelpunt. Deze optie is handig voor het uitvoeren van een testfailover.

3. Selecteer De machine afsluiten voordat u **failover begint** als u wilt dat Siteherstel probeert de bron-VM's uit te schakelen voordat de failover wordt geactiveerd. Shutdown zorgt ervoor dat er geen gegevens verloren gaan. De failover wordt voortgezet zelfs als het afsluiten is mislukt. Site Recovery ruimt de bron niet op na een failover.

4. Volg de voortgang van de failover op de pagina **Taken**.

5. Valideer de virtuele machine na de failover door u aan te melden bij de virtuele machine. Als u naar een ander herstelpunt voor de virtuele machine wilt gaan, kunt u de optie **Herstelpunt wijzigen** gebruiken.

6. Wanneer u tevreden bent met de virtuele machine waarvoor u de failover hebt uitgevoerd, kunt u de failover **Doorvoeren**.
   Als u de failover doorvoert, worden alle herstelpunten die beschikbaar zijn voor de service verwijderd. U het herstelpunt nu niet meer wijzigen.

> [!NOTE]
> Wanneer u niet slaagt boven een virtuele machine waaraan u een schijf toevoegt nadat u replicatie voor de vm hebt ingeschakeld, worden de schijven weergegeven die beschikbaar zijn voor herstel. Als een virtuele machine bijvoorbeeld één schijf heeft en u een nieuwe schijf toevoegt, geven replicatiepunten die zijn gemaakt voordat u de schijf hebt toegevoegd, aan dat het replicatiepunt bestaat uit '1 van 2 schijven'.

![Fail over met een toegevoegde schijf](./media/azure-to-azure-tutorial-failover-failback/failover-added.png)

## <a name="reprotect-the-secondary-vm"></a>De secundaire virtuele machine opnieuw beveiligen

Na een failover van de virtuele machine moet u deze opnieuw beveiligen zodat deze terug naar de primaire regio wordt gerepliceerd.

1. Zorg ervoor dat de virtuele machine de status **Failover is doorgevoerd** heeft en controleer of de primaire regio beschikbaar is, en of u er nieuwe resources op kunt maken en deze kunt openen.
2. Klik in > **gevault-gerepliceerde items**met de rechtermuisknop op de vm die is mislukt en selecteer **Opnieuw beveiligen**. **Vault**

   ![Klik met de rechtermuisknop om opnieuw te beveiligen](./media/azure-to-azure-tutorial-failover-failback/reprotect.png)

2. Controleer of de beschermingsrichting, secundair aan het primaire gebied, al is geselecteerd.
3. Controleer de informatie over **resourcegroep, netwerk, opslag en beschikbaarheidssets**. Alle resources die als nieuw zijn gemarkeerd, worden gemaakt als onderdeel van de bewerking reprotect.
4. Klik op **OK** om het opnieuw beveiligen te activeren. Met deze taak wordt de doelsite van de meest recente gegevens voorzien. Vervolgens worden de verschillen naar de primaire regio gerepliceerd. De virtuele machine heeft nu een beveiligde status.

## <a name="next-steps"></a>Volgende stappen
- Na het opnieuw beveiligen, [leert u hoe](azure-to-azure-tutorial-failback.md) u falen naar de primaire regio wanneer deze beschikbaar is.
- [Meer informatie](azure-to-azure-how-to-reprotect.md#what-happens-during-reprotection) over de reprotectiestroom.
