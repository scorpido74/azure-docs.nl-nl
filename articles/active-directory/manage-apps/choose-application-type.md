---
title: Kiezen welk toepassings type moet worden gebruikt bij het toevoegen van een toepassing | Microsoft Docs
description: Meer informatie over de ondersteunde typen toepassingen die u kunt integreren met Azure AD en de bijbehorende configuratie opties
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
ms.date: 09/03/2019
ms.author: mimart
ms.collection: M365-identity-device-management
ROBOTS: NOINDEX
ms.openlocfilehash: d5bd2397c345a4f670bde343f751cd69f825ecb9
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71056069"
---
# <a name="choosing-the-application-type-when-adding-an-application-in-azure-active-directory"></a>Het toepassings type kiezen bij het toevoegen van een toepassing in Azure Active Directory

Meer informatie over de vier typen toepassingen die u kunt toevoegen aan Azure Active Directory (Azure AD). Wanneer u een toepassing toevoegt in Azure Active Directory, wordt u gevraagd een van de vier toepassings typen te kiezen.

## <a name="what-are-the-types-of-applications"></a>Wat zijn de typen toepassingen?

Azure AD ondersteunt vier hoofd toepassings typen die u kunt toevoegen met behulp van de functie **toevoegen** onder **bedrijfs toepassingen**. Deze omvatten:

- **Azure AD Gallery-toepassingen** : een toepassing die vooraf is geïntegreerd voor eenmalige aanmelding met Azure AD.

- **Toepassingen voor toepassings proxy** : een toepassing die in uw on-premises omgeving wordt uitgevoerd en die u wilt voorzien van een beveiligde eenmalige aanmelding op een externe locatie.

- **Aangepaste, ontwikkelde toepassingen** : een toepassing die uw organisatie wil ontwikkelen op het Azure ad-platform voor toepassings ontwikkeling, maar die mogelijk nog niet bestaat.

- **Niet-galerie toepassingen** : Breng uw eigen toepassingen. Een wille keurige webkoppeling of een toepassing die een gebruikers naam-en wachtwoord veld weergeeft, ondersteunt SAML-of OpenID Connect Connect-protocollen of ondersteunt SCIM die u wilt integreren voor eenmalige aanmelding met Azure AD.

## <a name="features-and-capabilities-supported-by-the-application-types"></a>Functies en mogelijkheden die worden ondersteund door de toepassings typen

De volgende functies worden ondersteund door een van de voor gaande vier toepassings typen in azure AD:

- **Snelstartgids** : Ga snel aan de slag met een toepassing door [eenvoudige implementaties tappen uit te voeren](https://docs.microsoft.com/azure/active-directory/active-directory-integrating-applications-getting-started)

- **Algemeen eigenschappen beheer** : krijg een [direct diepe koppeling](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) naar een toepassing, [Pas de huis stijl](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-change-app-logo-user-azure-portal) van een toepassing aan of [Schakel de toepassing](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-disable-app-azure-portal) voor alle gebruikers uit.

- **Gebruikers-en groeps beheer** : u kunt gebruikers en groepen [toewijzen](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) aan of [verwijderen](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-remove-assignment-azure-portal) uit een toepassing en eventueel de specifieke toepassings rollen toewijzen waartoe deze gebruikers en groepen toegang hebben

- **Toegang voor selfservice toepassingen** : Hiermee kunnen uw gebruikers de [toegang](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) tot een toepassing op basis van de toegangs Vensters van de toepassing toestaan via hun toepassings panelen door een toepassing rechtstreeks toe te voegen of lid te worden van [een groep waarvoor self-service is ingeschakeld ](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management), eventueel goed keuring vereist voor uw bedrijf

- **Aanmeld logboeken** : [alle aanmeldingen voor een toepassing](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins)of al uw toepassingen weer geven

- **Audit logboeken** : Zie [gedetailleerde audit logboeken over wijzigingen in een toepassing](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs)of voor al uw toepassingen

- **Voorwaardelijke en op risico gebaseerde toegang** : Stel krachtige [toegangs regels op basis van een voor waarde](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access) in die worden afgedwongen wanneer gebruikers zich aanmelden bij een bepaalde toepassing

- **Machtigingen weer geven** : een van de [OAuth2-machtigingen](https://docs.microsoft.com/azure/active-directory/active-directory-apps-permissions-consent) weer geven die een toepassing vanuit één locatie toegang heeft tot uw Directory

## <a name="single-sign-on-and-provisioning-modes-supported-by-specific-application-types"></a>Eenmalige aanmelding en inrichtings modi die worden ondersteund door specifieke toepassings typen

In de volgende tabel worden de verschillende modi voor eenmalige aanmelding en inrichtingen beschreven die worden ondersteund door elk van de voor gaande toepassings typen. U kunt deze tabel gebruiken om te begrijpen welke toepassing u moet toevoegen ter ondersteuning van een specifiek doel.

  ![Tabel Verschillende SSO-en inrichtings modi die worden ondersteund door elk app-type](./media/choose-application-type/table1.png)

## <a name="how-to-choose-a-single-sign-on-mode"></a>De modus voor eenmalige aanmelding kiezen

Hieronder vindt u de ondersteunde modi voor **eenmalige aanmelding** voor Azure AD-toepassingen.

- **Eenmalige aanmelding voor Azure AD is uitgeschakeld** : Kies voor eenmalige aanmelding van Azure AD de **modus** voor eenmalige aanmeldingen als u de toepassing nog niet kunt integreren met eenmalige aanmelding met Azure AD of als u deze eenvoudig wilt testen

- **Gekoppelde aanmelding** : Kies de modus voor het [koppelen van](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) **eenmalige** aanmelding als u een toepassing hebt die al verbonden is met een bestaande oplossing voor eenmalige aanmelding of als u een eenvoudige koppeling voor uw gebruikers wilt publiceren in hun [ Toepassings toegangs venster of het](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) start programma voor [Office 365-toepassingen](https://login.microsoftonline.com/common/oauth2/authorize?response_mode=form_post&response_type=id_token&scope=openid&nonce=d508a995-f6d6-4b8a-81b8-825c71f1be46.636253878097046923&state=https%3a%2f%2fsupport.office.com%2farticle%2fMeet-the-Office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a%3fui%3den-US%26rs%3den-US%26ad%3dUS&client_id=4b233688-031c-404b-9a80-a4f3f2351f90&redirect_uri=https%3a%2f%2fsupport.office.com%2fauth%2fsignin&login_hint=asteen%40microsoft.com&prompt=none)

- **Aanmelding op basis van wacht woorden** : Kies de aanmeldings **modus** voor eenmalige aanmelding [op basis van wacht woorden](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) als uw toepassing een HTML-gebruikers naam en-wacht woord weergeeft en u wilt dat de gebruikers naam en het wacht woord veilig kunnen worden afgespeeld naar de toepassing later

- Op **SAML gebaseerde aanmelding** : Kies de op [SAML gebaseerde aanmeld](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) modus voor eenmalige aanmelding als uw toepassing ondersteuning biedt voor het SAML-of OpenID Connect Connect-protocol, of als u gebruikers wilt kunnen toewijzen aan specifieke toepassings rollen op basis van regels die u in uw SAML definieert claims

  >[!NOTE]
  >Deze optie is niet beschikbaar wanneer de toepassings proxy is geconfigureerd voor een toepassing.

- **Aanmelden op basis** van de koptekst: Kies deze modus voor eenmalige aanmelding op [basis](application-proxy-configure-single-sign-on-with-ping-access.md) van een aanmelding als u een toepassing hebt die gebruikmaakt van PingAccess die ondersteuning bieden voor verificatie op basis van http-headers waarvoor u eenmalige aanmelding wilt uitvoeren

  >[!NOTE]
  >Deze optie is alleen beschikbaar wanneer de toepassings proxy en PingAccess zijn geconfigureerd voor een toepassing.

- **Geïntegreerde Windows-verificatie** : Kies de geïntegreerde modus voor eenmalige aanmelding voor [Windows-verificatie](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-sso-using-kcd) wanneer u een on-premises WIA-toepassing wilt weer geven waarvoor eenmalige aanmelding moet worden uitgevoerd

  >[!NOTE]
  >Deze optie is alleen beschikbaar wanneer de toepassings proxy is geconfigureerd voor een toepassing.

## <a name="single-sign-on-modes-for-custom-developed-applications"></a>Modus voor eenmalige aanmelding voor speciaal ontwikkelde toepassingen

Toepassingen die u zelf hebt ontwikkeld via de aangepaste ervaring, bieden ook ondersteuning voor aanvullende eenmalige aanmeldings modi die niet eerder zijn vermeld, waaronder:

- [OAuth 2,0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code) -aanmelding

- [OpenID Connect Connect](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code) -aanmelding op basis van 1,0

- Aanmelding op basis van [WS-Federation 1,2](https://docs.oasis-open.org/wsfed/federation/v1.2/os/ws-federation-1.2-spec-os.html)

- Op [SAML 2,0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-protocol-reference) gebaseerde aanmelding

Lees de [Azure Active Directory hand leiding voor ontwikkel aars](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide) voor meer informatie over het maken van een aangepaste toepassing die ondersteuning biedt voor deze modus voor eenmalige aanmelding.

## <a name="how-to-set-an-applications-single-sign-on-mode"></a>De modus voor eenmalige aanmelding van een toepassing instellen

Volg de volgende instructies om de modus voor eenmalige aanmelding van een toepassing in te stellen:

1. Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als **globale beheerder** of **co-beheerder.**
1. Open de **uitbrei ding Azure Active Directory** door te klikken op **alle services** boven aan het hoofd navigatie menu aan de linkerkant.
1. Typ in **' Azure Active Directory**' in het zoekvak van filter en selecteer de **Azure Active Directory** item.
1. Klik op **bedrijfs toepassingen** in het navigatie menu Azure Active Directory aan de linkerkant.
1. Klik op **alle toepassingen** om een lijst met al uw toepassingen weer te geven.

   * Als u de toepassing die u wilt weergeven die hier niet ziet, gebruikt u de **Filter** besturingselement aan de bovenkant van de **lijst met alle toepassingen** en stel de **weergeven** optie naar **alle Toepassingen.**

1. Selecteer de toepassing waarvoor u eenmalige aanmelding wilt configureren.
1. Zodra de toepassing is geladen, klikt u op **eenmalige aanmelding** in het navigatie menu aan de linkerkant van de toepassing.

## <a name="how-to-choose-a-provisioning-mode"></a>Een inrichtings modus kiezen

- **Hand matig inrichten** : Kies de [hand matige](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-manage-provisioning#provisioning-modes) inrichtings modus als u bestaande accounts hebt, of als u accounts voor deze toepassing buiten Azure ad wilt beheren.

- **Automatische inrichting** : Kies de [automatische](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-manage-provisioning#configuring-automatic-user-account-provisioning) **inrichtings modus** als u automatische op API gebaseerde inrichting en/of het ongedaan maken van de inrichting van gebruikers accounts voor deze toepassing wilt inschakelen 

- **Automatische inrichting op basis van scim** : gebruik [op scim gebaseerde automatische inrichting](https://docs.microsoft.com/azure/active-directory/active-directory-scim-provisioning) als uw toepassing ondersteuning biedt voor het scim-protocol voor het detecteren van wijzigingen aan gebruikers en groepen, die automatisch worden verzonden voor wijzigingen in een toepassing die is geïntegreerd met Azure AD 

  >[!NOTE]
  >Deze optie wordt niet weer gegeven als een specifieke inrichtings modus, maar is standaard ingeschakeld voor alle toepassingen die zijn geïntegreerd met Azure AD.

## <a name="how-to-set-an-applications-provisioning-mode"></a>De inrichtings modus van een toepassing instellen

Volg deze instructies om de **inrichtings** modus van een toepassing in te stellen:

1. Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als **globale beheerder** of **co-beheerder.**
1. Open de **uitbrei ding Azure Active Directory** door te klikken op **alle services** boven aan het hoofd navigatie menu aan de linkerkant.
1. Typ in **' Azure Active Directory**' in het zoekvak van filter en selecteer de **Azure Active Directory** item.
1. Klik op **bedrijfs toepassingen** in het navigatie menu Azure Active Directory aan de linkerkant.
1. Klik op **alle toepassingen** om een lijst met al uw toepassingen weer te geven.

   * Als u de toepassing die u wilt weergeven die hier niet ziet, gebruikt u de **Filter** besturingselement aan de bovenkant van de **lijst met alle toepassingen** en stel de **weergeven** optie naar **alle Toepassingen.**

1. Selecteer de toepassing waarvoor u de inrichting wilt configureren.
1. Zodra de toepassing is geladen, klikt u op **inrichten** in het navigatie menu aan de linkerkant van de toepassing.

## <a name="next-steps"></a>Volgende stappen

[Toepassingen beheren met Azure Active Directory](what-is-application-management.md)
