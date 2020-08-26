---
title: 'Zelfstudie: Azure Active Directory-integratie met Pega Systems | Microsoft Docs'
description: In deze zelfstudie leert u hoe u eenmalige aanmelding kunt configureren tussen Azure Active Directory en Pega Systems.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/26/2019
ms.author: jeedes
ms.openlocfilehash: 3dd09770ef5f747f008090368c35b1f274374c5f
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/18/2020
ms.locfileid: "88549066"
---
# <a name="tutorial-azure-active-directory-integration-with-pega-systems"></a>Zelfstudie: Azure Active Directory-integratie met Pega Systems

In deze zelfstudie leert u hoe u Pega Systems integreert met Azure Active Directory (Azure AD).

Deze integratie biedt de volgende voordelen:

* U kunt in Azure AD bepalen wie er toegang krijgt tot Pega Systems.
* U kunt inschakelen dat gebruikers automatisch met hun Azure Active Directory-account worden aangemeld bij Pega Systems (eenmalige aanmelding).
* U kunt uw accounts vanaf één locatie beheren: de Azure-portal.

Zie [Eenmalige aanmelding voor toepassingen in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) voor meer informatie over de integratie van SaaS-apps met Azure AD.

Als u geen abonnement op Azure hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie met Pega Systems wilt configureren, hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u zich registreren voor een [proefabonnement van één maand](https://azure.microsoft.com/pricing/free-trial/).
* Een abonnement op Pega Systems waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Pega Systems ondersteunt door SP geïnitieerde en door IdP geïnitieerde eenmalige aanmelding.

## <a name="add-pega-systems-from-the-gallery"></a>Pega Systems toevoegen vanuit de galerie

Voor het instellen van de integratie van Pega Systems met Azure Active Directory moet u Pega Systems vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. In de [Azure-portal](https://portal.azure.com), selecteert u in het linkerdeelvenster **Azure Active Directory**:

    ![Selecteer Azure Active Directory](common/select-azuread.png)

2. Ga naar **Bedrijfstoepassingen** > **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een toepassing wilt toevoegen, selecteert u **Nieuwe toepassing** boven aan het venster:

    ![Selecteer Nieuwe toepassing](common/add-new-app.png)

4. Voer **Pega Systems** in in het zoekvak. Selecteer **Pega Systems** in de zoekresultaten en selecteer vervolgens **Toevoegen**.

     ![Zoekresultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u eenmalige aanmelding voor Azure AD met Pega Systems door een testgebruiker te gebruiken met de naam Britta Simon.
Om eenmalige aanmelding in te schakelen, moet u een relatie maken tussen een Azure AD-gebruiker en de desbetreffende gebruiker in Pega Systems.

Als u eenmalige aanmelding voor Azure AD met Pega Systems wilt configureren en testen, moet u deze stappen voltooien:

1. **[Eenmalige aanmelding voor Azure AD configureren](#configure-azure-ad-single-sign-on)** om de functie voor uw gebruikers in te schakelen.
2. **[Configureer Pega Systems voor eenmalige aanmelding](#configure-pega-systems-single-sign-on)** aan de kant van de toepassing.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** om eenmalige aanmelding van Azure AD te testen.
4. **[Wijs de Azure AD-testgebruiker toe](#assign-the-azure-ad-test-user)** om eenmalige aanmelding in te schakelen voor de gebruiker.
5. **[Maak een Pega Systems-testgebruiker](#create-a-pega-systems-test-user)** die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Als u eenmalige aanmelding voor Azure AD met Pega Systems wilt configureren, moet u deze stappen voltooien:

1. In de [Azure Portal](https://portal.azure.com/) selecteert u **Eenmalige aanmelding** op de integratiepagina van de toepassing **Pega Systems**:

    ![Selecteer Eenmalige aanmelding](common/select-sso.png)

2. Selecteer in het dialoogvenster **Selecteer een methode voor eenmalige aanmelding** de modus **SAML/WS-Fed** om eenmalige aanmelding in te schakelen:

    ![Selecteer een methode voor eenmalige aanmelding](common/select-saml-option.png)

3. Selecteer op de pagina **Eenmalige aanmelding instellen met SAML** het pictogram voor **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen:

    ![Pictogram bewerken](common/edit-urls.png)

4. In het dialoogvenster **Standaard SAML-configuratie** voert u de volgende stappen uit als u de toepassing in de door IdP geïnitieerde modus wilt configureren.

    ![Dialoogvenster Standaard SAML-configuratie](common/idp-intiated.png)

    1. Voer in het vak **Id** een URL met dit patroon in:

       `https://<customername>.pegacloud.io:443/prweb/sp/<instanceID>`

    1. Voer in het vak **Antwoord-URL** een URL met dit patroon in:

       `https://<customername>.pegacloud.io:443/prweb/PRRestService/WebSSO/SAML/AssertionConsumerService`

5. Als u de toepassing in de door SP geïnitieerde modus wilt configureren, selecteert u **Extra URL's instellen** en voert u de volgende stappen uit.

    ![Domein- en URL-gegevens voor eenmalige aanmelding bij Pega Systems](common/both-advanced-urls.png)

    1. Voer in het vak **Aanmeldings-URL** de aanmeldwaarde van de URL in.

    1. Voer in het vak **Relaystatus** een URL met dit patroon in: `https://<customername>.pegacloud.io/prweb/sso`

    > [!NOTE]
    > De waarden die hier worden vermeld, zijn tijdelijke aanduidingen. U moet de werkelijke id, antwoord-URL, aanmeldings-URL en relaystatus gebruiken. U kunt de id-en antwoord-URL-waarden ophalen uit een Pega-toepassing, zoals verderop in deze zelfstudie wordt uitgelegd. Neem contact op met het [ondersteuningsteam van PegaSystems](https://www.pega.com/contact-us)als u de waarde voor de relaystatus wilt ophalen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

6. Voor de Pega Systems-toepassing moeten de SAML-asserties een specifieke indeling hebben. Om deze in de juiste indeling te krijgen, moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van uw SAML-tokenkenmerken. Op de volgende schermopname worden de standaardkenmerken weergegeven. Selecteer het pictogram **Bewerken** om het dialoogvenster **Gebruikerskenmerken** te openen:

    ![Gebruikerskenmerken](common/edit-attribute.png)

7. Naast de kenmerken die in de vorige schermopname worden weergegeven, moeten voor de Pega Systems-toepassing nog enkele kenmerken worden doorgegeven in het SAML-antwoord. In de sectie **Gebruikersclaims** in het dialoogvenster **Gebruikerskenmerken** voert u de volgende stappen uit om deze SAML-tokenkenmerken toe te voegen:

    
   - `uid`
   - `cn`
   - `mail`
   - `accessgroup`  
   - `organization`  
   - `orgdivision`
   - `orgunit`
   - `workgroup`  
   - `Phone`

    > [!NOTE]
    > Deze waarden zijn specifiek voor uw organisatie. Geef de relevante waarden op.

    1. Selecteer **Nieuwe claim toevoegen** om het dialoogvenster **Gebruikersclaims beheren** te openen:

    ![Selecteer Nieuwe claim toevoegen](common/new-save-attribute.png)

    ![Het dialoogvenster Gebruikersclaims beheren](common/new-attribute-details.png)

    1. Typ in het tekstvak **Naam** de naam van het kenmerk die voor die rij wordt weergegeven.

    1. Laat het vak **Naamruimte** leeg.

    1. Als **Bron** selecteert u **Kenmerk**.

    1. Selecteer de kenmerkwaarde voor die rij in de lijst met **Bronkenmerken**.

    1. Selecteer **OK**.

    1. Selecteer **Opslaan**.

8. Op de pagina **Eenmalige aanmelding met SAML instellen**, in de sectie **SAML-handtekeningcertificaat**, selecteert u de koppeling **Downloaden** naast **XML-bestand met federatieve metagegevens** overeenkomstig uw behoeften, en slaat u het certificaat op uw computer op:

    ![De koppeling om het certificaat te downloaden](common/metadataxml.png)

9. Kopieer in de sectie **Pega Systems instellen** de juiste URL's op basis van uw vereisten.

    ![De configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    1. **Aanmeldings-URL**.

    1. **Azure AD-id**.

    1. **Afmeldings-URL**.

### <a name="configure-pega-systems-single-sign-on"></a>Eenmalige aanmelding van Pega Systems configureren

1. Meld u aan bij de Pega-Portal met een beheerdersaccount in een ander browservenster om eenmalige aanmelding te configureren aan de **Pega Systems**-zijde.

2. Selecteer **Maken** > **SysAdmin** > **Verificatieservice**:

    ![Selecteer verificatieservice](./media/pegasystems-tutorial/tutorial_pegasystems_admin.png)
    
3. Voer de volgende stappen uit op het scherm **Verificatieservice maken**.

    ![Verificatieservice-scherm maken](./media/pegasystems-tutorial/tutorial_pegasystems_admin1.png)

    1. Selecteer **SAML 2.0** in de lijst **Type**.

    1. Voer in het vak **Naam** een naam in (bijvoorbeeld **Azure AD SSO**).

    1. Voer een beschrijving in in het venster **Korte beschrijving**.  

    1. Selecteer **Maken en openen**.
    
4. Selecteer in de sectie **Informatie over id-provider (IdP)** **Metagegevens van IdP importeren** en blader naar het metagegevensbestand dat u hebt gedownload van Azure Portal. Klik op **Verzenden** om de metagegevens te laden:

    ![Sectie Informatie over id-provider (IdP)](./media/pegasystems-tutorial/tutorial_pegasystems_admin2.png)
    
    Bij het importeren worden de IdP-gegevens ingevuld, zoals hier wordt weer gegeven:

    ![Geïmporteerde IdP-gegevens](./media/pegasystems-tutorial/tutorial_pegasystems_admin3.png)
    
6. Voltooi de volgende stappen in de sectie **Instellingen serviceprovider (SP)** .

    ![Instellingen voor serviceprovider](./media/pegasystems-tutorial/tutorial_pegasystems_admin4.png)

    1. Kopieer de waarde van de **Entiteits-id** en plak deze in het tekstvak voor de **id** in de sectie **Standaard SAML-configuratie** in Azure Portal.

    1. Kopieer de waarde van de **Assertion Consumer Service (ACS)-locatie** en plak deze in het tekstvak **Antwoord-URL** in de sectie **Standaard SAML-configuratie** in Azure Portal.

    1. Selecteer **Uitschakelen aanvraag ondertekening**.

7. Selecteer **Opslaan**.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie gaat u een testgebruiker met de naam Britta Simon maken in de Azure-portal.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**:

    ![Selecteer Alle gebruikers](common/users.png)

2. Selecteer **Nieuwe gebruiker** boven aan het scherm:

    ![Selecteer Nieuwe gebruiker](common/new-user.png)

3. Voltooi in het dialoogvenster **Gebruiker** de volgende stappen.

    ![Dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het vak **Naam** **Britta Simon**in.
  
    b. Voer in het vak **Gebruikersnaam** **brittasimon@\<yourcompanydomain>.\<extension>** in. (Bijvoorbeeld: BrittaSimon@contoso.com.)

    c. Selecteer **Wachtwoord weergeven** en noteer de waarde die in het vak **Wachtwoord** wordt getoond.

    d. Selecteer **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie stelt u Britta Simon in staat gebruik te maken van eenmalige aanmelding van Azure door haar toegang te geven tot Pega Systems.

1. Selecteer in Azure Portal achtereenvolgens **Ondernemingstoepassingen**, **Alle toepassingen** en **Pega Systems**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Pega Systems** in de lijst met toepassingen.

    ![Lijst met toepassingen](common/all-applications.png)

3. Selecteer in het linkerdeelvenster **Gebruikers en groepen**:

    ![Gebruikers en groepen selecteren](common/users-groups-blade.png)

4. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Gebruiker toevoegen selecteren](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de gebruikerslijst en selecteer vervolgens de knop **Selecteren** onderaan het scherm.

6. Als u een rolwaarde verwacht in de SAML-assertie, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst. Klik onder aan het scherm op de knop **Selecteren**.

7. Selecteer **Toewijzen** in het dialoogvenster **Toewijzing toevoegen**.

### <a name="create-a-pega-systems-test-user"></a>Maak een Pega Systems-testgebruiker

Vervolgens moet u een gebruiker met de naam Britta Simon maken in Pega Systems. Werk met het [ondersteuningsteam van Pega Systems](https://www.pega.com/contact-us) om gebruikers te maken.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

Nu moet u de configuratie voor eenmalige aanmelding van Azure AD testen met behulp van het toegangsvenster.

Wanneer u de tegel Pega Systems selecteert in het toegangsvenster, zou u automatisch moeten worden aangemeld bij de instantie van Pega Systems waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Toepassingen openen en gebruiken in de portal Mijn apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) voor meer informatie.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Tutorials for integrating SaaS applications with Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Zelfstudies voor het integreren van SaaS-toepassingen met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)