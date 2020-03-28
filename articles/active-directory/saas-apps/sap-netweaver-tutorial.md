---
title: 'Zelfstudie: Zelfstudie: SSO-integratie (Azure Active Directory Single Sign-On) met SAP NetWeaver | Microsoft Documenten'
description: Informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en SAP NetWeaver.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 1b9e59e3-e7ae-4e74-b16c-8c1a7ccfdef3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 700f2ca4d46b3483531fa0784cb78699befb20ca
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78897736"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sap-netweaver"></a>Zelfstudie: Azure Active Directory Single sign-on (SSO) integratie met SAP NetWeaver

In deze zelfstudie leert u hoe u SAP NetWeaver integreert met Azure Active Directory (Azure AD). Wanneer u SAP NetWeaver integreert met Azure AD, u het als:

* Beheer in Azure AD die toegang heeft tot SAP NetWeaver.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij SAP NetWeaver met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* SAP NetWeaver single sign-on (SSO) ingeschakeld abonnement.
* Voor SAP NetWeaver V7.20 is ten minste vereist

## <a name="scenario-description"></a>Scenariobeschrijving

SAP NetWeaver ondersteunt zowel **SAML** (**SP geïnitieerdE SSO**) als **OAuth**. In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving. 

> [!NOTE]
> Configureer de toepassing in SAML of in OAuth volgens uw organisatorische vereisten. 

## <a name="adding-sap-netweaver-from-the-gallery"></a>SAP NetWeaver toevoegen vanuit de galerie

Voor het configureren van de integratie van SAP NetWeaver met Azure AD moet u SAP NetWeaver uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **SAP NetWeaver** in het zoekvak in de sectie **Toevoegen vanuit de galerie.**
1. Selecteer **SAP NetWeaver** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sap-netweaver"></a>Azure AD-aanmelding configureren en testen voor SAP NetWeaver

Azure AD SSO configureren en testen met SAP NetWeaver met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in SAP NetWeaver.

Als u Azure AD SSO wilt configureren en testen met SAP NetWeaver, voert u de volgende bouwstenen in:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Maak een Azure AD-testgebruiker](#create-an-azure-ad-test-user)** om Azure AD-enkele aanmelding met B.Simon te testen.
    1. **[Wijs de Azure AD-testgebruiker](#assign-the-azure-ad-test-user)** toe om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
1. **[Configureer SAP NetWeaver met SAML](#configure-sap-netweaver-using-saml)** om de SSO-instellingen aan toepassingszijde te configureren.
    1. **[Maak SAP NetWeaver-testgebruiker](#create-sap-netweaver-test-user)** om een tegenhanger van B.Simon in SAP NetWeaver te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Test SSO](#test-sso)** om te controleren of de configuratie werkt.
1. **[Configureer SAP NetWeaver voor OAuth](#configure-sap-netweaver-for-oauth)** om de OAuth-instellingen aan toepassingszijde te configureren.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voor het configureren van Azure AD-eenmalige aanmelding met SAP NetWeaver moet u de volgende stappen uitvoeren:

1. Een nieuw webbrowservenster openen en u aanmelden bij uw SAP NetWeaver-bedrijfssite als beheerder

1. Zorg ervoor dat **http**- en **https**-services actief zijn en de juiste poorten zijn toegewezen in T-code **SMICM**.

1. Meld u aan bij de zakelijke client van SAP System (T01), waar SSO vereist is en activeer HTTP Security session Management.

    a. Ga naar transactiecode **SICF_SESSIONS**. Hier worden alle relevante profielparameters met de huidige waarden weergegeven. Deze zien eruit als hieronder:
    ```
    login/create_sso2_ticket = 2
    login/accept_sso2_ticket = 1
    login/ticketcache_entries_max = 1000
    login/ticketcache_off = 0  login/ticket_only_by_https = 0 
    icf/set_HTTPonly_flag_on_cookies = 3
    icf/user_recheck = 0  http/security_session_timeout = 1800
    http/security_context_cache_size = 2500
    rdisp/plugin_auto_logout = 1800
    rdisp/autothtime = 60
    ```
    >[!NOTE]
    > Pas de bovenstaande parameters aan volgens de vereisten van uw organisatie. De bovenstaande parameters worden hier slechts als indicatie weergegeven.

    b. Pas indien nodig parameters aan, in het instantie/standaardprofiel van het SAP-systeem en start het SAP-systeem opnieuw op.

    c. Dubbelklik op relevante client om http-beveiligingssessie in te schakelen.

    ![De link om het certificaat te downloaden](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_profileparameter.png)

    d. Activeer de onderstaande SICF-services:
    ```
    /sap/public/bc/sec/saml2
    /sap/public/bc/sec/cdc_ext_service
    /sap/bc/webdynpro/sap/saml2
    /sap/bc/webdynpro/sap/sec_diag_tool (This is only to enable / disable trace)
    ```
1. Ga naar transactiecode **SAML2** in de bedrijfsclient van het SAP-systeem [T01/122]. In een browser wordt een gebruikersinterface geopend. In dit voorbeeld wordt ervanuit gegaan dat 122 een SAP-bedrijfsclient is.

    ![De link om het certificaat te downloaden](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_sapbusinessclient.png)

1. Voer uw gebruikersnaam en wachtwoord in de gebruikersinterface in en klik op **Edit**.

    ![De link om het certificaat te downloaden](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_userpwd.png)

1. Vervang **Provider Name** T01122 door `http://T01122` en klik op **Save**.

    > [!NOTE]
    > Standaard worden de `<sid><client>` naam van de provider als `<protocol>://<name>`indeling, maar Azure AD `https://<sid><client>` verwacht dat naam in de indeling van , aanbeveelt om de naam van de provider te behouden om meerdere SAP NetWeaver ABAP-engines te laten configureren in Azure AD.

    ![De link om het certificaat te downloaden](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_providername.png)

1. **Metagegevens van de serviceprovider genereren**: als de instellingen **Local Provider** en **Trusted Providers** in de SAML 2.0-gebruikersinterface zijn geconfigureerd, bestaat de volgende stap uit het genereren van het bestand met metagegevens van de serviceprovider (dat alle instellingen, contexten voor verificatie en overige configuraties in SAP bevat). Als dit bestand is gegenereerd, moet het in Azure AD worden geüpload.

    ![De link om het certificaat te downloaden](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_generatesp.png)

    a. Ga naar het tabblad **Local Provider**.

    b. Klik op **Metadata**.

    c. Sla het **XML-bestand met gegenereerde metagegevens** op uw computer op en upload het in de sectie **BasisSAML-configuratie** om de **URL-waarden-id** en **antwoord automatisch** in te vullen in azure-portal.

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **SAP NetWeaver-toepassingsintegratie** de sectie **Beheren** en selecteer **Enkele aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **BasisSAML-configuratie** de volgende stap uit als u de toepassing in de **idp-modus** wilt configureren:

    a. Klik **op Metagegevensbestand uploaden** om het **metagegevensbestand van**de serviceprovider te uploaden , dat u eerder hebt verkregen.

    b. Klik op het **mappictogram** om het metagegevensbestand te selecteren en klik op **Uploaden**.

    c. Nadat het bestand met metagegevens is geüpload, worden de waarden voor **Identifier** en **Reply URL** automatisch ingevuld in het tekstvak van de sectie **Standaard SAML-configuratie**, zoals hieronder weergegeven:

    d. Typ in het tekstvak **AANmeldings-URL** een URL met het volgende patroon:`https://<your company instance of SAP NetWeaver>`

    > [!NOTE]
    > Enkele klanten hebben een fout gerapporteerd dat een onjuiste antwoord-URL voor hun instantie is geconfigureerd. Als u een dergelijke foutmelding krijgt, kunt u het volgende PowerShell-script als tijdelijke oplossing gebruiken om de juiste antwoord-URL voor uw instantie in te stellen:
    > ```
    > Set-AzureADServicePrincipal -ObjectId $ServicePrincipalObjectId -ReplyUrls "<Your Correct Reply URL(s)>"
    > ``` 
    > De ServicePrincipal-object-id moet eerst door uzelf worden ingesteld, maar u kunt deze ook hier doorgeven.

1. SAP NetWeaver-toepassing verwacht de SAML-beweringen in een specifieke indeling, waarvoor u aangepaste kenmerktoewijzingen moet toevoegen aan uw SAML-tokenkenmerkenconfiguratie. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven. Klik op het pictogram **Bewerken** om het dialoogvenster gebruikerskenmerken te openen.

    ![installatiekopie](common/edit-attribute.png)

1. In de sectie **Gebruikersclaims** in het dialoogvenster **Gebruikerskenmerken** configureert u het kenmerk van het SAML-token zoals wordt weergegeven in de bovenstaande afbeelding en voert u de volgende stappen uit:

    a. Klik op **pictogram bewerken** om het dialoogvenster **Gebruikersclaims beheren** te openen.

    ![installatiekopie](./media/sapnetweaver-tutorial/nameidattribute.png)

    ![installatiekopie](./media/sapnetweaver-tutorial/nameidattribute1.png)

    b. Selecteer in de lijst **Transformatie****ExtractMailPrefix()**.

    c. Selecteer **user.userprincipalname**in de lijst **Parameter 1** .

    d. Klik op **Opslaan**.

1. Zoek op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** de optie **Federation Metadata XML** en selecteer **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

   ![De link om het certificaat te downloaden](common/metadataxml.png)

1. Kopieer in de sectie **SAP NetWeaver instellen** de juiste URL(s) op basis van uw vereiste.

   ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een testgebruiker in de Azure-portal genaamd B.Simon.

1. Selecteer in het linkerdeelvenster in de Azure-portal **Azure Active Directory,** selecteer **Gebruikers**en selecteer **Alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Voer **in** de eigenschappen gebruiker de volgende stappen uit:
    1. Voer in het veld **Naam**`B.Simon` in.  
    1. Voer **in** het veld username@companydomain.extensionGebruikersnaam de . Bijvoorbeeld `B.Simon@contoso.com`.
    1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord.**
    1. Klik **op Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie u B.Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot SAP NetWeaver.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Typ en selecteer **SAP NetWeaver** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

## <a name="configure-sap-netweaver-using-saml"></a>SAP NetWeaver configureren met SAML

1. Meld u aan bij het SAP-systeem en ga naar transactiecode SAML2. Er wordt een nieuwe browservenster geopend met een SAML-configuratiescherm.

2. Ga naar het tabblad **Trusted Providers** om eindpunten voor vertrouwde identiteitsproviders (Azure AD) te configureren.

    ![Eenmalige aanmelding configureren](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_samlconfig.png)

3. Druk op **Add** en selecteer **Upload Metadata File** in het snelmenu.

    ![Eenmalige aanmelding configureren](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_uploadmetadata.png)

4. Upload het metagegevensbestand dat u eerder in de Azure-portal hebt gedownload.

    ![Eenmalige aanmelding configureren](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_metadatafile.png)

5. Typ de aliasnaam in het volgende scherm. Bijvoorbeeld aadsts en druk op **Volgende** om door te gaan.

    ![Eenmalige aanmelding configureren](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_aliasname.png)

6. Zorg ervoor dat **Digest Algorithm****SHA-256** is, vereis geen wijzigingen en druk op **Next**.

    ![Eenmalige aanmelding configureren](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_identityprovider.png)

7. Gebruik bij **Single Sign-On Endpoints****HTTP POST** en klik op **Next** om door te gaan.

    ![Eenmalige aanmelding configureren](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_httpredirect.png)

8. Gebruik bij **Single Logout Endpoints****HTTPRedirect** en klik op **Next** om door te gaan.

    ![Eenmalige aanmelding configureren](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_httpredirect1.png)

9. Druk bij **Artifact Endpoints** op **Next** om door te gaan.

    ![Eenmalige aanmelding configureren](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_artifactendpoint.png)

10. Klik bij **Authentication Requirements** op **Finish**.

    ![Eenmalige aanmelding configureren](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_authentication.png)

11. Ga naar de Trusted Provider Identity Federation op het tabblad **Trusted Provider** > **(vanaf** het scherm). Klik op **Bewerken**.

    ![Eenmalige aanmelding configureren](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_trustedprovider.png)

12. Klik onder het tabblad **Identity Federation** op **Add** (onderste venster).

    ![Eenmalige aanmelding configureren](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_addidentityprovider.png)

13. Selecteer in het pop-upvenster **Niet-gespecificeerd** in de **ondersteunde naam-id-indelingen** en klik op OK.

    ![Eenmalige aanmelding configureren](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_nameid.png)

14. Houd er rekening mee dat waarden voor de toewijzingsmodus voor **gebruikers-id's** en **gebruikers-id's** de koppeling tussen SAP-gebruiker en Azure AD-claim bepalen.  

    #### <a name="scenario-sap-user-to-azure-ad-user-mapping"></a>Scenario: SAP-gebruiker naar Azure AD-gebruikerstoewijzing.

    a. Schermopname van details NameID van SAP.

    ![Eenmalige aanmelding configureren](./media/sapnetweaver-tutorial/nameiddetails.png)

    b. Schermopname met Vereiste claims van Azure AD.

    ![Eenmalige aanmelding configureren](./media/sapnetweaver-tutorial/claimsaad1.png)

    #### <a name="scenario-select-sap-user-id-based-on-configured-email-address-in-su01-in-this-case-email-id-should-be-configured-in-su01-for-each-user-who-requires-sso"></a>Scenario: Selecteer SAP-gebruikers-id op basis van geconfigureerd e-mailadres in SU01. In dit geval moet e-id worden geconfigureerd in su01 voor elke gebruiker die SSO nodig heeft.

    a.  Schermopname van details NameID van SAP.

    ![Eenmalige aanmelding configureren](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_nameiddetails1.png)

    b. Schermopname met Vereiste claims van Azure AD.

    ![Eenmalige aanmelding configureren](./media/sapnetweaver-tutorial/claimsaad2.png)

15. Klik op **Save** en vervolgens op **Enable** om de id-provider in te schakelen.

    ![Eenmalige aanmelding configureren](./media/sapnetweaver-tutorial/configuration1.png)

16. Klik op **OK** zodra daarom wordt gevraagd.

    ![Eenmalige aanmelding configureren](./media/sapnetweaver-tutorial/configuration2.png)

    ### <a name="create-sap-netweaver-test-user"></a>SAP NetWeaver-testgebruiker maken

    In deze sectie maakt u een gebruiker genaamd B.simon in SAP NetWeaver. Werk samen met uw interne SAP-deskundige of met de SAP-partner van uw organisatie om de gebruikers aan het SAP NetWeaver-platform toe te voegen.

## <a name="test-sso"></a>Test SSO

1. Als de id-provider Azure AD is geactiveerd, opent u de onderstaande URL om eenmalige aanmelding te controleren (u wordt niet om gebruikersnaam en wachtwoord gevraagd)

    `https://<sapurl>/sap/bc/bsp/sap/it00/default.htm`

    (of) gebruik de onderstaande URL

    `https://<sapurl>/sap/bc/bsp/sap/it00/default.htm`

    > [!NOTE]
    > Vervang sapurl door de feitelijke SAP-hostnaam.

2. Via de bovenstaande URL moet u op het hieronder genoemd scherm terechtkomen. Als u de onderstaande pagina kunt bereiken, is eenmalige aanmelding voor Azure AD ingesteld.

    ![Eenmalige aanmelding configureren](./media/sapnetweaver-tutorial/testingsso.png)

3. Als u om een gebruikersnaam en wachtwoord wordt gevraagd, probeert u het probleem vast te stellen door de tracering in te schakelen met behulp van de onderstaande URL

    `https://<sapurl>/sap/bc/webdynpro/sap/sec_diag_tool?sap-client=122&sap-language=EN#`

## <a name="configure-sap-netweaver-for-oauth"></a>SAP NetWeaver configureren voor OAuth

1. SAP Documented-proces is beschikbaar op locatie: [NetWeaver Gateway Service Enabling en OAuth 2.0 Scope Creation](https://wiki.scn.sap.com/wiki/display/Security/NetWeaver+Gateway+Service+Enabling+and+OAuth+2.0+Scope+Creation)

2. Ga naar SPRO en vind **services activeren en onderhouden.**

    ![Eenmalige aanmelding configureren](./media/sapnetweaver-tutorial/oauth01.png)

3. In dit voorbeeld willen we de `DAAG_MNGGRP` OData-service verbinden: met OAuth met Azure AD SSO. Gebruik de technische servicenaam `DAAG_MNGGRP` zoeken naar de service en activeren `green` als nog niet actief, al (kijk voor de status onder ICF knooppunten tabblad). Controleer of de systeemalias (het aangesloten backendsysteem, waar de service daadwerkelijk wordt uitgevoerd) correct is.

    ![Eenmalige aanmelding configureren](./media/sapnetweaver-tutorial/oauth02.png)

    * Klik vervolgens op drukknop **OAuth** op `scope` de bovenste knopbalk en wijs (houd de standaardnaam zoals aangeboden) toe.

4. Voor ons voorbeeld `DAAG_MNGGRP_001`is het bereik, het wordt gegenereerd uit de servicenaam door automatisch een nummer toe te voegen. Rapport `/IWFND/R_OAUTH_SCOPES` kan worden gebruikt om de naam van het bereik te wijzigen of handmatig te maken.

    ![Eenmalige aanmelding configureren](./media/sapnetweaver-tutorial/oauth03.png)

    > [!NOTE]
    > Bericht `soft state status is not supported` – kan worden genegeerd, als geen probleem. Voor meer informatie, zie [hier](https://help.sap.com/doc/saphelp_nw74/7.4.16/1e/c60c33be784846aad62716b4a1df39/content.htm?no_cache=true)

### <a name="create-a-service-user-for-the-oauth-20-client"></a>Een servicegebruiker maken voor de OAuth 2.0-client

1. OAuth2 gebruikt `service ID` een om het toegangstoken voor de eindgebruiker namens de eindgebruiker te krijgen. Belangrijke beperking door OAuth-ontwerp: `OAuth 2.0 Client ID` de `username` moet identiek zijn aan de OAuth 2.0-client die wordt gebruikt voor aanmelding bij het aanvragen van een Access Token. Daarom gaan we bijvoorbeeld een OAuth 2.0-client registreren met naam CLIENT1, en als voorwaarde moet er een gebruiker met dezelfde naam (CLIENT1) in het SAP-systeem staan en die gebruiker die we configureren om door de doorverwezen toepassing te worden gebruikt. 

2. Bij het registreren van een `SAML Bearer Grant type`OAuth Client gebruiken we de .

    >[!NOTE]
    >Voor meer informatie, verwijzen OAuth 2.0 Client Registration voor de SAML Drager Grant Type [hier](https://wiki.scn.sap.com/wiki/display/Security/OAuth+2.0+Client+Registration+for+the+SAML+Bearer+Grant+Type)

3. tcod: SU01 / maak `System type` gebruiker CLIENT1 als en wachtwoord toewijzen, opslaan als nodig is om de referentie te verstrekken aan de API-programmeur, die moet branden met de gebruikersnaam aan de belcode. Er mag geen profiel of rol worden toegewezen.

### <a name="register-the-new-oauth-20-client-id-with-the-creation-wizard"></a>De nieuwe OAuth 2.0-client-id registreren met de wizard Maken

1. Het registreren van een nieuwe **OAuth 2.0 client** start transactie **SOAUTH2**. De transactie geeft een overzicht weer over de OAuth 2.0-clients die al waren geregistreerd. Kies **Maken** om de wizard te starten voor de nieuwe OAuth-client die is genoemd als CLIENT1 in dit voorbeeld.

2. Ga naar T-Code: **SOAUTH2** en Geef de beschrijving op klik **vervolgens**.

    ![Eenmalige aanmelding configureren](./media/sapnetweaver-tutorial/oauth04.png)

    ![Eenmalige aanmelding configureren](./media/sapnetweaver-tutorial/oauth05.png)

3. Selecteer de reeds toegevoegde **SAML2 IdP – Azure AD** in de vervolgkeuzelijst en sla op.

    ![Eenmalige aanmelding configureren](./media/sapnetweaver-tutorial/oauth06.png)

    ![Eenmalige aanmelding configureren](./media/sapnetweaver-tutorial/oauth07.png)

    ![Eenmalige aanmelding configureren](./media/sapnetweaver-tutorial/oauth08.png)

4. Klik op **Toevoegen** onder bereiktoewijzing om het eerder gemaakte bereik toe te voegen:`DAAG_MNGGRP_001`

    ![Eenmalige aanmelding configureren](./media/sapnetweaver-tutorial/oauth09.png)

    ![Eenmalige aanmelding configureren](./media/sapnetweaver-tutorial/oauth10.png)

5. Klik **op Voltooien**.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Sap NetWeaver uitproberen met Azure AD](https://aad.portal.azure.com/)