---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: 9d394b07ff17ab24305b2580efc8bedfc5e7d384
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "78183370"
---
#### <a name="applications"></a>[Toepassingen](#tab/applications/)

1. Selecteer **toepassingen**.
1. Selecteer de toepassing *webapi1* om de **Eigenschappen** pagina te openen.
1. Selecteer **Gepubliceerde bereiken**. Gepubliceerde bereiken kunnen worden gebruikt om bepaalde machtigingen toe te kennen aan een client toepassing voor de Web-API.
1. `Read access to the web API`Voer bij **bereik**, `demo.read`ENTER en voor **Beschrijving**in.
1. `Write access to the web API`Voer bij **bereik**, `demo.write`ENTER en voor **Beschrijving**in.
1. Selecteer **Opslaan**.

#### <a name="app-registrations-preview"></a>[App-registraties (preview-versie)](#tab/app-reg-preview/)

1. Selecteer **app-registraties (preview)**.
1. Selecteer de *webapi1* -toepassing om de pagina **overzicht** te openen.
1. Onder **beheren**selecteert u **een API zichtbaar**maken.
1. Selecteer de koppeling **instellen** naast de URI van de **toepassings-id**.
1. Vervang de standaard waarde (een GUID) `api`door en selecteer vervolgens **Opslaan**. De volledige URI wordt weer gegeven en moet de indeling `https://your-tenant-name.onmicrosoft.com/api`hebben. Wanneer uw webtoepassing een toegangs token voor de API aanvraagt, moet deze de URI toevoegen als het voor voegsel voor elk bereik dat u voor de API definieert.
1. Selecteer **een bereik toevoegen**onder **scopes die door deze API zijn gedefinieerd**.
1. Voer de volgende waarden in om een bereik te maken dat lees toegang tot de API definieert, en selecteer vervolgens **bereik toevoegen**:
    1. **Scope naam**:`demo.read`
    1. **Weergave naam van beheerders toestemming**:`Read access to demo API`
    1. **Beschrijving van beheerders toestemming**:`Allows read access to the demo API`
1. Selecteer **een bereik toevoegen**, voer de volgende waarden in om een bereik toe te voegen waarmee schrijf toegang tot de API wordt gedefinieerd en selecteer vervolgens **bereik toevoegen**:
    1. **Scope naam**:`demo.write`
    1. **Weergave naam van beheerders toestemming**:`Write access to demo API`
    1. **Beschrijving van beheerders toestemming**:`Allows write access to the demo API`