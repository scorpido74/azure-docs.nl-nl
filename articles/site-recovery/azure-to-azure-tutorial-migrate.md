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
ms.openlocfilehash: 4882206692c334d6ab6af28feb5d2cba5277eea1
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78303933"
---
# <a name="move-azure-vms-to-another-region"></a>Virtuele Azure-machines verplaatsen naar een andere regio

Er zijn verschillende scenario's waarin u uw bestaande Azure IaaS-virtuele machines (VM's) van de ene regio naar de andere wilt verplaatsen. U wilt bijvoorbeeld de betrouwbaarheid en beschikbaarheid van uw bestaande VM's verbeteren, de beheerbaarheid verbeteren of om governanceredenen verplaatsen. Zie het overzicht [azure VM-verplaatsen](azure-to-azure-move-overview.md)voor meer informatie . 

U de [Azure Site Recovery-service](site-recovery-overview.md) gebruiken voor het beheren en orkestreren van noodherstel van on-premises machines en Azure VM's voor bedrijfscontinuïteit en disaster recovery (BCDR). U siteherstel ook gebruiken om de verplaatsing van Azure VM's naar een secundaire regio te beheren.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> 
> * Vereisten voor de verhuizing verifiëren
> * De bron-VM's en het doelgebied voorbereiden
> * De gegevens kopiëren en replicatie inschakelen
> * De configuratie testen en de verplaatsing uitvoeren
> * De bronnen in het brongebied verwijderen
> 
> [!NOTE]
> In deze zelfstudie ziet u hoe u Azure VM's van de ene regio naar de andere verplaatst. Zie de [zelfstudie Azure VM's verplaatsen naar beschikbaarheid](move-azure-vms-avset-azone.md)in een beschikbaarheidsset naar zone vastgemaakte VM's in een andere regio.

## <a name="prerequisites"></a>Vereisten

- Controleer of de Azure VM's zich in het Azure-gebied bevinden van waaruit u wilt verplaatsen.
- Controleer of uw [bronregio - doelregiocombinatie wordt ondersteund](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support)en neem een weloverwogen beslissing over de doelregio.
- Zorg ervoor dat u inzicht hebt in de [architectuur en onderdelen voor dit scenario](azure-to-azure-architecture.md).
- Controleer de [beperkingen en vereisten voor ondersteuning](azure-to-azure-support-matrix.md).
- Accountmachtigingen verifiëren. Als u uw gratis Azure-account hebt gemaakt, bent u de beheerder van uw abonnement. Als u niet de abonnementsbeheerder bent, werkt u samen met de beheerder om de machtigingen toe te wijzen die u nodig hebt. Als u replicatie voor een virtuele machine wilt inschakelen en in wezen gegevens wilt kopiëren met Azure Site Recovery, moet u het als nog even hebben:

    - Machtigingen voor het maken van een virtuele machine in Azure-resources. De ingebouwde rol Virtuele machinebijdrager heeft deze machtigingen, waaronder:
    - Machtiging voor het maken van een virtuele machine in de geselecteerde resourcegroep
    - Machtiging voor het maken van een virtuele machine in het geselecteerde virtuele netwerk
    - Machtigingen om naar het geselecteerde opslagaccount te schrijven
    
    - Machtigingen voor het beheren van Azure Site Recovery-bewerkingen. De rol Siteherstelbijdrager heeft alle machtigingen die nodig zijn om siteherstelbewerkingen in een kluis van Herstelservices te beheren.

- Zorg ervoor dat alle nieuwste basiscertificaten zich bevinden op de Azure VM's die u wilt verplaatsen. Als de laatste basiscertificaten niet op de VM staan, worden de gegevenskopie naar het doelgebied voorkomen.

- Voor Windows-VM’s moet u de meest recente Windows-updates op de VM installeren, zodat alle vertrouwde basiscertificaten op de machine aanwezig zijn. In een niet-verbonden omgeving moet u de standaardprocedures van Windows Update en de standaardprocedures voor het bijwerken van de certificaten van uw organisatie volgen.
    
- Volg voor Linux VM's de richtlijnen van uw Linux-distributeur om de nieuwste vertrouwde rootcertificaten en certificaatintrekkingslijst op de VM te krijgen.
- Zorg ervoor dat u geen verificatieproxy gebruikt om de netwerkconnectiviteit te beheren voor VM's die u wilt verplaatsen.

- Als de VM die u probeert te verplaatsen geen toegang heeft tot internet of als deze een firewallproxy gebruikt om uitgaande toegang te beheren, [controleert u de vereisten](azure-to-azure-tutorial-enable-replication.md#set-up-outbound-network-connectivity-for-vms).

- Identificeer de indeling van de bronnetwerkindeling en alle bronnen die u momenteel gebruikt. Dit omvat, maar is niet beperkt tot load balancers, network security groups (NSGs) en openbare IP's.

- Controleer of u met uw Azure-abonnement VM's maken in het doelgebied dat wordt gebruikt voor noodherstel. Neem contact op met ondersteuning voor het inschakelen van het vereiste quotum.

- Zorg ervoor dat uw abonnement voldoende resources heeft om VM's te ondersteunen met formaten die overeenkomen met uw bron-VM's. Als u Siteherstel gebruikt om gegevens naar het doel te kopiëren, kiest Siteherstel dezelfde grootte of de dichtstbijzijnde grootte voor de doel-vm.

- Zorg ervoor dat u een doelbron maakt voor elke component die is geïdentificeerd in de indeling van het bronnetwerk. Deze stap is belangrijk om ervoor te zorgen dat uw VM's alle functionaliteit en functies hebben in het doelgebied dat u in het brongebied had.

     > [!NOTE] 
     > Azure Site Recovery detecteert en maakt automatisch een virtueel netwerk wanneer u replicatie inschakelt voor de bron-vm. U ook een netwerk vooraf maken en toewijzen aan de VM in de gebruikersstroom om replicatie in te schakelen. Zoals later vermeld, moet u handmatig andere resources in het doelgebied maken.

    Zie de volgende documentatie voor het maken van de meest gebruikte netwerkbronnen die voor u relevant zijn op basis van de bron-VM-configuratie:
    - [Netwerkbeveiligingsgroepen](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)
    - [Load balancers](https://docs.microsoft.com/azure/load-balancer)
    -  [Openbaar IP](../virtual-network/virtual-network-public-ip-address.md)
    - Zie de [netwerkdocumentatie](https://docs.microsoft.com/azure/?pivot=products&panel=network)voor andere netwerkonderdelen.



## <a name="prepare"></a>Voorbereiden
In de volgende stappen ziet u hoe u de virtuele machine voorbereidt op de verhuizing met Azure Site Recovery als oplossing. 

### <a name="create-the-vault-in-any-region-except-the-source-region"></a>De kluis maken in een regio, behalve het brongebied

1. Meld u aan bij de [Azure portal](https://portal.azure.com) > **Recovery Services**.
1. Selecteer > **Back-up- en siteherstel****van hulpprogramma's** > voor **bronnenbeheer maken**.
1. Bij **Naam** geeft u de beschrijvende naam **ContosoVMVault** op. Als u meer dan één abonnement hebt, selecteert u het gewenste abonnement.
1. Maak de resourcegroep **ContosoRG**.
1. Geef een Azure-regio op. Zie geografische beschikbaarheid in [de prijsdetails van Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/)om ondersteunde regio's te controleren.
1. Selecteer **overzicht** > **ContosoVMVault** > **+Repliceren**in **vaults van Recovery Services.**
1. Bij **Bron** selecteert u **Azure**.
1. Bij **Bronlocatie** selecteert u de Azure-bronregio waar uw VM’s momenteel worden uitgevoerd.
1. Selecteer het Resource Manager-implementatiemodel. Selecteer vervolgens het **Bronabonnement** en de **Bronresourcegroep**.
1. Selecteer **OK** om de instellingen op te slaan.

### <a name="enable-replication-for-azure-vms-and-start-copying-the-data"></a>Replicatie voor Azure VM's inschakelen en beginnen met het kopiëren van de gegevens

Siteherstel haalt een lijst op met de VM's die zijn gekoppeld aan de abonnements- en resourcegroep.

1. Selecteer in de volgende stap de VM die u wilt verplaatsen en selecteer **OK**.
1. Selecteer Herstel **na noodgevallen**in **Instellingen**.
1. Selecteer in **Doelgebied voor noodherstel** > **configureren**het doelgebied waaraan u zult repliceren.
1. Accepteer voor deze zelfstudie de overige standaardinstellingen.
1. Selecteer **Replicatie inschakelen**. Met deze stap wordt een taak gestart om replicatie voor de VM in te schakelen.

    ![Replicatie inschakelen](media/tutorial-migrate-azure-to-azure/settings.png)

## <a name="move"></a>Verplaatsen

In de volgende stappen ziet u hoe u de verplaatsing naar het doelgebied uitvoert.

1. Ga naar de kluis. Selecteer in**gerepliceerde items** **instellingen** > de VM en selecteer **Vervolgens Failover**.
2. Bij **Failover** selecteert u **Meest recente**.
3. Selecteer **Sluit de computer af voordat de failover wordt gestart**. Site Recovery sluit de virtuele bronmachine af voordat de failover wordt geactiveerd. De failover wordt voortgezet zelfs als het afsluiten is mislukt. U de failovervoortgang volgen op de pagina **Vacatures.**
4. Controleer nadat de taak is voltooid, of de VM zoals verwacht wordt weergegeven in het doelazure-gebied.


## <a name="discard"></a>Verwijderen 

Als u de verplaatste vm hebt gecontroleerd en moet u de wijzigingspunt wijzigen of wilt u teruggaan naar een vorig punt, selecteert u in de **gerepliceerde items**met de rechtermuisknop het VM-> **herstelpunt wijzigen**. Met deze stap u een ander herstelpunt en failover opgeven. 


## <a name="commit"></a>Doorvoeren 

Zodra u de verplaatste VM hebt gecontroleerd en klaar bent om de wijziging te maken, selecteert u in de **gerepliceerde items**de rechtermuisknop op de VM-> **Commit**. Met deze stap wordt het verplaatsingsproces naar het doelgebied voltooid. Wacht tot de commit taak is afgelopen.

## <a name="clean-up"></a>Opruimen

De volgende stappen begeleiden u bij het opruimen van het brongebied en de bijbehorende resources die voor de verhuizing zijn gebruikt.

Voor alle resources die voor de verhuizing zijn gebruikt:

- Ga naar de VM. Selecteer **Replicatie uitschakelen**. Met deze stap wordt voorkomen dat het proces de gegevens voor de vm kopieert.

   > [!IMPORTANT]
   > Het is belangrijk om deze stap uit te voeren om te voorkomen dat er kosten in rekening worden gebracht voor Azure Site Recovery-replicatie.

Als u geen plannen hebt om een van de bronbronnen opnieuw te gebruiken, voert u de volgende aanvullende stappen uit:

1. Verwijder alle relevante netwerkbronnen in het brongebied die u in [vereisten](#prerequisites)hebt geïdentificeerd.
1. Verwijder het bijbehorende opslagaccount in de bronregio.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een Azure VM verplaatst naar een andere Azure-regio. Nu u noodherstel configureren voor de VM die u hebt verplaatst.

> [!div class="nextstepaction"]
> [Herstel na noodgeval instellen na een migratie](azure-to-azure-quickstart.md)

