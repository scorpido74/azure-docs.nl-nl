---
title: Azure AD Service to Service Auth met OAuth2.0 | Microsoft Documenten
description: In dit artikel wordt beschreven hoe u HTTP-berichten gebruiken om serviceverificatie te implementeren met behulp van de subsidiestroom van De OAuth2.0-clientreferenties.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 02/08/2017
ms.author: ryanwi
ms.reviewer: nacanuma
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: f2d1eaec80c8925eb7b38af848e29e944f1ebf69
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154539"
---
# <a name="service-to-service-calls-using-client-credentials-shared-secret-or-certificate"></a>Service aan serviceoproepen met behulp van clientreferenties (gedeeld geheim of certificaat)

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Met de OAuth 2.0 Client Credentials Grant Flow kan een webservice *(vertrouwelijke client)* zijn eigen referenties gebruiken in plaats van zich voor te doen als een gebruiker, om te verifiëren wanneer u een andere webservice aanroept. In dit scenario is de client meestal een middle-tier webservice, een daemon-service of website. Voor een hoger niveau van zekerheid staat Azure AD de oproepservice ook toe om een certificaat (in plaats van een gedeeld geheim) als referentie te gebruiken.

## <a name="client-credentials-grant-flow-diagram"></a>Stroomdiagram voor clientreferenties
In het volgende diagram wordt uitgelegd hoe de subsidiestroom voor clientreferenties werkt in Azure Active Directory (Azure AD).

![Subsidiestroom clientreferenties van OAuth2.0-client](./media/v1-oauth2-client-creds-grant-flow/active-directory-protocols-oauth-client-credentials-grant-flow.jpg)

1. De clienttoepassing verifieert naar het eindpunt voor azure AD-tokenuitgifte en vraagt om een toegangstoken.
2. Het eindpunt voor azure AD-tokenuitgifte geeft het toegangstoken uit.
3. Het toegangstoken wordt gebruikt om te verifiëren naar de beveiligde bron.
4. Gegevens uit de beveiligde bron worden teruggestuurd naar de clienttoepassing.

## <a name="register-the-services-in-azure-ad"></a>De Services registreren in Azure AD
Registreer zowel de oproepservice als de ontvangende service in Azure Active Directory (Azure AD). Zie [Toepassingen integreren met Azure Active Directory](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)voor gedetailleerde instructies.

## <a name="request-an-access-token"></a>Een Access-token aanvragen
Als u een toegangstoken wilt aanvragen, gebruikt u een HTTP-BERICHT naar het tenantspecifieke Azure AD-eindpunt.

```
https://login.microsoftonline.com/<tenant id>/oauth2/token
```

## <a name="service-to-service-access-token-request"></a>Tokenaanvraag service-to-service-toegang
Er zijn twee gevallen, afhankelijk van of de clienttoepassing ervoor kiest om te worden beveiligd door een gedeeld geheim, of een certificaat.

### <a name="first-case-access-token-request-with-a-shared-secret"></a>Eerste geval: Toegang tot tokenaanvraag met een gedeeld geheim
Bij het gebruik van een gedeeld geheim bevat een service-to-service access tokenaanvraag de volgende parameters:

| Parameter |  | Beschrijving |
| --- | --- | --- |
| grant_type |vereist |Hiermee geeft u het aangevraagde subsidietype op. In een clientreferentiessubsidiestroom moet de waarde worden **client_credentials**. |
| client_id |vereist |Hiermee geeft u de Azure AD-client-id van de oproepwebservice op. Als u de client-id van de aanroepende toepassing wilt vinden, klikt u in de [Azure-portal](https://portal.azure.com)op **Azure Active Directory**, klikt u op **App-registraties**en klikt u op de toepassing. De client_id is de *applicatie-id* |
| client_secret |vereist |Voer een sleutel in die is geregistreerd voor de oproepwebservice of daemon-toepassing in Azure AD. Als u een sleutel wilt maken, klikt u in de **Azure-portal**op Azure Active Directory , klikt u op **App-registraties,** klikt u op de toepassing, klikt u op **Instellingen,** klikt u op **Sleutels**en voegt u een sleutel toe.  URL-coderen dit geheim bij het verstrekken van het. |
| resource |vereist |Voer de URI van de app-id van de ontvangende webservice in. Als u de URI voor app-id's wilt vinden, klikt u in de Azure Active Directory op **Azure Active Directory**, klikt u op **App-registraties,** klikt u op de servicetoepassing en klikt u vervolgens op **Instellingen** en **Eigenschappen**. |

#### <a name="example"></a>Voorbeeld
De volgende HTTP POST vraagt `https://service.contoso.com/` een [toegangstoken](../develop/access-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) aan voor de webservice. De `client_id` identificeert de webservice die het toegangstoken aanvraagt.

```
POST /contoso.com/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials&client_id=625bc9f6-3bf6-4b6d-94ba-e97cf07a22de&client_secret=qkDwDJlDfig2IpeuUZYKH1Wb8q1V0ju6sILxQQqhJ+s=&resource=https%3A%2F%2Fservice.contoso.com%2F
```

### <a name="second-case-access-token-request-with-a-certificate"></a>Tweede aanvraag: Aanvraag voor toegangstoken met een certificaat
Een service-to-service access tokenaanvraag met een certificaat bevat de volgende parameters:

| Parameter |  | Beschrijving |
| --- | --- | --- |
| grant_type |vereist |Hiermee geeft u het gevraagde antwoordtype op. In een clientreferentiessubsidiestroom moet de waarde worden **client_credentials**. |
| client_id |vereist |Hiermee geeft u de Azure AD-client-id van de oproepwebservice op. Als u de client-id van de aanroepende toepassing wilt vinden, klikt u in de [Azure-portal](https://portal.azure.com)op **Azure Active Directory**, klikt u op **App-registraties**en klikt u op de toepassing. De client_id is de *applicatie-id* |
| client_assertion_type |vereist |De waarde moet`urn:ietf:params:oauth:client-assertion-type:jwt-bearer` |
| client_assertion |vereist | Een bewering (een JSON-webtoken) dat u moet maken en ondertekenen met het certificaat dat u hebt geregistreerd als referenties voor uw toepassing. Lees meer over [certificaatreferenties](../develop/active-directory-certificate-credentials.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) om te leren hoe u uw certificaat en de notatie van de bewering registreert.|
| resource | vereist |Voer de URI van de app-id van de ontvangende webservice in. Als u de URI voor app-id's wilt vinden, klikt u in de Azure Active Directory op **Azure Active Directory**, klikt u op **App-registraties,** klikt u op de servicetoepassing en klikt u vervolgens op **Instellingen** en **Eigenschappen**. |

Merk op dat de parameters zijn bijna hetzelfde als in het geval van het verzoek door gedeeld geheim, behalve dat de client_secret parameter wordt vervangen door twee parameters: een client_assertion_type en client_assertion.

#### <a name="example"></a>Voorbeeld
De volgende HTTP POST vraagt `https://service.contoso.com/` een toegangstoken aan voor de webservice met een certificaat. De `client_id` identificeert de webservice die het toegangstoken aanvraagt.

```
POST /<tenant_id>/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

resource=https%3A%2F%contoso.onmicrosoft.com%2Ffc7664b4-cdd6-43e1-9365-c2e1c4e1b3bf&client_id=97e0a5b7-d745-40b6-94fe-5f77d35c6e05&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg&grant_type=client_credentials
```

### <a name="service-to-service-access-token-response"></a>Reactie van service-to-servicetoegang- tokenrespons

Een succesrespons bevat een JSON OAuth 2.0 respons met de volgende parameters:

| Parameter | Beschrijving |
| --- | --- |
| access_token |Het gevraagde toegangstoken. De bellende webservice kan dit token gebruiken om te verifiëren bij de ontvangende webservice. |
| token_type |Geeft de waarde van het tokentype aan. Het enige type dat Azure AD ondersteunt, is **Drager**. Zie Het [OAuth 2.0 Authorization Framework: Toondertokengebruik (RFC 6750) voor](https://www.rfc-editor.org/rfc/rfc6750.txt)meer informatie over tokens aan toonder. |
| expires_in |Hoe lang het toegangstoken geldig is (in seconden). |
| expires_on |Het tijdstip waarop het toegangstoken verloopt. De datum wordt weergegeven als het aantal seconden van 1970-01-01T0:0:0Z UTC tot de vervaldatum. Deze waarde wordt gebruikt om de levensduur van tokens in de cache te bepalen. |
| not_before |Het tijdstip waarop het toegangstoken bruikbaar wordt. De datum wordt weergegeven als het aantal seconden van 1970-01-01T0:0:0Z UTC tot het tijdstip van geldigheid voor het token.|
| resource |De App ID URI van de ontvangende webservice. |

#### <a name="example-of-response"></a>Voorbeeld van respons
In het volgende voorbeeld wordt een succesreactie weergegeven op een aanvraag voor een toegangstoken voor een webservice.

```
{
"access_token":"eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0ODI2NywibmJmIjoxMzg4NDQ4MjY3LCJleHAiOjEzODg0NTIxNjcsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6ImE5OTE5MTYyLTkyMTctNDlkYS1hZTIyLWYxMTM3YzI1Y2RlYSIsInN1YiI6ImE5OTE5MTYyLTkyMTctNDlkYS1hZTIyLWYxMTM3YzI1Y2RlYSIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZS8iLCJhcHBpZCI6ImQxN2QxNWJjLWM1NzYtNDFlNS05MjdmLWRiNWYzMGRkNThmMSIsImFwcGlkYWNyIjoiMSJ9.aqtfJ7G37CpKV901Vm9sGiQhde0WMg6luYJR4wuNR2ffaQsVPPpKirM5rbc6o5CmW1OtmaAIdwDcL6i9ZT9ooIIicSRrjCYMYWHX08ip-tj-uWUihGztI02xKdWiycItpWiHxapQm0a8Ti1CWRjJghORC1B1-fah_yWx6Cjuf4QE8xJcu-ZHX0pVZNPX22PHYV5Km-vPTq2HtIqdboKyZy3Y4y3geOrRIFElZYoqjqSv5q9Jgtj5ERsNQIjefpyxW3EwPtFqMcDm4ebiAEpoEWRN4QYOMxnC9OUBeG9oLA0lTfmhgHLAtvJogJcYFzwngTsVo6HznsvPWy7UP3MINA",
"token_type":"Bearer",
"expires_in":"3599",
"expires_on":"1388452167",
"resource":"https://service.contoso.com/"
}
```

## <a name="see-also"></a>Zie ook
* [OAuth 2.0 in Azure AD](v1-protocols-oauth-code.md)
* [Voorbeeld in C# van de serviceserviceoproep met een gedeeld geheim](https://github.com/Azure-Samples/active-directory-dotnet-daemon) en Voorbeeld in [C# van de serviceserviceoproep met een certificaat](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential)
