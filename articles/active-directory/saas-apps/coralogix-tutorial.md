---
title: 'Zelf studie: integratie Azure Active Directory met Coralogix | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Coralogix.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: ba79bfc1-992e-4924-b76a-8eb0dfb97724
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 1/2/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8589c366c029ab51c7cd740a1b63cff7c0481a51
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73158469"
---
# <a name="tutorial-azure-active-directory-integration-with-coralogix"></a>Zelf studie: integratie Azure Active Directory met Coralogix

In deze zelfstudie leert u hoe u Coralogix kunt integreren met Azure AD (Azure Active Azure).
Integratie van Coralogix met Azure AD biedt u de volgende voordelen:

* U kunt in Azure AD beheren wie toegang heeft tot Coralogix.
* U kunt ervoor zorgen dat uw gebruikers automatisch worden aangemeld bij Coralogix (eenmalige aanmelding) met hun Azure AD-accounts.
* U kunt uw accounts op één centrale locatie beheren: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om Azure AD-integratie met Coralogix te configureren:

- Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u een [proef versie van één maand](https://azure.microsoft.com/pricing/free-trial/)ontvangen.
- Een Coralogix-abonnement waarvoor eenmalige aanmelding is ingeschakeld. 

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Coralogix ondersteunt door SP geïnitieerde SSO.

## <a name="add-coralogix-from-the-gallery"></a>Coralogix toevoegen vanuit de galerie

Als u de integratie van Coralogix in azure AD wilt configureren, voegt u eerst Coralogix uit de galerie toe aan uw lijst met beheerde SaaS-apps.

Voer de volgende stappen uit om Coralogix toe te voegen uit de galerie:

1. Selecteer in de [Azure Portal](https://portal.azure.com)in het linkerdeel venster het **Azure Active Directory** pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u de knop **nieuwe toepassing** boven aan het dialoog venster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Coralogix**in het zoekvak. Selecteer **Coralogix** in het resultaten venster en selecteer vervolgens de knop **toevoegen** om de toepassing toe te voegen.

     ![Coralogix in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie kunt u eenmalige aanmelding voor Azure AD configureren en testen met Coralogix op basis van een test gebruiker met de naam Julia Simon.
Als u eenmalige aanmelding wilt gebruiken, moet u een koppeling tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Coralogix.

Als u eenmalige aanmelding voor Azure AD wilt configureren en testen met Coralogix, voltooit u eerst de volgende bouw stenen:

1. [Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-single-sign-on) zodat uw gebruikers deze functie kunnen gebruiken.
2. [Coralogix eenmalige aanmelding configureren](#configure-coralogix-single-sign-on) om de instellingen voor eenmalige aanmelding aan de kant van de toepassing te configureren.
3. [Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user) voor het testen van eenmalige aanmelding van Azure AD met Britta Simon.
4. [De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user) zodat Britta Simon gebruik kan maken van eenmalige aanmelding van Azure AD.
5. [Maak een Coralogix-test gebruiker](#create-a-coralogix-test-user) om een tegen hanger van Julia Simon in Coralogix te hebben dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
6. [Eenmalige aanmelding testen](#test-single-sign-on) om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding voor Azure AD te configureren met Coralogix:

1. Selecteer in de [Azure-portal](https://portal.azure.com/) op de pagina voor integratie van toepassingen met **Coralogix** de optie **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. Selecteer in het dialoog venster **eenmalige aanmelding selecteren** de optie **SAML** om eenmalige aanmelding in te scha kelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Selecteer op de pagina **Eenmalige aanmelding met SAML instellen** het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. Voer in het dialoog venster **basis configuratie van SAML** de volgende stappen uit:

    ![Informatie over eenmalige aanmelding bij Coralogix-domeinen en -URL's](common/sp-identifier.png)

    a. Voer in het vak **AANMELDINGS URL** een URL in met het volgende patroon: `https://<SUBDOMAIN>.coralogix.com`

    b. Voer in het tekstvak **id (Entiteits-ID)** een URL in, bijvoorbeeld:
    
    `https://api.coralogix.com/saml/metadata.xml`

    of

    `https://aws-client-prod.coralogix.com/saml/metadata.xml` 

    > [!NOTE]
    > De waarde voor de aanmeldings-URL is niet echt. Werk de waarde bij met de werkelijke aanmeldings-URL. Neem contact op met het [ondersteunings team van Coralogix-clients](mailto:info@coralogix.com) om de waarde op te halen. U kunt ook verwijzen naar de patronen in het gedeelte **basis configuratie van SAML** in de Azure Portal.

5. De Coralogix-toepassing verwacht de SAML-beweringen in een specifieke indeling. Configureer de volgende claims voor deze toepassing. U kunt de waarden van deze kenmerken vanuit de sectie **Gebruikerskenmerken** op de integratiepagina van de toepassing-beheren. Selecteer op de pagina **eenmalige aanmelding met SAML instellen** de knop **bewerken** om het dialoog venster **gebruikers kenmerken** te openen.

    ![image](common/edit-attribute.png)

6. Bewerk de claims in het gedeelte **gebruikers claims** in het dialoog venster **gebruikers kenmerken** met behulp van het **bewerkings** pictogram. U kunt de claims ook toevoegen met behulp van **nieuwe claim toevoegen** om het SAML-token kenmerk te configureren, zoals wordt weer gegeven in de vorige afbeelding. Voer dan de volgende stappen uit:
    
    a. Selecteer het **bewerkings pictogram** om het dialoog venster **gebruikers claims beheren** te openen.

    ![installatie kopie](./media/coralogix-tutorial/tutorial_usermail.png) ![installatie kopie](./media/coralogix-tutorial/tutorial_usermailedit.png)

    b. Selecteer in de lijst **Kies een indeling voor de naam-id** de optie **E-mailadres**.

    c. Selecteer in de lijst **Bronkenmerk** de optie **user.mail**.

    d. Selecteer **Opslaan**.

7. Selecteer op de pagina **eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekening certificaat** de optie **downloaden** om de **federatieve meta gegevens-XML** te downloaden uit de opgegeven opties volgens uw vereisten. Sla het vervolgens op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

8. Kopieer in de sectie **Coralogix instellen** de gewenste URL ('s).

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-coralogix-single-sign-on"></a>Eenmalige aanmelding voor Coralogix configureren

Als u eenmalige aanmelding wilt configureren aan de **Coralogix** zijde, verzendt u de gedownloade **federatieve meta gegevens-XML** en gekopieerde url's van de Azure Portal naar het [ondersteunings team van Coralogix](mailto:info@coralogix.com). Ze zorgen ervoor dat de SAML SSO-verbinding aan beide kanten juist is ingesteld.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken 

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer aan de bovenkant van het scherm de optie **nieuwe gebruiker**.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. Voer in het dialoog venster **gebruiker** de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam** **Britta Simon** in.
  
    b. Voer in het veld **gebruikers naam** 'brittasimon@yourcompanydomain.extension' in. In dit geval kunt u bijvoorbeeld 'brittasimon@contoso.com' invoeren.

    c. Schakel het selectie vakje **wacht woord weer geven** in en noteer de waarde die wordt weer gegeven in het vak **wacht woord** .

    d. Selecteer **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie geeft u Britta Simon toestemming voor gebruik van eenmalige aanmelding met Azure door haar toegang te geven tot Coralogix.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**, selecteer **alle toepassingen**en selecteer vervolgens **Coralogix**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst met toepassingen de optie **Coralogix**.

    ![De Coralogix-koppeling in de lijst met toepassingen](common/all-applications.png)

3. Selecteer **Gebruikers en groepen** in het menu aan de linkerkant.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Selecteer de knop **gebruiker toevoegen** . Selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoog venster **gebruikers en groepen** de optie **Julia Simon** in de lijst gebruikers. Klik vervolgens op de knop **selecteren** onder aan het scherm.

6. Als u een rolwaarde verwacht in de SAML-assertie, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst. Klik vervolgens op de knop **selecteren** onder aan het scherm.

7. Selecteer de knop **toewijzen** in het dialoog venster **toewijzing toevoegen** .

### <a name="create-a-coralogix-test-user"></a>Een Coralogix-test gebruiker maken

In deze sectie maakt u een gebruiker in Coralogix met de naam Britta Simon. Werk samen met het [Coralogix-ondersteunings team](mailto:info@coralogix.com) om de gebruikers toe te voegen aan het Coralogix-platform. U moet gebruikers maken en activeren voordat u eenmalige aanmelding gebruikt.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie van eenmalige aanmelding voor Azure AD met behulp van de MyApps-Portal.

Wanneer u de tegel Coralogix in de MyApps-Portal selecteert, moet u automatisch worden aangemeld bij Coralogix. Zie [Wat is de MyApps-Portal?](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)voor meer informatie over de MyApps-Portal.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelf studies voor het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

