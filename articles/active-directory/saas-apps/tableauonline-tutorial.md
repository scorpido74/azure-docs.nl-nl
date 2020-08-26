---
title: 'Zelfstudie: Azure Active Directory-integratie met Tableau Online | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding tussen Azure Active Directory en Tableau Online kunt configureren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.openlocfilehash: 035bc13ee44a8a003ae860eb2bdd67432fa91f14
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/18/2020
ms.locfileid: "88542509"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-tableau-online"></a>Zelfstudie: Integratie van eenmalige aanmelding (SSO) van Azure Active Directory met Tableau Online

In deze zelfstudie leert u hoe u Tableau Online kunt integreren met Azure AD (Active Directory). Wanneer u Tableau Online integreert met Azure AD, kunt u het volgende doen:

* In Azure AD bepalen wie er toegang heeft tot Tableau Online.
* Ervoor zorgen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij Tableau Online.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op Tableau Online waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Tableau Online biedt ondersteuning voor eenmalige aanmelding die is gestart vanuit de **SP**
* Zodra u Tableau Online hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-tableau-online-from-the-gallery"></a>Tableau Online toevoegen vanuit de galerie

Voor het configureren van de integratie van Tableau Online met Azure AD moet u Tableau Online uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen vanuit de galerie** **Tableau Online** in het zoekvak.
1. Selecteer **Tableau Online** in de resultaten en voeg de app vervolgens toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In dit gedeelte configureert en test u eenmalige aanmelding voor Azure AD met Tableau Online op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in Tableau Online tot stand is gebracht.

Voltooi de volgende procedures om Azure AD-eenmalige aanmelding met Tableau Online te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding met Tableau Online configureren](#configure-tableau-online-sso)** : als u de instellingen voor eenmalige aanmelding aan de clientzijde wilt configureren.
    1. **[Tableau Online-testgebruiker maken](#create-tableau-online-test-user)** : als tegenhanger van B.Simon in Tableau Online die is gekoppeld aan de representatie van een gebruiker in Azure AD.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding voor Azure AD met Tableau Online te configureren:

1. Ga in [Azure Portal](https://portal.azure.com/) naar de pagina voor de integratie van de toepassing **Tableau Online** en selecteer **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Domein- en URL-gegevens voor eenmalige aanmelding bij Tableau Online](common/sp-identifier.png)

    a. In het tekstvak **Aanmeldings-URL** typt u de URL: `https://sso.online.tableau.com/public/sp/login?alias=<entityid>`

    b. Typ in het tekstvak **Id (Entiteits-id)** de volgende URL: `https://sso.online.tableau.com/public/sp/metadata?alias=<entityid>`

    > [!NOTE]
    > U krijgt de waarde voor `<entityid>` in de sectie **Tableau Online instellen** van deze zelfstudie. De waarde voor de entiteits-id gebruikt u als de waarde voor **Azure AD identifier** in de sectie **Tableau Online instellen**.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens**  te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

6. Kopieer in de sectie **Tableau Online instellen** de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **Nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam** **Britta Simon**in.
  
    b. In het veld **Gebruikersnaam** typt u **brittasimon\@yourcompanydomain.extension**  
    Bijvoorbeeld BrittaSimon\@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In dit gedeelte stelt u Britta Simon in staat om gebruik te maken van eenmalige aanmelding van Azure door haar toegang te geven tot Tableau Online.

1. Selecteer **Bedrijfstoepassingen** in Azure Portal, selecteer **Alle toepassingen** en selecteer vervolgens **Tableau Online**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Tableau Online** in de lijst met toepassingen.

    ![De koppeling naar Tableau Online in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-tableau-online-sso"></a>Eenmalige aanmelding configureren in Tableau Online

1. Meld u in een ander browservenster aan bij Tableau Online. Ga naar **Settings** en daarna **Authentication**.

    ![Eenmalige aanmelding configureren](./media/tableauonline-tutorial/tutorial_tableauonline_09.png)

2. Als u SAML wilt inschakelen, doet u het volgende onder **Authentication types**. Selecteer **Enable an additional authentication method** en schakel vervolgens het selectievakje **SAML** in.

    ![Eenmalige aanmelding configureren](./media/tableauonline-tutorial/tutorial_tableauonline_12.png)

3. Schuif omlaag naar de sectie **Import metadata file into Tableau Online**.  Klik op Browse om het bestand met metagegevens dat u hebt gedownload uit Azure AD te importeren. Klik vervolgens op **Apply**.

   ![Eenmalige aanmelding configureren](./media/tableauonline-tutorial/tutorial_tableauonline_13.png)

4. Geef in de sectie **Match assertions** waarden van de id-provider op voor **email address**, **first name** en **last name**. Als u deze informatie wilt ophalen uit Azure AD: 
  
    a. Ga in Azure Portal naar de pagina voor de integratie van de toepassing **Tableau Online**.

    b. Klik in de sectie **Gebruikerskenmerken en claims** op het pictogram Bewerken.

   ![Eenmalige aanmelding configureren](./media/tableauonline-tutorial/attributesection.png)

    c. Kopieer de waarde van de naamruimte voor deze kenmerken: givenname, e-mail en achternaam. Gebruik hiervoor de volgende stappen:

   ![Eenmalige aanmelding van Azure AD](./media/tableauonline-tutorial/tutorial_tableauonline_10.png)

    d. Klik op de waarde voor **user.givenname**

    e. Kopieer de waarde uit het tekstvak **Naamruimte**.

    ![Eenmalige aanmelding configureren](./media/tableauonline-tutorial/attributesection2.png)

    f. Als u de waarden van de naamruimte voor het e-mailadres en de achternaam wilt kopiëren, herhaalt u de bovenstaande stappen.

    g. Ga naar Tableau Online en stel de sectie **User Attributes & Claims** als volgt in:

    * Email: **mail** of **userprincipalname**

    * First name: **givenname**

    * Last name: **surname**

    ![Eenmalige aanmelding configureren](./media/tableauonline-tutorial/tutorial_tableauonline_14.png)

### <a name="create-tableau-online-test-user"></a>Een testgebruiker voor Tableau Online maken

In dit gedeelte maakt u in Tableau Online een gebruiker met de naam Britta Simon.

1. Klik in **Tableau Online** op **Settings** en open daarna de sectie **Authentication**. Schuif omlaag naar de sectie **Manage Users**. Klik achtereenvolgens op **Add Users** en **Enter Email Addresses**.
  
    ![Een Azure AD-testgebruiker maken](./media/tableauonline-tutorial/tutorial_tableauonline_15.png)

2. Selecteer **Add users for (SAML) authentication**. Voeg in het tekstvak **Enter email addresses** britta.simon\@contoso.com toe.
  
    ![Een Azure AD-testgebruiker maken](./media/tableauonline-tutorial/tutorial_tableauonline_11.png)

3. Klik op **Gebruikers toevoegen**.

### <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel Tableau Online klikt, wordt u automatisch aangemeld bij de instantie van Tableau Online waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)