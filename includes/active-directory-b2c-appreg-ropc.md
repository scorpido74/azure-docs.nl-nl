---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: cea3245176e6c38137d68e3ad4b47477bedc78be
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80529167"
---
Als u een toepassing in uw Azure AD B2C-Tenant wilt registreren, kunt u de huidige **toepassingen** gebruiken of onze nieuwe **Preview-ervaring (Unified app-registraties)** . [Meer informatie over de nieuwe ervaring](https://aka.ms/b2cappregintro).

#### <a name="applications"></a>[Toepassingen](#tab/applications/)

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Selecteer het filter **Directory + abonnement** in het bovenste menu en selecteer vervolgens de map die uw Azure AD B2C Tenant bevat.
1. Selecteer in het linkermenu **Azure AD B2C**. U kunt ook **alle services** selecteren en **Azure AD B2C**zoeken en selecteren.
1. Selecteer **toepassingen**en selecteer vervolgens **toevoegen**.
1. Voer een naam in voor de toepassing. Bijvoorbeeld *ROPC_Auth_app*.
1. Selecteer voor **native client**de optie **Ja**.
1. Wijzig de andere waarden en selecteer vervolgens **maken**.
1. Noteer de **toepassings-id** voor gebruik in een latere stap.

#### <a name="app-registrations-preview"></a>[App-registraties (preview-versie)](#tab/app-reg-preview/)

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Selecteer het filter **Directory + abonnement** in het bovenste menu en selecteer vervolgens de map die uw Azure AD B2C Tenant bevat.
1. Selecteer in het linkermenu **Azure AD B2C**. U kunt ook **alle services** selecteren en **Azure AD B2C**zoeken en selecteren.
1. Selecteer **app-registraties (preview)** en selecteer vervolgens **nieuwe registratie**.
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