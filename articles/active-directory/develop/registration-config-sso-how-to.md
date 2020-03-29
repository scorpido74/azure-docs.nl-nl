---
title: Toepassingsmelding configureren | Microsoft Documenten
description: Eenmalige aanmelding configureren voor een aangepaste toepassing die u ontwikkelt en registreert bij Azure AD.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/15/2019
ms.author: ryanwi
ms.openlocfilehash: bb77f376e22428e9259ff3efc84cf6f1cb3491fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76702638"
---
# <a name="how-to-configure-single-sign-on-for-an-application"></a>Eenmalige aanmelding configureren voor een toepassing

Het inschakelen van fedderated single sign-on (SSO) in uw app wordt automatisch ingeschakeld wanneer u via Azure AD voor OpenID Connect, SAML 2.0 of WS-Fed wordt gefedereerd. Als uw eindgebruikers zich moeten aanmelden ondanks dat er al een bestaande sessie met Azure AD is, is het waarschijnlijk dat uw app verkeerd is geconfigureerd.

* Als u ADAL/MSAL gebruikt, controleert u of **PromptBehavior** is ingesteld op **Automatisch** in plaats van **Altijd.**

* Als u een mobiele app bouwt, hebt u mogelijk extra configuraties nodig om brokered of niet-brokered SSO in te schakelen.

Zie Cross [App SSO inschakelen in Android](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-android)voor Android .<br>

Zie [Cross App SSO inschakelen in iOS](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-ios)voor iOS .

## <a name="next-steps"></a>Volgende stappen

[Azure AD SSO](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)<br>

[Cross App SSO inschakelen in Android](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-android)<br>

[Cross App SSO inschakelen in iOS](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-ios)<br>

[Apps integreren in AzureAD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)<br>

[Toestemming en machtigingen voor geconvergeerde apps voor AzureAD v2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)<br>

[AzureAD StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)
