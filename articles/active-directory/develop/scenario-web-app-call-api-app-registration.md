---
title: Een web-app registreren die web-API's aanroept - Microsoft-identiteitsplatform | Azure
description: Meer informatie over het registreren van een webapp die web-API's aanroept
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
ms.openlocfilehash: 8cb7d86bd419563363779c499962c81f0c59e3b6
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80881873"
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
