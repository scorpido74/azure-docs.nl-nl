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
1. Selecteer in de [Azure-portal](https://portal.azure.com/) **Bladeren door Alle** > **App-services**. Selecteer vervolgens de back-end van mobiele apps. Selecteer **onder Instellingen**de optie App Service **Push**. Selecteer vervolgens de naam van de meldingshub.
2. Ga naar **Windows (WNS)**. Voer vervolgens de **beveiligingssleutel** (klantgeheim) en **Package SID** in die u hebt verkregen van de Live Services-site. Selecteer vervolgens **Opslaan**.

    ![De WNS-toets instellen in de portal](./media/app-service-mobile-configure-wns/mobile-push-wns-credentials.png)

Uw back-end is nu geconfigureerd om WNS te gebruiken om pushmeldingen te verzenden.
