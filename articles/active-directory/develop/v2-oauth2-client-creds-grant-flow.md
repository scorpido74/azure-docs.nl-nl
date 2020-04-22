---
title: De stroom van oAuth 2.0-clientreferenties op het Microsoft-identiteitsplatform | Azure
description: Bouw webapplicaties met behulp van het Microsoft-identiteitsplatform dat wordt geïmplementeerd in het OAuth 2.0-verificatieprotocol.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: f559dc0da8680a6cd3243b5ee12c3145244c9c2c
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81677868"
---
# <a name="microsoft-identity-platform-and-the-oauth-20-client-credentials-flow"></a>Microsoft-identiteitsplatform en de stroom van de OAuth 2.0-clientreferenties

U de [OAuth 2.0-clientreferentiessubsidie](https://tools.ietf.org/html/rfc6749#section-4.4) gebruiken die is opgegeven in RFC 6749, ook wel *tweebenige OAuth*genoemd, om toegang te krijgen tot webgehoste bronnen met behulp van de identiteit van een toepassing. Dit type subsidie wordt vaak gebruikt voor server-naar-server-interacties die op de achtergrond moeten worden uitgevoerd, zonder onmiddellijke interactie met een gebruiker. Dit soort toepassingen worden vaak aangeduid als *daemons* of *service accounts*.

In dit artikel wordt beschreven hoe u rechtstreeks programmeren tegen het protocol in uw toepassing. Waar mogelijk raden we u aan de ondersteunde Microsoft Authentication Libraries (MSAL) te gebruiken om tokens te [verkrijgen en beveiligde web-API's te bellen.](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows)  Kijk ook eens naar de [voorbeeld-apps die MSAL gebruiken.](sample-v2-code.md)

Met de subsidiestroom van de OAuth 2.0-clientreferenties kan een webservice (vertrouwelijke client) zijn eigen referenties gebruiken, in plaats van zich voor te doen als een gebruiker, om te verifiëren wanneer u een andere webservice aanroept. In dit scenario is de client meestal een middle-tier webservice, een daemon-service of een website. Voor een hoger niveau van zekerheid, het Microsoft-identiteitsplatform staat de oproepservice ook toe om een certificaat (in plaats van een gedeeld geheim) als referentie te gebruiken.

In de meer typische *driebenige OAuth*krijgt een clienttoepassing toestemming om namens een specifieke gebruiker toegang te krijgen tot een bron. De toestemming wordt gedelegeerd van de gebruiker aan de toepassing, meestal tijdens het [toestemmingsproces.](v2-permissions-and-consent.md) Echter, in de client referenties (*tweebenige OAuth*) stroom, machtigingen worden direct verleend aan de toepassing zelf. Wanneer de app een token aan een resource presenteert, dwingt de resource dat de app zelf toestemming heeft om een actie uit te voeren en niet de gebruiker.

## <a name="protocol-diagram"></a>Protocoldiagram

De volledige stroom van clientreferenties lijkt op het volgende diagram. We beschrijven elk van de stappen later in dit artikel.

![Diagram met de stroom van clientreferenties](./media/v2-oauth2-client-creds-grant-flow/convergence-scenarios-client-creds.svg)

## <a name="get-direct-authorization"></a>Directe autorisatie krijgen

Een app krijgt doorgaans een directe autorisatie om op twee manieren toegang te krijgen tot een bron:

* [Via een toegangscontrolelijst (ACL) bij de resource](#access-control-lists)
* [Toewijzing van toepassingsmachtigingen in Azure AD](#application-permissions)

Deze twee methoden zijn de meest voorkomende in Azure AD en we raden ze aan voor clients en resources die de stroom van clientreferenties uitvoeren. Een resource kan er ook voor kiezen om zijn klanten op andere manieren te autoriseren. Elke resourceserver kan de methode kiezen die het meest zinvol is voor de toepassing ervan.

### <a name="access-control-lists"></a>Toegangsbeheerlijsten

Een resourceprovider kan een autorisatiecontrole afdwingen op basis van een lijst met toepassings-id's die hij kent en waartoe hij een specifiek niveau van toegang verleent. Wanneer de bron een token ontvangt van het eindpunt van het Microsoft-identiteitsplatform, kan `appid` deze `iss` het token decoderen en de toepassings-id van de client uit de en claims halen. Vervolgens vergelijkt de toepassing met een toegangscontrolelijst (ACL) die het onderhoudt. De granulariteit en methode van de ACL kunnen aanzienlijk verschillen tussen resources.

Een veelgebruikte use case is het gebruik van een ACL om tests uit te voeren voor een webtoepassing of voor een web-API. De web-API verleent mogelijk alleen een subset van volledige machtigingen aan een specifieke client. Als u end-to-end-tests op de API wilt uitvoeren, maakt u een testclient die tokens van het eindpunt van het Microsoft-identiteitsplatform verwerft en deze vervolgens naar de API verzendt. De API controleert vervolgens de ACL op de toepassings-id van de testclient op volledige toegang tot de volledige functionaliteit van de API. Als u dit soort ACL gebruikt, moet u niet `appid` alleen de waarde `iss` van de beller valideren, maar ook valideren dat de waarde van het token wordt vertrouwd.

Dit type autorisatie is gebruikelijk voor daemons en serviceaccounts die toegang moeten hebben tot gegevens die eigendom zijn van consumentengebruikers die persoonlijke Microsoft-accounts hebben. Voor gegevens die eigendom zijn van organisaties, raden we u aan de benodigde autorisatie te krijgen via toepassingsmachtigingen.

### <a name="application-permissions"></a>Toepassingsmachtigingen

In plaats van ACL's te gebruiken, u API's gebruiken om een set **toepassingsmachtigingen**bloot te leggen. Een toepassingsmachtiging wordt verleend aan een toepassing door de beheerder van een organisatie en kan alleen worden gebruikt om toegang te krijgen tot gegevens die eigendom zijn van die organisatie en haar werknemers. Microsoft Graph legt bijvoorbeeld verschillende toepassingsmachtigingen bloot om het volgende te doen:

* E-mail lezen in alle postvakken
* E-mail lezen en schrijven in alle postvakken
* E-mail verzenden als elke gebruiker
* Mapgegevens lezen

Ga voor meer informatie over toepassingsmachtigingen naar [Microsoft Graph](https://developer.microsoft.com/graph).

Als u toepassingsmachtigingen in uw app wilt gebruiken, voert u de stappen uit die in de volgende secties worden besproken.


> [!NOTE]
> Wanneer u als toepassing authenticeert, in tegenstelling tot bij een gebruiker, u geen 'gedelegeerde machtigingen' (scopes die door een gebruiker worden verleend) gebruiken.  U moet "toepassingsmachtigingen" gebruiken, ook wel "rollen" genoemd, die worden verleend door een beheerder voor de toepassing (of via pre-autorisatie door de web-API).


#### <a name="request-the-permissions-in-the-app-registration-portal"></a>De machtigingen aanvragen in de app-registratieportal

1. Registreer en maak een app via de nieuwe [App-registratie (Preview) ervaring](quickstart-register-app.md).
2. Ga naar uw toepassing in de app-registratie (Preview)-ervaring. Navigeer naar de sectie **Certificaten & geheimen** en voeg een nieuw **clientgeheim**toe, omdat u ten minste één clientgeheim nodig hebt om een token aan te vragen.
3. Zoek de sectie **API-machtigingen** en voeg vervolgens de **toepassingsmachtigingen** toe die uw app vereist.
4. Sla de app-registratie **op.**

#### <a name="recommended-sign-the-user-into-your-app"></a>Aanbevolen: de gebruiker aanmelden bij uw app

Wanneer u een toepassing maakt die toepassingsmachtigingen gebruikt, heeft de app doorgaans een pagina of weergave nodig waarop de beheerder de machtigingen van de app goedkeurt. Deze pagina kan deel uitmaken van de aanmeldingsstroom van de app, onderdeel van de instellingen van de app, of het kan een speciale "connect"-stroom zijn. In veel gevallen is het zinvol dat de app deze 'connect'-weergave pas weergeeft nadat een gebruiker zich heeft aangemeld met een Microsoft-account op het werk of op school.

Als u de gebruiker aantekent bij uw app, u de organisatie identificeren waartoe de gebruiker behoort voordat u de gebruiker vraagt de toepassingsmachtigingen goed te keuren. Hoewel het niet strikt noodzakelijk is, kan het u helpen een intuïtievere ervaring voor uw gebruikers te creëren. Als u de gebruiker wilt aanmelden, volgt u onze zelfstudies van het [Microsoft-identiteitsplatformprotocol](active-directory-v2-protocols.md).

#### <a name="request-the-permissions-from-a-directory-admin"></a>De machtigingen aanvragen bij een directorybeheerder

Wanneer u klaar bent om machtigingen aan te vragen bij de beheerder van de organisatie, u de gebruiker doorverwijzen naar het eindpunt van de *toestemming van*de microsoft-identiteitsplatformbeheerder.

> [!TIP]
> Probeer dit verzoek uit te voeren in Postman! (Gebruik uw eigen app-id voor de beste resultaten - de zelfstudietoepassing vraagt geen nuttige machtigingen aan.) [Probeer dit verzoek uit te voeren in Postman ![](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

```
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/adminconsent?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&state=12345
&redirect_uri=http://localhost/myapp/permissions
```

```
// Pro tip: Try pasting the following request in a browser.
```

```
https://login.microsoftonline.com/common/adminconsent?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&state=12345&redirect_uri=http://localhost/myapp/permissions
```

| Parameter | Voorwaarde | Beschrijving |
| --- | --- | --- |
| `tenant` | Vereist | De directorytenant waarvan u toestemming wilt vragen. Dit kan in GUID of vriendelijke naam formaat. Als u niet weet tot welke tenant de gebruiker behoort en u wilt `common`deze laten inloggen met een tenant, gebruikt u . |
| `client_id` | Vereist | De **toepassings-id (client)** die de [Azure-portal – App-registraties](https://go.microsoft.com/fwlink/?linkid=2083908) ervaring toegewezen aan uw app. |
| `redirect_uri` | Vereist | De omleiding URI waar u wilt dat het antwoord wordt verzonden voor uw app te verwerken. Het moet precies overeenkomen met een van de omleiding URI's die u geregistreerd in de portal, behalve dat het moet worden URL gecodeerd, en het kan extra pad segmenten hebben. |
| `state` | Aanbevolen | Een waarde die is opgenomen in het verzoek dat ook wordt geretourneerd in het tokenantwoord. Het kan een reeks van alle inhoud die u wilt. De status wordt gebruikt om informatie over de status van de gebruiker in de app te coderen voordat het verificatieverzoek is ingediend, zoals de pagina of weergave waarop ze zich bevonden. |

Op dit moment dwingt Azure AD af dat alleen een tenantbeheerder zich kan aanmelden bij het voltooien van de aanvraag. De beheerder wordt gevraagd om alle directe toepassingsmachtigingen goed te keuren die u voor uw app hebt aangevraagd in de app-registratieportal.

##### <a name="successful-response"></a>Succesvolle reactie

Als de beheerder de machtigingen voor uw toepassing goedkeurt, ziet het geslaagde antwoord er als volgt uit:

```
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| Parameter | Beschrijving |
| --- | --- |
| `tenant` | De directorytenant die uw aanvraag de door deze aanvraag gevraagde machtigingen heeft verleend, in GUID-indeling. |
| `state` | Een waarde die is opgenomen in het verzoek dat ook wordt geretourneerd in het tokenantwoord. Het kan een reeks van alle inhoud die u wilt. De status wordt gebruikt om informatie over de status van de gebruiker in de app te coderen voordat het verificatieverzoek is ingediend, zoals de pagina of weergave waarop ze zich bevonden. |
| `admin_consent` | Ingesteld op **True**. |

##### <a name="error-response"></a>Foutreactie

Als de beheerder de machtigingen voor uw toepassing niet goedkeurt, ziet het mislukte antwoord er als volgt uit:

```
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| Parameter | Beschrijving |
| --- | --- |
| `error` | Een foutcodetekenreeks die u gebruiken om typen fouten te classificeren en die u gebruiken om op fouten te reageren. |
| `error_description` | Een specifiek foutbericht waarmee u de hoofdoorzaak van een fout identificeren. |

Nadat u een succesvol antwoord hebt ontvangen van het eindpunt voor het inrichten van apps, heeft uw app de directe toepassingsmachtigingen gekregen die is aangevraagd. Nu u een token aanvragen voor de gewenste bron.

## <a name="get-a-token"></a>Een token ophalen

Nadat u de benodigde autorisatie voor uw aanvraag hebt verkregen, gaat u verder met het verkrijgen van toegangstokens voor API's. Als u een token wilt krijgen met behulp van `/token` de toekenning van clientreferenties, stuurt u een post-verzoek naar het eindpunt van het Microsoft-identiteitsplatform:

> [!TIP]
> Probeer dit verzoek uit te voeren in Postman! (Gebruik uw eigen app-id voor de beste resultaten - de zelfstudietoepassing vraagt geen nuttige machtigingen aan.) [Probeer dit verzoek uit te voeren in Postman ![](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

### <a name="first-case-access-token-request-with-a-shared-secret"></a>Eerste geval: Toegang tot tokenaanvraag met een gedeeld geheim

```
POST /{tenant}/oauth2/v2.0/token HTTP/1.1           //Line breaks for clarity
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=535fb089-9ff3-47b6-9bfb-4f1264799865
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_secret=qWgdYAmab0YSkuL1qKv5bPX
&grant_type=client_credentials
```

```
// Replace {tenant} with your tenant!
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=535fb089-9ff3-47b6-9bfb-4f1264799865&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default&client_secret=qWgdYAmab0YSkuL1qKv5bPX&grant_type=client_credentials' 'https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token'
```

| Parameter | Voorwaarde | Beschrijving |
| --- | --- | --- |
| `tenant` | Vereist | De directory tenant de toepassing plannen om te werken tegen, in GUID of domeinnaam formaat. |
| `client_id` | Vereist | De toepassings-id die aan uw app is toegewezen. U deze informatie vinden in de portal waar u uw app hebt geregistreerd. |
| `scope` | Vereist | De waarde die `scope` wordt doorgegeven voor de parameter in deze aanvraag moet de resource-id (application ID URI) zijn van de resource die u wilt, die is aangebracht met het `.default` achtervoegsel. Voor het voorbeeld van Microsoft `https://graph.microsoft.com/.default`Graph is de waarde . <br/>Deze waarde vertelt het eindpunt van het Microsoft-identiteitsplatform dat van alle directe toepassingsmachtigingen die u voor uw app hebt geconfigureerd, het eindpunt een token moet uitgeven voor de machtigingen die zijn gekoppeld aan de bron die u wilt gebruiken. Zie de `/.default` [toestemmingsdocumentatie](v2-permissions-and-consent.md#the-default-scope)voor meer informatie over het bereik. |
| `client_secret` | Vereist | Het clientgeheim dat u voor uw app hebt gegenereerd in de app-registratieportal. Het clientgeheim moet worden gecodeerd door de URL voordat het wordt verzonden. |
| `grant_type` | Vereist | Moet ingesteld `client_credentials`zijn op. |

### <a name="second-case-access-token-request-with-a-certificate"></a>Tweede aanvraag: Aanvraag voor toegangstoken met een certificaat

```
POST /{tenant}/oauth2/v2.0/token HTTP/1.1               // Line breaks for clarity
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_id=97e0a5b7-d745-40b6-94fe-5f77d35c6e05
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&grant_type=client_credentials
```

| Parameter | Voorwaarde | Beschrijving |
| --- | --- | --- |
| `tenant` | Vereist | De directory tenant de toepassing plannen om te werken tegen, in GUID of domeinnaam formaat. |
| `client_id` | Vereist |De toepassings-id (client) die aan uw app is toegewezen. |
| `scope` | Vereist | De waarde die `scope` wordt doorgegeven voor de parameter in deze aanvraag moet de resource-id (application ID URI) zijn van de resource die u wilt, die is aangebracht met het `.default` achtervoegsel. Voor het voorbeeld van Microsoft `https://graph.microsoft.com/.default`Graph is de waarde . <br/>Deze waarde informeert het eindpunt van het Microsoft-identiteitsplatform dat van alle directe toepassingsmachtigingen die u voor uw app hebt geconfigureerd, een token moet uitgeven voor de machtigingen die zijn gekoppeld aan de bron die u wilt gebruiken. Zie de `/.default` [toestemmingsdocumentatie](v2-permissions-and-consent.md#the-default-scope)voor meer informatie over het bereik. |
| `client_assertion_type` | Vereist | De waarde moet `urn:ietf:params:oauth:client-assertion-type:jwt-bearer`worden ingesteld op . |
| `client_assertion` | Vereist | Een bewering (een JSON-webtoken) dat u moet maken en ondertekenen met het certificaat dat u hebt geregistreerd als referenties voor uw toepassing. Lees meer over [certificaatreferenties](active-directory-certificate-credentials.md) om te leren hoe u uw certificaat en de notatie van de bewering registreert.|
| `grant_type` | Vereist | Moet ingesteld `client_credentials`zijn op. |

Merk op dat de parameters zijn bijna hetzelfde als in het geval van het verzoek door gedeeld geheim, behalve dat de client_secret parameter wordt vervangen door twee parameters: een client_assertion_type en client_assertion.

### <a name="successful-response"></a>Succesvolle reactie

Een geslaagd antwoord ziet er zo uit:

```
{
  "token_type": "Bearer",
  "expires_in": 3599,
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBP..."
}
```

| Parameter | Beschrijving |
| --- | --- |
| `access_token` | Het gevraagde toegangstoken. De app kan dit token gebruiken om te verifiëren voor de beveiligde bron, zoals een web-API. |
| `token_type` | Geeft de waarde van het tokentype aan. Het enige type dat het `bearer`Identiteitsplatform van Microsoft ondersteunt, is . |
| `expires_in` | De tijd dat een toegangstoken geldig is (in seconden). |

### <a name="error-response"></a>Foutreactie

Een foutreactie ziet er als volgt uit:

```
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/.default is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| Parameter | Beschrijving |
| --- | --- |
| `error` | Een foutcodetekenreeks die u gebruiken om typen fouten die optreden te classificeren en om te reageren op fouten. |
| `error_description` | Een specifiek foutbericht waarmee u de hoofdoorzaak van een verificatiefout identificeren. |
| `error_codes` | Een lijst met STS-specifieke foutcodes die kunnen helpen bij diagnostische gegevens. |
| `timestamp` | Het tijdstip waarop de fout is opgetreden. |
| `trace_id` | Een unieke id voor het verzoek om te helpen met diagnostische gegevens. |
| `correlation_id` | Een unieke id voor het verzoek om te helpen met diagnostische gegevens over onderdelen. |

## <a name="use-a-token"></a>Een token gebruiken

Nu u een token hebt aangeschaft, gebruikt u het token om aanvragen voor de bron in te dienen. Wanneer het token verloopt, herhaalt u het verzoek tot het `/token` eindpunt om een nieuw toegangstoken te verkrijgen.

```
GET /v1.0/me/messages
Host: https://graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

```
// Pro tip: Try the following command! (Replace the token with your own.)
```

```
curl -X GET -H "Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbG...." 'https://graph.microsoft.com/v1.0/me/messages'
```

## <a name="code-samples-and-other-documentation"></a>Codemonsters en andere documentatie

De [overzichtsdocumentatie](https://aka.ms/msal-net-client-credentials) voor clientreferenties lezen uit de Microsoft-verificatiebibliotheek

| Voorbeeld | Platform |Beschrijving |
|--------|----------|------------|
|[active-directory-dotnetcore-daemon-v2](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) | .NET Core 2.1-console | Een eenvoudige .NET Core-toepassing die de gebruikers van een tenant die de Microsoft Graph opvraagt, weergeeft met behulp van de identiteit van de toepassing, in plaats van namens een gebruiker. Het voorbeeld illustreert ook de variatie met certificaten voor verificatie. |
|[active-directory-dotnet-daemon-v2](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2)|ASP.NET MVC | Een webtoepassing die gegevens uit de Microsoft Graph synchroniseert met de identiteit van de toepassing, in plaats van namens een gebruiker. |
