---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: bbbc606497b53a0790d667a63b45101a3c18bbc4
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/02/2020
ms.locfileid: "84297481"
---
#### <a name="app-registrations-preview"></a>[App-registraties (preview)](#tab/app-reg-preview/)

1. Selecteer **app-registraties (preview)**.
1. Selecteer de *webapi1* -toepassing om de pagina **overzicht** te openen.
1. Onder **beheren**selecteert u **een API zichtbaar**maken.
1. Selecteer de koppeling **instellen** naast de URI van de **toepassings-id**.
1. Vervang de standaard waarde (een GUID) door `api` en selecteer vervolgens **Opslaan**. De volledige URI wordt weer gegeven en moet de indeling hebben `https://your-tenant-name.onmicrosoft.com/api` . Wanneer uw webtoepassing een toegangs token voor de API aanvraagt, moet deze de URI toevoegen als het voor voegsel voor elk bereik dat u voor de API definieert.
1. Selecteer **een bereik toevoegen**onder **scopes die door deze API zijn gedefinieerd**.
1. Voer de volgende waarden in om een bereik te maken dat lees toegang tot de API definieert, en selecteer vervolgens **bereik toevoegen**:
    1. **Scope naam**:`demo.read`
    1. **Weergave naam van beheerders toestemming**:`Read access to demo API`
    1. **Beschrijving van beheerders toestemming**:`Allows read access to the demo API`
1. Selecteer **een bereik toevoegen**, voer de volgende waarden in om een bereik toe te voegen waarmee schrijf toegang tot de API wordt gedefinieerd en selecteer vervolgens **bereik toevoegen**:
    1. **Scope naam**:`demo.write`
    1. **Weergave naam van beheerders toestemming**:`Write access to demo API`
    1. **Beschrijving van beheerders toestemming**:`Allows write access to the demo API`

#### <a name="applications"></a>[Toepassingen](#tab/applications/)

1. Selecteer **toepassingen**.
1. Selecteer de toepassing *webapi1* om de **Eigenschappen** pagina te openen.
1. Selecteer **Gepubliceerde bereiken**. Gepubliceerde bereiken kunnen worden gebruikt om bepaalde machtigingen toe te kennen aan een client toepassing voor de Web-API.
1. Voer bij **bereik**, Enter `demo.read` en voor **Beschrijving**in `Read access to the web API` .
1. Voer bij **bereik**, Enter `demo.write` en voor **Beschrijving**in `Write access to the web API` .
1. Selecteer **Opslaan**.