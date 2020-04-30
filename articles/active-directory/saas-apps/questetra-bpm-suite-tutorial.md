---
title: 'Zelf studie: integratie Azure Active Directory met Questetra BPM Suite | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Questetra BPM Suite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: fb6d5b73-e491-4dd2-92d6-94e5aba21465
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: f58d6cbc6ec04e51e105662dff31c60ff502584c
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "67093361"
---
# <a name="tutorial-azure-active-directory-integration-with-questetra-bpm-suite"></a>Zelf studie: integratie Azure Active Directory met Questetra BPM Suite

In deze zelf studie leert u hoe u Questetra BPM Suite integreert met Azure Active Directory (Azure AD).
Het integreren van Questetra BPM Suite met Azure AD biedt de volgende voor delen:

* U kunt beheren in azure AD die toegang heeft tot de Questetra BPM Suite.
* U kunt ervoor zorgen dat uw gebruikers automatisch worden aangemeld bij Questetra BPM Suite (eenmalige aanmelding) met hun Azure AD-accounts.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u nog geen abonnement op Azure hebt, [Maak dan een gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie met Questetra BPM Suite wilt configureren, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Abonnement voor eenmalige aanmelding van Questetra BPM Suite

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Questetra BPM Suite ondersteunt door **SP** GEÏNITIEERDe SSO

## <a name="adding-questetra-bpm-suite-from-the-gallery"></a>Questetra BPM Suite toevoegen uit de galerie

Als u de integratie van Questetra BPM Suite wilt configureren in azure AD, moet u Questetra BPM Suite uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om Questetra BPM Suite toe te voegen vanuit de galerie:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ in het zoekvak **QUESTETRA BPM Suite**, selecteer **Questetra BPM Suite** van het deel venster result en klik vervolgens op de knop **toevoegen** om de toepassing toe te voegen.

     ![Questetra BPM Suite in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie kunt u eenmalige aanmelding voor Azure AD configureren en testen met Questetra BPM Suite op basis van een test gebruiker met de naam **Julia Simon**.
Voor een goede werking van eenmalige aanmelding moet er een koppelings relatie tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Questetra BPM Suite tot stand worden gebracht.

Als u eenmalige aanmelding voor Azure AD wilt configureren en testen met Questetra BPM Suite, moet u de volgende bouw stenen volt ooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding van QUESTETRA BPM Suite configureren](#configure-questetra-bpm-suite-single-sign-on)** : Hiermee configureert u de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Maak een QUESTETRA BPM Suite test gebruiker](#create-questetra-bpm-suite-test-user)** -om een soort tegen te gaan van Julia Simon in Questetra BPM Suite die is gekoppeld aan de Azure AD-representatie van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)**: als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding voor Azure AD te configureren met Questetra BPM Suite:

1. Selecteer in de [Azure Portal](https://portal.azure.com/)op de pagina **Questetra BPM Suite** Application Integration de optie **eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Informatie over eenmalige aanmelding van Questetra BPM Suite-domein en Url's](common/sp-identifier.png)

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<subdomain>.questetra.net/saml/SSO/alias/bpm`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met het volgende patroon: `https://<subdomain>.questetra.net/`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de daadwerkelijke aanmeldings-URL en id. U kunt deze waarden ophalen uit de sectie **SP Information** op de bedrijfs site van de **Questetra BPM-Suite** , die verderop in de zelf studie wordt beschreven of contact opneemt met het [ondersteunings team van Questetra BPM Suite](https://www.questetra.com/contact/). U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

6. Kopieer op de sectie **set up QUESTETRA BPM Suite** de gewenste URL ('s) volgens uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-questetra-bpm-suite-single-sign-on"></a>Eenmalige aanmelding van Questetra BPM Suite configureren

1. Meld u in een ander webbrowser venster als beheerder aan bij de bedrijfs site van uw **QUESTETRA BPM-Suite** .

2. Klik in het menu aan de bovenkant op **systeem instellingen**. 
   
    ![Eenmalige aanmelding voor Azure AD][10]

3. Klik op **SSO (SAML)** om de pagina **SingleSignOnSAML** te openen. 
   
    ![Eenmalige aanmelding voor Azure AD][11]

4. Voer de volgende stappen uit in de sectie **SP Information** van de **Questetra BPM Suite** -bedrijfs site:

    a. Kopieer de **ACS-URL**en plak deze in het tekstvak **Sign on URL** in het gedeelte **basis-SAML-configuratie** van Azure Portal.
    
    b. Kopieer de **Entiteits-ID**en plak deze in het tekstvak **id** in het gedeelte **basis configuratie van SAML** van Azure Portal.

5. Voer de volgende stappen uit op de bedrijfs site van de **QUESTETRA BPM-Suite** : 
   
    ![Eenmalige aanmelding configureren][15]
   
    a. Selecteer **eenmalige aanmelding inschakelen**.
   
    b. Plak in het tekstvak **Entiteits-ID** de waarde van de **Azure ad-id** die u van Azure Portal hebt gekopieerd.
    
    c. Plak in het tekstvak URL voor aanmeldings **pagina** de waarde van de **aanmeldings-URL** die u hebt gekopieerd uit Azure Portal.
    
    d. Plak in het tekstvak URL van de afmeldings **pagina** de waarde van de **afmeldings-URL** die u van Azure Portal hebt gekopieerd.
    
    e. Typ `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`in het tekstvak **NameID-indeling** .

    f. Open uw met **Base 64** versleutelde certificaat in Klad blok dat u hebt gedownload van Azure Portal, kopieer de inhoud ervan naar het klem bord en plak het in het tekstvak **validatie certificaat** . 

    g. Klik op **Opslaan**.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken 

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam****Britta Simon**in.
  
    b. Typ brittasimon@yourcompanydomain.extensionin het veld **gebruikers naam** . Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik op **maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u Julia Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan de Questetra BPM Suite.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**, selecteer **alle toepassingen**en selecteer vervolgens **Questetra BPM Suite**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst toepassingen de optie **QUESTETRA BPM Suite**.

    ![De koppeling Questetra BPM Suite in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoog venster **gebruikers en groepen** **Julia Simon** in de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.

6. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-questetra-bpm-suite-test-user"></a>Questetra BPM Suite-test gebruiker maken

Het doel van deze sectie is het maken van een gebruiker met de naam Julia Simon in Questetra BPM Suite.

**Als u een gebruiker met de naam Julia Simon in Questetra BPM Suite wilt maken, voert u de volgende stappen uit:**

1. Meld u als beheerder aan bij de bedrijfs site van uw Questetra BPM-Suite.

2. Ga naar **systeem instellingen > gebruikers lijst > nieuwe gebruiker**.
 
3. Voer de volgende stappen uit in het dialoog venster nieuwe gebruiker: 
   
    ![Test gebruiker maken][300] 
   
    a. Typ in het tekstvak **naam** de **naam** van de gebruiker britta.simon@contoso.com.
   
    b. Typ in het tekstvak **e-mail** **e-mail** van de gebruiker britta.simon@contoso.com.
   
    c. Typ een **wacht woord** van de gebruiker in het tekstvak **wacht woord** .
    
    d. Klik op **nieuwe gebruiker toevoegen**.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Questetra BPM Suite in het toegangs venster klikt, moet u automatisch worden aangemeld bij de Questetra BPM Suite waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

<!--Image references-->

[10]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_03.png
[11]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_04.png
[15]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_08.png
[300]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_11.png