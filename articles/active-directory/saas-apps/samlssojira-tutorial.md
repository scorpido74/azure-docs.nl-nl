---
title: 'Zelfstudie: Azure Active Directory-integratie met SAML SSO voor Jira door Resolution GmbH | Microsoft Documenten'
description: Informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en SAML SSO for Jira by resolution GmbH.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 20e18819-e330-4e40-bd8d-2ff3b98e035f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/03/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 957fee48397bc0b23737157dec0e74cf6505fab5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "73160125"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-jira-by-resolution-gmbh"></a>Zelfstudie: Azure Active Directory-integratie met SAML SSO voor Jira door resolutie GmbH

In deze zelfstudie leert u hoe u SAML SSO voor Jira instelt op resolutie GmbH met Azure Active Directory (Azure AD).
Integratie van SAML SSO for Jira by resolution GmbH met Azure AD biedt de volgende voordelen:

* U in Azure AD bepalen wie zich bij De OPLOSSING GmbH bij Jira kan aanmelden met de SAML SSO-plug-in.
* U uw gebruikers automatisch laten inloggen op Jira met hun Azure AD-accounts door SAML SSO voor Jira te gebruiken bij resolutie GmbH (Single Sign-On).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie en SAML SSO voor Jira wilt configureren door resolutie GmbH, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) een proefversie van één maand krijgen
* Een abonnement op SAML SSO for Jira by resolution GmbH waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* SAML SSO voor Jira door resolutie GmbH ondersteunt **SP** en **IDP** geïnitieerd sso

## <a name="adding-an-enterprise-application-for-single-sign-on"></a>Een bedrijfstoepassing toevoegen voor eenmalige aanmelding

Als u eenmalige aanmelding wilt instellen in Azure AD, moet u een nieuwe bedrijfstoepassing toevoegen. In de galerij is hiervoor een vooraf geconfigureerde applicatie-voorinstelling, **SAML SSO voor Jira by resolution GmbH.**

**Voer de volgende stappen uit om SAML SSO for Jira by resolution GmbH toe te voegen vanuit de galerie:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Ondernemingstoepassingen**en klik op **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u nieuwe toepassingen wilt toevoegen, klikt u boven aan het dialoogvenster op de knop **Nieuwe toepassing.**

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **SAML SSO voor Jira bij resolutie GmbH,** selecteer **SAML SSO voor Jira in resolutie GmbH** in het resultaatpaneel en klik vervolgens op de knop **Toevoegen** om de toepassing toe te voegen. U ook de naam van de bedrijfsapp wijzigen.

     ![SAML SSO for Jira by resolution GmbH in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-single-sign-on-with-the-saml-sso-plugin-and-azure-ad"></a>Eenmalige aanmelding configureren en testen met de SAML SSO-plug-in en Azure AD

In deze sectie test en configureert u eenmalige aanmelding bij Jira voor een Azure AD-gebruiker. Dit zal worden gedaan voor een test gebruiker genaamd **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in SAML SSO for Jira by resolution GmbH tot stand is gebracht.

Als u eenmalige aanmelding wilt configureren en testen, moet u de volgende stappen uitvoeren:

1. **[De Azure AD-bedrijfstoepassing configureren voor één aanmelding](#configure-the-azure-ad-enterprise-application-for-single-sign-on)** - De Azure AD-bedrijfstoepassing configureren voor de enkele aanmelding
2. **[Configureer de SAML SSO-plug-in van uw Jira-exemplaar](#configure-the-saml-sso-plugin-of-your-jira-instance)** - Configureer de instellingen voor enkele aanmelding aan de toepassingszijde.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : maak een testgebruiker in Azure AD.
1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** : de testgebruiker inschakelen om de enkele aanmelding aan de Azure-kant te gebruiken.
1. **[De testgebruiker maken in Jira](#create-the-test-user-also-in-jira)** - Een testgebruiker maken in Jira voor de Azure AD-testgebruiker.
1. **[Eén aanmelding testen](#test-single-sign-on)** - Controleer of de configuratie werkt.

### <a name="configure-the-azure-ad-enterprise-application-for-single-sign-on"></a>De Azure AD-bedrijfstoepassing configureren voor één aanmelding

In deze sectie stelt u de enkele aanmelding in de Azure-portal in.

Voer de volgende stappen uit om de enkele aanmelding met SAML SSO voor Jira te configureren door resolutie GmbH:

1. Selecteer in de [Azure-portal](https://portal.azure.com/)in de zojuist gemaakte **SAML SSO voor Jira door de enterprise-toepassing van GmbH** de optie **Eén aanmelding** in het linkerdeelvenster.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. Selecteer de **SAML-modus** om een enkele aanmeldingsmethode in te schakelen voor **Eenmalige aanmeldingsmethode.**

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Klik daarna op het pictogram **Bewerken** om het dialoogvenster **BasisSAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. Voer in de sectie **BasisSAML-configuratie** de volgende stappen uit als u de toepassing in de **idp-modus** wilt configureren:

    ![Informatie over domein en URL's voor eenmalige aanmelding met SAML SSO for Jira by resolution GmbH](common/idp-intiated.png)

    a. Typ **in** het tekstvak Id een URL met het volgende patroon:`https://<server-base-url>/plugins/servlet/samlsso`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<server-base-url>/plugins/servlet/samlsso`

    c. Klik **op Extra URL's instellen** en voer de volgende stap uit als u de toepassing wilt configureren in de sp-modus: **SP**

    ![Informatie over domein en URL's voor eenmalige aanmelding met SAML SSO for Jira by resolution GmbH](common/metadata-upload-additional-signon.png)

    In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://<server-base-url>/plugins/servlet/samlsso`

    > [!NOTE]
    > Voor de URL-functie-id, antwoord-URL en aanmeldings-URL u ** \<de server-base-url vervangen door>** met de basis-URL van uw Jira-exemplaar. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal. Als u een probleem hebt, neem dan contact met ons op bij [SAML SSO voor Jira door resolutie GmbH Client support team](https://www.resolution.de/go/support).

4. Download op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** de **XML met aalmetagegevens** van de Federatie en sla deze op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

### <a name="configure-the-saml-sso-plugin-of-your-jira-instance"></a>De SAML SSO-plug-in van uw Jira-exemplaar configureren 

1. Meld u in een ander browservenster aan bij uw Jira-instantie als beheerder.

2. Plaats de muisaanwijzer op het tandwiel aan de rechterkant en klik op **Apps beheren**.
    
    ![Eenmalige aanmelding configureren](./media/samlssojira-tutorial/addon1.png)

3. Als u wordt doorgestuurd naar de pagina Administrator Access, voert u het **wachtwoord** in en klikt u op **Bevestigen.**

    ![Eenmalige aanmelding configureren](./media/samlssojira-tutorial/addon2.png)

4. Jira verwijst je normaal gesproken door naar de Atlassian marktplaats. Als dit niet het zo is, klikt u op **Nieuwe apps zoeken** in het linkerdeelvenster. Zoek naar **SAML Single Sign On (SSO) voor JIRA** en klik op de knop **Installeren** om de SAML-plug-in te installeren.

    ![Eenmalige aanmelding configureren](./media/samlssojira-tutorial/store.png)

5. De installatie van de invoegtoepassing wordt gestart. Als dit is gedaan, klikt u op de knop **Sluiten.**

    ![Eenmalige aanmelding configureren](./media/samlssojira-tutorial/store-2.png)

    ![Eenmalige aanmelding configureren](./media/samlssojira-tutorial/store-3.png)

6. Klik vervolgens op **Beheren**.

    ![Eenmalige aanmelding configureren](./media/samlssojira-tutorial/store-4.png)
    
8. Klik daarna op **Configureren** om de zojuist geïnstalleerde plug-in te configureren.

    ![Eenmalige aanmelding configureren](./media/samlssojira-tutorial/store-5.png)

9. Klik in de wizard **SAML SingleSignOn-plug-inconfiguratie** op **Nieuwe IdP toevoegen** om Azure AD te configureren als een nieuwe identiteitsprovider.

    ![Eenmalige aanmelding configureren](./media/samlssojira-tutorial/addon4.png) 

10. Voer op de pagina **Uw SAML-identiteitsprovider kiezen** de volgende stappen uit:

    ![Eenmalige aanmelding configureren](./media/samlssojira-tutorial/addon5a.png)
 
    a. Stel **Azure AD** als het type id-provider.
    
    b. Voeg de **naam** van de identiteitsprovider toe (bijvoorbeeld Azure AD).
    
    c. Voeg een (optionele) **beschrijving** van de identiteitsprovider toe (bijvoorbeeld Azure AD).
    
    d. Klik op **Volgende**.
    
11. Klik op de **configuratiepagina van de identiteitsprovider** op **Volgende**.
 
    ![Eenmalige aanmelding configureren](./media/samlssojira-tutorial/addon5b.png)

12. Op de pagina **SAML-IDP-metagegevens importeren** voert u de volgende stappen uit:

    ![Eenmalige aanmelding configureren](./media/samlssojira-tutorial/addon5c.png)

    a. Klik op de knop **XML-bestand met metagegevens selecteren** en kies het **XML-bestand met federatiemetagegevens** dat u eerder hebt gedownload.

    b. Klik op de knop **Importeren.**
     
    c. Wacht kort tot de import slaagt.  
     
    d. Klik op de knop **Next**
    
13. Klik op **de kenmerk- en transformatiepagina van gebruikersnaam** op de knop **Volgende.**

    ![Eenmalige aanmelding configureren](./media/samlssojira-tutorial/addon5d.png)
    
14. Klik **op** de pagina Gebruiker maken en bijwerken op **& volgende opslaan** om de instellingen op te slaan.
    
    ![Eenmalige aanmelding configureren](./media/samlssojira-tutorial/addon6a.png)
    
15. Klik **op** de pagina Uw instellingen testen op **Test & configureren handmatig configureren** om de gebruikerstest voorlopig over te slaan. Dit wordt uitgevoerd in de volgende sectie en vereist een aantal instellingen in de Azure-portal.
    
    ![Eenmalige aanmelding configureren](./media/samlssojira-tutorial/addon6b.png)
    
16. Klik op **OK** om de waarschuwing over te slaan.
    
    ![Eenmalige aanmelding configureren](./media/samlssojira-tutorial/addon6c.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken. Met de gebruiker test u eenmalige aanmelding.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Kies **Nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. Voer **in**de eigenschappen gebruiker de volgende stappen uit:

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer **Britta Simon**in het veld **Naam** in.
  
    b. Voer **in** het veld <b>BrittaSimon@contoso.com</b>Gebruikersnaam .

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik **op Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie voegt u Britta Simon toe aan de bedrijfstoepassing, waarmee ze eenmalige aanmelding kan gebruiken.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**. 

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Zoek in de lijst met toepassingen naar de bedrijfstoepassing die u in het begin van deze zelfstudie hebt gemaakt. Als u de stappen van de tutorial volgt, heet het **SAML SSO voor Jira door resolutie GmbH.** Als je het een andere naam hebt gegeven, zoek dan naar die naam.

    ![De koppeling SAML SSO for Jira by resolution GmbH in de lijst met toepassingen](common/all-applications.png)

3. Klik in het linkerdeelvenster op **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer **In** het dialoogvenster Gebruikers en groepen De optie **Britta Simon** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.

6. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.

7. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

### <a name="create-the-test-user-also-in-jira"></a>Maak de testgebruiker ook in Jira

Om Azure AD-gebruikers in staat te stellen zich bij SAML SSO voor Jira aan te melden bij resolutie GmbH, moeten ze bij resolutie GmbH worden ingericht in SAML SSO voor Jira. Voor het geval van deze tutorial, moet je de provisioning met de hand te doen. Er zijn echter ook andere provisioning modellen beschikbaar voor de SAML SSO plugin by resolution, bijvoorbeeld **Just In Time** provisioning. Raadpleeg hun documentatie bij [SAML SSO by resolution GmbH](https://wiki.resolution.de/doc/saml-sso/latest/all). Als u er een vraag over hebt, neemt u contact op met ondersteuning [bij ondersteuning voor de oplossing.](https://www.resolution.de/go/support)

**Voer de volgende stappen uit om handmatig een gebruikersaccount in te richten:**

1. Meld u aan bij de instantie Jira als beheerder.

2. Plaats de plaats van de knop over het tandwiel en selecteer **Gebruikersbeheer**.

   ![Werknemer toevoegen](./media/samlssojira-tutorial/user1.png)

3. Als u wordt doorgestuurd naar de pagina Administratortoegangs, voert u het **wachtwoord** in en klikt u op **Bevestigen.**

    ![Werknemer toevoegen](./media/samlssojira-tutorial/user2.png) 

4. Klik onder de sectie **Gebruikersbeheer** op **Gebruiker maken**.

    ![Werknemer toevoegen](./media/samlssojira-tutorial/user3-new.png) 

5. Voer op de pagina **'Nieuwe gebruiker maken'** de volgende stappen uit. U moet de gebruiker precies zo maken als in Azure AD:

    ![Werknemer toevoegen](./media/samlssojira-tutorial/user4-new.png) 

    a. Typ in het tekstvak **E-mailadres** het <b>BrittaSimon@contoso.com</b>e-mailadres van de gebruiker: .

    b. Typ in het tekstvak **Volledige naam** de volledige naam van de gebruiker: **Britta Simon**.

    c. Typ in het tekstvak **Gebruikersnaam** het e-mailadres van de gebruiker: <b>BrittaSimon@contoso.com</b>. 

    d. Voer **in** het tekstvak Wachtwoord het wachtwoord van de gebruiker in.

    e. Klik **op Gebruiker maken** om de gebruikerscreatie te voltooien.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Als u in het toegangsvenster op de tegel SAML SSO for Jira by resolution GmbH klikt, zou u automatisch moeten worden aangemeld bij het exemplaar van SAML SSO for Jira by resolution GmbH waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

U ook eenmalige aanmelding testen, als u naar [https://\<server-base-url>/plugins/servlet/samlsso navigeert.](https://\<server-base-url>/plugins/servlet/samlsso) Vervang ** \<server-base-url>** met de basis-URL van uw Jira-exemplaar.


## <a name="enable-single-sign-on-redirection-for-jira"></a>Omleiding van één aanmelding voor Jira inschakelen

Zoals vermeld in de sectie voor, zijn er momenteel twee manieren om de enkele sign-on trigger. Door de **Azure-portal** te gebruiken of **een speciale koppeling naar uw Jira-exemplaar te gebruiken.** Met de SAML SSO-plug-in by resolution GmbH u ook eenmalige aanmelding activeren door **simpelweg toegang te krijgen tot elke URL die naar uw Jira-instantie wijst.**

In wezen zullen alle gebruikers die toegang hebben tot Jira worden doorgestuurd naar de enkele aanmelding na het activeren van een optie in de plug-in.

Ga als volgt te werk **om**SSO-omleiding te activeren:

1. Toegang tot de configuratiepagina van de SAML SSO-plug-in in Jira.
1. Klik op **Omleiding** in het linkerdeelvenster.
![](./media/samlssojira-tutorial/ssore1.png)

1. Vink **SSO Redirect inschakelen**in.
![](./media/samlssojira-tutorial/ssore2.png) 

1. Druk op de knop **Instellingen opslaan** in de rechterbovenhoek.

Nadat u de optie hebt geactiveerd, u nog steeds de gebruikersnaam/wachtwoordprompt bereiken als de optie **Nosso inschakelen** is aangevinkt door te navigeren naar [https://\<server-base-url>/login.jsp?nosso](https://\<server-base-url>/login.jsp?nosso). Zoals altijd, vervangen ** \<server-base-url>** met uw basis URL.


## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

