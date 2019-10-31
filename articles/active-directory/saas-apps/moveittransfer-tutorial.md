---
title: 'Zelf studie: integratie Azure Active Directory met MOVEit-overdracht-Azure AD-integratie | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en MOVEit-overdracht-Azure AD-integratie.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 8ff7102d-be73-4888-ae81-d8e3d01dd534
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/25/2019
ms.author: jeedes
ms.openlocfilehash: 43383e82d983c998d159728997da4757cc364999
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73161316"
---
# <a name="tutorial-azure-active-directory-integration-with-moveit-transfer---azure-ad-integration"></a>Zelf studie: integratie Azure Active Directory met MOVEit-overdracht-Azure AD-integratie

In deze zelf studie leert u hoe u MOVEit-overdracht integreert met Azure AD-integratie met Azure Active Directory (Azure AD).
Integreren van MOVEit-overdracht: Azure AD-integratie met Azure AD biedt u de volgende voor delen.

* U kunt beheren in azure AD die toegang heeft tot MOVEit-overdracht-Azure AD-integratie.
* U kunt ervoor zorgen dat uw gebruikers automatisch worden aangemeld voor MOVEit overdracht: Azure AD-integratie (eenmalige aanmelding) met hun Azure AD-accounts.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie met MOVEit-overdracht-Azure AD-integratie wilt configureren, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* MOVEit-overdracht: Azure AD-integratie ingeschakeld abonnement voor eenmalige aanmelding

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* MOVEit-overdracht: Azure AD-integratie ondersteunt door **SP** GEÏNITIEERDe SSO

## <a name="adding-moveit-transfer---azure-ad-integration-from-the-gallery"></a>MOVEit-overdracht toevoegen-Azure AD-integratie vanuit de galerie

Als u de integratie van MOVEit-overdracht-Azure AD-integratie in azure AD wilt configureren, moet u MOVEit overdracht-Azure AD-integratie vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om MOVEit-overdracht van Azure AD-integratie toe te voegen vanuit de galerie:**

1. Klik in het linkernavigatievenster in de  **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **MOVEit-overdracht-Azure AD-integratie**in het zoekvak, selecteer **MOVEit overdracht-Azure AD-integratie** uit het paneel resultaten en klik vervolgens op de knop **toevoegen** om de toepassing toe te voegen.

     ![MOVEit-overdracht-Azure AD-integratie in de resultaten lijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie kunt u eenmalige aanmelding voor Azure AD configureren en testen met MOVEit-overdracht-Azure AD-integratie op basis van een test gebruiker met de naam **Julia Simon**.
Als u eenmalige aanmelding wilt gebruiken, moet u een koppelings relatie tussen een Azure AD-gebruiker en de bijbehorende gebruiker in MOVEit-overdracht-Azure AD-integratie tot stand brengen.

Als u eenmalige aanmelding voor Azure AD wilt configureren en testen met MOVEit-overdracht-Azure AD-integratie, moet u de volgende bouw stenen volt ooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[MOVEit-overdracht configureren: eenmalige aanmelding voor Azure AD-integratie](#configure-moveit-transfer---azure-ad-integration-single-sign-on)** -voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Maak een MOVEit-overdracht-Azure AD-integratie test gebruiker](#create-moveit-transfer---azure-ad-integration-test-user)** -om een tegen hanger te hebben van Julia Simon in MOVEit-overdracht-Azure AD-integratie die is gekoppeld aan de Azure AD-representatie van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding voor Azure AD met MOVEit-overdracht-Azure AD-integratie te configureren:

1. Selecteer in de [Azure Portal](https://portal.azure.com/)op de pagina **MOVEit-overdracht van Azure AD-integratie** toepassing de optie **eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. Voer in de sectie **Standaard SAML-configuratie** de volgende stappen uit als u beschikt over een **bestand met metagegevens van de serviceprovider**:

    a. Klik op **Metagegevensbestand uploaden**.

    ![Metagegevensbestand uploaden](common/upload-metadata.png)

    b. Klik op het **mappictogram** om het metagegevensbestand te selecteren en klik op **Uploaden**.

    ![Metagegevensbestand kiezen](common/browse-upload-metadata.png)

    c. Nadat het meta gegevensbestand is geüpload, wordt de **id** en de **antwoord-URL** -waarde automatisch ingevuld in de **basis configuratie** sectie voor SAML:

    ![MOVEit-overdracht: Azure AD-integratie domein en Url's eenmalige aanmelding](common/sp-identifier-reply.png)

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://contoso.com`

    > [!NOTE]
    > De waarde voor de **aanmeldings-URL** is niet echt. Werk de waarde bij met de werkelijke aanmeldings-URL. Neem contact op met [MOVEit overdracht: Azure AD Integration client support](https://community.ipswitch.com/s/support) team om de waarde op te halen. U kunt het **META gegevensbestand van de service provider** downloaden van de **meta gegevens-URL van de service provider** , die later wordt beschreven in de sectie **MOVEit-overdracht configureren-Azure AD-integratie eenmalige aanmelding** van de zelf studie. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

4. Ga op de pagina **Eenmalige aanmelding met SAML instellen** naar de sectie **SAML-handtekeningcertificaat** en klik op **Downloaden** om het **XML-bestand met federatieve metagegevens** te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

6. Kopieer op de sectie **MOVEit-overdracht instellen-Azure AD-integratie** de gewenste URL ('s) volgens uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-moveit-transfer---azure-ad-integration-single-sign-on"></a>MOVEit-overdracht configureren-eenmalige aanmelding voor Azure AD-integratie

1. Meld u als beheerder aan bij uw MOVEit-overdrachts Tenant.

2. Klik in het linkernavigatiedeelvenster op **Instellingen**.

    ![De sectie instellingen aan de kant van de app](./media/moveittransfer-tutorial/tutorial_moveittransfer_000.png)

3. Klik op **één aanmelding** -koppeling, die zich onder beveiligings beleid bevindt **-> gebruikers verificatie**.

    ![Beveiligings beleid aan de kant van de app](./media/moveittransfer-tutorial/tutorial_moveittransfer_001.png)

4. Klik op de koppeling URL van meta gegevens om het meta gegevens document te downloaden.

    ![Meta gegevens-URL van service provider](./media/moveittransfer-tutorial/tutorial_moveittransfer_002.png)
    
   * Controleer of **entityID** overeenkomt met de **id** in het gedeelte **basis configuratie van SAML** .
   * Controleer of de locatie-URL van de **AssertionConsumerService** overeenkomt met de **antwoord-URL** in het gedeelte **basis configuratie van SAML** .
    
     ![Eenmalige aanmelding in de app configureren](./media/moveittransfer-tutorial/tutorial_moveittransfer_007.png)

5. Klik op de knop **ID-provider toevoegen** om een nieuwe federatieve id-provider toe te voegen.

    ![Id-provider toevoegen](./media/moveittransfer-tutorial/tutorial_moveittransfer_003.png)

6. Klik op **Bladeren...** om het meta gegevensbestand te selecteren dat u van Azure Portal hebt gedownload en klik vervolgens op **ID-provider toevoegen** om het gedownloade bestand te uploaden.

    ![SAML-ID-provider](./media/moveittransfer-tutorial/tutorial_moveittransfer_004.png)

7. Selecteer**Ja**als **ingeschakeld** op de pagina **instellingen voor federatieve identiteiten bewerken...** en klik op **Opslaan**.

    ![Instellingen voor federatieve id-providers](./media/moveittransfer-tutorial/tutorial_moveittransfer_005.png)

8. Voer op de pagina **federatieve id-provider gebruikers instellingen bewerken** de volgende acties uit:
    
    ![Instellingen voor federatieve id-providers bewerken](./media/moveittransfer-tutorial/tutorial_moveittransfer_006.png)
    
    a. Selecteer **SAML NameID** als **aanmeldings naam**.
    
    b. Selecteer **anders** als **volledige naam** en plaats de waarde in het tekstvak **kenmerk naam** : `http://schemas.microsoft.com/identity/claims/displayname`.
    
    c. Selecteer **anders** als **e-mail adres** en plaats de waarde in het tekstvak **kenmerk naam** : `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.
    
    d. Selecteer **Ja** als **account automatisch maken op aanmelding**.
    
    e. Klik op de knop **Save**.

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

In deze sectie schakelt u Julia Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot MOVEit-overdracht-Azure AD-integratie.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**, selecteer **alle toepassingen**en selecteer vervolgens **MOVEIT-overdracht-Azure AD-integratie**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst toepassingen de optie **MOVEit overdracht-Azure AD-integratie**.

    ![De koppeling MOVEit-overdracht-Azure AD-integratie in de lijst met toepassingen](common/all-applications.png)

3. Selecteer **Gebruikers en groepen** in het menu aan de linkerkant.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-moveit-transfer---azure-ad-integration-test-user"></a>MOVEit-overdracht maken-test gebruiker van Azure AD-integratie

Het doel van deze sectie is het maken van een gebruiker met de naam Julia Simon in MOVEit-overdracht-Azure AD-integratie. MOVEit-overdracht: Azure AD-integratie biedt ondersteuning voor Just-in-time-inrichting, die u hebt ingeschakeld. Er is geen actie-item voor u in deze sectie. Er wordt een nieuwe gebruiker gemaakt tijdens een poging toegang te krijgen tot MOVEit-overdracht-Azure AD-integratie als deze nog niet bestaat.

>[!NOTE]
>Als u hand matig een gebruiker moet maken, moet u contact opnemen met de [MOVEit-overdracht: Azure AD Integration client support-team](https://community.ipswitch.com/s/support).

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel MOVEit overdracht-Azure AD-integratie in het toegangs venster klikt, moet u automatisch worden aangemeld bij de MOVEit-overdracht-Azure AD-integratie waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

