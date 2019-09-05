---
title: 'Zelfstudie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met ServiceNow | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en ServiceNow.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: a5a1a264-7497-47e7-b129-a1b5b1ebff5b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 65526fe501b190f9bf76c58ab1c14b5ec35fe49d
ms.sourcegitcommit: aebe5a10fa828733bbfb95296d400f4bc579533c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/05/2019
ms.locfileid: "70376022"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-servicenow"></a>Zelfstudie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met ServiceNow

In deze zelf studie leert u hoe u ServiceNow integreert met Azure Active Directory (Azure AD). Wanneer u ServiceNow integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot ServiceNow.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld bij ServiceNow met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app (Software as a Service) met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* Een ServiceNow-abonnement met eenmalige aanmelding (SSO).
* Voor ServiceNow, een instantie of Tenant van ServiceNow, Calgary-versie of hoger.
* Voor ServiceNow Express, een instantie van ServiceNow Express, Helsinki versie of hoger.
* Voor de ServiceNow-tenant moet de [invoegtoepassing Multiple-Provider single sign-on (SSO)](https://wiki.servicenow.com/index.php?title=Multiple_Provider_Single_Sign-On#gsc.tab=0) zijn ingeschakeld. U kunt dit doen door [een service aanvraag](https://hi.service-now.com)in te dienen.
* Schakel voor automatische configuratie de multi-provider-invoegtoepassing voor ServiceNow in.
* Als u de toepassing ServiceNow Classic (Mobile) wilt installeren, gaat u naar de juiste Store en zoekt u naar de klassieke ServiceNow-toepassing. Down load het vervolgens.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving. ServiceNow ondersteunt door **SP** geïnitieerde SSO en [geautomatiseerde gebruikers inrichting](servicenow-provisioning-tutorial.md).

U kunt de klassieke ServiceNow-toepassing (Mobile) configureren met Azure AD voor het inschakelen van SSO. Het ondersteunt zowel Android-als iOS-gebruikers. In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

## <a name="add-servicenow-from-the-gallery"></a>ServiceNow toevoegen vanuit de galerie

Voor het configureren van de integratie van ServiceNow in Azure AD moet u ServiceNow uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met behulp van een werk-of school account of met behulp van een persoonlijke Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het linkerdeel venster.
1. Ga naar **bedrijfs toepassingen**en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Voer in de sectie **toevoegen vanuit de galerie** **ServiceNow** in het zoekvak in.
1. Selecteer **ServiceNow** uit het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on-for-servicenow"></a>Eenmalige aanmelding voor Azure AD configureren en testen voor ServiceNow

Azure AD SSO met ServiceNow configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in ServiceNow.

Als u Azure AD SSO wilt configureren en testen met ServiceNow, voltooit u de volgende bouw stenen:

1. [Configureer Azure AD SSO](#configure-azure-ad-sso) zodat uw gebruikers deze functie kunnen gebruiken.
    1. [Maak een Azure AD-test gebruiker om de](#create-an-azure-ad-test-user) eenmalige aanmelding van Azure ad te testen met B. Simon.
    1. [Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user) toe om B. Simon in te scha kelen voor het gebruik van eenmalige aanmelding voor Azure AD.
    1. [Configureer Azure AD SSO voor ServiceNow Express](#configure-azure-ad-sso-for-servicenow-express) om uw gebruikers in staat te stellen deze functie te gebruiken.
2. [Configureer ServiceNow](#configure-servicenow) om de SSO-instellingen aan de kant van de toepassing te configureren.
    1. [Maak een ServiceNow-test gebruiker](#create-servicenow-test-user) die een equivalent van B. Simon in ServiceNow heeft, gekoppeld aan de Azure AD-representatie van de gebruiker.
    1. [Configureer ServiceNow Express SSO](#configure-servicenow-express-sso) om de instellingen voor eenmalige aanmelding aan de kant van de toepassing te configureren.  
3. [Test SSO](#test-sso) om te controleren of de configuratie werkt.
4. [Test SSO voor ServiceNow Classic (Mobile)](#test-sso-for-servicenow-classic-mobile) om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in het [Azure Portal](https://portal.azure.com/)op de pagina Toepassings integratie van **ServiceNow** de sectie **beheren** . Selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Op de pagina **eenmalige aanmelding met SAML instellen** selecteert u het pictogram voor de pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Scherm afbeelding van het instellen van eenmalige aanmelding met de SAML-pagina, met het pictogram van een pen gemarkeerd](common/edit-urls.png)

4. Voer de volgende stappen uit in de sectie **basis configuratie van SAML** :

    a. Voer bij **URL voor aanmelden**een URL in die gebruikmaakt van het volgende patroon:`https://<instance-name>.service-now.com/navpage.do`

    b. Voer in **id (Entiteits-ID)** een URL in die gebruikmaakt van het volgende patroon:`https://<instance-name>.service-now.com`

    > [!NOTE]
    > Dit zijn geen echte waarden. U moet deze waarden bijwerken met de daad werkelijke aanmeldings-URL en-id, die verderop in de zelf studie wordt uitgelegd. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Ga op de pagina **eenmalige aanmelding met SAML instellen** naar **certificaat (base64)** in het gedeelte **SAML-handtekening certificaat** . Selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

   ![Scherm afbeelding van de sectie SAML-handtekening certificaat, met gemarkeerde down load](common/certificatebase64.png)

   a. Selecteer de knop kopiëren om de **URL voor de federatieve meta gegevens**van de app te kopiëren en plak deze in Klad blok. Deze URL wordt verderop in de zelf studie gebruikt.

    b. Selecteer **downloaden** om **certificaat (base64)** te downloaden en sla het certificaat bestand op uw computer op.

1. In de sectie **ServiceNow instellen** kopieert u de juiste url's, op basis van uw vereiste.

   ![Scherm afbeelding van de sectie ServiceNow instellen, met de Url's gemarkeerd](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Maak een testgebruiker Azure AD

In deze sectie maakt u een test gebruiker met de naam B. Simon, in de Azure Portal.

1. Selecteer **Azure Active Directory** > gebruikersvan > **alle gebruikers**in het linkerdeel venster van de Azure Portal.
1. Selecteer **nieuwe gebruiker** aan de bovenkant van het scherm.
1. Voer de volgende stappen uit in de eigenschappen van de **gebruiker** :
   1. Voer`B.Simon`in bij **naam**.  
   1. Voer de username@companydomain.extensionin bij **gebruikers naam**. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Selecteer **wacht woord weer geven**en noteer de waarde die wordt weer gegeven in het vak **wacht woord** .
   1. Selecteer **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan ServiceNow.

1. Selecteer in het Azure Portal**alle toepassingen**in **bedrijfs toepassingen** > .
1. Selecteer **ServiceNow** in de lijst met toepassingen.
1. Zoek op de pagina overzicht van de app de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![Scherm opname van de sectie beheren met gebruikers en groepen gemarkeerd](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**. Selecteer in het dialoog venster **toewijzing toevoegen** de optie **gebruikers en groepen**.

    ![Scherm opname van gebruikers en groepen, met gemarkeerde gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en kies vervolgens **selecteren**.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst. Kies vervolgens **selecteren**.
1. Selecteer **Toewijzen** in het dialoogvenster **Toewijzing toevoegen**.

### <a name="configure-azure-ad-sso-for-servicenow-express"></a>Azure AD SSO voor ServiceNow Express configureren

1. Selecteer in de [Azure Portal](https://portal.azure.com/)op de pagina **ServiceNow** Application Integration de optie **eenmalige aanmelding**.

    ![Scherm opname van de ServiceNow-toepassings integratie pagina, waarbij eenmalige aanmelding is gemarkeerd](common/select-sso.png)

2. Selecteer in het dialoog venster **eenmalige aanmelding selecteren** de optie **SAML/WS-** gegevensinvoermodus om eenmalige aanmelding in te scha kelen.

    ![Scherm opname van de methode voor eenmalige aanmelding selecteren, waarbij SAML is gemarkeerd](common/select-saml-option.png)

3. Selecteer op de pagina **eenmalige aanmelding met SAML instellen** het pictogram pen om het dialoog venster **eenvoudige SAML-configuratie** te openen.

    ![Scherm afbeelding van het instellen van eenmalige aanmelding met de SAML-pagina, met het pictogram van een pen gemarkeerd](common/edit-urls.png)

4. Voer de volgende stappen uit in de sectie **basis configuratie van SAML** :

    a. Voer voor de **aanmeldings-URL**een URL in die gebruikmaakt van het volgende patroon:`https://<instance-name>.service-now.com/navpage.do`

    b. Voer voor **id (Entiteits-ID)** een URL in die gebruikmaakt van het volgende patroon:`https://<instance-name>.service-now.com`

    > [!NOTE]
    > Dit zijn geen echte waarden. U moet deze waarden bijwerken met de daad werkelijke aanmeldings-URL en-id, die verderop in de zelf studie wordt uitgelegd. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Selecteer op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , **downloaden** om het **certificaat (base64)** te downloaden van de opgegeven opties, afhankelijk van uw vereisten. Sla het op uw computer op.

    ![Scherm afbeelding van het certificaat van het SAML-ondertekening, met de markering gedownload](common/certificatebase64.png)

6. U kunt Azure AD automatisch ServiceNow configureren voor verificatie op basis van SAML. Als u deze service wilt inschakelen, gaat u naar de sectie **ServiceNow instellen** en selecteert u **Stapsgewijze instructies weer geven** om het venster **aanmelden configureren** te openen.

    ![Scherm afbeelding van de sectie ServiceNow instellen met weer gave stapsgewijze instructies gemarkeerd](./media/servicenow-tutorial/tutorial_servicenow_configure.png)

7. Voer in het formulier **aanmelden configureren** de naam van uw ServiceNow-exemplaar, de gebruikers naam van de beheerder en het beheerders wachtwoord in. Selecteer **nu configureren**. Voor het werken met de gebruikers naam van de beheerder moet de rol **security_admin** zijn toegewezen in ServiceNow. Als u ServiceNow hand matig wilt configureren voor het gebruik van Azure AD als een SAML-ID-provider, selecteert u **eenmalige aanmelding hand matig configureren**. Kopieer de **Afmeldings-URL, de Azure ad-id en de aanmeldings-URL** uit de sectie snelle verwijzing.

    ![Scherm opname van het formulier voor aanmelden configureren, met nu configureren gemarkeerd](./media/servicenow-tutorial/configure.png "App-URL configureren")

## <a name="configure-servicenow"></a>ServiceNow configureren

1. Meld u aan bij uw ServiceNow-toepassing als beheerder.

2. Activeer de invoeg toepassing voor de **installatie van eenmalige aanmelding met meerdere providers** door de volgende stappen uit te voeren:

    a. Zoek in het linkerdeel venster naar het gedeelte **systeem definitie** in het zoekvak en selecteer vervolgens **invoeg toepassingen**.

    ![Scherm afbeelding van de sectie systeem definitie, met systeem definitie en invoeg toepassingen gemarkeerd](./media/servicenow-tutorial/tutorial_servicenow_03.png "Invoeg toepassing activeren")

    b. Zoek naar **integratie-installatie programma voor eenmalige aanmelding met meerdere providers**.

     ![Scherm opname van de pagina met systeem-invoeg toepassingen, met integratie-installatie programma voor eenmalige aanmelding met meerdere providers gemarkeerd](./media/servicenow-tutorial/tutorial_servicenow_04.png "Invoeg toepassing activeren")

    c. Selecteer de invoeg toepassing. Klik met de rechter muisknop en selecteer **activeren/bijwerken**.

     ![Scherm opname van de invoeg toepassing met de rechter muisknop, waarbij Activate/upgrade is gemarkeerd](./media/servicenow-tutorial/tutorial_activate.png "Invoeg toepassing activeren")

    d. Selecteer **activeren**.

     ![Scherm afbeelding van het dialoog venster invoeg toepassing activeren, met de markering gemarkeerd](./media/servicenow-tutorial/tutorial_activate1.png "Invoeg toepassing activeren")

3. Zoek in het linkerdeel venster de **SSO-sectie met meerdere providers** van de zoek balk en selecteer vervolgens **Eigenschappen**.

    ![Scherm opname van de SSO-sectie met meerdere providers, met SSO en eigenschappen van de multi-provider gemarkeerd](./media/servicenow-tutorial/tutorial_servicenow_06.png "App-URL configureren")

4. Voer in het dialoog venster **SSO-eigenschappen van meerdere providers** de volgende stappen uit:

    ![Scherm afbeelding van het dialoog venster SSO-eigenschappen van meerdere providers](./media/servicenow-tutorial/ic7694981.png "App-URL configureren")

    * Selecteer **Ja**als u **SSO voor meerdere providers wilt inschakelen**.
  
    * Selecteer **Ja**om **het automatisch importeren van gebruikers van alle id-providers in de gebruikers tabel in te scha kelen**.

    * Selecteer **Ja**om **logboek registratie voor fout opsporing in te scha kelen voor de multi provider-SSO-integratie**.

    * Voer voor **het veld in de tabel gebruiker de gebruikers** **naam**in.
  
    * Selecteer **Opslaan**.

6. U kunt ServiceNow automatisch of hand matig configureren. Voer de volgende stappen uit om ServiceNow automatisch te configureren:

    1. Ga terug naar de **ServiceNow** -pagina voor eenmalige aanmelding in de Azure Portal.

    1. Er is een service voor het configureren van één klik voor ServiceNow. Als u deze service wilt inschakelen, gaat u naar de **ServiceNow-configuratie** sectie en selecteert u **ServiceNow configureren** om het venster **aanmelden configureren** te openen.

        ![Scherm opname van ServiceNow instellen, waarbij stapsgewijze instructies voor weer gave zijn gemarkeerd](./media/servicenow-tutorial/tutorial_servicenow_configure.png)

    1. Voer in het formulier **aanmelden configureren** de naam van uw ServiceNow-exemplaar, de gebruikers naam van de beheerder en het beheerders wachtwoord in. Selecteer **nu configureren**. Voor het werken met de gebruikers naam van de beheerder moet de rol **security_admin** zijn toegewezen in ServiceNow. Als u ServiceNow hand matig wilt configureren voor het gebruik van Azure AD als een SAML-ID-provider, selecteert u **eenmalige aanmelding hand matig configureren**. Kopieer de **URL voor het afmelden, de SAML-Entiteits-ID en de webservice voor de SSO-service voor eenmalige** aanmelding uit het gedeelte snelle verwijzing.

        ![Scherm opname van het formulier voor aanmelden configureren, met nu configureren gemarkeerd](./media/servicenow-tutorial/configure.png "App-URL configureren")

    1. Meld u aan bij uw ServiceNow-toepassing als beheerder.

       * In de automatische configuratie worden alle benodigde instellingen geconfigureerd aan de kant van de **ServiceNow** , maar het **X. 509-certificaat** is niet standaard ingeschakeld. U moet deze hand matig toewijzen aan uw ID-provider in ServiceNow. Volg deze stappen:

         1. Zoek in het linkerdeel venster de SSO-sectie van de **multi-provider** op in het zoekvak en selecteer **id-providers**.

            ![Scherm opname van de SSO-sectie van de multi-provider, met de id-providers gemarkeerd](./media/servicenow-tutorial/tutorial_servicenow_07.png "Eenmalige aanmelding configureren")

         1. Selecteer de automatisch gegenereerde id-provider.

            ![Scherm opname van id-providers, met automatisch gegenereerde id-provider gemarkeerd](./media/servicenow-tutorial/tutorial_servicenow_08.png "Eenmalige aanmelding configureren")

         1.  Voer in de sectie **Identity Provider** de volgende stappen uit:

             ![Scherm afbeelding van de sectie ID-provider](./media/servicenow-tutorial/automatic_config.png "Eenmalige aanmelding configureren")

               * Voer bij **naam**een naam in voor uw configuratie (bijvoorbeeld **Microsoft Azure federatieve eenmalige aanmelding**).

               * Verwijder de SingleLogoutRequest waarde van de ingevuld **ID-provider** uit het tekstvak.

               * Kopieer de waarde voor de **Start pagina van ServiceNow** en plak deze in de **aanmeldings-URL** in het gedeelte **ServiceNow Basic SAML-configuratie** van de Azure Portal.

                  > [!NOTE]
                  > De startpagina van de ServiceNow-instantie is een samenvoeging van uw **ServiceNow-tenant-URL** en **/navpage.do** (bijvoorbeeld:`https://fabrikam.service-now.com/navpage.do`).

              * Kopieer de **Entiteits-ID/naam** van de verlener en plak deze in de **id** in de **ServiceNow Basic SAML-configuratie** sectie van de Azure Portal.

              * Bevestig dat het **NameID** -beleid `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified` is ingesteld op waarde. 

         1. Schuif omlaag naar de sectie **X. 509-certificaat** en selecteer **bewerken**.

             ![Scherm afbeelding van de sectie X. 509-certificaat, met de markering gemarkeerd](./media/servicenow-tutorial/tutorial_servicenow_09.png "Eenmalige aanmelding configureren")

         1. Selecteer het certificaat en selecteer het pictogram met de pijl naar rechts om het certificaat toe te voegen

            ![Scherm afbeelding van verzameling, met pictogram voor het certificaat en de pijl naar rechts gemarkeerd](./media/servicenow-tutorial/tutorial_servicenow_11.png "Eenmalige aanmelding configureren")

          1. Selecteer **Opslaan**.

          1. Selecteer in de rechter bovenhoek van de pagina **verbinding testen**.

             ![Scherm afbeelding van de pagina, waarbij de test verbinding is gemarkeerd](./media/servicenow-tutorial/tutorial_activate2.png "Invoeg toepassing activeren")

          1. Voer uw referenties in als u daarom wordt gevraagd. U ziet de volgende pagina. De **SSO-Afmeldings testresultaten** fout wordt verwacht. Negeer de fout en selecteer **activeren**.

             ![Scherm opname van testresultaten pagina](./media/servicenow-tutorial/servicenowactivate.png "Eenmalige aanmelding configureren")
  
6. Voer de volgende stappen uit om **ServiceNow** hand matig te configureren:

    1. Meld u aan bij uw ServiceNow-toepassing als beheerder.

    1. Selecteer in het linkerdeel venster **id-providers**.

        ![Scherm opname van SSO van meerdere providers, met id-providers gemarkeerd](./media/servicenow-tutorial/tutorial_servicenow_07.png "Eenmalige aanmelding configureren")

    1. Selecteer in het dialoog venster **id-providers** de optie **Nieuw**.

        ![Scherm afbeelding van id-providers in het dialoog venster met nieuwe gemarkeerd](./media/servicenow-tutorial/ic7694977.png "Eenmalige aanmelding configureren")

    1. Selecteer in het dialoog venster **id-providers** de optie **SAML**.

        ![Scherm afbeelding van id-providers in het dialoog venster met SAML gemarkeerd](./media/servicenow-tutorial/ic7694978.png "Eenmalige aanmelding configureren")

    1. Voer in de **meta gegevens van de identiteits provider importeren**de volgende stappen uit:

        ![Scherm opname van meta gegevens van de identiteits provider importeren, met een URL en een gemarkeerde import](./media/servicenow-tutorial/idp.png "Eenmalige aanmelding configureren")

        1. Voer de **URL voor de federatieve meta gegevens** van de app in die u hebt gekopieerd uit de Azure Portal.

        1. Selecteer **importeren**.

    1. Hiermee wordt de URL van de IdP-meta gegevens gelezen en worden alle veld gegevens ingevuld.

        ![Scherm afbeelding van ID-provider](./media/servicenow-tutorial/ic7694982.png "Eenmalige aanmelding configureren")

        * Voer bij **naam**een naam in voor uw configuratie (bijvoorbeeld **Microsoft Azure federatieve eenmalige aanmelding**).

        * Verwijder de SingleLogoutRequest waarde van de opgevulde **ID-provider** uit het tekstvak.

        * Kopieer de waarde voor de **Start pagina van ServiceNow** . Plak deze in de **aanmeldings-URL** in de **ServiceNow Basic SAML-configuratie** sectie van de Azure Portal.

            > [!NOTE]
            > De startpagina van de ServiceNow-instantie is een samenvoeging van uw **ServiceNow-tenant-URL** en **/navpage.do** (bijvoorbeeld:`https://fabrikam.service-now.com/navpage.do`).

        * Kopieer de waarde van de **entiteit-id/verlener** . Plak dit in de sectie **id** in **ServiceNow Basic SAML-configuratie** van de Azure Portal.

        * Bevestig dat het **NameID** -beleid `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified` is ingesteld op waarde.

        * Selecteer **Geavanceerd**. Voer in het **veld gebruiker** **e-mail adres** of **gebruikers naam**in, afhankelijk van welk veld wordt gebruikt om gebruikers in uw ServiceNow-implementatie uniek te identificeren.

            > [!NOTE]
            > U kunt Azure AD configureren voor het verzenden van de Azure AD-gebruikers-ID (user principal name) of het e-mail adres als de unieke id in het SAML-token. Hiertoe gaat u naar de sectie **ServiceNow** > **Attributes** > **eenmalige aanmelding** van de Azure Portal en het gewenste veld aan het kenmerk **nameidentifier** toe te wijzen. De waarde die is opgeslagen voor het geselecteerde kenmerk in azure AD (bijvoorbeeld user principal name) moet overeenkomen met de waarde die is opgeslagen in ServiceNow voor het ingevoerde veld (bijvoorbeeld gebruikers naam).

        * Selecteer **verbinding testen** in de rechter bovenhoek van de pagina.

        * Voer uw referenties in als u daarom wordt gevraagd. U ziet de volgende pagina. De **SSO-Afmeldings testresultaten** fout wordt verwacht. Negeer de fout en selecteer **activeren**.

          ![Scherm opname van testresultaten pagina](./media/servicenow-tutorial/servicenowactivate.png "Eenmalige aanmelding configureren")

### <a name="create-servicenow-test-user"></a>Een testgebruiker maken in ServiceNow

Het doel van deze sectie is het maken van een gebruiker met de naam B. Simon in ServiceNow. ServiceNow ondersteunt automatische gebruikers inrichting, dat standaard is ingeschakeld.

> [!NOTE]
> Als u hand matig een gebruiker moet maken, neemt u contact op met het [ondersteunings team van ServiceNow-clients](https://www.servicenow.com/support/contact-support.html).

### <a name="configure-servicenow-express-sso"></a>ServiceNow Express SSO configureren

1. Meld u aan bij uw ServiceNow Express-toepassing als beheerder.

2. Selecteer **eenmalige aanmelding**in het linkerdeel venster.

    ![Scherm opname van ServiceNow Express-toepassing, waarbij eenmalige aanmelding is gemarkeerd](./media/servicenow-tutorial/ic7694980ex.png "App-URL configureren")

3. Selecteer in het dialoog venster **eenmalige aanmelding** het configuratie pictogram in de rechter bovenhoek en stel de volgende eigenschappen in:

    ![Scherm afbeelding van het dialoog venster eenmalige aanmelding](./media/servicenow-tutorial/ic7694981ex.png "App-URL configureren")

    a. Schuif de schakelaar **Enable multiple provider SSO** naar rechts.

    b. Schuif de schakelaar **Enable debug logging for the multiple provider SSO integration** naar rechts.

    c. Voer in **het veld in de tabel gebruiker de gebruikers** **naam**in.

4. Selecteer in het dialoog venster **eenmalige aanmelding** de optie **Nieuw certificaat toevoegen**.

    ![Scherm afbeelding van het dialoog venster eenmalige aanmelding met nieuw certificaat toevoegen gemarkeerd](./media/servicenow-tutorial/ic7694973ex.png "Eenmalige aanmelding configureren")

5. Voer in het dialoog venster **X. 509-certificaten** de volgende stappen uit:

    ![Scherm afbeelding van het dialoog venster X. 509-certificaten](./media/servicenow-tutorial/ic7694975.png "Eenmalige aanmelding configureren")

    a. Voer bij **naam**een naam in voor uw configuratie (bijvoorbeeld: **TestSAML2.0**).

    b. Selecteer **Active**.

    c. Selecteer voor **indeling**de optie **PEM**.

    d. Selecteer bij **type**het **vertrouwens archief**van het certificaat.

    e. Open uw base64-gecodeerd certificaat dat is gedownload van Azure Portal in Klad blok. Kopieer de inhoud ervan naar het klem bord en plak deze in het tekstvak **pem certificate** .

    f. **Update** selecteren

6. Selecteer in het dialoog venster **eenmalige aanmelding** de optie **nieuwe IDP toevoegen**.

    ![Scherm afbeelding van het dialoog venster eenmalige aanmelding met nieuwe IDP gemarkeerd](./media/servicenow-tutorial/ic7694976ex.png "Eenmalige aanmelding configureren")

7. Voer in het dialoog venster **nieuwe ID-provider toevoegen** onder **ID-provider configureren**de volgende stappen uit:

    ![Scherm afbeelding van het dialoog venster nieuwe ID-provider toevoegen](./media/servicenow-tutorial/ic7694982ex.png "Eenmalige aanmelding configureren")

    a. Voer bij **naam**een naam in voor uw configuratie (bijvoorbeeld: **SAML 2.0**).

    b. Plak de waarde van de ID-provider die u hebt gekopieerd uit de Azure Portal voor de URL van de **identiteits provider**.

    c. Plak voor de AuthnRequest van de **identiteits provider**de waarde van de URL van de verificatie aanvraag die u hebt gekopieerd uit de Azure Portal.

    d. Plak de waarde van de afmeldings-URL die u hebt gekopieerd uit de Azure Portal voor de SingleLogoutRequest van de **identiteits provider**.

    e. Selecteer voor **ID-provider certificaat**het certificaat dat u in de vorige stap hebt gemaakt.

8. Selecteer **Geavanceerde instellingen**. Voer onder **aanvullende eigenschappen van de identiteits provider**de volgende stappen uit:

    ![Scherm opname van het dialoog venster nieuwe ID-provider toevoegen met geavanceerde instellingen gemarkeerd](./media/servicenow-tutorial/ic7694983ex.png "Eenmalige aanmelding configureren")

    a. Voer voor **protocol binding voor de SingleLogoutRequest van de IDP** **urn: Oasis: names: TC: SAML: 2.0: bindings: HTTP-redirect**in.

    b. Voor **NameID-beleid**voert u **urn: Oasis: names: TC: SAML: 1.1: NameID-indeling: niet opgegeven**in.

    c. Voer`http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password`voor de **methode AuthnContextClassRef**in.

    d. Als u **een AuthnContextClass wilt maken**, schakelt u deze in op uit (niet geselecteerd).

9. Voer onder **Additional Service Provider Properties** de volgende stappen uit:

    ![Scherm afbeelding van het dialoog venster nieuwe ID-provider toevoegen met diverse eigenschappen gemarkeerd](./media/servicenow-tutorial/ic7694984ex.png "Eenmalige aanmelding configureren")

    a. Voer de URL van de start pagina van uw ServiceNow-exemplaar in voor **ServiceNow Home page**.

    > [!NOTE]
    > De startpagina van de ServiceNow-instantie is een samenvoeging van uw **ServiceNow-tenant-URL** en **/navpage.do** (bijvoorbeeld: `https://fabrikam.service-now.com/navpage.do`).

    b. Voor **Entiteits-ID/verlener**voert u de URL van uw ServiceNow-Tenant in.

    c. Voer de URL van uw ServiceNow-Tenant in voor de **doel groep-URI**.

    d. Voer **60**in bij **klok scheefheid**.

    e. Voer voor het **veld gebruiker** **e-mail adres** of **gebruikers naam**in, afhankelijk van welk veld wordt gebruikt om gebruikers in uw ServiceNow-implementatie uniek te identificeren.

    > [!NOTE]
    > U kunt Azure AD configureren voor het verzenden van de Azure AD-gebruikers-ID (user principal name) of het e-mail adres als de unieke id in het SAML-token. Hiertoe gaat u naar de sectie **ServiceNow** > **Attributes** > **eenmalige aanmelding** van de Azure Portal en het gewenste veld aan het kenmerk **nameidentifier** toe te wijzen. De waarde die is opgeslagen voor het geselecteerde kenmerk in azure AD (bijvoorbeeld user principal name) moet overeenkomen met de waarde die is opgeslagen in ServiceNow voor het ingevoerde veld (bijvoorbeeld gebruikers naam).

    f. Selecteer **Opslaan**.

## <a name="test-sso"></a>SSO testen

Wanneer u de tegel ServiceNow in het toegangs venster selecteert, wordt u automatisch aangemeld bij de ServiceNow waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="test-sso-for-servicenow-classic-mobile"></a>SSO testen voor ServiceNow Classic (mobiel)

1. Open de toepassing **ServiceNow Classic (Mobile)** en voer de volgende stappen uit:

    a. Selecteer het plus teken in de rechter benedenhoek.

    ![Scherm afbeelding van klassieke ServiceNow-toepassing, met plus teken gemarkeerd](./media/servicenow-tutorial/test03.png)

    b. Voer de naam van uw ServiceNow-exemplaar in en selecteer **door gaan**.

    ![Scherm afbeelding van de pagina exemplaar toevoegen, met door gaan gemarkeerd](./media/servicenow-tutorial/test04.png)

    c. Voer de volgende stappen uit op de pagina **Aanmelden** :

    ![Scherm opname van de pagina aanmelden waarbij externe aanmelding gebruiken is gemarkeerd](./media/servicenow-tutorial/test01.png)

    *  Voer de **gebruikers naam**in B.simon@contoso.com, bijvoorbeeld.

    *  Selecteer **externe aanmelding gebruiken**. U wordt omgeleid naar de pagina van Azure AD om u aan te melden.
    
    *  Voer uw referenties in. Als er sprake is van een verificatie van derden of een andere beveiligings functie is ingeschakeld, moet de gebruiker op de juiste wijze reageren. De **Start pagina** van de toepassing wordt weer gegeven.

        ![Scherm opname van de start pagina van de toepassing](./media/servicenow-tutorial/test02.png)

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelf studies voor het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Gebruikers inrichten configureren](servicenow-provisioning-tutorial.md)

- [Probeer ServiceNow met Azure AD](https://aad.portal.azure.com)
