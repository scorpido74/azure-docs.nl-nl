---
title: 'Zelf studie: integratie Azure Active Directory met FM: systemen | Microsoft Docs'
description: 'Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en FM: systemen.'
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f78c58c5-6e98-458b-8991-78624a245665
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/05/2019
ms.author: jeedes
ms.openlocfilehash: 9f869d595aeb3ff2ab7c3fd0bab7b52c72ab904b
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80048614"
---
# <a name="tutorial-azure-active-directory-integration-with-fmsystems"></a>Zelf studie: integratie Azure Active Directory met FM: systemen

In deze zelf studie leert u hoe u FM kunt integreren: systemen met Azure Active Directory (Azure AD).
FM integreren: systemen met Azure AD bieden de volgende voor delen:

* U kunt beheren in azure AD die toegang heeft tot FM: Systems.
* U kunt ervoor zorgen dat uw gebruikers automatisch worden aangemeld bij FM: systemen (eenmalige aanmelding) met hun Azure AD-accounts.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u nog geen abonnement op Azure hebt, [Maak dan een gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie met FM: systemen wilt configureren, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) aanvragen
* FM: abonnement voor eenmalige aanmelding voor systemen

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* FM: systemen bieden ondersteuning voor door **IDP** GEÏNITIEERDe SSO

## <a name="adding-fmsystems-from-the-gallery"></a>Toevoegen FM: systemen uit de galerie

Als u de integratie van FM:-systemen wilt configureren in azure AD, moet u FM: systemen uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Als u FM: systemen uit de galerie wilt toevoegen, voert u de volgende stappen uit:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. In het zoekvak typt u **FM: Systems**, **FM: systemen** uit het paneel result en klikt u vervolgens op knop **toevoegen** om de toepassing toe te voegen.

     ![FM: systemen in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie kunt u eenmalige aanmelding voor Azure AD configureren en testen met FM: systemen op basis van een test gebruiker met de naam **Julia Simon**.
Voor eenmalige aanmelding, een koppelings relatie tussen een Azure AD-gebruiker en de bijbehorende gebruiker in FM: systemen moeten tot stand worden gebracht.

Als u eenmalige aanmelding voor Azure AD wilt configureren en testen met FM: systemen, moet u de volgende bouw stenen volt ooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[FM configureren: eenmalige aanmelding van systemen](#configure-fmsystems-single-sign-on)** -voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[FM: systeem test gebruiker maken](#create-fmsystems-test-user)** -een tegen hanger van Julia Simon in FM: systemen die zijn gekoppeld aan de Azure AD-representatie van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)**: als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding voor Azure AD met FM:-systemen te configureren:

1. Selecteer in de [Azure Portal](https://portal.azure.com/)op de pagina **FM: systeem** toepassings integratie de optie **eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![FM: systeem domein en Url's eenmalige aanmelding gegevens](common/both-replyurl.png)

    Typ in het tekstvak **antwoord-URL** een URL met het volgende patroon:`https://<companyname>.fmshosted.com/fminteract/ConsumerService2.aspx`
    
    > [!NOTE]
    > Deze waarde is niet echt. Werk deze waarde bij met de werkelijke antwoord-URL. Contact opnemen met [FM: ondersteunings team voor Systems-clients](https://fmsystems.com/support-services/) om de waarde op te halen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens** te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

6. Kopieer op de sectie **FM: Systems instellen** de gewenste URL ('s) volgens uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-fmsystems-single-sign-on"></a>FM configureren: eenmalige aanmelding voor systemen

Voor het configureren van eenmalige aanmelding op **FM: systeem** zijde moet u het gedownloade **XML-bestand met federatieve meta gegevens** en de juiste gekopieerde url's verzenden van Azure Portal naar [FM: systemen ondersteunings team](https://fmsystems.com/support-services/). Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken 

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam****Britta Simon**in.
  
    b. Typ `brittasimon@yourcompanydomain.extension`in het veld **gebruikers naam** . Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik op **maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u Julia Simon in voor het gebruik van eenmalige aanmelding van Azure door toegang te verlenen aan FM: Systems.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**, selecteer **alle toepassingen**en selecteer vervolgens **FM: Systems**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst toepassingen **FM: Systems**.

    ![De koppeling FM: Systems in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoog venster **gebruikers en groepen** **Julia Simon** in de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.

6. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-fmsystems-test-user"></a>FM maken: systeem test gebruiker

1. Meld u in een webbrowser venster aan bij uw FM: Systems-bedrijfs site als beheerder.

2. Ga naar **systeem beheer \> gebruikers lijst \> beveiligings \> gebruikers beheren**.
   
    ![Systeem beheer](./media/fm-systems-tutorial/ic795905.png "Systeem beheer")

3. Klik op **nieuwe gebruiker maken**.
   
    ![Nieuwe gebruiker maken](./media/fm-systems-tutorial/ic795906.png "Nieuwe gebruiker maken")

4. Voer in de sectie **Create User** de volgende stappen uit:
   
    ![Gebruiker maken](./media/fm-systems-tutorial/ic795907.png "Gebruiker maken")
   
    a. Typ de **gebruikers naam**, het **wacht woord**, de bevestiging van het wacht **woord**, de **E-mail** en de **werk nemer-id** van een geldig Azure Active Directory account dat u wilt inrichten in de bijbehorende tekst vakken.
   
    b. Klik op **Volgende**.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel FM: systemen in het toegangs venster klikt, moet u automatisch worden aangemeld bij de FM: systemen waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

