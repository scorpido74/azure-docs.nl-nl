---
title: 'Zelfstudie: Azure Active Directory-integratie met Zscaler-bèta | Microsoft Documenten'
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
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "68943294"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-beta"></a>Zelfstudie: Azure Active Directory-integratie met Zscaler-bèta

In deze zelfstudie leert u hoe u Zscaler Beta kunt integreren met Azure Active Directory (Azure AD).
Wanneer u Zscaler Beta integreert met Azure AD, u het als:

* Beheer in Azure AD die toegang heeft tot De Bèta van Zscaler.
* Laat uw gebruikers automatisch worden aangemeld bij De Bèta van Zscaler met hun Azure AD-accounts. Dit toegangscontrole wordt single sign-on (SSO) genoemd.
* Beheer uw accounts op één centrale locatie met behulp van de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory voor](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)meer informatie over de integratie van de App Voor software as a service (SaaS) met Azure AD.
Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u integratie tussen Azure AD met Zscaler Beta wilt configureren, hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, u een [gratis account](https://azure.microsoft.com/free/)krijgen.
* Een Zscaler Beta-abonnement dat gebruik maakt van eenmalige aanmelding.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Zscaler Beta ondersteunt SP-geïnitieerde SSO.
* Zscaler Beta ondersteunt just-in-time gebruikersinrichting.

## <a name="add-zscaler-beta-from-the-azure-marketplace"></a>Zscaler-bèta toevoegen vanuit de Azure Marketplace

Als u de integratie van Zscaler-bèta in Azure AD wilt configureren, voegt u Zscaler Beta van de Azure Marketplace toe aan uw lijst met beheerde SaaS-apps.

Voer de volgende stappen uit om Zscaler-bèta toe te voegen vanuit de Azure Marketplace.

1. Selecteer **Azure Active Directory**in de [Azure-portal](https://portal.azure.com)in het linkernavigatiedeelvenster .

    ![Knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **Enterprise-toepassingen**en selecteer **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u **Nieuwe toepassing** bovenaan het dialoogvenster.

    ![Knop Nieuwe toepassing](common/add-new-app.png)

4. Voer in het zoekvak **De Zscaler-bèta**in. Selecteer **Zscaler-bèta** in het resultaatpaneel en selecteer **Vervolgens Toevoegen**.

     ![Zscaler Beta in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u Azure AD single sign-on met Zscaler Beta op basis van testgebruiker Britta Simon.
Voor eenmalige aanmelding om te werken, u een koppelingsrelatie aangaan tussen een Azure AD-gebruiker en de gerelateerde gebruiker in De Bèta van Zscaler.

Als u Azure AD-singlesign-aan met Zscaler-bèta wilt configureren en testen, voert u de volgende bouwstenen uit:

- [Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-single-sign-on) zodat uw gebruikers deze functie kunnen gebruiken.
- [Configureer de enkele aanmelding van Zscaler Beta](#configure-zscaler-beta-single-sign-on) om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
- [Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user) voor het testen van eenmalige aanmelding van Azure AD met Britta Simon.
- [De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user) zodat Britta Simon gebruik kan maken van eenmalige aanmelding van Azure AD.
- [Maak een Zscaler Beta-testgebruiker](#create-a-zscaler-beta-test-user) om een tegenhanger van Britta Simon in Zscaler Beta te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
- [Eenmalige aanmelding testen](#test-single-sign-on) om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om azure AD single sign-on te configureren met De Bèta van Zscaler.

1. In de [Azure-portal](https://portal.azure.com/) selecteert u **Zscaler Beta** op de integratiepagina van de toepassing **Eenmalige aanmelding**.

    ![Koppeling Eén aanmelding configureren](common/select-sso.png)

2. Selecteer in het dialoogvenster **Selecteer een methode voor eenmalige aanmelding** de modus **SAML/WS-Fed** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. **Selecteer** bewerken om het dialoogvenster **BasisSAML-configuratie** te openen op de pagina **Eén aanmelding instellen met SAML.**

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. Volg in de sectie **BasisSAML-configuratie** de volgende stap:

    ![Zscaler Beta-domein en URL's met eenmalige aanmeldingsgegevens](common/sp-intiated.png)

    - Voer in het vak **Aanmelding smijten in** de URL in die door uw gebruikers wordt gebruikt om zich aan te melden bij uw Zscaler-bètatoepassing.

    > [!NOTE]
    > De waarde is niet echt. Werk de waarde bij met de werkelijke URL-waarde aanbording. Neem contact op met het [Zscaler Beta client support team](https://www.zscaler.com/company/contact)om de waarde te krijgen.

5. De Zscaler Beta applicatie verwacht dat de SAML beweringen in een specifiek formaat. U moet aangepaste kenmerktoewijzingen toevoegen aan uw SAML-tokenkenmerkenconfiguratie. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven. Selecteer **Bewerken** om het dialoogvenster **Gebruikerskenmerken te** openen.

    ![Het dialoogvenster Gebruikerskenmerken](common/edit-attribute.png)

6. De Zscaler Beta applicatie verwacht dat een paar meer attributen worden teruggegeven in SAML reactie. Voer in de sectie **Gebruikersclaims** in het dialoogvenster **Gebruikerskenmerken** de volgende stappen uit om het SAML-tokenkenmerk toe te voegen, zoals in de volgende tabel wordt weergegeven.
    
    | Name | Bronkenmerk | 
    | ---------------| --------------- |
    | memberOf  | user.assignedroles |

    a. Selecteer **Nieuwe claim toevoegen** om het dialoogvenster **Gebruikersclaims beheren** te openen.

    ![Dialoogvenster Gebruikersclaims](common/new-save-attribute.png)

    ![Het dialoogvenster Gebruikersclaims beheren](common/new-attribute-details.png)

    b. Typ in het tekstvak **Naam** de naam van het kenmerk die voor die rij wordt weergegeven.

    c. Laat het vak **Naamruimte** leeg.

    d. Selecteer **Kenmerk**voor **Bron**.

    e. Typ in de lijst **Bronkenmerk** de kenmerkwaarde voor die rij.

    f. Selecteer **OK**.

    g. Selecteer **Opslaan**.

    > [!NOTE]
    > Zie [De rolclaim configureren](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-app-role-management)voor meer informatie over het configureren van rollen in Azure AD.

7. Selecteer op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** de optie **Downloaden** om het **certificaat (Base64)** te downloaden. Sla het op uw computer op.

    ![De koppeling om het certificaat te downloaden](common/certificatebase64.png)

8. Kopieer in de sectie **Zscaler-bèta instellen** de URL's die u nodig hebt voor uw vereisten:

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    - Aanmeldings-URL
    - Azure AD-id
    - Afmeldings-URL

### <a name="configure-zscaler-beta-single-sign-on"></a>Eenmalig aanmelden voor Zscaler-bèta configureren

1. Als u de configuratie binnen De Bèta van Zscaler wilt automatiseren, installeert u **de extensie Mijn apps Secure Sign-in** door De extensie installeren **te**selecteren.

    ![Extensie Mijn apps](common/install-myappssecure-extension.png)

2. Nadat u de extensie aan de browser hebt toegevoegd, leidt het selecteren **van Zscaler-bèta instellen** u door naar de Bètatoepassing Van Zscaler. Geef van daaruit de beheerdersreferenties op om u aan te melden bij De Bèta van Zscaler. De browserextensie configureert automatisch de toepassing voor u en automatiseert stap 3 tot en met 6.

    ![Configuratie instellen](common/setup-sso.png)

3. Als u De Bèta van Zscaler handmatig wilt instellen, opent u een nieuw browservenster. Meld u aan bij uw bedrijf site van Zscaler Beta als beheerder en volg deze stappen.

4. Ga naar**Authentication** > **Verificatieinstellingen** **voor verificatie beheer** > en volg deze stappen.
   
    ![Beheer](./media/zscaler-beta-tutorial/ic800206.png "Beheer")

    a. Selecteer **SAML** **onder Verificatietype**.

    b. Selecteer **SAML configureren**.

5. Voer in het venster **SAML bewerken** de volgende stappen uit: 
            
    ![Gebruikers & verificatie beheren](./media/zscaler-beta-tutorial/ic800208.png "Gebruikers & verificatie beheren")
    
    a. Plak in het **vak SAML-portal URL** de **URL van aanmelding** die u hebt gekopieerd uit de Azure-portal.

    b. Voer in het vak **Aanmeldingsnaamkenmerk** **naam**in.

    c. Selecteer in het vak **Openbaar SSL-certificaat** de optie **Uploaden** om het Azure SAML-ondertekeningscertificaat te uploaden dat u hebt gedownload van de Azure-portal.

    d. **Inschakeling in SAML Automatisch inrichten**.

    e. Voer in het vak **Kenmerk naam van de gebruikersweergave** display display **display** in als u SAML-autoprovisioning wilt inschakelen voor weergavenaamkenmerken.

    f. Voer in het vak **Groepsnaamkenmerk** lid in **als** u SAML-autoprovisioning wilt inschakelen voor leden Van kenmerken.

    g. **Voer** in het vak **Eigenschappenvan afdeling spaarnaam** in als u SAML autoprovisioning wilt inschakelen voor afdelingskenmerken.

    h. Selecteer **Opslaan**.

6. Voer op de pagina **Gebruikersverificatie configureren** de volgende stappen uit:

    ![Menu Activering en knop Activeren](./media/zscaler-beta-tutorial/ic800207.png)

    a. Plaats de plaats linksonder in het menu **Activering.**

    b. Selecteer **Activeren**.

## <a name="configure-proxy-settings"></a>Proxyinstellingen configureren
Voer deze stappen uit om de proxy-instellingen in Internet Explorer te configureren.

1. Start **Internet Explorer**.

2. Selecteer **internetopties** in het menu **Extra** om het dialoogvenster **Internetopties te** openen. 
    
     ![Dialoogvenster Internetopties](./media/zscaler-beta-tutorial/ic769492.png "Internetopties")

3. Selecteer het tabblad **Verbindingen.** 
  
     ![Tabblad Verbindingen](./media/zscaler-beta-tutorial/ic769493.png "Verbindingen")

4. Selecteer **LAN-instellingen** om het dialoogvenster **LAN-instellingen (Local Area Network) te** openen.

5. Voer in de sectie **Proxyserver** de volgende stappen uit: 
   
    ![Sectie Proxyserver](./media/zscaler-beta-tutorial/ic769494.png "Proxyserver")

    a. Schakel het selectievakje **Een proxyserver voor uw LAN gebruiken** in.

    b. Voer **in** het vak Adres **de gateway in. Zscaler Beta.net**.

    c. Voer **in** het vak Poort **80**in .

    d. Schakel het selectievakje **Proxyserver voor lokale adressen omzeilen** in.

    e. Selecteer **OK** om het dialoogvenster **LAN-instellingen (Local Area Network) te** sluiten.

6. Selecteer **OK** om het dialoogvenster **Internetopties** te sluiten.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken 

Maak een testgebruiker in de Azure-portal genaamd Britta Simon.

1. Selecteer in de Azure-portal in het linkerdeelvenster de optie **Azure Active Directory** > **Users** > **All users All users**.

    ![Koppelingen voor gebruikers en alle gebruikers](common/users.png)

2. Selecteer **Nieuwe gebruiker** boven aan het scherm.

    ![De knop Nieuwe gebruiker](common/new-user.png)

3. Voer **in** het dialoogvenster Gebruiker de volgende stappen uit:

    ![Dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het vak **Naam****Britta Simon**in.
  
    b. Voer in het vak **Gebruikersnaam**`brittasimon@yourcompanydomain.extension` in. Een voorbeeld is BrittaSimon@contoso.com.

    c. Schakel het selectievakje **Wachtwoord weergeven** in. Noteer de waarde die wordt weergegeven in het vak **Wachtwoord.**

    d. Selecteer **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

Schakel Britta Simon in om Azure single sign-on te gebruiken door toegang te verlenen tot Zscaler Beta.

1. Selecteer in de Azure-portal De optie **Enterprise-toepassingen** > **Alle toepassingen** > **Zscaler Beta**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Voer in de lijst met toepassingen **De Zscaler-bèta**in en selecteer deze .

    ![Zscaler Beta-koppeling in de lijst Toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![Koppeling gebruikers en groepen](common/users-groups-blade.png)

4. Selecteer **Gebruiker toevoegen**. Selecteer in het dialoogvenster **Toewijzing toevoegen** de optie Gebruikers **en groepen**.

    ![Knop Gebruiker toevoegen](common/add-assign-user.png)

5. Selecteer **in** het dialoogvenster Gebruikers en groepen de gebruiker zoals **Britta Simon** in de lijst. Kies vervolgens **Selecteer** onder aan het scherm.

    ![Dialoogvenster Gebruikers en groepen](./media/zscaler-beta-tutorial/tutorial_zscalerbeta_users.png)

6. Selecteer **in** het dialoogvenster Rol selecteren de juiste gebruikersrol in de lijst. Kies vervolgens **Selecteer** onder aan het scherm.

    ![Dialoogvenster Rol selecteren](./media/zscaler-beta-tutorial/tutorial_zscalerbeta_roles.png)

7. Selecteer **Toewijzen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Dialoogvenster Toewijzing toevoegen](./media/zscaler-beta-tutorial/tutorial_zscalerbeta_assign.png)

### <a name="create-a-zscaler-beta-test-user"></a>Een Zscaler Beta-testgebruiker maken

In deze sectie wordt de gebruiker Britta Simon gemaakt in Zscaler Beta. Zscaler Beta biedt ondersteuning voor **Just-In-Time-inrichting van gebruikers**. Deze functie is standaard ingeschakeld. Er is niets voor jou te doen in deze sectie. Als er nog geen gebruiker in Zscaler Beta bestaat, wordt er een nieuwe gemaakt nadat deze is geverifieerd.

>[!Note]
>Neem contact op met het [ondersteuningsteam van Zscaler Beta](https://www.zscaler.com/company/contact)om handmatig een gebruiker te maken.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

Test uw configuratie met eenmalige aanmelding in Azure AD met behulp van het Toegangspaneel.

Wanneer u de bèta-tegel Zscaler selecteert in het toegangspaneel, moet u automatisch worden aangemeld bij de Zscaler-bèta waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

