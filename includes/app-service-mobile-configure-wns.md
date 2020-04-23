---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: 79459be30a5a2018dc82486a84895b1a954941bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67176534"
---
1. Selecteer in de [Azure Portal](https://portal.azure.com/) **Bladeren alle** > **app Services**. Selecteer vervolgens uw Mobile Apps back-end. Selecteer **app Service push**onder **instellingen**. Selecteer vervolgens de naam van de notification hub.
2. Ga naar **Windows (WNS)**. Voer vervolgens de **beveiligings sleutel** (client geheim) en de **pakket-sid** in die u hebt verkregen van de Live Services-site. Selecteer vervolgens **Opslaan**.

    ![De WNS-sleutel in de Portal instellen](./media/app-service-mobile-configure-wns/mobile-push-wns-credentials.png)

Uw back-end is nu geconfigureerd om WNS te gebruiken om Push meldingen te verzenden.
