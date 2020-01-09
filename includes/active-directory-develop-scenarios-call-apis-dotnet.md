---
title: bestand opnemen
description: bestand opnemen
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 4e01dbb0036761215a9a05c464b20ead340a2e3d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75423769"
---
### <a name="authenticationresult-properties-in-msalnet"></a>Eigenschappen van AuthenticationResult in MSAL.NET

De methoden voor het verkrijgen van tokens retour neren een `AuthenticationResult` (of, voor de async-methoden, een `Task<AuthenticationResult>`.

In MSAL.NET, `AuthenticationResult` exposes:

- `AccessToken` voor de Web-API voor toegang tot bronnen. Deze para meter is een teken reeks, meestal een met base64 gecodeerde JWT, maar de client mag nooit in het toegangs token kijken. De indeling is niet gegarandeerd stabiel en kan worden versleuteld voor de resource. Personen die code schrijven, afhankelijk van de inhoud van het toegangs token op de client, is een van de grootste bronnen van fouten en client logica-onderbrekingen. Zie ook [toegangs tokens](../articles/active-directory/develop/access-tokens.md)
- `IdToken` voor de gebruiker (deze para meter is een gecodeerde JWT). [Id-tokens](../articles/active-directory/develop/id-tokens.md) bekijken
- `ExpiresOn` vertelt de datum/tijd waarop het token verloopt
- `TenantId` bevat de Tenant waarin de gebruiker is gevonden. Voor gast gebruikers (Azure AD B2B-scenario's) is de Tenant-ID de gast-Tenant, niet de unieke Tenant.
Wanneer het token voor een gebruiker wordt geleverd, bevat `AuthenticationResult` ook informatie over deze gebruiker. Voor vertrouwelijke client stromen waarbij tokens worden aangevraagd zonder gebruiker (voor de toepassing), is deze gebruikers informatie null.
- Het `Scopes` waarvoor het token is uitgegeven.
- De unieke id voor de gebruiker.

### <a name="iaccount"></a>IAccount

MSAL.NET definieert het begrip account (via de `IAccount` Interface). Deze breuk wijziging biedt de juiste semantiek: het feit dat dezelfde gebruiker meerdere accounts kan hebben in verschillende Azure AD-directory's. MSAL.NET biedt ook betere informatie in het geval van gast scenario's, wanneer er informatie over het thuis account wordt verstrekt.
In het volgende diagram ziet u de structuur van de `IAccount`-Interface:

![installatiekopie](https://user-images.githubusercontent.com/13203188/44657759-4f2df780-a9fe-11e8-97d1-1abbffade340.png)

De klasse `AccountId` identificeert een account in een specifieke Tenant. Het heeft de volgende eigenschappen:

| Eigenschap | Beschrijving |
|----------|-------------|
| `TenantId` | Een teken reeks representatie voor een GUID. Dit is de ID van de Tenant waar het account zich bevindt. |
| `ObjectId` | Een teken reeks representatie voor een GUID. Dit is de ID van de gebruiker die eigenaar is van het account in de Tenant. |
| `Identifier` | De unieke id voor het account. `Identifier` is de samen voeging van `ObjectId` en `TenantId` gescheiden door een komma en worden niet met base64 gecodeerd. |

De `IAccount`-interface vertegenwoordigt informatie over één account. Dezelfde gebruiker kan zich in verschillende tenants bevinden, dat wil zeggen dat een gebruiker meerdere accounts kan hebben. De leden zijn:

| Eigenschap | Beschrijving |
|----------|-------------|
| `Username` | Een teken reeks met de waarde unplayable in de indeling UserPrincipalName (UPN), bijvoorbeeld john.doe@contoso.com. Deze teken reeks kan null zijn, terwijl HomeAccountId en HomeAccountId. Identifier niet null zijn. Met deze eigenschap wordt de eigenschap `DisplayableId` van `IUser` in eerdere versies van MSAL.NET vervangen. |
| `Environment` | Een teken reeks met de ID-provider voor dit account, bijvoorbeeld `login.microsoftonline.com`. Met deze eigenschap wordt de eigenschap `IdentityProvider` van `IUser`vervangen, behalve dat `IdentityProvider` ook informatie over de Tenant bevat (naast de cloud omgeving), terwijl de waarde alleen de host is. |
| `HomeAccountId` | AccountId van het thuis account voor de gebruiker. Met deze eigenschap wordt de gebruiker op unieke wijze in azure AD-tenants geïdentificeerd. |

### <a name="using-the-token-to-call-a-protected-api"></a>Het token gebruiken om een beveiligde API aan te roepen

Zodra de `AuthenticationResult` is geretourneerd door MSAL (in `result`), moet u deze toevoegen aan de HTTP-autorisatie-header voordat u de aanroep opent om toegang te krijgen tot de beveiligde web-API.

```csharp
httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call Web API.
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
...
}
```