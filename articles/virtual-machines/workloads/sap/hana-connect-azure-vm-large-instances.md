---
title: Connectiviteitsinstallatie van virtuele machines naar SAP HANA op Azure (grote exemplaren) | Microsoft Documenten
description: Connectiviteitsinstellingen van virtuele machines voor het gebruik van SAP HANA op Azure (Large Instances).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/25/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: fb6f88fbfcbd539603e435b11661c428d54f3c34
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74224733"
---
# <a name="connecting-azure-vms-to-hana-large-instances"></a>Azure-VM's verbinden met grote HANA-instanties

Het artikel [Wat is SAP HANA op Azure (Large Instances)?](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) vermeldt dat de minimale implementatie van HANA Large Instances met de SAP-toepassingslaag in Azure er als volgt uitziet:

![Azure VNet verbonden met SAP HANA op Azure (Large Instances) en on-premises](./media/hana-overview-architecture/image1-architecture.png)

Als u de virtuele netwerkzijde van Azure nader bekijkt, is er behoefte aan:

- De definitie van een virtueel Azure-netwerk waarin u de VM's van de SAP-toepassingslaag gaat implementeren.
- De definitie van een standaardsubnet in het virtuele Azure-netwerk dat echt het segment is waarin de VM's worden geïmplementeerd.
- Het azure virtuele netwerk dat is gemaakt, moet ten minste één VM-subnet en één subnet voor azure ExpressRoute-clients hebben. Aan deze subnetten moeten de IP-adresbereiken worden toegewezen, zoals gespecificeerd en besproken in de volgende secties.


## <a name="create-the-azure-virtual-network-for-hana-large-instances"></a>Het virtuele Azure-netwerk maken voor HANA-grote exemplaren

>[!Note]
>Het Azure virtual network voor HANA Large Instances moet worden gemaakt met behulp van het Azure Resource Manager-implementatiemodel. Het oudere Azure-implementatiemodel, beter bekend als het klassieke implementatiemodel, wordt niet ondersteund door de HANA Large Instance-oplossing.

U de Azure-portal, PowerShell, een Azure-sjabloon of de Azure CLI gebruiken om het virtuele netwerk te maken. (Zie [Een virtueel netwerk maken met de Azure-portal](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network)voor meer informatie. In het volgende voorbeeld kijken we naar een virtueel netwerk dat is gemaakt met behulp van de Azure-portal.

Wanneer u verwijst naar de **adresruimte** in deze documentatie, naar de adresruimte die het virtuele Azure-netwerk mag gebruiken. Deze adresruimte is ook het adresbereik dat het virtuele netwerk gebruikt voor het uitdragen van BGP-route. Deze **adresruimte** is hier te zien:

![Adresruimte van een Virtueel Azure-netwerk dat wordt weergegeven in de Azure-portal](./media/hana-overview-connectivity/image1-azure-vnet-address-space.png)

In het vorige voorbeeld, met 10.16.0.0/16, kreeg het virtuele Azure-netwerk een vrij groot en breed IP-adresbereik om te gebruiken. Daarom kunnen alle IP-adresbereiken van volgende subnetten binnen dit virtuele netwerk hun bereik binnen die adresruimte hebben. We raden meestal niet zo'n groot adresbereik aan voor één virtueel netwerk in Azure. Maar laten we eens kijken naar de subnetten die zijn gedefinieerd in het virtuele Azure-netwerk:

![Subnetten voor azure-netwerken en hun IP-adresbereiken](./media/hana-overview-connectivity/image2b-vnet-subnets.png)

We kijken naar een virtueel netwerk met een eerste VM-subnet (hier "standaard" genoemd) en een subnet genaamd "GatewaySubnet".

In de twee vorige afbeeldingen omvat de **virtuele netwerkadresruimte** zowel **het subnet-IP-adresbereik van de Azure VM** als dat van de virtuele netwerkgateway.

U de **virtuele netwerkadresruimte** beperken tot de specifieke bereiken die door elk subnet worden gebruikt. U ook de **virtuele netwerkadresruimte** van een virtueel netwerk definiëren als meerdere specifieke bereiken, zoals hier wordt weergegeven:

![Azure virtual network address space with two spaces Azure virtual network address space with two spaces Azure virtual network address space with two spaces Azure virtual](./media/hana-overview-connectivity/image3-azure-vnet-address-space_alternate.png)

In dit geval heeft de **virtuele netwerkadresruimte** twee spaties gedefinieerd. Ze zijn hetzelfde als de IP-adresbereiken die zijn gedefinieerd voor het subnet-IP-adresbereik van de Azure VM en de virtuele netwerkgateway. 

U elke naamgevingsstandaard gebruiken die u wilt voor deze tenant-subnetten (VM-subnetten). Er moet echter **altijd één en slechts één gateway-subnet zijn voor elk virtueel netwerk** dat verbinding maakt met het SAP HANA op Azure (Large Instances) ExpressRoute-circuit. **Dit gateway subnet moet "GatewaySubnet" heten** om ervoor te zorgen dat de ExpressRoute gateway correct is geplaatst.

> [!WARNING] 
> Het is van cruciaal belang dat het gatewaysubnet altijd "GatewaySubnet" wordt genoemd.

U meerdere VM-subnetten en niet-aaneengesloten adresbereiken gebruiken. Deze adresbereiken moeten worden gedekt door de **virtuele netwerkadresruimte** van het virtuele netwerk. Ze kunnen in een geaggregeerde vorm zijn. Ze kunnen ook in een lijst van de exacte bereiken van de VM-subnetten en het gateway-subnet staan.

Hieronder volgt een overzicht van de belangrijke feiten over een virtueel Azure-netwerk dat verbinding maakt met HANA Large Instances:

- U moet de **ruimte voor virtueel netwerkadres** indienen bij Microsoft wanneer u een eerste implementatie van HANA Large Instances uitvoert. 
- De **virtuele netwerkadresruimte** kan een groter bereik zijn dat de bereiken dekt voor zowel het subnet-IP-adresbereik van de Azure VM als de virtuele netwerkgateway.
- U ook meerdere bereiken indienen die betrekking hebben op de verschillende IP-adresbereiken van VM-subnet-IP-adresbereik(s) en het IP-adresbereik van de virtuele netwerkgateway.
- De gedefinieerde **virtuele netwerkadresruimte** wordt gebruikt voor het uitdragen van BGP-routering.
- De naam van het gatewaysubnet moet zijn: **"GatewaySubnet"**.
- De adresruimte wordt gebruikt als filter aan de HANA Large Instance-kant om verkeer naar de HANA Large Instance-eenheden van Azure toe te staan of te weigeren. De BGP-routeringsgegevens van het virtuele Azure-netwerk en de IP-adresbereiken die zijn geconfigureerd voor filtering aan de HANA-kant van grote instanties, moeten overeenkomen. Anders kunnen verbindingsproblemen optreden.
- Er zijn enkele details over de gateway subnet die later worden besproken, in de sectie **Het aansluiten van een virtueel netwerk met HANA Large Instance ExpressRoute.**



## <a name="different-ip-address-ranges-to-be-defined"></a>Verschillende te definiëren IP-adresbereiken 

Sommige IP-adresbereiken die nodig zijn voor het implementeren van HANA Large Instances, zijn al geïntroduceerd. Maar er zijn meer IP-adres bereiken die ook belangrijk zijn. Niet alle volgende IP-adresbereiken hoeven bij Microsoft te worden ingediend. U moet ze echter wel definiëren voordat u een verzoek voor de eerste implementatie verzendt:

- **Virtuele netwerkadresruimte**: De **virtuele netwerkadresruimte** is de IP-adresbereiken die u toewijst aan de parameter adresruimte in de virtuele Azure-netwerken. Deze netwerken maken verbinding met de SAP HANA Large Instance-omgeving. We raden aan dat deze parameter adresruimte een multiregelwaarde is. Het moet bestaan uit het subnetbereik van de Azure VM en het subnetbereik(en) van de Azure-gateway. Dit subnetbereik werd weergegeven in de vorige afbeeldingen. Het mag NIET overlappen met uw on-premises of server IP-pool of ER-P2P-adresbereiken. Hoe krijg je deze IP-adres bereik (s)? Uw bedrijfsnetwerkteam of serviceprovider moet een of meerdere IP-adresbereik(en) bieden die niet binnen uw netwerk worden gebruikt. Het subnet van uw Azure VM is bijvoorbeeld 10.0.1.0/24 en het subnet van uw Azure-gatewaysubnet is 10.0.2.0/28.  We raden u aan de status van uw Azure-netwerkadres te definiëren als: 10.0.1.0/24 en 10.0.2.0/28. Hoewel de adresruimtewaarden kunnen worden samengevoegd, raden we u aan deze aan te passen aan de subnetbereiken. Op deze manier u per ongeluk voorkomen dat ongebruikte IP-adresbereiken worden hergebruikt binnen grotere adresruimten elders in uw netwerk. **De virtuele netwerkadresruimte is een IP-adresbereik. Het moet worden ingediend bij Microsoft wanneer u vraagt om een eerste implementatie**.
- **IP-adresbereik azure VM-subnet:** Dit IP-adresbereik is het bereik dat u toewijst aan de subnetparameter Azure virtual network. Deze parameter bevindt zich in uw virtuele Azure-netwerk en maakt verbinding met de SAP HANA Large Instance-omgeving. Dit IP-adresbereik wordt gebruikt om IP-adressen toe te wijzen aan uw Azure VM's. De IP-adressen buiten dit bereik mogen verbinding maken met uw SAP HANA Large Instance server(s). Indien nodig u meerdere Azure VM-subnetten gebruiken. We raden een CIDR-blok van 24 euro aan voor elk Azure VM-subnet. Dit adresbereik moet deel uitmaken van de waarden die worden gebruikt in de azure-ruimte voor virtuele netwerkadres. Hoe krijg je dit IP-adresbereik? Uw bedrijfsnetwerkteam of serviceprovider moet een IP-adresbereik bieden dat niet binnen uw netwerk wordt gebruikt.
- **IP-adresbereik voor virtuele netwerkgateways:** Afhankelijk van de functies die u van plan bent te gebruiken, is de aanbevolen grootte:
   - Ultra-performance ExpressRoute gateway: /26 adres blok - vereist voor Type II klasse van SKU's.
   - Coëxistentie met VPN en ExpressRoute met behulp van een high-performance ExpressRoute virtuele netwerk gateway (of kleiner): / 27 adres blok.
   - Alle andere situaties: /28 adresblok. Dit adresbereik moet deel uitmaken van de waarden die worden gebruikt in de waarden 'VNet-adresruimte'. Dit adresbereik moet deel uitmaken van de waarden die worden gebruikt in de waarden van de azure-adresruimte voor virtuele netwerken die u bij Microsoft indient. Hoe krijg je dit IP-adresbereik? Uw bedrijfsnetwerkteam of serviceprovider moet een IP-adresbereik bieden dat momenteel niet binnen uw netwerk wordt gebruikt. 
- **Adresbereik voor ER-P2P-connectiviteit:** Dit bereik is het IP-bereik voor uw SAP HANA Large Instance ExpressRoute (ER) P2P-verbinding. Dit bereik van IP-adressen moet een /29 CIDR IP-adresbereik zijn. Dit bereik mag NIET overlappen met uw on-premises of andere Azure IP-adresbereiken. Dit IP-adresbereik wordt gebruikt om de ER-connectiviteit in te stellen vanaf uw Virtuele ExpressRoute-gateway naar de SAP HANA Large Instance-servers. Hoe krijg je dit IP-adresbereik? Uw bedrijfsnetwerkteam of serviceprovider moet een IP-adresbereik bieden dat momenteel niet binnen uw netwerk wordt gebruikt. **Dit bereik is een IP-adresbereik. Het moet worden ingediend bij Microsoft wanneer u vraagt om een eerste implementatie**.  
- **Adresbereik van server-IP-groep:** Dit IP-adresbereik wordt gebruikt om het afzonderlijke IP-adres toe te wijzen aan HANA-servers voor grote instanties. De aanbevolen subnetgrootte is een /24 CIDR-blok. Indien nodig kan het kleiner zijn, met slechts 64 IP-adressen. Vanuit dit bereik zijn de eerste 30 IP-adressen gereserveerd voor gebruik door Microsoft. Zorg ervoor dat u rekening houdt met dit feit wanneer u de grootte van het bereik kiest. Dit bereik mag NIET overlappen met uw on-premises of andere Azure IP-adressen. Hoe krijg je dit IP-adresbereik? Uw bedrijfsnetwerkteam of serviceprovider moet een IP-adresbereik bieden dat momenteel niet binnen uw netwerk wordt gebruikt.  **Dit bereik is een IP-adresbereik, dat moet worden ingediend bij Microsoft wanneer u om een eerste implementatie vraagt.**

Optionele IP-adresbereiken die uiteindelijk aan Microsoft moeten worden ingediend:

- Als u ervoor kiest [expressroute global reach](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) te gebruiken om directe routering van on-premises naar HANA Large Instance-eenheden in te schakelen, moet u een ander IP-adresbereik van 29 reserveren. Dit bereik overlapt mogelijk niet met een van de andere IP-adresbereiken die u eerder hebt gedefinieerd.
- Als u ervoor kiest [expressroute global reach](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) te gebruiken om directe routering van een HANA Large Instance-tenant in een Azure-regio naar een andere HANA Large Instance-tenant in een andere Azure-regio in te schakelen, moet u een ander IP-adresbereik van 29 reserveren. Dit bereik overlapt mogelijk niet met een van de andere IP-adresbereiken die u eerder hebt gedefinieerd.

Voor meer informatie over ExpressRoute Global Reach en het gebruik rond HANA grote exemplaren, raadpleegt u de documenten:

- [SAP HANA (Large Instances) netwerkarchitectuur](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture)
- [Een virtueel netwerk verbinden met grote exemplaren van HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-connect-vnet-express-route)
 
U moet de IP-adresbereiken definiëren en plannen die eerder zijn beschreven. U hoeft ze echter niet allemaal naar Microsoft te verzenden. De IP-adresbereiken die u aan Microsoft moet noemen, zijn:

- Azure virtual network address space(s)
- Adresbereik voor ER-P2P-connectiviteit
- Adresbereik van server-IP-groep

Als u extra virtuele netwerken toevoegt die verbinding moeten maken met HANA Large Instances, moet u de nieuwe Azure-adresruimte voor virtuele netwerken indienen die u aan Microsoft toevoegt. 

Hieronder volgt een voorbeeld van de verschillende bereiken en enkele voorbeeldbereiken die u moet configureren en uiteindelijk aan Microsoft moet verstrekken. De waarde voor de azure-adresruimte voor virtuele netwerken wordt in het eerste voorbeeld niet samengevoegd. Het is echter gedefinieerd uit de bereiken van het eerste Azure VM-subnet-IP-adresbereik en het subnet-IP-adresbereik voor virtuele netwerkgateways. 

U meerdere VM-subnetten binnen het virtuele Azure-netwerk gebruiken wanneer u de extra IP-adresbereiken van de extra VM-subnet(s) configureert en verzendt als onderdeel van de azure-virtuele netwerkadresruimte.

![Minimale implementatie van IP-adresbereiken vereist in SAP HANA op Azure (Large Instances)](./media/hana-overview-connectivity/image4b-ip-addres-ranges-necessary.png)

De afbeelding toont niet het extra IP-adresbereik(en) dat nodig is voor het optionele gebruik van ExpressRoute Global Reach.

U ook de gegevens die u bij Microsoft indient, samenvoegen. In dat geval bevat de adresruimte van het virtuele Azure-netwerk slechts één ruimte. Met behulp van het IP-adres varieert van het eerdere voorbeeld, de geaggregeerde virtuele netwerk adresruimte kan eruit zien als de volgende afbeelding:

![Tweede mogelijkheid van IP-adresbereiken vereist in SAP HANA op Azure (Large Instances) minimale implementatie](./media/hana-overview-connectivity/image5b-ip-addres-ranges-necessary-one-value.png)

In het voorbeeld hebben we in plaats van twee kleinere bereiken die de adresruimte van het virtuele Azure-netwerk hebben gedefinieerd, één groter bereik dat 4096 IP-adressen omvat. Zo'n grote definitie van de adresruimte laat een aantal vrij grote reeksen ongebruikt. Aangezien de waarde(en) van de virtuele netwerkadresruimte wordt gebruikt voor het uitdragen van BGP-route, kan het gebruik van de ongebruikte bereiken on-premises of elders in uw netwerk leidend probleem veroorzaken. De afbeelding toont niet het extra IP-adresbereik(en) dat nodig is voor het optionele gebruik van ExpressRoute Global Reach.

We raden u aan de adresruimte goed af te stemmen op de werkelijke subnetadresruimte die u gebruikt. Indien nodig, zonder downtime op het virtuele netwerk, u later altijd nieuwe adresruimtewaarden toevoegen.
 
> [!IMPORTANT] 
> Elk IP-adresbereik in ER-P2P, de IP-groep van de server en de azure-virtuele netwerkadresruimte mogen **NIET** overlappen met elkaar of met een ander bereik dat in uw netwerk wordt gebruikt. Elk moet discreet zijn. Zoals de twee vorige graphics laten zien, kunnen ze ook niet een subnet van een ander bereik. Als er overlappingen zijn tussen bereiken, maakt het virtuele Azure-netwerk mogelijk geen verbinding met het ExpressRoute-circuit.

## <a name="next-steps-after-address-ranges-have-been-defined"></a>Volgende stappen nadat adresbereiken zijn gedefinieerd
Nadat de IP-adresbereiken zijn gedefinieerd, moeten de volgende dingen gebeuren:

1. Verzend de IP-adresbereiken voor de azure-locatie adresruimte voor virtuele netwerken, de ER-P2P-connectiviteit en het IP-pooladresbereik van de server, samen met andere gegevens die aan het begin van het document zijn vermeld. Op dit punt u ook beginnen met het maken van het virtuele netwerk en de VM-subnetten. 
2. Er wordt een ExpressRoute-circuit gemaakt door Microsoft tussen uw Azure-abonnement en de HANA Large Instance-stempel.
3. Er wordt een tenantnetwerk gemaakt op de stempel Voor grote instanties van Microsoft.
4. Microsoft configureert netwerken in de SAP HANA op Azure (Large Instances) infrastructuur om IP-adressen te accepteren van uw Azure virtuele netwerkadresruimte die communiceert met HANA Large Instances.
5. Afhankelijk van de specifieke SAP HANA op Azure (Large Instances) SKU die u hebt gekocht, wijst Microsoft een rekeneenheid toe in een tenantnetwerk. Het wijst ook en monteert opslag, en installeert het besturingssysteem (SUSE of Red Hat Linux). IP-adressen voor deze eenheden worden uit het IP-pooladresbereik van de server gehaald dat u bij Microsoft hebt ingediend.

Aan het einde van het implementatieproces levert Microsoft de volgende gegevens aan u:
- Informatie die nodig is om uw virtuele Azure-netwerk(en) te verbinden met het ExpressRoute-circuit dat virtuele Azure-netwerken verbindt met HANA Large Instances:
     - Autorisatiesleutel(en)
     - ExpressRoute PeerID
- Gegevens voor toegang tot HANA Large Instances nadat u het ExpressRoute-circuit en het virtuele Azure-netwerk hebt ingesteld.

U ook de volgorde van het verbinden van HANA Large Instances vinden in de installatie van [SAP HANA op Azure (Large Instances).](https://azure.microsoft.com/resources/sap-hana-on-azure-large-instances-setup/) Veel van de volgende stappen worden weergegeven in een voorbeeldimplementatie in dat document. 

## <a name="next-steps"></a>Volgende stappen

- Raadpleeg [het verbinden van een virtueel netwerk met HANA Large Instance ExpressRoute.](hana-connect-vnet-express-route.md)
