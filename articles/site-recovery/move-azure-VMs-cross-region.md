---
title: Virtuele Azure IaaS-machines verplaatsen naar een andere Azure-regio met behulp van de Azure Site Recovery-service | Microsoft Docs
description: Azure Site Recovery gebruiken om Azure IaaS-VM’s te verplaatsen van de ene Azure-regio naar een andere.
services: site-recovery
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: ec78e4c260c2ca5e0469f9373f60d8bca29ada7f
ms.sourcegitcommit: aebe5a10fa828733bbfb95296d400f4bc579533c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/05/2019
ms.locfileid: "70375722"
---
# <a name="move-azure-vms-to-another-region"></a>Virtuele Azure-machines verplaatsen naar een andere regio

Mogelijk wilt u Azure Infrastructure as a Service (IaaS) virtuele machines verplaatsen van de ene regio naar een andere om de betrouw baarheid, Beschik baarheid, beheer of governance te verbeteren. Deze zelf studie laat zien hoe u Vm's naar een andere regio kunt verplaatsen met behulp van Azure Site Recovery. U leert het volgende:

> [!div class="checklist"]
> * Vereisten verifiëren
> * De bron-VM’s voorbereiden
> * De doelregio voorbereiden
> * Gegevens kopiëren naar de doelregio
> * De configuratie testen
> * De verplaatsing uitvoeren
> * De resources uit de bron regio verwijderen


> [!IMPORTANT]
> In dit *artikel wordt beschreven*hoe u virtuele Azure-machines naar een andere regio kunt verplaatsen. Als u de beschik baarheid van uw infra structuur wilt verbeteren door Vm's te verplaatsen naar beschikbaarheids zones, raadpleegt [u Azure-Vm's verplaatsen naar Beschikbaarheidszones](move-azure-vms-avset-azone.md).

## <a name="prerequisites"></a>Vereisten

- Zorg ervoor dat u virtuele Azure-machines hebt in de Azure-regio die u wilt *verplaatsen.*
- Controleer of uw keuze van de [bron regio-doel regio combi natie wordt ondersteund](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support)en kies zorgvuldig de doel regio.
- Zorg ervoor dat u inzicht hebt in de [architectuur en onderdelen voor dit scenario](azure-to-azure-architecture.md).
- Controleer de [beperkingen en vereisten voor ondersteuning](azure-to-azure-support-matrix.md).
- Controleer de account machtigingen. Als u zojuist een gratis Azure-account hebt gemaakt, bent *u* de beheerder van uw abonnement. Als u niet de beheerder bent, kunt u met de beheerder de benodigde machtigingen verkrijgen:
  -  Als u replicatie voor een virtuele machine wilt inschakelen en gegevens naar het doel wilt kopiëren met behulp van Site Recovery, moet u gemachtigd zijn om een virtuele machine te maken in uw Azure-resources. De ingebouwde rol Inzender voor virtuele machines beschikt over deze machtigingen. Met de machtigingen kunt u het volgende doen:
        - Het maken van een VM in de geselecteerde resourcegroep.
        - Het maken van een VM in het geselecteerde virtuele netwerk.
        - Schrijven naar het geselecteerde opslagaccount.

  - U hebt ook machtigingen nodig voor het beheren van Site Recovery bewerkingen. De rol Site Recovery Inzender heeft alle machtigingen die nodig zijn voor het beheren van Site Recovery bewerkingen in een Azure Recovery Services-kluis.

## <a name="prepare-the-source-vms"></a>De bron-VM’s voorbereiden

1. Controleer of de Azure-Vm's die u wilt verplaatsen over de meest recente basis certificaten beschikken. Als dat niet het geval is, kunt u het kopiëren van gegevens naar de doel regio niet inschakelen vanwege beveiligings beperkingen.

    - Voor Windows-Vm's installeert u de meest recente Windows-updates zodat alle vertrouwde basis certificaten zich op de computer bevinden. In een niet-verbonden omgeving volgt u de standaard Windows Update en processen voor het bijwerken van certificaten voor uw organisatie.
    - Volg voor Linux-Vm's de richt lijnen van uw Linux-distributeur om de meest recente vertrouwde basis certificaten en certificaatintrekkingslijst te verkrijgen.
2. Zorg ervoor dat u geen verificatie proxy gebruikt voor het beheren van de netwerk verbinding voor Vm's die u wilt verplaatsen.
3. Als een virtuele machine die u wilt verplaatsen, geen toegang heeft tot internet en een firewall proxy gebruikt om uitgaande toegang te beheren, controleert u de [vereisten](azure-to-azure-tutorial-enable-replication.md#set-up-outbound-network-connectivity-for-vms).
4. Documenteer de bron netwerk indeling en alle resources die u momenteel gebruikt, inclusief (maar niet beperkt tot) load balancers, netwerk beveiligings groepen en open bare IP-adressen voor verificatie.

## <a name="prepare-the-target-region"></a>De doelregio voorbereiden

1. Controleer in uw Azure-abonnement of u virtuele machines kunt maken in de doel regio die wordt gebruikt voor herstel na nood gevallen. Neem contact op met de ondersteuning om het vereiste quotum zo nodig in te scha kelen.

2. Zorg ervoor dat uw abonnement voldoende bronnen heeft voor de ondersteuning van uw bron-Vm's. Als u Site Recovery gebruikt voor het kopiëren van gegevens naar het doel, wordt de grootte of de dichtstbijzijnde beschik bare grootte voor de doel-Vm's gekozen.

3. Zorg ervoor dat u een doel bron maakt voor elk onderdeel dat u in de bron netwerk indeling hebt geïdentificeerd. Zo zorgt u ervoor dat uw Vm's alle functionaliteit en functies hebben in de doel regio die ze in de bron regio hadden.

   Azure Site Recovery detecteert en maakt automatisch een virtueel netwerk en een opslag account wanneer u replicatie inschakelt voor de bron-VM. U kunt deze resources ook vooraf maken en deze toewijzen aan de VM als onderdeel van de stap voor het inschakelen van de replicatie. Maar u moet hand matig andere resources in de doel regio maken. Raadpleeg de volgende documenten voor het maken van de meest gebruikte netwerk bronnen op basis van de configuratie van uw bron-VM:

   - [Netwerkbeveiligingsgroepen](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)
   - [Load balancers](https://docs.microsoft.com/azure/load-balancer)
   - [Openbare IP](../virtual-network/virtual-network-public-ip-address.md)
    
   Raadpleeg de [documentatie voor Azure-netwerken](https://docs.microsoft.com/azure/#pivot=products&panel=network)voor andere netwerk onderdelen. 

4. Als u de configuratie wilt testen voordat u de verplaatsing uitvoert, maakt u hand matig [een niet-productie netwerk](https://docs.microsoft.com/azure/virtual-network/quick-create-portal) in de doel regio. Door de installatie te testen, maakt u minimale interferentie met de productie omgeving en wordt het aanbevolen.
    
## <a name="copy-data-to-the-target-region"></a>Gegevens kopiëren naar de doelregio
In de volgende stappen worden Azure Site Recovery gebruikt voor het kopiëren van gegevens naar de doel regio.

### <a name="create-the-vault-in-any-region-except-the-source"></a>De kluis in een wille keurige regio maken, behalve de bron

1. Meld u aan bij [Azure Portal](https://portal.azure.com) > **Recovery Services**.
2. Selecteer **een resource** > **beheer hulpprogramma's** > maken**back-up en site Recovery**.
3. Geef bij **naam**de beschrijvende naam **ContosoVMVault**op. Als u meer dan één abonnement hebt, selecteert u het gewenste abonnement.
4. Maak een resourcegroep met de naam **ContosoRG**.
5. Geef een Azure-regio op. Zie [Azure site Recovery prijs informatie](https://azure.microsoft.com/pricing/details/site-recovery/)als u de ondersteunde regio's wilt controleren.
6. Voor Recovery Services kluizen selecteert u **overzicht** > **ConsotoVMVault** >  **+ repliceren**.
7. Selecteer voor **bron**de optie **Azure**.
8. Voor **bron locatie**selecteert u de Azure-bron regio waar uw vm's momenteel worden uitgevoerd.
9. Selecteer het Azure Resource Manager-implementatie model. Selecteer vervolgens het **bron abonnement** en de **resource groep bron**.
10. Selecteer **OK** om de instellingen op te slaan.

### <a name="enable-replication-for-azure-vms-and-start-copying-the-data"></a>Replicatie inschakelen voor Azure-Vm's en beginnen met het kopiëren van de gegevens

Site Recovery haalt een lijst op met de virtuele machines die zijn gekoppeld aan het abonnement en de resource groep.

1. Selecteer de virtuele machine die u wilt verplaatsen en selecteer **OK**.
2. Voor **instellingen**selecteert u **herstel na nood gevallen**.
3. Selecteer bij**doel regio**voor **nood herstel** > configureren de doel regio waarnaar u wilt repliceren.
4. Kies voor het gebruik van de standaard doel resources of die u vooraf hebt gemaakt.
5. Selecteer **replicatie inschakelen** om de taak te starten.

   ![Replicatie inschakelen](media/tutorial-migrate-azure-to-azure/settings.png)

 

## <a name="test-the-configuration"></a>De configuratie testen


1. Ga naar de kluis. Selecteer in **instellingen** > **gerepliceerde items**de virtuele machine die u wilt verplaatsen naar de doel regio. Selecteer vervolgens **failover testen**.
2. Selecteer in **failover testen**het herstel punt dat u voor de failover wilt gebruiken:

   - **Laatst verwerkt**: voert een failover van de VM uit naar het laatste herstelpunt dat is verwerkt door de Site Recovery-service. Het tijdstempel wordt weergegeven. Er wordt geen tijd besteed aan het verwerken van gegevens. deze optie biedt daarom een lage herstel tijd (RTO).
   - **Laatste toepassingsconsistente punt**: Failover van alle Vm's naar het meest recente toepassings consistente herstel punt. Het tijdstempel wordt weergegeven.
   - **Aangepast**: selecteer een herstelpunt.

3. Selecteer het Azure-doelnetwerk waar u de Azure VM's naartoe wilt verplaatsen, om de configuratie te testen.

   > [!IMPORTANT]
   > We raden u aan om een afzonderlijk Azure VM-netwerk te gebruiken voor de testfailover, niet het productie netwerk in de doel regio.

4. Selecteer **OK**om te beginnen met het testen van de verplaatsing. Als u de voortgang wilt bijhouden, selecteert u de virtuele machine om de eigenschappen ervan te openen **.** Of selecteer de taak **testfailover** in de kluis. Selecteer vervolgens **instellingen** > **taken** > **site Recovery taken**.
5. Nadat de failover is voltooid, wordt de replica-Azure-VM weergegeven in Azure Portal > **Virtuele machines**. Controleer of de VM draait, de juiste grootte heeft en aangesloten is op het juiste netwerk.
6. Als u de virtuele machine die u hebt gemaakt voor testen wilt verwijderen, selecteert u testfailover **opschonen** voor het gerepliceerde item. Noteer alle opmerkingen met betrekking tot de test en sla ze op van **notities**.

## <a name="perform-the-move-and-confirm"></a>Het verplaatsen en bevestigen uitvoeren

1. Ga naar de kluis in **instellingen** > **gerepliceerde items**, selecteer de virtuele machine en selecteer vervolgens **failover**.
1. Voor **failover**selecteert u **meest recent**. 
2. Selecteer **Sluit de computer af voordat de failover wordt gestart**. Site Recovery probeert de bron-VM af te sluiten voordat de failover wordt geactiveerd. Failover wordt echter voortgezet, zelfs als het afsluiten mislukt. U kunt de voortgang van de failover volgen op de pagina **Taken**.
3. Wanneer de taak is voltooid, controleert u of de virtuele machine wordt weer gegeven in de Azure-doel regio zoals verwacht.
4. Klik in **gerepliceerde items**met de rechter muisknop op de virtuele machine en selecteer **door voeren**. De verplaatsing is voltooid. Wacht tot de doorvoer taak is voltooid.

## <a name="discard-the-resources-from-the-source-region"></a>De resources uit de bron regio verwijderen

- Ga naar de virtuele machine en selecteer **replicatie uitschakelen**. Hiermee wordt het proces van het kopiëren van de gegevens voor de VM beëindigd.

  > [!IMPORTANT]
  > Voer deze stap uit om te voor komen dat Site Recovery replicatie na de verplaatsing in rekening wordt gebracht.

Als u geen van de bron resources opnieuw wilt gebruiken, voert u de volgende stappen uit:

1. Verwijder alle relevante netwerk bronnen in de bron regio die u hebt vermeld in stap 4 van [de bron-Vm's voorbereiden](#prepare-the-source-vms).
2. Verwijder het bijbehorende opslagaccount in de bronregio.

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u geleerd hoe u virtuele Azure-machines naar een andere Azure-regio kunt verplaatsen. U kunt nu herstel na nood gevallen configureren voor deze Vm's.

> [!div class="nextstepaction"]
> [Herstel na noodgeval instellen na een migratie](azure-to-azure-quickstart.md)

