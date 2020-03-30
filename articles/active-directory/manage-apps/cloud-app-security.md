---
title: Zichtbaarheid en beheer van apps met Microsoft Cloud App Security
description: Leer manieren om app-risiconiveaus te identificeren, inbreuken en lekken in realtime te stoppen en gebruik app-connectors om gebruik te maken van api's van leveranciers voor zichtbaarheid en beheer.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: overview
ms.workload: identity
ms.date: 02/03/2020
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 206e1a06acddae0973d5dbc7e64212026149f217
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77069740"
---
# <a name="cloud-app-visibility-and-control"></a>Zichtbaarheid en beheer van cloud-apps

Om optimaal te kunnen profiteren van cloud-apps en -services, moet een IT-team de juiste balans vinden tussen ondersteuning van toegang met behoud van controle om kritieke gegevens te beschermen. Microsoft Cloud App Security biedt uitgebreide zichtbaarheid, controle over gegevensreizen en geavanceerde analyses om cyberbedreigingen te identificeren en te bestrijden in al uw Microsoft- en externe cloudservices.

## <a name="discover-and-manage-shadow-it-in-your-network"></a>Schaduw-IT in uw netwerk detecteren en beheren

Wanneer IT-beheerders wordt gevraagd hoeveel cloud-apps ze denken dat hun werknemers gebruiken, zeggen ze gemiddeld 30 of 40, terwijl in werkelijkheid het gemiddelde meer dan 1.000 afzonderlijke apps is die worden gebruikt door werknemers in uw organisatie. Shadow IT helpt u te weten en te identificeren welke apps worden gebruikt en wat uw risiconiveau is. Tachtig procent van de werknemers gebruikt niet-goedgekeurde apps die niemand heeft beoordeeld en mogelijk niet voldoet aan uw beveiligings- en nalevingsbeleid. En omdat uw medewerkers toegang hebben tot uw bronnen en apps van buiten uw bedrijfsnetwerk, volstaat het niet langer om regels en beleidsregels op uw firewalls te hebben.

Gebruik Microsoft Cloud App Discovery (een Azure Active Directory Premium P1-functie) om te ontdekken welke apps worden gebruikt, het risico van deze apps te verkennen, beleid te configureren om nieuwe riskante apps te identificeren en deze apps niet te sanctioneren om deze apps native te blokkeren met behulp van uw proxy of firewall toestel.

- Schaduw-IT detecteren en identificeren
- Evalueren en analyseren
- Uw apps beheren
- Advanced Shadow IT discovery reporting
- Gesanctioneerde apps beheren
 
### <a name="learn-more"></a>Meer informatie

- [Ontdek en beheer schaduw-IT in uw netwerk](https://docs.microsoft.com/cloud-app-security/tutorial-shadow-it)
- [Ontdekte apps met Cloud App Security](https://docs.microsoft.com/cloud-app-security/discovered-apps)
 
## <a name="user-session-visibility-and-control"></a>Zichtbaarheid en controle van gebruikerssessies 

Op de werkplek van vandaag is het vaak niet genoeg om te weten wat er daarna in uw cloudomgeving gebeurt. U wilt inbreuken en lekken in realtime stoppen voordat werknemers opzettelijk of per ongeluk uw gegevens en uw organisatie in gevaar brengen. Samen met Azure Active Directory (Azure AD) biedt Microsoft Cloud App Security deze mogelijkheden in een holistische en geïntegreerde ervaring met Conditional Access App Control. 

Sessiebeheer maakt gebruik van een reverse proxy-architectuur en is uniek geïntegreerd met Azure AD Conditional Access. Met Voorwaardelijke toegang voor Azure AD u toegangsbesturingselementen voor de apps van uw organisatie afdwingen op basis van bepaalde voorwaarden. De voorwaarden bepalen wie (gebruiker of groep gebruikers) en welke (welke cloud-apps) en waar (welke locaties en netwerken) een Conditional Access-beleid wordt toegepast. Nadat u de voorwaarden hebt bepaald, u gebruikers doorsturen naar Cloud App Security, waar u gegevens in realtime beveiligen.  

Met deze controle u:  
- Bestandsdownloads beheren
- B2B-scenario's bewaken  
- Toegang tot bestanden beheren  
- Documenten beveiligen bij het downloaden  
 
### <a name="learn-more"></a>Meer informatie

- [Apps beveiligen met sessiebeheer in cloud-appbeveiliging](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
 
## <a name="advanced-app-visibility-and-controls"></a>Geavanceerde zichtbaarheid en besturingselementen voor apps 

App-connectors gebruiken de API's van app-providers om meer zichtbaarheid en controle van Microsoft Cloud App Security mogelijk te maken via de apps waarmee u verbinding maakt. Cloud App Security maakt gebruik van de API's van de cloudprovider. Elke service heeft zijn eigen framework- en API-beperkingen, zoals beperking, API-limieten, dynamische tijdverschuivende API-vensters en andere. Het productteam van Cloud App Security werkte met deze services samen om het gebruik van API's te optimaliseren en de beste prestaties te leveren. Rekening houdend met verschillende beperkingen die services opleggen aan hun API's, maken de Cloud App Security-engines gebruik van hun maximaal toegestane capaciteit. Voor sommige bewerkingen, zoals het scannen van alle bestanden in de tenant, zijn tal van API-aanroepen vereist, zodat ze over een langere periode zijn verspreid. Verwacht dat sommige beleidsregels enkele uren of dagen worden uitgevoerd. 
 
### <a name="learn-more"></a>Meer informatie  

- [Apps verbinden in Cloud App Security](https://docs.microsoft.com/cloud-app-security/enable-instant-visibility-protection-and-governance-actions-for-your-apps)

## <a name="next-steps"></a>Volgende stappen

- [Ontdek en beheer schaduw-IT in uw netwerk](https://docs.microsoft.com/cloud-app-security/tutorial-shadow-it)
- [Ontdekte apps met Cloud App Security](https://docs.microsoft.com/cloud-app-security/discovered-apps)
- [Apps beveiligen met sessiebeheer in cloud-appbeveiliging](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
- [Apps verbinden in Cloud App Security](https://docs.microsoft.com/cloud-app-security/enable-instant-visibility-protection-and-governance-actions-for-your-apps)
