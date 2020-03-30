---
title: Veelgestelde vragen over B2B-samenwerking - Azure Active Directory | Microsoft Documenten
description: Krijg antwoorden op veelgestelde vragen over Azure Active Directory B2B-samenwerking.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: reference
ms.date: 03/19/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 92c3e0d77a26db406e24d6d2fa07e96349613634
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050821"
---
# <a name="azure-active-directory-b2b-collaboration-faqs"></a>Veelgestelde vragen over Azure Active Directory B2B-samenwerking

Deze veelgestelde vragen (veelgestelde vragen) over Azure Active Directory (Azure AD) business-to-business (B2B) worden periodiek bijgewerkt om nieuwe onderwerpen op te nemen.

   > [!IMPORTANT]
   > **Vanaf 31 maart 2021**ondersteunt Microsoft niet langer het inwisselen van uitnodigingen door onbeheerde Azure AD-accounts en tenants te maken voor B2B-samenwerkingsscenario's. Ter voorbereiding moedigen we klanten aan om te kiezen voor [eenmalige wachtwoordverificatie voor e-mail.](one-time-passcode.md) We zijn blij met uw feedback over deze openbare preview-functie en zijn verheugd om nog meer manieren te creëren om samen te werken.

### <a name="can-we-customize-our-sign-in-page-so-its-more-intuitive-for-our-b2b-collaboration-guest-users"></a>Kunnen we onze aanmeldingspagina aanpassen zodat deze intuïtiever is voor onze gastgebruikers van b2b-samenwerking?
Absoluut! Zie onze [blogpost over deze functie](https://blogs.technet.microsoft.com/enterprisemobility/2017/04/07/improving-the-branding-logic-of-azure-ad-login-pages/). Zie [Bedrijfsbranding toevoegen om u aan te melden en pagina's van het deelvenster Openen voor](../fundamentals/customize-branding.md)meer informatie over het aanpassen van de aanmeldingspagina's van uw organisatie.

### <a name="can-b2b-collaboration-users-access-sharepoint-online-and-onedrive"></a>Hebben B2B-samenwerkingsgebruikers toegang tot SharePoint Online en OneDrive?
Ja. De mogelijkheid om te zoeken naar bestaande gastgebruikers in SharePoint Online met behulp van de kiezer voor personen is echter standaard **uitgeschakeld.** Als u de optie wilt inschakelen om te zoeken naar bestaande gastgebruikers, stelt u **ShowPeoplePickerSuggestionsForGuestUsers** in **op Aan**. U deze instelling inschakelen op tenantniveau of op siteverzamelingsniveau. U deze instelling wijzigen met de cmdlets Set-SPOTenant en Set-SPOSite. Met deze cmdlets kunnen leden alle bestaande gastgebruikers in de directory doorzoeken. Wijzigingen in het tenantbereik hebben geen invloed op SharePoint Online-sites die al zijn ingericht.

### <a name="is-the-csv-upload-feature-still-supported"></a>Wordt de CSV-uploadfunctie nog steeds ondersteund?
Ja. Zie [dit PowerShell-voorbeeld](code-samples.md)voor meer informatie over het gebruik van de functie CSV-bestandsupload.

### <a name="how-can-i-customize-my-invitation-emails"></a>Hoe kan ik mijn uitnodigingse-mails aanpassen?
U bijna alles over het inviter-proces aanpassen met behulp van de [B2B-uitnodigings-API's.](customize-invitation-api.md)

### <a name="can-guest-users-reset-their-multi-factor-authentication-method"></a>Kunnen gastgebruikers hun multi-factor authenticatiemethode resetten?
Ja. Gastgebruikers kunnen hun multi-factor authenticatiemethode op dezelfde manier resetten als gewone gebruikers.

### <a name="which-organization-is-responsible-for-multi-factor-authentication-licenses"></a>Welke organisatie is verantwoordelijk voor multi-factor authenticatielicenties?
De uitnodigende organisatie voert multi-factor authenticatie uit. De uitnodigende organisatie moet ervoor zorgen dat de organisatie voldoende licenties heeft voor hun B2B-gebruikers die multi-factor authenticatie gebruiken.

### <a name="what-if-a-partner-organization-already-has-multi-factor-authentication-set-up-can-we-trust-their-multi-factor-authentication-and-not-use-our-own-multi-factor-authentication"></a>Wat gebeurt er als een partnerorganisatie al multi-factor authenticatie heeft ingesteld? Kunnen we vertrouwen op hun multi-factor authenticatie, en niet gebruik maken van onze eigen multi-factor authenticatie?
Deze functie wordt momenteel niet ondersteund. Als toegang tot de resources van uw organisatie multifactorauthenticatie vereist, moet de partnerorganisatie zich registreren voor meervoudige verificatie in uw (uitnodigende) organisatie.

### <a name="how-can-i-use-delayed-invitations"></a>Hoe kan ik vertraagde uitnodigingen gebruiken?
Een organisatie wil mogelijk Gebruikers van B2B-samenwerkingstoevoegen, deze zo nodig aan toepassingen inrichten en vervolgens uitnodigingen verzenden. U de B2B-uitnodigingsuitnodigings-API gebruiken om de onboarding-workflow aan te passen.

### <a name="can-i-make-guest-users-visible-in-the-exchange-global-address-list"></a>Kan ik gastgebruikers zichtbaar maken in de Exchange Global Address List?
Ja. Gastobjecten zijn standaard niet zichtbaar in de algemene adreslijst (GAL) van uw organisatie, maar u Azure Active Directory PowerShell gebruiken om ze zichtbaar te maken. Zie [Kan ik gastobjecten zichtbaar maken in de algemene adreslijst?](https://docs.microsoft.com/office365/admin/create-groups/manage-guest-access-in-groups#add-guests-to-the-global-address-list)

### <a name="can-i-make-a-guest-user-a-limited-administrator"></a>Kan ik van een gastgebruiker een beperkte beheerder maken?
Absoluut. Zie [Gastgebruikers toevoegen aan een rol voor](add-guest-to-role.md)meer informatie.

### <a name="does-azure-ad-b2b-collaboration-allow-b2b-users-to-access-the-azure-portal"></a>Kunnen B2B-gebruikers met Azure AD B2B toegang krijgen tot de Azure-portal?
Tenzij een gebruiker de rol van beperkte beheerder krijgt toegewezen, hebben gebruikers van B2B-samenwerking geen toegang nodig tot de Azure-portal. B2B-samenwerkingsgebruikers die de rol van beperkte beheerder toegewezen krijgen, hebben echter toegang tot de portal. Als een gastgebruiker die geen van deze beheerdersrollen toegewezen krijgt, toegang heeft tot de portal, heeft de gebruiker mogelijk toegang tot bepaalde delen van de ervaring. De gastgebruikersrol heeft een aantal machtigingen in de map.

### <a name="can-i-block-access-to-the-azure-portal-for-guest-users"></a>Kan ik de toegang tot de Azure-portal voor gastgebruikers blokkeren?

Ja. U een beleid voor voorwaardelijke toegang maken dat alle gast- en externe gebruikers blokkeert om toegang te krijgen tot de Azure-portal. Wanneer u dit beleid configureert, moet u voorkomen dat u per ongeluk de toegang tot leden en beheerders blokkeert.

1. Meld u aan bij uw [Azure-portal](https://portal.azure.com/) als beveiligingsbeheerder of beheerder van voorwaardelijke toegang.
2. Selecteer in de Azure-portal **Azure Active Directory**. 
3. Selecteer **Onder Beheren**de optie **Beveiliging**.
4. Selecteer voorwaardelijke **toegang** **onder Beveiligen**. Selecteer **Nieuw beleid**.
5. Voer op de pagina **Nieuw** in het tekstvak **Naam** een naam in voor het beleid (bijvoorbeeld Gasten blokkeren om toegang te krijgen tot de portal).
6. Onder **Toewijzingen** selecteert u **Gebruikers en groepen**.
7. Kies op het tabblad **Opnemen** de optie **Gebruikers en groepen selecteren**en selecteer Alle **gast- en externe gebruikers (Voorbeeld)**.
9. Selecteer **Done**.
10. Selecteer op de pagina **Nieuw** in de sectie **Toewijzingen** de optie **Cloud-apps of -acties**.
11. Kies op de pagina **Cloud-apps of -acties** de optie **Apps selecteren**en kies Selecteer **Vervolgens Selecteren**.
12. Kies op de pagina **Selecteren** de optie **Microsoft Azure Management** en kies vervolgens **Selecteren**.
13. Selecteer **Cloud apps or actions** **Gereed**.

### <a name="does-azure-ad-b2b-collaboration-support-multi-factor-authentication-and-consumer-email-accounts"></a>Ondersteunt Azure AD B2B-samenwerking multi-factor authenticatie en e-mailaccounts voor consumenten?
Ja. Multi-factor authenticatie en consumentene-mailaccounts worden beide ondersteund voor Azure AD B2B-samenwerking.

### <a name="do-you-support-password-reset-for-azure-ad-b2b-collaboration-users"></a>Ondersteunt u wachtwoordreset voor gebruikers van Azure AD B2B-samenwerkingsgebruikers?
Als uw Azure AD-tenant de thuismap voor een gebruiker is, u het wachtwoord van de gebruiker opnieuw instellen vanuit de [Azure-portal.](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-reset-password-azure-portal) U een wachtwoord echter niet rechtstreeks opnieuw instellen voor een gastgebruiker die zich aanmeldt met een account dat wordt beheerd door een andere Azure AD-map of externe identiteitsprovider. Alleen de gastgebruiker of een beheerder in de thuismap van de gebruiker kan het wachtwoord opnieuw instellen. Hier volgen enkele voorbeelden van hoe wachtwoordreset werkt voor gastgebruikers:
 
* Gastgebruikers die zich aanmelden met een guestuser@live.comMicrosoft-account (bijvoorbeeld) kunnen hun eigen wachtwoorden opnieuw instellen met behulp van microsoft-account selfservice wachtwoordreset (SSPR). Zie [Het wachtwoord van uw Microsoft-account opnieuw instellen.](https://support.microsoft.com/help/4026971/microsoft-account-how-to-reset-your-password)
* Gastgebruikers die zich aanmelden met een Google-account of een andere externe identiteitsprovider, kunnen hun eigen wachtwoorden opnieuw instellen met behulp van de SSPR-methode van hun identiteitsprovider. Een gastgebruiker met het Google-account guestuser@gmail.com kan bijvoorbeeld zijn wachtwoord opnieuw instellen door de instructies in Wijzigen of uw wachtwoord opnieuw [instellen.](https://support.google.com/accounts/answer/41078)
* Als de identiteitstenant een just-in-time (JIT) of "virale" tenant is (wat betekent dat het een aparte, onbeheerde Azure-tenant is), kan alleen de gastgebruiker zijn wachtwoord opnieuw instellen. Soms neemt een organisatie [het beheer over van virale tenants](https://docs.microsoft.com/azure/active-directory/users-groups-roles/domains-admin-takeover) die worden gemaakt wanneer werknemers hun e-mailadressen op het werk gebruiken om zich aan te melden voor services. Nadat de organisatie een virale tenant heeft overneemt, kan alleen een beheerder in die organisatie het wachtwoord van de gebruiker opnieuw instellen of SSPR inschakelen. Indien nodig u als uitnodigende organisatie het gastgebruikersaccount uit uw directory verwijderen en een uitnodiging opnieuw verzenden.

* Als de thuismap van de gastgebruiker uw Azure AD-tenant is, u het wachtwoord van de gebruiker opnieuw instellen. U hebt bijvoorbeeld een gebruiker gemaakt of een gebruiker gesynchroniseerd vanuit uw on-premises Active Directory en deze gebruiker ingesteld op Gast. Omdat deze gebruiker thuisis in uw directory, u het wachtwoord opnieuw instellen via de Azure-portal.

### <a name="does-microsoft-dynamics-365-provide-online-support-for-azure-ad-b2b-collaboration"></a>Biedt Microsoft Dynamics 365 online ondersteuning voor Azure AD B2B-samenwerking?
Ja, Dynamics 365 (online) ondersteunt Azure AD B2B-samenwerking. Zie het dynamics 365-artikel [Gebruikers uitnodigen met Azure AD B2B-samenwerking](https://docs.microsoft.com/dynamics365/customer-engagement/admin/invite-users-azure-active-directory-b2b-collaboration)voor meer informatie.

### <a name="what-is-the-lifetime-of-an-initial-password-for-a-newly-created-b2b-collaboration-user"></a>Wat is de levensduur van een eerste wachtwoord voor een nieuw gemaakte B2B-samenwerkingsgebruiker?
Azure AD heeft een vaste set vereisten voor teken-, wachtwoordsterkte en accountuitsluiting die ook van toepassing zijn op alle Azure AD-cloudgebruikersaccounts. Cloudgebruikersaccounts zijn accounts die niet worden gefedeerd met een andere identiteitsprovider, zoals 
* Microsoft-account
* Facebook
* Active Directory Federation Services
* Nog een cloudtenant (voor B2B-samenwerking)

Voor federatieve accounts is het wachtwoordbeleid afhankelijk van het beleid dat wordt toegepast in de on-premises huurovereenkomst en de Microsoft-accountinstellingen van de gebruiker.

### <a name="an-organization-might-want-to-have-different-experiences-in-their-applications-for-tenant-users-and-guest-users-is-there-standard-guidance-for-this-is-the-presence-of-the-identity-provider-claim-the-correct-model-to-use"></a>Een organisatie wil mogelijk verschillende ervaringen hebben in hun toepassingen voor tenantgebruikers en gastgebruikers. Is er standaard begeleiding voor dit? Is de aanwezigheid van de identiteitsprovider claim het juiste model te gebruiken?
Een gastgebruiker kan elke identiteitsprovider gebruiken om te verifiëren. Zie [Eigenschappen van een B2B-samenwerkingsgebruiker](user-properties.md)voor meer informatie. Gebruik de eigenschap **UserType** om de gebruikerservaring te bepalen. De **UserType-claim** is momenteel niet opgenomen in het token. Toepassingen moeten de Microsoft Graph API gebruiken om de map voor de gebruiker op te vragen en om het UserType te krijgen.

### <a name="where-can-i-find-a-b2b-collaboration-community-to-share-solutions-and-to-submit-ideas"></a>Waar vind ik een B2B samenwerkingscommunity om oplossingen te delen en ideeën in te dienen?
We luisteren voortdurend naar uw feedback om de B2B-samenwerking te verbeteren. Deel uw gebruikersscenario's, aanbevolen procedures en wat u leuk vindt aan Azure AD B2B-samenwerking. Neem deel aan de discussie in de [Microsoft Tech Community](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B/bd-p/AzureAD_B2b).
 
We nodigen u ook uit om uw ideeën in te dienen en te stemmen voor toekomstige functies op [B2B Collaboration Ideas](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B-Ideas/idb-p/AzureAD_B2B_Ideas).

### <a name="can-we-send-an-invitation-that-is-automatically-redeemed-so-that-the-user-is-just-ready-to-go-or-does-the-user-always-have-to-click-through-to-the-redemption-url"></a>Kunnen we een uitnodiging sturen die automatisch wordt ingewisseld, zodat de gebruiker gewoon "klaar is om te gaan"? Of moet de gebruiker altijd doorklikken naar de inwissel-URL?
U andere gebruikers in de partnerorganisatie uitnodigen met behulp van de UI-, PowerShell-scripts of API's. U de gastgebruiker vervolgens een directe link naar een gedeelde app sturen. In de meeste gevallen is het niet langer nodig om de e-mailuitnodiging te openen en op een inwissel-URL te klikken. Zie [inwisseling van Azure Active Directory B2B-uitnodigingsuitnodigingen](redemption-experience.md)voor samenwerkingsinstellingen .

### <a name="how-does-b2b-collaboration-work-when-the-invited-partner-is-using-federation-to-add-their-own-on-premises-authentication"></a>Hoe werkt B2B-samenwerking wanneer de uitgenodigde partner federatie gebruikt om hun eigen on-premises authenticatie toe te voegen?
Als de partner een Azure AD-tenant heeft die wordt gefedeerd naar de on-premises verificatie-infrastructuur, wordt on-premises single sign-on (SSO) automatisch bereikt. Als de partner geen Azure AD-tenant heeft, wordt een Azure AD-account gemaakt voor nieuwe gebruikers. 

### <a name="i-thought-azure-ad-b2b-didnt-accept-gmailcom-and-outlookcom-email-addresses-and-that-b2c-was-used-for-those-kinds-of-accounts"></a>Ik dacht dat Azure AD B2B niet gmail.com en outlook.com e-mailadressen accepteerde, en dat B2C werd gebruikt voor dit soort accounts?
We verwijderen de verschillen tussen B2B en business-to-consumer (B2C) samenwerking in termen van welke identiteiten worden ondersteund. De gebruikte identiteit is geen goede reden om te kiezen tussen het gebruik van B2B of het gebruik van B2C. Zie [B2B-samenwerking en B2C vergelijken in Azure Active Directory](compare-with-b2c.md)voor informatie over het kiezen van uw samenwerkingsoptie.

### <a name="what-applications-and-services-support-azure-b2b-guest-users"></a>Welke toepassingen en services ondersteunen Azure B2B-gastgebruikers?
Alle azure AD-toepassingen kunnen Azure B2B-gastgebruikers ondersteunen, maar ze moeten een eindpunt gebruiken dat is ingesteld als tenant om gastgebruikers te verifiëren. Mogelijk moet u ook [de claims aanpassen](claims-mapping.md) in het SAML-token dat wordt uitgegeven wanneer een gastgebruiker zich verifieert naar de app. 

### <a name="can-we-force-multi-factor-authentication-for-b2b-guest-users-if-our-partners-dont-have-multi-factor-authentication"></a>Kunnen we multi-factor authenticatie forceren voor B2B-gastgebruikers als onze partners geen multi-factor authenticatie hebben?
Ja. Zie [Voorwaardelijke toegang voor Gebruikers van B2B-samenwerkingsverbanden voor](conditional-access.md)meer informatie.

### <a name="in-sharepoint-you-can-define-an-allow-or-deny-list-for-external-users-can-we-do-this-in-azure"></a>In SharePoint u een lijst 'Toestaan' of 'weigeren' definiëren voor externe gebruikers. Kunnen we dit doen in Azure?
Ja. Azure AD B2B-samenwerking ondersteunt lijsten en weigeringslijsten. 

### <a name="what-licenses-do-we-need-to-use-azure-ad-b2b"></a>Welke licenties hebben we nodig om Azure AD B2B te gebruiken?
Zie Azure [Active Directory B2B-samenwerkingslicentierichtlijnen](licensing-guidance.md)voor informatie over welke licenties uw organisatie nodig heeft om Azure AD B2B te gebruiken.

### <a name="next-steps"></a>Volgende stappen

- [Wat is Azure AD B2B-samenwerking?](what-is-b2b.md)

