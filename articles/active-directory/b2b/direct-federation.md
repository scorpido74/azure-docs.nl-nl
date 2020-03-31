---
title: Directe federatie met een identiteitsprovider voor B2B - Azure AD
description: Direct federatiemet een SAML- of WS-Fed-identiteitsprovider, zodat gasten zich kunnen aanmelden bij uw Azure AD-apps
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 02/27/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2b99a80a90df8fcfc5efe6dfa0c2cd7e8e5e04e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050886"
---
# <a name="direct-federation-with-ad-fs-and-third-party-providers-for-guest-users-preview"></a>Directe federatie met AD FS en externe providers voor gastgebruikers (preview)
|     |
| --- |
| Directe federatie is een openbare preview-functie van Azure Active Directory. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie over voorvertoningen.|
|     |

In dit artikel wordt beschreven hoe u directe federatie met een andere organisatie voor B2B-samenwerking instellen. U directe federatie instellen met elke organisatie waarvan de identiteitsprovider (IdP) het SAML 2.0- of WS-Fed-protocol ondersteunt.
Wanneer u een directe federatie met de IdP van een partner instelt, kunnen nieuwe gastgebruikers uit dat domein hun eigen door IdP beheerde organisatieaccount gebruiken om zich aan te melden bij uw Azure AD-tenant en met u samen te werken. De gastgebruiker hoeft geen apart Azure AD-account te maken.
> [!NOTE]
> Gastgebruikers van de directe federatie moeten zich aanmelden via `https://myapps.microsoft.com/?tenantid=<tenant id>` een `https://portal.azure.com/<tenant id>`koppeling die de tenantcontext bevat `https://myapps.microsoft.com/\<verified domain>.onmicrosoft.com`(bijvoorbeeld of in het geval van een geverifieerd domein). Directe koppelingen naar toepassingen en resources werken ook zolang ze de tenantcontext bevatten. Gebruikers van directe federatiekunnen zich momenteel niet aanmelden met algemene eindpunten die geen tenantcontext hebben. Bijvoorbeeld, met `https://myapps.microsoft.com` `https://portal.azure.com`behulp `https://teams.microsoft.com` van , , of zal resulteren in een fout.
 
## <a name="when-is-a-guest-user-authenticated-with-direct-federation"></a>Wanneer is een gastgebruiker geverifieerd met directe federatie?
Nadat u een directe federatie met een organisatie hebt ingesteld, worden alle nieuwe gastgebruikers die u uitnodigt, geverifieerd met behulp van directe federatie. Het is belangrijk op te merken dat het instellen van directe federatie de verificatiemethode niet wijzigt voor gastgebruikers die al een uitnodiging van u hebben ingewisseld. Hier volgen enkele voorbeelden:
 - Als gastgebruikers al uitnodigingen van u hebben ingewisseld en u vervolgens een directe federatie met hun organisatie hebt ingesteld, blijven die gastgebruikers dezelfde verificatiemethode gebruiken die ze hebben gebruikt voordat u een directe federatie instelt.
 - Als u een directe federatie met een partnerorganisatie instelt en gastgebruikers uitnodigt, en vervolgens de partnerorganisatie later naar Azure AD verhuist, blijven de gastgebruikers die al uitnodigingen hebben ingewisseld directe federatie gebruiken, zolang de directe federatiebeleid in uw tenant bestaat.
 - Als u de directe federatie met een partnerorganisatie verwijdert, kunnen gastgebruikers die momenteel de directe federatie gebruiken, zich niet aanmelden.

In een van deze scenario's u de verificatiemethode van een gastgebruiker bijwerken door het gastgebruikersaccount uit uw directory te verwijderen en deze opnieuw uit te nodigen.

Directe federatie is gekoppeld aan domeinnaamruimten, zoals contoso.com en fabrikam.com. Bij het opstellen van een directe federatieconfiguratie met AD FS of een idp van derden, koppelen organisaties een of meer domeinnaamruimten aan deze IdP's. 

## <a name="end-user-experience"></a>De ervaring voor de eindgebruiker 
Met directe federatie melden gastgebruikers zich aan bij uw Azure AD-tenant met hun eigen organisatieaccount. Wanneer ze toegang hebben tot gedeelde bronnen en worden gevraagd om zich aan te melden, worden directe federatiegebruikers doorgestuurd naar hun IdP. Na een succesvolle aanmelding worden ze teruggestuurd naar Azure AD om toegang te krijgen tot bronnen. De vernieuwingstokens van directe federatiegebruikers zijn 12 uur geldig, de [standaardlengte voor het doorsturen van vernieuwingstokens](../develop/active-directory-configurable-token-lifetimes.md#exceptions) in Azure AD. Als de gefedereerde IdP SSO heeft ingeschakeld, ervaart de gebruiker SSO en ziet hij geen aanmeldingsprompt na de eerste verificatie.

## <a name="limitations"></a>Beperkingen

### <a name="dns-verified-domains-in-azure-ad"></a>DNS-geverifieerde domeinen in Azure AD
Het domein waarmee u wilt vastzetten, mag ***niet*** worden geverifieerd in Azure AD. U directe federatie instellen met niet-beheerde (e-mailgeverifieerde of 'virale') Azure AD-tenants omdat ze niet dns-geverifieerd zijn.

### <a name="authentication-url"></a>URL voor verificatie
Directe federatie is alleen toegestaan voor beleid waarbij het domein van de verificatie-URL overeenkomt met het doeldomein of waarbij de verificatie-URL een van deze toegestane identiteitsproviders is (deze lijst kan worden gewijzigd):
-   accounts.google.com
-   pingidentity.com
-   login.pingone.com
-   okta.com
-   my.salesforce.com
-   federation.exostar.com
-   federation.exostartest.com

Wanneer u bijvoorbeeld directe federatie voor **fabrikam.com** `https://fabrikam.com/adfs` instelt, passeert de verificatie-URL de validatie. Een host in hetzelfde domein zal `https://sts.fabrikam.com/adfs`ook passeren, bijvoorbeeld . De url `https://fabrikamconglomerate.com/adfs` van `https://fabrikam.com.uk/adfs` de verificatie of voor hetzelfde domein wordt echter niet doorgegeven.

### <a name="signing-certificate-renewal"></a>Verlenging van het ondertekenen van certificaten
Als u de URL met ametjes opgeeft in de instellingen van de identiteitsprovider, verlengt Azure AD het ondertekeningscertificaat automatisch wanneer deze verloopt. Als het certificaat echter om welke reden dan ook wordt geroteerd vóór de vervaldatum of als u geen URL met metagegevens verstrekt, kan Azure AD het certificaat niet vernieuwen. In dit geval moet u het ondertekeningscertificaat handmatig bijwerken.

### <a name="limit-on-federation-relationships"></a>Limiet voor federatierelaties
Momenteel worden maximaal 1.000 federatierelaties ondersteund. Deze limiet omvat zowel [interne federaties](https://docs.microsoft.com/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0) als directe federaties.

### <a name="limit-on-multiple-domains"></a>Limiet voor meerdere domeinen
We ondersteunen momenteel geen directe federatie met meerdere domeinen van dezelfde tenant.

## <a name="frequently-asked-questions"></a>Veelgestelde vragen
### <a name="can-i-set-up-direct-federation-with-a-domain-for-which-an-unmanaged-email-verified-tenant-exists"></a>Kan ik een directe federatie instellen met een domein waarvoor een niet-beheerde (e-mail geverifieerde) tenant bestaat? 
Ja. Als het domein niet is geverifieerd en de tenant geen [beheerdersovername](../users-groups-roles/domains-admin-takeover.md)heeft ondergaan, u directe federatie met dat domein instellen. Niet-beheerde of e-mailgeverifieerde tenants worden gemaakt wanneer een gebruiker een B2B-uitnodiging inwisselt of een selfservice-aanmelding voor Azure AD uitvoert met een domein dat momenteel niet bestaat. U directe federatie instellen met deze domeinen. Als u probeert directe federatie in te stellen met een dns-geverifieerd domein, in de Azure-portal of via PowerShell, ziet u een fout.
### <a name="if-direct-federation-and-email-one-time-passcode-authentication-are-both-enabled-which-method-takes-precedence"></a>Als directe federatie en e-mail eenmalige wachtwoordverificatie beide zijn ingeschakeld, welke methode heeft dan voorrang?
Wanneer directe federatie is ingesteld met een partnerorganisatie, heeft deze voorrang op e-maileentoegangscodeverificatie voor nieuwe gastgebruikers van die organisatie. Als een gastgebruiker een uitnodiging heeft ingewisseld met eenmalige wachtwoordverificatie voordat u een directe federatie instelt, blijft deze eenmalige wachtwoordverificatie gebruiken. 
### <a name="does-direct-federation-address-sign-in-issues-due-to-a-partially-synced-tenancy"></a>Heeft directe federatie adres aanmeldingsproblemen als gevolg van een gedeeltelijk gesynchroniseerde huurovereenkomst?
Nee, de [one-time toegangscodefunctie voor e-mail](one-time-passcode.md) moet in dit scenario worden gebruikt. Een "gedeeltelijk gesynchroniseerde huurovereenkomst" verwijst naar een Azure AD-tenant van een partner waarbij on-premises gebruikersidentiteiten niet volledig worden gesynchroniseerd met de cloud. Een gast wiens identiteit nog niet bestaat in de cloud, maar die uw B2B-uitnodiging probeert in te wisselen, kan zich niet aanmelden. Met de eenmalige toegangscodefunctie kan deze gast zich aanmelden. De functie Directe federatie behandelt scenario's waarin de gast zijn eigen idp-beheerde organisatieaccount heeft, maar de organisatie helemaal geen Azure AD-aanwezigheid heeft.

## <a name="step-1-configure-the-partner-organizations-identity-provider"></a>Stap 1: De identiteitsprovider van de partnerorganisatie configureren
Ten eerste moet uw partnerorganisatie hun identiteitsprovider configureren met de vereiste claims en vertrouwensrelaties van de relying party. 

> [!NOTE]
> Als u wilt illustreren hoe u een identiteitsprovider voor directe federatie configureert, gebruiken we Bijvoorbeeld Active Directory Federation Services (AD FS). Zie het artikel [Directe federatie configureren met AD FS](direct-federation-adfs.md), waarin voorbeelden worden gegeven van het configureren van AD FS als een SAML 2.0- of WS-Fed-identiteitsprovider ter voorbereiding op directe federatie.

### <a name="saml-20-configuration"></a>SAML 2.0-configuratie

Azure AD B2B kan worden geconfigureerd om te reageren op identiteitsproviders die het SAML-protocol gebruiken met specifieke vereisten die hieronder worden vermeld. Zie [Een SAML 2.0-identiteitsprovider (IdP) gebruiken voor eenmalige aanmelding voor](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-fed-saml-idp)meer informatie over het instellen van een vertrouwensrelatie tussen uw SAML-identiteitsprovider en Azure AD.  

> [!NOTE]
> Het doeldomein voor directe federatie mag niet worden geverifieerd op Azure AD. Het URL-domein voor verificatie moet overeenkomen met het doeldomein of het moet het domein van een toegestane identiteitsprovider zijn. Zie de sectie [Beperkingen](#limitations) voor meer informatie. 

#### <a name="required-saml-20-attributes-and-claims"></a>Vereiste SAML 2.0-kenmerken en -claims
In de volgende tabellen worden vereisten weergegeven voor specifieke kenmerken en claims die moeten worden geconfigureerd bij de externe identiteitsprovider. Als u een directe federatie wilt instellen, moeten de volgende kenmerken worden ontvangen in het SAML 2.0-antwoord van de identiteitsprovider. Deze kenmerken kunnen worden geconfigureerd door te linken naar het XML-bestand voor online beveiligingstokenservices of door ze handmatig in te voeren.

Vereiste kenmerken voor het SAML 2.0-antwoord van de IdP:

|Kenmerk  |Waarde  |
|---------|---------|
|BeweringConsumerService     |`https://login.microsoftonline.com/login.srf`         |
|Doelgroep     |`urn:federation:MicrosoftOnline`         |
|Verlener     |De uitgever URI van de partner IdP, bijvoorbeeld`http://www.example.com/exk10l6w90DHM0yi...`         |


Vereiste claims voor het SAML 2.0-token uitgegeven door de IdP:

|Kenmerk  |Waarde  |
|---------|---------|
|NameID-indeling     |`urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

### <a name="ws-fed-configuration"></a>WS-Fed-configuratie 
Azure AD B2B kan worden geconfigureerd om te reageren op identiteitsproviders die het WS-Fed-protocol gebruiken met een aantal specifieke vereisten zoals hieronder vermeld. Momenteel zijn de twee WS-Fed-providers getest op compatibiliteit met Azure AD, waaronder AD FS en Shibboleth. Zie het 'STS-integratiedocument met WS-protocollen' dat beschikbaar is in de Azure [AD Identity Provider Compatibility Docs](https://www.microsoft.com/download/details.aspx?id=56843)voor meer informatie over het instellen van een vertrouwensrelatie tussen een WS-Fed-compatibele provider met Azure AD.

> [!NOTE]
> Het doeldomein voor directe federatie mag niet worden geverifieerd op Azure AD. Het URL-domein voor verificatie moet overeenkomen met het doeldomein of het domein van een toegestane identiteitsprovider. Zie de sectie [Beperkingen](#limitations) voor meer informatie. 

#### <a name="required-ws-fed-attributes-and-claims"></a>Vereiste WS-Fed attributen en claims

In de volgende tabellen worden vereisten weergegeven voor specifieke kenmerken en claims die moeten worden geconfigureerd bij de ws-fed-identiteitsprovider van derden. Als u een directe federatie wilt instellen, moeten de volgende kenmerken worden ontvangen in het WS-Fed-bericht van de identiteitsprovider. Deze kenmerken kunnen worden geconfigureerd door te linken naar het XML-bestand voor online beveiligingstokenservices of door ze handmatig in te voeren.

Vereiste kenmerken in het WS-Fed-bericht van de IdP:
 
|Kenmerk  |Waarde  |
|---------|---------|
|PassiefRequestorEndpoint     |`https://login.microsoftonline.com/login.srf`         |
|Doelgroep     |`urn:federation:MicrosoftOnline`         |
|Verlener     |De uitgever URI van de partner IdP, bijvoorbeeld`http://www.example.com/exk10l6w90DHM0yi...`         |

Vereiste claims voor het WS-Fed-token uitgegeven door de IdP:

|Kenmerk  |Waarde  |
|---------|---------|
|Onveranderlijke ID     |`http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

## <a name="step-2-configure-direct-federation-in-azure-ad"></a>Stap 2: Directe federatie configureren in Azure AD 
Vervolgens configureert u de federatie met de identiteitsprovider die is geconfigureerd in stap 1 in Azure AD. U de Azure AD-portal of PowerShell gebruiken. Het kan 5-10 minuten duren voordat het directe federatiebeleid van kracht wordt. Probeer gedurende deze periode geen uitnodiging voor het directe federatiedomein in te wisselen. De volgende kenmerken zijn vereist:
- Uitgever URI van partner IdP
- Passief verificatieeindpunt van partner IdP (alleen https wordt ondersteund)
- Certificaat

### <a name="to-configure-direct-federation-in-the-azure-ad-portal"></a>Directe federatie configureren in de Azure AD-portal

1. Ga naar de [Azure-portal.](https://portal.azure.com/) Selecteer de knop **Azure Active Directory** in het linkerdeelvenster. 
2. Selecteer **Organisatierelaties**.
3. Selecteer **Identiteitsproviders**en selecteer **Vervolgens Nieuwe SAML/WS-Fed IdP**.

    ![Schermafbeelding van de knop voor het toevoegen van een nieuwe SAML of WS-Fed IdP](media/direct-federation/new-saml-wsfed-idp.png)

4. Selecteer **SAML** of **WS-FED**IdP op de nieuwe **SAML/WS-Fed IdP-pagina** onder **het protocol van de identiteitsprovider**.

    ![Schermafbeelding van de parse-knop op de SAML- of WS-Fed IdP-pagina](media/direct-federation/new-saml-wsfed-idp-parse.png)

5. Voer de domeinnaam van uw partnerorganisatie in, die de doeldomeinnaam voor directe federatie zal zijn
6. U een metagegevensbestand uploaden om metagegevens in te vullen. Als u ervoor kiest om metagegevens handmatig in te voeren, voert u de volgende gegevens in:
   - Domeinnaam van partner IdP
   - Entiteits-id van partner IdP
   - Passief requestor eindpunt van partner IdP
   - Certificaat
   > [!NOTE]
   > Metadata URL is optioneel, maar we raden het ten zeerste aan. Als u de URL met metagegevens opgeeft, kan Azure AD het ondertekeningscertificaat automatisch verlengen wanneer het verloopt. Als het certificaat om welke reden dan ook wordt geroteerd vóór de vervaldatum of als u geen URL met metagegevens opgeeft, kan Azure AD het niet verlengen. In dit geval moet u het ondertekeningscertificaat handmatig bijwerken.

7. Selecteer **Opslaan**. 

### <a name="to-configure-direct-federation-in-azure-ad-using-powershell"></a>Directe federatie configureren in Azure AD met PowerShell

1. Installeer de nieuwste versie van de Azure AD PowerShell voor Grafiekmodule[(AzureADPreview).](https://www.powershellgallery.com/packages/AzureADPreview) (Als u gedetailleerde stappen nodig hebt, bevat de snelle start voor het toevoegen van een gastgebruiker de sectie [Installeer de nieuwste AzureADPreview-module](b2b-quickstart-invite-powershell.md#install-the-latest-azureadpreview-module).) 
2. Voer de volgende opdracht uit: 
   ```powershell
   Connect-AzureAD
   ```
1. Meld u bij de aanmeldingsprompt aan met het beheerde account van global administrator. 
2. Voer de volgende opdrachten uit en vervang de waarden uit het bestand met metagegevens van de federatie. Voor AD FS Server en Okta is het federatiebestand `https://sts.totheclouddemo.com/federationmetadata/2007-06/federationmetadata.xml`federationmetadata.xml, bijvoorbeeld: . 

   ```powershell
   $federationSettings = New-Object Microsoft.Open.AzureAD.Model.DomainFederationSettings
   $federationSettings.PassiveLogOnUri ="https://sts.totheclouddemo.com/adfs/ls/"
   $federationSettings.LogOffUri = $federationSettings.PassiveLogOnUri
   $federationSettings.IssuerUri = "http://sts.totheclouddemo.com/adfs/services/trust"
   $federationSettings.MetadataExchangeUri="https://sts.totheclouddemo.com/adfs/services/trust/mex"
   $federationSettings.SigningCertificate= <Replace with X509 signing cert’s public key>
   $federationSettings.PreferredAuthenticationProtocol="WsFed" OR "Samlp"
   $domainName = <Replace with domain name>
   New-AzureADExternalDomainFederation -ExternalDomainName $domainName  -FederationSettings $federationSettings
   ```

## <a name="step-3-test-direct-federation-in-azure-ad"></a>Stap 3: Directe federatie testen in Azure AD
Test nu uw directe federatie-setup door een nieuwe B2B-gastgebruiker uit te nodigen. Zie Azure [AD B2B-samenwerkingsgebruikers toevoegen in de Azure-portal](add-users-administrator.md)voor meer informatie.
 
## <a name="how-do-i-edit-a-direct-federation-relationship"></a>Hoe kan ik een directe federatierelatie bewerken?

1. Ga naar de [Azure-portal.](https://portal.azure.com/) Selecteer de knop **Azure Active Directory** in het linkerdeelvenster. 
2. Selecteer **Organisatierelaties**.
3. Identiteitsproviders **selecteren**
4. Selecteer onder **SAML/WS-Fed identiteitsproviders**de provider.
5. Werk in het detailvenster van de identiteitsprovider de waarden bij.
6. Selecteer **Opslaan**.


## <a name="how-do-i-remove-direct-federation"></a>Hoe verwijder ik directe federatie?
U uw directe federatie-instelling verwijderen. Als u dit wel doet, kunnen directe federatiegastgebruikers die hun uitnodigingen al hebben ingewisseld, zich niet aanmelden. Maar u ze weer toegang geven tot uw bronnen door ze uit de map te verwijderen en ze opnieuw uit te nodigen. Ga als eerste over een koppeling met een identiteitsprovider in de Azure AD-portal:

1. Ga naar de [Azure-portal.](https://portal.azure.com/) Selecteer de knop **Azure Active Directory** in het linkerdeelvenster. 
2. Selecteer **Organisatierelaties**.
3. Selecteer **identiteitsproviders**.
4. Selecteer de identiteitsprovider en selecteer **Verwijderen**. 
5. Selecteer **Ja** om verwijdering te bevestigen. 

Ga als een te meer met ingetoed door de directe federatie met een identiteitsprovider te verwijderen door PowerShell te gebruiken:
1. Installeer de nieuwste versie van de Azure AD PowerShell voor Grafiekmodule[(AzureADPreview).](https://www.powershellgallery.com/packages/AzureADPreview)
2. Voer de volgende opdracht uit: 
   ```powershell
   Connect-AzureAD
   ```
3. Meld u bij de aanmeldingsprompt aan met het beheerde account van global administrator. 
4. Voer de volgende opdracht in:
   ```powershell
   Remove-AzureADExternalDomainFederation -ExternalDomainName  $domainName
   ```
