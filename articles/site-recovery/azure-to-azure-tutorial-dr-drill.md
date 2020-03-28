---
title: Een Azure VM-noodherstelboor uitvoeren met Azure Site Recovery
description: Meer informatie over het uitvoeren van een noodhersteloefening naar een secundaire regio voor Azure VM's met behulp van de Azure Site Recovery-service.
services: site-recovery
ms.topic: tutorial
ms.date: 01/16/2020
ms.custom: mvc
ms.openlocfilehash: b2ce157f0f192135ab0507e4aae4c0a282bda1ea
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76166180"
---
# <a name="run-a-disaster-recovery-drill-to-a-secondary-region-for-azure-vms"></a>Een noodherstelboor uitvoeren naar een secundaire regio voor Azure VM's

De [Azure Site Recovery](site-recovery-overview.md) service draagt bij aan uw strategie voor zakelijke continuïteit en noodherstel (BCDR) door te zorgen dat uw zakelijke apps beschikbaar blijven tijdens geplande en ongeplande uitval. Site Recovery beheert en orkestreert noodherstel van on-premises machines en virtuele Azure-machines (VM's), met inbegrip van replicatie, failover en herstel.

Deze tutorial laat zien hoe u een DR-herstelanalyse kunt uitvoeren voor een Azure-VM, van de ene Azure-regio naar de andere, met een testfailover. Een analyse valideert uw replicatiestrategie zonder gegevensverlies of uitvaltijd, en is niet van invloed op uw productieomgeving. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Vereisten controleren
> * Een testfailover uitvoeren voor één VM

> [!NOTE]
> Deze zelfstudie helpt u om een noodhersteloefening uit te voeren met minimale stappen. Zie de documentatie voor Azure [VM-replicatie,](azure-to-azure-how-to-enable-replication.md) [netwerken,](azure-to-azure-about-networking.md) [automatisering](azure-to-azure-powershell.md)of [probleemoplossing](azure-to-azure-troubleshoot-errors.md)voor meer informatie over de verschillende functies die betrekking hebben op het uitvoeren van een noodhersteloefening.

## <a name="prerequisites"></a>Vereisten

Controleer de volgende items voordat u deze zelfstudie doet:

- Voordat u een testfailover uitvoert, raden we u aan de eigenschappen van de VM te controleren om te controleren of deze is geconfigureerd voor herstel na noodgevallen. Ga naar de **eigenschappen operations** > **disaster recovery** > **van** de VM om de replicatie- en failovereigenschappen weer te geven.
- **Wij raden u aan om een apart VM-netwerk te gebruiken voor de failover** en niet het standaardnetwerk dat is ingesteld toen u replicatie inschakelde.
- Afhankelijk van uw bronnetwerkconfiguraties voor elke NIC, u **Subnet,** **Privé-IP-adres,** **Openbaar IP,** **Netwerkbeveiligingsgroep**of **Load-balancer** opgeven om aan elke NIC te koppelen onder testfailoverinstellingen in **Compute en Netwerk** voordat u een noodherstelboor doet.

## <a name="run-a-test-failover"></a>Een testfailover uitvoeren

In dit voorbeeld ziet u hoe u een vault voor Herstelservices gebruikt om een mislukte VM-test uit te brengen.

1. Selecteer een kluis en ga naar **gerepliceerde items met beveiligde** > **items** en selecteer een vm.
1. Selecteer **in Test Failover**een herstelpunt dat u wilt gebruiken voor de failover:
   - **Laatste**: Verwerkt alle gegevens in Site Recovery en biedt de laagste RTO (Recovery Time Objective).
   - **Laatst verwerkte**: voert een failover van de VM uit naar het laatste herstelpunt dat is verwerkt door Site Recovery. Het tijdstempel wordt weergegeven. Met deze optie wordt geen tijd besteed aan het verwerken van gegevens, dus het biedt een lage RTO.
   - **Laatste toepassingsconsistente punt**: met deze optie wordt er een failover uitgevoerd van alle VM’s naar het laatste toepassingsconsistente herstelpunt. Het tijdstempel wordt weergegeven.
   - **Aangepast:** Niet naar een bepaald herstelpunt. Custom is alleen beschikbaar als u niet meer dan één virtuele machine hebt, en niet voor failover met een herstelplan.
1. Selecteer het doelazure-virtuele netwerk waarmee Azure VM's in het secundaire gebied verbinding maken na de failover.

   > [!NOTE]
   > Als de failoverinstellingen voor de test vooraf zijn geconfigureerd voor het gerepliceerde item, is het vervolgkeuzemenu om een virtueel Azure-netwerk te selecteren niet zichtbaar.

1. Als u de failover wilt starten, selecteert u **OK**. Als u de voortgang van de kluis wilt bijhouden, gaat u naar **Taken voor siteherstel** > **Site Recovery jobs** controleren en selecteert u de taak **Failover** testen.
1. Nadat de failover is voltooid, wordt de replica Azure VM weergegeven in de **virtuele machines**van de Azure-portal. Controleer of de VM draait, de juiste grootte heeft en aangesloten is op het juiste netwerk.
1. Als u de VM's wilt verwijderen die tijdens de failover van de test zijn gemaakt, selecteert u **Failover opschonen** op het gerepliceerde item of het herstelplan. **Noteer**en sla eventuele waarnemingen in verband met de testfailover op en sla deze op.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een productie-failover uitvoeren](azure-to-azure-tutorial-failover-failback.md)
