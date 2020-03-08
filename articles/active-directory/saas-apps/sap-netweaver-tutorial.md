---
title: 'Zelf studie: zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met SAP NetWeaver | Microsoft Docs'
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
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/07/2020
ms.locfileid: "78897736"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sap-netweaver"></a>Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met SAP net-Weaver

In deze zelf studie leert u hoe u SAP NetWeaver integreert met Azure Active Directory (Azure AD). Wanneer u SAP NetWeaver integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot SAP net-Weaver.
* Stel in dat uw gebruikers automatisch worden aangemeld bij SAP NetWeaver met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* SAP NetWeaver single sign-on (SSO) ingeschakeld abonnement.
* Voor SAP NetWeaver V7.20 is ten minste vereist

## <a name="scenario-description"></a>Scenariobeschrijving

SAP NetWeaver ondersteunt zowel **SAML** (**SP initiated SSO**) als **OAuth**. In deze zelf studie configureert en test u Azure AD SSO in een test omgeving. 

> [!NOTE]
> Configureer de toepassing in SAML of in OAuth conform uw organisatie vereiste. 

## <a name="adding-sap-netweaver-from-the-gallery"></a>SAP NetWeaver toevoegen vanuit de galerie

Voor het configureren van de integratie van SAP NetWeaver met Azure AD moet u SAP NetWeaver uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **SAP NetWeaver** in het zoekvak.
1. Selecteer **SAP Netweave** uit het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sap-netweaver"></a>Eenmalige aanmelding voor Azure AD configureren en testen voor SAP net-Weaver

Azure AD SSO configureren en testen met SAP NetWeaver met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in SAP net-Weaver.

Als u Azure AD SSO wilt configureren en testen met SAP NetWeaver, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Maak een Azure AD-test gebruiker om de](#create-an-azure-ad-test-user)** eenmalige aanmelding van Azure ad te testen met B. Simon.
    1. **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe om B. Simon in te scha kelen voor het gebruik van eenmalige aanmelding voor Azure AD.
1. **[CONFIGUREER SAP NetWeaver met behulp van SAML](#configure-sap-netweaver-using-saml)** om de SSO-instellingen aan de kant van de toepassing te configureren.
    1. U kunt een **[SAP NetWeaver-gebruiker maken](#create-sap-netweaver-test-user)** voor een soort B. Simon in SAP NetWeaver die is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[Test SSO](#test-sso)** om te controleren of de configuratie werkt.
1. **[SAP Netweave voor OAuth configureren](#configure-sap-netweaver-for-oauth)** om de OAuth-instellingen aan de kant van de toepassing te configureren.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO configureren

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voor het configureren van Azure AD-eenmalige aanmelding met SAP NetWeaver moet u de volgende stappen uitvoeren:

1. Open een nieuw webbrowser venster en meld u als beheerder aan bij uw SAP NetWeaver-bedrijfs site

1. Zorg ervoor dat **http**- en **https**-services actief zijn en de juiste poorten zijn toegewezen in T-code **SMICM**.

1. Meld u aan bij de Business-client van het SAP-systeem (T01), waarbij SSO is vereist en schakel het beheer van HTTP-beveiligings sessies in.

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

    b. Wijzig indien nodig de para meters in het exemplaar/standaard Profiel van het SAP-systeem en start SAP-systeem opnieuw op.

    c. Dubbel klik op de relevante client om de HTTP-beveiligings sessie in te scha kelen.

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
    > Standaard is de naam van de provider als `<sid><client>`-indeling, maar de naam van Azure AD verwacht in de indeling van `<protocol>://<name>`. u wordt aangeraden de naam van de provider in te houden als `https://<sid><client>` zodat meerdere SAP NetWeaver ABAP-engines kunnen worden geconfigureerd in azure AD.

    ![De link om het certificaat te downloaden](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_providername.png)

1. **Metagegevens van de serviceprovider genereren**: als de instellingen **Local Provider** en **Trusted Providers** in de SAML 2.0-gebruikersinterface zijn geconfigureerd, bestaat de volgende stap uit het genereren van het bestand met metagegevens van de serviceprovider (dat alle instellingen, contexten voor verificatie en overige configuraties in SAP bevat). Als dit bestand is gegenereerd, moet het in Azure AD worden geüpload.

    ![De link om het certificaat te downloaden](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_generatesp.png)

    a. Ga naar het tabblad **Local Provider**.

    b. Klik op **Metadata**.

    c. Sla het gegenereerde **XML-bestand met meta gegevens** op uw computer op en upload dit in de sectie **basis configuratie van SAML** om de **id** -en **antwoord-URL** -waarden in azure Portal automatisch in te vullen.

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in de [Azure Portal](https://portal.azure.com/)op de pagina **SAP NetWeaver** -toepassings integratie de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Als u de toepassing in de gestarte modus **IDP** wilt configureren, voert u de volgende stap uit in de sectie **basis configuratie van SAML** :

    a. Klik op **bestand met meta gegevens uploaden** om het **META gegevensbestand van de service provider**te uploaden, dat u eerder hebt verkregen.

    b. Klik op het **mappictogram** om het metagegevensbestand te selecteren en klik op **Uploaden**.

    c. Nadat het bestand met metagegevens is geüpload, worden de waarden voor **Identifier** en **Reply URL** automatisch ingevuld in het tekstvak van de sectie **Standaard SAML-configuratie**, zoals hieronder weergegeven:

    d. In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://<your company instance of SAP NetWeaver>`

    > [!NOTE]
    > Enkele klanten hebben een fout gerapporteerd dat een onjuiste antwoord-URL voor hun instantie is geconfigureerd. Als u een dergelijke foutmelding krijgt, kunt u het volgende PowerShell-script als tijdelijke oplossing gebruiken om de juiste antwoord-URL voor uw instantie in te stellen:
    > ```
    > Set-AzureADServicePrincipal -ObjectId $ServicePrincipalObjectId -ReplyUrls "<Your Correct Reply URL(s)>"
    > ``` 
    > De ServicePrincipal-object-id moet eerst door uzelf worden ingesteld, maar u kunt deze ook hier doorgeven.

1. SAP NetWeaver-toepassing verwacht de SAML-beweringen in een specifieke indeling, waarvoor u aangepaste kenmerk toewijzingen moet toevoegen aan de configuratie van uw SAML-token kenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven. Klik op het pictogram **Bewerken** om het dialoogvenster gebruikerskenmerken te openen.

    ![image](common/edit-attribute.png)

1. In de sectie **Gebruikersclaims** in het dialoogvenster **Gebruikerskenmerken** configureert u het kenmerk van het SAML-token zoals wordt weergegeven in de bovenstaande afbeelding en voert u de volgende stappen uit:

    a. Klik op **Pictogram bewerken** om het dialoogvenster **Gebruikersclaims beheren** te openen.

    ![image](./media/sapnetweaver-tutorial/nameidattribute.png)

    ![image](./media/sapnetweaver-tutorial/nameidattribute1.png)

    b. Selecteer in de lijst **Transformatie** **ExtractMailPrefix()** .

    c. Selecteer in de lijst **para meter 1** de optie **User. userPrincipalName**.

    d. Klik op **Opslaan**.

1. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , de **federatieve meta gegevens-XML** en selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

   ![De link om het certificaat te downloaden](common/metadataxml.png)

1. Op de sectie **SAP NetWeaver instellen** kopieert u de gewenste URL ('s) op basis van uw vereiste.

   ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een test gebruiker in de Azure Portal met de naam B. Simon.

1. Selecteer in het linkerdeel venster van de Azure Portal **Azure Active Directory**, selecteer **gebruikers**en selecteer vervolgens **alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Voer de volgende stappen uit in de eigenschappen van de **gebruiker** :
    1. Voer in het veld **Naam**`B.Simon` in.  
    1. Voer in het veld **gebruikers naam** de username@companydomain.extensionin. Bijvoorbeeld `B.Simon@contoso.com`.
    1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.
    1. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u B. Simon in om de eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan SAP net-Weaver.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Typ en selecteer **SAP NetWeaver** in de lijst met toepassingen.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-sap-netweaver-using-saml"></a>SAP netweave configureren met SAML

1. Meld u aan bij het SAP-systeem en ga naar transactie code SAML2. Er wordt een nieuwe browservenster geopend met een SAML-configuratiescherm.

2. Ga naar het tabblad **Trusted Providers** om eindpunten voor vertrouwde identiteitsproviders (Azure AD) te configureren.

    ![Eenmalige aanmelding configureren](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_samlconfig.png)

3. Druk op **Add** en selecteer **Upload Metadata File** in het snelmenu.

    ![Eenmalige aanmelding configureren](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_uploadmetadata.png)

4. Upload het metagegevensbestand dat u eerder in de Azure-portal hebt gedownload.

    ![Eenmalige aanmelding configureren](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_metadatafile.png)

5. Typ de aliasnaam in het volgende scherm. Bijvoorbeeld aadsts en druk op **volgende** om door te gaan.

    ![Eenmalige aanmelding configureren](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_aliasname.png)

6. Zorg ervoor dat **Digest Algorithm** **SHA-256** is, vereis geen wijzigingen en druk op **Next**.

    ![Eenmalige aanmelding configureren](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_identityprovider.png)

7. Gebruik bij **Single Sign-On Endpoints** **HTTP POST** en klik op **Next** om door te gaan.

    ![Eenmalige aanmelding configureren](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_httpredirect.png)

8. Gebruik bij **Single Logout Endpoints** **HTTPRedirect** en klik op **Next** om door te gaan.

    ![Eenmalige aanmelding configureren](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_httpredirect1.png)

9. Druk bij **Artifact Endpoints** op **Next** om door te gaan.

    ![Eenmalige aanmelding configureren](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_artifactendpoint.png)

10. Klik bij **Authentication Requirements** op **Finish**.

    ![Eenmalige aanmelding configureren](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_authentication.png)

11. Ga naar het tabblad **Trusted Provider** > **Identity Federation** (vanaf de onderkant van het scherm). Klik op **Bewerken**.

    ![Eenmalige aanmelding configureren](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_trustedprovider.png)

12. Klik onder het tabblad **Identity Federation** op **Add** (onderste venster).

    ![Eenmalige aanmelding configureren](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_addidentityprovider.png)

13. Selecteer in het pop-upvenster niet **opgegeven** uit de **ondersteunde NameID-indelingen** en klik op OK.

    ![Eenmalige aanmelding configureren](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_nameid.png)

14. Houd er rekening mee dat de waarden voor de toewijzing van de **bron-** en **gebruikers-id-** koppeling tussen de SAP-gebruiker en de Azure AD-claim worden bepaald.  

    #### <a name="scenario-sap-user-to-azure-ad-user-mapping"></a>Scenario: SAP-gebruiker naar Azure AD-gebruikers toewijzing.

    a. Schermopname van details NameID van SAP.

    ![Eenmalige aanmelding configureren](./media/sapnetweaver-tutorial/nameiddetails.png)

    b. Schermopname met Vereiste claims van Azure AD.

    ![Eenmalige aanmelding configureren](./media/sapnetweaver-tutorial/claimsaad1.png)

    #### <a name="scenario-select-sap-user-id-based-on-configured-email-address-in-su01-in-this-case-email-id-should-be-configured-in-su01-for-each-user-who-requires-sso"></a>Scenario: Selecteer een SAP-gebruikers-ID op basis van het geconfigureerde e-mail adres in SU01. In dit geval moet de e-mail-ID worden geconfigureerd in su01 voor elke gebruiker die SSO nodig heeft.

    a.  Schermopname van details NameID van SAP.

    ![Eenmalige aanmelding configureren](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_nameiddetails1.png)

    b. Schermopname met Vereiste claims van Azure AD.

    ![Eenmalige aanmelding configureren](./media/sapnetweaver-tutorial/claimsaad2.png)

15. Klik op **Save** en vervolgens op **Enable** om de id-provider in te schakelen.

    ![Eenmalige aanmelding configureren](./media/sapnetweaver-tutorial/configuration1.png)

16. Klik op **OK** zodra daarom wordt gevraagd.

    ![Eenmalige aanmelding configureren](./media/sapnetweaver-tutorial/configuration2.png)

    ### <a name="create-sap-netweaver-test-user"></a>SAP NetWeaver-testgebruiker maken

    In deze sectie maakt u een gebruiker met de naam B. Simon in SAP net-Weaver. Werk samen met uw interne SAP-deskundige of met de SAP-partner van uw organisatie om de gebruikers aan het SAP NetWeaver-platform toe te voegen.

## <a name="test-sso"></a>SSO testen

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

## <a name="configure-sap-netweaver-for-oauth"></a>SAP netweave configureren voor OAuth

1. SAP gedocumenteerde proces is beschikbaar op de volgende locatie: [NetWeaver Gateway service inschakelen en OAuth 2,0 scope maken](https://wiki.scn.sap.com/wiki/display/Security/NetWeaver+Gateway+Service+Enabling+and+OAuth+2.0+Scope+Creation)

2. Ga naar SPRO en zoek naar **Services activeren en onderhouden**.

    ![Eenmalige aanmelding configureren](./media/sapnetweaver-tutorial/oauth01.png)

3. In dit voor beeld willen we verbinding maken met de OData-service: `DAAG_MNGGRP` met OAuth naar Azure AD SSO. Gebruik de naam van de technische service voor het zoeken naar de service `DAAG_MNGGRP` en Activeer indien deze nog niet actief is (zoek naar `green` status onder het tabblad ICF-knoop punten). Zorg ervoor dat de systeem alias (het verbonden back-end-systeem, waar de service daad werkelijk wordt uitgevoerd) juist is.

    ![Eenmalige aanmelding configureren](./media/sapnetweaver-tutorial/oauth02.png)

    * Klik vervolgens op knop **OAuth** op de bovenste knop balk en wijs `scope` toe (behoud de standaard naam als deze wordt aangeboden).

4. In ons voor beeld wordt het bereik `DAAG_MNGGRP_001`en wordt het gegenereerd op basis van de service naam door automatisch een nummer toe te voegen. Rapport `/IWFND/R_OAUTH_SCOPES` kan worden gebruikt om de naam van het bereik te wijzigen of hand matig te maken.

    ![Eenmalige aanmelding configureren](./media/sapnetweaver-tutorial/oauth03.png)

    > [!NOTE]
    > Bericht `soft state status is not supported`: kan worden genegeerd, omdat er geen problemen zijn. Zie [hier](https://help.sap.com/doc/saphelp_nw74/7.4.16/1e/c60c33be784846aad62716b4a1df39/content.htm?no_cache=true) voor meer informatie

### <a name="create-a-service-user-for-the-oauth-20-client"></a>Een service gebruiker maken voor de OAuth 2,0-client

1. OAuth2 maakt gebruik van een `service ID` voor het verkrijgen van het toegangs token voor de eind gebruiker. Belang rijke beperking van het OAuth-ontwerp: de `OAuth 2.0 Client ID` moet identiek zijn aan de `username` de OAuth 2,0-client wordt gebruikt voor aanmelding bij het aanvragen van een toegangs token. Daarom gaan we in ons voor beeld een OAuth 2,0-client met de naam CLIENT1 registreren, en moet er een vereiste zijn dat er een gebruiker met dezelfde naam (CLIENT1) in het SAP-systeem bestaat en dat de gebruiker wordt geconfigureerd voor gebruik door de toepassing waarnaar wordt verwezen. 

2. Wanneer u een OAuth-client registreert, gebruiken we de `SAML Bearer Grant type`.

    >[!NOTE]
    >Raadpleeg voor meer informatie OAuth 2,0-client registratie voor het toekennings [type voor](https://wiki.scn.sap.com/wiki/display/Security/OAuth+2.0+Client+Registration+for+the+SAML+Bearer+Grant+Type) de SAML Bearer.

3. tcod: SU01/Create User CLIENT1 als `System type` en geef een wacht woord op. Sla het op als dit nodig is om de referentie toe te voegen aan de API-programmeur, die deze moet branden met de gebruikers naam voor de aanroepende code. Er mag geen profiel of rol worden toegewezen.

### <a name="register-the-new-oauth-20-client-id-with-the-creation-wizard"></a>De nieuwe OAuth 2,0-client-ID registreren bij de wizard maken

1. Een nieuwe **SOAUTH2**van de **OAuth 2,0-client** registreren. In de trans actie wordt een overzicht weer gegeven van de OAuth 2,0-clients die al zijn geregistreerd. Kies **maken** om de wizard te starten voor de nieuwe OAuth-client met de naam CLIENT1in in dit voor beeld.

2. Ga naar T-code: **SOAUTH2** en geef de beschrijving op en klik vervolgens op **volgende**.

    ![Eenmalige aanmelding configureren](./media/sapnetweaver-tutorial/oauth04.png)

    ![Eenmalige aanmelding configureren](./media/sapnetweaver-tutorial/oauth05.png)

3. Selecteer het al toegevoegde **SAML2 IDP – Azure AD** in de vervolg keuzelijst en sla het op.

    ![Eenmalige aanmelding configureren](./media/sapnetweaver-tutorial/oauth06.png)

    ![Eenmalige aanmelding configureren](./media/sapnetweaver-tutorial/oauth07.png)

    ![Eenmalige aanmelding configureren](./media/sapnetweaver-tutorial/oauth08.png)

4. Klik onder Scope toewijzing op **toevoegen** om het eerder gemaakte bereik toe te voegen: `DAAG_MNGGRP_001`

    ![Eenmalige aanmelding configureren](./media/sapnetweaver-tutorial/oauth09.png)

    ![Eenmalige aanmelding configureren](./media/sapnetweaver-tutorial/oauth10.png)

5. Klik op **volt ooien**.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer SAP netweave met Azure AD](https://aad.portal.azure.com/)