---
title: Daemon-app die web-Api's aanroept (app-configuratie)-micro soft Identity-platform
description: Meer informatie over het bouwen van een daemon-app die web-Api's aanroept (app-configuratie)
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
ms.date: 09/15/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 394137a1b7901a3272e36f6a6d74944b87f30082
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71056493"
---
# <a name="daemon-app-that-calls-web-apis---code-configuration"></a>Daemon-app die web-Api's aanroept-code configuratie

Meer informatie over het configureren van de code voor uw daemon-toepassing die web-Api's aanroept.

## <a name="msal-libraries-supporting-daemon-apps"></a>MSAL-bibliotheken ondersteunen daemon-apps

De micro soft libraries ondersteunen daemon-apps zijn:

  MSAL-bibliotheek | Description
  ------------ | ----------
  ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Ondersteunde platforms voor het bouwen van een daemon-toepassing zijn .NET Framework en .NET Core-platformen (niet UWP, Xamarin. iOS en Xamarin. Android als deze platformen worden gebruikt voor het bouwen van open bare client toepassingen)
  ![Python](media/sample-v2-code/logo_python.png) <br/> MSAL.Python | Ontwikkeling in uitvoering-in open bare preview
  ![Java](media/sample-v2-code/logo_java.png) <br/> MSAL.Java | Ontwikkeling in uitvoering-in open bare preview

## <a name="configuration-of-the-authority"></a>Configuratie van de instantie

Omdat de daemon-toepassingen geen gedelegeerde machtigingen gebruiken, maar toepassings machtigingen, worden het *ondersteunde account type* niet *accounts in een organisatorische map en persoonlijke micro soft-accounts (bijvoorbeeld Skype, Xbox, Outlook.com)* . Er is inderdaad geen Tenant beheerder om toestemming te verlenen voor de daemon-toepassing voor persoonlijke micro soft-accounts. U moet *accounts in mijn organisatie* of *accounts in een organisatie*kiezen.

Daarom moet de in de toepassings configuratie opgegeven instantie Tenant-ED zijn (een Tenant-ID of een domein naam opgeven die is gekoppeld aan uw organisatie).

Als u een ISV bent en een multi tenant hulp programma wilt bieden, kunt u gebruiken `organizations`. Maar houd er rekening mee dat u ook moet uitleggen wat uw klanten zijn om toestemming van de beheerder te verlenen. Zie [toestemming vragen voor een volledige Tenant](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant) voor meer informatie. Er is op dit moment een beperking in MSAL `organizations` : is alleen toegestaan wanneer de client referenties een toepassings geheim zijn (niet een certificaat).

## <a name="application-configuration-and-instantiation"></a>Toepassings configuratie en instantiëring

In MSAL-bibliotheken worden de client referenties (geheim of certificaat) door gegeven als para meter van de client toepassings constructie.

> [!IMPORTANT]
> Zelfs als uw toepassing een console toepassing is die als een service wordt uitgevoerd, en als het een daemon-toepassing is, moet deze een vertrouwelijke client toepassing zijn.

### <a name="configuration-file"></a>Configuratiebestand

Het configuratie bestand definieert het volgende:

- de instantie of het Cloud exemplaar en tenantId
- de ClientID die u hebt ontvangen van de registratie van de toepassing
- een client geheim of een certificaat
 
# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

[appSettings. json](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/master/1-Call-MSGraph/daemon-console/appsettings.json) van het voor beeld van de [.net Core-Console-daemon](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) .

```JSon
{
  "Instance": "https://login.microsoftonline.com/{0}",
  "Tenant": "[Enter here the tenantID or domain name for your Azure AD tenant]",
  "ClientId": "[Enter here the ClientId for your application]",
  "ClientSecret": "[Enter here a client secret for your application]",
  "CertificateName": "[Or instead of client secret: Enter here the name of a certificate (from the user cert store) as registered with your application]"
}
```

U geeft een clientSecret of een certificaatnaam op. Beide instellingen zijn exclusief.

# <a name="pythontabpython"></a>[Python](#tab/python)

Wanneer u een vertrouwelijke client met client geheimen bouwt, zou het configuratie bestand als deze uittreksel van het voor beeld [confidential_client_secret_sample. py # N4-L15](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/0.6.1/sample/confidential_client_secret_sample.py#L4-L15) in github zijn.

```Json
{
    "authority": "https://login.microsoftonline.com/organizations",
    "client_id": "your_client_id",
    "scope": ["https://graph.microsoft.com/.default"],
    "secret": "The secret generated by AAD during your confidential app registration"
}
```

Wanneer u een vertrouwelijke client met certificaten bouwt, zal deze als uittreksel van het voor beeld [confidential_client_certificate_sample. py # N4-L15](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/4b34fd660e5574eb876cfba63a1b927ae375efd6/sample/confidential_client_certificate_sample.py#L4-L15) in github zijn.

```Json
{
    "authority": "https://login.microsoftonline.com/organizations",
    "client_id": "your_client_id",
    "scope": ["https://graph.microsoft.com/.default"],
    "thumbprint": "790E... The thumbprint generated by AAD when you upload your public cert",
    "private_key_file": "filename.pem"
}
```

# <a name="javatabjava"></a>[Java](#tab/java)

Dit is de klasse die wordt gebruikt in msal4j dev-voor beelden om de voor beelden te configureren: [TestData](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/TestData.java).

```Java
public class TestData {

    final static String TENANT_SPECIFIC_AUTHORITY = "https://login.microsoftonline.com/<TenantId>/";
    final static String GRAPH_DEFAULT_SCOPE = "https://graph.microsoft.com/.default";
    final static String CONFIDENTIAL_CLIENT_ID = "";
    final static String CONFIDENTIAL_CLIENT_SECRET = "";
}
```

U geeft een CONFIDENTIAL_CLIENT_ID of een CONFIDENTIAL_CLIENT_SECRET op. Beide instellingen zijn exclusief.

---

### <a name="instantiation-of-the-msal-application"></a>Instantiëring van de MSAL-toepassing

Als u de MSAL-toepassing wilt instantiëren, moet u het volgende doen:

- Voeg het MSAL-pakket (afhankelijk van de taal) toe, of referentie of importeer het.
- De bouw verschilt, afhankelijk van of u client geheimen of certificaten gebruikt (of, als een geavanceerd scenario, ondertekende beweringen)

De daemon-toepassing wordt weer gegeven door een`IConfidentialClientApplication`

#### <a name="reference-the-package"></a>Verwijzen naar het pakket

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

Voeg het [micro soft. IdentityClient](https://www.nuget.org/packages/Microsoft.Identity.Client) NuGet-pakket toe aan uw toepassing.
In MSAL.net wordt de vertrouwelijke client toepassing vertegenwoordigd door de `IConfidentialClientApplication` interface.
MSAL.NET-naam ruimte in de bron code gebruiken

```CSharp
using Microsoft.Identity.Client;
IConfidentialClientApplication app;
```

# <a name="pythontabpython"></a>[Python](#tab/python)

```python
import msal
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
import com.microsoft.aad.msal4j.ClientCredentialFactory;
import com.microsoft.aad.msal4j.ClientCredentialParameters;
import com.microsoft.aad.msal4j.ConfidentialClientApplication;
import com.microsoft.aad.msal4j.IAuthenticationResult;
```

---

#### <a name="instantiate-the-confidential-client-application-with-client-secrets"></a>De vertrouwelijke client toepassing instantiëren met client geheimen

Dit is de code voor het instantiëren van de vertrouwelijke client toepassing met een client geheim:

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

```CSharp
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
           .WithClientSecret(config.ClientSecret)
           .WithAuthority(new Uri(config.Authority))
           .Build();
```

# <a name="pythontabpython"></a>[Python](#tab/python)

```Python
config = json.load(open(sys.argv[1]))

# Create a preferably long-lived app instance which maintains a token cache.
app = msal.ConfidentialClientApplication(
    config["client_id"], authority=config["authority"],
    client_credential=config["secret"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )
```

# <a name="javatabjava"></a>[Java](#tab/java)

```Java
ConfidentialClientApplication app = ConfidentialClientApplication.builder(
        TestData.CONFIDENTIAL_CLIENT_ID,
        ClientCredentialFactory.create(TestData.CONFIDENTIAL_CLIENT_SECRET))
        .authority(TestData.TENANT_SPECIFIC_AUTHORITY)
        .build();
```

---

#### <a name="instantiate-the-confidential-client-application-with-client-certificate"></a>De vertrouwelijke client toepassing instantiëren met het client certificaat

Hier volgt de code voor het bouwen van een toepassing met een certificaat:

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

```CSharp
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
    .WithCertificate(certificate)
    .WithAuthority(new Uri(config.Authority))
    .Build();
```

# <a name="pythontabpython"></a>[Python](#tab/python)

```Python
config = json.load(open(sys.argv[1]))

# Create a preferably long-lived app instance which maintains a token cache.
app = msal.ConfidentialClientApplication(
    config["client_id"], authority=config["authority"],
    client_credential={"thumbprint": config["thumbprint"], "private_key": open(config['private_key_file']).read()},
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )
```

# <a name="javatabjava"></a>[Java](#tab/java)

In MSAL. Java: er zijn twee Builders voor het instantiëren van de vertrouwelijke client toepassing met certificaten.

```Java

InputStream pkcs12Certificate = ... ; /* containing PCKS12 formatted certificate*/
string certificatePassword = ... ;    /* contains the password to access the certificate */

ConfidentialClientApplication app = ConfidentialClientApplication.builder(
        TestData.CONFIDENTIAL_CLIENT_ID,
        ClientCredentialFactory.create(pkcs12Certificate, certificatePassword))
        .authority(TestData.TENANT_SPECIFIC_AUTHORITY)
        .build();
```

of

```Java
PrivateKey key = getPrivateKey(); /* RSA private key to sign the assertion */
X509Certificate publicCertificate = getPublicCertificate(); /* x509 public certificate used as a thumbprint */

ConfidentialClientApplication app = ConfidentialClientApplication.builder(
        TestData.CONFIDENTIAL_CLIENT_ID,
        ClientCredentialFactory.create(rsaPrivateKey, publicKeyCertificate))
        .authority(TestData.TENANT_SPECIFIC_AUTHORITY)
        .build();
```

---

#### <a name="advanced-scenario---instantiate-the-confidential-client-application-with-client-assertions"></a>Geavanceerd scenario: de vertrouwelijke client toepassing instantiëren met client verklaringen

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

In plaats van een client geheim of een certificaat kan de vertrouwelijke client toepassing ook de identiteit bewijzen met behulp van client verklaringen.

MSAL.NET heeft twee methoden om ondertekende bevestigingen te bieden aan de vertrouwelijke client-app:

- `.WithClientAssertion()`
- `.WithClientClaims()`

Wanneer u gebruikt `WithClientAssertion`, moet u een ondertekende JWT opgeven. Dit geavanceerde scenario wordt beschreven in [client verklaringen](msal-net-client-assertions.md)

```CSharp
string signedClientAssertion = ComputeAssertion();
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithClientAssertion(signedClientAssertion)
                                          .Build();
```

Wanneer u gebruikt `WithClientClaims`, bewaart MSAL.net zichzelf een ondertekende bevestiging met de claims die worden verwacht door Azure AD plus aanvullende client claims die u wilt verzenden.
Hier volgt een code fragment waarmee u dit kunt doen:

```CSharp
string ipAddress = "192.168.1.2";
var claims = new Dictionary<string, string> { { "client_ip", ipAddress } };
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithAuthority(new Uri(config.Authority))
                                          .WithClientClaims(certificate, claims)
                                          .Build();```
```

Zie voor meer informatie [client verklaringen](msal-net-client-assertions.md).

# <a name="pythontabpython"></a>[Python](#tab/python)

In MSAL python kunt u client claims opgeven met behulp van de claims die worden ondertekend door `ConfidentialClientApplication`de persoonlijke sleutel van deze persoon.

```Python
config = json.load(open(sys.argv[1]))

# Create a preferably long-lived app instance which maintains a token cache.
app = msal.ConfidentialClientApplication(
    config["client_id"], authority=config["authority"],
    client_credential={"thumbprint": config["thumbprint"], "private_key": open(config['private_key_file']).read()},
    client_claims = {“client_ip”: “x.x.x.x”}
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )
```

Zie MSAL voor meer informatie. Referentie documentatie voor python voor [ConfidentialClientApplication](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.__init__)

# <a name="javatabjava"></a>[Java](#tab/java)

msal4j is beschikbaar in de open bare preview. Ondertekende verklaringen worden nog niet ondersteund

---

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Daemon-app-tokens ophalen voor de app](./scenario-daemon-acquire-token.md)
