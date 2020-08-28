---
title: Windows-verificatie op virtueel bureau blad-Azure
description: Verificatie methoden voor Windows virtueel bureau blad.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/27/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 8328fa9c9e32a22e8e5093aa343f333cd1914ddc
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/27/2020
ms.locfileid: "89038544"
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

## <a name="single-sign-on-sso"></a>Eenmalige aanmelding (SSO)

Windows virtueel bureau blad ondersteunt momenteel geen Active Directory Federation Services (ADFS) voor verificatie of SSO.

De enige manier om te voor komen dat u wordt gevraagd om uw referenties voor de host op te slaan in de-client. U kunt dit het beste alleen doen met beveiligde apparaten om te voor komen dat andere gebruikers toegang krijgen tot uw resources.

## <a name="next-steps"></a>Volgende stappen

Nieuws gierig over andere manieren om uw implementatie veilig te blijven? Bekijk [aanbevolen beveiligings procedures](security-guide.md).
