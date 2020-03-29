---
title: Problemen bij het aanmelden bij een op maat ontwikkelde toepassing | Microsoft Documenten
description: Veelvoorkomende fouten waardoor u zich niet aanmelden bij een toepassing die u met Azure AD hebt ontwikkeld
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: b8ad2499aea8bf4e41ca00d6c78d76e112f0493e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "65825241"
---
# <a name="problems-signing-in-to-a-custom-developed-application"></a>Problemen bij het aanmelden bij een op maat ontwikkelde toepassing

Er zijn verschillende fouten waardoor u zich niet aanmelden bij een app. De grootste reden waarom mensen dit probleem tegenkomen, zijn verkeerd geconfigureerde apps.

## <a name="errors-related-to--misconfigured-apps"></a>Fouten in verband met verkeerd geconfigureerde apps

* Controleer of beide configuraties in de portal overeenkomen met wat u in uw app hebt. Vergelijk specifiek client-/toepassings-id, antwoord-URL's, clientgeheimen/sleutels en URI voor app-id's.

* Vergelijk de resource waartoe u toegang in code aanvraagt met de geconfigureerde machtigingen op het tabblad **Vereiste resources** om ervoor te zorgen dat u alleen resources opvraagt die u hebt geconfigureerd.

* Zie [Azure AD StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory) voor vergelijkbare fouten of problemen.

## <a name="next-steps"></a>Volgende stappen

[Azure AD-ontwikkelaarshandleiding](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)<br>

[Apps in- en integreren van apps in Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)<br>

[Toestemming en machtigingen voor geconvergeerde apps voor Azure AD v2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)<br>

[Azure AD StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory>)
