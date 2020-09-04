---
title: 'Zelfstudie: Integratie van Azure Active Directory met Zscaler Beta | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Zscaler Beta.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/24/2019
ms.author: jeedes
ms.openlocfilehash: f342371ec065c4fb60c492c4354c6f8c717c9bb8
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/18/2020
ms.locfileid: "88545995"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-beta"></a>Zelfstudie: Integratie van Azure Active Directory met Zscaler Beta

In deze zelfstudie leert u hoe u Zscaler Beta kunt integreren met Azure Active Directory (Azure AD).
Wanneer u Zscaler Beta integreert met Azure AD, kunt u het volgende:

* U kunt in Azure AD beheren wie toegang heeft tot Zscaler Beta.
* Ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij Zscaler Beta. Dit toegangsbeheerelement wordt eenmalige aanmelding (SSO) genoemd.
* Uw accounts op een centrale locatie beheren, namelijk met behulp van Azure Portal.

Meer informatie over de integratie van SaaS-apps (Software as a Service) met Azure AD vindt u in [Eenmalige aanmelding van toepassingen in Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u integratie tussen Azure AD met Zscaler Beta wilt configureren, hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) krijgen.
* Een Zscaler Beta-abonnement waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Zscaler Beta biedt ondersteuning voor door SP geïnitieerde eenmalige aanmelding.
* Zscaler Beta biedt ondersteuning voor het Just-In-Time inrichten van gebruikers.

## <a name="add-zscaler-beta-from-the-azure-marketplace"></a>Zscaler Beta toevoegen vanuit Azure Marketplace

Als u de integratie van Zscaler Beta met Azure AD wilt configureren, moet u Zscaler Beta vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

Volg deze stappen om Zscaler Beta toe te voegen vanuit Azure Marketplace.

1. Selecteer **Azure Active Directory** in [Azure Portal](https://portal.azure.com) in het navigatiepaneel aan de linkerkant.

    ![Knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u **Nieuwe toepassing** bovenaan het dialoogvenster.

    ![Knop Nieuwe toepassing](common/add-new-app.png)

4. Voer in het zoekvak **Zscaler Beta** in. Selecteer **Zscaler Beta** in het resultatenvenster en selecteer **Toevoegen**.

     ![Zscaler Beta in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u eenmalige aanmelding van Azure AD met Zscaler Beta op basis van een testgebruiker met de naam Britta Simon.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in Zscaler Beta tot stand is gebracht.

Als u eenmalige aanmelding van Azure AD bij Zscaler Beta wilt configureren en testen, voert u de volgende stappen uit:

- [Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-single-sign-on) zodat uw gebruikers deze functie kunnen gebruiken.
- [Eenmalige aanmelding van Zscaler Beta configureren](#configure-zscaler-beta-single-sign-on): als u de instellingen voor eenmalige aanmelding aan de clientzijde wilt configureren.
- [Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user) voor het testen van eenmalige aanmelding bij Azure AD met Britta Simon.
- [De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user) zodat Britta Simon gebruik kan maken van eenmalige aanmelding bij Azure AD.
- [Een testgebruiker voor Zscaler Beta maken](#create-a-zscaler-beta-test-user): als u in Zscaler Beta een tegenhanger van Britta Simon wilt hebben die is gekoppeld aan de Azure AD-representatie van de gebruiker.
- [Eenmalige aanmelding testen](#test-single-sign-on) om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer deze stappen uit om eenmalige aanmelding van Azure AD bij Zscaler Beta te configureren.

1. In de [Azure-portal](https://portal.azure.com/) selecteert u **Zscaler Beta** op de integratiepagina van de toepassing **Eenmalige aanmelding**.

    ![Koppeling naar Eenmalige aanmelding configureren](common/select-sso.png)

2. Selecteer in het dialoogvenster **Selecteer een methode voor eenmalige aanmelding** de modus **SAML/WS-Fed** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Selecteer **Bewerken** op de pagina **Eenmalige aanmelding instellen met SAML** om het dialoogvenster **SAML-basisconfiguratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. Voer deze stap uit in de sectie **SAML-basisconfiguratie**:

    ![Informatie over eenmalige aanmelding bij Zscaler Beta-domeinen en -URL's](common/sp-intiated.png)

    - Typ in het tekstvak **Aanmeldings-URL** de URL waarmee uw gebruikers zich aanmelden bij uw Zscaler Beta-toepassing.

    > [!NOTE]
    > Deze waarde is niet echt. Vervang de waarde door de werkelijke aanmeldings-URL. Neem contact op met het [klantondersteuningsteam van Zscaler Beta](https://www.zscaler.com/company/contact) om de waarde op te vragen.

5. De Zscaler Beta-toepassing verwacht dat de SAML-beweringen in een bepaalde indeling staan. U moet aangepaste kenmerktoewijzingen toevoegen aan de configuratie van uw SAML-tokenkenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven. Selecteer **Bewerken** om het dialoogvenster **Gebruikerskenmerken** te openen.

    ![Het dialoogvenster Gebruikerskenmerken](common/edit-attribute.png)

6. Bovendien verwacht de Zscaler Beta-toepassing nog enkele kenmerken die als SAML-antwoord moeten worden doorgestuurd. In de sectie **Gebruikersclaims** in het dialoogvenster **Gebruikerskenmerken** voert u deze stappen uit om het kenmerk van het SAML-token toe te voegen zoals wordt weergegeven in de volgende tabel.
    
    | Naam | Bronkenmerk | 
    | ---------------| --------------- |
    | memberOf  | user.assignedroles |

    a. Selecteer **Nieuwe claim toevoegen** om het dialoogvenster **Gebruikersclaims beheren** te openen.

    ![Het dialoogvenster Gebruikersclaims](common/new-save-attribute.png)

    ![Het dialoogvenster Gebruikersclaims beheren](common/new-attribute-details.png)

    b. Typ in het tekstvak **Naam** de naam van het kenmerk die voor die rij wordt weergegeven.

    c. Laat het vak **Naamruimte** leeg.

    d. Als **Bron** selecteert u **Kenmerk**.

    e. Typ in de lijst **Bronkenmerk** de kenmerkwaarde voor die rij.

    f. Selecteer **OK**.

    g. Selecteer **Opslaan**.

    > [!NOTE]
    > Raadpleeg [De rolclaim configureren](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-app-role-management) voor informatie over het configureren van rollen in Azure AD.

7. Ga naar de pagina **Eenmalige aanmelding met SAML instellen** en selecteer in de sectie **SAML-handtekeningcertificaat** de optie **Downloaden** om het **certificaat (Base64)** te downloaden. Sla het op uw computer op.

    ![De koppeling om het certificaat te downloaden](common/certificatebase64.png)

8. In de sectie **Zscaler Beta instellen** kopieert u de juiste URL('s) op basis van uw behoeften:

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    - Aanmeldings-URL
    - Azure AD-id
    - Afmeldings-URL

### <a name="configure-zscaler-beta-single-sign-on"></a>Eenmalige aanmelding van Zscaler Beta configureren

1. Als u de configuratie in Zscaler Beta wilt automatiseren, moet u **My Apps-browserextensie voor veilig aanmelden** installeren door **De extensie installeren** te selecteren.

    ![Mijn apps-extensie](common/install-myappssecure-extension.png)

2. Als u na het toevoegen van de extensie aan de browser de optie **Zscaler Beta instellen** selecteert, wordt u doorgestuurd naar de Zscaler Beta-toepassing. Geef hier de Administrator-referenties op om u aan te melden bij Zscaler Beta. In de browserextensie wordt de toepassing automatisch voor u geconfigureerd en worden stappen 3 t/m 6 geautomatiseerd.

    ![Instelling configureren](common/setup-sso.png)

3. Als u Zscaler Beta handmatig wilt instellen, opent u een nieuw webbrowservenster. Meld u als beheerder aan bij uw Zscaler Beta-bedrijfssite en voer de volgende stappen uit.

4. Ga naar **Beheer** > **Verificatie** > **Verificatie-instellingen** en volg deze stappen.
   
    ![Beheer](./media/zscaler-beta-tutorial/ic800206.png "Beheer")

    a. Kies onder **Verificatietype** de optie **SAML**.

    b. Selecteer **SAML configureren**.

5. Volg deze stappen in het venster **SAML bewerken**: 
            
    ![Gebruikers en verificatie beheren](./media/zscaler-beta-tutorial/ic800208.png "Gebruikers en verificatie beheren")
    
    a. Plak in het vak **IDP-aanmeldings-URL** de **aanmeldings-URL** die u uit Azure Portal hebt gekopieerd.

    b. Voer in het tekstvak **Kenmerk aanmeldingsnaam** **NameID** in.

    c. Selecteer **Uploaden** in het tekstvak **Openbaar SSL-certificaat** om het Azure SAML-ondertekeningscertificaat te uploaden dat u in Azure Portal hebt gedownload.

    d. Schakel **Automatische inrichting van SAML inschakelen** in.

    e. Voer in het vak **Kenmerk van weergavenaam van gebruiker** **displayName** in als u automatische inrichting van SAML wilt inschakelen voor displayName-kenmerken.

    f. Voer in het vak **Kenmerk van groepsnaam** **memberOf** in als u automatische inrichting van SAML wilt inschakelen voor memberOf-kenmerken.

    g. Voer in het vak **Kenmerk van afdelingsnaam** **department** in als u automatische inrichting van SAML wilt inschakelen voor department-kenmerken.

    h. Selecteer **Opslaan**.

6. Voer in het dialoogvenster **Gebruikersverificatie configureren** de volgende stappen uit:

    ![Het menu Activering en de knop Activeren](./media/zscaler-beta-tutorial/ic800207.png)

    a. Beweeg de muisaanwijzer boven het menu **Activering** linksonder.

    b. Selecteer **Activate**.

## <a name="configure-proxy-settings"></a>Proxyinstellingen configureren
Volg deze stappen om de proxyinstellingen te configureren in Internet Explorer.

1. Start **Internet Explorer**.

2. Selecteer **Internetopties** in het menu **Extra** om het dialoogvenster **Internetopties** te openen. 
    
     ![Het dialoogvenster Internetopties](./media/zscaler-beta-tutorial/ic769492.png "Internetopties")

3. Selecteer het tabblad **Verbindingen**. 
  
     ![Tabblad Verbindingen](./media/zscaler-beta-tutorial/ic769493.png "Verbindingen")

4. Selecteer **LAN-instellingen** om het dialoogvenster **LAN-instellingen (Local Area Network)** te openen.

5. Volg deze stappen in de sectie **Proxyserver**: 
   
    ![De sectie Proxyserver](./media/zscaler-beta-tutorial/ic769494.png "Proxyserver")

    a. Schakel het selectievakje **Een proxyserver voor uw LAN-netwerk gebruiken** in.

    b. Typ **gateway.Zscaler Beta.net** in het tekstvak **Adres**.

    c. Voer in het vak **Poort** de waarde **80** in.

    d. Schakel het selectievakje **Proxyserver niet voor lokale adressen gebruiken** in.

    e. Selecteer **OK** om het dialoogvenster **LAN-instellingen** te sluiten.

6. Selecteer **OK** om het dialoogvenster **Internetopties** te sluiten.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken 

Maak in Azure Portal een testgebruiker met de naam Britta Simon.

1. Selecteer in de Azure-portal aan de linkerkant **Azure Active Directory** > **Gebruikers** > **Alle gebruikers**.

    ![Koppelingen naar Gebruikers en Alle gebruikers](common/users.png)

2. Selecteer **Nieuwe gebruiker** boven aan het scherm.

    ![De knop Nieuwe gebruiker](common/new-user.png)

3. Volg deze stappen in het dialoogvenster **Gebruiker**:

    ![Dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het vak **Naam** **Britta Simon**in.
  
    b. Voer in het vak **Gebruikersnaam** `brittasimon@yourcompanydomain.extension` in. Een voorbeeld is BrittaSimon@contoso.com.

    c. Schakel het selectievakje **Wachtwoord weergeven** in. Noteer de waarde die wordt weer gegeven in het vak **Wachtwoord**.

    d. Selecteer **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

Geef Britta Simon toestemming voor gebruik van eenmalige aanmelding van Azure door haar toegang te geven tot Zscaler Beta.

1. Selecteer in Azure Portal **Bedrijfstoepassingen** > **Alle toepassingen** > **Zscaler Beta**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Voer **Zscaler Beta** in en selecteer deze optie in de lijst met toepassingen.

    ![De Zscaler Beta-koppeling in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Selecteer **Gebruiker toevoegen**. Selecteer **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De knop Gebruiker toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst. Kies vervolgens **Selecteren** onderaan het scherm.

    ![Het dialoogvenster Gebruikers en groepen](./media/zscaler-beta-tutorial/tutorial_zscalerbeta_users.png)

6. Selecteer in het dialoogvenster **Rol selecteren** de juiste gebruikersrol in de lijst. Kies vervolgens **Selecteren** onderaan het scherm.

    ![Het dialoogvenster Rol selecteren](./media/zscaler-beta-tutorial/tutorial_zscalerbeta_roles.png)

7. Selecteer **Toewijzen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het dialoogvenster Toewijzing toevoegen](./media/zscaler-beta-tutorial/tutorial_zscalerbeta_assign.png)

### <a name="create-a-zscaler-beta-test-user"></a>Een Zscaler Beta-testgebruiker maken

In deze sectie wordt de gebruiker Britta Simon gemaakt in Zscaler Beta. Zscaler Beta biedt ondersteuning voor **Just-In-Time-inrichting van gebruikers**. Deze functie is standaard ingeschakeld. In deze sectie hoeft u niets te doen. Als er nog geen gebruiker in Zscaler Beta bestaat, wordt er een nieuwe gemaakt nadat deze is geverifieerd.

>[!Note]
>Als u handmatig een gebruiker wilt maken, neemt u contact op met het [ondersteuningsteam van Zscaler Beta](https://www.zscaler.com/company/contact).

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

Test de configuratie voor eenmalige aanmelding van Azure AD met behulp van het toegangsvenster.

Wanneer u de tegel Zscaler Beta in het toegangsvenster selecteert, wordt u automatisch aangemeld bij de instantie van Zscaler Beta waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)
- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

