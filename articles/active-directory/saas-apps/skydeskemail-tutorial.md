---
title: 'Zelf studie: integratie met SkyDesk-e-mail Azure Active Directory | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en SkyDesk-e-mail.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a9d0bbcb-ddb5-473f-a4aa-028ae88ced1a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: faaa5dcc435452d6ed9e0f2c5b481df1e352dfd2
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "67090428"
---
# <a name="tutorial-azure-active-directory-integration-with-skydesk-email"></a>Zelf studie: integratie met SkyDesk-e-mail Azure Active Directory

In deze zelf studie leert u hoe u SkyDesk-e-mail kunt integreren met Azure Active Directory (Azure AD).
Het integreren van SkyDesk-e-mail met Azure AD biedt de volgende voor delen:

* U kunt beheren in azure AD die toegang heeft tot SkyDesk-e-mail.
* U kunt ervoor zorgen dat uw gebruikers automatisch worden aangemeld voor SkyDesk-e-mail (eenmalige aanmelding) met hun Azure AD-accounts.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u nog geen abonnement op Azure hebt, [Maak dan een gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie met SkyDesk-e-mail wilt configureren, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Abonnement voor eenmalige aanmelding van SkyDesk-e-mail

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* SkyDesk-e-mail ondersteunt door **SP** GEÏNITIEERDe SSO

## <a name="adding-skydesk-email-from-the-gallery"></a>SkyDesk-e-mail toevoegen vanuit de galerie

Als u de integratie van SkyDesk-e-mail in azure AD wilt configureren, moet u SkyDesk-e-mail vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om SkyDesk-e-mail toe te voegen vanuit de galerie:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **SkyDesk-e-mail**in het zoekvak, selecteer **SkyDesk e-mail** in het resultaten paneel en klik vervolgens op de knop **toevoegen** om de toepassing toe te voegen.

     ![SkyDesk-e-mail in de resultaten lijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie kunt u eenmalige aanmelding voor Azure AD configureren en testen met SkyDesk-e-mail op basis van een test gebruiker met de naam **Julia Simon**.
Voor een goede werking van eenmalige aanmelding moet er een koppelings relatie tussen een Azure AD-gebruiker en de bijbehorende gebruiker in SkyDesk-e-mail worden gemaakt.

Als u eenmalige aanmelding voor Azure AD wilt configureren en testen met SkyDesk-e-mail, moet u de volgende bouw stenen volt ooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding van SkyDesk-e-mail configureren](#configure-skydesk-email-single-sign-on)** : Hiermee configureert u de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Maak een SkyDesk-e-mail test gebruiker](#create-skydesk-email-test-user)** -een equivalent van Julia Simon in SkyDesk-e-mail dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)**: als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding voor Azure AD te configureren met SkyDesk-e-mail:

1. Selecteer in de [Azure Portal](https://portal.azure.com/)op de pagina integratie van **SkyDesk-e-mail** toepassing de optie **eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Informatie over eenmalige aanmelding van SkyDesk-e-mail domein en Url's](common/sp-signonurl.png)

    In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://mail.skydesk.jp/portal/<companyname>`

    > [!NOTE]
    > De waarde is niet echt. Werk de waarde bij met de werkelijke aanmeldings-URL. Neem contact op met het [ondersteunings team van SkyDesk email client](https://www.skydesk.jp/apps/support/) om de waarde op te halen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

6. Kopieer in het gedeelte **SkyDesk-e-mail instellen** de gewenste URL ('s) volgens uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-skydesk-email-single-sign-on"></a>Eenmalige aanmelding voor SkyDesk-e-mail configureren

1. Meld u in een andere webbrowser aan bij uw SkyDesk-e-mail account als beheerder.

1. Klik in het menu aan de bovenkant op **instellen**en selecteer **org**.

    ![Eenmalige aanmelding configureren](./media/skydeskemail-tutorial/tutorial_skydeskemail_51.png)
  
1. Klik op **domeinen** in het linkerdeel venster.

    ![Eenmalige aanmelding configureren](./media/skydeskemail-tutorial/tutorial_skydeskemail_53.png)

1. Klik op **domein toevoegen**.

    ![Eenmalige aanmelding configureren](./media/skydeskemail-tutorial/tutorial_skydeskemail_54.png)

1. Voer uw domein naam in en controleer het domein.

    ![Eenmalige aanmelding configureren](./media/skydeskemail-tutorial/tutorial_skydeskemail_55.png)

1. Klik op **SAML-verificatie** in het linkerdeel venster.

    ![Eenmalige aanmelding configureren](./media/skydeskemail-tutorial/tutorial_skydeskemail_52.png)

1. Voer de volgende stappen uit op de pagina het dialoog venster **SAML-verificatie** :

    ![Eenmalige aanmelding configureren](./media/skydeskemail-tutorial/tutorial_skydeskemail_56.png)

    > [!NOTE]
    > Als u op SAML gebaseerde verificatie wilt gebruiken, moet u de installatie van de domein-of **Portal-URL** hebben **geverifieerd** . U kunt de Portal-URL instellen met de unieke naam.

    ![Eenmalige aanmelding configureren](./media/skydeskemail-tutorial/tutorial_skydeskemail_57.png)

    a. Plak in het tekstvak **aanmeldings-URL** de waarde van de **aanmeldings-URL**die u van Azure Portal hebt gekopieerd.

    b. Plak in het tekstvak **Afmeldings** -URL de waarde van de **afmeldings-URL**, die u van Azure Portal hebt gekopieerd.

    c. Het wijzigen van de **wachtwoord-URL** is optioneel. laat het veld leeg.

    d. Klik op **ophalen sleutel van bestand** om het gedownloade certificaat te selecteren van Azure Portal en klik vervolgens op **openen** om het certificaat te uploaden.

    e. Selecteer **RSA** als **algoritme**.

    f. Klik op **OK** om de wijzigingen op te slaan.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam****Britta Simon**in.
  
    b. In het veld **Gebruikersnaam** typt u **brittasimon@yourcompanydomain.extension**.  
    Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik op **maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u Julia Simon in om de eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan SkyDesk-e-mail.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**, selecteer **alle toepassingen**en selecteer vervolgens **SkyDesk-e-mail**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst toepassingen de optie **SkyDesk-e-mail**.

    ![De e-mail koppeling SkyDesk in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoog venster **gebruikers en groepen** **Julia Simon** in de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.

6. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-skydesk-email-test-user"></a>Test gebruiker voor SkyDesk-e-mail maken

In deze sectie maakt u een gebruiker met de naam Julia Simon in SkyDesk-e-mail.

Klik op **gebruikers toegang** via het linkerpaneel in SkyDesk-e-mail en voer uw gebruikers naam in.

![Eenmalige aanmelding configureren](./media/skydeskemail-tutorial/tutorial_skydeskemail_58.png)

> [!NOTE]
> Als u bulk gebruikers wilt maken, moet u contact opnemen met het [ondersteunings team van de SkyDesk-e-mailclient](https://www.skydesk.jp/apps/support/).

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel E-mail SkyDesk in het toegangs venster klikt, moet u automatisch worden aangemeld bij de SkyDesk-e-mail waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

