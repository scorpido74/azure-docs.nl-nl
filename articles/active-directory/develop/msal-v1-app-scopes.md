---
title: Scopes voor v1.0 apps (MSAL) | Azure
description: Meer informatie over de scopes voor een v1.0-toepassing met behulp van de Microsoft Authentication Library (MSAL).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/25/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 61d07c1ba912a0e24b2f4e5fa67243b4525db367
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536179"
---
# <a name="scopes-for-a-web-api-accepting-v10-tokens"></a>Scopes voor een web-API die v1.0-tokens accepteert

OAuth2-machtigingen zijn machtigingsscopes die een Azure Active Directory (Azure AD) voor ontwikkelaars (v1.0) web-API-toepassing (resource) blootstelt aan clienttoepassingen. Deze machtigingsmogelijkheden kunnen worden verleend aan clienttoepassingen tijdens de toestemming. Zie de `oauth2Permissions` sectie over in de naslagverwijzing naar het [Azure Active Directory-toepassingsmanifest](reference-app-manifest.md#manifest-reference).

## <a name="scopes-to-request-access-to-specific-oauth2-permissions-of-a-v10-application"></a>Scopes om toegang te vragen tot specifieke OAuth2-machtigingen van een v1.0-toepassing

Tokens aanschaffen voor specifieke scopes van een v1.0-toepassing (bijvoorbeeld https://graph.microsoft.com)de Microsoft Graph API, namelijk scopes maken door een gewenste resource-id te concateneren met een gewenste OAuth2-machtiging voor die bron.

Bijvoorbeeld om namens de gebruiker toegang te krijgen tot een v1.0-web-API waarbij de app-ID URI is: `ResourceId`

```csharp
var scopes = new [] {  ResourceId+"/user_impersonation"};
```

```javascript
var scopes = [ ResourceId + "/user_impersonation"];
```

Als u wilt lezen en schrijven met MSAL.NET\/Azure AD met behulp van de Microsoft Graph API (https: /graph.microsoft.com/), moet u een lijst met scopes maken, zoals weergegeven in de volgende voorbeelden:

```csharp
string ResourceId = "https://graph.microsoft.com/";
var scopes = new [] { ResourceId + "Directory.Read", ResourceID + "Directory.Write"}
```

```javascript
var ResourceId = "https://graph.microsoft.com/";
var scopes = [ ResourceId + "Directory.Read", ResourceID + "Directory.Write"];
```

Als u het bereik wilt schrijven dat\/overeenkomt met de Azure Resource Manager API (https: /management.core.windows.net/), moet u het volgende bereik aanvragen (let op de twee slashes):

```csharp
var scopes = new[] {"https://management.core.windows.net//user_impersonation"};
var result = await app.AcquireTokenInteractive(scopes).ExecuteAsync();

// then call the API: https://management.azure.com/subscriptions?api-version=2016-09-01
```

> [!NOTE]
> U moet twee slashes gebruiken omdat de Azure Resource Manager API een slash verwacht in de doelgroepclaim (aud) en er vervolgens een slash is om de API-naam van het bereik te scheiden.

De logica die wordt gebruikt door Azure AD is de volgende:

- Voor ADAL-eindpunt (Azure AD v1.0) met een v1.0-toegangstoken (het enige mogelijk), aud=resource
- Voor MSAL -eindpunt (Microsoft Identity Platform (v2.0)) vraagt u een toegangstoken voor een bron die v2.0-tokens accepteert,`aud=resource.AppId`
- Voor MSAL (v2.0-eindpunt) die een toegangstoken vraagt voor een bron die een v1.0-toegangstoken accepteert (wat hierboven het geval is), ontleedt Azure AD het gewenste publiek uit het gevraagde bereik door alles voor de laatste slash te nemen en het als resource-id te gebruiken. Daarom, als\/https: /database.windows.net een publiek\/van "https: /database.windows.net/" verwacht, moet\/je een bereik van "https: /database.windows.net//.default" aanvragen. Zie ook GitHub-probleem [#747: De trailing slash van de resourceurl wordt weggelaten, waardoor sql auth-fouten zijn veroorzaakt.](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747)

## <a name="scopes-to-request-access-to-all-the-permissions-of-a-v10-application"></a>Scopes om toegang te vragen tot alle machtigingen van een v1.0-toepassing

Als u een token wilt aanschaffen voor alle statische scopes van een v1.0-toepassing, wordt ".default" toegevoegd aan de app-ID URI van de API:

```csharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

```javascript
var ResourceId = "someAppIDURI";
var scopes = [ ResourceId + "/.default"];
```

## <a name="scopes-to-request-for-a-client-credential-flowdaemon-app"></a>Scopes die moeten worden aangevraagd voor een clientreferentiestroom/daemon-app

In het geval van clientreferentiestroom zou de `/.default`te passeren scope ook . Dit vertelt aan Azure AD: "alle machtigingen op app-niveau waarmee de beheerder heeft ingestemd in de toepassingsregistratie.
