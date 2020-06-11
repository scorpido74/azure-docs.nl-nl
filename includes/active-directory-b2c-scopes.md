---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: 3ebe1ec4c0292a530e5ef2c754e9b002e931300e
ms.sourcegitcommit: f01c2142af7e90679f4c6b60d03ea16b4abf1b97
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/10/2020
ms.locfileid: "84680272"
---
#### <a name="app-registrations"></a>[App-registraties](#tab/app-reg-ga/) 

1. Selecteer **App-registraties**.
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

#### <a name="applications-legacy"></a>[Toepassingen (verouderd)](#tab/applications-legacy/)

1. **Toepassingen (verouderd)** selecteren.
1. Selecteer de toepassing *webapi1* om de **Eigenschappen** pagina te openen.
1. Selecteer **Gepubliceerde bereiken**. Gepubliceerde bereiken kunnen worden gebruikt om bepaalde machtigingen toe te kennen aan een client toepassing voor de Web-API.
1. Voer bij **bereik**, Enter `demo.read` en voor **Beschrijving**in `Read access to the web API` .
1. Voer bij **bereik**, Enter `demo.write` en voor **Beschrijving**in `Write access to the web API` .
1. Selecteer **Opslaan**.