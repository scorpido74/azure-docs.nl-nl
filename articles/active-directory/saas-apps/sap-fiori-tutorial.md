---
title: 'Zelfstudie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met SAP Fiori | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en SAP Fiori.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 77ad13bf-e56b-4063-97d0-c82a19da9d56
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/05/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 50d1875ce2529222e8ff7472c48bf6d4dd878667
ms.sourcegitcommit: a4b5d31b113f520fcd43624dd57be677d10fc1c0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/06/2019
ms.locfileid: "70772877"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sap-fiori"></a>Zelfstudie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met SAP-Fiori

In deze zelf studie leert u hoe u SAP Fiori integreert met Azure Active Directory (Azure AD). Wanneer u SAP Fiori integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot SAP-Fiori.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij SAP Fiori met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* SAP Fiori-abonnement met eenmalige aanmelding (SSO) ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* SAP Fiori ondersteunt door **SP** GEÏNITIEERDe SSO

> [!NOTE]
> Voor SAP Fiori heeft iFrame-verificatie gestart, kunt u het beste de para meter **IsPassive** gebruiken in het SAML-AuthnRequest voor Silent-verificatie. Raadpleeg de informatie over [eenmalige aanmelding voor Azure AD SAML](https://docs.microsoft.com/azure/active-directory/develop/single-sign-on-saml-protocol) voor meer informatie over de para meter **IsPassive** .

## <a name="adding-sap-fiori-from-the-gallery"></a>SAP Fiori toevoegen vanuit de galerie

Als u de integratie van SAP Fiori wilt configureren in azure AD, moet u SAP Fiori toevoegen vanuit de galerie aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **SAP Fiori** in het zoekvak.
1. Selecteer **SAP Fiori** uit het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sap-fiori"></a>Eenmalige aanmelding van Azure AD voor SAP Fiori configureren en testen

Azure AD SSO met SAP Fiori configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in SAP Fiori.

Als u Azure AD SSO wilt configureren en testen met SAP Fiori, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    1. **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    1. **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
1. **[SAP Fiori SSO configureren](#configure-sap-fiori-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    1. Een **[SAP Fiori-test gebruiker maken](#create-sap-fiori-test-user)** : als u een tegen hanger wilt hebben van B. Simon in SAP Fiori dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Open een nieuw webbrowser venster en meld u als beheerder aan bij uw SAP Fiori-bedrijfs site.

1. Zorg ervoor dat de **http-** en **https** -Services actief zijn en dat de relevante poorten zijn toegewezen aan de **SMICM**.

1. Meld u aan bij de SAP Business-client voor SAP System **T01**, waarbij eenmalige aanmelding is vereist. Activeer vervolgens het beheer van de HTTP-beveiligings sessie.

    1. Ga naar transactie code **SICF_SESSIONS**. Alle relevante profiel parameters met de huidige waarden worden weer gegeven. Ze zien eruit als in het volgende voor beeld:

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
        > Pas de para meters aan op basis van de vereisten van uw organisatie. De voor gaande para meters worden alleen als voor beeld gegeven.

    1. Indien nodig wijzigt u de para meters in het exemplaar (standaard profiel) van het SAP-systeem en start u het SAP-systeem opnieuw op.

    1. Dubbel klik op de relevante client om een HTTP-beveiligings sessie in te scha kelen.

        ![De huidige waarden van de pagina met relevante profiel parameters in SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-profileparameter.png)

    1. Activeer de volgende SICF-Services:

        ```
        /sap/public/bc/sec/saml2
        /sap/public/bc/sec/cdc_ext_service
        /sap/bc/webdynpro/sap/saml2
        /sap/bc/webdynpro/sap/sec_diag_tool (This is only to enable / disable trace)
        ```

1. Ga naar transactie code **SAML2** in Business client voor SAP System [**T01/122**]. De configuratie-UI wordt geopend in een nieuw browser venster. In dit voor beeld gebruiken we Business client voor SAP System 122.

    ![De aanmeldings pagina van de SAP Fiori-client](./media/sapfiori-tutorial/tutorial-sapnetweaver-sapbusinessclient.png)

1. Voer uw gebruikers naam en wacht woord in en selecteer **Aanmelden**.

    ![De SAML 2,0-configuratie van de ABAP System T01/122-pagina in SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-userpwd.png)

1. Vervang **T01122** door **http:\//T01122**in het vak **naam van provider** en selecteer vervolgens **Opslaan**.

    > [!NOTE]
    > De naam van de provider bevindt zich standaard \<in de\<indeling sid > client >. Azure AD verwacht de naam in de notatie \<protocol >://\<name >. U wordt aangeraden de provider naam als HTTPS\://\<-sid >\<client > te behouden, zodat u meerdere SAP Fiori ABAP-engines kunt configureren in azure AD.

    ![De bijgewerkte provider naam in de SAML 2,0-configuratie van de ABAP System T01/122-pagina in SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-providername.png)

1. Selecteer de**meta gegevens**van het **lokale provider tabblad** > .

1. Down load in het dialoog venster **SAML 2,0 meta data** het GEGENEREERDE XML-bestand met meta gegevens en sla het op uw computer op.

    ![De koppeling meta gegevens downloaden in het dialoog venster SAP SAML 2,0-meta gegevens](./media/sapfiori-tutorial/tutorial-sapnetweaver-generatesp.png)

1. Zoek in de [Azure Portal](https://portal.azure.com/)op de pagina **SAP Fiori** Application Integration de sectie **Manage** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **Standaard SAML-configuratie** de volgende stappen uit als u beschikt over een **bestand met metagegevens van de serviceprovider**:

    a. Klik op **Metagegevensbestand uploaden**.

    ![Metagegevensbestand uploaden](common/upload-metadata.png)

    b. Klik op het **mappictogram** om het metagegevensbestand te selecteren en klik op **Uploaden**.

    ![Metagegevensbestand kiezen](common/browse-upload-metadata.png)

    c. Wanneer het meta gegevensbestand is geüpload, worden de **id** -en **antwoord-URL** -waarden automatisch ingevuld in het **basis configuratie** venster voor SAML. Voer in het vak **AANMELDINGS URL** een URL in die het volgende patroon heeft: `https:\//\<your company instance of SAP Fiori\>`.

    > [!NOTE]
    > Een aantal klanten rapporteren fouten met betrekking tot onjuiste geconfigureerde **antwoord-URL** -waarden. Als u deze fout ziet, kunt u het volgende Power shell-script gebruiken om de juiste antwoord-URL voor uw exemplaar in te stellen:
    >
    > ```
    > Set-AzureADServicePrincipal -ObjectId $ServicePrincipalObjectId -ReplyUrls "<Your Correct Reply URL(s)>"
    > ``` 
    > 
    > U kunt de `ServicePrincipal` object-id zelf instellen voordat u het script uitvoert, of u kunt dit hier door geven.

1. De SAP Fiori-toepassing verwacht dat de SAML-bevestigingen een specifieke indeling hebben. Configureer de volgende claims voor deze toepassing. Als u deze kenmerk waarden wilt beheren, selecteert u in het deel venster **eenmalige aanmelding met SAML instellen** de optie **bewerken**.

    ![Het deel venster gebruikers kenmerken](common/edit-attribute.png)

1. Configureer in het deel venster **gebruikers kenmerken & claims** de SAML-token kenmerken, zoals wordt weer gegeven in de vorige afbeelding. Voer de volgende stappen uit:

    1. Selecteer **bewerken** om het deel venster **gebruikers claims beheren** te openen.

    1. Selecteer in de lijst **trans formatie** de optie **ExtractMailPrefix ()** .

    1. Selecteer in de lijst **para meter 1** de optie **User. userprinicipalname**.

    1. Selecteer **Opslaan**.

       ![Het deel venster gebruikers claims beheren](./media/sapfiori-tutorial/nameidattribute.png)

       ![De sectie trans formatie in het deel venster gebruikers claims beheren](./media/sapfiori-tutorial/nameidattribute1.png)
    
1. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , de **federatieve meta gegevens-XML** en selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

    ![De downloadkoppeling certificaat](common/metadataxml.png)

1. Op de sectie **SAP Fiori instellen** kopieert u de gewenste URL ('s) op basis van uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Maak een testgebruiker Azure AD

In deze sectie maakt u een test gebruiker in de Azure Portal met de naam B. Simon.

1. Selecteer in het linkerdeel venster van de Azure Portal **Azure Active Directory**, selecteer **gebruikers**en selecteer vervolgens **alle gebruikers**.
1. Selecteer **nieuwe gebruiker** aan de bovenkant van het scherm.
1. Voer de volgende stappen uit in de eigenschappen van de **gebruiker** :
   1. Voer in het veld **Naam** `B.Simon` in.  
   1. Voer in het veld **gebruikers naam** het username@companydomain.extensionin. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.
   1. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan SAP Fiori.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer **SAP Fiori**in de lijst toepassingen.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling 'Gebruikers en groepen'](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-sap-fiori-sso"></a>SAP Fiori SSO configureren

1. Meld u aan bij het SAP-systeem en ga naar transactie code **SAML2**. Er wordt een nieuw browser venster geopend met de configuratie pagina voor SAML.

1. Als u eind punten voor een vertrouwde id-provider (Azure AD) wilt configureren, selecteert u het tabblad **vertrouwde providers** .

    ![Het tabblad Vertrouwde providers in SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-samlconfig.png)

1. Selecteer **toevoegen**en selecteer vervolgens **META gegevensbestand uploaden** in het context menu.

    ![De opties voor het meta gegevensbestand toevoegen en uploaden in SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-uploadmetadata.png)

1. Upload het meta gegevensbestand dat u hebt gedownload in de Azure Portal. Selecteer **Volgende**.

    ![Het meta gegevensbestand selecteren dat in SAP moet worden geüpload](./media/sapfiori-tutorial/tutorial-sapnetweaver-metadatafile.png)

1. Voer op de volgende pagina in het vak **alias** de alias naam in. Bijvoorbeeld **aadsts**. Selecteer **Volgende**.

    ![Het vak alias in SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-aliasname.png)

1. Zorg ervoor dat de waarde in het vak **Digest** -algoritme **SHA-256**is. Selecteer **Volgende**.

    ![De waarde van het Digest-algoritme controleren in SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-identityprovider.png)

1. Selecteer **http post**onder **eenmalige aanmelding**en selecteer vervolgens **volgende**.

    ![Opties voor eenmalige aanmelding in SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-httpredirect.png)

1. Selecteer onder **eenmalige Afmeldings eindpunten** **HTTP-omleiding**en selecteer vervolgens **volgende**.

    ![Opties voor eenmalige afmeldings eindpunten in SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-httpredirect1.png)

1. Onder **artefact eindpunten**selecteert u **volgende** om door te gaan.

    ![Opties voor artefact eindpunten in SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-artifactendpoint.png)

1. Selecteer **volt ooien**onder **verificatie vereisten**.

    ![Opties voor verificatie vereisten en de optie volt ooien in SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-authentication.png)

1. Selecteer**identiteits Federatie** van **vertrouwde provider** > (onder aan de pagina). Selecteer **Bewerken**.

    ![De tabbladen vertrouwde provider en identiteits Federatie in SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-trustedprovider.png)

1. Selecteer **Toevoegen**.

    ![De optie toevoegen op het tabblad identiteits Federatie](./media/sapfiori-tutorial/tutorial-sapnetweaver-addidentityprovider.png)

1. In het dialoog venster **ondersteunde NameID-indelingen** selecteert u niet **opgegeven**. Selecteer **OK**.

    ![Het dialoog venster ondersteunde NameID-indelingen en opties in SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-nameid.png)

    De waarden voor de modus **gebruikers-ID-bron** en **gebruikers-id toewijzen** bepalen de koppeling tussen de SAP-gebruiker en de Azure AD-claim.  

    **Scenario 1**: SAP-gebruiker naar Azure AD-gebruikers toewijzing

    1. In SAP, onder **Details van de NameID-indeling niet opgegeven**, noteert u de details:

        ![De details van het dialoog venster voor de NameID-indeling niet opgegeven in SAP](./media/sapfiori-tutorial/nameiddetails.png)

    1. In het Azure Portal noteert u onder **gebruikers kenmerken & claims**de vereiste claims van Azure AD.

        ![Het dialoog venster gebruikers kenmerken & claims in de Azure Portal](./media/sapfiori-tutorial/claimsaad1.png)

    **Scenario 2**: Selecteer de SAP-gebruikers-ID op basis van het geconfigureerde e-mail adres in SU01. In dit geval moet de e-mail-ID worden geconfigureerd in SU01 voor elke gebruiker die SSO vereist.

    1.  In SAP, onder **Details van de NameID-indeling niet opgegeven**, noteert u de details:

        ![De details van het dialoog venster voor de NameID-indeling niet opgegeven in SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-nameiddetails1.png)

    1. In het Azure Portal noteert u onder **gebruikers kenmerken & claims**de vereiste claims van Azure AD.

       ![Het dialoog venster gebruikers kenmerken & claims in de Azure Portal](./media/sapfiori-tutorial/claimsaad2.png)

1. Selecteer **Opslaan**en selecteer vervolgens **inschakelen** om de ID-provider in te scha kelen.

    ![De opties opslaan en inschakelen in SAP](./media/sapfiori-tutorial/configuration1.png)

1. Selecteer **OK** wanneer u hierom wordt gevraagd.

    ![De optie OK in het dialoog venster SAML 2,0-configuratie in SAP](./media/sapfiori-tutorial/configuration2.png)

### <a name="create-sap-fiori-test-user"></a>SAP Fiori-test gebruiker maken

In deze sectie maakt u een gebruiker met de naam Julia Simon in SAP Fiori. Werk samen met uw interne SAP-team van experts of uw SAP-partner van uw organisatie om de gebruiker toe te voegen in het SAP Fiori-platform.

## <a name="test-sso"></a>SSO testen

1. Nadat de ID-provider Azure AD is geactiveerd in SAP Fiori, probeert u een van de volgende Url's te openen om eenmalige aanmelding te testen (u moet niet worden gevraagd naar een gebruikers naam en wacht woord):

    * https:\//sapurl\>/SAP/BC/BSP/SAP/it00/default.htm\<
    * https:\//sapurl\>/SAP/BC/BSP/SAP/it00/default.htm\<

    > [!NOTE]
    > Vervang *sapurl* door de naam van de werkelijke SAP-hostnaam.

1. Met de test-URL gaat u naar de volgende pagina van de test toepassing in SAP. Als de pagina wordt geopend, is de eenmalige aanmelding van Azure AD ingesteld.

    ![De pagina standaard test toepassing in SAP](./media/sapfiori-tutorial/testingsso.png)

1. Als u wordt gevraagd om een gebruikers naam en wacht woord, schakelt u traceren in om het probleem te diagnosticeren. Gebruik de volgende URL voor de tracering: https\/:\>/\<sapurl/SAP/BC/webdynpro/SAP/sec_diag_tool? SAP-client = 122 & SAP-language = en #.

## <a name="additional-resources"></a>Aanvullende resources

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [SAP-Fiori proberen met Azure AD](https://aad.portal.azure.com/)
