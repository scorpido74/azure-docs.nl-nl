---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: f941f67105227b3973386b015344d5fa787c3485
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/02/2020
ms.locfileid: "84317791"
---
Als u een toepassing in uw Azure AD B2C-Tenant wilt registreren, kunt u gebruikmaken van onze nieuwe geïntegreerde **app-registraties** ervaring of onze oudere **toepassingen (** verouderd). [Meer informatie over de nieuwe ervaring](https://aka.ms/b2cappregtraining).

#### <a name="app-registrations"></a>[App-registraties](#tab/app-reg-ga/)

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer het filter **Map + Abonnement** in het bovenste menu en selecteer vervolgens de map die uw Azure AD B2C-tenant bevat.
1. Selecteer **Azure AD B2C** in het linkermenu. Of selecteer **Alle services** en zoek naar en selecteer **Azure AD B2C**.
1. Selecteer **app-registraties**en selecteer vervolgens **nieuwe registratie**.
1. Voer een **naam** in voor de toepassing. Bijvoorbeeld *ROPC_Auth_app*.
1. Wijzig de andere waarden en selecteer vervolgens **registreren**.
1. Noteer de **id van de toepassing (client)** voor gebruik in een latere stap.
1. Selecteer onder **beheren**de optie **verificatie**.
1. Selecteer **de nieuwe ervaring uitproberen** (indien weer gegeven).
1. Onder **standaard client type**selecteert u **Ja** om de toepassing als een open bare client te behandelen. Deze instelling is vereist voor de ROPC-stroom.
1. Selecteer **Opslaan**.
1. Selecteer in het menu links de optie **manifest** om de manifest editor te openen. 
1. Stel het kenmerk **oauth2AllowImplicitFlow** in op *True*:
    ```json
    "oauth2AllowImplicitFlow": true,
    ```
1. Selecteer **Opslaan**.

#### <a name="applications-legacy"></a>[Toepassingen (verouderd)](#tab/applications-legacy/)

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer het filter **Map + Abonnement** in het bovenste menu en selecteer vervolgens de map die uw Azure AD B2C-tenant bevat.
1. Selecteer **Azure AD B2C** in het linkermenu. Of selecteer **Alle services** en zoek naar en selecteer **Azure AD B2C**.
1. Selecteer **toepassingen (verouderd)** en selecteer vervolgens **toevoegen**.
1. Voer een naam in voor de toepassing. Bijvoorbeeld *ROPC_Auth_app*.
1. Voor **Systeemeigen client** selecteert u **Ja**.
1. Wijzig de andere waarden en selecteer vervolgens **maken**.
1. Noteer de **toepassings-id** voor gebruik in een latere stap.