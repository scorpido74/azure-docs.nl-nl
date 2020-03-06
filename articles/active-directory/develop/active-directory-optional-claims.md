---
title: Optionele claims voor Azure AD-apps bieden | Azure
titleSuffix: Microsoft identity platform
description: Aangepaste of extra claims toevoegen aan de SAML-tokens (2,0) en JSON Web tokens (JWT) die zijn uitgegeven door Azure Active Directory.
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 12/08/2019
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin, keyam
ms.custom: aaddev
ms.openlocfilehash: 9ea3388cb65b18c093ffff3ec8b8c9f2764ef189
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78300065"
---
# <a name="how-to-provide-optional-claims-to-your-azure-ad-app"></a>Procedure: optionele claims voor uw Azure AD-App opgeven

Ontwikkel aars van toepassingen kunnen optionele claims in hun Azure AD-toepassingen gebruiken om op te geven welke claims ze willen in tokens die naar hun toepassing worden verzonden. 

U kunt optionele claims gebruiken voor het volgende:

- Selecteer extra claims die moeten worden toegevoegd aan de tokens voor uw toepassing.
- Wijzig het gedrag van bepaalde claims die Azure AD retourneert in tokens.
- Aangepaste claims toevoegen en openen voor uw toepassing.

Zie de documentatie voor [toegangs tokens](access-tokens.md) en [id_token](id-tokens.md) claims voor de lijsten met standaard claims. 

Hoewel optionele claims worden ondersteund in de indelings tokens v 1.0 en v 2.0, en op SAML-tokens, bieden ze de meeste waarde bij het overstappen van v 1.0 naar v 2.0. Een van de doel stellingen van het [micro soft Identity platform-eind punt van de v 2.0](active-directory-appmodel-v2-overview.md) is kleiner dan de grootte van tokens om optimale prestaties door clients te garanderen. Als gevolg hiervan zijn enkele claims voorheen opgenomen in de toegangs-en ID-tokens niet meer aanwezig in de v 2.0-tokens en moeten ze specifiek worden gevraagd per toepassing.

**Tabel 1: toepasselijkheid**

| Account type | v 1.0-tokens | v 2.0-tokens  |
|--------------|---------------|----------------|
| Persoonlijke Microsoft-account  | N.v.t.  | Ondersteund |
| Azure AD-account      | Ondersteund | Ondersteund |

## <a name="v10-and-v20-optional-claims-set"></a>optionele claim sets v 1.0 en v 2.0

Hieronder vindt u de set optionele claims die standaard beschikbaar zijn voor het gebruik van toepassingen. Zie [Directory-extensies](#configuring-directory-extension-optional-claims)hieronder om aangepaste optionele claims voor uw toepassing toe te voegen. Wanneer claims aan het **toegangs token**worden toegevoegd, zijn de claims van toepassing op toegangs tokens die zijn aangevraagd *voor* de toepassing (een web-API) en niet claims die worden aangevraagd *door* de toepassing. Ongeacht hoe de client toegang heeft tot uw API, worden de juiste gegevens weer gegeven in het toegangs token dat wordt gebruikt voor verificatie op basis van uw API.

> [!NOTE]
> De meeste van deze claims kunnen worden opgenomen in JWTs voor de tokens v 1.0 en v 2.0, maar niet voor SAML-tokens, tenzij anders vermeld in de kolom token type. Consumenten accounts ondersteunen een subset van deze claims, die zijn gemarkeerd in de kolom ' gebruiker type '.  Veel van de vermelde claims zijn niet van toepassing op gebruikers van consumenten (ze hebben geen Tenant, dus `tenant_ctry` heeft geen waarde).  

**Tabel 2: v 1.0 en v 2.0 optionele claim sets**

| Naam                       |  Beschrijving   | Token type | Gebruikers type | Opmerkingen  |
|----------------------------|----------------|------------|-----------|--------|
| `auth_time`                | Tijdstip waarop de laatste verificatie van de gebruiker is gestart. Zie OpenID Connect Connect spec.| JWT        |           |  |
| `tenant_region_scope`      | De regio van de resource-Tenant | JWT        |           | |
| `home_oid`                 | Voor gast gebruikers, de object-ID van de gebruiker in de thuis Tenant van de gebruiker.| JWT        |           | |
| `sid`                      | Sessie-ID, die wordt gebruikt voor de afmelding per sessie van de gebruiker. | JWT        |  Persoonlijke en Azure AD-accounts.   |         |
| `platf`                    | Platform apparaat    | JWT        |           | Beperkt tot beheerde apparaten die het apparaattype kunnen controleren.|
| `verified_primary_email`   | Bron van de PrimaryAuthoritativeEmail van de gebruiker      | JWT        |           |         |
| `verified_secondary_email` | Bron van de SecondaryAuthoritativeEmail van de gebruiker   | JWT        |           |        |
| `enfpolids`                | Afgedwongen beleids-Id's. Een lijst met de beleids-Id's die voor de huidige gebruiker zijn geëvalueerd. | JWT |  |  |
| `vnet`                     | Gegevens van VNET-specificatie. | JWT        |           |      |
| `fwd`                      | IP-adres.| JWT    |   | Hiermee wordt het oorspronkelijke IPv4-adres van de aanvragende client toegevoegd (in een VNET) |
| `ctry`                     | Land van gebruiker | JWT |  | Azure AD retourneert de `ctry` optionele claim als deze aanwezig is en de waarde van de claim is een standaard land code van twee letters, zoals FR, JP, SZ, enzovoort. |
| `tenant_ctry`              | Land van de resource Tenant | JWT | | |
| `xms_pdl`          | Voorkeurs locatie van gegevens   | JWT | | Voor multi-geo-tenants is de gewenste gegevens locatie de code van drie letters waarin de geografische regio waarin de gebruiker zich bevindt. Zie de [Azure AD Connect documentatie over de voorkeurs locatie van gegevens](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-feature-preferreddatalocation)voor meer informatie.<br/>Bijvoorbeeld: `APC` voor Azië en Stille Oceaan. |
| `xms_pl`                   | Voorkeurs taal van gebruiker  | JWT ||De voorkeurs taal van de gebruiker, indien ingesteld. Vanuit hun thuis Tenant, in scenario's voor toegang tot de gast. Geformatteerd LL-CC ("en-US"). |
| `xms_tpl`                  | Voorkeurs taal van Tenant| JWT | | De voorkeurs taal van de resource-Tenant, indien ingesteld. Opgemaakte LL ("en"). |
| `ztdid`                    | Nul-touch implementatie-ID | JWT | | De apparaat-id die wordt gebruikt voor [Windows auto pilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-10-autopilot) |
| `email`                    | De adresseer bare e-mail voor deze gebruiker als de gebruiker er een heeft.  | JWT, SAML | MSA, Azure AD | Deze waarde is standaard opgenomen als de gebruiker een gast in de Tenant is.  Voor beheerde gebruikers (de gebruikers in de Tenant) moet deze worden aangevraagd via deze optionele claim of, alleen op v 2.0, met het OpenID Connect-bereik.  Voor beheerde gebruikers moet het e-mail adres worden ingesteld in de [Office-beheer Portal](https://portal.office.com/adminportal/home#/users).| 
| `groups`| Optionele notatie voor groepclaims |JWT, SAML| |Wordt gebruikt in combi natie met de instelling GroupMembershipClaims in het manifest van de [toepassing](reference-app-manifest.md), die ook moet worden ingesteld. Zie voor meer informatie [groeps claims](#configuring-groups-optional-claims) hieronder. Zie groepclaims [configureren](../hybrid/how-to-connect-fed-group-claims.md) voor meer informatie over groepclaims.
| `acct`             | Status van gebruikers account in Tenant. | JWT, SAML | | Als de gebruiker lid is van de Tenant, is de waarde `0`. Als ze een gast zijn, is de waarde `1`. |
| `upn`                      | UserPrincipalName claim. | JWT, SAML  |           | Hoewel deze claim automatisch wordt opgenomen, kunt u deze opgeven als een optionele claim om extra eigenschappen toe te voegen om het gedrag van de gebruiker te wijzigen.  |

## <a name="v20-specific-optional-claims-set"></a>v 2.0-specifieke optionele claim sets

Deze claims zijn altijd opgenomen in de Azure AD-tokens v 1.0, maar zijn niet opgenomen in de v 2.0-tokens, tenzij u hierom wordt gevraagd. Deze claims zijn alleen van toepassing op JWTs (ID-tokens en toegangs tokens). 

**Tabel 3: alleen optionele claims voor v 2.0**

| JWT Claim     | Naam                            | Beschrijving                                | Opmerkingen |
|---------------|---------------------------------|-------------|-------|
| `ipaddr`      | IP-adres                      | Het IP-adres van de client die is aangemeld bij.   |       |
| `onprem_sid`  | Lokale beveiligings-id |                                             |       |
| `pwd_exp`     | Wacht woord verloop tijd        | De datum/tijd waarop het wacht woord verloopt. |       |
| `pwd_url`     | URL voor wacht woord wijzigen             | Een URL die de gebruiker kan bezoeken om het wacht woord te wijzigen.   |   |
| `in_corp`     | Binnen bedrijfs netwerk        | Geeft aan of de client zich aanmeldt vanuit het bedrijfs netwerk. Als dat niet het geval is, is de claim niet opgenomen.   |  Op basis van de instellingen voor [vertrouwde IP-adressen](../authentication/howto-mfa-mfasettings.md#trusted-ips) in MFA.    |
| `nickname`    | naam                        | Een extra naam voor de gebruiker. De bijnaam is gescheiden van de voor-en achternaam. | 
| `family_name` | Achternaam                       | Hiermee geeft u de achternaam, de achternaam of de familynaam van de gebruiker, zoals gedefinieerd in het gebruikers object. <br>"family_name": "Miller" | Ondersteund in MSA en Azure AD   |
| `given_name`  | Voornaam                      | Hiermee wordt de eerste of de naam van de gebruiker opgegeven, zoals ingesteld op het gebruikers object.<br>"given_name": "Frank"                   | Ondersteund in MSA en Azure AD  |
| `upn`         | User principal name | Een id voor de gebruiker die kan worden gebruikt met de para meter username_hint.  Geen duurzame id voor de gebruiker en mag niet worden gebruikt voor belang rijke gegevens. | Zie de onderstaande [aanvullende eigenschappen](#additional-properties-of-optional-claims) voor de configuratie van de claim. |

### <a name="additional-properties-of-optional-claims"></a>Aanvullende eigenschappen van optionele claims

Sommige optionele claims kunnen worden geconfigureerd om de manier waarop de claim wordt geretourneerd, te wijzigen. Deze aanvullende eigenschappen worden meestal gebruikt om de migratie van on-premises toepassingen met verschillende gegevens verwachtingen te helpen (bijvoorbeeld `include_externally_authenticated_upn_without_hash` helpt bij clients die geen hash-markeringen (`#`) in de UPN kunnen verwerken)

**Tabel 4: waarden voor het configureren van optionele claims**

| Naam van eigenschap  | Aanvullende naam van eigenschap | Beschrijving |
|----------------|--------------------------|-------------|
| `upn`          |                          | Kan worden gebruikt voor SAML-en JWT-antwoorden, en voor de tokens v 1.0 en v 2.0. |
|                | `include_externally_authenticated_upn`  | Bevat de gast-UPN zoals deze is opgeslagen in de resource-Tenant. Bijvoorbeeld: `foo_hometenant.com#EXT#@resourcetenant.com` |             
|                | `include_externally_authenticated_upn_without_hash` | Hetzelfde als hierboven, behalve dat de hash-markeringen (`#`) worden vervangen door onderstrepings tekens (`_`), bijvoorbeeld `foo_hometenant.com_EXT_@resourcetenant.com` |

#### <a name="additional-properties-example"></a>Voor beeld van extra eigenschappen

    ```json
        "optionalClaims": 
         {
             "idToken": [ 
            { 
                      "name": "upn", 
                      "essential": false,
                  "additionalProperties": [ "include_externally_authenticated_upn"]  
                    }
                 ]
        }
    ```

Dit OptionalClaims-object zorgt ervoor dat het ID-token dat naar de client wordt geretourneerd, een andere UPN bevat met de extra thuis Tenant en informatie over de bron Tenant. De `upn` claim wordt alleen gewijzigd in het token als de gebruiker een gast in de Tenant is (die gebruikmaakt van een andere IDP voor verificatie). 

## <a name="configuring-optional-claims"></a>Optionele claims configureren

> [!IMPORTANT]
> Toegangs tokens worden **altijd** gegenereerd met het manifest van de resource, niet de-client.  In de aanvraag `...scope=https://graph.microsoft.com/user.read...` de resource dus de Microsoft Graph-API.  Het toegangs token wordt dus gemaakt met behulp van het API-manifest van Microsoft Graph, niet het manifest van de client.  Als u het manifest voor uw toepassing wijzigt, worden er nooit tokens voor de Microsoft Graph-API weer geven.  Als u wilt controleren of uw `accessToken` wijzigingen van kracht zijn, vraagt u een token voor uw toepassing op, niet een andere app.  

U kunt optionele claims voor uw toepassing configureren via de gebruikers interface of het toepassings manifest.

1. Ga naar de [Azure Portal](https://portal.azure.com). Zoek en selecteer **Azure Active Directory**.
1. Selecteer in de sectie **beheren** de optie **app-registraties**.
1. Selecteer in de lijst de toepassing waarvoor u de optionele claims wilt configureren.

**Optionele claims configureren via de gebruikers interface:**

[![laat zien hoe u optionele claims configureert via de gebruikers interface](./media/active-directory-optional-claims/token-configuration.png)](./media/active-directory-optional-claims/token-configuration.png)

1. Selecteer in de sectie **beheren** de optie **token configuratie (preview)** .
2. Selecteer **optionele claim toevoegen**.
3. Selecteer het token type dat u wilt configureren.
4. Selecteer de optionele claims die u wilt toevoegen.
5. Klik op **Toevoegen**.

**Optionele claims configureren via het toepassings manifest:**

[![laat zien hoe u optionele claims configureert met het app-manifest](./media/active-directory-optional-claims/app-manifest.png)](./media/active-directory-optional-claims/app-manifest.png)

1. Selecteer in de sectie **beheren** de optie **manifest**. Een manifest editor op het web wordt geopend, zodat u het manifest kunt bewerken. U kunt optioneel ook op **Downloaden** klikken en het manifest lokaal bewerken. Gebruik vervolgens **Uploaden** om het opnieuw toe te passen op de toepassing. Meer informatie over het toepassings manifest vindt u in het [artikel over het Azure AD-manifest](reference-app-manifest.md)van de toepassing.

    De volgende vermelding in het toepassings manifest voegt de auth_time, ipaddr en UPN-optionele claims toe aan de ID, toegang en SAML-tokens.

    ```json
        "optionalClaims":  
           {
              "idToken": [
                    {
                          "name": "auth_time", 
                          "essential": false
                     }
              ],
              "accessToken": [
                     {
                            "name": "ipaddr", 
                            "essential": false
                      }
              ],
              "saml2Token": [
                      {
                            "name": "upn", 
                            "essential": false
                       },
                       {
                            "name": "extension_ab603c56068041afb2f6832e2a17e237_skypeId",
                            "source": "user", 
                            "essential": false
                       }
               ]
           }
    ```

2. Klik op **Opslaan** als u klaar bent. De opgegeven optionele claims worden nu opgenomen in de tokens voor uw toepassing.    


### <a name="optionalclaims-type"></a>Type OptionalClaims

Declareert de optionele claims die worden aangevraagd door een toepassing. Een toepassing kan optionele claims configureren die moeten worden geretourneerd in elk van de drie typen tokens (ID-token, toegangs token, SAML 2-token) die kan worden ontvangen van de beveiligings token service. De toepassing kan een andere set optionele claims configureren die in elk token type worden geretourneerd. De eigenschap OptionalClaims van de entiteit Application is een OptionalClaims-object.

**Tabel 5: eigenschappen van OptionalClaims-type**

| Naam        | Type                       | Beschrijving                                           |
|-------------|----------------------------|-------------------------------------------------------|
| `idToken`     | Verzameling (OptionalClaim) | De optionele claims die zijn geretourneerd in het JWT-ID-token. |
| `accessToken` | Verzameling (OptionalClaim) | De optionele claims die worden geretourneerd in het JWT-toegangs token. |
| `saml2Token`  | Verzameling (OptionalClaim) | De optionele claims die zijn geretourneerd in het SAML-token.   |

### <a name="optionalclaim-type"></a>Type OptionalClaim

Bevat een optionele claim die is gekoppeld aan een toepassing of een service-principal. De eigenschappen idToken, accessToken en saml2Token van het type [OptionalClaims](https://docs.microsoft.com/graph/api/resources/optionalclaims?view=graph-rest-1.0) is een verzameling van OptionalClaim.
Als dit wordt ondersteund door een specifieke claim, kunt u ook het gedrag van de OptionalClaim wijzigen met behulp van het veld AdditionalProperties.

**Tabel 6: eigenschappen van OptionalClaim-type**

| Naam                 | Type                    | Beschrijving                                                                                                                                                                                                                                                                                                   |
|----------------------|-------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `name`                 | Edm.String              | De naam van de optionele claim.                                                                                                                                                                                                                                                                           |
| `source`               | Edm.String              | De bron (Directory-object) van de claim. Er zijn vooraf gedefinieerde claims en door de gebruiker gedefinieerde claims van extensie-eigenschappen. Als de bron waarde Null is, is de claim een vooraf gedefinieerde optionele claim. Als de bron waarde gebruiker is, is de waarde in de eigenschap naam de extensie-eigenschap van het gebruikers object. |
| `essential`            | Edm.Boolean             | Als de waarde True is, is de claim die door de client is opgegeven, nodig om te zorgen voor een soepele autorisatie-ervaring voor de specifieke taak die door de eind gebruiker wordt aangevraagd. De standaardwaarde is false.                                                                                                             |
| `additionalProperties` | Verzameling (EDM. String) | Aanvullende eigenschappen van de claim. Als er een eigenschap in deze verzameling bestaat, wordt het gedrag van de optionele claim die is opgegeven in de eigenschap name, gewijzigd.                                                                                                                                               |
## <a name="configuring-directory-extension-optional-claims"></a>Optionele claims voor Directory-extensies configureren

Naast de optionele standaard claim sets, kunt u ook tokens configureren voor het insluiten van extensies. Deze functie is handig voor het koppelen van aanvullende gebruikers informatie die uw app kan gebruiken, bijvoorbeeld een extra id of een belang rijke configuratie optie die de gebruiker heeft ingesteld. Zie de onderkant van deze pagina voor een voor beeld.

> [!NOTE]
> - Directory schema-uitbrei dingen zijn een Azure AD-functie, dus als uw toepassings manifest een aangepaste extensie aanvraagt en een MSA-gebruiker zich aanmeldt bij uw app, worden deze uitbrei dingen niet geretourneerd.

### <a name="directory-extension-formatting"></a>Opmaak van Directory-extensies

Gebruik de volledige naam van de extensie (in de indeling: `extension_<appid>_<attributename>`) bij het configureren van optionele claims voor Directory-extensies met het toepassings manifest. De `<appid>` moet overeenkomen met de ID van de toepassing die de claim aanvraagt. 

Binnen de JWT worden deze claims verzonden met de volgende naam notatie: `extn.<attributename>`.

Binnen de SAML-tokens worden deze claims verzonden met de volgende URI-indeling: `http://schemas.microsoft.com/identity/claims/extn.<attributename>`

## <a name="configuring-groups-optional-claims"></a>Groepen optionele claims configureren

   > [!NOTE]
   > De mogelijkheid om groeps namen te verzenden voor gebruikers en groepen die vanaf on-premises zijn gesynchroniseerd, is open bare preview.

In deze sectie worden de configuratie opties beschreven onder optionele claims voor het wijzigen van de groeps kenmerken die worden gebruikt in groeps claims van de standaard groep-objectID tot kenmerken die worden gesynchroniseerd vanuit on-premises Windows-Active Directory. U kunt groepen optionele claims voor uw toepassing configureren via de gebruikers interface of het toepassings manifest.

> [!IMPORTANT]
> Zie [groeps claims configureren voor toepassingen met Azure AD](../hybrid/how-to-connect-fed-group-claims.md)voor meer informatie, inclusief belang rijke aanvullende opmerkingen voor de open bare preview van groeps claims van on-premises kenmerken.

**Groepen optionele claims configureren via de gebruikers interface:**
1. Meld u aan bij [Azure Portal](https://portal.azure.com)
1. Nadat u bent geverifieerd, kiest u uw Azure AD-Tenant door deze te selecteren in de rechter bovenhoek van de pagina
1. **Azure Active Directory** selecteren in het menu aan de linkerkant
1. Selecteer in de sectie **beheren** de optie **app-registraties**
1. Selecteer in de lijst de toepassing waarvoor u de optionele claims wilt configureren
1. Selecteer in de sectie **beheren** de optie **token configuratie (preview-versie)**
2. Claim voor het **toevoegen van groepen** selecteren
3. Selecteer de groeps typen die moeten worden geretourneerd (**alle groepen**, **beveiligings groep**of **DirectoryRole**). De optie **alle groepen** omvat **beveiligings groep**, **DirectoryRole**en **DistributionList**
4. Optioneel: Klik op de eigenschappen van het specifieke token type om de claim waarde voor groepen te wijzigen zodat deze lokale groeps kenmerken bevat of om het claim type te wijzigen in een rol
5. Klik op **Opslaan**.

**Groepen optionele claims configureren via het toepassings manifest:**
1. Meld u aan bij [Azure Portal](https://portal.azure.com)
1. Nadat u bent geverifieerd, kiest u uw Azure AD-Tenant door deze te selecteren in de rechter bovenhoek van de pagina
1. **Azure Active Directory** selecteren in het menu aan de linkerkant
1. Selecteer in de lijst de toepassing waarvoor u de optionele claims wilt configureren
1. Selecteer in de sectie **beheren** de optie **manifest**
3. Voeg de volgende vermelding toe met behulp van de manifest editor:

   De geldige waarden zijn:

   - ' All ' (deze optie omvat beveiligings groep, DirectoryRole en DistributionList)
   - Beveiligings groep
   - "DirectoryRole"

   Bijvoorbeeld:

    ```json
        "groupMembershipClaims": "SecurityGroup"
    ```

   Standaard wordt groeps Objectid's verzonden in de claim waarde van de groep.  Als u de claim waarde wilt wijzigen zodat deze lokale groeps kenmerken bevat, of als u het claim type wilt wijzigen in rol, gebruikt u de OptionalClaims-configuratie als volgt:

3. Stel groeps naam configuratie optionele claims in.

   Als u wilt dat groepen in het token de on-premises AD-groeps kenmerken bevatten in de sectie optionele claims, geeft u op welk token type optionele claim moet worden toegepast op, de naam van de optionele claim die is aangevraagd en eventuele extra eigenschappen die nodig zijn.  Meerdere token typen kunnen worden weer gegeven:

   - idToken voor het OIDC-ID-token
   - accessToken voor het OAuth/OIDC-toegangs token
   - Saml2Token voor SAML-tokens.

   > [!NOTE]
   > Het type Saml2Token is van toepassing op zowel SAML 1.1-als SAML 2.0-indelings tokens

   Wijzig voor elk relevant token type de OptionalClaims-sectie in het manifest. Het OptionalClaims-schema is als volgt:

    ```json
       {
       "name": "groups",
       "source": null,
       "essential": false,
       "additionalProperties": []
       }
    ```

   | Optioneel claim schema | Waarde |
   |----------|-------------|
   | **naam** | Moet ' groups ' zijn |
   | **Bron** | Niet gebruikt. Null-waarde weglaten of opgeven |
   | **downloaden** | Niet gebruikt. Weglaten of onwaar opgeven |
   | **additionalProperties:** | Lijst met aanvullende eigenschappen.  Geldige opties zijn ' sam_account_name ', ' dns_domain_and_sam_account_name ', ' netbios_domain_and_sam_account_name ', ' emit_as_roles ' |

   In additionalProperties is slechts een van de sam_account_name, dns_domain_and_sam_account_name, netbios_domain_and_sam_account_name vereist.  Als er meer dan een aanwezig is, wordt de eerste gebruikt en worden alle andere genegeerd.

   Voor sommige toepassingen is groeps informatie over de gebruiker in de rol claim vereist.  Als u het claim type wilt wijzigen van een groepclaim in een groepclaim, voegt u ' emit_as_roles ' toe aan extra eigenschappen.  De groeps waarden worden verzonden in de rol claim.

   > [!NOTE]
   > Als ' emit_as_roles ' wordt gebruikt, worden de toepassings rollen die zijn geconfigureerd dat de gebruiker is toegewezen, niet weer gegeven in de rol claim

**Vindt**

1) Groepen verzenden als groeps namen in OAuth-toegangs tokens in de dnsDomainName\sAMAccountName-indeling

    
    **Configuratie van de gebruikers interface:**

    [![laat zien hoe u optionele claims configureert via de gebruikers interface](./media/active-directory-optional-claims/groups-example-1.png)](./media/active-directory-optional-claims/groups-example-1.png)


    **Vermelding van het toepassings manifest:**
    ```json
        "optionalClaims": {
            "accessToken": [{
            "name": "groups",
            "additionalProperties": ["dns_domain_and_sam_account_name"]
            }]
        }
    ```

 
    
2) Namen van het verzenden van groepen die moeten worden geretourneerd in de netbiosDomain\sAMAccountName-indeling als de rol claim in SAML-en OIDC-ID-tokens

    **Configuratie van de gebruikers interface:**

    [![laat zien hoe u optionele claims configureert via de gebruikers interface](./media/active-directory-optional-claims/groups-example-2.png)](./media/active-directory-optional-claims/groups-example-2.png)

    **Vermelding van het toepassings manifest:**
    
    ```json
        "optionalClaims": {
        "saml2Token": [{
            "name": "groups",
            "additionalProperties": ["netbios_name_and_sam_account_name", "emit_as_roles"]
        }],
        "idToken": [{
            "name": "groups",
            "additionalProperties": ["netbios_name_and_sam_account_name", "emit_as_roles"]
        }]
    ``` 
     

## <a name="optional-claims-example"></a>Voor beeld van optioneel claim

In deze sectie kunt u een scenario door lopen om te zien hoe u de optionele claim functie voor uw toepassing kunt gebruiken.
Er zijn meerdere opties beschikbaar voor het bijwerken van de eigenschappen van de identiteits configuratie van een toepassing om optionele claims in te scha kelen en te configureren:
-    U kunt de gebruikers interface van de **token configuratie (preview-versie** ) gebruiken (Zie het onderstaande voor beeld)
-    U kunt het **manifest** gebruiken (Zie voor beeld hieronder). Lees eerst het [document over het Azure AD-toepassings manifest](https://docs.microsoft.com/azure/active-directory/develop/active-directory-application-manifest) voor een inleiding tot het manifest.
-   Het is ook mogelijk om een toepassing te schrijven die gebruikmaakt van de [Microsoft Graph-API](https://docs.microsoft.com/graph/use-the-api?context=graph%2Fapi%2F1.0&view=graph-rest-1.0) om uw toepassing bij te werken. Het [OptionalClaims](https://docs.microsoft.com/graph/api/resources/optionalclaims?view=graph-rest-1.0) -type in de referentie gids voor de Microsoft Graph-API kan u helpen bij het configureren van de optionele claims.

**Voor beeld:** In het onderstaande voor beeld gebruikt u de gebruikers interface voor **token configuratie (preview)** en het **manifest** om optionele claims toe te voegen aan de toegang, id en SAML-tokens die zijn bedoeld voor uw toepassing. Er worden verschillende optionele claims toegevoegd aan elk type token dat de toepassing kan ontvangen:
-    De ID-tokens bevatten nu de UPN voor federatieve gebruikers in het volledige formulier (`<upn>_<homedomain>#EXT#@<resourcedomain>`).
-    De toegangs tokens die andere clients aanvragen voor deze toepassing, bevatten nu de auth_time claim
-    De SAML-tokens bevatten nu de skypeId Directory-schema-uitbrei ding (in dit voor beeld is de App-ID voor deze app ab603c56068041afb2f6832e2a17e237). De SAML-tokens tonen de Skype-ID als `extension_skypeId`.

**Configuratie van de gebruikers interface:**

1. Meld u aan bij [Azure Portal](https://portal.azure.com)

1. Nadat u bent geverifieerd, kiest u uw Azure AD-Tenant door deze te selecteren in de rechter bovenhoek van de pagina.

1. Selecteer **Azure Active Directory** in het menu aan de linkerkant.

1. Selecteer **app-registraties**in het gedeelte **beheren** .

1. Zoek de toepassing waarvoor u de optionele claims wilt configureren in de lijst en klik erop.

1. Klik onder de sectie **beheren** op **token configuratie (preview-versie)** .

1. Selecteer **optionele claim toevoegen**, selecteer het **id-** token type, selecteer **UPN** in de lijst met claims en klik vervolgens op **toevoegen**.

1. Selecteer **optionele claim toevoegen**, selecteer het type **toegangs** token, selecteer **auth_time** in de lijst met claims en klik vervolgens op **toevoegen**.

1. Klik in het scherm overzicht van de token configuratie op het potlood pictogram naast **UPN**, klik op de **extern geauthenticeerde** wissel knop en klik vervolgens op **Opslaan**.

1. Selecteer **optionele claim toevoegen**, selecteer het **SAML** -token type, selecteer **Extn. skypeID** in de lijst met claims (alleen van toepassing als u een Azure AD-gebruikers object hebt gemaakt met de naam SkypeID) en klik vervolgens op **toevoegen**.

    [![laat zien hoe u optionele claims configureert via de gebruikers interface](./media/active-directory-optional-claims/token-config-example.png)](./media/active-directory-optional-claims/token-config-example.png)

**Manifest configuratie:**
1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Nadat u bent geverifieerd, kiest u uw Azure AD-Tenant door deze te selecteren in de rechter bovenhoek van de pagina.
1. Selecteer **Azure Active Directory** in het menu aan de linkerkant.
1. Zoek de toepassing waarvoor u de optionele claims wilt configureren in de lijst en klik erop.
1. Klik onder de sectie **beheren** op **manifest** om de editor voor inline manifesten te openen.
1. U kunt het manifest rechtstreeks bewerken met deze editor. Het manifest volgt het schema voor de [toepassings entiteit](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest)en maakt het manifest automatisch op wanneer het is opgeslagen. Nieuwe elementen worden toegevoegd aan de eigenschap `OptionalClaims`.

    ```json
            "optionalClaims": {
                "idToken": [ 
                     { 
                        "name": "upn", 
                        "essential": false, 
                        "additionalProperties": [ "include_externally_authenticated_upn"]  
                     }
                     ],
                "accessToken": [ 
                      {
                        "name": "auth_time", 
                        "essential": false
                      }
                     ],
            "saml2Token": [ 
                  { 
                    "name": "extension_ab603c56068041afb2f6832e2a17e237_skypeId",
                    "source": "user", 
                    "essential": true
                  }
                 ]
        ``` 


1. When you're finished updating the manifest, click **Save** to save the manifest.

## Next steps

Learn more about the standard claims provided by Azure AD.

- [ID tokens](id-tokens.md)
- [Access tokens](access-tokens.md)
