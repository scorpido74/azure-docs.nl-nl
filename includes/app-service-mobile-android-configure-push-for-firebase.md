---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: 51a75ee7bf87c38e3916bdbc8d85abcfb14dca8b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67176540"
---
1. Klik in de [Azure-portal](https://portal.azure.com/)op Alle**App-services** **bladeren** > en klik vervolgens op de back-end van uw mobiele apps. Klik **onder Instellingen**op Push van **App-service**en klik vervolgens op de naam van de meldingshub.
2. Ga naar **Google (GCM),** voer de waarde **serversleutel** in die u in de vorige procedure van Firebase hebt verkregen en klik op **Opslaan**.

    ![De API-sleutel instellen in de portal](./media/app-service-mobile-android-configure-push/mobile-push-api-key.png)

De back-end van Mobiele apps is nu geconfigureerd voor het gebruik van Firebase Cloud Messaging. Hiermee u pushmeldingen verzenden naar uw app die wordt uitgevoerd op een Android-apparaat, met behulp van de meldingshub.
