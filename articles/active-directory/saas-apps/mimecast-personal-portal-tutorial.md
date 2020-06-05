---
title: 'Zelfstudie: Azure Active Directory-integratie met de persoonlijke Portal Mimecast | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Mimecast Personal Portal.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 345b22be-d87e-45a4-b4c0-70a67eaf9bfd
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 05/21/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d28835472198a1fddc5f7ed0fe5f0037b602f039
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/26/2020
ms.locfileid: "83848324"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-mimecast-personal-portal"></a>Zelfstudie: Azure Active Directory-integratie met Mimecast Personal Portal voor eenmalige aanmelding

In deze zelfstudie leert u hoe u Mimecast Personal Portal kunt integreren met Azure Active Directory (Azure AD). Wanneer u Mimecast Personal Portal integreert met Azure AD, kunt u het volgende doen:

* In Azure AD bepalen wie toegang heeft tot Mimecast Personal Portal.
* Uw gebruikers zich automatisch laten aanmelden bij Mimecast Personal Portal met hun Azure AD-account.
* Uw accounts op één centrale locatie beheren: de Azure-portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) krijgen.
* Een abonnement op Mimecast Personal Portal waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Mimecast Personal Portal ondersteunt door **SP en IDP** geïnitieerde eenmalige aanmelding
* Zodra u Mimecast Personal Portal hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-mimecast-personal-portal-from-the-gallery"></a>Mimecast Personal Portal toevoegen vanuit de galerie

Voor het configureren van de integratie van Mimecast Personal Portal in Azure AD moet u Mimecast Personal Portal uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** **Mimecast Personal Portal** in het zoekvak.
1. Selecteer **Mimecast Personal Portal** uit het resultatenvenster en voeg de app vervolgens toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-mimecast-personal-portal"></a>Eenmalige aanmelding van Azure AD configureren en testen voor Mimecast Personal Portal

Configureer en test eenmalige aanmelding van Azure AD met Mimecast Personal Portal met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Mimecast Personal Portal.

Als u eenmalige aanmelding van Azure AD met Mimecast Personal Portal wilt configureren en testen, moet u de volgende stappen voltooien:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B. Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** : zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding voor Mimecast Personal Portal configureren](#configure-mimecast-personal-portal-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wil configureren.
    1. **[Een testgebruiker maken in Mimecast Personal Portal](#create-mimecast-personal-portal-test-user)** : om in Mimecast Personal Portal een tegenhanger van B.Simon te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in de Azure-portal.

1. Ga in de [Azure-portal](https://portal.azure.com/) op de toepassingsintegratiepagina van **Mimecast Personal Portal** naar de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In het gedeelte **Standaard SAML-configuratie** voert u de volgende stappen uit als u de toepassing in de door IDP geïnitieerde modus wilt configureren:

    a. In het tekstvak **Id** typt u een URL met het volgende patroon:

    | Regio  |  Waarde | 
    | --------------- | --------------- |
    | Europa          | `https://eu-api.mimecast.com/sso/<accountcode>`|
    | Verenigde Staten   | `https://us-api.mimecast.com/sso/<accountcode>`|
    | Zuid-Afrika    | `https://za-api.mimecast.com/sso/<accountcode>`|
    | Australië       | `https://au-api.mimecast.com/sso/<accountcode>`|
    | Offshore        | `https://jer-api.mimecast.com/sso/<accountcode>`|

    > [!NOTE]
    > U vindt de waarde `accountcode` in Mimecast Personal Portal onder **Account** > **Instellingen** > **Accountcode**. Voeg de `accountcode` toe aan de id.

    b. Typ een URL in het tekstvak **Antwoord-URL**:

    | Regio  |  Waarde |
    | --------------- | --------------- |
    | Europa          | `https://eu-api.mimecast.com/login/saml`|
    | Verenigde Staten   | `https://us-api.mimecast.com/login/saml`|
    | Zuid-Afrika    | `https://za-api.mimecast.com/login/saml`|
    | Australië       | `https://au-api.mimecast.com/login/saml`|
    | Offshore        | `https://jer-api.mimecast.com/login/saml`|

1. Als u de toepassing wilt configureren in door **SP**geïnitieerde modus:

    Typ een URL in het tekstvak **Aanmeldings-URL**:

    | Regio  |  Waarde |
    | --------------- | --------------- |
    | Europa          | `https://eu-api.mimecast.com/login/saml`|
    | Verenigde Staten   | `https://us-api.mimecast.com/login/saml`|
    | Zuid-Afrika    | `https://za-api.mimecast.com/login/saml`|
    | Australië       | `https://au-api.mimecast.com/login/saml`|
    | Offshore        | `https://jer-api.mimecast.com/login/saml`|

1. Klik op **Opslaan**.

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op de kopieerknop om de **URL voor federatieve metagegevens van de app** te kopiëren en slaat u deze op uw computer op.

    ![De link om het certificaat te downloaden](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie gaat u een testgebruiker met de naam B.Simon maken in de Azure-portal.

1. Selecteer in het linkerdeelvenster van de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Volg de volgende stappen bij de eigenschappen voor **Gebruiker**:
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer username@companydomain.extension in het veld **Gebruikersnaam** in. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.
   1. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In dit gedeelte gaat u B.Simon toestemming geven voor gebruik van eenmalige aanmelding met Azure door haar toegang te geven tot Mimecast Personal Portal.

1. Selecteer in de Azure-portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Mimecast Personal Portal** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-mimecast-personal-portal-sso"></a>Eenmalige aanmelding voor Mimecast Personal Portal configureren

1. Meld u in een ander browservenster bij de Mimecast-beheerconsole aan.

1. Ga naar **Administration** > **Services** > **Applications**.

    ![Mimecast Personal Portal configureren](./media/mimecast-personal-portal-tutorial/services.png)

1. Klik op het tabblad **Authentication Profiles**.
    
    ![Mimecast Personal Portal configureren](./media/mimecast-personal-portal-tutorial/authentication-profiles.png)

1. Klik op het tabblad **New Authentication Profile**.

    ![Mimecast Personal Portal configureren](./media/mimecast-personal-portal-tutorial/new-authenticatio-profile.png)

1. Geef een geldige beschrijving op in het tekstvak **Description** en schakel het selectievakje **Enforce SAML Authentication for Mimecast Personal Portal** in.

    ![Mimecast Personal Portal configureren](./media/mimecast-personal-portal-tutorial/selecting-personal-portal.png)

1. Voer de volgende stappen uit op de pagina **SAML Configuration for Mimecast Personal Portal**:

    ![Mimecast Personal Portal configureren](./media/mimecast-personal-portal-tutorial/sso-settings.png)

    a. Selecteer bij **Provider** **Azure Active Directory** in de vervolgkeuzelijst.

    b. Plak in het tekstvak **Metadata URL** de waarde van **App Federation Metadata URL** die u uit de Azure-portal hebt gekopieerd.

    c. Klik op **Import**. Na het importeren van de metagegevens-URL, worden de velden automatisch ingevuld. U hoeft geen actie uit te voeren voor deze velden.

    d. Schakel de selectievakjes **Use Password protected Context** en **Use Integrated Authentication Context** uit.

    e. Klik op **Opslaan**.

### <a name="create-mimecast-personal-portal-test-user"></a>Een testgebruiker maken in Mimecast Personal Portal

1. Meld u in een ander browservenster bij de Mimecast-beheerconsole aan.

1. Ga naar **Administration** > **Directories** > **Internal Directories**.

    ![Mimecast Personal Portal configureren](./media/mimecast-personal-portal-tutorial/internal-directories.png)

1. Selecteer uw domein als het domein hieronder wordt vermeld, of maak een nieuw domein door op **New Domain** te klikken.

    ![Mimecast Personal Portal configureren](./media/mimecast-personal-portal-tutorial/domain-name.png)

1. Klik op het tabblad **New Address**.

    ![Mimecast Personal Portal configureren](./media/mimecast-personal-portal-tutorial/new-address.png)

1. Geef de vereiste gebruikersgegevens op de volgende pagina op:

    ![Mimecast Personal Portal configureren](./media/mimecast-personal-portal-tutorial/user-information.png)

    a. Voer in het tekstvak **Email Address** het e-mailadres van de gebruiker in, bijvoorbeeld `B.Simon@yourdomainname.com`.

    b. Voer in het tekstvak **Global Name** de volledige naam van de gebruiker in bij **Full name**.

    c. Voer in de tekstvakken **Password** en **Confirm Password** het wachtwoord van de gebruiker in.

    d. Schakel het selectievakje **Force Change at Login** in.

    e. Klik op **Opslaan**.

    f. Als u rollen aan de gebruiker wilt toewijzen, klikt u op **Role Edit** en wijst u de vereiste rol toe aan de gebruiker volgens de vereisten van uw organisatie.

    ![Mimecast Personal Portal configureren](./media/mimecast-personal-portal-tutorial/assign-role.png)


## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Mimecast Personal Portal in het toegangsvenster klikt, wordt u automatisch aangemeld bij de instantie van Mimecast Personal Portal waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Mimecast Personal Portal met Azure AD proberen](https://aad.portal.azure.com/)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Mimecast Personal Portal beveiligen met geavanceerde zichtbaarheid en besturingselementen](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
