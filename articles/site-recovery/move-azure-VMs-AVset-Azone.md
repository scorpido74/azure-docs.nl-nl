---
title: VM's verplaatsen naar een Azure-regio met beschikbaarheidszones met Azure Site Recovery
services: site-recovery
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: 5728ce7125695b191de4f91d5bd9003384f428a7
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78298303"
---
# <a name="move-azure-vms-into-availability-zones"></a>Virtuele Azure-machines verplaatsen naar beschikbaarheidszones
Beschikbaarheidszones in Azure helpen uw toepassingen en gegevens te beschermen tegen datacenterfouten. Elke beschikbaarheidszone bestaat uit een of meer datacenters die zijn uitgerust met onafhankelijke voeding, koeling en netwerken. Om voor tolerantie te zorgen, is er een minimum van drie afzonderlijke zones in alle ingeschakelde regio's. De fysieke scheiding van beschikbaarheidszones binnen een regio helpt toepassingen en gegevens te beschermen tegen datacenterfouten. Met beschikbaarheidszones biedt Azure een service-level agreement (SLA) van 99,99% voor uptime van virtuele machines (VM's). Beschikbaarheidszones worden ondersteund in bepaalde regio's, zoals vermeld in [Wat zijn beschikbaarheidszones in Azure?](https://docs.microsoft.com/azure/availability-zones/az-overview#services-support-by-region).

In een scenario waarin uw VM's als *één instantie* in een bepaalde regio worden geïmplementeerd en u uw beschikbaarheid wilt verbeteren door deze VM's naar een beschikbaarheidszone te verplaatsen, u dit doen met Azure Site Recovery. Deze actie kan verder worden gecategoriseerd in:

- VM's met één instantie verplaatsen naar beschikbaarheidszones in een doelgebied
- VM's verplaatsen in een beschikbaarheidszone in beschikbaarheidszones in een doelregio

> [!IMPORTANT]
> Azure Site Recovery ondersteunt momenteel het verplaatsen van VM's van de ene regio naar de andere, maar biedt geen ondersteuning voor verplaatsing binnen een regio.

## <a name="check-prerequisites"></a>Vereisten controleren

- Controleer of de doelregio ondersteuning heeft [voor beschikbaarheidszones.](https://docs.microsoft.com/azure/availability-zones/az-overview#services-support-by-region) Controleer of uw keuze van [de combinatie bronregio/doelregio wordt ondersteund.](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support) Neem een weloverwogen beslissing over de doelregio.
- Zorg ervoor dat u inzicht hebt in de [architectuur en onderdelen voor dit scenario](azure-to-azure-architecture.md).
- Controleer de [beperkingen en vereisten voor ondersteuning](azure-to-azure-support-matrix.md).
- Controleer accountmachtigingen. Als u zojuist uw gratis Azure-account hebt gemaakt, bent u de beheerder van uw abonnement. Als u niet de abonnementsbeheerder bent, werkt u samen met de beheerder om de machtigingen toe te wijzen die u nodig hebt. Als u replicatie voor een virtuele machine wilt inschakelen en uiteindelijk gegevens naar het doel wilt kopiëren met Azure Site Recovery, moet u het gewenste doel hebben:

    1. Toestemming om een VM te maken in Azure-bronnen. De ingebouwde rol *Virtuele machinebijdrager* heeft deze machtigingen, waaronder:
        - Machtiging voor het maken van een virtuele machine in de geselecteerde resourcegroep
        - Machtiging voor het maken van een virtuele machine in het geselecteerde virtuele netwerk
        - Machtigingen om naar het geselecteerde opslagaccount te schrijven

    2. Toestemming voor het beheren van Azure Site Recovery-taken. De rol *Site herstelbijdrager* heeft alle machtigingen die nodig zijn om siteherstelacties in een kluis van Herstelservices te beheren.

## <a name="prepare-the-source-vms"></a>De bron-VM’s voorbereiden

1. Uw VM's moeten beheerde schijven gebruiken als u deze wilt verplaatsen naar een beschikbaarheidszone met behulp van Siteherstel. U bestaande Windows VM's die onbeheerde schijven gebruiken converteren naar beheerde schijven. Volg de stappen bij [Een virtuele windows-machine converteren van onbeheerde schijven naar beheerde schijven.](https://docs.microsoft.com/azure/virtual-machines/windows/convert-unmanaged-to-managed-disks) Controleer of de beschikbaarheidsset is geconfigureerd als *beheerd*.
2. Controleer of de meest recente basiscertificaten aanwezig zijn op de Azure-VM’s die u wilt verplaatsen. Als de laatste basiscertificaten niet aanwezig zijn, kan de gegevenskopie naar het doelgebied niet worden ingeschakeld vanwege beveiligingsbeperkingen.

3. Voor Windows-VM’s moet u de meest recente Windows-updates op de VM installeren, zodat alle vertrouwde basiscertificaten op de machine aanwezig zijn. Volg in een niet-verbonden omgeving de standaard windows-update- en certificaatupdateprocessen voor uw organisatie.

4. Volg voor Linux VM's de richtlijnen van uw Linux-distributeur om de nieuwste vertrouwde rootcertificaten en certificaatintrekkingslijst op de VM te krijgen.
5. Zorg ervoor dat u geen verificatieproxy gebruikt om de netwerkconnectiviteit te beheren voor VM's die u wilt verplaatsen.

6. Als de vm die u probeert te verplaatsen geen toegang heeft tot internet en een firewallproxy gebruikt om uitgaande toegang te beheren, controleert u de vereisten bij [Uitgaande netwerkconnectiviteit configureren.](azure-to-azure-tutorial-enable-replication.md#set-up-outbound-network-connectivity-for-vms)

7. Identificeer de indeling van bronnetwerken en de bronnen die u momenteel gebruikt voor verificatie, waaronder load balancers, NSGs en openbare IP.Identify the source networking layout and the resources you currently use for verification, including load balancers, NSGs, and public IP.

## <a name="prepare-the-target-region"></a>De doelregio voorbereiden

1. Controleer of u met uw Azure-abonnement VM's maken in het doelgebied dat wordt gebruikt voor noodherstel. Neem indien nodig contact op met de ondersteuning om het vereiste quotum mogelijk te maken.

2. Zorg ervoor dat uw abonnement voldoende resources heeft die ondersteuning kunnen bieden voor VM’s met grootten die overeenkomen met uw bron-VM’s. Als u Siteherstel gebruikt om gegevens naar het doel te kopiëren, kiest deze dezelfde grootte of de dichtstbijzijnde grootte voor de doel-vm.

3. Maak een doelbron voor elk onderdeel dat in de indeling van de bronnetwerkwordt geïdentificeerd. Deze actie zorgt ervoor dat nadat u naar het doelgebied bent gegaan, uw VM's alle functionaliteit en functies hebben die u in de bron had.

    > [!NOTE]
    > Azure Site Recovery detecteert en maakt automatisch een virtueel netwerk- en opslagaccount wanneer u replicatie inschakelt voor de bron-vm. U deze resources ook vooraf maken en aan de VM toewijzen als onderdeel van de replicatiestap inschakelen. Maar voor alle andere bronnen, zoals later vermeld, moet u ze handmatig maken in de doelregio.

     In de volgende documenten wordt uitgelegd hoe u de meest gebruikte netwerkbronnen maken die voor u relevant zijn, op basis van de bron-VM-configuratie.

    - [Netwerkbeveiligingsgroepen](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)
    - [Load balancers](https://docs.microsoft.com/azure/load-balancer)
    - [Openbaar IP](../virtual-network/virtual-network-public-ip-address.md)
    
   Voor alle andere netwerkonderdelen verwijzen we u naar de [netwerkdocumentatie.](https://docs.microsoft.com/azure/?pivot=products&panel=network)

    > [!IMPORTANT]
    > Zorg ervoor dat u een zoneredundante load balancer in het doel gebruikt. U meer lezen bij [Standard Load Balancer en Availability Zones.](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones)

4. Maak handmatig [een niet-productienetwerk](https://docs.microsoft.com/azure/virtual-network/quick-create-portal) in het doelgebied als u de configuratie wilt testen voordat u naar het doelgebied gaat. We raden deze aanpak aan omdat het minimale interferentie met de productieomgeving veroorzaakt.

## <a name="enable-replication"></a>Replicatie inschakelen
De volgende stappen leiden u bij het gebruik van Azure Site Recovery om replicatie van gegevens naar het doelgebied mogelijk te maken, voordat u ze uiteindelijk naar beschikbaarheidszones verplaatst.

> [!NOTE]
> Deze stappen zijn voor één virtuele machine. U hetzelfde uitbreiden naar meerdere VM's. Ga naar de kluis Recovery Services, selecteer **+ Repliceren**en selecteer samen de relevante VM's.

1. Selecteer **virtuele machines**in de Azure-portal en selecteer de VM die u naar beschikbaarheidszones wilt verplaatsen.
2. Selecteer in **Bewerkingen** de optie **Herstel na noodgeval**.
3. Selecteer in **Doelgebied voor noodherstel** > **configureren**het doelgebied waaraan u zult repliceren. Zorg ervoor dat deze regio beschikbaarheidszones [ondersteunt.](https://docs.microsoft.com/azure/availability-zones/az-overview#services-support-by-region)

    ![Selectie van doelregio's](media/azure-vms-to-zones/enable-rep-1.PNG)

4. Selecteer **Volgende: Geavanceerde instellingen**.
5. Kies de juiste waarden voor het doelabonnement, de vm-brongroep en het virtuele netwerk.
6. Kies **in** de sectie Beschikbaarheid de beschikbaarheidszone waarin u de vm wilt verplaatsen. 
   > [!NOTE]
   > Als u de optie voor beschikbaarheidsset of Beschikbaarheidszone niet ziet, moet u ervoor zorgen dat aan de [vereisten](#prepare-the-source-vms) wordt voldaan en dat de [voorbereiding](#prepare-the-source-vms) van de bron-VM's is voltooid.
  
    ![Selecties voor het kiezen van een beschikbaarheidszone](media/azure-vms-to-zones/enable-rep-2.PNG)

7. Selecteer **Replicatie inschakelen**. Met deze actie wordt een taak gestart om replicatie voor de VM in te schakelen.

## <a name="check-settings"></a>Instellingen controleren

Nadat de replicatietaak is voltooid, kunt u de replicatiestatus controleren, de replicatie-instellingen wijzigen en de implementatie testen.

1. Selecteer in het VM-menu de optie **Herstel na noodgeval**.
2. U de replicatiestatus, de herstelpunten die zijn gemaakt en de bron en doelgebieden op de kaart controleren.

   ![Replicatiestatus](media/azure-to-azure-quickstart/replication-status.png)

## <a name="test-the-configuration"></a>De configuratie testen

1. Selecteer **disaster recovery**in het menu voor virtuele machines.
2. Selecteer het pictogram **Failover testen.**
3. Selecteer **in Test Failover**een herstelpunt dat u wilt gebruiken voor de failover:

   - **Laatst verwerkte**: voert een failover van de VM uit naar het laatste herstelpunt dat is verwerkt door de Site Recovery-service. Het tijdstempel wordt weergegeven. Met deze optie wordt er geen tijd besteed aan het verwerken van gegevens, zodat er sprake is van een lage RTO (Recovery Time Objective).
   - **Laatste toepassingsconsistente punt**: met deze optie wordt er een failover uitgevoerd van alle VM’s naar het laatste toepassingsconsistente herstelpunt. Het tijdstempel wordt weergegeven.
   - **Aangepast**: selecteer een herstelpunt.

3. Selecteer het Azure-doeltestnetwerk waar u de Azure VM's naartoe wilt verplaatsen om de configuratie te testen. 

    > [!IMPORTANT]
    > We raden u aan een apart Azure VM-netwerk te gebruiken voor de testfout en niet voor het productienetwerk in het doelgebied waaru uw VM's wilt verplaatsen.

4. Als u de verplaatsing wilt testen, selecteert u **OK**. Als u de voortgang wilt bijhouden, selecteert u de VM om de eigenschappen ervan te openen. **Jobs** > U ook de taak **Failover** testen selecteren in de kluisnaam > Vacatures**site hersteltaken** **instellingen** > .
5. Nadat de failover is voltooid, wordt de replica-Azure-VM weergegeven in Azure Portal > **Virtuele machines**. Controleer of de VM draait, de juiste grootte heeft en aangesloten is op het juiste netwerk.
6. Als u de VM wilt verwijderen die is gemaakt als onderdeel van het testen van de verplaatsing, selecteert u **Failover opschonen** van de test op het gerepliceerde item. Leg in **Notities** eventuele opmerkingen over de test vast en sla deze op.

## <a name="move-to-the-target-region-and-confirm"></a>Naar het doelgebied gaan en bevestigen

1.  Selecteer **disaster recovery**in het menu voor virtuele machines.
2. Selecteer het **pictogram Failover.**
3. Bij **Failover** selecteert u **Meest recente**. 
4. Selecteer **Sluit de computer af voordat de failover wordt gestart**. Site Recovery sluit de virtuele bronmachine af voordat de failover wordt geactiveerd. De failover wordt voortgezet zelfs als het afsluiten is mislukt. U de failovervoortgang volgen op de pagina **Vacatures.** 
5. Controleer nadat de taak is voltooid, of de VM zoals verwacht wordt weergegeven in het doelazure-gebied.
6. Klik in **Gerepliceerde items** met de rechtermuisknop op de virtuele machine > **Doorvoeren**. De verplaatsing naar de doelregio wordt voltooid. Wacht tot de commit job is voltooid.

## <a name="discard-the-resource-in-the-source-region"></a>Verwijder de resource in de bronregio

Ga naar de VM. Selecteer **Replicatie uitschakelen**. Met deze actie wordt het proces voor het kopiëren van de gegevens voor de vm gestopt.  

> [!IMPORTANT]
> Doe de vorige stap om te voorkomen dat er na de verhuizing kosten in rekening worden gebracht voor replicatie van siteherstel. De bronreplicatie-instellingen worden automatisch opgeschoond. Houd er rekening mee dat de extensie Siteherstel die is geïnstalleerd als onderdeel van de replicatie niet wordt verwijderd en handmatig moet worden verwijderd.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u de beschikbaarheid van een Azure VM vergroot door over te stappen op een beschikbaarheidsset of beschikbaarheidszone. Nu u noodherstel instellen voor de verplaatste VM.

> [!div class="nextstepaction"]
> [Herstel na noodgeval instellen na een migratie](azure-to-azure-quickstart.md)


