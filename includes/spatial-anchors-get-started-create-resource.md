---
author: craigktreasure
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 12/13/2018
ms.author: crtreasu
ms.openlocfilehash: 32f4545a45eda8acddd7c93cc4917dbadca9ad4d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "67135046"
---
## <a name="create-a-spatial-anchors-resource"></a>Een Spatial Anchors-resource maken

Ga naar de <a href="https://portal.azure.com" target="_blank">Azure Portal</a>.

Selecteer in het navigatie deel venster links in de Azure Portal **een resource maken**.

Gebruik het zoekvak om te zoeken naar **ruimtelijke ankers**.

   ![Zoeken naar Spatial Anchors](./media/spatial-anchors-get-started-create-resource/portal-search.png)

Selecteer **ruimtelijk ankers**. Selecteer in het dialoog venster **maken**.

In het dialoog venster **ruimtelijk ankers-account** :

- Voer een unieke resource naam in, met behulp van reguliere alfanumerieke tekens.
- Selecteer het abonnement waaraan u de resource wilt koppelen.
- Maak een resource groep door **Nieuw maken**te selecteren. Noem deze **myResourceGroup** en selecteer **OK**.
      [!INCLUDE [resource group intro text](resource-group.md)]
- Selecteer een locatie (regio) waarin u de resource wilt plaatsen.
- Selecteer **Nieuw** om te beginnen met het maken van de resource.

   ![Een resource maken](./media/spatial-anchors-get-started-create-resource/create-resource-form.png)

Nadat de resource is gemaakt, wordt in azure portal weer gegeven dat uw implementatie is voltooid. Klik op **Ga naar resource**.

![Implementatie voltooid](./media/spatial-anchors-get-started-create-resource/deployment-complete.png)

Vervolgens kunt u de eigenschappen van de resource weer geven. Kopieer de waarde van de **account-id** van de resource naar een tekst editor, want u hebt deze later nodig.

   ![Resource-eigenschappen](./media/spatial-anchors-get-started-create-resource/view-resource-properties.png)

Onder **instellingen**selecteert u **sleutel**. Kopieer de waarde van de **primaire sleutel** naar een tekst editor. Deze waarde is de `Account Key`. U hebt deze later nodig.

   ![Accountsleutel](./media/spatial-anchors-get-started-create-resource/view-account-key.png)
