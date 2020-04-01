---
title: 'Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met SAP Fiori | Microsoft Documenten'
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
ms.openlocfilehash: 917ba9274276fec5d01a40bdf7219e8d4fee1395
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78897764"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sap-fiori"></a>Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met SAP Fiori

In deze zelfstudie leert u hoe u SAP Fiori integreert met Azure Active Directory (Azure AD). Wanneer u SAP Fiori integreert met Azure AD, u het:

* Beheer in Azure AD die toegang heeft tot SAP Fiori.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij SAP Fiori met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* SAP Fiori single sign-on (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

* SAP Fiori ondersteunt **door SP** geïnitieerde SSO

> [!NOTE]
> Voor SAP Fiori geïnitieerde iFrame-verificatie raden we u aan de parameter **IsPassive** in de SAML AuthnRequest te gebruiken voor stille verificatie. Voor meer informatie over de parameter **IsPassive** verwijzen wij u naar [Azure AD SAML single sign-on](https://docs.microsoft.com/azure/active-directory/develop/single-sign-on-saml-protocol) information

## <a name="adding-sap-fiori-from-the-gallery"></a>SAP Fiori toevoegen uit de galerie

Als u de integratie van SAP Fiori in Azure AD wilt configureren, moet u SAP Fiori uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **SAP Fiori** in het zoekvak in de sectie Toevoegen in **de galerie.**
1. Selecteer **SAP Fiori** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sap-fiori"></a>Azure AD-aanmelding voor SAP Fiori configureren en testen

Azure AD SSO configureren en testen met SAP Fiori met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in SAP Fiori.

Als u Azure AD SSO wilt configureren en testen met SAP Fiori, voert u de volgende bouwstenen in:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** - om Azure AD-enkele aanmelding te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** - om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
1. **[Sap Fiori SSO configureren](#configure-sap-fiori-sso)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
    1. **[Sap Fiori-testgebruiker maken](#create-sap-fiori-test-user)** - om een tegenhanger van B.Simon in SAP Fiori te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Open een nieuw browservenster en meld u aan bij uw SAP Fiori-bedrijfssite als beheerder.

1. Zorg ervoor dat **http-** en **https-services** actief zijn en dat de relevante poorten zijn toegewezen aan transactiecode **SMICM**.

1. Meld u aan bij SAP Business Client voor SAP-systeem **T01**, waar eenmalig aanmelden vereist is. Activeer vervolgens HTTP Security Session Management.

    1. Ga naar transactiecode **SICF_SESSIONS**. Alle relevante profielparameters met huidige waarden worden weergegeven. Ze zien eruit als het volgende voorbeeld:

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
        > Pas de parameters aan op basis van uw organisatievereisten. De voorgaande parameters worden alleen als voorbeeld gegeven.

    1. Pas indien nodig parameters aan in het instantieprofiel (standaard) van het SAP-systeem en start het SAP-systeem opnieuw op.

    1. Dubbelklik op de relevante client om een HTTP-beveiligingssessie in te schakelen.

        ![De pagina Huidige waarden van relevante profielparameters in SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-profileparameter.png)

    1. Activeer de volgende SICF-services:

        ```
        /sap/public/bc/sec/saml2
        /sap/public/bc/sec/cdc_ext_service
        /sap/bc/webdynpro/sap/saml2
        /sap/bc/webdynpro/sap/sec_diag_tool (This is only to enable / disable trace)
        ```

1. Ga naar transactiecode **SAML2** in Business Client voor SAP-systeem [**T01/122**]. De configuratie-gebruikersinterface wordt geopend in een nieuw browservenster. In dit voorbeeld gebruiken we Business Client voor SAP-systeem 122.

    ![De aanmeldingspagina van sap Fiori Business Client](./media/sapfiori-tutorial/tutorial-sapnetweaver-sapbusinessclient.png)

1. Voer uw gebruikersnaam en wachtwoord in en selecteer **Log in**.

    ![De SAML 2.0-configuratie van ABAP-systeem T01/122-pagina in SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-userpwd.png)

1. Vervang **T01122** in het vak **Providernaam** door **http:\//T01122**en selecteer **Opslaan**.

    > [!NOTE]
    > Standaard staat de naam van \<de \<provider in de indeling sid>client>. Azure AD verwacht dat \<de naam\<in het opmaakprotocol>:// naam>. We raden u aan de\://\<naam \<van de provider te behouden als https sid>client> zodat u meerdere SAP Fiori ABAP-engines in Azure AD configureren.

    ![De bijgewerkte providernaam in de SAML 2.0-configuratie van ABAP-systeem T01/122-pagina in SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-providername.png)

1. Selecteer **Metagegevens van het tabblad** > **Metadata**Lokale provider .

1. Download in het dialoogvenster **SAML 2.0-metagegevens** het XML-bestand met gegenereerde metagegevens en sla het op uw computer op.

    ![De koppeling Metagegevens downloaden in het dialoogvenster METAGEGEVENS van SAP SAML 2.0](./media/sapfiori-tutorial/tutorial-sapnetweaver-generatesp.png)

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **SAP Fiori-toepassingsintegratie** de sectie **Beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **Standaard SAML-configuratie** de volgende stappen uit als u beschikt over een **bestand met metagegevens van de serviceprovider**:

    a. Klik op **Metagegevensbestand uploaden**.

    ![Metagegevensbestand uploaden](common/upload-metadata.png)

    b. Klik op het **mappictogram** om het metagegevensbestand te selecteren en klik op **Uploaden**.

    ![Metagegevensbestand kiezen](common/browse-upload-metadata.png)

    c. Wanneer het metagegevensbestand is geüpload, worden de **URL-waarden Id** en **Antwoord** automatisch ingevuld in het deelvenster **BasisSAML-configuratie.** Voer in het vak **Aanmelding op URL** een `https:\//\<your company instance of SAP Fiori\>`URL in met het volgende patroon: .

    > [!NOTE]
    > Een paar klanten melden fouten met betrekking tot onjuist geconfigureerde **URL-waarden voor antwoorden.** Als u deze fout ziet, u het volgende PowerShell-script gebruiken om de juiste url voor antwoord voor uw instantie in te stellen:
    >
    > ```
    > Set-AzureADServicePrincipal -ObjectId $ServicePrincipalObjectId -ReplyUrls "<Your Correct Reply URL(s)>"
    > ``` 
    > 
    > U `ServicePrincipal` de object-ID zelf instellen voordat u het script uitvoert, of u het hier doorgeven.

1. De SAP Fiori applicatie verwacht dat de SAML beweringen in een specifiek formaat zijn. Configureer de volgende claims voor deze toepassing. Als u deze kenmerkwaarden wilt beheren, selecteert u In het deelvenster **Eén aanmelding instellen met SAML** de optie **Bewerken**.

    ![Het deelvenster Kenmerken van gebruiker](common/edit-attribute.png)

1. Configureer in het deelvenster **Gebruikerskenmerken & Claims** de SAML-tokenkenmerken zoals weergegeven in de vorige afbeelding. Voer de volgende stappen uit:

    1. Selecteer **Bewerken** om het deelvenster **Gebruikersclaims beheren** te openen.

    1. Selecteer **In** de lijst Transformatie de optie **ExtractMailPrefix()**.

    1. Selecteer **user.userprincipalname**in de lijst **Parameter 1** .

    1. Selecteer **Opslaan**.

       ![Het deelvenster Gebruikersclaims beheren](./media/sapfiori-tutorial/nameidattribute.png)

       ![De sectie Transformatie in het deelvenster Gebruikersclaims beheren](./media/sapfiori-tutorial/nameidattribute1.png)
    
1. Zoek op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** de optie **Federation Metadata XML** en selecteer **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. Kopieer in de sectie **SAP Fiori instellen** de juiste URL(s) op basis van uw vereiste.

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

In deze sectie u B.Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot SAP Fiori.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer **SAP Fiori**in de lijst met toepassingen .
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

## <a name="configure-sap-fiori-sso"></a>SAP Fiori SSO configureren

1. Meld u aan bij het SAP-systeem en ga naar transactiecode **SAML2**. Er wordt een nieuw browservenster geopend met de SAML-configuratiepagina.

1. Als u eindpunten wilt configureren voor een vertrouwde identiteitsprovider (Azure AD), selecteert u het tabblad **Vertrouwde providers.**

    ![Het tabblad Vertrouwde providers in SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-samlconfig.png)

1. Selecteer **Toevoegen**en selecteer **Vervolgens Metagegevensbestand uploaden** in het contextmenu.

    ![De opties voor metagegevensbestand toevoegen en uploaden in SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-uploadmetadata.png)

1. Upload het metagegevensbestand dat u hebt gedownload in de Azure-portal. Selecteer **Volgende**.

    ![Selecteer het metagegevensbestand dat u wilt uploaden in SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-metadatafile.png)

1. Voer op de volgende pagina in het vak **Alias** de aliasnaam in. Bijvoorbeeld, **aadsts**. Selecteer **Volgende**.

    ![Het vak Alias in SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-aliasname.png)

1. Zorg ervoor dat de waarde in het vak **Digest-algoritme** **SHA-256**is. Selecteer **Volgende**.

    ![De waarde van het Digest-algoritme in SAP verifiëren](./media/sapfiori-tutorial/tutorial-sapnetweaver-identityprovider.png)

1. Selecteer **HTTP POST**onder **Afzonderlijke aanmeldingseindpunten**en selecteer **Volgende**.

    ![Opties voor afzonderlijke aanmeldingseindpunten in SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-httpredirect.png)

1. Selecteer **onder Afzonderlijke afmeldingseindpunten**de optie **HTTP Redirect**en selecteer **Volgende**.

    ![Opties voor afzonderlijke aanmeldingseindpunten in SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-httpredirect1.png)

1. Selecteer **Onder Artefact-eindpunten**de optie **Volgende** om door te gaan.

    ![Opties voor artefact-eindpunten in SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-artifactendpoint.png)

1. Selecteer Onder **Verificatievereisten**de optie **Voltooien**.

    ![Opties voor verificatievereisten en de optie Voltooien in SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-authentication.png)

1. Selecteer **Trusted Provider** > **Identity Federation** (onder aan de pagina). Selecteer **Bewerken**.

    ![De tabbladen Trusted Provider and Identity Federation in SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-trustedprovider.png)

1. Selecteer **Toevoegen**.

    ![De optie Toevoegen op het tabblad Identiteitsfederatie](./media/sapfiori-tutorial/tutorial-sapnetweaver-addidentityprovider.png)

1. Selecteer **Niet-opgegeven**in het dialoogvenster **Ondersteunde naam-id-indelingen** . Selecteer **OK**.

    ![Het dialoogvenster Ondersteunde naam-id-indelingen en -opties in SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-nameid.png)

    De waarden voor **de modus Bron van gebruikers-** en gebruikersnaam **bepalen** de koppeling tussen de SAP-gebruiker en de Azure AD-claim.  

    **Scenario 1**: SAP-gebruiker naar Azure AD-gebruikerstoewijzing

    1. Noteer in SAP onder **Details van nameID-indeling "Niet gespecificeerd"** de details:

        ![Het dialoogvenster Details van naam-id-indeling "Niet-gespecificeerd" in SAP](./media/sapfiori-tutorial/nameiddetails.png)

    1. Noteer in de Azure-portal onder **Gebruikerskenmerken & Claims**de vereiste claims van Azure AD.

        ![Het dialoogvenster Gebruikerskenmerken & claims in de Azure-portal](./media/sapfiori-tutorial/claimsaad1.png)

    **Scenario 2**: Selecteer de SAP-gebruikers-id op basis van het geconfigureerde e-mailadres in SU01. In dit geval moet de e-id worden geconfigureerd in SU01 voor elke gebruiker die SSO nodig heeft.

    1.  Noteer in SAP onder **Details van nameID-indeling "Niet gespecificeerd"** de details:

        ![Het dialoogvenster Details van naam-id-indeling "Niet-gespecificeerd" in SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-nameiddetails1.png)

    1. Noteer in de Azure-portal onder **Gebruikerskenmerken & Claims**de vereiste claims van Azure AD.

       ![Het dialoogvenster Gebruikerskenmerken & claims in de Azure-portal](./media/sapfiori-tutorial/claimsaad2.png)

1. Selecteer **Opslaan**en selecteer **Inschakelen** om de identiteitsprovider in te schakelen.

    ![De opties opslaan en inschakelen in SAP](./media/sapfiori-tutorial/configuration1.png)

1. Selecteer **OK** wanneer u daarom wordt gevraagd.

    ![De optie OK in het dialoogvenster SAML 2.0-configuratie in SAP](./media/sapfiori-tutorial/configuration2.png)

### <a name="create-sap-fiori-test-user"></a>Sap Fiori-testgebruiker maken

In deze sectie maakt u een gebruiker genaamd Britta Simon in SAP Fiori. Werk samen met uw eigen SAP-team van experts of uw SAP-partner voor uw organisatie om de gebruiker toe te voegen aan het SAP Fiori-platform.

## <a name="test-sso"></a>Test SSO

1. Nadat de identiteitsprovider Azure AD is geactiveerd in SAP Fiori, probeert u toegang te krijgen tot een van de volgende URL's om eenmalige aanmelding te testen (u moet niet worden gevraagd om een gebruikersnaam en wachtwoord):

    * https:\//\<sapurl\>/sap/bc/bsp/sap/it00/default.htm
    * https:\//\<sapurl\>/sap/bc/bsp/sap/it00/default.htm

    > [!NOTE]
    > Vervang *sapurl* door de werkelijke SAP-hostnaam.

1. De test-URL brengt u naar de volgende testtoepassingspagina in SAP. Als de pagina wordt geopend, is de enkele aanmelding van Azure AD ingesteld.

    ![De standaardtesttoepassingspagina in SAP](./media/sapfiori-tutorial/testingsso.png)

1. Als u wordt gevraagd om een gebruikersnaam en wachtwoord, schakelt u tracering in om het probleem te diagnosticeren. Gebruik de volgende URL voor\//\<het\>traceert: https: sap/sap/bc/webdynpro/sap/sec_diag_tool?sap-client=122&sap-language=EN#.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [SAP Fiori uitproberen met Azure AD](https://aad.portal.azure.com/)
