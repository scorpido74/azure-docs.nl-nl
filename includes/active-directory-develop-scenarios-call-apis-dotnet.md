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
ms.openlocfilehash: 3d4e45d1bf53bab4d1f9c45367f9d051f1668e2b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76309032"
---
### <a name="authenticationresult-properties-in-msalnet"></a>VerificatieResultaateigenschappen in MSAL.NET

De methoden om tokens `AuthenticationResult`te verkrijgen keren terug. `Task<AuthenticationResult>` Retourneert voor async-methoden.

In MSAL.NET, `AuthenticationResult` onthult:

- `AccessToken`voor de web-API om toegang te krijgen tot bronnen. Deze parameter is een tekenreeks, meestal een Base-64-gecodeerde JWT. De client mag nooit in het toegangstoken kijken. Het formaat is niet gegarandeerd stabiel te blijven, en het kan worden versleuteld voor de bron. Het schrijven van code die afhankelijk is van toegangstokeninhoud op de client is een van de grootste bronnen van fouten en fouten in de clientlogica. Zie [Toegangstokens voor](../articles/active-directory/develop/access-tokens.md)meer informatie.
- `IdToken`voor de gebruiker. Deze parameter is een gecodeerde JWT. Zie [ID-tokens voor](../articles/active-directory/develop/id-tokens.md)meer informatie.
- `ExpiresOn`geeft de datum en het tijdstip aan waarop het token verloopt.
- `TenantId`bevat de tenant waarin de gebruiker is gevonden. Voor gastgebruikers in Azure Active Directory (Azure AD) B2B-scenario's is de tenant de gasttenant, niet de unieke tenant.
Wanneer het token voor een `AuthenticationResult` gebruiker wordt geleverd, bevat het ook informatie over deze gebruiker. Voor vertrouwelijke clientstromen waarbij tokens worden aangevraagd zonder gebruiker voor de toepassing, is deze gebruikersinformatie null.
- Waarvoor `Scopes` het token werd uitgegeven.
- De unieke ID voor de gebruiker.

### <a name="iaccount"></a>IAccount

MSAL.NET definieert de notie van een account via de `IAccount` interface. Deze brekende verandering biedt de juiste semantiek. Dezelfde gebruiker kan meerdere accounts hebben, in verschillende Azure AD-mappen. Ook biedt MSAL.NET betere informatie in het geval van gastscenario's omdat thuisaccountgegevens worden verstrekt.
Het volgende diagram toont `IAccount` de structuur van de interface.

![IAccount-interfacestructuur](https://user-images.githubusercontent.com/13203188/44657759-4f2df780-a9fe-11e8-97d1-1abbffade340.png)

De `AccountId` klasse identificeert een account in een specifieke tenant met de eigenschappen die in de volgende tabel worden weergegeven.

| Eigenschap | Beschrijving |
|----------|-------------|
| `TenantId` | Een tekenreeksweergave voor een GUID, de id van de tenant waar het account zich bevindt. |
| `ObjectId` | Een tekenreeksweergave voor een GUID, de id van de gebruiker die eigenaar is van het account in de tenant. |
| `Identifier` | Unieke id voor het account. `Identifier`is de samenvoeging van `ObjectId` en `TenantId` gescheiden door een komma. Ze zijn niet gebaseerd op basis 64. |

De `IAccount` interface vertegenwoordigt informatie over één account. Dezelfde gebruiker kan aanwezig zijn in verschillende tenants, wat betekent dat een gebruiker meerdere accounts kan hebben. De leden worden weergegeven in de volgende tabel.

| Eigenschap | Beschrijving |
|----------|-------------|
| `Username` | Een tekenreeks die bijvoorbeeld de weergavebare waarde bevat in de john.doe@contoso.comUPN-indeling (UserPrincipalName). Deze tekenreeks kan null zijn, in tegenstelling tot HomeAccountId en HomeAccountId.Identifier, die niet null zijn. Deze eigenschap `DisplayableId` vervangt `IUser` de eigenschap van in eerdere versies van MSAL.NET. |
| `Environment` | Een tekenreeks die bijvoorbeeld de identiteitsprovider voor `login.microsoftonline.com`dit account bevat. Deze eigenschap `IdentityProvider` vervangt `IUser`de eigenschap `IdentityProvider` van , behalve dat had ook informatie over de huurder, in aanvulling op de cloud-omgeving. Hier is de waarde alleen de gastheer. |
| `HomeAccountId` | De account-ID van het thuisaccount voor de gebruiker. Deze eigenschap identificeert de gebruiker op unieke wijze voor Azure AD-tenants. |

### <a name="use-the-token-to-call-a-protected-api"></a>Het token gebruiken om een beveiligde API aan te roepen

Nadat `AuthenticationResult` wordt geretourneerd door `result`MSAL in , voeg deze toe aan de HTTP-autorisatiekop voordat u de aanroep doet om toegang te krijgen tot de beveiligde web-API.

```csharp
httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call the web API.
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
...
}
```