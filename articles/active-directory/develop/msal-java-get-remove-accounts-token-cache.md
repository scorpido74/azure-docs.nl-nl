---
title: Accounts ophalen & verwijderen uit de token cache (MSAL4j) | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over het weer geven en verwijderen van accounts uit de token cache met behulp van de micro soft-verificatie bibliotheek voor Java.
services: active-directory
author: sangonzal
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 11/07/2019
ms.author: sagonzal
ms.reviewer: navyasri.canumalla
ms.custom: aaddev, devx-track-java
ms.openlocfilehash: 6267c2118b7bac9befb42102d1118faac639bf49
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87312551"
---
# <a name="get-and-remove-accounts-from-the-token-cache-using-msal-for-java"></a>Accounts uit de token cache ophalen en verwijderen met MSAL voor Java

MSAL voor Java biedt standaard een token cache in het geheugen. De token cache in het geheugen duurt de duur van het toepassings exemplaar.

## <a name="see-which-accounts-are-in-the-cache"></a>Bekijken welke accounts zich in de cache bevinden

U kunt controleren welke accounts zich in de cache bevinden door aan te roepen `PublicClientApplication.getAccounts()` zoals wordt weer gegeven in het volgende voor beeld:

```java
PublicClientApplication pca = new PublicClientApplication.Builder(
                labResponse.getAppId()).
                authority(TestConstants.ORGANIZATIONS_AUTHORITY).
                build();

Set<IAccount> accounts = pca.getAccounts().join();
```

## <a name="remove-accounts-from-the-cache"></a>Accounts uit de cache verwijderen

Als u een account uit de cache wilt verwijderen, gaat u naar het account dat moet worden verwijderd en roept u vervolgens de volgende opdracht aan, `PublicClientApplicatoin.removeAccount()` zoals wordt weer gegeven in het onderstaande voor beeld:

```java
Set<IAccount> accounts = pca.getAccounts().join();

IAccount accountToBeRemoved = accounts.stream().filter(
                x -> x.username().equalsIgnoreCase(
                        UPN_OF_USER_TO_BE_REMOVED)).findFirst().orElse(null);

pca.removeAccount(accountToBeRemoved).join();
```

## <a name="learn-more"></a>Meer informatie

Als u MSAL voor Java gebruikt, kunt u meer informatie vinden over [aangepaste serialisatie van de token cache in MSAL voor Java](msal-java-token-cache-serialization.md).
