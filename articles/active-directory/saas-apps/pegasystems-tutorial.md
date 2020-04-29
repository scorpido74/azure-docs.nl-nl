---
title: 'Zelf studie: integratie Azure Active Directory met Pega-systemen | Microsoft Docs'
description: In deze zelf studie leert u hoe u eenmalige aanmelding kunt configureren tussen Azure Active Directory-en Pega-systemen.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 31acf80f-1f4b-41f1-956f-a9fbae77ee69
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/26/2019
ms.author: jeedes
ms.openlocfilehash: bd54466e054055ff84cd5bb2b28c5cc074ac0017
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "72026800"
---
# <a name="tutorial-azure-active-directory-integration-with-pega-systems"></a>Zelf studie: integratie Azure Active Directory met Pega-systemen

In deze zelf studie leert u hoe u Pega-systemen integreert met Azure Active Directory (Azure AD).

Deze integratie biedt de volgende voor delen:

* U kunt Azure AD gebruiken om te bepalen wie toegang heeft tot Pega-systemen.
* U kunt ervoor zorgen dat uw gebruikers automatisch worden aangemeld bij Pega-systemen (eenmalige aanmelding) met hun Azure AD-accounts.
* U kunt uw accounts op één centrale locatie beheren: de Azure Portal.

Zie [Eenmalige aanmelding voor toepassingen in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) voor meer informatie over de integratie van SaaS-apps met Azure AD.

Als u nog geen abonnement op Azure hebt, [Maak dan een gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie met Pega-systemen wilt configureren, hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u zich aanmelden voor een [proef versie van één maand](https://azure.microsoft.com/pricing/free-trial/).
* Een Pega Systems-abonnement waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u eenmalige aanmelding voor Azure AD in een test omgeving.

* Pega-systemen ondersteunen door SP geïnitieerde en door IdP geïnitieerde SSO.

## <a name="add-pega-systems-from-the-gallery"></a>Pega-systemen toevoegen vanuit de galerie

Als u de integratie van Pega-systemen in azure AD wilt instellen, moet u Pega-systemen uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Selecteer **Azure Active Directory**in het linkerdeel venster van de [Azure Portal](https://portal.azure.com):

    ![Selecteer Azure Active Directory](common/select-azuread.png)

2. Ga naar**alle toepassingen**in **bedrijfs toepassingen** > .

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een toepassing wilt toevoegen, selecteert u **nieuwe toepassing** boven aan het venster:

    ![Nieuwe toepassing selecteren](common/add-new-app.png)

4. Voer in het zoekvak Pega- **systemen**in. Selecteer **Pega-systemen** in de zoek resultaten en selecteer vervolgens **toevoegen**.

     ![Zoekresultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie kunt u eenmalige aanmelding voor Azure AD configureren en testen met Pega-systemen met behulp van een test gebruiker met de naam Julia Simon.
Om eenmalige aanmelding in te scha kelen, moet u een relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Pega-systemen.

U moet de volgende stappen uitvoeren om eenmalige aanmelding voor Azure AD te configureren en te testen met Pega-systemen:

1. **[Eenmalige aanmelding voor Azure AD configureren](#configure-azure-ad-single-sign-on)** om de functie voor uw gebruikers in te scha kelen.
2. **[Eenmalige aanmelding voor Pega-systemen configureren](#configure-pega-systems-single-sign-on)** aan de kant van de toepassing.
3. **[Maak een Azure AD-test gebruiker om de](#create-an-azure-ad-test-user)** eenmalige aanmelding van Azure ad te testen.
4. **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe om eenmalige aanmelding voor Azure AD in te scha kelen voor de gebruiker.
5. **[Een test gebruiker voor Pega-systemen maken](#create-a-pega-systems-test-user)** die is gekoppeld aan de Azure AD-weer gave van de gebruiker.
6. **[Test eenmalige aanmelding](#test-single-sign-on)** om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie schakelt u eenmalige aanmelding voor Azure AD in de Azure Portal.

Voer de volgende stappen uit om eenmalige aanmelding voor Azure AD te configureren met Pega-systemen:

1. Selecteer in de [Azure Portal](https://portal.azure.com/)op de pagina **Pega Systems** Application Integration de optie **eenmalige aanmelding**:

    ![Eenmalige aanmelding selecteren](common/select-sso.png)

2. Selecteer in het dialoog venster **eenmalige aanmelding selecteren** de optie **SAML/WS-** gegevensinvoermodus om eenmalige aanmelding in te scha kelen:

    ![Selecteer een methode voor eenmalige aanmelding](common/select-saml-option.png)

3. Selecteer op de pagina **eenmalige aanmelding met SAML instellen** het **bewerkings** pictogram om het dialoog venster **basis configuratie van SAML** te openen:

    ![Bewerkpictogram](common/edit-urls.png)

4. In het dialoog venster **basis configuratie van SAML** kunt u de volgende stappen uitvoeren als u de toepassing in de door IDP gestarte modus wilt configureren.

    ![Het dialoog venster basis configuratie van SAML](common/idp-intiated.png)

    1. Voer in het vak **id** een URL in dit patroon in:

       `https://<customername>.pegacloud.io:443/prweb/sp/<instanceID>`

    1. Voer in het vak **antwoord-URL** een URL in dit patroon in:

       `https://<customername>.pegacloud.io:443/prweb/PRRestService/WebSSO/SAML/AssertionConsumerService`

5. Als u de toepassing in de door SP geïnitieerde modus wilt configureren, selecteert u **extra Url's instellen** en voert u de volgende stappen uit.

    ![Informatie over Pega-systemen domein en Url's eenmalige aanmelding](common/both-advanced-urls.png)

    1. Voer in het vak **URL voor aanmelden** de waarde voor de AANMELDINGS-URL in.

    1. Voer in het vak **Relay-status** een URL in dit patroon in:`https://<customername>.pegacloud.io/prweb/sso`

    > [!NOTE]
    > De waarden die hier worden vermeld, zijn tijdelijke aanduidingen. U moet de daad werkelijke id, de antwoord-URL, de URL voor het aanmelden en de URL voor de relay-status gebruiken. U kunt de id-en antwoord-URL-waarden ophalen uit een Pega-toepassing, zoals verderop in deze zelf studie wordt uitgelegd. Neem contact op met het [ondersteunings team van Pega Systems](https://www.pega.com/contact-us)om de waarde voor de relay-status op te halen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

6. Voor de toepassing Pega Systems moeten de SAML-bevestigingen een specifieke indeling hebben. Om ze in de juiste indeling te krijgen, moet u aangepaste kenmerk toewijzingen toevoegen aan de configuratie van uw SAML-token kenmerken. De volgende scherm afbeelding toont de standaard kenmerken. Selecteer het **bewerkings** pictogram om het dialoog venster **gebruikers kenmerken** te openen:

    ![Gebruikers kenmerken](common/edit-attribute.png)

7. Naast de kenmerken die in de vorige scherm afbeelding worden weer gegeven, moeten voor de toepassing Pega Systems nog enkele kenmerken worden door gegeven aan het SAML-antwoord. In het gedeelte **gebruikers claims** van het dialoog venster **gebruikers kenmerken** voert u de volgende stappen uit om deze SAML-token kenmerken toe te voegen:

    
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
    > Deze waarden zijn specifiek voor uw organisatie. Geef de juiste waarden op.

    1. Selecteer **nieuwe claim toevoegen** om het dialoog venster **gebruikers claims beheren** te openen:

    ![Nieuwe claim toevoegen selecteren](common/new-save-attribute.png)

    ![Het dialoogvenster Gebruikersclaims beheren](common/new-attribute-details.png)

    1. Typ in het tekstvak **Naam** de naam van het kenmerk die voor die rij wordt weergegeven.

    1. Laat het vak **naam ruimte** leeg.

    1. Selecteer voor de **bron**de optie **kenmerk**.

    1. Selecteer in de lijst **bron kenmerk** de waarde van het kenmerk dat voor die rij wordt weer gegeven.

    1. Selecteer **OK**.

    1. Selecteer **Opslaan**.

8. Selecteer op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , de **Download** koppeling naast **federatieve meta gegevens-XML**, volgens uw vereisten en sla het certificaat op uw computer op:

    ![De koppeling om het certificaat te downloaden](common/metadataxml.png)

9. In de sectie **Pega Systems instellen** kopieert u de juiste url's, op basis van uw vereisten.

    ![De configuratie-Url's kopiëren](common/copy-configuration-urls.png)

    1. **Aanmeldings-URL**.

    1. **Id van Azure AD**.

    1. **Afmeldings-URL**.

### <a name="configure-pega-systems-single-sign-on"></a>Eenmalige aanmelding voor Pega-systemen configureren

1. Als u eenmalige aanmelding wilt configureren op de **Pega-systemen** , meldt u zich aan bij de Pega-Portal met een beheerders account in een ander browser venster.

2. Selecteer **Create** > **SysAdmin**sysadmin > -**verificatie service**maken:

    ![Verificatie service selecteren](./media/pegasystems-tutorial/tutorial_pegasystems_admin.png)
    
3. Voer de volgende stappen uit in het scherm **verificatie service maken** .

    ![Het scherm verificatie service maken](./media/pegasystems-tutorial/tutorial_pegasystems_admin1.png)

    1. Selecteer **SAML 2,0**in de lijst **type** .

    1. Voer in het vak **naam** een naam in (bijvoorbeeld **Azure AD SSO**).

    1. Voer in het vak **korte beschrijving** een beschrijving in.  

    1. Selecteer **maken en openen**.
    
4. Selecteer in de sectie informatie van de **identiteits provider (IDP)** de optie **IDP-meta gegevens importeren** en blader naar het meta gegevensbestand dat u hebt gedownload van de Azure Portal. Klik op **verzenden** om de meta gegevens te laden:

    ![Informatie over ID-provider (IdP)](./media/pegasystems-tutorial/tutorial_pegasystems_admin2.png)
    
    Bij het importeren worden de IdP-gegevens ingevuld, zoals hier wordt weer gegeven:

    ![Geïmporteerde IdP-gegevens](./media/pegasystems-tutorial/tutorial_pegasystems_admin3.png)
    
6. Voer de volgende stappen uit in de sectie instellingen van de **service provider (SP)** .

    ![Instellingen voor service providers](./media/pegasystems-tutorial/tutorial_pegasystems_admin4.png)

    1. Kopieer de waarde voor de **Entiteits-ID** en plak deze in het vak **id** in het gedeelte **basis configuratie van SAML** in de Azure Portal.

    1. Kopieer de waarde van de **Assertion Consumer Service (ACS)-locatie** en plak deze in het vak **antwoord-URL** in het gedeelte **basis configuratie van SAML** in de Azure Portal.

    1. Selecteer de optie voor het **ondertekenen van aanvragen uitschakelen**.

7. Selecteer **Opslaan**.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een test gebruiker met de naam Julia Simon in de Azure Portal.

1. Selecteer in de Azure Portal **Azure Active Directory** in het linkerdeel venster, selecteer **gebruikers**en selecteer vervolgens **alle gebruikers**:

    ![Selecteer Alle gebruikers](common/users.png)

2. Selecteer **nieuwe gebruiker** boven aan het scherm:

    ![Nieuwe gebruiker selecteren](common/new-user.png)

3. Voer in het dialoog venster **gebruiker** de volgende stappen uit.

    ![Dialoog venster gebruiker](common/user-properties.png)

    a. Voer in het vak **Naam****Britta Simon**in.
  
    b. Voer in het vak **gebruikers naam** **brittasimon@\<yourcompanydomain> in.\< extensie>**. (Bijvoorbeeld BrittaSimon@contoso.com.)

    c. Selecteer **wacht woord weer geven**en noteer de waarde in het vak **wacht woord** .

    d. Selecteer **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u Julia Simon in om de eenmalige aanmelding van Azure te gebruiken door haar toegang tot Pega-systemen te verlenen.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**, selecteer **alle toepassingen**en selecteer vervolgens **Pega Systems**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst met toepassingen **Pega Systems**.

    ![Lijst met toepassingen](common/all-applications.png)

3. Selecteer in het linkerdeel venster **gebruikers en groepen**:

    ![Gebruikers en groepen selecteren](common/users-groups-blade.png)

4. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Gebruiker toevoegen selecteren](common/add-assign-user.png)

5. Selecteer in het dialoog venster **gebruikers en groepen** de optie **Julia Simon** in de lijst met gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.

6. Als u een waarde van een rol verwacht in de SAML-verklaring, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst. Klik op de knop **selecteren** onder aan het scherm.

7. Selecteer **Toewijzen** in het dialoogvenster **Toewijzing toevoegen**.

### <a name="create-a-pega-systems-test-user"></a>Een test gebruiker voor een Pega-systeem maken

Vervolgens moet u in Pega-systemen een gebruiker met de naam Julia Simon maken. Werk met het [ondersteunings team van Pega Systems](https://www.pega.com/contact-us) om gebruikers te maken.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

Nu moet u de configuratie van de eenmalige aanmelding voor Azure AD testen met behulp van het toegangs venster.

Wanneer u de tegel Pega Systems selecteert in het toegangs venster, moet u automatisch worden aangemeld bij de Pega Systems-instantie waarvoor u SSO hebt ingesteld. Zie voor meer informatie [apps openen en gebruiken in de portal mijn apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Aanvullende bronnen

- [Tutorials for integrating SaaS applications with Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Zelfstudies voor het integreren van SaaS-toepassingen met Azure Active Directory)

- [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)