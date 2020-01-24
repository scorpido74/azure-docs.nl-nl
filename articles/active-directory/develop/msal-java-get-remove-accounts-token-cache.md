---
title: Accounts ophalen & verwijderen uit de token cache (MSAL4j) | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over het weer geven en verwijderen van accounts uit de token cache met behulp van de micro soft-verificatie bibliotheek voor Java.
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
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76696194"
---
# <a name="get-and-remove-accounts-from-the-token-cache-using-msal-for-java"></a>Accounts uit de token cache ophalen en verwijderen met MSAL voor Java

MSAL voor Java biedt standaard een token cache in het geheugen. De token cache in het geheugen duurt de duur van het toepassings exemplaar.

## <a name="see-which-accounts-are-in-the-cache"></a>Bekijken welke accounts zich in de cache bevinden

U kunt controleren welke accounts zich in de cache bevinden door `PublicClientApplication.getAccounts()` aan te roepen, zoals wordt weer gegeven in het volgende voor beeld:

```java
PublicClientApplication pca = new PublicClientApplication.Builder(
                labResponse.getAppId()).
                authority(TestConstants.ORGANIZATIONS_AUTHORITY).
                build();

Set<IAccount> accounts = pca.getAccounts().join();
```

## <a name="remove-accounts-from-the-cache"></a>Accounts uit de cache verwijderen

Als u een account uit de cache wilt verwijderen, gaat u naar het account dat moet worden verwijderd en roept u `PublicClientApplicatoin.removeAccount()` aan, zoals wordt weer gegeven in het volgende voor beeld:

```java
Set<IAccount> accounts = pca.getAccounts().join();

IAccount accountToBeRemoved = accounts.stream().filter(
                x -> x.username().equalsIgnoreCase(
                        UPN_OF_USER_TO_BE_REMOVED)).findFirst().orElse(null);

pca.removeAccount(accountToBeRemoved).join();
```

## <a name="learn-more"></a>Meer informatie

Als u MSAL voor Java gebruikt, kunt u meer informatie vinden over [aangepaste serialisatie van de token cache in MSAL voor Java](msal-java-token-cache-serialization.md).
