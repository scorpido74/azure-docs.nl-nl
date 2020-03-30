---
title: Beveiligingsfuncties beheren en bewaken - Microsoft Azure | Microsoft Documenten
description: In dit artikel vindt u een overzicht van de beveiligingsfuncties en services die Azure biedt om te helpen bij het beheer en de bewaking van Azure-cloudservices en virtuele machines.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 5cf2827b-6cd3-434d-9100-d7411f7ed424
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2019
ms.author: terrylan
ms.openlocfilehash: 7ad7a29a92d25556190b4cf44f4e48158a6f0952
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73162743"
---
# <a name="azure-security-management-and-monitoring-overview"></a>Overzicht van Azure-beveiligingsbeheer en -bewaking
In dit artikel vindt u een overzicht van de beveiligingsfuncties en services die Azure biedt om te helpen bij het beheer en de bewaking van Azure-cloudservices en virtuele machines.

## <a name="role-based-access-control"></a>Op rollen gebaseerd toegangsbeheer

RBAC (Role-Based Access Control) biedt gedetailleerd toegangsbeheer voor Azure-bronnen. Door RBAC te gebruiken, u mensen alleen de hoeveelheid toegang geven die ze nodig hebben om hun werk uit te voeren. RBAC kan u ook helpen ervoor te zorgen dat wanneer mensen de organisatie verlaten, ze geen toegang meer hebben tot bronnen in de cloud.

Meer informatie:

* [Active Directory-teamblog over RBAC](https://cloudblogs.microsoft.com/enterprisemobility/?product=azure-active-directory)
* [Azure-toegangsbeheer op basis van azure-rollen](../../role-based-access-control/role-assignments-portal.md)

## <a name="antimalware"></a>Antimalware

Met Azure u antimalwaresoftware van grote beveiligingsleveranciers zoals Microsoft, Symantec, Trend Micro, McAfee en Kaspersky gebruiken. Deze software helpt uw virtuele machines te beschermen tegen schadelijke bestanden, adware en andere bedreigingen.

Microsoft Antimalware voor Azure Cloud Services en Virtual Machines biedt u de mogelijkheid om een antimalwaremiddel te installeren voor zowel PaaS-rollen als virtuele machines. Op basis van System Center Endpoint Protection brengt deze functie bewezen on-premises beveiligingstechnologie naar de cloud.

We bieden ook diepgaande integratie voor Trend's [Deep Security-](https://www.trendmicro.com/us/enterprise/cloud-solutions/deep-security/) en [SecureCloud-producten](https://www.trendmicro.com/us/enterprise/cloud-solutions/secure-cloud/) in het Azure-platform. Deep Security is een antivirusoplossing en SecureCloud is een versleutelingsoplossing. Deep Security wordt geïmplementeerd in VM's via een extensiemodel. Door de Gebruikersinterface van de Azure-portal en PowerShell te gebruiken, u ervoor kiezen om Deep Security te gebruiken in nieuwe VM's die worden gesponnen of bestaande VM's die al zijn geïmplementeerd.

Symantec Endpoint Protection (SEP) wordt ook ondersteund op Azure. Door middel van portalintegratie u opgeven dat u VAN plan bent SEP op een VM te gebruiken. SEP kan op een nieuwe VM worden geïnstalleerd via de Azure-portal, of het kan via PowerShell op een bestaande VM worden geïnstalleerd.

Meer informatie:

* [Deploying Antimalware Solutions on Azure Virtual Machines](https://azure.microsoft.com/blog/deploying-antimalware-solutions-on-azure-virtual-machines/) (Antimalware-oplossingen implementeren op virtuele machines van Azure)
* [Microsoft Antimalware voor Azure Cloud Services en virtuele machines](antimalware.md)
* [Trend Micro Deep Security as a Service installeren en configureren op een Windows VM](/azure/virtual-machines/windows/classic/install-trend)
* [Symantec Endpoint Protection installeren en configureren op een Windows VM](/azure/virtual-machines/windows/classic/install-symantec)
* [Nieuwe antimalwareopties voor het beschermen van virtuele azure-machines](https://azure.microsoft.com/blog/new-antimalware-options-for-protecting-azure-virtual-machines/)

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication

Azure Multi-Factor Authentication is een verificatiemethode waarvoor meer dan één verificatiemethode moet worden gebruikt. Het voegt een kritieke tweede beveiligingslaag toe aan aanmeldingen en transacties van gebruikers.

Multi-Factor Authentication helpt de toegang tot gegevens en toepassingen te waarborgen en tegelijkertijd te voldoen aan de vraag van gebruikers naar een eenvoudig aanmeldingsproces. Het levert sterke verificatie via een reeks verificatieopties (telefoongesprek, sms of mobiele app-melding of verificatiecode) en OATH-tokens van derden.

Meer informatie:

* [Multi-Factor Authentication](https://azure.microsoft.com/documentation/services/multi-factor-authentication/)
* [Wat is Azure Multi-Factor Authentication?](/azure/active-directory/authentication/multi-factor-authentication)
* [Hoe Multi-Factor Authentication werkt](../../active-directory/authentication/concept-mfa-howitworks.md)

## <a name="expressroute"></a>ExpressRoute

U Azure ExpressRoute gebruiken om uw on-premises netwerken uit te breiden naar de Microsoft Cloud via een speciale privéverbinding die wordt gefaciliteerd door een connectiviteitsprovider. Met ExpressRoute u verbindingen tot stand brengen met Microsoft-cloudservices zoals Azure, Office 365 en CRM Online. Connectiviteit kan zijn van:

* Een any-to-any (IP VPN) netwerk.
* Een point-to-point Ethernet-netwerk.
* Een virtuele cross-verbinding via een connectiviteitsprovider op een colocatiefaciliteit.

ExpressRoute-verbindingen gaan niet via het openbare internet. Ze kunnen bieden meer betrouwbaarheid, hogere snelheden, lagere latencies, en hogere beveiliging dan typische verbindingen via het internet.

Meer informatie:

* [Technisch overzicht van ExpressRoute](../../expressroute/expressroute-introduction.md)

## <a name="virtual-network-gateways"></a>Virtuele netwerkgateways

VPN-gateways, ook wel Azure virtual network gateways genoemd, worden gebruikt om netwerkverkeer tussen virtuele netwerken en on-premises locaties te verzenden. Ze worden ook gebruikt om verkeer te verzenden tussen meerdere virtuele netwerken binnen Azure (netwerk naar netwerk). VPN-gateways bieden veilige cross-premises connectiviteit tussen Azure en uw infrastructuur.

Meer informatie:

* [Over VPN-gateways](../../vpn-gateway/vpn-gateway-about-vpngateways.md)
* [Overzicht van Azure-netwerkbeveiliging](network-overview.md)

## <a name="privileged-identity-management"></a>Privileged Identity Management

Soms moeten gebruikers bevoorrechte bewerkingen uitvoeren in Azure-resources of andere SaaS-toepassingen. Dit betekent vaak dat organisaties hen permanent bevoorrechte toegang geven in Azure Active Directory (Azure AD).

Dit is een groeiend beveiligingsrisico voor door de cloud gehoste resources omdat organisaties niet voldoende kunnen controleren wat die gebruikers doen met hun bevoorrechte toegang. Bovendien, als een gebruikersaccount met bevoorrechte toegang is aangetast, kan die ene inbreuk de algehele cloudbeveiliging van een organisatie beïnvloeden. Azure AD Privileged Identity Management helpt dit risico op te lossen door de belichtingstijd van bevoegdheden te verlagen en de zichtbaarheid in het gebruik te vergroten.  

Privileged Identity Management introduceert het concept van een tijdelijke beheerder voor een rol of "just in time" beheerderstoegang. Dit soort beheerder is een gebruiker die een activeringsproces voor die toegewezen rol moet voltooien. Het activeringsproces wijzigt de toewijzing van de gebruiker in een rol in Azure AD van inactief naar actief, voor een bepaalde periode.

Meer informatie:

* [Azure AD Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md)
* [Aan de slag met Azure AD Privileged Identity Management](../../active-directory/privileged-identity-management/pim-getting-started.md)

## <a name="identity-protection"></a>Identiteitsbeveiliging

Azure AD Identity Protection biedt een geconsolideerde weergave van verdachte aanmeldingsactiviteiten en potentiële kwetsbaarheden om uw bedrijf te beschermen. Identiteitsbescherming detecteert verdachte activiteiten voor gebruikers en bevoorrechte (beheerders) identiteiten, op basis van signalen zoals:

* Brute-force aanvallen.
* Gelekte referenties.
* Aanmeldingen vanaf onbekende locaties en geïnfecteerde apparaten.

Door meldingen te verstrekken en aanbevolen herstel, helpt Identity Protection om risico's in realtime te beperken. Het berekent de ernst van het gebruikersrisico. U op risico's gebaseerd beleid configureren om de toegang tot toepassingen automatisch te beschermen tegen toekomstige bedreigingen.

Meer informatie:

* [Azure Active Directory-identiteitsbeveiliging](/azure/active-directory/active-directory-identityprotection)
* [Kanaal 9: Azure AD en Identity Show: Preview voor identiteitsbescherming](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

## <a name="security-center"></a>Security Center

Azure Security Center helpt u bedreigingen te voorkomen, te detecteren en erop te reageren. Security Center geeft u meer inzicht in en controle over de beveiliging van uw Azure-resources. Het biedt geïntegreerde beveiligingsbewaking en beleidsbeheer voor uw Azure-abonnementen. Het helpt bij het detecteren van bedreigingen die anders onopgemerkt zouden kunnen blijven, en werkt met een breed ecosysteem van beveiligingsoplossingen.

Security Center helpt u bij het optimaliseren en bewaken van de beveiliging van uw Azure-bronnen door:

* Hiermee u beleidsregels voor uw Azure-abonnementsbronnen definiëren volgens:
  * De beveiligingsbehoeften van uw bedrijf.
  * Het type toepassingen of de gevoeligheid van de gegevens in elk abonnement.
* De status van uw virtuele Azure-machines, netwerken en toepassingen controleren.
* Een lijst met geprioritteerde beveiligingswaarschuwingen, inclusief waarschuwingen van geïntegreerde partneroplossingen. Het biedt ook de informatie die u nodig hebt om snel een aanval te onderzoeken en aanbevelingen over hoe deze te verhelpen.

Meer informatie:

* [Inleiding tot Azure Security Center](../../security-center/security-center-intro.md)
* [Uw veilige score verbeteren in Azure Security Center](../../security-center/security-center-secure-score.md)

## <a name="intelligent-security-graph"></a>Intelligente beveiligingsgrafiek

Intelligent Security Graph biedt real-time bescherming tegen bedreigingen in Microsoft-producten en -services. Het maakt gebruik van geavanceerde analyses die een enorme hoeveelheid bedreigingsinformatie en beveiligingsgegevens koppelen om inzichten te bieden die de organisatiebeveiliging kunnen versterken. Microsoft maakt gebruik van geavanceerde analyses: het verwerken van meer dan 450 miljard verificaties per maand, het scannen van 400 miljard e-mails op malware en phishing en het bijwerken van een miljard apparaten om rijkere inzichten te bieden. Deze inzichten kunnen uw organisatie helpen om aanvallen snel te detecteren en erop te reageren.

* [Intelligente beveiligingsgrafiek](https://www.microsoft.com/security/intelligence)

## <a name="next-steps"></a>Volgende stappen
Meer informatie over het [gedeelde verantwoordelijkheidsmodel](shared-responsibility.md) en welke beveiligingstaken door Microsoft worden uitgevoerd en welke taken door u worden uitgevoerd.

Zie [Beveiligingsbeheer in Azure](management.md)voor meer informatie over beveiligingsbeheer.
