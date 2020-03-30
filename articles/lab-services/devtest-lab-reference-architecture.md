---
title: Enterprise-referentiearchitectuur voor Azure DevTest Labs
description: In dit artikel vindt u richtlijnen voor referentiearchitectuur voor Azure DevTest Labs in een onderneming.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2019
ms.author: spelluru
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 77e6ab588f74c8b810f211e069c1c24043155111
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77132839"
---
# <a name="azure-devtest-labs-reference-architecture-for-enterprises"></a>Azure DevTest Labs-referentiearchitectuur voor ondernemingen
In dit artikel vindt u referentiearchitectuur waarmee u een oplossing implementeren op basis van Azure DevTest Labs in een onderneming. Het bevat de volgende gegevens:
- On-premises connectiviteit via Azure ExpressRoute
- Een externe desktopgateway om zich op afstand aan te melden bij virtuele machines
- Connectiviteit met een artefact-opslagplaats voor privéartefacten
- Andere PaaS-diensten die worden gebruikt in laboratoria

![Referentiearchitectuurdiagram](./media/devtest-lab-reference-architecture/reference-architecture.png)

## <a name="architecture"></a>Architectuur
Dit zijn de belangrijkste elementen van de referentiearchitectuur:

- **Azure Active Directory (Azure AD)**: DevTest Labs gebruikt de [Azure AD-service voor identiteitsbeheer](../active-directory/fundamentals/active-directory-whatis.md). Houd rekening met deze twee belangrijke aspecten wanneer u gebruikers toegang geeft tot een omgeving op basis van DevTest Labs:
    - **Resourcebeheer**: het biedt toegang tot de Azure-portal om resources te beheren (virtuele machines maken; omgevingen maken; starten, stoppen, opnieuw starten, verwijderen en toepassen van artefacten, enzovoort). Resourcebeheer gebeurt in Azure met behulp van op rollen gebaseerd toegangscontrolebeheer (RBAC). U wijst rollen toe aan gebruikers en stelt machtigingen op resource- en access-niveau in.
    - **Virtuele machines (netwerkniveau):** In de standaardconfiguratie gebruiken virtuele machines een lokaal beheerdersaccount. Als er een domein beschikbaar is[(Azure AD Domain Services,](../active-directory-domain-services/overview.md)een on-premises domein of een clouddomein), kunnen machines worden samengevoegd met het domein. Gebruikers kunnen vervolgens hun domeingebaseerde identiteiten gebruiken om verbinding te maken met de VM's.
- **On-premises connectiviteit**: In ons architectuurdiagram wordt [ExpressRoute](../expressroute/expressroute-introduction.md) gebruikt. Maar u ook gebruik maken van een [site-to-site VPN.](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md) Hoewel ExpressRoute niet vereist is voor DevTest Labs, wordt het vaak gebruikt in ondernemingen. ExpressRoute is alleen vereist als u toegang nodig hebt tot bedrijfsbronnen. Veelvoorkomende scenario's zijn:
    - U hebt on-premises gegevens die niet naar de cloud kunnen worden verplaatst.
    - U wilt liever de virtuele machines van het lab aansluiten bij het on-premises domein.
    - U wilt al het netwerkverkeer in en uit de cloudomgeving afdwingen via een on-premises firewall voor beveiliging/compliance.
- **Netwerkbeveiligingsgroepen**: Een veelgebruikte manier om verkeer te beperken tot de cloudomgeving (of binnen de cloudomgeving) op basis van ip-adressen van bron en bestemming, is het gebruik van een [netwerkbeveiligingsgroep.](../virtual-network/security-overview.md) U wilt bijvoorbeeld alleen verkeer dat afkomstig is van het bedrijfsnetwerk toestaan in de netwerken van het lab.
- **Extern bureaublad-gateway**: Ondernemingen blokkeren doorgaans uitgaande externe bureaubladverbindingen op de bedrijfsfirewall. Er zijn verschillende opties om connectiviteit met de cloudomgeving in DevTest Labs mogelijk te maken, waaronder:
  - Gebruik een [externe desktopgateway](/windows-server/remote/remote-desktop-services/desktop-hosting-logical-architecture)en sta het statische IP-adres van de gatewayloadbalancer toe.
  - [Direct al het inkomende RDP-verkeer](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) via de ExpressRoute/site-to-site VPN-verbinding. Deze functionaliteit is een veelvoorkomende overweging wanneer ondernemingen een DevTest Labs-implementatie plannen.
- **Netwerkservices (virtuele netwerken, subnetten):** De [Azure-netwerktopologie](../networking/networking-overview.md) is een ander belangrijk element in de DevTest Labs-architectuur. Het bepaalt of bronnen uit het lab kunnen communiceren en toegang hebben tot on-premises en het internet. Ons architectuurdiagram bevat de meest voorkomende manieren waarop klanten DevTest Labs gebruiken: Alle labs maken verbinding via [virtuele netwerkpeering](../virtual-network/virtual-network-peering-overview.md) met behulp van een [hub-spoke-model](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) met de ExpressRoute/site-to-site VPN-verbinding met on-premises. Maar DevTest Labs maakt rechtstreeks gebruik van Azure Virtual Network, dus er zijn geen beperkingen voor de manier waarop u de netwerkinfrastructuur instelt.
- **DevTest Labs**: DevTest Labs is een belangrijk onderdeel van de algehele architectuur. Zie [Over DevTest Labs](devtest-lab-overview.md)voor meer informatie over de service.
- **Virtuele machines en andere resources (SaaS, PaaS, IaaS):** Virtuele machines zijn een belangrijke workload die DevTest Labs ondersteunt, samen met andere Azure-bronnen. Met DevTest Labs kan een onderneming eenvoudig en snel toegang bieden tot Azure-bronnen (waaronder VM's en andere Azure-bronnen). Meer informatie over toegang tot Azure voor [ontwikkelaars](devtest-lab-developer-lab.md) en [testers](devtest-lab-test-env.md).

## <a name="scalability-considerations"></a>Schaalbaarheidsoverwegingen
Hoewel DevTest Labs geen ingebouwde quota of limieten heeft, hebben andere Azure-resources die worden gebruikt in de typische bewerking van een lab [quota op abonnementsniveau.](../azure-resource-manager/management/azure-subscription-service-limits.md) In een typische bedrijfsimplementatie hebt u dus meerdere Azure-abonnementen nodig voor een grote implementatie van DevTest Labs. De quota die ondernemingen het meest bereiken zijn:

- **Resourcegroepen:** In de standaardconfiguratie maakt DevTest Labs een resourcegroep voor elke nieuwe virtuele machine of maakt de gebruiker een omgeving met behulp van de service. Abonnementen kunnen [maximaal 980 resourcegroepen](../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits)bevatten. Dus, dat is de limiet van virtuele machines en omgevingen in een abonnement. Er zijn twee andere configuraties die u moet overwegen:
    - **[Alle virtuele machines gaan naar dezelfde resourcegroep:](resource-group-control.md)** hoewel u met deze instelling voldoet aan de limiet voor resourcegroepen, is dit van invloed op de limiet resource-type-per-resourcegroep.
    - **Gedeelde openbare IP's gebruiken:** alle VM's van dezelfde grootte en regio gaan naar dezelfde resourcegroep. Deze configuratie is een "middenweg" tussen resourcegroepquota en resourcetype-per-resourcegroepquota, als de virtuele machines openbare IP-adressen mogen hebben.
- **Resources per resourcegroep per resourcetype:** de standaardlimiet voor [resources per resourcegroep per resourcetype is 800](../azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits).  Wanneer u de *alle VM's gebruikt, gaat u naar dezelfde configuratie van de brongroep,* raken gebruikers deze abonnementslimiet veel eerder, vooral als de VM's veel extra schijven hebben.
- **Opslagaccounts**: Een lab in DevTest Labs wordt geleverd met een opslagaccount. Het Azure-quotum voor [het aantal opslagaccounts per regio per abonnement is 250](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits). Het maximum aantal DevTest Labs in dezelfde regio is ook 250.
- **Roltoewijzingen**: Een roltoewijzing is hoe u een gebruiker of hoofdsom toegang geeft tot een resource (eigenaar, resource, machtigingsniveau). In Azure geldt een [limiet van 2.000 roltoewijzingen per abonnement.](../azure-resource-manager/management/azure-subscription-service-limits.md#role-based-access-control-limits) Standaard maakt de devTest Labs-service een resourcegroep voor elke virtuele machine. De eigenaar krijgt toestemming van de *eigenaar* voor de VM DevTest Labs en *de lezer* toestemming voor de resourcegroep. Op deze manier gebruikt elke nieuwe VM die u maakt twee roltoewijzingen naast de toewijzingen die worden gebruikt wanneer u gebruikers toestemming geeft voor het lab.
- **API-leest/schrijft**: Er zijn verschillende manieren om Azure en DevTest Labs te automatiseren, waaronder REST API's, PowerShell, Azure CLI en Azure SDK. Door middel van automatisering u een andere limiet voor API-aanvragen bereiken: elk abonnement biedt maximaal [12.000 leesverzoeken en 1.200 schrijfverzoeken per uur](../azure-resource-manager/management/request-limits-and-throttling.md)mogelijk. Wees je bewust van deze limiet wanneer u DevTest Labs automatiseert.

## <a name="manageability-considerations"></a>Beheerbaarheidsoverwegingen
DevTest Labs heeft een geweldige administratieve gebruikersinterface voor het werken met een enkel lab. Maar in een onderneming hebt u waarschijnlijk meerdere Azure-abonnementen en veel labs. Om consistent wijzigingen aan te brengen in al uw labs, is scripting/automatisering vereist. Hier volgen enkele voorbeelden en best management practices voor een DevTest Labs-implementatie:

- **Wijzigingen in de labinstellingen:** Een veelvoorkomend scenario is het bijwerken van een specifieke labinstelling in alle labs in de implementatie. Er is bijvoorbeeld een nieuwe VM-instantiegrootte beschikbaar en alle labs moeten worden bijgewerkt om dit mogelijk te maken. Het is het beste om deze wijzigingen te automatiseren met PowerShell-scripts, de CLI- of REST-API's.  
- **Artefact repository persoonlijke toegangstoken:** Meestal vervallen persoonlijke toegangstokens voor een Git-repository in 90 dagen, een jaar of twee jaar. Om de continuïteit te garanderen, is het belangrijk om het persoonlijke toegangstoken uit te breiden of een nieuwe te maken. Gebruik vervolgens automatisering om het nieuwe persoonlijke toegangstoken toe te passen op alle laboratoria.
- **Wijzigingen beperken tot een labinstelling:** Vaak moet een bepaalde instelling worden beperkt (zoals het toestaan van het gebruik van marktplaatsafbeeldingen). U Azure Policy gebruiken om wijzigingen in een resourcetype te voorkomen. U ook een aangepaste rol maken en gebruikers die rol toekennen in plaats van de rol van de *eigenaar* voor het lab. U dit doen voor de meeste instellingen in het lab (interne ondersteuning, lab aankondiging, toegestane VM-formaten, enzovoort).
- **Vm's verplichten om een naamgevingsconventie te volgen:** Managers willen vaak eenvoudig VM's identificeren die deel uitmaken van een cloudgebaseerde ontwikkel- en testomgeving. U dit doen met [Azure Policy](https://github.com/Azure/azure-policy/tree/master/samples/TextPatterns/allow-multiple-name-patterns).

Het is belangrijk op te merken dat DevTest Labs onderliggende Azure-resources gebruikt die op dezelfde manier worden beheerd: netwerken, schijven, compute, enzovoort. Azure Policy is bijvoorbeeld van toepassing op virtuele machines die in een lab zijn gemaakt. Azure Security Center kan rapporteren over VM-naleving. En de Azure Backup-service kan regelmatig back-ups bieden voor de VM's in het lab.

## <a name="security-considerations"></a>Beveiligingsoverwegingen
Azure DevTest Labs maakt gebruik van bestaande bronnen in Azure (compute, networking, enzovoort). Dus het profiteert automatisch van de beveiligingsfuncties die zijn ingebouwd in het platform. Als u bijvoorbeeld wilt dat binnenkomende externe bureaubladverbindingen alleen afkomstig zijn van het bedrijfsnetwerk, voegt u eenvoudig een netwerkbeveiligingsgroep toe aan het virtuele netwerk op de extern bureaublad-gateway. De enige extra beveiligingsoverweging is het niveau van machtigingen dat u verleent aan teamleden die de labs dagelijks gebruiken. De meest voorkomende machtigingen zijn [ *eigenaar* en *gebruiker.*](devtest-lab-add-devtest-user.md) Zie Eigenaren en gebruikers [toevoegen in Azure DevTest Labs voor](devtest-lab-add-devtest-user.md)meer informatie over deze rollen.

## <a name="next-steps"></a>Volgende stappen
Zie het volgende artikel in deze serie: [Schaal uw Azure DevTest Labs-infrastructuur](devtest-lab-guidance-scale.md)op.
