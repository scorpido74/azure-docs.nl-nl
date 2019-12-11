---
title: Web-API aanroepen van web-Api's naar productie-micro soft Identity-platform | Azure
description: Meer informatie over het verplaatsen van een web-API die web-Api's aanroept voor productie.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0d59a5b2a74c10e36103713725113cbe8c9cc412
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74965166"
---
# <a name="web-api-that-calls-web-apis---move-to-production"></a>Web-API voor het aanroepen van web-Api's-verplaatsen naar productie

Zodra u een token hebt aangeschaft om Web-Api's aan te roepen, kunt u uw app naar productie verplaatsen.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="learn-more"></a>Meer informatie

Nu u de basis principes kent van het aanroepen van web-Api's vanuit uw eigen web-API, bent u mogelijk geïnteresseerd in deze zelf studie, waarmee de code wordt beschreven die wordt gebruikt voor het bouwen van een beveiligde web API die web-Api's aanroept.

| Voorbeeld | Platform | Beschrijving |
|--------|----------|-------------|
| [active-directory-aspnetcore-webapi-tutorial-v2](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/tree/master/2.%20Web%20API%20now%20calls%20Microsoft%20Graph) | ASP.NET Core 2,2 Web-API, Desktop (WPF) | ASP.NET Core 2,2 Web-API die Microsoft Graph onderroept, wordt aangeroepen vanuit een WPF-toepassing met behulp van het micro soft Identity platform (v 2.0) |
