---
title: Een specifieke API vinden die nodig is voor een speciaal ontwikkelde toepassing | Microsoft Docs
description: De machtigingen configureren die u nodig hebt voor toegang tot een bepaalde API in uw aangepaste, ontwikkelde Azure AD-toepassing
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: ryanwi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 66c223df28f8082125d48f1e3619088de5cf2687
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74844597"
---
# <a name="how-to-find-a-specific-api-needed-for-a-custom-developed-application"></a>Een specifieke API vinden die nodig is voor een speciaal ontwikkelde toepassing

Toegang tot Api's vereist configuratie van toegangs scopes en rollen. Als u de Web-Api's van uw resource toepassing beschikbaar wilt maken voor client toepassingen, moet u de toegangs scopes en rollen voor de API configureren. Als u een-client toepassing toegang wilt geven tot een web-API, moet u machtigingen configureren voor toegang tot de API in de app-registratie.

## <a name="configuring-a-resource-application-to-expose-web-apis"></a>Een resourcetoepassing configureren voor het beschikbaar maken van web-API's

Wanneer u uw web-API beschikbaar maakt, wordt de API weer gegeven in de lijst **een API selecteren** wanneer u machtigingen toevoegt aan een app-registratie. Volg de stappen die worden beschreven in [een toepassing configureren om Web-api's](quickstart-configure-app-expose-web-apis.md)weer te geven om toegangs scopes toe te voegen.

## <a name="configuring-a-client-application-to-access-web-apis"></a>Een client toepassing configureren voor toegang tot Web-Api's

Wanneer u machtigingen toevoegt voor uw app-registratie, kunt u **API-toegang toevoegen** aan weer gegeven Web-api's. Als u toegang wilt krijgen tot Web-Api's, volgt u de stappen in [een client toepassing configureren voor toegang tot Web-api's](quickstart-configure-app-access-web-apis.md).

## <a name="next-steps"></a>Volgende stappen

- [Informatie over het Azure Active Directory toepassings manifest](https://docs.microsoft.com/azure/active-directory/develop/active-directory-application-manifest)
