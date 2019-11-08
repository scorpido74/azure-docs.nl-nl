---
author: mmacy
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: marsma
ms.openlocfilehash: dec912ef6479e3668f22227c5f05de5ae8b12fcc
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73799773"
---
Als u een toepassing in uw Azure AD B2C-Tenant wilt registreren, kunt u de huidige **toepassingen** gebruiken of onze nieuwe **Preview-ervaring (Unified app-registraties)** . [Meer informatie over de nieuwe ervaring](https://aka.ms/b2cappregintro).

#### <a name="applicationstabapplications"></a>[Toepassingen](#tab/applications/)

1. Meld u aan bij de [Azure Portal](https://portal.azure.com).
1. Selecteer het filter **Directory + abonnement** in het bovenste menu en selecteer vervolgens de map die uw Azure AD B2C Tenant bevat.
1. Selecteer in het linkermenu **Azure AD B2C**. U kunt ook **alle services** selecteren en **Azure AD B2C**zoeken en selecteren.
1. Selecteer **Toepassingen** en vervolgens **Toevoegen**.
1. Voer een naam in voor de toepassing. Bijvoorbeeld *ROPC_Auth_app*.
1. Selecteer voor **native client**de optie **Ja**.
1. Wijzig de andere waarden en selecteer vervolgens **maken**.
1. Noteer de **toepassings-id** voor gebruik in een latere stap.

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[App-registraties (preview-versie)](#tab/app-reg-preview/)

1. Meld u aan bij de [Azure Portal](https://portal.azure.com).
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