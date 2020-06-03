---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: 2981a46502ad7a7ab673e00bd1e8d2033ef2213d
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/02/2020
ms.locfileid: "84317641"
---
Als u een toepassing in uw Azure AD B2C-Tenant wilt registreren, kunt u gebruikmaken van onze nieuwe geïntegreerde **app-registraties** ervaring of onze oudere **toepassingen (** verouderd). [Meer informatie over de nieuwe ervaring](https://aka.ms/b2cappregtraining).

#### <a name="app-registrations"></a>[App-registraties](#tab/app-reg-ga/)

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer het filter **Map + Abonnement** in het bovenste menu en selecteer vervolgens de map die uw Azure AD B2C-tenant bevat.
1. Selecteer **Azure AD B2C** in het linkermenu. Of selecteer **Alle services** en zoek naar en selecteer **Azure AD B2C**.
1. Selecteer **app-registraties**en selecteer vervolgens **nieuwe registratie**.
1. Voer een **naam** in voor de toepassing. Bijvoorbeeld *nativeapp1*.
1. Selecteer onder **Ondersteunde accounttypen** de optie **Accounts in een organisatieadreslijst of een identiteitsprovider**.
1. Gebruik **Omleidings-URI**, gebruik het vervolgkeuzemenu om **Openbare client/systeemeigen (mobiel en desktop)** te selecteren.
1. Voer een omleidings-URI in met een uniek schema. Bijvoorbeeld `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`. Er zijn twee belangrijke overwegingen bij het kiezen van een omleidings-URI:
    * **Uniek**: Het schema van de omleidings-URI moet voor elke toepassing uniek zijn. In het voorbeeld `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect` is `com.onmicrosoft.contosob2c.exampleapp` het schema. Dit patroon moet worden gevolgd. Als twee toepassingen hetzelfde schema delen, kan de gebruiker een toepassing kiezen. Als de gebruiker onjuist kiest, mislukt de aanmelding.
    * **Volledig**: De omleidings-URI moet een schema en een pad hebben. Het pad moet ten minste één slash na het domein bevatten. Bijvoorbeeld: `//oauth/` werkt en `//oauth` mislukt. Neem geen speciale tekens op in de URI, bijvoorbeeld lage streepjes.
1. Selecteer in **Machtigingen** het selectievakje *Beheerdersgoedkeuring verlenen aan machtigingen van OpenID en offline_access*.
1. Selecteer **Registreren**.

#### <a name="applications-legacy"></a>[Toepassingen (verouderd)](#tab/applications-legacy/)

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer het filter **Map + Abonnement** in het bovenste menu en selecteer vervolgens de map die uw Azure AD B2C-tenant bevat.
1. Selecteer **Azure AD B2C** in het linkermenu. Of selecteer **Alle services** en zoek naar en selecteer **Azure AD B2C**.
1. Selecteer **toepassingen (verouderd)** en selecteer vervolgens **toevoegen**.
1. Voer een naam in voor de toepassing. Bijvoorbeeld *nativeapp1*.
1. Voor **Systeemeigen client** selecteert u **Ja**.
1. Voer een **Aangepaste omleidings-URI** met een uniek schema in. Bijvoorbeeld `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`. Er zijn twee belangrijke overwegingen bij het kiezen van een omleidings-URI:
    * **Uniek**: Het schema van de omleidings-URI moet voor elke toepassing uniek zijn. In het voorbeeld `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect` is `com.onmicrosoft.contosob2c.exampleapp` het schema. Dit patroon moet worden gevolgd. Als twee toepassingen hetzelfde schema delen, kan de gebruiker een toepassing kiezen. Als de gebruiker onjuist kiest, mislukt de aanmelding.
    * **Volledig**: De omleidings-URI moet een schema en een pad hebben. Het pad moet ten minste één slash na het domein bevatten. Bijvoorbeeld: `//oauth/` werkt en `//oauth` mislukt. Neem geen speciale tekens op in de URI, bijvoorbeeld lage streepjes.
1. Selecteer **Maken**.