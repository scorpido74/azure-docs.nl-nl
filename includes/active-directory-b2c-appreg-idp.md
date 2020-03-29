---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: 80e5166775b0cf5acbfce32e61d91c0889e3b086
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78186353"
---
Als u een toepassing wilt registreren in uw Azure AD B2C-tenant, u de huidige **ervaring met toepassingen** of onze nieuwe uniforme **app-registratie (Preview)-ervaring** gebruiken. [Meer informatie over de nieuwe ervaring](https://aka.ms/b2cappregintro).

#### <a name="applications"></a>[Toepassingen](#tab/applications/)

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Selecteer het **filter Directory + abonnement** in het bovenste menu en selecteer vervolgens de map met uw Azure AD B2C-tenant.
1. Selecteer Azure AD **B2C**in het linkermenu . Selecteer Ook **Alle services** en zoek naar Azure AD **B2C**en selecteer deze .
1. Selecteer **Toepassingen**en selecteer **Toevoegen**.
1. Voer een naam in voor de toepassing. *Testapp1*bijvoorbeeld .
1. Selecteer **Ja**voor **Web App / Web API.**
1. Voer **voor de URL van antwoord**`https://jwt.ms`
1. Selecteer **Maken**.

#### <a name="app-registrations-preview"></a>[App-registraties (voorbeeld)](#tab/app-reg-preview/)

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Selecteer het **filter Directory + abonnement** in het bovenste menu en selecteer vervolgens de map met uw Azure AD B2C-tenant.
1. Selecteer Azure AD **B2C**in het linkermenu . Selecteer Ook **Alle services** en zoek naar Azure AD **B2C**en selecteer deze .
1. Selecteer **App-registraties (Voorbeeld)** en selecteer **Vervolgens Nieuwe registratie**.
1. Voer een **naam** voor de toepassing in. *Testapp1*bijvoorbeeld .
1. Selecteer **Accounts in een organisatiemap of een identiteitsprovider.**
1. Selecteer **onder URI omleiden** **en**voer het `https://jwt.ms` tekstvak URL in.
1. Schakel **onder Machtigingen**het selectievakje *Beheerderstoestemming verlenen voor openid en offline_access machtigingen* in.
1. Selecteer **Registreren**.

Zodra de registratie van de aanvraag is voltooid, schakelt u de impliciete subsidiestroom in:

1. Selecteer onder **Beheren**de optie **Verificatie**.
1. Selecteer **Probeer de nieuwe ervaring** uit (indien weergegeven).
1. Schakel **onder Impliciete subsidie**de selectievakjes **Access-tokens** en **ID-tokens** in.
1. Selecteer **Opslaan**.