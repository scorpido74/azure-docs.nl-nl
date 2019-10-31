---
title: 'Zelf studie: integratie Azure Active Directory met SAML SSO voor confluence door Resolution GmbH | Microsoft Docs'
description: Informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en SAML SSO for Confluence by resolution GmbH.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 6b47d483-d3a3-442d-b123-171e3f0f7486
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/24/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9676a71940569b26d6b0b6bfef767108ae57d953
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73161214"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-confluence-by-resolution-gmbh"></a>Zelf studie: integratie Azure Active Directory met SAML SSO voor confluence by Solution GmbH

Deze zelfstudie laat zien hoe u SAML SSO for Confluence by resolution GmbH integreert met Azure Active Directory (Azure AD).
Integratie van SAML SSO for Confluence by resolution GmbH met Azure AD biedt de volgende voordelen:

* U kunt in Azure AD beheren wie toegang heeft tot SAML SSO for Confluence by resolution GmbH.
* U kunt inschakelen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij SAML SSO for Confluence by resolution GmbH (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD wilt integreren met SAML SSO for Confluence by resolution GmbH, hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Abonnement waarvoor eenmalige aanmelding met SAML SSO for Confluence by resolution GmbH is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* SAML SSO for Confluence by resolution GmbH ondersteunt door **SP** en **IDP** geïnitieerde eenmalige aanmelding

## <a name="adding-saml-sso-for-confluence-by-resolution-gmbh-from-the-gallery"></a>SAML SSO for Confluence by resolution GmbH toevoegen vanuit de galerie

Als u de integratie van SAML SSO for Confluence by resolution GmbH met Azure AD wilt configureren, moet u SAML SSO for Confluence by resolution GmbH vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om SAML SSO for Confluence by resolution GmbH toe te voegen vanuit de galerie:**

1. Klik in het linkernavigatievenster in de  **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **SAML SSO for Confluence by resolution GmbH** in het zoekvak, selecteer **SAML SSO for Confluence by resolution GmbH** in het resultatenvenster en klik op **Toevoegen** om de toepassing toe te voegen.

     ![SAML SSO for Confluence by resolution GmbH in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u Azure AD-eenmalige aanmelding met SAML SSO for Confluence by resolution GmbH op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in SAML SSO for Confluence by resolution GmbH tot stand is gebracht.

Als u Azure AD-eenmalige aanmelding met SAML SSO for Confluence by resolution GmbH wilt configureren en testen, moet u de volgende bouwstenen voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding voor SAML SSO for Confluence by resolution GmbH configureren](#configure-saml-sso-for-confluence-by-resolution-gmbh-single-sign-on)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wil configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Testgebruiker van SAML SSO for Confluence by resolution GmbH maken](#create-saml-sso-for-confluence-by-resolution-gmbh-test-user)** : als u een tegenhanger van Britta Simon in SAML SSO for Confluence by resolution GmbH wilt hebben die is gekoppeld aan de Azure AD-versie van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Als u Azure AD-eenmalige aanmelding wilt integreren met SAML SSO for Confluence by resolution GmbH, voert u de volgende stappen uit:

1. In de [Azure-portal](https://portal.azure.com/), op de integratiepagina voor de toepassing **SAML SSO for Confluence by resolution GmbH**, selecteert u **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In het gedeelte **Standaard SAML-configuratie** voert u de volgende stappen uit als u de toepassing in de door **IDP geïnitieerde** modus wilt configureren:

    ![Informatie over domein en URL's voor eenmalige aanmelding met SAML SSO for Confluence by resolution GmbH](common/idp-intiated.png)

    a. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://<server-base-url>/plugins/servlet/samlsso`

    b. In het tekstvak **Antwoord-URL** typt u een URL met het volgende patroon: `https://<server-base-url>/plugins/servlet/samlsso`

    c. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door SP geïnitieerde modus wilt configureren:

    ![Informatie over domein en URL's voor eenmalige aanmelding met SAML SSO for Confluence by resolution GmbH](common/metadata-upload-additional-signon.png)

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<server-base-url>/plugins/servlet/samlsso`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke-id, de antwoord-URL en de aanmeldings-URL. Neem contact op met het [klantondersteuningsteam voor SAML SSO for Confluence by resolution GmbH](https://www.resolution.de/go/support) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

4. Ga op de pagina **Eenmalige aanmelding met SAML instellen** naar de sectie **SAML-handtekeningcertificaat** en klik op **Downloaden** om het **XML-bestand met federatieve metagegevens** te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

### <a name="configure-saml-sso-for-confluence-by-resolution-gmbh-single-sign-on"></a>Eenmalige aanmelding met SAML SSO for Confluence by resolution GmbH configureren

1. Meld u in een ander webbrowservenster aan bij uw **SAML SSO for Confluence by resolution GmbH-beheerportal** als beheerder.

2. Wijs het tandwiel aan met de muisaanwijzer en klik op **Add-ons**.
    
    ![Eenmalige aanmelding configureren](./media/samlssoconfluence-tutorial/addon1.png)

3. U wordt omgeleid naar de pagina voor beheerderstoegang. Voer het wachtwoord in en klik op **Bevestigen**.

    ![Eenmalige aanmelding configureren](./media/samlssoconfluence-tutorial/addon2.png)

4. Op het tabblad **ATLASSIAN MARKETPLACE** klikt u op **Nieuwe invoegtoepassingen zoeken**. 

    ![Eenmalige aanmelding configureren](./media/samlssoconfluence-tutorial/addon.png)

5. Zoek **SAML Single Sign On (SSO) for Confluence** en klik op de knop **Installeren** om de nieuwe SAML-invoegtoepassing te installeren.

    ![Eenmalige aanmelding configureren](./media/samlssoconfluence-tutorial/addon7.png)

6. De installatie van de invoegtoepassing wordt gestart. Klik op **Sluiten**.

    ![Eenmalige aanmelding configureren](./media/samlssoconfluence-tutorial/addon8.png)

    ![Eenmalige aanmelding configureren](./media/samlssoconfluence-tutorial/addon9.png)

7.  Klik op **Beheren**.

    ![Eenmalige aanmelding configureren](./media/samlssoconfluence-tutorial/addon10.png)
    
8. Klik op **Configure** om de nieuwe invoegtoepassing te configureren.

    ![Eenmalige aanmelding configureren](./media/samlssoconfluence-tutorial/addon11.png)

9. Deze nieuwe invoegtoepassing is ook terug te vinden op het tabblad **GEBRUIKERS EN BEVEILIGING**.

    ![Eenmalige aanmelding configureren](./media/samlssoconfluence-tutorial/addon3.png)
    
10. Klik op de pagina **SAML SSO-invoegtoepassing configureren** op de knop **Nieuwe IDP toevoegen** om de instellingen van de id-provider te configureren.

    ![Eenmalige aanmelding configureren](./media/samlssoconfluence-tutorial/addon4.png)

11. Voer op de pagina **Choose your SAML Identity Provider** (Uw SAML-id-provider kiezen) de volgende stappen uit:

    ![Eenmalige aanmelding configureren](./media/samlssoconfluence-tutorial/addon5a.png)
 
    a. Stel **Azure AD** als het type id-provider.
    
    b. Voeg de **naam** van de id-provider toe (bijvoorbeeld Azure AD).
    
    c. Voeg de **omschrijving** van de id-provider toe (bijvoorbeeld Azure AD).
    
    d. Klik op **Volgende**.
    
12. Op de pagina voor het **configureren van de id-provider** klikt u op de knop **Volgende**.

    ![Eenmalige aanmelding configureren](./media/samlssoconfluence-tutorial/addon5b.png)

13. Op de pagina **SAML-IDP-metagegevens importeren** voert u de volgende stappen uit:

    ![Eenmalige aanmelding configureren](./media/samlssoconfluence-tutorial/addon5c.png)

    a. Klik op de knop **Bestand laden** en kies het XML-metagegevensbestand dat u in stap 5 hebt gedownload.

    b. Klik op **Importeren**.
    
    c. Wacht totdat het importeren is geslaagd.
    
    d. Klik op de knop **Volgende**.
    
14. Op de pagina **Gebruikers-id-kenmerk en transformatie** klikt u op de knop **Volgende**.

    ![Eenmalige aanmelding configureren](./media/samlssoconfluence-tutorial/addon5d.png)
    
15. Op de pagina **Gebruiker maken en bijwerken** klikt u op **Opslaan en volgende** om de instellingen op te slaan.   
    
    ![Eenmalige aanmelding configureren](./media/samlssoconfluence-tutorial/addon6a.png)
    
16. Op de pagina **Uw instellingen testen** klikt u op **Test overslaan en handmatig configureren** om de gebruikerstest nu over te slaan. Deze test wordt uitgevoerd in de volgende sectie en daarvoor zijn bepaalde instellingen in de Azure-portal vereist. 
    
    ![Eenmalige aanmelding configureren](./media/samlssoconfluence-tutorial/addon6b.png)
    
17. Klik in het dialoog venster dat wordt weer gegeven **overs laan van de test...** op **OK**.
    
    ![Eenmalige aanmelding configureren](./media/samlssoconfluence-tutorial/addon6c.png)

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

In deze sectie stelt u Britta Simon in staat om eenmalige aanmelding van Azure te gebruiken door haar toegangsrechten voor SAML SSO for Confluence by resolution GmbH te verlenen.

1. Selecteer in de Azure-portal **Bedrijfstoepassingen**, selecteer **Alle toepassingen** en selecteer vervolgens **SAML SSO for Confluence by resolution GmbH**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Typ en selecteer in de lijst met toepassingen **SAML SSO for Confluence by resolution GmbH**.

    ![De koppeling SAML SSO for Confluence by resolution GmbH in de lijst met toepassingen](common/all-applications.png)

3. Selecteer **Gebruikers en groepen** in het menu aan de linkerkant.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-saml-sso-for-confluence-by-resolution-gmbh-test-user"></a>Testgebruiker voor SAML SSO for Confluence by resolution GmbH maken

Als u wilt dat Azure AD-gebruikers zich kunnen aanmelden bij SAML SSO for Confluence by resolution GmbH, moeten deze worden ingericht in SAML SSO for Confluence by resolution GmbH.  
Inrichten is een handmatige taak in SAML SSO for Confluence by resolution GmbH.

**Als u een gebruikersaccount wilt inrichten, voert u de volgende stappen uit:**

1. Meld u aan bij de bedrijfssite van SAML SSO for Confluence by resolution GmbH als een beheerder.

2. Wijs het tandwiel aan met de muisaanwijzer en klik op **User management**.

    ![Werknemer toevoegen](./media/samlssoconfluence-tutorial/user1.png) 

3. Klik onder gebruikers op tabblad **gebruikers toevoegen** . Voer de volgende stappen uit op de dialoog pagina **' gebruiker toevoegen '** :

    ![Werknemer toevoegen](./media/samlssoconfluence-tutorial/user2.png) 

    a. Typ in het tekstvak **Username** het e-mailadres van de gebruiker, zoals Britta Simon.

    b. Typ in het tekstvak **Full Name** de volledige naam van de gebruiker, zoals Britta Simon.

    c. Typ in het tekstvak **Email** het e-mailadres van de gebruiker, bijvoorbeeld Brittasimon@contoso.com.

    d. Typ in het tekstvak **Password** het wachtwoord van Britta Simon.

    e. Typ het wachtwoord ter bevestiging in het vak **Confirm Password**.
    
    f. Klik op de knop **Add**.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Als u in het toegangsvenster op de tegel SAML SSO for Confluence by resolution GmbH klikt, moet u automatisch worden aangemeld bij de SAML SSO for Confluence by resolution GmbH waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

