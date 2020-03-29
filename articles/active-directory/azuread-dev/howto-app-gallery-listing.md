---
title: Uw app weergeven in de Azure AD-toepassingsgalerie | Microsoft Documenten
description: Meer informatie over het weergeven van een toepassing die eenmalige aanmelding ondersteunt in de Azure Active Directory-app-galerie
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.topic: conceptual
ms.workload: identity
ms.date: 12/06/2019
ms.author: ryanwi
ms.reviewer: jeedes
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 969193b2c0843c4eb217d2bdb9f9ad9a40ccf9af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154964"
---
# <a name="list-your-application-in-the-azure-active-directory-application-gallery"></a>Uw toepassing weergeven in de Azure Active Directory-toepassingsgalerie

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

In dit artikel ziet u hoe u een toepassing aanbieden in de azure active directory -toepassingsgalerie (Azure AD), eenmalige aanmelding (SSO) implementeren en de vermelding beheren.

## <a name="what-is-the-azure-ad-application-gallery"></a>Wat is de Azure AD-toepassingsgalerie?

- Klanten vinden de best mogelijke single sign-on ervaring.
- Configuratie van de applicatie is eenvoudig en minimaal.
- Bij een snelle zoekopdracht vindt u uw toepassing in de galerie.
- Gratis, Basic- en Premium Azure AD-klanten kunnen deze integratie allemaal gebruiken.
- Wederzijdse klanten krijgen een stapsgewijze configuratie-zelfstudie.
- Klanten die het Systeem voor Cross-domain Identity Management[(SCIM)](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/Provisioning-with-SCIM-getting-started/ba-p/880010)gebruiken, kunnen inrichten voor dezelfde app gebruiken.

## <a name="prerequisites"></a>Vereisten

- Voor federatieve toepassingen (Open ID en SAML/WS-Fed) moet de toepassing het saas-model (software-as-a-service) ondersteunen om te worden weergegeven in de azure AD-app-galerie. De enterprise gallery-toepassingen moeten meerdere klantconfiguraties ondersteunen en geen specifieke klant.
- Voor Open ID Connect moet de toepassing multitenant zijn en moet het [Azure AD-toestemmingskader](../develop/consent-framework.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) correct zijn geïmplementeerd voor de toepassing. De gebruiker kan de aanmeldingsaanvraag naar een gemeenschappelijk eindpunt sturen, zodat elke klant toestemming kan geven voor de toepassing. U de gebruikerstoegang beheren op basis van de tenant-id en de UPN van de gebruiker die in het token is ontvangen.
- Voor SAML 2.0/WS-Fed moet uw toepassing de mogelijkheid hebben om de SAML/WS-Fed SSO-integratie in sp- of IDP-modus uit te voeren. Zorg ervoor dat deze mogelijkheid correct werkt voordat u de aanvraag indient.
- Voor wachtwoord SSO moet u ervoor zorgen dat uw toepassing formulierverificatie ondersteunt, zodat wachtwoordkluising kan worden gedaan om eenmalige aanmelding te laten werken zoals verwacht.
- Je hebt een permanent account nodig voor het testen met ten minste twee geregistreerde gebruikers.

**Hoe krijg je Azure AD voor ontwikkelaars?**

U een gratis testaccount krijgen met alle premium Azure AD-functies - 90 dagen gratis en kan worden verlengd zolang u er dev mee werkt:https://docs.microsoft.com/office/developer-program/office-365-developer-program

## <a name="submit-the-request-in-the-portal"></a>Dien de aanvraag in in het portaal

Nadat u hebt getest of uw toepassingsintegratie werkt met Azure AD, dient u uw verzoek om toegang in de [portal voor toepassingsnetwerk](https://microsoft.sharepoint.com/teams/apponboarding/Apps)in. Als u een Office 365-account hebt, gebruikt u deze om u aan te melden bij deze portal. Als dit niet het zo is, gebruikt u uw Microsoft-account, zoals Outlook of Hotmail, om u aan te melden.

Als de volgende pagina wordt weergegeven nadat u zich hebt aangemeld, neemt u contact op met het [Azure AD SSO-integratieteam](<mailto:SaaSApplicationIntegrations@service.microsoft.com>). Geef het e-mailaccount op dat u wilt gebruiken voor het indienen van de aanvraag. Het Azure AD-team voegt het account toe in de Portal van het Microsoft Application Network.

![Bericht voor toegangsaanvragen op SharePoint-portal](./media/howto-app-gallery-listing/errorimage.png)

Nadat het account is toegevoegd, u zich aanmelden bij de Portal van het Microsoft Application Network.

Als de volgende pagina wordt weergegeven nadat u zich hebt aangemeld, geeft u een zakelijke rechtvaardiging voor het nodig hebben van toegang in het tekstvak. Selecteer vervolgens **Toegang aanvragen**.

  ![Vak Ondernemingsrechtvaardiging op SharePoint-portal](./media/howto-app-gallery-listing/accessrequest.png)

Ons team beoordeelt de details en geeft u dienovereenkomstig toegang. Nadat uw aanvraag is goedgekeurd, u zich aanmelden bij de portal en de aanvraag indienen door de tegel **Verzoek verzenden (ISV)** op de startpagina te selecteren.

![Tegel Aanvraag (ISV) verzenden op startpagina](./media/howto-app-gallery-listing/homepage.png)

## <a name="issues-on-logging-into-portal"></a>Problemen bij het inloggen op portal

Als u deze fout ziet tijdens het inloggen dan hier zijn de details over het probleem en dit is hoe je het oplossen.

* Als uw aanmelding is geblokkeerd, zoals hieronder wordt weergegeven:

  ![problemen met het oplossen van toepassing in de galerie](./media/howto-app-gallery-listing/blocked.png)

**Wat gebeurt er:**

De gastgebruiker wordt gefedeerd naar een thuistenant die ook een Azure AD is. De gastgebruiker loopt een hoog risico. Microsoft staat gebruikers met een hoog risico geen toegang toe om toegang te krijgen tot de bronnen. Alle gebruikers met een hoog risico (werknemers of gasten/leveranciers) moeten hun risico herstellen / sluiten om toegang te krijgen tot Microsoft-bronnen. Voor gastgebruikers is dit gebruikersrisico afkomstig van de thuistenant en het beleid komt van de resourcetenant (Microsoft in dit geval).
 
**Veilige oplossingen:**

* MFA geregistreerde gastgebruikers saneren hun eigen gebruikersrisico. Dit kan worden gedaan door de gastgebruiker hethttps://aka.ms/sspr) uitvoeren van een beveiligde wachtwoord te wijzigen of opnieuw (bij hun huis huurder (dit moet MFA en SSPR bij de woning huurder). De beveiligde wachtwoordwijziging of -reset moet worden gestart op Azure AD en niet on-prem.

* Gastgebruikers hebben hun beheerders hun risico te verzien. In dit geval voert de beheerder een wachtwoordreset uit (tijdelijke wachtwoordgeneratie). Dit heeft geen identiteitsbescherming nodig. De beheerder van de gastgebruiker kan naar https://aka.ms/RiskyUsers 'Wachtwoord opnieuw instellen' klikken.

* Gastgebruikers hebben hun admins dicht / ontslaan hun risico. Nogmaals, dit heeft geen identiteitsbescherming nodig. De beheerder kan https://aka.ms/RiskyUsers naar toe gaan en op 'Gebruikersrisico afwijzen' klikken. Echter, de admin moet de due diligence doen om ervoor te zorgen dat dit een vals-positieve risicobeoordeling was voordat het gebruikersrisico werd gesloten. Anders brengen ze de middelen van hun en Microsoft in gevaar door een risicobeoordeling zonder onderzoek te onderdrukken.

> [!NOTE]
> Als u problemen hebt met de toegang, neemt u contact op met het [Azure AD SSO Integration Team](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

## <a name="implement-sso-by-using-the-federation-protocol"></a>SSO implementeren met behulp van het federatieprotocol

Als u een toepassing wilt aanbieden in de azure AD-app-galerie, moet u eerst een van de volgende federatieprotocollen implementeren die worden ondersteund door Azure AD. U moet ook akkoord gaan met de algemene voorwaarden van de Azure AD-toepassingsgalerie. Lees de algemene voorwaarden van de Azure AD-toepassingsgalerie op [deze website](https://azure.microsoft.com/support/legal/active-directory-app-gallery-terms/).

- **OpenID Connect**: Als u uw toepassing wilt integreren met Azure AD met behulp van het Open ID [Connect-protocol,](v1-authentication-scenarios.md)volgt u de instructies van de ontwikkelaars.

    ![Een OpenID Connect-toepassing in de galerie aanbieden](./media/howto-app-gallery-listing/openid.png)

    * Als u uw toepassing wilt toevoegen aan de lijst in de galerie met OpenID Connect, selecteert u **OpenID Connect & OAuth 2.0** zoals weergegeven.
    * Als u problemen hebt met de toegang, neemt u contact op met het [Azure AD SSO Integration Team](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

- **SAML 2.0** of **WS-Fed:** Als uw app SAML 2.0 ondersteunt, u deze rechtstreeks integreren met een Azure AD-tenant door de instructies te volgen [om een aangepaste toepassing toe te voegen.](../active-directory-saas-custom-apps.md)

  ![Een SAML 2.0- of WS-Fed-toepassing in de galerie aanbieden](./media/howto-app-gallery-listing/saml.png)

  * Als u uw toepassing wilt toevoegen aan de lijst in de galerie met **SAML 2.0** of **WS-Fed,** selecteert u **SAML 2.0/WS-Fed** zoals weergegeven.

  * Als u problemen hebt met de toegang, neemt u contact op met het [Azure AD SSO Integration Team](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

## <a name="implement-sso-by-using-the-password-sso"></a>SSO implementeren met het wachtwoord SSO

Maak een webtoepassing met een HTML-aanmeldingspagina om [een enkele aanmelding op basis van een wachtwoord](../manage-apps/what-is-single-sign-on.md)te configureren. Met sso op basis van wachtwoorden, ook wel wachtwoordkluis genoemd, u gebruikerstoegang en wachtwoorden beheren voor webtoepassingen die identiteitsfederatie niet ondersteunen. Het is ook handig voor scenario's waarin meerdere gebruikers één account moeten delen, zoals de sociale media-app-accounts van uw organisatie.

![Een SSO-toepassing voor wachtwoorden in de galerie vermelden](./media/howto-app-gallery-listing/passwordsso.png)

* Als u uw toepassing wilt toevoegen aan de lijst in de galerie met wachtwoord SSO, selecteert u **Wachtwoord SSO** zoals weergegeven.
* Als u problemen hebt met de toegang, neemt u contact op met het [Azure AD SSO Integration Team](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

## <a name="request-for-user-provisioning"></a>Aanvraag voor gebruikersinrichting

Volg het proces in de volgende afbeelding om gebruikersinrichting aan te vragen.

   ![Aanvraag voor gebruikersinrichting](./media/howto-app-gallery-listing/user-provisioning.png)

## <a name="update-or-remove-an-existing-listing"></a>Een bestaande aanbieding bijwerken of verwijderen

Als u een bestaande toepassing wilt bijwerken of verwijderen in de azure AD-app-galerie, moet u de aanvraag eerst indienen in de [portal van het toepassingsnetwerk.](https://microsoft.sharepoint.com/teams/apponboarding/Apps) Als u een Office 365-account hebt, gebruikt u deze om u aan te melden bij deze portal. Als dit niet het zo is, gebruikt u uw Microsoft-account, zoals Outlook of Hotmail, om u aan te melden.

- Selecteer de juiste optie zoals weergegeven in de volgende afbeelding.

    ![Een SAML-toepassing in de galerie aanbieden](./media/howto-app-gallery-listing/updateorremove.png)

    * Als u een bestaande toepassing wilt bijwerken, selecteert u de juiste optie volgens uw vereiste.
    * Als u een bestaande toepassing wilt verwijderen uit de azure AD-app-galerie, selecteert u **Mijn toepassingsvermelding verwijderen uit de galerie**.
    * Als u problemen hebt met de toegang, neemt u contact op met het [Azure AD SSO Integration Team](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

## <a name="list-requests-by-customers"></a>Lijstaanvragen van klanten

Klanten kunnen een aanvraag indienen om een aanvraag aan te bieden door **app-aanvragen van klanten** > te selecteren**Dien een nieuwe aanvraag in.**

![Toont de tegel met door de klant aangevraagde apps](./media/howto-app-gallery-listing/customer-submit-request.png)

Hier is de stroom van door de klant aangevraagde toepassingen.

![Toont de door de klant gevraagde appsstroom](./media/howto-app-gallery-listing/customerrequest.png)

## <a name="timelines"></a>Tijdlijnen

De tijdlijn voor het proces van het aanbieden van een SAML 2.0 of WS-Fed applicatie in de galerij is 7 tot 10 werkdagen.

  ![Tijdlijn voor het aanbieden van een SAML-toepassing in de galerie](./media/howto-app-gallery-listing/timeline.png)

De tijdlijn voor het proces van het aanbieden van een OpenID Connect-toepassing in de galerie is 2 tot 5 werkdagen.

  ![Tijdlijn voor het aanbieden van een OpenID Connect-toepassing in de galerie](./media/howto-app-gallery-listing/timeline2.png)

## <a name="escalations"></a>Escalaties

Voor escalaties stuurt u e-mail naar het [Azure AD SSO Integration Team](mailto:SaaSApplicationIntegrations@service.microsoft.com) op SaaSApplicationIntegrations@service.microsoft.com, en we reageren zo snel mogelijk.
