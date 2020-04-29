---
title: 'Zelf studie: integratie Azure Active Directory met mensheid | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Humanity.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 6aa771e9-31c6-48d1-8dde-024bebc06943
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/15/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: fd7cfe93aa39982836944230f860c603c7117ceb
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "74233439"
---
# <a name="tutorial-azure-active-directory-integration-with-humanity"></a>Zelf studie: integratie Azure Active Directory met mensheid

In deze zelfstudie leert u hoe u Humanity kunt integreren met Azure Active Directory (Azure AD).
De integratie van Humanity met Azure AD biedt de volgende voordelen:

* U kunt in Azure AD bepalen wie er toegang heeft tot Humanity.
* U kunt inschakelen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij Humanity (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u nog geen abonnement op Azure hebt, [Maak dan een gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Om Azure AD-integratie te configureren met Humanity hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Een abonnement op Humanity waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Humanity ondersteunt door **SP** geïnitieerde eenmalige aanmelding

## <a name="adding-humanity-from-the-gallery"></a>Humanity toevoegen vanuit de galerie

Voor het configureren van de integratie van Humanity met Azure AD moet u Humanity vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit als u Humanity wilt toevoegen vanuit de galerie:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Humanity** in het zoekvak, selecteer **Humanity** in het resultaatvenster en klik vervolgens op de knop **Toevoegen** om de toepassing toe te voegen.

     ![Humanity in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie gaat u Azure AD-eenmalige aanmelding voor Humanity configureren en testen op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in Humanity tot stand is gebracht.

Als u Azure AD-eenmalige aanmelding wilt configureren en testen met Humanity, dient u de volgende bouwstenen te voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Humanity-eenmalige aanmelding configureren](#configure-humanity-single-sign-on)**: als u de instellingen voor eenmalige aanmelding aan de clientzijde wilt configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Testgebruiker voor Humanity maken](#create-humanity-test-user)**: als u een tegenhanger van Britta Simon in Humanity wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)**: als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit als u Azure AD-eenmalige aanmelding wilt configureren met Humanity:

1. In de [Azure-portal](https://portal.azure.com/), op de integratiepagina van de **Humanity**-toepassing, selecteert u **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Gegevens voor domein en URL's voor eenmalige aanmelding bij Humanity](common/sp-identifier.png)

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://company.humanity.com/includes/saml/`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met het volgende patroon: `https://company.humanity.com/app/`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en -id. Neem contact op met het [Klantondersteuningsteam van Humanity](https://www.humanity.com/support/) (Engelstalig) om deze waarden op te halen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

4. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

6. Kopieer in de sectie **Set up Humanity** de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-humanity-single-sign-on"></a>Eenmalige aanmelding van Humanity configureren

1. Meld u in een ander browservenster als een beheerder aan bij de bedrijfssite van **Humanity**.

2. Klik in het menu bovenaan op **Admin**.

    ![Beheerder](./media/shiftplanning-tutorial/iC786619.png "Beheerder")
3. Klik onder **Integration** op **Single Sign-On**.

    ![Eenmalige aanmelding](./media/shiftplanning-tutorial/iC786620.png "Eenmalige aanmelding")

4. Voer de volgende stappen uit in de sectie **eenmalige aanmelding** :

    ![Eenmalige aanmelding](./media/shiftplanning-tutorial/iC786905.png "Eenmalige aanmelding")

    a. Selecteer **SAML Enabled**.

    b. Selecteer **Allow Password Login**.

    c. Plak in het tekstvak **SAML endpoint URL** de waarde van **Aanmeldings-URL** die u uit de Azure-portal hebt gekopieerd.

    d. Plak in het tekstvak **Remote Logout URL** de waarde van de **afmeldings-URL** die u uit de Azure-portal hebt gekopieerd.

    e. Open in Kladblok het met Base 64 gecodeerde certificaat, kopieer de inhoud ervan naar het Klembord en plak deze vervolgens in het tekstvak **X.509 Certificate**.

    f. Klik op **instellingen opslaan**.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam****Britta Simon**in.
  
    b. Typ in het veld **gebruikers naam** **brittasimon\@yourcompanydomain. extension**  
    Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik op **maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie gaat u Britta Simon toestemming geven voor gebruik van eenmalige aanmelding met Azure door haar toegang te geven tot Humanity.

1. Selecteer in de Azure-portal achtereenvolgens **Bedrijfstoepassingen**, **Alle toepassingen** en **Humanity**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Humanity** in de lijst met toepassingen.

    ![De koppeling Humanity in de lijst Toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoog venster **gebruikers en groepen** **Julia Simon** in de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.

6. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-humanity-test-user"></a>Humanity-testgebruiker maken

Als u wilt dat Azure AD-gebruikers zich kunnen aanmelden bij Humanity, moeten ze worden ingericht in Humanity. In het geval van Humanity wordt het inrichten handmatig uitgevoerd.

**Voer de volgende stappen uit als u een gebruikersaccount wilt inrichten:**

1. Meld u als een beheerder aan bij de bedrijfssite van **Humanity**.

2. Klik op **Admin**.

    ![Beheerder](./media/shiftplanning-tutorial/iC786619.png "Beheerder")

3. Klik op **Staff**.

    ![Mede](./media/shiftplanning-tutorial/ic786623.png "Mede")

4. Klik onder **Actions** op **Add Employees**.

    ![Werk nemers toevoegen](./media/shiftplanning-tutorial/iC786624.png "Werk nemers toevoegen")

5. Voer in de sectie **Add Employees** de volgende stappen uit:

    ![Werk nemers opslaan](./media/shiftplanning-tutorial/iC786625.png "Werk nemers opslaan")

    a. Typ de **voor naam**, **Achternaam**en het **e-mail adres** van een geldige Azure ad-account die u wilt inrichten in de bijbehorende tekst vakken.

    b. Klik op **Save Employees**.

> [!NOTE]
> U kunt alle andere hulpprogram ma's voor het maken van mensheid-gebruikers accounts of Api's die worden geleverd door mensheid, gebruiken om Azure AD-gebruikers accounts in te richten.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Humanity in het toegangsvenster klikt, wordt u automatisch aangemeld bij de instantie van Humanity waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)