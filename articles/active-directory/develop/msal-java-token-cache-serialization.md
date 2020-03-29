---
title: Aangepaste tokencacheserialisatie (MSAL4j)
titleSuffix: Microsoft identity platform
description: Meer informatie over het serialiseren van de tokencache voor MSAL voor Java
services: active-directory
author: sangonzal
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/07/2019
ms.author: sagonzal
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: bcb34d83365112b97769186ad74dfd762b05c2e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76696160"
---
# <a name="custom-token-cache-serialization-in-msal-for-java"></a>Aangepaste tokencacheserialisatie in MSAL voor Java

Als u de tokencache tussen exemplaren van uw toepassing wilt blijven beheren, moet u de serialisatie aanpassen. De Java-klassen en interfaces die betrokken zijn bij tokencacheserialisatie zijn de volgende:

- [ITokenCache:](https://static.javadoc.io/com.microsoft.azure/msal4j/0.5.0-preview/com/microsoft/aad/msal4j/ITokenCache.html)interface die cache voor beveiligingstoken vertegenwoordigt.
- [ITokenCacheAccessAspect:](https://static.javadoc.io/com.microsoft.azure/msal4j/0.5.0-preview/com/microsoft/aad/msal4j/ITokenCacheAccessAspect.html)Interface die de werking van de uitvoerende code voor en na de toegang vertegenwoordigt. U @Override zou *vóórCacheAccess* en *afterCacheAccess* met de logica die verantwoordelijk is voor het serialiseren en deserialiseren van de cache.
- [ITokenCacheContext](https://static.javadoc.io/com.microsoft.azure/msal4j/0.5.0-preview/com/microsoft/aad/msal4j/ITokenCacheAccessContext.html): Interface die context weergeeft waarin de tokencache wordt geopend. 

Hieronder vindt u een naïeve implementatie van aangepaste serialisatie van tokencacheserialisatie/deserialisatie. Kopieer en plak dit niet in een productieomgeving.

```Java
static class TokenPersistence implements ITokenCacheAccessAspect {
String data;

TokenPersistence(String data) {
        this.data = data;
}

@Override
public void beforeCacheAccess(ITokenCacheAccessContext iTokenCacheAccessContext) {
        iTokenCacheAccessContext.tokenCache().deserialize(data);
}

@Override
public void afterCacheAccess(ITokenCacheAccessContext iTokenCacheAccessContext) {
        data = iTokenCacheAccessContext.tokenCache().serialize();
}
```

```Java
// Loads cache from file
String dataToInitCache = readResource(this.getClass(), "/cache_data/serialized_cache.json");

ITokenCacheAccessAspect persistenceAspect = new TokenPersistence(dataToInitCache);

// By setting *TokenPersistence* on the PublicClientApplication, MSAL will call *beforeCacheAccess()* before accessing the cache and *afterCacheAccess()* after accessing the cache. 
PublicClientApplication app = 
PublicClientApplication.builder("my_client_id").setTokenCacheAccessAspect(persistenceAspect).build();
```

## <a name="learn-more"></a>Meer informatie

Meer informatie over [Accounts ophalen en verwijderen uit de tokencache met MSAL voor Java](msal-java-get-remove-accounts-token-cache.md).
