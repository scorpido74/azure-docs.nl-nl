---
title: Client verklaringen in micro soft-verificatie bibliotheek voor .NET
titleSuffix: Microsoft identity platform
description: Meer informatie over ondertekende client verklaringen ondersteuning voor vertrouwelijke client toepassingen in micro soft Authentication Library voor .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/16/2019
ms.author: jmprieur
ms.reviewer: ''
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: fcf11ac8dc39dcb1d70b932dbe870687f5446a52
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72802846"
---
# <a name="confidential-client-assertions"></a>Verklaringen van vertrouwelijke client
Om hun identiteit te bewijzen, wisselen vertrouwelijke client toepassingen een geheim uit met Azure AD. Het geheim kan het volgende zijn:
- Een client geheim (toepassings wachtwoord).
- Een certificaat dat wordt gebruikt voor het bouwen van een ondertekende bevestiging met standaard claims.

Dit geheim kan ook rechtstreeks een ondertekende bevestiging zijn.

MSAL.NET heeft vier methoden om referenties of beweringen te verstrekken aan de vertrouwelijke client-app:
- `.WithClientSecret()`
- `.WithCertificate()`
- `.WithClientAssertion()`
- `.WithClientClaims()`

### <a name="signed-assertions"></a>Ondertekende verklaringen

Een ondertekende client bevestiging gaat in de vorm van een ondertekende JWT met de nettolading die de vereiste verificatie claims bevat die zijn voorgeschreven door Azure AD, base64-gecodeerd. U gebruikt deze als volgt:

```CSharp
string signedClientAssertion = ComputeAssertion();
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithClientAssertion(signedClientAssertion)
                                          .Build();
```

De claims die worden verwacht door Azure AD zijn:

Claim type | Waarde | Beschrijving
---------- | ---------- | ----------
AUD | https://login.microsoftonline.com/{tenantId}/v2.0 | De claim ' AUD ' (doel groep) identificeert de ontvangers waarvoor de JWT is bedoeld (hier Azure AD) Zie [RFC 7519, sectie 4.1.3]
Geldig | Do jun 27 2019 15:04:17 GMT + 0200 (Romaanse zomer tijd) | De claim ' exp ' (verval tijd) identificeert de verval tijd op of waarna de JWT niet moet worden geaccepteerd voor verwerking. Zie [RFC 7519, sectie 4.1.4]
ISS | ClientID | De claim ' ISS ' (verlener) identificeert de principal die de JWT heeft uitgegeven. De verwerking van deze claim is toepassingsspecifiek. De "ISS"-waarde is een hoofdletter gevoelige teken reeks met een StringOrURI-waarde. [RFC 7519, sectie 4.1.1]
jti | (een GUID) | De claim ' JTI ' (JWT-ID) biedt een unieke id voor de JWT. De id-waarde moet worden toegewezen op een manier die ervoor zorgt dat er een Verwaarloos bare kans is dat dezelfde waarde per ongeluk wordt toegewezen aan een ander gegevens object. Als de toepassing meerdere verleners gebruikt, moeten conflicten worden voor komen tussen waarden die door verschillende verleners worden geproduceerd. De claim ' JTI ' kan worden gebruikt om te voor komen dat de JWT opnieuw wordt afgespeeld. De waarde ' JTI ' is een hoofdletter gevoelige teken reeks. [RFC 7519, sectie 4.1.7]
NBF | Do jun 27 2019 14:54:17 GMT + 0200 (Romaanse zomer tijd) | De claim ' NBF ' (niet vóór) identificeert de tijd waarna de JWT niet moet worden geaccepteerd voor verwerking. [RFC 7519, sectie 4.1.5]
chronische | ClientID | De claim ' sub ' (subject) identificeert het onderwerp van de JWT. De claims in een JWT hebben doorgaans instructies over het onderwerp. De onderwerpwaarde moet worden ingesteld op lokaal uniek in de context van de verlener of wereld wijd uniek zijn. De Zie [RFC 7519, rubriek 4.1.2]

Hier volgt een voor beeld van het aanwijzen van deze claims:

```CSharp
private static IDictionary<string, string> GetClaims()
{
      //aud = https://login.microsoftonline.com/ + Tenant ID + /v2.0
      string aud = "https://login.microsoftonline.com/72f988bf-86f1-41af-hd4m-2d7cd011db47/v2.0";

      string ConfidentialClientID = "61dab2ba-145d-4b1b-8569-bf4b9aed5dhb" //client id
      const uint JwtToAadLifetimeInSeconds = 60 * 10; // Ten minutes
      DateTime validFrom = DateTime.UtcNow;
      var nbf = ConvertToTimeT(validFrom);
      var exp = ConvertToTimeT(validFrom + TimeSpan.FromSeconds(JwtToAadLifetimeInSeconds));

      return new Dictionary<string, string>()
           {
                { "aud", aud },
                { "exp", exp.ToString() },
                { "iss", ConfidentialClientID },
                { "jti", Guid.NewGuid().ToString() },
                { "nbf", nbf.ToString() },
                { "sub", ConfidentialClientID }
            };
}
```

U gaat als volgt te werk om een ondertekende client bevestiging te bevestigen:

```CSharp
string Encode(byte[] arg)
{
    char Base64PadCharacter = '=';
    char Base64Character62 = '+';
    char Base64Character63 = '/';
    char Base64UrlCharacter62 = '-';
    char Base64UrlCharacter63 = '_';

    string s = Convert.ToBase64String(arg);
    s = s.Split(Base64PadCharacter)[0]; // RemoveAccount any trailing padding
    s = s.Replace(Base64Character62, Base64UrlCharacter62); // 62nd char of encoding
    s = s.Replace(Base64Character63, Base64UrlCharacter63); // 63rd char of encoding

    return s;
}

string GetAssertion()
{
    //Signing with SHA-256
    string rsaSha256Signature = "http://www.w3.org/2001/04/xmldsig-more#rsa-sha256";
    X509Certificate2 certificate = ReadCertificate(config.CertificateName);

    //Create RSACryptoServiceProvider
    var x509Key = new X509AsymmetricSecurityKey(certificate);
    var privateKeyXmlParams = certificate.PrivateKey.ToXmlString(true);
    var rsa = new RSACryptoServiceProvider();
    rsa.FromXmlString(privateKeyXmlParams);

    //alg represents the desired signing algorithm, which is SHA-256 in this case
    //kid represents the certificate thumbprint
    var header = new Dictionary<string, string>()
         {
              { "alg", "RS256"},
              { "kid", Encode(Certificate.GetCertHash()) }
         };

    //Please see the previous code snippet on how to craft claims for the GetClaims() method
    string token = Encode(Encoding.UTF8.GetBytes(JObject.FromObject(header).ToString())) + "." + Encode(Encoding.UTF8.GetBytes(JObject.FromObject(GetClaims())));

    string signature = Encode(rsa.SignData(Encoding.UTF8.GetBytes(token), new SHA256Cng()));
    string SignedAssertion = string.Concat(token, ".", signature);
    return SignedAssertion;
}
```

### <a name="withclientclaims"></a>WithClientClaims

`WithClientClaims(X509Certificate2 certificate, IDictionary<string, string> claimsToSign, bool mergeWithDefaultClaims = true)` maakt standaard een ondertekende bevestiging met de claims die worden verwacht door Azure AD plus aanvullende client claims die u wilt verzenden. Hier volgt een code fragment waarmee u dit kunt doen.

```CSharp
string ipAddress = "192.168.1.2";
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithAuthority(new Uri(config.Authority))
                                          .WithClientClaims(certificate, 
                                                                      new Dictionary<string, string> { { "client_ip", ipAddress } })
                                          .Build();

```

Als een van de claims in de door u opgegeven woorden lijst hetzelfde is als een van de verplichte claims, wordt de waarde van de extra claim in rekening gebracht. Hiermee worden de claims overschreven die worden berekend door MSAL.NET.

Als u uw eigen claims wilt opgeven, met inbegrip van de verplichte claims die door Azure AD worden verwacht, geeft u `false` door voor de para meter `mergeWithDefaultClaims`.
