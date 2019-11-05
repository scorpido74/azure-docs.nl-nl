---
author: mmacy
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: marsma
ms.openlocfilehash: 59cf0b81363c724d709d85105525bece13743f26
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73474875"
---
#### <a name="applicationstabapplications"></a>[Toepassingen](#tab/applications/)

1. Selecteer **toepassingen**.
1. Selecteer de toepassing *webapi1* om de **Eigenschappen** pagina te openen.
1. Selecteer **Gepubliceerde bereiken**. Gepubliceerde bereiken kunnen worden gebruikt om bepaalde machtigingen toe te kennen aan een client toepassing voor de Web-API.
1. Voer voor **bereik**`demo.read`in en voer bij **Beschrijving**`Read access to the web API`in.
1. Voer voor **bereik**`demo.write`in en voer bij **Beschrijving**`Write access to the web API`in.
1. Selecteer **Opslaan**.

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[App-registraties (preview-versie)](#tab/app-reg-preview/)

1. Selecteer **app-registraties (preview)** .
1. Selecteer de *webapi1* -toepassing om de pagina **overzicht** te openen.
1. Onder **beheren**selecteert u **een API zichtbaar**maken.
1. Selecteer de koppeling **instellen** naast de URI van de **toepassings-id**.
1. Vervang de standaard waarde (een GUID) door `api`en selecteer vervolgens **Opslaan**. De volledige URI wordt weer gegeven en moet de indeling `https://your-tenant-name.onmicrosoft.com/api`hebben. Wanneer uw webtoepassing een toegangs token voor de API aanvraagt, moet deze de URI toevoegen als het voor voegsel voor elk bereik dat u voor de API definieert.
1. Selecteer **een bereik toevoegen**onder **scopes die door deze API zijn gedefinieerd**.
1. Voer de volgende waarden in om een bereik te maken dat lees toegang tot de API definieert, en selecteer vervolgens **bereik toevoegen**:
    1. **Scope naam**: `demo.read`
    1. **Weergave naam van beheerders toestemming**: `Read access to demo API`
    1. **Beschrijving van beheerders toestemming**: `Allows read access to the demo API`
1. Selecteer **een bereik toevoegen**, voer de volgende waarden in om een bereik toe te voegen waarmee schrijf toegang tot de API wordt gedefinieerd en selecteer vervolgens **bereik toevoegen**:
    1. **Scope naam**: `demo.write`
    1. **Weergave naam van beheerders toestemming**: `Write access to demo API`
    1. **Beschrijving van beheerders toestemming**: `Allows write access to the demo API`