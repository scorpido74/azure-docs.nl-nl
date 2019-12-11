---
title: Geef een httpclient maakt-& proxy op (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over het bieden van uw eigen httpclient maakt en proxy om verbinding te maken met Azure AD met behulp van micro soft Authentication Library voor .NET (MSAL.NET).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/23/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 80822c2a745d7c9c5b3f56b7921fcc83c5c807d1
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74963225"
---
# <a name="providing-your-own-httpclient-and-proxy-using-msalnet"></a>Uw eigen httpclient maakt en proxy bieden met behulp van MSAL.NET
Wanneer u [een open bare client toepassing initialiseert](msal-net-initializing-client-applications.md), kunt u de `.WithHttpClientFactory method` gebruiken om uw eigen httpclient maakt te bieden.  Door uw eigen httpclient maakt te bieden, kunt u geavanceerde scenario's maken met een dergelijk nauw keurig beheer van een HTTP-proxy, het aanpassen van de headers van de gebruikers agent of het afdwingen van MSAL om een specifieke httpclient maakt te gebruiken (bijvoorbeeld in ASP.NET Core web apps/Api's).

## <a name="initialize-with-httpclientfactory"></a>Initialiseren met HttpClientFactory
In het volgende voor beeld ziet u hoe u een `HttpClientFactory` maakt en vervolgens een open bare client toepassing initialiseert:

```csharp
IMsalHttpClientFactory httpClientFactory = new MyHttpClientFactory();

var pca = PublicClientApplicationBuilder.Create(MsalTestConstants.ClientId) 
                                        .WithHttpClientFactory(httpClientFactory)
                                        .Build();
```

## <a name="httpclient-and-xamarin-ios"></a>Httpclient maakt en Xamarin iOS
Wanneer u Xamarin iOS gebruikt, is het raadzaam om een `HttpClient` te maken die expliciet gebruikmaakt van de op `NSURLSession`gebaseerde handler voor iOS 7 en hoger. MSAL.NET maakt automatisch een `HttpClient` die gebruikmaakt van `NSURLSessionHandler` voor iOS 7 en hoger. Raadpleeg de [Xamarin IOS-documentatie voor httpclient maakt](/xamarin/cross-platform/macios/http-stack)voor meer informatie.