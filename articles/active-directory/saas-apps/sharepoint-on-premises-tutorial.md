---
title: 'Zelfstudie: Azure Active Directory-integratie met on-premises SharePoint | Microsoft Documenten'
description: Informatie over de configuratie van eenmalige aanmelding tussen Azure Active Directory en SharePoint on-premises.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 85b8d4d0-3f6a-4913-b9d3-8cc327d8280d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 03/19/2020
ms.author: jeedes
ms.openlocfilehash: 22b673ef481593247b6ee1007c13390a498c66be
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "80048618"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sharepoint-on-premises"></a>Zelfstudie: SSO-integratie (Single Sign-On) van Azure Active Directory (SSO) met On-premises SharePoint

In deze zelfstudie leert u hoe u SharePoint on-premises integreert met Azure Active Directory (Azure AD). Wanneer u SharePoint on-premises integreert met Azure AD, u het als:

* Beheer in Azure AD die on-premises toegang heeft tot SharePoint.
* Stel uw gebruikers in staat om automatisch on-premises te worden aangemeld bij SharePoint met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* SharePoint on-premises single sign-on (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

* SharePoint on-premises ondersteunt door **SP** geïnitieerde eenmalige aanmelding
* Zodra u SharePoint on-premises configureert, u sessiebesturingselementen afdwingen, die exfiltratie en infiltratie van de gevoelige gegevens van uw organisatie in realtime beschermen. Sessiebeheer strekt zich uit van Voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
* Raadpleeg deze [koppeling](https://docs.microsoft.com/archive/blogs/kaevans/sharepoint-2013-user-profile-sync-for-claims-users) voor meer informatie over het synchroniseren van gebruikersprofielen van SharePoint On-Premise naar Azure AD

## <a name="adding-sharepoint-on-premises-from-the-gallery"></a>SharePoint on-premises toevoegen uit de galerie

Voor het configureren van de integratie van SharePoint on-premises in Azure AD, moet u SharePoint on-premises uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**

    > [!NOTE]
    > Als het element niet beschikbaar is, kan het ook worden geopend via de vaste **verbinding Alle services** boven aan het linker navigatiepaneel. In het volgende overzicht bevindt de **Koppeling Azure Active Directory** zich in de sectie **Identiteit** of kan worden gezocht met behulp van het tekstvak filter.

1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **SharePoint on-premises** in het zoekvak in de sectie **Toevoegen vanuit de galerie.**
1. Selecteer **SharePoint on-premises** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sharepoint-on-premises"></a>Azure AD-aanmelding voor On-premises SharePoint configureren en testen

Azure AD SSO configureren en testen met On-premises SharePoint met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in SharePoint on-premises.

Als u Azure AD SSO wilt configureren en testen met On-premises SharePoint, voert u de volgende bouwstenen in:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige SharePoint on-premises-aanmelding configureren](#configure-sharepoint-on-premises-single-sign-on)**: als u de instellingen voor eenmalige aanmelding aan de clientzijde wilt configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[Maak een Azure AD-beveiligingsgroep in de Azure-portal](#create-an-azure-ad-security-group-in-the-azure-portal)** - om een nieuwe beveiligingsgroep in Azure AD in te schakelen voor eenmalige aanmelding.
5. **[Geef toegang tot SharePoint on-premises beveiligingsgroep](#grant-access-to-sharepoint-on-premises-security-group)** - geef toegang voor bepaalde groepen aan Azure AD.
6. **[Wijs de Azure AD-beveiligingsgroep toe aan de Azure-portal](#assign-the-azure-ad-security-group-in-the-azure-portal)** om de betreffende groep toe te wijzen aan Azure AD voor verificatie.
7. **[Eenmalige aanmelding testen](#test-single-sign-on)**: als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voor de configuratie van eenmalige Azure AD-aanmelding met SharePoint on-premises, moet u de volgende stappen uitvoeren:

1. Selecteer in [Azure Portal](https://portal.azure.com/) op de integratiepagina van de **SharePoint on-premises**-toepassing de optie **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:


    a. Typ in het tekstvak **AANmeldings-URL** een URL met het volgende patroon:`https://<YourSharePointServerURL>/_trust/default.aspx`

    b. In het tekstvak **Id** typt u een URL met het volgende patroon: `urn:sharepoint:federation`

    c. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<YourSharePointServerURL>/_trust/default.aspx`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id, de antwoord-URL en de aanmeldings-URL. Neem contact op met het [klantondersteuningsteam van SharePoint on-premises](https://support.office.com/) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

    > [!Note]
    > Noteer het bestandspad waarnaar u het certificaatbestand hebt gedownload. U hebt dit later nodig in het PowerShell-script voor de configuratie.

6. Kopieer in de sectie **SharePoint on-premises instellen** de juiste URL('s) overeenkomstig wat u nodig hebt. Gebruik voor de **URL van de service Eenmalige aanmelding** een waarde met de volgende notatie: `https://login.microsoftonline.com/_my_directory_id_/wsfed`

    > [!Note]
    > _my_directory_id_ is de tenant-id van het Azure AD-abonnement.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

    > [!NOTE]
    > De SharePoint on-premises-toepassing maakt gebruik van een SAML 1.1-token, zodat Azure AD een WS Fed-aanvraag verwacht van de SharePoint-server en na verificatie het SAML 1.1-token uitgeeft.

### <a name="configure-sharepoint-on-premises-single-sign-on"></a>Eenmalige SharePoint on-premises-aanmelding configureren

1. Meld u in een ander browservenster aan bij uw sharepoint-on-premises bedrijfssite als beheerder.

2. **Een nieuwe vertrouwde id-provider in SharePoint Server 2016 configureren**

    Meld u aan bij de server voor SharePoint Server 2016 en open de SharePoint 2016 Management Shell. Vul de waarden in voor $realm (de id-waarde in de sectie SharePoint on-premises-domein en -URL in Azure Portal), $wsfedurl (URL van de service Eenmalige aanmelding) en $filepath (bestandspad waarnaar u het certificaatbestand hebt gedownload) van Azure Portal en voer de volgende opdrachten uit om een nieuwe vertrouwde id-provider te configureren.

    > [!TIP]
    > Zie [SharePoint PowerShell](https://docs.microsoft.com/powershell/sharepoint/overview?view=sharepoint-ps) als u geen ervaring hebt met PowerShell of meer wilt weten over hoe PowerShell werkt.

    ```
    $realm = "<Identifier value from the SharePoint on-premises Domain and URLs section in the Azure portal>"
    $wsfedurl="<SAML single sign-on service URL value which you have copied from the Azure portal>"
    $filepath="<Full path to SAML signing certificate file which you have downloaded from the Azure portal>"
    $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($filepath)
    New-SPTrustedRootAuthority -Name "AzureAD" -Certificate $cert
    $map = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name" -IncomingClaimTypeDisplayName "name" -LocalClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn"
    $map2 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname" -IncomingClaimTypeDisplayName "GivenName" -SameAsIncoming
    $map3 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname" -IncomingClaimTypeDisplayName "SurName" -SameAsIncoming
    $map4 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress" -IncomingClaimTypeDisplayName "Email" -SameAsIncoming
    $map5 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.microsoft.com/ws/2008/06/identity/claims/role" -IncomingClaimTypeDisplayName "Role" -SameAsIncoming
    $ap = New-SPTrustedIdentityTokenIssuer -Name "AzureAD" -Description "SharePoint secured by Azure AD" -realm $realm -ImportTrustCertificate $cert -ClaimsMappings $map,$map2,$map3,$map4,$map5 -SignInUrl $wsfedurl -IdentifierClaim "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name"
    ```

    Voer vervolgens deze stappen uit om de vertrouwde id-provider voor uw toepassing in te schakelen:

    a. Navigeer in Centraal beheer naar **Webtoepassing beheren** en selecteer de webtoepassing die u wilt beveiligen met Azure AD.

    b. Klik in het lint op **Verificatieproviders** en kies de zone die u wilt gebruiken.

    c. Selecteer **Vertrouwde id-provider** en selecteer de id-provider die u zojuist hebt geregistreerd met de naam *AzureAD*.

    d. Selecteer in de instelling Aanmeldingspagina-URL **Aangepaste aanmeldingspagina** en geef de waarde /_trust/ op.

    e. Klik op **OK**.

    ![Uw verificatieprovider configureren](./media/sharepoint-on-premises-tutorial/fig10-configauthprovider.png)

    > [!NOTE]
    > Sommige externe gebruikers kunnen deze integratie van eenmalige aanmelding niet gebruiken, doordat hun UPN een niet-herkende waarde bevat, zoals `MYEMAIL_outlook.com#ext#@TENANT.onmicrosoft.com`. Binnenkort kunnen klanten hun app configureren voor de verwerking van de UPN, afhankelijk van het type gebruiker. Hierna moeten alle gastgebruikers eenmalige aanmelding probleemloos kunnen gebruiken als werknemers van de organisatie.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **Nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam****Britta Simon**in.
  
    b. In het veld **Gebruikersnaam** typt u `brittasimon@yourcompanydomain.extension`.  
    Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik **op Maken**.

### <a name="create-an-azure-ad-security-group-in-the-azure-portal"></a>Een Azure AD-beveiligingsgroep maken in de Azure-portal

1. Klik op **Azure Active Directory > Alle groepen**.

    ![Een Azure AD-beveiligingsgroep maken](./media/sharepoint-on-premises-tutorial/allgroups.png)

2. Klik op **Nieuwe groep:**

    ![Een Azure AD-beveiligingsgroep maken](./media/sharepoint-on-premises-tutorial/newgroup.png)

3. Groepstype **Group type**invullen , **groepsnaam**, **groepsomschrijving**, **lidmaatschapstype**. Klik op de pijl om leden te selecteren en zoek naar of klik op het lid dat u aan de groep wilt toevoegen. Klik op **Selecteren** om de geselecteerde leden toe te voegen en klik vervolgens op **Maken**.

    ![Een Azure AD-beveiligingsgroep maken](./media/sharepoint-on-premises-tutorial/addingmembers.png)

    > [!NOTE]
    > Om Azure Active Directory Security Groups on-premises aan SharePoint toe te wijzen, is het noodzakelijk om [AzureCP](https://yvand.github.io/AzureCP/) te installeren en configureren in de on-premises SharePoint-farm OF een alternatieve aangepaste claimprovider voor SharePoint te ontwikkelen en te configureren.  Zie de sectie meer informatie aan het einde van het document voor het maken van uw eigen leverancier van aangepaste claims als u AzureCP niet gebruikt.

### <a name="grant-access-to-sharepoint-on-premises-security-group"></a>On-premises beveiligingsgroep verlenen tot SharePoint-beveiligingsgroep

**Beveiligingsgroepen en machtigingen configureren voor de app-registratie**

1. Selecteer in de Azure-portal **Azure Active Directory**en selecteer vervolgens **App-registraties**.

    ![De blade Bedrijfstoepassingen](./media/sharepoint-on-premises-tutorial/appregistrations.png)

2. Typ en selecteer **SharePoint on-premises**in het zoekvak .

    ![SharePoint on-premises in de lijst met resultaten](./media/sharepoint-on-premises-tutorial/appsearch.png)

3. Klik op **Manifest**.

    ![Manifest, optie](./media/sharepoint-on-premises-tutorial/manifest.png)

4. Wijzigen `groupMembershipClaims` `NULL`: `groupMembershipClaims`, `SecurityGroup`Naar : . Klik vervolgens op Opslaan

    ![Manifest bewerken](./media/sharepoint-on-premises-tutorial/manifestedit.png)

5. Klik op **Instellingen**en klik vervolgens op **Vereiste machtigingen.**

    ![Vereiste machtigingen](./media/sharepoint-on-premises-tutorial/settings.png)

6. Klik op **Toevoegen** en **selecteer vervolgens een API**.

    ![API-toegang](./media/sharepoint-on-premises-tutorial/required_permissions.png)

7. Voeg zowel **Windows Azure Active Directory** als Microsoft Graph API **toe,** maar het is alleen mogelijk om er een tegelijk te selecteren.

    ![API selecteren](./media/sharepoint-on-premises-tutorial/permissions.png)

8. Selecteer Windows Azure Active Directory, controleer Mapgegevens lezen en klik op Selecteren. Ga terug en voeg Microsoft Graph toe en selecteer Mapgegevens lezen.  Klik op Selecteren en klik op Gereed.

    ![Toegang inschakelen](./media/sharepoint-on-premises-tutorial/readpermission.png)

9. Klik nu onder Vereiste instellingen op **Machtigingen verlenen** en klik vervolgens op Ja om machtigingen toe te kennen.

    ![Machtigingen verlenen](./media/sharepoint-on-premises-tutorial/grantpermission.png)

    > [!NOTE]
    > Controleer onder meldingen of de machtigingen zijn verleend.  Als dit niet het zo is, werkt de AzureCP niet goed en is het niet mogelijk om SharePoint on-premises te configureren met Azure Active Directory Security Groups.

10. Configureer de AzureCP op de On-premises Farm van SharePoint of een alternatieve oplossing voor aangepaste claimprovider.  In dit voorbeeld gebruiken we AzureCP.

    > [!NOTE]
    > Houd er rekening mee dat AzureCP geen Microsoft-product is of wordt ondersteund door Microsoft Technical Support. AzureCP downloaden, installeren en configureren op de on-premises SharePoint-farm per[https://yvand.github.io/AzureCP/](https://yvand.github.io/AzureCP/) 

11. **Geef toegang tot de Azure Active Directory Security Group in het on-premises SharePoint:-** De groepen moeten toegang krijgen tot de toepassing in SharePoint on-premises.  Gebruik de volgende stappen om de machtigingen voor toegang tot de webtoepassing in te stellen.

12. Klik in Centraal beheer op Toepassingsbeheer, Webtoepassingen beheren, selecteer vervolgens de webtoepassing om het lint te activeren en klik op Gebruikersbeleid.

    ![Centrale administratie](./media/sharepoint-on-premises-tutorial/centraladministration.png)

13. Klik onder Beleid voor webtoepassing op Gebruikers toevoegen en selecteer de zone en klik op Volgende.  Klik op het adresboek.

    ![Beleid voor webtoepassing](./media/sharepoint-on-premises-tutorial/webapp-policy.png)

14. Zoek en voeg vervolgens de Azure Active Directory Security Group toe en klik op OK.

    ![Beveiligingsgroep toevoegen](./media/sharepoint-on-premises-tutorial/securitygroup.png)

15. Selecteer de machtigingen en klik op Voltooien.

    ![Beveiligingsgroep toevoegen](./media/sharepoint-on-premises-tutorial/permissions1.png)

16. Zie onder Beleid voor webtoepassing wordt de Azure Active Directory Group toegevoegd.  De groepsclaim toont de object-id van azure Active Directory Security Group voor de gebruikersnaam.

    ![Beveiligingsgroep toevoegen](./media/sharepoint-on-premises-tutorial/addgroup.png)

17. Blader naar de SharePoint-siteverzameling en voeg daar ook de groep toe. Klik op Site-instellingen en klik vervolgens op Sitemachtigingen en Machtigingen verlenen.  Zoek naar de claim Groepsrol, wijs het machtigingsniveau toe en klik op Delen.

    ![Beveiligingsgroep toevoegen](./media/sharepoint-on-premises-tutorial/grantpermission1.png)

### <a name="configuring-one-trusted-identity-provider-for-multiple-web-applications"></a>Eén vertrouwde id-provider voor meerdere webtoepassingen configureren

De configuratie werkt voor één webtoepassing. Als u dezelfde vertrouwde id-provider wilt gebruiken voor meerdere webtoepassingen, moet u aanvullende configuratiestappen uitvoeren. Stel, u hebt een webtoepassing uitgebreid voor gebruik van de URL `https://portal.contoso.local` en u wilt de gebruikers ook verifiëren voor `https://sales.contoso.local`. Hiervoor moet de id-provider worden bijgewerkt om te voldoen aan de WReply-parameter en moet de toepassingsregistratie in Azure AD worden bijgewerkt om een antwoord-URL toe te voegen.

1. Open de Azure AD-map in de Azure-portal. Klik op **App-registraties** en klik vervolgens op **Alle toepassingen weergeven**. Klik op de toepassing die u eerder hebt gemaakt (SharePoint SAML-integratie).

2. Klik op **Instellingen**.

3. Klik op de blade Instellingen op **Antwoord-URL's**.

4. Voeg de URL toe voor de extra webtoepassing en voeg `/_trust/default.aspx` toe aan de URL (bijvoorbeeld `https://sales.contoso.local/_trust/default.aspx`) en klik vervolgens op **Opslaan**.

5. Open op de SharePoint-server de **SharePoint 2016 Management Shell** en voer de volgende opdrachten uit met de naam van de vertrouwde id-tokenuitgever die u eerder hebt gebruikt.

    ```
    $t = Get-SPTrustedIdentityTokenIssuer "AzureAD"
    $t.UseWReplyParameter=$true
    $t.Update()
    ```

6. Ga in Centraal beheer naar de webtoepassing en schakel de bestaande vertrouwde id-provider in. Configureer de URL van de aanmeldingspagina ook als een aangepaste aanmeldingspagina `/_trust/`.

7. Klik in Centraal beheer op de webtoepassing en kies **Gebruikersbeleid**. Voeg een gebruiker toe met de juiste machtigingen, zoals eerder beschreven.

### <a name="fixing-people-picker"></a>De personenkiezer herstellen

Gebruikers kunnen zich nu aanmelden bij SharePoint 2016 met behulp van identiteiten van Azure AD, maar er zijn nog steeds mogelijkheden voor verbetering van de gebruikerservaring. Wanneer u bijvoorbeeld zoekt naar een gebruiker, worden meerdere zoekresultaten weergegeven in de personenkiezer. Er is een zoekresultaat voor elk van de 3 claimtypen die zijn gemaakt in de claimtoewijzing. Als u een gebruiker wilt kiezen met de personenkiezer, moet u de gebruikersnaam exact typen en het resultaat van de **naam**claim kiezen.

![Zoekresultaten van claims](./media/sharepoint-on-premises-tutorial/fig16-claimssearchresults.png)

Er wordt geen validatie uitgevoerd op de waarden die u zoekt, wat tot spelfouten kan leiden of ervoor kan zorgen dat gebruikers per ongeluk het verkeerde claimtype kiezen voor toewijzing, zoals de claim **Achternaam**. Hierdoor hebben gebruikers mogelijk geen toegang tot resources.

De open-sourceoplossing [AzureCP](https://yvand.github.io/AzureCP/) biedt een aangepaste claimprovider voor SharePoint 2016. Het zal de Microsoft Graph API gebruiken om op te lossen wat gebruikers invoeren en validatie uit te voeren. U vindt hierover meer informatie op [AzureCP](https://yvand.github.io/AzureCP/).

### <a name="assign-the-azure-ad-security-group-in-the-azure-portal"></a>De Azure AD-beveiligingsgroep toewijzen in de Azure-portal

1. Selecteer in Azure Portal **Bedrijfstoepassingen**, selecteer **Alle toepassingen** en selecteer vervolgens **SharePoint on-premises**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Typ en selecteer **SharePoint on-premises** in de lijst met toepassingen.

    ![De koppeling van SharePoint on-premises in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik **op**de gebruiker toevoegen .

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Zoek naar de beveiligingsgroep die u wilt gebruiken en klik vervolgens op de groep om deze toe te voegen aan de sectie Leden selecteren. Klik **op Selecteren**en klik vervolgens op **Toewijzen**.

    ![Beveiligingsgroep zoeken](./media/sharepoint-on-premises-tutorial/securitygroup1.png)

    > [!NOTE]
    > Controleer de meldingen in de menubalk om te worden gemeld dat de groep is toegewezen aan de Enterprise-toepassing in de Azure-portal.

### <a name="create-sharepoint-on-premises-test-user"></a>Een testgebruiker maken voor SharePoint on-premises

In deze sectie maakt u in SharePoint on-premises een gebruiker met de naam Britta Simon. Werk samen met [het on-premises ondersteuningsteam van SharePoint](https://support.office.com/) om de gebruikers toe te voegen aan het on-premises SharePoint-platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Als u op de tegel SharePoint on-premises in het toegangsvenster klikt, wordt u automatisch aangemeld bij de instantie van SharePoint on-premises waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)