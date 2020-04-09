---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 04/04/2020
ms.author: mimart
ms.openlocfilehash: ee63c1c0913baf3700d77e6fba1582fe7027ead7
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80875665"
---
#### <a name="applications"></a>[Toepassingen](#tab/applications/)

1. Selecteer **Toepassingen**en selecteer vervolgens de webtoepassing die toegang moet hebben tot de API. *Webapp1*bijvoorbeeld .
1. Selecteer **API-toegang** en vervolgens **Toevoegen**.
1. Selecteer **in** de vervolgkeuzelijst API selecteren de API tot welke webtoepassing toegang moet worden verleend. Bijvoorbeeld *webapi1*.
1. Selecteer **in** de vervolgkeuzelijst Bereik selecteren de scopes die u eerder hebt gedefinieerd. Bijvoorbeeld, *demo.read* en *demo.write*.
1. Selecteer **OK**.

#### <a name="app-registrations-preview"></a>[App-registraties (voorbeeld)](#tab/app-reg-preview/)

1. Selecteer **App-registraties (Voorbeeld)** en selecteer vervolgens de webtoepassing die toegang moet hebben tot de API. *Webapp1*bijvoorbeeld .
1. Selecteer **API-machtigingen** **onder Beheren**.
1. Selecteer **Onder Geconfigureerde machtigingen**de optie Een machtiging **toevoegen**.
1. Selecteer het tabblad **Mijn API's.**
1. Selecteer de API waarvoor de webtoepassing toegang moet krijgen. Bijvoorbeeld *webapi1*.
1. Vouw **onder Machtiging**demo uit en selecteer vervolgens de scopes die u eerder hebt gedefinieerd. **demo** Bijvoorbeeld, *demo.read* en *demo.write*.
1. Selecteer **Machtigingen toevoegen**.
1. Selecteer **Toestemming voor beheerders verlenen voor (uw tenantnaam)**.
1. Als u wordt gevraagd een account te selecteren, selecteert u uw momenteel aangemelde beheerdersaccount of meldt u zich aan met een account in uw Azure AD B2C-tenant waaraan ten minste de functie *voor cloudtoepassingsbeheerder* is toegewezen.
1. Selecteer **Ja**.
1. Selecteer **Vernieuwen**en controleer vervolgens of 'Verleend voor ..." wordt weergegeven onder **Status** voor beide scopes.
