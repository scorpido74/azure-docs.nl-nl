---
title: Web API's voor weboproepen naar productie verplaatsen - Microsoft-identiteitsplatform | Azure
description: Meer informatie over het verplaatsen van een web-API die web-API's naar productie aanroept.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76701719"
---
# <a name="a-web-api-that-calls-web-apis-move-to-production"></a>Een web-API die web-API's aanroept: naar productie gaan

Nadat u een token hebt aangeschaft om web-API's te bellen, u uw app naar productie verplaatsen.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="learn-more"></a>Meer informatie

Nu u de basisprincipes kent van het aanroepen van webAPI's vanuit uw eigen web-API, bent u mogelijk geïnteresseerd in de volgende zelfstudie, waarin de code wordt beschreven die wordt gebruikt om een beveiligde web-API te bouwen die web-API's aanroept.

| Voorbeeld | Platform | Beschrijving |
|--------|----------|-------------|
| [active-directory-aspnetcore-webapi-tutorial-v2](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/tree/master/2.%20Web%20API%20now%20calls%20Microsoft%20Graph) | ASP.NET Core 2.2 Web API, Desktop (WPF) | ASP.NET Core 2.2 Web API-aanroepen Microsoft Graph, die u vanuit een WPF-toepassing aanroept met behulp van het Microsoft-identiteitsplatform (v2.0). |
