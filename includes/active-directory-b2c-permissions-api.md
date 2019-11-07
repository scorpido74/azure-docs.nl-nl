---
author: mmacy
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: marsma
ms.openlocfilehash: ce49e9e26160017d64d745c9c7ad5402bc3ae2ca
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73641567"
---
#### <a name="applicationstabapplications"></a>[Toepassingen](#tab/applications/)

1. Selecteer **toepassingen**en selecteer vervolgens de webtoepassing die toegang moet hebben tot de API. Bijvoorbeeld *webapp1*.
1. Selecteer **API-toegang** en vervolgens **Toevoegen**.
1. Selecteer in de vervolg keuzelijst **API selecteren** de API waaraan de webtoepassing toegang moet worden verleend. Bijvoorbeeld *webapi1*.
1. Selecteer in de vervolg keuzelijst **bereiken selecteren** de bereiken die u eerder hebt gedefinieerd. Bijvoorbeeld: *demo. Read* en *demo. write*.
1. Selecteer **OK**.

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[App-registraties (preview-versie)](#tab/app-reg-preview/)

1. Selecteer **app-registraties (preview)** en selecteer vervolgens de webtoepassing die toegang moet hebben tot de API. Bijvoorbeeld *webapp1*.
1. Selecteer onder **beheren**de optie **API-machtigingen**.
1. Selecteer onder **geconfigureerde machtigingen** **de optie een machtiging toevoegen**.
1. Selecteer het tabblad **mijn api's** .
1. Selecteer de API waaraan de webtoepassing toegang moet worden verleend. Bijvoorbeeld *webapi1*.
1. Vouw onder **machtiging**de optie **demo**uit en selecteer vervolgens de bereiken die u eerder hebt gedefinieerd. Bijvoorbeeld: *demo. Read* en *demo. write*.
1. Selecteer **machtigingen toevoegen**. Wacht een paar minuten voordat u verdergaat met de volgende stap.
1. Selecteer **beheerder toestemming geven voor (uw Tenant naam)** .
1. Selecteer het momenteel aangemelde Administrator-account of Meld u aan met een account in uw Azure AD B2C-Tenant waaraan ten minste de rol van *Cloud toepassings beheerder* is toegewezen.
1. Selecteer **Accepteren**.
1. Selecteer **vernieuwen**en controleer vervolgens of ' verleend voor... ' wordt onder **status** weer gegeven voor beide bereiken. Het kan enkele minuten duren voordat de machtigingen zijn door gegeven.