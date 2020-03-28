---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 1/30/2019
ms.author: rgarcia
ms.openlocfilehash: 0dab71b6d169e26a3d7dc208dd09efe1143fbe13
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67175724"
---
### <a name="open-the-publish-wizard"></a>De wizard Publiceren openen

Klik in **Solution Explorer**met de rechtermuisknop op het **project SharingService** en selecteer **Publiceren**.

De wizard Publiceren wordt gestart. Selecteer **App-servicepubliceren** > **Publish** om het dialoogvenster **App-service maken** te openen.

### <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Selecteer in het dialoogvenster **App-service maken** de optie **Een account toevoegen** en meld u aan bij uw Azure-abonnement. Als u al bent aangemeld, selecteert u het gewenste account in de vervolgkeuzelijst.

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

Gebruik de volgende instellingen in het dialoogvenster **Hostingplan configureren:**

| Instelling | Voorgestelde waarde | Beschrijving |
|-|-|-|
|App Service-plan| MySharingServicePlan | De naam van het App Service-plan. |
| Locatie | VS - west | Het datacenter waar de web-app wordt gehost. |
| Grootte | Gratis | De [prijslaag](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) die hostingfuncties bepaalt. |

Selecteer **OK**.

### <a name="create-and-publish-the-web-app"></a>De web-app maken en publiceren

Voer in **App-naam**een unieke app-naam in (geldige tekens zijn `a-z`, `0-9`en `-`), of accepteer de automatisch gegenereerde unieke naam. De URL van de web-app is `https://<app_name>.azurewebsites.net`, waarbij `<app_name>` de naam van uw app is.

Selecteer **Maken** om de Azure-resources te gaan maken.

Nadat de wizard is voltooid, publiceert deze de ASP.NET Core-web-app naar Azure en opent deze de app in uw standaardbrowser.

![Gepubliceerde ASP.NET-web-app in Azure](./media/spatial-anchors-azure/web-app-running-live.png)

De app-naam die u in deze sectie hebt `https://<app_name>.azurewebsites.net`gebruikt, wordt gebruikt als het URL-voorvoegsel in de indeling. Let op deze URL omdat je deze nodig hebt.
