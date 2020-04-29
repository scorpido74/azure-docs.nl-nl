---
title: Virtuele Azure-machines verplaatsen naar een andere regio met Azure Site Recovery
description: Azure Site Recovery gebruiken om Azure IaaS-VM’s te verplaatsen van de ene Azure-regio naar een andere.
services: site-recovery
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: 4882206692c334d6ab6af28feb5d2cba5277eea1
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "78303933"
---
# <a name="move-azure-vms-to-another-region"></a>Virtuele Azure-machines verplaatsen naar een andere regio

Er zijn verschillende scenario's waarin u uw bestaande Azure IaaS virtuele machines (Vm's) wilt verplaatsen van de ene regio naar een andere. U wilt bijvoorbeeld de betrouw baarheid en beschik baarheid van uw bestaande Vm's verbeteren, om de beheersbaarheid te verbeteren of om redenen om beheer te verplaatsen. Zie [overzicht van Azure VM verplaatsen](azure-to-azure-move-overview.md)voor meer informatie. 

U kunt de [Azure site Recovery](site-recovery-overview.md) -service gebruiken voor het beheren en organiseren van herstel na nood gevallen van on-premises machines en Azure-vm's voor bedrijfs continuïteit en herstel na nood gevallen (BCDR). U kunt Site Recovery ook gebruiken om de verplaatsing van Azure-Vm's naar een secundaire regio te beheren.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> 
> * Controleer de vereisten voor de verplaatsing
> * De bron-Vm's en de doel regio voorbereiden
> * De gegevens kopiëren en replicatie inschakelen
> * De configuratie testen en de verplaatsing uitvoeren
> * De resources in de bron regio verwijderen
> 
> [!NOTE]
> In deze zelf studie leert u hoe u virtuele Azure-machines naar een andere regio kunt verplaatsen. Als u de beschik baarheid wilt verbeteren door Vm's in een beschikbaarheidsset te verplaatsen naar een zone vastgemaakte Vm's in een andere regio, raadpleegt u de [zelf studie Azure-Vm's verplaatsen naar Beschikbaarheidszones](move-azure-vms-avset-azone.md).

## <a name="prerequisites"></a>Vereisten

- Zorg ervoor dat de Azure-Vm's zich bevinden in de Azure-regio van waaruit u wilt verplaatsen.
- Controleer of uw keuze van de [bron regio-doel regio combinatie wordt ondersteund](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support)en maak een weloverwogen beslissing over de doel regio.
- Zorg ervoor dat u inzicht hebt in de [architectuur en onderdelen voor dit scenario](azure-to-azure-architecture.md).
- Controleer de [beperkingen en vereisten voor ondersteuning](azure-to-azure-support-matrix.md).
- Controleer de account machtigingen. Als u uw gratis Azure-account hebt gemaakt, bent u de beheerder van uw abonnement. Als u niet de abonnements beheerder bent, kunt u met de beheerder samen werken om de benodigde machtigingen toe te wijzen. Als u replicatie voor een virtuele machine wilt inschakelen en gegevens in feite wilt kopiëren met behulp van Azure Site Recovery, hebt u het volgende nodig:

    - Machtigingen voor het maken van een virtuele machine in Azure-resources. De ingebouwde rol Inzender voor de virtuele machine heeft deze machtigingen, waaronder:
    - Machtiging voor het maken van een virtuele machine in de geselecteerde resourcegroep
    - Machtiging voor het maken van een virtuele machine in het geselecteerde virtuele netwerk
    - Machtigingen om naar het geselecteerde opslagaccount te schrijven
    
    - Machtigingen voor het beheren van Azure Site Recovery bewerkingen. De rol Site Recovery Inzender heeft alle machtigingen die nodig zijn voor het beheren van Site Recovery bewerkingen in een Recovery Services kluis.

- Zorg ervoor dat alle meest recente basis certificaten zich bevinden op de virtuele Azure-machines die u wilt verplaatsen. Als de meest recente basis certificaten zich niet op de virtuele machine bevinden, voor komt u dat de gegevens naar de doel regio worden gekopieerd.

- Voor Windows-VM’s moet u de meest recente Windows-updates op de VM installeren, zodat alle vertrouwde basiscertificaten op de machine aanwezig zijn. In een niet-verbonden omgeving moet u de standaardprocedures van Windows Update en de standaardprocedures voor het bijwerken van de certificaten van uw organisatie volgen.
    
- Voor Linux-Vm's volgt u de richt lijnen van uw Linux-distributeur om de meest recente vertrouwde basis certificaten en certificaatintrekkingslijsten te verkrijgen op de virtuele machine.
- Zorg ervoor dat u geen verificatie proxy gebruikt voor het beheren van de netwerk verbinding voor Vm's die u wilt verplaatsen.

- Als de virtuele machine die u wilt verplaatsen geen toegang tot internet heeft of een firewall proxy gebruikt om uitgaande toegang te beheren, [controleert u de vereisten](azure-to-azure-tutorial-enable-replication.md#set-up-outbound-network-connectivity-for-vms).

- Identificeer de bron netwerk indeling en alle resources die u momenteel gebruikt. Dit omvat, maar is niet beperkt tot load balancers, netwerk beveiligings groepen (Nsg's) en open bare Ip's.

- Controleer of u met uw Azure-abonnement virtuele machines kunt maken in de doel regio die wordt gebruikt voor herstel na nood gevallen. Neem contact op met ondersteuning voor het inschakelen van het vereiste quotum.

- Zorg ervoor dat uw abonnement voldoende bronnen heeft voor de ondersteuning van Vm's met een grootte die overeenkomt met uw bron-Vm's. Als u Site Recovery gebruikt voor het kopiëren van gegevens naar het doel, kiest Site Recovery dezelfde grootte of de dichtstbijzijnde mogelijke grootte voor de doel-VM.

- Zorg ervoor dat u een doel bron maakt voor elk onderdeel dat in de bron netwerk indeling wordt geïdentificeerd. Deze stap is belang rijk om ervoor te zorgen dat uw virtuele machines beschikken over alle functionaliteit en functies in de doel regio die u in de bron regio had.

     > [!NOTE] 
     > Azure Site Recovery detecteert en maakt automatisch een virtueel netwerk wanneer u replicatie inschakelt voor de bron-VM. U kunt ook een netwerk vooraf maken en dit toewijzen aan de virtuele machine in de gebruikers stroom voor het inschakelen van replicatie. Zoals later vermeld, moet u hand matig andere resources in de doel regio maken.

    Als u de meest gebruikte netwerk bronnen wilt maken die relevant zijn voor u op basis van de configuratie van de bron-VM, raadpleegt u de volgende documentatie:
    - [Netwerkbeveiligingsgroepen](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)
    - [Load balancers](https://docs.microsoft.com/azure/load-balancer)
    -  [Openbaar IP-adres](../virtual-network/virtual-network-public-ip-address.md)
    - Zie de [documentatie van netwerken](https://docs.microsoft.com/azure/?pivot=products&panel=network)voor andere netwerk onderdelen.



## <a name="prepare"></a>Voorbereiden
De volgende stappen laten zien hoe u de virtuele machine voorbereidt voor het verplaatsen met behulp van Azure Site Recovery als een oplossing. 

### <a name="create-the-vault-in-any-region-except-the-source-region"></a>De kluis in een wille keurige regio maken, met uitzonde ring van de bron regio

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) > -**Recovery Services**.
1. Selecteer **een resource** > **beheer hulpprogramma's** > maken**back-up en site Recovery**.
1. Bij **Naam** geeft u de beschrijvende naam **ContosoVMVault** op. Als u meer dan één abonnement hebt, selecteert u het gewenste abonnement.
1. Maak de resource groep **ContosoRG**.
1. Geef een Azure-regio op. Zie geografische Beschik baarheid in [Azure site Recovery prijs informatie](https://azure.microsoft.com/pricing/details/site-recovery/)om de ondersteunde regio's te controleren.
1. Selecteer in **Recovery Services kluizen** **overzicht** > **ContosoVMVault** > **+ repliceren**.
1. Bij **Bron** selecteert u **Azure**.
1. Bij **Bronlocatie** selecteert u de Azure-bronregio waar uw VM’s momenteel worden uitgevoerd.
1. Selecteer het Resource Manager-implementatiemodel. Selecteer vervolgens het **Bronabonnement** en de **Bronresourcegroep**.
1. Selecteer **OK** om de instellingen op te slaan.

### <a name="enable-replication-for-azure-vms-and-start-copying-the-data"></a>Replicatie inschakelen voor Azure-Vm's en beginnen met het kopiëren van de gegevens

Site Recovery haalt een lijst op met de virtuele machines die zijn gekoppeld aan het abonnement en de resource groep.

1. Selecteer in de volgende stap de virtuele machine die u wilt verplaatsen en selecteer vervolgens **OK**.
1. Selecteer in **instellingen** **herstel na nood gevallen**.
1. Selecteer in > **doel regio**voor **nood herstel configureren**de doel regio waarnaar u wilt repliceren.
1. Accepteer voor deze zelfstudie de overige standaardinstellingen.
1. Selecteer **replicatie inschakelen**. Met deze stap wordt een taak gestart om replicatie in te scha kelen voor de virtuele machine.

    ![Replicatie inschakelen](media/tutorial-migrate-azure-to-azure/settings.png)

## <a name="move"></a>Verplaatsen

De volgende stappen laten zien hoe u de overstap naar de doel regio kunt uitvoeren.

1. Ga naar de kluis. Selecteer de virtuele machine in **instellingen** > **gerepliceerde items**en selecteer vervolgens **failover**.
2. Bij **Failover** selecteert u **Meest recente**.
3. Selecteer **Sluit de computer af voordat de failover wordt gestart**. Site Recovery sluit de virtuele bronmachine af voordat de failover wordt geactiveerd. De failover wordt voortgezet zelfs als het afsluiten is mislukt. U kunt de voortgang van de failover op de pagina **taken** volgen.
4. Nadat de taak is voltooid, controleert u of de virtuele machine wordt weer gegeven in de Azure-doel regio zoals verwacht.


## <a name="discard"></a>Verwijderen 

Als u de verplaatste virtuele machine hebt gecontroleerd en wilt wijzigen in het herstel punt, of als u terug wilt gaan naar een vorig punt, selecteert u in de **gerepliceerde items**de virtuele machine > **wijzigings punt**. Deze stap biedt u de mogelijkheid om een ander herstel punt en failover naar die ene te specificeren. 


## <a name="commit"></a>Doorvoeren 

Wanneer u de verplaatste VM hebt gecontroleerd en klaar bent om de wijziging door te voeren, selecteert u in de **gerepliceerde items**de virtuele machine > **door voeren**. Met deze stap wordt het verplaatsings proces voltooid naar de doel regio. Wacht tot de doorvoer taak is voltooid.

## <a name="clean-up"></a>Opruimen

De volgende stappen begeleiden u bij het opschonen van de bron regio en gerelateerde resources die zijn gebruikt voor de verplaatsing.

Voor alle resources die zijn gebruikt voor de verplaatsing:

- Ga naar de virtuele machine. Selecteer **replicatie uitschakelen**. Met deze stap wordt het proces voor het kopiëren van de gegevens voor de virtuele machine gestopt.

   > [!IMPORTANT]
   > Het is belang rijk om deze stap uit te voeren om te voor komen dat Azure Site Recovery-replicatie wordt gefactureerd.

Als u geen plannen hebt om een van de bron resources opnieuw te gebruiken, voert u de volgende aanvullende stappen uit:

1. Verwijder alle relevante netwerk bronnen in de bron regio die u hebt geïdentificeerd in [vereisten](#prerequisites).
1. Verwijder het bijbehorende opslagaccount in de bronregio.

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u een virtuele Azure-machine verplaatst naar een andere Azure-regio. U kunt nu herstel na nood gevallen configureren voor de VM die u hebt verplaatst.

> [!div class="nextstepaction"]
> [Herstel na noodgeval instellen na een migratie](azure-to-azure-quickstart.md)

