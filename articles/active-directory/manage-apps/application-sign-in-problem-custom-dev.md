---
title: Problemen bij het aanmelden bij een aangepaste toepassing | Microsoft Docs
description: Veelvoorkomende fouten die ertoe kunnen leiden dat u zich niet kunt aanmelden bij een toepassing die u hebt ontwikkeld met Azure AD
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: cdfc2a39c6bd3b68df7feb978d2548ad67631235
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84759127"
---
# <a name="problems-signing-in-to-a-custom-developed-application"></a>Problemen bij het aanmelden bij een aangepaste toepassing

Er zijn verschillende fouten die ertoe kunnen leiden dat u zich niet kunt aanmelden bij een app. De grootste reden dat mensen dit probleem tegen komen, zijn niet-geconfigureerde apps.

## <a name="errors-related-to--misconfigured-apps"></a>Fouten met betrekking tot onjuist geconfigureerde apps

* Controleer of beide configuraties in de portal overeenkomen met wat u in uw app hebt. Vergelijk met name de client/toepassings-ID, antwoord-Url's, client geheimen/sleutels en de URI van de App-ID.

* Vergelijk de resource waarmee u toegang vraagt in code met de geconfigureerde machtigingen op het tabblad **vereiste resources** om ervoor te zorgen dat u alleen de resources opvraagt die u hebt geconfigureerd.

* Zie [Azure AD stack overflow](https://stackoverflow.com/questions/tagged/azure-active-directory) voor vergelijk bare fouten of problemen.

## <a name="next-steps"></a>Volgende stappen

[Ontwikkelaars handleiding voor Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)<br>

[Apps goed keuren en integreren in azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)<br>

[Machtigingen en toestemming in het eindpunt van het Microsoft-identiteitsplatform](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)<br>

[Azure AD stack overflow](https://stackoverflow.com/questions/tagged/azure-active-directory>)
