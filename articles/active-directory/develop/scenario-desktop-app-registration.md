---
title: Bureau blad-apps registreren die web-Api's aanroepen-micro soft Identity-platform | Azure
description: Meer informatie over het bouwen van een bureau blad-app die web-Api's aanroept (app-registratie)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/09/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: dabc96ef669f0c0c61a7bca4a16828294cf404df
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75423844"
---
# <a name="desktop-app-that-calls-web-apis---app-registration"></a>Bureau blad-app die web-Api's aanroept-app-registratie

In dit artikel worden de app-registratie gegevens voor een bureaublad toepassing besproken.

## <a name="supported-accounts-types"></a>Ondersteunde account typen

De account typen die worden ondersteund in bureaublad toepassing, zijn afhankelijk van de ervaring die u wilt oplichten. Vanwege deze relatie zijn de ondersteunde account typen afhankelijk van de stromen die u wilt gebruiken.

### <a name="audience-for-interactive-token-acquisition"></a>Doel groep voor het verkrijgen van interactief tokens

Als uw bureaublad toepassing interactieve verificatie gebruikt, kunt u gebruikers van elk [account type](quickstart-register-app.md#register-a-new-application-using-the-azure-portal)aanmelden.

### <a name="audience-for-desktop-app-silent-flows"></a>Doel groep voor desktop-app Silent flows

- Als u geïntegreerde Windows-verificatie of gebruikers naam/wacht woord wilt gebruiken, moet uw toepassing zich aanmelden bij uw eigen Tenant (LOB-ontwikkelaar) of in azure Active Directory-organisaties (ISV-scenario). Deze verificatie stromen worden niet ondersteund voor persoonlijke micro soft-accounts.
- Als u de code stroom van het apparaat wilt gebruiken, kunt u zich nog niet aanmelden bij gebruikers met hun persoonlijke micro soft-accounts.
- Als u gebruikers aanmeldt met sociale identiteiten die een B2C-instantie en-beleid door geven, kunt u alleen de interactieve verificatie en gebruikers naam-wacht woord gebruiken.

## <a name="redirect-uris"></a>Omleidings-URI's

De omleidings-Uri's voor gebruik in een bureaublad toepassing zijn afhankelijk van de stroom die u wilt gebruiken.

- Als u de **interactieve verificatie** of de **code stroom**van het apparaat gebruikt, wilt u `https://login.microsoftonline.com/common/oauth2/nativeclient`gebruiken. U verkrijgt deze configuratie door te klikken op de bijbehorende URL in het gedeelte **verificatie** voor uw toepassing.
  
  > [!IMPORTANT]
  > Vandaag MSAL.NET maakt standaard gebruik van een andere omleidings-URI in bureaublad toepassingen die worden uitgevoerd op Windows (`urn:ietf:wg:oauth:2.0:oob`). In de toekomst wilt u deze standaard instelling wijzigen. u kunt daarom het beste `https://login.microsoftonline.com/common/oauth2/nativeclient` gebruiken

- Als u een systeem eigen doel-C of SWIFT-app bouwt voor macOS, wilt u de redirectUri registreren op basis van de bundel-id van uw toepassing in de volgende indeling: **msauth. < your. app. bundel. id >://auth** (Vervang < your. app. bundel. id > met de bundel-id van uw toepassing)
- Als uw app alleen gebruikmaakt van geïntegreerde Windows-verificatie of gebruikers naam/wacht woord, hoeft u geen omleidings-URI voor uw toepassing te registreren. Deze stromen maken een afronding van het micro soft Identity platform v 2.0-eind punt en uw toepassing wordt niet terugaangeroepen op een specifieke URI.
- Als u de apparaatcode stroom, geïntegreerde Windows-verificatie en gebruikers naam/wacht woord van een vertrouwelijke client toepassings stroom die geen omleidings-Uri's heeft, wilt onderscheiden (de client referentie stroom die wordt gebruikt in daemon-toepassingen), moet u uitdrukkelijk controleren of uw toepassing een open bare client toepassing is. Voor deze configuratie gaat u naar het gedeelte **verificatie** voor uw toepassing. Klik in de Subsectie **Geavanceerde instellingen** in het standaard- **client type** op **Ja** als **een open bare client**om de vraag te behandelen.

  ![Open bare client toestaan](media/scenarios/default-client-type.png)

## <a name="api-permissions"></a>API-machtigingen

Bureau blad-Apps bellen Api's voor de aangemelde gebruiker. Ze moeten gedelegeerde machtigingen aanvragen. Ze kunnen echter geen toepassings machtigingen aanvragen, die alleen worden verwerkt in [daemon-toepassingen](scenario-daemon-overview.md).

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Bureau blad-app-app-configuratie](scenario-desktop-app-configuration.md)
