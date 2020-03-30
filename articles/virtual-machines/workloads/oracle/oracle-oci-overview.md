---
title: Microsoft Azure integreren met Oracle Cloud Infrastructure | Microsoft Documenten
description: Meer informatie over oplossingen waarmee Oracle-apps die op Microsoft Azure worden uitgevoerd, worden geïntegreerd met databases in Oracle Cloud Infrastructure (OCI).
services: virtual-machines-linux
documentationcenter: ''
author: romitgirdhar
manager: gwallace
tags: ''
ms.assetid: ''
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/04/2019
ms.author: rogirdh
ms.custom: ''
ms.openlocfilehash: e1249913300be532cc6514f1478bbc6f4183c001
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78300550"
---
# <a name="oracle-application-solutions-integrating-microsoft-azure-and-oracle-cloud-infrastructure-preview"></a>Oracle-toepassingsoplossingen die Microsoft Azure en Oracle Cloud Infrastructure integreren (preview)

Microsoft en Oracle werken samen om cross-cloudconnectiviteit met een lage latentie met hoge doorvoer te bieden, zodat u profiteren van het beste van beide clouds. 

Met deze cross-cloud-connectiviteit u een toepassing met meerdere lagen partitioneren om uw databaselaag uit te voeren op Oracle Cloud Infrastructure (OCI) en de toepassing en andere lagen op Microsoft Azure. De ervaring is vergelijkbaar met het uitvoeren van de volledige oplossingsstack in één cloud. 

> [!IMPORTANT]
> Deze cross-cloudmogelijkheid bevindt zich momenteel in preview en [er gelden beperkingen.](#region-availability) Om een low latency-verbinding tussen Azure en OCI tot stand te brengen, moet uw Azure-abonnement eerst voor deze mogelijkheid zijn ingeschakeld. U moet zich inschrijven voor de preview door dit korte [enquêteformulier in te vullen.](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyzVVsi364tClw522rL9tkpUMVFGVVFWRlhMNUlRQTVWSTEzT0dXMlRUTyQlQCN0PWcu) U ontvangt een e-mailbevestiging zodra uw abonnement is geregistreerd. U de mogelijkheid pas gebruiken als u een bevestigingsmail hebt ontvangen. U ook contact opnemen met uw Microsoft-vertegenwoordiger om voor deze preview te worden ingeschakeld. Toegang tot de preview-mogelijkheid is afhankelijk van beschikbaarheid en beperkt door Microsoft naar eigen inzicht. De voltooiing van de enquête garandeert geen toegang. Deze preview wordt zonder serviceniveau geleverd en mag niet worden gebruikt voor productieworkloads. Bepaalde functies worden mogelijk niet ondersteund, zijn mogelijk beperkt of zijn mogelijk niet beschikbaar in alle Azure-locaties. Zie [de aanvullende gebruiksvoorwaarden](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor Microsoft Azure Previews voor meer informatie. Sommige aspecten van deze functionaliteit kunnen wijzigen voordat deze functionaliteit algemeen beschikbaar wordt.

Als u oracle-oplossingen volledig wilt implementeren op azure-infrastructuur, raadpleegt u [Oracle VM-afbeeldingen en de implementatie ervan op Microsoft Azure.](oracle-vm-solutions.md)

## <a name="scenario-overview"></a>Overzicht van scenario's

Cross-cloudconnectiviteit biedt u een oplossing om de toonaangevende toepassingen van Oracle en uw eigen aangepaste toepassingen op virtuele Azure-machines uit te voeren, terwijl u geniet van de voordelen van gehoste databaseservices in OCI. 

Toepassingen die u uitvoeren in een cross-cloudconfiguratie zijn:

* E-Business Suite
* JD Edwards EnterpriseOne
* Peoplesoft
* Oracle Retail-toepassingen
* Oracle Hyperion Financieel Beheer

Het volgende diagram is een overzicht op hoog niveau van de verbonden oplossing. Voor de eenvoud toont het diagram alleen een toepassingslaag en een gegevenslaag. Afhankelijk van de toepassingsarchitectuur kan uw oplossing extra lagen bevatten, zoals een weblaag in Azure. Zie de volgende secties voor meer informatie.

![Overzicht van Azure OCI-oplossing](media/oracle-oci-overview/crosscloud.png)

## <a name="region-availability"></a>Beschikbaarheid van regio's 

Cross-cloud connectiviteit is beperkt tot de volgende regio's:
* Azure East US (eastus) & OCI Ashburn (US East)
* Azure UK South (uksouth) & OCI London (UK South)
* Azure Canada Central (canadacentral) & OCI Toronto (Canada Zuidoost)
* Azure West Europe (West-Europa) & OCI Amsterdam (Nederland Noordwest)

## <a name="networking"></a>Netwerken

Enterprise-klanten kiezen er vaak voor om workloads over meerdere clouds te diversifiëren en te implementeren om verschillende zakelijke en operationele redenen. Om te diversifiëren verbinden klanten cloudnetwerken via internet, IPSec VPN of via de directe connectiviteitsoplossing van de cloudprovider via uw on-premises netwerk. Het onderling verbinden van cloudnetwerken kan aanzienlijke investeringen in tijd, geld, ontwerp, inkoop, installatie, testen en bewerkingen vereisen. 

Om deze uitdagingen van klanten het hoofd te bieden, hebben Oracle en Microsoft een geïntegreerde multi-cloudervaring ingeschakeld. Cross-cloud netwerken wordt tot stand gebracht door een [ExpressRoute-circuit](../../../expressroute/expressroute-introduction.md) in Microsoft Azure te verbinden met een [FastConnect-circuit](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/fastconnectoverview.htm) in OCI. Deze connectiviteit is mogelijk wanneer een Azure ExpressRoute-peeringlocatie zich in de nabijheid van of op dezelfde peeringlocatie bevindt als de OCI FastConnect. Deze setup zorgt voor veilige, snelle connectiviteit tussen de twee clouds zonder dat er een tussenliggende serviceprovider nodig is.

Met ExpressRoute en FastConnect kunnen klanten een virtueel netwerk in Azure peeren met een virtueel cloudnetwerk in OCI, mits de privé-IP-adresruimte elkaar niet overlapt. Met peering van de twee netwerken kan een bron in het virtuele netwerk communiceren met een bron in het virtuele netwerk van OCI alsof ze zich beide in hetzelfde netwerk bevinden.

## <a name="network-security"></a>Netwerkbeveiliging

Netwerkbeveiliging is een cruciaal onderdeel van elke bedrijfstoepassing en staat centraal in deze multi-cloudoplossing. Verkeer dat via ExpressRoute en FastConnect rijdt, gaat over een privénetwerk. Deze configuratie zorgt voor veilige communicatie tussen een virtueel Azure-netwerk en een virtueel cloudnetwerk van Oracle. U hoeft geen openbaar IP-adres op te geven aan virtuele machines in Azure. Op dezelfde manier hebt u geen internetgateway in OCI nodig. Alle communicatie gebeurt via het privé IP-adres van de machines.

Bovendien u [beveiligingslijsten](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/securitylists.htm) instellen op uw virtuele netwerk- en beveiligingsregels van OCI (gekoppeld aan [Azure-netwerkbeveiligingsgroepen).](../../../virtual-network/security-overview.md) Gebruik deze regels om het verkeer dat tussen machines in de virtuele netwerken stroomt te regelen. Netwerkbeveiligingsregels kunnen worden toegevoegd op machineniveau, op subnetniveau en op virtueel netwerkniveau.
 
## <a name="identity"></a>Identiteit

Identiteit is een van de belangrijkste pijlers van de samenwerking tussen Microsoft en Oracle. Er is belangrijk werk verricht om [Oracle Identity Cloud Service](https://docs.oracle.com/en/cloud/paas/identity-cloud/index.html) (IDCS) te integreren met Azure Active [Directory](../../../active-directory/index.yml) (Azure AD). Azure AD is de cloudgebaseerde service voor identiteits- en toegangsbeheer van Microsoft. Het helpt uw gebruikers zich aan te melden en toegang te krijgen tot verschillende bronnen. Met Azure AD u ook uw gebruikers en hun machtigingen beheren.

Op dit moment u met deze integratie beheren op één centrale locatie, namelijk Azure Active Directory. Azure AD synchroniseert alle wijzigingen in de directory met de bijbehorende Oracle-map en wordt gebruikt voor single sign-on voor cross-cloud Oracle-oplossingen.

## <a name="next-steps"></a>Volgende stappen

Ga aan de slag met een [cross-cloudnetwerk](configure-azure-oci-networking.md) tussen Azure en OCI. 

Zie de [Oracle Cloud-documentatie](https://docs.cloud.oracle.com/iaas/Content/home.htm) voor meer informatie en whitepapers over OCI.
