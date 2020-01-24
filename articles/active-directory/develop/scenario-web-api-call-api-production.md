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
ms.openlocfilehash: 474f771f007666179295f4502108acee88d1dc33
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701719"
---
# <a name="a-web-api-that-calls-web-apis-move-to-production"></a>Een web-API die web-Api's aanroept: verplaatsen naar productie

Nadat u een token hebt aangeschaft om Web-Api's aan te roepen, kunt u uw app naar productie verplaatsen.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="learn-more"></a>Meer informatie

Nu u de basis principes kent van het aanroepen van web-Api's vanuit uw eigen web-API, bent u mogelijk geïnteresseerd in de volgende zelf studie, waarmee de code wordt beschreven die wordt gebruikt voor het bouwen van een beveiligde web-API die web-Api's aanroept.

| Voorbeeld | Platform | Beschrijving |
|--------|----------|-------------|
| [active-directory-aspnetcore-webapi-tutorial-v2](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/tree/master/2.%20Web%20API%20now%20calls%20Microsoft%20Graph) | ASP.NET Core 2,2 Web-API, Desktop (WPF) | ASP.NET Core 2,2 Web API-aanroepen Microsoft Graph, die u aanroept vanuit een WPF-toepassing met behulp van het micro soft Identity platform (v 2.0). |
