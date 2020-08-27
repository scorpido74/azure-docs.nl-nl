---
title: Een app met één pagina verplaatsen naar productie-micro soft Identity-platform | Azure
description: Meer informatie over het bouwen van een toepassing met één pagina (verplaatsen naar productie)
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 21ba0193c3f1e19ffc74452aaceee34759c7e606
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/27/2020
ms.locfileid: "88949011"
---
# <a name="single-page-application-move-to-production"></a>Toepassing met één pagina: verplaatsen naar productie

Nu u weet hoe u een token kunt verkrijgen om Web-Api's aan te roepen, leert u hoe u kunt overstappen op productie.

## <a name="improve-your-app"></a>Uw app verbeteren

[Schakel logboek registratie](msal-logging.md) in om uw app-productie gereed te maken.

## <a name="test-your-integration"></a>Uw integratie testen

Test uw integratie door de [controle lijst voor de integratie van micro soft Identity platform](identity-platform-integration-checklist.md)te volgen.

## <a name="deploy-your-app"></a>Uw app implementeren

Bekijk een voor [beeld](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnet-webapi-multitenant/tree/master/Chapter3) van de implementatie om te leren hoe u uw spa-en Web-API-projecten kunt implementeren met Azure Storage en Azure-app Services. 

## <a name="next-steps"></a>Volgende stappen

Grondige kennis van het Snelstartgids-voor beeld, waarin de code wordt uitgelegd voor het aanmelden van gebruikers en het verkrijgen van een toegangs token om de **Microsoft Graph-API** aan te roepen met behulp van **MSAL.js**:

> [!div class="nextstepaction"]
> [Java script-zelf studie](./tutorial-v2-javascript-spa.md)

Voor beeld dat laat zien hoe u tokens kunt ophalen voor uw eigen back-end web-API (ASP.NET Core) met behulp van **MSAL.js**:

> [!div class="nextstepaction"]
> [Beveiligd-wachtwoord verificatie met een ASP.NET-back-end](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnetcore-webapi)

Voor beeld dat laat zien hoe u toegangs tokens voor uw back-end web-API (Node.js) kunt valideren met behulp van **Pass Port-Azure-AD**.

> [!div class="nextstepaction"]
> [Node.js Web-API (Azure AD)](https://github.com/Azure-Samples/active-directory-javascript-nodejs-webapi-v2)

Voor beeld waarin wordt uitgelegd hoe u **MSAL.js** kunt gebruiken om gebruikers aan te melden bij een app die is geregistreerd met **Azure Active Directory B2C** (Azure AD B2C):

> [!div class="nextstepaction"]
> [Beveiligd-wachtwoord verificatie met Azure AD B2C](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp)

Voor beeld waarin wordt uitgelegd hoe u **Pass Port-Azure-AD** gebruikt om toegangs tokens te valideren voor apps die zijn geregistreerd met **Azure Active Directory B2C** (Azure AD B2C)

> [!div class="nextstepaction"]
> [Node.js Web-API (Azure AD B2C)](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi)
