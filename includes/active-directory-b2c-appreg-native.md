---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: d3a323a28d92e2a5834b65316d61c7d53a42aacf
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "78183772"
---
Als u een toepassing wilt registreren in uw Azure AD B2C-tenant, kunt u de huidige ervaring voor **Toepassingen** gebruiken, of onze nieuwe uniforme **App-registraties (preview)** . [Meer informatie over de nieuwe ervaring](https://aka.ms/b2cappregintro).

#### <a name="applications"></a>[Toepassingen](#tab/applications/)

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer het filter **Map + Abonnement** in het bovenste menu en selecteer vervolgens de map die uw Azure AD B2C-tenant bevat.
1. Selecteer **Azure AD B2C** in het linkermenu. Of selecteer **Alle services** en zoek naar en selecteer **Azure AD B2C**.
1. Selecteer **Toepassingen** en vervolgens **Toevoegen**.
1. Voer een naam in voor de toepassing. Bijvoorbeeld *nativeapp1*.
1. Voor **Systeemeigen client** selecteert u **Ja**.
1. Voer een **Aangepaste omleidings-URI** met een uniek schema in. Bijvoorbeeld `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`. Er zijn twee belangrijke overwegingen bij het kiezen van een omleidings-URI:
    * **Uniek**: Het schema van de omleidings-URI moet voor elke toepassing uniek zijn. In het voorbeeld `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect` is `com.onmicrosoft.contosob2c.exampleapp` het schema. Dit patroon moet worden gevolgd. Als twee toepassingen hetzelfde schema delen, kan de gebruiker een toepassing kiezen. Als de gebruiker onjuist kiest, mislukt de aanmelding.
    * **Volledig**: De omleidings-URI moet een schema en een pad hebben. Het pad moet ten minste één slash na het domein bevatten. Bijvoorbeeld: `//oauth/` werkt en `//oauth` mislukt. Neem geen speciale tekens op in de URI, bijvoorbeeld lage streepjes.
1. Selecteer **Maken**.

#### <a name="app-registrations-preview"></a>[App-registraties (preview)](#tab/app-reg-preview/)

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer het filter **Map + Abonnement** in het bovenste menu en selecteer vervolgens de map die uw Azure AD B2C-tenant bevat.
1. Selecteer **Azure AD B2C** in het linkermenu. Of selecteer **Alle services** en zoek naar en selecteer **Azure AD B2C**.
1. Selecteer **App-registraties (preview)** en selecteer daarna **Nieuwe registratie**.
1. Voer een **naam** in voor de toepassing. Bijvoorbeeld *nativeapp1*.
1. Selecteer onder **Ondersteunde accounttypen** de optie **Accounts in een organisatieadreslijst of een identiteitsprovider**.
1. Gebruik **Omleidings-URI**, gebruik het vervolgkeuzemenu om **Openbare client/systeemeigen (mobiel en desktop)** te selecteren.
1. Voer een omleidings-URI in met een uniek schema. Bijvoorbeeld `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`. Er zijn twee belangrijke overwegingen bij het kiezen van een omleidings-URI:
    * **Uniek**: Het schema van de omleidings-URI moet voor elke toepassing uniek zijn. In het voorbeeld `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect` is `com.onmicrosoft.contosob2c.exampleapp` het schema. Dit patroon moet worden gevolgd. Als twee toepassingen hetzelfde schema delen, kan de gebruiker een toepassing kiezen. Als de gebruiker onjuist kiest, mislukt de aanmelding.
    * **Volledig**: De omleidings-URI moet een schema en een pad hebben. Het pad moet ten minste één slash na het domein bevatten. Bijvoorbeeld: `//oauth/` werkt en `//oauth` mislukt. Neem geen speciale tekens op in de URI, bijvoorbeeld lage streepjes.
1. Selecteer in **Machtigingen** het selectievakje *Beheerdersgoedkeuring verlenen aan machtigingen van OpenID en offline_access*.
1. Selecteer **Registreren**.