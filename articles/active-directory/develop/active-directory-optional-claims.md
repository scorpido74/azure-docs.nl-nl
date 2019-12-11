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
ms.collection: M365-identity-device-management
ms.openlocfilehash: bcaf347eb91f8777b56bb2ea4d26985b2d75f645
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74967200"
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
| Persoonlijke Microsoft-account  | N/A  | Ondersteund |
| Azure AD-account      | Ondersteund | Ondersteund |

## <a name="v10-and-v20-optional-claims-set"></a>optionele claim sets v 1.0 en v 2.0

Hieronder vindt u de set optionele claims die standaard beschikbaar zijn voor het gebruik van toepassingen. Zie [Directory-extensies](#configuring-directory-extension-optional-claims)hieronder om aangepaste optionele claims voor uw toepassing toe te voegen. Wanneer claims aan het **toegangs token**worden toegevoegd, geldt dit voor toegangs tokens die zijn aangevraagd voor de toepassing (een web-API), niet *voor* de toepassingen *van* de toepassing. Dit zorgt ervoor dat de client geen toegang heeft tot uw API; de juiste gegevens zijn aanwezig in het toegangs token dat wordt gebruikt voor verificatie bij uw API.

> [!NOTE]
> De meeste van deze claims kunnen worden opgenomen in JWTs voor de tokens v 1.0 en v 2.0, maar niet voor SAML-tokens, tenzij anders vermeld in de kolom token type. Consumenten accounts ondersteunen een subset van deze claims, die zijn gemarkeerd in de kolom ' gebruiker type '.  Veel van de vermelde claims zijn niet van toepassing op gebruikers van consumenten (ze hebben geen Tenant, dus `tenant_ctry` heeft geen waarde).  

**Tabel 2: v 1.0 en v 2.0 optionele claim sets**

| Naam                       |  Beschrijving   | Token type | Gebruikers type | Opmerkingen  |
|----------------------------|----------------|------------|-----------|--------|
| `auth_time`                | Tijdstip waarop de laatste verificatie van de gebruiker is gestart. Zie OpenID Connect Connect spec.| JWT        |           |  |
| `tenant_region_scope`      | De regio van de resource-Tenant | JWT        |           | |
| `home_oid`                 | Voor gast gebruikers, de object-ID van de gebruiker in de thuis Tenant van de gebruiker.| JWT        |           | |
| `sid`                      | Sessie-ID, die wordt gebruikt voor gebruikers die zich afmelden per sessie. | JWT        |  Persoonlijke en Azure AD-accounts.   |         |
| `platf`                    | Apparaatplatform    | JWT        |           | Beperkt tot beheerde apparaten die het apparaattype kunnen controleren.|
| `verified_primary_email`   | Bron van de PrimaryAuthoritativeEmail van de gebruiker      | JWT        |           |         |
| `verified_secondary_email` | Bron van de SecondaryAuthoritativeEmail van de gebruiker   | JWT        |           |        |
| `enfpolids`                | Afgedwongen beleids-Id's. Een lijst met de beleids-Id's die voor de huidige gebruiker zijn geëvalueerd. | JWT |  |  |
| `vnet`                     | Gegevens van VNET-specificatie. | JWT        |           |      |
| `fwd`                      | IP-adres.| JWT    |   | Hiermee wordt het oorspronkelijke IPv4-adres van de aanvragende client toegevoegd (in een VNET) |
| `ctry`                     | Land van gebruiker | JWT |  | Azure AD retourneert de `ctry` optionele claim als deze aanwezig is en de waarde van de claim is een standaard land code van twee letters, zoals FR, JP, SZ, enzovoort. |
| `tenant_ctry`              | Land van de resource Tenant | JWT | | |
| `xms_pdl`          | Voorkeurs locatie van gegevens   | JWT | | Voor multi-geo-tenants is dit de 3-letter-code met de geografische regio waarin de gebruiker zich bevindt. Zie de [Azure AD Connect documentatie over de voorkeurs locatie van gegevens](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-feature-preferreddatalocation)voor meer informatie.<br/>Bijvoorbeeld: `APC` voor Azië en Stille Oceaan. |
| `xms_pl`                   | Voorkeurs taal van gebruiker  | JWT ||De voorkeurs taal van de gebruiker, indien ingesteld. Vanuit hun thuis Tenant, in scenario's voor toegang tot de gast. Geformatteerd LL-CC ("en-US"). |
| `xms_tpl`                  | Voorkeurs taal van Tenant| JWT | | De voorkeurs taal van de resource-Tenant, indien ingesteld. Opgemaakte LL ("en"). |
| `ztdid`                    | Nul-touch implementatie-ID | JWT | | De apparaat-id die wordt gebruikt voor [Windows auto pilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-10-autopilot) |
| `email`                    | De adresseer bare e-mail voor deze gebruiker als de gebruiker er een heeft.  | JWT, SAML | MSA, Azure AD | Deze waarde is standaard opgenomen als de gebruiker een gast in de Tenant is.  Voor beheerde gebruikers (die binnen de Tenant) moet het worden aangevraagd via deze optionele claim of, alleen op v 2.0, met het OpenID Connect-bereik.  Voor beheerde gebruikers moet het e-mail adres worden ingesteld in de [Office-beheer Portal](https://portal.office.com/adminportal/home#/users).| 
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
| `pwd_exp`     | Wachtwoordverlooptijd        | De datum/tijd waarop het wacht woord verloopt. |       |
| `pwd_url`     | URL voor wacht woord wijzigen             | Een URL die de gebruiker kan bezoeken om het wacht woord te wijzigen.   |   |
| `in_corp`     | Binnen bedrijfsnetwerk        | Geeft aan of de client zich aanmeldt vanuit het bedrijfs netwerk. Als dat niet het geval is, is de claim niet opgenomen.   |  Op basis van de instellingen voor [vertrouwde IP-adressen](../authentication/howto-mfa-mfasettings.md#trusted-ips) in MFA.    |
| `nickname`    | Bijnaam                        | Een extra naam voor de gebruiker, gescheiden van de voor-en achternaam. | 
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

Dit OptionalClaims-object zorgt ervoor dat het ID-token dat naar de client wordt geretourneerd, een andere UPN bevat met de extra thuis Tenant en informatie over de bron Tenant. Hiermee wordt alleen de `upn` claim in het token gewijzigd als de gebruiker een gast in de Tenant is (die gebruikmaakt van een andere IDP voor verificatie). 

## <a name="configuring-optional-claims"></a>Optionele claims configureren

> [!IMPORTANT]
> Toegangs tokens worden **altijd** gegenereerd met het manifest van de resource, niet de-client.  In de aanvraag `...scope=https://graph.microsoft.com/user.read...` de resource dus diagram.  Het toegangs token wordt dus gemaakt met behulp van het grafiek manifest, niet het manifest van de client.  Als u het manifest voor uw toepassing wijzigt, worden er nooit tokens voor de grafiek weer geven.  Als u wilt controleren of uw `accessToken` wijzigingen van kracht zijn, vraagt u een token voor uw toepassing op, niet een andere app.  

U kunt optionele claims voor uw toepassing configureren via de gebruikers interface of het toepassings manifest.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Nadat u bent geverifieerd, kiest u uw Azure AD-Tenant door deze te selecteren in de rechter bovenhoek van de pagina.
1. Selecteer **Azure Active Directory** in het menu links.
1. Selecteer in de sectie **beheren** de optie **app-registraties**.
1. Selecteer in de lijst de toepassing waarvoor u de optionele claims wilt configureren.

**Optionele claims configureren via de gebruikers interface:**

[![laat zien hoe u optionele claims configureert via de gebruikers interface](./media/active-directory-optional-claims/token-configuration.png)](./media/active-directory-optional-claims/token-configuration.png)

1. Selecteer in de sectie **beheren** de optie **token configuratie (preview)** .
2. Selecteer **optionele claim toevoegen**.
3. Selecteer het token type dat u wilt configureren.
4. Selecteer de optionele claims die u wilt toevoegen.
5. Klik op **Add**.

**Optionele claims configureren via het toepassings manifest:**

[![laat zien hoe u optionele claims configureert met het app-manifest](./media/active-directory-optional-claims/app-manifest.png)](./media/active-directory-optional-claims/app-manifest.png)

1. Selecteer in de sectie **beheren** de optie **manifest**. Een manifest editor op het web wordt geopend, zodat u het manifest kunt bewerken. U kunt optioneel ook op **Downloaden** klikken en het manifest lokaal bewerken. Gebruik vervolgens **Uploaden** om het opnieuw toe te passen op de toepassing. Meer informatie over het toepassings manifest vindt u in het [artikel over het Azure AD-manifest](reference-app-manifest.md)van de toepassing.

    De volgende vermelding in het toepassings manifest voegt de auth_time, ipaddr en UPN-optionele claims toe aan ID-, toegangs-en SAML-tokens.

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

2. When finished, click **Save**. Now the specified optional claims will be included in the tokens for your application.    


### OptionalClaims type

Declares the optional claims requested by an application. An application can configure optional claims to be returned in each of three types of tokens (ID token, access token, SAML 2 token) it can receive from the security token service. The application can configure a different set of optional claims to be returned in each token type. The OptionalClaims property of the Application entity is an OptionalClaims object.

**Table 5: OptionalClaims type properties**

| Name        | Type                       | Description                                           |
|-------------|----------------------------|-------------------------------------------------------|
| `idToken`     | Collection (OptionalClaim) | The optional claims returned in the JWT ID token. |
| `accessToken` | Collection (OptionalClaim) | The optional claims returned in the JWT access token. |
| `saml2Token`  | Collection (OptionalClaim) | The optional claims returned in the SAML token.   |

### OptionalClaim type

Contains an optional claim associated with an application or a service principal. The idToken, accessToken, and saml2Token properties of the [OptionalClaims](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#optionalclaims-type) type is a collection of OptionalClaim.
If supported by a specific claim, you can also modify the behavior of the OptionalClaim using the AdditionalProperties field.

**Table 6: OptionalClaim type properties**

| Name                 | Type                    | Description                                                                                                                                                                                                                                                                                                   |
|----------------------|-------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `name`                 | Edm.String              | The name of the optional claim.                                                                                                                                                                                                                                                                           |
| `source`               | Edm.String              | The source (directory object) of the claim. There are predefined claims and user-defined claims from extension properties. If the source value is null, the claim is a predefined optional claim. If the source value is user, the value in the name property is the extension property from the user object. |
| `essential`            | Edm.Boolean             | If the value is true, the claim specified by the client is necessary to ensure a smooth authorization experience for the specific task requested by the end user. The default value is false.                                                                                                             |
| `additionalProperties` | Collection (Edm.String) | Additional properties of the claim. If a property exists in this collection, it modifies the behavior of the optional claim specified in the name property.                                                                                                                                               |
## Configuring directory extension optional claims

In addition to the standard optional claims set, you can also configure tokens to include directory schema extensions. For more info, see [Directory schema extensions](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions). This feature is useful for attaching additional user information that your app can use – for example, an additional identifier or important configuration option that the user has set. See the bottom of this page for an example.

> [!NOTE]
> - Directory schema extensions are an Azure AD-only feature, so if your application manifest requests a custom extension and an MSA user logs into your app, these extensions will not be returned.
> - Azure AD optional claims only work with Azure AD Graph extensions and do not work with Microsoft Graph directory extensions. Both APIs require the `Directory.ReadWriteAll` permission, which can only be consented by admins.

### Directory extension formatting

When configuring directory extension optional claims using the application manifest, use the full name of the extension (in the format: `extension_<appid>_<attributename>`). The `<appid>` must match the ID of the application requesting the claim. 

Within the JWT, these claims will be emitted with the following name format:  `extn.<attributename>`.

Within the SAML tokens, these claims will be emitted with the following URI format: `http://schemas.microsoft.com/identity/claims/extn.<attributename>`

## Configuring groups optional claims

   > [!NOTE]
   > The ability to emit group names for users and groups synced from on-premises is Public Preview.

This section covers the configuration options under optional claims for changing the group attributes used in group claims from the default group objectID to attributes synced from on-premises Windows Active Directory. You can configure groups optional claims for your application through the UI or application manifest.

> [!IMPORTANT]
> See [Configure group claims for applications with Azure AD](../hybrid/how-to-connect-fed-group-claims.md) for more details including important caveats for the public preview of group claims from on-premises attributes.

**Configuring groups optional claims through the UI:**
1. Sign in to the [Azure portal](https://portal.azure.com)
1. After you've authenticated, choose your Azure AD tenant by selecting it from the top right corner of the page
1. Select **Azure Active Directory** from the left hand menu
1. Under the **Manage** section, select **App registrations**
1. Select the application you want to configure optional claims for in the list
1. Under the **Manage** section, select **Token configuration (preview)**
2. Select **Add groups claim**
3. Select the group types to return (**All Groups**, **SecurityGroup** or **DirectoryRole**). The **All Groups** option includes **SecurityGroup**, **DirectoryRole** and **DistributionList**
4. Optional: click on the specific token type properties to modify the groups claim value to contain on premises group attributes or to change the claim type to a role
5. Click **Save**

**Configuring groups optional claims through the application manifest:**
1. Sign in to the [Azure portal](https://portal.azure.com)
1. After you've authenticated, choose your Azure AD tenant by selecting it from the top right corner of the page
1. Select **Azure Active Directory** from the left hand menu
1. Select the application you want to configure optional claims for in the list
1. Under the **Manage** section, select **Manifest**
3. Add the following entry using the manifest editor:

   The valid values are:

   - "All" (this option includes SecurityGroup, DirectoryRole and DistributionList)
   - "SecurityGroup"
   - "DirectoryRole"

   For example:

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

**Voorbeelden:**

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
-   Het is ook mogelijk om een toepassing te schrijven die gebruikmaakt van de [Graph API](https://docs.microsoft.com/azure/active-directory/develop/active-directory-graph-api) om uw toepassing bij te werken. De [verwijzing naar de entiteit en het complexe type](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#optionalclaims-type) in de referentie gids van Graph API kan u helpen bij het configureren van de optionele claims.

**Voor beeld:** In het onderstaande voor beeld gebruikt u de gebruikers interface voor **token configuratie (preview)** en het **manifest** om optionele claims toe te voegen aan de toegang, id en SAML-tokens die zijn bedoeld voor uw toepassing. Er worden verschillende optionele claims toegevoegd aan elk type token dat de toepassing kan ontvangen:
-    De ID-tokens bevatten nu de UPN voor federatieve gebruikers in het volledige formulier (`<upn>_<homedomain>#EXT#@<resourcedomain>`).
-    De toegangs tokens die andere clients aanvragen voor deze toepassing, bevatten nu de auth_time claim
-    De SAML-tokens bevatten nu de skypeId Directory-schema-uitbrei ding (in dit voor beeld is de App-ID voor deze app ab603c56068041afb2f6832e2a17e237). De SAML-tokens tonen de Skype-ID als `extension_skypeId`.

**Configuratie van de gebruikers interface:**

1. Meld u aan bij [Azure Portal](https://portal.azure.com)

1. Nadat u bent geverifieerd, kiest u uw Azure AD-Tenant door deze te selecteren in de rechter bovenhoek van de pagina.

1. Selecteer **Azure Active Directory** in het menu links.

1. Selecteer **app-registraties**in het gedeelte **beheren** .

1. Zoek de toepassing waarvoor u de optionele claims wilt configureren in de lijst en klik erop.

1. Klik onder de sectie **beheren** op **token configuratie (preview-versie)** .

1. Selecteer **optionele claim toevoegen**, selecteer het **id-** token type, selecteer **UPN** in de lijst met claims en klik vervolgens op **toevoegen**.

1. Selecteer **optionele claim toevoegen**, selecteer het type **toegangs** token, selecteer **auth_time** in de lijst met claims en klik vervolgens op **toevoegen**.

1. Klik in het scherm overzicht van token configuratie op het potlood pictogram naast **UPN**, klik op de **extern geauthenticeerde** wissel knop en klik vervolgens op **Opslaan**.

1. Selecteer **optionele claim toevoegen**, selecteer het **SAML** -token type, selecteer **Extn. skypeID** in de lijst met claims (alleen van toepassing als u een Azure AD-gebruikers object hebt gemaakt met de naam SkypeID) en klik vervolgens op **toevoegen**.

    [![laat zien hoe u optionele claims configureert via de gebruikers interface](./media/active-directory-optional-claims/token-config-example.png)](./media/active-directory-optional-claims/token-config-example.png)

**Manifest configuratie:**
1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Nadat u bent geverifieerd, kiest u uw Azure AD-Tenant door deze te selecteren in de rechter bovenhoek van de pagina.
1. Selecteer **Azure Active Directory** in het menu links.
1. Zoek de toepassing waarvoor u de optionele claims wilt configureren in de lijst en klik erop.
1. Klik onder de sectie **beheren** op **manifest** om de editor voor inline manifesten te openen.
1. U kunt het manifest rechtstreeks bewerken met deze editor. Het manifest volgt het schema voor de [toepassings entiteit]. (https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest) en maakt het manifest automatisch na het opslaan. Nieuwe elementen worden toegevoegd aan de eigenschap `OptionalClaims`.

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
