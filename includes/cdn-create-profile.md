---
title: bestand opnemen
description: bestand opnemen
services: cdn
author: SyntaxC4
ms.service: azure-cdn
ms.topic: include
ms.date: 04/30/2020
ms.author: cfowler
ms.custom: include file
ms.openlocfilehash: d73a8d743a948cbd94a3af81fe2e77c45c0eeb67
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/09/2020
ms.locfileid: "82996310"
---
## <a name="create-a-new-cdn-profile"></a>Nieuwe CDN-profielen maken

Een CDN-profiel is een container voor CDN-eindpunten waarmee een prijscategorie wordt opgegeven.

1. Selecteer in de Azure Portal **een resource maken** (linksboven). Het deelvenster **Nieuw** verschijnt.
   
1. Zoek en selecteer **CDN**en selecteer vervolgens **maken**:
   
    ![CDN-resource selecteren](./media/cdn-create-profile/cdn-new-resource.png)

    Het deel venster **CDN-profiel** wordt weer gegeven.

1. Voer de volgende waarden in:
   
    | Instelling  | Waarde |
    | -------- | ----- |
    | **Naam** | Voer het *CDN-profiel-123* in voor uw profiel naam. Deze naam moet globaal uniek zijn. Als deze al in gebruik is, voert u een andere naam in. |
    | **Abonnement** | Kies een Azure-abonnement in de vervolgkeuzelijst. |
    | **Resourcegroep** | Selecteer **nieuwe maken** en voer *CDNQuickstart-RG* in voor de naam van de resource groep of selecteer **bestaande gebruiken** en kies *CDNQuickstart-RG* als u de groep al hebt. | 
    | **Resourcegroeplocatie** | Selecteer een locatie bij u in de vervolg keuzelijst. |
    | **Prijs categorie** | Selecteer een **standaard** optie voor Akamai in de vervolg keuzelijst. (De implementatie tijd voor de Akamai-laag is ongeveer een minuut. De micro soft-laag neemt ongeveer 10 minuten in beslag en de Verizon-lagen nemen ongeveer 90 minuten in.) |
    | **Nu een nieuw CDN-eindpunt maken** | Laat het selectievakje uitgeschakeld. |  
   
    ![Nieuw CDN-profiel](./media/cdn-create-profile/cdn-new-profile.png)

1. Selecteer **Maken** om het profiel te maken.

