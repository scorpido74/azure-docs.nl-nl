---
title: 'Concept: een implementatie van een Azure VMware-oplossing integreren in een hub-en spoke-architectuur'
description: Meer informatie over de aanbevelingen voor het integreren van een implementatie van een Azure VMware-oplossing in een bestaande of een nieuwe hub-en spoke-architectuur in Azure.
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: a1ba75369a1d6f19ca7ae1f36107170f219f72d0
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/20/2020
ms.locfileid: "88649581"
---
# <a name="integrate-azure-vmware-solution-in-a-hub-and-spoke-architecture"></a>Azure VMware-oplossing integreren in een hub-en spoke-architectuur

In dit artikel bieden we aanbevelingen voor het integreren van een implementatie van een Azure VMware-oplossing in een bestaande of een nieuwe [hub-en spoke-architectuur](/azure/architecture/reference-architectures/hybrid-networking/shared-services) in Azure. 

In het hub-en-spoke-scenario wordt uitgegaan van een hybride cloud omgeving met workloads op:

* Systeem eigen Azure met IaaS-of PaaS-Services
* Azure VMware Solution 
* on-premises vSphere

## <a name="architecture"></a>Architectuur

De *hub* is een Azure-Virtual Network die fungeert als een centraal punt van connectiviteit met uw on-premises en Azure VMware-oplossing privécloud. De *spokes* zijn virtuele netwerken die zijn gekoppeld aan de hub om intervirtuele netwerk communicatie in te scha kelen.

Verkeer tussen het on-premises Data Center, de privécloud van Azure VMware-oplossing en de hub gaat via ExpressRoute-verbindingen. Spoke Virtual Networks bevatten doorgaans IaaS werk belastingen, maar kunnen PaaS-services zoals [app service Environment](../app-service/environment/intro.md)hebben, die directe integratie met Virtual Network hebben, of andere PaaS-services waarvoor [Azure private link](../private-link/index.yml) is ingeschakeld. 

In het diagram ziet u een voor beeld van een hub-en spoke-implementatie in azure die is verbonden met on-premises en Azure VMware-oplossing via ExpressRoute.

:::image type="content" source="./media/hub-spoke/avs-hub-and-spoke-deployment.png" alt-text="Implementatie van Azure VMware Solution hub en spoke-integratie":::




De architectuur heeft de volgende hoofd onderdelen:

-   **On-premises site:** On-premises data centers van de klant die zijn verbonden met Azure via een ExpressRoute-verbinding.

-   **Privécloud van Azure VMware-oplossing:** Azure VMware-oplossing SDDC gevormd door een of meer vSphere-clusters, elk met Maxi maal 16 knoop punten.

-   **ExpressRoute-gateway:** Hiermee schakelt u de communicatie tussen de privécloud-oplossing van Azure VMware, het on-premises netwerk, gedeelde services op het virtuele hub-netwerk en workloads die worden uitgevoerd op spoke virtuele netwerken.

    > [!NOTE]
    > **Overwegingen voor S2S-VPN:** Voor implementaties van Azure VMware-oplossingen worden Azure S2S niet ondersteund vanwege netwerk vereisten voor HCX. Voor een haalbaarheids test of een niet-productie-implementatie die geen HCX vereist, kan deze echter worden gebruikt.

-   **Virtueel netwerk hub:** Fungeert als het centrale punt van connectiviteit naar uw on-premises netwerk en Azure VMware-oplossing privécloud.

-   **Spoke-virtueel netwerk**

    -   **IaaS Spaak:** Een IaaS-spoke fungeert als host voor Azure IaaS-workloads, waaronder VM-beschikbaarheids sets en schaal sets voor virtuele machines en de bijbehorende netwerk onderdelen.

    -   **PaaS Spaak:** Een PaaS Spaak fungeert als host voor Azure PaaS Services met privé-adres Sering dankzij het [persoonlijke eind punt](../private-link/private-endpoint-overview.md) en de [persoonlijke koppeling](../private-link/private-link-overview.md).

-   **Azure firewall:** Fungeert als het centrale stuk om het verkeer tussen de spokes, de on-premises en de Azure VMware-oplossing te segmenteren.

-   **Application Gateway:** Beschrijft en beveiligt web-apps die worden uitgevoerd op Azure IaaS/PaaS of virtuele machines met Azure VMware-oplossingen. Het is geïntegreerd met andere services, zoals API Management.

## <a name="network-and-security-considerations"></a>Netwerk-en beveiligings overwegingen

Met ExpressRoute-verbindingen kunnen verkeer tussen on-premises, Azure VMware-oplossingen en de Azure-netwerk infrastructuur worden geplaatst. De Azure VMware-oplossing maakt gebruik van [ExpressRoute Global Reach](../expressroute/expressroute-global-reach.md) voor het implementeren van deze verbinding.

On-premises connectiviteit kan ook gebruikmaken van ExpressRoute Global Reach, maar dit is niet verplicht.

* **Verkeers stroom van on-premises naar Azure VMware-oplossing**

  :::image type="content" source="media/hub-spoke/on-prem-to-avs-traffic-flow.png" alt-text="Verkeers stroom van on-premises naar Azure VMware-oplossing":::


* **Azure VMware-oplossing voor hub VNET-verkeers stroom**

  :::image type="content" source="media/hub-spoke/avs-to-hub-vnet-traffic-flow.png" alt-text="Azure VMware-oplossing voor het hub-verkeer van virtuele netwerken":::


Meer informatie over Azure VMware Solution Network-en interconnectiviteit-concepten vindt u in de [product documentatie van de Azure VMware-oplossing](./concepts-networking.md).

### <a name="traffic-segmentation"></a>Segmentatie van verkeer

[Azure firewall](../firewall/index.yml) is het centrale gedeelte van de hub-en spoke-topologie, geïmplementeerd op het virtuele hub-netwerk. Gebruik Azure Firewall of een ander door Azure ondersteund virtueel netwerk apparaat voor het instellen van verkeers regels en Segmenteer de communicatie tussen de verschillende spokes, on-premises en Azure VMware Solution-werk belastingen.

Maak route tabellen om het verkeer naar Azure Firewall te sturen.  Voor de spoke-virtuele netwerken maakt u een route waarmee de standaard route wordt ingesteld op de interne interface van Azure Firewall, op deze manier wanneer een werk belasting in de Virtual Network de adres ruimte van de Azure VMware-oplossing moet bereiken, kan de firewall deze evalueren en de bijbehorende verkeers regel Toep assen om deze toe te staan of te weigeren.  

:::image type="content" source="media/hub-spoke/create-route-table-to-direct-traffic.png" alt-text="Route tabellen maken voor het omleiden van verkeer naar Azure Firewall":::


> [!IMPORTANT]
> Een route met het adres voorvoegsel 0.0.0.0/0 voor de **GatewaySubnet** -instelling wordt niet ondersteund.

Routes instellen voor specifieke netwerken op de bijbehorende route tabel. Bijvoorbeeld: routes voor het bereiken van Azure VMware-oplossings beheer en workloads IP-voor voegsels van on-premises en andersom, routeren alle verkeer van on-premises naar Azure VMware-oplossing privécloud via Azure Firewall.

:::image type="content" source="media/hub-spoke/specify-gateway-subnet-for-route-table.png" alt-text="Routes instellen voor specifieke netwerken op de bijbehorende route tabel":::

Een tweede niveau van Traffic segmentatie met behulp van de netwerk beveiligings groepen binnen de spokes en de hub om een meer gedetailleerd verkeers beleid te maken. 


### <a name="application-gateway"></a>Application Gateway

Azure-toepassing gateway v1 en v2 zijn getest met web apps die worden uitgevoerd op virtuele machines uit de Azure VMware-oplossing als back-end-groep. Application Gateway is momenteel de enige methode die wordt ondersteund om web-apps die worden uitgevoerd op virtuele machines van Azure VMware-oplossingen, beschikbaar te maken op internet. Ook kunnen de apps veilig worden blootgesteld aan interne gebruikers.

:::image type="content" source="media/hub-spoke/avs-second-level-traffic-segmentation.png" alt-text="Tweede niveau van verkeers segmentatie met behulp van de netwerk beveiligings groepen":::


### <a name="jumpbox-and-azure-bastion"></a>JumpBox en Azure Bastion

Toegang tot de Azure VMware-oplossings omgeving met JumpBox, een Windows 10-of Windows Server-VM die is geïmplementeerd in het subnet van de gedeelde service binnen het virtuele netwerk van de hub.

Implementeer [Microsoft Azure Bastion](../bastion/index.yml) -service in het virtuele netwerk van de hub als beveiligings best practice. Azure Bastion biedt naadloze RDP-en SSH-toegang tot Vm's die zijn geïmplementeerd op Azure zonder dat open bare IP-adressen op deze resources hoeven te worden ingericht. Nadat u de Azure Bastion-service hebt ingericht, kunt u vanuit de Azure Portal toegang krijgen tot de geselecteerde VM. Nadat de verbinding tot stand is gebracht, wordt er een nieuw tabblad geopend, waarin het JumpBox-bureau blad wordt weer gegeven, en op dat bureau blad hebt u toegang tot het beheer vlak van de privécloud-oplossing van Azure VMware.

> [!IMPORTANT]
> Geef geen openbaar IP-adres aan de JumpBox-VM of open de poort 3389/TCP voor het open bare Internet. 


:::image type="content" source="media/hub-spoke/azure-bastion-hub-vnet.png" alt-text="Virtueel Azure Bastion hub-netwerk":::


## <a name="azure-dns-resolution-considerations"></a>Overwegingen voor Azure DNS oplossingen

Voor Azure DNS oplossing zijn er twee opties beschikbaar:

-   Gebruik de Azure Active Directory (Azure AD) domein controllers die zijn geïmplementeerd op de hub (beschreven in [identiteits overwegingen](#identity-considerations)) als naam servers.

-   Een Azure DNS privé zone implementeren en configureren.

U kunt het beste beide combi neren om een betrouw bare naam omzetting te bieden voor de Azure VMware-oplossing, on-premises en Azure.

Als algemene ontwerp aanbeveling gebruikt u de bestaande Azure DNS-infra structuur (in dit geval Active Directory-geïntegreerde DNS) die is geïmplementeerd op ten minste twee virtuele Azure-machines die zijn geïmplementeerd in het VM-netwerk en die in de spoke-virtuele netwerken zijn geconfigureerd voor het gebruik van deze Azure DNS servers in de DNS-instellingen.

Azure Privé-DNS kan nog steeds worden gebruikt wanneer de Azure Privé-DNS-zone is gekoppeld aan de virtuele netwerken, en DNS-servers worden gebruikt als hybride resolver met voorwaardelijke door sturen naar een on-premises/Azure VMware-oplossing die gebruikmaakt van een Azure Privé-DNS-infra structuur van de klant.

Er zijn verschillende aandachtspunten voor het Azure DNS van particuliere zones:

* Automatische registratie moet zijn ingeschakeld om Azure DNS de levens cyclus van de DNS-records voor de Vm's die in spoke Virtual Networks zijn geïmplementeerd, automatisch te beheren.
* Het maximum aantal privé-DNS-zones waaraan een virtueel netwerk kan worden gekoppeld met de functie voor registratie is ingeschakeld, is slechts één.
* Het maximum aantal privé-DNS-zones waaraan een virtueel netwerk kan worden gekoppeld, is 1000 zonder dat de registratie is ingeschakeld.

On-premises en Azure VMware-oplossingen kunnen worden geconfigureerd met voorwaardelijke doorstuur servers voor het oplossen van Vm's in azure voor de Azure Privé-DNS-zone.

## <a name="identity-considerations"></a>Identiteits overwegingen

Voor identiteits doeleinden is de beste benadering om ten minste één AD-domein controller op de hub te implementeren, met behulp van het subnet van de gedeelde service, in het ideale niveau twee in een zone-gedistribueerde mode of een VM-beschikbaarheidsset. Zie [Azure Architecture Center](/azure/architecture/reference-architectures/identity/adds-extend-domain) voor het uitbreiden van uw on-PREMISes AD-domein naar Azure.

Implementeer daarnaast een andere domein controller op de Azure VMware-oplossing zijde om als identiteits-en DNS-bron binnen de vSphere omgeving te fungeren.

Voor vCenter en SSO stelt u een identiteits bron in het Azure Portal in, op identiteits-identiteits ** \> \> bronnen beheren**.

Als aanbevolen best practice kunt u het [AD-domein integreren met Azure Active Directory](/azure/architecture/reference-architectures/identity/azure-ad).

<!-- LINKS - external -->
[Azure Architecture Center]: /azure/architecture/

[Hub & Spoke topology]: /azure/architecture/reference-architectures/hybrid-networking/hub-spoke

[Azure networking documentation]: ../networking/index.yml

<!-- LINKS - internal -->
