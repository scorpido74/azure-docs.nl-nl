---
title: Vraag gedrag interactieve aanvraag (MSAL. js) | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over het aanpassen van prompt gedrag in interactieve aanroepen met behulp van de micro soft-verificatie bibliotheek voor Java script (MSAL. js).
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/24/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6654b523cef4623a55bf9f857fd5a723fae2dc47
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74921955"
---
# <a name="prompt-behavior-in-msaljs-interactive-requests"></a>Vraag gedrag in interactieve aanvragen van MSAL. js

Wanneer een gebruiker een actieve Azure AD-sessie met meerdere gebruikers accounts heeft gemaakt, wordt de gebruiker door de aanmeldings pagina van Azure AD standaard gevraagd een account te selecteren voordat u zich aanmeldt. Gebruikers zien geen account selectie-ervaring als er slechts één geverifieerde sessie is met Azure AD.

De MSAL. JS-bibliotheek (vanaf v 0.2.4) verzendt geen prompt parameter tijdens de interactieve aanvragen (`loginRedirect`, `loginPopup`, `acquireTokenRedirect` en `acquireTokenPopup`), en dwingt daardoor geen vraag gedrag af. Voor Silent token aanvragen met behulp van de `acquireTokenSilent` methode stuurt MSAL. js een prompt parameter ingesteld op `none`.

Op basis van uw toepassings scenario kunt u het prompt gedrag voor de interactieve aanvragen bepalen door de para meter prompt in te stellen in de aanvraag parameters die aan de methoden zijn door gegeven. Als u bijvoorbeeld de account selectie wilt aanroepen:

```javascript
var request = {
    scopes: ["user.read"],
    prompt: 'select_account',
}

userAgentApplication.loginRedirect(request);
```


De volgende prompt waarden kunnen worden door gegeven bij de verificatie met Azure AD:

**aanmelding:** Met deze waarde wordt de gebruiker gedwongen om referenties in te voeren voor de verificatie aanvraag.

**select_account:** Met deze waarde krijgt de gebruiker een account selectie-ervaring met een lijst met alle accounts in de sessie.

**toestemming:** Met deze waarde wordt het dialoog venster OAuth-toestemming aangeroepen waarmee gebruikers machtigingen voor de app kunnen verlenen.

**geen:** Met deze waarde wordt ervoor gezorgd dat de gebruiker geen interactieve prompt ziet. U kunt deze waarde het beste niet door geven aan interactieve methoden in MSAL. js, omdat deze mogelijk onverwachte gedragingen heeft. Gebruik in plaats daarvan de `acquireTokenSilent` methode voor het bezorgen van Silent-aanroepen.

## <a name="next-steps"></a>Volgende stappen

Lees meer over de para meter `prompt` in het [OAuth 2,0 impliciet Grant](v2-oauth2-implicit-grant-flow.md) -protocol dat wordt gebruikt door de MSAL. JS-bibliotheek.
