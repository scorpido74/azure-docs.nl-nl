---
title: bestand opnemen
description: bestand opnemen
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/10/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 2325509f68ced7c66d9f733b07247ae01301b565
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82181529"
---
## <a name="test-your-code"></a>Uw code testen

Als u uw project wilt uitvoeren, selecteert u op **F5**in Visual Studio. De **mainwindow** van uw toepassing wordt weer gegeven, zoals hier wordt weer gegeven:

![Uw toepassing testen](./media/active-directory-develop-guidedsetup-windesktop-test/samplescreenshot.png)

De eerste keer dat u de toepassing uitvoert en de API-knop **oproep Microsoft Graph** selecteert, wordt u gevraagd u aan te melden. Gebruik een Azure Active Directory account (werk-of school account) of een Microsoft-account (live.com, outlook.com) om het te testen.

![Aanmelden bij Azure Portal](./media/active-directory-develop-guidedsetup-windesktop-test/signinscreenshot.png)

### <a name="provide-consent-for-application-access"></a>Toestemming geven voor toegang tot toepassingen

De eerste keer dat u zich bij uw toepassing aanmeldt, wordt u ook gevraagd toestemming te geven om de toepassing toegang te verlenen tot uw profiel en u aan te melden, zoals hier wordt weer gegeven:

![Geef uw toestemming voor toegang tot toepassingen](./media/active-directory-develop-guidedsetup-windesktop-test/consentscreen.png)

### <a name="view-application-results"></a>Toepassings resultaten weer geven

Nadat u zich hebt aangemeld, ziet u de gebruikers profiel gegevens die worden geretourneerd door de aanroep van de Microsoft Graph-API. De resultaten worden weer gegeven in het vak **API-aanroep resultaten** . Basis informatie over het token dat is verkregen via de aanroep van `AcquireTokenInteractive` of `AcquireTokenSilent` moet zichtbaar zijn in het vak **token info** . De resultaten bevatten de volgende eigenschappen:

|Eigenschap  |Indeling  |Beschrijving |
|---------|---------|---------|
|**Gebruikers** |<span>user@domain.com</span> |De gebruikers naam die wordt gebruikt om de gebruiker te identificeren.|
|**Token verloopt** |DateTime |Het tijdstip waarop het token verloopt. MSAL breidt de verloop datum uit door de token indien nodig te vernieuwen.|

### <a name="more-information-about-scopes-and-delegated-permissions"></a>Meer informatie over bereiken en gedelegeerde machtigingen

Voor de Microsoft Graph-API is het bereik *User. Read* vereist om het profiel van een gebruiker te lezen. Deze scope wordt standaard automatisch toegevoegd in elke toepassing die is geregistreerd in de portal voor toepassings registratie. Andere Api's voor Microsoft Graph, evenals aangepaste Api's voor uw back-endserver, vereisen mogelijk extra scopes. De Microsoft Graph-API vereist het bereik *Calendars. Read* om de agenda's van de gebruiker weer te geven.

Als u toegang wilt krijgen tot de agenda's van de gebruiker in de context van een toepassing, voegt u de *agenda's toe.* gedelegeerde machtigingen lezen voor de registratie gegevens van de toepassing. Voeg vervolgens de *agenda's* toe aan de `acquireTokenSilent` aanroep.

>[!NOTE]
>De gebruiker wordt mogelijk gevraagd om aanvullende toestemmingen wanneer u het aantal bereiken verhoogt.

[!INCLUDE [Help and support](./active-directory-develop-help-support-include.md)]
