---
title: De oauth 2.0-autorisatiecodestroom in Azure AD begrijpen
description: In dit artikel wordt beschreven hoe u HTTP-berichten gebruikt om toegang tot webtoepassingen en web-API's in uw tenant te autoriseren met Azure Active Directory en OAuth 2.0.
services: active-directory
documentationcenter: .net
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 3ec7cf5a45ce31cde923dce521636589cfcda786
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154454"
---
# <a name="authorize-access-to-azure-active-directory-web-applications-using-the-oauth-20-code-grant-flow"></a>Azure Active Directory-webtoepassingen toegang verlenen met de stroom voor het verlenen van de OAuth 2.0-code

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

> [!NOTE]
>  Als u de server niet vertelt welke resource u van plan bent te bellen, activeert de server niet het beleid voor voorwaardelijke toegang voor die bron. Dus om MFA trigger hebben, moet u een bron in uw URL. 
>

Azure Active Directory (Azure AD) maakt gebruik van OAuth 2.0 om het mogelijk te maken dat u toegang tot webtoepassingen en web-API's in uw Azure AD-tenant kunt autoriseren. Deze handleiding is taalonafhankelijk en beschrijft hoe u HTTP-berichten verzenden en ontvangen zonder gebruik te maken van een van onze [open-source bibliotheken.](active-directory-authentication-libraries.md)

De OAuth 2.0 autorisatiecodestroom wordt beschreven in [punt 4.1 van de OAuth 2.0-specificatie](https://tools.ietf.org/html/rfc6749#section-4.1). Het wordt gebruikt om verificatie en autorisatie uit te voeren in de meeste toepassingstypen, waaronder web-apps en native geïnstalleerde apps.

## <a name="register-your-application-with-your-ad-tenant"></a>Uw toepassing registreren bij uw AD-tenant
Registreer uw toepassing eerst bij uw Azure Active Directory-tenant (Azure AD). Na registratie beschikt u over een toepassings-id voor uw toepassing en kan uw toepassing tokens ontvangen.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
   
1. Kies uw Azure AD-tenant door uw account te selecteren in de rechterbovenhoek van de pagina, gevolgd door de navigatie van de **Switch Directory** te selecteren en vervolgens de juiste tenant te selecteren. 
   - Sla deze stap over als u slechts één Azure AD-tenant onder uw account hebt of als u al de juiste Azure AD-tenant hebt geselecteerd.
   
1. Zoek en selecteer **Azure Active Directory**in de Azure-portal.
   
1. Selecteer **app-registratie**in het menu **Azure Active Directory** links en selecteer Nieuwe **registratie**.
   
1. Volg de aanwijzingen op het scherm en maak een nieuwe toepassing. Het maakt niet uit of het een webapplicatie of een openbare client (mobile & desktop) applicatie voor deze tutorial, maar als je wilt specifieke voorbeelden voor webapplicaties of openbare client toepassingen, check out onze [quickstarts](v1-overview.md).
   
   - **Naam** is de naam van de toepassing en beschrijft de toepassing voor eindgebruikers.
   - Selecteer onder **Ondersteunde accounttypen** de optie **Accounts in een organisatieadreslijst en persoonlijke Microsoft-account**.
   - Geef de **Omleiding URI**. Voor webtoepassingen is dit de basis-URL van uw app waar gebruikers zich kunnen aanmelden.  Bijvoorbeeld `http://localhost:12345`. Voor openbare clients (mobile & desktop) gebruikt Azure AD deze om tokenreacties terug te sturen. Voer een specifieke waarde in voor uw toepassing.  Bijvoorbeeld `http://MyFirstAADApp`.
   <!--TODO: add once App ID URI is configurable: The **App ID URI** is a unique identifier for your application. The convention is to use `https://<tenant-domain>/<app-name>`, e.g. `https://contoso.onmicrosoft.com/my-first-aad-app`-->  
   
1. Zodra u de registratie hebt voltooid, wijst Azure AD uw toepassing een unieke client-id toe (de **toepassings-id**). U hebt deze waarde nodig in de volgende secties, dus kopieer deze van de toepassingspagina.
   
1. Als u uw toepassing wilt vinden in de Azure-portal, selecteert u **App-registraties**en selecteert u **Alle toepassingen weergeven**.

## <a name="oauth-20-authorization-flow"></a>OAuth 2.0-autorisatiestroom

Op een hoog niveau ziet de volledige autorisatiestroom voor een toepassing er een beetje als volgt uit:

![OAuth Auth-codestroom](./media/v1-protocols-oauth-code/active-directory-oauth-code-flow-native-app.png)

## <a name="request-an-authorization-code"></a>Een autorisatiecode aanvragen

De autorisatiecodestroom begint met de client `/authorize` die de gebruiker naar het eindpunt leidt. In dit verzoek geeft de client de machtigingen aan die hij van de gebruiker moet verkrijgen. U het eindpunt van de OAuth 2.0-autorisatie voor uw tenant krijgen door **App-registraties > Eindpunten** in de Azure-portal te selecteren.

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%3A12345
&response_mode=query
&resource=https%3A%2F%2Fservice.contoso.com%2F
&state=12345
```

| Parameter |  | Beschrijving |
| --- | --- | --- |
| tenant |vereist |De `{tenant}` waarde in het pad van de aanvraag kan worden gebruikt om te bepalen wie zich bij de toepassing kan aanmelden. De toegestane waarden zijn tenant-id's, bijvoorbeeld, `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` of `contoso.onmicrosoft.com` `common` voor tenant-onafhankelijke tokens |
| client_id |vereist |De toepassings-id die aan uw app is toegewezen toen u deze registreerde bij Azure AD. U vindt dit in de Azure Portal. Klik **op Azure Active Directory** in de zijbalk van services, klik op **App-registraties**en kies de toepassing. |
| response_type |vereist |Moet `code` voor de stroom van de vergunningscode omvatten. |
| redirect_uri |Aanbevolen |De redirect_uri van uw app, waar verificatiereacties door uw app kunnen worden verzonden en ontvangen. Het moet precies overeenkomen met een van de redirect_uris die u hebt geregistreerd in de portal, behalve dat het moet worden gecodeerd. Voor native & mobiele apps, moet `https://login.microsoftonline.com/common/oauth2/nativeclient`u de standaardwaarde van . |
| response_mode |optioneel |Hiermee geeft u de methode op die moet worden gebruikt om het resulterende token terug te sturen naar uw app. Kan `query`worden `fragment`, `form_post`, of . `query`geeft de code als parameter voor querytekenreeksen op uw omleidings-URI. Als u een ID-token aanvraagt met behulp `query` van de impliciete stroom, u niet gebruiken zoals opgegeven in de [OpenID-spec](https://openid.net/specs/oauth-v2-multiple-response-types-1_0.html#Combinations). Als u alleen de code aanvraagt, `query` `fragment`kunt `form_post`u , of . `form_post`hiermee wordt een post met de code naar uw omleiding uri uitgevoerd. De standaardinstelling is `query` voor een codestroom.  |
| state |Aanbevolen |Een waarde die is opgenomen in het verzoek en die ook wordt geretourneerd in het tokenantwoord. Een willekeurig gegenereerde unieke waarde wordt meestal gebruikt voor [het voorkomen van cross-site request vervalsing aanvallen](https://tools.ietf.org/html/rfc6749#section-10.12). De status wordt ook gebruikt om informatie over de status van de gebruiker in de app te coderen voordat de verificatieaanvraag plaatsvond, zoals de pagina of weergave waarop ze zich bevonden. |
| resource | Aanbevolen |De App ID URI van de doelweb-API (beveiligde bron). Als u de URI voor app-id's wilt zoeken, klikt u in de Azure Portal op **Azure Active Directory**, klikt u op **Toepassingsregistraties**, opent u de pagina **Instellingen van** de toepassing en klikt u vervolgens op **Eigenschappen**. Het kan ook een `https://graph.microsoft.com`externe bron zoals . Dit is vereist in een van de autorisatie- of tokenaanvragen. Om ervoor te zorgen minder authenticatie prompts plaats het in de autorisatie aanvraag om ervoor te zorgen toestemming wordt ontvangen van de gebruiker. |
| scope | **Genegeerd** | Voor v1 Azure AD-apps moeten scopes statisch zijn geconfigureerd in de Azure Portal onder de **vereiste** **machtigingen**. |
| Prompt |optioneel |Geef het type gebruikersinteractie aan dat vereist is.<p> Geldige waarden zijn: <p> *inloggen*: De gebruiker moet worden gevraagd om opnieuw te verifiëren. <p> *select_account:* De gebruiker wordt gevraagd om een account te selecteren, waarbij een enkel aanmelding wordt onderbroken. De gebruiker kan een bestaand aangemeld account selecteren, zijn referenties invoeren voor een onthouden account of ervoor kiezen om een ander account te gebruiken. <p> *toestemming*: Toestemming van de gebruiker is verleend, maar moet worden bijgewerkt. De gebruiker moet worden gevraagd om toestemming. <p> *admin_consent*: Een beheerder moet worden gevraagd om toestemming te geven namens alle gebruikers in hun organisatie |
| login_hint |optioneel |Kan worden gebruikt om vooraf de gebruikersnaam/e-mailadres veld van de aanmeldingspagina voor de gebruiker in te vullen, als u hun gebruikersnaam van tevoren kent. Vaak gebruiken apps deze parameter tijdens de herverificatie, nadat ze de `preferred_username` gebruikersnaam al uit een eerdere aanmelding hebben gehaald met behulp van de claim. |
| domain_hint |optioneel |Geeft een hint over de tenant of het domein die de gebruiker moet gebruiken om zich aan te melden. De waarde van de domain_hint is een geregistreerd domein voor de tenant. Als de tenant wordt gefedereerd naar een on-premises directory, wordt AAD omgeleid naar de opgegeven tenantfederatieserver. |
| code_challenge_method | Aanbevolen    | De methode die wordt `code_verifier` gebruikt `code_challenge` om de parameter te coderen. Kan een `plain` van `S256`of . Indien uitgesloten, `code_challenge` wordt verondersteld plaintext `code_challenge` te zijn als wordt opgenomen. Azure AAD v1.0 `plain` `S256`ondersteunt beide en . Zie voor meer informatie de [PKCE RFC](https://tools.ietf.org/html/rfc7636). |
| code_challenge        | Aanbevolen    | Wordt gebruikt om autorisatiecode-subsidies te beveiligen via Proof Key for Code Exchange (PKCE) van een native of openbare client. Vereist `code_challenge_method` als is inbegrepen. Zie voor meer informatie de [PKCE RFC](https://tools.ietf.org/html/rfc7636). |

> [!NOTE]
> Als de gebruiker deel uitmaakt van een organisatie, kan een beheerder van de organisatie namens de gebruiker toestemming geven of weigeren, of de gebruiker toestemming geven. De gebruiker krijgt de mogelijkheid om alleen toestemming te geven wanneer de beheerder dit toestaat.
>
>

Op dit moment wordt de gebruiker gevraagd om zijn referenties in te voeren en in te stemmen met de door de app gevraagde machtigingen in de Azure Portal. Zodra de gebruiker zijn toestemming heeft verkregen en verleent, `redirect_uri` stuurt Azure AD een antwoord naar uw app op het adres in uw aanvraag met de code.

### <a name="successful-response"></a>Succesvolle reactie
Een succesvolle reactie kan er als volgt uitzien:

```
GET  HTTP/1.1 302 Found
Location: http://localhost:12345/?code= AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrqqf_ZT_p5uEAEJJ_nZ3UmphWygRNy2C3jJ239gV_DBnZ2syeg95Ki-374WHUP-i3yIhv5i-7KU2CEoPXwURQp6IVYMw-DjAOzn7C3JCu5wpngXmbZKtJdWmiBzHpcO2aICJPu1KvJrDLDP20chJBXzVYJtkfjviLNNW7l7Y3ydcHDsBRKZc3GuMQanmcghXPyoDg41g8XbwPudVh7uCmUponBQpIhbuffFP_tbV8SNzsPoFz9CLpBCZagJVXeqWoYMPe2dSsPiLO9Alf_YIe5zpi-zY4C3aLw5g9at35eZTfNd0gBRpR5ojkMIcZZ6IgAA&session_state=7B29111D-C220-4263-99AB-6F6E135D75EF&state=D79E5777-702E-4260-9A62-37F75FF22CCE
```

| Parameter | Beschrijving |
| --- | --- |
| admin_consent |De waarde is True als een beheerder heeft ingestemd met een toestemmingsverzoek. |
| code |De autorisatiecode die de aanvraag heeft aangevraagd. De toepassing kan de autorisatiecode gebruiken om een toegangstoken voor de doelbron aan te vragen. |
| session_state |Een unieke waarde die de huidige gebruikerssessie identificeert. Deze waarde is een GUID, maar moet worden behandeld als een ondoorzichtige waarde die zonder onderzoek wordt doorgegeven. |
| state |Als een parameter in de status in de aanvraag is opgenomen, moet dezelfde waarde in het antwoord worden weergegeven. Het is een goede gewoonte voor de toepassing om te controleren of de statuswaarden in de aanvraag en het antwoord identiek zijn voordat u het antwoord gebruikt. Dit helpt bij het detecteren [van Cross-Site Request Forgery (CSRF) aanvallen](https://tools.ietf.org/html/rfc6749#section-10.12) op de klant. |

### <a name="error-response"></a>Foutreactie
Foutreacties kunnen ook naar `redirect_uri` de toepassing worden verzonden, zodat deze op de juiste manier kunnen worden verwerkt.

```
GET http://localhost:12345/?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parameter | Beschrijving |
| --- | --- |
| error |Een foutcodewaarde gedefinieerd in sectie 5.2 van het [OAuth 2.0-autorisatiekader](https://tools.ietf.org/html/rfc6749). In de volgende tabel worden de foutcodes beschreven die Azure AD retourneert. |
| error_description |Een meer gedetailleerde beschrijving van de fout. Dit bericht is niet bedoeld om gebruiksvriendelijk te zijn. |
| state |De statuswaarde is een willekeurig gegenereerde niet-hergebruikte waarde die in de aanvraag wordt verzonden en wordt geretourneerd in het antwoord om aanvallen op valsheid in geschrifte (CsrF) te voorkomen. |

#### <a name="error-codes-for-authorization-endpoint-errors"></a>Foutcodes voor eindpuntfouten voor autorisatie
In de volgende tabel worden de verschillende foutcodes beschreven die kunnen worden geretourneerd in de `error` parameter van de foutreactie.

| Foutcode | Beschrijving | Clientactie |
| --- | --- | --- |
| invalid_request |Protocolfout, zoals een ontbrekende vereiste parameter. |De aanvraag herstellen en opnieuw indienen. Dit is een ontwikkelingsfout, en wordt meestal gevangen tijdens de eerste tests. |
| unauthorized_client |De clientaanvraag is niet toegestaan om een autorisatiecode aan te vragen. |Dit gebeurt meestal wanneer de clienttoepassing niet is geregistreerd in Azure AD of niet wordt toegevoegd aan de Azure AD-tenant van de gebruiker. De toepassing kan de gebruiker instructies geven voor het installeren van de toepassing en het toevoegen aan Azure AD. |
| access_denied |Eigenaar van de resource heeft geen toestemming geweigerd |De clienttoepassing kan de gebruiker laten weten dat deze niet door kan gaan, tenzij de gebruiker hiermee instemt. |
| unsupported_response_type |De autorisatieserver ondersteunt het antwoordtype in de aanvraag niet. |De aanvraag herstellen en opnieuw indienen. Dit is een ontwikkelingsfout, en wordt meestal gevangen tijdens de eerste tests. |
| server_error |Er is een onverwachte fout opgetreden op de server. |Probeer het verzoek opnieuw. Deze fouten kunnen het gevolg zijn van tijdelijke omstandigheden. De clienttoepassing kan de gebruiker uitleggen dat de reactie is vertraagd als gevolg van een tijdelijke fout. |
| temporarily_unavailable |De server is tijdelijk te druk om de aanvraag af te handelen. |Probeer het verzoek opnieuw. De clienttoepassing kan de gebruiker uitleggen dat de reactie is vertraagd als gevolg van een tijdelijke aandoening. |
| invalid_resource |De doelbron is ongeldig omdat deze niet bestaat, Azure AD deze niet kan vinden of niet correct is geconfigureerd. |Dit geeft aan dat de resource, als deze bestaat, niet is geconfigureerd in de tenant. De toepassing kan de gebruiker instructies geven voor het installeren van de toepassing en het toevoegen aan Azure AD. |

## <a name="use-the-authorization-code-to-request-an-access-token"></a>De autorisatiecode gebruiken om een toegangstoken aan te vragen
Nu u een autorisatiecode hebt verkregen en toestemming van de gebruiker hebt gekregen, u de code voor een `/token` toegangstoken voor de gewenste bron inwisselen door een POST-verzoek naar het eindpunt te sturen:

```
// Line breaks for legibility only

POST /{tenant}/oauth2/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded
grant_type=authorization_code
&client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrqqf_ZT_p5uEAEJJ_nZ3UmphWygRNy2C3jJ239gV_DBnZ2syeg95Ki-374WHUP-i3yIhv5i-7KU2CEoPXwURQp6IVYMw-DjAOzn7C3JCu5wpngXmbZKtJdWmiBzHpcO2aICJPu1KvJrDLDP20chJBXzVYJtkfjviLNNW7l7Y3ydcHDsBRKZc3GuMQanmcghXPyoDg41g8XbwPudVh7uCmUponBQpIhbuffFP_tbV8SNzsPoFz9CLpBCZagJVXeqWoYMPe2dSsPiLO9Alf_YIe5zpi-zY4C3aLw5g9at35eZTfNd0gBRpR5ojkMIcZZ6IgAA
&redirect_uri=https%3A%2F%2Flocalhost%3A12345
&resource=https%3A%2F%2Fservice.contoso.com%2F
&client_secret=p@ssw0rd

//NOTE: client_secret only required for web apps
```

| Parameter |  | Beschrijving |
| --- | --- | --- |
| tenant |vereist |De `{tenant}` waarde in het pad van de aanvraag kan worden gebruikt om te bepalen wie zich bij de toepassing kan aanmelden. De toegestane waarden zijn tenant-id's, bijvoorbeeld, `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` of `contoso.onmicrosoft.com` `common` voor tenant-onafhankelijke tokens |
| client_id |vereist |De toepassings-id die aan uw app is toegewezen toen u deze registreerde bij Azure AD. U vindt dit in de Azure-portal. De toepassings-id wordt weergegeven in de instellingen van de app-registratie. |
| grant_type |vereist |Moet `authorization_code` voor de autorisatiecodestroom zijn. |
| code |vereist |De `authorization_code` informatie die u in de vorige sectie hebt verworven |
| redirect_uri |vereist | Een `redirect_uri`geregistreerd op de client applicatie. |
| client_secret |vereist voor web-apps, niet toegestaan voor openbare klanten |Het toepassingsgeheim dat u hebt gemaakt in de Azure Portal voor uw app onder **Sleutels**. Het kan niet worden gebruikt in een native app (openbare client), omdat client_secrets niet betrouwbaar kunnen worden opgeslagen op apparaten. Het is vereist voor web-apps en web API's (alle `client_secret` vertrouwelijke clients), die de mogelijkheid hebben om de veilig op te slaan op de server kant. De client_secret moet worden gecodeerd voordat u wordt verzonden. |
| resource | Aanbevolen |De App ID URI van de doelweb-API (beveiligde bron). Als u de URI voor app-id's wilt zoeken, klikt u in de Azure Portal op **Azure Active Directory**, klikt u op **Toepassingsregistraties**, opent u de pagina **Instellingen van** de toepassing en klikt u vervolgens op **Eigenschappen**. Het kan ook een `https://graph.microsoft.com`externe bron zoals . Dit is vereist in een van de autorisatie- of tokenaanvragen. Om ervoor te zorgen minder authenticatie prompts plaats het in de autorisatie aanvraag om ervoor te zorgen toestemming wordt ontvangen van de gebruiker. Als in zowel de autorisatieaanvraag als de tokenaanvraag de parameters van de resource moeten overeenkomen. | 
| code_verifier | optioneel | Dezelfde code_verifier die werd gebruikt om de authorization_code te verkrijgen. Vereist als PKCE werd gebruikt in de vergunningscode subsidieaanvraag. Zie voor meer informatie de [PKCE RFC](https://tools.ietf.org/html/rfc7636)   |

Als u de URI voor app-id's wilt zoeken, klikt u in de Azure Portal op **Azure Active Directory**, klikt u op **Toepassingsregistraties**, opent u de pagina **Instellingen van** de toepassing en klikt u vervolgens op **Eigenschappen**.

### <a name="successful-response"></a>Succesvolle reactie
Azure AD retourneert een [toegangstoken](../develop/access-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) na een geslaagde reactie. Om netwerkoproepen van de clienttoepassing en de bijbehorende latentie te minimaliseren, moet de clienttoepassing toegangstokens in de cache opslaan voor de tokenlevensduur die is opgegeven in de OAuth 2.0-respons. Gebruik de parameterwaarden of `expires_in` `expires_on` parameterwaarden om de levensduur van het token te bepalen.

Als een web-API-bron een `invalid_token` foutcode retourneert, kan dit erop wijzen dat de bron heeft bepaald dat het token is verlopen. Als de kloktijden van de client en resource verschillend zijn (bekend als een 'tijdsverschil'), kan de resource het token beschouwen als verlopen voordat het token uit de clientcache wordt gewist. Als dit gebeurt, moet u het token uit de cache wissen, zelfs als het nog binnen de berekende levensduur valt.

Een succesvolle reactie kan er als volgt uitzien:

```
{
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ",
  "token_type": "Bearer",
  "expires_in": "3600",
  "expires_on": "1388444763",
  "resource": "https://service.contoso.com/",
  "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4rTfgV29ghDOHRc2B-C_hHeJaJICqjZ3mY2b_YNqmf9SoAylD1PycGCB90xzZeEDg6oBzOIPfYsbDWNf621pKo2Q3GGTHYlmNfwoc-OlrxK69hkha2CF12azM_NYhgO668yfcUl4VBbiSHZyd1NVZG5QTIOcbObu3qnLutbpadZGAxqjIbMkQ2bQS09fTrjMBtDE3D6kSMIodpCecoANon9b0LATkpitimVCrl-NyfN3oyG4ZCWu18M9-vEou4Sq-1oMDzExgAf61noxzkNiaTecM-Ve5cq6wHqYQjfV9DOz4lbceuYCAA",
  "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
  "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83ZmU4MTQ0Ny1kYTU3LTQzODUtYmVjYi02ZGU1N2YyMTQ3N2UvIiwiaWF0IjoxMzg4NDQwODYzLCJuYmYiOjEzODg0NDA4NjMsImV4cCI6MTM4ODQ0NDc2MywidmVyIjoiMS4wIiwidGlkIjoiN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlIiwib2lkIjoiNjgzODlhZTItNjJmYS00YjE4LTkxZmUtNTNkZDEwOWQ3NGY1IiwidXBuIjoiZnJhbmttQGNvbnRvc28uY29tIiwidW5pcXVlX25hbWUiOiJmcmFua21AY29udG9zby5jb20iLCJzdWIiOiJKV3ZZZENXUGhobHBTMVpzZjd5WVV4U2hVd3RVbTV5elBtd18talgzZkhZIiwiZmFtaWx5X25hbWUiOiJNaWxsZXIiLCJnaXZlbl9uYW1lIjoiRnJhbmsifQ."
}

```

| Parameter | Beschrijving |
| --- | --- |
| access_token |Het gevraagde toegangstoken.  Dit is een ondoorzichtige tekenreeks - het hangt af van wat de resource verwacht te ontvangen, en is niet bedoeld voor de client om naar te kijken. De app kan dit token gebruiken om te verifiëren voor de beveiligde bron, zoals een web-API. |
| token_type |Geeft de waarde van het tokentype aan. Het enige type dat Azure AD ondersteunt, is Drager. Zie [OAuth2.0 Authorization Framework: Bearer Token Usage (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt) voor meer informatie over tokens voor dragers. |
| expires_in |Hoe lang het toegangstoken geldig is (in seconden). |
| expires_on |Het tijdstip waarop het toegangstoken verloopt. De datum wordt weergegeven als het aantal seconden van 1970-01-01T0:0:0Z UTC tot de vervaldatum. Deze waarde wordt gebruikt om de levensduur van tokens in de cache te bepalen. |
| resource |De App ID URI van de web-API (beveiligde bron). |
| scope |Imitatiemachtigingen die zijn verleend aan de clienttoepassing. De standaardmachtiging `user_impersonation`is . De eigenaar van de beveiligde bron kan extra waarden registreren in Azure AD. |
| refresh_token |Een OAuth 2.0 refresh token. De app kan dit token gebruiken om extra toegangstokens te verkrijgen nadat het huidige toegangstoken is verlopen. Vernieuwingstokens zijn van lange duur en kunnen worden gebruikt om toegang tot bronnen voor langere tijd te behouden. |
| id_token |Een niet-ondertekend JSON-webtoken (JWT) dat een [ID-token](../develop/id-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)vertegenwoordigt. De app kan de segmenten van dit token ontcodeeren om informatie op te vragen over de gebruiker die zich heeft aangemeld. De app kan de waarden in de cache bewaren en weergeven, maar mag er niet op vertrouwen voor autorisatie- of beveiligingsgrenzen. |

Zie de [JWT IETF-conceptspecificatie](https://go.microsoft.com/fwlink/?LinkId=392344)voor meer informatie over JSON-webtokens.   Zie de `id_tokens`v1.0 OpenID Connect-stroom voor meer informatie over de [v1.0 OpenID Connect-stroom](v1-protocols-openid-connect-code.md).

### <a name="error-response"></a>Foutreactie
De eindpuntfouten voor tokenuitgifte zijn HTTP-foutcodes, omdat de client het eindpunt voor tokenuitgifte rechtstreeks aanroept. Naast de HTTP-statuscode retourneert het eindpunt voor azure-tokenuitgifte ook een JSON-document met objecten die de fout beschrijven.

Een voorbeeldfoutreactie kan er als volgt uitzien:

```
{
  "error": "invalid_grant",
  "error_description": "AADSTS70002: Error validating credentials. AADSTS70008: The provided authorization code or refresh token is expired. Send a new interactive authorization request for this user and resource.\r\nTrace ID: 3939d04c-d7ba-42bf-9cb7-1e5854cdce9e\r\nCorrelation ID: a8125194-2dc8-4078-90ba-7b6592a7f231\r\nTimestamp: 2016-04-11 18:00:12Z",
  "error_codes": [
    70002,
    70008
  ],
  "timestamp": "2016-04-11 18:00:12Z",
  "trace_id": "3939d04c-d7ba-42bf-9cb7-1e5854cdce9e",
  "correlation_id": "a8125194-2dc8-4078-90ba-7b6592a7f231"
}
```
| Parameter | Beschrijving |
| --- | --- |
| error |Een foutcodetekenreeks die kan worden gebruikt om typen fouten die zich voordoen te classificeren en kan worden gebruikt om op fouten te reageren. |
| error_description |Een specifiek foutbericht waarmee een ontwikkelaar de hoofdoorzaak van een verificatiefout kan identificeren. |
| error_codes |Een lijst met STS-specifieke foutcodes die kunnen helpen bij diagnostiek. |
| tijdstempel |Het tijdstip waarop de fout is opgetreden. |
| trace_id |Een unieke id voor de aanvraag die kan helpen bij diagnostische gegevens. |
| correlation_id |Een unieke id voor de aanvraag die kan helpen bij diagnostische gegevens over onderdelen. |

#### <a name="http-status-codes"></a>HTTP-statuscode
In de volgende tabel worden de HTTP-statuscodes weergegeven die het eindpunt van de tokenuitgifte retourneert. In sommige gevallen is de foutcode voldoende om het antwoord te beschrijven, maar als er fouten zijn, moet u het bijbehorende JSON-document ontleden en de foutcode onderzoeken.

| HTTP-code | Beschrijving |
| --- | --- |
| 400 |Standaard HTTP-code. Gebruikt in de meeste gevallen en is meestal te wijten aan een misvormde aanvraag. De aanvraag herstellen en opnieuw indienen. |
| 401 |Verificatie mislukt. De aanvraag mist bijvoorbeeld de parameter client_secret. |
| 403 |De autorisatie is mislukt. De gebruiker heeft bijvoorbeeld geen toestemming om toegang te krijgen tot de bron. |
| 500 |Er is een interne fout opgetreden bij de service. Probeer het verzoek opnieuw. |

#### <a name="error-codes-for-token-endpoint-errors"></a>Foutcodes voor tokeneindpuntfouten
| Foutcode | Beschrijving | Clientactie |
| --- | --- | --- |
| invalid_request |Protocolfout, zoals een ontbrekende vereiste parameter. |De aanvraag oplossen en opnieuw indienen |
| invalid_grant |De autorisatiecode is ongeldig of is verlopen. |Een nieuw verzoek `/authorize` naar het eindpunt proberen |
| unauthorized_client |De geverifieerde client is niet bevoegd om dit type autorisatieverlening te gebruiken. |Dit gebeurt meestal wanneer de clienttoepassing niet is geregistreerd in Azure AD of niet wordt toegevoegd aan de Azure AD-tenant van de gebruiker. De toepassing kan de gebruiker instructies geven voor het installeren van de toepassing en het toevoegen aan Azure AD. |
| invalid_client |Clientverificatie is mislukt. |De clientreferenties zijn niet geldig. Om het probleem op te lossen, werkt de toepassingsbeheerder de referenties bij. |
| unsupported_grant_type |De autorisatieserver ondersteunt het type autorisatieverlening niet. |Wijzig het subsidietype in de aanvraag. Dit type fout mag alleen optreden tijdens de ontwikkeling en worden gedetecteerd tijdens de eerste tests. |
| invalid_resource |De doelbron is ongeldig omdat deze niet bestaat, Azure AD deze niet kan vinden of niet correct is geconfigureerd. |Dit geeft aan dat de resource, als deze bestaat, niet is geconfigureerd in de tenant. De toepassing kan de gebruiker instructies geven voor het installeren van de toepassing en het toevoegen aan Azure AD. |
| interaction_required |De aanvraag vereist interactie van de gebruiker. Er is bijvoorbeeld een extra verificatiestap vereist. | In plaats van een niet-interactieve aanvraag, probeer opnieuw met een interactieve autorisatieaanvraag voor dezelfde bron. |
| temporarily_unavailable |De server is tijdelijk te druk om de aanvraag af te handelen. |Probeer het verzoek opnieuw. De clienttoepassing kan de gebruiker uitleggen dat de reactie is vertraagd als gevolg van een tijdelijke aandoening. |

## <a name="use-the-access-token-to-access-the-resource"></a>Het toegangstoken gebruiken om toegang te krijgen tot de bron
Nu u een `access_token`token hebt aangeschaft, u het token gebruiken in aanvragen `Authorization` voor web-API's, door het in de koptekst op te nemen. In [de RFC 6750-specificatie](https://www.rfc-editor.org/rfc/rfc6750.txt) wordt uitgelegd hoe u tokens aan toonder gebruiken in HTTP-verzoeken om toegang te krijgen tot beveiligde bronnen.

### <a name="sample-request"></a>Voorbeeldaanvraag
```
GET /data HTTP/1.1
Host: service.contoso.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ
```

### <a name="error-response"></a>Foutreactie
Beveiligde bronnen die http-statuscodes van RFC 6750 implementeren. Als het verzoek geen verificatiereferenties bevat of het token `WWW-Authenticate` mist, bevat het antwoord een koptekst. Wanneer een aanvraag mislukt, reageert de resourceserver met de HTTP-statuscode en een foutcode.

Het volgende is een voorbeeld van een mislukt antwoord wanneer het clientverzoek het token aan toonder niet bevat:

```
HTTP/1.1 401 Unauthorized
WWW-Authenticate: Bearer authorization_uri="https://login.microsoftonline.com/contoso.com/oauth2/authorize",  error="invalid_token",  error_description="The access token is missing.",
```

#### <a name="error-parameters"></a>Foutparameters
| Parameter | Beschrijving |
| --- | --- |
| authorization_uri |Het URI (fysiek eindpunt) van de autorisatieserver. Deze waarde wordt ook gebruikt als een opzoeksleutel om meer informatie over de server te krijgen vanaf een detectieeindpunt. <p><p> De client moet valideren dat de autorisatieserver wordt vertrouwd. Wanneer de bron wordt beschermd door Azure AD, volstaat `https://login.microsoftonline.com` het om te controleren of de URL begint met of een andere hostnaam die Azure AD ondersteunt. Een tenantspecifieke resource moet altijd een tenantspecifieke autorisatie URI retourneren. |
| error |Een foutcodewaarde gedefinieerd in sectie 5.2 van het [OAuth 2.0-autorisatiekader](https://tools.ietf.org/html/rfc6749). |
| error_description |Een meer gedetailleerde beschrijving van de fout. Dit bericht is niet bedoeld om gebruiksvriendelijk te zijn. |
| resource_id |Geeft als resultaat de unieke id van de resource. De clienttoepassing kan deze id gebruiken `resource` als de waarde van de parameter wanneer deze een token voor de bron aanvraagt. <p><p> Het is belangrijk voor de clienttoepassing om deze waarde te verifiëren, anders kan een kwaadwillende service mogelijk een **hoogte-van-privileges-aanval** veroorzaken <p><p> De aanbevolen strategie voor het voorkomen van `resource_id` een aanval is om te controleren of de basis van de web-API URL die wordt geopend. Bijvoorbeeld, als `https://service.contoso.com/data` wordt geopend, `resource_id` kan `https://service.contoso.com/`het worden . De clienttoepassing moet `resource_id` een die niet begint met de basis-URL weigeren, tenzij er een betrouwbare alternatieve manier is om de id te verifiëren. |

#### <a name="bearer-scheme-error-codes"></a>Foutcodes van het schema aan toonder
De RFC 6750-specificatie definieert de volgende fouten voor resources die het SCHEMA WWW-Authenticate-header en Toon gebruiken in het antwoord.

| HTTP-statuscode | Foutcode | Beschrijving | Clientactie |
| --- | --- | --- | --- |
| 400 |invalid_request |Het verzoek is niet goed gevormd. Het kan bijvoorbeeld een parameter missen of dezelfde parameter twee keer gebruiken. |Los de fout op en probeer het verzoek opnieuw. Dit type fout mag alleen optreden tijdens de ontwikkeling en worden gedetecteerd in de eerste tests. |
| 401 |invalid_token |Het toegangstoken ontbreekt, ongeldig of wordt ingetrokken. De waarde van de parameter error_description geeft extra details. |Vraag een nieuw token aan bij de autorisatieserver. Als het nieuwe token mislukt, is er een onverwachte fout opgetreden. Stuur een foutbericht naar de gebruiker en probeer het opnieuw na willekeurige vertragingen. |
| 403 |insufficient_scope |Het toegangstoken bevat niet de imitatiemachtigingen die nodig zijn om toegang te krijgen tot de bron. |Stuur een nieuwe autorisatieaanvraag naar het eindpunt van de autorisatie. Als het antwoord de bereikparameter bevat, gebruikt u de bereikwaarde in de aanvraag voor de resource. |
| 403 |insufficient_access |Het onderwerp van het token heeft niet de machtigingen die nodig zijn om toegang te krijgen tot de bron. |Vraag de gebruiker om een ander account te gebruiken of om machtigingen aan te vragen voor de opgegeven bron. |

## <a name="refreshing-the-access-tokens"></a>De toegangstokens vernieuwen

Toegangstokens zijn van korte duur en moeten worden vernieuwd nadat ze zijn verlopen om toegang te blijven krijgen tot bronnen. U `access_token` de door `POST` een ander `/token` verzoek indienen bij het `refresh_token` eindpunt `code`te vernieuwen, maar deze keer het verstrekken van de in plaats van de.  Vernieuwingstokens zijn geldig voor alle bronnen waarvoor uw client al toestemming heeft gekregen `resource=https://graph.microsoft.com` voor toegang - dus een `resource=https://contoso.com/api`vernieuwingstoken die is uitgegeven op een verzoek om een nieuw toegangstoken aan te vragen voor . 

Vernieuwingstokens hebben geen opgegeven levensduur. Meestal zijn de levensduur van vernieuwingstokens relatief lang. In sommige gevallen verlopen vernieuwingstokens echter, worden ze ingetrokken of ontbreken ze niet over voldoende bevoegdheden voor de gewenste actie. Uw toepassing moet fouten die door het eindpunt voor tokenuitgifte worden geretourneerd, correct verwachten en verwerken.

Wanneer u een antwoord ontvangt met een vernieuwingstokenfout, verwijdert u het huidige vernieuwingstoken en vraagt u een nieuwe autorisatiecode of toegangstoken aan. Als u een antwoord ontvangt met de `interaction_required` foutcodes `invalid_grant` of foutcodes, gooi u het vernieuwingstoken en vraagt u een nieuwe autorisatiecode aan wanneer u een vernieuwingstoken gebruikt in de subsidiestroom autorisatiecode.

Een voorbeeldaanvraag voor het **tenantspecifieke** eindpunt (u ook het **algemene** eindpunt gebruiken) om een nieuw toegangstoken te krijgen met behulp van een vernieuwingstoken ziet er als volgt uit:

```
// Line breaks for legibility only

POST /{tenant}/oauth2/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&refresh_token=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq...
&grant_type=refresh_token
&resource=https%3A%2F%2Fservice.contoso.com%2F
&client_secret=JqQX2PNo9bpM0uEihUPzyrh    // NOTE: Only required for web apps
```

### <a name="successful-response"></a>Succesvolle reactie
Een succesvolle tokenreactie ziet eruit als volgt:

```
{
  "token_type": "Bearer",
  "expires_in": "3600",
  "expires_on": "1460404526",
  "resource": "https://service.contoso.com/",
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ",
  "refresh_token": "AwABAAAAv YNqmf9SoAylD1PycGCB90xzZeEDg6oBzOIPfYsbDWNf621pKo2Q3GGTHYlmNfwoc-OlrxK69hkha2CF12azM_NYhgO668yfcUl4VBbiSHZyd1NVZG5QTIOcbObu3qnLutbpadZGAxqjIbMkQ2bQS09fTrjMBtDE3D6kSMIodpCecoANon9b0LATkpitimVCrl PM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4rTfgV29ghDOHRc2B-C_hHeJaJICqjZ3mY2b_YNqmf9SoAylD1PycGCB90xzZeEDg6oBzOIPfYsbDWNf621pKo2Q3GGTHYlmNfwoc-OlrxK69hkha2CF12azM_NYhgO668yfmVCrl-NyfN3oyG4ZCWu18M9-vEou4Sq-1oMDzExgAf61noxzkNiaTecM-Ve5cq6wHqYQjfV9DOz4lbceuYCAA"
}
```
| Parameter | Beschrijving |
| --- | --- |
| token_type |Het tokentype. De enige ondersteunde waarde is **drager**. |
| expires_in |De resterende levensduur van het token in seconden. Een typische waarde is 3600 (een uur). |
| expires_on |De datum en tijd waarop het token verloopt. De datum wordt weergegeven als het aantal seconden van 1970-01-01T0:0:0Z UTC tot de vervaldatum. |
| resource |Hiermee identificeert u de beveiligde bron die het toegangstoken kan gebruiken om toegang te krijgen. |
| scope |Imitatiemachtigingen die zijn verleend aan de native client-toepassing. De standaardmachtiging wordt **user_impersonation**. De eigenaar van de doelbron kan alternatieve waarden registreren in Azure AD. |
| access_token |Het nieuwe toegangstoken dat is aangevraagd. |
| refresh_token |Een nieuwe OAuth 2.0 refresh_token die kan worden gebruikt om nieuwe toegangstokens aan te vragen wanneer de ene in dit antwoord verloopt. |

### <a name="error-response"></a>Foutreactie
Een voorbeeldfoutreactie kan er als volgt uitzien:

```
{
  "error": "invalid_resource",
  "error_description": "AADSTS50001: The application named https://foo.microsoft.com/mail.read was not found in the tenant named 295e01fc-0c56-4ac3-ac57-5d0ed568f872. This can happen if the application has not been installed by the administrator of the tenant or consented to by any user in the tenant. You might have sent your authentication request to the wrong tenant.\r\nTrace ID: ef1f89f6-a14f-49de-9868-61bd4072f0a9\r\nCorrelation ID: b6908274-2c58-4e91-aea9-1f6b9c99347c\r\nTimestamp: 2016-04-11 18:59:01Z",
  "error_codes": [
    50001
  ],
  "timestamp": "2016-04-11 18:59:01Z",
  "trace_id": "ef1f89f6-a14f-49de-9868-61bd4072f0a9",
  "correlation_id": "b6908274-2c58-4e91-aea9-1f6b9c99347c"
}
```

| Parameter | Beschrijving |
| --- | --- |
| error |Een foutcodetekenreeks die kan worden gebruikt om typen fouten die zich voordoen te classificeren en kan worden gebruikt om op fouten te reageren. |
| error_description |Een specifiek foutbericht waarmee een ontwikkelaar de hoofdoorzaak van een verificatiefout kan identificeren. |
| error_codes |Een lijst met STS-specifieke foutcodes die kunnen helpen bij diagnostiek. |
| tijdstempel |Het tijdstip waarop de fout is opgetreden. |
| trace_id |Een unieke id voor de aanvraag die kan helpen bij diagnostische gegevens. |
| correlation_id |Een unieke id voor de aanvraag die kan helpen bij diagnostische gegevens over onderdelen. |

Zie [Foutcodes voor tokeneindpuntfouten voor](#error-codes-for-token-endpoint-errors)een beschrijving van de foutcodes en de aanbevolen clientactie.

## <a name="next-steps"></a>Volgende stappen
Zie [voorbeeldtoepassingen](sample-v1-code.md)voor meer informatie over het Azure AD v1.0-eindpunt en hoe u verificatie en autorisatie toevoegen aan uw webtoepassingen en web-API's.
