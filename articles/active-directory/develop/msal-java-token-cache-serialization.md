---
title: Aangepaste serialisatie van token cache in MSAL voor Java
titleSuffix: Microsoft identity platform
description: Meer informatie over het serialiseren van de token cache voor MSAL voor Java
services: active-directory
documentationcenter: dev-center-name
author: sangonzal
manager: henrikm
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/07/2019
ms.author: sagonzal
ms.reviewer: navyasri.canumalla
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7323a47095c58958503cecf16189ed875fac3b4d
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2019
ms.locfileid: "74452601"
---
# <a name="custom-token-cache-serialization-in-msal-for-java"></a>Aangepaste serialisatie van token cache in MSAL voor Java

Als u de token cache tussen exemplaren van uw toepassing wilt behouden, moet u de serialisatie aanpassen. De Java-klassen en interfaces die betrokken zijn bij de serialisatie van de token cache zijn de volgende:

- [ITokenCache](https://static.javadoc.io/com.microsoft.azure/msal4j/0.5.0-preview/com/microsoft/aad/msal4j/ITokenCache.html): interface die de cache van het beveiligings token vertegenwoordigt.
- [ITokenCacheAccessAspect](https://static.javadoc.io/com.microsoft.azure/msal4j/0.5.0-preview/com/microsoft/aad/msal4j/ITokenCacheAccessAspect.html): een interface die de werking van code voor en na Access weergeeft. U zou @Override *beforeCacheAccess* en *afterCacheAccess* met de logica die verantwoordelijk is voor het serialiseren en deserialiseren van de cache.
- [ITokenCacheContext](https://static.javadoc.io/com.microsoft.azure/msal4j/0.5.0-preview/com/microsoft/aad/msal4j/ITokenCacheAccessContext.html): de interface die de context vertegenwoordigt waarin de token cache wordt geopend. 

Hieronder vindt u een Naïve-implementatie van aangepaste serialisatie van de serialisatie/deserialisatie van de token cache. Kopieer en plak dit niet in een productie omgeving.

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

Meer informatie over [het ophalen en verwijderen van accounts uit de token cache met behulp van MSAL voor Java](msal-java-get-remove-accounts-token-cache.md).
