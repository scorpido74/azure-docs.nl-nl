---
title: 'Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met uniFLOW Online| Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en uniFLOW Online.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 28313d27-638c-4d50-92ad-d093f2ae9ecf
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/02/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f081d6b01ffb0b4fc78c23a076c20ff93c483203
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/09/2020
ms.locfileid: "86166957"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-uniflow-online"></a>Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met uniFLOW Online

In deze zelfstudie leert u hoe u uniFLOW Online integreert met Azure Active Directory (Azure AD). Wanneer u uniFLOW Online integreert met Azure AD, kunt u het volgende doen:

* Beheer in Azure Active Directory wie toegang heeft tot uniFLOW Online.
* Stel uw gebruikers in staat om zich online aan te melden bij uniFLOW met hun Azure AD-accounts.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* uniFLOW Online-tenant.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* uniFLOW Online biedt ondersteuning voor eenmalige aanmelding die is gestart vanuit **SP**

## <a name="adding-uniflow-online-from-the-gallery"></a>uniFLOW Online toevoegen vanuit de galerie

Als u de integratie van uniFLOW in Azure AD wilt configureren, moet u uniFLOW vanuit de galerie toevoegen aan de lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** in het zoekvak: **uniFLOW Online**.
1. Selecteer **uniFLOW Online** in het paneel resultaten en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-uniflow-online"></a>Eenmalige aanmelding van Azure AD configureren en testen voor uniFLOW Online

Configureer en test eenmalige aanmelding van Azure AD met uniFLOW Online met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in uniFLOW Online.

Voltooi de volgende bouwstenen om eenmalige aanmelding van Azure AD met uniFLOW Online te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
   1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
   1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding bij uniFLOW Online configureren](#configure-uniflow-online-sso)** : om de instellingen voor eenmalige aanmelding aan de toepassingszijde te configureren.
    * **[Meld u aan bij uniFLOW Online met behulp van de gemaakte test gebruiker](#sign-in-to-uniflow-online-using-the-created-test-user)** - om gebruikersaanmelding te testen aan de kant van de toepassing.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in de [Azure-portal](https://portal.azure.com/), op de integratiepagina voor de **uniFLOW Online**-toepassing, naar de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard-SAML-configuratie** voert u de waarden in voor de volgende velden:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon:

    - `https://<tenant_domain_name>.eu.uniflowonline.com`
    - `https://<tenant_domain_name>.us.uniflowonline.com`
    - `https://<tenant_domain_name>.sg.uniflowonline.com`
    - `https://<tenant_domain_name>.jp.uniflowonline.com`
    - `https://<tenant_domain_name>.au.uniflowonline.com`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met het volgende patroon:

    - `https://<tenant_domain_name>.eu.uniflowonline.com`
    - `https://<tenant_domain_name>.us.uniflowonline.com`
    - `https://<tenant_domain_name>.sg.uniflowonline.com`
    - `https://<tenant_domain_name>.jp.uniflowonline.com`
    - `https://<tenant_domain_name>.au.uniflowonline.com`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en id. Neem contact op met [het uniFLOW Online-clientondersteuningsteam](mailto:support@nt-ware.com) om deze waarden te verkrijgen. U kunt ook verwijzen naar de patronen die worden weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal of raadpleeg de antwoord-URL die wordt weergegeven in uw uniFLOW Online-tenant.

1. In de uniFLOW Online-toepassing worden de SAML-beweringen in een specifieke indeling verwacht. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van de SAML-tokenkenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![image](common/default-attributes.png)

1. Bovendien verwacht de uniFLOW Online nog enkele kenmerken die als SAML-antwoord moeten worden doorgestuurd. Deze worden hieronder weergegeven. Deze kenmerken worden ook vooraf ingevuld, maar u kunt ze herzien volgens uw vereisten.

    | Naam |  Bronkenmerk|
    | -----------| --------------- |
    | displayname | user.displayname |
    | bijnaam | user.onpremisessamaccountname |

   > [!NOTE]
   > Het kenmerk `user.onpremisessamaccountname` bevat alleen een waarde als uw Azure AD-gebruikers worden gesynchroniseerd vanuit een lokale Windows Active Directory.

1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u in de sectie **SAML-handtekeningcertificaat** op de kopieerknop om de **URL voor federatieve metagegevens van de app** te kopiëren en slaat u deze op uw computer op.

    ![De link om het certificaat te downloaden](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie gaat u een testgebruiker met de naam B.Simon maken in Azure Portal.

1. Selecteer in het linkerdeelvenster van Azure Portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Volg de volgende stappen bij de eigenschappen voor **Gebruiker**:
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer username@companydomain.extension in het veld **Gebruikersnaam** in. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.
   1. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie geeft u B. Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot uniFLOW Online.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer in de lijst met toepassingen de optie **uniFLOW Online**.
1. Ga op de overzichtspagina van de app naar het gedeelte **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

   ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

> [!NOTE]
> Als u wilt dat alle gebruikers toegang krijgen tot de toepassing zonder handmatige toewijzing, gaat u naar de sectie **Beheren** en selecteert u **Eigenschappen**. Wijzig vervolgens de parameter **Gebruikerstoewijzing vereist** naar **NEE**.

## <a name="configure-uniflow-online-sso"></a>UniFLOW online-SSO configureren

1. Meld u in een ander browservenster als beheerder aan bij de website van uniFLOW Online.

1. Selecteer het tabblad **Gebruikers** in het linkernavigatievenster.

    ![uniFLOW Online-configuratie](./media/uniflow-online-tutorial/configure1.png)

1. Klik op **Id-providers**.

    ![uniFLOW Online-configuratie](./media/uniflow-online-tutorial/configure2.png)

1. Klik op **Id-provider toevoegen**.

    ![uniFLOW Online-configuratie](./media/uniflow-online-tutorial/configure3.png)

1. Voer in de sectie **IDENTITY PROVIDER TOEVOEGEN** de volgende stappen uit:


    ![uniFLOW Online-configuratie](./media/uniflow-online-tutorial/configure4.png)

    a. Voer de weergavenaam in, bijv.: *AzureAD SSO*.

    b. Selecteer in de vervolgkeuzelijst **WS-Fed** optie voor **Providertype**.

    c. Selecteer bij **WS-Fed-type** de optie **Azure Active Directory** in de vervolgkeuzelijst.

    d. Klik op **Opslaan**.

1. Voer op het tabblad **Algemeen** de volgende stappen uit:

    ![uniFLOW Online-configuratie](./media/uniflow-online-tutorial/configure5.png)

    a. Voer de weergavenaam in, bijv.: *AzureAD SSO*.

    b. Selecteer de optie **Van URL** voor de **Metagegevens van ADFS Federation**.

    c. Plak in het tekstvak **URL van metagegevens** de waarde van **Metagegevens-URL van App Federation** die u uit de Azure-portal hebt gekopieerd.

    d. Selecteer **Id-provider** als **Ingeschakeld**.

    e. Selecteer **Automatische registratie van gebruikers** als **Geactiveerd**.

    f. Klik op **Opslaan**.

### <a name="sign-in-to-uniflow-online-using-the-created-test-user"></a>Meld u aan bij uniFLOW Online met de gemaakte testgebruiker

1. In een ander browservenster gaat u naar de uniFLOW Online-URL voor uw tenant.

1. Selecteer de eerder gemaakte id-provider om u aan te melden via uw Azure AD-exemplaar.

1. Aanmelden met behulp van de testgebruiker.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer uniFLOW Online met Azure AD](https://aad.portal.azure.com/)
