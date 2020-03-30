---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 02/12/2020
ms.author: mimart
ms.openlocfilehash: bf8b9370abc58902d9c751505f633dee852210a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78184339"
---
#### <a name="applications"></a>[Toepassingen](#tab/applications/)

1. Selecteer Op de **overzichtspagina Geregistreerde app** de optie **Instellingen**.
1. Selecteer onder **API-toegang**de optie **Vereiste machtigingen**.
1. Selecteer **Microsoft Graph**.
1. Schakel **onder Toepassingsmachtigingen**het selectievakje in van de machtiging om uw beheertoepassing toe te kennen. Bijvoorbeeld:
    * **Lees alle controlelogboekgegevens:** Selecteer deze machtiging voor het lezen van de controlelogboeken van de map.
    * **Adreslijstgegevens lezen en schrijven**: Selecteer deze machtiging voor gebruikersmigratie of gebruikersbeheerscenario's.
    * **Lees en schrijf het vertrouwenskaderbeleid van uw organisatie:** Selecteer deze machtiging voor ci/cd-scenario's voor continue integratie/continue levering. Bijvoorbeeld aangepaste beleidsimplementatie met Azure Pipelines.
1. Selecteer **Opslaan**.
1. Selecteer **Machtigingen verlenen**en selecteer **Ope.** Het kan enkele minuten duren voordat de machtigingen volledig worden doorgegeven.

#### <a name="app-registrations-preview"></a>[App-registraties (voorbeeld)](#tab/app-reg-preview/)

1. Selecteer **API-machtigingen** **onder Beheren**.
1. Selecteer **Onder Geconfigureerde machtigingen**de optie Een machtiging **toevoegen**.
1. Selecteer het tabblad **Microsoft-API's** en selecteer **Vervolgens Microsoft Graph**.
1. Selecteer **Toepassingsmachtigingen**.
1. Vouw de juiste machtigingsgroep uit en schakel het selectievakje in van de machtiging om uw beheertoepassing toe te kennen. Bijvoorbeeld:
    * **AuditLog** > **AuditLog.Read.All:** Voor het lezen van de controlelogboeken van de directory.
    * **Directory** > **Directory.ReadWrite.All:** voor gebruikersmigratie of gebruikersbeheerscenario's.
    * **Policy** > **Beleidsbeleid.ReadWrite.TrustFramework**: Voor continue integratie/continue levering (CI/CD) scenario's. Bijvoorbeeld aangepaste beleidsimplementatie met Azure Pipelines.
1. Selecteer **Machtigingen toevoegen**. Zoals aangegeven, wacht een paar minuten voordat u doorgaat naar de volgende stap.
1. Selecteer **Toestemming voor beheerders verlenen voor (uw tenantnaam)**.
1. Selecteer uw momenteel aangemelde beheerdersaccount of meld u aan met een account in uw Azure AD B2C-tenant die ten minste de functie *voor cloudtoepassingsbeheerder* is toegewezen.
1. Selecteer **Accepteren**.
1. Selecteer **Vernieuwen**en controleer vervolgens of 'Verleend voor ..." wordt weergegeven onder **Status**. Het kan enkele minuten duren voordat de machtigingen worden doorgegeven.
