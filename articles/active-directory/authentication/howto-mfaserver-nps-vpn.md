---
title: Azure MFA Server en VPN's van derden - Azure Active Directory
description: Stapsgewijze configuratiehandleidingen voor Azure MFA Server om te integreren met Cisco, Citrix en Juniper.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: fdb24bcd79f1766a52f290fd6fe0e6e5bf17e7c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74847946"
---
# <a name="advanced-scenarios-with-azure-mfa-server-and-third-party-vpn-solutions"></a>Geavanceerde scenario's met Azure MFA Server en VPN-oplossingen van derden

Azure Multi-Factor Authentication Server (Azure MFA Server) kan worden gebruikt om naadloos verbinding te maken met verschillende VPN-oplossingen van derden. Dit artikel richt zich op Cisco® ASA VPN appliance, Citrix NetScaler SSL VPN appliance en het Juniper Networks Secure Access/Pulse Secure Connect Secure SSL VPN appliance. We hebben configuratiehandleidingen gemaakt om deze drie veelvoorkomende apparaten aan te pakken. Azure MFA Server kan ook worden geïntegreerd met de meeste andere systemen die RADIUS, LDAP, IIS of claimgebaseerde verificatie gebruiken voor AD FS. Meer details vindt u in [Azure MFA Server-configuraties.](howto-mfaserver-deploy.md#next-steps)

> [!IMPORTANT]
> Vanaf 1 juli 2019 biedt Microsoft geen MFA Server meer aan voor nieuwe implementaties. Nieuwe klanten die multi-factor authenticatie van hun gebruikers willen vereisen, moeten azure multi-factor authenticatie in de cloud gebruiken. Bestaande klanten die MFA Server vóór 1 juli hebben geactiveerd, kunnen de nieuwste versie, toekomstige updates downloaden en activeringsreferenties genereren zoals gewoonlijk.

## <a name="cisco-asa-vpn-appliance-and-azure-mfa-server"></a>Cisco ASA VPN-toestel en Azure MFA Server
Azure MFA Server integreert met uw Cisco® ASA VPN-toestel om extra beveiliging te bieden voor Cisco AnyConnect® VPN-aanmeldingen en portaltoegang.  U het LDAP- of RADIUS-protocol gebruiken.  Selecteer een van de volgende opties om de gedetailleerde stapsgewijze configuratiehandleidingen te downloaden.

| Configuratiehandleiding | Beschrijving |
| --- | --- |
| [Cisco ASA met Anyconnect VPN en Azure MFA-configuratie voor LDAP](https://download.microsoft.com/download/A/2/0/A201567C-C3DE-4227-AF89-4567A470899E/Cisco_ASA_Azure_MFA_LDAP.docx) | Uw Cisco ASA VPN-toestel integreren met Azure MFA met LDAP |
| [Cisco ASA met Anyconnect VPN en Azure MFA-configuratie voor RADIUS](https://download.microsoft.com/download/4/5/7/4579C1CF-35B0-4FBE-8A1A-B49CB2CC0382/Cisco_ASA_Azure_MFA_RADIUS.docx) | Uw Cisco ASA VPN-toestel integreren met Azure MFA met RADIUS |

## <a name="citrix-netscaler-ssl-vpn-and-azure-mfa-server"></a>Citrix NetScaler SSL VPN en Azure MFA Server
Azure MFA Server integreert met uw Citrix NetScaler SSL VPN-toestel om extra beveiliging te bieden voor Citrix NetScaler SSL VPN logins en portal toegang.  U het LDAP- of RADIUS-protocol gebruiken.  Selecteer een van de volgende opties om de gedetailleerde stapsgewijze configuratiehandleidingen te downloaden.

| Configuratiehandleiding | Beschrijving |
| --- | --- |
| [Citrix NetScaler SSL VPN en Azure MFA-configuratie voor LDAP](https://download.microsoft.com/download/2/4/E/24E1E722-72DF-471F-A88A-D1338DB1AF83/Citrix_NS_Azure_MFA_LDAP.docx) | Integreer uw Citrix NetScaler SSL VPN met Azure MFA-toestel met LDAP |
| [Citrix NetScaler SSL VPN en Azure MFA-configuratie voor RADIUS](https://download.microsoft.com/download/1/A/4/1A482764-4A63-45C2-A5EC-2B673ACCDD12/Citrix_NS_Azure_MFA_RADIUS.docx) | Integreer uw Citrix NetScaler SSL VPN-toestel met Azure MFA met RADIUS |

## <a name="juniperpulse-secure-ssl-vpn-appliance-and-azure-mfa-server"></a>Juniper/Pulse Secure SSL VPN-toestel en Azure MFA Server
Azure MFA Server integreert met uw Juniper/Pulse Secure SSL VPN-toestel om extra beveiliging te bieden voor Juniper/Pulse Secure SSL VPN-aanmeldingen en portaltoegang.  U het LDAP- of RADIUS-protocol gebruiken.  Selecteer een van de volgende opties om de gedetailleerde stapsgewijze configuratiehandleidingen te downloaden.

| Configuratiehandleiding | Beschrijving |
| --- | --- |
| [Juniper/Pulse Secure SSL VPN en Azure MFA-configuratie voor LDAP](https://download.microsoft.com/download/6/5/8/6587B418-75B1-4FCB-84D4-984BC479309E/JuniperPulse_Azure_MFA_LDAP.docx) | Integreer uw Juniper/Pulse Secure SSL VPN met Azure MFA-toestel met LDAP |
| [Juniper/Pulse Secure SSL VPN en Azure MFA-configuratie voor RADIUS](https://download.microsoft.com/download/7/9/A/79AB3DAD-4799-4379-B1DA-B95ABDF231DC/JuniperPulse_Azure_MFA_RADIUS.docx) | Integreer uw Juniper/Pulse Secure SSL VPN-toestel met Azure MFA met RADIUS |

## <a name="next-steps"></a>Volgende stappen

- [Uw bestaande verificatie-infrastructuur uitbreiden met de NPS-extensie voor Azure Multi-Factor Authentication](howto-mfa-nps-extension.md)

- [Azure-instellingen voor meervoudige verificatie configureren](howto-mfa-mfasettings.md)
