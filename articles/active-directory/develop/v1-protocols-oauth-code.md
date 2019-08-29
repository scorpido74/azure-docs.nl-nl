---
title: Meer informatie over de OAuth 2,0-autorisatie code stroom in azure AD
description: In dit artikel wordt beschreven hoe u HTTP-berichten gebruikt om toegang te verlenen tot webtoepassingen en Web-Api's in uw Tenant met behulp van Azure Active Directory en OAuth 2,0.
services: active-directory
documentationcenter: .net
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/05/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 611947c8c1d202cf4abf4222dfe0072aced58507
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/29/2019
ms.locfileid: "70135730"
---
# <a name="authorize-access-to-azure-active-directory-web-applications-using-the-oauth-20-code-grant-flow"></a>Azure Active Directory-webtoepassingen toegang verlenen met de stroom voor het verlenen van de OAuth 2.0-code

> [!NOTE]
>  Als u de server niet vertelt welke resource u wilt aanroepen, wordt het beleid voor voorwaardelijke toegang voor die bron niet geactiveerd door de server. Als u een MFA-trigger wilt gebruiken, moet u een resource in uw URL toevoegen. 
>

Azure Active Directory (Azure AD) gebruikt OAuth 2,0 om toegang te verlenen tot webtoepassingen en Web-Api's in uw Azure AD-Tenant. Deze hand leiding is taal onafhankelijk en beschrijft hoe u HTTP-berichten verzendt en ontvangt zonder gebruik te maken van de [open source-bibliotheken](active-directory-authentication-libraries.md).

De OAuth 2,0-autorisatie code stroom wordt beschreven in [sectie 4,1 van de oauth 2,0-specificatie](https://tools.ietf.org/html/rfc6749#section-4.1). Dit wordt gebruikt om verificatie en autorisatie uit te voeren in de meeste toepassings typen, waaronder web-apps en systeem eigen geïnstalleerde apps.

[!INCLUDE [active-directory-protocols-getting-started](../../../includes/active-directory-protocols-getting-started.md)]

## <a name="oauth-20-authorization-flow"></a>OAuth 2,0-autorisatie stroom

Op hoog niveau ziet de volledige autorisatie stroom voor een toepassing er ongeveer als volgt uit:

![OAuth-verificatie code stroom](./media/v1-protocols-oauth-code/active-directory-oauth-code-flow-native-app.png)

## <a name="request-an-authorization-code"></a>Een autorisatie code aanvragen

De autorisatie code stroom begint met de client die de gebruiker omleidt naar `/authorize` het eind punt. In deze aanvraag geeft de client aan welke machtigingen moeten worden verkregen van de gebruiker. U kunt het OAuth 2,0-autorisatie-eind punt voor uw Tenant verkrijgen door **App-registraties > eind punten** te selecteren in de Azure Portal.

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

| Parameter |  | Description |
| --- | --- | --- |
| tenant |vereist |De `{tenant}` waarde in het pad van de aanvraag kan worden gebruikt om te bepalen wie zich kan aanmelden bij de toepassing. De toegestane waarden zijn Tenant-id's, bijvoorbeeld `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` `common` of `contoso.onmicrosoft.com` voor Tenant-onafhankelijke tokens |
| client_id |vereist |De toepassings-ID die is toegewezen aan uw app wanneer u deze hebt geregistreerd bij Azure AD. U kunt dit vinden in de Azure-Portal. Klik op **Azure Active Directory** in de zijbalk Services, klik op **app-registraties**en kies de toepassing. |
| response_type |vereist |Moet de `code` autorisatie code stroom bevatten. |
| redirect_uri |aanbevelingen |De redirect_uri van uw app, waar verificatie reacties kunnen worden verzonden en ontvangen door uw app. Het moet exact overeenkomen met een van de redirect_uris die u in de portal hebt geregistreerd, behalve het moet een URL-code ring zijn. Voor systeem eigen & mobiele apps moet u de standaard waarde van `urn:ietf:wg:oauth:2.0:oob`gebruiken. |
| response_mode |optioneel |Hiermee geeft u de methode op die moet worden gebruikt om het resulterende token terug naar uw app te verzenden. `query`Kan, `fragment`of. `form_post` `query`levert de code als een query reeks parameter op de omleidings-URI. Als u een id-token met behulp van de impliciete stroom aanvraagt, kunt u niet gebruiken `query` zoals opgegeven in de OpenID Connect- [specificatie](https://openid.net/specs/oauth-v2-multiple-response-types-1_0.html#Combinations). Als u alleen de code wilt aanvragen, kunt u, `query` `fragment`of `form_post`gebruiken. `form_post`Hiermee wordt een bericht met de code uitgevoerd naar de omleidings-URI. De standaard waarde `query` is voor een code stroom.  |
| toestand |aanbevelingen |Een waarde die in de aanvraag is opgenomen en die ook wordt geretourneerd in de token reactie. Een wille keurig gegenereerde unieke waarde wordt doorgaans gebruikt om [vervalsing van aanvragen op meerdere sites](https://tools.ietf.org/html/rfc6749#section-10.12)te voor komen. De status wordt ook gebruikt voor het coderen van informatie over de status van de gebruiker in de app voordat de verificatie aanvraag is uitgevoerd, zoals de pagina of weer gave waarin ze zich bevonden. |
| resource | aanbevelingen |De App-ID-URI van de doel-Web-API (beveiligde bron). Als u de URI van de App-ID wilt zoeken, klikt u in azure Portal op **Azure Active Directory**, klikt u op **toepassings registraties**, opent u de pagina **instellingen** van de toepassing en klikt u vervolgens op **Eigenschappen**. Het kan ook een externe bron zijn, `https://graph.microsoft.com`zoals. Dit is vereist in een van de autorisatie-of Token aanvragen. Om ervoor te zorgen dat er minder verificatie prompts worden uitgevoerd in de autorisatie aanvraag, om ervoor te zorgen dat de gebruiker om toestemming wordt gevraagd. |
| bereik | **genegeerd** | Voor v1 Azure AD-apps moeten scopes statisch worden geconfigureerd in azure portal onder de **instellingen**voor toepassingen, **vereiste machtigingen**. |
| verschijnt |optioneel |Geef het type gebruikers interactie op dat is vereist.<p> Geldige waarden zijn: <p> *aanmelding*: De gebruiker moet worden gevraagd om opnieuw te worden geverifieerd. <p> *select_account*: De gebruiker wordt gevraagd een account te selecteren en de eenmalige aanmelding te onderbreken. De gebruiker kan een bestaand aangemeld account selecteren, hun referenties voor een onthouden account invoeren of ervoor kiezen om een ander account samen te gebruiken. <p> *toestemming*: Toestemming van de gebruiker is verleend, maar moet worden bijgewerkt. De gebruiker moet om toestemming wordt gevraagd. <p> *admin_consent*: Een beheerder moet worden gevraagd om toestemming namens alle gebruikers in hun organisatie |
| login_hint |optioneel |Kan worden gebruikt om het veld gebruikers naam/e-mail adres vooraf in te vullen op de aanmeldings pagina voor de gebruiker, als u de gebruikers naam van tevoren kent. Vaak gebruiken apps deze para meter tijdens de herauthenticatie, waarbij de gebruikers naam al is geëxtraheerd van een eerdere aanmelding `preferred_username` met de claim. |
| domain_hint |optioneel |Biedt een hint over de Tenant of het domein waarmee de gebruiker zich aanmeldt. De waarde van domain_hint is een geregistreerd domein voor de Tenant. Als de Tenant federatief is voor een on-premises Directory, wordt door AAD omgeleid naar de opgegeven Tenant-Federatie server. |
| code_challenge_method | aanbevelingen    | De methode die wordt gebruikt voor `code_verifier` het coderen `code_challenge` van de voor de para meter. Dit kan een van `plain` of `S256`zijn. Als deze `code_challenge` is `code_challenge` uitgesloten, wordt ervan uitgegaan dat de tekst zonder opmaak wordt gebruikt. Azure Aad v 1.0 ondersteunt zowel `plain` als `S256`. Zie [PKCE RFC](https://tools.ietf.org/html/rfc7636)(Engelstalig) voor meer informatie. |
| code_challenge        | aanbevelingen    | Wordt gebruikt voor het beveiligen van autorisatie code subsidies via de bewijs code voor code Exchange (PKCE) van een systeem eigen of open bare client. Vereist als `code_challenge_method` is opgenomen. Zie [PKCE RFC](https://tools.ietf.org/html/rfc7636)(Engelstalig) voor meer informatie. |

> [!NOTE]
> Als de gebruiker deel uitmaakt van een organisatie, kan een beheerder van de organisatie toestemming geven of weigeren namens de gebruiker of de gebruiker toestemming geven. De gebruiker krijgt de mogelijkheid om alleen toestemming te geven als de beheerder dit toestaat.
>
>

Op dit moment wordt de gebruiker gevraagd om hun referenties in te voeren en toestemming te geven voor de machtigingen die worden aangevraagd door de app in azure Portal. Zodra de gebruiker zich heeft geverifieerd en toestemming verleent, stuurt Azure AD een antwoord naar uw app op `redirect_uri` het adres in uw aanvraag met de code.

### <a name="successful-response"></a>Geslaagde reactie
Een geslaagde reactie kan er als volgt uitzien:

```
GET  HTTP/1.1 302 Found
Location: http://localhost:12345/?code= AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrqqf_ZT_p5uEAEJJ_nZ3UmphWygRNy2C3jJ239gV_DBnZ2syeg95Ki-374WHUP-i3yIhv5i-7KU2CEoPXwURQp6IVYMw-DjAOzn7C3JCu5wpngXmbZKtJdWmiBzHpcO2aICJPu1KvJrDLDP20chJBXzVYJtkfjviLNNW7l7Y3ydcHDsBRKZc3GuMQanmcghXPyoDg41g8XbwPudVh7uCmUponBQpIhbuffFP_tbV8SNzsPoFz9CLpBCZagJVXeqWoYMPe2dSsPiLO9Alf_YIe5zpi-zY4C3aLw5g9at35eZTfNd0gBRpR5ojkMIcZZ6IgAA&session_state=7B29111D-C220-4263-99AB-6F6E135D75EF&state=D79E5777-702E-4260-9A62-37F75FF22CCE
```

| Parameter | Description |
| --- | --- |
| admin_consent |De waarde is True als een beheerder een prompt voor een aanvraag voor toestemming heeft verzonden. |
| code |De autorisatie code die door de toepassing is aangevraagd. De toepassing kan de autorisatie code gebruiken om een toegangs token aan te vragen voor de doel bron. |
| session_state |Een unieke waarde die de huidige gebruikers sessie identificeert. Deze waarde is een GUID, maar moet worden behandeld als een ondoorzichtige waarde die zonder onderzoek wordt door gegeven. |
| toestand |Als een para meter State is opgenomen in de aanvraag, moet dezelfde waarde in het antwoord worden weer gegeven. Het is een goede gewoonte voor de toepassing om te controleren of de status waarden in de aanvraag en het antwoord identiek zijn voordat u het antwoord gebruikt. Dit helpt bij het detecteren van [CSRF-aanvallen (cross-site request vervalsing)](https://tools.ietf.org/html/rfc6749#section-10.12) op de client. |

### <a name="error-response"></a>Fout bericht
Fout reacties kunnen ook worden verzonden naar de `redirect_uri` zodat de toepassing deze op de juiste wijze kan afhandelen.

```
GET http://localhost:12345/?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parameter | Description |
| --- | --- |
| fout |Een fout code waarde die is gedefinieerd in sectie 5,2 van het [OAuth 2,0-autorisatie raamwerk](https://tools.ietf.org/html/rfc6749). In de volgende tabel worden de fout codes beschreven die door Azure AD worden geretourneerd. |
| error_description |Een gedetailleerde beschrijving van de fout. Dit bericht is niet bedoeld voor eind gebruikers vriendelijk. |
| toestand |De status waarde is een wille keurig gegenereerde niet-hergebruikte waarde die in de aanvraag wordt verzonden en geretourneerd in het antwoord om CSRF-aanvallen (cross-site request vervalsing) te voor komen. |

#### <a name="error-codes-for-authorization-endpoint-errors"></a>Fout codes voor verificatie eindpunt fouten
In de volgende tabel worden de verschillende fout codes beschreven die kunnen worden geretourneerd `error` in de para meter van het fout bericht.

| Foutcode | Description | Client actie |
| --- | --- | --- |
| invalid_request |Protocol fout, zoals een ontbrekende vereiste para meter. |Corrigeer en verzend de aanvraag opnieuw. Dit is een ontwikkelings fout en wordt doorgaans onderschept tijdens de eerste test. |
| unauthorized_client |De client toepassing mag geen autorisatie code aanvragen. |Dit gebeurt meestal wanneer de client toepassing niet is geregistreerd bij Azure AD of niet is toegevoegd aan de Azure AD-Tenant van de gebruiker. De toepassing kan de gebruiker vragen met instructies voor het installeren van de toepassing en het toevoegen aan Azure AD. |
| access_denied |De resource-eigenaar heeft toestemming geweigerd |De client toepassing kan de gebruiker hiervan op de hoogte stellen dat deze niet kan door gaan tenzij de gebruiker deze heeft toegestuurd. |
| unsupported_response_type |De autorisatie server biedt geen ondersteuning voor het antwoord type in de aanvraag. |Corrigeer en verzend de aanvraag opnieuw. Dit is een ontwikkelings fout en wordt doorgaans onderschept tijdens de eerste test. |
| server_error |Er is een onverwachte fout opgetreden op de server. |Voer de aanvraag opnieuw uit. Deze fouten kunnen worden veroorzaakt door tijdelijke voor waarden. De client toepassing kan bijvoorbeeld verklaren dat het antwoord van de gebruiker is vertraagd vanwege een tijdelijke fout. |
| temporarily_unavailable |De server is tijdelijk niet actief om de aanvraag af te handelen. |Voer de aanvraag opnieuw uit. De client toepassing kan bijvoorbeeld verklaren dat het antwoord van de gebruiker is vertraagd vanwege een tijdelijke voor waarde. |
| invalid_resource |De doel resource is ongeldig omdat deze niet bestaat, Azure AD niet kan worden gevonden of niet juist is geconfigureerd. |Dit geeft aan dat de resource, indien aanwezig, niet is geconfigureerd in de Tenant. De toepassing kan de gebruiker vragen met instructies voor het installeren van de toepassing en het toevoegen aan Azure AD. |

## <a name="use-the-authorization-code-to-request-an-access-token"></a>De autorisatie code gebruiken om een toegangs token aan te vragen
Nu u een autorisatie code hebt aangeschaft en toestemming hebt gegeven door de gebruiker, kunt u de code voor een toegangs token inwisselen voor de gewenste resource door een post-aanvraag te verzenden naar het `/token` eind punt:

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

| Parameter |  | Description |
| --- | --- | --- |
| tenant |vereist |De `{tenant}` waarde in het pad van de aanvraag kan worden gebruikt om te bepalen wie zich kan aanmelden bij de toepassing. De toegestane waarden zijn Tenant-id's, bijvoorbeeld `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` `common` of `contoso.onmicrosoft.com` voor Tenant-onafhankelijke tokens |
| client_id |vereist |De toepassings-id die is toegewezen aan uw app wanneer u deze hebt geregistreerd bij Azure AD. U kunt dit vinden in de Azure Portal. De toepassings-id wordt weer gegeven in de instellingen van de app-registratie. |
| grant_type |vereist |Moet voor `authorization_code` de autorisatie code stroom zijn. |
| code |vereist |De `authorization_code` die u hebt verkregen in de vorige sectie |
| redirect_uri |vereist | Een `redirect_uri`geregistreerd op de client toepassing. |
| client_secret |vereist voor web-apps, niet toegestaan voor open bare clients |Het toepassings geheim dat u hebt gemaakt in azure portal voor uw app onder **sleutels**. Het kan niet worden gebruikt in een systeem eigen app (open bare client) omdat client_secrets niet betrouwbaar kan worden opgeslagen op apparaten. Het is vereist voor web-apps en Web-api's (alle vertrouwelijke clients), die de mogelijkheid hebben om `client_secret` de beveiliging op te slaan aan de server zijde. De client_secret moet een URL-code ring hebben voordat deze wordt verzonden. |
| resource | aanbevelingen |De App-ID-URI van de doel-Web-API (beveiligde bron). Als u de URI van de App-ID wilt zoeken, klikt u in azure Portal op **Azure Active Directory**, klikt u op **toepassings registraties**, opent u de pagina **instellingen** van de toepassing en klikt u vervolgens op **Eigenschappen**. Het kan ook een externe bron zijn, `https://graph.microsoft.com`zoals. Dit is vereist in een van de autorisatie-of Token aanvragen. Om ervoor te zorgen dat er minder verificatie prompts worden uitgevoerd in de autorisatie aanvraag, om ervoor te zorgen dat de gebruiker om toestemming wordt gevraagd. In zowel de autorisatie aanvraag als de token aanvraag moeten de para meters van de resource overeenkomen. | 
| code_verifier | optioneel | Hetzelfde code_verifier dat is gebruikt om de authorization_code op te halen. Vereist als PKCE is gebruikt in de aanvraag voor autorisatie code toekenning. Zie voor meer informatie de [PKCE RFC](https://tools.ietf.org/html/rfc7636)   |

Als u de URI van de App-ID wilt zoeken, klikt u in azure Portal op **Azure Active Directory**, klikt u op **toepassings registraties**, opent u de pagina **instellingen** van de toepassing en klikt u vervolgens op **Eigenschappen**.

### <a name="successful-response"></a>Geslaagde reactie
Azure AD retourneert een [toegangs token](access-tokens.md) bij een geslaagde reactie. Om netwerk aanroepen van de client toepassing en de bijbehorende latentie te minimaliseren, moet de client toepassing toegangs tokens in de cache opslaan voor de levens duur van het token dat is opgegeven in het OAuth 2,0-antwoord. Als u de levens duur van het token wilt `expires_in` bepalen `expires_on` , gebruikt u de para meter-of-waarden.

Als een web-API-resource `invalid_token` een fout code retourneert, kan dit erop wijzen dat de bron heeft vastgesteld dat het token is verlopen. Als de klok tijden van de client en de resource verschillend zijn (ook wel ' tijd scheef trekken ' genoemd), kan de bron overwegen dat het token is verlopen voordat het token uit de client cache wordt gewist. Als dit het geval is, wist u het token uit de cache, zelfs als dit nog steeds binnen de berekende levens duur ligt.

Een geslaagde reactie kan er als volgt uitzien:

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

| Parameter | Description |
| --- | --- |
| access_token |Het aangevraagde [toegangs token](access-tokens.md) als een ondertekende JSON Web token (JWT). De app kan dit token gebruiken om te verifiëren bij de beveiligde bron, zoals een web-API. |
| token_type |Geeft de waarde van het token type aan. Het enige type dat door Azure AD wordt ondersteund, is Bearer. Zie [voor meer informatie over Bearer-tokens OAuth 2.0-autorisatie raamwerk: Token gebruik van Bearer (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt) |
| expires_in |Hoe lang het toegangs token geldig is (in seconden). |
| expires_on |Het tijdstip waarop het toegangs token verloopt. De datum wordt weer gegeven als het aantal seconden van 1970-01-01T0:0: 0Z UTC tot de verloop tijd. Deze waarde wordt gebruikt om de levens duur van tokens in de cache te bepalen. |
| resource |De App-ID-URI van de Web-API (beveiligde bron). |
| bereik |Imitatie machtigingen verleend aan de client toepassing. De standaard machtiging is `user_impersonation`. De eigenaar van de beveiligde resource kan aanvullende waarden registreren in azure AD. |
| refresh_token |Een OAuth 2,0-vernieuwings token. De app kan dit token gebruiken om extra toegangs tokens te verkrijgen nadat het huidige toegangs token is verlopen. Vernieuwings tokens worden lang bewaard en kunnen worden gebruikt om de toegang tot resources te bewaren gedurende lange Peri Oden. |
| id_token |Een niet-ondertekende JSON Web Token (JWT) die een [id-token](id-tokens.md)vertegenwoordigt. De app kan de segmenten van deze token base64Url decoderen om informatie aan te vragen over de gebruiker die zich heeft aangemeld. De app kan de waarden in de cache opslaan en weer geven, maar deze moet niet afhankelijk zijn van autorisatie of beveiligings grenzen. |

Zie de [specificatie JWT IETF draft](https://go.microsoft.com/fwlink/?LinkId=392344)voor meer informatie over JSON-webtokens.   Zie `id_tokens` [v 1.0 OpenID Connect Connect flow](v1-protocols-openid-connect-code.md)voor meer informatie over.

### <a name="error-response"></a>Fout bericht
De eindpunt fouten van de token uitgifte zijn HTTP-fout codes, omdat de client het eind punt voor token uitgifte direct aanroept. Naast de HTTP-status code retourneert het Azure AD token uitgifte-eind punt ook een JSON-document met objecten die de fout beschrijven.

Een voor beeld van een fout bericht kan er als volgt uitzien:

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
| Parameter | Description |
| --- | --- |
| fout |Een teken reeks voor fout codes die kan worden gebruikt voor het classificeren van typen fouten die optreden en kunnen worden gebruikt om te reageren op fouten. |
| error_description |Een specifiek fout bericht dat een ontwikkelaar kan helpen bij het identificeren van de hoofd oorzaak van een verificatie fout. |
| error_codes |Een lijst met STS-specifieke fout codes die u kunnen helpen bij het diagnosticeren. |
| timestamp |Het tijdstip waarop de fout is opgetreden. |
| trace_id |Een unieke id voor de aanvraag die kan helpen bij diagnostische gegevens. |
| correlation_id |Een unieke id voor de aanvraag die kan helpen bij het diagnosticeren van verschillende onderdelen. |

#### <a name="http-status-codes"></a>HTTP-statuscodes
De volgende tabel bevat de HTTP-status codes die het eind punt voor token uitgifte retourneert. In sommige gevallen is de fout code voldoende om het antwoord te beschrijven, maar als er fouten zijn, moet u het bijbehorende JSON-document parseren en de fout code onderzoeken.

| HTTP-code | Description |
| --- | --- |
| 400 |Standaard-HTTP-code. Wordt in de meeste gevallen gebruikt en wordt meestal veroorzaakt door een ongeldige aanvraag. Corrigeer en verzend de aanvraag opnieuw. |
| 401 |Verificatie is mislukt. De aanvraag ontbreekt bijvoorbeeld de para meter client_secret. |
| 403 |Autorisatie is mislukt. De gebruiker heeft bijvoorbeeld geen machtiging voor toegang tot de resource. |
| 500 |Er is een interne fout opgetreden bij de service. Voer de aanvraag opnieuw uit. |

#### <a name="error-codes-for-token-endpoint-errors"></a>Fout codes voor token eindpunt fouten
| Foutcode | Description | Client actie |
| --- | --- | --- |
| invalid_request |Protocol fout, zoals een ontbrekende vereiste para meter. |De aanvraag herstellen en opnieuw verzenden |
| invalid_grant |De autorisatie code is ongeldig of is verlopen. |Een nieuwe aanvraag naar het `/authorize` eind punt proberen |
| unauthorized_client |De geverifieerde client is niet gemachtigd om dit type autorisatie machtiging te gebruiken. |Dit gebeurt meestal wanneer de client toepassing niet is geregistreerd bij Azure AD of niet is toegevoegd aan de Azure AD-Tenant van de gebruiker. De toepassing kan de gebruiker vragen met instructies voor het installeren van de toepassing en het toevoegen aan Azure AD. |
| invalid_client |Client verificatie is mislukt. |De client referenties zijn ongeldig. De toepassings beheerder werkt de referenties bij om deze te herstellen. |
| unsupported_grant_type |De autorisatie server biedt geen ondersteuning voor het type autorisatie toekenning. |Wijzig het toekennings type in de aanvraag. Dit type fout moet alleen tijdens de ontwikkeling optreden en tijdens de eerste test worden gedetecteerd. |
| invalid_resource |De doel resource is ongeldig omdat deze niet bestaat, Azure AD niet kan worden gevonden of niet juist is geconfigureerd. |Dit geeft aan dat de resource, indien aanwezig, niet is geconfigureerd in de Tenant. De toepassing kan de gebruiker vragen met instructies voor het installeren van de toepassing en het toevoegen aan Azure AD. |
| interaction_required |De aanvraag vereist een gebruikers interactie. Zo is een extra verificatie stap vereist. | Probeer het opnieuw met een interactieve autorisatie aanvraag voor dezelfde bron in plaats van een niet-interactieve aanvraag. |
| temporarily_unavailable |De server is tijdelijk niet actief om de aanvraag af te handelen. |Voer de aanvraag opnieuw uit. De client toepassing kan bijvoorbeeld verklaren dat het antwoord van de gebruiker is vertraagd vanwege een tijdelijke voor waarde. |

## <a name="use-the-access-token-to-access-the-resource"></a>Het toegangs token gebruiken om toegang te krijgen tot de resource
Nu u een `access_token`hebt aangeschaft, kunt u het token gebruiken in aanvragen voor web-api's door dit op te nemen in de `Authorization` kop. In de [RFC 6750](https://www.rfc-editor.org/rfc/rfc6750.txt) -specificatie wordt uitgelegd hoe u Bearer-tokens kunt gebruiken in HTTP-aanvragen om toegang te krijgen tot beveiligde bronnen.

### <a name="sample-request"></a>Voorbeeld van een aanvraag
```
GET /data HTTP/1.1
Host: service.contoso.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ
```

### <a name="error-response"></a>Fout bericht
Beveiligde bronnen voor het implementeren van RFC 6750-probleem HTTP-status codes. Als de aanvraag geen verificatie referenties bevat of als het token ontbreekt, is het antwoord een `WWW-Authenticate` header. Wanneer een aanvraag mislukt, reageert de resource server met de HTTP-status code en een fout code.

Hier volgt een voor beeld van een niet-geslaagde reactie wanneer de client aanvraag geen Bearer-token bevat:

```
HTTP/1.1 401 Unauthorized
WWW-Authenticate: Bearer authorization_uri="https://login.microsoftonline.com/contoso.com/oauth2/authorize",  error="invalid_token",  error_description="The access token is missing.",
```

#### <a name="error-parameters"></a>Fout parameters
| Parameter | Description |
| --- | --- |
| authorization_uri |De URI (fysiek eind punt) van de autorisatie server. Deze waarde wordt ook gebruikt als opzoek sleutel voor het verkrijgen van meer informatie over de server van een detectie-eind punt. <p><p> De client moet valideren of de autorisatie server vertrouwd is. Wanneer de resource wordt beveiligd door Azure AD, is het voldoende om te controleren of de URL begint https://login.microsoftonline.com met of een andere hostnaam die door Azure AD wordt ondersteund. Een Tenant-specifieke resource moet altijd een Tenant-specifieke autorisatie-URI retour neren. |
| fout |Een fout code waarde die is gedefinieerd in sectie 5,2 van het [OAuth 2,0-autorisatie raamwerk](https://tools.ietf.org/html/rfc6749). |
| error_description |Een gedetailleerde beschrijving van de fout. Dit bericht is niet bedoeld voor eind gebruikers vriendelijk. |
| resource_id |Retourneert de unieke id van de resource. De client toepassing kan deze id als de waarde van de `resource` para meter gebruiken bij het aanvragen van een token voor de resource. <p><p> Het is belang rijk dat de client toepassing deze waarde verifieert. anders kan een schadelijke service een aanval **van bevoegdheden** tot gevolg hebben. <p><p> De aanbevolen strategie voor het voor komen van een aanval is om `resource_id` te controleren of het overeenkomt met de basis van de Web API-URL die wordt geopend. Als https://service.contoso.com/data bijvoorbeeld wordt geopend, kan de `resource_id` htttps://service.contoso.com/zijn. De client toepassing moet een `resource_id` die niet begint met de basis-URL afwijzen, tenzij er een betrouw bare alternatieve manier is om de id te verifiëren. |

#### <a name="bearer-scheme-error-codes"></a>Fout codes van Bearer-schema's
De RFC 6750-specificatie definieert de volgende fouten voor resources die gebruikmaken van de WWW-Authenticate-header en Bearer-schema in het antwoord.

| HTTP-status code | Foutcode | Description | Client actie |
| --- | --- | --- | --- |
| 400 |invalid_request |De aanvraag is niet juist opgemaakt. Er kan bijvoorbeeld een para meter ontbreken of dezelfde para meter twee keer worden gebruikt. |Los de fout op en voer de aanvraag opnieuw uit. Dit type fout moet alleen tijdens de ontwikkeling optreden en tijdens de eerste test worden gedetecteerd. |
| 401 |invalid_token |Het toegangs token ontbreekt, is ongeldig of is ingetrokken. De waarde van de para meter error_description biedt meer details. |Een nieuw token aanvragen bij de autorisatie server. Als het nieuwe token mislukt, is er een onverwachte fout opgetreden. Stuur een fout bericht naar de gebruiker en probeer het opnieuw na wille keurige vertragingen. |
| 403 |insufficient_scope |Het toegangs token bevat niet de imitatie machtigingen die vereist zijn voor toegang tot de bron. |Verzend een nieuwe autorisatie aanvraag naar het autorisatie-eind punt. Als het antwoord de bereik parameter bevat, gebruikt u de bereik waarde in de aanvraag voor de resource. |
| 403 |insufficient_access |Het onderwerp van het token heeft niet de machtigingen die nodig zijn voor toegang tot de resource. |De gebruiker vragen om een ander account te gebruiken of om machtigingen aan te vragen voor de opgegeven resource. |

## <a name="refreshing-the-access-tokens"></a>De toegangs tokens vernieuwen

Toegangs tokens zijn korte duur en moeten worden vernieuwd nadat ze hebben geduurd om toegang te blijven houden tot resources. U `access_token` kunt de vernieuwen door een andere `POST` aanvraag in te `/token` dienen bij het eind punt, maar `refresh_token` deze keer is `code`de tijd die u opgeeft in plaats van de.  De vernieuwings tokens zijn geldig voor alle resources die uw client al toestemming heeft gegeven om toegang te krijgen `resource=https://graph.microsoft.com` `resource=https://contoso.com/api`. Daarom kan een vernieuwings token dat is uitgegeven op een aanvraag voor, worden gebruikt om een nieuw toegangs token aan te vragen. 

Voor vernieuwings tokens is geen levens duur opgegeven. De levens duur van vernieuwings tokens is doorgaans relatief lang. In sommige gevallen verloopt het vernieuwen van tokens, worden deze ingetrokken of beschikt over onvoldoende machtigingen voor de gewenste actie. Uw toepassing moet verwachten en fouten afhandelen die door het eind punt voor token uitgifte zijn geretourneerd.

[!NOTE] De levens duur van toegangs tokens kunt u hier vinden: https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-configurable-token-lifetimes#configurable-token-lifetime-properties De standaard waarde voor toegangs tokens is 1 uur en de standaard waarde voor vernieuwings tokens is 90 dagen. Deze levens duur kan worden gewijzigd door de levens duur van het token dienovereenkomstig te configureren. 

Wanneer u een reactie met een vernieuwings token fout ontvangt, moet u het huidige vernieuwings token verwijderen en een nieuwe autorisatie code of toegangs token aanvragen. Bij het gebruik van een vernieuwings token in de machtigings stroom van de autorisatie code wordt `interaction_required` `invalid_grant` met name het vernieuwings token verwijderd en een nieuwe autorisatie code aangevraagd.

Een voorbeeld aanvraag aan het **Tenant-specifieke** eind punt (u kunt ook het **gemeen schappelijke** eind punt gebruiken) om een nieuw toegangs token op te halen met behulp van een vernieuwings token ziet er als volgt uit:

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

### <a name="successful-response"></a>Geslaagde reactie
Een geslaagd token antwoord ziet er als volgt uit:

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
| Parameter | Description |
| --- | --- |
| token_type |Het token type. De enige ondersteunde waarde is **Bearer**. |
| expires_in |De resterende levens duur van het token in seconden. Een typische waarde is 3600 (één uur). |
| expires_on |De datum en tijd waarop het token verloopt. De datum wordt weer gegeven als het aantal seconden van 1970-01-01T0:0: 0Z UTC tot de verloop tijd. |
| resource |Hiermee wordt de beveiligde bron geïdentificeerd waarmee het toegangs token kan worden gebruikt om toegang te krijgen. |
| bereik |Imitatie machtigingen die zijn verleend aan de systeem eigen client toepassing. De standaard machtiging is **user_impersonation**. De eigenaar van de doel resource kan alternatieve waarden registreren in azure AD. |
| access_token |Het nieuwe toegangs token dat is aangevraagd. |
| refresh_token |Een nieuw OAuth 2,0 refresh_token dat kan worden gebruikt om nieuwe toegangs tokens aan te vragen wanneer het in dit antwoord verloopt. |

### <a name="error-response"></a>Fout bericht
Een voor beeld van een fout bericht kan er als volgt uitzien:

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

| Parameter | Description |
| --- | --- |
| fout |Een teken reeks voor fout codes die kan worden gebruikt voor het classificeren van typen fouten die optreden en kunnen worden gebruikt om te reageren op fouten. |
| error_description |Een specifiek fout bericht dat een ontwikkelaar kan helpen bij het identificeren van de hoofd oorzaak van een verificatie fout. |
| error_codes |Een lijst met STS-specifieke fout codes die u kunnen helpen bij het diagnosticeren. |
| timestamp |Het tijdstip waarop de fout is opgetreden. |
| trace_id |Een unieke id voor de aanvraag die kan helpen bij diagnostische gegevens. |
| correlation_id |Een unieke id voor de aanvraag die kan helpen bij het diagnosticeren van verschillende onderdelen. |

Zie voor een beschrijving van de fout codes en de aanbevolen client actie [fout codes voor token eindpunt fouten](#error-codes-for-token-endpoint-errors).
