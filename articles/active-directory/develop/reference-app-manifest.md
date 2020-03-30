---
title: Inzicht in het Azure Active Directory-app-manifest | Microsoft Documenten
description: Gedetailleerde dekking van het Azure Active Directory-appmanifest, dat de identiteitsconfiguratie van een toepassing in een Azure AD-tenant vertegenwoordigt en wordt gebruikt om de OAuth-autorisatie, toestemmingservaring en meer te vergemakkelijken.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/23/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: sureshja
ms.openlocfilehash: 6d9a4af5ee814282589959fcf840c1061358ca18
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2020
ms.locfileid: "80383936"
---
# <a name="azure-active-directory-app-manifest"></a>Azure Active Directory-app-manifest

Het toepassingsmanifest bevat een definitie van alle kenmerken van een toepassingsobject in het Microsoft-identiteitsplatform. Het dient ook als een mechanisme voor het bijwerken van het toepassingsobject. Zie de documentatie van de entiteit Graph API Application voor meer informatie over de entiteit Toepassing toepassing toepassing van toepassing en toepassing van [grafiek.](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity)

U de kenmerken van een app configureren via de Azure-portal of programmatisch gebruiken met [REST API](https://docs.microsoft.com/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#application-entity) of [PowerShell.](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#applications) Er zijn echter enkele scenario's waarin u het app-manifest moet bewerken om het kenmerk van een app te configureren. Deze scenario's omvatten:

* Als u de app hebt geregistreerd als Azure AD-multitenant en persoonlijke Microsoft-accounts, u de ondersteunde Microsoft-accounts in de gebruikersinterface niet wijzigen. In plaats daarvan moet u de editor voor het manifest van de toepassing gebruiken om het ondersteunde accounttype te wijzigen.
* Als u machtigingen en rollen moet definiëren die uw app ondersteunt, moet u het toepassingsmanifest wijzigen.

## <a name="configure-the-app-manifest"></a>Het app-manifest configureren

Ga als u het toepassingsmanifest wilt configureren:

1. Ga naar de [Azure-portal.](https://portal.azure.com) Zoek naar en selecteer de **Azure Active Directory-service.**
1. Selecteer **App-registraties**.
1. Selecteer de app die u wilt configureren.
1. Selecteer op de pagina **Overzicht** van de app de sectie **Manifest**. Er wordt een op het web gebaseerde manifesteditor geopend, zodat u het manifest binnen de portal bewerken. Optioneel u **Downloaden** selecteren om het manifest lokaal te bewerken en vervolgens **Uploadgebruiken** om het opnieuw toe te passen op uw toepassing.

## <a name="manifest-reference"></a>Manifestverwijzing

In deze sectie worden de kenmerken beschreven die in het toepassingsmanifest worden gevonden.

### <a name="accesstokenacceptedversion-attribute"></a>kenmerk accessTokenAcceptedVersion

| Sleutel | Waardetype |
| :--- | :--- |
| accessTokenAcceptedVersion | Nietig verklarende Int32 |

Hiermee geeft u de door de bron verwachte versie van het toegangstoken op. Deze parameter wijzigt de versie en indeling van de JWT die is geproduceerd onafhankelijk van het eindpunt of de client die wordt gebruikt om het toegangstoken aan te vragen.

Het gebruikte eindpunt, v1.0 of v2.0, wordt gekozen door de client en heeft alleen invloed op de versie van id_tokens. Resources moeten expliciet `accesstokenAcceptedVersion` configureren om de ondersteunde access token-indeling aan te geven.

Mogelijke waarden `accesstokenAcceptedVersion` voor zijn 1, 2 of null. Als de waarde null is, wordt deze parameter standaard ingesteld op 1, wat overeenkomt met het v1.0-eindpunt.

Als `signInAudience` `AzureADandPersonalMicrosoftAccount`dat zo is, moet de waarde . `2`

Voorbeeld:

```json
    "accessTokenAcceptedVersion": 2,
```

### <a name="addins-attribute"></a>addIns-kenmerk

| Sleutel | Waardetype |
| :--- | :--- |
| Addins | Verzameling |

Hiermee definieert u aangepast gedrag dat een verbruikende service kan gebruiken om een app in specifieke contexten aan te roepen. Toepassingen die bijvoorbeeld bestandsstromen kunnen renderen, kunnen de eigenschap instellen voor de `addIns` functionaliteit 'FileHandler'. Met deze parameter kunnen services zoals Office 365 de toepassing aanroepen in de context van een document waar de gebruiker aan werkt.

Voorbeeld:

```json
    "addIns": [
       {
        "id": "968A844F-7A47-430C-9163-07AE7C31D407",
        "type":" FileHandler",
        "properties": [
           {
              "key": "version",
              "value": "2"
           }
        ]
       }
    ],
```

### <a name="allowpublicclient-attribute"></a>kenmerk allowPublicClient

| Sleutel | Waardetype |
| :--- | :--- |
| allowPublicClient | Booleaans |

Hiermee geeft u het terugvaltoepassingstype op. Azure AD leidt standaard het toepassingstype af van de replyUrlsWithType. Er zijn bepaalde scenario's waarin Azure AD het type client-app niet kan bepalen. Een dergelijk scenario is bijvoorbeeld de [ROPC-stroom waarbij HTTP-aanvragen](https://tools.ietf.org/html/rfc6749#section-4.3) worden weergegeven zonder omleiding van de URL). In die gevallen interpreteert Azure AD het toepassingstype op basis van de waarde van deze eigenschap. Als deze waarde is ingesteld op true, wordt het terugvaltoepassingstype ingesteld als openbare client, zoals een geïnstalleerde app die wordt uitgevoerd op een mobiel apparaat. De standaardwaarde is false, wat betekent dat het terugvaltoepassingstype een vertrouwelijke client is, zoals de web-app.

Voorbeeld:

```json
    "allowPublicClient": false,
```

### <a name="availabletoothertenants-attribute"></a>availableToOtherTenants-kenmerk

| Sleutel | Waardetype |
| :--- | :--- |
| beschikbaarToOtherTenants | Booleaans |

Ingesteld op true als de toepassing wordt gedeeld met andere tenants. anders, vals.

> [!NOTE]
> Dit kenmerk is alleen beschikbaar in de ervaring **App-registraties (Legacy).** Vervangen `signInAudience` door in de [app-registraties](https://go.microsoft.com/fwlink/?linkid=2083908) ervaring.

### <a name="appid-attribute"></a>appId-kenmerk

| Sleutel | Waardetype |
| :--- | :--- |
| appId | Tekenreeks |

Hiermee geeft u de unieke id op voor de app die is toegewezen aan een app van Azure AD.

Voorbeeld:

```json
    "appId": "601790de-b632-4f57-9523-ee7cb6ceba95",
```

### <a name="approles-attribute"></a>kenmerk appRoles

| Sleutel | Waardetype |
| :--- | :--- |
| appRollen | Verzameling |

Hiermee geeft u de verzameling rollen op die een app kan declareren. Deze rollen kunnen worden toegewezen aan gebruikers, groepen of serviceprincipals. Zie [App-rollen toevoegen in uw toepassing en deze ontvangen in het token](howto-add-app-roles-in-azure-ad-apps.md)voor meer voorbeelden en informatie.

Voorbeeld:

```json
    "appRoles": [
        {
           "allowedMemberTypes": [
               "User"
           ],
           "description": "Read-only access to device information",
           "displayName": "Read Only",
           "id": "601790de-b632-4f57-9523-ee7cb6ceba95",
           "isEnabled": true,
           "value": "ReadOnly"
        }
    ],
```

### <a name="displayname-attribute"></a>kenmerk displayName

| Sleutel | Waardetype |
| :--- | :--- |
| displayName | Tekenreeks |

De weergavenaam voor de app.

> [!NOTE]
> Dit kenmerk is alleen beschikbaar in de ervaring **App-registraties (Legacy).** Vervangen `name` door in de [app-registraties](https://go.microsoft.com/fwlink/?linkid=2083908) ervaring.

### <a name="errorurl-attribute"></a>kenmerk errorUrl

| Sleutel | Waardetype |
| :--- | :--- |
| foutUrl | Tekenreeks |

Unsupported.

### <a name="groupmembershipclaims-attribute"></a>kenmerk groupMembershipClaims

| Sleutel | Waardetype |
| :--- | :--- |
|groupMembershipClaims | Tekenreeks |

Hiermee configureert u de `groups` claim die is uitgegeven in een gebruikers- of OAuth 2.0-toegangstoken dat de app verwacht. Als u dit kenmerk wilt instellen, gebruikt u een van de volgende geldige tekenreekswaarden:

- `"None"`
- `"SecurityGroup"`(voor beveiligingsgroepen en Azure AD-rollen)
- `"All"`(hiermee worden alle beveiligingsgroepen, distributiegroepen en Azure AD-maprollen waarde aangemelde gebruiker lid van is, op de hoogte.

Voorbeeld:

```json
    "groupMembershipClaims": "SecurityGroup",
```

### <a name="homepage-attribute"></a>kenmerk startpagina

| Sleutel | Waardetype |
| :--- | :--- |
| Homepage |Tekenreeks |

De URL naar de startpagina van de toepassing.

> [!NOTE]
> Dit kenmerk is alleen beschikbaar in de ervaring **App-registraties (Legacy).** Vervangen `signInUrl` door in de [app-registraties](https://go.microsoft.com/fwlink/?linkid=2083908) ervaring.

### <a name="objectid-attribute"></a>objectId-kenmerk

| Sleutel | Waardetype |
| :--- | :--- |
|objectId | Tekenreeks |

De unieke id voor de app in de map.

Dit is alleen beschikbaar in de **app-registratie (Legacy)** ervaring. Vervangen `id` door in de [app-registraties](https://go.microsoft.com/fwlink/?linkid=2083908) ervaring.

Voorbeeld:

```json
    "objectId": "f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd",
```

### <a name="optionalclaims-attribute"></a>optionoptionClaims-kenmerk

| Sleutel | Waardetype |
| :--- | :--- |
| optioneel Claims | Tekenreeks |

De optionele claims die in het token worden geretourneerd door de beveiligingstokenservice voor deze specifieke app.

Op dit moment kunnen apps die zowel persoonlijke accounts als Azure AD ondersteunen (geregistreerd via de app-registratieportal) geen optionele claims gebruiken. Apps die zijn geregistreerd voor alleen Azure AD met het v2.0-eindpunt, kunnen echter de optionele claims krijgen die ze in het manifest hebben aangevraagd. Zie [Optionele claims voor](active-directory-optional-claims.md)meer informatie .

Voorbeeld:

```json
    "optionalClaims": null,
```

### <a name="id-attribute"></a>id-kenmerk

| Sleutel | Waardetype |
| :--- | :--- |
| id | Tekenreeks |

De unieke id voor de app in de map. Deze id is niet de id die wordt gebruikt om de app in een protocoltransactie te identificeren. Het wordt gebruikt voor het verwijzen naar het object in directoryquery's.

Voorbeeld:

```json
    "id": "f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd",
```

### <a name="identifieruris-attribute"></a>kenmerk identifierUris

| Sleutel | Waardetype |
| :--- | :--- |
| identifierUris | Tekenreeksarray |

Door de gebruiker gedefinieerde URI(s) die op unieke wijze een web-app identificeren binnen de Azure AD-tenant of binnen een geverifieerd aangepast domein als de app multi-tenant is.

Voorbeeld:

```json
    "identifierUris": "https://MyRegisteredApp",
```

### <a name="informationalurls-attribute"></a>kenmerk informationalUrls

| Sleutel | Waardetype |
| :--- | :--- |
| informationalUrls | Tekenreeks |

Hiermee geeft u de koppelingen naar de servicevoorwaarden en privacyverklaring van de app op. De servicevoorwaarden en privacyverklaring worden aan gebruikers opgedoken via de gebruikerstoestemmingservaring. Zie [Servicevoorwaarden en privacyverklaring toevoegen voor geregistreerde Azure AD-apps voor](howto-add-terms-of-service-privacy-statement.md)meer informatie.

Voorbeeld:

```json
    "informationalUrls": {
        "termsOfService": "https://MyRegisteredApp/termsofservice",
        "support": "https://MyRegisteredApp/support",
        "privacy": "https://MyRegisteredApp/privacystatement",
        "marketing": "https://MyRegisteredApp/marketing"
    },
```

### <a name="keycredentials-attribute"></a>kenmerk keyCredentials

| Sleutel | Waardetype |
| :--- | :--- |
| keyCredentials | Verzameling |

Bevat verwijzingen naar app-toegewezen referenties, gedeelde geheimen op basis van tekenreeksen en X.509-certificaten. Deze referenties worden gebruikt bij het aanvragen van toegangstokens (wanneer de app fungeert als een client in plaats van dat als een bron).

Voorbeeld:

```json
    "keyCredentials": [
        {
           "customKeyIdentifier":null,
           "endDate":"2018-09-13T00:00:00Z",
           "keyId":"<guid>",
           "startDate":"2017-09-12T00:00:00Z",
           "type":"AsymmetricX509Cert",
           "usage":"Verify",
           "value":null
        }
    ],
```

### <a name="knownclientapplications-attribute"></a>knownClientApplications-kenmerk

| Sleutel | Waardetype |
| :--- | :--- |
| knownClientApplications | Tekenreeksarray |

Wordt gebruikt voor het bundelen van toestemming als u een oplossing hebt die twee delen bevat: een client-app en een aangepaste web-API-app. Als u de appID van de client-app indeze waarde invoert, hoeft de gebruiker slechts eenmaal toestemming te geven voor de client-app. Azure AD weet dat toestemming voor de client betekent dat u impliciet instemt met de web-API. Het zal automatisch service principals voor zowel de client en web API op hetzelfde moment. Zowel de client als de web-API-app moeten in dezelfde tenant worden geregistreerd.

Voorbeeld:

```json
    "knownClientApplications": ["f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd"],
```

### <a name="logourl-attribute"></a>logoUrl-kenmerk

| Sleutel | Waardetype |
| :--- | :--- |
| logoUrl | Tekenreeks |

Lees alleen waarde die verwijst naar de CDN URL naar logo dat is geüpload in de portal.

Voorbeeld:

```json
    "logoUrl": "https://MyRegisteredAppLogo",
```

### <a name="logouturl-attribute"></a>kenmerk uitloggenUrl

| Sleutel | Waardetype |
| :--- | :--- |
| uitlogurl | Tekenreeks |

De URL om u af te melden bij de app.

Voorbeeld:

```json
    "logoutUrl": "https://MyRegisteredAppLogout",
```

### <a name="name-attribute"></a>naamkenmerk

| Sleutel | Waardetype |
| :--- | :--- |
| name | Tekenreeks |

De weergavenaam voor de app.

Voorbeeld:

```json
    "name": "MyRegisteredApp",
```

### <a name="oauth2allowimplicitflow-attribute"></a>oauth2AllowImplicitFlow-kenmerk

| Sleutel | Waardetype |
| :--- | :--- |
| oauth2AllowImplicitFlow | Booleaans |

Hiermee geeft u op of deze web-app impliciete stroomtoegangstokens op Te vragen kan aanvragen. De standaardinstelling is onwaar. Deze vlag wordt gebruikt voor browsergebaseerde apps, zoals JavaScript-apps met één pagina. Voor meer informatie `OAuth 2.0 implicit grant flow` voert u de inhoudsopgave in en bekijk de onderwerpen over impliciete stroom.

Voorbeeld:

```json
    "oauth2AllowImplicitFlow": false,
```

### <a name="oauth2allowidtokenimplicitflow-attribute"></a>oauth2AllowIdTokenImplicitFlow-kenmerk

| Sleutel | Waardetype |
| :--- | :--- |
| oauth2AllowIdTokenImplicitFlow | Booleaans |

Hiermee geeft u op of deze web-app OAuth2.0 impliciete flow ID-tokens kan aanvragen. De standaardinstelling is onwaar. Deze vlag wordt gebruikt voor browsergebaseerde apps, zoals JavaScript-apps met één pagina.

Voorbeeld:

```json
    "oauth2AllowIdTokenImplicitFlow": false,
```

### <a name="oauth2permissions-attribute"></a>kenmerk oauth2Permissions

| Sleutel | Waardetype |
| :--- | :--- |
| oauth2Machtigingen | Verzameling |

Hiermee geeft u de verzameling OAuth 2.0-machtigingsscopes op die de web-API-app (resource) blootstelt aan client-apps. Deze machtigingsmogelijkheden kunnen worden verleend aan client-apps tijdens de toestemming.

Voorbeeld:

```json
    "oauth2Permissions": [
       {
          "adminConsentDescription": "Allow the app to access resources on behalf of the signed-in user.",
          "adminConsentDisplayName": "Access resource1",
          "id": "<guid>",
          "isEnabled": true,
          "type": "User",
          "userConsentDescription": "Allow the app to access resource1 on your behalf.",
          "userConsentDisplayName": "Access resources",
          "value": "user_impersonation"
        }
    ],
```

### <a name="oauth2requiredpostresponse-attribute"></a>oauth2VereistePostResponse-kenmerk

| Sleutel | Waardetype |
| :--- | :--- |
| oauth2VereistePostResponse | Booleaans |

Hiermee geeft u op of Azure AD als onderdeel van OAuth 2.0-tokenaanvragen POST-aanvragen toestaat, in tegenstelling tot GET-aanvragen. De standaardinstelling is false, wat aangeeft dat alleen GET-aanvragen zijn toegestaan.

Voorbeeld:

```json
    "oauth2RequirePostResponse": false,
```

### <a name="parentalcontrolsettings-attribute"></a>kenmerk ouderlijkinstellingen

| Sleutel | Waardetype |
| :--- | :--- |
| ouderlijkeInstellingen voor ouderlijk toezicht | Tekenreeks |

- `countriesBlockedForMinors`geeft op welke landen de app is geblokkeerd voor minderjarigen.
- `legalAgeGroupRule`geeft de wettelijke leeftijdsgroepregel op die van toepassing is op gebruikers van de app. Kan worden `Allow`ingesteld `RequireConsentForPrivacyServices` `RequireConsentForMinors`op `RequireConsentForKids`, `BlockMinors`, , of .  

Voorbeeld:

```json
    "parentalControlSettings": {
        "countriesBlockedForMinors": [],
        "legalAgeGroupRule": "Allow"
    },
```

### <a name="passwordcredentials-attribute"></a>kenmerk passwordCredentials

| Sleutel | Waardetype |
| :--- | :--- |
| wachtwoordReferenties | Verzameling |

Zie de beschrijving `keyCredentials` voor de accommodatie.

Voorbeeld:

```json
    "passwordCredentials": [
      {
        "customKeyIdentifier": null,
        "endDate": "2018-10-19T17:59:59.6521653Z",
        "keyId": "<guid>",
        "startDate":"2016-10-19T17:59:59.6521653Z",
        "value":null
      }
    ],
```

### <a name="preauthorizedapplications-attribute"></a>kenmerk preAuthorizedApplications

| Sleutel | Waardetype |
| :--- | :--- |
| preAuthorizedApplications | Verzameling |

Hiermee worden toepassingen en gevraagde machtigingen voor impliciete toestemming vermeld. Een beheerder moet toestemming hebben gegeven voor de toepassing. preAuthorizedApplications vereisen niet dat de gebruiker instemt met de gevraagde machtigingen. Machtigingen die in preAuthorizedApplications worden vermeld, vereisen geen toestemming van de gebruiker. Aanvullende aangevraagde machtigingen die niet in preAuthorizedApplications zijn vermeld, vereisen echter toestemming van de gebruiker.

Voorbeeld:

```json
    "preAuthorizedApplications": [
       {
          "appId": "abcdefg2-000a-1111-a0e5-812ed8dd72e8",
          "permissionIds": [
             "8748f7db-21fe-4c83-8ab5-53033933c8f1"
            ]
        }
    ],
```

### <a name="publicclient-attribute"></a>publicClient-kenmerk

| Sleutel | Waardetype |
| :--- | :--- |
| publicClient | Booleaans|

Hiermee geeft u op of deze toepassing een openbare client is (zoals een geïnstalleerde toepassing die wordt uitgevoerd op een mobiel apparaat). 

Deze eigenschap is alleen beschikbaar in de **app-registratie (Legacy)-ervaring.** Vervangen `allowPublicClient` door in de [app-registraties](https://go.microsoft.com/fwlink/?linkid=2083908) ervaring.

### <a name="publisherdomain-attribute"></a>publisherDomain-kenmerk

| Sleutel | Waardetype |
| :--- | :--- |
| publisherDomain | Tekenreeks |

Het geverifieerde uitgeversdomein voor de toepassing. Alleen-lezen.

Voorbeeld:

```json
    "publisherDomain": "https://www.contoso.com",
````

### <a name="replyurls-attribute"></a>kenmerk replyUrls

| Sleutel | Waardetype |
| :--- | :--- |
| replyUrls | Stringarray |

Deze eigenschap met meerdere waarde bevat de lijst met geregistreerde redirect_uri waarden die Azure AD als bestemmingen accepteert bij het retourneren van tokens.

Deze eigenschap is alleen beschikbaar in de **app-registratie (Legacy)-ervaring.** Vervangen `replyUrlsWithType` door in de [app-registraties](https://go.microsoft.com/fwlink/?linkid=2083908) ervaring.

### <a name="replyurlswithtype-attribute"></a>replyUrlsWithType-kenmerk

| Sleutel | Waardetype |
| :--- | :--- |
| replyUrlsWithType | Verzameling |

Deze eigenschap met meerdere waarde bevat de lijst met geregistreerde redirect_uri waarden die Azure AD als bestemmingen accepteert bij het retourneren van tokens. Elke URI-waarde moet een bijbehorende waarde van het app-type bevatten. Ondersteunde typewaarden zijn:

- `Web`
- `InstalledClient`

Zie [beperkingen en beperkingen beantwoorden voor](https://docs.microsoft.com/azure/active-directory/develop/reply-url)meer informatie.

Voorbeeld:

```json
    "replyUrlsWithType": [
       {
          "url": "https://localhost:4400/services/office365/redirectTarget.html",
          "type": "InstalledClient"
       }
    ],
```

### <a name="requiredresourceaccess-attribute"></a>vereist ResourceAccess-kenmerk

| Sleutel | Waardetype |
| :--- | :--- |
| vereistResourceAccess | Verzameling |

Met dynamische `requiredResourceAccess` toestemming, stimuleert de admin toestemming ervaring en de gebruiker toestemming ervaring voor gebruikers die gebruik maken van statische toestemming. Deze parameter geeft echter geen reden tot de gebruikerstoestemmingservaring voor het algemene geval.

- `resourceAppId`is de unieke id voor de bron waartoe de app toegang nodig heeft. Deze waarde moet gelijk zijn aan de appId die is opgegeven in de doelbron-app.
- `resourceAccess`is een array met de OAuth2.0-machtigingsscopes en app-rollen die de app vereist van de opgegeven bron. Bevat `id` de `type` waarden en waarden van de opgegeven resources.

Voorbeeld:

```json
    "requiredResourceAccess": [
        {
            "resourceAppId": "00000002-0000-0000-c000-000000000000",
            "resourceAccess": [
                {
                    "id": "311a71cc-e848-46a1-bdf8-97ff7156d8e6",
                    "type": "Scope"
                }
            ]
        }
    ],
```

### <a name="samlmetadataurl-attribute"></a>samlMetadataUrl-kenmerk

| Sleutel | Waardetype |
| :--- | :--- |
| samlMetadataUrl samlMetadataUrl | Tekenreeks |

De URL naar de SAML-metagegevens voor de app.

Voorbeeld:

```json
    "samlMetadataUrl": "https://MyRegisteredAppSAMLMetadata",
```

### <a name="signinurl-attribute"></a>kenmerk signInUrl

| Sleutel | Waardetype |
| :--- | :--- |
| signInUrl | Tekenreeks |

Hiermee geeft u de URL op naar de startpagina van de app.

Voorbeeld:

```json
    "signInUrl": "https://MyRegisteredApp",
```

### <a name="signinaudience-attribute"></a>kenmerk signInAudience

| Sleutel | Waardetype |
| :--- | :--- |
| signInAudience | Tekenreeks |

Hiermee geeft u op welke Microsoft-accounts worden ondersteund voor de huidige toepassing. Ondersteunde waarden zijn:
- `AzureADMyOrg`- Gebruikers met een Microsoft-werk- of schoolaccount in de Azure AD-tenant van mijn organisatie (bijvoorbeeld één tenant)
- `AzureADMultipleOrgs`- Gebruikers met een Microsoft-werk- of schoolaccount in de Azure AD-tenant van een organisatie (bijvoorbeeld multi-tenant)
- `AzureADandPersonalMicrosoftAccount`- Gebruikers met een persoonlijk Microsoft-account of een werk- of schoolaccount in de Azure AD-tenant van een organisatie
- `PersonalMicrosoftAccount`- Persoonlijke accounts die worden gebruikt om u aan te melden bij services zoals Xbox en Skype.

Voorbeeld:

```json
    "signInAudience": "AzureADandPersonalMicrosoftAccount",
```

### <a name="tags-attribute"></a>kenmerk tags

| Sleutel | Waardetype |
| :--- | :--- |
| tags | Tekenreeksarray  |

Aangepaste tekenreeksen die kunnen worden gebruikt om de toepassing te categoriseren en te identificeren.

Voorbeeld:

```json
    "tags": [
       "ProductionApp"
    ],
```

## <a name="common-issues"></a>Algemene problemen

### <a name="manifest-limits"></a>Manifestlimieten

Een toepassingsmanifest heeft meerdere kenmerken die worden aangeduid als verzamelingen. bijvoorbeeld appRollen, keyCredentials, bekendeClientApplications, identifierUris, redirectUris, requiredResourceAccess en oauth2Permissions. In het volledige aanvraagmanifest voor elke aanvraag is het totale aantal inzendingen in alle collecties samen beperkt tot 1200. Als u eerder 100 omleidings-URI's opgeeft in het toepassingsmanifest, blijft er nog maar 1100 resterende vermeldingen over die in alle andere verzamelingen worden gebruikt die deel uitmaken van het manifest.

> [!NOTE]
> In het geval u probeert om meer dan 1200 items toe te voegen in de toepassing manifest, u een fout **Kan niet bij te werken toepassing xxxxxx. Foutgegevens: de grootte van het manifest heeft de limiet overschreden. Verlaag het aantal waarden en probeer uw aanvraag opnieuw."**

### <a name="unsupported-attributes"></a>Niet-ondersteunde kenmerken

Het toepassingsmanifest vertegenwoordigt het schema van het onderliggende toepassingsmodel in Azure AD. Naarmate het onderliggende schema evolueert, wordt de manifesteditor bijgewerkt om het nieuwe schema van tijd tot tijd weer te geven. Als gevolg hiervan u nieuwe kenmerken opmerken die in het toepassingsmanifest worden weergegeven. In zeldzame gevallen u een syntactische of semantische wijziging in de bestaande kenmerken opmerken of u een kenmerk vinden dat voorheen bestond, niet meer wordt ondersteund. U ziet bijvoorbeeld nieuwe kenmerken in de [app-registraties](https://go.microsoft.com/fwlink/?linkid=2083908), die bekend zijn met een andere naam in de ervaring App-registraties (Legacy).

| App-registraties (Legacy)| App-registraties           |
|---------------------------|-----------------------------|
| `availableToOtherTenants` | `signInAudience`            |
| `displayName`             | `name`                      |
| `errorUrl`                | -                           |
| `homepage`                | `signInUrl`                 |
| `objectId`                | `Id`                        |
| `publicClient`            | `allowPublicClient`         |
| `replyUrls`               | `replyUrlsWithType`         |

Zie de [sectie manifestverwijzing](#manifest-reference) voor beschrijvingen voor deze kenmerken.

Wanneer u een eerder gedownload manifest probeert te uploaden, ziet u mogelijk een van de volgende fouten. Deze fout is waarschijnlijk omdat de manifesteditor nu een nieuwere versie van het schema ondersteunt, die niet overeenkomt met de versie die u probeert te uploaden.

* "Niet updaten xxxxxx applicatie. Foutdetail: ongeldige object-id 'niet gedefinieerd'. []."
* "Niet updaten xxxxxx applicatie. Foutdetail: een of meer opgegeven eigenschapswaarden zijn ongeldig. []."
* "Niet updaten xxxxxx applicatie. Foutdetail: niet toegestaan om beschikbaarOtherTenants in te stellen in deze api-versie voor update. []."
* "Niet updaten xxxxxx applicatie. Foutdetail: Updates van de eigenschap 'replyUrls' zijn niet toegestaan voor deze toepassing. Gebruik in plaats daarvan de eigenschap 'replyUrlsWithType'. []."
* "Niet updaten xxxxxx applicatie. Foutdetail: er is een waarde zonder typenaam gevonden en er is geen verwacht type beschikbaar. Wanneer het model is opgegeven, moet elke waarde in de payload een type hebben dat kan worden opgegeven in de payload, expliciet door de beller of impliciet afgeleid van de bovenliggende waarde. []"

Wanneer u een van deze fouten ziet, raden we de volgende acties aan:

1. Bewerk de kenmerken afzonderlijk in de manifesteditor in plaats van het uploaden van een eerder gedownload manifest. Gebruik de [manifestreferentietabel](#manifest-reference) om de syntaxis en semantiek van oude en nieuwe kenmerken te begrijpen, zodat u de kenmerken waarin u geïnteresseerd bent, bewerken. 
1. Als je werkstroom vereist dat je de manifesten opslaat in je bronopslagplaats voor later gebruik, raden we je aan om de opgeslagen manifesten in je repository te rebaseren met de manifesten die je ziet in de **app-registratieservaring.**

## <a name="next-steps"></a>Volgende stappen

* Zie Hoofdobjecten voor toepassingen [en serviceinaalheid in Azure AD](app-objects-and-service-principals.md)voor meer informatie over de relatie tussen de toepassing van een app en het hoofdobject(en) van de service.
* Zie de woordenlijst voor [ontwikkelaars van het Microsoft-identiteitsplatform](developer-glossary.md) voor definities van enkele belangrijke microsoft-concepten voor identiteitsplatformontwikkelaars.

Gebruik de volgende opmerkingensectie om feedback te geven die helpt bij het verfijnen en vormgeven van onze inhoud.

<!--article references -->
[AAD-APP-OBJECTS]:app-objects-and-service-principals.md
[AAD-DEVELOPER-GLOSSARY]:developer-glossary.md
[AAD-GROUPS-FOR-AUTHORIZATION]: http://www.dushyantgill.com/blog/2014/12/10/authorization-cloud-applications-using-ad-groups/
[ADD-UPD-RMV-APP]:quickstart-v1-integrate-apps-with-azure-ad.md
[APPLICATION-ENTITY]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[APPLICATION-ENTITY-APP-ROLE]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#approle-type
[APPLICATION-ENTITY-OAUTH2-PERMISSION]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permission-type
[AZURE-PORTAL]: https://portal.azure.com
[DEV-GUIDE-TO-AUTH-WITH-ARM]: http://www.dushyantgill.com/blog/2015/05/23/developers-guide-to-auth-with-azure-resource-manager-api/
[GRAPH-API]: active-directory-graph-api.md
[IMPLICIT-GRANT]:v1-oauth2-implicit-grant-flow.md
[INTEGRATING-APPLICATIONS-AAD]: https://azure.microsoft.com/documentation/articles/active-directory-integrating-applications/
[O365-PERM-DETAILS]: https://msdn.microsoft.com/office/office365/HowTo/application-manifest
[O365-SERVICE-DAEMON-APPS]: https://msdn.microsoft.com/office/office365/howto/building-service-apps-in-office-365
[RBAC-CLOUD-APPS-AZUREAD]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/
