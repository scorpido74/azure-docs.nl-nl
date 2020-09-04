---
title: Een Azure VM-herstelanalyse uitvoeren met Azure Site Recovery
description: Leer hoe u een herstelanalyse kunt uitvoeren op een secundaire regio voor Azure-VM's met behulp van de Azure Site Recovery-service.
services: site-recovery
ms.topic: tutorial
ms.date: 01/16/2020
ms.custom: mvc
ms.openlocfilehash: b2ce157f0f192135ab0507e4aae4c0a282bda1ea
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/22/2020
ms.locfileid: "76166180"
---
# <a name="run-a-disaster-recovery-drill-to-a-secondary-region-for-azure-vms"></a>Een herstelanalyse uitvoeren op een secundaire regio van Azure-VM's

De [Azure Site Recovery](site-recovery-overview.md) service draagt bij aan uw strategie voor zakelijke continuïteit en noodherstel (BCDR) door te zorgen dat uw zakelijke apps beschikbaar blijven tijdens geplande en ongeplande uitval. Site Recovery beheert en orkestreert noodherstel van on-premises machines en virtuele Azure-machines (VM's), met inbegrip van replicatie, failover en herstel.

Deze tutorial laat zien hoe u een DR-herstelanalyse kunt uitvoeren voor een Azure-VM, van de ene Azure-regio naar de andere, met een testfailover. Een analyse valideert uw replicatiestrategie zonder gegevensverlies of uitvaltijd, en is niet van invloed op uw productieomgeving. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Vereisten controleren
> * Een testfailover uitvoeren voor één VM

> [!NOTE]
> Deze zelfstudie helpt u bij het uitvoeren van een herstelanalyse met minimale stappen. Voor meer informatie over de verschillende functies die betrekking hebben op het uitvoeren van een herstelanalyse raadpleegt u de documentatie voor Azure VM's [Replicatie](azure-to-azure-how-to-enable-replication.md), [Netwerken](azure-to-azure-about-networking.md), [Automatisering](azure-to-azure-powershell.md) of [Probleemoplossing](azure-to-azure-troubleshoot-errors.md).

## <a name="prerequisites"></a>Vereisten

Controleer de volgende punten voordat u deze zelfstudie uitvoert:

- Voordat u een testfailover uitvoert, wordt u aangeraden de eigenschappen van de virtuele machine te controleren om ervoor te zorgen dat deze is geconfigureerd voor herstel na noodgeval. Ga naar **Bewerkingen** > **Herstel na noodgeval** > **Eigenschappen** van de VM om de eigenschappen voor replicatie en failover weer te geven.
- **Wij raden u aan om een apart VM-netwerk te gebruiken voor de failover** en niet het standaardnetwerk dat is ingesteld toen u replicatie inschakelde.
- Afhankelijk van de configuraties voor bronnetwerken voor elke NIC, kunt u **Subnet**, **Privé-IP-adres**, **Openbare IP**, **Netwerkbeveiligingsgroep** of **Load balancer** opgeven om te koppelen met elke NIC onder testfailover-instellingen in **Berekening en netwerk** voordat u een herstelanalyse uitvoert.

## <a name="run-a-test-failover"></a>Een testfailover uitvoeren

In dit voorbeeld ziet u hoe u een Recovery Services-kluis gebruikt om een VM-testfailover uit te voeren.

1. Selecteer een kluis en ga naar **Beveiligde items** > **Gerepliceerde items** en selecteer een virtuele machine.
1. Selecteer in **Failover testen** een herstelpunt om voor de failover te gebruiken:
   - **Laatste**: hiermee worden alle gegevens in Site Recovery verwerkt en het laagste Recovery Time Objective (RTO) geboden.
   - **Laatst verwerkte**: voert een failover van de VM uit naar het laatste herstelpunt dat is verwerkt door Site Recovery. Het tijdstempel wordt weergegeven. Met deze optie wordt er geen tijd besteed aan het verwerken van gegevens, zodat er een lage RTO is.
   - **Laatste toepassingsconsistente punt**: met deze optie wordt er een failover uitgevoerd van alle VM's naar het laatste toepassingsconsistente herstelpunt. Het tijdstempel wordt weergegeven.
   - **Aangepast**: hiermee voert u een failover uit naar een bepaald herstelpunt. Aangepast is alleen beschikbaar wanneer u een failover uitvoert voor één virtuele machine en niet voor failover met een herstelplan.
1. Selecteer het virtuele Azure-doelnetwerk waaraan Azure VM's in de secundaire regio na de failover worden gekoppeld.

   > [!NOTE]
   > Als de instellingen van de testfailover vooraf zijn geconfigureerd voor het gerepliceerde item, is de vervolgkeuzelijst voor het selecteren van een virtueel Azure-netwerk niet zichtbaar.

1. Selecteer **OK** om de failover te starten. Als u de voortgang van de kluis wilt volgen, gaat u naar **Bewaking** > **Site Recovery-taken** en selecteert u de taak **Testfailover**.
1. Nadat de failover is voltooid, wordt de replica-Azure-VM weergegeven in **Virtuele machines** van Azure Portal. Controleer of de VM draait, de juiste grootte heeft en aangesloten is op het juiste netwerk.
1. Verwijder VM's die tijdens de testfailover zijn gemaakt door op het gerepliceerde item of het herstelplan op **Failovertest opschonen** te klikken. Leg in **Notities** eventuele opmerkingen over de testfailover vast en sla deze op.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een productie-failover uitvoeren](azure-to-azure-tutorial-failover-failback.md)
