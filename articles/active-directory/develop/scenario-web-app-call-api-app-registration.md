---
title: Een web-app registreren die web-Api's aanroept-micro soft Identity-platform | Azure
description: Meer informatie over het registreren van een web-app die web-Api's aanroept
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
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/26/2020
ms.locfileid: "76759054"
---
# <a name="a-web-app-that-calls-web-apis-app-registration"></a>Een web-app die web-Api's aanroept: app-registratie

Een web-app die web-Api's aanroept, heeft dezelfde registratie als een web-app die gebruikers ondertekent in. Volg de instructies in [een web-app die zich in gebruikers aanmeldt: app-registratie](scenario-web-app-sign-user-app-registration.md).

Echter, omdat de web-app nu ook Web-Api's aanroept, wordt het een vertrouwelijke client toepassing. Daarom is een extra registratie vereist. De app moet client referenties of *geheimen*delen met het micro soft Identity-platform.

[!INCLUDE [Registration of client secrets](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>API-machtigingen

Web apps roepen namens de aangemelde gebruiker Api's aan. Hiervoor moeten ze *gedelegeerde machtigingen*aanvragen. Zie [machtigingen voor toegang tot Web-Api's toevoegen](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een web-app die web-Api's aanroept: code configuratie](scenario-web-app-call-api-app-configuration.md)
