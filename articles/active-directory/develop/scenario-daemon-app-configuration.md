---
title: Daemon-apps configureren die web-API's aanroepen - Microsoft-identiteitsplatform | Azure
description: Meer informatie over het configureren van de code voor uw daemon-toepassing die web-API's aanroept (app-configuratie)
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
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: fc441ef64f98ace04b7b847c03d575215656f9db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77611834"
---
# <a name="daemon-app-that-calls-web-apis---code-configuration"></a>Daemon-app die web-API's aanroept - codeconfiguratie

Meer informatie over het configureren van de code voor uw daemon-toepassing die web-API's aanroept.

## <a name="msal-libraries-that-support-daemon-apps"></a>MSAL-bibliotheken die daemon-apps ondersteunen

Deze Microsoft-bibliotheken ondersteunen daemon-apps:

  MSAL-bibliotheek | Beschrijving
  ------------ | ----------
  ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | De .NET Framework- en .NET Core-platforms worden ondersteund voor het bouwen van daemon-toepassingen. (UWP, Xamarin.iOS en Xamarin.Android worden niet ondersteund omdat deze platforms worden gebruikt om openbare clienttoepassingen te bouwen.)
  ![Python](media/sample-v2-code/logo_python.png) <br/> MSAL Python | Ondersteuning voor daemon-toepassingen in Python.
  ![Java](media/sample-v2-code/logo_java.png) <br/> MSAL Java | Ondersteuning voor daemon toepassingen in Java.

## <a name="configure-the-authority"></a>De autoriteit configureren

Daemon-toepassingen gebruiken toepassingsmachtigingen in plaats van gedelegeerde machtigingen. Het ondersteunde accounttype kan dus geen account zijn in een organisatiemap of een persoonlijk Microsoft-account (bijvoorbeeld Skype, Xbox, Outlook.com). Er is geen tenantbeheerder die toestemming verleent voor een daemon-toepassing voor een persoonlijk Microsoft-account. U moet accounts in *mijn organisatie* of accounts in *een organisatie*kiezen.

De instantie die in de toepassingsconfiguratie is opgegeven, moet dus worden getenant (het opgeven van een tenant-id of een domeinnaam die is gekoppeld aan uw organisatie).

Als u een ISV bent en een multitenant-tool `organizations`wilt bieden, u. Maar houd er rekening mee dat u uw klanten ook moet uitleggen hoe u toestemming van de beheerder verlenen. Zie Toestemming [vragen voor een volledige tenant](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant)voor meer informatie. Ook is er momenteel een beperking `organizations` in MSAL: is alleen toegestaan wanneer de clientreferenties een toepassingsgeheim zijn (geen certificaat).

## <a name="configure-and-instantiate-the-application"></a>De toepassing configureren en instantiëren

In MSAL-bibliotheken worden de clientreferenties (geheim of certificaat) doorgegeven als parameter van de vertrouwelijke clienttoepassingsconstructie.

> [!IMPORTANT]
> Zelfs als uw toepassing een consoletoepassing is die als een service wordt uitgevoerd, moet het een vertrouwelijke clienttoepassing zijn als het een daemon-toepassing is.

### <a name="configuration-file"></a>Configuratiebestand

Het configuratiebestand definieert:

- De autoriteit of de cloud-instantie en tenant-id.
- De client-ID die u hebt gekregen van de registratie van de aanvraag.
- Een geheim van de klant of een certificaat.

# <a name="net"></a>[.NET](#tab/dotnet)

[appsettings.json](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/master/1-Call-MSGraph/daemon-console/appsettings.json) van het [voorbeeld van de .NET Core console daemon.](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2)

```JSon
{
  "Instance": "https://login.microsoftonline.com/{0}",
  "Tenant": "[Enter here the tenantID or domain name for your Azure AD tenant]",
  "ClientId": "[Enter here the ClientId for your application]",
  "ClientSecret": "[Enter here a client secret for your application]",
  "CertificateName": "[Or instead of client secret: Enter here the name of a certificate (from the user cert store) as registered with your application]"
}
```

U verstrekt `ClientSecret` een `CertificateName`of een . Deze instellingen zijn exclusief.

# <a name="python"></a>[Python](#tab/python)

Wanneer u een vertrouwelijke client met klantgeheimen bouwt, ziet het [bestand parameters.json](https://github.com/Azure-Samples/ms-identity-python-daemon/blob/master/1-Call-MsGraph-WithSecret/parameters.json) config in het [voorbeeld van Python daemon](https://github.com/Azure-Samples/ms-identity-python-daemon) als volgt:

```Json
{
  "authority": "https://login.microsoftonline.com/Enter_the_Tenant_Name_Here",
  "client_id": "your_client_id",
  "scope": [ "https://graph.microsoft.com/.default" ],
  "secret": "The secret generated by AAD during your confidential app registration",
  "endpoint": "https://graph.microsoft.com/v1.0/users"
}
```

Wanneer u een vertrouwelijke client met certificaten bouwt, ziet het bestand [parameters.json](https://github.com/Azure-Samples/ms-identity-python-daemon/blob/master/2-Call-MsGraph-WithCertificate/parameters.json) config in het voorbeeld [van Python daemon](https://github.com/Azure-Samples/ms-identity-python-daemon) als volgt uit:

```Json
{
  "authority": "https://login.microsoftonline.com/Enter_the_Tenant_Name_Here",
  "client_id": "your_client_id",
  "scope": [ "https://graph.microsoft.com/.default" ],
  "thumbprint": "790E... The thumbprint generated by AAD when you upload your public cert",
  "private_key_file": "server.pem",
  "endpoint": "https://graph.microsoft.com/v1.0/users"
}
```

# <a name="java"></a>[Java](#tab/java)

```Java
 private final static String CLIENT_ID = "";
 private final static String AUTHORITY = "https://login.microsoftonline.com/<tenant>/";
 private final static String CLIENT_SECRET = "";
 private final static Set<String> SCOPE = Collections.singleton("https://graph.microsoft.com/.default");
```

---

### <a name="instantiate-the-msal-application"></a>Instantiate de MSAL applicatie

Als u de MSAL-toepassing wilt instantiëren, moet u het MSAL-pakket toevoegen, verwijzen of importeren (afhankelijk van de taal).

De constructie is anders, afhankelijk van of u klantgeheimen of certificaten gebruikt (of, als een geavanceerd scenario, ondertekende beweringen).

#### <a name="reference-the-package"></a>Verwijzen naar het pakket

Verwijs naar het MSAL-pakket in uw toepassingscode.

# <a name="net"></a>[.NET](#tab/dotnet)

Voeg het [Microsoft.IdentityClient](https://www.nuget.org/packages/Microsoft.Identity.Client) NuGet-pakket toe aan uw toepassing.
In MSAL.NET wordt de vertrouwelijke clientapplicatie `IConfidentialClientApplication` vertegenwoordigd door de interface.
Gebruik de MSAL.NET naamruimte in de broncode.

```csharp
using Microsoft.Identity.Client;
IConfidentialClientApplication app;
```

# <a name="python"></a>[Python](#tab/python)

```python
import msal
```

# <a name="java"></a>[Java](#tab/java)

```java
import com.microsoft.aad.msal4j.ClientCredentialFactory;
import com.microsoft.aad.msal4j.ClientCredentialParameters;
import com.microsoft.aad.msal4j.ConfidentialClientApplication;
import com.microsoft.aad.msal4j.IAuthenticationResult;
import com.microsoft.aad.msal4j.IClientCredential;
import com.microsoft.aad.msal4j.MsalException;
import com.microsoft.aad.msal4j.SilentParameters;
```

---

#### <a name="instantiate-the-confidential-client-application-with-a-client-secret"></a>Instantiate de vertrouwelijke client applicatie met een client geheim

Hier is de code om de vertrouwelijke clientapplicatie te instantiëren met een geheim van een klant:

# <a name="net"></a>[.NET](#tab/dotnet)

```csharp
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
           .WithClientSecret(config.ClientSecret)
           .WithAuthority(new Uri(config.Authority))
           .Build();
```

# <a name="python"></a>[Python](#tab/python)

```Python
config = json.load(open(sys.argv[1]))

# Create a preferably long-lived app instance that maintains a token cache.
app = msal.ConfidentialClientApplication(
    config["client_id"], authority=config["authority"],
    client_credential=config["secret"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )
```

# <a name="java"></a>[Java](#tab/java)

```Java
IClientCredential credential = ClientCredentialFactory.createFromSecret(CLIENT_SECRET);

ConfidentialClientApplication cca =
        ConfidentialClientApplication
                .builder(CLIENT_ID, credential)
                .authority(AUTHORITY)
                .build();
```

---

#### <a name="instantiate-the-confidential-client-application-with-a-client-certificate"></a>Instantiate de vertrouwelijke client applicatie met een client certificaat

Hier is de code om een toepassing met een certificaat te bouwen:

# <a name="net"></a>[.NET](#tab/dotnet)

```csharp
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
    .WithCertificate(certificate)
    .WithAuthority(new Uri(config.Authority))
    .Build();
```

# <a name="python"></a>[Python](#tab/python)

```Python
config = json.load(open(sys.argv[1]))

# Create a preferably long-lived app instance that maintains a token cache.
app = msal.ConfidentialClientApplication(
    config["client_id"], authority=config["authority"],
    client_credential={"thumbprint": config["thumbprint"], "private_key": open(config['private_key_file']).read()},
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )
```

# <a name="java"></a>[Java](#tab/java)

In MSAL Java zijn er twee bouwers om de vertrouwelijke clientapplicatie te instantiëren met certificaten:

```Java

InputStream pkcs12Certificate = ... ; /* Containing PCKS12-formatted certificate*/
string certificatePassword = ... ;    /* Contains the password to access the certificate */

IClientCredential credential = ClientCredentialFactory.createFromCertificate(pkcs12Certificate, certificatePassword);

ConfidentialClientApplication cca =
        ConfidentialClientApplication
                .builder(CLIENT_ID, credential)
                .authority(AUTHORITY)
                .build();
```

of

```Java
PrivateKey key = getPrivateKey(); /* RSA private key to sign the assertion */
X509Certificate publicCertificate = getPublicCertificate(); /* x509 public certificate used as a thumbprint */

IClientCredential credential = ClientCredentialFactory.createFromCertificate(key, publicCertificate);

ConfidentialClientApplication cca =
        ConfidentialClientApplication
                .builder(CLIENT_ID, credential)
                .authority(AUTHORITY)
                .build();
```

---

#### <a name="advanced-scenario-instantiate-the-confidential-client-application-with-client-assertions"></a>Geavanceerd scenario: De vertrouwelijke clienttoepassing instantiëren met clientbeweringen

# <a name="net"></a>[.NET](#tab/dotnet)

In plaats van een klantgeheim of een certificaat, kan de vertrouwelijke clientapplicatie ook zijn identiteit bewijzen aan de hand van beweringen van klanten.

MSAL.NET heeft twee methoden om ondertekende beweringen te doen aan de vertrouwelijke client-app:

- `.WithClientAssertion()`
- `.WithClientClaims()`

Wanneer u `WithClientAssertion`gebruik maakt, moet u een ondertekende JWT. Dit geavanceerde scenario wordt beschreven in [clientbeweringen](msal-net-client-assertions.md).

```csharp
string signedClientAssertion = ComputeAssertion();
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithClientAssertion(signedClientAssertion)
                                          .Build();
```

Wanneer u `WithClientClaims`MSAL.NET gebruikt, produceert u een ondertekende bewering die de claims bevat die worden verwacht door Azure AD, plus aanvullende clientclaims die u wilt verzenden.
Deze code laat zien hoe je dat doet:

```csharp
string ipAddress = "192.168.1.2";
var claims = new Dictionary<string, string> { { "client_ip", ipAddress } };
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithAuthority(new Uri(config.Authority))
                                          .WithClientClaims(certificate, claims)
                                          .Build();```
```

Nogmaals, voor meer informatie, zie [Client beweringen](msal-net-client-assertions.md).

# <a name="python"></a>[Python](#tab/python)

In MSAL Python u claims van klanten indienen met `ConfidentialClientApplication`behulp van de claims die worden ondertekend door deze privésleutel.

```Python
config = json.load(open(sys.argv[1]))

# Create a preferably long-lived app instance that maintains a token cache.
app = msal.ConfidentialClientApplication(
    config["client_id"], authority=config["authority"],
    client_credential={"thumbprint": config["thumbprint"], "private_key": open(config['private_key_file']).read()},
    client_claims = {"client_ip": "x.x.x.x"}
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )
```

Zie voor meer informatie de referentiedocumentatie van MSAL Python voor [ConfidentialClientApplication](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.__init__).

# <a name="java"></a>[Java](#tab/java)

```Java
IClientCredential credential = ClientCredentialFactory.createFromClientAssertion(assertion);

ConfidentialClientApplication cca =
        ConfidentialClientApplication
                .builder(CLIENT_ID, credential)
                .authority(AUTHORITY)
                .build();
```

---

## <a name="next-steps"></a>Volgende stappen

# <a name="net"></a>[.NET](#tab/dotnet)

> [!div class="nextstepaction"]
> [Daemon app - het verwerven van tokens voor de app](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-acquire-token?tabs=dotnet)

# <a name="python"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Daemon app - het verwerven van tokens voor de app](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-acquire-token?tabs=python)

# <a name="java"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Daemon app - het verwerven van tokens voor de app](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-acquire-token?tabs=java)

---
