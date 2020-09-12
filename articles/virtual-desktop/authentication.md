---
title: Windows-verificatie op virtueel bureau blad-Azure
description: Verificatie methoden voor Windows virtueel bureau blad.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 5681228e5da2708912d69f16a4b09a4a93d8bb04
ms.sourcegitcommit: 206629373b7c2246e909297d69f4fe3728446af5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/06/2020
ms.locfileid: "89500297"
---
# <a name="supported-authentication-methods"></a>Ondersteunde verificatie methoden

In dit artikel krijgt u een beknopt overzicht van wat voor soort verificatie u kunt gebruiken in Windows virtueel bureau blad.

## <a name="session-host-authentication"></a>Session Host-verificatie

Virtueel bureau blad van Windows ondersteunt zowel NT LAN Manager (NTLM) als Kerberos voor authenticatie van sessie-hosts. Voor het gebruik van Kerberos moet de client echter Kerberos-beveiligings tickets ontvangen van een Key Distribution Center-service (KDC) die wordt uitgevoerd op een domein controller. Om tickets te krijgen, moet de client een directe regel van het gezichts vermogen aan de domein controller hebben. U kunt een directe gezichts lijn krijgen met behulp van uw bedrijfs netwerk. U kunt ook een VPN-verbinding met uw bedrijfs netwerk gebruiken.

Dit zijn de momenteel ondersteunde aanmeldings methoden:

- Windows Desktop-client
    - Gebruikersnaam en wachtwoord
    - Cards
    - Windows Hello
- Windows Store-client
    - Gebruikersnaam en wachtwoord
- Webclient
    - Gebruikersnaam en wachtwoord
- Android
    - Gebruikersnaam en wachtwoord
- iOS
    - Gebruikersnaam en wachtwoord
- macOS
    - Gebruikersnaam en wachtwoord

>[!NOTE]
>Smart Card en Windows hello kunnen alleen Kerberos gebruiken om zich aan te melden. Aanmelden met Kerberos vereist een regel voor het gezichts vermogen van de domein controller.

## <a name="hybrid-identity"></a>Hybride identiteit

Het virtuele bureau blad van Windows ondersteunt [hybride identiteiten](../active-directory/hybrid/whatis-hybrid-identity.md) via Azure Active Directory (AD), waaronder federatieve Active Directory Federation Services (ADFS). Omdat gebruikers moeten kunnen worden gedetecteerd via Azure AD, ondersteunt Windows Virtual Desktop geen zelfstandige Active Directory implementaties met ADFS.

## <a name="single-sign-on-sso"></a>Eenmalige aanmelding (SSO)

Windows virtueel bureau blad biedt momenteel geen ondersteuning voor Active Directory Federation Services (ADFS) voor SSO.

De enige manier om te voor komen dat u wordt gevraagd om uw referenties voor de host op te slaan in de-client. U kunt dit het beste alleen doen met beveiligde apparaten om te voor komen dat andere gebruikers toegang krijgen tot uw resources.

## <a name="next-steps"></a>Volgende stappen

Nieuws gierig over andere manieren om uw implementatie veilig te blijven? Bekijk [aanbevolen beveiligings procedures](security-guide.md).
