---
title: 'Zelfstudie: Azure Active Directory-integratie met xMatters OnDemand | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en xMatters OnDemand.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/29/2019
ms.author: jeedes
ms.openlocfilehash: 9e84be9cbc6aa50f4beb3ed333b47459b15fbcc1
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/18/2020
ms.locfileid: "88546279"
---
# <a name="tutorial-azure-active-directory-integration-with-xmatters-ondemand"></a>Zelfstudie: Azure Active Directory-integratie met xMatters OnDemand

In deze zelfstudie leert u hoe u xMatters OnDemand integreert met Azure Active Directory (Azure AD).
xMatters OnDemand integreren met Azure AD biedt u de volgende voordelen:

* U kunt in Azure AD beheren wie toegang heeft tot xMatters OnDemand.
* U kunt inschakelen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij xMatters OnDemand (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie met xMatters OnDemand wilt integreren, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) krijgen.
* Een xMatters OnDemand-abonnement waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* xMatters OnDemand biedt ondersteuning voor door **IDP** geïnitieerde eenmalige aanmelding

## <a name="adding-xmatters-ondemand-from-the-gallery"></a>xMatters OnDemand toevoegen vanuit de galerie

Voor het configureren van de integratie van xMatters OnDemand in Azure AD moet u xMatters OnDemand vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om xMatters OnDemand toe te voegen vanuit de galerie:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ in het zoekvak **xMatters OnDemand**, selecteer in het resultatenvenster **xMatters OnDemand** en klik vervolgens op de knop **Toevoegen** om de toepassing toe te voegen.

     ![xMatters OnDemand in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u Azure AD-eenmalige aanmelding met xMatters OnDemand op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in xMatters OnDemand tot stand is gebracht.

Als u Azure AD-eenmalige aanmelding wilt testen met xMatters OnDemand hebt u de volgende bouwstenen nodig:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding voor xMatters OnDemand configureren](#configure-xmatters-ondemand-single-sign-on)** : als u de instellingen voor eenmalige aanmelding aan de clientzijde wilt configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Een xMatters OnDemand-testgebruiker maken](#create-xmatters-ondemand-test-user)** : als u een equivalent van Britta Simon in xMatters OnDemand wilt dat is gekoppeld aan de Azure AD-versie van die gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om Azure AD-eenmalige aanmelding met xMatters OnDemand te configureren:

1. In [Azure Portal](https://portal.azure.com/) selecteert u op de integratiepagina van de toepassing **xMatters OnDemand** de optie **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. Op de pagina **Eenmalige aanmelding instellen met SAML** voert u de volgende stappen uit:

    ![Informatie over eenmalige aanmelding bij het xMatters OnDemand-domein en xMatters OnDemand-URL's](common/idp-intiated.png)

    a. In het tekstvak **Id** typt u een URL met het volgende patroon:

    - `https://<companyname>.au1.xmatters.com.au/`
    - `https://<companyname>.cs1.xmatters.com/`
    - `https://<companyname>.xmatters.com/`
    - `https://www.xmatters.com`
    - `https://<companyname>.xmatters.com.au/`

    b. In het tekstvak **Antwoord-URL** typt u een URL met het volgende patroon:

    - `https://<companyname>.au1.xmatters.com.au`
    - `https://<companyname>.xmatters.com/sp/<instancename>`
    - `https://<companyname>.cs1.xmatters.com/sp/<instancename>`
    - `https://<companyname>.au1.xmatters.com.au/<instancename>`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id en antwoord-URL. Neem contact op met het [xMatters OnDemand Client-ondersteuningsteam](https://www.xmatters.com/company/contact-us/) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

    > [!IMPORTANT]
    > U moet het certificaat doorsturen naar het [xMatters OnDemand-ondersteuningsteam](https://www.xmatters.com/company/contact-us/). Het certificaat moet worden geüpload door het xMatters-ondersteuningsteam voordat u de configuratie voor eenmalige aanmelding kunt voltooien.

6. In de sectie **xMatters OnDemand instellen** kopieert u de juiste URL('s) voor uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-xmatters-ondemand-single-sign-on"></a>xMatters OnDemand-eenmalige aanmelding configureren

1. Meld u in een andere browser als beheerder aan bij uw bedrijfssite in xMatters OnDemand.

2. Klik in de werkbalk bovenaan op **Beheerder**en klik vervolgens op **Bedrijfsgegevens** in de navigatiebalk aan de linkerkant.

    ![Beheerder](./media/xmatters-ondemand-tutorial/IC776795.png "Beheerder")

3. Voer de volgende stappen uit op de pagina **SAML Configuration**:

    ![SAML-configuratie](./media/xmatters-ondemand-tutorial/IC776796.png "SAML-configuratie")

    a. Selecteer **SAML inschakelen**.

    b. Plak in het tekstvak **Id van id-provider** de waarde van de **Azure AD-id** die u hebt gekopieerd uit de Azure Portal.

    c. Plak in het tekstvak **Eenmalige aanmeldings-URL** de waarde van de **aanmeldings-URL** die u uit de Azure Portal hebt gekopieerd.

    d. Plak in het tekstvak **Eenmalige afmeldings-URL** de waarde van **Afmeldings-URL** die u uit de Azure Portal hebt gekopieerd.

    e. Klik op de pagina Bedrijfsgegevens bovenaan op **Wijzigingen opslaan**.

    ![Bedrijfsgegevens](./media/xmatters-ondemand-tutorial/IC776797.png "Bedrijfsgegevens")

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken 

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **Nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam** **Britta Simon**in.
  
    b. In het veld **Gebruikersnaam** typt u brittasimon@yourcompanydomain.extension. Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie gaat u het gebruik van eenmalige aanmelding van Azure mogelijk maken voor Britta Simon door haar toegang te geven tot xMatters OnDemand.

1. Selecteer in Azure Portal **Bedrijfstoepassingen**, selecteer **Alle toepassingen** en selecteer vervolgens **xMatters OnDemand**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst toepassingen **xMatters OnDemand**.

    ![De xMatters OnDemand-koppeling in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-xmatters-ondemand-test-user"></a>xMatters OnDemand testgebruiker maken

Het doel van deze sectie is om in xMatters OnDemand een gebruiker te maken met de naam Britta Simon.

**Als u de gebruiker handmatig moet maken, voert u de volgende stappen uit:**

1. Meld u aan bij uw **xMatters OnDemand** tenant.

2. Klik op het tabblad **Gebruikers** en klik vervolgens op **Gebruiker toevoegen**.

    ![Gebruikers](./media/xmatters-ondemand-tutorial/IC781048.png "Gebruikers")

3. Voer in de sectie **Gebruiker toevoegen** de volgende stappen uit:

    ![Een gebruiker toevoegen](./media/xmatters-ondemand-tutorial/IC781049.png "Een gebruiker toevoegen")

    a. Selecteer **Active**.

    b. Typ in het tekstvak **Gebruikers-id** de gebruikers-id in van de gebruiker, bijvoorbeeld Brittasimon@contoso.com.

    c. Typ in het tekstvak **First Name** de voornaam van de gebruiker, bijvoorbeeld Britta.

    d. Typ in het tekstvak **Last Name** de achternaam van de gebruiker, bijvoorbeeld Simon.

    e. Voer in het tekstvak **Site** de geldige site in van een geldige Azure AD-account die u wilt inrichten.

    f. Klik op **Opslaan**.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel xMatters OnDemand in het toegangsvenster klikt, wordt u automatisch aangemeld bij de instantie van xMatters OnDemand waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

