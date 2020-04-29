---
title: Een Azure VM-nood herstel analyse uitvoeren met Azure Site Recovery
description: Meer informatie over het uitvoeren van een nood herstel analyse naar een secundaire regio voor Azure-Vm's met behulp van de Azure Site Recovery-service.
services: site-recovery
ms.topic: tutorial
ms.date: 01/16/2020
ms.custom: mvc
ms.openlocfilehash: b2ce157f0f192135ab0507e4aae4c0a282bda1ea
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "76166180"
---
# <a name="run-a-disaster-recovery-drill-to-a-secondary-region-for-azure-vms"></a>Een nood herstel analyse uitvoeren naar een secundaire regio voor virtuele Azure-machines

De [Azure Site Recovery](site-recovery-overview.md) service draagt bij aan uw strategie voor zakelijke continuïteit en noodherstel (BCDR) door te zorgen dat uw zakelijke apps beschikbaar blijven tijdens geplande en ongeplande uitval. Site Recovery beheert en orkestreert noodherstel van on-premises machines en virtuele Azure-machines (VM's), met inbegrip van replicatie, failover en herstel.

Deze tutorial laat zien hoe u een DR-herstelanalyse kunt uitvoeren voor een Azure-VM, van de ene Azure-regio naar de andere, met een testfailover. Een analyse valideert uw replicatiestrategie zonder gegevensverlies of uitvaltijd, en is niet van invloed op uw productieomgeving. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Vereisten controleren
> * Een testfailover uitvoeren voor één VM

> [!NOTE]
> Deze zelf studie helpt u bij het uitvoeren van een nood herstel analyse met minimale stappen. Zie de documentatie voor Azure Vm's- [replicatie](azure-to-azure-how-to-enable-replication.md), [netwerken](azure-to-azure-about-networking.md), [automatisering](azure-to-azure-powershell.md)of [probleem oplossing](azure-to-azure-troubleshoot-errors.md)voor meer informatie over de verschillende functies die betrekking hebben op het uitvoeren van een nood herstel analyse.

## <a name="prerequisites"></a>Vereisten

Controleer de volgende items voordat u deze zelf studie volgt:

- Voordat u een testfailover uitvoert, wordt u aangeraden de eigenschappen van de virtuele machine te controleren om ervoor te zorgen dat deze is geconfigureerd voor herstel na nood gevallen. Ga naar de**Eigenschappen** voor **Operations** > **nood herstel** > van de virtuele machine om de eigenschappen voor replicatie en failover weer te geven.
- **Wij raden u aan om een apart VM-netwerk te gebruiken voor de failover** en niet het standaardnetwerk dat is ingesteld toen u replicatie inschakelde.
- Afhankelijk van de configuratie van de bron netwerken voor elke NIC, kunt u het **subnet**, het **persoonlijke IP-adres**, de **open bare IP**, de **netwerk beveiligings groep**of de **Load Balancer** toevoegen aan elke NIC onder failover-instellingen testen in **Compute en netwerk** , voordat u een nood herstel analyse uitvoert.

## <a name="run-a-test-failover"></a>Een testfailover uitvoeren

In dit voor beeld ziet u hoe u een Recovery Services kluis gebruikt om een VM-testfailover uit te voeren.

1. Selecteer een kluis en ga naar **beveiligde items** > **gerepliceerde items** en selecteer een virtuele machine.
1. Selecteer in **failover testen**het herstel punt dat u voor de failover wilt gebruiken:
   - **Nieuwste**: verwerkt alle gegevens in site Recovery en biedt de laagste RTO (Recovery Time doelstelling).
   - **Laatst verwerkte**: voert een failover van de VM uit naar het laatste herstelpunt dat is verwerkt door Site Recovery. Het tijdstempel wordt weergegeven. Met deze optie wordt er geen tijd besteed aan het verwerken van gegevens, zodat er een lage RTO beschikbaar is.
   - **Laatste toepassingsconsistente punt**: met deze optie wordt er een failover uitgevoerd van alle VM’s naar het laatste toepassingsconsistente herstelpunt. Het tijdstempel wordt weergegeven.
   - **Aangepast**: failover naar een bepaald herstel punt. Aangepast is alleen beschikbaar wanneer u een failover hebt uitgevoerd voor één virtuele machine en niet voor failover met een herstel plan.
1. Selecteer het virtuele Azure-doel netwerk dat door Azure Vm's in de secundaire regio wordt verbonden na de failover.

   > [!NOTE]
   > Als de instellingen van de testfailover vooraf zijn geconfigureerd voor het gerepliceerde item, is de vervolg keuzelijst voor het selecteren van een virtueel Azure-netwerk niet zichtbaar.

1. Selecteer **OK**om de failover te starten. Als u de voortgang van de kluis wilt volgen, gaat u naar **bewaking** > **site Recovery taken** en selecteert u de taak **testfailover** .
1. Nadat de failover is voltooid, wordt de replica van de virtuele machine van Azure weer gegeven in de **virtual machines**van de Azure Portal. Controleer of de VM draait, de juiste grootte heeft en aangesloten is op het juiste netwerk.
1. Als u de virtuele machines wilt verwijderen die tijdens de testfailover zijn gemaakt, selecteert u testfailover **opschonen** voor het gerepliceerde item of het herstel plan. Leg in **notities**eventuele opmerkingen over de testfailover vast en sla deze op.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een productie-failover uitvoeren](azure-to-azure-tutorial-failover-failback.md)
