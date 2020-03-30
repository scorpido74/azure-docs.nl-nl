---
title: Een daemon-app verplaatsen die web-API's naar productie roept - Microsoft-identiteitsplatform | Azure
description: Meer informatie over het verplaatsen van een daemon-app die web-API's naar productie roept
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
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: db5f52c95daf4e93c140b4c93f39dad19971319d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262618"
---
# <a name="daemon-app-that-calls-web-apis---move-to-production"></a>Daemon app die web API's aanroept - overstappen naar productie

Nu u weet hoe u een token aanschaffen en gebruiken voor een service-to-service-gesprek, leest u hoe u uw app naar productie verplaatsen.

## <a name="deployment---multitenant-daemon-apps"></a>Implementatie - daemon-apps met meerdere tenant's

Als u een ISV bent die een daemon-toepassing maakt die in verschillende tenants kan worden uitgevoerd, moet u ervoor zorgen dat de tenantbeheerder:

- Bepalingen een serviceprincipal voor de toepassing.
- Verleent toestemming voor de aanvraag.

U moet uw klanten uitleggen hoe u deze bewerkingen moet uitvoeren. Zie [Toestemming aanvragen voor een volledige tenant voor](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant)meer informatie.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>Volgende stappen

Hier zijn een paar links om u te helpen meer te weten te komen:

# <a name="net"></a>[.NET](#tab/dotnet)

- Snelstart: [een token en aanroep Microsoft Graph API van een console-app met behulp van de identiteit van de app.](./quickstart-v2-netcore-daemon.md)
- Referentiedocumentatie voor:
  - Instantiating [ConfidentialClientApplication](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationbuilder).
  - Bellen [naar AcquiretokenForClient](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.acquiretokenforclientparameterbuilder).
- Andere voorbeelden / tutorials:
  - [Microsoft-identity-platform-console-daemon](https://github.com/Azure-Samples/microsoft-identity-platform-console-daemon) beschikt over een eenvoudige .NET Core daemon-consoletoepassing die de gebruikers van een tenant die Microsoft Graph opvraagt, weergeeft.

    ![Voorbeeld van daemon-app-topologie](media/scenario-daemon-app/daemon-app-sample.svg)

    Hetzelfde voorbeeld illustreert ook een variatie met certificaten:

    ![Voorbeeld van daemon-app-topologie - certificaten](media/scenario-daemon-app/daemon-app-sample-with-certificate.svg)

  - [microsoft-identity-platform-aspnet-webapp-daemon](https://github.com/Azure-Samples/microsoft-identity-platform-aspnet-webapp-daemon) beschikt over een ASP.NET MVC-webapplicatie die gegevens uit Microsoft Graph synchroniseert met behulp van de identiteit van de toepassing in plaats van namens een gebruiker. Dit voorbeeld illustreert ook het beheerderstoestemmingsproces.

    ![topologie](media/scenario-daemon-app/damon-app-sample-web.svg)

# <a name="python"></a>[Python](#tab/python)

Probeer de quickstart [Een token verwerven en bel Microsoft Graph API vanuit een Python-console-app met de identiteit van de app.](./quickstart-v2-python-daemon.md)

# <a name="java"></a>[Java](#tab/java)

MSAL Java is momenteel in openbare preview. Voor meer info, zie [MSAL Java dev monsters](https://github.com/AzureAD/microsoft-authentication-library-for-java/tree/dev/src/samples).

---
