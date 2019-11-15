---
title: Failover en failback instellen voor fysieke servers met Site Recovery
description: Meer informatie over het uitvoeren van een failover van fysieke servers naar Azure en het uitvoeren van een failback naar de on-premises site voor herstel na nood gevallen met Azure Site Recovery
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: 2c0d2e57a34286f65be45a95403a32de42c51908
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74084574"
---
# <a name="fail-over-and-fail-back-physical-servers-replicated-to-azure"></a>Failover en failback van fysieke servers die zijn gerepliceerd naar Azure

In deze zelf studie wordt beschreven hoe u een failover van een fysieke server naar Azure kunt uitvoeren. Nadat u een failover hebt uitgevoerd, wordt de server niet meer teruggestuurd naar uw on-premises site wanneer deze beschikbaar is.

## <a name="preparing-for-failover-and-failback"></a>Voorbereiden voor failover en failback

Fysieke servers die worden gerepliceerd naar Azure met Site Recovery kunnen alleen failback uitvoeren als virtuele VMware-machines. U hebt een VMware-infra structuur nodig om een failback uit te kunnen laten.

Failover en failback bestaat uit vier fasen:

1. **Failover naar Azure**: failover-overschakeling uitvoeren van computers van de on-premises site naar Azure.
2. **Azure-vm's opnieuw beveiligen**: Beveilig de Azure vm's opnieuw, zodat ze weer worden gerepliceerd naar on-premises virtuele VMware-machines.
3. **Failover naar on-premises**: voer een failover-overschakeling uit om een failback uit te voeren van Azure.
4. **On-premises vm's opnieuw beveiligen**: nadat de gegevens zijn hersteld, moet u de on-premises VMware-vm's die u hebt teruggezet opnieuw beveiligen, zodat ze repliceren naar Azure.

## <a name="verify-server-properties"></a>Server eigenschappen controleren

Controleer de server eigenschappen en controleer of deze voldoet aan de [Azure-vereisten](vmware-physical-azure-support-matrix.md#replicated-machines) voor Azure-vm's.

1. Klik in **beveiligde items**op **gerepliceerde items**en selecteer de computer.

2. In het deel venster **gerepliceerd item** bevindt zich een samen vatting van de computer gegevens, de integriteits status en de meest recente beschik bare herstel punten. Klik op **Eigenschappen** om meer details te bekijken.
3. In **Berekening en netwerk** kunt u de Azure-naam, de resourcegroep, de doelgrootte, [beschikbaarheidsset](../virtual-machines/windows/tutorial-availability-sets.md) en beheerde-schijfinstellingen wijzigen
4. U kunt de netwerkinstellingen bekijken en wijzigen, inclusief het netwerk-/subnet waarin de Azure VM zich na failover bevindt en het IP-adres dat eraan wordt toegewezen.
5. In **schijven**kunt u informatie over het besturings systeem en de gegevens schijven van de machine bekijken.

## <a name="run-a-failover-to-azure"></a>Een failover naar Azure uitvoeren

1. Klik in **Instellingen** > **Gerepliceerde items** op de machine > **Failover**.
2. Selecteer in **Failover** een **Herstelpunt** waarnaar u de failover wilt uitvoeren. U kunt een van de volgende opties gebruiken:
   - **Laatste**: met deze optie worden eerst alle gegevens naar Site Recovery verzonden gegevens verwerkt. Dit biedt het laagste RPO (Recovery Point Objective), omdat de na de failover gemaakte Azure-VM alle gegevens heeft die naar Site Recovery is gerepliceerd toen de failover werd geactiveerd.
   - **Laatst verwerkte**: met deze optie wordt er een failover uitgevoerd van de machine naar het laatste herstel punt dat is verwerkt door site Recovery. Deze optie heeft een lage RTO (Recovery Time Objective), omdat er geen tijd wordt besteed aan het verwerken van niet-verwerkte gegevens.
   - **Nieuwste app-consistent**: met deze optie kan de computer worden overgeschakeld naar het nieuwste toepassings consistente herstel punt dat is verwerkt door site Recovery.
   - **Aangepast**: geef een herstelpunt op.

3. Selecteer **Afsluiten machine voordat u de failover start** als u wilt dat site Recovery probeert de bron machine af te sluiten voordat de failover wordt geactiveerd. De failover wordt voortgezet zelfs als het afsluiten is mislukt. U kunt de voortgang van de failover volgen op de pagina **Taken**.
4. Als u de verbinding met de Azure VM hebt voorbereid, maakt u na de failover verbinding om deze te valideren.
5. Na het verifiëren kunt u de failover **Doorvoeren**. Hiermee verwijdert u alle beschikbare herstelpunten.

> [!WARNING]
> Een failover wordt niet geannuleerd. Voordat de failover wordt gestart, wordt de machine replicatie gestopt. Als u de failover annuleert, wordt deze gestopt, maar de computer wordt niet opnieuw gerepliceerd.
> Voor fysieke servers kan een extra failover-verwerking rond acht tot tien minuten duren.

## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Voorbereiden op het verbinden met virtuele Azure-machines na een failover

Als u verbinding wilt maken met virtuele Azure-machines met behulp van RDP/SSH na een failover, volgt u de procedure die [hier](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover) wordt beschreven.

Volg de stappen die [hier](site-recovery-failover-to-azure-troubleshoot.md) worden beschreven om eventuele verbindingsproblemen na een failover op te lossen.

## <a name="create-a-process-server-in-azure"></a>Een processerver maken in Azure

De processerver ontvangt gegevens van de Azure VM en verzendt deze naar de on-premises site. Er is een netwerk met lage latentie vereist tussen de proces server en de beveiligde machine.

- Als u een Azure ExpressRoute-verbinding hebt, kunt u voor testdoeleinden de on-premises processerver gebruiken die automatisch wordt geïnstalleerd op de configuratieserver.
- Als u een VPN-verbinding hebt of als u failback uitvoert in een productieomgeving, moet u een Azure-VM instellen als een Azure-processerver voor failback.
- Volg de instructies in [dit artikel](vmware-azure-set-up-process-server-azure.md) voor het instellen van een proces server in Azure.

## <a name="configure-the-master-target-server"></a>De hoofddoelserver configureren

De hoofddoel server ontvangt standaard failback-gegevens. Het wordt uitgevoerd op de on-premises configuratie server.

- Als de virtuele VMware-machine waarvoor u een failback hebt uitgevoerd, zich op een ESXi-host bevindt die wordt beheerd door VMware vCenter Server, moet de hoofddoel server toegang hebben tot de gegevens opslag (VMDK) van de virtuele machine om gerepliceerde gegevens naar de VM-schijven te schrijven. Zorg ervoor dat de VM-gegevensopslag is gekoppeld op de host van het hoofddoel, met lees-/schrijftoegang.
- Als de ESXi-host die niet wordt beheerd door een vCenter-Server, maakt Site Recovery service een nieuwe VM tijdens het opnieuw beveiligen. De virtuele machine wordt gemaakt op de ESX-host waarop u de Master doel-VM maakt. De harde schijf van de VM moet zich in een gegevensopslag bevinden die toegankelijk is voor de host waarop de hoofddoelserver wordt uitgevoerd.
- Voor fysieke machines waarvoor u een failback uitvoert, moet u de detectie volt ooien van de host waarop de hoofddoel server wordt uitgevoerd, voordat u de computer opnieuw kunt beveiligen.
- Een andere optie, als de on-premises VM al bestaat voor failback, is deze te verwijderen voordat u een failback gaat uitvoeren. Failback maakt vervolgens een nieuwe VM op dezelfde host als de ESX-host van de hoofddoelserver. Wanneer u een failback uitvoert naar een alternatieve locatie, worden de gegevens hersteld naar dezelfde gegevensopslag en dezelfde ESX-host als die welke gebruikt wordt door de on-premises hoofddoelserver.
- U kunt Storage vMotion niet gebruiken op de hoofddoelserver. Als u dat doet, werkt failback niet, omdat de schijven er niet beschikbaar voor zijn. Sluit de hoofddoelservers uit van uw vMotion-lijst.

## <a name="reprotect-azure-vms"></a>Azure VM’s opnieuw beveiligen

Bij deze procedure wordt ervan uitgegaan dat de on-premises VM niet beschikbaar is en dat u opnieuw beschermt naar een alternatieve locatie.

1. Klik in **Instellingen** > **Gerepliceerde items** met de rechtermuisknop op de VM waarvoor een failover-overschakeling is uitgevoerd > **Opnieuw beveiligen**.
2. Controleer in **Opnieuw beveiligen** of **Azure naar on-premises** is geselecteerd.
3. Geef de on-premises hoofddoelserver en de processerver op.

4. Selecteer in **Gegevensopslag** de gegevensopslag van het hoofddoel waarnaar u de on-premises schijven wilt herstellen. Gebruik deze optie wanneer de on-premises VM is verwijderd en u nieuwe schijven moet maken. Deze instellingen worden genegeerd als de schijven al bestaan, maar u moet wel een waarde opgeven.
5. Selecteer het bewaarstation van het hoofddoel. Het failback-beleid wordt automatisch geselecteerd.
6. Klik op **OK** om te beginnen met opnieuw beveiligen. Er wordt een taak gestart voor het repliceren van de VM van Azure naar de on-premises site. U kunt de voortgang volgen op het tabblad **Taken**.

> [!NOTE]
> Als u de Azure-VM wilt herstellen naar een bestaande on-premises VM, koppelt u de gegevensopslag van de virtuele machine met lees-/schrijf-toegang aan de ESXi-host van de hoofddoelserver.


## <a name="run-a-failover-from-azure-to-on-premises"></a>Een failover van Azure naar on-premises uitvoeren

Voor opnieuw repliceren naar on-premises wordt een failbackbeleid gebruikt. Dit beleid wordt automatisch gemaakt wanneer u een replicatiebeleid voor replicatie naar Azure hebt gemaakt:

- Het beleid wordt automatisch gekoppeld aan de configuratieserver.
- Het beleid kan niet worden gewijzigd.
- De beleidswaarden zijn:
    - RPO-drempelwaarde = 15 minuten
    - Bewaarperiode van het herstelpunt = 24 uur
    - Frequentie van de app-consistente momentopname = 60 minuten

Voer de failover als volgt uit:

1. Klik op de pagina **Gerepliceerde items** met de rechtermuisknop op de computer > **Niet-geplande failover**.
2. Controleer in **Failover bevestigen** of de failoverrichting van Azure is.

3. Selecteer het herstelpunt dat u wilt gebruiken voor de failover. Een app-consistent herstelpunt treedt op voor het meest recente tijdstip, en dit veroorzaakt enig gegevensverlies. Wanneer failover wordt uitgevoerd, schakelt Site Recovery de Azure VM's uit en start de on-premises VM op. Er zal wat downtime zijn, dus kies een geschikte tijd.
4. Klik met de rechtermuisknop op de computer en klik op **Doorvoeren**. Dit activeert een taak waardoor de Azure VM's worden verwijderd.
5. Controleer of de Azure VM's zijn afgesloten zoals verwacht.


## <a name="reprotect-on-premises-machines-to-azure"></a>On-premises computers opnieuw beveiligen naar Azure

De gegevens moeten nu weer terug zijn op uw on-premises site, maar ze worden niet gerepliceerd naar Azure. U kunt als volgt weer beginnen met repliceren naar Azure:

1. Selecteer in de kluis > **Instellingen** >**Gerepliceerde items** de failback-VM's waarvoor een failback is uitgevoerd en klik op **Opnieuw beveiligen**.
2. Selecteer de processerver die wordt gebruikt om de gerepliceerde gegevens naar Azure te verzenden en klik op **OK**.

Nadat het opnieuw beveiligen is voltooid, repliceert de VM weer naar Azure en kunt u zo nodig een failover uitvoeren.
