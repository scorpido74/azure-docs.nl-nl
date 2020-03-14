---
title: Meer informatie over het Azure Active Directory app-manifest | Microsoft Docs
description: Gedetailleerde dekking van het app-manifest van de Azure Active Directory, dat de identiteits configuratie van een toepassing vertegenwoordigt in een Azure AD-Tenant, en wordt gebruikt om de OAuth-autorisatie, de toestemmings ervaring en nog veel meer te vergemakkelijken.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/03/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: sureshja
ms.openlocfilehash: a12715ba9aac77461d4968bd9b8f3de30af243c4
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79262748"
---
# <a name="azure-active-directory-app-manifest"></a>Azure Active Directory app-manifest

Het manifest van de toepassing bevat een definitie van alle kenmerken van een toepassings object in het micro soft Identity-platform. Het fungeert ook als mechanisme voor het bijwerken van het toepassings object. Zie de documentatie van de [Graph API toepassings entiteit](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity)voor meer informatie over de toepassings entiteit en het bijbehorende schema.

U kunt de kenmerken van een app configureren via de Azure Portal of programmatisch met behulp van [rest API](https://docs.microsoft.com/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#application-entity) of [Power shell](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#applications). Er zijn echter enkele scenario's waarin u het app-manifest moet bewerken om het kenmerk van een app te configureren. Deze scenario's omvatten:

* Als u de app als Azure AD-multi tenant en persoonlijke micro soft-accounts hebt geregistreerd, kunt u de ondersteunde micro soft-accounts in de gebruikers interface niet wijzigen. In plaats daarvan moet u de Application manifest editor gebruiken om het ondersteunde account type te wijzigen.
* Als u machtigingen en rollen wilt definiëren die door uw app worden ondersteund, moet u het toepassings manifest wijzigen.

## <a name="configure-the-app-manifest"></a>Het app-manifest configureren

Het toepassings manifest configureren:

1. Ga naar de [Azure Portal](https://portal.azure.com). Zoek en selecteer de **Azure Active Directory** service.
1. Selecteer **App-registraties**.
1. Selecteer de app die u wilt configureren.
1. Selecteer op de pagina **Overzicht** van de app de sectie **Manifest**. Een manifest editor op het web wordt geopend, zodat u het manifest in de portal kunt bewerken. Desgewenst kunt u **downloaden** selecteren om het manifest lokaal te bewerken en vervolgens **uploaden** gebruiken om het opnieuw toe te passen op uw toepassing.

## <a name="manifest-reference"></a>Manifest verwijzing


### <a name="key-value-type-accesstokenacceptedversion-nullable-int32"></a>Sleutel, waardetype: `accessTokenAcceptedVersion`, nullable Int32 
Hiermee geeft u de versie van het toegangs token verwacht door de resource. Met deze para meter wijzigt u de versie en indeling van de JWT die onafhankelijk is gemaakt van het eind punt of de client die wordt gebruikt om het toegangs token aan te vragen.

Het eind punt dat wordt gebruikt, v 1.0 of v 2.0, wordt gekozen door de client en heeft alleen invloed op de versie van id_tokens. Resources moeten expliciet `accesstokenAcceptedVersion` configureren om de ondersteunde indeling van het toegangs token aan te duiden.

Mogelijke waarden voor `accesstokenAcceptedVersion` zijn 1, 2 of null. Als de waarde Null is, wordt deze para meter standaard ingesteld op 1, die overeenkomt met het eind punt v 1.0. 

Als `signInAudience` is `AzureADandPersonalMicrosoftAccount`, moet de waarde worden `2`  

Voorbeeldwaarde: `2` 

### <a name="key-value-type-addins-collection"></a>Sleutel, waardetype: `addIns`, verzameling 
Hiermee wordt aangepast gedrag gedefinieerd dat een verbruikte service kan gebruiken om een app in specifieke contexten aan te roepen. Toepassingen die bestands stromen kunnen renderen, kunnen bijvoorbeeld de eigenschap addIns instellen voor de functionaliteit ' FileHandler '. Met deze para meter kunnen services zoals Office 365 de toepassing aanroepen in de context van een document waarmee de gebruiker werkt. 

Voorbeeld waarde: 
<code>{<br>&nbsp;&nbsp;&nbsp;"id":"968A844F-7A47-430C-9163-07AE7C31D407"<br>&nbsp;&nbsp;&nbsp;"type": "FileHandler",<br>&nbsp;&nbsp;&nbsp;"properties": [<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{"key": "version", "value": "2" }<br>&nbsp;&nbsp;&nbsp;]<br>}</code>

### <a name="key-value-type-allowpublicclient-boolean"></a>Sleutel, waardetype: `allowPublicClient`, Boolean 
Hiermee wordt het type terugval toepassing opgegeven. Azure AD leidt het toepassings type standaard af van de replyUrlsWithType. Er zijn bepaalde scenario's waarin het app-type van de client niet kan worden bepaald door Azure AD. Een voor beeld hiervan is de [ROPC](https://tools.ietf.org/html/rfc6749#section-4.3) -stroom waarbij een HTTP-aanvraag wordt uitgevoerd zonder URL-omleiding). In dergelijke gevallen interpreteert Azure AD het toepassings type op basis van de waarde van deze eigenschap. Als deze waarde is ingesteld op True, wordt het type terugval toepassing ingesteld als open bare client, zoals een geïnstalleerde app die wordt uitgevoerd op een mobiel apparaat. De standaard waarde is False. Dit betekent dat het type terugval toepassing vertrouwelijk is, zoals web-app. 

Voorbeeldwaarde: `false` 

### <a name="key-value-type-availabletoothertenants-boolean"></a>Sleutel, waardetype: `availableToOtherTenants`, Boolean 
waar als de toepassing wordt gedeeld met andere tenants; anders false. <br><br> Opmerking: dit is alleen beschikbaar in de ervaring **app-registraties (verouderd)** . Vervangen door `signInAudience` in de [app-registraties](https://go.microsoft.com/fwlink/?linkid=2083908) -ervaring. 

### <a name="key-value-type-appid-string"></a>Sleutel, waardetype: `appId`, String 
Hiermee geeft u de unieke id op voor de app die is toegewezen aan een app door Azure AD. 

Voorbeeldwaarde: `"601790de-b632-4f57-9523-ee7cb6ceba95"` 

### <a name="key-value-type-approles-collection"></a>Sleutel, waardetype: `appRoles`, verzameling 
Hiermee geeft u de verzameling rollen op die een app kan declareren. Deze rollen kunnen worden toegewezen aan gebruikers, groepen of service-principals. Zie [app-functies toevoegen in uw toepassing en deze ontvangen in het token](howto-add-app-roles-in-azure-ad-apps.md) voor meer voor beelden en informatie. 

Voorbeeld waarde: 
<code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"allowedMemberTypes": [<br>&emsp;&nbsp;&nbsp;&nbsp;"User"<br>&nbsp;&nbsp;&nbsp;],<br>&nbsp;&nbsp;&nbsp;"description":"Read-only access to device information",<br>&nbsp;&nbsp;&nbsp;"displayName":"Read Only",<br>&nbsp;&nbsp;&nbsp;"id":guid,<br>&nbsp;&nbsp;&nbsp;"isEnabled":true,<br>&nbsp;&nbsp;&nbsp;"value":"ReadOnly"<br>&nbsp;&nbsp;}<br>]</code>  

### <a name="key-value-type-displayname-string"></a>Sleutel, waardetype: `displayName`, String 
De weergave naam voor de app. <br><br> Opmerking: dit is alleen beschikbaar in de ervaring **app-registraties (verouderd)** . Vervangen door `name` in de [app-registraties](https://go.microsoft.com/fwlink/?linkid=2083908) -ervaring. 

Voorbeeldwaarde: `"MyRegisteredApp"` 

### <a name="key-value-type-errorurl-string"></a>Sleutel, waardetype: `errorUrl`, String 
Niet ondersteund. 

### <a name="key-value-type-groupmembershipclaims-string"></a>Sleutel, waardetype: `groupMembershipClaims`, String 
Hiermee wordt de `groups` claim geconfigureerd die is uitgegeven in een gebruiker of OAuth 2,0-toegangs token dat de app verwacht. Als u dit kenmerk wilt instellen, gebruikt u een van de volgende geldige teken reeks waarden:
- `"None"`
- `"SecurityGroup"` (voor beveiligings groepen en Azure AD-rollen)
- `"All"` (Hiermee worden alle beveiligings groepen, distributie groepen en Azure AD-adreslijst rollen opgehaald waarvan de aangemelde gebruiker lid is. 

Voorbeeldwaarde: `"SecurityGroup"` 

### <a name="key-value-type-homepage-string"></a>Sleutel, waardetype: `homepage`, String 
De URL van de start pagina van de toepassing. <br><br> Opmerking: dit is alleen beschikbaar in de ervaring **app-registraties (verouderd)** . Vervangen door `signInUrl` in de [app-registraties](https://go.microsoft.com/fwlink/?linkid=2083908) -ervaring. 

Voorbeeldwaarde: `"https://MyRegisteredApp"` 

### <a name="key-value-type-objectid-string"></a>Sleutel, waardetype: `objectId`, String 
De unieke id voor de app in de Directory. <br><br> Dit is alleen beschikbaar in de ervaring **app-registraties (verouderd)** . Vervangen door `id` in de [app-registraties](https://go.microsoft.com/fwlink/?linkid=2083908) -ervaring. 

Voorbeeldwaarde: `"f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd"` 

### <a name="key-value-type-optionalclaims-string"></a>Sleutel, waardetype: `optionalClaims`, String 
De optionele claims die in het token worden geretourneerd door de beveiligings token service voor deze specifieke app.<br>Op dit moment kunnen apps die zowel persoonlijke accounts als Azure AD ondersteunen (geregistreerd via de app registratie-Portal) geen gebruikmaken van optionele claims. Apps die zijn geregistreerd voor Azure AD met behulp van het v 2.0-eind punt kunnen echter de optionele claims ophalen die ze in het manifest hebben aangevraagd. Zie voor meer informatie [optionele claims](active-directory-optional-claims.md). 

Voorbeeld waarde:  
`null` 

### <a name="key-value-type-id-string"></a>Sleutel, waardetype: `id`, String 
De unieke id voor de app in de Directory. Deze ID is niet de id die wordt gebruikt om de app in een protocol transactie te identificeren. Het wordt gebruikt voor het verwijzen naar het object in Directory-query's. 

Voorbeeldwaarde: `"f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd"` 

### <a name="key-value-type-identifieruris-string-array"></a>Sleutel, waardetype: `identifierUris`, teken reeks matrix 
Door de gebruiker gedefinieerde URI (s) waarmee een web-app in de Azure AD-Tenant wordt geïdentificeerd, of binnen een geverifieerd aangepast domein als de app meerdere tenants is. 

Voorbeeld waarde: 
<code>[<br>&nbsp;&nbsp;"https://MyRegisteredApp"<br>]</code> 

### <a name="key-value-type-informationalurls-string"></a>Sleutel, waardetype: `informationalUrls`, String 
Hiermee geeft u de koppelingen naar de service voorwaarden en de privacyverklaring van de app op. De service voorwaarden en de privacyverklaring worden aan gebruikers door gegeven via de toestemming van de gebruiker. Zie [How to: Service voorwaarden toevoegen en privacyverklaring voor geregistreerde Azure AD-apps](howto-add-terms-of-service-privacy-statement.md)voor meer informatie. 

Voorbeeld waarde: 
<code>{<br>&nbsp;&nbsp;&nbsp;"marketing":"https://MyRegisteredApp/marketing",<br>&nbsp;&nbsp;&nbsp;"privacy":"https://MyRegisteredApp/privacystatement",<br>&nbsp;&nbsp;&nbsp;"support":"https://MyRegisteredApp/support",<br>&nbsp;&nbsp;&nbsp;"termsOfService":"https://MyRegisteredApp/termsofservice"<br>}</code> 

### <a name="key-value-type-keycredentials-collection"></a>Sleutel, waardetype: `keyCredentials`, verzameling 
Bevat verwijzingen naar referenties die aan de app zijn toegewezen, op teken reeks gebaseerde gedeelde geheimen en X. 509-certificaten. ). 

Voorbeeld waarde: 
<code>[<br>&nbsp;{<br>&nbsp;&These credentials are used when requesting access tokens (when the app is acting as a client rather that as resourcenbsp;&nbsp;"customKeyIdentifier":null,<br>&nbsp;&nbsp;&nbsp;"endDate":"2018-09-13T00:00:00Z",<br>&nbsp;&nbsp;&nbsp;"keyId":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"startDate":"2017-09-12T00:00:00Z",<br>&nbsp;&nbsp;&nbsp;"type":"AsymmetricX509Cert",<br>&nbsp;&nbsp;&nbsp;"usage":"Verify",<br>&nbsp;&nbsp;&nbsp;"value":null<br>&nbsp;&nbsp;}<br>]</code> 

### <a name="key-value-type-knownclientapplications-string-array"></a>Sleutel, waardetype: `knownClientApplications`, teken reeks matrix 
Wordt gebruikt voor bundeling-toestemming als u een oplossing hebt die twee onderdelen bevat: een client-app en een aangepaste web-API-app. Als u de appID van de client-app in deze waarde invoert, hoeft de gebruiker slechts eenmaal toestemming te geven aan de client-app. Azure AD weet dat de toestemming van de client impliciet wordt gestemd op de Web-API. De service-principals worden automatisch voor zowel de client als de Web-API ingericht. Zowel de client als de Web-API-app moeten zijn geregistreerd in dezelfde Tenant. 

Voorbeeldwaarde: `["f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd"]` 

### <a name="key-value-type-logourl-string"></a>Sleutel, waardetype: `logoUrl`, String 
Alleen-lezen waarde die verwijst naar de CDN-URL naar het logo dat is geüpload in de portal. 

Voorbeeldwaarde: `"https://MyRegisteredAppLogo"` 

### <a name="key-value-type-logouturl-string"></a>Sleutel, waardetype: `logoutUrl`, String 
De URL voor het afmelden bij de app. 

Voorbeeld waarde:  
`"https://MyRegisteredAppLogout"` 

### <a name="key-value-type-name-string"></a>Sleutel, waardetype: `name`, String 
De weergave naam voor de app. 

Voorbeeldwaarde: `"MyRegisteredApp"` 

### <a name="key-value-type-oauth2allowimplicitflow-boolean"></a>Sleutel, waardetype: `oauth2AllowImplicitFlow`, Boolean 
Hiermee geeft u op of deze web-app OAuth 2.0 impliciet flow-toegangs tokens kan aanvragen. De standaard waarde is False. Deze markering wordt gebruikt voor apps die zijn gebaseerd op de browser, zoals Java script-apps met één pagina. Als u meer wilt weten, voert u `OAuth 2.0 implicit grant flow` in de inhouds opgave in en raadpleegt u de onderwerpen over impliciete stroom. 

Voorbeeldwaarde: `false` 

### <a name="key-value-type-oauth2allowidtokenimplicitflow-boolean"></a>Sleutel, waardetype: `oauth2AllowIdTokenImplicitFlow`, Boolean 
Hiermee geeft u op of deze web-app OAuth 2.0 impliciete stroom-ID-tokens kan aanvragen. De standaard waarde is False. Deze markering wordt gebruikt voor apps die zijn gebaseerd op de browser, zoals Java script-apps met één pagina. 

Voorbeeldwaarde: `false` 

### <a name="key-value-type-oauth2permissions-collection"></a>Sleutel, waardetype: `oauth2Permissions`, verzameling 
Hiermee geeft u de verzameling OAuth 2,0-machtigings bereik op die de Web-API (resource)-app beschikbaar maakt voor client-apps. Deze machtigings bereiken kunnen worden verleend aan client-apps tijdens toestemming. 

Voorbeeld waarde: 
<code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"adminConsentDescription":"Allow the app to access resources on behalf of the signed-in user.",<br>&nbsp;&nbsp;&nbsp;"adminConsentDisplayName":"Access resource1",<br>&nbsp;&nbsp;&nbsp;"id":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"isEnabled":true,<br>&nbsp;&nbsp;&nbsp;"type":"User",<br>&nbsp;&nbsp;&nbsp;"userConsentDescription":"Allow the app to access resource1 on your behalf.",<br>&nbsp;&nbsp;&nbsp;"userConsentDisplayName":"Access resources",<br>&nbsp;&nbsp;&nbsp;"value":"user_impersonation"<br>&nbsp;&nbsp;}<br>] </code>

### <a name="key-value-type-oauth2requiredpostresponse-boolean"></a>Sleutel, waardetype: `oauth2RequiredPostResponse`, Boolean 
Hiermee geeft u op of door Azure AD POST-aanvragen worden toegestaan als onderdeel van OAuth 2,0-token aanvragen. De standaard waarde is False. Hiermee wordt aangegeven dat alleen GET-aanvragen worden toegestaan. 

Voorbeeldwaarde: `false` 

### <a name="key-value-type-parentalcontrolsettings-string"></a>Sleutel, waardetype: `parentalControlSettings`, String 

`countriesBlockedForMinors` geeft de landen aan waarin de app is geblokkeerd voor minder jarigen.<br>met `legalAgeGroupRule` geeft u de regel voor de juridische leeftijds groep op die van toepassing is op gebruikers van de app. Kan worden ingesteld op `Allow`, `RequireConsentForPrivacyServices`, `RequireConsentForMinors`, `RequireConsentForKids`of `BlockMinors`.  

Voorbeeld waarde: 
<code>{<br>&nbsp;&nbsp;&nbsp;"countriesBlockedForMinors":[],<br>&nbsp;&nbsp;&nbsp;"legalAgeGroupRule":"Allow"<br>} </code> 

### <a name="key-value-type-passwordcredentials-collection"></a>Sleutel, waardetype: `passwordCredentials`, verzameling 
Zie de beschrijving voor de eigenschap `keyCredentials`. 

Voorbeeld waarde: 
<code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"customKeyIdentifier":null,<br>&nbsp;&nbsp;&nbsp;"endDate":"2018-10-19T17:59:59.6521653Z",<br>&nbsp;&nbsp;&nbsp;"keyId":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"startDate":"2016-10-19T17:59:59.6521653Z",<br>&nbsp;&nbsp;&nbsp;"value":null<br>&nbsp;&nbsp;&nbsp;}<br>] </code> 

### <a name="key-value-type-preauthorizedapplications-collection"></a>Sleutel, waardetype: `preAuthorizedApplications`, verzameling 
Een lijst met toepassingen en aangevraagde machtigingen voor impliciete toestemming. Vereist dat een beheerder toestemming heeft gegeven voor de toepassing. de gebruiker is niet verplicht om toestemming te geven voor de aangevraagde machtigingen. De machtigingen die worden vermeld in preAuthorizedApplications, vereisen geen toestemming van de gebruiker. Andere aangevraagde machtigingen die niet worden vermeld in preAuthorizedApplications, vereisen echter toestemming van de gebruiker. 

Voorbeeld waarde: 
<code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;"appId": "abcdefg2-000a-1111-a0e5-812ed8dd72e8",<br>&nbsp;&nbsp;&nbsp;&nbsp;"permissionIds": [<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"8748f7db-21fe-4c83-8ab5-53033933c8f1"<br>&nbsp;&nbsp;&nbsp;&nbsp;]<br>&nbsp;&nbsp;}<br>]</code> 

### <a name="key-value-type-publicclient-boolean"></a>Sleutel, waardetype: `publicClient`, Boolean 
Hiermee geeft u op of deze toepassing een open bare client is (zoals een geïnstalleerde toepassing die wordt uitgevoerd op een mobiel apparaat). 

Deze eigenschap is alleen beschikbaar in de ervaring **app-registraties (verouderd)** . Vervangen door `allowPublicClient` in de [app-registraties](https://go.microsoft.com/fwlink/?linkid=2083908) -ervaring. 

### <a name="key-value-type-publisherdomain-string"></a>Sleutel, waardetype: `publisherDomain`, String 
Het geverifieerde uitgevers domein voor de toepassing. Alleen-lezen. 

Voorbeeldwaarde: `https://www.contoso.com`

### <a name="key-value-type-replyurls-string-array"></a>Sleutel, waardetype: `replyUrls`, teken reeks matrix 
Deze eigenschap met meerdere waarden bevat de lijst met geregistreerde redirect_uri waarden die Azure AD accepteert als doelen bij het retour neren van tokens. <br><br> Deze eigenschap is alleen beschikbaar in de ervaring **app-registraties (verouderd)** . Vervangen door `replyUrlsWithType` in de [app-registraties](https://go.microsoft.com/fwlink/?linkid=2083908) -ervaring. 

### <a name="key-value-type-replyurlswithtype-collection"></a>Sleutel, waardetype: `replyUrlsWithType`, verzameling 
Deze eigenschap met meerdere waarden bevat de lijst met geregistreerde redirect_uri waarden die Azure AD accepteert als doelen bij het retour neren van tokens. Elke URI-waarde moet een gekoppelde app-type waarde bevatten. Ondersteunde type waarden zijn: <ul><li>`Web`</li><li>`InstalledClient`</li></ul><br> Meer informatie over [replyUrl-beperkingen en-beperkingen](https://docs.microsoft.com/azure/active-directory/develop/reply-url). 

Voorbeeld waarde: 
<code>"replyUrlsWithType":&nbsp;[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"url":&nbsp;"https://localhost:4400/services/office365/redirectTarget.html",<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"type":&nbsp;"InstalledClient"&nbsp;&nbsp;&nbsp;<br>&nbsp;&nbsp;}<br>]</code> 

### <a name="key-value-type-requiredresourceaccess-collection"></a>Sleutel, waardetype: `requiredResourceAccess`, verzameling 
Met dynamische toestemming heeft `requiredResourceAccess` de toestemming van de beheerder en de gebruikers toestemming verleend voor gebruikers die een statische toestemming gebruiken. Deze para meter is echter niet van toepassing op de gebruikers toestemming voor algemeen gebruik.<br>`resourceAppId` is de unieke id voor de resource waartoe de app toegang vereist. Deze waarde moet gelijk zijn aan de appId die is gedeclareerd voor de doel resource-app.<br>`resourceAccess` is een matrix met een lijst met de OAuth 2.0-machtigings bereiken en app-rollen die voor de app van de opgegeven resource zijn vereist. Bevat de `id` en `type` waarden van de opgegeven resources. 

Voorbeeld waarde: 
<code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;"resourceAppId":"00000002-0000-0000-c000-000000000000",<br>&nbsp;&nbsp;&nbsp;&nbsp;"resourceAccess":[<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"id":"311a71cc-e848-46a1-bdf8-97ff7156d8e6",<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"type":"Scope"<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;}<br>&nbsp;&nbsp;&nbsp;&nbsp;]<br>&nbsp;&nbsp;}<br>] </code> 

### <a name="key-value-type-samlmetadataurl-string"></a>Sleutel, waardetype: `samlMetadataUrl`, String 
De URL naar de SAML-meta gegevens voor de app. 

Voorbeeldwaarde: `https://MyRegisteredAppSAMLMetadata` 

### <a name="key-value-type-signinurl-string"></a>Sleutel, waardetype: `signInUrl`, String 

Hiermee geeft u de URL naar de start pagina van de app op. 

Voorbeeldwaarde: `https://MyRegisteredApp` 

### <a name="key-value-type-signinaudience-string"></a>Sleutel, waardetype: `signInAudience`, String 
Hiermee geeft u op welke micro soft-accounts voor de huidige toepassing worden ondersteund. Ondersteunde waarden zijn:
- **AzureADMyOrg** : gebruikers met een werk-of school account van micro soft in de Azure AD-Tenant van mijn organisatie (bijvoorbeeld één Tenant)
- **AzureADMultipleOrgs** : gebruikers met een werk-of school account van micro soft in de Azure AD-Tenant van elke organisatie (bijvoorbeeld multi tenant)
- **AzureADandPersonalMicrosoftAccount** : gebruikers met een persoonlijk Microsoft-account, of een werk-of school account in de Azure AD-Tenant van elke organisatie 

Voorbeeld waarde:  
`AzureADandPersonalMicrosoftAccount` 

### <a name="key-value-type-tags-string-array"></a>Sleutel, waardetype: `tags`, teken reeks matrix 
Aangepaste teken reeksen die kunnen worden gebruikt voor het categoriseren en identificeren van de toepassing. 

Voorbeeld waarde: 
<code>[<br>&nbsp;&nbsp;"ProductionApp"<br>]</code>

## <a name="common-issues"></a>Algemene problemen

### <a name="manifest-limits"></a>Limieten voor manifest

Een toepassings manifest heeft meerdere kenmerken die als verzamelingen worden aangeduid. bijvoorbeeld approles, knownClientApplications, identifierUris, rediretUris, requiredResourceAccess en oauth2Permissions. In het volledige toepassings manifest voor elke toepassing is het totale aantal vermeldingen in alle verzamelingen gecombineerd op 1200. Als u in het manifest van de toepassing eerder 100 omleidings-Uri's hebt opgegeven, bent u nog niet meer met 1100 resterende vermeldingen voor gebruik in alle andere verzamelingen gecombineerd waaruit het manifest wordt gemaakt.

> [!NOTE]
> Als u probeert meer dan 1200 vermeldingen toe te voegen aan het toepassings manifest, ziet u mogelijk een fout **' kan de toepassing xxxxxx niet bijwerken. Fout Details: de limiet voor de grootte van het manifest is overschreden. Verminder het aantal waarden en probeer de aanvraag opnieuw uit te voeren. "**

### <a name="unsupported-attributes"></a>Niet-ondersteunde kenmerken

Het manifest van de toepassing vertegenwoordigt het schema van het onderliggende toepassings model in azure AD. Naarmate het onderliggende schema zich ontwikkelt, wordt de manifest editor bijgewerkt zodat het nieuwe schema van tijd tot tijd wordt weer gegeven. Als gevolg hiervan ziet u mogelijk nieuwe kenmerken die worden weer gegeven in het manifest van de toepassing. In zeldzame gevallen kunt u een syntactische of semantische wijziging in de bestaande kenmerken opmerken of een kenmerk dat eerder bestond niet meer wordt ondersteund. U ziet bijvoorbeeld nieuwe kenmerken in de [app-registraties](https://go.microsoft.com/fwlink/?linkid=2083908), die bekend zijn met een andere naam in de app-registraties (verouderde) ervaring.

| App-registraties (verouderd)| App-registraties           |
|---------------------------|-----------------------------|
| `availableToOtherTenants` | `signInAudience`            |
| `displayName`             | `name`                      |
| `errorUrl`                | -                           |
| `homepage`                | `signInUrl`                 |
| `objectId`                | `Id`                        |
| `publicClient`            | `allowPublicClient`         |
| `replyUrls`               | `replyUrlsWithType`         |

Zie de sectie [manifest verwijzing](#manifest-reference) voor beschrijvingen van deze kenmerken.

Wanneer u een eerder gedownload manifest probeert te uploaden, ziet u mogelijk een van de volgende fouten. Deze fout is waarschijnlijk omdat de manifest editor nu ondersteuning biedt voor een nieuwere versie van het schema, wat niet overeenkomt met het bestand dat u wilt uploaden.

* Het bijwerken van de xxxxxx-toepassing is mislukt. Fout Details: ongeldige object-id niet gedefinieerd. []."
* Het bijwerken van de xxxxxx-toepassing is mislukt. Fout Details: een of meer opgegeven eigenschaps waarden zijn ongeldig. []."
* Het bijwerken van de xxxxxx-toepassing is mislukt. Fout Details: het is niet toegestaan om availableToOtherTenants in te stellen in deze API-versie voor de update. []."
* Het bijwerken van de xxxxxx-toepassing is mislukt. Fout Details: het bijwerken van de eigenschap replyUrls is niet toegestaan voor deze toepassing. Gebruik in plaats daarvan de eigenschap replyUrlsWithType. []."
* Het bijwerken van de xxxxxx-toepassing is mislukt. Fout Details: er is een waarde zonder type naam gevonden en er is geen verwacht type beschikbaar. Wanneer het model is opgegeven, moet elke waarde in de payload een type hebben dat kan worden opgegeven in de payload, expliciet door de aanroeper of impliciet afgeleid van de bovenliggende waarde. []"

Wanneer u een van deze fouten ziet, raden we u aan de volgende acties uit te voeren:

1. Bewerk de kenmerken afzonderlijk in de manifest editor in plaats van een eerder gedownload manifest te uploaden. Gebruik de [verwijzings](#manifest-reference) tabel van het manifest om inzicht te krijgen in de syntaxis en semantiek van oude en nieuwe kenmerken, zodat u de kenmerken kunt bewerken waarin u bent geïnteresseerd. 
1. Als uw werk stroom vereist dat u de manifesten in uw bron opslagplaats opslaat voor later gebruik, wordt u aangeraden om de opgeslagen manifesten in uw opslag plaats te herbaseren met de map die u ziet in de **app-registraties** -ervaring.

## <a name="next-steps"></a>Volgende stappen

* Zie [Application and Service Principal Objects in azure AD](app-objects-and-service-principals.md)(Engelstalig) voor meer informatie over de relatie tussen de toepassing en Service-Principal-objecten van een app.
* Zie de [verklarende woorden lijst voor ontwikkel aars van micro soft Identity platform](developer-glossary.md) voor definities van sommige basis concepten voor ontwikkel aars van micro soft Identity platform.

Gebruik de volgende opmerkingen sectie om uw inhoud te verfijnen en vorm te geven.

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
