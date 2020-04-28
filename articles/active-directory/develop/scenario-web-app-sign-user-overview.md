---
title: Gebruikers aanmelden bij een web-app-micro soft Identity-platform | Azure
description: Meer informatie over het bouwen van een web-app die gebruikers aanmeldt (overzicht)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/17/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 65d254cec5735c54e19f5adfde57fb6aed776a2c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80881482"
---
# <a name="scenario-web-app-that-signs-in-users"></a>Scenario: Web-app die gebruikers aantekent

Meer informatie over wat u nodig hebt om een web-app te bouwen die gebruikmaakt van het micro soft Identity-platform om gebruikers aan te melden.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Aan de slag

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Als u uw eerste Portable (ASP.NET Core) Web-App wilt maken die zich aanmeldt bij gebruikers, volgt u deze Snelstartgids:

> [!div class="nextstepaction"]
> [Quick Start: ASP.NET Core web-app die gebruikers ondertekent](quickstart-v2-aspnet-core-webapp.md)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Als u wilt weten hoe u aanmelden kunt toevoegen aan een bestaande ASP.NET-webtoepassing, probeert u de volgende Snelstartgids:

> [!div class="nextstepaction"]
> [Quick Start: ASP.NET-Web-app die gebruikers aanmeldt](quickstart-v2-aspnet-webapp.md)

# <a name="java"></a>[Java](#tab/java)

Als u een Java-ontwikkelaar bent, kunt u de volgende Snelstartgids proberen:

> [!div class="nextstepaction"]
> [Snelstartgids: aanmelden toevoegen met micro soft aan een Java-Web-app](quickstart-v2-java-webapp.md)

# <a name="python"></a>[Python](#tab/python)

Als u met python ontwikkelt, voert u de volgende Snelstartgids uit:

> [!div class="nextstepaction"]
> [Snelstartgids: aanmelden toevoegen met micro soft aan een Python-web-app](quickstart-v2-python-webapp.md)

---

## <a name="overview"></a>Overzicht

U voegt verificatie toe aan uw web-app zodat gebruikers zich kunnen aanmelden. Door verificatie toe te voegen kan uw web-app toegang krijgen tot beperkte profiel gegevens om de gebruikers ervaring aan te passen. 

Web-apps verifiëren een gebruiker in een webbrowser. In dit scenario wordt de browser van de gebruiker door de web-app doorgestuurd om deze aan te melden bij Azure Active Directory (Azure AD). Azure AD retourneert een aanmeldings reactie via de browser van de gebruiker, die claims bevat over de gebruiker in een beveiligings token. Bij het aanmelden van gebruikers wordt gebruikgemaakt van het [Open-ID Connect-](./v2-protocols-oidc.md) standaard protocol, vereenvoudigd door het gebruik van middleware- [bibliotheken](scenario-web-app-sign-user-app-configuration.md#libraries-for-protecting-web-apps).

![Web-app-ondertekening in gebruikers](./media/scenario-webapp/scenario-webapp-signs-in-users.svg)

Als tweede fase kunt u ervoor zorgen dat uw toepassing namens de aangemelde gebruiker Web-Api's aanroept. Deze volgende fase is een ander scenario dat u vindt in web- [apps die web-api's aanroepen](scenario-web-app-call-api-overview.md).

> [!NOTE]
> Het toevoegen van een aanmelding aan een web-app is het beveiligen van de web-app en het valideren van een gebruikers token. Dit zijn de **middleware** -bibliotheken. In het geval van .NET hebt u voor dit scenario nog geen micro soft Authentication Library (MSAL) nodig. Dit is het verkrijgen van een token voor het aanroepen van beveiligde Api's. Verificatie bibliotheken worden geïntroduceerd in het vervolg scenario, wanneer de web-app Web-Api's moet aanroepen.

## <a name="specifics"></a>Opsporingsgegevens

- Tijdens de registratie van de toepassing moet u een of meer opgeven (als u uw app op verschillende locaties implementeert) antwoord-Uri's. In sommige gevallen (ASP.NET en ASP.NET Core) moet u het ID-token inschakelen. Ten slotte wilt u een afmeldings-URI instellen, zodat uw toepassing reageert op gebruikers die zich afmelden.
- In de code voor uw toepassing moet u de instantie opgeven waarnaar uw web-app zich aanmeldt. Mogelijk wilt u de token validatie aanpassen (met name in partner scenario's).
- Webtoepassingen ondersteunen elk account type. Zie [supported account types](v2-supported-account-types.md)(Engelstalig) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

> [!div class="nextstepaction"]
> [App-registratie](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-app-registration?tabs=aspnetcore)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

> [!div class="nextstepaction"]
> [App-registratie](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-app-registration?tabs=aspnet)

# <a name="java"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [App-registratie](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-app-registration?tabs=java)

# <a name="python"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [App-registratie](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-app-registration?tabs=python)

---
