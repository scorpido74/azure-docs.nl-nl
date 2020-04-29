---
title: 'Zelf studie: integratie Azure Active Directory met Marketo | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Marketo.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b88c45f5-d288-4717-835c-ca965add8735
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/19/2019
ms.author: jeedes
ms.openlocfilehash: 0488fd1e9bc10d61d6660745acfc8c39becf3a89
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "73159475"
---
# <a name="tutorial-azure-active-directory-integration-with-marketo"></a>Zelf studie: integratie met Marketo Azure Active Directory

In deze zelfstudie leert u hoe u Marketo kunt integreren met Azure Active Directory (Azure AD).
De integratie van Marketo met Azure AD biedt de volgende voordelen:

* U kunt in Azure AD bepalen wie er toegang heeft tot Marketo.
* U kunt instellen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij Marketo (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u nog geen abonnement op Azure hebt, [Maak dan een gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Om Azure AD-integratie te configureren met Marketo hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Een abonnement op Marketo waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Marketo ondersteunt door **IDP** geïnitieerde eenmalige aanmelding

## <a name="adding-marketo-from-the-gallery"></a>Marketo toevoegen vanuit de galerie

Om de integratie van Marketo in Azure AD te configureren, moet u Marketo vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om Marketo toe te voegen vanuit de galerie:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Marketo** in het zoekvak, selecteer **Marketo** in het deelvenster met resultaten en klik op de knop **Toevoegen** om de toepassing toe te voegen.

     ![Marketo in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie gaat u Azure AD-eenmalige aanmelding bij Marketo configureren en testen op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in Marketo tot stand is gebracht.

Als u eenmalige aanmelding van Azure AD met Marketo wilt configureren en testen, moet u de volgende bouwstenen voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding met Marketo configureren](#configure-marketo-single-sign-on)**: als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wil configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Testgebruiker maken voor Marketo](#create-marketo-test-user)**: als u een tegenhanger van Britta Simon in Marketo wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)**: als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit als u Azure AD-eenmalige aanmelding wilt configureren met Marketo:

1. In de [Azure-portal](https://portal.azure.com/) selecteert u **Eenmalige aanmelding** op de integratiepagina van de toepassing **Marketo**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. Op de pagina **Eenmalige aanmelding instellen met SAML** voert u de volgende stappen uit:

    ![Gegevens van domein en URL's voor eenmalige aanmelding van Marketo](common/idp-intiated.png)

    a. Typ in het tekstvak **id** een URL met het volgende patroon:`https://saml.marketo.com/sp`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://login.marketo.com/saml/assertion/\<munchkinid\>`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id en antwoord-URL. Neem contact op met het [ondersteuningsteam van Marketo](https://investors.marketo.com/contactus.cfm) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

6. In de sectie **Marketo instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-marketo-single-sign-on"></a>Marketo configureren voor eenmalige aanmelding

1. Om de Munchkin-id van uw toepassing te verkrijgen, meldt u zich met beheerdersreferenties bij Marketo aan en voert u de volgende acties uit:
   
    a. Meld u bij Marketo aan met beheerdersreferenties.
   
    b. Klik in het bovenste navigatievenster op de knop **Admin**.
   
    ![Eenmalige aanmelding configureren](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Navigeer naar het menu Integration en klik op de **Munchkin-koppeling**.
   
    ![Eenmalige aanmelding configureren](./media/marketo-tutorial/tutorial_marketo_11.png)
   
    d. Kopieer de Munchkin-id die wordt weergegeven in het scherm en vul uw antwoord-URL in de wizard Azure AD-configuratie in.
   
    ![Eenmalige aanmelding configureren](./media/marketo-tutorial/tutorial_marketo_12.png) 

2. Voer onderstaande stappen uit om eenmalige aanmelding te configureren in de toepassing:
   
    a. Meld u bij Marketo aan met beheerdersreferenties.
   
    b. Klik in het bovenste navigatievenster op de knop **Admin**.
   
    ![Eenmalige aanmelding configureren](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Navigeer naar het menu Integration en klik op **Single Sign On**.
   
    ![Eenmalige aanmelding configureren](./media/marketo-tutorial/tutorial_marketo_07.png) 
   
    d. Klik op de knop **Edit** om de SAML-instellingen in te schakelen.
   
    ![Eenmalige aanmelding configureren](./media/marketo-tutorial/tutorial_marketo_08.png) 
   
    e. SSO-instellingen **ingeschakeld**.
   
    f. Plak de **Azure AD-id** in het tekstvak **Issuer-ID**.
   
    g. Voer in het tekstvak **Entity ID** de URL in als `http://saml.marketo.com/sp`.
   
    h. Selecteer de User ID Location **Name Identifier element**.
   
    ![Eenmalige aanmelding configureren](./media/marketo-tutorial/tutorial_marketo_09.png)
   
    > [!NOTE]
    > Als uw gebruikers-id geen UPN-waarde is, wijzig de waarde dan op het tabblad Attribute.
   
    i. Upload het certificaat dat u hebt gedownload van de Azure AD-configuratiewizard. **Sla** de instellingen op.
   
    j. Bewerk de instellingen op de pagina Redirect Pages.
   
    k. Plak de **Aanmeldings-URL** in het tekstvak **Login URL**.
   
    l. Plak de **Afmeldings-URL** in het tekstvak **Logout URL**.
   
    m. Plak in het tekstvak **Error URL****de URL van uw Marketo-exemplaar** en klik op **Save** om de instellingen op te slaan.
   
    ![Eenmalige aanmelding configureren](./media/marketo-tutorial/tutorial_marketo_10.png)

3. Voer de volgende acties uit om SSO in te schakelen voor gebruikers:
   
    a. Meld u bij Marketo aan met beheerdersreferenties.
   
    b. Klik in het bovenste navigatievenster op de knop **Admin**.
   
    ![Eenmalige aanmelding configureren](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Navigeer naar het menu **Security** en klik op **Login Settings**.
   
    ![Eenmalige aanmelding configureren](./media/marketo-tutorial/tutorial_marketo_13.png)
   
    d. Vink de optie **Require SSO** in en **sla de instellingen op**.
   
    ![Eenmalige aanmelding configureren](./media/marketo-tutorial/tutorial_marketo_14.png)

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

In deze sectie zorgt u ervoor dat Britta Simon gebruik kan maken van eenmalige aanmelding van Azure, door haar toegang te geven tot Marketo.

1. Selecteer **Bedrijfstoepassingen** in de Azure-portal, selecteer **Alle toepassingen** en selecteer vervolgens **Marketo**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Marketo** in de lijst met toepassingen.

    ![De koppeling Marketo in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoog venster **gebruikers en groepen** **Julia Simon** in de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.

6. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-marketo-test-user"></a>Marketo-testgebruiker maken

In deze sectie gaat u in Marketo een gebruiker maken met de naam Britta Simon. Volg deze stappen om een gebruiker te maken in het Marketo-platform.

1. Meld u bij Marketo aan met beheerdersreferenties.

2. Klik in het bovenste navigatievenster op de knop **Admin**.
   
    ![Eenmalige aanmelding configureren](./media/marketo-tutorial/tutorial_marketo_06.png) 

3. Navigeer naar het menu **Security** en klik op **User & Roles**
   
    ![Eenmalige aanmelding configureren](./media/marketo-tutorial/tutorial_marketo_19.png)  

4. Klik op het tabblad Users op de koppeling **Invite New User**
   
    ![Eenmalige aanmelding configureren](./media/marketo-tutorial/tutorial_marketo_15.png) 

5. Vul in de wizard Invite New User de volgende informatie in
   
    a. Voer het **e-mailadres** van de gebruiker in het tekstvak in
   
    ![Eenmalige aanmelding configureren](./media/marketo-tutorial/tutorial_marketo_16.png)
   
    b. Voer de **voornaam** in het tekstvak in
   
    c. Voer de **achternaam** in het tekstvak in
   
    d. Klik op **volgende**

6. Selecteer op het tabblad **Permissions** de optie **userRoles** en klik op **Next**
   
    ![Eenmalige aanmelding configureren](./media/marketo-tutorial/tutorial_marketo_17.png)
7. Klik op de knop **Send** om de gebruikersuitnodiging te verzenden
   
    ![Eenmalige aanmelding configureren](./media/marketo-tutorial/tutorial_marketo_18.png)

8. De gebruiker ontvangt de e-mailmelding en moet op de koppeling klikken en het wachtwoord wijzigen om het account te activeren. 

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel Marketo klikt, zou u automatisch moeten worden aangemeld bij het exemplaar van Marketo waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

