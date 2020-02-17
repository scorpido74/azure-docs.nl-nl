---
title: 'Zelf studie: integratie met share point op locatie Azure Active Directory | Microsoft Docs'
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
ms.devlang: na
ms.topic: tutorial
ms.date: 04/25/2019
ms.author: jeedes
ms.openlocfilehash: 60ff8769192191ba112008e7baee23740147dfe9
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/15/2020
ms.locfileid: "77367030"
---
# <a name="tutorial-azure-active-directory-integration-with-sharepoint-on-premises"></a>Zelf studie: integratie met share point op locatie Azure Active Directory

In deze zelfstudie leert u hoe u SharePoint on-premises integreert met Azure Active Directory (Azure AD).
De integratie van SharePoint on-premises met Azure AD biedt de volgende voordelen:

* U kunt in Azure AD bepalen wie toegang heeft tot SharePoint on-premises.
* U kunt inschakelen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij SharePoint on-premises (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Voor de configuratie van Azure AD-integratie met SharePoint on-premises, hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) aanvragen
* Een abonnement op SharePoint on-premises waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* SharePoint on-premises ondersteunt door **SP** geïnitieerde eenmalige aanmelding

## <a name="adding-sharepoint-on-premises-from-the-gallery"></a>SharePoint on-premises toevoegen uit de galerie

Voor het configureren van de integratie van SharePoint on-premises in Azure AD, moet u SharePoint on-premises uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om SharePoint on-premises uit de galerie toe te voegen:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

    > [!NOTE]   
    > Als het element niet beschikbaar moet zijn, kan het ook worden geopend via de koppeling vaste **alle services** bovenin het navigatie paneel aan de linkerkant. In het volgende overzicht bevindt de **Azure Active Directory** koppeling zich in het gedeelte **identiteit** of kan er worden gezocht met behulp van het tekstvak filter.

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ in het zoekvak **SharePoint on-premises**, selecteer in het deelvenster met resultaten **SharePoint on-premises** en klik vervolgens op **Toevoegen** om de toepassing toe te voegen.

    ![SharePoint on-premises in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u Azure AD-eenmalige aanmelding met SharePoint on-premises op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in SharePoint on-premises tot stand is gebracht.

Als u Azure AD-eenmalige aanmelding met SharePoint on-premises wilt configureren en testen, moet u de volgende bouwstenen uitvoeren:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[SharePoint on-premises-eenmalige aanmelding configureren](#configure-sharepoint-on-premises-single-sign-on)** : als u de instellingen voor eenmalige aanmelding aan de clientzijde wilt configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[Maak een Azure AD-beveiligings groep in de Azure Portal](#create-an-azure-ad-security-group-in-the-azure-portal)** -om een nieuwe beveiligings groep in azure AD in te scha kelen voor eenmalige aanmelding.
5. **[Toegang verlenen tot share point on-premises beveiligings groep](#grant-access-to-sharepoint-on-premises-security-group)** : toegang verlenen aan een bepaalde groep in azure AD.
6. **[Wijs de Azure AD-beveiligings groep toe aan de Azure Portal](#assign-the-azure-ad-security-group-in-the-azure-portal)** -om de specifieke groep toe te wijzen aan Azure AD voor verificatie.
7. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voor de configuratie van Azure AD-eenmalige aanmelding met SharePoint on-premises, moet u de volgende stappen uitvoeren:

1. Selecteer in [Azure Portal](https://portal.azure.com/) op de integratiepagina van de **SharePoint on-premises**-toepassing de optie **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Informatie over eenmalige aanmelding bij het SharePoint on-premises-domein en -URL's](common/sp-identifier-reply.png)

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://<YourSharePointServerURL>/_trust/default.aspx`

    b. In het tekstvak **Id** typt u een URL met het volgende patroon: `urn:sharepoint:federation`

    c. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<YourSharePointServerURL>/_trust/default.aspx`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL, id en antwoord-URL. Neem contact op met het [klantondersteuningsteam van SharePoint on-premises](https://support.office.com/) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

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
    > De SharePoint on-premises-toepassing maakt gebruik van een SAML 1.1-token, zodat Azure AD een WS Fed-aanvraag verwacht van de SharePoint-server en na verificatie het SAML 1.1-token. uitgeeft.

### <a name="configure-sharepoint-on-premises-single-sign-on"></a>SharePoint on-premises-eenmalige aanmelding configureren

1. Meld u in een ander webbrowser venster aan bij uw on-premises share point-bedrijfs site als beheerder.

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

    a. Voer in het veld **Naam** **Britta Simon**in.
  
    b. Typ in het veld **gebruikers naam** `brittasimon@yourcompanydomain.extension`  
    Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik op **Create**.

### <a name="create-an-azure-ad-security-group-in-the-azure-portal"></a>Een Azure AD-beveiligings groep maken in de Azure Portal

1. Klik op **Azure Active Directory alle groepen >** .

    ![Een Azure AD-beveiligings groep maken](./media/sharepoint-on-premises-tutorial/allgroups.png)

2. Klik op **nieuwe groep**:

    ![Een Azure AD-beveiligings groep maken](./media/sharepoint-on-premises-tutorial/newgroup.png)

3. **Groeps type**, **groeps naam**, **groeps beschrijving**, **type lidmaatschap**. Klik op de pijl om leden te selecteren, zoek vervolgens naar of klik op het lid dat u aan de groep wilt toevoegen. Klik op **selecteren** om de geselecteerde leden toe te voegen en klik vervolgens op **maken**.

    ![Een Azure AD-beveiligings groep maken](./media/sharepoint-on-premises-tutorial/addingmembers.png)

    > [!NOTE]
    > Als u Azure Active Directory beveiligings groepen on-premises wilt toewijzen aan share point, moet u [AzureCP](https://yvand.github.io/AzureCP/) installeren en configureren in de on-premises share point-Farm of een alternatieve aangepaste claim provider voor share point ontwikkelen en configureren.  Zie de sectie meer informatie aan het einde van het document voor het maken van uw eigen aangepaste claim provider als u geen gebruik maakt van AzureCP.

### <a name="grant-access-to-sharepoint-on-premises-security-group"></a>Toegang verlenen tot een on-premises share point-beveiligings groep

**Beveiligings groepen en-machtigingen configureren voor de app-registratie**

1. Selecteer in de Azure Portal **Azure Active Directory**en selecteer vervolgens **app-registraties**.

    ![De blade Bedrijfstoepassingen](./media/sharepoint-on-premises-tutorial/appregistrations.png)

2. In het zoekvak typt en selecteert u **share point on-premises**.

    ![SharePoint on-premises in de lijst met resultaten](./media/sharepoint-on-premises-tutorial/appsearch.png)

3. Klik op **manifest**.

    ![Manifest optie](./media/sharepoint-on-premises-tutorial/manifest.png)

4. Wijzig `groupMembershipClaims`: `NULL`naar `groupMembershipClaims`: `SecurityGroup`. Klik vervolgens op opslaan

    ![Manifest bewerken](./media/sharepoint-on-premises-tutorial/manifestedit.png)

5. Klik op **instellingen**en klik vervolgens op **vereiste machtigingen**.

    ![Vereiste machtigingen](./media/sharepoint-on-premises-tutorial/settings.png)

6. Klik op **toevoegen** en **Selecteer vervolgens een API**.

    ![API-toegang](./media/sharepoint-on-premises-tutorial/required_permissions.png)

7. Voeg zowel **Windows Azure Active Directory** als **Microsoft Graph-API**toe, maar het is wel mogelijk om er één tegelijk te selecteren.

    ![API selecteren](./media/sharepoint-on-premises-tutorial/permissions.png)

8. Selecteer Windows Azure Active Directory, Controleer de gegevens van de Directory lezen en klik op selecteren. Ga terug en voeg Microsoft Graph toe en selecteer ook Directory gegevens lezen.  Klik op selecteren en klik op gereed.

    ![Toegang inschakelen](./media/sharepoint-on-premises-tutorial/readpermission.png)

9. Klik nu onder vereiste instellingen op **machtigingen verlenen** en klik vervolgens op Ja om machtigingen te verlenen.

    ![Machtigingen verlenen](./media/sharepoint-on-premises-tutorial/grantpermission.png)

    > [!NOTE]
    > Controleer onder meldingen om te bepalen of de machtigingen zijn toegekend.  Als dat niet het geval is, werkt de AzureCP niet goed en is het niet mogelijk om share point on-premises te configureren met Azure Active Directory-beveiligings groepen.

10. Configureer de AzureCP op de share point on-premises-Farm of een alternatieve oplossing voor aangepaste claim providers.  In dit voor beeld gebruiken we AzureCP.

    > [!NOTE]
    > Houd er rekening mee dat AzureCP geen micro soft-product is of niet wordt ondersteund door de technische ondersteuning van micro soft. Down load, installeer en configureer AzureCP op de on-premises share point-Farm per https://yvand.github.io/AzureCP/ 

11. **Toegang verlenen tot de Azure Active Directory beveiligings groep in de on-premises share point** :-voor de groepen moeten on-premises toegang worden verleend tot de toepassing in share point.  Gebruik de volgende stappen om de machtigingen voor toegang tot de webtoepassing in te stellen.

12. Klik in Centraal beheer op toepassings beheer, webtoepassingen beheren en selecteer vervolgens de webtoepassing om het lint te activeren en klik op gebruikers beleid.

    ![Centraal beheer](./media/sharepoint-on-premises-tutorial/centraladministration.png)

13. Klik onder beleid voor webtoepassing op gebruikers toevoegen en selecteer vervolgens de zone en klik op volgende.  Klik op het adres boek.

    ![Beleid voor webtoepassing](./media/sharepoint-on-premises-tutorial/webapp-policy.png)

14. Zoek en voeg de Azure Active Directory beveiligings groep toe en klik op OK.

    ![Beveiligings groep toevoegen](./media/sharepoint-on-premises-tutorial/securitygroup.png)

15. Selecteer de machtigingen en klik vervolgens op volt ooien.

    ![Beveiligings groep toevoegen](./media/sharepoint-on-premises-tutorial/permissions1.png)

16. Zie onder beleid voor Web Application de Azure Active Directory groep wordt toegevoegd.  De groepclaim toont de Azure Active Directory beveiligings groeps object-ID voor de gebruikers naam.

    ![Beveiligings groep toevoegen](./media/sharepoint-on-premises-tutorial/addgroup.png)

17. Blader naar de share point-site verzameling en voeg ook de groep toe. Klik op site-instellingen en klik vervolgens op site machtigingen en machtigingen verlenen.  Zoek naar de groepclaim, wijs het machtigings niveau toe en klik op delen.

    ![Beveiligings groep toevoegen](./media/sharepoint-on-premises-tutorial/grantpermission1.png)

### <a name="configuring-one-trusted-identity-provider-for-multiple-web-applications"></a>Eén vertrouwde id-provider voor meerdere webtoepassingen configureren

De configuratie werkt voor één webtoepassing. Als u dezelfde vertrouwde id-provider wilt gebruiken voor meerdere webtoepassingen, moet u aanvullende configuratiestappen uitvoeren. Stel, u hebt een webtoepassing uitgebreid voor gebruik van de URL `https://portal.contoso.local` en u wilt de gebruikers ook verifiëren voor `https://sales.contoso.local`. Hiervoor moet de id-provider worden bijgewerkt om te voldoen aan de WReply-parameter en moet de toepassingsregistratie in Azure AD worden bijgewerkt om een antwoord-URL toe te voegen.

1. Open de Azure AD-Directory in de Azure Portal. Klik op **App-registraties** en klik vervolgens op **Alle toepassingen weergeven**. Klik op de toepassing die u eerder hebt gemaakt (SharePoint SAML-integratie).

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

Gebruikers kunnen zich nu aanmelden bij share point 2016 met identiteiten vanuit Azure AD, maar er zijn nog steeds mogelijkheden voor verbetering van de gebruikers ervaring. Wanneer u bijvoorbeeld zoekt naar een gebruiker, worden meerdere zoekresultaten weergegeven in de personenkiezer. Er is een zoekresultaat voor elk van de 3 claimtypen die zijn gemaakt in de claimtoewijzing. Als u een gebruiker wilt kiezen met de personenkiezer, moet u de gebruikersnaam exact typen en het resultaat van de **naam**claim kiezen.

![Zoekresultaten van claims](./media/sharepoint-on-premises-tutorial/fig16-claimssearchresults.png)

Er wordt geen validatie uitgevoerd op de waarden die u zoekt, wat tot spelfouten kan leiden of ervoor kan zorgen dat gebruikers per ongeluk het verkeerde claimtype kiezen voor toewijzing, zoals de claim **Achternaam**. Hierdoor hebben gebruikers mogelijk geen toegang tot resources.

De open-sourceoplossing [AzureCP](https://yvand.github.io/AzureCP/) biedt een aangepaste claimprovider voor SharePoint 2016. De Microsoft Graph-API wordt gebruikt om te achterhalen wat gebruikers invoeren en validatie uitvoeren. U vindt hierover meer informatie op [AzureCP](https://yvand.github.io/AzureCP/).

### <a name="assign-the-azure-ad-security-group-in-the-azure-portal"></a>Wijs de Azure AD-beveiligings groep toe aan de Azure Portal

1. Selecteer in Azure Portal **Bedrijfstoepassingen**, selecteer **Alle toepassingen** en selecteer vervolgens **SharePoint on-premises**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Typ en selecteer **SharePoint on-premises** in de lijst met toepassingen.

    ![De koppeling van SharePoint on-premises in de lijst met toepassingen](common/all-applications.png)

3. Selecteer **Gebruikers en groepen** in het menu aan de linkerkant.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de **gebruiker toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Zoek naar de beveiligings groep die u wilt gebruiken en klik vervolgens op de groep om deze toe te voegen aan de sectie leden selecteren. Klik op **selecteren**en vervolgens op **toewijzen**.

    ![Beveiligings groep zoeken](./media/sharepoint-on-premises-tutorial/securitygroup1.png)

    > [!NOTE]
    > Controleer de meldingen in de menu balk om een melding te ontvangen dat de groep is toegewezen aan de bedrijfs toepassing in de Azure Portal.

### <a name="create-sharepoint-on-premises-test-user"></a>Een testgebruiker maken voor SharePoint on-premises

In deze sectie maakt u in SharePoint on-premises een gebruiker met de naam Britta Simon. Neem contact op met het  [ondersteuningsteam van SharePoint on-premises](https://support.office.com/) om de gebruikers toe te voegen aan het SharePoint on-premises-platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Als u op de tegel SharePoint on-premises in het toegangsvenster klikt, wordt u automatisch aangemeld bij de instantie van SharePoint on-premises waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
