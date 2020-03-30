---
title: Veelgestelde vragen (FAQ) voor Azure Active Directory B2C
description: Antwoorden op veelgestelde vragen over Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 40285c811cd6f407c20c40bf3a90ec5b779a9c18
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264399"
---
# <a name="azure-ad-b2c-frequently-asked-questions-faq"></a>Azure AD B2C: veelgestelde vragen (FAQ)

Op deze pagina worden veelgestelde vragen beantwoord over de Azure Active Directory B2C (Azure AD B2C). Blijf terugkomen op updates.

### <a name="why-cant-i-access-the-azure-ad-b2c-extension-in-the-azure-portal"></a>Waarom heb ik geen toegang tot de Azure AD B2C-extensie in de Azure-portal?

Er zijn twee veelvoorkomende redenen waarom de Azure AD-extensie niet voor u werkt. Azure AD B2C vereist dat uw gebruikersrol in de map algemeen beheerder is. Neem contact op met uw beheerder als u denkt dat u toegang moet hebben. Als u algemene beheerdersrechten hebt, controleert u of u zich in een Azure AD B2C-map bevindt en niet in een Azure Active Directory. U instructies zien voor [het maken van een Azure AD B2C-tenant.](tutorial-create-tenant.md)

### <a name="can-i-use-azure-ad-b2c-features-in-my-existing-employee-based-azure-ad-tenant"></a>Kan ik Azure AD B2C-functies gebruiken in mijn bestaande Azure AD-tenant in gebruik?

Azure AD en Azure AD B2C zijn afzonderlijke productaanbiedingen en kunnen niet naast elkaar bestaan in dezelfde tenant. Een Azure AD-tenant vertegenwoordigt een organisatie. Een Azure AD B2C-tenant vertegenwoordigt een verzameling identiteiten die moeten worden gebruikt bij toepassingen van relying party.A Azure AD B2C tenant represents a collection of identities to be used with relying party applications. Door **nieuwe OpenID Connect-provider** toe te voegen onder **Azure AD B2C-> Identiteitsproviders** of met aangepast beleid, kan Azure AD B2C worden gekoppeld aan Azure AD waarmee verificatie van werknemers in een organisatie mogelijk is.

### <a name="can-i-use-azure-ad-b2c-to-provide-social-login-facebook-and-google-into-office-365"></a>Kan ik Azure AD B2C gebruiken om sociale aanmelding (Facebook en Google+) in Office 365 te verstrekken?

Azure AD B2C kan niet worden gebruikt om gebruikers te verifiëren voor Microsoft Office 365. Azure AD is de oplossing van Microsoft voor het beheren van de toegang van werknemers tot SaaS-apps en het heeft functies die voor dit doel zijn ontworpen, zoals licenties en voorwaardelijke toegang. Azure AD B2C biedt een platform voor identiteits- en toegangsbeheer voor het bouwen van web- en mobiele toepassingen. Wanneer Azure AD B2C is geconfigureerd om te worden gefedereerd naar een Azure AD-tenant, beheert de Azure AD-tenant de toegang van werknemers tot toepassingen die afhankelijk zijn van Azure AD B2C.

### <a name="what-are-local-accounts-in-azure-ad-b2c-how-are-they-different-from-work-or-school-accounts-in-azure-ad"></a>Wat zijn lokale accounts in Azure AD B2C? Waarin verschillen ze van werk- of schoolaccounts in Azure AD?

In een Azure AD-tenant worden gebruikers die behoren tot de `<xyz>@<tenant domain>`tenantaangemeld met een e-mailadres van het formulier . Het `<tenant domain>` is een van de geverifieerde domeinen `<...>.onmicrosoft.com` in de tenant of het oorspronkelijke domein. Dit type account is een werk- of schoolaccount.

In een Azure AD B2C-tenant willen de meeste apps dat de gebruiker joe@comcast.netzich bob@gmail.com sarah@contoso.comaanmeldt jim@live.commet een willekeurig e-mailadres (bijvoorbeeld , , of ). Dit type account is een lokaal account. We ondersteunen ook willekeurige gebruikersnamen als lokale accounts (bijvoorbeeld Joe, Bob, Sarah of Jim). U een van deze twee lokale accounttypen kiezen bij het configureren van identiteitsproviders voor Azure AD B2C in de Azure-portal. Selecteer in uw Azure AD B2C-tenant **Identiteitsproviders,** selecteer **Lokaal account**en selecteer **Gebruikersnaam**.

Gebruikersaccounts voor toepassingen kunnen worden gemaakt via een aanmeldingsgebruikersstroom, aanmeldingof aanmeldingsgebruikersstroom, de Microsoft Graph API of in de Azure-portal.

### <a name="which-social-identity-providers-do-you-support-now-which-ones-do-you-plan-to-support-in-the-future"></a>Welke social identity providers ondersteunt u nu? Welke bent u van plan te ondersteunen in de toekomst?

We ondersteunen momenteel verschillende aanbieders van sociale identiteit, waaronder Amazon, Facebook, GitHub (preview), Google, LinkedIn, Microsoft Account (MSA), QQ (preview), Twitter, WeChat (preview) en Weibo (preview). We evalueren het toevoegen van ondersteuning voor andere populaire aanbieders van sociale identiteit op basis van de vraag van de klant.

Azure AD B2C ondersteunt ook [aangepaste beleidsregels.](custom-policy-overview.md) Met aangepast beleid u uw eigen beleid maken voor elke identiteitsprovider die [OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) of SAML ondersteunt. Ga aan de slag met aangepast beleid door ons [aangepaste startpakket voor beleid te](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack)bekijken.

### <a name="can-i-configure-scopes-to-gather-more-information-about-consumers-from-various-social-identity-providers"></a>Kan ik scopes configureren om meer informatie over consumenten te verzamelen bij verschillende aanbieders van sociale identiteit?

Nee. De standaardscopes die worden gebruikt voor onze ondersteunde set aanbieders van sociale identiteit zijn:

* Facebook: e-mail
* Google+: e-mail
* Microsoft-account: openid-e-mailprofiel
* Amazon: profiel
* LinkedIn: r_emailaddress, r_basicprofile

### <a name="does-my-application-have-to-be-run-on-azure-for-it-work-with-azure-ad-b2c"></a>Moet mijn toepassing worden uitgevoerd op Azure omdat deze werkt met Azure AD B2C?

Nee, u uw toepassing overal hosten (in de cloud of on-premises). Het enige wat nodig is om te communiceren met Azure AD B2C is de mogelijkheid om HTTP-aanvragen te verzenden en te ontvangen op openbaar toegankelijke eindpunten.

### <a name="i-have-multiple-azure-ad-b2c-tenants-how-can-i-manage-them-on-the-azure-portal"></a>Ik heb meerdere Azure AD B2C-tenants. Hoe kan ik ze beheren op de Azure-portal?

Voordat u 'Azure AD B2C' opent in het linkermenu van de Azure-portal, moet u overschakelen naar de map die u wilt beheren. Schakel mappen door rechtsboven in de Azure-portal op uw identiteit te klikken en kies vervolgens een map in de vervolgkeuzelijst die wordt weergegeven.

### <a name="how-do-i-customize-verification-emails-the-content-and-the-from-field-sent-by-azure-ad-b2c"></a>Hoe pas ik verificatiee-mails (de inhoud en het veld 'Van:' aan die door Azure AD B2C zijn verzonden?

U de [functie bedrijfsbranding](../active-directory/fundamentals/customize-branding.md) gebruiken om de inhoud van verificatiee-mails aan te passen. In het bijzonder kunnen deze twee elementen van de e-mail worden aangepast:

* **Bannerlogo**: Rechtsonder afgebeeld.
* **Achtergrondkleur**: Bovenaan weergegeven.

    ![Schermafbeelding van een aangepaste verificatie-e-mail](./media/faq/company-branded-verification-email.png)

De e-mailhandtekening bevat de naam van de Azure AD B2C-tenant die u hebt opgegeven toen u de Azure AD B2C-tenant voor het eerst maakte. U de naam wijzigen met behulp van de volgende instructies:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/) als globale beheerder.
1. Open de blade **Azure Active Directory**.
1. Klik op het tabblad **Eigenschappen.**
1. Het veld **Naam** wijzigen.
1. Klik bovenaan de pagina op **Opslaan**.

Momenteel is er geen manier om het veld 'Van:' op de e-mail te wijzigen.

### <a name="how-can-i-migrate-my-existing-user-names-passwords-and-profiles-from-my-database-to-azure-ad-b2c"></a>Hoe kan ik mijn bestaande gebruikersnamen, wachtwoorden en profielen migreren vanuit mijn database naar Azure AD B2C?

U de Microsoft Graph API gebruiken om uw migratietool te schrijven. Zie de [gebruikersmigratiehandleiding](user-migration.md) voor meer informatie.

### <a name="what-password-user-flow-is-used-for-local-accounts-in-azure-ad-b2c"></a>Welke wachtwoordgebruikersstroom wordt gebruikt voor lokale accounts in Azure AD B2C?

De gebruikersstroom voor Azure AD B2C-wachtwoord voor lokale accounts is gebaseerd op het beleid voor Azure AD. De aanmeldings-, aanmeldings- of aanmeldings- en aanmeldings- en wachtwoordreset-gebruikersstromen van Azure AD B2C gebruiken de kracht van het "sterke" wachtwoord en verlopen geen wachtwoorden. Zie [Wachtwoordbeleid en -beperkingen in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy)voor meer informatie.

Zie [Bedreigingen voor resources en gegevens beheren in Azure Active Directory B2C voor](threat-management.md)informatie over accountuitsluitingen en wachtwoorden.

### <a name="can-i-use-azure-ad-connect-to-migrate-consumer-identities-that-are-stored-on-my-on-premises-active-directory-to-azure-ad-b2c"></a>Kan ik Azure AD Connect gebruiken om consumentenidentiteiten te migreren die zijn opgeslagen in mijn on-premises Active Directory naar Azure AD B2C?

Nee, Azure AD Connect is niet ontworpen om te werken met Azure AD B2C. Overweeg de [Microsoft Graph API](manage-user-accounts-graph-api.md) te gebruiken voor gebruikersmigratie. Zie de [gebruikersmigratiehandleiding](user-migration.md) voor meer informatie.

### <a name="can-my-app-open-up-azure-ad-b2c-pages-within-an-iframe"></a>Kan mijn app Azure AD B2C-pagina's openen binnen een iFrame?

Nee, om veiligheidsredenen kunnen Azure AD B2C-pagina's niet worden geopend binnen een iFrame. Onze service communiceert met de browser om iFrames te verbieden. De beveiligingscommunity in het algemeen en de OAUTH2-specificatie raden u af om iFrames te gebruiken voor identiteitservaringen vanwege het risico van click-jacking.

### <a name="does-azure-ad-b2c-work-with-crm-systems-such-as-microsoft-dynamics"></a>Werkt Azure AD B2C met CRM-systemen zoals Microsoft Dynamics?

Integratie met Microsoft Dynamics 365 Portal is beschikbaar. Zie [Dynamics 365-portal configureren om Azure AD B2C te gebruiken voor verificatie](https://docs.microsoft.com/dynamics365/customer-engagement/portals/azure-ad-b2c).

### <a name="does-azure-ad-b2c-work-with-sharepoint-on-premises-2016-or-earlier"></a>Werkt Azure AD B2C met SharePoint on-premises 2016 of eerder?

Azure AD B2C is niet bedoeld voor het scenario voor het delen van externe partners in SharePoint. zie [Azure AD B2B](https://cloudblogs.microsoft.com/enterprisemobility/2015/09/15/learn-all-about-the-azure-ad-b2b-collaboration-preview/) in plaats daarvan.

### <a name="should-i-use-azure-ad-b2c-or-b2b-to-manage-external-identities"></a>Moet ik Azure AD B2C of B2B gebruiken om externe identiteiten te beheren?

Lees [B2B-samenwerking en B2C vergelijken in Azure AD](../active-directory/b2b/compare-with-b2c.md) voor meer informatie over het toepassen van de juiste functies op uw externe identiteitsscenario's.

### <a name="what-reporting-and-auditing-features-does-azure-ad-b2c-provide-are-they-the-same-as-in-azure-ad-premium"></a>Welke rapportage- en controlefuncties biedt Azure AD B2C? Zijn ze hetzelfde als in Azure AD Premium?

Nee, Azure AD B2C biedt geen ondersteuning voor dezelfde set rapporten als Azure AD Premium. Er zijn echter veel overeenkomsten:

* **Aanmeldingsrapporten** bieden een registratie van elke aanmelding met beperkte details.
* **Controlerapporten** bevatten zowel beheerdersactiviteiten als toepassingsactiviteit.
* **Gebruiksrapporten** omvatten het aantal gebruikers, het aantal aanmeldingen en het volume van MFA.

### <a name="can-i-localize-the-ui-of-pages-served-by-azure-ad-b2c-what-languages-are-supported"></a>Kan ik de gebruikersinterface van pagina's die worden bediend door Azure AD B2C lokaliseren? Welke talen worden ondersteund?

Ja, zie [taalaanpassing](user-flow-language-customization.md). Wij bieden vertalingen voor 36 talen en u elke tekenreeks overschrijven die aan uw behoeften voldoet.

### <a name="can-i-use-my-own-urls-on-my-sign-up-and-sign-in-pages-that-are-served-by-azure-ad-b2c-for-instance-can-i-change-the-url-from-contosob2clogincom-to-logincontosocom"></a>Kan ik mijn eigen URL's gebruiken op mijn aanmeldings- en aanmeldingspagina's die worden weergegeven door Azure AD B2C? Kan ik bijvoorbeeld de URL wijzigen van contoso.b2clogin.com naar login.contoso.com?

Momenteel niet. Deze functie staat op onze roadmap. Als u uw domein controleert op het tabblad **Domeinen** in de Azure-portal, wordt dit doel niet bereikt. Echter, met b2clogin.com bieden we een [neutraal top level domein,](b2clogin.md)en dus de externe verschijning kan worden geïmplementeerd zonder de vermelding van Microsoft.

### <a name="how-do-i-delete-my-azure-ad-b2c-tenant"></a>Hoe verwijder ik mijn Azure AD B2C-tenant?

Volg deze stappen om uw Azure AD B2C-tenant te verwijderen.

U gebruik maken van de huidige **toepassingen** ervaring of onze nieuwe unified **App registraties (Preview)** ervaring. [Meer informatie over de nieuwe ervaring](https://aka.ms/b2cappregintro).

#### <a name="applications"></a>[Toepassingen](#tab/applications/)

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/) als *abonnementsbeheerder*. Gebruik hetzelfde werk- of schoolaccount of hetzelfde Microsoft-account dat u hebt gebruikt om u aan te melden voor Azure.
1. Selecteer het **filter Directory + abonnement** in het bovenste menu en selecteer vervolgens de map met uw Azure AD B2C-tenant.
1. Selecteer Azure AD **B2C**in het linkermenu . Selecteer Ook **Alle services** en zoek naar Azure AD **B2C**en selecteer deze .
1. Verwijder alle **gebruikersstromen (beleidsregels)** in uw Azure AD B2C-tenant.
1. Verwijder alle **toepassingen die** u hebt geregistreerd in uw Azure AD B2C-tenant.
1. Selecteer **Azure Active Directory** in het linkermenu.
1. Onder **Beheren**, selecteer **Gebruikers**.
1. Selecteer op zijn beurt elke gebruiker (exclusief de gebruiker *van abonnementsbeheerder* die u momenteel bent aangemeld als). Selecteer **Verwijderen** onder aan de pagina en selecteer **JA** wanneer daarom wordt gevraagd.
1. Selecteer **onder Beheren** **app-registraties** (of **App-registraties (Legacy)**).
1. Alle **toepassingen weergeven selecteren**
1. Selecteer de toepassing met de naam **b2c-extensions-app**, selecteer **Verwijderen**en selecteer **Vervolgens Ja** wanneer u daarom wordt gevraagd.
1. Selecteer **onder Beheren**de optie **Gebruikersinstellingen**.
1. Als aanwezig, selecteert u onder **LinkedIn-accountverbindingen** **Nee**, selecteert u **Opslaan**.
1. Selecteer **Eigenschappen** **onder Beheren**
1. Selecteer onder **Toegangsbeheer voor Azure-resources****Ja** en selecteer vervolgens **Opslaan**.
1. Meld u af bij de Azure-portal en meld u vervolgens weer aan om uw toegang te vernieuwen.
1. Selecteer **Azure Active Directory** in het linkermenu.
1. Selecteer op de pagina **Overzicht** de optie **Map verwijderen**. Volg de instructies op het scherm om het proces te voltooien.

#### <a name="app-registrations-preview"></a>[App-registraties (voorbeeld)](#tab/app-reg-preview/)

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/) als *abonnementsbeheerder*. Gebruik hetzelfde werk- of schoolaccount of hetzelfde Microsoft-account dat u hebt gebruikt om u aan te melden voor Azure.
1. Selecteer het **filter Directory + abonnement** in het bovenste menu en selecteer vervolgens de map met uw Azure AD B2C-tenant.
1. Selecteer Azure AD **B2C**in het linkermenu . Selecteer Ook **Alle services** en zoek naar Azure AD **B2C**en selecteer deze .
1. Verwijder alle **gebruikersstromen (beleidsregels)** in uw Azure AD B2C-tenant.
1. Selecteer **App-registraties (Voorbeeld)** en selecteer vervolgens het tabblad **Alle toepassingen.**
1. Verwijder alle aanvragen die u hebt geregistreerd.
1. Verwijder de **b2c-extensies-app**.
1. Onder **Beheren**, selecteer **Gebruikers**.
1. Selecteer op zijn beurt elke gebruiker (exclusief de gebruiker *van abonnementsbeheerder* die u momenteel bent aangemeld als). Selecteer **Verwijderen** onder aan de pagina en selecteer **Ja** wanneer daarom wordt gevraagd.
1. Selecteer **Azure Active Directory** in het linkermenu.
1. Selecteer **onder Beheren**de optie **Gebruikersinstellingen**.
1. Als aanwezig, selecteert u onder **LinkedIn-accountverbindingen** **Nee**, selecteert u **Opslaan**.
1. Selecteer **Eigenschappen** **onder Beheren**
1. Selecteer onder **Toegangsbeheer voor Azure-resources****Ja** en selecteer vervolgens **Opslaan**.
1. Meld u af bij de Azure-portal en meld u vervolgens weer aan om uw toegang te vernieuwen.
1. Selecteer **Azure Active Directory** in het linkermenu.
1. Selecteer op de pagina **Overzicht** de optie **Map verwijderen**. Volg de instructies op het scherm om het proces te voltooien.

* * *

### <a name="can-i-get-azure-ad-b2c-as-part-of-enterprise-mobility-suite"></a>Kan ik Azure AD B2C krijgen als onderdeel van Enterprise Mobility Suite?

Nee, Azure AD B2C is een Azure-service voor betalen per gebruik en maakt geen deel uit van Enterprise Mobility Suite.

### <a name="how-do-i-report-issues-with-azure-ad-b2c"></a>Hoe rapporteer ik problemen met Azure AD B2C?

Zie [Bestandsondersteuningsaanvragen voor Azure Active Directory B2C](support-options.md).
