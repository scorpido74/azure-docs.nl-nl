---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: da3793c428c624ce3a224cbd7606ab26c4a50803
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67176538"
---
De functie Mobiele apps van Azure App Service maakt gebruik van [Azure Notification Hubs] om pushes te verzenden, zodat u een meldingshub voor uw mobiele app configureert.

1. Ga in de [Azure-portal]naar **App Services**en selecteer vervolgens de back-end van uw app. Selecteer **Onder Instellingen**de optie **Push**.
2. Als u een bron voor de meldingshub aan de app wilt toevoegen, selecteert u **Verbinding maken**. U een hub maken of verbinding maken met een bestaande hub.

    ![Een hub configureren](./media/app-service-mobile-create-notification-hub/configure-hub-flow.png)

Nu hebt u een meldingshub gekoppeld aan uw back-endproject voor mobiele apps. Later configureert u deze meldingshub om verbinding te maken met een platformmeldingssysteem (PNS) om naar apparaten te pushen.

[Azure-portal]: https://portal.azure.com/
[Azure Notification Hubs]: https://azure.microsoft.com/documentation/articles/notification-hubs-push-notification-overview/
