---
title: bestand opnemen
description: bestand opnemen
services: cdn
author: SyntaxC4
ms.service: azure-cdn
ms.topic: include
ms.date: 05/24/2018
ms.author: cfowler
ms.custom: include file
ms.openlocfilehash: 8aa6cb3f10b86a6821cd93190ecc2135508739cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67593996"
---
## <a name="create-a-new-cdn-profile"></a>Nieuwe CDN-profielen maken

Een CDN-profiel is een container voor CDN-eindpunten waarmee een prijscategorie wordt opgegeven.

1. Selecteer linksboven in Azure Portal **Een resource maken**. 
    
    Het deelvenster **Nieuw** verschijnt.
   
2. Selecteer **Web en mobiel** en vervolgens **CDN**.
   
    ![CDN-resource selecteren](./media/cdn-create-profile/cdn-new-resource.png)

    Het **cdn-profielvenster** wordt weergegeven.

3. Gebruik voor de CDN-profielinstellingen de waarden die in de volgende tabel zijn opgegeven:
   
    | Instelling  | Waarde |
    | -------- | ----- |
    | **Naam** | Voer *my-cdn-profile-123* in als profielnaam. Deze naam moet wereldwijd uniek zijn. Als deze al in gebruik is, kunt u een andere naam invoeren. |
    | **Abonnement** | Kies een Azure-abonnement in de vervolgkeuzelijst. |
    | **Resourcegroep** | Selecteer **Nieuwe maken** en voer *my-resource-group-123* in als naam voor de resourcegroep. Als deze al in gebruik is, kunt u een andere naam invoeren of **Bestaande gebruiken** selecteren en **my-resource-group-123** in de vervolgkeuzelijst selecteren. | 
    | **Resourcegroeplocatie** | Selecteer **VS - centraal** in de vervolgkeuzelijst. |
    | **Prijslaag** | Selecteer **Standard - Verizon** in de vervolgkeuzelijst. |
    | **Nu een nieuw CDN-eindpunt maken** | Laat het selectievakje uitgeschakeld. |  
   
    ![Nieuw CDN-profiel](./media/cdn-create-profile/cdn-new-profile.png)

4. Selecteer **Aan dashboard vastmaken** om het profiel in het dashboard op te slaan nadat het is gemaakt.
    
5. Selecteer **Maken** om het profiel te maken. 

    Voor alleen **Azure CDN Standard van Microsoft**-profielen duurt het voltooien van het profiel meestal twee uur. 

