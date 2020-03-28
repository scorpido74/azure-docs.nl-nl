---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: d3a323a28d92e2a5834b65316d61c7d53a42aacf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78183772"
---
Als u een toepassing wilt registreren in uw Azure AD B2C-tenant, u de huidige **ervaring met toepassingen** of onze nieuwe uniforme **app-registratie (Preview)-ervaring** gebruiken. [Meer informatie over de nieuwe ervaring](https://aka.ms/b2cappregintro).

#### <a name="applications"></a>[Toepassingen](#tab/applications/)

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Selecteer het **filter Directory + abonnement** in het bovenste menu en selecteer vervolgens de map met uw Azure AD B2C-tenant.
1. Selecteer Azure AD **B2C**in het linkermenu . Selecteer Ook **Alle services** en zoek naar Azure AD **B2C**en selecteer deze .
1. Selecteer **Toepassingen**en selecteer **Toevoegen**.
1. Voer een naam in voor de toepassing. Bijvoorbeeld *nativeapp1*.
1. Selecteer **Ja**voor **native client.**
1. Voer een **aangepaste omleiding uri** met een uniek schema. Bijvoorbeeld `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`. Er zijn twee belangrijke overwegingen bij het kiezen van een omleidings-URI:
    * **Uniek**: Het schema van de omleiding URI moet uniek zijn voor elke toepassing. In het voorbeeld `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect` is `com.onmicrosoft.contosob2c.exampleapp` het schema. Dit patroon moet worden gevolgd. Als twee toepassingen hetzelfde schema delen, kan de gebruiker een toepassing kiezen. Als de gebruiker ten onrechte kiest, mislukt de aanmelding.
    * **Voltooid:** De omleiding uri moet een zowel een schema als een pad hebben. Het pad moet ten minste één slash na het domein bevatten. Werkt bijvoorbeeld `//oauth/` terwijl `//oauth` het mislukt. Neem bijvoorbeeld geen speciale tekens op in de URI.
1. Selecteer **Maken**.

#### <a name="app-registrations-preview"></a>[App-registraties (voorbeeld)](#tab/app-reg-preview/)

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Selecteer het **filter Directory + abonnement** in het bovenste menu en selecteer vervolgens de map met uw Azure AD B2C-tenant.
1. Selecteer Azure AD **B2C**in het linkermenu . Selecteer Ook **Alle services** en zoek naar Azure AD **B2C**en selecteer deze .
1. Selecteer **App-registraties (Voorbeeld)** en selecteer **Vervolgens Nieuwe registratie**.
1. Voer een **naam** voor de toepassing in. Bijvoorbeeld *nativeapp1*.
1. Selecteer **accounts in een organisatiemap of een identiteitsprovider**onder Ondersteunde **accounttypen**.
1. Gebruik onder **Uri omleiden**de vervolgkeuzelijst om **Openbare client/native (mobile & desktop)** te selecteren.
1. Voer een omleiding URI met een uniek schema. Bijvoorbeeld `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`. Er zijn twee belangrijke overwegingen bij het kiezen van een omleidings-URI:
    * **Uniek**: Het schema van de omleiding URI moet uniek zijn voor elke toepassing. In het voorbeeld `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect` is `com.onmicrosoft.contosob2c.exampleapp` het schema. Dit patroon moet worden gevolgd. Als twee toepassingen hetzelfde schema delen, kan de gebruiker een toepassing kiezen. Als de gebruiker ten onrechte kiest, mislukt de aanmelding.
    * **Voltooid:** De omleiding uri moet een zowel een schema als een pad hebben. Het pad moet ten minste één slash na het domein bevatten. Werkt bijvoorbeeld `//oauth/` terwijl `//oauth` het mislukt. Neem bijvoorbeeld geen speciale tekens op in de URI.
1. Schakel **onder Machtigingen**het selectievakje *Beheerderstoestemming verlenen voor openid en offline_access machtigingen* in.
1. Selecteer **Registreren**.