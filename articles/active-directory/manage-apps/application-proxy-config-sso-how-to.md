---
title: Eenmalige aanmelding configureren voor een toepassings proxy-app
description: Het configureren van eenmalige aanmelding voor uw toepassing proxytoepassing snel
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
ms.openlocfilehash: 8b5eca08e0b736937af42e58d81148636e3269df
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74275642"
---
# <a name="how-to-configure-single-sign-on-to-an-application-proxy-application"></a>Single sign-on bij een toepassing Application Proxy configureren

Eenmalige aanmelding (SSO) kunt uw gebruikers toegang krijgen tot een toepassing zonder verificatie van meerdere keren. Dit kunt de één verificatie kan worden uitgevoerd in de cloud, op basis van Azure Active Directory, en de service of de Connector om de gebruiker voor het voltooien van elke aanvullende verificatie uitdagingen van de toepassing te imiteren.

## <a name="how-to-configure-single-sign-on"></a>Het configureren van eenmalige aanmelding op
Zorg ervoor dat uw toepassing is geconfigureerd voor pre-authenticatie via Azure Active Directory voor het configureren van eenmalige aanmelding. Als u deze configuratie wilt uitvoeren, gaat u naar **Azure Active Directory** -&gt; **bedrijfs toepassingen** -&gt; **alle toepassingen** -uw toepassing &gt;-**toepassings proxy**.&gt; Op deze pagina kunt u zien van het veld 'Vooraf-verificatie' en zorg ervoor dat is ingesteld op 'Azure Active Directory. 

Zie stap 4 van het [app-publicatie document](application-proxy-add-on-premises-application.md)voor meer informatie over de pre-verificatie methoden.

   ![Methode voor verificatie vooraf in Azure portal](./media/application-proxy-config-sso-how-to/app-proxy.png)

## <a name="configuring-single-sign-on-modes-for-application-proxy-applications"></a>Modi voor eenmalige aanmelding configureren voor Application Proxy-toepassingen
Het specifieke type van eenmalige aanmelding configureren. De aanmeldings-methoden zijn ingedeeld op basis van de back-endtoepassing maakt gebruik van welk type verificatie. App-Proxy toepassingen ondersteuning bieden voor drie typen van aanmelding:

-   **Aanmelden op basis van wacht woorden**: aanmelden op basis van wacht woorden kan worden gebruikt voor elke toepassing die gebruikmaakt van gebruikers naam en wacht woord om zich aan te melden. Configuratie stappen zijn [een eenmalige aanmelding met een wacht woord configureren voor een toepassing in de Azure AD-galerie](configure-password-single-sign-on-gallery-applications.md).

-   **Geïntegreerde Windows-verificatie**: voor toepassingen die gebruikmaken van geïntegreerde Windows-authenticatie (IWA), wordt eenmalige aanmelding ingeschakeld via beperkte Kerberos-delegering (KCD). Deze methode biedt machtiging Toepassingsproxyconnectors in Active Directory te imiteren van gebruikers, en voor het verzenden en ontvangen van tokens in hun naam. Meer informatie over het configureren van KCD vindt u in de [eenmalige aanmelding met KCD-documentatie](application-proxy-configure-single-sign-on-with-kcd.md).

-   **Op headers gebaseerde aanmelding**: aanmelden op basis van een header is ingeschakeld via een partnerschap en vereist een aantal aanvullende configuratie. Zie de [PingAccess voor Azure AD-documentatie](application-proxy-configure-single-sign-on-with-ping-access.md)voor meer informatie over de samen werking en stapsgewijze instructies voor het configureren van eenmalige aanmelding voor een toepassing die gebruikmaakt van headers voor authenticatie.

-   **Eenmalige aanmelding via SAML**: met eenmalige aanmelding via SAML verifieert Azure AD bij de toepassing met behulp van het Azure ad-account van de gebruiker. Azure AD communiceert de informatie aanmelding voor de toepassing via een verbindingsprotocol. Met eenmalige aanmelding op basis van SAML kunt u gebruikers toewijzen aan specifieke toepassings rollen op basis van de regels die u in uw SAML-claims definieert. Zie voor meer informatie over het instellen van eenmalige SAML-aanmelding [SAML voor eenmalige aanmelding met toepassings proxy](application-proxy-configure-single-sign-on-on-premises-apps.md).

U kunt elk van deze opties vinden door naar uw toepassing te gaan in ' bedrijfs toepassingen ' en de pagina voor **eenmalige aanmelding** te openen in het menu links. Als uw toepassing is gemaakt in de oude Portal, ziet u mogelijk niet al deze opties.

Op deze pagina ook ziet u een extra optie voor eenmalige aanmelding: gekoppelde aanmelding. Deze optie wordt ook ondersteund door de toepassingsproxy. Deze optie wordt echter niet toegevoegd eenmalige aanmelding voor de toepassing. Dat gezegd dat de toepassing mogelijk al eenmalige aanmelding geïmplementeerd met behulp van een andere service, zoals Active Directory Federation Services. 

Deze optie kunt een beheerder een koppeling maken naar een toepassing die gebruikers eerste land op bij het openen van de toepassing. Bijvoorbeeld, als er een toepassing die is geconfigureerd voor het verifiëren van gebruikers met behulp van Active Directory Federation Services 2.0, kunt een beheerder de optie ' gekoppelde aanmelding ' gebruiken om te maken van een koppeling naar het in het toegangsvenster.

## <a name="next-steps"></a>Volgende stappen
- [Wachtwoord kluis voor eenmalige aanmelding met toepassings proxy](application-proxy-configure-single-sign-on-password-vaulting.md)
- [Beperkte Kerberos-overdracht voor eenmalige aanmelding met toepassings proxy](application-proxy-configure-single-sign-on-with-kcd.md)
- [Verificatie op basis van een header voor eenmalige aanmelding met toepassings proxy](application-proxy-configure-single-sign-on-with-ping-access.md) 
- [SAML voor eenmalige aanmelding met toepassings proxy](application-proxy-configure-single-sign-on-on-premises-apps.md).
