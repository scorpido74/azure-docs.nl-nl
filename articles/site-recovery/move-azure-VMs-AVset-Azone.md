---
title: Vm's verplaatsen naar een Azure-regio met beschikbaarheids zones met behulp van Azure Site Recovery
services: site-recovery
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: 1d771d1e13d1ffd92a18658d08bb948d97e55999
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82209006"
---
# <a name="move-azure-vms-into-availability-zones"></a>Virtuele Azure-machines verplaatsen naar beschikbaarheidszones
Beschikbaarheidszones in azure helpen bij het beveiligen van uw toepassingen en gegevens van data centers. Elke beschikbaarheidszone bestaat uit een of meer datacenters die zijn uitgerust met onafhankelijke voeding, koeling en netwerken. Om voor tolerantie te zorgen, is er een minimum van drie afzonderlijke zones in alle ingeschakelde regio's. De fysieke schei ding van Beschikbaarheidszones binnen een regio helpt bij het beveiligen van toepassingen en gegevens van data centers. Met Beschikbaarheidszones biedt Azure een SLA (Service Level Agreement) van 99,99% voor de uptime van virtuele machines (Vm's). Beschikbaarheidszones worden ondersteund in regio's selecteren, zoals vermeld in [regio's die ondersteuning bieden voor Beschikbaarheidszones](https://docs.microsoft.com/azure/availability-zones/az-region).

In een scenario waarin uw Vm's worden geïmplementeerd als *één exemplaar* in een specifieke regio en u de beschik baarheid wilt verbeteren door deze vm's te verplaatsen naar een beschikbaarheids zone, kunt u dit doen met behulp van Azure site Recovery. Deze actie kan verder worden gecategoriseerd in:

- Vm's met één exemplaar verplaatsen naar Beschikbaarheidszones in een doel regio
- Vm's in een beschikbaarheidsset verplaatsen naar Beschikbaarheidszones in een doel regio

> [!IMPORTANT]
> Momenteel biedt Azure Site Recovery ondersteuning voor het verplaatsen van Vm's van de ene regio naar een andere, maar biedt geen ondersteuning voor verplaatsen binnen een regio.

## <a name="check-prerequisites"></a>Vereisten controleren

- Controleer of de doel regio [ondersteuning biedt voor Beschikbaarheidszones](https://docs.microsoft.com/azure/availability-zones/az-region). Controleer of de [combi natie van de bron regio/doel regio wordt ondersteund](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support). Maak een weloverwogen beslissing voor de doel regio.
- Zorg ervoor dat u inzicht hebt in de [architectuur en onderdelen voor dit scenario](azure-to-azure-architecture.md).
- Controleer de [beperkingen en vereisten voor ondersteuning](azure-to-azure-support-matrix.md).
- Controleer de account machtigingen. Als u zojuist een gratis Azure-account hebt gemaakt, bent u de beheerder van uw abonnement. Als u niet de abonnements beheerder bent, kunt u met de beheerder samen werken om de benodigde machtigingen toe te wijzen. Als u replicatie voor een virtuele machine wilt inschakelen en uiteindelijk gegevens naar het doel wilt kopiëren met behulp van Azure Site Recovery, hebt u het volgende nodig:

    1. Machtiging voor het maken van een virtuele machine in azure-resources. De ingebouwde rol Inzender voor de *virtuele machine* heeft deze machtigingen, waaronder:
        - Machtiging voor het maken van een virtuele machine in de geselecteerde resourcegroep
        - Machtiging voor het maken van een virtuele machine in het geselecteerde virtuele netwerk
        - Machtigingen om naar het geselecteerde opslagaccount te schrijven

    2. Machtiging voor het beheren van Azure Site Recovery taken. De rol *site Recovery Inzender* heeft alle machtigingen die nodig zijn voor het beheren van site Recovery acties in een Recovery Services kluis.

## <a name="prepare-the-source-vms"></a>De bron-VM’s voorbereiden

1. Uw Vm's moeten beheerde schijven gebruiken als u deze wilt verplaatsen naar een beschikbaarheids zone met behulp van Site Recovery. U kunt bestaande Windows-Vm's die gebruikmaken van niet-beheerde schijven, converteren voor het gebruik van beheerde schijven. Volg de stappen in [een virtuele Windows-machine converteren van niet-beheerde schijven naar beheerde schijven](https://docs.microsoft.com/azure/virtual-machines/windows/convert-unmanaged-to-managed-disks). Zorg ervoor dat de beschikbaarheidsset is geconfigureerd als *beheerd*.
2. Controleer of de meest recente basiscertificaten aanwezig zijn op de Azure-VM’s die u wilt verplaatsen. Als de meest recente basis certificaten niet aanwezig zijn, kan het kopiëren van gegevens naar de doel regio niet worden ingeschakeld vanwege beveiligings beperkingen.

3. Voor Windows-VM’s moet u de meest recente Windows-updates op de VM installeren, zodat alle vertrouwde basiscertificaten op de machine aanwezig zijn. In een niet-verbonden omgeving voert u de standaard Windows Update-en certificaat update processen voor uw organisatie uit.

4. Voor Linux-Vm's volgt u de richt lijnen van uw Linux-distributeur om de meest recente vertrouwde basis certificaten en certificaatintrekkingslijsten te verkrijgen op de virtuele machine.
5. Zorg ervoor dat u geen verificatie proxy gebruikt voor het beheren van de netwerk verbinding voor Vm's die u wilt verplaatsen.

6. Als de virtuele machine die u wilt verplaatsen, geen toegang heeft tot internet en een firewall proxy gebruikt om uitgaande toegang te beheren, controleert u de vereisten op [uitgaande netwerk verbinding configureren](azure-to-azure-tutorial-enable-replication.md#set-up-outbound-network-connectivity-for-vms).

7. Identificeer de bron netwerk indeling en de resources die u momenteel gebruikt voor verificatie, inclusief load balancers, Nsg's en open bare IP.

## <a name="prepare-the-target-region"></a>De doelregio voorbereiden

1. Controleer of u met uw Azure-abonnement virtuele machines kunt maken in de doel regio die wordt gebruikt voor herstel na nood gevallen. Neem zo nodig contact op met de ondersteuning om het vereiste quotum in te scha kelen.

2. Zorg ervoor dat uw abonnement voldoende resources heeft die ondersteuning kunnen bieden voor VM’s met grootten die overeenkomen met uw bron-VM’s. Als u Site Recovery gebruikt voor het kopiëren van gegevens naar het doel, wordt dezelfde grootte of de dichtstbijzijnde mogelijke grootte voor de doel-VM gekozen.

3. Maak een doel bron voor elk onderdeel dat is geïdentificeerd in de bron netwerk indeling. Met deze actie zorgt u ervoor dat nadat u hebt overgeschakeld naar de doel regio, uw Vm's beschikken over alle functionaliteit en functies die u in de bron had.

    > [!NOTE]
    > Azure Site Recovery detecteert en maakt automatisch een virtueel netwerk en een opslag account wanneer u replicatie inschakelt voor de bron-VM. U kunt deze resources ook vooraf maken en toewijzen aan de virtuele machine als onderdeel van de stap replicatie inschakelen. Voor alle andere resources, zoals later vermeld, moet u deze hand matig in de doel regio maken.

     In de volgende documenten wordt uitgelegd hoe u de meest gebruikte netwerk bronnen maakt die relevant zijn voor u, op basis van de configuratie van de bron-VM.

    - [Netwerkbeveiligingsgroepen](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)
    - [Load balancers](https://docs.microsoft.com/azure/load-balancer)
    - [Openbaar IP-adres](../virtual-network/virtual-network-public-ip-address.md)
    
   Raadpleeg de netwerk [documentatie](https://docs.microsoft.com/azure/?pivot=products&panel=network)voor andere netwerk onderdelen.

    > [!IMPORTANT]
    > Zorg ervoor dat u een zone-redundante load balancer gebruikt in het doel. Meer informatie vindt u op [Standard Load Balancer en Beschikbaarheidszones](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones).

4. Maak hand matig [een niet-productie netwerk](https://docs.microsoft.com/azure/virtual-network/quick-create-portal) in de doel regio als u de configuratie wilt testen voordat u naar de doel regio knipt. We raden u aan deze benadering uit te voeren, omdat deze minimale interferentie veroorzaakt met de productie omgeving.

## <a name="enable-replication"></a>Replicatie inschakelen
De volgende stappen helpen u bij het gebruik van Azure Site Recovery om de replicatie van gegevens naar de doel regio in te scha kelen voordat u deze uiteindelijk verplaatst naar Beschikbaarheidszones.

> [!NOTE]
> Deze stappen gelden voor één virtuele machine. U kunt dezelfde uitbreiden naar meerdere Vm's. Ga naar de Recovery Services kluis, selecteer **+ repliceren**en selecteer de relevante vm's samen.

1. Selecteer in het Azure Portal **virtuele machines**en selecteer de virtuele machine die u naar Beschikbaarheidszones wilt verplaatsen.
2. Selecteer in **Bewerkingen** de optie **Herstel na noodgeval**.
3. Selecteer in > **doel regio**voor **nood herstel configureren**de doel regio waarnaar u wilt repliceren. Zorg ervoor dat deze regio Beschikbaarheidszones [ondersteunt](https://docs.microsoft.com/azure/availability-zones/az-region) .

    ![Selectie van doel regio](media/azure-vms-to-zones/enable-rep-1.PNG)

4. Selecteer **volgende: geavanceerde instellingen**.
5. Kies de juiste waarden voor het doel abonnement, de doel-VM-resource groep en het virtuele netwerk.
6. Kies in het gedeelte **Beschik baarheid** de beschikbaarheids zone waarnaar u de virtuele machine wilt verplaatsen. 
   > [!NOTE]
   > Als u de optie voor het instellen van de beschikbaarheidsset of de zone beschikbaarheids niet ziet, moet u ervoor zorgen dat aan de [vereisten](#prepare-the-source-vms) wordt voldaan en dat de voor [bereiding](#prepare-the-source-vms) van de bron-vm's is voltooid.
  
    ![Selecties voor het kiezen van een beschikbaarheids zone](media/azure-vms-to-zones/enable-rep-2.PNG)

7. Selecteer **Replicatie inschakelen**. Met deze actie wordt een taak gestart om replicatie in te scha kelen voor de virtuele machine.

## <a name="check-settings"></a>Instellingen controleren

Nadat de replicatietaak is voltooid, kunt u de replicatiestatus controleren, de replicatie-instellingen wijzigen en de implementatie testen.

1. Selecteer in het VM-menu de optie **Herstel na noodgeval**.
2. U kunt de replicatie status controleren, de herstel punten die zijn gemaakt en de bron-en doel regio's op de kaart.

   ![Replicatiestatus](media/azure-to-azure-quickstart/replication-status.png)

## <a name="test-the-configuration"></a>De configuratie testen

1. Selecteer in het menu virtuele machine **herstel na nood gevallen**.
2. Selecteer het pictogram **testfailover** .
3. Selecteer in **failover testen**het herstel punt dat u voor de failover wilt gebruiken:

   - **Laatst verwerkte**: voert een failover van de VM uit naar het laatste herstelpunt dat is verwerkt door de Site Recovery-service. Het tijdstempel wordt weergegeven. Met deze optie wordt er geen tijd besteed aan het verwerken van gegevens, zodat er sprake is van een lage RTO (Recovery Time Objective).
   - **Laatste toepassingsconsistente punt**: met deze optie wordt er een failover uitgevoerd van alle VM’s naar het laatste toepassingsconsistente herstelpunt. Het tijdstempel wordt weergegeven.
   - **Aangepast**: selecteer een herstelpunt.

3. Selecteer het Azure-doeltestnetwerk waar u de Azure VM's naartoe wilt verplaatsen om de configuratie te testen. 

    > [!IMPORTANT]
    > We raden u aan om een afzonderlijk Azure VM-netwerk te gebruiken voor de test fout en niet het productie netwerk in de doel regio waarin u de Vm's wilt verplaatsen.

4. Selecteer **OK**om te beginnen met het testen van de verplaatsing. Als u de voortgang wilt bijhouden, selecteert u de virtuele machine om de eigenschappen ervan te openen. U kunt ook de taak **testfailover** selecteren in de kluis naam > **instellingen** > **taken** > **site Recovery taken**.
5. Nadat de failover is voltooid, wordt de replica-Azure-VM weergegeven in Azure Portal > **Virtuele machines**. Controleer of de VM draait, de juiste grootte heeft en aangesloten is op het juiste netwerk.
6. Als u de virtuele machine wilt verwijderen die is gemaakt als onderdeel van het testen van de verplaatsing, selecteert u **testfailover opschonen** voor het gerepliceerde item. Leg in **Notities** eventuele opmerkingen over de test vast en sla deze op.

## <a name="move-to-the-target-region-and-confirm"></a>Ga naar de doel regio en bevestig

1.  Selecteer in het menu virtuele machine **herstel na nood gevallen**.
2. Selecteer het pictogram **failover** .
3. Bij **Failover** selecteert u **Meest recente**. 
4. Selecteer **Sluit de computer af voordat de failover wordt gestart**. Site Recovery sluit de virtuele bronmachine af voordat de failover wordt geactiveerd. De failover wordt voortgezet zelfs als het afsluiten is mislukt. U kunt de voortgang van de failover op de pagina **taken** volgen. 
5. Nadat de taak is voltooid, controleert u of de virtuele machine wordt weer gegeven in de Azure-doel regio zoals verwacht.
6. Klik in **Gerepliceerde items** met de rechtermuisknop op de virtuele machine > **Doorvoeren**. De verplaatsing naar de doelregio wordt voltooid. Wacht tot de doorvoer taak is voltooid.

## <a name="discard-the-resource-in-the-source-region"></a>Verwijder de resource in de bronregio

Ga naar de virtuele machine. Selecteer **replicatie uitschakelen**. Met deze actie wordt het proces voor het kopiëren van de gegevens voor de virtuele machine gestopt.  

> [!IMPORTANT]
> Voer de voor gaande stap uit om te voor komen dat Site Recovery replicatie na de verplaatsing wordt gefactureerd. De bronreplicatie-instellingen worden automatisch opgeschoond. Houd er rekening mee dat de uitbrei ding van de Site Recovery die is geïnstalleerd als onderdeel van de replicatie, niet wordt verwijderd en hand matig moet worden verwijderd.

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u de beschik baarheid van een Azure-VM verbeterd door over te stappen op een beschikbaarheidsset of beschikbaarheids zone. U kunt nu herstel na nood gevallen instellen voor de verplaatste VM.

> [!div class="nextstepaction"]
> [Herstel na noodgeval instellen na een migratie](azure-to-azure-quickstart.md)


