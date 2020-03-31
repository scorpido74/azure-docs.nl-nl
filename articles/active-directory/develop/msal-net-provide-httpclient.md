---
title: Een httpclient-& proxy (MSAL.NET) geven | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over het verstrekken van uw eigen HttpClient en proxy om verbinding te maken met Azure AD met behulp van Microsoft Authentication Library voor .NET (MSAL.NET).
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
ms.openlocfilehash: dbf08e23b2bc1f657363f69df55763437e6c8a90
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76695043"
---
# <a name="providing-your-own-httpclient-and-proxy-using-msalnet"></a>Uw eigen HttpClient en proxy bieden met behulp van MSAL.NET
Bij [het initialiseren van een openbare clientapplicatie](msal-net-initializing-client-applications.md)u de `.WithHttpClientFactory method` toepassing gebruiken om uw eigen HttpClient aan te bieden.  Het verstrekken van uw eigen HttpClient maakt geavanceerde scenario's mogelijk, zoals fijnmazige besturing van een HTTP-proxy, het aanpassen van user agent headers of het dwingen van MSAL om een specifieke HttpClient te gebruiken (bijvoorbeeld in ASP.NET Core web apps/API's).

## <a name="initialize-with-httpclientfactory"></a>Initialiseren met HttpClientFactory
In het volgende voorbeeld `HttpClientFactory` wordt weergegeven om een openbare clienttoepassing te maken en vervolgens hiermee te initialiseren:

```csharp
IMsalHttpClientFactory httpClientFactory = new MyHttpClientFactory();

var pca = PublicClientApplicationBuilder.Create(MsalTestConstants.ClientId) 
                                        .WithHttpClientFactory(httpClientFactory)
                                        .Build();
```

## <a name="httpclient-and-xamarin-ios"></a>HttpClient en Xamarin iOS
Bij het gebruik van Xamarin iOS `HttpClient` wordt aanbevolen om `NSURLSession`een bewerking te maken die expliciet de op basis van handler voor iOS 7 en nieuwer gebruikt. MSAL.NET maakt automatisch `HttpClient` een `NSURLSessionHandler` die wordt gebruikt voor iOS 7 en nieuwer. Lees voor meer informatie de [Xamarin iOS-documentatie voor HttpClient.](/xamarin/cross-platform/macios/http-stack)