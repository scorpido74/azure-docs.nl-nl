---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 04/04/2020
ms.author: mimart
ms.openlocfilehash: e37419b5d568b0f60e4c6a0c8fed26eb418f501e
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/02/2020
ms.locfileid: "84297480"
---
#### <a name="app-registrations-preview"></a>[App-registraties (preview)](#tab/app-reg-preview/)

1. Selecteer **app-registraties (preview)** en selecteer vervolgens de webtoepassing die toegang moet hebben tot de API. Bijvoorbeeld *webapp1*.
1. Selecteer onder **beheren**de optie **API-machtigingen**.
1. Selecteer onder **geconfigureerde machtigingen** **de optie een machtiging toevoegen**.
1. Selecteer het tabblad **mijn api's** .
1. Selecteer de API waaraan de webtoepassing toegang moet worden verleend. Bijvoorbeeld *webapi1*.
1. Vouw onder **machtiging**de optie **demo**uit en selecteer vervolgens de bereiken die u eerder hebt gedefinieerd. Bijvoorbeeld: *demo. Read* en *demo. write*.
1. Selecteer **machtigingen toevoegen**.
1. Selecteer **beheerder toestemming geven voor (uw Tenant naam)**.
1. Als u wordt gevraagd om een account te selecteren, selecteert u het account dat momenteel is aangemeld, of meldt u zich aan met een account in uw Azure AD B2C-Tenant waaraan ten minste de rol van *Cloud toepassings beheerder* is toegewezen.
1. Selecteer **Ja**.
1. Selecteer **vernieuwen**en controleer vervolgens of ' verleend voor... ' wordt onder **status** weer gegeven voor beide bereiken.

#### <a name="applications"></a>[Toepassingen](#tab/applications/)

1. Selecteer **toepassingen**en selecteer vervolgens de webtoepassing die toegang moet hebben tot de API. Bijvoorbeeld *webapp1*.
1. Selecteer **API-toegang** en vervolgens **Toevoegen**.
1. Selecteer in de vervolg keuzelijst **API selecteren** de API waaraan de webtoepassing toegang moet worden verleend. Bijvoorbeeld *webapi1*.
1. Selecteer in de vervolg keuzelijst **bereiken selecteren** de bereiken die u eerder hebt gedefinieerd. Bijvoorbeeld: *demo. Read* en *demo. write*.
1. Selecteer **OK**.
