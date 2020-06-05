---
author: craigktreasure
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 12/13/2018
ms.author: crtreasu
ms.openlocfilehash: b2b3ca886359a0b4c906b89ed76f57486fc2c368
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83639004"
---
## <a name="create-a-spatial-anchors-resource"></a>Een Spatial Anchors-resource maken

Ga naar de <a href="https://portal.azure.com" target="_blank">Azure Portal</a>.

Selecteer **Een resource maken** in het navigatiedeelvenster aan de linkerkant van de Azure-portal.

Gebruik het zoekvak om te zoeken naar **Spatial Anchors**.

   ![Zoeken naar Spatial Anchors](./media/spatial-anchors-get-started-create-resource/portal-search.png)

Selecteer **Spatial Anchors**. Selecteer **Maken** in het dialoogvenster.

In het dialoogvenster **Spatial Anchors-account**:

- Voer een unieke resourcenaam in, met reguliere alfanumerieke tekens.
- Selecteer het abonnement waaraan u de resource wilt koppelen.
- Maak een resourcegroep door **Nieuwe maken** te selecteren. Noem het **myResourceGroup** en selecteer **OK**.
      [!INCLUDE [resource group intro text](resource-group.md)]
- Selecteer de locatie (regio) waarin u de resource wilt plaatsen.
- Selecteer **Nieuw** om te beginnen met het maken van de resource.

   ![Een resource maken](./media/spatial-anchors-get-started-create-resource/create-resource-form.png)

Nadat de resource is gemaakt, toont de Azure-portal dat uw implementatie is voltooid. Klik op **Ga naar resource**.

![Implementatie voltooid](./media/spatial-anchors-get-started-create-resource/deployment-complete.png)

Vervolgens kunt u de resource-eigenschappen bekijken. Kopieer de waarde bij **Account-ID** van de resource naar een teksteditor. U hebt deze later nodig.

   ![Resource-eigenschappen](./media/spatial-anchors-get-started-create-resource/view-resource-properties.png)

Kopieer ook de waarde bij **Accountdomein** van de resource naar een teksteditor. U hebt deze later nodig.

   ![Accountdomein](./media/spatial-anchors-get-started-create-resource/view-resource-domain.png)

Selecteer onder **Instellingen** de optie **Sleutel**. Kopieer de waarde bij **Primaire sleutel** naar een teksteditor. Deze waarde is de `Account Key`. U hebt deze later nodig.

   ![Accountsleutel](./media/spatial-anchors-get-started-create-resource/view-account-key.png)
