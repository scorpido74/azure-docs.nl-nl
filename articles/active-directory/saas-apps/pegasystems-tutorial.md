---
title: 'Zelfstudie: Azure Active Directory-integratie met Pega-systemen | Microsoft Documenten'
description: In deze zelfstudie leert u hoe u eenmalige aanmelding tussen Azure Active Directory en Pega Systems configureert.
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
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "72026800"
---
# <a name="tutorial-azure-active-directory-integration-with-pega-systems"></a>Zelfstudie: Azure Active Directory-integratie met Pega-systemen

In deze zelfstudie leert u hoe u Pega-systemen integreren met Azure Active Directory (Azure AD).

Deze integratie biedt deze voordelen:

* U Azure AD gebruiken om te bepalen wie toegang heeft tot Pega Systems.
* U uw gebruikers automatisch laten aanmelden bij Pega Systems (eenmalige aanmelding) met hun Azure AD-accounts.
* U uw accounts beheren op één centrale locatie: de Azure-portal.

Zie [Eenmalige aanmelding voor toepassingen in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) voor meer informatie over de integratie van SaaS-apps met Azure AD.

Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u azure AD-integratie met Pega Systems wilt configureren, moet u het:

* Een Azure AD-abonnement Als u geen Azure [AD-omgeving](https://azure.microsoft.com/pricing/free-trial/)hebt, u zich aanmelden voor een proefversie van één maand.
* Een Pega Systems-abonnement met eenmalige aanmelding ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD single sign-on in een testomgeving.

* Pega Systems ondersteunt door SP geïnitieerde en idp-geïnitieerde SSO.

## <a name="add-pega-systems-from-the-gallery"></a>Pega-systemen toevoegen vanuit de galerie

Als u de integratie van Pega-systemen in Azure AD wilt instellen, moet u Pega-systemen uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Selecteer azure **active directory**in de [Azure-portal](https://portal.azure.com)in het linkerdeelvenster:

    ![Selecteer Azure Active Directory](common/select-azuread.png)

2. Ga naar **Enterprise-toepassingen** > **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een toepassing wilt toevoegen, selecteert u **Nieuwe toepassing** boven aan het venster:

    ![Nieuwe toepassing selecteren](common/add-new-app.png)

4. Voer **Pega Systems**in het zoekvak in. Selecteer **Pega-systemen** in de zoekresultaten en selecteer **Toevoegen**.

     ![Zoekresultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u Azure AD single sign-on met Pega Systems met behulp van een testgebruiker genaamd Britta Simon.
Als u eenmalige aanmelding wilt inschakelen, moet u een relatie tot stand brengen tussen een Azure AD-gebruiker en de desbetreffende gebruiker in Pega Systems.

Als u Azure AD-single sign-on met Pega Systems wilt configureren en testen, moet u de volgende stappen uitvoeren:

1. **[Configureer de single sign-on van Azure AD](#configure-azure-ad-single-sign-on)** om de functie voor uw gebruikers in te schakelen.
2. **[Configureren Pega Systems single sign-on](#configure-pega-systems-single-sign-on)** aan de toepassingszijde.
3. **[Maak een Azure AD-testgebruiker](#create-an-azure-ad-test-user)** om azure AD-enkele aanmelding te testen.
4. **[Wijs de Azure AD-testgebruiker](#assign-the-azure-ad-test-user)** toe om azure AD-enkele aanmelding voor de gebruiker in te schakelen.
5. **[Maak een Pega Systems-testgebruiker](#create-a-pega-systems-test-user)** die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Test eenmalige aanmelding](#test-single-sign-on)** om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie schakelt u Azure AD single sign-on in de Azure-portal in.

Als u Azure AD-melding wilt configureren met Pega Systems, gaat u als volgt te werk:

1. Selecteer in de [Azure-portal](https://portal.azure.com/)op de pagina Met de integratie **van pega-systemen-toepassingen** de optie **Enkele aanmelding:**

    ![Eén aanmelding selecteren](common/select-sso.png)

2. Selecteer in het dialoogvenster **Een enkele aanmeldingsmethode** selecteren de **SAML/WS-Fed-modus** om eenmalige aanmelding in te schakelen:

    ![Eén aanmeldingsmethode selecteren](common/select-saml-option.png)

3. Selecteer op de pagina **Eén aanmelding instellen met SAML** het pictogram **Bewerken** om het dialoogvenster **BasisSAML-configuratie** te openen:

    ![Bewerkpictogram](common/edit-urls.png)

4. Voer in het dialoogvenster **BasisSAML-configuratie** de volgende stappen uit als u de toepassing in de idp-modus wilt configureren.

    ![Dialoogvenster BasisSAML-configuratie](common/idp-intiated.png)

    1. Voer in het vak **Id** een URL in dit patroon in:

       `https://<customername>.pegacloud.io:443/prweb/sp/<instanceID>`

    1. Voer in het vak **URL van antwoord** een URL in dit patroon in:

       `https://<customername>.pegacloud.io:443/prweb/PRRestService/WebSSO/SAML/AssertionConsumerService`

5. Als u de toepassing wilt configureren in de door SP geïnitieerde modus, selecteert u **Extra URL's instellen** en voert u de volgende stappen uit.

    ![Pega Systems Domain en URL's single sign-on informatie](common/both-advanced-urls.png)

    1. Voer in het vak **Aanmelding op URL** de waarde van het teken op URL in.

    1. Voer in het vak **Relaystatus** een URL in dit patroon in:`https://<customername>.pegacloud.io/prweb/sso`

    > [!NOTE]
    > De waarden die hier worden opgegeven zijn tijdelijke aanduidingen. U moet de werkelijke id, de URL van het antwoord, de URL van de URL aanmelden en de status-URL doorgeven. U de URL-waarden van de id en het antwoord ophalen uit een Pega-toepassing, zoals later in deze zelfstudie wordt uitgelegd. Neem contact op met het [ondersteuningsteam van Pega Systems](https://www.pega.com/contact-us)om de waarde van de relaisstatus te krijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

6. De Pega Systems applicatie moet de SAML beweringen te worden in een specifiek formaat. Als u ze in de juiste indeling wilt krijgen, moet u aangepaste toewijzingen toevoegen aan uw SAML-tokenkenmerkenconfiguratie. In de volgende schermafbeelding worden de standaardkenmerken weergegeven. Selecteer het pictogram **Bewerken** om het dialoogvenster **Gebruikerskenmerken te** openen:

    ![Gebruikerskenmerken](common/edit-attribute.png)

7. Naast de kenmerken in de vorige schermafbeelding, vereist de Pega Systems-toepassing nog een paar kenmerken die moeten worden doorgegeven in de SAML-respons. Voer in het gedeelte **Gebruikersclaims** van het dialoogvenster **Gebruikerskenmerken** de volgende stappen uit om deze SAML-tokenkenmerken toe te voegen:

    
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

    1. Selecteer **Nieuwe claim toevoegen** om het dialoogvenster **Gebruikersclaims beheren** te openen:

    ![Nieuwe claim toevoegen selecteren](common/new-save-attribute.png)

    ![Het dialoogvenster Gebruikersclaims beheren](common/new-attribute-details.png)

    1. Typ in het tekstvak **Naam** de naam van het kenmerk die voor die rij wordt weergegeven.

    1. Laat het vak **Naamruimte** leeg.

    1. Selecteer **Kenmerk**voor de **bron**.

    1. Selecteer in de lijst **Bronkenmerk** de kenmerkwaarde die voor die rij wordt weergegeven.

    1. Selecteer **Ok**.

    1. Selecteer **Opslaan**.

8. Selecteer op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** de koppeling **Downloaden** naast **Federation Metadata XML**en sla het certificaat op uw computer op:

    ![De koppeling om het certificaat te downloaden](common/metadataxml.png)

9. Kopieer in de sectie **Pega Systems instellen** de juiste URL's op basis van uw vereisten.

    ![De configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    1. **Aanmeldings-URL**.

    1. **Azure AD-id**.

    1. **Url van afmelden**.

### <a name="configure-pega-systems-single-sign-on"></a>Eenmalig aanmelden pega-systemen configureren

1. Als u eenmalige aanmelding wilt configureren aan de kant **van Pega Systems,** meldt u zich aan bij de Pega Portal met een beheerdersaccount in een ander browservenster.

2. Selecteer > **SysAdmin-verificatieservice** > **Authentication Service** **maken:**

    ![Verificatieservice selecteren](./media/pegasystems-tutorial/tutorial_pegasystems_admin.png)
    
3. Voer de volgende stappen uit in het scherm **Verificatieservice maken.**

    ![Scherm Verificatieservice maken](./media/pegasystems-tutorial/tutorial_pegasystems_admin1.png)

    1. Selecteer **SAML 2.0**in de lijst **Type** .

    1. Voer in het vak **Naam** een naam in (bijvoorbeeld **Azure AD SSO).**

    1. Voer in het vak **Korte beschrijving** een beschrijving in.  

    1. Selecteer **Maken en openen**.
    
4. Selecteer in de **sectie Gegevens van de Identiteitsprovider (IdP)** de optie **IdP-metagegevens importeren** en blader naar het metagegevensbestand dat u hebt gedownload van de Azure-portal. Klik **op Verzenden** om de metagegevens te laden:

    ![Informatiesectie Identiteitsprovider (IdP)](./media/pegasystems-tutorial/tutorial_pegasystems_admin2.png)
    
    De import vult de IdP-gegevens zoals hier wordt weergegeven:

    ![Geïmporteerde IdP-gegevens](./media/pegasystems-tutorial/tutorial_pegasystems_admin3.png)
    
6. Voer de volgende stappen uit in de sectie **Instellingen van de Serviceprovider (SP).**

    ![Instellingen voor serviceproviders](./media/pegasystems-tutorial/tutorial_pegasystems_admin4.png)

    1. Kopieer de waarde **entiteitsidentificatie** en plak deze in het vak **Identifier** in de sectie **BasisSAML-configuratie** in de Azure-portal.

    1. Kopieer de **locatiewaarde van de Bevestigingsconsumentenservice (ACS)** en plak deze in het vak **URL beantwoorden** in de sectie **BasisSAML-configuratie** in de Azure-portal.

    1. Selecteer **Aanvraagondertekenen uitschakelen**.

7. Selecteer **Opslaan**.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een testgebruiker met de naam Britta Simon in de Azure-portal.

1. Selecteer in de Azure-portal **Azure Active Directory** in het linkerdeelvenster, selecteer **Gebruikers**en selecteer **Alle gebruikers:**

    ![Selecteer Alle gebruikers](common/users.png)

2. Selecteer **Nieuwe gebruiker** boven aan het scherm:

    ![Nieuwe gebruiker selecteren](common/new-user.png)

3. Voer **in** het dialoogvenster Gebruiker de volgende stappen uit.

    ![Dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het vak **Naam****Britta Simon**in.
  
    b. Voer in het vak **Gebruikersnaam** **brittasimon@\<uw\< bedrijfsdomein in>. uitbreiding>**. (Bijvoorbeeld .) BrittaSimon@contoso.com

    c. Selecteer **Wachtwoord weergeven**en noteer de waarde in het vak **Wachtwoord.**

    d. Selecteer **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie stelt u Britta Simon in staat om Azure single sign-on te gebruiken door haar toegang te verlenen tot Pega Systems.

1. Selecteer in de Azure-portal **Enterprise-toepassingen,** selecteer **Alle toepassingen**en selecteer **Pega-systemen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Pega Systems**in de lijst met toepassingen .

    ![Lijst van aanvragen](common/all-applications.png)

3. Selecteer in het linkerdeelvenster **gebruikers en groepen:**

    ![Gebruikers en groepen selecteren](common/users-groups-blade.png)

4. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Gebruiker toevoegen selecteren](common/add-assign-user.png)

5. Selecteer **In** het dialoogvenster Gebruikers en groepen de optie **Britta Simon** in de gebruikerslijst en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst. Klik op de knop **Selecteren** onder aan het scherm.

7. Selecteer **Toewijzen** in het dialoogvenster **Toewijzing toevoegen**.

### <a name="create-a-pega-systems-test-user"></a>Een testgebruiker van Pega Systems maken

Vervolgens moet u een gebruiker genaamd Britta Simon maken in Pega Systems. Werk samen met het ondersteuningsteam van [Pega Systems](https://www.pega.com/contact-us) om gebruikers te maken.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

Nu moet u uw Azure AD-configuratie met eenmalige aanmelding testen met behulp van het Access-paneel.

Wanneer u de tegel Pega Systems selecteert in het toegangspaneel, moet u automatisch worden aangemeld bij het voorbeeld Pega Systems waarvoor u SSO hebt ingesteld. Zie [Apps openen en gebruiken op de portal Mijn apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)voor meer informatie.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Tutorials for integrating SaaS applications with Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Zelfstudies voor het integreren van SaaS-toepassingen met Azure Active Directory)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)