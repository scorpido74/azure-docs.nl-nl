---
title: Virtuele machine verplaatsen naar een andere regio (Azure Site Recovery)
description: Ontdek hoe u uw virtuele SQL Server-machine migreren van de ene regio naar de andere binnen Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 07/30/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 3b84119cdcc1bb7e8603de64e3d23c69dac70cc3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74022295"
---
# <a name="move-sql-server-vm-to-another-region-within-azure-with-azure-site-recovery-services"></a>SQL Server VM verplaatsen naar een andere regio binnen Azure met Azure Site Recovery-services

In dit artikel leert u hoe u Azure Site Recovery gebruiken om uw virtuele SQL Server-virtuele machine (VM) van de ene regio naar de andere binnen Azure te migreren. 

Als u een SQL Server VM naar een andere regio verplaatst, moet u het volgende doen:
1. [**Voorbereiden:**](#prepare-to-move)controleer of zowel uw bron SQL Server VM als doelregio voldoende zijn voorbereid op de verhuizing. 
1. [**Configureren:**](#configure-azure-site-recovery-vault)het verplaatsen van uw SQL Server VM vereist dat het een gerepliceerd object is binnen de azure site recovery-kluis. U moet uw SQL Server VM toevoegen aan de kluis Azure Site Recovery. 
1. [**Testen:**](#test-move-process)Voor het migreren van de SQL Server VM moet deze niet worden overgedragen van het brongebied naar het gerepliceerde doelgebied. Om ervoor te zorgen dat het verplaatsingsproces slaagt, moet u eerst testen of uw SQL Server VM met succes kan mislukken naar het doelgebied. Dit zal helpen bij het blootleggen van eventuele problemen en voorkomen dat ze bij het uitvoeren van de werkelijke beweging. 
1. [**Verplaatsen:**](#move-the-sql-server-vm)Zodra uw testfailover is geslaagd en u weet dat u veilig bent om uw SQL Server VM te migreren, u de verplaatsing van de VM naar het doelgebied uitvoeren. 
1. [**Opruimen**](#clean-up-source-resources): Verwijder de SQL Server VM uit de kluis en onnodige resources die overblijven in de brongroep om factureringskosten te voorkomen. 

## <a name="verify-prerequisites"></a>Vereisten verifiëren 

- Controleer of het verplaatsen van uw bronregio naar uw doelregio [wordt ondersteund.](../../../site-recovery/azure-to-azure-support-matrix.md#region-support)  
- Bekijk de [scenarioarchitectuur en -componenten en](../../../site-recovery/azure-to-azure-architecture.md) de [ondersteuningsbeperkingen en -vereisten](../../../site-recovery/azure-to-azure-support-matrix.md). 
- Accountmachtigingen verifiëren. Als u uw gratis Azure-account hebt gemaakt, bent u de beheerder van uw abonnement. Als u niet de abonnementsbeheerder bent, werkt u samen met de beheerder om de machtigingen toe te wijzen die u nodig hebt. Als u replicatie voor een virtuele machine wilt inschakelen en gegevens wilt kopiëren met Azure Site Recovery, moet u het als nog even hebben: 
    - Machtigingen voor het maken van een vm. De ingebouwde rol *Virtuele machinebijdrager* heeft deze machtigingen, waaronder: 
        - Machtigingen voor het maken van een vm in de geselecteerde resourcegroep. 
        - Machtigingen voor het maken van een VM in het geselecteerde virtuele netwerk. 
        - Machtigingen om naar het geselecteerde opslagaccount te schrijven. 
      - Machtigingen voor het beheren van Azure Site Recovery-bewerkingen. De rol *Siteherstelbijdrager* heeft alle machtigingen die nodig zijn om siteherstelbewerkingen in een kluis van Herstelservices te beheren.  

## <a name="prepare-to-move"></a>Voorbereiden om te bewegen
Bereid zowel de bron SQL Server VM als het doelgebied voor op de verhuizing. 

### <a name="prepare-the-source-sql-server-vm"></a>De bron SQL Server VM voorbereiden

- Zorg ervoor dat alle nieuwste hoofdcertificaten zich op de SQL Server VM bevinden die u wilt verplaatsen. Als de laatste basiscertificaten er niet zijn, voorkomen beveiligingsbeperkingen dat gegevens naar het doelgebied worden gekopieerd. 
- Installeer voor Windows VM's alle nieuwste Windows-updates op de VM, zodat alle vertrouwde rootcertificaten op de machine staan. Volg in een niet-verbonden omgeving het standaard Windows UPdate- en certificaatupdateproces voor uw organisatie. 
- Volg voor Linux VM's de richtlijnen van uw Linux-distributeur om de nieuwste vertrouwde rootcertificaten en certificaatintrekkingslijst op de VM te krijgen. 
- Zorg ervoor dat u geen verificatieproxy gebruikt om de netwerkconnectiviteit te beheren voor de VM's die u wilt verplaatsen. 
- Als de VM die u probeert te verplaatsen geen toegang heeft tot internet of als deze een firewallproxy gebruikt om uitgaande toegang te beheren, controleert u de vereisten. 
- Identificeer de indeling van de bronnetwerkindeling en alle bronnen die u momenteel gebruikt. Dit omvat, maar is niet beperkt tot load balancers, network security groups (NSGs) en openbare IP's. 

### <a name="prepare-the-target-region"></a>De doelregio voorbereiden

- Controleer of u met uw Azure-abonnement VM's maken in het doelgebied dat wordt gebruikt voor noodherstel. Neem contact op met ondersteuning voor het inschakelen van het vereiste quotum. 
- Zorg ervoor dat uw abonnement voldoende resources heeft om VM's te ondersteunen met een grootte die overeenkomt met uw bron-VM's. Als u Siteherstel gebruikt om gegevens naar het doel te kopiëren, kiest Siteherstel dezelfde grootte of de dichtstbijzijnde grootte voor de doel-vm. 
- Zorg ervoor dat u een doelbron maakt voor elke component die is geïdentificeerd in de indeling van het bronnetwerk. Deze stap is belangrijk om ervoor te zorgen dat uw VM's alle functionaliteit en functies hebben in het doelgebied dat u in het brongebied had. 
    - Azure Site Recovery detecteert en maakt automatisch een virtueel netwerk wanneer u replicatie inschakelt voor de bron-vm. U ook vooraf een netwerk maken en toewijzen aan de VM in de gebruikersstroom voor het inschakelen van replicatie. U moet handmatig andere resources in het doelgebied maken.
- Zie de volgende documentatie voor het maken van de meest gebruikte netwerkbronnen die voor u relevant zijn op basis van de bron-VM-configuratie: 
    - [Netwerkbeveiligingsgroepen](../../../virtual-network/tutorial-filter-network-traffic.md) 
    - [Load balancer](../../../load-balancer/tutorial-load-balancer-basic-internal-portal.md)
    - [Openbaar IP-adres](../../../virtual-network/virtual-network-public-ip-address.md)
    - Zie de [netwerkdocumentatie](../../../virtual-network/virtual-networks-overview.md)voor aanvullende netwerkonderdelen.
- Maak handmatig een niet-productienetwerk in het doelgebied als u de configuratie wilt testen voordat u de uiteindelijke verhuizing naar het doelgebied uitvoert. We raden deze stap aan omdat het zorgt voor minimale interferentie met het productienetwerk. 

## <a name="configure-azure-site-recovery-vault"></a>Kluis Azure Site Recovery configureren

In de volgende stappen ziet u hoe u Azure Site Recovery gebruiken om gegevens naar het doelgebied te kopiëren. Maak de kluis Herstelservices in een andere regio dan het brongebied. 

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). 
1. Kies ervoor om **een resource** te maken vanuit de linkerbovenhoek van het navigatiedeelvenster. 
1. Selecteer **IT-& beheertools** en selecteer **vervolgens Back-up en siteherstel**. 
1. Maak op het tabblad **Basisbeginselen** onder **Projectdetails**een nieuwe resourcegroep in het doelgebied of selecteer een bestaande resourcegroep in de doelgroep. 
1. Geef **onder Instantiedetails**een naam voor uw kluis op en selecteer vervolgens uw **doelregio** in de vervolgkeuzelijst. 
1. Selecteer **Controleren + Maken** om uw kluis van Herstelservices te maken. 
1. Selecteer **Alle services** in de linkerbovenhoek van het navigatiedeelvenster `recovery services`en in het zoekvaktype . 
1. (Optioneel) Selecteer de ster naast **de kluizen van Recovery Services** om deze toe te voegen aan uw snelle navigatiebalk. 
1. Selecteer **kluizen van Herstelservices** en selecteer vervolgens de kluis van Herstelservices die u hebt gemaakt. 
1. Selecteer **Repliceren**in het deelvenster **Overzicht** . 

   ![Replicatie configureren](media/virtual-machines-windows-sql-move-to-new-region/configure-replication.png)

1. Selecteer **Bron** en selecteer **Azure** als bron. Selecteer de juiste waarden voor de andere vervolgkeuzevelden, zoals de locatie voor uw bron-VM's. Alleen resourcesgroepen in het gebied **Bronlocatie** zijn zichtbaar in het veld **Bronbrongroep.** 
1. Selecteer **Virtuele machines** en kies vervolgens de virtuele machines die u wilt migreren. Selecteer **OK** om uw VM-selectie op te slaan. 
1. Selecteer **Instellingen**en kies vervolgens uw **doellocatie** in de vervolgkeuzelijst. Dit moet de resourcegroep zijn die u eerder hebt voorbereid. 
1. Zodra u de replicatie hebt aangepast, selecteert u **Doelbronnen maken** om de bronnen op de nieuwe locatie te maken. 
1. Zodra het maken van resources is voltooid, selecteert u **Replicatie inschakelen** om replicatie van uw SQL Server VM te starten van de bron naar het doelgebied.
1. U de status van replicatie controleren door naar uw herstelkluis te navigeren, **gerepliceerde items** te selecteren en de **status** van uw SQL Server VM weer te geven. Een status **van Beveiligd** geeft aan dat replicatie is voltooid. 

   ![Replicatiestatus controleren](media/virtual-machines-windows-sql-move-to-new-region/check-replication-status.png)

## <a name="test-move-process"></a>Bewegingsproces testen
In de volgende stappen ziet u hoe u Azure Site Recovery gebruiken om het verplaatsingsproces te testen. 

1. Navigeer naar de **kluis Recovery Services** in de [Azure-portal](https://portal.azure.com) en selecteer **Gerepliceerde items.** 
1. Selecteer de SQL Server VM die u wilt verplaatsen, controleer of de **replicatiestatus** **als gezond wordt** weergegeven en selecteer Vervolgens **Failover testen**. 

   ![Failover testen voor uw vm](media/virtual-machines-windows-sql-move-to-new-region/test-failover-of-replicated-vm.png)

1. Selecteer op de pagina **Failover** testen het laatste herstelpunt voor de toepassing van de app die u wilt gebruiken voor de failover, omdat dat het enige type momentopname is dat sql **server-gegevensconsistentie** kan garanderen. 
1. Selecteer het virtuele netwerk onder **het virtuele Azure-netwerk** en selecteer **OK** om failover te testen. 
   
   >[!IMPORTANT]
   > We raden u aan een apart Azure VM-netwerk te gebruiken voor de failovertest. Gebruik het productienetwerk dat is ingesteld toen u replicatie hebt ingeschakeld en waar u uw VM's uiteindelijk naartoe wilt verplaatsen. 

1. Als u de voortgang wilt controleren, navigeert u naar uw kluis, selecteert u **Sitehersteltaken** onder **Bewaken**en selecteert u de **failovertaak testen** die aan de gang is.

   ![Voortgang failovertest controleren](media/virtual-machines-windows-sql-move-to-new-region/monitor-failover-test-job.png)

1. Zodra de test is voltooid, navigeert u naar **virtuele machines** in de portal en bekijkt u de nieuw gemaakte virtuele machine. Controleer of de SQL Server VM wordt uitgevoerd, op de juiste grootte is en is verbonden met het juiste netwerk. 
1. Verwijder de VM die is gemaakt als onderdeel van de test, omdat de optie **Failover** grijs wordt totdat de failovertestbronnen zijn opgeschoond. Navigeer terug naar de kluis, selecteer **Gerepliceerde items,** selecteer de SQL Server VM en selecteer **Vervolgens Failoveropen.** Neem eventuele waarnemingen in verband met de test op en sla deze op in de sectie **Notities** en schakel het selectievakje naast **Testen in. Testfailover virtuele machines verwijderen**. Selecteer **OK** om resources op te schonen na de test. 

   ![items op te ruimen na failovertest](media/virtual-machines-windows-sql-move-to-new-region/cleanup-test-items.png)

## <a name="move-the-sql-server-vm"></a>De SQL Server VM verplaatsen 
In de volgende stappen ziet u hoe u de SQL Server VM van uw bronregio naar uw doelregio verplaatst. 

1. Navigeer naar de kluis **Recovery Services,** selecteer **Gerepliceerde items,** selecteer de VM en selecteer **Failover**. 

   ![Failover starten](media/virtual-machines-windows-sql-move-to-new-region/initiate-failover.png)

1. Selecteer het **nieuwste herstelpunt voor app-consistent** onder **Herstelpunt**. 
1. Schakel het selectievakje naast **Het afsluiten van de machine in voordat de failover begint.** Site herstel zal proberen af te sluiten van de bron VM voordat triggering de failover. Failover zal doorgaan, zelfs als shut down mislukt. 
1. Selecteer **OK** om de failover te starten.
1. U het failoverproces controleren vanaf dezelfde pagina **met sitehersteltaken** die u hebt bekeken bij het bewaken van de failovertest in de vorige sectie. 
1. Controleer nadat de taak is voltooid, of de SQL Server VM zoals verwacht in het doelgebied wordt weergegeven. 
1. Navigeer terug naar de kluis, selecteer **Gerepliceerde items,** selecteer de SQL Server VM en selecteer **Commit** om het verplaatsingsproces naar het doelgebied te voltooien. Wacht tot de commit taak is afgelopen. 
1. Registreer uw SQL Server VM bij de SQL VM-resourceprovider om **sql-beheerbaarheid** voor virtuele machines in te schakelen in de Azure-portal en functies die zijn gekoppeld aan de resourceprovider. Zie [SQL Server VM registreren met SQL VM-resourceprovider](virtual-machines-windows-sql-register-with-rp.md)voor meer informatie. 

  > [!WARNING]
  > SQL Server-gegevensconsistentie is alleen gegarandeerd met app-consistente momentopnamen. De **nieuwste verwerkte** momentopname kan niet worden gebruikt voor SQL Server-failover, omdat een momentopname voor crashherstel de consistentie van SQL Server-gegevens niet kan garanderen. 

## <a name="clean-up-source-resources"></a>Bronbronnen opschonen
Verwijder de SQL Server VM uit de kluis om factureringskosten te voorkomen en verwijdert onnodige bijbehorende resources. 

1. Navigeer terug naar de kluis **Siteherstel,** selecteer **Gerepliceerde items**en selecteer de SQL Server VM. 
1. Selecteer **Replicatie uitschakelen**. Selecteer een reden voor het uitschakelen van de beveiliging en selecteer **OK** om replicatie uit te schakelen. 

   >[!IMPORTANT]
   > Het is belangrijk om deze stap uit te voeren om te voorkomen dat er kosten in rekening worden gebracht voor Azure Site Recovery-replicatie. 

1. Als u geen plannen hebt om een van de bronnen in het brongebied opnieuw te gebruiken, verwijdert u alle relevante netwerkbronnen en bijbehorende opslagaccounts. 


## <a name="next-steps"></a>Volgende stappen

Raadpleeg voor meer informatie de volgende artikelen: 

* [Overzicht van SQL Server op een Windows VM](virtual-machines-windows-sql-server-iaas-overview.md)
* [VEELgestelde vragen over SQL Server op een Windows VM](virtual-machines-windows-sql-server-iaas-faq.md)
* [SQL Server op een windows VM-prijsrichtlijnen](virtual-machines-windows-sql-server-pricing-guidance.md)
* [SQL Server op een Windows VM-releasenotities](virtual-machines-windows-sql-server-iaas-release-notes.md)


