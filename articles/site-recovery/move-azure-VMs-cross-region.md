---
title: Azure VM's verplaatsen naar een andere regio met Azure Site Recovery
description: Azure Site Recovery gebruiken om Azure IaaS-VM’s te verplaatsen van de ene Azure-regio naar een andere.
services: site-recovery
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: dc37cb6fa05a2be56de7bf5536d7274190257d85
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78303916"
---
# <a name="move-azure-vms-to-another-azure-region"></a>Azure VM's verplaatsen naar een andere Azure-regio

U virtuele azure-infrastructuur als serviceservice (IaaS) verplaatsen van de ene regio naar de andere om de betrouwbaarheid, beschikbaarheid, beheer of governance te verbeteren. In deze zelfstudie ziet u hoe u VM's naar een andere regio verplaatst met Azure Site Recovery. U leert het volgende:

> [!div class="checklist"]
> * Vereisten verifiëren
> * De bron-VM’s voorbereiden
> * De doelregio voorbereiden
> * Gegevens kopiëren naar de doelregio
> * De configuratie testen
> * De verplaatsing uitvoeren
> * De bronnen uit het brongebied verwijderen


> [!IMPORTANT]
> In dit artikel wordt beschreven hoe u Azure VM's van de ene regio naar de andere verplaatst *als*. Zie [Azure VM's verplaatsen naar beschikbaarheidszones](move-azure-vms-avset-azone.md)als u de beschikbaarheid van uw infrastructuur wilt verbeteren door VM's naar beschikbaarheidszones te verplaatsen.

## <a name="prerequisites"></a>Vereisten

- Zorg ervoor dat u Azure VM's hebt in het azure-brongebied waarvan u wilt *overstappen.*
- Controleer of uw keuze van [de combinatie bronregio-doelregio wordt ondersteund](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support)en kies zorgvuldig het doelgebied.
- Zorg ervoor dat u inzicht hebt in de [architectuur en onderdelen voor dit scenario](azure-to-azure-architecture.md).
- Controleer de [beperkingen en vereisten voor ondersteuning](azure-to-azure-support-matrix.md).
- Accountmachtigingen verifiëren. Als u zojuist uw gratis Azure-account hebt gemaakt, bent *u* de beheerder van uw abonnement. Als u niet de beheerder bent, werkt u samen met de beheerder om de machtigingen te krijgen die u nodig hebt:
  -  Als u replicatie voor een virtuele machine wilt inschakelen en gegevens naar het doel wilt kopiëren met Siteherstel, moet u machtigingen hebben om een VM in uw Azure-bronnen te maken. De ingebouwde rol Inzender voor virtuele machines beschikt over deze machtigingen. Met de machtigingen u het:
        - Het maken van een VM in de geselecteerde resourcegroep.
        - Het maken van een VM in het geselecteerde virtuele netwerk.
        - Schrijven naar het geselecteerde opslagaccount.

  - U hebt ook machtigingen nodig om siteherstelbewerkingen te beheren. De rol Siteherstelbijdrager heeft alle machtigingen die nodig zijn om siteherstelbewerkingen te beheren in een azure recovery services-kluis.

## <a name="prepare-the-source-vms"></a>De bron-VM’s voorbereiden

1. Controleer of de Azure VM's die u wilt verplaatsen, over de nieuwste basiscertificaten beschikken. Als dit niet het zo is, u gegevenskopiëren naar het doelgebied niet inschakelen vanwege beveiligingsbeperkingen.

    - Installeer voor Windows VM's de nieuwste Windows-updates zodat alle vertrouwde rootcertificaten op de machine staan. Volg in een niet-verbonden omgeving de standaard windows-update- en certificaatupdateprocessen voor uw organisatie.
    - Volg voor Linux VM's de richtlijnen van uw Linux-distributeur om de nieuwste vertrouwde rootcertificaten en certificaatintrekkingslijst te krijgen.
2. Zorg ervoor dat u geen verificatieproxy gebruikt om de netwerkconnectiviteit te beheren voor VM's die u wilt verplaatsen.
3. Als een VM die u wilt verplaatsen geen toegang heeft tot internet en een firewallproxy gebruikt om uitgaande toegang te beheren, controleert u de [vereisten](azure-to-azure-tutorial-enable-replication.md#set-up-outbound-network-connectivity-for-vms).
4. Documenteer de indeling van de bronnetwerken en alle resources die u momenteel gebruikt, inclusief (maar niet beperkt tot) load balancers, netwerkbeveiligingsgroepen en openbare IP-adressen voor verificatie.

## <a name="prepare-the-target-region"></a>De doelregio voorbereiden

1. Controleer in uw Azure-abonnement of u VM's maken in het doelgebied dat wordt gebruikt voor noodherstel. Neem contact op met Ondersteuning om indien nodig het vereiste quotum in te schakelen.

2. Zorg ervoor dat uw abonnement voldoende middelen heeft om uw bron-VM's te ondersteunen. Als u Siteherstel gebruikt om gegevens naar het doel te kopiëren, wordt dezelfde grootte of dichtstbijzijnde beschikbare grootte voor de doel-VM's gebruikt.

3. Zorg ervoor dat u een doelbron maakt voor elke component die u hebt geïdentificeerd in de indeling van het bronnetwerk. Dit zorgt ervoor dat uw VM's alle functionaliteit en functies in het doelgebied hebben dat ze in het brongebied hadden.

   Azure Site Recovery detecteert en maakt automatisch een virtueel netwerk- en opslagaccount wanneer u replicatie inschakelt voor de bron-vm. U deze resources ook vooraf maken en deze toewijzen aan de VM als onderdeel van de stap enable-replicatie. Maar u moet handmatig andere bronnen in het doelgebied maken. Raadpleeg de volgende documenten om de meest gebruikte netwerkbronnen te maken op basis van de configuratie van uw bron-VM:

   - [Netwerkbeveiligingsgroepen](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)
   - [Load balancers](https://docs.microsoft.com/azure/load-balancer)
   - [Openbaar IP](../virtual-network/virtual-network-public-ip-address.md)
    
   Zie de [Azure-netwerkdocumentatie](https://docs.microsoft.com/azure/?pivot=products&panel=network)voor andere netwerkonderdelen. 

4. Als u de configuratie wilt testen voordat u de verplaatsing uitvoert, maakt u handmatig [een niet-productienetwerk](https://docs.microsoft.com/azure/virtual-network/quick-create-portal) in het doelgebied. Het testen van de installatie zorgt voor minimale interferentie met de productieomgeving en we raden het aan.
    
## <a name="copy-data-to-the-target-region"></a>Gegevens kopiëren naar de doelregio
De volgende stappen gebruiken Azure Site Recovery om gegevens naar het doelgebied te kopiëren.

### <a name="create-the-vault-in-any-region-except-the-source"></a>De kluis in een regio maken, behalve de bron

1. Meld u aan bij de [Azure portal](https://portal.azure.com) > **Recovery Services**.
2. Selecteer > **Back-up- en siteherstel****van hulpprogramma's** > voor **bronnenbeheer maken**.
3. Geef voor **Naam**de vriendelijke naam **ContosoVMVault**op. Als u meer dan één abonnement hebt, selecteert u het gewenste abonnement.
4. Maak een resourcegroep met de naam **ContosoRG**.
5. Geef een Azure-regio op. Zie [Prijsdetails azure site recovery .](https://azure.microsoft.com/pricing/details/site-recovery/)
6. Selecteer **Overzicht** > **ConsotoVMVault** > **+Repliceren voor**kluizen van Recovery Services.
7. Selecteer **Azure**voor **Bron**.
8. Selecteer **voor Bronlocatie**het bronAzure-gebied waar uw VM's momenteel worden uitgevoerd.
9. Selecteer het implementatiemodel azure resource manager. Selecteer vervolgens het **bronabonnement** en **bronbrongroep**.
10. Selecteer **OK** om de instellingen op te slaan.

### <a name="enable-replication-for-azure-vms-and-start-copying-the-data"></a>Replicatie voor Azure VM's inschakelen en beginnen met het kopiëren van de gegevens

Siteherstel haalt een lijst op met de VM's die zijn gekoppeld aan de abonnements- en resourcegroep.

1. Selecteer de VM die u wilt verplaatsen en selecteer **OK**.
2. Selecteer Herstel **na noodgevallen**voor **Instellingen**.
3. Selecteer **voor Doelregio Voor noodherstel** > **Target region**doel gebied configureren het doelgebied dat u herenigt.
4. Kies ervoor om de standaarddoelbronnen of de resources te gebruiken die u vooraf hebt gemaakt.
5. Selecteer **Replicatie inschakelen** om de taak te starten.

   ![Replicatie inschakelen](media/tutorial-migrate-azure-to-azure/settings.png)

 

## <a name="test-the-configuration"></a>De configuratie testen


1. Ga naar de kluis. Selecteer **in gerepliceerde items instellingen** > **Replicated items**de virtuele machine die u naar het doelgebied wilt verplaatsen. Selecteer vervolgens **Failover testen**.
2. Selecteer **in Test Failover**een herstelpunt dat u wilt gebruiken voor de failover:

   - **Laatst verwerkte**: voert een failover van de VM uit naar het laatste herstelpunt dat is verwerkt door de Site Recovery-service. Het tijdstempel wordt weergegeven. Er wordt geen tijd besteed aan het verwerken van gegevens, dus deze optie biedt een doelstelling voor een lage hersteltijd (RTO).
   - **Nieuwste app-consistent:** Mislukt over alle VM's naar het nieuwste app-consistente herstelpunt. Het tijdstempel wordt weergegeven.
   - **Aangepast**: selecteer een herstelpunt.

3. Selecteer het Azure-doelnetwerk waar u de Azure VM's naartoe wilt verplaatsen, om de configuratie te testen.

   > [!IMPORTANT]
   > We raden u aan een apart Azure VM-netwerk te gebruiken voor de testfailover, niet voor het productienetwerk in het doelgebied.

4. Als u de verplaatsing wilt testen, selecteert u **OK**. Als u de voortgang wilt bijhouden, selecteert u de VM om de **eigenschappen te openen.** Of selecteer de taak **Failover testen** in de kluis. Selecteer vervolgens **Vacatures** > **sitehersteltaken****Jobs** > instellen .
5. Nadat de failover is voltooid, wordt de replica-Azure-VM weergegeven in Azure Portal > **Virtuele machines**. Controleer of de VM draait, de juiste grootte heeft en aangesloten is op het juiste netwerk.
6. Als u de VM wilt verwijderen die u hebt gemaakt voor het testen, selecteert u Failover ophet gerepliceerde item **opschonen.** Uit **Notities,** registreren en opslaan van eventuele opmerkingen met betrekking tot de test.

## <a name="perform-the-move-and-confirm"></a>De verplaatsing uitvoeren en bevestigen

1. Ga naar de kluis in**Gerepliceerde items** **instellingen,** > selecteer de virtuele machine en selecteer **Failover**.
1. Selecteer **Laatste**optie voor **Failover**. 
2. Selecteer **Sluit de computer af voordat de failover wordt gestart**. Siteherstel probeert de bron-VM af te sluiten voordat de failover wordt geactiveerd. Maar failover blijft, zelfs als shutdown mislukt. U de failovervoortgang volgen op de pagina **Vacatures.**
3. Controleer bij het einde van de taak of de VM wordt weergegeven in het doelazure-gebied zoals verwacht.
4. Klik in **gerepliceerde items**met de rechtermuisknop op de virtuele machine en selecteer **Vastleggen**. Dit maakt de verhuizing af. Wacht tot de commit taak is afgelopen.

## <a name="discard-the-resources-from-the-source-region"></a>De bronnen uit het brongebied verwijderen

- Ga naar de vm en selecteer **Replicatie uitschakelen**. Hiermee wordt het proces van het kopiëren van de gegevens voor de VM beëindigd.

  > [!IMPORTANT]
  > Voer deze stap uit om te voorkomen dat er na de verhuizing kosten in rekening worden gebracht voor replicatie van siteherstel.

Als u niet van plan bent een van de bronbronnen opnieuw te gebruiken, voert u de volgende stappen uit:

1. Verwijder alle relevante netwerkbronnen in het brongebied die u hebt vermeld in stap 4 van [De bron-VM's voorbereiden.](#prepare-the-source-vms)
2. Verwijder het bijbehorende opslagaccount in de bronregio.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u Azure VM's naar een andere Azure-regio verplaatsen. Nu u noodherstel configureren voor die VM's.

> [!div class="nextstepaction"]
> [Herstel na noodgeval instellen na een migratie](azure-to-azure-quickstart.md)

