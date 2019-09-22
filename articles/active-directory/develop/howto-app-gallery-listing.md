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
ms.openlocfilehash: 88d74fe794f4de95b7ba8b0dd5575ca56d2016e5
ms.sourcegitcommit: 83df2aed7cafb493b36d93b1699d24f36c1daa45
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2019
ms.locfileid: "71176952"
---
# <a name="how-to-list-your-application-in-the-azure-active-directory-application-gallery"></a>Procedure: Uw toepassing weergeven in de Azure Active Directory-toepassingsgalerie

In dit artikel wordt beschreven hoe u een toepassing kunt weer geven in de Azure AD-toepassings galerie, eenmalige aanmelding (SSO) implementeert en de vermelding beheert.

## <a name="what-is-the-azure-ad-application-gallery"></a>Wat is de Azure AD-toepassings galerie?

- Klanten vinden de best mogelijke eenmalige aanmelding.
- De configuratie van de toepassing is eenvoudig en mini maal.
- Met een snelle zoek opdracht vindt u uw toepassing in de galerie.
- Gratis, Basic en Premium Azure AD-klanten kunnen deze integratie gebruiken.
- Wederzijdse klanten krijgen een stapsgewijze zelf studie voor configuratie.
- Klanten die SCIM gebruiken, kunnen het inrichten voor dezelfde app gebruiken.

## <a name="prerequisites"></a>Vereisten

- Voor federatieve toepassingen (Open ID en SAML/WS-voeder) moet de toepassing het SaaS-model ondersteunen om te worden weer gegeven in de Azure AD-galerie. De toepassingen in de bedrijfs galerie moeten ondersteuning bieden voor meerdere klant configuraties en niet voor een specifieke klant.

- Voor Open ID Connect moet de toepassing multi-tenanted en [Azure AD toestemmings raamwerk](consent-framework.md) goed worden geïmplementeerd voor de toepassing. De gebruiker kan de aanmeldings aanvraag verzenden naar een gemeen schappelijk eind punt, zodat elke klant toestemming kan geven voor de toepassing. U kunt de gebruikers toegang beheren op basis van de Tenant-ID en de UPN van de gebruiker die in het token is ontvangen.

- Voor SAML 2.0/WS-voeder moet uw toepassing de mogelijkheid hebben om de SSO-integratie met SAML/WS-invoering in de SP-of IDP-modus te kunnen uitvoeren. Controleer of dit correct werkt voordat u de aanvraag indient.

- Zorg ervoor dat uw toepassing ondersteuning biedt voor formulier verificatie zodat wachtwoord kluizen kan worden uitgevoerd voor het uitvoeren van eenmalige aanmelding.

- U hebt een permanent account nodig om te testen met ten minste twee gebruikers die zijn geregistreerd.

## <a name="submit-the-request-in-the-portal"></a>De aanvraag verzenden in de portal

Nadat u hebt gecontroleerd of uw toepassings integratie werkt met Azure AD, dient u uw aanvraag in voor toegang tot de portal van de [toepassings netwerk](https://microsoft.sharepoint.com/teams/apponboarding/Apps). Als u een Office 365-account hebt, kunt u dit gebruiken om u aan te melden bij deze portal. Als dat niet het geval is, gebruikt u uw Microsoft-account (zoals Outlook of Hotmail) om u aan te melden.

Als de volgende pagina wordt weer gegeven nadat u zich hebt aangemeld, neemt u contact op met het [Azure AD SSO-integratie team](<mailto:SaaSApplicationIntegrations@service.microsoft.com>) en geeft u het e-mail account op dat u wilt gebruiken voor het indienen van de aanvraag. Vervolgens voegt Azure AD team het account toe aan de portal van het micro soft-toepassings netwerk.

![Toegangs aanvraag in de share Point-Portal](./media/howto-app-gallery-listing/errorimage.png)

Zodra het account is toegevoegd, kunt u zich aanmelden bij de portal van het micro soft-toepassings netwerk.

En als de volgende pagina na het aanmelden wordt weer gegeven, moet u een zakelijke reden opgeven voor toegang tot het tekstvak en vervolgens **toegang aanvragen**selecteren.

  ![Toegangs aanvraag in de share Point-Portal](./media/howto-app-gallery-listing/accessrequest.png)

Ons team bekijkt de details en geeft u de juiste toegang. Zodra uw aanvraag is goedgekeurd, kunt u zich aanmelden bij de portal en de aanvraag indienen door te klikken op de tegel **aanvraag indienen (ISV)** op de start pagina.

![Start pagina van share Point Portal](./media/howto-app-gallery-listing/homepage.png)

> [!NOTE]
> Als u problemen hebt met de toegang, neemt u contact op met het [Azure AD SSO-integratie team](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

## <a name="implementing-sso-using-federation-protocol"></a>Eenmalige aanmelding implementeren met behulp van het federatieve Protocol

Als u een toepassing in de Azure AD-App-galerie wilt weer geven, moet u eerst een van de volgende Federatie protocollen implementeren die door Azure AD worden ondersteund en akkoord gaan met de voor waarden van de Azure AD-toepassings galerie. Lees [hier](https://azure.microsoft.com/support/legal/active-directory-app-gallery-terms/)de voor waarden van de Azure AD-toepassings galerie.

- **OpenID connect verbinding maken**: Volg de instructies van de [ontwikkel aars](authentication-scenarios.md)om uw toepassing te integreren met Azure AD met behulp van het Open ID Connect-protocol.

    ![Tijd lijn van de vermelding OpenID Connect Connect-toepassing in de galerie](./media/howto-app-gallery-listing/openid.png)

    * Als u uw toepassing wilt toevoegen aan de lijst in de galerie met behulp van OpenID Connect Connect, selecteert u **OpenID Connect connect & OAuth 2,0** als hierboven.
    * Als u problemen hebt met de toegang, neemt u contact op met het [Azure AD SSO-integratie team](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

- **SAML 2,0** of **WS-voeder**: Als uw app SAML 2,0 ondersteunt, kunt u deze rechtstreeks integreren met een Azure AD-Tenant met behulp van de [instructies voor het toevoegen van een aangepaste toepassing](../active-directory-saas-custom-apps.md).

  ![Tijd lijn van de vermelding van een SAML 2,0-of WS-toepassing in de galerie](./media/howto-app-gallery-listing/saml.png)

  * Als u uw toepassing wilt toevoegen aan de lijst in de galerie met behulp van **saml 2,0** of **WS-voeder**, selecteert u **SAML 2.0/WS-** net als hierboven.
  * Als u problemen hebt met de toegang, neemt u contact op met het [Azure AD SSO-integratie team](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

## <a name="implementing-sso-using-password-sso"></a>Eenmalige aanmelding implementeren met wacht woord-SSO

Maak een webtoepassing met een HTML-aanmeldings pagina voor het configureren [van eenmalige aanmelding op basis van wacht woorden](../manage-apps/what-is-single-sign-on.md). Met SSO op basis van wacht woorden, ook wel wachtwoord kluizen genoemd, kunt u gebruikers toegang en-wacht woorden beheren voor webtoepassingen die geen identiteits Federatie ondersteunen. Het is ook handig voor scenario's waarin meerdere gebruikers één account moeten delen, zoals de accounts voor de sociale media-app van uw organisatie.

![Tijd lijn van vermelding van de SSO-toepassing voor wacht woorden in de galerie](./media/howto-app-gallery-listing/passwordsso.png)

* Als u uw toepassing wilt toevoegen aan de lijst in de galerie met wacht woord-SSO, selecteert u **eenmalige aanmelding voor wacht woord** .
* Als u problemen hebt met de toegang, neemt u contact op met het [Azure AD SSO-integratie team](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

## <a name="requesting-for-user-provisioning"></a>Aanvragen voor gebruikers inrichting

Volg de onderstaande procedure om de gebruikers inrichting aan te vragen-

   ![Tijd lijn van de weer gave van SAML-toepassing in de galerie](./media/howto-app-gallery-listing/user-provisioning.png)

## <a name="updateremove-existing-listing"></a>Bestaande vermelding bijwerken/verwijderen

Als u een bestaande toepassing wilt bijwerken of verwijderen in de app-galerie van Azure AD, moet u de aanvraag eerst indienen in de portal voor het [toepassings netwerk](https://microsoft.sharepoint.com/teams/apponboarding/Apps). Als u een Office 365-account hebt, kunt u dit gebruiken om u aan te melden bij deze portal. Als dat niet het geval is, gebruikt u uw Microsoft-account (zoals Outlook of Hotmail) om u aan te melden.

- Selecteer de gewenste optie, zoals wordt weer gegeven in de volgende afbeelding:

    ![Tijd lijn van de weer gave van SAML-toepassing in de galerie](./media/howto-app-gallery-listing/updateorremove.png)

    * Als u een bestaande toepassing wilt bijwerken, selecteert u de gewenste optie volgens uw vereiste.
    * Als u een bestaande toepassing uit de Azure AD-galerie wilt verwijderen, selecteert u **mijn toepassings vermelding verwijderen in de galerie**.
    * Als u problemen hebt met de toegang, neemt u contact op met het [Azure AD SSO-integratie team](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

## <a name="listing-requests-by-customers"></a>Aanvragen voor klanten weer geven

Klanten kunnen een aanvraag indienen door te klikken op app- **aanvragen per klant** -> **nieuwe aanvraag indienen**.

![Toont de tegel klant aangevraagde apps](./media/howto-app-gallery-listing/customer-submit-request.png)

Hieronder volgt de stroom van door de klant aangevraagde toepassingen-

![Toont de door de klant aangevraagde apps stroom](./media/howto-app-gallery-listing/customer-request.png)

## <a name="timelines"></a>Tijd lijnen

De tijd lijn voor het proces van het weer geven van een SAML 2,0-of WS-gevoede toepassing in de galerie is 7-10 werk dagen.

  ![Tijd lijn van de weer gave van SAML-toepassing in de galerie](./media/howto-app-gallery-listing/timeline.png)

De tijd lijn voor het proces van het weer geven van een OpenID Connect Connect-toepassing in de galerie is 2-5 werk dagen.

  ![Tijd lijn van de weer gave van SAML-toepassing in de galerie](./media/howto-app-gallery-listing/timeline2.png)

## <a name="escalations"></a>Escalaties

Voor eventuele escalaties verzendt u een e-mail naar het [Azure AD SSO-integratie team](mailto:SaaSApplicationIntegrations@service.microsoft.com) . dit wordt SaaSApplicationIntegrations@service.microsoft.com zo snel mogelijk gereageerd.
