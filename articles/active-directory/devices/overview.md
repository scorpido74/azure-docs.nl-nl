---
title: Wat is apparaatidentiteit in Azure Active Directory?
description: Ontdek hoe het beheer van apparaatidentiteiten u kan helpen bij het beheren van de apparaten die toegang hebben tot bronnen in uw omgeving.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: overview
ms.date: 03/26/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: c895a13eb9b2bed9e3a8a5a250c4e925dfa834c5
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80331817"
---
# <a name="what-is-a-device-identity"></a>Wat is een apparaat-id?

Met de wildgroei aan apparaten in alle soorten en maten en het Bring Your Own Device (BYOD)-concept worden IT-professionals geconfronteerd met twee enigszins tegengestelde doelen:

- Laat eindgebruikers productief zijn, waar en wanneer dan ook
- De activa van de organisatie beschermen

Om deze assets te beschermen, moeten IT-medewerkers eerst de identiteit van het apparaat beheren. IT-medewerkers kunnen voortbouwen op de identiteit van het apparaat met tools zoals Microsoft Intune om ervoor te zorgen dat aan de normen voor beveiliging en naleving wordt voldaan. Azure Active Directory (Azure AD) maakt eenmalige aanmelding op apparaten, apps en services mogelijk, overal via deze apparaten.

- Uw gebruikers krijgen toegang tot de assets van uw organisatie die ze nodig hebben. 
- Uw IT-medewerkers krijgen de controles die ze nodig hebben om uw organisatie te beveiligen.

Apparaatidentiteitsbeheer is de basis voor [voorwaardelijke toegang op basis van apparaten.](../conditional-access/require-managed-devices.md) Met apparaatgebaseerdbeleid voor voorwaardelijke toegang u ervoor zorgen dat toegang tot bronnen in uw omgeving alleen mogelijk is met beheerde apparaten.

## <a name="getting-devices-in-azure-ad"></a>Apparaten in Azure AD opmaken

Als u een apparaat in Azure AD wilt krijgen, hebt u meerdere opties:

- **Azure AD-geregistreerd**
   - Apparaten die Azure AD-geregistreerd zijn, zijn doorgaans persoonlijk eigendom of mobiele apparaten en zijn aangemeld met een persoonlijk Microsoft-account of een ander lokaal account.
      - Windows 10
      - iOS
      - Android
      - MacOS
- **Neemt deel aan Azure AD**
   - Apparaten waarvan Azure AD is toegetreden, zijn eigendom van een organisatie en zijn aangemeld met een Azure AD-account dat deel uitmaakt van die organisatie. Ze bestaan alleen in de cloud.
      - Windows 10 
      - Windows Server 2019 (Servercore wordt niet ondersteund)
- **lid is van hybride Azure Active Directory**
   - Apparaten waarvan hybride Azure AD is toegetreden, zijn eigendom van een organisatie en zijn aangemeld met een Azure AD-account dat tot die organisatie behoort. Ze bestaan in de cloud en on-premises.
      - Windows 7, 8.1 of 10
      - Windows Server 2008 of nieuwer

![Apparaten die worden weergegeven in het blade van Azure AD-apparaten](./media/overview/azure-active-directory-devices-all-devices.png)

## <a name="device-management"></a>Apparaatbeheer

Apparaten in Azure AD kunnen worden beheerd met mdm-hulpprogramma's (Mobile Device Management), zoals Microsoft Intune, Microsoft Endpoint Configuration Manager, Group Policy (hybrid Azure AD join), Mam-hulpprogramma's (Mobile Application Management) of andere hulpprogramma's van derden.

## <a name="resource-access"></a>Toegang tot resources

Door apparaten te registreren en samen te voegen met Azure AD kunnen uw gebruikers naadloze aanmeldings (SSO) naar cloudbronnen leiden. Dit proces biedt beheerders ook de mogelijkheid om beleid voor voorwaardelijke toegang toe te passen op resources op basis van het apparaat waarze toegang toe hebben. 

> [!NOTE]
> Voor beleid voor voorwaardelijke toegang op basis van apparaten zijn hybride Azure AD-apparaten vereist of azure AD-apparaten of azure AD-geregistreerde apparaten.

Apparaten die Azure AD zijn samengevoegd of hybride Azure AD hebben baat bij SSO voor de on-premises resources van uw organisatie en cloudresources. Meer informatie is te vinden in het artikel, [Hoe SSO om on-premises resources werkt op Azure AD samengevoegde apparaten](azuread-join-sso.md).

## <a name="device-security"></a>Apparaatbeveiliging

- **Azure AD-geregistreerde apparaten** maken gebruik van een account dat wordt beheerd door de eindgebruiker, dit account is een Microsoft-account of een andere lokaal beheerde referentie die is beveiligd met een of meer van de volgende opties.
   - Wachtwoord
   - Pincode
   - Patroon
   - Windows Hello
- **Azure AD-samengevoegde of hybride Azure AD-apparaten** maken gebruik van een organisatie-account in Azure AD dat is beveiligd met een of meer van de volgende opties.
   - Wachtwoord
   - Windows Hello voor Bedrijven

## <a name="provisioning"></a>Inrichten

Apparaten in Azure AD krijgen, kan op een selfservice-manier of een gecontroleerd inrichtingsproces door beheerders worden uitgevoerd.

## <a name="summary"></a>Samenvatting

Met apparaatidentiteitsbeheer in Azure AD u het als nog stellen:

- Vereenvoudig het proces van het brengen en beheren van apparaten in Azure AD
- Bied uw gebruikers een gebruiksvriendelijke toegang tot de cloudbronnen van uw organisatie

## <a name="license-requirements"></a>Licentievereisten

[!INCLUDE [Active Directory P1 license](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [azure AD-geregistreerde apparaten](concept-azure-ad-register.md)
- Meer informatie over [azure AD-apparaten](concept-azure-ad-join.md)
- Meer informatie over [hybride Azure AD-apparaten](concept-azure-ad-join-hybrid.md)
- Zie [Apparaatidentiteiten beheren met de Azure-portal](device-management-azure-portal.md)voor een overzicht van het beheren van apparaatidentiteiten in de Azure-portal.
- Zie [Azure Active Directory-beleid voor voorwaardelijke toegang tot apparaten configureren](../conditional-access/require-managed-devices.md)voor meer informatie over voorwaardelijke toegang op basis van apparaten .
