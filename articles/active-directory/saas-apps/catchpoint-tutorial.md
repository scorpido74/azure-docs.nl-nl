---
title: 'Zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met Catchpoint'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Catchpoint.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ab3eead7-8eb2-4c12-bb3a-0e46ec899d37
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/27/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7b19e286d299811a950df05f93d221bd710676ea
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80743493"
---
# <a name="tutorial-azure-active-directory-single-sign-on-integration-with-catchpoint"></a>Zelf studie: de integratie van eenmalige aanmelding Azure Active Directory met Catchpoint

In deze zelf studie leert u hoe u Catchpoint integreert met Azure Active Directory (Azure AD). Wanneer u Catchpoint integreert met Azure AD, kunt u het volgende doen:

* Beheer de toegang van gebruikers tot Catchpoint vanuit Azure AD.
* Automatische Catchpoint-aanmelding inschakelen voor gebruikers met Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* Een Catchpoint-abonnement waarvoor eenmalige aanmelding (SSO) is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* Catchpoint ondersteunt door SP geïnitieerde en door IDP geïnitieerde SSO.
* Catchpoint biedt ondersteuning voor Just-in-time-gebruikers inrichting.
* Nadat u Catchpoint hebt geconfigureerd, kunt u sessie beheer afdwingen. Deze voorzorgsmaatregel beschermt tegen exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in real-time. Sessie beheer is een uitbrei ding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessie beheer met Microsoft Cloud app Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="add-catchpoint-from-the-gallery"></a>Catchpoint toevoegen vanuit de galerie

Als u de integratie van Catchpoint in azure AD wilt configureren, voegt u Catchpoint toe aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met een werk-, school-of persoonlijke Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het linkerdeel venster.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **Catchpoint** in het zoekvak.
1. Selecteer **Catchpoint** in het deel venster resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on-for-catchpoint"></a>Eenmalige aanmelding voor Azure AD configureren en testen voor Catchpoint

Voor het werken met SSO moet u een Azure AD-gebruiker koppelen aan een gebruiker in Catchpoint. Voor deze zelf studie configureert u een test gebruiker met de naam **B. Simon**. 

Voer de volgende secties uit:

1. [Configureer Azure AD SSO](#configure-azure-ad-sso)om deze functie in te scha kelen voor uw gebruikers.
    * [Maak een Azure AD-test gebruiker om de](#create-an-azure-ad-test-user)eenmalige aanmelding van Azure ad te testen met B. Simon.
    * [Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)toe om B. Simon in te scha kelen voor het gebruik van eenmalige aanmelding voor Azure AD.
1. [Configureer CATCHPOINT SSO](#configure-catchpoint-sso)voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    * [Maak een Catchpoint-test gebruiker](#create-a-catchpoint-test-user)om koppelen van het test account B. Simon Azure AD toe te staan voor een vergelijkbaar gebruikers account in Catchpoint.
1. [Test SSO](#test-sso)om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen in de Azure Portal om Azure AD SSO in te scha kelen:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
1. Zoek op de pagina Toepassings integratie van **Catchpoint** de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Selecteer op de pagina **eenmalige aanmelding met SAML instellen** het pictogram pen om de basis instellingen voor **SAML-configuratie** te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. De gestarte modus configureren voor Catchpoint:
   - Voer de waarden voor de volgende velden in voor de modus gestart voor **IDP**:
     - Voor **id**:`https://portal.catchpoint.com/SAML2`
     - Voor **antwoord-URL**:`https://portal.catchpoint.com/ui/Entry/SingleSignOn.aspx`
   - Voor de door **SP**geïnitieerde modus selecteert u **extra url's instellen** en voert u de volgende waarde in:
     - Voor **aanmeldings-URL**:`https://portal.catchpoint.com/ui/Entry/SingleSignOn.aspx`

1. De Catchpoint-toepassing verwacht de SAML-beweringen in een specifieke indeling. Aangepaste kenmerk toewijzingen toevoegen aan uw configuratie van SAML-token kenmerken. De volgende tabel bevat de lijst met standaard kenmerken:

    | Naam | Bronkenmerk|
    | ------------ | --------- |
    | GivenName | User. givenneame |
    | Achternaam | user.surname |
    | EmailAddress | user.mail |
    | Naam | user.userprincipalname |
    | Unieke gebruikers-id | user.userprincipalname |

    ![Scherm opname van gebruikers kenmerken & lijst met claims](common/default-attributes.png)

1. Daarnaast verwacht de Catchpoint-toepassing een ander kenmerk dat wordt door gegeven in een SAML-reactie. Zie de volgende tabel. Dit kenmerk is ook vooraf ingevuld, maar u kunt het controleren en bijwerken zodat het aan uw vereisten voldoet.

    | Naam | Bronkenmerk|
    | ------------ | --------- |
    | naamruimte | user.assignedrole |

    > [!NOTE]
    > De `namespace` claim moet worden toegewezen aan de account naam. Deze account naam moet worden ingesteld met een rol in azure AD om terug te worden door gegeven in het SAML-antwoord. Zie [Configure the Role claim issued in het SAML-token voor zakelijke toepassingen](https://docs.microsoft.com/azure/active-directory/develop/active-directory-enterprise-app-role-management)voor meer informatie over rollen in azure AD.

1. Ga naar de pagina **eenmalige aanmelding met SAML instellen** . Zoek in de sectie **SAML-handtekening certificaat** het **certificaat (base64)**. Selecteer **downloaden** om het certificaat op uw computer op te slaan.

    ![De koppeling voor het downloaden van certificaten](common/certificatebase64.png)

1. In de sectie **Catchpoint instellen** kopieert u de url's die u nodig hebt in een latere stap.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie gebruikt u de Azure Portal om een Azure AD-test gebruiker te maken met de naam B. Simon.

1. Selecteer **Azure Active Directory** > **gebruikers** > van**alle gebruikers**in het linkerdeel venster van de Azure Portal.
1. Selecteer **nieuwe gebruiker** boven aan het scherm.
1. Voer de volgende stappen uit in de eigenschappen van de **gebruiker** :
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer in het veld **gebruikers naam** het username@companydomain.extensionin. Geef bijvoorbeeld `B.Simon@contoso.com` op.
   1. Schakel het selectie vakje **wacht woord weer geven** in. Let op de weer gegeven wachtwoord waarde.
   1. Selecteer **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan Catchpoint.

1. Selecteer in het Azure Portal**alle toepassingen**in **bedrijfs toepassingen** > .
1. Selecteer in de lijst toepassingen de optie **Catchpoint**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling ' gebruiker toevoegen '](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst met gebruikers. Klik onder aan het scherm op **selecteren** .
1. Als u een waarde voor een rol verwacht in de SAML-bewering, kijkt u in het dialoog venster **rol selecteren** en kiest u de rol van de gebruiker in de lijst. Klik op de knop **selecteren** onder aan het scherm.
1. Selecteer **Toewijzen** in het dialoogvenster **Toewijzing toevoegen**.

## <a name="configure-catchpoint-sso"></a>Catchpoint SSO configureren

1. Meld u in een ander webbrowser venster als beheerder aan bij de Catchpoint-toepassing.

1. Selecteer het pictogram **instellingen** en vervolgens **SSO-ID-provider**.

    ![Scherm opname van Catchpoint-instellingen waarvoor SSO-ID-provider is geselecteerd](./media/catchpoint-tutorial/configuration1.png)

1. Voer op de pagina **eenmalige aanmelding** de volgende velden in:

   ![Scherm opname van Catchpoint-pagina voor eenmalige aanmelding](./media/catchpoint-tutorial/configuration2.png)

   Veld | Waarde
   ----- | ----- 
   **Naam ruimte** | Een geldige waarde voor een naam ruimte.
   **Verlener van ID-provider** | De `Azure AD Identifier` waarde van de Azure Portal.
   **URL voor eenmalige aanmelding** | De `Login URL` waarde van de Azure Portal.
   **Certificaat** | De inhoud van het gedownloade `Certificate (Base64)` bestand van de Azure Portal. Gebruik Klad blok om het weer te geven en te kopiëren.

   U kunt ook de **XML voor federatieve meta gegevens** uploaden door de optie voor het **uploaden van meta gegevens** te selecteren.

1. Selecteer **Opslaan**.

### <a name="create-a-catchpoint-test-user"></a>Een Catchpoint-test gebruiker maken

Catchpoint biedt ondersteuning voor Just-in-time-gebruikers inrichting, die standaard is ingeschakeld. U hebt geen actie-items in deze sectie. Als B. Simon niet al bestaat als een gebruiker in Catchpoint, wordt deze gemaakt na verificatie.

## <a name="test-sso"></a>SSO testen

In deze sectie test u de configuratie van eenmalige aanmelding voor Azure AD met behulp van de portal mijn apps.

Wanneer u de tegel Catchpoint in de portal mijn apps selecteert, moet u automatisch worden aangemeld bij de Catchpoint-app waarvoor SSO is geconfigureerd. Zie voor meer informatie over de portal mijn apps [Aanmelden en apps starten vanuit de portal mijn apps](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access).

> [!NOTE]
> Wanneer u bent aangemeld bij de Catchpoint-toepassing via de aanmeldings pagina, voert u na het opgeven van **Catchpoint-referenties**de geldige **naam ruimte** in het veld **bedrijfs referenties (SSO)** in en selecteert u **Aanmelden**.
> 
> ![Catchpoint-configuratie](./media/catchpoint-tutorial/loginimage.png)

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelf studies voor het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer Catchpoint met Azure AD](https://aad.portal.azure.com/)

- [Wat is sessie beheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
