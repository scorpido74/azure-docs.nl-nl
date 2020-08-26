---
title: Certificaat referenties voor micro soft-identiteits platform
titleSuffix: Microsoft identity platform
description: In dit artikel worden de registratie en het gebruik van certificaat referenties voor toepassings verificatie beschreven.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 08/12/2020
ms.author: hirsin
ms.reviewer: nacanuma, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 6330621aac78d5e9df52f2cd3ad9c3968bb0120d
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/25/2020
ms.locfileid: "88853386"
---
# <a name="microsoft-identity-platform-application-authentication-certificate-credentials"></a>Referenties voor verificatie certificaat voor micro soft-identiteits platform

Met het micro soft Identity-platform kan een toepassing eigen referenties voor verificatie gebruiken, bijvoorbeeld in de OAuth 2,0-  [client referenties toekennings](v2-oauth2-client-creds-grant-flow.md) stroom en de OBO-stroom ( [on-of](v2-oauth2-on-behalf-of-flow.md) --).

Een van de referenties die een toepassing voor verificatie kan gebruiken is een [JSON Web token](./security-tokens.md#json-web-tokens-jwts-and-claims) (JWT)-verklaring die is ondertekend met een certificaat waarvan de toepassing eigenaar is.

## <a name="assertion-format"></a>Bevestigings indeling

Als u de bewering wilt berekenen, kunt u een van de vele JWT-bibliotheken gebruiken in de taal van uw keuze. De informatie wordt uitgevoerd door het token in de [header](#header), [claims](#claims-payload)en [hand tekening](#signature).

### <a name="header"></a>Koptekst

| Parameter |  Opmerkingen |
| --- | --- |
| `alg` | Moet **RS256** zijn |
| `typ` | Moet **JWT** zijn |
| `x5t` | De X. 509-certificaat-hash (ook wel bekend als de SHA-1- *vinger afdruk*van het certificaat) hexadecimale weer gave die is gecodeerd als een base64-teken reeks waarde. Als u bijvoorbeeld een X. 509-certificaat-hash van `84E05C1D98BCE3A5421D225B140B36E86A3D5534` (hex) hebt opgegeven, zou dit een `x5t` claim zijn `hOBcHZi846VCHSJbFAs26Go9VTQ=` . |

### <a name="claims-payload"></a>Claims (Payload)

| Parameter |  Opmerkingen |
| --- | --- |
| `aud` | Doel groep: moet `https://login.microsoftonline.com/<your-tenant-id>/oauth2/token` |
| `exp` | Verval datum: de datum waarop het token verloopt. De tijd wordt weer gegeven als het aantal seconden van 1 januari 1970 (1970-01-01T0:0: 0Z) UTC tot het moment dat de geldigheid van het token verloopt. We raden u aan om een korte verloop tijd-10 minuten tot een uur te gebruiken.|
| `iss` | Verlener: moet de client_id (client-*id)* van de client service zijn. |
| `jti` | GUID: de JWT-ID |
| `nbf` | Niet vóór: de datum waarop het token niet kan worden gebruikt. De tijd wordt weer gegeven als het aantal seconden van 1 januari 1970 (1970-01-01T0:0: 0Z) UTC tot het moment waarop de bevestiging is gemaakt. |
| `sub` | Onderwerp: als voor `iss` , moet de client_id (*client-id)* van de client service zijn. |

### <a name="signature"></a>Handtekening

De hand tekening wordt berekend door het certificaat toe te passen zoals beschreven in de [RFC7519-specificatie van JSON Web token](https://tools.ietf.org/html/rfc7519).

## <a name="example-of-a-decoded-jwt-assertion"></a>Voor beeld van een gedecodeerde JWT-bevestiging

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

## <a name="example-of-an-encoded-jwt-assertion"></a>Voor beeld van een gecodeerde JWT-bevestiging

De volgende teken reeks is een voor beeld van een gecodeerde bevestiging. Als u er voorzichtig van gaat, ziet u drie secties, gescheiden door punten ( `.` ):

* De eerste sectie codeert de *header*
* In het tweede gedeelte worden de *claims* (Payload) gecodeerd
* De laatste sectie is de *hand tekening* die wordt berekend met de certificaten uit de inhoud van de eerste twee secties

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

- `$base64Thumbprint` -Met base64 gecodeerde waarde van de certificaat-hash
- `$base64Value` -Base64-gecodeerde waarde van de onbewerkte gegevens van het certificaat

U moet ook een GUID opgeven om de sleutel te identificeren in het manifest van de toepassing ( `$keyId` ).

In de registratie van de Azure-app voor de client toepassing:
1. Selecteer **manifest** om het toepassings manifest te openen.
2. Vervang de eigenschap voor kenmerk *referenties* door uw nieuwe certificaat gegevens met behulp van het volgende schema.

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
3. Sla de wijzigingen op in het manifest van de toepassing en upload het manifest naar het micro soft Identity-platform.

   De `keyCredentials` eigenschap is meerdere waarden, dus u kunt meerdere certificaten uploaden voor uitgebreid sleutel beheer.

## <a name="next-steps"></a>Volgende stappen

De [.net core daemon-console toepassing met behulp van het micro soft Identity platform](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) -code voorbeeld op github laat zien hoe een toepassing zijn eigen referenties voor verificatie gebruikt. Ook wordt uitgelegd hoe u [een zelfondertekend certificaat kunt maken](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/tree/master/1-Call-MSGraph#optional-use-the-automation-script) met behulp van de `New-SelfSignedCertificate` Power shell-cmdlet. U kunt ook de scripts voor het [maken van apps](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/master/1-Call-MSGraph/AppCreationScripts-withCert/AppCreationScripts.md) in het voor beeld-opslag plaats gebruiken om certificaten te maken, de vinger afdruk te berekenen, enzovoort.
