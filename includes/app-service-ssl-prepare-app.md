---
title: Include-bestand
description: Include-bestand
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 10/15/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 4c5c0197ab6cecbba4bac4c0bff5ef76de24b6de
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "78300996"
---
## <a name="prepare-your-web-app"></a>Uw web-app voorbereiden

Als u aangepaste beveiligings bindingen wilt maken of client certificaten wilt inschakelen voor uw app service-app, moet uw [app service plan](https://azure.microsoft.com/pricing/details/app-service/) zich in de laag **Basic**, **Standard**, **Premium**of **geïsoleerd** bevallen. In deze stap zorgt u ervoor dat de web-app zich in de ondersteunde prijscategorie bevindt.

### <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Open [Azure Portal](https://portal.azure.com).

### <a name="navigate-to-your-web-app"></a>Navigeer naar uw web-app

Zoek en selecteer **app Services**.

![App Services selecteren](./media/app-service-ssl-prepare-app/app-services.png)

Selecteer op de pagina **app Services** de naam van uw web-app.

![Navigatie naar Azure-app in de portal](./media/app-service-ssl-prepare-app/select-app.png)

U hebt gelandd op de beheer pagina van uw web-app.  

### <a name="check-the-pricing-tier"></a>Controleer de prijscategorie

Scrol in de navigatiebalk links van de web-app-pagina naar het gedeelte **Instellingen** en selecteer **Opschalen (App Service-plan)**.

![Menu Opschalen](./media/app-service-ssl-prepare-app/scale-up-menu.png)

Controleer of de web-app zich niet in de laag **F1** of **D1** bevindt. De huidige categorie van de web-app is gemarkeerd met een donkerblauw vak.

![Controleer prijscategorie](./media/app-service-ssl-prepare-app/check-pricing-tier.png)

Aangepaste SSL wordt niet ondersteund in de laag **F1** of **D1**. Als u omhoog moet schalen, volgt u de stappen in het volgende gedeelte. Sluit anders de pagina **Omhoog schalen** en sla de sectie [Uw App Service-plan omhoog schalen](#scale-up-your-app-service-plan) over.

### <a name="scale-up-your-app-service-plan"></a>Uw App Service-plan omhoog schalen

Selecteer een van de lagen die niet gratis zijn (**B1**, **B2**, **B3** of een laag in de categorie **Productie**). Klik op **Aanvullende opties bekijken** voor aanvullende opties.

Klik op **Toepassen**.

![Prijscategorie kiezen](./media/app-service-ssl-prepare-app/choose-pricing-tier.png)

Wanneer u de volgende melding ziet, is de schaalbewerking voltooid.

![Melding voor omhoog schalen](./media/app-service-ssl-prepare-app/scale-notification.png)

