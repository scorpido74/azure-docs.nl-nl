---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: 31d4b65c6cd6a3dfe3cb779022329f338436c69b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78184340"
---
Als u een toepassing wilt registreren in uw Azure AD B2C-tenant, u de huidige **ervaring met toepassingen** of onze nieuwe uniforme **app-registratie (Preview)-ervaring** gebruiken. [Meer informatie over de nieuwe ervaring](https://aka.ms/b2cappregintro).

#### <a name="applications"></a>[Toepassingen](#tab/applications/)

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Selecteer het pictogram **Directory + Abonnement** op de werkbalk van de portal en selecteer vervolgens de map met uw Azure AD B2C-tenant.
1. Zoek en selecteer **Azure Active Directory**in de Azure-portal.
1. Selecteer **onder Beheren** **app-registraties (Legacy)**.
1. Selecteer **Nieuwe toepassing registreren**.
1. Voer een naam in voor de toepassing. Bijvoorbeeld, *managementapp1*.
1. Selecteer **Web-app / API**voor **toepassingstype**.
1. Voer een geldige URL in **aanmeldings-URL**in . Bijvoorbeeld `https://localhost`. Het eindpunt hoeft niet bereikbaar te zijn, maar moet wel een geldige URL zijn.
1. Selecteer **Maken**.
1. De **toepassings-id** registreren die wordt weergegeven op de **overzichtspagina geregistreerde apps.** U gebruikt deze waarde in een latere stap.

#### <a name="app-registrations-preview"></a>[App-registraties (voorbeeld)](#tab/app-reg-preview/)

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Selecteer het pictogram **Directory + Abonnement** op de werkbalk van de portal en selecteer vervolgens de map met uw Azure AD B2C-tenant.
1. Zoek en selecteer **Azure AD B2C**in de Azure-portal .
1. Selecteer **App-registraties (Voorbeeld)** en selecteer **Vervolgens Nieuwe registratie**.
1. Voer een **naam** voor de toepassing in. Bijvoorbeeld, *managementapp1*.
1. Selecteer **alleen Accounts in deze organisatiemap**.
1. Schakel **onder Machtigingen**het selectievakje Toestemming voor beheerders verlenen voor *openid en offline_access machtigingen* uit.
1. Selecteer **Registreren**.
1. Neem de **toepassings-id (client) op** die wordt weergegeven op de pagina toepassingsoverzicht. U gebruikt deze waarde in een latere stap.