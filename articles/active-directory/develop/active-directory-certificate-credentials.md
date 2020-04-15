---
title: Microsoft-certificaatreferenties voor identiteitsplatform
titleSuffix: Microsoft identity platform
description: In dit artikel wordt de registratie en het gebruik van certificaatreferenties voor toepassingsverificatie besproken.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: hirsin
ms.reviewer: nacanuma, jmprieur
ms.custom: aaddev
ms.openlocfilehash: d834d37f686279facf0052e3c8f4da3c8d259e0b
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81309679"
---
# <a name="microsoft-identity-platform-application-authentication-certificate-credentials"></a>Referenties van microsoft-identiteitsplatformverificatiecertificaat

Met het Microsoft-identiteitsplatform kan een toepassing zijn eigen referenties gebruiken voor verificatie, bijvoorbeeld in de [OAuth 2.0 Client Credentials Grant flowv2.0](v2-oauth2-client-creds-grant-flow.md) en de [on-behalf-of-flow).](v2-oauth2-on-behalf-of-flow.md)

Een vorm van referenties die een toepassing kan gebruiken voor verificatie is een JSON Web Token (JWT) bewering ondertekend met een certificaat dat de toepassing bezit.

## <a name="assertion-format"></a>Beweringsindeling
Microsoft-identiteitsplatform Om de bewering te berekenen, u een van de vele [JSON-webtokenbibliotheken](https://jwt.ms/) gebruiken in de taal van uw keuze. De informatie die door het token wordt overgedragen, is als volgt:

### <a name="header"></a>Header

| Parameter |  Opmerking |
| --- | --- |
| `alg` | Moet **RS256** |
| `typ` | Moet **JWT** |
| `x5t` | Moet de X.509 Certificate SHA-1 duimafdruk zijn |

### <a name="claims-payload"></a>Claims (payload)

| Parameter |  Opmerkingen |
| --- | --- |
| `aud` | Publiek: Moet ** https://login.microsoftonline.com/ *worden tenant_Id*/oauth2/token** |
| `exp` | Vervaldatum: de datum waarop het token verloopt. De tijd wordt weergegeven als het aantal seconden vanaf 1 januari 1970 (1970-01-01T0:0:0Z) UTC tot het moment dat de tokengeldigheid verloopt.|
| `iss` | Uitgever: moet de client_id zijn (application ID van de clientservice) |
| `jti` | GUID: de JWT ID |
| `nbf` | Niet eerder: de datum waarvoor het token niet kan worden gebruikt. De tijd wordt weergegeven als het aantal seconden vanaf 1 januari 1970 (1970-01-01T0:0:0Z) UTC tot het moment dat het token werd uitgegeven. |
| `sub` | Betreft: Wat `iss`betreft , moet de client_id zijn (Application ID van de client service) |

### <a name="signature"></a>Handtekening

De handtekening wordt berekend bij het toepassen van het certificaat zoals beschreven in de [JSON Web Token RFC7519-specificatie](https://tools.ietf.org/html/rfc7519)

## <a name="example-of-a-decoded-jwt-assertion"></a>Voorbeeld van een gedecodeerde JWT-bewering

```JSON
{
  "alg": "RS256",
  "typ": "JWT",
  "x5t": "gx8tGysyjcRqKjFPnd7RFwvwZI0"
}
.
{
  "aud": "https: //login.microsoftonline.com/contoso.onmicrosoft.com/oauth2/token",
  "exp": 1484593341,
  "iss": "97e0a5b7-d745-40b6-94fe-5f77d35c6e05",
  "jti": "22b3bb26-e046-42df-9c96-65dbd72c1c81",
  "nbf": 1484592741,
  "sub": "97e0a5b7-d745-40b6-94fe-5f77d35c6e05"
}
.
"Gh95kHCOEGq5E_ArMBbDXhwKR577scxYaoJ1P{a lot of characters here}KKJDEg"
```

## <a name="example-of-an-encoded-jwt-assertion"></a>Voorbeeld van een gecodeerde JWT-bewering

De volgende tekenreeks is een voorbeeld van gecodeerde bewering. Als je goed kijkt, zie je drie secties gescheiden door stippen (.):
* Het eerste deel codeert de koptekst
* Het tweede deel codeert het laadvermogen
* De laatste sectie is de handtekening berekend met de certificaten van de inhoud van de eerste twee secties

```
"eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJhdWQiOiJodHRwczpcL1wvbG9naW4ubWljcm9zb2Z0b25saW5lLmNvbVwvam1wcmlldXJob3RtYWlsLm9ubWljcm9zb2Z0LmNvbVwvb2F1dGgyXC90b2tlbiIsImV4cCI6MTQ4NDU5MzM0MSwiaXNzIjoiOTdlMGE1YjctZDc0NS00MGI2LTk0ZmUtNWY3N2QzNWM2ZTA1IiwianRpIjoiMjJiM2JiMjYtZTA0Ni00MmRmLTljOTYtNjVkYmQ3MmMxYzgxIiwibmJmIjoxNDg0NTkyNzQxLCJzdWIiOiI5N2UwYTViNy1kNzQ1LTQwYjYtOTRmZS01Zjc3ZDM1YzZlMDUifQ.
Gh95kHCOEGq5E_ArMBbDXhwKR577scxYaoJ1P{a lot of characters here}KKJDEg"
```

## <a name="register-your-certificate-with-microsoft-identity-platform"></a>Uw certificaat registreren bij microsoft-identiteitsplatform

U de certificaatreferenties koppelen aan de clienttoepassing in het Microsoft-identiteitsplatform via de Azure-portal met behulp van een van de volgende methoden:

### <a name="uploading-the-certificate-file"></a>Het certificaatbestand uploaden

In de Azure-appregistratie voor de clienttoepassing:
1. Selecteer **Certificaten & geheimen**.
2. Klik op **Certificaat uploaden** en selecteer het certificaatbestand dat u wilt uploaden.
3. Klik op**toevoegen**.
  Zodra het certificaat is geüpload, worden de duimafdruk, begindatum en vervaldatumwaarden weergegeven.

### <a name="updating-the-application-manifest"></a>Het toepassingsmanifest bijwerken

Nadat u een certificaat in handen hebt, moet u berekenen:

- `$base64Thumbprint`, dat is de base64 codering van het certificaat hash
- `$base64Value`, dat is de base64 codering van het certificaat ruwe gegevens

U moet ook een GUID verstrekken om de`$keyId`sleutel in het toepassingsmanifest te identificeren ( ).

In de Azure-appregistratie voor de clienttoepassing:
1. Selecteer **Manifest** om het toepassingsmanifest te openen.
2. Vervang de eigenschap *keyCredentials* door uw nieuwe certificaatgegevens met behulp van het volgende schema.

   ```JSON
   "keyCredentials": [
       {
           "customKeyIdentifier": "$base64Thumbprint",
           "keyId": "$keyid",
           "type": "AsymmetricX509Cert",
           "usage": "Verify",
           "value":  "$base64Value"
       }
   ]
   ```
3. Sla de bewerkingen op in het toepassingsmanifest en upload het manifest vervolgens naar het identiteitsplatform van Microsoft.

   De `keyCredentials` eigenschap is multi-gewaardeerd, dus u meerdere certificaten uploaden voor rijker sleutelbeheer.

## <a name="code-sample"></a>Codevoorbeeld

> [!NOTE]
> U moet de X5T-header berekenen door deze om te zetten naar een basis64-tekenreeks met behulp van de hash van het certificaat. De code om dit in `System.Convert.ToBase64String(cert.GetCertHash());`C# uit te voeren is .

In het codevoorbeeld [.NET Core daemon-consoletoepassing met Microsoft-identiteitsplatform](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) ziet u hoe een toepassing zijn eigen referenties gebruikt voor verificatie. Het laat ook zien hoe u een `New-SelfSignedCertificate` zelfondertekend certificaat maken met de powershell-opdracht. [create a self-signed certificate](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/tree/master/1-Call-MSGraph#optional-use-the-automation-script) U ook profiteren en de [scripts voor het maken van apps](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/master/1-Call-MSGraph/AppCreationScripts-withCert/AppCreationScripts.md) gebruiken om de certificaten te maken, de duimafdruk te berekenen, enzovoort.
