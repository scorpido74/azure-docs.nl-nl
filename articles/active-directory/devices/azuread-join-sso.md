---
title: Hoe SSO-resources werken op azure AD-samengevoegde apparaten | Microsoft Documenten
description: Lees hoe u hybride Azure Active Directory-gekoppelde apparaten kunt configureren.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: f9d8c0cd803424e117bd4dc7a3382b7b32df2d05
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672706"
---
# <a name="how-sso-to-on-premises-resources-works-on-azure-ad-joined-devices"></a>Hoe SSO-to-on-premises resources werken op Azure AD-apparaten

Het is waarschijnlijk geen verrassing dat een Azure Active Directory (Azure AD) samengevoegd apparaat u één sso-ervaring (sign-on) geeft voor de cloud-apps van uw tenant. Als uw omgeving een on-premises Active Directory (AD) heeft, u de SSO-ervaring op deze apparaten uitbreiden.

In dit artikel wordt uitgelegd hoe dit werkt.

## <a name="prerequisites"></a>Vereisten

 Als azure AD-apparaten niet zijn verbonden met het netwerk van uw organisatie, is een VPN of andere netwerkinfrastructuur vereist. On-premises SSO vereist gezichtscommunicatie met uw on-premises AD DS-domeincontrollers.

## <a name="how-it-works"></a>Hoe werkt het? 

Omdat u slechts één gebruikersnaam en wachtwoord moet onthouden, vereenvoudigt SSO de toegang tot uw bronnen en verbetert het de beveiliging van uw omgeving. Met een Azure AD-apparaat hebben uw gebruikers al een SSO-ervaring voor de cloud-apps in uw omgeving. Als uw omgeving een Azure AD en een on-premises AD heeft, wilt u waarschijnlijk het bereik van uw SSO-ervaring uitbreiden naar uw lob-apps, bestandsshares en printers (On-premises Line Of Business).

Azure AD-apparaten hebben geen kennis over uw on-premises AD-omgeving omdat ze er niet aan zijn verbonden. U deze apparaten echter aanvullende informatie over uw on-premises AD aan deze apparaten verstrekken met Azure AD Connect.

Een omgeving met beide, een Azure AD en een on-premises AD, is ook bekend met een hybride omgeving. Als u een hybride omgeving hebt, is het waarschijnlijk dat u azure AD Connect al hebt geïmplementeerd om uw on-premises identiteitsgegevens te synchroniseren met de cloud. Als onderdeel van het synchronisatieproces synchroniseert Azure AD Connect on-premises gebruikersgegevens met Azure AD. Wanneer een gebruiker zich aanmeldt bij een Azure AD-apparaat in een hybride omgeving:

1. Azure AD stuurt de naam van het on-premises domein waar de gebruiker lid van is terug naar het apparaat.
1. De lokale beveiligingsinstantie (LSA) service maakt Kerberos authenticatie op het apparaat.

Tijdens een toegangspoging tot een resource die Kerberos opvraagt in de on-premises omgeving van de gebruiker, gaat het apparaat als:

1. Hiermee verzendt u de on-premises domeingegevens en gebruikersreferenties naar de gelokaliseerde DC om de gebruiker te laten verifiëren.
1. Ontvangt een Kerberos [Ticket-Granting Ticket (TGT)](/windows/desktop/secauthn/ticket-granting-tickets) dat wordt gebruikt om toegang te krijgen tot AD-verbonden bronnen. Als de poging om de TGT voor het AAD-verbindingsdomein te krijgen mislukt (gerelateerde DCLocator-time-out kan een vertraging veroorzaken), worden credential manager-vermeldingen geprobeerd of ontvangt de gebruiker mogelijk een meldingspop-up waarin referenties voor de doelbron worden aangevraagd.

Alle apps die zijn geconfigureerd voor **Windows-geïntegreerde verificatie** krijgen SSO naadloos wanneer een gebruiker er toegang toe probeert te krijgen.

Windows Hello for Business vereist extra configuratie om on-premises SSO in te schakelen vanaf een Azure AD-apparaat. Zie [Azure AD-samengevoegde apparaten configureren voor on-premises single-sign-on met Windows Hello voor Bedrijven](/windows/security/identity-protection/hello-for-business/hello-hybrid-aadj-sso-base)voor meer informatie. 

## <a name="what-you-get"></a>Wat u krijgt

Met SSO u op een Azure AD-apparaat: 

- Een UNC-pad openen op een AD-lidserver
- Toegang tot een webserver van AD-leden die is geconfigureerd voor windows-geïntegreerde beveiliging 

Als u uw on-premises AD vanaf een Windows-apparaat wilt beheren, installeert u de [hulpprogramma's voor extern serverbeheer voor Windows 10.](https://www.microsoft.com/download/details.aspx?id=45520)

U kunt gebruikmaken van:

- De ADUC (Active Directory Users and Computers) maakt de module om alle AD-objecten te beheren. U moet echter wel het domein opgeven waarmee u handmatig verbinding wilt maken.
- De DHCP-module om een DHCP-server met AD-lid te beheren. Het kan echter nodig zijn om de naam of het adres van de DHCP-server op te geven.
 
## <a name="what-you-should-know"></a>Wat u moet weten

Mogelijk moet u uw [domeinfilter in](../hybrid/how-to-connect-sync-configure-filtering.md#domain-based-filtering) Azure AD Connect aanpassen om ervoor te zorgen dat de gegevens over de vereiste domeinen worden gesynchroniseerd.

Apps en resources die afhankelijk zijn van Active Directory-machineverificatie werken niet omdat azure AD-apparaten geen computerobject in AD hebben. 

U geen bestanden delen met andere gebruikers op een door Azure gekoppeld apparaat.

## <a name="next-steps"></a>Volgende stappen

Zie [Wat is apparaatbeheer in Azure Active Directory voor](overview.md) meer informatie? 
