---
title: Secure Azure Computing Architecture
description: Deze referentie architectuur voor een DMZ-architectuur op bedrijfs niveau maakt gebruik van virtuele netwerk apparaten en andere hulpprogram ma's. Deze architectuur is ontworpen om te voldoen aan de functionele vereisten van het ministerie van defensie voor de veiligheid van Cloud Computing. Het kan ook worden gebruikt voor elke organisatie. Deze verwijzing bevat twee geautomatiseerde opties die gebruikmaken van Citrix-of F5-apparaten.
author: jahender
ms.author: jahender
ms.date: 4/9/2019
ms.topic: article
ms.service: security
ms.openlocfilehash: 3a27eac3d4609f1054b0ef6a9417fe2f1ca53ae4
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69656645"
---
# <a name="secure-azure-computing-architecture"></a>Secure Azure Computing Architecture

V.S. Klanten met een ministerie van defensie (DoD) die werk belastingen implementeren naar Azure, hebben gevraagd om veilige virtuele netwerken in te stellen en de beveiligings Programma's en-services te configureren die worden bepaald door de standaard instellingen en-prak tijken van DoD. 

De defensie Information System Agency (DISA) heeft het [document van de SCCA (Secure Cloud Computing Architecture) gepubliceerd (FRD)](https://dl.dod.cyber.mil/wp-content/uploads/cloud/pdf/SCCA_FRD_v2-9.pdf) in 2017. SCCA beschrijft de functionele doel stellingen voor het beveiligen van de beveiligings punten van het defensie Information System-netwerk (DISN) en commerciële Cloud provider. SCCA beschrijft ook hoe missie-eigen aren Cloud toepassingen beveiligen tegen de grens van de verbinding. Elke DoD-entiteit die verbinding maakt met de commerciële Cloud moet voldoen aan de richt lijnen die zijn uiteengezet in de SCCA-FRD.
 
De SCCA heeft vier onderdelen:
 
- Grens Cloud toegangs punt (BCAP)
- Virtual Data Center Security stack (VDSS)
- Virtual Data Center Managed Service (VDMS)
- Trusted Cloud Credential Manager (TCCM) 

Micro soft heeft een oplossing ontwikkeld die voldoet aan de SCCA-vereisten voor zowel IL4-als IL5-workloads die in Azure worden uitgevoerd. Deze specifieke Azure-oplossing wordt de beveiligde Azure Computing Architecture (SACA) genoemd. Klanten die SACA implementeren, voldoen aan de SCCA-FRD. Ze kunnen klanten van DoD in staat stellen werk belastingen naar Azure te verplaatsen nadat ze zijn verbonden.

SCCA-richt lijnen en-architecturen zijn specifiek voor klanten van DoD, maar de laatste revisies voor SACA die civiele klanten helpen te voldoen aan de richt lijnen voor de betrouw bare Internet verbinding (boter). Met de meest recente revisies kunnen commerciële klanten die een beveiligde DMZ willen implementeren, hun Azure-omgevingen beveiligen.


## <a name="secure-cloud-computing-architecture-components"></a>Architectuur onderdelen van Cloud Computing beveiligen

### <a name="bcap"></a>BCAP

Het doel van de BCAP is om de DISN te beschermen tegen aanvallen die afkomstig zijn uit de cloud omgeving. BCAP voert detectie en preventie van indringers uit. ook wordt het niet-geautoriseerde verkeer gefilterd. Dit onderdeel kan zich op dezelfde locatie bevinden als andere onderdelen van de SCCA. U wordt aangeraden dit onderdeel te implementeren met behulp van fysieke hardware. De beveiligings vereisten voor BCAP worden in de volgende tabel weer gegeven.

#### <a name="bcap-security-requirements"></a>Beveiligings vereisten voor BCAP

![BCAP-vereisten matrix](media/bcapreqs.png)


### <a name="vdss"></a>VDSS

Het doel van de VDSS is om toepassingen voor de missie van het DoD-eigendom te beveiligen die worden gehost in Azure. VDSS voert het meren deel van de beveiligings bewerkingen uit in de SCCA. Het verkeer wordt geïnspecteerd om de toepassingen te beveiligen die worden uitgevoerd in Azure. Dit onderdeel kan worden opgenomen in uw Azure-omgeving.

#### <a name="vdss-security-requirements"></a>Beveiligings vereisten voor VDSS

![VDSS-vereisten matrix](media/vdssreqs.png)

### <a name="vdms"></a>VDMS

Het doel van VDMS is het bieden van host Security en Shared Data Center Services. De functies van VDMS kunnen worden uitgevoerd in de hub van uw SCCA of de eigenaar van de missie kan delen van deze implementeren in hun eigen specifieke Azure-abonnement. Dit onderdeel kan worden opgenomen in uw Azure-omgeving.

#### <a name="vdms-security-requirements"></a>Beveiligings vereisten voor VDMS

![VDMS-vereisten matrix](media/vdmsreqs.png)


### <a name="tccm"></a>TCCM

TCCM is een zakelijke rol. Deze persoon is verantwoordelijk voor het beheer van de SCCA. Hun taken zijn: 

- Stel plannen en beleids regels in voor account toegang tot de cloud omgeving. 
- Zorg ervoor dat identiteits-en toegangs beheer goed werkt. 
- Onderhoud van het Cloud Credential management-plan. 

Deze persoon wordt benoemd door de geautoriseerde ambtenaar. De BCAP, VDSS en VDMS bieden de mogelijkheden die de TCCM nodig heeft om de taak uit te voeren.

#### <a name="tccm-security-requirements"></a>Beveiligings vereisten voor TCCM

![TCCM-vereisten matrix](media/tccmreqs.png) 

## <a name="saca-components-and-planning-considerations"></a>SACA-onderdelen en plannings overwegingen 

De SACA-referentie architectuur is ontworpen om de VDSS-en VDMS-onderdelen in azure te implementeren en de TCCM in te scha kelen. Deze architectuur is modulair. Alle stukjes VDSS en VDMS kunnen in een gecentraliseerde hub wonen. Aan sommige van de besturings elementen kan worden voldaan in de ruimte van de missie-eigenaar of zelfs on-premises. Micro soft raadt u aan om de VDSS-en VDMS-onderdelen te verplaatsen naar een centraal virtueel netwerk waarmee alle eigenaar van de missie verbinding kunnen maken. In het volgende diagram ziet u deze architectuur: 


![Diagram van SACA-referentie architectuur](media/sacav2generic.png)

Houd bij het plannen van uw SCCA nalevings-strategie en de technische architectuur rekening met de volgende onderwerpen, omdat deze van invloed zijn op elke klant. De volgende problemen zijn beschikbaar voor klanten van DoD en hebben vaak de planning en uitvoering trager. 

#### <a name="which-bcap-will-your-organization-use"></a>Welke BCAP zal uw organisatie gebruiken?
   - DISA BCAP:
        - DISA heeft twee operationele BCAPs op de vijf hoek en op Camp Roberts, CA. Een derde is gepland om binnenkort online te komen. 
        - DISA van BCAPs hebben allemaal Azure ExpressRoute-circuits naar Azure, dat door klanten van DoD kan worden gebruikt voor connectiviteit. 
        - DISA heeft een micro soft-peering-sessie op ondernemings niveau voor klanten die zich willen abonneren op micro soft-hulpprogram ma's voor software as a Service (SaaS), zoals Office 365. Met behulp van de DISA-BCAP kunt u connectiviteit en peering inschakelen voor uw SACA-exemplaar. 
    - Uw eigen BCAP bouwen:
        - Voor deze optie moet u ruimte vrijmaken in een gegevens centrum met co-locaties en een ExpressRoute-circuit instellen op Azure. 
        - Voor deze optie is aanvullende goed keuring vereist. 
        - Vanwege de extra goed keuring en een fysieke build is deze optie de meeste tijd. 
    - U wordt aangeraden de DISA BCAP te gebruiken. Deze optie is eenvoudig beschikbaar, heeft ingebouwde redundantie en heeft klanten die vandaag in productie zijn.
- DoD-Routeer bare IP-ruimte:
    - U moet de IP-adres ruimte met DoD-Routeer aan uw rand gebruiken. De optie voor het gebruik van NAT om deze Spaces te verbinden met particuliere IP-ruimte in Azure is beschikbaar.
    - Neem contact op met het DoD Network Information Center (NIC) om IP-ruimte te verkrijgen. U hebt deze nodig als onderdeel van de inzending van uw systeem/netwerk goedkeurings proces (module) met DISA. 
    - Als u NAT wilt gebruiken om persoonlijke adres ruimte te verbinden in azure, moet u Mini maal een/24-subnet van de adres ruimte die is toegewezen vanuit de NIC voor elke regio waar u SACA wilt implementeren.
- Redundantie:
    - Implementeer een SACA-exemplaar op ten minste twee regio's. In de DoD-Cloud implementeert u deze in zowel beschik bare DoD-regio's.
    - Maak verbinding met ten minste twee BCAPs via afzonderlijke ExpressRoute-circuits. Beide ExpressRoute-verbindingen kunnen vervolgens worden gekoppeld aan de SACA-instantie van elke regio. 
- Onderdeel-specifieke vereisten voor DoD:
    - Heeft uw organisatie specifieke vereisten buiten de SCCA-vereisten? Sommige organisaties hebben specifieke vereisten voor IP-adressen.
- SACA is een modulaire architectuur:
    - Gebruik alleen de onderdelen die u nodig hebt voor uw omgeving. 
        - Implementeer virtuele netwerk apparaten in één laag of met meerdere lagen.
        - Gebruik geïntegreerde IP'S of uw eigen IP'S.
- Het niveau van de DoD van uw toepassingen en gegevens:
    - Als er sprake is van toepassingen die worden uitgevoerd in micro soft IL5-regio's, bouwt u uw SACA-exemplaar op in IL5. Het exemplaar kan worden gebruikt voor IL4-toepassingen en IL5. Een IL4 SACA-exemplaar vóór een IL5-toepassing ontvangt waarschijnlijk geen accreditatie.

#### <a name="which-network-virtual-appliance-vendor-will-you-use-for-vdss"></a>Welke virtuele netwerk apparaat-leverancier gebruikt u voor VDSS?
Zoals eerder vermeld, kunt u deze SACA-referentie samen stellen met behulp van verschillende apparaten en Azure-Services. Micro soft heeft geautomatiseerde oplossings sjablonen voor het implementeren van de SACA-architectuur met zowel F5 als Citrix. Deze oplossingen zijn opgenomen in de volgende sectie.

#### <a name="which-azure-services-will-you-use"></a>Welke Azure-Services wilt u gebruiken?
- Er zijn Azure-Services die voldoen aan de vereisten voor log Analytics, beveiliging op basis van een host en ID'S. Het is mogelijk dat sommige services niet algemeen beschikbaar zijn in micro soft IL5 regions. In dit geval moet u mogelijk hulp middelen van derden gebruiken als deze Azure-Services niet voldoen aan uw vereisten. Bekijk de hulpprogram ma's waarmee u vertrouwd bent en de haal baarheid van het gebruik van Azure systeem eigen hulp middelen.
- We raden u aan zo veel mogelijk Azure systeem eigen hulpprogram ma's te gebruiken. Ze zijn gebouwd op basis van Cloud beveiliging en kunnen naadloos worden geïntegreerd met de rest van het Azure-platform. Gebruik de systeem eigen hulp middelen van Azure in de volgende lijst om te voldoen aan verschillende vereisten van SCCA:

    - [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview )
    - [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) 
    - [Azure Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) 
    - [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) 
    - [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)
    - [Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/overview)
    - [Azure Firewall](https://docs.microsoft.com/azure/firewall/overview) 
    - [Azure front deur](https://docs.microsoft.com/azure/frontdoor/front-door-overview)
    - [Azure-beveiligings groepen](https://docs.microsoft.com/azure/virtual-network/security-overview)
    - [Azure DDoS Protection](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview)
    - [Azure Sentinel](https://docs.microsoft.com/azure/sentinel/overview)
- Grootte aanpassen
    - Een schaal oefening moet worden voltooid. Bekijk het aantal gelijktijdige verbindingen dat u mogelijk hebt via het SACA-exemplaar en de vereisten voor netwerk doorvoer. 
    - Deze stap is essentieel. Het helpt om de grootte van de virtuele machines te wijzigen en te bepalen welke licenties zijn vereist voor de verschillende leveranciers die u in uw SACA-exemplaar gebruikt. 
    - Er kan geen goede kosten analyse worden uitgevoerd zonder deze aanpassings oefening. Met de juiste grootte kunt u ook de beste prestaties. 


## <a name="most-common-deployment-scenario"></a>Meest voorkomende implementatie scenario

 Verschillende micro soft-klanten hebben de volledige implementatie door lopen of ten minste de plannings stadia van hun SACA-omgevingen. Hun ervaringen hebben inzicht in het meest voorkomende implementatie scenario. In het volgende diagram ziet u de meest voorkomende architectuur: 


![Diagram van SACA-referentie architectuur](media/sacav2commonscenario.png) 


Zoals u kunt zien in het diagram, abonneren klanten meestal op twee van de DISA-BCAPs. Een van deze levens bevindt zich op de West kust en in het andere leven op de Oost kust. Een persoonlijke ExpressRoute-peer is op elke DISA BCAP-locatie ingeschakeld voor Azure. Deze ExpressRoute-peers worden vervolgens gekoppeld aan de gateway van het virtuele netwerk in de Azure-regio's DoD-Oost en DoD-centraal. Een SACA-exemplaar wordt geïmplementeerd in de Azure-regio's DoD-Oost en DoD-midden. Alle binnenkomend en uitgaand verkeer loopt door naar en van de ExpressRoute-verbinding met de DISA-BCAP.

Doel-eigendoms toepassingen kiezen vervolgens de Azure-regio's waarin ze hun toepassingen willen implementeren. Ze maken gebruik van peering van het virtuele netwerk om het virtuele netwerk van de toepassing te verbinden met het virtuele SACA-netwerk. Vervolgens forceren ze het tunnelen van al hun verkeer via het VDSS-exemplaar.

We raden aan deze architectuur te voldoen omdat deze voldoet aan de vereisten voor SCCA. Het is Maxi maal beschikbaar en eenvoudig schaalbaar. Het vereenvoudigt de implementatie en het beheer.

## <a name="automated-saca-deployment-options"></a>Opties voor automatische SACA-implementatie

 Zoals eerder vermeld, heeft micro soft een geautomatiseerd SACA-infrastructuur sjabloon gekoppeld aan twee leveranciers. Beide sjablonen implementeren de volgende Azure-onderdelen: 

- SACA virtueel netwerk
    - Subnet van beheer
        - In dit subnet worden beheer-Vm's en-Services geïmplementeerd, ook wel een Jump-vakje genoemd.
        - VDMS-subnet
            - In dit subnet worden virtuele machines en Services geïmplementeerd die worden gebruikt voor VDMS.
        - Niet-vertrouwde en vertrouwde subnetten
            - In deze subnetten worden virtuele apparaten geïmplementeerd.
        - Gatewaysubnet
            - Dit subnet is de locatie van de ExpressRoute-gateway.
- Virtuele machines voor het beheer van Jump box
    - Ze worden gebruikt voor out-of-band-beheer van de omgeving.
- Virtuele netwerkapparaten
    - U gebruikt Citrix of F5 op basis van de sjabloon die u implementeert.
- Openbare IP-adressen
    - Ze worden gebruikt voor de front-end totdat ExpressRoute online wordt gebracht. Deze IP-adressen worden omgezet naar de back-end Azure privé-adres ruimte.
- Routetabellen 
    - Deze route tabellen worden toegepast tijdens de automatisering en forceren alle verkeer via het virtuele apparaat.
- Azure load balancers-standaard-SKU
    - Ze worden gebruikt voor het verdelen van verkeer over de apparaten.
- Netwerkbeveiligingsgroepen
    - Ze worden gebruikt om te bepalen welke soorten verkeer kan passeren op bepaalde eind punten.


### <a name="citrix-saca-deployment"></a>Citrix SACA-implementatie

Een Citrix-implementatie sjabloon implementeert twee lagen van Maxi maal beschik bare Citrix ADC-apparaten. Deze architectuur voldoet aan de vereisten van VDSS. 

![Citrix SACA-diagram](media/citrixsaca.png)


Zie [deze github-koppeling](https://github.com/citrix/netscaler-azure-templates/tree/master/templates/saca)voor de Citrix-documentatie en het implementatie script.


 ### <a name="f5-saca-deployment"></a>F5 SACA-implementatie

Twee afzonderlijke F5-implementatie sjablonen beslaan twee verschillende architecturen. De eerste sjabloon bevat slechts één laag van F5-apparaten in een actief-actief Maxi maal beschik bare configuratie. Deze architectuur voldoet aan de vereisten voor VDSS. Met de tweede sjabloon wordt een tweede laag van actief-actief Maxi maal beschik bare F5s toegevoegd. Met deze tweede laag kunnen klanten hun eigen IP-adressen toevoegen, gescheiden van F5, tussen de F5-lagen. Niet alle onderdelen van DoD hebben specifieke IP'S die voor gebruik zijn voorgeschreven. Als dat het geval is, werkt de enkele laag van F5 toestellen het meest, omdat deze architectuur IP'S bevat op de F5-apparaten.

![F5 SACA-diagram](media/f5saca.png)

Zie [deze github-koppeling](https://github.com/f5devcentral/f5-azure-saca)voor de F5-documentatie en het implementatie script.












