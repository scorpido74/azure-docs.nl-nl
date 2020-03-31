---
title: Interactief verzoekpromptgedrag (MSAL.js) | Azure
titleSuffix: Microsoft identity platform
description: Informatie over het aanpassen van het promptgedrag in interactieve gesprekken met de Microsoft-verificatiebibliotheek voor JavaScript (MSAL.js).
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
ms.openlocfilehash: 778e89655019a49a30904fbe8d8e6aedf1833e9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76695973"
---
# <a name="prompt-behavior-in-msaljs-interactive-requests"></a>Snel gedrag in interactieve verzoeken van MSAL.js

Wanneer een gebruiker een actieve Azure AD-sessie met meerdere gebruikersaccounts heeft ingesteld, wordt de aanmeldingspagina van Azure AD standaard gevraagd de gebruiker een account te selecteren voordat hij verder gaat om zich aan te melden. Gebruikers zien geen ervaring met accountselectie als er slechts één geverifieerde sessie met Azure AD is.

De MSAL.js-bibliotheek (vanaf v0.2.4) verzendt geen promptparameter`loginRedirect`tijdens `loginPopup` `acquireTokenRedirect` de `acquireTokenPopup`interactieve aanvragen ( , en ) en dwingt daardoor geen snel gedrag af. Voor stille tokenaanvragen `acquireTokenSilent` met de methode geeft MSAL.js een promptparameter door die is ingesteld op `none`.

Op basis van uw toepassingsscenario u het promptgedrag voor de interactieve aanvragen beheren door de promptparameter in te stellen in de aanvraagparameters die naar de methoden worden doorgegeven. Als u bijvoorbeeld de ervaring voor accountselectie wilt aanroepen:

```javascript
var request = {
    scopes: ["user.read"],
    prompt: 'select_account',
}

userAgentApplication.loginRedirect(request);
```


De volgende promptwaarden kunnen worden doorgegeven wanneer u deze met Azure AD hebt geauthenticeren:

**inloggen:** Deze waarde dwingt de gebruiker om referenties in te voeren op de verificatieaanvraag.

**select_account:** Deze waarde biedt de gebruiker een accountselectie-ervaring met alle accounts in sessie.

**toestemming:** Deze waarde zal een beroep doen op de OAuth toestemming dialoog waarmee gebruikers machtigingen te verlenen aan de app.

**none** Deze waarde zorgt ervoor dat de gebruiker geen interactieve prompt ziet. Het wordt aanbevolen om deze waarde niet door te geven aan interactieve methoden in MSAL.js, omdat het onverwacht gedrag kan vertonen. Gebruik in `acquireTokenSilent` plaats daarvan de methode om stille oproepen te bereiken.

## <a name="next-steps"></a>Volgende stappen

Lees meer `prompt` over de parameter in het [OAuth 2.0 impliciete subsidieprotocol](v2-oauth2-implicit-grant-flow.md) dat MSAL.js library gebruikt.
