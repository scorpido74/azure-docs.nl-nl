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
ms.openlocfilehash: f121be4ec8c3d3ab618e2955d9dbd8ab5eea461d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "79128854"
---
## <a name="test-your-code"></a>Uw code testen

Als u uw project wilt uitvoeren, selecteert u in Visual Studio **F5**. Uw **toepassingsmainwindow** wordt weergegeven, zoals hier wordt weergegeven:

![Uw toepassing testen](./media/active-directory-develop-guidedsetup-windesktop-test/samplescreenshot.png)

De eerste keer dat u de toepassing uitvoert en de **api-knop Microsoft Graph aanroepen** selecteert, wordt u gevraagd zich aan te melden. Gebruik een Azure Active Directory-account (werk- of schoolaccount) of een Microsoft-account (live.com, outlook.com) om het te testen.

![Aanmelden bij Azure Portal](./media/active-directory-develop-guidedsetup-windesktop-test/signinscreenshot.png)

### <a name="provide-consent-for-application-access"></a>Toestemming geven voor toegang tot toepassingen

De eerste keer dat u zich aanmeldt bij uw aanvraag, wordt u ook gevraagd om toestemming te geven om de toepassing toegang te geven tot uw profiel en u aan te melden, zoals hier wordt weergegeven:

![Geef uw toestemming voor toegang tot toepassingen](./media/active-directory-develop-guidedsetup-windesktop-test/consentscreen.png)

### <a name="view-application-results"></a>Toepassingsresultaten weergeven

Nadat u zich hebt aangemeld, ziet u de gebruikersprofielgegevens die door de aanroep naar de Microsoft Graph-API worden geretourneerd. De resultaten worden weergegeven in het vak **API-oproepresultaten.** Basisinformatie over het token dat is `AcquireTokenInteractive` aangeschaft `AcquireTokenSilent` via de oproep naar of moet zichtbaar zijn in het vak **Token info.** De resultaten bevatten de volgende eigenschappen:

|Eigenschap  |Indeling  |Beschrijving |
|---------|---------|---------|
|**Gebruikersnaam** |<span>user@domain.com</span> |De gebruikersnaam die wordt gebruikt om de gebruiker te identificeren.|
|**Token verloopt** |DateTime |Het tijdstip waarop het token verloopt. MSAL verlengt de vervaldatum door het token zo nodig te vernieuwen.|


<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>Meer informatie over scopes en gedelegeerde machtigingen

De Microsoft Graph API vereist dat de *user.read-scope* het profiel van een gebruiker leest. Dit bereik wordt standaard automatisch toegevoegd in elke toepassing die is geregistreerd in het Portal voor toepassingsregistratie. Andere API's voor Microsoft Graph en aangepaste API's voor uw back-endserver vereisen mogelijk extra scopes. De Microsoft Graph API vereist de *agenda's.Lees* bereik om de agenda's van de gebruiker weer te geven.

Als u de agenda's van de gebruiker wilt openen in de context van een toepassing, voegt u de gedelegeerde machtiging *Agenda's* toe aan de registratiegegevens van de toepassing. Voeg vervolgens het *bereik Agenda's toe.Lees* het bereik aan het `acquireTokenSilent` gesprek.

>[!NOTE]
>De gebruiker kan worden gevraagd om extra toestemmingen als u het aantal scopes te verhogen.

<!--end-collapse-->

[!INCLUDE [Help and support](./active-directory-develop-help-support-include.md)]
