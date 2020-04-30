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
ms.topic: tutorial
ms.date: 01/15/2020
ms.author: miguego
ms.openlocfilehash: 7e47891a74feb60b0f3e4594bd1621e8b62d64d1
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81867101"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sharepoint-on-premises"></a>Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met share point on-premises

In deze zelf studie leert u hoe u share point on-premises integreert met Azure Active Directory (Azure AD). Wanneer u share point on-premises integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die on-premises toegang heeft tot share point.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij share point on-premises met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

Als u Azure Active Directory integratie met share point on-premises wilt configureren, hebt u de volgende items nodig:

* Een Azure Active Directory-abonnement. Als u geen Azure AD-omgeving hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) aanvragen
* Een share point 2013-Farm of nieuwer.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure Active Directory eenmalige aanmelding in een test omgeving. Gebruikers van Azure Active Directory hebben on-premises toegang tot uw share point.

## <a name="create-the-enterprise-applications-in-azure-portal"></a>De Enter prise-toepassingen maken in Azure Portal

Voor het configureren van de integratie van SharePoint on-premises in Azure AD, moet u SharePoint on-premises uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

Voer de volgende stappen uit om SharePoint on-premises uit de galerie toe te voegen:

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

 > [!NOTE]
 > Als het element niet beschikbaar moet zijn, kan het ook worden geopend via de koppeling vaste **alle services** bovenin het navigatie paneel aan de linkerkant. In het volgende overzicht bevindt de **Azure Active Directory** koppeling zich in het gedeelte **identiteit** of kan er worden gezocht met behulp van het tekstvak filter.

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

4. Typ **share point on-premises**in het zoekvak, selecteer **share point on-premises** uit het paneel resultaten.

    <kbd>![SharePoint on-premises in de lijst met resultaten](./media/sharepoint-on-premises-tutorial/search-new-app.png)</kbd>

1. Geef een naam op voor uw share point-premises en klik op de knop **toevoegen** om de toepassing toe te voegen.

1. Klik in de nieuwe bedrijfs toepassing op Eigenschappen en controleer de waarde voor **gebruikers toewijzing vereist**

   <kbd>![SharePoint on-premises in de lijst met resultaten](./media/sharepoint-on-premises-tutorial/user-assignment-required.png)</kbd>

in ons scenario is deze waarde ingesteld op **Nee**.

## <a name="configure-and-test-azure-ad"></a>Azure AD configureren en testen

In deze sectie configureert u eenmalige aanmelding voor Azure AD met share point on-premises.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in SharePoint on-premises tot stand is gebracht.

Om Azure Active Directory eenmalige aanmelding te configureren en te testen met share point on-premises, moet u de volgende bouw stenen volt ooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
1. **[Share point on-premises configureren](#configure-sharepoint-on-premises)** : Hiermee configureert u de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
1. **[Maak een Azure AD-test gebruiker in het Azure Portal](#create-an-azure-ad-test-user-in-the-azure-portal)** -Maak een nieuwe gebruiker in azure AD voor eenmalige aanmelding.
1. **[Maak een Azure AD-beveiligings groep in de Azure Portal](#create-an-azure-ad-security-group-in-the-azure-portal)** -nieuwe beveiligings groep maken in azure AD voor eenmalige aanmelding.
1. **[Machtigingen verlenen voor Azure Active Directory-account in share point on-premises](#grant-permissions-to-azure-active-directory-account-in-sharepoint-on-premises)** -machtigingen verlenen aan de gebruiker van Azure AD.
1. **[Machtigingen verlenen aan de Azure AD-groep in share point on-premises](#grant-permissions-to-azure-ad-group-in-sharepoint-on-premises)** -machtigingen verlenen aan de Azure AD-groep.
1. **[Toegang verlenen tot een gast account voor on-premises share point in de Azure Portal](#grant-access-to-a-guest-account-to-sharepoint-on-premises-in-the-azure-portal)** -machtigingen verlenen aan gast account in azure AD voor share point on-premises.
1. **[De vertrouwde id-provider voor meerdere webtoepassingen configureren](#configuring-the-trusted-identity-provider-for-multiple-web-applications)** : gebruik dezelfde provider voor vertrouwde id's voor meerdere webtoepassingen

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voor de configuratie van eenmalige Azure AD-aanmelding met SharePoint on-premises, moet u de volgende stappen uitvoeren:

1. Open de Azure AD-Directory in de [Azure Portal](https://portal.azure.com/), klik op **bedrijfs toepassingen**, klik op de **eerder gemaakte naam van de bedrijfs toepassing** en klik op **eenmalige aanmelding**.

2. Klik in het dialoog venster **eenmalige aanmelding selecteren** op de **SAML** -modus om eenmalige aanmelding in te scha kelen.
 
3. Klik op het pictogram **bewerken** in het dialoog venster een **basis configuratie** openen voor het configureren van een **eenmalige aanmelding met de SAML-** pagina.

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Informatie over eenmalige aanmelding bij het SharePoint on-premises-domein en -URL's](./media/sharepoint-on-premises-tutorial/sp-identifier-reply.png)

    1. In het tekstvak **Id** typt u een URL met het volgende patroon: `urn:<sharepointFarmName>:<federationName>`

    1. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<YourSharePointSiteURL>/_trust/`

    1. Typ in het tekstvak **URL voor aanmelding** een URL met het volgende patroon:`https://<YourSharePointSiteURL>/`
    1. Klik op opslaan.

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id, de antwoord-URL en de aanmeldings-URL.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](./media/sharepoint-on-premises-tutorial/certificatebase64.png)

6. Kopieer in de sectie **SharePoint on-premises instellen** de juiste URL('s) overeenkomstig wat u nodig hebt.
    
    1. **Aanmeldings-URL**
    
        De aanmeldings-URL kopiëren Vervang **/saml2** aan het einde met **/wsfed**. Dit zou **https://login.microsoftonline.com/2c4f1a9f-be5f-10ee-327d-a95dac567e4f/wsfed** er als volgt uitzien (deze URL is niet nauw keurig)

    1. **Azure AD-id**
    1. **Afmeldings-URL**
    > [!NOTE]
    > Deze URL kan niet worden gebruikt als bevindt zich in share point: u moet **/saml2** vervangen door **/wsfed**. De SharePoint on-premises-toepassing maakt gebruik van een SAML 1.1-token, zodat Azure AD een WS Fed-aanvraag verwacht van de SharePoint-server en na verificatie het SAML 1.1-token uitgeeft.

### <a name="configure-sharepoint-on-premises"></a>Share point on-premises configureren

1. **Een nieuwe vertrouwde id-provider maken in share Point server 2016**

    Meld u aan bij share Point server en open de share point-beheer shell. Vul de waarden in:
    1. **$realm** (id-waarde van de sectie share point on-premises domein en url's in de Azure Portal).
    1. **$wsfedurl** (URL voor eenmalige aanmelding-service).
   1. **$filepath** (bestandspad waarnaar u het certificaat bestand hebt gedownload) van Azure Portal.

    Voer de volgende opdrachten uit om een nieuwe vertrouwde id-provider te configureren.

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
1. **De vertrouwde id-provider voor uw toepassing inschakelen**

    a. Navigeer in Centraal beheer naar **Webtoepassing beheren** en selecteer de webtoepassing die u wilt beveiligen met Azure AD.

    b. Klik in het lint op **Verificatieproviders** en kies de zone die u wilt gebruiken.

    c. Selecteer **Vertrouwde id-provider** en selecteer de id-provider die u zojuist hebt geregistreerd met de naam *AzureAD*.

    d. Klik op **OK**.

    ![Uw verificatieprovider configureren](./media/sharepoint-on-premises-tutorial/config-auth-provider.png)

### <a name="create-an-azure-ad-test-user-in-the-azure-portal"></a>Een Azure AD-test gebruiker maken in de Azure Portal

Het doel van deze sectie is het maken van een test gebruiker in het Azure Portal.

1. Selecteer in de Azure Portal in het linkerdeel venster **Azure Active Directory** **in pan-** **gebruikers**selecteren.

2. Selecteer vervolgens **alle gebruikers** , gevolgd door **nieuwe gebruiker** boven aan het scherm.

3. Selecteer de optie **gebruiker maken** en in de gebruikers eigenschappen de volgende stappen uit.  
   U kunt mogelijk gebruikers in uw Azure AD maken met behulp van uw Tenant achtervoegsel of een geverifieerd domein. 

    a. Voer in het veld **naam** de gebruikers naam in, wij hebben gebruik van **test User**.
  
    b. In het veld **Gebruikersnaam** typt u `TestUser@yourcompanydomain.extension`.  
    Bijvoorbeeld: TestUser@contoso.com

    ![Het dialoogvenster Gebruiker](./media/sharepoint-on-premises-tutorial/user-properties.png)

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik op **maken**.

    e. U kunt de site nu delen met TestUser@contoso.com en toestaan dat deze gebruiker er toegang toe heeft.

### <a name="create-an-azure-ad-security-group-in-the-azure-portal"></a>Een Azure AD-beveiligings groep maken in de Azure Portal

1. Klik op **Azure Active Directory > groepen**.

2. Klik op **nieuwe groep**:

3. **Groeps type**, **groeps naam**, **groeps beschrijving**, **type lidmaatschap**. Klik op de pijl om leden te selecteren, zoek vervolgens naar of klik op het lid dat u aan de groep wilt toevoegen. Klik op **selecteren** om de geselecteerde leden toe te voegen en klik vervolgens op **maken**.

![Een Azure AD-beveiligings groep maken](./media/sharepoint-on-premises-tutorial/new-group.png)

### <a name="grant-permissions-to-azure-active-directory-account-in-sharepoint-on-premises"></a>Machtigingen verlenen voor het Azure Active Directory-account in share point on-premises

Als u toegang wilt verlenen aan de Azure Active Directory gebruiker in de on-premises share point, moet u de site verzameling delen of de Azure Active Directory gebruiker toevoegen aan een van de groep van de site verzameling. Gebruikers kunnen zich nu aanmelden bij share point 201x met behulp van identiteiten vanuit Azure AD, maar er zijn nog steeds mogelijkheden voor verbetering van de gebruikers ervaring. Wanneer u bijvoorbeeld zoekt naar een gebruiker, worden meerdere zoekresultaten weergegeven in de personenkiezer. Er is een Zoek resultaat voor elk van de claim typen die zijn gemaakt in de claim toewijzing. Als u een gebruiker wilt kiezen met de personenkiezer, moet u de gebruikersnaam exact typen en het resultaat van de **naam**claim kiezen.

![Zoekresultaten van claims](./media/sharepoint-on-premises-tutorial/claims-search-results.png)

Er is geen validatie voor de waarden die u zoekt, wat kan leiden tot spel fouten of gebruikers hebben per ongeluk het verkeerde claim type gekozen. Hierdoor hebben gebruikers mogelijk geen toegang tot resources.

**Voor het oplossen van de personen kiezer** met dit scenario is er een open-source oplossing met de naam [AzureCP](https://yvand.github.io/AzureCP/) die een aangepaste claim provider biedt voor share point 2013, 2016 en 2019. De Microsoft Graph-API wordt gebruikt om te achterhalen wat gebruikers invoeren en validatie uitvoeren. U vindt hierover meer informatie op [AzureCP](https://yvand.github.io/AzureCP/).

  > [!NOTE]
  > Zonder AzureCP kunt u groepen toevoegen door de ID van de Azure AD-groep toe te voegen, maar dit is niet de gebruiks vriendelijke en betrouw bare. U ziet het volgende:  
  >   
  >![Azure AD-groep toevoegen aan share point-groep](./media/sharepoint-on-premises-tutorial/adding-group-by-id.png)
  
### <a name="grant-permissions-to-azure-ad-group-in-sharepoint-on-premises"></a>Machtigingen verlenen aan een Azure AD-groep in share point on-premises

Als u Azure Active Directory beveiligings groepen on-premises wilt toewijzen aan share point, moet u een aangepaste claim provider voor share Point server gebruiken. In ons voor beeld hebben we AzureCP gebruikt.

 > [!NOTE]
 > Houd er rekening mee dat AzureCP geen micro soft-product is of niet wordt ondersteund door de technische ondersteuning van micro soft. Down load, installeer en configureer AzureCP op de on-premises share https://yvand.github.io/AzureCP/Point-Farm per. 

1. Configureer de AzureCP op de share point on-premises-Farm of een alternatieve oplossing voor aangepaste claim providers. de stappen voor de configuratie van AzureCP zijn beschikbaar ophttps://yvand.github.io/AzureCP/Register-App-In-AAD.html

1. Open de Azure AD-Directory in de Azure Portal. Klik op **bedrijfs toepassingen**, klik op de **eerder gemaakte bedrijfs toepassings naam** en klik op **eenmalige aanmelding**.

1. Bewerk op de pagina **eenmalige aanmelding met Sam**de **gebruikers kenmerken & sectie claims** .

1. Klik op **een groepclaim toevoegen**.

1. Selecteer welke groepen die aan de gebruiker zijn gekoppeld, moeten worden geretourneerd in de claim. in ons geval selecteert u **alle groepen** en in de sectie Bron kenmerk selecteert u **groeps-id** en klikt u op **Opslaan**.

Als u toegang wilt verlenen tot de Azure Active Directory beveiligings groep in de on-premises share point, moet u de site verzameling delen of de Azure Active Directory-beveiligings groep toevoegen aan een van de groepen van de site verzameling.

1. Blader naar de share point-site verzameling onder site-instellingen voor de site verzameling en klik op personen en groepen. Selecteer de share point-groep en klik vervolgens op nieuw, gebruikers aan deze groep toevoegen en begin met het typen van de naam van de groep die door de persoons kiezer wordt weer gegeven de Azure Active Directory beveiligings groep.

    ![Azure AD-groep toevoegen aan share point-groep](./media/sharepoint-on-premises-tutorial/permission-azure-ad-group.png)

### <a name="grant-access-to-a-guest-account-to-sharepoint-on-premises-in-the-azure-portal"></a>Toegang verlenen tot een gast account voor on-premises share point in de Azure Portal

het is nu mogelijk om toegang tot uw share point-site op een consistente manier te verlenen aan een gast account. De UPN wordt gewijzigd. Gebruiker met jdoe@outlook.com wordt weer gegeven als `jdoe_outlook.com#ext#@TENANT.onmicrosoft.com`. Het is mogelijk om een naadloze ervaring te krijgen tijdens het delen van uw site met externe gebruikers. het is daarom nood zakelijk om enkele wijzigingen toe te voegen aan de sectie kenmerken van uw **gebruikers & claims** in de Azure Portal.

1. Open de Azure AD-Directory in de Azure Portal. Klik op **bedrijfs toepassingen**, klik op de **eerder gemaakte bedrijfs toepassings naam** en klik op **eenmalige aanmelding**.

1. Bewerk op de pagina **eenmalige aanmelding met Sam**de **gebruikers kenmerken & sectie claims** .

1. Klik in de **vereiste claim** zone op **unieke gebruikers-ID (naam-id)**.

1. Wijzig de eigenschap **bron kenmerk** in de waarde **User. localuserprincipalname** en **Sla**het bestand op.

    ![Gebruikers kenmerken & eerste claims](./media/sharepoint-on-premises-tutorial/manage-claim.png)

1. Het lint gebruiken ga terug naar op **SAML gebaseerde aanmelding** nu de **gebruikers kenmerken & claim** sectie er als volgt uitzien: 

    ![Gebruikers kenmerken & definitief claims](./media/sharepoint-on-premises-tutorial/user-attributes-claims-final.png)  

    > [!NOTE]
    > Voor de achternaam en de voor waarde zijn niet vereist in deze installatie.

1. Selecteer in de Azure Portal in het linkerdeel venster **Azure Active Directory**de optie **gebruikers**.

1. Klik op **nieuwe gast gebruiker**

1. Selecteer de optie **gebruiker uitnodigen** en vul de eigenschappen van de gebruiker in en klik op **uitnodigen**.

1. U kunt de site nu delen met MyGuestAccount@outlook.com en toestaan dat deze gebruiker er toegang toe heeft.

    ![Site delen met een gast account](./media/sharepoint-on-premises-tutorial/sharing-guest-account.png)

### <a name="configuring-the-trusted-identity-provider-for-multiple-web-applications"></a>De vertrouwde id-provider voor meerdere webtoepassingen configureren

De configuratie werkt voor één webtoepassing. Als u dezelfde vertrouwde id-provider wilt gebruiken voor meerdere webtoepassingen, moet u aanvullende configuratiestappen uitvoeren. Stel, u hebt een webtoepassing uitgebreid voor gebruik van de URL `https://sales.contoso.com` en u wilt de gebruikers ook verifiëren voor `https://marketing.contoso.com`. Hiervoor moet de id-provider worden bijgewerkt om te voldoen aan de WReply-parameter en moet de toepassingsregistratie in Azure AD worden bijgewerkt om een antwoord-URL toe te voegen.

1. Open de Azure AD-Directory in de Azure Portal. Klik op **bedrijfs toepassingen**, klik op de **eerder gemaakte bedrijfs toepassings naam** en klik op **eenmalige aanmelding**.

2. Bewerk op de pagina **eenmalige aanmelding met Sam**de **elementaire SAML-configuratie**.

    ![Eenvoudige SAML-configuratie bewerken](./media/sharepoint-on-premises-tutorial/add-reply-url.png)

3. Voeg in **antwoord-URL (assertion Consumer Service-URL)** de URL voor de extra webtoepassingen toe en klik op **Opslaan**.

    ![Eenvoudige SAML-configuratie bewerken](./media/sharepoint-on-premises-tutorial/reply-url-for-web-application.png)

4. Open de **share point 201X-beheer shell** op de share Point-server en voer de volgende opdrachten uit met behulp van de naam van de uitgever van het vertrouwde identiteits token dat u eerder hebt gebruikt.
    ```
    $t = Get-SPTrustedIdentityTokenIssuer "AzureAD"
    $t.UseWReplyParameter=$true
    $t.Update()
    ```
5. Ga in Centraal beheer naar de webtoepassing en schakel de bestaande vertrouwde id-provider in.

U hebt mogelijk een ander scenario waarbij u toegang wilt verlenen tot uw on-premises share point voor uw interne gebruikers, voor dit scenario moet u Microsoft Azure Active Directory Connect implementeren waarmee u uw lokale gebruikers kunt synchroniseren met Azure Active Directory. deze installatie maakt deel uit van een ander artikel. 

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wat is een hybride identiteit met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-hybrid-identity)
