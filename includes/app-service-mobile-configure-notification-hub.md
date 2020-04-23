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
De functie Mobile Apps van Azure App Service gebruikt [Azure notification hubs] voor het verzenden van pushes, zodat u een notification hub kunt configureren voor uw mobiele app.

1. Ga in het [Azure Portal]naar **app Services**en selecteer vervolgens de back-end van uw app. Onder **instellingen**selecteert u **Push**.
2. Selecteer **verbinding maken**om een notification hub-resource aan de app toe te voegen. U kunt een hub maken of met een bestaande verbinding verbinden.

    ![Een hub configureren](./media/app-service-mobile-create-notification-hub/configure-hub-flow.png)

U hebt nu een notification hub verbonden met uw Mobile Apps back-end-project. Later configureert u deze notification hub om verbinding te maken met een platform Notification System (PNS) om naar apparaten te pushen.

[Azure Portal]: https://portal.azure.com/
[Azure Notification Hubs]: https://azure.microsoft.com/documentation/articles/notification-hubs-push-notification-overview/
