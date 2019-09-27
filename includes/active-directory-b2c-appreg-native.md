---
author: mmacy
ms.service: active-directory-b2c
ms.topic: include
ms.date: 09/27/2019
ms.author: marsma
ms.openlocfilehash: b83a6b9185eb4ef127da1dd3b55aba4e8b2945f9
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/27/2019
ms.locfileid: "71326307"
---
1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Selecteer het filter **Directory + abonnement** in het bovenste menu en selecteer vervolgens de map die uw Azure AD B2C Tenant bevat.
1. Selecteer in het linkermenu **Azure AD B2C**. U kunt ook **alle services** selecteren en **Azure AD B2C**zoeken en selecteren.
1. Selecteer **Toepassingen** en vervolgens **Toevoegen**.
1. Voer een naam in voor de toepassing. Bijvoorbeeld *nativeapp1*.
1. Selecteer voor **native client**de optie **Ja**.
1. Geef een **aangepaste omleidings-URI** met een uniek schema op. Bijvoorbeeld `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`. Er zijn twee belangrijke overwegingen bij het kiezen van een omleidings-URI:
    1. **Uniek**: Het schema van de omleidings-URI moet uniek zijn voor elke toepassing. In het voorbeeld `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect` is `com.onmicrosoft.contosob2c.exampleapp` het schema. Dit patroon moet worden gevolgd. Als twee toepassingen hetzelfde schema delen, kan de gebruiker een toepassing kiezen. Als de gebruiker onjuist kiest, mislukt de aanmelding.
    1. **Volt ooien**: De omleidings-URI moet een schema en een pad hebben. Het pad moet ten minste één slash na het domein bevatten. @No__t-0 werkt bijvoorbeeld wanneer `//oauth` mislukt. Neem geen speciale tekens in de URI op, bijvoorbeeld onderstrepingen.
1. Selecteer **Maken**.
