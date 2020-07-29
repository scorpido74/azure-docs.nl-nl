---
title: Eenmalige aanmelding voor de toepassing configureren
description: Eenmalige aanmelding configureren voor een aangepaste toepassing die u ontwikkelt en registreert met Azure AD.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: conceptual
ms.date: 07/15/2019
ms.author: ryanwi
ms.openlocfilehash: 9b6451d29a3a874e09d74cbe664662a395b647f9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "82890469"
---
# <a name="how-to-configure-single-sign-on-for-an-application"></a>Eenmalige aanmelding voor een toepassing configureren

Het inschakelen van federatieve eenmalige aanmelding (SSO) in uw app wordt automatisch ingeschakeld wanneer federeren via Azure AD voor OpenID Connect Connect, SAML 2,0 of WS-inschakelt. Als uw eind gebruikers zich moeten aanmelden ondanks al een bestaande sessie met Azure AD, is het waarschijnlijk dat uw app onjuist is geconfigureerd.

* Als u ADAL/MSAL gebruikt, zorg er dan voor dat u **PromptBehavior** hebt ingesteld op **auto** in plaats van **altijd**.

* Als u een mobiele app bouwt, hebt u mogelijk aanvullende configuraties nodig om brokered of niet-brokered SSO in te scha kelen.

Zie [SSO van meerdere apps inschakelen in Android](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-android)voor Android.<br>

Zie [SSO van meerdere apps inschakelen in Ios](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-ios)voor IOS.

## <a name="next-steps"></a>Volgende stappen

[Azure AD SSO](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)<br>

[SSO van meerdere apps inschakelen in Android](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-android)<br>

[SSO van meerdere apps inschakelen in iOS](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-ios)<br>

[Apps integreren in AzureAD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)<br>

[Machtigingen en toestemming in het eindpunt van het Microsoft-identiteitsplatform](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)<br>

[AzureAD stack overflow](https://stackoverflow.com/questions/tagged/azure-active-directory)
