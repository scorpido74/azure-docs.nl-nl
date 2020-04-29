---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 1/30/2019
ms.author: rgarcia
ms.openlocfilehash: 0dab71b6d169e26a3d7dc208dd09efe1143fbe13
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "67175724"
---
### <a name="open-the-publish-wizard"></a>Open de wizard Publiceren

Klik in **Solution Explorer**met de rechter muisknop op het project **SharingService** en selecteer **publiceren**.

De wizard Publiceren wordt gestart. Selecteer **app service** > **publiceren** om het dialoog venster **app service maken** te openen.

### <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Selecteer in het dialoog venster **app service maken** de optie **een account toevoegen** en meld u aan bij uw Azure-abonnement. Als u al bent aangemeld, selecteert u het gewenste account in de vervolg keuzelijst.

> [!NOTE]
> Als u al bent aangemeld, selecteert u **Maken** nog niet.
>

### <a name="create-a-resource-group"></a>Een resourcegroep maken

[!INCLUDE [resource group intro text](resource-group.md)]

Selecteer **Nieuw** naast **Resourcegroep**.

Geef de resourcegroep de naam **myResourceGroup** en selecteer **OK**.

### <a name="create-an-app-service-plan"></a>Een App Service-plan maken

[!INCLUDE [app-service-plan](app-service-plan.md)]

Selecteer bij **Hostingabonnement****Nieuw**.

Gebruik in het dialoog venster **hosting plan configureren** de volgende instellingen:

| Instelling | Voorgestelde waarde | Beschrijving |
|-|-|-|
|App Service-plan| MySharingServicePlan | De naam van het App Service-plan. |
| Locatie | VS - west | Het datacenter waar de web-app wordt gehost. |
| Grootte | Gratis | De [prijs categorie](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) die de hosting functies bepaalt. |

Selecteer **OK**.

### <a name="create-and-publish-the-web-app"></a>De web-app maken en publiceren

Voer **in app-naam**een unieke app-naam in (geldige `a-z`tekens `0-9`zijn, `-`en) of accepteer de automatisch gegenereerde unieke naam. De URL van de web-app is `https://<app_name>.azurewebsites.net`, waarbij `<app_name>` de naam van uw app is.

Selecteer **Maken** om de Azure-resources te gaan maken.

Nadat de wizard is voltooid, wordt de ASP.NET Core web-app gepubliceerd naar Azure en wordt vervolgens de App geopend in de standaard browser.

![Gepubliceerde ASP.NET-web-app in Azure](./media/spatial-anchors-azure/web-app-running-live.png)

De naam van de app die u in deze sectie hebt gebruikt, wordt gebruikt als het `https://<app_name>.azurewebsites.net`URL-voor voegsel in de notatie. Noteer deze URL omdat u deze nodig hebt.
