---
title: Web-API aanroepen van web-Api's naar productie-micro soft Identity-platform | Azure
description: Meer informatie over het verplaatsen van een web-API die web-Api's aanroept voor productie.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 078ed3e5f3a19bfa4350f9edea858b717c69e3f8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "81537148"
---
# <a name="a-web-api-that-calls-web-apis-move-to-production"></a>Een web-API die web-Api's aanroept: verplaatsen naar productie

Nadat u een token hebt aangeschaft om Web-Api's aan te roepen, kunt u uw app naar productie verplaatsen.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="learn-more"></a>Lees meer

Nu u de basis principes kent van het aanroepen van web-Api's vanuit uw eigen web-API, bent u mogelijk geïnteresseerd in de volgende zelf studie, waarmee de code wordt beschreven die wordt gebruikt voor het bouwen van een beveiligde web-API die web-Api's aanroept.

| Voorbeeld | Platform | Beschrijving |
|--------|----------|-------------|
| [Active-Directory-aspnetcore-webapi-zelf studie-v2](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/tree/master/2.%20Web%20API%20now%20calls%20Microsoft%20Graph) | ASP.NET Core 2,2 Web-API, Desktop (WPF) | ASP.NET Core 2,2 Web API-aanroepen Microsoft Graph, die u aanroept vanuit een WPF-toepassing met behulp van het micro soft Identity platform (v 2.0). |
