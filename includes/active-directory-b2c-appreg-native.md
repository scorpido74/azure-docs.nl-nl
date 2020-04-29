---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: d3a323a28d92e2a5834b65316d61c7d53a42aacf
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "78183772"
---
Als u een toepassing in uw Azure AD B2C-Tenant wilt registreren, kunt u de huidige **toepassingen** gebruiken of onze nieuwe **Preview-ervaring (Unified app-registraties)** . [Meer informatie over de nieuwe ervaring](https://aka.ms/b2cappregintro).

#### <a name="applications"></a>[Toepassingen](#tab/applications/)

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer het filter **Directory + abonnement** in het bovenste menu en selecteer vervolgens de map die uw Azure AD B2C Tenant bevat.
1. Selecteer in het linkermenu **Azure AD B2C**. U kunt ook **alle services** selecteren en **Azure AD B2C**zoeken en selecteren.
1. Selecteer **toepassingen**en selecteer vervolgens **toevoegen**.
1. Voer een naam in voor de toepassing. Bijvoorbeeld *nativeapp1*.
1. Selecteer voor **native client**de optie **Ja**.
1. Geef een **aangepaste omleidings-URI** met een uniek schema op. Bijvoorbeeld `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`. Er zijn twee belangrijke overwegingen bij het kiezen van een omleidings-URI:
    * **Uniek**: het schema van de omleidings-URI moet uniek zijn voor elke toepassing. In het voorbeeld `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect` is `com.onmicrosoft.contosob2c.exampleapp` het schema. Dit patroon moet worden gevolgd. Als twee toepassingen hetzelfde schema delen, kan de gebruiker een toepassing kiezen. Als de gebruiker onjuist kiest, mislukt de aanmelding.
    * **Voltooid**: de omleidings-URI moet een schema en een pad hebben. Het pad moet ten minste één slash na het domein bevatten. `//oauth/` Werkt bijvoorbeeld als `//oauth` mislukt. Neem geen speciale tekens in de URI op, bijvoorbeeld onderstrepingen.
1. Selecteer **Maken**.

#### <a name="app-registrations-preview"></a>[App-registraties (preview-versie)](#tab/app-reg-preview/)

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer het filter **Directory + abonnement** in het bovenste menu en selecteer vervolgens de map die uw Azure AD B2C Tenant bevat.
1. Selecteer in het linkermenu **Azure AD B2C**. U kunt ook **alle services** selecteren en **Azure AD B2C**zoeken en selecteren.
1. Selecteer **app-registraties (preview)** en selecteer vervolgens **nieuwe registratie**.
1. Voer een **naam** in voor de toepassing. Bijvoorbeeld *nativeapp1*.
1. Onder **ondersteunde account typen**selecteert u **accounts in elke organisatie Directory of een id-provider**.
1. Gebruik onder **omleidings-URI**de vervolg keuzelijst om een **open bare client/systeem eigen (mobiele & bureau blad)** te selecteren.
1. Geef een omleidings-URI met een uniek schema op. Bijvoorbeeld `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`. Er zijn twee belangrijke overwegingen bij het kiezen van een omleidings-URI:
    * **Uniek**: het schema van de omleidings-URI moet uniek zijn voor elke toepassing. In het voorbeeld `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect` is `com.onmicrosoft.contosob2c.exampleapp` het schema. Dit patroon moet worden gevolgd. Als twee toepassingen hetzelfde schema delen, kan de gebruiker een toepassing kiezen. Als de gebruiker onjuist kiest, mislukt de aanmelding.
    * **Voltooid**: de omleidings-URI moet een schema en een pad hebben. Het pad moet ten minste één slash na het domein bevatten. `//oauth/` Werkt bijvoorbeeld als `//oauth` mislukt. Neem geen speciale tekens in de URI op, bijvoorbeeld onderstrepingen.
1. Schakel onder **machtigingen**het selectie vakje *verlenen beheerder toestemming geven aan openid connect-en offline_access machtigingen* in.
1. Selecteer **Registreren**.