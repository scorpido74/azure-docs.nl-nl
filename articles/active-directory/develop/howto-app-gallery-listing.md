---
title: Uw toepassing weer geven in de Azure Active Directory-toepassings galerie | Microsoft Docs
description: Meer informatie over het weer geven van een toepassing die eenmalige aanmelding in de app-galerie van Azure Active Directory ondersteunt
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/16/2019
ms.author: ryanwi
ms.reviewer: elisol, bryanla
ms.custom: aaddev, seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: c77657101f5cd8a117b2163386f6d551b7985458
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72374075"
---
# <a name="list-your-application-in-the-azure-active-directory-application-gallery"></a>Uw toepassing weergeven in de Azure Active Directory-toepassingsgalerie

In dit artikel wordt beschreven hoe u een toepassing kunt weer geven in de toepassings galerie van Azure Active Directory (Azure AD), eenmalige aanmelding (SSO) implementeert en de vermelding beheert.

## <a name="what-is-the-azure-ad-application-gallery"></a>Wat is de Azure AD-toepassings galerie?

- Klanten vinden de best mogelijke eenmalige aanmelding.
- De configuratie van de toepassing is eenvoudig en mini maal.
- Met een snelle zoek opdracht vindt u uw toepassing in de galerie.
- Gratis, Basic en Premium Azure AD-klanten kunnen deze integratie gebruiken.
- Wederzijdse klanten krijgen een stapsgewijze zelf studie voor configuratie.
- Klanten die het systeem voor Cross-Domain Identity Management ([scim](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/Provisioning-with-SCIM-getting-started/ba-p/880010)) gebruiken, kunnen inrichten voor dezelfde app gebruiken.

## <a name="prerequisites"></a>Vereisten

- Voor federatieve toepassingen (Open-ID en SAML/WS-voeder) moet de toepassing het SaaS-model (Software-as-a-Service) ondersteunen om te worden weer gegeven in de Azure AD-App-galerie. De Enter prise Gallery-toepassingen moeten ondersteuning bieden voor meerdere klant configuraties en niet voor een specifieke klant.
- Voor Open ID Connect moet de toepassing meerdere tenants zijn en moet het [Azure AD toestemmings raamwerk](consent-framework.md) goed worden geïmplementeerd voor de toepassing. De gebruiker kan de aanmeldings aanvraag naar een gemeen schappelijk eind punt verzenden, zodat elke klant toestemming kan geven voor de toepassing. U kunt de gebruikers toegang beheren op basis van de Tenant-ID en de UPN van de gebruiker die in het token is ontvangen.
- Voor SAML 2.0/WS-voeder moet uw toepassing de mogelijkheid hebben om de integratie van SAML/WS-invoerende SSO in de SP-of IDP-modus te kunnen uitvoeren. Zorg ervoor dat deze functie correct werkt voordat u de aanvraag indient.
- Zorg ervoor dat uw toepassing ondersteuning biedt voor formulier verificatie, zodat wachtwoord kluizen kan worden uitgevoerd om eenmalige aanmelding te kunnen werken.
- U hebt een permanent account nodig om te testen met ten minste twee gebruikers die zijn geregistreerd.

## <a name="submit-the-request-in-the-portal"></a>De aanvraag verzenden in de portal

Nadat u hebt gecontroleerd of uw toepassings integratie werkt met Azure AD, moet u uw verzoek om toegang verzenden in de portal voor het [toepassings netwerk](https://microsoft.sharepoint.com/teams/apponboarding/Apps). Als u een Office 365-account hebt, kunt u dit gebruiken om u aan te melden bij deze portal. Als dat niet het geval is, gebruikt u uw Microsoft-account, zoals Outlook of Hotmail, om u aan te melden.

Als de volgende pagina wordt weer gegeven nadat u zich hebt aangemeld, neemt u contact op met het [Azure AD SSO-integratie team](<mailto:SaaSApplicationIntegrations@service.microsoft.com>). Geef het e-mail account op dat u wilt gebruiken voor het verzenden van de aanvraag. Het team van Azure AD voegt het account toe aan de portal van het micro soft-toepassings netwerk.

![Bericht over toegangs aanvraag in share Point Portal](./media/howto-app-gallery-listing/errorimage.png)

Nadat het account is toegevoegd, kunt u zich aanmelden bij de portal van het micro soft-toepassings netwerk.

Als de volgende pagina wordt weer gegeven nadat u zich hebt aangemeld, moet u een zakelijke reden opgeven voor toegang tot het tekstvak. Selecteer vervolgens **toegang aanvragen**.

  ![Vak zakelijke rechtvaardiging in share Point Portal](./media/howto-app-gallery-listing/accessrequest.png)

Ons team bekijkt de details en geeft u de juiste toegang. Nadat uw aanvraag is goedgekeurd, kunt u zich aanmelden bij de portal en de aanvraag indienen door de tegel **aanvraag indienen (ISV)** op de start pagina te selecteren.

![Tegel Request (ISV) verzenden op Start pagina](./media/howto-app-gallery-listing/homepage.png)

> [!NOTE]
> Als u problemen hebt met Access, neemt u contact op met het [Azure AD SSO-integratie team](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

## <a name="implement-sso-by-using-the-federation-protocol"></a>SSO implementeren met behulp van het Federation-Protocol

Als u een toepassing in de Azure AD-App-galerie wilt weer geven, moet u eerst een van de volgende Federatie protocollen implementeren die door Azure AD worden ondersteund. U moet ook akkoord gaan met de voor waarden van de Azure AD-toepassings galerie. Lees de voor waarden van de Azure AD-toepassings galerie op [deze website](https://azure.microsoft.com/support/legal/active-directory-app-gallery-terms/).

- **OpenID Connect Connect**: als u uw toepassing wilt integreren met Azure AD met behulp van het Open-ID Connect-protocol, volgt u de instructies van de [ontwikkel aars](v1-authentication-scenarios.md).

    ![Een OpenID Connect Connect-toepassing in de galerie weer geven](./media/howto-app-gallery-listing/openid.png)

    * Als u uw toepassing wilt toevoegen aan de lijst in de galerie met behulp van OpenID Connect Connect, selecteert u **OpenID Connect connect & OAuth 2,0** , zoals wordt weer gegeven.
    * Als u problemen hebt met Access, neemt u contact op met het [Azure AD SSO-integratie team](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

- **Saml 2,0** of **WS-voeder**: als uw app SAML 2,0 ondersteunt, kunt u deze rechtstreeks integreren met een Azure AD-Tenant door de [instructies te volgen om een aangepaste toepassing toe te voegen](../active-directory-saas-custom-apps.md).

  ![Een SAML 2,0-of WS--toepassing in de galerie weer geven](./media/howto-app-gallery-listing/saml.png)

  * Als u uw toepassing wilt toevoegen aan de lijst in de galerie met behulp van **saml 2,0** of **WS-voeder**, selecteert u **SAML 2.0/WS-wordt gevoederd** zoals weer gegeven.
  * Als u problemen hebt met Access, neemt u contact op met het [Azure AD SSO-integratie team](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

## <a name="implement-sso-by-using-the-password-sso"></a>SSO implementeren met behulp van de SSO van het wacht woord

Maak een webtoepassing met een HTML-aanmeldings pagina voor het configureren [van eenmalige aanmelding op basis van wacht woorden](../manage-apps/what-is-single-sign-on.md). Met SSO op basis van wacht woorden, ook wel wachtwoord kluizen genoemd, kunt u gebruikers toegang en-wacht woorden beheren voor webtoepassingen die geen identiteits Federatie ondersteunen. Het is ook handig voor scenario's waarin meerdere gebruikers één account moeten delen, zoals de accounts voor de sociale media-app van uw organisatie.

![Een SSO-toepassing voor wacht woorden in de galerie weer geven](./media/howto-app-gallery-listing/passwordsso.png)

* Als u uw toepassing wilt toevoegen aan de lijst in de galerie met behulp van wacht woord-SSO, selecteert u **SSO van wacht woord** , zoals wordt weer gegeven.
* Als u problemen hebt met Access, neemt u contact op met het [Azure AD SSO-integratie team](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

## <a name="request-for-user-provisioning"></a>Aanvraag voor gebruikers inrichting

Volg het proces dat wordt weer gegeven in de volgende afbeelding om het inrichten van gebruikers aan te vragen.

   ![Aanvraag voor gebruikers inrichting](./media/howto-app-gallery-listing/user-provisioning.png)

## <a name="update-or-remove-an-existing-listing"></a>Een bestaande vermelding bijwerken of verwijderen

Als u een bestaande toepassing wilt bijwerken of verwijderen in de app-galerie van Azure AD, moet u de aanvraag eerst indienen in de portal voor het [toepassings netwerk](https://microsoft.sharepoint.com/teams/apponboarding/Apps). Als u een Office 365-account hebt, kunt u dit gebruiken om u aan te melden bij deze portal. Als dat niet het geval is, gebruikt u uw Microsoft-account, zoals Outlook of Hotmail, om u aan te melden.

- Selecteer de gewenste optie, zoals wordt weer gegeven in de volgende afbeelding.

    ![Een SAML-toepassing in de galerie weer geven](./media/howto-app-gallery-listing/updateorremove.png)

    * Als u een bestaande toepassing wilt bijwerken, selecteert u de gewenste optie volgens uw vereiste.
    * Als u een bestaande toepassing uit de Azure AD-App-galerie wilt verwijderen, selecteert u **mijn toepassings vermelding verwijderen in de galerie**.
    * Als u problemen hebt met Access, neemt u contact op met het [Azure AD SSO-integratie team](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

## <a name="list-requests-by-customers"></a>Aanvragen van klanten weer geven

Klanten kunnen een aanvraag indienen om een toepassing weer te geven door **app-aanvragen per klant**te selecteren  > **nieuwe aanvraag**in te dienen.

![Toont de tegel door de klant aangevraagde apps](./media/howto-app-gallery-listing/customer-submit-request.png)

Hier volgt de stroom van door de klant aangevraagde toepassingen.

![Toont de stroom van de door de klant aangevraagde apps](./media/howto-app-gallery-listing/customer-request.png)

## <a name="timelines"></a>Tijd lijnen

De tijd lijn voor het proces van het weer geven van een SAML 2,0-of WS-set-toepassing in de galerie is 7 tot 10 werk dagen.

  ![Tijd lijn voor het weer geven van een SAML-toepassing in de galerie](./media/howto-app-gallery-listing/timeline.png)

De tijd lijn voor het proces van het weer geven van een OpenID Connect Connect-toepassing in de galerie is 2 tot 5 werk dagen.

  ![Tijd lijn voor het weer geven van een OpenID Connect Connect-toepassing in de galerie](./media/howto-app-gallery-listing/timeline2.png)

## <a name="escalations"></a>Escalaties

Voor eventuele escalaties verzendt u een e-mail naar het [Azure AD SSO-integratie team](mailto:SaaSApplicationIntegrations@service.microsoft.com) op SaaSApplicationIntegrations@service.microsoft.com, waarna we zo snel mogelijk reageren.
