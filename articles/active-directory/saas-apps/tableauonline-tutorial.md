---
title: 'Zelf studie: integratie Azure Active Directory met tableau online | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en tableau online.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1d4b1149-ba3b-4f4e-8bce-9791316b730d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.openlocfilehash: 187600edb599f5a5775e1b847ed1cb3a49f3b827
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/04/2020
ms.locfileid: "76985570"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-tableau-online"></a>Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met tableau online

In deze zelf studie leert u hoe u tableau online integreert met Azure Active Directory (Azure AD). Wanneer u tableau online integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot tableau online.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld bij tableau met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* Tableau online abonnement voor eenmalige aanmelding (SSO) is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Tableau online ondersteunt door **SP** GEÏNITIEERDe SSO
* Nadat u tableau online hebt geconfigureerd, kunt u sessie beheer afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessie beheer is uitgebreid met voorwaardelijke toegang. [Meer informatie over het afdwingen van sessie beheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-tableau-online-from-the-gallery"></a>Tableau online toevoegen vanuit de galerie

Als u de integratie van tableau online wilt configureren in azure AD, moet u tableau online toevoegen vanuit de galerie aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **tableau online** in het zoekvak.
1. Selecteer **tableau online** in het resultaten paneel en voeg de app vervolgens toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie kunt u eenmalige aanmelding voor Azure AD configureren en testen met tableau online op basis van een test gebruiker met de naam **Julia Simon**.
Voor een goede werking van eenmalige aanmelding moet er een koppelings relatie tussen een Azure AD-gebruiker en de bijbehorende gebruiker in tableau online worden gemaakt.

Als u Azure AD SSO wilt configureren en testen met tableau online, voert u de volgende bouw stenen uit:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    1. **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    1. **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Tableau online-SSO configureren](#configure-tableau-online-sso)** : Hiermee configureert u de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    1. **[Maak een tableau online test gebruiker](#create-tableau-online-test-user)** -om een soort tegen te brengen van B. Simon in tableau online dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO configureren

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding voor Azure AD met tableau online te configureren:

1. Selecteer in de [Azure Portal](https://portal.azure.com/)op de pagina **tableau online** Application Integration de optie **eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Informatie over eenmalige aanmelding tableau online domein en Url's](common/sp-identifier.png)

    a. In het tekstvak **Aanmeldings-URL** typt u de URL: `https://sso.online.tableau.com/public/sp/login?alias=<entityid>`

    b. Typ in het tekstvak **Id (Entiteits-id)** de volgende URL: `https://sso.online.tableau.com/public/sp/metadata?alias=<entityid>`

    > [!NOTE]
    > In deze zelf studie krijgt u de `<entityid>` waarde uit de sectie **tableau online instellen** . De entiteit-ID-waarde is de **Azure AD-ID** -waarde in de sectie **tableau online instellen** .

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens** te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

6. Kopieer op de sectie **tableau online instellen** de gewenste URL ('s) volgens uw vereiste.

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
  
    b. Typ in het veld **gebruikers naam** **brittasimon\@yourcompanydomain. extension**  
    Bijvoorbeeld BrittaSimon\@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik op **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u Julia Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan tableau online.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**, selecteer **alle toepassingen**en selecteer **tableau online**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **tableau online**in de lijst toepassingen.

    ![De online koppeling tableau in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-tableau-online-sso"></a>Tableau online-SSO configureren

1. Meld u in een ander browser venster aan bij uw tableau online-toepassing. Ga naar **instellingen** en vervolgens op **authenticatie**.

    ![Eenmalige aanmelding configureren](./media/tableauonline-tutorial/tutorial_tableauonline_09.png)

2. Als u SAML wilt inschakelen, onder **verificatie typen** sectie. Schakel **een extra verificatie methode in** en schakel het selectie vakje **SAML** in.

    ![Eenmalige aanmelding configureren](./media/tableauonline-tutorial/tutorial_tableauonline_12.png)

3. Schuif omlaag om het **META gegevensbestand te importeren in tableau online** .  Klik op Bladeren en importeer het meta gegevensbestand dat u hebt gedownload van Azure AD. Klik vervolgens op **Toep assen**.

   ![Eenmalige aanmelding configureren](./media/tableauonline-tutorial/tutorial_tableauonline_13.png)

4. In de sectie **beweringen afstemmen** voert u de bijbehorende naam van de identiteits provider voor het **e-mail adres**, de **voor naam**en de **Achternaam**in. Als u deze informatie van Azure AD wilt ontvangen: 
  
    a. Ga in het Azure Portal naar de pagina **tableau online** Application Integration.

    b. Klik in de sectie **gebruikers kenmerken & claims** op het pictogram bewerken.

   ![Eenmalige aanmelding configureren](./media/tableauonline-tutorial/attributesection.png)

    c. Kopieer de waarde van de naam ruimte voor deze kenmerken: benamingnaam, e-mail en achternaam met behulp van de volgende stappen:

   ![Eenmalige aanmelding voor Azure AD](./media/tableauonline-tutorial/tutorial_tableauonline_10.png)

    d. Klik op de waarde **User.** valued

    e. Kopieer de waarde uit het tekstvak **naam ruimte** .

    ![Eenmalige aanmelding configureren](./media/tableauonline-tutorial/attributesection2.png)

    f. De waarden van de naam ruimte voor het e-mail bericht kopiëren en de achternaam herhalen de bovenstaande stappen.

    g. Ga naar de online toepassing tableau en stel de volgende **gebruikers kenmerken & claims** in:

    * E-mail: **e-mail** of **userPrincipalName**

    * Voor naam: **benaming**

    * Achternaam: **naam**

    ![Eenmalige aanmelding configureren](./media/tableauonline-tutorial/tutorial_tableauonline_14.png)

### <a name="create-tableau-online-test-user"></a>Tableau online test gebruiker maken

In deze sectie maakt u een gebruiker met de naam Julia Simon in tableau online.

1. Klik op **tableau online**op **instellingen** en vervolgens op **verificatie** sectie. Schuif omlaag naar de sectie **gebruikers beheren** . Klik op **gebruikers toevoegen** en klik vervolgens op **e-mail adressen invoeren**.
  
    ![Een Azure AD-testgebruiker maken](./media/tableauonline-tutorial/tutorial_tableauonline_15.png)

2. Selecteer **gebruikers voor SAML-verificatie (toevoegen)** . Voeg in het tekstvak **e-mail adressen invoeren** Julia. simon toe\@contoso.com
  
    ![Een Azure AD-testgebruiker maken](./media/tableauonline-tutorial/tutorial_tableauonline_11.png)

3. Klik op **gebruikers toevoegen**.

### <a name="test-sso"></a>SSO testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel online tableau klikt in het deel venster toegang, moet u automatisch worden aangemeld bij de tableau online waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wat is sessie beheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)