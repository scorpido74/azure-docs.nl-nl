---
title: 'Zelf studie: integratie Azure Active Directory met PerformanceCentre | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en PerformanceCentre.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 65288c32-f7e6-4eb3-a6dc-523c3d748d1c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: 08f0f1c1e9bbfcd27f97cd58fa6c6e40f448f9f5
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "67094641"
---
# <a name="tutorial-azure-active-directory-integration-with-performancecentre"></a>Zelf studie: integratie Azure Active Directory met PerformanceCentre

In deze zelf studie leert u hoe u PerformanceCentre integreert met Azure Active Directory (Azure AD).
Het integreren van PerformanceCentre met Azure AD biedt de volgende voor delen:

* U kunt beheren in azure AD die toegang heeft tot PerformanceCentre.
* U kunt ervoor zorgen dat uw gebruikers automatisch worden aangemeld bij PerformanceCentre (eenmalige aanmelding) met hun Azure AD-accounts.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u nog geen abonnement op Azure hebt, [Maak dan een gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie met PerformanceCentre wilt configureren, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Abonnement voor eenmalige aanmelding PerformanceCentre ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* PerformanceCentre ondersteunt door **SP** GEÏNITIEERDe SSO

## <a name="adding-performancecentre-from-the-gallery"></a>PerformanceCentre toevoegen uit de galerie

Als u de integratie van PerformanceCentre in azure AD wilt configureren, moet u PerformanceCentre uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om PerformanceCentre toe te voegen uit de galerie:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **PerformanceCentre**in het zoekvak, selecteer **PerformanceCentre** in het resultaten paneel en klik vervolgens op de knop **toevoegen** om de toepassing toe te voegen.

     ![PerformanceCentre in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie kunt u eenmalige aanmelding voor Azure AD configureren en testen met PerformanceCentre op basis van een test gebruiker met de naam **Julia Simon**.
Voor een goede werking van eenmalige aanmelding moet er een koppelings relatie tussen een Azure AD-gebruiker en de bijbehorende gebruiker in PerformanceCentre tot stand worden gebracht.

Als u eenmalige aanmelding voor Azure AD wilt configureren en testen met PerformanceCentre, moet u de volgende bouw stenen volt ooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[PerformanceCentre eenmalige aanmelding configureren](#configure-performancecentre-single-sign-on)** : Hiermee configureert u de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Maak een PerformanceCentre-test gebruiker](#create-performancecentre-test-user)** -om een equivalent van Julia Simon in PerformanceCentre te hebben dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)**: als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding voor Azure AD te configureren met PerformanceCentre:

1. Selecteer in de [Azure Portal](https://portal.azure.com/)op de pagina **PerformanceCentre** Application Integration de optie **eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Informatie over eenmalige aanmelding voor PerformanceCentre domein en Url's](common/sp-identifier.png)

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `http://<companyname>.performancecentre.com/saml/SSO`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met het volgende patroon: `http://<companyname>.performancecentre.com`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en -id. Neem contact op met het [ondersteunings team van PerformanceCentre-clients](https://www.performancecentre.com/contact-us/) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

4. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens** te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

6. Kopieer op de sectie **PerformanceCentre instellen** de gewenste URL ('s) volgens uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-performancecentre-single-sign-on"></a>Eenmalige aanmelding voor PerformanceCentre configureren

1. Meld u aan bij de **PerformanceCentre** -bedrijfs site als beheerder.

2. Klik op het tabblad aan de linkerkant op **configureren**.
   
    ![Eenmalige aanmelding voor Azure AD][10]

3. Klik op het tabblad aan de linkerkant op **Diversen**en klik vervolgens op **eenmalige aanmelding**.
   
    ![Eenmalige aanmelding voor Azure AD][11]

4. Als **protocol**selecteert u **SAML**.
   
    ![Eenmalige aanmelding voor Azure AD][12]

5. Open het gedownloade meta gegevensbestand in Klad blok, kopieer de inhoud, plak het in het tekstvak met de **meta gegevens van de identiteits provider** en klik vervolgens op **Opslaan**.
   
    ![Eenmalige aanmelding voor Azure AD][13]

6. Controleer of de waarden voor de **basis-URL** van de entiteit en de URL van de **Entiteits-ID** juist zijn.
    
     ![Eenmalige aanmelding voor Azure AD][14]

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken 

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam****Britta Simon**in.
  
    b. In het veld **Gebruikersnaam** typt u **brittasimon@yourcompanydomain.extension**.  
    Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik op **maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u Julia Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan PerformanceCentre.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**, selecteer **alle toepassingen**en selecteer vervolgens **PerformanceCentre**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst toepassingen de optie **PerformanceCentre**.

    ![De koppeling PerformanceCentre in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoog venster **gebruikers en groepen** **Julia Simon** in de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.

6. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-performancecentre-test-user"></a>PerformanceCentre-test gebruiker maken

Het doel van deze sectie is het maken van een gebruiker met de naam Julia Simon in PerformanceCentre.

**Als u een gebruiker met de naam Julia Simon in PerformanceCentre wilt maken, voert u de volgende stappen uit:**

1. Meld u aan bij de PerformanceCentre-bedrijfs site als beheerder.

2. Klik in het menu aan de linkerkant op **interrelate**en klik vervolgens op **deel nemer maken**.
   
    ![Gebruiker maken][400]

3. Voer de volgende stappen uit in het dialoog venster **interrelate-deel nemer maken** :
   
    ![Gebruiker maken][401]
    
    a. Typ de vereiste kenmerken voor Julia Simon in gerelateerde tekst vakken.
    
    >[!IMPORTANT]
    >Het kenmerk van de gebruikers naam van Julia in PerformanceCentre moet hetzelfde zijn als de gebruikers naam in azure AD.
    
    b. Selecteer **client beheerder** als **rol kiezen**.
    
    c. Klik op **Opslaan**. 

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel PerformanceCentre in het toegangs venster klikt, moet u automatisch worden aangemeld bij de PerformanceCentre waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

<!--Image references-->

[10]: ./media/performancecentre-tutorial/tutorial_performancecentre_06.png
[11]: ./media/performancecentre-tutorial/tutorial_performancecentre_07.png
[12]: ./media/performancecentre-tutorial/tutorial_performancecentre_08.png
[13]: ./media/performancecentre-tutorial/tutorial_performancecentre_09.png
[14]: ./media/performancecentre-tutorial/tutorial_performancecentre_10.png
[400]: ./media/performancecentre-tutorial/tutorial_performancecentre_11.png
[401]: ./media/performancecentre-tutorial/tutorial_performancecentre_12.png