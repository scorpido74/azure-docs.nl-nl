---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: 03329252c0ed4231585d1717d9361a2aef35b36f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78186987"
---
Als u een toepassing wilt registreren in uw Azure AD B2C-tenant, u de huidige **ervaring met toepassingen** of onze nieuwe uniforme **app-registratie (Preview)-ervaring** gebruiken. [Meer informatie over de nieuwe ervaring](https://aka.ms/b2cappregintro).

#### <a name="applications"></a>[Toepassingen](#tab/applications/)

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Selecteer het **filter Directory + abonnement** in het bovenste menu en selecteer vervolgens de map met uw Azure AD B2C-tenant.
1. Selecteer Azure AD **B2C**in het linkermenu . Selecteer Ook **Alle services** en zoek naar Azure AD **B2C**en selecteer deze .
1. Selecteer **Toepassingen**en selecteer **Toevoegen**.
1. Voer een naam in voor de toepassing. Bijvoorbeeld *ROPC_Auth_app*.
1. Selecteer **Ja**voor **native client.**
1. Laat de andere waarden zoals ze zijn en selecteer **Vervolgens Maken**.
1. De **toepassings-id opnemen** voor gebruik in een latere stap.

#### <a name="app-registrations-preview"></a>[App-registraties (voorbeeld)](#tab/app-reg-preview/)

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Selecteer het **filter Directory + abonnement** in het bovenste menu en selecteer vervolgens de map met uw Azure AD B2C-tenant.
1. Selecteer Azure AD **B2C**in het linkermenu . Selecteer Ook **Alle services** en zoek naar Azure AD **B2C**en selecteer deze .
1. Selecteer **App-registraties (Voorbeeld)** en selecteer **Vervolgens Nieuwe registratie**.
1. Voer een **naam** voor de toepassing in. Bijvoorbeeld *ROPC_Auth_app*.
1. Laat de andere waarden zoals ze zijn en selecteer **Vervolgens Registreren**.
1. Neem de **toepassings-id (client) op** voor gebruik in een latere stap.
1. Selecteer onder **Beheren**de optie **Verificatie**.
1. Selecteer **Probeer de nieuwe ervaring** uit (indien weergegeven).
1. Selecteer **Onder Standaardclienttype**de optie **Ja** om de toepassing als openbare client te behandelen. Deze instelling is vereist voor de ROPC-stroom.
1. Selecteer **Opslaan**.