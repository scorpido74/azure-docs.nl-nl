---
title: Initialiseren MSAL.NET clientaanvragen | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over het initialiseren van openbare client- en vertrouwelijke clienttoepassingen met behulp van de Microsoft-verificatiebibliotheek voor .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/12/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 57ce6ab31421cd4016f7e204eeabce82f2f7e6a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77083986"
---
# <a name="initialize-client-applications-using-msalnet"></a>Clienttoepassingen initialiseren met MSAL.NET
In dit artikel wordt beschreven dat openbare client- en vertrouwelijke clienttoepassingen worden geïnitialiseren met behulp van Microsoft Authentication Library voor .NET (MSAL.NET).  Lees het [overzicht](msal-client-applications.md)voor meer informatie over de typen clienttoepassingen en toepassingsconfiguratie.

Met MSAL.NET 3.x, de aanbevolen manier om een toepassing te instantiëren is met behulp van de applicatie bouwers: `PublicClientApplicationBuilder` en `ConfidentialClientApplicationBuilder`. Ze bieden een krachtig mechanisme om de toepassing te configureren, hetzij vanuit de code, of vanuit een configuratiebestand, of zelfs door beide benaderingen te mengen.

## <a name="prerequisites"></a>Vereisten
Voordat u een toepassing initialiseert, moet u deze eerst [registreren,](quickstart-register-app.md) zodat uw app kan worden geïntegreerd met het Microsoft-identiteitsplatform.  Na registratie hebt u mogelijk de volgende informatie nodig (die te vinden is in de Azure-portal):

- De client-id (een tekenreeks die een GUID vertegenwoordigt)
- De URL van de identiteitsprovider (de naam van de instantie) en de aanmeldingsdoelgroep voor uw toepassing. Deze twee parameters staan gezamenlijk bekend als de autoriteit.
- De tenant-id als u een regel van zakelijke toepassing uitsluitend voor uw organisatie (ook wel single-tenant applicatie) schrijft.
- Het toepassingsgeheim (clientsecret string) of certificaat (van type X509Certificate2) als het een vertrouwelijke client-app is.
- Voor web-apps, en soms voor openbare client-apps (met name wanneer uw app een broker moet gebruiken), hebt u ook de redirectUri ingesteld waar de identiteitsprovider contact opneemt met uw toepassing met de beveiligingstokens.

## <a name="ways-to-initialize-applications"></a>Manieren om toepassingen te initialiseren
Er zijn veel verschillende manieren om clienttoepassingen te instantiëren.

### <a name="initializing-a-public-client-application-from-code"></a>Een openbare clienttoepassing initialiseren van code

Met de volgende code worden een openbare clienttoepassing, aanmeldende gebruikers in de openbare microsoft azure-cloud, met hun werk- en schoolaccounts of hun persoonlijke Microsoft-accounts geanimeerd.

```csharp
IPublicClientApplication app = PublicClientApplicationBuilder.Create(clientId)
    .Build();
```

### <a name="initializing-a-confidential-client-application-from-code"></a>Een vertrouwelijke clienttoepassing initialiseren van code

Op dezelfde manier wordt met de volgende code een vertrouwelijke toepassing `https://myapp.azurewebsites.net`(een web-app op) gekoppeld aan tokens van gebruikers in de openbare Microsoft Azure-cloud, met hun werk- en schoolaccounts of hun persoonlijke Microsoft-accounts geinstantieerd. De toepassing wordt geïdentificeerd met de identiteitsprovider door het delen van een klantgeheim:

```csharp
string redirectUri = "https://myapp.azurewebsites.net";
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.Create(clientId)
    .WithClientSecret(clientSecret)
    .WithRedirectUri(redirectUri )
    .Build();
```

Zoals u wellicht weet, wilt u in de productie, in plaats van een clientgeheim te gebruiken, een certificaat delen met Azure AD. De code zou dan de volgende zijn:

```csharp
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.Create(clientId)
    .WithCertificate(certificate)
    .WithRedirectUri(redirectUri )
    .Build();
```

### <a name="initializing-a-public-client-application-from-configuration-options"></a>Een openbare clienttoepassing initialiseren op basis van configuratieopties

Met de volgende code wordt een openbare clienttoepassing geinstantt vanaf een configuratieobject, dat programmatisch kan worden ingevuld of kan worden gelezen uit een configuratiebestand:

```csharp
PublicClientApplicationOptions options = GetOptions(); // your own method
IPublicClientApplication app = PublicClientApplicationBuilder.CreateWithApplicationOptions(options)
    .Build();
```

### <a name="initializing-a-confidential-client-application-from-configuration-options"></a>Een vertrouwelijke clienttoepassing initialiseren vanuit configuratieopties

Hetzelfde soort patroon geldt voor vertrouwelijke clienttoepassingen. U ook andere `.WithXXX` parameters toevoegen met modifiers (hier een certificaat).

```csharp
ConfidentialClientApplicationOptions options = GetOptions(); // your own method
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(options)
    .WithCertificate(certificate)
    .Build();
```

## <a name="builder-modifiers"></a>Builder modifiers

In de codefragmenten met toepassingsbouwers `.With` kunnen een aantal methoden worden toegepast als `.WithCertificate` `.WithRedirectUri`modifiers (bijvoorbeeld en ). 

### <a name="modifiers-common-to-public-and-confidential-client-applications"></a>Modifiers gemeenschappelijk voor openbare en vertrouwelijke clienttoepassingen

De modifiers die u instellen op een openbare client of vertrouwelijke clienttoepassingsbouwer zijn:

|Modifier | Beschrijving|
|--------- | --------- |
|`.WithAuthority()`7 overschrijvingen | Hiermee stelt u de standaardautoriteit van de toepassing in op een Azure AD-autoriteit, met de mogelijkheid om de Azure Cloud, het publiek, de tenant (tenant-id of domeinnaam) te kiezen of rechtstreeks de instantie URI te verstrekken.|
|`.WithAdfsAuthority(string)` | Hiermee stelt u de standaardautoriteit voor toepassingen in als een ADFS-autoriteit.|
|`.WithB2CAuthority(string)` | Hiermee stelt u de standaardautoriteit voor toepassing in als een Azure AD B2C-autoriteit.|
|`.WithClientId(string)` | Overschrijft de client-id.|
|`.WithComponent(string)` | Hiermee stelt u de naam van de bibliotheek in met MSAL.NET (om telemetrieredenen). |
|`.WithDebugLoggingCallback()` | Als de toepassing wordt `Debug.Write` aangeroepen, wordt de foutopsporing sbugging-sporen ingeschakeld. Zie [Logboekregistratie](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/logging) voor meer informatie.|
|`.WithExtraQueryParameters(IDictionary<string,string> eqp)` | Stel de extra queryparameters op toepassingsniveau in die in alle verificatieaanvragen worden verzonden. Dit is overridable op elk niveau van `.WithExtraQueryParameters pattern`de tokenacquisitiemethode (met hetzelfde).|
|`.WithHttpClientFactory(IMsalHttpClientFactory httpClientFactory)` | Hiermee kunnen geavanceerde scenario's zoals configureren voor een HTTP-proxy of OM MSAL dwingen om een bepaalde HttpClient te gebruiken (bijvoorbeeld in ASP.NET Core-webapps/API's).|
|`.WithLogging()` | Als de toepassing wordt aangeroepen, wordt een callback met foutopsporingssporen aangeroepen. Zie [Logboekregistratie](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/logging) voor meer informatie.|
|`.WithRedirectUri(string redirectUri)` | Hiermee wordt de standaard omleiding uri overschreven. In het geval van openbare clienttoepassingen is dit handig voor scenario's waarbij de makelaar betrokken is.|
|`.WithTelemetry(TelemetryCallback telemetryCallback)` | Hiermee stelt u de gemachtigde in die wordt gebruikt om telemetrie te verzenden.|
|`.WithTenantId(string tenantId)` | Overschrijft de tenant-id of de tenantbeschrijving.|

### <a name="modifiers-specific-to-xamarinios-applications"></a>Modifiers specifiek voor Xamarin.iOS-toepassingen

De modifiers die u instellen op een openbare clienttoepassingsbouwer op Xamarin.iOS zijn:

|Modifier | Beschrijving|
|--------- | --------- |
|`.WithIosKeychainSecurityGroup()` | **Alleen Xamarin.iOS**: Hiermee stelt u de beveiligingsgroep voor iOS-sleutelhangers in (voor de cachepersistentie).|

### <a name="modifiers-specific-to-confidential-client-applications"></a>Modifiers specifiek voor vertrouwelijke clienttoepassingen

De modifiers die u instellen op een vertrouwensrelatie met clienttoepassingen zijn:

|Modifier | Beschrijving|
|--------- | --------- |
|`.WithCertificate(X509Certificate2 certificate)` | Hiermee stelt u het certificaat in dat de toepassing identificeert met Azure AD.|
|`.WithClientSecret(string clientSecret)` | Hiermee stelt u het clientgeheim (app-wachtwoord) in om de toepassing te identificeren met Azure AD.|

Deze modifiers sluiten elkaar uit. Als u beide verstrekt, zal MSAL een zinvolle uitzondering werpen.

### <a name="example-of-usage-of-modifiers"></a>Voorbeeld van het gebruik van modifiers

Laten we aannemen dat uw toepassing een bedrijfsregel is, die alleen voor uw organisatie is.  Dan u schrijven:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAadAuthority(AzureCloudInstance.AzurePublic, tenantId)
        .Build();
```

Waar het interessant wordt, is dat de programmering voor nationale clouds nu is vereenvoudigd. Als u wilt dat uw toepassing een multi-tenant toepassing in een nationale cloud is, u bijvoorbeeld schrijven:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAadAuthority(AzureCloudInstance.AzureUsGovernment, AadAuthorityAudience.AzureAdMultipleOrgs)
        .Build();
```

Er is ook een overschrijving voor ADFS (ADFS 2019 wordt momenteel niet ondersteund):
```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAdfsAuthority("https://consoso.com/adfs")
        .Build();
```

Tot slot u, als u een Azure AD B2C-ontwikkelaar bent, uw tenant als volgt opgeven:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithB2CAuthority("https://fabrikamb2c.b2clogin.com/tfp/{tenant}/{PolicySignInSignUp}")
        .Build();
```
