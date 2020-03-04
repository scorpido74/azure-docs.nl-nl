---
title: Wat is de apparaat-id in Azure Active Directory?
description: Meer informatie over hoe het beheer van apparaat-id's u kan helpen bij het beheren van de apparaten die toegang hebben tot resources in uw omgeving.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: overview
ms.date: 06/27/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 594f0ed55b5ce5c31e87fd2011f3bc1522a12380
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2020
ms.locfileid: "78249037"
---
# <a name="what-is-a-device-identity"></a>Wat is een apparaat-id?

Dankzij de toename van apparaten van alle vormen en grootten en het BYOD-concept (your own Device), worden IT-professionals geconfronteerd met twee enigszins tegengestelde doel stellingen:

- Eind gebruikers toestaan overal en altijd productief te zijn
- De bedrijfs middelen beveiligen

Voor het beveiligen van deze assets moeten IT-mede werkers eerst de apparaat-id's beheren. IT-mede werkers kunnen de identiteit van het apparaat bouwen met hulpprogram ma's als Microsoft Intune om ervoor te zorgen dat aan de normen voor beveiliging en naleving wordt voldaan. Met Azure Active Directory (Azure AD) kunt u eenmalige aanmelding op apparaten, apps en services vanaf elke locatie via deze apparaten.

- Uw gebruikers krijgen toegang tot de activa van uw organisatie die ze nodig hebben. 
- Uw IT-mede werkers krijgen de besturings elementen die ze nodig hebben om uw organisatie te beveiligen.

Apparaat-id-beheer is de basis voor [voorwaardelijke toegang op basis van apparaten](../conditional-access/require-managed-devices.md). Met op apparaten gebaseerd beleid voor voorwaardelijke toegang kunt u ervoor zorgen dat toegang tot resources in uw omgeving alleen mogelijk is met beheerde apparaten.

## <a name="getting-devices-in-azure-ad"></a>Apparaten in azure AD ophalen

U hebt meerdere opties om een apparaat in azure AD op te halen:

- **Geregistreerde Azure AD**
   - Apparaten die zijn geregistreerd bij Azure AD, zijn doorgaans persoonlijk eigendom of mobiele apparaten en zijn aangemeld met een persoonlijke Microsoft-account of een ander lokaal account.
      - Windows 10
      - iOS
      - Android
      - MacOS
- **Toegevoegd aan Azure AD**
   - Apparaten die lid zijn van Azure AD zijn eigendom van een organisatie en zijn aangemeld met een Azure AD-account dat deel uitmaakt van die organisatie. Ze bestaan alleen in de Cloud.
      - Windows 10 
- **lid is van hybride Azure Active Directory**
   - Apparaten die lid zijn van hybride Azure AD zijn eigendom van een organisatie en zijn aangemeld met een Azure AD-account dat deel uitmaakt van die organisatie. Ze bevinden zich in de Cloud en on-premises.
      - Windows 7, 8,1 of 10
      - Windows Server 2008 of hoger

![Weer gave van apparaten op de Blade Azure AD-apparaten](./media/overview/azure-active-directory-devices-all-devices.png)

## <a name="device-management"></a>Apparaatbeheer

Apparaten in azure AD kunnen worden beheerd met Mobile Device Management (MDM)-hulpprogram ma's zoals Microsoft Intune, micro soft endpoint Configuration Manager, groepsbeleid (Hybrid Azure AD-deelname), Mobile Application Management-hulpprogram ma's (MAM) of andere hulpprogram ma's van derden.

## <a name="resource-access"></a>Toegang tot bronnen

Het registreren en koppelen van apparaten aan Azure AD biedt uw gebruikers naadloze aanmelding (SSO) naar cloud resources. Dit proces biedt beheerders de mogelijkheid om beleid voor voorwaardelijke toegang toe te passen op resources op basis van het apparaat waartoe ze toegang hebben. 

> [!NOTE]
> Voor op apparaten gebaseerd beleid voor voorwaardelijke toegang moeten hybride, aan Azure AD gekoppelde apparaten of aan Azure AD gekoppelde of Azure AD geregistreerde apparaten zijn gekoppeld.

Apparaten die zijn toegevoegd aan Azure AD of hybride Azure AD, profiteren van eenmalige aanmelding bij de on-premises resources van uw organisatie en in cloud resources. Meer informatie vindt u in het artikel, [hoe SSO to on-premises resources werkt op apparaten die zijn toegevoegd aan Azure AD](azuread-join-sso.md).

## <a name="device-security"></a>Beveiliging van apparaten

- **Geregistreerde apparaten van Azure AD** gebruiken een account dat wordt beheerd door de eind gebruiker. dit account is een Microsoft-account of een andere lokaal beheerde referentie die is beveiligd met een of meer van de volgende.
   - Wachtwoord
   - PINCODE
   - Patroon
   - Windows Hello
- Aan **Azure AD gekoppelde of hybride Azure AD gekoppelde apparaten** maken gebruik van een organisatie account in azure AD dat is beveiligd met een of meer van de volgende.
   - Wachtwoord
   - Windows Hello voor Bedrijven

## <a name="provisioning"></a>Inrichting

Het ophalen van apparaten in voor Azure AD kan worden uitgevoerd op een selfservice manier of een bewaakt inrichtings proces door beheerders.

## <a name="summary"></a>Samenvatting

Met apparaat-id-beheer in azure AD kunt u het volgende doen:

- Vereenvoudig het proces van het in azure AD inbrengen en beheren van apparaten
- Uw gebruikers gemakkelijke toegang tot de cloudgebaseerde bronnen van uw organisatie bieden

## <a name="license-requirements"></a>Licentievereisten

[!INCLUDE [Active Directory P1 license](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [geregistreerde Azure ad-apparaten](concept-azure-ad-register.md)
- Meer informatie over [apparaten die zijn toegevoegd aan Azure AD](concept-azure-ad-join.md)
- Meer informatie over [hybride Azure AD gekoppelde apparaten](concept-azure-ad-join-hybrid.md)
- Zie [apparaat-Id's beheren met behulp van de Azure Portal](device-management-azure-portal.md)voor een overzicht van het beheren van apparaat-id's in de Azure Portal.
- Zie [Azure Active Directory op apparaten gebaseerde beleids regels voor voorwaardelijke toegang configureren](../conditional-access/require-managed-devices.md)voor meer informatie over voorwaardelijke toegang op basis van apparaten.
