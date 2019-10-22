---
title: 'Zelf studie: integratie Azure Active Directory met Zscaler Beta | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Zscaler Beta.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 56b846ae-a1e7-45ae-a79d-992a87f075ba
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/24/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 07b0f8112f724c857ffb46378f7aa7ef605b9bbb
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/21/2019
ms.locfileid: "68943294"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-beta"></a>Zelf studie: integratie Azure Active Directory met Zscaler Beta

In deze zelfstudie leert u hoe u Zscaler Beta kunt integreren met Azure Active Directory (Azure AD).
Wanneer u Zscaler Beta integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot Zscaler Beta.
* Toestaan dat uw gebruikers automatisch worden aangemeld bij Zscaler Beta met hun Azure AD-accounts. Dit toegangs beheer wordt eenmalige aanmelding (SSO) genoemd.
* Beheer uw accounts op één centrale locatie met behulp van de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van een SaaS-app (Software as a Service) met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u integratie tussen Azure AD met Zscaler Beta wilt configureren, hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* Een Zscaler-bèta abonnement dat gebruikmaakt van eenmalige aanmelding.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Zscaler beta ondersteunt door SP geïnitieerde SSO.
* Zscaler Beta biedt ondersteuning voor Just-in-time-gebruikers inrichting.

## <a name="add-zscaler-beta-from-the-azure-marketplace"></a>Zscaler Beta toevoegen vanuit Azure Marketplace

Als u de integratie van Zscaler beta in azure AD wilt configureren, voegt u Zscaler Beta van Azure Marketplace toe aan uw lijst met beheerde SaaS-apps.

Voer de volgende stappen uit om Zscaler Beta toe te voegen vanuit Azure Marketplace.

1. Selecteer **Azure Active Directory**in de [Azure Portal](https://portal.azure.com)in het navigatie deel venster links.

    ![Knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **bedrijfs toepassingen**en selecteer **alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u **Nieuwe toepassing** bovenaan het dialoogvenster.

    ![Knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Zscaler Beta**in het zoekvak. Selecteer **Zscaler Beta** in het deel venster result en selecteer vervolgens **toevoegen**.

     ![Zscaler Beta in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie kunt u eenmalige aanmelding voor Azure AD configureren en testen met Zscaler Beta op basis van de test gebruiker Julia Simon.
Als u eenmalige aanmelding wilt gebruiken, stelt u een koppelings relatie in tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Zscaler Beta.

Als u eenmalige aanmelding voor Azure AD wilt configureren en testen met Zscaler beta, voltooit u de volgende bouw stenen:

- [Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-single-sign-on) zodat uw gebruikers deze functie kunnen gebruiken.
- [Eenmalige aanmelding voor Zscaler Beta configureren](#configure-zscaler-beta-single-sign-on) om de instellingen voor eenmalige aanmelding aan de kant van de toepassing te configureren.
- [Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user) voor het testen van eenmalige aanmelding van Azure AD met Britta Simon.
- [De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user) zodat Britta Simon gebruik kan maken van eenmalige aanmelding van Azure AD.
- [Maak een Zscaler beta-test gebruiker](#create-a-zscaler-beta-test-user) voor een tegen hanger van Julia Simon in Zscaler bèta dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
- [Eenmalige aanmelding testen](#test-single-sign-on) om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding voor Azure AD te configureren met Zscaler Beta.

1. In de [Azure-portal](https://portal.azure.com/) selecteert u **Zscaler Beta** op de integratiepagina van de toepassing **Eenmalige aanmelding**.

    ![Koppeling voor eenmalige aanmelding configureren](common/select-sso.png)

2. Selecteer in het dialoogvenster **Selecteer een methode voor eenmalige aanmelding** de modus **SAML/WS-Fed** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Selecteer op de pagina **eenmalige aanmelding met SAML instellen** de optie **bewerken** om het dialoog venster **eenvoudige SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. Voer de volgende stap uit in de sectie **basis configuratie van SAML** :

    ![Informatie over eenmalige aanmelding voor Zscaler Beta-domein en Url's](common/sp-intiated.png)

    - Voer in het vak **AANMELDINGS URL** de URL in die wordt gebruikt door uw gebruikers om zich aan te melden bij uw Zscaler-bèta toepassing.

    > [!NOTE]
    > De waarde is niet echt. Werk de waarde bij met de waarde voor de werkelijke aanmeldings URL. Neem contact op met het [ondersteunings team van de Zscaler Beta-Client](https://www.zscaler.com/company/contact)om de waarde op te halen.

5. De Zscaler-bèta toepassing verwacht de SAML-beweringen in een specifieke indeling. U moet aangepaste kenmerk toewijzingen toevoegen aan de configuratie van uw SAML-token kenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven. Selecteer **bewerken** om het dialoog venster **gebruikers kenmerken** te openen.

    ![Het dialoogvenster Gebruikerskenmerken](common/edit-attribute.png)

6. De Zscaler-bèta toepassing verwacht nog enkele kenmerken die opnieuw moeten worden door gegeven in het SAML-antwoord. Voer in de sectie **gebruikers claims** in het dialoog venster **gebruikers kenmerken** de volgende stappen uit om het SAML-token kenmerk toe te voegen, zoals wordt weer gegeven in de volgende tabel.
    
    | Naam | Bronkenmerk | 
    | ---------------| --------------- |
    | memberOf  | user.assignedroles |

    a. Selecteer **Nieuwe claim toevoegen** om het dialoogvenster **Gebruikersclaims beheren** te openen.

    ![Het dialoog venster gebruikers claims](common/new-save-attribute.png)

    ![Het dialoogvenster Gebruikersclaims beheren](common/new-attribute-details.png)

    b. Typ in het tekstvak **Naam** de naam van het kenmerk die voor die rij wordt weergegeven.

    c. Laat het vak **naam ruimte** leeg.

    d. Selecteer bij **bron**de optie **kenmerk**.

    e. Typ in de lijst **Bronkenmerk** de kenmerkwaarde voor die rij.

    f. Selecteer **OK**.

    g. Selecteer **Opslaan**.

    > [!NOTE]
    > Zie [Configure the Role claim](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-app-role-management)(Engelstalig) voor meer informatie over het configureren van rollen in azure AD.

7. Selecteer op de pagina **eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekening certificaat** de optie **downloaden** om het **certificaat (base64)** te downloaden. Sla het op uw computer op.

    ![De koppeling om het certificaat te downloaden](common/certificatebase64.png)

8. Kopieer in het gedeelte **set up Zscaler Beta** de url's die u nodig hebt voor uw vereisten:

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    - Aanmeldings-URL
    - Azure AD-id
    - Afmeldings-URL

### <a name="configure-zscaler-beta-single-sign-on"></a>Eenmalige aanmelding voor Zscaler Beta configureren

1. Als u de configuratie in Zscaler Beta wilt automatiseren, installeert u de **extensie mijn apps veilig aanmelden** door **de extensie installeren**te selecteren.

    ![Uitbrei ding voor mijn apps](common/install-myappssecure-extension.png)

2. Nadat u de uitbrei ding aan de browser hebt toegevoegd, kunt u **Zscaler Beta instellen** naar de Zscaler-bèta toepassing sturen. Geef de beheerders referenties op om u aan te melden bij Zscaler Beta. Met de browser extensie wordt de toepassing automatisch voor u geconfigureerd en wordt stap 3 tot en met 6 geautomatiseerd.

    ![Configuratie van Setup](common/setup-sso.png)

3. Als u Zscaler Beta hand matig wilt instellen, opent u een nieuw webbrowser venster. Meld u aan bij uw Zscaler Beta-bedrijfs site als beheerder en voer de volgende stappen uit.

4. Ga naar **beheer**  > **verificatie**  > **verificatie-instellingen**en voer de volgende stappen uit.
   
    ![Dienen](./media/zscaler-beta-tutorial/ic800206.png "Beheer")

    a. Onder **verificatie type**selecteert u **SAML**.

    b. Selecteer **SAML configureren**.

5. Voer in het venster **SAML bewerken** de volgende stappen uit: 
            
    ![Gebruikers & verificatie beheren](./media/zscaler-beta-tutorial/ic800208.png "Gebruikers & verificatie beheren")
    
    a. In het vak URL van de **SAML-Portal** plakt u de AANMELDINGS- **URL** die u hebt gekopieerd uit de Azure Portal.

    b. Voer **NameID**in het vak Naam van het **aanmeldings kenmerk** in.

    c. Selecteer **uploaden** in het vak **openbaar SSL-certificaat** om het Azure SAML-handtekening certificaat te uploaden dat u hebt gedownload van de Azure Portal.

    d. **Schakel automatische inrichting van SAML inschakelen**in.

    e. Geef in het vak **gebruikers weergave naam kenmerk** **DisplayName** op als u het inschakelen van SAML voor het autoinrichten van een klasse voor DisplayName-kenmerken.

    f. Voer in het vak Naam van het **kenmerk groep de waarde** **memberOf** in als u de functie voor het inschakelen van SAML in te scha kelen voor memberOf-kenmerken.

    g. Voer in het vak **afdelings naam** het veld **afdeling** in als u het inschakelen van SAML-autoprovisioning wilt toestaan voor kostenplaats kenmerken.

    h. Selecteer **Opslaan**.

6. Voer de volgende stappen uit op de pagina het dialoog venster **gebruikers verificatie configureren** :

    ![Activerings menu en knop activeren](./media/zscaler-beta-tutorial/ic800207.png)

    a. Beweeg de muis aanwijzer over het **activerings** menu aan de linkerkant.

    b. Selecteer **activeren**.

## <a name="configure-proxy-settings"></a>Proxy-instellingen configureren
Als u de proxy-instellingen in Internet Explorer wilt configureren, volgt u deze stappen.

1. Start **Internet Explorer**.

2. Selecteer **Internet opties** in het menu **extra** om het dialoog venster **Internet opties** te openen. 
    
     ![Het dialoog venster Internet opties](./media/zscaler-beta-tutorial/ic769492.png "Internet opties")

3. Selecteer het tabblad **verbindingen** . 
  
     ![Tabblad verbindingen](./media/zscaler-beta-tutorial/ic769493.png "Verbindingen")

4. Selecteer **LAN-instellingen** om het dialoog venster LAN **-instellingen** te openen.

5. Voer de volgende stappen uit in de sectie **proxy server** : 
   
    ![Sectie proxy server](./media/zscaler-beta-tutorial/ic769494.png "Proxy server")

    a. Schakel het selectie vakje **een proxy server voor uw LAN gebruiken** in.

    b. Voer in het vak **adres** de **gateway in. Zscaler Beta.net**.

    c. Typ **80**in het vak **poort** .

    d. Schakel het selectie vakje **proxy server niet gebruiken voor lokale adressen** in.

    e. Selecteer **OK** om het dialoog venster **LAN-instellingen** te sluiten.

6. Selecteer **OK** om het dialoog venster **Internet opties** te sluiten.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken 

Maak een test gebruiker in de Azure Portal met de naam Julia Simon.

1. Selecteer in de Azure-portal aan de linkerkant **Azure Active Directory** > **Gebruikers** > **Alle gebruikers**.

    ![Koppelingen voor gebruikers en alle gebruikers](common/users.png)

2. Selecteer **Nieuwe gebruiker** boven aan het scherm.

    ![De knop Nieuwe gebruiker](common/new-user.png)

3. Voer in het dialoog venster **gebruiker** de volgende stappen uit:

    ![Dialoog venster gebruiker](common/user-properties.png)

    a. Voer in het vak **Naam** **Britta Simon**in.
  
    b. Voer in het vak **gebruikers naam** `brittasimon@yourcompanydomain.extension` in. Een voorbeeld is BrittaSimon@contoso.com.

    c. Schakel het selectie vakje **wacht woord weer geven** in. Noteer de waarde die wordt weer gegeven in het vak **wacht woord** .

    d. Selecteer **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

Schakel Julia Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan Zscaler Beta.

1. Selecteer in de Azure Portal **bedrijfs toepassingen**  > **alle toepassingen**  > **Zscaler Beta**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Voer in de lijst toepassingen de optie **Zscaler Beta**in en selecteer deze.

    ![Zscaler Beta-koppeling in de lijst met toepassingen](common/all-applications.png)

3. Selecteer **Gebruikers en groepen** in het menu aan de linkerkant.

    ![Koppeling gebruikers en groepen](common/users-groups-blade.png)

4. Selecteer **Gebruiker toevoegen**. Selecteer in het dialoog venster **toewijzing toevoegen** de optie **gebruikers en groepen**.

    ![Knop gebruiker toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoog venster **gebruikers en groepen** de gebruiker zoals **Julia Simon** in de lijst. Kies vervolgens **selecteren** onder aan het scherm.

    ![Het dialoog venster gebruikers en groepen](./media/zscaler-beta-tutorial/tutorial_zscalerbeta_users.png)

6. Selecteer in het dialoog venster **rol selecteren** de juiste gebruikersrol in de lijst. Kies vervolgens **selecteren** onder aan het scherm.

    ![Het dialoog venster rol selecteren](./media/zscaler-beta-tutorial/tutorial_zscalerbeta_roles.png)

7. Selecteer **Toewijzen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het dialoog venster toewijzing toevoegen](./media/zscaler-beta-tutorial/tutorial_zscalerbeta_assign.png)

### <a name="create-a-zscaler-beta-test-user"></a>Een Zscaler beta-test gebruiker maken

In deze sectie wordt de gebruiker Julia Simon gemaakt in Zscaler Beta. Zscaler Beta biedt ondersteuning voor **Just-In-Time-inrichting van gebruikers**. Deze functie is standaard ingeschakeld. U kunt in deze sectie niets doen. Als er nog geen gebruiker in Zscaler Beta bestaat, wordt er een nieuwe gemaakt nadat deze is geverifieerd.

>[!Note]
>Als u een gebruiker hand matig wilt maken, neemt u contact op met het [ondersteunings team van Zscaler Beta](https://www.zscaler.com/company/contact).

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

Test uw Azure AD-configuratie voor eenmalige aanmelding via het toegangs venster.

Wanneer u de tegel Zscaler Beta selecteert in het toegangs venster, moet u automatisch worden aangemeld bij de Zscaler-bèta waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelf studies voor het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)
- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

