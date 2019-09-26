---
author: mmacy
ms.service: active-directory-b2c
ms.topic: include
ms.date: 09/27/2019
ms.author: marsma
ms.openlocfilehash: 3b6f8ef5f2ec9f77dcba9c006fdd28b5ec668033
ms.sourcegitcommit: 9fba13cdfce9d03d202ada4a764e574a51691dcd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/26/2019
ms.locfileid: "71315019"
---
Communicatie met Azure AD B2C vindt plaats via een toepassing die u registreert in uw B2C-Tenant. In deze sectie vindt u optionele stappen die u kunt uitvoeren om een test toepassing te maken als u dit nog niet hebt gedaan.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Selecteer het filter **Directory + abonnement** in het bovenste menu en selecteer vervolgens de map die uw Azure AD B2C Tenant bevat.
1. Selecteer in het linkermenu **Azure AD B2C**. U kunt ook **alle services** selecteren en **Azure AD B2C**zoeken en selecteren.
1. Selecteer **Toepassingen** en vervolgens **Toevoegen**.
1. Voer een naam in voor de toepassing. Bijvoorbeeld *testapp1*.
1. Selecteer **Ja**voor **Web-app/Web-API**.
1. Voer bij **antwoord-URL**`https://jwt.ms`
1. Selecteer **Maken**.
