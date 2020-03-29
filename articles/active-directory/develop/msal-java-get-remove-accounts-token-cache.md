---
title: Download & accounts uit de tokencache (MSAL4j) te verwijderen | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over het weergeven en verwijderen van accounts uit de tokencache met behulp van de Microsoft-verificatiebibliotheek voor Java.
services: active-directory
author: sangonzal
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/07/2019
ms.author: sagonzal
ms.reviewer: navyasri.canumalla
ms.custom: aaddev
ms.openlocfilehash: 2b138678b186cc41b76254658ad604c2da2d76c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76696194"
---
# <a name="get-and-remove-accounts-from-the-token-cache-using-msal-for-java"></a>Accounts ophalen en verwijderen uit de tokencache met MSAL voor Java

MSAL voor Java biedt standaard een in-memory tokencache. De in-memory tokencache duurt de duur van de toepassingsinstantie.

## <a name="see-which-accounts-are-in-the-cache"></a>Zien welke accounts zich in de cache bevinden

U controleren welke accounts zich `PublicClientApplication.getAccounts()` in de cache bevinden door te bellen zoals in het volgende voorbeeld wordt weergegeven:

```java
PublicClientApplication pca = new PublicClientApplication.Builder(
                labResponse.getAppId()).
                authority(TestConstants.ORGANIZATIONS_AUTHORITY).
                build();

Set<IAccount> accounts = pca.getAccounts().join();
```

## <a name="remove-accounts-from-the-cache"></a>Accounts uit de cache verwijderen

Als u een account uit de cache wilt verwijderen, `PublicClientApplicatoin.removeAccount()` zoekt u het account dat moet worden verwijderd en belt u vervolgens zoals in het volgende voorbeeld wordt weergegeven:

```java
Set<IAccount> accounts = pca.getAccounts().join();

IAccount accountToBeRemoved = accounts.stream().filter(
                x -> x.username().equalsIgnoreCase(
                        UPN_OF_USER_TO_BE_REMOVED)).findFirst().orElse(null);

pca.removeAccount(accountToBeRemoved).join();
```

## <a name="learn-more"></a>Meer informatie

Als u MSAL voor Java gebruikt, leest u meer over [custom tokencache serialisatie in MSAL for Java.](msal-java-token-cache-serialization.md)
