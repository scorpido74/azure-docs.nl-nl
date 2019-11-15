---
title: Een Azure VM-nood herstel analyse uitvoeren met Azure Site Recovery
description: Meer informatie over het uitvoeren van een nood herstel analyse naar een secundaire regio voor Azure-Vm's met behulp van de Azure Site Recovery-service.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/14/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 817a220e36ac250b1d5a5aa90d0bddbfb155cc26
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74091337"
---
# <a name="run-a-disaster-recovery-drill-to-a-secondary-region-for-azure-vms"></a>Een nood herstel analyse uitvoeren naar een secundaire regio voor virtuele Azure-machines 

De [Azure Site Recovery](site-recovery-overview.md) service draagt bij aan uw strategie voor zakelijke continuïteit en noodherstel (BCDR) door te zorgen dat uw zakelijke apps beschikbaar blijven tijdens geplande en ongeplande uitval. Site Recovery beheert en orkestreert noodherstel van on-premises machines en virtuele Azure-machines (VM's), met inbegrip van replicatie, failover en herstel.

Deze tutorial laat zien hoe u een DR-herstelanalyse kunt uitvoeren voor een Azure-VM, van de ene Azure-regio naar de andere, met een testfailover. Een analyse valideert uw replicatiestrategie zonder gegevensverlies of uitvaltijd, en is niet van invloed op uw productieomgeving. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Vereisten controleren
> * Een testfailover uitvoeren voor één VM

> [!NOTE]
> Deze zelf studie helpt u bij het uitvoeren van een DR-analyse met minimale stappen. Als u meer wilt weten over de verschillende aspecten van het uitvoeren van een DR-analyse, inclusief netwerk overwegingen, automatisering of probleem oplossing, raadpleegt u de documenten onder ' How to ' voor Azure Vm's.

## <a name="prerequisites"></a>Vereisten

- Voordat u een failovertest uitvoert, wordt u aangeraden de VM-eigenschappen te controleren of alles is zoals verwacht.  Ga naar de VM-eigenschappen in **Gerepliceerde items**. In de **Essentials**-blade ziet u informatie over de instellingen en status van machines.
- **Wij raden u aan om een apart VM-netwerk te gebruiken voor de failover** en niet het standaardnetwerk dat is ingesteld toen u replicatie inschakelde.
- Afhankelijk van de configuratie van de bron netwerken voor elke NIC, kunt u optioneel **subnet, IP-adres, openbaar IP, netwerk beveiligings groep of interne Load Balancer** opgeven om aan elke NIC te koppelen onder de instellingen voor de Testfailover in compute &-netwerk voordat Dr-analyse wordt verricht.


## <a name="run-a-test-failover"></a>Een testfailover uitvoeren

1. Klik in **Instellingen** > **Gerepliceerde items** op het pictogram **+Failover testen** van de VM.

2. Selecteer in **Failover testen** een herstelpunt om voor de failover te gebruiken:

    - **Nieuwste**: verwerkt alle gegevens in site Recovery en biedt de laagste RTO (Recovery Time doelstelling).
    - **Laatst verwerkte**: voert een failover van de VM uit naar het laatste herstelpunt dat is verwerkt door Site Recovery. Het tijdstempel wordt weergegeven. Met deze optie wordt er geen tijd besteed aan het verwerken van gegevens, zodat er een lage RTO (Recovery Time Objective) is
   - **Laatste toepassingsconsistente punt**: met deze optie wordt er een failover uitgevoerd van alle VM’s naar het laatste toepassingsconsistente herstelpunt. Het tijdstempel wordt weergegeven.
   - **Aangepast**: failover naar een bepaald herstel punt. Aangepast is alleen beschikbaar wanneer u een failover hebt uitgevoerd voor één virtuele machine en niet voor failover met een herstel plan.

3. Selecteer het virtuele Azure-doelnetwerk waarop Azure VM's in de secundaire regio na de failover worden aangesloten.

    > [!NOTE]
    > De vervolg keuzelijst voor het selecteren van een virtueel Azure-netwerk is niet zichtbaar als de failovertest voor het gerepliceerde item vooraf zijn geconfigureerd.

4. Klik op **OK** om de failover te starten. Klik op de VM om de eigenschappen ervan te openen als u de voortgang wilt volgen. U kunt ook op de taak **Failover testen** klikken in kluisnaam > **Instellingen** > **Taken** > **Site Recovery-taken**.
5. Nadat de failover is voltooid, wordt de replica-Azure-VM weergegeven in Azure Portal > **Virtuele machines**. Controleer of de VM draait, de juiste grootte heeft en aangesloten is op het juiste netwerk.
6. Verwijder VM's die tijdens de testfailover zijn gemaakt door op het gerepliceerde item of het herstelplan op **Failovertest opschonen** te klikken. Leg in **Notities** eventuele opmerkingen over de testfailover vast en sla deze op.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een productie-failover uitvoeren](azure-to-azure-tutorial-failover-failback.md)
