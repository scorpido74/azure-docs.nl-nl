---
title: 'Concept: een implementatie van een Azure VMware-oplossing integreren in een hub-en spoke-architectuur'
description: Meer informatie over de aanbevelingen voor het integreren van een implementatie van een Azure VMware-oplossing in een bestaande of een nieuwe hub-en spoke-architectuur in Azure.
ms.topic: conceptual
ms.date: 09/09/2020
ms.openlocfilehash: a2007e159d23a02ca573fd833590651061c59973
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91271729"
---
# <a name="integrate-azure-vmware-solution-in-a-hub-and-spoke-architecture"></a>Azure VMware-oplossing integreren in een hub-en spoke-architectuur

In dit artikel bieden we aanbevelingen voor het integreren van een implementatie van een Azure VMware-oplossing in een bestaande of een nieuwe [hub-en spoke-architectuur](/azure/architecture/reference-architectures/hybrid-networking/shared-services) in Azure. 

In het hub-en-spoke-scenario wordt uitgegaan van een hybride cloud omgeving met workloads op:

* Systeem eigen Azure met IaaS-of PaaS-Services
* Azure VMware Solution 
* on-premises vSphere

## <a name="architecture"></a>Architectuur

De *hub* is een Azure-Virtual Network die fungeert als een centraal punt van connectiviteit met uw on-premises en Azure VMware-oplossing privécloud. De *spokes* zijn virtuele netwerken die zijn gekoppeld aan de hub om intervirtuele netwerk communicatie in te scha kelen.

Verkeer tussen het on-premises Data Center, de privécloud van Azure VMware-oplossing en de hub loopt via Azure ExpressRoute-verbindingen. Spoke Virtual Networks bevatten doorgaans IaaS werk belastingen, maar kunnen PaaS-services zoals [app service Environment](../app-service/environment/intro.md)hebben, die directe integratie met Virtual Network hebben, of andere PaaS-services waarvoor [Azure private link](../private-link/index.yml) is ingeschakeld.

In het diagram ziet u een voor beeld van een hub-en spoke-implementatie in azure die is verbonden met on-premises en Azure VMware-oplossing via ExpressRoute Global Reach.

:::image type="content" source="./media/hub-spoke/avs-hub-and-spoke-deployment.png" alt-text="Implementatie van Azure VMware Solution hub en spoke-integratie" border="false":::

De architectuur heeft de volgende hoofd onderdelen:

-   **On-premises site:** On-premises data centers van de klant die zijn verbonden met Azure via een ExpressRoute-verbinding.

-   **Privécloud van Azure VMware-oplossing:** Azure VMware-oplossing SDDC gevormd door een of meer vSphere-clusters, elk met Maxi maal 16 knoop punten.

-   **ExpressRoute-gateway:** Hiermee schakelt u de communicatie tussen de privécloud-oplossing van Azure VMware, gedeelde services op het virtuele hub-netwerk en workloads die worden uitgevoerd op spoke virtuele netwerken.

-   **ExpressRoute Global Reach:** Hiermee schakelt u de connectiviteit tussen on-premises en Azure VMware-oplossing privécloud.


  > [!NOTE]
  > **Overwegingen voor S2S-VPN:** Voor implementaties van Azure VMware-oplossingen productie wordt Azure S2S VPN niet ondersteund vanwege netwerk vereisten voor VMware-HCX. Het kan echter worden gebruikt voor een haalbaarheids implementatie.


-   **Virtueel netwerk hub:** Fungeert als het centrale punt van connectiviteit naar uw on-premises netwerk en Azure VMware-oplossing privécloud.

-   **Spoke-virtueel netwerk**

    -   **IaaS Spaak:** Een IaaS-spoke fungeert als host voor Azure IaaS-workloads, waaronder VM-beschikbaarheids sets en schaal sets voor virtuele machines en de bijbehorende netwerk onderdelen.

    -   **PaaS Spaak:** Een PaaS Spaak fungeert als host voor Azure PaaS Services met privé-adres Sering dankzij het [persoonlijke eind punt](../private-link/private-endpoint-overview.md) en de [persoonlijke koppeling](../private-link/private-link-overview.md).

-   **Azure firewall:** Fungeert als het centrale stuk om het verkeer tussen de spokes en de Azure VMware-oplossing te segmenteren.

-   **Application Gateway:** Beschrijft en beveiligt web-apps die worden uitgevoerd op Azure IaaS/PaaS of virtuele machines met Azure VMware-oplossingen. Het is geïntegreerd met andere services, zoals API Management.

## <a name="network-and-security-considerations"></a>Netwerk-en beveiligings overwegingen

Met ExpressRoute-verbindingen kunnen verkeer tussen on-premises, Azure VMware-oplossingen en de Azure-netwerk infrastructuur worden geplaatst. De Azure VMware-oplossing maakt gebruik van [ExpressRoute Global Reach](../expressroute/expressroute-global-reach.md) voor het implementeren van deze verbinding.

Omdat een ExpressRoute-gateway geen transitieve route ring biedt tussen de verbonden circuits, moet on-premises connectiviteit ook ExpressRoute Global Reach gebruiken om te communiceren tussen de on-premises vSphere-omgeving en de Azure VMware-oplossing. 

* **Verkeers stroom van on-premises naar Azure VMware-oplossing**

  :::image type="content" source="media/hub-spoke/on-prem-to-avs-traffic-flow.png" alt-text="Verkeers stroom van on-premises naar Azure VMware-oplossing" border="false":::


* **Azure VMware-oplossing voor hub VNET-verkeers stroom**

  :::image type="content" source="media/hub-spoke/avs-to-hub-vnet-traffic-flow.png" alt-text="Azure VMware-oplossing voor het hub-verkeer van virtuele netwerken" border="false":::


Meer informatie over de netwerk-en connectiviteits concepten van Azure VMware Solution vindt u in de [product documentatie van de Azure VMware-oplossing](./concepts-networking.md).

### <a name="traffic-segmentation"></a>Segmentatie van verkeer

[Azure firewall](../firewall/index.yml) is het centrale gedeelte van de hub-en spoke-topologie, geïmplementeerd op het virtuele hub-netwerk. Gebruik Azure Firewall of een ander door Azure ondersteund virtueel netwerk apparaat om verkeers regels vast te leggen en de communicatie tussen de verschillende spokes en de workloads van Azure VMware-oplossingen te segmenteren.

Maak route tabellen om het verkeer naar Azure Firewall te sturen.  Voor de spoke-virtuele netwerken maakt u een route waarmee de standaard route wordt ingesteld op de interne interface van Azure Firewall, op deze manier wanneer een werk belasting in de Virtual Network de adres ruimte van de Azure VMware-oplossing moet bereiken, kan de firewall deze evalueren en de bijbehorende verkeers regel Toep assen om deze toe te staan of te weigeren.  

:::image type="content" source="media/hub-spoke/create-route-table-to-direct-traffic.png" alt-text="Route tabellen maken voor het omleiden van verkeer naar Azure Firewall":::


> [!IMPORTANT]
> Een route met het adres voorvoegsel 0.0.0.0/0 voor de **GatewaySubnet** -instelling wordt niet ondersteund.

Routes instellen voor specifieke netwerken op de bijbehorende route tabel. Bijvoorbeeld: routes voor het bereiken van Azure VMware-oplossings beheer en werkbelastingen IP-voor voegsels van de spoke-workloads en vice versa.

:::image type="content" source="media/hub-spoke/specify-gateway-subnet-for-route-table.png" alt-text="Routes instellen voor specifieke netwerken op de bijbehorende route tabel":::

Een tweede niveau van Traffic segmentatie met behulp van de netwerk beveiligings groepen binnen de spokes en de hub om een meer gedetailleerd verkeers beleid te maken.

> [!NOTE]
> **Verkeer van on-premises naar Azure VMware-oplossing:** Verkeer tussen on-premises workloads, ofwel vSphere of anderen, wordt ingeschakeld door Global Reach, maar het verkeer gaat niet via Azure Firewall op de hub. In dit scenario moet u segmentatie mechanismen voor verkeer on-premises of in de Azure VMware-oplossing implementeren.

### <a name="application-gateway"></a>Application Gateway

Azure-toepassing gateway v1 en v2 zijn getest met web apps die worden uitgevoerd op virtuele machines uit de Azure VMware-oplossing als back-end-groep. Application Gateway is momenteel de enige methode die wordt ondersteund om web-apps die worden uitgevoerd op virtuele machines van Azure VMware-oplossingen, beschikbaar te maken op internet. Ook kunnen de apps veilig worden blootgesteld aan interne gebruikers.

Raadpleeg het artikel over de Azure VMware-oplossing op [Application Gateway](./protect-avs-web-apps-with-app-gateway.md) voor de details en vereisten.

:::image type="content" source="media/hub-spoke/avs-second-level-traffic-segmentation.png" alt-text="Tweede niveau van verkeers segmentatie met behulp van de netwerk beveiligings groepen" border="false":::


### <a name="jumpbox-and-azure-bastion"></a>JumpBox en Azure Bastion

Toegang tot de Azure VMware-oplossings omgeving met JumpBox, een Windows 10-of Windows Server-VM die is geïmplementeerd in het subnet van de gedeelde service binnen het virtuele netwerk van de hub.

Implementeer [Microsoft Azure Bastion](../bastion/index.yml) -service in het virtuele netwerk van de hub als beveiligings best practice. Azure Bastion biedt naadloze RDP-en SSH-toegang tot Vm's die zijn geïmplementeerd op Azure zonder dat open bare IP-adressen op deze resources hoeven te worden ingericht. Nadat u de Azure Bastion-service hebt ingericht, kunt u vanuit de Azure Portal toegang krijgen tot de geselecteerde VM. Nadat de verbinding tot stand is gebracht, wordt er een nieuw tabblad geopend met het JumpBox bureau blad waar u toegang hebt tot het beheer vlak van de privécloud-oplossing van Azure VMware.

> [!IMPORTANT]
> Geef geen openbaar IP-adres aan de JumpBox-VM of open de poort 3389/TCP voor het open bare Internet. 


:::image type="content" source="media/hub-spoke/azure-bastion-hub-vnet.png" alt-text="Virtueel Azure Bastion hub-netwerk" border="false":::


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

Voor identiteits doeleinden is de beste benadering om ten minste één AD-domein controller op de hub te implementeren met behulp van het subnet van de gedeelde service. In het ideale geval zijn er twee in de zone gedistribueerde mode of een VM-beschikbaarheidsset. Zie [Azure Architecture Center](/azure/architecture/reference-architectures/identity/adds-extend-domain) voor het uitbreiden van uw on-PREMISes AD-domein naar Azure.

Implementeer daarnaast een andere domein controller op de Azure VMware-oplossing zijde om als identiteits-en DNS-bron binnen de vSphere omgeving te fungeren.

Als aanbevolen best practice kunt u het [AD-domein integreren met Azure Active Directory](/azure/architecture/reference-architectures/identity/azure-ad).

<!-- LINKS - external -->
[Azure Architecture Center]: /azure/architecture/

[Hub & Spoke topology]: /azure/architecture/reference-architectures/hybrid-networking/hub-spoke

[Azure networking documentation]: ../networking/index.yml

<!-- LINKS - internal -->
