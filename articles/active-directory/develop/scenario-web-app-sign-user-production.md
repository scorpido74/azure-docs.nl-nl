---
title: Web-app die gebruikers aanmeldt (verplaatsen naar productie)-micro soft Identity-platform
description: Meer informatie over het bouwen van een web-app die wordt aangemeld bij gebruikers (verplaatsen naar productie)
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
ms.date: 09/17/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: bd6717c132d32d54c16e7678695a09e35181a057
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/18/2019
ms.locfileid: "71086549"
---
# <a name="web-app-that-signs-in-users---move-to-production"></a>Web-app die gebruikers aanmeldt-verplaatsen naar productie

Nu u weet hoe u een token kunt verkrijgen om Web-Api's aan te roepen, leert u hoe u het kunt verplaatsen naar productie.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>Volgende stappen

### <a name="calling-web-apis-scenario"></a>Scenario voor web-Api's aanroepen

Als uw web-app zich aanmeldt, kan de gebruiker Web-Api's namens de aangemelde gebruikers aanroepen. Het aanroepen van web-Api's vanuit de web-app is het object van het volgende scenario:

> [!div class="nextstepaction"]
> [Web-app die web-API's aanroept](scenario-web-app-call-api-overview.md)

### <a name="deep-dive---aspnet-core-web-app-tutorial"></a>Diep gaande kennis-ASP.NET Core zelf studie over de web-app

Meer informatie over andere manieren waarop gebruikers zich kunnen aanmelden met de ASP.NET Core zelf studie: [MS-Identity-aspnetcore-webapp-zelf studie](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial). Dit voor beeld is een progressieve zelf studie met code voor productie gereed voor een web-app, inclusief het toevoegen van aanmelden met accounts in:

- uw organisatie,
- meerdere organisaties,
- werk-of school accounts of persoonlijke Microsoft-account,
- met [Azure AD B2C](https://aka.ms/aadb2c),
- of in nationale Clouds.

### <a name="sample-code---java-web-app"></a>Voorbeeld code-Java-Web-app

Meer informatie over de Java-Web-app vanuit het voor beeld op GitHub: [Een Java-webtoepassing die zich aanmeldt bij gebruikers met het micro soft-identiteits platform en aanroepen Microsoft Graph](https://github.com/Azure-Samples/ms-identity-java-webapp)
