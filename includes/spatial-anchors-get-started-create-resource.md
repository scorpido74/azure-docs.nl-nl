---
author: craigktreasure
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 12/13/2018
ms.author: crtreasu
ms.openlocfilehash: 32f4545a45eda8acddd7c93cc4917dbadca9ad4d
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "67135046"
---
## <a name="create-a-spatial-anchors-resource"></a>Een Spatial Anchors-resource maken

Ga naar de <a href="https://portal.azure.com" target="_blank">Azure-portal.</a>

Selecteer in het linkernavigatiedeelvenster in de Azure-portal de optie **Een resource maken**.

Gebruik het zoekvak om te zoeken naar **ruimtelijke ankers.**

   ![Zoeken naar Spatial Anchors](./media/spatial-anchors-get-started-create-resource/portal-search.png)

Selecteer **Ruimtelijke ankers**. Selecteer in het dialoogvenster **Maken**.

Ga als **een overzicht van de ruimteankers:**

- Voer een unieke resourcenaam in met behulp van normale alfanumerieke tekens.
- Selecteer het abonnement waaraan u de resource wilt koppelen.
- Maak een resourcegroep door **Nieuw maken te**selecteren . Geef het **myResourceGroup** een naam en selecteer **OK**.
      [!INCLUDE [resource group intro text](resource-group.md)]
- Selecteer een locatie (regio) waar u de resource wilt plaatsen.
- Selecteer **Nieuw** om te beginnen met het maken van de resource.

   ![Een resource maken](./media/spatial-anchors-get-started-create-resource/create-resource-form.png)

Nadat de bron is gemaakt, laat Azure Portal zien dat uw implementatie is voltooid. Klik op **Ga naar resource**.

![Implementatie voltooid](./media/spatial-anchors-get-started-create-resource/deployment-complete.png)

Vervolgens u de resourceeigenschappen bekijken. Kopieer de **account-id-waarde** van de bron naar een teksteditor omdat u deze later nodig hebt.

   ![Resourceeigenschappen](./media/spatial-anchors-get-started-create-resource/view-resource-properties.png)

Selecteer **Onder Instellingen**de optie **Sleutel**. Kopieer de waarde **primaire sleutel** naar een teksteditor. Deze waarde `Account Key`is de . U hebt deze later nodig.

   ![Accountsleutel](./media/spatial-anchors-get-started-create-resource/view-account-key.png)
