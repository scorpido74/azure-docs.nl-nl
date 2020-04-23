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
ms.date: 01/15/2020
ms.author: miguego
ms.openlocfilehash: 7e47891a74feb60b0f3e4594bd1621e8b62d64d1
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81867101"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sharepoint-on-premises"></a>Zelfstudie: SSO-integratie (Single Sign-On) van Azure Active Directory (SSO) met On-premises SharePoint

In deze zelfstudie leert u hoe u SharePoint on-premises integreert met Azure Active Directory (Azure AD). Wanneer u SharePoint on-premises integreert met Azure AD, u het als:

* Beheer in Azure AD die on-premises toegang heeft tot SharePoint.
* Stel uw gebruikers in staat om automatisch on-premises te worden aangemeld bij SharePoint met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Als u Azure Active Directory-integratie wilt configureren met on-premises SharePoint, hebt u de volgende items nodig:

* Een Azure Active Directory-abonnement. Als u geen Azure AD-omgeving hebt, u een [gratis account](https://azure.microsoft.com/free/) krijgen
* Een SharePoint 2013-farm of nieuwer.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure Active Directory single sign-on in een testomgeving. Gebruikers van Azure Active Directory hebben toegang tot uw Sharepoint On-Premises.

## <a name="create-the-enterprise-applications-in-azure-portal"></a>De Enterprise-toepassingen maken in Azure-portal

Voor het configureren van de integratie van SharePoint on-premises in Azure AD, moet u SharePoint on-premises uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

Voer de volgende stappen uit om SharePoint on-premises uit de galerie toe te voegen:

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

 > [!NOTE]
 > Als het element niet beschikbaar is, kan het ook worden geopend via de vaste **verbinding Alle services** boven aan het linker navigatiepaneel. In het volgende overzicht bevindt de **Koppeling Azure Active Directory** zich in de sectie **Identiteit** of kan worden gezocht met behulp van het tekstvak filter.

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

4. Typ **SharePoint on-premises in**het zoekvak en selecteer **SharePoint on-premises** vanuit het resultaatpaneel.

    <kbd>![SharePoint on-premises in de lijst met resultaten](./media/sharepoint-on-premises-tutorial/search-new-app.png)</kbd>

1. Geef een naam op voor uw SharePoint OnPrem en klik op de knop **Toevoegen** om de toepassing toe te voegen.

1. Klik in de nieuwe Enterprise-toepassing op Eigenschappen en controleer de vereiste waarde voor **gebruikerstoewijzing**

   <kbd>![SharePoint on-premises in de lijst met resultaten](./media/sharepoint-on-premises-tutorial/user-assignment-required.png)</kbd>

in ons scenario is deze waarde ingesteld op **Nee**.

## <a name="configure-and-test-azure-ad"></a>Azure AD configureren en testen

In deze sectie configureert u Azure AD-enkele aanmelding met On-premises SharePoint.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in SharePoint on-premises tot stand is gebracht.

Als u Azure Active Directory-aanmelding met On-premises van SharePoint wilt configureren en testen, moet u de volgende bouwstenen voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
1. **[SharePoint on-premises configureren](#configure-sharepoint-on-premises)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
1. **[Maak een Azure AD-testgebruiker in de Azure-portal](#create-an-azure-ad-test-user-in-the-azure-portal)** - maak nieuwe gebruikers in Azure AD voor eenmalige aanmelding.
1. **[Maak een Azure AD-beveiligingsgroep in de Azure-portal](#create-an-azure-ad-security-group-in-the-azure-portal)** - maak een nieuwe beveiligingsgroep in Azure AD voor eenmalige aanmelding.
1. **[Machtigingen verlenen aan Azure Active Directory-account in SharePoint on-premises](#grant-permissions-to-azure-active-directory-account-in-sharepoint-on-premises)** - geef machtigingen aan Azure AD-gebruiker.
1. **[Machtigingen verlenen aan de Azure AD-groep in SharePoint on-premises](#grant-permissions-to-azure-ad-group-in-sharepoint-on-premises)** - geef machtigingen aan de Azure AD-groep.
1. **[Geef on-premises machtigingen voor een gastaccount aan SharePoint in de Azure-portal](#grant-access-to-a-guest-account-to-sharepoint-on-premises-in-the-azure-portal)** - geef machtigingen aan gastaccount in Azure AD voor On-premises SharePoint.
1. **[De vertrouwde identiteitsprovider configureren voor meerdere webtoepassingen](#configuring-the-trusted-identity-provider-for-multiple-web-applications)** - dezelfde vertrouwde identiteitsprovider gebruiken voor meerdere webtoepassingen

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voor de configuratie van eenmalige Azure AD-aanmelding met SharePoint on-premises, moet u de volgende stappen uitvoeren:

1. Open in de [Azure-portal](https://portal.azure.com/)de Azure AD-map, klik op **Enterprise-toepassingen**, klik op de **eerder gemaakte naam van de Enterprise-toepassing** en klik op Eén **aanmelding**.

2. Klik in het dialoogvenster **Eenmalige aanmeldingsmethode** selecteren op **de SAML-modus** om eenmalige aanmelding in te schakelen.
 
3. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram **Bewerken** om het dialoogvenster **BasisSAML-configuratie** te openen.

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Informatie over eenmalige aanmelding bij het SharePoint on-premises-domein en -URL's](./media/sharepoint-on-premises-tutorial/sp-identifier-reply.png)

    1. In het tekstvak **Id** typt u een URL met het volgende patroon: `urn:<sharepointFarmName>:<federationName>`

    1. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<YourSharePointSiteURL>/_trust/`

    1. Typ in het tekstvak **AANmeldings-URL** een URL met het volgende patroon:`https://<YourSharePointSiteURL>/`
    1. klik op opslaan.

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id, de antwoord-URL en de aanmeldings-URL.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](./media/sharepoint-on-premises-tutorial/certificatebase64.png)

6. Kopieer in de sectie **SharePoint on-premises instellen** de juiste URL('s) overeenkomstig wat u nodig hebt.
    
    1. **Aanmeldings-URL**
    
        Copy login URL vervangen **/ saml2** aan het einde met **https://login.microsoftonline.com/2c4f1a9f-be5f-10ee-327d-a95dac567e4f/wsfed** / **wsfed**, het zou eruit zien (deze url is niet accuraat)

    1. **Azure AD-id**
    1. **Afmeldings-URL**
    > [!NOTE]
    > Deze URL kan niet worden gebruikt zoals in SharePoint: u moet **/saml2** vervangen door **/wsfed**. De SharePoint on-premises-toepassing maakt gebruik van een SAML 1.1-token, zodat Azure AD een WS Fed-aanvraag verwacht van de SharePoint-server en na verificatie het SAML 1.1-token uitgeeft.

### <a name="configure-sharepoint-on-premises"></a>SharePoint on-premises configureren

1. **Een nieuwe vertrouwde identiteitsprovider maken in SharePoint Server 2016**

    Meld u aan bij sharepoint-server en open de SharePoint-beheershell. Vul de waarden in:
    1. **$realm** (id-waarde van de sectie On-premises domein en URL's van SharePoint in de Azure-portal).
    1. **$wsfedurl** (URL van een malige aanmeldingsservice).
   1. **$filepath** (bestandspad waarnaar u het certificaatbestand hebt gedownload) van azure-portal.

    Voer de volgende opdrachten uit om een nieuwe vertrouwde identiteitsprovider te configureren.

    > [!TIP]
    > Zie [SharePoint PowerShell](https://docs.microsoft.com/powershell/sharepoint/overview?view=sharepoint-ps) als u geen ervaring hebt met PowerShell of meer wilt weten over hoe PowerShell werkt.


    ```
    $realm = "urn:sharepoint:sps201x"
    $wsfedurl="https://login.microsoftonline.com/2c4f1a9f-be5f-10ee-327d-a95dac567e4f/wsfed"
    $filepath="C:\temp\SharePoint 2019 OnPrem.cer"
    $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($filepath)
    New-SPTrustedRootAuthority -Name "AzureAD" -Certificate $cert
    $map1 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name" -IncomingClaimTypeDisplayName "name" -LocalClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn"
    $map2 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.microsoft.com/ws/2008/06/identity/claims/role" -IncomingClaimTypeDisplayName "Role" -SameAsIncoming
    $ap = New-SPTrustedIdentityTokenIssuer -Name "AzureAD" -Description "Azure AD SharePoint server 201x" -realm $realm -ImportTrustCertificate $cert -ClaimsMappings $map1,$map2 -SignInUrl $wsfedurl -IdentifierClaim $map1.InputClaimType
    ```
1. **De aanbieder van vertrouwde identiteit inschakelen voor uw toepassing**

    a. Navigeer in Centraal beheer naar **Webtoepassing beheren** en selecteer de webtoepassing die u wilt beveiligen met Azure AD.

    b. Klik in het lint op **Verificatieproviders** en kies de zone die u wilt gebruiken.

    c. Selecteer **Vertrouwde id-provider** en selecteer de id-provider die u zojuist hebt geregistreerd met de naam *AzureAD*.

    d. Klik op **OK**.

    ![Uw verificatieprovider configureren](./media/sharepoint-on-premises-tutorial/config-auth-provider.png)

### <a name="create-an-azure-ad-test-user-in-the-azure-portal"></a>Een Azure AD-testgebruiker maken in de Azure-portal

Het doel van deze sectie is het maken van een testgebruiker in de Azure-portal.

1. Selecteer in de Azure-portal in het linkerdeelvenster **Azure Active Directory**, selecteer in Het pan **beheren** de **optie Gebruikers**.

2. Selecteer vervolgens **Alle gebruikers,** gevolgd door **Nieuwe gebruiker** boven aan het scherm.

3. Selecteer de optie **Gebruiker maken** en voer in de eigenschappen Gebruiker de volgende stappen uit.  
   Mogelijk u gebruikers in uw Azure AD maken met behulp van uw tenantachtervoegsel of een geverifieerd domein. 

    a. In het veld **Naam** voer we de gebruikersnaam in, hebben we **TestUser**gebruikt.
  
    b. In het veld **Gebruikersnaam** typt u `TestUser@yourcompanydomain.extension`.  
    Bijvoorbeeld: TestUser@contoso.com

    ![Het dialoogvenster Gebruiker](./media/sharepoint-on-premises-tutorial/user-properties.png)

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik **op Maken**.

    e. U de site TestUser@contoso.com nu delen met en deze gebruiker toegang geven tot deze site.

### <a name="create-an-azure-ad-security-group-in-the-azure-portal"></a>Een Azure AD-beveiligingsgroep maken in de Azure-portal

1. Klik op **Azure Active Directory > Groepen**.

2. Klik op **Nieuwe groep:**

3. Groepstype **Group type**invullen , **groepsnaam**, **groepsomschrijving**, **lidmaatschapstype**. Klik op de pijl om leden te selecteren en zoek naar of klik op het lid dat u aan de groep wilt toevoegen. Klik op **Selecteren** om de geselecteerde leden toe te voegen en klik vervolgens op **Maken**.

![Een Azure AD-beveiligingsgroep maken](./media/sharepoint-on-premises-tutorial/new-group.png)

### <a name="grant-permissions-to-azure-active-directory-account-in-sharepoint-on-premises"></a>Machtigingen verlenen aan Azure Active Directory-account in On-premises SharePoint

Als u toegang wilt verlenen tot de Azure Active Directory-gebruiker in het on-premise SharePoint, moet u de siteverzameling delen of de Azure Active Directory-gebruiker toevoegen aan een van de groep van de siteverzameling. Gebruikers kunnen zich nu aanmelden bij SharePoint 201x met behulp van identiteiten van Azure AD, maar er zijn nog steeds mogelijkheden voor verbetering van de gebruikerservaring. Wanneer u bijvoorbeeld zoekt naar een gebruiker, worden meerdere zoekresultaten weergegeven in de personenkiezer. Er is een zoekresultaat voor elk van de claimtypen die zijn gemaakt in de claimtoewijzing. Als u een gebruiker wilt kiezen met de personenkiezer, moet u de gebruikersnaam exact typen en het resultaat van de **naam**claim kiezen.

![Zoekresultaten van claims](./media/sharepoint-on-premises-tutorial/claims-search-results.png)

Er is geen validatie op de waarden die u zoekt, wat kan leiden tot spelfouten of gebruikers die per ongeluk het verkeerde claimtype kiezen. Hierdoor hebben gebruikers mogelijk geen toegang tot resources.

**Om de mensenkiezer** met dit scenario op te lossen, is er een open-sourceoplossing genaamd [AzureCP](https://yvand.github.io/AzureCP/) die een aangepaste claimprovider biedt voor SharePoint 2013, 2016 en 2019. Het zal de Microsoft Graph API gebruiken om op te lossen wat gebruikers invoeren en validatie uit te voeren. U vindt hierover meer informatie op [AzureCP](https://yvand.github.io/AzureCP/).

  > [!NOTE]
  > Zonder AzureCP u groepen toevoegen door de ID van de Azure AD-groep toe te voegen, maar dit is niet vriendelijk en betrouwbaar voor de gebruiker. Er is hoe het eruit ziet:  
  >   
  >![Azure AD-groep toevoegen aan Sharepoint-groep](./media/sharepoint-on-premises-tutorial/adding-group-by-id.png)
  
### <a name="grant-permissions-to-azure-ad-group-in-sharepoint-on-premises"></a>Machtigingen verlenen aan Azure AD-groep in SharePoint on-premises

Om Azure Active Directory Security Groups on-premise aan SharePoint toe te wijzen, is het noodzakelijk om een aangepaste claimprovider voor SharePoint Server te gebruiken. In ons voorbeeld hebben we AzureCP gebruikt.

 > [!NOTE]
 > Houd er rekening mee dat AzureCP geen Microsoft-product is of wordt ondersteund door Microsoft Technical Support. AzureCP downloaden, installeren en configureren op de https://yvand.github.io/AzureCP/on-premises SharePoint-farm per . 

1. Configureer de AzureCP op de On-premises Farm van SharePoint of een alternatieve oplossing voor aangepaste claimprovider. de stappen van de AzureCP-configuratie zijn beschikbaar ophttps://yvand.github.io/AzureCP/Register-App-In-AAD.html

1. Open de Azure AD-map in de Azure-portal. Klik op **Enterprise-toepassingen,** klik op de **eerder gemaakte naam van de Enterprise Application** en klik op Enkele **aanmelding**.

1. Bewerk op de pagina **Single Sign-On with SAM**de sectie **Gebruikerskenmerken & Claims.**

1. Klik op **Een groepsclaim toevoegen**.

1. Selecteer welke groepen die aan de gebruiker zijn gekoppeld, in de claim moeten worden geretourneerd, selecteer in ons geval **Alle groepen** en selecteer in de sectie Bronkenmerk **Groeps-id** en klik op **Opslaan**.

Als u toegang wilt verlenen tot de Azure Active Directory Security Group in het on-premise SharePoint, moet u de siteverzameling delen of de Azure Active Directory Security Group toevoegen aan een van de groep van de siteverzameling.

1. Blader naar de SharePoint-siteverzameling, klik onder Site-instellingen voor de siteverzameling op 'Personen en groepen'. Selecteer de SharePoint-groep en klik op Nieuw, 'Gebruikers toevoegen aan deze groep' en typ de naam van uw groep die de Lijstpicker van Personen de Azure Active Directory Security Group weergeeft.

    ![Azure AD-groep toevoegen aan Sharepoint-groep](./media/sharepoint-on-premises-tutorial/permission-azure-ad-group.png)

### <a name="grant-access-to-a-guest-account-to-sharepoint-on-premises-in-the-azure-portal"></a>On-premises toegang verlenen tot een gastaccount aan SharePoint in de Azure-portal

het is nu mogelijk om op een consistente manier toegang tot uw SharePoint-site te verlenen aan een gastaccount. Het gebeurt dat de UPN wordt gewijzigd. Gebruiker jdoe@outlook.com met zal worden `jdoe_outlook.com#ext#@TENANT.onmicrosoft.com`vertegenwoordigd als . Het is mogelijk om een naadloze ervaring te krijgen terwijl u uw site deelt met externe gebruikers, het zou nodig zijn om enkele wijzigingen toe te voegen aan uw **sectie Gebruikerskenmerken & Claims** in de Azure-portal.

1. Open de Azure AD-map in de Azure-portal. Klik op **Enterprise-toepassingen,** klik op de **eerder gemaakte naam van de Enterprise Application** en klik op Enkele **aanmelding**.

1. Bewerk op de pagina **Single Sign-On with SAM**de sectie **Gebruikerskenmerken & Claims.**

1. klik in de **vereiste claimzone** op **Unieke gebruikersnaam (naam-id).**

1. Wijzig de eigenschap **Eigenschap Bronkenmerk** in de naam value **user.localuserprincipalname** en **sla op**.

    ![Gebruikerskenmerken & claims initieel](./media/sharepoint-on-premises-tutorial/manage-claim.png)

1. Met behulp van lint ga terug naar **SAML-gebaseerde Sign-on** nu de **user attributes & Claims** sectie zou er zo uitzien: 

    ![Gebruikerskenmerken & claims definitief](./media/sharepoint-on-premises-tutorial/user-attributes-claims-final.png)  

    > [!NOTE]
    > Achternaam en voornaam zijn niet vereist in deze setup.

1. Selecteer In de Azure-portal in het linkerdeelvenster de optie **Azure Active Directory**, selecteer **Gebruikers**.

1. klik op **Nieuwe gastgebruiker**

1. Selecteer de optie **Gebruiker uitnodigen** en vul de eigenschappen van de gebruiker op en klik op **Uitnodigen**.

1. U de site MyGuestAccount@outlook.com nu delen met en deze gebruiker toegang geven tot deze site.

    ![Site delen met gastaccount](./media/sharepoint-on-premises-tutorial/sharing-guest-account.png)

### <a name="configuring-the-trusted-identity-provider-for-multiple-web-applications"></a>De vertrouwde identiteitsprovider configureren voor meerdere webtoepassingen

De configuratie werkt voor één webtoepassing. Als u dezelfde vertrouwde id-provider wilt gebruiken voor meerdere webtoepassingen, moet u aanvullende configuratiestappen uitvoeren. Stel, u hebt een webtoepassing uitgebreid voor gebruik van de URL `https://sales.contoso.com` en u wilt de gebruikers ook verifiëren voor `https://marketing.contoso.com`. Hiervoor moet de id-provider worden bijgewerkt om te voldoen aan de WReply-parameter en moet de toepassingsregistratie in Azure AD worden bijgewerkt om een antwoord-URL toe te voegen.

1. Open de Azure AD-map in de Azure-portal. Klik op **Enterprise-toepassingen,** klik op de **eerder gemaakte naam van de Enterprise Application** en klik op Enkele **aanmelding**.

2. Bewerk op de pagina **Single Sign-On with SAM**de **basisSAML-configuratie**.

    ![BasisSAML-configuratie bewerken](./media/sharepoint-on-premises-tutorial/add-reply-url.png)

3. Voeg in **De URL van antwoord (BEVESTIGINGSconsumentenservice URL)** de URL voor de extra webtoepassingen toe en klik op **Opslaan**.

    ![BasisSAML-configuratie bewerken](./media/sharepoint-on-premises-tutorial/reply-url-for-web-application.png)

4. Open op de SharePoint-server de **SharePoint 201x Management Shell** en voer de volgende opdrachten uit met de naam van de vertrouwde uitgever van identiteitstoken die u eerder hebt gebruikt.
    ```
    $t = Get-SPTrustedIdentityTokenIssuer "AzureAD"
    $t.UseWReplyParameter=$true
    $t.Update()
    ```
5. Ga in Centraal beheer naar de webtoepassing en schakel de bestaande vertrouwde id-provider in.

U hebt mogelijk een ander scenario waarin u uw On-Premise Sharepoint toegang wilt geven aan uw interne gebruikers, voor dit scenario moet u Microsoft Azure Active Directory Connect implementeren waarmee uw On-Premise-gebruikers kunnen worden gesynchroniseerd met Azure Active Directory, deze instelling zou deel uitmaken van een ander artikel. 

## <a name="additional-resources"></a>Extra resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wat is hybride identiteit met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-hybrid-identity)
