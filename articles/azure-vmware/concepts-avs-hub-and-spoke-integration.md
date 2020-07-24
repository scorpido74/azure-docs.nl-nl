---
title: 'Concept: een implementatie van een Azure VMware-oplossing (AVS) integreren in een hub-en spoke-architectuur'
description: Meer informatie over de aanbevelingen voor het integreren van een implementatie van een Azure VMware-oplossing (AVS) in een bestaande of een nieuwe hub-en spoke-architectuur in Azure.
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: 0d95ed81c5188eab0dc508f5320549c4a402e151
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87062924"
---
# <a name="integrate-azure-vmware-solution-avs-in-a-hub-and-spoke-architecture"></a>Azure VMware-oplossing (AVS) integreren in een hub-en spoke-architectuur

In dit artikel bieden we aanbevelingen voor het integreren van een implementatie van een Azure VMware-oplossing (AVS) in een bestaande of een nieuwe [hub-en spoke-architectuur](/azure/architecture/reference-architectures/hybrid-networking/shared-services) in Azure. 

In het hub-en-spoke-scenario wordt uitgegaan van een hybride cloud omgeving met workloads op:

* Systeem eigen Azure met IaaS-of PaaS-Services
* SYNCHRONISATIE 
* on-premises vSphere

## <a name="architecture"></a>Architectuur

De *hub* is een Azure-Virtual Network die fungeert als een centraal punt van connectiviteit met uw persoonlijke cloud van on-premises en AVS. De *spokes* zijn virtuele netwerken die zijn gekoppeld aan de hub om intervirtuele netwerk communicatie in te scha kelen.

Verkeer tussen het on-premises Data Center, de privécloud en de hub loopt via ExpressRoute-verbindingen. Spoke Virtual Networks bevatten doorgaans IaaS werk belastingen, maar kunnen PaaS-services zoals [app service Environment](../app-service/environment/intro.md)hebben, die directe integratie met Virtual Network hebben, of andere PaaS-services waarvoor [Azure private link](../private-link/index.yml) is ingeschakeld. 

In het diagram ziet u een voor beeld van een hub-en spoke-implementatie in azure die is verbonden met on-premises en AVS via ExpressRoute.

:::image type="content" source="./media/hub-spoke/avs-hub-and-spoke-deployment.png" alt-text="Implementatie van AVS hub en spoke-integratie":::




De architectuur heeft de volgende hoofd onderdelen:

-   **On-premises site:** On-premises data centers van de klant die zijn verbonden met Azure via een snelle route verbinding.

-   **Privécloud:** AVS SDDC gevormd door een of meer vSphere-clusters, elk met Maxi maal 16 knoop punten.

-   **ExpressRoute-gateway:** Hiermee schakelt u de communicatie tussen de Privécloud in de Cloud, het on-premises netwerk, gedeelde services op het virtuele hub-netwerk en werk belastingen die worden uitgevoerd op spoke virtuele netwerken.

    > [!NOTE]
    > **Overwegingen voor S2S-VPN:** Voor de productie-implementaties van AVS wordt Azure S2S niet ondersteund vanwege netwerk vereisten voor HCX. Voor een haalbaarheids test of een niet-productie-implementatie die geen HCX vereist, kan deze echter worden gebruikt.

-   **Virtueel netwerk hub:** Fungeert als het centrale punt van connectiviteit naar uw on-premises netwerk en de privécloud.

-   **Spoke-virtueel netwerk**

    -   **IaaS Spaak:** Een IaaS-spoke fungeert als host voor Azure IaaS-werk belastingen, waaronder VM-beschikbaarheids sets en schaal sets voor virtuele machines en de bijbehorende netwerk onderdelen.

    -   **PaaS Spaak:** Een PaaS Spaak fungeert als host voor Azure PaaS Services met privé-adres Sering dankzij het [persoonlijke eind punt](../private-link/private-endpoint-overview.md) en de [persoonlijke koppeling](../private-link/private-link-overview.md).

-   **Azure firewall:** Fungeert als het centrale stuk om het verkeer tussen de spokes, de on-premises en de AVS te segmenteren.

-   **Application Gateway:** Beschrijft en beveiligt web-apps die worden uitgevoerd op Azure IaaS/PaaS of virtuele machines van AVS (Vm's). Het is geïntegreerd met andere services, zoals API Management.

## <a name="network-and-security-considerations"></a>Netwerk-en beveiligings overwegingen

Met ExpressRoute-verbindingen kunnen verkeer tussen on-premises, AVS en de Azure-netwerk infrastructuur worden geplaatst. AVS maakt gebruik van [ExpressRoute Global Reach](../expressroute/expressroute-global-reach.md) voor het implementeren van deze verbinding.

On-premises connectiviteit kan ook gebruikmaken van ExpressRoute Global Reach, maar dit is niet verplicht.

* **On-premises naar AVS-verkeer stroom**

  :::image type="content" source="media/hub-spoke/on-prem-to-avs-traffic-flow.png" alt-text="On-premises naar AVS-verkeer stroom":::


* **AVS naar hub VNET-verkeers stroom**

  :::image type="content" source="media/hub-spoke/avs-to-hub-vnet-traffic-flow.png" alt-text="Automatische synchronisatie van AVS naar hub verkeer van virtueel netwerk":::


Meer informatie over AVS-netwerken en interconnectiviteit-concepten vindt u in de [AVS-product documentatie](./concepts-networking.md).

### <a name="traffic-segmentation"></a>Segmentatie van verkeer

[Azure firewall](../firewall/index.yml) is het centrale gedeelte van de hub-en spoke-topologie, geïmplementeerd op het virtuele hub-netwerk. Gebruik Azure Firewall of een ander door Azure ondersteund virtueel netwerk apparaat om verkeers regels vast te leggen en de communicatie tussen de verschillende spokes, on-premises en AVS-workloads te segmenteren.

Maak route tabellen om het verkeer naar Azure Firewall te sturen.  Voor de spoke-virtuele netwerken maakt u een route waarmee de standaard route wordt ingesteld op de interne interface van Azure Firewall, op deze manier wanneer een werk belasting in de Virtual Network de AVS-adres ruimte moet bereiken, kan de firewall deze evalueren en de bijbehorende verkeers regel Toep assen om deze toe te staan of te weigeren.  

:::image type="content" source="media/hub-spoke/create-route-table-to-direct-traffic.png" alt-text="Route tabellen maken voor het omleiden van verkeer naar Azure Firewall":::


> [!IMPORTANT]
> Een route met het adres voorvoegsel 0.0.0.0/0 voor de **GatewaySubnet** -instelling wordt niet ondersteund.

Routes instellen voor specifieke netwerken op de bijbehorende route tabel. Routes voor het bereiken van AVS-beheer en werk belastingen IP-voor voegsels van on-premises en vice versa, routeren bijvoorbeeld alle verkeer van on-premises naar AVS-privécloud via Azure Firewall.

:::image type="content" source="media/hub-spoke/specify-gateway-subnet-for-route-table.png" alt-text="Routes instellen voor specifieke netwerken op de bijbehorende route tabel":::

Een tweede niveau van Traffic segmentatie met behulp van de netwerk beveiligings groepen binnen de spokes en de hub om een meer gedetailleerd verkeers beleid te maken. 


### <a name="application-gateway"></a>Application Gateway

Azure-toepassing gateway v1 en v2 zijn getest met web apps die worden uitgevoerd op de AVS-Vm's als een back-end-groep. Application Gateway is momenteel de enige methode die wordt ondersteund om web-apps die worden uitgevoerd op de AVS-Vm's op internet beschikbaar te maken. Ook kunnen de apps veilig worden blootgesteld aan interne gebruikers.

:::image type="content" source="media/hub-spoke/avs-second-level-traffic-segmentation.png" alt-text="Tweede niveau van verkeers segmentatie met behulp van de netwerk beveiligings groepen":::


### <a name="jumpbox-and-azure-bastion"></a>JumpBox en Azure Bastion

Toegang tot de AVS-omgeving met JumpBox, een Windows 10-of Windows Server-VM die is geïmplementeerd in het subnet van de gedeelde service binnen het virtuele hub-netwerk.

Implementeer [Microsoft Azure Bastion](../bastion/index.yml) -service in het virtuele netwerk van de hub als beveiligings best practice. Azure Bastion biedt naadloze RDP-en SSH-toegang tot Vm's die zijn geïmplementeerd op Azure zonder dat open bare IP-adressen op deze resources hoeven te worden ingericht. Nadat u de Azure Bastion-service hebt ingericht, kunt u vanuit de Azure Portal toegang krijgen tot de geselecteerde VM. Nadat de verbinding tot stand is gebracht, wordt er een nieuw tabblad geopend, waarin het JumpBox-bureau blad wordt weer gegeven, en op dat bureau blad hebt u toegang tot het beheer vlak van de privécloud.

> [!IMPORTANT]
> Geef geen openbaar IP-adres aan de JumpBox-VM of open de poort 3389/TCP voor het open bare Internet. 


:::image type="content" source="media/hub-spoke/azure-bastion-hub-vnet.png" alt-text="Virtueel Azure Bastion hub-netwerk":::


## <a name="azure-dns-resolution-considerations"></a>Overwegingen voor Azure DNS oplossingen

Voor Azure DNS oplossing zijn er twee opties beschikbaar:

-   Gebruik de Azure Active Directory (Azure AD) domein controllers die zijn geïmplementeerd op de hub (beschreven in [identiteits overwegingen](#identity-considerations)) als naam servers.

-   Een Azure DNS privé zone implementeren en configureren.

De beste manier is om beide te combi neren om een betrouw bare naam omzetting te bieden voor AVS, on-premises en Azure.

Als algemene ontwerp aanbeveling gebruikt u de bestaande Azure DNS-infra structuur (in dit geval Active Directory-geïntegreerde DNS) die is geïmplementeerd op ten minste twee virtuele Azure-machines die zijn geïmplementeerd in het VM-netwerk en die in de spoke-virtuele netwerken zijn geconfigureerd voor het gebruik van deze Azure DNS servers in de DNS-instellingen.

Azure Privé-DNS kan nog steeds worden gebruikt wanneer de Azure Privé-DNS-zone is gekoppeld aan de virtuele netwerken, en DNS-servers worden gebruikt als hybride resolver met voorwaardelijk door sturen naar on-premises/AVS waarbij DNS-namen worden uitgevoerd op basis van de Azure Privé-DNS-infra structuur van de klant.

Er zijn verschillende aandachtspunten voor het Azure DNS van particuliere zones:

* Automatische registratie moet zijn ingeschakeld om Azure DNS de levens cyclus van de DNS-records voor de Vm's die in spoke Virtual Networks zijn geïmplementeerd, automatisch te beheren.
* Het maximum aantal privé-DNS-zones waaraan een virtueel netwerk kan worden gekoppeld met de functie voor registratie is ingeschakeld, is slechts één.
* Het maximum aantal privé-DNS-zones waaraan een virtueel netwerk kan worden gekoppeld, is 1000 zonder dat de registratie is ingeschakeld.

On-premises en AVS-servers kunnen worden geconfigureerd met voorwaardelijke doorstuur server voor het oplossen van Vm's in azure voor de Azure Privé-DNS-zone.

## <a name="identity-considerations"></a>Identiteits overwegingen

Voor identiteits doeleinden is de beste benadering om ten minste één AD-domein controller op de hub te implementeren, met behulp van het subnet van de gedeelde service, in het ideale niveau twee in een zone-gedistribueerde mode of een VM-beschikbaarheidsset. Zie [Azure Architecture Center](/azure/architecture/reference-architectures/identity/adds-extend-domain) voor het uitbreiden van uw on-PREMISes AD-domein naar Azure.

Implementeer daarnaast een andere domein controller op de AVS-zijde om te fungeren als identiteits-en DNS-bron binnen de vSphere-omgeving.

Voor vCenter en SSO stelt u een identiteits bron in het Azure Portal in, op identiteits-identiteits ** \> \> bronnen beheren**.

Als aanbevolen best practice kunt u het [AD-domein integreren met Azure Active Directory](/azure/architecture/reference-architectures/identity/azure-ad).

<!-- LINKS - external -->
[Azure Architecture Center]: /azure/architecture/

[Hub & Spoke topology]: /azure/architecture/reference-architectures/hybrid-networking/hub-spoke

[Azure networking documentation]: ../networking/index.yml

<!-- LINKS - internal -->
