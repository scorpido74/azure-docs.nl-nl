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
ms.openlocfilehash: 1426ad250e18d0132e116162a374120dda2e1200
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/16/2020
ms.locfileid: "76044131"
---
# <a name="a-web-api-that-calls-web-apis-move-to-production"></a>Een web-API die web-Api's aanroept: verplaatsen naar productie

Nadat u een token hebt aangeschaft om Web-Api's aan te roepen, kunt u uw app naar productie verplaatsen.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="learn-more"></a>Meer informatie

Nu u de basis principes kent van het aanroepen van web-Api's vanuit uw eigen web-API, bent u mogelijk geïnteresseerd in de volgende zelf studie, waarmee de code wordt beschreven die wordt gebruikt voor het bouwen van een beveiligde web-API die web-Api's aanroept.

| Voorbeeld | Platform | Beschrijving |
|--------|----------|-------------|
| [active-directory-aspnetcore-webapi-tutorial-v2](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/tree/master/2.%20Web%20API%20now%20calls%20Microsoft%20Graph) | ASP.NET Core 2,2 Web-API, Desktop (WPF) | ASP.NET Core 2,2 Web API-aanroepen Microsoft Graph, die u aanroept vanuit een WPF-toepassing met behulp van het micro soft Identity platform (v 2.0). |
