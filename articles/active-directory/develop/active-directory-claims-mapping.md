---
title: Azure AD-tenantappclaims aanpassen (PowerShell)
titleSuffix: Microsoft identity platform
description: Op deze pagina wordt beschreven toewijzing van Azure Active Directory-claims.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin, jeedes, luleon
ms.openlocfilehash: 49860504da8dd2a1b994a23a24df95f59c959c90
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263190"
---
# <a name="how-to-customize-claims-emitted-in-tokens-for-a-specific-app-in-a-tenant-preview"></a>How to: Claims aanpassen die worden uitgezonden in tokens voor een specifieke app in een tenant (Preview)

> [!NOTE]
> Deze functie vervangt en vervangt de [claims aanpassing](active-directory-saml-claims-customization.md) aangeboden via de portal vandaag. Als u claims aanpast met behulp van de portal naast de grafiek/PowerShell-methode die in dit document wordt beschreven, negeren tokens die voor die toepassing zijn uitgegeven, de configuratie in de portal. Configuraties die zijn gemaakt via de methoden die in dit document worden beschreven, worden niet weergegeven in de portal.

Deze functie wordt gebruikt door tenantbeheerders om de claims aan te passen die worden uitgezonden in tokens voor een specifieke toepassing in hun tenant. U beleid voor het toewijzen van claims gebruiken om:

- Selecteer welke claims zijn opgenomen in tokens.
- Claimtypen maken die nog niet bestaan.
- Kies of wijzig de bron van gegevens die worden uitgezonden in specifieke claims.

> [!NOTE]
> Deze mogelijkheid bevindt zich momenteel in een openbare preview. Wees voorbereid om terug te keren of eventuele wijzigingen te verwijderen. De functie is beschikbaar in elk Azure Active Directory-abonnement (Azure AD) tijdens openbare preview. Wanneer de functie echter algemeen beschikbaar wordt, zijn voor sommige aspecten van de functie mogelijk een Azure AD Premium-abonnement vereist. Deze functie ondersteunt het configureren van claimtoewijzingsbeleid voor WS-Fed-, SAML-, OAuth- en OpenID Connect-protocollen.

## <a name="claims-mapping-policy-type"></a>Beleidstype claimmapping

In Azure AD vertegenwoordigt een **object Beleid** een set regels die worden afgedwongen voor afzonderlijke toepassingen of voor alle toepassingen in een organisatie. Elk type beleid heeft een unieke structuur, met een set eigenschappen die vervolgens worden toegepast op objecten waaraan ze zijn toegewezen.

Een beleid voor het toewijzen van claims is een type **beleidsobject** dat de claims wijzigt die worden uitgegeven in tokens die zijn uitgegeven voor specifieke toepassingen.

## <a name="claim-sets"></a>Claimsets

Er zijn bepaalde sets claims die bepalen hoe en wanneer ze worden gebruikt in tokens.

| Claimset | Beschrijving |
|---|---|
| Core claim set | Zijn aanwezig in elk token, ongeacht het beleid. Deze claims worden ook als beperkt beschouwd en kunnen niet worden gewijzigd. |
| Basisclaimset | Inclusief de claims die standaard worden uitgestoten voor tokens (naast de basisclaimset). U basisclaims weglaten of wijzigen met behulp van het beleid voor het toewijzen van claims. |
| Beperkte claimset | Kan niet worden gewijzigd met behulp van beleid. De gegevensbron kan niet worden gewijzigd en er wordt geen transformatie toegepast bij het genereren van deze claims. |

### <a name="table-1-json-web-token-jwt-restricted-claim-set"></a>Tabel 1: Json Web Token (JWT) beperkte claimset

| Claimtype (naam) |
| ----- |
| _claim_names |
| _claim_sources |
| access_token |
| account_type |
| acr |
| Acteur |
| actortoken |
| Aio |
| altsecid (altsecid) |
| Amr |
| app_chain |
| app_displayname |
| app_res |
| appctx appctx |
| appctxsender |
| Appid |
| appidacr appidacr |
| Bewering |
| at_hash |
| aud |
| auth_data |
| auth_time |
| authorization_code |
| azp azp |
| azpacr azpacr |
| c_hash |
| ca_enf |
| cc |
| cert_token_use |
| client_id |
| cloud_graph_host_name |
| cloud_instance_name |
| Cnf |
| code |
| besturingselementen |
| credential_keys |
| Mvo |
| csr_type |
| deviceid |
| dns_names |
| domain_dns_name |
| domain_netbios_name |
| e_exp |
| e-mail |
| endpoint |
| enfpolids |
| Exp |
| expires_on |
| grant_type |
| Grafiek |
| group_sids |
| groepen |
| hasgroups |
| hash_alg |
| home_oid |
| `http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationinstant` |
| `http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod` |
| `http://schemas.microsoft.com/ws/2008/06/identity/claims/expiration` |
| `http://schemas.microsoft.com/ws/2008/06/identity/claims/expired` |
| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` |
| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` |
| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` |
| Iat |
| identityprovider |
| Idp |
| in_corp |
| Exemplaar |
| Ipaddr |
| isbrowserhostedapp |
| Iss |
| jwk jwk |
| key_id |
| key_type |
| mam_compliance_url |
| mam_enrollment_url |
| mam_terms_of_use_url |
| mdm_compliance_url |
| mdm_enrollment_url |
| mdm_terms_of_use_url |
| naamid |
| nbf (nbf) |
| netbios_name |
| Nonce |
| Oid |
| on_prem_id |
| onprem_sam_account_name |
| onprem_sid |
| openid2_id |
| wachtwoord |
| platf |
| polids |
| pop_jwk |
| preferred_username |
| previous_refresh_token |
| primary_sid |
| puid puid |
| pwd_exp |
| pwd_url |
| redirect_uri |
| refresh_token |
| refreshtoken |
| request_nonce |
| resource |
| role |
| rolls |
| scope |
| Scp |
| Sid |
| Handtekening |
| signin_state |
| src1 |
| src2 |
| sub |
| tbid |
| tenant_display_name |
| tenant_region_scope |
| thumbnail_photo |
| Tid |
| tokenAutologonIngeschakeld |
| trustedfordelegation |
| unique_name |
| Upn |
| user_setting_sync_url |
| gebruikersnaam |
| Uti |
| Ver |
| verified_primary_email |
| verified_secondary_email |
| wids wids wids |
| win_ver |

### <a name="table-2-saml-restricted-claim-set"></a>Tabel 2: SAML-set voor beperkte claim

| Claimtype (URI) |
| ----- |
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/expiration`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/expired`|
|`http://schemas.microsoft.com/identity/claims/accesstoken`|
|`http://schemas.microsoft.com/identity/claims/openid2_id`|
|`http://schemas.microsoft.com/identity/claims/identityprovider`|
|`http://schemas.microsoft.com/identity/claims/objectidentifier`|
|`http://schemas.microsoft.com/identity/claims/puid`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier [MR1]`|
|`http://schemas.microsoft.com/identity/claims/tenantid`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationinstant`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod`|
|`http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/groups`|
|`http://schemas.microsoft.com/claims/groups.link`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/role`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/wids`|
|`http://schemas.microsoft.com/2014/09/devicecontext/claims/iscompliant`|
|`http://schemas.microsoft.com/2014/02/devicecontext/claims/isknown`|
|`http://schemas.microsoft.com/2012/01/devicecontext/claims/ismanaged`|
|`http://schemas.microsoft.com/2014/03/psso`|
|`http://schemas.microsoft.com/claims/authnmethodsreferences`|
|`http://schemas.xmlsoap.org/ws/2009/09/identity/claims/actor`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/samlissuername`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/confirmationkey`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/primarygroupsid`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/authorizationdecision`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/authentication`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/sid`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlyprimarygroupsid`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlyprimarysid`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/denyonlysid`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlywindowsdevicegroup`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsdeviceclaim`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsdevicegroup`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsfqbnversion`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowssubauthority`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsuserclaim`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/x500distinguishedname`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/ispersistent`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/privatepersonalidentifier`|
|`http://schemas.microsoft.com/identity/claims/scope`|

## <a name="claims-mapping-policy-properties"></a>Eigenschappen van beleid voor het toewijzen van claims

Als u wilt bepalen welke claims worden uitgestoten en waar de gegevens vandaan komen, gebruikt u de eigenschappen van een beleid voor het toewijzen van claims. Als een beleid niet is ingesteld, geeft het systeem tokens uit die de basisclaimset, de basisclaimset en eventuele [optionele claims](active-directory-optional-claims.md) bevatten die de toepassing heeft gekozen om te ontvangen.

### <a name="include-basic-claim-set"></a>Basisclaimset opnemen

**Tekenreeks:** IncludeBasicClaimSet

**Gegevenstype:** Boolean (waar of onwaar)

**Samenvatting:** Deze eigenschap bepaalt of de basisclaimset is opgenomen in tokens die door dit beleid worden beïnvloed.

- Als deze is ingesteld op True, worden alle claims in de basisclaimset uitgestoten in tokens die door het beleid worden beïnvloed. 
- Als deze is ingesteld op False, staan claims in de basisclaimset niet in de tokens, tenzij ze afzonderlijk worden toegevoegd in de eigenschap claimschema van hetzelfde beleid.

> [!NOTE] 
> Claims in de kernclaimset zijn aanwezig in elk token, ongeacht waar deze eigenschap op is ingesteld. 

### <a name="claims-schema"></a>Schadeschema

**Tekenreeks:** Claimschema

**Gegevenstype:** JSON-blob met een of meer claimschemavermeldingen

**Samenvatting:** Deze eigenschap definieert welke claims aanwezig zijn in de tokens die door het beleid worden beïnvloed, naast de basisclaimset en de basisclaimset.
Voor elke claimschemavermelding die in deze eigenschap is gedefinieerd, is bepaalde informatie vereist. Geef op waar de gegevens vandaan komen (**Waarde** of **Bron/ID-paar**) en welke claim dat de gegevens worden uitgezonden als (**Claimtype**).

### <a name="claim-schema-entry-elements"></a>Elementen voor schema-invoer claimen

**Waarde:** Het element Waarde definieert een statische waarde als de gegevens die in de claim moeten worden uitgezonden.

**Bron/ID-paar:** De bron- en id-elementen bepalen waar de gegevens in de claim vandaan komen. 

Stel het element Bron in op een van de volgende waarden: 

- "gebruiker": De gegevens in de claim zijn een eigenschap op het object Gebruiker. 
- "toepassing": De gegevens in de claim zijn een eigenschap op de applicatie (client) service principal. 
- "resource": De gegevens in de claim zijn een eigenschap op de resourceserviceprincipal.
- 'doelgroep': De gegevens in de claim zijn een eigenschap op de serviceprincipal die het publiek van het token is (de client of resourceserviceprincipal).
- "bedrijf": De gegevens in de claim zijn een eigenschap op het bedrijfsobject van de resourcetenant.
- "transformatie": De gegevens in de claim zijn afkomstig van claimstransformatie (zie de sectie 'Transformatie van claims' later in dit artikel).

Als de bron transformatie is, moet het **Element TransformationID** ook in deze claimdefinitie worden opgenomen.

Het ID-element identificeert welke eigenschap op de bron de waarde voor de claim geeft. In de volgende tabel worden de waarden weergegeven van id die geldig is voor elke waarde van de bron.

#### <a name="table-3-valid-id-values-per-source"></a>Tabel 3: Geldige ID-waarden per bron

| Bron | Id | Beschrijving |
|-----|-----|-----|
| Gebruiker | surname | Familienaam |
| Gebruiker | givenname | Voornaam |
| Gebruiker | displayname | Weergavenaam |
| Gebruiker | objectid | ObjectID |
| Gebruiker | mail | E-mailadres |
| Gebruiker | userprincipalname | User principal name |
| Gebruiker | department|Afdeling|
| Gebruiker | onpremisessamaccountnaam | On-premises SAM-accountnaam |
| Gebruiker | netbiosnaam| NetBios-naam |
| Gebruiker | dnsdomainname | DNS-domeinnaam |
| Gebruiker | onpremisesecurityidentifier | On-premises beveiligings-id |
| Gebruiker | Bedrijfsnaam| Organisatienaam |
| Gebruiker | adres | Adres |
| Gebruiker | Postcode | Postcode |
| Gebruiker | preferredlanguange preferredlanguange preferredlanguange preferredlang | Voorkeurstaal |
| Gebruiker | onpremisesuserprincipalname | On-premises UPN |
| Gebruiker | mailnickname | Bijnaam e-mail |
| Gebruiker | extensieattribuut1 | Extensiekenmerk 1 |
| Gebruiker | extensieattribuut2 | Extensiekenmerk 2 |
| Gebruiker | extensieattribuut3 | Extensiekenmerk 3 |
| Gebruiker | extensieattribuut4 | Extensiekenmerk 4 |
| Gebruiker | extensieattribuut5 | Extensiekenmerk 5 |
| Gebruiker | extensieattribuut6 | Extensiekenmerk 6 |
| Gebruiker | extensieattribuut7 | Extensiekenmerk 7 |
| Gebruiker | extensieattribuut8 | Extensiekenmerk 8 |
| Gebruiker | extensieattribuut9 | Extensiekenmerk 9 |
| Gebruiker | extensieattribuut10 | Extensiekenmerk 10 |
| Gebruiker | extensieattribuut11 | Extensiekenmerk 11 |
| Gebruiker | extensieattribuut12 | Extensiekenmerk 12 |
| Gebruiker | extensieattribuut13 | Extensiekenmerk 13 |
| Gebruiker | extensieattribuut14 | Extensiekenmerk 14 |
| Gebruiker | extensieattribuut15 | Extensiekenmerk 15 |
| Gebruiker | othermail | Andere e-mail |
| Gebruiker | land | Land |
| Gebruiker | city | Plaats |
| Gebruiker | state | Status |
| Gebruiker | functietitel | Functie |
| Gebruiker | employeeid | Werknemer-id |
| Gebruiker | faxnummer | Facsimile telefoonnummer |
| toepassing, resource, doelgroep | displayname | Weergavenaam |
| toepassing, resource, doelgroep | Bezwaar | ObjectID |
| toepassing, resource, doelgroep | tags | Tag serviceprincipal |
| Bedrijf | huurderland | Het land van de huurder |

**TransformationID:** Het Element TransformationID mag alleen worden verstrekt als het bronelement is ingesteld op "transformatie".

- Dit element moet overeenkomen met het ID-element van de transformatievermelding in de eigenschap **ClaimsTransformation** waarmee wordt gedefinieerd hoe de gegevens voor deze claim worden gegenereerd.

**Claimtype:** De **Elementen JwtClaimType** en **SamlClaimType** definiëren naar welke claimdeze claimschema-vermelding verwijst.

- Het JwtClaimType moet de naam bevatten van de claim die in JWT's moet worden uitgezonden.
- Het SamlClaimType moet de URI bevatten van de claim die moet worden uitgezonden in SAML-tokens.

> [!NOTE]
> Namen en URI's van claims in de beperkte claimset kunnen niet worden gebruikt voor de elementen van het claimtype. Zie de sectie 'Uitzonderingen en beperkingen' later in dit artikel voor meer informatie.

### <a name="claims-transformation"></a>Claimstransformatie

**Tekenreeks:** ClaimsTransformatie

**Gegevenstype:** JSON-blob, met een of meer transformatievermeldingen 

**Samenvatting:** Gebruik deze eigenschap om algemene transformaties toe te passen op brongegevens om de uitvoergegevens te genereren voor claims die zijn opgegeven in het claimschema.

**ID:** Gebruik het element ID om naar deze transformatievermelding te verwijzen in het item TransformationID Claims Schema. Deze waarde moet uniek zijn voor elke transformatievermelding binnen dit beleid.

**TransformationMethod:** Het element TransformationMethod identificeert welke bewerking wordt uitgevoerd om de gegevens voor de claim te genereren.

Op basis van de gekozen methode wordt een reeks ingangen en uitgangen verwacht. Definieer de ingangen en uitgangen met behulp van de elementen **InputClaims**, **InputParameters** en **OutputClaims.**

#### <a name="table-4-transformation-methods-and-expected-inputs-and-outputs"></a>Tabel 4: Transformatiemethoden en verwachte ingangen en uitgangen

|Transformatiemethode|Verwachte invoer|Verwachte uitvoer|Beschrijving|
|-----|-----|-----|-----|
|Koppelen|tekenreeks1, tekenreeks2, scheidingsteken|outputClaim|Hiermee worden invoertekenreeksen samengebracht met behulp van een scheidingsteken ertussen. Bijvoorbeeld: tekenreeks1:"foo@bar.com" , tekenreeks2:"sandbox" , scheidingsteken:"."foo@bar.com.sandboxresulteert in outputClaim:" "|
|ExtractMailVoorvoegsel|mail|outputClaim|Haalt het lokale deel van een e-mailadres uit. Bijvoorbeeld: e-mail:"foo@bar.com" resulteert in outputClaim:"foo". Als \@ er geen teken aanwezig is, wordt de oorspronkelijke invoertekenreeks geretourneerd zoals het is.|

**Invoerclaims:** Gebruik een element InputClaims om de gegevens van een claimschema-vermelding door te geven aan een transformatie. Het heeft twee kenmerken: **ClaimTypeReferenceId** en **TransformationClaimType**.

- **ClaimTypeReferenceId** wordt samengevoegd met ID-element van de vermelding van het claimschema om de juiste invoerclaim te vinden. 
- **TransformationClaimType** wordt gebruikt om een unieke naam te geven aan deze invoer. Deze naam moet overeenkomen met een van de verwachte ingangen voor de transformatiemethode.

**Invoerparameters:** Gebruik een element InputParameters om een constante waarde door te geven aan een transformatie. Het heeft twee kenmerken: **Waarde** en **ID**.

- **Waarde** is de werkelijke constante waarde die moet worden doorgegeven.
- **ID** wordt gebruikt om een unieke naam te geven aan de invoer. De naam moet overeenkomen met een van de verwachte ingangen voor de transformatiemethode.

**OutputClaims:** Gebruik een element OutputClaims om de gegevens die door een transformatie worden gegenereerd vast te houden en deze te koppelen aan een claimschema-vermelding. Het heeft twee kenmerken: **ClaimTypeReferenceId** en **TransformationClaimType**.

- **ClaimTypeReferenceId** wordt samengevoegd met de id van de vermelding van het claimschema om de juiste uitvoerclaim te vinden.
- **TransformationClaimType** wordt gebruikt om een unieke naam te geven aan de uitvoer. De naam moet overeenkomen met een van de verwachte uitgangen voor de transformatiemethode.

### <a name="exceptions-and-restrictions"></a>Uitzonderingen en beperkingen

**SAML NameID en UPN:** De kenmerken waarvan u de waarden NameID en UPN en de toegestane claimtransformaties invoert, zijn beperkt. Zie tabel 5 en tabel 6 om de toegestane waarden te bekijken.

#### <a name="table-5-attributes-allowed-as-a-data-source-for-saml-nameid"></a>Tabel 5: Kenmerken die zijn toegestaan als gegevensbron voor SAML NameID

|Bron|Id|Beschrijving|
|-----|-----|-----|
| Gebruiker | mail|E-mailadres|
| Gebruiker | userprincipalname|User principal name|
| Gebruiker | onpremisessamaccountnaam|On Premises Sam-accountnaam|
| Gebruiker | employeeid|Werknemer-id|
| Gebruiker | extensieattribuut1 | Extensiekenmerk 1 |
| Gebruiker | extensieattribuut2 | Extensiekenmerk 2 |
| Gebruiker | extensieattribuut3 | Extensiekenmerk 3 |
| Gebruiker | extensieattribuut4 | Extensiekenmerk 4 |
| Gebruiker | extensieattribuut5 | Extensiekenmerk 5 |
| Gebruiker | extensieattribuut6 | Extensiekenmerk 6 |
| Gebruiker | extensieattribuut7 | Extensiekenmerk 7 |
| Gebruiker | extensieattribuut8 | Extensiekenmerk 8 |
| Gebruiker | extensieattribuut9 | Extensiekenmerk 9 |
| Gebruiker | extensieattribuut10 | Extensiekenmerk 10 |
| Gebruiker | extensieattribuut11 | Extensiekenmerk 11 |
| Gebruiker | extensieattribuut12 | Extensiekenmerk 12 |
| Gebruiker | extensieattribuut13 | Extensiekenmerk 13 |
| Gebruiker | extensieattribuut14 | Extensiekenmerk 14 |
| Gebruiker | extensieattribuut15 | Extensiekenmerk 15 |

#### <a name="table-6-transformation-methods-allowed-for-saml-nameid"></a>Tabel 6: Transformatiemethoden toegestaan voor SAML NameID

| Transformatiemethode | Beperkingen |
| ----- | ----- |
| ExtractMailVoorvoegsel | Geen |
| Koppelen | Het achtervoegsel dat wordt samengevoegd, moet een geverifieerd domein van de resourcetenant zijn. |

### <a name="custom-signing-key"></a>Aangepaste ondertekeningssleutel

Er moet een aangepaste ondertekeningssleutel worden toegewezen aan het hoofdobject van de service om een beleid voor het toewijzen van claims van kracht te laten worden. Dit zorgt ervoor dat de maker van het beleid voor het in kaart brengen van claims is gewijzigd en dat toepassingen worden beschermd tegen beleid voor het toewijzen van claims dat is gemaakt door kwaadwillende actoren. Als u een aangepaste ondertekeningssleutel wilt toevoegen, `new-azureadapplicationkeycredential` u de Azure PowerShell-cmdlet gebruiken om een symmetrische sleutelreferentie voor uw toepassingsobject te maken. Zie [Nieuw-AzureADApplicationKeyCredential](https://docs.microsoft.com/powerShell/module/Azuread/New-AzureADApplicationKeyCredential?view=azureadps-2.0)voor meer informatie over deze Azure PowerShell-cmdlet.

Apps die claimtoewijzing hebben ingeschakeld, moeten hun `appid={client_id}` tokenondertekeningssleutels valideren door toe te voegen aan hun [metagegevensverzoeken van OpenID Connect.](v2-protocols-oidc.md#fetch-the-openid-connect-metadata-document) Hieronder vindt u de indeling van het OpenID Connect-metagegevensdocument dat u moet gebruiken: 

```
https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration?appid={client-id}
```

### <a name="cross-tenant-scenarios"></a>Scenario's voor meerdere huurders

Beleid voor het toewijzen van claims is niet van toepassing op gastgebruikers. Als een gastgebruiker toegang probeert te krijgen tot een toepassing met een claimtoewijzingsbeleid dat is toegewezen aan de serviceprincipal, wordt het standaardtoken uitgegeven (het beleid heeft geen effect).

## <a name="claims-mapping-policy-assignment"></a>Beleidstoewijzing voor het in kaart brengen van claims

Het toewijzingsbeleid voor claims kan alleen worden toegewezen aan hoofdobjecten van de service.

### <a name="example-claims-mapping-policies"></a>Voorbeeldbeleid voor het in kaart brengen van claims

In Azure AD zijn veel scenario's mogelijk wanneer u claims aanpassen die worden uitgezonden in tokens voor specifieke serviceprincipals. In deze sectie doorlopen we een aantal veelvoorkomende scenario's die u kunnen helpen begrijpen hoe u het beleidstype voor het toewijzen van claims gebruiken.

#### <a name="prerequisites"></a>Vereisten

In de volgende voorbeelden maakt, updatet, linkt en verwijdert u beleidsregels voor serviceprincipals. Als u nieuw bent in Azure AD, raden we u [aan meer te weten te komen over het aanschaffen van een Azure AD-tenant](quickstart-create-new-tenant.md) voordat u verdergaat met deze voorbeelden.

Ga als volgt te werk om aan de slag te gaan:

1. Download de nieuwste [openbare preview-versie van azure AD PowerShell Module](https://www.powershellgallery.com/packages/AzureADPreview).
1. Voer de opdracht Verbinding uit om u aan te melden bij uw Azure AD-beheerdersaccount. Voer deze opdracht uit telkens wanneer u een nieuwe sessie start.

   ``` powershell
   Connect-AzureAD -Confirm
   ```
1. Voer de volgende opdracht uit om alle beleidsregels te bekijken die in uw organisatie zijn gemaakt. We raden u aan deze opdracht uit te voeren na de meeste bewerkingen in de volgende scenario's, om te controleren of uw beleid wordt gemaakt zoals verwacht.

   ``` powershell
   Get-AzureADPolicy
   ```

#### <a name="example-create-and-assign-a-policy-to-omit-the-basic-claims-from-tokens-issued-to-a-service-principal"></a>Voorbeeld: Een beleid maken en toewijzen om de basisclaims van tokens die zijn uitgegeven aan een serviceprincipal weg te laten

In dit voorbeeld maakt u een beleid dat de basisclaimset verwijdert van tokens die zijn uitgegeven aan gekoppelde serviceprincipals.

1. Maak een beleid voor het in kaart brengen van claims. Dit beleid, gekoppeld aan specifieke serviceprincipals, verwijdert de basisclaimset van tokens.
   1. Voer de opdracht uit om het beleid te maken: 
    
      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"false"}}') -DisplayName "OmitBasicClaims" -Type "ClaimsMappingPolicy"
      ```
   2. Voer de volgende opdracht uit om uw nieuwe beleid te bekijken en de beleidsobjectid op te halen:
    
      ``` powershell
      Get-AzureADPolicy
      ```
1. Wijs het beleid toe aan uw serviceprincipal. U moet ook de ObjectId van uw serviceprincipal krijgen.
   1. Als u alle serviceprincipals van uw organisatie wilt bekijken, u [de Microsoft Graph-API opvragen.](/graph/traverse-the-graph) Of meld u in [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)aan bij uw Azure AD-account.
   2. Wanneer u de ObjectId van uw serviceprincipal hebt, voert u de volgende opdracht uit:  
     
      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

#### <a name="example-create-and-assign-a-policy-to-include-the-employeeid-and-tenantcountry-as-claims-in-tokens-issued-to-a-service-principal"></a>Voorbeeld: Een beleid maken en toewijzen om de EmployeeID en TenantCountry op te nemen als claims in tokens die zijn uitgegeven aan een serviceprincipal

In dit voorbeeld maakt u een beleid dat de EmployeeID en TenantCountry toevoegt aan tokens die zijn uitgegeven aan gekoppelde serviceprincipals. De EmployeeID wordt uitgezonden als het type naamclaim in zowel SAML-tokens als JWT's. Het TenantCountry wordt uitgestoten als het landclaimtype in zowel SAML-tokens als JWT's. In dit voorbeeld blijven we de basisclaims opnemen in de tokens.

1. Maak een beleid voor het in kaart brengen van claims. Dit beleid, gekoppeld aan specifieke serviceprincipals, voegt de claims EmployeeID en TenantCountry toe aan tokens.
   1. Voer de volgende opdracht uit om het beleid te maken:  
     
      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema": [{"Source":"user","ID":"employeeid","SamlClaimType":"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name","JwtClaimType":"name"},{"Source":"company","ID":"tenantcountry","SamlClaimType":"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/country","JwtClaimType":"country"}]}}') -DisplayName "ExtraClaimsExample" -Type "ClaimsMappingPolicy"
      ```
    
   2. Voer de volgende opdracht uit om uw nieuwe beleid te bekijken en de beleidsobjectid op te halen:
     
      ``` powershell  
      Get-AzureADPolicy
      ```
1. Wijs het beleid toe aan uw serviceprincipal. U moet ook de ObjectId van uw serviceprincipal krijgen. 
   1. Als u alle serviceprincipals van uw organisatie wilt bekijken, u [de Microsoft Graph-API opvragen.](/graph/traverse-the-graph) Of meld u in [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)aan bij uw Azure AD-account.
   2. Wanneer u de ObjectId van uw serviceprincipal hebt, voert u de volgende opdracht uit:  
     
      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

#### <a name="example-create-and-assign-a-policy-that-uses-a-claims-transformation-in-tokens-issued-to-a-service-principal"></a>Voorbeeld: Een beleid maken en toewijzen dat een claimtransformatie gebruikt in tokens die zijn uitgegeven aan een serviceprincipal

In dit voorbeeld maakt u een beleid dat een aangepaste claim 'JoinedData' uitzendt naar JWT's die zijn uitgegeven aan gekoppelde serviceprincipals. Deze claim bevat een waarde die is gemaakt door de gegevens die zijn opgeslagen in het kenmerk extensionattribute1 op het gebruikersobject samen te voegen met ".sandbox". In dit voorbeeld sluiten we de basisclaims uit die in de tokens zijn ingesteld.

1. Maak een beleid voor het in kaart brengen van claims. Dit beleid, gekoppeld aan specifieke serviceprincipals, voegt de claims EmployeeID en TenantCountry toe aan tokens.
   1. Voer de volgende opdracht uit om het beleid te maken:
     
      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema":[{"Source":"user","ID":"extensionattribute1"},{"Source":"transformation","ID":"DataJoin","TransformationId":"JoinTheData","JwtClaimType":"JoinedData"}],"ClaimsTransformations":[{"ID":"JoinTheData","TransformationMethod":"Join","InputClaims":[{"ClaimTypeReferenceId":"extensionattribute1","TransformationClaimType":"string1"}], "InputParameters": [{"ID":"string2","Value":"sandbox"},{"ID":"separator","Value":"."}],"OutputClaims":[{"ClaimTypeReferenceId":"DataJoin","TransformationClaimType":"outputClaim"}]}]}}') -DisplayName "TransformClaimsExample" -Type "ClaimsMappingPolicy"
      ```
    
   2. Voer de volgende opdracht uit om uw nieuwe beleid te bekijken en de beleidsobjectid op te halen: 
     
      ``` powershell
      Get-AzureADPolicy
      ```
1. Wijs het beleid toe aan uw serviceprincipal. U moet ook de ObjectId van uw serviceprincipal krijgen. 
   1. Als u alle serviceprincipals van uw organisatie wilt bekijken, u [de Microsoft Graph-API opvragen.](/graph/traverse-the-graph) Of meld u in [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)aan bij uw Azure AD-account.
   2. Wanneer u de ObjectId van uw serviceprincipal hebt, voert u de volgende opdracht uit: 
     
      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

## <a name="see-also"></a>Zie ook

Zie Claims die [zijn uitgegeven in het SAML-token voor](active-directory-saml-claims-customization.md) meer informatie over het aanpassen van claims die zijn uitgegeven in het SAML-token voor bedrijfstoepassingen:
