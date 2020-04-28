---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: 31d4b65c6cd6a3dfe3cb779022329f338436c69b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "78184340"
---
Als u een toepassing in uw Azure AD B2C-Tenant wilt registreren, kunt u de huidige **toepassingen** gebruiken of onze nieuwe **Preview-ervaring (Unified app-registraties)** . [Meer informatie over de nieuwe ervaring](https://aka.ms/b2cappregintro).

#### <a name="applications"></a>[Toepassingen](#tab/applications/)

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer het pictogram voor het adres van de map en het **abonnement** op de werk balk van de portal en selecteer vervolgens de map die uw Azure AD B2C Tenant bevat.
1. Zoek in het Azure Portal naar en selecteer **Azure Active Directory**.
1. Selecteer onder **beheren**de optie **app-registraties (verouderd)**.
1. Selecteer **Nieuwe toepassing registreren**.
1. Voer een naam in voor de toepassing. Bijvoorbeeld *managementapp1*.
1. Selecteer **Web-app/API**voor het **toepassings type**.
1. Voer een geldige URL in voor de **aanmeldings-URL**. Bijvoorbeeld `https://localhost`. Het eind punt hoeft niet bereikbaar te zijn, maar moet een geldige URL zijn.
1. Selecteer **Maken**.
1. Noteer de **toepassings-id** die wordt weer gegeven op de overzichts pagina van de **geregistreerde app** . U gebruikt deze waarde in een latere stap.

#### <a name="app-registrations-preview"></a>[App-registraties (preview-versie)](#tab/app-reg-preview/)

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer het pictogram voor het adres van de map en het **abonnement** op de werk balk van de portal en selecteer vervolgens de map die uw Azure AD B2C Tenant bevat.
1. Zoek in het Azure Portal naar en selecteer **Azure AD B2C**.
1. Selecteer **app-registraties (preview)** en selecteer vervolgens **nieuwe registratie**.
1. Voer een **naam** in voor de toepassing. Bijvoorbeeld *managementapp1*.
1. Selecteer **alleen accounts in deze organisatie Directory**.
1. Schakel onder **machtigingen**het selectie vakje *verlenen beheerder toestemming voor openid connect-en offline_access machtigingen* uit.
1. Selecteer **Registreren**.
1. Noteer de **toepassings-id** van de toepassing die wordt weer gegeven op de overzichts pagina van de toepassing. U gebruikt deze waarde in een latere stap.