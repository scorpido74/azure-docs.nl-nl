---
title: 'Zelf studie: integratie Azure Active Directory met Adobe Creative Cloud | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Adobe Creative Cloud.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: c199073f-02ce-45c2-b515-8285d4bbbca2
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/15/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8f3fdd1aa4fa655cfffb9e9a2f29d551133613a8
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73154011"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-creative-cloud"></a>Zelf studie: integratie Azure Active Directory met Adobe Creative Cloud

In deze zelfstudie leert u hoe u Adobe Creative Cloud integreert met Azure Active Directory (Azure AD).
De integratie van Adobe Creative Cloud met Azure AD heeft de volgende voordelen:

* U kunt in Azure AD beheren wie toegang tot Adobe Creative Cloud heeft.
* U kunt instellen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij Adobe Creative Cloud (eenmalige aanmelding of SSO).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Voor het configureren van de Azure AD-integratie met Adobe Creative Cloud hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Abonnement op Adobe Creative Cloud waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Adobe Creative Cloud ondersteunt door **SP** geïnitieerde eenmalige aanmelding

## <a name="adding-adobe-creative-cloud-from-the-gallery"></a>Adobe Creative Cloud toevoegen vanuit de galerie

Om de integratie van Adobe Creative Cloud met Azure AD te configureren, moet u Adobe Creative Cloud vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Ga als volgt te werk om Adobe Creative Cloud vanuit de galerie toe te voegen:**

1. Klik in het linkernavigatievenster in de  **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Adobe Creative Cloud** in het zoekvak, selecteer **Adobe Creative Cloud** in het deelvenster met resultaten en klik vervolgens op **Toevoegen** om de toepassing toe te voegen.

    ![Adobe Creative Cloud in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In dit gedeelte gaat u eenmalige aanmelding bij Adobe Creative Cloud met Azure AD configureren en testen op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerd gebruiker in Adobe Creative Cloud tot stand is gebracht.

Om eenmalige aanmelding bij Adobe Creative Cloud met Azure AD te configureren en testen, moet u de volgende procedures voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding bij Adobe Creative Cloud configureren](#configure-adobe-creative-cloud-single-sign-on)** : de instellingen voor eenmalige aanmelding aan de clientzijde configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Een testgebruiker voor Adobe Creative Cloud definiëren](#create-adobe-creative-cloud-test-user)**  - een tegenhanger voor Britta Simon definiëren in Adobe Creative Cloud die is gekoppeld aan de Azure AD-voorstelling van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding met Azure AD te configureren voor Adobe Creative Cloud:

1. Ga in de [Azure-portal](https://portal.azure.com/) naar de overzichtspagina van de integratie voor **Adobe Creative Cloud** en selecteer **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Informatie over eenmalige aanmelding voor domein en URL's van Adobe Creative Cloud](common/sp-identifier.png)

    a. Typ `https://adobe.com` in het tekstvak **Aanmeldings-URL**.

    b. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://www.okta.com/saml2/service-provider/<token>`

    > [!NOTE]
    > De id-waarde is niet echt. Werk deze waarde bij met de werkelijke id. Neem contact op met het [ondersteuningsteam van Adobe Creative Cloud](https://www.adobe.com/au/creativecloud/business/teams/plans.html) om deze waarde te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. De toepassing Adobe Creative Cloud verwacht dat de SAML-asserties een specifieke indeling hebben. Configureer de volgende claims voor deze toepassing. U kunt de waarden van deze kenmerken vanuit de sectie **Gebruikerskenmerken** op de integratiepagina van de toepassing-beheren. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op de knop **Bewerken** om het dialoogvenster **Gebruikerskenmerken** te openen.

    ![image](common/edit-attribute.png)

6. In de sectie **Gebruikersclaims** in het dialoogvenster **Gebruikerskenmerken** configureert u het kenmerk van het SAML-token zoals wordt weergegeven in de bovenstaande afbeelding en voert u de volgende stappen uit:

    | Naam | Bronkenmerk|
    |----- | --------- |
    | FirstName | user.givenname |
    | LastName | user.surname |
    | E-mail | user.mail

    a. Klik op **Nieuwe claim toevoegen** om het dialoogvenster **Gebruikersclaims beheren** te openen.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. In het tekstvak **Naam** typt u de naam van het kenmerk die voor die rij wordt weergegeven.

    c. Laat **Naamruimte** leeg.

    d. Selecteer Bron bij **Kenmerk**.

    e. Typ de kenmerkwaarde voor die rij in de lijst met **bronkenmerken**.

    f. Klik op **OK**.

    g. Klik op **Opslaan**.

    > [!NOTE]
    > De waarde voor de claim Email kan alleen worden gevuld in het SAML-antwoord als gebruikers beschikken over een geldige licentie voor Office 365 ExO.

7. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

8. Kopieer in het gedeelte **Adobe Creative Cloud instellen** de juiste URL('s) overeenkomstig wat u nodig hebt.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-adobe-creative-cloud-single-sign-on"></a>Eenmalige aanmelding bij Adobe Creative Cloud configureren

1. Meld u in een ander browservenster als beheerder aan bij [Adobe Admin Console](https://adminconsole.adobe.com).

2. Ga naar **Settings** in de bovenste navigatiebalk en kies **Identity**. De lijst met domeinen wordt geopend. Klik op de koppeling **Configure** voor uw domein. Voer de volgende stappen uit in het gedeelte **Single Sign On Configuration Required**. Zie [Set up domains](https://helpx.adobe.com/enterprise/using/set-up-domain.html) voor meer informatie.

    ![Instellingen](https://helpx.adobe.com/content/dam/help/en/enterprise/using/configure-microsoft-azure-with-adobe-sso/_jcr_content/main-pars/procedure_719391630/proc_par/step_3/step_par/image/edit-sso-configuration.png "Instellingen")

    a. Klik op **Browse** om het gedownloade certificaat van Azure AD te uploaden naar **IDP Certificate**.

    b. Plak in het tekstvak **IDP Issuer** de waarde van **Id (entiteits-id)** die u hebt gekopieerd uit het gedeelte **Aanmelding configureren** in de Azure-portal.

    c. Plak in het tekstvak **IDP login URL** de waarde van **SAML SSO Service-URL** die u hebt gekopieerd uit het gedeelte **Aanmelding configureren** in de Azure-portal.

    d. Selecteer **HTTP - Redirect** bij **IDP Binding**.

    e. Selecteer **Email** bij **User login setting**.

    f. Klik op de knop **Save**.

3. In het dashboard wordt nu het XML-bestand **Download Metadata** weergegeven. Dit bestand bevat de EntityDescriptor-URL en AssertionConsumerService-URL van Adobe. Open het bestand en configureer de URL's in de Azure AD-toepassing.

    ![Eenmalige aanmelding aan app-zijde configureren](./media/adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_003.png)

    a. Gebruik de waarde voor de EntityDescriptor die u van Adobe hebt ontvangen voor **Id** in het dialoogvenster **App-instellingen configureren**.

    b. Gebruik de waarde voor de AssertionConsumerService die u van Adobe hebt ontvangen voor **Antwoord-URL** in het dialoogvenster **App-instellingen configureren**.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **Nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam** **Britta Simon**in.
  
    b. Typ in het veld **gebruikers naam** **brittasimon\@yourcompanydomain. extension**  
    Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik op **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In dit gedeelte geeft u Britta Simon de mogelijkheid om eenmalige aanmelding van Azure te gebruiken door haar toegang te geven tot Adobe Creative Cloud.

1. Selecteer in de Azure-portal achtereenvolgens **Bedrijfstoepassingen**, **Alle toepassingen** en **Adobe Creative Cloud**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Adobe Creative Cloud** in de lijst met toepassingen.

    ![De koppeling naar Adobe Creative Cloud in de lijst met toepassingen](common/all-applications.png)

3. Selecteer **Gebruikers en groepen** in het menu aan de linkerkant.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-adobe-creative-cloud-test-user"></a>Een testgebruiker voor Adobe Creative Cloud definiëren

Om Azure AD-gebruikers toegang te bieden tot Adobe Creative Cloud, moeten ze worden ingericht in Adobe Creative Cloud. In het geval van Adobe Creative Cloud moet dit handmatig gebeuren.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Ga als volgt te werk om een gebruikersaccount in te richten:

1. Meld u als een beheerder aan bij [Adobe Admin Console](https://adminconsole.adobe.com).

2. Voeg de gebruiker toe als een federatieve id en wijs een productprofiel toe aan de gebruiker. Zie [Add users in Adobe Admin Console](https://helpx.adobe.com/enterprise/using/users.html#Addusers) voor uitgebreide informatie over het toevoegen van gebruikers. 

3. Typ uw e-mailadres/upn in het aanmeldingsformulier van Adobe en druk op tab om als federatieve gebruiker terug te gaan naar Azure AD:
   * Webtoegang: www\.adobe.com >-aanmelding
   * In het hulpprogramma voor desktop-app > aanmelden
   * In de toepassing > help > aanmelden

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Adobe Creative Cloud klikt in het toegangsvenster, wordt u als het goed is automatisch aangemeld bij het exemplaar van Adobe Creative Cloud waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
  
- [Set up domains (adobe.com)](https://helpx.adobe.com/enterprise/using/set-up-domain.html)
  
- [Configure Microsoft Azure for use with Adobe SSO (adobe.com)](https://helpx.adobe.com/enterprise/kb/configure-microsoft-azure-with-adobe-sso.html)
