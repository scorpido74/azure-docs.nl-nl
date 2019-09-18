---
title: Daemon-app die web-Api's aanroept (naar productie)-micro soft Identity-platform
description: Meer informatie over het bouwen van een daemon-app die web-Api's aanroept (verplaatsen naar productie)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/15/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: c509e061c43c81f72682fb428529a8e72b34066a
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71056327"
---
# <a name="daemon-app-that-calls-web-apis---move-to-production"></a>Daemon-app voor het aanroepen van web-Api's-verplaatsen naar productie

Nu u weet hoe u een token kunt verkrijgen en gebruiken voor een service-naar-service-oproep, leert u hoe u uw app naar productie kunt verplaatsen.

## <a name="deployment---case-of-multi-tenant-daemon-apps"></a>Implementatie van multi tenant-daemon-apps

Als u een ISV-toepassing maakt die in meerdere tenants kan worden uitgevoerd, moet u ervoor zorgen dat de Tenant beheerders:

- Een service-principal voor de toepassing inrichten
- Verleent toestemming voor de toepassing

U moet uitleggen wat uw klanten zijn om deze bewerkingen uit te voeren. Zie [toestemming vragen voor een hele Tenant](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant)voor meer informatie.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>Volgende stappen

Hier volgen enkele koppelingen voor meer informatie:

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

- Als u dat nog niet hebt gedaan, kunt u het beste [een token aanschaffen en Microsoft Graph-API aanroepen vanuit een console-app met behulp van de identiteit van de app](./quickstart-v2-netcore-daemon.md).
- Referentie documentatie voor:
  - [ConfidentialClientApplication](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationbuilder) instantiëren
  - [AcquireTokenForClient](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.acquiretokenforclientparameterbuilder) aanroepen
- Andere voor beelden/zelf studies:
  - [micro soft-Identity-platform-console-daemon](https://github.com/Azure-Samples/microsoft-identity-platform-console-daemon) bevat een eenvoudige .net core daemon-console toepassing die de gebruikers van een Tenant die query's uitvoert op de Microsoft Graph.

    ![topologie](media/scenario-daemon-app/daemon-app-sample.svg)

    Hetzelfde voor beeld illustreert ook de variatie met certificaten.

    ![topologie](media/scenario-daemon-app/daemon-app-sample-with-certificate.svg)

  - [micro soft-Identity-platform-ASPNET-webapp-daemon](https://github.com/Azure-Samples/microsoft-identity-platform-aspnet-webapp-daemon) bevat een ASP.NET MVC-webtoepassing die gegevens synchroniseert van Microsoft Graph met behulp van de identiteit van de toepassing, in plaats van namens een gebruiker. Het voor beeld illustreert ook het proces voor de toestemming van de beheerder.

    ![topologie](media/scenario-daemon-app/damon-app-sample-web.svg)

# <a name="pythontabpython"></a>[Python](#tab/python)

MSAL python is momenteel beschikbaar als open bare preview.
Zie voor meer informatie [MSAL python in voor beelden voor opslag](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/dev/sample)plaatsen.

# <a name="javatabjava"></a>[Java](#tab/java)

msal4j (MSAL. Java) is momenteel beschikbaar als open bare preview. Zie [MSAL java in-repository voor beelden](https://github.com/AzureAD/microsoft-authentication-library-for-java/tree/dev/src/samples)voor meer informatie.

---
