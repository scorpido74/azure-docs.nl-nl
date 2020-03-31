---
title: Optionele claims voor Azure AD-apps aanbieden | Azure
titleSuffix: Microsoft identity platform
description: Aangepaste of aanvullende claims toevoegen aan de SAML 2.0- en JSON Web Tokens (JWT)-tokens die zijn uitgegeven door Azure Active Directory.
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 3/11/2020
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin, keyam
ms.custom: aaddev
ms.openlocfilehash: 23d83b59c510f2565b2f66f78dad56c9c9592dd0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79136514"
---
# <a name="how-to-provide-optional-claims-to-your-azure-ad-app"></a>How to: Optionele claims voor uw Azure AD-app

Toepassingsontwikkelaars kunnen optionele claims in hun Azure AD-toepassingen gebruiken om aan te geven welke claims ze willen in tokens die naar hun toepassing worden verzonden. 

U optionele claims gebruiken om:

- Selecteer aanvullende claims die u wilt opnemen in tokens voor uw toepassing.
- Wijzig het gedrag van bepaalde claims die Azure AD retourneert in tokens.
- Aangepaste claims voor uw toepassing toevoegen en openen.

Zie voor de lijsten met standaardclaims de [documentatie voor toegangstoken](access-tokens.md) en [id_token](id-tokens.md) claims. 

Hoewel optionele claims worden ondersteund in zowel v1.0- als v2.0-formaattokens, evenals SAML-tokens, bieden ze het grootste deel van hun waarde bij het verplaatsen van v1.0 naar v2.0. Een van de doelen van het eindpunt van het [V2.0 Microsoft-identiteitsplatform](active-directory-appmodel-v2-overview.md) is kleinere tokenformaten om optimale prestaties van klanten te garanderen. Als gevolg hiervan zijn verschillende claims die voorheen in de access- en ID-tokens waren opgenomen, niet langer aanwezig in v2.0-tokens en moeten ze specifiek per toepassing worden gevraagd.

**Tabel 1: Toepasbaarheid**

| Accounttype | v1.0 tokens | v2.0-tokens  |
|--------------|---------------|----------------|
| Persoonlijk Microsoft-account  | N.v.t.  | Ondersteund |
| Azure AD-account      | Ondersteund | Ondersteund |

## <a name="v10-and-v20-optional-claims-set"></a>v1.0 en v2.0 optionele claimset

De set optionele claims die standaard beschikbaar zijn voor toepassingen die kunnen worden gebruikt, vindt u hieronder. Zie [Directory extensions](#configuring-directory-extension-optional-claims), hieronder. Bij het toevoegen van claims aan het **toegangstoken**zijn de claims van toepassing op toegangstokens die zijn aangevraagd *voor* de toepassing (een web-API), niet op claims die *door* de toepassing worden aangevraagd. Het maakt niet uit hoe de client toegang heeft tot uw API, de juiste gegevens zijn aanwezig in het toegangstoken dat wordt gebruikt om te verifiëren tegen uw API.

> [!NOTE]
> De meerderheid van deze claims kan worden opgenomen in JWT's voor v1.0- en v2.0-tokens, maar niet in SAML-tokens, behalve waar vermeld in de kolom Token type. Consumentenaccounts ondersteunen een subset van deze claims, gemarkeerd in de kolom 'Gebruikerstype'.  Veel van de genoemde claims zijn niet van toepassing op `tenant_ctry` consumentengebruikers (ze hebben geen huurder, dus heeft geen waarde).  

**Tabel 2: optionele claimset v1.0 en v2.0**

| Name                       |  Beschrijving   | Tokentype | Gebruikerstype | Opmerkingen  |
|----------------------------|----------------|------------|-----------|--------|
| `auth_time`                | Tijd waarop de gebruiker voor het laatst is geverifieerd. Zie OpenID Connect-specificaties.| JWT JWT        |           |  |
| `tenant_region_scope`      | Regio van de resourcetenant | JWT JWT        |           | |
| `home_oid`                 | Voor gastgebruikers is de object-id van de gebruiker in de thuistenant van de gebruiker.| JWT JWT        |           | |
| `sid`                      | Sessie-ID, gebruikt voor het afmelden van gebruikers per sessie. | JWT JWT        |  Persoonlijke en Azure AD-accounts.   |         |
| `platf`                    | Apparaatplatform    | JWT JWT        |           | Beperkt tot beheerde apparaten die het type apparaat kunnen verifiëren.|
| `verified_primary_email`   | Afkomstig van de primaireAuthoritativeEmail van de gebruiker      | JWT JWT        |           |         |
| `verified_secondary_email` | Afkomstig van de SecondaryAuthoritativeEmail van de gebruiker   | JWT JWT        |           |        |
| `enfpolids`                | Afgedwongen beleids-id's. Een lijst met de beleids-id's die zijn geëvalueerd voor de huidige gebruiker. | JWT JWT |  |  |
| `vnet`                     | VNET specifier informatie. | JWT JWT        |           |      |
| `fwd`                      | IP-adres.| JWT JWT    |   | Hiermee voegt u het oorspronkelijke IPv4-adres van de aanvragende client toe (wanneer u zich in een VNET bevindt) |
| `ctry`                     | Land van gebruiker | JWT JWT |  | Azure AD `ctry` retourneert de optionele claim als deze aanwezig is en de waarde van de claim is een standaard landcode met twee letters, zoals FR, JP, SZ, enzovoort. |
| `tenant_ctry`              | Land van de resourcetenant | JWT JWT | | |
| `xms_pdl`          | Voorkeursgegevenslocatie   | JWT JWT | | Voor multi-geotenants is de voorkeursgegevenslocatie de code van drie letters met de geografische regio waarin de gebruiker zich bevindt. Zie de Azure [AD Connect-documentatie over de gewenste gegevenslocatie](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-feature-preferreddatalocation)voor meer informatie .<br/>Bijvoorbeeld: `APC` voor Azië-Pacific. |
| `xms_pl`                   | Gebruiker voorkeurstaal  | JWT JWT ||De voorkeurstaal van de gebruiker, indien ingesteld. Afkomstig van hun huis huurder, in gast toegangsscenario's. Geformatteerde LL-CC ("en-us"). |
| `xms_tpl`                  | Voorkeurstaal voor tenant| JWT JWT | | De voorkeurstaal van de resourcetenant, indien ingesteld. Opgemaakte LL ("nl"). |
| `ztdid`                    | Zero-touch implementatie-id | JWT JWT | | De apparaatidentiteit die wordt gebruikt voor [Windows AutoPilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-10-autopilot) |
| `email`                    | De adresseerbare e-mail voor deze gebruiker, als de gebruiker er een heeft.  | JWT, SAML | MSA, Azure AD | Deze waarde wordt standaard opgenomen als de gebruiker een gast in de tenant is.  Voor beheerde gebruikers (de gebruikers in de tenant) moet deze worden aangevraagd via deze optionele claim of, alleen op v2.0, met de OpenID-scope.  Voor beheerde gebruikers moet het e-mailadres worden ingesteld in de [Office-beheerportal](https://portal.office.com/adminportal/home#/users).| 
| `groups`| Optionele opmaak voor groepsclaims |JWT, SAML| |Gebruikt in combinatie met de instelling GroupMembershipClaims in het [toepassingsmanifest](reference-app-manifest.md), die ook moet worden ingesteld. Zie hieronder [groepsclaims.](#configuring-groups-optional-claims) Zie [Groepsclaims configureren](../hybrid/how-to-connect-fed-group-claims.md) voor meer informatie over groepsclaims
| `acct`             | Gebruikersaccountstatus in tenant. | JWT, SAML | | Als de gebruiker lid is van de `0`tenant, is de waarde . Als ze te gast zijn, is `1`de waarde. |
| `upn`                      | UserPrincipalName claim. | JWT, SAML  |           | Hoewel deze claim automatisch wordt opgenomen, u deze opgeven als een optionele claim om extra eigenschappen toe te voegen om het gedrag ervan in de gastgebruikersaanvraag te wijzigen.  |

## <a name="v20-specific-optional-claims-set"></a>v2.0-specifieke optionele claimset

Deze claims zijn altijd opgenomen in v1.0 Azure AD-tokens, maar zijn niet opgenomen in v2.0-tokens, tenzij gevraagd. Deze claims zijn alleen van toepassing op JWT's (ID-tokens en Toegangstokens). 

**Tabel 3: optionele claims v2.0**

| JWT Claim     | Name                            | Beschrijving                                | Opmerkingen |
|---------------|---------------------------------|-------------|-------|
| `ipaddr`      | IP-adres                      | Het IP-adres waar de client van is ingelogd.   |       |
| `onprem_sid`  | On-premises beveiligings-id |                                             |       |
| `pwd_exp`     | Wachtwoordverlooptijd        | De datum waarop het wachtwoord verloopt. |       |
| `pwd_url`     | URL wachtwoord wijzigen             | Een URL die de gebruiker kan bezoeken om zijn wachtwoord te wijzigen.   |   |
| `in_corp`     | Binnen bedrijfsnetwerk        | Signalen als de client inlogt vanuit het bedrijfsnetwerk. Als dat niet het zo is, is de claim niet inbegrepen.   |  Gebaseerd op de [vertrouwde IP-instellingen](../authentication/howto-mfa-mfasettings.md#trusted-ips) in MFA.    |
| `nickname`    | Bijnaam                        | Een extra naam voor de gebruiker. De bijnaam staat los van voor- of achternaam. Vereist `profile` de scope.| 
| `family_name` | Achternaam                       | Hiermee geeft u de achternaam, achternaam of familienaam van de gebruiker op, zoals gedefinieerd in het gebruikersobject. <br>"family_name":"Miller" | Ondersteund in MSA en Azure AD. Vereist `profile` de scope.   |
| `given_name`  | Voornaam                      | Hiermee geeft u de eerste of "gegeven" naam van de gebruiker, zoals ingesteld op het gebruikersobject.<br>"given_name": "Frank"                   | Ondersteund in MSA en Azure AD .  Vereist `profile` de scope. |
| `upn`         | User principal name | Een identifer voor de gebruiker die kan worden gebruikt met de parameter username_hint.  Geen duurzame id voor de gebruiker en mag niet worden gebruikt om belangrijke gegevens. | Zie [hieronder aanvullende eigenschappen](#additional-properties-of-optional-claims) voor de configuratie van de claim. Vereist `profile` de scope.|

### <a name="additional-properties-of-optional-claims"></a>Aanvullende eigenschappen van optionele claims

Sommige optionele claims kunnen worden geconfigureerd om de manier waarop de claim wordt geretourneerd te wijzigen. Deze extra eigenschappen worden meestal gebruikt om de migratie van on-premises toepassingen met verschillende gegevensverwachtingen te helpen (bijvoorbeeld `include_externally_authenticated_upn_without_hash` helpt bij clients die hash-markeringen ()`#`niet kunnen verwerken in de UPN)

**Tabel 4: Waarden voor het configureren van optionele claims**

| Naam van eigenschap  | Aanvullende naam van de eigenschap | Beschrijving |
|----------------|--------------------------|-------------|
| `upn`          |                          | Kan worden gebruikt voor zowel SAML- als JWT-antwoorden en voor v1.0- en v2.0-tokens. |
|                | `include_externally_authenticated_upn`  | Inclusief de gast UPN zoals opgeslagen in de resourcetenant. Bijvoorbeeld: `foo_hometenant.com#EXT#@resourcetenant.com` |             
|                | `include_externally_authenticated_upn_without_hash` | Hetzelfde als hierboven, behalve`#`dat de hash merken`_`( ) worden vervangen door underscores ( ), bijvoorbeeld`foo_hometenant.com_EXT_@resourcetenant.com` |

#### <a name="additional-properties-example"></a>Voorbeeld van extra eigenschappen

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

Dit object OptionalClaims zorgt ervoor dat het ID-token dat naar de client wordt teruggestuurd, een upn-claim bevat met de extra tenant van huis en resourcetenantgegevens. De `upn` claim wordt alleen gewijzigd in het token als de gebruiker een gast is in de tenant (die een andere IDP gebruikt voor verificatie). 

## <a name="configuring-optional-claims"></a>Optionele claims configureren

> [!IMPORTANT]
> Toegangstokens worden **altijd** gegenereerd met behulp van het manifest van de bron, niet de client.  Dus in `...scope=https://graph.microsoft.com/user.read...` de aanvraag de bron is de Microsoft Graph API.  Het toegangstoken wordt dus gemaakt met behulp van het Microsoft Graph API-manifest, niet het manifest van de client.  Als u het manifest voor uw toepassing wijzigt, ziet de Microsoft Graph API er nooit anders uit.  Als u wilt `accessToken` valideren of uw wijzigingen van kracht zijn, vraagt u een token aan voor uw toepassing, niet voor een andere app.  


U optionele claims voor uw toepassing configureren via het ui- of toepassingsmanifest.

1. Ga naar de [Azure-portal.](https://portal.azure.com) Zoek naar **Azure Active Directory** en selecteer deze optie.
1. Selecteer **app-registraties**in de sectie **Beheren** .
1. Selecteer de toepassing waarvoor u optionele claims wilt configureren in de lijst.

**Optionele claims configureren via de gebruikersinterface:**

[![Toont hoe u optionele claims configureert met de gebruikersinterface](./media/active-directory-optional-claims/token-configuration.png)](./media/active-directory-optional-claims/token-configuration.png)

1. Selecteer **tokenconfiguratie (voorbeeld)** in de sectie **Beheren** .
2. Selecteer **Optionele claim toevoegen**.
3. Selecteer het tokentype dat u wilt configureren.
4. Selecteer de optionele claims die u wilt toevoegen.
5. Klik op**toevoegen**.

**Optionele claims configureren via het toepassingsmanifest:**

[![Toont hoe u optionele claims configureert met behulp van het app-manifest](./media/active-directory-optional-claims/app-manifest.png)](./media/active-directory-optional-claims/app-manifest.png)

1. Selecteer **Manifest**in de sectie **Beheren** . Er wordt een op het web gebaseerde manifesteditor geopend, zodat u het manifest bewerken. U kunt optioneel ook op **Downloaden** klikken en het manifest lokaal bewerken. Gebruik vervolgens **Uploaden** om het opnieuw toe te passen op de toepassing. Zie het artikel Het manifest [van het Azure AD-toepassingsmanifest begrijpen](reference-app-manifest.md)voor meer informatie over het toepassingsmanifest .

    De volgende toepassing manifest entry voegt de auth_time, ipaddr, en upn optionele claims voor ID, toegang, en SAML tokens.

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

2. Klik op **Opslaan** als u klaar bent. Nu worden de opgegeven optionele claims opgenomen in de tokens voor uw toepassing.    


### <a name="optionalclaims-type"></a>Type OptionieClaim

Declareert de optionele claims die door een aanvraag worden aangevraagd. Een toepassing kan optionele claims configureren die moeten worden geretourneerd in elk van de drie soorten tokens (ID-token, toegangstoken, SAML 2-token) die het kan ontvangen van de beveiligingstokenservice. De toepassing kan een andere set optionele claims configureren die in elk tokentype moeten worden geretourneerd. De eigenschap OptionalClaims van de entiteit Application is een object OptionalClaims.

**Tabel 5: Eigenschappen van het type Optionoption**

| Name        | Type                       | Beschrijving                                           |
|-------------|----------------------------|-------------------------------------------------------|
| `idToken`     | Verzameling (OptionalClaim) | De optionele claims worden geretourneerd in het JWT ID-token. |
| `accessToken` | Verzameling (OptionalClaim) | De optionele claims worden geretourneerd in het JWT-toegangstoken. |
| `saml2Token`  | Verzameling (OptionalClaim) | De optionele claims worden geretourneerd in het SAML-token.   |

### <a name="optionalclaim-type"></a>Optionu.type OptionalClaim optionalClaim option option optional

Bevat een optionele claim die is gekoppeld aan een toepassing of een serviceprincipal. De eigenschappen idToken, accessToken en saml2Token van het type [OptionalClaims](https://docs.microsoft.com/graph/api/resources/optionalclaims?view=graph-rest-1.0) is een verzameling van OptionalClaim.
Als u wordt ondersteund door een specifieke claim, u ook het gedrag van de OptionalClaim wijzigen met het veld ExtraEigenschappen.

**Tabel 6: Eigenschappen van het type OptionalClaim**

| Name                 | Type                    | Beschrijving                                                                                                                                                                                                                                                                                                   |
|----------------------|-------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `name`                 | Edm.String              | De naam van de optionele claim.                                                                                                                                                                                                                                                                           |
| `source`               | Edm.String              | De bron (directoryobject) van de claim. Er zijn vooraf gedefinieerde claims en door de gebruiker gedefinieerde claims van extensie-eigenschappen. Als de bronwaarde null is, is de claim een vooraf gedefinieerde optionele claim. Als de bronwaarde gebruiker is, is de waarde in de eigenschap naam de eigenschap extensie van het gebruikersobject. |
| `essential`            | Edm.Boolean             | Als de waarde waar is, is de claim die door de client is opgegeven, noodzakelijk om een soepele autorisatie-ervaring te garanderen voor de specifieke taak die door de eindgebruiker wordt aangevraagd. De standaardwaarde is false.                                                                                                             |
| `additionalProperties` | Verzameling (Edm.String) | Extra eigenschappen van de claim. Als er een eigenschap in deze verzameling bestaat, wordt het gedrag van de optionele claim die is opgegeven in de eigenschap naam gewijzigd.                                                                                                                                               |
## <a name="configuring-directory-extension-optional-claims"></a>Optionele claims voor directoryextensie configureren

Naast de standaard optionele claimset u ook tokens configureren om extensies op te nemen. Zie voor meer informatie [de Microsoft Graph-extensieProperty documentation](https://docs.microsoft.com/graph/api/resources/extensionproperty?view=graph-rest-1.0) - noteer dat schema en open extensies niet worden ondersteund door optionele claims, alleen de AAD-Graph stijl directory extensies. Deze functie is handig voor het toevoegen van aanvullende gebruikersgegevens die uw app kan gebruiken, bijvoorbeeld een extra id of belangrijke configuratieoptie die de gebruiker heeft ingesteld. Zie de onderkant van deze pagina voor een voorbeeld.

> [!NOTE]
> - Directory-schema-extensies zijn een azure AD-only-functie, dus als uw toepassingsmanifest een aangepaste extensie aanvraagt en een MSA-gebruiker zich aanmeldt bij uw app, worden deze extensies niet geretourneerd.

### <a name="directory-extension-formatting"></a>Opmaak van directory-extensie

Wanneer u optionele claims voor directoryextensie's configureert met behulp van `extension_<appid>_<attributename>`het toepassingsmanifest, gebruikt u de volledige naam van de extensie (in de indeling: ). De `<appid>` moet overeenkomen met de ID van de aanvraag die de claim aanvraagt. 

Binnen de JWT worden deze claims weergegeven met `extn.<attributename>`de volgende naamnotatie: .

Binnen de SAML-tokens worden deze claims uitgezonden met de volgende URI-indeling:`http://schemas.microsoft.com/identity/claims/extn.<attributename>`

## <a name="configuring-groups-optional-claims"></a>Optionele claims voor groepen configureren

   > [!NOTE]
   > De mogelijkheid om groepsnamen uit te zenden voor gebruikers en groepen die zijn gesynchroniseerd vanuit on-premises is Public Preview.

In deze sectie worden de configuratieopties onder optionele claims voor het wijzigen van de groepskenmerken die worden gebruikt in groepsclaims van de standaardgroepobjectID, gebruikt voor kenmerken die zijn gesynchroniseerd met on-premises Windows Active Directory. U optionele claims voor groepen voor uw toepassing configureren via het ui- of toepassingsmanifest.

> [!IMPORTANT]
> Zie [Groepsclaims configureren voor toepassingen met Azure AD](../hybrid/how-to-connect-fed-group-claims.md)voor meer informatie, waaronder belangrijke kanttekeningen voor de openbare preview van groepsclaims van on-premises kenmerken.

**Optionele claims configureren via de gebruikersinterface:**
1. Aanmelden bij de [Azure-portal](https://portal.azure.com)
1. Nadat u uw Azure AD-tenant hebt geverifieerd, kiest u deze door deze te selecteren in de rechterbovenhoek van de pagina
1. **Azure Active Directory selecteren** in het linkermenu
1. Selecteer **app-registraties** onder de sectie **Beheren**
1. Selecteer de toepassing waarvoor u optionele claims wilt configureren in de lijst
1. Selecteer onder de sectie **Beheren** de optie **Tokenconfiguratie (voorbeeld)**
2. Groepenclaim **toevoegen selecteren**
3. Selecteer de groepstypen die u wilt**retourneren (Alle groepen**, **Beveiligingsgroep**of **Maprol).** De optie **Alle groepen** omvat **SecurityGroup,** **DirectoryRole**en **DistributionList**
4. Optioneel: klik op de specifieke eigenschappen van tokentype om de claimwaarde van groepen te wijzigen die op lokalengroepkenmerken moet worden opgenomen of om het claimtype in een rol te wijzigen
5. Klik **op Opslaan**

**Het configureren van optionele claims voor groepen via het toepassingsmanifest:**
1. Aanmelden bij de [Azure-portal](https://portal.azure.com)
1. Nadat u uw Azure AD-tenant hebt geverifieerd, kiest u deze door deze te selecteren in de rechterbovenhoek van de pagina
1. **Azure Active Directory selecteren** in het linkermenu
1. Selecteer de toepassing waarvoor u optionele claims wilt configureren in de lijst
1. Selecteer **Manifest** onder de sectie **Beheren**
3. Voeg het volgende item toe met de manifesteditor:

   De geldige waarden zijn:

   - 'Alles' (deze optie omvat SecurityGroup, DirectoryRole en DistributionList)
   - "SecurityGroup"
   - "DirectoryRole"

   Bijvoorbeeld:

    ```json
        "groupMembershipClaims": "SecurityGroup"
    ```

   Standaard worden groepsobjectinstellingen uitgestoten in de groepsclaimwaarde.  Als u de claimwaarde wilt wijzigen om kenmerken van de lokalengroep te bevatten of om het claimtype in rol te wijzigen, gebruikt u de configuratie OptionalClaims als volgt:

3. Stel optionele claims voor groepsnaamconfiguratie in.

   Als u wilt groepen in het token om de ad-groepskenmerken op locatie te bevatten in de optionele sectie claims, opgeven op welk tokentype optionele claim moet worden toegepast, de naam van de gevraagde optionele claim en eventuele gewenste extra eigenschappen.  Er kunnen meerdere tokentypen worden weergegeven:

   - idToken voor het OIDC ID-token
   - accessToken voor het OAuth-toegangstoken
   - Saml2Token voor SAML-tokens.

   > [!NOTE]
   > Het Saml2Token-type is van toepassing op zowel SAML1.1- als SAML2.0-formaattokens

   Wijzig voor elk relevant tokentype de claim groepen om de sectie OptionalClaims in het manifest te gebruiken. Het schema OptionalClaims is als volgt:

    ```json
       {
       "name": "groups",
       "source": null,
       "essential": false,
       "additionalProperties": []
       }
    ```

   | Optioneel claimschema | Waarde |
   |----------|-------------|
   | **Naam:** | Moet "groepen" |
   | **Bron:** | Niet gebruikt. Null weglaten of opgeven |
   | **Essentiële:** | Niet gebruikt. Onwaar weglaten of opgeven |
   | **additionalProperties:** | Lijst met extra eigenschappen.  Geldige opties zijn "sam_account_name", "dns_domain_and_sam_account_name", "netbios_domain_and_sam_account_name", "emit_as_roles" |

   In additionalProperties zijn slechts één van "sam_account_name", "dns_domain_and_sam_account_name", "netbios_domain_and_sam_account_name" vereist.  Als er meer dan één aanwezig is, wordt de eerste gebruikt en alle anderen genegeerd.

   Voor sommige toepassingen is groepsinformatie over de gebruiker vereist in de rolclaim.  Als u het claimtype wilt wijzigen in een groepsclaim in een rolclaim, voegt u 'emit_as_roles' toe aan extra eigenschappen.  De groepswaarden worden weergegeven in de rolclaim.

   > [!NOTE]
   > Als 'emit_as_roles' wordt gebruikt, worden toepassingsrollen die zijn geconfigureerd en die de gebruiker toegewezen heeft, niet weergegeven in de rolclaim

**Voorbeelden:**

1) Groepen uitzenden als groepsnamen in OAuth-toegangstokens in dnsDomainName\sAMAccountName-indeling

    
    **UI-configuratie:**

    [![Toont hoe u optionele claims configureert met de gebruikersinterface](./media/active-directory-optional-claims/groups-example-1.png)](./media/active-directory-optional-claims/groups-example-1.png)


    **Inschrijving manifest vermelding:**
    ```json
        "optionalClaims": {
            "accessToken": [{
            "name": "groups",
            "additionalProperties": ["dns_domain_and_sam_account_name"]
            }]
        }
    ```

 
    
2) Groepsnamen uitzenden die moeten worden geretourneerd in de netbiosDomain\sAMAccountName-indeling als de rolclaim in SAML- en OIDC ID-tokens

    **UI-configuratie:**

    [![Toont hoe u optionele claims configureert met de gebruikersinterface](./media/active-directory-optional-claims/groups-example-2.png)](./media/active-directory-optional-claims/groups-example-2.png)

    **Inschrijving manifest vermelding:**
    
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
     

## <a name="optional-claims-example"></a>Voorbeeld van optionele claims

In deze sectie u een scenario doorlopen om te zien hoe u de optionele claimfunctie voor uw toepassing gebruiken.
Er zijn meerdere opties beschikbaar voor het bijwerken van de eigenschappen van de identiteitsconfiguratie van een toepassing om optionele claims in te schakelen en te configureren:
-    U de gebruikersinterface **voor tokenconfiguratie (voorbeeld)** gebruiken (zie voorbeeld hieronder)
-    U het **manifest** gebruiken (zie voorbeeld hieronder). Lees [eerst het manifestdocument van de Azure AD-toepassingsvoor](https://docs.microsoft.com/azure/active-directory/develop/active-directory-application-manifest) een inleiding tot het manifest.
-   Het is ook mogelijk om een toepassing te schrijven die de [Microsoft Graph API](https://docs.microsoft.com/graph/use-the-api?context=graph%2Fapi%2F1.0&view=graph-rest-1.0) gebruikt om uw toepassing bij te werken. Het type [OptionalClaims](https://docs.microsoft.com/graph/api/resources/optionalclaims?view=graph-rest-1.0) in de referentiegids voor Microsoft Graph API kan u helpen bij het configureren van de optionele claims.

**Voorbeeld:** In het onderstaande voorbeeld gebruikt u de gebruikersinterface en **het manifest** **voor tokenconfiguratie (voorbeeld)** om optionele claims toe te voegen aan de toegangs-, ID- en SAML-tokens die zijn bedoeld voor uw toepassing. Aan elk type token dat de toepassing kan ontvangen, worden verschillende optionele claims toegevoegd:
-    De ID-tokens bevatten nu de UPN voor federatieve`<upn>_<homedomain>#EXT#@<resourcedomain>`gebruikers in de volledige vorm ( ).
-    De toegangstokens die andere clients voor deze toepassing aanvragen, bevatten nu de auth_time claim
-    De SAML-tokens bevatten nu de skypeId-directoryschema-extensie (in dit voorbeeld is de app-id voor deze app ab603c56068041afb2f6832e2a17e237). De SAML-tokens zullen de `extension_skypeId`Skype-id blootstellen als .

**UI-configuratie:**

1. Aanmelden bij de [Azure-portal](https://portal.azure.com)

1. Nadat u uw Azure AD-tenant hebt geverifieerd, kiest u deze door deze te selecteren in de rechterbovenhoek van de pagina.

1. Selecteer **Azure Active Directory** in het linkermenu.

1. Selecteer **app-registraties**onder de sectie **Beheren** .

1. Zoek de toepassing waarvoor u optionele claims wilt configureren in de lijst en klik erop.

1. Klik onder de sectie **Beheren** op **Tokenconfiguratie (voorbeeld)**.

1. Selecteer **Optionele claim toevoegen,** selecteer het type **ID-token,** selecteer **upn** in de lijst met claims en klik op **Toevoegen**.

1. Selecteer **Optionele claim toevoegen,** selecteer het type **Access-token,** selecteer **auth_time** in de lijst met claims en klik op **Toevoegen**.

1. Klik in het overzichtsscherm Tokenconfiguratie op het potloodpictogram naast **upn,** klik op de **extern geverifieerde** schakelaar en klik vervolgens op **Opslaan**.

1. Selecteer **Optionele claim toevoegen,** selecteer het **SAML-tokentype,** selecteer **extn.skypeID** in de lijst met claims (alleen van toepassing als u een Azure AD-gebruikersobject genaamd SkypeID hebt gemaakt) en klik vervolgens op **Toevoegen**.

    [![Toont hoe u optionele claims configureert met de gebruikersinterface](./media/active-directory-optional-claims/token-config-example.png)](./media/active-directory-optional-claims/token-config-example.png)

**Manifestconfiguratie:**
1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Nadat u uw Azure AD-tenant hebt geverifieerd, kiest u deze door deze te selecteren in de rechterbovenhoek van de pagina.
1. Selecteer **Azure Active Directory** in het linkermenu.
1. Zoek de toepassing waarvoor u optionele claims wilt configureren in de lijst en klik erop.
1. Klik onder de sectie **Beheren** op **Manifest** om de inline manifesteditor te openen.
1. U het manifest direct bewerken met behulp van deze editor. Het manifest volgt het schema voor de [entiteit Toepassing](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest)en maakt het manifest automatisch op nadat het is opgeslagen. Nieuwe elementen worden toegevoegd `OptionalClaims` aan de eigenschap.

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
