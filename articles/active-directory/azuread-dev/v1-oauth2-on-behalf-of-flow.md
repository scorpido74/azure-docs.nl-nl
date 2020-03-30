---
title: Service-to-service-verificatie met OAuth2.0 namens de stroom | Microsoft Documenten
description: In dit artikel wordt beschreven hoe u HTTP-berichten gebruiken om service-to-service-verificatie te implementeren met de oauth2.0-stroom voor rekening van de service.
services: active-directory
documentationcenter: .net
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: ryanwi
ms.reviewer: hirsin, nacanuma
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: a301029f30a77f4e62ad3529aac488a81c12566e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154522"
---
# <a name="service-to-service-calls-that-use-delegated-user-identity-in-the-on-behalf-of-flow"></a>Service-to-service-aan-huisoproepen die de gedelegeerde gebruikersidentiteit gebruiken in de stroom namens de gebruiker

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Met de OAuth 2.0-obo-stroom (OBO) kan een toepassing die een service of web-API aanroept, gebruikersverificatie doorgeven aan een andere service of web-API. De OBO-stroom verspreidt de gedelegeerde gebruikersidentiteit en machtigingen via de aanvraagketen. Als u de service op het middenniveau om geverifieerde aanvragen voor de downstreamservice in te voeren, moet deze namens de gebruiker een toegangstoken beveiligen vanuit Azure Active Directory (Azure AD).

> [!IMPORTANT]
> Vanaf mei 2018 `id_token` kan een niet meer worden gebruikt voor de on-Behalf-Of flow.  Apps met één pagina (SB's) moeten een toegangstoken doorgeven aan een vertrouwelijke client op het middenniveau om OBO-stromen uit te voeren. Zie [beperkingen](#client-limitations)voor meer informatie over de clients die namens oproepen kunnen uitvoeren.

## <a name="on-behalf-of-flow-diagram"></a>Diagram voor de stroom aan de andere kant

De OBO-stroom wordt gestart nadat de gebruiker is geverifieerd op een toepassing die de [oauth 2.0-autorisatiecode-subsidiestroom](v1-protocols-oauth-code.md)gebruikt. Op dat moment stuurt de toepassing een toegangstoken (token A) naar de middle-tier web API (API A) met de claims en toestemming van de gebruiker om toegang te krijgen tot API A. Vervolgens doet API A een geverifieerd verzoek aan de downstream web API (API B).

Deze stappen vormen de on-behalf-of-flow: ![toont de stappen in de OAuth2.0 On-Behalf-Of flow](./media/v1-oauth2-on-behalf-of-flow/active-directory-protocols-oauth-on-behalf-of-flow.png)

1. De clienttoepassing doet een verzoek om API A met het token A.
1. API A verifieert naar het eindpunt voor de uitgifte van Azure AD-tokenen en vraagt een token aan om toegang te krijgen tot API B.
1. Het eindpunt voor de uitgifte van Azure AD-tokenvalideert de referenties van API A met token A en geeft het toegangstoken voor API B (token B) uit.
1. De aanvraag voor API B bevat token B in de autorisatiekoptekst.
1. API B retourneert gegevens van de beveiligde bron.

>[!NOTE]
>De doelgroepclaim in een toegangstoken dat wordt gebruikt om een token voor een downstreamservice aan te vragen, moet de id zijn van de service die de OBO-aanvraag indient. Het token moet ook worden ondertekend met de globale ondertekeningssleutel van Azure Active Directory (de standaardinstelling voor toepassingen die zijn geregistreerd via **app-registraties** in de portal).

## <a name="register-the-application-and-service-in-azure-ad"></a>De toepassing en service registreren in Azure AD

Registreer zowel de middle-tier service als de clienttoepassing in Azure AD.

### <a name="register-the-middle-tier-service"></a>De service op het middenniveau registreren

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Selecteer op de bovenste balk uw account en zoek onder de **lijst Map** een Active Directory-tenant voor uw toepassing te selecteren.
1. Selecteer **Meer services** in het linkerdeelvenster en kies Azure Active **Directory**.
1. Selecteer **App-registraties** en vervolgens **Nieuwe registratie**.
1. Voer een vriendelijke naam in voor de toepassing en selecteer het toepassingstype.
1. Selecteer onder **Ondersteunde accounttypen** de optie **Accounts in een organisatieadreslijst en persoonlijke Microsoft-account**.
1. Stel de omleidings-URI in naar de basis-URL.
1. Selecteer **Registreren** om de toepassing te maken.
1. Genereer een clientgeheim voordat u de Azure-portal verlaat.
1. Kies in de Azure-portal uw toepassing en selecteer **Certificaten & geheimen.**
1. Selecteer **Nieuw klantgeheim** en voeg een geheim toe met een looptijd van één of twee jaar.
1. Wanneer u deze pagina opslaat, geeft de Azure-portal de geheime waarde weer. Kopieer en sla de geheime waarde op een veilige locatie op.

> [!IMPORTANT]
> U hebt het geheim nodig om de toepassingsinstellingen in uw implementatie te configureren. Deze geheime waarde wordt niet opnieuw weergegeven en kan op geen enkele andere manier worden opgehaald. Neem het op zodra het zichtbaar is in de Azure-portal.

### <a name="register-the-client-application"></a>De clientaanvraag registreren

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Selecteer op de bovenste balk uw account en zoek onder de **lijst Map** een Active Directory-tenant voor uw toepassing te selecteren.
1. Selecteer **Meer services** in het linkerdeelvenster en kies Azure Active **Directory**.
1. Selecteer **App-registraties** en vervolgens **Nieuwe registratie**.
1. Voer een vriendelijke naam in voor de toepassing en selecteer het toepassingstype.
1. Selecteer onder **Ondersteunde accounttypen** de optie **Accounts in een organisatieadreslijst en persoonlijke Microsoft-account**.
1. Stel de omleidings-URI in naar de basis-URL.
1. Selecteer **Registreren** om de toepassing te maken.
1. Machtigingen voor uw toepassing configureren. Selecteer in **API-machtigingen**De optie **Een machtiging toevoegen** en vervolgens Mijn **API's**.
1. Typ de naam van de middelste-rijservice in het tekstveld.
1. Kies **Machtigingen selecteren** en selecteer vervolgens de naam van de ** \<Toegangsservice>**.

### <a name="configure-known-client-applications"></a>Bekende clienttoepassingen configureren

In dit scenario moet de middle-tier service toestemming van de gebruiker verkrijgen om toegang te krijgen tot de downstream API zonder interactie van de gebruiker. De optie om toegang te verlenen tot de downstream API moet vooraf worden gepresenteerd als onderdeel van de toestemmingsstap tijdens de verificatie.

Volg de onderstaande stappen om de registratie van de client-app in Azure AD expliciet te binden aan de registratie van de middle-tier service. Met deze bewerking worden de toestemming die vereist door zowel de client als de middelste laag, samengevoegd in één dialoogvenster.

1. Ga naar de serviceregistratie op het middenniveau en selecteer **Manifest** om de manifesteditor te openen.
1. Zoek `knownClientApplications` de eigenschap array en voeg de client-id van de clienttoepassing toe als element.
1. Sla het manifest op door **Opslaan te selecteren.**

## <a name="service-to-service-access-token-request"></a>Tokenaanvraag service-to-service-toegang

Als u een toegangstoken wilt aanvragen, maakt u een HTTP-bericht naar het tenantspecifieke Azure AD-eindpunt met de volgende parameters:

```
https://login.microsoftonline.com/<tenant>/oauth2/token
```

De clienttoepassing wordt beveiligd door een gedeeld geheim of door een certificaat.

### <a name="first-case-access-token-request-with-a-shared-secret"></a>Eerste geval: Toegang tot tokenaanvraag met een gedeeld geheim

Bij het gebruik van een gedeeld geheim bevat een service-to-service access tokenaanvraag de volgende parameters:

| Parameter |  | Beschrijving |
| --- | --- | --- |
| grant_type |vereist | Het type tokenaanvraag. Een OBO-aanvraag maakt gebruik van een JSON Web Token (JWT) zodat de waarde **urn:ietf:params:oauth:grant-type:jwt-bearer**moet zijn. |
| Bewering |vereist | De waarde van het toegangstoken dat in de aanvraag wordt gebruikt. |
| client_id |vereist | De app-id die tijdens de registratie met Azure AD aan de belservice is toegewezen. Als u de app-id in de Azure-portal wilt vinden, selecteert u **Active Directory,** kiest u de map en selecteert u vervolgens de toepassingsnaam. |
| client_secret |vereist | De sleutel die is geregistreerd voor de belservice in Azure AD. Deze waarde had moeten worden opgemerkt op het moment van registratie. |
| resource |vereist | De app ID URI van de ontvangende service (beveiligde bron). Als u de URI van de app-id in de Azure-portal wilt vinden, selecteert u **Active Directory** en kiest u de map. Selecteer de toepassingsnaam, kies **Alle instellingen**en selecteer **Eigenschappen**. |
| requested_token_use |vereist | Hiermee geeft u op hoe de aanvraag moet worden verwerkt. In de on-behalf-of-flow moet de waarde **on_behalf_of**zijn . |
| scope |vereist | Een ruimtegescheiden lijst met scopes voor de tokenaanvraag. Voor OpenID Connect moet de **openid** van het bereik worden opgegeven.|

#### <a name="example"></a>Voorbeeld

De volgende HTTP POST vraagt https://graph.microsoft.com een toegangstoken aan voor de web-API. De `client_id` service identificeert de service die het toegangstoken aanvraagt.

```
// line breaks for legibility only

POST /oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=urn%3Aietf%3Aparams%3Aoauth%3Agrant-type%3Ajwt-bearer
&client_id=625391af-c675-43e5-8e44-edd3e30ceb15
&client_secret=0Y1W%2BY3yYb3d9N8vSjvm8WrGzVZaAaHbHHcGbcgG%2BoI%3D
&resource=https%3A%2F%2Fgraph.microsoft.com
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.ewogICJhdWQiOiAiaHR0cHM6Ly9ncmFwaC5taWNyb3NvZnQuY29tIiwKICAiaXNzIjogImh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzI2MDM5Y2NlLTQ4OWQtNDAwMi04MjkzLTViMGM1MTM0ZWFjYi8iLAogICJpYXQiOiAxNDkzNDIzMTY4LAogICJuYmYiOiAxNDkzNDIzMTY4LAogICJleHAiOiAxNDkzNDY2OTUxLAogICJhY3IiOiAiMSIsCiAgImFpbyI6ICJBU1FBMi84REFBQUE1NnZGVmp0WlNjNWdBVWwrY1Z0VFpyM0VvV2NvZEoveWV1S2ZqcTZRdC9NPSIsCiAgImFtciI6IFsKICAgICJwd2QiCiAgXSwKICAiYXBwaWQiOiAiNjI1MzkxYWYtYzY3NS00M2U1LThlNDQtZWRkM2UzMGNlYjE1IiwKICAiYXBwaWRhY3IiOiAiMSIsCiAgImVfZXhwIjogMzAyNjgzLAogICJmYW1pbHlfbmFtZSI6ICJUZXN0IiwKICAiZ2l2ZW5fbmFtZSI6ICJOYXZ5YSIsCiAgImlwYWRkciI6ICIxNjcuMjIwLjEuMTc3IiwKICAibmFtZSI6ICJOYXZ5YSBUZXN0IiwKICAib2lkIjogIjFjZDRiY2FjLWI4MDgtNDIzYS05ZTJmLTgyN2ZiYjFiYjczOSIsCiAgInBsYXRmIjogIjMiLAogICJwdWlkIjogIjEwMDMzRkZGQTEyRUQ3RkUiLAogICJzY3AiOiAiVXNlci5SZWFkIiwKICAic3ViIjogIjNKTUlaSWJlYTc1R2hfWHdDN2ZzX0JDc3kxa1l1ekZKLTUyVm1Zd0JuM3ciLAogICJ0aWQiOiAiMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiIiwKICAidW5pcXVlX25hbWUiOiAibmF2eWFAZGRvYmFsaWFub3V0bG9vay5vbm1pY3Jvc29mdC5jb20iLAogICJ1cG4iOiAibmF2eWFAZGRvYmFsaWFub3V0bG9vay5vbm1pY3Jvc29mdC5jb20iLAogICJ1dGkiOiAieEN3ZnpoYS1QMFdKUU9MeENHZ0tBQSIsCiAgInZlciI6ICIxLjAiCn0.cqmUVjfVbqWsxJLUI1Z4FRx1mNQAHP-L0F4EMN09r8FY9bIKeO-0q1eTdP11Nkj_k4BmtaZsTcK_mUygdMqEp9AfyVyA1HYvokcgGCW_Z6DMlVGqlIU4ssEkL9abgl1REHElPhpwBFFBBenOk9iHddD1GddTn6vJbKC3qAaNM5VarjSPu50bVvCrqKNvFixTb5bbdnSz-Qr6n6ACiEimiI1aNOPR2DeKUyWBPaQcU5EAK0ef5IsVJC1yaYDlAcUYIILMDLCD9ebjsy0t9pj_7lvjzUSrbMdSCCdzCqez_MSNxrk1Nu9AecugkBYp3UVUZOIyythVrj6-sVvLZKUutQ
&requested_token_use=on_behalf_of
&scope=openid
```

### <a name="second-case-access-token-request-with-a-certificate"></a>Tweede aanvraag: Aanvraag voor toegangstoken met een certificaat

Een service-to-service access tokenaanvraag met een certificaat bevat de volgende parameters:

| Parameter |  | Beschrijving |
| --- | --- | --- |
| grant_type |vereist | Het type tokenaanvraag. Een OBO-aanvraag maakt gebruik van een JWT-toegangstoken, zodat de waarde **urn:ietf:params:oauth:grant-type:jwt-bearer**moet zijn. |
| Bewering |vereist | De waarde van het token dat in de aanvraag wordt gebruikt. |
| client_id |vereist | De app-id die tijdens de registratie met Azure AD aan de belservice is toegewezen. Als u de app-id in de Azure-portal wilt vinden, selecteert u **Active Directory,** kiest u de map en selecteert u vervolgens de toepassingsnaam. |
| client_assertion_type |vereist |De waarde moet`urn:ietf:params:oauth:client-assertion-type:jwt-bearer` |
| client_assertion |vereist | Een JSON-webtoken dat u maakt en ondertekent met het certificaat dat u hebt geregistreerd als referenties voor uw toepassing. Zie [certificaatreferenties](../develop/active-directory-certificate-credentials.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) voor meer informatie over de beweringsindeling en over het registreren van uw certificaat.|
| resource |vereist | De app ID URI van de ontvangende service (beveiligde bron). Als u de URI van de app-id in de Azure-portal wilt vinden, selecteert u **Active Directory** en kiest u de map. Selecteer de toepassingsnaam, kies **Alle instellingen**en selecteer **Eigenschappen**. |
| requested_token_use |vereist | Hiermee geeft u op hoe de aanvraag moet worden verwerkt. In de on-behalf-of-flow moet de waarde **on_behalf_of**zijn . |
| scope |vereist | Een ruimtegescheiden lijst met scopes voor de tokenaanvraag. Voor OpenID Connect moet de **openid** van het bereik worden opgegeven.|

Deze parameters zijn bijna hetzelfde als bij het `client_secret parameter` verzoek per gedeeld `client_assertion_type` `client_assertion`geheim, behalve dat de wordt vervangen door twee parameters: en .

#### <a name="example"></a>Voorbeeld

De volgende HTTP POST vraagt https://graph.microsoft.com een toegangstoken aan voor de web-API met een certificaat. De `client_id` service identificeert de service die het toegangstoken aanvraagt.

```
// line breaks for legibility only

POST /oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=urn%3Aietf%3Aparams%3Aoauth%3Agrant-type%3Ajwt-bearer
&client_id=625391af-c675-43e5-8e44-edd3e30ceb15
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&resource=https%3A%2F%2Fgraph.microsoft.com
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2Rkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tLzE5MjNmODYyLWU2ZGMtNDFhMy04MWRhLTgwMmJhZTAwYWY2ZCIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzI2MDM5Y2NlLTQ4OWQtNDAwMi04MjkzLTViMGM1MTM0ZWFjYi8iLCJpYXQiOjE0OTM0MjMxNTIsIm5iZiI6MTQ5MzQyMzE1MiwiZXhwIjoxNDkzNDY2NjUyLCJhY3IiOiIxIiwiYWlvIjoiWTJaZ1lCRFF2aTlVZEc0LzM0L3dpQndqbjhYeVp4YmR1TFhmVE1QeG8yYlN2elgreHBVQSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiJiMzE1MDA3OS03YmViLTQxN2YtYTA2YS0zZmRjNzhjMzI1NDUiLCJhcHBpZGFjciI6IjAiLCJlX2V4cCI6MzAyNDAwLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJEVXpYbkdKMDJIUk0zRW5pbDFxdjZCakxTNUllQy0tQ2ZpbzRxS1MzNEc4IiwidGlkIjoiMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiIiwidW5pcXVlX25hbWUiOiJuYXZ5YUBkZG9iYWxpYW5vdXRsb29rLm9ubWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidmVyIjoiMS4wIn0.R-Ke-XO7lK0r5uLwxB8g5CrcPAwRln5SccJCfEjU6IUqpqcjWcDzeDdNOySiVPDU_ZU5knJmzRCF8fcjFtPsaA4R7vdIEbDuOur15FXSvE8FvVSjP_49OH6hBYqoSUAslN3FMfbO6Z8YfCIY4tSOB2I6ahQ_x4ZWFWglC3w5mK-_4iX81bqi95eV4RUKefUuHhQDXtWhrSgIEC0YiluMvA4TnaJdLq_tWXIc4_Tq_KfpkvI004ONKgU7EAMEr1wZ4aDcJV2yf22gQ1sCSig6EGSTmmzDuEPsYiyd4NhidRZJP4HiiQh-hePBQsgcSgYGvz9wC6n57ufYKh2wm_Ti3Q
&requested_token_use=on_behalf_of
&scope=openid
```

## <a name="service-to-service-access-token-response"></a>Service-to-service-antwoord op tokenvan service tot service

Een succesrespons is een JSON OAuth 2.0-respons met de volgende parameters:

| Parameter | Beschrijving |
| --- | --- |
| token_type |Geeft de waarde van het tokentype aan. Het enige type dat Azure AD ondersteunt, is **Drager**. Zie het [OAuth 2.0 Authorization Framework: Toondertokengebruik (RFC 6750) voor](https://www.rfc-editor.org/rfc/rfc6750.txt)meer informatie over tokens aan toonder. |
| scope |De reikwijdte van de toegang die in het token wordt verleend. |
| expires_in |De tijdsduur van het toegangstoken is geldig (in seconden). |
| expires_on |Het tijdstip waarop het toegangstoken verloopt. De datum wordt weergegeven als het aantal seconden van 1970-01-01T0:0:0Z UTC tot de vervaldatum. Deze waarde wordt gebruikt om de levensduur van tokens in de cache te bepalen. |
| resource |De app ID URI van de ontvangende service (beveiligde bron). |
| access_token |Het gevraagde toegangstoken. De oproepservice kan dit token gebruiken om te verifiëren aan de ontvangende service. |
| id_token |Het gevraagde ID-token. De oproepservice kan dit token gebruiken om de identiteit van de gebruiker te verifiëren en een sessie met de gebruiker te beginnen. |
| refresh_token |Het vernieuwingstoken voor het gevraagde toegangstoken. De aanroepende service kan dit token gebruiken om een ander toegangstoken aan te vragen nadat het huidige toegangstoken is verlopen. |

### <a name="success-response-example"></a>Voorbeeld van succesrespons

In het volgende voorbeeld wordt een succesreactie weergegeven https://graph.microsoft.com op een aanvraag voor een toegangstoken voor de web-API.

```json
{
    "token_type":"Bearer",
    "scope":"User.Read",
    "expires_in":"43482",
    "ext_expires_in":"302683",
    "expires_on":"1493466951",
    "not_before":"1493423168",
    "resource":"https://graph.microsoft.com",
    "access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2dyYXBoLndpbmRvd3MubmV0IiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiLyIsImlhdCI6MTQ5MzQyMzE2OCwibmJmIjoxNDkzNDIzMTY4LCJleHAiOjE0OTM0NjY5NTEsImFjciI6IjEiLCJhaW8iOiJBU1FBMi84REFBQUE1NnZGVmp0WlNjNWdBVWwrY1Z0VFpyM0VvV2NvZEoveWV1S2ZqcTZRdC9NPSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiI2MjUzOTFhZi1jNjc1LTQzZTUtOGU0NC1lZGQzZTMwY2ViMTUiLCJhcHBpZGFjciI6IjEiLCJlX2V4cCI6MzAyNjgzLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJwdWlkIjoiMTAwMzNGRkZBMTJFRDdGRSIsInNjcCI6IlVzZXIuUmVhZCIsInN1YiI6IjNKTUlaSWJlYTc1R2hfWHdDN2ZzX0JDc3kxa1l1ekZKLTUyVm1Zd0JuM3ciLCJ0aWQiOiIyNjAzOWNjZS00ODlkLTQwMDItODI5My01YjBjNTEzNGVhY2IiLCJ1bmlxdWVfbmFtZSI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidXBuIjoibmF2eWFAZGRvYmFsaWFub3V0bG9vay5vbm1pY3Jvc29mdC5jb20iLCJ1dGkiOiJ4Q3dmemhhLVAwV0pRT0x4Q0dnS0FBIiwidmVyIjoiMS4wIn0.cqmUVjfVbqWsxJLUI1Z4FRx1mNQAHP-L0F4EMN09r8FY9bIKeO-0q1eTdP11Nkj_k4BmtaZsTcK_mUygdMqEp9AfyVyA1HYvokcgGCW_Z6DMlVGqlIU4ssEkL9abgl1REHElPhpwBFFBBenOk9iHddD1GddTn6vJbKC3qAaNM5VarjSPu50bVvCrqKNvFixTb5bbdnSz-Qr6n6ACiEimiI1aNOPR2DeKUyWBPaQcU5EAK0ef5IsVJC1yaYDlAcUYIILMDLCD9ebjsy0t9pj_7lvjzUSrbMdSCCdzCqez_MSNxrk1Nu9AecugkBYp3UVUZOIyythVrj6-sVvLZKUutQ",
    "refresh_token":"AQABAAAAAABnfiG-mA6NTae7CdWW7QfdjKGu9-t1scy_TDEmLi4eLQMjJGt_nAoVu6A4oSu1KsRiz8XyQIPKQxSGfbf2FoSK-hm2K8TYzbJuswYusQpJaHUQnSqEvdaCeFuqXHBv84wjFhuanzF9dQZB_Ng5za9xKlUENrNtlq9XuLNVKzxEyeUM7JyxzdY7JiEphWImwgOYf6II316d0Z6-H3oYsFezf4Xsjz-MOBYEov0P64UaB5nJMvDyApV-NWpgklLASfNoSPGb67Bc02aFRZrm4kLk-xTl6eKE6hSo0XU2z2t70stFJDxvNQobnvNHrAmBaHWPAcC3FGwFnBOojpZB2tzG1gLEbmdROVDp8kHEYAwnRK947Py12fJNKExUdN0njmXrKxNZ_fEM33LHW1Tf4kMX_GvNmbWHtBnIyG0w5emb-b54ef5AwV5_tGUeivTCCysgucEc-S7G8Cz0xNJ_BOiM_4bAv9iFmrm9STkltpz0-Tftg8WKmaJiC0xXj6uTf4ZkX79mJJIuuM7XP4ARIcLpkktyg2Iym9jcZqymRkGH2Rm9sxBwC4eeZXM7M5a7TJ-5CqOdfuE3sBPq40RdEWMFLcrAzFvP0VDR8NKHIrPR1AcUruat9DETmTNJukdlJN3O41nWdZOVoJM-uKN3uz2wQ2Ld1z0Mb9_6YfMox9KTJNzRzcL52r4V_y3kB6ekaOZ9wQ3HxGBQ4zFt-2U0mSszIAA",
    "id_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiI2MjUzOTFhZi1jNjc1LTQzZTUtOGU0NC1lZGQzZTMwY2ViMTUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC8yNjAzOWNjZS00ODlkLTQwMDItODI5My01YjBjNTEzNGVhY2IvIiwiaWF0IjoxNDkzNDIzMTY4LCJuYmYiOjE0OTM0MjMxNjgsImV4cCI6MTQ5MzQ2Njk1MSwiYW1yIjpbInB3ZCJdLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJzdWIiOiJEVXpYbkdKMDJIUk0zRW5pbDFxdjZCakxTNUllQy0tQ2ZpbzRxS1MzNEc4IiwidGlkIjoiMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiIiwidW5pcXVlX25hbWUiOiJuYXZ5YUBkZG9iYWxpYW5vdXRsb29rLm9ubWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidXRpIjoieEN3ZnpoYS1QMFdKUU9MeENHZ0tBQSIsInZlciI6IjEuMCJ9."
}
```

### <a name="error-response-example"></a>Voorbeeld van foutreactie

Het eindpunt van het Azure AD-token retourneert een foutreactie wanneer het een toegangstoken probeert te verkrijgen voor een downstream-API die is ingesteld met een beleid voor voorwaardelijke toegang (bijvoorbeeld meervoudige verificatie). De service op het middenniveau moet deze fout aan de clienttoepassing weergeven, zodat de clienttoepassing de gebruikersinteractie kan bieden om te voldoen aan het beleid voor voorwaardelijke toegang.

```json
{
    "error":"interaction_required",
    "error_description":"AADSTS50079: Due to a configuration change made by your administrator, or because you moved to a new location, you must enroll in multi-factor authentication to access 'bf8d80f9-9098-4972-b203-500f535113b1'.\r\nTrace ID: b72a68c3-0926-4b8e-bc35-3150069c2800\r\nCorrelation ID: 73d656cf-54b1-4eb2-b429-26d8165a52d7\r\nTimestamp: 2017-05-01 22:43:20Z",
    "error_codes":[50079],
    "timestamp":"2017-05-01 22:43:20Z",
    "trace_id":"b72a68c3-0926-4b8e-bc35-3150069c2800",
    "correlation_id":"73d656cf-54b1-4eb2-b429-26d8165a52d7",
    "claims":"{\"access_token\":{\"polids\":{\"essential\":true,\"values\":[\"9ab03e19-ed42-4168-b6b7-7001fb3e933a\"]}}}"
}
```

## <a name="use-the-access-token-to-access-the-secured-resource"></a>Het toegangstoken gebruiken om toegang te krijgen tot de beveiligde bron

De middle-tier service kan het verkregen toegangstoken gebruiken om geverifieerde aanvragen te `Authorization` doen voor de downstream web-API door het token in de koptekst in te stellen.

### <a name="example"></a>Voorbeeld

```
GET /me?api-version=2013-11-08 HTTP/1.1
Host: graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2dyYXBoLndpbmRvd3MubmV0IiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiLyIsImlhdCI6MTQ5MzQyMzE2OCwibmJmIjoxNDkzNDIzMTY4LCJleHAiOjE0OTM0NjY5NTEsImFjciI6IjEiLCJhaW8iOiJBU1FBMi84REFBQUE1NnZGVmp0WlNjNWdBVWwrY1Z0VFpyM0VvV2NvZEoveWV1S2ZqcTZRdC9NPSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiI2MjUzOTFhZi1jNjc1LTQzZTUtOGU0NC1lZGQzZTMwY2ViMTUiLCJhcHBpZGFjciI6IjEiLCJlX2V4cCI6MzAyNjgzLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJwdWlkIjoiMTAwMzNGRkZBMTJFRDdGRSIsInNjcCI6IlVzZXIuUmVhZCIsInN1YiI6IjNKTUlaSWJlYTc1R2hfWHdDN2ZzX0JDc3kxa1l1ekZKLTUyVm1Zd0JuM3ciLCJ0aWQiOiIyNjAzOWNjZS00ODlkLTQwMDItODI5My01YjBjNTEzNGVhY2IiLCJ1bmlxdWVfbmFtZSI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidXBuIjoibmF2eWFAZGRvYmFsaWFub3V0bG9vay5vbm1pY3Jvc29mdC5jb20iLCJ1dGkiOiJ4Q3dmemhhLVAwV0pRT0x4Q0dnS0FBIiwidmVyIjoiMS4wIn0.cqmUVjfVbqWsxJLUI1Z4FRx1mNQAHP-L0F4EMN09r8FY9bIKeO-0q1eTdP11Nkj_k4BmtaZsTcK_mUygdMqEp9AfyVyA1HYvokcgGCW_Z6DMlVGqlIU4ssEkL9abgl1REHElPhpwBFFBBenOk9iHddD1GddTn6vJbKC3qAaNM5VarjSPu50bVvCrqKNvFixTb5bbdnSz-Qr6n6ACiEimiI1aNOPR2DeKUyWBPaQcU5EAK0ef5IsVJC1yaYDlAcUYIILMDLCD9ebjsy0t9pj_7lvjzUSrbMdSCCdzCqez_MSNxrk1Nu9AecugkBYp3UVUZOIyythVrj6-sVvLZKUutQ
```

## <a name="saml-assertions-obtained-with-an-oauth20-obo-flow"></a>SAML beweringen verkregen met een OAuth2.0 OBO flow

Sommige Op OAuth gebaseerde webservices moeten toegang krijgen tot andere webservice-API's die SAML-beweringen accepteren in niet-interactieve stromen. Azure Active Directory kan een SAML-bewering bieden als reactie op een on-behalf-of-flow die een SAML-gebaseerde webservice als doelbron gebruikt.

>[!NOTE]
>Dit is een niet-standaard extensie voor de OAuth 2.0 On-Behalf-Of flow waarmee een Op OAuth2 gebaseerde toepassing toegang heeft tot webservice API-eindpunten die SAML-tokens gebruiken.

> [!TIP]
> Wanneer u een SAML-beveiligde webservice aanroept vanuit een front-end webtoepassing, u eenvoudig de API aanroepen en een normale interactieve verificatiestroom starten met de bestaande sessie van de gebruiker. U hoeft alleen een OBO-stroom te gebruiken wanneer een service-to-service-oproep een SAML-token vereist om gebruikerscontext te bieden.

### <a name="obtain-a-saml-token-by-using-an-obo-request-with-a-shared-secret"></a>Een SAML-token verkrijgen met behulp van een OBO-verzoek met een gedeeld geheim

Een service-to-service-aanvraag voor een SAML-bewering bevat de volgende parameters:

| Parameter |  | Beschrijving |
| --- | --- | --- |
| grant_type |vereist | Het type tokenaanvraag. Voor een aanvraag die een JWT gebruikt, moet de waarde **urn:ietf:params:oauth:grant-type:jwt-bearer**zijn. |
| Bewering |vereist | De waarde van het toegangstoken dat in de aanvraag wordt gebruikt.|
| client_id |vereist | De app-id die tijdens de registratie met Azure AD aan de belservice is toegewezen. Als u de app-id in de Azure-portal wilt vinden, selecteert u **Active Directory,** kiest u de map en selecteert u vervolgens de toepassingsnaam. |
| client_secret |vereist | De sleutel die is geregistreerd voor de belservice in Azure AD. Deze waarde had moeten worden opgemerkt op het moment van registratie. |
| resource |vereist | De app ID URI van de ontvangende service (beveiligde bron). Dit is de bron die het publiek van het SAML-token zal zijn. Als u de URI van de app-id in de Azure-portal wilt vinden, selecteert u **Active Directory** en kiest u de map. Selecteer de toepassingsnaam, kies **Alle instellingen**en selecteer **Eigenschappen**. |
| requested_token_use |vereist | Hiermee geeft u op hoe de aanvraag moet worden verwerkt. In de on-behalf-of-flow moet de waarde **on_behalf_of**zijn . |
| requested_token_type | vereist | Hiermee geeft u het type token op dat is aangevraagd. De waarde kan **urn:ietf:params:oauth:token-type:saml2** of **urn:ietf:params:oauth:token-type:saml1** afhankelijk van de vereisten van de toegangbron zijn. |

Het antwoord bevat een SAML-token gecodeerd in UTF8 en Base64url.

- **SubjectConfirmationData for a SAML-bewering afkomstig van een OBO-aanroep:** Als de doeltoepassing een waarde van de ontvanger vereist in **SubjectConfirmationData,** moet de waarde een URL voor een niet-wildcard-antwoord zijn in de configuratie van de brontoepassing.
- **Het knooppunt SubjectConfirmationData**: Het knooppunt kan geen **InResponseTo-kenmerk** bevatten, omdat het geen deel uitmaakt van een SAML-antwoord. De toepassing die het SAML-token ontvangt, moet de SAML-bewering kunnen accepteren zonder een **InResponseTo-kenmerk.**

- **Toestemming**: Er moet toestemming zijn verleend voor het ontvangen van een SAML-token met gebruikersgegevens op een OAuth-stroom. Zie Machtigingen en toestemming in het [Azure Active Directory v1.0-eindpunt](https://docs.microsoft.com/azure/active-directory/azuread-dev/v1-permissions-consent)voor informatie over machtigingen en het verkrijgen van toestemming van beheerders.

### <a name="response-with-saml-assertion"></a>Reactie met SAML-bewering

| Parameter | Beschrijving |
| --- | --- |
| token_type |Geeft de waarde van het tokentype aan. Het enige type dat Azure AD ondersteunt, is **Drager**. Zie [OAuth 2.0 Authorization Framework: Bearer Token Usage (RFC 6750) voor](https://www.rfc-editor.org/rfc/rfc6750.txt)meer informatie over tokens aan toonder. |
| scope |De reikwijdte van de toegang die in het token wordt verleend. |
| expires_in |De tijdsduur van het toegangstoken is geldig (in seconden). |
| expires_on |Het tijdstip waarop het toegangstoken verloopt. De datum wordt weergegeven als het aantal seconden van 1970-01-01T0:0:0Z UTC tot de vervaldatum. Deze waarde wordt gebruikt om de levensduur van tokens in de cache te bepalen. |
| resource |De app ID URI van de ontvangende service (beveiligde bron). |
| access_token |De parameter die de SAML-bewering retourneert. |
| refresh_token |Het vernieuwingstoken. De aanroepende service kan dit token gebruiken om een ander toegangstoken aan te vragen nadat de huidige SAML-bewering is verlopen. |

- token_type: Drager
- expires_in: 3296
- ext_expires_in: 0
- expires_on: 1529627844
- Resource:`https://api.contoso.com`
- access_token: \<SAML-bewering\>
- issued_token_type: urn:ietf:params:oauth:token-type:saml2
- refresh_token: \<Token vernieuwen\>

## <a name="client-limitations"></a>Clientbeperkingen

Url's met een wildcard-antwoord `id_token` kunnen geen URL's voor OBO-stromen gebruiken. Een vertrouwelijke client kan echter nog steeds **toegangstokens** inwisselen die zijn verkregen via de impliciete subsidiestroom, zelfs als de openbare client een wildcard omleiding URI heeft geregistreerd.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het OAuth 2.0-protocol en een andere manier om service-to-service-verificatie uit te voeren die clientreferenties gebruikt:

* [Service tot serviceverificatie met OAuth 2.0-clientreferenties verlenen in Azure AD](v1-oauth2-client-creds-grant-flow.md)
* [OAuth 2.0 in Azure AD](v1-protocols-oauth-code.md)
