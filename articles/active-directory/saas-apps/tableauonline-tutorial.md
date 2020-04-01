---
title: 'Zelfstudie: Azure Active Directory-integratie met Tableau Online | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Tableau Online.
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
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76985570"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-tableau-online"></a>Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met Tableau Online

In deze zelfstudie leert u hoe u Tableau Online integreren met Azure Active Directory (Azure AD). Wanneer u Tableau Online integreert met Azure AD, u het:

* Beheer in Azure AD die toegang heeft tot Tableau Online.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij Tableau Online met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* Tableau Online single sign-on (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Tableau Online ondersteunt **SP** geïnitieerde SSO
* Zodra je Tableau Online hebt geconfigureerd, kun je sessiecontrole afdwingen, die exfiltratie en infiltratie van de gevoelige gegevens van je organisatie in realtime beschermen. Sessiebeheer strekt zich uit van Voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-tableau-online-from-the-gallery"></a>Tableau Online toevoegen vanuit de galerie

Als u de integratie van Tableau Online in Azure AD wilt configureren, moet u Tableau Online vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **Tableau Online** in het zoekvak in de sectie Toevoegen in **de galerijsectie.**
1. Selecteer **Tableau Online** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureer en test u Azure AD single sign-on met Tableau Online op basis van een testgebruiker genaamd **Britta Simon**.
Voor eenmalige aanmelding aan het werk moet een koppeling worden gemaakt tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Tableau Online.

Als u Azure AD SSO wilt configureren en testen met Tableau Online, voert u de volgende bouwstenen in:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** - om Azure AD-enkele aanmelding te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** - om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
1. **[Configureer Tableau Online SSO](#configure-tableau-online-sso)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
    1. **[Maak Tableau Online-testgebruiker](#create-tableau-online-test-user)** - om een tegenhanger van B.Simon te hebben in Tableau Online die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om de volgende stappen uit te voeren om de enkele aanmelding van Azure AD met Tableau Online te configureren:

1. Selecteer in de [Azure-portal](https://portal.azure.com/)op de pagina **Tableau Online-toepassingsintegratie** de optie Eén **aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Informatie over Tableau Online-domein en URL's met één aanmelding](common/sp-identifier.png)

    a. In het tekstvak **Aanmeldings-URL** typt u de URL: `https://sso.online.tableau.com/public/sp/login?alias=<entityid>`

    b. Typ in het tekstvak **Id (Entiteits-id)** de volgende URL: `https://sso.online.tableau.com/public/sp/metadata?alias=<entityid>`

    > [!NOTE]
    > Je krijgt `<entityid>` de waarde van de sectie **Tableau Online instellen** in deze zelfstudie. De waarde van de entiteits-id is de waarde **van azure AD-id** in de sectie **Tableau Online instellen.**

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens** te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

6. Kopieer in de sectie **Tableau Online instellen** de juiste URL(s) volgens uw eis.

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

    a. Voer in het veld **Naam****Britta Simon**in.
  
    b. In het **veld Type Gebruikersnaam** **brittasimon\@yourcompanydomain.extension**  
    Bijvoorbeeld, BrittaSimon\@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik **op Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie u Britta Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot Tableau Online.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**, selecteer **Alle toepassingen**en selecteer Vervolgens **Tableau Online**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Tableau Online**in de lijst met toepassingen .

    ![De Tableau Online-link in de lijst Toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer **In** het dialoogvenster Gebruikers en groepen **Britta Simon** in de lijst Gebruikers en klik je op de knop **Selecteren** onder aan het scherm.

6. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-tableau-online-sso"></a>Tableau Online SSO configureren

1. Meld je in een ander browservenster aan bij je Tableau Online-toepassing. Ga naar **Instellingen** en vervolgens **Verificatie**.

    ![Eenmalige aanmelding configureren](./media/tableauonline-tutorial/tutorial_tableauonline_09.png)

2. Als u SAML wilt inschakelen, wordt onder sectie **Verificatietypen** ingeschakeld. Schakel **Een aanvullende verificatiemethode inschakelen** in en schakel het selectievakje **SAML** in.

    ![Eenmalige aanmelding configureren](./media/tableauonline-tutorial/tutorial_tableauonline_12.png)

3. Scroll omlaag naar **het bestand met metagegevens importeren in de** sectie Tableau Online.  Klik op Bladeren en importeer het metagegevensbestand dat u hebt gedownload van Azure AD. Klik vervolgens op **Toepassen**.

   ![Eenmalige aanmelding configureren](./media/tableauonline-tutorial/tutorial_tableauonline_13.png)

4. Voeg in de sectie **Beweringen overeenkomen** de overeenkomstige bevestigingsnaam van de identiteitsprovider in voor **e-mailadres,** **voornaam**en **achternaam**. Ga als volgt te werk om deze informatie uit Azure AD te halen: 
  
    a. Ga in de Azure-portal naar de pagina **Met de integratie** van Tableau Online-toepassingen.

    b. Klik in de sectie **Gebruikerskenmerken & Claims** op het pictogram bewerken.

   ![Eenmalige aanmelding configureren](./media/tableauonline-tutorial/attributesection.png)

    c. Kopieer de naamruimtewaarde voor deze kenmerken: givenname, e-mail en achternaam met behulp van de volgende stappen:

   ![Azure AD-aanmelding](./media/tableauonline-tutorial/tutorial_tableauonline_10.png)

    d. Klik op de waarde **user.givenname**

    e. Kopieer de waarde uit het tekstvak **Naamruimte.**

    ![Eenmalige aanmelding configureren](./media/tableauonline-tutorial/attributesection2.png)

    f. Als u de naamruimtewaarden voor de e-mail en achternaam wilt kopiëren, herhaalt u de bovenstaande stappen.

    g. Schakel over naar de Tableau Online-toepassing en stel de sectie **Gebruikerskenmerken & Claims** als volgt in:

    * E-mail: **e-mail** of **gebruikersnaam**

    * Voornaam: **voornaam**

    * Achternaam: **achternaam**

    ![Eenmalige aanmelding configureren](./media/tableauonline-tutorial/tutorial_tableauonline_14.png)

### <a name="create-tableau-online-test-user"></a>Tableau Online-testgebruiker maken

In deze sectie maak je een gebruiker genaamd Britta Simon in Tableau Online.

1. Klik op **Tableau Online**op **Instellingen** en vervolgens op de sectie **Verificatie.** Scroll omlaag naar de sectie **Gebruikers beheren.** Klik **op Gebruikers toevoegen** en klik vervolgens op **E-mailadressen invoeren**.
  
    ![Een Azure AD-testgebruiker maken](./media/tableauonline-tutorial/tutorial_tableauonline_15.png)

2. Selecteer **Gebruikers toevoegen voor (SAML)-verificatie**. Voeg in het enter **e-mailadressen** tekstvak britta.simon\@toe contoso.com
  
    ![Een Azure AD-testgebruiker maken](./media/tableauonline-tutorial/tutorial_tableauonline_11.png)

3. Klik **op Gebruikers toevoegen**.

### <a name="test-sso"></a>Test SSO

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer je op de Tableau Online-tegel in het Toegangspaneel klikt, moet je automatisch worden aangemeld bij het Tableau Online waarvoor je SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)