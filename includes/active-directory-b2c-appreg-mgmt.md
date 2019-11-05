---
author: mmacy
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: marsma
ms.openlocfilehash: baf39c1fe72703d8ebc18b03bae1c963536d7ced
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73475211"
---
Als u een toepassing in uw Azure AD B2C-Tenant wilt registreren, kunt u de huidige **toepassingen** gebruiken of onze nieuwe **Preview-ervaring (Unified app-registraties)** . Meer [informatie over de preview-ervaring](https://aka.ms/b2cappregintro).

#### <a name="applicationstabapplications"></a>[Toepassingen](#tab/applications/)

1. Meld u aan bij de [Azure Portal](https://portal.azure.com).
1. Selecteer het filter **Directory + abonnement** in het bovenste menu en selecteer vervolgens de map die uw Azure AD B2C Tenant bevat.
1. Selecteer **Azure Active Directory** (*niet* Azure AD B2C) in het menu links. U kunt ook **alle services** selecteren en vervolgens zoeken naar en **Azure Active Directory**selecteren.
1. Selecteer onder **beheren**de optie **app-registraties (verouderd)** .
1. Selecteer **Nieuwe toepassing registreren**.
1. Voer een naam in voor de toepassing. Bijvoorbeeld *managementapp1*.
1. Selecteer **Web-app/API**voor het **toepassings type**.
1. Voer een geldige URL in voor de **aanmeldings-URL**. Bijvoorbeeld `https://localhost`. Het eind punt hoeft niet bereikbaar te zijn, maar moet een geldige URL zijn.
1. Selecteer **Maken**.
1. Noteer de **toepassings-id** die wordt weer gegeven op de overzichts pagina van de **geregistreerde app** . U gebruikt deze waarde in een latere stap.

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[App-registraties (preview-versie)](#tab/app-reg-preview/)

1. Meld u aan bij de [Azure Portal](https://portal.azure.com).
1. Selecteer het filter **Directory + abonnement** in het bovenste menu en selecteer vervolgens de map die uw Azure AD B2C Tenant bevat.
1. Selecteer in het linkermenu **Azure AD B2C**. U kunt ook **alle services** selecteren en **Azure AD B2C**zoeken en selecteren.
1. Selecteer **app-registraties (preview)** en selecteer vervolgens **nieuwe registratie**.
1. Voer een **naam** in voor de toepassing. Bijvoorbeeld *managementapp1*.
1. Selecteer **alleen accounts in deze organisatie Directory**.
1. Schakel onder **machtigingen**het selectie vakje *verlenen beheerder toestemming voor OpenID Connect-en offline_access-machtigingen* uit.
1. Selecteer **Registreren**.
1. Noteer de **toepassings-id** van de toepassing die wordt weer gegeven op de overzichts pagina van de toepassing. U gebruikt deze waarde in een latere stap.