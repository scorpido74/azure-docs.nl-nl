---
title: Accounts ophalen en verwijderen uit de token cache met behulp van MSAL voor Java (MSAL4j)
titleSuffix: Microsoft identity platform
description: Meer informatie over het weer geven en verwijderen van accounts uit de token cache met behulp van de micro soft-verificatie bibliotheek voor Java.
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
ms.openlocfilehash: 343abd87d3b5e8b82989b8b370cef61ec6d051df
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/10/2019
ms.locfileid: "73905509"
---
# <a name="get-and-remove-accounts-from-the-token-cache-using-msal-for-java-msal4j"></a>Accounts ophalen en verwijderen uit de token cache met behulp van MSAL voor Java (MSAL4j)

MSAL4J biedt standaard een token cache in het geheugen. De token cache in het geheugen duurt de duur van het toepassings exemplaar.

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
