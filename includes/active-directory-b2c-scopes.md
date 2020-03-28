---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: 9d394b07ff17ab24305b2580efc8bedfc5e7d384
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78183370"
---
#### <a name="applications"></a>[Toepassingen](#tab/applications/)

1. Selecteer **Toepassingen**.
1. Selecteer de *webapi1-toepassing* om de pagina **Eigenschappen** te openen.
1. Selecteer **Gepubliceerde bereiken**. Gepubliceerde scopes kunnen worden gebruikt om een clienttoepassing bepaalde machtigingen te verlenen voor de web-API.
1. Voor **SCOPE** `demo.read`, voer , `Read access to the web API`en voor **BESCHRIJVING**, voer .
1. Voor **SCOPE** `demo.write`, voer , `Write access to the web API`en voor **BESCHRIJVING**, voer .
1. Selecteer **Opslaan**.

#### <a name="app-registrations-preview"></a>[App-registraties (voorbeeld)](#tab/app-reg-preview/)

1. Selecteer **App-registraties (Voorbeeld)**.
1. Selecteer de *webapi1-toepassing* om de **pagina Overzicht** te openen.
1. Selecteer **onder Beheren**de optie Een API **blootleggen**.
1. Selecteer naast **Application ID URI**de koppeling **Instellen.**
1. Vervang de standaardwaarde (een `api`GUID) door , en selecteer **Vervolgens Opslaan**. De volledige URI wordt weergegeven, en `https://your-tenant-name.onmicrosoft.com/api`moet in het formaat . Wanneer uw webtoepassing een toegangstoken voor de API aanvraagt, moet deze URI worden toegevoegd als het voorvoegsel voor elk bereik dat u definieert voor de API.
1. Selecteer Onder **Scopes die door deze API zijn gedefinieerd,** de optie **Een bereik toevoegen**.
1. Voer de volgende waarden in om een bereik te maken dat leestoegang tot de API definieert en selecteer **vervolgens Bereik toevoegen:**
    1. **Naam van het toepassingsgebied**:`demo.read`
    1. **Weergavenaam beheerderstoestemming:**`Read access to demo API`
    1. **Beschrijving van de toestemming van beheerders:**`Allows read access to the demo API`
1. Selecteer **Een bereik toevoegen,** voer de volgende waarden in om een bereik toe te voegen dat schrijftoegang tot de API definieert en selecteer **vervolgens Bereik toevoegen:**
    1. **Naam van het toepassingsgebied**:`demo.write`
    1. **Weergavenaam beheerderstoestemming:**`Write access to demo API`
    1. **Beschrijving van de toestemming van beheerders:**`Allows write access to the demo API`