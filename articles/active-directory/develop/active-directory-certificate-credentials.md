---
title: Certificaat referenties voor micro soft-identiteits platform
titleSuffix: Microsoft identity platform
description: In dit artikel worden de registratie en het gebruik van certificaat referenties voor toepassings verificatie beschreven.
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: 88f0c64a-25f7-4974-aca2-2acadc9acbd8
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: ryanwi
ms.reviewer: nacanuma, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 7b42676fa387914bc4825e2850b3d2f032827a79
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/02/2020
ms.locfileid: "76962115"
---
# <a name="microsoft-identity-platform-application-authentication-certificate-credentials"></a>Referenties voor verificatie certificaat voor micro soft-identiteits platform

Met micro soft Identity platform kan een toepassing eigen referenties voor authenticatie gebruiken, bijvoorbeeld in de [OAuth 2,0-client referenties Grant flowv 2.0](v2-oauth2-client-creds-grant-flow.md) en de namens [-stroom](v2-oauth2-on-behalf-of-flow.md).

Een van de referenties die een toepassing voor verificatie kan gebruiken is een JSON Web Token (JWT)-verklaring die is ondertekend met een certificaat waarvan de toepassing eigenaar is.

## <a name="assertion-format"></a>Bevestigings indeling
Micro soft Identity platform om de bewering te berekenen, kunt u een van de vele [JSON Web token](https://jwt.ms/) bibliotheken gebruiken in de taal van uw keuze. De gegevens die door het token worden uitgevoerd, zijn als volgt:

### <a name="header"></a>Header

| Parameter |  Opmerkingen |
| --- | --- |
| `alg` | Moet **RS256** zijn |
| `typ` | Moet **JWT** zijn |
| `x5t` | Moet de X. 509-certificaat-SHA-1-vinger afdruk zijn |

### <a name="claims-payload"></a>Claims (Payload)

| Parameter |  Opmerkingen |
| --- | --- |
| `aud` | Doel groep: moet **https://login.microsoftonline.com/*tenant_Id*/oauth2/token** |
| `exp` | Verval datum: de datum waarop het token verloopt. De tijd wordt weer gegeven als het aantal seconden van 1 januari 1970 (1970-01-01T0:0: 0Z) UTC tot het moment dat de geldigheid van het token verloopt.|
| `iss` | Verlener: moet de client_id (toepassings-ID van de client service) |
| `jti` | GUID: de JWT-ID |
| `nbf` | Niet vóór: de datum waarop het token niet kan worden gebruikt. De tijd wordt weer gegeven als het aantal seconden van 1 januari 1970 (1970-01-01T0:0: 0Z) UTC tot het moment dat het token is uitgegeven. |
| `sub` | Onderwerp: als voor `iss`, moet de client_id (toepassings-ID van de client service) |

### <a name="signature"></a>Handtekening

De hand tekening wordt berekend met het Toep assen van het certificaat, zoals beschreven in de [JSON Web token RFC7519-specificatie](https://tools.ietf.org/html/rfc7519)

## <a name="example-of-a-decoded-jwt-assertion"></a>Voor beeld van een gedecodeerde JWT-bevestiging

```
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

## <a name="example-of-an-encoded-jwt-assertion"></a>Voor beeld van een gecodeerde JWT-bevestiging

De volgende teken reeks is een voor beeld van een gecodeerde bevestiging. Als u er voorzichtig van gaat, ziet u drie secties, gescheiden door punten (.):
* De eerste sectie codeert de header
* In het tweede gedeelte wordt de payload gecodeerd
* De laatste sectie is de hand tekening die wordt berekend met de certificaten uit de inhoud van de eerste twee secties

```
"eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJhdWQiOiJodHRwczpcL1wvbG9naW4ubWljcm9zb2Z0b25saW5lLmNvbVwvam1wcmlldXJob3RtYWlsLm9ubWljcm9zb2Z0LmNvbVwvb2F1dGgyXC90b2tlbiIsImV4cCI6MTQ4NDU5MzM0MSwiaXNzIjoiOTdlMGE1YjctZDc0NS00MGI2LTk0ZmUtNWY3N2QzNWM2ZTA1IiwianRpIjoiMjJiM2JiMjYtZTA0Ni00MmRmLTljOTYtNjVkYmQ3MmMxYzgxIiwibmJmIjoxNDg0NTkyNzQxLCJzdWIiOiI5N2UwYTViNy1kNzQ1LTQwYjYtOTRmZS01Zjc3ZDM1YzZlMDUifQ.
Gh95kHCOEGq5E_ArMBbDXhwKR577scxYaoJ1P{a lot of characters here}KKJDEg"
```

## <a name="register-your-certificate-with-microsoft-identity-platform"></a>Uw certificaat registreren bij micro soft Identity platform

U kunt de certificaat referentie koppelen aan de client toepassing in het micro soft Identity-platform via de Azure Portal een van de volgende methoden gebruiken:

### <a name="uploading-the-certificate-file"></a>Het certificaat bestand uploaden

In de registratie van de Azure-app voor de client toepassing:
1. Selecteer **certificaten & geheimen**. 
2. Klik op **certificaat uploaden** en selecteer het certificaat bestand dat u wilt uploaden.
3. Klik op **Add**.
  Zodra het certificaat is geüpload, worden de vinger afdruk, de start datum en de verval waarden weer gegeven. 

### <a name="updating-the-application-manifest"></a>Het toepassings manifest bijwerken

Als u een certificaat hebt, moet u het volgende berekenen:

- `$base64Thumbprint`, dit is de base64-code ring van de certificaat-hash
- `$base64Value`, dit is de base64-code ring van de onbewerkte gegevens van het certificaat

U moet ook een GUID opgeven om de sleutel te identificeren in het manifest van de toepassing (`$keyId`).

In de registratie van de Azure-app voor de client toepassing:
1. Selecteer **manifest** om het toepassings manifest te openen.
2. Vervang de eigenschap voor kenmerk *referenties* door uw nieuwe certificaat gegevens met behulp van het volgende schema.

   ```
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
3. Sla de wijzigingen op in het manifest van de toepassing en upload het manifest naar het micro soft Identity-platform. 

   De eigenschap `keyCredentials` heeft meerdere waarden, dus u kunt meerdere certificaten uploaden voor uitgebreid sleutel beheer.
   
## <a name="code-sample"></a>Codevoorbeeld

> [!NOTE]
> U moet de X5T-header berekenen door deze te converteren naar een basis teken reeks van 64 met de hash van het certificaat. De code waarmee dit C# kan worden uitgevoerd, is: `System.Convert.ToBase64String(cert.GetCertHash());`

Het code voorbeeld voor het [verifiëren van micro soft Identity platform in daemon-apps met certificaten](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential) laat zien hoe een toepassing eigen referenties voor verificatie gebruikt. Ook wordt uitgelegd hoe u [een zelfondertekend certificaat kunt maken](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential#create-a-self-signed-certificate) met behulp van de `New-SelfSignedCertificate` Power shell-opdracht. U kunt ook gebruikmaken van de scripts voor het maken van de [app](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential/blob/master/AppCreationScripts/AppCreationScripts.md) voor het maken van de certificaten, het berekenen van de vinger afdruk, enzovoort.
