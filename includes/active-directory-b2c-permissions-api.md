---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: 71a6654acd436c27bd2370646dede81778113860
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78186135"
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
1. Selecteer **Machtigingen toevoegen**. Zoals aangegeven, wacht een paar minuten voordat u doorgaat naar de volgende stap.
1. Selecteer **Toestemming voor beheerders verlenen voor (uw tenantnaam)**.
1. Selecteer uw momenteel aangemelde beheerdersaccount of meld u aan met een account in uw Azure AD B2C-tenant die ten minste de functie *voor cloudtoepassingsbeheerder* is toegewezen.
1. Selecteer **Accepteren**.
1. Selecteer **Vernieuwen**en controleer vervolgens of 'Verleend voor ..." wordt weergegeven onder **Status** voor beide scopes. Het kan enkele minuten duren voordat de machtigingen worden doorgegeven.