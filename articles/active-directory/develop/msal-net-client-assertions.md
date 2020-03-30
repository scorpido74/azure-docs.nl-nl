---
title: Beweringen van de cliënt (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over de ondersteuning van ondertekende clientbeweringen voor vertrouwelijke clienttoepassingen in de Microsoft-verificatiebibliotheek voor .NET (MSAL.NET).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/18/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 8c97387bfd2a362d3bf5a6b8a3252242f061da31
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050292"
---
# <a name="confidential-client-assertions"></a>Vertrouwelijke beweringen van klanten

Om hun identiteit te bewijzen, wisselen vertrouwelijke clienttoepassingen een geheim uit met Azure AD. Het geheim kan zijn:
- Een clientgeheim (toepassingswachtwoord).
- Een certificaat, dat wordt gebruikt om een ondertekende bewering met standaardclaims op te bouwen.

Dit geheim kan ook direct een ondertekende bewering zijn.

MSAL.NET heeft vier methoden om referenties of beweringen te verstrekken aan de vertrouwelijke client-app:
- `.WithClientSecret()`
- `.WithCertificate()`
- `.WithClientAssertion()`
- `.WithClientClaims()`

> [!NOTE]
> Hoewel het mogelijk is `WithClientAssertion()` om de API te gebruiken om tokens voor de vertrouwelijke client te verkrijgen, raden we het niet aan om deze standaard te gebruiken omdat het geavanceerder is en is ontworpen om zeer specifieke scenario's te verwerken die niet gebruikelijk zijn. Met `.WithCertificate()` behulp van de API kunnen MSAL.NET dit voor u afhandelen. Deze api biedt u de mogelijkheid om uw verificatieverzoek `.WithCertificate()` aan te passen indien nodig, maar de standaardbewering die is gemaakt door volstaat voor de meeste verificatiescenario's. Deze API kan ook worden gebruikt als tijdelijke oplossing in sommige scenario's waarin MSAL.NET de ondertekeningsbewerking intern niet uitvoert.

### <a name="signed-assertions"></a>Ondertekende beweringen

Een ondertekende clientbewering vindt de vorm aan van een ondertekende JWT met de payload met de vereiste verificatieclaims in opdracht van Azure AD, Base64 gecodeerd. U gebruikt deze als volgt:

```csharp
string signedClientAssertion = ComputeAssertion();
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithClientAssertion(signedClientAssertion)
                                          .Build();
```

De claims die worden verwacht door Azure AD zijn:

Claimtype | Waarde | Beschrijving
---------- | ---------- | ----------
aud | `https://login.microsoftonline.com/{tenantId}/v2.0` | De claim 'aud' (doelgroep) identificeert de ontvangers waarvoor de JWT is bedoeld (hier Azure AD) Zie [RFC 7519, sectie 4.1.3]
Exp | Do 27 juni 2019 15:04:17 GMT+0200 (Romance Daylight Time) | De "exp" (vervaldatum) claim identificeert de vervaldatum op of waarna de JWT NIET mag worden geaccepteerd voor verwerking. Zie [RFC 7519, punt 4.1.4]
Iss | {ClientID} | De claim "iss" (emittent) identificeert het hoofdsom dat de JWT heeft uitgegeven. De verwerking van deze claim is toepassingsspecifiek. De waarde "iss" is een hoofdlettergevoelige tekenreeks met een StringOrURI-waarde. [RFC 7519, punt 4.1.1]
Gti | (a Guid) | De "jti" (JWT ID) claim biedt een unieke id voor de JWT. De id-waarde moet worden toegewezen op een manier die ervoor zorgt dat er een verwaarloosbare kans is dat dezelfde waarde per ongeluk aan een ander gegevensobject wordt toegewezen; als de toepassing meerdere emittenten gebruikt, moeten botsingen worden voorkomen tussen waarden die door verschillende emittenten worden geproduceerd. De "jti" claim kan worden gebruikt om te voorkomen dat de JWT wordt overspeeld. De "jti"-waarde is een hoofdlettergevoelige tekenreeks. [RFC 7519, punt 4.1.7]
nbf (nbf) | Do 27 juni 2019 14:54:17 GMT+0200 (Romance Daylight Time) | De "nbf" (niet eerder) claim identificeert de tijd vóór welke de JWT niet mag worden aanvaard voor verwerking. [RFC 7519, punt 4.1.5]
sub | {ClientID} | De "sub" (onderwerp) claim identificeert het onderwerp van de JWT. De vorderingen in een JWT zijn normaal gesproken verklaringen over het onderwerp. De onderwerpwaarde moet lokaal uniek zijn in de context van de emittent of wereldwijd uniek zijn. De See [RFC 7519, punt 4.1.2]

Hier is een voorbeeld van hoe deze claims ambachtelijke:

```csharp
private static IDictionary<string, string> GetClaims()
{
      //aud = https://login.microsoftonline.com/ + Tenant ID + /v2.0
      string aud = $"https://login.microsoftonline.com/{tenantId}/v2.0";

      string ConfidentialClientID = "00000000-0000-0000-0000-000000000000" //client id
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

Hier is hoe maak je een ondertekende client bewering ambachtelijke:

```csharp
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

string GetSignedClientAssertion()
{
    //Signing with SHA-256
    string rsaSha256Signature = "http://www.w3.org/2001/04/xmldsig-more#rsa-sha256";
     X509Certificate2 certificate = new X509Certificate2("Certificate.pfx", "Password", X509KeyStorageFlags.EphemeralKeySet);

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
    string signedClientAssertion = string.Concat(token, ".", signature);
    return signedClientAssertion;
}
```

### <a name="alternative-method"></a>Alternatieve methode

U hebt ook de mogelijkheid om [Microsoft.IdentityModel.JsonWebTokens](https://www.nuget.org/packages/Microsoft.IdentityModel.JsonWebTokens/) te gebruiken om de bewering voor u te maken. De code zal een meer elegante zoals weergegeven in het onderstaande voorbeeld:

```csharp
        string GetSignedClientAssertion()
        {
            var cert = new X509Certificate2("Certificate.pfx", "Password", X509KeyStorageFlags.EphemeralKeySet);

            //aud = https://login.microsoftonline.com/ + Tenant ID + /v2.0
            string aud = $"https://login.microsoftonline.com/{tenantID}/v2.0";

            // client_id
            string confidentialClientID = "00000000-0000-0000-0000-000000000000";

            // no need to add exp, nbf as JsonWebTokenHandler will add them by default.
            var claims = new Dictionary<string, object>()
            {
                { "aud", aud },
                { "iss", confidentialClientID },
                { "jti", Guid.NewGuid().ToString() },
                { "sub", confidentialClientID }
            };

            var securityTokenDescriptor = new SecurityTokenDescriptor
            {
                Claims = claims,
                SigningCredentials = new X509SigningCredentials(cert)
            };

            var handler = new JsonWebTokenHandler();
            var signedClientAssertion = handler.CreateToken(securityTokenDescriptor);
        }
```

Zodra u uw ondertekende client bewering, u deze gebruiken met de MSAL apis zoals hieronder weergegeven.

```csharp
            string signedClientAssertion = GetSignedClientAssertion();

            var confidentialApp = ConfidentialClientApplicationBuilder
                .Create(ConfidentialClientID)
                .WithClientAssertion(signedClientAssertion)
                .Build();
```

### <a name="withclientclaims"></a>WithClientClaims

`WithClientClaims(X509Certificate2 certificate, IDictionary<string, string> claimsToSign, bool mergeWithDefaultClaims = true)`standaard wordt een ondertekende bewering weergegeven met de claims die worden verwacht door Azure AD plus aanvullende clientclaims die u wilt verzenden. Hier is een code fragment over hoe dat te doen.

```csharp
string ipAddress = "192.168.1.2";
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithAuthority(new Uri(config.Authority))
                                          .WithClientClaims(certificate, 
                                                                      new Dictionary<string, string> { { "client_ip", ipAddress } })
                                          .Build();

```

Als een van de claims in het woordenboek die u invoert hetzelfde is als een van de verplichte claims, wordt rekening gehouden met de waarde van de aanvullende claim. Het zal de claims die door MSAL.NET worden berekend, overschrijven.

Als u uw eigen claims wilt indienen, inclusief de verplichte `false` claims `mergeWithDefaultClaims` die door Azure AD worden verwacht, wordt de parameter ingediend.
