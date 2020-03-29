---
title: Een web-app registreren die web-API's aanroept - Microsoft-identiteitsplatform | Azure
description: Meer informatie over het registreren van een webapp die web-API's aanroept
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
ms.openlocfilehash: 5a57fcef3569734964bf6e8a41faa49800798f9b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76759054"
---
# <a name="a-web-app-that-calls-web-apis-app-registration"></a>Een web-app die web-API's aanroept: app-registratie

Een web-app die web-API's aanroept, heeft dezelfde registratie als een web-app waarin gebruikers worden aanmelding. Volg dus de instructies in [een web-app die gebruikers aanmeldt: App-registratie](scenario-web-app-sign-user-app-registration.md).

Echter, omdat de web-app nu ook web API's noemt, wordt het een vertrouwelijke clienttoepassing. Daarom is wat extra registratie vereist. De app moet clientreferenties of *geheimen*delen met het Microsoft-identiteitsplatform.

[!INCLUDE [Registration of client secrets](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>API-machtigingen

Web-apps bellen API's namens de aangemelde gebruiker. Om dat te doen, moeten ze *gedelegeerde machtigingen*aanvragen. Zie Machtigingen [toevoegen om toegang te krijgen tot web-API's](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een web-app die web-API's aanroept: codeconfiguratie](scenario-web-app-call-api-app-configuration.md)
