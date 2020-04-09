---
title: Gebruikers aanmelden via een web-app - Microsoft-identiteitsplatform | Azure
description: Meer informatie over het bouwen van een web-app die zich aanmeldt bij gebruikers (overzicht)
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
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80881482"
---
# <a name="scenario-web-app-that-signs-in-users"></a>Scenario: web-app die zich aanmeldt bij gebruikers

Lees alles wat u nodig hebt om een web-app te bouwen die het Microsoft-identiteitsplatform gebruikt om gebruikers aan te melden.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Aan de slag

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Als u uw eerste draagbare (ASP.NET Core)-webapp wilt maken die gebruikers aanmeldt, volgt u deze quickstart:

> [!div class="nextstepaction"]
> [Snelstart: ASP.NET Core-web-app die gebruikers aanmeldt](quickstart-v2-aspnet-core-webapp.md)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Als u wilt weten hoe u aanmelding toevoegt aan een bestaande ASP.NET-webtoepassing, probeert u de volgende snelstart:

> [!div class="nextstepaction"]
> [Snelstart: ASP.NET web-app die gebruikers aanmeldt](quickstart-v2-aspnet-webapp.md)

# <a name="java"></a>[Java](#tab/java)

Als je een Java-ontwikkelaar bent, probeer je de volgende snelstart:

> [!div class="nextstepaction"]
> [Snelstart: aanmelden met Microsoft toevoegen aan een Java-web-app](quickstart-v2-java-webapp.md)

# <a name="python"></a>[Python](#tab/python)

Als u zich ontwikkelt met Python, probeert u de volgende snelstart:

> [!div class="nextstepaction"]
> [Snelstart: aanmelden met Microsoft toevoegen aan een Python-web-app](quickstart-v2-python-webapp.md)

---

## <a name="overview"></a>Overzicht

U voegt verificatie toe aan uw web-app, zodat deze gebruikers kan aanmelden. Door verificatie toe te voegen, heeft uw web-app toegang tot beperkte profielgegevens om de ervaring voor gebruikers aan te passen. 

Web-apps verifiëren een gebruiker in een webbrowser. In dit scenario stuurt de web-app de browser van de gebruiker aan om deze aan te melden bij Azure Active Directory (Azure AD). Azure AD retourneert een aanmeldingsreactie via de browser van de gebruiker, die claims over de gebruiker bevat in een beveiligingstoken. Aanmelden van gebruikers maakt gebruik van het [Open ID Connect](./v2-protocols-oidc.md) standaardprotocol, vereenvoudigd door het gebruik van middleware-bibliotheken. [libraries](scenario-web-app-sign-user-app-configuration.md#libraries-for-protecting-web-apps)

![Web app-aan-man bij gebruikers](./media/scenario-webapp/scenario-webapp-signs-in-users.svg)

Als tweede fase u uw toepassing inschakelen om web-API's te bellen namens de aangemelde gebruiker. Deze volgende fase is een ander scenario, dat u vindt in [web-app die web API's aanroept.](scenario-web-app-call-api-overview.md)

> [!NOTE]
> Het toevoegen van aanmelding aan een web-app gaat over het beschermen van de web-app en het valideren van een gebruikerstoken, wat **middleware-bibliotheken** doen. In het geval van .NET vereist dit scenario nog geen Microsoft Authentication Library (MSAL), die gaat over het verkrijgen van een token om beveiligde API's aan te roepen. Verificatiebibliotheken worden geïntroduceerd in het vervolgscenario, wanneer de web-app web-API's moet aanroepen.

## <a name="specifics"></a>Details

- Tijdens de registratie van de toepassing moet u een of meerdere (als u uw app op verschillende locaties implementeert) e-URL's beantwoorden. In sommige gevallen (ASP.NET en ASP.NET Core) moet u het ID-token inschakelen. Ten slotte wilt u een afmelduri instellen, zodat uw toepassing reageert op gebruikers die zich afmelden.
- In de code voor uw toepassing moet u de bevoegdheid verstrekken waarop uw web-app zich aanmeldt. U tokenvalidatie aanpassen (met name in partnerscenario's).
- Webtoepassingen ondersteunen alle accounttypen. Zie [Ondersteunde accounttypen](v2-supported-account-types.md)voor meer informatie .

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
