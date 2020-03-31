---
title: Eenmalige aanmelding configureren voor een app Application Proxy
description: Hoe u één aanmelding voor uw toepassingsproxy-toepassing snel configureren
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/12/2019
ms.author: mimart
ms.reviewer: japere, asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 589b3e51f27147f0a0432b61c22a024c202e388b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76712015"
---
# <a name="how-to-configure-single-sign-on-to-an-application-proxy-application"></a>Eenmalige aanmelding configureren voor een toepassingsproxytoepassing

Met eenmalige aanmelding (SSO) kunnen uw gebruikers toegang krijgen tot een toepassing zonder meerdere keren te authenticeren. Hiermee kan de enkele verificatie plaatsvinden in de cloud, tegen Azure Active Directory, en kan de service of connector zich voordoen als de gebruiker om eventuele extra verificatieproblemen van de toepassing te voltooien.

## <a name="how-to-configure-single-sign-on"></a>Eenmalige aanmelding configureren op
Als u SSO wilt configureren, moet u eerst ervoor zorgen dat uw toepassing is geconfigureerd voor pre-verificatie via Azure Active Directory. Ga voor deze configuratie naar **Azure Active Directory**  - &gt; **Enterprise Applications**  - &gt; **All Applications**  - &gt; Your application ** - &gt; Application Proxy**. Op deze pagina ziet u het veld 'Pre-verificatie' en controleert u of dit is ingesteld op 'Azure Active Directory'. 

Zie stap 4 van het [publicatiedocument](application-proxy-add-on-premises-application.md)voor apps voor meer informatie over de methoden voor preverificatie.

   ![Verificatiemethode voor vooraf in Azure-portal](./media/application-proxy-config-sso-how-to/app-proxy.png)

## <a name="configuring-single-sign-on-modes-for-application-proxy-applications"></a>Enkele aanmeldingsmodi configureren voor toepassingsproxytoepassingen
Configureer het specifieke type eenmalige aanmelding. De aanmeldingsmethoden worden geclassificeerd op basis van het type verificatie dat de backend-toepassing gebruikt. App Proxy-toepassingen ondersteunen drie soorten aanmelding:

-   **Op een wachtwoord gebaseerde aanmelding:** aanmelding op basis van een wachtwoord kan worden gebruikt voor elke toepassing die gebruikersnaam- en wachtwoordvelden gebruikt om u aan te melden. Configuratiestappen zijn in [Wachtwoord configureren Eenmalige aanmelding voor een Azure AD-galerietoepassing](configure-password-single-sign-on-non-gallery-applications.md).

-   **Geïntegreerde Windows-verificatie:** voor toepassingen die geïntegreerde Windows-verificatie (IWA) gebruiken, is eenmalige aanmelding ingeschakeld via Kerberos Constrained Delegation (KCD). Met deze methode geeft Application Proxy Connectors toestemming in Active Directory om gebruikers na te bootsen en namens hen tokens te verzenden en te ontvangen. Details over het configureren van KCD zijn te vinden in de [Single Sign-On with KCD-documentatie.](application-proxy-configure-single-sign-on-with-kcd.md)

-   **Sign-On op basis van kopteksten:** aanbordopbasis van kopteksten is ingeschakeld via een partnerschap en vereist wel een extra configuratie. Zie de [PingAccess voor Azure AD-documentatie](application-proxy-configure-single-sign-on-with-ping-access.md)voor meer informatie over de partnerschaps- en stapsgewijze instructies voor het configureren van een enkele aanmelding voor een toepassing die kopteksten voor verificatie gebruikt.

-   **SAML-aanmelding**: met SAML-eenmalige aanmelding verifieert Azure AD de toepassing met behulp van het Azure AD-account van de gebruiker. Azure AD communiceert de aanmeldingsgegevens naar de toepassing via een verbindingsprotocol. Met SAML-gebaseerde enkele aanmelding u gebruikers toewijzen aan specifieke toepassingsrollen op basis van regels die u definieert in uw SAML-claims. Zie [SAML voor eenmalige aanmelding met Application Proxy voor](application-proxy-configure-single-sign-on-on-premises-apps.md)informatie over het instellen van SAML-aanmelding.

Elk van deze opties is te vinden door naar uw toepassing te gaan in 'Enterprise Applications' en de pagina **Eenmalig aanmelden** in het linkermenu te openen. Houd er rekening mee dat als uw toepassing is gemaakt in de oude portal, u mogelijk niet al deze opties ziet.

Op deze pagina ziet u ook een extra aanmeldingsoptie: Linked Sign-On. Deze optie wordt ook ondersteund door Application Proxy. Deze optie voegt echter geen enkele aanmelding toe aan de toepassing. Dat gezegd hebbende, kan de toepassing al eenmalige aanmelding hebben geïmplementeerd met behulp van een andere service, zoals Active Directory Federation Services. 

Met deze optie kan een beheerder een koppeling maken naar een toepassing waarop gebruikers eerst worden ingeschakeld bij het openen van de toepassing. Als er bijvoorbeeld een toepassing is die is geconfigureerd om gebruikers te verifiëren met Active Directory Federation Services 2.0, kan een beheerder de optie 'Gekoppeld aanmelden' gebruiken om er een koppeling naar te maken in het toegangspaneel.

## <a name="next-steps"></a>Volgende stappen
- [Wachtwoordkluisvoor eenmalige aanmelding met toepassingsproxy](application-proxy-configure-single-sign-on-password-vaulting.md)
- [Kerberos Constrained Delegation voor eenmalige aanmelding met application proxy](application-proxy-configure-single-sign-on-with-kcd.md)
- [Verificatie op basis van kopteksten voor één aanmelding met toepassingsproxy](application-proxy-configure-single-sign-on-with-ping-access.md) 
- [SAML voor eenmalige aanmelding met Application Proxy](application-proxy-configure-single-sign-on-on-premises-apps.md).
